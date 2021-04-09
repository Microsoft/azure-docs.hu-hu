---
title: Automatikus hangolás engedélyezése
description: A Azure Portal használatával egyszerűen engedélyezheti az adatbázis automatikus finomhangolását.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 03/03/2021
ms.openlocfilehash: 1362d8c1f15b64b9d76b28fd354cdae8919504b0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558278"
---
# <a name="enable-automatic-tuning-in-the-azure-portal-to-monitor-queries-and-improve-workload-performance"></a>A Azure Portal automatikus hangolásának engedélyezése a lekérdezések figyeléséhez és a munkaterhelés teljesítményének növeléséhez
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A Azure SQL Database automatikusan kezeli azokat az adatszolgáltatásokat, amelyek folyamatosan figyelik a lekérdezéseket, és azonosítják a munkaterhelés teljesítményének növelése érdekében végrehajtható műveleteket. Áttekintheti az ajánlásokat, és manuálisan alkalmazhatja őket, vagy engedélyezheti Azure SQL Database a javítási műveletek automatikus alkalmazását – ezt az **Automatikus hangolási módot** nevezzük.

Az automatikus hangolás a kiszolgálón vagy az adatbázis szintjén engedélyezhető a következő módon:

- A [Azure Portal](automatic-tuning-enable.md#azure-portal)
- [REST API](automatic-tuning-enable.md#rest-api) hívások
- [T-SQL-](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true) parancsok

> [!NOTE]
> Az Azure SQL felügyelt példányai esetében a FORCE_LAST_GOOD_PLAN támogatott beállítás csak a [T-SQL-](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) en keresztül konfigurálható. A jelen cikkben ismertetett Azure Portal-alapú konfigurációs és automatikus index-hangolási beállítások nem vonatkoznak az Azure SQL felügyelt példányaira.

> [!NOTE]
> Az Automatikus hangolási beállítások az ARM (Azure Resource Manager) sablonnal való konfigurálása jelenleg nem támogatott.

## <a name="enable-automatic-tuning-on-server"></a>Automatikus hangolás engedélyezése a kiszolgálón

A kiszolgáló szintjén dönthet úgy, hogy az "Azure Defaults" értékről örökli az Automatikus hangolási konfigurációt, vagy nem örökli a konfigurációt. Az Azure alapértelmezett értékei FORCE_LAST_GOOD_PLAN engedélyezve vannak, CREATE_INDEX le van tiltva, és a DROP_INDEX le van tiltva.

> [!IMPORTANT]
> Márciustól a 2020 új Azure alapértékek az automatikus hangoláshoz a következők:
>
> - FORCE_LAST_GOOD_PLAN = engedélyezve, CREATE_INDEX = letiltva és DROP_INDEX = letiltva.
> - Az Automatikus hangolási beállításokat nem tartalmazó meglévő kiszolgálók automatikusan konfigurálva vannak az Azure alapértelmezett beállításainak ÖRÖKLÉSére. Ez minden olyan ügyfélre vonatkozik, amely jelenleg nem definiált állapotban van az Automatikus hangolási kiszolgáló beállításaival.
> - A létrehozott új kiszolgálók automatikusan úgy lesznek konfigurálva, hogy ÖRÖKLIk az Azure alapértelmezett beállításait (a korábbitól eltérően, ha az Automatikus hangolási konfiguráció nem definiált állapotú az új kiszolgáló létrehozása után).

### <a name="azure-portal"></a>Azure Portal

Ha engedélyezni szeretné az automatikus finomhangolást Azure SQL Database- [kiszolgálón](logical-servers.md) , keresse meg a kiszolgálót a Azure Portal, majd válassza az **automatikus hangolás** lehetőséget a menüben.

![A képernyőképen a Azure Portal automatikus finomhangolása látható, ahol a kiszolgálók beállításait is alkalmazhatja.](./media/automatic-tuning-enable/server.png)

> [!NOTE]
> Vegye figyelembe, hogy a **DROP_INDEX** lehetőség jelenleg nem kompatibilis a partíciós váltást és az indexelési tippeket használó alkalmazásokkal, és ezeket az eseteket nem szabad engedélyezni. A nem használt indexek nem támogatottak a prémium és üzletileg kritikus szolgáltatási szinteken.

Válassza ki az engedélyezni kívánt Automatikus hangolási beállításokat, és kattintson az **alkalmaz** gombra.

A kiszolgálón az Automatikus hangolási beállítások a kiszolgálón lévő összes adatbázisra vonatkoznak. Alapértelmezés szerint az összes adatbázis örökli a konfigurációt a szülő kiszolgálóról, de ez felülbírálható és megadható minden egyes adatbázishoz.

### <a name="rest-api"></a>REST API

Ha többet szeretne megtudni a **kiszolgálók** automatikus finomhangolásának engedélyezéséről REST API használatáról, tekintse meg a [kiszolgáló automatikus finomhangolásának frissítése és a http-METÓDUSok beolvasása](/rest/api/sql/serverautomatictuning)című témakört.

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Automatikus hangolás engedélyezése egy adott adatbázison

Azure SQL Database lehetővé teszi az egyes adatbázisok Automatikus hangolási konfigurációjának egyedi megadását. Az adatbázis szintjén dönthet úgy, hogy örökli az Automatikus hangolási konfigurációt a szülő kiszolgálóról, az "Azure Defaults" vagy a konfiguráció öröklése nélkül. Az Azure alapértelmezett beállításai engedélyezve FORCE_LAST_GOOD_PLAN, CREATE_INDEX le van tiltva, és a DROP_INDEX le van tiltva.

> [!TIP]
> Az általános javaslat az Automatikus hangolási konfiguráció kezelése a **kiszolgáló szintjén** , így ugyanazokat a konfigurációs beállításokat lehet minden adatbázison automatikusan alkalmazni. Az automatikus hangolást csak akkor konfigurálja egy adott adatbázison, ha az adatbázisnak eltérő beállításokkal kell rendelkeznie, mint mások, mint a beállítások öröklése ugyanarról a kiszolgálóról.

### <a name="azure-portal"></a>Azure Portal

**Egyetlen adatbázis** automatikus finomhangolásának engedélyezéséhez navigáljon a Azure Portal adatbázisához, és válassza az **automatikus hangolás** lehetőséget.

Az egyes adatbázisokhoz külön konfigurálható az automatikus finomhangolási beállítások. Manuálisan is konfigurálhat egyéni Automatikus hangolási beállítást, vagy megadhatja, hogy egy beállítás örökli a beállításait a-kiszolgálótól.

![A képernyőképen a Azure Portal automatikus finomhangolása látható, ahol egyetlen adatbázishoz is alkalmazhat beállításokat.](./media/automatic-tuning-enable/database.png)

Vegye figyelembe, hogy az DROP_INDEX lehetőség jelenleg nem kompatibilis a partíciós váltást és az indexeket használó alkalmazásokkal, és ezeket az eseteket nem szabad engedélyezni.

Miután kiválasztotta a kívánt konfigurációt, kattintson az **alkalmaz** gombra.

### <a name="rest-api"></a>Rest API

Ha szeretne többet megtudni arról, hogyan használhatja az automatikus finomhangolást egyetlen adatbázison REST API, tekintse meg a [Azure SQL Database Automatikus hangolási frissítés és a http-metódusok BEolvasása](/rest/api/sql/databaseautomatictuning)című témakört.

### <a name="t-sql"></a>T-SQL

Ha engedélyezni szeretné az automatikus finomhangolást egyetlen adatbázison a T-SQL használatával, kapcsolódjon az adatbázishoz, és hajtsa végre a következő lekérdezést:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

Az automatikus Finomhangolás automatikus beállítása az Azure alapértelmezett beállításait fogja alkalmazni. Az öröklés beállításakor a rendszer az Automatikus hangolási konfigurációt örökli a fölérendelt kiszolgálótól. Az egyéni lehetőséget választva manuálisan kell konfigurálnia az automatikus hangolást.

Az egyes Automatikus hangolási beállítások T-SQL-kapcsolaton keresztüli konfigurálásához kapcsolódjon az adatbázishoz, és hajtsa végre a lekérdezést, például:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = ON, DROP_INDEX = OFF)
```

Ha a be értékre állítja az egyes hangolási lehetőséget, az az adatbázis által örökölt beállításokat felülbírálja, és engedélyezi a hangolási beállítást. Ha kikapcsolja a beállítást, a rendszer felülbírálja az adatbázis által örökölt és a hangolási beállítást letiltó beállításokat is. Az Automatikus hangolási beállítás, amelynél az alapértelmezett érték meg van adva, a a kiszolgálói szintű beállításoktól örökli az Automatikus hangolási konfigurációt.  

> [!IMPORTANT]
> [Aktív földrajzi replikálás](auto-failover-group-overview.md)esetén az automatikus finomhangolást csak az elsődleges adatbázison kell konfigurálni. A automatikusan alkalmazott hangolási műveletek, például az index létrehozása vagy törlése automatikusan replikálódnak a csak olvasható másodlagosra. Ha a csak olvasási jogosultsággal rendelkező T-SQL-n keresztül szeretné engedélyezni az automatikus hangolást, a rendszer hibát okoz, mivel a csak olvasási jogosultsággal rendelkező másodlagos konfiguráció nem támogatott.
>

Ha többet szeretne megtudni a vmivel T-SQL-beállításokról az automatikus hangolás konfigurálásához, tekintse meg az [adatbázis-beállítási beállítások módosítása (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true)című témakört.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="automated-recommendation-management-is-disabled"></a>Az automatizált javaslatok kezelése le van tiltva

Ha az automatikus ajánlás-kezelési szolgáltatás le van tiltva, vagy egyszerűen le van tiltva a rendszeren, a leggyakoribb okok a következők:
- A lekérdezési tároló nincs engedélyezve, vagy
- A lekérdezési tároló egy adott adatbázis írásvédett módban van, vagy
- A lekérdezési tároló leállt, mert a lefoglalt tárolóhelyet használta.

A probléma kijavításához a következő lépéseket kell figyelembe venni:
- Törölje a lekérdezési tárolót, vagy módosítsa az adatmegőrzési időszakot az "Auto" értékre a T-SQL használatával. Lásd: a [lekérdezési tároló ajánlott megőrzési és rögzítési szabályzatának konfigurálása](./query-performance-insight-use.md#recommended-retention-and-capture-policy).
- Használja a SQL Server Management Studio (SSMS), és kövesse az alábbi lépéseket:
  - Kapcsolódás a Azure SQL Databasehoz
  - Kattintson a jobb gombbal az adatbázisra
  - Válassza a tulajdonságok lehetőséget, és kattintson a lekérdezés-áruház elemre.
  - A műveleti mód módosítása Read-Writere
  - Az áruház rögzítési módjának módosítása automatikusra
  - A méret alapú karbantartási mód módosítása automatikusra

### <a name="permissions"></a>Engedélyek

Mivel az automatikus hangolás egy Azure-szolgáltatás, a használatához az Azure beépített szerepköreit kell használnia. Az SQL-hitelesítés használata nem elegendő a funkciónak a Azure Portal való használatához.

Az automatikus hangolás használatához a felhasználónak való megadáshoz minimálisan szükséges engedély az Azure beépített [SQL Database közreműködő](../../role-based-access-control/built-in-roles.md#sql-db-contributor) szerepköre. Érdemes lehet magasabb jogosultsági szerepköröket is használni, például SQL Server közreműködőt, SQL felügyelt példány közreműködőjét, közreműködőt és tulajdonost.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>E-mail értesítések automatikus hangolásának konfigurálása

Ha automatizált e-mail-értesítéseket szeretne kapni az automatikus hangolással kapcsolatos javaslatokról, tekintse meg az [e-mail értesítések automatikus finomhangolása](automatic-tuning-email-notifications-configure.md) útmutatót.

## <a name="next-steps"></a>Következő lépések

- Az automatikus hangolással kapcsolatos további információkért olvassa el az automatikus finomhangolásról szóló [cikket](automatic-tuning-overview.md) , és azt, hogy miként segíthet a teljesítmény javításában.
- Tekintse meg a teljesítményre vonatkozó [javaslatokat](database-advisor-implement-performance-recommendations.md) a Azure SQL Database teljesítményével kapcsolatos javaslatok áttekintéséhez.
- A leggyakoribb lekérdezések teljesítményre gyakorolt hatásának megtekintéséhez tekintse meg a [lekérdezési teljesítmény](query-performance-insight-use.md) elemzését ismertető témakört.