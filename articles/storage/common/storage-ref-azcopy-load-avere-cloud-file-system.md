---
title: azcopy betöltési CLFS | Microsoft Docs
titleSuffix: Azure Storage
description: Ez a cikk a azcopy Load CLFS paranccsal kapcsolatos tudnivalókat tartalmaz.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b36ea25180c31fef199aaacb10e46b3caa20f807
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98878375"
---
# <a name="azcopy-load-clfs"></a>azcopy load clfs

Helyi adatátvitelt végez egy tárolóba, és tárolja azt a Microsoft avere Cloud FileSystem (CLFS) formátumában.

## <a name="synopsis"></a>Áttekintés

A Load paranccsal az Azure Blob Storage-tárolóba másolhatja az adatfájlokat, majd a Microsoft avere Cloud FileSystem (CLFS) formátumában tárolja azokat. A tulajdonosi CLFS formátumát az Azure HPC cache és az Azure-termékek avere vFXT használják.

A parancs kihasználása érdekében telepítse a szükséges bővítményt a következőn keresztül: pip3 install clfsload ~ = 1.0.23. Győződjön meg arról, hogy a CLFSLoad.py az elérési útban van. További információt erről a lépésről a következő webhelyen talál: [https://aka.ms/azcopy/clfs](https://aka.ms/azcopy/clfs) .

Ez a parancs egy egyszerű lehetőség a meglévő adat Felhőbeli tárhelyre való áthelyezésére a Microsoft nagy teljesítményű számítási gyorsítótár-termékeivel való használatra. 

Mivel ezek a termékek egy szabadalmaztatott felhőalapú fájlrendszer-formátumot használnak az adatkezeléshez, az adott Adatmásolás nem tölthető be a natív másolási paranccsal. 

Ehelyett az adatoknak a gyorsítótár-terméken keresztül vagy a betöltési parancs használatával kell betölteni, amely a megfelelő saját formátumot használja.
Ez a parancs lehetővé teszi az adatok átvitelét a gyorsítótár használata nélkül. Például a tárterület előzetes kitöltéséhez vagy fájlok egy munkakészlethez való hozzáadásához a gyorsítótár terhelésének növelése nélkül.

A cél egy üres Azure Storage-tároló. Az átvitel befejezésekor a cél tároló használható Azure HPC cache-példánnyal vagy avere-vFXT az Azure-fürthöz.

> [!NOTE] 
> Ez a Load parancs előzetes verziója. Kérjük, jelentse a AzCopy GitHub-tárházban felmerülő problémákat.

```
azcopy load clfs [local dir] [container URL] [flags]
```

## <a name="related-conceptual-articles"></a>Kapcsolódó fogalmi cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele a AzCopy és a blob Storage szolgáltatással](./storage-use-azcopy-v10.md#transfer-data)
- [Adatok átvitele az AzCopy használatával és fájltárolás](storage-use-azcopy-files.md)
- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)

## <a name="examples"></a>Példák

Töltse be a teljes könyvtárat egy tárolóba egy SAS-CLFS formátumban:

```azcopy
azcopy load clfs "/path/to/dir" "https://[account].blob.core.windows.net/[container]?[SAS]" --state-path="/path/to/state/path"
```

## <a name="options"></a>Beállítások

**--Compression-Type** karakterlánc adja meg az átvitelekhez használandó tömörítési típust. Az elérhető értékek a következők: `DISABLED` , `LZ4` . (alapértelmezett `LZ4` )

**– Súgó**    a `azcopy load clfs` parancshoz.

**--a log-Level** sztring a naplófájl részletességét határozza meg, a rendelkezésre álló szintek: `DEBUG` , `INFO` , `WARNING` , `ERROR` . (alapértelmezett `INFO` )

**--Max-errors** Uint32 adja meg az átadási hibák maximális számát. Ha elég hiba történik, azonnal állítsa le a feladatot.

**– új munkamenet**   Az új feladatok elindításához nem kell megtartania egy meglévőt, amelynek nyomon követési adatai megmaradnak `--state-path` . (alapértelmezett true)

**--megőrzés – hardlinks**    A rögzített kapcsolati kapcsolatok megőrzése.

**--az állapot-elérésiút-** karakterláncnak meg kell adnia egy helyi könyvtár elérési útját a feladatok állapotának nyomon követéséhez. Az elérési útnak egy meglévő könyvtárra kell mutatnia a feladatok folytatása érdekében. Új feladatokhoz üresnek kell lennie.

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--Cap-Mbps lebegőpontos|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|
|--megbízható-Microsoft-utótagok karakterlánca   | További tartomány-utótagokat határoz meg, amelyekben Azure Active Directory bejelentkezési tokenek küldhetők.  Az alapértelmezett érték: "*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Az itt felsorolt beállítások az alapértelmezett értékre kerülnek. A biztonság érdekében itt csak Microsoft Azure-tartományokat helyezhet el. Több bejegyzést pontosvesszővel kell elválasztani.|

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
