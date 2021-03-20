---
title: Útmutatás és ajánlott eljárások
description: Ismerje meg a Felhőbeli és a helyszíni számítási feladatok felhőbe történő biztonsági mentésének ajánlott eljárásait és útmutatását.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 0b3f70061be4d158ae717a97779d6ab0445f5858
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100593336"
---
# <a name="backup-cloud-and-on-premises-workloads-to-cloud"></a>Felhőbeli és helyszíni számítási feladatok biztonsági mentése a felhőbe

## <a name="introduction"></a>Bevezetés

A Azure Backup az Azure-beli adategységeket egy egyszerű, biztonságos és költséghatékony megoldással biztosítja, amely nulla infrastruktúrát igényel. Ez a Azure's beépített adatvédelmi megoldás a számítási feladatok széles köréhez. Segít a felhőben futó, kritikus fontosságú számítási feladatok elleni védelemben, és gondoskodik arról, hogy a biztonsági mentések mindig elérhetők legyenek, és a teljes biztonsági mentési hagyatékon is kezelhetők legyenek.

### <a name="intended-audience"></a>Célközönség

Ennek a cikknek az elsődleges célközönsége az informatikai és az alkalmazás-rendszergazdák, valamint a nagyméretű és közepes méretű szervezetek alkalmazásai, akik megismerhetik az Azure beépített adatvédelmi technológiájának képességeit, Azure Backup, valamint azt, hogy miként lehet hatékonyan megvalósítani az üzemelő példányok jobb védelmét biztosító megoldásokat. A cikk feltételezi, hogy már ismeri az alapvető Azure-technológiákat, az adatvédelmi fogalmakat és a biztonsági mentési megoldással való munkát. Az ebben a cikkben ismertetett útmutató megkönnyíti a biztonsági mentési megoldás megtervezését az Azure-ban a meghatározott minták használatával, és az ismert buktatók elkerülését.

### <a name="how-this-article-is-organized"></a>A cikk rendszerezése

Habár könnyen megkezdheti az infrastruktúra és az alkalmazások védelmét az Azure-ban, amikor gondoskodik arról, hogy az alapul szolgáló Azure-erőforrások megfelelően legyenek beállítva, és optimálisan használatba kerüljenek az idő értékét. Ez a cikk rövid áttekintést nyújt a Azure Backup központi telepítésének optimális konfigurálásához szükséges tervezési szempontokról és útmutatásokról. Megvizsgálja az alapvető összetevőket (például a Recovery Services tárolót, a biztonsági mentési szabályzatot) és a fogalmakat (például irányítás), valamint azt, hogy miként képzelheti el őket és képességeiket a részletes termékdokumentációra mutató hivatkozásokkal.

## <a name="architecture"></a>Architektúra

![Az Azure Backup architektúrája](./media/guidance-best-practices/azure-backup-architecture.png)

### <a name="workloads"></a>Számítási feladatok

Azure Backup lehetővé teszi az adatvédelem különböző számítási feladatokhoz (a helyszíni és a felhőben). Biztonságos és megbízható, beépített adatvédelmi mechanizmus az Azure-ban. Zökkenőmentesen méretezheti a védelmet több számítási feladattal, anélkül, hogy felügyeleti terheléssel kellene foglalkoznia. A PowerShell, a CLI, a Azure Resource Manager sablonok és a REST API-k segítségével több Automation-csatornát is engedélyezhet.

* **Méretezhető, tartós és biztonságos tárolás –** A Azure Backup megbízható blob Storage-t használ beépített biztonsági és magas rendelkezésre állási funkciókkal. A biztonsági mentési adataihoz LRS, GRS vagy RA-GRS tárolókat is választhat.  

* **Natív munkaterhelés-integráció –** A Azure Backup natív integrációt biztosít az Azure-beli számítási feladatokkal (virtuális gépekkel, SAP HANAekkel, az SQL Azure-beli virtuális gépekkel és akár Azure Filesekkel), anélkül, hogy az automatizálást vagy az infrastruktúrát az ügynökök üzembe helyezéséhez kellene kezelnie,

### <a name="data-plane"></a>Adatsík

* **Automatizált tárolók kezelése** – Azure Backup automatizálja a Storage-fiókok kihelyezését és kezelését a biztonsági mentési adatmennyiség érdekében, így biztosítva, hogy a biztonsági mentési adatmennyiség növekszik.

* **Rosszindulatú Törlés elleni védelem –** A biztonsági mentések törlésével megakadályozható a véletlen és rosszindulatú kísérletek elleni védelem. A törölt biztonsági mentési adatok tárolása 14 napig díjmentes, és lehetővé teszi, hogy ezt az állapotból lehessen helyreállítani.

* **Biztonságos titkosított biztonsági másolatok –** Azure Backup biztosítja, hogy a biztonsági mentési adatait biztonságos módon tárolják, az Azure platform, például az Azure RBAC és a titkosítás beépített biztonsági képességeinek kihasználásával.

* **Biztonsági másolatok életciklusának kezelése –** Azure Backup automatikusan törli a régebbi biztonsági mentési adatokból az adatmegőrzési szabályzatoknak való megfelelést. Az adatok az operatív tárolóból a tár tárterületére is felhasználhatók.

### <a name="management-plane"></a>Felügyeleti sík

* **Hozzáférés-vezérlés** – a tárolók (Recovery Services és Backup-tárolók) biztosítják a felügyeleti képességeket, és elérhetők a Azure Portal, a Backup Center, a tároló irányítópultok, az SDK, a CLI és a REST API-k segítségével. Ez egy Azure RBAC-határ is, amely lehetővé teszi a biztonsági másolatok elérésének korlátozását csak az engedélyezett biztonsági mentési rendszergazdák számára.

* **Házirend-kezelés** – az egyes tárakon belül Azure Backup szabályzatok határozzák meg, hogy a biztonsági mentések Mikor legyenek aktiválva, és mennyi ideig kell megőrizni őket. Ezeket a szabályzatokat kezelheti, és több elem között is alkalmazhatja őket.

* **Monitorozás és jelentéskészítés** – a Azure Backup együttműködik a log Analyticsekkel, és lehetővé teszi a jelentések munkafüzeteken keresztüli megtekintését is.

* **Pillanatkép-kezelés** – Azure Backup pillanatképeket készít bizonyos Azure-beli natív munkaterhelésekhez (virtuális gépekhez és Azure Files), kezeli ezeket a pillanatképeket, és lehetővé teszi a gyors visszaállítást. Ez a beállítás drasztikusan csökkenti az adatok eredeti tárolóba való helyreállításának idejét.

## <a name="vault-considerations"></a>Tár szempontjai

A Azure Backup a tárolókat (Recovery Services és Backup-tárolókat) használja a biztonsági másolatok előkészítéséhez és kezeléséhez. A tárolókat is használ a biztonsági másolatok tárolásához. A hatékony tár kialakításával a szervezetek létrehozhatnak egy struktúrát az Azure-beli biztonsági mentési eszközök rendszerezéséhez és kezeléséhez az üzleti prioritások támogatásához. Tár létrehozásakor vegye figyelembe a következő irányelveket:  

### <a name="align-to-subscription-design-strategy"></a>Igazítás az előfizetési terv stratégiájához

Mivel a tár hatóköre egy előfizetésre van korlátozva, a tár kialakításával teljesítheti az előfizetési tervezési stratégiát, például az alkalmazások *kategóriájának stratégiáját* , ahol az előfizetéseket meghatározott alkalmazások vagy szolgáltatások alapján, vagy az alkalmazási archetípusok soraiban választják el. További információkért tekintse meg ezt a [cikket](/azure/cloud-adoption-framework/decision-guides/subscriptions/).

### <a name="single-or-multiple-vault"></a>Egy vagy több tároló

A biztonsági másolatok rendszerezéséhez és kezeléséhez egyetlen tárat vagy több tárolót használhat. Tekintse meg a következő útmutatót:

* Ha a számítási feladatokat egyetlen előfizetéssel és egyetlen erőforrással felügyeli, akkor egyetlen tároló használatával figyelheti és kezelheti a biztonsági mentési hagyatékot.

* Ha a számítási feladatok az előfizetések között oszlanak el, több tárolót is létrehozhat, egy vagy több előfizetéssel.
  * A Backup Center lehetővé teszi, hogy egyetlen üvegtábla legyen a biztonsági mentéssel kapcsolatos összes feladat kezeléséhez. [További információt itt talál]().
  * A nézeteket a munkafüzet sablonjaival is testreszabhatja. A Backup Explorer egy ilyen sablon az Azure-beli virtuális gépekhez. [További információt itt talál](monitor-azure-backup-with-backup-explorer.md).
  * Ha konzisztens szabályzatra van szüksége a tárakban, akkor az Azure Policy segítségével több tárolón is propagálhatja a biztonsági mentési szabályzatot. Írhat olyan egyéni Azure Policy- [definíciót](../governance/policy/concepts/definition-structure.md) , amely a ["deployifnotexists"](../governance/policy/concepts/effects.md#deployifnotexists) effektus használatával propagálja a biztonsági mentési szabályzatot több tárolón keresztül. Ezt a Azure Policy definíciót [hozzárendelheti](../governance/policy/assign-policy-portal.md) egy adott hatókörhöz (előfizetés vagy RG) is, így egy "biztonsági mentési szabályzat" erőforrást helyez üzembe az Azure Policy hozzárendelés hatókörében lévő összes Recovery Services-tárolóra. A biztonsági mentési szabályzat beállításait (például a biztonsági mentés gyakoriságát, a megőrzést stb.) a felhasználónak kell megadnia paraméterként a Azure Policy-hozzárendelésben.

* Ahogy a szervezeti lábnyoma nő, érdemes lehet áthelyezni a munkaterheléseket az előfizetések között a következő okok miatt: a biztonsági mentési szabályzattal való összehangolás, a tárolók összevonása, az alacsonyabb redundancia elleni kereskedelmi forgalom csökkentése a GRS és a LRS között.  Azure Backup támogatja egy Recovery Services-tároló áthelyezését az Azure-előfizetések között, vagy egy másik erőforráscsoporthoz ugyanazon az előfizetésen belül. [További információt itt talál](backup-azure-move-recovery-services-vault.md).

### <a name="review-default-settings"></a>Alapértelmezett beállítások áttekintése

A biztonsági mentések konfigurálása előtt tekintse át a tárolási replikálási típus és a biztonsági beállítások alapértelmezett beállításait az igényeinek megfelelően.

* Alapértelmezés szerint a *tárolási replikálás típusa* geo-REDUNDÁNS (GRS) értékre van állítva. A biztonsági mentés konfigurálása után a módosítás lehetőség le lesz tiltva. A beállítások áttekintéséhez és módosításához kövesse az [alábbi](backup-create-rs-vault.md#set-storage-redundancy) lépéseket.

* Az újonnan létrehozott tárolók alapértelmezés szerint a *Soft delete* lehetőséggel védik a biztonsági mentési adatok véletlen vagy kártékony törlési funkcióit. A beállítások áttekintéséhez és módosításához kövesse az [alábbi](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) lépéseket.

* A *régiók közötti visszaállítás* lehetővé teszi az Azure-beli virtuális gépek visszaállítását egy másodlagos régióban, amely egy Azure-beli párosított régió. Ez a beállítás lehetővé teszi a naplózási és megfelelőségi követelmények teljesítését, valamint a virtuális gép vagy annak lemezének visszaállítását, ha az elsődleges régióban katasztrófa következik be. A CRR bármely GRS-tár egyik funkciója. [További információt itt talál](backup-create-rs-vault.md#set-cross-region-restore).

* A tár kialakításának véglegesítése előtt tekintse át a tároló [támogatási mátrixait](backup-support-matrix.md#vault-support) , hogy megismerje a tervezési lehetőségeket befolyásoló vagy korlátozó tényezőket.

## <a name="backup-policy-considerations"></a>Biztonsági mentési szabályzattal kapcsolatos megfontolások

Azure Backup házirendnek két összetevője van: az *ütemterv* (a biztonsági mentés ideje) és a *megőrzés* (a biztonsági mentés időtartamának megőrzése). A szabályzatot a biztonsági mentés alatt álló adatok típusa alapján határozhatja meg, RTO/RPO követelmények, működési vagy szabályozási megfelelőségi igények és számítási feladatok típusa (például virtuális gép, adatbázis, fájlok). [További információt itt talál](backup-architecture.md#backup-policy-essentials).

Biztonsági mentési szabályzat létrehozásakor vegye figyelembe a következő irányelveket:

### <a name="schedule-considerations"></a>Az ütemterv szempontjai

* Érdemes lehet olyan virtuális gépeket csoportosítani, amelyekben ugyanaz az ütemterv kezdési időpontja, gyakorisága és adatmegőrzési beállításai szükségesek egyetlen házirendben.

* Győződjön meg arról, hogy a biztonsági mentés ütemezett kezdési időpontja az éles üzemen kívüli alkalmazás időtartama alatt van.

* A biztonsági mentési forgalom elosztása érdekében érdemes a különböző virtuális gépek biztonsági mentését a nap különböző pontjain, és ügyeljen arra, hogy az időpontok ne legyenek átfedésben.

### <a name="retention-considerations"></a>Megőrzési megfontolások

* A rövid távú adatmegőrzés a következő lehet: "Minutes" vagy "Daily". A "hetente", "havonta" vagy "éves" biztonsági mentési pontok megőrzését hosszú távú megőrzésnek nevezzük.

* Hosszú távú megőrzés:
  * Tervezett (megfelelőségi követelmények) – Ha előre tudja, hogy az adatok a jelenlegi időponttól kezdve szükségesek, akkor használja a hosszú távú adatmegőrzést.
  * Nem tervezett (igény szerinti követelmény) – Ha nem tudja előre, használja az igény szerinti egyéni adatmegőrzési beállításokat (ezeket az egyéni adatmegőrzési beállításokat nem érinti a házirend-beállítások).

* Igény szerinti biztonsági mentés egyéni megőrzéssel – ha a biztonsági mentési szabályzattal nem ütemezett biztonsági mentésre van szüksége, igény szerinti biztonsági mentést is használhat. Ez olyan biztonsági másolatok készítéséhez lehet hasznos, amelyek nem felelnek meg az ütemezett biztonsági mentésnek vagy a részletes biztonsági mentésnek (például naponta több IaaS virtuális gép biztonsági mentése, mivel az ütemezett biztonsági mentés naponta csak egy biztonsági mentést engedélyez). Fontos megjegyezni, hogy az ütemezett házirendben definiált adatmegőrzési szabályzat nem vonatkozik az igény szerinti biztonsági másolatokra.

### <a name="optimize-backup-policy"></a>Biztonsági mentési szabályzat optimalizálása

* Az üzleti követelmények változása esetén előfordulhat, hogy ki kell terjesztenie vagy csökkentenie kell a megőrzés időtartamát. Ha így tesz, a következőket várhatja el:  
  * Ha a megőrzés kibővül, a meglévő helyreállítási pontok az új szabályzatnak megfelelően vannak megjelölve és tárolva.
  * Ha a megőrzés csökken, a helyreállítási pontok a következő tisztítási feladatokban törlésre vannak megjelölve, és ezt követően törlődnek.
  * A legújabb adatmegőrzési szabályok az összes megőrzési pontra érvényesek (kivéve az igény szerinti megőrzési pontokat). Tehát ha a megőrzési időtartam kibővül (például 100 napig), akkor a biztonsági mentést követően (például 100 nap és hét nap között), az összes biztonsági mentési adat megmarad az utolsó megadott megőrzési időtartam (azaz 7 nap) alapján.

* Azure Backup rugalmasságot biztosít a *biztonsági másolatok védelmének és kezelésének leállításához*:
  * *A védelem leállítása és a biztonsági mentési adat megőrzése*. Ha kihasználja vagy leszereli az adatforrást (VM, Application), de az adatok megőrzését vagy megfelelőségét kell megtartania, akkor ezzel a lehetőséggel leállíthatja az összes jövőbeli biztonsági mentési feladatot az adatforrás védelméről, és megtarthatja a biztonsági mentés alatt álló helyreállítási pontokat. Ezután visszaállíthatja vagy folytathatja a virtuális gépek védelmét.
  * *Állítsa le a védelmet, és törölje a biztonsági másolati fájlokat*. Ez a beállítás leállítja az összes jövőbeli biztonsági mentési feladatot a virtuális gép védelmére, és törli az összes helyreállítási pontot. Nem fogja tudni visszaállítani a virtuális gépet, és nem használhatja a biztonsági mentés folytatása lehetőséget.

  * Ha folytatja a védelmet (egy adatforrást, amely az adatok megőrzése miatt leállt), akkor a megőrzési szabályok érvényesek lesznek. Minden lejárt helyreállítási pont el lesz távolítva (az ütemezett időpontban).

* A szabályzat kialakításának elvégzése előtt fontos tisztában lennie azzal, hogy milyen tényezők befolyásolhatják a kialakítási döntéseket.
  * A biztonsági mentési szabályzat egy tárolóra terjed ki.
  * Korlátozva van az elemek száma (például 100 virtuális gép). A skálázáshoz ismétlődő szabályzatokat hozhat létre, amelyek azonos vagy eltérő ütemtervekkel rendelkeznek.
  * Meghatározott helyreállítási pontokat nem lehet szelektíven törölni.
  * Az ütemezett biztonsági mentést nem lehet teljesen letiltani, és az adatforrás védett állapotban marad. A szabályzattal konfigurálható legritkább biztonsági mentés egy hetente ütemezett biztonsági mentést tartalmaz. Egy másik lehetőség, hogy leállítja a védelmet az adatmegőrzés és a védelem engedélyezése minden egyes alkalommal, amikor biztonsági mentést szeretne készíteni, igény szerinti biztonsági mentést készít, majd kikapcsolja a védelmet, de megőrzi a biztonsági mentési adatait. [További információt itt talál](backup-azure-manage-vms.md#stop-protecting-a-vm).

## <a name="security-considerations"></a>Biztonsági szempontok

A biztonsági mentési adatok védelme és a vállalata biztonsági igényeinek kielégítése érdekében Azure Backup titkosságot, integritást és rendelkezésre állási garanciákat biztosít a szándékos támadásokkal szemben, valamint az értékes adatokkal és rendszerekkel való visszaéléssel. Vegye figyelembe a Azure Backup megoldásának következő biztonsági irányelveit:

### <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés

* Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) lehetővé teszi a részletes hozzáférés-kezelést, a feladatok elkülönítését a csapaton belül, és csak a feladataik elvégzéséhez szükséges felhasználók számára biztosít hozzáférést. [További információt itt talál](backup-rbac-rs-vault.md).

* A Azure Backup három beépített szerepkört biztosít a biztonságimásolat-kezelési műveletek vezérléséhez: a biztonsági mentési közreműködők, a kezelők és az olvasók. [További információt itt talál](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions).

* Azure Backup számos, a szolgáltatásba beépített biztonsági vezérlőt tartalmaz a biztonsági rések megelőzésére, észlelésére és reagálására (További információ)

* A Recovery Services-tárolók által használt Storage-fiókok el vannak különítve, és a felhasználók nem férhetnek hozzá semmilyen kártékony célra. A hozzáférés csak Azure Backup felügyeleti műveleteken, például a visszaállításon keresztül engedélyezett.

### <a name="encryption-of-data-in-transit-and-at-rest"></a>Az átvitel és a nyugalmi állapotban lévő adatok titkosítása

A titkosítás védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében.

* Az Azure-on belül az Azure Storage és a tároló közötti adatátvitel HTTPS-védelemmel történik. Ezek az adatközpontok az Azure-hálózaton belül maradnak.

* A biztonsági mentési adatai automatikusan titkosítva vannak a Microsoft által felügyelt kulcsokkal. Másik lehetőségként használhatja a saját kulcsait, más néven az [ügyfél által felügyelt kulcsokat](encryption-at-rest-with-cmk.md).

* Azure Backup támogatja az olyan Azure-beli virtuális gépek biztonsági mentését és visszaállítását, amelyek az operációs rendszer/adatlemezei Azure Disk Encryption (ADE) titkosítással rendelkeznek. [További információt itt talál](backup-azure-vms-encryption.md).

### <a name="protection-of-backup-data-from-unintentional-deletes"></a>A véletlen törlésből származó biztonsági mentési adatok védelme

A Azure Backup biztonsági funkciókat biztosít a biztonsági mentési adatainak a törlés után még a védelme érdekében. Ha a felhasználó törli a biztonsági mentést (virtuális gép, SQL Server-adatbázis, Azure-fájlmegosztás, SAP HANA-adatbázis), a törlést követően a biztonsági mentési adat 14 további napig megmarad, így a biztonsági mentési elem helyreállítása adatvesztés nélkül történik. A "Soft Delete" állapotban lévő biztonsági mentési adatok további 14 napos megőrzése nem jár semmilyen költséggel. [További információt itt talál](backup-azure-security-feature-cloud.md).

### <a name="monitoring-and-alerts-of-suspicious-activity"></a>Gyanús tevékenység figyelése és riasztásai

A Azure Backup beépített figyelési és riasztási képességeket biztosít a Azure Backuphoz kapcsolódó események műveleteinek megtekintésére és konfigurálására. [További információt itt talál](security-overview.md#monitoring-and-alerts-of-suspicious-activity).

### <a name="security-features-to-help-protect-hybrid-backups"></a>Biztonsági funkciók a hibrid biztonsági másolatok védelme érdekében

A Azure Backup szolgáltatás a Microsoft Azure Recovery Services (MARS) ügynököt használja a fájlok, mappák, valamint a kötet vagy a rendszerállapot biztonsági mentésére és visszaállítására egy helyszíni számítógépről az Azure-ba. A MARS mostantól biztonsági funkciókat biztosít: egy jelszó, amelyet a feltöltés és a visszafejtés előtt titkosítani kell a Azure Backupből való letöltés után, a törölt biztonsági mentési adatok a törlés időpontjától számítva további 14 napig, a kritikus művelet (például a jelszó módosítása csak olyan felhasználók által végezhető el, akik rendelkeznek érvényes Azure-beli hitelesítő adatokkal. [További információt itt talál](backup-azure-security-feature.md).

## <a name="network-considerations"></a>Hálózati szempontok

Azure Backup a számítási feladatból származó adatok áthelyezését igényli a Recovery Services-tárolóba. A Azure Backup számos képességet biztosít a biztonsági mentési adatok véletlen megjelenítésének biztosításához (például a hálózatban található, ember általi támadás esetén). Tekintse meg a következő útmutatót:

### <a name="internet-connectivity"></a>Internetkapcsolat

* *Azure-beli virtuális gép biztonsági mentése* – a Storage és a Azure Backup szolgáltatás közötti összes szükséges kommunikáció és adatforgalom az Azure-hálózaton belül történik anélkül, hogy a virtuális hálózathoz kellene férnie. A biztonságos hálózatokon belül elhelyezett Azure-beli virtuális gépek biztonsági mentése nem igényli az IP-címek és a teljes tartománynevek elérésének engedélyezését.

* *SAP HANA-adatbázisok Azure-beli virtuális gépen, SQL Server Azure-beli virtuális gépen található adatbázisok* – a Azure Backup szolgáltatáshoz, az Azure Storage-hoz és a Azure Active Directoryhoz kell kapcsolódniuk. Ezt privát végpontok használatával vagy a szükséges nyilvános IP-címekhez vagy teljes tartománynevek elérésének engedélyezésével lehet elérni. A szükséges Azure-szolgáltatásokhoz való megfelelő kapcsolódás nem teszi lehetővé az adatbázis-felderítést, a biztonsági mentés konfigurálását, a biztonsági másolatok készítését és az adatok visszaállítását. A NSG-címkék, az Azure tűzfal és a HTTP-proxy használatakor a teljes hálózati útmutatásért tekintse meg ezeket az [SQL](backup-sql-server-database-azure-vms.md#establish-network-connectivity) -és [SAP HANA](./backup-azure-sap-hana-database.md#establish-network-connectivity) -cikkeket.

* *Hibrid* – a MARS (Microsoft Azure Recovery Services) ügynök minden kritikus művelethez hálózati hozzáférést igényel – telepítés, konfigurálás, biztonsági mentés és visszaállítás. A MARS-ügynök az [Azure ExpressRoute](install-mars-agent.md#use-azure-expressroute) keresztül tud csatlakozni a Azure Backup szolgáltatáshoz a nyilvános közvetítés (a régi áramkörök számára elérhető) és a Microsoft-társ, [privát végpontok](install-mars-agent.md#private-endpoints) vagy [a megfelelő hozzáférés-vezérléssel rendelkező proxy/tűzfal](install-mars-agent.md#verify-internet-access)használatával.

### <a name="private-endpoints-for-azure-backup"></a>Azure Backup magánhálózati végpontok

Az Azure [Private Endpoint](../private-link/private-endpoint-overview.md) egy olyan hálózati adapter, amely az Azure Private-kapcsolaton keresztül az Ön számára biztosít privát és biztonságos szolgáltatásokat. Azure Backup lehetővé teszi az adatok biztonságos biztonsági mentését és visszaállítását a Recovery Services-tárolóból privát végpontok használatával.

* Ha engedélyezi a privát végpontokat a tárolóhoz, azok csak az SQL-és SAP HANA-munkaterhelések biztonsági mentéséhez és visszaállításához használatosak az Azure-beli virtuális gépen és a MARS-ügynök biztonsági mentésekor.  A tárolót használhatja más számítási feladatok biztonsági mentésére is (a saját végpontokat azonban nem szükséges). Az SQL és a SAP HANA számítási feladatainak és a MARS-ügynök használatával történő biztonsági mentésének kiegészítéseként az Azure-beli virtuális gépek biztonsági mentése esetén is a rendszer privát végpontokat használ a fájlok helyreállításához. [További információt itt talál](private-endpoints.md#recommended-and-supported-scenarios).

* Azure Active Directory jelenleg nem támogatja a privát végpontokat. Így a Azure Active Directory számára szükséges IP-címeket és teljes tartományneveket engedélyezni kell a biztonságos hálózatról, amikor az Azure-beli virtuális gépeken található adatbázisok biztonsági mentését hajtja végre, és a MARS-ügynök használatával készít biztonsági mentést. NSG-címkéket és Azure Firewall címkéket is használhat az Azure AD-hez való hozzáférés engedélyezéséhez. További információ az [előfeltételekről](./private-endpoints.md#before-you-start).

## <a name="governance-considerations"></a>Szabályozási szempontok

Az Azure-ban az irányítás elsődlegesen [Azure Policy](../governance/policy/overview.md) és [Azure Cost Managementekkel](../cost-management-billing/cost-management-billing-overview.md)valósul meg. [Azure Policy](../governance/policy/overview.md) lehetővé teszi a szabályzat-definíciók létrehozását, hozzárendelését és kezelését az erőforrások szabályainak érvényesítéséhez. Ez a szolgáltatás a vállalati szabványoknak megfelelően tartja meg ezeket az erőforrásokat. A [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) segítségével nyomon követheti az Azure-erőforrások és más felhőalapú szolgáltatók Felhőbeli használatát és kiadásait. Emellett a következő eszközök, mint például az [Azure Price Calculator](https://azure.microsoft.com/pricing/calculator/) és a [Azure Advisor](../advisor/advisor-overview.md)  fontos szerepet játszanak a Cost Management folyamatában.

### <a name="azure-backup-support-two-key-scenarios-via-built-in-azure-policy"></a>Azure Backup két fő forgatókönyvet támogat a beépített Azure Policy használatával

* Győződjön meg arról, hogy az újonnan létrehozott üzleti szempontból kritikus gépek automatikusan biztonsági mentést végeznek a megfelelő adatmegőrzési beállításokkal. A Azure Backup beépített szabályzatot biztosít (Azure Policy használatával), amely az előfizetésben vagy az erőforráscsoport egy adott helyén lévő összes Azure-beli virtuális géphez hozzárendelhető. Ha a szabályzatot egy adott hatókörhöz rendeli hozzá, az abban a hatókörben létrehozott összes új virtuális gépet a rendszer automatikusan konfigurálja egy meglévő tárolóba ugyanazon a helyen és előfizetésben. A felhasználó megadhatja azt a tárolót és adatmegőrzési házirendet, amelyhez a biztonsági másolattal rendelkező virtuális gépeket társítani kell. [További információt itt talál](backup-azure-auto-enable-backup.md).

* Győződjön meg arról, hogy az újonnan létrehozott tárolók rendelkeznek a jelentések támogatásához szükséges diagnosztikai lehetőségekkel. Gyakran előfordulhat, hogy egy tárolóban manuálisan ad hozzá egy diagnosztikai beállítást, amely nehézkes feladat lehet. Emellett minden új tárolóhoz engedélyezve kell lennie a diagnosztikai beállításoknak, így megtekintheti a tár jelentéseit. A diagnosztikai beállítások méretezésének egyszerűsítése érdekében (Log Analytics a célként megadott módon) a Azure Backup beépített Azure Policy biztosít. Ez a házirend egy LA diagnosztikai beállítást hoz létre az egyes előfizetésekben vagy erőforráscsoportok összes tárolójában. A következő szakasz útmutatást nyújt ennek a szabályzatnak a használatáról. [További információt itt talál](azure-policy-configure-diagnostics.md).

### <a name="azure-backup-cost-considerations"></a>Azure Backup a költséghatékonysággal kapcsolatos megfontolások

A Azure Backup szolgáltatás képességei rugalmasan kezelhetik a költségeket, és továbbra is megfelelnek a BCDR (üzletmenet-folytonossági és vész-helyreállítási) üzleti követelményeknek. Tekintse meg a következő útmutatót:

* A díjszabási számológép használatával kiértékelheti és optimalizálhatja a költségeket a különböző karok módosításával. [További információ](azure-backup-pricing.md)

* Backup Explorer: a Backup Explorer vagy a biztonsági mentési jelentések segítségével megismerheti és optimalizálhatja a biztonsági mentési tár növekedését, és nem kritikus fontosságú számítási feladatokhoz vagy törölt virtuális gépekhez is leállíthatja a biztonsági mentés A teljes hagyaték összesített nézetét egy biztonsági mentési perspektívából érheti el. Ez nem csak a biztonsági másolati elemekre vonatkozó információkat tartalmazza, hanem olyan erőforrásokat is, amelyek nincsenek biztonsági mentésre konfigurálva. Ez biztosítja, hogy soha ne maradjon le a növekvő adatközpontban található kritikus adatok védelme, és a biztonsági mentések a nem kritikus fontosságú számítási feladatokhoz vagy a törölt munkaterhelésekhez vannak optimalizálva.

* Biztonsági mentési szabályzat optimalizálása
  * Az ütemterv és a megőrzési beállítások optimalizálása a számítási feladatok archetípusa alapján (például kritikus fontosságú, nem kritikus fontosságú)
  * Az azonnali visszaállítás megőrzési beállításainak optimalizálása
  * Válassza ki a megfelelő biztonsági mentési típust a követelmények teljesítéséhez, miközben a biztonsági mentési típusok (teljes, növekményes, napló, differenciális) a számítási feladatok által Azure Backup figyelembe vételével.

* Szelektív biztonsági mentési lemezek: a lemez kihagyása (előzetes verzió) funkció hatékony és költséghatékony megoldást kínál a kritikus fontosságú adat szelektív biztonsági mentéséhez. Például csak egy lemezről készíthet biztonsági mentést, ha nem kíván biztonsági másolatot készíteni a virtuális géphez csatolt többi lemezről. Ez akkor is hasznos, ha több biztonsági mentési megoldással rendelkezik. Ha például biztonsági mentést készít az adatbázisokról vagy az adatokról egy munkaterhelés biztonsági mentési megoldással (SQL Server adatbázis az Azure VM Backup szolgáltatásban), és az Azure-beli virtuális gép biztonsági mentését szeretné használni a kiválasztott lemezekhez.

* Azure Backup pillanatképeket küld az Azure-beli virtuális gépekről, és azokat a lemezekkel együtt tárolja a helyreállítási pont létrehozása és a visszaállítási műveletek felgyorsításához. Ezt az azonnali visszaállításnak nevezzük. Alapértelmezés szerint az azonnali visszaállítási Pillanatképek két napig tartanak. Ez a funkció lehetővé teszi a visszaállítási műveletek elvégzését ezekről a pillanatképekről a visszaállítási idő csökkentésével. Ez csökkenti az adatok tárolóból való visszaalakításához és másolásához szükséges időt. Ennek eredményeképpen megjelennek a tárolási költségek, amelyek megfelelnek az adott időszakban készített pillanatképeknek. [További információt itt talál](backup-instant-restore-capability.md#configure-snapshot-retention).

* Azure Backup tár tárolási replikálási típusa alapértelmezés szerint a Geo-redundáns (GRS) értékre van állítva. Ez a beállítás az elemek védelme után nem módosítható. A Geo-redundáns tárolás (GRS) magasabb szintű adattartósságot biztosít, mint a helyileg redundáns tárolás (LRS), lehetővé teszi a több régióra kiterjedő visszaállítás használatát és a költségek csökkentését. Tekintse át az alacsonyabb költségek és a magasabb adattartósság közötti kompromisszumokat, és döntse el, hogy mi a legmegfelelőbb a forgatókönyvhöz. [További információ](backup-create-rs-vault.md#set-storage-redundancy)

* Ha a virtuális gépen belül futó munkaterhelést is védi, és maga a virtuális gép is van, ellenőrizze, hogy van-e szükség a kettős védelemre.

## <a name="monitoring-and-alerting-considerations"></a>Figyelési és riasztási megfontolások

Biztonsági mentési felhasználóként vagy rendszergazdaként képesnek kell lennie az összes biztonsági mentési megoldás figyelésére, és értesítést kapni a fontos forgatókönyvekről. Ez a szakasz a Azure Backup szolgáltatás által biztosított figyelési és értesítési képességeket részletezi.

### <a name="monitoring"></a>Figyelés

* A Azure Backup **beépített feladatokat** biztosít a műveletekhez, például a biztonsági mentés konfigurálásához, a biztonsági mentéshez, a visszaállításhoz, a biztonsági mentés törléséhez stb. Ez a tárolóra vonatkozik, és ideális megoldás egyetlen tároló figyelésére. [További információt itt talál](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault).

* Ha skálán kell figyelnie az operatív tevékenységeket, a **Backup Explorer** a teljes biztonsági mentési hagyaték összesített nézetét jeleníti meg, amely részletes elemzést és hibaelhárítást tesz lehetővé. Ez egy beépített Azure Monitor-munkafüzet, amely egyetlen, központi helyet biztosít az Azure-beli teljes biztonsági mentési környezet működési tevékenységeinek figyelésére, a bérlők, a helyek, az előfizetések, az erőforráscsoportok és a tárolók átfedésére. [További információt itt talál](monitor-azure-backup-with-backup-explorer.md).
  * Ezzel azonosíthatja a biztonsági mentéshez nem konfigurált erőforrásokat, és gondoskodhat arról, hogy soha ne maradjon le a növekvő területen a kritikus adatok védelme.
  * Az irányítópult operatív tevékenységeket biztosít az elmúlt hét napban (maximum). Ha meg kell őriznie ezeket az adatfájlokat, exportálhatja Excel-fájlként, és megtarthatja őket.
  * Ha Ön Azure Lighthouse-felhasználó, több bérlőn is megtekintheti az információkat, ami lehetővé teszi a határ nélküli figyelést.

* Ha hosszú távon meg kell őriznie és meg kell tekintenie az operatív tevékenységeket, használja a **jelentéseket**. A biztonsági mentési rendszergazdákra vonatkozó gyakori követelmény, hogy a biztonsági másolatok alapján bepillantást nyerjen az olyan adatokat, amelyek hosszú időn keresztül terjednek ki. Ilyen megoldás esetén a következő esetekben használhatók:
  * A Felhőbeli tárhely kiosztása és előrejelzése.
  * Biztonsági mentések és visszaállítások naplózása.
  * A legfontosabb trendek azonosítása különböző részletességi szinteken.

* ráadásul
  * Az adatok (például feladatok, házirendek stb.) a **log Analytics** munkaterületre is elküldhetők. Ez lehetővé teszi Azure Monitor naplók funkcióit, hogy lehetővé tegyék az adatok korrelációját a Azure Monitor által gyűjtött más monitorozási adatokkal, összevonja a naplóbejegyzéseket több Azure-előfizetésből és-bérlőből egyetlen helyre az elemzéshez [További információt itt talál](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace).
  * Adatokat küldhet az Event hub-nak az Azure-on kívüli bejegyzések küldéséhez, például egy harmadik féltől származó SIEM (biztonsági információk és rendezvényszervezés) vagy más log Analytics megoldáshoz. [További információt itt talál](../azure-monitor/essentials/activity-log.md#send-to-azure-event-hubs).
  * Az Azure Storage-fiókba küldheti az adatait, ha a naplózást, a statikus elemzést vagy a biztonsági mentést 90 napnál hosszabb ideig kívánja megőrizni. Ha csak 90 vagy kevesebb napig kell megtartania az eseményeket, nem kell létrehoznia az archívumokat egy Storage-fiókhoz, mivel a tevékenység-naplózási események az Azure-platformon maradnak a 90 napig. [További információ](../azure-monitor/essentials/activity-log.md#send-to--azure-storage).

### <a name="alerting"></a>Riasztások kezelése

* A riasztások elsősorban arra szolgálnak, hogy értesítést kapjanak a vonatkozó teendők meghozataláról. A biztonsági mentési riasztások szakasz a Azure Backup szolgáltatás által generált riasztásokat jeleníti meg.

* Azure Backup egy **beépített riasztási** értesítési mechanizmust biztosít a hibák, figyelmeztetések és kritikus műveletek e-mailben történő elküldéséhez. Megadhat egyedi e-mail-címeket vagy terjesztési listát, amelyekről értesítést kaphat, ha riasztás jön létre. Azt is megadhatja, hogy az egyes riasztások értesítést kapjanak-e, vagy egy óránkénti kivonatban csoportosítsa őket, majd értesítést kap.
  * Ezeket a riasztásokat a szolgáltatás definiálja, és támogatást nyújt a korlátozott forgatókönyvek számára – biztonsági mentési/visszaállítási hibák, az adatok megőrzésének megakadályozása/a védelem leállítása az adatok törlésével és így tovább. [További információt itt talál](backup-azure-monitoring-built-in-monitor.md#alert-scenarios).
  * Ha olyan roncsolásos műveletet végez, mint például a védelem leállítása a törlési művelettel, a rendszer riasztást küld, és e-mailt küld az előfizetés-tulajdonosoknak, a rendszergazdáknak és a társ-rendszergazdáknak akkor is, ha az értesítések **nincsenek konfigurálva az** Recovery Services-tárolóhoz.
  * Bizonyos munkaterhelések nagy gyakoriságú hibákat okozhatnak (például SQL Server 15 percenként). Ha meg szeretné akadályozni, hogy az egyes hibák előfordulásakor kiváltott riasztások eljusson, a riasztások konszolidálva lesznek. [További információt itt talál](backup-azure-monitoring-built-in-monitor.md#consolidated-alerts).
  * A beépített riasztások nem szabhatók testre, és a Azure Portal meghatározott e-mailekre vannak korlátozva.

* Ha **Egyéni riasztásokat kell létrehoznia** (például a sikeres feladatok riasztásait), használja a log Analytics. Azure Monitor a saját riasztásokat a Log Analytics munkaterületen hozhatja létre. A hibrid munkaterhelések (DPM/MABS) is küldhetnek az adatoknak az LA-be, és a LA használatával a Azure Backup által támogatott munkaterhelések közötti gyakori riasztásokat.

* Értesítéseket a beépített Recovery Services tár **tevékenység-naplókban** is kaphat. Azonban támogatja a korlátozott forgatókönyveket, és nem alkalmas olyan műveletekhez, mint az ütemezett biztonsági mentés, amely jobban igazodik az erőforrás-naplókhoz, mint a tevékenységek naplói. Ha többet szeretne megtudni ezekről a korlátozásokról, valamint arról, hogy miként használhatja a Log Analytics munkaterületet az Azure Backup által védett munkaterhelések figyelésére és riasztására, tekintse meg ezt a [cikket](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-to-monitor-at-scale).

## <a name="next-steps"></a>Következő lépések

Javasoljuk, hogy olvassa el a következő cikkeket a Azure Backup használatának kiindulási pontjaiként:

* [Azure Backup áttekintése](backup-overview.md)
* [Gyakori kérdések](backup-azure-backup-faq.md)
