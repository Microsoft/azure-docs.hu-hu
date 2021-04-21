---
title: SSH-kulcspár létrehozása és használata Linux rendszerű virtuális gépekhez az Azure-ban
description: Nyilvános-titkos SSH-kulcspár létrehozása és használata Linux rendszerű virtuális gépekhez az Azure-ban a hitelesítési folyamat biztonságának növelése érdekében.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: c5e683e1f5af42a69fac45c20f52169834967649
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788132"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Gyors lépések: Nyilvános-titkos SSH-kulcspár létrehozása és használata Linux rendszerű virtuális gépekhez az Azure-ban

Secure Shell- (SSH-) kulcspár használatával SSH-kulcsokat használva hozhat létre olyan virtuális gépeket az Azure-ban, amelyek SSH-kulcsokat használnak a hitelesítéshez. Ez a cikk bemutatja, hogyan hozhat létre és használhat gyorsan egy nyilvános-titkos SSH-kulcspárt Linux rendszerű virtuális gépekhez. Ezeket a lépéseket a Azure Cloud Shell macOS- vagy Linux-gazdagépen. 

> [!NOTE]
> Az SSH-kulcsokkal létrehozott virtuális gépek alapértelmezés szerint letiltott jelszavakkal vannak konfigurálva, ami jelentősen növeli a találgatásos támadásokkal szembeni nehézséget. 

További háttér-információkért és példákért lásd az SSH-kulcspárok [létrehozásához szükséges részletes lépéseket.](create-ssh-keys-detailed.md)

Az SSH-kulcsok Windows rendszerű számítógépeken történő létrehozásáról és használatának további módjaiért lásd: SSH-kulcsok használata Windows rendszerrel [az Azure-ban.](ssh-from-windows.md)

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása

A `ssh-keygen` paranccsal hozzon létre nyilvános és titkos SSH-kulcsfájlokat. Ezek a fájlok alapértelmezés szerint a ~/.ssh könyvtárban vannak létrehozva. Megadhat egy másik helyet és egy nem kötelező jelszót *(jelszót)* a titkos kulcsfájl eléréséhez. Ha a megadott helyen létezik azonos nevű SSH-kulcspár, a rendszer felülírja ezeket a fájlokat.

A következő parancs egy SSH-kulcspárt hoz létre RSA-titkosítással és 4096-os bithosszsal:

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

Ha az [Azure CLI-t](/cli/azure) használja a virtuális gép létrehozásához [az az vm create](/cli/azure/vm#az_vm_create) paranccsal, az kapcsolóval is létrehozhat nyilvános és titkos SSH-kulcsfájlokat. `--generate-ssh-keys` A rendszer a ~/.ssh könyvtárban tárolja a kulcsfájlokat, kivéve, ha a kapcsolóval másként van `--ssh-dest-key-path` megadva. Ha már létezik SSH-kulcspár, és a beállítást használja, a rendszer nem hoz létre új kulcspárt, hanem a meglévő  `--generate-ssh-keys` kulcspárt használja. A következő parancsban cserélje le a *VMname és* *az RGname* értékeket a saját értékeire:

```azurecli
az vm create --name VMname --resource-group RGname --image UbuntuLTS --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Nyilvános SSH-kulcs megszabadása virtuális gép üzembe helyezésekor

Olyan Linux rendszerű virtuális gép létrehozásához, amely SSH-kulcsokat használ a hitelesítéshez, adja meg a nyilvános SSH-kulcsot a virtuális gép létrehozásakor az Azure Portal, az Azure CLI, Azure Resource Manager sablonok vagy más módszerek használatával:

* [Linux virtuális gép létrehozása az Azure Portal használatával](quick-create-portal.md)
* [Linux rendszerű virtuális gép létrehozása az Azure CLI használatával](quick-create-cli.md)
* [Linux virtuális gép létrehozása Azure-sablon alapján](create-ssh-secured-vm-from-template.md)

Ha nem ismeri a nyilvános SSH-kulcs formátumát, a következő paranccsal jelenítse meg a nyilvános kulcsot, és szükség esetén cserélje le a adatokat a saját nyilvános kulcsfájljának elérési útjára és `cat` `~/.ssh/id_rsa.pub` fájlnevére:

```bash
cat ~/.ssh/id_rsa.pub
```

Egy tipikus nyilvános kulcsérték az alábbi példához hasonló:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Ha a nyilvános kulcsfájl tartalmát másolja és beilleszti a Azure Portal- vagy Resource Manager-sablonba, ügyeljen rá, hogy ne másoljon záró szóközt. A macOS-hez elérhető nyilvános kulcs másolásához át kell másolnia a nyilvános kulcsfájlt a következőbe: `pbcopy` . Linux rendszeren hasonlóképpen a nyilvános kulcsfájlt olyan programoknak is átküldheti, mint a `xclip` .

A Linux rendszerű virtuális gépen az Azure-ban tárolt nyilvános kulcs alapértelmezés szerint a ~/.ssh/id_rsa.pub helyen található, hacsak nem adott meg másik helyet a kulcspár létrehozásakor. Ha az [Azure CLI 2.0-val](/cli/azure) meglévő nyilvános kulccsal hozza létre a virtuális gépet, adja meg az értéket és opcionálisan a nyilvános kulcs helyét az [az vm create](/cli/azure/vm#az_vm_create) paranccsal, az `--ssh-key-values` kapcsolóval. A következő parancsban cserélje le a *myVM,* *myResourceGroup,* *UbuntuLTS,* *azureuser*, és *mysshkey.pub* értékeket a saját értékeire:


```azurecli
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --ssh-key-values mysshkey.pub
```

Ha több SSH-kulcsot szeretne használni a virtuális géphez, megadhatja őket egy szóköz-elválasztott listában, a következő `--ssh-key-values sshkey-desktop.pub sshkey-laptop.pub` listában: .


## <a name="ssh-into-your-vm"></a>Bejelentkezés a virtuális gépre SSH-val

Az Azure-beli virtuális gépen üzembe helyezett nyilvános kulccsal és a helyi rendszeren található titkos kulccsal SSH-val a virtuális gép IP-címével vagy DNS-nevével csatlakozik a virtuális géphez. A következő parancsban cserélje le az *azureuser* és *myvm.westus.cloudapp.azure.com* a rendszergazda felhasználónevére és a teljes tartománynévre (vagy IP-címre):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Ha a kulcspár létrehozásakor jelszót adott meg, adja meg ezt a jelszót, amikor a rendszer kéri a bejelentkezési folyamat során. A rendszer hozzáadja a virtuális gépet a ~/.ssh/known_hosts-fájlhoz, és addig nem fog újra csatlakozni, amíg meg nem változik az Azure-beli virtuális gép nyilvános kulcsa, vagy el nem távolítja a kiszolgáló nevét a ~/.ssh/known_hosts.

Ha a virtuális gép az "időben elérhető" hozzáférési szabályzatot használja, a virtuális géphez való csatlakozás előtt hozzáférést kell kérnie. További információ az "időben elérhető" szabályzatról: Manage virtual machine access using the just in time policy (Virtuális gépek hozzáférésének kezelése [az időben elérhető házirend használatával).](../../security-center/security-center-just-in-time.md)

## <a name="next-steps"></a>Következő lépések

* További információ az SSH-kulcspárok kezeléséhez: SSH-kulcspárok létrehozásához és [kezeléséhez szükséges részletes lépések.](create-ssh-keys-detailed.md)

* Ha nehézségekbe ütközik az Azure-beli virtuális gépekhez való SSH-kapcsolatok során, tekintse meg a Linux rendszerű Azure-beli virtuális gépek [SSH-kapcsolatainak hibaelhárításával kapcsolatos témakört.](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection)