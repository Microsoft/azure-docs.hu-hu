---
title: 'Oktatóanyag: másolás virtuális merevlemezekről a felügyelt lemezekre'
titleSuffix: Azure Data Box
description: Útmutató a virtuális merevlemezekről származó adatok másolásához a helyszíni virtuálisgép-munkaterhelésekről a Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 10/20/2019
ms.author: alkohli
ms.openlocfilehash: 1394cf6511a65a0e406e51229953e8666d4d4d8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94337678"
---
# <a name="tutorial-use-data-box-to-import-data-as-managed-disks-in-azure"></a>Oktatóanyag: az adatData Box használata felügyelt lemezként az Azure-ban

Ez az oktatóanyag leírja, hogyan telepítheti át a helyszíni virtuális merevlemezeket a felügyelt lemezekre az Azure-ban a Azure Data Box használatával. A helyszíni virtuális gépekről származó virtuális merevlemezeket a rendszer átmásolja Data Boxba, és az Azure-ba felügyelt lemezként feltölti őket. Ezek a felügyelt lemezek ezután az Azure-beli virtuális gépekhez csatlakoztathatók.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek áttekintése
> * Csatlakozás a Data Boxhoz
> * Adatok másolása a Data Boxra

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Az [ Az Azure Data Box beállítása](data-box-deploy-set-up.md) című oktatóanyagot.
2. Megkapta a Data Boxot, és a portálon a megrendelés **Kézbesítve** állapotú.
3. Nagy sebességű hálózathoz csatlakozik. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. Ha egy 10 GbE-kapcsolat nem érhető el, használjon 1 GbE adatkapcsolatot, de a másolási sebességet is érinti.
4. Áttekintette a következőket:

    - Támogatott [felügyelt lemezek mérete az Azure-objektumok méretének korlátaiban](data-box-limits.md#azure-object-size-limits).
    - [Bevezetés az Azure Managed Disks](../virtual-machines/managed-disks-overview.md)használatába. 

5. Megőrizte a forrásadatok másolatát, amíg meg nem erősítette, hogy a Data Box átvitte adatait az Azure Storage szolgáltatásba.

## <a name="connect-to-data-box"></a>Csatlakozás a Data Boxhoz

A megadott erőforráscsoportok alapján Data Box létrehoz egy megosztást az egyes társított erőforráscsoportok számára. Például ha `mydbmdrg1` `mydbmdrg2` a és a rendelés elhelyezésekor jött létre, a következő megosztások jönnek létre:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

Az egyes megosztásokon belül a következő három mappa jön létre, amelyek a Storage-fiókban lévő tárolóknak felelnek meg.

- Prémium SSD
- Standard HDD
- Standard SSD

A következő táblázat a Data Box megosztások UNC elérési útját mutatja be.
 
|        Kapcsolati protokoll           |             A megosztás UNC elérési útja                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<PremiumSSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<StandardHDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<StandardSSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<PremiumSSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<StandardHDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<StandardSSD>/file3.vhd` |

Attól függően, hogy az SMB vagy az NFS használatával csatlakozik-e Data Box megosztásokhoz, a kapcsolódás lépései eltérőek.

> [!NOTE]
> A REST-kapcsolaton keresztüli kapcsolódás nem támogatott ehhez a szolgáltatáshoz.

### <a name="connect-to-data-box-via-smb"></a>Kapcsolódás Data Box SMB-n keresztül

Ha Windows Server rendszerű gazdagépet használ, kövesse az alábbi lépéseket a Data Boxhoz történő csatlakozáshoz.

1. Az első lépés a hitelesítés elvégzése, majd a munkamenet elindítása. Lépjen a **Connect and copy** (Kapcsolódás és másolás) elemre. Kattintson a **hitelesítő adatok beolvasása** elemre az erőforráscsoporthoz társított megosztások hozzáférési hitelesítő adatainak beszerzéséhez. A hozzáférési hitelesítő adatokat a Azure Portalban található **eszköz adatainál** is lekérheti.

    > [!NOTE]
    > A felügyelt lemezek összes megosztásának hitelesítő adatai azonosak.

    ![Kapcsolat és másolás, megosztási hitelesítő adatok beolvasása](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. A **hozzáférés megosztása és adatok másolása** párbeszédpanelen másolja a megosztáshoz tartozó **felhasználónevet** és **jelszót** . Kattintson az **OK** gombra.
    
    ![Kapcsolat és másolás, megosztási hitelesítő adatok másolása](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. Ha el szeretné érni az erőforráshoz társított megosztásokat (a következő példában szereplő *mydbmdrg1* ), nyisson meg egy parancssori ablakot. A parancssorba írja be a következőt:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Az UNC-megosztás elérési útjai ebben a példában a következők:

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. Ha a rendszer kéri, adja meg a megosztás jelszavát. A következő példa bemutatja, hogyan kell csatlakozni egy megosztáshoz a fenti parancs használatával.

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for 'mdisk' to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

5. Nyomja le a Windows + R billentyűkombinációt. A **Futtatás** ablakban adja meg a következőt: `\\<device IP address>\<ShareName>`. Kattintson az **OK** gombra a Fájlkezelő megnyitásához.
    
    ![Kapcsolódás a megosztáshoz a Fájlkezelővel](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    Ekkor a következő előre létrehozott mappákat kell látnia az egyes megosztásokon belül.
    
    ![Kapcsolódás megosztáshoz a Fájlkezelőben, a megosztás mappáiban](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-via-nfs"></a>Kapcsolódás Data Box NFS-en keresztül

Amennyiben Linux rendszerű gazdagépet használ, a következő módon konfigurálhatja a Data Boxot, hogy hozzáférést biztosítson az NFS-ügyelek számára.

1. Adja meg azon ügyfelek IP-címeit, akik hozzáférhetnek a megosztáshoz. A helyi webes KEZELŐFELÜLETen lépjen a **Kapcsolódás és másolás** lapra. Az **NFS settings** (NFS-beállítások) pontban kattintson az **NFS client access** (NFS-ügyfélhozzáférés) lehetőségre.

    ![NFS-ügyfél-hozzáférés konfigurálása](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. Adja meg az NFS-ügynök IP-címét, és kattintson az **Add** (Hozzáadás) gombra. Ezt a lépést megismételve további NFS-ügyfeleket is konfigurálhat. Kattintson az **OK** gombra.

    ![NFS-ügyfél IP-címének konfigurálása](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Győződjön meg arról, hogy a Linux gazdagépen az NFS-ügyfél [támogatott verziója](data-box-system-requirements.md) van telepítve. Használja a Linux-disztribúciónak megfelelő verziót.

3. Az NFS-ügyfél telepítését követően az alábbi paranccsal csatlakoztathatja a Data Box-eszközön található NFS-megosztást:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    Az alábbi példa bemutatja, hogyan kell NFS-en keresztüli kapcsolódni egy Data Box-megosztáshoz. A Data Box-eszköz IP-címe `169.254.250.200`, a megosztás (`mydbmdrg1_MDisk`) az ubuntuVM virtuális géphez csatlakozik, a csatlakozási pont pedig a következő: `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box"></a>Adatok másolása a Data Boxra

Miután csatlakozott az adatkiszolgálóhoz, a következő lépés az Adatmásolás. A VHD-fájlt a rendszer átmásolja az átmeneti Storage-fiókba az oldal blob néven. A rendszer ezután a blobot egy felügyelt lemezre konvertálja, és egy erőforráscsoporthoz helyezi át.

Az Adatmásolás megkezdése előtt tekintse át a következő szempontokat:

- Mindig másolja át a VHD-kat az egyik előre létrehozott mappába. Ha a virtuális merevlemezeket ezen mappákon kívül vagy egy Ön által létrehozott mappában másolja, a VHD-k az Azure Storage-fiókba lesznek feltöltve, és nem felügyelt lemezként.
- Csak a rögzített VHD-ket lehet feltölteni felügyelt lemezek létrehozásához. A VHDX-fájlok, illetve a dinamikus és a különbséglemezek VHD-k nem támogatottak.
- Az összes előlétrehozott mappában csak egy felügyelt lemez tartozhat egy adott névvel. Ez azt is jelenti, hogy az előre létrehozott mappákba feltöltött VHD-knak egyedi névvel kell rendelkezniük. Győződjön meg arról, hogy a megadott név nem egyezik meg egy erőforráscsoport meglévő felügyelt lemez nevével sem.
- Tekintse át a felügyelt lemezek korlátozásait az [Azure-objektumok méretének korlátaiban](data-box-limits.md#azure-object-size-limits).

Attól függően, hogy SMB-n vagy NFS-en keresztül csatlakozik, használhatja a következőt:

- [Adatok másolása SMB-n keresztül](data-box-deploy-copy-data.md#copy-data-to-data-box)
- [Adatok másolása NFS-en keresztül](data-box-deploy-copy-data-via-nfs.md#copy-data-to-data-box)

Várjon, amíg a másolási feladatok befejeződik. Győződjön meg arról, hogy a másolási feladatok nem voltak hibák, mielőtt a következő lépéshez ugorjon.

![Nincsenek hibák * * a kapcsolat és a másolás * * oldal](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

Ha a másolási folyamat során hibák léptek fel, töltse le a naplókat a **csatlakozás és másolás** lapról.

- Ha olyan fájlt másolt, amely nem 512 bájtra van igazítva, a fájl nem töltődik fel lapozófájlként az átmeneti tárolási fiókba. Hibaüzenet jelenik meg a naplókban. Távolítsa el a fájlt, és másolja a 512 bájtra igazított fájlt.

- Ha egy VHDX másolt (ezek a fájlok nem támogatottak) hosszú névvel, hibaüzenet jelenik meg a naplókban.

    ![Hiba történt a naplókból a * * kapcsolat és a másolás * * oldalról](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    Hárítsa el a hibákat, mielőtt továbblép a következő lépésre.

Az adatok integritásának biztosítása érdekében az ellenőrzőösszeg kiszámítására beágyazva, az adatok másolása közben kerül sor. A másolás befejezése után ellenőrizze, hogy mekkora a felhasznált és a szabad tárhely az eszközén.
    
![A szabad és a felhasznált tárhely ellenőrzése az irányítópulton](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

A másolási feladatok befejezése után **szállításra való előkészítés** léphet.


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
> * Előfeltételek áttekintése
> * Csatlakozás a Data Boxhoz
> * Adatok másolása a Data Boxra


Folytassa a következő oktatóanyaggal, amelyben megismerheti, hogyan küldheti vissza a Data Boxot a Microsoftnak.

> [!div class="nextstepaction"]
> [Azure Data Box elküldése a Microsoftnak](./data-box-deploy-picked-up.md)