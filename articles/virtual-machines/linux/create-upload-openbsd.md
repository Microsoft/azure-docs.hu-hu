---
title: OpenBSD-rendszerkép létrehozása és feltöltése
description: Ismerje meg, hogyan hozhat létre és tölthet fel olyan virtuális merevlemezt (VHD-t), amely az OpenBSD operációs rendszert tartalmazza Azure-beli virtuális gép Azure CLI-n keresztüli létrehozásához
author: gbowerman
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 05/24/2017
ms.author: guybo
ms.openlocfilehash: 3437fcd2d1d521aad237ecf6d3f7937b4835d376
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554598"
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>OpenBSD lemezkép létrehozása és feltöltése az Azure-ba
Ebből a cikkből megtudhatja, hogyan hozhat létre és tölthet fel egy OpenBSD operációs rendszert tartalmazó virtuális merevlemezt (VHD-t). A feltöltés után saját képként is létrehozhatja a virtuális gépet (VM) az Azure CLI használatával.


## <a name="prerequisites"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy a következő elemek vannak:

* **Azure-előfizetés** – ha nem rendelkezik fiókkal, mindössze néhány perc alatt létrehozhat egyet. Ha rendelkezik MSDN-előfizetéssel, tekintse meg [a Visual Studio-előfizetőknek szóló havi Azure-kreditet](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Ellenkező esetben megtudhatja, hogyan [hozhat létre ingyenes próbaverziós fiókot](https://azure.microsoft.com/pricing/free-trial/).  
* **Azure CLI** – ellenőrizze, hogy a legújabb [Azure CLI](/cli/azure/install-azure-cli) telepítve van-e, és bejelentkezett-e az Azure-fiókjába az [az login](/cli/azure/reference-index)paranccsal.
* **. Vhd-fájlba telepített OpenBSD operációs rendszer** – a támogatott OpenBSD operációs rendszer (6,6-es [verzió](https://ftp.openbsd.org/pub/OpenBSD/6.6/amd64/)) virtuális merevlemezre kell, hogy telepítve legyen. Több eszköz létezik a. vhd fájlok létrehozásához. Használhat például egy virtualizációs megoldást, például a Hyper-V-t a. vhd fájl létrehozásához és az operációs rendszer telepítéséhez. A Hyper-V telepítésével és használatával kapcsolatos utasításokért lásd: [a Hyper-v telepítése és virtuális gép létrehozása](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).


## <a name="prepare-openbsd-image-for-azure"></a>OpenBSD-rendszerkép előkészítése az Azure-hoz
Azon a virtuális gépen, amelyre telepítette a Hyper-V-támogatást hozzáadó 6,1-es OpenBSD operációs rendszert, hajtsa végre az alábbi eljárásokat:

1. Ha a DHCP nincs engedélyezve a telepítés során, engedélyezze a szolgáltatást a következőképpen:

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. Állítsa be a soros konzolt a következőképpen:

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. A csomagok telepítésének konfigurálása a következőképpen történik:

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. Alapértelmezés szerint a `root` felhasználó le van tiltva az Azure-beli virtuális gépeken. A felhasználók az OpenBSD virtuális gépen futtatott parancs használatával emelt szintű jogosultságokkal futtathatnak parancsokat `doas` . A DOAs alapértelmezés szerint engedélyezve van. További információ: [DOAs. conf](https://man.openbsd.org/doas.conf.5). 

5. Telepítse és konfigurálja az Azure-ügynök előfeltételeit az alábbiak szerint:

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. Az Azure-ügynök legújabb kiadása mindig a [githubon](https://github.com/Azure/WALinuxAgent/releases)érhető el. Az ügynököt a következőképpen telepítheti:

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > Az Azure Agent telepítése után érdemes ellenőrizni, hogy az a következő módon fut-e:
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. Szüntesse meg a rendszer kiépítését a tisztításhoz, és tegye alkalmassá újra a kiépítéshez. A következő parancs az utolsó kiosztott felhasználói fiókot és a hozzá tartozó adatmennyiséget is törli:

    ```sh
    waagent -deprovision+user -force
    ```

Most leállíthatja a virtuális gépet.


## <a name="prepare-the-vhd"></a>A virtuális merevlemez előkészítése
A VHDX formátuma nem támogatott az Azure-ban, csak a **rögzített VHD**. A lemezt rögzített VHD formátumba konvertálhatja a Hyper-V kezelőjével vagy a PowerShell [Convert-VHD](/powershell/module/hyper-v/convert-vhd) parancsmag használatával. Ilyen például a következő.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>Tárolási erőforrások létrehozása és feltöltés
Először hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

A virtuális merevlemez feltöltéséhez hozzon létre egy Storage-fiókot az [az Storage Account Create](/cli/azure/storage/account)paranccsal. A Storage-fiók nevének egyedinek kell lennie, ezért adja meg a saját nevét. A következő példa egy *mystorageaccount* nevű Storage-fiókot hoz létre:

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

A Storage-fiókhoz való hozzáférés szabályozásához szerezze be a Storage-kulcsot az [az Storage Account Keys List](/cli/azure/storage/account/keys) paranccsal a következőképpen:

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

A feltöltött virtuális merevlemezek logikai elkülönítéséhez hozzon létre egy tárolót a Storage-fiókon belül az [az Storage Container Create](/cli/azure/storage/container)paranccsal:

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

Végül töltse fel a VHD-t az [az Storage blob upload](/cli/azure/storage/blob) paranccsal a következőképpen:

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>Virtuális gép létrehozása a VHD-ből
Létrehozhat egy virtuális gépet egy [minta parancsfájllal](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-vhd) vagy közvetlenül az [az VM Create](/cli/azure/vm)paranccsal. A feltöltött OpenBSD VHD megadásához használja a `--image` paramétert a következő módon:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Szerezze be az OpenBSD virtuális gép IP-címét az [az VM List-IP-Addresss](/cli/azure/vm) paranccsal az alábbiak szerint:

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

Mostantól a megszokott módon SSH-t telepíthet az OpenBSD virtuális gépre:
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a Hyper-V-támogatásról az OpenBSD 6.1-es verzióban, olvassa el az [openbsd 6,1](https://www.openbsd.org/61.html) -es és a [HyperV. 4](https://man.openbsd.org/hyperv.4).

Ha egy virtuális gépet szeretne létrehozni a felügyelt lemezről, olvassa el [az az Disk](/cli/azure/disk).