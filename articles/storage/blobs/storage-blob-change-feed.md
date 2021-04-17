---
title: Változáscsatorna a Azure Blob Storage | Microsoft Docs
description: Ismerje meg a változáscsatorna naplóit a Azure Blob Storage és használatukról.
author: normesta
ms.author: normesta
ms.date: 02/08/2021
ms.topic: how-to
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 6da83ceb6d8ee51916d25949309d7ddfba0e4b30
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503600"
---
# <a name="change-feed-support-in-azure-blob-storage"></a>Változáscsatorna támogatása a Azure Blob Storage

A változáscsatorna célja, hogy tranzakciónaplókat biztosítson a blobok és a tárfiókban lévő blobok metaadatainak változásairól. A változáscsatorna **rendezett,** garantált **,** **tartós,** nem **módosítható,** csak **olvasható naplót** biztosít a változásokról. Az ügyfélalkalmazások bármikor olvashatják ezeket a naplókat streamelési vagy kötegelt módban. A változáscsatorna segítségével hatékony és skálázható megoldásokat építhet, amelyek alacsony költség mellett Blob Storage a fiókban előforduló változáseseményeket.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-the-change-feed-works"></a>A változáscsatorna működése

A változáscsatorna blobokként tárolód egy speciális tárolóban a tárfiókban standard [blob díjszabási költség](https://azure.microsoft.com/pricing/details/storage/blobs/) mellett. [](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) A fájlok megőrzési időtartama a követelmények alapján szabályozható (lásd [az aktuális](#conditions) kiadás feltételeit). A változási események rekordként vannak hozzáfűzve a változáscsatornához az [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) formátum specifikációban: ez egy kompakt, gyors, bináris formátum, amely beágyazott sémával gazdag adatstruktúrákat biztosít. Ezt a formátumot széles körben használják a Hadoop ökoszisztémájában, Stream Analytics és Azure Data Factory.

Ezeket a naplókat aszinkron módon, növekményesen vagy teljes mértékben is feldolgozhatja. A változáscsatornát függetlenül, párhuzamosan és a saját tempójában bármilyen számú ügyfélalkalmazás olvashatja. Az olyan elemzési alkalmazások, mint az [Apache Drill](https://drill.apache.org/docs/querying-avro-files/) vagy [az Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) közvetlenül Avro-fájlokként használják a naplókat, amelyek segítségével alacsony költséggel, nagy sávszélességgel és egyéni alkalmazás írása nélkül is feldolgozhatja őket.

Az alábbi ábra bemutatja, hogyan megjelenik a rekordok hozzáadása a változáscsatornához:

:::image type="content" source="media/storage-blob-change-feed/change-feed-diagram.png" alt-text="A változáscsatorna működését bemutató ábra, amely a blobok módosításainak rendezett naplóját biztosítja":::

A változáscsatorna-támogatás jól használható olyan forgatókönyvekhez, amelyek módosított objektumokon alapuló adatokat feldolgoznak. Az alkalmazások például a következőt használhatjak:

  - Másodlagos index frissítése, szinkronizálás gyorsítótárral, keresőmotorral vagy más tartalomkezelési forgatókönyvekkel.
  
  - Az objektumokon végrehajtott módosítások alapján kinyerheti az üzleti elemzési betekintő adatokat és metrikákat akár adatfolyammal, akár kötegelt módon.
  
  - Az objektumok módosításainak tárolása, naplózása és elemzése bármilyen időszakra, a biztonság, a megfelelőség vagy a vállalati adatkezelési intelligencia érdekében.

  - A vészkezelés vagy megfelelőség érdekében megoldásokat építhet ki az objektumállapot biztonsági mentésére, tükrözésére vagy replikáljára a fiókjában.

  - Olyan csatlakoztatott alkalmazás-folyamatok létrehozása, amelyek reagálnak a változási eseményekre, vagy végrehajtásokat ütemeznek a létrehozott vagy módosított objektumok alapján.
  
A változáscsatorna az [](object-replication-overview.md) objektumreplikáció és a blokkblobok időponthoz időben történő visszaállításának [előfeltétele.](point-in-time-restore-overview.md)

> [!NOTE]
> A változáscsatorna tartós, rendezett naplómodellt biztosít a blobon végrehajtott módosításokról. A módosítások a változáscsatorna naplójában néhány percen belül meg vannak írva és elérhetővé teve. Ha az alkalmazásnak ennél gyorsabban kell reagálnia az eseményekre, fontolja meg a Blob Storage [eseményeket.](storage-blob-event-overview.md) [Blob Storage Események](storage-blob-event-overview.md) valós idejű, egyszeres eseményeket biztosít, amelyek lehetővé teszik, hogy Azure Functions vagy alkalmazások gyorsan reagáljanak a blobok változásaira. 

## <a name="enable-and-disable-the-change-feed"></a>A változáscsatorna engedélyezése és letiltása

A módosítások rögzítésének és rögzítésének megkezdéséhez engedélyeznie kell a változáscsatornát a tárfiókon. Tiltsa le a változáscsatornát a módosítások rögzítésének letiltásához. A módosításokat a portálon vagy a PowerShellben Azure Resource Manager engedélyezheti és tilthatja le.

Néhány dolog, amit szem előtt kell tartani a változáscsatorna engedélyezésekor.

- Minden tárfiókban csak egy változáscsatorna található a blobszolgáltatáshoz, és a blobtárolóban **$blobchangefeed** tárolóban.

- A létrehozási, frissítési és törlési módosításokat a rendszer csak a blobszolgáltatás szintjén rögzíti.

- A változáscsatorna  a fiókban előforduló összes elérhető esemény összes változását rögzíti. Az ügyfélalkalmazások szükség szerint kiszűrik az eseménytípusokat. (Lásd [az aktuális](#conditions) kiadás feltételeit).

- Csak a GPv2- és Blob Storage-fiókok engedélyezhetik a változáscsatornát. A prémium szintű BlockBlobStorage-fiókok és a hierarchikus névtér-kompatibilis fiókok jelenleg nem támogatottak. A GPv1-tárfiókok nem támogatottak, de állásidő nélkül frissíthető GPv2-re. További információ: [Frissítés GPv2-tárfiókra.](../common/storage-account-upgrade.md)

### <a name="portal"></a>[Portál](#tab/azure-portal)

Változáscsatorna engedélyezése a tárfiókon a következő Azure Portal:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a tárfiókját.
1. Nyissa meg az **Adatvédelem lehetőséget** a **adatkezelés.**
1. A **Követés alatt** válassza a Blob **változáscsatorna engedélyezése lehetőséget.**
1. Az **adatvédelmi beállítások** megerősítéséhez kattintson a Mentés gombra.

    :::image type="content" source="media/storage-blob-change-feed/change-feed-enable-portal.png" alt-text="Képernyőkép a változáscsatorna engedélyezéséről a Azure Portal":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Változáscsatorna engedélyezése a PowerShell használatával:

1. Telepítse a powershellget legújabb verzióját.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. Zárja be, majd nyissa meg újra a PowerShell-konzolt.

3. Telepítse az **Az.Storage** modul 2.5.0-s vagy újabb verzióját.

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 2.5.0 –AllowClobber –Force
   ```

4. Jelentkezzen be az Azure-előfizetésbe az paranccsal, és kövesse a képernyőn megjelenő `Connect-AzAccount` utasításokat a hitelesítéshez.

   ```powershell
   Connect-AzAccount
   ```

5. Változáscsatorna engedélyezése a tárfiókhoz.

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[Sablon](#tab/template)
Egy új Azure Resource Manager a változáscsatorna meglévő tárfiókon való engedélyezéséhez a következő Azure Portal:

1. A Azure Portal válassza az **Erőforrás létrehozása lehetőséget.**

2. A **Keresés a Marketplace-en mezőbe írja** be a sablon üzembe **helyezését,** majd nyomja le az ENTER **billentyűt.**

3. Válassza **[az Egyéni sablon üzembe helyezése lehetőséget,](https://portal.azure.com/#create/Microsoft.Template)** majd válassza a Build your own template in the editor (Saját sablon létrehozása a **szerkesztőben) lehetőséget.**

4. A sablonszerkesztőben illessze be a következő json-t. Cserélje le `<accountName>` a helyőrzőt a tárfiókja nevére.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts/blobServices",
           "apiVersion": "2019-04-01",
           "name": "<accountName>/default",
           "properties": {
               "changeFeed": {
                   "enabled": true
               }
           } 
        }]
   }
   ```
    
5. Kattintson **a Mentés gombra,** adja meg a fiók erőforráscsoportját, majd kattintson a **Vásárlás** gombra a sablon üzembe helyezéséhez és a változáscsatorna engedélyezéséhez.

---

## <a name="consume-the-change-feed"></a>A változáscsatorna felhasználta

A változáscsatorna több metaadatot és naplófájlt állít elő. Ezek a fájlok a **tárfiók $blobchangefeed** tárolóban találhatók. 

> [!NOTE]
> Az aktuális kiadásban a $blobchangefeed tároló csak a Azure Portal látható, a Azure Storage Explorer. A ListContainers API hívatáskor a $blobchangefeed-tároló jelenleg nem látható, de a ListBlobs API közvetlenül a tárolón hívható meg a blobok számára

Az ügyfélalkalmazások a Változáscsatorna feldolgozó SDK-hoz biztosított blob változáscsatorna-feldolgozó kódtár használatával is felhasználhatjak a változáscsatornát. 

Lásd: [Változáscsatorna-naplók feldolgozása a Azure Blob Storage.](storage-blob-change-feed-how-to.md)

## <a name="understand-change-feed-organization"></a>A változáscsatorna-szervezés

<a id="segment-index"></a>

### <a name="segments"></a>Szegmensek

A változáscsatorna olyan változások naplója,   amelyek óránkénti szegmensekbe vannak rendezve, de néhány percenként hozzáfűzve és frissítve vannak. Ezek a szegmensek csak akkor jön létre, ha blobváltozási események történnek abban az órában. Ez lehetővé teszi, hogy az ügyfélalkalmazás felhasználja az adott időtartományon belül végrehajtott módosításokat anélkül, hogy a teljes naplóban keresnie kell. További információ: [Specifikációk.](#specifications)

A változáscsatorna egy elérhető óránkénti szegmensét egy jegyzékfájl ismerteti, amely meghatározza az egyes szegmens változáscsatorna-fájljainak elérési útját. A virtuális könyvtár listája idő szerint rendezetten jeleníti meg `$blobchangefeed/idx/segments/` ezeket a szegmenseket. A szegmens útvonala a szegmens által képviselt óránkénti időtartomány kezdő idejét írja le. Ezzel a listával kiszűrheti az Ön számára érdekes naplószegmenseket.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> A `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json` automatikusan létrejön a változáscsatorna engedélyezésekor. Ezt a fájlt nyugodtan figyelmen kívül hagyhatja. Ez egy mindig üres inicializáló fájl. 

A szegmens jegyzékfájlja ( ) a tulajdonságban az erre a szegmensre vonatkozó változáscsatorna-fájlok `meta.json` elérési `chunkFilePaths` útját jeleníti meg. Az alábbi példában egy szegmens jegyzékfájlja található.

```json
{
    "version": 0,
    "begin": "2019-02-22T18:10:00.000Z",
    "intervalSecs": 3600,
    "status": "Finalized",
    "config": {
        "version": 0,
        "configVersionEtag": "0x8d698f0fba563db",
        "numShards": 2,
        "recordsFormat": "avro",
        "formatSchemaVersion": 1,
        "shardDistFnVersion": 1
    },
    "chunkFilePaths": [
        "$blobchangefeed/log/00/2019/02/22/1810/",
        "$blobchangefeed/log/01/2019/02/22/1810/"
    ],
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-22T18:11:01.187Z",
        "data": {
            "aid": "55e507bf-8006-0000-00d9-ca346706b70c"
        }
    }
}
```

> [!NOTE]
> A tároló csak akkor jelenik meg, ha engedélyezte a változáscsatorna `$blobchangefeed` funkciót a fiókjában. A változáscsatorna engedélyezése után néhány percet várnia kell, mielőtt listába sorolhatja a tárolóban lévő blobokat. 

<a id="log-files"></a>

### <a name="change-event-records"></a>Eseményrekordok módosítása

A változáscsatornafájlok módosítási eseményrekordok sorozatát tartalmazzák. Minden módosítási eseményrekord egy adott blob egy-egy változásának felel meg. A rekordok szerializálása és a fájlba való írása az [Apache Avro formátumspecifikációval megszabadított.](https://avro.apache.org/docs/1.8.2/spec.html) A rekordok az Avro fájlformátum specifikációval olvashatók. Számos kódtár érhető el a fájlok ilyen formátumú feldolgozásához.

A változáscsatornafájlok hozzáfűző blobokként vannak `$blobchangefeed/log/` tárolva a [virtuális könyvtárban.](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) Az egyes elérési utak alatt található első változáscsatornafájl a `00000` fájlnévben lesz (például `00000.avro` ). Az elérési úthoz hozzáadott minden további naplófájl neve 1-ével nő (például: `00001.avro` ).

A változáscsatorna-rekordok a következő eseménytípusokat rögzítik:
- BlobLétrehozva
- Blob törölve
- BlobPropertiesUpdated
- BlobSnapshotCreated

Példa az eseményrekord JSON-ként konvertált változáscsatorna-fájlról való változásra.

```json
{
     "schemaVersion": 1,
     "topic": "/subscriptions/dd40261b-437d-43d0-86cf-ef222b78fd15/resourceGroups/sadodd/providers/Microsoft.Storage/storageAccounts/mytestaccount",
     "subject": "/blobServices/default/containers/mytestcontainer/blobs/mytestblob",
     "eventType": "BlobCreated",
     "eventTime": "2019-02-22T18:12:01.079Z",
     "id": "55e5531f-8006-0000-00da-ca3467000000",
     "data": {
         "api": "PutBlob",
         "clientRequestId": "edf598f4-e501-4750-a3ba-9752bb22df39",
         "requestId": "00000000-0000-0000-0000-000000000000",
         "etag": "0x8D698F13DCB47F6",
         "contentType": "application/octet-stream",
         "contentLength": 128,
         "blobType": "BlockBlob",
         "url": "",
         "sequencer": "000000000000000100000000000000060000000000006d8a",
         "storageDiagnostics": {
             "bid": "11cda41c-13d8-49c9-b7b6-bc55c41b3e75",
             "seq": "(6,5614,28042,28038)",
             "sid": "591651bd-8eb3-c864-1001-fcd187be3efd"
         }
  }
}
```

Az egyes tulajdonságokkal kapcsolatos leírásért lásd a Azure Event Grid [eseménysémát ismertető Blob Storage.](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties) A BlobPropertiesUpdated és a BlobSnapshotCreated események jelenleg kizárólag a Változáscsatornára vannak kizáróan, és a Blob Storage eseményeknél még nem támogatottak.

> [!NOTE]
> A szegmens változáscsatorna-fájljai nem jelennek meg azonnal a szegmens létrehozása után. A késés időtartama a változáscsatorna közzétételi késésének normál időközében van, amely a módosítástól számított néhány percen belül esik.

<a id="specifications"></a>

## <a name="specifications"></a>Specifikációk

- A módosítási események rekordjai csak a változáscsatornához vannak hozzáfűzve. A rekordok hozzáfűzése után azok nem módosíthatók, és a rekord pozíciója stabil. Az ügyfélalkalmazások fenntartják saját ellenőrzőpontjukat a változáscsatorna olvasási pozícióján.

- A módosítási eseményrekordokat a rendszer a módosítás után néhány percen belül hozzáfűzi. Az ügyfélalkalmazások dönthetnek úgy, hogy a rekordokat a folyamatos elérés érdekében hozzáfűzik, vagy tömegesen, bármikor.

- A módosítási eseményrekordok blobonkénti módosítási **sorrend szerint vannak rendezettek.** A blobok közötti módosítások sorrendje nincs meghatározva a Azure Blob Storage. Az előző szegmensben történt összes módosítás a későbbi szegmensek minden változását megelőzően történt.

- A módosítási eseményrekordok az Apache [Avro 1.8.2](https://avro.apache.org/docs/1.8.2/spec.html) formátumspecifikációval vannak szerializálva a naplófájlba.

- Módosítsa azokat az eseményrekordokat, ahol a értéke belső rendszerrekord, és nem tükrözik a fiókban lévő objektumok `eventType` `Control` változását. Ezeket a rekordokat nyugodtan figyelmen kívül hagyhatja.

- A tulajdonságos csomag értékei csak belső használatra használhatók, és nem az alkalmazás `storageDiagnostics` által használhatók. Az alkalmazások nem függnek szerződéses függőséggel az adatoktól. Ezeket a tulajdonságokat nyugodtan figyelmen kívül hagyhatja.

- A szegmens által ábrázolt idő **körülbelül** 15 perces. Ezért annak érdekében, hogy az összes rekord egy adott idő alatt fel legyen használva, az egymást követő előző és következő óra szegmenst kell felfogni.

- Az egyes szegmensek száma a naplóstream belső particionálásának köszönhetően eltérő lehet a közzététel `chunkFilePaths` átviteli sebességének kezeléséhez. A naplófájlok garantáltan tartalmaznak egymást kölcsönösen kizáró blobokat, és párhuzamosan is feldolgozhatók anélkül, hogy megsértené a módosítások blobonkénti sorrendjét az `chunkFilePath` iteráció során.

- A szegmensek az állapotuktól `Publishing` indulnak. Ha a rekordok a szegmenshez való hozzáfűzése befejeződött, a következő lesz: `Finalized` . A fájl tulajdonságának dátuma után dátumozott szegmensek naplófájlját az alkalmazás nem `LastConsumable` `$blobchangefeed/meta/Segments.json` használja fel. Az alábbi példa egy `LastConsumable` fájlban található `$blobchangefeed/meta/Segments.json` tulajdonságra mutat példát:

```json
{
    "version": 0,
    "lastConsumable": "2019-02-23T01:10:00.000Z",
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-23T02:24:00.556Z",
        "data": {
            "aid": "55e551e3-8006-0000-00da-ca346706bfe4",
            "lfz": "2019-02-22T19:10:00.000Z"
        }
    }
}

```

<a id="conditions"></a>

## <a name="conditions-and-known-issues"></a>Feltételek és ismert problémák

Ez a szakasz a változáscsatorna aktuális kiadásának ismert problémáit és feltételeit ismerteti. 

- Az egyes változások eseményrekordjainak módosítása egynél több alkalommal is megjelenhet a változáscsatornában.
- Még nem tudja kezelni a változáscsatorna naplófájlok élettartamát, ha időalapú adatmegőrzési szabályzatot ad meg hozzájuk, és nem törölheti a blobokat.
- A `url` naplófájl tulajdonsága jelenleg mindig üres.
- A fájlban segments.jstulajdonság nem tartalmazza a változáscsatorna által véglegesítendő első `LastConsumable` szegmenst. Ez a probléma csak az első szegmens véglegesített kiadását követően jelentkezik. A tulajdonság pontosan rögzíti az első óra utáni összes további `LastConsumable` szegmenst.
- A ListContainers API $blobchangefeed nem látja a Azure Portal tárolót, és a tároló nem Storage Explorer.  A tartalom megtekintéséhez hívja meg a ListBlobs API-t a $blobchangefeed tárolóban.
- Előfordulhat, hogy a [](../common/storage-disaster-recovery-guidance.md) fiók feladatátvételét korábban kezdeményező tárfiókok nem jelennek meg a naplófájlban. A fiók jövőbeli feladatátvételei hatással lehetnek a naplófájlra is.

## <a name="faq"></a>GYIK

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>Mi a különbség a változáscsatorna és a Storage Analytics között?
Az elemzési naplók az összes műveletre vonatkozó, sikeres és sikertelen kérésekkel kapcsolatos olvasási, írási, listás és törlési művelet rekordjait tartalmazják. Az elemzési naplók mindent megtesznek, és nem garantálnak rendelést.

A változáscsatorna egy olyan megoldás, amely tranzakciónaplót biztosít a sikeresmutációkról vagy a fiók módosításairól, például a blobok létrehozásáról, módosításáról és törléséről. A változáscsatorna garantálja az összes esemény rögzítését és megjelenítését a blobonkénti sikeres módosítások sorrendjében, így nem kell kiszűrni a nagy mennyiségű olvasási műveletből vagy sikertelen kérésből származó zajt. A változáscsatorna alapvetően olyan alkalmazásfejlesztéshez lett kialakítva és optimalizálva, amely bizonyos garanciákat igényel.

### <a name="should-i-use-change-feed-or-storage-events"></a>Változáscsatorna- vagy Storage-eseményeket használjak?
A Változáscsatorna és a [Blob Storage-események](storage-blob-event-overview.md) funkciói ugyanazt az információt biztosítják ugyanazokkal a teljesítési megbízhatósági garanciával, a fő különbség pedig az eseményrekordok késése, sorrendje és tárolása. A változáscsatorna néhány percen belül közzéteszi a rekordokat a naplóban, és garantálja a módosítási műveletek blobonkénti sorrendjét. A tárolási eseményeket a rendszer valós időben lekérte, és nem biztos, hogy megrendeli őket. A változáscsatorna eseményei tartósan, csak olvasható, stabil naplókként vannak tárolva a tárfiókban a saját meghatározott megőrzési időtartammal, míg a tárolási eseményeket az eseménykezelő csak átmeneti módon tárolja, kivéve, ha Ön kifejezetten tárolja őket. A változáscsatornával számos alkalmazás használhatja a naplókat a saját kényelmeikben blob API-k vagy SDK-k használatával. 

## <a name="next-steps"></a>Következő lépések

- Példa a változáscsatorna .NET-ügyfélalkalmazással való beolvassa. Lásd: [Változáscsatorna-naplók feldolgozása a Azure Blob Storage.](storage-blob-change-feed-how-to.md)
- Ismerje meg, hogyan reagálhat valós időben az eseményekre. Lásd: [Reagálás Blob Storage eseményekre](storage-blob-event-overview.md)
- További információ az összes kérés sikeres és sikertelen műveleteinek részletes naplózási információiról. Lásd: [Az Azure Storage elemzési naplózása](../common/storage-analytics-logging.md)
