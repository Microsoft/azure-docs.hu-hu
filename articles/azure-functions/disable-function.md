---
title: Függvények letiltása a Azure Functionsban
description: Megtudhatja, hogyan tilthatja le és engedélyezheti a függvényeket a Azure Functionsban.
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 1ad484804f66a2e2d4d0f1da4a37cf0d6c485f38
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584737"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Függvények letiltása a Azure Functionsban

Ez a cikk azt ismerteti, hogyan lehet letiltani egy függvényt a Azure Functionsban. A függvények *letiltásához* azt jelenti, hogy a futtatókörnyezet figyelmen kívül hagyja a függvényhez definiált automatikus triggert. Ezzel megakadályozhatja, hogy egy adott függvény a teljes Function app leállítása nélkül fusson.

A függvények letiltásának ajánlott módja a következő formátumban beállított alkalmazás beállítása: `AzureWebJobs.<FUNCTION_NAME>.Disabled` `true` . Az Alkalmazásbeállítások számos módon hozhatók létre és módosíthatók, például az [Azure CLI](/cli/azure/) használatával és a függvény **áttekintés** lapjának [Azure Portal](https://portal.azure.com). 

> [!NOTE]  
> Ha letilt egy HTTP által aktivált függvényt a jelen cikkben ismertetett módszerekkel, a végpont továbbra is elérhető, ha a helyi számítógépen fut.  

## <a name="disable-a-function"></a>Függvény letiltása

# <a name="portal"></a>[Portál](#tab/portal)

Használja az **Engedélyezés** és **Letiltás** gombokat a függvény **Áttekintés** lapján. Ezek a gombok az Alkalmazásbeállítások értékének módosításával működnek `AzureWebJobs.<FUNCTION_NAME>.Disabled` . Ez a függvény-specifikus beállítás az első letiltásakor jön létre. 

![Függvény állapotának kapcsolója](media/disable-function/function-state-switch.png)

Még ha egy helyi projektből is közzéteszi a Function alkalmazást, továbbra is használhatja a portált a függvények letiltásához a Function alkalmazásban. 

> [!NOTE]  
> A portálon integrált tesztelési funkció figyelmen kívül hagyja a `Disabled` beállítást. Ez azt jelenti, hogy egy letiltott függvény továbbra is fut, amikor a portálon a **teszt** ablakból indult el. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Az Azure CLI-ben a [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) paranccsal hozhatja létre és módosíthatja az alkalmazás beállításait. A következő parancs letiltja a nevű függvényt egy `QueueTrigger` nevű Alkalmazásbeállítás létrehozásával `AzureWebJobs.QueueTrigger.Disabled` `true` . 

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

A függvény újbóli engedélyezéséhez futtassa újra a parancsot egy értékkel `false` .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

A [`Update-AzFunctionAppSetting`](/powershell/module/az.functions/update-azfunctionappsetting) parancs egy Alkalmazásbeállítás hozzáadására vagy frissítésére szolgál. A következő parancs letiltja a nevű függvényt egy `QueueTrigger` nevű Alkalmazásbeállítás létrehozásával `AzureWebJobs.QueueTrigger.Disabled` `true` . 

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "true"}
```

A függvény újbóli engedélyezéséhez futtassa újra a parancsot egy értékkel `false` .

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "false"}
```
---

## <a name="functions-in-a-slot"></a>Egy tárolóhelyen lévő függvények

Alapértelmezés szerint az Alkalmazásbeállítások az üzembe helyezési tárolóhelyeken futó alkalmazásokra is érvényesek. A tárolóhely által használt alkalmazás-beállításokat azonban felülbírálhatja, ha beállítja a tárolóhely-specifikus alkalmazás beállítását. Előfordulhat például, hogy egy függvényt éles környezetben kell aktív, de nem az üzembe helyezési teszt során, például egy időzítő által aktivált függvényt. 

Függvény letiltása csak az átmeneti tárolóhelyen:

# <a name="portal"></a>[Portál](#tab/portal)

Navigáljon a Function app slot-példányára úgy, hogy kijelöli **az üzembe helyezési pontok lehetőséget az** **üzembe helyezés** területen, kiválasztja a tárolóhelyet, és kiválasztja a **függvények** elemet a tárolóhely-példányban  Válassza ki a függvényt, majd használja a funkció **Áttekintés** lapjának **Engedélyezés** és **Letiltás** gombját. Ezek a gombok az Alkalmazásbeállítások értékének módosításával működnek `AzureWebJobs.<FUNCTION_NAME>.Disabled` . Ez a függvény-specifikus beállítás az első letiltásakor jön létre. 

Közvetlenül is hozzáadhatja a nevű alkalmazás-beállítást a `AzureWebJobs.<FUNCTION_NAME>.Disabled` (z) értékkel a `true` tárolóhely-példány **konfigurációjában** . Ha egy adott tárolóhelyre vonatkozó alkalmazást ad hozzá, ügyeljen rá, hogy ellenőrizze az **üzembe helyezési pont beállítása** mezőt. Ez fenntartja a beállítási értéket a tárolóhelyre a swapok során.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=true
```
A függvény újbóli engedélyezéséhez futtassa újra a parancsot egy értékkel `false` .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

A Azure PowerShell jelenleg nem támogatja ezt a funkciót.

---

További információ: [Azure functions üzembe helyezési](functions-deployment-slots.md)pontok.

## <a name="localsettingsjson"></a>local.settings.json

A függvények a helyileg futtatott módon is letilthatók. Ha le szeretne tiltani egy nevű függvényt `HttpExample` , adjon hozzá egy bejegyzést a local.settings.jsfájljában található értékek gyűjteményéhez, a következőképpen:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "UseDevelopmentStorage=true", 
    "AzureWebJobs.HttpExample.Disabled": "true"
  }
}
``` 

## <a name="other-methods"></a>Egyéb módszerek

Míg az alkalmazás-beállítási módszer minden nyelvhez és az összes futásidejű verzióhoz ajánlott, számos más módon is letilthatja a függvényeket. Ezeket a metódusokat, amelyek nyelvtől és futtatókörnyezettől függően változnak, a visszamenőleges kompatibilitás érdekében tartanak fenn. 

### <a name="c-class-libraries"></a>C# szintű kódtárak

A Class Library függvényben az attribútum segítségével is `Disable` megakadályozhatja a függvény aktiválását. Ez az attribútum lehetővé teszi a függvény letiltására használt beállítás nevének testreszabását. Használja az attribútum azon verzióját, amely lehetővé teszi egy logikai alkalmazás-beállításra hivatkozó konstruktor paraméter definiálását, ahogy az az alábbi példában is látható:

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Ezzel a módszerrel engedélyezheti és letilthatja a függvényt úgy, hogy az újrafordítás vagy az újbóli telepítés nélkül megváltoztatja az alkalmazás beállítását. Az Alkalmazásbeállítások módosítása a Function alkalmazás újraindítását eredményezi, így a letiltott állapot változása azonnal felismerhető.

Létezik olyan konstruktor is a paraméterhez, amely nem fogad el karakterláncot a beállítás nevéhez. Az attribútum ezen verziója nem ajánlott. Ha ezt a verziót használja, újra kell fordítania és újra kell telepítenie a projektet a függvény letiltott állapotának módosításához.

### <a name="functions-1x---scripting-languages"></a>Függvények 1. x – parancsfájlkezelési nyelvek

Az 1. x verzióban a `disabled` fájl *function.js* tulajdonságával is megadhatja, hogy a futtatókörnyezet ne indítson el egy függvényt. Ez a metódus csak olyan programozási nyelveken működik, mint a C# parancsfájl és a JavaScript. A tulajdonság beállítható a következőre `disabled` `true` : vagy egy alkalmazás-beállítás neve:

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
vagy 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

A második példában a függvény le van tiltva, ha van egy IS_DISABLED nevű alkalmazás-beállítás, és a értéke `true` vagy 1.

>[!IMPORTANT]  
>A portál Alkalmazásbeállítások használatával letiltja a v1. x függvényeket. Ha egy alkalmazás beállítása ütközik a fájl function.jsával, hiba léphet fel. A hibák elkerülése érdekében távolítsa el a `disabled` tulajdonságot a fájl function.jsból. 


## <a name="next-steps"></a>Következő lépések

Ez a cikk az automatikus eseményindítók letiltását ismerteti. További információ az eseményindítókkal kapcsolatban: [triggerek és kötések](functions-triggers-bindings.md).
