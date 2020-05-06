---
title: Matrix der Ansible-Module und -Versionen für Azure | Microsoft-Dokumentation
description: Matrix der Ansible-Module und -Versionen für Azure
keywords: Ansible, Rollen, Matrix, Version, Azure, DevOps
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 54e27c7570ba1cdbce7355740181d68a2f3efbac
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "80741648"
---
# <a name="ansible-module-and-version-matrix"></a>Matrix der Ansible-Module und -Versionen

Ansible umfasst eine Suite von Modulen für die Verwendung bei der Bereitstellung und Konfiguration von Azure-Ressourcen. Diese Ressourcen umfassen virtuelle Computer, Skalierungsgruppen, Netzwerkdienste und Containerdienste. In diesem Artikel werden die verschiedenen Ansible-Module für Azure aufgeführt sowie die Ansible-Versionen, zu deren Lieferumfang sie gehören.

## <a name="ansible-modules-for-azure"></a>Ansible-Module für Azure

Die folgenden Module können direkt auf Remotehosts oder über Playbooks ausgeführt werden.  

Diese Module sind in dem offiziellen Release von Ansible oder aus den folgenden Microsoft-Playbookrollen erhältlich.

> [!NOTE]
> Ab Ansible 2.9 haben wir alle *_facts-Module in *_info umbenannt, um die Ansible-Benennungskonvention einzuhalten. Die alten und umbenannten Module sind verknüpft, weshalb alle Module, abgesehen von einer Veraltungswarnung, funktionieren wie bisher.

| Ansible-Module für Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible 2.8 | Ansible 2.9 | Ansible-Rolle | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------|--------------|  
| **Compute**                    |           |                          |                          |                            |           |           |           |
| azure_rm_availabilityset                   | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_availabilityset_info              | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_batchaccount                       | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_deployment                         | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_deployment_info                   | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_functionapp                        | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_functionapp_info                  | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_gallery                            | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_gallery_info                       | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_galleryimage                       | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_galleryimage_info                  | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_galleryimageversion                | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_galleryimageversion_info           | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_image                              | -            | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_image_info                        | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_resource                           | -            | -                           | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_resource_info                     | -            | -                           | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_resourcegroup                      | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_resourcegroup_info                | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_snapshot                           | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_virtualmachine                     | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_virtualmachine_info               | -            | -                           | -            | Ja          | Ja          | Ja          | Ja          |
| azure_rm_virtualmachineextension            | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_virtualmachineextension_info      | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_virtualmachineimage_info          | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_virtualmachinescaleset             | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_virtualmachinescaleset_info       | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_virtualmachinescalesetextension_info | -            | -                        | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_virtualmachinescalesetinstance_info | -            | -                         | -            | -            | Ja          | Ja          | Ja          |
| **Netzwerk**                              |              |                             |              |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | Ja          | Ja          | Ja          | Ja          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_applicationsecuritygroup_info     | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | Ja          | Ja          | Ja          |
| azure_rm_cdnendpoint_info                  | -            | -                         | -          | -            | Ja          | Ja          | Ja          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | Ja          | Ja          | Ja          |
| azure_rm_cdnprofile_info                   | -            | -                         | -          | -            | Ja          | Ja          | Ja          |
| azure_rm_dnsrecordset                       | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_dnsrecordset_info                 | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_dnszone                            | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_dnszone_info                      | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_firewall                           | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_firewall_info                      | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_loadbalancer                       | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_loadbalancer_info                 | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_networkinterface                   | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_networkinterface_info             | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_publicipaddress                    | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_publicipaddress_info              | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_route                              | -            | -                           | -            | Ja          | Ja          | Ja          | Ja          |
| azure_rm_routetable                         | -            | -                           | -            | Ja          | Ja          | Ja          | Ja          |
| azure_rm_routetable_info                   | -            | -                           | -            | Ja          | Ja          | Ja          | Ja          |
| azure_rm_securitygroup                      | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_securitygroup_info                 | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_subnet                             | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_subnet_info                       | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_trafficmanagerendpoint_info       | -            | -                         | -          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_trafficmanagerprofile_info        | -            | -                         | -          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_virtualnetwork                     | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_virtualnetwork_info               | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_virtualnetworkgateway              | -            | -                         | -          | -            | Ja          | Ja          | Ja          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | Ja          | Ja          | Ja          |
| azure_rm_virtualnetworkpeering_info         | -            | -                         | -          | -            | -            | Ja          | Ja          |
| **Storage**                    |           |                          |                          |                            |           |           |         |
| azure_rm_manageddisk                        | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_manageddisk_info                  | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_storageaccount                     | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_storageaccount_info               | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_storageblob                        | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| **Web**                    |           |                          |                          |                             |           |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_appserviceplan_info               | -            | -                         | -          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_webapp                             | -            | -                         | -          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_webapp_info                       | -            | -                         | -          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | Ja          | Ja          | Ja          |
| **Container**                    |           |                          |                          |                            |           |           |          |
| azure_rm_acs                                | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_aks                                | -            | -                           | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_aks_info                          | -            | -                           | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_aksversion_info                   | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_containerinstance                  | -            | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_containerinstance_info            | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_containerregistry                  | -            | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_containerregistry_info            | -            | -                           | -            | Ja          | Ja          | Ja          | Ja          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | -          | -          | Ja          |
| azure_rm_containerregistryreplication_info  | -            | -                           | -            | -            | -          | -          | Ja          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | -          | -          | Ja          |
| azure_rm_containerregistrywebhook_info      | -            | -                           | -            | -            | -          | -          | Ja          |
| **Datenbanken**                    |           |                          |                          |                             |           |           |          |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_cosmosdbaccount_info              | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_mariadbconfiguration_info         | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_mariadbdatabase_info              | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_mariadbfirewallrule_info          | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_mariadbserver_info                | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_mysqlconfiguration_info           | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_mysqldatabase                      | -            | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_mysqldatabase_info                | -            | -                           | -            | Ja          | Ja          | Ja          | Ja          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_mysqlfirewallrule_info            | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_mysqlserver                        | -            | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_mysqlserver_info                  | -            | -                           | -            | Ja          | Ja          | Ja          | Ja          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_postgresqlconfiguration_info      | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_postgresqldatabase                 | -            | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_postgresqldatabase_info           | -            | -                           | -            | Ja          | Ja          | Ja          | Ja          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_postgresqlfirewallrule_info       | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_postgresqlserver                   | -            | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_postgresqlserver_info             | -            | -                           | -            | Ja          | Ja          | Ja          | Ja          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_rediscache_info                   | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_sqldatabase                        | -            | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_sqldatabase_info                  | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_sqlelasticpool                    | -            | -                           | -            | -            | -          | -          | Ja          |
| azure_rm_sqlelasticpool_info               | -            | -                           | -            | -            | -          | -          | Ja          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Ja          | Ja          | Ja          | Ja          |
| azure_rm_sqlfirewallrule_info              | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_sqlserver                          | -            | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_sqlserver_info                    | -            | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| **Analyse**                    |           |                          |                          |                             |           |           |          |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_hdinsightcluster_info              | -            | -                           | -            | -            | -            | Ja          | Ja          |
| **Integration**                    |           |                          |                          |                             |           |           |          |
| azure_rm_servicebus                         | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_servicebus_info                   | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| **Sicherheit**                    |           |                          |                          |                             |           |           |           |
| azure_rm_keyvault                           | -            | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_keyvault_info                     | -            | -                           | -              | -          | Ja          | Ja          | Ja          |
| azure_rm_keyvaultkey                        | -            | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_keyvaultkey_info                   | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_keyvaultsecret                     | -            | Ja                         | Ja          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_roleassignment_info               | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_roledefinition_info               | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| **DevOps**               |           |                          |                          |                             |           |           |                  |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_devtestlab_info                   | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_devtestlabarmtemplate_info        | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_devtestlabartifact_info           | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_devtestlabartifactsource_info     | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_devtestlabcustomimage_info         | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_devtestlabenvironment_info         | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_devtestlabpolicy_info              | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_devtestlabschedule_info            | -            | -                           | -            | -            | -            | Ja          | Ja          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_devtestlabvirtualmachine_info | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_devtestlabvirtualnetwork_info     | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| **Azure Monitor**                           |              |                           |            |              |                 |           |              |
| azure_rm_autoscale                          | -            | -                         | -          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_autoscale_info                    | -            | -                         | -          | Ja          | Ja          | Ja          | Ja          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_loganalyticsworkspace_info        | -            | -                           | -            | -            | Ja          | Ja          | Ja          |
| azure_rm_monitorlogprofile                  | -            | -                           | -            | -            | -            | Ja          | Ja          |
| **Verwaltung und Governance**     |              |                           |            |            |            |            |              |
| azure_rm_automationaccount        | -            | -                         | -          | -          | -          | Ja        | Ja          |
| azure_rm_automationaccount_info   | -            | -                         | -          | -          | -          | Ja        | Ja          |
| azure_rm_lock                     | -            | -                         | -          | -          | -          | Ja        | Ja          |
| azure_rm_lock_info                | -            | -                         | -          | -          | -          | Ja        | Ja          |
| **Internet der Dinge (IoT)**            |              |                           |            |            |            |            |              |
| azure_rm_iotdevice                | -            | -                         | -          | -          | -          | Ja        | Ja          |
| azure_rm_iotdevice_info           | -            | -                         | -          | -          | -          | Ja        | Ja          |
| azure_rm_iotdevicemodule          | -            | -                         | -          | -          | -          | Ja        | Ja          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | Ja        | Ja          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | Ja        | Ja          |
| azure_rm_iothubconsumergroup      | -            | -                         | -          | -          | -          | Ja        | Ja          |

## <a name="introduction-to-playbook-role-for-azure"></a>Einführung in die Playbookrolle für Azure

Die [Playbookrolle „azure_preview_module“](https://galaxy.ansible.com/Azure/azure_preview_modules/) enthält alle neuesten Azure-Module. Die Updates und Fehlerbehebungen erfolgen schneller als für das offizielle Ansible-Release. Wenn Sie Ansible zum Bereitstellen von Azure-Ressourcen verwenden, wird empfohlen, die Playbookrolle `azure_preview_module` zu installieren.

Die Playbookrolle `azure_preview_module` wird alle drei Wochen veröffentlicht.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Playbookrollen finden Sie unter [Erstellen wiederverwendbarer Playbooks](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 