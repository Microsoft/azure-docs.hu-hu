---
title: Rendszerhéj-parancsfájlok futtatása egy Linux rendszerű virtuális gépen az Azure-ban
description: Ez a témakör ismerteti, hogyan lehet egy Azure-beli Linuxos virtuális gép futtatása paranccsal belül parancsfájlok futtatása
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: fe0547c1ddb89b8d9ab3e876e83bbdea17a58743
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64570601"
---
# <a name="run-shell-scripts-in-your-linux-vm-with-run-command"></a>A Linux rendszerű virtuális gép futtatása paranccsal shell-szkript futtatása

Futtassa a parancsot használja a Virtuálisgép-ügynök-beli Linuxos virtuális Gépen belül shell-szkript futtatásához. Ezek a parancsfájlok vagy általános számítógép-felügyelet is használható, és segítségével gyorsan diagnosztizálhatja és a virtuális gép hozzáférés és a hálózati problémák elhárítására és a egy jó állapotba a virtuális gép lekérése.

## <a name="benefits"></a>Előnyök

A virtuális gépek eléréséhez használható több lehetőség közül. A futtatási parancs futtathatók a parancsprogramok segítségével távolról a Virtuálisgép-ügynököt a virtuális gépeken. A futtatási parancs is használható az Azure Portalon keresztül [REST API-val](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), vagy [Azure CLI-vel](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) Linux rendszerű virtuális gépekhez.

Ez a funkció akkor hasznos, minden olyan esetben, ha szeretne futtatni egy parancsfájlt a virtual machines szolgáltatáson belül, és a egy egyetlen módja hibaelhárítása és orvoslása egy virtuális gépet, amely nem rendelkezik az RDP vagy SSH-port megnyitásához miatt nem megfelelő hálózati vagy a rendszergazda felhasználó konfiguráció.

## <a name="restrictions"></a>Korlátozások

Az alábbiakban találhatók, amikor futtatása paranccsal korlátozásainak listáját.

* Kimeneti korlátozódik a legutóbb 4096 bájtos
* A parancsfájl futtatása nagyjából 20 másodperc minimális idő
* Parancsfájlok futtatása alapértelmezés szerint emelt szintű felhasználói Linux rendszeren
* Egyszerre csak egy parancsfájl futhat.
* Parancsfájlok, amelyek információkat (interaktív mód) kérése nem támogatottak.
* Nem vethető el a parancsprogram futtatásához
* A maximális idő egy parancsfájlt az 90 perc után, amelyben az időkorlátja
* A virtuális gépről kimenő kapcsolat szükséges a parancsfájl eredményét adja vissza.

> [!NOTE]
> A megfelelő működéshez, a futtatása paranccsal kapcsolódnia kell (a 443-as port) az Azure nyilvános IP-címeket. Ha a bővítmény nem rendelkezik a fenti végpontokkal való hozzáférést, a parancsfájlok sikeresen lefutott, de az eredményeket ad vissza. Ha blokkolja a forgalmat a virtuális gépen, akkor használhatja [szolgáltatáscímkéket](../../virtual-network/security-overview.md#service-tags) , hogy a forgalom az Azure nyilvános IP-címek használatával a `AzureCloud` címke.

## <a name="azure-cli"></a>Azure CLI

Az alábbiakban egy példa a használatával a [az virtuális gép Futtatás-parancs](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) parancsot beírva futtassa azt a héjparancsfájlt-beli Linux rendszerű virtuális gépen.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Egy másik felhasználóként parancsok futtatásához használhatja `sudo -u` használata egy felhasználói fiókot szeretne megadni.

## <a name="azure-portal"></a>Azure Portal

Keresse meg a virtuális gép [Azure](https://portal.azure.com) válassza **futtatása paranccsal** alatt **műveletek**. Megjelennek az elérhető parancsok futtatásához a virtuális gép listáját.

![Futtassa a parancsot listája](./media/run-command/run-command-list.png)

Válassza ki a futtatni kívánt parancs. Parancsai közül néhányat, előfordulhat, nem kötelező vagy szükséges bemeneti paramétereket. Ezek a parancsok a paramétereket, hogy adja meg a bemeneti szöveges mezők jelennek meg. Megtekintheti a parancsfájl által bővítése futtató minden egyes parancsnál **parancsfájl megjelenítése**. **RunShellScript** eltér a többi parancs, mert Ön a saját egyéni parancsfájl biztosít.

> [!NOTE]
> A beépített parancsok, amelyek nem szerkeszthető.

Ha ki van választva a parancsot, kattintson **futtatása** a parancsfájl futtatásához. A szkript fut, és amikor elkészült, adja vissza a kimenet és az esetleges hibákat a kimeneti ablakban. A következő képernyőképen látható egy példa kimenet futtatását a **ifconfig** parancsot.

![Futtassa a parancsot parancsprogram kimenete](./media/run-command/run-command-script-output.png)

## <a name="available-commands"></a>Elérhető parancsok

Ez a táblázat a Linux rendszerű virtuális gépekhez elérhető parancsok listáját jeleníti meg. A **RunShellScript** parancs bármilyen kívánt egyéni szkript futtatásához használható.

|**Name (Név)**|**Leírás**|
|---|---|
|**RunShellScript**|Linux shellt szkriptet hajt végre.|
|**ifconfig**| Összes hálózati adapter konfigurációjának beszerzéséhez.|

## <a name="limiting-access-to-run-command"></a>Futtatása paranccsal való hozzáférés korlátozása

A futtatási parancsok listázása és a egy parancs részleteit megjelenítő szükséges a `Microsoft.Compute/locations/runCommands/read` engedélyt az előfizetés szintjén, amely a beépített [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkör, és magasabb.

Parancs futtatásához szükség van a `Microsoft.Compute/virtualMachines/runCommand/action` engedélyt az előfizetés szintjén, amely a [virtuális gépek Közreműködője](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szerepkör és újabb.

Használhatja az egyik a [beépített](../../role-based-access-control/built-in-roles.md) szerepkörök, vagy hozzon létre egy [egyéni](../../role-based-access-control/custom-roles.md) szerepkör-parancs futtatása.

## <a name="next-steps"></a>További lépések

Látható, [szkriptek futtatása a Linux rendszerű virtuális gép](run-scripts-in-vm.md) parancsprogramok és parancsok a virtuális Géphez a távoli futtatásához egyéb módjaival kapcsolatos.
