---
title: fájl belefoglalása
description: fájl belefoglalása
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 04/07/2021
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: c4c36c0e099ed7474a5d27f6edcbd4b3ac435f4f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030822"
---
| Erőforrás | Korlát |
| --- | --- |
| Adatátviteli sebesség |30 GB/s<sup>1</sup> |
|Szabályok korlátai|10 000 egyedi forrás/cél a hálózati szabályokban|
|Maximális DNAT-szabályok|298 egyetlen nyilvános IP-címhez.<br>Minden további nyilvános IP-cím a rendelkezésre álló SNAT-portokhoz járul hozzá, de csökkentheti az elérhető DNAT-szabályok számát. A két nyilvános IP-cím például 297 DNAT-szabályt tesz lehetővé. Ha a szabály protokollja a TCP és az UDP használatára van konfigurálva, két szabálynak számít.|
|Minimális AzureFirewallSubnet-méret |/26|
|Porttartomány a hálózat és az alkalmazás szabályaiban|1 - 65535|
|Nyilvános IP-címek|250 maximális érték. Az összes nyilvános IP-cím használható DNAT-szabályokban, és mind hozzájárulnak az elérhető SNAT-portokhoz.|
|IP-címek az IP-csoportokban|Tűzfalon legfeljebb 100 IP-csoport adható meg.<br>Az egyes IP-csoportokban legfeljebb 5000 egyedi IP-cím vagy IP-előtag adható meg.
|Útválasztási táblázat|Alapértelmezés szerint a AzureFirewallSubnet 0.0.0.0/0 útvonalon van, és az NextHopType érték van beállítva az **internethez**.<br><br>Az Azure Firewallnak közvetlen internetkapcsolatra van szüksége. Ha a AzureFirewallSubnet a BGP-n keresztül tanulja meg a helyszíni hálózat alapértelmezett útvonalát, akkor a közvetlen internetkapcsolat **fenntartása érdekében egy** 0.0.0.0/0 UDR kell megadnia a **NextHopType** értékkel. Alapértelmezés szerint a Azure Firewall nem támogatja a kényszerített bújtatást egy helyszíni hálózatra.<br><br>Ha azonban a konfiguráció kényszerített bújtatást igényel egy helyszíni hálózathoz, a Microsoft eseti alapon fogja támogatni azt. Forduljon az ügyfélszolgálathoz, és tekintse át az esetet. Ha elfogadják, engedélyezzük az előfizetését, és gondoskodni kell a tűzfal internetkapcsolatának fenntartásáról.|
|A hálózati szabályok teljes tartománynevei|A megfelelő teljesítmény érdekében a tűzfalon belül minden hálózati szabály esetében ne haladja meg a 1000 teljes tartománynevet.|

<sup>1</sup> Ha meg kell emelnie ezeket a korlátokat, forduljon az Azure ügyfélszolgálatához.
