---
title: Azure Linux rendszerű virtuális gép hozzáférésének visszaállítása
description: A rendszergazda felhasználók kezelése és a Linux rendszerű virtuális gépek hozzáférésének alaphelyzetbe állítása a VMAccess-bővítmény és az Azure CLI használatával
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 05/10/2018
ms.openlocfilehash: e0e63762fc151d93cb34885c04dd4d03285c29f1
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557046"
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli"></a>A rendszergazda felhasználók, SSH és a Linux rendszerű virtuális gépeken található lemezek megkeresése és javítása az Azure CLI-vel való VMAccess-bővítmény használatával
## <a name="overview"></a>Áttekintés
A Linux rendszerű virtuális gépen lévő lemez hibákat jelenít meg. Valahogy alaphelyzetbe állítja a linuxos virtuális gép legfelső szintű jelszavát, vagy véletlenül törölte az SSH titkos kulcsát. Ha az adatközpont napjaiban történt vissza, akkor oda kell vezetnie, majd meg kell nyitnia a KVM-t, hogy lekérje a kiszolgáló konzolját. Tekintse át az Azure VMAccess bővítményt, amely a KVM-kapcsolót használja, amely lehetővé teszi a konzol elérését a Linuxhoz való hozzáférés alaphelyzetbe állításához vagy a lemez szintű karbantartáshoz.

Ebből a cikkből megtudhatja, hogyan használhatja az Azure VMAccess bővítményt a lemezek vizsgálatához és javításához, a felhasználói hozzáférés alaphelyzetbe állításához, a rendszergazdai felhasználói fiókok kezeléséhez, illetve a Linux SSH-konfigurációjának frissítéséhez, ha Azure Resource Manager virtuális gépekként fut. Ha klasszikus virtuális gépeket kell kezelnie – kövesse a [klasszikus VM-dokumentációban](/previous-versions/azure/virtual-machines/linux/classic/reset-access-classic)található utasításokat. 
 
> [!NOTE]
> Ha a VMAccess bővítmény használatával állítja alaphelyzetbe a virtuális gép jelszavát a HRE bejelentkezési bővítmény telepítése után, újra kell futtatnia a HRE-bejelentkezési bővítményt, hogy újra engedélyezze a HRE-bejelentkezést a gépen.

## <a name="prerequisites"></a>Előfeltételek
### <a name="operating-system"></a>Operációs rendszer

A virtuális gépek hozzáférési bővítménye a következő Linux-disztribúciók esetében futtatható:

| Disztribúció | Verzió |
|---|---|
| Ubuntu | 16,04 LTS, 14,04 LTS és 12,04 LTS |
| Debian | Debian 7.9 +, 8.2 + |
| Red Hat | RHEL 6.7 +, 7.1 + |
| Oracle Linux | 6.4 +, 7.0 + |
| SUSE | 11 és 12 |
| OpenSuse | openSUSE LEAP 42.2 + |
| CentOS | CentOS 6.3 +, 7.0 + |
| CoreOS | 494.4.0 + |

## <a name="ways-to-use-the-vmaccess-extension"></a>A VMAccess bővítmény használati módjai
A Linux rendszerű virtuális gépeken kétféleképpen használhatja a VMAccess-bővítményt:

* Használja az Azure CLI-t és a szükséges paramétereket.
* [Használjon nyers JSON-fájlokat a VMAccess-bővítmény folyamatán](#use-json-files-and-the-vmaccess-extension) , majd járjon el.

Az alábbi példák az [az VM User](/cli/azure/vm/user) parancsait használják. Ezen lépések elvégzéséhez szüksége lesz a legújabb [Azure CLI](/cli/azure/install-az-cli2) -re, és be kell jelentkeznie egy Azure-fiókba az [az login](/cli/azure/reference-index)használatával.

## <a name="update-ssh-key"></a>SSH-kulcs frissítése
Az alábbi példa frissíti a felhasználó SSH-kulcsát a `azureuser` nevű virtuális gépen `myVM` :

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **Megjegyzés:** A `az vm user update` parancs hozzáfűzi az új nyilvános kulcs szövegét a `~/.ssh/authorized_keys` virtuális gép rendszergazdai felhasználójának fájljához. Ez nem helyettesíti vagy nem távolítja el a meglévő SSH-kulcsokat. Ez a beállítás nem távolítja el az üzembe helyezéskor vagy a későbbi frissítésekben beállított korábbi kulcsokat a VMAccess-bővítmény használatával.

## <a name="reset-password"></a>Új jelszó létrehozása
A következő példa alaphelyzetbe állítja a felhasználó jelszavát a `azureuser` nevű virtuális gépen `myVM` :

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>SSH újraindítása
Az alábbi példa újraindítja az SSH démont, és visszaállítja az SSH-konfigurációt egy nevű virtuális gép alapértelmezett értékeire `myVM` :

```azurecli-interactive
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Rendszergazdai/sudo felhasználó létrehozása
A következő példa egy `myNewUser` **sudo** engedélyekkel ellátott felhasználót hoz létre. A fiók SSH-kulcsot használ a hitelesítéshez a nevű virtuális gépen `myVM` . Ez a módszer úgy lett kialakítva, hogy segítse a virtuális gépekhez való hozzáférés visszaszerzését abban az esetben, ha az aktuális hitelesítő adatok elvesznek vagy elfelejtenek. Ajánlott eljárásként korlátozni kell a **sudo** engedélyekkel rendelkező fiókokat.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="delete-a-user"></a>Felhasználó törlése
A következő példa töröl egy nevű felhasználót `myNewUser` a nevű virtuális gépen `myVM` :

```azurecli-interactive
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```

## <a name="use-json-files-and-the-vmaccess-extension"></a>A JSON-fájlok és a VMAccess bővítmény használata
Az alábbi példák nyers JSON-fájlokat használnak. Az [az VM Extension set](/cli/azure/vm/extension) paranccsal hívja meg a JSON-fájlokat. Ezek a JSON-fájlok az Azure-sablonokból is meghívhatók. 

### <a name="reset-user-access"></a>Felhasználói hozzáférés alaphelyzetbe állítása
Ha megszakadt a hozzáférése a Linux rendszerű virtuális gép gyökeréhez, elindíthat egy VMAccess-szkriptet a felhasználó SSH-kulcsának vagy jelszavának frissítéséhez.

Egy felhasználó nyilvános SSH-kulcsának frissítéséhez hozzon létre egy nevű fájlt, `update_ssh_key.json` és adja hozzá a beállításokat a következő formátumban. Helyettesítse be a és a paraméterek saját értékeit `username` `ssh_key` :

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

Futtassa a VMAccess parancsfájlt az alábbiakkal:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings update_ssh_key.json
```

Egy felhasználói jelszó alaphelyzetbe állításához hozzon létre egy nevű fájlt, `reset_user_password.json` és adja hozzá a beállításokat a következő formátumban. Helyettesítse be a és a paraméterek saját értékeit `username` `password` :

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

Futtassa a VMAccess parancsfájlt az alábbiakkal:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="restart-ssh"></a>SSH újraindítása
Az SSH démon újraindításához és az SSH-konfiguráció alapértelmezett értékre való visszaállításához hozzon létre egy nevű fájlt `reset_sshd.json` . Adja hozzá a következő tartalmat:

```json
{
  "reset_ssh": true
}
```

Futtassa a VMAccess parancsfájlt az alábbiakkal:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-administrative-users"></a>Rendszergazda felhasználók kezelése

Ha olyan **sudo** -engedélyekkel rendelkező felhasználót szeretne létrehozni, amely SSH-kulcsot használ a hitelesítéshez, hozzon létre egy nevű fájlt, `create_new_user.json` és adja hozzá a beállításokat a következő formátumban. Helyettesítse be a és a paraméterek saját értékeit `username` `ssh_key` . Ez a módszer úgy lett kialakítva, hogy segítse a virtuális gépekhez való hozzáférés visszaszerzését abban az esetben, ha az aktuális hitelesítő adatok elvesznek vagy elfelejtenek. Ajánlott eljárásként korlátozni kell a **sudo** engedélyekkel rendelkező fiókokat.

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Futtassa a VMAccess parancsfájlt az alábbiakkal:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

Egy felhasználó törléséhez hozzon létre egy nevű fájlt, `delete_user.json` és adja hozzá a következő tartalmat. Helyettesítse be a saját értékét a következő `remove_user` paraméterhez:

```json
{
  "remove_user":"myNewUser"
}
```

Futtassa a VMAccess parancsfájlt az alábbiakkal:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>A lemez keresése vagy javítása
A VMAccess használatával a linuxos virtuális géphez hozzáadott lemezeket is megtekintheti és kijavíthatja.

A lemez vizsgálatához és kijavításához hozzon létre egy nevű fájlt, `disk_check_repair.json` és adja hozzá a beállításokat a következő formátumban. Helyettesítse be a saját értékét a következő névre `repair_disk` :

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
}
```

Futtassa a VMAccess parancsfájlt az alábbiakkal:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```
## <a name="troubleshoot-and-support"></a>Hibakeresés és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A bővítmények állapotával kapcsolatos adatok a Azure Portalból és az Azure CLI használatával kérhetők le. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa az alábbi parancsot az Azure CLI használatával.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel az [MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/forums/)is. Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a támogatás kérése lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).
