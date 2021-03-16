---
title: 'Oktatóanyag: gráf létrehozása az Azure Digital Twinsban (ügyfélalkalmazás)'
titleSuffix: Azure Digital Twins
description: Oktatóanyag Azure digitális Twins-forgatókönyv létrehozásához egy minta parancssori alkalmazás használatával
author: baanders
ms.author: baanders
ms.date: 5/8/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: c18366fd4bc510f32ac0ef255b27709797a3b626
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493704"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-a-sample-client-app"></a>Oktatóanyag: Azure digitális Twins-gráf létrehozása minta ügyfélalkalmazás használatával

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

Ebben az oktatóanyagban egy gráfot fog létrehozni az Azure Digital Ikrekben modellek, ikrek és kapcsolatok használatával. Az oktatóanyag eszköze az a **minta parancssori ügyfélalkalmazás** , amely az Azure Digital Twins-példánnyal való interakciót ismerteti. Az ügyfélalkalmazás hasonló az [*oktatóanyagban írt útmutatóhoz: az ügyfélalkalmazás kódolása*](tutorial-code.md).

Ezt a mintát használhatja olyan alapvető Azure-beli digitális Twins-műveletek elvégzéséhez, mint például a modellek feltöltése, az ikrek létrehozása és módosítása, valamint kapcsolatok létrehozása. A [minta kódját](https://github.com/Azure-Samples/digital-twins-samples/tree/master/) is megtekintheti az Azure Digital Twins API-k megismeréséhez, valamint a saját parancsok megvalósításának gyakorlásához, ha módosítani szeretné a minta projektet.

Ebben az oktatóanyagban a következő lesz:...
> [!div class="checklist"]
> * Környezet modellezése
> * Digitális ikerpéldányok létrehozása
> * Kapcsolatok hozzáadása gráfok létrehozásához
> * A gráf lekérdezése a kérdések megválaszolásához

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

### <a name="run-the-sample-project"></a>A minta projekt futtatása

Most, hogy az alkalmazás és a hitelesítés be van állítva, futtassa a projektet ezzel a gombbal az eszköztáron:

:::image type="content" source="media/tutorial-command-line/app/start-button-sample.png" alt-text="Képernyőfelvétel a Visual Studio Start gombjáról (SampleClientApp projekt)." lightbox="media/tutorial-command-line/app/start-button-sample.png":::

Megnyílik a konzol ablaka, majd hajtsa végre a hitelesítést, és várjon egy parancsot. 
* A hitelesítés a böngészőben történik: az alapértelmezett webböngésző egy hitelesítési kérdéssel fog megnyílni. Ezzel a kérdéssel jelentkezhet be az Azure-beli hitelesítő adataival. Ezután lezárhatja a böngésző lapja vagy ablakát.

Itt látható egy képernyőkép arról, hogy a Project Console hogyan néz ki:

:::image type="content" source="media/tutorial-command-line/app/command-line-app.png" alt-text="Képernyőkép az üdvözlő üzenetről a parancssori alkalmazásból." lightbox="media/tutorial-command-line/app/command-line-app.png":::

> [!TIP]
> A projekttel használható összes lehetséges parancs listáját a `help` projekt konzolon írja be, majd nyomja le az ENTER billentyűt.

Az oktatóanyag további lépéseinek megtartása érdekében futtassa a Project Console-t.

## <a name="model-a-physical-environment-with-dtdl"></a>Fizikai környezet modellezése a DTDL

Most, hogy beállította az Azure digitális Twins-példányt és a minta alkalmazást, megkezdheti a forgatókönyvek gráfjának kiépítése. 

Az Azure digitális Twins-megoldások létrehozásának első lépéseként a rendszer Twin [**modelleket**](concepts-models.md) határoz meg a környezet számára. 

A modellek hasonlók az objektumorientált programozási nyelvekben található osztályokhoz. felhasználói sablonokat biztosítanak a [digitális ikrek](concepts-twins-graph.md) számára a későbbi követéshez és létrehozáshoz. A rendszer a **digitális Twins Definition Language (DTDL)** nevű JSON-szerű nyelven íródott, és meghatározhatja a Twin *tulajdonságait*, *telemetria*, *kapcsolatait* és *összetevőit*.

> [!NOTE]
> A DTDL lehetővé teszi a digitális Twins- *parancsok* definícióját is. A parancsok azonban jelenleg nem támogatottak az Azure Digital Twins szolgáltatásban.

A _**AdtE2ESample**_ -projektet megnyitó Visual Studio-ablakban a *megoldáskezelő* ablaktáblán navigáljon a *AdtSampleApp\SampleClientApp\Models mappára*. Ez a mappa minta modelleket tartalmaz.

Válassza a *Room.json* lehetőséget a szerkesztési ablakban való megnyitásához, és módosítsa a következő módokon:

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>Modellek feltöltése az Azure Digital Twinsba

A modellek tervezése után fel kell töltenie őket az Azure Digital Twins-példányba. Ezzel konfigurálja az Azure Digital Twins szolgáltatás példányát a saját egyéni tartományának szókincsével. Miután feltöltötte a modelleket, létrehozhat kettős példányokat, amelyek használják azokat.

1. A Project Console ablakban a következő parancs futtatásával töltse fel a frissített *szoba* modelljét, valamint egy olyan *emeleti* modellt, amelyet a következő szakaszban is használhat a különböző típusú ikrek létrehozásához.

    ```cmd/sh
    CreateModels Room Floor
    ```
    
    A kimenetnek jeleznie kell, hogy a modellek sikeresen létrejöttek.

1. Ellenőrizze, hogy a modellek létrejöttek-e a parancs futtatásával `GetModels true` . Ez lekérdezi az Azure Digital Twins-példányt minden feltöltött modellhez, és kinyomtatja a teljes információt. Keresse meg a szerkesztett *Room* modellt az eredmények között:

    :::image type="content" source="media/tutorial-command-line/app/output-get-models.png" alt-text="Képernyőkép a GetModels eredményéről, amely a frissített helyiség modelljét mutatja." lightbox="media/tutorial-command-line/app/output-get-models.png":::

### <a name="errors"></a>Hibák

A minta alkalmazás a szolgáltatásból származó hibákat is kezeli. 

Futtassa újra a `CreateModels` parancsot, és próbálja meg újból feltölteni az imént feltöltött modellek egyikét, a második alkalommal:

```cmd/sh
CreateModels Room
```

Mivel a modellek nem írhatók felül, a szolgáltatás hibaüzenetet ad vissza.
A meglévő modellek törlésével kapcsolatos részletekért lásd [*: útmutató: DTDL-modellek kezelése*](how-to-manage-model.md).
```cmd/sh
Response 409: Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"ModelAlreadyExists","message":"Could not add model dtmi:example:Room;2 as it already exists. Use Model_List API to view models that already exist. See the Swagger example.(http://aka.ms/ModelListSwSmpl)"}}

Headers:
Strict-Transport-Security: REDACTED
Date: Wed, 20 May 2020 00:53:49 GMT
Content-Length: 223
Content-Type: application/json; charset=utf-8
```

## <a name="create-digital-twins"></a>Digitális ikerpéldányok létrehozása

Most, hogy egyes modellek fel lettek töltve az Azure Digital Twins-példányba, létrehozhat [**digitális ikreket**](concepts-twins-graph.md) a modell definíciói alapján. A digitális ikrek az Ön üzleti környezetében lévő entitásokat képviselik – például az érzékelők egy farmon, egy épületben lévő helyiségek vagy egy autóban található fények. 

Digitális dupla létrehozásához használja az `CreateDigitalTwin` parancsot. Arra a modellre kell hivatkoznia, amelyre a Twin alapul, és opcionálisan meghatározhatja a modell bármely tulajdonságának kezdeti értékeit is. Ebben a fázisban nem kell semmilyen kapcsolati információt átadnia.

1. Futtassa ezt a kódot a futó projekt konzolon, hogy több ikreket hozzon létre, a korábban frissített *szobatípus* és egy másik modell, a *padló* alapján. Ne felejtse el, hogy a *helyiség* három tulajdonsággal rendelkezik, így a kezdeti értékekkel megadhat argumentumokat. (A tulajdonságértékek inicializálása általánosságban nem kötelező, de ehhez az oktatóanyaghoz szükség van.)

    ```cmd/sh
    CreateDigitalTwin dtmi:example:Room;2 room0 RoomName string Room0 Temperature double 70 HumidityLevel double 30
    CreateDigitalTwin dtmi:example:Room;2 room1 RoomName string Room1 Temperature double 80 HumidityLevel double 60
    CreateDigitalTwin dtmi:example:Floor;1 floor0
    CreateDigitalTwin dtmi:example:Floor;1 floor1
    ```

    A parancsok kimenetének jeleznie kell, hogy az ikrek sikeresen létrejöttek. 
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-digital-twin.png" alt-text="Képernyőfelvétel: a CreateDigitalTwin parancsok eredményeiből származó részlet, amely magában foglalja a floor0, a floor1, a room0 és a room1." lightbox="media/tutorial-command-line/app/output-create-digital-twin.png":::

1. A parancs futtatásával ellenőrizheti, hogy az ikrek létre lettek-e hozva `Query` . Ez a parancs lekérdezi az Azure Digital Twins-példányt az összes olyan digitális Twins esetében, amelyet tartalmaz. Az eredmények között keresse meg a *room0*, a *room1*, a *floor0* és a *floor1* ikreket.

### <a name="modify-a-digital-twin"></a>Digitális Twin módosítása

Módosíthatja a létrehozott Twin-példányok tulajdonságait is. 

> [!NOTE]
> A mögöttes REST API [JSON-javító](http://jsonpatch.com/) formátumot használ a frissítések a Twin-re való definiálásához. A parancssori alkalmazás ezt a formátumot is használja, hogy igazabb élményt nyújtson a mögöttes API-k által vártnak.

1. Futtassa ezt a parancsot a *Room0* RoomName a *room0* -ből a *PresidentialSuite*-re való módosításához:
    
    ```cmd/sh
    UpdateDigitalTwin room0 add /RoomName string PresidentialSuite
    ```
    
    A kimenetnek jeleznie kell, hogy a Twin frissítése sikeresen megtörtént.

1. A frissítés sikeres ellenőrzéséhez futtassa ezt a parancsot a *room0* információinak megtekintéséhez:

    ```cmd/sh
    GetDigitalTwin room0
    ```
    
    A kimenetnek a frissített nevet kell tükröznie.


## <a name="create-a-graph-by-adding-relationships"></a>Gráf létrehozása kapcsolatok hozzáadásával

Ezután létrehozhat néhány **kapcsolatot** ezen ikrek között, hogy összekapcsolja őket egy [**kettős gráfban**](concepts-twins-graph.md). A kettős gráfok egy teljes környezet ábrázolására szolgálnak. 

Az egyik és a másik között létrehozható kapcsolatok típusai a korábban feltöltött [modellekben](#model-a-physical-environment-with-dtdl) vannak meghatározva. A [ *Floor* modell definíciója](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) azt adja meg, hogy a padlók egy nevű kapcsolati típussal rendelkezhetnek. Ez lehetővé teszi, hogy az egyes *emeleti* kapcsolatokból hozzon létre egy beépített típusú kapcsolatot a *benne található megfelelő* helyiségbe.

Kapcsolat hozzáadásához használja az `CreateRelationship` parancsot. Adja meg a kapcsolat létrejöttét, a kapcsolat típusát, valamint azt, hogy a kapcsolat milyen dupla kapcsolaton keresztül kapcsolódik. Végül adja meg a kapcsolatot egy egyedi AZONOSÍTÓval.

1. Futtassa a következő kódot a "tartalmaz" kapcsolat hozzáadásához a korábban létrehozott *emeleti* ikrekből a megfelelő *Room* Twin-ben. A kapcsolatok neve *relationship0* és *relationship1*.

    ```cmd/sh
    CreateRelationship floor0 contains room0 relationship0
    CreateRelationship floor1 contains room1 relationship1
    ```

    >[!TIP]
    >Az [ *emeleti* modellben](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) *található kapcsolat is* két karakterlánc-tulajdonsággal lett definiálva, `ownershipUser` így a `ownershipDepartment` kapcsolatok létrehozásakor a kezdeti értékekkel is megadhat argumentumokat.
    > A fenti parancs egy másik verziója, amely a tulajdonságok kezdeti értékeit is megadja a *relationship0* létrehozásához:
    > ```cmd/sh
    > CreateRelationship floor0 contains room0 relationship0 ownershipUser string MyUser ownershipDepartment string myDepartment
    > ``` 
    
    A parancsok kimenete megerősíti, hogy a kapcsolatok sikeresen létrejöttek:
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-relationship.png" alt-text="Képernyőkép a CreateRelationship parancsok eredményéről, amely tartalmazza a relationship0 és a relationship1." lightbox="media/tutorial-command-line/app/output-create-relationship.png":::

1. A következő parancsok bármelyikével ellenőrizheti a kapcsolatokat, amelyek az Azure Digital Twins-példányban lévő kapcsolatokat kérdezik le.
    * Az egyes emeletekről érkező kapcsolatok megtekintéséhez (az egyik oldal kapcsolatainak megtekintése):
        ```cmd/sh
        GetRelationships floor0
        GetRelationships floor1
        ```
    * Az egyes helyiségekben érkező összes kapcsolat megtekintéséhez (a "másik" oldal kapcsolatának megtekintése):
        ```cmd/sh
        GetIncomingRelationships room0
        GetIncomingRelationships room1
        ```
    * Ha ezeket a kapcsolatokat egyenként szeretné keresni, azonosító szerint:
        ```cmd/sh
        GetRelationship floor0 relationship0
        GetRelationship floor1 relationship1
        ```

Az ebben az oktatóanyagban beállított ikrek és kapcsolatok a következő koncepcionális diagramot alkotják:

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="Egy elméleti gráfot bemutató diagram. a floor0 a relationship0-on keresztül csatlakozik a room0-hez, és a floor1 a relationship1-on keresztül csatlakozik a room1-hoz." border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>A Twin gráf lekérdezése a környezeti kérdések megválaszolásához

Az Azure Digital Twins egyik fő funkciója, hogy könnyen és hatékonyan [lekérdezheti](concepts-query-language.md) a Twin Graphot a környezettel kapcsolatos kérdések megválaszolásához. 

Futtassa a következő parancsokat a futó projekt konzolon a minta környezettel kapcsolatos kérdések megválaszolásához.

1. **Mi a környezetem minden olyan entitása, amely az Azure Digital Twins-ban szerepel?** (az összes lekérdezése)

    ```cmd/sh
    Query
    ```

    Ez lehetővé teszi, hogy a környezetét egy pillantással átvegye, és győződjön meg róla, hogy minden úgy képviselteti magát, mintha az Azure digitális Ikreken belül lenne. Ennek eredménye az, hogy az egyes digitális Twin termékek a részleteket tartalmazzák. Íme egy részlet:

    :::image type="content" source="media/tutorial-command-line/app/output-query-all.png" alt-text="A kettős lekérdezés részleges eredményét ábrázoló képernyőkép, beleértve a room0 és a floor1.":::

    >[!NOTE]
    >A minta projektben a parancs a `Query` további argumentumok nélkül is megegyezik `Query SELECT * FROM DIGITALTWINS` . Ha a példányban lévő összes ikreket le szeretné kérdezni a [lekérdezési API](/rest/api/digital-twins/dataplane/query) -k vagy a [CLI-parancsok](how-to-use-cli.md)használatával, használja a hosszú (teljes) lekérdezést.

1. **Mik azok a szobák a saját környezetben?** (lekérdezés modell alapján)

    ```cmd/sh
    Query SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')
    ```

    A lekérdezés bizonyos típusú ikrekre korlátozható, így pontosabb információkhoz juthat a megjelenített adatokról. Ennek eredménye a *room0* és a *room1*, de nem jeleníti meg a *floor0* vagy a *floor1* (mivel ezek padlók **, nem szobák** ).
    
    :::image type="content" source="media/tutorial-command-line/app/output-query-model.png" alt-text="Képernyőkép a modell lekérdezés eredményéről, amely csak a room0 és a room1 jeleníti meg.":::

1. **Mi a *floor0* összes Terme?** (lekérdezés kapcsolat alapján)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0'
    ```

    A diagramon létesített kapcsolatok alapján lekérdezheti a lekérdezéseket, így információkat kaphat arról, hogy az ikrek hogyan vannak csatlakoztatva, vagy hogy a lekérdezés egy bizonyos területen legyen korlátozva. Csak *room0* van a *floor0*, így az egyetlen hely az eredményben.

    :::image type="content" source="media/tutorial-command-line/app/output-query-relationship.png" alt-text="Képernyőkép a kapcsolati lekérdezés eredményéről, amely a room0 mutatja.":::

1. **Mi a környezetem minden olyan kisvárosa, amely 75 fölötti hőmérséklettel rendelkezik?** (lekérdezés tulajdonság alapján)

    ```cmd/sh
    Query SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
    ```

    A diagramot a tulajdonságok alapján kérdezheti le, hogy különböző kérdésekre válaszoljon, beleértve a kiugró értékek megkeresését a környezetben, amelyeknek szüksége lehet a beavatkozásra. Más összehasonlító operátorok (,,, *<* *>* *=* vagy *! =*) is támogatottak. az eredmények itt jelennek meg, mert a *room1* 80-as hőmérséklettel rendelkezik.

    :::image type="content" source="media/tutorial-command-line/app/output-query-property.png" alt-text="Képernyőkép a tulajdonság-lekérdezés eredményéről, amely csak a room1 jeleníti meg.":::

1. **Mi a *floor0* található összes szoba 75-nál magasabb hőmérséklettel?** (összetett lekérdezés)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75
    ```

    Azt is megteheti, hogy kombinálja a korábbi lekérdezéseket, mint például az SQL-ben, az olyan kombinációs operátorok használatával, mint a `AND` , `OR` `NOT` . A lekérdezés `AND` azt használja, hogy az előző lekérdezés a két hőmérséklet konkrétabb legyen. Az eredmény mostantól csak a 75-nál nagyobb hőmérsékletű szobákat tartalmazza, amelyek a *floor0* vannak – ami ebben az esetben egyikük sem. Az eredményhalmaz üres.

    :::image type="content" source="media/tutorial-command-line/app/output-query-compound.png" alt-text="Képernyőkép az összetett lekérdezés eredményéről, amely nem jeleníti meg az eredményeket." lightbox="media/tutorial-command-line/app/output-query-compound.png":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag elvégzése után kiválaszthatja, hogy mely erőforrásokat szeretné eltávolítani, attól függően, hogy mit szeretne tenni.

* **Ha azt tervezi, hogy továbbra is a következő oktatóanyagot** használja, megtarthatja az itt beállított erőforrásokat, hogy továbbra is használhassa ezt az Azure Digital Twins-példányt és konfigurált minta alkalmazást a következő oktatóanyaghoz

* **Ha továbbra is az Azure Digital Twins-példányt szeretné használni, de törli az összes modelljét, ikreket és kapcsolatát**, a minta alkalmazás és a parancsok segítségével `DeleteAllTwins` `DeleteAllModels` törölheti a példányban található ikreket és modelleket.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Érdemes törölni a Project mappát is a helyi gépről.

## <a name="next-steps"></a>Következő lépések 

Ebben az oktatóanyagban megkezdte az Azure Digital Twins használatát, ha egy minta ügyfélalkalmazás használatával kiépít egy gráfot a példányban. Létrehozta a modelleket, a digitális ikreket és a kapcsolatokat, amelyek egy gráfot alkotnak. Néhány lekérdezést is futtatott a gráfon, hogy megtudja, milyen kérdésekre ad választ az Azure Digital Twins a környezetről.

Folytassa a következő oktatóanyaggal, amellyel kombinálhatja az Azure Digital Twins szolgáltatást más Azure-szolgáltatásokkal, és elvégezheti az adatvezérelt és végpontok közötti helyzetet:
> [!div class="nextstepaction"]
> [*Oktatóanyag: végpontok közötti megoldás összekötése*](tutorial-end-to-end.md)