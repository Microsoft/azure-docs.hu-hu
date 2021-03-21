---
title: A Cloud-init használatával bash-szkriptet futtathat egy Linux rendszerű virtuális gépen az Azure-ban
description: A Cloud-init használata bash-szkript futtatásához Linux rendszerű virtuális gépen az Azure CLI-vel való létrehozás során
author: rickstercdn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 1dabd7db85d4222ca2e9f8387b716af291ee6a69
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559426"
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>A Cloud-init használata bash-szkript futtatásához Linux rendszerű virtuális gépen az Azure-ban
Ez a cikk bemutatja, hogyan használható a [Cloud-init](https://cloudinit.readthedocs.io) egy meglévő bash-szkript futtatásához Linux rendszerű virtuális GÉPEN (VM) vagy virtuálisgép-méretezési csoportokon (VMSS) az Azure üzembe helyezési ideje alatt. Ezek a felhő-init parancsfájlok az első rendszerindítás során futnak az Azure-beli erőforrások kiépítés után. További információ arról, hogyan működik a Cloud-init natív módon az Azure-ban és a támogatott Linux-disztribúciókban: a [Cloud-init áttekintése](using-cloud-init.md)

## <a name="run-a-bash-script-with-cloud-init"></a>Bash-szkript futtatása a Cloud-init használatával
A Cloud-init esetében nem kell konvertálnia a meglévő parancsfájlokat egy felhő-konfigurációba, a Cloud-init több bemeneti típust is elfogad, amelyek közül az egyik egy bash-szkript.

Ha a Linux egyéni parancsfájl Azure-bővítményét használja a parancsfájlok futtatásához, áttelepítheti őket a Cloud-init használatára. Az Azure-bővítmények azonban integrált jelentéskészítéssel figyelmeztetnek a parancsfájlok hibáira, a Cloud-init rendszerkép központi telepítése nem fog sikerülni, ha a parancsfájl meghibásodik.

A funkció működés közbeni megtekintéséhez hozzon létre egy egyszerű bash-szkriptet a teszteléshez. A Cloud-init `#cloud-config` fájlhoz hasonlóan a parancsfájlnak helyinek kell lennie, ahol a virtuális gép kiépítéséhez a AzureCLI parancsokat fogja futtatni.  Ebben a példában hozza létre a fájlt a Cloud Shell nem a helyi gépen. Bármelyik szerkesztőt használhatja. Írja be a `sensible-editor simple_bash.sh` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. A **Nano** Editor használatához válassza a #1 lehetőséget. Győződjön meg arról, hogy a teljes Cloud-init fájl megfelelően van másolva, különösen az első sorban.  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

A rendszerkép telepítése előtt létre kell hoznia egy erőforráscsoportot az az [Group Create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy virtuális gépet az [az VM Create](/cli/azure/vm) paranccsal, és adja meg a bash parancsfájlt a `--custom-data simple_bash.sh` következő módon:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>A bash-parancsfájl futtatásának ellenőrzése
SSH-t az előző parancs kimenetében látható virtuális gép nyilvános IP-címére. Adja meg saját **publicIpAddress** a következőképpen:

```bash
ssh <publicIpAddress>
```

Váltson át a **/tmp** könyvtárra, és ellenőrizze, hogy létezik-e myScript.txt fájl, és hogy rendelkezik-e benne a megfelelő szöveggel.  Ha nem, további részletekért tekintse meg a **/var/log/Cloud-init.log** .  Keresse meg a következő bejegyzést:

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>Következő lépések
További felhő-inicializálási példákat a konfiguráció változásairól a következő témakörben talál:
 
- [További linuxos felhasználó hozzáadása egy virtuális géphez](cloudinit-add-user.md)
- [Csomagkezelő futtatása a meglévő csomagok frissítéséhez az első rendszerindításkor](cloudinit-update-vm.md)
- [Virtuális gép helyi gazdagépének módosítása](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítése, konfigurációs fájlok frissítése és kulcsok behelyezése](tutorial-automate-vm-deployment.md)
