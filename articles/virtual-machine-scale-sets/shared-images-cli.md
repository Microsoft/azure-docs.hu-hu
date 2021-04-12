---
title: Megosztott virtuálisgép-rendszerképek használata méretezési csoport létrehozásához az Azure CLI-ben
description: Ebből a témakörből megtudhatja, hogyan hozhat létre virtuálisgép-méretezési csoportokat az Azure-ban az Azure CLI használatával.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: b5e9d5995e8173950db483c5c26a11830a62862e
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444003"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Megosztott rendszerképek létrehozása és használata a virtuálisgép-méretezési csoportokhoz az Azure CLI 2,0-vel

Méretezési csoport létrehozása során meg kell adnia egy rendszerképet a virtuálisgép-példányok üzembe helyezésekor. A [megosztott képgyűjtemény](../virtual-machines/shared-image-galleries.md) egyszerűbbé teszi a szervezeten belüli Egyéni rendszerképek megosztását. Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Az egyéni rendszerképek segítségével indíthatók olyan konfigurálások, mint az alkalmazások betöltése, alkalmazások konfigurálása és más operációsrendszer-konfigurálások. 

A megosztott rendszerkép-katalógus segítségével másokkal is megoszthatja a lemezképeket. Válassza ki a megosztani kívánt képeket, mely régiókat szeretné elérhetővé tenni a alkalmazásban, és hogy kivel szeretné megosztani azokat. 


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>Következő lépések

Hozzon létre egy rendszerkép-verziót egy [virtuális](../virtual-machines/image-version-vm-cli.md)gépről vagy egy [felügyelt rendszerképből](../virtual-machines/image-version-managed-image-cli.md).

A megosztott képtárakkal kapcsolatos további információkért tekintse meg az [áttekintést](../virtual-machines/shared-image-galleries.md). Ha problémákba ütközik, tekintse meg a [megosztott képtárak hibaelhárítása](../virtual-machines/troubleshooting-shared-images.md)című témakört.
