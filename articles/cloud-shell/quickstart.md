---
title: Azure Cloud Shell rövid útmutató – bash
description: Megtudhatja, hogyan használhatja a bash parancssort a böngészőben a Azure Cloud Shell használatával.
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/12/2018
ms.author: damaerte
ms.openlocfilehash: 58d795a5aee79e4149864a79a923ce34950b31d6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "79458069"
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>A bash gyors üzembe helyezése Azure Cloud Shell

Ez a dokumentum részletesen ismerteti, hogyan használható a bash a [Azure Portal](https://ms.portal.azure.com/)Azure Cloud Shellban.

> [!NOTE]
> Azure Cloud Shell rövid útmutatóban is elérhető egy [PowerShell](quickstart-powershell.md) .

## <a name="start-cloud-shell"></a>Cloud Shell indítása
1. **Cloud Shell** elindítása a Azure Portal legfelső navigációs sávján. <br>
![](media/quickstart/shell-icon.png)

2. Válasszon egy előfizetést, és hozzon létre egy Storage-fiókot, és Microsoft Azure a fájlok megosztását.
3. Válassza a "tároló létrehozása" lehetőséget.

> [!TIP]
> A rendszer minden munkamenetben automatikusan hitelesíti az Azure CLI-t.

### <a name="select-the-bash-environment"></a>Bash-környezet kiválasztása
Győződjön meg arról, hogy a környezet legördülő menüje a rendszerhéj ablakának bal oldali részén látható `Bash` . <br>
![](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>Előfizetés beállítása
1. Azon előfizetések listázása, amelyekhez hozzáférése van.
   ```azurecli-interactive
   az account list
   ```

2. Az előnyben részesített előfizetés beállítása: <br>
```azurecli-interactive
az account set --subscription 'my-subscription-name'
```

> [!TIP]
> Az előfizetést a későbbi munkamenetek esetén is megjegyezjük `/home/<user>/.azure/azureProfile.json` .

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy új erőforráscsoportot a "MyRG" nevű WestUS.
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Linux rendszerű virtuális gép létrehozása
Hozzon létre egy Ubuntu virtuális gépet az új erőforráscsoporthoz. Az Azure CLI létrehozza az SSH-kulcsokat, és beállítja velük a virtuális gépet. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> A használata `--generate-ssh-keys` arra utasítja az Azure CLI-t, hogy nyilvános és titkos kulcsokat hozzon létre és állítson be a virtuális gépen és a `$Home` címtárban. Alapértelmezés szerint a kulcsok a és a Cloud Shell `/home/<user>/.ssh/id_rsa` kerülnek `/home/<user>/.ssh/id_rsa.pub` . A `.ssh` mappát a rendszer a csatolt fájlmegosztás 5 GB-os rendszerképében őrzi meg `$Home` .

A virtuális gépen lévő felhasználóneve a Cloud Shell ($) által használt Felhasználónév User@Azure: .

### <a name="ssh-into-your-linux-vm"></a>SSH-t a linuxos virtuális gépre
1. Keresse meg a virtuális gép nevét a Azure Portal keresési sávjában.
2. Kattintson a "kapcsolódás" gombra a virtuális gép nevének és nyilvános IP-címének lekéréséhez. <br>
   ![](media/quickstart/sshcmd-copy.png)

3. SSH-t a virtuális gépre a `ssh` cmd fájllal.
   ```
   ssh username@ipaddress
   ```

Az SSH-kapcsolatok létrehozásakor az Ubuntu Welcome promptot kell látnia. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Takarítás 
1. Lépjen ki az SSH-munkamenetből.
   ```
   exit
   ```

2. Törölje az erőforráscsoportot és a benne található összes erőforrást.
   ```azurecli-interactive
   az group delete -n MyRG
   ```

## <a name="next-steps"></a>További lépések
[Tudnivalók a bash-fájlok megőrzéséről Cloud Shell](persisting-shell-storage.md) <br>
[További tudnivalók az Azure CLI-ről](https://docs.microsoft.com/cli/azure/) <br>
[Tudnivalók a Azure Files Storage szolgáltatásról](../storage/files/storage-files-introduction.md) <br>
