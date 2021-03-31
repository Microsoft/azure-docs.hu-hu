---
title: SQL Server adatbázisok biztonsági mentése az Azure-ba
description: Ez a cikk a SQL Server Azure-ba történő biztonsági mentését ismerteti. A cikk a SQL Server helyreállítást is ismerteti.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 510d9637031928e31abaa5f82a5bf58c6ef44719
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91316841"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Információk az Azure-beli virtuális gépeken futó SQL Server Backupról

A [Azure Backup](backup-overview.md) stream-alapú, speciális megoldást kínál az Azure-beli virtuális gépeken futó SQL Server biztonsági mentésére. Ez a megoldás a Azure Backup a nulla infrastruktúra biztonsági mentésével, a hosszú távú adatmegőrzéssel és a központi felügyelettel kapcsolatos előnyökkel illeszkedik. Emellett a következő előnyöket nyújtja kifejezetten a SQL Server számára:

1. Az összes biztonsági mentési típust támogató, munkaterhelés-alapú biztonsági másolatok – teljes, különbözeti és napló
2. 15 perces RPO (helyreállítási pont célkitűzés) gyakori naplók biztonsági másolatokkal
3. Időponthoz képesti helyreállítás másodpercenként
4. Egyedi adatbázis-szintű biztonsági mentés és visszaállítás

A jelenleg támogatott biztonsági mentési és visszaállítási forgatókönyvek megtekintéséhez tekintse meg a [támogatási mátrixot](sql-support-matrix.md#scenario-support).

## <a name="backup-process"></a>A biztonsági mentés folyamata

Ez a megoldás kihasználja az SQL natív API-kat az SQL-adatbázisok biztonsági mentésének elvégzéséhez.

* Miután megadta a védelemmel ellátni kívánt SQL Server VMt, és lekérdezi a benne lévő adatbázisokat, Azure Backup szolgáltatás a számítási feladatok biztonsági mentési bővítményét a virtuális gépen telepíti a `AzureBackupWindowsWorkload` fájlnévkiterjesztés alapján.
* Ez a bővítmény egy koordinátorból és egy SQL-beépülő modulból áll. Míg a koordinátor felelős a munkafolyamatok különböző műveletekhez, például a biztonsági mentés konfigurálásához, a biztonsági mentéshez és a visszaállításhoz, a beépülő modul felelős a tényleges adatforgalomért.
* Ahhoz, hogy fel tudja deríteni a virtuális gép adatbázisait, Azure Backup létrehozza a fiókot `NT SERVICE\AzureWLBackupPluginSvc` . Ez a fiók használható a biztonsági mentéshez és a visszaállításhoz, és SQL sysadmin engedélyekre van szükség. A `NT SERVICE\AzureWLBackupPluginSvc` fiók egy [virtuális szolgáltatásfiók](/windows/security/identity-protection/access-control/service-accounts#virtual-accounts), ezért nincs szükség jelszavas felügyeletre. Azure Backup az `NT AUTHORITY\SYSTEM` adatbázis-felderítési/lekérdezési fiókot használja, így ennek a fióknak nyilvános bejelentkezésre van szüksége az SQL-ben. Ha nem az Azure Marketplace-en hozta létre az SQL Servert futtató virtuális gépet, akkor a következő hibaüzenetet kaphatja: **UserErrorSQLNoSysadminMembership**. Ha ez előfordul, akkor [kövesse az alábbi utasításokat](#set-vm-permissions).
* Miután elindította a védelem konfigurálását a kiválasztott adatbázisokon, a Backup szolgáltatás beállítja a koordinátort a biztonsági mentési ütemtervekkel és egyéb házirend-adatokkal, amelyeket a bővítmény a virtuális gépen helyileg gyorsítótáraz.
* Az ütemezett időpontban a koordinátor kommunikál a beépülő modullal, és elindítja a biztonsági mentési adatok továbbítását az SQL Serverről a VDI használatával.  
* A beépülő modul közvetlenül az Recovery Services-tárolóba küldi az adatokat, így nincs szükség átmeneti helyre. Az adattitkosítás és a Azure Backup szolgáltatás tárolja a Storage-fiókokban.
* Az adatátvitel befejezésekor a koordinátor ellenőrzi a véglegesítést a biztonsági mentési szolgáltatással.

  ![SQL biztonsági mentési architektúra](./media/backup-azure-sql-database/azure-backup-sql-overview.png)

## <a name="before-you-start"></a>Előkészületek

Mielőtt elkezdené, ellenőrizze a következő követelményeket:

1. Győződjön meg arról, hogy rendelkezik az Azure-ban futó SQL Server-példánnyal. [Gyorsan létrehozhat egy SQL Server példányt](../azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart.md) a piactéren.
2. Tekintse át a [szolgáltatással kapcsolatos szempontokat](sql-support-matrix.md#feature-considerations-and-limitations) és a [forgatókönyvek támogatását](sql-support-matrix.md#scenario-support)
3. [Tekintse át](faq-backup-sql-server.md) a forgatókönyvre vonatkozó gyakori kérdéseket.

## <a name="set-vm-permissions"></a>Virtuális gépek engedélyeinek beállítása

  Ha a felderítést SQL Serveron futtatja, a Azure Backup a következő műveleteket végzi el:

* Hozzáadja az AzureBackupWindowsWorkload bővítményt.
* Egy NT SERVICE\AzureWLBackupPluginSvc-fiókot hoz létre a virtuális gépen lévő adatbázisok felderítéséhez. Ez a fiók biztonsági mentésre és helyreállításra szolgál, és SQL sysadmin engedélyeket igényel.
* Egy virtuális gépen futó adatbázisok felfedése, Azure Backup az NT AUTHORITY\SYSTEM fiókot használja. Ennek a fióknak nyilvános bejelentkezéssel kell rendelkeznie az SQL-ben.

Ha nem hozta létre a SQL Server VM az Azure Marketplace-en, vagy ha SQL 2008-es vagy 2008 R2-es verzióval rendelkezik, előfordulhat, hogy **UserErrorSQLNoSysadminMembership** hibaüzenetet kap.

A Windows 2008 R2 rendszeren futó **SQL 2008** és **2008 R2** esetén az engedélyek megadása [itt](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2)található.

Az összes többi verzió esetében javítsa ki az engedélyeket a következő lépésekkel:

  1. SQL Server sysadmin engedélyekkel rendelkező fiókkal jelentkezhet be SQL Server Management Studio (SSMS) szolgáltatásba. Ha speciális engedélyekre van szüksége, a Windows-hitelesítésnek működnie kell.
  2. A SQL Server nyissa meg a **Biztonság/bejelentkezések** mappát.

      ![A fiókok megjelenítéséhez nyissa meg a biztonság/bejelentkezések mappát](./media/backup-azure-sql-database/security-login-list.png)

  3. Kattintson a jobb gombbal a **bejelentkezések** mappára, és válassza az **új bejelentkezés** lehetőséget. A **Bejelentkezés – új** területen válassza a **Keresés** lehetőséget.

      ![A bejelentkezés – új párbeszédpanelen válassza a keresés lehetőséget.](./media/backup-azure-sql-database/new-login-search.png)

  4. A virtuális gép regisztrálása és az SQL-felderítési fázis során létrejött a Windows Virtual Service Account **NT SERVICE\AzureWLBackupPluginSvc** . Adja meg a fiók nevét az **adja meg a kijelölendő objektum nevét mezőben** látható módon. Válassza **a Névellenőrzés lehetőséget** a név feloldásához. Válassza az **OK** lehetőséget.

      ![Az ismeretlen szolgáltatásnév feloldásához válassza a Névellenőrzés lehetőséget](./media/backup-azure-sql-database/check-name.png)

  5. A **kiszolgálói szerepkörök** területen ellenőrizze, hogy ki van-e választva a **sysadmin (rendszergazda** ) szerepkör. Válassza az **OK** lehetőséget. A szükséges engedélyeknek már léteznie kell.

      ![Győződjön meg arról, hogy a sysadmin kiszolgálói szerepkör van kiválasztva](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Most társítsa az adatbázist a Recovery Services-tárolóhoz. A Azure Portal a **védett kiszolgálók** listában kattintson a jobb gombbal arra a kiszolgálóra, amely hibás állapotban van > az adatbázisok újbóli **felderítése** elemre.

      ![Ellenőrizze, hogy a kiszolgáló rendelkezik-e megfelelő engedélyekkel](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Az **értesítések** területén lévő előrehaladás ellenőrzéséhez. Ha a kiválasztott adatbázisok találhatók, megjelenik egy sikert jelző üzenet.

      ![Sikeres üzembe helyezést jelző üzenet](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> Ha a SQL Server a SQL Server több példánya is telepítve van, akkor minden SQL-példányhoz rendszergazdai jogosultságot kell hozzáadnia az **NT Service\AzureWLBackupPluginSvc** -fiókhoz.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>SQL sysadmin-engedélyek megadása az SQL 2008 és az SQL 2008 R2 rendszerhez

Adja hozzá az **NT AUTHORITY\SYSTEM** és **NT Service\AzureWLBackupPluginSvc** -bejelentkezéseket az SQL Server-példányhoz:

1. Lépjen a SQL Server példányra az Object Explorerben.
2. Lépjen a biztonság > bejelentkezések lehetőségre
3. Kattintson a jobb gombbal a bejelentkezések elemre, és válassza az *új bejelentkezés... lehetőséget.*

    ![Új bejelentkezés a SSMS használatával](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Nyissa meg az általános lapot, és adja meg a bejelentkezési név **NT AUTHORITY\SYSTEM** .

    ![A SSMS bejelentkezési neve](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Lépjen a *kiszolgálói szerepkörök* elemre, és válassza ki a *nyilvános* és a *sysadmin* szerepkört.

    ![Szerepkörök kiválasztása a SSMS-ben](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Ugrás az *állapotra*. Engedély *megadása* az adatbázismotor és a bejelentkezés *engedélyezettként* való bekapcsolásához.

    ![Engedélyek megadása a SSMS-ben](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Kattintson az OK gombra.
8. Ismételje meg ugyanezeket a lépéseket (1-7 fent) a SQL Server-példányhoz tartozó NT Service\AzureWLBackupPluginSvc-bejelentkezés hozzáadásához. Ha a bejelentkezés már létezik, győződjön meg róla, hogy a rendszergazda kiszolgálói szerepkörrel rendelkezik, és az állapota területen a jogosultsággal rendelkezik az adatbázismotor és a bejelentkezés engedélyezettként való bekapcsolásához.
9. Az engedélyek megadása után **Fedezze fel újra az adatbázisok** a portálon: tár biztonsági mentési infrastruktúrájának számítási feladatait az **->** **->** Azure virtuális gépen:

    ![A Azure Portalban lévő adatbázisok újbóli felderítése](media/backup-azure-sql-database/sql-rediscover-dbs.png)

Azt is megteheti, hogy a következő PowerShell-parancsok rendszergazdai módban történő futtatásával automatizálja az engedélyeket. A példány neve alapértelmezés szerint az MSSQLSERVER értékre van állítva. Módosítsa a példány neve argumentumot a parancsfájlban, ha szükséges:

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

* [Tudnivalók SQL Server adatbázisok biztonsági](backup-sql-server-database-azure-vms.md) mentéséről.
* [Tudnivalók a](restore-sql-database-azure-vm.md) SQL Server adatbázisok biztonsági másolatának visszaállításáról.
* [Tudnivalók a](manage-monitor-sql-database-backup.md) SQL Server adatbázisok biztonsági másolatának kezeléséről.
