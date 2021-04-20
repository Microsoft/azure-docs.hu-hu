---
title: Eseményindítók és kötések a Azure Functions
description: Megtudhatja, hogyan csatlakoztathatja azure-függvényét online eseményekhez és felhőalapú szolgáltatásokhoz eseményindítók és kötések használatával.
author: craigshoemaker
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 8648a52c58929983070b9a89c8fe946b89d37385
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739403"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions-eseményindítók és -kötések – fogalmak

Ebben a cikkben megismeri a függvények eseményindítóit és kötését körülvevő alapvető fogalmakat.

A függvények az eseményindítók miatt futnak. Az eseményindító határozza meg egy függvény meghívásának a metódusát, és egy függvénynek pontosan egy eseményindítóval kell lennie. A triggerekhez társított adatok tartoznak, amelyek gyakran a függvény hasznos adataiként vannak megadva. 

A függvényhez való kötéssel deklaratív módon csatlakoztatunk egy másik erőforrást a függvényhez; A kötések bemeneti kötésként, kimeneti kötésként *vagy* mindkettőként is összekapcsolhatóak. A kötések adatai a függvények számára paraméterekként vannak megadva.

A különböző kötéseket igény szerint kombinálhatja. A kötések opcionálisak, és egy függvény egy vagy több bemeneti, illetve kimeneti kötéssel is rendelkezhet.

Az eseményindítók és kötések segítségével elkerülheti a más szolgáltatásokhoz való szoftveres hozzáférést. A függvény függvényparaméterekben kapja meg az adatokat (például egy üzenetsor üzenetének tartalmát). Az adatokat (például egy üzenetsor üzenetének tartalmát) a függvény által visszaadott értékek használatával küldheti el. 

Tekintse meg az alábbi példákat a különböző függvények implementáljanak.

| Példaforgatókönyv | Eseményindító | Bemeneti kötés | Kimeneti kötés |
|-------------|---------|---------------|----------------|
| Új üzenetsor-üzenet érkezik, amely egy függvényt futtat egy másik üzenetsorba való íráshoz. | Várólista<sup>*</sup> | *Nincs* | Várólista<sup>*</sup> |
|Az ütemezett feladat beolvassa Blob Storage tartalmát, és létrehoz egy Cosmos DB dokumentumot. | Időzítő | Blob Storage | Cosmos DB |
|A Event Grid egy kép beolvassa a Blob Storage és egy dokumentumot a Cosmos DB e-mail küldését. | Event Grid | Blob Storage és Cosmos DB | SendGrid |
| Egy olyan webhook, amely Microsoft Graph Excel-munkalapok frissítéséhez. | HTTP | *Nincs* | Microsoft Graph |

<sup>\*</sup> Különböző üzenetsorokat képvisel

Ezek a példák nem teljes körűek, de bemutatják, hogyan használhatók együtt az eseményindítók és kötések.

###  <a name="trigger-and-binding-definitions"></a>Trigger- és kötésdefiníciók

Az eseményindítók és kötések meghatározása a fejlesztési nyelvtől függően eltérő.

| Nyelv | Az eseményindítókat és kötéseket a következő konfigurálja: |
|-------------|--------------------------------------------|
| C#-osztálytár | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;metódusok és paraméterek dekorálása C#-attribútumokkal |
| Java | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;metódusok és paraméterek dekorálása Java-jegyzetekkel  | 
| JavaScript/PowerShell/Python/TypeScript | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a [function.jsfrissítése](./functions-reference.md) ([séma](http://json.schemastore.org/function)) |

Az integrációs function.jshasznált nyelvekhez a portál egy felhasználói felületet biztosít **kötések hozzáadásához** az Integráció lapon. A fájlt közvetlenül a portálon is szerkesztheti a függvény **Kód + teszt** lapján. Visual Studio Code lehetővé teszi, hogy könnyedén adjon hozzá kötést egy [fájlon function.js](functions-develop-vs-code.md?tabs=nodejs#add-a-function-to-your-project) egy fájlban található fájlhoz egy kényelmes üzenetkészlet használatával. 

A .NET és a Java paramétertípusa határozza meg a bemeneti adatok adattípusát. A használatával például kötést hozhat létre egy üzenetsor-eseményindító szöveghez, egy binárisként beolvasott bájttömbhöz, valamint egy egyéni típushoz, amely egy objektumra `string` deszerializálható. Mivel a .NET-osztálytár függvényei és  a Java-függvények nem függnek afunction.jsa kötésdefiníciókhoz, nem lehet őket létrehozni és szerkeszteni a portálon. A C#-portál szerkesztése c#-szkripten alapul, amelyfunction.jshelyett a *attribútumokat* használja.

További információ a kötések meglévő függvényekbe való hozzáadásáról: Függvények csatlakoztatása Azure-szolgáltatásokhoz [kötések használatával.](add-bindings-existing-function.md)

A dinamikusan begépelt nyelvek, például a JavaScript esetében használja a fájlfunction.js`dataType` *tulajdonságát.* Ha például egy HTTP-kérés tartalmát bináris formátumban olvassa be, állítsa a `dataType` `binary` következőre:

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

A további lehetőségei `dataType` a és `stream` a `string` .

## <a name="binding-direction"></a>Kötés iránya

Minden eseményindító és kötés tulajdonsága a fájl `direction` [function.jstulajdonsága:](./functions-reference.md)

- Eseményindítóknál az irány mindig `in`
- A bemeneti és kimeneti kötések a és a `in``out`
- Egyes kötések speciális irányt `inout` támogatnak. Ha a et használja, csak a Speciális szerkesztő érhető el a portál `inout` **Integrálás**  lapján.

Ha egy [osztálytárban](functions-dotnet-class-library.md) attribútumokkal konfigurálja az eseményindítókat és kötéseket, az irány egy attribútum konstruktorában van megadva, vagy a paramétertípusból következik.

## <a name="add-bindings-to-a-function"></a>Kötések hozzáadása függvényhez

A függvényt csatlakoztathatja más szolgáltatásokhoz bemeneti vagy kimeneti kötések használatával. Kötés hozzáadásához adja hozzá az adott definícióit a függvényhez. További információ: [Kötések hozzáadása meglévő függvényhez a Azure Functions.](add-bindings-existing-function.md)  

## <a name="supported-bindings"></a>Támogatott kötések

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Az előzetes verzióban elérhető vagy éles használatra jóváhagyott kötésekkel kapcsolatos információkért lásd: [Támogatott nyelvek.](supported-languages.md)

## <a name="bindings-code-examples"></a>Példák kötéskódra

Az alábbi táblázatban konkrét kötéstípusokra talál példákat, amelyek bemutatják, hogyan használhatja a kötéseket a függvényben. Először válassza ki a projektnek megfelelő nyelvi lapot. 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="custom-bindings"></a>Egyéni kötések

Létrehozhat egyéni bemeneti és kimeneti kötéseket. A kötéseket .NET-en kell szerzői, de bármely támogatott nyelvből használhatók. További információ az egyéni kötések létrehozásáról: Egyéni bemeneti és kimeneti [kötések létrehozása.](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings)

## <a name="resources"></a>Források
- [Kifejezések és minták kötése](./functions-bindings-expressions-patterns.md)
- [Az Azure-függvény visszatérési értékének használata](./functions-bindings-return-value.md)
- [Kötéskifejezés regisztrálása](./functions-bindings-register.md)
- Vizsgálat:
  - [Kódtesztelési stratégiák az Azure Functions szolgáltatásban](functions-test-a-function.md)
  - [Nem HTTP által aktivált függvény manuális futtatása](functions-manually-run-non-http.md)
- [Kötési hibák kezelése](./functions-bindings-errors.md)

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Kötési Azure Functions regisztrálása](./functions-bindings-register.md)
