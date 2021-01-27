---
title: AzCopy konfigurálása, optimalizálása és hibáinak megoldása az Azure Storage szolgáltatással | Microsoft Docs
description: A AzCopy konfigurálása, optimalizálása és megoldása az Azure Storage-ban. Módosítsa a csomag helyét, vagy távolítsa el a naplófájlokat. Módosítsa az alapértelmezett naplózási szintet.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 1a319c728b918dbad7dd5f240bc7a0bfeb0c4c09
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881044"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>AzCopy konfigurálása, optimalizálása és megoldása

Az AzCopy egy parancssori segédprogram, amellyel blobokat és fájlokat másolhat a tárfiókok között. Ez a cikk segítséget nyújt a speciális konfigurációs feladatok elvégzéséhez és az AzCopy használata során felmerülő problémák elhárításához.

> [!NOTE]
> Ha olyan tartalmat keres, amely segítséget nyújt a AzCopy megkezdéséhez, tekintse meg a következő cikkeket:
> - [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
> - [Adatok átvitele a AzCopy és a blob Storage szolgáltatással](./storage-use-azcopy-v10.md#transfer-data)
> - [Adatok átvitele az AzCopy használatával és fájltárolás](storage-use-azcopy-files.md)
> - [Adatok átvitele a AzCopy és az Amazon S3 gyűjtővel](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Proxybeállítások konfigurálása

A AzCopy proxybeállítások konfigurálásához állítsa be a `HTTPS_PROXY` környezeti változót. Ha Windows rendszeren futtatja az AzCopyt, az AzCopy automatikusan észleli a proxybeállításokat, így Windows rendszeren nem kell használnia ezt a beállítást. Ha ezen beállítás használata mellett dönt Windows rendszeren, az felülírja az automatikus észlelést.

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | A parancssorban használja a következőket: `set HTTPS_PROXY=<proxy IP>:<proxy port>`<br> A PowerShellben használja a következőket: `$env:HTTPS_PROXY="<proxy IP>:<proxy port>"`|
| **Linux** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |
| **macOS** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |

A AzCopy jelenleg nem támogatja az NTLM-vagy Kerberos-hitelesítést igénylő proxykat.

### <a name="bypassing-a-proxy"></a>Proxy megkerülése ###

Ha Windows rendszeren futtatja a AzCopy-t, és azt szeretné tudni, hogy _ne_ használja a proxyt (a beállítások automatikus észlelése helyett), használja ezeket a parancsokat. Ezekkel a beállításokkal a AzCopy nem fog megjelenni, vagy nem próbálkozik a proxy használatára.

| Operációs rendszer | Környezet | Parancsok  |
|--------|-----------|----------|
| **Windows** | Parancssor (CMD) | `set HTTPS_PROXY=dummy.invalid` <br>`set NO_PROXY=*`|
| **Windows** | PowerShell | `$env:HTTPS_PROXY="dummy.invalid"` <br>`$env:NO_PROXY="*"`<br>|

Más operációs rendszereken egyszerűen ne törölje a HTTPS_PROXY változót, ha nem kíván proxyt használni.

## <a name="optimize-performance"></a>Teljesítmény optimalizálása

A teljesítmény és az erőforrás-felhasználás közötti optimális kompromisszum megtalálásához használhatja a teljesítményt, majd parancsokat és környezeti változókat használhat.

Ez a szakasz a következő optimalizálási feladatok elvégzéséhez nyújt segítséget:

> [!div class="checklist"]
> * Teljesítményteszt-tesztek futtatása
> * Teljesítmény optimalizálása
> * Memória használatának optimalizálása 
> * Fájlok szinkronizálásának optimalizálása

### <a name="run-benchmark-tests"></a>Teljesítményteszt-tesztek futtatása

Adott blob-tárolók vagy fájlmegosztás teljesítmény-teljesítményteszt-tesztjét futtatva megtekintheti az általános teljesítménymutatókat és a teljesítmény szűk keresztmetszeteit. A tesztet a generált teszteredmények feltöltésével vagy letöltésével futtathatja. 

A teljesítmény-teljesítményteszt teszt futtatásához használja a következő parancsot.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy benchmark 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **Példa** | `azcopy benchmark 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

> [!TIP]
> Ez a példa a Path argumentumokat szimpla idézőjelekkel (' ') fedi le. Használjon egy idézőjelet az összes parancs-rendszerhéjon, kivéve a Windows parancs-rendszerhéjt (cmd.exe). Ha Windows parancs-rendszerhéjt (cmd.exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé foglalja a Path argumentumokat ("").

Ez a parancs teljesítménytesztet futtat egy megadott célhelyre való feltöltéssel. A tesztelési adatok a memóriában jönnek létre, feltöltve a célhelyre, majd a teszt befejezése után törlődnek a célhelyről. Megadhatja, hogy hány fájl legyen létrehozva, és hogy milyen méretben szeretné őket használni a választható parancssori paraméterek használatával.

Ha a tesztet az adatletöltéssel szeretné futtatni, állítsa a paramétert a következőre: `mode` `download` . A részletes dokumentációs dokumentáció: [azcopy benchmark](storage-ref-azcopy-bench.md). 

### <a name="optimize-throughput"></a>Teljesítmény optimalizálása

A `cap-mbps` parancsokban a jelzőt használhatja, hogy a felső korlátot az adatátviteli sebességre helyezze. A következő parancs például egy feladatot és a Caps átviteli sebességét a másodpercenként `10` megabit (MB) értékre folytatja. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

Az átviteli sebesség kisebb fájlok átvitele esetén csökkenhet. A környezeti változó beállításával növelheti az átviteli sebességet `AZCOPY_CONCURRENCY_VALUE` . Ez a változó meghatározza az egyidejű kérelmek lehetséges számát.  

Ha a számítógép kevesebb mint 5 processzorral rendelkezik, akkor a változó értéke a következő lesz: `32` . Egyéb esetben az alapértelmezett érték a CPU-k száma szorozva 16-tal. Ennek a változónak a maximális alapértelmezett értéke a `3000` , de ezt az értéket manuálisan vagy lejjebb is állíthatja. 

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **macOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

A `azcopy env` változó aktuális értékének megadásához használja a következőt:. Ha az érték üres, akkor megtekintheti, hogy melyik értéket használja a rendszer a AzCopy-naplófájl elején. A kijelölt érték és a kiválasztott ok a jelentésekben szerepelnek.

Mielőtt beállítja ezt a változót, javasoljuk, hogy futtasson egy teljesítményteszt-tesztet. A teljesítményteszt-tesztelési folyamat a javasolt Egyidejűség értékét fogja jelenteni. Ha a hálózati feltételek és a hasznos adatok eltérőek, akkor a változót `AUTO` egy adott szám helyett a szóhoz kell beállítani. Ez azt eredményezi, hogy a AzCopy mindig ugyanazt az Automatikus hangolási folyamatot fogja futtatni, amelyet a teljesítményteszt-tesztekben használ.

### <a name="optimize-memory-use"></a>Memória használatának optimalizálása

Állítsa be a `AZCOPY_BUFFER_GB` környezeti változót a fájlok letöltésekor és feltöltésekor használni kívánt AzCopy maximális mennyiségének megadásához.
Ez az érték gigabájtban (GB) kifejezve.

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **macOS** | `export AZCOPY_BUFFER_GB=<value>` |

### <a name="optimize-file-synchronization"></a>Fájlok szinkronizálásának optimalizálása

A [szinkronizálási](storage-ref-azcopy-sync.md) parancs azonosítja a célhelyen lévő összes fájlt, majd a szinkronizálási művelet megkezdése előtt összehasonlítja a fájlneveket és az utolsó módosítás időbélyegeket. Ha nagy számú fájlt használ, a teljesítmény javítása érdekében kiküszöbölheti ezt a kezdeti feldolgozást. 

Ehhez használja helyette a [azcopy Copy](storage-ref-azcopy-copy.md) parancsot, és állítsa a jelölőt a következőre: `--overwrite` `ifSourceNewer` . A AzCopy össze fogja hasonlítani a fájlokat a másolásuk előtt, anélkül, hogy előzetes vizsgálatokat és összehasonlításokat végezne. Ez olyan teljesítményt biztosít, amelyben nagy mennyiségű fájlt kell összehasonlítani.

A [azcopy másolási](storage-ref-azcopy-copy.md) parancs nem törli a célhelyről a fájlokat, ezért ha törölni szeretné a célhelyen lévő fájlokat, ha már nem léteznek a forrásnál, akkor a [azcopy Sync](storage-ref-azcopy-sync.md) parancsot a (z) `--delete-destination` vagy értékre állítja a jelzővel `true` `prompt` . 

## <a name="troubleshoot-issues"></a>Problémák elhárítása

Az AzCopy napló- és tervfájlokat hoz létre az összes feladathoz. A naplók segítségével kivizsgálhatja és elháríthatja a lehetséges problémákat. 

A naplók a hiba ( `UPLOADFAILED` , `COPYFAILED` , és `DOWNLOADFAILED` ) állapotát, a teljes elérési utat és a hiba okát fogják tartalmazni.

Alapértelmezés szerint a napló-és a terv fájlok a `%USERPROFILE%\.azcopy` Windows vagy a címtár Mac és Linux rendszerű könyvtárában találhatók `$HOME$\.azcopy` , de ha kívánja, módosíthatja ezt a helyet.

A kapcsolódó hiba nem feltétlenül a fájlban megjelenő első hiba. Hibák, például hálózati hibák, időtúllépések és a kiszolgáló foglalt hibái esetén a AzCopy legfeljebb 20 alkalommal próbálkozik újra, és általában az újrapróbálkozási folyamat sikeres lesz.  A megjelenő első hiba lehet valami ártalmatlan, amely sikeresen újrapróbálkozott.  Tehát ahelyett, hogy a fájl első hibáját keresi, keresse meg a közelében, vagy a következő hibákat: `UPLOADFAILED` `COPYFAILED` `DOWNLOADFAILED` . 

> [!IMPORTANT]
> Amikor kérelmet küld a Microsoft ügyfélszolgálata (vagy bármely harmadik féltől származó probléma elhárítása), ossza meg a futtatni kívánt parancs kivont verzióját. Ez biztosítja, hogy az SAS véletlenül senkivel se legyen megosztva. A leválasztott verziót a naplófájl elején találja.

### <a name="review-the-logs-for-errors"></a>A hibákkal kapcsolatos naplók áttekintése

A következő parancs az állapottal kapcsolatos összes hibát `UPLOADFAILED` a `04dc9ca9-158f-7945-5933-564021086c79` naplóból fogja kapni:

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Feladatok megtekintése és folytatása

Minden egyes átviteli művelet létrehoz egy AzCopy-feladatot. A következő parancs használatával tekintheti meg a feladatok előzményeit:

```
azcopy jobs list
```

A feladatok statisztikáinak megtekintéséhez használja a következő parancsot:

```
azcopy jobs show <job-id>
```

Az átvitelek állapot szerinti szűréséhez használja a következő parancsot:

```
azcopy jobs show <job-id> --with-status=Failed
```

A sikertelen/megszakított feladat folytatásához használja az alábbi parancsot. Ez a parancs az azonosítót az SAS-jogkivonattal együtt használja, mivel a biztonsági okokból nem állandó.

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

> [!TIP]
> Az elérési út argumentumai, például a SAS-token egyetlen idézőjelekkel (' '). Használjon egy idézőjelet az összes parancs-rendszerhéjon, kivéve a Windows parancs-rendszerhéjt (cmd.exe). Ha Windows parancs-rendszerhéjt (cmd.exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé foglalja a Path argumentumokat ("").

Amikor folytat egy feladatot, a AzCopy megtekinti a feladatütemezés fájlját. A terv fájl felsorolja a feladatok első létrehozásakor feldolgozásra azonosított összes fájlt. Ha folytatja a feladatot, a AzCopy megkísérli átvinni az összes olyan fájlt, amely a csomagban már nem lett áthelyezve.

## <a name="change-the-location-of-the-plan-and-log-files"></a>A csomag és a naplófájlok helyének módosítása

Alapértelmezés szerint a terv és a naplófájlok a `%USERPROFILE%\.azcopy` Windows könyvtárban vagy a `$HOME$\.azcopy` Mac és Linux rendszerű könyvtárban találhatók. Ezt a helyet módosíthatja.

### <a name="change-the-location-of-plan-files"></a>A csomag fájljai helyének módosítása

Használja az alábbi parancsok bármelyikét.

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | PowerShell`$env:AZCOPY_JOB_PLAN_LOCATION="<value>"` <br> A parancssorban használja a következőket: `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **macOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

A `azcopy env` változó aktuális értékének megadásához használja a következőt:. Ha az érték üres, akkor a fájlok megtervezése az alapértelmezett helyre történik.

### <a name="change-the-location-of-log-files"></a>A naplófájlok helyének módosítása

Használja az alábbi parancsok bármelyikét.

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | PowerShell`$env:AZCOPY_LOG_LOCATION="<value>"` <br> A parancssorban használja a következőket: `set AZCOPY_LOG_LOCATION=<value>`|
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **macOS** | `export AZCOPY_LOG_LOCATION=<value>` |

A `azcopy env` változó aktuális értékének megadásához használja a következőt:. Ha az érték üres, akkor a naplók az alapértelmezett helyre íródnak.

## <a name="change-the-default-log-level"></a>Az alapértelmezett naplózási szint módosítása

Alapértelmezés szerint a AzCopy naplózási szintje a következőre van beállítva: `INFO` . Ha csökkenteni szeretné a napló részletességét, hogy lemezterületet szabadítson fel, írja felül ezt a beállítást a ``--log-level`` kapcsoló használatával. 

A rendelkezésre álló naplózási szintek a következők:,,,,, `NONE` `DEBUG` `INFO` `WARNING` `ERROR` `PANIC` és `FATAL` .

## <a name="remove-plan-and-log-files"></a>Csomag-és naplófájlok eltávolítása

Ha el szeretné távolítani az összes csomagot és naplófájlt a helyi gépről, hogy lemezterületet szabadítson fel, használja az `azcopy jobs clean` parancsot.

Csak egy feladatokhoz társított csomag és naplófájlok eltávolításához használja a következőt: `azcopy jobs rm <job-id>` . Cserélje le a példában szereplő helyőrzőt a feladatokhoz tartozó azonosítójú elemre `<job-id>` .
