---
title: A Cloud-init használatával hozzáadhat egy felhasználót egy Linux rendszerű virtuális géphez az Azure-ban
description: A Cloud-init használata felhasználó hozzáadása linuxos virtuális géphez az Azure CLI-vel való létrehozás során
author: rickstercdn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 2c459965f2eb29a469ac90fdeb42107d1dbcf86a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559409"
---
# <a name="use-cloud-init-to-add-a-user-to-a-linux-vm-in-azure"></a>Felhasználó hozzáadása Linux rendszerű virtuális géphez az Azure-ban a Cloud-init használatával
Ez a cikk bemutatja, hogyan lehet a [Cloud-init](https://cloudinit.readthedocs.io) használatával felhasználókat felvenni egy virtuális GÉPRE (VM) vagy virtuálisgép-méretezési csoportokra (VMSS) az Azure üzembe helyezési ideje alatt. Ez a Cloud-init parancsfájl az első indításkor fut, ha az erőforrások kiépítve lettek az Azure-ban. További információ arról, hogyan működik a Cloud-init natív módon az Azure-ban és a támogatott Linux-disztribúciókban: [Cloud-init – áttekintés](using-cloud-init.md).

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Felhasználó hozzáadása virtuális géphez a Cloud-init használatával
Az új Linux rendszerű virtuális gépek egyik első feladata, hogy a *root* használatának elkerüléséhez további felhasználót adjon hozzá. Az SSH-kulcsok a biztonság és a használhatóság szempontjából ajánlott eljárások. A kulcsok hozzá lettek adva a *~/.ssh/authorized_keys* fájlhoz ezzel a Cloud-init parancsfájllal.

Ha felhasználót szeretne hozzáadni egy linuxos virtuális géphez, hozzon létre egy fájlt a *cloud_init_add_user.txt* nevű aktuális rendszerhéjban, és illessze be a következő konfigurációt. Ebben a példában hozza létre a fájlt a Cloud Shell nem a helyi gépen. Bármelyik szerkesztőt használhatja. Írja be a `sensible-editor cloud_init_add_user.txt` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. A **Nano** Editor használatához válassza a #1 lehetőséget. Győződjön meg arról, hogy a teljes Cloud-init fájl megfelelően van másolva, különösen az első sorban.  Meg kell adnia a saját nyilvános kulcsát (például a *~/.ssh/id_rsa. pub* fájl tartalmát) a következő értékhez: `ssh-authorized-keys:`

```yaml
#cloud-config
users:
  - default
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```
> [!NOTE] 
> A #cloud-config fájl tartalmazza a `- default` paramétert. Ezzel hozzáfűzi a felhasználót a kiépítés során létrehozott meglévő rendszergazda felhasználóhoz. Ha a paraméter nélkül hoz létre egy felhasználót, `- default` a rendszer felülírja az Azure platform által létrehozott automatikusan generált rendszergazdai felhasználót. 

A rendszerkép telepítése előtt létre kell hoznia egy erőforráscsoportot az az [Group Create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy virtuális gépet az [az VM Create](/cli/azure/vm) paranccsal, és határozza meg a Cloud-init fájlt a `--custom-data cloud_init_add_user.txt` következő módon:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_add_user.txt \
  --generate-ssh-keys 
```

SSH-t az előző parancs kimenetében látható virtuális gép nyilvános IP-címére. Adja meg saját **publicIpAddress** a következőképpen:

```bash
ssh <publicIpAddress>
```

Annak megerősítéséhez, hogy a felhasználó hozzá lett adva a virtuális géphez és a megadott csoportokhoz, tekintse meg a */etc/group* fájl tartalmát a következőképpen:

```bash
cat /etc/group
```

A következő példa kimenete azt mutatja, hogy a *cloud_init_add_user.txt* fájlból származó felhasználó hozzá lett adva a virtuális géphez és a megfelelő csoporthoz:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Következő lépések
További felhő-inicializálási példákat a konfiguráció változásairól a következő témakörben talál:
 
- [További linuxos felhasználó hozzáadása egy virtuális géphez](cloudinit-add-user.md)
- [Csomagkezelő futtatása a meglévő csomagok frissítéséhez az első rendszerindításkor](cloudinit-update-vm.md)
- [Virtuális gép helyi gazdagépének módosítása](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítése, konfigurációs fájlok frissítése és kulcsok behelyezése](tutorial-automate-vm-deployment.md)
