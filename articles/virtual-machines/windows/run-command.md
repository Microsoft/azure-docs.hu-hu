---
title: PowerShell-parancsfájlok futtatása Azure-beli Windows rendszerű virtuális gépen
description: Ez a témakör azt ismerteti, hogyan futtathat PowerShell-parancsfájlokat egy Azure Windows rendszerű virtuális gépen a parancs futtatása funkció használatával
services: automation
ms.service: virtual-machines
ms.collection: windows
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: carmonm
ms.openlocfilehash: e2cd8ee4095db235215a2beaa68975e819b474c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560684"
---
# <a name="run-powershell-scripts-in-your-windows-vm-by-using-run-command"></a>PowerShell-parancsfájlok futtatása a Windows rendszerű virtuális gépen a Futtatás parancs használatával

A futtatási parancs funkció a virtuális gép (VM) ügynököt használja PowerShell-parancsfájlok futtatására egy Azure Windows rendszerű virtuális gépen. Ezeket a szkripteket a gépek vagy alkalmazások általános kezelésére használhatja. Segítségükkel gyorsan diagnosztizálhatja és elháríthatja a virtuális gépek hozzáférési és hálózati problémáit, és visszaállíthatja a virtuális gépet jó állapotba.



## <a name="benefits"></a>Előnyök

Több módon is elérheti a virtuális gépeket. A Run parancs a virtuálisgép-ügynök használatával távolról is futtathat parancsfájlokat a virtuális gépeken. A Futtatás parancsot a Windows rendszerű virtuális gépekhez tartozó Azure Portalon, [Rest APIon](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)vagy [powershellen](/powershell/module/az.compute/invoke-azvmruncommand) keresztül használhatja.

Ez a képesség minden olyan helyzetben hasznos, ahol parancsfájlt szeretne futtatni egy virtuális gépen belül. Ez az egyik lehetőség az olyan virtuális gépek hibáinak elhárítására és szervizelésére, amelyek nem rendelkeznek az RDP-vagy SSH-porttal, mert nem megfelelő hálózati vagy rendszergazdai konfigurációval rendelkeznek.

## <a name="restrictions"></a>Korlátozások

A futtatási parancs használatakor a következő korlátozások érvényesek:

* A kimenet az utolsó 4 096 bájtra korlátozódik.
* A parancsfájl futtatásának minimális ideje körülbelül 20 másodperc.
* A parancsfájlok Windows rendszeren futnak.
* Egyszerre csak egy parancsfájl futtatható.
* Az adatokat kérő parancsfájlok (interaktív mód) nem támogatottak.
* Futó parancsfájlt nem lehet megszakítani.
* A parancsfájlok futtatásának maximális ideje 90 perc. Ezt követően időtúllépés jelenik meg.
* A parancsfájl eredményeinek visszaküldéséhez a virtuális gépről kimenő kapcsolat szükséges.
* Nem ajánlott olyan parancsfájlt futtatni, amely a virtuálisgép-ügynök leállítását vagy frissítését eredményezi. Ez lehetővé teszi, hogy a bővítmény átmeneti állapotba kerüljön, ami időtúllépést okoz.

> [!NOTE]
> Ahhoz, hogy megfelelően működjön, futtassa a következő parancsot az Azure nyilvános IP-címeihez: kapcsolat (443-es port). Ha a bővítmény nem fér hozzá ezekhez a végpontokhoz, előfordulhat, hogy a parancsfájlok sikeresen futnak, de nem adják vissza az eredményeket. Ha blokkolja a forgalmat a virtuális gépen, a [címkék](../../virtual-network/network-security-groups-overview.md#service-tags) használatával engedélyezheti az Azure nyilvános IP-címekre irányuló forgalmat a `AzureCloud` címkével.

## <a name="available-commands"></a>Elérhető parancsok

Ez a táblázat a Windows rendszerű virtuális gépekhez elérhető parancsok listáját jeleníti meg. A **RunPowerShellScript** parancs használatával bármilyen egyéni parancsfájlt futtathat, amelyet szeretne. Ha az Azure CLI vagy a PowerShell használatával futtat egy parancsot, a vagy paraméterhez megadott értéknek az `--command-id` `-CommandId` alábbi felsorolt értékek egyikének kell lennie. Ha olyan értéket ad meg, amely nem elérhető parancs, a következő hibaüzenet jelenik meg:

```error
The entity was not found in this Azure location
```

|**Név**|**Leírás**|
|---|---|
|**RunPowerShellScript**|PowerShell-parancsfájlt futtat.|
|**EnableRemotePS**|A távoli PowerShell engedélyezését konfigurálja a gépen.|
|**EnableAdminAccount**|Ellenőrzi, hogy a helyi rendszergazdai fiók le van-e tiltva, és ha igen, akkor.|
|**IPConfig**| Az IP-cím, az alhálózati maszk és az alapértelmezett átjáró részletes információit jeleníti meg a TCP/IP-hez kötött egyes adapterek esetében.|
|**RDPSettings**|Ellenőrzi a beállításjegyzék beállításait és a tartományi házirend beállításait. Házirend-műveleteket javasol, ha a számítógép egy tartomány része, vagy módosítja a beállításokat az alapértelmezett értékekre.|
|**ResetRDPCert**|Eltávolítja az RDP-figyelőhöz kötött TLS/SSL-tanúsítványt, és visszaállítja az RDP-figyelő biztonsági beállítását az alapértelmezett értékre. Akkor használja ezt a parancsfájlt, ha a tanúsítvánnyal kapcsolatos problémákat tapasztal.|
|**SetRDPPort**|Beállítja Távoli asztal kapcsolatok alapértelmezett vagy felhasználó által megadott portszámát. Engedélyezi a tűzfalszabályok bejövő hozzáférését a porthoz.|

## <a name="azure-cli"></a>Azure CLI

Az alábbi példa az az [VM Run-Command](/cli/azure/vm/run-command#az-vm-run-command-invoke) parancs használatával futtat egy rendszerhéj-parancsfájlt egy Azure Windows rendszerű virtuális gépen.

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

Lépjen a [Azure Portal](https://portal.azure.com) egy virtuális gépre, és válassza a **Futtatás parancsot** a **műveletek** területen. Ekkor megjelenik a virtuális gépen futtatandó elérhető parancsok listája.

![Parancsok listája](./media/run-command/run-command-list.png)

Válassza ki a futtatandó parancsot. Egyes parancsok opcionális vagy kötelező bemeneti paramétereket tartalmazhatnak. Ezen parancsok esetében a paraméterek szövegmezőként jelennek meg a bemeneti értékek megadásához. Minden parancsnál megtekintheti a Futtatás alatt álló parancsfájlt a **megtekintési parancsfájl** kibontásával. A **RunPowerShellScript** eltér a többi parancstól, mert lehetővé teszi a saját egyéni parancsfájl megadását.

> [!NOTE]
> A beépített parancsok nem szerkeszthetők.

A parancs kiválasztása után válassza a **Futtatás** parancsot a szkript futtatásához. A szkript befejeződése után visszaadja a kimenetet és az esetleges hibákat a kimeneti ablakban. Az alábbi képernyőképen egy példa látható a **RDPSettings** parancs futtatásának eredményéről.

![Parancsfájl kimenetének futtatása](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

Az alábbi példa a [meghívó-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) parancsmag használatával futtat PowerShell-parancsfájlt egy Azure-beli virtuális gépen. A parancsmag arra vár, `-ScriptPath` hogy a paraméterben hivatkozott parancsfájl helyi legyen, ahol a parancsmag fut.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>A Parancs futtatása funkcióhoz való hozzáférés korlátozása

A futtatási parancsok listázásához vagy egy parancs részleteinek megjelenítéséhez az `Microsoft.Compute/locations/runCommands/read` előfizetési szinten engedélyre van szükség. Ez az engedély a beépített [olvasói](../../role-based-access-control/built-in-roles.md#reader) szerepkörhöz és a magasabb szintekhez tartozik.

A parancs futtatásához `Microsoft.Compute/virtualMachines/runCommand/action` engedély szükséges. Ez az engedély a [virtuális gép közreműködői](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szerepköre és a magasabb szinten van.

Használhatja a [beépített szerepkörök](../../role-based-access-control/built-in-roles.md) egyikét, vagy létrehozhat egy [Egyéni szerepkört](../../role-based-access-control/custom-roles.md) a futtatási parancs használatához.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a parancsfájlok és parancsok távoli virtuális gépen való futtatásának egyéb módjairól, olvassa el a [parancsfájlok futtatása a Windows rendszerű virtuális gépen](run-scripts-in-vm.md)című témakört.
