---
title: Mi a Personalizer?
description: A személyre szabott felhőalapú szolgáltatás lehetővé teszi, hogy kiválassza a legjobb felhasználói élményt, amely a valós idejű viselkedéstől való tanulást mutatja be.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 08/27/2020
ms.custom: cog-serv-seo-aug-2020
keywords: személyre szabott, Azure személyre szabott, gépi tanulás
ms.openlocfilehash: b2577502907b69e134651c93ab7a98fc51e9aaa6
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169249"
---
# <a name="what-is-personalizer"></a>Mi a Personalizer?

Az Azure személyre szabott felhőalapú szolgáltatás lehetővé teszi, hogy az alkalmazások a legjobb tartalmi elemet válasszák a felhasználók megjelenítéséhez. A személyre szabott szolgáltatással meghatározhatja, hogy milyen terméket javasol a vásárlóknak, vagy hogy kiderítse a hirdetmény optimális pozícióját. Miután a tartalom megjelenik a felhasználó számára, az alkalmazás figyeli a felhasználó válaszát, és visszaküldi a jutalom pontszámát a személyre szabott szolgáltatásnak. Ezzel biztosíthatja a gépi tanulási modell folyamatos fejlesztését, és személyre szabhatja a legjobb tartalmi elemet a kapott környezetfüggő információk alapján.

> [!TIP]
> A tartalom bármilyen típusú információ, például szöveg, kép, URL-cím, e-mailek, vagy bármi más, amelyet a felhasználók számára szeretne kijelölni és megjeleníteni.

Ez a dokumentáció a következő cikk-típusokat tartalmazza:  

* [**A gyors**](quickstart-personalizer-sdk.md) üzembe helyezési útmutató végigvezeti Önt a szolgáltatásra irányuló kérések lépésein.  
* A [**útmutatók**](how-to-settings.md) útmutatói a szolgáltatás részletesebb vagy testreszabott módokon történő használatára vonatkozó utasításokat tartalmaznak.  
* A [**fogalmak**](how-personalizer-works.md) részletesen ismertetik a szolgáltatás funkcióit és funkcióit.  
* Az [**oktatóanyagok**](tutorial-use-personalizer-web-app.md) már olyan útmutatók, amelyek bemutatják, hogyan használhatja a szolgáltatást összetevőként a szélesebb körű üzleti megoldásokban.  

A Kezdés előtt próbálja ki [a személyre szabást az interaktív bemutatóval](https://personalizationdemo.azurewebsites.net/).

## <a name="how-does-personalizer-select-the-best-content-item"></a>Hogyan választja ki a személyre szabott tartalmi elemet?

A személyre szabás a **megerősítő tanulás** segítségével kiválasztja a legjobb elemet (_művelet_) a kollektív viselkedés és a jutalom pontszámok alapján az összes felhasználó számára. A műveletek a tartalmi elemek, például hírek, adott mozgóképek vagy termékek.

A **rangsorban** hívja meg a műveleti elemet, valamint a művelet funkcióit és a környezeti funkciókat, hogy kiválassza a felső műveleti elemet:

* **Funkciókkal rendelkező műveletek** – az egyes elemekhez tartozó funkciókat tartalmazó tartalmi elemek
* **Környezeti funkciók** – a felhasználók, a környezetük vagy a környezetük funkciói az alkalmazás használatakor

A rangsor hívása azt az azonosítót adja vissza, amely a felhasználó számára megjelenített tartalmi __elem, a__ **jutalmazási művelet azonosítója** mezőben található.

A felhasználó számára megjelenő __művelet__ gépi tanulási modellel van kiválasztva, amely az idő múlásával maximalizálja a nyeremények teljes összegét.

### <a name="sample-scenarios"></a>Mintaforgatókönyvek

Vessünk egy pillantást néhány olyan helyzetre, ahol személyre szabhatja, hogy kiválassza a legmegfelelőbb tartalmat a felhasználók számára.

|Tartalomtípus|Műveletek (funkciókkal)|Környezeti funkciók|Visszaadott jutalom műveleti azonosítója<br>(a tartalom megjelenítése)|
|--|--|--|--|
|Hírek listája|a. `The president...` (országos, politikai, [text])<br>b. `Premier League ...` (globális, sport, [szöveg, rendszerkép, videó])<br> c. `Hurricane in the ...` (regionális, időjárás, [szöveg, rendszerkép]|Az eszköz híreinek olvasása<br>Hónap vagy idény<br>|egy `The president...`|
|Filmek listája|1. `Star Wars` (1977, [művelet, kaland, fantázia], George Lucas)<br>2. `Hoop Dreams` (1994, [dokumentumfilm, sport], Steve James<br>3. `Casablanca` (1942, [Romance, dráma, War], Michael Kertész)|A rendszer figyeli az eszköz mozgóképét<br>képernyő mérete<br>Felhasználó típusa<br>|3. `Casablanca`|
|Termékek listája|i. `Product A` (3 kg, $ $ $ $, 24 órás kézbesítés)<br>ii. `Product B` (20 kg, $ $, 2 hetes szállítás vámmal)<br>iii. `Product C` (3 kg, $ $ $, kézbesítés 48 órán belül)|Az eszköz vásárlásának beolvasása<br>Felhasználói költségkeret<br>Hónap vagy idény|ii. `Product B`|

A személyre szabott megerősítő tanulás segítségével kiválaszthatja az egyetlen legjobb műveletet, amely a _jutalmazási művelet azonosítója_. A Machine learning-modell a következőket használja: 

* Betanított modell – a gépi tanulási modell fejlesztéséhez használt személyre szabott szolgáltatásból korábban kapott információk
* Aktuális adatspecifikus műveletek a funkciókkal és a környezeti funkciókkal

## <a name="when-to-use-personalizer"></a>Mikor kell használni a személyre szabott

A személyre szabási [API](https://go.microsoft.com/fwlink/?linkid=2092082) -t minden **alkalommal meg kell** hívni, amikor az alkalmazás tartalmat jelenít meg. Ez az **esemény egy esemény-** _azonosítóval_ megjegyezve.

A személyre szabott **jutalmazási** [API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) valós időben hívható meg, vagy késleltethető, hogy jobban illeszkedjen az infrastruktúrához. Az üzleti igények alapján határozza meg a jutalom pontszámát. A jutalom pontszáma 0 és 1 között van. Ez lehet egyetlen érték, például 1, jó, 0 vagy rossz, vagy egy szám, amelyet egy, az üzleti célok és mérőszámok alapján létrehozott algoritmus állít elő.

## <a name="content-requirements"></a>Tartalomra vonatkozó követelmények

A személyre szabott tartalom használata:

* Korlátozott számú művelet vagy elem (legfeljebb ~ 50) közül választhat az egyes személyre szabási események közül. Ha nagyobb listával rendelkezik, az [ajánlási motor használatával](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution) csökkentse a lista 50 elemét minden egyes alkalommal, amikor a személyre szabási szolgáltatásban meghívja a rangsort.
* A rangsorolni kívánt tartalmat leíró információkkal rendelkezik: a _funkciók és a_ _környezet funkcióival_ kapcsolatos műveletek.
* Legalább ~ 1k/nap tartalommal kapcsolatos eseményt biztosít a személyre szabáshoz. Ha a személyre szabott nem kapja meg a minimálisan szükséges forgalmat, a szolgáltatás továbbra is megtarthatja az egyetlen legmegfelelőbb tartalmi elemet.

Mivel a személyre szabott, közel valós időben a személyre szabott adatokat használ, a szolgáltatás nem a következő:
* Felhasználói profil adatainak fenntartása és kezelése
* Egyéni felhasználói beállítások vagy előzmények naplózása
* Megtisztított és címkézett tartalom megkövetelése

## <a name="how-to-design-for-and-implement-personalizer"></a>Személyre szabás és megvalósítás

1. [Tervezze](concepts-features.md) meg és tervezze meg a tartalmat, a **_műveleteket_** és a **_környezetet_**. Határozza meg **_a jutalmas pontszámhoz_** tartozó jutalmazási algoritmust.
1. Az Ön által létrehozott minden [személyre szabott erőforrás](how-to-settings.md) egy tanulási ciklusnak tekintendő. A hurok az adott tartalomhoz vagy felhasználói élményhez tartozó rang és jutalmazási hívásokat is megkapja.

    |Erőforrás típusa| Cél|
    |--|--|
    |[Gyakornoki mód](concept-apprentice-mode.md) `E0`|A személyre szabott modell betanítása anélkül, hogy ez hatással lenne a meglévő alkalmazásra, majd telepítse az online tanulási viselkedést éles környezetbe|
    |Standard `S0`|Online tanulási viselkedés éles környezetben|
    |Ingyenes `F0`| Az online tanulási viselkedés kipróbálása nem éles környezetben|

1. Személyre szabás hozzáadása az alkalmazáshoz, webhelyhez vagy rendszeren:
    1. Az alkalmazásban, a webhelyen vagy a rendszeren testreszabhatja a személyre szabási **hívást,** hogy meghatározza a legjobb, egyetlen _tartalmi_ elemet, mielőtt a tartalom megjelenik a felhasználó számára.
    1. Jelenítse meg a legjobb, egyetlen _tartalmi_ tételt, amely a visszaadott _jutalom műveleti azonosítója_ a felhasználónak.
    1. Az _üzleti logikát_ alkalmazva gyűjtheti össze a felhasználó működésével kapcsolatos információkat a **jutalom** pontszámának meghatározásához, például:

    |Működés|Számított jutalom pontszáma|
    |--|--|
    |A felhasználó a legjobb, egyetlen _tartalmi_ elemet (jutalmazási művelet azonosítója) választotta|**1**|
    |A felhasználó által kiválasztott egyéb tartalom|**0**|
    |A felhasználó szüneteltetve, a nem határozott módon görgetve, mielőtt kiválasztja a legjobb, egyetlen _tartalmi_ elemet (jutalmazási művelet azonosítója)|**0,5**|

    1. **Jutalom-hívás küldése** 0 és 1 közötti jutalom pontszámának megadásához
        * A tartalom megjelenítése után azonnal
        * Vagy valamivel később egy offline rendszeren
    1. Egy használati időszak után [értékelje ki a hurkot](concepts-offline-evaluation.md) offline kiértékeléssel. Az offline kiértékelés lehetővé teszi a személyre szabott szolgáltatás hatékonyságának tesztelését és értékelését a kód módosítása vagy a felhasználói élmény befolyásolása nélkül.

## <a name="reference"></a>Referencia 

* [Személyre szabott C# kódon SDK](/dotnet/api/overview/azure/cognitiveservices/client/personalizer)
* [Személyre szabású go SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview)
* [Személyre szabott JavaScript SDK](/javascript/api/@azure/cognitiveservices-personalizer/)
* [Személyre szabott Python SDK](/python/api/overview/azure/cognitiveservices/personalizer)
* [REST API-k](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> A [megszemélyesítő működése](how-personalizer-works.md) 
>  [Mi a megerősítő tanulás?](concepts-reinforcement-learning.md)
