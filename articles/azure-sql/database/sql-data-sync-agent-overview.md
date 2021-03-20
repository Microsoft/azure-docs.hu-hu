---
title: SQL-adatszinkronizálás adatszinkronizálási ügynök
description: Ismerje meg, hogyan telepítheti és futtathatja az Azure-beli SQL-adatszinkronizálás adatszinkronizálási ügynökét az adatSQL Server-adatbázisokkal való szinkronizáláshoz
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/20/2018
ms.openlocfilehash: ed8d51adf5a93b470f287383a4d3eeb866b15236
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92791460"
---
# <a name="data-sync-agent-for-sql-data-sync"></a>SQL-adatszinkronizálás adatszinkronizálási ügynök
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Az Azure-beli SQL-adatszinkronizálás adatszinkronizálási ügynökének telepítésével és konfigurálásával SQL Server adatbázisokkal szinkronizálhatja az adatszinkronizálást. További információ a SQL-adatszinkronizálásről: az [adatok szinkronizálása több Felhőbeli és helyszíni adatbázis között SQL-adatszinkronizálásokkal](sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> A SQL-adatszinkronizálás jelenleg **nem** támogatja az Azure SQL felügyelt példányát.

## <a name="download-and-install"></a>Letöltés és telepítés

Az adatszinkronizálási ügynök letöltéséhez nyissa meg a [SQL-adatszinkronizálás Agent ügynököt](https://www.microsoft.com/download/details.aspx?id=27693). Az adatszinkronizálási ügynök frissítéséhez telepítse az ügynököt a régi ügynökével megegyező helyre, és felülírja az eredetit.

### <a name="install-silently"></a>Csendes telepítés

Ha az adatszinkronizálási ügynököt csendesen szeretné telepíteni a parancssorból, adjon meg egy, az alábbi példához hasonló parancsot. Keresse meg a letöltött. msi fájl fájlnevét, és adja meg a saját értékeit a **TARGETDIRCDotNet** és a **SERVICEACCOUNT** argumentumhoz.

- Ha nem ad meg értéket a **TARGETDIRCDotNet**, az alapértelmezett érték: `C:\Program Files (x86)\Microsoft SQL Data Sync 2.0` .

- Ha a `LocalSystem` **SERVICEACCOUNT** értékeként adja meg, akkor SQL Server hitelesítést kell használnia, ha úgy konfigurálja az ügynököt, hogy az SQL Serverhoz kapcsolódjon.

- Ha a **SERVICEACCOUNT** értékeként tartományi felhasználói fiókot vagy helyi felhasználói fiókot ad meg, akkor a **SERVICEPASSWORD** argumentummal is meg kell adnia a jelszót. Például: `SERVICEACCOUNT="<domain>\<user>"  SERVICEPASSWORD="<password>"`.

```cmd
msiexec /i "SQLDataSyncAgent-2.0-x86-ENU.msi" TARGETDIR="C:\Program Files (x86)\Microsoft SQL Data Sync 2.0" SERVICEACCOUNT="LocalSystem" /qn
```

## <a name="sync-data-with-a-sql-server-database"></a>Az adatszinkronizálás SQL Server-adatbázissal

Az adatszinkronizálási ügynök konfigurálásához, hogy egy vagy több SQL Server adatbázissal szinkronizálja az adatszinkronizálást, tekintse meg [a SQL Server-adatbázis hozzáadása](sql-data-sync-sql-server-configure.md#add-on-prem)című témakört.

## <a name="data-sync-agent-faq"></a><a name="agent-faq"></a> Adatszinkronizálási ügynök – gyakori kérdések

### <a name="why-do-i-need-a-client-agent"></a>Miért van szükségem ügyfél-ügynökre

A SQL-adatszinkronizálás szolgáltatás az ügyfél ügynökén keresztül kommunikál SQL Server adatbázisokkal. Ez a biztonsági szolgáltatás megakadályozza a közvetlen kommunikációt a tűzfal mögötti adatbázisokkal. Ha a SQL-adatszinkronizálás szolgáltatás kommunikál az ügynökkel, a titkosított kapcsolatokat és egyedi tokent vagy *ügynököt* használ. A SQL Server adatbázisok hitelesítik az ügynököt a kapcsolatok karakterláncának és az ügynök kulcsának használatával. Ez a kialakítás magas szintű biztonságot nyújt az adatai számára.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>A helyi ügynök felhasználói felületének több példánya is futtatható

A felhasználói felületnek csak egy példánya futtatható.

### <a name="how-can-i-change-my-service-account"></a>Hogyan változtathatom meg a szolgáltatásfiókot

Miután telepítette az ügynököt, az egyetlen módszer a szolgáltatásfiók módosítására, hogy eltávolítsa, és telepítsen egy új ügyfélszolgáltatást az új szolgáltatásfiók használatával.

### <a name="how-do-i-change-my-agent-key"></a>Az ügynök kulcsának módosítása Hogyan

Az ügynök kulcsát csak egyszer használhatja ügynök. Nem használható fel újra, ha eltávolítja, majd újratelepíti az új ügynököt, és nem is használhatja több ügynök. Ha egy meglévő ügynökhöz új kulcsot kell létrehoznia, akkor győződjön meg arról, hogy ugyanazt a kulcsot rögzíti az ügyfél ügynökével és a SQL-adatszinkronizálás szolgáltatással.

### <a name="how-do-i-retire-a-client-agent"></a>Ügyfél-ügynök kivonása Hogyan

Az ügynökök azonnali érvénytelenítéséhez vagy kivonásához a kulcs újralétrehozása a portálon, de ne küldje el az ügynök felhasználói felületén. A kulcs újragenerálása érvényteleníti az előző kulcsot, függetlenül attól, hogy a kapcsolódó ügynök online vagy offline állapotban van-e.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Ügyféloldali ügynök áthelyezése másik számítógépre Hogyan

Ha a helyi ügynököt egy másik számítógépről szeretné futtatni, mint amelyen jelenleg van, tegye a következőket:

1. Telepítse az ügynököt a kívánt számítógépre.
2. Jelentkezzen be a SQL-adatszinkronizálás portálra, és állítson be egy ügynököt az új ügynökhöz.
3. Az új ügynök felhasználói felületén küldje el az új ügynököt.
4. Várjon, amíg az ügyfél-ügynök letölti a korábban regisztrált helyszíni adatbázisok listáját.
5. Adja meg az adatbázis hitelesítő adatait az összes olyan adatbázishoz, amely nem érhető el. Ezeknek az adatbázisoknak elérhetőnek kell lenniük az új számítógépről, amelyen az ügynök telepítve van.

## <a name="troubleshoot-data-sync-agent-issues"></a><a name="agent-tshoot"></a> Az adatszinkronizálási ügynökkel kapcsolatos problémák elhárítása

- [Sikertelen volt az ügyfél-ügynök telepítése, eltávolítása vagy javítása](#agent-install)

- [Az ügyfél ügynöke nem működik az Eltávolítás megszakítása után](#agent-uninstall)

- [Az adatbázis nem szerepel az ügynökök listáján](#agent-list)

- [Az ügyfél ügynöke nem indul el (1069-es hiba)](#agent-start)

- [Nem tudom elküldeni az ügynök kulcsát](#agent-key)

- [Az ügyfél ügynöke nem törölhető a portálról, ha a hozzá tartozó helyszíni adatbázis nem érhető el](#agent-delete)

- [A helyi szinkronizáló ügynök alkalmazás nem tud kapcsolódni a helyi szinkronizálási szolgáltatáshoz](#agent-connect)

### <a name="the-client-agent-install-uninstall-or-repair-fails"></a><a name="agent-install"></a> Sikertelen volt az ügyfél-ügynök telepítése, eltávolítása vagy javítása

- **OK**. Ennek a hibának számos oka lehet. A hiba konkrét okának meghatározásához tekintse meg a naplókat.

- **Megoldás**. A hiba konkrét okának megállapításához állítson elő és tekintse meg a Windows Installer naplókat. A naplózást bekapcsolhatja a parancssorba. Ha például a letöltött telepítőfájl `SQLDataSyncAgent-2.0-x86-ENU.msi` , a naplófájlokat a következő parancssorok segítségével hozhatja meg és vizsgálhatja meg:

  - Telepítés esetén: `msiexec.exe /i SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`
  - Eltávolítások esetén: `msiexec.exe /x SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`

    Bekapcsolhatja a naplózást is a Windows Installer által végrehajtott összes telepítéshez. A Microsoft Tudásbázis cikke a [Windows Installer naplózásának engedélyezéséhez](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) egy kattintással elérhető megoldást biztosít a Windows Installer naplózásának bekapcsolásához. A naplófájlok helyét is megadja.

### <a name="the-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a><a name="agent-uninstall"></a> Az ügyfél ügynöke nem működik az Eltávolítás megszakítása után

Az ügyfél ügynöke nem működik, még az eltávolításának megszakítása után is.

- **OK**. Ennek az az oka, hogy a SQL-adatszinkronizálás ügyfél ügynöke nem tárolja a hitelesítő adatokat.

- **Megoldás**. A következő két megoldás kipróbálására van lehetősége:

    -   A Services. msc segítségével adja meg újra az ügyfél ügynökének hitelesítő adatait.
    -   Távolítsa el ezt az ügynököt, majd telepítsen egy újat. Töltse le és telepítse a legújabb ügyfél-ügynököt a [letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=27693).

### <a name="my-database-isnt-listed-in-the-agent-list"></a><a name="agent-list"></a> Az adatbázis nem szerepel az ügynökök listáján

Amikor meglévő SQL Server adatbázist próbál hozzáadni egy szinkronizálási csoporthoz, az adatbázis nem jelenik meg az ügynökök listájában.

A probléma a következő esetekben fordulhat elő:

- **OK**. Az ügyfél-ügynök és a szinkronizálási csoport különböző adatközpontokban található.

- **Megoldás**. Az ügyfél-ügynöknek és a szinkronizálási csoportnak ugyanabban az adatközpontban kell lennie. Ennek beállításához két lehetőség közül választhat:

    -   Hozzon létre egy új ügynököt abban az adatközpontban, amelyben a szinkronizálási csoport található. Ezután regisztrálja az adatbázist az ügynökkel.
    -   Az aktuális szinkronizálási csoport törlése. Ezután hozza létre újra a szinkronizálási csoportot abban az adatközpontban, amelyben az ügynök található.

- **OK**. Az ügyfél-ügynök listája nem aktuális.

- **Megoldás**. Állítsa le, majd indítsa újra az ügyfél-ügynök szolgáltatást.

    A helyi ügynök csak az ügynök kulcsának első beadásakor tölti le a társított adatbázisok listáját. Nem tölti le a társított adatbázisok listáját a következő ügynök kulcsának beküldésekor. Az ügynök áthelyezése során regisztrált adatbázisok nem jelennek meg az eredeti ügynök példányában.

### <a name="client-agent-doesnt-start-error-1069"></a><a name="agent-start"></a> Az ügyfél ügynöke nem indul el (1069-es hiba)

Felderíti, hogy az ügynök nem fut SQL Server üzemeltető számítógépen. Amikor megkísérli manuálisan elindítani az ügynököt, megjelenik egy párbeszédpanel, amelyen a "hiba 1069: a szolgáltatás nem indult el a bejelentkezési hiba miatt." hibaüzenet jelenik meg.

![Adatszinkronizálási hiba 1069 párbeszédpanel](./media/sql-data-sync-agent-overview/sync-error-1069.png)

- **OK**. Ennek a hibának a valószínű oka, hogy a helyi kiszolgálón lévő jelszó megváltozott az ügynök és az ügynök jelszavának létrehozása óta.

- **Megoldás**. Frissítse az ügynök jelszavát az aktuális kiszolgáló jelszavára:

  1. Keresse meg az SQL-adatszinkronizálás ügyfél-ügynök szolgáltatást.  
    a. Válassza az **Indítás** elemet.  
    b. A keresőmezőbe írja be a **Services. msc** kifejezést.  
    c. A keresési eredmények között válassza a **szolgáltatások** lehetőséget.  
    d. A **szolgáltatások** ablakban görgessen a **SQL-adatszinkronizálás-ügynök** bejegyzéséhez.  
  1. Kattintson a jobb gombbal **SQL-adatszinkronizálás ügynökre**, majd válassza a **Leállítás** lehetőséget.
  1. Kattintson a jobb gombbal **SQL-adatszinkronizálás ügynökre**, majd válassza a **Tulajdonságok** lehetőséget.
  1. **SQL-adatszinkronizálás ügynök tulajdonságai** lapon válassza a **Bejelentkezés** lapot.
  1. A **jelszó** mezőbe írja be a jelszavát.
  1. A **Jelszó megerősítése** mezőbe írja be újra a jelszót.
  1. Válassza az **Apply** (Alkalmaz) lehetőséget, majd kattintson az **OK** gombra.
  1. A **szolgáltatások** ablakban kattintson a jobb gombbal a **SQL-adatszinkronizálás ügynök** szolgáltatásra, majd kattintson az **Indítás** parancsra.
  1. A **szolgáltatások** ablak bezárásához.

### <a name="i-cant-submit-the-agent-key"></a><a name="agent-key"></a> Nem tudom elküldeni az ügynök kulcsát

Miután létrehozta vagy újból létrehozta az ügynök kulcsát, megpróbálja elküldeni a kulcsot a SqlAzureDataSyncAgent alkalmazáson keresztül. A Küldés nem hajtható végre.

![Szinkronizálási hiba párbeszédpanel – nem lehet elküldeni az ügynök kulcsát](./media/sql-data-sync-agent-overview/sync-error-cant-submit-agent-key.png)

- **Előfeltételek**. A folytatás előtt tekintse át a következő előfeltételeket:

  - A SQL-adatszinkronizálás Windows-szolgáltatás fut.

  - A SQL-adatszinkronizálás Windows-szolgáltatáshoz tartozó szolgáltatásfiók hálózati hozzáféréssel rendelkezik.

  - A kimenő 1433-as port nyitva van a helyi tűzfalszabály-szabályban.

  - A rendszer hozzáadja a helyi IP-címet a szinkronizálási metaadat-adatbázis kiszolgálói vagy adatbázis-tűzfalszabály-szabályához.

- **OK**. Az ügynök kulcsa egyedileg azonosítja az egyes helyi ügynököket. A kulcsnak két feltételnek kell megfelelnie:

  -   Az SQL-adatszinkronizálás-kiszolgálón lévő ügyfél-ügynök kulcsnak és a helyi számítógépnek azonosnak kell lennie.
  -   Az ügyfél-ügynök kulcsa csak egyszer használható.

- **Megoldás**. Ha az ügynök nem működik, ez azért van, mert az egyik vagy mindkét feltétel nem teljesül. Az ügynök újbóli működésének megkezdéséhez:

  1. Új kulcs létrehozása.
  1. Alkalmazza az új kulcsot az ügynökre.

  Az új kulcs alkalmazása az ügynökre:

  1. A Fájlkezelőben lépjen az ügynök telepítési könyvtárába. Az alapértelmezett telepítési könyvtár a C: \\ Program Files (x86) \\ Microsoft SQL-adatszinkronizálás.
  1. Kattintson duplán a bin alkönyvtárra.
  1. Nyissa meg a SqlAzureDataSyncAgent alkalmazást.
  1. Válassza a **küldési ügynök kulcsa** lehetőséget.
  1. A rendelkezésre álló helyen illessze be a kulcsot a vágólapról.
  1. Válassza az **OK** lehetőséget.
  1. A program lezárása.

### <a name="the-client-agent-cant-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a><a name="agent-delete"></a> Az ügyfél ügynöke nem törölhető a portálról, ha a hozzá tartozó helyszíni adatbázis nem érhető el

Ha egy SQL-adatszinkronizálás ügyfél-ügynökkel regisztrált helyi végpont (azaz adatbázis) nem érhető el, az ügyfél ügynöke nem törölhető.

- **OK**. Nem lehet törölni a helyi ügynököt, mert a nem elérhető adatbázis még regisztrálva van az ügynökben. Amikor megpróbálja törölni az ügynököt, a törlési folyamat megkísérli elérni az adatbázist, ami nem sikerül.

- **Megoldás**. Törölje a nem elérhető adatbázist a "kényszerített Törlés" paranccsal.

> [!NOTE]
> Ha a szinkronizálási metaadatok táblái a "kényszerített Törlés" után is megmaradnak, használja a (z `deprovisioningutil.exe` ) t a (z) használatával

### <a name="local-sync-agent-app-cant-connect-to-the-local-sync-service"></a><a name="agent-connect"></a> A helyi szinkronizáló ügynök alkalmazás nem tud kapcsolódni a helyi szinkronizálási szolgáltatáshoz

- **Megoldás**. Próbálja meg a következőket:

  1. Lépjen ki az alkalmazásból.  
  1. Nyissa meg a összetevő-szolgáltatások panelt.  
    a. A tálcán a keresőmezőbe írja be a **Services. msc parancsot**.  
    b. A keresési eredmények között kattintson duplán a **szolgáltatások** elemre.  
  1. Állítsa le a **SQL-adatszinkronizálás** szolgáltatást.
  1. Indítsa újra a **SQL-adatszinkronizálás** szolgáltatást.  
  1. újra meg kell nyitniuk az alkalmazást;

## <a name="run-the-data-sync-agent-from-the-command-prompt"></a>Az adatszinkronizálási ügynök futtatása a parancssorból

A parancssorból a következő adatszinkronizálási ügynök parancsait futtathatja:

### <a name="ping-the-service"></a>A szolgáltatás pingelése

#### <a name="usage"></a>Használat

```cmd
SqlDataSyncAgentCommand.exe -action pingsyncservice
```

#### <a name="example"></a>Példa

```cmd
SqlDataSyncAgentCommand.exe -action "pingsyncservice"
```

### <a name="display-registered-databases"></a>Regisztrált adatbázisok megjelenítése

#### <a name="usage"></a>Használat

```cmd
SqlDataSyncAgentCommand.exe -action displayregistereddatabases
```

#### <a name="example"></a>Példa

```cmd
SqlDataSyncAgentCommand.exe -action "displayregistereddatabases"
```

### <a name="submit-the-agent-key"></a>Az ügynök kulcsának elküldése

#### <a name="usage"></a>Használat

```cmd
Usage: SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key]  -username [user name] -password [password]
```

#### <a name="example"></a>Példa

```cmd
SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key generated from portal, PowerShell, or API] -username [user name to sync metadata database] -password [user name to sync metadata database]
```

### <a name="register-a-database"></a>Adatbázis regisztrálása

#### <a name="usage"></a>Használat

```cmd
SqlDataSyncAgentCommand.exe -action registerdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Példák

```cmd
SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication windows -encryption true

```

### <a name="unregister-a-database"></a>Adatbázis regisztrációjának törlése

Ha ezt a parancsot használja egy adatbázis regisztrációjának megszüntetésére, a rendszer teljesen kiépíti az adatbázist. Ha az adatbázis más szinkronizálási csoportokban is részt vesz, a művelet megszakítja a többi szinkronizálási csoportot.

#### <a name="usage"></a>Használat

```cmd
SqlDataSyncAgentCommand.exe -action unregisterdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]
```

#### <a name="example"></a>Példa

```cmd
SqlDataSyncAgentCommand.exe -action "unregisterdatabase" -serverName localhost -databaseName testdb
```

### <a name="update-credentials"></a>Hitelesítő adatok frissítése

#### <a name="usage"></a>Használat

```cmd
SqlDataSyncAgentCommand.exe -action updatecredential -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Példák

```cmd
SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication windows -encryption true
```

## <a name="next-steps"></a>Következő lépések

A SQL-adatszinkronizálásról további információt a következő cikkekben talál:

-   Áttekintés – az [adatszinkronizálás több Felhőbeli és helyszíni adatbázison SQL-adatszinkronizálás az Azure-ban](sql-data-sync-data-sql-server-sql-database.md)
-   Adatszinkronizálás beállítása
    - A portálon – [oktatóanyag: SQL-adatszinkronizálás beállítása az Azure SQL Database és a SQL Server közötti adatszinkronizáláshoz](sql-data-sync-sql-server-configure.md)
    - A PowerShell-lel
        -  [Több adatbázis közötti szinkronizálás a Azure SQL Database-ben a PowerShell használatával](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [Azure SQL Database és egy SQL Server-példányban található adatbázis közötti szinkronizálás a PowerShell használatával](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Ajánlott eljárások – [ajánlott eljárások az Azure SQL-adatszinkronizálás](sql-data-sync-best-practices.md)
-   Figyelő – [SQL-adatszinkronizálás figyelése Azure monitor naplókkal](./monitor-tune-overview.md)
-   Hibaelhárítás – [az Azure SQL-adatszinkronizálásval kapcsolatos problémák elhárítása] SQL--adatszinkronizálás – troubleshoot.md)
-   A szinkronizálási séma frissítése
    -   A Transact-SQL- [ben a séma változásainak a SQL-adatszinkronizálás az Azure-ban való replikálásának automatizálása](sql-data-sync-update-sync-schema.md)
    -   A PowerShell [használatával – egy meglévő szinkronizálási csoportban lévő szinkronizálási séma frissítéséhez használja a PowerShellt](scripts/update-sync-schema-in-sync-group.md) .