---
title: Verwenden von Chef mit Azure
description: Einführung in die Verwendung von Chef zum Konfigurieren und Testen Ihrer Azure-Infrastruktur
keywords: Azure, Chef, Devops, virtuelle Computer, Übersicht, Automatisieren
ms.date: 02/22/2020
ms.topic: article
ms.custom: devx-track-chef
ms.openlocfilehash: 2c24bba7d3da9976d5bd21a7a10bbb0de0887667
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831436"
---
# <a name="using-chef-with-azure"></a>Verwenden von Chef mit Azure

[Chef](https://www.chef.io) ist eine leistungsstarke Automatisierungsplattform, die die Infrastruktur des virtuellen Computers in Azure in Code transformiert. Chef automatisiert die Art, in der Infrastruktur in Ihrem Netzwerk – unabhängig von seiner Größe – konfiguriert, bereitgestellt und verwaltet wird.

In diesem Artikel werden die Vorteile bei der Verwendung von Chef zum Verwalten der Azure-Infrastruktur beschrieben.

## <a name="chef-extension-on-azure"></a>Chef-Erweiterung für Azure

Stellen Sie einen virtuellen Computer mit dem Chef-Client bereit, der als Hintergrunddienst mit der [Chef-Erweiterung](/azure/chef/chef-extension-portal) im [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) ausgeführt wird. Nach der Bereitstellung können diese virtuellen Computer von einem Chef-Server verwaltet werden.

## <a name="chef-cloud-shell"></a>Chef Cloud Shell

Verwenden Sie Chef Workstation direkt in Azure Cloud Shell. Führen Sie all Ihre Chef-Hilfsprogramme und InSpec direkt über Cloud Shell aus. Sie können die Chef-Befehle aus folgenden Hilfsprogrammen verwenden:

* [chef](https://docs.chef.io/ctl_chef.html)
* [kitchen](https://docs.chef.io/ctl_kitchen.html)
* [inspec](https://www.inspec.io/docs/reference/cli/)
* [knife](https://docs.chef.io/knife.html)
* [cookstyle](https://docs.chef.io/cookstyle.html)
* [chef-run](https://www.chef.sh/docs/chef-workstation/getting-started/)

Kombinieren Sie unsere Befehlshilfsprogramme mit den anderen Tools, die in Cloud Shell zur Verfügung stehen, z.B. `git`, `az-cli` und `terraform`, und schreiben Sie Ihre Infrastruktur- und Konformitätsautomatisierung über den Browser.

## <a name="automate-everything-with-one-platform"></a>Automatisieren sämtlicher Aspekte mit einer einzelnen Plattform

Unternehmen sind auf Geschwindigkeit und Sicherheit angewiesen, um auf dem digitalen Marktplatz konkurrieren zu können. Gemeinsam helfen Chef und Microsoft Einzelpersonen, Teams und Unternehmen, alle diese Anforderungen zu bewältigen. Mit einer Plattform – Chef Automate – können Sie Ihre Infrastruktur, Anwendungen und Konformität jetzt in Ihrer gesamten Microsoft-Umgebung automatisieren und kontinuierlich bereitstellen.

## <a name="test-drive-chef-automate-on-azure"></a>Testen von Chef Automate in Azure

Unterstützt von Chef ermöglicht die [Chef Automate Azure Marketplace-Lösung](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate) Ihnen, Ihre Infrastruktur und Anwendungen gemeinsam aufzubauen, bereitzustellen und zu verwalten. Mit einem Klick erhalten Sie sofortigen Zugriff auf alle in Chef Automate enthaltenen kommerziellen Features, erlangen vollständige Sichtbarkeit Ihrer gesamten Flotte, ermöglichen dauerhafte Konformität und können alle Änderungen mit einem einheitlichen Workflow verwalten.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines virtuellen Windows-Computers in Azure mithilfe von Chef](windows-vm-configure.md)