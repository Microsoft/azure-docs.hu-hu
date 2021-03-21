---
title: Cognitive Services csatolása egy készségkészlet
titleSuffix: Azure Cognitive Search
description: Megtudhatja, hogyan csatolhat Cognitive Services all-in-One előfizetést egy AI-dúsítási folyamathoz az Azure-ban Cognitive Search.
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 77735166fafe9d39dff483baa89a4b31db31275d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100577933"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Cognitive Services-erőforrás csatlakoztatása egy készségkészlet az Azure-ban Cognitive Search

Ha a [mesterséges intelligencia](cognitive-search-concept-intro.md) -bővítési folyamatot az Azure Cognitive Searchban konfigurálja, a korlátozott számú dokumentumot díjmentesen bővítheti. Nagyobb és gyakoribb számítási feladatokhoz csatoljon egy számlázandó "all-in-One" Cognitive Services-erőforrást. Az "all-in-One" előfizetések "Cognitive Services"-ként szerepelnek az egyedi szolgáltatások helyett, és egyetlen API-kulcson keresztül kapnak hozzáférést.

Az "all-in-One" Cognitive Services erőforrás a készségkészlet megadható [előre definiált képességeket](cognitive-search-predefined-skills.md) hajtja végre:

+ [Computer Vision](https://azure.microsoft.com/services/cognitive-services/computer-vision/) a képek elemzéséhez és az optikai karakterfelismeréshez (OCR)
+ [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) a nyelvfelismerés, az entitások felismerése, a hangulat elemzése és a kulcsfontosságú kifejezés kinyerése
+ [Szövegfordítás](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)

Egy készségkészlet-definícióban az "all-in-One" Cognitive Services kulcs nem kötelező. Ha a napi tranzakció száma kevesebb, mint 20 naponta, a rendszer felveszi a költségeket. Ha azonban a tranzakciók túllépik ezt a számot, a feldolgozás folytatásához érvényes erőforrás-kulcsot kell megadni.

Minden "all-in-One" erőforrás-kulcs érvényes. Belsőleg a keresési szolgáltatás az ugyanabban a fizikai régióban található erőforrást fogja használni, még akkor is, ha az "all-in-One" kulcs egy másik régióban található erőforráshoz van. A [termék rendelkezésre állása](https://azure.microsoft.com/global-infrastructure/services/?products=search) oldal a regionális rendelkezésre állást jeleníti meg egymás mellett.

> [!NOTE]
> Ha kihagyja az előre meghatározott képességeket egy készségkészlet, akkor a Cognitive Services nem érhető el, és nem számítunk fel díjat, még akkor sem, ha a készségkészlet egy kulcsot határoz meg.

## <a name="how-billing-works"></a>A számlázás működése

+ Az Azure Cognitive Search a készségkészlet megadott Cognitive Services erőforrás-kulcsot használja a képek és a szöveg gazdagítása érdekében. A számlázható képességek végrehajtása az [Cognitive Services utólagos elszámolású áron](https://azure.microsoft.com/pricing/details/cognitive-services/)érhető el.

+ A képek kinyerése egy Azure Cognitive Search művelet, amely akkor fordul elő, ha a rendszer a dúsítás előtt megrepedt a dokumentumokat. A rendszerkép kibontása számlázható. A képek kibontásával kapcsolatos díjszabásért tekintse meg az [Azure Cognitive Search díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/search/).

+ A szöveg kibontása a dokumentum csinos kifejezésében is előfordul. Nem számlázható.

+ A Cognitive Servicest nem meghívó készségek, beleértve a feltételes, a formázó, a szöveges egyesítés és a szöveg felosztása, nem számlázható.

## <a name="same-region-requirement"></a>Azonos régióra vonatkozó követelmény

Mindkét Cognitive Searchnak és Cognitive Servicesnak ugyanabban a fizikai régióban kell lennie, ahogy azt a [termék rendelkezésre állása](https://azure.microsoft.com/global-infrastructure/services/?products=search) lapon is feltüntették. A legtöbb felkínált régió Cognitive Search Cognitive Services is kínál.

Ha olyan régióban kísérli meg a mesterséges intelligenciát, amely nem rendelkezik mindkét szolgáltatással, akkor a következő üzenet jelenik meg: "a megadott kulcs nem érvényes CognitiveServices a keresési szolgáltatás régiójához."

> [!NOTE]
> Néhány beépített képesség nem regionális Cognitive Services alapul (például a [szöveges fordítási képességre](cognitive-search-skill-text-translation.md)). A nem regionális képesség azt jelenti, hogy a kérést az Azure Cognitive Search régiójától eltérő régióban lehet kiszolgálni. További információ a nem regionális szolgáltatásokról: [Cognitive Services termékek régiónként](https://aka.ms/allinoneregioninfo) oldal.

## <a name="use-free-resources"></a>Ingyenes erőforrások használata

A mesterséges intelligenciával kapcsolatos oktatóanyag és a gyors üzembe helyezési feladatok elvégzéséhez korlátozott, ingyenes feldolgozási lehetőség is használható.

Az ingyenes (korlátozott dúsítású) erőforrások napi 20 dokumentumra korlátozódnak. Az [Indexelő alaphelyzetbe](search-howto-run-reset-indexers.md) állításával alaphelyzetbe állíthatja a számlálót.

Ha az **adatimportálás** varázslót használja az AI- **bővítéshez, az AI-gazdagítás (opcionális)** lapon megtalálja a "csatolás Cognitive Services" lehetőséget.

![Kibontott csatolás Cognitive Services szakasz](./media/cognitive-search-attach-cognitive-services/attach1.png "Kibontott csatolás Cognitive Services szakasz")

## <a name="use-billable-resources"></a>Számlázható erőforrások használata

A napi 20-nál több frissítést létrehozó munkaterhelések esetében ügyeljen arra, hogy számlázható Cognitive Services erőforrást csatoljon. Azt javasoljuk, hogy mindig csatoljon számlázható Cognitive Services-erőforrást, még akkor is, ha soha nem kívánja hívni a Cognitive Services API-k. Egy erőforrás csatolása felülbírálja a napi korlátot.

Csak olyan készségekért kell fizetnie, amelyek meghívja a Cognitive Services API-k. Nem számítunk fel díjat az [Egyéni készségekért](cognitive-search-create-custom-skill-example.md), vagy olyan készségekért, mint a [szöveges egyesítés](cognitive-search-skill-textmerger.md), a [szöveges osztó](cognitive-search-skill-textsplit.md)és a [formáló](cognitive-search-skill-shaper.md), amelyek nem API-alapúak.

Ha az **adatok importálása** varázslót használja, beállíthat egy számlázható erőforrást az **AI-dúsítás hozzáadása (nem kötelező)** oldaláról.

1. Bontsa ki a **csatolás Cognitive Services** elemet, majd válassza az **új Cognitive Services erőforrás létrehozása** lehetőséget. Megnyílik egy új lap, ahol létrehozhatja az erőforrást:

   ![Cognitive Services-erőforrás létrehozása](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Cognitive Services-erőforrás létrehozása")

1. A **hely** listában válassza ki a keresési szolgáltatással rendelkező régiót.

1. A **díjszabási réteg** listában válassza a **S0** lehetőséget a Cognitive Services-funkciók teljes gyűjteményének beszerzéséhez, beleértve az Azure-Cognitive Search által nyújtott beépített készségeket támogató jövőképet és nyelvi funkciókat.

   A S0 szinten a [Cognitive Services díjszabási oldalán](https://azure.microsoft.com/pricing/details/cognitive-services/)találhat meghatározott számítási feladatokra vonatkozó díjszabást.
  
   + Győződjön meg arról, hogy az **ajánlat kiválasztása** listán a **Cognitive Services** van kiválasztva.
   + A **nyelvi** szolgáltatások területen az **text Analytics standard** díjszabása az AI-indexelésre vonatkozik.
   + A **látási** funkciók területen **Computer Vision S1** díjszabása érvényes.

1. Válassza a **Létrehozás** lehetőséget az új Cognitive Services erőforrás kiépítéséhez.

1. Térjen vissza az előző lapra. Válassza a **frissítés** lehetőséget a Cognitive Services erőforrás megjelenítéséhez, majd válassza ki az erőforrást:

   ![Cognitive Services erőforrás kiválasztása](./media/cognitive-search-attach-cognitive-services/attach2.png "Cognitive Services erőforrás kiválasztása")

1. Bontsa ki a **kognitív képességek hozzáadása** szakaszt az adatain futtatni kívánt kognitív képességek kiválasztásához. Fejezze be a varázsló hátralévő részét.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Meglévő készségkészlet csatolása Cognitive Services erőforráshoz

Ha rendelkezik meglévő készségkészlet, csatolhatja azt egy új vagy egy másik Cognitive Services erőforráshoz.

1. A keresési szolgáltatás áttekintése lapon válassza a **szakértelmével**:

   ![Szakértelmével lap](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Szakértelmével lap")

1. Válassza ki a készségkészlet nevét, majd válasszon ki egy meglévő erőforrást, vagy hozzon létre újat. A módosítások jóváhagyásához kattintson **az OK gombra** .

   ![Készségkészlet-erőforrások listája](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Készségkészlet-erőforrások listája")

   Ne feledje, hogy az **ingyenes (korlátozott dúsítású)** beállítás naponta 20 dokumentumra korlátozza a dokumentumokat, és az új **Cognitive Services erőforrás létrehozása** lehetőséggel új számlázható erőforrást építhet ki. Ha új erőforrást hoz létre, válassza a **frissítés** lehetőséget a Cognitive Services erőforrások listájának frissítéséhez, majd válassza ki az erőforrást.

## <a name="attach-cognitive-services-programmatically"></a>Cognitive Services programozott csatolása

Ha programozott módon definiálja a készségkészlet, vegyen fel egy `cognitiveServices` szakaszt a készségkészlet. Ebben a szakaszban adja meg a készségkészlet társítandó Cognitive Services erőforrás kulcsát. Ne feledje, hogy az erőforrásnak ugyanabban a régióban kell lennie, mint az Azure Cognitive Search erőforrásnak. Adja meg `@odata.type` a és a értékeit is `#Microsoft.Azure.Search.CognitiveServicesByKey` .

A következő példa ezt a mintát mutatja be. Figyelje meg a `cognitiveServices` definíció végén található szakaszt.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="example-estimate-costs"></a>Példa: becsült költségek

A kognitív keresési indexeléshez kapcsolódó költségek kiszámításához Kezdje azzal, hogy egy átlagos dokumentum hogyan néz ki, így néhány számot futtathat. Például megközelítheti a következőket:

+ 1 000 PDF-fájlok.
+ Hat oldal.
+ Oldalanként egy kép (6 000 lemezkép).
+ 3 000 karakter/oldal.

Tegyük fel, hogy egy olyan folyamat, amely az egyes PDF-fájlokból, képekből és szöveg-kivonásokból, optikai karakterfelismerésből (OCR) és a szervezetek entitás-felismerésével kapcsolatos dokumentumok repedését tartalmazza.

A cikkben szereplő díjak feltételezettek. A becslési folyamat szemléltetésére szolgálnak. A költségek alacsonyabbak lehetnek. A tranzakciók tényleges áraival kapcsolatban lásd: [Cognitive Services díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services).

1. Szöveg-és képtartalommal való repedés esetén a szöveg kibontása jelenleg ingyenes. 6 000-lemezkép esetén tegyük fel, hogy $1 minden kinyert 1 000-lemezképhez. Ez a lépés a $6,00-as díj.

2. Az 6 000-es rendszerképek optikai KARAKTERFELISMERÉSi képességei a legjobb algoritmust használják (DescribeText). Ennek a lépésnek a $2,50/1 000 rendszerképekkel való elemzését feltételezve kell $15,00 fizetnie.

3. Az entitások kinyeréséhez oldalanként összesen három szöveges rekord van. Minden rekord 1 000 karakterből áll. Oldalanként három szöveges rekord, szorozva az 6 000-lapokkal, 18 000 szöveges rekordokkal. Az $2,00-as 1 000-es szöveges rekordok esetében ennek a lépésnek a díja $36,00.

Mindezt együttesen a $57,00-es számú 1 000-es PDF-dokumentumot kell fizetnie a leírt készségkészlet.

## <a name="next-steps"></a>Következő lépések

+ [Az Azure Cognitive Search díjszabási oldala](https://azure.microsoft.com/pricing/details/search/)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [Készségkészlet létrehozása (REST)](/rest/api/searchservice/create-skillset)
+ [A dúsított mezők leképezése](cognitive-search-output-field-mapping.md)