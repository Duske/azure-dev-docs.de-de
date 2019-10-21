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
ms.openlocfilehash: 0730cec8470a3b55421c6c0cafa08f88819cb1d8
ms.sourcegitcommit: 6012460ad8d6ff112226b8f9ea6da397ef77712d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72279085"
---
# <a name="parameters-for-operation-configuration"></a>Parameter für die Vorgangskonfiguration

Sie können zusätzliche Parameter für Methoden für Vorgänge im Azure SDK für Python bereitstellen.

Zusätzliche Parameter werden in `kwargs` zur Verfügung gestellt. Diese Funktion wird als *operation_config* bezeichnet.

Die Optionen für die Vorgangskonfiguration sind:

|Parametername|type|Role|
|----------------------|------|---------------|
| Überprüfen |`bool`|Legt fest, ob das SSL-Zertifikat überprüft wird. Der Standardwert ist TRUE.|
|  cert |`str`| Pfad zum lokalen Zertifikat für die clientseitige Überprüfung|
|  timeout |`int`| Zeitlimit für das Herstellen einer Serververbindung in Sekunden.|
|  allow_redirects |`bool` | Legt fest, ob Umleitungen zulässig sind.|
|  max_redirects  |`int`| Maximale Anzahl zulässiger Umleitungen|
|  proxies  |`dict` |Proxyservereinstellungen.|
|  use_env_proxies |`bool` |Legt fest, ob Proxyeinstellungen aus lokalen Umgebungsvariablen gelesen werden.|
|  retries  |`int` | Gesamtanzahl der Wiederholungsversuche.|
|  enable_http_logger | `bool`| Aktivieren von Protokollen von HTTP im Debugmodus (standardmäßig „False“).|
