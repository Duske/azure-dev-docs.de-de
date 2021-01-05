---
title: React-Code mit maschinellem Sehen
description: In diesem Abschnitt des Tutorials werden die Schritte und der Code lediglich _erläutert_. Die hier beschriebenen Schritte müssen für das Tutorial nicht ausgeführt werden.
ms.topic: tutorial
ms.date: 12/17/2020
ms.custom: devx-track-js
ms.openlocfilehash: 84140472c4bb57e208cc0e2c0665e72680664a2f
ms.sourcegitcommit: 1c508f5ba73a12e4baeacc88ad9a8359301acb50
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97687482"
---
# <a name="6-review-how-to-add-computer-vision-to-the-react-app"></a>6. Vertrautmachen mit dem Hinzufügen von maschinellem Sehen zur React-App

Dieses Beispiel enthält den gesamten TypeScript-Code, der zum Hinzufügen des maschinellen Sehens zur React-App erforderlich ist. In diesem Abschnitt des Tutorials werden die Schritte und der Code lediglich _erläutert_. Die hier beschriebenen Schritte müssen für das Tutorial nicht ausgeführt werden. 

* [Beispielcode](https://github.com/Azure-Samples/js-e2e-client-cognitive-services)
* Azure-Dienste
    * [Statische Web-App](https://docs.microsoft.com/azure/static-web-apps)
    * [Maschinelles Sehen von Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)

## <a name="add-computer-vision-to-local-react-app"></a>Hinzufügen von maschinellem Sehen zur lokalen React-App

Verwenden Sie npm, um der Datei „package.json“ maschinelles Sehen hinzuzufügen. 

```bash
npm install @azure/cognitiveservices-computervision 
```

## <a name="add-computer-vision-code-as-separate-module"></a>Hinzufügen des Codes für maschinelles Sehen als separates Modul

Der Code für maschinelles Sehen ist in einer separaten Datei namens `./src/azure-cognitiveservices-computervision.js` enthalten. Die Hauptfunktion des Moduls ist hervorgehoben: 

:::code language="javascript" source="~/../js-e2e-client-cognitive-services/src/azure-cognitiveservices-computervision.js" highlight="55-75" :::

## <a name="add-catalog-of-images-as-separate-module"></a>Hinzufügen eines Katalogs mit Bildern als separates Modul

Wenn der Benutzer keine Bild-URL eingibt, wird von der App nach dem Zufallsprinzip ein Bild aus einem Katalog ausgewählt. Die Funktion für die Zufallsauswahl ist hervorgehoben: 

:::code language="javascript" source="~/../js-e2e-client-cognitive-services/src/DefaultImages.js" highlight="33-35" :::

## <a name="add-custom-computer-vision-module-to-react-app"></a>Hinzufügen eines benutzerdefinierten Moduls für maschinelles Sehen zur React-App

Fügen Sie der Datei `app.js` von React Methoden hinzu. Bildanalyse Ergebnisanzeige sind hervorgehoben:

:::code language="javascript" source="~/../js-e2e-client-cognitive-services/src/App.js" highlight="20-25, 29-42" :::

## <a name="next-step"></a>Nächster Schritt

> [!div class="nextstepaction"]
> [Bereinigen von Ressourcen](clean-up-resources.md) 