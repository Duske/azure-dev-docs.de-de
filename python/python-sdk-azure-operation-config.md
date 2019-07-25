---
title: Azure SDK-Konfiguration für Python-Vorgänge
description: C vom Azure SDK für Python ausgelöst
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 03/07/2018
ms.topic: conceptual
ms.devlang: python
ms.openlocfilehash: 9638aa4602f96e2da0155a7b3840e5be4857eb98
ms.sourcegitcommit: 2efdb9d8a8f8a2c1914bd545a8c22ae6fe0f463b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2019
ms.locfileid: "68285511"
---
# <a name="operation-config"></a>Vorgangskonfiguration 

Methoden für Vorgänge weisen zusätzliche Parameter auf, die in `kwargs` bereitgestellt werden können. Dies wird als „operation_config“ bezeichnet.

Die Optionen für die Vorgangskonfiguration sind:

|Parametername|type|Role|
|----------------------|------|---------------|
| Überprüfen |`bool`|Legt fest, ob das SSL-Zertifikat überprüft wird. Der Standardwert ist TRUE.|
|  cert |`str`| Pfad zum lokalen Zertifikat für die clientseitige Überprüfung.|
|  timeout |`int`| Zeitlimit für das Herstellen einer Serververbindung in Sekunden.|
|  allow_redirects |`bool` | Legt fest, ob Umleitungen zulässig sind.|
|  max_redirects  |`int`| Maximale Anzahl zulässiger Umleitungen.|
|  proxies  |`dict` |Proxyservereinstellungen.|
|  use_env_proxies |`bool` |Legt fest, ob Proxyeinstellungen aus lokalen Umgebungsvariablen gelesen werden.|
|  retries  |`int` | Gesamtanzahl der Wiederholungsversuche.|
|  enable_http_logger | `bool`| Aktivieren von Protokollen von HTTP im Debugmodus (standardmäßig „False“).|
