---
title: azcopy load clfs | Microsoft Docs
titleSuffix: Azure Storage
description: Ez a cikk az azcopy load clfs parancs referenciainformációit biztosítja.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ebf04531f29e18f9d120ca2efa17244c4282084c
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503269"
---
# <a name="azcopy-load-clfs"></a>azcopy load clfs

Helyi adatokat továbbít egy tárolóba, és a Microsoft Avere Cloud FileSystem (CLFS) formátumában tárolja őket.

## <a name="synopsis"></a>Áttekintés

A betöltés parancs adatokat másol az Azure Blob Storage-tárolókba, majd az adatokat a Microsoft Avere Cloud FileSystem (CLFS) formátumában tárolja. A jogvédett CLFS formátumot a szoftver- és Azure HPC Cache Avere vFXT for Azure használják.

A parancs használhatja a szükséges bővítményt a következőn keresztül: pip3 install clfsload~=1.0.23. Győződjön meg CLFSLoad.py, hogy az elérési útban van. További információ erről a lépésről: [https://aka.ms/azcopy/clfs](https://aka.ms/azcopy/clfs) .

Ez a parancs egy egyszerű lehetőség a meglévő adatok felhőalapú tárolóba való áthelyezésére adott Microsoft nagy teljesítményű számítási gyorsítótár-termékekkel való használatra. 

Mivel ezek a termékek saját felhőbeli fájlrendszert használnak az adatok kezeléséhez, ezek az adatok nem tölthetők be a natív másolási paranccsal. 

Ehelyett az adatokat magát a gyorsítótárterméket kell betölteni, vagy ezzel a betöltési paranccsal, amely a megfelelő jogvédett formátumot használja.
Ez a parancs lehetővé teszi az adatok gyorsítótár használata nélküli átvitelét. Például a tároló előzetes feltöltéséhez vagy fájlok munkakészlethez való hozzáadásához a gyorsítótár terhelésének növelése nélkül.

A célhely egy üres Azure Storage-tároló. Az átvitel befejezése után a céltároló használható egy Azure HPC Cache-Avere vFXT for Azure fürtben.

> [!NOTE] 
> Ez a betöltés parancs előzetes kiadása. Jelentse a problémákat az AzCopy GitHub-adattárban.

```
azcopy load clfs [local dir] [container URL] [flags]
```

## <a name="related-conceptual-articles"></a>Kapcsolódó elméleti cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele az AzCopy és a Blob Storage használatával](./storage-use-azcopy-v10.md#transfer-data)
- [Adatok átvitele az AzCopy használatával és fájltárolás](storage-use-azcopy-files.md)

## <a name="examples"></a>Példák

Töltsön be egy teljes könyvtárat egy CLFS formátumú SAS-t tartalmazó tárolóba:

```azcopy
azcopy load clfs "/path/to/dir" "https://[account].blob.core.windows.net/[container]?[SAS]" --state-path="/path/to/state/path"
```

## <a name="options"></a>Beállítások

**A --compression-type sztring** határozza meg az átvitelhez használni kívánt tömörítési típust. Az elérhető értékek a következőek: `DISABLED` , `LZ4` . (alapértelmezett `LZ4` )

**--help**    help a `azcopy load clfs` parancshoz.

**--log-level string** Adja meg a naplófájl részletes naplóját az elérhető szintekkel: `DEBUG` , , , `INFO` `WARNING` `ERROR` . (alapértelmezett `INFO` )

**--max-errors** uint32 Adja meg a megengedett átviteli hibák maximális számát. Ha elegendő hiba fordul elő, azonnal állítsa le a feladatot.

**--new-session**   Indíts el egy új feladatot ahelyett, hogy folytatná azt a meglévő feladatot, amelynek követési információi a oldalon `--state-path` maradnak. (alapértelmezett igaz)

**--preserve-hardlinks**    A nem tartós kapcsolati kapcsolatok megőrzése.

**--state-path sztring** Kötelező elérési út egy helyi könyvtárhoz a feladatállapot nyomon követéséhez. A feladat folytatásához az elérési útnak egy meglévő könyvtárra kell mutasson. Új feladathoz üresnek kell lennie.

## <a name="options-inherited-from-parent-commands"></a>A szülőparancsok által örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--cap-mbps lebegőpontos érték|Megabit/másodpercben megszabja az átviteli sebességet. A pillanatnyi átviteli sebesség kis mértékben eltérhet a felső felsőértéktől. Ha ez a beállítás nulla vagy nincs megadva, az átviteli sebesség nincs korlátozva.|
|--output-type string|A parancs kimenetének formátuma. A lehetőségek a következők: text, json. Az alapértelmezett érték a "text".|
|--trusted-microsoft-suffixes sztring   | További tartomány-utótagokat ad meg, Azure Active Directory bejelentkezési jogkivonatokat lehet küldeni.  Az alapértelmezett érték a '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Az itt felsoroltak hozzáadva az alapértelmezetthez. A biztonság érdekében itt csak a Microsoft Azure helyezzen el. Több bejegyzést pontosvesszővel válassza el egymástól.|

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
