---
title: azcopy szinkronizálása | Microsoft Docs
description: Ez a cikk a azcopy Sync paranccsal kapcsolatos tudnivalókat tartalmaz.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: dc3451a4b46a317dccda0e4292dcb1712b4171f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878307"
---
# <a name="azcopy-sync"></a>azcopy sync

A forrás helyét replikálja a célhelyre.

## <a name="synopsis"></a>Áttekintés

A rendszer az utolsó módosítás időpontját használja az összehasonlításhoz. A rendszer kihagyja a fájlt, ha a cél utolsó módosításának ideje újabb.

A támogatott párok a következők:

- helyi < – > Azure-Blob (SAS-vagy OAuth-hitelesítés is használható)
- Azure Blob <-> Azure Blob (a forrásnak tartalmaznia kell SAS-t vagy nyilvánosan elérhetőnek kell lennie, vagy az SAS vagy a OAuth hitelesítés használható a célhelyre)
- Azure file <-> Azure-fájl (a forrásnak tartalmaznia kell SAS vagy nyilvánosan elérhető; Az SAS-hitelesítést a célhelyre kell használni.)

A szinkronizálási parancs többféle módon eltér a másolási parancstól:

1. Alapértelmezés szerint a rekurzív jelző igaz értékű, és a szinkronizálás az összes alkönyvtárat átmásolja. A szinkronizálás csak akkor másolja a legfelső szintű fájlokat egy könyvtárba, ha a rekurzív jelző hamis.
2. A virtuális könyvtárak közötti szinkronizáláskor adjon hozzá egy perjelet az elérési úthoz (példák), ha van olyan blob, amelynek a neve megegyezik a virtuális könyvtárak egyikével.
3. Ha a `deleteDestination` jelző értéke TRUE (igaz) vagy prompt, akkor a Sync törli azokat a fájlokat és blobokat a célhelyen, amelyek nem szerepelnek a forrásban.

## <a name="related-conceptual-articles"></a>Kapcsolódó fogalmi cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele a AzCopy és a blob Storage szolgáltatással](./storage-use-azcopy-v10.md#transfer-data)
- [Adatok átvitele az AzCopy használatával és fájltárolás](storage-use-azcopy-files.md)
- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)

### <a name="advanced"></a>Felsőfokú

Ha nem ad meg fájlkiterjesztést, a AzCopy automatikusan észleli a fájlok tartalomtípusát a helyi lemezről történő feltöltéskor a fájlkiterjesztés vagy a tartalom alapján (ha nincs megadva kiterjesztés).

A beépített keresési táblázat kicsi, de a UNIX rendszeren a helyi rendszer MIME. types fájl (ok), ha az alábbi nevek közül egy vagy több található:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

Windows rendszeren a MIME-típusokat a rendszer kinyeri a beállításjegyzékből.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>Példák

Egyetlen fájl szinkronizálása:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Ugyanaz, mint a fenti, de a fájl tartalmának MD5 kivonatát is kiszámítja, majd a blob Content-MD5 tulajdonságának mentse az MD5-kivonatot. 

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Egy teljes könyvtár szinkronizálása az alkönyvtáraival együtt (a rekurzív alapértelmezés szerint):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

vagy

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Csak a könyvtárban lévő fájlokat szinkronizálja, de alkönyvtárakat vagy alkönyvtárakban lévő fájlokat nem:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Fájlok egy részhalmazának szinkronizálása egy címtárban (például: csak jpg-és PDF-fájlok, vagy ha a fájl neve `exactName` ):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include-pattern="*.jpg;*.pdf;exactName"
```

Egy teljes címtár szinkronizálása, de bizonyos fájlok kizárása a hatókörből (például: minden olyan fájl, amely foo-vel kezdődik, vagy a sáv végén végződik):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude-pattern="foo*;*bar"
```

Egyetlen blob szinkronizálása:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Virtuális könyvtár szinkronizálása:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Egy blobtal megegyező nevű virtuális könyvtár szinkronizálása (a egyértelműsítse az elérési útra a záró perjelet adja hozzá):

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Azure file Directory szinkronizálása (a Blobtal megegyező szintaxis):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Ha a belefoglalási/kizárási jelzők együtt vannak használatban, csak a belefoglalási mintáknak megfelelő fájlok fognak megjelenni, a kizárási mintáknak megfelelők azonban mindig figyelmen kívül lesznek hagyva.

## <a name="options"></a>Beállítások

**--Block-Size-MB** lebegőpontos használata esetén ez a blokk mérete (a MIB-ben van megadva) az Azure Storage-ba való feltöltéskor vagy az Azure Storage-ból való letöltéskor. A rendszer automatikusan kiszámítja az alapértelmezett értéket a fájlméret alapján. A tizedes törtek engedélyezettek (például: `0.25` ).

**--ellenőrzés-MD5** karakterlánc megadja, hogy a letöltéskor milyen szigorúan kell ellenőrizni az MD5-kivonatok érvényességét. Ez a lehetőség csak a letöltéskor érhető el. Az elérhető értékek a következők: `NoCheck` , `LogOnly` , `FailIfDifferent` , `FailIfDifferentOrMissing` . (alapértelmezett `FailIfDifferent` ). (alapértelmezett `FailIfDifferent` )

**--delete-Destination** sztring meghatározza, hogy a rendszer törli-e a forrásban nem szereplő további fájlokat a célhelyről. A, a vagy a értékre állítható `true` `false` `prompt` . Ha a értékre van állítva `prompt` , a rendszer megkérdezi a felhasználót, mielőtt ütemezi a fájlok és a Blobok törlését. (alapértelmezett `false` ). (alapértelmezett `false` )

**– kizárás – attribútumok** karakterlánca (csak Windows) kizárja azokat a fájlokat, amelyek attribútumai megegyeznek az attribútumok listájával. Például: `A;S;R`

**--kizárás – a Path** karakterlánc kizárja ezeket az elérési utakat, amikor a forrást a célhelyen hasonlítja össze. Ez a beállítás nem támogatja a helyettesítő karaktereket (*). Ellenőrzi a relatív elérési út előtagját (például: `myFolder;myFolder/subDirName/file.pdf` ).

**--kizárás-Pattern** karakterlánc zárja ki azokat a fájlokat, amelyeknek a neve megegyezik a minta listával. Például: `*.jpg;*.pdf;exactName`

**– Súgó**    a szinkronizáláshoz.

**--include-attributes** sztring (csak Windows) csak azokat a fájlokat tartalmazza, amelyek attribútumai megegyeznek az attribútumok listájával. Például: `A;S;R`

**--include-Pattern** sztring csak olyan fájlokat tartalmazhat, amelyekben a név megegyezik a minta listával. Például: `*.jpg;*.pdf;exactName`

**– a naplózási szintű** karakterlánc meghatározza a naplófájl részletességét, a rendelkezésre álló szinteket: `INFO` (az összes kérelem és válasz), `WARNING` (lassú válasz), `ERROR` (csak sikertelen kérelmek) és `NONE` (nincs kimeneti napló). (alapértelmezett `INFO` ). 

**--az SMB-info megőrzése**   Alapértelmezés szerint hamis. A megőrzi az SMB-tulajdonságok adatait (az utolsó írási időt, a létrehozási időt, az attribútum-biteket) az SMB-kompatibilis erőforrások (Windows és Azure Files) között. Ez a jelző mind a fájlokra, mind a mappákra vonatkozik, kivéve, ha meg van adva egy csak fájlhoz tartozó szűrő (például: include-Pattern). A mappákhoz továbbított adatok ugyanazok, mint a fájlok esetében, kivéve az utolsó írási időt, amelyet a mappák nem őrzik meg.

**--megőrzése-SMB-engedélyek**   Alapértelmezés szerint hamis. Az SMB ACL-ek megőrzése az adatforrások (Windows és Azure Files) között. Ez a jelző a fájlokra és a mappákra is vonatkozik, kivéve, ha meg van adva egy csak fájl szűrő (például `include-pattern` ).

**--put-MD5**     Hozzon létre egy MD5-kivonatot minden fájlhoz, és mentse a kivonatot a cél blob vagy fájl tartalom-MD5 tulajdonságának megfelelően. (Alapértelmezés szerint a rendszer nem hozza létre a kivonatot.) Csak feltöltéskor érhető el.

**– rekurzív** `True` Alapértelmezés szerint a címtárak közötti szinkronizáláskor a rendszer rekurzív módon vizsgálja az alkönyvtárakat.     (alapértelmezett `True` ). 

**--S2S-megőrzés – hozzáférési réteg**  A hozzáférési szintek megőrzése a szolgáltatás és a szolgáltatás közötti másolás során. Tekintse meg az [Azure Blob Storage: gyakori, ritka elérésű és archív hozzáférési rétegeket](../blobs/storage-blob-storage-tiers.md) , hogy a cél Storage-fiók támogassa a hozzáférési szint beállítását. Abban az esetben, ha a hozzáférési szintet nem támogatja, használja a s2sPreserveAccessTier = false kapcsolót a hozzáférési szintek másolásának mellőzéséhez. (alapértelmezett `true` ). 

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--Cap-Mbps UInt32|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|
|--megbízható-Microsoft-utótagok karakterlánca   |További tartomány-utótagokat határoz meg, amelyekben Azure Active Directory bejelentkezési tokenek küldhetők.  Az alapértelmezett érték: "*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Az itt felsorolt beállítások az alapértelmezett értékre kerülnek. A biztonság érdekében itt csak Microsoft Azure-tartományokat helyezhet el. Több bejegyzést pontosvesszővel kell elválasztani.|

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
