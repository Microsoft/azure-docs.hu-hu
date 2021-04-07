---
title: Példány és hitelesítés beállítása (PowerShell)
titleSuffix: Azure Digital Twins
description: 'Lásd: az Azure Digital Twins szolgáltatás példányának beállítása Azure PowerShell használatával'
author: baanders
ms.author: baanders
ms.date: 12/16/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 024b238ef9a6330831ae6cf4dcd6bb72d72dcc74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98044271"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-powershell"></a>Azure digitális Twins-példány és-hitelesítés beállítása (PowerShell)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Ez a cikk az **új Azure Digital Twins-példány beállításának** lépéseit ismerteti, beleértve a példány létrehozását és a hitelesítés beállítását. A cikk elvégzése után egy Azure Digital Twins-példánnyal kell elkezdenie a programozást.

A cikk jelen verziója ezeket a lépéseket manuálisan, egyenként, a [Azure PowerShell](/powershell/azure/new-azureps-module-az)használatával hajtja végre.

* Ha ezeket a lépéseket manuálisan szeretné elvégezni a Azure Portal használatával, tekintse meg a jelen cikk portáljának verzióját: [*útmutató: példány és hitelesítés beállítása (portál)*](how-to-set-up-instance-portal.md).
* Ha az üzembe helyezési parancsfájl mintájának használatával szeretne automatikus telepítést végezni, tekintse meg a jelen cikk parancsfájlokkal ellátott verzióját: [*útmutató: példány és hitelesítés beállítása (megírt)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prepare-your-environment"></a>A környezet előkészítése

1. Először válassza ki a cikkben szereplő parancsok futtatásának helyét. Azure PowerShell parancsokat a Azure PowerShell helyi telepítésével vagy a böngészőablakban [Azure Cloud Shell](https://shell.azure.com)használatával futtathat.
    * Ha a Azure PowerShell helyi használatát választja:
       1. [Telepítse az az PowerShell-modult](/powershell/azure/install-az-ps).
       1. Nyisson meg egy PowerShell-ablakot a gépen.
       1. Kapcsolódjon az Azure-fiókjához a [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmag használatával.
    * Ha a Azure Cloud Shell használatát választja:
        1. További információ a Cloud Shellról: [Azure Cloud Shell áttekintése](../cloud-shell/overview.md) .
        1. Nyisson meg egy Cloud Shell ablakot a böngészőben a [hivatkozást](https://shell.azure.com) követve.
        1. Győződjön meg arról, hogy a Cloud Shell a PowerShell-verzió futtatására van beállítva a Cloud Shell ikon sávján.
    
            :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="Cloud Shell a PowerShell-verzió kijelölését bemutató ablak":::
    
1. Ha több Azure-előfizetéssel rendelkezik, válassza ki a megfelelő előfizetést, amelyben az erőforrásokat számlázni kell. Válasszon ki egy adott előfizetést a [set-AzContext](/powershell/module/az.accounts/set-azcontext) parancsmag használatával.

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. Ha első alkalommal használja az Azure digitális Twins szolgáltatást ezzel az előfizetéssel, regisztrálnia kell a **Microsoft. DigitalTwins** erőforrás-szolgáltatót. (Ha nem biztos abban, hogy a múltban is elvégezte a futtatást, akkor is.)

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.DigitalTwins
   ```

1. Az az **. DigitalTwins** PowerShell-modul telepítéséhez használja az alábbi parancsot.
    ```azurepowershell-interactive
    Install-Module -Name Az.DigitalTwins
    ```

> [!IMPORTANT]
> Az az **. DigitalTwins** PowerShell-modul előzetes verzióban érhető el, és a `Install-Module` fent leírtak szerint külön kell telepítenie a parancsmaggal. Miután ez a PowerShell-modul általánosan elérhetővé válik, a jövőbeli Az PowerShell modulkiadások részévé válik, és natívan elérhető lesz az Azure Cloud Shellből.

## <a name="create-the-azure-digital-twins-instance"></a>Az Azure Digital Twins-példány létrehozása

Ebben a szakaszban az **Azure Digital Twins új példányát fogja létrehozni** Azure PowerShell használatával.
A következőket kell megadnia:

* Egy [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) , amelyben a példány üzembe lesz helyezve. Ha még nem rendelkezik meglévő erőforráscsoporthoz, létrehozhat egyet a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmag használatával:

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <name-for-your-resource-group> -Location <region>
  ```

* Egy régió az üzemelő példányhoz. Ha szeretné megtekinteni, hogy mely régiók támogatják az Azure Digital Twins-t, látogasson el az [*Azure-termékek területre*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* A példány neve. Az új példány nevének a régión belül egyedinek kell lennie az előfizetéshez. Ha az előfizetése egy másik Azure Digital Twins-példánnyal rendelkezik abban a régióban, amely már a megadott nevet használja, a rendszer arra kéri, hogy válasszon másik nevet.

A példány létrehozásához használja a következő parancs értékeit:

```azurepowershell-interactive
New-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> -Location <region>
```

### <a name="verify-success-and-collect-important-values"></a>Sikeres ellenőrzés és fontos értékek összegyűjtése

Ha a példány sikeresen létrejött, az eredmény hasonló a létrehozott erőforrással kapcsolatos információkat tartalmazó következő kimenethez:

```Output
Location   Name                                         Type
--------   ----                                         ----
<region>   <name-for-your-Azure-Digital-Twins-instance> Microsoft.DigitalTwins/digitalTwinsInstances
```

Ezután jelenítse meg az új példány tulajdonságait a (z) és a (z) parancs futtatásával a következőhöz `Get-AzDigitalTwinsInstance` `Select-Object -Property *` hasonló módon:

```azurepowershell-interactive
Get-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> |
  Select-Object -Property *
```

> [!TIP]
> Ezzel a paranccsal bármikor megtekintheti a példány összes tulajdonságát.

Jegyezze fel az Azure digitális ikrek példányának **állomásnév**-, **név**-és **ResourceGroup**. Ezek olyan fontos értékek, amelyekre szüksége lehet, ha folytatja az Azure Digital Twins-példánnyal való munkavégzést, a hitelesítés beállítását, valamint a kapcsolódó Azure-erőforrásokat. Ha más felhasználók is programozást végeznek a példányon, ezeket az értékeket meg kell osztani velük.

Most már rendelkezik egy Azure Digital Twins-példánnyal, amely készen áll a használatra. Ezután meg kell adnia a megfelelő Azure-felhasználói engedélyeket a kezeléséhez.

## <a name="set-up-user-access-permissions"></a>Felhasználói hozzáférési engedélyek beállítása

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Először határozza meg annak a felhasználónak az Azure AD-fiókjához tartozó **ObjectId** , amelyet hozzá kell rendelni a szerepkörhöz. Ezt az értéket a [Get-AzAdUser](/powershell/module/az.resources/get-azaduser) parancsmaggal érheti el, ha az Azure ad-fiók egyszerű felhasználónevét adja meg a ObjectId (és más felhasználói adatok) lekéréséhez. A legtöbb esetben az egyszerű felhasználónév megegyezik a felhasználó e-mail-címével az Azure AD-fiókban.

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName <Azure-AD-user-principal-name-of-user-to-assign>
```

Ezután a következő parancs **ObjectId** használatával rendelje hozzá a szerepkört. A parancshoz meg kell adnia a példány létrehozásakor korábban kiválasztott előfizetés-azonosítót, erőforráscsoport-nevet és Azure digitális Twins-példány nevét is. A parancsot a [megfelelő engedélyekkel](#prerequisites-permission-requirements) rendelkező felhasználónak kell futtatnia az Azure-előfizetésben.

```azurepowershell-interactive
$Params = @{
  ObjectId = '<Azure-AD-user-object-id-of-user-to-assign>'
  RoleDefinitionName = 'Azure Digital Twins Data Owner'
  Scope = '/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<name-for-your-Azure-Digital-Twins-instance>'
}
New-AzRoleAssignment @Params
```

Ennek a parancsnak az eredménye a létrehozott szerepkör-hozzárendeléssel kapcsolatos információ.

### <a name="verify-success"></a>Sikeres ellenőrzés

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Most már rendelkezik egy Azure Digital Twins-példánnyal, amely készen áll a használatra, és hozzárendelt engedélyekkel rendelkezik a kezeléséhez.

## <a name="next-steps"></a>Következő lépések

Tekintse meg, hogyan csatlakoztatható ügyfélalkalmazás a példányhoz hitelesítési kóddal:
* [*Útmutató: az alkalmazás-hitelesítési kód írása*](how-to-authenticate-client.md)
