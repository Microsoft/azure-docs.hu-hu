---
title: SAP HANA-adatbázis biztonsági mentése az Azure-ba Azure Backup
description: Ebből a cikkből megtudhatja, hogyan készíthet biztonsági mentést egy SAP HANA-adatbázisról az Azure-beli virtuális gépekre a Azure Backup szolgáltatással.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: e7735c4240529cc6fc9bb6470934dd335d22aa77
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101719610"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>SAP HANA-adatbázisok biztonsági mentése Azure-beli virtuális gépeken

SAP HANA adatbázisok olyan kritikus fontosságú számítási feladatok, amelyek alacsony helyreállítási célt (RPO) és hosszú távú adatmegőrzést igényelnek. Az Azure Virtual Machines (VM) rendszeren futó SAP HANA adatbázisok biztonsági mentését [Azure Backup](backup-overview.md)használatával végezheti el.

Ez a cikk az Azure-beli virtuális gépeken futó SAP HANA adatbázisok biztonsági mentését mutatja be egy Azure Backup Recovery Services-tárolón.

Ebből a cikkből megtudhatja, hogyan:
> [!div class="checklist"]
>
> * Tároló létrehozása és konfigurálása
> * Adatbázisok felderítése
> * Biztonsági mentések konfigurálása
> * Igény szerinti biztonsági mentési feladatok futtatása

>[!NOTE]
>Az 2020-as augusztus 1-től a RHEL (7,4, 7,6, 7,7 & 8,1) SAP HANA biztonsági mentés általánosan elérhető.

>[!NOTE]
>Az Azure-beli virtuális gépeken futó **SQL Server-kiszolgáló és az Azure** -beli virtuális gépekhez készült SAP HANA Soft delete már előzetes verzióban érhető el.<br>
>Az előzetes verzióra való feliratkozáshoz írjon nekünk a következő címen: [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="prerequisites"></a>Előfeltételek

Tekintse át az [előfeltételeket](tutorial-backup-sap-hana-db.md#prerequisites) , valamint azt, hogy az [előzetes regisztrációs parancsfájl mit tartalmaz](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) az adatbázis biztonsági mentéshez való beállításához.

### <a name="establish-network-connectivity"></a>Hálózati kapcsolat létrehozása

Az Azure-beli virtuális gépeken futó SAP HANA adatbázisoknak minden művelethez kapcsolódniuk kell a Azure Backup szolgáltatáshoz, az Azure Storage-hoz és a Azure Active Directoryhoz. Ezt privát végpontok használatával vagy a szükséges nyilvános IP-címekhez vagy teljes tartománynevek elérésének engedélyezésével lehet elérni. A szükséges Azure-szolgáltatásokhoz való megfelelő kapcsolódás nem teszi lehetővé az adatbázis-felderítést, a biztonsági mentés konfigurálását, a biztonsági másolatok készítését és az adatok visszaállítását.

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

Hasonlóképpen NSG kimenő biztonsági szabályokat hozhat létre az Azure Storage és az Azure AD számára. A szolgáltatás címkével kapcsolatos további információkért tekintse meg [ezt a cikket](../virtual-network/service-tags-overview.md).

#### <a name="azure-firewall-tags"></a>Címkék Azure Firewall

Ha Azure Firewall használ, hozzon létre egy szabályt a *AzureBackup* [Azure Firewall FQDN címke](../firewall/fqdn-tags.md)használatával. Ez lehetővé teszi Azure Backup összes kimenő hozzáférését.

#### <a name="allow-access-to-service-ip-ranges"></a>Hozzáférés engedélyezése a szolgáltatás IP-tartományai számára

Ha a hozzáférési szolgáltatás IP-címeinek engedélyezését választja, tekintse meg az [itt](https://www.microsoft.com/download/confirmation.aspx?id=56519)elérhető JSON-fájl IP-tartományait. Engedélyeznie kell a hozzáférést a Azure Backup, az Azure Storage és a Azure Active Directory megfelelő IP-címekhez.

#### <a name="allow-access-to-service-fqdns"></a>Hozzáférés engedélyezése a szolgáltatás teljes tartománynevéhez

A következő teljes tartományneveket is használhatja a szükséges szolgáltatások elérésének engedélyezéséhez a kiszolgálókon:

| Szolgáltatás    | Elérni kívánt tartománynevek                             |
| -------------- | ------------------------------------------------------------ |
| Azure Backup  | `*.backup.windowsazure.com`                             |
| Azure Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | Az 56-es és a 59-es szakaszban található teljes tartománynevek elérésének engedélyezése [a jelen cikk](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) alapján |

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>HTTP-proxykiszolgáló használata a forgalom irányításához

Ha egy Azure-beli virtuális gépen futó SAP HANA adatbázisról készít biztonsági másolatot, a virtuális gépen található biztonsági mentési bővítmény a HTTPS API-k használatával küldi el a felügyeleti parancsokat a Azure Backup és az Azure Storage-ba történő adattároláshoz. A biztonsági mentési bővítmény az Azure AD-t is használja a hitelesítéshez. Irányítsa a biztonsági mentési bővítmény a három szolgáltatáshoz kapcsolódó forgalmát a HTTP-proxyn keresztül. A fent említett IP-címek és FQDN-k listájának használata a szükséges szolgáltatásokhoz való hozzáférés engedélyezéséhez. A hitelesített proxykiszolgálók nem támogatottak.

> [!NOTE]
> A szolgáltatási szint proxyja nem támogatott. Ez azt jelentheti, hogy a proxyn keresztüli forgalom csak néhány vagy kiválasztott szolgáltatásból (Azure Backup Services) nem támogatott. A teljes adatokat vagy forgalmat proxy útján lehet irányítani.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Az adatbázisok felderítése

1. A tárolóban, a **első lépések** területen válassza a **biztonsági mentés** lehetőséget. A **hol fut a** számítási feladat? területen válassza **a SAP HANA lehetőséget az Azure-beli virtuális gépen**.
2. Válassza a **felderítés indítása** lehetőséget. Ez elindítja a nem védett Linux rendszerű virtuális gépek felderítését a tároló régiójában.

   * A felderítést követően a nem védett virtuális gépek a portálon jelennek meg a név és az erőforráscsoport alapján.
   * Ha a virtuális gép nem a várt módon van felsorolva, ellenőrizze, hogy már van-e biztonsági másolat egy tárolóban.
   * Több virtuális gépnek is ugyanaz a neve, de különböző erőforráscsoportok tartoznak hozzájuk.

3. A **Virtual Machines kiválasztása** lapon válassza ki a hivatkozást a parancsfájl letöltéséhez, amely a Azure Backup szolgáltatás számára engedélyeket biztosít a SAP HANA virtuális gépek adatbázis-felderítéshez való hozzáféréséhez.
4. Futtassa a szkriptet minden olyan virtuális gépen, amely SAP HANA-adatbázisokat üzemeltet, amelyekről biztonsági másolatot szeretne készíteni.
5. Miután futtatta a szkriptet a virtuális gépeken, a **Virtual Machines kiválasztása** területen válassza ki a virtuális gépeket. Ezután válassza a **felderítési adatbázisok** lehetőséget.
6. Azure Backup a virtuális gépen lévő összes SAP HANA adatbázist felfedi. A felderítés során Azure Backup regisztrálja a virtuális gépet a tárolóban, és telepít egy bővítményt a virtuális gépre. Nincs ügynök telepítve az adatbázison.

    ![SAP HANA adatbázisok felderítése](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása  

Most engedélyezze a biztonsági mentést.

1. A 2. lépésben válassza a **biztonsági mentés konfigurálása** elemet.

    ![Biztonsági mentés konfigurálása](./media/backup-azure-sap-hana-database/configure-backup.png)
2. Az **elemek kijelölése biztonsági mentéshez** területen jelölje ki az összes védelemmel ellátni kívánt adatbázist > **az OK gombra**.

    ![Válassza ki azokat az elemeket, amelyekről biztonsági másolatot szeretne készíteni](./media/backup-azure-sap-hana-database/select-items.png)
3. A **biztonsági mentési házirendben**  >  **válassza a biztonsági** mentési házirend elemet, hozzon létre egy új biztonsági mentési szabályzatot az adatbázisokhoz az alábbi utasítások szerint.

    ![Biztonsági mentési házirend kiválasztása](./media/backup-azure-sap-hana-database/backup-policy.png)
4. A szabályzat létrehozása után a **biztonsági mentés** menüben válassza a **biztonsági mentés engedélyezése** lehetőséget.

    ![Biztonsági mentés engedélyezése](./media/backup-azure-sap-hana-database/enable-backup.png)
5. A biztonsági mentési konfiguráció előrehaladásának nyomon követése a portál **értesítések** területén.

### <a name="create-a-backup-policy"></a>Biztonsági mentési szabályzat létrehozása

A biztonsági mentési szabályzat meghatározza a biztonsági másolatok készítésének idejét, valamint azt, hogy mennyi ideig őrzi meg a rendszer.

* A szabályzat a tárolószinten jön létre.
* Több tároló is használhatja ugyanazt a biztonsági mentési szabályzatot, de a biztonsági mentési szabályzatot alkalmazni kell minden egyes tárolóra.

>[!NOTE]
>A Azure Backup nem módosítja automatikusan a nyári időmegtakarítást az Azure-beli virtuális gépen futó SAP HANA-adatbázis biztonsági mentésekor.
>
>Szükség szerint módosítsa manuálisan a szabályzatot.

A házirend-beállításokat a következőképpen adhatja meg:

1. A **Szabályzat neve** lehetőségnél adja meg az új szabályzat nevét.

   ![Adja meg a szabályzat nevét](./media/backup-azure-sap-hana-database/policy-name.png)
1. A **Teljes biztonsági mentés** pontban válassza ki a **Biztonsági mentés gyakorisága**, majd a **Naponta** vagy **Hetente** lehetőséget.
   * **Napi**: válassza ki azt az órát és időzónát, amelyben a biztonsági mentési feladatok megkezdődnek.
       * Teljes biztonsági mentést kell futtatnia. Ezt a beállítást nem lehet kikapcsolni.
       * A **Teljes biztonsági mentés** kiválasztásával megtekintheti a szabályzatot.
       * Nem hozhat létre különbözeti biztonsági mentéseket a napi rendszerességű teljes biztonsági mentések esetén.
   * **Hetente**: válassza ki a hét azon napját, óráját és időzónáját, amelyben a biztonsági mentési feladatot futtatja.

   ![Biztonsági mentés gyakoriságának kiválasztása](./media/backup-azure-sap-hana-database/backup-frequency.png)

1. A **megőrzési tartomány** területen konfigurálja a teljes biztonsági mentés megőrzési beállításait.
    * Alapértelmezés szerint minden beállítás ki van választva. Törölje az összes olyan megőrzési időtartamra vonatkozó korlátozást, amelyet nem kíván használni, és állítsa be azokat.
    * A minimális megőrzési idő bármilyen típusú biztonsági mentés esetén (teljes/különbözeti/napló) hét nap.
    * A rendszer a helyreállítási pontokat a megőrzési időtartamuk alapján jelöli megőrzésre. Ha például napi rendszerességű teljes biztonsági mentést választ, a rendszer naponta csak egy teljes biztonsági mentést indít el.
    * Egy adott nap biztonsági másolata a heti megőrzési időtartam és a beállítás alapján van megcímkézve és megtartva.
    * A havi és éves megőrzési időtartamok hasonló módon viselkednek.

1. A **Teljes biztonsági mentési szabályzat** menüben kattintson az **OK** gombra a beállítások elfogadásához.
1. Válasszon különbözeti **biztonsági másolatot** a különbözeti szabályzat hozzáadásához.
1. A **Különbözeti biztonsági mentési szabályzat** pontban válassza az **Engedélyezés** lehetőséget, hogy megnyissa a gyakorisági és megőrzési beállításokat.
    * Legfeljebb napi egy különbözeti biztonsági mentést kezdeményezhet.
    * A különbözeti biztonsági mentéseket legfeljebb 180 napig lehet megőrizni. Ha hosszabb megőrzésre van szüksége, akkor teljes biztonsági mentést kell használnia.

    ![Különbözeti biztonsági mentési szabályzat](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > Választhatja a napi biztonsági mentés különbözetét vagy növekményét is, de mindkettőt nem.
1. A **növekményes biztonsági mentési szabályzatban** válassza az **Engedélyezés** lehetőséget a gyakoriság és a megőrzési vezérlők megnyitásához.
    * Legfeljebb napi egy növekményes biztonsági mentést indíthat.
    * A növekményes biztonsági mentések legfeljebb 180 napig tárolhatók. Ha hosszabb megőrzésre van szüksége, akkor teljes biztonsági mentést kell használnia.

    ![Növekményes biztonsági mentési szabályzat](./media/backup-azure-sap-hana-database/incremental-backup-policy.png)

1. Kattintson az **OK** gombra, hogy mentse a szabályzatot, és visszatérjen a fő **Biztonsági mentési szabályzat** menübe.
1. A tranzakciós napló biztonsági mentési szabályzatának hozzáadásához válassza a **napló biztonsági mentése** lehetőséget,
    * A **napló biztonsági mentése** területen válassza az **Engedélyezés** lehetőséget.  Ez nem tiltható le, mert a SAP HANA az összes napló biztonsági mentését kezeli.
    * Állítsa be a gyakoriságot és a megőrzési vezérlőket.

    > [!NOTE]
    > A biztonsági másolatok naplózása csak a sikeres teljes biztonsági mentés befejezése után kezdi a folyamatot.

1. Kattintson az **OK** gombra, hogy mentse a szabályzatot, és visszatérjen a fő **Biztonsági mentési szabályzat** menübe.
1. Miután befejezte a biztonsági mentési szabályzat definiálását, kattintson **az OK gombra**.

> [!NOTE]
> Minden naplózási biztonsági mentés az előző teljes biztonsági mentéshez van láncolva, amely helyreállítási láncot alkot. Ez a teljes biztonsági mentés a legutóbbi napló biztonsági mentésének lejárta után is megmarad. Ez azt jelentheti, hogy a teljes biztonsági mentést egy további időszakra vonatkozóan kell megőrizni, hogy az összes napló helyreállítható legyen. Tegyük fel, hogy egy felhasználó hetente teljes biztonsági mentést, napi különbözeti és 2 órás naplókat tartalmaz. Mindegyiket 30 napig őrzi meg a rendszer. A hetente megteltek azonban csak a következő teljes biztonsági mentés elérhetővé tételével, azaz 30 + 7 nap után törlődnek. A heti teljes biztonsági mentés például november 16-án történik. Az adatmegőrzési szabályzat szerint az IT-t december 16-i-ig kell megőrizni. Az utolsó napló biztonsági mentése ehhez a teljes, november 22-én a következő beütemezett megtelte előtt történik. Amíg a napló nem érhető el a DEC 22nd-ig, a november 16-i teljes nem törölhető. Így a november 16-i Full megmarad december 22-én.

## <a name="run-an-on-demand-backup"></a>Igény szerinti biztonsági mentések futtatása

A biztonsági mentések a szabályzat ütemezésével összhangban futnak. Az igény szerinti biztonsági mentést az alábbi módon futtathatja:

1. A tároló menüben válassza a **biztonsági másolati elemek elemet**.
2. A **biztonsági másolati elemek** területen válassza ki a SAP HANA adatbázist futtató virtuális gépet, majd válassza a **biztonsági mentés** lehetőséget.
3. A **biztonsági mentés** területen válassza ki a végrehajtani kívánt biztonsági mentés típusát. Ez után válassza az **OK** gombot. Ezt a biztonsági mentést a biztonsági másolathoz tartozó szabályzatnak megfelelően megőrzi a rendszer.
4. A portál értesítéseinek figyelése. A feladat előrehaladását a tároló irányítópultján követheti nyomon > **biztonsági mentési feladatok**  >  **folyamatban** vannak. Az adatbázis méretétől függően a kezdeti biztonsági mentés hosszabb időt is igénybe vehet.

Alapértelmezés szerint az igény szerinti biztonsági mentések megőrzése 45 nap.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>SAP HANA Studio Backup futtatása Azure Backup engedélyezve lévő adatbázison

Ha egy olyan adatbázis helyi biztonsági másolatát kívánja használni, amelyről biztonsági mentés készül Azure Backupsal, tegye a következőket:

1. Várjon, amíg befejeződik az adatbázis teljes vagy naplózott biztonsági mentése. Az állapot ellenõrzése SAP HANA Studio/pilótafülkében.
1. Tiltsa le a naplók biztonsági mentését, és állítsa a biztonsági mentési katalógust a fájlrendszerre a megfelelő adatbázishoz.
1. Ehhez kattintson duplán a **systemdb**-konfiguráció elemre, majd  >    >  **válassza az adatbázis**  >  **-szűrő (napló)** lehetőséget.
1. A **enable_auto_log_backup** beállítása **nem** értékre.
1. **Log_backup_using_backint** beállítása **hamis** értékre.
1. **Catalog_backup_using_backint** beállítása **hamis** értékre.
1. Igény szerint készítsen teljes biztonsági mentést az adatbázisról.
1. Várjon, amíg befejeződik a teljes biztonsági mentés és a katalógus biztonsági mentése.
1. A korábbi beállítások visszaállítása az Azure-ba:
    * Állítsa  a Enable_auto_log_backup **értéket igen** értékre.
    * A **log_backup_using_backint** beállítása **igaz** értékre.
    * A **catalog_backup_using_backint** beállítása **igaz** értékre.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [állíthatja vissza az Azure-beli virtuális gépeken futó SAP HANA-adatbázisokat](./sap-hana-db-restore.md)
* Megtudhatja, hogyan [kezelheti SAP HANA-adatbázisok biztonsági mentését a Azure Backup használatával](./sap-hana-db-manage.md)
