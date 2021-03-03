---
title: A xrdp használata Linux rendszeren
description: Megtudhatja, hogyan telepíthet és konfigurálhat Távoli asztal (xrdp) az Azure-beli Linux rendszerű virtuális gépekhez grafikus eszközök használatával való kapcsolódáshoz
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/01/2021
ms.author: cynthn
ms.openlocfilehash: 448e9f6487b5afc51be9b3dee8e07007c8534a0b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695175"
---
# <a name="install-and-configure-xrdp-to-use-remote-desktop-with-a-linux-vm"></a>A xrdp telepítése és konfigurálása a Távoli asztal Linux rendszerű virtuális géppel való használatához

Az Azure-ban a Linux rendszerű virtuális gépeket általában egy Secure Shell-(SSH-) kapcsolatok használatával kezelik a parancssorból. A Linux vagy a gyors hibaelhárítási forgatókönyvek esetében a távoli asztal használata egyszerűbb lehet. Ez a cikk részletesen ismerteti, hogyan telepíthet és konfigurálhat egy asztali környezetet ([Xfce](https://www.xfce.org)) és egy távoli asztalt ([Xrdp](http://xrdp.org)) a Linux rendszerű virtuális géphez a Resource Manager-alapú üzemi modell használatával.


## <a name="prerequisites"></a>Előfeltételek
Ez a cikk egy meglévő Ubuntu 18,04 LTS virtuális gépet igényel az Azure-ban. Ha létre kell hoznia egy virtuális gépet, használja a következő módszerek egyikét:

- Az [Azure CLI](quick-create-cli.md)
- A [Azure Portal](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Asztali környezet telepítése Linux rendszerű virtuális gépen

Az Azure-ban a legtöbb Linux rendszerű virtuális gép nem rendelkezik alapértelmezés szerint telepített asztali környezettel. A Linux rendszerű virtuális gépeket általában SSH-kapcsolatokkal, és nem asztali környezettel kezeljük. A Linux különböző asztali környezeteket is választhat. Az asztali környezettől függően egy-két GB lemezterületet is felhasználhat, és 5 – 10 percet is igénybe vehet az összes szükséges csomag telepítéséhez és konfigurálásához.

Az alábbi példa telepíti a Lightweight [Xfce4](https://www.xfce.org/) asztali környezetet egy Ubuntu 18,04 LTS virtuális gépre. A más disztribúciók parancsai némileg eltérőek (a `yum` Red Hat Enterprise Linux telepítésére és a megfelelő `selinux` szabályok konfigurálására, vagy `zypper` például a (z) rendszerre történő telepítésre HASZNÁLHATÓk a SUSE használatára).

Először SSH-t a virtuális géphez. A következő példa a *myvm.westus.cloudapp.Azure.com* nevű virtuális géphez csatlakozik az *azureuser* felhasználónevével. Saját értékek használata:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Ha Windows rendszert használ, és további információkra van szüksége az SSH használatával kapcsolatban, tekintse meg az [ssh-kulcsok használata a Windowsban](ssh-from-windows.md)című témakört.

Ezután telepítse az Xfce- `apt` t a következő módon:

```bash
sudo apt-get update
sudo apt-get -y install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Távoli asztali kiszolgáló telepítése és konfigurálása
Most, hogy telepítette az asztali környezetet, konfigurálja a távoli asztali szolgáltatást a bejövő kapcsolatok figyelésére. a [xrdp](http://xrdp.org) egy nyílt forráskódú RDP protokoll (RDP) kiszolgáló, amely a legtöbb Linux-disztribúción elérhető, és az Xfce-sel is jól működik. Telepítse a xrdp-t az Ubuntu-alapú virtuális gépre a következőképpen:

```bash
sudo apt-get -y install xrdp
sudo systemctl enable xrdp
```

Adja meg, hogy a munkamenet indításakor milyen asztali környezetet kell használni a xrdp. Konfigurálja úgy a xrdp, hogy az Xfce-t az alábbiak szerint használja az asztali környezetként:

```bash
echo xfce4-session >~/.xsession
```

A módosítások életbe léptetéséhez indítsa újra a xrdp szolgáltatást a következőképpen:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Helyi felhasználói fiók jelszavának beállítása
Ha a virtuális gép létrehozásakor létrehozott egy jelszót a felhasználói fiókhoz, hagyja ki ezt a lépést. Ha csak az SSH-kulcsos hitelesítést használja, és nem rendelkezik helyi fiók jelszavával, adja meg a jelszót, mielőtt a xrdp használatával bejelentkezik a virtuális gépre. a xrdp nem fogadhatnak SSH-kulcsokat a hitelesítéshez. A következő példa a *felhasználói fiókhoz* tartozó jelszó megadását adja meg:

```bash
sudo passwd azureuser
```

> [!NOTE]
> A jelszó megadása nem frissíti az SSHD konfigurációját a jelszó-bejelentkezések engedélyezéséhez, ha az jelenleg nem. Biztonsági szempontból előfordulhat, hogy a kulcs alapú hitelesítéssel szeretne csatlakozni a virtuális géphez egy SSH-alagút használatával, majd csatlakozzon a xrdp-hez. Ha igen, ugorja át a következő lépést a hálózati biztonsági csoport szabályának létrehozásához a távoli asztal forgalmának engedélyezéséhez.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Hálózati biztonsági csoportra vonatkozó szabály létrehozása Távoli asztal forgalomhoz
Annak engedélyezéséhez, hogy Távoli asztal forgalom elérje a linuxos virtuális gépet, létre kell hozni egy hálózati biztonsági csoportra vonatkozó szabályt, amely lehetővé teszi a TCP-t a 3389-as porton a virtuális gép eléréséhez. A hálózati biztonsági csoportokra vonatkozó szabályokkal kapcsolatos további információkért lásd: [Mi az a hálózati biztonsági csoport?](../../virtual-network/network-security-groups-overview.md) [A Azure Portal használatával létrehozhat egy hálózati biztonsági csoportra vonatkozó szabályt](../windows/nsg-quickstart-portal.md)is.

A következő példában létrehozunk egy hálózati biztonsági csoportra vonatkozó szabályt az [az VM Open-port](/cli/azure/vm#az-vm-open-port) on *3389* porton. Az Azure CLI-ből, ne a virtuális géphez tartozó SSH-munkamenetből nyissa meg a következő hálózati biztonsági csoport szabályt:

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Linux rendszerű virtuális gép összekötése Távoli asztal ügyféllel

Nyissa meg a helyi távoli asztali ügyfelet, és kapcsolódjon a Linux rendszerű virtuális gép IP-címéhez vagy DNS-nevéhez. 

:::image type="content" source="media/use-remote-desktop/remote-desktop.png" alt-text="Képernyőkép a távoli asztali ügyfélről.":::

Adja meg a felhasználói fiók felhasználónevét és jelszavát a virtuális gépen a következőképpen:

:::image type="content" source="media/use-remote-desktop/xrdp-login.png" alt-text="Képernyőfelvétel a xrdp-naplóról.":::

A hitelesítés után az Xfce asztali környezet betöltődik, és az alábbi példához hasonlóan fog kinézni:

![Xfce asztali környezet a xrdp-n keresztül](./media/use-remote-desktop/xfce-desktop-environment.png)

Ha a helyi RDP-ügyfél hálózati szintű hitelesítést (NLA) használ, előfordulhat, hogy le kell tiltania ezt a kapcsolódási beállítást. A XRDP jelenleg nem támogatja a NLA. Megtekintheti a NLA támogató alternatív RDP-megoldásokat, például a [FreeRDP](https://www.freerdp.com)-t is.


## <a name="troubleshoot"></a>Hibaelhárítás
Ha Távoli asztal-ügyféllel nem tud csatlakozni a linuxos virtuális géphez, használja a-t a `netstat` linuxos virtuális gépen annak ellenőrzéséhez, hogy a virtuális gép az alábbi módon figyeli-e az RDP-kapcsolatokat:

```bash
sudo netstat -plnt | grep rdp
```

Az alábbi példa azt mutatja, hogy a virtuális gép a várt módon figyeli a 3389-as TCP-portot:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Ha az *xrdp-sesman* szolgáltatás nem figyel, az Ubuntu-alapú virtuális gép a következőképpen indítja újra a szolgáltatást:

```bash
sudo service xrdp restart
```

Tekintse át az Ubuntu-alapú virtuális gépen található */var/log* -naplókat, hogy a szolgáltatás miért nem válaszol. A rendszernaplót a távoli asztali kapcsolati kísérlet során is figyelheti a hibák megtekintéséhez:

```bash
tail -f /var/log/syslog
```

Más Linux-disztribúciók, például a Red Hat Enterprise Linux és a SUSE különböző módokon indíthatják újra a szolgáltatásokat és az alternatív naplófájlok helyét az ellenőrzés céljából.

Ha nem kap választ a távoli asztal ügyfelén, és nem lát eseményeket a rendszernaplóban, ez a viselkedés azt jelzi, hogy a távoli asztali forgalom nem tudja elérni a virtuális gépet. Tekintse át a hálózati biztonsági csoport szabályait, és győződjön meg arról, hogy rendelkezik olyan szabállyal, amely engedélyezi a TCP-t a 3389-es port További információ: az [alkalmazások kapcsolódási problémáinak elhárítása](../troubleshooting/troubleshoot-app-connection.md).


## <a name="next-steps"></a>Következő lépések
Az SSH-kulcsok Linux virtuális gépekkel való létrehozásával és használatával kapcsolatos további információkért lásd: [ssh-kulcsok létrehozása Linux rendszerű virtuális gépekhez az Azure-ban](mac-create-ssh-keys.md).

További információ az SSH Windows rendszeren való használatáról: [ssh-kulcsok használata a Windowsban](ssh-from-windows.md).