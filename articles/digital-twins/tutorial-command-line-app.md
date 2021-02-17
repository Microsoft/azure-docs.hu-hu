---
title: 'Oktatóanyag: az alapok megismerése egy minta ügyfélalkalmazás alkalmazásával'
titleSuffix: Azure Digital Twins
description: Oktatóanyag az Azure digitális Twins SDK-k megismeréséhez egy minta parancssori alkalmazás használatával
author: baanders
ms.author: baanders
ms.date: 5/8/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: d54207de9d74944e5b44e9970c1c5b896a56a373
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560766"
---
# <a name="tutorial-explore-azure-digital-twins-with-a-sample-client-app"></a>Oktatóanyag: az Azure Digital ikrek megismerése egy minta ügyfélalkalmazás alkalmazásával

Ez az oktatóanyag egy olyan minta alkalmazást vezet be, amely egy parancssori ügyfélalkalmazás megvalósítását teszi lehetővé egy Azure digitális Twins-példánnyal való interakcióhoz. Az ügyfélalkalmazás hasonló az [*oktatóanyagban írt útmutatóhoz: az ügyfélalkalmazás kódolása*](tutorial-code.md).

Ezt a mintát használhatja olyan alapvető Azure-beli digitális Twins-műveletek elvégzéséhez, mint például a modellek feltöltése, az ikrek létrehozása és módosítása, valamint kapcsolatok létrehozása. A minta kódját is megtekintheti az Azure Digital Twins API-k megismeréséhez, valamint a saját parancsok megvalósításának gyakorlásához, ha módosítani szeretné a minta projektet.

Ebben az oktatóanyagban a következő lesz:...
> [!div class="checklist"]
> * Azure digitális Twins-példány beállítása
> * A minta parancssori alkalmazás konfigurálása a példánnyal való kommunikációhoz
> * A parancssori alkalmazás használatával megismerheti az Azure digitális Twins szolgáltatásait, beleértve a **modelleket**, a **digitális ikreket**, a **kapcsolatokat** és a **lekérdezéseket**

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="explore-with-the-sample-solution"></a>Ismerkedés a minta megoldással

Most, hogy a példány és a minta alkalmazás konfigurálva van, a minta projekt és néhány előre megírt példa kód segítségével kiépítheti és felfedezheti az alapszintű Azure Digital Twins-megoldást. A fő megoldás-összetevők **modellek**, **digitális ikrek** és **kapcsolatok**, ami egy adott környezet lekérdezési és **kettős gráfját** eredményezi.

### <a name="model-a-physical-environment-with-dtdl"></a>Fizikai környezet modellezése a DTDL

Az Azure digitális Twins-megoldások létrehozásának első lépéseként a rendszer Twin [**modelleket**](concepts-models.md) határoz meg a környezet számára. 

A modellek hasonlók az objektumorientált programozási nyelvekben található osztályokhoz. felhasználói sablonokat biztosítanak a [digitális ikrek](concepts-twins-graph.md) számára a későbbi követéshez és létrehozáshoz. A rendszer a **digitális Twins Definition Language (DTDL)** nevű JSON-szerű nyelven íródott, és meghatározhatja a Twin *tulajdonságait*, *telemetria*, *kapcsolatait* és *összetevőit*.

> [!NOTE]
> A DTDL lehetővé teszi a digitális Twins- *parancsok* definícióját is. A parancsok azonban jelenleg nem támogatottak az Azure Digital Twins szolgáltatásban.

A _**AdtE2ESample**_ -projektet megnyitó Visual Studio-ablakban a *megoldáskezelő* ablaktáblán navigáljon a *AdtSampleApp\SampleClientApp\Models* mappára. Ez a mappa minta modelleket tartalmaz.

Válassza a *Room.json* lehetőséget a szerkesztési ablakban való megnyitásához, és módosítsa a következő módokon:

1. **Frissítse a verziószámot**, hogy jelezze, hogy a modell egy frissített verzióját biztosítja. Ezt úgy teheti meg, hogy az érték végén lévő *1* `@id` értéket egy *2* értékre módosítja. A jelenlegi verziószámnál nagyobb szám is fog működni.
1. **Tulajdonság szerkesztése**. Módosítsa a `Humidity` tulajdonság nevét *HumidityLevel* értékre (vagy valami más, ha szeretné. Ha más, mint a *HumidityLevel*-t használja, jegyezze fel, hogy mit használt, és folytatja a használatát a *HumidityLevel* helyett az oktatóanyag során.
1. **Adjon hozzá egy tulajdonságot**. A `HumidityLevel` 15. sorban végződő tulajdonság alatt illessze be a következő kódot, és adjon hozzá egy `RoomName` tulajdonságot a helyiséghez:

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="16-20":::

1. **Kapcsolat hozzáadása**. Az `RoomName` imént hozzáadott tulajdonság alatt illessze be a következő kódot, hogy az ilyen típusú Twin formátumhoz való képességet a más  ikrekkel való kapcsolatokkal együtt adja hozzá:

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="21-24":::

Ha elkészült, a frissített modellnek meg kell egyeznie a következővel:

:::code language="json" source="~/digital-twins-docs-samples/models/Room.json":::

Mielőtt továbblép, ügyeljen arra, hogy mentse a fájlt.

> [!TIP]
> Ha saját modellt szeretne létrehozni, beillesztheti a *Room* Model-kódot egy új fájlba, amelyet egy *. JSON* kiterjesztéssel menthet a *AdtSampleApp\SampleClientApp\Models* mappában. Ezt követően a következő lépésekkel adhat hozzá tulajdonságokat és kapcsolatokat, amelyeknek bármilyen tulajdonsága van. A mappában található egyéb mintákat is megtekintheti ötleteket.

> [!TIP] 
> A DTDL érvényességének ellenőrzéséhez használhatja a Language-agnosztikus [DTDL-érvényesítő mintát](/samples/azure-samples/dtdl-validator/dtdl-validator) , amellyel ellenőrizhetők a modell dokumentumai. A szolgáltatás a DTDL-elemző könyvtárra épül, amelyről további információt a modellek elemzése [*és ellenőrzése*](how-to-parse-models.md)című témakörben olvashat.

### <a name="get-started-with-the-command-line-app"></a>Ismerkedés a parancssori alkalmazással

Most, hogy meghatározta a modellt, a további lépések a minta alkalmazás használatával működnek együtt az Azure Digital Twins-példánnyal. Futtassa a projektet ezzel a gombbal az eszköztáron:

:::image type="content" source="media/tutorial-command-line-app/start-button-sample.png" alt-text="A Visual Studio Start gombja (SampleClientApp projekt)":::

Megnyílik a konzol ablaka, majd hajtsa végre a hitelesítést, és várjon egy parancsot. 
* A hitelesítés a böngészőben történik: az alapértelmezett webböngésző egy hitelesítési kérdéssel fog megnyílni. Ezzel a kérdéssel jelentkezhet be az Azure-beli hitelesítő adataival. Ezután lezárhatja a böngésző lapja vagy ablakát.

Itt látható egy képernyőkép arról, hogy a Project Console hogyan néz ki:

:::image type="content" source="media/tutorial-command-line-app/command-line-app.png" alt-text="Üdvözlő üzenet a parancssori alkalmazásból":::

> [!TIP]
> A projekttel használható összes lehetséges parancs listáját a `help` projekt konzolon írja be, majd nyomja le az ENTER billentyűt.
> :::image type="content" source="media/tutorial-command-line-app/command-line-app-help.png" alt-text="A Súgó parancs kimenete":::

Az oktatóanyag további lépéseinek megtartása érdekében futtassa a Project Console-t.

#### <a name="upload-models-to-azure-digital-twins"></a>Modellek feltöltése az Azure Digital Twinsba

A modellek tervezése után fel kell töltenie őket az Azure Digital Twins-példányba. Ezzel konfigurálja az Azure Digital Twins szolgáltatás példányát a saját egyéni tartományának szókincsével. Miután feltöltötte a modelleket, létrehozhat kettős példányokat, amelyek használják azokat.

A Project Console ablakban a következő parancs futtatásával töltse fel a frissített *szoba* modelljét, valamint egy olyan *emeleti* modellt, amelyet a következő szakaszban is használhat a különböző típusú ikrek létrehozásához.

```cmd/sh
CreateModels Room Floor
```

A kimenetnek jeleznie kell, hogy a modellek sikeresen létrejöttek.

> [!TIP]
> Ha korábban úgy tervezte meg a saját modelljét, hogy itt is feltöltheti azt, a fájl nevének hozzáadásával (a bővítmény kihagyható) a `Room Floor` fenti parancsban található listára.

Ellenőrizze, hogy a modellek létrejöttek-e a parancs futtatásával `GetModels true` . Ez lekérdezi az Azure Digital Twins-példányt minden feltöltött modellhez, és kinyomtatja a teljes információt. Keresse meg a szerkesztett *Room* modellt az eredmények között:

:::image type="content" source="media/tutorial-command-line-app/output-get-models.png" alt-text="A GetModels eredményei, amely a frissített helyiség modelljét mutatja":::

#### <a name="errors"></a>Hibák

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

### <a name="create-digital-twins"></a>Digitális ikerpéldányok létrehozása

Most, hogy egyes modellek fel lettek töltve az Azure Digital Twins-példányba, létrehozhat [**digitális ikreket**](concepts-twins-graph.md) a modell definíciói alapján. A digitális ikrek az Ön üzleti környezetében lévő entitásokat képviselik – például az érzékelők egy farmon, egy épületben lévő helyiségek vagy egy autóban található fények. 

Digitális dupla létrehozásához használja az `CreateDigitalTwin` parancsot. Arra a modellre kell hivatkoznia, amelyre a Twin alapul, és opcionálisan meghatározhatja a modell bármely tulajdonságának kezdeti értékeit is. Ebben a fázisban nem kell semmilyen kapcsolati információt átadnia.

Futtassa ezt a kódot a futó projekt konzolon, hogy több ikreket hozzon létre, a korábban frissített *szobatípus* és egy másik modell, a *padló* alapján. Ne felejtse el, hogy a *helyiség* három tulajdonsággal rendelkezik, így a kezdeti értékekkel megadhat argumentumokat.

```cmd/sh
CreateDigitalTwin dtmi:example:Room;2 room0 RoomName string Room0 Temperature double 70 HumidityLevel double 30
CreateDigitalTwin dtmi:example:Room;2 room1 RoomName string Room1 Temperature double 80 HumidityLevel double 60
CreateDigitalTwin dtmi:example:Floor;1 floor0
CreateDigitalTwin dtmi:example:Floor;1 floor1
```

> [!TIP]
> Ha korábban feltöltötte a saját modelljét, próbálja meg a `CreateDigitalTwin` fenti parancsok alapján saját parancsot hozzáadni a saját modell típusának hozzáadásához.

A parancsok kimenetének jeleznie kell, hogy az ikrek sikeresen létrejöttek. 

:::image type="content" source="media/tutorial-command-line-app/output-create-digital-twin.png" alt-text="A CreateDigitalTwin parancsok eredményeiből származó részlet, amely az floor0, a floor1, a room0 és a room1 mutatja":::

Azt is ellenőrizheti, hogy az ikrek létrejöttek-e a `Query` parancs futtatásával. Ez a parancs lekérdezi az Azure Digital Twins-példányt az összes olyan digitális Twins esetében, amelyet tartalmaz. Az eredmények között keresse meg a *floor0*, a *floor1*, a *room0* és a *room1* ikreket.

#### <a name="modify-a-digital-twin"></a>Digitális Twin módosítása

Módosíthatja a létrehozott Twin-példányok tulajdonságait is. Próbálja meg futtatni ezt a parancsot a *Room0* RoomName a *room0* -ből a *PresidentialSuite*-re való módosításához:

```cmd/sh
UpdateDigitalTwin room0 add /RoomName string PresidentialSuite
```

A kimenetnek jeleznie kell, hogy a Twin frissítése sikeresen megtörtént.

Azt is ellenőrizheti, hogy a parancs futtatásával megtekintheti a *room0* adatait:

```cmd/sh
GetDigitalTwin room0
```

A kimenetnek a frissített nevet kell tükröznie.

> [!NOTE]
> A mögöttes REST API JSON-javítást használ a frissítések a Twin-re való definiálásához. A parancssori alkalmazás ezt a formátumot tükrözi, így kísérletezni tud a mögöttes API-k által várt módon.

### <a name="create-a-graph-by-adding-relationships"></a>Gráf létrehozása kapcsolatok hozzáadásával

Ezután létrehozhat néhány **kapcsolatot** ezen ikrek között, hogy összekapcsolja őket egy [**kettős gráfban**](concepts-twins-graph.md). A kettős gráfok egy teljes környezet ábrázolására szolgálnak. 

Kapcsolat hozzáadásához használja az `CreateRelationship` parancsot. Adja meg a kapcsolathoz tartozó IKeret, a felvenni kívánt kapcsolat típusát, valamint azt, hogy a kapcsolat milyen dupla kapcsolattal csatlakozzon. Végül adja meg a kapcsolat nevét (AZONOSÍTÓját).

Futtassa a következő kódot a "tartalmaz" kapcsolat hozzáadásához a korábban létrehozott *emeleti* ikrekből a megfelelő *Room* Twin-ben. Vegye figyelembe, hogy a *padló* -modellben definiálni *kell egy olyan* kapcsolatot, amely erre a lehetőségre vonatkozik.

```cmd/sh
CreateRelationship floor0 contains room0 relationship0
CreateRelationship floor1 contains room1 relationship1
```

A parancsok kimenete megerősíti, hogy a kapcsolatok sikeresen létrejöttek:

:::image type="content" source="media/tutorial-command-line-app/output-create-relationship.png" alt-text="A CreateRelationship parancsok eredményeiből származó részlet, amely a relationship0 és a relationship1 mutatja":::

Az alábbi parancsokkal is ellenőrizheti a kapcsolatokat, amelyek az Azure Digital Twins-példányban lévő kapcsolatokat kérdezik le.
* Ha meg szeretné tekinteni az egyes emeletek összes kapcsolatát (az egyik oldalról a kapcsolatokat tekinti meg),
    ```cmd/sh
    GetRelationships floor0
    GetRelationships floor1
    ```
* Az egyes helyiségekben érkező összes kapcsolat megtekintéséhez (a "másik" oldal kapcsolatának megtekintése)
    ```cmd/sh
    GetIncomingRelationships room0
    ```
* Ha ezeket a kapcsolatokat egyenként szeretné lekérdezni, 
    ```cmd/sh
    GetRelationship floor0 relationship0
    GetRelationship floor1 relationship1
    ```

Az ebben az oktatóanyagban beállított ikrek és kapcsolatok a következő koncepcionális diagramot alkotják:

:::image type="content" source="media/tutorial-command-line-app/sample-graph.png" alt-text="A relationship0 és a room0 között összekapcsolt floor0, valamint a relationship1-room1 keresztül csatlakoztatott floor1" border="false":::

### <a name="query-the-twin-graph-to-answer-environment-questions"></a>A Twin gráf lekérdezése a környezeti kérdések megválaszolásához

Az Azure Digital Twins egyik fő funkciója, hogy könnyen és hatékonyan [lekérdezheti](concepts-query-language.md) a Twin Graphot a környezettel kapcsolatos kérdések megválaszolásához. Futtassa az alábbi parancsokat a futó projekt konzolon, hogy megtudja, milyen hasonló.

* **Milyen entitások vannak a környezetében az Azure Digital Ikrekben?** (az összes lekérdezése)

    ```cmd/sh
    Query
    ```

    Ez lehetővé teszi, hogy a környezetét egy pillantással átvegye, és győződjön meg róla, hogy minden úgy képviselteti magát, mintha az Azure digitális Ikreken belül lenne. Ennek eredménye az, hogy az egyes digitális Twin termékek a részleteket tartalmazzák. Íme egy részlet:

    :::image type="content" source="media/tutorial-command-line-app/output-query-all.png" alt-text="A kettős lekérdezés részleges eredménye, amely megjeleníti a room0 és a floor1":::

    >[!NOTE]
    >A minta projektben a parancs a `Query` további argumentumok nélkül is megegyezik `Query SELECT * FROM DIGITALTWINS` . Ha a példányban lévő összes ikreket le szeretné kérdezni a [lekérdezési API](/rest/api/digital-twins/dataplane/query) -k vagy a [CLI-parancsok](how-to-use-cli.md)használatával, használja a hosszú (teljes) lekérdezést.

* **Mik azok a szobák a saját környezetben?** (lekérdezés modell alapján)

    ```cmd/sh
    Query SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')
    ```

    A lekérdezés bizonyos típusú ikrekre korlátozható, így pontosabb információkhoz juthat a megjelenített adatokról. Ennek eredménye a *room0* és a *room1*, de nem jeleníti meg a *floor0* vagy a *floor1* (mivel ezek padlók **, nem szobák** ).
    
    :::image type="content" source="media/tutorial-command-line-app/output-query-model.png" alt-text="A modell lekérdezésének eredménye, amely csak a room0 és a room1 jeleníti meg":::

* **Mi a *floor0* összes Terme?** (lekérdezés kapcsolat alapján)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0'
    ```

    A diagramon létesített kapcsolatok alapján lekérdezheti a lekérdezéseket, így információkat kaphat arról, hogy az ikrek hogyan vannak csatlakoztatva, vagy hogy a lekérdezés egy bizonyos területen legyen korlátozva. Csak *room0* van a *floor0*, így az egyetlen hely az eredményben.

    :::image type="content" source="media/tutorial-command-line-app/output-query-relationship.png" alt-text="A room0 mutató kapcsolati lekérdezés eredményei":::

* **Mi a környezetem minden olyan kisvárosa, amely 75 fölötti hőmérséklettel rendelkezik?** (lekérdezés tulajdonság alapján)

    ```cmd/sh
    Query SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
    ```

    A diagramot a tulajdonságok alapján kérdezheti le, hogy különböző kérdésekre válaszoljon, beleértve a kiugró értékek megkeresését a környezetben, amelyeknek szüksége lehet a beavatkozásra. Más összehasonlító operátorok (,,, *<* *>* *=* vagy *! =*) is támogatottak. az eredmények itt jelennek meg, mert a *room1* 80-as hőmérséklettel rendelkezik.

    :::image type="content" source="media/tutorial-command-line-app/output-query-property.png" alt-text="A tulajdonság-lekérdezés eredménye, amely csak a room1 jeleníti meg":::

* **Mi a *floor0* található összes szoba 75-nál magasabb hőmérséklettel?** (összetett lekérdezés)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75
    ```

    Azt is megteheti, hogy kombinálja a korábbi lekérdezéseket, mint például az SQL-ben, az olyan kombinációs operátorok használatával, mint a `AND` , `OR` `NOT` . A lekérdezés `AND` azt használja, hogy az előző lekérdezés a két hőmérséklet konkrétabb legyen. Az eredmény mostantól csak a 75-nál nagyobb hőmérsékletű szobákat tartalmazza, amelyek a *floor0* vannak – ami ebben az esetben egyikük sem. Az eredményhalmaz üres.

    :::image type="content" source="media/tutorial-command-line-app/output-query-compound.png" alt-text="Az összetett lekérdezés eredménye, amely nem mutat eredményt":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag elvégzése után kiválaszthatja, hogy mely erőforrásokat szeretné eltávolítani, attól függően, hogy mit szeretne tenni.

* **Ha azt tervezi, hogy továbbra is a következő oktatóanyagot** használja, megtarthatja az itt beállított erőforrásokat, hogy továbbra is használhassa ezt az Azure Digital Twins-példányt és konfigurált minta alkalmazást a következő oktatóanyaghoz

* **Ha továbbra is az Azure Digital Twins-példányt szeretné használni, de törli az összes modelljét, ikreket és kapcsolatát**, a minta alkalmazás és a parancsok segítségével `DeleteAllTwins` `DeleteAllModels` törölheti a példányban található ikreket és modelleket. Ez a következő oktatóanyaghoz biztosít tiszta lappal.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Érdemes törölni a Project mappát is a helyi gépről.

## <a name="next-steps"></a>Következő lépések 

Ebben az oktatóanyagban megkezdte az Azure Digital Twins használatát egy példány és egy ügyfélalkalmazás beállításával a példánnyal való interakcióhoz. Az ügyfélalkalmazás felhasználta az Azure digitális ikrek felfedezését, a modellek, a digitális ikrek és a kapcsolatok létrehozását. Néhány lekérdezést is futtatott a megoldással kapcsolatban, hogy megtudja, milyen kérdésekre ad választ az Azure Digital Twins a környezetről.

Folytassa a következő oktatóanyaggal, amely a minta parancssori alkalmazást más Azure-szolgáltatásokkal együtt használja egy adatvezérelt, végpontok közötti forgatókönyv végrehajtásához:
> [!div class="nextstepaction"]
> [*Oktatóanyag: végpontok közötti megoldás összekötése*](tutorial-end-to-end.md)