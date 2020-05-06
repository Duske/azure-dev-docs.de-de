---
title: Verwenden von Ansible mit Azure
description: Enthält eine Einführung in die Verwendung von Ansible zum Automatisieren von Cloudbereitstellung, Konfigurationsverwaltung und Anwendungsbereitstellungen.
keywords: Ansible, Azure, DevOps, Übersicht, Cloudbereitstellung, Konfigurationsverwaltung, Anwendungsbereitstellung, Ansible-Module, Ansible-Playbooks
ms.topic: overview
ms.date: 04/30/2019
ms.openlocfilehash: e37807ffd4d7479bccec0dc058760be2e1f1e6d5
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "80741518"
---
# <a name="using-ansible-with-azure"></a>Verwenden von Ansible mit Azure

[Ansible](https://www.ansible.com) ist ein Open-Source-Produkt, mit dem die Cloudbereitstellung, Konfigurationsverwaltung und Anwendungsbereitstellungen automatisiert werden können. Mit Ansible können Sie virtuelle Computer, Container und Netzwerkumgebungen sowie auch vollständige Cloudinfrastrukturen bereitstellen. Ansible ermöglicht außerdem die Automatisierung der Bereitstellung und Konfiguration von Ressourcen in Ihrer Umgebung.

Dieser Artikel enthält eine allgemeine Übersicht über die Vorteile der Verwendung von Ansible mit Azure.

## <a name="ansible-playbooks"></a>Ansible-Playbooks

Mit [Ansible-Playbooks](https://docs.ansible.com/ansible/latest/playbooks.html) können Sie Ansible anweisen, Ihre Umgebung zu konfigurieren. Playbooks werden mit YAML codiert, damit sie für Menschen lesbar sind. Der Abschnitt „Tutorials“ enthält viele Beispiele für die Verwendung von Playbooks zum Installieren und Konfigurieren von Azure-Ressourcen. 

## <a name="ansible-modules"></a>Ansible-Module

Ansible umfasst eine Sammlung von [Ansible-Modulen](https://docs.ansible.com/ansible/latest/modules_by_category.html), die direkt auf Remotehosts oder über [Playbooks](https://docs.ansible.com/ansible/latest/playbooks.html) ausgeführt werden. Benutzer können ihre eigenen Module erstellen. Module werden verwendet, um Systemressourcen zu steuern, z. B. Dienste, Pakete oder Dateien, oder um Systembefehle auszuführen.

Für die Interaktion mit Azure-Diensten enthält Ansible eine Sammlung von [Ansible-Cloudmodulen](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure). Mit diesen Modulen können Sie Ihre Infrastruktur in Azure erstellen und orchestrieren. 

## <a name="migrate-existing-workload-to-azure"></a>Migrieren einer vorhandenen Workload zu Azure

Wenn Sie Ansible zum Definieren Ihrer Infrastruktur verwenden, können Sie das Playbook Ihrer Anwendung anwenden, damit Ihre Umgebung je nach Bedarf automatisch von Azure skaliert werden kann. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatisieren einer nativen Cloudanwendung in Azure

Mit Ansible können Sie native Cloudanwendungen in Azure automatisieren, indem Sie Azure-Microservices wie [Azure Functions](https://azure.microsoft.com//services/functions/) und [Kubernetes in Azure](https://azure.microsoft.com/services/container-service/kubernetes/) verwenden.  

## <a name="manage-deployments-with-dynamic-inventory"></a>Verwalten von Bereitstellungen mit dynamischem Bestand

Mithilfe des Features [Dynamic Inventory](https://docs.ansible.com/ansible/intro_dynamic_inventory.html) (Dynamischer Bestand) können mit Ansible Bestandsdaten per Pullvorgang von Azure-Ressourcen abgerufen werden. Sie können Ihre vorhandenen Azure-Bereitstellungen dann mit Tags versehen und diese gekennzeichneten Bereitstellungen mit Ansible verwalten.

## <a name="additional-azure-marketplace-options"></a>Zusätzliche Azure Marketplace-Optionen

[Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) ist ein Azure Marketplace-Image von Red Hat. 

Bei Ansible Tower handelt es sich um eine webbasierte Benutzeroberfläche und ein Dashboard für Ansible mit den folgenden Features:

* Ermöglicht das Definieren der rollenbasierten Zugriffssteuerung, Auftragsplanung und grafischen Bestandsverwaltung. 
* Umfasst eine REST-API und CLI, damit Sie Tower in vorhandene Tools und Prozesse einfügen können. 
* Unterstützt die Echtzeitausgabe von Playbookausführungen. 
* Verschlüsselt Anmeldeinformationen, z. B. Azure- und SSH-Schlüssel, damit Sie Aufgaben delegieren können, ohne Anmeldeinformationen verfügbar zu machen.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Matrix der Ansible-Module und -Versionen für Azure

Ansible umfasst eine Suite von Modulen für die Verwendung bei der Bereitstellung und Konfiguration von Azure-Ressourcen. Diese Ressourcen umfassen virtuelle Computer, Skalierungsgruppen, Netzwerkdienste und Containerdienste. In der [Ansible-Matrix](./module-version-matrix.md) sind die Ansible-Module für Azure und die Ansible-Versionen aufgeführt, zu deren Lieferumfang sie gehören.

## <a name="next-steps"></a>Nächste Schritte

- [Schnellstart: Bereitstellen der Ansible-Lösungsvorlage für Azure in CentOS](./solution-template-deploy.md)
- [Schnellstart: Erstellen eines virtuellen Linux-Computers in Azure mithilfe von Ansible](./install-on-linux-vm.md)