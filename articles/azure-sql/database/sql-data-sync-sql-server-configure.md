---
title: Az SQL Data Sync beállítása
description: Ez az oktatóanyag bemutatja, hogyan állíthat be SQL-adatszinkronizálás Azure-hoz
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
ms.openlocfilehash: 77073d21f982e82e567e517b7d9eca061cb91859
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812923"
---
# <a name="tutorial-set-up-sql-data-sync-between-databases-in-azure-sql-database-and-sql-server"></a>Oktatóanyag: A SQL-adatszinkronizálás és a Azure SQL Database adatbázisok közötti SQL Server
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ez az oktatóanyag bemutatja, hogyan állíthatja be a SQL-adatszinkronizálás egy olyan szinkronizálási csoport létrehozásával, amely a Azure SQL Database és a SQL Server is tartalmazza. A szinkronizálási csoport egyénileg van konfigurálva, és a beállított ütemezés szerint szinkronizál.

Az oktatóanyag feltételezi, hogy legalább némi tapasztalattal rendelkezik a SQL Database és SQL Server.

A szolgáltatások áttekintését SQL-adatszinkronizálás [adatok](sql-data-sync-data-sql-server-sql-database.md)felhőbeli és helyszíni adatbázisok közötti szinkronizálása a SQL-adatszinkronizálás.

Az adatbázis konfigurálásával SQL-adatszinkronizálás PowerShell-példákért lásd: A SQL Database adatbázisai vagy a Azure SQL Database és a SQL Server [](scripts/sql-data-sync-sync-data-between-sql-databases.md) [](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

> [!IMPORTANT]
> SQL-adatszinkronizálás **jelenleg nem támogatja** Azure SQL Managed Instance-t.

## <a name="create-sync-group"></a>Szinkronizálási csoport létrehozása

1. A Azure Portal [meg](https://portal.azure.com) az adatbázist a SQL Database. Keresse meg és válassza az **SQL-adatbázisok lehetőséget.**

    ![Adatbázisok keresése, Microsoft Azure Portal](./media/sql-data-sync-sql-server-configure/search-for-sql-databases.png)

1. Válassza ki a központi adatbázisként használni kívánt adatbázist a adatszinkronizálás.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/select-sql-database.png" alt-text = "Select from the database list, Microsoft Azure portal":::

    > [!NOTE]
    > A központi adatbázis a szinkronizálási topológia központi végpontja, amelyben a szinkronizálási csoport több adatbázisvégponttal rendelkezik. A szinkronizálási csoport végpontjaival együtt a többi tagadatbázis szinkronizálva lesz a központi adatbázissal.

1. A kiválasztott **adatbázis SQL Database** menüjében válassza a Szinkronizálás más **adatbázisokkal lehetőséget.**

    :::image type="content" source="./media/sql-data-sync-sql-server-configure/sync-to-other-databases.png" alt-text = "Sync to other databases, Microsoft Azure portal":::

1. A Szinkronizálás **más adatbázisokkal lapon** válassza az Új **szinkronizálási csoport lehetőséget.** Megnyílik **az Új szinkronizálási csoport** lap a **Szinkronizálási csoport létrehozása lehetőség segítségével.**

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/create-sync-group.png" alt-text = "Set up new sync group with private link":::

   A Csoport **adatszinkronizálás lapon** módosítsa a következő beállításokat:

   | Beállítás                        | Leírás |
   | ------------------------------ | ------------------------------------------------- |
   | **Szinkronizálási csoport neve** | Adja meg az új szinkronizálási csoport nevét. Ez a név nem azonos az adatbázis nevével. |
   | **Metaadat-adatbázis szinkronizálása** | Adatbázis létrehozása (ajánlott) vagy meglévő adatbázis használata.<br/><br/>Ha az Új **adatbázis lehetőséget választja,** válassza az **Új adatbázis létrehozása lehetőséget.** Ezután a **SQL Database** adja meg és konfigurálja az új adatbázist, majd kattintson az **OK gombra.**<br/><br/>Ha a Meglévő **adatbázis használata lehetőséget választja,** válassza ki az adatbázist a listából. |
   | **Automatikus szinkronizálás** | Válassza a **Be vagy** a **Ki lehetőséget.**<br/><br/>Ha a **Be** lehetőséget választja, adjon meg egy számot, és válassza a **Másodpercek,**  **Percek,** Órák vagy **Napok** lehetőséget a Szinkronizálás **gyakorisága szakaszban.**<br/> Az első szinkronizálás a kiválasztott időköz eltelte után kezdődik a konfiguráció mentésének ideje után.|
   | **Ütközésfeloldás** | Válassza a **Hub win (Hub nyer)** vagy **a Member win (Tag nyer) lehetőséget.**<br/><br/>**A hub nyerés** azt jelenti, hogy ütközések esetén a központi adatbázis adatai felülírják a tagadatbázisban található ütköző adatokat.<br/><br/>**A tag nyerés** azt jelenti, hogy ütközés esetén a tagadatbázisban található adatok felülírják a központi adatbázisban található ütköző adatokat. |
   | **Privát kapcsolat használata** | Válasszon egy szolgáltatás által felügyelt privát végpontot, hogy biztonságos kapcsolatot létesítsen a szinkronizálási szolgáltatás és a központi adatbázis között. |

   > [!NOTE]
   > A Microsoft javasolja, hogy hozzon létre egy új, üres adatbázist, amely a Metaadatok szinkronizálása **adatbázisként használható.** adatszinkronizálás létrehoz táblákat ebben az adatbázisban, és futtat egy gyakori számítási feladatot. Ez az adatbázis  a metaadat-szinkronizálási adatbázisként van megosztva a kiválasztott régióban és előfizetésben található összes szinkronizálási csoport számára. Nem módosíthatja az adatbázist vagy a nevét a régióban található összes szinkronizálási csoport és szinkronizálási ügynök eltávolítása nélkül. Ezenkívül a rugalmas feladatok adatbázisa nem használható metaadat SQL-adatszinkronizálás adatbázisként, és fordítva.  

   Kattintson **az OK** gombra, és várja meg a szinkronizálási csoport létrejöttét és üzembe helyezését.
   
1. Az Új **szinkronizálási csoport lapon,** ha a Privát kapcsolat használata beállítást **választotta,** jóvá kell hagynia a privát végpont kapcsolatát. Az információs üzenetben található hivatkozás arra a privát végpontkapcsolati élményre mutató hivatkozást tartalmazza, ahol jóváhagyhatja a kapcsolatot. 

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/approve-private-link-update.png" alt-text = "Approve private link":::
   
   > [!NOTE]
   > A syng csoport privát hivatkozásai és a szinkronizálási tagok külön kell létrehozni, jóváhagyni és letiltani. 

## <a name="add-sync-members"></a>Szinkronizálási tagok hozzáadása

Az új szinkronizálási csoport létrehozása és üzembe helyezése után nyissa meg a szinkronizálási csoportot, és nyissa meg az **Adatbázisok** lapot, ahol kiválaszthatja a szinkronizálási tagokat.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/add-sync-members.png" alt-text = "Select sync members":::
   
   > [!NOTE]
   > Ha frissíteni vagy beszúrni a felhasználónevet és  a jelszót a központi adatbázisba, a Szinkronizálási tagok kiválasztása lapon válassza a Központi adatbázis **szakaszt.** 

### <a name="to-add-a-database-in-azure-sql-database"></a>Adatbázis hozzáadása a Azure SQL Database

A **Szinkronizálási tagok kiválasztása szakaszban** igény szerint hozzáadhat egy adatbázist a Azure SQL Database a szinkronizálási csoporthoz az Azure-adatbázis hozzáadása lehetőség **kiválasztásával.** Megnyílik **az Azure-adatbázis konfigurálása** oldal.
  
   :::image type="content" source="./media/sql-data-sync-sql-server-configure/step-two-configure.png" alt-text = "Add a database to the sync group":::
   
  A Konfiguráció **Azure SQL Database** módosítsa a következő beállításokat:

  | Beállítás                       | Leírás |
  | ----------------------------- | ------------------------------------------------- |
  | **Szinkronizálási tag neve** | Adja meg az új szinkronizálási tag nevét. Ez a név nem azonos az adatbázis nevével. |
  | **Előfizetés** | Számlázási célból válassza ki a társított Azure-előfizetést. |
  | **Azure SQL Server** | Válassza ki a meglévő kiszolgálót. |
  | **Azure SQL Database** | Válassza ki a meglévő adatbázist a SQL Database. |
  | **Szinkronizálási irányok** | Válassza **a Kétirányú szinkronizálás** lehetőséget a **központba,** vagy a **Központból lehetőséget.** |
  | **Felhasználónév** és **jelszó** | Adja meg annak a kiszolgálónak a meglévő hitelesítő adatait, amelyen a tagadatbázis található. Ebben a szakaszban ne *adjon* meg új hitelesítő adatokat. |
  | **Privát kapcsolat használata** | Válasszon egy szolgáltatás által felügyelt privát végpontot, hogy biztonságos kapcsolatot létesítsen a szinkronizálási szolgáltatás és a tagadatbázis között. |

  Kattintson **az OK** gombra, és várjon, amíg létrejön és üzembe lesz telepítve az új szinkronizálási tag.

<a name="add-on-prem"></a>

### <a name="to-add-a-sql-server-database"></a>Új adatbázis SQL Server hozzáadása

A **Tagadatbázis szakaszban** igény szerint hozzáadhat egy SQL Server-adatbázist a szinkronizálási csoporthoz a Helyszíni adatbázis **hozzáadása lehetőség kiválasztásával.** Megnyílik **a Helyszíni konfigurálás** lap, ahol a következő lehetőségeket használhatja:

1. Válassza **a Szinkronizálási ügynök átjáró kiválasztása lehetőséget.** Megnyílik **a Szinkronizálási ügynök** kiválasztása lap.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/steptwo-agent.png" alt-text = "Creating a sync agent":::

1. A **Szinkronizálási ügynök kiválasztása lapon** válassza ki, hogy meglévő ügynököt használ-e, vagy létrehoz egy ügynököt.

   Ha a Meglévő **ügynökök lehetőséget választja,** válassza ki a listából a meglévő ügynököt.

   Ha az **Új ügynök létrehozása lehetőséget választja,** tegye a következőket:

   1. Töltse le az adatszinkronizálási ügynököt a megadott hivatkozásról, és telepítse arra a számítógépre, SQL Server található. Az ügynököt letöltheti közvetlenül a következő Azure SQL-adatszinkronizálás [ügynökből:](https://www.microsoft.com/download/details.aspx?id=27693).

      > [!IMPORTANT]
      > Ahhoz, hogy az ügyfélügynök kommunikáljon a kiszolgálóval, meg kell nyitnia az 1433-as kimenő TCP-portot a tűzfalon.

   1. Adja meg az ügynök nevét.

   1. Válassza a Create and Generate Key (Kulcs **létrehozása és létrehozása) lehetőséget,** és másolja az ügynökkulcsot a vágólapra.

   1. Kattintson **az OK** gombra a **Szinkronizálási ügynök kiválasztása lap bezárul.**

1. A SQL Server keresse meg és futtassa az Ügyfélszinkronizálási ügynök alkalmazást.

   ![Az adatszinkronizálási ügyfélügynök alkalmazás](./media/sql-data-sync-sql-server-configure/datasync-preview-clientagent.png)

    1. A szinkronizálási ügynökalkalmazásban válassza az **Ügynökkulcs elküldése lehetőséget.** Megnyílik **a Metaadatok szinkronizálása adatbázis konfigurációja** párbeszédpanel.

    1. A **Metaadatok szinkronizálása adatbázis konfigurációja** párbeszédpanelen illessze be az ügynökkulcsot a Azure Portal. Adja meg annak a kiszolgálónak a meglévő hitelesítő adatait is, amelyen a metaadat-adatbázis található. (Ha metaadat-adatbázist hozott létre, ez az adatbázis ugyanazon a kiszolgálón van, mint a központi adatbázis.) Kattintson **az OK** gombra, és várjon, amíg a konfiguráció befejeződik.

        ![Adja meg az ügynökkulcsot és a kiszolgáló hitelesítő adatait](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Ha tűzfalhiba jelenik meg, hozzon létre egy tűzfalszabályt az Azure-ban, amely engedélyezi a bejövő forgalmat a SQL Server számítógépről. A szabályt létrehozhatja manuálisan a portálon vagy a SQL Server Management Studio (SSMS) is. Az SSMS-ban csatlakozzon az Azure-beli központi adatbázishoz úgy, hogy a nevét <hub_database_name>.database.windows.net.

    1. Válassza **a Regisztráció** lehetőséget egy SQL Server regisztrálásához az ügynökkel. Megnyílik **SQL Server Konfiguráció** párbeszédpanel.

        ![Adatbázis hozzáadása és SQL Server konfigurálása](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-adddb.png)

    1. A SQL Server **párbeszédpanelen** válassza a csatlakozást SQL Server windowsos hitelesítéssel. Ha a hitelesítés SQL Server, adja meg a meglévő hitelesítő adatokat. Adja meg SQL Server szinkronizálni kívánt adatbázis nevét, majd válassza a **Kapcsolat** tesztelése lehetőséget a beállítások teszteléséhez. Ezután válassza **a Mentés lehetőséget,** és a regisztrált adatbázis megjelenik a listában.

        ![SQL Server adatbázis most már regisztrálva van](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-dbadded.png)

    1. Zárja be az Ügyfélszinkronizálási ügynök alkalmazást.

1. A portálOnon **konfigurálás lapján** válassza az **Adatbázis kiválasztása lehetőséget.**

1. Az Adatbázis **kiválasztása lap** **Tagnév** szinkronizálása mezőjében adja meg az új szinkronizálási tag nevét. Ez a név nem azonos az adatbázis nevével. Válassza ki az adatbázist a listából. A **Szinkronizálási irányok mezőben** válassza **a Kétirányú** szinkronizálás, a **Központba** vagy a **Központból lehetőséget.**

    ![A helyszíni adatbázis kiválasztása](./media/sql-data-sync-sql-server-configure/datasync-preview-selectdb.png)

1. Kattintson **az OK** gombra az Adatbázis kiválasztása lap **bezárulához.** Ezután kattintson az  **OK** gombra a Helyszíni konfigurálás lap bezárul, és várjon, amíg létrejön és üzembe lesz állítva az új szinkronizálási tag. Végül kattintson az **OK gombra** a **Szinkronizálási tagok kiválasztása lap bezárul.**

> [!NOTE]
> Ha csatlakozni szeretne SQL-adatszinkronizálás és a helyi ügynökhöz, adja hozzá a felhasználónevét a *szerepkörhöz DataSync_Executor.* adatszinkronizálás létrehozza ezt a szerepkört a SQL Server példányon.

## <a name="configure-sync-group"></a>Szinkronizálási csoport konfigurálása

Az új szinkronizálási csoport tagjainak létrehozása és  üzembe helyezése után az Adatbázis-szinkronizálási csoport oldal Táblák **szakaszára** kell átmenni.

![3. lépés– beállítások](./media/sql-data-sync-sql-server-configure/configure-sync-group.png)

1. A Táblák **lapon** válasszon ki egy adatbázist a szinkronizálási csoport tagjainak listájából, majd válassza a **Séma frissítése lehetőséget.** Várjon néhány perces késleltetést a frissítési sémában. Privát kapcsolat használata esetén a késés néhány perccel tovább is tarthat.

1. A listából válassza ki a szinkronizálni kívánt táblákat. Alapértelmezés szerint minden oszlop ki van jelölve, ezért tiltsa le a jelölőnégyzetet a szinkronizálni nem kívánt oszlopoknál. Ne hagyja kijelölve az elsődleges kulcs oszlopot.

1. Kattintson a **Mentés** gombra.

1. Alapértelmezés szerint az adatbázisok nincsenek szinkronizálva, amíg nincsenek ütemezve vagy manuálisan futtatva. Manuális szinkronizálás futtatásához lépjen az adatbázisra a SQL Database a Azure Portal, válassza a Szinkronizálás más adatbázisokkal lehetőséget, majd válassza ki a szinkronizálási csoportot.  Megnyílik **adatszinkronizálás** lap. Válassza a **Szinkronizálás** elemet.

    ![Manuális szinkronizálás](./media/sql-data-sync-sql-server-configure/datasync-sync.png)

## <a name="faq"></a>GYIK

**Teljes SQL-adatszinkronizálás hoz létre táblákat?**

Ha a szinkronizálási sématáblák hiányoznak a céladatbázisból, az SQL-adatszinkronizálás létrehozza azokat a kiválasztott oszlopokkal. Ez azonban nem eredményez teljes hűségsémát a következő okok miatt:

- Csak az Ön által kiválasztott oszlopok jönnek létre a céltáblában. A nem kiválasztott oszlopokat a program figyelmen kívül hagyja.
- Csak a kiválasztott oszlopindexek jönnek létre a céltáblában. A nem kiválasztott oszlopok indexeit a program figyelmen kívül hagyja.
- Az XML-típusú oszlopok indexe nem jön létre.
- A CHECK korlátozások nem jön létre.
- A forrástáblákban nem jön létre eseményindító.
- A nézetek és a tárolt eljárások nem jön létre.

E korlátozások miatt javasoljuk a következőket:

- Éles környezetekben saját maga hozhatja létre a teljes körű sémát.
- A szolgáltatással való kísérletezéskor használja az automatikus kiépítési funkciót.

**Miért látom a nem létrehozott táblákat?**

adatszinkronizálás további táblákat hoz létre az adatbázisban a változáskövetéshez. Ezeket ne törölje, vagy adatszinkronizálás leáll.

**Az adataim konvergensek a szinkronizálás után?**

Nem feltétlenül. Vegy egy szinkronizálási csoportot egy központtal és három küllővel (A, B és C), ahol a szinkronizálások az A központ és a B központ, valamint a központ és a C között vannak. Ha az *A* adatbázist a központ szinkronizálása után módosítják, a rendszer a következő szinkronizálási feladatig nem írja a változást a B adatbázisba vagy a C adatbázisba.

**Hogyan be a sémaváltozásokat egy szinkronizálási csoportba?**

Minden sémamódosítást manuálisan kell elvégeznie és propagálnia.

1. Replikálja manuálisan a sémaváltozásokat a központba és az összes szinkronizálási tagra.
1. Frissítse a szinkronizálási sémát.

Új táblák és oszlopok hozzáadásához:

Az új táblák és oszlopok nem befolyásolják az aktuális szinkronizálást, és adatszinkronizálás figyelmen kívül hagyja őket, amíg fel nem veszi őket a szinkronizálási sémába. Új adatbázis-objektumok hozzáadásakor kövesse az alábbi sorrendet:

1. Adjon hozzá új táblákat vagy oszlopokat a központhoz és az összes szinkronizálási taghoz.
1. Adjon hozzá új táblákat vagy oszlopokat a szinkronizálási sémához.
1. Kezdje el beszúrni az értékeket az új táblákba és oszlopokba.

Egy oszlop adattípusának módosításához:

Ha módosítja egy meglévő oszlop adattípusát, a adatszinkronizálás továbbra is működni fog, ha az új értékek illeszkednek a szinkronizálási sémában meghatározott eredeti adattípushoz. Ha például a forrásadatbázis típusát **int** értékről **bigint** típusra módosítja, a adatszinkronizálás addig működik, amíg be nem szúr egy túl nagy értéket az **int** adattípushoz. A módosítás befejezéséhez replikálja manuálisan a sémaváltozást a központba és az összes szinkronizálási tagra, majd frissítse a szinkronizálási sémát.

**Hogyan exportálható és importálható adatbázis adatszinkronizálás?**

Miután *.bacpac* fájlként exportált egy adatbázist, és importálta a fájlt egy adatbázis létrehozásához, tegye a következőket az adatszinkronizálás az új adatbázisban:

1. A parancsprogram használatával adatszinkronizálás az új adatbázis összes objektumát és további [tábláját.](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql) A szkript törli az összes szükséges adatszinkronizálás az adatbázisból.
1. Hozza létre újra a szinkronizálási csoportot az új adatbázissal. Ha már nincs szüksége a régi szinkronizálási csoportra, törölje azt.

**Hol találok információkat az ügyfélügynökkel kapcsolatban?**

Az ügyfélügynökkel kapcsolatos gyakori kérdésekért tekintse meg az [ügynökkel kapcsolatos gyakori kérdéseket.](sql-data-sync-agent-overview.md#agent-faq)

**Manuálisan kell jóváhagyni a hivatkozást, mielőtt elkezdenék használni?**

Igen, manuálisan kell jóváhagynia a szolgáltatás által felügyelt privát végpontot a szinkronizálási csoport üzembe helyezése Azure Portal Privát végponti kapcsolatok lapján vagy a PowerShell használatával.

**Miért jelenik meg tűzfalhiba, amikor a szinkronizálási feladat kiépíti az Azure-adatbázisomat?**

Ez azért fordulhat elő, mert az Azure-erőforrások nem férhetnek hozzá a kiszolgálóhoz. Győződjön meg arról, hogy az Azure-adatbázis tűzfalán az "Allow Azure services and resources to access this server" (Azure-szolgáltatások és -erőforrások hozzáférésének engedélyezése a kiszolgálóhoz) beállítás "Igen" van beállítva.


## <a name="next-steps"></a>Következő lépések

Gratulálunk! Létrehozott egy szinkronizálási csoportot, amely tartalmaz egy SQL Database-példányt és egy SQL Server adatbázist.

További információ az SQL Data Syncről:

- [Data Sync Agent a Azure SQL-adatszinkronizálás](sql-data-sync-agent-overview.md)
- [Ajánlott eljárások és](sql-data-sync-best-practices.md) [a problémák elhárítása Azure SQL-adatszinkronizálás](sql-data-sync-troubleshoot.md)
- [Az SQL-adatszinkronizálás monitor Azure Monitor naplók segítségével](./monitor-tune-overview.md)
- [A szinkronizálási séma frissítése a Transact-SQL vagy](sql-data-sync-update-sync-schema.md) [a PowerShell használatával](scripts/update-sync-schema-in-sync-group.md)

További információ az SQL Database-ről:

- [Az SQL Database áttekintése](sql-database-paas-overview.md)
- [Az adatbázis életciklusának felügyelete](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
