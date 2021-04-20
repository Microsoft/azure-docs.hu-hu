---
title: Alhálózati bővítmény az Azure | Microsoft Docs
description: További tudnivalók az Azure-beli alhálózati bővítményről.
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
ms.openlocfilehash: e39859e3cc4d28ac4b1456fcdaec65ecfb9c7f31
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728211"
---
# <a name="subnet-extension"></a>Alhálózati bővítmény
A számítási feladatok nyilvános felhőbe való migrálásához gondos tervezésre és koordinációra van szükség. Az egyik legfontosabb szempont az IP-címek megőrzésének képessége. Ez különösen akkor lehet fontos, ha az alkalmazások IP-címektől függnek, vagy megfelelőségi követelmények vonatkoznak adott IP-címek használatára. Az Azure Virtual Network úgy oldja meg ezt a problémát, hogy lehetővé teszi virtuális hálózatok és alhálózatok létrehozására a választott IP-címtartomány használatával.

A migrálás egy kicsit kihívást jelenthet, ha a fenti követelményhez egy további követelmény is szükséges, amely miatt egyes alkalmazások a helyszínen tarthatóak. Ilyen esetben fel kell osztania az alkalmazásokat az Azure és a helyszíni rendszer között anélkül, hogy mindkét oldalon újraszámozhatja az IP-címeket. Emellett engedélyeznie kell, hogy az alkalmazások úgy kommunikáljanak, mintha ugyanazon a hálózaton belül lenne.

A fenti probléma egyik megoldása az alhálózati bővítmény. A hálózat kiterjesztése lehetővé teszi, hogy az alkalmazások ugyanazon szórási tartományon keresztül beszéljenek, amikor különböző fizikai helyeken léteznek, így nincs szükség a hálózati topológia újraépítésére. 

Bár a hálózat kiterjesztése általában nem jó gyakorlat, az alábbi esetekben szükség lehet rá.

- **Szakaszos migrálás:** A leggyakoribb forgatókönyv az, ha szakaszosra szeretné átemeltetni a migrálást. Először és idővel néhány alkalmazást szeretne átemelni az Azure-ba.
- **Késés:** Az alacsony késési követelmények miatt egyes alkalmazásokat a helyszínen is meg kell tartani, hogy a lehető közelebb legyenek az adatközponthoz.
- **Megfelelőség:** Egy másik alkalmazási helyzet az lehet, hogy megfelelőségi követelményekkel kell megfelelnie ahhoz, hogy egyes alkalmazásokat a helyszínen tartson.
 
> [!NOTE] 
> Csak akkor terjessze ki az alhálózatokat, ha szükséges. Abban az esetben, amikor kiterjeszti az alhálózatokat, érdemes köztes lépésként megszűrni. Idővel érdemes újraszámolnia az alkalmazásokat a helyszíni hálózatban, és áttérni az Azure-ba.

A következő szakaszban azt tárgyaljuk, hogyan bővítheti az alhálózatokat az Azure-ba.


## <a name="extend-your-subnet-to-azure"></a>Az alhálózat kiterjesztése az Azure-ba
 A helyszíni alhálózatokat kiterjesztheti az Azure-ra egy 3. rétegbeli hálózatalapú megoldással. A legtöbb megoldás egy átfedő technológiával, például a VXLAN-nal bővíti ki a 2. réteg hálózatát egy 3. rétegbefedő hálózat használatával. Az alábbi ábrán egy általánosított megoldás látható. Ebben a megoldásban ugyanaz az alhálózat létezik mindkét oldalon, azaz az Azure-ban és a helyszínen is. 

![Példa alhálózati bővítményre](./media/subnet-extension/subnet-extension.png)

Az alhálózat IP-címei azure-beli és helyszíni virtuális gépekhez vannak rendelve. Az Azure-ban és a helyszínen is van egy NVA beszúrva a hálózatukba. Amikor egy Azure-beli virtuális gép egy helyszíni hálózaton található virtuális géphez próbál beszélni, az Azure NVA rögzíti a csomagot, beágyazja azt, és VPN/Express Route-on keresztül elküldi a helyszíni hálózatra. A helyszíni NVA fogadja a csomagot, beágyazja azt, és továbbítja a hálózatában található címzettnek. A visszatérő forgalom hasonló útvonalat és logikát használ.

A fenti példában az Azure NVA és a helyszíni NVA kommunikál egymással, és megismeri a mögöttes IP-címeket. Az összetettebb hálózatok is kaphatnak leképezési szolgáltatást, amely fenntartja az NVA-k és a mögöttes IP-címek közötti leképezést. Amikor egy NVA csomagot kap, lekérdezi a leképezési szolgáltatástól annak az NVA-nak a címét, amely mögött a cél IP-címe található.

A következő szakaszban az Azure-ban tesztelt alhálózati bővítménymegoldásokkal kapcsolatos részleteket talál.

## <a name="next-steps"></a>Következő lépések 
[Kiterjesztheti a helyszíni alhálózatokat az Azure-ba az Azure Extended Network használatával.](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/azure-extended-network)