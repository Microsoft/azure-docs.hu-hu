---
title: Tudnivalók az Azure-beli virtuális gépekkel kapcsolatos vész-helyreállításról Azure Site Recovery
description: Áttekintést nyújt az Azure-beli virtuális gépek Azure Site Recovery használatával történő replikálásához szükséges hálózatkezelésről.
services: site-recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/13/2020
ms.author: harshacs
ms.openlocfilehash: b9fdaf8a0791570ecee402442c5faefe2f70a22b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92370440"
---
# <a name="about-networking-in-azure-vm-disaster-recovery"></a>Tudnivalók az Azure-beli virtuális gépek vész-helyreállításáról



Ez a cikk hálózatkezelési útmutatást nyújt az Azure-beli virtuális gépek egyik régióból a másikba történő replikálásához és helyreállításához [Azure site Recovery](site-recovery-overview.md)használatával.

## <a name="before-you-start"></a>Előkészületek

[Ebből a forgatókönyvből](azure-to-azure-architecture.md)megtudhatja, hogyan biztosít a site Recovery vész-helyreállítást.

## <a name="typical-network-infrastructure"></a>Tipikus hálózati infrastruktúra

Az alábbi ábra egy tipikus Azure-környezetet ábrázol az Azure-beli virtuális gépeken futó alkalmazások esetében:

![Az Azure-beli virtuális gépeken futó alkalmazások tipikus Azure-környezetét ábrázoló diagram.](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Ha Azure-ExpressRoute használ, vagy a helyszíni hálózatról az Azure-ba VPN-kapcsolattal rendelkezik, a környezet a következő:

![ügyfél – környezet](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

A hálózatok általában tűzfalak és hálózati biztonsági csoportok (NSG-EK) használatával védettek. A hálózati kapcsolat vezérléséhez a szolgáltatás címkéit kell használni. A NSG több szolgáltatási címkét is engedélyezni kell a kimenő kapcsolatok vezérléséhez.

>[!IMPORTANT]
> Site Recovery nem támogatja hitelesített proxy használatát a hálózati kapcsolat vezérléséhez, és a replikáció nem engedélyezhető.

>[!NOTE]
>- Az IP-cím alapú szűrés nem hajtható végre a kimenő kapcsolatok vezérléséhez.
>- Azure Site Recovery IP-címeket nem szabad hozzáadni az Azure útválasztási táblában a kimenő kapcsolatok vezérléséhez.

## <a name="outbound-connectivity-for-urls"></a>Kimenő kapcsolat URL-címek esetén

Ha URL-alapú tűzfal-proxyt használ a kimenő kapcsolat vezérléséhez, engedélyezze ezeket a Site Recovery URL-címeket:

**URL-cím** | **Részletek**
--- | ---
*.blob.core.windows.net | Kötelező megadni, hogy az adatok a virtuális gépről származó forrás régióban lévő cache Storage-fiókba írhatók legyenek. Ha ismeri a virtuális gépekhez tartozó összes gyorsítótár-tárolási fiókot, a *. blob.core.windows.net helyett engedélyezheti a hozzáférést az adott Storage-fiók URL-címeihez (például: cache1.blob.core.windows.net és cache2.blob.core.windows.net).
login.microsoftonline.com | Az engedélyezéshez és a hitelesítéshez szükséges a Site Recovery szolgáltatás URL-címeihez.
*.hypervrecoverymanager.windowsazure.com | Szükséges, hogy a Site Recovery szolgáltatás kommunikációja a virtuális gépről is megtörténjen.
*.servicebus.windows.net | Szükséges, hogy a Site Recovery monitorozási és diagnosztikai adatok a virtuális gépről is írhatók legyenek.
*.vault.azure.net | Lehetővé teszi a hozzáférést az ADE-kompatibilis virtuális gépek replikálásának engedélyezéséhez a portálon keresztül
*. automation.ext.azure.com | Lehetővé teszi a mobilitási ügynöknek a portálon keresztüli replikált elemek automatikus frissítésének engedélyezését

## <a name="outbound-connectivity-using-service-tags"></a>Kimenő kapcsolat szolgáltatás-címkék használatával

A kimenő kapcsolatok vezérléséhez a NSG használatakor engedélyezni kell a szolgáltatás címkéit.

- A forrás régióban lévő Storage-fiókok esetében:
    - Hozzon létre egy [tárolási szolgáltatás címkén](../virtual-network/network-security-groups-overview.md#service-tags) ALAPULó NSG-szabályt a forrás régióhoz.
    - Engedélyezze ezeket a címeket úgy, hogy az adatok a virtuális gépről a gyorsítótárbeli Storage-fiókba legyenek írva.
- Hozzon létre egy [Azure Active Directory (HRE) Service tag](../virtual-network/network-security-groups-overview.md#service-tags) -alapú NSG-szabályt, amely lehetővé teszi a HRE-hoz tartozó összes IP-cím elérését
- Hozzon létre egy EventsHub-alapú NSG-szabályt a célként megadott régióhoz, és engedélyezze a hozzáférést Site Recovery figyeléshez.
- Hozzon létre egy AzureSiteRecovery-alapú NSG-szabályt, amellyel bármely régióban engedélyezheti a hozzáférést Site Recovery szolgáltatáshoz.
- Hozzon létre egy AzureKeyVault-alapú NSG-szabályt. Ez csak az ADE-kompatibilis virtuális gépek portálon keresztüli replikálásának engedélyezéséhez szükséges.
- Hozzon létre egy GuestAndHybridManagement-alapú NSG-szabályt. Ez csak a mobilitási ügynöknek a portálon keresztüli replikált elemek esetében történő automatikus frissítésének engedélyezéséhez szükséges.
- Javasoljuk, hogy hozza létre a szükséges NSG-szabályokat egy teszt NSG, és ellenőrizze, hogy nincsenek-e problémák a szabályok éles NSG való létrehozása előtt.

## <a name="example-nsg-configuration"></a>Példa NSG-konfigurációra

Ez a példa bemutatja, hogyan konfigurálhatja a virtuális gépek NSG-szabályait a replikáláshoz.

- Ha NSG szabályokat használ a kimenő kapcsolatok vezérlésére, használja a "HTTPS kimenő" szabályokat a 443-as portra a szükséges IP-címtartományok esetében.
- A példa azt feltételezi, hogy a virtuális gép forrása "az USA keleti régiója", a célhely pedig az "USA középső régiója".

### <a name="nsg-rules---east-us"></a>NSG-szabályok – USA keleti régiója

1. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt a "Storage. EastUS" számára a NSG, ahogy az alábbi képernyőképen is látható.

      ![Képernyőfelvétel: a hálózati biztonsági csoporthoz tartozó kimenő biztonsági szabály hozzáadása a következőhöz: Storage dot East U S.](./media/azure-to-azure-about-networking/storage-tag.png)

2. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt a NSG "AzureActiveDirectory" számára, ahogy az alábbi képernyőképen is látható.

      ![A képernyőképen az Azure A D hálózati biztonsági csoportjának kimenő biztonsági szabályának hozzáadása látható.](./media/azure-to-azure-about-networking/aad-tag.png)

3. A fenti biztonsági szabályokhoz hasonlóan hozzon létre egy kimenő HTTPS (443) biztonsági szabályt a "EventHub. CentralUS" számára a NSG, amely megfelel a célhelynek. Ez lehetővé teszi Site Recovery figyeléshez való hozzáférést.

4. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt a "AzureSiteRecovery" számára a NSG. Ez bármely régióban engedélyezi Site Recovery szolgáltatás elérését.

### <a name="nsg-rules---central-us"></a>NSG-szabályok – USA középső régiója

Ezek a szabályok azért szükségesek, hogy a replikáció engedélyezhető legyen a célként megadott régióból a feladatátvételt követően:

1. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt a "Storage. CentralUS" számára a NSG.

2. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt a "AzureActiveDirectory" számára a NSG.

3. A fenti biztonsági szabályokhoz hasonlóan hozzon létre egy kimenő HTTPS (443) biztonsági szabályt a "EventHub. EastUS" számára a NSG, amely megfelel a forrás helyének. Ez lehetővé teszi Site Recovery figyeléshez való hozzáférést.

4. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt a "AzureSiteRecovery" számára a NSG. Ez bármely régióban engedélyezi Site Recovery szolgáltatás elérését.

## <a name="network-virtual-appliance-configuration"></a>Hálózati virtuális berendezés konfigurációja

Ha hálózati virtuális berendezéseket (NVA-ket) használ a virtuális gépek kimenő hálózati forgalmának szabályozására, akkor előfordulhat, hogy a berendezés szabályozása megtörténik, ha az összes replikációs forgalom a NVA halad át. Javasoljuk, hogy hozzon létre egy hálózati szolgáltatási végpontot a virtuális hálózaton a "Storage" számára, hogy a replikálási forgalom ne lépjen a NVA.

### <a name="create-network-service-endpoint-for-storage"></a>Hálózati szolgáltatás végpontjának létrehozása a Storage-hoz
Létrehozhat egy hálózati szolgáltatási végpontot a virtuális hálózatban a "Storage" számára, így a replikálási forgalom nem hagyja el az Azure-határt.

- Válassza ki az Azure-beli virtuális hálózatot, és kattintson a "szolgáltatási végpontok" elemre.

    ![tárolás – végpont](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Kattintson a "Hozzáadás" és a "szolgáltatási végpontok hozzáadása" fülre.
- Válassza a "Microsoft. Storage" lehetőséget a "szolgáltatás" alatt, valamint a szükséges alhálózatokat az "alhálózatok" mezőben, és kattintson a "Hozzáadás" gombra.

>[!NOTE]
>Ne korlátozza a virtuális hálózati hozzáférést az ASR szolgáltatáshoz használt Storage-fiókokhoz. Engedélyeznie kell a hozzáférést az összes hálózatról

### <a name="forced-tunneling"></a>Alagúthasználat kényszerítése

Az Azure alapértelmezett rendszerútvonalát felülbírálhatja a 0.0.0.0/0 címek előtagja számára egy [Egyéni útvonallal](../virtual-network/virtual-networks-udr-overview.md#custom-routes) , és átirányíthatja a virtuális gépek forgalmát egy helyszíni hálózati virtuális készülékre (NVA), de ez a konfiguráció nem ajánlott site Recovery replikáláshoz. Ha egyéni útvonalakat használ, [hozzon létre egy virtuális hálózati szolgáltatási végpontot](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) a "Storage" virtuális hálózatában, hogy a replikálási forgalom ne hagyja el az Azure-határt.

## <a name="next-steps"></a>Következő lépések
- Az Azure-beli [virtuális gépek replikálásával](./azure-to-azure-quickstart.md)megkezdheti a számítási feladatok védelmét.
- További információ az Azure-beli virtuális gépek feladatátvételének [IP-címének megőrzéséről](site-recovery-retain-ip-azure-vm-failover.md) .
- További információ az Azure-beli [virtuális gépek ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md)-mel való vész-helyreállításáról.