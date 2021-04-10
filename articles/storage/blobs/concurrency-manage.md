---
title: Egyidejűség kezelése a blob Storage-ban
titleSuffix: Azure Storage
description: Megtudhatja, hogyan kezelhet több írót egy blobon, ha optimista vagy pesszimista párhuzamosságot hajt végre az alkalmazásban. Az optimista Egyidejűség ellenőrzi a blob ETag értékét, és összehasonlítja a megadott ETag. A pesszimista Egyidejűség kizárólagos bérletet használ a blob más írókhoz való zárolására.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: ea0bed0884a3a03e2cd15b274b2afb0f054b0cbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96523517"
---
# <a name="managing-concurrency-in-blob-storage"></a>Egyidejűség kezelése a blob Storage-ban

A modern alkalmazások gyakran több felhasználóval is megtekintik és frissítik az adatfeldolgozást. Az alkalmazás fejlesztőknek körültekintően kell megfontolniuk, hogyan biztosíthat kiszámítható felhasználói élményt a végfelhasználók számára, különösen olyan helyzetekben, ahol több felhasználó is frissítheti ugyanazt az információt. Három fő adatpárhuzamossági stratégia létezik, amelyeket a fejlesztők általában figyelembe vesznek:  

- **Optimista Egyidejűség**: egy frissítést végrehajtó alkalmazás a frissítés részeként megállapítja, hogy az adatok megváltoztak-e, mivel az alkalmazás utoljára olvasta az adott adatforrást. Ha például két felhasználó egy wiki-oldalt tekint meg, akkor a wiki platformnak biztosítania kell, hogy a második frissítés ne írja felül az első frissítést. Azt is biztosítania kell, hogy a felhasználók tisztában legyenek azzal, hogy a frissítés sikeres volt-e. Ezt a stratégiát leggyakrabban a webes alkalmazásokban használják.

- **Pesszimista Egyidejűség**: egy frissítés elvégzését végző alkalmazás zárolja az objektumot, amely megakadályozza, hogy más felhasználók a zárolás felszabadítása előtt frissítse az adatok frissítését. Például egy elsődleges/másodlagos adatreplikálási forgatókönyvben, amelyben csak az elsődleges frissítések vannak végrehajtva, az elsődleges általában egy hosszabb ideig kizárólagos zárolást tart fenn az adatokon, így biztosítva, hogy senki más nem tudja frissíteni.

- **Utolsó író WINS**: egy olyan megközelítés, amely lehetővé teszi a frissítési műveletek folytatását anélkül, hogy először meg kellene határozni, hogy egy másik alkalmazás frissítette-e az adatok olvasását. Ezt a módszert általában akkor használja a rendszer, ha az adatparticionálás oly módon történik, hogy több felhasználó sem fér hozzá egyszerre ugyanazon az adategységhez. Hasznos lehet a rövid élettartamú adatfolyamok feldolgozásakor is.

Az Azure Storage mind a három stratégiát támogatja, bár ez a sajátossága annak, hogy teljes körű támogatást nyújtson az optimista és a pesszimista párhuzamosságokhoz. Az Azure Storage úgy lett kialakítva, hogy egy erős konzisztencia-modellt biztosítson, amely garantálja, hogy miután a szolgáltatás beszúrt vagy frissítési műveletet végez, a későbbi olvasási műveletek a legújabb frissítést adják vissza.

A megfelelő egyidejűségi stratégia kiválasztásán kívül a fejlesztőknek is tisztában kell lenniük azzal, hogy a tárolási platform hogyan különíti el a változásokat, különösen a tranzakciókban megjelenő objektumok változásait. Az Azure Storage pillanatkép-elkülönítéssel lehetővé teszi az olvasási műveletek egyazon partíción belüli írási műveletekkel való egyidejű használatát. A pillanatkép-elkülönítés garantálja, hogy minden olvasási művelet konzisztens pillanatképet ad vissza az adatokról még a frissítések során.

Dönthet úgy is, hogy optimista vagy pesszimista párhuzamossági modelleket használ a blobok és tárolók elérésének kezeléséhez. Ha nem ad meg explicit módon stratégiát, akkor alapértelmezés szerint az utolsó író WINS.  

## <a name="optimistic-concurrency"></a>Optimista Egyidejűség

Az Azure Storage minden tárolt objektumhoz hozzárendel egy azonosítót. Ez az azonosító minden alkalommal frissül, amikor írási műveletet végeznek egy objektumon. Az azonosítót a http GET válasz részeként visszaadja az ügyfélnek a HTTP-protokoll által definiált ETag fejlécben.

Egy frissítést végző ügyfél az eredeti ETag egy feltételes fejléccel együtt is elküldheti, így biztosítva, hogy egy adott feltétel teljesülése esetén a frissítés csak akkor történjen meg. Ha például az **IF-Match** fejléc van megadva, az Azure Storage ellenőrzi, hogy a frissítési kérelemben megadott ETAG megegyezik-e a frissítendő objektum ETAG. További információ a feltételes fejlécekről: [feltételes fejlécek megadása blob Service műveletekhez](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).

A folyamat körvonala a következő:  

1. BLOB beolvasása az Azure Storage-ból. A válasz tartalmaz egy HTTP ETag fejléc-értéket, amely az objektum aktuális verzióját azonosítja.
1. A blob frissítésekor adja meg az írási kérelem **IF-Match** feltételes fejlécében az 1. lépésben kapott ETAG értéket. Az Azure Storage összehasonlítja a kérelemben szereplő ETag értéket a blob aktuális ETag értékével.
1. Ha a blob aktuális ETag értéke eltér a kérelemben szereplő **IF-Match** feltételes fejlécben megadott értéktől, akkor az Azure Storage a 412-as HTTP-állapotkódot adja vissza (az előfeltétel meghiúsult). Ez a hiba azt jelzi, hogy az ügyfél egy másik folyamat frissítette a blobot, mivel az ügyfél először beolvasta azt.
1. Ha a blob aktuális ETag értéke megegyezik a kérelemben szereplő feltételes fejlécben szereplő ETag, **Az Azure** Storage végrehajtja a kért műveletet, és frissíti a blob aktuális ETAG értékét.  

Az alábbi kódrészletek bemutatják, hogyan hozhat létre egy **IF-Match** feltételt az írási kérelemben, amely ellenőrzi a blob ETAG értékét. Az Azure Storage kiértékeli, hogy a blob aktuális ETag megegyezik-e a kérésben megadott ETag, és csak akkor hajtja végre az írási műveletet, ha a két ETag-érték egyezik. Ha egy másik folyamat a blobot átmeneti állapotban frissítette, akkor az Azure Storage egy HTTP 412 (előfeltétel-sikertelen) állapotjelző üzenetet ad vissza.  

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstrateOptimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.NETTÓ v11](#tab/dotnetv11)

```csharp
public void DemonstrateOptimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate optimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    // Create test blob. The default strategy is last writer wins, so
    // write operation will overwrite existing blob if present.
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");

    // Retrieve the ETag from the newly created blob.
    string originalETag = blockBlob.Properties.ETag;
    Console.WriteLine("Blob added. Original ETag = {0}", originalETag);

    /// This code simulates an update by another client.
    string helloText = "Blob updated by another client.";
    // No ETag was provided, so original blob is overwritten and ETag updated.
    blockBlob.UploadText(helloText);
    Console.WriteLine("Blob updated. Updated ETag = {0}", blockBlob.Properties.ETag);

    // Now try to update the blob using the original ETag value.
    try
    {
        Console.WriteLine(@"Attempt to update blob using original ETag
                            to generate if-match access condition");
        blockBlob.UploadText(helloText, accessCondition: AccessCondition.GenerateIfMatchCondition(originalETag));
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure as expected.
                                Blob's ETag does not match.");
        }
        else
        {
            throw;
        }
    }
    Console.WriteLine();
}
```

---

Az Azure Storage más feltételes fejléceket is támogat, például **Ha-módosítva-óta**, **Ha-nem módosított-óta** és **If-None-Match**. További információ: [feltételes fejlécek megadása a blob Service-műveletekhez](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

## <a name="pessimistic-concurrency-for-blobs"></a>A Blobok pesszimista párhuzamossága

Egy blob kizárólagos használatra történő zárolásához megszerezheti a bérletet. A bérlet beolvasásakor meg kell adnia a bérlet időtartamát. A véges bérlet a 15 és 60 másodperc közötti időszakra érvényes lehet. Egy bérlet is lehet végtelen, amely egy exkluzív zárolási összegnek számít. Megújíthat egy véges bérletet, és kiterjesztheti a bérletet, ha elkészült. Az Azure Storage automatikusan felszabadítja a véges bérleteket, amikor lejárnak.  

A bérletek lehetővé teszik a különböző szinkronizálási stratégiák támogatását, beleértve az exkluzív írási/megosztott olvasási műveleteket, az exkluzív írási/kizárólagos olvasási műveleteket, valamint a közös írási/kizárólagos olvasási műveleteket. Ha egy bérlet létezik, az Azure Storage kizárólagos hozzáférést kényszerít a bérlet birtokosának írási műveleteihez. Az olvasási műveletek kizárólagosságának biztosításához azonban a fejlesztőnek meg kell győződnie arról, hogy az összes ügyfélalkalmazás címbérlet-azonosítót használ, és hogy egyszerre csak egy ügyfél rendelkezik érvényes bérlet-AZONOSÍTÓval. Azok az olvasási műveletek, amelyek nem tartalmaznak címbérlet-azonosítót, megosztott olvasást eredményeznek.  

Az alábbi példák azt mutatják be, hogyan lehet kizárólagos bérletet beszerezni egy blobon, frissítse a blob tartalmát a bérlet AZONOSÍTÓjának megadásával, majd szabadítsa fel a bérletet. Ha a bérlet aktív, és a címbérlet-azonosító nincs megadva írási kérelemben, az írási művelet meghiúsul a 412 hibakód miatt (az előfeltétel sikertelen volt).  

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstratePessimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.NETTÓ v11](#tab/dotnetv11)

```csharp
public void DemonstratePessimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate pessimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");
    Console.WriteLine("Blob added.");

    // Acquire lease for 15 seconds.
    string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
    Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

    // Update blob using lease. This operation should succeed.
    const string helloText = "Blob updated";
    var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
    blockBlob.UploadText(helloText, accessCondition: accessCondition);
    Console.WriteLine("Blob updated using an exclusive lease");

    // Simulate another client attempting to update to blob without providing lease.
    try
    {
        // Operation will fail as no valid lease was provided.
        Console.WriteLine("Now try to update blob without valid lease.");
        blockBlob.UploadText("Update operation will fail without lease.");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure error as expected.
                                Blob lease not provided.");
        }
        else
        {
            throw;
        }
    }

    // Release lease proactively.
    blockBlob.ReleaseLease(accessCondition);
    Console.WriteLine();
}
```

---

## <a name="pessimistic-concurrency-for-containers"></a>A tárolók pesszimista egyidejűsége

A tárolókban lévő bérletek lehetővé teszik a Blobok által támogatott szinkronizálási stratégiákat, beleértve az exkluzív írási/közös olvasási, kizárólagos írási/kizárólagos olvasási és közös írási/kizárólagos olvasást. A tárolók esetében azonban az exkluzív zárolás csak törlési műveletekre van kényszerítve. Egy aktív bérlettel rendelkező tároló törléséhez az ügyfélnek tartalmaznia kell az aktív címbérlet AZONOSÍTÓját a törlési kérelemmel. Az összes többi tároló művelet a bérelt tárolón a bérlet azonosítója nélkül fog sikerülni.  

## <a name="next-steps"></a>Következő lépések

* [Feltételes fejlécek megadása Blob service műveletekhez](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)
* [Bérlet tárolója](/rest/api/storageservices/lease-container)
* [Blobbérlet](/rest/api/storageservices/lease-blob)
