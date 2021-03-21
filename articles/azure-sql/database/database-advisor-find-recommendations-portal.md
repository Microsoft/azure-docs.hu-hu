---
title: Teljesítménnyel kapcsolatos javaslatok alkalmazása
description: A Azure Portal használatával olyan teljesítménnyel kapcsolatos javaslatokat találhat, amelyek optimalizálni tudják az adatbázis teljesítményét.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 12/19/2018
ms.openlocfilehash: 748ac448ad8bf5c06e5be8b7a4a8b00a9b7af84b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96500886"
---
# <a name="find-and-apply-performance-recommendations"></a>Teljesítményre vonatkozó javaslatok keresése és alkalmazása
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A Azure Portal használatával olyan teljesítménnyel kapcsolatos javaslatokat találhat, amelyek Azure SQL Database vagy a számítási feladatban azonosított problémák megoldásában optimalizálják az adatbázis teljesítményét. A Azure Portal **teljesítmény-javaslat** lapja lehetővé teszi a leggyakoribb javaslatok megkeresését a lehetséges hatásuk alapján.

## <a name="viewing-recommendations"></a>Javaslatok megtekintése

A teljesítménnyel kapcsolatos javaslatok megtekintéséhez és alkalmazásához a megfelelő [Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC)](../../role-based-access-control/overview.md) engedélyek szükségesek az Azure-ban. Az **olvasó**, az **SQL-adatbázis közreműködői** engedélyekre van szükség a javaslatok megtekintéséhez és a **tulajdonoshoz**, az **SQL-adatbázis közreműködői** engedélyekre van szükség a műveletek végrehajtásához; indexek létrehozása vagy eldobása, indexek létrehozásának megszakítása.

A következő lépések végrehajtásával találhat teljesítménnyel kapcsolatos javaslatokat a Azure Portal:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Nyissa meg az **összes szolgáltatás**  >  **SQL-adatbázisát**, és válassza ki az adatbázist.
3. A kiválasztott adatbázishoz elérhető javaslatok megtekintéséhez navigáljon a **teljesítménnyel kapcsolatos javaslathoz** .

A teljesítménnyel kapcsolatos javaslatok az alábbi ábrán láthatóhoz hasonló táblázatban láthatók:

![A képernyőképen a műveletek és javaslatok leírását tartalmazó táblázatban láthatók a teljesítménnyel kapcsolatos javaslatok.](./media/database-advisor-find-recommendations-portal/recommendations.png)

A javaslatok a következő kategóriákba sorolhatók a teljesítményre gyakorolt lehetséges hatás szerint:

| Hatás | Description |
|:--- |:--- |
| Magas |A nagy hatású ajánlásoknak meg kell adniuk a legjelentősebb teljesítményre gyakorolt hatást. |
| Közepes |A közepes hatású javaslatoknak javítaniuk kell a teljesítményt, de lényegében nem. |
| Alacsony |Az alacsony hatású javaslatok jobb teljesítményt biztosítanak, mint a nélkül, de előfordulhat, hogy a fejlesztés nem jelentős. |

> [!NOTE]
> A Azure SQL Databasenak legalább egy napig figyelnie kell a tevékenységeket a javaslatok azonosítása érdekében. A Azure SQL Database könnyebben optimalizálható a konzisztens lekérdezési minták esetében, mint a véletlenszerű, pattanásos adattörések esetében. Ha a javaslatok jelenleg nem érhetők el, a **teljesítményre vonatkozó javaslat** oldalon egy üzenet ismerteti, hogy miért.

Megtekintheti a korábbi műveletek állapotát is. Válasszon ki egy javaslatot vagy egy állapotot a további információk megtekintéséhez.

Íme egy példa az "index létrehozása" javaslatra a Azure Portal.

![Index létrehozása](./media/database-advisor-find-recommendations-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Javaslatok alkalmazása

Azure SQL Database a következő három lehetőség bármelyikével teljes mértékben szabályozhatja a javaslatok használatát:

* Egyéni javaslatok egyenként történő alkalmazása.
* Engedélyezze az automatikus finomhangolást a javaslatok automatikus alkalmazásához.
* Ha manuálisan szeretné megvalósítani a javaslatot, futtassa az ajánlott T-SQL-szkriptet az adatbázison.

Válassza ki a kívánt javaslatot a részletek megtekintéséhez, majd kattintson a **parancsfájl megtekintése** elemre a javaslat létrehozásának pontos részleteinek áttekintéséhez.

Az adatbázis továbbra is online marad a javaslat alkalmazása közben. a teljesítményre vonatkozó javaslat vagy az automatikus hangolás soha nem végez offline állapotba egy adatbázist.

### <a name="apply-an-individual-recommendation"></a>Egyéni javaslat alkalmazása

Egyszerre csak egy javaslatot tekinthet meg és fogadhat el.

1. A **javaslatok** lapon válasszon ki egy javaslatot.
2. A **részletek** lapon kattintson az Apply ( **alkalmaz** ) gombra.

   ![Javaslat alkalmazása](./media/database-advisor-find-recommendations-portal/apply.png)

A kiválasztott javaslatok az adatbázisra vonatkoznak.

### <a name="removing-recommendations-from-the-list"></a>Javaslatok eltávolítása a listáról

Ha a javaslatok listája olyan elemeket tartalmaz, amelyeket el szeretne távolítani a listáról, elvetheti a javaslatot:

1. Válasszon ki egy javaslatot a **javaslatok** listájában a részletek megnyitásához.
2. A **részletek** lapon kattintson az **Elvetés** gombra.

Ha kívánja, hozzáadhat elvetett elemeket a **javaslatok** listájához:

1. A **javaslatok** lapon kattintson az **elvetettek megtekintése** elemre.
2. A részletek megtekintéséhez válasszon ki egy elvetett elemet a listából.
3. Ha szeretné, kattintson az **Elvetés visszavonása** gombra, és adja hozzá az indexet a **javaslatok** fő listájához.

> [!NOTE]
> Vegye figyelembe, hogy ha SQL Database [automatikus hangolás](automatic-tuning-overview.md) engedélyezve van, és ha manuálisan elvetett egy javaslatot a listáról, a rendszer soha nem alkalmazza automatikusan az adott javaslatot. A javaslatok figyelmen kívül hagyása egy praktikus módszer a felhasználók számára, hogy az automatikus hangolás engedélyezve legyen abban az esetben, ha egy konkrét javaslatot nem kell alkalmazni.
> Visszaállíthatja ezt a viselkedést úgy, hogy elvetett javaslatokat ad vissza a javaslatok listájához az Elvetés visszavonása lehetőség kiválasztásával.

### <a name="enable-automatic-tuning"></a>Automatikus hangolás engedélyezése

Az adatbázis beállítható úgy, hogy automatikusan végrehajtsa az ajánlásokat. Amint a javaslatok elérhetővé válnak, a rendszer automatikusan alkalmazza őket. A szolgáltatás által kezelt összes javaslathoz hasonlóan, ha a teljesítményre gyakorolt hatás negatív, a rendszer visszaállít egy javaslatot.

1. A **javaslatok** lapon kattintson az **automatizálás** lehetőségre:

   ![Advisor-beállítások](./media/database-advisor-find-recommendations-portal/settings.png)
2. Válassza ki az automatizálni kívánt műveleteket:

   ![Képernyőkép, amely bemutatja, hogy hol válassza ki az automatizálni kívánt műveleteket.](./media/database-advisor-find-recommendations-portal/server.png)

> [!NOTE]
> Vegye figyelembe, hogy **DROP_INDEX** lehetőség jelenleg nem kompatibilis a partíciós váltást és az indexelési tippeket használó alkalmazásokkal.

Miután kiválasztotta a kívánt konfigurációt, kattintson az Alkalmaz gombra.

### <a name="manually-apply-recommendations-through-t-sql"></a>Javaslatok manuális alkalmazása a T-SQL használatával

Válassza ki az egyik javaslatot, majd kattintson a **parancsfájl megjelenítése** lehetőségre. Futtassa ezt a szkriptet az adatbázison, hogy manuálisan alkalmazza a javaslatot.

*A manuálisan végrehajtott indexek figyelése és érvényesítése a szolgáltatás teljesítményére gyakorolt hatás miatt nem* történik meg, ezért azt javasoljuk, hogy a létrehozás után figyelje ezeket az indexeket, hogy ellenőrizze, hogy a teljesítmény-és módosítási és törlési műveletekhez szükség van-e. Az indexek létrehozásával kapcsolatos részletekért lásd: [create index (Transact-SQL)](/sql/t-sql/statements/create-index-transact-sql). Emellett a manuálisan alkalmazott javaslatok továbbra is aktívak maradnak, és megjelennek a 24-48 óra javaslatok listájában. mielőtt a rendszer automatikusan visszavonja őket. Ha hamarabb el szeretné távolítani a javaslatot, manuálisan elvégezheti azt.

### <a name="canceling-recommendations"></a>Javaslatok megszakítása

A **függőben lévő**, **érvényesítési** vagy **sikerességi** állapotú javaslatok megvonhatók. Nem lehet megszakítani a **végrehajtás** állapotával kapcsolatos ajánlásokat.

1. Válasszon ki egy javaslatot a **hangolási előzmények** területen a **javaslatok részletei** lap megnyitásához.
2. Kattintson a **Mégse** gombra a javaslat alkalmazási folyamatának megszakításához.

## <a name="monitoring-operations"></a>Figyelési műveletek

Előfordulhat, hogy egy javaslat alkalmazása nem történik meg azonnal. A portál a javaslatok állapotával kapcsolatos részleteket tartalmaz. Az alábbi lehetséges állapotok lehetnek az indexek:

| Állapot | Leírás |
|:--- |:--- |
| Függőben |A javaslat alkalmazása parancs beérkezett, és végrehajtásra van ütemezve. |
| Végrehajtása |A javaslat alkalmazása folyamatban van. |
| Érvényesítése |Az ajánlás alkalmazása sikeresen megtörtént, és a szolgáltatás az előnyöket méri. |
| Siker |Az ajánlás alkalmazása sikeresen megtörtént, és a rendszer kiértékelte az előnyöket. |
| Hiba |Hiba történt a javaslat alkalmazásának folyamata során. Ez lehet átmeneti probléma, vagy lehetséges, hogy a séma módosul a táblán, és a parancsfájl már nem érvényes. |
| Visszatérés |A javaslat alkalmazása megtörtént, de nem megfelelőnek minősül, és a rendszer automatikusan visszaállította. |
| Visszatért |Az ajánlás visszavonásra került. |

További információk megjelenítéséhez kattintson egy folyamaton belüli javaslatra a listából.

![A folyamaton belüli javaslatok listáját bemutató képernyőkép.](./media/database-advisor-find-recommendations-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Javaslat visszaállítása

Ha a teljesítményre vonatkozó javaslatokkal alkalmazta a javaslatot (ami azt jelenti, hogy nem futtatta manuálisan a T-SQL-szkriptet), akkor a automatikusan visszaváltja a változást, ha a negatív hatással van a teljesítményre. Ha bármilyen okból kifolyólag egyszerűen visszaállít egy javaslatot, a következőket teheti:

1. Válasszon ki egy sikeresen alkalmazott javaslatot a **hangolási előzmények** területen.
2. Kattintson a **Recover** elemre a **Javaslat részletei** lapon.

![Ajánlott indexek](./media/database-advisor-find-recommendations-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Az indexre vonatkozó javaslatok teljesítményének figyelése

A javaslatok sikeres implementálása után (jelenleg csak az indexelési műveletek és a parametrizálja-lekérdezések ajánlásai) a javaslat részletei lapon **a lekérdezési** teljesítmény elemzése lehetőségre kattintva megnyithatja a [lekérdezési teljesítménnyel](query-performance-insight-use.md) kapcsolatos elemzéseket, és megtekintheti a leggyakoribb lekérdezések teljesítményére gyakorolt hatást.

![Teljesítmény-hatás figyelése](./media/database-advisor-find-recommendations-portal/query-insights.png)

## <a name="summary"></a>Összefoglalás

Azure SQL Database az adatbázis teljesítményének javítására vonatkozó ajánlásokat tartalmaz. A T-SQL-parancsfájlok nyújtásával segítséget nyújt az adatbázis optimalizálásában, és végül a lekérdezési teljesítmény javításában.

## <a name="next-steps"></a>Következő lépések

Figyelje a javaslatokat, és alkalmazza őket a teljesítmény pontosítására. Az adatbázis-számítási feladatok dinamikusak, és folyamatosan változnak. Azure SQL Database folytatja az adatbázis teljesítményének növelésére szolgáló javaslatok figyelését és megadását.

* A Azure SQL Database automatikus hangolásával kapcsolatos további tudnivalókért tekintse meg az [automatikus hangolást](automatic-tuning-overview.md) ismertető témakört.
* Tekintse meg a teljesítményre vonatkozó [javaslatokat](database-advisor-implement-performance-recommendations.md) a Azure SQL Database teljesítményével kapcsolatos javaslatok áttekintéséhez.
* A leggyakoribb lekérdezések teljesítményre gyakorolt hatásának megtekintéséhez tekintse meg a [lekérdezési teljesítmény](query-performance-insight-use.md) elemzését ismertető témakört.

## <a name="additional-resources"></a>További források

* [Lekérdezéstár](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)
* [CREATE INDEX](/sql/t-sql/statements/create-index-transact-sql)
* [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md)