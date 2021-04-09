---
title: Az Azure Page Blobok áttekintése | Microsoft Docs
description: Az Azure-beli blobok és azok előnyeinek áttekintése, beleértve a minta parancsfájlokkal történő használati eseteket.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/15/2020
ms.author: tamram
ms.reviewer: wielriac
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 88c0d88a1d3119ef2fa00eb49da447749fde3221
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105543839"
---
# <a name="overview-of-azure-page-blobs"></a>Az Azure-oldal Blobok áttekintése

Az Azure Storage háromféle blob Storage-típust kínál: Blobok blokkolását, blobok és Blobok hozzáfűzését. A blokkos Blobok blokkokból állnak, és ideálisak a szöveges vagy bináris fájlok tárolására, valamint a nagyméretű fájlok hatékony feltöltésére. A hozzáfűzési Blobok is blokkokból állnak, de a hozzáfűzési műveletekhez vannak optimalizálva, így ideálisak a naplózási forgatókönyvekhez. Az oldal 512 Blobok összesen legfeljebb 8 TB méretű lapokat alkotnak, és a gyakori véletlenszerű olvasási/írási műveletekhez lettek kialakítva. Az oldal Blobok az Azure IaaS-lemezek alapjai. Ebből a cikkből megtudhatja, hogyan fejti ki a Blobok funkcióit és előnyeit.

Az oldal Blobok a 512 bájtos lapok gyűjteményei, amelyek lehetővé teszik a bájtok tetszőleges tartományának olvasását/írását. Ezért az oldal Blobok ideálisak az olyan index-alapú és ritka adatstruktúrák tárolására, mint az operációs rendszer és az adatlemezek Virtual Machines és adatbázisokhoz. Az Azure SQL DB például a blobokat használja az adatbázisok alapjául szolgáló állandó tárterületként. Emellett a Range-Based-frissítésekkel rendelkező fájlok esetében is gyakran használják az oldal blobokat.  

Az Azure Page Blobok legfontosabb funkciói a REST-felület, a mögöttes tároló tartóssága, valamint a zökkenőmentes áttelepítési képességek az Azure-ban. Ezeket a funkciókat részletesebben a következő szakaszban tárgyaljuk. Emellett az Azure-lapok Blobok jelenleg két típusú tárolóban támogatottak: Premium Storage és standard Storage. A Premium Storage kifejezetten olyan számítási feladatokhoz lett tervezve, amelyek konzisztens nagy teljesítményű és kis késleltetésű, prémium szintű oldal-blobokat biztosítanak a nagy teljesítményű tárolási helyzetekben. A standard szintű Storage-fiókok költséghatékonyan használhatók a késést nem okozó számítási feladatok futtatásához.

## <a name="restrictions"></a>Korlátozások

Az oldal Blobok csak a **lassú elérési** szintet használhatják, nem használhatják a ritka **vagy az** **archiválási** szintet. A hozzáférési szintekkel kapcsolatos további információkért lásd: [hozzáférési szintek az Azure Blob Storage – gyors, ritka és archív](storage-blob-storage-tiers.md).

## <a name="sample-use-cases"></a>Használati példák

Az Azure IaaS-lemezektől kezdődően néhány felhasználási esetet ismertetünk az oldal-Blobok esetében. Az Azure-oldal Blobok az Azure IaaS virtuális lemezek platformjának gerincét jelentik. Az Azure operációs rendszer és az adatlemezek is olyan virtuális lemezekként valósulnak meg, amelyekben az tartósan az Azure Storage platformon tárolják, majd a maximális teljesítmény érdekében a virtuális gépeknek továbbítják azokat. Az Azure-lemezek a Hyper-V [VHD formátumában](/previous-versions/windows/it-pro/windows-7/dd979539(v=ws.10)) tárolódnak, és az Azure Storage-ban [oldal blobként](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) vannak tárolva. A virtuális lemezek Azure IaaS-alapú virtuális gépekhez való használata mellett az oldal Blobok is engedélyezhetik a Pásti és a DBaaS forgatókönyveket, például az Azure SQL DB szolgáltatást, amely jelenleg az SQL-adatok tárolására szolgáló blobokat használ, és lehetővé teszi a gyors, véletlenszerű írási és olvasási műveleteket az adatbázis számára. Egy másik példa lenne, ha a közös hozzáférésű videoszerkesztő alkalmazásokhoz való megosztott média eléréséhez tartozó Péter-szolgáltatással rendelkezik, az oldal Blobok lehetővé teszik a gyors hozzáférést az adathordozón található véletlenszerű helyekhez. Emellett lehetővé teszi, hogy több felhasználó is gyorsan és hatékonyan szerkessze és egyesítse ugyanazt az adathordozót. 

Az első féltől származó Microsoft-szolgáltatások, mint például a Azure Site Recovery, a Azure Backup, valamint számos harmadik féltől származó fejlesztő implementálta piacvezető újdonságait az oldal blob REST-felületének használatával. Az alábbiakban néhány, az Azure-ban megvalósított egyedi forgatókönyvet ismertetünk: 

* Az alkalmazás által irányított növekményes Pillanatképek kezelése: az alkalmazások a blob-pillanatképeket és a REST API-kat használhatják az alkalmazás-ellenőrzőpontok az adatok költséges duplikálása nélkül történő mentéséhez. Az Azure Storage támogatja az oldal Blobok helyi pillanatképeit, amelyek nem igénylik a teljes blob másolását. Ezek a nyilvános pillanatkép-API-k lehetővé teszik a pillanatképek közötti különbözetek elérését és másolását is.
* Alkalmazások és adatok élő áttelepítése a helyszínről a felhőbe: másolja a helyszíni adatok és a REST API-k használatával közvetlenül egy Azure-oldal blobba írhat, miközben a helyszíni virtuális gép továbbra is fut. Ha a cél már megtörtént, gyorsan feladatátvételt hajthat végre az Azure-beli virtuális gépen az adott adattal. Ily módon áttelepítheti a virtuális gépeket és a virtuális lemezeket a helyszínről a felhőbe minimális állásidővel, mivel az adatok áttelepítése a háttérben történik, miközben a virtuális gép továbbra is használatban van, és a feladatátvételhez szükséges állásidő rövid lesz (percben).
* [SAS-alapú](../common/storage-sas-overview.md) közös hozzáférés, amely olyan forgatókönyveket tesz lehetővé, mint például a több olvasó és egy író, amely támogatja a Egyidejűség-vezérlést.

## <a name="pricing"></a>Díjszabás

Az oldal-blobokhoz kínált mindkét típusú tárterület saját díjszabási modellel rendelkezik. A Premium Page Blobok a Managed Disks díjszabási modelljét követik, míg a standard oldal blobokat a felhasznált méret és az egyes tranzakciók alapján számítjuk fel. További információkért tekintse meg az [Azure Page Blobs díjszabási oldalát](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

## <a name="page-blob-features"></a>Lapblobok funkciói

### <a name="rest-api"></a>REST API

Tekintse meg a következő dokumentumot, amelyből megismerheti az [oldal Blobok használatával történő fejlesztést](./storage-quickstart-blobs-dotnet.md). Példaként tekintse meg, hogyan érheti el az oldal blobokat a Storage ügyféloldali kódtára a .NET-hez. 

A következő ábra a fiók, a tárolók és a Blobok közötti általános kapcsolatokat ismerteti.

![A fiók, a tárolók és a Blobok közötti kapcsolatokat ábrázoló képernyőfelvétel](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-specified-size"></a>Egy megadott méretű üres oldal blobjának létrehozása

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Először szerezzen be egy hivatkozást egy tárolóra. Az oldal blobjának létrehozásához hívja meg a GetPageBlobClient metódust, és hívja meg a [PageBlobClient. Create](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.create) metódust. Adja meg a blob létrehozásához szükséges maximális méretet. Ennek a méretnek 512 bájtos többszörösének kell lennie.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_CreatePageBlob":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Az oldal blobjának létrehozásához először hozzon létre egy **CloudBlobClient** objektumot a Storage-fiók blob Storage-hoz való hozzáféréséhez szükséges alap URI-val (*pbaccount* az 1. ábrán) a **StorageCredentialsAccountAndKey** objektummal együtt, az alábbi példában látható módon. A példa ezután egy **CloudBlobContainer** objektumra mutató hivatkozást hoz létre, majd létrehozza a tárolót (*testvhds*), ha még nem létezik. Ezután a **CloudBlobContainer** objektum használatával hozzon létre egy **CloudPageBlob** objektumra mutató hivatkozást úgy, hogy megadhatja az oldal blobjának nevét (OS4. vhd) az eléréséhez. Az oldal blobjának létrehozásához hívja meg a [CloudPageBlob. Create](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.create), és adja meg a blob létrehozásához szükséges maximális méretet. A *blobSize* 512 bájtos többszörösének kell lennie.

```csharp
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

long OneGigabyteAsBytes = 1024 * 1024 * 1024;
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("testvhds");

// Create the container if it doesn't already exist.
container.CreateIfNotExists();

CloudPageBlob pageBlob = container.GetPageBlobReference("os4.vhd");
pageBlob.Create(16 * OneGigabyteAsBytes);
```

---

#### <a name="resizing-a-page-blob"></a>Oldal blobjának átméretezése

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Ha a létrehozás után át szeretné méretezni az oldal blobját, használja az [átméretezési](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.resize) módszert. A kért méretnek 512 bájtos többszörösnek kell lennie.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ResizePageBlob":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Ha a létrehozás után át szeretné méretezni az oldal blobját, használja az [átméretezési](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.resize) módszert. A kért méretnek 512 bájtos többszörösnek kell lennie.

```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes);
```

---

#### <a name="writing-pages-to-a-page-blob"></a>Lapok megírása egy oldal blobba

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Lapok írásához használja a [PageBlobClient. UploadPages](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.uploadpages) metódust.  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_WriteToPageBlob":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Lapok írásához használja a [CloudPageBlob. WritePages](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.beginwritepages) metódust.  

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

---

Ez lehetővé teszi a lapok szekvenciális készletének megírását a 4MBs. Az éppen megírt eltolásnak egy 512 bájtos határon kell kezdődnie (startingOffset %512 = = 0), és véget kell mutatnia a 512 határán – 1. 

Amint a blob szolgáltatásban a lapok szekvenciális készletére vonatkozó írási kérelem sikeres lesz, és a rendszer replikálja a tartósságot és a rugalmasságot, az írás véglegesítve lett, és a siker visszakerül az ügyfélnek.  

Az alábbi ábrán két különálló írási művelet látható:

![Egy diagram, amely a két különálló írási lehetőséget mutatja.](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  Egy írási művelet, amely 1024 bájt hosszúságú 0 bájtnál kezdődik 
2.  Az 1024 hosszúságú 4096-es eltolásnál kezdődő írási művelet 

#### <a name="reading-pages-from-a-page-blob"></a>Lapok beolvasása egy oldal blobból

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Lapok olvasásához használja a [PageBlobClient. download](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadto) metódust, hogy az oldal blobján belül beolvassa a bájtok egy tartományát. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ReadFromPageBlob":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Lapok olvasásához használja a [CloudPageBlob. DownloadRangeToByteArray](/dotnet/api/microsoft.azure.storage.blob.icloudblob.downloadrangetobytearray) metódust, hogy az oldal blobjában lévő bájtok egy tartományát olvassa. 

```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```

---

Ez lehetővé teszi, hogy letöltse a blob összes eltolásával kezdődő teljes blobot vagy bájtos tartományt. Olvasáskor az eltolásnak nem kell elindulnia a 512 többszörösén. A NUL-lapok bájtjainak olvasásakor a szolgáltatás nulla bájtot ad vissza.

Az alábbi ábrán egy olvasási művelet látható, amelynek eltolása 256, a tartomány mérete pedig 4352. A visszaadott adatértékek narancssárga színnel vannak kiemelve. A rendszer nulla értékeket ad vissza a NUL-lapokhoz.

![Egy 256-es eltolású olvasási művelet és a 4352-es tartomány méretének diagramja](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

Ha ritkán feltöltött blobtal rendelkezik, érdemes letöltenie az érvényes oldal régióit, hogy elkerülje a nulla bájtos egressing és a letöltési késés csökkentését.  

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Az [PageBlobClient. GetPageRanges](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.getpageranges)használatával megállapíthatja, hogy mely oldalakról készül biztonsági másolat. Ezután enumerálhatja a visszaadott tartományokat, és letöltheti az egyes tartományokban lévő összes értéket. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ReadValidPageRegionsFromPageBlob":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Az [CloudPageBlob. GetPageRanges](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.getpageranges)használatával megállapíthatja, hogy mely oldalakról készül biztonsági másolat. Ezután enumerálhatja a visszaadott tartományokat, és letöltheti az egyes tartományokban lévő összes értéket. 

```csharp
IEnumerable<PageRange> pageRanges = pageBlob.GetPageRanges();

foreach (PageRange range in pageRanges)
{
    // Calculate the range size
    int rangeSize = (int)(range.EndOffset + 1 - range.StartOffset);

    byte[] buffer = new byte[rangeSize];

    // Read from the correct starting offset in the page blob and
    // place the data in the bufferOffset of the buffer byte array
    pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, range.StartOffset, rangeSize); 

    // Then use the buffer for the page range just read
}
```

---

#### <a name="leasing-a-page-blob"></a>Oldal blobjának bérbeadása

A címbérleti blob művelet az írási és törlési műveletekhez egy blob zárolását hozza létre és kezeli. Ez a művelet olyan esetekben hasznos, amikor egy oldal blobja több ügyfélről érhető el, így biztosítva, hogy egyszerre csak egy ügyfél tudja írni a blobot. Az Azure-lemezek például ezt a lízing mechanizmust használják annak biztosítására, hogy a lemezt csak egyetlen virtuális gép kezelje. A zárolás időtartama lehet 15 – 60 másodperc, vagy lehet végtelen. További részletekért [tekintse meg a](/rest/api/storageservices/lease-blob) dokumentációt.

A sokoldalú REST API-k mellett az oldal Blobok közös hozzáférést, tartósságot és fokozott biztonságot is biztosítanak. Ezeket az előnyöket a következő bekezdésekben részletesebben fogjuk kimutatni. 

### <a name="concurrent-access"></a>Párhuzamos hozzáférés

Az oldal Blobok REST API és annak lízing mechanizmusa lehetővé teszi az alkalmazások számára, hogy több ügyfélről is hozzáférjenek az oldal blobhoz. Tegyük fel például, hogy létre kell hoznia egy elosztott felhőalapú szolgáltatást, amely több felhasználóval osztja meg a tárolási objektumokat. Ez lehet egy webalkalmazás, amely a képek nagy gyűjteményét szolgálja több felhasználó számára. Az egyik lehetőség ennek megvalósítására egy csatlakoztatott lemezekkel rendelkező virtuális gép használata. Ennek hátrányai a következők: (i) a korlátozás, amelyet egy lemez csak egyetlen virtuális géphez csatolhat, így korlátozva a méretezhetőséget, a rugalmasságot és növeli a kockázatokat. Ha a virtuális gépen vagy a virtuális gépen futó szolgáltatásnál probléma merül fel, akkor a bérlet miatt a rendszerkép nem érhető el, amíg a bérlet lejár vagy megszakadt; és (II) a IaaS virtuális gép további költségeit. 

Alternatív megoldásként közvetlenül az Azure Storage REST API-kon keresztül használhatja az oldal blobokat. Ez a lehetőség szükségtelenné teszi a költséges IaaS virtuális gépeket, így teljes rugalmasságot biztosít a több ügyféltől érkező közvetlen hozzáféréshez, leegyszerűsíti a klasszikus üzemi modellt, így nincs szükség a lemezek csatlakoztatására/leválasztására, és kiküszöböli a virtuális gép problémáinak kockázatát. Továbbá ugyanolyan teljesítményt biztosít a véletlenszerű olvasási/írási műveletekhez lemezként

### <a name="durability-and-high-availability"></a>Tartósság és magas rendelkezésre állás

A standard és a Premium Storage is tartós tárolást biztosít, ahol az oldal blob-információi mindig replikálódnak a tartósság és a magas rendelkezésre állás biztosítása érdekében. Az Azure Storage redundanciával kapcsolatos további információkért tekintse meg ezt a [dokumentációt](../common/storage-redundancy.md). Az Azure nagyvállalati szintű tartósságot biztosít a IaaS-lemezek és az oldal-Blobok számára, és az iparágban vezető, nulla százalékú, [éves meghibásodási arányt](https://en.wikipedia.org/wiki/Annualized_failure_rate)eredményezett.

### <a name="seamless-migration-to-azure"></a>Zökkenőmentes áttelepítés az Azure-ba

Azon ügyfelek és fejlesztők számára, akik a saját testreszabott biztonsági mentési megoldás megvalósítását érdeklik, az Azure olyan növekményes pillanatképeket is kínál, amelyek csak a különbözeteket őrzik meg. Ezzel a funkcióval elkerülhető a kezdeti teljes másolat díja, ami jelentősen csökkenti a biztonsági mentési költségeket. A differenciált adatok hatékony olvasásának és másolásának lehetősége mellett ez egy olyan hatékony képesség, amely még nagyobb innovációt tesz lehetővé a fejlesztők számára, ami az Azure-ban a legjobb biztonsági mentési és vész-helyreállítási (DR) felhasználói élményt nyújtja. Saját biztonsági mentési vagy DR-megoldást is beállíthat az Azure-beli virtuális gépekre a [blob Snapshot](/rest/api/storageservices/snapshot-blob) használatával, valamint a [Page Ranges](/rest/api/storageservices/get-page-ranges) API és a [növekményes másolási blob](/rest/api/storageservices/incremental-copy-blob) API-val, amelyekkel egyszerűen másolhatók a Dr. növekményes adatok. 

Emellett számos vállalat rendelkezik olyan kritikus számítási feladatokkal, amelyek már futnak a helyszíni adatközpontokban. A számítási feladatok felhőbe való áttelepítéséhez az egyik fő szempont az adatok másolásához szükséges állásidő mennyisége, valamint az átállás utáni váratlan problémák kockázata. Sok esetben a leállás showstopper lehet a felhőbe való Migrálás során. Az oldal Blobok REST API használatával az Azure ezt a problémát úgy oldja meg, hogy engedélyezi a felhőbe való áttelepítést a kritikus fontosságú számítási feladatok minimális megszakadásával. 

A pillanatképek készítésének módjáról, valamint az oldal blobjának pillanatképből történő visszaállításáról a [biztonsági mentési folyamat beállítása növekményes Pillanatképek használatával](../../virtual-machines/windows/incremental-snapshots.md) című cikkben talál példákat.