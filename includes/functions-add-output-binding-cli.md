---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 7ef3bd0f401ba54d56ed42df34cd2e761681dbc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96904071"
---
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="add-an-output-binding-definition-to-the-function"></a>Kimeneti kötési definíció hozzáadása a függvényhez

Bár a függvények csak egy triggerrel rendelkezhetnek, több bemeneti és kimeneti kötéssel is rendelkezhet, amelyek lehetővé teszik más Azure-szolgáltatásokhoz és-erőforrásokhoz való csatlakozást az egyéni integrációs kód írása nélkül. 
::: zone-end
::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Ezeket a kötéseket a Function mappában található fájl *function.js* deklarálhatja. Az előző rövid útmutatóból a *HttpExample* mappában található fájl *function.js* két kötést tartalmaz a `bindings` gyűjteményben:  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
Minden kötéshez legalább egy típus, egy irány és egy név tartozik. A fenti példában az első kötés típusa az `httpTrigger` irány `in` . Az `in` iránynál `name` adja meg egy bemeneti paraméter nevét, amelyet a rendszer az eseményindító által meghívott függvénynek továbbít.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
A gyűjtemény második kötésének neve `res` . Ez a `http` kötés egy kimeneti kötés ( `out` ), amely a http-válasz írására szolgál. 

Ha a függvényből szeretne írni egy Azure Storage-várólistába, adjon hozzá egy `out` típusú kötést a következő `queue` `msg` kódban látható módon:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
A gyűjtemény második kötése `http` az iránysal van `out` megadva, ebben az esetben a speciális `name` `$return` érték azt jelzi, hogy ez a kötés a függvény visszatérési értékét használja, nem pedig bemeneti paramétert.

Ha a függvényből szeretne írni egy Azure Storage-várólistába, adjon hozzá egy `out` típusú kötést a következő `queue` `msg` kódban látható módon:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
A gyűjtemény második kötésének neve `res` . Ez a `http` kötés egy kimeneti kötés ( `out` ), amely a http-válasz írására szolgál. 

Ha a függvényből szeretne írni egy Azure Storage-várólistába, adjon hozzá egy `out` típusú kötést a következő `queue` `msg` kódban látható módon:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Ebben az esetben `msg` a függvény kimeneti argumentumként van megadva. A `queue` típushoz meg kell adnia a várólista nevét is, és meg kell `queueName` adnia az Azure Storage-kapcsolatok *nevét* ( *local.settings.json*) `connection` . 
::: zone-end  
