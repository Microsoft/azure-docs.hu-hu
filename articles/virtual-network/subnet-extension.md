---
title: Alhálózat-bővítmény az Azure-ban | Microsoft Docs
description: Az alhálózati bővítmény ismertetése az Azure-ban.
services: virtual-network
documentationcenter: na
author: anupam-p
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2019
ms.author: anupand
ms.openlocfilehash: 555c903dc3375080a8b0d006d2aac7216854c939
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99539298"
---
# <a name="subnet-extension"></a>Alhálózati bővítmény
A munkaterhelések áttelepítése a nyilvános felhőbe gondos tervezést és koordinációt igényel. Az egyik kulcsfontosságú szempont az IP-címek megőrzése is. Ez különösen akkor fontos, ha az alkalmazások IP-címektől függenek, vagy ha megfelelőségi követelmények vonatkoznak a megadott IP-címek használatára. Az Azure Virtual Network megoldja ezt a problémát, mivel lehetővé teszi, hogy az Ön által választott IP-címtartomány használatával VNet és alhálózatokat hozzon létre.

A Migrálás egy kis kihívást jelenthet, ha a fenti követelményt egy további, a helyszínen lévő alkalmazások fenntartására vonatkozó követelménysel párosítják. Ilyen esetben az alkalmazásokat az Azure és a helyszíni között kell megosztania, az IP-címek mindkét oldalon való újraszámozása nélkül. Emellett engedélyeznie kell, hogy az alkalmazások ugyanúgy kommunikáljanak, mintha ugyanabban a hálózaton vannak.

A fenti probléma egyik megoldása az alhálózati bővítmény. A hálózat kibővítése lehetővé teszi, hogy az alkalmazások ugyanazon szórási tartományon keresztül beszéljenek, amikor különböző fizikai helyeken találhatók, így nincs szükség a hálózati topológia újratervezésére. 

A hálózat kibővítése során általában nem jó gyakorlat, hogy az alábbi használati esetek is szükségesek.

- **Többfázisú áttelepítés**: a leggyakoribb forgatókönyv az áttelepítés fázisa. Először is be szeretne állítani néhány alkalmazást az Azure-ba, és az idő múlásával áttelepíti a többi alkalmazást.
- **Késés**: az alacsony késési követelmények miatt előfordulhat, hogy bizonyos alkalmazások a helyszínen maradnak, így biztosítva, hogy a lehető legközelebb legyenek az adatközponthoz.
- **Megfelelőség**: egy másik felhasználási eset az, hogy megfelelőségi követelményekkel rendelkezhet, hogy egyes alkalmazásai a helyszínen maradjanak.
 
> [!NOTE] 
> Ha szükséges, ne terjessze ki az alhálózatokat. Azokban az esetekben, amikor kiterjeszti az alhálózatokat, meg kell tennie egy közbenső lépést. Idővel próbálkozzon újra a helyszíni hálózatban található alkalmazások újraszámozásával és az Azure-ba való áttelepítésével.

A következő szakaszban megbeszéljük, hogyan bővíthetők az alhálózatok az Azure-ban.


## <a name="extend-your-subnet-to-azure"></a>Az alhálózat kiterjesztése az Azure-ra
 A helyszíni alhálózatokat kiterjesztheti az Azure-ra egy 3. rétegbeli hálózati megoldás használatával. A legtöbb megoldás olyan átfedési technológiát használ, mint például a VXLAN a 2. rétegbeli hálózat kibővítéséhez. Az alábbi ábrán egy általánosított megoldás látható. Ebben a megoldásban ugyanaz az alhálózat létezik mindkét oldalon, az Azure-ban és a helyszínen is. 

![Alhálózati bővítmény – példa](./media/subnet-extension/subnet-extension.png)

Az alhálózat IP-címei az Azure-beli és a helyszíni virtuális gépekhez vannak rendelve. Mind az Azure-, mind a helyszíni NVA be van helyezve a hálózatba. Ha egy Azure-beli virtuális gép megpróbál kommunikálni egy virtuális géppel a helyszíni hálózaton, az Azure-NVA rögzíti a csomagot, beágyazza azt, és a VPN/Express útvonalon továbbítja a helyszíni hálózatra. A helyszíni NVA fogadja a csomagot, decapsulates azt, és továbbítja azt a hálózatában lévő kívánt címzettnek. A visszaküldött forgalom hasonló elérési utat és logikát használ.

A fenti példában az Azure NVA és a helyszíni NVA kommunikál, és megismerheti az egymás mögötti IP-címeket. Az összetettebb hálózatok egy leképezési szolgáltatással is rendelkezhetnek, amely fenntartja a NVA és a mögöttes IP-címek közötti leképezést. Amikor egy NVA fogad egy csomagot, lekérdezi a leképezési szolgáltatást, hogy megkeresse annak a NVA a címét, amely a cél IP-címmel rendelkezik.

A következő szakaszban megtalálhatja az Azure-ban tesztelt alhálózati bővítményi megoldások részleteit.

## <a name="next-steps"></a>Következő lépések 
[Terjessze ki az alhálózatot az Azure-ba gyártói megoldások használatával.](https://github.com/microsoft/Azure-LISP)