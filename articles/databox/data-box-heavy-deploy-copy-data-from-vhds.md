---
title: 'Oktatóanyag: másolás virtuális merevlemezekről a felügyelt lemezekre'
titleSuffix: Azure Data Box Heavy
description: Útmutató a virtuális merevlemezekről származó adatok másolásához a helyszíni virtuálisgép-munkaterhelésekről a Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 335b564225bc2b7a4c2217c1d912f952239ecf24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92127199"
---
# <a name="tutorial-use-data-box-heavy-to-import-data-as-managed-disks-in-azure"></a>Oktatóanyag: az adatData Box Heavy használata felügyelt lemezként az Azure-ban

Ez az oktatóanyag leírja, hogyan telepítheti át a helyszíni virtuális merevlemezeket a felügyelt lemezekre az Azure-ban a Azure Data Box Heavy használatával. A helyszíni virtuális gépekről származó virtuális merevlemezeket a rendszer átmásolja Data Box Heavyba, és az Azure-ba felügyelt lemezként feltölti őket. Ezek a felügyelt lemezek ezután az Azure-beli virtuális gépekhez csatlakoztathatók.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Előfeltételek áttekintése
> * Csatlakozás a Data Box Heavyhez
> * Adatok másolása a Data Box Heavyre


## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Az [ Az Azure Data Box Heavy beállítása](data-box-heavy-deploy-set-up.md) című oktatóanyagot.
2. Megkapta a Data Box Heavyt, és a portálon a megrendelés **Kézbesítve** állapotú.
3. Nagy sebességű hálózathoz csatlakozik. A legnagyobb másolási sebesség érdekében két 40 GbE sebességű kapcsolat (csomópontonként egy) használható egyidejűleg. Ha nem rendelkezik 40 GbE sebességű kapcsolattal, javasoljuk, hogy legalább két 10 GbE sebességű kapcsolattal (csomópontonként eggyel) rendelkezzen. 
4. Áttekintette a következőket:

    - Támogatott [felügyelt lemezek mérete az Azure-objektumok méretének korlátaiban](data-box-heavy-limits.md#azure-object-size-limits).
    - [Bevezetés az Azure Managed Disks](../virtual-machines/managed-disks-overview.md)használatába. 

## <a name="connect-to-data-box-heavy"></a>Csatlakozás a Data Box Heavyhez

A megadott erőforráscsoportok alapján a Data Box Heavy minden egyes társított erőforráscsoport számára létrehoz egy megosztást. Például ha `mydbmdrg1` `mydbmdrg2` a és a rendelés elhelyezésekor jött létre, a következő megosztások jönnek létre:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

Az egyes megosztásokon belül a következő három mappa jön létre, amelyek a Storage-fiókban lévő tárolóknak felelnek meg.

- Prémium SSD
- Standard HDD
- Standard SSD

A következő táblázat a Data Box Heavy megosztások UNC elérési útját mutatja be.
 
|        Kapcsolati protokoll           |             A megosztás UNC elérési útja                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Premium SSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard HDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard SSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<Premium SSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard HDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard SSD>/file3.vhd` |

Attól függően, hogy az SMB vagy az NFS használatával csatlakozik-e Data Box Heavy megosztásokhoz, a kapcsolódás lépései eltérőek.

> [!NOTE]
> - A REST-kapcsolaton keresztüli kapcsolódás nem támogatott ehhez a szolgáltatáshoz.
> - A Data Box Heavy második csomópontjára való kapcsolódáshoz ismételje meg a kapcsolódási utasításokat.

### <a name="connect-to-data-box-heavy-via-smb"></a>Kapcsolódás Data Box Heavy SMB-n keresztül

Ha Windows Server rendszerű gazdagépet használ, kövesse az alábbi lépéseket a Data Box Heavyhez történő csatlakozáshoz.

1. Az első lépés a hitelesítés elvégzése, majd a munkamenet elindítása. Lépjen a **Connect and copy** (Kapcsolódás és másolás) elemre. Kattintson a **hitelesítő adatok beolvasása** elemre az erőforráscsoporthoz társított megosztások hozzáférési hitelesítő adatainak beszerzéséhez. A hozzáférési hitelesítő adatokat a Azure Portalban található **eszköz adatainál** is lekérheti.

    > [!NOTE]
    > A felügyelt lemezek összes megosztásának hitelesítő adatai azonosak.

    ![Megosztások hitelesítő adatainak beszerzése](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. A hozzáférés megosztása és adatok másolása párbeszédpanelen másolja a megosztáshoz tartozó **felhasználónevet** és **jelszót** . Kattintson az **OK** gombra.
    
    ![Megosztások hitelesítő adatainak beszerzése 2](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. Ha el szeretné érni az erőforráshoz társított megosztásokat (a következő példában szereplő *mydbmdrg1* ), nyisson meg egy parancssori ablakot. A parancssorba írja be a következőt:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Az UNC-megosztás elérési útjai ebben a példában a következők:

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. Ha a rendszer kéri, adja meg a megosztás jelszavát. A következő példa bemutatja, hogyan kell csatlakozni egy megosztáshoz a fenti parancs használatával.

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for ‘mdisk’ to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

4. Nyomja le a Windows + R billentyűkombinációt. A **Futtatás** ablakban adja meg a következőt: `\\<device IP address>\<ShareName>`. Kattintson az **OK** gombra a Fájlkezelő megnyitásához.
    
    ![Kapcsolódás a megosztáshoz a Fájlkezelővel](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    Ekkor a következő előre létrehozott mappákat kell látnia az egyes megosztásokon belül.
    
    ![Kapcsolódás a megosztáshoz a Fájlkezelővel 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-heavy-via-nfs"></a>Kapcsolódás Data Box Heavy NFS-en keresztül

Ha Linux rendszerű gazdagépet használ, a következő lépésekkel konfigurálhatja az eszközt az NFS-ügyfelek elérésének engedélyezéséhez.

1. Adja meg azon ügyfelek IP-címeit, akik hozzáférhetnek a megosztáshoz. A helyi webes felületen lépjen a **Connect and copy** (Kapcsolódás és másolás) lapra. Az **NFS settings** (NFS-beállítások) pontban kattintson az **NFS client access** (NFS-ügyfélhozzáférés) lehetőségre.

    ![NFS-ügyfél-hozzáférés konfigurálása](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. Adja meg az NFS-ügynök IP-címét, és kattintson az **Add** (Hozzáadás) gombra. Ezt a lépést megismételve további NFS-ügyfeleket is konfigurálhat. Kattintson az **OK** gombra.

    ![NFS-ügyfélhozzáférés konfigurálása 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Győződjön meg arról, hogy a Linux gazdagépen az NFS-ügyfél [támogatott verziója](data-box-system-requirements.md) van telepítve. Használja a Linux-disztribúciónak megfelelő verziót.

3. Miután telepítette az NFS-ügyfelet, az alábbi paranccsal csatlakoztathatja az NFS-megosztást az eszközön:

    `sudo mount <Data Box or Data Box Heavy IP>:/<NFS share on Data Box or Data Box Heavy device> <Path to the folder on local Linux computer>`

    Az alábbi példa bemutatja, hogyan csatlakozhat NFS-en keresztül egy Data Box vagy Data Box Heavy-megosztáshoz. A Data Box vagy Data Box Heavy eszköz IP-címe `169.254.250.200` , a megosztás a `mydbmdrg1_MDisk` ubuntuVM, a csatlakoztatási ponthoz van csatlakoztatva `/home/databoxubuntuhost/databox` .

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box-heavy"></a>Adatok másolása a Data Box Heavyre

Miután csatlakozott az adatkiszolgálóhoz, a következő lépés az Adatmásolás. A VHD-fájlt a rendszer átmásolja az átmeneti Storage-fiókba az oldal blob néven. A rendszer ezután a blobot egy felügyelt lemezre konvertálja, és egy erőforráscsoporthoz helyezi át.

Az Adatmásolás megkezdése előtt tekintse át a következő szempontokat:

- Mindig másolja át a VHD-kat az egyik előre létrehozott mappába. Ha a virtuális merevlemezeket ezen mappákon kívül vagy egy Ön által létrehozott mappában másolja, a VHD-k az Azure Storage-fiókba lesznek feltöltve, és nem felügyelt lemezként.
- Csak a rögzített VHD-ket lehet feltölteni felügyelt lemezek létrehozásához. A VHDX-fájlok, illetve a dinamikus és a különbséglemezek VHD-k nem támogatottak.
- Az összes előlétrehozott mappában csak egy felügyelt lemez tartozhat egy adott névvel. Ez azt is jelenti, hogy az előre létrehozott mappákba feltöltött VHD-knak egyedi névvel kell rendelkezniük. Győződjön meg arról, hogy a megadott név nem egyezik meg egy erőforráscsoport meglévő felügyelt lemez nevével sem.
- Tekintse át a felügyelt lemezek korlátozásait az [Azure-objektumok méretének korlátaiban](data-box-heavy-limits.md#azure-object-size-limits).

Attól függően, hogy SMB-n vagy NFS-en keresztül csatlakozik, használhatja a következőt:

- [Adatok másolása SMB-n keresztül](data-box-heavy-deploy-copy-data.md#copy-data-to-data-box-heavy)
- [Adatok másolása NFS-en keresztül](data-box-heavy-deploy-copy-data-via-nfs.md#copy-data-to-data-box-heavy)

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


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box Heavyvel kapcsolatos témakörökkel ismerkedett meg, például a következőkkel:

> [!div class="checklist"]
> * Előfeltételek áttekintése
> * Csatlakozás a Data Box Heavyhez
> * Adatok másolása a Data Box Heavyre


Folytassa a következő oktatóanyaggal, amelyben megismerheti, hogyan küldheti vissza a Data Box Heavyt a Microsoftnak.

> [!div class="nextstepaction"]
> [Az Azure Data Box Heavy visszaküldése a Microsoftnak](./data-box-heavy-deploy-picked-up.md)