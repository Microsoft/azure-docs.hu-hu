---
title: Bevezetés a FreeBSD Azure-on való használatába
description: Tudnivalók a FreeBSD rendszerű virtuális gépek használatáról az Azure-ban
author: thomas1206
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: mimckitt
ms.openlocfilehash: 0451047cd0fa31d27553a33b0c9318e03ffa222e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558950"
---
# <a name="introduction-to-freebsd-on-azure"></a>Bevezetés a FreeBSD Azure-on való használatába
Ez a cikk áttekintést nyújt a FreeBSD rendszerű virtuális gépek Azure-ban való futtatásáról.

## <a name="overview"></a>Áttekintés
A FreeBSD for Microsoft Azure a modern kiszolgálók, asztali számítógépek és beágyazott platformok teljesítményére szolgáló fejlett számítógépes operációs rendszer.

A Microsoft Corporation az Azure-ban elérhető FreeBSD-lemezképeket készít az Azure-beli [virtuális gép vendég ügynökével](https://github.com/Azure/WALinuxAgent/) előre konfigurálva. A Microsoft jelenleg a következő FreeBSD-verziókat kínálja lemezképként:

- A FreeBSD 10,4 Az Azure piactéren
- A FreeBSD 11,2 Az Azure piactéren
- A FreeBSD 12,0 az Azure piactéren

Az ügynök feladata a FreeBSD virtuális gép és az Azure-háló közötti kommunikáció, például a virtuális gép üzembe helyezése az első használatnál (Felhasználónév, jelszó vagy SSH-kulcs, állomásnév stb.), valamint a szelektív virtuálisgép-bővítmények funkcióinak engedélyezése.

A FreeBSD jövőbeli verzióihoz hasonlóan a stratégia is naprakész marad, és hamarosan elérhetővé teszi a legújabb kiadásokat a FreeBSD Release Engineering csapatának közzétételekor.

### <a name="create-a-freebsd-vm-through-azure-cli-on-freebsd"></a>FreeBSD-alapú virtuális gép létrehozása az Azure CLI-vel a FreeBSD-n
Először telepítenie kell az [Azure CLI](/cli/azure/get-started-with-azure-cli) -t, de a következő parancsot egy FreeBSD-gépen.

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

Ha a bash nincs telepítve a FreeBSD rendszerű gépen, futtassa a következő parancsot a telepítés előtt. 

```bash
sudo pkg install bash
```

Ha a Python nincs telepítve a FreeBSD rendszerű gépen, futtassa a következő parancsokat a telepítés előtt. 

```bash
sudo pkg install python35
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.5 /usr/local/bin/python
```

A telepítés során a rendszer megkérdezi `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)` . Ha megválaszolja `y` és beírja `/etc/rc.conf` `a path to an rc file to update` a as-t, akkor előfordulhat, hogy a probléma teljesül `ERROR: [Errno 13] Permission denied` . A probléma megoldásához az írási jogot az aktuális felhasználónak kell megadnia a fájlhoz `etc/rc.conf` .

Most már bejelentkezhet az Azure-ba, és létrehozhatja a FreeBSD rendszerű virtuális gépet. Az alábbi példa egy FreeBSD 11,0 virtuális gép létrehozását mutatja be. A paramétert `--public-ip-address-dns-name` globálisan egyedi DNS-névvel is hozzáadhatja egy újonnan létrehozott nyilvános IP-címhez. 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Ezután bejelentkezhet a FreeBSD rendszerű virtuális gépre a fenti üzemelő példány kimenetében kinyomtatott IP-cím használatával. 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>VM-bővítmények a FreeBSD-hez
A következő a FreeBSD-ben támogatott virtuálisgép-bővítmények.

### <a name="vmaccess"></a>VMAccess
A [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) bővítmény a következőket teheti:

* Az eredeti sudo-felhasználó jelszavának alaphelyzetbe állítása.
* Hozzon létre egy új sudo-felhasználót a megadott jelszóval.
* Állítsa a nyilvános gazda kulcsot a megadott kulccsal.
* Állítsa alaphelyzetbe a virtuális gép kiépítés során megadott nyilvános gazdagép kulcsát, ha nincs megadva a gazdagép kulcsa.
* Nyissa meg az SSH-portot (22), és állítsa vissza a sshd_config, ha reset_ssh True (igaz) értékre van állítva.
* Távolítsa el a meglévő felhasználót.
* Lemezek ellenőrzéséhez.
* Javítsa ki a hozzáadott lemezt.

### <a name="customscript"></a>CustomScript
A [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) bővítmény a következőket teheti:

* Ha meg van adni, töltse le a testreszabott parancsfájlokat az Azure Storage-ból vagy a külső nyilvános tárolóból (például GitHub).
* Futtassa a belépési pont parancsfájlját.
* A beágyazott parancsok támogatása.
* A Windows stílusú sortörést a rendszerhéjban és a Python-szkriptekben automatikusan konvertálhatja.
* Az ANYAGJEGYZÉK eltávolítása a rendszerhéjból és a Python-parancsfájlokból automatikusan történik.
* Bizalmas adatok védelme a CommandToExecute-ben.

> [!NOTE]
> A FreeBSD VM jelenleg csak az 1. x verziójú CustomScript támogatja.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Hitelesítés: felhasználónevek, jelszavak és SSH-kulcsok
Ha a Azure Portal használatával hoz létre egy FreeBSD rendszerű virtuális gépet, meg kell adnia a felhasználónevet, a jelszót vagy az SSH nyilvános kulcsát.
A FreeBSD rendszerű virtuális gépek Azure-beli üzembe helyezéséhez használt felhasználónevek nem egyeznek meg a virtuális gépen (például "root") már meglévő rendszerfiókok (UID <100) neveivel.
Jelenleg csak az RSA SSH-kulcs támogatott. A többsoros SSH-kulcsnak a `---- BEGIN SSH2 PUBLIC KEY ----` és a végződéssel kell kezdődnie `---- END SSH2 PUBLIC KEY ----` .

## <a name="obtaining-superuser-privileges"></a>Rendszergazdai jogosultságok beszerzése
Az Azure-beli virtuálisgép-példány üzembe helyezése során megadott felhasználói fiók egy kiemelt fiók. A sudo csomag a közzétett FreeBSD-rendszerképbe lett telepítve.
Miután bejelentkezett ezzel a felhasználói fiókkal, a parancs szintaxisa segítségével root-ként futtathatja a parancsokat.

```
$ sudo <COMMAND>
```

Igény szerint megszerezheti a legfelső szintű rendszerhéjt is `sudo -s` .

## <a name="known-issues"></a>Ismert problémák
Az [Azure VM-vendég ügynökének](https://github.com/Azure/WALinuxAgent/) 2.2.2-es verziója [ismert hibát](https://github.com/Azure/WALinuxAgent/pull/517) tartalmaz, amely az Azure-beli FreeBSD rendszerű virtuális gépek üzembe helyezési hibáját okozza. A javítást az [Azure VM Guest Agent](https://github.com/Azure/WALinuxAgent/) 2.2.3-es és újabb kiadásaiban rögzítettük. 

## <a name="next-steps"></a>Következő lépések
* Nyissa meg az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/thefreebsdfoundation.freebsd-12_2?tab=Overview) -t egy FreeBSD rendszerű virtuális gép létrehozásához.
