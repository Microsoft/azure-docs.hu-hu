---
title: RDP-Felhasználónév és-jelszó frissítése a PowerShellben
description: Azure PowerShell-példaszkript – Az RPD-felhasználónév és -jelszó frissítése az egy konkrét csomóponttípusba tartozó összes Service Fabric-fürtcsomópont esetében.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 03/19/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: bcf619e2251f5c1b641190549da45f721835ce0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87076148"
---
# <a name="update-the-admin-username-and-password-of-the-vms-in-a-cluster"></a>Az egy fürtbe tartozó virtuális gépek rendszergazdai felhasználónevének és jelszavának frissítése

A Service Fabric-fürtök mindegyik [csomóponttípusa](../service-fabric-cluster-nodetypes.md) egy virtuálisgép-méretezési csoportot jelent. Ez a példaszkript frissíti az egy konkrét csomóponttípusba tartozó fürtözött virtuális gépek rendszergazdai felhasználónevét és jelszavát.  Adja hozzá a VMAccessAgent bővítményt a méretezési csoporthoz, mert a rendszergazdai jelszó nem módosítható méretezésicsoport-tulajdonság.  A felhasználónév és jelszó módosításai a méretezési csoport összes csomópontjára érvényesek. Szabja testre a paramétereket szükség szerint.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/) utasításait követve. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-user-and-pw/change-rdp-user-and-pw.ps1 "Updates a RDP username and password for cluster nodes")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja: A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Lekéri egy fürtcsomóponttípus (egy virtuálisgép-méretezési csoport) tulajdonságait.   |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension)| Hozzáad egy bővítményt a virtuálisgép-méretezési csoporthoz.|
| [Frissítés – AzVmss](/powershell/module/az.compute/update-azvmss)|Frissíti a virtuálisgép-méretezési csoport állapotát a helyi VMSS-objektum állapotával megegyezőre.|

## <a name="duration"></a>Időtartam

A Felhasználónév vagy a jelszó megváltoztatásához egyetlen, öt csomóponttal rendelkező csomópont-típusnak kell lennie, például 45 és 60 perc közötti időtartammal. 

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).

További Azure Powershell-példákat az Azure Service Fabrichez az [Azure PowerShell-példák](../service-fabric-powershell-samples.md) között találhat.
