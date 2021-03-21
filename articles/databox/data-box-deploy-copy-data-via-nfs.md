---
title: Az adatAzure Data Box NFS-en keresztüli adatmásolási oktatóanyag | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan csatlakozhat a gazdagépről, és hogyan másolhat adatokból Azure Data Box az NFS és a helyi webes felhasználói felület használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: alkohli
ms.openlocfilehash: d53a619dc6ca5fb0f43f6097664f50bf22943928
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97678879"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-nfs"></a>Oktatóanyag: az Adatmásolás Azure Data Box NFS-en keresztül

Ez az oktatóanyag azt ismerteti, hogyan csatlakozhat a gazdagéphez és hogyan másolhat onnan adatokat a helyi webes felhasználói felület használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Csatlakozás a Data Boxhoz
> * Adatok másolása a Data Boxra

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Befejezte [az Azure Data Box beállítását ismertető oktatóanyagot](data-box-deploy-set-up.md).
2. Megkapta a Data Boxot, és a portálon a megrendelés **Kézbesítve** állapotú.
3. Rendelkezik egy gazdagéppel, amelyen a Data Boxra másolni kívánt adatok találhatók. A gazdaszámítógépen:
    - egy [támogatott operációs rendszernek](data-box-system-requirements.md) kell futnia;
    - egy nagy sebességű hálózathoz kell csatlakoznia. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. Ha egy 10 GbE-kapcsolat nem érhető el, a rendszer egy 1 GbE adatkapcsolatot használ, de a másolási sebesség hatással lesz rá. 

## <a name="connect-to-data-box"></a>Csatlakozás a Data Boxhoz

A kiválasztott tárfióktól függően a Data Box a következőket hozhatja létre:
- Három megosztás minden társított tárfiókhoz, GPv1-hez és GPv2-höz.
- Egy megosztás a prémium szintű Storage-hoz. 
- Egy megosztás a Blob Storage-fiókhoz. 

A blokkblob- és lapblobmegosztások alatti első szintű entitások tárolók, a második szintű entitások pedig blobok. Az Azure Files-megosztások alatti első szintű entitások megosztások, a második szintű entitások pedig fájlok.

Az alábbi táblázat a Data Boxon található megosztások UNC elérési útját és az adatok feltöltéséhez használt Azure Storage elérési útjának URL-címét mutatja. Az Azure Storage elérési útjának végső URL-címe a megosztás UNC elérési útjából származik.
 
| Azure Storage-típus| Data Box-megosztások                                       |
|-------------------|--------------------------------------------------------------------------------|
| Azure-blokkblobok | <li>A megosztások UNC elérési útja: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-lapblobok  | <li>A megosztások UNC elérési útja: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>A megosztások UNC elérési útja: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

Amennyiben Linux rendszerű gazdagépet használ, a következő módon konfigurálhatja a Data Boxot, hogy hozzáférést biztosítson az NFS-ügyelek számára.

1. Adja meg azon ügyfelek IP-címeit, akik hozzáférhetnek a megosztáshoz. A helyi webes felületen lépjen a **Connect and copy** (Kapcsolódás és másolás) lapra. Az **NFS settings** (NFS-beállítások) pontban kattintson az **NFS client access** (NFS-ügyfélhozzáférés) lehetőségre. 

    ![NFS-ügyfél-hozzáférés konfigurálása](media/data-box-deploy-copy-data/nfs-client-access-1.png)

2. Adja meg az NFS-ügynök IP-címét, és kattintson az **Add** (Hozzáadás) gombra. Ezt a lépést megismételve további NFS-ügyfeleket is konfigurálhat. Kattintson az **OK** gombra.

    ![NFS-ügyfél IP-címének konfigurálása](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Győződjön meg arról, hogy a Linux gazdagépen az NFS-ügyfél [támogatott verziója](data-box-system-requirements.md) van telepítve. Használja a Linux-disztribúciónak megfelelő verziót. 

3. Az NFS-ügyfél telepítését követően az alábbi paranccsal csatlakoztathatja a Data Box-eszközön található NFS-megosztást:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    Az alábbi példa bemutatja, hogyan kell NFS-en keresztüli kapcsolódni egy Data Box-megosztáshoz. A Data Box-eszköz IP-címe `10.161.23.130`, a megosztás (`Mystoracct_Blob`) az ubuntuVM virtuális géphez csatlakozik, a csatlakozási pont pedig a következő: `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`
    
    Mac-ügyfelek esetén a következő módon kell hozzáadnia egy további lehetőséget: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **Mindig hozzon létre egy mappát azokhoz a fájlokhoz, amelyeket másolni szeretne a megosztás alatt, majd másolja a fájlokat a létrehozott mappába**. A blokkblob- és lapblobmegosztások alatt létrehozott mappa azt a tárolót jelöli, amelybe a rendszer feltölti az adatokat blobokként. Nem másolhat fájlokat közvetlenül a tárfiók *gyökér* mappájába.

## <a name="copy-data-to-data-box"></a>Adatok másolása a Data Boxra

A Data Box-megosztáshoz való kapcsolódás után a következő lépés az adatok másolása. Az adatok másolásának megkezdése előtt tekintse át a következőket:

* Győződjön meg arról, hogy az adatokat a helyes adatformátumnak megfelelő megosztásokba másolja. A blokkblobadatokat például másolja a blokkbloboknak fenntartott megosztásba. Másolja a VHD-ket az oldal blobokra. Ha az adatok formátuma nem egyezik a megfelelő megosztástípussal, akkor egy későbbi lépés során az Azure-ba történő adatfeltöltés sikertelen lesz.
*  Az adatok másolása közben ellenőrizze, hogy az adatok mérete megfelel-e az [Azure Storage-fiók méretének korlátaiban](data-box-limits.md#azure-storage-account-size-limits)ismertetett méretkorlát-korlátoknak.
* Ha a Data Box által éppen feltöltés alatt álló adatokat egyidejűleg egy másik alkalmazás is feltölti a Data Boxon kívül, ez a feltöltési feladatok meghiúsulásához és az adatok meghibásodásához vezethet.
* Azt javasoljuk, hogy ne használjon egyidejűleg SMB-t és NFS-t az Azure-ban, illetve ne másolja ugyanazokat az adatokat ugyanarra a célhelyre. Ilyen esetekben a végeredmény nem garantálható.
* **Mindig hozzon létre egy mappát azokhoz a fájlokhoz, amelyeket másolni szeretne a megosztás alatt, majd másolja a fájlokat a létrehozott mappába**. A blokkblob- és lapblobmegosztások alatt létrehozott mappa azt a tárolót jelöli, amelybe a rendszer feltölti az adatokat blobokként. Nem másolhat fájlokat közvetlenül a tárfiók *gyökér* mappájába.
* Ha a kis-és nagybetűket megkülönböztető könyvtárakat és fájlneveket az NFS-megosztásról az NFS-re Data Box:
  * Az eset a névben marad.
  * A fájlok kis-és nagybetűk megkülönböztetése nélkül.

    Ha például a másoláskor a (z `SampleFile.txt` ), a (z) és a (z `Samplefile.Txt` ), akkor a rendszer a nevet megőrzi a Data boxban, de a második fájl felülírja az elsőt, mivel ezek ugyanaz a fájlnak számítanak.

> [!IMPORTANT]
> Gondoskodjon róla, hogy megtartja a forrásadatok egy másolatát addig, amíg ellenőrizheti, hogy a Data Box valóban átvitte-e az adatokat az Azure Storage-ba.

Linux rendszerű gazdagép esetében használjon egy, a Robocopyhoz hasonló másolási segédprogramot. A Linux rendszerben elérhető alternatívák némelyike [`rsync`](https://rsync.samba.org/) , [FreeFileSync](https://www.freefilesync.org/), [egyszólamú](https://www.cis.upenn.edu/~bcpierce/unison/)vagy [Ultracopier](https://ultracopier.first-world.info/).  

A `cp` parancs az egyik legjobb választás a könyvtárak másolására. A parancs használatáról [a cp tájékoztató oldalain](http://man7.org/linux/man-pages/man1/cp.1.html) talál további információt.

Ha `rsync` többszálas másolás esetén a beállítást használja, kövesse az alábbi irányelveket:

* Telepítse a **CIFS Utils** vagy az **NFS Utils** csomagot, attól függően, hogy a Linux-ügyfél milyen fájlrendszert használ.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

* `rsync`A install és a **Parallel** (a Linux elosztott verziójától függően változhat).

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

* Hozzon létre egy csatlakozási pontot.

    `sudo mkdir /mnt/databox`

* Csatlakoztassa a kötetet.

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

* Tükrözze a mappa könyvtárstruktúráját.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

* Másolja át a fájlokat.

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     A j a párhuzamos folyamatok számát jelöli, X = párhuzamos példányok száma

     Azt javasoljuk, hogy kezdetben 16 párhuzamos példánnyal dolgozzon, és az elérhető erőforrásoknak megfelelően növelje a szálak számát.

> [!IMPORTANT]
> A következő Linux-fájltípusok nem támogatottak: szimbolikus hivatkozások, szövegfájlok, fájlok, szoftvercsatornák és csövek blokkolása. Ezek a fájltípusok a **szállításra való előkészítés** lépésben hibát okoznak.

Ha hiba lép fel a másolási folyamat során, megjelenik erről egy értesítés.

![Csatlakozás és másolás oldal hibáinak letöltése és megtekintése](media/data-box-deploy-copy-data/view-errors-1.png)

Válassza a **Hibalista letöltése** lehetőséget.

![Másolási hiba a problémák listájának letöltése](media/data-box-deploy-copy-data/view-errors-2.png)

Nyissa meg a listát a hiba részleteinek megtekintéséhez, majd kattintson a megoldás URL-címére az ajánlott megoldás megtekintéséhez.

![Hibák a másolási hibák listájában](media/data-box-deploy-copy-data/view-errors-3.png)

További információkért lásd: [Az adatok Data Boxra másolása során készült hibanaplók megtekintése](data-box-logs.md#view-error-log-during-data-copy). Az adatok másolása során felmerülő hibák részletes listájáért tekintse meg a [Data Box-problémák elhárításával](data-box-troubleshoot.md) kapcsolatos cikket.

Az adatok integritásának biztosítása érdekében az ellenőrzőösszeg kiszámítására beágyazva, az adatok másolása közben kerül sor. A másolás befejezése után ellenőrizze, hogy mekkora a felhasznált és a szabad tárhely az eszközén.

   ![A szabad és a felhasznált tárhely ellenőrzése az irányítópulton](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
>
> * Előfeltételek
> * Csatlakozás a Data Boxhoz
> * Adatok másolása a Data Boxra

Folytassa a következő oktatóanyaggal, amelyben megismerheti, hogyan küldheti vissza a Data Boxot a Microsoftnak.

> [!div class="nextstepaction"]
> [Azure Data Box elküldése a Microsoftnak](./data-box-deploy-picked-up.md)
