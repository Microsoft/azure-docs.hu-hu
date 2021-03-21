---
title: azcopy másolás | Microsoft Docs
description: Ez a cikk a azcopy másolási parancsra vonatkozó tudnivalókat tartalmazza.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 03/08/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c676b92fd07c6e444aa22f25c48fdb1b1957ca7a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493764"
---
# <a name="azcopy-copy"></a>azcopy copy

A forrásadatok másolása a célhelyre.

## <a name="synopsis"></a>Áttekintés

A forrásadatok másolása a célhelyre. A támogatott utasítások a következők:

  - helyi < – > Azure Blob (SAS vagy OAuth Authentication)
  - helyi <-> Azure Files (megosztás/könyvtár SAS-hitelesítése)
  - helyi <-> Azure Data Lake Storage Gen 2 (SAS, OAuth vagy megosztott kulcsos hitelesítés)
  - Azure Blob (SAS vagy Public) – > Azure Blob (SAS vagy OAuth Authentication)
  - Azure Blob (SAS vagy Public) – > Azure Files (SAS)
  - Azure Files (SAS) – > Azure Files (SAS)
  - Azure Files (SAS) – > Azure-Blob (SAS vagy OAuth-hitelesítés)
  - Amazon Web Services (AWS) S3 (hozzáférési kulcs) – > Azure Block blob (SAS vagy OAuth Authentication)
  - Google Cloud Storage (szolgáltatásfiók kulcsa) – > Azure Block blob (SAS vagy OAuth Authentication) [előzetes verzió]

További információkért tekintse meg a jelen cikk példák című szakaszát.

## <a name="related-conceptual-articles"></a>Kapcsolódó fogalmi cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele a AzCopy és a blob Storage szolgáltatással](./storage-use-azcopy-v10.md#transfer-data)
- [Adatok átvitele az AzCopy használatával és fájltárolás](storage-use-azcopy-files.md)
- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)

## <a name="advanced"></a>Felsőfokú

A AzCopy automatikusan észleli a fájlok tartalmának típusát, amikor feltölti őket a helyi lemezről. A AzCopy a fájlkiterjesztés vagy a tartalom alapján észleli a tartalomtípust (ha nincs megadva kiterjesztés).

A beépített keresési táblázat kicsi, de UNIX rendszeren a helyi rendszer fájlja (ke) t is kibővíti, `mime.types` Ha ezek egy vagy több név alatt érhetők el:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

Windows rendszeren a MIME-típusokat a rendszer kinyeri a beállításjegyzékből. Ez a funkció egy jelző segítségével kapcsolható ki. További információkért tekintse meg a jelen cikk jelző szakaszát.

Ha egy környezeti változót a parancssor használatával állít be, akkor ez a változó a parancssori előzményekben olvasható. Érdemes lehet a parancssori előzményekből származó hitelesítő adatokat tartalmazó változókat törölni. Ahhoz, hogy a változók megjelenjenek az előzményekben, egy parancsfájl használatával megkérheti a felhasználótól a hitelesítő adataikat, és beállíthatja a környezeti változót.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Példák

Töltsön fel egyetlen fájlt a OAuth-hitelesítés használatával. Ha még nem jelentkezett be a AzCopy-be, futtassa a `azcopy login` parancsot a következő parancs futtatása előtt.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```
Ugyanaz, mint a fentiekben, de ezúttal is kiszámítja a fájl tartalmának MD5-kivonatát, és a blob Content-MD5 tulajdonságként menti azt:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Egyetlen fájl feltöltése SAS-token használatával:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Egyetlen fájl feltöltése SAS-token és-csővezeték használatával (csak a Blobok blokkolása):
  
```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]
```

Teljes könyvtár feltöltése SAS-token használatával:
  
```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

vagy

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive --put-md5
```

Fájlok készletének feltöltése SAS-token és helyettesítő karakter (*) karakterek használatával:
 
```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

Fájlok és könyvtárak feltöltése SAS-token és helyettesítő karakter (*) karakterek használatával:

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

Töltse fel a fájlokat és könyvtárakat az Azure Storage-fiókba, és állítsa be a lekérdezés-karakterlánc kódolású címkéket a blobon. 

- A címkék {Key = "bla bla", val = "foo"} és {Key = "bla bla 2", val = "Bar"} beállításához használja a következő szintaxist: `azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
    
- A kulcsok és értékek URL-kódolású, a kulcs-érték párok pedig egy jellel ("&") vannak elválasztva.

- A címkék a blobokra való beállítása során további engedélyek (nem "a címkékhez) találhatók az SAS-ben, amelyek nélkül a szolgáltatás visszaadja az engedélyezési hibát.

Töltsön le egyetlen fájlt a OAuth-hitelesítés használatával. Ha még nem jelentkezett be a AzCopy-be, futtassa a `azcopy login` parancsot a következő parancs futtatása előtt.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

Egyetlen fájl letöltése SAS-token használatával:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

Egyetlen fájl letöltése SAS-token használatával, majd a kimenet fájlba állítása (csak a Blobok blokkolása esetén):
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
``` 

Teljes könyvtár letöltése SAS-token használatával:
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive
``` 

Megjegyzés a helyettesítő karakter (*) URL-címekben való használatáról:

Egy URL-címben csak két támogatott módon használható helyettesítő karakter. 

- Egy URL-cím utolsó perjel (/) utáni végét is használhat. A helyettesítő karakter használata a könyvtárban lévő összes fájlt közvetlenül a célhelyre másolja, anélkül, hogy azokat egy alkönyvtárba helyezzük. 

- A tároló nevében helyettesítő karaktert is használhat, feltéve, hogy az URL-cím csak egy tárolóra hivatkozik, és nem a blobra. Ezt a módszert használhatja a tárolók egy részhalmazában lévő fájlok beszerzésére. 

Töltse le a könyvtár tartalmát anélkül, hogy magáról a tartalmazó könyvtárat másolja.
 
```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*?[SAS]" "/path/to/dir"
```

Töltse le a teljes Storage-fiókot.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --recursive
```

A Storage-fiókban lévő tárolók egy részhalmazát letöltheti egy helyettesítő karakter (*) használatával a tároló nevében.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --recursive
```

Egyetlen blob másolása egy másik blobba SAS-token használatával.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Egyetlen blob másolása egy másik blobba SAS-jogkivonat és egy hitelesítési jogkivonat használatával. A fiók URL-címének végén egy SAS-jogkivonatot kell használnia, de a cél fióknak nincs szüksége rá, ha a parancs használatával bejelentkezik a AzCopy `azcopy login` . 

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

Egy blob virtuális könyvtár másolása egy másikba SAS-token használatával:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Az összes blob-tároló,-könyvtár és-blob másolása a Storage-fiókból egy másikba SAS-token használatával:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Egyetlen objektum másolása Amazon Web Services (AWS) S3-Blob Storage egy hozzáférési kulccsal és egy SAS-token használatával. Először állítsa be a környezeti változót `AWS_ACCESS_KEY_ID` és `AWS_SECRET_ACCESS_KEY` az AWS S3 forrás értékét.
  
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Egy teljes könyvtár másolása az AWS S3-Blob Storage egy hozzáférési kulccsal és egy SAS-token használatával. Először állítsa be a környezeti változót `AWS_ACCESS_KEY_ID` és `AWS_SECRET_ACCESS_KEY` az AWS S3 forrás értékét.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```
    
  https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.htmlA [mappa] helyőrző jobb megismeréséhez tekintse meg a következőt:.

Másolja az összes gyűjtőt a Amazon Web Services (AWS) Blob Storage egy hozzáférési kulccsal és egy SAS-token használatával. Először állítsa be a környezeti változót `AWS_ACCESS_KEY_ID` és `AWS_SECRET_ACCESS_KEY` az AWS S3 forrás értékét.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Másolja az összes gyűjtőt egy Amazon Web Services (AWS) régióból Blob Storage egy hozzáférési kulccsal és egy SAS-token használatával. Először állítsa be a környezeti változót `AWS_ACCESS_KEY_ID` és `AWS_SECRET_ACCESS_KEY` az AWS S3 forrás értékét.
 
```azcopy
- azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Másolja a gyűjtők egy részhalmazát egy helyettesítő karakter (*) szimbólum használatával a gyűjtő nevében. Az előző példákhoz hasonlóan egy hozzáférési kulcsra és egy SAS-jogkivonatra is szüksége lesz. Ügyeljen arra, hogy a környezeti változót `AWS_ACCESS_KEY_ID` és `AWS_SECRET_ACCESS_KEY` az AWS S3 forrást adja meg.

```azcopy
- azcopy cp "https://s3.amazonaws.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Fájlok és könyvtárak átvitele az Azure Storage-fiókba, valamint a megadott lekérdezési karakterláncot tartalmazó címkék beállítása a blobon. 

- A címkék {Key = "bla bla", val = "foo"} és {Key = "bla bla 2", val = "Bar"} beállításához használja a következő szintaxist: `azcopy cp "https://[account].blob.core.windows.net/[source_container]/[path/to/directory]?[SAS]" "https://[account].blob.core.windows.net/[destination_container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
        
- A kulcsok és értékek URL-kódolású, a kulcs-érték párok pedig egy jellel ("&") vannak elválasztva.
    
- A címkék a blobokra való beállítása során további engedélyek (nem "a címkékhez) találhatók az SAS-ben, amelyek nélkül a szolgáltatás visszaadja az engedélyezési hibát.

Egyetlen objektum másolása a Google Cloud Storage-ból Blob Storage egy szolgáltatásfiók-kulcs és egy SAS-token használatával. Először állítsa be a Google Cloud Storage-forrás környezeti változóját GOOGLE_APPLICATION_CREDENTIALS.
  
```azcopy
azcopy cp "https://storage.cloud.google.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Egy teljes könyvtár másolása a Google Cloud Storage-ból Blob Storage egy szolgáltatásfiók-kulcs és egy SAS-token használatával. Először állítsa be a Google Cloud Storage-forrás környezeti változóját GOOGLE_APPLICATION_CREDENTIALS.
 
```azcopy
  - azcopy cp "https://storage.cloud.google.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Egy teljes gyűjtő másolása Blob Storage a Google Cloud Storage szolgáltatásból egy szolgáltatásfiók-kulcs és egy SAS-token használatával. Először állítsa be a Google Cloud Storage-forrás környezeti változóját GOOGLE_APPLICATION_CREDENTIALS.

```azcopy 
azcopy cp "https://storage.cloud.google.com/[bucket]" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

Másolja az összes gyűjtőt a Google Cloud Storage-ból Blob Storage egy szolgáltatásfiók-kulcs és egy SAS-token használatával. Először állítsa be a környezeti változókat GOOGLE_APPLICATION_CREDENTIALS és GOOGLE_CLOUD_PROJECT =<Project-ID> a GCS-forráshoz

```azcopy
  - azcopy cp "https://storage.cloud.google.com/" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

Másolja ki a gyűjtők egy részhalmazát egy helyettesítő karakter (*) használatával a Google Cloud Storage-ból, egy szolgáltatásfiók-kulccsal és egy, a célhelyhez tartozó SAS-token használatával. Először állítsa be a környezeti változókat GOOGLE_APPLICATION_CREDENTIALS és GOOGLE_CLOUD_PROJECT =<Project-ID> a Google Cloud Storage-forráshoz.
 
```azcopy
azcopy cp "https://storage.cloud.google.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

## <a name="options"></a>Beállítások

**– biztonsági mentés** Aktiválja a Windows SeBackupPrivilege a feltöltésekhez vagy SeRestorePrivilege a letöltésekhez, hogy lehetővé tegye a AzCopy számára az összes fájl megtekintését és olvasását, a fájlrendszer engedélyeitől függetlenül, valamint az összes engedély visszaállítását. Megköveteli, hogy a AzCopy-t futtató fiók már rendelkezik ezekkel az engedélyekkel (például rendszergazdai jogokkal rendelkezik, vagy a `Backup Operators` csoport tagja). Ez a jelző aktiválja azokat a jogosultságokat, amelyekkel a fiók már rendelkezik.

**--blob-címkék** sztring a Blobok címkéit a Storage-fiókban lévő adatkategorizáláshoz.

**--a blob-Type** karakterlánc határozza meg a blob típusát a célhelyen. Ez a Blobok feltöltésére és a fiókok közötti másolásra használatos (alapértelmezett `Detect` ). Az érvényes értékek a következők:,, `Detect` `BlockBlob` `PageBlob` és `AppendBlob` . A fiókok közötti másoláskor a `Detect` AzCopy hatására a rendszer a forrás blob típusát használja a cél blob típusának meghatározásához. Fájl feltöltésekor meghatározza, hogy `Detect` a fájl egy VHD vagy egy VHDX-fájl a fájlkiterjesztés alapján. Ha a fájl az éter VHD-vagy VHDX-fájlja, a AzCopy oldal blobként kezeli a fájlt. (alapértelmezett "észlelés")

**--Block-blob-réteg** karakterlánc feltöltési blokk blob az Azure Storage-ba ezzel a blob réteggel. (alapértelmezett "nincs")

**--Block-Size-MB** lebegőpontos használata esetén ez a blokk mérete (a MIB-ben van megadva) az Azure Storage-ba való feltöltéskor és az Azure Storage-ból való letöltéssel. Az alapértelmezett értéket a rendszer automatikusan kiszámítja a fájl mérete alapján. Tizedes törtek engedélyezettek (például: 0,25).

**--Cache-Control** sztring a Cache-Control fejlécet adja meg. A letöltés után tért vissza.

**--ellenőrzési hossz** Az átvitel után a célhelyen lévő fájl hosszának ellenőrzését. Ha eltérés van a forrás és a cél között, az átvitel sikertelenként van megjelölve. (az alapértelmezett érték a `true` )

**--ellenőrzés-MD5** karakterlánc megadja, hogy a letöltéskor milyen szigorúan kell ellenőrizni az MD5-kivonatok érvényességét. Csak a letöltéskor érhető el. Elérhető beállítások: `NoCheck` , `LogOnly` , `FailIfDifferent` , `FailIfDifferentOrMissing` . (alapértelmezett `FailIfDifferent` ) (alapértelmezett "FailIfDifferent")

**--Content-hajlam** sztring beállítja a Content-hajlam fejlécet. A letöltés után tért vissza.

**--Content-Encoding** string beállítja a Content-Encoding fejlécet. A letöltés után tért vissza.

**--Content-Language** karakterlánc a Content-Language fejlécet adja meg. A letöltés után tért vissza.

**--Content-Type** karakterlánc megadja a fájl tartalomtípusát. A nem-GUESS-MIME-típust jelenti. A letöltés után tért vissza.

**– Kibontás** Fájlok automatikus kibontása letöltéskor, ha a tartalmuk kódolása azt jelzi, hogy tömörítve vannak. A támogatott Content-Encoding értékek a következők: `gzip` és `deflate` . A `.gz` / `.gzip` vagy `.zz` nem szükséges fájlkiterjesztés, de ha van ilyen, el lesz távolítva.

**– kizárás – attribútumok** karakterlánca (csak Windows) kizárja azokat a fájlokat, amelyek attribútumai megegyeznek az attribútumok listájával. Például: A; S R

**--kizárás-blob-Type** karakterlánc opcionálisan meghatározza a Blobok `BlockBlob` /  `PageBlob` /  `AppendBlob` a tárolóból vagy a fiókból való másolása során kizárandó blob típusát (). A jelző használata nem alkalmazható az adatok nem Azure szolgáltatásból szolgáltatásba történő másolására. Több blobot is el kell különíteni egymástól `;` . 

**--kizárás – az elérési út** karakterlánca kizárja ezeket az elérési utakat másoláskor. Ez a beállítás nem támogatja a helyettesítő karaktereket (*). Ellenőrzi a relatív elérési út előtagját (például: `myFolder;myFolder/subDirName/file.pdf` ). Ha a fiókhoz való bejárással együtt használja, az elérési utak nem tartalmazzák a tároló nevét.

**--kizárás-Pattern** sztring kizárja ezeket a fájlokat másoláskor. Ez a beállítás támogatja a helyettesítő karaktereket (*).

**– követés – symlinks**  A helyi fájlrendszerből való feltöltéskor kövesse a szimbolikus hivatkozásokat.

**--Force-if-csak olvasható** Ha Windows vagy Azure Files rendszeren felülír egy meglévő fájlt, kényszerítse a felülírást a működésre, még akkor is, ha a meglévő fájl írásvédett attribútuma be van állítva.

**--from-to** karakterlánc opcionálisan megadja a forrás céljának kombinációját. Például: `LocalBlob` , `BlobLocal` , `LocalBlobFS` .

**– Súgó**  a másoláshoz.

**--include-After** karakterlánc csak azokat a fájlokat tartalmazza, amelyek a megadott dátum/idő után vagy után módosultak. Az értéknek ISO8601 formátumúnak kell lennie. Ha nincs megadva timezone, az érték a AzCopy-t futtató gép helyi időzónájában lesz feltételezve. például UTC- `2020-08-19T15:04:00Z` időre vagy `2020-08-19` éjfélre (00:00) a helyi időzónában. A AzCopy 10,5-as verzióban ez a jelző csak a fájlokra, a mappákra vonatkozik, így a mappa tulajdonságai nem lesznek átmásolva, ha ezzel a jelzővel a vagy a kapcsolót használja `--preserve-smb-info` `--preserve-smb-permissions`

 **--a-include-Before** karakterlánc csak azokat a fájlokat tartalmazza, amelyeket a megadott dátum/idő előtt vagy után módosítottak. Az értéknek ISO8601 formátumúnak kell lennie. Ha nincs megadva timezone, az érték a AzCopy-t futtató gép helyi időzónájában lesz feltételezve. Például `2020-08-19T15:04:00Z` UTC-időre vagy `2020-08-19` éjfélre (00:00) a helyi időzónában. A AzCopy 10,7-es verziójától kezdve ez a jelző csak a fájlokra, a mappákra vonatkozik, így a mappa tulajdonságai nem lesznek átmásolva, ha a jelzőt a vagy a paranccsal használja `--preserve-smb-info` `--preserve-smb-permissions` .

**--include-attributes** sztring (csak Windows) olyan fájlokat tartalmaz, amelyek attribútumai megegyeznek az attribútumok listájával. Például: A; S R

a- **-include-Path** sztring csak a másoláskor tartalmazza ezeket az elérési utakat. Ez a beállítás nem támogatja a helyettesítő karaktereket (*). Ellenőrzi a relatív elérési út előtagját (például: `myFolder;myFolder/subDirName/file.pdf` ).

**--include-Pattern** sztring csak a másoláskor tartalmazza ezeket a fájlokat. Ez a beállítás támogatja a helyettesítő karaktereket (*). Külön fájlok a használatával `;` .

a **--Versions** karakterlánc egy olyan fájlt ad meg, amelyben minden verzióazonosító külön sorban szerepel. Győződjön meg arról, hogy a forrásnak egyetlen blobra kell mutatnia, és a fájlban megadott összes verzióazonosító csak a forrás blobhoz tartozhat. A AzCopy letölti a megadott verziókat a megadott célmappában. További információ: [a blob korábbi verzióinak letöltése](./storage-use-azcopy-v10.md#transfer-data).

**--a log szintű** karakterlánc határozza meg a naplófájl részletességét, a rendelkezésre álló szinteket: info (minden kérelem/válasz), figyelmeztetés (lassú válasz), hiba (csak sikertelen kérések), és nincs (nincs kimeneti napló). (alapértelmezett `INFO` ). 

**--a metaadatok** karakterláncának feltöltése az Azure Storage-ba ezekkel a kulcs-érték párokkal.

**--nem-GUESS-MIME-típus**  Megakadályozza, hogy a AzCopy a fájl kiterjesztése vagy tartalma alapján észlelje a tartalom típusát.

**– a karakterlánc felülírása** felülírja az ütköző fájlokat és blobokat a célhelyen, ha a jelző értéke TRUE (igaz). (alapértelmezett `true` ) A lehetséges értékek a következők:,, `true` `false` `prompt` és `ifSourceNewer` . A mappákat támogató célhelyek esetében az ütköző mappa szintű tulajdonságok felülírják ezt a jelzőt, `true` vagy ha pozitív választ adnak a kérésnek. (alapértelmezett érték: "true")

**--Page-blob-rétegbeli** karakterlánc feltöltése oldal blob az Azure Storage-ba ezen blob-réteg használatával. (alapértelmezett `None` ). (alapértelmezett "nincs")

**--megőrzés-utolsó módosítás időpontja**  Csak akkor érhető el, ha a cél fájlrendszer.

**--megőrzés – tulajdonos**    Csak a letöltésre van hatással, és csak akkor, amikor `--preserve-smb-permissions` a használatban van. Ha az értéke TRUE (alapértelmezett), a fájl tulajdonosa és a csoport letöltése megmarad. Ha hamis értékre van állítva, a továbbra is `--preserve-smb-permissions` megőrizheti az ACL-eket, de a tulajdonos és a csoport a AzCopy-t futtató felhasználón alapul (alapértelmezett igaz)

**--az SMB-info megőrzése**   Alapértelmezés szerint hamis. A megőrzi az SMB-tulajdonságok adatait (az utolsó írási időt, a létrehozási időt, az attribútum-biteket) az SMB-kompatibilis erőforrások (Windows és Azure Files) között. Csak az Azure Files által támogatott attribútum-bitek lesznek átadva; minden más figyelmen kívül lesz hagyva. Ez a jelző mind a fájlokra, mind a mappákra vonatkozik, kivéve, ha meg van adva egy csak fájlhoz tartozó szűrő (például: include-Pattern). A mappákhoz továbbított adatok ugyanazok, mint a fájlok esetében, kivéve az utolsó írási időt, amelyet a rendszer soha nem tart fenn a mappákban.

**--megőrzése-SMB-engedélyek**   Alapértelmezés szerint hamis. Az SMB ACL-ek megőrzése az adatforrások (Windows és Azure Files) között. A letöltések esetében a jelzőt is meg kell `--backup` adni a visszaállításhoz, ha az új tulajdonos nem lesz a AzCopy-t futtató felhasználó. Ez a jelző a fájlokra és a mappákra is vonatkozik, kivéve, ha meg van adva egy csak fájl szűrő (például `include-pattern` ).

**--put-MD5**    Hozzon létre egy MD5-kivonatot minden fájlhoz, és mentse a kivonatot a cél blob vagy fájl tartalom-MD5 tulajdonságának megfelelően. (Alapértelmezés szerint a rendszer nem hozza létre a kivonatot.) Csak feltöltéskor érhető el.

**– rekurzív**    A helyi fájlrendszerből való feltöltéskor a rendszer rekurzív módon vizsgálja az alkönyvtárakat.

**--S2S-Detect-Source-changed**   Annak észlelése, hogy a forrásfájl/blob megváltozik-e az olvasáskor. (Ez a paraméter csak a szolgáltatások közötti másolatokra vonatkozik, mert a megfelelő ellenőrzések véglegesen engedélyezve vannak a feltöltésekhez és a letöltésekhez.)

**--S2S-Handle-érvénytelen – a metaadatok** karakterlánca megadja, hogy a rendszer hogyan kezelje az érvénytelen metaadat-kulcsokat. Elérhető lehetőségek: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (alapértelmezett `ExcludeIfInvalid` ). (alapértelmezett "ExcludeIfInvalid")

**--S2S-megőrzés – hozzáférési réteg**   A hozzáférési szintek megőrzése a szolgáltatás és a szolgáltatás közötti másolás során. Tekintse meg az [Azure Blob Storage: gyakori, ritka elérésű és archív hozzáférési rétegeket](../blobs/storage-blob-storage-tiers.md) , hogy a cél Storage-fiók támogassa a hozzáférési szint beállítását. Abban az esetben, ha a hozzáférési szintet nem támogatja, a s2sPreserveAccessTier = FALSE értéket kell használni a hozzáférési szintek másolásának mellőzéséhez. (alapértelmezett `true` ).  (alapértelmezett érték: "true")

**--S2S-megőrzése-Properties**   A szolgáltatás és a szolgáltatás közötti másolás során megőrzi a teljes tulajdonságokat. Az AWS S3 és az Azure file nem single file Source esetében a List művelet nem ad vissza objektumok és fájlok teljes tulajdonságait. A teljes tulajdonságok megőrzése érdekében a AzCopy egy további kérelmet kell küldenie egy objektumra vagy fájlra vonatkozóan. (alapértelmezett true)

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

**--Cap-Mbps lebegőpontos**   Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.

**--** a parancs kimenetének kimeneti típusú karakterlánc-formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték `text`. (alapértelmezett "text")

**--a megbízható-Microsoft-utótagok** karakterlánca további tartomány-utótagokat határoz meg, amelyekben Azure Active Directory bejelentkezési tokenek küldhetők.  A mező alapértelmezett értéke: `*.core.windows.net;*.core.chinacloudapi.cn;*.core.cloudapi.de;*.core.usgovcloudapi.net`. Az itt felsorolt beállítások az alapértelmezett értékre kerülnek. A biztonság érdekében itt csak Microsoft Azure-tartományokat helyezhet el. Több bejegyzést pontosvesszővel kell elválasztani.

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
