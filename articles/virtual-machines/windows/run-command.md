---
title: PowerShell-parancsfájlok futtatásához egy Windows-beli virtuális gépen az Azure-ban
description: Ez a témakör ismerteti a PowerShell-parancsfájlok futtatása paranccsal, egy Windows Azure virtuális gépen belüli futtatásáról
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 23973445992ceaeb0cd3bc0589665f2fac5b64e5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64575334"
---
# <a name="run-powershell-scripts-in-your-windows-vm-with-run-command"></a>PowerShell-szkriptek futtatása a Windows virtuális gép futtatása paranccsal

Futtassa a parancsot használja a Virtuálisgép-ügynök Windows Azure virtuális Gépen belül a PowerShell-parancsfájlok futtatását. Ezek a parancsfájlok vagy általános számítógép-felügyelet is használható, és segítségével gyorsan diagnosztizálhatja és a virtuális gép hozzáférés és a hálózati problémák elhárítására és a egy jó állapotba a virtuális gép lekérése.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="benefits"></a>Előnyök

A virtuális gépek eléréséhez használható több lehetőség közül. A futtatási parancs futtathatók a parancsprogramok segítségével távolról a Virtuálisgép-ügynököt a virtuális gépeken. A futtatási parancs is használható az Azure Portalon keresztül [REST API-val](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), vagy [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) Windows virtuális gépek számára.

Ez a funkció akkor hasznos, minden olyan esetben, ha szeretne futtatni egy parancsfájlt a virtual machines szolgáltatáson belül, és a egy egyetlen módja hibaelhárítása és orvoslása egy virtuális gépet, amely nem rendelkezik az RDP vagy SSH-port megnyitásához miatt nem megfelelő hálózati vagy a rendszergazda felhasználó konfiguráció.

## <a name="restrictions"></a>Korlátozások

A következő korlátozások vonatkoznak, ha futtatása paranccsal:

* Kimeneti korlátozódik utolsó 4096 bájtos
* A minimális ideje a parancsfájl futtatása után körülbelül 20 másodperc
* A Windows rendszert futtató parancsfájlok
* Egyszerre csak egy parancsfájl futhat.
* Parancsfájlok, amelyek információkat (interaktív mód) kérése nem támogatottak.
* Nem vethető el a parancsprogram futtatásához
* A maximális idő egy parancsfájlt az 90 perc után, amelyben az időkorlátja
* A virtuális gépről kimenő kapcsolat szükséges a parancsfájl eredményét adja vissza.

> [!NOTE]
> A megfelelő működéshez, a futtatása paranccsal kapcsolódnia kell (a 443-as port) az Azure nyilvános IP-címeket. Ha a bővítmény nem rendelkezik a fenti végpontokkal való hozzáférést, a parancsfájlok sikeresen lefutott, de az eredményeket ad vissza. Ha blokkolja a forgalmat a virtuális gépen, akkor használhatja [szolgáltatáscímkéket](../../virtual-network/security-overview.md#service-tags) , hogy a forgalom az Azure nyilvános IP-címek használatával a `AzureCloud` címke.

## <a name="run-a-command"></a>Parancs futtatása

Keresse meg a virtuális gép [Azure](https://portal.azure.com) válassza **futtatása paranccsal** alatt **műveletek**. Megjelennek az elérhető parancsok futtatásához a virtuális gép listáját.

![Futtassa a parancsot listája](./media/run-command/run-command-list.png)

Válassza ki a futtatni kívánt parancs. Parancsai közül néhányat, előfordulhat, nem kötelező vagy szükséges bemeneti paramétereket. Ezek a parancsok a paramétereket, hogy adja meg a bemeneti szöveges mezők jelennek meg. Megtekintheti a parancsfájl által bővítése futtató minden egyes parancsnál **parancsfájl megjelenítése**. **RunPowerShellScript** eltér a többi parancs, mert Ön a saját egyéni parancsfájl biztosít.

> [!NOTE]
> A beépített parancsok, amelyek nem szerkeszthető.

Ha ki van választva a parancsot, kattintson **futtatása** a parancsfájl futtatásához. A szkript fut, és amikor elkészült, adja vissza a kimenet és az esetleges hibákat a kimeneti ablakban. A következő képernyőképen látható egy példa kimenet futtatását a **RDPSettings** parancsot.

![Futtassa a parancsot parancsprogram kimenete](./media/run-command/run-command-script-output.png)

## <a name="commands"></a>Parancsok

Ez a táblázat Windows virtuális gépek esetében elérhető parancsok listáját jeleníti meg. A **RunPowerShellScript** parancs bármilyen kívánt egyéni szkript futtatásához használható.

|**Name (Név)**|**Leírás**|
|---|---|
|**RunPowerShellScript**|Egy PowerShell-parancsprogram végrehajtása|
|**EnableRemotePS**|Konfigurálja a számítógépet, hogy engedélyezze a távoli PowerShell.|
|**EnableAdminAccount**|Ha a helyi rendszergazdai fiók le van tiltva, és ha igen lehetővé teszi, hogy ellenőrzi.|
|**IPConfig**| Részletes információk megjelenítése az IP-cím, alhálózati maszk és az alapértelmezett átjáró mindegyik adapterhez kötve a TCP/IP.|
|**RDPSettings**|Beállításjegyzék-beállítások és házirend beállításainak ellenőrzi. Házirend műveleteket javasol, ha a gép része egy tartománynak, vagy módosítja a beállításokat az alapértelmezett értékekre.|
|**ResetRDPCert**|Eltávolítja az RDP-figyelő kötött SSL-tanúsítványt, és az RDP-listerner biztonsági visszaállítja az alapértelmezett. Akkor használja ezt a parancsfájlt, ha bármilyen problémát észlel, a tanúsítvány.|
|**SetRDPPort**|Beállítja az alapértelmezett vagy felhasználó megadott portszám távoli asztali kapcsolatok. Lehetővé teszi, hogy a port hozzáférése bejövő tűzfalszabályt.|

## <a name="powershell"></a>PowerShell

Az alábbiakban egy példa a használatával a [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) parancsmag-beli virtuális gépen egy PowerShell-szkript futtatásához. A parancsmagnak a hivatkozott parancsprogram a `-ScriptPath` paramétereként helyi, ahol a parancsmag futtatásához.


```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Futtatása paranccsal való hozzáférés korlátozása

A futtatási parancsok listázása és a egy parancs részleteit megjelenítő szükséges a `Microsoft.Compute/locations/runCommands/read` engedélyt az előfizetés szintjén, amely a beépített [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkör, és magasabb.

Parancs futtatásához szükség van a `Microsoft.Compute/virtualMachines/runCommand/action` engedélyt az előfizetés szintjén, amely a [virtuális gépek Közreműködője](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szerepkör és újabb.

Használhatja az egyik a [beépített](../../role-based-access-control/built-in-roles.md) szerepkörök, vagy hozzon létre egy [egyéni](../../role-based-access-control/custom-roles.md) szerepkör-parancs futtatása.

## <a name="next-steps"></a>További lépések

Látható, [szkriptek futtatása a Windows virtuális gép](run-scripts-in-vm.md) parancsprogramok és parancsok a virtuális Géphez a távoli futtatásához egyéb módjaival kapcsolatos.
