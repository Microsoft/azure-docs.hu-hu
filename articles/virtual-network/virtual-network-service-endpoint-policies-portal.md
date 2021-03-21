---
title: Szolgáltatás-végponti házirendek létrehozása és hozzárendelése – Azure Portal
titlesuffix: Azure Virtual Network
description: Ebből a cikkből megtudhatja, hogyan állíthat be és társíthat szolgáltatás-végponti házirendeket a Azure Portal használatával.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: bdf0e87c92a55d0dbb5bbe34334a6de4580cb350
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96004959"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>Szolgáltatás-végponti házirend létrehozása, módosítása vagy törlése a Azure Portal használatával

A szolgáltatás-végponti házirendek lehetővé teszik a virtuális hálózati forgalom szűrését bizonyos Azure-erőforrásokra, a szolgáltatási végpontokon keresztül. Ha még nem ismeri a szolgáltatás-végponti házirendeket, tekintse meg a [szolgáltatás-végponti házirendek áttekintését](virtual-network-service-endpoint-policies-overview.md) .

 Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Szolgáltatás-végponti szabályzat létrehozása
> * Szolgáltatás-végponti házirend definíciójának létrehozása
> * Virtuális hálózat létrehozása alhálózattal
> * Szolgáltatás-végponti házirend hozzárendelése egy alhálózathoz

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-service-endpoint-policy"></a>Szolgáltatás-végponti szabályzat létrehozása

1. Válassza az Azure Portal bal felső sarkában található **+ Erőforrás létrehozása** lehetőséget.
2. A keresés ablaktáblán írja be a "szolgáltatási végponti házirend" kifejezést, majd válassza a **szolgáltatás-végponti házirend** elemet, majd kattintson a **Létrehozás** gombra.

![Szolgáltatás-végponti szabályzat létrehozása](./media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

3. Adja meg vagy válassza ki a következő információkat az **alapismeretekben** 

   - Előfizetés: válassza ki az előfizetést a Szabályzathoz
   - Erőforráscsoport: válassza az **új létrehozása** elemet, és adja meg a *myResourceGroup*
   - Név: myEndpointPolicy
   - Hely: USA középső régiója
 
   ![Szolgáltatás-végponti szabályzat létrehozása alapjai](./media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

4. Válassza a **+ Hozzáadás** az **erőforrások** alatt lehetőséget, majd adja meg vagy válassza ki az alábbi adatokat az **erőforrás hozzáadása** panelen.

   - Szolgáltatás: csak a **Microsoft. Storage** szolgáltatás érhető el a szolgáltatási végpont házirendjeivel
   - Hatókör: válasszon ki egyet **egyetlen fiókból**, az **előfizetésben szereplő összes fiókot** és **az erőforráscsoport összes fiókját** .
   - Előfizetés: válassza ki az előfizetését a Storage-fiókhoz. A házirend-és a tárolási fiókok különböző előfizetésekben lehetnek.
   - Erőforráscsoport: válassza ki az erőforráscsoportot. Kötelező, ha a hatókör beállítása "minden fiók az erőforráscsoporthoz" vagy "single account".  
   - Erőforrás: válassza ki az Azure Storage-erőforrást a kiválasztott előfizetés vagy erőforráscsoport alatt
   - Kattintson a **Hozzáadás** gombra a lap alján az erőforrás hozzáadásának befejezéséhez

   ![Szolgáltatás végpont-házirendjének definíciója – erőforrás](./media/virtual-network-service-endpoint-policies-portal/create-sep-add-resource.png)

   - További erőforrások hozzáadásához szükség szerint ismételje meg a fenti lépéseket.

5. Opcionális: adja meg vagy válassza ki a következő adatokat a **címkékben**:
   
   - Kulcs: válassza ki a Szabályzathoz tartozó kulcsot. Pl.: Dept     
   - Érték: adja meg a kulcshoz tartozó érték párokat. Pl.: pénzügy

6. Válassza a **Felülvizsgálat és létrehozás** lehetőséget. Ellenőrizze az adatokat, majd kattintson a **Létrehozás** gombra. További szerkesztéshez kattintson az **előző** gombra. 

   ![Szolgáltatás-végponti szabályzat végleges érvényesítésének létrehozása](./media/virtual-network-service-endpoint-policies-portal/create-sep-review-create.png)
  
## <a name="view-endpoint-policies"></a>Végponti házirendek megtekintése 

1. A portál *összes szolgáltatás* mezőjében kezdje el beírni a *szolgáltatás-végponti házirendeket*. Válassza ki a **szolgáltatás-végponti házirendeket**.
2. Az **előfizetések** területen válassza ki az előfizetését és az erőforráscsoportot az alábbi ábrán látható módon

   ![Házirend megjelenítése](./media/virtual-network-service-endpoint-policies-portal/sep-view.png)
       
3. Válassza ki a szabályzatot, és kattintson a **házirend-definíciók** elemre a további szabályzat-definíciók megtekintéséhez vagy hozzáadásához.

   ![Szabályzat-definíciók megjelenítése](./media/virtual-network-service-endpoint-policies-portal/sep-policy-definition.png)

4. A **társított** alhálózatok elemre kattintva megtekintheti azokat az alhálózatokat, amelyekre a házirend társítva van. Ha még nincs alhálózat társítva, kövesse a következő lépés utasításait.

   ![Társított alhálózatok](./media/virtual-network-service-endpoint-policies-portal/sep-associated-subnets.png)
 
5. Szabályzat hozzárendelése alhálózathoz

>[!WARNING] 
> Győződjön meg arról, hogy az alhálózatról elért összes erőforrás hozzá van adva a házirend-definícióhoz, mielőtt társítja a házirendet az adott alhálózathoz. A házirend társítása után a szolgáltatás-végpontokon csak a *felsorolt* erőforrásokhoz való hozzáférés engedélyezett lesz. 
>
> Győződjön meg arról is, hogy egyetlen felügyelt Azure-szolgáltatás sem létezik a szolgáltatási végpont házirendjéhez társított alhálózatban.

- Mielőtt hozzárendelhet egy házirendet egy alhálózathoz, létre kell hoznia egy virtuális hálózatot és alhálózatot. Ehhez a súgóhoz a [Virtual Network létrehozása](./quick-create-portal.md) című cikkben olvashat.

- A virtuális hálózat és az alhálózat beállítása után konfigurálnia kell Virtual Network szolgáltatási végpontokat az Azure Storage szolgáltatáshoz. A Virtual Network panelen válassza a **szolgáltatási végpontok** lehetőséget, majd a következő ablaktáblán válassza a **Microsoft. Storage** lehetőséget, és az **alhálózatok** területen válassza ki a kívánt VNet vagy alhálózatot.

- Most választhat, hogy kijelöli-e a szolgáltatási végpont házirendjét a fenti ablaktábla legördülő menüjéből, ha már létrehozott szolgáltatási végponti házirendeket az alhálózat szolgáltatási végpontjának konfigurálása előtt, az alábbi ábrán látható módon.

    ![Alhálózat hozzárendelése szolgáltatási végpont létrehozásakor](./media/virtual-network-service-endpoint-policies-portal/vnet-config-service-endpoint-add-sep.png)

- Ha a szolgáltatási végpontok konfigurálása után már konfigurálta a szolgáltatás-végponti házirendeket, akkor az alhálózatot a szolgáltatás végponti házirendjének paneljén úgy is társíthatja, hogy a **társított alhálózatok** panelre navigál, ahogy az alábbi ábrán látható.

    ![Alhálózat hozzárendelése a SEP-n keresztül](./media/virtual-network-service-endpoint-policies-portal/sep-edit-subnet-association.png)

>[!WARNING] 
>Az Azure Storage-erőforrásokhoz való hozzáférés minden régióban az alhálózatból származó szolgáltatási végponti szabályzatként lesz korlátozva.

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban létrehozott egy szolgáltatási végponti házirendet, és hozzárendelte egy alhálózathoz. A szolgáltatás-végponti házirendekkel kapcsolatos további tudnivalókért lásd: [szolgáltatás-végponti házirendek áttekintése.](virtual-network-service-endpoint-policies-overview.md)
