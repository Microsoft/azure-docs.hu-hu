---
title: Oktatóanyag – SAP HANA-adatbázisok biztonsági mentése Azure-beli virtuális gépeken
description: Ebből az oktatóanyagból megtudhatja, hogyan készíthet biztonsági másolatot az Azure-beli virtuális gépen futó SAP HANA-adatbázisokról egy Azure Backup Recovery Services-tárolóra.
ms.topic: tutorial
ms.date: 02/24/2020
ms.openlocfilehash: 65f2a7ba51fcf811e36839d3998902ba37a90fc4
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88889991"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Oktatóanyag: SAP HANA-adatbázisok biztonsági mentése Azure-beli virtuális gépen

Ez az oktatóanyag bemutatja, hogyan készíthet biztonsági mentést SAP HANA Azure-beli virtuális gépeken futó adatbázisokról egy Azure Backup Recovery Services-tárolóra. Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:

> [!div class="checklist"]
>
> * Tároló létrehozása és konfigurálása
> * Adatbázisok felderítése
> * Biztonsági mentések konfigurálása

[Itt](sap-hana-backup-support-matrix.md#scenario-support) találja az összes olyan forgatókönyvet, amelyet jelenleg támogatunk.

>[!NOTE]
>Az 2020-as augusztus 1-től a RHEL (7,4, 7,6, 7,7 & 8,1) SAP HANA biztonsági mentés általánosan elérhető.

## <a name="prerequisites"></a>Előfeltételek

A biztonsági mentések konfigurálása előtt győződjön meg arról, hogy a következőket végzi el:

* Azonosítsa vagy hozzon létre egy [Recovery Services](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) tárolót ugyanabban a régióban és előfizetésben, mint a SAP HANA futtató virtuális gép.
* Engedélyezze a virtuális gép és az Internet közötti kapcsolat használatát, hogy az elérhető legyen az Azure-ban, az alábbi [hálózati csatlakozási eljárás beállítása](#set-up-network-connectivity) című témakörben leírtak szerint.
* Győződjön meg arról, hogy a SAP HANA-kiszolgáló virtuális gép neve és az erőforráscsoport neve nem haladja meg a Azure Resource Manager 84 karakterét (ARM_ virtuális gépek (és a klasszikus virtuális gépek 77 karaktereit). Ez a korlátozás azért van, mert egyes karaktereket a szolgáltatás foglal le.
* Léteznie kell egy kulcsnak a **hdbuserstore** , amely megfelel a következő feltételeknek:
  * Az alapértelmezett **hdbuserstore**jelen kell lennie. Az alapértelmezett érték az a `<sid>adm` fiók, amelyben a SAP HANA telepítve van.
  * A MDC a kulcsnak a **NÉVSZERVER**SQL-portjára kell mutatnia. SDC esetén a **INDEXSERVER** SQL-portjára kell mutatnia.
  * A felhasználónak hitelesítő adatokkal kell rendelkeznie a felhasználók hozzáadásához és törléséhez
* Futtassa a SAP HANA biztonsági mentési konfigurációs parancsfájlt (előzetes regisztrációs parancsfájl) azon a virtuális gépen, ahol a HANA telepítve van, a legfelső szintű felhasználóként. [Ez a szkript](https://aka.ms/scriptforpermsonhana) a HANA-rendszer biztonsági mentésre kész állapotba helyezését kéri le. Az előzetes regisztrációs szkripttel kapcsolatos további információkért tekintse meg az [előzetes regisztrációs parancsfájlt](#what-the-pre-registration-script-does) ismertető szakaszt.

>[!NOTE]
>Az előregisztrációs parancsfájl a RHEL-on (7,4, 7,6 és 7,7) futó SAP HANA-munkaterhelések unixODBC234 telepíti, valamint a RHEL 8,1 **-** **unixODBC** . [Ez a csomag a RHEL for SAP HANA (a RHEL 7 Server esetében) Update Services for SAP Solutions (RPMs)](https://access.redhat.com/solutions/5094721)tárházban található.  Az Azure Marketplace RHEL rendszerképében a tárház a következő lenne: **rhui-RHEL-SAP-Hana-for-RHEL-7-Server-rhui-e4s-RPMs**.

## <a name="set-up-network-connectivity"></a>Hálózati kapcsolat beállítása

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

### <a name="private-endpoints"></a>Privát végpontok

A privát végpontok lehetővé teszik a biztonságos kapcsolódást a virtuális hálózaton belüli kiszolgálókról a Recovery Services-tárba. A privát végpont egy IP-címet használ a tár VNET. A virtuális hálózaton belüli erőforrásai és a tároló közötti hálózati forgalom a virtuális hálózatra és a Microsoft gerinc hálózatán található privát kapcsolatra is áthalad. Ezzel kiküszöbölhető a nyilvános internetről való kitettség. További információ a Azure Backup privát végpontokról [itt](./private-endpoints.md)olvasható.

### <a name="nsg-tags"></a>NSG Címkék

Ha hálózati biztonsági csoportokat (NSG) használ, használja a *AzureBackup* szolgáltatás címkéjét, hogy engedélyezze a kimenő hozzáférést Azure Backuphoz. A Azure Backup címkén kívül az *Azure ad* -hez és az *Azure Storage*-hoz hasonló [NSG-szabályok](../virtual-network/security-overview.md#service-tags) létrehozásával is engedélyeznie kell a csatlakozást a hitelesítéshez és az adatátvitelhez.  A következő lépések azt ismertetik, hogyan hozható létre szabály a Azure Backup címke számára:

1. A **minden szolgáltatás**területen lépjen a **hálózati biztonsági csoportok** elemre, és válassza ki a hálózati biztonsági csoportot.

1. A **Beállítások**területen válassza a **kimenő biztonsági szabályok** lehetőséget.

1. Válassza a **Hozzáadás** elemet. Adja meg az új szabály létrehozásához szükséges összes adatot a [biztonsági szabály beállításai](../virtual-network/manage-network-security-group.md#security-rule-settings)című témakörben leírtak szerint. Győződjön meg arról, hogy a **cél** a *Service tag* és a **cél szolgáltatás címkéje** *AzureBackup*értékre van állítva.

1. Kattintson a **Hozzáadás**  gombra az újonnan létrehozott kimenő biztonsági szabály mentéséhez.

Hasonlóképpen NSG kimenő biztonsági szabályokat hozhat létre az Azure Storage és az Azure AD számára. A szolgáltatás címkével kapcsolatos további információkért tekintse meg [ezt a cikket](../virtual-network/service-tags-overview.md).

### <a name="azure-firewall-tags"></a>Címkék Azure Firewall

Ha Azure Firewall használ, hozzon létre egy szabályt a *AzureBackup* [Azure Firewall FQDN címke](../firewall/fqdn-tags.md)használatával. Ez lehetővé teszi Azure Backup összes kimenő hozzáférését.

### <a name="allow-access-to-service-ip-ranges"></a>Hozzáférés engedélyezése a szolgáltatás IP-tartományai számára

Ha a hozzáférési szolgáltatás IP-címeinek engedélyezését választja, tekintse meg az [itt](https://www.microsoft.com/download/confirmation.aspx?id=56519)elérhető JSON-fájl IP-tartományait. Engedélyeznie kell a hozzáférést a Azure Backup, az Azure Storage és a Azure Active Directory megfelelő IP-címekhez.

### <a name="allow-access-to-service-fqdns"></a>Hozzáférés engedélyezése a szolgáltatás teljes tartománynevéhez

A következő teljes tartományneveket is használhatja a szükséges szolgáltatások elérésének engedélyezéséhez a kiszolgálókon:

| Szolgáltatás    | Elérni kívánt tartománynevek                             |
| -------------- | ------------------------------------------------------------ |
| Azure Backup  | `*.backup.windowsazure.com`                             |
| Azure Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | Az 56-es és a 59-es szakaszban található teljes tartománynevek elérésének engedélyezése [a jelen cikk](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) alapján |

### <a name="use-an-http-proxy-server-to-route-traffic"></a>HTTP-proxykiszolgáló használata a forgalom irányításához

Ha egy Azure-beli virtuális gépen futó SAP HANA adatbázisról készít biztonsági másolatot, a virtuális gépen található biztonsági mentési bővítmény a HTTPS API-k használatával küldi el a felügyeleti parancsokat a Azure Backup és az Azure Storage-ba történő adattároláshoz. A biztonsági mentési bővítmény az Azure AD-t is használja a hitelesítéshez. Irányítsa a biztonsági mentési bővítmény forgalmát a három szolgáltatáshoz a HTTP-proxyn keresztül. A fent említett IP-címek és FQDN-k listájának használata a szükséges szolgáltatásokhoz való hozzáférés engedélyezéséhez. A hitelesített proxykiszolgálók nem támogatottak.

## <a name="what-the-pre-registration-script-does"></a>Az előzetes regisztrációs parancsfájl

Az előzetes regisztrációs parancsfájl futtatása a következő funkciókat hajtja végre:

* A Linux-disztribúció alapján a parancsfájl telepíti vagy frissíti a Azure Backup ügynök által igényelt szükséges csomagokat.
* Kimenő hálózati kapcsolati ellenőrzéseket hajt végre Azure Backup-kiszolgálókkal és függő szolgáltatásokkal (például Azure Active Directory és Azure Storage).
* Bejelentkezik a HANA rendszerbe az [Előfeltételek](#prerequisites)részeként felsorolt felhasználói kulccsal. A felhasználói kulcs használatával biztonsági mentési felhasználó (AZUREWLBACKUPHANAUSER) hozható létre a HANA-rendszeren, és a felhasználói kulcs törölhető az előzetes regisztrációs parancsfájl sikeres futtatása után.
* A AZUREWLBACKUPHANAUSER a szükséges szerepköröket és engedélyeket rendeli hozzá:
  * ADATBÁZIS-rendszergazda (MDC esetén) és biztonsági mentési rendszergazda (SDC esetén): új adatbázisok létrehozása a visszaállítás során.
  * Katalógus OLVASása: a biztonsági mentési katalógus beolvasása.
  * SAP_INTERNAL_HANA_SUPPORT: néhány privát tábla eléréséhez.
* A parancsfájl egy kulcsot hoz létre a HANA Backup beépülő modulhoz tartozó AZUREWLBACKUPHANAUSER **hdbuserstore** , amely az összes műveletet (adatbázis-lekérdezések, visszaállítási műveletek, a biztonsági mentés konfigurálása és futtatása) kezeli.

>[!NOTE]
> Explicit módon megadhatja az [előfeltételként](#prerequisites) megadott felhasználói kulcsot az előfeltételek paraméterként az előzetes regisztrációs parancsfájlhoz: `-sk SYSTEM_KEY_NAME, --system-key SYSTEM_KEY_NAME` <br><br>
>A parancsfájl által elfogadott egyéb paraméterek megismeréséhez használja a parancsot `bash msawb-plugin-config-com-sap-hana.sh --help`

A kulcs létrehozásának megerősítéséhez futtassa a HDBSQL parancsot a HANA gépen a SIDADM hitelesítő adataival:

```hdbsql
hdbuserstore list
```

A parancs kimenetének meg kell jelennie a {SID} {DBNAME} kulcsnak, amely a felhasználó AZUREWLBACKUPHANAUSER jelenik meg.

>[!NOTE]
> Győződjön meg arról, hogy rendelkezik a SSFS-fájlok egyedi készletével `/usr/sap/{SID}/home/.hdb/` . Ezen az elérési úton csak egy mappa lehet.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A Recovery Services-tároló egy olyan entitás, amely az idő múlásával létrehozott biztonsági mentéseket és helyreállítási pontokat tárolja. A Recovery Services-tároló a védett virtuális gépekhez társított biztonsági mentési házirendeket is tartalmazza.

Egy Recovery Services-tároló létrehozásához:

1. Jelentkezzen be az előfizetésbe a [Azure Portalban](https://portal.azure.com/).

2. A bal oldali menüben válassza a **minden szolgáltatás** lehetőséget.

   ![Minden szolgáltatás kiválasztása](./media/tutorial-backup-sap-hana-db/all-services.png)

3. A **minden szolgáltatás** párbeszédpanelen írja be a **Recovery Services**értéket. Az erőforrás-szűrők listája a bemenet alapján. Az erőforrások listájában válassza a **Recovery Services**-tárolók lehetőséget.

   ![Recovery Services tárolók kiválasztása](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. A **Recovery Services** -tárolók irányítópultján válassza a **Hozzáadás**lehetőséget.

   ![Recovery Services-tároló hozzáadása](./media/tutorial-backup-sap-hana-db/add-vault.png)

   Megnyílik az **Recovery Services** -tároló párbeszédpanel. Adja meg a **név, az előfizetés, az erőforráscsoport** és a **hely** értékét.

   ![Helyreállítási tár létrehozása](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * **Név**: a név a Recovery Services-tár azonosítására szolgál, és egyedinek kell lennie az Azure-előfizetésben. Adjon meg legalább két, de legfeljebb 50 karakterből álló nevet. A névnek betűvel kell kezdődnie, és csak betűt, számot és kötőjelet tartalmazhat. Ebben az oktatóanyagban a **SAPHanaVault**nevet használtuk.
   * **Előfizetés**: válassza ki a használni kívánt előfizetést. Ha csak egy előfizetés tagja, akkor ezt a nevet fogja látni. Ha nem biztos abban, hogy melyik előfizetést szeretné használni, használja az alapértelmezett (javasolt) előfizetést. Több választási lehetőség is van, ha a munkahelyi vagy iskolai fiók egynél több Azure-előfizetéshez van társítva. Itt a **SAP HANA Solution Lab előfizetési** előfizetést használtuk.
   * **Erőforráscsoport**: használjon meglévő erőforráscsoportot, vagy hozzon létre egy újat. Itt **SAPHANADemo**használunk.<br>
   Az előfizetésben elérhető erőforráscsoportok listájának megtekintéséhez válassza a **meglévő használata**lehetőséget, majd válasszon ki egy erőforrást a legördülő listából. Új erőforráscsoport létrehozásához válassza az **új létrehozása** lehetőséget, és adja meg a nevet. Az erőforráscsoportok részletes ismertetését itt tekintheti meg: [Azure Resource Manager Overview (áttekintés](../azure-resource-manager/management/overview.md)).
   * **Hely**: válassza ki a tároló földrajzi régióját. A tárolónak ugyanabban a régióban kell lennie, mint ahol a virtuális gép SAP HANA fut. Használtuk az **USA 2. keleti**régióját.

5. Válassza a **felülvizsgálat + létrehozás**lehetőséget.

   ![& létrehozási áttekintés kiválasztása](./media/tutorial-backup-sap-hana-db/review-create.png)

Ekkor létrejön a Recovery Services-tároló.

## <a name="discover-the-databases"></a>Az adatbázisok felderítése

1. A tárolóban **első lépések**kattintson a **biztonsági mentés**elemre. A **hol fut a**számítási feladat? területen válassza **a SAP HANA lehetőséget az Azure-beli virtuális gépen**.
2. Kattintson a **felderítés indítása**gombra. Ez elindítja a nem védett Linux rendszerű virtuális gépek felderítését a tároló régiójában. Ekkor megjelenik a védelemmel ellátni kívánt Azure-beli virtuális gép.
3. A **Virtual Machines kiválasztása lapon**kattintson a hivatkozásra azon parancsfájl letöltéséhez, amely a Azure Backup szolgáltatás számára engedélyeket biztosít a SAP HANA virtuális gépek adatbázis-felderítéshez való hozzáféréséhez.
4. Futtassa a szkriptet azon a virtuális gépen, amely a biztonsági mentésre használni kívánt SAP HANA adatbázis (oka) t futtatja.
5. Miután futtatta a szkriptet a virtuális gépen, a **Virtual Machines kiválasztása**lapon válassza ki a virtuális gépet. Ezután kattintson az **adatbázisok felderítése**elemre.
6. Azure Backup a virtuális gépen lévő összes SAP HANA adatbázist felfedi. A felderítés során Azure Backup regisztrálja a virtuális gépet a tárolóban, és telepít egy bővítményt a virtuális gépre. Nincs ügynök telepítve az adatbázison.

   ![Az adatbázisok felderítése](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása

Most, hogy felderítjük a biztonsági mentéshez használni kívánt adatbázisokat, engedélyezzük a biztonsági mentést.

1. Kattintson a **biztonsági mentés konfigurálása**elemre.

   ![Biztonsági mentés konfigurálása](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. Az **elemek kijelölése biztonsági mentéshez**területen válasszon ki egy vagy több védelemmel ellátni kívánt adatbázist, majd kattintson **az OK**gombra.

   ![Válassza ki azokat az elemeket, amelyekről biztonsági másolatot szeretne készíteni](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. A **biztonsági mentési szabályzat > válassza a biztonsági mentési szabályzat lehetőséget**, hozzon létre egy új biztonsági mentési szabályzatot az adatbázis (ok) hoz a következő szakaszban található utasítások szerint.

   ![Biztonsági mentési házirend kiválasztása](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. A szabályzat létrehozása után a **biztonsági mentés menüben**kattintson a **biztonsági mentés engedélyezése**elemre.

   ![Kattintson a biztonsági mentés engedélyezése elemre.](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. A biztonsági mentési konfiguráció előrehaladásának nyomon követése a portál **értesítések** területén.

## <a name="creating-a-backup-policy"></a>Biztonsági mentési szabályzat létrehozása

A biztonsági mentési szabályzat meghatározza a biztonsági másolatok készítésének idejét, valamint azt, hogy mennyi ideig őrzi meg a rendszer.

* A rendszer a tároló szintjén hozza létre a szabályzatot.
* Több tároló is használhatja ugyanazt a biztonsági mentési szabályzatot, de a biztonsági mentési szabályzatot minden egyes tárba alkalmaznia kell.

A házirend-beállításokat a következőképpen adhatja meg:

1. A **Házirend neve**mezőben adja meg az új szabályzat nevét. Ebben az esetben adja meg a **SAPHANA**.

   ![Adja meg az új szabályzat nevét](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. A **teljes biztonsági mentési szabályzatban**válasszon ki egy **biztonsági mentési gyakoriságot**. **Napi** vagy **heti**választ is választhat. Ebben az oktatóanyagban a **napi** biztonsági mentést választottuk.

   ![Biztonsági mentés gyakoriságának kiválasztása](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. A **megőrzési tartomány**területen konfigurálja a teljes biztonsági mentés megőrzési beállításait.
   * Alapértelmezés szerint az összes beállítás ki van választva. Törölje az összes olyan megőrzési időtartamra vonatkozó korlátozást, amelyet nem kíván használni, és állítsa be azokat.
   * A minimális megőrzési idő bármilyen típusú biztonsági mentés esetén (teljes/különbözeti/napló) hét nap.
   * A helyreállítási pontok megőrzési időtartam alapján vannak címkézve. Ha például napi teljes biztonsági mentést választ, a naponta csak egy teljes biztonsági mentést indít el.
   * Egy adott nap biztonsági másolata a heti megőrzési időtartam és a beállítás alapján van megcímkézve és megtartva.
   * A havi és az éves adatmegőrzési tartomány hasonló módon viselkedik.
4. A **teljes biztonsági mentési szabályzat** menüben kattintson az **OK** gombra a beállítások elfogadásához.
5. Ezt követően válassza a **különbözeti biztonsági mentés** lehetőséget a különbözeti szabályzat hozzáadásához.
6. A **különbözeti biztonsági mentési házirendben**válassza az **Engedélyezés** lehetőséget a gyakoriság és a megőrzési vezérlők megnyitásához. Minden **vasárnap** ( **2:00 órakor**) elvégezte a különbözeti biztonsági mentést, amely **30 napig**tart.

   ![Különbözeti biztonsági mentési szabályzat](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >A növekményes biztonsági mentések jelenleg nem támogatottak.
   >

7. A házirend mentéséhez kattintson az **OK** gombra, és térjen vissza a **biztonsági mentési házirend** fő menüjére.
8. A tranzakciós napló biztonsági mentési szabályzatának hozzáadásához válassza a **napló biztonsági mentése** lehetőséget,
   * A **napló biztonsági mentése** alapértelmezés szerint **engedélyezve**van. Ez nem tiltható le, mert SAP HANA kezeli az összes napló biztonsági mentését.
   * A biztonsági mentési ütemtervnek és a megőrzési idő **15 napjának** **2 óra** van beállítva.

    ![Biztonsági mentési szabályzat naplózása](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > A naplók biztonsági mentései csak egy sikeres teljes biztonsági mentés befejezése után kezdődnek.
   >

9. A házirend mentéséhez kattintson az **OK** gombra, és térjen vissza a **biztonsági mentési házirend** fő menüjére.
10. Miután befejezte a biztonsági mentési szabályzat definiálását, kattintson **az OK**gombra.

Sikeresen konfigurálta a (z) SAP HANA adatbázis (ok) hoz készült biztonsági másolat (oka) t.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [futtathat igény szerinti biztonsági mentést az Azure-beli virtuális gépeken futó SAP HANA-adatbázisokon](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* Ismerje meg, hogyan [állíthatja vissza az Azure-beli virtuális gépeken futó SAP HANA-adatbázisokat](sap-hana-db-restore.md)
* Megtudhatja, hogyan [kezelheti SAP HANA-adatbázisok biztonsági mentését a Azure Backup használatával](sap-hana-db-manage.md)
* Ismerje meg, hogy miként lehet [elhárítani a SAP HANA adatbázisok biztonsági mentése során](backup-azure-sap-hana-database-troubleshoot.md) felmerülő gyakori problémákat
