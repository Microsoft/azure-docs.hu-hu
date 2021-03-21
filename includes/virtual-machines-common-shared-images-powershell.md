---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 807cbd283cf7971bf4256451028ffa16a0911266
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96026640"
---
## <a name="create-an-image-gallery"></a>Rendszerkép-gyűjtemény létrehozása 

A képgyűjtemény a képmegosztás engedélyezéséhez használt elsődleges erőforrás. A katalógus nevének megengedett karaktere nagybetűs vagy kisbetűk, számjegyek, pontok és időszakok. A gyűjtemény neve nem tartalmazhat kötőjeleket. A katalógus nevének egyedinek kell lennie az előfizetésen belül. 

Hozzon létre egy képtárat a [New-AzGallery](/powershell/module/az.compute/new-azgallery)használatával. A következő példában létrehozunk egy *MyGallery* nevű katalógust a *myGalleryRG* -erőforráscsoporthoz.

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```
   

## <a name="share-the-gallery"></a>A katalógus megosztása

Javasoljuk, hogy a Képtár szintjén ossza meg a hozzáférést. Használjon e-mail-címet és a [Get-AzADUser](/powershell/module/az.resources/get-azaduser) parancsmagot a felhasználó objektumazonosító beszerzéséhez, majd a [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) használatával adja meg nekik a katalógushoz való hozzáférést. Cserélje le a példában szereplő e-mailt a alinne_montes@contoso.com saját adataira.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName

```