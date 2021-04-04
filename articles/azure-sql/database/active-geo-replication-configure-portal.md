---
title: 'Oktatóanyag: Geo-replikálás & feladatátvétel a portálon'
description: Konfigurálja a Geo-replikációt egy adatbázishoz a Azure Portal és a feladatátvétel kezdeményezése segítségével.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 02/13/2019
ms.openlocfilehash: 71c73fec4f559b34b097556243617636acd77480
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92673265"
---
# <a name="tutorial-configure-active-geo-replication-and-failover-in-the-azure-portal-azure-sql-database"></a>Oktatóanyag: az aktív földrajzi replikálás és feladatátvétel konfigurálása a Azure Portalban (Azure SQL Database)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ebből a cikkből megtudhatja, hogyan konfigurálhatja az [aktív földrajzi replikálást Azure SQL Database](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities) a [Azure Portal](https://portal.azure.com) használatával és a feladatátvétel elindításához.

Az automatikus feladatátvételi csoportok használatával kapcsolatos ajánlott eljárásokért tekintse meg az [Azure SQL felügyelt példányának](auto-failover-group-overview.md#best-practices-for-sql-managed-instance)Azure SQL Database és ajánlott eljárásaival kapcsolatos ajánlott [eljárásokat](auto-failover-group-overview.md#best-practices-for-sql-database) . 



## <a name="prerequisites"></a>Előfeltételek

Az aktív geo-replikáció a Azure Portal használatával történő konfigurálásához a következő erőforrásra van szükség:

* Egy adatbázis a Azure SQL Databaseban: az elsődleges adatbázis, amelyet egy másik földrajzi régióba kíván replikálni.

> [!Note]
> Azure Portal használatakor a rendszer csak egy másodlagos adatbázist hozhat létre ugyanazon az előfizetésen belül, mint az elsődleges. Ha egy másodlagos adatbázisnak másik előfizetéshez kell tartoznia, használja az [adatbázis létrehozása REST API](/rest/api/sql/databases/createorupdate) vagy az [Alter Database Transact-SQL API-](/sql/t-sql/statements/alter-database-transact-sql)t.

## <a name="add-a-secondary-database"></a>Másodlagos adatbázis hozzáadása

A következő lépések egy új másodlagos adatbázist hoznak létre egy földrajzi replikálási partnerségben.  

Másodlagos adatbázis hozzáadásához az előfizetés tulajdonosának vagy társtulajdonosa kell lennie.

A másodlagos adatbázis neve megegyezik az elsődleges adatbázis nevével, és alapértelmezés szerint ugyanazt a szolgáltatási szintet és számítási méretet adja meg. A másodlagos adatbázis lehet önálló adatbázis vagy készletezett adatbázis is. További információ: [DTU-alapú vásárlási modell](service-tiers-dtu.md) és [virtuális mag-alapú vásárlási modell](service-tiers-vcore.md).
A másodlagos létrehozása és összevetése után az adatok megkezdik az elsődleges adatbázisból az új másodlagos adatbázisba való replikálást.

> [!NOTE]
> Ha a partner-adatbázis már létezik (például egy korábbi földrajzi replikálási kapcsolat megszakítása miatt), a parancs sikertelen lesz.

1. A [Azure Portal](https://portal.azure.com)keresse meg a Geo-replikációhoz beállítani kívánt adatbázist.
2. A SQL Database lapon válassza a **geo-replikálás** lehetőséget, majd válassza ki a régiót a másodlagos adatbázis létrehozásához. Bármelyik régiót kiválaszthatja, amely nem az elsődleges adatbázist üzemeltető régió, hanem a [párosított régiót](../../best-practices-availability-paired-regions.md)is ajánljuk.

    ![Aktív georeplikáció konfigurálása](./media/active-geo-replication-configure-portal/configure-geo-replication.png)
3. Válassza ki vagy konfigurálja a másodlagos adatbázis kiszolgálóját és díjszabási szintjét.

    ![másodlagos űrlap létrehozása](./media/active-geo-replication-configure-portal/create-secondary.png)
4. Lehetősége van másodlagos adatbázist is felvenni egy rugalmas készletbe. A másodlagos adatbázis készletben való létrehozásához kattintson a **rugalmas készlet** elemre, és jelöljön ki egy készletet a célkiszolgálón. Egy készletnek már léteznie kell a célkiszolgálón. Ez a munkafolyamat nem hoz létre készletet.
5. A másodlagos hozzáadásához kattintson a **Létrehozás** gombra.
6. A rendszer létrehozza a másodlagos adatbázist, és megkezdi az előkészítési folyamatot.

    ![formátumú másodlagos zónák Térkép](./media/active-geo-replication-configure-portal/seeding0.png)
7. A kiindulási folyamat befejezésekor a másodlagos adatbázis megjeleníti annak állapotát.

    ![Előkészítés befejezve](./media/active-geo-replication-configure-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Feladatátvétel kezdeményezése

A másodlagos adatbázis átváltható elsődlegesként.  

1. A [Azure Portal](https://portal.azure.com)tallózással keresse meg az elsődleges adatbázist a Geo-replikálási partnerségben.
2. A SQL Database panelen válassza a **minden beállítás**  >  **földrajzi replikálás** lehetőséget.
3. A **formátumú másodlagos zónák** listában válassza ki azt az adatbázist, amelynek az új elsődlegesnek kell lennie, majd kattintson a **kényszerített feladatátvétel** elemre.

    ![feladatátvétel](./media/active-geo-replication-configure-portal/secondaries.png)
4. A feladatátvétel elindításához kattintson az **Igen** gombra.

A parancs azonnal átváltja a másodlagos adatbázist az elsődleges szerepkörbe. Ez a folyamat általában legalább 30 másodpercen belül elvégezhető.

Van egy rövid időszak, amelyben mindkét adatbázis nem érhető el (0 és 25 másodperc között), amíg a szerepkörök át nem állnak. Ha az elsődleges adatbázis több másodlagos adatbázissal rendelkezik, a parancs automatikusan újrakonfigurálja a többi formátumú másodlagos zónák az új elsődlegeshez való kapcsolódáshoz. A teljes műveletnek egy percnél rövidebbnek kell lennie, hogy a szokásos körülmények között befejeződjön.

> [!NOTE]
> Ez a parancs leállás esetén az adatbázis gyors helyreállítására szolgál. Az adatszinkronizálás (kényszerített feladatátvétel) nélkül indítja el a feladatátvételt.  Ha az elsődleges hálózat online állapotú, és tranzakciókat véglegesít, ha a parancs kiadása valamilyen adatvesztést eredményezhet.

## <a name="remove-secondary-database"></a>Másodlagos adatbázis eltávolítása

Ez a művelet véglegesen leállítja a replikálást a másodlagos adatbázisba, és a másodlagos szerepkört egy normál írási-olvasási adatbázisra módosítja. Ha a másodlagos adatbázishoz való kapcsolódás megszakad, a parancs sikeres lesz, de a másodlagos nem írható-olvasható, amíg a kapcsolat vissza nem áll.  

1. A [Azure Portal](https://portal.azure.com)tallózással keresse meg az elsődleges adatbázist a Geo-replikálási partnerségben.
2. Az SQL Database lapon válassza a **geo-replikáció** lehetőséget.
3. A **formátumú másodlagos zónák** listából válassza ki azt az adatbázist, amelyet el szeretne távolítani a földrajzi replikálási partnerségből.
4. Kattintson a **replikálás leállítása** elemre.

    ![Másodlagos eltávolítása](./media/active-geo-replication-configure-portal/remove-secondary.png)
5. Megnyílik egy megerősítő ablak. Az **Igen** gombra kattintva távolítsa el az adatbázist a földrajzi replikálási partnerségből. (Állítsa azt egy írható-olvasható adatbázisba, amely nem része egyetlen replikációnak sem.)

## <a name="next-steps"></a>Következő lépések

* További információ az aktív földrajzi replikálásról: [Active geo-Replication](active-geo-replication-overview.md).
* További információ az automatikus feladatátvételi csoportokról: [automatikus feladatátvételi csoportok](auto-failover-group-overview.md)
* Az üzletmenet folytonosságának áttekintése és forgatókönyvei: az [üzletmenet folytonosságának áttekintése](business-continuity-high-availability-disaster-recover-hadr-overview.md).