---
title: 'Tutorial: Konfigurieren von Rollen der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) in Azure Kubernetes Service (AKS) mithilfe von Ansible'
description: Es wird beschrieben, wie Sie Ansible verwenden, um die rollenbasierte Zugriffssteuerung (RBAC) in einem AKS-Cluster (Azure Kubernetes Service) zu konfigurieren.
keywords: Ansible, Azure, DevOps, Bash, Cloud Shell, Playbook, AKS, Container, Kubernetes, Azure Active Directory, RBAC
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 9018ca465246163a00e984d013449cfef6ae0be1
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "81743804"
---
# <a name="tutorial-configure-role-based-access-control-rbac-roles-in-azure-kubernetes-service-aks-using-ansible"></a>Tutorial: Konfigurieren von Rollen der rollenbasierten Zugriffssteuerung (RBAC) in Azure Kubernetes Service (AKS) mit Ansible

[!INCLUDE [ansible-28-note.md](includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../includes/open-source-devops-intro-aks.md)]

AKS kann für die Verwendung von [Azure Active Directory (AD)](/azure/active-directory/) für die Benutzerauthentifizierung konfiguriert werden. Nach der Konfiguration verwenden Sie Ihr Azure AD-Authentifizierungstoken, um sich am AKS-Cluster anzumelden. Die rollenbasierte Zugriffssteuerung (RBAC) kann auf der Identität eines Benutzers oder auf der Mitgliedschaft in einer Verzeichnisgruppe basieren.

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Erstellen eines Azure AD-fähigen AKS-Clusters
> * Konfigurieren einer RBAC-Rolle im Cluster

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Installieren Sie die RedHat OpenShift-Bibliothek** - `pip install openshift`.

## <a name="configure-azure-ad-for-aks-authentication"></a>Konfigurieren von Azure AD für die AKS-Authentifizierung

Bei der Konfiguration von Azure AD für die AKS-Authentifizierung werden zwei Azure AD-Anwendungen konfiguriert. Dieser Vorgang muss von einem Azure-Mandantenadministrator ausgeführt werden. Weitere Informationen finden Sie unter [Integrieren von Azure Active Directory in Azure Kubernetes Service](/azure/aks/aad-integration#create-the-server-application). 

Beschaffen Sie über den Azure-Mandantenadministrator die folgenden Werte:

- Server-App-Geheimnis
- Server-App-ID
- Client-App-ID 
- Mandanten-ID

Diese Werte werden zum Ausführen des Beispielplaybooks benötigt.  

## <a name="create-an-aks-cluster"></a>Erstellen eines AKS-Clusters

In diesem Abschnitt erstellen Sie mit der [Azure AD-Anwendung](#configure-azure-ad-for-aks-authentication) eine AKS-Instanz.

Hier sind einige wichtige Hinweise aufgeführt, die Sie beim Arbeiten mit dem Beispielplaybook berücksichtigen sollten:

- Das Playbook lädt `ssh_key` aus `~/.ssh/id_rsa.pub`. Verwenden Sie bei etwaigen Änderungen das einzeilige Format, und beginnen Sie die Zeile mit „ssh-rsa“ (ohne Anführungszeichen).
- Die Werte `client_id` und `client_secret` werden aus `~/.azure/credentials` geladen. Dies ist die Standarddatei mit den Anmeldeinformationen. Sie können diese Werte auf Ihren Dienstprinzipal festlegen oder sie aus Umgebungsvariablen laden:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

Speichern Sie das folgende Playbook als `aks-create.yml`:

```yml
- name: Create resource group
  azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

- name: List supported kubernetes version from Azure
  azure_rm_aksversion_facts:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with RBAC enabled
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      enable_rbac: yes
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      aad_profile:
          client_app_id: "{{ client_app_id }}"
          server_app_id: "{{ server_app_id }}"
          server_app_secret: "{{ server_app_secret }}"
          tenant_id: "{{ app_tenant_id }}"
  register: aks

- name: Save cluster user config
  copy:
      content: "{{ aks.kube_config }}"
      dest: "aks-{{ name }}-kubeconfig-user"

- name: Get admin config of AKS
  azure_rm_aks_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      show_kubeconfig: admin
  register: aks

- name: Save the kubeconfig
  copy:
      content: "{{ aks.aks[0].kube_config }}"
      dest: "aks-{{ name }}-kubeconfig"
```

## <a name="get-the-azure-ad-object-id"></a>Abrufen der Azure AD-Objekt-ID

Zum Erstellen einer RBAC-Bindung müssen Sie zuerst die Azure AD-Objekt-ID abrufen. 

1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.

1. Geben Sie im Suchfeld oben auf der Seite `Azure Active Directory` ein. 

1. Klicken Sie auf `Enter`.

1. Wählen Sie im Menü **Verwalten** die Option **Benutzer**.

1. Suchen Sie im Namensfeld nach Ihrem Konto.

1. Wählen Sie in der Spalte **Name** den Link zu Ihrem Konto aus.

1. Kopieren Sie im Abschnitt **Identität** die **Objekt-ID**.

    ![Kopieren Sie die Azure AD-Objekt-ID.](./media/aks-configure-rbac/ansible-aad-object-id.png)

## <a name="create-rbac-binding"></a>Erstellen einer RBAC-Bindung

In diesem Abschnitt erstellen Sie eine Rollen- bzw. Clusterrollenbindung in AKS. 

Speichern Sie das folgende Playbook als `kube-role.yml`:

```yml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: <your-aad-account>
```

Ersetzen Sie den Platzhalter `<your-aad-account>` durch die [Objekt-ID](#get-the-azure-ad-object-id) Ihres Azure AD-Mandanten.

Speichern Sie das folgende Playbook, mit dem Ihre neue Rolle in AKS bereitgestellt wird, als `aks-kube-deploy.yml`:

```yml
- name: Apply role to AKS
  k8s:
      src: kube-role.yml
      kubeconfig: "aks-{{ name }}-kubeconfig"
```

## <a name="run-the-sample-playbook"></a>Ausführen des Beispielplaybooks

Dieser Abschnitt enthält das vollständige Beispielplaybook, mit dem die in diesem Artikel erstellten Aufgaben aufgerufen werden. 

Speichern Sie das folgende Playbook als `aks-rbac.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create AKS
       vars:
           client_app_id: <client id>
           server_app_id: <server id>
           server_app_secret: <server secret>
           app_tenant_id: <tenant id>
       include_tasks: aks-create.yml

     - name: Enable RBAC
       include_tasks: aks-kube-deploy.yml
```

Ersetzen Sie im Abschnitt `vars` die folgenden Platzhalter durch Ihre Azure AD-Informationen:

- `<client id>`
- `<server id>`
- `<server secret>`
- `<tenant id>`

Führen Sie das vollständige Playbook mit dem Befehl `ansible-playbook` aus:

```bash
ansible-playbook aks-rbac.yml
```

## <a name="verify-the-results"></a>Überprüfen der Ergebnisse

In diesem Abschnitt nutzen Sie kubectl, um die in diesem Artikel erstellten Knoten aufzulisten.

Geben Sie den folgenden Befehl an einer Terminaleingabeaufforderung ein:

```bash
kubectl --kubeconfig aks-aksansibletest-kubeconfig-user get nodes
```

Mit dem Befehl gelangen Sie zu einer Authentifizierungsseite. Melden Sie sich mit Ihrem Azure-Konto an.

Nach der Authentifizierung werden die Knoten von kubectl in etwa wie folgt aufgelistet:

```txt
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XXXXXXXX to authenticate.
NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-33413200-0   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-1   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-2   Ready    agent   49m   v1.12.6
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die in diesem Artikel erstellten Ressourcen, wenn Sie sie nicht mehr benötigen. 

Speichern Sie den folgenden Code als `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      name: aksansibletest
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
      - name: Remove kubeconfig
        file:
            state: absent
            path: "aks-{{ name }}-kubeconfig"
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Ansible unter Azure](/azure/ansible/)
