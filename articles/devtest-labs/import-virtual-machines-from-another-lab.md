---
title: Virtuális gépek importálása egy másik laborból Azure DevTest Labs
description: Ez a cikk azt ismerteti, hogyan importálhat virtuális gépeket egy másik laborból a Azure DevTest Labs aktuális laborba.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0f664a0ae399575ee936565adaf7364fd1c5ce5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85475934"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Virtuális gépek importálása egy másik laborból Azure DevTest Labs
Ez a cikk azt ismerteti, hogyan lehet virtuális gépeket importálni egy másik laborból a laborba.

## <a name="scenarios"></a>Forgatókönyvek
Íme néhány forgatókönyv, amelyben virtuális gépeket kell importálnia egy másik laborba:

- A csapaton belüli személy egy másik csoportba kerül át a vállalaton belül, és az új csapat DevTest Labs-be kívánja tenni a fejlesztői asztalt.
- A csoport egy [előfizetési szintű kvótával](../azure-resource-manager/management/azure-subscription-service-limits.md) rendelkezik, és néhány előfizetésre szeretné osztani a csapatokat
- A vállalat az expressz útvonalra (vagy más új hálózati topológiára) vált, és a csapat szeretné áthelyezni a Virtual Machines az új infrastruktúra használatára.

## <a name="solution-and-constraints"></a>Megoldás és megkötések
Ez a funkció lehetővé teszi, hogy a virtuális gépeket egy tesztkörnyezetben (forrás) egy másik laborba (cél) importálja. Megadhat egy új nevet a cél virtuális géphez a folyamatban. Az importálási folyamat magában foglalja az összes függőséget, például a lemezeket, az ütemterveket, a hálózati beállításokat és így tovább.

A folyamat eltarthat egy ideig, és az alábbi tényezők befolyásolják:

- A forrásoldali géphez csatolt lemezek száma/mérete (mivel ez egy másolási művelet, nem pedig áthelyezési művelet)
- Távolság a célhelytől (például az USA keleti régiója Délkelet-Ázsiában).

A folyamat befejezése után a forrásként szolgáló virtuális gép leáll, és az újat a cél laborban futtatja.

Két fontos korlátozást kell figyelembe vennie, amikor a virtuális gépeket egy laborból egy másik laborba importálja:

- A virtuális gépek a különböző előfizetésekben és régiókban való importálása támogatott, de az előfizetéseket ugyanahhoz a Azure Active Directory bérlőhöz kell társítani.
- A Virtual Machines nem lehet a forrás laborban található, igényelhető állapotban.
- A virtuális gép tulajdonosa a tesztkörnyezetben, a laborban pedig a tesztkörnyezet tulajdonosa.
- Jelenleg ez a funkció csak a PowerShell és a REST API használatával támogatott.

## <a name="use-powershell"></a>A PowerShell használata
Töltse le ImportVirtualMachines.ps1 fájlt a [githubról](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines). A parancsfájl használatával egyetlen virtuális gépet vagy a forrás laborban lévő összes virtuális gépet importálhatja a cél laborba.

### <a name="use-powershell-to-import-a-single-vm"></a>Egyetlen virtuális gép importálása a PowerShell használatával
Ennek a PowerShell-parancsfájlnak a végrehajtásához meg kell adnia a forrás virtuális gépet és a cél labort, és opcionálisan meg kell adni egy új nevet a célszámítógépen:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>A PowerShell használata az összes virtuális gép importálásához a forrás laborban
Ha nincs megadva a forrásként szolgáló virtuális gép, a parancsfájl automatikusan importálja az összes virtuális gépet a DevTest Labs-ben.  Például:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>Virtuális gép importálása HTTP REST használatával
A REST-hívás egyszerű. Adjon meg elegendő információt a forrás-és a cél erőforrásainak azonosításához. Ne feledje, hogy a művelet a cél labor erőforráson történik.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [Tesztkörnyezet szabályzatának beállítása](devtest-lab-set-lab-policy.md)
- [Gyakori kérdések](devtest-lab-faq.md)
