---
title: 'PowerShell-parancsfájl: meghívás elfogadása Azure-adatmegosztásból'
description: Ez a PowerShell-parancsfájl egy meglévő adatmegosztásból fogadja a meghívókat.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.openlocfilehash: 24091bafac7ad5c558b975d52064f12715b3d622
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221400"
---
# <a name="use-powershell-to-accept-a-data-share-invitation"></a>Adatmegosztási meghívás elfogadása a PowerShell használatával

Ez a PowerShell-parancsfájl fogadja a fogyasztóknak küldött meghívókat.

## <a name="sample-script"></a>Példaszkript
```powershell
#List invitations sent to a consumer
Get-AzDataShareInvitation

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$invitationId = "<Invitation id>"

#Accept a specific invitation by creating a share subscription
New-AzDataShareSubscription -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName -InvitationId $invitationId

```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Jegyzetek |
|---|---|
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation) | Az elküldött adatmegosztási meghívások beolvasása és listázása |
| [Új – AzDataShareSubscription](/powershell/module/az.datashare/get-azdatasharesubscription) | Hozzon létre egy adatmegosztási előfizetést. |
|||

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/).

További Azure-adatmegosztási PowerShell-szkriptek az Azure-beli [adatmegosztás PowerShell-mintáinak](../../samples-powershell.md)használatával találhatók.
