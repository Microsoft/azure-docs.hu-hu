---
title: azcopycopycopy | Microsoft Docs
description: Ez a cikk az azcopycopy parancs referenciainformációit biztosítja.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 1e49e787854069c2fcea30df7a43c3aacdd21b9e
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502028"
---
# <a name="azcopy-benchmark"></a>azcopy benchmark

Teljesítménytesztet futtat tesztadatok feltöltésével vagy letöltésével egy adott célhelyre vagy célról. Feltöltésekkor a tesztadatok automatikusan létrejönnek.

A teljesítményteszt parancsa ugyanazt a folyamatot futtatja, mint a "copy" (másolás) parancs, azzal a kivételt leszámítva: 

  - A forrás- és célparaméterek igénylése helyett a teljesítményteszt csak egyet vesz igénybe. Ez az a blobtároló, Azure Files Megosztás vagy Azure Data Lake Storage Gen2, amelybe fel szeretne tölteni vagy letölteni szeretne.

  - A "mode" paraméter azt írja le, hogy az AzCopy tesztelje-e az adott célra való feltöltést vagy letöltést. Az érvényes értékek az "Upload" (Feltöltés) és a "Download" (Letöltés). Az alapértelmezett érték az "Upload".

  - A feltöltési teljesítménytesztek hasznos adatát parancssori paraméterek írják le, amelyek azt írják le, hogy hány fájl lesz automatikusan generálva, és milyen jelentősek a fájlok. A generálási folyamat teljes egészében a memóriában történik. A lemez nincs használva.

  - Letöltésekkor a hasznos adatok a forrásban már létező fájlokból állnak. (Ha szükséges, tekintse meg az alábbi példát a tesztfájlok létrehozásáról).
  
  - A másolási parancshoz elérhető választható paraméterek csak néhány használata támogatott.
  
  - A rendszer további diagnosztikát is mér és jelent.
  
  - Feltöltések esetén az alapértelmezett viselkedés az átvitt adatok törlése a tesztfutat végén.  Letöltések esetén a rendszer soha nem menti helyileg az adatokat.

A teljesítményteszt mód automatikusan a maximális átviteli sebességet lehetővé tő párhuzamos TCP-kapcsolatok számára hangolja magát. Ez a szám jelenik meg a végén. Az automatikus csatlakozás megakadályozása érdekében állítsa a AZCOPY_CONCURRENCY_VALUE környezeti változót adott számú kapcsolatra. 

Az összes szokásos hitelesítési típus támogatott. A feltöltés teljesítményértékelésének legkényelmesebb módja azonban általában egy üres tároló létrehozása SAS-jogkivonattal és SAS-hitelesítés használata. (A letöltési módhoz tesztadatok egy készletének kell lennie a céltárolóban.)

## <a name="related-conceptual-articles"></a>Kapcsolódó elméleti cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Az AzCopy 10-es és Azure Storage-beli teljesítményének optimalizálása](storage-use-azcopy-optimize.md)


## <a name="examples"></a>Példák

```azcopy
azcopy benchmark [destination] [flags]
```

Teljesítményteszt futtatása alapértelmezett paraméterekkel (legfeljebb 1 Gb/s-os hálózatok teljesítményértékelésére alkalmas):

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"
```
Futtatassa a teljesítménytesztet, amely 100 fájlt tölt fel, amelyek mérete 2 GiB: (alkalmas gyors hálózaton, például 10 Gb/s-on való teljesítményteszthez):

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"--file-count 100 --size-per-file 2G
```
Futtatassa a teljesítménytesztet, de használjon 50 000 fájlt, mindegyik 8 MiB méretű fájlt, és számítsa ki az MD5-hasheket (ugyanúgy, ahogyan a jelölő ezt a másolási parancsban `--put-md5` teszi). A teljesítményteszt célja annak tesztelése, hogy az MD5-számítás befolyásolja-e a kiválasztott fájlszám és `--put-md5` -méret átviteli sebességét:

```azcopy
azcopy bench --mode='Upload' "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 50000 --size-per-file 8M --put-md5
```

Teljesítményteszt futtatása, amely meglévő fájlokat tölt le egy célból

```azcopy
azcopy bench --mode='Download' "https://[account].blob.core.windows.net/[container]?<SAS?"
```

Olyan feltöltést futtat, amely nem törli az átvitt fájlokat. (Ezek a fájlok hasznos adatként szolgálhatnak egy letöltési teszthez)

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 100 --delete-test-data=false
```

## <a name="options"></a>Beállítások

**--blob-type** string A célhelyen lévő blob típusát határozza meg. A különböző blobtípusok teljesítménymérésének lehetővé teszi. Ugyanaz, mint a másolási parancs azonos nevű paramétere (alapértelmezett "Észlelés").

**--block-size-mb** float Használja ezt a blokkméretet (a MiB-ban megadva). A rendszer automatikusan kiszámítja az alapértelmezett értéket a fájlméret alapján. Tizedes törtek engedélyezettek – például 0,25. Ugyanaz, mint a másolási parancs azonos nevű paramétere.

**--check-length**  Ellenőrizze a fájl hosszát a célhelyen az átvitel után. Ha eltérés van a forrás és a cél között, az átvitel sikertelenként lesz megjelölve. (alapértelmezett igaz)

**--delete-test-data**  Ha igaz, a teljesítményteszt adatai a teljesítményteszt futásának végén törlődnek.  Állítsa false (hamis) értékre, ha az adatokat a célhelyen szeretné tartani – például ha manuális tesztekhez szeretné használni a teljesítményteszt módon kívül (alapértelmezés szerint igaz).

**--file-count** uint.  A használni szükséges automatikusan létrehozott adatfájlok száma (alapértelmezés szerint 100).

**--help**  Segítség a temékhez

**--log-level** string Adja meg a naplófájl részletes naplóját, az elérhető szinteket: INFO(minden kérés/válasz), WARNING(lassú válaszok), ERROR(csak sikertelen kérések) és NONE(nincs kimeneti napló). (alapértelmezett "INFO")

**--mode string** Meghatározza, hogy az Azcopy tesztelje-e a célról való feltöltést vagy letöltést. Az érvényes értékek a "feltöltés" és a "letöltés". Az alapértelmezett beállítás a "feltöltés". (alapértelmezett feltöltés)

**--number-of-folders** uint Ha nagyobb, mint 0, hozzon létre mappákat az adatok felosztására.

**--put-md5**  Hozzon létre egy MD5-kivonatot az egyes fájlokból, és mentse a kivonatot a célblob/fájl Content-MD5 tulajdonságaként. (Alapértelmezés szerint a kivonat NEM jön létre.) Ugyanaz, mint a másolási parancs azonos nevű paramétere.

**--size-per-file string** Az automatikusan létrehozott adatfájl mérete. Egy olyan számnak kell lennie, amelyet közvetlenül követ a K, az M vagy a G. 12k vagy 200G (alapértelmezett "250 M").

## <a name="options-inherited-from-parent-commands"></a>A szülőparancsok által örökölt beállítások

**--cap-mbps lebegőpontos érték**  Megabit/másodpercben megszabja az átviteli sebességet. A pillanatnyi átviteli sebesség kis mértékben eltérhet a felső teljesítménytől. Ha ez a beállítás nulla vagy nincs megadva, az átviteli sebesség nincs korlátozva.

**--output-type string** A parancs kimenetének formátuma. A lehetőségek a következők: text, json. Az alapértelmezett érték a "text". (alapértelmezett "text").

**--trusted-microsoft-suffixes** sztring További tartomány-utótagokat ad meg, Azure Active Directory bejelentkezési jogkivonatokat lehet küldeni.  Az alapértelmezett érték a '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Az itt felsoroltak hozzáadva az alapértelmezetthez. A biztonság érdekében itt csak a Microsoft Azure helyezzen el. Több bejegyzést pontosvesszővel válassza el egymástól.


## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
