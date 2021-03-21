---
title: Moderálási feladatok használata a REST API-konzollal – Content Moderator
titleSuffix: Azure Cognitive Services
description: Az API-k áttekintése feladat műveleteivel teljes körű tartalom-moderálási feladatokat indíthat el a képekhez vagy szöveges tartalmakhoz az Azure Content Moderatorban.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 924c21037a464770fac13c9b45ddcf261ff5a058
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96905164"
---
# <a name="define-and-use-moderation-jobs-api-console"></a>Moderálási feladatok definiálása és használata (API-konzol)

A moderálási feladatok egyfajta burkolóként szolgálnak a tartalom moderálása, a munkafolyamatok és az értékelések működéséhez. Ez az útmutató bemutatja, hogyan kezdeményezheti és tekintheti meg a tartalom moderálási feladatait a feladat REST API-jai segítségével. Miután megértette az API-k szerkezetét, könnyedén elvégezheti ezeket a hívásokat a REST-kompatibilis platformokra.

## <a name="prerequisites"></a>Előfeltételek

- Jelentkezzen be, vagy hozzon létre egy fiókot a Content Moderator [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/) webhelyén.
- Választható [Definiáljon egy egyéni munkafolyamatot](./Review-Tool-User-Guide/Workflows.md) a feladatokhoz való használatra; használhatja az alapértelmezett munkafolyamatot is.

## <a name="create-a-job"></a>Feladat létrehozása

Moderációs feladatok létrehozásához nyissa meg az API [-hivatkozás létrehozása](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) lapot, és válassza ki az előfizetési régióhoz tartozó gombot. A régiót a végpont URL-címében találja a [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/) **hitelesítő adatok** lapján. Ezzel elindítja az API-konzolt, ahol könnyedén hozhat létre és futtathat REST API hívásokat.

![Feladatok – oldal régiójának kiválasztása](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>Adja meg a REST-hívás paramétereit

A REST-hívás létrehozásához adja meg a következő értékeket:

- **csapatnév**: a [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/) fiókjának beállításakor létrehozott csoport azonosítója (a felülvizsgálati eszköz hitelesítő adatai képernyő **azonosító** mezőjében található).
- **ContentType**: ez lehet "rendszerkép", "text" vagy "Video".
- **ContentId**: egyéni azonosító sztring. Ezt a karakterláncot a rendszer átadja az API-nak, és visszaküldi a visszahívást. Hasznos lehet belső azonosítók vagy metaadatok társítására egy moderálási feladatok eredményeivel.
- **Workflowname**: a korábban létrehozott munkafolyamat neve (vagy "default" az alapértelmezett munkafolyamathoz).
- **CallbackEndpoint**: (nem kötelező) a visszahívási információk fogadására szolgáló URL-cím a felülvizsgálat befejezésekor.
- **OCP-APIM-előfizetés-kulcs**: a Content moderator kulcsa. Ezt a kulcsot a [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com) **Beállítások** lapján találja.

### <a name="fill-in-the-request-body"></a>Töltse ki a kérelem törzsét

A REST-hívás törzse egy mezőt, **ContentValue** tartalmaz. Illessze be a szöveget a nyers szövegbe, ha moderálást végez, vagy adjon meg egy képet vagy videó URL-címét, ha egy képet vagy videót használ. A következő minta képurl-címet használhatja: [https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Feladatok – konzol lekérdezési paramétereinek, fejléceknek és kérelem törzsének létrehozása](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>A kérés elküldése

Kattintson a **Küldés** gombra. Ha a művelet sikeres, a **Válasz állapota** `200 OK` , a **válasz tartalma** mező pedig a feladatokhoz tartozó azonosítót jeleníti meg. Másolja ezt az azonosítót a következő lépésekben való használatra.

![Felülvizsgálat – a konzol válaszának létrehozása mező megjeleníti a felülvizsgálati azonosítót](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>Feladatok állapotának beolvasása

Egy futó vagy Befejezett feladatok állapotának és részleteinek beszerzéséhez nyissa meg a [feladatok beolvasása – API-](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) hivatkozás lapot, és válassza ki a régióhoz tartozó gombot (az a régió, amelyben a kulcsot felügyeli).

![Feladatokhoz tartozó régió kiválasztása](images/test-drive-region.png)

Adja meg a REST-hívás paramétereit a fenti szakaszban leírtak szerint. Ebben a lépésben a **JobId** a feladatokhoz kapott egyedi azonosító sztring. Kattintson a **Küldés** gombra. Ha a művelet sikeres, a **Válasz állapota** `200 OK` , a **válasz tartalma** pedig JSON formátumban jeleníti meg a feladatot, a következőhöz hasonlóan:

```json
{  
  "Id":"2018014caceddebfe9446fab29056fd8d31ffe",
  "TeamName":"some team name",
  "Status":"Complete",
  "WorkflowId":"OCR",
  "Type":"Image",
  "CallBackEndpoint":"",
  "ReviewId":"201801i28fc0f7cbf424447846e509af853ea54",
  "ResultMetaData":[  
    {  
      "Key":"hasText",
      "Value":"True"
    },
    {  
      "Key":"ocrText",
      "Value":"IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
    }
  ],
  "JobExecutionReport":[  
    {  
      "Ts":"2018-01-07T00:38:29.3238715",
      "Msg":"Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
    },
    {  
      "Ts":"2018-01-07T00:38:29.2928416",
      "Msg":"Job marked completed and job content has been removed"
    },
    {  
      "Ts":"2018-01-07T00:38:29.0856472",
      "Msg":"Execution Complete"
    },
    {  
      "Ts":"2018-01-07T00:38:26.7714671",
      "Msg":"Successfully got hasText response from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:26.4181346",
      "Msg":"Getting hasText from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:25.5122828",
      "Msg":"Starting Execution - Try 1"
    }
  ]
}
```

![Feladatok – REST-hívási válasz](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Vizsgálja meg az új felülvizsgálat (oka) t

Ha a tartalmi feladatokban felülvizsgálatot eredményezett, azt a [felülvizsgálati eszközben](https://contentmoderator.cognitive.microsoft.com)tekintheti meg. Válassza   >   / a **képszöveg** / **Videójának** áttekintése (a használt tartalomtól függően) lehetőséget. A tartalomnak meg kell jelennie, és készen kell állnia az emberi felülvizsgálatra. Miután egy emberi moderátor áttekinti az automatikusan hozzárendelt címkéket és az előrejelzési adatokat, és beküldi a végső moderálási döntést, a feladatok API elküldi az összes információt a kijelölt visszahívási végpontnak.

## <a name="next-steps"></a>Következő lépések

Ebben az útmutatóban megtanulta, hogyan hozhat létre és kérhet le tartalom-moderálási feladatokat a REST API használatával. Ezután integrálja a feladatokat egy végpontok közötti moderálási forgatókönyvbe, például az [E-kereskedelmi moderálási](./ecommerce-retail-catalog-moderation.md) oktatóanyagba.