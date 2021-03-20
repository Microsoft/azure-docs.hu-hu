---
title: Tárolás és adatáthelyezés a rendereléshez
description: Tudnivalók a különböző tárolási és adatáthelyezési lehetőségekről az eszközök és a kimeneti fájlok számítási feladatainak megjelenítéséhez.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: 55ec04df2a107dabfc72298bc8849c13f3a926e0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86147296"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>Tárolási és adatáthelyezési lehetőségek az eszköz és a kimeneti fájlok megjelenítéséhez

Több lehetőség is rendelkezésre áll, hogy a jelenet és az objektum fájljai elérhetők legyenek a készletben lévő virtuális gépeken a renderelési alkalmazások számára:

* [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md):
  * A jelenet és az adategység fájljai a blob Storage-ba kerülnek a helyi fájlrendszerből. Ha az alkalmazást egy feladat futtatja, akkor a szükséges fájlokat a rendszer a blob Storage-ból másolja a virtuális gépre, hogy a renderelési alkalmazás hozzáférhessen. A kimeneti fájlokat a renderelési alkalmazás a virtuálisgép-lemezre írja, majd átmásolja a blob Storage-ba.  Ha szükséges, a kimeneti fájlok letölthetők a blob Storage-ból egy helyi fájlrendszerbe.
  * Az Azure Blob Storage egy egyszerű és költséghatékony megoldás kisebb projektekhez.  Mivel minden adategység-fájlra szükség van az egyes készleteken lévő virtuális gépeken, az adatfájlok számának és méretének növelése után ügyelni kell arra, hogy a fájlok átvitele a lehető leghatékonyabb legyen.  
* Azure Storage fájlrendszer a [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md)használatával:
  * Linux rendszerű virtuális gépek esetén a Storage-fiókok elérhetők, és fájlrendszerként használhatók a blobfuse virtuális fájlrendszer illesztőprogramjának használatakor.
  * Ennek a lehetőségnek az az előnye, hogy rendkívül költséghatékony, mivel a fájlrendszerhez nem szükséges virtuális gépek, a virtuális gépek blobfuse gyorsítótárazása pedig elkerüli a több feladathoz és feladatokhoz tartozó fájlok ismételt letöltését.  Az adatáthelyezés is egyszerű, mivel a fájlok egyszerűen blobok és standard API-k és eszközök, mint például a azcopy, használhatók fájlok másolására egy helyszíni fájlrendszer és az Azure Storage között.
* Fájlrendszer vagy fájlmegosztás:
  * A virtuálisgép-operációs rendszertől és a teljesítmény/méretezési követelményektől függően a lehetőségek közé tartoznak a [Azure Files](../storage/files/storage-files-introduction.md), a csatlakoztatott lemezekkel rendelkező virtuális gépek használata az NFS-hez, több virtuális gép használata csatlakoztatott lemezekkel, például GlusterFS vagy harmadik féltől származó ajánlat használata.
  * A [avere Systems](https://www.averesystems.com/) már a Microsoft része, és a közeljövőben olyan megoldásokat kínál, amelyek ideálisak a nagyméretű, nagy teljesítményű rendereléshez.  A avere megoldás lehetővé teszi egy olyan Azure-alapú NFS-vagy SMB-gyorsítótár létrehozását, amely blob Storage vagy helyszíni NAS-eszközökkel együtt működik.
  * A fájlrendszerrel a fájlok közvetlenül a fájlrendszerbe írhatók vagy írhatók, illetve másolhatók a fájlrendszer és a készletbeli virtuális gépek között.
  * A megosztott fájlrendszer lehetővé teszi, hogy nagy számú, a projektek és a feladatok között megosztott objektum legyen használatban, és a renderelési feladatok csak a szükséges értéket használják.

## <a name="using-azure-blob-storage"></a>Az Azure Blob Storage használata

Egy blob Storage-fiókot vagy egy általános célú v2 Storage-fiókot kell használni.  Ez a két tárolási fióktípus az általános célú v1-es Storage-fiókhoz képest jóval magasabb határértékekkel konfigurálható a [blogbejegyzésben](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)részletezett módon.  Ha be van állítva, a magasabb szintű korlátok sokkal jobb teljesítményt és méretezhetőséget tesznek lehetővé, különösen akkor, ha a Storage-fiókhoz sok készletet használó virtuális gép fér hozzá.

### <a name="copying-files-between-client-and-blob-storage"></a>Fájlok másolása az ügyfél és a blob Storage között

Ha fájlokat szeretne másolni az Azure Storage-ba és az-ból, különféle mechanizmusok használhatók, beleértve a Storage blob API-t, az [Azure Storage adatátviteli könyvtárát](https://github.com/Azure/azure-storage-net-data-movement), a Windows vagy [Linux](../storage/common/storage-use-azcopy-v10.md) [rendszerhez](../storage/common/storage-use-azcopy-v10.md) készült azcopy parancssori eszközt, [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)és [Azure batch Explorer](https://azure.github.io/BatchExplorer/).

Például a azcopy használatával a mappában lévő összes eszköz átvihető a következőképpen:


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

A csak a módosított fájlok másolásához a/XO paramétert lehet használni:

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Bemeneti adategység fájljainak másolása blob Storage-ból batch-készletbe tartozó virtuális gépekre

A fájlok másolását a feladatok eszközeinek mérete által meghatározott legjobb megközelítéssel különböző módszerekkel végezheti el.
A legegyszerűbb módszer az összes objektum összes fájljának másolása a készlet virtuális gépekre az egyes feladatokhoz:

* Ha vannak olyan fájlok, amelyek egy adott feladat számára egyediek, de a feladatok összes feladatához szükségesek, a feladat- [előkészítési feladat](/rest/api/batchservice/job/add#jobpreparationtask) megadható az összes fájl másolásához.  A feladat-előkészítési feladat akkor fut le, amikor az első feladatot egy virtuális gépen hajtja végre, de a későbbi feladatokhoz nem futtatja újra.
* A feladat befejezését követően meg kell adni egy [feladat kiadási feladatát](/rest/api/batchservice/job/add#jobreleasetask) a feladat-visszavonási fájlok eltávolításához. Ezzel a művelettel elkerülhető, hogy a virtuális gép lemeze kitöltse az összes feladatkártya-fájlt.
* Ha több feladat is ugyanazokat az eszközöket használja, és az egyes feladatokhoz csak növekményes változások tartoznak, akkor a rendszer az összes adategységet is másolja, még akkor is, ha csak egy részhalmaz lett frissítve.  Ez nem hatékony, ha sok nagy méretű objektum fájlja van.

Ha az adatfájlok újra felhasználhatók a feladatok között, és a feladatok között csak növekményes változások vannak, akkor a hatékonyabb, de valamivel nagyobb mértékben többek között az, hogy az eszközöket a virtuális gép megosztott mappájába tárolja, és szinkronizálja a módosított fájlokat.

* A feladat-előkészítési feladat a azcopy és a/XO paraméter használatával hajtja végre a másolást a AZ_BATCH_NODE_SHARED_DIR környezeti változó által meghatározott virtuálisgép-megosztott mappába.  Ez a beállítás csak a módosított fájlokat másolja át az egyes virtuális gépekre.
* Úgy kell megadnia az összes eszköz méretét, hogy azok megfeleljenek a készletben lévő virtuális gépek ideiglenes meghajtójára.

A Azure Batch beépített támogatást nyújt a fájlok másolásához a Storage-fiók és a Batch-készlet virtuális gépei között.  A Task [Resource Files](/rest/api/batchservice/job/add#resourcefile) fájlokat másol a tárolóból a készletbe tartozó virtuális gépekre, és megadható a feladat-előkészítési feladathoz.  Sajnos, ha több száz fájl van, lehetséges, hogy a korlátot és a sikertelen feladatokat kell megadnia.  Ha nagy számú eszköz van, ajánlott a azcopy parancssort használni a feladat-előkészítési feladatban, amely helyettesítő karaktereket is használhat, és nincs korlátja.

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>Kimeneti fájlok másolása blob Storage-tárolóba a Batch-készletből származó virtuális gépekről

A [kimeneti fájlok](/rest/api/batchservice/task/add#outputfile) a készletben lévő virtuális gépekről a tárolóba másolhatnak fájlokat.  A feladat befejezése után egy vagy több fájl átmásolható a virtuális gépről a megadott Storage-fiókba.  A megjelenített kimenetet át kell másolni, de érdemes lehet a naplófájlokat is tárolni.

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>Blobfuse virtuális fájlrendszer használata Linux rendszerű virtuális gépek készletei számára

A Blobfuse egy virtuális fájlrendszer-illesztőprogram az Azure Blob Storagehoz, amely lehetővé teszi a blobként tárolt fájlok elérését egy Storage-fiókban a Linux fájlrendszer használatával.

A készlet csomópontjai elindításkor csatlakoztathatók a fájlrendszerhez, vagy a csatlakoztatás egy feladat-előkészítési tevékenység részeként történik – egy feladat, amely csak akkor fut le, ha egy adott feladat első feladata egy csomóponton fut.  A Blobfuse konfigurálható úgy, hogy egy Ramdisk-t és a virtuális gépek helyi SSD-t is kihasználja a fájlok gyorsítótárazásához, ami jelentősen növeli a teljesítményt, ha egy csomóponton több feladat is hozzáfér egy adott fájlhoz.

A [sablonok](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount) az önálló V-Ray renderelés blobfuse-fájlrendszerrel való futtatására használhatók, és a más alkalmazásokhoz tartozó sablonok alapjául szolgálnak.

### <a name="accessing-files"></a>Fájlok elérése

A feladatok feladatai a csatlakoztatott fájlrendszer használatával határozzák meg a bemeneti fájlok és a kimeneti fájlok elérési útját.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Bemeneti adategység fájljainak másolása blob Storage-ból batch-készletbe tartozó virtuális gépekre

Mivel a fájlok egyszerűen Blobok az Azure Storage-ban, a standard blob API-kat, eszközöket és felületeket a rendszer a helyi fájlrendszer és a blob Storage közötti fájlok másolására használhatja. például: azcopy, Storage Explorer, Batch Explorer stb.

## <a name="using-azure-files-with-windows-vms"></a>Azure Files használata Windows rendszerű virtuális gépekkel

A [Azure Files](../storage/files/storage-files-introduction.md) teljes körűen felügyelt fájlmegosztást biztosít a felhőben, amely az SMB protokollon keresztül érhető el.  Azure Files az Azure Blob Storage-on alapul; [költséghatékony](https://azure.microsoft.com/pricing/details/storage/files/) , és konfigurálható adatreplikálással egy másik régióba, így globálisan redundáns.  A [méretezési célokat](../storage/files/storage-files-scale-targets.md#azure-files-scale-targets) felül kell vizsgálni annak megállapításához, hogy Azure filest kell-e használni az előrejelzési készlet mérete és az adategységek száma miatt.

Az Azure-fájlmegosztás csatlakoztatására vonatkozó [dokumentációt](../storage/files/storage-how-to-use-files-windows.md) tartalmaz.

### <a name="mounting-an-azure-files-share"></a>Azure Files-megosztás csatlakoztatása

A Batch szolgáltatásban való használathoz a csatlakoztatási műveletet minden alkalommal el kell végrehajtani, amikor egy feladat fut, mivel nem lehet megtartani a kapcsolatot a feladatok között.  Ennek a legegyszerűbb módja, ha a cmdkey használatával tartja meg a hitelesítő adatokat a készlet konfigurációjának indítási tevékenységével, majd minden feladat előtt csatlakoztatja a megosztást.

Példa a cmdkey használatára egy készlet sablonban (a JSON-fájlokban való használathoz) – vegye figyelembe, hogy amikor a cmdkey hívást a net use hívásból választja el, az indítási tevékenység felhasználói környezetének meg kell egyeznie a feladatok futtatásához használttal:

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

Példa a feladat feladatának parancssorára:
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

A feladatok feladatai a csatlakoztatott fájlrendszer használatával a bemeneti fájlok és a kimeneti fájlok elérési útját adják meg, vagy egy csatlakoztatott meghajtót vagy egy UNC elérési utat.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Bemeneti adategység fájljainak másolása blob Storage-ból batch-készletbe tartozó virtuális gépekre

A Azure Files az Azure Storage-támogatással rendelkező főbb API-k és eszközök támogatják. például: azcopy, Azure CLI, Storage Explorer, Azure PowerShell, Batch Explorer stb.

[Azure file Sync](../storage/files/storage-sync-files-planning.md) elérhető a fájlok automatikus szinkronizálása egy helyszíni fájlrendszer és egy Azure-fájlmegosztás között.

## <a name="next-steps"></a>Következő lépések

A tárolási lehetőségekről további információt a részletes dokumentációban talál:

* [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)
* [Blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md)
* [Azure Files](../storage/files/storage-files-introduction.md)
