---
title: 'Tutorial: Konfigurieren von Apps in Azure App Service mithilfe von Ansible'
description: Es wird beschrieben, wie Sie eine App in Azure App Service mit Java 8 und der Tomcat-Containerruntime erstellen.
keywords: Ansible, Azure, DevOps, Bash, Playbook, Azure App Service, Web-App, Java
ms.topic: tutorial
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: 83bd47fca1e04aa55bbebca84b3f52bdc9f52ecc
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2020
ms.locfileid: "88240372"
---
# <a name="tutorial-configure-apps-in-azure-app-service-using-ansible"></a>Tutorial: Konfigurieren von Apps in Azure App Service mit Ansible

[!INCLUDE [ansible-27-note.md](includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Erstellen einer App in Azure App Service mit Java 8 und der Tomcat-Containerruntime
> * Erstellen eines Azure Traffic Manager-Profils
> * Definieren eines Traffic Manager-Endpunkts mit der erstellten App

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-basic-app-service"></a>Erstellen eines einfachen App-Diensts

Mit dem Playbookcode in diesem Abschnitt werden die folgenden Ressourcen definiert:

* Azure-Ressourcengruppe, in der der App Service-Plan und die App bereitgestellt werden
* App Service unter Linux mit Java 8 und der Tomcat-Containerruntime

Speichern Sie das folgende Playbook als `firstwebapp.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    webapp_name: myfirstWebApp
    plan_name: myAppServicePlan
    location: eastus
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create App Service on Linux with Java Runtime
      azure_rm_webapp:
        resource_group: "{{ resource_group }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        frameworks:
          - name: "java"
            version: "8"
            settings:
              java_container: tomcat
              java_container_version: 8.5
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook firstwebapp.yml
```

Nach dem Ausführen des Playbooks wird in etwa die folgende Ausgabe angezeigt:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="create-an-app-and-use-azure-traffic-manager"></a>Erstellen einer App und Verwenden von Azure Traffic Manager

Mit [Azure Traffic Manager](/azure/app-service/web-sites-traffic-manager) können Sie steuern, wie Anforderungen von Webclients auf Apps in Azure App Service verteilt werden. Wenn einem Azure Traffic Manager-Profil App Service-Endpunkte hinzugefügt werden, überwacht Traffic Manager den Status Ihrer App Service-Apps. Mögliche Statuswerte sind „Ausgeführt“, „Beendet“ und „Gelöscht“. Traffic Manager wird verwendet, um zu entscheiden, welche Endpunkte den Datenverkehr empfangen sollen.

In App Service wird eine App in einem [App Service-Plan](/azure/app-service/overview-hosting-plans) ausgeführt. Mit einem App Service-Plan wird ein Satz mit Computeressourcen für eine auszuführende App definiert. Sie können Ihren App Service-Plan und die Web-App in unterschiedlichen Gruppen verwalten.

Mit dem Playbookcode in diesem Abschnitt werden die folgenden Ressourcen definiert:

* Azure-Ressourcengruppe, in der der App Service-Plan bereitgestellt wird
* App Service-Plan
* Azure-Ressourcengruppe, in der die App bereitgestellt wird
* App Service unter Linux mit Java 8 und der Tomcat-Containerruntime
* Traffic Manager-Profil
* Traffic Manager-Endpunkt, für den die erstellte App verwendet wird

Speichern Sie das folgende Playbook als `webapp.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group_webapp: myResourceGroupWebapp
    resource_group: myResourceGroup
    webapp_name: myLinuxWebApp
    plan_name: myAppServicePlan
    location: eastus
    traffic_manager_profile_name: myTrafficManagerProfile
    traffic_manager_endpoint_name: myTrafficManagerEndpoint

  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group_webapp }}"
        location: "{{ location }}"

  - name: Create secondary resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

  - name: Create App Service Plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      location: "{{ location }}"
      is_linux: true
      sku: S1
      number_of_workers: 1

  - name: Create App Service on Linux with Java Runtime
    azure_rm_webapp:
        resource_group: "{{ resource_group_webapp }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        app_settings:
          testkey: "testvalue"
        frameworks:
          - name: java
            version: 8
            settings:
              java_container: "Tomcat"
              java_container_version: "8.5"

  - name: Get web app facts
    azure_rm_webapp_facts:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ webapp_name }}"
    register: webapp
    
  - name: Create Traffic Manager Profile
    azure_rm_trafficmanagerprofile:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ traffic_manager_profile_name }}"
      location: global
      routing_method: performance
      dns_config:
        relative_name: "{{ traffic_manager_profile_name }}"
        ttl:  60
      monitor_config:
        protocol: HTTPS
        port: 80
        path: '/'

  - name: Add endpoint to traffic manager profile, using created web site
    azure_rm_trafficmanagerendpoint:
      resource_group: "{{ resource_group_webapp }}"
      profile_name: "{{ traffic_manager_profile_name }}"
      name: "{{ traffic_manager_endpoint_name }}"
      type: azure_endpoints
      location: "{{ location }}"
      target_resource_id: "{{ webapp.webapps[0].id }}"
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook webapp.yml
```

Nach dem Ausführen des Playbooks wird in etwa die folgende Ausgabe angezeigt:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create resource group for app service plan] 
changed: [localhost]

TASK [Create App Service Plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
changed: [localhost]

TASK [Get web app facts] 
ok: [localhost]

TASK [Create Traffic Manager Profile] 
 [WARNING]: Azure API profile latest does not define an entry for TrafficManagerManagementClient

changed: [localhost]

TASK [Add endpoint to traffic manager profile, using the web site created above] 
changed: [localhost]

TASK [Get Traffic Manager Profile facts] 
ok: [localhost]

PLAY RECAP 
localhost                  : ok=9    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Tutorial: Skalieren von Apps in Azure App Service mit Ansible](/azure/ansible/ansible-scale-azure-web-apps)