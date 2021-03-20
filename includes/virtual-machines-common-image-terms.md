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
ms.openlocfilehash: 98d765e2f6909f00f8dfe76d06aef017aad67adf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "71174959"
---
## <a name="terminology"></a>Terminológia

Az Azure Piactéri rendszerképének jellemzői a következők:

* **Közzétevő**: az a szervezet, amely létrehozta a rendszerképet. Példák: Canonical, MicrosoftWindowsServer
* **Ajánlat**: a közzétevő által létrehozott kapcsolódó rendszerképek csoportjának neve. Példák: UbuntuServer, WindowsServer
* **SKU**: egy ajánlat egy példánya, például egy terjesztés fő kiadása. Példák: 18,04-LTS, 2019-Datacenter
* **Version (verzió**): egy RENDSZERKÉP-SKU verziószáma. 

Ha programozott módon telepíti a piactér lemezképét, adja meg ezeket az értékeket paraméterekként. Egyes eszközök elfogadnak egy *rendszerképet,* amely egyesíti ezeket az értékeket a kettősponttal elválasztva (:) karakter: *közzétevő*:*ajánlat*:*SKU*:*Version*. Az URN-ben a verziószámot a "legújabb" értékre cserélve kiválaszthatja a lemezkép legújabb verzióját. 

Ha a rendszerkép kiadója további licenceket és vásárlási feltételeket biztosít, akkor el kell fogadnia ezeket a feltételeket, és engedélyeznie kell a programozott telepítést. Ha programozott módon telepíti a virtuális gépet, meg kell adnia a *vásárlási terv* paramétereit is. Lásd: [lemezkép üzembe helyezése Piactéri feltételekkel](#deploy-an-image-with-marketplace-terms).
