---
title: Ikergráf kezelése kapcsolatokkal
titleSuffix: Azure Digital Twins
description: Megtudhatja, hogyan kezelheti a digitális ikrek gráfját úgy, hogy összekapcsolja őket a kapcsolatokkal.
author: baanders
ms.author: baanders
ms.date: 11/03/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: fde473453aa79e0078765df394acdeb54b3c7fe9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102433318"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Digitális ikrek gráfjának kezelése kapcsolatok használatával

Az Azure digitális ikrek szíve a teljes környezetet jelképező [kettős gráf](concepts-twins-graph.md) . A Twin gráf a **kapcsolatokon** keresztül összekapcsolt digitális ikrekből tevődik össze. 

Miután egy működő [Azure digitális Twins-példánnyal](how-to-set-up-instance-portal.md) rendelkezik, és beállította a [hitelesítési](how-to-authenticate-client.md) kódot az ügyfélalkalmazás számára, a [**DigitalTwins API**](/rest/api/digital-twins/dataplane/twins) -kkal digitális ikreket és azok kapcsolatait is létrehozhatja, módosíthatja és törölheti egy Azure digitális Twins-példányban. Használhatja a [.net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client)-t vagy az [Azure Digital Twins CLI](how-to-use-cli.md)-t is.

Ez a cikk a kapcsolatok és a gráf egészének kezelésére koncentrál. az egyes digitális ikrekkel való munkavégzéshez tekintse meg az [*útmutató: digitális ikrek kezelése*](how-to-manage-twin.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-graph"></a>A gráf kezelésének módjai

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

A gráfot az Azure Digital Twins Explorer-minta használatával is módosíthatja, amely lehetővé teszi az ikrek és a Graph megjelenítését, és az SDK használatát teszi lehetővé a színfalak mögött. A következő szakasz részletesen ismerteti ezt a mintát.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Kapcsolatok létrehozása

A kapcsolatok leírják, hogy a különböző digitális ikrek hogyan kapcsolódnak egymáshoz, ami a Twin gráf alapját képezi.

A kapcsolatok a hívás használatával jönnek létre `CreateOrReplaceRelationshipAsync()` . 

Kapcsolat létrehozásához meg kell adnia a következőket:
* A forrásként szolgáló Twin azonosító (az `srcId` alábbi kódrészletben): annak a Twin-azonosítónak a azonosítója, amelyből a kapcsolat származik.
* A cél dupla azonosító (az `targetId` alábbi kódrészletben): annak a Twin-azonosítónak a azonosítója, amelyben a kapcsolat megérkezik.
* Kapcsolat neve (az `relName` alábbi kódrészletben): a kapcsolat általános típusa, például a _tartalmaz_.
* Egy kapcsolat azonosítója (az `relId` alábbi mintakód esetében): a kapcsolat adott neve, például _Relationship1_.

A kapcsolat AZONOSÍTÓjának egyedinek kell lennie az adott forrás ikerén belül. Nem kell globálisan egyedinek lennie.
A Twin *foo* esetében például minden egyes kapcsolat azonosítójának egyedinek kell lennie. Egy másik dupla *sáv* azonban rendelkezhet olyan kimenő kapcsolattal, amely megegyezik egy *foo* -kapcsolat ugyanazzal az azonosítóval.

Az alábbi mintakód azt szemlélteti, hogyan hozható létre kapcsolat az Azure Digital Twins-példányban. Az SDK-hívást (kiemelten) egy olyan egyéni metóduson belül használja, amely egy nagyobb program kontextusában jelenhet meg.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="CreateRelationshipMethod" highlight="13":::

Ezt az egyéni függvényt most már meghívhatja, hogy hozzon _létre egy olyan kapcsolatot, amely a_ következőhöz hasonló: 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseCreateRelationship":::

Ha több kapcsolatot szeretne létrehozni, megismételheti a hívásokat ugyanahhoz a metódushoz, és a különböző típusú kapcsolatokat átadva az argumentumba. 

További információ a segítő osztályról `BasicRelationship` [*: útmutató: az Azure digitális Twins API-k és SDK-k használata*](how-to-use-apis-sdks.md#serialization-helpers).

### <a name="create-multiple-relationships-between-twins"></a>Több kapcsolat létrehozása az ikrek között

A kapcsolatok besorolása a következők egyike lehet: 

* Kimenő kapcsolatok: az ehhez a twinhoz tartozó kapcsolatok, amelyek kifelé mutatnak a másik ikrekhez való kapcsolódáshoz. A `GetRelationshipsAsync()` metódus a kettős kapcsolat kimenő kapcsolatainak lekérésére szolgál.
* Bejövő kapcsolatok: a "beérkező" hivatkozás létrehozásához a másik ikrek felé mutató kapcsolatok. A `GetIncomingRelationshipsAsync()` metódus a kettős kapcsolat bejövő kapcsolatainak beolvasására szolgál.

A két ikrek közötti kapcsolatok száma nincs korlátozva – az ikrek tetszőleges számú kapcsolata lehet. 

Ez azt jelenti, hogy egyszerre több különböző típusú kapcsolatot tud kifejezni két ikrek között. Például a *Twin A egy* *tárolt* *kapcsolattal és egy* *dupla B*-vel létesített kapcsolattal is rendelkezhet.

Akár ugyanazon típusú kapcsolat több példányát is létrehozhatja ugyanazon két ikrek között, ha szükséges. Ebben a példában a *Twin A* két különböző *tárolt* kapcsolattal *rendelkezik, a 2.* számú kapcsolattal, feltéve, hogy a kapcsolatok eltérő kapcsolati azonosítókkal rendelkeznek.

## <a name="list-relationships"></a>Kapcsolatok listázása

A gráfban lévő egy adott Twin-hez tartozó **kimenő** kapcsolatok listájának eléréséhez az `GetRelationships()` alábbihoz hasonló módszert használhatja:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

Ez egy vagy értéket ad vissza `Azure.Pageable<T>` `Azure.AsyncPageable<T>` , attól függően, hogy a hívás szinkron vagy aszinkron verzióját használja-e.

Íme egy példa, amely a kapcsolatok listáját kéri le. Az SDK-hívást (kiemelten) egy olyan egyéni metóduson belül használja, amely egy nagyobb program kontextusában jelenhet meg.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod" highlight="8":::

Most meghívhatja ezt az egyéni metódust az ikrek kimenő kapcsolatainak megtekintéséhez:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindOutgoingRelationships":::

A beolvasott kapcsolatok használatával más ikrek is megnyitható a gráfban. Ehhez olvassa el a `target` visszaadott kapcsolat mezőjét, majd használja azonosítóként a következő hívásához: `GetDigitalTwin()` .

### <a name="find-incoming-relationships-to-a-digital-twin"></a>Bejövő kapcsolatok keresése digitális Twin-re

Az Azure Digital Twins is rendelkezik egy API-val, amely az adott iker összes **bejövő** kapcsolatát megkeresi. Ez gyakran hasznos a fordított navigáláshoz, illetve a Twin törléséhez.

>[!NOTE]
> `IncomingRelationship` a hívások nem adják vissza a kapcsolat teljes törzsét. A osztályról további információt a `IncomingRelationship` [dokumentációjában](/dotnet/api/azure.digitaltwins.core.incomingrelationship)talál.

Az előző szakaszban található mintakód a kimenő kapcsolatok egy Twin-ből való megtalálására koncentrál. A következő példa hasonló strukturált, de a *bejövő* kapcsolatokat is megkeresi a Twin helyett. Ez a példa az SDK-hívást (kiemelten) is használja egy olyan egyéni metóduson belül, amely egy nagyobb program kontextusában jelenhet meg.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindIncomingRelationshipsMethod" highlight="8":::

Most meghívhatja ezt az egyéni metódust az ikrek bejövő kapcsolatainak megtekintéséhez:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindIncomingRelationships":::

### <a name="list-all-twin-properties-and-relationships"></a>Az összes Twin tulajdonság és kapcsolat listázása

A fenti módszerek használatával a kimenő és a bejövő kapcsolatok egy Twin-re való listázásához létrehozhat egy metódust, amely a teljes Twin-adatokat, például a Twin tulajdonságait és a kapcsolatainak mindkét típusát kinyomtatja. Íme egy példa az egyéni módszerre, amely bemutatja, hogyan lehet a fenti egyéni módszereket kombinálni erre a célra.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FetchAndPrintMethod":::

Mostantól a következőképpen hívhatja ezt az egyéni függvényt: 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFetchAndPrint":::

## <a name="update-relationships"></a>Kapcsolatok frissítése

A kapcsolatok a metódus használatával frissülnek `UpdateRelationship` . 

>[!NOTE]
>Ez a módszer egy kapcsolat **tulajdonságainak** frissítésére szolgál. Ha módosítania kell a kapcsolati forrás Twin vagy a cél ikerét, [törölnie kell a kapcsolatot](#delete-relationships) , majd [újra létre kell hoznia egyet](#create-relationships) az új ikrek használatával.

Az ügyfél hívásához szükséges paraméterek a forrás-és a Twin (az a kapcsolat, ahol a kapcsolat származik), a frissíteni kívánt kapcsolat azonosítója, valamint egy [JSON-javítási](http://jsonpatch.com/) dokumentum, amely a frissíteni kívánt tulajdonságokat és új értékeket tartalmazza.

Az alábbi mintakód bemutatja, hogyan használhatja ezt a metódust. Ez a példa az SDK-hívást (kiemelten) használja egy olyan egyéni metóduson belül, amely egy nagyobb program kontextusában is megjelenhet.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UpdateRelationshipMethod" highlight="6":::

Íme egy példa erre az egyéni metódusra, amely egy JSON-javító dokumentumot továbbít a tulajdonság frissítéséhez szükséges információkkal.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseUpdateRelationship":::

## <a name="delete-relationships"></a>Kapcsolatok törlése

Az első paraméter határozza meg a forrást (Twin) (az a kapcsolat, amelyben a kapcsolat származik). A másik paraméter a kapcsolat azonosítója. A Twin azonosítót és a kapcsolat AZONOSÍTÓját egyaránt meg kell adni, mivel a kapcsolati azonosítók csak a Twin hatókörén belül egyediek.

Az alábbi mintakód bemutatja, hogyan használhatja ezt a metódust. Ez a példa az SDK-hívást (kiemelten) használja egy olyan egyéni metóduson belül, amely egy nagyobb program kontextusában is megjelenhet.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="DeleteRelationshipMethod" highlight="5":::

Most meghívhatja ezt az egyéni metódust a kapcsolat törléséhez, például:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseDeleteRelationship":::

## <a name="runnable-twin-graph-sample"></a>Futtatható Twin Graph minta

A következő futtatható a jelen cikk kapcsolati műveleteivel létrehoz egy dupla gráfot a digitális ikrekből és a kapcsolatokból.

### <a name="set-up-the-runnable-sample"></a>A futtatható minta beállítása

A kódrészlet a [*Room.json*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) és a [*Floor.js*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) modell-definíciókat használja az [*oktatóanyag: az Azure Digital Twins és egy minta ügyfélalkalmazás megismerése*](tutorial-command-line-app.md). Ezekkel a hivatkozásokkal közvetlenül megtekintheti a fájlokat, vagy letöltheti őket a teljes végpontok közötti [minta projekt](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)részeként. 

A minta futtatása előtt tegye a következőket:
1. Töltse le a modell fájljait, helyezze el őket a projektbe, és cserélje le az `<path-to>` alábbi kódban található helyőrzőket, hogy megtudja, hol találják meg a programot.
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

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs":::

Itt látható a fenti program konzoljának kimenete: 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="Konzol kimenete, amely a Twin részleteit, valamint az ikrek bejövő és kimenő kapcsolatait mutatja." lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> A Twin Graph az ikrek közötti kapcsolatok létrehozási fogalma. Ha meg szeretné tekinteni a Twin gráf vizuális ábrázolását, tekintse meg a jelen cikk [*vizualizációs*](how-to-manage-graph.md#visualization) szakaszát. 

## <a name="create-graph-from-a-csv-file"></a>Gráf létrehozása CSV-fájlból

Gyakorlati használati esetekben a különálló hierarchiák gyakran egy másik adatbázisban tárolt adatokból jönnek létre, vagy akár egy táblázatkezelőben vagy CSV-fájlban is. Ez a szakasz azt szemlélteti, hogyan lehet beolvasni egy CSV-fájl adatait, és létrehozni egy különálló gráfot.

Vegye figyelembe a következő adattáblázatot, amely leírja a digitális ikrek és a kapcsolatok készletét.

|  Modellazonosító    | Dupla azonosító (egyedinek kell lennie) | Kapcsolat neve  | Cél dupla azonosító  | Kettős init-adathalmaz |
| --- | --- | --- | --- | --- |
| dtmi: példa: Floor; 1    | Floor1 | contains | Room1 | |
| dtmi: példa: Floor; 1    | Floor0 | contains | Room0 | |
| dtmi: példa: Room; 1    | Room1 | | | {"Hőmérséklet": 80} |
| dtmi: példa: Room; 1    | Room0 | | | {"Hőmérséklet": 70} |

Az Azure digitális Twins-ba való beszerzésének egyik módja, ha átalakítja a táblázatot egy CSV-fájlba, és kódot ír a fájl értelmezéséhez az ikrek és kapcsolatok létrehozásához szükséges parancsokra. Az alábbi mintakód szemlélteti a CSV-fájlból származó adatok olvasását és az Azure digitális Twins-ban létrehozott Twin gráfok létrehozását.

Az alábbi kódban a CSV-fájl neve *data.csv*, és az Azure Digital Twins-példány **állomásneve** jelképező helyőrző. A minta számos olyan csomagot is használ, amelyeket hozzáadhat a projekthez a folyamat segítése érdekében.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graphFromCSV.cs":::

## <a name="next-steps"></a>Következő lépések

Tudnivalók az Azure Digital Twins Twin Graph lekérdezéséről:
* [*Fogalmak: lekérdezési nyelv*](concepts-query-language.md)
* [*Útmutató: a Twin gráf lekérdezése*](how-to-query-graph.md)