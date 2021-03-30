---
title: 'PowerShell-parancsfájl: a fogyasztónak küldött Azure-beli adatmegosztási meghívások listázása'
description: Megtudhatja, hogyan kapja meg a PowerShell-parancsfájl a fogyasztóknak küldött meghívókat, és megtekintheti a használható szkriptet.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.openlocfilehash: 5f75894094fa2a15dbc9e2809ed6c3631df96c3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221213"
---
# <a name="use-powershell-to-get-a-data-share-invitation"></a>Adatmegosztási meghívás beszerzése a PowerShell használatával

Ez a PowerShell-parancsfájl lekéri a fogyasztóknak küldött meghívókat.

## <a name="sample-script"></a>Példaszkript
```powershell
# Set variables with your own values
$invitationId = "<invitationId>"
$location = "<location>"

#List invitations sent to a consumer
Get-AzDataShareInvitation

#Get a specific invitation sent to a consumer
Get-AzDataShareInvitation -location -invitationId 

```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Jegyzetek |
|---|---|
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation) | Az elküldött adatmegosztási meghívások beolvasása és listázása |
|||

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/).

További Azure-adatmegosztási PowerShell-szkriptek az Azure-beli [adatmegosztás PowerShell-mintáinak](../../samples-powershell.md)használatával találhatók.
