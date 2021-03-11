---
title: Azure file Storage csatlakoztatása Linux rendszerű virtuális gépekhez SMB használatával
description: Az Azure file Storage csatlakoztatása Linux rendszerű virtuális gépekhez SMB használatával az Azure CLI-vel
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: cynthn
ms.openlocfilehash: 45042f474f39b3f64d45913905765016fae3cb26
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102551793"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Azure file Storage csatlakoztatása Linux rendszerű virtuális gépekhez SMB használatával

Ez a cikk bemutatja, hogyan használhatja az Azure file Storage szolgáltatást egy Linux rendszerű virtuális gépen az Azure CLI-vel való SMB-csatlakoztatás használatával. Az Azure file Storage a szabványos SMB protokoll használatával kínál fájlmegosztást a felhőben. 

A file Storage olyan fájlmegosztást biztosít a felhőben, amely a szabványos SMB protokollt használja. Bármely, az SMB 3,0-et támogató operációs rendszerből is csatlakoztathat fájlmegosztást. Ha a Linux rendszeren SMB-csatlakoztatást használ, az egyszerű biztonsági mentést egy SLA-t támogató robusztus, állandó archiválási tárolóhelyre teszi elérhetővé.

Ha a fájlokat egy virtuális gépről a file Storage-ban üzemeltetett SMB-csatlakoztatásra helyezi át, a naplók hibakeresése nagyszerű módszer. Az SMB-megosztást helyileg is csatlakoztathatja Mac, Linux vagy Windows rendszerű munkaállomásán. Az SMB nem a legjobb megoldás a Linux vagy az alkalmazás naplófájljainak valós idejű továbbítására, mert az SMB protokoll nem az ilyen súlyos naplózási feladatok kezelésére készült. Egy dedikált, egységesített naplózási rétegbeli eszköz (például a Fluent) jobb választás lenne, mint az SMB a Linux-és az alkalmazások naplózási kimenetének gyűjtéséhez.

Ehhez az útmutatóhoz az Azure CLI 2.0.4 vagy újabb verzióját kell futtatnia. A verzió megkereséséhez futtassa a következő parancsot: **az --version**. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 


## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy *myResourceGroup* nevű ERŐFORRÁSCSOPORTOT az *USA keleti* régiójában.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

Hozzon létre egy új Storage-fiókot a létrehozott erőforráscsoport keretében az [az Storage Account Create](/cli/azure/storage/account)paranccsal. Ez a példa létrehoz egy *mystorageacct kifejezést \<random number>* nevű Storage-fiókot, és a **storageacct tárfiókban** változóban helyezi el a Storage-fiók nevét. A Storage-fiók nevének egyedinek kell lennie, ezzel `$RANDOM` egy számot fűz a végponthoz, hogy az egyedi legyen.

```azurecli
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

## <a name="get-the-storage-key"></a>A tároló kulcsának beolvasása

Amikor létrehoz egy Storage-fiókot, a rendszer párokban hozza létre a fiók kulcsait, hogy a szolgáltatás megszakítása nélkül is elforgatható legyen. Ha a pár második kulcsára vált, létrehoz egy új kulcspárt. Az új Storage-fiókok kulcsai mindig párokban jönnek létre, így mindig legalább egy fel nem használt Storage-fiók kulcsa készen áll a váltásra.

Tekintse meg a Storage-fiók kulcsait az [az Storage Account Keys List](/cli/azure/storage/account/keys)paranccsal. Ez a példa az 1. kulcs értékét tárolja a **STORAGEKEY** változóban.

```azurecli
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-a-file-share"></a>Fájlmegosztás létrehozása

Hozza létre a file Storage-megosztást az [az Storage Share Create](/cli/azure/storage/share)paranccsal. 

A megosztási név csak kisbetűket, számokat és szimpla kötőjeleket tartalmazhat, de nem kezdődhet kötőjellel. A fájlmegosztások és fájlok elnevezésére vonatkozó információkért lásd: [Naming and Referencing Shares, Directories, Files, and Metadata](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata) (Megosztások, könyvtárak, fájlok és metaadatok elnevezése és hivatkozása).

Ez a példa egy *MyShare* nevű megosztást hoz létre egy 10-GIB kvótával. 

```azurecli
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY
```

## <a name="create-a-mount-point"></a>Csatlakoztatási pont létrehozása

Ahhoz, hogy csatlakoztatni lehessen az Azure-fájlmegosztást a Linux rendszerű számítógépén, meg kell győződnie arról, hogy telepítve van a **CIFS-utils** csomag. A telepítési utasításokért lásd: [a CIFS-utils csomag telepítése a Linux-disztribúcióhoz](../../storage/files/storage-how-to-use-files-linux.md#install-cifs-utils).

Azure Files SMB protokollt használ, amely a 445-as TCP-porton keresztül kommunikál.  Ha nem sikerül csatlakoztatni az Azure-fájlmegosztást, győződjön meg arról, hogy a tűzfal nem blokkolja a 445-es TCP-portot.


```bash
mkdir -p /mnt/MyAzureFileShare
```

## <a name="mount-the-share"></a>A megosztás csatlakoztatása

Csatlakoztassa az Azure-fájlmegosztást a helyi könyvtárba. 

```bash
sudo mount -t cifs //$STORAGEACCT.file.core.windows.net/myshare /mnt/MyAzureFileShare -o vers=3.0,username=$STORAGEACCT,password=$STORAGEKEY,dir_mode=0777,file_mode=0777,serverino
```

A fenti parancs a [Mount](https://linux.die.net/man/8/mount) parancs használatával csatlakoztatja az Azure-fájlmegosztást és a [CIFS](https://linux.die.net/man/8/mount.cifs)-ra vonatkozó beállításokat. Pontosabban a file_mode és a dir_mode beállítások a fájlok és a könyvtárak engedélyezésére vannak beállítva `0777` . Az `0777` engedély olvasási, írási és végrehajtási engedélyeket biztosít az összes felhasználó számára. Ezeket az engedélyeket megváltoztathatja az értékek más chmod- [engedélyekkel](https://en.wikipedia.org/wiki/Chmod)való lecserélésével. Más [CIFS](https://linux.die.net/man/8/mount.cifs) -beállításokat is használhat, mint például a GID vagy az UID. 


## <a name="persist-the-mount"></a>A csatlakoztatás fenntartása

A Linux rendszerű virtuális gép újraindításakor a csatlakoztatott SMB-megosztás le van választva a leállítás során. Ha újra szeretné csatlakoztatni az SMB-megosztást a rendszerindításkor, adjon hozzá egy sort a Linux/etc/fstab. A Linux az fstab fájl használatával listázza a rendszerindítási folyamat során csatlakoztatni kívánt fájlrendszereket. Az SMB-megosztás hozzáadásával biztosítható, hogy a file Storage-megosztás egy tartósan csatlakoztatott fájlrendszer legyen a Linux rendszerű virtuális gép számára. A file Storage SMB-megosztás új virtuális géphez való hozzáadása a Cloud-init használata esetén lehetséges.

```bash
//myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=myStorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

Az éles környezetek fokozott biztonsága érdekében a hitelesítő adatokat az fstab-n kívül kell tárolnia.

## <a name="next-steps"></a>Következő lépések

- [Linux rendszerű virtuális gép testreszabása a Cloud-init használatával a létrehozás során](using-cloud-init.md)
- [Add a disk to a Linux VM (Lemez hozzáadása Linux rendszerű virtuális géphez)](add-disk.md)
- [Azure Disk Encryption Linux rendszerű virtuális gépekhez](disk-encryption-overview.md)
