---
title: Az Azure Firewall szabályfeldolgozási logikája
description: Tudnivalók a Azure Firewall szabályok feldolgozásának logikáról
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 70026173d1cb932d30a59ea2b876ef22217a81bc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563680"
---
# <a name="azure-firewall-rule-processing-logic"></a>Az Azure Firewall szabályfeldolgozási logikája

Azure Firewall rendelkezik NAT-szabályokkal, hálózati szabályokkal és alkalmazási szabályokkal. A szabályok feldolgozása a szabály típusa szerint történik.

## <a name="network-rules-and-applications-rules"></a>Hálózati szabályok és alkalmazások szabályai

Először alkalmazza a hálózati szabályokat, majd az alkalmazás szabályait. A szabályok leállnak. Tehát ha a hálózati szabályok egyezést találnak, akkor a rendszer nem dolgozza fel az alkalmazás szabályait.  Ha egyetlen hálózati szabály sem egyezik, és ha a Packet protokoll HTTP/HTTPS, akkor a rendszer az alkalmazási szabályok alapján értékeli ki a csomagot. Ha még mindig nem található egyezés, akkor a rendszer kiértékeli a csomagot az infrastruktúra-szabálygyűjtemény alapján. Ha még mindig nincs egyezés, akkor a csomag alapértelmezés szerint meg van tagadva.

## <a name="nat-rules"></a>NAT-szabályok

A bejövő kapcsolat engedélyezhető a célként megadott hálózati címfordítás (DNAT) konfigurálásával az [oktatóanyag: a bejövő forgalom szűrése Azure Firewall DNAT a Azure Portal használatával](../firewall/tutorial-firewall-dnat.md). A rendszer először alkalmazza a DNAT-szabályokat. Ha talál egyezést, egy implicit megfelelő hálózati szabályt ad hozzá a lefordított forgalom engedélyezéséhez. Ezt a viselkedést felülírhatja, ha explicit módon hozzáad egy hálózatiszabály-készletet, amely megtagadja azokat a szabályokat, amelyek a lefordított adatforgalomhoz tartoznak. Ezekhez a kapcsolatokhoz nem alkalmazhatók alkalmazási szabályok.

## <a name="inherited-rules"></a>Örökölt szabályok

A szülő házirendből örökölt hálózati szabályok gyűjteményeit mindig rangsorolja a rendszer az új szabályzat részeként definiált hálózati szabályok gyűjteményei felett. Ugyanez a logika vonatkozik az alkalmazási szabályok gyűjteményére is. A hálózati szabályok gyűjteményeit azonban a rendszer mindig feldolgozza az alkalmazási szabályok gyűjtése előtt, az örökléstől függetlenül.

Alapértelmezés szerint a házirend örökli a szülő házirend fenyegetésének intelligencia üzemmódját. Ezt felülbírálhatja úgy, hogy a fenyegetési intelligencia módot egy másik értékre állítja be a házirend-beállítások lapon. Csak szigorúbb értékkel lehet felülbírálni. Ha például a szülői házirend *csak riasztásra*van beállítva, a helyi házirendet beállíthatja *riasztásra és megtagadásre*, de nem kapcsolhatja ki.

## <a name="next-steps"></a>További lépések

- [További információ a Azure Firewall Managerről](overview.md)