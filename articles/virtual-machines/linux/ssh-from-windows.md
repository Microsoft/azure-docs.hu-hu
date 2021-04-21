---
title: SSH-kulcsok használata Linux rendszerű virtuális gépekhez való csatlakozáshoz
description: Megtudhatja, hogyan hozhat létre és használhat SSH-kulcsokat Egy Windows rendszerű számítógépről linuxos virtuális géphez való csatlakozáshoz az Azure-ban.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.date: 10/31/2020
ms.topic: how-to
ms.author: cynthn
ms.openlocfilehash: 0199a47b2306d7d461ba61057c7ab1015015df08
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835559"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>SSH-kulcsok használata Windows rendszeren az Azure-ban

Ez a cikk olyan Windows-felhasználók számára használható, akik Secure [](#connect-to-your-vm) *Shell-* (SSH-) kulcsokat szeretne létrehozni és használni az Azure-beli Linux rendszerű virtuális gépekhez való csatlakozáshoz. [](#create-an-ssh-key-pair) Emellett létrehozhat [és tárolhat SSH-kulcsokat](../ssh-keys-portal.md) a Azure Portal, amikor virtuális gépeket hoz létre a portálon.


Linux- vagy macOS-ügyfél SSH-kulcsait a gyors [lépésekben használhatja.](mac-create-ssh-keys.md) Az SSH részletesebb áttekintését lásd: Részletes lépések: SSH-kulcsok létrehozása és kezelése Linux rendszerű virtuális gépek hitelesítéséhez az [Azure-ban.](create-ssh-keys-detailed.md)

## <a name="overview-of-ssh-and-keys"></a>Az SSH és a kulcsok áttekintése

[Az SSH](https://www.ssh.com/ssh/) egy titkosított kapcsolati protokoll, amely biztonságos bejelentkezéseket tesz lehetővé nem biztonságos kapcsolatokon keresztül. Az SSH az Azure-ban üzemeltetett Linux rendszerű virtuális gépek alapértelmezett kapcsolati protokollja. Bár maga az SSH titkosított kapcsolatot biztosít, a jelszavak SSH-val való használata továbbra is sebezhetővé teszi a virtuális gépet a találgatásos támadásokkal szemben. Javasoljuk, hogy SSH-kapcsolaton keresztül csatlakozzon egy virtuális géphez egy nyilvános-titkos kulcspár, más néven *SSH-kulcs használatával.* 

A nyilvános-titkos kulcspár olyan, mint a bejárati ajtó zárolása. A zárolást a nyilvános , bárki megnyithatja, aki a megfelelő kulccsal van. A kulcs **privát,** és csak olyan személyeknek van megadva, akikben megbízik, mert az ajtó feloldására használható. 

- A *nyilvános kulcs* a Linux rendszerű virtuális gépen lesz elhelyezve a virtuális gép létrehozásakor. 

- A *titkos kulcs* a helyi rendszeren marad. Védje a titkos kulcsot. Ne ossza meg senkivel.

Amikor csatlakozik a Linux rendszerű virtuális géphez, a virtuális gép teszteli az SSH-ügyfelet, hogy a megfelelő titkos kulccsal rendelkezik-e. Ha az ügyfél rendelkezik a titkos kulccsal, hozzáférést kap a virtuális géphez. 

A szervezet biztonsági szabályzataitól függően egyetlen kulcspárt használhat több Azure-beli virtuális gép és szolgáltatás eléréséhez. Nincs szükség külön kulcspárra minden virtuális géphez. 

A nyilvános kulcs bárkivel megosztható, de csak Ön (vagy a helyi biztonsági infrastruktúra) férhet hozzá a titkos kulcshoz.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-clients"></a>SSH-ügyfelek

A legújabb verziói Windows 10 [OpenSSH-ügyfélparancsokat](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) tartalmaznak SSH-kulcsok létrehozásához és használathoz, valamint SSH-kapcsolatok létrehozásához a PowerShellből vagy egy parancssorból. Ez a legegyszerűbb módszer arra, hogy windowsos számítógépről hozzon létre SSH-kapcsolatot a Linux rendszerű virtuális géppel. 

A virtuális géphez való csatlakozáshoz a Bash Azure Cloud Shell [is](../../cloud-shell/overview.md) használhatja a virtuális géphez. Használhatja a Cloud Shell [böngészőben,](https://shell.azure.com/bash)a [Azure Portal-ban,](https://portal.azure.com)vagy terminálként az Visual Studio Code-ban az [Azure-fiók bővítmény használatával.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

A virtuális géphez SSH Linuxos Windows-alrendszer keresztüli csatlakozáshoz és más natív Linux-eszközök [Bash-rendszerhéjon](/windows/wsl/about) belüli használatára is telepítheti a virtuális gépet.

## <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása

Hozzon létre egy SSH-kulcspárt az `ssh-keygen` paranccsal. Adjon meg egy fájlnevet, vagy használja az alapértelmezett zárójelet `C:\Users\username/.ssh/id_rsa` (például).  Adjon meg egy jelszót a fájlhoz, vagy hagyja üresen a jelszót, ha nem szeretne jelszót használni. 

```powershell
ssh-keygen -m PEM -t rsa -b 4096
```

## <a name="create-a-vm-using-your-key"></a>Virtuális gép létrehozása a kulccsal

Olyan Linux rendszerű virtuális gép létrehozásához, amely SSH-kulcsokat használ a hitelesítéshez, adja meg a nyilvános SSH-kulcsot a virtuális gép létrehozásakor.

Az Azure CLI használatával megadhatja a nyilvános kulcs elérési útját és fájlnevét a és a `az vm create` `--ssh-key-value` paraméterrel.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVM \
   --image UbuntuLTS\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

A PowerShellben használja a parancsot, és adja hozzá az `New-AzVM` SSH-kulcsot a virtuálisgép-konfigurációhoz a használatával. Példa: Rövid útmutató: Linux rendszerű virtuális gép [létrehozása az Azure-ban a PowerShell használatával.](quick-create-powershell.md)

Ha sok üzembe helyezést szeretne a portálon keresztül, érdemes lehet feltölteni a nyilvános kulcsot az Azure-ba, ahol könnyen kiválasztható, amikor virtuális gépet hoz létre a portálról. További információ: [SSH-kulcs feltöltése.](../ssh-keys-portal.md#upload-an-ssh-key)


## <a name="connect-to-your-vm"></a>Csatlakozás a virtuális géphez

Az Azure-beli virtuális gépen üzembe helyezett nyilvános kulccsal és a helyi rendszeren található titkos kulccsal SSH-val a virtuális gép IP-címével vagy DNS-nevével. A következő parancsban cserélje le az *azureuser* és a *10.111.12.123* címet a rendszergazda felhasználónevére, az IP-címre (vagy teljes tartománynévre) és a titkos kulcs elérési útjára:

```bash
ssh -i ~/.ssh/id_rsa.pub azureuser@10.111.12.123
```

Ha a kulcspár létrehozásakor jelszót konfigurált, amikor a rendszer kéri, adja meg a jelszót.

Ha a virtuális gép az "időben elérhető" hozzáférési szabályzatot használja, a virtuális géphez való csatlakozás előtt hozzáférést kell kérnie. További információ az "időben elérhető" szabályzatról: Manage virtual machine access using the just in time policy (Virtuális gépek hozzáférésének kezelése [az időben elérhető házirend használatával).](../../security-center/security-center-just-in-time.md)


## <a name="next-steps"></a>Következő lépések

- További információ az SSH-kulcsokról a Azure Portal: [Generate and store SSH keys](../ssh-keys-portal.md) in the Azure Portal to use when creating VMs in the portal.

- Az SSH-kulcsokkal való munkavégzés részletes lépéseit, beállításait és speciális példáit az SSH-kulcspárok létrehozásához szükséges részletes [lépéseket lásd:](create-ssh-keys-detailed.md).

- Az SSH-kulcsok létrehozásához és linuxos virtuális gépekhez Azure Cloud Shell PowerShellt is használhatja. Lásd a [PowerShell rövid útmutatóját.](../../cloud-shell/quickstart-powershell.md#ssh)

- Ha nehézségekbe ütközik a Linux rendszerű virtuális gépekhez való SSH-csatlakozás során, tekintse meg a Linux rendszerű Azure-beli virtuális géphez való [SSH-kapcsolatok hibaelhárításával kapcsolatos témakört.](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
