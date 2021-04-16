---
title: azcopy copy| Microsoft Docs
description: Ez a cikk az azcopy copy parancs referenciainformációit biztosítja.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 03/08/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7c1e265f473c1c6fb70fd97416722e7b863c429b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503558"
---
# <a name="azcopy-copy"></a>azcopy copy

Átmásolja a forrásadatokat egy célhelyre.

## <a name="synopsis"></a>Áttekintés

Átmásolja a forrásadatokat egy célhelyre. A támogatott irányok a következőek:

  - helyi < Azure-> (SAS- vagy OAuth-hitelesítés)
  - helyi <-> Azure Files (Share/Directory SAS-hitelesítés)
  - helyi < Gen 2 > Azure Data Lake Storage (SAS, OAuth vagy megosztott kulcsos hitelesítés)
  - Azure Blob (SAS vagy nyilvános) – azure> (SAS- vagy OAuth-hitelesítés)
  - Azure Blob (SAS vagy nyilvános) -> Azure Files (SAS)
  - Azure Files (SAS) -> Azure Files (SAS)
  - Azure Files (SAS) –> Azure Blob (SAS- vagy OAuth-hitelesítés)
  - Amazon Web Services (AWS) S3 (hozzáférési kulcs) -> Azure Block Blob (SAS- vagy OAuth-hitelesítés)
  - Google Cloud Storage (szolgáltatásfiókkulcs) – > Azure Block Blob (SAS- vagy OAuth-hitelesítés) [előzetes verzió]

További információért tekintse meg a cikk példákkal kapcsolatos szakaszát.

## <a name="related-conceptual-articles"></a>Kapcsolódó elméleti cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Oktatóanyag: Helyszíni adatok áttelepítése felhőalapú tárolóba az AzCopy használatával](storage-use-azcopy-migrate-on-premises-data.md)
- [Adatok átvitele az AzCopy és a Blob Storage használatával](./storage-use-azcopy-v10.md#transfer-data)
- [Adatok átvitele az AzCopy használatával és fájltárolás](storage-use-azcopy-files.md)

## <a name="advanced"></a>Felsőfokú

Az AzCopy automatikusan észleli a fájlok tartalomtípusát, amikor feltölti őket a helyi lemezről. Az AzCopy a fájlkiterjesztés vagy a tartalom alapján észleli a tartalom típusát (ha nincs megadva kiterjesztés).

A beépített keresési tábla kicsi, de Unix rendszeren a helyi rendszer fájlja(i) bővíti, ha ezek egy vagy több ilyen név alatt `mime.types` elérhetők:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

Windows rendszeren a MIME-típusokat a rendszer kinyeri a beállításjegyzékből. Ez a funkció egy jelzővel kikapcsolható. További információért tekintse meg a cikk jelölő szakaszát.

Ha a parancssor használatával környezeti változót ad meg, az olvasható lesz a parancssori előzményekben. Érdemes lehet törölni a hitelesítő adatokat tartalmazó változókat a parancssori előzményekből. Ha meg kell tartania, hogy a változók ne jelenjenek meg az előzményekben, egy szkript használatával meg kell adnia a felhasználónak a hitelesítő adatait, és be kell állítania a környezeti változót.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Példák

Töltsön fel egyetlen fájlt OAuth-hitelesítéssel. Ha még nem jelentkezett be az AzCopyba, futtassa a parancsot `azcopy login` a következő parancs futtatása előtt.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```
Ugyanaz, mint fent, de ezúttal a fájl tartalmának MD5-kivonatát is számítsa ki, és mentse a blob Content-MD5 tulajdonságaként:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Egyetlen fájl feltöltése SAS-jogkivonat használatával:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Egyetlen fájlt tölthet fel SAS-jogkivonat és -áttöltés használatával (csak blokkblobok esetén):
  
```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]
```

Töltsön fel egy teljes könyvtárat egy SAS-jogkivonat használatával:
  
```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

vagy

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive --put-md5
```

Töltsön fel egy fájlkészletet SAS-jogkivonattal és helyettesítő karakterekkel (*) karakterekkel:
 
```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

Fájlok és könyvtárak feltöltése SAS-jogkivonat és helyettesítő karakter (*) karakter használatával:

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

Fájlokat és könyvtárakat tölthet fel az Azure Storage-fiókba, és beállíthatja a lekérdezési sztringben kódolt címkéket a blobon. 

- A {key = "bla bla", val = "foo"} és {key = "bla bla 2", val = "bar"} címkék beállításhoz használja a következő szintaxist: `azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
    
- A kulcsok és az értékek URL-kódolásúak, és a kulcs-érték párokat és jel választja el (&)

- A blobok címkéinek beállításakor további engedélyek (nem' a címkékhez) vannak az SAS-ben, amelyek nélkül a szolgáltatás engedélyezési hibát ad vissza.

Egyetlen fájl letöltése OAuth-hitelesítéssel. Ha még nem jelentkezett be az AzCopyba, futtassa a parancsot `azcopy login` a következő parancs futtatása előtt.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

Egyetlen fájl letöltése SAS-jogkivonat használatával:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

Töltsön le egyetlen fájlt EGY SAS-jogkivonattal, majd a kimenetet egy fájlba (csak blokkblobok esetén):
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
``` 

Teljes könyvtár letöltése SAS-jogkivonat használatával:
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive
``` 

Megjegyzés a helyettesítő karakter (*) URL-címekben való használatával kapcsolatban:

Az URL-címekben csak két támogatott módon használható helyettesítő karakter. 

- Egy URL-cím utolsó perjele (/) után is használhat egyet. A helyettesítő karakter használata a könyvtárban lévő összes fájlt átmásolja közvetlenül a célhelyre anélkül, hogy alkönyvtárba helyezi őket. 

- A tároló nevében helyettesítő karakter is használható, ha az URL-cím csak egy tárolóra vonatkozik, a blobra nem. Ezzel a módszersel fájlokat szerezhet be a tárolók egy részkészletből. 

Töltse le egy könyvtár tartalmát az azt tartalmazó könyvtár másolása nélkül.
 
```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*?[SAS]" "/path/to/dir"
```

Töltsön le egy teljes tárfiókot.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --recursive
```

Töltse le a tárolók egy részkészletét egy tárfiókon belül egy helyettesítő karakter (*) használatával a tároló nevében.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --recursive
```

Egyetlen blob másolása egy másik blobba SAS-jogkivonat használatával.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Egyetlen blob másolása másik blobba SAS-jogkivonat és hitelesítési jogkivonat használatával. Sas-jogkivonatot kell használnia a forrásfiók URL-címének végén, de a célfióknak nincs szüksége erre, ha az paranccsal jelentkezik be az `azcopy login` AzCopyba. 

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

Egy blob virtuális könyvtárának másolása egy másikra SAS-jogkivonat használatával:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Sas-jogkivonat használatával másolja át az összes blobtárolót, könyvtárat és blobot a tárfiókból a másikba:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Egy hozzáférési kulcs és egy SAS-jogkivonat Blob Storage másolhat Amazon Web Services S3-as S3-as adatbázisba. Először állítsa be a és környezeti `AWS_ACCESS_KEY_ID` `AWS_SECRET_ACCESS_KEY` változót az AWS S3 forráshoz.
  
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Egy teljes könyvtár másolása Blob Storage AWS S3-ból egy hozzáférési kulcs és egy SAS-jogkivonat használatával. Először állítsa be a és környezeti `AWS_ACCESS_KEY_ID` `AWS_SECRET_ACCESS_KEY` változót az AWS S3 forráshoz.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```
    
  A [folder] helyőrzőt a https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html (mappa) helyőrzőt itt lehet jobban megérteni.

Másolja az összes Blob Storage a Amazon Web Services (AWS) szolgáltatásból egy hozzáférési kulcs és egy SAS-jogkivonat használatával. Először állítsa be a és környezeti `AWS_ACCESS_KEY_ID` `AWS_SECRET_ACCESS_KEY` változót az AWS S3 forráshoz.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Másolja az összes Blob Storage egy Amazon Web Services (AWS) régióból egy hozzáférési kulcs és egy SAS-jogkivonat használatával. Először állítsa be a és környezeti `AWS_ACCESS_KEY_ID` `AWS_SECRET_ACCESS_KEY` változót az AWS S3 forráshoz.
 
```azcopy
- azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

A gyűjtők egy részkészletét egy helyettesítő karakter (*) szimbólummal másolja ki a gyűjtő nevébe. Az előző példákhoz hasonlóan szüksége lesz egy hozzáférési kulcsra és egy SAS-jogkivonatra. Ügyeljen arra, hogy beállítsa a környezeti `AWS_ACCESS_KEY_ID` változót `AWS_SECRET_ACCESS_KEY` és az AWS S3 forráshoz.

```azcopy
- azcopy cp "https://s3.amazonaws.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Fájlok és könyvtárak átvitele az Azure Storage-fiókba, és a megadott lekérdezési sztring kódolású címkék beállítása a blobon. 

- A {key = "bla bla", val = "foo"} és {key = "bla bla 2", val = "bar"} címkék beállításhoz használja a következő szintaxist: `azcopy cp "https://[account].blob.core.windows.net/[source_container]/[path/to/directory]?[SAS]" "https://[account].blob.core.windows.net/[destination_container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
        
- A kulcsok és az értékek URL-kódolásúak, és a kulcs-érték párokat és jel ('&') választja el.
    
- A blobok címkéinek beállításakor további engedélyek (nem' a címkékhez) vannak az SAS-ben, amelyek nélkül a szolgáltatás engedélyezési hibát ad vissza.

Egyetlen objektumot másolhat Blob Storage Google Cloud Storage-ból egy szolgáltatásfiókkulcs és egy SAS-jogkivonat használatával. Először állítsa be a környezeti változót GOOGLE_APPLICATION_CREDENTIALS Google Cloud Storage-forráshoz.
  
```azcopy
azcopy cp "https://storage.cloud.google.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

A Google Cloud Storage-Blob Storage egy szolgáltatásfiókkulcs és egy SAS-jogkivonat használatával másolhatja át a teljes könyvtárat a Google Cloud Storage-ból. Először állítsa be a környezeti változót GOOGLE_APPLICATION_CREDENTIALS Google Cloud Storage-forráshoz.
 
```azcopy
  - azcopy cp "https://storage.cloud.google.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Egy teljes tároló másolása Blob Storage Google Cloud Storage-ból egy szolgáltatásfiókkulcs és egy SAS-jogkivonat használatával. Először állítsa be a környezeti változót GOOGLE_APPLICATION_CREDENTIALS Google Cloud Storage-forráshoz.

```azcopy 
azcopy cp "https://storage.cloud.google.com/[bucket]" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

Másolja az összes gyűjtőt a Blob Storage Google Cloud Storage-ból egy szolgáltatásfiókkulcs és egy SAS-jogkivonat használatával. Először állítsa be a környezeti változókat GOOGLE_APPLICATION_CREDENTIALS és GOOGLE_CLOUD_PROJECT=<projektazonosítót> GCS-forráshoz

```azcopy
  - azcopy cp "https://storage.cloud.google.com/" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

Másolja a gyűjtők egy részkészletét egy (*) helyettesítő karakterrel a Google Cloud Storage tárolónevében egy szolgáltatásfiók-kulcs és egy SAS-jogkivonat használatával a célhelyhez. Először állítsa be a környezeti változókat GOOGLE_APPLICATION_CREDENTIALS és GOOGLE_CLOUD_PROJECT=<a Google Cloud Storage-> projektazonosítóját.
 
```azcopy
azcopy cp "https://storage.cloud.google.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

## <a name="options"></a>Beállítások

**--backup** Aktiválja a Windows SeBackupPrivilege jogosultságát a feltöltéshez, vagy a SeRestorePrivilege-et a letöltéshez, hogy az AzCopy a fájlrendszer engedélyétől függetlenül minden fájlt lát és olvasson, valamint hogy visszaállítsa az összes engedélyt. Ehhez az AzCopyt futtató fióknak már rendelkeznie kell ezekkel az engedélyekkel (például rendszergazdai jogosultságokkal rendelkezik, vagy tagja a `Backup Operators` csoportnak). Ez a jelző aktiválja a fiókhoz már rendelkező jogosultságokat.

**--blob-tags sztring** Címkék beállítása blobok számára a tárfiókban lévő adatok kategorizálásához.

**--blob-type** string A célhelyen lévő blob típusát határozza meg. Ez blobok feltöltését és fiókok közötti másolást (alapértelmezett) `Detect` használja. Az érvényes értékek a `Detect` következők: `BlockBlob` , , `PageBlob` és `AppendBlob` . Fiókok közötti másoláskor a értéke miatt az AzCopy a forrásblob típusának használatával határozza meg a `Detect` célblob típusát. Fájl feltöltésekor a fájl kiterjesztése alapján megállapítja, hogy a fájl `Detect` VHD- vagy VHDX-fájl-e. Ha a fájl egy VHD- vagy VHDX-fájl éter, az AzCopy lapblobként kezeli a fájlt. (alapértelmezett "Észlelés")

**--block-blobszintű sztring** Blokkblob feltöltése az Azure Storage-ba ezzel a blobszinttel. (alapértelmezett "Nincs")

**--block-size-mb** float Ezt a blokkméretet használja (amely a MiB-ban van megadva) az Azure Storage-ba való feltöltéskor és az Azure Storage-ból való letöltéskor. Az alapértelmezett értéket a rendszer automatikusan kiszámítja a fájlméret alapján. A tizedes törtek engedélyezettek (például: 0,25).

**--cache-control sztring** Állítsa be a cache-control fejlécet. A visszaadott érték letöltés után.

**--check-length** Ellenőrizze a fájl hosszát a célhelyen az átvitel után. Ha eltérés van a forrás és a cél között, az átvitel sikertelenként lesz megjelölve. (az alapértelmezett érték `true` )

**--check-md5 sztring** – Azt határozza meg, hogy a rendszer milyen szigorúan ellenőrizze az MD5-hasheket a letöltéskor. Csak letöltéskor érhető el. Elérhető lehetőségek: `NoCheck` , `LogOnly` , , `FailIfDifferent` `FailIfDifferentOrMissing` . (alapértelmezett `FailIfDifferent` ) (alapértelmezett "FailIfDifferent")

**--content-disposition sztring** Állítsa be a content-disposition fejlécet. A visszaadott érték letöltés után.

**--content-encoding string** Állítsa be a content-encoding fejlécet. A visszaadott érték letöltés után.

**--content-language sztring** Állítsa be a content-language fejlécet. A visszaadott érték letöltés után.

**--content-type string** – A fájl tartalomtípusát határozza meg. Ez a no-guess-mime-type típusra utal. A visszaadott érték letöltés után.

**--decompress** Fájlok automatikus kibontása letöltéskor, ha a tartalomkódolásuk azt jelzi, hogy tömörítve vannak. A támogatott tartalomkódolási értékek a `gzip` és `deflate` a . A vagy a fájlkiterjesztése `.gz` / `.gzip` `.zz` nem szükséges, de a rendszer eltávolítja, ha van ilyen.

**--exclude-attributes sztring** (csak Windows esetén) Kizárja azokat a fájlokat, amelyek attribútumai megegyeznek az attribútumlistával. Például: A; S; R

**--exclude-blob-type** string Opcionálisan megadja a blob típusát ( ) a blobok tárolóból vagy fiókból `BlockBlob` /  `PageBlob` /  `AppendBlob` való másolásakor. Ennek a jelzőnek a használata nem alkalmazható az adatok nem Azure-szolgáltatásból szolgáltatásba való másolására. Egynél több blobot kell elválasztani `;` egymástól. 

**--exclude-path sztring** Zárja ki ezeket az elérési utakat másoláskor. Ez a beállítás nem támogatja a helyettesítő karaktereket (*). Ellenőrzi a relatív elérési út előtagját (például: `myFolder;myFolder/subDirName/file.pdf` ). A fiókbejárással együtt használva az elérési utak nem tartalmazzák a tároló nevét.

**--exclude-pattern sztring** – Másoláskor zárja ki ezeket a fájlokat. Ez a beállítás támogatja a helyettesítő karaktereket (*).

**--follow-symlinks**  Kövesse a szimbolikus hivatkozásokat a helyi fájlrendszerből való feltöltéskor.

**--force-if-read-only** Ha windowsos vagy Azure Files meglévő fájlt ír felül, kényszerítenie kell a felülírás megfelelő beállítását, még akkor is, ha a meglévő fájl írási attribútuma be van állítva.

**A --from-to-string** opcionálisan megadja a forrás célhely kombinációját. Például: `LocalBlob` , `BlobLocal` , `LocalBlobFS` .

**--help**  help for copy.

**--include-after sztring** Csak a megadott dátumon/időpont után módosított fájlokat foglalja bele. Az értéknek ISO8601 formátumúnak kell lennie. Ha nincs megadva időzóna, a rendszer feltételezi, hogy az érték az AzCopyt futtató gép helyi időzónában van. például UTC időzóna szerint, vagy éjfél `2020-08-19T15:04:00Z` `2020-08-19` (00:00) időpontban a helyi időzónában. Az AzCopy 10.5-ként ez a jelző csak a fájlokra vonatkozik, a mappákra nem, így a mappatulajdonságok nem lesznek átmásolva, ha ezt a jelzőt a vagy a jelölővel `--preserve-smb-info` `--preserve-smb-permissions` használja.

 **--include-before** sztring Csak a megadott dátum/idő előtt vagy időpontban módosított fájlokat foglalja bele. Az értéknek ISO8601 formátumúnak kell lennie. Ha nincs megadva időzóna, a rendszer feltételezi, hogy az érték az AzCopyt futtató gép helyi időzónában van. Például `2020-08-19T15:04:00Z` időpontban (UTC) vagy `2020-08-19` éjfél (00:00) időpontban a helyi időzónában. Az AzCopy 10.7-től ez a jelző csak a fájlokra vonatkozik, a mappákra nem, így a mappatulajdonságok nem lesznek átmásolva, ha ezt a jelzőt a vagy a jelölővel `--preserve-smb-info` `--preserve-smb-permissions` használja.

**--include-attributes sztring** (csak Windows esetén) Olyan fájlokat foglal magába, amelyek attribútumai megegyeznek az attribútumlistával. Például: A; S; R

**--include-path sztring** Csak ezeket az elérési utakat foglalja bele másoláskor. Ez a beállítás nem támogatja a helyettesítő karaktereket (*). Ellenőrzi a relatív elérési út előtagját (például: `myFolder;myFolder/subDirName/file.pdf` ).

**--include-pattern sztring** Csak ezeket a fájlokat foglalja bele másoláskor. Ez a beállítás támogatja a helyettesítő karaktereket (*). Válassza el a fájlokat egy `;` használatával.

**--list-of-versions sztring** – Egy olyan fájlt ad meg, amelyben az egyes verzióazonosítók külön sorban vannak felsorolva. Győződjön meg arról, hogy a forrásnak egyetlen blobra kell mutasson, és a jelölőt használó fájlban megadott verziószámok csak a forrásblobhoz tartoznak. Az AzCopy letölti a megadott verziókat a megadott célmappába. További információ: Blob korábbi [verzióinak letöltése.](./storage-use-azcopy-v10.md#transfer-data)

**--log-level** string Adja meg a naplófájl részletes naplóját, az elérhető szinteket: INFO(minden kérés/válasz), WARNING(lassú válaszok), ERROR(csak sikertelen kérések) és NONE(no output logs). `INFO`(alapértelmezett). 

**--metadata sztring** Feltöltés az Azure Storage-ba ezekkel a kulcs-érték párokkal metaadatként.

**--no-guess-mime-type**  Megakadályozza, hogy az AzCopy a fájl kiterjesztése vagy tartalma alapján észlelje a tartalomtípust.

**--overwrite** string Overwrite the conflicting files and blobs at the destination if this flag is set to true. (alapértelmezett `true` ) A lehetséges értékek a `true` következők: `false` , , `prompt` és `ifSourceNewer` . A mappákat támogató célhely esetén az ütköző mappaszintű tulajdonságok felülírják ezt a jelzőt, vagy pozitív választ ad `true` a parancssornak. (alapértelmezett "true")

**--page-blob-tier string** Lapblob feltöltése az Azure Storage-ba ezzel a blobszinttel. `None`(alapértelmezett). (alapértelmezett "Nincs")

**--preserve-last-modified-time**  Csak akkor érhető el, ha a cél a fájlrendszer.

**--preserve-owner**    Csak a letöltésre van hatással, és csak akkor, ha `--preserve-smb-permissions` használja. Ha igaz (ez az alapértelmezett beállítás), a letöltések megőrzik a fájl tulajdonosát és csoportját. Ha false (hamis) értéket ad meg, a továbbra is megőrzi az ACL-eket, de a Tulajdonos és a Csoport az AzCopyt futtató felhasználón alapul `--preserve-smb-permissions` (alapértelmezés szerint igaz)

**--preserve-smb-info**   Alapértelmezés szerint hamis. Megőrzi az SMB-tulajdonságinformációkat (utolsó írási idő, létrehozási idő, attribútum-bitek) az SMB-t tisztában álló erőforrások (Windows és Azure Files). A rendszer csak a Azure Files által támogatott attribútum-biteket továbbítja; A többit a rendszer figyelmen kívül hagyja. Ez a jelző fájlokra és mappákra is vonatkozik, kivéve, ha csak fájlra vonatkozó szűrő van megadva (például include-pattern). A mappákra átvitt adatok ugyanazok, mint a fájlok adatai, kivéve az Utolsó írási időt, amely soha nem marad meg a mappákban.

**--preserve-smb-permissions**   Alapértelmezés szerint hamis. Megőrzi az SMB ACL-eket a tudó erőforrások (Windows és Azure Files). Letöltések esetén a jelzőre is szükség lesz az engedélyek visszaállításához, ahol az új tulajdonos nem az `--backup` AzCopyt futtató felhasználó lesz. Ez a jelző fájlokra és mappákra is vonatkozik, kivéve, ha csak fájlszűrő van megadva (például `include-pattern` ).

**--put-md5**    Hozzon létre egy MD5-kivonatot minden fájlból, és mentse a kivonatot a célblob vagy -fájl Content-MD5 tulajdonságaként. (Alapértelmezés szerint a kivonat NEM jön létre.) Csak feltöltéskor érhető el.

**--rekurzív**    A helyi fájlrendszerből való feltöltéskor rekurzív módon keresse meg az alkönyvtárakat.

**--s2s-detect-source-changed**   Annak észlelése, hogy a forrásfájl/blob módosul-e olvasás közben. (Ez a paraméter csak a szolgáltatások között másolt példányra vonatkozik, mert a megfelelő ellenőrzés véglegesen engedélyezve van a feltöltések és letöltések esetén.)

**--s2s-handle-invalid-metadata** sztring – Meghatározza az érvénytelen metaadatkulcsok kezelését. Elérhető beállítások: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. `ExcludeIfInvalid`(alapértelmezett). (alapértelmezett "ExcludeIfInvalid")

**--s2s-preserve-access-tier**   A hozzáférési szint megőrzése a szolgáltatások és szolgáltatások másolása során. Tekintse meg [az Azure Blob Storage: a forró,](../blobs/storage-blob-storage-tiers.md) a elérésű és az archív hozzáférési szinteket annak biztosításához, hogy a cél tárfiók támogatja-e a hozzáférési szint beállítását. Abban az esetben, ha a hozzáférési szint beállítása nem támogatott, használja az s2sPreserveAccessTier=false paramétert a hozzáférési szint másolásának megkerülése érdekében. `true`(alapértelmezett).  (alapértelmezett "true")

**--s2s-preserve-properties**   A szolgáltatások és szolgáltatások másolása során őrizze meg a teljes tulajdonságokat. Az AWS S3 és az Azure File nem egy fájlforrás esetén a listaművelet nem adja vissza az objektumok és fájlok teljes tulajdonságait. A teljes tulajdonságok megőrzéséhez az AzCopynak objektumonként vagy fájlonként egy további kérést kell küldenie. (alapértelmezett igaz)

## <a name="options-inherited-from-parent-commands"></a>A szülőparancsok által örökölt beállítások

**--cap-mbps lebegőpontos érték**   Megabit/másodpercben megszabja az átviteli sebességet. A pillanatnyi átviteli sebesség kis mértékben eltérhet a felső felsőértéktől. Ha ez a beállítás nulla vagy nincs megadva, az átviteli sebesség nincs korlátozva.

**--output-type** string A parancs kimenetének formátuma. A lehetőségek a következők: szöveg, json. Az alapértelmezett érték `text`. (alapértelmezett "szöveg")

**--trusted-microsoft-suffixes** sztring További tartomány-utótagokat ad meg, Azure Active Directory bejelentkezési jogkivonatokat lehet küldeni.  A mező alapértelmezett értéke: `*.core.windows.net;*.core.chinacloudapi.cn;*.core.cloudapi.de;*.core.usgovcloudapi.net`. Az itt felsoroltak az alapértelmezett értékhez kerülnek. A biztonság érdekében itt csak a Microsoft Azure helyezzen el. Több bejegyzést pontosvesszővel válassza el egymástól.

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
