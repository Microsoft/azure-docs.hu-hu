---
title: fájl belefoglalása
description: fájl belefoglalása
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 11/06/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: d479c3087b971aa17cf145e0111890da07381eab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94386521"
---
| Erőforrás | Korlát |
| --- | --- |
| Adatátviteli sebesség |30 GB/s<sup>1</sup> |
|Szabályok|10 000. Minden szabálytípus kombinálva.|
|Maximális DNAT-szabályok|298 egyetlen nyilvános IP-címhez.<br>Minden további nyilvános IP-cím a rendelkezésre álló SNAT-portokhoz járul hozzá, de csökkentheti az elérhető DNAT-szabályok számát. A két nyilvános IP-cím például 297 DNAT-szabályt tesz lehetővé. Ha a szabály protokollja a TCP és az UDP használatára van konfigurálva, két szabálynak számít.|
|Minimális AzureFirewallSubnet-méret |/26|
|Porttartomány a hálózat és az alkalmazás szabályaiban|1 - 65535|
|Nyilvános IP-címek|250 maximális érték. Az összes nyilvános IP-cím használható DNAT-szabályokban, és mind hozzájárulnak az elérhető SNAT-portokhoz.|
|IP-címek az IP-csoportokban|Tűzfalon legfeljebb 100 IP-csoport adható meg.<br>Az egyes IP-csoportokban legfeljebb 5000 egyedi IP-cím vagy IP-előtag adható meg.
|Útválasztási táblázat|Alapértelmezés szerint a AzureFirewallSubnet 0.0.0.0/0 útvonalon van, és az NextHopType érték van beállítva az **internethez**.<br><br>Az Azure Firewallnak közvetlen internetkapcsolatra van szüksége. Ha a AzureFirewallSubnet a BGP-n keresztül tanulja meg a helyszíni hálózat alapértelmezett útvonalát, akkor a közvetlen internetkapcsolat **fenntartása érdekében egy** 0.0.0.0/0 UDR kell megadnia a **NextHopType** értékkel. Alapértelmezés szerint a Azure Firewall nem támogatja a kényszerített bújtatást egy helyszíni hálózatra.<br><br>Ha azonban a konfiguráció kényszerített bújtatást igényel egy helyszíni hálózathoz, a Microsoft eseti alapon fogja támogatni azt. Forduljon az ügyfélszolgálathoz, és tekintse át az esetet. Ha elfogadják, engedélyezzük az előfizetését, és gondoskodni kell a tűzfal internetkapcsolatának fenntartásáról.|
|A hálózati szabályok teljes tartománynevei|A megfelelő teljesítmény érdekében a tűzfalon belül minden hálózati szabály esetében ne haladja meg a 1000 teljes tartománynevet.|

<sup>1</sup> Ha meg kell emelnie ezeket a korlátokat, forduljon az Azure ügyfélszolgálatához.
