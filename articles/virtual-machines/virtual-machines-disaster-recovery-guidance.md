---
title: Azure-szolgáltatásbeli fennakadások
description: Ismerje meg, mi a teendő abban az esetben, ha egy Azure-szolgáltatás megszakad az Azure-beli virtuális gépeken.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 05/31/2017
ms.author: cynthn
ms.reviewer: ''
ms.openlocfilehash: 5c6036a65fa449986f5c085202fefa752e3a414a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98701033"
---
# <a name="what-if-an-azure-service-disruption-impacts-azure-vms"></a>Mi a teendő, ha az Azure-szolgáltatás megszakadásának hatására az Azure virtuális gépek

A Microsoftnál keményen dolgozunk, hogy a szolgáltatások mindig elérhetők legyenek, amikor szüksége van rájuk. A szabályozáson kívüli erők időnként a nem tervezett szolgáltatások megszakadását okozó módokat érintik.

A Microsoft szolgáltatói szerződés (SLA) szolgáltatást biztosít a szolgáltatásai számára az üzemidő és a kapcsolat iránti elkötelezettségként. Az egyes Azure-szolgáltatásokra vonatkozó SLA-t az [Azure-szolgáltatói szerződésekben](https://azure.microsoft.com/support/legal/sla/)találhatja meg.

Az Azure már számos beépített platformot kínál, amelyek támogatják a magas rendelkezésre állású alkalmazásokat. A szolgáltatásokkal kapcsolatos további információkért olvassa el a vész [-helyreállítást és a magas rendelkezésre állást az Azure-alkalmazásokhoz](/azure/architecture/framework/resiliency/backup-and-recovery).

Ez a cikk egy valós vész-helyreállítási forgatókönyvet mutat be, amikor egy egész régió jelentős természeti katasztrófák vagy széleskörű szolgáltatás-megszakítás miatt leáll. Ezek ritkán előfordulnak, de fel kell készülnie arra, hogy a teljes régió leálljon. Ha egy teljes régió a szolgáltatás megszakadását tapasztalja, az adatai helyileg redundáns másolatai átmenetileg elérhetetlenné válnak. Ha engedélyezte a földrajzi replikálást, az Azure Storage-blobok és-táblák három további példánya egy másik régióban tárolódik. Ha egy teljes regionális leállás vagy egy olyan katasztrófa következik be, amelyben az elsődleges régió nem helyreállítható, az Azure az összes DNS-bejegyzést átképezi a földrajzilag replikált régióba.

A ritka előfordulások kezeléséhez a következő útmutatást nyújtjuk az Azure-beli virtuális gépekhez abban az esetben, ha az Azure-beli virtuálisgép-alkalmazást üzembe helyező teljes régiót megzavarják.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>1. lehetőség: feladatátvétel kezdeményezése Azure Site Recovery használatával
A virtuális gépekhez Azure Site Recovery konfigurálhatja, hogy az alkalmazás a percek alatt egyetlen kattintással helyreállítható legyen. A replikációt a választott Azure-régióba replikálhatja, és nem korlátozhatja a párosított régiókat. [A virtuális gépek replikálásával](../site-recovery/azure-to-azure-quickstart.md)kezdheti meg a lépéseket. [Létrehozhat egy helyreállítási tervet](../site-recovery/site-recovery-create-recovery-plans.md) , amely segítségével automatizálhatja az alkalmazás teljes feladatátvételi folyamatát. [A feladatátvételt előre tesztelheti](../site-recovery/site-recovery-test-failover-to-azure.md) a termelési alkalmazás vagy a folyamatban lévő replikáció nélkül. Az elsődleges régiók megszakadásakor a [feladatátvételt kezdeményezni](../site-recovery/site-recovery-failover.md) kell, és az alkalmazást a célként megadott régióban kell használni.


## <a name="option-2-wait-for-recovery"></a>2. lehetőség: várakozás a helyreállításra
Ebben az esetben nincs szükség beavatkozásra a részen. Biztos lehet benne, hogy szorgalmasan dolgozunk a szolgáltatás rendelkezésre állásának helyreállításán. A szolgáltatás aktuális állapotát a [Azure Service Health irányítópulton](https://azure.microsoft.com/status/)tekintheti meg.

Ez a legjobb megoldás, ha nem állított be Azure Site Recovery, a megszakadás előtt olvassa el a Geo-redundáns tárterületet vagy a Geo-redundáns tárterületet. Ha a virtuális gép virtuális merevlemezeit (VHD-ket) tartalmazó Storage-fiókhoz beállította a Geo-redundáns tárolást vagy az olvasási hozzáférésű geo-redundáns tárolót, akkor megkeresheti az alapszintű lemezkép VHD-jét, és kiépítheti az új virtuális gépet. Ez nem előnyben részesített lehetőség, mert nincs garancia az adatszinkronizálásra. Ennek következtében ez a lehetőség nem garantált a működéshez.


> [!NOTE]
> Ügyeljen arra, hogy a folyamat felett ne legyen vezérlés, és csak az egész régióra kiterjedő szolgáltatásokkal kapcsolatos fennakadások esetén fog történni. Emiatt a legmagasabb szintű rendelkezésre állás elérése érdekében más alkalmazásspecifikus biztonsági mentési stratégiákra is támaszkodnia kell. További információ: a vész- [helyreállítási adatstratégiákkal](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan)foglalkozó szakasz.
>
>

## <a name="next-steps"></a>Következő lépések

- [Az Azure-beli virtuális gépeken futó alkalmazások védelmének](../site-recovery/azure-to-azure-quickstart.md) megkezdése Azure site Recovery használatával

- Ha többet szeretne megtudni a vész-helyreállítási és a magas rendelkezésre állási stratégia megvalósításáról, tekintse meg a vész [-helyreállítási és magas rendelkezésre állású Azure-alkalmazások](/azure/architecture/framework/resiliency/backup-and-recovery)című témakört.

- A felhőalapú platform képességeinek részletes technikai megismeréséhez lásd: az [Azure rugalmasságával kapcsolatos technikai útmutató](../data-lake-store/data-lake-store-disaster-recovery-guidance.md).


- Ha az utasítások nem egyértelműek, vagy ha szeretné, hogy a Microsoft végezze el a műveleteket az Ön nevében, forduljon az [ügyfélszolgálathoz](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).