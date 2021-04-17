---
title: Függvények letiltása a Azure Functions
description: Megtudhatja, hogyan tilthatja le és engedélyezheti a függvényeket a Azure Functions.
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: ef3886c4b9b73f87238bf386d1320ecbac8ad181
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374896"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Függvények letiltása a Azure Functions

Ez a cikk bemutatja, hogyan tilthatja le a függvényeket a Azure Functions. Egy *függvény* letiltásához a futásidőt figyelmen kívül kell hagyni a függvényhez definiált automatikus eseményindítóval. Ez lehetővé teszi egy adott függvény futtatásának megakadályozását a teljes függvényalkalmazás leállítása nélkül.

A függvények letiltására az ajánlott módszer, ha egy alkalmazásbeállítás formátuma `AzureWebJobs.<FUNCTION_NAME>.Disabled` a következőre van állítva: `true` . Ezt az alkalmazásbeállítást többféleképpen is létrehozhatja és módosíthatja, többek között  az [Azure CLI](/cli/azure/) használatával, valamint a függvény Áttekintés lapján a [Azure Portal.](https://portal.azure.com) 

> [!NOTE]  
> Ha letilt egy HTTP-eseményindítóval aktivált függvényt a cikkben ismertetett módszerekkel, a végpont elérhető marad a helyi számítógépen való futtatáskor.  

## <a name="disable-a-function"></a>Függvény letiltása

# <a name="portal"></a>[Portál](#tab/portal)

Használja **az Engedélyezés** és a **Letiltás** gombot a függvény **Áttekintés lapján.** Ezek a gombok az alkalmazásbeállítás értékének módosításával `AzureWebJobs.<FUNCTION_NAME>.Disabled` működnek. Ez a függvényspecifikus beállítás az első letiltáskor jön létre. 

![Függvényállapot-kapcsoló](media/disable-function/function-state-switch.png)

Akkor is letilthatja a függvényeket a portálon, ha egy helyi projektből teszi közzé a függvényalkalmazásban. 

> [!NOTE]  
> A portállal integrált tesztelési funkció figyelmen kívül hagyja a `Disabled` beállítást. Ez azt jelenti, hogy a letiltott  függvény továbbra is fut, amikor a portál Teszt ablakából indult el. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Az Azure CLI-ban az paranccsal hozhatja létre és [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) módosíthatja az alkalmazásbeállítást. A következő parancs egy nevű függvény letiltásához létrehoz egy nevű `QueueTrigger` alkalmazásbeállítást, és `AzureWebJobs.QueueTrigger.Disabled` a következőre be van omkantva: `true` . 

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

A függvény újra engedélyezéséhez futtassa újra ugyanazt a parancsot `false` értékkel.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

A [`Update-AzFunctionAppSetting`](/powershell/module/az.functions/update-azfunctionappsetting) parancs hozzáad vagy frissíti az alkalmazásbeállítást. A következő parancs letilt egy nevű függvényt egy nevű alkalmazásbeállítás létrehozásával és a `QueueTrigger` `AzureWebJobs.QueueTrigger.Disabled` `true` beállításával. 

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "true"}
```

A függvény újra engedélyezéséhez futtassa újra ugyanazt a parancsot a `false` értékével.

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "false"}
```
---

## <a name="functions-in-a-slot"></a>Függvények egy tárolóhelyen

Alapértelmezés szerint az alkalmazásbeállítások az üzembe helyezési tárolóhelyeken futó alkalmazásokra is érvényesek. A tárolóhely által használt alkalmazásbeállítást azonban felülbírálhatja egy tárolóhely-specifikus alkalmazásbeállítás beállításával. Előfordulhat például, hogy azt szeretné, hogy egy függvény éles környezetben aktív legyen, de ne az üzembe helyezés tesztelése során, például egy időzítő által aktivált függvényben. 

Ha csak az előkészítési ponton szeretné letiltani a függvényt:

# <a name="portal"></a>[Portál](#tab/portal)

Navigáljon a függvényalkalmazás pontpéldányához úgy, hogy az Üzembe helyezés alatt kiválasztja az Üzembe helyezési pontokat, majd kiválasztja a pontpéldány **Functions** (Függvények) beállítását.    Válassza ki a függvényt, majd használja az **Engedélyezés** **és** a Letiltás gombot a függvény **Áttekintés lapján.** Ezek a gombok az alkalmazásbeállítás értékének módosításával `AzureWebJobs.<FUNCTION_NAME>.Disabled` működnek. Ez a függvényspecifikus beállítás az első letiltáskor jön létre. 

Közvetlenül is hozzáadhatja a nevű alkalmazásbeállítást a értékével a `AzureWebJobs.<FUNCTION_NAME>.Disabled` `true` **tárolóhelypéldány** konfigurációjában. Ha tárolóhely-specifikus alkalmazásbeállítást ad meg, mindenképpen jelölje be az Üzembe **helyezési hely beállítás jelölőnégyzetet.** Ez megtartja a beállítás értékét a felcserélések során a tárolóhelyen.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=true
```
A függvény újra engedélyezéséhez futtassa újra ugyanazt a parancsot a `false` értékével.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell jelenleg nem támogatja ezt a funkciót.

---

További tudnivalókért lásd az [üzembehely Azure Functions üzembe helyezési helyeket.](functions-deployment-slots.md)

## <a name="localsettingsjson"></a>local.settings.json

A függvények ugyanúgy letilthatók, mint a helyi futtatáskor. Egy nevű függvény letiltásához adjon hozzá egy bejegyzést a fájlban local.settings.js`HttpExample` értékek gyűjteményéhez a következőképpen:

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

Osztálytári függvényben a attribútummal is megakadályozhatja a `Disable` függvény aktiválását. Ez az attribútum lehetővé teszi a függvény letiltásához használt beállítás nevének testreszabását. Használja a attribútum verzióját, amellyel definiálhat egy logikai alkalmazásbeállításra hivatkozó konstruktorparamétert az alábbi példában látható módon:

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

Ez a metódus lehetővé teszi a függvény engedélyezését és letiltását az alkalmazás beállításának módosításával, újrakompatinálás vagy ismételt üzembe ásás nélkül. Ha módosít egy alkalmazásbeállítást, a függvényalkalmazás újraindul, így a letiltott állapotváltozást a rendszer azonnal felismeri.

A paraméterhez egy konstruktor is rendelkezésre van, amely nem fogad el sztringet a beállítás neveként. Az attribútum ezen verziója nem ajánlott. Ha ezt a verziót használja, a függvény letiltott állapotának a módosítása érdekében újra le kell egészülnie és újra kell üzembe kell nie a projektet.

### <a name="functions-1x---scripting-languages"></a>Functions 1.x – parancsnyelvek

Az 1.x verzióban a fájlban találhatófunction.jsis megszabadhatja, hogy a futásidő ne aktiválja `disabled` a függvényt.  Ez a metódus csak olyan parancsnyelvekkel működik, mint a C#-szkript és a JavaScript. A `disabled` tulajdonság beállítható vagy az `true` alkalmazásbeállítás nevére:

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
