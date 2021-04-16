---
title: Útmutatás és ajánlott eljárások
description: Megismerheti a felhőbeli és helyszíni számítási feladatok felhőbe való biztonságimentéséhez ajánlott eljárásokat és útmutatókat
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 14476533cf896434182e1d63f89c6a1279b36362
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519063"
---
# <a name="backup-cloud-and-on-premises-workloads-to-cloud"></a>Felhőbeli és helyszíni számítási feladatok felhőbe való biztonsági mentése

## <a name="introduction"></a>Bevezetés

Azure Backup azure-beli adateszközöket egy egyszerű, biztonságos és költséghatékony megoldással védi, amely infrastruktúrát nem igényel. Ez az Azure beépített adatvédelmi megoldása a számítási feladatok széles köréhez. Segít megvédeni a felhőben futó kritikus fontosságú számítási feladatokat, és biztosítja, hogy a biztonsági másolatok mindig elérhetők és nagy méretekben kezelhetők a teljes biztonsági mentési környezetben.

### <a name="intended-audience"></a>Célközönség

A cikk elsődleges célcsoportja az informatikai és alkalmazás-rendszergazdák, valamint a nagy és közepes méretű szervezetek végrehajtói, akik meg szeretnék tudni az Azure beépített adatvédelmi technológiája, az Azure Backup képességeit, valamint az üzemelő példányok védelmét hatékonyabban megvalósító megoldások implementációját. A cikk feltételezi, hogy ismeri az alapvető Azure-technológiákat és az adatvédelmi fogalmakat, és rendelkezik tapasztalattal a biztonsági mentési megoldások használatában. A cikkben szereplő útmutatás megkönnyíti a biztonsági mentési megoldás tervezését az Azure-ban a már ismert minták használatával, és elkerülheti az ismert buktatókat.

### <a name="how-this-article-is-organized"></a>A cikk rendszerezése

Bár az Azure-beli infrastruktúra és alkalmazások védelme egyszerű, ha gondoskodik arról, hogy az alapul szolgáló Azure-erőforrások megfelelően vannak beállítva és optimálisan vannak használva, felgyorsíthatja az érték-érték arányt. Ez a cikk rövid áttekintést nyújt a tervezési szempontokról, és útmutatást nyújt az üzemelő példány optimális Azure Backup konfigurálásához. Megvizsgálja az alapvető összetevőket (például a Recovery Services-tárolót, a biztonsági mentési szabályzatot) és az alapfogalmakat (például a szabályozást), valamint azt, hogyan gondolhat rájuk és képességeikre részletes termékdokumentációra mutató hivatkozásokkal.

## <a name="architecture"></a>Architektúra

![Az Azure Backup architektúrája](./media/guidance-best-practices/azure-backup-architecture.png)

### <a name="workloads"></a>Számítási feladatok

Azure Backup (helyszíni és felhőbeli) számítási feladatok adatvédelmét teszi lehetővé. Ez egy biztonságos és megbízható beépített adatvédelmi mechanizmus az Azure-ban. Zökkenőmentesen skálázhatja a védelmét több számítási feladatra anélkül, hogy bármilyen felügyeleti többletterhelést okoz. Ennek engedélyezéséhez több automatizálási csatorna is elérhető (a PowerShell, a parancssori felület, Azure Resource Manager sablonok és REST API-k használatával).

* **Skálázható, tartós és biztonságos tárolás –** Azure Backup beépített biztonsági és magas rendelkezésre állású megbízható Blob Storage-tárolót használ. A biztonsági mentési adatokhoz LRS-, GRS- vagy RA-GRS-tárolókat is választhat.  

* **Natív számítási feladatok integrációja –** az Azure Backup natív integrációt biztosít az Azure-beli számítási feladatokkal (virtuális gépek, SAP HANA, SQL az Azure-beli virtuális gépeken vagy akár az Azure Files-ban) anélkül, hogy az ügynökök üzembe helyezéséhez, új szkriptek írásához vagy tároló üzembe helyezéséhez automatizálást vagy infrastruktúrát kellene kezelnie.

### <a name="data-plane"></a>Adatsík

* **Automatizált tárolókezelés** – Azure Backup automatizálja a biztonsági mentési adatok tárfiókok üzembehelyezését és kezelését, hogy a biztonsági mentési adatok növekedésével együtt skálázható legyen.

* **Rosszindulatú törlés elleni védelem –** Védelmet nyújt a biztonsági másolatok véletlen és rosszindulatú törlési kísérletei ellen a biztonsági másolatok törlésével. A törölt biztonsági mentési adatokat a rendszer 14 napig ingyenesen tárolja, és lehetővé teszi azok helyreállítását ebből az állapotból.

* **Biztonságos titkosított biztonsági mentések –** Azure Backup az Azure platform beépített biztonsági funkcióinak, például az Azure RBAC-nek és a titkosításnak a segítségével gondoskodik a biztonsági másolatok biztonságos tárolására.

* **Biztonsági mentési adatok életciklusának kezelése –** Azure Backup automatikusan megtisztítja a régebbi biztonsági mentési adatokat, hogy megfeleljenek a megőrzési szabályzatnak. Az adatokat réteg is rétegozhatja az operatív tárolótól a tárolótárolóig.

### <a name="management-plane"></a>Felügyeleti sík

* **Hozzáférés-vezérlés** – A tárolók (Recovery Services- és Backup-tárolók) biztosítják a felügyeleti képességeket, és a Azure Portal, a Backup Center, a tároló irányítópultja, az SDK, a CLI és még a REST API-kon keresztül is elérhetők. Ez egyben egy Azure RBAC-határ is, amely lehetőséget nyújt arra, hogy a biztonsági másolatok hozzáférését csak a jogosult biztonsági mentési rendszergazdákra korlátozza.

* **Szabályzatkezelés** – Azure Backup tárolókban található szabályzatok határozzák meg, hogy mikor kell aktiválni a biztonsági másolatokat, és mennyi ideig kell azokat megőrizni. Ezeket a szabályzatokat több elemre is alkalmazhatja.

* **Monitorozás és jelentéskészítés** – Azure Backup Log Analytics szolgáltatással integrálható, és lehetővé teszi a jelentések munkafüzetek segítségével történő áttekintését is.

* **Pillanatkép-kezelés** – Azure Backup azure-beli natív számítási feladatokról (virtuális gépekről és Azure Files) készít pillanatképeket, kezeli ezeket a pillanatképeket, és gyors visszaállítást tesz lehetővé ezekből. Ez a beállítás jelentősen csökkenti az adatok eredeti tárolóba való helyreállításának idejét.

## <a name="vault-considerations"></a>Tárolóval kapcsolatos szempontok

Azure Backup tárolókat (Recovery Services és Backup-tárolók) használ a biztonsági mentések vezénylése és kezelése érdekében. Tárolókat is használ a biztonságimentett adatok tárolására. A tároló hatékony kialakítása segít a szervezeteknek kialakítani egy struktúrát a biztonsági mentési eszközök rendszerezéséhez és kezeléséhez az Azure-ban az üzleti prioritások támogatása érdekében. A tárolók létrehozásakor vegye figyelembe az alábbi irányelveket:  

### <a name="align-to-subscription-design-strategy"></a>Igazodás az előfizetés-tervezési stratégiához

Mivel a tároló hatóköre egy előfizetésre terjed ki, a tároló  kialakítása megfelel az előfizetés-tervezési stratégiának, például az alkalmazáskategória-stratégiának, ahol az előfizetések adott alkalmazások vagy szolgáltatások alapján vagy az alkalmazásarchetípusok szerint vannak elkülönítve. További információkért tekintse meg [ezt a cikket](/azure/cloud-adoption-framework/decision-guides/subscriptions/).

### <a name="single-or-multiple-vault"></a>Egy vagy több tároló

A biztonsági másolatok rendszerezéséhez és kezeléséhez egyetlen tárolót vagy több tárolót is használhat. Tekintse meg a következő útmutatót:

* Ha a számítási feladatokat egyetlen előfizetés és egyetlen erőforrás kezeli, akkor egyetlen tárolóval monitorozhatja és kezelheti a biztonsági mentési tulajdont.

* Ha a számítási feladatok előfizetések között vannak elszava, akkor előfizetésenként több tárolót is létrehozhat.
  * A Backup Center lehetővé teszi, hogy egyetlen ablaktáblával kezelje a Biztonsági mentéssel kapcsolatos összes feladatot. [További információt itt talál]().
  * A nézeteket munkafüzetsablonokkal szabhatja testre. Backup-kezelő egy ilyen sablon az Azure-beli virtuális gépekhez. [További információt itt talál](monitor-azure-backup-with-backup-explorer.md).
  * Ha konzisztens szabályzatra volt szüksége több tárolóra, akkor az Azure Policy használatával több tárolóra is propagálhatja a biztonsági mentési szabályzatot. Írhat olyan egyéni [Azure Policy,](../governance/policy/concepts/definition-structure.md) amely a ["deployifnotexists"](../governance/policy/concepts/effects.md#deployifnotexists) hatás segítségével propagál egy biztonsági mentési szabályzatot több tárolóra. Ezt az [](../governance/policy/assign-policy-portal.md) Azure Policy-definíciót hozzárendelheti egy adott hatókör (előfizetés vagy erőforráscsoport) számára is, így az üzembe helyez egy "biztonsági mentési szabályzat" erőforrást az összes Recovery Services-tárolóban a Azure Policy-hozzárendelés hatókörében. A biztonsági mentési házirend beállításait (például a biztonsági mentés gyakoriságát, a megőrzést stb.) a felhasználónak paraméterként kell megadnia a Azure Policy hozzárendelésében.

* A szervezeti terhelés növekedésével a következő okokból érdemes lehet áthelyezni a számítási feladatokat az előfizetések között: biztonsági mentési szabályzat szerinti igazítás, tárolók konszolidálása, alacsonyabb redundancia csökkentés a költségek csökkentése érdekében (áthelyezés GRS-ről LRS-re).  Azure Backup recovery services-tárolók Azure-előfizetések közötti vagy ugyanazon előfizetésen belüli másik erőforráscsoportba való áthelyezését támogatja. [További információt itt talál](backup-azure-move-recovery-services-vault.md).

### <a name="review-default-settings"></a>Alapértelmezett beállítások áttekintése

Mielőtt biztonsági mentéseket konfigurál a tárolóban, tekintse át a Tárreplikáció típusa és a Biztonsági beállítások alapértelmezett beállításait, hogy megfeleljen a követelményeknek.

* *A Tárreplikáció típusa* alapértelmezés szerint Georedundáns (GRS) lesz. A biztonsági mentés konfigurálása után a módosítás lehetősége le van tiltva. Kövesse [az alábbi](backup-create-rs-vault.md#set-storage-redundancy) lépéseket a beállítások áttekintéséhez és módosításához.

* *A soft delete* alapértelmezés szerint Engedélyezve van az újonnan létrehozott tárolókban, hogy megvédje a biztonsági mentési adatokat a véletlen vagy rosszindulatú törlésektól. Kövesse [az alábbi](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) lépéseket a beállítások áttekintéséhez és módosításához.

* *A régiók közötti visszaállítás* lehetővé teszi az Azure-beli virtuális gépek visszaállítását egy másodlagos régióban, amely egy azure-beli párosított régió. Ez a beállítás lehetővé teszi a naplózási vagy megfelelőségi követelmények teljesítését, valamint a virtuális gép vagy annak lemezének visszaállítását, ha katasztrófa történik az elsődleges régióban. A CRR bármely GRS-tárolóhoz használható. [További információt itt talál](backup-create-rs-vault.md#set-cross-region-restore).

* A tároló kialakításának véglegesítője előtt tekintse át a tárolótámogatási mátrixokat, hogy átláthassa, milyen tényezők befolyásolhatják vagy korlátozhatják a kialakítási lehetőségeket. [](backup-support-matrix.md#vault-support)

## <a name="backup-policy-considerations"></a>Biztonsági mentési szabályzatokkal kapcsolatos szempontok

Azure Backup szabályzat két összetevőből áll: *Ütemezés* (mikor kell biztonsági másolatot készíteni) és *Megőrzés* (mennyi ideig őrizze meg a biztonsági másolatot). A szabályzatot a biztonsági mentése alatt lévő adatok típusa, az RTO/RPO követelményei, a működési vagy jogszabályi megfelelési igények és a számítási feladatok típusa (például virtuális gép, adatbázis, fájlok) alapján határozhatja meg. [További információt itt talál](backup-architecture.md#backup-policy-essentials).

A biztonsági mentési szabályzat létrehozásakor vegye figyelembe a következő irányelveket:

### <a name="schedule-considerations"></a>Ütemezési szempontok

* Érdemes lehet olyan virtuális gépeket csoportosíteni, amelyekhez azonos ütemezési kezdési idő, gyakoriság és megőrzési beállításokra van szükség egyetlen szabályzaton belül.

* Győződjön meg arról, hogy a biztonsági mentés ütemezett kezdési ideje nem csúcsidőszakban van az éles környezetben.

* A biztonsági másolatok forgalmának elosztásához fontolja meg a különböző virtuális gépek biztonsági mentését a nap különböző időpontokban, és győződjön meg arról, hogy az időpontok nem fedik át egymást.

### <a name="retention-considerations"></a>Megőrzési szempontok

* A rövid távú megőrzés lehet "perc" vagy "naponta". A "Heti", "havi" vagy "éves" biztonsági mentési pontok megőrzését hosszú távú megőrzésnek nevezzük.

* Hosszú távú megőrzés:
  * Tervezett (megfelelőségi követelmények) – ha előre tudja, hogy az adatokra az aktuális időponttól való évekre van szükség, használja a Hosszú távú megőrzést.
  * Nem tervezett (igény szerinti követelmény) – ha nem tudja előre, a használatával igény szerint használhatja az adott egyéni adatmegőrzési beállításokat (ezeket az egyéni adatmegőrzési beállításokat a szabályzatbeállítások nem befolyásolják).

* Igény szerinti biztonsági mentés egyéni megőrzéssel – ha olyan biztonsági mentést kell készítenie, amely nincs ütemezve a biztonsági mentési szabályzaton keresztül, akkor igény szerinti biztonsági mentést is használhat. Ez hasznos lehet az ütemezett biztonsági mentéshez nem megfelelő biztonsági mentések készítéséhez vagy részletes biztonsági mentéshez (például naponta több IaaS virtuális gép biztonsági mentéséhez, mivel az ütemezett biztonsági mentés naponta csak egy biztonsági mentést engedélyez). Fontos megjegyezni, hogy az ütemezett szabályzatban meghatározott adatmegőrzési szabályzat nem vonatkozik az igény szerinti biztonsági mentésre.

### <a name="optimize-backup-policy"></a>Biztonsági mentési szabályzat optimalizálása

* Az üzleti követelmények változásával szükség lehet a megőrzés időtartamának meghosszabbítására vagy csökkentésére. Ha így van, a következőkre számíthat:  
  * Ha meghosszabbítja a megőrzési megőrzést, a meglévő helyreállítási pontok meg vannak jelölve, és az új szabályzatnak megfelelően maradnak.
  * Ha csökkenti a megőrzési megőrzést, a helyreállítási pontok a következő tisztítási feladat során törlésre vannak megjelölve, majd törlődnek.
  * A legújabb megőrzési szabályok minden adatmegőrzési pontra érvényesek (kivéve az igény szerinti adatmegőrzési pontokat). Így ha a megőrzési időtartam meg van hosszabbva (például 100 napra), akkor a biztonsági mentés, majd a megőrzés csökkentése (például 100 nap és hét nap) között az összes biztonsági mentési adat az utolsó megadott megőrzési időszak (tehát 7 nap) szerint lesz megőrizve.

* Azure Backup biztosítja a biztonsági másolatok védelmét és kezelését:
  * *Állítsa le a védelmet, és őrizze meg a biztonsági mentési adatokat.* Ha kiveszi vagy leszereli az adatforrást (virtuális gép, alkalmazás), de naplózási vagy megfelelőségi célokból meg kell őriznie az adatokat, akkor ezzel a beállítással leállíthatja, hogy a jövőbeli biztonsági mentési feladatok megvédjék az adatforrást, és megőrizze azokat a helyreállítási pontokat, amelyekről biztonsági másolat lett készítve. Ezután visszaállíthatja vagy folytathatja a virtuális gépek védelmét.
  * *Állítsa le a védelmet, és törölje a biztonsági mentési adatokat.* Ez a beállítás leállítja az összes jövőbeli biztonsági mentési feladat védelmét a virtuális gép számára, és törli az összes helyreállítási pontot. A virtuális gépet nem tudja visszaállítani, és nem használhatja a Biztonsági mentés folytatása lehetőséget sem.

  * Ha folytatja a védelmet (olyan adatforrás esetén, amely le lett állítva az adatok megőrzésével), akkor a megőrzési szabályok érvényesek lesznek. A lejárt helyreállítási pontok el lesznek távolítva (az ütemezett időpontban).

* A szabályzattervezés befejezése előtt fontos tisztában lenni az alábbi tényezőkkel, amelyek befolyásolhatják a tervezési döntéseket.
  * A biztonsági mentési szabályzat hatóköre egy tárolóra terjed ki.
  * A szabályzatonkénti elemek száma korlátozva van (például 100 virtuális gép). A méretezéshez duplikált szabályzatokat hozhat létre azonos vagy eltérő ütemezéssel.
  * Adott helyreállítási pontok nem törölhetők szelektíven.
  * Nem tilthatja le teljesen az ütemezett biztonsági mentést, és nem tarthatja védett állapotban az adatforrást. A szabályzat segítségével konfigurálható legkisebb gyakori biztonsági mentés egy heti ütemezett biztonsági mentés. Alternatív megoldásként leállíthatja a védelmet az adatok megőrzésével, és minden alkalommal engedélyezheti a védelmet, amikor biztonsági másolatot szeretne készíteni, igény szerinti biztonsági mentést készít, majd kikapcsolja a védelmet, de megőrzi a biztonsági mentési adatokat. [További információt itt talál](backup-azure-manage-vms.md#stop-protecting-a-vm).

## <a name="security-considerations"></a>Biztonsági szempontok

A biztonsági másolatok adatainak védelme és a vállalat biztonsági igényeinek kielégítése érdekében a Azure Backup titkosságot, integritást és rendelkezésre állást biztosít a szándékos támadások és az értékes adatokkal és rendszerekkel való visszaélés ellen. Vegye figyelembe a következő biztonsági irányelveket a Azure Backup megoldáshoz:

### <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés

* Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) lehetővé teszi a hozzáférések finomhangolását, a feladatok elkülönítését a csapaton belül, és csak a munkájuk elvégzéséhez szükséges felhasználói hozzáférést. [További információt itt talál](backup-rbac-rs-vault.md).

* Azure Backup három beépített szerepkört biztosít a biztonsági mentési felügyeleti műveletek vezérléséhez: Biztonsági mentési közreműködők, operátorok és olvasók. [További információt itt talál](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions).

* Azure Backup számos biztonsági vezérlő van beépítve a szolgáltatásba a biztonsági rések megelőzésére, észlelésére és az ezekre való válaszadásra (További információ)

* A Recovery Services-tárolók által használt tárfiókok el vannak különítve, és a felhasználók semmilyen rosszindulatú céllal nem érhetik el őket. A hozzáférés csak olyan felügyeleti Azure Backup keresztül engedélyezett, mint például a visszaállítás.

### <a name="encryption-of-data-in-transit-and-at-rest"></a>Átvitel közbeni és nem használt adatok titkosítása

A titkosítás védi az adatokat, és segít a szervezeti biztonsági és megfelelőségi kötelezettségvállalások teljesítésében.

* Az Azure-ban az Azure-tároló és a tároló közötti átvitel során áteső adatokat HTTPS védi. Ezek az adatok az Azure-hálózaton belül maradnak.

* Az adatok biztonsági mentése automatikusan titkosítva lesz a Microsoft által kezelt kulcsokkal. Másik lehetőségként használhatja a saját kulcsait is, más néven a felhasználó által kezelt [kulcsokat.](encryption-at-rest-with-cmk.md)

* Azure Backup támogatja az olyan Azure-beli virtuális gépek biztonsági mentését és visszaállítását, amelyek operációsrendszer-/adatlemezei titkosítva vannak Azure Disk Encryption (ADE). [További információt itt talál](backup-azure-vms-encryption.md).

### <a name="protection-of-backup-data-from-unintentional-deletes"></a>Biztonsági másolatok adatainak védelme a véletlen törlésekkel ellen

Azure Backup biztonsági funkciókat biztosít, amelyek a törlést követően is segítenek megvédeni a biztonsági mentési adatokat. Helyreállító törlés esetén, ha a felhasználó törli a biztonsági mentést (egy virtuális gép, egy SQL Server-adatbázis, egy Azure-fájlmegosztás vagy egy SAP HANA-adatbázis biztonsági másolatát), a rendszer további 14 napig megőrzi a biztonsági mentési adatokat, így adatvesztés nélkül helyreállíthatja a biztonsági mentési elemet. A "soft delete" állapotban a biztonsági másolatok adatainak további 14 napos megőrzése nem jár költséggel. [További információt itt talál](backup-azure-security-feature-cloud.md).

### <a name="monitoring-and-alerts-of-suspicious-activity"></a>Gyanús tevékenységek monitorozása és riasztásai

Azure Backup beépített monitorozási és riasztási képességeket biztosít az eseményekhez kapcsolódó műveletek megtekintéséhez és Azure Backup. [További információt itt talál](security-overview.md#monitoring-and-alerts-of-suspicious-activity).

### <a name="security-features-to-help-protect-hybrid-backups"></a>Biztonsági funkciók a hibrid biztonsági mentések védelméhez

Azure Backup szolgáltatás a Microsoft Azure Recovery Services- (MARS-) ügynököt használja a fájlok, mappák és kötetek vagy rendszerállapotok biztonsági mentésére és visszaállítására egy helyszíni számítógépről az Azure-ba. A MARS már rendelkezik biztonsági funkciókkal: egy jelszó, amely a Azure Backup-ból való letöltést követően titkosítja a titkosítást, majd visszafejti a Azure Backup-ból való letöltést követően, a törölt biztonsági másolatok adatait a törlés dátumát követően további 14 napig őrzi meg a rendszer, valamint a kritikus műveletet (például: jelszó módosítása) csak olyan felhasználók hajthatnak végre, akik érvényes Azure-beli hitelesítő adatokkal vannak. [További információt itt talál](backup-azure-security-feature.md).

## <a name="network-considerations"></a>Hálózati szempontok

Azure Backup adatoknak a számítási feladatból a Recovery Services-tárolóba való mozgása szükséges. Azure Backup a biztonsági másolatok adatainak véletlen felfedés elleni védelmére (például a hálózatra támadás). Tekintse meg a következő útmutatót:

### <a name="internet-connectivity"></a>Internetkapcsolat

* *Azure-beli* virtuális gépek biztonsági mentése – a tárterület és az Azure Backup közötti összes szükséges kommunikáció és adatátvitel anélkül történik meg az Azure-hálózaton belül, hogy hozzá kellene férni a virtuális hálózathoz. Így a biztonságos hálózatokon elhelyezett Azure-beli virtuális gépek biztonsági mentéséhez nem szükséges IP-címhez vagy teljes tartományhoz hozzáférést biztosítani.

* *SAP HANA* Azure-beli virtuális gépen található SQL Server azure-beli virtuális gépen található adatbázisokhoz – az Azure Backup-szolgáltatáshoz, az Azure Storage-hoz és az Azure Active Directory. Ez privát végpontok használatával vagy a szükséges nyilvános IP-címek vagy teljes tartomány nevek elérésének engedélyezésével érhető el. Ha nem engedélyezi a megfelelő kapcsolódást a szükséges Azure-szolgáltatásokhoz, az az olyan műveletek sikertelenségéhez vezethet, mint az adatbázis-felderítés, a biztonsági mentés konfigurálása, a biztonsági mentések végrehajtása és az adatok visszaállítása. Az NSG-címkék, az Azure Firewall és a HTTP-proxy használata közbeni teljes körű hálózati útmutatásért tekintse meg ezeket az [SQL-](backup-sql-server-database-azure-vms.md#establish-network-connectivity) és [SAP HANA cikkeket.](./backup-azure-sap-hana-database.md#establish-network-connectivity)

* *Hibrid* – a MARS (Microsoft Azure Recovery Services-ügynöknek hálózati hozzáférésre van szüksége minden kritikus művelethez – telepítéshez, konfiguráláshoz, biztonsági mentéshez és visszaállításhoz. A MARS-ügynök a Azure Backup-szolgáltatáshoz [Azure ExpressRoute-en](install-mars-agent.md#use-azure-expressroute) keresztül csatlakozhat nyilvános társviszony-létesítés és Microsoft-társviszony-létesítés használatával, privát végpontok használatával vagy [proxyn/tűzfalon](install-mars-agent.md#verify-internet-access)keresztül, megfelelő hozzáférés-vezérléssel. [](install-mars-agent.md#private-endpoints)

### <a name="private-endpoints-for-azure-backup"></a>Privát végpontok Azure Backup

Az Azure [privát végpont](../private-link/private-endpoint-overview.md) egy olyan hálózati adapter, amely privát módon és biztonságosan csatlakoztatja a Azure Private Link. Azure Backup lehetővé teszi az adatok biztonságos biztonsági mentését és visszaállítását a Recovery Services-tárolókból privát végpontok használatával.

* Ha engedélyezi a privát végpontokat a tárolóhoz, azok csak az SQL és SAP HANA számítási feladatok biztonsági mentésére és visszaállítására szolgálnak egy Azure-beli virtuális gép és MARS-ügynök biztonsági mentésében.  A tárolót más számítási feladatok biztonsági mentéséhez is használhatja (ezek azonban nem igényelnek privát végpontokat). Az SQL- és SAP HANA számítási feladatok biztonsági mentése, valamint a MARS-ügynökkel való biztonsági mentés mellett privát végpontok is használhatók fájl-helyreállítás végrehajtásához az Azure-beli virtuális gépek biztonsági mentésekor. [További információt itt talál](private-endpoints.md#recommended-and-supported-scenarios).

* Azure Active Directory jelenleg nem támogatja a privát végpontokat. Ezért a Azure Active Directory-hoz szükséges IP-címeket és FQDN-eket kimenő hozzáféréssel kell biztosítani a biztonságos hálózatról, amikor Azure-beli virtuális gépeken és MARS-ügynökkel biztonsági másolatot készít az adatbázisokról. Az Azure AD-hez való hozzáférés engedélyezéséhez NSG- és Azure Firewall címkéket is használhat. Az előfeltételekről itt [olvashat bővebben.](./private-endpoints.md#before-you-start)

## <a name="governance-considerations"></a>Szabályozási szempontok

Az Azure-ban a szabályozást elsősorban a Azure Policy és [a](../governance/policy/overview.md) [Azure Cost Management.](../cost-management-billing/cost-management-billing-overview.md) [Azure Policy](../governance/policy/overview.md) segítségével szabályzatdefiníciókat hozhat létre, rendelhet hozzá és kezelhet az erőforrásokra vonatkozó szabályok érvényesítéséhez. Ez a funkció a vállalati szabványoknak megfelelően tartja be ezeket az erőforrásokat. [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) lehetővé teszi az Azure-erőforrások és más felhőszolgáltatók felhőhasználatának és költségeinek nyomon követését. Emellett az alábbi eszközök, például az Azure-díjkalkulátor és a [Azure Advisor](../advisor/advisor-overview.md) is fontos szerepet játszanak a költségkezelési folyamatban. [](https://azure.microsoft.com/pricing/calculator/)

### <a name="azure-backup-support-two-key-scenarios-via-built-in-azure-policy"></a>Azure Backup két kulcsfontosságú forgatókönyvet támogat a beépített Azure Policy

* Győződjön meg arról, hogy az újonnan létrehozott, üzleti fontosságú gépekről a megfelelő megőrzési beállításokkal automatikusan biztonságimentés készült. Azure Backup egy beépített szabályzatot biztosít (Azure Policy használatával), amely egy előfizetésen vagy erőforráscsoporton belül egy adott helyen található összes Azure-beli virtuális géphez hozzárendelhető. Ha ezt a szabályzatot egy adott hatókörhöz rendeli, a hatókörben létrehozott összes új virtuális gép automatikusan konfigurálva lesz egy ugyanazon a helyen és előfizetésben lévő meglévő tárolóba való biztonsági mentésre. A felhasználó megadhatja azt a tárolót és adatmegőrzési szabályzatot, amelyhez a biztonságimentett virtuális gépeket társítja. [További információt itt talál](backup-azure-auto-enable-backup.md).

* Győződjön meg arról, hogy az újonnan létrehozott tárolókhoz engedélyezve van a diagnosztika a jelentések támogatásához. A diagnosztikai beállítások tárolónkénti manuális hozzáadása gyakran nehézkes feladat lehet. Emellett minden újonnan létrehozott tárolón engedélyezni kell a diagnosztikai beállításokat, hogy megtekintsen jelentéseket a tárolóhoz. A diagnosztikai beállítások nagy léptékű létrehozásának egyszerűsítése érdekében (a Log Analytics a célhely), Azure Backup beépített Azure Policy. Ez a szabályzat egy LA diagnosztikai beállítást ad hozzá az egyes előfizetések vagy erőforráscsoportok összes tárolójához. A következő szakaszok a szabályzat használatára vonatkozó utasításokat tartalmaznak. [További információt itt talál](azure-policy-configure-diagnostics.md).

### <a name="azure-backup-cost-considerations"></a>Azure Backup költséggel kapcsolatos szempontok

A Azure Backup szolgáltatás képességei rugalmasságot biztosítanak a költségek hatékony kezeléséhez, és továbbra is megfelelnek a BCDR (üzletmenet-folytonossági és vészhelyreállítási) üzleti követelményeinek. Tekintse meg a következő útmutatót:

* A díjkalkulátor segítségével kiértékelheti és optimalizálhatja a költségeket a különböző szintek módosításával. [További információt itt olvashat](azure-backup-pricing.md)

* Backup-kezelő: A Backup-kezelő vagy Backup-jelentések segítségével megértheti és optimalizálhatja a biztonsági másolatok tárterületének növekedését, valamint leállíthatja a nem kritikus számítási feladatok vagy a törölt virtuális gépek biztonsági mentését. Biztonsági mentésből a teljes tulajdon összesített nézetét is megtekintheti. Ez nem csupán a biztonsági mentési elemekre vonatkozó információkat, hanem azokat az erőforrásokat is tartalmazza, amelyek nincsenek konfigurálva a biztonsági mentéshez. Ez biztosítja, hogy soha ne hagyja ki a növekvő tulajdon kritikus fontosságú adatainak védelmét, és a biztonsági másolatok nem kritikus számítási feladatokra vagy törölt számítási feladatokra vannak optimalizálva.

* Biztonsági mentési szabályzat optimalizálása
  * Optimalizálja az ütemezési és megőrzési beállításokat a számítási feladatok archetípusai alapján (például kritikus fontosságú, nem kritikus fontosságú)
  * Az azonnali visszaállítás megőrzési beállításainak optimalizálása
  * Válassza ki a követelményeknek megfelelő biztonsági mentési típust, miközben a támogatott biztonsági mentési típusokat (teljes, növekményes, napló, különbségi) a számítási feladat Azure Backup figyelembe véve.

* Lemezek szelektív biztonsági mentése: A lemezek kizárása (előzetes verziójú funkció) hatékony és költséghatékony választás a kritikus adatok szelektív biztonsági mentéséhez. Például csak egy lemezről szeretne biztonságimentet, ha nem szeretne biztonságimentet kapni a virtuális géphez csatolt többi lemezről. Ez több biztonsági mentési megoldás esetén is hasznos. Ha például biztonsági mentést készít az adatbázisokról vagy az adatokról egy számítási feladat biztonsági mentési megoldásával (SQL Server-adatbázis az Azure-beli virtuális gép biztonsági mentésében), és azure-beli virtuálisgép-szintű biztonsági mentést szeretne használni a kiválasztott lemezekhez.

* Azure Backup pillanatképeket készít az Azure-beli virtuális gépekről, és lemezekkel együtt tárolja azokat a helyreállítási pont létrehozásának felgyorsítása és a visszaállítási műveletek felgyorsítása érdekében. Ezt azonnali visszaállításnak nevezzük. Alapértelmezés szerint az azonnali visszaállítási pillanatképek két napig vannak megőrizve. Ez a funkció lehetővé teszi a visszaállítási műveleteket ezekről a pillanatképekről a visszaállítási idők csökkentésével. Csökkenti az adatok átalakításához és a tárolóból való visszamásolódáshoz szükséges időt. Ennek eredményeképpen olyan tárolási költségeket fog látni, amelyek az adott időszakban készített pillanatképekkel kapcsolatosak. [További információt itt talál](backup-instant-restore-capability.md#configure-snapshot-retention).

* Azure Backup tároló Tárreplikáció típusa alapértelmezés szerint Georedundáns (GRS) lesz. Ez a beállítás az elemek védelme után nem módosítható. A georedundáns tárolás (GRS) magasabb szintű adatmegőrzést biztosít, mint a helyileg redundáns tárolás (LRS), lehetővé teszi a régiók közötti visszaállítás használatát, és többe kerül. Tekintse át az alacsonyabb költségek és a magasabb adatmegőrzés közötti különbségeket, és döntse el, mi a legmegfelelőbb az Ön forgatókönyvéhez. [További információ itt](backup-create-rs-vault.md#set-storage-redundancy)

* Ha a virtuális gépen belül futó számítási feladatot és magát a virtuális gépet is védi, ellenőrizze, hogy szükség van-e erre a kettős védelemre.

## <a name="monitoring-and-alerting-considerations"></a>Monitorozási és riasztási szempontok

Biztonságimásolat-készítő felhasználóként vagy rendszergazdaként képesnek kell lennie az összes biztonsági mentési megoldás figyelése és a fontos forgatókönyvekről való értesítésre. Ez a szakasz a Azure Backup figyelési és értesítési képességeit részletezi.

### <a name="monitoring"></a>Figyelés

* Azure Backup beépített **feladatfigyelési** adatokat biztosít az olyan műveletekhez, mint például a biztonsági mentés konfigurálása, a biztonsági mentés, a visszaállítás, a biztonsági mentés törlése stb. Ennek hatóköre a tárolóra terjed ki, és ideális egyetlen tároló monitorozása során. [További információt itt talál](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault).

* Ha nagy léptékben kell figyelnie az üzemeltetési tevékenységeket, a Backup-kezelő összesített nézetet biztosít a teljes biztonsági mentési tulajdonról, lehetővé téve a részletes lehatolási elemzést és a hibaelhárítást.  Ez egy beépített Azure Monitor-munkafüzet, amely egyetlen központi helyet biztosít a teljes Azure-beli biztonsági mentési tulajdon üzemeltetési tevékenységeinek figyelése során, a bérlőkre, helyekre, előfizetésekre, erőforráscsoportokra és tárolókra kiterjedően. [További információt itt talál](monitor-azure-backup-with-backup-explorer.md).
  * Segítségével azonosíthatja azokat az erőforrásokat, amelyek nincsenek konfigurálva a biztonsági mentéshez, és biztosíthatja, hogy soha ne hagyja ki a növekvő tulajdonban álló kritikus fontosságú adatok védelmét.
  * Az irányítópult az elmúlt hét napra (maximum) vonatkozó működési tevékenységeket tartalmaz. Ha meg kell őriznie ezeket az adatokat, akkor Excel-fájlként exportálhatja és megtarthatja őket.
  * Ha Ön egy Azure Lighthouse felhasználó, több bérlő adatait is megtekintheti, így lehetővé teszi a határvonal-figyelőt.

* Ha hosszú távon meg kell őriznie és meg kell tekintenie az operatív tevékenységeket, használja a Jelentések **jelentéseket.** A biztonsági mentések rendszergazdái számára gyakori követelmény, hogy hosszabb időtartamra vonatkozó adatokon alapuló elemzéseket szerezzenek a biztonsági másolatok alapján. Az ilyen megoldások a következő esetekben használhatók:
  * A felhasznált felhőalapú tárolók allokálása és előrejelzése.
  * A biztonsági mentések és a visszaállítások naplózása.
  * A fő trendek azonosítása különböző részletességi szinteken.

* ráadásul
  * Adatokat (például feladatokat, szabályzatokat stb.) küldhet a **Log Analytics-munkaterületre.** Ez lehetővé teszi a Azure Monitor-naplók funkcióinak használatát az adatok és az Azure Monitor által gyűjtött egyéb monitorozási adatok korrelációja, több Azure-előfizetésből és -bérlőből származó naplóbejegyzések egyetlen helyre történő konszolidálása az együttes elemzéshez, naplólekérdezések használata összetett elemzések elvégzéséhez és a naplóbejegyzések mélyebb elemzéséhez. [További információt itt talál](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace).
  * Adatokat küldhet az Eseményközpontba, hogy az Azure-n kívüli bejegyzéseket küldjön, például egy külső SIEM-nek (biztonsági információk és események kezelése) vagy más naplóelemzési megoldásnak. [További információt itt talál](../azure-monitor/essentials/activity-log.md#send-to-azure-event-hubs).
  * Ha 90 napnál hosszabb ideig szeretné megőrizni a naplóadatokat naplózás, statikus elemzés vagy biztonsági mentés céljából, elküldheti az adatokat egy Azure Storage-fiókba. Ha csak legfeljebb 90 napig kell megőriznie az eseményeket, nem kell archívumokat beállítania a tárfiókban, mivel a tevékenységnapló eseményei 90 napig maradnak az Azure platformon. [További információ](../azure-monitor/essentials/activity-log.md#send-to--azure-storage).

### <a name="alerting"></a>Riasztások kezelése

* A riasztások elsődlegesen a megfelelő műveletekkel kapcsolatos értesítéseket küldik. A Biztonsági mentési riasztások szakasz a biztonsági mentési szolgáltatás által Azure Backup riasztásokat jeleníti meg.

* Azure Backup beépített riasztási értesítési mechanizmust biztosít **e-mailben** a hibák, figyelmeztetések és kritikus műveletek esetén. Megadhat egyéni e-mail-címeket vagy terjesztési listákat, amelyek értesítést kaphatnak a riasztások generálásakor. Azt is eldöntheti, hogy minden egyes riasztásról értesítést kap, vagy óránkénti kivonatba csoportosítja őket, majd értesítést kap.
  * Ezeket a riasztásokat a szolgáltatás határozza meg, és korlátozott forgatókönyvekhez nyújtanak támogatást – biztonsági mentési/visszaállítási hibák, védelem leállítása az adatok megőrzésével/Védelem leállítása törlési adatokkal stb. [További információt itt talál](backup-azure-monitoring-built-in-monitor.md#alert-scenarios).
  * Ha kipusztító művelet, például a védelem törlési adatokkal való leállítása történik, a rendszer riasztást küld,  és e-mailt küld az előfizetés tulajdonosainak, rendszergazdáinak és társadminisztr rendszergazdáinak, még akkor is, ha az értesítések nincsenek konfigurálva a Recovery Services-tárolóhoz.
  * Bizonyos számítási feladatok nagy gyakori hibákat okozhatnak (például 15 percenként SQL Server hibákat). Annak érdekében, hogy ne terhelődjön túl az egyes hibák előfordulásakor felhozott riasztások, a rendszer összesítenie kell a riasztásokat. [További információt itt talál](backup-azure-monitoring-built-in-monitor.md#consolidated-alerts).
  * A beépített riasztások nem szabhatók testre, és csak az ebben a Azure Portal.

* Ha egyéni riasztásokat kell **létrehoznia** (például sikeres feladatok riasztásai), használja a Log Analyticset. A Azure Monitor létrehozhat saját riasztásokat egy Log Analytics-munkaterületen. A hibrid számítási feladatok (DPM/MABS) is küldhetnek adatokat LA-nak, és lazán keresztül is küldhetnek gyakori riasztásokat a felhőben támogatott Azure Backup.

* Az értesítéseket a beépített Recovery Services-tároló tevékenységnaplóiban **is lekértheti.** Azonban korlátozott forgatókönyveket támogat, és nem alkalmas olyan műveletekhez, mint az ütemezett biztonsági mentés, amely jobban igazodik az erőforrásnaplókhoz, mint a tevékenységnaplókhoz. Ha többet szeretne megtudni ezekről a korlátozásokról, illetve arról, hogyan használhatja a Log Analytics-munkaterületet a monitorozáshoz és a riasztáshoz nagy méretekben a Azure Backup által védett összes számítási feladathoz, tekintse meg ezt a [cikket.](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-to-monitor-at-scale)

## <a name="next-steps"></a>Következő lépések

Javasoljuk, hogy a következő cikkeket kiindulási pontként olvassa el a Azure Backup:

* [Azure Backup áttekintése](backup-overview.md)
* [Gyakori kérdések](backup-azure-backup-faq.yml)
