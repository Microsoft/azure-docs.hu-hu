---
title: azcopy sync | Microsoft Docs
description: Ez a cikk az azcopy sync parancs referenciainformációit biztosítja.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 83d10a7a6e9eb14379d32cc88800a2c443feac60
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503048"
---
# <a name="azcopy-sync"></a>azcopy sync

Replikálja a forráshelyet a célhelyre. Ez a cikk az azcopy sync parancs részletes referenciáját biztosítja. A blobok forrás- és célhelyek közötti szinkronizálásával kapcsolatos további információkért lásd: Szinkronizálás az Azure Blob Storage-ral az [AzCopy 10-es](storage-use-azcopy-blobs-synchronize.md)használatával. További Azure Files fájlok [szinkronizálása.](storage-use-azcopy-files.md#synchronize-files)

## <a name="synopsis"></a>Áttekintés

Az utolsó módosítás időpontjai összehasonlításra használhatók. A rendszer kihagyja a fájlt, ha a legutóbbi módosítás időpontja a célhelyen újabb.

A támogatott párok a következőek:

- helyi < Azure-> (SAS- vagy OAuth-hitelesítés is használható)
- Azure Blob <-> Azure Blob (a forrásnak tartalmaznia kell egy SAS-t vagy nyilvánosan elérhető; célhelyként SAS- vagy OAuth-hitelesítés használható)
- Azure File < Azure-> (a forrásnak tartalmaznia kell egy SAS-t vagy nyilvánosan elérhetőnek kell lennie; A célhelyhez SAS-hitelesítést kell használni)

A szinkronizálási parancs többféleképpen különbözik a másolási parancstól:

1. Alapértelmezés szerint a rekurzív jelző true (igaz) érték, és a sync minden alkönyvtárat átmásol. A Sync csak akkor másolja a legfelső szintű fájlokat egy könyvtárba, ha a rekurzív jelző hamis.
2. A virtuális könyvtárak közötti szinkronizáláskor adjon hozzá egy záró perjelet az elérési úthoz (tekintse meg a példákat), ha van olyan blob, amely neve megegyezik a virtuális könyvtárak egyikével.
3. Ha a jelző true (igaz) vagy prompt (parancssor) értékre van állítva, akkor a sync törli azokat a fájlokat és blobokat a célhelyen, amelyek nincsenek jelen a `deleteDestination` forrásnál.

## <a name="related-conceptual-articles"></a>Kapcsolódó elméleti cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Oktatóanyag: Helyszíni adatok áttelepítése felhőalapú tárolóba az AzCopy használatával](storage-use-azcopy-migrate-on-premises-data.md)
- [Adatok átvitele az AzCopy és a Blob Storage használatával](./storage-use-azcopy-v10.md#transfer-data)
- [Adatok átvitele az AzCopy használatával és fájltárolás](storage-use-azcopy-files.md)

### <a name="advanced"></a>Felsőfokú

Ha nem ad meg fájlkiterjesztést, az AzCopy a helyi lemezről való feltöltéskor automatikusan észleli a fájlok tartalomtípusát a fájlkiterjesztés vagy a tartalom alapján (ha nincs megadva kiterjesztés).

A beépített keresési tábla kicsi, de Unix rendszeren kiegészítve van a helyi rendszer mime.types fájlja(ival), ha elérhető egy vagy több ilyen név alatt:

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

Ugyanaz, mint fent, de kiszámítja a fájl tartalmának MD5-kivonatát is, majd menti az MD5-kivonatot a blob Content-MD5 tulajdonságaként. 

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Szinkronizálja a teljes könyvtárat az alkönyvtáraival együtt (vegye figyelembe, hogy a rekurzív alapértelmezés szerint be van kapcsolva):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

vagy

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Csak a könyvtáron belüli fájlokat szinkronizálja, az alkönyvtárakat és az alkönyvtárakon belüli fájlokat nem:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Fájlok egy részkészletének szinkronizálása egy könyvtárban (például csak jpg- és pdf-fájlok esetén, vagy ha a fájlnév `exactName` ):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include-pattern="*.jpg;*.pdf;exactName"
```

Szinkronizálhat egy teljes könyvtárat, de kizárhat bizonyos fájlokat a hatókörből (például: minden fájl, amely a foo előnévvel kezdődik vagy egy sávgal végződik):

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

Szinkronizáljon egy blob nevével azonos nevű virtuális könyvtárat (a kétértelműség érdekében adjon hozzá egy záró perjelet az elérési úthoz):

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Azure File-könyvtár szinkronizálása (ugyanaz a szintaxis, mint a Blob):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Ha a rendszer együtt használja az include/exclude jelzőket, a rendszer csak a include mintának megfelelő fájlokat veszi figyelembe, de a kizárási mintákkal egyező fájlokat a rendszer mindig figyelmen kívül hagyja.

## <a name="options"></a>Beállítások

**--block-size-mb** float Ezt a blokkméretet használja (a MiB-ban megadva) az Azure Storage-ba való feltöltéskor vagy az Azure Storage-ból való letöltéskor. A rendszer automatikusan kiszámítja az alapértelmezett értéket a fájlméret alapján. Tizedes törtek engedélyezettek (például: `0.25` ).

**--check-md5 sztring** – Azt határozza meg, hogy a rendszer hogyan ellenőrizze szigorúan az MD5-hasheket a letöltéskor. Ez a lehetőség csak letöltéskor érhető el. Az elérhető értékek a következők: `NoCheck` `LogOnly` , , , `FailIfDifferent` `FailIfDifferentOrMissing` . `FailIfDifferent`(alapértelmezett). (alapértelmezett `FailIfDifferent` )

**--delete-destination** string Meghatározza, hogy töröljön-e további fájlokat a célhelyről, amelyek nincsenek jelen a forrásban. A beállítása `true` lehet , `false` vagy `prompt` . Ha a beállítása , a felhasználónak kérdést kell válaszolnia a fájlok és blobok törlésre `prompt` való ütemezése előtt. `false`(alapértelmezett). (alapértelmezett `false` )

**--exclude-attributes sztring** (csak Windows esetén) Kizárja azokat a fájlokat, amelyek attribútumai megegyeznek az attribútumlistával. Például: `A;S;R`

**--exclude-path sztring** Zárja ki ezeket az elérési utakat, amikor összehasonlítja a forrást a célhelyen. Ez a beállítás nem támogatja a helyettesítő karaktereket (*). Ellenőrzi a relatív elérési út előtagját (például: `myFolder;myFolder/subDirName/file.pdf` ).

**--exclude-pattern sztring** Azon fájlok kizárása, amelyek neve megegyezik a mintalistával. Például: `*.jpg;*.pdf;exactName`

**--help**    help for sync.

**--include-attributes sztring** (csak Windows esetén) Csak azokat a fájlokat tartalmazza, amelyek attribútumai megegyeznek az attribútumlistával. Például: `A;S;R`

**--include-pattern sztring** Csak azokat a fájlokat foglalja bele, amelyek neve megegyezik a mintalistával. Például: `*.jpg;*.pdf;exactName`

**--log-level** string Adja meg a napló részletességét a naplófájlhoz, az elérhető szinteket: (minden kérés és `INFO` válasz), (lassú válaszok), (csak sikertelen kérések) és (nincsenek kimeneti `WARNING` `ERROR` `NONE` naplók). `INFO`(alapértelmezett). 

**--preserve-smb-info**   Alapértelmezés szerint hamis. Megőrzi az SMB-tulajdonságinformációkat (utolsó írási idő, létrehozási idő, attribútum-bitek) az SMB-t tisztában álló erőforrások (Windows és Azure Files). Ez a jelző fájlokra és mappákra is vonatkozik, kivéve, ha csak fájlra vonatkozó szűrő van megadva (például include-pattern). A mappákra átvitt adatok ugyanazok, mint a fájlok adatai, kivéve a mappák utolsó írási idejét.

**--preserve-smb-permissions**   Alapértelmezés szerint hamis. Megőrzi az SMB ACL-eket a tudó erőforrások (Windows és Azure Files). Ez a jelző fájlokra és mappákra is vonatkozik, kivéve, ha csak fájlszűrő van megadva (például `include-pattern` ).

**--put-md5**     Hozzon létre egy MD5-kivonatot minden fájlból, és mentse a kivonatot a célblob vagy -fájl Content-MD5 tulajdonságaként. (Alapértelmezés szerint a kivonat NEM jön létre.) Csak feltöltéskor érhető el.

**--rekurzív** `True` alapértelmezés szerint a könyvtárak közötti szinkronizáláskor rekurzív módon keresse meg az alkönyvtárakat.     `True`(alapértelmezett). 

**--s2s-preserve-access-tier**  A hozzáférési szint megőrzése a szolgáltatások és szolgáltatások másolása során. Tekintse meg [az Azure Blob Storage: a forró,](../blobs/storage-blob-storage-tiers.md) a elérésű és az archív hozzáférési szinteket annak biztosításához, hogy a cél tárfiók támogatja-e a hozzáférési szint beállítását. Abban az esetben, ha a hozzáférési szint beállítása nem támogatott, használja az s2sPreserveAccessTier=false értéket a hozzáférési szint másolásának megkerülése érdekében. `true`(alapértelmezett). 

## <a name="options-inherited-from-parent-commands"></a>A szülőparancsok által örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--cap-mbps uint32|Megabit/másodpercben megszabja az átviteli sebességet. A pillanatnyi átviteli sebesség kis mértékben eltérhet a felső felsőértéktől. Ha ez a beállítás nulla vagy nincs megadva, az átviteli sebesség nincs korlátozva.|
|--output-type string|A parancs kimenetének formátuma. A lehetőségek a következők: text, json. Az alapértelmezett érték a "text".|
|--trusted-microsoft-suffixes sztring   |További tartomány-utótagokat ad meg, Azure Active Directory bejelentkezési jogkivonatokat lehet küldeni.  Az alapértelmezett érték a '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Az itt felsoroltak hozzáadva az alapértelmezetthez. A biztonság érdekében itt csak a Microsoft Azure helyezzen el. Több bejegyzést pontosvesszővel válassza el egymástól.|

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
