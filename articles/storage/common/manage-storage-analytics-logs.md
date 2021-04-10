---
title: Azure Storage Analytics naplók engedélyezése és kezelése (klasszikus) | Microsoft Docs
description: Megtudhatja, hogyan figyelheti a Storage-fiókot az Azure-ban Azure Storage Analytics használatával.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 0c182e1093c29206d27a0e55a46dd9a5607fa6ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701705"
---
# <a name="enable-and-manage-azure-storage-analytics-logs-classic"></a>Azure Storage Analytics naplók engedélyezése és kezelése (klasszikus)

[Azure Storage Analytics](storage-analytics.md) naplókat biztosít a blobokhoz, várólistákhoz és táblákhoz. A [Azure Portal](https://portal.azure.com) a naplók konfigurálására is használható a fiókjában. Ebből a cikkből megtudhatja, hogyan engedélyezheti és kezelheti a naplókat. A metrikák engedélyezésével kapcsolatos információkért lásd: [Azure Storage Analytics metrikák engedélyezése és kezelése (klasszikus)]().  A Azure Portal megfigyelési adatainak vizsgálatára és tárolására vonatkozó költségekkel jár. További információiért tekintse meg a [Storage Analyticset](storage-analytics.md) bemutató cikket.

> [!NOTE]
> Azt javasoljuk, hogy Storage Analytics naplók helyett Azure Monitor Azure Storage-naplókat használjon. Az Azure Monitor Azure Storage-naplók nyilvános előzetes verzióban érhetők el, és elérhetők az előzetes teszteléshez az összes nyilvános felhőben. Ez az előzetes verzió lehetővé teszi a Blobok (köztük a Azure Data Lake Storage Gen2), a fájlok, a várólisták és a táblák naplófájljainak naplózását. További információt a következő cikkekben talál:
>
> - [Az Azure Blob Storage figyelése](../blobs/monitor-blob-storage.md)
> - [Figyelés Azure Files](../files/storage-files-monitoring.md)
> - [Az Azure Queue Storage figyelése](../queues/monitor-queue-storage.md)
> - [Az Azure Table Storage figyelése](../tables/monitor-table-storage.md)

Az Azure Storage szolgáltatással kapcsolatos problémák azonosítására, diagnosztizálására és hibaelhárítására vonatkozó részletes útmutató a Storage Analytics és egyéb eszközök használatáról: [Microsoft Azure Storage figyelése, diagnosztizálása és hibaelhárítása](storage-monitoring-diagnosing-troubleshooting.md).

<a id="configure-logging"></a>

## <a name="enable-logs"></a>Naplók engedélyezése

A blob-, tábla-és üzenetsor-szolgáltatásokhoz tartozó olvasási, írási és törlési kérelmek esetében az Azure Storage-ba is utasíthatja a diagnosztikai naplók mentését. A beállított adatmegőrzési szabályzat ezekre a naplókra is érvényes.

> [!NOTE]
> Azure Files jelenleg támogatja a Storage Analytics metrikákat, de nem támogatja Storage Analytics naplózását.

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [Azure Portal](https://portal.azure.com)válassza ki a Storage- **fiókok** elemet, majd a Storage-fiók nevét a Storage-fiók panel megnyitásához.

2. Válassza a **diagnosztikai beállítások (klasszikus)** lehetőséget a menü panel **monitorozás (klasszikus)** szakaszában.

    ![Diagnosztika menüpont a Azure Portal figyelés területén.](./media/manage-storage-analytics-logs/storage-enable-metrics-00.png)

3. Győződjön **meg** arról, hogy az **állapot** beállítás be értékre van állítva, majd válassza ki azokat a **szolgáltatásokat** , amelyeknek engedélyezni szeretné a naplózást.

   > [!div class="mx-imgBorder"]
   > ![Konfigurálja a naplózást a Azure Portalban.](./media/manage-storage-analytics-logs/enable-diagnostics.png)    


4. Győződjön meg arról, hogy az **adattörlés** jelölőnégyzet be van jelölve.  Ezután állítsa be, hogy hány nap elteltével szeretné megőrizni a naplózási adatnaplót, ha a csúszka vezérlőelemet a jelölőnégyzet alá helyezi, vagy közvetlenül módosítja a csúszka vezérlőelem melletti szövegmezőben megjelenő értéket. Az új Storage-fiókok alapértelmezett értéke hét nap. Ha nem szeretne adatmegőrzési szabályt beállítani, adja meg a nulla értéket. Ha nincs adatmegőrzési szabály, akkor a rendszer törli a naplózási adatok törlését.

   > [!WARNING]
   > A rendszer a naplókat a fiókjában tárolt adatként tárolja. a naplózási adat idővel felhalmozódhat a fiókban, ami növelheti a tárterület költségeit. Ha a naplózási adatokra csak kis ideig van szükség, csökkentheti az adatmegőrzési szabályzat módosításával járó költségeket. Az elavult naplózási adatok (az adatmegőrzési szabályzatnál régebbi adatok) törlődnek a rendszerből. Azt javasoljuk, hogy állítsa be az adatmegőrzési szabályzatot attól függően, hogy mennyi ideig szeretné megőrizni a fiókja adatait. További információért lásd a [Storage-metrikák számlázása](storage-analytics-metrics.md#billing-on-storage-metrics) című témakört.

4. Kattintson a **Mentés** gombra.

   A diagnosztikai naplókat a rendszer egy *$logs* nevű blob-tárolóba menti a Storage-fiókjában. A naplófájlokat a Storage Explorer, például a [Microsoft Azure Storage Explorer](https://storageexplorer.com)használatával tekintheti meg, vagy programozott módon a Storage ügyféloldali kódtár vagy a PowerShell használatával.

   További információ a $logs tároló eléréséről: [Storage Analytics naplózása](storage-analytics-logging.md).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Nyisson meg egy Windows PowerShell-parancssori ablakot.

2. Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

   ```powershell
   Connect-AzAccount
   ```

3. Ha az identitása egynél több előfizetéshez van társítva, akkor állítsa be az aktív előfizetését.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Cserélje le a `<subscription-id>` helyőrző értékét az előfizetés azonosítójával.

5. Szerezze be a használni kívánt Storage-fiókot meghatározó Storage-fiók környezetét.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Cserélje le a `<resource-group-name>` helyőrző értékét az erőforráscsoport nevére.

   * Cserélje le a `<storage-account-name>` helyőrző értékét a Storage-fiók nevére. 

6. A **set-AzStorageServiceLoggingProperty** használatával módosíthatja az aktuális napló beállításait. A tárolási naplózást vezérlő parancsmagok olyan **LoggingOperations** paramétert használnak, amely a naplózandó kérelmek vesszővel tagolt listáját tartalmazó karakterlánc. A három lehetséges kérelem típusa: **olvasás**, **írás** és **Törlés**. A naplózás kikapcsolásához használja a **none** értéket a **LoggingOperations** paraméternél.  

   A következő parancs bekapcsolja az olvasási, írási és törlési kérelmek naplózását az alapértelmezett Storage-fiókban lévő Queue szolgáltatásban, a megőrzési érték pedig öt napig:  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5 -Context $ctx
   ```  

   > [!WARNING]
   > A rendszer a naplókat a fiókjában tárolt adatként tárolja. a naplózási adat idővel felhalmozódhat a fiókban, ami növelheti a tárterület költségeit. Ha a naplózási adatokra csak kis ideig van szükség, csökkentheti az adatmegőrzési szabályzat módosításával járó költségeket. Az elavult naplózási adatok (az adatmegőrzési szabályzatnál régebbi adatok) törlődnek a rendszerből. Azt javasoljuk, hogy állítsa be az adatmegőrzési szabályzatot attól függően, hogy mennyi ideig szeretné megőrizni a fiókja adatait. További információért lásd a [Storage-metrikák számlázása](storage-analytics-metrics.md#billing-on-storage-metrics) című témakört.
   
   A következő parancs kikapcsolja az alapértelmezett Storage-fiókban lévő Table szolgáltatás naplózását:  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none -Context $ctx 
   ```  

   Az Azure PowerShell-parancsmagoknak az Azure-előfizetéssel való használatához való konfigurálásával és a használni kívánt alapértelmezett Storage-fiók kiválasztásával kapcsolatos információkért lásd: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/).  

### <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

### <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
``` 

---

<a id="modify-retention-policy"></a>

## <a name="modify-log-data-retention-period"></a>Naplózási adatok megőrzési idejének módosítása

A naplózási adat idővel felhalmozódhat a fiókban, ami növelheti a tárterület költségeit. Ha a naplózási adatokra csak kis ideig van szükség, csökkentheti a költségeket a naplózási adatok megőrzési időtartamának módosításával. Ha például csak három napig van szüksége a naplókra, állítsa a naplózási adatok megőrzési idejét értékre `3` . A rendszer a naplókat 3 nap után automatikusan törli a fiókból. Ebből a szakaszból megtudhatja, hogyan tekintheti meg az aktuális napló adatmegőrzési idejét, majd frissítheti az adott időszakot, ha ezt kívánja elvégezni.

> [!NOTE]
> Ezek a lépések csak olyan fiókok esetében érvényesek, amelyeken nincs engedélyezve a **hierarchikus névtér** beállítása. Ha engedélyezte ezt a beállítást a fiókjában, akkor a megőrzési napok beállítása még nem támogatott. Ehelyett manuálisan kell törölnie a naplókat bármilyen támogatott eszközzel, például a Azure Storage Explorer, a REST vagy az SDK használatával. A naplók a Storage-fiókban való megkereséséhez lásd: a [naplók tárolása](storage-analytics-logging.md#how-logs-are-stored).

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [Azure Portal](https://portal.azure.com)válassza ki a Storage- **fiókok** elemet, majd a Storage-fiók nevét a Storage-fiók panel megnyitásához.
2. Válassza a **diagnosztikai beállítások (klasszikus)** lehetőséget a menü panel **monitorozás (klasszikus)** szakaszában.

    ![Diagnosztika menüpont a Azure Portal figyelés területén](./media/manage-storage-analytics-logs/storage-enable-metrics-00.png)

3. Győződjön meg arról, hogy az **adattörlés** jelölőnégyzet be van jelölve.  Ezután állítsa be, hogy hány nap elteltével szeretné megőrizni a naplózási adatnaplót, ha a csúszka vezérlőelemet a jelölőnégyzet alá helyezi, vagy közvetlenül módosítja a csúszka vezérlőelem melletti szövegmezőben megjelenő értéket.

   > [!div class="mx-imgBorder"]
   > ![A Azure Portal megőrzési idejének módosítása](./media/manage-storage-analytics-logs/modify-retention-period.png)

   Az új Storage-fiókok alapértelmezett napi száma hét nap. Ha nem szeretne adatmegőrzési szabályt beállítani, adja meg a nulla értéket. Ha nincs adatmegőrzési szabály, akkor a figyelési adatok törlése is megtörténik.
   
4. Kattintson a **Mentés** gombra.

   A diagnosztikai naplókat a rendszer egy *$logs* nevű blob-tárolóba menti a Storage-fiókjában. A naplófájlokat a Storage Explorer, például a [Microsoft Azure Storage Explorer](https://storageexplorer.com)használatával tekintheti meg, vagy programozott módon a Storage ügyféloldali kódtár vagy a PowerShell használatával.

   További információ a $logs tároló eléréséről: [Storage Analytics naplózása](storage-analytics-logging.md).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Nyisson meg egy Windows PowerShell-parancssori ablakot.

2. Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

   ```powershell
   Connect-AzAccount
   ```

3. Ha az identitása egynél több előfizetéshez van társítva, akkor állítsa be az aktív előfizetését.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Cserélje le a `<subscription-id>` helyőrző értékét az előfizetés azonosítójával.

5. Szerezze be a Storage-fiókot definiáló Storage-fiók környezetét.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Cserélje le a `<resource-group-name>` helyőrző értékét az erőforráscsoport nevére.

   * Cserélje le a `<storage-account-name>` helyőrző értékét a Storage-fiók nevére. 

6. A [Get-AzStorageServiceLoggingProperty](/powershell/module/az.storage/get-azstorageserviceloggingproperty) használatával tekintheti meg az aktuális napló adatmegőrzési szabályzatát. A következő példa a konzolra nyomtatja a blob és a várólista tárolási szolgáltatásainak megőrzési idejét.

   ```powershell
   Get-AzStorageServiceLoggingProperty -ServiceType Blob, Queue -Context $ctx
   ```  

   A konzol kimenetében a megőrzési idő az `RetentionDays` oszlop fejléce alatt jelenik meg.

   > [!div class="mx-imgBorder"]
   > ![Adatmegőrzési szabályzat a PowerShell kimenetében](./media/manage-storage-analytics-logs/retention-period-powershell.png)

7. A megőrzési időtartam módosításához használja a [set-AzStorageServiceLoggingProperty](/powershell/module/az.storage/set-azstorageserviceloggingproperty) . A következő példa a megőrzési időtartamot 4 napra módosítja.  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Blob, Queue -RetentionDays 4 -Context $ctx
   ```  

   Az Azure PowerShell-parancsmagoknak az Azure-előfizetéssel való használatához való konfigurálásával és a használni kívánt alapértelmezett Storage-fiók kiválasztásával kapcsolatos információkért lásd: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/).  

### <a name="net-v12"></a>[.NET V12](#tab/dotnet)

A következő példa a konzolra nyomtatja a blob és a várólista tárolási szolgáltatásainak megőrzési idejét.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_ViewRetentionPeriod":::

A következő példa a megőrzési időtartamot 4 napra módosítja. 

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_ModifyRetentionPeriod":::

### <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

A következő példa a konzolra nyomtatja a blob és a várólista tárolási szolgáltatásainak megőrzési idejét.

```csharp
var storageAccount = CloudStorageAccount.Parse(connectionString);

var blobClient = storageAccount.CreateCloudBlobClient();
var queueClient = storageAccount.CreateCloudQueueClient();

var blobserviceProperties = blobClient.GetServiceProperties();
var queueserviceProperties = queueClient.GetServiceProperties();

Console.WriteLine("Retention period for logs from the blob service is: " +
   blobserviceProperties.Logging.RetentionDays.ToString());

Console.WriteLine("Retention period for logs from the queue service is: " +
   queueserviceProperties.Logging.RetentionDays.ToString());
```

Az alábbi példa megváltoztatja a naplófájlok megőrzési időtartamát a blob és a várólista tárolási szolgáltatásai számára 4 napra. 

```csharp

blobserviceProperties.Logging.RetentionDays = 4;
queueserviceProperties.Logging.RetentionDays = 4;

blobClient.SetServiceProperties(blobserviceProperties);
queueClient.SetServiceProperties(queueserviceProperties);  
``` 

---

### <a name="verify-that-log-data-is-being-deleted"></a>A naplófájlok törlésének ellenőrzése

A naplófájlok törlésének ellenőrzéséhez tekintse meg a `$logs` Storage-fiókja tárolójának tartalmát. Az alábbi képen a tároló egyik mappájának tartalma látható `$logs` . A mappa megfelel a január 2021-nek, és minden mappa egy napig tartalmaz naplókat. Ha a mai nap 29. január 2021 volt, és az adatmegőrzési szabályzat csak egy napra van beállítva, akkor ez a mappa csak egy napig tartalmazhat naplókat.

> [!div class="mx-imgBorder"]
> ![A naplófájlok listája az Azure Portalon](./media/manage-storage-analytics-logs/verify-and-delete-logs.png)

<a id="download-storage-logging-log-data"></a>

## <a name="view-log-data"></a>Naplófájlok megtekintése

 A naplófájlok megtekintéséhez és elemzéséhez le kell töltenie azokat a blobokat, amelyek tartalmazzák a helyi számítógép számára érdeklik a napló adatait. Számos Storage-Browsing eszköz lehetővé teszi a Blobok letöltését a Storage-fiókból; a naplófájlok letöltéséhez használhatja az Azure Storage csapata által biztosított parancssori Azure Copy Tool [AzCopy](storage-use-azcopy-v10.md) is.  
 
>[!NOTE]
> A `$logs` tároló nincs integrálva a Event Gridba, így nem fog értesítést kapni a naplófájlok írásakor. 

 Győződjön meg arról, hogy letölti a kívánt naplózási adatnaplót, és hogy ne töltsön le ugyanazokat a naplókat többször:  

-   A naplózási adatokkal rendelkező Blobok dátum-és időelnevezési konvenciójának használatával nyomon követheti, hogy mely Blobok vannak már letöltve az elemzéshez, így elkerülhető, hogy az adatok többször is újra le legyenek töltve.  

-   A naplózási adatokat tartalmazó Blobok metaadatait használva azonosíthatja azt a megadott időszakot, ameddig a blob a naplófájlokban tárolja a letölteni kívánt blob azonosításához szükséges adatokat.  

A AzCopy megkezdéséhez tekintse meg a [AzCopy első lépéseivel](storage-use-azcopy-v10.md) foglalkozó témakört. 

Az alábbi példa bemutatja, hogyan töltheti le a üzenetsor-szolgáltatás naplófájljait a 09., 10:00 és 11. május 20-án, 2014-kor kezdődő órákig.

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

További információ az egyes fájlok letöltéséről: [Blobok letöltése az Azure Blob Storage-ból az AzCopy v10 használatával](./storage-use-azcopy-blobs-download.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Ha letöltötte a napló adatait, megtekintheti a bejegyzéseket a fájlokban. Ezek a naplófájlok olyan tagolt szöveges formátumot használnak, amelyet számos napló-olvasási eszköz képes elemezni (további információt az útmutató [figyelése, diagnosztizálása és hibaelhárítása Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)) című témakörben talál. A különböző eszközök különböző lehetőségekkel rendelkeznek a naplófájlok tartalmának formázásához, szűréséhez, rendezéséhez, valamint az ad-kereséshez. További információ a tárolási naplózási naplófájl formátumáról és tartalmáról: [Storage Analytics naplózási formátum](/rest/api/storageservices/storage-analytics-log-format) és [Storage Analytics naplózott műveletek és állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Következő lépések

* További információ a Storage Analyticsről: [Storage Analytics](storage-analytics.md) Storage Analytics.
* További információ a tárolási naplózás konfigurálásához használható .NET-nyelvről: a [Storage ügyféloldali kódtár referenciája](/previous-versions/azure/dn261237(v=azure.100)). 
* A tárolási naplózásnak a REST API használatával történő konfigurálásával kapcsolatos általános információkért lásd: [Storage Analytics engedélyezése és konfigurálása](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).
* További információ a Storage Analytics naplók formátumáról. Lásd: [Storage Analytics naplózási formátum](/rest/api/storageservices/storage-analytics-log-format).
