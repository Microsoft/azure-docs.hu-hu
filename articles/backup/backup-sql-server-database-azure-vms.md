---
title: Több SQL Servert futtató virtuális gép biztonsági mentése a tárolóból
description: Ebből a cikkből megtudhatja, hogyan készíthet biztonsági mentést az Azure-beli virtuális gépeken található SQL Server-adatbázisokról a Recovery Services-tároló Azure Backupával
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 798dc81012ad968c3ecc287717240513a08a1349
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "105567210"
---
# <a name="back-up-multiple-sql-server-vms-from-the-recovery-services-vault"></a>Több SQL Server virtuális gép biztonsági mentése a Recovery Services-tárból

SQL Server adatbázisok olyan kritikus fontosságú számítási feladatok, amelyek alacsony helyreállítási célt (RPO) és hosszú távú adatmegőrzést igényelnek. Az Azure Virtual Machines (VM) rendszeren futó SQL Server adatbázisok biztonsági mentését [Azure Backup](backup-overview.md)használatával végezheti el.

Ez a cikk bemutatja, hogyan készíthet biztonsági mentést egy Azure-beli virtuális gépen futó SQL Server-adatbázisról egy Azure Backup Recovery Services-tárolóra.

Ebből a cikkből megtudhatja, hogyan:

> [!div class="checklist"]
>
> * Tároló létrehozása és konfigurálása.
> * Adatbázisok felderítése és biztonsági mentések beállítása.
> * Automatikus védelem beállítása adatbázisokhoz.

>[!NOTE]
>Az Azure-beli virtuális gépeken futó **SQL Server-kiszolgáló és az Azure** -beli virtuális gépekhez készült SAP HANA Soft delete már előzetes verzióban érhető el.<br>
>Az előzetes verzióra való feliratkozáshoz írjon nekünk a következőt: AskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Előfeltételek

SQL Server adatbázis biztonsági mentése előtt tekintse meg a következő feltételeket:

1. Azonosítson vagy hozzon létre egy [Recovery Services](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) tárolót ugyanabban a régióban és előfizetésben, mint a SQL Server példányt futtató virtuális gép.
1. Ellenőrizze, hogy a virtuális gép rendelkezik-e [hálózati kapcsolattal](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
1. Győződjön meg arról, hogy a SQL Server adatbázisok a [Azure Backup adatbázis-elnevezési irányelveit](#database-naming-guidelines-for-azure-backup)követik.
1. Győződjön meg arról, hogy a SQL Server VM nevének és az erőforráscsoport nevének együttes hossza nem haladja meg Azure Resource Manager virtuális gépek esetén a 84 karaktert (vagy a klasszikus virtuális gépek 77 karaktereit). Ez a korlátozás azért van, mert egyes karaktereket a szolgáltatás foglal le.
1. Győződjön meg arról, hogy nincs más biztonsági mentési megoldás, amely engedélyezve van az adatbázishoz. Az adatbázis biztonsági mentése előtt tiltsa le az összes többi SQL Server biztonsági mentést.
1. SQL Server 2008 R2 vagy SQL Server 2012 használata esetén előfordulhat, hogy az [itt](https://support.microsoft.com/help/2697983/kb2697983-fix-an-incorrect-value-is-stored-in-the-time-zone-column-of)leírtak szerint futtathatja az időzóna-problémát a biztonsági mentéshez. Győződjön meg arról, hogy a legújabb összesítő frissítéseket választotta, hogy elkerülje a fent ismertetett időzóna kapcsolatos problémát. Ha az Azure-beli virtuális gépen lévő SQL Server példány frissítéseit nem lehet megvalósítani, tiltsa le a virtuális gép időzónájának nyári időszámítási idejét (DST).

> [!NOTE]
> Engedélyezheti az Azure-beli virtuális gépek Azure Backupét, valamint a virtuális gépen futó SQL Server-adatbázis ütközés nélküli működését is.

### <a name="establish-network-connectivity"></a>Hálózati kapcsolat létrehozása

A SQL Server VM az összes művelethez kapcsolódnia kell a Azure Backup szolgáltatáshoz, az Azure Storage-hoz és a Azure Active Directoryhoz. Ezt privát végpontok használatával vagy a szükséges nyilvános IP-címekhez vagy teljes tartománynevek elérésének engedélyezésével lehet elérni. A szükséges Azure-szolgáltatásokhoz való megfelelő kapcsolódás nem teszi lehetővé az adatbázis-felderítést, a biztonsági mentés konfigurálását, a biztonsági másolatok készítését és az adatok visszaállítását.

A következő táblázat a kapcsolatok létrehozásához használható különböző alternatívákat sorolja fel:

| **Beállítás**                        | **Előnyök**                                               | **Hátrányok**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Privát végpontok                 | Biztonsági másolatok engedélyezése privát IP-címeken a virtuális hálózaton belül  <br><br>   Részletes vezérlés biztosítása a hálózat és a tároló oldalán | Standard magánhálózati végponti [költségek](https://azure.microsoft.com/pricing/details/private-link/) |
| NSG szolgáltatás címkéi                  | A tartomány módosításainak könnyebb kezelése automatikusan történik   <br><br>   Nincs további költség | Csak NSG használható  <br><br>    Hozzáférést biztosít a teljes szolgáltatáshoz |
| Azure Firewall FQDN-Címkék          | Könnyebb kezelhetőség, mert a szükséges teljes tartománynevek automatikusan felügyelhetők | Csak Azure Firewall használható                         |
| Hozzáférés engedélyezése a szolgáltatás teljes tartománynevéhez/IP-címeihez | Nincs további költség   <br><br>  Együttműködik az összes hálózati biztonsági berendezéssel és tűzfallal | Szükség lehet az IP-címek vagy a teljes tartománynevek elérésére   |
| HTTP-proxy használata                 | A virtuális gépekhez való internetes hozzáférés egyetlen pontja                       | További költségek egy virtuális gép futtatásához a proxy szoftverrel         |

A fenti beállítások használatával kapcsolatos további részletekért lásd a következőt:

#### <a name="private-endpoints"></a>Privát végpontok

A privát végpontok lehetővé teszik a biztonságos kapcsolódást a virtuális hálózaton belüli kiszolgálókról a Recovery Services-tárba. A privát végpont egy IP-címet használ a tár VNET. A virtuális hálózaton belüli erőforrásai és a tároló közötti hálózati forgalom a virtuális hálózatra és a Microsoft gerinc hálózatán található privát kapcsolatra is áthalad. Ezzel kiküszöbölhető a nyilvános internetről való kitettség. További információ a Azure Backup privát végpontokról [itt](./private-endpoints.md)olvasható.

#### <a name="nsg-tags"></a>NSG Címkék

Ha hálózati biztonsági csoportokat (NSG) használ, használja a *AzureBackup* szolgáltatás címkéjét, hogy engedélyezze a kimenő hozzáférést Azure Backuphoz. A Azure Backup címkén kívül az Azure AD-hoz (*AzureActiveDirectory*) és az Azure Storage-hoz (*Storage*) hasonló [NSG szabályok](../virtual-network/network-security-groups-overview.md#service-tags) létrehozásával is engedélyeznie kell a csatlakozást a hitelesítéshez és az adatátvitelhez.  A következő lépések azt ismertetik, hogyan hozható létre szabály a Azure Backup címke számára:

1. A **minden szolgáltatás** területen lépjen a **hálózati biztonsági csoportok** elemre, és válassza ki a hálózati biztonsági csoportot.

1. A **Beállítások** területen válassza a **kimenő biztonsági szabályok** lehetőséget.

1. Válassza a **Hozzáadás** lehetőséget. Adja meg az új szabály létrehozásához szükséges összes adatot a [biztonsági szabály beállításai](../virtual-network/manage-network-security-group.md#security-rule-settings)című témakörben leírtak szerint. Győződjön meg arról, hogy a **cél** a *Service tag* és a **cél szolgáltatás címkéje** *AzureBackup* értékre van állítva.

1. Válassza a **Hozzáadás**  lehetőséget az újonnan létrehozott kimenő biztonsági szabály mentéséhez.

Hasonlóképpen NSG kimenő biztonsági szabályokat hozhat létre az Azure Storage és az Azure AD számára.

#### <a name="azure-firewall-tags"></a>Címkék Azure Firewall

Ha Azure Firewall használ, hozzon létre egy szabályt a *AzureBackup* [Azure Firewall FQDN címke](../firewall/fqdn-tags.md)használatával. Ez lehetővé teszi Azure Backup összes kimenő hozzáférését.

#### <a name="allow-access-to-service-ip-ranges"></a>Hozzáférés engedélyezése a szolgáltatás IP-tartományai számára

Ha a hozzáférési szolgáltatás IP-címeinek engedélyezését választja, tekintse meg az [itt](https://www.microsoft.com/download/confirmation.aspx?id=56519)elérhető JSON-fájl IP-tartományait. Engedélyeznie kell a hozzáférést a Azure Backup, az Azure Storage és a Azure Active Directory megfelelő IP-címekhez.

#### <a name="allow-access-to-service-fqdns"></a>Hozzáférés engedélyezése a szolgáltatás teljes tartománynevéhez

A következő teljes tartományneveket is használhatja a szükséges szolgáltatások elérésének engedélyezéséhez a kiszolgálókon:

| Szolgáltatás    | Elérni kívánt tartománynevek                             | Portok
| -------------- | ------------------------------------------------------------ | ---
| Azure Backup  | `*.backup.windowsazure.com`                             | 443
| Azure Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` | 443
| Azure AD      | Az 56-es és a 59-es szakaszban található teljes tartománynevek elérésének engedélyezése [a jelen cikk](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) alapján | Adott esetben

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>HTTP-proxykiszolgáló használata a forgalom irányításához

Ha egy Azure-beli virtuális gépen SQL Server adatbázisról készít biztonsági másolatot, a virtuális gépen futó biztonsági mentési bővítmény a HTTPS API-k használatával küldi el a felügyeleti parancsokat az Azure Storage-ba való Azure Backup és az adattároláshoz. A biztonsági mentési bővítmény az Azure AD-t is használja a hitelesítéshez. Irányítsa a biztonsági mentési bővítmény a három szolgáltatáshoz kapcsolódó forgalmát a HTTP-proxyn keresztül. A fent említett IP-címek és FQDN-k listájának használata a szükséges szolgáltatásokhoz való hozzáférés engedélyezéséhez. A hitelesített proxykiszolgálók nem támogatottak.

### <a name="database-naming-guidelines-for-azure-backup"></a>Adatbázis-elnevezési irányelvek a Azure Backup

Kerülje a következő elemek használatát az adatbázis-nevekben:

* Záró és kezdő szóközök
* Záró felkiáltójelek (!)
* Szögletes zárójelek zárása (])
* Pontosvessző: ";"
* Perjelek továbbítása

Az alias nem támogatott karakterekhez érhető el, de a rendszer elkerüli a használatát. További információt a [Table Service adatmodelljét ismertető](/rest/api/storageservices/understanding-the-table-service-data-model) témakörben talál.

>[!NOTE]
>A speciális karaktereket (például "+" vagy "&") tartalmazó adatbázisok védelmi műveletének **konfigurálása** nem támogatott. Megváltoztathatja az adatbázis nevét, vagy engedélyezheti az **automatikus védelmet**, amely sikeresen képes védelemmel ellátni ezeket az adatbázisokat.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>SQL Server-adatbázisok

Virtuális gépen futó adatbázisok felderítése:

1. Az [Azure Portalon](https://portal.azure.com) nyissa meg azt a helyreállítási tárat, amelyet az adatbázis biztonsági mentésekor használ.

2. A **Recovery Services** -tároló irányítópultján válassza a **biztonsági mentés** lehetőséget.

   ![A Biztonsági mentés célja menüben válassza a Biztonsági mentés lehetőséget](./media/backup-azure-sql-database/open-backup-menu.png)

3. A **biztonsági mentés célja** beállításnál állítsa be, hogy **hol fut a** számítási feladat? az **Azure**-ban.

4. A **Miről szeretne biztonsági másolatot készíteni?** pontnál válassza az **Azure-beli virtuális gépen futtatott SQL Server** elemet.

    ![Válassza ki az Azure-beli virtuális gépen futtatott SQL Servert a biztonsági mentéshez](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. A **Biztonsági mentés célja** > **A virtuális gépeken lévő adatbázisok felderítése** pontban válassza a **Felderítés elindítása** lehetőséget az előfizetésben található nem védett virtuális gépek kereséséhez. A keresés eltarthat egy ideig, az előfizetésben nem védett virtuális gépek számától függően.

   * A nem védett virtuális gépek a felderítést követően megjelennek a listában név és erőforráscsoport szerint.
   * Ha egy virtuális gép nem szerepel a várt módon, tekintse meg, hogy már van-e biztonsági másolat egy tárolóban.
   * Több virtuális gépnek is ugyanaz a neve, de különböző erőforráscsoportok tartoznak hozzájuk.

     ![A rendszer felfüggeszti a biztonsági mentést a virtuális gépeken lévő adatbázisok keresése közben](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. A virtuális gépek listájában válassza ki az SQL Server-adatbázist futtató virtuális gépet, majd az **Adatbázisok felderítése** parancsot.

7. Az **értesítésekben** nyomon követheti az adatbázis-felderítést. A művelethez szükséges idő a virtuális gépek adatbázisainak számától függ. Amikor a kiválasztott adatbázisok felderítése megtörtént, megjelenik egy sikerességet jelző üzenet.

    ![Sikeres üzembe helyezést jelző üzenet](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Az Azure Backup felderíti a virtuális gépen futtatott összes SQL Server-adatbázist. A felderítés során a háttérben a következő elemek történnek:

    * Azure Backup regisztrálja a virtuális gépet a tárolóban a munkaterhelés biztonsági mentéséhez. A regisztrált virtuális gépen lévő összes adatbázis biztonsági mentése csak erre a tárolóra lehetséges.
    * Az Azure Backup feltelepíti a virtuális gépre az AzureBackupWindowsWorkload bővítményt. Nincs ügynök telepítve egy SQL-adatbázisra.
    * A virtuális gépen az Azure Backup létrehozza a következő szolgáltatásfiókot: NT Service\AzureWLBackupPluginSvc.
      * Minden biztonsági mentési és visszaállítási művelet a szolgáltatásfiókot használja.
      * Az NT Service\AzureWLBackupPluginSvc használatához SQL sysadmin engedély szükséges. A piactéren létrehozott összes SQL Server virtuális gép a SqlIaaSExtension van telepítve. Az AzureBackupWindowsWorkload bővítmény az SQLIaaSExtension bővítményt használja a szükséges engedélyek automatikus beszerzéséhez.
    * Ha nem hozta létre a virtuális gépet a piactéren, vagy ha SQL 2008-es és 2008 R2-es verzióban van, előfordulhat, hogy a virtuális gép nem rendelkezik a SqlIaaSExtension, és a felderítési művelet meghiúsul, és a UserErrorSQLNoSysAdminMembership hibaüzenet jelenik meg. A probléma megoldásához kövesse a [virtuális gép engedélyeinek beállítása](backup-azure-sql-database.md#set-vm-permissions)című témakör utasításait.

        ![Virtuális gép és adatbázis kiválasztása](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása  

1. A **biztonsági mentés célja**  >  **2. lépés: a biztonsági mentés konfigurálása** lapon válassza a **biztonsági mentés konfigurálása** elemet.

   ![Biztonsági mentés konfigurálásának kiválasztása](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

1. Válassza az **erőforrások hozzáadása** lehetőséget a regisztrált rendelkezésre állási csoportok és az önálló SQL Server-példányok megjelenítéséhez.

    ![Válassza az erőforrások hozzáadása lehetőséget.](./media/backup-azure-sql-database/add-resources.png)

1. Az **elemek kiválasztása a biztonsági mentéshez** képernyőn válassza ki a sor bal oldalán lévő nyilat az adott példányban vagy always on rendelkezésre állási csoportba tartozó nem védett adatbázisok listájának kibontásához.

    ![Biztonsági mentésre kijelölt elemek kiválasztása](./media/backup-azure-sql-database/select-items-to-backup.png)

1. Válassza ki az összes védelemmel ellátni kívánt adatbázist, majd kattintson **az OK gombra**.

   ![Az adatbázis védelme](./media/backup-azure-sql-database/select-database-to-protect.png)

   A biztonsági mentési terhelések optimalizálásához az Azure Backup az egy biztonsági mentési feladatban lévő adatbázisok maximális számát 50-re állítja.

     * Több mint 50 adatbázis védelméhez több biztonsági mentést is konfigurálhat.
     * A teljes példány vagy az Always On rendelkezésre állási csoport [engedélyezéséhez](#enable-auto-protection) az **AutoProtect** legördülő listában válassza  **a** be lehetőséget, majd kattintson az **OK gombra**.

         > [!NOTE]
         > Az [automatikus védelem](#enable-auto-protection) funkció nem csak egyszerre engedélyezi a védelmet az összes meglévő adatbázison, de automatikusan védi az adott példányhoz vagy a rendelkezésre állási csoporthoz hozzáadott új adatbázisokat is.  

1. Adja meg a **biztonsági mentési szabályzatot**. Tegye a következők egyikét:

   * Válassza ki az alapértelmezett szabályzatot *HourlyLogBackup*.
   * Válasszon ki egy, korábban az SQL-hez létrehozott létező biztonsági mentési szabályzatot.
   * Hozzon létre egy új szabályzatot az RPO és a megőrzési időtartam alapján.

     ![Biztonsági mentési szabályzat kiválasztása](./media/backup-azure-sql-database/select-backup-policy.png)

1. Válassza a **biztonsági mentés engedélyezése** lehetőséget a **védelem konfigurálása** művelet elküldéséhez és a konfigurációs folyamat nyomon követéséhez a portál **értesítések** területén.

   ![A konfiguráció előrehaladásának nyomon követése](./media/backup-azure-sql-database/track-configuration-progress.png)

### <a name="create-a-backup-policy"></a>Biztonsági mentési szabályzat létrehozása

A biztonsági mentési szabályzat meghatározza, hogy mikor készüljön biztonsági mentés, és mennyi ideig őrizze meg a rendszer.

* A szabályzat a tárolószinten jön létre.
* Több tároló is használhatja ugyanazt a biztonsági mentési szabályzatot, de a biztonsági mentési szabályzatot alkalmazni kell minden egyes tárolóra.
* Amikor létrehoz egy biztonsági mentési szabályzatot, a napi teljes biztonsági mentés az alapértelmezett beállítás.
* Hozzáadhat egy különbözeti biztonsági mentést is, de csak akkor, ha a teljes biztonsági mentés hetente történik.
* Ismerje meg a [biztonsági mentési szabályzatok különböző típusait](backup-architecture.md#sql-server-backup-types).

Biztonsági mentési szabályzat létrehozása:

1. A tárolóban válassza a **Biztonsági mentési szabályzatok** > **Hozzáadás** lehetőséget.
1. A **Hozzáadás** területen válassza a SQL Server lehetőséget az **Azure-beli virtuális gépen** a szabályzat típusának meghatározásához.

   ![Az új biztonsági mentési szabályzat szabályzattípusának kiválasztása](./media/backup-azure-sql-database/policy-type-details.png)

1. A **Szabályzat neve** lehetőségnél adja meg az új szabályzat nevét.

    ![Adja meg a szabályzat nevét](./media/backup-azure-sql-database/policy-name.png)

1. Az alapértelmezett beállítások módosításához jelölje ki a megfelelő **szerkesztési** hivatkozást a **teljes biztonsági mentéshez**.

   * Válassza ki a **biztonsági mentés gyakoriságát**. Válassza a **naponta** vagy **hetente** lehetőséget.
   * A **Naponta** beállítással válassza ki a biztonsági mentési feladat kezdetét jelző órát és időzónát. Nem hozhat létre különbözeti biztonsági mentéseket a napi rendszerességű teljes biztonsági mentések esetén.

     ![Új biztonsági mentési szabályzat mezői](./media/backup-azure-sql-database/full-backup-policy.png)  

1. A **megőrzési tartomány** területen az összes beállítás alapértelmezés szerint ki van választva. Törölje a nem kívánt adatmegőrzési tartomány korlátait, majd állítsa be a használandó intervallumokat.

    * A minimális megőrzési idő bármilyen típusú biztonsági mentés esetén (teljes, különbözeti és napló) hét nap.
    * A rendszer a helyreállítási pontokat a megőrzési időtartamuk alapján jelöli megőrzésre. Ha például napi rendszerességű teljes biztonsági mentést választ, a rendszer naponta csak egy teljes biztonsági mentést indít el.
    * Egy adott nap biztonsági másolata a heti megőrzési időtartam és a heti megőrzési beállítás alapján van megjelölve és megőrizve.
    * A havi és az éves adatmegőrzési tartományok hasonló módon viselkednek.

       ![Megőrzési időtartamok intervallumbeállításai](./media/backup-azure-sql-database/retention-range-interval.png)

1. Válassza az **OK** gombot a teljes biztonsági mentések beállításának elfogadásához.
1. Az alapértelmezett beállítások módosításához válassza a **különbözeti biztonsági másolatnak** megfelelő **szerkesztési** hivatkozást.

    * A **Különbözeti biztonsági mentési szabályzat** pontban válassza az **Engedélyezés** lehetőséget, hogy megnyissa a gyakorisági és megőrzési beállításokat.
    * Naponta csak egy különbözeti biztonsági mentést lehet kiváltani. Egy különbözeti biztonsági másolat nem indítható el a teljes biztonsági mentés napján.
    * A különbözeti biztonsági mentéseket legfeljebb 180 napig lehet megőrizni.
    * A Master adatbázis nem támogatja a különbözeti biztonsági mentést.

      ![Különbözeti biztonsági mentési szabályzat](./media/backup-azure-sql-database/differential-backup-policy.png)

1. Az alapértelmezett beállítások módosításához válassza a **napló biztonsági mentésének** megfelelő **szerkesztési** hivatkozást

    * A **Napló biztonsági mentése** pontban válassza az **Engedélyezés** lehetőséget, majd állítsa be a gyakorisági és megőrzési beállításokat.
    * A naplók biztonsági mentései akár 15 percenként is előfordulhatnak, és akár 35 napig is megtartható.
    * Ha az adatbázis az [egyszerű helyreállítási modellben](/sql/relational-databases/backup-restore/recovery-models-sql-server)van, a rendszer szünetelteti az adott adatbázishoz tartozó napló biztonsági mentési ütemezését, így a rendszer nem készít biztonsági mentést.
    * Ha az adatbázis helyreállítási modellje **teljesről** **egyszerűre** változik, a rendszer a biztonsági mentéseket a helyreállítási modell változásának 24 órán belül szünetelteti. Hasonlóképpen, ha a helyreállítási modell **egyszerűről** változik, a naplók biztonsági másolatait mostantól lehet támogatni az adatbázishoz, a biztonsági mentések ütemezése a helyreállítási modell változásának 24 órán belül engedélyezve lesz.

      ![Biztonsági mentési szabályzat naplózása](./media/backup-azure-sql-database/log-backup-policy.png)

1. A **biztonsági mentési házirend** menüben válassza ki, hogy engedélyezi-e az **SQL biztonsági mentési tömörítést** , vagy sem. A beállítás alapértelmezés szerint le van tiltva. Ha engedélyezve van, SQL Server egy tömörített biztonsági mentési adatfolyamot küld a VDI-nek. A Azure Backup felülbírálja a példány szintjének alapértelmezett értékét a TÖMÖRÍTÉSi/NO_COMPRESSION záradékkal a vezérlő értékétől függően.

1. Ha befejezte a biztonsági mentési szabályzat szerkesztését, kattintson az **OK** gombra.

> [!NOTE]
> Minden naplózási biztonsági mentés az előző teljes biztonsági mentéshez van láncolva, amely helyreállítási láncot alkot. Ez a teljes biztonsági mentés a legutóbbi napló biztonsági mentésének lejárta után is megmarad. Ez azt jelentheti, hogy a teljes biztonsági mentést egy további időszakra vonatkozóan kell megőrizni, hogy az összes napló helyreállítható legyen. Tegyük fel, hogy heti teljes biztonsági mentést, napi különbözeti és 2 órás naplókat használ. Mindegyiket 30 napig őrzi meg a rendszer. A hetente megteltek azonban csak a következő teljes biztonsági mentés elérhetővé tételével, azaz 30 + 7 nap után törlődnek. A heti teljes biztonsági mentés például november 16-án történik. Az adatmegőrzési szabályzat szerint az IT-t december 16-i-ig kell megőrizni. Az utolsó napló biztonsági mentése ehhez a teljes, november 22-én a következő beütemezett megtelte előtt történik. Amíg a napló nem érhető el a DEC 22nd-ig, a november 16-i teljes nem törölhető. Így a november 16-i Full megmarad december 22-én.

## <a name="enable-auto-protection"></a>Automatikus védelem engedélyezése  

Az automatikus védelem engedélyezésével automatikusan biztonsági másolatot készíthet az összes meglévő és jövőbeli adatbázisról egy önálló SQL Server-példányra vagy egy always on rendelkezésre állási csoportba.

* Az automatikus védelemhez egyszerre kiválasztható adatbázisok száma nincs korlátozva. A felderítés általában nyolc óránként fut. Az új adatbázisokat azonban azonnal felderítheti és védetté teheti, ha manuálisan futtat egy felderítést. Ehhez válassza az **adatbázisok ismételt felderítése** lehetőséget.
* Az automatikus védelem engedélyezésekor nem lehet szelektív védelemmel ellátni vagy kizárni az adatbázisokat a védelemből egy példányon.
* Ha a példánya már tartalmaz néhány védett adatbázist, akkor az automatikus védelem bekapcsolása után is a megfelelő szabályzatok alatt maradnak. A később hozzáadott nem védett adatbázisok csak egyetlen szabályzattal rendelkeznek, amelyet az automatikus védelem engedélyezésekor a **biztonsági mentés beállítása** szakaszban talál. Az automatikusan védett adatbázishoz tartozó házirendet azonban később is módosíthatja.  

Az automatikus védelem engedélyezése:

  1. A **biztonsági mentéshez használt elemek** területen válassza ki azt a példányt, amelynek engedélyezni szeretné az automatikus védelmet.
  2. Válassza ki a legördülő listát az **AutoProtect** alatt, majd válassza **a be** lehetőséget, majd kattintson **az OK gombra**.

      ![Automatikus védelem engedélyezése a rendelkezésre állási csoportban](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. A Backup minden adatbázishoz konfigurálva van, és nyomon követhető a **biztonsági mentési feladatokban**.

Ha le kell tiltania az automatikus védelmet, válassza ki a példány nevét a **biztonsági mentés konfigurálása** területen, majd válassza az automatikus **védelem letiltása** a példányhoz lehetőséget. A rendszer az összes adatbázist biztonsági mentést folytatja, de a jövőbeli adatbázisok nem lesznek automatikusan védve.

![Automatikus védelem letiltása az adott példányon](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>Következő lépések

Az alábbiak végrehajtásának módját ismerheti meg:

* [SQL Server adatbázisok biztonsági másolatának visszaállítása](restore-sql-database-azure-vm.md)
* [Biztonsági másolatok SQL Server adatbázisok kezelése](manage-monitor-sql-database-backup.md)
