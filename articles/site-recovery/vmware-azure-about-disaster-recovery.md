---
title: VMware vész-helyreállítás Azure Site Recovery
description: Ez a cikk áttekintést nyújt a VMware virtuális gépek az Azure-ba való vész-helyreállításáról az Azure Site Recovery szolgáltatás használatával.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: dfbdff01064b483085233ece47d1d3b635b68743
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87021460"
---
# <a name="about-disaster-recovery-of-vmware-vms-to-azure"></a>A VMware virtuális gépek az Azure-ba való vész-helyreállításáról

Ez a cikk áttekintést nyújt a helyszíni VMware virtuális gépekhez az Azure-ban az [Azure site Recovery](site-recovery-overview.md) szolgáltatással történő vész-helyreállítási feladatairól.

## <a name="what-is-bcdr"></a>Mi az a BCDR?

Az üzletmenet-folytonosság és a vész-helyreállítási (BCDR) stratégia segít megőrizni üzleti tevékenységét. A tervezett állásidő és a váratlan kimaradások során a BCDR gondoskodik az adatbiztonságról és a rendelkezésre állásról, és biztosítja, hogy az alkalmazások tovább futnak. A platform BCDR funkciói, például a regionális párosítások és a magas rendelkezésre állású tárolók mellett az Azure a BCDR-megoldás szerves részét képezi Recovery Services. A helyreállítási szolgáltatások a következők: 

- [Azure Backup](../backup/backup-overview.md) biztonsági mentést készít a helyszíni és az Azure-beli virtuális gép adatairól. Biztonsági mentést készíthet egy fájlokról és mappákról, adott munkaterhelésekről vagy egy teljes virtuális gépről. 
- A [Azure site Recovery](site-recovery-overview.md) rugalmasságot és vész-helyreállítási lehetőségeket biztosít a helyszíni gépeken vagy az Azure IaaS virtuális gépeken futó alkalmazások és munkaterhelések számára. Site Recovery összehangolja a replikációt, és leállások esetén kezeli a feladatátvételt az Azure-ban. Az Azure-ból az elsődleges helyre történő helyreállítást is kezeli. 

> [!NOTE]
> A Site Recovery nem helyezi át vagy nem tárolja az ügyféladatokat a célként megadott régióból, amely során a forrásként szolgáló gépekhez a vész-helyreállítás lett beállítva. Az ügyfelek választhatnak egy Recovery Services-tárolót egy másik régióból is. A Recovery Services-tároló metaadatokat tartalmaz, de nincsenek tényleges ügyféladatok.

## <a name="how-does-site-recovery-do-disaster-recovery"></a>Hogyan működik Site Recovery a vész-helyreállítás?

1. Az Azure és a helyszíni hely előkészítése után be-és kikapcsolhatja a helyszíni gépek replikálását.
2. Site Recovery a házirend-beállításoknak megfelelően összehangolja a gép kezdeti replikációját.
3. A kezdeti replikálást követően Site Recovery replikálja az Azure-beli különbözeti változásokat. 
4. Ha minden a várt módon replikálódik, egy vész-helyreállítási részletezést futtat.
    - A részletezéssel biztosítható, hogy a feladatátvétel a várt módon működjön, amikor valós igény merül fel.
    - A részletezés feladatátvételi tesztet hajt végre az éles környezet befolyásolása nélkül.
5. Leállás esetén teljes feladatátvételt hajt végre az Azure-ban. Feladatátvételt végezhet egyetlen gépen, vagy létrehozhat egy olyan helyreállítási tervet, amely egyszerre több gép feladatátvételét végzi el.
6. Feladatátvételkor az Azure-beli virtuális gépek a virtuális gép adataiból jönnek létre a felügyelt lemezeken vagy a Storage-fiókokban. A felhasználók továbbra is hozzáférhetnek az alkalmazásokhoz és munkaterhelésekhez az Azure-beli virtuális gépről
7. Ha a helyszíni hely ismét elérhetővé válik, az Azure-ból fog visszatérni.
8. A feladat-visszavételt követően, és az elsődleges helyről még egyszer dolgozik, a helyszíni virtuális gépeket ismét az Azure-ba replikálja.


## <a name="how-do-i-know-if-my-environment-is-suitable-for-disaster-recovery-to-azure"></a>Hogyan, hogy a környezetem alkalmas-e a vész-helyreállításra az Azure-ban?

A Site Recovery bármely, a támogatott VMware virtuális gépen vagy fizikai kiszolgálón futó számítási feladatot replikálhat. A következő műveleteket kell megadnia a környezetben:

- Ha VMware virtuális gépeket replikál, a VMware virtualizációs kiszolgálók megfelelő verzióit futtatja? [Itt tájékozódhat](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers).
- Egy támogatott operációs rendszert futtató gépeket kíván replikálni? [Itt tájékozódhat](vmware-physical-azure-support-matrix.md#replicated-machines).
- A Linux vész-helyreállítás esetén támogatott fájlrendszer/vendég tárolót futtató gépek? [Itt tekintheti meg](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)
- A replikálni kívánt gépek megfelelnek az Azure-követelményeknek? [Itt tájékozódhat](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
- Támogatja a hálózati konfigurációt? [Itt tájékozódhat](vmware-physical-azure-support-matrix.md#network).
- Támogatott-e a tárolási konfiguráció? [Itt tájékozódhat](vmware-physical-azure-support-matrix.md#storage).


## <a name="what-do-i-need-to-set-up-in-azure-before-i-start"></a>Mire van szükség az Azure-ban a Kezdés előtt?

Az Azure-ban a következőket kell előkészítenie:

1. Ellenőrizze, hogy az Azure-fiókja rendelkezik-e a virtuális gépek Azure-beli létrehozásához szükséges engedélyekkel.
2. Hozzon létre egy Azure-hálózatot, amelyhez az Azure virtuális gépek csatlakoznak a Storage-fiókok vagy a felügyelt lemezek létrehozása után a feladatátvétel után.
3. Hozzon létre egy Azure Recovery Services-tárolót a Site Recoveryhoz. A tároló a Azure Portalban található, és a Site Recovery üzembe helyezésére, konfigurálására, hangolására, figyelésére és hibakeresésére szolgál.

*További segítségre van szüksége?*

Ismerje meg, hogyan állíthatja be az Azure-t [a fiók ellenőrzésével](tutorial-prepare-azure.md#verify-account-permissions), a [hálózat](tutorial-prepare-azure.md#set-up-an-azure-network)létrehozásával és [a tároló beállításával](tutorial-prepare-azure.md#create-a-recovery-services-vault).



## <a name="what-do-i-need-to-set-up-on-premises-before-i-start"></a>Mire van szükség a helyszíni üzembe helyezéshez, mielőtt elkezdem?

A helyszínen a következőket kell tennie:

1. Be kell állítania néhány fiókot:

    - Ha VMware virtuális gépeket replikál, egy fiókra van szükség ahhoz, hogy a virtuális gépek automatikus felderítéséhez Site Recovery vCenter Server vagy vSphere ESXi-gazdagépek eléréséhez.
    - A replikálni kívánt fizikai gépekre vagy virtuális gépekre a Site Recovery mobilitási szolgáltatás ügynökének telepítéséhez fiók szükséges.

2. Ha korábban nem tette meg, ellenőriznie kell a VMware-infrastruktúra kompatibilitását.
3. A feladatátvételt követően ellenőrizze, hogy tud-e csatlakozni az Azure-beli virtuális gépekhez. Az RDP-t a helyszíni Windows-gépeken, vagy az SSH-val Linux rendszerű gépeken állíthatja be.

*További segítségre van szüksége?*
- Készítse elő a fiókokat az [automatikus felderítéshez](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) és [a mobilitási szolgáltatás telepítéséhez](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation).
- [Ellenőrizze](vmware-azure-tutorial-prepare-on-premises.md#check-vmware-requirements) , hogy a VMware-beállítások kompatibilisek-e.
- [Készítse elő](vmware-azure-tutorial-prepare-on-premises.md#prepare-to-connect-to-azure-vms-after-failover) , hogy a feladatátvételt követően kapcsolódjon az Azure-ban.
- Ha részletesebb segítségre van szüksége az Azure-beli virtuális gépek IP-címzésének beállításához a feladatátvétel után, [olvassa el ezt a cikket](concepts-on-premises-to-azure-networking.md).

## <a name="how-do-i-set-up-disaster-recovery"></a>Hogyan beállítani a vész-helyreállítást?

Az Azure és a helyszíni infrastruktúra meglétét követően beállíthatja a vész-helyreállítást.

1. Az üzembe helyezni kívánt összetevők megismeréséhez tekintse át a [VMware – Azure architektúrát](vmware-azure-architecture.md), valamint a fizikai és az [Azure architektúrát](physical-azure-architecture.md). Számos összetevő létezik, ezért fontos tisztában lennie azzal, hogyan illeszkednek egymáshoz.
2. **Forrásoldali környezet**: az üzembe helyezés első lépéseként be kell állítania a replikációs forrás környezetét. Megadhatja, hogy mit szeretne replikálni, és hová szeretné replikálni a-t.
3. **Konfigurációs kiszolgáló**: be kell állítania egy konfigurációs kiszolgálót a helyszíni forrás környezetében:
    - A konfigurációs kiszolgáló egyetlen helyszíni gép. A VMware vész-helyreállítási szolgáltatásként azt javasoljuk, hogy egy letölthető OVF-sablonból üzembe helyezhető VMware virtuális gépként telepítse.
    - A konfigurációs kiszolgáló koordinálja a helyszíni és az Azure közötti kommunikációt
    - Néhány egyéb összetevő a konfigurációs kiszolgáló számítógépén fut.
        - A folyamat-kiszolgáló fogadja, optimalizálja és elküldi a replikációs adatokat az Azure-ban tárolt Storage-fiókba. Kezeli továbbá a mobilitási szolgáltatás automatikus telepítését a replikálni kívánt gépeken, és elvégzi a virtuális gépek automatikus felderítését a VMware-kiszolgálókon.
        - A fő célkiszolgáló az Azure-ból történő feladat-visszavétel során kezeli a replikációs adatokat.
    - A beállítás magában foglalja a konfigurációs kiszolgáló regisztrálását a tárolóban, a MySQL-kiszolgáló és a VMware-PowerCLI letöltését, valamint az automatikus felderítési és mobilitási szolgáltatás telepítéséhez létrehozott fiókok megadását.
4. **Cél környezet**: a cél Azure-környezet beállítása az Azure-előfizetés és a hálózati beállítások megadásával.
5. **Replikációs házirend**: megadhatja, hogyan történjen a replikálás. A beállítások közé tartozik a helyreállítási pontok létrehozásának és tárolásának gyakorisága, valamint az, hogy az alkalmazással konzisztens pillanatképeket kell-e létrehozni.
6. **Replikáció engedélyezése**. A helyszíni gépek replikálását engedélyezheti. Ha létrehozott egy fiókot a mobilitási szolgáltatás telepítéséhez, akkor a rendszer telepíti a számítógép replikálásának engedélyezésekor. 

*További segítségre van szüksége?*

- A lépések gyors áttekintéséhez kipróbálhatja a [VMware-oktatóanyagot](vmware-azure-tutorial.md)és a [fizikai kiszolgáló](physical-azure-disaster-recovery.md)bemutatóját.
- [További](vmware-azure-set-up-source.md) információ a forrás környezet beállításáról.
- [Ismerje meg](vmware-azure-deploy-configuration-server.md) a konfigurációs kiszolgáló követelményeit, és állítsa be a konfigurációs kiszolgálót egy OVF-sablonnal a VMware-replikációhoz. Ha valamilyen okból nem használhat sablont, vagy fizikai kiszolgálókat replikál, [kövesse ezeket az utasításokat](physical-azure-set-up-source.md#set-up-the-source-environment).
- [További](vmware-azure-set-up-target.md) információ a célként megadott beállításokról.
- [További információ](vmware-azure-set-up-replication.md) a replikációs szabályzat beállításáról.
- [Megtudhatja](vmware-azure-enable-replication.md) , hogyan engedélyezheti a replikációt, és hogyan [zárhat ki](vmware-azure-exclude-disk.md) lemezeket a replikációból.


## <a name="something-went-wrong-how-do-i-troubleshoot"></a>Valami hiba történt, hogyan lehet elhárítani a hibákat?

- Első lépésként próbálja meg a [központi telepítés figyelését](site-recovery-monitor-and-troubleshoot.md) , hogy ellenőrizze a replikált elemek, a feladatok és az infrastrukturális problémák állapotát, és azonosítsa az esetleges hibákat.
- Ha nem tudja befejezni a kezdeti replikálást, vagy a folyamatos replikáció nem a várt módon működik, [tekintse át ezt a cikket](vmware-azure-troubleshoot-replication.md) a gyakori hibákért és a hibaelhárítási tippekért.
- Ha problémákat tapasztal a mobilitási szolgáltatás a replikálni kívánt gépeken történő automatikus telepítésével kapcsolatban, tekintse át a [cikkben](vmware-azure-troubleshoot-push-install.md)szereplő gyakori hibákat.
- Ha a feladatátvétel nem a várt módon működik, tekintse meg [a cikk](site-recovery-failover-to-azure-troubleshoot.md)gyakori hibáit.
- Ha a feladat-visszavétel nem működik, ellenőrizze, hogy a probléma megjelenik- [e ebben a cikkben](vmware-azure-troubleshoot-failback-reprotect.md).



## <a name="next-steps"></a>Következő lépések

Ha a replikáció már megtörtént, a vész- [helyreállítási gyakorlat futtatásával](tutorial-dr-drill-azure.md) gondoskodhat arról, hogy a feladatátvétel a várt módon működjön. 
