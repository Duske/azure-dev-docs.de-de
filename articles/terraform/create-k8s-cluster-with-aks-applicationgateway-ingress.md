---
title: 'Tutorial: Erstellen eines Application Gateway-Eingangscontrollers in Azure Kubernetes Service'
description: Es wird beschrieben, wie Sie einen Kubernetes-Cluster mit Azure Kubernetes Service mit Application Gateway als Eingangscontroller erstellen.
keywords: Azure DevOps Terraform Application Gateway eingehend AKS Kubernetes
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: a98d9af5c8d3bd63451f007f66a8793d9f38a61d
ms.sourcegitcommit: 8cd0ddf1651c3b64bb72dedc2890108c2cfe3bcb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87334448"
---
# <a name="tutorial-create-an-application-gateway-ingress-controller-in-azure-kubernetes-service"></a>Tutorial: Erstellen eines Application Gateway-Eingangscontrollers in Azure Kubernetes Service

[Azure Kubernetes Service (AKS)](/azure/aks/) verwaltet Ihre gehostete Kubernetes-Umgebung. Mit AKS können Sie ganz ohne Kenntnisse im Bereich Containerorchestrierung schnell und einfach containerbasierte Anwendungen bereitstellen und verwalten. Mit AKS müssen Anwendungen für Betriebs- und Wartungsaufgaben nicht mehr manuell offline geschaltet werden. Diese Aufgaben, darunter auch das Bereitstellen, Upgraden und Skalieren von Ressourcen, können mit AKS bedarfsgerecht durchgeführt werden.

Für Kubernetes-Dienste bietet ein Eingangscontroller zahlreiche Funktionen. Zu diesen Funktionen gehören Reverseproxy, konfigurierbares Routing von Datenverkehr und TLS-Terminierung. Mithilfe von Ressourcen für eingehende Kubernetes-Daten werden Eingangsregeln für einzelne Kubernetes-Dienste konfiguriert. Durch die Verwendung von einem Eingangscontroller und Eingangsregeln kann eine einzelne IP-Adresse zum Weiterleiten von Datenverkehr an mehrere Dienste in einem Kubernetes-Cluster verwendet werden. Alle oben genannten Funktionen werden von Azure [Application Gateway](/azure/Application-Gateway/) bereitgestellt, was Application Gateway zu einem idealen Eingangscontroller für Kubernetes in Azure macht. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes)-Clusters mit AKS und Application Gateway als Eingangscontroller
> * Definieren eines Kubernetes-Clusters mithilfe von HCL (HashiCorp-Sprache)
> * Erstellen einer Application Gateway-Ressource mithilfe von Terraform
> * Verwenden von Terraform und AKS zum Erstellen eines Kubernetes-Clusters
> * Verwenden des kubectl-Tools zum Testen der Verfügbarkeit eines Kubernetes-Clusters

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

- **Konfigurieren von Terraform:** Befolgen Sie die Anweisungen im Artikel [Installieren und Konfigurieren von Terraform zum Bereitstellen von VMs und sonstiger Infrastruktur in Azure](get-started-cloud-shell.md).

- **Azure-Ressourcengruppe**: Sollten Sie über keine Azure-Ressourcengruppe für die Demo verfügen, [erstellen Sie eine Azure-Ressourcengruppe](/azure/azure-resource-manager/manage-resource-groups-portal#create-resource-groups). Notieren Sie sich Name und Standort der Ressourcengruppe, da diese Werte in der Demo verwendet werden.

- **Azure-Dienstprinzipal:** Befolgen Sie die Anweisungen im Abschnitt **Erstellen des Dienstprinzipals** des Artikels [Erstellen eines Azure-Dienstprinzipals mit der Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Notieren Sie sich die Werte für `appId`, `displayName` und `password`.

- **Abrufen der Dienstprinzipalobjekt-ID**: Führen Sie den folgenden Befehl in Cloud Shell aus: `az ad sp list --display-name <displayName>`.

## <a name="create-the-directory-structure"></a>Erstellen der Verzeichnisstruktur

Der erste Schritt ist das Erstellen des Verzeichnisses, das Ihre Terraform-Konfigurationsdateien für die Übung enthält.

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).

1. Öffnen Sie [Azure Cloud Shell](/azure/cloud-shell/overview).

1. Wechseln Sie in das Verzeichnis `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Erstellen Sie ein Verzeichnis namens `terraform-aks-appgw-ingress`.

    ```bash
    mkdir terraform-aks-appgw-ingress
    ```

1. Wechseln Sie zum neuen Verzeichnis:

    ```bash
    cd terraform-aks-appgw-ingress
    ```

## <a name="declare-the-azure-provider"></a>Deklarieren des Azure-Anbieters

Erstellen Sie die Terraform-Konfigurationsdatei, die den Azure-Anbieter deklariert.

1. Erstellen Sie in Cloud Shell eine Datei namens `main.tf`.

    ```bash
    code main.tf
    ```

1. Fügen Sie den folgenden Code in den Editor ein:

    ```hcl
    provider "azurerm" {
      # The "feature" block is required for AzureRM provider 2.x. 
      # If you are using version 1.x, the "features" block is not allowed.
      version = "~>2.0"
      features {}
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. Speichern Sie die Datei ( **&lt;STRG+S**), und beenden Sie den Editor ( **&lt;STRG+Q**).

## <a name="define-input-variables"></a>Definieren von Eingabevariablen

Erstellen Sie die Terraform-Konfigurationsdatei, in der alle für diese Bereitstellung erforderlichen Variablen aufgeführt werden.

1. Erstellen Sie in Cloud Shell eine Datei namens `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Fügen Sie den folgenden Code in den Editor ein:
    
    ```hcl
    variable "resource_group_name" {
      description = "Name of the resource group."
    }

    variable "location" {
      description = "Location of the cluster."
    }

    variable "aks_service_principal_app_id" {
      description = "Application ID/Client ID  of the service principal. Used by AKS to manage AKS related resources on Azure like vms, subnets."
    }

    variable "aks_service_principal_client_secret" {
      description = "Secret of the service principal. Used by AKS to manage Azure."
    }

    variable "aks_service_principal_object_id" {
      description = "Object ID of the service principal."
    }

    variable "virtual_network_name" {
      description = "Virtual network name"
      default     = "aksVirtualNetwork"
    }

    variable "virtual_network_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/8"
    }

    variable "aks_subnet_name" {
      description = "AKS Subnet Name."
      default     = "kubesubnet"
    }

    variable "aks_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/16"
    }

    variable "app_gateway_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.1.0.0/16"
    }

    variable "app_gateway_name" {
      description = "Name of the Application Gateway."
      default = "ApplicationGateway1"
    }

    variable "app_gateway_sku" {
      description = "Name of the Application Gateway SKU."
      default = "Standard_v2"
    }

    variable "app_gateway_tier" {
      description = "Tier of the Application Gateway SKU."
      default = "Standard_v2"
    }

    variable "aks_name" {
      description = "Name of the AKS cluster."
      default     = "aks-cluster1"
    }
    variable "aks_dns_prefix" {
      description = "Optional DNS prefix to use with hosted Kubernetes API server FQDN."
      default     = "aks"
    }

    variable "aks_agent_os_disk_size" {
      description = "Disk size (in GB) to provision for each of the agent pool nodes. This value ranges from 0 to 1023. Specifying 0 applies the default disk size for that agentVMSize."
      default     = 40
    }

    variable "aks_agent_count" {
      description = "The number of agent nodes for the cluster."
      default     = 3
    }

    variable "aks_agent_vm_size" {
      description = "The size of the Virtual Machine."
      default     = "Standard_D3_v2"
    }

    variable "kubernetes_version" {
      description = "The version of Kubernetes."
      default     = "1.11.5"
    }

    variable "aks_service_cidr" {
      description = "A CIDR notation IP range from which to assign service cluster IPs."
      default     = "10.0.0.0/16"
    }

    variable "aks_dns_service_ip" {
      description = "Containers DNS server IP address."
      default     = "10.0.0.10"
    }

    variable "aks_docker_bridge_cidr" {
      description = "A CIDR notation IP for Docker bridge."
      default     = "172.17.0.1/16"
    }

    variable "aks_enable_rbac" {
      description = "Enable RBAC on the AKS cluster. Defaults to false."
      default     = "false"
    }

    variable "vm_user_name" {
      description = "User name for the VM"
      default     = "vmuser1"
    }

    variable "public_ssh_key_path" {
      description = "Public key path for SSH."
      default     = "~/.ssh/id_rsa.pub"
    }

    variable "tags" {
      type = map(string)

      default = {
        source = "terraform"
      }
    }
    ```

1. Speichern Sie die Datei ( **&lt;STRG+S**), und beenden Sie den Editor ( **&lt;STRG+Q**).

## <a name="define-the-resources"></a>Definieren der Ressourcen 
Erstellen Sie die Terraform-Konfigurationsdatei für die Erstellung der Ressourcen. 

1. Erstellen Sie in Cloud Shell eine Datei namens `resources.tf`.

    ```bash
    code resources.tf
    ```

1. Fügen Sie den folgenden Codeblock ein, um einen lokalen Block für berechnete Variablen zur Wiederverwendung zu erstellen:

    ```hcl
    # # Locals block for hardcoded names. 
    locals {
        backend_address_pool_name      = "${azurerm_virtual_network.test.name}-beap"
        frontend_port_name             = "${azurerm_virtual_network.test.name}-feport"
        frontend_ip_configuration_name = "${azurerm_virtual_network.test.name}-feip"
        http_setting_name              = "${azurerm_virtual_network.test.name}-be-htst"
        listener_name                  = "${azurerm_virtual_network.test.name}-httplstn"
        request_routing_rule_name      = "${azurerm_virtual_network.test.name}-rqrt"
        app_gateway_subnet_name = "appgwsubnet"
    }
    ```

1. Fügen Sie den folgenden Codeblock ein, um eine Datenquelle für die Ressourcengruppe und eine neue Benutzeridentität zu erstellen:

    ```hcl
    data "azurerm_resource_group" "rg" {
      name = var.resource_group_name
    }

    # User Assigned Identities 
    resource "azurerm_user_assigned_identity" "testIdentity" {
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      name = "identity1"

      tags = var.tags
    }
    ```

1. Fügen Sie den folgenden Codeblock ein, um Basisnetzwerkressourcen zu erstellen:

    ```hcl
    resource "azurerm_virtual_network" "test" {
      name                = var.virtual_network_name
      location            = data.azurerm_resource_group.rg.location
      resource_group_name = data.azurerm_resource_group.rg.name
      address_space       = [var.virtual_network_address_prefix]

      subnet {
        name           = var.aks_subnet_name
        address_prefix = var.aks_subnet_address_prefix
      }

      subnet {
        name           = "appgwsubnet"
        address_prefix = var.app_gateway_subnet_address_prefix
      }

      tags = var.tags
    }

    data "azurerm_subnet" "kubesubnet" {
      name                 = var.aks_subnet_name
      virtual_network_name = azurerm_virtual_network.test.name
      resource_group_name  = data.azurerm_resource_group.rg.name
    }

    data "azurerm_subnet" "appgwsubnet" {
      name                 = "appgwsubnet"
      virtual_network_name = azurerm_virtual_network.test.name
      resource_group_name  = data.azurerm_resource_group.rg.name
    }

    # Public Ip 
    resource "azurerm_public_ip" "test" {
      name                         = "publicIp1"
      location                     = data.azurerm_resource_group.rg.location
      resource_group_name          = data.azurerm_resource_group.rg.name
      allocation_method            = "Static"
      sku                          = "Standard"

      tags = var.tags
    }
    ```

1. Fügen Sie den folgenden Codeblock ein, um Application Gateway-Ressourcen zu erstellen:

    ```hcl
    resource "azurerm_application_gateway" "network" {
      name                = var.app_gateway_name
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      sku {
        name     = var.app_gateway_sku
        tier     = "Standard_v2"
        capacity = 2
      }

      gateway_ip_configuration {
        name      = "appGatewayIpConfig"
        subnet_id = data.azurerm_subnet.appgwsubnet.id
      }

      frontend_port {
        name = local.frontend_port_name
        port = 80
      }

      frontend_port {
        name = "httpsPort"
        port = 443
      }

      frontend_ip_configuration {
        name                 = local.frontend_ip_configuration_name
        public_ip_address_id = azurerm_public_ip.test.id
      }

      backend_address_pool {
        name = local.backend_address_pool_name
      }

      backend_http_settings {
        name                  = local.http_setting_name
        cookie_based_affinity = "Disabled"
        port                  = 80
        protocol              = "Http"
        request_timeout       = 1
      }

      http_listener {
        name                           = local.listener_name
        frontend_ip_configuration_name = local.frontend_ip_configuration_name
        frontend_port_name             = local.frontend_port_name
        protocol                       = "Http"
      }

      request_routing_rule {
        name                       = local.request_routing_rule_name
        rule_type                  = "Basic"
        http_listener_name         = local.listener_name
        backend_address_pool_name  = local.backend_address_pool_name
        backend_http_settings_name = local.http_setting_name
      }

      tags = var.tags

      depends_on = [azurerm_virtual_network.test, azurerm_public_ip.test]
    }
    ```

1. Fügen Sie den folgenden Codeblock ein, um Rollenzuweisungen zu erstellen:

    ```hcl
    resource "azurerm_role_assignment" "ra1" {
      scope                = data.azurerm_subnet.kubesubnet.id
      role_definition_name = "Network Contributor"
      principal_id         = var.aks_service_principal_object_id 

      depends_on = [azurerm_virtual_network.test]
    }

    resource "azurerm_role_assignment" "ra2" {
      scope                = azurerm_user_assigned_identity.testIdentity.id
      role_definition_name = "Managed Identity Operator"
      principal_id         = var.aks_service_principal_object_id
      depends_on           = [azurerm_user_assigned_identity.testIdentity]
    }

    resource "azurerm_role_assignment" "ra3" {
      scope                = azurerm_application_gateway.network.id
      role_definition_name = "Contributor"
      principal_id         = azurerm_user_assigned_identity.testIdentity.principal_id
      depends_on           = [azurerm_user_assigned_identity.testIdentity, azurerm_application_gateway.network]
    }

    resource "azurerm_role_assignment" "ra4" {
      scope                = data.azurerm_resource_group.rg.id
      role_definition_name = "Reader"
      principal_id         = azurerm_user_assigned_identity.testIdentity.principal_id
      depends_on           = [azurerm_user_assigned_identity.testIdentity, azurerm_application_gateway.network]
    }
    ```

1. Fügen Sie den folgenden Codeblock ein, um den Kubernetes-Cluster zu erstellen:

    ```hcl
    resource "azurerm_kubernetes_cluster" "k8s" {
      name       = var.aks_name
      location   = data.azurerm_resource_group.rg.location
      dns_prefix = var.aks_dns_prefix

      resource_group_name = data.azurerm_resource_group.rg.name

      linux_profile {
        admin_username = var.vm_user_name

        ssh_key {
          key_data = file(var.public_ssh_key_path)
        }
      }

      addon_profile {
        http_application_routing {
          enabled = false
        }
      }

      default_node_pool {
        name            = "agentpool"
        node_count      = var.aks_agent_count
        vm_size         = var.aks_agent_vm_size
        os_disk_size_gb = var.aks_agent_os_disk_size
        vnet_subnet_id  = data.azurerm_subnet.kubesubnet.id
      }

      service_principal {
        client_id     = var.aks_service_principal_app_id
        client_secret = var.aks_service_principal_client_secret
      }

      network_profile {
        network_plugin     = "azure"
        dns_service_ip     = var.aks_dns_service_ip
        docker_bridge_cidr = var.aks_docker_bridge_cidr
        service_cidr       = var.aks_service_cidr
      }

      depends_on = [azurerm_virtual_network.test, azurerm_application_gateway.network]
      tags       = var.tags
    }

    ```

1. Speichern Sie die Datei ( **&lt;STRG+S**), und beenden Sie den Editor ( **&lt;STRG+Q**).

Der in diesem Abschnitt aufgeführte Code legt den Namen des Clusters, den Speicherort und „resource_group_name“ fest. Der Wert `dns_prefix` wird festgelegt, der Teil des vollqualifizierten Domänennamens (FQDN) ist, der für den Zugriff auf den Cluster verwendet wird.

Mit dem Datensatz `linux_profile` können Sie die Einstellungen konfigurieren, die eine Anmeldung bei Workerknoten mithilfe von SSH ermöglichen.

Mit AKS zahlen Sie nur für die Workerknoten. Der Datensatz `agent_pool_profile` konfiguriert die Details für diese Workerknoten. Der Datensatz `agent_pool_profile record` enthält die Anzahl der zu erstellenden Workerknoten und den Typ der Workerknoten. Wenn Sie den Cluster in der Zukunft hochskalieren oder herunterskalieren müssen, ändern Sie den Wert `count` in diesem Datensatz.

## <a name="create-a-terraform-output-file"></a>Erstellen einer Terraform-Ausgabedatei

Mit [Terraform-Ausgaben](https://www.terraform.io/docs/configuration/outputs.html) können Sie Werte definieren, die für den Benutzer hervorgehoben werden, wenn Terraform einen Plan anwendet. Sie können mit dem Befehl `terraform output` abgefragt werden. In diesem Abschnitt erstellen Sie eine Ausgabedatei, die Zugriff auf den Cluster mit [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) ermöglicht.

1. Erstellen Sie in Cloud Shell eine Datei namens `output.tf`.

    ```bash
    code output.tf
    ```

1. Fügen Sie den folgenden Code in den Editor ein:

    ```hcl
    output "client_key" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_key
    }

    output "client_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate
    }

    output "cluster_ca_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate
    }

    output "cluster_username" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.username
    }

    output "cluster_password" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.password
    }

    output "kube_config" {
        value = azurerm_kubernetes_cluster.k8s.kube_config_raw
    }

    output "host" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.host
    }

    output "identity_resource_id" {
        value = azurerm_user_assigned_identity.testIdentity.id
    }

    output "identity_client_id" {
        value = azurerm_user_assigned_identity.testIdentity.client_id
    }
    ```

1. Speichern Sie die Datei ( **&lt;STRG+S**), und beenden Sie den Editor ( **&lt;STRG+Q**).

## <a name="configure-azure-storage-to-store-terraform-state"></a>Konfigurieren von Azure-Speicher zum Speichern des Terraform-Status

Terraform verfolgt den Status lokal über die Datei `terraform.tfstate`. Dieses Muster funktioniert gut in einer Umgebung mit nur einer Person. In einer praxisorientierteren Umgebung mit mehreren Personen müssen Sie den Status allerdings auf dem Server mit [Azure Storage](/azure/storage/) nachverfolgen. In diesem Abschnitt erfahren Sie, wie Sie die erforderlichen Speicherkontoinformationen abrufen und einen Speichercontainer erstellen. Die Terraform-Statusinformationen werden dann in diesem Container gespeichert.

1. Wählen Sie im Azure-Portal unter **Azure-Dienste** die Option **Speicherkonten** aus. (Sollte die Option **Speicherkonten** auf der Hauptseite nicht angezeigt werden, wählen Sie **Weitere Dienste** aus, suchen Sie nach der Option, und wählen Sie sie aus.)

1. Wählen Sie auf der Seite **Speicherkonten** den Namen des Speicherkontos aus, in dem Terraform den Zustand speichern soll. Beispielsweise können Sie das Speicherkonto verwenden, das erstellt wurde, als Sie Cloud Shell zum ersten Mal geöffnet haben.  Der Name des von Cloud Shell erstellten Speicherkontos beginnt in der Regel mit `cs` gefolgt von einer zufälligen Zeichenfolge aus Zahlen und Buchstaben. 

    Notieren Sie sich den Namen des ausgewählten Speicherkontos, da Sie ihn später benötigen.

1. Wählen Sie auf der Seite „Speicherkonto“ die Option **Zugriffsschlüssel** aus.

    ![Menü des Speicherkontos](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. Notieren Sie sich den **Schlüsselwert** von **Schlüssel1**. (Wenn Sie das Symbol auf der rechten Seite des Schlüssels auswählen, wird der Wert in die Zwischenablage kopiert.)

    ![Speicherkonto-Zugriffsschlüssel](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

1. Erstellen Sie in Cloud Shell einen Container in Ihrem Azure-Speicherkonto. Ersetzen Sie die Platzhalter durch die entsprechenden Werte für Ihr Azure-Speicherkonto.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Erstellen des Kubernetes-Clusters
In diesem Abschnitt erfahren Sie, wie der Befehl `terraform init` verwendet wird, um die Ressourcen zu erstellen, die in den Konfigurationsdateien definiert sind, die Sie in den vorherigen Abschnitten erstellt haben.

1. Initialisieren Sie Terraform in Cloud Shell. Ersetzen Sie die Platzhalter durch die entsprechenden Werte für Ihr Azure-Speicherkonto.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
  
    Der Befehl `terraform init` zeigt den Erfolg der Initialisierung von Back-End- und -Anbieter-Plug-Ins an:

    ![Beispiel für Ergebnisse von „terraform init“](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. Erstellen Sie in Cloud Shell eine Datei namens `terraform.tfvars`:

    ```bash
    code terraform.tfvars
    ```

1. Fügen Sie die folgenden Variablen, die Sie weiter oben erstellt haben, in den Editor ein. Verwenden Sie `az account list-locations`, um den Standortwert für Ihre Umgebung zu erhalten.

    ```hcl
    resource_group_name = "<Name of the Resource Group already created>"

    location = "<Location of the Resource Group>"
      
    aks_service_principal_app_id = "<Service Principal AppId>"
      
    aks_service_principal_client_secret = "<Service Principal Client Secret>"
      
    aks_service_principal_object_id = "<Service Principal Object Id>"
        
    ```

1. Speichern Sie die Datei ( **&lt;STRG+S**), und beenden Sie den Editor ( **&lt;STRG+Q**).

1. Führen Sie den Befehl `terraform plan` aus, um den Terraform-Plan zu erstellen, der die Infrastrukturelemente definiert. 

    ```bash
    terraform plan -out out.plan
    ```

    Der Befehl `terraform plan` zeigt die Ressourcen an, die beim Ausführen des Befehls `terraform apply` erstellt werden:

    ![Beispiel für Ergebnisse von „terraform plan“](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. Führen Sie den Befehl `terraform apply` aus, um den Plan zum Erstellen des Kubernetes-Clusters anzuwenden. Der Prozess zum Erstellen eines Kubernetes-Clusters dauert einige Minuten. Dies kann zu einem Timeout der Cloud Shell-Sitzung führen. Wenn ein Timeout der Cloud Shell-Sitzung eintritt, können Sie die Schritte im Abschnitt „Wiederherstellen nach einem Cloud Shell-Timeout“ ausführen, um das Tutorial abzuschließen.

    ```bash
    terraform apply out.plan
    ```

    Der Befehl `terraform apply` zeigt die Ergebnisse der Erstellung der Ressourcen an, die in den Konfigurationsdateien definiert sind:

    ![Beispiel für Ergebnisse von „terraform apply“](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-apply-complete.png)

1. Wählen Sie im Azure-Portal im linken Menü die Option **Ressourcengruppen** aus, um die Ressourcen anzuzeigen, die für Ihren neuen Kubernetes-Cluster in der ausgewählten Ressourcengruppe erstellt wurden.

    ![Cloud Shell-Eingabeaufforderung](./media/terraform-k8s-cluster-appgw-with-tf-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Wiederherstellen nach einem Cloud Shell-Timeout

Bei einem Timeout der Cloud Shell-Sitzung können Sie zur Wiederherstellung die folgenden Schritte ausführen:

1. Starten Sie eine Cloud Shell-Sitzung.

1. Wechseln Sie zu dem Verzeichnis, das Ihre Terraform Konfigurationsdateien enthält.

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. Führen Sie den folgenden Befehl aus:

    ```bash
    export KUBECONFIG=./azurek8s
    ```
  
## <a name="test-the-kubernetes-cluster"></a>Testen des Kubernetes-Clusters
Die Kubernetes-Tools können verwendet werden, um den neu erstellten Cluster zu überprüfen.

1. Rufen Sie die Kubernetes-Konfiguration aus dem Terraform-Status ab, und speichern Sie sie in einer Datei, die kubectl lesen kann.

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. Legen Sie eine Umgebungsvariable so fest, dass kubectl die richtige Konfiguration auswählt.

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. Überprüfen Sie die Integrität des Clusters.

    ```bash
    kubectl get nodes
    ```

    Sie sollten die Details der Workerknoten sehen, und sie sollten alle den Status **Ready** aufweisen, wie in der folgenden Abbildung dargestellt:

    ![Das kubectl-Tool ermöglicht die Überprüfung der Integrität des Kubernetes-Clusters](./media/terraform-k8s-cluster-appgw-with-tf-aks/kubectl-get-nodes.png)

## <a name="install-azure-ad-pod-identity"></a>Installieren einer Azure AD-Podidentität

Die Azure Active Directory-Podidentität bietet tokenbasierten Zugriff auf [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview).

Die [Azure AD-Podidentität](https://github.com/Azure/aad-pod-identity) fügt Ihrem Kubernetes-Cluster folgende Komponenten hinzu:

  - Kubernetes-[CRDs](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/): `AzureIdentity`, `AzureAssignedIdentity` und `AzureIdentityBinding`
  - [MIC-Komponente (Managed Identity Controller)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)
  - [NMI-Komponente (Node Managed Identity)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)

Wenn RBAC **aktiviert** ist, führen Sie den folgenden Befehl aus, um die Azure AD-Podidentität in Ihrem Cluster zu installieren:

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
```

Wenn RBAC **deaktiviert** ist, führen Sie den folgenden Befehl aus, um die Azure AD-Podidentität in Ihrem Cluster zu installieren:

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
```

## <a name="install-helm"></a>Installieren von Helm

Der Code in diesem Abschnitt verwendet [Helm](/azure/aks/kubernetes-helm), den Kubernetes-Paketmanager, um das Paket `application-gateway-kubernetes-ingress` zu installieren:

1. Wenn RBAC **aktiviert** ist, führen Sie den folgenden Befehlssatz zum Installieren und Konfigurieren von Helm aus:

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

1. Wenn RBAC **deaktiviert** ist, führen Sie den folgenden Befehl zum Installieren und Konfigurieren von Helm aus:

    ```bash
    helm init
    ```

1. Fügen Sie das AGIC-Helm-Repository hinzu:

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="install-ingress-controller-helm-chart"></a>Installieren des Helm-Eingangscontrollerdiagramms

1. Laden Sie zum Konfigurieren von AGIC `helm-config.yaml` herunter:

    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```

1. Bearbeiten Sie `helm-config.yaml`, und geben Sie die entsprechenden Werte für die Abschnitte `appgw` und `armAuth` ein.

    ```bash
    code helm-config.yaml
    ```

    Die Werte lassen sich wie folgt beschreiben:

    - `verbosityLevel`: Legt die Ausführlichkeitsebene der AGIC-Protokollinfrastruktur fest. Unter [Protokolliergrade](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) finden Sie mögliche Werte.
    - `appgw.subscriptionId`: Die Azure-Abonnement-ID für die Application Gateway-Instanz. Beispiel: `a123b234-a3b4-557d-b2df-a0bc12de1234`
    - `appgw.resourceGroup`: Name der Azure-Ressourcengruppe, in der die Application Gateway-Instanz erstellt wurde. 
    - `appgw.name`: Name der Application Gateway-Instanz. Beispiel: `applicationgateway1`.
    - `appgw.shared`: Für dieses boolesche Flag muss standardmäßig `false` festgelegt werden. Setzen Sie es auf `true`, wenn Sie eine [freigegebene Application Gateway-Instanz](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway) benötigen.
    - `kubernetes.watchNamespace`: Geben Sie den Namespace an, der von AGIC überwacht werden soll. Beim Namespace kann es sich um eine einzelne Zeichenfolge oder eine durch Trennzeichen getrennte Namespaceliste handeln. Wenn Sie diese Variable auskommentiert lassen oder auf eine leere Zeichenfolge festlegen, überwacht der Eingangscontroller alle zugänglichen Namespaces.
    - `armAuth.type`: Ein Wert von `aadPodIdentity` oder `servicePrincipal`
    - `armAuth.identityResourceID`: Ressourcen-ID der verwalteten Identität
    - `armAuth.identityClientId`: Die Client-ID der Identität
    - `armAuth.secretJSON`: Nur erforderlich, wenn der Typ des Dienstprinzipalgeheimnisses ausgewählt wird (beim Festlegen von `armAuth.type` auf `servicePrincipal`)

    Wichtige Hinweise:
    - Der Wert `identityResourceID` wird im Terraform-Skript erstellt und kann abgerufen werden, indem Sie `echo "$(terraform output identity_resource_id)"` ausführen.
    - Der Wert `identityClientID` wird im Terraform-Skript erstellt und kann abgerufen werden, indem Sie `echo "$(terraform output identity_client_id)"` ausführen.
    - Der Wert `<resource-group>` ist die Ressourcengruppe Ihrer Application Gateway-Instanz.
    - Der Wert `<identity-name>` ist der Name der erstellten Identität.
    - Alle Identitäten eines bestimmten Abonnements werden unter Verwendung von `az identity list` aufgelistet.

1. Installieren Sie das Application Gateway-Eingangscontrollerpaket:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

### <a name="install-a-sample-app"></a>Installieren einer Beispiel-App

Nach der Installation der Application Gateway-Instanz sowie von AKS und AGIC können Sie die Beispiel-App über [Azure Cloud Shell](https://shell.azure.com/) installieren:

1. Verwenden Sie den cURL-Befehl, um die YAML-Datei herunterzuladen:

    ```bash
    curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
    ```

2. Wenden Sie die YAML-Datei an:

    ```bash
    kubectl apply -f aspnetapp.yaml
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die in diesem Artikel erstellten Ressourcen, wenn Sie sie nicht mehr benötigen.  

Ersetzen Sie den Platzhalter durch den entsprechenden Wert. Alle Ressourcen innerhalb der angegebenen Ressourcengruppe werden gelöscht.

```azurecli
az group delete -n <resource-group>
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Application Gateway-Eingangscontroller](https://azure.github.io/application-gateway-kubernetes-ingress/)