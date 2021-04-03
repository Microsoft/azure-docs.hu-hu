---
title: Személy modell testreszabása Video Indexer webhellyel
titleSuffix: Azure Media Services
description: Megtudhatja, hogyan szabhatja testre a személy modelljét a Video Indexer webhelyén.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/16/2020
ms.author: juliako
ms.openlocfilehash: 890f8c159c3e8e8d0b4164cf218f320551ea63ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97628935"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Személy modell testreszabása a Video Indexer webhellyel

A Video Indexer támogatja a videókra vonatkozó híresség-felismerést. A híresség-felismerési funkció a gyakran igényelt adatforrások (például a IMDB, a wikipedia és a legnépszerűbb LinkedIn-befolyásoló) alapján körülbelül 1 000 000 arcot ölel fel. A részletes áttekintést lásd: [személy modell testreszabása video Indexerban](customize-person-model-overview.md).

A jelen témakörben ismertetett módon szerkesztheti a videóban észlelt arcokat a Video Indexer webhelyen. Használhatja az API-t is a következő témakörben ismertetett módon: [személyre szabott modell testreszabása API](customize-person-model-with-api.md)-k használatával.

## <a name="central-management-of-person-models-in-your-account"></a>A fiókban található személyek modelljeinek központi kezelése

1. A fiókban lévő személy modellek megtekintéséhez, szerkesztéséhez és törléséhez keresse meg a Video Indexer webhelyet, és jelentkezzen be.
1. Válassza ki a tartalom modell testreszabása gombot a lap bal oldalán.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="Tartalmi modell testreszabása":::
1. Válassza a People (személyek) lapot.

    Ekkor megjelenik az alapértelmezett person modell a fiókjában. Az alapértelmezett person-modell minden olyan arcot tartalmaz, amelyet Ön a videók elemzésekor vagy módosításakor adott meg, amelyhez nem adott meg egyéni személy modellt az indexelés során.

    Ha más személy modelleket hozott létre, azok is megjelennek ezen az oldalon.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-face-model/content-model-customization-people-tab.png" alt-text="Személyek testreszabása":::

## <a name="create-a-new-person-model"></a>Új személy modell létrehozása

1. Kattintson a jobb oldalon a **+ modell hozzáadása** gombra.
1. Adja meg a modell nevét. Mostantól új személyeket és arcokat vehet fel az új person-modellbe.
1. Válassza a lista menü gombot, és válassza a **+ személy hozzáadása** elemet.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-face-model/add-new-person.png" alt-text="Peron hozzáadása":::

## <a name="add-a-new-person-to-a-person-model"></a>Új személy hozzáadása egy személy modellhez

> [!NOTE]
> Video Indexer lehetővé teszi több személy hozzáadását ugyanazzal a névvel egy person-modellben. Javasoljuk azonban, hogy egyedi neveket adjon meg a modellben a használhatóság és az átláthatóság érdekében.

1. Ha új arcot szeretne hozzáadni egy személy modellhez, válassza a lista menü gombot azon személy modell mellett, amelyhez hozzá kívánja adni a Face elemet.
1. Válassza a **+ személy hozzáadása** lehetőséget a menüből.

    Az előugró ablak a személy adatainak kitöltésére kéri. Írja be a személy nevét, és kattintson a pipa gombra.

    Ezután kiválaszthatja a fájlkezelőt, vagy áthúzhatja a Face (arc) rendszerképeit. A Video Indexer minden szabványos képfájl-típust végrehajt (pl.: JPG, PNG és egyebek).

    A Video Indexer képes érzékelni az adott személy előfordulásait az indexelt jövőbeli videók és az aktuálisan már indexelt videók alapján, a személy modelljével, amelyhez új arcot adott hozzá. Az aktuális videókban szereplő személy felismerése hosszabb időt is igénybe vehet, mivel ez egy batch-folyamat.

## <a name="rename-a-person-model"></a>Személy modell átnevezése

A fiókban bármely személy modell átnevezhető, beleértve az alapértelmezett személy modelljét is. Még akkor is, ha átnevezi az alapértelmezett személy modelljét, továbbra is az alapértelmezett személy modellként fog megjelenni a fiókjában.

1. Válassza az átnevezni kívánt személy modell melletti lista menü gombot.
1. Válassza az **Átnevezés** lehetőséget a menüből.
1. Válassza ki a modell aktuális nevét, és írja be az új nevet.
1. Jelölje be a modell átnevezett jelölőnégyzetét.

## <a name="delete-a-person-model"></a>Személy modell törlése

Törölheti a fiókjában létrehozott bármely személy modelljét. Nem törölheti azonban az alapértelmezett személy modelljét.

1. A menüből válassza a **Törlés** lehetőséget.

    Ekkor megjelenik egy előugró ablak, amely értesíti arról, hogy a művelet törli a személy modelljét és az összes benne található embert és fájlt. Ez a művelet nem vonható vissza.
1. Ha biztos benne, válassza a törlés újra lehetőséget.

> [!NOTE]
> Az ezzel (most törölt), a személy modellje által indexelt meglévő videók nem támogatják a videóban megjelenő arcok nevének frissítését. Ezekben a videókban csak akkor szerkesztheti az arcok neveit, ha azokat egy másik személy modellel újraindexeli. Ha a személy modell megadása nélkül végez újraindexelést, a rendszer az alapértelmezett modellt fogja használni.

## <a name="manage-existing-people-in-a-person-model"></a>Személy modellben lévő meglévő személyek kezelése

Ha meg szeretné nézni a saját modelljeinek tartalmát, válassza a személy modell neve melletti nyilat. A legördülő lista az adott személy modellben lévő összes embert megjeleníti. Ha az egyes személyek mellett a lista menü gombra kattint, megjelenik a kezelés, az átnevezés és a törlés lehetőség.  

![A képernyőképen egy környezetfüggő menü látható, amely a kezelés, az átnevezés és a törlés lehetőséggel rendelkezik.](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Személy átnevezése

1. A személy modellben lévő személy átnevezéséhez kattintson a lista menü gombra, és válassza az **Átnevezés** lehetőséget a lista menüből.
1. Válassza ki a személy aktuális nevét, és gépelje be az új nevet.
1. Jelölje be a jelölőnégyzetet, és a személy átnevezve lesz.

### <a name="delete-a-person"></a>Személy törlése

1. Ha törölni szeretne egy személyt a személy modelljéből, kattintson a lista menü gombra, és válassza a **Törlés** lehetőséget a lista menüből.
1. Egy előugró ablak azt jelzi, hogy ez a művelet törli a személyt, és a művelet nem vonható vissza.
1. Válassza a **Törlés** újra lehetőséget, és ezzel eltávolítja a személyt a person modellből.

### <a name="manage-a-person"></a>Személy kezelése

Ha a **kezelés** lehetőséget választja, a **személy részleteit** tartalmazó ablakban láthatja, hogy az adott személy modellje milyen arcot oktat. Ezek az arcok az adott személy azon lapjairól érkeznek, amelyek ezt a személy modellt használják, vagy a manuálisan feltöltött képekből származnak.

> [!TIP]
> A személy **adatai** ablak megnyitásához kattintson a személy nevére, vagy kattintson a **kezelés** lehetőségre, ahogy az a fentiekben látható.

#### <a name="add-a-face"></a>Arc hozzáadása

A **képek hozzáadása** lehetőség kiválasztásával több arcot is hozzáadhat a személyhez.

#### <a name="delete-a-face"></a>Arc törlése

Válassza ki a törölni kívánt rendszerképet, és kattintson a **Törlés** gombra.

#### <a name="rename-and-delete-the-person"></a>Személy átnevezése és törlése 

A kezelés ablaktáblával átnevezheti a személyt, és törölheti a személyt a személy modellből.

## <a name="use-a-person-model-to-index-a-video"></a>Személy modell használata videó indexeléséhez

Az új videót úgy is használhatja, hogy a person modellt a videó feltöltésekor rendeli hozzá.

Ha a személy modelljét új videón szeretné használni, hajtsa végre a következő lépéseket:

1. Kattintson a lap jobb oldalán található **feltöltés** gombra.
1. Dobja el a videofájlokat, vagy tallózással keresse meg a fájlt.
1. Válassza a **Speciális beállítások** nyilat.
1. Válassza ki a legördülő listát, és válassza ki a létrehozott személy modellt.
1. Válassza ki a **feltöltés** lehetőséget az oldal alján, és az új videó indexelve lesz a személy modellje alapján.

Ha nem adja meg a személy modelljét a feltöltés során, Video Indexer indexeli a videót a fiókjában lévő alapértelmezett személy modell használatával.

## <a name="use-a-person-model-to-reindex-a-video"></a>Videó újraindexelése személy modell használatával

Ha egy személy modell segítségével szeretné átindexelni a gyűjteményben található videót, lépjen a fiókhoz tartozó videók oldalra a Video Indexer kezdőlapon, és vigye az egérmutatót az újraindexelni kívánt videó nevére.

Megtekintheti a videó szerkesztésének, törlésének és újraindexelésének lehetőségeit.

1. Válassza a videó újraindexelésének lehetőségét.

    ![A képernyőfelvételen a fiókok videói és a videó újraindexelésének lehetősége látható.](./media/customize-face-model/reindex.png)

    Mostantól kiválaszthatja azt a személy modellt, amellyel újra indexelheti a videót.
1. Válassza ki a legördülő listát, és válassza ki a használni kívánt személy modellt.
1. Jelölje be az **újraindexelés** gombot, és a videó újraindexelve lesz a személy modellje alapján.

Minden olyan új módosítást, amelyet a rendszer az imént újraindexelt videóban észlelt és azonosított arcokon végez, a videó újraindexeléséhez használt személy modellbe menti.

## <a name="managing-people-in-your-videos"></a>Felhasználók kezelése a videókban

Kezelheti az észlelt arcokat, valamint az arcok szerkesztésével és törlésével az indexelt videókban felismert személyeket.

Egy arc törlésével egy adott arc törlődik a videóból.

Az arc szerkesztése átnevezi az észlelt és valószínűleg felismerhető arcot a videóban. Ha szerkeszt egy arcot a videójában, akkor a rendszer a nevet a feltöltés és az indexelés során a videóhoz rendelt személyként adja meg.

Ha nem rendel hozzá egy személy modellt a videóhoz a feltöltés során, a Szerkesztés a fiók alapértelmezett személy modelljébe kerül.

### <a name="edit-a-face"></a>Arc szerkesztése

> [!NOTE]
> Ha egy személy modell két vagy több különböző személlyel rendelkezik ugyanazzal a névvel, ezt a nevet nem fogja tudni felcímkézni az adott személy modellt használó videókon belül. A Video Indexerban csak a tartalom modell testreszabása oldal People (személyek) lapján módosíthatja azokat a személyeket, akik ezt a nevet megosztják. Ezért javasoljuk, hogy egyedi neveket adjon meg minden személynek a személyes modellben.

1. Nyissa meg a Video Indexer webhelyét, és jelentkezzen be.
1. Keressen rá egy videóra, amelyet meg szeretne tekinteni, és szerkeszteni szeretné a fiókjában.
1. A videóban lévő arc szerkesztéséhez lépjen az elemzések lapra, és válassza a ceruza ikont az ablak jobb felső sarkában.

    ![A képernyőképen egy ismeretlen képpel rendelkező videó jelenik meg.](./media/customize-face-model/edit-face.png)

1. Válassza ki az észlelt arcok bármelyikét, és változtassa meg a nevüket az "ismeretlen #X" (vagy a korábban az arcához rendelt név) alapján.
1. Az új név beírása után jelölje be az új név melletti pipa ikont. Ez a művelet elmenti az új nevet, és felismeri és átnevezi az adott arc összes előfordulását a többi aktuális videóban és a később feltöltött videókon. A többi aktuális videóban lévő arc felismerése hosszabb időt is igénybe vehet, mivel ez egy batch-folyamat.

Ha egy olyan személy nevét adja meg a személy modelljében, amelyet a videó használ, akkor az adott személy ezen videójában lévő észlelt Face images egyesíteni fogja a modellben már létezőket. Ha új névvel látja el a nevet, a rendszer létrehoz egy új személy bejegyzést a videó által használt személy modellben.

### <a name="delete-a-face"></a>Arc törlése

Ha törölni szeretne egy észlelt arcot a videójában, lépjen az elemzések ablaktáblára, és válassza a ceruza ikont a panel jobb felső sarkában. Válassza a **Törlés** lehetőséget az arc neve alatt. Ez a művelet eltávolítja az észlelt arcot a videóból. A személy arca továbbra is megjelenik a többi olyan videóban, amelyben megjelenik, de az indexelt után is törölheti ezeket a videókat.

A személy, ha megkapta a nevét, továbbra is létezik a személy modellben, amely azt a videót indexeli, amelyről törölte az arcot, kivéve, ha kifejezetten törli a személyt a személy modelljéből.

## <a name="next-steps"></a>Következő lépések

[Személy modell testreszabása API-k használatával](customize-person-model-with-api.md)
