---
title: Azure Functions-eseményindító és -kötés – példa
description: Ismerje meg az Azure Function-kötések konfigurálását
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: c95524a5de6696bd0ffe7463451d152a9d3a19b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88205971"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Azure Functions-eseményindító és -kötés – példa

Ez a cikk bemutatja, hogyan konfigurálhat egy [triggert és kötést](./functions-triggers-bindings.md) egy Azure-függvényben.

Tegyük fel, hogy új sort szeretne írni az Azure Table Storage-ba, amikor új üzenet jelenik meg az Azure üzenetsor-tárolóban. Ez a működés megvalósítható egy Azure Queue-tárolóbeli eseményindítóval, és egy Azure Table-tárolóbeli kimeneti kötéssel. 

Ebben a forgatókönyvben a fájl *function.js* . 

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "direction": "in",
      "name": "order",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "direction": "out",
      "name": "$return",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

A tömb első eleme `bindings` a várólista-tároló triggere. A `type` és a `direction` Tulajdonságok azonosítják az triggert. A `name` tulajdonság azonosítja az üzenetsor-üzenet tartalmát fogadó Function paramétert. A figyelni kívánt várólista neve `queueName` , a kapcsolódási karakterlánc pedig a által azonosított alkalmazás-beállításban található `connection` .

A tömb második eleme `bindings` Az Azure Table Storage output kötés. A `type` és a `direction` tulajdonság azonosítja a kötést. A `name` tulajdonság azt határozza meg, hogy a függvény hogyan adja meg az új tábla sorát, ebben az esetben a függvény visszatérési értékét használva. A tábla neve a (z `tableName` ) helyen található, és a kapcsolatok karakterlánca a által azonosított alkalmazás-beállításban szerepel `connection` .

A Azure Portal *function.js* tartalmának megtekintéséhez és szerkesztéséhez kattintson a függvény **integrálás** lapján található **speciális szerkesztő** lehetőségre.

> [!NOTE]
> A értéke `connection` egy olyan Alkalmazásbeállítás neve, amely tartalmazza a kapcsolatok karakterláncát, nem maga a kapcsolatok karakterlánca. A kötések az Alkalmazásbeállítások között tárolt kapcsolati karakterláncokat használják annak érdekében, hogy a *function.json* nem tartalmazza a szolgáltatás titkos kulcsait.

## <a name="c-script-example"></a>C# parancsfájl – példa

A következő C#-szkripttel működik ezzel az triggerrel és kötéssel. Figyelje meg, hogy a várólista-üzenet tartalmát megadó paraméter neve `order` ; erre a névre van szükség, mert a `name` *function.json* tulajdonság értéke `order` 

```cs
#r "Newtonsoft.Json"

using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, ILogger log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

## <a name="javascript-example"></a>JavaScript-példa

Ugyanaz a *function.jsa* fájlon JavaScript-függvénnyel is használható:

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

## <a name="class-library-example"></a>Példa az osztály könyvtárára

Egy osztály-függvénytárban ugyanazokat az trigger-és kötési információs &mdash; várólistákat és táblázatokat, a Storage-fiókokat, a bemeneti és kimeneti függvények paramétereit a &mdash; fájl function.jshelyett attribútumok szolgáltatják. Bemutatunk egy példát:

```csharp
public static class QueueTriggerTableOutput
{
    [FunctionName("QueueTriggerTableOutput")]
    [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
    public static Person Run(
        [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order,
        ILogger log)
    {
        return new Person() {
                PartitionKey = "Orders",
                RowKey = Guid.NewGuid().ToString(),
                Name = order["Name"].ToString(),
                MobileNumber = order["MobileNumber"].ToString() };
    }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

Most már van egy működő funkciója, amelyet egy Azure-üzenetsor aktivál, és az adatokat az Azure Table Storage-ba exportálja.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Functions kötési kifejezés mintái](./functions-bindings-expressions-patterns.md)
