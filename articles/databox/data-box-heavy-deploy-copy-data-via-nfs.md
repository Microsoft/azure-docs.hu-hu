---
title: Az adatAzure Data Box Heavy NFS-en keresztüli adatmásolási oktatóanyag | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan csatlakozhat a gazdagépről, és hogyan másolhat adatokból Azure Data Box Heavy az NFS és a helyi webes felhasználói felület használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: a8199039ea803d5919e168edccaaa03c18985725
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87921027"
---
# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-nfs"></a>Oktatóanyag: az Adatmásolás Azure Data Box Heavy NFS-en keresztül

Ez az oktatóanyag azt ismerteti, hogyan csatlakozhat a gazdagépről, és hogyan másolhatja azokat a helyi webes KEZELŐFELÜLETtel a Azure Data Box Heavy.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Előfeltételek
> * Csatlakozás a Data Box Heavyhez
> * Adatok másolása a Data Box Heavyre

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Elvégezte az [oktatóanyagot: Azure Data Box Heavy beállítása](data-box-heavy-deploy-set-up.md).
2. Megkapta a Data Box Heavyt, és a portálon megjelenő megrendelés állapota **kézbesítve**.
3. Rendelkezik egy gazdagéppel, amelyen a Data Box Heavyre másolni kívánt adatok találhatók. A gazdaszámítógépen:
    - egy [támogatott operációs rendszernek](data-box-heavy-system-requirements.md) kell futnia;
    - egy nagy sebességű hálózathoz kell csatlakoznia. A legnagyobb másolási sebesség érdekében két 40 GbE sebességű kapcsolat (csomópontonként egy) használható egyidejűleg. Ha nem rendelkezik 40 GbE sebességű kapcsolattal, javasoljuk, hogy legalább két 10 GbE sebességű kapcsolattal (csomópontonként eggyel) rendelkezzen. 

## <a name="connect-to-data-box-heavy"></a>Csatlakozás a Data Box Heavyhez

A kiválasztott tárfióktól függően a Data Box Heavy a következőket hozhatja létre:
- Három megosztás minden társított tárfiókhoz, GPv1-hez és GPv2-höz.
- Egy megosztás a prémium szintű Storage-hoz.
- Egy megosztás a Blob Storage-fiókhoz.

Ezek a megosztások az eszköz mindkét csomópontján létrejönnek.

A blokkblob- és lapblobmegosztások alatt:
- Az első szintű entitások a tárolók.
- A második szintű entitások a blobok.

A megosztások alatt az Azure Files esetében:
- Az első szintű entitások a megosztások.
- A második szintű entitások a fájlok.

Az alábbi táblázat a Data Box Heavyn található megosztások UNC elérési útját és az adatok feltöltéséhez használt Azure Storage elérési útjának URL-címét mutatja. Az Azure Storage elérési útjának végső URL-címe a megosztás UNC elérési útjából származik.
 
| Storage           | UNC elérési út                                                                       |
|-------------------|--------------------------------------------------------------------------------|
| Azure-blokkblobok | <li>A megosztások UNC elérési útja: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-lapblobok  | <li>A megosztások UNC elérési útja: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>A megosztások UNC elérési útja: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

Ha Linux rendszerű gazdagépet használ, a következő lépésekkel konfigurálhatja az eszközt az NFS-ügyfelek elérésének engedélyezéséhez.

1. Adja meg azon ügyfelek IP-címeit, akik hozzáférhetnek a megosztáshoz. A helyi webes felületen lépjen a **Connect and copy** (Kapcsolódás és másolás) lapra. Az **NFS settings** (NFS-beállítások) pontban kattintson az **NFS client access** (NFS-ügyfélhozzáférés) lehetőségre. 

    ![NFS-ügyfélhozzáférés konfigurálása 1](media/data-box-deploy-copy-data/nfs-client-access.png)

2. Adja meg az NFS-ügynök IP-címét, és kattintson az **Add** (Hozzáadás) gombra. Ezt a lépést megismételve további NFS-ügyfeleket is konfigurálhat. Kattintson az **OK** gombra.

    ![NFS-ügyfélhozzáférés konfigurálása 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Győződjön meg arról, hogy a Linux gazdagépen az NFS-ügyfél [támogatott verziója](data-box-system-requirements.md) van telepítve. Használja a Linux-disztribúciónak megfelelő verziót. 

3. Az NFS-ügyfél telepítését követően az alábbi paranccsal csatlakoztathatja a Data Box-eszközön található NFS-megosztást:

    `sudo mount <Data Box Heavy device IP>:/<NFS share on Data Box Heavy device> <Path to the folder on local Linux computer>`

    Az alábbi példa bemutatja, hogyan csatlakozhat NFS-en keresztül egy Data Box Heavy-megosztáshoz. A Data Box Heavy IP-cím: `10.161.23.130` a megosztás a `Mystoracct_Blob` ubuntuVM, a csatlakoztatási ponthoz van csatlakoztatva `/home/databoxheavyubuntuhost/databoxheavy` .

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxheavyubuntuhost/databoxheavy`
    
    Mac-ügyfelek esetén a következő módon kell hozzáadnia egy további lehetőséget: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxheavyubuntuhost/databoxheavy`

    **Mindig hozzon létre egy mappát azokhoz a fájlokhoz, amelyeket másolni szeretne a megosztás alatt, majd másolja a fájlokat a létrehozott mappába**. A blokkblob- és lapblobmegosztások alatt létrehozott mappa azt a tárolót jelöli, amelybe a rendszer feltölti az adatokat blobokként. Nem másolhat fájlokat közvetlenül a tárfiók *gyökér* mappájába.

## <a name="copy-data-to-data-box-heavy"></a>Adatok másolása a Data Box Heavyre

Ha csatlakozott a Data Box Heavy-megosztásokhoz, a következő lépés az Adatmásolás. Az adatok másolásának megkezdése előtt tekintse át a következőket:

- Győződjön meg arról, hogy az adatokat a helyes adatformátumnak megfelelő megosztásokba másolja. A blokkblobadatokat például másolja a blokkbloboknak fenntartott megosztásba. Másolja a VHD-ket az oldal blobokra. Ha az adatok formátuma nem egyezik a megfelelő megosztástípussal, akkor egy későbbi lépés során az Azure-ba történő adatfeltöltés sikertelen lesz.
-  Az adatok másolása közben ügyeljen arra, hogy az adatok mérete megfeleljen az [Azure Storage-ban és a Data Box Heavy korlátokban](data-box-heavy-limits.md)leírt méretkorlát-korlátoknak. 
- Ha a Data Box Heavy által éppen feltöltés alatt álló adatokat egyidejűleg más alkalmazások is feltöltik a Data Box Heavyn kívül, az a feltöltési feladat meghiúsulásához és az adatok sérüléséhez vezethet.
- Azt javasoljuk, hogy ne használjon egyidejűleg SMB-t és NFS-t az Azure-ban, illetve ne másolja ugyanazokat az adatokat ugyanarra a célhelyre. Ilyen esetekben a végeredmény nem garantálható.
- **Mindig hozzon létre egy mappát azokhoz a fájlokhoz, amelyeket másolni szeretne a megosztás alatt, majd másolja a fájlokat a létrehozott mappába**. A blokkblob- és lapblobmegosztások alatt létrehozott mappa azt a tárolót jelöli, amelybe a rendszer feltölti az adatokat blobokként. Nem másolhat fájlokat közvetlenül a tárfiók *gyökér* mappájába.
- Ha a kis-és nagybetűket megkülönböztető könyvtárakat és fájlneveket az NFS-megosztásról az NFS-re Data Box Heavy: 
    - Az eset a névben marad.
    - A fájlok kis-és nagybetűk megkülönböztetése nélkül.
    
    Ha például a másolás és a, akkor a rendszer a `SampleFile.txt` nevet az `Samplefile.Txt` eszközre való másoláskor megőrzi, de a második fájl felülírja az elsőt, mivel ezek ugyanaz a fájlnak tekintendők.


Linux rendszerű gazdagép esetében használjon egy, a Robocopyhoz hasonló másolási segédprogramot. Ilyen például az [rsync](https://rsync.samba.org/), a [FreeFileSync](https://www.freefilesync.org/), a [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) vagy az [Ultracopier](https://ultracopier.first-world.info/).  

A `cp` parancs az egyik legjobb választás a könyvtárak másolására. A parancs használatáról [a cp tájékoztató oldalain](http://man7.org/linux/man-pages/man1/cp.1.html) talál további információt.

Amennyiben az rsyncet használja többszálas másoláshoz, a következő irányelveket kell betartania:

 - Telepítse a **CIFS Utils** vagy az **NFS Utils** csomagot, attól függően, hogy a Linux-ügyfél milyen fájlrendszert használ.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

 -  Telepítse az **Rsyncet** és a **Parallelt** (a Linux elosztott verziójától függően).

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

 - Hozzon létre egy csatlakozási pontot.

    `sudo mkdir /mnt/databoxheavy`

 - Csatlakoztassa a kötetet.

    `sudo mount -t NFS4  //Databox-heavy-IP-Address/share_name /mnt/databoxheavy` 

 - Tükrözze a mappa könyvtárstruktúráját.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databoxheavy`

 - Másolja át a fájlokat. 

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databoxheavy/{}`

     A j a párhuzamos folyamatok számát jelöli, X = párhuzamos példányok száma

     Azt javasoljuk, hogy kezdetben 16 párhuzamos példánnyal dolgozzon, és az elérhető erőforrásoknak megfelelően növelje a szálak számát.

> [!IMPORTANT]
> A következő Linux-fájltípusok nem támogatottak: szimbolikus hivatkozások, szövegfájlok, fájlok, szoftvercsatornák és csövek blokkolása. Ezek a fájltípusok a **szállításra való előkészítés** lépésben hibát okoznak.

Nyissa meg a célmappát a másolt fájlok megtekintéséhez és ellenőrzéséhez. Ha hibába ütközik a másolási folyamat során, töltse le a hibafájlokat a hibaelhárításhoz. További információkért lásd: [Az adatok Data Box Heavyre másolása során készült hibanaplók megtekintése](data-box-logs.md#view-error-log-during-data-copy). Az adatok másolása során felmerülő hibák részletes listájáért tekintse meg a [Data Box Heavy-problémák elhárításával](data-box-troubleshoot.md) kapcsolatos cikket.

Az adatok integritásának biztosítása érdekében az ellenőrzőösszeg kiszámítására beágyazva, az adatok másolása közben kerül sor. A másolás befejezése után ellenőrizze, hogy mekkora a felhasznált és a szabad tárhely az eszközén.
    
   ![A szabad és a felhasznált tárhely ellenőrzése az irányítópulton](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box Heavyvel kapcsolatos témakörökkel ismerkedett meg, például a következőkkel:

> [!div class="checklist"]
> * Előfeltételek
> * Csatlakozás a Data Box Heavyhez
> * Adatok másolása a Data Box Heavyre


Folytassa a következő oktatóanyaggal, amelyben megismerheti, hogyan küldheti vissza a Data Boxot a Microsoftnak.

> [!div class="nextstepaction"]
> [Az Azure Data Box Heavy visszaküldése a Microsoftnak](./data-box-heavy-deploy-picked-up.md)

