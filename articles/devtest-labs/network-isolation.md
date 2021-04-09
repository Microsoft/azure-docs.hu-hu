---
title: Hálózati elkülönítés Azure DevTest Labs
description: A Azure DevTest Labs hálózati elkülönítésének megismerése.
ms.topic: article
ms.date: 08/25/2020
ms.openlocfilehash: 28bfa048a8a6666deb58a8ecfff38779c91d95b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96352932"
---
# <a name="network-isolation-in-devtest-labs"></a>Hálózati elkülönítés a DevTest Labs szolgáltatásban

Az [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) biztonsági határként működik, és az Azure-erőforrásokat a nyilvános internetről különíti el. Egy Azure-beli virtuális hálózatot is csatlakoztathat a helyszíni hálózathoz, hogy biztonságosan tudjon csatlakozni a helyszíni erőforrásokhoz. A DevTest Labs szolgáltatásban dönthet úgy, hogy [elkülöníti az összes Tesztkörnyezet virtuális gépet](devtest-lab-configure-vnet.md) és [környezetét a hálózatra](connect-environment-lab-virtual-network.md) , így biztosítva a laboratóriumi erőforrások számára a szervezeti hálózatkezelési házirendek követését. 

A labor tulajdonosaként dönthet úgy is, hogy teljesen elkülöníti a labort, ami azt jelenti, hogy a virtuális gépek és a környezetek a kiválasztott hálózathoz való elkülönítése mellett el is különítheti az előfizetésében létrehozott Lab Storage-fiókot és kulcstartókat. Ez a cikk végigvezeti a hálózat elkülönített laborjának létrehozásán. 

Tekintse át a következő cikkeket is:

- [Hogyan használja a DevTest Labs a Lab Storage-fiókot](encrypt-storage.md)
- [Hogyan használja a DevTest Labs a Key vaultokat?](devtest-lab-store-secrets-in-key-vault.md)
 
> [!NOTE]
> A hálózat elkülönítése jelenleg csak az új Labs-létrehozás esetében támogatott.

## <a name="steps-to-enable-network-isolation-during-lab-creation"></a>A hálózati elkülönítés engedélyezésének lépései a labor létrehozása során

1. A labor létrehozása során lépjen a **hálózatkezelés** lapra.
1. Kiválaszthat egy **alapértelmezett** hálózatot, amelyet a labor létrehoz az Ön számára, vagy kiválaszthat egy meglévő hálózatot a legördülő menüből. Csak olyan hálózatokat választhat, amelyek ugyanabban a régióban és előfizetésben találhatók, mint a laborban. 

    > [!div class="mx-imgBorder"]
    > ![Tesztkörnyezet létrehozása](./media/network-isolation/create-lab.png)
1. Válasszon egy alhálózatot.

    > [!div class="mx-imgBorder"]
    > ![Alhálózat létrehozása](./media/network-isolation/create-lab-subnet.png)
1. Ha úgy dönt, hogy elkülöníti a labor-erőforrásokat (Lab Storage-fiókot és kulcstartót) az alapértelmezett hálózathoz, nincs szükség további műveletre, és a labor kezeli az erőforrások elkülönítését.
 
    > [!div class="mx-imgBorder"]
    > ![Hálózatelkülönítés](./media/network-isolation/isolate-lab-resources.png)
1. Ha úgy dönt, hogy elkülöníti a laboratóriumi erőforrásokat (Lab Storage-fiókot és kulcstartót) egy meglévő hálózatra, akkor a labor létrehozása után végre kell hajtania a következő lépéseket, hogy a labor továbbra is az elkülönített módban működjön. 
 
    > [!div class="mx-imgBorder"]
    > ![Újraforrások elkülönítése](./media/network-isolation/isolate-my-vnet.png)

    > [!IMPORTANT]
    > A labor tulajdonosának végre kell hajtania ezeket a lépéseket a laborban található erőforrások konfigurálása és/vagy létrehozása előtt.

### <a name="steps-to-follow-post-lab-creation"></a>A labor utáni létrehozás követésének lépései

1. A labor kezdőlapján válassza ki az **erőforráscsoportot** az **Áttekintés** oldalon. Ekkor meg kell jelennie a labort tartalmazó erőforráscsoport **erőforráscsoport** -oldalának. 
 
   > [!div class="mx-imgBorder"]
   > ![Contoso labor](./media/network-isolation/contoso-lab.png)
1. Válassza ki a labor Azure Storage-fiókját. A labor Storage-fiók elnevezési konvenciója a következő: egy<*labNameWithoutInvalidCharacters* > *<4 számjegyű számú*>. Ha például a labor neve contosolab, a Storage-fiók neve acontosolab1234 lehet.
 
   > [!div class="mx-imgBorder"]
   > ![Contoso-teszt](./media/network-isolation/contoso-test.png)
1. A Storage-fiókban válassza a tűzfalak és virtuális hálózatok lehetőséget, és győződjön meg arról, hogy a "megbízható Microsoft-szolgáltatások hozzáférhetnek ehhez a Storage-fiókhoz" jelölőnégyzet be van jelölve. Mivel a [DevTest Labs egy megbízható Microsoft-szolgáltatás](../storage/common/storage-network-security.md#trusted-microsoft-services), ez a beállítás lehetővé teszi, hogy a labor szabályosan működjön a hálózat elkülönített üzemmódjában. 

   > [!div class="mx-imgBorder"]
   > ![Contoso Lab tűzfalak](./media/network-isolation/contoso-lab-firewalls-vnets.png)
1. Ezután kattintson a **+ meglévő virtuális hálózat hozzáadása** lehetőségre, válassza ki a tesztkörnyezet létrehozásakor kiválasztott virtuális hálózatot és alhálózatot, és kattintson az **Engedélyezés** gombra. 

   > [!div class="mx-imgBorder"]
   > ![Contoso saját vnet](./media/network-isolation/contoso-lab-my-vnet.png)
5.  Miután a szolgáltatási végpont sikeresen engedélyezve lett a kiválasztott virtuális hálózatra, kattintson a **Hozzáadás** gombra. 

   > [!div class="mx-imgBorder"]
   > ![Hozzáadás](./media/network-isolation/contoso-firewall-add.png)
 
Ezzel az Azure Storage lehetővé teszi a hozzáadott virtuális hálózat bejövő kapcsolatainak engedélyezését, és lehetővé teszi, hogy a labor sikeresen működjön a hálózat izolált üzemmódjában. 

Ezen beállítások automatizálását is beállíthatja úgy, hogy több laborhoz is konfigurálja ezt a beállítást. 

[További információ az Azure Storage-hoz készült alapértelmezett hálózati hozzáférési szabályok kezeléséről a PowerShell és a parancssori felület használatával](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#powershell)

## <a name="things-to-remember-while-using-a-lab-in-a-network-isolated-mode"></a>Megjegyezhető dolgok a hálózat izolált üzemmódjában lévő laborok használatakor

### <a name="accessing-labs-storage-account-outside-the-lab"></a>A labor Storage-fiókjához való hozzáférés a laboron kívül 

A hálózati elkülönített laboron belül olyan műveletekhez, mint például a virtuális merevlemez feltöltése a labor Storage-fiókjába egyéni rendszerképek létrehozásához, a labor tulajdonosának explicit módon engedélyeznie kell a Storage-fiók elérését egy engedélyezett végpontról. Ezt úgy teheti meg, hogy létrehoz egy virtuális gépet, és biztonságosan hozzáfér a laborhoz a virtuális gépről. 

[További információ a Storage-fiókok virtuális gépről való magánhálózati eléréséről](../private-link/tutorial-private-endpoint-storage-portal.md)

### <a name="exporting-usage-data-from-the-lab"></a>Használati adatok exportálása a laborból 

A laboratóriumban lévő [személyes használati adatok exportálásához](personal-data-delete-export.md)a labor tulajdonosának explicit módon kell megadnia a Storage-fiókot, és a fiókon belül kell létrehoznia az adatok tárolásához szükséges blobot. 

Ha nincs megadva Storage-fiók, a művelet sikertelen lesz a hálózat izolált módjában, mivel a labor Storage-fiókja nem érhető el a labor számára, hogy az ügyfél által biztosított Storage-fiókot ne használja. 

[További információ a tesztkörnyezet használati adatoknak a megadott Storage-fiókban való exportálásáról](personal-data-delete-export.md#azure-powershell)

## <a name="next-steps"></a>Következő lépések

[Labs automatikus létrehozása vagy módosítása Azure Resource Manager-sablonokkal és a PowerShell-lel](devtest-lab-use-arm-and-powershell-for-lab-resources.md)