---
title: Biztonsági mentése SQL Server Azure-ba
description: Ez a cikk bemutatja, hogyan lehet biztonsági SQL Server Azure-ba. A cikk a helyreállítás SQL Server is ismerteti.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: b6daf631248958948e799b20284d84a1e59e5dfe
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518864"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Információk az Azure-beli virtuális gépeken futó SQL Server Backupról

[Azure Backup](backup-overview.md) azure-beli virtuális gépeken futó SQL Server streamalapú, speciális megoldást kínál. Ez a megoldás Azure Backup infrastruktúra-biztonsági mentés, a hosszú távú megőrzés és a központi felügyelet előnyeivel. Emellett a következő előnyöket biztosítja kifejezetten a SQL Server:

1. Számítási feladatokat támogató biztonsági mentések, amelyek minden biztonsági mentési típust támogatnak – teljes, különbségi és naplós
2. 15 perces RPO (helyreállítási időpont-célkitűzés) gyakori naplómentésekkel
3. Időponthoz időben való helyreállítás legfeljebb egy másodpercre
4. Önálló adatbázisszintű biztonsági mentés és visszaállítás

A jelenleg támogatott biztonsági mentési és visszaállítási forgatókönyvek megtekintéséhez tekintse meg a [támogatási mátrixot.](sql-support-matrix.md#scenario-support)

## <a name="backup-process"></a>A biztonsági mentés folyamata

Ez a megoldás a natív SQL API-kat használja az SQL-adatbázisok biztonsági mentéséhez.

* Miután megadja a SQL Server VM védeni kívánt virtuális gépet, és lekérdezi a benne található adatbázisokat, a Azure Backup szolgáltatás egy számítási feladat biztonsági mentési bővítményét telepíti a virtuális gépre `AzureBackupWindowsWorkload` névkiterjesztéssel.
* Ez a bővítmény egy koordinátorból és egy SQL beépülő modulból áll. Bár a koordinátor felelős a különböző műveletek (például a biztonsági mentés, a biztonsági mentés és a visszaállítás) munkafolyamatának aktiválásáért, a beépülő modul felelős a tényleges adatfolyamért.
* A virtuális gépen található adatbázisok felderítésének Azure Backup a `NT SERVICE\AzureWLBackupPluginSvc` fiókot. Ez a fiók biztonsági mentésre és visszaállításra használatos, és SQL-rendszergazdai engedélyekkel rendelkezik. A `NT SERVICE\AzureWLBackupPluginSvc` fiók egy virtuális [szolgáltatásfiók,](/windows/security/identity-protection/access-control/service-accounts#virtual-accounts)ezért nem igényel jelszókezelést. Azure Backup a fiókot használja az adatbázis felderítésére/lekérdezésére, ezért ennek a fióknak nyilvános SQL-bejelentkezésnek kell `NT AUTHORITY\SYSTEM` lennie. Ha nem az Azure Marketplace-en hozta létre az SQL Servert futtató virtuális gépet, akkor a következő hibaüzenetet kaphatja: **UserErrorSQLNoSysadminMembership**. Ha ez előfordul, akkor [kövesse az alábbi utasításokat](#set-vm-permissions).
* Miután elindította a védelem konfigurálását a kiválasztott adatbázisokon, a biztonsági mentési szolgáltatás beállítja a koordinátort a biztonsági mentési ütemezésekkel és egyéb szabályzati adatokkal, amelyeket a bővítmény helyileg gyorsítótáraz a virtuális gépen.
* Az ütemezett időpontban a koordinátor kommunikál a beépülő modullal, és elkezdi streamelni a biztonsági mentési adatokat az SQL-kiszolgálóról a VDI használatával.  
* A beépülő modul közvetlenül a Recovery Services-tárolóba küldi az adatokat, így nincs szükség átmeneti helyre. Az adatokat a tárfiókok titkosítják és Azure Backup tárolják.
* Ha az adatátvitel befejeződött, a koordinátor megerősíti a véglegesítést a biztonsági mentési szolgáltatással.

  ![SQL Backup-architektúra](./media/backup-azure-sql-database/azure-backup-sql-overview.png)

## <a name="before-you-start"></a>Előkészületek

Mielőtt elkezdené, ellenőrizze a következő követelményeket:

1. Győződjön meg arról, hogy SQL Server fut az Azure-ban. Gyorsan létrehozhat [egy új SQL Server a](../azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart.md) piactéren.
2. Tekintse át [a funkcióval kapcsolatos szempontokat](sql-support-matrix.md#feature-considerations-and-limitations) [és forgatókönyv-támogatást.](sql-support-matrix.md#scenario-support)
3. [Tekintse át a forgatókönyvvel](faq-backup-sql-server.yml) kapcsolatos gyakori kérdéseket.

## <a name="set-vm-permissions"></a>Virtuális gépek engedélyeinek beállítása

  Amikor felderítést futtat egy SQL Server, Azure Backup a következőket:

* Hozzáadja az AzureBackupWindowsWorkload bővítményt.
* Létrehoz egy NT SERVICE\AzureWLBackupPluginSvc fiókot a virtuális gépen található adatbázisok felderítése számára. Ez a fiók biztonsági mentéshez és visszaállításhoz használatos, és SQL-rendszergazdai engedélyekkel rendelkezik.
* Felderíti a virtuális gépen futó adatbázisokat, Azure Backup NT AUTHORITY\SYSTEM fiókot használja. Ennek a fióknak nyilvános SQL-bejelentkezésnek kell lennie.

Ha nem az SQL Server VM-t hozza létre Azure Marketplace vagy sql 2008-at vagy 2008 R2-t futtat, **UserErrorSQLNoSysadminMembership** hiba jelenhet meg.

Ha Windows 2008 R2 rendszeren futó **SQL 2008** és **2008 R2** esetében ad engedélyt, tekintse meg a [következőt:](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2).

Minden más verzióhoz javítsa ki az engedélyeket a következő lépésekkel:

  1. Sysadmin SQL Server engedéllyel rendelkező fiókkal jelentkezzen be a SQL Server Management Studio (SSMS) alkalmazásba. Hacsak nincs szüksége különleges engedélyekre, a Windows-hitelesítésnek működnie kell.
  2. A SQL Server nyissa meg a **Biztonság/Bejelentkezések** mappát.

      ![Nyissa meg a Security/Logins mappát a fiókok megnyitásához](./media/backup-azure-sql-database/security-login-list.png)

  3. Kattintson a jobb gombbal **a Bejelentkezések mappára,** és válassza az **Új bejelentkezés lehetőséget.** A **Bejelentkezés – Új mezőben** válassza a Keresés **lehetőséget.**

      ![A Bejelentkezés – Új párbeszédpanelen válassza a Keresés lehetőséget.](./media/backup-azure-sql-database/new-login-search.png)

  4. Az **NT SERVICE\AzureWLBackupPluginSvc** Windows virtuális szolgáltatásfiók a virtuális gép regisztrálása és az SQL felderítési fázisa során jött létre. Adja meg a fiók nevét az Itt adhatja meg **az objektum nevét a kiválasztásához.** A **név feloldásához válassza** a Névellenőrzés lehetőséget. Válassza az **OK** lehetőséget.

      ![Az ismeretlen szolgáltatásnév feloldásához válassza a Névellenőrzés lehetőséget](./media/backup-azure-sql-database/check-name.png)

  5. A **Kiszolgálói szerepkörök beállításban** győződjön meg arról, hogy a **sysadmin** szerepkör van kiválasztva. Válassza az **OK** lehetőséget. A szükséges engedélyeknek most már léteznie kell.

      ![Győződjön meg arról, hogy a sysadmin kiszolgálói szerepkör van kiválasztva](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Most társítsa az adatbázist a Recovery Services-tárolóhoz. A Azure Portal a Védett kiszolgálók  listában kattintson a jobb gombbal arra a kiszolgálóra, amely hibás állapotban van, > **adatbázisokat.**

      ![Ellenőrizze, hogy a kiszolgáló rendelkezik-e a megfelelő engedélyekkel](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Ellenőrizze a folyamat előrehaladását **az Értesítések** területen. A kiválasztott adatbázisok megtalálása után megjelenik a sikerességről egy üzenet.

      ![Sikeres üzembe helyezést jelző üzenet](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> Ha a SQL Server több telepített SQL Server-példányt tartalmaz, akkor hozzá kell adni az **NT Service\AzureWLBackupPluginSvc** fiók rendszergazdai engedélyét az összes SQL-példányhoz.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>SQL sysadmin-engedélyeket adhat az SQL 2008-nak és az SQL 2008 R2-nek

Adja **hozzá az NT AUTHORITY\SYSTEM** és az NT **Service\AzureWLBackupPluginSvc** bejelentkezési adatokat a SQL Server példányhoz:

1. Az Object Explorerben SQL Server meg a következő példányt: .
2. Lépjen a Biztonság - > bejelentkezések lapra
3. Kattintson a jobb gombbal a bejelentkezések elemre, és válassza az *Új bejelentkezés... lehetőséget.*

    ![Új bejelentkezés az SSMS használatával](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Lépjen az Általános lapra, és adja meg az **NT AUTHORITY\SYSTEM** bejelentkezési nevet.

    ![Az SSMS bejelentkezési neve](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Válassza a *Kiszolgálói szerepkörök lehetőséget,* és válassza a *nyilvános* és a *sysadmin szerepkört.*

    ![Szerepkörök kiválasztása az SSMS-ban](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Ugrás a Status *(Állapot) állapotra.* *Adjon engedélyt* az adatbázismotorhoz való csatlakozásra, a bejelentkezést pedig *engedélyezveként.*

    ![Engedélyek megadása az SSMS-ban](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Kattintson az OK gombra.
8. Ismételje meg ugyanezen lépések sorozatát (a fenti 1–7. lépéssel) az NT Service\AzureWLBackupPluginSvc bejelentkezés hozzáadásához a SQL Server példányhoz. Ha a bejelentkezés már létezik, ellenőrizze, hogy rendelkezik-e a sysadmin kiszolgálói szerepkörrel, és az Állapot alatt adja meg az Engedély megadása az adatbázismotorhoz való csatlakozáshoz és a bejelentkezéshez engedélyezve beállítást.
9. Az engedély megadása után a **adatbázisokat** újra fel kell tárni a portálon: Tároló biztonsági mentési infrastruktúrájának számítási **->** feladatai az **->** Azure-beli virtuális gépen:

    ![A DBs újra Azure Portal](media/backup-azure-sql-database/sql-rediscover-dbs.png)

Másik lehetőségként automatizálhatja az engedélyeket az alábbi PowerShell-parancsok rendszergazdai módban való futtatásával. A példány neve alapértelmezés szerint MSSQLSERVER. Ha szükséges, módosítsa a példánynév argumentumát a szkriptben:

```powershell
param(
    [Parameter(Mandatory=$false)]
    [string] $InstanceName = "MSSQLSERVER"
)
if ($InstanceName -eq "MSSQLSERVER")
{
    $fullInstance = $env:COMPUTERNAME   # In case it is the default SQL Server Instance
}
else
{
    $fullInstance = $env:COMPUTERNAME + "\" + $InstanceName   # In case of named instance
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT Service\AzureWLBackupPluginSvc', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT AUTHORITY\SYSTEM', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
```

## <a name="next-steps"></a>Következő lépések

* [Tudnivalók a biztonsági](backup-sql-server-database-azure-vms.md) SQL Server biztonsági mentéseiről.
* [Tudnivalók a](restore-sql-database-azure-vm.md) biztonsági mentések biztonsági SQL Server visszaállításáról.
* [Tudnivalók a biztonsági](manage-monitor-sql-database-backup.md) SQL Server kezeléséről.
