---
title: Digitális ikermodellek és az ikergráf
titleSuffix: Azure Digital Twins
description: Ismerje meg a digitális Twin fogalmát, és azt, hogy a kapcsolataik hogyan használják a diagramot.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 00058f75a2c4378371c427ff9ebabe7e2336b06a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99576548"
---
# <a name="understand-digital-twins-and-their-twin-graph"></a>A digitális ikrek és a Twin Graph ismertetése

Egy Azure digitális Twins-megoldásban a környezetében lévő entitásokat a **digitális ikrek** jelölik. A digitális Twin a saját egyéni [modelljeinek](concepts-models.md)egyik példánya. Összeköthető más digitális Twins **kapcsolatokkal** a **kettős gráf** kialakításához: Ez a Twin gráf a teljes környezet ábrázolását mutatja be.

> [!TIP]
> Az "Azure digitális Twins" az Azure-szolgáltatás egészére vonatkozik. A "digitális twin (k)" vagy csak a "twin (k)" a szolgáltatás példányán belüli különálló csomópontokra utal.

## <a name="digital-twins"></a>Digital Twins

Ahhoz, hogy létre lehessen hozni egy Digital Twin-et az Azure Digital Twins-példányban, rendelkeznie kell egy, a szolgáltatásba feltöltött *modellel* . A modellek a tulajdonságok, a telemetria és az adott Twin kapcsolatok készletét írják le, egyebek között. A modellben definiált információk típusaiért lásd [*: fogalmak: egyéni modellek*](concepts-models.md).

Egy modell létrehozása és feltöltése után az ügyfélalkalmazás létrehozhat egy típusú példányt; Ez egy digitális Twin. Például a *Floor* modell létrehozása után létrehozhat egy vagy több olyan digitális ikreket, amelyek ezt a típust használják (például a *Floor* típust, egy másikat *, a* *Floor2* stb.).

[!INCLUDE [digital-twins-versus-device-twins](../../includes/digital-twins-versus-device-twins.md)]

## <a name="relationships-a-graph-of-digital-twins"></a>Kapcsolatok: digitális ikrek gráfja

Az ikrek a kapcsolataik között kettős gráfba csatlakoznak. A Twin típusú kapcsolatok a modell részeként definiálhatók.  

Például a modell *emeleten* definiálhat egy olyan kapcsolatot, *amely a* *szoba* típusú ikreket célozza meg. Ezzel a definícióval az Azure Digital Twins lehetővé teszi, hogy az összes *emeleti* Twin-ből *származó kapcsolatot* hozzon létre a Twin *Room* (beleértve az olyan ikreket, amelyek a *szoba* altípusai). 

Ennek a folyamatnak az eredménye egy gráfon (a kapcsolatokon) keresztül összekapcsolt csomópontok (a digitális ikrek) halmaza.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-with-the-apis"></a>Létrehozás az API-kkal

Ez a szakasz azt mutatja be, hogyan hozhat létre digitális ikreket és kapcsolatokat egy ügyfélalkalmazás használatával. Olyan .NET-kód példákat tartalmaz, amelyek a [DigitalTwins API-kat](/rest/api/digital-twins/dataplane/twins)használják, hogy további kontextust biztosítson az egyes fogalmakon belül.

### <a name="create-digital-twins"></a>Digitális ikerpéldányok létrehozása

Az alábbi kódrészlet a [DigitalTwins API](/rest/api/digital-twins/dataplane/twins) -kat használja egy Twin típusú *szoba* létrehozásához.

A Twin tulajdonságot inicializálhatja a létrehozáskor, vagy később is beállíthatja. Ha a kettős és a inicializált tulajdonságokat szeretné létrehozni, hozzon létre egy JSON-dokumentumot, amely megadja a szükséges inicializálási értékeket.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

Használhat egy nevű segítő osztályt is `BasicDigitalTwin` , amely egy "Twin" objektumban található tulajdonság-mezőket közvetlenül a szótár használata alternatívájaként tárolja. További információ a segítő osztályról és a használatáról: a [*digitális*](how-to-manage-twin.md#create-a-digital-twin) ikerek kezelése című rész, amely *bemutatja a digitális ikrek kezelését*.

>[!NOTE]
>Míg a Twin tulajdonságokat nem kötelezőként kezeli a rendszer, ezért nem kell inicializálni, a Twin-ben lévő összes [összetevőt](concepts-models.md#elements-of-a-model) **be kell állítani** a Twin létrehozásakor. Lehetnek üres objektumok, de maguknak az összetevőknek is léteznie kell.

### <a name="create-relationships"></a>Kapcsolatok létrehozása

Íme néhány példa arra, hogy a [DigitalTwins API](/rest/api/digital-twins/dataplane/twins) -kat használó ügyfél létrehozza a kapcsolatot egy digitális twin (a "forrás" Twin) és egy másik digitális twin (a "cél" Twin) között.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="CreateRelationship_short":::

## <a name="json-representations-of-graph-elements"></a>Graph-elemek JSON-ábrázolásai

A digitális Twin-és a kapcsolati adatfájlok egyaránt JSON formátumban vannak tárolva. Ez azt jelenti, hogy amikor [lekérdezi a Twin Graphot](how-to-query-graph.md) az Azure Digital Twins-példányban, az eredmény a digitális ikrek és a létrehozott kapcsolatok JSON-ábrázolása lesz.

### <a name="digital-twin-json-format"></a>Digitális kettős JSON formátum

Ha JSON-objektumként jelenik meg, a Digital Twin a következő mezőket jeleníti meg:

| Mező neve | Description |
| --- | --- |
| `$dtId` | A digitális Twin AZONOSÍTÓját jelölő, felhasználó által megadott karakterlánc |
| `$etag` | A webkiszolgáló által hozzárendelt szabványos HTTP-mező |
| `$conformance` | A digitális iker megfelelőségi állapotát tartalmazó felsorolás ( *nem**megfelelő,* *ismeretlen*) |
| `{propertyName}` | Egy tulajdonság értéke a JSON-ban ( `string` , szám típusa vagy objektum) |
| `$relationships` | A kapcsolatok gyűjtemény elérési útjának URL-címe Ez a mező hiányzik, ha a digitális Twin nem rendelkezik kimenő kapcsolati élekkel. |
| `$metadata.$model` | Választható A digitális IKeret jellemző modell felület azonosítója |
| `$metadata.{propertyName}.desiredValue` | [Csak írható tulajdonságok esetében] A megadott tulajdonság kívánt értéke |
| `$metadata.{propertyName}.desiredVersion` | [Csak írható tulajdonságok esetében] A kívánt érték verziója |
| `$metadata.{propertyName}.ackVersion` | A digitális twint megvalósító eszköz alkalmazás által elfogadott verzió |
| `$metadata.{propertyName}.ackCode` | [Csak írható tulajdonságok esetében] A `ack` digitális IKeret megvalósító eszköz alkalmazás által visszaadott kód |
| `$metadata.{propertyName}.ackDescription` | [Csak írható tulajdonságok esetében] A `ack` digitális IKeret megvalósító eszköz alkalmazás által visszaadott Leírás |
| `{componentName}` | Egy JSON-objektum, amely tartalmazza az összetevő tulajdonságának értékeit és metaadatait, hasonlóan a gyökérszintű objektumhoz. Ez az objektum akkor is létezik, ha az összetevő nem rendelkezik tulajdonsággal. |
| `{componentName}.{propertyName}` | Az összetevő tulajdonságának értéke a JSON-ban ( `string` , szám típusa vagy objektum) |
| `{componentName}.$metadata` | Az összetevő metaadat-információi, a legfelső szintűhez hasonlóan `$metadata` |

Íme egy példa egy, a JSON-objektumként formázott Digital Twin-re:

```json
{
  "$dtId": "Cafe",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "Temperature": 72,
  "Location": {
    "x": 101,
    "y": 33
  },
  "component": {
    "TableOccupancy": 1,
    "$metadata": {
      "TableOccupancy": {
        "desiredValue": 1,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Room;1",
    "Temperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "Location": {
      "desiredValue": {
        "x": 101,
        "y": 33,
      },
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="relationship-json-format"></a>Kapcsolat JSON-formátuma

Ha JSON-objektumként jelenik meg, a digitális iker kapcsolata a következő mezőket jeleníti meg:

| Mező neve | Description |
| --- | --- |
| `$relationshipId` | Egy felhasználó által megadott karakterlánc, amely a kapcsolat AZONOSÍTÓját jelöli. Ez a karakterlánc egyedi a forrásként szolgáló digitális Twin környezetben, ami azt is jelenti, hogy az `sourceId`  +  `relationshipId` Azure Digital Twins-példány kontextusában egyedi. |
| `$etag` | A webkiszolgáló által hozzárendelt szabványos HTTP-mező |
| `$sourceId` | A forrásként szolgáló digitális Twin azonosító |
| `$targetId` | A cél digitális iker azonosítója |
| `$relationshipName` | A kapcsolat neve |
| `{propertyName}` | Választható A kapcsolat tulajdonságának értéke JSON ( `string` , number Type vagy Object) |

Íme egy példa JSON-objektumként formázott kapcsolatra:

```json
{
  "$relationshipId": "relationship-01",
  "$etag": "W/\"506e8391-2b21-4ac9-bca3-53e6620f6a90\"",
  "$sourceId": "GroundFloor",
  "$targetId": "Cafe",
  "$relationshipName": "contains",
  "startDate": "2020-02-04"
}
```

## <a name="next-steps"></a>Következő lépések

Lásd: gráf-elemek kezelése az Azure Digital Twin API-kkal:
* [*Útmutató: digitális ikrek kezelése*](how-to-manage-twin.md)
* [*Útmutató: a Twin gráf kezelése kapcsolatok használatával*](how-to-manage-graph.md)

Vagy Ismerje meg az Azure Digital ikrek Twin Graph lekérdezését az információkhoz:
* [*Fogalmak: lekérdezési nyelv*](concepts-query-language.md)