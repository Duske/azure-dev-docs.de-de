---
title: 'Parameter für die Vorgangskonfiguration: Azure SDK für Python'
description: C vom Azure SDK für Python ausgelöst
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 03/07/2018
ms.topic: conceptual
ms.devlang: python
ms.custom: seo-python-october2019
ms.openlocfilehash: ca69b72789f28445c4654e635e641e2954890a38
ms.sourcegitcommit: bed07b313eeab51281d1a6d4eba67a75524b2f57
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72172373"
---
# <a name="parameters-for-operation-configuration"></a>Parameter für die Vorgangskonfiguration

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
