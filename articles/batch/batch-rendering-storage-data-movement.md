---
title: Tárolás és adatáthelyelés rendereléshez
description: Ismerje meg a különböző tárolási és adatátviteli lehetőségeket az adateszközök és a kimeneti fájlok számítási feladatainak renderelése során.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: 0a18ee6961cb601b0fa9db7213eb6115afa20096
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765196"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>Tárolási és adatátviteli lehetőségek az adateszközök és a kimeneti fájlok renderelése esetén

A készlet virtuális gépei renderelő alkalmazásai számára többféle lehetőség is van a jelenet és az adatfájl elérhetővé tenni:

* [Azure Blob Storage:](../storage/blobs/storage-blobs-introduction.md)
  * A jelenet- és objektumfájlok egy helyi fájlrendszerből vannak feltöltve a blobtárolóba. Ha az alkalmazást egy feladat futtatja, akkor a szükséges fájlokat a rendszer átmásolja a blobtárolóból a virtuális gépre, hogy a renderelő alkalmazás el tudja őket férni. A kimeneti fájlokat a renderelő alkalmazás írja a virtuálisgép-lemezre, majd átmásolja a blobtárolóba.  Szükség esetén a kimeneti fájlok letölthetők a blobtárolóból egy helyi fájlrendszerbe.
  * Az Azure Blob Storage egyszerű és költséghatékony megoldás kisebb projektekhez.  Mivel az összes eszközfájlra szükség van az egyes készletben lévő virtuális gépeken, az adatfájlszám és -méret növekedése után ügyelni kell arra, hogy a fájlátvitelek a lehető leghatékonyabbak legyenek.  
* Azure Storage fájlrendszerként a [blobfuse használatával:](../storage/blobs/storage-how-to-mount-container-linux.md)
  * Linux rendszerű virtuális gépeken a blobfuse virtuális fájlrendszer-illesztőprogram használata során a tárfiók elérhetővé és használható fájlrendszerként.
  * Ennek a lehetőségnek az az előnye, hogy nagyon költséghatékony, mivel nincs szükség virtuális gépre a fájlrendszerhez, valamint a virtuális gépek blobfuse gyorsítótárazása elkerüli ugyanazon fájlok többszöri letöltését több feladathoz és tevékenységhez.  Az adatáthelyzés is egyszerű, mivel a fájlok egyszerűen blobok, és szabványos API-k és eszközök, például az azcopy használhatók fájlok másolására a helyszíni fájlrendszer és az Azure Storage között.
* Fájlrendszer vagy fájlmegosztás:
  * A virtuális gép operációs rendszerétől és teljesítmény-/méretezési követelményeitől függően a lehetőségek közé tartozik az [Azure Files](../storage/files/storage-files-introduction.md), a csatlakoztatott lemezekkel rendelkező virtuális gépek használata NFS-hez, több virtuális gép használata csatlakoztatott lemezekkel egy elosztott fájlrendszerhez, például aSterFS-hez vagy egy külső ajánlathoz.
  * [Az Avere Systems](https://www.averesystems.com/) most már a Microsoft része, és a közeljövőben olyan megoldásokkal is fogni, amelyek ideálisak a nagy méretű, nagy teljesítményű rendereléshez.  Az Avere-megoldás lehetővé teszi egy Azure-alapú NFS- vagy SMB-gyorsítótár létrejöttét, amely együttműködik a blobtárolóval vagy a helyszíni NAS-eszközökkel.
  * Fájlrendszerrel a fájlok közvetlenül a fájlrendszerbe olvashatók vagy írhatók, vagy másolhatók a fájlrendszer és a készlet virtuális gépei között.
  * A megosztott fájlrendszer lehetővé teszi a projektek és feladatok közötti nagy számú adateszköz kihasználását, és a renderelési tevékenységek csak a szükséges elemekhez férnek hozzá.

## <a name="using-azure-blob-storage"></a>Az Azure Blob Storage használata

Blob Storage-fiókot vagy általános célú v2-tárfiókot kell használni.  Ez a két tárfióktípus az általános célú v1-tárfiókhoz képest jelentősen magasabb korlátokkal konfigurálható, amint az ebben a [blogbejegyzésben is le van állítva.](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)  Ha konfigurálva van, a magasabb korlátok sokkal jobb teljesítményt és méretezhetőséget eredményeznek, különösen akkor, ha sok készletbeli virtuális gép fér hozzá a tárfiókhoz.

### <a name="copying-files-between-client-and-blob-storage"></a>Fájlok másolása az ügyfél és a blobtároló között

Az Azure Storage-ba és onnan másba történő fájlmásoláshoz különböző mechanizmusok használhatók, beleértve a Storage Blob API-t, az [Azure Storage](https://github.com/Azure/azure-storage-net-data-movement)adatátviteli kódtárat, az azcopy parancssori eszközt [Windows](../storage/common/storage-use-azcopy-v10.md) vagy [Linux](../storage/common/storage-use-azcopy-v10.md)rendszeren, [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), és [Azure Batch Explorer.](https://azure.github.io/BatchExplorer/)

Az azcopy használatával például egy mappában lévő összes eszköz a következőképpen továbbítható:


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

Ha csak a módosított fájlokat másolja, a /XO paraméter használható:

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Bemeneti objektumfájlok másolása blobtárolóból Batch-készlet virtuális gépeibe

A fájlok másolásának több módja is van, a legjobb módszer a feladateszközök mérete alapján.
A legegyszerűbb módszer, ha minden feladathoz átmásolja az összes eszközfájlt a készlet virtuális gépeibe:

* Ha egy feladat egyedi fájlokat tartalmaz, de egy feladat összes [](/rest/api/batchservice/job/add#jobpreparationtask) tevékenységéhez szükségesek, akkor meg lehet adni egy feladat-előkészítési tevékenységet az összes fájl másolásához.  A feladat-előkészítési tevékenység egyszer fut le, amikor az első feladatfeladatot egy virtuális gépen futtatja, de a későbbi feladatfeladatok esetében nem futtatja újra.
* A [feladat befejezése után](/rest/api/batchservice/job/add#jobreleasetask) meg kell adni egy feladat-kiadási tevékenységet, amely eltávolítja a feladatonkénti fájlokat; Így elkerülhető, hogy a virtuálisgép-lemezen az összes feladateszközfájl kitöltsen.
* Ha több feladat használja ugyanazt az adatcsoportot, és az egyes feladatokhoz csak növekményes módosításokat végez az adateszközöken, akkor a rendszer továbbra is átmásolja az összes adateszközfájlt, még akkor is, ha csak egy részkészlet frissült.  Ez nem lenne hatékony, ha sok nagy méretű adateszközfájl lenne.

Ha az eszközfájlokat a feladatok között újra felhasználják, és csak növekményes módosításokat alkalmaznak a feladatok között, akkor hatékonyabb, de valamivel nagyobb mértékben érintett megközelítés az eszközök tárolása a virtuális gép megosztott mappájában, és a módosított fájlok szinkronizálása.

* A feladat-előkészítési tevékenység az azcopy és a /XO paraméter használatával végzi el a másolást a környezeti változó által AZ_BATCH_NODE_SHARED_DIR virtuális gép megosztott mappájába.  Ez csak a módosított fájlokat másolja az egyes virtuális gépekre.
* Átgondolni kell az összes eszköz méretét, hogy azok elférnek-e a készlet virtuális gépének ideiglenes meghajtón.

Azure Batch támogatja a fájlok tárfiók és Batch-készlet virtuális gépek közötti másolását.  A [tevékenységerőforrás-fájlok fájlokat](/rest/api/batchservice/job/add#resourcefile) másolnak a tárolóból a készletbe virtuális gépekbe, és meg lehet adni a feladat-előkészítési tevékenységhez.  Sajnos több száz fájl esetén lehetséges a korlát túllépni a korlátot, és a feladatok meghiúsulnak.  Ha nagy számú adateszköz van, javasoljuk, hogy használja az azcopy parancssort a feladat-előkészítési feladatban, amely helyettesítő karaktereket használhat, és nincs korlátja.

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>Kimeneti fájlok másolása blobtárolóba a Batch-készlet virtuális gépeiből

[A kimeneti fájlok](/rest/api/batchservice/task/add#outputfile) egy készlet virtuális gépről a tárolóba másolhatnak fájlokat.  A feladat befejezése után egy vagy több fájl átmásolható a virtuális gépről egy adott tárfiókba.  A renderelt kimenetet másolni kell, de érdemes lehet naplófájlokat is tárolni.

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>Blobfuse virtuális fájlrendszer használata Linux rendszerű virtuálisgép-készletekhez

A Blobfuse a Azure Blob Storage virtuális fájlrendszer-illesztőprogramja, amely lehetővé teszi a Storage-fiókban blobként tárolt fájlok linuxos fájlrendszeren keresztüli hozzáférését.

A készletcsomópontok csatlakoztatják a fájlrendszert, amikor elindítják, vagy a csatlakoztatás egy feladat-előkészítési tevékenység részeként történhet – olyan tevékenység, amely csak akkor fut, amikor a feladat első tevékenysége fut egy csomóponton.  A Blobfuse konfigurálható úgy, hogy a ramdisk és a virtuális gépek helyi SSD-ját is használja a fájlok gyorsítótárazása érdekében, ami jelentősen növeli a teljesítményt, ha egy csomóponton több tevékenység is hozzáfér ugyanazokhoz a fájlokhoz.

[A mintasablonok önálló](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount) V-Ray-renderelések blobfuse fájlrendszerrel való futtatásához érhetők el, és más alkalmazások sablonjainak alapjául is használhatók.

### <a name="accessing-files"></a>Fájlok elérése

A feladatfeladatok elérési utakat határoznak meg a bemeneti és kimeneti fájlok számára a csatlakoztatott fájlrendszer használatával.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Bemeneti objektumfájlok másolása blobtárolóból Batch-készlet virtuális gépeibe

Mivel a fájlok egyszerűen blobok az Azure Storage-ban, a standard blob API-k, eszközök és felhasználói felületek használatával fájlok másolhatóak a helyszíni fájlrendszer és a blobtároló között; például az azcopy, Storage Explorer, Batch Explorer stb.

## <a name="using-azure-files-with-windows-vms"></a>Az Azure Files használata Windows rendszerű virtuális gépekkel

[Azure Files](../storage/files/storage-files-introduction.md) teljes körűen felügyelt felhőbeli fájlmegosztásokat kínál, amelyek az SMB protokollon keresztül érhetők el.  Azure Files Azure Blob Storage-on alapul; költséghatékony, [](https://azure.microsoft.com/pricing/details/storage/files/) és konfigurálható adatreplikációval egy másik, globálisan redundáns régióba.  [A méretezési](../storage/files/storage-files-scale-targets.md#azure-files-scale-targets) célokat át kell vizsgálni annak meghatározásához, hogy Azure Files-e az előrejelzési készlet mérete és az eszközfájlok száma alapján.

Az [Azure-fájlmegosztások](../storage/files/storage-how-to-use-files-windows.md) csatlakoztatását egy dokumentáció tartalmazza.

### <a name="mounting-an-azure-files-share"></a>Egy Azure Files csatlakoztatása

A Batchben való használathoz csatlakoztatási műveletet kell végrehajtani minden alkalommal, amikor egy tevékenység fut a futtatás során, mivel a tevékenységek közötti kapcsolat nem lehetséges.  Ennek legegyszerűbb módja, ha a cmdkey használatával a hitelesítő adatokat a készlet konfigurációjában található indítási feladattal megőrzésére használja, majd az egyes feladatok előtt csatlakoztatja a megosztást.

Példa a cmdkey használatára egy készletsablonban (A JSON-fájlban való használatra escape-karakterrel van elválasztva) – vegye figyelembe, hogy amikor a cmdkey hívást a net use hívástól választja el, az indítási tevékenység felhasználói környezetének meg kell egynie a feladatok futtatásához használttal:

```
"startTask": {
  "commandLine": "cmdkey /add:storageaccountname.file.core.windows.net
    /user:AZURE\\markscuscusbatch /pass:storage_account_key",
  "userIdentity":{
    "autoUser": {
      "elevationLevel": "nonadmin",
      "scope": "pool"
    }
}
```

Példa feladatfeladat parancssorára:
```
"commandLine":"net use S:
  \\\\storageaccountname.file.core.windows.net\\rendering &
3dsmaxcmdio.exe -v:5 -rfw:0 -10 -end:10
  -bitmapPath:\"s:\\3dsMax\\Dragon\\Assets\"
  -outputName:\"s:\\3dsMax\\Dragon\\RenderOutput\\dragon.jpg\"
  -w:1280 -h:720
  \"s:\\3dsMax\\Dragon\\Assets\\Dragon_Character_Rig.max\""
```

### <a name="accessing-files"></a>Fájlok elérése

A feladatfeladatok csatlakoztatott meghajtó vagy UNC elérési út használatával határozzák meg a bemeneti és kimeneti fájlok elérési útját a csatlakoztatott fájlrendszer használatával.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Bemeneti objektumfájlok másolása blobtárolóból Batch-készlet virtuális gépeibe

Azure Files összes olyan fő API és eszköz támogatja, amelyek támogatják az Azure Storage-et; például azcopy, Azure CLI, Storage Explorer, Azure PowerShell, Batch Explorer stb.

[Azure File Sync](../storage/file-sync/file-sync-planning.md) a fájloknak a helyszíni fájlrendszer és az Azure-fájlmegosztás közötti automatikus szinkronizálása.

## <a name="next-steps"></a>Következő lépések

A tárolási lehetőségekkel kapcsolatos további információkért tekintse meg a részletes dokumentációt:

* [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)
* [Blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md)
* [Azure Files](../storage/files/storage-files-introduction.md)
