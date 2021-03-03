---
title: SAP számítási feladatok tervezési és üzembe helyezési ellenőrzőlistája | Microsoft Docs
description: Ellenőrzőlista az SAP-munkaterhelések üzembe helyezésének megtervezéséhez az Azure-ban és a számítási feladatok üzembe helyezése
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 40d22a410054f17676c4d9db639fd5e32da18531
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101665983"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>SAP-munkaterhelések az Azure-ban: tervezési és üzembe helyezési ellenőrzőlista

Ez az ellenőrzőlista olyan ügyfelek számára készült, akik SAP NetWeaver-, S/4HANA-és Hybris-alkalmazásokat helyeznek üzembe az Azure-infrastruktúra szolgáltatásként. A projekt időtartama alatt az ügyfélnek és/vagy az SAP-partnernek át kell tekintenie az ellenőrzőlistát. Fontos megjegyezni, hogy számos ellenőrzés a projekt elején és a tervezési fázisban fejeződik be. Az üzembe helyezést követően az üzembe helyezett Azure-infrastruktúra vagy az SAP-szoftverek egyszerű módosításai összetettek lehetnek.

Tekintse át a legfontosabb mérföldkövek ellenőrzőlistáját a projekt során. Ez lehetővé teszi a kisebb problémák észlelését, mielőtt azok nagy problémákba lépnek. Emellett elegendő idő áll rendelkezésére a szükséges módosítások újbóli megtervezéséhez és teszteléséhez. Ne gondolja végig, hogy ez az ellenőrzőlista nem fejeződött be. Előfordulhat, hogy a helyzettől függően számos további ellenőrzést kell végrehajtania.

Az ellenőrzőlista nem tartalmazza az Azure-tól független feladatokat. Az SAP-alkalmazási felületek például az Azure platformra vagy egy szolgáltatóra való áttérés során változnak.

Ez az ellenőrzőlista a már üzembe helyezett rendszerek esetében is használható. Az új funkciók, például a írásgyorsító és a Availability Zones, és a telepítése óta új virtuálisgép-típusok is hozzáadhatók. Ezért érdemes rendszeresen áttekinteni az ellenőrzőlistát, hogy biztosan tisztában legyen az új funkciókkal az Azure platformon.

## <a name="project-preparation-and-planning-phase"></a>Projekt előkészítési és tervezési fázisa
Ebben a fázisban az SAP-munkaterhelések áttelepítését tervezzük az Azure platformon. Ebben a fázisban legalább a következő dokumentumokat kell létrehoznia, és meg kell határoznia az áttelepítés következő elemeit:

1. Magas szintű tervezési dokumentum. Ennek a dokumentumnak a következőket kell tartalmaznia:
    - Az SAP-összetevők és-alkalmazások aktuális leltára, valamint az Azure-hoz készült célalkalmazás-leltár.
    - Egy felelősség-hozzárendelési mátrix (RÁCOK), amely meghatározza az érintett felek feladatait és hozzárendeléseit. Kezdjen magas szinten, és a tervezés során és az első üzembe helyezésnél több részletességi szintet is dolgozhat.
    - Magas szintű megoldás-architektúra.
    - Döntés arról, hogy mely Azure-régiók telepíthetők a szolgáltatásba. Tekintse meg az [Azure-régiók listáját](https://azure.microsoft.com/global-infrastructure/regions/). Ha szeretné megtudni, hogy mely szolgáltatások érhetők el az egyes régiókban, tekintse meg a [régiók által elérhető termékeket](https://azure.microsoft.com/global-infrastructure/services/).
    - Egy hálózati architektúra, amellyel a helyszínről az Azure-ba kapcsolódhat. Ismerkedjen meg az Azure-hoz készült [Virtual Datacenter tervvel](/azure/architecture/vdc/).
    - A nagy hatású üzleti adatszolgáltatások Azure-ban való futtatásának biztonsági alapelvei. Az adatbiztonsággal kapcsolatos további információkért tekintse meg az [Azure Security dokumentációját](../../../security/index.yml).
2.  Technikai tervezési dokumentum. Ennek a dokumentumnak a következőket kell tartalmaznia:
    - A megoldáshoz tartozó blokk diagram.
    - A számítási, tárolási és hálózatkezelési összetevők méretezése az Azure-ban. Az Azure-beli virtuális gépek SAP-méretezéséhez lásd: [SAP 
    -  Megjegyzés #1928533] ( https://launchpad.support.sap.com/#/notes/1928533) .
    - Üzletmenet-folytonosság és vész-helyreállítási architektúra.
    - Részletes információk az operációs rendszer, az adatbázis, a kernel és az SAP támogatási csomagjának verzióiról. Nem feltétlenül igaz, hogy az SAP NetWeaver vagy az S/4HANA által támogatott összes operációsrendszer-kiadás támogatott az Azure-beli virtuális gépeken. Ugyanez érvényes az adatbázis-kezelői kiadások esetében is. Az SAP-és az Azure-támogatás biztosításához az alábbi forrásokból tájékozódhat, és szükség esetén frissítse az SAP-kiadásokat, az adatbázis-kezelői kiadásokat és az operációs rendszert Az SAP és az Azure által támogatott kiadási kombinációkat kell használnia az SAP és a Microsoft teljes körű támogatásához. Szükség esetén meg kell terveznie bizonyos szoftver-összetevők frissítését. A támogatott SAP-, operációsrendszer-és adatbázis-kezelő szoftverekkel kapcsolatos további részleteket a következő dokumentáció ismerteti:
        - [SAP-támogatás megjegyzés #1928533](https://launchpad.support.sap.com/#/notes/1928533). Ez a Megjegyzés az Azure-beli virtuális gépeken támogatott minimális operációsrendszer-kiadásokat határozza meg. Meghatározza továbbá a legtöbb nem HANA-adatbázishoz szükséges minimális adatbázis-kiadásokat is. Végezetül az SAP által támogatott Azure VM-típusok SAP-méretezését biztosítja.
        - [SAP-támogatás megjegyzés #2015553](https://launchpad.support.sap.com/#/notes/2015553). Ez a Megjegyzés az Azure Storage és a Microsoft által igényelt támogatási kapcsolatokra vonatkozó támogatási szabályzatokat határozza meg.
        - [SAP-támogatás megjegyzés #2039619](https://launchpad.support.sap.com/#/notes/2039619). Ez a Megjegyzés az Azure-hoz készült Oracle-támogatási mátrixot határozza meg. Az Oracle csak a Windows és Oracle Linux operációs rendszerként támogatja az Azure-beli SAP-munkaterheléseket. Ez a támogatási utasítás az SAP-példányokat futtató SAP-alkalmazás rétegére is vonatkozik. Az Oracle azonban nem támogatja a magas rendelkezésre állást az SAP központi szolgáltatásaihoz Oracle Linux a Pacemakeren keresztül. Ha a ASCS magas rendelkezésre állásra van szüksége a Oracle Linuxon, akkor a Linux rendszerhez készült SIOS Protection Suite-t kell használnia. A részletes SAP-tanúsítványokra vonatkozó információkért lásd: SAP-támogatás Megjegyzés [#1662610 – támogatás a Linux rendszerhez készült SIOS Protection Suite számára](https://launchpad.support.sap.com/#/notes/1662610). A Windows esetében az SAP által támogatott Windows Server feladatátvételi fürtszolgáltatás az SAP központi szolgáltatásokhoz az Oracle-sel együtt támogatott az adatbázis-kezelő rétegként.
        - [SAP-támogatás megjegyzés #2235581](https://launchpad.support.sap.com/#/notes/2235581). Ez a Megjegyzés a támogatási mátrixot ismerteti SAP HANA különböző operációsrendszer-kiadásokon.
        - SAP HANA által támogatott Azure-beli virtuális gépek és [HANA nagyméretű példányok](./hana-overview-architecture.md) listája az [SAP webhelyén](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)található.
        - [SAP-termék rendelkezésre állási mátrixa](https://support.sap.com/en/).
        - [SAP-támogatás Megjegyzés #2555629-SAP HANA 2,0 Dynamic rétegű – hypervisor és Cloud support](https://launchpad.support.sap.com/#/notes/2555629)
        - [SAP-támogatás Megjegyzés #1662610 – támogatás a SIOS Protection Suite for Linux rendszerhez](https://launchpad.support.sap.com/#/notes/1662610)
        - SAP-megjegyzések más SAP-specifikus termékekhez.     
    - Az SAP Central Services több SID-alapú fürtjének konfigurációjának használatával támogatott a Windows, a SLES és a RHEL vendég operációs rendszerek használata az Azure-ban. Ne feledje, hogy a Blast-sugár növelheti a több ASCS/SCS-t, amelyet egy ilyen multi-SID fürtön helyez el. A megfelelő vendég operációs rendszerekre vonatkozó dokumentáció a következő cikkekben található:
        - [SAP ASCS/SCS instance multi-SID magas rendelkezésre állás a Windows Server feladatátvételi fürtszolgáltatással és a megosztott lemezzel az Azure-ban](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md)
        - [SAP ASCS/SCS instance multi-SID magas rendelkezésre állás a Windows Server feladatátvételi fürtszolgáltatással és fájlmegosztás az Azure-ban](./sap-ascs-ha-multi-sid-wsfc-file-share.md)
        - [Magas rendelkezésre állás az SAP NetWeaver Azure-beli virtuális gépeken SUSE Linux Enterprise Server for SAP Applications multi-SID Guide](./high-availability-guide-suse-multi-sid.md)
        - [Magas rendelkezésre állás az SAP NetWeaver Azure-beli virtuális gépeken Red Hat Enterprise Linux for SAP Applications multi-SID Guide](./high-availability-guide-rhel-multi-sid.md)
    - Magas rendelkezésre állású és vész-helyreállítási architektúra.
        - A RTO és a RPO alapján határozza meg, hogy a magas rendelkezésre állást és a vész-helyreállítási architektúrát hogyan kell kinéznie.
        - Egy zónán belüli magas rendelkezésre álláshoz tekintse meg a kívánt adatbázis-kezelőt az Azure-ban. A legtöbb adatbázis-kezelő csomag szinkron módszert kínál a szinkron gyors készenléti állapothoz, amelyet az üzemi rendszerek esetében ajánlott. Tekintse meg az SAP-hez kapcsolódó dokumentációt a különböző adatbázisokhoz, az [Azure Virtual Machines adatbázis-kezelő üzembe helyezésével kapcsolatos megfontolások alapján az SAP-munkaterhelésekhez és a](./dbms_guide_general.md) kapcsolódó dokumentumokhoz.
           A Windows Server feladatátvételi fürtszolgáltatás az adatbázis-kezelő réteg megosztott lemezes konfigurációjának használatával, mint például a [SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server), nem támogatott. Ehelyett használjon például a következő megoldásokat:
           - [SQL Server Always On](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Oracle Data Guard](../oracle/configure-oracle-dataguard.md)
           - [HANA rendszerreplikáció](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - Az Azure-régiók közötti vész-helyreállításhoz tekintse át a különböző adatbázis-kezelői szolgáltatók által kínált megoldásokat. Ezek többsége támogatja az aszinkron replikálást vagy a naplózást.
        - Az SAP-alkalmazás réteg esetében döntse el, hogy futtatja-e az üzleti regressziós teszt rendszereit, amelyek ideális esetben az éles környezetek replikái, ugyanabban az Azure-régióban vagy a DR régióban. A második esetben megcélozhatja, hogy az üzleti regressziós rendszer az éles környezetekhez tartozó DR célként legyen kiemelve.
        - Ha úgy dönt, hogy nem helyezi el a nem üzemi rendszereket a DR-helyen, tekintse át Azure Site Recovery az SAP-alkalmazás rétegének Azure DR régióba való replikálásának módszerét. További információ: vész [-helyreállítás beállítása többrétegű SAP NetWeaver-alkalmazás telepítéséhez](../../../site-recovery/site-recovery-sap.md).
        - Ha úgy dönt, hogy a [Azure Availability Zones](../../../availability-zones/az-overview.md)használatával összevont HADR-konfigurációt használ, ismerkedjen meg az Azure-régiókkal, ahol Availability Zones elérhetők. Olyan korlátozásokat is figyelembe kell vennie, amelyek a két Availability Zones közötti hálózati késések miatt is bevezethetők.  
3.  Az összes SAP-csatoló (SAP és nem SAP) leltára.
4.  Az alapszolgáltatások kialakítása. A tervnek a következő elemeket kell tartalmaznia:
    - Active Directory és DNS-kialakítás.
    - Hálózati topológia az Azure-ban és különböző SAP-rendszerek hozzárendelése.
    - [Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC)](../../../role-based-access-control/overview.md) struktúra az Azure-ban infrastruktúrát és SAP-alkalmazásokat kezelő csapatok számára.
    - Erőforráscsoport-topológia.
    - [Címkézési stratégia](../../../azure-resource-manager/management/tag-resources.md#tags-and-billing).
    - A virtuális gépek és egyéb infrastruktúra-összetevők és/vagy logikai nevek elnevezési konvenciói.
5.  Microsoft Professional vagy Premier szintű támogatás szerződés. Ha Premier szintű támogatási szerződése van a Microsofttal, azonosítsa a Microsoft műszaki fiókkezelő (TAM) eszközét. Az SAP-támogatási követelményekkel kapcsolatban lásd: [SAP-támogatási megjegyzés #2015553](https://launchpad.support.sap.com/#/notes/2015553).
6.  Az előfizetésekhez tartozó Azure-előfizetések és a fő kvóta száma. Az [Azure-előfizetések kvótáinak igény szerinti növeléséhez nyissa meg a támogatási kérelmeket](../../../azure-portal/supportability/resource-manager-core-quotas-request.md) .
7.  Adatmennyiség-csökkentési és adatáttelepítési terv az SAP-beli Azure-ba való Migrálás során. Az SAP NetWeaver Systems esetében az SAP a nagy adatmennyiségek mennyiségének korlátozására vonatkozó útmutatást tartalmaz. Tekintse meg [ezt az SAP-útmutatót](https://wiki.scn.sap.com/wiki/download/attachments/247399467/DVM_%20Guide_7.2.pdf?version=1&modificationDate=1549365516000&api=v2) az SAP ERP-rendszerek adatkezelésével foglalkozó témakörben. Néhány tartalom általában a NetWeaver és S/4HANA rendszerekre is vonatkozik.
8.  Automatikus üzembe helyezési módszer. Az infrastruktúra Azure-beli üzembe helyezésének célja, hogy determinisztikus módon telepítsen, és determinisztikus eredményeket kapjon. Számos ügyfél PowerShell-vagy CLI-alapú parancsfájlokat használ. Vannak azonban olyan nyílt forráskódú technológiák, amelyek segítségével üzembe helyezheti az Azure-infrastruktúrát az SAP számára, és akár SAP-szoftvert is telepíthet. Példákat a GitHubon talál:
    - [Automatizált SAP-üzembe helyezések az Azure-felhőben](https://github.com/Azure/sap-hana)
    - [SAP HANA telepítés](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Az ügyfél, a rendszerintegrátor, a Microsoft és az egyéb érintett felek közötti rendszeres tervezési és üzembe helyezési felülvizsgálati lépésszám definiálása.


## <a name="pilot-phase-strongly-recommended"></a>Kísérleti fázis (erősen ajánlott)
 
A projektek tervezése és előkészítése előtt vagy közben is futtathat egy próbát. A kísérleti fázist is használhatja a tervezési és előkészítési fázisban végrehajtott megközelítések és kialakítások tesztelésére. És kiterjesztheti a próbaüzem fázisát, hogy valódi bizonyítékot hozzon a koncepcióra.

Javasoljuk, hogy a kísérleti üzembe helyezés során egy teljes HADR-megoldást és biztonsági tervet állítson be és ellenőrizzen. Néhány ügyfél skálázhatósági teszteket hajt végre ebben a fázisban. Más ügyfelek az SAP homokozó rendszerek üzembe helyezését kísérleti fázisként használják. Feltételezzük, hogy már azonosította az Azure-ba a próbaüzem előtt áttelepíteni kívánt rendszerét.

1. Optimalizálja az adatátvitelt az Azure-ba. Az optimális választás nagymértékben függ az adott forgatókönyvtől. A helyszíni átvitel az [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) -on keresztül a leggyorsabb, ha a ExpressRoute-áramkörnek elegendő sávszélessége van. Más helyzetekben az interneten keresztüli átvitel gyorsabb.
2. Egy heterogén SAP-platform áttelepítéséhez, amely adatexportálást és-importálást is magában foglal, tesztelje és optimalizálja az exportálási és importálási fázisokat. A nagyméretű áttelepítésekhez, amelyekben SQL Server a cél platform, [javaslatokat](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070)találhat. Az áttelepítési figyelőt/SWPM akkor használhatja, ha nincs szüksége kombinált kiadási frissítésre. Az SAP [DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) folyamata akkor használható, ha az áttelepítést egy SAP kiadási frissítéssel kombinálja. Ehhez meg kell felelnie bizonyos követelményeknek a forrás és a cél adatbázis-kezelő platform kombinációjának. Ez a folyamat a 2,0-es [összegű SP03 adatbázis-áttelepítési beállításában (DMO)](https://launchpad.support.sap.com/#/notes/2631152)van dokumentálva.
   1.  Exportálás a forrásba, a fájlok feltöltése az Azure-ba, és az importálási teljesítmény. Az Exportálás és az importálás közötti átfedés maximalizálása.
   2.  Értékelje ki az adatbázis mennyiségét a cél és a cél platformon az infrastruktúra méretezése érdekében.
   3.  Az időzítés ellenőrzése és optimalizálása.
1. Technikai ellenőrzés.
   1. VIRTUÁLIS gépek típusai.
        - Tekintse át az SAP-támogatási megjegyzések, a SAP HANA a hardver könyvtára és az SAP PAM-ban található erőforrásokat. Győződjön meg arról, hogy az Azure-beli támogatott virtuális gépek nem változtak, támogatott operációsrendszer-kiadások a virtuálisgép-típusokhoz, valamint a támogatott SAP-és adatbázis-kezelői kiadások.
        - Ellenőrizze újra az alkalmazás méretezését és az Azure-ban üzembe helyezett infrastruktúrát. Ha meglévő alkalmazásokat helyez át, akkor gyakran a szükséges SAP-t származtathatja a használt infrastruktúrából és az [SAP teljesítményteszt weblapjáról](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) , és összehasonlíthatja az [SAP-támogatási megjegyzésekben](https://launchpad.support.sap.com/#/notes/1928533)felsorolt sap-számokkal #1928533. Tartsa szem előtt [ezt a cikket a SAP-minősítések](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208) esetében is.
        - Értékelje ki és tesztelje az Azure-beli virtuális gépek méretezését a tervezési fázisban kiválasztott virtuálisgép-típusok maximális tárolási sebessége és hálózati átviteli sebessége tekintetében. Itt megtalálja az itt található adatfájlokat:
           -  [A Windows rendszerű virtuális gépek méretei az Azure-ban](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Fontos, hogy figyelembe vegye a *gyorsítótár nélküli lemez maximális átviteli sebességét* a méretezéshez.
           -  [A Linux rendszerű virtuális gépek méretei az Azure-ban](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Fontos, hogy figyelembe vegye a *gyorsítótár nélküli lemez maximális átviteli sebességét* a méretezéshez.
   2. Tárterület
        - Az [Azure Storage-beli tárolási típusok keresése az SAP-munkaterheléshez](./planning-guide-storage.md)
        - Legalább az [Azure standard SSD Storage](../../disks-types.md#standard-ssd) -t használja az SAP-alkalmazási rétegeket képviselő virtuális gépekhez, valamint olyan adatbázis-kezelők üzembe helyezéséhez, amelyek nem érzékenyek a teljesítményre.
        - Általánosságban elmondható, hogy az [Azure standard HDD-lemezek](../../disks-types.md#standard-hdd)használatát nem javasoljuk.
        - Az [Azure Premium Storage](../../disks-types.md#premium-ssd) bármely olyan adatbázis-kezelő virtuális gép esetében használható, amely távoli teljesítményre érzékeny.
        - Az [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)használata.
        - Az M-sorozatú adatbázis-kezelői naplók Azure írásgyorsító használhatók. Vegye figyelembe a írásgyorsító korlátozásait és használatát a [írásgyorsító](../../how-to-enable-write-accelerator.md)dokumentációjában leírtaknak megfelelően.
        - A különböző adatbázis-kezelői típusok esetében olvassa el az [általános SAP-vel kapcsolatos adatbázis-kezelői dokumentációt](./dbms_guide_general.md) , valamint az általános dokumentum az adatbázis-kezelői szolgáltatásra vonatkozó dokumentációját.
        - További információ a SAP HANAről: [SAP HANA infrastruktúra-konfigurációk és-műveletek az Azure](./hana-vm-operations.md)-ban.
        - Az eszköz AZONOSÍTÓjának használatával soha ne csatlakoztassa az Azure-beli adatlemezeket egy Azure-beli linuxos virtuális géphez. Ehelyett használja az univerzálisan egyedi azonosítót (UUID). Ügyeljen arra, hogy az Azure-adatlemezek csatlakoztatásához grafikus eszközöket használjon, például:. Ellenőrizze az/etc/fstab bejegyzéseit, és győződjön meg róla, hogy az UUID a lemezek csatlakoztatására szolgál. További részleteket [ebben a cikkben](../../linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk)talál.
   3. Hálózati.
        - Tesztelje és értékelje ki a virtuális hálózati infrastruktúrát és az SAP-alkalmazások elosztását a különböző Azure-beli virtuális hálózatokon keresztül.
        -  Értékelje ki a sugaras virtuális hálózati architektúra megközelítését, vagy az egyetlen Azure-beli virtuális hálózatban lévő szegmentálási megközelítést. Értékelés alapja:
               1. Az [Azure-beli virtuális hálózatok](../../../virtual-network/virtual-network-peering-overview.md)közötti adatcsere költségei. További információ a költségekről: [Virtual Network díjszabása](https://azure.microsoft.com/pricing/details/virtual-network/).
               2. Az Azure-beli virtuális hálózatok közötti társítás gyors leválasztásának előnyei, valamint a hálózati biztonsági csoport módosítása a virtuális hálózaton belüli alhálózat elkülönítésére. Ez a kiértékelés olyan esetekben használható, amikor a virtuális hálózat alhálózatán üzemeltetett alkalmazások vagy virtuális gépek biztonsági kockázatot jelentenek.
                3. A helyszíni, a külvilág és az Azure-ban létrehozott virtuális adatközpont közötti hálózati forgalom központi naplózása és naplózása.
        - Értékelje ki és tesztelje az adatelérési utat az SAP-alkalmazás réteg és az SAP adatbázis-kezelő réteg között.
            -  Az Azure-beli [hálózati virtuális berendezések](https://azure.microsoft.com/solutions/network-appliances/) elhelyezése az SAP-alkalmazás és az SAP NetWeaver, Hybris vagy S/4HANA alapú SAP-rendszerek adatbázis-kezelői rétege közötti kommunikációs útvonalon nem támogatott.
            -  Az SAP-alkalmazás rétegének és az SAP adatbázis-kezelők különböző Azure-beli virtuális hálózatokban való elhelyezése nem támogatott.
            -  Az [alkalmazás biztonsági csoportja és a hálózati biztonsági csoport szabályai](../../../virtual-network/network-security-groups-overview.md) segítségével megadhatja az SAP-alkalmazás rétege és az SAP adatbázis-kezelő réteg közötti útvonalakat.
        - Győződjön meg arról, hogy az [Azure gyorsított hálózatkezelés](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) engedélyezve van az SAP-alkalmazás rétegében és az SAP adatbázis-kezelő rétegében használt virtuális gépeken. Ne feledje, hogy az Azure-ban a gyorsított hálózatkezelés támogatásához különböző operációsrendszer-szintekre van szükség:
            - Windows Server 2012 R2 vagy újabb.
            - SUSE Linux 12 SP3 vagy újabb verzió.
            - RHEL 7,4 vagy újabb.
            - Oracle Linux 7,5. Ha a RHCKL kernelt használja, a Release 3.10.0-862.13.1. el7 szükséges. Ha az Oracle UEK-kernelt használja, az 5. kiadás szükséges.
        - Tesztelje és értékelje ki a hálózati késést az SAP alkalmazási rétegbeli virtuális gépek és az adatbázis-kezelő virtuális gépek között az SAP-támogatási megjegyzések [#500235](https://launchpad.support.sap.com/#/notes/500235) és [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)alapján. Értékelje ki az eredményeket a hálózati késéssel kapcsolatos útmutatóban az [SAP-támogatási megjegyzés #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A hálózati késésnek közepes vagy jó tartományban kell lennie. A kivételek a virtuális gépek és a HANA nagyméretű példányok közötti forgalomra vonatkoznak, a [jelen cikkben](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)leírtak szerint.
        - Győződjön meg arról, hogy a ILB központi telepítései a közvetlen kiszolgáló visszaadását használják. Ez a beállítás csökkenti a késést, ha az Azure ILB az adatbázis-kezelő réteg magas rendelkezésre állású konfigurációi esetében használják.
        - Ha Azure Load Balancer a Linux vendég operációs rendszerekkel együtt használja, ellenőrizze, hogy a Linux hálózati **net.IPv4.tcp_timestamps** paraméter értéke **0**. Ez az ajánlás ütközik az [SAP megjegyzés #2382421](https://launchpad.support.sap.com/#/notes/2382421)régebbi verzióiban található javaslatokkal. Az SAP-Megjegyzés frissítve lett azzal az állapottal, hogy ez a paraméter **0** értékűre van állítva az Azure Load balancerrel való együttműködéshez.
        - Érdemes lehet az [Azure Proximity-elhelyezési csoportokat](../../co-location.md) használni az optimális hálózati késés érdekében. További információ: [Azure Proximity-elhelyezési csoportok optimális hálózati késéshez SAP-alkalmazásokkal](sap-proximity-placement-scenarios.md).
   4. Magas rendelkezésre állású és vész-helyreállítási üzemelő példányok.
        - Ha egy adott Azure rendelkezésre állási zóna meghatározása nélkül helyezi üzembe az SAP-alkalmazás rétegét, győződjön meg arról, hogy minden olyan virtuális gép, amely az SAP-párbeszédpanelek példányait vagy az egyetlen SAP-rendszer összes közbenső példányát futtatja egy [rendelkezésre állási csoportba](../../manage-availability.md)
        - Ha nincs szüksége magas rendelkezésre állásra az SAP Central Services és az adatbázis-kezelő rendszer számára, akkor ezeket a virtuális gépeket az SAP-alkalmazás rétegével megegyező rendelkezésre állási csoportba helyezheti.
        - Ha a magas rendelkezésre állás érdekében a passzív replikáció révén védi az SAP központi szolgáltatásait és az adatbázis-kezelő réteget, helyezze el a két csomópontot az SAP Central Services számára egy különálló rendelkezésre állási csoportba, illetve egy másik rendelkezésre állási csoport két adatbázis-kezelő csomópontját.
        - Ha Azure Availability Zonesbe helyez üzembe, nem használhatja a rendelkezésre állási csoportokat. Azonban gondoskodnia kell arról, hogy az aktív és a passzív központi szolgáltatások csomópontjait két különböző Availability Zones telepítse. Használjon olyan Availability Zones, amely a legkisebb késéssel rendelkezik.
          Ne feledje, hogy az [Azure standard Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md) -t kell használnia, ha Windows vagy pacemaker feladatátvételi fürtöket kíván létrehozni az adatbázis-kezelő szolgáltatás és az SAP központi szolgáltatások rétegében Availability Zones között. A zónákhoz való központi telepítéshez nem használhatók [Alapszintű Load Balancer](../../../load-balancer/load-balancer-overview.md) .
   5. Időtúllépési beállítások.
        - Ellenőrizze az SAP-példányok SAP NetWeaver fejlesztői nyomkövetését, és győződjön meg arról, hogy a sorba helyezni-kiszolgáló és az SAP-munkafolyamatok között nincsenek kapcsolódási megszakítások. A következő két beállításjegyzék-paraméter beállításával elkerülhető a kapcsolatok megszakítása:
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000. További információ: [KeepAliveTime](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)).
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000. További információ: [KeepAliveInterval](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)).
        - Ha el szeretné kerülni az Azure-ban üzembe helyezett, helyszíni SAP GUI-felületek és SAP-alkalmazások rétegei közötti GUI-időtúllépést, akkor győződjön meg arról, hogy ezek a paraméterek be vannak-e állítva az alapértelmezett. PFL vagy a példány profiljában:
            - rdisp/keepalive_timeout = 3600
            - rdisp/életben tartás = 20
        - Az SAP-sorba helyezni folyamat és az SAP-munkafolyamatok közötti létesített kapcsolatok megzavarásának megelőzése érdekében a **enque/encni/set_so_keepalive** paramétert **igaz** értékre kell állítani. Lásd még: [SAP-megjegyzés #2743751](https://launchpad.support.sap.com/#/notes/2743751).  
        - Ha Windows feladatátvevő fürtöt használ, győződjön meg arról, hogy a nem válaszoló csomópontokra való reagálás időpontja helyesen van beállítva az Azure-hoz. A [feladatátvételi fürt hálózati küszöbértékeit](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) felsoroló cikk felsorolja a paramétereket és azt, hogy azok hogyan befolyásolják a feladatátvételi érzékenységet. Ha feltételezi, hogy a fürtcsomópontok ugyanabban az alhálózatban vannak, akkor módosítania kell a következő paramétereket:
            - SameSubNetDelay = 2000
            - SameSubNetThreshold = 15
            - RoutingHistorylength = 30
    6. OPERÁCIÓSRENDSZER-beállítások vagy javítások
        - A HANA az SAP-on való futtatásához olvassa el a következő megjegyzéseket és dokumentációkat:
            -   [SAP-támogatás Megjegyzés #2814271 – SAP HANA biztonsági mentés az Azure-ban sikertelen, ellenőrzőösszeg-hiba esetén](https://launchpad.support.sap.com/#/notes/2814271)
            -   [SAP-támogatás Megjegyzés #2753418 – lehetséges teljesítmény-romlás az időzítő tartaléka miatt](https://launchpad.support.sap.com/#/notes/2753418)
            -   [SAP-támogatás Megjegyzés #2791572 – teljesítmény romlása az Azure-beli Hyper-V VDSO-támogatásának hiánya miatt](https://launchpad.support.sap.com/#/notes/2791572)
            -   [SAP-támogatás Megjegyzés #2382421 – a hálózati konfiguráció optimalizálása a HANA-és az operációs rendszer szintjén](https://launchpad.support.sap.com/#/notes/2382421)
            -   [SAP-támogatás Megjegyzés #2694118 – Red Hat Enterprise Linux HA Add-On az Azure-ban](https://launchpad.support.sap.com/#/notes/2694118)
            -   [SAP-támogatás Megjegyzés #1984787 – SUSE LINUX Enterprise Server 12: telepítési megjegyzések](https://launchpad.support.sap.com/#/notes/1984787)
            -   [SAP-támogatás Megjegyzés #2002167-Red Hat Enterprise Linux 7. x: telepítés és frissítés](https://launchpad.support.sap.com/#/notes/0002002167)
            -   [SAP-támogatás Megjegyzés #2292690-SAP HANA DB: ajánlott operációsrendszer-beállítások a RHEL 7 rendszerhez](https://launchpad.support.sap.com/#/notes/0002292690)
            -   [SAP-támogatás Megjegyzés #2772999-Red Hat Enterprise Linux 8. x: telepítés és konfigurálás](https://launchpad.support.sap.com/#/notes/2772999)
            -   [SAP-támogatás Megjegyzés #2777782-SAP HANA DB: ajánlott operációsrendszer-beállítások a RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
            -   [SAP-támogatás Megjegyzés #2578899-SUSE Linux Enterprise Server 15: telepítési Megjegyzés](https://launchpad.support.sap.com/#/notes/2578899)
            -   [SAP-támogatás Megjegyzés #2455582-Linux: SAP-alkalmazások futtatása a GCC 6. x-szel](https://launchpad.support.sap.com/#/notes/0002455582)
            -    [SAP-támogatási Megjegyzés #2729475 – a HWCCT nem sikerült – a "hypervisor nem támogatott" hibaüzenetet a SAP HANA számára tanúsított Azure-beli virtuális gépeken](https://launchpad.support.sap.com/#/notes/2729475)
1. Tesztelje a magas rendelkezésre állást és a vész-helyreállítási eljárásokat.
   1. Feladatátvételi helyzetek szimulálása a virtuális gépek (Windows vendég operációs rendszerek) leállításával vagy az operációs rendszerek pánik módba helyezésével (Linux vendég operációs rendszerek). Ez a lépés segít kideríteni, hogy a feladatátvételi konfigurációk a tervezettnek megfelelően működnek-e.
   1. Azt méri, hogy mennyi ideig tart a feladatátvétel végrehajtása. Ha az idő túl hosszú, vegye figyelembe a következőket:
        - SUSE Linux esetén a feladatátvétel felgyorsításához használjon SBD-eszközöket az Azure kerítés ügynök helyett.
        - SAP HANA esetén, ha az Újratöltés túl sokáig tart, érdemes lehet több tárolási sávszélességet kiépíteni.
   3. Tesztelje a biztonsági mentési/visszaállítási sorozatot és az időzítést, és végezze el a szükséges javítási műveleteket. Győződjön meg arról, hogy a biztonsági mentési idők elegendőek. A visszaállítási és az idő-visszaállítási tevékenységeket is tesztelni kell. Győződjön meg arról, hogy a visszaállítási időpontok a RTO SLA-n belül vannak, ahol a RTO adatbázis vagy virtuális gép visszaállítási folyamatán alapul.
   4. Több régióra kiterjedő DR funkció és architektúra tesztelése.
1. Biztonsági ellenőrzések.
   1. Tesztelje az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) architektúrájának érvényességét. A cél az, hogy elkülönítse és korlátozza a különböző csapatok hozzáférését és engedélyeit. Az SAP-csapat tagjai például telepíthetnek virtuális gépeket, és az Azure Storage-ból lemezeket rendelhetnek egy adott Azure virtuális hálózatban. Az SAP-alapú csapat azonban nem hozhat létre saját virtuális hálózatokat, és nem módosíthatja a meglévő virtuális hálózatok beállításait. A hálózati csapat tagjai nem telepíthetnek virtuális gépeket olyan virtuális hálózatokra, amelyekben az SAP-alkalmazás és az adatbázis-kezelő virtuális gépek futnak. A csapat tagjai nem változtathatják meg a virtuális gépek attribútumait, vagy akár virtuális gépeket vagy lemezeket is törölhetnek.  
   1.  Ellenőrizze, hogy a [hálózati biztonsági csoport és az ASC](../../../virtual-network/network-security-groups-overview.md) -szabályok a várt módon működnek-e, és védi a védett erőforrásokat.
   1.  Győződjön meg arról, hogy az összes titkosítani kívánt erőforrás titkosítva van. Megadhatja és implementálhatja a tanúsítványok biztonsági mentésére, tárolására és elérésére szolgáló folyamatokat, és visszaállíthatja a titkosított entitásokat.
   1.  Az operációsrendszer-lemezek [Azure Disk Encryption](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md) használata, ha lehetséges, az operációs rendszer által támogatott nézetből.
   1.  Ügyeljen arra, hogy ne használjon túl sok titkosítási réteget. Bizonyos esetekben érdemes Azure Disk Encryption együtt használni az adatbázis-kezelői transzparens adattitkosítás módszerek egyikével, hogy az ugyanazon a kiszolgálón lévő különböző lemezeket vagy összetevőket védjék.  Például egy SAP adatbázis-kezelő kiszolgálón a Azure Disk Encryption (ADE) engedélyezhető az operációs rendszer rendszerindító lemezén (ha az operációs rendszer támogatja az ADE-t), és azokat az adatlemezeket, amelyeket az adatbázis-kezelői adatmegőrzési fájlok nem használnak.  Erre példa az az ADE használata az adatbázis-kezelő TDE titkosítási kulcsait tároló lemezen.
1. Teljesítmény tesztelése. Az SAP-ben SAP-nyomkövetés és-mérések alapján végezze el az alábbi összehasonlításokat:
   - Ha szükséges, hasonlítsa össze az első 10 online jelentést a jelenlegi megvalósításával.
   - Ha alkalmazható, hasonlítsa össze az első 10 batch-feladatot a jelenlegi implementációval.
   - Összehasonlíthatja az adatátvitelt a felületeken keresztül az SAP-rendszeren. Olyan felületekre koncentrálhat, amelyekről tudja, hogy az átvitel különböző helyszíneken zajlik, például a helyszínről az Azure-ba.


## <a name="non-production-phase"></a>Nem éles fázis 
Ebben a fázisban feltételezzük, hogy a sikeres próbaüzem vagy a koncepció igazolása (POC) után a nem éles környezetben üzemelő SAP-rendszerek üzembe helyezése az Azure-ban folyamatban van. Vegyen fel mindent, amit megtanult és tapasztalt a POC-ben erre az üzembe helyezésre. A Pócsre vonatkozó összes feltétel és lépés erre a központi telepítésre is vonatkozik.

Ebben a fázisban általában fejlesztési rendszereket, egység-tesztelési rendszereket és üzleti regressziós tesztelési rendszereket helyez üzembe az Azure-ban. Javasoljuk, hogy az egyik SAP-alkalmazásban legalább egy nem éles rendszernek megfelelő, magas rendelkezésre állású konfigurációt biztosítson a jövőbeli üzemi rendszer számára. Az alábbiakban néhány további lépést is végre kell hajtania ebben a fázisban:  

1.  Mielőtt áthelyezi a rendszereket a régi platformról az Azure-ba, Gyűjtse össze az erőforrás-használati adatokat, például a CPU-használatot, a tárolási teljesítményt és a IOPS adatokat. Különösen ezeket az adatokat az adatbázis-kezelő réteg egységei gyűjtik, de az alkalmazási rétegből is gyűjthetik. A hálózati és a tárolási késést is méri.
2.  Rögzítse a rendszerek rendelkezésre állási használati idejét. A cél az, hogy kiderítse, hogy a nem éles rendszerű rendszereket kell-e minden nap elérhetővé tennie, illetve hogy vannak-e olyan nem üzemi rendszerek, amelyek egy hét vagy hónap bizonyos fázisaiban leállíthatók.
3.  Tesztelje és határozza meg, hogy szeretné-e saját operációsrendszer-lemezképeket létrehozni a virtuális gépekhez az Azure-ban, illetve hogy szeretne-e lemezképet használni az Azure Shared Image Gallery használatával. Ha a megosztott rendszerkép-katalógusból rendszerképet használ, ügyeljen arra, hogy olyan rendszerképet használjon, amely az operációs rendszer forgalmazójával tartozó támogatási szerződést tükrözi. Egyes operációsrendszer-gyártók esetében a megosztott képkatalógus lehetővé teszi a saját licencek rendszerképének megkeresését. Más operációsrendszer-lemezképek esetében az Azure által jegyzett ár tartalmazza a támogatást. Ha úgy dönt, hogy saját operációsrendszer-lemezképeket hoz létre, a következő cikkekben talál dokumentációt:
    -   [Az Azure-ban üzembe helyezett, Windows rendszerű virtuális gépek általánosított rendszerképének összeállítása](../../windows/capture-image-resource.md)
    -   [Az Azure-ban üzembe helyezett Linux rendszerű virtuális gépek általánosított rendszerképének összeállítása](../../linux/capture-image.md)
3.  Ha a megosztott rendszerkép-katalógusban SUSE és Red Hat Linux rendszerképeket használ, a megosztott lemezképek katalógusában található Linux-gyártók által biztosított SAP-lemezképeket kell használnia.
4.  Győződjön meg arról, hogy megfelel a Microsoft támogatási szerződésekre vonatkozó SAP-támogatás követelményeinek. Lásd: [SAP-támogatás megjegyzés #2015553](https://launchpad.support.sap.com/#/notes/2015553). A HANA nagyméretű példányaival kapcsolatban lásd: előkészítési [követelmények](./hana-onboarding-requirements.md).
4.  Győződjön meg arról, hogy a megfelelő személyek [tervezett karbantartási értesítéseket](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/) kapnak, hogy kiválassza a legjobb állásidőt.
5.  Az Azure-bemutatók gyakori keresése olyan csatornákon, mint például a [Channel 9](https://channel9.msdn.com/) az üzembe helyezésekre esetlegesen alkalmazható új funkciók esetében.
6.  Tekintse át az Azure-hoz kapcsolódó SAP-megjegyzéseket, például a [támogatási megjegyzések #1928533ét](https://launchpad.support.sap.com/#/notes/1928533), az új VM SKU-ket és az újonnan támogatott operációsrendszer-és adatbázis-verziókat Hasonlítsa össze az új virtuálisgép-típusok díjszabását a régebbi virtuálisgép-típusoknál, így a legjobb ár/teljesítmény aránysal telepítheti a virtuális gépeket.
7.  Az SAP-támogatási megjegyzések, a SAP HANA hardver könyvtára és az SAP PAM ismételt ellenőrzését. Győződjön meg arról, hogy az Azure-beli támogatott virtuális gépeken nem történt változás, támogatott operációsrendszer-kiadások a virtuális gépeken, valamint a támogatott SAP-és adatbázis-kezelői kiadások.
8.  Az Azure-ban az új HANA-tanúsítvánnyal rendelkező SKU-ket az [SAP webhelyén találja](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) . Hasonlítsa össze az új SKU-ket a használni tervezettek díjszabásával. Végül végezze el a szükséges módosításokat a legjobb ár/teljesítmény aránnyal rendelkezők használatához.
9.  Az üzembe helyezési parancsfájlok új virtuálisgép-típusok használatára való hozzáigazítása és a használni kívánt új Azure-szolgáltatások beépítése.
10. Az infrastruktúra üzembe helyezése után tesztelje és értékelje ki a hálózati késést az SAP-alkalmazások és az adatbázis-kezelő virtuális gépek között, az SAP-támogatási megjegyzések [#500235](https://launchpad.support.sap.com/#/notes/500235) és [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)alapján. Értékelje ki az eredményeket a hálózati késéssel kapcsolatos útmutatóban az [SAP-támogatási megjegyzés #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A hálózati késésnek közepes vagy jó tartományban kell lennie. A kivételek a virtuális gépek és a HANA nagyméretű példányok közötti forgalomra vonatkoznak, a [jelen cikkben](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)leírtak szerint. Győződjön meg arról, hogy az [azure Virtual Machines adatbázis-kezelő rendszerbe állításához az SAP-munkaterhelések](./dbms_guide_general.md#azure-network-considerations) , valamint az [Azure-SAP HANA infrastruktúra-konfigurációk és-műveletek](./hana-vm-operations.md) egyike sem vonatkozik az üzemelő példányra.
11. Győződjön meg arról, hogy a virtuális gépek a megfelelő [Azure közelségi elhelyezési csoportba](../../co-location.md)vannak telepítve, az [Azure Proximity-elhelyezési csoportok az SAP-alkalmazásokkal való optimális hálózati késés](sap-proximity-placement-scenarios.md)érdekében című témakörben leírtak szerint.
11. A számítási feladatok alkalmazása előtt végezze el az összes többi, a koncepció igazolására szolgáló szakaszt.
12. A számítási feladatok alkalmazása esetén jegyezze fel a rendszerek erőforrás-felhasználását az Azure-ban. Hasonlítsa össze ezt a felhasználást a régi platform rekordjaival. A jövőbeli központi telepítések VM-méretezésének módosítása, ha úgy látja, hogy nagy különbségek vannak. Ne feledje, hogy ha a virtuális gépek számának csökkentése, tárolása és hálózati sávszélessége is csökken.
    - [A Windows rendszerű virtuális gépek méretei az Azure-ban](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [Linuxos virtuális gépek méretei az Azure-ban](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Kísérletezzen a rendszermásolási funkciókkal és folyamatokkal. A cél az, hogy megkönnyítse egy fejlesztési rendszer vagy egy tesztelési rendszer másolását, így a Project csapatok gyorsan új rendszerekhez juthatnak. 
14. Optimalizálja és élesítse a csapat Azure szerepköralapú hozzáférését, engedélyeit és folyamatait, hogy megbizonyosodjon róla, hogy a feladatok elkülönítése megtörtént. Ugyanakkor győződjön meg arról, hogy az összes csapat el tudja végezni a feladatait az Azure-infrastruktúrában.
15. Magas rendelkezésre állású és vész-helyreállítási eljárások gyakorlása, tesztelése és dokumentálása, amelyek lehetővé teszik, hogy a munkatársak végrehajtsák ezeket a feladatokat. Azonosítsa a hiányosságokat, és alkalmazkodjon az új Azure-funkciókhoz, amelyeket integrál az üzembe helyezésbe.


## <a name="production-preparation-phase"></a>Éles előkészítési fázis 
Ebben a fázisban gyűjti a tapasztalt és megtanult adatokat a nem éles környezetben üzemelő környezetekben, és alkalmazza azt a jövőbeli éles üzembe helyezésekre. Elő kell készítenie a jelenlegi üzemeltetési hely és az Azure közötti adatátvitel folyamatát is.

1.  Az Azure-ba való áttérés előtt fejezze be az éles rendszerek szükséges SAP-kiadásának frissítését.
1.  Fogadja el az üzleti tulajdonosokat a működési és üzleti teszteken, amelyeket az éles rendszer áttelepítése után kell elvégezni.
1.  Győződjön meg arról, hogy ezek a tesztek a jelenlegi üzemeltetési helyen lévő forrásoldali rendszerekkel zárulnak. Kerülje a tesztek elvégzését a rendszer az Azure-ba történő első áthelyezése után.
1.  Tesztelje az éles rendszerek Azure-ba való áttelepítésének folyamatát. Ha nem helyezi át az összes éles rendszert az Azure-ba ugyanabban az időkeretben, hozzon létre olyan éles rendszerű csoportokat, amelyeknek azonos üzemeltetési helyen kell lenniük. Az adatáttelepítés tesztelése. Íme néhány gyakori módszer:
    - Olyan adatbázis-kezelői módszereket használhat, mint például a Backup/Restore a SQL Server always on, a HANA rendszerreplikáció vagy a napló szállítása az Azure-ban, és szinkronizálja az adatbázis tartalmát.
    - Kisebb adatbázisok biztonsági mentésének és visszaállításának használata.
    - Heterogén Migrálás végrehajtásához használja az SAP-SWPM integrált SAP Migration monitort.
    - Ha az áttelepítést SAP-kiadási frissítéssel kell kombinálni, használja az [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) folyamatot. Ne feledje, hogy a forrás-adatbázis-kezelő és a cél adatbázis-kezelő összes kombinációja nem támogatott. A DMO különböző kiadásaira vonatkozó SAP-támogatási megjegyzésekben talál további információt. Például: [adatbázis-áttelepítési beállítás (DMO), SUM 2,0 SP04](https://launchpad.support.sap.com/#/notes/2644872).
    - Ellenőrizze, hogy az adatátvitel sebessége jobb-e az interneten vagy a ExpressRoute keresztül, ha biztonsági másolatokat vagy SAP-exportálási fájlokat kell áthelyeznie. Ha az interneten keresztül helyezi át az adatátvitelt, előfordulhat, hogy módosítania kell néhány hálózati biztonsági csoport/alkalmazás biztonsági csoportra vonatkozó szabályt, amelyekre szüksége lesz a jövőbeli éles rendszerekhez.
1.  Mielőtt a régi platformról az Azure-ba helyezi a rendszereket, Gyűjtse össze az erőforrás-felhasználási adatokat. A hasznos adatok közé tartoznak a CPU-használat, a tárolási sebesség és a IOPS-adatok. Különösen ezeket az adatokat az adatbázis-kezelő réteg egységei gyűjtik, de az alkalmazási rétegből is gyűjthetik. A hálózati és a tárolási késést is méri.
1.  Az SAP-támogatási megjegyzések és a szükséges operációsrendszer-beállítások, a SAP HANA hardverek és az SAP PAM újrajelölése. Győződjön meg arról, hogy az Azure-beli támogatott virtuális gépeken nem történt változás, támogatott operációsrendszer-kiadások a virtuális gépeken, valamint a támogatott SAP-és adatbázis-kezelői kiadások.
1.  Frissítse az üzembe helyezési parancsfájlokat, hogy figyelembe vegye a virtuálisgép-típusokon és az Azure-funkciókon elvégzett legújabb döntéseket.
1.  Az infrastruktúra és az alkalmazások üzembe helyezése után ellenőrizze az alábbiakat:
    - A megfelelő virtuálisgép-típusok üzembe helyezése a megfelelő attribútumokkal és tárolási méretekkel.
    - A virtuális gépek a megfelelő és a kívánt operációsrendszer-kiadásokkal és-javításokkal rendelkeznek, és egységesek.
    - A virtuális gépeket szükség szerint és egységes módon kell megerősíteni.
    - Az alkalmazások helyes kiadásainak és javításának telepítése és telepítése megtörtént.
    - A virtuális gépek üzembe helyezése az Azure rendelkezésre állási csoportjaiba történt a tervezett módon.
    - Az Azure Premium Storage a késésre érzékeny lemezekhez használatos, vagy a [99,9%-os egyszeri virtuális gépre vonatkozó SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) -t kell megadni.
    - Az Azure írásgyorsító megfelelően van telepítve.
        - Győződjön meg arról, hogy a virtuális gépeken belül a tárolóhelyek és a csíkkészletek megfelelően lettek kiépítve a írásgyorsítót igénylő lemezeken.
        - A [szoftveres RAID konfigurációjának megtekintése Linux rendszeren](/previous-versions/azure/virtual-machines/linux/configure-raid).
        - Az [LVM konfigurálása az Azure-beli Linux rendszerű virtuális gépeken](/previous-versions/azure/virtual-machines/linux/configure-lvm).
    - Az [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) használata kizárólag.
    - A virtuális gépek üzembe helyezése a megfelelő rendelkezésre állási csoportokban és Availability Zones.
    - Az [Azure gyorsított hálózatkezelés](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) engedélyezve van az SAP-alkalmazás rétegében és az SAP adatbázis-kezelő rétegében használt virtuális gépeken.
    - Nem található Azure-beli [hálózati virtuális készülék](https://azure.microsoft.com/solutions/network-appliances/) az SAP-alkalmazás és az SAP NetWeaver, a Hybris vagy a S/4HANA alapú SAP-rendszerek adatbázis-kezelő rétegének kommunikációs útvonalán.
    - Az alkalmazás biztonsági csoportja és a hálózati biztonsági csoport szabályai lehetővé teszik a kommunikációt a kívánt módon, illetve a tervezett és a kommunikációt, ha szükséges.
    - Az időtúllépési beállítások a korábban leírtaknak megfelelően vannak beállítva.
    - A virtuális gépek üzembe helyezése a megfelelő [Azure Proximity-elhelyezési csoportba](../../co-location.md)történik, az Azure-beli [Proximity-elhelyezési csoportok az SAP-alkalmazásokkal való optimális hálózati késés](sap-proximity-placement-scenarios.md)érdekében.
    - Az SAP-alkalmazások és az adatbázis-kezelő rendszerű virtuális gépek közötti hálózati késés az SAP-támogatási megjegyzések [#500235](https://launchpad.support.sap.com/#/notes/500235) és [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)című témakörben leírtak szerint lett tesztelve és érvényesítve Értékelje ki az eredményeket a hálózati késéssel kapcsolatos útmutatóban az [SAP-támogatási megjegyzés #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A hálózati késésnek közepes vagy jó tartományban kell lennie. A kivételek a virtuális gépek és a HANA nagyméretű példányok közötti forgalomra vonatkoznak, a [jelen cikkben](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)leírtak szerint.
    - A titkosítás szükség esetén, és a megfelelő titkosítási módszerrel lett implementálva.
    - A felületek és más alkalmazások összekapcsolhatják az újonnan üzembe helyezett infrastruktúrát.
1.  Hozzon létre egy forgatókönyvet a tervezett Azure-karbantartásra való reagáláshoz. Határozza meg azt a sorrendet, amelyben a rendszereknek és a virtuális gépeknek újra kell indítaniuk a tervezett karbantartást.
    

## <a name="go-live-phase"></a>Go-Live fázis
A Go-Live fázisban mindenképpen kövesse a korábbi fázisokban fejlesztett forgatókönyveket. Hajtsa végre a tesztelt és gyakorlott lépéseket. Ne fogadja el az utolsó perces módosításokat a konfigurációkban és a folyamatokban. Hajtsa végre a következő lépéseket is:

1. Ellenőrizze, hogy a Azure Portal figyelési és egyéb figyelési eszközök működnek-e. Javasoljuk, hogy a Windows Teljesítményfigyelőt (Perfmon) a Linux rendszerhez és a SAR-hoz.
    - CPU-számlálók.
        - Átlagos CPU-idő, összesen (minden processzor)
        - Átlagos CPU-idő, minden egyes processzor (128 processzor a M128 virtuális gépeken)
        - CPU kernel-idő, minden egyes processzor
        - CPU felhasználói idő, minden egyes processzor
    - Memory.
        - Szabad memória
        - Memória lap/másodperc
        - Memória lap kimenő/második
    - Lemez.
        - Lemez olvasása kbit/s-ban, külön lemezenként
        - Lemez olvasása/másodperc, egyéni lemezenként
        - Lemez olvasása a másodpercenkénti/olvasási, egyenkénti lemezen
        - Lemez írása kbit/s-ban, külön lemezenként
        - Lemez írása/másodperc, egyéni lemezenként
        - Lemezes írás a másodpercenkénti/olvasási, egyenkénti lemezen
    - Hálózat.
        - Hálózati csomagok/másodperc
        - Kimenő hálózati csomagok/másodperc
        - Hálózati KB/másodperc
        - Hálózati KB kimenő/másodperc
1.  Az adatok áttelepítése után végezze el az összes olyan ellenőrző tesztet, amelyet a vállalat tulajdonosai elfogadtak. Az érvényesítési teszt eredményét csak akkor fogadja el, ha az eredeti forrásoldali rendszerek eredményeivel rendelkezik.
1.  Győződjön meg arról, hogy a felületek működnek-e, és hogy más alkalmazások képesek-e kommunikálni az újonnan telepített éles rendszerekkel.
1.  Az SAP Transaction STM használatával vizsgálja meg a szállítási és a javítási rendszereket.
1.  Az adatbázis biztonsági mentését a rendszer éles környezetben való kiadása után végezze el.
1.  Végezze el a virtuális gépek biztonsági mentését az SAP-alkalmazás rétegének virtuális gépei számára a rendszer éles környezetben való kiadása után.
1.  Az olyan SAP-rendszerek esetében, amelyek nem részei a jelenlegi Go-Live fázisnak, de kommunikálnak az Azure-ba a Go-Live fázisban áthelyezett SAP-rendszerekkel, alaphelyzetbe kell állítania az állomásnév pufferét a SM51-ben. Ezzel a művelettel eltávolítja az Azure-ba áthelyezett alkalmazás-példányok neveihez társított régi gyorsítótárazott IP-címeket.  


## <a name="post-production"></a>Utómunka
Ez a fázis a rendszer figyelésére, üzemeltetésére és felügyeletére szolgál. SAP-szempontból a régi üzemeltetési helyen való befejezéshez szükséges szokásos feladatok érvényesek. Végezze el az alábbi Azure-specifikus feladatokat is:

1. Tekintse át az Azure-számlákat a nagy díjszabású rendszerekhez.
2. Optimalizálja az ár/teljesítmény hatékonyságát a virtuális gép oldalán és a tárolási oldalon.
3. Optimalizálja a rendszerek leállításának időpontját.  


## <a name="next-steps"></a>Következő lépések
Olvassa el a következő cikkeket:

- [Azure Virtual Machines az SAP NetWeaver tervezése és megvalósítása](./planning-guide.md)
- [Azure Virtual Machines üzembe helyezés az SAP NetWeaver-ben](./deployment-guide.md)
- [Az Azure Virtual Machines adatbázis-kezelő üzembe helyezésének szempontjai az SAP-munkaterhelésekhez](./dbms_guide_general.md)