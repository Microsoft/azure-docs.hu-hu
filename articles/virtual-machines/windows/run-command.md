---
title: PowerShell-szkriptek futtatása Windows rendszerű virtuális gépen az Azure-ban
description: Ez a témakör azt ismerteti, hogyan futtathat PowerShell-szkripteket egy Azure Windows rendszerű virtuális gépen az parancsfuttatás funkcióval
services: automation
ms.service: virtual-machines
ms.collection: windows
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: carmonm
ms.openlocfilehash: 3271f5461447439772b656b8927a54057c8b0c7e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786404"
---
# <a name="run-powershell-scripts-in-your-windows-vm-by-using-run-command"></a>PowerShell-szkriptek futtatása Windows rendszerű virtuális gépen az parancsfuttatás

A parancsfuttatás funkció a virtuálisgép-ügynököt használja PowerShell-szkriptek futtatására egy Azure-beli Windows rendszerű virtuális gépen. Ezeket a szkripteket a gépek vagy alkalmazások általános kezelésére használhatja. Segíthetnek a virtuális gépek hozzáférési és hálózati problémáinak gyors diagnosztizálásában és megoldásában, és a virtuális gép megfelelő állapotba való visszahozásához.



## <a name="benefits"></a>Előnyök

A virtuális gépeket többféleképpen is elérheti. parancsfuttatás szkripteket futtathat távolról a virtuálisgép-ügynök használatával. A windowsos parancsfuttatás a Azure Portal, [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)a [PowerShell](/powershell/module/az.compute/invoke-azvmruncommand) használatával.

Ez a képesség minden olyan helyzetben hasznos, ahol egy szkriptet szeretne futtatni egy virtuális gépen belül. Ez az egyik egyetlen módja a nem megfelelő hálózati vagy rendszergazdai felhasználói konfiguráció miatt nem megnyitott RDP- vagy SSH-porttal nem rendelkező virtuális gépek hibaelhárításának és szervizelésének.

## <a name="restrictions"></a>Korlátozások

A következő korlátozások érvényesek a következő parancsfuttatás:

* A kimenet az utolsó 4096 bájtra van korlátozva.
* A szkriptek futtatásának minimális ideje körülbelül 20 másodperc.
* A parancsfájlok Rendszerként futnak Windows rendszeren.
* Egyszerre egy szkript futtatható.
* Az információkat bekért parancsfájlok (interaktív mód) nem támogatottak.
* Futó szkriptet nem lehet megszakítani.
* A szkriptek futtatásának maximális ideje 90 perc. Ezután időkorrekta lesz.
* A szkript eredményeinek visszaadásához kimenő kapcsolatra van szükség a virtuális gépről.
* Nem ajánlott olyan szkriptet futtatni, amely a virtuálisgép-ügynök leállítását vagy frissítését okozhatja. Ez átmeneti állapotba hagyhatja a bővítményt, ami időtúllépéshez vezet.

> [!NOTE]
> A megfelelő működéshez a parancsfuttatás azure-beli nyilvános IP-címekkel való kapcsolatra (443-as port) van szükség. Ha a bővítmény nem rendelkezik hozzáféréssel ezekhez a végponthoz, előfordulhat, hogy a szkriptek sikeresen futnak, de nem adják vissza az eredményeket. Ha blokkolja a forgalmat a virtuális gépen, szolgáltatáscímkék használatával engedélyezheti a nyilvános Azure IP-címekre való forgalmat a címke [](../../virtual-network/network-security-groups-overview.md#service-tags) `AzureCloud` használatával.

## <a name="available-commands"></a>Elérhető parancsok

Ez a táblázat a Windows rendszerű virtuális gépekhez elérhető parancsok listáját tartalmazza. A **RunPowerShellScript paranccsal** bármilyen egyéni szkriptet futtathat. Ha az Azure CLI-t vagy a PowerShellt használja egy parancs futtatására, a vagy paraméterhez megadott értéknek az alábbi értékek egyikének `--command-id` `-CommandId` kell lennie. Ha olyan értéket ad meg, amely nem elérhető parancs, a következő hibaüzenet jelenik meg:

```error
The entity was not found in this Azure location
```

|**Név**|**Leírás**|
|---|---|
|**FuttatásPowerShellScript**|Egy PowerShell-szkriptet futtat.|
|**EnableRemotePS**|A távoli PowerShell engedélyezésére konfigurálja a gépet.|
|**EnableAdminAccount (EnableAdminAccount)**|Ellenőrzi, hogy a helyi rendszergazdai fiók le van-e tiltva, és hogy engedélyezi-e.|
|**Ipconfig**| Részletes információkat nyújt az IP-címről, az alhálózati maszkról és az alapértelmezett átjáróról a TCP/IP-hez kötött egyes adapterek esetében.|
|**RDPSettings**|Ellenőrzi a beállításjegyzék és a tartományi házirend beállításait. Szabályzatműveleteket javasol, ha a gép egy tartomány része, vagy az alapértelmezett értékekre módosítja a beállításokat.|
|**ResetRDPCert**|Eltávolítja az RDP- listenerhez kötött TLS-/SSL-tanúsítványt, és visszaállítja az RDP- listener biztonsági beállítását az alapértelmezettre. Használja ezt a szkriptet, ha problémát lát a tanúsítvánnyal kapcsolatban.|
|**SetRDPPort (RdpPort beállítása)**|Beállítja az alapértelmezett vagy a felhasználó által megadott portszámot a Távoli asztal számára. Engedélyezi a port bejövő elérésére vonatkozó tűzfalszabályokat.|

## <a name="azure-cli"></a>Azure CLI

Az alábbi példa az [az vm run-command paranccsal](/cli/azure/vm/run-command#az_vm_run_command_invoke) futtat egy héjszkprogramot egy Azure-beli Windows rendszerű virtuális gépen.

```azurecli-interactive
# script.ps1
#   param(
#       [string]$arg1,
#       [string]$arg2
#   )
#   Write-Host This is a sample script with parameters $arg1 and $arg2

az vm run-command invoke  --command-id RunPowerShellScript --name win-vm -g my-resource-group \
    --scripts @script.ps1 --parameters "arg1=somefoo" "arg2=somebar"
```

## <a name="azure-portal"></a>Azure Portal

A virtuális géphez [](https://portal.azure.com) a virtuális gép Azure Portal **a Parancsok futtatása lehetőséget** a **MŰVELETEK területen.** Megjelenik a virtuális gépen futtatandó elérhető parancsok listája.

![Parancsok listája](./media/run-command/run-command-list.png)

Válassza ki a futtatandó parancsot. Egyes parancsok opcionális vagy kötelező bemeneti paraméterekkel is lehetnek. Ezekhez a parancsokhoz a paraméterek szöveges mezőkként jelennek meg, amelyekben meg lehet adni a bemeneti értékeket. Minden parancshoz megtekintheti a futtatott szkriptet a Szkript megtekintése **kibontva.** **A RunPowerShellScript** eltér a többi parancstól, mivel lehetővé teszi saját egyéni szkriptek használatát.

> [!NOTE]
> A beépített parancsok nem szerkeszthetők.

A parancs kiválasztása után válassza a **Futtatás lehetőséget** a szkript futtatásához. A szkript a befejeződését követően visszaadja a kimenetet és az esetleges hibákat a kimeneti ablakban. Az alábbi képernyőképen egy példakimenet látható az **RDPSettings parancs futtatásából.**

![Parancsszk szkript kimenetének futtatása](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

Az alábbi példa az [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) parancsmag használatával futtat egy PowerShell-szkriptet egy Azure-beli virtuális gépen. A parancsmag elvárja, hogy a paraméterben hivatkozott szkript helyi legyen a parancsmag futtatásának `-ScriptPath` helyében.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>A Parancs futtatása funkcióhoz való hozzáférés korlátozása

A futtatás parancsok listázásához vagy a parancsok részleteinek megjelenítéséhez engedélyre van szükség `Microsoft.Compute/locations/runCommands/read` az előfizetés szintjén. A beépített Olvasó [szerepkör](../../role-based-access-control/built-in-roles.md#reader) és a magasabb szintek is erre az engedélyre vannak felépítve.

A parancsok futtatásához `Microsoft.Compute/virtualMachines/runCommand/action` engedélyre van szükség. A [Virtuális gépek közreműködője szerepkör](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) és a magasabb szintek is erre az engedélyre vannak felvéve.

Használhatja a beépített szerepkörök [valamelyikét,](../../role-based-access-control/built-in-roles.md) vagy létrehozhat egy egyéni szerepkört [a](../../role-based-access-control/custom-roles.md) parancsfuttatás.

## <a name="next-steps"></a>Következő lépések

A szkriptek és parancsok virtuális gépen való távoli futtatásának egyéb módjairól a [Szkriptek](run-scripts-in-vm.md)futtatása Windows rendszerű virtuális gépen.
