---
title: Az SQL Data Sync beállítása
description: Ez az oktatóanyag bemutatja, hogyan állíthat be SQL-adatszinkronizálás az Azure-hoz
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/14/2019
ms.openlocfilehash: d6b5bab1c1b6c8db4821fdf84728eb66eb55b899
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98882229"
---
# <a name="tutorial-set-up-sql-data-sync-between-databases-in-azure-sql-database-and-sql-server"></a>Oktatóanyag: SQL-adatszinkronizálás beállítása Azure SQL Database és SQL Server adatbázisai között
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be a SQL-adatszinkronizálás egy olyan szinkronizálási csoport létrehozásával, amely Azure SQL Database és SQL Server példányokat is tartalmaz. A szinkronizálási csoport egyéni konfigurálva van, és szinkronizálja a beállított ütemtervet.

Az oktatóanyag feltételezi, hogy van legalább néhány korábbi tapasztalata SQL Database és SQL Server.

A SQL-adatszinkronizálás áttekintését lásd: az [adatszinkronizálás a Felhőbeli és a helyszíni adatbázisok között SQL-adatszinkronizálásokkal](sql-data-sync-data-sql-server-sql-database.md).

PowerShell-példák a SQL-adatszinkronizálás konfigurálásának módjáról: az [adatbázisok közötti szinkronizálás SQL Database](scripts/sql-data-sync-sync-data-between-sql-databases.md) vagy [adatbázisok között Azure SQL Database és SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

> [!IMPORTANT]
> A SQL-adatszinkronizálás jelenleg **nem** támogatja az Azure SQL felügyelt példányát.

## <a name="create-sync-group"></a>Szinkronizálási csoport létrehozása

1. Nyissa meg a [Azure Portalt](https://portal.azure.com) , és keresse meg az adatbázist SQL Databaseban. Keresse meg és válassza ki az **SQL-adatbázisokat**.

    ![Adatbázisok keresése, Microsoft Azure Portal](./media/sql-data-sync-sql-server-configure/search-for-sql-databases.png)

1. Válassza ki azt az adatbázist, amelyet központi adatbázisként kíván használni az adatszinkronizáláshoz.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/select-sql-database.png" alt-text = "Select from the database list, Microsoft Azure portal":::

    > [!NOTE]
    > A hub-adatbázis egy szinkronizálási topológia központi végpontja, amelyben a szinkronizálási csoport több adatbázis-végponttal rendelkezik. A szinkronizálási csoportban található végpontokkal rendelkező összes többi tag-adatbázis szinkronizálva van a központi adatbázissal.

1. A kiválasztott adatbázishoz tartozó **SQL-adatbázis** menüben válassza a **szinkronizálás más adatbázisokkal** lehetőséget.

    :::image type="content" source="./media/sql-data-sync-sql-server-configure/sync-to-other-databases.png" alt-text = "Sync to other databases, Microsoft Azure portal":::

1. A **más adatbázisok szinkronizálása** lapon válassza az **új szinkronizálási csoport** elemet. Megnyílik az **új szinkronizálási csoport** lap, amely **létrehoz egy szinkronizálási csoportot (1. lépés)**.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/new-sync-group-private-link.png" alt-text = "Set up new sync group with private link":::

   Az **adatszinkronizálási csoport létrehozása** lapon módosítsa a következő beállításokat:

   | Beállítás                        | Leírás |
   | ------------------------------ | ------------------------------------------------- |
   | **Szinkronizálási csoport neve** | Adja meg az új szinkronizálási csoport nevét. Ez a név nem azonos az adatbázis nevével. |
   | **Metaadat-adatbázis szinkronizálása** | Válassza az adatbázis létrehozása (javasolt) lehetőséget, vagy egy meglévő adatbázis használatát.<br/><br/>Ha az **új adatbázis** lehetőséget választja, válassza az **új adatbázis létrehozása lehetőséget.** Ezután a **SQL Database** lapon nevezze el és konfigurálja az új adatbázist, és kattintson **az OK gombra**.<br/><br/>Ha a **meglévő adatbázis használata** lehetőséget választja, válassza ki az adatbázist a listából. |
   | **Automatikus szinkronizálás** | Válassza **a** be vagy **ki** lehetőséget.<br/><br/>Ha **a be** lehetőséget választja, adjon meg egy számot, és válassza a **másodperc**, **perc**, **óra** vagy **nap** lehetőséget a **szinkronizálás gyakorisága** szakaszban.<br/> Az első szinkronizálás akkor kezdődik el, amikor a kiválasztott intervallum eltelik a konfiguráció mentésekor.|
   | **Ütközés feloldása** | Válassza a **hub Win** vagy a **tag Win** lehetőséget.<br/><br/>A **hub-Win** azt jelenti, hogy ütközések esetén a központi adatbázisban lévő adatai felülírják a tag adatbázisában található ütköző adatforrásokat.<br/><br/>A **tag Win** azt jelenti, hogy ütközések esetén a tag adatbázisában lévő adatai felülírják az ütköző adatforrásokat a központi adatbázisban. |
   | **Privát hivatkozás használata** | Válassza ki a szolgáltatás által felügyelt privát végpontot a szinkronizálási szolgáltatás és a hub-adatbázis közötti biztonságos kapcsolat létrehozásához. |

   > [!NOTE]
   > A Microsoft azt javasolja, hogy hozzon létre egy új, üres adatbázist a **szinkronizálási metaadat-adatbázisként** való használatra. Az adatok szinkronizálása táblákat hoz létre ebben az adatbázisban, és gyakori számítási feladatokat futtat. Ez az adatbázis a kiválasztott régióban és előfizetésben lévő összes szinkronizálási csoport **szinkronizálási metaadat-adatbázisa** . Az adatbázis vagy a neve nem módosítható a régió összes szinkronizálási csoportjának és szinkronizálási ügynökének eltávolítása nélkül.

   Kattintson az **OK gombra** , és várja meg a szinkronizálási csoport létrehozását és üzembe helyezését.
   
1. Ha a **privát hivatkozás használata** lehetőséget választotta, akkor az **új szinkronizálási csoport** lapon jóvá kell hagynia a magánhálózati végponti kapcsolatot. Az információs üzenetben található hivatkozásra kattintva megtekintheti a privát végponti kapcsolatok felületét, ahol jóváhagyhatja a kapcsolatot. 

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/approve-private-link.png" alt-text = "Approve private link":::

## <a name="add-sync-members"></a>Szinkronizálási Tagok hozzáadása

Az új szinkronizálási csoport létrehozása és telepítése után a **szinkronizálási Tagok hozzáadása (2. lépés)** kiemelve jelenik meg az **új szinkronizálási csoport** lapon.

A **hub-adatbázis** szakaszban adja meg azon kiszolgáló meglévő hitelesítő adatait, amelyen a központi adatbázis található. Ebben a szakaszban ne adjon meg *új* hitelesítő adatokat.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/steptwo.png" alt-text = "Enter existing credentials for the hub database server":::

### <a name="to-add-a-database-in-azure-sql-database"></a>Adatbázis hozzáadása Azure SQL Database

A **tag adatbázisa** szakaszban opcionálisan hozzáadhat egy adatbázist a Azure SQL Database a szinkronizálási csoporthoz a **Azure SQL Database hozzáadása** lehetőség kiválasztásával. Megnyílik a **Azure SQL Database konfigurálása** lap.
  
   :::image type="content" source="./media/sql-data-sync-sql-server-configure/step-two-configure.png" alt-text = "Add a database to the sync group":::
   
  A **Azure SQL Database konfigurálása** lapon módosítsa a következő beállításokat:

  | Beállítás                       | Leírás |
  | ----------------------------- | ------------------------------------------------- |
  | **Szinkronizálási tag neve** | Adja meg az új szinkronizálási tag nevét. Ez a név nem azonos az adatbázis nevével. |
  | **Előfizetés** | Válassza ki a társított Azure-előfizetést számlázási célokra. |
  | **Azure SQL Server** | Válassza ki a meglévő kiszolgálót. |
  | **Azure SQL Database** | Válassza ki a SQL Database meglévő adatbázisát. |
  | **Szinkronizálási irányok** | Válassza a **kétirányú szinkronizálás**, **a hub** vagy **a hub** lehetőséget. |
  | **Felhasználónév** és **jelszó** | Adja meg azon kiszolgáló meglévő hitelesítő adatait, amelyen a tag adatbázisa található. Ebben a szakaszban ne adjon meg *új* hitelesítő adatokat. |
  | **Privát hivatkozás használata** | Válassza ki a szolgáltatás által felügyelt privát végpontot a szinkronizálási szolgáltatás és a tag adatbázisa közötti biztonságos kapcsolat létrehozásához. |

  Kattintson az **OK gombra** , és várjon, amíg a rendszer létrehozza és telepíti az új szinkronizálási tagot.

<a name="add-on-prem"></a>

### <a name="to-add-a-sql-server-database"></a>SQL Server-adatbázis hozzáadása

A **tag adatbázisa** szakaszban opcionálisan hozzáadhat egy SQL Server adatbázist a szinkronizálási csoporthoz a helyszíni **adatbázis hozzáadása** lehetőség kiválasztásával. Megnyílik a helyszíni **Konfigurálás** lap, ahol a következő műveleteket végezheti el:

1. Válassza **a szinkronizálási ügynök átjárójának kiválasztása** lehetőséget. Megnyílik a **szinkronizálási ügynök kiválasztása** lap.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/steptwo-agent.png" alt-text = "Creating a sync agent":::

1. A **szinkronizálási ügynök kiválasztása** lapon válassza ki, hogy meglévő ügynököt kíván-e használni, vagy ügynököt szeretne létrehozni.

   Ha a **meglévő ügynökök** lehetőséget választja, válassza ki a listából a meglévő ügynököt.

   Ha az **új ügynök létrehozása** lehetőséget választja, tegye a következőket:

   1. Töltse le az adatszinkronizálási ügynököt a megadott hivatkozásról, és telepítse azt azon a számítógépen, ahol a SQL Server található. Az ügynököt közvetlenül az [Azure SQL-adatszinkronizálás agentből](https://www.microsoft.com/download/details.aspx?id=27693)is letöltheti.

      > [!IMPORTANT]
      > Meg kell nyitnia a 1433-as kimenő TCP-portot a tűzfalon, hogy az ügyfél kommunikáljon a kiszolgálóval.

   1. Adja meg az ügynök nevét.

   1. Válassza a **Létrehozás és létrehozás kulcs** elemet, és másolja az ügynök kulcsát a vágólapra.

   1. A **szinkronizálási ügynök kiválasztása** lap bezárásához kattintson **az OK gombra** .

1. A SQL Server számítógépen keresse meg és futtassa a Ügyfél-szinkronizációs ügynök alkalmazást.

   ![Az adatszinkronizálási ügyfél ügynökének alkalmazás](./media/sql-data-sync-sql-server-configure/datasync-preview-clientagent.png)

    1. A Szinkronizáló ügynök alkalmazásban válassza a **küldési ügynök kulcsa** lehetőséget. Megnyílik a **szinkronizálási metaadatok adatbázis-konfigurációja** párbeszédpanel.

    1. A **metaadatok szinkronizálása adatbázis konfigurációja** párbeszédpanelen illessze be a Azure Portalból másolt ügynök kulcsát. Adja meg azon kiszolgáló meglévő hitelesítő adatait is, amelyen a metaadat-adatbázis található. (Ha létrehozott egy metaadat-adatbázist, akkor ez az adatbázis a hub-adatbázissal megegyező kiszolgálón található.) Kattintson az **OK gombra** , és várjon, amíg a konfiguráció be nem fejeződik.

        ![Adja meg az ügynök kulcsát és a kiszolgáló hitelesítő adatait](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Ha tűzfal-hibaüzenetet kap, hozzon létre egy tűzfalszabály az Azure-ban, hogy engedélyezze a SQL Server számítógépről érkező forgalmat. A szabályt manuálisan is létrehozhatja a portálon vagy SQL Server Management Studioban (SSMS). A SSMS-ben kapcsolódjon az Azure-beli hub-adatbázishoz úgy, hogy a nevét <hub_database_name>. database.windows.net értékre írja be.

    1. A **regisztráció** gombra kattintva regisztrálhat egy SQL Server adatbázist az ügynökkel. Megnyílik a **SQL Server konfigurálása** párbeszédpanel.

        ![SQL Server-adatbázis hozzáadása és konfigurálása](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-adddb.png)

    1. A **SQL Server konfiguráció** párbeszédpanelen válassza a kapcsolódás SQL Server hitelesítéssel vagy a Windows-hitelesítéssel lehetőséget. Ha SQL Server hitelesítést választja, adja meg a meglévő hitelesítő adatokat. Adja meg a szinkronizálni kívánt adatbázis SQL Server nevét és nevét, majd válassza a **Kapcsolódás tesztelése** lehetőséget a beállítások teszteléséhez. Ezután kattintson a **Save (Mentés** ) gombra, és a regisztrált adatbázis megjelenik a listában.

        ![A SQL Server adatbázis regisztrálva van](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-dbadded.png)

    1. A Ügyfél-szinkronizációs ügynök alkalmazás bezárásához.

1. A portálon a helyszíni **Konfigurálás** lapon válassza **az adatbázis kiválasztása** lehetőséget.

1. Az **adatbázis kiválasztása** lap **szinkronizálási tag neve** mezőjében adja meg az új szinkronizálási tag nevét. Ez a név nem azonos az adatbázis nevével. Válassza ki az adatbázist a listából. A **szinkronizálási irányok** mezőben válassza a **kétirányú szinkronizálás**, **a hub** vagy **a hub** elemet.

    ![A helyszíni adatbázis kiválasztása](./media/sql-data-sync-sql-server-configure/datasync-preview-selectdb.png)

1. Az **adatbázis kiválasztása** lap bezárásához kattintson **az OK gombra** . Ezután kattintson az **OK** gombra a helyszíni **Konfigurálás** lap bezárásához és az új szinkronizálási tag létrehozásához és üzembe helyezéséhez. Végül kattintson az **OK** gombra a **szinkronizálási tagok kiválasztása** oldal bezárásához.

> [!NOTE]
> A SQL-adatszinkronizálás és a helyi ügynökhöz való kapcsolódáshoz adja hozzá a felhasználónevét a szerepkör- *DataSync_Executorhoz*. Az adatszinkronizálás létrehozza ezt a szerepkört a SQL Server példányon.

## <a name="configure-sync-group"></a>Szinkronizálási csoport konfigurálása

Az új szinkronizálási csoport tagjainak létrehozása és telepítése után a **szinkronizálási csoport konfigurálása (3. lépés)** az **új szinkronizálási csoport** lapon van kiemelve.

![3. lépés – beállítások](./media/sql-data-sync-sql-server-configure/stepthree.png)

1. A **táblák** lapon válasszon ki egy adatbázist a szinkronizálási csoport tagjainak listájáról, és válassza a **séma frissítése** lehetőséget.

1. A listából válassza ki a szinkronizálni kívánt táblákat. Alapértelmezés szerint minden oszlop ki van választva, ezért tiltsa le az olyan oszlopok jelölőnégyzetét, amelyeket nem szeretne szinkronizálni. Ügyeljen arra, hogy a kijelölt elsődleges kulcs oszlopot hagyja kiválasztva.

1. Kattintson a **Mentés** gombra.

1. Alapértelmezés szerint az adatbázisok nincsenek szinkronizálva az ütemezett vagy a manuális futtatásig. Manuális szinkronizálás futtatásához navigáljon az adatbázishoz SQL Database a Azure Portalban, válassza a **szinkronizálás más adatbázisokhoz** lehetőséget, majd válassza ki a szinkronizálási csoportot. Megnyílik az **adatszinkronizálás** lap. Válassza a **Szinkronizálás** elemet.

    ![Manuális szinkronizálás](./media/sql-data-sync-sql-server-configure/datasync-sync.png)

## <a name="faq"></a>GYIK

**SQL-adatszinkronizálás teljes táblákat létrehozni?**

Ha a szinkronizálási sématáblák hiányoznak a céladatbázisból, az SQL-adatszinkronizálás létrehozza azokat a kiválasztott oszlopokkal. Ez azonban nem eredményez teljes körű megbízhatósági sémát a következő okok miatt:

- Csak az Ön által kiválasztott oszlopok jönnek létre a céltáblában. A nem kiválasztott oszlopokat a program figyelmen kívül hagyja.
- Csak a kiválasztott oszlopindexek jönnek létre a céltáblában. A nem kiválasztott oszlopok indexeit a program figyelmen kívül hagyja.
- Az XML-típusú oszlopok indexei nem jönnek létre.
- Az ellenőrzési megkötések nem jönnek létre.
- A forrástábla eseményindítói nem jönnek létre.
- A nézetek és a tárolt eljárások nem jönnek létre.

E korlátozások miatt javasoljuk a következőket:

- Éles környezetekben saját maga hozza létre a teljes hűségű sémát.
- A szolgáltatással való kísérletezéskor használja az automatikus kiépítési funkciót.

**Miért látom a nem létrehozott táblákat?**

Az adatszinkronizálás további táblákat hoz létre az adatbázisban a változások nyomon követéséhez. Ne törölje ezeket, vagy az adatszinkronizálás leáll.

**Az adataim konvergens szinkronizálás után?**

Nem feltétlenül. A szinkronizálási csoportot egy központtal és három küllővel (A, B és C) szinkronizálja, ahol a szinkronizációk az A, A hub és a C közötti csomópont. Ha az "A" adatbázis módosítást végez a központ szinkronizálása *után* , a változás nem íródik a B vagy a C adatbázisba a következő szinkronizálási feladat előtt.

**Hogyan beolvasni a séma módosításait egy szinkronizálási csoportba?**

Végezze el és propagálja a séma összes módosítását manuálisan.

1. A séma módosításait a hub és az összes szinkronizálási tag között manuálisan replikálja.
1. Frissítse a szinkronizálási sémát.

Új táblák és oszlopok hozzáadásához:

Az új táblák és oszlopok nem érintik az aktuális szinkronizálást, és az adatszinkronizálás figyelmen kívül hagyja őket, amíg hozzá nem adja őket a szinkronizálási sémához. Új adatbázis-objektumok hozzáadásakor kövesse a következő sorozatot:

1. Vegyen fel új táblákat vagy oszlopokat a hubhoz és az összes szinkronizálási taghoz.
1. Új táblák vagy oszlopok hozzáadása a szinkronizálási sémához.
1. Megkezdheti az értékek beszúrását az új táblákba és oszlopokba.

Oszlop adattípusának módosítása:

Egy meglévő oszlop adattípusának módosításakor az adatok szinkronizálása továbbra is működik, amíg az új értékek illeszkednek a szinkronizálási sémában meghatározott eredeti adattípushoz. Ha például a forrás adatbázisban az **int** értékről a **bigint** értékre módosítja a típust, akkor az adatok szinkronizálása továbbra is működni fog, amíg nem szúr be túl nagy értéket az **int** adattípushoz. A módosítás befejezéséhez replikálja a séma módosításait a központba és az összes szinkronizálási tagra, majd frissítse a szinkronizálási sémát.

**Hogyan lehet exportálni és importálni egy adatbázist az adatszinkronizálással?**

Miután *. bacpac* fájlként exportált egy adatbázist, és importálja a fájlt egy adatbázis létrehozásához, a következő módon használhatja az adatszinkronizálást az új adatbázisban:

1. A [parancsfájl](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql)használatával törölje az adatszinkronizálási objektumokat és a további táblákat az új adatbázison. A szkript törli az összes szükséges adatszinkronizálási objektumot az adatbázisból.
1. Hozza létre újra a szinkronizálási csoportot az új adatbázissal. Ha már nincs szüksége a régi szinkronizálási csoportra, törölje azt.

**Hol találhatok információt az ügyfél-ügynökről?**

Az ügyfél-ügynökkel kapcsolatos gyakori kérdésekért lásd: [ügynök – gyakori](sql-data-sync-agent-overview.md#agent-faq)kérdések.

**Szükség van-e a privát hivatkozás manuális jóváhagyására, mielőtt elkezdem használni?**

Igen, manuálisan jóvá kell hagynia a szolgáltatás által felügyelt magánhálózati végpontot, a Azure Portal magánhálózati kapcsolatok lapján a szinkronizálási csoport központi telepítése során vagy a PowerShell használatával.

## <a name="next-steps"></a>Következő lépések

Gratulálunk! Olyan szinkronizálási csoportot hozott létre, amely SQL Database példányt és egy SQL Server adatbázist is tartalmaz.

További információ az SQL Data Syncről:

- [Az Azure SQL-adatszinkronizálás adatszinkronizálási ügynöke](sql-data-sync-agent-overview.md)
- [Ajánlott eljárások](sql-data-sync-best-practices.md) és [az Azure-SQL-adatszinkronizálás kapcsolatos problémák elhárítása](sql-data-sync-troubleshoot.md)
- [SQL-adatszinkronizálás figyelése Azure Monitor naplókkal](./monitor-tune-overview.md)
- [A szinkronizálási séma frissítése a Transact-SQL vagy a](sql-data-sync-update-sync-schema.md) [PowerShell használatával](scripts/update-sync-schema-in-sync-group.md)

További információ az SQL Database-ről:

- [Az SQL Database áttekintése](sql-database-paas-overview.md)
- [Az adatbázis életciklusának felügyelete](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
