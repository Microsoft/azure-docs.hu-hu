---
title: Adatok átvitele az Azure Data Box Gatewayjel | Microsoft Docs
description: Megtudhatja, hogyan adhat hozzá és csatlakozhat a Azure Data Box Gateway lévő megosztásokhoz, és a Data Box Gateway eszköz az Azure-ba viheti át az adatátvitelt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 0c0ef6157ebf70c896fbac5ff692246e4fad2c14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98937199"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway"></a>Oktatóanyag: adatok átvitele Azure Data Box Gateway


## <a name="introduction"></a>Bevezetés

Ez a cikk azt ismerteti, hogyan lehet hozzáadni és csatlakozni a Data Box Gateway-megosztásokhoz. A megosztások hozzáadása után az eszköz átviheti az adatokkal az Azure-ba.

A folyamat elvégzése körülbelül 10 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Megosztás hozzáadása
> * Csatlakozás megosztáshoz

## <a name="prerequisites"></a>Előfeltételek

Mielőtt megosztásokat ad a Data Box Gatewayhez, ellenőrizze az alábbiakat:

- Kiépített egy virtuális eszközt, és azt részletesen csatlakoztatta a [Hyper-V Data Box Gateway](data-box-gateway-deploy-provision-hyperv.md) üzembe helyezése vagy [a VMware-Data Box Gateway kiépítése](data-box-gateway-deploy-provision-vmware.md)során.

- Aktiválta a [Azure Data Box Gateway csatlakoztatása és aktiválása](data-box-gateway-deploy-connect-setup-activate.md)című témakörben leírt virtuális eszközt.

- Az eszköz készen áll a megosztások létrehozására és az adatátvitelre.

## <a name="add-a-share"></a>Megosztás hozzáadása

A megosztás létrehozásához hajtsa végre a következő eljárást:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a Data Box Gateway erőforrást, majd kattintson az **Áttekintés** gombra. Az eszköznek online állapotban kell lennie. Válassza a **+ megosztás hozzáadása** lehetőséget az eszköz parancssáv-sávján.
   
   ![Megosztás hozzáadása](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. A **megosztás hozzáadása** területen hajtsa végre a következő eljárást:

    1. Adjon egy egyedi nevet a megosztásnak. A megosztások neve csak kisbetűket, számokat és kötőjelet tartalmazhat. A megosztás nevének 3–63 karakter hosszúnak kell lennie, és betűvel vagy számmal kell kezdődnie. A kötőjelek előtt és után csak nem kötőjel karakter állhat.
    
    2. Válasszon egy **típust** a megosztáshoz. A típus SMB vagy NFS lehet. Az alapértelmezett érték az SMB. Ez a szokásos típus Windows-ügyfelekhez, míg az NFS a Linux rendszerű ügyfelekhez használatos. Attól függően, hogy az SMB vagy az NFS típust választja, a megjelenő beállítások kis mértékben eltérőek.

    3. Adjon meg egy tárfiókot, ahol a megosztás tárolva lesz. A rendszer létrehoz egy tárolót a tárfiókban az újonnan létrehozott megosztás nevével, ha még nem létezik ilyen. Ha létezik, akkor a rendszer a meglévő tárolót használja.
       > [!IMPORTANT]
       > Győződjön meg arról, hogy a használt Azure Storage-fiók nem rendelkezik módosíthatatlansági-házirendekkel, ha Data Box Gateway eszközzel használja. További információ: [módosíthatatlansági-szabályzatok beállítása és kezelése a blob Storage-hoz](../storage/blobs/storage-blob-immutability-policies-manage.md).
    
    4. Válassza ki a **tárolási szolgáltatást** a blokkblobok, lapblobok vagy fájlok közül. A kiválasztott szolgáltatástípustól függ, hogy az Azure milyen formátumban tárolja az adatokat. Ebben az esetben például azt szeretnénk, hogy az adatok blokkblobokban legyenek tárolva az Azure-ban, ezért a Blokkblob lehetőséget választjuk. Ha a Lapblob lehetőséget választja, biztosítania kell az adatok 512 bájtos igazítását. A VHDX például mindig 512 bájtos igazítású.
   
    5. A következő lépés attól függ, hogy SMB- vagy NFS-megosztást hozunk-e létre.
     
    - **SMB-megosztás** – **az összes jogosultság helyi felhasználó** területen válassza az **új létrehozása** vagy a **meglévő használata** lehetőséget. Ha új helyi felhasználót hoz létre, adjon meg egy **felhasználónevet** és egy **jelszót**, majd **erősítse meg a jelszót**. Ez a művelet hozzárendeli az engedélyeket a helyi felhasználóhoz. A megosztási szintű engedélyek módosítása jelenleg nem támogatott.
    
        ![SMB-megosztás hozzáadása](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        Ha bejelöli a **csak olvasási műveletek engedélyezése** jelölőnégyzetet a megosztási adatokhoz, megadhat csak olvasási jogosultsággal rendelkező felhasználókat.
        
    - **NFS-megosztás** – adja meg azon engedélyezett ügyfelek IP-címeit, akik hozzáférhetnek a megosztáshoz.

        ![NFS-megosztás hozzáadása](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. A megosztás létrehozásához válassza a **Létrehozás** lehetőséget.
    
    Értesítést kap arról, hogy a megosztás létrehozása folyamatban van. Miután létrehozta a megosztást a megadott beállításokkal, a **megosztások** csempe frissül, hogy tükrözze az új megosztást.
    
    ![Frissített megosztások csempe](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Csatlakozás a megosztáshoz

Most már csatlakozhat egy vagy több, az utolsó lépésben létrehozott megosztáshoz. Attól függően, hogy van-e SMB vagy NFS-megosztás, a lépések változhatnak.

### <a name="connect-to-an-smb-share"></a>Csatlakozás SMB-megosztáshoz

A Data Box Gatewayhez csatlakoztatott Windows Server-ügyfélen csatlakozzon egy SMB-megosztáshoz a következő parancsok megadásával:


1. Egy parancsablakban írja be a következőt: 

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Ha a rendszer kéri, adja meg a megosztás jelszavát. Az alábbiakban látható egy példa a parancs kimenetére.

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2017 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60'  
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. A billentyűzeten válassza a Windows + R lehetőséget. 
3. A **Futtatás** ablakban adja meg a elemet, majd kattintson az `\\<device IP address>` **OK gombra**. Megnyílik a Fájlkezelő. A létrehozott megosztásoknak meg kell jelenniük mappaként. A Fájlkezelőben duplán kattintva nyisson meg egy megosztást (mappát) a tartalma megtekintéséhez.
 
    ![Csatlakozás SMB-megosztáshoz](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    Az adatokat azok előállításakor a rendszer a megosztásba írja, majd az eszköz továbbítja az adatokat a felhőbe.

### <a name="connect-to-an-nfs-share"></a>Csatlakozás NFS-megosztáshoz

Az eszközhöz csatlakoztatott Linux-ügyfélen hajtsa végre a következő eljárást:

1. Győződjön meg arról, hogy az ügyfélen telepítve van az NFSv4-ügyfél. Ha nincs, az NFS-ügyfél telepítéséhez használja az alábbi parancsot:

   `sudo apt-get install nfs-common`

    További információért látogasson el a [nfsv4 névleképezője-ügyfél telepítése](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client)elemre.

2. Az NFS-ügyfél telepítését követően az alábbi parancs használatával csatlakoztathatja a Data Box Gateway eszközön létrehozott NFS-fájlmegosztást:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    A csatlakoztatás előtt ellenőrizze, hogy a könyvtárak, amelyek helyi számítógépen csatlakozási pontként fognak szolgálni, már létrejöttek-e, illetve hogy nem tartalmaznak-e fájlokat vagy almappákat.

    Az alábbi példa bemutatja az NFS-en keresztüli csatlakozást egy Gateway eszközön található megosztáshoz. A virtuális eszköz IP-címe `10.10.10.60`, a `mylinuxshare2` megosztás az ubuntuVM virtuális géphez csatlakozik, a csatlakozási pont pedig a `/home/databoxubuntuhost/gateway`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> A következő kikötések alkalmazhatók erre a kiadásra:
> - Miután létrehozott egy fájlt a megosztásokban, a fájl átnevezése nem támogatott.
> - A fájlok megosztásból való törlése nem törli a bejegyzéseket a tárfiókból.
> - Ha az `rsync` Adatmásolást használja, akkor `rsync -a` a beállítás nem támogatott.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a Data Box Gatewayjel kapcsolatos alábbi témakörökkel ismerkedett meg:

> [!div class="checklist"]
> * Megosztás hozzáadása
> * Csatlakozás megosztáshoz


A következő oktatóanyag azt mutatja be, hogyan felügyelhető a Data Box Gateway.

> [!div class="nextstepaction"]
> [A Data Box Gateway felügyelete a helyi webes felhasználói felületen](https://aka.ms/dbg-docs)