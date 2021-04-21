---
title: Függvények letiltása a Azure Functions
description: Megtudhatja, hogyan tilthatja le és engedélyezheti a függvényeket a Azure Functions.
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: devx-track-csharp, devx-track-azurepowershell
ms.openlocfilehash: c4743603504639cba5c48af57046179a0680b371
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829877"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Függvények letiltása a Azure Functions

Ez a cikk bemutatja, hogyan tilthatja le a függvényeket a Azure Functions. Egy *függvény* letiltásához a futásidőt figyelmen kívül kell hagyni a függvényhez definiált automatikus eseményindítóval. Ez lehetővé teszi egy adott függvény futtatásának megakadályozását a teljes függvényalkalmazás leállítása nélkül.

A függvények letiltására az ajánlott módszer, ha egy alkalmazásbeállítás `AzureWebJobs.<FUNCTION_NAME>.Disabled` formátuma a következőre van állítva: `true` . Ezt az alkalmazásbeállítást többféleképpen is létrehozhatja és módosíthatja, többek között  az [Azure CLI](/cli/azure/) használatával, valamint a függvény Áttekintés lapján a [Azure Portal.](https://portal.azure.com) 

> [!NOTE]  
> Ha letilt egy HTTP-eseményindítóval aktivált függvényt a cikkben ismertetett módszerekkel, a végpont a helyi számítógépen való futtatáskor továbbra is elérhető lehet.  

## <a name="disable-a-function"></a>Függvény letiltása

# <a name="portal"></a>[Portál](#tab/portal)

Használja az **Engedélyezés** és a **Letiltás** gombot a függvény **Áttekintés lapján.** Ezek a gombok az alkalmazásbeállítás értékének módosításával `AzureWebJobs.<FUNCTION_NAME>.Disabled` működnek. Ez a függvényspecifikus beállítás az első letiltáskor jön létre. 

![Függvényállapot-kapcsoló](media/disable-function/function-state-switch.png)

Még akkor is letilthatja a függvényeket a függvényalkalmazásban, ha egy helyi projektből teszi közzé a függvényalkalmazásban a portált. 

> [!NOTE]  
> A portállal integrált tesztelési funkció figyelmen kívül hagyja a `Disabled` beállítást. Ez azt jelenti, hogy a letiltott függvények továbbra is futnak a portál **Teszt** ablakából elindítva. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Az Azure CLI-ban az paranccsal [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set) hozhatja létre és módosíthatja az alkalmazásbeállítást. A következő parancs letilt egy nevű függvényt egy nevű alkalmazásbeállítás létrehozásával és a `QueueTrigger` `AzureWebJobs.QueueTrigger.Disabled` `true` beállításával. 

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

A függvény újra engedélyezéséhez futtassa újra ugyanazt a parancsot a `false` értékével.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

A [`Update-AzFunctionAppSetting`](/powershell/module/az.functions/update-azfunctionappsetting) parancs hozzáad vagy frissíti az alkalmazásbeállítást. A következő parancs egy nevű függvény letiltásához létrehoz egy nevű `QueueTrigger` alkalmazásbeállítást, `AzureWebJobs.QueueTrigger.Disabled` és be kell kapcsolnia a következőre: `true` . 

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "true"}
```

A függvény újra engedélyezéséhez futtassa újra ugyanazt a parancsot `false` értékkel.

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "false"}
```
---

## <a name="functions-in-a-slot"></a>Függvények egy tárolóhelyen

Alapértelmezés szerint az alkalmazásbeállítások az üzembe helyezési tárolóhelyeken futó alkalmazásokra is vonatkoznak. A tárolóhely által használt alkalmazásbeállítást azonban felülbírálhatja egy tárolóhely-specifikus alkalmazásbeállítás beállításával. Előfordulhat például, hogy azt szeretné, hogy egy függvény éles környezetben aktív legyen, de ne az üzembe helyezés tesztelése során, például egy időzítő által aktivált függvényben. 

Ha csak az előkészítési ponton szeretné letiltani a függvényt:

# <a name="portal"></a>[Portál](#tab/portal)

Navigáljon a függvényalkalmazás pontpéldányához úgy, hogy az Üzembe helyezés alatt kiválasztja az Üzembe helyezési pontokat, kiválasztja a ponthelyet, majd a **Függvények** et választja a pontpéldányban.    Válassza ki a függvényt, majd használja az **Engedélyezés** **és** a Letiltás gombot a függvény **Áttekintés lapján.** Ezek a gombok az alkalmazásbeállítás értékének módosításával `AzureWebJobs.<FUNCTION_NAME>.Disabled` működnek. Ez a függvényspecifikus beállítás az első letiltáskor jön létre. 

Közvetlenül is hozzáadhatja a nevű alkalmazásbeállítást a értékkel a `AzureWebJobs.<FUNCTION_NAME>.Disabled` `true` **konfigurációban** a slot példányhoz. Ha tárolóhely-specifikus alkalmazásbeállítást ad meg, ügyeljen arra, hogy be jelölje be az Üzembe **helyezési hely beállítás jelölőnégyzetet.** Ez megtartja a beállítás értékét a felcserélések során a tárolóhelyen.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=true
```
A függvény újra engedélyezéséhez futtassa újra ugyanazt a parancsot `false` értékkel.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell jelenleg nem támogatja ezt a funkciót.

---

További információ: [Üzembehely Azure Functions üzembe helyezési tárolóhelyek.](functions-deployment-slots.md)

## <a name="localsettingsjson"></a>local.settings.json

A függvények ugyanúgy letilthatók, mint a helyi futtatáskor. Egy nevű függvény letiltásához adjon hozzá egy bejegyzést a Fájl local.settings.js`HttpExample` értékek gyűjteményéhez a következőképpen:

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

Bár az alkalmazásbeállítási módszer minden nyelvhez és minden futásidejű verzióhoz ajánlott, a függvények letiltására többféle módszer is rendelkezésre áll. Ezek a nyelvi és futásidejű verziónként eltérő metódusok a visszamenőleges kompatibilitás érdekében megmaradnak. 

### <a name="c-class-libraries"></a>C#-osztálytárak

Osztálytári függvényben a attribútummal is megakadályozhatja a függvény `Disable` aktiválását. Ez az attribútum lehetővé teszi a függvény letiltásához használt beállítás nevének testreszabását. Használja annak az attribútumnak a verzióját, amellyel definiálhat egy logikai alkalmazásbeállításra hivatkozó konstruktorparamétert az alábbi példában látható módon:

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

Ez a módszer lehetővé teszi a függvény engedélyezését és letiltását az alkalmazás beállításának módosításával, újra- vagy újratelepítés nélkül. Az alkalmazásbeállítás módosításával a függvényalkalmazás újraindul, így a letiltott állapotváltozás azonnal felismerhető.

A paraméternek konstruktora is van, amely nem fogad el sztringet a beállítás nevéhez. Az attribútum ezen verziója nem ajánlott. Ha ezt a verziót használja, a függvény letiltott állapotának a módosítása érdekében újra le kell egészíti és újra üzembe kell azt használnia.

### <a name="functions-1x---scripting-languages"></a>Functions 1.x – parancsnyelvek

Az 1.x verzióban a fájlfunction.jstulajdonságát is használhatja, hogy a futásidő ne indítson `disabled` el függvényt.  Ez a metódus csak olyan parancsnyelvek esetében működik, mint a C#-szkript és a JavaScript. A tulajdonság beállítható vagy egy `disabled` `true` alkalmazásbeállítás nevére:

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

A második példában a függvény le van tiltva, ha 1 vagy IS_DISABLED nevű `true` alkalmazásbeállítás van.

>[!IMPORTANT]  
>A portál alkalmazásbeállításokat használ az 1.x-es függvények letiltásához. Ha egy alkalmazásbeállítás ütközik function.jsfájlban található fájllal, hiba léphet fel. A hibák elkerülése érdekében távolítsa el a `disabled` tulajdonságot function.jsfájlban található fájlból. 


## <a name="next-steps"></a>Következő lépések

Ez a cikk az automatikus eseményindítók letiltásával kapcsolatos. További információ az eseményindítókról: [Eseményindítók és kötések.](functions-triggers-bindings.md)
