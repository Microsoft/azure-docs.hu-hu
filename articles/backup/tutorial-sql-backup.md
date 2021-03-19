---
title: Oktatóanyag – SQL Server-adatbázisok biztonsági mentése az Azure-ba
description: Az oktatóanyag segítségével megtanulhatja, hogyan készíthet biztonsági mentést egy Azure-beli virtuális gépen futó SQL Server-adatbázisról egy Azure Backup helyreállítási tárba.
ms.topic: tutorial
ms.date: 06/18/2019
ms.openlocfilehash: 17a8472da2595c08cb198baaf853faf110a619fa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88612452"
---
# <a name="back-up-a-sql-server-database-in-an-azure-vm"></a>SQL Server-adatbázis biztonsági mentése egy Azure-beli virtuális gépen

Az oktatóanyagból megtudhatja, hogyan készíthet biztonsági mentést egy Azure-beli virtuális gépen futó SQL Server-adatbázisról egy Azure Backup helyreállítási tárba. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> * Tároló létrehozása és konfigurálása.
> * Adatbázisok felderítése és a biztonsági mentések beállítása.
> * Automatikus védelem beállítása adatbázisokhoz.
> * Igény szerinti biztonsági mentések futtatása.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt biztonsági mentést készítene az SQL Server-adatbázisáról, ellenőrizze a következő feltételek teljesülését:

1. Azonosítson vagy [hozzon létre](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) egy helyreállítási tárat ugyanabban a régióban vagy ugyanazon a helyszínen, ahol az SQL Server-példányt tároló virtuális gép található.
2. [Ellenőrizze a virtuális gép azon engedélyeit](backup-azure-sql-database.md#set-vm-permissions), amelyek az SQL-adatbázisok biztonsági mentéséhez szükségesek.
3. Ellenőrizze, hogy a virtuális gépnek van-e [hálózati kapcsolata](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
4. Ellenőrizze, hogy az SQL Server-adatbázisok az Azure Backup [elnevezési irányelveinek](#verify-database-naming-guidelines-for-azure-backup) megfelelő nevet használnak-e.
5. Ellenőrizze, hogy nincsenek-e érvényben egyéb engedélyezett biztonsági mentési megoldások az adatbázisnál. A forgatókönyv előkészítése előtt tiltson le minden egyéb SQL Serveres biztonsági mentést. A virtuális gépen futó SQL Server-adatbázis Azure Backupja mellett ütközések nélkül engedélyezheti az Azure-beli virtuális gép Azure Backupját is.

### <a name="establish-network-connectivity"></a>Hálózati kapcsolat létrehozása

Az SQL Servert futtató virtuális gépnek minden művelethez csatlakoznia kell a nyilvános Azure IP-címekhez. A virtuális gépek műveletei (adatbázis-felderítés, biztonsági mentések konfigurálása, biztonsági mentések ütemezése, helyreállítási pontok visszaállítása stb.) meghiúsulnak a nyilvános IP-címekkel fennálló kapcsolat nélkül. Hozza létre a kapcsolatot az alábbi lehetőségek egyikével:

* **Engedélyezze az Azure-adatközpont IP-címtartományait**: Engedélyezze a letöltés [IP-címtartományait](https://www.microsoft.com/download/details.aspx?id=41653). A hálózati biztonsági csoport (NSG) eléréséhez használja a **Set-AzureNetworkSecurityRule** parancsmagot.
* **Helyezzen üzembe egy HTTP-proxykiszolgálót a forgalom irányítására**: Ha egy Azure-beli virtuális gépen SQL Server-adatbázisról készít biztonsági mentést, a virtuális gépen futó biztonsági mentési bővítmény a HTTPS API-k használatával küldi el a felügyeleti parancsokat az Azure Backup szolgáltatásba, illetve az adatokat az Azure Storage-ba. A biztonsági mentési bővítmény is Azure Active Directoryt (Azure AD) használ a hitelesítéshez. Irányítsa a biztonsági mentési bővítmény a három szolgáltatáshoz kapcsolódó forgalmát a HTTP-proxyn keresztül. A bővítmények az egyedüli komponensek, amelyeket a nyilvános internethez való hozzáférésre konfiguráltak.

Minden beállításnak megvannak a maga előnyei és hátrányai

**Beállítás** | **Előnyök** | **Hátrányok**
--- | --- | ---
IP-címtartományok engedélyezése | Nincs további költség. | Bonyolult a kezelésük, mivel az IP-címtartományok idővel változnak. <br/><br/> Hozzáférést biztosít a teljes Azure-hoz, nem csak az Azure Storage-hez.
HTTP-proxy használata   | Lehetőség van a tároló URL-címeinek pontos szabályozására a proxyban. <br/><br/> Egyetlen internet-hozzáférési pont a virtuális gépekhez. <br/><br/> Nincs kitéve az Azure IP-címek változásának. | A proxyszoftverrel ellátott virtuális gép futtatása többletköltséghez vezet.

### <a name="set-vm-permissions"></a>Virtuális gépek engedélyeinek beállítása

Az Azure Backup számos dolgot hajt végre az SQL Server-adatbázis biztonsági mentésének konfigurálásakor:

* Hozzáadja az **AzureBackupWindowsWorkload** bővítményt.
* A virtuális gépen található adatbázisok felderítésére az Azure Backup létrehozza az **NT SERVICE\AzureWLBackupPluginSvc** fiókot. A fiókot biztonsági mentésre és visszaállításra használják, és SQL-rendszergazdai engedélyekre van szüksége.
* Az Azure Backup felhasználja az **NT AUTHORITY\SYSTEM**-fiókot az adatbázis felderítéséhez és lekérdezéséhez, így ennek a fióknak nyilvános bejelentkezésnek kell lennie az SQL-ben.

Ha nem az Azure Marketplace-en hozta létre az SQL Servert futtató virtuális gépet, akkor a következő hibaüzenetet kaphatja: **UserErrorSQLNoSysadminMembership**. Ha ez előfordul, akkor [kövesse az alábbi utasításokat](backup-azure-sql-database.md#set-vm-permissions).

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Ellenőrizze az Azure Backup elnevezési irányelveit

Az adatbázisok elnevezésekor kerülje az alábbiakat:

* Szóköz első vagy utolsó karakterként
* „!” zárókarakter
* Szögletes zárójel „]”
* „F:\” kezdetű adatbázisnevek

Aliasokat használunk az Azure-tábla nem támogatott karaktereinek használata esetén, de érdemes elkerülni a használatukat. [További információ](/rest/api/storageservices/understanding-the-table-service-data-model).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>SQL Server-adatbázisok

A virtuális gépen futó adatbázisok felderítése.

1. Az [Azure Portalon](https://portal.azure.com) nyissa meg azt a helyreállítási tárat, amelyet az adatbázis biztonsági mentésekor használ.

2. A **helyreállítási tárak** irányítópultján válassza a **Biztonsági mentés** lehetőséget.

   ![A Biztonsági mentés célja menüben válassza a Biztonsági mentés lehetőséget](./media/backup-azure-sql-database/open-backup-menu.png)

3. A **Biztonsági mentés célja** menüben a **Hol futnak az alkalmazások és szolgáltatások?** pontnál válassza az **Azure** lehetőséget (az alapértelmezettet).

4. A **Miről szeretne biztonsági másolatot készíteni?** pontnál válassza az **Azure-beli virtuális gépen futtatott SQL Server** elemet.

    ![Válassza ki az Azure-beli virtuális gépen futtatott SQL Servert a biztonsági mentéshez](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. A **Biztonsági mentés célja** > **A virtuális gépeken lévő adatbázisok felderítése** pontban válassza a **Felderítés elindítása** lehetőséget az előfizetésben található nem védett virtuális gépek kereséséhez. Ennek a lépésnek az elvégzése az előfizetésben található nem védett virtuális gépek számának függvényében eltarthat egy darabig.

   * A nem védett virtuális gépek a felderítést követően megjelennek a listában név és erőforráscsoport szerint.
   * Ha egy várt virtuális gép nem szerepel a listában, ellenőrizze, hogy készítettek-e már róla biztonsági mentést a tárolóban.
   * Van lehetőség ugyanazon név használatára több virtuális gép esetén, de akkor külön erőforráscsoportba tartoznak majd.

     ![A rendszer felfüggeszti a biztonsági mentést a virtuális gépeken lévő adatbázisok keresése közben](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. A virtuális gépek listájában válassza ki az SQL Server-adatbázist futtató virtuális gépet, majd az **Adatbázisok felderítése** parancsot.

7. Az **Értesítési területen** nyomon követheti az adatbázis felderítését. A virtuális gépen található adatbázisok számától függően a feladat végrehajtása eltarthat egy darabig. Amikor a kiválasztott adatbázisok felderítése megtörtént, megjelenik egy sikerességet jelző üzenet.

    ![Sikeres üzembe helyezést jelző üzenet](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Az Azure Backup felderíti a virtuális gépen futtatott összes SQL Server-adatbázist. Felderítés közben a következő történik a háttérben:

    * Az Azure Backup regisztrálja a virtuális gépet a tárolóban a számítási feladat biztonsági mentéséhez. A regisztrált virtuális gépen található adatbázisok biztonsági mentését csak ezen a tárolón lehet létrehozni.
    * Az Azure Backup feltelepíti a virtuális gépre az **AzureBackupWindowsWorkload** bővítményt. Az SQL-adatbázisra nincs ügynök telepítve.
    * A virtuális gépen az Azure Backup létrehozza a következő szolgáltatásfiókot: **NT Service\AzureWLBackupPluginSvc**.
      * Minden biztonsági mentési és visszaállítási művelet a szolgáltatásfiókot használja.
      * Az **NT Service\AzureWLBackupPluginSvc** szolgáltatásfióknak SQL-rendszergazdai engedélyekre van szüksége. Az Azure Marketplace-en létrehozott minden SQL Servert futtató virtuális gépre előre feltelepítették az **SqlIaaSExtension** bővítményt. Az **AzureBackupWindowsWorkload** bővítmény az **SQLIaaSExtension** bővítményt használja a szükséges engedélyek automatikus beszerzéséhez.
    * Ha nem a piactérről hozta létre virtuális gépet, akkor a virtuális gépre nincs telepítve az **SqlIaaSExtension**, és a felderítési művelet meghiúsul a következő hibaüzenettel: **UserErrorSQLNoSysAdminMembership**. A hiba megoldásához kövesse az [utasításokat](backup-azure-sql-database.md#set-vm-permissions).

        ![Virtuális gép és adatbázis kiválasztása](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása  

Konfigurálja a biztonsági mentést az alábbiak szerint:

1. A **Biztonsági mentés célja** pontban válassza ki a **Biztonsági mentés konfigurálása** lehetőséget.

   ![Biztonsági mentés konfigurálásának kiválasztása](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Válassza a **Biztonsági mentés konfigurálása** pontot, és megjelenik az **Elemek kijelölése biztonsági mentéshez** panel. Ez megjeleníti a regisztrált rendelkezésreállási csoportok és különálló SQL Serverek listáját. Bontsa ki a sor bal oldalán található sornyilat, hogy megtekintse az összes nem védett adatbázist a példányban vagy Always on AG-ben.  

    ![A különálló adatbázisokkal rendelkező SQL Server-példányok megjelenítése](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Válassza ki az összes védelem alá helyezni kívánt adatbázist, majd az **OK** lehetőséget.

   ![Az adatbázis védelme](./media/backup-azure-sql-database/select-database-to-protect.png)

   A biztonsági mentési terhelések optimalizálásához az Azure Backup az egy biztonsági mentési feladatban lévő adatbázisok maximális számát 50-re állítja.

     * Azt is megteheti, hogy a teljes példányon vagy Always On rendelkezésreállási csoporton engedélyezi az automatikus védelmet úgy, hogy kiválasztja az **AUTOMATIKUS VÉDELEM** oszlop megfelelő legördülő menüjében a **BE** lehetőséget. Az automatikus védelmi funkció nemcsak engedélyezi a védelmet az összes létező adatbázison, hanem automatikusan védi az adott példányhoz vagy a rendelkezésreállási csoportba felvenni kívánt új adatbázisokat is.  

4. Kattintson az **OK** gombra a **Biztonsági mentési szabályzat** panel megnyitásához.

    ![Az Always On rendelkezésreállási csoport automatikus védelmének engedélyezése](./media/backup-azure-sql-database/enable-auto-protection.png)

5. A **Biztonsági mentési szabályzat kiválasztása** pontban válasszon ki egy szabályzatot, majd kattintson az **OK** gombra.

   * Válassza ki az alábbi alapértelmezett szabályzatot: HourlyLogBackup.
   * Válasszon ki egy, korábban az SQL-hez létrehozott létező biztonsági mentési szabályzatot.
   * Hozzon létre egy új szabályzatot az RPO és a megőrzési időtartam alapján.

     ![Biztonsági mentési szabályzat kiválasztása](./media/backup-azure-sql-database/select-backup-policy.png)

6. A **Biztonsági mentés** menüben válassza ki a **Biztonsági mentés engedélyezése** lehetőséget.

    ![A kiválasztott biztonsági mentési szabályzat engedélyezése](./media/backup-azure-sql-database/enable-backup-button.png)

7. A konfigurációs folyamatot a portál **Értesítési területén** követheti nyomon.

    ![Értesítési terület](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Biztonsági mentési szabályzat létrehozása

A biztonsági mentési szabályzat meghatározza, hogy mikor készüljön biztonsági mentés, és mennyi ideig őrizze meg a rendszer.

* A szabályzat a tárolószinten jön létre.
* Több tároló is használhatja ugyanazt a biztonsági mentési szabályzatot, de a biztonsági mentési szabályzatot alkalmazni kell minden egyes tárolóra.
* Amikor létrehoz egy biztonsági mentési szabályzatot, a napi teljes biztonsági mentés az alapértelmezett beállítás.
* Hozzáadhat egy különbözeti biztonsági mentést is, de csak akkor, ha a teljes biztonsági mentés hetente történik.
* [Tudnivalók](backup-architecture.md#sql-server-backup-types) a biztonsági mentési szabályzatok különböző típusairól.

Biztonsági mentési szabályzat létrehozása:

1. A tárolóban válassza a **Biztonsági mentési szabályzatok** > **Hozzáadás** lehetőséget.
2. A **Hozzáadás** menüben válassza ki az **Azure-beli virtuális gépen futtatott SQL Server** lehetőséget a szabályzattípus meghatározásához.

   ![Az új biztonsági mentési szabályzat szabályzattípusának kiválasztása](./media/backup-azure-sql-database/policy-type-details.png)

3. A **Szabályzat neve** lehetőségnél adja meg az új szabályzat nevét.
4. A **Teljes biztonsági mentés** pontban válassza ki a **Biztonsági mentés gyakorisága**, majd a **Naponta** vagy **Hetente** lehetőséget.

   * A **Naponta** beállítással válassza ki a biztonsági mentési feladat kezdetét jelző órát és időzónát.
   * Teljes biztonsági mentést kell futtatnia, mivel nem lehet kikapcsolni a **Teljes biztonsági mentés** lehetőséget.
   * A **Teljes biztonsági mentés** kiválasztásával megtekintheti a szabályzatot.
   * Nem hozhat létre különbözeti biztonsági mentéseket a napi rendszerességű teljes biztonsági mentések esetén.
   * A **Hetente** beállítással válassza ki a biztonsági mentési feladat kezdetét jelző napot, órát és időzónát.

     ![Új biztonsági mentési szabályzat mezői](./media/backup-azure-sql-database/full-backup-policy.png)  

5. A **Megőrzési időtartam** esetében alapértelmezés szerint minden lehetőség ki van választva. Törölje azokat a nem kívánt megőrzési tartománykorlátokat, amelyeket nem szeretne használni, és állítsa be azokat az intervallumokat, amelyeket igen.

    * Bármely biztonsági mentés esetén (teljes, különbözeti, napló) a minimális megőrzési periódus hét nap.
    * A rendszer a helyreállítási pontokat a megőrzési időtartamuk alapján jelöli megőrzésre. Ha például napi rendszerességű teljes biztonsági mentést választ, a rendszer naponta csak egy teljes biztonsági mentést indít el.
    * Egy adott nap biztonsági mentését a rendszer a heti megőrzési időtartam és a heti megőrzési beállítás alapján jelöli meg és őrzi meg.
    * A havi és éves megőrzési időtartamok hasonló módon viselkednek.

   ![Megőrzési időtartamok intervallumbeállításai](./media/backup-azure-sql-database/retention-range-interval.png)

6. A **Teljes biztonsági mentési szabályzat** menüben kattintson az **OK** gombra a beállítások elfogadásához.
7. Különbözeti biztonsági mentési szabályzat hozzáadásához válassza ki a **Különbözeti biztonsági mentés** lehetőséget.

   ![A megőrzési időtartam intervallumbeállításai](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Nyissa meg a különbözeti biztonsági mentési szabályzat menüt](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. A **Különbözeti biztonsági mentési szabályzat** pontban válassza az **Engedélyezés** lehetőséget, hogy megnyissa a gyakorisági és megőrzési beállításokat.

    * Legfeljebb napi egy különbözeti biztonsági mentést kezdeményezhet.
    * A különbözeti biztonsági mentéseket legfeljebb 180 napig lehet megőrizni. Ha hosszabb megőrzésre van szüksége, akkor teljes biztonsági mentést kell használnia.

9. Kattintson az **OK** gombra, hogy mentse a szabályzatot, és visszatérjen a fő **Biztonsági mentési szabályzat** menübe.

10. Tranzakciós napló biztonsági mentési szabályzatának hozzáadásához válassza a **Napló biztonsági mentése** lehetőséget.
11. A **Napló biztonsági mentése** pontban válassza az **Engedélyezés** lehetőséget, majd állítsa be a gyakorisági és megőrzési beállításokat. A naplók biztonsági mentései akár 15 percenként is bekövetkezhetnek, és akár 35 napig is megőrizhetők.
12. Kattintson az **OK** gombra, hogy mentse a szabályzatot, és visszatérjen a fő **Biztonsági mentési szabályzat** menübe.

    ![Naplók biztonsági mentési szabályzatának szerkesztése](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. A **Biztonsági mentési szabályzat** menüben válassza ki, hogy engedélyezi-e az **SQL biztonsági mentés tömörítése** lehetőséget.
    * A tömörítés alapértelmezés szerint le van tiltva.
    * Az Azure Backup a háttérben natív SQL biztonsági mentési tömörítést használ.

14. Ha befejezte a biztonsági mentési szabályzat szerkesztését, kattintson az **OK** gombra.

## <a name="run-an-on-demand-backup"></a>Igény szerinti biztonsági mentések futtatása

1. A helyreállítási tárban válassza ki a Biztonsági mentés elemeit.
2. Válassza az „SQL Azure-beli virtuális gépen” lehetőséget.
3. Kattintson a jobb gombbal az adatbázisra, majd válassza ki a „Biztonsági mentés indítása” lehetőséget.
4. Válassza ki a biztonsági mentés típusát (teljes, különbözeti, napló vagy csak teljes másolat), majd a tömörítést (engedélyezés vagy letiltás)
5. Kattintson az OK gombra a biztonsági mentés megkezdéséhez.
6. Ha belép a helyreállítási tárba, és kiválasztja a „Biztonsági mentési feladatok” elemet, figyelemmel kísérheti a biztonsági mentési feladatot.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket végezte el az Azure Portalon:

> [!div class="checklist"]
>
> * Tároló létrehozása és konfigurálása.
> * Adatbázisok felderítése és a biztonsági mentések beállítása.
> * Automatikus védelem beállítása adatbázisokhoz.
> * Igény szerinti biztonsági mentések futtatása.

Folytassa a következő oktatóanyaggal, ha tudni szeretné, hogyan lehet lemezről visszaállítani Azure-beli virtuális gépeket.

> [!div class="nextstepaction"]
> [SQL Server-adatbázisok visszaállítása Azure-beli virtuális gépeken](./restore-sql-database-azure-vm.md)
