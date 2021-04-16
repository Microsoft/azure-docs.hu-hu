---
title: azcopy remove | Microsoft Docs
description: Ez a cikk az azcopy remove parancs referenciainformációit biztosítja.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: bd221215d6be3c14ce1200e8bd374a97cb7608a0
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503014"
---
# <a name="azcopy-remove"></a>azcopy remove

Blobok vagy fájlok törlése egy Azure Storage-fiókból.

## <a name="synopsis"></a>Áttekintés

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Kapcsolódó elméleti cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele az AzCopy és a Blob Storage használatával](./storage-use-azcopy-v10.md#transfer-data)
- [Adatok átvitele az AzCopy használatával és fájltárolás](storage-use-azcopy-files.md)

## <a name="examples"></a>Példák

Egyetlen blob eltávolítása SAS-jogkivonat használatával:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Egy teljes virtuális könyvtár eltávolítása SAS-jogkivonat használatával:
 
```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Csak a virtuális könyvtáron belüli blobokat távolítsa el, az alkönyvtárakból és blobokból azonban ne távolítson el egyetlen alkönyvtárat sem:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Távolítsa el a blobok egy részkészletét egy virtuális könyvtárból (például csak jpg- és pdf-fájlokat távolítson el, vagy ha a blob neve `exactName` ):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include-pattern="*.jpg;*.pdf;exactName"
```

Eltávolíthat egy teljes virtuális könyvtárat, de kizárhat bizonyos blobokat a hatókörből (például minden olyan blobot, amely foo-val kezdődik vagy sávgal végződik):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude-pattern="foo*;*bar"
```

Távolítsa el a konkrét blobokat és virtuális könyvtárakat úgy, hogy a relatív elérési útjukat (NEM URL-kódolású) egy fájlba írja:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
- file content:
    dir1/dir2
    blob1
    blob2
```
Egyetlen fájlt távolíthat el egy hierarchikus Blob Storage -fiókból (az include/exclude nem támogatott):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Egyetlen címtár eltávolítása egy hierarchikus Blob Storage (a be- és kizárás nem támogatott) nevű fiókból:

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Beállítások

**--delete-snapshots sztring** Alapértelmezés szerint a törlési művelet meghiúsul, ha egy blob pillanatképekkel rendelkezik. Adja meg, hogy eltávolítsa a gyökérblobot és annak összes pillanatképét; vagy adja meg, hogy csak a pillanatképeket távolítsa el, de tartsa `include` `only` meg a gyökérblobot.

**--exclude-path sztring** Eltávolításkor zárja ki ezeket az elérési utakat. Ez a beállítás nem támogatja a helyettesítő karaktereket (*). Ellenőrzi a relatív elérési út előtagját. Például: `myFolder;myFolder/subDirName/file.pdf`

**--exclude-pattern sztring** Azon fájlok kizárása, amelyek neve megegyezik a mintalistával. Például: `*.jpg` ; `*.pdf` ;`exactName`

**--force-if-read-only**   Ha töröl egy Azure Files fájlt vagy mappát, akkor is kényszerítenie kell a törlést, hogy akkor is működjön, ha a meglévő objektum rendelkezik írási attribútumkészletével.

**--help**   help for remove.

**--include-path sztring** Csak ezeket az elérési utakat foglalja bele az eltávolításkor. Ez a beállítás nem támogatja a helyettesítő karaktereket (*). Ellenőrzi a relatív elérési út előtagját. Például: `myFolder;myFolder/subDirName/file.pdf`

**--include-pattern sztring** Csak azokat a fájlokat foglalja bele, amelyek neve megegyezik a mintalistával. Például: *`.jpg` ;* `.pdf` ;`exactName`

**--list-of-files** sztring Egy olyan fájl helyét határozza meg, amely a törölni kívánt fájlok és könyvtárak listáját tartalmazza. A relatív elérési utakat sortörésekkel kell elválasztva, és az elérési utak NEM URL-kódolásúak. 

**--list-of-versions sztring** Egy olyan fájlt ad meg, amelyben az egyes verzióazonosítók külön sorban vannak felsorolva. Győződjön meg arról, hogy a forrásnak egyetlen blobra kell mutasson, és a jelölőt használó fájlban megadott összes verzió-értéknek csak a forrásblobhoz kell tartozni. Az adott blob megadott verziószámai törölve lesznek az Azure Storage-ból. 

**--log-level string** Adja meg a naplófájl részletes naplóját. A rendelkezésre álló szintek a következők: `INFO` (minden kérés/válasz), (lassú válaszok), (csak sikertelen kérések) és `WARNING` `ERROR` `NONE` (kimeneti naplók nélkül). (alapértelmezett `INFO` ) (alapértelmezett `INFO` )

**--rekurzív**    A könyvtárak közötti szinkronizáláskor tárja fel a rekurzív alkönyvtárakat.

## <a name="options-inherited-from-parent-commands"></a>A szülőparancsok által örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--cap-mbps lebegőpontos érték|Megabit/másodpercben megszabja az átviteli sebességet. A pillanatnyi átviteli sebesség kis mértékben eltérhet a felső teljesítménytől. Ha ez a beállítás nulla vagy nincs megadva, az átviteli sebesség nincs korlátozva.|
|--output-type string|A parancs kimenetének formátuma. A lehetőségek a következők: text, json. Az alapértelmezett érték a "text".|
|--trusted-microsoft-suffixes sztring   |További tartomány-utótagokat ad meg, Azure Active Directory bejelentkezési jogkivonatokat lehet küldeni.  Az alapértelmezett érték a '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Az itt felsoroltak hozzáadva az alapértelmezetthez. A biztonság érdekében itt csak a Microsoft Azure helyezzen el. Több bejegyzést pontosvesszővel válassza el egymástól.|

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
