---
title: Az xrdp használata Linux rendszeren
description: Megtudhatja, hogyan telepíthet és konfigurálhat Távoli asztal (xrdp) linuxos virtuális géphez való csatlakozáshoz az Azure-ban grafikus eszközökkel
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/03/2021
ms.author: cynthn
ms.openlocfilehash: 309b106d2141c8257c5163efe7ff45a7bae5d5c3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759650"
---
# <a name="install-and-configure-xrdp-to-use-remote-desktop-with-ubuntu"></a>Az xrdp telepítése és konfigurálása a Távoli asztal Ubuntuval való használatára

Az Azure-ban a Linux rendszerű virtuális gépeket (VM-eket) általában a parancssorból, Secure Shell- (SSH-) kapcsolaton keresztül kezelik. A Linux újdonságai vagy a gyors hibaelhárítási forgatókönyvek esetében a távoli asztal használata egyszerűbb lehet. Ez a cikk részletesen bemutatja, hogyan telepíthet és konfigurálhatja az Ubuntut futtató Linux rendszerű virtuális gép asztali környezetét[(xfce)](https://www.xfce.org)és távoli asztalát[(xrdp).](http://xrdp.org)

A cikk megírása és tesztelése Ubuntu 18.04-es virtuális gép használatával történt. 

## <a name="prerequisites"></a>Előfeltételek

Ehhez a cikkhez egy meglévő Ubuntu 18.04 LTS virtuális gépre van szükség az Azure-ban. Ha létre kell hoznia egy virtuális gépet, használja az alábbi módszerek egyikét:

- Az [Azure CLI](quick-create-cli.md)
- A [Azure Portal](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Asztali környezet telepítése Linux rendszerű virtuális gépen

Az Azure-beli Linux rendszerű virtuális gépeken alapértelmezés szerint nincs telepítve asztali környezet. A Linux rendszerű virtuális gépeket általában SSH-kapcsolatokkal kezelik asztali környezet helyett. Linuxon különböző asztali környezetek közül választhat. A választott asztali környezettől függően 1–2 GB lemezterületet foglalhat, és az összes szükséges csomag telepítése és konfigurálása 5–10 percet is igénybe vehet.

Az alábbi példa egy Ubuntu 18.04 LTS virtuális gépre telepíti az egyszerűsített [xfce4](https://www.xfce.org/) asztali környezetet. A más disztribúciókhoz használható parancsok némileg eltérőek (a használatával telepíthetők a Red Hat Enterprise Linux és konfigurálhatóak a megfelelő szabályok, vagy például a SUSE-hez való `yum` `selinux` `zypper` telepítéshez használhatók).

Először is SSH-t a virtuális géphez. Az alábbi példa az azureuser felhasználónevével *csatlakozik a myvm.westus.cloudapp.azure.com* nevű virtuális *géphez.* Használja a saját értékeit:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Ha Windowst használ, és további információra van szüksége az SSH használatával kapcsolatban, tekintse meg az [SSH-kulcsok](ssh-from-windows.md)windowsos használatát.

Ezután telepítse az xfce-t a `apt` következőképpen:

```bash
sudo apt-get update
sudo apt-get -y install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Távoli asztali kiszolgáló telepítése és konfigurálása
Most, hogy telepített egy asztali környezetet, konfigurálnia kell egy távoli asztali szolgáltatást a bejövő kapcsolatok figyelése érdekében. [Az xrdp](http://xrdp.org) egy nyílt forráskódú RDP protokoll (RDP) kiszolgáló, amely a legtöbb Linux-disztribúción elérhető, és jól működik az xfce-szel. Telepítse az xrdp-t az Ubuntu virtuális gépre a következőképpen:

```bash
sudo apt-get -y install xrdp
sudo systemctl enable xrdp
```

Mondja el az xrdp-nek, hogy milyen asztali környezetet használjon a munkamenet elindítani. Konfigurálja az xrdp-t az xfce asztali környezetként való használatára az alábbiak szerint:

```bash
echo xfce4-session >~/.xsession
```

Indítsa újra az xrdp szolgáltatást, hogy a módosítások a következőképpen lépnek érvénybe:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Helyi felhasználói fiók jelszavának beállítása
Ha a virtuális gép létrehozásakor jelszót hozott létre a felhasználói fiókjához, hagyja ki ezt a lépést. Ha csak SSH-kulcsos hitelesítést használ, és nincs beállítva helyi fiókjelszó, adjon meg egy jelszót, mielőtt az xrdp használatával bejelentkezik a virtuális gépre. Az xrdp nem tud SSH-kulcsokat fogadni a hitelesítéshez. Az alábbi példa az *azureuser* felhasználói fiók jelszavát adja meg:

```bash
sudo passwd azureuser
```

> [!NOTE]
> A jelszó megadása nem frissíti az SSHD-konfigurációt, hogy lehetővé teszi a jelszó-bejelentkezést, ha jelenleg nem. Biztonsági szempontból előfordulhat, hogy egy SSH-alagúttal szeretne csatlakozni a virtuális géphez kulcsalapú hitelesítéssel, majd az xrdp-hez. Ha igen, hagyja ki a hálózati biztonsági csoport létrehozására vonatkozó következő lépést a távoli asztali forgalom engedélyezése érdekében.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Hálózati biztonsági csoport szabályának létrehozása a Távoli asztal számára
Ahhoz, Távoli asztal forgalmat elérjék a Linux rendszerű virtuális géphez, létre kell hozva egy hálózati biztonságicsoport-szabályt, amely engedélyezi a TCP számára a 3389-es porton a virtuális géphez való hozzáférést. További információ a hálózati biztonsági csoportok szabályairól: [Mi az a hálózati biztonsági csoport?](../../virtual-network/network-security-groups-overview.md) A hálózati [biztonsági csoport Azure Portal is létrehozhat egy szabályt.](../windows/nsg-quickstart-portal.md)

Az alábbi példa létrehoz egy hálózati biztonsági csoportra vonatkozó szabályt [az az vm open-port parancsokkal](/cli/azure/vm#az_vm_open_port) a *3389-es porton.* Az Azure CLI-ről nyissa meg a következő hálózati biztonságicsoport-szabályt, ne a virtuális gép SSH-munkamenetét:

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Linux rendszerű virtuális gép csatlakoztatása Távoli asztal ügyfélhez

Nyissa meg a helyi távoli asztali ügyfelet, és csatlakozzon a Linux rendszerű virtuális gép IP-címhez vagy DNS-nevéhez. 

:::image type="content" source="media/use-remote-desktop/remote-desktop.png" alt-text="A távoli asztali ügyfél képernyőképe.":::

Adja meg a virtuális gépen található felhasználói fiók felhasználónevét és jelszavát a következőképpen:

:::image type="content" source="media/use-remote-desktop/xrdp-login.png" alt-text="Képernyőkép az xrdp bejelentkezési képernyőről.":::

A hitelesítés után az xfce asztali környezet betöltődik, és az alábbi példához hasonlóan néz ki:

![xfce asztali környezet xrdp-kapcsolaton keresztül](./media/use-remote-desktop/xfce-desktop-environment.png)

Ha a helyi RDP-ügyfél hálózati szintű hitelesítést (NLA) használ, előfordulhat, hogy le kell tiltania ezt a kapcsolati beállítást. Az XRDP jelenleg nem támogatja az NLA-t. Alternatív RDP-megoldásokat is talál, amelyek támogatják az NLA-t, például a [FreeRDP-t.](https://www.freerdp.com)


## <a name="troubleshoot"></a>Hibaelhárítás
Ha nem tud csatlakozni a Linux rendszerű virtuális gépéhez egy Távoli asztal-ügyféllel, a Linux rendszerű virtuális gépen a használatával ellenőrizze, hogy a virtuális gép figyel-e `netstat` az RDP-kapcsolatokra az alábbiak szerint:

```bash
sudo netstat -plnt | grep rdp
```

Az alábbi példa a várt módon figyelő virtuális gépet mutatja be a 3389-es TCP-porton:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Ha az *xrdp-sesman* szolgáltatás nem figyel, ubuntus virtuális gépen indítsa újra a szolgáltatást a következőképpen:

```bash
sudo service xrdp restart
```

Tekintse át az Ubuntu virtuális gép */var/log* naplóit, és ellenőrizze, hogy miért nem válaszol a szolgáltatás. A syslogot távoli asztali kapcsolaton keresztül is figyelheti a hibák megtekintésére:

```bash
tail -f /var/log/syslog
```

Más Linux-disztribúciók, például a Red Hat Enterprise Linux és a SUSE különböző módokon indíthatják újra a szolgáltatásokat és a naplófájlok alternatív helyeit, hogy átlássa őket.

Ha nem kap választ a távoli asztali ügyfélen, és nem lát eseményeket a rendszernaplóban, ez a viselkedés azt jelzi, hogy a távoli asztali forgalom nem tudja elérni a virtuális gépet. Tekintse át a hálózati biztonsági csoport szabályait, és ellenőrizze, hogy van-e olyan szabálya, amely engedélyezi a TCP-t a 3389-es porton. További információ: [Alkalmazáskapcsolati problémák elhárítása.](/troubleshoot/azure/virtual-machines/troubleshoot-app-connection)


## <a name="next-steps"></a>Következő lépések
Az SSH-kulcsok Linux rendszerű virtuális gépeken való létrehozásával és használatával kapcsolatos további információkért lásd: SSH-kulcsok létrehozása Linux rendszerű virtuális gépekhez az [Azure-ban.](mac-create-ssh-keys.md)

Az SSH Windowsból történő használatával kapcsolatos információkért lásd: [SSH-kulcsok használata Windows rendszeren.](ssh-from-windows.md)