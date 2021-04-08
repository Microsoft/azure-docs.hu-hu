---
title: SSH-kulcsok használata Linux rendszerű virtuális gépekhez való kapcsolódáshoz
description: Útmutató SSH-kulcsok létrehozásához és használatához Windows-számítógépről az Azure-beli Linux rendszerű virtuális gépekhez való kapcsolódáshoz.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.date: 10/31/2020
ms.topic: how-to
ms.author: cynthn
ms.openlocfilehash: f018f591052050431996e3017335ab003973d25a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771005"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>SSH-kulcsok használata az Azure-ban a Windowsban

Ez a cikk olyan Windows-felhasználók számára készült, akik *Secure Shell* -(SSH-) kulcsokat szeretnének [létrehozni](#create-an-ssh-key-pair) és használni az Azure-beli linuxos virtuális gépekhez való [kapcsolódáshoz](#connect-to-your-vm) . Az SSH-kulcsokat a portálon a virtuális gépek létrehozásakor használt [Azure Portal is létrehozhatja és tárolhatja](../ssh-keys-portal.md) .


Ha Linux vagy macOS rendszerű ügyfélről szeretne SSH-kulcsokat használni, tekintse meg a [gyors lépéseket](mac-create-ssh-keys.md). Az SSH részletesebb áttekintését a [részletes lépések: ssh-kulcsok létrehozása és kezelése az Azure-beli Linux](create-ssh-keys-detailed.md)RENDSZERű virtuális gépeken történő hitelesítéshez című témakörben tekintheti meg.

## <a name="overview-of-ssh-and-keys"></a>Az SSH és a kulcsok áttekintése

Az [SSH](https://www.ssh.com/ssh/) egy titkosított kapcsolati protokoll, amely biztonságos bejelentkezéseket tesz lehetővé a nem biztonságos kapcsolatokon keresztül. Az SSH az Azure-ban üzemeltetett Linux rendszerű virtuális gépek alapértelmezett kapcsolati protokollja. Habár az SSH titkosított kapcsolatokat biztosít, a jelszavak SSH-val való használata továbbra is sebezhetővé teszi a virtuális gépet a találgatásos támadásokkal szemben. Azt javasoljuk, hogy SSH-kapcsolaton keresztül csatlakozzon egy virtuális géphez egy nyilvános titkos kulcspár, más néven *SSH*-kulcs használatával. 

A nyilvános titkos kulcspár olyan, mint a bejárati ajtó zárolása. A zárolás elérhetővé vált a **nyilvánosság** számára, a megfelelő kulccsal bárki megnyithatja az ajtót. A kulcs **magán**, és csak a megbízhatónak ítélt személyeket adja meg, mert feloldható az ajtó feloldása. 

- A *nyilvános kulcsot* a rendszer a linuxos virtuális gépre helyezi a virtuális gép létrehozásakor. 

- A *titkos kulcs* a helyi rendszeren marad. Védje a titkos kulcsot. Ne ossza meg senkivel.

A Linux rendszerű virtuális géphez való csatlakozáskor a virtuális gép teszteli az SSH-ügyfelet, hogy megbizonyosodjon róla, hogy a megfelelő titkos kulccsal rendelkezik. Ha az ügyfél rendelkezik titkos kulccsal, a rendszer hozzáférést kap a virtuális géphez. 

A szervezet biztonsági házirendjeitől függően egyetlen kulcspárt is felhasználhat több Azure-beli virtuális gép és szolgáltatás elérésére. Az egyes virtuális gépekhez nem kell külön kulcspárt létrehoznia. 

A nyilvános kulcs bárkivel megosztható, de csak Ön (vagy a helyi biztonsági infrastruktúra) férhetnek hozzá a titkos kulcshoz.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-clients"></a>SSH-ügyfelek

A Windows 10-es legújabb verziói az [OpenSSH-ügyfélprogramok](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) ssh-kulcsok létrehozására és használatára, valamint a PowerShell vagy a parancssor használatával történő SSH-kapcsolatokra vonatkozó parancsokat tartalmaznak. Ez a legegyszerűbb módszer a Linux rendszerű virtuális géphez való SSH-kapcsolatok létrehozásához egy Windows rendszerű számítógépről. 

A virtuális géphez való kapcsolódáshoz a [Azure Cloud Shell](../../cloud-shell/overview.md) bash is használható. Az [Azure-fiók bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)használatával [webböngészőben](https://shell.azure.com/bash), a [Azure Portal](https://portal.azure.com)vagy a Visual Studio Code-ban található terminálként is használhatja a Cloud shell.

A [Linux Windows alrendszerét](/windows/wsl/about) is telepítheti a virtuális géphez SSH-n keresztül, és más natív Linux-eszközöket is használhat a bash-rendszerhéjon belül.

## <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása

Hozzon létre egy SSH-kulcspárt a `ssh-keygen` parancs használatával. Adjon meg egy fájlnevet, vagy használja az alapértelmezett zárójelet (például `C:\Users\username/.ssh/id_rsa` ).  Adjon meg egy hozzáférési kódot a fájlhoz, vagy hagyja üresen a jelszót, ha nem szeretne jelszót használni. 

```powershell
ssh-keygen -m PEM -t rsa -b 4096
```

## <a name="create-a-vm-using-your-key"></a>Virtuális gép létrehozása a kulcs használatával

SSH-kulcsokat használó Linux rendszerű virtuális gép létrehozásához adja meg az SSH nyilvános kulcsát a virtuális gép létrehozásakor.

Az Azure CLI használatával a és a paraméter használatával adhatja meg a nyilvános kulcs elérési útját és fájlnevét `az vm create` `--ssh-key-value` .

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVM \
   --image UbuntuLTS\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa
```

A PowerShell használatával használja `New-AzVM` és adja hozzá az SSH-kulcsot a virtuális gép konfigurációjához a következő használatával:. Példa: gyors útmutató [: linuxos virtuális gép létrehozása az Azure-ban a PowerShell használatával](quick-create-powershell.md).

Ha sok üzembe helyezést végez a portál használatával, feltölthet egy nyilvános kulcsot az Azure-ba, ahol egyszerűen kiválaszthatja a virtuális gép portálról való létrehozásakor. További információ: SSH- [kulcs feltöltése](../ssh-keys-portal.md#upload-an-ssh-key).


## <a name="connect-to-your-vm"></a>Csatlakozás a virtuális géphez

Az Azure-beli virtuális gépen üzembe helyezett nyilvános kulccsal és a helyi rendszeren lévő titkos kulccsal, SSH-val a virtuális géphez a virtuális gép IP-címét vagy DNS-nevét használva. Cserélje le az *azureuser* és a *10.111.12.123* parancsot a következő parancsra a rendszergazda FELHASZNÁLÓNEVÉVEL, az IP-címmel (vagy a teljes tartománynévvel) és a titkos kulcs elérési útjával:

```bash
ssh -i ~/.ssh/id_rsa.pub azureuser@10.111.12.123
```

Ha a kulcspárt a kulcspár létrehozásakor konfigurálta, írja be a jelszót, amikor a rendszer kéri.

Ha a virtuális gép az igény szerinti hozzáférési szabályzatot használja, a virtuális géphez való kapcsolódáshoz a hozzáférést kell kérnie. Az igény szerinti szabályzattal kapcsolatos további információkért lásd: [virtuális gépek hozzáférésének kezelése az igény szerinti házirend használatával](../../security-center/security-center-just-in-time.md).


## <a name="next-steps"></a>Következő lépések

- A Azure Portal SSH-kulcsaival kapcsolatos információkért lásd: az [ssh-kulcsok létrehozása és tárolása](../ssh-keys-portal.md) a portálon a virtuális gépek létrehozásához használt Azure Portal.

- Az SSH-kulcsok használatának részletes lépéseiről, lehetőségeiről és speciális példáit az [SSH-kulcspár létrehozásának részletes lépései](create-ssh-keys-detailed.md)című cikkben tekintheti meg.

- A PowerShellt Azure Cloud Shell is használhatja SSH-kulcsok létrehozásához és SSH-kapcsolatok létrehozásához Linux rendszerű virtuális gépekhez. Tekintse meg a [PowerShell](../../cloud-shell/quickstart-powershell.md#ssh)rövid útmutatóját.

- Ha az SSH használatával nem tud csatlakozni a Linux rendszerű virtuális gépekhez, tekintse meg [az SSH-kapcsolatok Azure Linux rendszerű virtuális géphez](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)való kapcsolódásával kapcsolatos problémát.
