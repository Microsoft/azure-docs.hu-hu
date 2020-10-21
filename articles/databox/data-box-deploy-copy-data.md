---
title: Oktatóanyag az adatok SMB-n keresztül történő másolásához az Azure Data Boxra | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan csatlakozhat a gazdagéphez, és hogyan másolhat onnan adatokat az Azure Data Boxra az SMB és a helyi webes felhasználói felület használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/29/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 9427ec4530ac249d5b8059d04fc85f1183c0081c
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92123891"
---
::: zone target="docs"

# <a name="tutorial-copy-data-to-azure-data-box-via-smb"></a>Oktatóanyag: Adatok másolása az Azure Data Boxra SMB-n keresztül

::: zone-end

::: zone target="chromeless"

## <a name="copy-data-to-azure-data-box"></a>Adatok másolása az Azure Data Boxra

::: zone-end

::: zone target="docs"

Ez az oktatóanyag azt ismerteti, hogyan csatlakozhat a gazdagéphez és hogyan másolhat onnan adatokat a helyi webes felhasználói felület használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Csatlakozás a Data Boxhoz
> * Adatok másolása a Data Boxra

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Az [ Az Azure Data Box beállítása](data-box-deploy-set-up.md) című oktatóanyagot.
2. Megkapta a Data Boxot, és a portálon a megrendelés **Kézbesítve** állapotú.
3. Rendelkezik egy gazdagéppel, amelyen a Data Boxra másolni kívánt adatok találhatók. A gazdaszámítógépen:
   * egy [támogatott operációs rendszernek](data-box-system-requirements.md) kell futnia;
   * egy nagy sebességű hálózathoz kell csatlakoznia. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. Ha 10 GbE sebességű kapcsolat nem áll rendelkezésre, 1 GbE sebességű adatkapcsolat is használható, azonban ez csökkenti a másolási sebességet.

## <a name="connect-to-data-box"></a>Csatlakozás a Data Boxhoz

A kiválasztott tárfióktól függően a Data Box a következőket hozhatja létre:

* Három megosztás minden társított tárfiókhoz, GPv1-hez és GPv2-höz.
* Egy megosztás a prémium szintű Storage-hoz.
* Egy megosztás a Blob Storage-fiókhoz.

A blokkblob- és lapblobmegosztások alatti első szintű entitások tárolók, a második szintű entitások pedig blobok. Az Azure Files-megosztások alatti első szintű entitások megosztások, a második szintű entitások pedig fájlok.

Az alábbi táblázat a Data Boxon található megosztások UNC elérési útját és az adatok feltöltéséhez használt Azure Storage elérési útjának URL-címét mutatja. Az Azure Storage elérési útjának végső URL-címe a megosztás UNC elérési útjából származik.
 
|Azure Storage-tárolótípusok  | Data Box-megosztások            |
|-------------------|--------------------------------------------------------------------------------|
| Azure-blokkblobok | <li>A megosztások UNC elérési útja: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-lapblobok  | <li>A megosztások UNC elérési útja: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>A megosztások UNC elérési útja: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Ha Windows Server rendszerű gazdagépet használ, kövesse az alábbi lépéseket a Data Boxhoz történő csatlakozáshoz.

1. Az első lépés a hitelesítés elvégzése, majd a munkamenet elindítása. Lépjen a **Connect and copy** (Kapcsolódás és másolás) elemre. Kattintson az **SMB** elemre a tárfiókhoz társított megosztások hozzáférési hitelesítő adatainak lekéréséhez. 

    ![Megosztások hitelesítő adatainak beszerzése SMB-megosztásokhoz](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. Az Access share and copy data (Megosztási és másolási adatok másolása) párbeszédpanelen másolja ki a megosztásnak megfelelő **Username** (Felhasználónév) és **Password** (Jelszó) értékeket. Kattintson az **OK** gombra.
    
    ![Felhasználónév és jelszó beszerzése egy megosztáshoz](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. A tárfiókjához (a következő példában *utsac1*) társított megosztások gazdagépről történő eléréséhez nyisson meg egy parancsablakot. A parancssorba írja be a következőt:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Az adatok formátumától függően a megosztások útvonalai a következők:
    - Azure-blokkblob – `\\10.126.76.138\utSAC1_202006051000_BlockBlob`
    - Azure-lapblob – `\\10.126.76.138\utSAC1_202006051000_PageBlob`
    - Azure Files – `\\10.126.76.138\utSAC1_202006051000_AzFile`

4. Ha a rendszer kéri, adja meg a megosztás jelszavát. A következő példa bemutatja, hogyan kell csatlakozni egy megosztáshoz a fenti parancs használatával.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.138\utSAC1_202006051000_BlockBlob /u:testuser1
    Enter the password for 'testuser1' to connect to '10.126.76.138':
    The command completed successfully.
    ```

4. Nyomja le a Windows + R billentyűkombinációt. A **Futtatás** ablakban adja meg a következőt: `\\<device IP address>`. Kattintson az **OK** gombra a Fájlkezelő megnyitásához.
    
    ![Kapcsolódás a megosztáshoz a Fájlkezelővel](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

    A megosztásoknak ezután mappaként kell megjelenniük.
    
    ![A Fájlkezelőben látható megosztások](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png)

    **Mindig hozzon létre egy mappát azokhoz a fájlokhoz, amelyeket másolni szeretne a megosztás alatt, majd másolja a fájlokat a létrehozott mappába**. A blokkblob- és lapblobmegosztások alatt létrehozott mappa azt a tárolót jelöli, amelybe a rendszer feltölti az adatokat blobokként. Nem másolhat fájlokat közvetlenül a tárfiók *gyökér*mappájába.
    
Linux-ügyfél használata esetén csatlakoztassa az SMB-megosztást az alábbi parancs használatával. Az alábbi vers paraméter az SMB Linux-gazdagép által támogatott verziója. Az alábbi parancsban adja meg a megfelelő verziót. A Data Box által támogatott SMB-verziókkal kapcsolatban tekintse meg a [Linux-ügyfelek esetében támogatott fájlrendszereket](./data-box-system-requirements.md#supported-file-transfer-protocols-for-clients) ismertető cikket. 

```console
sudo mount -t nfs -o vers=2.1 10.126.76.138:/utSAC1_202006051000_BlockBlob /home/databoxubuntuhost/databox
```

## <a name="copy-data-to-data-box"></a>Adatok másolása a Data Boxra

A Data Box-megosztásokhoz történő csatlakozás után a következő lépés az adatok másolása. Az adatok másolásának megkezdése előtt tekintse át a következőket:

* Ügyeljen rá, hogy az adatokat a helyes adatformátumnak megfelelő megosztásokba másolja. A blokkblobadatokat például másolja a blokkbloboknak fenntartott megosztásba. Másolja a VHD-kat a lapblobba. Ha az adatok formátuma nem egyezik a megfelelő megosztástípussal, akkor egy későbbi lépés során az Azure-ba történő adatfeltöltés sikertelen lesz.
* Mindig hozzon létre egy mappát a megosztás alatt azokhoz a fájlokhoz, amelyeket másolni szeretne, majd másolja a fájlokat a létrehozott mappába. A blokkblob- és lapblobmegosztások alatt létrehozott mappa azt a tárolót jelöli, amelybe a rendszer feltölti az adatokat blobokként. Nem másolhat fájlokat közvetlenül a tárfiók *gyökér*mappájába.
* Az adatok másolása közben győződjön meg arról, hogy az adatok mérete megfelel az [Azure-tárfiók korlátaival](data-box-limits.md#azure-storage-account-size-limits) foglalkozó cikkben ismertetett méretkorlátoknak.
* Ha meg szeretné őrizni a metaadatokat (ACL-ek, időbélyegek, fájlattribútumok) az adatok az Azure Filesba történő átvitelekor, kövesse a [fájlok ACL-jeinek, attribútumainak és időbélyegeinek az Azure Data Box használata esetén történő megőrzését](data-box-file-acls-preservation.md) ismertető szakasz utasításait  
* Ha a Data Box által éppen feltöltés alatt álló adatokat egyidejűleg más, a Data Boxtól eltérő alkalmazás is feltölti, az a feltöltési feladat meghiúsulásához és az adatok sérüléséhez vezethet.
* A következő megoldást javasoljuk:
  * Ne használjon egyidejűleg SMB-t és NFS-t.
  * Ne másolja ugyanazokat az adatokat ugyanarra a célhelyre az Azure-ban.
  Ilyen esetekben a végeredmény nem garantálható.

> [!IMPORTANT]
> Gondoskodjon róla, hogy megtartja a forrásadatok egy másolatát addig, amíg ellenőrizheti, hogy a Data Box valóban átvitte-e az adatokat az Azure Storage-ba.

Az SMB-megosztáshoz való csatlakozás után kezdje meg az adatok másolását. Az adatok másolásához bármilyen SMB-kompatibilis fájlmásoló eszközt (pl.: Robocopy) használhat. A Robocopyval több másolási feladat is elindítható. Használja az alábbi parancsot:

```console
robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile>
```

Az attribútumok leírását az alábbi táblázatban találja meg.
    
|Attribútum  |Leírás  |
|---------|---------|
|/e     |Átmásolja az alkönyvtárakat, az üres könyvtárakkal együtt.         |
|/r:     |A meghiúsult másolások esetén indított újrapróbálkozások számát határozza meg.         |
|/w:     |Az újrapróbálkozások közötti várakozási időt határozza meg másodpercben.         |
|/is     |A fájlok nevét tartalmazza.         |
|/nfl     |Megadja, hogy a fájlnevek ne legyenek naplózva.         |
|/ndl    |Megadja, hogy a könyvtárnevek ne legyenek naplózva.        |
|/np     |Azt határozza meg, hogy ne jelenjen meg a másolási művelet állapota (az addig átmásolt fájlok vagy könyvtárak száma). Az állapot megjelenítése jelentősen csökkenti a teljesítményt.         |
|/MT     | Több szál használata. 32 vagy 64 szál használata ajánlott. Titkosított fájlokhoz ez a beállítás nem használható. Előfordulhat, hogy szét kell választania a titkosított és a nem titkosított fájlokat. Vegye figyelembe, hogy az egyszálas másolás jelentősen csökkenti a teljesítményt.           |
|/fft     | Ennek használatával bármilyen fájlrendszerben csökkentheti a timestamp részletességét.        |
|/b     | A fájlokat biztonsági mentési módban másolja.        |
|/z    | A fájlokat újraindítási módban másolja; akkor lehet rá szükség, ha a környezet instabil. Ez a beállítás a fokozott naplózás miatt csökkenti az átviteli sebességet.      |
| /zb     | Az újraindítási mód használata. A hozzáférés megtagadása esetén áttér a biztonsági mentési mód használatára. Ez a beállítás csökkenti az átviteli sebességet az ellenőrzőpontok létrehozása miatt.         |
|/efsraw     | Az összes titkosított fájlt EFS feldolgozatlan módban másolja át. Csak titkosított fájlokhoz használja.         |
|log+:\<LogFile>| Hozzáfűzi a kimenetet a meglévő naplófájlhoz.|    
 
Az alábbi minta a fájloknak a Data Boxra történő másolásához használt Robocopy-parancs kimenetét mutatja be.

```output
C:\Users>robocopy

    -------------------------------------------------------------------------------
    ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------

        Started : Thursday, March 8, 2018 2:34:53 PM
        Simple Usage :: ROBOCOPY source destination /MIR

        source :: Source Directory (drive:\path or \\server\share\path).
        destination :: Destination Dir  (drive:\path or \\server\share\path).
                /MIR :: Mirror a complete directory tree.

    For more usage information run ROBOCOPY /?

    ****  /MIR can DELETE files as well as copy them !

C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:32

    -------------------------------------------------------------------------------
    ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------

        Started : Thursday, March 8, 2018 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
            Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\

        Files : *.*

        Options : *.* /DCOPY:DA /COPY:DAT /MT:32 /R:5 /W:60

    ------------------------------------------------------------------------------

    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------

                Total    Copied   Skipped  Mismatch    FAILED    Extras
    Dirs :         1         1         1         0         0         0
    Files :        17        17         0         0         0         0
    Bytes :     3.9 k     3.9 k         0         0         0         0
C:\Users>
```

A teljesítmény optimalizálása érdekében használja a következő Robocopy-paramétereket az adatok másolásához.

|    Platform    |    Többnyire kis méretű fájlok < 512 kB                           |    Többnyire közepes méretű fájlok 512 kB – 1 MB                      |    Többnyire nagy méretű fájlok > 1 MB                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
|    Data Box         |    2 Robocopy-munkamenet <br> 16 szál munkamenetenként    |    3 Robocopy-munkamenet <br> 16 szál munkamenetenként    |    2 Robocopy-munkamenet <br> 24 szál munkamenetenként    |

A Robocopy-paranccsal kapcsolatos további információért lásd [a Robocopyt és néhány példát](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) bemutató témakört.

Ha hiba lép fel a másolási folyamat során, megjelenik erről egy értesítés.

![A másolással kapcsolatos hibaüzenet a Csatlakozás és másolás oldalon](media/data-box-deploy-copy-data/view-errors-1.png)

Válassza a **Hibalista letöltése** lehetőséget.

![Csatlakozás és másolás oldal hibáinak letöltése és megtekintése 2](media/data-box-deploy-copy-data/view-errors-2.png)

Nyissa meg a listát a hiba részleteinek megtekintéséhez, majd kattintson a megoldás URL-címére az ajánlott megoldás megtekintéséhez.

![Csatlakozás és másolás oldal hibáinak letöltése és megtekintése 3](media/data-box-deploy-copy-data/view-errors-3.png)

További információkért lásd: [Az adatok Data Boxra másolása során készült hibanaplók megtekintése](data-box-logs.md#view-error-log-during-data-copy). Az adatok másolása során felmerülő hibák részletes listájáért tekintse meg a [Data Box-problémák elhárításával](data-box-troubleshoot.md) kapcsolatos cikket.

Az adatok integritásának biztosítása érdekében az ellenőrzőösszeg kiszámítására beágyazva, az adatok másolása közben kerül sor. A másolás befejezése után ellenőrizze, hogy mekkora a felhasznált és a szabad tárhely az eszközén.

![A szabad és a felhasznált tárhely ellenőrzése az irányítópulton](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

::: zone-end

::: zone target="chromeless"

A forráskiszolgálóról SMB, NFS, REST és adatmásolási szolgáltatás segítségével másolhatja az adatokat a Data Boxra vagy felügyelt lemezekre.

Minden esetben gondoskodjon róla, hogy a megosztások és a mappák neve, valamint az adatok mérete megfeleljen [az Azure Storage és a Data Box szolgáltatás korlátaival](data-box-limits.md) foglalkozó cikkben foglaltaknak.

## <a name="copy-data-via-smb"></a>Adatok másolása SMB-n keresztül

Adatok másolása SMB-n keresztül:

1. Windows rendszerű gazdagép használata esetén csatlakoztassa az SMB-megosztásokat az alábbi parancs használatával:

    `\\<IP address of your device>\ShareName`

2. A megosztás eléréséhez szükséges hitelesítő adatokat a Data Box helyi webes kezelőfelületének **Connect & copy** (Kapcsolódás és másolás) lapján tekintheti meg.
3. Az adatok megosztásokba történő másolásához használjon SMB-kompatibilis fájlmásoló eszközt, mint amilyen a Robocopy. 

Részletes útmutatásért lásd: [Oktatóanyag: Adatok másolása az Azure Data Boxra SMB-n keresztül](data-box-deploy-copy-data.md).

## <a name="copy-data-via-nfs"></a>Adatok másolása NFS-en keresztül

Adatok másolása NFS-en keresztül:

1. NFS-gazdagép használata esetén csatlakoztassa az NFS-megosztásokat a Data Boxhoz az alábbi parancs használatával:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. A megosztás eléréséhez szükséges hitelesítő adatokat a Data Box helyi webes kezelőfelületének **Connect & copy** (Kapcsolódás és másolás) lapján tekintheti meg.
3. Az adatok másolásához használja a `cp` vagy az `rsync` parancsot.

Részletes útmutatásért lásd: [Oktatóanyag: Adatok másolása az Azure Data Boxra NFS-en keresztül](data-box-deploy-copy-data-via-nfs.md).

## <a name="copy-data-via-rest"></a>Adatok másolása REST-n keresztül

Adatok másolása REST-en keresztül:

1. Az adatok Data Box Blob Storage és REST API-k segítségével történő másolásához csatlakozhat *http* vagy *https* protokollal is.
2. Az adatok Data Box Blob Storage-ba másolásához használhatja az AzCopyt.

Részletes útmutatásért lásd: [Oktatóanyag: Adatok másolása a Data Box Blob Storage-ba REST API-k segítségével](data-box-deploy-copy-data-via-nfs.md).

## <a name="copy-data-via-data-copy-service"></a>Adatok másolása adatmásolási szolgáltatással

Adatok másolása adatmásolási szolgáltatással:

1. Az adatok adatmásolási szolgáltatás használatával történő másolásához létre kell hoznia egy feladatot. A Data Box helyi webes felhasználói felületén lépjen a **Kezelés > Adatok másolása > Létrehozás** lehetőségre.
2. Adja meg a paramétereket, és hozzon létre egy feladatot.

Részletes útmutatásért lásd: [Oktatóanyag: Adatok másolása az Azure Data Boxra az adatmásolási szolgáltatással](data-box-deploy-copy-data-via-copy-service.md).

## <a name="copy-data-to-managed-disks"></a>Adatok másolása felügyelt lemezekre

Adatok másolása felügyelt lemezekre:

1. A Data Box eszköz megrendelésekor a felügyelt lemezeket kellett kiválasztania tárolási célként.
2. A Data Boxhoz SMB- és NFS-megosztásokon keresztül is csatlakozhat.
3. Ezután SMB- vagy NFS-eszközökkel másolhatja az adatokat.

Részletes útmutatásért lásd: [Oktatóanyag: Adatok importálása felügyelt lemezekként az Azure-ban a Data Box használatával](data-box-deploy-copy-data-from-vhds.md).

::: zone-end

::: zone target="docs"

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
>
> * Előfeltételek
> * Csatlakozás a Data Boxhoz
> * Adatok másolása a Data Boxra

Folytassa a következő oktatóanyaggal, amelyben megismerheti, hogyan küldheti vissza a Data Boxot a Microsoftnak.

> [!div class="nextstepaction"]
> [Azure Data Box elküldése a Microsoftnak](./data-box-deploy-picked-up.md)

::: zone-end