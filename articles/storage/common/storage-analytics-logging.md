---
title: Azure Storage Analytics-naplózás
description: Storage Analytics használatával naplózhatja az Azure Storage-kérelmek részleteit. Megtekintheti a naplózott kérelmeket, a naplók tárolási módját, a tárolási naplózás engedélyezését és egyebeket.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 07/23/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: f0f9832a8128a447970535f18cceca3cd4dccc69
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880253"
---
# <a name="azure-storage-analytics-logging"></a>Azure Storage Analytics-naplózás

A Storage Analytics naplózza a tárolási szolgáltatásoknak elküldött sikeres és sikertelen kérelmek részletes információit. Ezekkel az információkkal monitorozhatók az egyes kérelmek és diagnosztizálhatók a tárolási szolgáltatások problémái. A kéréseket a rendszer a lehető legjobb módon naplózza.

 A Storage Analytics-naplózás alapértelmezés szerint nincs engedélyezve a Storage-fiókjához. Engedélyezheti a [Azure Portal](https://portal.azure.com/); További információ: [Storage-fiók figyelése a Azure Portalban](./storage-monitor-storage-account.md). Storage Analytics programozott módon is engedélyezheti a REST API vagy az ügyféloldali kódtár használatával. A [blob szolgáltatás](/rest/api/storageservices/Blob-Service-REST-API)tulajdonságainak beolvasása, a [várólista-szolgáltatás tulajdonságainak](/rest/api/storageservices/Get-Queue-Service-Properties)beolvasása és a [Table Service tulajdonságok beolvasása](/rest/api/storageservices/Get-Table-Service-Properties) műveletekkel engedélyezheti az egyes szolgáltatások Storage Analyticsét.

 A naplóbejegyzések csak akkor jönnek létre, ha a szolgáltatás-végpontra irányuló kérések történnek. Ha például egy Storage-fiók egy tevékenységgel rendelkezik a blob-végponton, de nem a táblázat-vagy várólista-végpontokon, akkor csak a Blob servicehoz tartozó naplók lesznek létrehozva.

> [!NOTE]
>  A Storage Analytics-naplózás jelenleg csak a Blob, a Queue és a Table szolgáltatás esetében érhető el. Storage Analytics naplózás a prémium szintű teljesítményű [BlockBlobStorage](../blobs/storage-blob-create-account-block-blob.md) -fiókok esetében is elérhető. Azonban nem érhető el a prémium szintű teljesítményű általános célú v2-fiókok esetében.

## <a name="requests-logged-in-logging"></a>Naplózásban bejelentkezett kérelmek
### <a name="logging-authenticated-requests"></a>Hitelesített kérelmek naplózása

 A hitelesített kérések alábbi típusai vannak rögzítve:

- Sikeres kérések
- Sikertelen kérések, köztük az időtúllépések, torlódások, valamint a hálózati, hitelesítési és egyéb hibák
- Közös hozzáférésű jogosultságkódot vagy OAuth-hitelesítést használó sikertelen és sikeres kérések
- Elemzési adatokhoz kapcsolódó kérelmek

  A Storage Analytics saját maga által kezdeményezett kérelmeket (például a naplózás létrehozását vagy törlését) nem naplózza a rendszer. A naplózott adatok teljes listáját az [Storage Analytics naplózott műveletek és állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) , valamint a [Storage Analytics naplózási formátum](/rest/api/storageservices/storage-analytics-log-format) témakörök ismertetik.

### <a name="logging-anonymous-requests"></a>Névtelen kérelmek naplózása

 A rendszer a következő típusú névtelen kérelmeket naplózza:

- Sikeres kérések
- Kiszolgálóhibák
- Időtúllépési hibák az ügyfél és a kiszolgáló esetében
- 304-es (Nincs módosítva) hibakóddal ellátott sikertelen GET-kérések

  Az összes többi sikertelen névtelen kérelmet nem naplózza a rendszer. A naplózott adatok teljes listáját az [Storage Analytics naplózott műveletek és állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) , valamint a [Storage Analytics naplózási formátum](/rest/api/storageservices/storage-analytics-log-format) témakörök ismertetik.

## <a name="how-logs-are-stored"></a>A naplók tárolása

A rendszer az összes naplót blokkoló blobokban tárolja egy nevű tárolóban `$logs` , amely automatikusan létrejön, amikor a Storage Analytics engedélyezve van egy Storage-fiókhoz. A `$logs` tároló a Storage-fiók blob-névterében található, például: `http://<accountname>.blob.core.windows.net/$logs` . Ez a tároló nem törölhető, ha Storage Analytics engedélyezve lett, de a tartalma törölhető. Ha a Storage-Browsing eszköz használatával közvetlenül a tárolóra navigál, a naplózási adatait tartalmazó Blobok jelennek meg.

> [!NOTE]
>  A tároló `$logs` nem jelenik meg, ha egy tároló-listaelemet hajt végre, például a tárolók listázása műveletet. Közvetlenül elérhetőnek kell lennie. Használhatja például a Blobok listázása műveletet a tárolóban lévő Blobok eléréséhez `$logs` .

Mivel a rendszer naplózza a kérelmeket, Storage Analytics a köztes eredményeket blokkként tölti fel. A Storage Analytics rendszeresen véglegesíti ezeket a blokkokat, és blobként elérhetővé teszi őket. Akár egy óráig is eltarthat, amíg a naplófájlok megjelennek a **$logs** tárolóban lévő blobokban, mert a tárolási szolgáltatás által használt gyakoriság a napló-írókat üríti. Ismétlődő rekordok létezhetnek ugyanabban az órában létrehozott naplókhoz. A **kérelemazonosító** és a **művelet** számának ellenőrzésével megállapíthatja, hogy egy rekord duplikált-e.

Ha nagy mennyiségű naplózási adata van, és minden órában több fájl található, akkor a blob metaadataival meghatározhatja, hogy a napló mely adatokat tartalmazza a blob metaadatainak mezőinek vizsgálatával. Ez azért is hasznos, mert időnként előfordulhat, hogy az adatok a naplófájlokban vannak írva: a blob metaadatai a blob nevénél pontosabban jelzik a blob tartalmát.

A legtöbb tárolási böngészési eszköz lehetővé teszi a Blobok metaadatainak megtekintését; ezeket az információkat a PowerShell vagy programozott módon is elolvashatja. A következő PowerShell-kódrészlet egy példa a naplók listájának szűrésére a név alapján egy idő megadásához, valamint a metaadatok alapján, hogy csak az **írási** műveleteket tartalmazó naplókat azonosítsák.  

 ```powershell
 Get-AzStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

A Blobok programozott listázásával kapcsolatos információkért lásd: [blob-erőforrások enumerálása](/rest/api/storageservices/Enumerating-Blob-Resources) , [Tulajdonságok és metaadatok beállítása és lekérése blob-erőforrásokhoz](/rest/api/storageservices/Setting-and-Retrieving-Properties-and-Metadata-for-Blob-Resources).  

### <a name="log-naming-conventions"></a>Napló elnevezési konvenciói

 Minden napló a következő formátumban lesz megírva:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 A következő táblázat a napló neve egyes attribútumait ismerteti:

|Attribútum|Leírás|
|---------------|-----------------|
|`<service-name>`|A tárolási szolgáltatás neve. Például: `blob` , `table` , vagy `queue`|
|`YYYY`|A naplófájl négyjegyű éve. Például: `2011`|
|`MM`|A napló két számjegyű hónapja. Például: `07`|
|`DD`|A napló két számjegyből álló napja. Például: `31`|
|`hh`|A két számjegyű óra, amely a naplók kezdő óráját jelzi 24 órás UTC formátumban. Például: `18`|
|`mm`|A naplók kezdő percét jelző kétszámjegyű szám. **Megjegyzés:**  Ez az érték nem támogatott a Storage Analytics aktuális verziójában, és az értéke mindig lesz `00` .|
|`<counter>`|Egy nulla-alapú számláló hat számjegytel, amely a tárolási szolgáltatáshoz egy órás időszakban generált naplók számát jelzi. Ez a számláló a következő időpontban indul: `000000` . Például: `000001`|

 A következő egy teljes minta-napló neve, amely a fenti példákat ötvözi:

 `blob/2011/07/31/1800/000001.log`

 A következő példa egy URI-t használ a fenti napló eléréséhez:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 Tárolási kérelem naplózásakor az eredményül kapott napló neve a kért művelet befejezésének órájával összefügg. Ha például egy GetBlob-kérelem 6: a 7/31/2011-kor fejeződött be, a naplót a következő előtaggal kell megírni: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Naplózási metaadatok

 Minden naplózási blobot metaadatok tárolnak, amelyek segítségével azonosíthatók a blob által tartalmazott naplózási adatok. Az alábbi táblázat az egyes metaadatok attribútumait ismerteti:

|Attribútum|Leírás|
|---------------|-----------------|
|`LogType`|Leírja, hogy a napló tartalmazza-e az olvasási, írási és törlési műveletekkel kapcsolatos információkat. Ez az érték egy típust vagy mindhárom kombinációt tartalmazhat, vesszővel elválasztva.<br /><br /> 1. példa: `write`<br /><br /> 2. példa: `read,write`<br /><br /> 3. példa: `read,write,delete`|
|`StartTime`|A napló bejegyzéseinek legkorábbi időpontja a ( `YYYY-MM-DDThh:mm:ssZ` ) formában. Például: `2011-07-31T18:21:46Z`|
|`EndTime`|A naplóba való bejegyzés legkésőbbi időpontja `YYYY-MM-DDThh:mm:ssZ` . Például: `2011-07-31T18:22:09Z`|
|`LogVersion`|A naplózási formátum verziója.|

 A következő lista a fenti példák alapján mutatja be a teljes minta metaadatokat:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>Tárolók naplózásának engedélyezése

Azure Portal, PowerShell és Storage SDK-k használatával engedélyezheti a tárolók naplózását.

### <a name="enable-storage-logging-using-the-azure-portal"></a>A tárolók naplózásának engedélyezése a Azure Portal használatával  

A Azure Portal használja a **diagnosztikai beállítások (klasszikus)** panelt a tárolási naplózás vezérléséhez, amely a Storage-fiók **menü** paneljének **figyelés (klasszikus)** szakaszában érhető el.

Megadhatja a naplózni kívánt tárolási szolgáltatásokat, valamint a naplózott adatok megőrzési időtartamát (napokban).  

### <a name="enable-storage-logging-using-powershell"></a>Tárolók naplózásának engedélyezése a PowerShell használatával  

 A helyi gépen a PowerShell használatával konfigurálhatja a Storage-fiók tárolási naplózását, ha a **Get-AzStorageServiceLoggingProperty** Azure PowerShell parancsmaggal szeretné lekérni az aktuális beállításokat, valamint a **set-AzStorageServiceLoggingProperty** parancsmagot az aktuális beállítások módosításához.  

 A tárolási naplózást vezérlő parancsmagok olyan **LoggingOperations** paramétert használnak, amely a naplózandó kérelmek vesszővel tagolt listáját tartalmazó karakterlánc. A három lehetséges kérelem típusa: **olvasás**, **írás** és **Törlés**. A naplózás kikapcsolásához használja a **none** értéket a **LoggingOperations** paraméternél.  

 A következő parancs bekapcsolja az olvasási, írási és törlési kérelmek naplózását az alapértelmezett Storage-fiókban lévő Queue szolgáltatásban, a megőrzési érték pedig öt napig:  

```powershell
Set-AzStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 A következő parancs kikapcsolja az alapértelmezett Storage-fiókban lévő Table szolgáltatás naplózását:  

```powershell
Set-AzStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 Az Azure PowerShell-parancsmagoknak az Azure-előfizetéssel való használatához való konfigurálásával és a használni kívánt alapértelmezett Storage-fiók kiválasztásával kapcsolatos információkért lásd: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/).  

### <a name="enable-storage-logging-programmatically"></a>A tárolási naplózás engedélyezése programozott módon  

 Amellett, hogy a Azure Portal vagy a Azure PowerShell parancsmagokat használja a tárolási naplózás vezérlésére, használhatja az egyik Azure Storage API-t is. Ha például .NET nyelvet használ, használhatja a Storage ügyféloldali kódtárat.  

# <a name="net-v12-sdk"></a>[\.NET V12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

---


 További információ a tárolási naplózás konfigurálásához használható .NET-nyelvről: a [Storage ügyféloldali kódtár referenciája](/previous-versions/azure/dn261237(v=azure.100)).  

 A tárolási naplózásnak a REST API használatával történő konfigurálásával kapcsolatos általános információkért lásd: [Storage Analytics engedélyezése és konfigurálása](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).  

## <a name="download-storage-logging-log-data"></a>Storage-naplózási napló adatai letöltése

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

Ha többet szeretne megtudni az egyes fájlok letöltéséről, tekintse meg az [adott fájlok letöltése](./storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#transfer-data)című témakört.

Ha letöltötte a napló adatait, megtekintheti a bejegyzéseket a fájlokban. Ezek a naplófájlok olyan tagolt szöveges formátumot használnak, amelyet számos napló-olvasási eszköz képes elemezni (további információt az útmutató [figyelése, diagnosztizálása és hibaelhárítása Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)) című témakörben talál. A különböző eszközök különböző lehetőségekkel rendelkeznek a naplófájlok tartalmának formázásához, szűréséhez, rendezéséhez, valamint az ad-kereséshez. További információ a tárolási naplózási naplófájl formátumáról és tartalmáról: [Storage Analytics naplózási formátum](/rest/api/storageservices/storage-analytics-log-format) és [Storage Analytics naplózott műveletek és állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>További lépések

* [Storage Analytics naplózási formátum](/rest/api/storageservices/storage-analytics-log-format)
* [Naplózott műveletek és állapotüzenetek Storage Analytics](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Storage Analytics metrikák (klasszikus)](storage-analytics-metrics.md)
