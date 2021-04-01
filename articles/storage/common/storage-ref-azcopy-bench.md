---
title: azcopy pad | Microsoft Docs
description: Ez a cikk a azcopy pad parancsra vonatkozó tudnivalókat tartalmaz.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c1028d0a4a458746c08fd6fa4f16aa952d9962a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87282007"
---
# <a name="azcopy-benchmark"></a>azcopy-teljesítményteszt

Teljesítmény-teljesítménytesztet futtat, ha a megadott célhelyről vagy a tesztelési adatok letöltését végzi. A feltöltések esetében automatikusan létrejönnek a tesztelési adatok.

A teljesítményteszt-parancs ugyanazzal a folyamattal fut, mint a másolás, kivéve a következőket: 

  - Ahelyett, hogy a forrás-és a célként megadott paramétereket is megkövetelje, a teljesítményteszt csupán egyet vesz igénybe. Ez a blob-tároló, Azure Files megosztás vagy Azure Data Lake Storage Gen2 fájlrendszer, amelyet fel szeretne tölteni vagy le kíván tölteni.

  - A "Mode" paraméter azt ismerteti, hogy a AzCopy kell-e tesztelni az adott célhelyre való feltöltést vagy letöltést. Az érvényes értékek: "upload" és "Download". Az alapértelmezett érték a "feltöltés".

  - A feltöltési referenciaértékek esetében a hasznos adatokat a parancssori paraméterek írják le, amelyek azt szabályozzák, hogy hány fájl legyen automatikusan létrehozva, és milyen jelentős a fájlok. A létrehozási folyamat teljes mértékben a memóriában zajlik. A lemez nincs használatban.

  - A letöltések esetében a hasznos adatok a forráson már létező fájlokból állnak. (Lásd az alábbi példát a tesztelési fájlok létrehozásához, ha szükséges).
  
  - A másolási parancs számára elérhető választható paraméterek közül csak néhány támogatott.
  
  - A további diagnosztika mérése és jelentése.
  
  - A feltöltések esetében az alapértelmezett viselkedés az átvitt adatok törlése a teszt futtatása végén.  A letöltések esetében az adatgyűjtés soha nem történik meg helyileg.

A teljesítményteszt mód automatikusan beállíthatja a maximális átviteli sebességet biztosító párhuzamos TCP-kapcsolatok számát. Ez a szám a végén jelenik meg. Az automatikus hangolás megakadályozásához állítsa a AZCOPY_CONCURRENCY_VALUE környezeti változót egy adott számú kapcsolatra. 

A szokásos hitelesítési típusok mindegyike támogatott. Ugyanakkor a feltöltési teljesítmény legalkalmasabb megközelítése általában egy üres tároló létrehozása SAS-jogkivonattal és SAS-hitelesítés használata. (A letöltési mód megköveteli, hogy a célként megadott tárolóban legyen jelen a tesztelési érték.)

## <a name="examples"></a>Példák

```azcopy
azcopy benchmark [destination] [flags]
```

Teljesítményteszt-teszt futtatása alapértelmezett paraméterekkel (legfeljebb 1 GB/s sebességű hálózatok esetében alkalmas): "

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"
```
Futtasson egy teljesítményteszt-tesztet, amely 100 fájlt tölt fel, amelyek mindegyike 2 GiB méretű: (gyors hálózaton, például 10 GB/s):

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"--file-count 100 --size-per-file 2G
```
Futtasson teljesítményteszt-tesztet, de használjon 50 000-es fájlokat, amelyek mindegyike 8 MiB méretű, és az MD5-kivonatokat számítja ki (ugyanúgy, ahogyan a `--put-md5` jelző ezt a másolási paranccsal végzi el). A teljesítményértékelés célja `--put-md5` annak tesztelése, hogy az MD5 számítás befolyásolja-e a kiválasztott fájlok számát és méretét:

```azcopy
azcopy bench --mode='Upload' "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 50000 --size-per-file 8M --put-md5
```

A meglévő fájlokat a célhelyről letöltő teljesítményteszt-teszt futtatása

```azcopy
azcopy bench --mode='Download' "https://[account].blob.core.windows.net/[container]?<SAS?"
```

Olyan feltöltést futtasson, amely nem törli az átvitt fájlokat. (Ezek a fájlok ezután a letöltési teszt hasznos adataiként szolgálhatnak)

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 100 --delete-test-data=false
```

## <a name="options"></a>Beállítások

**--a blob-Type** karakterlánc határozza meg a blob típusát a célhelyen. A különböző blob-típusok teljesítményértékelésének engedélyezésére szolgál. Megegyezik a másolási parancs azonos nevű paraméterével (az alapértelmezett "észlelés").

**--Block-Size-MB** lebegőpontos használata ez a blokk mérete (a MIB-ben van megadva). A rendszer automatikusan kiszámítja az alapértelmezett értéket a fájlméret alapján. Tizedes törtek engedélyezettek – például 0,25. Azonos a másolási parancs azonos nevű paraméterével.

**--ellenőrzési hossz**  Az átvitel után a célhelyen lévő fájl hosszának ellenőrzését. Ha eltérés van a forrás és a cél között, az átvitel sikertelenként van megjelölve. (alapértelmezett true)

**--delete-test--adatelemzés**  Ha az értéke igaz, a rendszer a teljesítményteszt-futtatás végén törli a teljesítményteszt-adatmennyiséget.  Állítsa be hamis értékre, ha a célhelyen szeretné megőrizni az adattárolást, például a teljesítményteszt üzemmódján kívüli manuális teszteléshez (alapértelmezett érték: true).

**--file-Count** uint.  A használandó automatikusan létrehozott adatfájlok száma (alapértelmezett 100).

**– Súgó**  A pad súgója

**--a log szintű** karakterlánc határozza meg a naplófájl részletességét, a rendelkezésre álló szinteket: info (minden kérelem/válasz), figyelmeztetés (lassú válasz), hiba (csak sikertelen kérések), és nincs (nincs kimeneti napló). (alapértelmezett "információ")

a **--Mode** karakterlánc határozza meg, hogy a Azcopy kell-e tesztelni a feltöltéseket vagy a letöltéseket a célhelyről. Az érvényes értékek: "upload" és "Download". Az alapértelmezett beállítás a "feltöltés". (alapértelmezett feltöltési érték)

**--mappák száma** uint ha 0-nál nagyobb, hozzon létre mappákat az adatmegosztáshoz.

**--put-MD5**  Hozzon létre egy MD5-kivonatot minden fájlhoz, és mentse a kivonatot a cél blob/fájl tartalom-MD5 tulajdonságának megfelelően. (Alapértelmezés szerint a rendszer nem hozza létre a kivonatot.) Azonos a másolási parancs azonos nevű paraméterével.

– az egyes automatikusan létrehozott adatfájlok **mérete/fájl** mérete. A számnak közvetlenül a K, M vagy G érték után kell szerepelnie, például: 12k vagy 200G (alapértelmezett "250M").

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

**--Cap-Mbps lebegőpontos**  Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.

**--** a parancs kimenetének kimeneti típusú karakterlánc-formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text". (alapértelmezett "text").

**--a megbízható-Microsoft-utótagok** karakterlánca további tartomány-utótagokat határoz meg, amelyekben Azure Active Directory bejelentkezési tokenek küldhetők.  Az alapértelmezett érték: "*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Az itt felsorolt beállítások az alapértelmezett értékre kerülnek. A biztonság érdekében itt csak Microsoft Azure-tartományokat helyezhet el. Több bejegyzést pontosvesszővel kell elválasztani.


## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
