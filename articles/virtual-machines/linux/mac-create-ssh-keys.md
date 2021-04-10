---
title: SSH-kulcspár létrehozása és használata az Azure-ban Linux rendszerű virtuális gépekhez
description: Nyilvános és titkos SSH-kulcspár létrehozása és használata az Azure-ban Linux rendszerű virtuális gépekhez a hitelesítési folyamat biztonságának javítása érdekében.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: c77375782ba23114be1953d9f8ad7de31ab06f1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582187"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Gyors lépések: nyilvános SSH-kulcspár létrehozása és használata az Azure-ban Linux rendszerű virtuális gépekhez

A Secure Shell (SSH) kulcspár használatával olyan virtuális gépeket hozhat létre az Azure-ban, amelyek SSH-kulcsokat használnak a hitelesítéshez. Ebből a cikkből megtudhatja, hogyan hozhat gyorsan elő és használhat nyilvános SSH-kulcspárt Linux rendszerű virtuális gépekhez. Ezeket a lépéseket a Azure Cloud Shell, egy macOS vagy Linux rendszerű gazdagép használatával hajthatja végre. 

> [!NOTE]
> Az SSH-kulcsokkal létrehozott virtuális gépek alapértelmezés szerint letiltott jelszavakkal vannak konfigurálva, ami jelentősen növeli a találgatásos kitalált támadások nehézségét. 

További hátteret és példákat a következő témakörben talál: [SSH-kulcspár létrehozásának részletes lépései](create-ssh-keys-detailed.md).

Az SSH-kulcsok Windows-számítógépen való létrehozásával és használatával kapcsolatos további lehetőségekért lásd: [az ssh-kulcsok használata az Azure-ban Windowson](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása

Használja az `ssh-keygen` parancsot az SSH nyilvános és titkos kulcs fájljainak létrehozásához. Alapértelmezés szerint ezek a fájlok a ~/.ssh könyvtárban jönnek létre. Megadhat egy másik helyet és egy opcionális *jelszót (jelszó*) a titkos kulcs fájljának eléréséhez. Ha a megadott helyen létezik egy azonos nevű SSH-kulcspár, a rendszer felülírja ezeket a fájlokat.

Az alábbi parancs egy SSH-kulcspárt hoz létre RSA-titkosítással és 4096-es hosszúságú használatával:

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

Ha az [Azure CLI](/cli/azure) használatával hozza létre a virtuális gépet az az [VM Create](/cli/azure/vm#az-vm-create) paranccsal, lehetősége van az SSH nyilvános és titkos kulcs fájljainak megadására a `--generate-ssh-keys` lehetőség használatával. A kulcs fájljait a ~/.ssh könyvtárban tárolja a rendszer, hacsak másként nincs megadva a `--ssh-dest-key-path` kapcsoló. Ha egy SSH-kulcspár már létezik, és a  `--generate-ssh-keys` kapcsolót használja, a rendszer nem generál új kulcspárt, hanem a meglévő kulcspárt fogja használni. A következő parancsban cserélje le a *VMname* és a *RGname* értéket a saját értékeire:

```azurecli
az vm create --name VMname --resource-group RGname --image UbuntuLTS --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Nyilvános SSH-kulcs megadása virtuális gép telepítésekor

SSH-kulcsokat használó Linux rendszerű virtuális gép létrehozásához adja meg az SSH nyilvános kulcsát a virtuális gép létrehozásakor a Azure Portal, az Azure CLI, a Azure Resource Manager sablonok vagy más módszerek használatával:

* [Linux virtuális gép létrehozása az Azure Portal használatával](quick-create-portal.md)
* [Linux rendszerű virtuális gép létrehozása az Azure CLI-vel](quick-create-cli.md)
* [Linux virtuális gép létrehozása Azure-sablon alapján](create-ssh-secured-vm-from-template.md)

Ha nem ismeri az SSH nyilvános kulcs formátumát, a nyilvános kulcsot a következő paranccsal jelenítheti meg `cat` , `~/.ssh/id_rsa.pub` Ha szükséges, a saját nyilvános kulcsfájl elérési útját és fájlnevét cserélje le:

```bash
cat ~/.ssh/id_rsa.pub
```

Egy tipikus nyilvánoskulcs-érték a következő példához hasonlóan néz ki:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Ha a Azure Portal vagy egy Resource Manager-sablonban használni kívánt nyilvánoskulcs-fájl tartalmát másolja és illeszti be, ügyeljen arra, hogy ne másolja a záró szóközöket. Egy nyilvános kulcs macOS-ben történő másolásához a nyilvános kulcs fájlját áthelyezheti a következőre: `pbcopy` . A Linux rendszerben hasonlóan a nyilvános kulcs fájlját is átadhatja olyan programokhoz, mint például a `xclip` .

A Linux rendszerű virtuális gépen az Azure-ban tárolt nyilvános kulcs alapértelmezés szerint a ~/.ssh/id_rsa. pub helyen található, hacsak nem adott meg másik helyet a kulcspár létrehozásakor. Ha az [Azure CLI 2,0](/cli/azure) használatával szeretné létrehozni a virtuális gépet egy meglévő nyilvános kulccsal, adja meg az értéket és opcionálisan a nyilvános kulcs helyét az az [VM Create](/cli/azure/vm#az-vm-create) paranccsal a `--ssh-key-values` kapcsolóval. A következő parancsban cserélje le a *myVM*, a *myResourceGroup*, a *UbuntuLTS*, az *azureuser* és a *mysshkey. pub* értéket a saját értékeire:


```azurecli
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --ssh-key-values mysshkey.pub
```

Ha több SSH-kulcsot szeretne használni a virtuális géppel, megadhatja őket egy szóközzel tagolt listában, például a következőhöz `--ssh-key-values sshkey-desktop.pub sshkey-laptop.pub` .


## <a name="ssh-into-your-vm"></a>Bejelentkezés a virtuális gépre SSH-val

Az Azure-beli virtuális gépen üzembe helyezett nyilvános kulccsal és a helyi rendszeren lévő titkos kulccsal, SSH-val a virtuális gépre a virtuális gép IP-címét vagy DNS-nevét használva. A következő parancsban cserélje le az *azureuser* és a *myvm.westus.cloudapp.Azure.com* nevet a rendszergazdai felhasználónévvel és a teljes TARTOMÁNYNÉVVEL (vagy IP-címmel):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Ha a kulcspár létrehozásakor jelszót adott meg, írja be a jelszót, amikor a rendszer a bejelentkezési folyamat során kéri. A virtuális gép hozzá van adva a ~/.ssh/known_hosts-fájlhoz, és a rendszer nem kéri újra a kapcsolódást, amíg az Azure-beli virtuális gép nyilvános kulcsa nem változik, vagy a kiszolgáló neve el lesz távolítva a ~/.ssh/known_hostsból.

Ha a virtuális gép az igény szerinti hozzáférési szabályzatot használja, a virtuális géphez való kapcsolódáshoz a hozzáférést kell kérnie. Az igény szerinti szabályzattal kapcsolatos további információkért lásd: [virtuális gépek hozzáférésének kezelése az igény szerinti házirend használatával](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Következő lépések

* Az SSH-kulcspár használatával kapcsolatos további információkért tekintse meg az [SSH-kulcspár létrehozásának és kezelésének részletes lépéseit](create-ssh-keys-detailed.md).

* Ha problémái vannak az Azure-beli virtuális gépek SSH-kapcsolataival, tekintse meg az [SSH-kapcsolatok Azure Linux rendszerű virtuális géppel való megoldásával kapcsolatos](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection)problémát