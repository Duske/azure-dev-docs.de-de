---
title: 'Tutorial: Verwenden von Azure Storage für Buildartefakte'
description: Es wird beschrieben, wie Sie den Azure-Blobdienst als Repository für Buildartefakte verwenden, die mit einer Continuous Integration-Lösung von Jenkins erstellt wurden.
keywords: Jenkins, Azure, DevOps, Storage, CI/CD, Buildartefakte
ms.topic: article
ms.date: 01/12/2021
ms.custom: devx-track-jenkins, devx-track-azurecli
ms.openlocfilehash: 31c86da8f861e4295967007cb1b885325feb93dc
ms.sourcegitcommit: 75a1f26aaff48a89631805df4b4a0c006de6a271
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128157"
---
# <a name="tutorial-use-azure-storage-for-build-artifacts"></a>Tutorial: Verwenden von Azure Storage für Buildartefakte

[!INCLUDE [jenkins-integration-with-azure.md](includes/jenkins-integration-with-azure.md)]

Dieser Artikel zeigt die Verwendung von Blob-Speicher als Repository von Buildartefakten, die durch eine Jenkins Continuous Integration-Lösung (CI) erstellt wurden, oder als eine Quelle von herunterladbaren Dateien, die in einem Buildvorgang verwendet werden. Diese Lösung ist zum Beispiel dann hilfreich, wenn Sie in einer agilen Entwicklungsumgebung (in Java oder anderen Sprachen) programmieren, Builds auf Basis von Continuous Integration ausgeführt werden, und Sie ein Repository für Ihre Buildartefakte benötigen, sodass Sie sie beispielsweise mit anderen Mitgliedern der Organisation oder Ihren Kunden gemeinsam nutzen oder ein Archiv pflegen können. Ein anderes Szenario liegt vor, wenn für Ihren Buildauftrag an sich andere Dateien erforderlich sind, beispielsweise als Teil der Buildeingabe herunterzuladende Abhängigkeiten.

## <a name="prerequisites"></a>Voraussetzungen

- **Azure-Abonnement**: Falls Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), bevor Sie beginnen.
- **Jenkins-Server**: Falls Sie keinen Jenkins-Server installiert haben, [erstellen Sie einen Jenkins-Servers in Azure](./configure-on-linux-vm.md).
- **Azure CLI**: Installieren Sie die Azure CLI (Version 2.0.67 oder höher) auf dem Jenkins-Server.
- **Azure-Speicherkonto**: Falls Sie noch nicht über ein Speicherkonto verfügen, [erstellen Sie ein Speicherkonto](/azure/storage/common/storage-account-create).

## <a name="add-azure-credential-needed-to-execute-azure-cli"></a>Hinzufügen erforderlicher Azure-Anmeldeinformationen zum Ausführen der Azure CLI

1. Navigieren Sie zum Jenkins-Portal.

1. Wählen Sie im Menü die Option **Manage Jenkins** (Jenkins verwalten) aus.

1. Wählen Sie **Manage Credentials** (Anmeldeinformationen verwalten) aus.

1. Wählen Sie die **globale** Domäne aus.

1. Wählen Sie **Add Credentials** (Anmeldeinformationen hinzufügen) aus.

1. Füllen Sie die erforderlichen Felder wie folgt aus:

    - **Variante**: Wählen Sie **Username with password** (Benutzername mit Kennwort) aus.
    - **Benutzername**: Geben Sie die App-ID (`appId`) des Dienstprinzipals an.
    - **Kennwort**: Geben Sie das Kennwort (`password`) des Dienstprinzipals an.
    - **ID:** Geben Sie einen Bezeichner für die Anmeldeinformationen an (beispielsweise `azuresp`).
    - **Description** (Beschreibung): Fügen Sie optional eine aussagekräftige Beschreibung für Ihre Umgebung hinzu.

1. Wählen Sie **OK** aus, um die Anmeldeinformationen zu erstellen.

## <a name="create-a-pipeline-job-to-upload-build-artifacts"></a>Erstellen eines Pipelineauftrags zum Hochladen von Buildartefakten

In den folgenden Schritten wird gezeigt, wie Sie einen Pipelineauftrag erstellen. Durch den Pipelineauftrag werden mehrere Dateien erstellt und unter Verwendung der Azure CLI in Ihr Speicherkonto hochgeladen.

1. Wählen Sie auf dem Jenkins-Dashboard die Option **New Item** (Neues Element) aus.

1. Nennen Sie den Auftrag **myjob**, wählen Sie **Pipeline** aus, und wählen Sie anschließend **OK** aus.

1. Wählen Sie im Abschnitt **Pipeline** der Auftragskonfiguration die Option **Pipeline script** (Pipelineskript) aus, und fügen Sie unter **Script** (Skript) Folgendes ein. Legen Sie die Platzhalter auf die entsprechenden Werte für Ihre Umgebung fest.

    ```groovy
    pipeline {
      agent any
      environment {
        AZURE_SUBSCRIPTION_ID='99999999-9999-9999-9999-999999999999'
        AZURE_TENANT_ID='99999999-9999-9999-9999-999999999999'
        AZURE_STORAGE_ACCOUNT='myStorageAccount'
      }
      stages {
        stage('Build') {
          steps {
            sh 'rm -rf *'
            sh 'mkdir text'
            sh 'echo Hello Azure Storage from Jenkins > ./text/hello.txt'
            sh 'date > ./text/date.txt'
          }
    
          post {
            success {
              withCredentials([usernamePassword(credentialsId: 'azuresp', 
                              passwordVariable: 'AZURE_CLIENT_SECRET', 
                              usernameVariable: 'AZURE_CLIENT_ID')]) {
                sh '''
                  echo $container_name
                  # Login to Azure with ServicePrincipal
                  az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
                  # Set default subscription
                  az account set --subscription $AZURE_SUBSCRIPTION_ID
                  # Execute upload to Azure
                  az storage container create --account-name $AZURE_STORAGE_ACCOUNT --name $JOB_NAME --auth-mode login
                  az storage blob upload-batch --destination ${JOB_NAME} --source ./text --auth-mode login
                  # Logout from Azure
                  az logout
                '''
              }
            }
          }
        }
      }
    }
    ```
    
1. Wählen Sie **Build Now** (Jetzt erstellen) aus, um **myjob** auszuführen.

1. Prüfen Sie den Status in der Ausgabe der Konsole. Wenn im Rahmen der Postbuildaktion die Buildartefakte hochgeladen werden, werden Statusmeldungen für Azure Storage in die Konsole geschrieben.

1. Sollte ein Fehler wie der folgende auftreten, muss Zugriff auf Containerebene gewährt werden: `ValidationError: You do not have the required permissions needed to perform this operation.` Informationen zur Behandlung dieser Fehlermeldung finden Sie bei Bedarf in den folgenden Artikeln:

    - [Auswählen der Autorisierung des Zugriffs auf Blobdaten mit der Azure CLI](/azure/storage/blobs/authorize-data-operations-cli) (Azure Storage)
    - [Zuweisen einer Azure-Rolle für den Zugriff auf Blob- und Warteschlangendaten über das Azure-Portal](/azure/storage/common/storage-auth-aad-rbac-portal) (Azure Storage)

1. Untersuchen Sie nach erfolgreichem Abschluss des Auftrags die Buildartefakte, indem Sie das öffentliche Blob öffnen:

    1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
    1. Wählen Sie **Speicher**.
    1. Wählen Sie den Speicherkontonamen aus, den Sie für Jenkins verwendet haben.
    1. Wählen Sie **Container** aus.
    1. Wählen Sie in der Liste mit den Blobs den Container **myjob** aus.
    1. Daraufhin sollten die beiden folgenden Dateien angezeigt werden: **hello.txt** und **date.txt**.
    1. Kopieren Sie die URL für eines dieser Elemente, und fügen Sie sie in Ihrem Browser ein. 
    1. Sie sehen die Textdatei, die als Buildartefakt hochgeladen wurde.
    
    **Hinweise**:

    - Containernamen und Blob-Namen bestehen im Azure-Speicher aus Kleinbuchstaben (es wird zwischen Groß- und Kleinschreibung unterschieden).

## <a name="create-a-pipeline-job-to-download-from-azure-blob-storage"></a>Erstellen eines Pipelineauftrags zum Herunterladen aus Azure Blob Storage

In den folgenden Schritten wird gezeigt, wie Sie einen Pipelineauftrag zum Herunterladen von Elementen aus Azure Blob Storage konfigurieren.

1. Wählen Sie im Abschnitt **Pipeline** der Auftragskonfiguration die Option **Pipeline script** (Pipelineskript) aus, und fügen Sie unter **Script** (Skript) Folgendes ein. Legen Sie die Platzhalter auf die entsprechenden Werte für Ihre Umgebung fest.

    ```groovy
    pipeline {
      agent any
      environment {
        AZURE_SUBSCRIPTION_ID='99999999-9999-9999-9999-999999999999'
        AZURE_TENANT_ID='99999999-9999-9999-9999-999999999999'
        AZURE_STORAGE_ACCOUNT='myStorageAccount'
      }
      stages {
        stage('Build') {
          steps {
            withCredentials([usernamePassword(credentialsId: 'azuresp', 
                            passwordVariable: 'AZURE_CLIENT_SECRET', 
                            usernameVariable: 'AZURE_CLIENT_ID')]) {
              sh '''
                # Login to Azure with ServicePrincipal
                az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
                # Set default subscription
                az account set --subscription $AZURE_SUBSCRIPTION_ID
                # Execute upload to Azure
                az storage blob download --account-name $AZURE_STORAGE_ACCOUNT --container-name myjob --name hello.txt --file ${WORKSPACE}/hello.txt --auth-mode login
                # Logout from Azure
                az logout
              '''   
            }
          }
        }
      }
    }
    ```
    
1. Überprüfen Sie nach dem Ausführen eines Buildvorgangs die Konsolenausgabe zum Buildverlauf. Alternativ können Sie sich auch Ihren Downloadspeicherort ansehen, um zu ermitteln, ob die erwarteten Blobs erfolgreich heruntergeladen wurden.  

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Jenkins in Azure](/azure/Jenkins/)