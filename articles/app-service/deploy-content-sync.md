---
title: Tartalom szinkronizálása egy Felhőbeli mappából
description: Megtudhatja, hogyan helyezheti üzembe az alkalmazást úgy, hogy a tartalom szinkronizálásával Azure App Service a Cloud mappában, például a OneDrive vagy a Dropbox használatával.
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 02/25/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: bfee320c7a8b4cbe8439c376350d1234b393bfb5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051229"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Tartalom szinkronizálása egy Felhőbeli mappából a Azure App Serviceba
Ez a cikk bemutatja, hogyan szinkronizálhat tartalmat [Azure app Service](./overview.md) a dropboxból és a OneDrive. 

A tartalom-szinkronizálási megközelítéssel az alkalmazás kódjával és egy kijelölt Felhőbeli mappában lévő tartalommal dolgozhat, és meggyőződni arról, hogy a rendszer készen áll a telepítésre, majd a gombra kattintva szinkronizálja App Service. 

Az API-k alapvető eltérései miatt a **OneDrive for Business** jelenleg nem támogatott.

> [!NOTE]
> A Azure Portal, amely a régi telepítési élmény, a **fejlesztői központ (klasszikus)** lapja 2021 márciusában elavulttá válik. Ez a módosítás nem befolyásolja az alkalmazásban meglévő központi telepítési beállításokat, és az alkalmazás központi telepítését továbbra is kezelheti a **központi telepítési központ** lapon.

## <a name="enable-content-sync-deployment"></a>Tartalom-szinkronizálás telepítésének engedélyezése

1. A [Azure Portal](https://portal.azure.com)navigáljon a app Service alkalmazás felügyeleti lapjára.

1. A bal oldali menüben kattintson a **központi telepítési központ**  >  **beállításai** elemre. 

1. A **forrás** területen válassza a **OneDrive** vagy a **Dropbox** lehetőséget.

1. Kattintson az **Engedélyezés** elemre, és kövesse az engedélyezési utasításokat. 

    ![Bemutatja, hogyan engedélyezhető a OneDrive vagy a Dropbox a központi telepítési központban a Azure Portalban.](media/app-service-deploy-content-sync/choose-source.png)

    Az Azure-fiókjához csak egyszer kell engedélyeznie a OneDrive vagy a Dropboxot. Ha egy alkalmazáshoz másik OneDrive vagy Dropbox-fiókot szeretne engedélyezni, kattintson a **fiók módosítása** gombra.

1. A **mappában** válassza ki a szinkronizálni kívánt mappát. Ez a mappa a OneDrive vagy a Dropbox következő kijelölt tartalom elérési útján jön létre. 
   
    * **OneDrive**: `Apps\Azure Web Apps`
    * **Dropbox**: `Apps\Azure`
    
1. Kattintson a **Mentés** gombra.

## <a name="synchronize-content"></a>Tartalom szinkronizálása

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. A [Azure Portal](https://portal.azure.com)navigáljon a app Service alkalmazás felügyeleti lapjára.

1. A bal oldali menüben kattintson a **központi telepítési központ**  >  **újratelepítése/szinkronizálás** elemre. 

    ![Bemutatja, hogyan lehet szinkronizálni a felhőalapú mappát App Servicekal.](media/app-service-deploy-content-sync/synchronize.png)
   
1. A szinkronizálás megerősítéséhez kattintson **az OK** gombra.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Indítsa el a szinkronizálást a következő parancs futtatásával, és cserélje le a és a parancsot \<group-name> \<app-name> :

```azurecli-interactive
az webapp deployment source sync –-resource-group <group-name> –-name <app-name>
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Indítsa el a szinkronizálást a következő parancs futtatásával, és cserélje le a és a parancsot \<group-name> \<app-name> :

```azurepowershell-interactive
Invoke-AzureRmResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action sync -ApiVersion 2019-08-01 -Force –Verbose
```

-----

## <a name="disable-content-sync-deployment"></a>Tartalom-szinkronizálás központi telepítésének letiltása

1. A [Azure Portal](https://portal.azure.com)navigáljon a app Service alkalmazás felügyeleti lapjára.

1. A bal oldali menüben kattintson a **központi telepítési központ**  >  **beállításai**  >  **Leválasztás** lehetőségre. 

    ![Bemutatja, hogyan lehet leválasztani a Cloud Folder syncet a App Service alkalmazással a Azure Portal.](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Üzembe helyezés helyi git-tárházból](deploy-local-git.md)