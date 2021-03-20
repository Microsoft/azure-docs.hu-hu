---
title: Törölt alkalmazások visszaállítása
description: Megtudhatja, hogyan állíthatja vissza egy törölt alkalmazást a Azure App Serviceban. Kerülje a véletlenül törölt alkalmazások fejfájását.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: 71f762ac0effc9ad14510c02679109362163f66d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97008537"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Eltávolított App Service-alkalmazás visszaállítása a PowerShell használatával

Ha véletlenül törölte az alkalmazást Azure App Serviceban, visszaállíthatja az az [PowerShell-modul](/powershell/azure/)parancsainak használatával.

> [!NOTE]
> - A törölt alkalmazások a kezdeti törlés után 30 nappal törlődnek a rendszerből. Az alkalmazások törlése után nem állítható helyre.
> - A redelete funkció nem támogatott a használati tervben.
> - A App Service Environmenton futó app Service-alkalmazások nem támogatják a pillanatképeket. Ezért a törlési funkció és a klónozási funkció nem támogatott a App Service Environment futó App Service alkalmazások esetében.
>

## <a name="re-register-app-service-resource-provider"></a>App Service erőforrás-szolgáltató újbóli regisztrálása

Egyes ügyfelek olyan problémákkal találkozhatnak, ahol a törölt alkalmazások listájának beolvasása meghiúsul. A probléma megoldásához futtassa a következő parancsot:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Törölt alkalmazások listázása

A törölt alkalmazások gyűjteményének lekéréséhez használhatja a következőt: `Get-AzDeletedWebApp` .

A következő használható egy adott törölt alkalmazás részletei:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

A részletes információk a következők:

- **DeletedSiteId**: az alkalmazás egyedi azonosítója, amelyet olyan helyzetekben kell használni, amikor már több azonos nevű alkalmazás is törölve lett
- **SubscriptionID**: a törölt erőforrást tartalmazó előfizetés
- **Hely**: az eredeti alkalmazás helye
- **ResourceGroupName**: az eredeti erőforráscsoport neve
- **Name (név**): az eredeti alkalmazás neve.
- **Tárolóhely**: a tárolóhely neve.
- **Törlés időpontja**: Mikor lett törölve az alkalmazás  

## <a name="restore-deleted-app"></a>Törölt alkalmazás visszaállítása

>[!NOTE]
> `Restore-AzDeletedWebApp` function apps esetében nem támogatott.

Miután azonosította a visszaállítani kívánt alkalmazást, visszaállíthatja azt a használatával `Restore-AzDeletedWebApp` .

```powershell
Restore-AzDeletedWebApp -TargetResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```
> [!NOTE]
> Az üzembe helyezési pontok nem állíthatók vissza az alkalmazás részeként. Ha vissza kell állítania egy átmeneti tárolóhelyet, használja a `-Slot <slot-name>`  jelzőt.
>

A következő parancs bemenetei:

- **Cél erőforráscsoport**: cél erőforráscsoport, amelyben az alkalmazás vissza lesz állítva
- **Név**: az alkalmazás nevének globálisan egyedinek kell lennie.
- **TargetAppServicePlanName**: app Service az alkalmazáshoz társított csomag

Alapértelmezés szerint az `Restore-AzDeletedWebApp` alkalmazás konfigurációját és tartalmát is visszaállítja. Ha csak a tartalmat szeretné visszaállítani, használja a `-RestoreContentOnly` jelzőt ezzel a parancsmagot.

> [!NOTE]
> Ha az alkalmazás futott, majd törölve lett egy App Service Environment, akkor csak akkor állítható vissza, ha a megfelelő App Service Environment még létezik.
>

A teljes parancsmagot-hivatkozást itt találja: [Restore-AzDeletedWebApp](/powershell/module/az.websites/restore-azdeletedwebapp).
