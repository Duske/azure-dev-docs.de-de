---
title: 'Tutorial: Verwalten von Webdatenverkehr per Azure Application Gateway mithilfe von Ansible'
description: Hier erfahren Sie, wie Sie mit Ansible eine Azure Application Gateway-Instanz zum Verwalten von Webdatenverkehr erstellen und konfigurieren.
keywords: Ansible, Azure, DevOps, Bash, Playbook, Application Gateway, Lastenausgleich, Webdatenverkehr
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 3ff9a43513a2ee34964e6e82447e3398469db8f8
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "81743720"
---
# <a name="tutorial-manage-web-traffic-with-azure-application-gateway-using-ansible"></a>Tutorial: Verwalten von Webdatenverkehr per Azure Application Gateway mit Ansible

[!INCLUDE [ansible-27-note.md](includes/ansible-27-note.md)]

[Azure Application Gateway](/azure/application-gateway/overview) ist ein Lastenausgleich für Webdatenverkehr, mit dem Sie eingehenden Datenverkehr für Ihre Webanwendungen verwalten können. Basierend auf der IP-Quelladresse und dem Quellport wird Datenverkehr von herkömmlichen Lastenausgleichsmodulen an eine IP-Zieladresse und einen Zielport weitergeleitet. Bei Verwendung von Application Gateway können Sie präziser steuern, wohin Datenverkehr basierend auf der URL geleitet werden kann. Sie können beispielsweise Folgendes definieren: Wenn `images` der Pfad der URL ist, wird Datenverkehr an eine bestimmte Gruppe von Servern (als Pool bezeichnet) geleitet, die für Images konfiguriert sind.

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Einrichten eines Netzwerks
> * Erstellen von zwei Azure-Containerinstanzen mit HTTPD-Images
> * Erstellen eines Anwendungsgateways für die Verwendung mit den Azure-Containerinstanzen im Serverpool

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Mit dem Playbookcode in diesem Abschnitt wird eine Azure-Ressourcengruppe erstellt. Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen konfiguriert werden.  

Speichern Sie das folgende Playbook als `rg.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

Beachten Sie vor dem Ausführen des Playbooks die folgenden Hinweise:

- Der Name der Ressourcengruppe lautet `myResourceGroup`. Dieser Wert wird im gesamten Tutorial verwendet.
- Die Ressourcengruppe wird am Standort `eastus` erstellt.

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Erstellen von Netzwerkressourcen

Mit dem Playbookcode in diesem Abschnitt wird ein virtuelles Netzwerk erstellt, damit das Anwendungsgateway mit anderen Ressourcen kommunizieren kann.

Speichern Sie das folgende Playbook als `vnet_create.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    vnet_name: myVNet 
    subnet_name: myAGSubnet 
    publicip_name: myAGPublicIPAddress
    publicip_domain: mydomain
  tasks:
    - name: Create a virtual network
      azure_rm_virtualnetwork:
        name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
            - 10.1.0.0/16
            - 172.100.0.0/16
        dns_servers:
            - 127.0.0.1
            - 127.0.0.2

    - name: Create a subnet
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: 10.1.0.0/24

    - name: Create a public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}" 
        allocation_method: Dynamic
        name: "{{ publicip_name }}"
        domain_name_label: "{{ publicip_domain }}"
```

Beachten Sie vor dem Ausführen des Playbooks die folgenden Hinweise:

* Der Abschnitt `vars` enthält die Werte, die zum Erstellen der Netzwerkressourcen verwendet werden. 
* Sie müssen diese Werte für Ihre jeweilige Umgebung ändern.

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>Erstellen von Servern

Mit dem Playbookcode in diesem Abschnitt werden zwei Azure-Containerinstanzen mit HTTPD-Images erstellt, die als Webserver für das Anwendungsgateway verwendet werden.  

Speichern Sie das folgende Playbook als `aci_create.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Create a container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_1_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80

    - name: Create another container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_2_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Erstellen des Anwendungsgateways

Mit dem Playbookcode in diesem Abschnitt wird ein Anwendungsgateway mit dem Namen `myAppGateway` erstellt.  

Speichern Sie das folgende Playbook als `appgw_create.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    vnet_name: myVNet
    subnet_name: myAGSubnet
    location: eastus
    publicip_name: myAGPublicIPAddress
    appgw_name: myAppGateway
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Get info of Subnet
      azure_rm_resource_facts:
        api_version: '2018-08-01'
        resource_group: "{{ resource_group }}"
        provider: network
        resource_type: virtualnetworks
        resource_name: "{{ vnet_name }}"
        subresource:
          - type: subnets
            name: "{{ subnet_name }}"
      register: subnet

    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_1_name }}"
      register: aci_1_output
    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_2_name }}"
      register: aci_2_output

    - name: Create instance of Application Gateway
      azure_rm_appgateway:
        resource_group: "{{ resource_group }}"
        name: "{{ appgw_name }}"
        sku:
          name: standard_small
          tier: standard
          capacity: 2
        gateway_ip_configurations:
          - subnet:
              id: "{{ subnet.response[0].id }}"
            name: appGatewayIP
        frontend_ip_configurations:
          - public_ip_address: "{{ publicip_name }}"
            name: appGatewayFrontendIP
        frontend_ports:
          - port: 80
            name: appGatewayFrontendPort
        backend_address_pools:
          - backend_addresses:
              - ip_address: "{{ aci_1_output.response[0].properties.ipAddress.ip }}"
              - ip_address: "{{ aci_2_output.response[0].properties.ipAddress.ip }}"
            name: appGatewayBackendPool
        backend_http_settings_collection:
          - port: 80
            protocol: http
            cookie_based_affinity: enabled
            name: appGatewayBackendHttpSettings
        http_listeners:
          - frontend_ip_configuration: appGatewayFrontendIP
            frontend_port: appGatewayFrontendPort
            name: appGatewayHttpListener
        request_routing_rules:
          - rule_type: Basic
            backend_address_pool: appGatewayBackendPool
            backend_http_settings: appGatewayBackendHttpSettings
            http_listener: appGatewayHttpListener
            name: rule1
```

Beachten Sie vor dem Ausführen des Playbooks die folgenden Hinweise:

* `appGatewayIP` wird im Block `gateway_ip_configurations` definiert. Für die IP-Konfiguration des Gateways ist ein Subnetzverweis erforderlich.
* `appGatewayBackendPool` wird im Block `backend_address_pools` definiert. Ein Anwendungsgateway muss über mindestens einen Back-End-Adresspool verfügen.
* `appGatewayBackendHttpSettings` wird im Block `backend_http_settings_collection` definiert. Hiermit wird angegeben, dass für die Kommunikation Port 80 und ein HTTP-Protokoll verwendet werden.
* `appGatewayHttpListener` wird im Block `backend_http_settings_collection` definiert. Hierbei handelt es sich um den Standardlistener, der „appGatewayBackendPool“ zugeordnet ist.
* `appGatewayFrontendIP` wird im Block `frontend_ip_configurations` definiert. Diese Einstellung weist „myAGPublicIPAddress“ zu „appGatewayHttpListener“ zu.
* `rule1` wird im Block `request_routing_rules` definiert. Hierbei handelt es sich um die Standardroutingregel, die „appGatewayHttpListener“ zugeordnet ist.

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook appgw_create.yml
```

Die Erstellung des Anwendungsgateways kann einige Minuten dauern.

## <a name="test-the-application-gateway"></a>Testen des Anwendungsgateways

1. Im Abschnitt [Erstellen einer Ressourcengruppe](#create-a-resource-group) geben Sie einen Standort an. Notieren Sie sich den entsprechenden Wert.

1. Im Abschnitt [Erstellen von Netzwerkressourcen](#create-network-resources) geben Sie die Domäne an. Notieren Sie sich den entsprechenden Wert.

1. Ersetzen Sie für die Test-URL das folgende Muster durch den Standort und die Domäne: `http://<domain>.<location>.cloudapp.azure.com`.

1. Navigieren Sie zur Test-URL.

1. Wenn die folgende Seite angezeigt wird, funktioniert das Anwendungsgateway wie erwartet.

    ![Erfolgreicher Test eines funktionierenden Anwendungsgateways](media/application-gateway-configure/demo.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die in diesem Artikel erstellten Ressourcen, wenn Sie sie nicht mehr benötigen. 

Speichern Sie den folgenden Code als `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Ansible unter Azure](/azure/ansible/)