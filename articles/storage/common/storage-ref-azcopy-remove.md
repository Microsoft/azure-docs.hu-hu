---
title: azcopy eltávolítása | Microsoft Docs
description: Ez a cikk a azcopy Remove parancsra vonatkozó tudnivalókat tartalmazza.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d28a20a6afc78939491e56f46a32782b4c0844cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879003"
---
# <a name="azcopy-remove"></a>azcopy remove

Blobok vagy fájlok törlése egy Azure Storage-fiókból.

## <a name="synopsis"></a>Áttekintés

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Kapcsolódó fogalmi cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele a AzCopy és a blob Storage szolgáltatással](./storage-use-azcopy-v10.md#transfer-data)
- [Adatok átvitele az AzCopy használatával és fájltárolás](storage-use-azcopy-files.md)
- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)

## <a name="examples"></a>Példák

Egyetlen blob eltávolítása SAS-token használatával:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Teljes virtuális könyvtár eltávolítása SAS-token használatával:
 
```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Csak a virtuális könyvtárak belsejében lévő blobokat távolítsa el, de ne távolítsa el az alkönyvtárakban található alkönyvtárakat vagy blobokat:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Blobok egy részhalmazának eltávolítása egy virtuális könyvtárban (például: csak jpg-és PDF-fájlok eltávolítása, vagy a blob neve `exactName` ):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include-pattern="*.jpg;*.pdf;exactName"
```

Távolítson el egy teljes virtuális könyvtárat, de zárja ki bizonyos blobokat a hatókörből (például: minden olyan blob, amely a foo vagy a Bar végződéssel kezdődik):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude-pattern="foo*;*bar"
```

Távolítsa el a megadott blobokat és virtuális könyvtárakat úgy, hogy a relatív elérési utakat (nem pedig URL-kódolású) egy fájlba helyezi:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
- file content:
    dir1/dir2
    blob1
    blob2
```
Egyetlen fájl eltávolítása olyan Blob Storage-fiókból, amely hierarchikus névtérrel rendelkezik (belefoglalása/kizárása nem támogatott):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Egyetlen könyvtár eltávolítása olyan Blob Storage-fiókból, amely hierarchikus névtérrel rendelkezik (belefoglalása/kizárása nem támogatott):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Beállítások

**--delete-Pillanatképek** sztring alapértelmezés szerint a törlési művelet meghiúsul, ha egy blob pillanatképekkel rendelkezik. `include`Ezzel a beállítással távolíthatja el a legfelső szintű blobot és annak összes pillanatképét, vagy megadhatja `only` , hogy csak a pillanatképek legyenek eltávolítva, de ne

**--kizárás – a Path** karakterlánc kizárja ezeket az elérési utakat a eltávolításakor. Ez a beállítás nem támogatja a helyettesítő karaktereket (*). Ellenőrzi a relatív elérési út előtagját. Például: `myFolder;myFolder/subDirName/file.pdf`

**--kizárás-Pattern** karakterlánc zárja ki azokat a fájlokat, amelyeknek a neve megegyezik a minta listával. Például: `*.jpg` ; `*.pdf` ;`exactName`

**--Force-if-csak olvasható**   Azure Files fájl vagy mappa törlésekor kényszerítse a törlést a működésre, még akkor is, ha a meglévő objektum írásvédett attribútuma van beállítva.

**– Súgó**   az eltávolításhoz.

a- **-include-Path** sztring csak az eltávolításkor megadott elérési utakat tartalmazza. Ez a beállítás nem támogatja a helyettesítő karaktereket (*). Ellenőrzi a relatív elérési út előtagját. Például: `myFolder;myFolder/subDirName/file.pdf`

**--include-Pattern** sztring csak olyan fájlokat tartalmazhat, amelyekben a név megegyezik a minta listával. Például: *`.jpg` ;* `.pdf` ;`exactName`

**--a-Files List-of-** Files karakterlánc határozza meg egy fájl helyét, amely a törlendő fájlok és könyvtárak listáját tartalmazza. A relatív elérési utakat sortöréssel kell elválasztani, és az elérési utakat nem szabad URL-kódolással ellátni. 

a **--Versions** karakterlánc egy olyan fájlt ad meg, amelyben minden verzióazonosító külön sorban szerepel. Győződjön meg arról, hogy a forrásnak egyetlen blobra kell mutatnia, és a fájlban megadott összes verzióazonosító csak a forrás blobhoz tartozhat. Az adott blob megadott verzióazonosító-azonosítóit a rendszer az Azure Storage-ból fogja törölni. 

**--a log-Level** karakterlánc határozza meg a naplófájl részletességét. A rendelkezésre álló szintek a következők: `INFO` (minden kérés/válasz), `WARNING` (lassú válasz), `ERROR` (csak sikertelen kérelmek) és `NONE` (nincs kimeneti napló). (alapértelmezett `INFO` ) (alapértelmezett `INFO` )

**– rekurzív**    A címtárak közötti szinkronizáláskor a program rekurzív módon vizsgálja az alkönyvtárakat.

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--Cap-Mbps lebegőpontos|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|
|--megbízható-Microsoft-utótagok karakterlánca   |További tartomány-utótagokat határoz meg, amelyekben Azure Active Directory bejelentkezési tokenek küldhetők.  Az alapértelmezett érték: "*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Az itt felsorolt beállítások az alapértelmezett értékre kerülnek. A biztonság érdekében itt csak Microsoft Azure-tartományokat helyezhet el. Több bejegyzést pontosvesszővel kell elválasztani.|

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
