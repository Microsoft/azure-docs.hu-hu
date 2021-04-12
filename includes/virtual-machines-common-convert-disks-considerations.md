---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 72050ff4887642ba16d52948c1c1253ca01443c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102473796"
---
* Az átalakítás újraindítja a virtuális gépet, így a virtuális gépek áttelepítését egy már létező karbantartási időszakra kell ütemeznie. 

* Az átalakítás nem vonható vissza. 

* Vegye figyelembe, hogy a virtuálisgép-közreműködő szerepkörrel rendelkező felhasználók nem változtathatják meg a [virtuális gép](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) méretét (ahogy a konverziót előkészítik). Ennek az az oka, hogy a felügyelt lemezekkel rendelkező virtuális gépeken a felhasználónak rendelkeznie kell a Microsoft. számítás/lemezek/írási engedéllyel az operációsrendszer-lemezeken.

* Mindenképpen tesztelje az átalakítást. Migráljon egy tesztcélú virtuális gépet, mielőtt végrehajtja a migrálást éles környezetben.

* Az átalakítás közben felszabadítja a virtuális gépet. A virtuális gép új IP-címet kap, amikor elindul az átalakítás után. Szükség esetén [hozzárendelhet egy statikus IP-címet](../articles/virtual-network/public-ip-addresses.md) a virtuális géphez.

* Tekintse át az Azure-beli virtuálisgép-ügynöknek az átalakítási folyamat támogatásához szükséges minimális verzióját. Az ügynök verziójának vizsgálatával és frissítésével kapcsolatos információkért lásd: virtuálisgép [-ügynökök minimális verziójának támogatása az Azure-ban](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)
