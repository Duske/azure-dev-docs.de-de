---
title: React-Code mit maschinellem Sehen
description: Dieses Beispiel enthält den gesamten Code, der benötigt wird, um der React-App maschinelles Sehen hinzufügen. In diesem Abschnitt des Tutorials werden die Schritte und der Code lediglich _erläutert_.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: a246e2e367aef4027516468ae691aa31117cd6c7
ms.sourcegitcommit: 4dac39849ba2e48034ecc91ef578d11aab796e58
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94993468"
---
# <a name="5-review-how-to-add-computer-vision-to-the-react-app"></a>5. Vertrautmachen mit dem Hinzufügen von maschinellem Sehen zur React-App

Dieses Beispiel enthält den gesamten Code, der benötigt wird, um der React-App maschinelles Sehen hinzufügen. In diesem Abschnitt des Tutorials werden die Schritte und der Code lediglich _erläutert_. Die hier beschriebenen Schritte müssen für das Tutorial nicht ausgeführt werden. 

## <a name="add-computer-vision-to-local-react-app"></a>Hinzufügen von maschinellem Sehen zur lokalen React-App

Verwenden Sie npm, um der Datei „package.json“ maschinelles Sehen hinzuzufügen. 

```bash
npm install @azure/cognitiveservices-computervision 
```

## <a name="add-computer-vision-code-as-separate-module"></a>Hinzufügen des Codes für maschinelles Sehen als separates Modul

Der Code für maschinelles Sehen ist in einer separaten Datei namens `./src/VisualAI.js` enthalten. Die Hauptfunktion des Moduls ist hervorgehoben: 

:::code language="javascript" source="~/../js-e2e-client-cognitive-services/src/VisualAI.js" highlight="55-75" :::

## <a name="add-catalog-of-images-as-separate-module"></a>Hinzufügen eines Katalogs mit Bildern als separates Modul

Wenn der Benutzer keine Bild-URL eingibt, wird von der App nach dem Zufallsprinzip ein Bild aus einem Katalog ausgewählt. Die Funktion für die Zufallsauswahl ist hervorgehoben: 

:::code language="javascript" source="~/../js-e2e-client-cognitive-services/src/DefaultImages.js" highlight="33-35" :::

## <a name="add-custom-computer-vision-module-to-react-app"></a>Hinzufügen eines benutzerdefinierten Moduls für maschinelles Sehen zur React-App

Fügen Sie der Datei `app.js` von React Methoden hinzu. Bildanalyse Ergebnisanzeige sind hervorgehoben:

:::code language="javascript" source="~/../js-e2e-client-cognitive-services/src/App.js" highlight="20-25, 29-42" :::

## <a name="next-step"></a>Nächster Schritt

> [!div class="nextstepaction"]
> [Bereinigen von Ressourcen](clean-up-resources.md) 