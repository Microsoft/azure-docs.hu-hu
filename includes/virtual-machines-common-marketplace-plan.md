---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 8e0e549f88caf4a541642bab77faf54b5e536b29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "71174958"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Rendszerkép üzembe helyezése Piactéri feltételekkel

Az Azure Marketplace-en néhány virtuálisgép-rendszerkép további licenccel és vásárlási feltételekkel rendelkezik, amelyeket el kell fogadnia, mielőtt programozott módon telepítené őket.  

Egy virtuális gép lemezképből való üzembe helyezéséhez mindkét esetben el kell fogadnia a lemezkép feltételeit, és engedélyeznie kell a programozott telepítést. Ezt csak egyszer kell megtennie egy előfizetésnél. Ezt követően minden alkalommal, amikor programozottan telepít egy virtuális gépet a lemezképből, meg kell adnia a *vásárlási terv* paramétereit is.

Az alábbi fejezetek a következőket szemléltetik:

* Annak megállapítása, hogy a Piactéri rendszerképnek van-e további licencfeltételek 
* A használati feltételek elfogadása programozott módon
* Beszerzési terv paramétereinek megadása a virtuális gépek programozott üzembe helyezése esetén

