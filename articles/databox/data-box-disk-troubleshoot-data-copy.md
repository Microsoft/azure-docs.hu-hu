---
title: Az adatmásolási problémák elhárítása Azure Data Box Disk | Microsoft Docs
description: Leírja, hogyan lehet elhárítani a naplók használatával Azure Data Box Disk Adatmásolás során észlelt problémákat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 06/13/2019
ms.author: alkohli
ms.openlocfilehash: 5d977fe0b7459af35f678e77681d3b27c31431cc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85849186"
---
# <a name="troubleshoot-data-copy-issues-in-azure-data-box-disk"></a>A Azure Data Box Disk adatmásolási problémáinak elhárítása

Ez a cikk Microsoft Azure Data Box Diskra vonatkozik, és leírja, hogyan lehet elhárítani az adatok lemezekre másolásakor megjelenő problémákat. A cikk a felosztott másolási eszköz használata során felmerülő problémákat is tárgyalja.


## <a name="data-copy-issues-when-using-a-linux-system"></a>Adatmásolási problémák Linux rendszer használata esetén

Ez a szakasz a Linux-ügyfelek lemezekre másolásakor felmerülő leggyakoribb problémákat ismerteti.

### <a name="issue-drive-getting-mounted-as-read-only"></a>Probléma: a meghajtó csak olvashatóként van csatlakoztatva
 
**Ok** 

Ennek oka lehet egy nem tiszta fájlrendszer.

A meghajtó újracsatlakoztatása írható-olvashatóként nem működik Data Box lemezekkel. Ez a forgatókönyv nem támogatott a delocker által visszafejtett meghajtókon. Lehetséges, hogy sikeresen újracsatlakoztatta az eszközt az alábbi parancs használatával:

```
# mount -o remount, rw /mnt/DataBoxDisk/mountVol1
```

Bár az újracsatlakoztatás sikeres volt, a rendszer nem őrzi meg az adatmennyiséget.

**Resolution** (Osztás)

Hajtsa végre a következő lépéseket a Linux rendszeren:

1. Telepítse a `ntfsprogs` csomagot a ntfsfix segédprogramhoz.
2. Válassza le a meghajtóhoz megadott csatlakoztatási pontokat a feloldási eszközön. A csatlakoztatási pontok száma változó lesz a meghajtóknál.

    ```
    unmount /mnt/DataBoxDisk/mountVol1
    ```

3. Futtassa a parancsot `ntfsfix` a megfelelő elérési úton. A kijelölt számnak meg kell egyeznie a 2. lépéssel.

    ```
    ntfsfix /mnt/DataBoxDisk/bitlockerVol1/dislocker-file
    ```

4. A következő parancs futtatásával távolítsa el a csatlakoztatási problémát okozó hibernációs metaadatokat.

    ```
    ntfs-3g -o remove_hiberfile /mnt/DataBoxDisk/bitlockerVol1/dislocker-file /mnt/DataBoxDisk/mountVol1
    ```

5. Tiszta leválasztást hajtson végre.

    ```
    ./DataBoxDiskUnlock_x86_64 /unmount
    ```

6. Végezze el a tiszta feloldást és a csatlakoztatást.
7. A csatlakoztatási pont tesztelése egy fájl megírásával.
8. Válassza le és csatlakoztassa újra a fájlok megőrzésének ellenőrzéséhez.
9. Folytassa az adatmásolással.
 
### <a name="issue-error-with-data-not-persisting-after-copy"></a>Probléma: a másolás után nem megőrzött adatmegőrzési hiba
 
**Ok** 

Ha úgy látja, hogy a meghajtó nem rendelkezik a leválasztást követően (bár az adatmásolt), akkor lehetséges, hogy a meghajtót csak olvashatóként csatlakoztatta újra írásra.

**Resolution** (Osztás)
 
Ebben az esetben tekintse meg a [csak olvashatóként csatlakoztatott meghajtók](#issue-drive-getting-mounted-as-read-only)feloldását.

Ha nem volt ilyen, másolja a naplókat a Data Box Disk-feloldási eszközt tartalmazó mappából, és [vegye fel a kapcsolatot Microsoft ügyfélszolgálata](data-box-disk-contact-microsoft-support.md).


## <a name="data-box-disk-split-copy-tool-errors"></a>A Data Box Disk másolásfelosztó eszközének hibái

Az alábbi táblázatban láthatók a megosztott másolási eszközök több lemezre való felosztásához használt problémák.

|Hibaüzenet/figyelmeztetések |Javaslatok |
|---------|---------|
|Információ A következő kötet BitLocker-jelszavának beolvasása: m <br>Hiba Kivétel történt az m kötet BitLocker-kulcsának beolvasása közben:<br> A szekvencia nem tartalmaz elemeket.|Ez a hibaüzenet azt jelzi, hogy a Data Box Disk célhelye offline állapotú. <br> A lemezek online állapotúvá tételéhez használja a `diskmgmt.msc` eszközt.|
|[Hiba] A következő kivétel történt: A WMI-művelet sikertelen volt:<br> Method=UnlockWithNumericalPassword, ReturnValue=2150694965, <br>Win32Message=A megadott helyreállítási jelszó formátuma érvénytelen. <br>A Bitlocker helyreállítási jelszavai 48 számjegyűek. <br>Ellenőrizze a helyreállítási jelszó formátumát, majd próbálkozzon újra.|Először oldja fel a lemezek zárolását a Data Box Disk lemezzárolás-feloldó eszközével, majd próbálja újból végrehajtani a parancsot. További információért lásd: <li> [a Data Box Disk lemezzárolás-feloldó eszközének Windows-ügyfélen való használatát ismertető részt](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client). </li><li> [a Data Box Disk lemezzárolás-feloldó eszközének Linux-ügyfélen való használatát ismertető részt](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client). </li>|
|[Hiba] A következő kivétel történt: A DriveManifest.xml fájl megtalálható a célmeghajtón. <br> Ez azt jelezheti, hogy a célmeghajtót más naplófájllal készítették elő. <br>Ha további adatokat szeretne hozzáadni ugyanahhoz a meghajtóhoz, használja az előző naplófájlt. A meglévő adatok törléséhez és a célként megadott meghajtó új importálási feladatokhoz való újrafelhasználásához törölje a *DriveManifest.xml* a meghajtón. Futtassa újra ezt a parancsot egy új naplófájllal.| Ez a hiba akkor fordulhat elő, ha több importálási munkamenethez is ugyanazt a meghajtókészletet próbálja használni. <br> Egy adott meghajtókészletet csak egyetlen másolásfelosztási munkamenethez használjon.|
|[Hiba] A következő kivétel történt: A CopySessionId importdata-sept-test-1 egy korábbi másolási munkamenetre hivatkozik, és nem használható fel újra egy új másolási munkamenethez.|Ez a hiba akkor fordulhat elő, ha egy új feladat neveként egy korábban már sikeresen befejeződött feladat nevét próbálja megadni.<br> Egyedi nevet adjon meg az új feladat számára.|
|[Információ] A célfájl vagy -könyvtár neve túllépte az NTFS fájlrendszerre érvényes hosszkorlátot. |Ezt az üzenetet akkor kapja, ha át kellett nevezni a célfájlt, mert túl hosszú volt a fájl elérési útja.<br> Ezt a viselkedést a `config.json` fájl diszponálási beállításánál módosíthatja.|
|[Hiba] A következő kivétel történt: Hibás JSON-feloldókarakter. |Ezt az üzenetet akkor kapja, ha a config.json fájl érvénytelen formátumot használ. <br> A fájl mentése előtt ellenőrizze a `config.json` fájlt a [JSONlint](https://jsonlint.com/) eszközzel.|


## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogy miként lehet [elhárítani az ellenőrző eszköz hibáit](data-box-disk-troubleshoot.md).
