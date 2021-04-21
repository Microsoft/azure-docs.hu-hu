---
title: Futásidejű Azure Functions megcélzott verziója
description: Azure Functions a futtatás több verzióját is támogatja. Megtudhatja, hogyan adhatja meg az Azure-ban üzemeltetett függvényalkalmazás futásidejű verzióját.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 9a9f8fca1c39fd0251df3e3a8da3d789aae4d3d6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779272"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Futásidejű Azure Functions megcélzott verziója

A függvényalkalmazás az alkalmazás egy adott verzióján Azure Functions fut. Három főverzió van: [3.x, 2.x és 1.x.](functions-versions.md) Alapértelmezés szerint a függvényalkalmazások a futtatás 3.x verziójában vannak létrehozva. Ez a cikk azt ismerteti, hogyan konfigurálhat egy függvényalkalmazást az Azure-ban a választott verzión való futtatáshoz. A helyi fejlesztési környezet egy adott verzióhoz való konfigurálásról további információért lásd: Kód és tesztkörnyezet [Azure Functions helyileg.](functions-run-local.md)

Az, hogy hogyan céloz meg manuálisan egy adott verziót, attól függ, hogy Windowst vagy Linuxot futtat.

## <a name="automatic-and-manual-version-updates"></a>Automatikus és manuális verziófrissítések

_Ez a szakasz nem érvényes a függvényalkalmazás Linux rendszeren [való futtatásakor.](#manual-version-updates-on-linux)_

Azure Functions lehetővé teszi, hogy a windowsos futtatás egy adott verzióját célozza meg `FUNCTIONS_EXTENSION_VERSION` egy függvényalkalmazás alkalmazásbeállításával. A függvényalkalmazás a megadott főverzión marad, amíg Ön kifejezetten nem dönt úgy, hogy új verzióra lép. Ha csak a főverziót adja meg, a függvényalkalmazás automatikusan frissül a futásidő új alverzióira, amikor azok elérhetővé válnak. Az új alverziók nem vezetnek be a legfrissebb változásokhoz. 

Ha alverziót (például "2.0.12345") ad meg, a függvényalkalmazás erre a verzióra lesz rögzítve, amíg explicit módon nem módosítja. A régebbi alverziókat a rendszer rendszeresen eltávolítja az éles környezetből. Ha az alverzió el lesz távolítva, a függvényalkalmazás visszatér a legújabb verzióra, és nem a-ban beállított `FUNCTIONS_EXTENSION_VERSION` verzióra. Ezért gyorsan meg kell oldania a függvényalkalmazás adott alverziót igénylő problémáit. Ezután visszatérhet a főverzióra. Az alverziók eltávolítását a App Service [jelentették be.](https://github.com/Azure/app-service-announcements/issues)

> [!NOTE]
> Ha a Azure Functions egy adott főverziója számára rögzít, majd az Visual Studio használatával próbál meg közzétenni az Azure-ban, megjelenik egy párbeszédpanel, amely felszólítja, hogy frissítsen a legújabb verzióra, vagy törölje a közzétételt. Ennek elkerülése érdekében adja hozzá a `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` tulajdonságot a `.csproj` fájlhoz.

Amikor egy új verzió nyilvánosan elérhetővé válik, a portálon megjelenő üzenet lehetőséget ad arra, hogy továbblépjon erre a verzióra. Az új verzióra való áthelyezést követően mindig használhatja az alkalmazásbeállítást egy `FUNCTIONS_EXTENSION_VERSION` korábbi verzióra való visszaléptethez.

Az alábbi táblázatban az `FUNCTIONS_EXTENSION_VERSION` egyes főverziók értékei láthatóak az automatikus frissítések engedélyezéséhez:

| Főverzió | `FUNCTIONS_EXTENSION_VERSION` Érték |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2.x  | `~2` |
| 1.x  | `~1` |

A futásidejű verzió módosítása a függvényalkalmazás újraindítását okozza.

>[!NOTE]
>A .NET-függvényalkalmazások úgy rögzítettek, hogy ne frissítsünk automatikusan `~2.0` a .NET Core 3.1-re. További tudnivalókért lásd a [Functions v2.x megfontolandó szempontjait.](functions-dotnet-class-library.md#functions-v2x-considerations)  

## <a name="view-and-update-the-current-runtime-version"></a>Az aktuális futásidejű verzió megtekintése és frissítése

_Ez a szakasz nem vonatkozik a függvényalkalmazás Linux rendszeren [való futtatásakor.](#manual-version-updates-on-linux)_

Módosíthatja a függvényalkalmazás által használt futásidejű verziót. Az esetlegesen jelentős változások miatt a futásidejű verziót csak akkor módosíthatja, mielőtt függvényeket hoz létre a függvényalkalmazásban. 

> [!IMPORTANT]
> Bár a futásidejű verziót a beállítás határozza meg, ezt a változtatást csak a Azure Portal, a beállítás `FUNCTIONS_EXTENSION_VERSION` közvetlen módosításával ne. Ennek az az oka, hogy a portál érvényesíti a módosításokat, és szükség szerint más kapcsolódó módosításokat is végre tesz.

# <a name="portal"></a>[Portál](#tab/portal)

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> A Azure Portal nem módosíthatja a függvényeket tartalmazó függvényalkalmazás futásidejű verzióját.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

A nézetét az `FUNCTIONS_EXTENSION_VERSION` Azure CLI-ről is megtekintheti és beállíthatja.  

Az Azure CLI használatával tekintse meg az aktuális futásidejű verziót az [az functionapp config appsettings set paranccsal.](/cli/azure/functionapp/config/appsettings)

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

Ebben a kódban cserélje `<function_app>` le a helyére a függvényalkalmazás nevét. Cserélje le a helyére a függvényalkalmazás `<my_resource_group>` erőforráscsoport-nevét. 

A következő kimenetben a látható, amelyet az átláthatóság érdekében `FUNCTIONS_EXTENSION_VERSION` csonkoltunk:

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

A függvényalkalmazásban a beállítást az `FUNCTIONS_EXTENSION_VERSION` [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) paranccsal frissítheti.

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--settings FUNCTIONS_EXTENSION_VERSION=<VERSION>
```

Cserélje `<FUNCTION_APP>` le a helyére a függvényalkalmazás nevét. Cserélje le a helyére a függvényalkalmazás `<RESOURCE_GROUP>` erőforráscsoport-nevét. Emellett cserélje le `<VERSION>` a helyére a következőt: egy adott verzió, vagy `~3` , vagy `~2` `~1` .

Válassza **a Try it** in the previous code example (Próbálja ki az előző példakódban) lehetőséget a parancs Azure Cloud Shell. [](../cloud-shell/overview.md) A parancs végrehajtásához [helyileg is futtathatja](/cli/azure/install-azure-cli) az Azure CLI-t. Helyi futtatáskor először az [az login futtatásával](/cli/azure/reference-index#az_login) kell bejelentkeznie.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A Azure Functions ellenőrzéshez használja a következő parancsmagot: 

```powershell
Get-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>"
```

Cserélje `<FUNCTION_APP>` le a helyére a függvényalkalmazás és a `<RESOURCE_GROUP>` nevét. A rendszer a beállítás aktuális `FUNCTIONS_EXTENSION_VERSION` értékét a kivonattáblában visszaadja.

A Functions-futtatás módosítása a következő szkript használatával:

```powershell
Update-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>" -AppSetting @{"FUNCTIONS_EXTENSION_VERSION" = "<VERSION>"} -Force
```

Ahogy korábban, cserélje le a helyére a függvényalkalmazás nevét, a helyére pedig `<FUNCTION_APP>` `<RESOURCE_GROUP>` az erőforráscsoport nevét. Cserélje le a helyére az adott verziót vagy `<VERSION>` főverziót, például vagy `~2` `~3` . A visszaadott kivonattáblában ellenőrizheti a beállítás frissített `FUNCTIONS_EXTENSION_VERSION` értékét. 

---

A függvényalkalmazás az alkalmazásbeállítás módosítása után újraindul.

## <a name="manual-version-updates-on-linux"></a>Manuális verziófrissítések Linuxon

Ha egy Linux-függvényalkalmazást egy adott gazdaverzióhoz rögzít, a rendszerkép URL-címét a hely konfigurációja "LinuxFxVersion" mezőjében adhatja meg. Például: ha egy node 10-es függvényalkalmazást szeretnénk kitűzni a következőre: host version 3.0.13142 -

**Linux App Service-hez/rugalmas prémium alkalmazásokhoz** – Állítsa `LinuxFxVersion` a beállítását a beállításra. `DOCKER|mcr.microsoft.com/azure-functions/node:3.0.13142-node10-appservice`

**Linux-használatú alkalmazások esetén** – Állítsa `LinuxFxVersion` a beállítását a (beállítás) beállításra. `DOCKER|mcr.microsoft.com/azure-functions/mesh:3.0.13142-node10`

# <a name="portal"></a>[Portál](#tab/portal)

A függvényalkalmazások webhely-konfigurációs beállításainak megtekintése és módosítása nem támogatott a Azure Portal. Használja helyette az Azure CLI-t.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

A megtekintéséhez és `LinuxFxVersion` beállításhoz használja az Azure CLI-t.  

Az aktuális futásidejű verzió megtekintéséhez használja az [parancsot az az functionapp config show paranccsal.](/cli/azure/functionapp/config)

```azurecli-interactive
az functionapp config show --name <function_app> \
--resource-group <my_resource_group> --query 'linuxFxVersion' -o tsv
```

Ebben a kódban cserélje `<function_app>` le a helyére a függvényalkalmazás nevét. A `<my_resource_group>` helyére a függvényalkalmazás erőforráscsoportnevét is be kell helyettesíteni. A rendszer a aktuális `linuxFxVersion` értékét ad vissza.

A függvényalkalmazás `linuxFxVersion` beállításának frissítéséhez használja [az az functionapp config set parancsot.](/cli/azure/functionapp/config)

```azurecli-interactive
az functionapp config set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--linux-fx-version <LINUX_FX_VERSION>
```

Cserélje `<FUNCTION_APP>` le a helyére a függvényalkalmazás nevét. A `<RESOURCE_GROUP>` helyére a függvényalkalmazás erőforráscsoportnevét is be kell helyettesíteni. A helyére írja be egy adott `<LINUX_FX_VERSION>` kép fent leírt értékét.

Ezt a parancsot a parancs futtatásához [Azure Cloud Shell](../cloud-shell/overview.md) előző kódmintában a Try **it** (Próbálja ki) használhatja. Helyileg is használhatja az [Azure CLI-t](/cli/azure/install-azure-cli) a parancs végrehajtásához, miután az [az login](/cli/azure/reference-index#az_login) parancsot futtatta a bejelentkezéshez.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Azure PowerShell jelenleg nem használható a `linuxFxVersion` beállításhoz. Használja helyette az Azure CLI-t.

---

A függvényalkalmazás a hely konfigurációja módosítása után újraindul.

> [!NOTE]
> A használat alapján csomagként futó alkalmazások esetében egy adott rendszerképre való `LinuxFxVersion` beállítással megnőhet a hideg indítási idő. Ennek az az oka, hogy egy adott rendszerképbe való rögzítés megakadályozza, hogy a Functions használ néhány hideg indítási optimalizálást. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A 2.0-s futásidő megcélása a helyi fejlesztési környezetben](functions-run-local.md)

> [!div class="nextstepaction"]
> [A futásidejű verziók kibocsátási megjegyzései](https://github.com/Azure/azure-webjobs-sdk-script/releases)
