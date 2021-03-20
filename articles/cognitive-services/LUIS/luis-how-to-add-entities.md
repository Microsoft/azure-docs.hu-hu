---
title: Entitások hozzáadása – LUIS
description: Entitásokat hozhat létre a Language Understanding (LUIS) alkalmazások felhasználói hosszúságú kimondott szöveg származó kulcsfontosságú adatok kinyeréséhez. Az ügyfélalkalmazás a kinyert fullfil használja.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: c5c6836c2d68036bf2b9c5abe191943537349b8d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91540965"
---
# <a name="add-entities-to-extract-data"></a>Entitások hozzáadása az adatok kinyeréséhez

Entitásokat hozhat létre a Language Understanding (LUIS) alkalmazások felhasználói hosszúságú kimondott szöveg származó kulcsfontosságú adatok kinyeréséhez. Az ügyfélalkalmazás a kinyert entitások adatait használja az fullfilához.

Az entitás egy szót vagy kifejezést jelöl a kinyerni kívánt szövegen belül. Az entitások a szándékkal kapcsolatos információkat írják le, és esetenként elengedhetetlenek, hogy az alkalmazás elvégezze a feladatát. Entitásokat akkor hozhat létre, ha egy példa szerinti kiválasztást ad hozzá egy szándékhoz, vagy kívülről (előtte vagy utána), ha egy Kimondás kiválasztását adja hozzá egy szándékhoz.

## <a name="plan-entities-then-create-and-label"></a>Entitások megtervezése, majd létrehozás és címkézés

a gépi tanulási entitások a példa hosszúságú kimondott szöveg hozhatók létre, vagy az **entitások** lapról hozhatók létre.

Általánosságban az ajánlott eljárás az, hogy időt kell fordítani az entitások megtervezésére, mielőtt a portálon gépi tanulási entitást hozna létre. Ezután hozza létre a gépi tanulási entitást a példából, és az alentitások és a szolgáltatásokban jól ismert funkciókkal. A [dekomponálható entitás oktatóanyaga](tutorial-machine-learned-entity.md) bemutatja, hogyan használhatja ezt a módszert.

Az entitások megtervezésének részeként tudnia kell, hogy szöveg-egyeztetési entitásokra van szüksége (például előre összeépített entitások, reguláris kifejezésű entitások vagy lista entitások). Ezeket az **entitások** lapon hozhatja létre, mielőtt a címkét példaként hosszúságú kimondott szöveg.

Címkézéskor felcímkézheti az egyes entitásokat, majd felépítheti azt egy fölérendelt gépi tanulási entitásba. Vagy elindíthat egy szülő gépi tanulással rendelkező entitást, és elvégezheti a gyermek entitások kibontását.

> [!TIP]
>Címkézse az összes olyan szót, amely egy entitást jelezhet, még akkor is, ha a szavakat nem használják az ügyfélalkalmazás kibontásakor.

## <a name="when-to-create-an-entity"></a>Mikor hozzon létre egy entitást

Az entitások megtervezése után létre kell hoznia a gépi tanulási entitásokat és alentitásokat. Ehhez szükség lehet előre összeépített entitások hozzáadására vagy szöveggel egyező entitások hozzáadására a gépi tanulási entitások szolgáltatásainak biztosításához. Ezeket mind a címkézés előtt el kell végezni.

A példa hosszúságú kimondott szöveg címkézésének megkezdése után létrehozhat Machine Learn entitásokat vagy kiterjesztheti a List entitásokat.

Az alábbi táblázat segítségével megismerheti, hogy az egyes entitások hogyan hozhatók létre vagy adhatók hozzá az alkalmazáshoz.

|Entitástípus|Hol hozható létre entitás a LUIS-portálon|
|--|--|
|gépi tanulási entitás|Entitások vagy leképezés részletei|
|Listaentitás|Entitások vagy leképezés részletei|
|Reguláriskifejezés-entitás|Entitások|
|Pattern.any entitás|Entitások|
|Előre elkészített entitás|Entitások|
|Előre elkészített tartományi entitás|Entitások|

**Az entitások lapon** hozhatja létre az összes entitást, vagy létrehozhat néhány entitást az entitás címkévé való címkézésének részeként a **leképezés részletei** lapon. Csak egy entitást lehet _címkével ellátni_ a **leképezés részletei** lapról.



## <a name="how-to-create-a-new-custom-entity"></a>Új egyéni entitás létrehozása

Ez a folyamat a gépi megtanult entitások, az entitások listázása és a reguláris kifejezés entitások esetében működik.

1. Jelentkezzen be a [Luis-portálra](https://www.luis.ai), és válassza ki az **előfizetését** és a **szerzői erőforrást** , hogy megtekintse az adott szerzői erőforráshoz rendelt alkalmazásokat.
1. Nyissa meg az alkalmazást úgy, hogy kiválasztja a nevét a **saját alkalmazások** oldalon.
1. Válassza az **entitások** lapot.
1. Válassza a **+ Létrehozás** lehetőséget, majd válassza ki az entitás típusát.
1. Folytassa az entitás konfigurálását, majd válassza a **Létrehozás** lehetőséget, ha elkészült.

## <a name="create-a-machine-learned-entity"></a>Gép megtanult entitásának létrehozása

1. Jelentkezzen be a [Luis-portálra](https://www.luis.ai), és válassza ki az **előfizetését** és a **szerzői erőforrást** , hogy megtekintse az adott szerzői erőforráshoz rendelt alkalmazásokat.
1. Nyissa meg az alkalmazást úgy, hogy kiválasztja a nevét a **saját alkalmazások** oldalon.
1. A **build (létrehozás** ) szakaszban válassza az **entitások** lehetőséget a bal oldali panelen, majd válassza a **+ Létrehozás** elemet.
1. Az **entitás típusának létrehozása** párbeszédpanelen adja meg az entitás nevét, és válassza a **gép megtanulta** lehetőséget, majd válassza a lehetőséget. Alentitások hozzáadásához válassza a **struktúra hozzáadása** elemet. Válassza a **Létrehozás** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a gép megtanult entitásának létrehozásáról.](media/add-entities/machine-learned-entity-with-structure.png)

1. Az **alentitások hozzáadása** területen vegyen fel egy alentitást a **+** szülő entitás sorára kattintva.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép az alentitások hozzáadásáról.](media/add-entities/machine-learned-entity-with-subentities.png)

1. Válassza a **Létrehozás** lehetőséget a létrehozási folyamat befejezéséhez.

## <a name="add-a-feature-to-a-machine-learned-entity"></a>Szolgáltatás hozzáadása egy géppel megtanult entitáshoz

1. Jelentkezzen be a [Luis-portálra](https://www.luis.ai), és válassza ki az **előfizetését** és a **szerzői erőforrást** , hogy megtekintse az adott szerzői erőforráshoz rendelt alkalmazásokat.
1. Nyissa meg az alkalmazást úgy, hogy kiválasztja a nevét a **saját alkalmazások** oldalon.
1. A **build (létrehozás** ) szakaszban válassza az **entitások** lehetőséget a bal oldali panelen, majd válassza ki a gépet megtanult entitást.
1. A funkció hozzáadásához válassza a **+ Hozzáadás funkciót** az entitás vagy az alentitás sorban.
1. Válassza ki a meglévő entitások és kifejezések listáját.
1. Ha az entitást csak akkor kell kinyerni, ha a szolgáltatás megtalálható, válassza ki a csillagot a `*` szolgáltatáshoz.

    > [!div class="mx-imgBorder"]
    > ![Képernyőfelvétel a szolgáltatás entitáshoz való hozzáadásáról.](media/add-entities/machine-learned-entity-schema-with-features.png)

## <a name="create-a-regular-expression-entity"></a>Reguláris kifejezés entitásának létrehozása

1. Jelentkezzen be a [Luis-portálra](https://www.luis.ai), és válassza ki az **előfizetését** és a **szerzői erőforrást** , hogy megtekintse az adott szerzői erőforráshoz rendelt alkalmazásokat.
1. Nyissa meg az alkalmazást úgy, hogy kiválasztja a nevét a **saját alkalmazások** oldalon.
1. A **build (létrehozás** ) szakaszban válassza az **entitások** lehetőséget a bal oldali panelen, majd válassza a **+ Létrehozás** elemet.

1. Az **entitás típusának létrehozása** párbeszédpanelen adja meg az entitás nevét, és válassza a **regex** lehetőséget, írja be a reguláris kifejezést a **regex** mezőbe, és válassza a **Létrehozás** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a reguláris kifejezés entitás létrehozásáról.](media/add-entities/add-regular-expression-entity.png)


<a name="add-list-entities"></a>

## <a name="create-a-list-entity"></a>Lista entitás létrehozása

A lista entitások rögzített, lezárt kapcsolódó szavakat jelölnek. Amíg a szerző megváltoztathatja a listát, a LUIS nem fogja növelni vagy csökkenteni a listát. Egy meglévő List entitást is importálhat, ha az [Entity. JSON formátumot](reference-entity-list.md#example-json-to-import-into-list-entity)használja.

A következő lista a kanonikus nevet és a szinonimákat mutatja be.

|Szín listaelem neve|Szín – szinonimák|
|--|--|
|Piros|Crimson, Blood, Apple, Fire-Engine|
|Kék|égbolt, kobalt|
|Zöld|Kelly, lime|

A List entitás létrehozásához kövesse az eljárást. A lista entitás létrehozása után nem kell megadnia például a hosszúságú kimondott szöveg. A listaelemek és a szinonimák pontos szöveggel egyeznek meg.
1. Jelentkezzen be a [Luis-portálra](https://www.luis.ai), és válassza ki az **előfizetését** és a **szerzői erőforrást** , hogy megtekintse az adott szerzői erőforráshoz rendelt alkalmazásokat.
1. Nyissa meg az alkalmazást úgy, hogy kiválasztja a nevét a **saját alkalmazások** oldalon.
1. A **build (létrehozás** ) szakaszban válassza az **entitások** lehetőséget a bal oldali panelen, majd válassza a **+ Létrehozás** elemet.

1. Az **entitás típusának létrehozása** párbeszédpanelen adja meg az entitás nevét, például a `Colors` és a **lista** lehetőséget.
1. Az **entitások listájának létrehozása** párbeszédpanel **új allista hozzáadása.**.. elemében adja meg a listaelem nevét (például) `Green` , majd adja hozzá a szinonimákat.

    > [!div class="mx-imgBorder"]
    > ![Hozzon létre egy listát a színek listájaként az entitás részletei lapon.](media/how-to-add-entities/create-list-entity-of-colors.png)

1. Ha elkészült listaelemek és szinonimák hozzáadásával, válassza a **Létrehozás** elemet.

    Ha végzett az alkalmazás változásainak egy csoportján, ne felejtse el **betanítani** az alkalmazást. Egyetlen módosítás után ne tanítsa az alkalmazást.

    > [!NOTE]
    > Ez az eljárás azt mutatja be, hogyan hozhat létre és címkéz fel egy lista entitást egy példa nélküli kifejezésből a **leképezés részletei** lapon. Ugyanezt az entitást is létrehozhatja az **entitások** lapon.

## <a name="add-a-role-for-an-entity"></a>Szerepkör hozzáadása egy entitáshoz

A szerepkör egy entitás névvel ellátott altípusa, a kontextus alapján.

### <a name="add-a-role-to-distinguish-different-contexts"></a>Szerepkör hozzáadása a különböző kontextusok megkülönböztetéséhez

A következő Kimondás során két helyen van, és mindegyiket szemantikailag kell megadni, például a következő szavakkal `to` `from` :

`Pick up the package from Seattle and deliver to New York City.`

Ebben az eljárásban hozzáadhat `origin` és `destination` szerepköröket egy előre elkészített geographyV2-entitáshoz.
1. Jelentkezzen be a [Luis-portálra](https://www.luis.ai), és válassza ki az **előfizetését** és a **szerzői erőforrást** , hogy megtekintse az adott szerzői erőforráshoz rendelt alkalmazásokat.
1. Nyissa meg az alkalmazást úgy, hogy kiválasztja a nevét a **saját alkalmazások** oldalon.
1. A **build (létrehozás** ) szakaszban válassza az **entitások** lehetőséget a bal oldali panelen.

1. Válassza az **+ előre összeépített entitás hozzáadása** elemet. Válassza a **geographyV2** lehetőséget, majd válassza a **kész** lehetőséget. Ezzel egy előre elkészített entitást adhat hozzá az alkalmazáshoz.

    Ha azt tapasztalja, hogy a Pattern.any entitást tartalmazó minta nem megfelelően vonja ki az entitásokat, egy [explicit lista](reference-pattern-syntax.md#explicit-lists) megoldhatja a problémát.

1. Válassza ki az újonnan hozzáadott előre elkészített geographyV2 entitást **az entitások lap** entitások listájában.
1. Új szerepkör hozzáadásához válassza a tovább lehetőséget, **+** Ha **nincs hozzáadott szerepkör**.
1. A szerepkör **..** . szövegmezőbe írja be a szerepkör nevét, majd írja be a következőt: `Origin` . Adja hozzá a második szerepkör nevét, `Destination` majd adja meg a nevet.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a forrásként szolgáló szerepkör hozzáadásáról a Location entitáshoz](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    A szerepkör hozzá lett adva az előre összeépített entitáshoz, de az adott entitást használó egyik hosszúságú kimondott szöveg sincs hozzájuk adva.

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Egy példa Kimondás szerepkörrel rendelkező szöveg felirata

> [!TIP]
> A szerepköröket lecserélheti egy gépi tanulási entitás alentitásával.

1. Jelentkezzen be a [Luis-portálra](https://www.luis.ai), és válassza ki az **előfizetését** és a **szerzői erőforrást** , hogy megtekintse az adott szerzői erőforráshoz rendelt alkalmazásokat.
1. Nyissa meg az alkalmazást úgy, hogy kiválasztja a nevét a **saját alkalmazások** oldalon.
1. Ugrás a szándék részletei lapra, amely a szerepkört használó hosszúságú kimondott szöveg tartalmaz.
1. Ha címkével szeretne felcímkézni a szerepkört, válassza ki az entitás címkéjét (szöveg alatti folytonos vonal), majd a legördülő listából válassza ki a **Megtekintés az entitásban ablaktáblát** .

    > [!div class="mx-imgBorder"]
    > ![Képernyőfelvétel: az entitások ablaktábla kiválasztott menüjének nézete.](media/add-entities/view-in-entity-pane.png)

    Az entitás paletta a jobb oldalon jelenik meg.

1. Válassza ki az entitást, majd lépjen a paletta aljára, és válassza ki a szerepkört.

    > [!div class="mx-imgBorder"]
    > ![A képernyőfelvételen a szerepkör kiválasztásának helye látható.](media/add-entities/select-role-in-entity-palette.png)

<a name="add-pattern-any-entities"></a>
<a name="add-a-patternany-entity"></a>
<a name="create-a-pattern-from-an-utterance"></a>

## <a name="create-a-patternany-entity"></a>Minta létrehozása bármely entitáshoz

A **minta. minden** entitás csak [mintázatokkal](luis-how-to-model-intent-pattern.md)érhető el.


## <a name="do-not-change-entity-type"></a>Entitás típusának módosítása

LUIS nem teszi lehetővé az entitás típusának módosítását, mert nem tudja, mit kell hozzáadni vagy eltávolítani az entitás létrehozásához. A típus módosításához jobb, ha a megfelelő típusú új entitást kis mértékben eltérő névvel hozza létre. Az entitás létrehozása után minden egyes Kimondás után távolítsa el a régi címkével ellátott entitás nevét, és adja hozzá az új entitás nevét. Ha az összes hosszúságú kimondott szöveg újracímkézték, törölje a régi entitást.


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Előre összeállított modellek használata](howto-add-prebuilt-models.md)

További információk:
* Betanítás [](luis-how-to-train.md)
* [Tesztelés](luis-interactive-test.md)
* [Közzététel](luis-how-to-publish-app.md)
* Minták
    * [Alapelvek](luis-concept-patterns.md)
    * [Syntax](reference-pattern-syntax.md)
* [Előre elkészített entitások GitHub-tárháza](https://github.com/Microsoft/Recognizers-Text)
* [Adatkiemelési fogalmak](luis-concept-data-extraction.md)



