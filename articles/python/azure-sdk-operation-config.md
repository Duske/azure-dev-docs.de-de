---
title: 'Parameter für die Vorgangskonfiguration: Azure SDK für Python'
description: C vom Azure SDK für Python ausgelöst
ms.date: 03/07/2018
ms.topic: conceptual
ms.custom: seo-python-october2019
ms.openlocfilehash: b0aaaf5bcd51bce42ab38830d5dbce508db226b1
ms.sourcegitcommit: 1bd9ec6a4115e9162e33b76a933869788e6ab702
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80441695"
---
# <a name="parameters-for-operation-configuration"></a>Parameter für die Vorgangskonfiguration

Sie können zusätzliche Parameter für Methoden für Vorgänge im Azure SDK für Python bereitstellen.

Zusätzliche Parameter werden in `kwargs` zur Verfügung gestellt. Diese Funktion wird als *operation_config* bezeichnet.

Die Optionen für die Vorgangskonfiguration sind:

|Parametername|type|Role|
|----------------------|------|---------------|
| Überprüfen |`bool`|Legt fest, ob das SSL-Zertifikat überprüft wird. Der Standardwert lautet "True".|
|  cert |`str`| Pfad zum lokalen Zertifikat für die clientseitige Überprüfung|
|  timeout |`int`| Zeitlimit für das Herstellen einer Serververbindung in Sekunden.|
|  allow_redirects |`bool` | Legt fest, ob Umleitungen zulässig sind.|
|  max_redirects  |`int`| Maximale Anzahl zulässiger Umleitungen|
|  proxies  |`dict` |Proxyservereinstellungen.|
|  use_env_proxies |`bool` |Legt fest, ob Proxyeinstellungen aus lokalen Umgebungsvariablen gelesen werden.|
|  retries  |`int` | Gesamtanzahl der Wiederholungsversuche.|
|  enable_http_logger | `bool`| Aktivieren von Protokollen von HTTP im Debugmodus (standardmäßig „False“).|
