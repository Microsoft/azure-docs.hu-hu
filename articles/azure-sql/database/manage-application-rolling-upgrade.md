---
title: Működés közbeni alkalmazásfrissítések
description: Megtudhatja, hogyan használhatja a Azure SQL Database geo-replikálást a felhőalapú alkalmazások működés közbeni frissítésének támogatásához
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 02/13/2019
ms.openlocfilehash: b7d21852ad684782fa1cb917442fee236d3c882b
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102502144"
---
# <a name="manage-rolling-upgrades-of-cloud-applications-by-using-sql-database-active-geo-replication"></a>Felhőalapú alkalmazások működés közbeni frissítésének kezelése SQL Database aktív földrajzi replikálás használatával
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ismerje meg, hogyan használhatja az [aktív földrajzi replikálást](auto-failover-group-overview.md) Azure SQL Database a felhőalapú alkalmazások működés közbeni frissítésének engedélyezéséhez. Mivel a frissítések zavaró műveletek, az üzleti folytonosság megtervezésének és kialakításának részét kell képezniük. Ebben a cikkben két különböző módszert vizsgálunk a frissítési folyamat előkészítéséhez és az egyes lehetőségek előnyeinek és kompromisszumának megvitatásához. Ebben a cikkben egy olyan alkalmazásra hivatkozunk, amely egy olyan webhelyből áll, amely egyetlen adatbázishoz van csatlakoztatva adatrétegként. Célunk, hogy a felhasználói élmény jelentős hatása nélkül frissítse az alkalmazás 1. verzióját (v1) a 2-es verzióra (v2).

A frissítési lehetőségek kiértékelése során vegye figyelembe a következő tényezőket:

* Az alkalmazások rendelkezésre állásának befolyásolása a verziófrissítések során, például hogy az alkalmazás-függvények meddig korlátozhatók vagy csökkenhetnek.
* Visszaállítási lehetőség, ha a frissítés sikertelen.
* Az alkalmazás biztonsági rése, ha nem kapcsolódó, katasztrofális hiba történik a frissítés során.
* Teljes dollár díj. Ez a tényező további adatbázis-redundanciát és a frissítési folyamat által használt ideiglenes összetevők növekményes költségeit tartalmazza.

## <a name="upgrade-applications-that-rely-on-database-backups-for-disaster-recovery"></a>A vész-helyreállítási adatbázis biztonsági mentésére támaszkodó alkalmazások frissítése

Ha az alkalmazás az adatbázis automatikus biztonsági mentésére támaszkodik, és a Geo-visszaállítást használja a vész-helyreállításhoz, akkor egyetlen Azure-régióba kerül üzembe. A felhasználói fennakadás csökkentése érdekében hozzon létre egy átmeneti környezetet az adott régióban a frissítésben érintett összes alkalmazás-összetevővel. Az első diagram a frissítési folyamat előtt mutatja be az operatív környezetet. A végpont a `contoso.azurewebsites.net` webalkalmazás éles környezetét jelöli. Ahhoz, hogy vissza tudja állítani a frissítést, létre kell hoznia egy átmeneti környezetet az adatbázis teljesen szinkronizált példányával. Az alábbi lépéseket követve hozhat létre átmeneti környezetet a frissítéshez:

1. Hozzon létre egy másodlagos adatbázist ugyanabban az Azure-régióban. Figyelje meg a másodlagost, és ellenőrizze, hogy a kiindulási folyamat befejeződött-e (1).
2. Hozzon létre egy új környezetet a webalkalmazáshoz, és hívja meg az "átmeneti" állapotot. Azure DNS az URL-címmel `contoso-staging.azurewebsites.net` (2) lesz regisztrálva.

> [!NOTE]
> Ezek az előkészítési lépések nem érintik az éles környezetet, ami teljes hozzáférésű módban működhet.

![A diagram a Felhőbeli vész-helyreállítás SQL Database geo-replikációs konfigurációját jeleníti meg.](./media/manage-application-rolling-upgrade/option1-1.png)

Az előkészítési lépések elvégzése után az alkalmazás készen áll a tényleges frissítésre. A következő diagram a frissítési folyamat lépéseit szemlélteti:

1. Állítsa az elsődleges adatbázist írásvédett módra (3). Ez a mód garantálja, hogy a webalkalmazás (v1) éles környezete csak olvasható marad a frissítés során, így megelőzve az adateltérést a v1 és v2 verziójú adatbázis-példányok között.
2. Válassza le a másodlagos adatbázist a tervezett megszakítási mód használatával (4). Ez a művelet az elsődleges adatbázis teljesen szinkronizált, független másolatát hozza létre. Ez az adatbázis frissülni fog.
3. Állítsa a másodlagos adatbázist írási-olvasási módba, és futtassa a frissítési parancsfájlt (5).

![A diagram megjeleníti SQL Database geo-replikációs konfigurációját a Felhőbeli vész-helyreállításhoz, amely a frissítési parancsfájlt futtatja.](./media/manage-application-rolling-upgrade/option1-2.png)

Ha a frissítés sikeresen befejeződött, most már készen áll arra, hogy átváltsa a felhasználókat az alkalmazás frissített példányára, amely éles környezetvé válik. A váltás néhány további lépést is magában foglal, ahogy azt a következő ábra szemlélteti:

1. Egy swap-művelet aktiválása a webalkalmazás üzemi és átmeneti környezetei között (6). A művelet bekapcsolja a két környezet URL-címeit. Most a webhely `contoso.azurewebsites.net` v2-verziójára és az adatbázisra (éles környezet) mutat. 
2. Ha már nincs szüksége a v1-es verzióra, amely átmeneti másolatot vált a swap után, akkor leszerelheti az átmeneti környezetet (7).

![SQL Database geo-replikációs konfiguráció a Felhőbeli vész-helyreállításhoz.](./media/manage-application-rolling-upgrade/option1-3.png)

Ha a frissítési folyamat sikertelen (például a Frissítési parancsfájl hibája miatt), vegye figyelembe, hogy az átmeneti környezet sérül. Az alkalmazás frissítés előtti állapotba való visszaállításához állítsa vissza az alkalmazást az éles környezetben teljes hozzáférésre. A következő ábrán a visszaállítás lépései láthatók:

1. Az adatbázis másolásának beállítása írható és olvasható módra (8). Ez a művelet visszaállítja az éles példány teljes v1 funkcióját.
2. Hajtsa végre a kiváltó okok elemzését, és szerelje le az átmeneti környezetet (9).

Ezen a ponton az alkalmazás teljesen működőképes, és megismételheti a frissítés lépéseit.

> [!NOTE]
> A visszaállítás nem igényli a DNS-módosításokat, mert még nem hajtott végre swap-műveletet.

![Ábrán látható SQL Database geo-replikálási konfiguráció a Felhőbeli vész-helyreállításhoz az átmeneti környezet leszerelt állapotában.](./media/manage-application-rolling-upgrade/option1-4.png)

Ennek a lehetőségnek a legfőbb előnye, hogy egyetlen régióban frissítheti az alkalmazásokat, ha az egyszerű lépések egy halmazát követi. A frissítés dolláros díja viszonylag alacsony. 

A fő kompromisszum az, hogy ha végzetes hiba történik a frissítés során, a frissítés előtti állapotba történő helyreállítás az alkalmazás egy másik régióban való újbóli üzembe helyezését, valamint az adatbázis biztonsági másolatból való visszaállítását jelenti a Geo-visszaállítás használatával. Ez a folyamat jelentős állásidőt eredményez.

## <a name="upgrade-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Az adatbázis-geo-replikációra támaszkodó alkalmazások frissítése vész-helyreállításra

Ha az alkalmazás aktív geo-replikálási vagy automatikus feladatátvételi csoportokat használ az üzletmenet folytonossága érdekében, az üzembe helyezése legalább két különböző régióban folyamatban van. Aktív, elsődleges adatbázis található egy elsődleges régióban és egy írásvédett, másodlagos adatbázis egy biztonsági mentési régióban. A cikk elején említett tényezőkkel együtt a frissítési folyamatnak a következőket is biztosítania kell:

* Az alkalmazás a frissítési folyamat során mindig védelmet biztosít a katasztrofális hibáktól.
* Az alkalmazás geo-redundáns összetevőit az aktív összetevőkkel párhuzamosan frissíti a rendszer.

Ahhoz, hogy ezeket a célokat a Web Apps környezetek használata mellett is elérhetővé tegye, az Azure Traffic Manager előnyeit egy aktív végponttal és egy biztonsági mentési végponttal rendelkező feladatátvételi profillal használhatja. A következő diagram a frissítési folyamat előtt mutatja be az operatív környezetet. A webhelyek `contoso-1.azurewebsites.net` és `contoso-dr.azurewebsites.net` az alkalmazás éles környezetét képviselik teljes földrajzi redundanciával. Az éles környezet a következő összetevőket tartalmazza:

* A webalkalmazás éles környezete `contoso-1.azurewebsites.net` az elsődleges régióban (1)
* Az elsődleges régió elsődleges adatbázisa (2)
* A webalkalmazás készenléti példánya a biztonsági mentési régióban (3)
* A földrajzi replikált másodlagos adatbázis a biztonsági mentési régióban (4)
* Egy Traffic Manager Performance profil egy nevű online végponttal `contoso-1.azurewebsites.net` és egy kapcsolat nélküli végpont `contoso-dr.azurewebsites.net`

A frissítés visszavonásához létre kell hoznia egy átmeneti környezetet az alkalmazás teljesen szinkronizált példányával. Mivel meg kell győződnie arról, hogy az alkalmazás gyorsan helyreállítható, ha a frissítési folyamat során végzetes hiba történik, az átmeneti környezetnek is földrajzilag redundánsnak kell lennie. A következő lépések szükségesek egy átmeneti környezet létrehozásához a frissítéshez:

1. Üzembe helyezheti a webalkalmazás átmeneti környezetét az elsődleges régióban (6).
2. Hozzon létre egy másodlagos adatbázist az elsődleges Azure-régióban (7). Konfigurálja a webalkalmazás átmeneti környezetét a kapcsolódáshoz. 
3. Hozzon létre egy másik geo-redundáns másodlagos adatbázist a biztonsági mentési régióban a másodlagos adatbázis elsődleges régióban való replikálásával. (Ezt a metódust *láncolt geo-replikációnak* nevezzük.) (8).
4. Helyezzen üzembe a webalkalmazás-példány átmeneti környezetét a biztonsági mentési régióban (9), és konfigurálja úgy, hogy a (8) időpontban létrehozott geo-redundáns másodlagos adatbázishoz kapcsolódjon.

> [!NOTE]
> Ezek az előkészítési lépések nem érintik az alkalmazást az éles környezetben. Az alkalmazás teljes funkcionalitásban marad az írási és olvasási módban.

![A diagram megjeleníti SQL Database geo-replikálási konfigurációját a Felhőbeli vész-helyreállításhoz az alkalmazás teljesen szinkronizált példányával.](./media/manage-application-rolling-upgrade/option2-1.png)

Az előkészítési lépések elvégzése után az átmeneti környezet készen áll a frissítésre. A következő diagram ezeket a frissítési lépéseket szemlélteti:

1. Állítsa az elsődleges adatbázist éles környezetben a csak olvasható módra (10). Ez a mód garantálja, hogy az éles adatbázis (v1) nem változik a frissítés során, így megakadályozza a v1 és v2 verziójú adatbázis-példányok közötti adateltérést.

```sql
-- Set the production database to read-only mode
ALTER DATABASE <Prod_DB>
SET (ALLOW_CONNECTIONS = NO)
```

2. Zárja le a Geo-replikációt a másodlagos (11) kapcsolat leválasztásával. Ez a művelet létrehoz egy független, de teljesen szinkronizált példányt az éles adatbázisból. Ez az adatbázis frissülni fog. A következő példa Transact-SQL-t használ, de a [PowerShell](/powershell/module/az.sql/remove-azsqldatabasesecondary) is elérhető. 

```sql
-- Disconnect the secondary, terminating geo-replication
ALTER DATABASE <Prod_DB>
REMOVE SECONDARY ON SERVER <Partner-Server>
```

3. Futtassa a frissítési parancsfájlt a `contoso-1-staging.azurewebsites.net` , `contoso-dr-staging.azurewebsites.net` a és az átmeneti elsődleges adatbázison (12). Az adatbázis módosításait a rendszer automatikusan replikálja a másodlagos átmeneti állapotba.

![A diagram megjeleníti SQL Database geo-replikálási konfigurációját a Felhőbeli vész-helyreállításhoz az adatbázis-módosításokkal, amelyek átmeneti állapotba kerülnek.](./media/manage-application-rolling-upgrade/option2-2.png)

Ha a frissítés sikeresen befejeződött, most már készen áll arra, hogy a felhasználókat az alkalmazás v2 verziójára váltsa. A következő diagram az alábbi lépéseket szemlélteti:

1. Aktiváljon egy swap-műveletet a webalkalmazás éles és átmeneti környezete között az elsődleges régióban (13) és a biztonsági mentési régióban (14). Az alkalmazás v2-je mostantól éles környezetbe kerül, és a biztonsági mentési régióban redundáns másolattal rendelkezik.
2. Ha már nincs szüksége a v1 alkalmazásra (15 és 16), leszerelheti az átmeneti környezetet.

![A diagramon SQL Database földrajzi replikálási konfiguráció látható a Felhőbeli vész-helyreállításhoz az átmeneti környezet választható leszerelésével.](./media/manage-application-rolling-upgrade/option2-3.png)

Ha a frissítési folyamat sikertelen (például a Frissítési parancsfájl hibája miatt), vegye figyelembe, hogy az átmeneti környezet inkonzisztens állapotban van. Az alkalmazás frissítés előtti állapotba való visszaállításához térjen vissza az alkalmazás v1-es verziójára az éles környezetben. A szükséges lépések a következő diagramon láthatók:

1. Állítsa az elsődleges adatbázis-másolatot az éles környezetben az írható-olvasható módba (17). Ez a művelet visszaállítja a teljes v1-es funkciót az éles környezetben.
2. Hajtsa végre a kiváltó okok elemzését és javítását, vagy távolítsa el az átmeneti környezetet (18 és 19).

Ezen a ponton az alkalmazás teljesen működőképes, és megismételheti a frissítés lépéseit.

> [!NOTE]
> A visszaállítás nem igényli a DNS-módosításokat, mert nem hajtott végre swap-műveletet.

![A diagram megjeleníti SQL Database geo-replikációs konfigurációját a Felhőbeli vész-helyreállításhoz a frissítési folyamat visszavonásával.](./media/manage-application-rolling-upgrade/option2-4.png)

Ennek a lehetőségnek a legfőbb előnye, hogy az alkalmazást és annak geo-redundáns másolatát párhuzamosan is frissítheti anélkül, hogy az üzletmenet folytonosságát veszélyeztetné a frissítés során.

A fő kompromisszum az, hogy minden alkalmazás-összetevőnél kettős redundancia szükséges, ezért magasabb dollár-költségekkel jár. Emellett bonyolultabb munkafolyamatot is magában foglal.

## <a name="summary"></a>Összefoglalás

A cikkben leírt két frissítési módszer különbözik az összetettség és a dollár díja között, de mindkettőre összpontosít, hogy a felhasználó mennyi ideig korlátozódik a csak olvasási műveletekre. Ezt az időt közvetlenül a Frissítési parancsfájl időtartama határozza meg. Ez nem függ az adatbázis méretétől, a választott szolgáltatási szinttől, a webhely konfigurációjától vagy más olyan tényezőktől, amelyeket nem tud egyszerűen szabályozni. Az előkészítési lépések elvesznek a frissítés lépéseitől, és nem befolyásolják az éles alkalmazást. A Frissítési parancsfájl hatékonysága kulcsfontosságú tényező, amely meghatározza a felhasználói élményt a frissítések során. Így a legjobb megoldás az, hogy a lehető leghatékonyabban fejlessze a frissítési szkriptet.

## <a name="next-steps"></a>Következő lépések

* Az üzletmenet folytonosságának áttekintése és forgatókönyvei: az [üzletmenet folytonosságának áttekintése](business-continuity-high-availability-disaster-recover-hadr-overview.md).
* Az aktív geo-replikálás Azure SQL Databaseával kapcsolatos további információkért lásd: [olvasható másodlagos adatbázisok létrehozása aktív geo-replikáció használatával](active-geo-replication-overview.md).
* Az automatikus feladatátvételi csoportok Azure SQL Databaseáról további információt az [automatikus feladatátvételi csoportok használata a több adatbázis átlátható és koordinált feladatátvételének engedélyezéséhez](auto-failover-group-overview.md)című témakörben talál.
* A Azure App Service átmeneti környezetének megismeréséhez tekintse meg az [átmeneti környezetek beállítása a Azure app Service-ben](../../app-service/deploy-staging-slots.md)című témakört.
* Az Azure Traffic Manager profiljaival kapcsolatos további információkért lásd: [azure Traffic Manager-profil kezelése](../../traffic-manager/traffic-manager-manage-profiles.md).
