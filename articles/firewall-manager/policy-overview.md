---
title: Azure Firewall Manager-házirend áttekintése
description: Tudnivalók a Azure Firewall Manager-házirendekről
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 88b7dc60b865325ef7dcd9e79015fdee10b4f9a2
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/30/2020
ms.locfileid: "85563709"
---
# <a name="azure-firewall-manager-policy-overview"></a>Azure Firewall Manager-házirend áttekintése

A tűzfalszabályok olyan Azure-erőforrások, amelyek NAT-, hálózat-és alkalmazás-szabály-gyűjteményeket, valamint a fenyegetési intelligencia beállításait tartalmazzák. Ez egy globális erőforrás, amely a biztonságos virtuális hubok és a hub virtuális hálózatok több Azure Firewall példányára is használható. A szabályzatok a régiók és az előfizetések között működnek.

![Azure Firewall Manager-házirend](media/policy-overview/policy-overview.png)

## <a name="policy-creation-and-association"></a>Házirend létrehozása és társítása

Egy házirend több módon is létrehozható és kezelhető, többek között a Azure Portal, a REST API, a sablonok, a Azure PowerShell és a parancssori felület.

A meglévő szabályokat Azure Firewall is áttelepítheti a portálról vagy Azure PowerShell a házirendek létrehozásához. További információ: [Azure Firewall konfigurációk migrálása Azure Firewall házirendre](migrate-to-policy.md). 

A szabályzatok egy vagy több virtuális hubhoz vagy virtuális hálózatok is társíthatók. A tűzfal a fiókjához és bármely régióhoz tartozó előfizetésben is lehet.

## <a name="hierarchical-policies"></a>Hierarchikus házirendek

Új szabályzatok hozhatók létre a semmiből, vagy a meglévő szabályzatokból örökölhető. Az öröklés lehetővé teszi, hogy a DevOps helyi tűzfal-házirendeket hozzon létre a szervezethez kötött alapházirend alapján.

A nem üres szülő házirendekkel létrehozott házirendek öröklik az összes szabályt a szülő házirendből. A szülő házirendből örökölt hálózati szabályok gyűjteményei mindig elsőbbséget élveznek az új szabályzat részeként definiált hálózati szabályok gyűjteményei felett. Ugyanez a logika vonatkozik az alkalmazási szabályok gyűjteményére is. A hálózati szabályok gyűjteményeit azonban a rendszer mindig feldolgozza az alkalmazási szabályok gyűjtése előtt, az örökléstől függetlenül.

A fenyegetési intelligencia mód a szülő házirendből is örökölt. A fenyegetési intelligencia mód beállítható más értékre, hogy felülbírálja ezt a viselkedést, de nem kapcsolhatja ki. Csak szigorúbb értékkel lehet felülbírálni. Ha például a szülő házirend **csak riasztásra**van beállítva, a helyi házirendet beállíthatja **riasztásra és megtagadásre**.

A veszélyforrások felderítési módjához hasonlóan a fenyegetések felderítésének engedélyezési listája örökli a szülő-házirendet. A gyermek házirend további IP-címeket is hozzáadhat az engedélyezési listához.

A NAT-szabályok gyűjteményei nem öröklődnek, mert egy adott tűzfalra vonatkoznak.

Öröklés esetén a rendszer automatikusan alkalmazza a szülő házirend módosításait a társított tűzfal-alárendelt házirendekbe.

## <a name="traditional-rules-and-policies"></a>Hagyományos szabályok és szabályzatok

A Azure Firewall a hagyományos szabályokat és szabályzatokat is támogatja. A következő táblázat összehasonlítja a szabályzatokat és a szabályokat:


|         |Szabályzat  |Szabályok  |
|---------|---------|---------|
|Contains     |NAT, hálózat, alkalmazás-szabályok, egyéni DNS-és DNS-proxybeállítások, IP-csoportok és fenyegetések intelligencia-beállításai (beleértve az engedélyezési listát)|NAT-, hálózati és alkalmazás-szabályok, egyéni DNS-és DNS-proxybeállítások, IP-csoportok és fenyegetések intelligencia-beállításai (beleértve az engedélyezési listát)|
|Védi     |Virtuális hubok és virtuális hálózatok|Csak virtuális hálózatok|
|Portal-felület     |Központi felügyelet a Firewall Manager használatával|Önálló tűzfal-élmény|
|Több tűzfal támogatása     |A tűzfal-házirend egy különálló erőforrás, amely tűzfalakon keresztül is felhasználható|Szabályok manuális exportálása és importálása vagy harmadik féltől származó felügyeleti megoldások használata |
|Díjszabás     |A számlázás a tűzfal társítása alapján történik. Tekintse meg a [díjszabást](#pricing).|Ingyenes|
|Támogatott üzembe helyezési mechanizmusok     |Portál, REST API, sablonok, Azure PowerShell és CLI|Portál, REST API, sablonok, PowerShell és parancssori felület. |

## <a name="pricing"></a>Díjszabás

A szabályzatok a tűzfal-társítások alapján lesznek kiszámlázva. Egy nulla vagy egy tűzfal társítással rendelkező szabályzat díjmentes. A több tűzfallal foglalkozó szabályzatot rögzített díjszabás szerint számítjuk fel. További információ: [Azure Firewall Manager díjszabása](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="next-steps"></a>További lépések

A Azure Firewall telepítésének megismeréséhez tekintse meg [az oktatóanyag: a felhőalapú hálózat védelme a Azure Firewall Managerrel a Azure Portal használatával](secure-cloud-network.md)című témakört.
