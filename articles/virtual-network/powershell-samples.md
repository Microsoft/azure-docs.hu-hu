---
title: Azure PowerShell-minták virtuális hálózathoz
description: Azure PowerShell-minták virtuális hálózathoz.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: de752cdacf17193d5be95b2b9f887938ace2d50f
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091871"
---
# <a name="azure-powershell-samples-for-virtual-network"></a>Azure PowerShell-minták virtuális hálózathoz

A következő táblázat Azure PowerShell-szkriptekre mutató hivatkozásokat tartalmaz:

| | |
|----|----|
| [Virtuális hálózat létrehozása többrétegű alkalmazásokhoz](./scripts/virtual-network-powershell-sample-multi-tier-application.md) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal. Az előtérbeli alhálózat felé irányuló forgalom HTTP-re, a háttérbeli alhálózat felé irányuló forgalom pedig az SQL-re, az 1433-as portra korlátozódik. |
| [Társviszony létesítése két virtuális hálózat között](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md) | Két virtuális hálózatot hoz létre és csatlakoztat ugyanabban a régióban. |
| [Forgalom irányítása hálózati virtuális készüléken keresztül](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal, valamint egy virtuális gépet, amely képes a két alhálózat közötti forgalom irányítására. |
| [Virtuális gép kimenő és bejövő hálózati forgalmának szűrése](./scripts/virtual-network-powershell-sample-filter-network-traffic.md) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal. Az előtérbeli alhálózat bejövő hálózati forgalma HTTP-re és HTTPS-re korlátozódik. A háttérbeli alhálózat felől az internetre irányuló kimenő forgalom nem engedélyezett. |
|[Az IPv4 + IPv6 kettős verem virtuális hálózatának beállítása alapszintű Load Balancer](./scripts/virtual-network-powershell-sample-ipv6-dual-stack.md)|Két virtuális géppel, valamint egy IPv4-és IPv6-alapú nyilvános IP-címmel rendelkező Azure alapszintű Load Balancer üzembe helyezi a kettős verem (IPv4 + IPv6) virtuális hálózatot. |
|[IPv4 + IPv6 kettős verem virtuális hálózatának konfigurálása standard Load Balancer](./scripts/virtual-network-powershell-sample-ipv6-dual-stack-standard-load-balancer.md)|Két virtuális gépre, valamint egy IPv4-és IPv6-alapú nyilvános IP-címmel rendelkező Azure-standard Load Balancer üzembe helyezi a kettős verem (IPv4 + IPv6) virtuális hálózatot. |
