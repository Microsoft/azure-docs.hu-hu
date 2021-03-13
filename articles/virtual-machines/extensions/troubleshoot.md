---
title: A Windowsos virtuális gépek bővítményével kapcsolatos hibák elhárítása
description: Tudnivalók az Azure-beli Windows virtuálisgép-bővítményekkel kapcsolatos hibák elhárításáról
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/29/2016
ms.openlocfilehash: 8cc8a0b5ae0a83a152168b14a2c5577f8f7b48ab
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564798"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Azure-beli Windows VM-bővítményekkel kapcsolatos hibák elhárítása
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>A bővítmény állapotának megtekintése
Azure Resource Manager-sablonok Azure PowerShellból is végrehajthatók. A sablon végrehajtása után a bővítmény állapota Azure Erőforrás-kezelőból vagy a parancssori eszközökből is megtekinthető.

Alább bemutatunk egy példát:

Azure PowerShell:

```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

Itt látható a minta kimenete:

```output
Extensions:  {
  "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
  "Name": "myCustomScriptExtension",
  "SubStatuses": [
    {
      "Code": "ComponentStatus/StdOut/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
          \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
          test.txt                          \\n\\n",
                  "Time": null
      },
    {
      "Code": "ComponentStatus/StdErr/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "",
      "Time": null
    }
  ]
}
```

## <a name="troubleshooting-extension-failures"></a>Bővítményekkel kapcsolatos hibák elhárítása

### <a name="rerun-the-extension-on-the-vm"></a>A bővítmény újrafuttatása a virtuális gépen
Ha a virtuális gépen parancsfájlokat futtat egyéni szkriptek használatával, időnként előfordulhat, hogy a virtuális gép sikeres létrehozásakor hiba történt, de a parancsfájl végrehajtása sikertelen volt. Ilyen körülmények között a hiba helyreállításának ajánlott módja a bővítmény eltávolítása és újbóli újrafuttatása.
Megjegyzés: a későbbiekben ez a funkció továbbra is kibővült, hogy eltávolítsa a bővítmény eltávolításának szükségességét.

#### <a name="remove-the-extension-from-azure-powershell"></a>Távolítsa el a bővítményt Azure PowerShell
```azurepowershell
Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"
```

A bővítmény eltávolítását követően a sablon újra végrehajtható a parancsfájlok futtatásához a virtuális gépen.

### <a name="trigger-a-new-goalstate-to-the-vm"></a>Új GoalState elindítása a virtuális gépen
Észreveheti, hogy egy bővítmény nem lett végrehajtva, vagy egy hiányzó "Windows Azure CRP-tanúsítvány-generátor miatt nem hajtható végre" (ez a tanúsítvány a bővítmény védett beállításainak átvitelét biztosítja).
Ezt a tanúsítványt a rendszer automatikusan újra létrehozza a Windows vendég ügynöknek a virtuális gépen való újraindításával:
- A Feladatkezelő megnyitása
- Ugrás a Részletek lapra
- A WindowsAzureGuestAgent.exe folyamat megkeresése
- Kattintson a jobb gombbal, és válassza a "feladat befejezése" lehetőséget. A rendszer automatikusan újraindítja a folyamatot


Új GoalState is aktiválhat a virtuális gépen a "virtuális gép újratelepítése" végrehajtásával. A virtuális gép [újratelepítése](/rest/api/compute/virtualmachines/reapply) a 2020-es verzióban bevezetett API a virtuális gép állapotának újraalkalmazásához. Azt javasoljuk, hogy ezt akkor hajtsa végre, amikor egy rövid virtuális gép leállását szeretné elviselni. Habár az újbóli alkalmazás nem okozza a virtuális gépek újraindítását, és az újraalkalmazás időpontjának túlnyomó többsége nem indítja újra a virtuális gépet, nagyon kicsi a kockázata, hogy a virtuálisgép-modellre való újraindításkor a rendszer végrehajtja a virtuális gép modelljének más függőben lévő frissítését, és a többi módosítás újraindítást igényelhet. 

Azure Portal:

A portálon válassza ki a virtuális gépet, és a bal oldali ablaktáblán, a **támogatás + hibaelhárítás** területen válassza az **újratelepítés + újraalkalmazás** lehetőséget, majd az **újbóli alkalmazás** lehetőséget.


Azure PowerShell *(a RG nevét és a virtuális gép nevét cserélje le az értékekre)*:

```azurepowershell
Set-AzVM -ResourceGroupName <RG Name> -Name <VM Name> -Reapply
```

Azure CLI *(a RG nevét és a virtuális gép nevét cserélje le az értékekre)*:

```azurecli
az vm reapply -g <RG Name> -n <VM Name>
```

Ha a "virtuális gép újratelepítése" nem működött, hozzáadhat egy új üres adatlemezt a virtuális géphez az Azure felügyeleti portálból, majd később eltávolíthatja azt a tanúsítvány újbóli hozzáadása után.