---
title: Készlet kiépítése egy virtuális hálózatban
description: Batch-készlet létrehozása egy Azure-beli virtuális hálózatban, hogy a számítási csomópontok biztonságosan kommunikálhassanak a hálózaton lévő más virtuális gépekkel, például a fájlkiszolgálón.
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: d6e5de75164e098fc95f6c086d9f98a652dcee4a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103561913"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Azure Batch-készlet létrehozása egy virtuális hálózaton

Azure Batch készlet létrehozásakor kiépítheti a készletet egy Ön által megadott Azure-beli [virtuális hálózat](../virtual-network/virtual-networks-overview.md) (VNet) alhálózatában. Ez a cikk azt ismerteti, hogyan lehet batch-készleteket beállítani egy VNet.

## <a name="why-use-a-vnet"></a>Miért érdemes VNet használni?

A készletben lévő számítási csomópontok kommunikálhatnak egymással, például több példányos feladatok futtatásához, külön VNet nélkül. Alapértelmezés szerint azonban a készlet csomópontjai nem tudnak kommunikálni a készleten kívüli virtuális gépekkel, például a licenckiszolgálóval vagy a fájlkiszolgálók használatával.

Ha lehetővé szeretné tenni a számítási csomópontok számára a biztonságos kommunikációt más virtuális gépekkel vagy helyszíni hálózattal, az Azure-VNet alhálózatán is kiépítheti a készletet.

## <a name="prerequisites"></a>Előfeltételek

- **Hitelesítés**. Egy Azure-beli virtuális hálózat használatához a Batch-ügyfél API-jának Azure Active Directory- (AD-) hitelesítést kell használnia. Az Azure AD Azure Batch-támogatásának dokumentációjáért lásd a [Batch szolgáltatás Active Directoryval történő hitelesítésével](batch-aad-auth.md) foglalkozó témakört.

- **Egy Azure-VNet**. A VNet követelményeinek és konfigurációjának megtekintéséhez tekintse meg a következő szakaszt. Egy vagy több alhálózattal rendelkező VNet előkészítéséhez használhatja a Azure Portal, Azure PowerShell, az Azure Command-Line felületét (CLI) vagy más módszert.
  - Azure Resource Manager-alapú VNet létrehozásával kapcsolatban lásd: [virtuális hálózat létrehozása](../virtual-network/manage-virtual-network.md#create-a-virtual-network). A Resource Manager-alapú VNet új központi telepítések esetén ajánlott, és csak a virtuális gépek konfigurációját használó készleteken támogatott.
  - Klasszikus VNet létrehozásával kapcsolatban lásd: [virtuális hálózat (klasszikus) létrehozása több alhálózattal](/previous-versions/azure/virtual-network/create-virtual-network-classic). A klasszikus VNet csak Cloud Services konfigurációt használó készleteken támogatott.

## <a name="vnet-requirements"></a>A virtuális hálózat követelményei

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-azure-portal"></a>Készlet létrehozása VNet a Azure Portal

Miután létrehozta a VNet, és hozzárendelt egy alhálózatot, létrehozhat egy batch-készletet az adott VNet. A következő lépésekkel hozhat létre egy készletet a Azure Portalből: 

1. Az Azure portálon lépjen Batch-fiókjára. Ennek a fióknak ugyanabban az előfizetésben és régióban kell lennie, mint a használni kívánt VNet tartalmazó erőforráscsoport.
2. A bal oldali **Beállítások** ablakban válassza a **készletek** menüpontot.
3. A **készletek** ablakban válassza a **Hozzáadás** lehetőséget.
4. A **készlet hozzáadása** ablakban válassza ki a használni kívánt beállítást a **rendszerkép típusa** legördülő listából.
5. Válassza ki a megfelelő **közzétevőt/ajánlatot/SKU-** t az egyéni rendszerképhez.
6. Megadhatja a fennmaradó szükséges beállításokat, beleértve a **csomópont méretét**, a **célként kijelölt csomópontokat** és az **alacsony prioritású csomópontokat**, valamint a kívánt választható beállításokat.
7. A **Virtual Network** területen válassza ki a használni kívánt virtuális hálózatot és alhálózatot.

   ![Készlet hozzáadása virtuális hálózattal](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>A kényszerített bújtatáshoz tartozó felhasználó által megadott útvonalak

Előfordulhat, hogy a szervezete követelményei szerint a rendszer átirányítja (kényszeríti) az internetre irányuló forgalmat az alhálózatról a helyszíni helyre a vizsgálat és a naplózás érdekében. Emellett előfordulhat, hogy engedélyezte a kényszerített bújtatást a VNet lévő alhálózatokon.

Annak biztosítása érdekében, hogy a készlet csomópontjai olyan VNet működjenek, amelyben a kényszerített bújtatás engedélyezve van, hozzá kell adnia a következő, [felhasználó által definiált útvonalakat](../virtual-network/virtual-networks-udr-overview.md) (UDR) ehhez az alhálózathoz:

- A Batch szolgáltatásnak kommunikálnia kell a csomópontokkal az ütemezési feladatokhoz. A kommunikáció engedélyezéséhez adjon hozzá egy UDR a Batch szolgáltatás által használt összes IP-címhez abban a régióban, ahol a Batch-fiók létezik. A Batch szolgáltatás IP-címeinek listájának beszerzéséhez lásd: [szolgáltatás-címkék a helyszínen](../virtual-network/service-tags-overview.md).

- Győződjön meg arról, hogy a helyszíni hálózat nem blokkolja az Azure Storage-ba irányuló kimenő adatforgalmat (pontosabban az űrlap URL-címét, `<account>.table.core.windows.net` `<account>.queue.core.windows.net` és `<account>.blob.core.windows.net` ).

UDR hozzáadásakor adja meg az útvonalat az egyes kapcsolódó batch IP-címek előtagjához, és állítsa be a **következő ugrás típusát** az **internetre**.

![Felhasználó által megadott útvonal](./media/batch-virtual-network/user-defined-route.png)

> [!WARNING]
> A Batch szolgáltatás IP-címei idővel módosulhatnak. Az IP-címek változása miatti kimaradások megelőzése érdekében hozzon létre egy folyamatot, amely automatikusan frissíti a Batch szolgáltatás IP-címeit, és naprakészen tartja azokat az útválasztási táblázatban.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg a [Batch szolgáltatás munkafolyamatát és az elsődleges erőforrásokat](batch-service-workflow-features.md) , például a készleteket, a csomópontokat, a feladatokat és a feladatokat.
- Megtudhatja, hogyan [hozhat létre felhasználó által megadott útvonalat a Azure Portalban](../virtual-network/tutorial-create-route-table-portal.md).
