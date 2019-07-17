---
title: Első szándékkal,C#
titleSuffix: Language Understanding - Azure Cognitive Services
description: A jelen C# a rövid útmutatóban elérhető nyilvános LUIS alkalmazás segítségével meghatározhatja, a felhasználó szándékának természetes nyelvi szövegből.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 73dba63c464ee31dd24fe0fdf97e948a5b6004fc
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276274"
---
# <a name="quickstart-get-intent-using-c"></a>Gyors útmutató: Leképezés használata elsőC#

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Előfeltételek

* [A Visual Studio Community 2017-es kiadása](https://visualstudio.microsoft.com/vs/community/)
* C# programozási nyelv (a VS Community 2017 része)
* A df67dcdb-c37d-46af-88e1-8b97951ca1c2 azonosítójú nyilvános alkalmazás


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS-kulcs lekérése

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Szándék lekérése böngészővel

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Szándék lekérése programozott módon

Ha C# nyelven lekérdezi a GET [API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) előrejelzési végpontot, ugyanazokat az eredményeket kapja, mint amelyeket az előző szakaszban látott a böngészőablakban. 

1. Hozzon létre egy új konzolalkalmazást a Visual Studióban. 

    ![Hozzon létre egy új konzolalkalmazást a Visual Studióban](media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. A Visual Studio projekt Megoldáskezelő ablakában válassza az **Add reference** (Referencia hozzáadása) elemet, majd válassza a **System.Web** lehetőséget az Assemblies (Szerelvények) lapon.

    ![Válassza a Hozzáadás hivatkozás, majd jelölje ki a System.Web a szerelvények lapról](media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

3. Írja felül a Program.cs fájlt a következő kóddal:
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/csharp/Program.cs)]

4. Cserélje le a `YOUR_KEY` értéket a LUIS-kulcsra.

5. Buildelje és futtassa a konzolalkalmazást. Megjelenik a korábban a böngészőablakban látott JSON.

    ![A LUIS által visszaadott JSON-eredményt megjelenítő konzolablak](./media/luis-get-started-cs-get-intent/console-turn-on.png)

## <a name="luis-keys"></a>LUIS-kulcsok

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor végzett ezzel a rövid útmutatóval, zárja be a Visual Studio-projektet, és távolítsa el a projekt könyvtárát a fájlrendszerből. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kimondott szövegek hozzáadása és betanítás a C# használatával](luis-get-started-cs-add-utterance.md)
