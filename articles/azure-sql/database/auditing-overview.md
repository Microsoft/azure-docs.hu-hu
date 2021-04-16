---
title: Azure SQL naplózási Azure SQL Database és Azure Synapse Analytics
description: Az Azure SQL Database naplózással naplózva követheti nyomon az adatbázis-eseményeket.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/17/2021
ms.custom: azure-synapse, sqldbrb=1
ms.openlocfilehash: bc7ac6b97d10e5941e46b8be3e12baff32bded4a
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483061"
---
# <a name="auditing-for-azure-sql-database-and-azure-synapse-analytics"></a>Naplózás a Azure SQL Database és Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

A naplózás [Azure SQL Database](sql-database-paas-overview.md) és [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) követi az adatbázis-eseményeket, és egy auditnaplóba írja őket az Azure Storage-fiókban, a Log Analytics-munkaterületen vagy a Event Hubs.

A naplózás további előnyei:

- Segít a jogszabályi megfelelőség fenntartásában és az adatbázison végzett tevékenység megértésében, valamint az esetleg üzleti veszélyeket vagy biztonsági problémákat jelző rendellenességek feltárásában.

- Lehetővé teszi és megkönnyíti a megfelelőségi szabványok betartását, bár nem mindig garantálja a megfelelőséget. A szabványoknak való megfelelést segítő Azure-programokkal kapcsolatos további információkért keresse fel az [Azure biztonsági és adatkezelési központot](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), ahol megtalálja az Azure SQL megfelelőségi tanúsítványainak az aktuális listáját is.

> [!NOTE]
> A naplózási Azure SQL Managed Instance lásd a következő cikket: Első lépések [a SQL Managed Instance naplózással.](../managed-instance/auditing-configure.md)

## <a name="overview"></a><a id="overview"></a>Áttekintés

A naplózási SQL Database a következőre:

- **Napló** megőrzése a kiválasztott eseményekről. Megadhatja a naplózni kívánt adatbázis-műveletek kategóriáit.
- **Jelentés** az adatbázis-tevékenységről. Az előre konfigurált jelentések és irányítópultok segítségével gyorsan megkezdheti a tevékenységekkel és az eseményekkel kapcsolatos jelentéskészítést.
- **Jelentések** elemzése. Azonosíthatja a gyanús eseményeket, a szokatlan tevékenységeket és a trendeket.

> [!IMPORTANT]
> A naplózás Azure SQL Database és Azure Synapse rendelkezésre állásra és teljesítményre van optimalizálva. Nagyon nagy aktivitású vagy magas hálózati terhelés esetén a Azure SQL Database és Azure Synapse engedélyezheti a műveleteket, és előfordulhat, hogy nem rögzítik a naplózható eseményeket.

### <a name="auditing-limitations"></a>Naplózási korlátozások

- **A Premium Storage** jelenleg **nem támogatott.**
- **A tárfiókhoz** **Azure Data Lake Storage Gen2 hierarchikus névtér** jelenleg **nem támogatott.**
- A naplózás szüneteltetett Azure Synapse **engedélyezése** nem támogatott. A naplózás engedélyezéséhez indítsa el ismét az Azure Synapse-et.
- Az **SQL-Azure Synapse naplózása csak** az alapértelmezett naplózási műveletcsoportokat **támogatja.**


#### <a name="define-server-level-vs-database-level-auditing-policy"></a><a id="server-vs-database-level"></a>Kiszolgálószintű és adatbázisszintű naplózási házirend meghatározása

Naplózási szabályzatok definiálhatóak egy adott adatbázishoz vagy az Azure alapértelmezett kiszolgálói szabályzatához (amely az Azure-ban SQL Database vagy Azure Synapse): [](logical-servers.md)

- A kiszolgálói házirend a kiszolgálón lévő összes meglévő és újonnan létrehozott adatbázisra vonatkozik.

- Ha *a kiszolgáló naplózása engedélyezve van,* az *mindig a adatbázisra vonatkozik.* A rendszer az adatbázis naplózási beállításaitól függetlenül naplót végez.

- Amikor naplózási szabályzatot határoz meg az adatbázis szintjén egy Log Analytics-munkaterületen vagy eseményközpont célhelyén, a következő műveletek nem tartják meg a forrásadatbázis-szintű naplózási szabályzatot:
    - [Adatbázis-másolat](database-copy.md)
    - [Adott időpontnak megfelelő helyreállítás](recovery-using-backups.md)
    - [Georeplikáció](active-geo-replication-overview.md) (a másodlagos adatbázis nem fog adatbázisszintű naplózást)

- Az adatbázis naplózásának engedélyezése a kiszolgálón való engedélyezése  mellett nem bírálja felül és nem módosítja a kiszolgáló naplózási beállításainak bármelyikét. Mindkét naplózás egymás mellett fog létezni. Ez azt jelenti, hogy az adatbázis naplózása kétszer, párhuzamosan; egyszer a kiszolgálói házirend, egyszer pedig az adatbázis-házirend által.

   > [!NOTE]
   > Kerülje a kiszolgáló- és adatbázisblob-naplózás együttes engedélyezését, kivéve, ha:
    >
    > - Egy adott adatbázishoz egy  másik tárfiókot, megőrzési időszakot vagy Log *Analytics-munkaterületet* szeretne használni.
    > - Olyan eseménytípusokat vagy kategóriákat szeretne naplólni egy adott adatbázishoz, amelyek eltérnek a kiszolgálón található többi adatbázistól. Előfordulhat például, hogy olyan táblabeszúrásokat kell naplólni, amelyek csak egy adott adatbázisra vonatkoznak.
   >
   > Ellenkező esetben azt javasoljuk, hogy csak a kiszolgálószintű naplózást engedélyezze, és hagyja letiltva az adatbázisszintű naplózást minden adatbázisnál.

#### <a name="remarks"></a>Megjegyzések

- Az auditnaplók a **hozzáfűző blobokba vannak írva** az Azure-előfizetésében lévő Azure Blob Storage-ban
- Az auditnaplók .xel formátumúak, és a következővel [nyithatók meg: SQL Server Management Studio (SSMS).](/sql/ssms/download-sql-server-management-studio-ssms)
- Ha nem módosítható naplótárolót konfigurál a kiszolgálóhoz vagy adatbázisszintű naplózási eseményekhez, kövesse az [Azure Storage utasításait.](../../storage/blobs/storage-blob-immutability-policies-manage.md#enabling-allow-protected-append-blobs-writes) A nem módosítható  blobtároló konfigurálásakor győződjön meg arról, hogy be van jelölve a További hozzáfűzés engedélyezése jelölőnégyzet.
- Az auditnaplókat virtuális hálózat vagy tűzfal mögötti Azure Storage-fiókba írhatja. További utasításokért lásd: Naplózás írása virtuális hálózat és tűzfal [mögötti tárfiókba.](audit-write-storage-account-behind-vnet-firewall.md)
- A naplóformátumról, a tárolási mappa hierarchiájának és az elnevezési konvenciókról a Blob auditnapló formátumának referenciájában [talál további információt.](./audit-log-format.md)
- A naplózás automatikusan engedélyezve van az [írásvédett replikákon](read-scale-out.md). További részleteket a tárolómappák hierarchiáról, az elnevezési konvenciókról és a naplóformátumról az auditnapló [formátumával SQL Database talál.](audit-log-format.md)
- Azure AD-hitelesítés használata esetén a sikertelen bejelentkezési rekordok nem *jelennek* meg az SQL-naplóban. A sikertelen bejelentkezési naplórekordok megtekintéséhez látogasson el a Azure Active Directory [portálra,](../../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)amely naplózza ezeknek az eseményeknek a részleteit.
- A bejelentkezéseket az átjáró ahhoz a konkrét példányhoz irányítja, amelyben az adatbázis található.  AAD-bejelentkezések esetén a rendszer azt megelőzően ellenőrzi a hitelesítő adatokat, mielőtt megkísérelné a kért adatbázisba való bejelentkezést az adott felhasználóval.  Sikertelenség esetén a kért adatbázis elérése sosem történik meg, így naplózás sem zajlik.  SQL-bejelentkezések esetén a rendszer ellenőrzi a hitelesítő adatokat a kért adatokon, így ebben az esetben naplózhatja őket.  A sikeres bejelentkezéseket, amelyek nyilvánvalóan elérik az adatbázist, mindkét esetben naplózza a rendszer.
- A naplózási beállítások konfigurálása után bekapcsolhatja az új fenyegetésészlelési funkciót, és konfigurálhatja az e-maileket a biztonsági riasztások fogadására. A fenyegetésészlelés használata esetén proaktív riasztásokat kap az adatbázissal kapcsolatos rendellenes tevékenységekről, amelyek potenciális biztonsági fenyegetéseket jeleznek. További információ: [A fenyegetésészlelés első lépések.](threat-detection-overview.md)

## <a name="set-up-auditing-for-your-server"></a><a id="setup-auditing"></a>Naplózás beállítása a kiszolgálóhoz

Az alapértelmezett naplózási szabályzat tartalmazza az összes műveletet és a következő műveletcsoportokat, amelyek naplózják az adatbázison végrehajtott összes lekérdezést és tárolt eljárást, valamint a sikeres és sikertelen bejelentkezéseket:
  
- BATCH_COMPLETED_GROUP
- SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
- FAILED_DATABASE_AUTHENTICATION_GROUP
  
A PowerShell használatával konfigurálhatja a naplózást a különböző típusú műveletekhez és műveletcsoportokhoz a Következő SQL Database a Azure PowerShell [szakaszban](#manage-auditing) leírtak szerint.

Azure SQL Database és Azure Synapse Audit 4000 karakter hosszú adatot tárol az auditrekord karaktermezőihez. Ha  egy naplózható művelet által visszaadott utasítás vagy data_sensitivity_information értékek több mint 4000 karaktert tartalmaznak, az első 4000 karakternél hosszabb adatok csonkolódnak, és nem lesznek **naplózva.** 
A következő szakasz a naplózás konfigurációját ismerteti a Azure Portal.

  > [!NOTE]
  > - Szüneteltetett dedikált SQL-készlet naplózásának engedélyezése nem lehetséges. A naplózás engedélyezéséhez szüneteltesse a dedikált SQL-készletet. További információ a [dedikált SQL-készletről.](../..//synapse-analytics/sql/best-practices-dedicated-sql-pool.md)
  > - Ha a naplózás log Analytics-munkaterületre vagy eseményközpont-célhelyre van konfigurálva a Azure Portal- [](../../azure-monitor/essentials/diagnostic-settings.md) vagy PowerShell-parancsmaggal, a rendszer diagnosztikai beállítást hoz létre az "SQLSecurityAuditEvents" kategóriával.

1. Nyissa meg az [Azure Portal](https://portal.azure.com).
2. Az **SQL-adatbázis vagy** az **SQL** Server panel Biztonság fejléce alatt lépjen a Naplózás **elemre.**
3. Ha inkább kiszolgálói naplózási házirendet szeretne beállítani, válassza a Kiszolgáló beállításainak megtekintése hivatkozást az adatbázis naplózási oldalán.  Ezután megtekintheti vagy módosíthatja a kiszolgáló naplózási beállításait. A kiszolgáló-naplózási házirendek a kiszolgálón lévő összes meglévő és újonnan létrehozott adatbázisra érvényesek.

    ![Képernyőkép az adatbázis naplózási oldalán kiemelt Kiszolgálóbeállítások megtekintése hivatkozásról.](./media/auditing-overview/2_auditing_get_started_server_inherit.png)

4. Ha az adatbázis szintjén szeretné engedélyezni a naplózást, kapcsolja **be** a **Naplózást.** Ha a kiszolgáló naplózása engedélyezve van, az adatbázis által konfigurált naplózás a kiszolgáló naplózásával együtt fog létezni.

5. Az auditnaplók írási helyének konfigurálásához több lehetőség is rendelkezésre áll. Naplókat írhat egy Azure-tárfiókba, egy Log Analytics-munkaterületre, amelyet Azure Monitor naplók használhatnak, vagy az eseményközpontba az eseményközpont használatával. Ezek bármilyen kombinációját konfigurálhatja, és mindegyikbe auditnaplók lesznek írva.
  
   ![tárolási lehetőségek](./media/auditing-overview/auditing-select-destination.png)

### <a name="auditing-of-microsoft-support-operations"></a><a id="auditing-of-microsoft-support-operations"></a>A Microsoft ügyfélszolgálata naplózása

A Azure SQL Server Microsoft ügyfélszolgálata naplózása lehetővé teszi a Microsoft támogatási szakembereinek műveleteinek naplózását, amikor egy támogatási kérés során hozzá kell férni a kiszolgálóhoz. Ennek a képességnek a használata a naplózással együtt átláthatóbbá teszi a munkaerőt, és lehetővé teszi az anomáliadetektálást, a trendek megjelenítését és az adatveszteség-megelőzést.

A naplózás engedélyezéséhez Microsoft ügyfélszolgálata a Azure SQL-kiszolgáló panel Biztonság fejlécében  lépjen a Naplózás elemre, és kapcsolja be a **Microsoft** támogatási műveleteinek naplózása **lehetőséget.** 

  > [!IMPORTANT]
  > A Microsoft támogatási műveleteinek naplózása nem támogatja a tárfiók célhelyét. A képesség engedélyezéséhez konfigurálni kell egy Log Analytics-munkaterületet vagy egy Event Hubs-célt.

![Képernyőkép a Microsoft ügyfélszolgálata műveletekről](./media/auditing-overview/support-operations.png)

A Log Analytics-munkaterületen Microsoft ügyfélszolgálata naplóinak áttekintéséhez használja a következő lekérdezést:

```kusto
AzureDiagnostics
| where Category == "DevOpsOperationsAudit"
```

### <a name="audit-to-storage-destination"></a><a id="audit-storage-destination"></a>Naplózás a tárolási célhelyen

Ha az auditnaplók tárfiókba való írását konfigurálja, válassza a **Storage** lehetőséget, amikor a **Naplózás szakaszra** jut. Válassza ki azt az Azure Storage-fiókot, ahová a naplókat menteni fogja, majd válassza ki a megőrzési megőrzési időszakot a **Speciális tulajdonságok megnyitásával.** Ezután kattintson a **Mentés** gombra. A megőrzési időtartamnál régebbi naplók törlődnek.

- A megőrzési időtartam alapértelmezett értéke 0 (korlátlan adatmegőrzés). Ezt az értéket a Megőrzés **(nap)** csúszkával módosíthatja a Speciális tulajdonságok között a tárfiók naplózásra való konfigurálásakor. 
  - Ha a megőrzési időszakot 0-ra (korlátlan megőrzésre) módosítja, vegye figyelembe, hogy a megőrzési idő csak a megőrzési érték módosítása után írt naplókra vonatkozik (a korlátlan megőrzési időszak alatt írt naplók megmaradnak, még a megőrzés engedélyezése után is).

  ![tárfiók](./media/auditing-overview/auditing_select_storage.png)

### <a name="audit-to-log-analytics-destination"></a><a id="audit-log-analytics-destination"></a>Naplózás a Log Analytics célhelyére
  
Ha az auditnaplók Log Analytics-munkaterületre való írását konfigurálja, válassza a **Log Analytics** lehetőséget, és nyissa meg a **Log Analytics részleteit.** Válassza ki azt a Log Analytics-munkaterületet, ahol a naplók meg lesznek írva, majd kattintson az **OK gombra.** Ha még nem hozott létre Log Analytics-munkaterületet, tekintse meg a Log Analytics-munkaterület létrehozása a [Azure Portal.](../../azure-monitor/logs/quick-create-workspace.md)

   ![LogAnalyticsworkspace](./media/auditing-overview/auditing_select_oms.png)

További részletek a Log Analytics Azure Monitor munkaterületről: A naplók üzembe [helyezésének Azure Monitor meg.](../../azure-monitor/logs/design-logs-deployment.md)
   
### <a name="audit-to-event-hub-destination"></a><a id="audit-event-hub-destination"></a>Naplózás az eseményközpont célhelyén

Az auditnaplók eseményközpontba való írásának konfigurálához válassza az **Eseményközpont lehetőséget.** Válassza ki azt az eseményközpontot, ahol a naplók meg lesznek írva, majd kattintson a **Mentés gombra.** Győződjön meg arról, hogy az eseményközpont ugyanabban a régióban van, mint az adatbázis és a kiszolgáló.

   ![Eventhub](./media/auditing-overview/auditing_select_event_hub.png)

## <a name="analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>Auditnaplók és jelentések elemzése

Ha úgy döntött, hogy naplókat ír a Azure Monitor naplókba:

- Használja az [Azure Portalt](https://portal.azure.com). Nyissa meg a vonatkozó adatbázist. Az adatbázis Naplózási oldalának tetején válassza az  **Auditnaplók megtekintése lehetőséget.**

    ![auditnaplók megtekintése](./media/auditing-overview/auditing-view-audit-logs.png)

- Ezután két módon is megtekintheti a naplókat:

    A **Naplórekordok** lap tetején található Log Analytics elemre kattintva **megnyílik** a Naplók nézet a Log Analytics-munkaterületen, ahol testre szabhatja az időtartományt és a keresési lekérdezést.

    ![megnyitás a Log Analytics-munkaterületen](./media/auditing-overview/auditing-log-analytics.png)

    Az **Audit records (Naplórekordok)** lap tetején található View dashboard (Irányítópult megtekintése) elemre kattintva megnyithat egy irányítópultot, amely megjeleníti az auditnaplók adatait, ahol lefúrhat a Security Insightsba, a Bizalmas adatokhoz való hozzáférésbe stb.  Ez az irányítópult az adatok biztonsági elemzésének segítésében segít.
    Az időtartományt és a keresési lekérdezést is testreszabhatja.
    ![Log Analytics-irányítópult megtekintése](media/auditing-overview/auditing-view-dashboard.png)

    ![Log Analytics-irányítópult](media/auditing-overview/auditing-log-analytics-dashboard.png)

    ![Log Analytics Security Insights](media/auditing-overview/auditing-log-analytics-dashboard-data.png)

- Az auditnaplókat a Log Analytics panelről is elérheti. Nyissa meg a Log Analytics-munkaterületet, és az **Általános szakaszban** kattintson a Naplók **elemre.** Kezdhet egy egyszerű lekérdezéssel, például: keressen rá az *"SQLSecurityAuditEvents"* kifejezésre az auditnaplók megtekintéséhez.
    Innen a naplók [](../../azure-monitor/logs/log-query-overview.md) használatával Azure Monitor is futtathat speciális kereséseket az auditnapló adatain. Azure Monitor naplók valós idejű működési elemzéseket nyújtanak integrált kereséssel és egyéni irányítópultokkal, amelyek segítségével több millió rekordot elemezhet az összes számítási feladatban és kiszolgálón. A naplók keresési nyelvére és Azure Monitor vonatkozó további hasznos információkért lásd: Azure Monitor [naplók keresési referenciája.](../../azure-monitor/logs/log-query-overview.md)

Ha úgy döntött, hogy auditnaplókat ír az eseményközpontba:

- Az eseményközpontból származó auditnapló-adatokhoz be kell állítania egy streamet, amely felhasználja az eseményeket, és kiírja őket egy célhoz. További információt a [dokumentációban Azure Event Hubs talál.](../index.yml)
- Az eseményközpontban az auditnaplók az [Apache Avro-események](https://avro.apache.org/) törzsében vannak tárolva, és UTF-8 kódolású JSON-formázással vannak tárolva. A naplók olvasásához [Avro-eszközöket](../../event-hubs/event-hubs-capture-overview.md#use-avro-tools) vagy a formátum feldolgozására képes hasonló eszközöket használhat.

Ha úgy döntött, hogy auditnaplókat ír egy Azure Storage-fiókba, a naplók megtekintésére több módszer is használható:

- Az auditnaplók a telepítés során kiválasztott fiókban vannak összesítve. Az auditnaplókat a következőhöz hasonló eszközzel vizsgálhatja [Azure Storage Explorer.](https://storageexplorer.com/) Az Azure Storage-ban a naplók blobfájlok gyűjteményeként vannak mentve egy **sqldbauditlogs nevű tárolóban.** További részleteket a tárolómappák hierarchiáról, az elnevezési konvenciókról és a naplóformátumról az auditnapló [formátumával SQL Database talál.](./audit-log-format.md)

- Használja az [Azure Portalt](https://portal.azure.com).  Nyissa meg a vonatkozó adatbázist. Az adatbázis Naplózási oldalának tetején kattintson az  **Auditnaplók megtekintése elemre.**

    ![auditnaplók megtekintése](./media/auditing-overview/auditing-view-audit-logs.png)

    **Megnyílik a** naplórekordok, amelyekből megtekintheti a naplókat.

  - Adott dátumokat a **Naplórekordok** oldal tetején található Szűrő gombra **kattintva tekintheti** meg.
  - A kiszolgálói naplózási házirend által  létrehozott naplórekordok és az adatbázis-naplózási házirend között a  **Naplózási forrás kapcsolóval válthat.**

       ![Képernyőkép a naplórekordok megtekintésének lehetőségeiről.]( ./media/auditing-overview/8_auditing_get_started_blob_audit_records.png)

- Az **auditnaplók sys.fn_get_audit_file** (T-SQL) használatával táblázatos formában adja vissza az auditnapló adatait. A függvény használatával kapcsolatos további információkért lásd: [sys.fn_get_audit_file.](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql)

- Használja **a Naplófájlok egyesítése a** SQL Server Management Studio (az SSMS 17-től kezdve):
    1. Az SSMS menüjében válassza a **Fájl**  >  **megnyitása**  >  **Auditfájlok egyesítése lehetőséget.**

        ![A Naplófájlok egyesítése menüpontot bemutató képernyőkép.](./media/auditing-overview/9_auditing_get_started_ssms_1.png)
    2. Megnyílik **a Naplófájlok** hozzáadása párbeszédpanel. A Hozzáadás lehetőségek **egyikének** kiválasztásával választhatja ki, hogy a helyi lemezről származó auditfájlokat egyesíteni vagy az Azure Storage-ból importálja. Meg kell adnia az Azure Storage adatait és a fiókkulcsot.

    3. Az egyesítéshez szükséges összes fájl hozzáadása után kattintson az **OK gombra** az egyesítési művelet befejezéséhez.

    4. Az egyesített fájl megnyílik az SSMS-ben, ahol megtekintheti és elemezheti azt, valamint exportálhatja egy XEL- vagy CSV-fájlba vagy egy táblába.

- Használja a Power BI. Az auditnapló adatait megtekintheti és elemezheti a Power BI. További információért és egy letölthető sablon eléréséhez lásd: Naplóadatok elemzése a [Power BI.](https://techcommunity.microsoft.com/t5/azure-database-support-blog/sql-azure-blob-auditing-basic-power-bi-dashboard/ba-p/368895)
- Töltse le a naplófájlokat az Azure Storage-blobtárolóból a portálon vagy egy olyan eszközzel, mint a [Azure Storage Explorer.](https://storageexplorer.com/)
  - Miután letöltött egy naplófájlt helyileg, kattintson duplán a fájlra a naplók megnyitásához, megtekintéséhez és elemzéséhez az SSMS-ban.
  - Egyszerre több fájlt is letölthet a Azure Storage Explorer. A helyi mappába mentéshez kattintson a jobb  gombbal egy adott almappára, és válassza a Mentés másként lehetőséget.

- További módszerek:

  - Több fájl vagy naplófájlokat tartalmazó almappa letöltése után helyileg egyesítheti azokat az SSMS Merge Audit Files (SSMS egyesítési auditfájlok) korábban ismertetett utasításainak megfelelően.
  - Blobnaplók programozott módon történő megtekintése: [Bővített eseményfájlok](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) lekérdezése a PowerShell használatával.

## <a name="production-practices"></a><a id="production-practices"></a>Éles eljárások


### <a name="auditing-geo-replicated-databases"></a>Georeplikált adatbázisok naplózása

A georeplikált adatbázisoknál az elsődleges adatbázis naplózásának engedélyezésekor a másodlagos adatbázis ugyanazokkal a naplózási szabályzatokkal fog megegyezni. A másodlagos adatbázis naplózását is beállíthatja úgy, hogy engedélyezi a naplózást a másodlagos kiszolgálón az elsődleges adatbázistól függetlenül.

- Kiszolgálószintű **(ajánlott):** Kapcsolja be a  naplózást mind az elsődleges kiszolgálón, mind a másodlagos kiszolgálón **–** az elsődleges és a másodlagos adatbázisok naplózása a megfelelő kiszolgálószintű házirendtől függetlenül fog levéve.
- Adatbázisszintű: A másodlagos adatbázisok adatbázisszintű naplózása csak az elsődleges adatbázis naplózási beállításaiból konfigurálható.
  - A naplózást az elsődleges adatbázison *kell* engedélyezni, nem a kiszolgálón.
  - A naplózás az elsődleges adatbázison való engedélyezése után a másodlagos adatbázison is engedélyezve lesz.

    > [!IMPORTANT]
    > Adatbázisszintű naplózással a másodlagos adatbázis tárolási beállításai megegyeznek az elsődleges adatbázis beállításaival, ami régiók közötti forgalmat okoz. Javasoljuk, hogy csak kiszolgálószintű naplózást engedélyezzen, és hagyja letiltva az adatbázisszintű naplózást minden adatbázisnál.

### <a name="storage-key-regeneration"></a>Tárkulcs újragenerálása

Éles környezetben valószínűleg rendszeresen frissíti a tárkulcsokat. Amikor auditnaplókat ír az Azure Storage-ba, a kulcsok frissítésekésekor újra kell mentenünk a naplózási szabályzatot. A folyamat a következő:

1. Nyissa meg **a Speciális tulajdonságokat** a **Storage alatt.** A **Tárelérési kulcs mezőben** válassza a Másodlagos **lehetőséget.** Ezután kattintson **a naplózási** konfigurációs oldal tetején található Mentés gombra.

    ![A másodlagos tárelérési kulcs kiválasztásának folyamatát bemutató képernyőkép.](./media/auditing-overview/5_auditing_get_started_storage_key_regeneration.png)
2. A tárolókonfigurációs oldalon újragenerálja az elsődleges hozzáférési kulcsot.

    ![Navigációs panel](./media/auditing-overview/6_auditing_get_started_regenerate_key.png)
3. Vissza a naplózási konfigurációs lapra, váltsa át a tárelérési kulcsot másodlagosról elsődlegesre, majd kattintson az **OK gombra.** Ezután kattintson **a naplózási** konfigurációs oldal tetején található Mentés gombra.
4. Vissza a tárolókonfigurációs oldalra, és újra létrehozza a másodlagos hozzáférési kulcsot (a következő kulcs frissítési ciklusának előkészítéseként).

## <a name="manage-azure-sql-database-auditing"></a><a id="manage-auditing"></a>Naplózás Azure SQL Database kezelése

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

**PowerShell-parancsmagok (beleértve a WHERE záradékok támogatását a további szűréshez):**

- [Adatbázis-naplózási szabályzat létrehozása vagy frissítése (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Kiszolgálói naplózási szabályzat létrehozása vagy frissítése (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [Adatbázis-naplózási szabályzat lekérte (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Kiszolgálói naplózási szabályzat le kérése (Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [Adatbázis-naplózási szabályzat eltávolítása (Remove-AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Kiszolgálói naplózási házirend eltávolítása (Remove-AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

Példaszk szkriptre: [Naplózás és fenyegetésészlelés konfigurálása a PowerShell használatával.](scripts/auditing-threat-detection-powershell-configure.md)

### <a name="using-rest-api"></a>A REST API használata

**REST API:**

- [Adatbázis-naplózási házirend létrehozása vagy frissítése](/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Kiszolgálói naplózási házirend létrehozása vagy frissítése](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Adatbázis-naplózási szabályzat lekérte](/rest/api/sql/database%20auditing%20settings/get)
- [Kiszolgálói naplózási házirend leellenőrzése](/rest/api/sql/server%20auditing%20settings/get)

Kiterjesztett szabályzat WHERE záradékkal a további szűréshez:

- [Adatbázis kiterjesztett naplózási *szabályzatának* létrehozása vagy frissítése](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Kiszolgáló kiterjesztett naplózási *házirend* létrehozása vagy frissítése](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Adatbázis kiterjesztett *naplózási* szabályzatának lekérte](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Kiszolgáló kiterjesztett *naplózási* szabályzatának lekérte](/rest/api/sql/server%20auditing%20settings/get)

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

- [Kiszolgáló naplózási szabályzatának kezelése](/cli/azure/sql/server/audit-policy)
- [Adatbázis naplózási szabályzatának kezelése](/cli/azure/sql/db/audit-policy)

### <a name="using-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok használata

A naplózást Azure SQL Database [sablonokkal](../../azure-resource-manager/management/overview.md) Azure Resource Manager, ahogy az alábbi példákban is látható:

- [Naplózással rendelkező Azure SQL Database üzembe helyezése az auditnaplók Azure Blob Storage-fiókba való írására](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Naplók Log Analyticsbe Azure SQL Database naplózási funkcióval rendelkező virtuális gép üzembe helyezése](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Olyan naplózási Azure SQL Database üzembe helyezése, amely engedélyezi az auditnaplók írását az Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> A csatolt minták egy külső nyilvános adattárban találhatóak, és jelen esetben, jótállás nélkül biztosítanak adatokat, és a Microsoft semmilyen támogatási programja/szolgáltatása nem támogatja.
