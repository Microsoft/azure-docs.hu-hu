---
title: Digitális ikereszközök kezelése
titleSuffix: Azure Digital Twins
description: 'Lásd: egyéni ikrek és kapcsolatok lekérése, frissítése és törlése.'
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 666e77a06bd2934622400cc2f11830d6ebc34ddb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954649"
---
# <a name="manage-digital-twins"></a>Digitális ikereszközök kezelése

A környezetben található entitásokat a [digitális ikrek](concepts-twins-graph.md)jelölik. A digitális ikrek kezelése magában foglalhatja a létrehozását, módosítását és eltávolítását. Ezeknek a műveleteknek a végrehajtásához használhatja a [**DigitalTwins API-kat**](/rest/api/digital-twins/dataplane/twins), a [.net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client)-t vagy az [Azure digitális Twins parancssori](how-to-use-cli.md)felületét.

Ez a cikk a digitális ikrek kezelésére koncentrál. Ha a kapcsolatokkal és a [Twin gráfmal](concepts-twins-graph.md) szeretne dolgozni, tekintse meg [*az útmutató: a Twin gráf kezelése a kapcsolatokkal*](how-to-manage-graph.md)című témakört.

> [!TIP]
> Minden SDK-függvény szinkron és aszinkron verzióban érhető el.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-twins"></a>Az ikrek kezelésének módjai

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

## <a name="create-a-digital-twin"></a>Digitális Twin létrehozása

A kettős létrehozásához használja a `CreateOrReplaceDigitalTwinAsync()` metódust a szolgáltatás ügyfélen, például:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwinCall":::

Digitális dupla létrehozásához a következőket kell megadnia:
* A digitális iker kívánt azonosítója
* A használni kívánt [modell](concepts-models.md)

Opcionálisan megadhatja a digitális iker összes tulajdonságának kezdeti értékeit. A tulajdonságokat a rendszer nem kötelezőként kezeli, és később is beállítható, de **nem jelennek meg a két példányban, amíg be nem állították őket.**

>[!NOTE]
>Míg a Twin tulajdonságokat nem kell inicializálni, **a Twin-** ben lévő összes [összetevőt](concepts-models.md#elements-of-a-model) be kell állítani a Twin létrehozásakor. Lehetnek üres objektumok, de maguknak az összetevőknek is léteznie kell.

A modell és a kezdeti tulajdonságértékek a paraméteren keresztül érhetők el `initData` , amely a releváns adatokat tartalmazó JSON-karakterlánc. Az objektum strukturálásával kapcsolatos további információkért folytassa a következő szakasszal.

> [!TIP]
> A Twin létrehozása vagy frissítése után akár 10 másodperces késés is lehet, mielőtt a módosítások megjelennek a [lekérdezésekben](how-to-query-graph.md). Az `GetDigitalTwin` API (a [cikk későbbi részében](#get-data-for-a-digital-twin)leírtak szerint) nem tapasztalja ezt a késleltetést, ezért ha azonnali válaszra van szüksége, használja a lekérdezés helyett az API-hívást az újonnan létrehozott ikrek megtekintéséhez. 

### <a name="initialize-model-and-properties"></a>Modell és tulajdonságok inicializálása

A Twin-példányok tulajdonságainak inicializálása a Twin létrehozásakor történik. 

A Twin-létrehozási API egy olyan objektumot fogad el, amely a Twin tulajdonságok érvényes JSON-leírására van szerializálva. Lásd a következő [*fogalmakat: digitális ikrek és a Twin gráf*](concepts-twins-graph.md) a Twin-fájl JSON-formátumának leírásához. 

Először létrehozhat egy adatobjektumot, amely a Twin és a tulajdonságának értékeit jelöli. A paramétereket manuálisan vagy egy megadott segítő osztály használatával is létrehozhatja. Íme egy példa.

#### <a name="create-twins-using-manually-created-data"></a>Ikrek létrehozása manuálisan létrehozott adatértékek használatával

Az egyéni segítő osztályok használata nélkül egy Twin tulajdonságot is megadhat a-ben `Dictionary<string, object>` , ahol a a tulajdonság neve, a pedig a `string` `object` tulajdonságot és annak értékét jelképező objektum.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

#### <a name="create-twins-with-the-helper-class"></a>Ikrek létrehozása a segítő osztállyal

A segítő osztály `BasicDigitalTwin` lehetővé teszi, hogy közvetlenül egy "Twin" objektumban tárolja a tulajdonságokat. Előfordulhat, hogy továbbra is szeretné felépíteni a tulajdonságok listáját a használatával `Dictionary<string, object>` , amelyet aztán közvetlenül a Twin objektumhoz lehet hozzáadni `CustomProperties` .

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

>[!NOTE]
> `BasicDigitalTwin` az objektumok egy `Id` mezővel rendelkeznek. Ezt a mezőt üresen hagyhatja, de ha egy azonosító értéket ad hozzá, akkor meg kell egyeznie a hívásnak átadott ID paraméterrel `CreateOrReplaceDigitalTwinAsync()` . Például:
>
>```csharp
>twin.Id = "myRoomId";
>```

## <a name="get-data-for-a-digital-twin"></a>Digitális Twin-adatlekérdezés

A következőhöz hasonló módon érheti el bármelyik digitális Twin adatait `GetDigitalTwin()` :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwinCall":::

Ez a hívás a kettős adattípust adja vissza, például: `BasicDigitalTwin` . `BasicDigitalTwin` a egy olyan szerializálási segítő osztály, amely tartalmazza az SDK-t, amely az alapszintű Twin metaadatokat és tulajdonságokat az előre elemzett űrlapon fogja visszaadni. Íme egy példa arra, hogyan használhatja ezt a Twin részletek megtekintésére:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin" highlight="2":::

Csak a legalább egyszer beállított tulajdonságokat adja vissza a rendszer, amikor egy Twin metódust kér le `GetDigitalTwin()` .

>[!TIP]
>A `displayName` for a Twin a modell metaadatai részét képezi, így nem fog megjelenni, amikor a Twin példányhoz adatokat kap. Ha meg szeretné tekinteni ezt az értéket, [a modellből](how-to-manage-model.md#retrieve-models)kérheti le.

Ha több Twins egyetlen API-hívással szeretne beolvasni, tekintse meg a következő témakörben található lekérdezési API-példákat [*: a Twin Graph lekérdezése*](how-to-query-graph.md).

Vegye figyelembe a következő modellt (a [digitális Twins Definition Language (DTDL) nyelven](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL)írt), amely a *holdat* határozza meg:

:::code language="json" source="~/digital-twins-docs-samples/models/Moon.json":::

A `object result = await client.GetDigitalTwinAsync("my-moon");` *Hold* típusú dupla hívás eredménye a következőképpen néz ki:

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:example:Moon;1",
    "radius": {
      "desiredValue": 1737.1,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "mass": {
      "desiredValue": 0.0734,
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

A digitális iker definiált tulajdonságai a digitális iker felső szintű tulajdonságaiként lesznek visszaadva. A DTDL-definíciónak nem részét képező metaadatokat vagy rendszeradatokat előtaggal adja vissza a rendszer `$` . A metaadatok tulajdonságai a következő értékeket tartalmazzák:
* `$dtId`: Az Azure Digital Twins-példányban található Digital Twin azonosító
* `$etag`: A webkiszolgáló által hozzárendelt szabványos HTTP-mező. Ezt a rendszer minden egyes frissítésekor új értékre frissíti, ami hasznos lehet annak megállapítására, hogy a Twin-adatbázis frissítve lett-e a kiszolgálón az előző ellenőrzések óta. A használatával `If-Match` olyan frissítéseket és törléseket végezhet, amelyek csak akkor fejeződnek be, ha az entitás ETAG megegyezik a megadott ETAG. A műveletekkel kapcsolatos további információkért tekintse meg a [DigitalTwins Update](/rest/api/digital-twins/dataplane/twins/digitaltwins_update) és a [DigitalTwins delete](/rest/api/digital-twins/dataplane/twins/digitaltwins_delete)dokumentációját.
* `$metadata`: Más tulajdonságok készlete, beleértve a következőket:
  - A digitális iker modell DTMI.
  - Az egyes írható tulajdonságok szinkronizálási állapota. Ez a leghasznosabb az eszközök esetében, ahol lehetséges, hogy a szolgáltatás és az eszköz eltérő állapotú (például ha egy eszköz offline állapotban van). Ez a tulajdonság jelenleg csak IoT Hubhoz csatlakoztatott fizikai eszközökre vonatkozik. A metaadatok szakaszban található adatokkal megismerheti a tulajdonságok teljes állapotát, valamint az utolsó módosítás időbélyegét is. A szinkronizálási állapottal kapcsolatos további információkért tekintse meg [ezt az IoT hub oktatóanyagot](../iot-hub/tutorial-device-twins.md) az eszköz állapotának szinkronizálásához.
  - Szolgáltatás-specifikus metaadatok, például IoT Hub vagy Azure digitális Twins. 

További információ a szerializálási segítő osztályokról, például a `BasicDigitalTwin` [*útmutató: az Azure Digital Twins API-k és SDK*](how-to-use-apis-sdks.md)-k használata.

## <a name="view-all-digital-twins"></a>Az összes digitális ikrek megtekintése

A példányban lévő összes digitális ikrek megtekintéséhez használjon [lekérdezést](how-to-query-graph.md). Futtathat egy lekérdezést a [lekérdezési API](/rest/api/digital-twins/dataplane/query) -kkal vagy a [CLI-parancsokkal](how-to-use-cli.md).

Itt látható az alapszintű lekérdezés törzse, amely a példányban található összes digitális ikrek listáját fogja visszaadni:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="GetAllTwins":::

## <a name="update-a-digital-twin"></a>Digitális iker frissítése

Egy digitális Twin tulajdonságainak frissítéséhez írja be a cserélni kívánt adatokat a [JSON-javítás](http://jsonpatch.com/) formátumában. Így egyszerre több tulajdonságot is lecserélhet. Ezután átadja a JSON-javítási dokumentumot a következő `UpdateDigitalTwin()` metódusnak:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="UpdateTwinCall":::

A javítási hívások a több tulajdonságot is frissíthetik egyetlen dupla értékre, ahogy szeretné (még az összeset is). Ha több ikrek esetében is frissítenie kell a tulajdonságokat, külön frissítési hívásra van szükség az egyes Twin-példányokhoz.

> [!TIP]
> A Twin létrehozása vagy frissítése után akár 10 másodperces késés is lehet, mielőtt a módosítások megjelennek a [lekérdezésekben](how-to-query-graph.md). Az `GetDigitalTwin` API (a [cikkben korábban](#get-data-for-a-digital-twin)leírtak szerint) nem tapasztalja ezt a késést, ezért a lekérdezés helyett használja az API-hívást az újonnan frissített ikrek megjelenítéséhez, ha azonnali válaszra van szüksége. 

Íme egy példa a JSON-javítási kódra. Ez a dokumentum helyettesíti a digitális Twin-fájl *tömeg* -és *RADIUS-* tulajdonságának értékét.

:::code language="json" source="~/digital-twins-docs-samples/models/patch.json":::

Az Azure .NET SDK [JsonPatchDocument](/dotnet/api/azure.jsonpatchdocument)is létrehozhat javításokat. Íme egy példa.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

### <a name="update-properties-in-digital-twin-components"></a>Tulajdonságok frissítése a digitális dupla összetevőkben

Ne felejtse el, hogy egy modell tartalmazhat összetevőket, így más modellekből is készíthető. 

A digitális Twin összetevőkben található elérési út szintaxisának megadásához használhatja a JSON-javítás Path szintaxisát:

:::code language="json" source="~/digital-twins-docs-samples/models/patch-component.json":::

### <a name="update-a-digital-twins-model"></a>Digitális Twin-modell frissítése

A `UpdateDigitalTwin()` függvény a digitális Twin-et egy másik modellre is áttelepítheti. 

Vegyük például a következő JSON-javítási dokumentumot, amely a digitális Twin metaadatok `$model` mezőjét helyettesíti:

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-1.json":::

Ez a művelet csak akkor lesz sikeres, ha a javítás által módosított digitális kettős modell megfelel az új modellel. 

Tekintse meg a következő példát:
1. Képzelje el, hogy egy digitális Twin *foo_old* modellel rendelkezik. *foo_old* meghatározza a szükséges tulajdonság *tömegét*.
2. Az új modell *foo_new* meghatározza a tulajdonság tömegét, és hozzáadja az új szükséges tulajdonság *hőmérsékletét*.
3. A javítás után a digitális ikernek mind a Mass, mind a hőmérséklet tulajdonsággal rendelkeznie kell. 

Ennek a helyzetnek a javításához frissítenie kell a modellt és a Twin 's hőmérséklet tulajdonságot is, például:

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-2.json":::

### <a name="handle-conflicting-update-calls"></a>Ütköző frissítési hívások kezelése

Az Azure Digital Twins gondoskodik arról, hogy a rendszer minden bejövő kérelmet a másik után dolgozza fel. Ez azt jelenti, hogy még akkor is, ha több függvény próbálkozik ugyanarra a tulajdonságra egy adott Twin-on egyszerre, **nem kell** explicit zárolási kódot írnia az ütközés kezelésére.

Ez a viselkedés/Twin alapon történik. 

Tegyük fel például, hogy a három hívás egy időben érkezik: 
*   Az A tulajdonság írása a *Twin1*
*   B tulajdonság írása a *Twin1*
*   Az A tulajdonság írása a *Twin2*

A *Twin1* módosító két hívás egy másik után kerül végrehajtásra, és minden egyes módosítás esetén az üzenetek módosulnak. Előfordulhat, hogy a *Twin2* módosítására irányuló hívás egyidejűleg, ütközés nélkül is végrehajtható, amint megérkezik.

## <a name="delete-a-digital-twin"></a>Digitális Twin törlése

Az ikreket az metódus használatával törölheti `DeleteDigitalTwin()` . Azonban csak akkor törölhet egy IKeret, ha nincs több kapcsolata. Ezért először törölje a két bejövő és kimenő kapcsolatot.

Az alábbi példa az ikrek és kapcsolataik törlésére szolgáló kódot mutat be. Az `DeleteDigitalTwin` SDK-hívás ki van emelve annak tisztázására, hogy hol esik a tágabb példa kontextusában.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="DeleteTwin" highlight="7":::

### <a name="delete-all-digital-twins"></a>Az összes digitális ikrek törlése

Az összes ikrek egyszerre történő törléséről például töltse le az oktatóanyagban használt minta alkalmazást [*: Fedezze fel az alapokat egy minta ügyfélalkalmazás*](tutorial-command-line-app.md)használatával. A *CommandLoop. cs* fájl ezt egy `CommandDeleteAllTwins()` függvényben végzi el.

## <a name="runnable-digital-twin-code-sample"></a>Futtatható digitális kettős kód minta

Az alábbi futtatható-mintakód használatával hozzon létre egy IKeret, frissítse a részleteit, és törölje a Twin. 

### <a name="set-up-the-runnable-sample"></a>A futtatható minta beállítása

A kódrészlet a modell definíciójában szereplő [Room.js](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) használja az [*oktatóanyag: az Azure Digital Twins megismerése egy minta ügyfélalkalmazás*](tutorial-command-line-app.md)használatával. Ezzel a hivatkozással közvetlenül a fájlra léphet, vagy letöltheti a teljes végpontok közötti [minta projekt](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)részeként.

A minta futtatása előtt tegye a következőket:
1. Töltse le a modell fájlját, helyezze el a projektbe, és cserélje le a `<path-to>` helyőrzőt az alábbi kódban, hogy a program hol találja meg.
2. Cserélje le a helyőrzőt az `<your-instance-hostname>` Azure Digital ikrek példányának állomásnévre.
3. Vegyen fel két függőséget a projekthez, amely szükséges lesz az Azure digitális ikrekkel való együttműködéshez. Az első a .NET-hez készült [Azure Digital Twins SDK](/dotnet/api/overview/azure/digitaltwins/client)csomag, a második pedig az Azure-hoz való hitelesítéshez segítséget nyújtó eszközöket biztosít.

      ```cmd/sh
      dotnet add package Azure.DigitalTwins.Core
      dotnet add package Azure.Identity
      ```

Ha közvetlenül szeretné futtatni a mintát, helyi hitelesítő adatokat is be kell állítania. A következő szakasz végigvezeti a lépéseken.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>Minta futtatása

A fenti lépések elvégzése után közvetlenül futtathatja a következő mintakód-kódot.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs":::

Itt látható a fenti program konzoljának kimenete: 

:::image type="content" source="./media/how-to-manage-twin/console-output-manage-twins.png" alt-text="Konzol kimenete, amely azt mutatja, hogy a Twin létrehozása, frissítése és törlése megtörtént" lightbox="./media/how-to-manage-twin/console-output-manage-twins.png":::

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan hozhat létre és kezelhet kapcsolatokat a digitális ikrek között:
* [*Útmutató: a Twin gráf kezelése kapcsolatok használatával*](how-to-manage-graph.md)