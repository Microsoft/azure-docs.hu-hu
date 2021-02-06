---
title: Eseményindítók és kötések Azure Functions
description: Megtudhatja, hogyan használhatja az eseményindítókat és a kötéseket az Azure-függvények online eseményekhez és felhőalapú szolgáltatásokhoz való összekapcsolásához.
author: craigshoemaker
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 4cafe9af1eb5a765ab86bafb63cc9ab7d0889dc8
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627599"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions-eseményindítók és -kötések – fogalmak

Ebből a cikkből megtudhatja, hogy milyen magas szintű fogalmakat indít a functions-eseményindítók és-kötések.

Az eseményindítók a függvény futtatását okozzák. Egy eseményindító határozza meg a függvény meghívásának módját, és a függvénynek pontosan egy eseményindítóval kell rendelkeznie. A triggerekhez társított adatok tartoznak, amelyek gyakran a függvény hasznos adataiként vannak megadva. 

A függvények kötése egy másik erőforrás a függvényhez való deklaratív csatlakoztatásának módja. a kötések *bemeneti Kötésként*, *kimeneti Kötésként* vagy mindkettőként is csatlakoztathatók. A kötések adatai a függvények számára paraméterekként vannak megadva.

A különböző kötéseket igény szerint kombinálhatja. A kötések opcionálisak, és egy függvény egy vagy több bemeneti, illetve kimeneti kötéssel is rendelkezhet.

Az eseményindítók és kötések lehetővé teszik a más szolgáltatásokhoz való rögzítjük való hozzáférés elkerülését. A függvény függvényparaméterekben kapja meg az adatokat (például egy üzenetsor üzenetének tartalmát). Az adatokat (például egy üzenetsor üzenetének tartalmát) a függvény által visszaadott értékek használatával küldheti el. 

Tekintse át az alábbi példákat a különböző függvények megvalósítására.

| Példaforgatókönyv | Eseményindító | Bemeneti kötés | Kimeneti kötés |
|-------------|---------|---------------|----------------|
| Egy új üzenetsor-üzenet érkezik, amely egy függvény futtatásával ír egy másik várólistára. | Várólista<sup>*</sup> | *Nincs* | Várólista<sup>*</sup> |
|Az ütemezett feladatok beolvassák Blob Storage tartalmát, és létrehoz egy új Cosmos DB dokumentumot. | Időzítő | Blob Storage | Cosmos DB |
|A Event Grid egy rendszerképet olvas be a Blob Storageról, és a Cosmos DB dokumentumból küld e-mailt. | Event Grid | Blob Storage és Cosmos DB | SendGrid |
| Egy webhook, amely Microsoft Grapht használ az Excel-lapok frissítéséhez. | HTTP | *Nincs* | Microsoft Graph |

<sup>\*</sup> Különböző várólistákat jelöl

Ezek a példák nem teljes körűek, de az eseményindítók és kötések együttes használatának szemléltetésére szolgálnak.

###  <a name="trigger-and-binding-definitions"></a>Trigger-és kötési definíciók

Az eseményindítók és kötések a fejlesztési nyelvtől függően eltérő módon vannak definiálva.

| Nyelv | Az eseményindítók és kötések konfigurálása... |
|-------------|--------------------------------------------|
| C# osztály könyvtára | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;díszítő módszerek és paraméterek C#-attribútumokkal |
| Java | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;díszítő módszerek és paraméterek Java-megjegyzésekkel  | 
| JavaScript/PowerShell/Python/írógéppel | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[function.js](./functions-reference.md) frissítése ([séma](http://json.schemastore.org/function)) |

Az function.js-on alapuló nyelveken a portál a kötések hozzáadására szolgáló felhasználói felületet biztosít az **integráció** lapon. A fájlt közvetlenül a portálon is szerkesztheti a függvény **kód + teszt** lapján. A Visual Studio Code segítségével egyszerűen [adhat hozzá egy kötést egy function.jsfájlhoz](functions-develop-vs-code.md?tabs=nodejs#add-a-function-to-your-project) , ha a promptok kényelmes készletét követi. 

A .NET-ben és a Java-ban a paraméter típusa határozza meg a bemeneti adatok adattípusát. Például a paranccsal `string` kötést lehet létrehozni egy üzenetsor-trigger, egy bájtos tömb, amely bináris fájlként olvasható, valamint egy objektumra deszerializálható egyéni típus. Mivel a .NET-osztály függvénytár-függvények és a Java-függvények nem a kötési definíciók *function.jsra* támaszkodnak, nem hozhatók létre és nem szerkeszthetők a portálon. A c#-portál szerkesztése C#-parancsfájlon alapul, amely az attribútumok helyett *function.jst* használ.

A kötések meglévő függvényekbe való hozzáadásával kapcsolatos további információkért lásd: [függvények összekapcsolását az Azure-szolgáltatásokhoz kötések használatával](add-bindings-existing-function.md).

A dinamikusan beírt nyelvek (például JavaScript) esetében használja a `dataType` *function.js* fájljában található tulajdonságot. Ha például bináris formátumban szeretné beolvasni egy HTTP-kérelem tartalmát, állítsa a következőre `dataType` `binary` :

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

A és a további beállításai `dataType` `stream` `string` .

## <a name="binding-direction"></a>Kötési irány

Minden eseményindító és kötés rendelkezik egy `direction` tulajdonsággal a (z) [function.js](./functions-reference.md) fájlban:

- Az eseményindítók esetében az irány mindig `in`
- A bemeneti és kimeneti kötések használata `in` és `out`
- Egyes kötések speciális irányt támogatnak `inout` . A használata esetén `inout` csak a **speciális szerkesztő** érhető el a portál **integrálás** lapján.

Ha az [attribútumokat egy osztály-függvénytárban](functions-dotnet-class-library.md) konfigurálja az eseményindítók és kötések konfigurálásához, az irány egy attribútum konstruktorában van megadva, vagy a paraméter típusa alapján következtethető ki.

## <a name="add-bindings-to-a-function"></a>Kötések hozzáadása egy függvényhez

A függvényt a bemeneti vagy kimeneti kötések használatával más szolgáltatásokhoz is összekapcsolhatjuk. Adja hozzá a kötést úgy, hogy hozzáadja a függvényhez tartozó konkrét definíciókat. További információ: [kötések hozzáadása meglévő függvényhez Azure functions](add-bindings-existing-function.md).  

## <a name="supported-bindings"></a>Támogatott kötések

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

További információ arról, hogy mely kötések érhetők el előzetes verzióban, vagy hogy a rendszer az éles használatra jóváhagyja a [támogatott nyelveket](supported-languages.md).

## <a name="bindings-code-examples"></a>Példák a kötések kódjára

A következő táblázat példákat mutat be bizonyos kötési típusokra, amelyek bemutatják, hogyan használhatók a függvények kötései. Először válassza ki a projektnek megfelelő nyelv fület. 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="custom-bindings"></a>Egyéni kötések

Létrehozhat egyéni bemeneti és kimeneti kötéseket is. A kötéseket a .NET-ben kell létrehozni, de bármilyen támogatott nyelvről felhasználhatók. Az egyéni kötések létrehozásával kapcsolatos további információkért lásd: [Egyéni bemeneti és kimeneti kötések létrehozása](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings).

## <a name="resources"></a>Források
- [Kötési kifejezések és minták](./functions-bindings-expressions-patterns.md)
- [Az Azure Function Return értékének használata](./functions-bindings-return-value.md)
- [Kötési kifejezés regisztrálása](./functions-bindings-register.md)
- Vizsgálat
  - [Kódtesztelési stratégiák az Azure Functions szolgáltatásban](functions-test-a-function.md)
  - [Nem HTTP által aktivált függvény manuális futtatása](functions-manually-run-non-http.md)
- [Kötési hibák feldolgozása](./functions-bindings-errors.md)

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Azure Functions kötési bővítmények regisztrálása](./functions-bindings-register.md)
