---
title: Az Azure HPC cache összesített névterének ismertetése
description: Az Azure HPC-gyorsítótár virtuális névterének megtervezése
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 1c28f549cf93d77f6aef6bcde6a2225345a79cc9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91612948"
---
# <a name="plan-the-aggregated-namespace"></a>Az aggregált névtér tervezése

Az Azure HPC cache lehetővé teszi, hogy az ügyfelek különböző tárolási rendszerekhez férhessenek hozzá egy virtuális névtéren keresztül, amely elrejti a háttérrendszer részletes adatait.

A tárolási cél hozzáadása után beállíthat egy vagy több, az ügyfél felé irányuló névtér elérési útját a tárolási célra. Az ügyfélszámítógépek a fájl elérési útját csatlakoztatják, és a tárolórendszer közvetlen csatlakoztatása helyett a fájl olvasási kérelmét is elvégezhetik a gyorsítótárban.

Mivel az Azure HPC cache kezeli ezt a virtuális fájlrendszert, a tárolási célt úgy módosíthatja, hogy az ügyfél felé irányuló elérési utat módosítaná. Előfordulhat például, hogy az ügyféloldali eljárások újraírása nélkül lecseréli a hardveres tárolási rendszereket a felhőalapú tárolóba.

## <a name="aggregated-namespace-example"></a>Példa aggregált névtérre

Tervezze meg az összesített névteret, hogy az ügyfélszámítógépek kényelmesen el tudják érni a szükséges információkat, így a rendszergazdák és a munkafolyamat-mérnökök könnyedén azonosíthatják az elérési utakat.

Vegyünk például egy olyan rendszerrendszert, amelyben az Azure-blobban tárolt adatfeldolgozás során egy Azure HPC cache-példány használatos. Az elemzéshez a helyszíni adatközpontokban tárolt sablonfájlok szükségesek.

A sablon adatai egy adatközpontban tárolódnak, és a feladathoz szükséges információk a következő alkönyvtárakban tárolódnak:

* */goldline/templates/acme2017/sku798*
* */goldline/templates/acme2017/sku980*

Az adatközpont tárolási rendszere a következő exportálásokat teszi elérhetővé:

* */*
* */goldline*
* */goldline/templates*

Az elemezni kívánt adattípust a rendszer átmásolta egy "sourcecollectionnek" nevű Azure Blob Storage-tárolóba a [CLFSLoad segédprogram](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload)használatával.

A gyorsítótáron keresztüli egyszerű hozzáférés engedélyezéséhez érdemes lehet tárolási célokat létrehozni a következő virtuális névtér elérési útjaival:

| Háttérrendszer tárolási rendszere <br/> (NFS-fájl elérési útja vagy blob-tároló) | Virtuális névtér elérési útja |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| sourcecollectionnek                        | /Source               |

Egy NFS-tárolási cél több virtuális névtér elérési úttal is rendelkezhet, feltéve, hogy mindegyik egy egyedi exportálási útvonalra hivatkozik. (Az NFS- [névtér elérési útjai](add-namespace-paths.md#nfs-namespace-paths) olvashatók az NFS-tárolók által ajánlott maximális számú névtér-elérési utak megismeréséhez.)

Mivel az NFS-forrás elérési útjai azonos exportálási alkönyvtárak, meg kell határoznia több névtér elérési útját is ugyanabból a tárolási céltól.

| Tárolási cél állomásneve  | NFS-exportálási útvonal     | Alkönyvtár elérési útja | Névtér elérési útja    |
|--------------------------|---------------------|-------------------|-------------------|
| *IP-cím vagy állomásnév* | /goldline/templates | acme2017/sku798   | /templates/sku798 |
| *IP-cím vagy állomásnév* | /goldline/templates | acme2017/sku980   | /templates/sku980 |

Egy ügyfélalkalmazás csatlakoztathatja a gyorsítótárat, és egyszerűen elérheti az összesített névtér elérési útját, ``/source`` ``/templates/sku798`` és ``/templates/sku980`` .

Alternatív megoldás lehet egy olyan virtuális elérési út létrehozása `/templates` , amely a szülő könyvtárra mutató hivatkozásokat is tartalmaz, `acme2017` majd az ügyfelek a `sku798` gyorsítótár csatlakoztatása után megnyitják az egyes és a `sku980` címtárakat. A névtér elérési útja azonban nem hozható létre egy másik névtérbeli elérési út alkönyvtáraként. Ha tehát a könyvtár elérési útját hozza létre, `acme2017` nem hozhat létre olyan névteres elérési utat, amely közvetlenül hozzá tud férni az alkönyvtáraihoz.

Az Azure HPC gyorsítótár- **névtér** beállításai lapon látható az ügyféloldali fájlrendszer, amely lehetővé teszi az elérési utak hozzáadását vagy szerkesztését. A részletekért olvassa el [az összesített névtér beállítása](add-namespace-paths.md) című leírást.

## <a name="next-steps"></a>Következő lépések

Miután eldöntötte, hogyan állíthatja be a virtuális fájlrendszert, végezze el a következő lépéseket a létrehozásához:

* [Tárolási célok létrehozása](hpc-cache-add-storage.md) a háttérbeli tárolási rendszerek Azure HPC-gyorsítótárba való felvételéhez
* [Névtér elérési útjának hozzáadása](add-namespace-paths.md) az ügyfélszámítógépek által a fájlok eléréséhez használt összesített névtér létrehozásához
