---
title: Vészhelyreállítás
description: Megtudhatja, hogyan állíthatja helyre az adatbázist regionális adatközpont-kimaradásból vagy meghibásodás esetén a Azure SQL Database aktív földrajzi replikálással és a Geo-visszaállítási képességekkel.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 06/21/2019
ms.openlocfilehash: 11c83a6ec364865eb3478112c9f33add22a5c09d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643269"
---
# <a name="restore-your-azure-sql-database-or-failover-to-a-secondary"></a>Azure SQL Database vagy feladatátvétel visszaállítása másodlagosra
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database a következő képességeket biztosítja az áramszünetek helyreállításához:

- [Aktív georeplikáció](active-geo-replication-overview.md)
- [Automatikus feladatátvételi csoportok](auto-failover-group-overview.md)
- [Geo-visszaállítás](recovery-using-backups.md#point-in-time-restore)
- [Zóna – redundáns adatbázisok](high-availability-sla.md)

Az üzletmenet-folytonossági forgatókönyvek és az ilyen forgatókönyveket támogató funkciók megismeréséhez tekintse meg az [üzletmenet folytonosságát](business-continuity-high-availability-disaster-recover-hadr-overview.md)ismertető témakört.

> [!NOTE]
> Ha a prémium szintű vagy üzletileg kritikus-adatbázisokat vagy-készleteket használja, a helyreállítási folyamat automatizálható, és a többi anyag nem alkalmazható.
>
> Mind az elsődleges, mind a másodlagos adatbázisnak azonos szolgáltatási szinten kell lennie. Javasoljuk továbbá, hogy a másodlagos adatbázis ugyanazzal a számítási mérettel (DTU vagy virtuális mag) legyen létrehozva, mint az elsődleges. További információ: [frissítés vagy visszalépés elsődleges adatbázisként](active-geo-replication-overview.md#upgrading-or-downgrading-primary-database).
>
> Több adatbázis feladatátvételének kezeléséhez használjon egy vagy több feladatátvételi csoportot.
> Ha egy meglévő geo-replikációs kapcsolatot ad hozzá a feladatátvételi csoporthoz, győződjön meg arról, hogy a Geo-másodlagos kiszolgáló ugyanazzal a szolgáltatási réteggel és számítási mérettel van konfigurálva, mint az elsődleges. További információ: [automatikus feladatátvételi csoportok használata több adatbázis átlátható és koordinált feladatátvételének engedélyezéséhez](auto-failover-group-overview.md).

## <a name="prepare-for-the-event-of-an-outage"></a>Felkészülés a leállás esetére

A feladatátvételi csoportok vagy a Geo-redundáns biztonsági másolatok használatával egy másik adatterületre történő helyreállítás sikeressége érdekében elő kell készítenie egy kiszolgálót egy másik adatközpont-kimaradásban, hogy az új elsődleges kiszolgálónak kellene lennie, valamint jól definiált lépéseket kell megadnia a zökkenőmentes helyreállítás érdekében. Az előkészítés lépései a következők:

- Azonosítsa a kiszolgálót egy másik régióban, hogy az új elsődleges kiszolgáló legyen. A Geo-visszaállítás esetében ez általában egy kiszolgáló a [párosított régióban](../../best-practices-availability-paired-regions.md) azon régió esetében, amelyben az adatbázis található. Ez kiküszöböli a további forgalmi költségeket a Geo-visszaállítási műveletek során.
- Azonosítsa és opcionálisan definiálja a kiszolgálói szintű IP-tűzfalszabályok szükségesek a felhasználók számára az új elsődleges adatbázis eléréséhez.
- Határozza meg, hogyan fogja átirányítani a felhasználókat az új elsődleges kiszolgálóra, például a kapcsolódási karakterláncok módosításával vagy a DNS-bejegyzések módosításával.
- Azonosítsa és opcionálisan hozza létre azokat a bejelentkezéseket, amelyeknek szerepelniük kell a főadatbázisban az új elsődleges kiszolgálón, és biztosítania kell, hogy ezek a bejelentkezések megfelelő engedélyekkel rendelkezzenek a Master adatbázisban, ha van ilyen. További információ: [SQL Database biztonság a katasztrófa utáni helyreállítás után](active-geo-replication-security-configure.md)
- Azonosítsa azokat a riasztási szabályokat, amelyeket frissíteni kell az új elsődleges adatbázishoz való leképezéshez.
- A naplózási konfiguráció dokumentálása az aktuális elsődleges adatbázisban
- Végezzen vész- [helyreállítási gyakorlatot](disaster-recovery-drills.md). A Geo-visszaállítás kimaradásának szimulálása érdekében törölheti vagy átnevezheti a forrás-adatbázist az alkalmazás kapcsolódási hibája miatt. A feladatátvételi csoportok használatával történő kimaradás szimulálása érdekében letilthatja az adatbázishoz csatlakozó webalkalmazást vagy virtuális gépet, vagy feladatátvételt hajthat végre az adatbázison az alkalmazások kapcsolódási hibái miatt.

## <a name="when-to-initiate-recovery"></a>Mikor kell kezdeményezni a helyreállítást

A helyreállítási művelet hatással van az alkalmazásra. Ehhez az SQL-kapcsolati karakterláncot vagy a DNS-sel való átirányítást kell módosítania, és maradandó adatvesztést eredményezhet. Ezért csak akkor kell elvégezni, ha a leállás valószínűleg hosszabb ideig tart, mint az alkalmazás helyreállítási idejének célkitűzése. Ha az alkalmazást éles környezetben telepítik, akkor az alkalmazás állapotának rendszeres figyelését kell végeznie, és a következő adatpontokat kell használnia, hogy a helyreállítás indokolt legyen:

1. Állandó csatlakozási hiba az alkalmazás szintjéről az adatbázisba.
2. A Azure Portal a régión belüli incidensek széles körű hatásával kapcsolatos riasztást jelenít meg.

> [!NOTE]
> Ha feladatátvételi csoportokat használ, és az automatikus feladatátvételt választotta, a helyreállítási folyamat automatizált és transzparens módon történik az alkalmazás számára.

Az alkalmazási toleranciára és az esetleges üzleti felelősségre függően a következő helyreállítási lehetőségek közül választhat.

Használja a helyreállítható [adatbázis beolvasása](/previous-versions/azure/reference/dn800985(v=azure.100)) (*LastAvailableBackupDate*) lehetőséget a legújabb földrajzi replikálási visszaállítási pont lekéréséhez.

## <a name="wait-for-service-recovery"></a>Várakozás a szolgáltatás helyreállítására

Az Azure-csapatok szorgalmasan dolgoznak a szolgáltatás rendelkezésre állásának lehető leggyorsabb helyreállításához, de a kiváltó okoktól függően órákig vagy napokig is eltarthat.  Ha az alkalmazása jelentős állásidőt képes elviselni, egyszerűen várjon, amíg a helyreállítás befejeződik. Ebben az esetben nincs szükség beavatkozásra a részen. A szolgáltatás aktuális állapotát a [Azure Service Health irányítópulton](https://azure.microsoft.com/status/)tekintheti meg. A régió helyreállítása után az alkalmazás rendelkezésre állása helyreáll.

## <a name="fail-over-to-geo-replicated-secondary-server-in-the-failover-group"></a>Feladatátvétel a feladatátvételi csoportban található másodlagos kiszolgálóra

Ha az alkalmazás leállása üzleti felelősséghez vezethet, akkor feladatátvételi csoportokat kell használnia. Lehetővé teszi az alkalmazás számára, hogy leállás esetén gyorsan visszaállítsa a rendelkezésre állást egy másik régióban. Oktatóanyag: [földrajzilag elosztott adatbázis implementálása](geo-distributed-application-configure-tutorial.md).

Az adatbázis (ok) rendelkezésre állásának visszaállításához a feladatátvételt a másodlagos kiszolgálóra kell kezdeményezni a támogatott módszerek egyikének használatával.

A következő útmutatók egyikével végezheti el a feladatátvételt egy földrajzilag replikált másodlagos adatbázisra:

- [Feladatátvétel egy földrajzilag replikált másodlagos kiszolgálóra a Azure Portal használatával](active-geo-replication-configure-portal.md)
- [Feladatátvétel a másodlagos kiszolgálóra a PowerShell használatával](scripts/setup-geodr-and-failover-database-powershell.md)
- [Feladatátvétel másodlagos kiszolgálóra a Transact-SQL (T-SQL) használatával](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true#e-failover-to-a-geo-replication-secondary)

## <a name="recover-using-geo-restore"></a>Helyreállítás a Geo-visszaállítás használatával

Ha az alkalmazás leállása nem jelent üzleti felelősséget, a [geo-visszaállítási](recovery-using-backups.md) módszer használatával helyreállíthatja az alkalmazás-adatbázis (oka) t. Létrehoz egy másolatot az adatbázisról a legújabb geo-redundáns biztonsági mentésből.

## <a name="configure-your-database-after-recovery"></a>Az adatbázis konfigurálása a helyreállítás után

Ha a Geo-visszaállítás segítségével állítja helyre a helyreállítást, meg kell győződnie arról, hogy az új adatbázisokhoz való kapcsolódás megfelelően van konfigurálva, hogy a normál alkalmazás-funkció folytatódni lehessen. Ez egy ellenőrzőlista a feladatokról, amelyekkel készen áll a helyreállított adatbázis gyártására.

### <a name="update-connection-strings"></a>A kapcsolatok karakterláncának frissítése

Mivel a helyreállított adatbázis egy másik kiszolgálón található, frissítenie kell az alkalmazás kapcsolódási karakterláncát, hogy erre a kiszolgálóra mutasson.

A kapcsolatok sztringek módosításával kapcsolatos további információkért tekintse meg a [Csatlakozáskezelő](connect-query-content-reference-guide.md#libraries)megfelelő fejlesztői nyelvét.

### <a name="configure-firewall-rules"></a>Tűzfalszabályok konfigurálása

Győződjön meg arról, hogy a kiszolgálón és az adatbázison konfigurált tűzfalszabályok megfelelnek az elsődleges kiszolgálón és az elsődleges adatbázisban konfigurált szabályoknak. További információkért lásd [: How to: Firewall Settings (tűzfalbeállítások konfigurálása (Azure SQL Database)](firewall-configure.md).

### <a name="configure-logins-and-database-users"></a>Bejelentkezések és adatbázis-felhasználók konfigurálása

Meg kell győződnie arról, hogy az alkalmazás által használt összes bejelentkezés a helyreállított adatbázist futtató kiszolgálón található. További információ: [biztonsági konfiguráció a Geo-replikációhoz](active-geo-replication-security-configure.md).

> [!NOTE]
> A vész-helyreállítási gyakorlat során konfigurálni és tesztelni kell a kiszolgáló tűzfalszabályok és bejelentkezési adatait (és azok engedélyeit). Előfordulhat, hogy ezek a kiszolgálói szintű objektumok és azok konfigurációja nem érhető el a leállás során.

### <a name="setup-telemetry-alerts"></a>Telemetria-riasztások beállítása

Győződjön meg arról, hogy a meglévő riasztási szabály beállításainak frissítése a helyreállított adatbázisra és a másik kiszolgálóra való leképezéshez szükséges.

Az adatbázis-riasztási szabályokról további információt a [Riasztási értesítések fogadása](../../azure-monitor/alerts/alerts-overview.md) és a [Service Health nyomon követése](../../service-health/service-notifications.md)című témakörben talál.

### <a name="enable-auditing"></a>Naplózás engedélyezése

Ha az adatbázis eléréséhez naplózásra van szükség, engedélyeznie kell a naplózást az adatbázis helyreállítása után. További információ: adatbázis- [naplózás](../../azure-sql/database/auditing-overview.md).

## <a name="next-steps"></a>Következő lépések

- Az automatikus biztonsági mentések Azure SQL Databaseával kapcsolatos további tudnivalókért lásd: [SQL Database automatikus biztonsági mentések](automated-backups-overview.md)
- További információ az üzletmenet-folytonosság kialakításáról és a helyreállítási forgatókönyvekről: [folytonossági forgatókönyvek](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- További információ a helyreállítás automatizált biztonsági mentések használatáról: [adatbázis visszaállítása a szolgáltatás által kezdeményezett biztonsági másolatokból](recovery-using-backups.md)