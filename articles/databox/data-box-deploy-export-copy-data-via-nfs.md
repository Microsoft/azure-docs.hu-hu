---
title: Oktatóanyag az adatok NFS-ből történő másolásáról a Azure Data Box használatával | Microsoft Docs
description: Ismerje meg, hogyan másolhat adatok a Azure Data Box NFS-en keresztül
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: alkohli
ms.openlocfilehash: bd8e6d4175c57bd31c3fd83bf6f9669d2b65ffb2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91660843"
---
# <a name="tutorial-copy-data-from-azure-data-box-via-nfs-preview"></a>Oktatóanyag: adatok másolása Azure Data Boxról NFS-en keresztül (előzetes verzió)

Ez az oktatóanyag azt ismerteti, hogyan lehet csatlakozni a Data Box helyi webes FELÜLETéről egy helyszíni adatkiszolgálóba az NFS-en keresztül. A Data Box lévő adatok az Azure Storage-fiókból lesznek exportálva.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Csatlakozás a Data Boxhoz
> * Adatok másolása a Data Boxról

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Megrendelt egy Azure Data Boxot.
    - Az importálási rendelésekkel kapcsolatos információkért lásd [ Az Azure Data Box megrendelése](data-box-deploy-ordered.md) című oktatóanyagot.
    - Exportálási rendelésekkel kapcsolatos információkért lásd: [Oktatóanyag: Az Azure Data Box megrendelése](data-box-deploy-export-ordered.md) című oktatóanyagot.
2. Megkapta a Data Boxot, és a portálon a megrendelés **Kézbesítve** állapotú.
3. Rendelkezik egy gazdagéppel, amelyre át kívánja másolni az adatokat a Data Boxról. A gazdaszámítógépen:
   * egy [támogatott operációs rendszernek](data-box-system-requirements.md) kell futnia;
   * egy nagy sebességű hálózathoz kell csatlakoznia. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. Ha 10 GbE sebességű kapcsolat nem áll rendelkezésre, 1 GbE sebességű adatkapcsolat is használható, azonban ez csökkenti a másolási sebességet.

## <a name="connect-to-data-box"></a>Csatlakozás a Data Boxhoz

[!INCLUDE [data-box-shares](../../includes/data-box-shares.md)]

Amennyiben Linux rendszerű gazdagépet használ, a következő módon konfigurálhatja a Data Boxot, hogy hozzáférést biztosítson az NFS-ügyelek számára. Data Box egyszerre legfeljebb öt NFS-ügyfelet tud összekötni.

1. Adja meg a megosztást elérő engedélyezett ügyfelek IP-címeit:

    1.  A helyi webes KEZELŐFELÜLETen lépjen a **Kapcsolódás és másolás** lapra. Az **NFS settings** (NFS-beállítások) pontban kattintson az **NFS client access** (NFS-ügyfélhozzáférés) lehetőségre. 

        ![NFS-ügyfél-hozzáférés megnyitása](media/data-box-deploy-export-copy-data/nfs-client-access-1.png)

    1. NFS-ügyfél hozzáadásához adja meg az ügyfél IP-címét, és kattintson a **Hozzáadás**gombra. Data Box egyszerre legfeljebb öt NFS-ügyfelet tud összekötni. Ha végzett, kattintson **az OK**gombra.

         ![NFS-ügyfél hozzáadása](media/data-box-deploy-export-copy-data/nfs-client-access-2.png)

2. Győződjön meg arról, hogy a Linux gazdagépen az NFS-ügyfél [támogatott verziója](data-box-system-requirements.md) van telepítve. Használja a Linux-disztribúciónak megfelelő verziót. 

3. Az NFS-ügyfél telepítését követően az alábbi paranccsal csatlakoztathatja a Data Box-eszközön található NFS-megosztást:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    Az alábbi példa bemutatja, hogyan kell NFS-en keresztüli kapcsolódni egy Data Box-megosztáshoz. A Data Box-eszköz IP-címe `10.161.23.130`, a megosztás (`Mystoracct_Blob`) az ubuntuVM virtuális géphez csatlakozik, a csatlakozási pont pedig a következő: `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`
    
    Mac-ügyfelek esetén a következő módon kell hozzáadnia egy további lehetőséget: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **Mindig hozzon létre egy mappát azokhoz a fájlokhoz, amelyeket másolni szeretne a megosztás alatt, majd másolja a fájlokat a létrehozott mappába**. A blokkblob- és lapblobmegosztások alatt létrehozott mappa azt a tárolót jelöli, amelybe a rendszer feltölti az adatokat blobokként. Nem másolhat fájlokat közvetlenül a tárfiók *gyökér*mappájába.

## <a name="copy-data-from-data-box"></a>Adatok másolása a Data Boxról

A Data Box-megosztásokhoz történő csatlakozás után a következő lépés az adatok másolása.

[!INCLUDE [data-box-export-review-logs](../../includes/data-box-export-review-logs.md)]

 Most már megkezdheti az Adatmásolást. Linux rendszerű gazdagép esetében használjon egy, a Robocopyhoz hasonló másolási segédprogramot. Ilyen például az [rsync](https://rsync.samba.org/), a [FreeFileSync](https://www.freefilesync.org/), a [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) vagy az [Ultracopier](https://ultracopier.first-world.info/).  

A `cp` parancs az egyik legjobb választás a könyvtárak másolására. A parancs használatáról [a cp tájékoztató oldalain](http://man7.org/linux/man-pages/man1/cp.1.html) talál további információt.

Amennyiben az rsyncet használja többszálas másoláshoz, a következő irányelveket kell betartania:

* Telepítse a **CIFS Utils** vagy az **NFS Utils** csomagot, attól függően, hogy a Linux-ügyfél milyen fájlrendszert használ.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

* Az **rsync** és a **Parallel** telepítése (a Linux elosztott verziótól függően változhat).

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

A másolás befejezése után nyissa meg az **Irányítópultot**, majd ellenőrizze, hogy az eszközén lévő felhasznált és szabad tárhely mennyiségét.

Ezután elküldheti az Azure Data Boxot a Microsoftnak.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
>
> * Előfeltételek
> * Csatlakozás a Data Boxhoz
> * Adatok másolása a Data Boxról

Folytassa a következő oktatóanyaggal, amelyben megismerheti, hogyan küldheti vissza a Data Boxot a Microsoftnak.

> [!div class="nextstepaction"]
> [Azure Data Box elküldése a Microsoftnak](./data-box-deploy-export-picked-up.md)
