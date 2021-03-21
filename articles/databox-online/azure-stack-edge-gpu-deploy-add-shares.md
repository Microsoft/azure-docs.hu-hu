---
title: Oktatóanyag az adatok megosztásokhoz való továbbításához Azure Stack Edge Pro GPU-val | Microsoft Docs
description: Megtudhatja, hogyan adhat hozzá és csatlakozhat a megosztásokhoz Azure Stack Edge Pro GPU-eszközön.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/22/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 26a2be6d2752393362b6b4f9a666dd0b74e64321
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102663314"
---
# <a name="tutorial-transfer-data-via-shares-with-azure-stack-edge-pro-gpu"></a>Oktatóanyag: adatok átvitele megosztásokon keresztül Azure Stack Edge Pro GPU-val

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez az oktatóanyag azt ismerteti, hogyan adhat hozzá és csatlakozhat a Azure Stack Edge Pro-eszközön található megosztásokhoz. A megosztások hozzáadása után Azure Stack Edge Pro átviheti az adatátvitelt az Azure-ba.

A folyamat elvégzése körülbelül 10 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Megosztás hozzáadása
> * Csatlakozás a megosztáshoz

## <a name="prerequisites"></a>Előfeltételek

Mielőtt megkezdené a megosztások hozzáadását Azure Stack Edge Pro-hoz, ügyeljen rá, hogy:

* Telepítette a fizikai eszközt a [Azure stack Edge Pro telepítése](azure-stack-edge-gpu-deploy-install.md)című témakörben leírtak szerint.

* A fizikai eszközt aktiválta a [Azure stack Edge Pro aktiválása](azure-stack-edge-gpu-deploy-activate.md)című témakörben leírtak szerint.

## <a name="add-a-share"></a>Megosztás hozzáadása

Megosztás létrehozásához hajtsa végre a következő eljárást:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a Azure stack Edge-erőforrást, majd lépjen az **Áttekintés** elemre. Az eszköznek online állapotban kell lennie. Válassza a **felhőalapú tároló-átjáró** elemet.

   ![Eszköz online](./media/azure-stack-edge-gpu-deploy-add-shares/device-online-1.png)

2. Válassza a **+ megosztás hozzáadása** lehetőséget az eszköz parancssáv-sávján.

   ![Megosztás hozzáadása](./media/azure-stack-edge-gpu-deploy-add-shares/select-add-share-1.png)

3. A **megosztás hozzáadása** panelen hajtsa végre az alábbi lépéseket:

    a. A **név** mezőben adjon meg egy egyedi nevet a megosztáshoz.  
    A megosztás neve csak betűket, számokat és kötőjeleket tartalmazhat. 3 – 63 karakter hosszúnak kell lennie, és betűvel vagy számmal kell kezdődnie. A kötőjelek előtt betűnek vagy számnak kell lennie, és utána kell következnie.
    
    b. Válasszon egy **típust** a megosztáshoz.  
    A típus lehet **SMB** vagy **NFS**, és az SMB az alapértelmezett. Ez a szokásos típus Windows-ügyfelekhez, míg az NFS a Linux rendszerű ügyfelekhez használatos.  
    Attól függően, hogy az SMB-vagy NFS-megosztásokat választja, a többi lehetőség némileg eltérő lehet. 

    c. Adjon meg egy tárfiókot, ahol a megosztás tárolva lesz.

    d. A **tárolási szolgáltatás** legördülő listában válassza a blob, **oldal blob** vagy **fájlok** **letiltása** lehetőséget.  
    A kiválasztott szolgáltatás típusa attól függ, hogy milyen formátumban szeretné használni az Azure-ban az adatok használatát. Ebben a példában, mert az adatblokk-blobokat az Azure-ban szeretnénk tárolni, válassza a **blob letiltása** lehetőséget. Ha kijelöli az **oldal blobot**, ügyeljen arra, hogy az adatai 512 bájtra legyenek igazítva. A VHDX például mindig 512 bájtos igazítású.

   > [!IMPORTANT]
   > Győződjön meg arról, hogy a használt Azure Storage-fiók nem rendelkezik módosíthatatlansági-házirendekkel, ha Azure Stack Edge Pro vagy Data Box Gateway eszközzel használja. További információ: [módosíthatatlansági-szabályzatok beállítása és kezelése a blob Storage-hoz](../storage/blobs/storage-blob-immutability-policies-manage.md).

    e. Hozzon létre egy új BLOB-tárolót, vagy használjon egy meglévőt a legördülő listából. BLOB-tároló létrehozásakor adja meg a tároló nevét. A rendszer létrehoz egy tárolót a tárfiókban az újonnan létrehozott megosztás nevével, ha még nem létezik ilyen.
   
    f. Attól függően, hogy létrehozott-e SMB-megosztást vagy NFS-megosztást, hajtsa végre a következő lépések egyikét: 
     
    - **SMB-megosztás**: **az összes jogosultság helyi felhasználó** területen válassza az **új létrehozása** vagy a **meglévő használata** lehetőséget. Ha új helyi felhasználót hoz létre, adjon meg egy felhasználónevet és egy jelszót, majd erősítse meg a jelszót. Ez a művelet engedélyeket rendel a helyi felhasználóhoz. A megosztási szintű engedélyek módosítása jelenleg nem támogatott. Ha bejelöli a **csak olvasási műveletek engedélyezése** jelölőnégyzetet ehhez a megosztási adatokhoz, megadhat csak olvasási jogosultsággal rendelkező felhasználókat.
    
        ![SMB-megosztás hozzáadása](./media/azure-stack-edge-gpu-deploy-add-shares/add-share-smb-1.png)
   
    - **NFS-megosztás**: adja meg a megosztást elérő engedélyezett ügyfelek IP-címeit.

        ![NFS-megosztás hozzáadása](./media/azure-stack-edge-gpu-deploy-add-shares/add-share-nfs-1.png)
   
4. A megosztás létrehozásához válassza a **Létrehozás** lehetőséget.
    
    Értesítést kap arról, hogy a megosztás létrehozása folyamatban van. Miután létrehozta a megosztást a megadott beállításokkal, a **megosztások** csempe frissül, hogy tükrözze az új megosztást.
    

## <a name="connect-to-the-share"></a>Csatlakozás a megosztáshoz

Most már csatlakozhat egy vagy több, az utolsó lépésben létrehozott megosztáshoz. Attól függően, hogy van-e SMB vagy NFS-megosztás, a lépések változhatnak.

Első lépésként győződjön meg arról, hogy az eszköz neve feloldható az SMB-vagy NFS-megosztás használata esetén.

### <a name="modify-host-file-for-name-resolution"></a>A gazdagép fájljának módosítása névfeloldáshoz

Ekkor hozzáadja az eszköz IP-címét és az eszköz helyi webes FELÜLETén megadott felhasználóbarát nevet a következőhöz:

- A gazdagép fájlja az ügyfélen, vagy
- A gazdagép fájlja a DNS-kiszolgálón

> [!IMPORTANT]
> Azt javasoljuk, hogy módosítsa a gazdagép fájlját a DNS-kiszolgálón az eszköz nevének feloldásához.

Az eszközhöz való kapcsolódáshoz használt Windows-ügyfélen hajtsa végre a következő lépéseket:

1. Indítsa el a **jegyzettömböt** rendszergazdaként, majd nyissa meg a következő helyen található **hosts** fájlt: `C:\Windows\System32\Drivers\etc` .

    ![Windows Intéző – fájl](media/azure-stack-edge-gpu-deploy-add-shares/client-hosts-file-1.png)


2. Adja hozzá a következő bejegyzést a **hosts** -fájlhoz az eszköz megfelelő értékei helyett: 

    ```
    <Device IP>   <device friendly name>
    ``` 
    Az eszköz IP-címét a **hálózatról** , az eszköz rövid nevét pedig a helyi webes felhasználói felületen található **eszköz** oldaláról szerezheti be. A Hosts fájl következő képernyőképe a bejegyzést jeleníti meg:

    ![A Windows Intéző a 2. fájlt tárolja](media/azure-stack-edge-gpu-deploy-add-shares/client-hosts-file-2.png)

### <a name="connect-to-an-smb-share"></a>Csatlakozás SMB-megosztáshoz

A Azure Stack Edge Pro-eszközhöz csatlakoztatott Windows Server-ügyfélen a következő parancsok megadásával csatlakozhat egy SMB-megosztáshoz:


1. Egy parancsablakban írja be a következőt: 

    `net use \\<Device name>\<share name>  /u:<user name for the share>`

    > [!NOTE]
    > Csak az eszköz nevével csatlakozhat egy SMB-megosztáshoz, és nem az eszköz IP-címén keresztül.

2. Ha a rendszer kéri, adja meg a megosztás jelszavát.  
   Az alábbiakban látható egy példa a parancs kimenetére.

    ```powershell
    Microsoft Windows [Version 10.0.18363.476)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\AzureStackEdgeUser>net use \\myasetest1\myasesmbshare1 /u:aseuser
    Enter the password for 'aseuser' to connect to 'myasetest1':
    The command completed successfully.
    
    C: \Users\AzureStackEdgeUser>
    ```   

3. A billentyűzeten válassza a Windows + R lehetőséget.

4. A **Futtatás** ablakban adja meg a `\\<device name>` elemet, majd kattintson az **OK gombra**.  

    ![Windows Futtatás párbeszédpanel](media/azure-stack-edge-gpu-deploy-add-shares/run-window-1.png)

   Megnyílik a Fájlkezelő. A létrehozott megosztásoknak meg kell jelenniük mappaként. A Fájlkezelőben duplán kattintva nyisson meg egy megosztást (mappát) a tartalma megtekintéséhez.
 
    ![Csatlakozás SMB-megosztáshoz](./media/azure-stack-edge-gpu-deploy-add-shares/file-explorer-smbshare-1.png)

    Az adatokat azok előállításakor a rendszer a megosztásba írja, majd az eszköz továbbítja az adatokat a felhőbe.

### <a name="connect-to-an-nfs-share"></a>Csatlakozás NFS-megosztáshoz

Az Azure Stack Edge Pro-eszközhöz csatlakoztatott Linux-ügyfélen hajtsa végre a következő eljárást:

1. Győződjön meg arról, hogy az ügyfélen telepítve van az NFSv4-ügyfél. Ha nincs, az NFS-ügyfél telepítéséhez használja az alábbi parancsot:

   `sudo apt-get install nfs-common`

    További információért látogasson el a [nfsv4 névleképezője-ügyfél telepítése](https://help.ubuntu.com/community/NFSv4Howto)elemre.

2. Az NFS-ügyfél telepítése után csatlakoztassa a Azure Stack Edge Pro-eszközön létrehozott NFS-megosztást a következő parancs használatával:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS share on device> /home/username/<Folder on local Linux computer>`

    Az eszköz IP-címe a helyi webes felhasználói felület **hálózat** lapjáról kérhető le.

    > [!IMPORTANT]
    > Ha a `sync` csatlakoztatási megosztások növelik a nagy méretű fájlok átvitelének sebességét, a lehetőség használata.
    > A megosztás csatlakoztatása előtt győződjön meg arról, hogy a helyi számítógépen csatolásiként szolgáló könyvtárak már létre lettek hozva. Ezek a címtárak nem tartalmazhatnak fájlokat vagy almappákat.

    Az alábbi példa bemutatja, hogyan csatlakozhat NFS-en keresztül egy megosztáshoz az Azure Stack Edge Pro-eszközön. Az eszköz IP-címe: `10.10.10.60`. A `mylinuxshare2` megosztás az ubuntuVM virtuális géphez csatlakozik. A megosztás csatlakoztatási pontja: `/home/azurestackedgeubuntuhost/edge`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/azurestackedgeubuntuhost/Edge`

> [!NOTE] 
> A következő kikötések alkalmazhatók erre a kiadásra:
> - Miután létrehozta a fájlt a megosztásban, a fájl átnevezése nem támogatott. 
> - Egy fájl megosztásból való törlése nem törli a bejegyzést az Azure Storage-fiókban.
> - Ha az `rsync` NFS-en keresztül történő másolásra használja, használja a `--inplace` jelzőt. 

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerte a következő Azure Stack Edge Pro-témaköröket:

> [!div class="checklist"]
> * Megosztás hozzáadása
> * Csatlakozás megosztáshoz

Ha szeretné megtudni, hogyan alakíthatja át adatait Azure Stack Edge Pro használatával, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Az adatátalakítás Azure Stack Edge Pro-val](./azure-stack-edge-j-series-deploy-configure-compute.md)