---
title: 'Tutorial: Bereitstellen von Apps für VM-Skalierungsgruppen in Azure mithilfe von Ansible'
description: Es wird beschrieben, wie Sie Ansible zum Konfigurieren von Azure-VM-Skalierungsgruppen und Bereitstellen von Anwendungen in der Skalierungsgruppe verwenden.
keywords: ansible, azure, devops, bash, playbook, vm, vm-skalierungsgruppen, vmss
ms.topic: tutorial
ms.date: 01/13/2020
ms.custom: devx-track-ansible
ms.openlocfilehash: 2ea2dbf740025797e6dd8514330e5b0c0de0df84
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831096"
---
# <a name="tutorial-deploy-apps-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Tutorial: Bereitstellen von Apps für VM-Skalierungsgruppen in Azure mit Ansible

[!INCLUDE [ansible-27-note.md](includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vm-scale-set.md](../includes/open-source-devops-intro-vm-scale-set.md)]

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Abrufen der Hostinformationen für eine Gruppe von virtuellen Azure-Computern
> * Klonen und Erstellen der Beispiel-App
> * Installieren einer JRE-Instanz (Java Runtime Environment) in einer Skalierungsgruppe
> * Bereitstellen der Java-Anwendung in einer Skalierungsgruppe

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](includes/ansible-prereqs-vm-scale-set.md)]
- **Git** - [Git](https://git-scm.com) wird verwendet, um ein in diesem Tutorial verwendetes Java-Beispiel herunterzuladen.
- **Java SE Development Kit (JDK)** : Mit dem [JDK](../java/fundamentals/java-jdk-long-term-support.md) wird das Java-Beispielprojekt erstellt.
- **Apache Maven** - [Apache Maven](https://maven.apache.org/download.cgi) wird verwendet, um das Java-Beispielprojekt zu erstellen.

## <a name="get-host-information"></a>Abrufen der Hostinformationen

Mit dem Playbookcode in diesem Abschnitt werden Hostinformationen für eine Gruppe virtueller Computer abgerufen. Der Code ruft die öffentlichen IP-Adressen und den Load Balancer in einer angegebenen Ressourcengruppe ab und erstellt im Bestand eine Hostgruppe mit dem Namen `scalesethosts`.

Speichern Sie das folgende Beispielplaybook als `get-hosts-tasks.yml`:

  ```yml
  - name: Get facts for all Public IPs within a resource groups
    azure_rm_publicipaddress_facts:
      resource_group: "{{ resource_group }}"
    register: output_ip_address

  - name: Get loadbalancer info
    azure_rm_loadbalancer_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ loadbalancer_name }}"
    register: output

  - name: Add all hosts
    add_host:
      groups: scalesethosts
      hostname: "{{ output_ip_address.publicipaddresses[0].ip_address }}_{{ item.properties.frontendPort }}"
      ansible_host: "{{ output_ip_address.publicipaddresses[0].ip_address }}"
      ansible_port: "{{ item.properties.frontendPort }}"
      ansible_ssh_user: "{{ admin_username }}"
      ansible_ssh_pass: "{{ admin_password }}"
    with_items:
      - "{{ output.ansible_info.azure_loadbalancers[0].properties.inboundNatRules }}"
  ```

## <a name="prepare-an-application-for-deployment"></a>Vorbereiten einer Anwendung für die Bereitstellung

Im Playbookcode in diesem Abschnitt wird `git` verwendet, um ein Java-Beispielprojekt von GitHub zu klonen, und das Projekt wird erstellt. 

Speichern Sie das folgende Playbook als `app.yml`:

  ```yml
  - hosts: localhost
    vars:
      repo_url: https://github.com/spring-guides/gs-spring-boot.git
      workspace: ~/src/helloworld

    tasks:
    - name: Git Clone sample app
      git:
        repo: "{{ repo_url }}"
        dest: "{{ workspace }}"

    - name: Build sample app
      shell: mvn package chdir="{{ workspace }}/complete"
  ```

Führen Sie das Ansible-Beispielplaybook mit dem folgenden Befehl aus:

  ```bash
  ansible-playbook app.yml
  ```

Nach dem Ausführen des Playbooks wird in etwa die folgende Ausgabe angezeigt:

  ```Output
  PLAY [localhost] 

  TASK [Gathering Facts] 
  ok: [localhost]

  TASK [Git Clone sample app] 
  changed: [localhost]

  TASK [Build sample app] 
  changed: [localhost]

  PLAY RECAP 
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-a-scale-set"></a>Bereitstellen der Anwendung in einer Skalierungsgruppe

Der Playbookcode in diesem Abschnitt wird für Folgendes verwendet:

* Installieren der JRE in einer Hostgruppe mit dem Namen `saclesethosts`
* Bereitstellen der Java-Anwendung in einer Hostgruppe mit dem Namen `saclesethosts`

Es gibt zwei Möglichkeiten, das Beispielplaybook abzurufen:

* [Laden Sie das Playbook herunter](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-setup-deploy.yml), und speichern Sie es als `vmss-setup-deploy.yml`.
* Erstellen Sie eine neue Datei mit dem Namen `vmss-setup-deploy.yml`, und kopieren Sie die folgenden Inhalte in diese Datei:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    scaleset_name: myScaleSet
    loadbalancer_name: myScaleSetLb
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
  tasks:
  - include: get-hosts-tasks.yml

- name: Install JRE on a scale set
  hosts: scalesethosts
  become: yes
  vars:
    workspace: ~/src/helloworld
    admin_username: azureuser

  tasks:
  - name: Install JRE
    apt:
      name: default-jre
      update_cache: yes

  - name: Copy app to Azure VM
    copy:
      src: "{{ workspace }}/complete/target/gs-spring-boot-0.1.0.jar"
      dest: "/home/{{ admin_username }}/helloworld.jar"
      force: yes
      mode: 0755

  - name: Start the application
    shell: java -jar "/home/{{ admin_username }}/helloworld.jar" >/dev/null 2>&1 &
    async: 5000
    poll: 0
```

Beachten Sie vor dem Ausführen des Playbooks die folgenden Hinweise:

* Ersetzen Sie im Abschnitt `vars` den Platzhalter `{{ admin_password }}` durch Ihr eigenes Kennwort.
* Installieren Sie das Programm sshpass, um den Verbindungstyp „ssh“ mit Kennwörtern zu verwenden:

    Ubuntu:

    ```bash
    apt-get install sshpass
    ```

    CentOS:

    ```bash
    yum install sshpass
    ```

* In einigen Umgebungen wird ggf. ein Fehler mit dem Hinweis angezeigt, das anstelle eines Schlüssels ein SSH-Kennwort verwendet werden muss. Wenn Sie diesen Fehler erhalten, können Sie die Überprüfung des Hostschlüssels deaktivieren, indem Sie `/etc/ansible/ansible.cfg` oder `~/.ansible.cfg` die folgende Zeile hinzufügen:

    ```bash
    [defaults]
    host_key_checking = False
    ```

Führen Sie das Playbook mit dem folgenden Befehl aus:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

In der Ausgabe nach Ausführung des Befehls „ansible-playbook“ ist angegeben, dass die Java-Beispielanwendung in der Hostgruppe der Skalierungsgruppe installiert wurde:

  ```Output
  PLAY [localhost]

  TASK [Gathering Facts]
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups]
  ok: [localhost]

  TASK [Get loadbalancer info]
  ok: [localhost]

  TASK [Add all hosts]
  changed: [localhost] ...

  PLAY [Install JRE on scale set]

  TASK [Gathering Facts]
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM]
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application]
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

## <a name="verify-the-results"></a>Überprüfen der Ergebnisse

Überprüfen Sie die Ergebnisse Ihrer Arbeit, indem Sie zur URL des Lastenausgleichs für Ihre Skalierungsgruppe navigieren:

![Ausgeführte Java-App in einer Skalierungsgruppe in Azure.](media/vm-scale-set-deploy-app/demo.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Automatisches Skalieren einer VM-Skalierungsgruppe in Azure mit Ansible](./vm-scale-set-auto-scale.md)