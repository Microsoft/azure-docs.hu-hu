---
title: Oktatóanyag – fájlok visszaállítása egy virtuális gépre Azure Backup
description: Megtudhatja, hogyan végezhet fájlszintű helyreállítást egy Azure-beli virtuális gépen a Backup és a Recovery Services használatával.
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d977919b806be32b84001a9b91dc9e396fbd63ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96557909"
---
# <a name="restore-files-to-a-virtual-machine-in-azure"></a>Fájlok visszaállítása Azure-beli virtuális gépekre

Az Azure Backup georedundáns helyreállítási tárolókban tárolt helyreállítási pontokat hoz létre. Helyreállítási pontról történő visszaállításkor visszaállíthatja a teljes virtuális gépet, vagy csak egyes fájlokat. Ez a cikk részletesen ismerteti az egyes fájlok visszaállításának módját. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
>
> * Helyreállítási pontok listázása és kiválasztása
> * Helyreállítási pont csatlakoztatása egy virtuális géphez
> * Fájlok visszaállítása egy helyreállítási pontból

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz olyan Linux rendszerű virtuális gépre van szükség, amelyet az Azure Backup véd. A fájl véletlen törlésének és a helyreállítási folyamatnak a szimulálásához töröljön egy oldalt egy webkiszolgálóról. Ha szüksége van egy linuxos virtuális gépre, amely egy webkiszolgálót futtat, és amelyet az Azure Backup véd, lásd: [Virtuális gép biztonsági mentése az Azure-ban a parancssori felület használatával](quick-backup-vm-cli.md).

A környezet előkészítése:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ehhez a cikkhez az Azure CLI 2.0.18 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="backup-overview"></a>A biztonsági mentés áttekintése

Amikor az Azure biztonsági mentést kezdeményez, a virtuális gépen futó biztonsági mentési bővítmény időponthoz kötött pillanatképet készít. A biztonsági mentési bővítmény az első biztonsági mentés kérésekor települ a virtuális gépre. Azure Backup az alapul szolgáló tárterületről is készíthet pillanatképet, ha a virtuális gép nem fut a biztonsági mentés során.

Alapértelmezés szerint az Azure Backup a fájlrendszerrel konzisztens biztonsági másolatot készít. Amikor az Azure Backup elkészítette a pillanatképet, az adatok átkerülnek a helyreállítási tárba. A maximális hatékonyság érdekében az Azure Backup csak azokat az adatblokkokat azonosítja és továbbítja, amelyek az előző biztonsági mentés óta változtak.

Ha az adatátvitel befejeződött, a rendszer eltávolítja a pillanatképet, és létrehoz egy helyreállítási pontot.

## <a name="delete-a-file-from-a-vm"></a>Fájl törlése egy virtuális gépről

Ha véletlenül törölt vagy módosított egy fájlt, arra is van lehetősége, hogy a helyreállítási pontból csak egyes fájlokat állítson vissza. Ez a folyamat lehetővé teszi a helyreállítási pont biztonsági másolatában szereplő fájlok tallózását, és csak a szükséges fájlok visszaállítását. Ebben a példában a fájlszintű helyreállítási folyamat bemutatásához törölni fogunk egy fájlt egy webkiszolgálóról.

1. A virtuális géphez való csatlakozáshoz kérje le a virtuális gép IP-címét az [az vm show](/cli/azure/vm#az-vm-show) paranccsal:

     ```azurecli-interactive
     az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
     ```

2. Annak ellenőrzéséhez, hogy a webhely jelenleg működik-e, nyissa meg a virtuális gép a nyilvános IP-címét egy webböngészőben. Ne zárja be a böngészőablakot.

    ![Alapértelmezett NGINX-weblap](./media/tutorial-restore-files/nginx-working.png)

3. Csatlakozzon a virtuális géphez SSH-val. Cserélje le a *publicIpAddress* kifejezést a korábbi paranccsal beolvasott nyilvános IP-címre:

    ```bash
    ssh publicIpAddress
    ```

4. Törölje a webkiszolgáló alapértelmezett lapját a */var/www/html/index.nginx-debian.html* címről az alábbiak szerint:

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```

5. A webböngészőben frissítse a weblapot. A webhely már nem tölti be a lapot, ahogy azt a következő példa is mutatja:

    ![Az NGINX webhely már nem tölti be az alapértelmezett oldalt](./media/tutorial-restore-files/nginx-broken.png)

6. Zárja be a virtuális gép felé indított SSH-munkamenetet a következőképpen:

    ```bash
    exit
    ```

## <a name="generate-file-recovery-script"></a>Fájlhelyreállítási szkript létrehozása

A fájlok visszaállításához az Azure Backup egy, a virtuális gépen futtatható szkriptet biztosít, amely helyi meghajtóként csatlakoztatja a helyreállítási pontot. Ezen a helyi meghajtón megkeresheti és visszaállíthatja a fájlokat a virtuális gépre. Ha ezzel végzett, válassza le a helyreállítási pont. Az Azure Backup a hozzárendelt ütemezési és megőrzési szabályzat szerint folytatja az adatok biztonsági mentését.

1. A virtuális gép helyreállítási pontjainak listázásához használja [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list) parancsot. Ebben a példában a *myVM* nevű virtuális gép legutóbbi helyreállítási pontját választjuk, amelyet a *myRecoveryServicesVault* véd:

    ```azurecli-interactive
    az backup recoverypoint list \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --query [0].name \
        --output tsv
    ```

2. A helyreállítási pontot a virtuális géphez kapcsoló vagy csatlakoztató szkript beszerzéséhez használja az [az backup restore files mount-rp](/cli/azure/backup/restore/files#az-backup-restore-files-mount-rp) parancsot. A következő példa a *myVM* nevű virtuális gép parancsfájlját szerzi be a *myRecoveryServicesVault*-ben védettként.

    Cserélje le a *myRecoveryPointName* kifejezést az előző paranccsal beszerzett helyreállítási pont nevére:

    ```azurecli-interactive
    az backup restore files mount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

    A rendszer az alábbi példában látható módon letölti a szkriptet, és megjeleníti a jelszót:

    ```output
    File downloaded: myVM_we_1571974050985163527.sh. Use password c068a041ce12465
    ```

3. A szkriptet a biztonságos másolás (Secure Copy – SCP) használatával továbbítsa a virtuális gépre. Adja meg a letöltött szkript nevét, és cserélje le *publicIpAddress* kifejezést a virtuális gép nyilvános IP-címére. Győződjön meg arról, hogy a `:` zárókarakter szerepel az SCP-parancs végén az alább látható módon:

    ```bash
    scp myVM_we_1571974050985163527.sh 52.174.241.110:
    ```

## <a name="restore-file-to-your-vm"></a>Fájl visszaállítása a virtuális gépre

Most, hogy a helyreállítási szkript a virtuális gépre van másolva, csatlakoztathatja a helyreállítási pontot, és visszaállíthatja a fájlokat.

>[!NOTE]
> [Itt](backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script) tekintheti meg, hogy a folytatás előtt futtathatja-e a parancsfájlt a virtuális gépen.

1. Csatlakozzon a virtuális géphez SSH-val. Cserélje le *publicIpAddress* kifejezést a virtuális gép nyilvános IP-címére az alább látható módon:

    ```bash
    ssh publicIpAddress
    ```

2. A szkript megfelelő működéséhez adjon hozzá végrehajtási jogosultságokat a **chmod** parancs használatával. Adja meg saját szkriptje nevét:

    ```bash
    chmod +x myVM_we_1571974050985163527.sh
    ```

3. A helyreállítási pont csatlakoztatásához futtassa a szkriptet. Adja meg saját szkriptje nevét:

    ```bash
    ./myVM_we_1571974050985163527.sh
    ```

    A szkript futtatásakor a rendszer felszólítja, hogy adjon meg egy jelszót a helyreállítási pont eléréséhez. Adja meg a helyreállítási szkriptet létrehozó, előzőleg futtatott [az backup restore files mount-rp](/cli/azure/backup/restore/files#az-backup-restore-files-mount-rp) parancs kimenetében szereplő jelszót.

    A szkript kimenetében megtalálható a helyreállítási pont elérési útja. Az alábbi példa kimenetében látható, hogy a helyreállítási pont csatlakoztatva van a */home/azureuser/myVM-20170919213536/Volume1* címen:

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : c068a041ce12465

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170919213536/Volume1

    ************ Open File Explorer to browse for files. ************
    ```

4. A **cp** parancs használatával másolja vissza az NGINX alapértelmezett weblapot a csatlakoztatott helyreállítási pontról fájl eredeti helyére. Cserélje le a */home/azureuser/myVM-20170919213536/Volume1* csatlakoztatási pontot a saját helyére:

    ```bash
    sudo cp /home/azureuser/myVM-20170919213536/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```

5. A webböngészőben frissítse a weblapot. A webhely most ismét megfelelően betöltődik az alábbi példában látható módon:

    ![Az NGINX webhely most már megfelelően betöltődik](./media/tutorial-restore-files/nginx-restored.png)

6. Zárja be a virtuális gép felé indított SSH-munkamenetet a következőképpen:

    ```bash
    exit
    ```

7. Válassza le a helyreállítási pontot a virtuális gépről az [az backup restore files unmount-rp](/cli/azure/backup/restore/files#az-backup-restore-files-unmount-rp) paranccsal. Az alábbi példa leválasztja a helyreállítási pontot a *myRecoveryServicesVault* tárolóban őrzött *myVM* nevű virtuális gépről.

    Cserélje le a *myRecoveryPointName* kifejezést az előző parancsokkal beszerzett helyreállítási pont nevére:

    ```azurecli-interactive
    az backup restore files unmount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy helyreállítási pontot csatlakoztatott egy virtuális géphez, és visszaállította egy webkiszolgáló fájljait. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
>
> * Helyreállítási pontok listázása és kiválasztása
> * Helyreállítási pont csatlakoztatása egy virtuális géphez
> * Fájlok visszaállítása egy helyreállítási pontból

Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan végezhető biztonsági mentés Windows Serverről az Azure-ba.

> [!div class="nextstepaction"]
> [Windows Server biztonsági mentése az Azure-ba](tutorial-backup-windows-server-to-azure.md)
