---
title: Törölt alkalmazások visszaállítása
description: Útmutató törölt alkalmazások visszaállításához a Azure App Service. Kerülje a véletlenül törölt alkalmazások ne okozták a hibat.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e894e0a8bd20d6a1c3c833a4c0a3656c0dcd0f05
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833165"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Eltávolított App Service-alkalmazás visszaállítása a PowerShell használatával

Ha véletlenül törölte az alkalmazást a Azure App Service, az Az PowerShell-modul parancsaival [visszaállíthatja.](/powershell/azure/)

> [!NOTE]
> - A törölt alkalmazások a kezdeti törlést követően 30 nappal törlődnek a rendszerből. Az alkalmazások végleges kiürítés után már nem állíthatók helyre.
> - A használatban való használatra szánt csomag nem támogatja a deklúnis funkciót.
> - Az Alkalmazások Szolgáltatás egy alkalmazásban futó App Service Environment nem támogatják a pillanatképeket. Ezért a szolgáltatásban futó App Service és klónozási funkciók App Service Environment.
>

## <a name="re-register-app-service-resource-provider"></a>Erőforrás-szolgáltató App Service regisztrálása

Egyes ügyfelek olyan problémát is előfordulhatnak, amely miatt a törölt alkalmazások listájának leolvasása meghiúsul. A probléma megoldásához futtassa a következő parancsot:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Törölt alkalmazások listából

A törölt alkalmazások gyűjteményének legyűjtéséhez használhatja a következőt: `Get-AzDeletedWebApp` .

Egy adott törölt alkalmazással kapcsolatos részletekért használja a következőt:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

A részletes információk a következőket tartalmazzák:

- **DeletedSiteId:** Az alkalmazás egyedi azonosítója, amely olyan forgatókönyvekhez használatos, ahol több azonos nevű alkalmazás lett törölve
- **SubscriptionID:** A törölt erőforrást tartalmazó előfizetés
- **Hely:** Az eredeti alkalmazás helye
- **ResourceGroupName:** Az eredeti erőforráscsoport neve
- **Név:** Az eredeti alkalmazás neve.
- **Slot**(Tárolóhely): a tárolóhely neve.
- **Törlés ideje:** Mikor lett törölve az alkalmazás  

## <a name="restore-deleted-app"></a>Törölt alkalmazás visszaállítása

>[!NOTE]
> `Restore-AzDeletedWebApp` függvényalkalmazások esetében nem támogatott.

Miután azonosította a visszaállítani kívánt alkalmazást, a használatával `Restore-AzDeletedWebApp` visszaállíthatja.

```powershell
Restore-AzDeletedWebApp -TargetResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```
> [!NOTE]
> Az üzembe helyezési pontokat a rendszer nem visszaállítja az alkalmazás részeként. Ha vissza kell állítania egy előkészítési helyet, használja a `-Slot <slot-name>`  jelzőt.
>

A parancs bemenetei a következőek:

- **Cél erőforráscsoport:** Cél erőforráscsoport, ahol az alkalmazás vissza lesz állítani
- **Név:** Az alkalmazás nevének globálisan egyedinek kell lennie.
- **TargetAppServicePlanName:** App Service az alkalmazáshoz kapcsolt csomag

Alapértelmezés szerint `Restore-AzDeletedWebApp` az alkalmazáskonfigurációt és a tartalmakat is visszaállítja. Ha csak a tartalmat szeretné visszaállítani, használja a `-RestoreContentOnly` jelölőt ezzel a parancsmaggal.

> [!NOTE]
> Ha az alkalmazást a szolgáltatás üzemeltette, majd törölte egy App Service Environment, csak akkor állítható vissza, ha a megfelelő App Service Environment még létezik.
>

A teljes parancsmag-referenciát itt találja: [Restore-AzDeletedWebApp](/powershell/module/az.websites/restore-azdeletedwebapp).
