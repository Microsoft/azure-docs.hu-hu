---
title: Eseményindítók és kötések Azure Functions
description: Megtudhatja, hogyan használhatja az eseményindítókat és a kötéseket az Azure-függvények online eseményekhez és felhőalapú szolgáltatásokhoz való összekapcsolásához.
author: craigshoemaker
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: e00fd3d1dac0a18ac7f7377e08ae8d20ae132c56
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2020
ms.locfileid: "91652617"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions-eseményindítók és -kötések – fogalmak

Ebből a cikkből megtudhatja, hogy milyen magas szintű fogalmakat indít a functions-eseményindítók és-kötések.

Az eseményindítók a függvény futtatását okozzák. Egy eseményindító határozza meg a függvény meghívásának módját, és a függvénynek pontosan egy eseményindítóval kell rendelkeznie. A triggerekhez társított adatok tartoznak, amelyek gyakran a függvény hasznos adataiként vannak megadva. 

A függvények kötése egy másik erőforrás a függvényhez való deklaratív csatlakoztatásának módja. a kötések *bemeneti Kötésként*, *kimeneti Kötésként*vagy mindkettőként is csatlakoztathatók. A kötések adatai a függvények számára paraméterekként vannak megadva.

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

Az eseményindítók és kötések a fejlesztési módszertől függően eltérő módon vannak meghatározva.

| Platform | Az eseményindítók és kötések konfigurálása... |
|-------------|--------------------------------------------|
| C# osztály könyvtára | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;díszítő módszerek és paraméterek C#-attribútumokkal |
| Minden más (beleértve a Azure Portal) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[function.js](./functions-reference.md) frissítése ([séma](http://json.schemastore.org/function)) |

A portál egy felhasználói felületet biztosít ehhez a konfigurációhoz, de a fájl szerkesztéséhez közvetlenül is megnyithatja a függvény **integrálás** lapján elérhető **speciális szerkesztőt** .

A .NET-ben a paraméter típusa határozza meg a bemeneti adatok adattípusát. Például a paranccsal `string` kötést hozhat létre egy üzenetsor-trigger szövegéhez, egy byte-tömböt, amely binárisként és egyéni típusként egy objektumra deszerializálható.

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
