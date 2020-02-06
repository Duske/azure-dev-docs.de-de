---
ms.openlocfilehash: 5d005f5a4906e5e2a55189d073383ab77f6c1860
ms.sourcegitcommit: 6fa28ea675ae17ffb9ac825415e2e26a3dfe7107
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2020
ms.locfileid: "77013775"
---
| **Erstellen von virtuellen Computern** || 
|---|---|
| [Verwalten virtueller Computer][1] | Erstellen, ändern, starten, beenden und löschen Sie virtuelle Computer. |
| [Erstellen eines virtuellen Computers aus einem benutzerdefinierten Image][2] | Erstellen Sie ein benutzerdefiniertes VM-Image, und verwenden Sie es für die Erstellung neuer virtueller Computer. | 
| [Erstellen eines virtuellen Computers mit einer speziellen VHD aus einer Momentaufnahme][3] | Erstellen Sie eine Momentaufnahme des Betriebssystems und der Datenträger des virtuellen Computers, erstellen Sie verwaltete Datenträger mithilfe der Momentaufnahmen, und erstellen Sie anschließend einen virtuellen Computer durch Anfügen der verwalteten Datenträger. |  
| [Paralleles Erstellen von virtuellen Computern im gleichen Netzwerk][4] | Erstellen Sie virtuelle Computer parallel in der gleichen Region und im gleichen virtuellen Netzwerk mit zwei Subnetzen. |
| [Paralleles Erstellen von virtuellen Computern in mehreren Regionen][5] | Erstellen Sie eine Gruppe von virtuellen Computern mit Lastenausgleich in mehreren Azure-Regionen. |
| **Netzwerk-VMs** || 
| [Verwalten virtueller Netzwerke][6] | Richten Sie ein virtuelles Netzwerk mit zwei Subnetzen ein, und schränken Sie den Internetzugriff für sie ein. |
| **Erstellen von Skalierungsgruppen** ||
| [Erstellen einer VM-Skalierungsgruppe mit einem Lastenausgleich][7] | Erstellen Sie eine VM-Skalierungsgruppe, richten Sie einen Lastenausgleich ein, und rufen Sie SSH Verbindungszeichenfolgen für die virtuellen Computer der Skalierungsgruppe ab. |

[1]: ../java-sdk-manage-virtual-machines.md
[2]: https://github.com/Azure-Samples/managed-disk-java-create-virtual-machine-using-custom-image/
[3]: https://github.com/Azure-Samples/managed-disk-java-create-virtual-machine-using-specialized-disk-from-vhd/
[4]: https://github.com/Azure-Samples/compute-java-manage-virtual-machines-in-parallel/
[5]: ../java-sdk-virtual-machines-in-parallel.md
[6]: ../java-sdk-manage-virtual-networks.md
[7]: ../java-sdk-manage-vm-scalesets.md