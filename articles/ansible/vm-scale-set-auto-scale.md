---
title: 'Tutorial: Automatisches Skalieren von VM-Skalierungsgruppen in Azure mithilfe von Ansible'
description: Hier erfahren Sie, wie Sie Ansible verwenden, um VM-Skalierungsgruppen in Azure mit Autoskalierung zu skalieren.
keywords: ansible, azure, devops, bash, playbook, skalieren, autoskalierung, virtueller computer, vm-skalierungsgruppen, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: f50ac83b03d957e0d6649a5803ee90982a532935
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "81743878"
---
# <a name="tutorial-autoscale-virtual-machine-scale-sets-in-azure-using-ansible"></a>Tutorial: Automatisches Skalieren von VM-Skalierungsgruppen in Azure mit Ansible

[!INCLUDE [ansible-27-note.md](includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vm-scale-set.md](../includes/open-source-devops-intro-vm-scale-set.md)]

Das Feature zur automatischen Anpassung der Anzahl von VM-Instanzen heißt [Autoskalierung](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview). Der Vorteil der Autoskalierung besteht in der Verringerung des Verwaltungsaufwands im Zusammenhang mit der Leistungsüberwachung und -optimierung für Ihre Anwendung. Die Autoskalierung kann bedarfsbasiert oder nach einem definierten Zeitplan konfiguriert werden. Mit Ansible können Sie Autoskalierungsregeln angeben, um eine angemessene Leistung für eine positive Kundenerfahrung zu definieren.

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Definieren eines Profils für die automatische Skalierung
> * Automatisches Skalieren auf der Grundlage einer Zeitplanserie
> * Automatisches Skalieren auf der Grundlage der App-Leistung
> * Abrufen von Informationen zu Autoskalierungseinstellungen 
> * Deaktivieren einer Autoskalierungseinstellung

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](includes/ansible-prereqs-vm-scale-set.md)]

## <a name="autoscale-based-on-a-schedule"></a>Automatisches Skalieren nach Zeitplan

Zum Aktivieren der automatischen Skalierung für eine Skalierungsgruppe legen Sie zuerst ein Profil für die automatische Skalierung fest. Dieses Profil definiert die standardmäßige, minimale und maximale Kapazität der Skalierungsgruppe. Diese Grenzwerte sind hilfreich bei der Kostenkontrolle und sorgen nicht nur dafür, dass nicht fortlaufend VM-Instanzen erstellt werden, sondern gewährleisten auch ein ausgewogenes Verhältnis zwischen angemessener Leistung und einer minimalen Anzahl von Instanzen, die beim horizontalen Herunterskalieren erhalten bleiben. 

Mit Ansible können Sie Ihre Skalierungsgruppen auf der Grundlage eines bestimmten Datums oder einer Zeitplanserie skalieren.

Der Playbookcode in diesem Abschnitt erhöht die Anzahl von VM-Instanzen jeden Montag um 10:00 Uhr auf drei.

Speichern Sie das folgende Playbook als `vmss-auto-scale.yml`:

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks: 
    - name: Create autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         target:
           namespace: "Microsoft.Compute"
           types: "virtualMachineScaleSets"
           name: "{{ vmss_name }}"
         enabled: true
         profiles:
         - count: '3'
           min_count: '3'
           max_count: '3'
           name: Auto created scale condition
           recurrence_timezone: Pacific Standard Time
           recurrence_frequency: Week
           recurrence_days:
              - Monday
           recurrence_mins:
              - '0'
           recurrence_hours:
              - '10'
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="autoscale-based-on-performance-data"></a>Automatisches Skalieren auf der Grundlage von Leistungsdaten

Wenn sich die Nachfrage für Ihre Anwendung erhöht, erhöht sich auch die Last für die VM-Instanzen in Ihren Skalierungsgruppen. Falls es sich um eine dauerhafte Last und nicht nur um eine kurzzeitige höhere Nachfrage handelt, können Sie die Regeln für die automatische Skalierung konfigurieren, um die Anzahl von VM-Instanzen in der Skalierungsgruppe zu erhöhen. Nachdem diese VM-Instanzen erstellt und Ihre Anwendungen bereitgestellt wurden, beginnt die Skalierungsgruppe damit, über das Lastenausgleichsmodul Datenverkehr darauf zu verteilen. Mit Ansible können Sie steuern, welche Metriken überwacht werden sollen (beispielsweise CPU-Auslastung, Datenträgerauslastung und App-Ladezeit). In Skalierungsgruppen kann basierend auf Leistungsmetrik-Schwellenwerten, auf der Grundlage einer Zeitplanserie oder an einem bestimmten Datum ab- und aufskaliert werden. 

Der Playbookcode in diesem Abschnitt überprüft jeden Montag um 18:00 Uhr die CPU-Auslastung der letzten zehn Minuten. 

Auf der Grundlage der prozentualen CPU-Metriken führt das Playbook eine der folgenden Aktionen aus:

- Erhöhen der Anzahl von VM-Instanzen auf vier
- Verringern der Anzahl von VM-Instanzen auf eine

Speichern Sie das folgende Playbook als `vmss-auto-scale-metrics.yml`:

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get scale set resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create autoscaling
    azure_rm_autoscale:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      target: "{{ vmss_id }}"
      enabled: true
      profiles:
      - count: '1'
        max_count: '1'
        min_count: '1'
        name: 'This scale condition is executed when none of the other scale condition(s) match'
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
      - count: '1'
        min_count: '1'
        max_count: '4'
        name: Auto created scale condition
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
        rules:
          - cooldown: 5
            direction: Increase
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: GreaterThan
            statistic: Average
            threshold: 70
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
          - cooldown: 5
            direction: Decrease
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: LessThan
            statistic: Average
            threshold: 30
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-autoscale-settings-information"></a>Abrufen von Informationen zu Autoskalierungseinstellungen 

Der Playbookcode in diesem Abschnitt verwendet das Modul `azure_rm_autoscale_facts`, um Details zur Autoskalierungseinstellung abzurufen.

Speichern Sie das folgende Playbook als `vmss-auto-scale-get-settings.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve autoscale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook vmss-auto-scale-get-settings.yml
```

## <a name="disable-autoscale-settings"></a>Deaktivieren von Autoskalierungseinstellungen

Autoskalierungseinstellungen können auf zwei Arten deaktiviert werden. Eine Möglichkeit besteht darin, den Schlüssel `enabled` von `true` in `false` zu ändern. Die zweite Möglichkeit ist das Löschen der Einstellung.

Der Playbookcode in diesem Abschnitt löscht die Autoskalierungseinstellung. 

Speichern Sie das folgende Playbook als `vmss-auto-scale-delete-setting.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
vmss-auto-scale-delete-setting.yml
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Tutorial: Aktualisieren des benutzerdefinierten Images von Skalierungsgruppen für virtuelle Azure-Computer mit Ansible](./vm-scale-set-update-image.md)