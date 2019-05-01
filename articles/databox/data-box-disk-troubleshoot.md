---
title: Azure Data Box Disk – hibaelhárítás | Microsoft Docs
description: Ez a cikk az Azure Data Box Disk szolgáltatásban jelentkező hibák elhárítását írja le.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/2/2019
ms.author: alkohli
ms.openlocfilehash: f9d01b56da2650be395878ce07e4aae73495061f
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939642"
---
# <a name="troubleshoot-issues-in-azure-data-box-disk"></a>Az Azure Data Box-lemezek hibáinak elhárítása

Ez a cikk azt ismerteti, a munkafolyamatok elháríthat bármilyen hibát lát, ez a megoldás üzembe helyezésekor használt, és a Microsoft Azure Data Box-lemezek vonatkozik. 

Ez a cikk az alábbi szakaszokat tartalmazza:

- Diagnosztikai naplók letöltése
- Tevékenységnaplók lekérdezése
- A Data Box Disk lemezzárolás-feloldó eszközének hibái
- A Data Box Disk másolásfelosztó eszközének hibái

## <a name="download-diagnostic-logs"></a>Diagnosztikai naplók letöltése

Ha az adatmásolási folyamat során hibák lépnek fel, a portálon megjelenik a mappa útvonala, amely a diagnosztikai naplókat tárolja. 

A diagnosztikai naplók lehetnek:
- Hibanaplók
- Részletes naplók  

A naplómásolási útvonal megkereséséhez lépjen a Data Box-rendeléshez társított tárfiókra. 

1.  Lépjen a **General > Order details** (Általános > Rendelés részletei) felületre, és jegyezze fel a rendeléshez társított tárfiókot.
 

2.  Lépjen az **All resources** (Összes erőforrás) felületre, és keresse meg az előző lépésben azonosított tárfiókot. Válassza ki a tárfiókot, majd kattintson rá.

    ![Naplók másolása 1](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs1.png)

3.  Lépjen a **Blob service > Browse blobs** (Blob szolgáltatás > Blobok tallózása) felületre, és keresse meg a tárfióknak megfelelő blobot. Lépjen a **diagnosticslogcontainer > waies** menüpontra. 

    ![Naplók másolása 2](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs2.png)

    Az adatmásolás hibanaplóinak és részletes naplóinak egyaránt meg kell jelennie. Válassza ki az egyes fájlokat, kattintson rájuk, és töltsön le egy-egy helyi példányt.

## <a name="query-activity-logs"></a>Tevékenységnaplók lekérdezése

A tevékenységnaplókból hibaelhárításkor megkeresheti a hibákat, vagy nyomon követheti, hogy a szervezete felhasználói hogyan módosították az erőforrásokat. A tevékenységnaplókból a következők állapíthatók meg:

- Az előfizetésben lévő erőforrásokon végrehajtott műveletek.
- A művelet kezdeményezője.
- A művelet végrehajtásának időpontja.
- A művelet állapota.
- A művelet felderítése során hasznosítható egyéb tulajdonságok értékei.

A tevékenységnapló tartalmazza az erőforrásokon végrehajtott összes írási műveletet (például PUT, POST, DELETE), az olvasási műveleteket (például GET) azonban nem.

A tevékenységnaplók 90 napon keresztül érhetők el. Bármilyen dátumtartományt lekérdezhet, amíg a kezdő dátum legfeljebb 90 nappal korábbra esik. Emellett az Insights beépített lekérdezéseivel is szűrheti az eredményeket. Például ha a hibára kattint, ezután kiválaszthatja a különféle meghibásodásokat, és rájuk kattinthat a kiváltó okok elemzéséhez.

## <a name="data-box-disk-unlock-tool-errors"></a>A Data Box Disk lemezzárolás-feloldó eszközének hibái


| Hibaüzenet/az eszköz viselkedése      | Javaslatok                                                                                               |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| None<br><br>A Data Box Disk zárolásának feloldására szolgáló eszköz összeomlik.                                                                            | A BitLocker nincs telepítve. Gondoskodjon róla, hogy a Data Box Disk zárolásának feloldására szolgáló eszközt futtató számítógépen a BitLocker telepítve legyen.                                                                            |
| A jelenlegi .NET-keretrendszer nem támogatott. A 4.5-ös és újabb verziók támogatottak.<br><br>Az eszköz hibaüzenettel bezárul.  | A .NET 4.5-ös verziója nincs telepítve. Telepítse a .NET 4.5-ös verzióját a Data Box Disk zárolásának feloldására szolgáló eszközt futtató számítógépre.                                                                            |
| Nem sikerült egyetlen kötetet sem feloldani vagy ellenőrizni. Vegye fel a kapcsolatot a Microsoft támogatási szolgálatával.  <br><br>Az eszköz nem tudott egyetlen zárolt meghajtót sem feloldani vagy ellenőrizni. | Az eszköz egyik zárolt meghajtót sem tudta feloldani a megadott hozzáférési kulccsal. A további lépésekhez kérjen segítséget a Microsoft ügyfélszolgálatától.                                                |
| Az alábbi kötetek lettek feloldva és ellenőrizve. <br>Meghajtó-betűjelek: E:<br>A következő hozzáférési kulcsokkal egyetlen kötetet sem sikerült feloldani: werwerqomnf, qwerwerqwdfda <br><br>Az eszköz felold egyes meghajtókat, és listázza a sikeres és sikertelen meghajtók betűjelét.| Részleges siker. Egyes meghajtókat nem sikerült feloldani a megadott hozzáférési kulccsal. A további lépésekhez kérjen segítséget a Microsoft ügyfélszolgálatától. |
| Az eszköz nem talált zárolt köteteket. Ellenőrizze, hogy a Microsofttól kapott lemez megfelelően csatlakoztatva és zárolt állapotban van-e.          | Az eszköz nem talált egyetlen zárolt meghajtót sem. A meghajtók már fel lettek oldva, vagy a rendszer nem észleli őket. Győződjön meg arról, hogy a meghajtók csatlakoztatva vannak és zároltak.                                                           |
| Végzetes hiba: Érvénytelen paraméter<br>Paraméter neve: invalid_arg<br>HASZNÁLAT:<br>DataBoxDiskUnlock /PassKeys:<passkey_list_separated_by_semicolon><br><br>Példa: DataBoxDiskUnlock /PassKeys:passkey1; passkey2; passkey3<br>Példa: DataBoxDiskUnlock /SystemCheck<br>Példa: DataBoxDiskUnlock /Help<br><br>/ Hozzáférési kulcsok:       A hozzáférési kulcs lekérése Azure DataBox lemezrendelését. A hozzáférési kulccsal oldhatók fel a lemezek.<br>/Help:           Ez a beállítás a parancsmag használati és a példákat nyújt segítséget.<br>/SystemCheck:    Ez a beállítás ellenőrzi, hogy ha a rendszer megfelel-e az eszköz futtatására vonatkozó követelményeknek.<br><br>A kilépéshez nyomja le bármelyik billentyűt. | Érvénytelen paraméter lett megadva. Csak engedélyezett paraméterei a következők: /SystemCheck /PassKey és/help.                                                                            |

## <a name="data-box-disk-split-copy-tool-errors"></a>A Data Box Disk másolásfelosztó eszközének hibái

|Hibaüzenet/figyelmeztetések  |Javaslatok |
|---------|---------|
|[Információ] Kötet BitLocker-jelszó beolvasása: m <br>[Hiba] Kivétel történt a kötet m: BitLocker-kulcs beolvasása közben<br> A szekvencia nem tartalmaz elemeket.|Ez a hibaüzenet azt jelzi, hogy a Data Box Disk célhelye offline állapotú. <br> A lemezek online állapotúvá tételéhez használja a `diskmgmt.msc` eszközt.|
|[Hiba] Kivétel lépett fel: Nem sikerült a WMI-műveletet:<br> Method=UnlockWithNumericalPassword, ReturnValue=2150694965, <br>Win32Message=A megadott helyreállítási jelszó formátuma érvénytelen. <br>A Bitlocker helyreállítási jelszavai 48 számjegyűek. <br>Ellenőrizze a helyreállítási jelszó formátumát, majd próbálkozzon újra.|Először oldja fel a lemezek zárolását a Data Box Disk lemezzárolás-feloldó eszközével, majd próbálja újból végrehajtani a parancsot. További információért lásd: <li> [a Data Box Disk lemezzárolás-feloldó eszközének Windows-ügyfélen való használatát ismertető részt](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client). </li><li> [a Data Box Disk lemezzárolás-feloldó eszközének Linux-ügyfélen való használatát ismertető részt](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client). </li>|
|[Hiba] Kivétel lépett fel: DriveManifest.xml fájl létezik a cél-meghajtón. <br> Ez azt jelezheti, hogy a célmeghajtót más naplófájllal készítették elő. <br>Ha további adatokat szeretne hozzáadni ugyanahhoz a meghajtóhoz, használja az előző naplófájlt. Ha törölni szeretné a meglévő adatokat, és a célmeghajtót új importálási feladathoz szeretné használni, törölje a DriveManifest.xml fájlt a meghajtóról. Futtassa újra ezt a parancsot egy új naplófájllal.| Ez a hiba akkor fordulhat elő, ha több importálási munkamenethez is ugyanazt a meghajtókészletet próbálja használni. <br> Egy adott meghajtókészletet csak egyetlen másolásfelosztási munkamenethez használjon.|
|[Hiba] Kivétel lépett fel: CopySessionId importadatokat szeptembertől-teszt – 1. egy előző másolási munkamenet hivatkozik, és nem használható fel újra egy új példányt munkamenethez.|Ez a hiba akkor fordulhat elő, ha egy új feladat neveként egy korábban már sikeresen befejeződött feladat nevét próbálja megadni.<br> Egyedi nevet adjon meg az új feladat számára.|
|[Információ] A célfájl vagy -könyvtár neve túllépte az NTFS fájlrendszerre érvényes hosszkorlátot. |Ezt az üzenetet akkor kapja, ha át kellett nevezni a célfájlt, mert túl hosszú volt a fájl elérési útja.<br> Ezt a viselkedést a `config.json` fájl diszponálási beállításánál módosíthatja.|
|[Hiba] Kivétel lépett fel: Hibás JSON escape-szekvencia. |Ezt az üzenetet akkor kapja, ha a config.json fájl érvénytelen formátumot használ. <br> A fájl mentése előtt ellenőrizze a `config.json` fájlt a [JSONlint](https://jsonlint.com/) eszközzel.|

## <a name="deployment-issues-for-linux"></a>Üzembe helyezési problémák Linux rendszeren

Ebben a szakaszban egy Linux-ügyfél használata az adatok másolása a Data Box-lemezek üzembe helyezése során szembesülnek kapcsolatos leggyakoribb hibák némelyike részletezi.

### <a name="issue-drive-getting-mounted-as-read-only"></a>Probléma: Írásvédett meghajtóként első csatlakoztatott meghajtó
 
**OK** 

Ezt okozhatja egy kernelverziója fájlrendszer. 

Data Box-lemezek írási-olvasási egy meghajtón szolgáltatással nem működik. Ez a forgatókönyv nem támogatott meghajtókkal dislocker visszafejteni. Előfordulhat, hogy rendelkezik sikerült csatlakoztatni az eszköz a következő paranccsal:

    `# mount -o remount, rw /mnt/DataBoxDisk/mountVol1`

Bár a szolgáltatással sikeres volt, az adatok nem megmaradnak.

**Felbontás**

Az alábbi lépéseket a Linux rendszeren:

1. Telepítse a `ntfsprogs` a ntfsfix segédprogram-csomagja.
2. Válassza le a csatlakoztatási pontok, a meghajtó a feloldás eszköz által biztosított. A csatlakoztatási pontok száma változhat meghajtókhoz.

    ```
    unmount /mnt/DataBoxDisk/mountVol1
    ```

3. Futtatás `ntfsfix` a megfelelő elérési úton. A kiemelt számot kell megadni, 2. lépés.

    ```
    ntfsfix /mnt/DataBoxDisk/bitlockerVol1/dislocker-file
    ```

4. Futtassa a következő paranccsal távolítható el a hibernálási metaadatokat, amelyek a csatlakozási problémát okozhatnak.

    ```
    ntfs-3g -o remove_hiberfile /mnt/DataBoxDisk/bitlockerVol1/dislocker-file /mnt/DataBoxDisk/mountVol1
    ```

5. Ezt egy tiszta leválasztás.

    ```
    ./DataBoxDiskUnlock_x86_64 /unmount
    ```

6. Hajtsa végre egy tiszta zárolásának feloldása, és csatlakoztassa.
7. Tesztelje a csatlakoztatási pont egy fájl írása.
8. Válassza le és csatlakoztassa újra a fájlmegőrzéssel ellenőrzése.
9. Folytassa az adatok másolását.
 
### <a name="issue-error-with-data-not-persisting-after-copy"></a>Probléma: Hiba történt az adatok másolása után nem megőrzése
 
**OK** 

Ha látja, hogy a meghajtó nem rendelkezik adatokkal után, leválasztani (bár az adatok másolta azt), akkor lehetséges, csak olvasható a meghajtó csatlakoztatása után ismét csatolva legyen olvasási és írási egy meghajtón.

**Felbontás**
 
Ha ez a helyzet, nézzék meg a [írásvédett meghajtóként első csatlakoztatott meghajtók](#issue-drive-getting-mounted-as-read-only).

Amely nem másolja a naplókat az a mappa, amely a Data Box lemez zárolásának feloldásához eszköz és [forduljon a Microsoft Support](data-box-disk-contact-microsoft-support.md).

## <a name="deployment-issues-for-windows"></a>Windows központi telepítési problémái

Ez a szakasz részletesen a Data Box-lemezek üzembe helyezése során szembesülnek, amikor a Windows-ügyfél használatával az adatok másolása leggyakoribb problémák

### <a name="issue-could-not-unlock-drive-from-bitlocker"></a>Probléma: A BitLocker meghajtó nem zárolásának feloldása.
 
**OK** 

A BitLocker párbeszédpanelen használta a jelszavát, és meghajtók párbeszédpanelen keresztül a Bitlockert a lemez zárolásának feloldásához próbál zárolásának feloldásához. Ez nem működik. 

**Felbontás**

A Data Box-lemezek zárolásának feloldásához meg kell a Data Box lemez zárolásának feloldásához eszközzel, és adja meg a jelszót az Azure Portalról. További információért ugorjon [oktatóanyag: Csomagolja ki, csatlakozzon, és az Azure Data Box-lemezek feloldásához](data-box-disk-deploy-set-up.md#connect-to-disks-and-get-the-passkey).
 
### <a name="issue-could-not-unlock-or-verify-some-volumes-contact-microsoft-support"></a>Probléma: Nem zárolásának feloldásához, vagy ellenőrizze az egyes kötetek. Vegye fel a kapcsolatot a Microsoft támogatási szolgálatával.
 
**OK** 

Előfordulhat, hogy tekintse meg a következő hiba történt a hibanapló, és nem, feloldhatja és ellenőrzése néhány kötet.

`Exception System.IO.FileNotFoundException: Could not load file or assembly 'Microsoft.Management.Infrastructure, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. The system cannot find the file specified.`
 
Ez azt jelzi, hogy valószínűleg hiányzik a Windows PowerShell megfelelő verziója a Windows-ügyfélen.

**Felbontás**

Telepíthet [Windows PowerShell-v 5.0](https://www.microsoft.com/download/details.aspx?id=54616) , majd próbálja megismételni a műveletet.
 
Ha még mindig nem tudja feloldani a kötetek nem ismeri, a naplók másolása a mappában, amely a Data Box lemez zárolásának feloldásához eszközzel rendelkezik, és [forduljon a Microsoft Support](data-box-disk-contact-microsoft-support.md).

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [felügyelheti a Data Box Disket az Azure Portalon keresztül](data-box-portal-ui-admin.md).
