---
title: Azure Storage Analytics-naplózás
description: Storage Analytics használatával naplózhatja az Azure Storage-kérelmek részleteit. Megtekintheti a naplózott kérelmeket, a naplók tárolási módját, a tárolási naplózás engedélyezését és egyebeket.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: f1d254eecc41ebef690b4fc9f8294bee5a368ae4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570026"
---
# <a name="azure-storage-analytics-logging"></a>Azure Storage Analytics-naplózás

A Storage Analytics naplózza a tárolási szolgáltatásoknak elküldött sikeres és sikertelen kérelmek részletes információit. Ezekkel az információkkal monitorozhatók az egyes kérelmek és diagnosztizálhatók a tárolási szolgáltatások problémái. A kéréseket a rendszer a lehető legjobb módon naplózza.

> [!NOTE]
> Azt javasoljuk, hogy Storage Analytics naplók helyett Azure Monitor Azure Storage-naplókat használjon. Az Azure Monitor Azure Storage-naplók nyilvános előzetes verzióban érhetők el, és elérhetők az előzetes teszteléshez az összes nyilvános felhőben. Ez az előzetes verzió lehetővé teszi a Blobok (köztük a Azure Data Lake Storage Gen2), a fájlok, a várólisták és a táblák naplófájljainak naplózását. További információt a következő cikkekben talál:
>
> - [Az Azure Blob Storage figyelése](../blobs/monitor-blob-storage.md)
> - [Figyelés Azure Files](../files/storage-files-monitoring.md)
> - [Az Azure Queue Storage figyelése](../queues/monitor-queue-storage.md)
> - [Az Azure Table Storage figyelése](../tables/monitor-table-storage.md)

 A Storage Analytics-naplózás alapértelmezés szerint nincs engedélyezve a Storage-fiókjához. A [Azure Portal](https://portal.azure.com/) vagy a PowerShell vagy az Azure CLI használatával engedélyezheti. Részletes útmutatásért lásd: [Azure Storage Analytics naplók engedélyezése és kezelése (klasszikus)](manage-storage-analytics-logs.md). 

A Storage Analytics-naplókat programozott módon is engedélyezheti a REST API vagy az ügyféloldali kódtár használatával. A [blob szolgáltatás](/rest/api/storageservices/Blob-Service-REST-API)tulajdonságainak beolvasása, a [várólista-szolgáltatás tulajdonságainak](/rest/api/storageservices/Get-Queue-Service-Properties)beolvasása és a [Table Service tulajdonságok beolvasása](/rest/api/storageservices/Get-Table-Service-Properties) műveletekkel engedélyezheti az egyes szolgáltatások Storage Analyticsét. Ha egy olyan példát szeretne látni, amely lehetővé teszi Storage Analytics naplók használatát a .NET használatával, lásd: [naplók engedélyezése](manage-storage-analytics-logs.md)

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
     $_.Name -match 'blob/2014/05/21/05' -and   
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


## <a name="next-steps"></a>Következő lépések

* [Azure Storage Analytics naplók engedélyezése és kezelése (klasszikus)](manage-storage-analytics-logs.md)
* [Storage Analytics naplózási formátum](/rest/api/storageservices/storage-analytics-log-format)
* [Naplózott műveletek és állapotüzenetek Storage Analytics](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Storage Analytics metrikák (klasszikus)](storage-analytics-metrics.md)
