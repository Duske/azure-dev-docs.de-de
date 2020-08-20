---
title: Erstellen eines Kubernetes-Clusters mit Azure Kubernetes Service (AKS) unter Verwendung von Terraform
description: Es wird beschrieben, wie Sie einen Kubernetes-Cluster mit Azure Kubernetes Service und Terraform erstellen.
keywords: Azure DevOps Terraform AKS Kubernetes
ms.topic: how-to
ms.date: 03/09/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: 726f4544f4c417792d784dfaddf93a3d79eaec9e
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2020
ms.locfileid: "88241252"
---
# <a name="create-a-kubernetes-cluster-with-azure-kubernetes-service-using-terraform"></a>Erstellen eines Kubernetes-Clusters mit Azure Kubernetes Service unter Verwendung von Terraform

[Azure Kubernetes Service (AKS)](/azure/aks/) verwaltet Ihre gehostete Kubernetes-Umgebung. Mit AKS können Sie ganz ohne Kenntnisse im Bereich Containerorchestrierung containerbasierte Anwendungen bereitstellen und verwalten. Darüber hinaus lassen sich mit AKS viele gängige Wartungsvorgänge ausführen, ohne die App offline schalten zu müssen. Zu diesen Vorgängen zählen unter anderem das Bereitstellen, Upgraden und Skalieren von Ressourcen nach Bedarf.

In diesem Artikel lernen Sie Folgendes:

> [!div class="checklist"]
> * Verwenden von HCL (HashiCorp-Sprache) zum Definieren eines Kubernetes-Clusters
> * Verwenden von Terraform und AKS zum Erstellen eines Kubernetes-Clusters
> * Verwenden des kubectl-Tools zum Testen der Verfügbarkeit eines Kubernetes-Clusters

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

- **Konfigurieren von Terraform:** Befolgen Sie die Anweisungen im Artikel [Installieren und Konfigurieren von Terraform zum Bereitstellen von VMs und sonstiger Infrastruktur in Azure](get-started-cloud-shell.md).

- **Azure-Dienstprinzipal:** Befolgen Sie die Anweisungen im Abschnitt **Erstellen des Dienstprinzipals** des Artikels [Erstellen eines Azure-Dienstprinzipals mit der Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Notieren Sie sich die Werte für `appId`, `displayName`, `password` und `tenant`.

## <a name="create-the-directory-structure"></a>Erstellen der Verzeichnisstruktur

Der erste Schritt ist das Erstellen des Verzeichnisses, das Ihre Terraform-Konfigurationsdateien für die Übung enthält.

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).

1. Öffnen Sie [Azure Cloud Shell](/azure/cloud-shell/overview). Falls Sie zuvor noch keine Umgebung ausgewählt haben, wählen Sie **Bash** als Umgebung aus.

    ![Cloud Shell-Eingabeaufforderung](./media/create-k8s-cluster-with-tf-and-aks/azure-portal-cloud-shell-button-min.png)

1. Wechseln Sie in das Verzeichnis `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Erstellen Sie ein Verzeichnis namens `terraform-aks-k8s`.

    ```bash
    mkdir terraform-aks-k8s
    ```

1. Wechseln Sie zum neuen Verzeichnis:

    ```bash
    cd terraform-aks-k8s
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

## <a name="define-a-kubernetes-cluster"></a>Definieren eines Kubernetes-Clusters

Erstellen Sie die Terraform-Konfigurationsdatei, die die Ressourcen für den Kubernetes-Cluster deklariert.

1. Erstellen Sie in Cloud Shell eine Datei namens `k8s.tf`.

    ```bash
    code k8s.tf
    ```

1. Fügen Sie den folgenden Code in den Editor ein:

    ```hcl
    resource "azurerm_resource_group" "k8s" {
        name     = var.resource_group_name
        location = var.location
    }
    
    resource "random_id" "log_analytics_workspace_name_suffix" {
        byte_length = 8
    }

    resource "azurerm_log_analytics_workspace" "test" {
        # The WorkSpace name has to be unique across the whole of azure, not just the current subscription/tenant.
        name                = "${var.log_analytics_workspace_name}-${random_id.log_analytics_workspace_name_suffix.dec}"
        location            = var.log_analytics_workspace_location
        resource_group_name = azurerm_resource_group.k8s.name
        sku                 = var.log_analytics_workspace_sku
    }

    resource "azurerm_log_analytics_solution" "test" {
        solution_name         = "ContainerInsights"
        location              = azurerm_log_analytics_workspace.test.location
        resource_group_name   = azurerm_resource_group.k8s.name
        workspace_resource_id = azurerm_log_analytics_workspace.test.id
        workspace_name        = azurerm_log_analytics_workspace.test.name

        plan {
            publisher = "Microsoft"
            product   = "OMSGallery/ContainerInsights"
        }
    }

    resource "azurerm_kubernetes_cluster" "k8s" {
        name                = var.cluster_name
        location            = azurerm_resource_group.k8s.location
        resource_group_name = azurerm_resource_group.k8s.name
        dns_prefix          = var.dns_prefix

        linux_profile {
            admin_username = "ubuntu"

            ssh_key {
                key_data = file(var.ssh_public_key)
            }
        }

        default_node_pool {
            name            = "agentpool"
            node_count      = var.agent_count
            vm_size         = "Standard_D2_v2"
        }

        service_principal {
            client_id     = var.client_id
            client_secret = var.client_secret
        }

        addon_profile {
            oms_agent {
            enabled                    = true
            log_analytics_workspace_id = azurerm_log_analytics_workspace.test.id
            }
        }
        
        network_profile {
        load_balancer_sku = "Standard"
        network_plugin = "kubenet"
        }

        tags = {
            Environment = "Development"
        }
    }
    ```

    Der obige Code legt den Namen des Clusters, den Standort und den Ressourcengruppennamen fest. Des Weiteren wird das Präfix für den vollständig qualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) festgelegt. Der FQDN wird für den Zugriff auf den Cluster verwendet.

    Mit dem Datensatz `linux_profile` können Sie die Einstellungen konfigurieren, die eine Anmeldung bei Workerknoten mithilfe von SSH ermöglichen.

    Mit AKS zahlen Sie nur für die Workerknoten. Der Datensatz `default_node_pool` konfiguriert die Details für diese Workerknoten. Der Datensatz `default_node_pool record` enthält die Anzahl der zu erstellenden Workerknoten und den Typ der Workerknoten. Wenn Sie den Cluster in der Zukunft hochskalieren oder herunterskalieren müssen, ändern Sie den Wert `count` in diesem Datensatz.

1. Speichern Sie die Datei ( **&lt;STRG+S**), und beenden Sie den Editor ( **&lt;STRG+Q**).

## <a name="declare-the-variables"></a>Deklarieren der Variablen

1. Erstellen Sie in Cloud Shell eine Datei namens `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Fügen Sie den folgenden Code in den Editor ein:

    ```hcl
    variable "client_id" {}
    variable "client_secret" {}

    variable "agent_count" {
        default = 3
    }

    variable "ssh_public_key" {
        default = "~/.ssh/id_rsa.pub"
    }

    variable "dns_prefix" {
        default = "k8stest"
    }

    variable cluster_name {
        default = "k8stest"
    }

    variable resource_group_name {
        default = "azure-k8stest"
    }

    variable location {
        default = "Central US"
    }

    variable log_analytics_workspace_name {
        default = "testLogAnalyticsWorkspaceName"
    }

    # refer https://azure.microsoft.com/global-infrastructure/services/?products=monitor for log analytics available regions
    variable log_analytics_workspace_location {
        default = "eastus"
    }

   # refer https://azure.microsoft.com/pricing/details/monitor/ for log analytics pricing 
   variable log_analytics_workspace_sku {
        default = "PerGB2018"
   }
    ```

1. Speichern Sie die Datei ( **&lt;STRG+S**), und beenden Sie den Editor ( **&lt;STRG+Q**).

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
    ```

1. Speichern Sie die Datei ( **&lt;STRG+S**), und beenden Sie den Editor ( **&lt;STRG+Q**).

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Einrichten von Azure-Speicher zum Speichern des Terraform-Status

Terraform verfolgt den Status lokal über die Datei `terraform.tfstate`. Dieses Muster funktioniert gut in einer Umgebung mit nur einer Person. In einer Umgebung mit mehreren Personen wird [Azure Storage](/azure/storage/) verwendet, um den Zustand zu verfolgen.

In diesem Abschnitt lernen Sie, wie Sie folgende Aufgaben ausführen:
- Abrufen von Speicherkontoinformationen (Kontoname und Kontoschlüssel)
- Erstellen eines Speichercontainers zum Speichern von Terraform-Zustandsinformationen

1. Wählen Sie im Azure-Portal im linken Menü **Alle Dienste** aus.

1. Wählen Sie **Speicherkonten** aus.

1. Wählen Sie auf der Registerkarte **Speicherkonten** den Namen des Speicherkontos aus, in dem Terraform den Status speichern soll. Beispielsweise können Sie das Speicherkonto verwenden, das erstellt wurde, als Sie Cloud Shell zum ersten Mal geöffnet haben.  Der Name des von Cloud Shell erstellten Speicherkontos beginnt in der Regel mit `cs` gefolgt von einer zufälligen Zeichenfolge aus Zahlen und Buchstaben. Notieren Sie sich den Namen des ausgewählten Speicherkontos. Dieser Wert wird später benötigt.

1. Wählen Sie auf der Registerkarte des Speicherkontos **Zugriffsschlüssel** aus.

    ![Menü des Speicherkontos](./media/create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. Notieren Sie sich den **Schlüsselwert** von **Schlüssel1**. (Wenn Sie das Symbol auf der rechten Seite des Schlüssels auswählen, wird der Wert in die Zwischenablage kopiert.)

    ![Speicherkonto-Zugriffsschlüssel](./media/create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

1. Erstellen Sie in Cloud Shell einen Container in Ihrem Azure-Speicherkonto. Ersetzen Sie die Platzhalter durch entsprechende Werte für Ihre Umgebung.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Erstellen des Kubernetes-Clusters

In diesem Abschnitt erfahren Sie, wie der Befehl `terraform init` verwendet wird, um die Ressourcen zu erstellen, die in den Konfigurationsdateien definiert sind, die Sie in den vorherigen Abschnitten erstellt haben.

1. Initialisieren Sie Terraform in Cloud Shell. Ersetzen Sie die Platzhalter durch entsprechende Werte für Ihre Umgebung.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    Der Befehl `terraform init` zeigt den Erfolg der Initialisierung von Back-End- und -Anbieter-Plug-Ins an:

    ![Beispiel für Ergebnisse von „terraform init“](./media/create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. Exportieren Sie die Anmeldeinformationen für den Dienstprinzipal. Ersetzen Sie die Platzhalter durch entsprechende Werte Ihres Dienstprinzipals.

    ```bash
    export TF_VAR_client_id=<service-principal-appid>
    export TF_VAR_client_secret=<service-principal-password>
    ```

1. Führen Sie den Befehl `terraform plan` aus, um den Terraform-Plan zu erstellen, der die Infrastrukturelemente definiert. 

    ```bash
    terraform plan -out out.plan
    ```

    Der Befehl `terraform plan` zeigt die Ressourcen an, die erstellt werden, wenn Sie den Befehl `terraform apply` ausführen:

    ![Beispiel für Ergebnisse von „terraform plan“](./media/create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. Führen Sie den Befehl `terraform apply` aus, um den Plan zum Erstellen des Kubernetes-Clusters anzuwenden. Der Prozess zum Erstellen eines Kubernetes-Clusters dauert einige Minuten. Dies kann zu einem Timeout der Cloud Shell-Sitzung führen. Wenn ein Timeout der Cloud Shell-Sitzung eintritt, können Sie die Schritte im Abschnitt „Wiederherstellen nach einem Cloud Shell-Timeout“ ausführen, um den Prozess abzuschließen.

    ```bash
    terraform apply out.plan
    ```

    Der Befehl `terraform apply` zeigt die Ergebnisse der Erstellung der Ressourcen an, die in den Konfigurationsdateien definiert sind:

    ![Beispiel für Ergebnisse von „terraform apply“](./media/create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. Wählen Sie im Azure-Portal im linken Menü die Option **Alle Ressourcen** aus, um die Ressourcen anzuzeigen, die für Ihren neuen Kubernetes-Cluster erstellt wurden.

    ![Alle Ressourcen im Azure-Portal](./media/create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Wiederherstellen nach einem Cloud Shell-Timeout

Bei einem Timeout der Cloud Shell-Sitzung können Sie zur Wiederherstellung die folgenden Schritte ausführen:

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

    ![Das kubectl-Tool ermöglicht die Überprüfung der Integrität des Kubernetes-Clusters](./media/create-k8s-cluster-with-tf-and-aks/kubectl-get-nodes.png)

## <a name="monitor-health-and-logs"></a>Überwachung von Integrität und Protokollen

Beim Erstellen des AKS-Clusters wurde die Überwachung aktiviert, um Integritätsmetriken für die Clusterknoten und die Pods zu erfassen. Diese Integritätsmetriken sind im Azure-Portal verfügbar. Weitere Informationen zur Überwachung der Integrität von Containern finden Sie unter [Überwachen der Integrität von Azure Kubernetes Service](/azure/azure-monitor/insights/container-insights-overview).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Weitere Informationen zur Verwendung von Terraform in Azure](/azure/terraform)
