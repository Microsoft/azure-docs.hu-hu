---
title: Azure SQL-naplózás a Azure SQL Database és az Azure szinapszis Analytics szolgáltatáshoz
description: Azure SQL Database naplózás használatával követheti nyomon az adatbázis-eseményeket egy naplóba.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/17/2021
ms.custom: azure-synapse, sqldbrb=1
ms.openlocfilehash: 8513127f4a79c9c94323140462ad2d2648a0130d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577702"
---
# <a name="auditing-for-azure-sql-database-and-azure-synapse-analytics"></a>A Azure SQL Database és az Azure szinapszis Analytics naplózása
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

A [Azure SQL Database](sql-database-paas-overview.md) és az [Azure szinapszis Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) naplózása nyomon követi az adatbázis eseményeit, és az Azure Storage-fiókban, log Analytics munkaterületen vagy Event Hubsban lévő naplóba írja azokat.

A naplózás további előnyei:

- Segít a jogszabályi megfelelőség fenntartásában és az adatbázison végzett tevékenység megértésében, valamint az esetleg üzleti veszélyeket vagy biztonsági problémákat jelző rendellenességek feltárásában.

- Lehetővé teszi és megkönnyíti a megfelelőségi szabványok betartását, bár nem mindig garantálja a megfelelőséget. A szabványoknak való megfelelést segítő Azure-programokkal kapcsolatos további információkért keresse fel az [Azure biztonsági és adatkezelési központot](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), ahol megtalálja az Azure SQL megfelelőségi tanúsítványainak az aktuális listáját is.

> [!NOTE]
> Az Azure SQL felügyelt példányok naplózásával kapcsolatos információkért tekintse meg a következő cikket, [amely az SQL felügyelt példányok naplózásának első lépéseit](../managed-instance/auditing-configure.md)ismerteti.

## <a name="overview"></a><a id="overview"></a>Áttekintés

SQL Database naplózást a következőre használhatja:

- A kiválasztott események naplózási nyomvonalának **megőrzése** . Megadhatja a naplózni kívánt adatbázis-műveletek kategóriáit.
- **Jelentés** az adatbázis-tevékenységről. Az előre konfigurált jelentések és irányítópultok segítségével gyorsan megkezdheti a tevékenységekkel és az eseményekkel kapcsolatos jelentéskészítést.
- Jelentések **elemzése** . Azonosíthatja a gyanús eseményeket, a szokatlan tevékenységeket és a trendeket.

> [!IMPORTANT]
> A Azure SQL Database és az Azure szinapszis naplózása a rendelkezésre állásra és a teljesítményre van optimalizálva. A nagyon magas tevékenység vagy a nagy hálózati terhelés, a Azure SQL Database és az Azure szinapszis lehetővé teszi, hogy a műveletek folytatódnak, és előfordulhat, hogy nem rögzítik a naplózott eseményeket.

### <a name="auditing-limitations"></a>Naplózási korlátozások

- A **Premium Storage** jelenleg **nem támogatott**.
- **Azure Data Lake Storage Gen2 Storage-fiók** **hierarchikus névtere** jelenleg **nem támogatott**.
- A naplózás engedélyezése szüneteltetett **Azure szinapszison** nem támogatott. A naplózás engedélyezéséhez indítsa el ismét az Azure Synapse-et.
- Az **Azure SZINAPSZIS SQL-készletek** naplózása **csak** az alapértelmezett naplózási műveleti csoportokat támogatja.


#### <a name="define-server-level-vs-database-level-auditing-policy"></a><a id="server-vs-database-level"></a>A kiszolgáló szintű és az adatbázis szintű naplózási szabályzat meghatározása

Egy naplózási szabályzat definiálható egy adott adatbázishoz, vagy az Azure-ban alapértelmezett [kiszolgálói](logical-servers.md) házirendként (amely SQL Database vagy Azure szinapszisot üzemeltet):

- A kiszolgálói házirend a kiszolgálón lévő összes meglévő és újonnan létrehozott adatbázisra vonatkozik.

- Ha a *kiszolgáló naplózása engedélyezve van*, az *mindig az adatbázisra vonatkozik*. A rendszer naplózza az adatbázist, az adatbázis naplózási beállításaitól függetlenül.

- Ha a naplózási házirend az adatbázis szintjén van definiálva egy Log Analytics munkaterületre vagy egy Event hub-célhelyre, a következő műveletek nem őrzik meg a forrás-adatbázis szintű naplózási házirendet:
    - [Adatbázis-másolat](database-copy.md)
    - [Adott időpontnak megfelelő helyreállítás](recovery-using-backups.md)
    - [Geo-replikáció](active-geo-replication-overview.md) (a másodlagos adatbázis nem rendelkezik adatbázis szintű naplózással)

- Az adatbázis naplózásának engedélyezése a kiszolgálón való engedélyezésen kívül a nem bírálja felül vagy *nem* módosítja a kiszolgáló naplózásának beállításait. Mindkét naplózás egymás mellett fog létezni. Ez azt jelenti, hogy az adatbázist párhuzamosan, kétszer kell naplózni. egyszer a kiszolgálói házirend és egyszer az adatbázis-házirend alapján.

   > [!NOTE]
   > Ne engedélyezze egyszerre a kiszolgáló naplózási és az adatbázis-blob naplózását, kivéve, ha:
    >
    > - Egy adott adatbázishoz másik *Storage-fiókot*, *megőrzési időtartamot* vagy *log Analytics munkaterületet* szeretne használni.
    > - Egy adott adatbázishoz tartozó eseménytípus vagy kategóriák naplózására van szükség, amely eltér a kiszolgálón lévő többi adatbázistól. Előfordulhat például, hogy olyan táblákat szúr be, amelyeket csak egy adott adatbázisra kell naplózni.
   >
   > Ellenkező esetben javasoljuk, hogy csak a kiszolgálói szintű naplózást engedélyezze, és hagyja letiltani az adatbázis-szintű naplózást minden adatbázis esetében.

#### <a name="remarks"></a>Megjegyzések

- A naplók egy Azure Blob-tárolóban lévő Blobok **hozzáfűzésére** szolgálnak az Azure-előfizetésben
- A naplók. xel formátumúak, és [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)használatával nyithatók meg.
- Ha egy nem módosítható napló-tárolót szeretne konfigurálni a kiszolgáló vagy az adatbázis szintű naplózási eseményekhez, kövesse az [Azure Storage által biztosított utasításokat](../../storage/blobs/storage-blob-immutability-policies-manage.md#enabling-allow-protected-append-blobs-writes). Győződjön meg arról, hogy a nem módosítható blob-tároló konfigurálásakor a **további Hozzáfűzések engedélyezése** jelölőnégyzet be van jelölve.
- A naplókat egy VNet vagy tűzfal mögötti Azure Storage-fiókba is írhatja. A konkrét utasításokért lásd: [a VNet és a tűzfal mögötti Storage-fiókba való írás naplózása](audit-write-storage-account-behind-vnet-firewall.md).
- A naplózási formátumra, a tárolási mappa hierarchiájának és az elnevezési konvenciók részleteiért tekintse meg a [blob naplózási napló formátumának referenciáját](./audit-log-format.md).
- A naplózás automatikusan engedélyezve van az [írásvédett replikákon](read-scale-out.md). A tárolási mappák hierarchiájának, az elnevezési konvencióknak és a napló formátumának további részleteiért tekintse meg a [SQL Database a naplózási napló formátumát](audit-log-format.md).
- Az Azure AD-hitelesítés használatakor a sikertelen bejelentkezések rekordjai *nem* jelennek meg az SQL-naplóban. A sikertelen bejelentkezési naplózási rekordok megtekintéséhez látogasson el a [Azure Active Directory portálra](../../active-directory/reports-monitoring/reference-sign-ins-error-codes.md), amely az események adatait naplózza.
- A bejelentkezéseket az átjáró ahhoz a konkrét példányhoz irányítja, amelyben az adatbázis található.  AAD-bejelentkezések esetén a rendszer azt megelőzően ellenőrzi a hitelesítő adatokat, mielőtt megkísérelné a kért adatbázisba való bejelentkezést az adott felhasználóval.  Sikertelenség esetén a kért adatbázis elérése sosem történik meg, így naplózás sem zajlik.  SQL-bejelentkezések esetén a hitelesítő adatokat a rendszer ellenőrzi a kért adatokon, így ebben az esetben naplózni lehet őket.  A sikeres bejelentkezéseket, amelyek nyilvánvalóan elérik az adatbázist, mindkét esetben naplózza a rendszer.
- A naplózási beállítások konfigurálása után bekapcsolhatja az új veszélyforrások észlelése funkciót, és konfigurálhatja az e-maileket a biztonsági riasztások fogadására. A veszélyforrások észlelése esetén a rendellenes adatbázis-tevékenységekkel kapcsolatos proaktív riasztásokat kap, amelyek potenciális biztonsági fenyegetéseket jelezhetnek. További információkért lásd: [a fenyegetések észlelésének első lépései](threat-detection-overview.md).

## <a name="set-up-auditing-for-your-server"></a><a id="setup-auditing"></a>A kiszolgáló naplózásának beállítása

Az alapértelmezett naplózási házirend magában foglalja az összes műveletet és a következő műveleti csoportokat, amelyek az adatbázison végrehajtott összes lekérdezést és tárolt eljárást naplózzák, valamint sikeres és sikertelen bejelentkezéseket is végeznek:
  
- BATCH_COMPLETED_GROUP
- SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
- FAILED_DATABASE_AUTHENTICATION_GROUP
  
Különböző típusú műveletek és műveleti csoportok naplózását a PowerShell használatával konfigurálhatja a következő témakörben leírtak szerint: [SQL Database naplózás kezelése Azure PowerShell használatával](#manage-auditing) .

A Azure SQL Database és az Azure szinapszis audit 4000 karakterből álló adattípust tárol egy naplózási rekordban. Ha egy auditálható művelet által visszaadott **utasítás** vagy **data_sensitivity_information** értéke több mint 4000 karakterből áll, az első 4000 karakternél hosszabb adatok csonkítva lesznek, **és nem lesznek naplózva**.
A következő szakasz ismerteti a naplózás konfigurációját a Azure Portal használatával.

  > [!NOTE]
  > - Egy szüneteltetett dedikált SQL-készlet naplózásának engedélyezése nem lehetséges. A naplózás engedélyezéséhez szüntesse meg a dedikált SQL-készlet szüneteltetését. További információ a [DEDIKÁLT SQL-készletről](../..//synapse-analytics/sql/best-practices-dedicated-sql-pool.md).
  > - Ha a naplózás a Azure Portal vagy a PowerShell-parancsmaggal egy Log Analytics munkaterületre vagy egy még hub-célhelyre van konfigurálva, akkor a rendszer a "SQLSecurityAuditEvents" kategória használatával hozza létre a [diagnosztikai beállításokat](../../azure-monitor/essentials/diagnostic-settings.md) .

1. Nyissa meg az [Azure Portal](https://portal.azure.com).
2. Navigáljon a **naplózás** elemre az SQL- **adatbázis** vagy az **SQL Server** -ablaktábla biztonsági fejléce alatt.
3. Ha a kiszolgáló naplózási szabályzatát szeretné beállítani, akkor az adatbázis naplózása lapon kiválaszthatja a **kiszolgáló beállításainak megtekintése** hivatkozást. Ezután megtekintheti vagy módosíthatja a kiszolgáló naplózási beállításait. A kiszolgáló naplózási házirendjei a kiszolgálón lévő összes meglévő és újonnan létrehozott adatbázisra érvényesek.

    ![Az adatbázis-naplózás lapon a kiszolgáló beállításainak megtekintése hivatkozás jelenik meg.](./media/auditing-overview/2_auditing_get_started_server_inherit.png)

4. Ha azt szeretné, hogy az adatbázis szintjén engedélyezze a naplózást, kapcsolja be a **naplózást** **a** következőre:. Ha a kiszolgáló naplózása engedélyezve van, az adatbázis által konfigurált naplózás párhuzamosan, a kiszolgáló auditálásával fog létezni.

5. Több lehetőség is van annak konfigurálására, hogy a naplók hol lesznek írva. Naplókat írhat egy Azure Storage-fiókba, Log Analytics munkaterületre Azure Monitor naplók általi felhasználáshoz, vagy az Event hub használatával történő felhasználásra. Ezen beállítások bármely kombinációját konfigurálhatja, és a rendszer a naplókat is megírja a naplókba.
  
   ![tárolási beállítások](./media/auditing-overview/auditing-select-destination.png)

### <a name="auditing-of-microsoft-support-operations"></a><a id="auditing-of-microsoft-support-operations"></a>Microsoft ügyfélszolgálata műveletek naplózása

Az Azure SQL Server Microsoft ügyfélszolgálata műveleteinek naplózása lehetővé teszi a Microsoft támogatási mérnökök műveleteinek naplózását, amikor egy támogatási kérelem során hozzá kell férniük a kiszolgálóhoz. Ennek a képességnek a használata a naplózással együtt nagyobb átláthatóságot biztosít a munkaerő számára, és lehetővé teszi a rendellenességek észlelését, a trendek megjelenítését és az adatveszteség-megelőzést.

A Microsoft ügyfélszolgálata műveletek naplózásának engedélyezéséhez navigáljon a **naplózás** elemre az **Azure SQL Server** paneljének biztonsági fejléce alatt, és váltson a **Microsoft támogatási műveleteinek naplózására** **a** következőre:.

  > [!IMPORTANT]
  > A Microsoft támogatási műveleteinek naplózása nem támogatja a Storage-fiók célhelyét. A képesség engedélyezéséhez be kell állítani egy Log Analytics munkaterületet vagy egy Event hub-célhelyet.

![Képernyőkép a Microsoft ügyfélszolgálata műveletekről](./media/auditing-overview/support-operations.png)

A Log Analytics munkaterületen Microsoft ügyfélszolgálata műveletek naplóinak áttekintéséhez használja a következő lekérdezést:

```kusto
AzureDiagnostics
| where Category == "DevOpsOperationsAudit"
```

### <a name="audit-to-storage-destination"></a><a id="audit-storage-destination"></a>Naplózás a tárolási célhelyre

A naplók egy Storage-fiókba való írásának konfigurálásához válassza a **tárterület** lehetőséget a **naplózás** szakaszhoz. Válassza ki azt az Azure Storage-fiókot, ahol a rendszer menti a naplókat, majd válassza ki a megőrzési időszakot a **Speciális tulajdonságok** megnyitásával. Ezután kattintson a **Mentés** gombra. A megőrzési időtartamnál régebbi naplók törlődnek.

- A megőrzési időtartam alapértelmezett értéke 0 (korlátlan megőrzés). Ez az érték módosítható úgy, hogy a Storage-fiók naplózáshoz való konfigurálásakor áthelyezi a **Speciális tulajdonságok** **megőrzés (nap)** csúszkáját.
  - Ha a megőrzési időszakot 0 (korlátlan megőrzés) értékre módosítja bármely más értékre, vegye figyelembe, hogy az adatmegőrzés csak a megőrzési érték módosítását követően írt naplókra vonatkozik

  ![tárfiók](./media/auditing-overview/auditing_select_storage.png)

### <a name="audit-to-log-analytics-destination"></a><a id="audit-log-analytics-destination"></a>Naplózás Log Analytics célhelyre
  
A naplók Log Analytics munkaterületre való írásának konfigurálásához válassza a **log Analytics** lehetőséget, majd nyissa meg **log Analytics részleteit**. Válassza ki a Log Analytics munkaterületet, ahol a naplók meg lesznek írva, majd kattintson **az OK** gombra. Ha nem hozott létre Log Analytics munkaterületet, tekintse meg a [log Analytics munkaterület létrehozása a Azure Portalben](../../azure-monitor/logs/quick-create-workspace.md)című témakört.

   ![LogAnalyticsworkspace](./media/auditing-overview/auditing_select_oms.png)

További információ a Azure Monitor Log Analytics munkaterületről: [a Azure monitor naplók üzembe helyezésének megtervezése](../../azure-monitor/logs/design-logs-deployment.md)
   
### <a name="audit-to-event-hub-destination"></a><a id="audit-event-hub-destination"></a>Naplózás az Event hub célhelyére

Ha be szeretné állítani a naplók írását az Event hubhoz, válassza az **Event hub** elemet. Válassza ki az Event hub-t, ahol a naplók meg lesznek írva, majd kattintson a **Save (Mentés**) gombra. Ügyeljen arra, hogy az Event hub ugyanabban a régióban legyen, mint az adatbázis és a kiszolgáló.

   ![Eventhub](./media/auditing-overview/auditing_select_event_hub.png)

## <a name="analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>Naplózási naplók és jelentések elemzése

Ha úgy döntött, hogy naplókat ír Azure Monitor naplókba:

- Használja az [Azure Portalt](https://portal.azure.com). Nyissa meg a vonatkozó adatbázist. Az adatbázis **naplózási** lapjának felső részén válassza a **naplók megtekintése** lehetőséget.

    ![naplók megtekintése](./media/auditing-overview/auditing-view-audit-logs.png)

- Ezt követően két módon megtekintheti a naplókat:

    Ha a **naplózási rekordok** lap tetején lévő **log Analytics** gombra kattint, megnyílik a naplók nézet a log Analytics munkaterületen, ahol testreszabhatja az időtartományt és a keresési lekérdezést.

    ![Megnyitás Log Analytics munkaterületen](./media/auditing-overview/auditing-log-analytics.png)

    Ha a **naplózási rekordok** oldal tetején található **irányítópult megtekintése** elemre kattint, megnyílik egy irányítópult, amely a naplók információit jeleníti meg, ahol a biztonsági elemzéseket, a bizalmas adatokhoz való hozzáférést és más adatokat is megtekintheti. Ez az irányítópult úgy lett kialakítva, hogy segítséget nyújtson az adataihoz szükséges biztonsági információk megszerzésében.
    Testreszabhatja az időtartományt és a keresési lekérdezést is.
    ![Log Analytics-irányítópult megtekintése](media/auditing-overview/auditing-view-dashboard.png)

    ![Log Analytics irányítópult](media/auditing-overview/auditing-log-analytics-dashboard.png)

    ![Log Analytics biztonsági ismeretek](media/auditing-overview/auditing-log-analytics-dashboard-data.png)

- Azt is megteheti, hogy a naplókat a Log Analytics panelről is elérheti. Nyissa meg Log Analytics munkaterületét, és az **általános** szakaszban kattintson a **naplók** elemre. Egy egyszerű lekérdezéssel kezdheti meg a naplókat, például a *következőt: Search "SQLSecurityAuditEvents"* .
    Itt is használhatja [Azure monitor naplókat](../../azure-monitor/logs/log-query-overview.md)  a speciális keresések futtatásához a naplózási napló adatain. A Azure Monitor naplók valós idejű üzemeltetési elemzéseket biztosítanak az integrált keresés és az egyéni irányítópultok használatával, amelyekkel a munkaterhelések és a kiszolgálók több millió rekordját is könnyedén elemezheti. További hasznos információk Azure Monitor naplók keresési nyelvéről és parancsairól: [Azure monitor naplók keresési referenciája](../../azure-monitor/logs/log-query-overview.md).

Ha úgy döntött, hogy naplókat ír az Event hub-ba:

- Az Event hub naplózási adatainak felhasználása érdekében be kell állítania egy streamet az események felhasználásához, és egy célhoz kell írnia azokat. További információ: [Azure Event Hubs dokumentáció](../index.yml).
- Az Event hub naplófájljai az [Apache Avro](https://avro.apache.org/) -események törzsében vannak rögzítve, és az UTF-8 kódolást használó JSON-formázással vannak tárolva. A naplók olvasásához [Avro-eszközöket](../../event-hubs/event-hubs-capture-overview.md#use-avro-tools) vagy a formátum feldolgozására képes hasonló eszközöket használhat.

Ha úgy döntött, hogy naplózza a naplókat egy Azure Storage-fiókba, a naplók megtekintéséhez több módszer is használható:

- A rendszer összesíti a naplókat a telepítés során kiválasztott fiókban. A naplókat a [Azure Storage Explorer](https://storageexplorer.com/)eszközzel is megismerheti. Az Azure Storage-ban a naplózási naplók a **sqldbauditlogs** nevű tárolóban lévő blob-fájlok gyűjteményében lesznek mentve. A tárolási mappák hierarchiájának, az elnevezési konvencióknak és a napló formátumának további részleteiért tekintse meg a [SQL Database a naplózási napló formátumát](./audit-log-format.md).

- Használja az [Azure Portalt](https://portal.azure.com).  Nyissa meg a vonatkozó adatbázist. Az adatbázis **naplózási** lapjának felső részén kattintson a **naplók megtekintése** elemre.

    ![naplók megtekintése](./media/auditing-overview/auditing-view-audit-logs.png)

    Megnyílik a **naplózási rekordok** , amelyekről megtekintheti a naplókat.

  - Az egyes dátumokat a **naplózási rekordok** lap tetején található **szűrő** gombra kattintva tekintheti meg.
  - A naplózási **forrás** váltásával válthat a *kiszolgálói naplózási házirend* és az adatbázis- *naplózási házirend* által létrehozott naplózási rekordok között.

       ![A naplózási rekordok megtekintésének beállításait bemutató képernyőkép.]( ./media/auditing-overview/8_auditing_get_started_blob_audit_records.png)

- A rendszerfunkció **sys.fn_get_audit_file** (T-SQL) segítségével táblázatos formátumban visszaküldheti a naplózási adatokat. További információ a függvény használatáról: [sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- A **naplófájlok egyesítése** SQL Server Management Studio (a SSMS 17-től kezdődően):
    1. A SSMS menüben válassza a **fájl**  >  **nyitott**  >  **fájlok egyesítése naplózása** elemet.

        ![A fájlok egyesítése menüpontot megjelenítő képernyőkép.](./media/auditing-overview/9_auditing_get_started_ssms_1.png)
    2. Megnyílik a naplófájlok **hozzáadása** párbeszédpanel. Válassza ki a **hozzáadási** lehetőségek egyikét, és válassza ki, hogy szeretné-e egyesíteni a naplófájlokat egy helyi lemezről, vagy importálni őket az Azure Storage-ból. Meg kell adnia az Azure Storage adatait és a fiók kulcsát.

    3. Miután az összes egyesíteni kívánt fájl hozzá lett adva, kattintson az **OK** gombra az egyesítési művelet befejezéséhez.

    4. Az egyesített fájl megnyílik a SSMS-ben, ahol megtekintheti és elemezheti, valamint exportálhatja egy XEL vagy CSV-fájlba vagy egy táblába.

- Power BI használata. Megtekintheti és elemezheti a naplózási naplóban tárolt adatPower BI. További információért és a letölthető sablonok eléréséhez tekintse meg a [naplózási napló adatainak elemzése Power BIban](https://techcommunity.microsoft.com/t5/azure-database-support-blog/sql-azure-blob-auditing-basic-power-bi-dashboard/ba-p/368895)című témakört.
- Töltse le a naplófájlokat az Azure Storage blob-tárolóból a portálon keresztül, vagy egy eszköz, például [Azure Storage Explorer](https://storageexplorer.com/)használatával.
  - A naplófájl helyi letöltése után kattintson duplán a fájlra a SSMS található naplók megnyitásához, megtekintéséhez és elemzéséhez.
  - Azure Storage Explorer használatával egyszerre több fájlt is letölthet. Ehhez kattintson a jobb gombbal egy adott almappára, és válassza a **Mentés másként** lehetőséget a helyi mappában való mentéshez.

- További metódusok:

  - Több fájl vagy a naplófájlokat tartalmazó almappa letöltése után helyileg egyesítheti azokat a korábban ismertetett SSMS-egyesítési naplófájlok című részben leírtak szerint.
  - A blob-naplózási naplók programozott módon történő megtekintése: [kiterjesztett események fájljainak lekérdezése](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) a PowerShell használatával.

## <a name="production-practices"></a><a id="production-practices"></a>Üzemi eljárások


### <a name="auditing-geo-replicated-databases"></a>Földrajzilag replikált adatbázisok naplózása

A földrajzilag replikált adatbázisok esetében, ha engedélyezi a naplózást az elsődleges adatbázison, a másodlagos adatbázis azonos naplózási házirenddel fog rendelkezni. A naplózás a másodlagos adatbázison is beállítható úgy, hogy az elsődleges adatbázistól függetlenül engedélyezi a naplózást a **másodlagos kiszolgálón**.

- Kiszolgáló szintű (**ajánlott**): a naplózás bekapcsolása mind az **elsődleges** , mind a **másodlagos kiszolgálón** – az elsődleges és másodlagos adatbázisok egymástól függetlenül lesznek naplózva a megfelelő kiszolgálói szintű házirend alapján.
- Adatbázis szintje: a másodlagos adatbázisok adatbázis-szintű naplózása csak az elsődleges adatbázis naplózási beállításaiból állítható be.
  - A naplózást engedélyezni kell az *elsődleges adatbázison*, nem pedig a kiszolgálón.
  - Miután az elsődleges adatbázison engedélyezte a naplózást, a másodlagos adatbázison is engedélyezve lesz.

    > [!IMPORTANT]
    > Az adatbázis szintű naplózással a másodlagos adatbázis tárolási beállításai azonosak lesznek az elsődleges adatbázissal, ami régiók közötti forgalmat okoz. Javasoljuk, hogy csak a kiszolgálói szintű naplózást engedélyezze, és hagyja letiltani az adatbázis-szintű naplózást minden adatbázis esetében.

### <a name="storage-key-regeneration"></a>Tárolási kulcs újragenerálása

Éles környezetben valószínűleg rendszeresen frissíti a tárolási kulcsokat. A naplók Azure Storage-ba írásakor újra kell mentenie a naplózási szabályzatot a kulcsok frissítésekor. A folyamat a következő:

1. Nyissa meg a **speciális tulajdonságokat** a **tárterület** területen. A **tároló-hozzáférési kulcs** mezőben válassza a **másodlagos** lehetőséget. Ezután kattintson a **Mentés** gombra a naplózási konfiguráció lap tetején.

    ![A másodlagos Storage-hozzáférési kulcs kiválasztásának folyamatát bemutató képernyőkép.](./media/auditing-overview/5_auditing_get_started_storage_key_regeneration.png)
2. Nyissa meg a tárolási konfiguráció lapot, és újból létrehozza az elsődleges hozzáférési kulcsot.

    ![Navigációs panel](./media/auditing-overview/6_auditing_get_started_regenerate_key.png)
3. Lépjen vissza a naplózási konfiguráció lapra, váltson másodlagosról elsődlegesre a Storage-hozzáférési kulcsra, majd kattintson **az OK** gombra. Ezután kattintson a **Mentés** gombra a naplózási konfiguráció lap tetején.
4. Lépjen vissza a tárolási konfiguráció lapra, és generálja újra a másodlagos elérési kulcsot (felkészülés a következő kulcs frissítési ciklusára).

## <a name="manage-azure-sql-database-auditing"></a><a id="manage-auditing"></a>Azure SQL Database naplózás kezelése

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

**PowerShell-parancsmagok (beleértve a további szűrések where záradékának támogatását)**:

- [Adatbázis-naplózási szabályzat létrehozása vagy frissítése (set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Kiszolgáló naplózási szabályzatának létrehozása vagy frissítése (set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [Adatbázis naplózási szabályzatának beolvasása (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Kiszolgáló naplózási szabályzatának beolvasása (Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [Adatbázis-naplózási házirend eltávolítása (remove-AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Kiszolgáló naplózási házirendjének eltávolítása (remove-AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

A parancsfájlokra példa: a [naplózás és a veszélyforrások észlelésének konfigurálása a PowerShell használatával](scripts/auditing-threat-detection-powershell-configure.md).

### <a name="using-rest-api"></a>A REST API használata

**REST API**:

- [Adatbázis-naplózási házirend létrehozása vagy frissítése](/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Kiszolgáló naplózási szabályzatának létrehozása vagy frissítése](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Adatbázis naplózási szabályzatának beolvasása](/rest/api/sql/database%20auditing%20settings/get)
- [Kiszolgáló naplózási szabályzatának beolvasása](/rest/api/sql/server%20auditing%20settings/get)

Kiterjesztett szabályzat a WHERE záradék támogatásával további szűréshez:

- [Adatbázis *kiterjesztett* naplózási szabályzatának létrehozása vagy frissítése](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Kiszolgáló *kiterjesztett* naplózási szabályzatának létrehozása vagy frissítése](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Adatbázis *kiterjesztett* naplózási szabályzatának beolvasása](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Kiszolgáló *kiterjesztett* naplózási szabályzatának beolvasása](/rest/api/sql/server%20auditing%20settings/get)

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

- [Kiszolgáló naplózási házirendjének kezelése](/cli/azure/sql/server/audit-policy)
- [Adatbázis naplózási szabályzatának kezelése](/cli/azure/sql/db/audit-policy)

### <a name="using-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok használata

A Azure SQL Database naplózást [Azure Resource Manager](../../azure-resource-manager/management/overview.md) sablonok használatával felügyelheti, ahogy az alábbi példákban is látható:

- [Azure SQL Database üzembe helyezése naplózással, amely lehetővé teszi a naplók írását az Azure Blob Storage-fiókba](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Olyan Azure SQL Database üzembe helyezése, amely lehetővé teszi a naplózást, hogy naplókat írjon a Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Olyan Azure SQL Database üzembe helyezése, amely lehetővé teszi a naplózást, hogy naplókat írjon a Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> A csatolt minták egy külső nyilvános tárházban találhatók, és az "adott állapotban", szavatosság nélkül, és semmilyen Microsoft támogatási program vagy szolgáltatás nem támogatott.
