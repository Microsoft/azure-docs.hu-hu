---
title: Gyakori hibák elhárítása Azure Load Balancer
description: Ismerje meg, hogy miként lehet elhárítani a Azure Load Balancerával kapcsolatos gyakori problémákat.
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: cddaf1bde84d7e60eb59bd4c58c65fa889e06ae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028811"
---
# <a name="troubleshoot-azure-load-balancer"></a>Az Azure Load Balancer hibaelhárítása

Ez az oldal az alapszintű és standard Azure Load Balancer kérdésekre vonatkozó hibaelhárítási információkat tartalmaz. További információ a standard Load Balancerről: [standard Load Balancer áttekintése](load-balancer-standard-diagnostics.md).

Ha a Load Balancer kapcsolat nem érhető el, a leggyakoribb tünetek a következők:

- Az Load Balancer mögötti virtuális gépek nem válaszolnak az állapot-mintavételre 
- A Load Balancer mögötti virtuális gépek nem válaszolnak a konfigurált porton lévő forgalomra

Amikor a külső ügyfelek a háttérbeli virtuális gépeken haladnak végig a terheléselosztóon, a rendszer az ügyfelek IP-címét fogja használni a kommunikációhoz. Győződjön meg arról, hogy az ügyfelek IP-címe a NSG engedélyezési listába kerül.

## <a name="no-outbound-connectivity-from-standard-internal-load-balancers-ilb"></a>Nincs kimenő kapcsolat a standard belső terheléselosztó (ILB)

**Ellenőrzés és megoldás**

A standard szintű ILB **alapértelmezés szerint biztonságosak**. Az alapszintű ILB a *rejtett* nyilvános IP-címen keresztül csatlakozhatnak az internethez. Ez nem ajánlott éles számítási feladatokhoz, mivel az IP-cím nem statikus és nem zárolható a saját NSG keresztül. Ha a közelmúltban áthelyezte az alapszintű ILB egy standard ILB, explicit módon hozzon létre egy nyilvános IP-címet, amely a NSG- [n keresztül](egress-only.md) lezárja az IP-címet. Az alhálózaton [NAT-átjárót](../virtual-network/nat-overview.md) is használhat.

## <a name="cant-change-backend-port-for-existing-lb-rule-of-a-load-balancer-that-has-virtual-machine-scale-set-deployed-in-the-backend-pool"></a>Nem lehet módosítani a háttér-portot egy olyan terheléselosztó meglévő LB-szabályához, amely a háttér-készletben üzembe helyezett virtuálisgép-méretezési csoporttal rendelkezik.

### <a name="cause-the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-virtual-machine-scale-set"></a>Ok: a háttér-port nem módosítható olyan terheléselosztási szabály esetében, amelyet a virtuálisgép-méretezési csoport által hivatkozott Load Balancer állapotának vizsgálata használ.

**Megoldás** A port módosításához távolítsa el az állapot-mintavételt a virtuálisgép-méretezési csoport frissítésével, frissítse a portot, majd konfigurálja újra az állapot-mintavételt.

## <a name="small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>A Load balancert a virtuális gépek a terheléselosztó háttér-készletéből való eltávolítása után továbbra is a kis forgalom hajtja végre.

### <a name="cause-vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>Ok: a háttér-készletből eltávolított virtuális gépek nem kapják meg a forgalmat. Az Azure-ban a hálózati forgalom kis hányada lehet a tárolással, a DNS-sel és az egyéb funkciókkal.

Az ellenőrzéshez hálózati nyomkövetést végezhet. A blob Storage-fiókokhoz használt teljes tartománynév az egyes Storage-fiókok tulajdonságai között szerepel.  Az Azure-előfizetésben található virtuális gépekről az nslookup segítségével meghatározhatja az adott Storage-fiókhoz rendelt Azure IP-címet.

## <a name="additional-network-captures"></a>További hálózati rögzítések

Ha úgy dönt, hogy megnyit egy támogatási esetet, a következő információkat kell összegyűjtenie a gyorsabb megoldás érdekében. A következő tesztek végrehajtásához válasszon egyetlen háttérbeli virtuális gépet:

- A PS ping használatával a VNet belül található egyik háttérbeli virtuális gép közül lehet tesztelni a mintavételi portra adott választ (például: PS ping 10.0.0.4:3389) és eredmények rögzítése. 
- Ha nem érkezik válasz ezen pingelési tesztekben, futtasson egyidejű netsh-nyomkövetést a háttérbeli virtuális gépen és a VNet teszt virtuális gépen a PsPing futtatásakor, majd állítsa le a netsh nyomkövetést.

## <a name="load-balancer-in-failed-state"></a>Sikertelen állapotú Load Balancer

**Resolution** (Osztás)

- Miután azonosította a hibás állapotú erőforrást, lépjen a [Azure erőforrás-kezelőra](https://resources.azure.com/) , és azonosítsa az erőforrást ebben az állapotban.
- Frissítse a jobb felső sarokban található váltógomb írás/írás értékre.
- Kattintson a Szerkesztés elemre az erőforrás sikertelen állapotában.
- Kattintson a PUT elemre, és győződjön meg arról, hogy a kiépítési állapot sikeresen frissítve lett.
- Ezután folytathatja a többi műveletet, mivel az erőforrás nem meghibásodott állapotban van.

## <a name="next-steps"></a>Következő lépések

Ha a fenti lépések nem oldják meg a problémát, nyisson meg egy [támogatási jegyet](https://azure.microsoft.com/support/options/).
