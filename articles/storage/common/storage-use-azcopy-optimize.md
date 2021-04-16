---
title: Az AzCopy 10-es és Azure Storage-| Microsoft Docs
description: Ez a cikk segít optimalizálni az AzCopy 10-es és Azure Storage-beli teljesítményét.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: b60fc4b1fc20c455c2c409f544a8af16f1dbf8d1
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509083"
---
# <a name="optimize-the-performance-of-azcopy-with-azure-storage"></a>Az AzCopy teljesítményének optimalizálása az Azure Storage használatával

Az AzCopy egy parancssori segédprogram, amellyel blobokat és fájlokat másolhat a tárfiókok között. Ez a cikk segít optimalizálni a teljesítményt.

> [!NOTE]
> Ha az AzCopy első lépésekben segítő tartalmat keres, tekintse meg az AzCopy első [lépésekről](storage-use-azcopy-v10.md)

Teljesítményteszteket használhat, majd parancsokkal és környezeti változók használatával optimális kapcsolatot találhat a teljesítmény és az erőforrás-használat között.

## <a name="run-benchmark-tests"></a>Teljesítménytesztek futtatása

Teljesítménytesztet futtathat adott blobtárolókon vagy fájlmegosztáson az általános teljesítménystatisztikák megtekintéséhez és a teljesítmény szűk keresztmetszetének azonosításához. A tesztet a létrehozott tesztadatok feltöltésével vagy letöltésével futtathatja. 

A következő paranccsal futtathat teljesítménytesztet.

**Syntax**

`azcopy benchmark 'https://<storage-account-name>.blob.core.windows.net/<container-name>'`

**Példa**

```azcopy
azcopy benchmark 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

> [!TIP]
> Ez a példa az elérésiút-argumentumokat ad meg egyszeres idézőjelek ('') közé. Használjon egyszeres idézőjeleket az összes parancshéjban, kivéve a Windows parancshéjat (cmd.exe). Ha Windows parancshéjat (cmd.exe) használ, az elérésiút-argumentumokat idézőjelek ("") közé kell tenni a single quotes ('' helyett).

Ez a parancs teljesítménytesztet futtat tesztadatok feltöltésével egy megadott célhelyre. A tesztadatok a memóriában jönnek létre, feltöltve a célhelyre, majd a teszt befejezése után törlődnek a célhelyről. Opcionális parancsparaméterek használatával megadhatja, hogy hány fájlt hozzon létre, és milyen méretű legyen.

Ha inkább adatok letöltésével szeretné futtatni a tesztet, állítsa a paramétert a `mode` következőre: `download` . Részletes referencia-dokumentumokért lásd: [azcopy benchmark.](storage-ref-azcopy-bench.md) 

## <a name="optimize-for-large-numbers-of-small-files"></a>Optimalizálás nagy mennyiségű kis fájlra

Kisebb fájlok átvitelekor az átviteli sebesség csökkenhet, különösen nagy számú fájl átvitelekor. A teljesítmény maximalizálása érdekében csökkentse az egyes feladatméreteket. A letöltési és feltöltési műveletekhez növelje az egyidejűséget, csökkentse a naplótevékenységet, és kapcsolja ki a magas teljesítményköltségeket eredményező szolgáltatásokat.

#### <a name="reduce-the-size-of-each-job"></a>Az egyes feladat méretének csökkentése

Az optimális teljesítmény elérése érdekében győződjön meg arról, hogy minden feladat 10 milliónál kevesebb fájlt továbbít. Az 50 milliónál több fájlt átvitelt végző feladatok gyenge teljesíthet, mivel az AzCopy feladatkövetési mechanizmusa jelentős többletterhelést okoz. A többletterhelés csökkentése érdekében érdemes lehet a nagy feladatokat kisebb feladatokra osztani. 

A feladat méretének csökkentésére az egyik módszer a feladat által érintett fájlok számának korlátozása. Ezt parancsparaméterekkel is meg lehet tenni. Egy feladat például csak a könyvtárak egy részkészletét másolhatja az azcopy copy parancs részeként a paraméter `include path` használatával. [](storage-ref-azcopy-copy.md) 

A `include-pattern` paraméterrel másolhatja a megadott kiterjesztésű fájlokat (például: `*.pdf` ). Egy külön feladatban használja a paramétert az összes olyan fájl másolására, amely nem tartalmaz `exclude-pattern` `*.pdf` kiterjesztést. Példákért [lásd: Adott fájlok feltöltése](storage-use-azcopy-blobs-upload.md#upload-specific-files) és Adott [blobok](storage-use-azcopy-blobs-download.md#download-specific-blobs) letöltése.

Miután eldöntötte, hogyan ossza fel a nagyméretű feladatokat kisebb feladatokra, fontolja meg a feladatok több virtuális gépen való futtatását.

#### <a name="increase-concurrency"></a>Az egyidejűség növelése

Ha fájlokat tölt fel vagy tölt le, a környezeti változóval növelheti a gépen előforduló egyidejű `AZCOPY_CONCURRENCY_VALUE` kérések számát. Állítsa be ezt a változót a lehető legnagyobbra a gép teljesítményének veszélyeztetése nélkül. Erről a változóról a cikk [Egyidejű](#increase-the-number-of-concurrent-requests) kérelmek számának növelése című szakaszában talál további információt.

Ha blobokat másol a tárfiókok között, fontolja meg a környezeti változó értékének nagyobb értékre `AZCOPY_CONCURRENCY_VALUE` való `1000` beállítását. Ezt a változót magasra állíthatja, mert az AzCopy kiszolgálók közötti API-kat használ, így az adatok közvetlenül a tárolókiszolgálók között vannak átmásolva, és nem használják a gép feldolgozási kapacitását.  

#### <a name="decrease-the-number-of-logs-generated"></a>A létrehozott naplók számának csökkentése

Javíthatja a teljesítményt, ha csökkenti az AzCopy által a művelet befejezése során létrehozott naplóbejegyzések számát. Alapértelmezés szerint az AzCopy a művelethez kapcsolódó összes tevékenységet naplózza. Az optimális teljesítmény eléréséhez fontolja meg a másolási, szinkronizálási vagy eltávolítási parancs paraméterének `log-level` beállítását a következőre: `ERROR` . Így az AzCopy csak a hibákat naplózza. Alapértelmezés szerint az értéknapló szintje értékre van `INFO` állítva. 

#### <a name="turn-off-length-checking"></a>A hossz-ellenőrzés kikapcsolása 

Ha fájlokat tölt fel vagy tölt le, fontolja meg a másolási és szinkronizálási parancsok beállítását a `--check-length` következőre: `false` . Ez megakadályozza, hogy az AzCopy ellenőrizze egy fájl hosszát az átvitel után. Alapértelmezés szerint az AzCopy ellenőrzi a hosszt, hogy a forrás- és célfájlok egyeznek-e az átvitel befejezése után. Az AzCopy ezt az ellenőrzést minden fájlátvitel után elvégzi. Ez az ellenőrzés csökkentheti a teljesítményt, ha a feladatok nagy mennyiségű kis fájlt továbbítnak. 

#### <a name="turn-on-concurrent-local-scanning-linux"></a>Egyidejű helyi vizsgálat bekapcsolás (Linux)

Egyes Linux rendszereken a fájlvizsgálatok nem futnak elég gyorsan az összes párhuzamos hálózati kapcsolat telítettségéhez. Ezekben az esetekben beállíthatja a következőt: `AZCOPY_CONCURRENT_SCAN` `true` . 

## <a name="increase-the-number-of-concurrent-requests"></a>Az egyidejű kérések számának növelése

Az átviteli sebességet a környezeti változó beállításával `AZCOPY_CONCURRENCY_VALUE` növelheti. Ez a változó meghatározza az egyidejű kérelmek lehetséges számát.  

Ha a számítógép kevesebb mint 5 PROCESSZORt használ, akkor ennek a változónak az értéke `32` . Egyéb esetben az alapértelmezett érték a CPU-k száma szorozva 16-tal. Ennek a változónak a maximális alapértelmezett értéke , de manuálisan is beállíthatja ezt az értéket `3000` magasabbra vagy alacsonyabbra. 

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **macOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

A változó aktuális értékének ellenőrzéshez használja `azcopy env` a értéket. Ha az érték üres, akkor bármely AzCopy-naplófájl elejére beolvashatja, hogy melyik értéket használja a rendszer. A kiválasztott érték és a kiválasztott érték oka itt lesz jelentve.

A változó beállítása előtt javasoljuk, hogy futtatassa a teljesítménytesztet. A teljesítményteszt tesztelési folyamata az ajánlott egyidejűségi értéket fogja jelenteni. Ha a hálózati feltételek és a hasznos adatok eltérőek, akkor ezt a változót állítsa a szóra egy adott szám `AUTO` helyett. Ennek hatására az AzCopy mindig ugyanazt az automatikus hangolási folyamatot futtatja, mint a teljesítménytesztek során.

## <a name="limit-the-throughput-data-rate"></a>Az átviteli sebesség korlátozása

A parancsokban a jelzővel felső határt is be lehet látni `cap-mbps` az átviteli sebességre. A következő parancs például folytatja a feladatot, és másodpercenként `10` megabitre (Mb) sebességet ad vissza. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

## <a name="optimize-memory-use"></a>Memóriahasználat optimalizálása

Állítsa be a környezeti változót a rendszermemória azon maximális mennyiségének megadásához, amit az AzCopy pufferel a fájlok letöltésekor és `AZCOPY_BUFFER_GB` feltöltésekor. Ezt az értéket gigabájtban (GB) kifejezve.

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **macOS** | `export AZCOPY_BUFFER_GB=<value>` |

> [!NOTE]
> A feladatkövetés mindig további többletterhelést okoz a memóriahasználatban. A mennyiség a feladaton belüli átadások számán alapul. A pufferek a memóriahasználat legnagyobb összetevője. A használatával szabályozhatja a terhelést, hogy az hozzávetőlegesen megfeleljen a követelményeknek, de nincs olyan jelző, amely szigorúan korlátozni tudja a teljes `AZCOPY_BUFFER_GB` memóriahasználatot.

## <a name="optimize-file-synchronization"></a>Fájlszinkronizálás optimalizálása

A [szinkronizálási](storage-ref-azcopy-sync.md) parancs azonosítja a célhelyen lévő összes fájlt, majd összehasonlítja a fájlneveket és a legutóbb módosított időbélyegeket a szinkronizálási művelet indítása előtt. Ha sok fájlja van, akkor javíthatja a teljesítményt ennek az előfeldolgozásnak a megszüntetésével. 

Ehhez használja helyette az [azcopy copy](storage-ref-azcopy-copy.md) parancsot, és állítsa a `--overwrite` jelzőt a következőre: `ifSourceNewer` . Az AzCopy a másolásuk során anélkül hasonlítja össze a fájlokat, hogy bármilyen vizsgálatot és összehasonlítást végez. Ez teljesítménybeli peremhálózatot biztosít olyan esetekben, amikor nagy számú fájlt kell összehasonlítani.

Az [azcopy copy](storage-ref-azcopy-copy.md) parancs nem töröl fájlokat a célhelyről, ezért ha törölni szeretné a fájlokat a célhelyen, amikor azok már nem léteznek a forrásnál, akkor használja az [azcopy sync](storage-ref-azcopy-sync.md) parancsot, és a jelző értéke vagy `--delete-destination` `true` `prompt` .

## <a name="see-also"></a>Lásd még

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)