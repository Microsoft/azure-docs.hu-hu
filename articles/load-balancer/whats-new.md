---
title: A Azure Load Balancer újdonságai
description: Ismerje meg a Azure Load Balancer újdonságait, például a legújabb kibocsátási megjegyzéseket, ismert problémákat, hibajavításokat, elavult funkciókat és a közelgő változásokat.
services: load-balancer
author: anavinahar
ms.service: load-balancer
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: anavin
ms.openlocfilehash: a30a42e8a8c4049b53274da512089dd29965e775
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96573153"
---
# <a name="whats-new-in-azure-load-balancer"></a>A Azure Load Balancer újdonságai

A Azure Load Balancer rendszeresen frissül. Maradjon naprakész a legújabb bejelentésekkel. Ez a cikk a következő információkat tartalmazza:

- A legújabb kiadások
- Ismert problémák
- Hibajavítások
- Elavult funkció (ha van ilyen)

Itt megtalálhatja a legújabb Azure Load Balancer frissítéseket, és feliratkozhat [ide](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer)az RSS-hírcsatornára.

## <a name="recent-releases"></a>Legutóbbi kiadások

| Típus |Név |Leírás  |Hozzáadás dátuma  |
| ------ |---------|---------|---------|
| Szolgáltatás | Az erőforrás-csoportok közötti áthelyezés támogatása | A standard Load Balancer és a standard szintű nyilvános IP-cím támogatása az [erőforráscsoport áthelyezéséhez](https://azure.microsoft.com/updates/standard-resource-group-move/). | 2020. október |
| Szolgáltatás | Az IP-alapú háttér-készlet felügyeletének támogatása (előzetes verzió) | Azure Load Balancer támogatja az erőforrások hozzáadását és eltávolítását egy háttérbeli készletből IPv4-vagy IPv6-címek használatával. Ez lehetővé teszi a tárolók, a virtuális gépek és a Load Balancerhoz társított virtuálisgép-méretezési csoportok egyszerű kezelését. Azt is lehetővé teszi, hogy az IP-címek a háttérbeli készlet részeként legyenek fenntartva a társított erőforrások létrehozása előtt. [További információ](backend-pool-management.md)|2020. július |
| Szolgáltatás| A Azure Monitor használatával Azure Load Balancer a bepillantást | A hálózatok Azure Monitorának részeként az ügyfelek mostantól rendelkeznek topológiai térképekkel az összes Load Balancer-konfigurációhoz és az állapot irányítópulthoz az Azure Portal metrikákkal előre konfigurált standard Load Balancerekhez. [Első lépések és további információ](https://azure.microsoft.com/blog/introducing-azure-load-balancer-insights-using-azure-monitor-for-networks/) | 2020. június |
| Érvényesítés | A HA-portok érvényesítésének hozzáadása | Az érvényesítés hozzá lett adva annak biztosításához, hogy ha a lebegőpontos IP-cím engedélyezve van, akkor a HA portra vonatkozó szabályok és a nem szükséges portszabályok nem konfigurálhatók. Korábban ez a konfiguráció áthalad, de nem a kívánt módon működik. Nem történt változás a funkcionalitásban. További információt [itt](load-balancer-ha-ports-overview.md#limitations) talál| 2020. június |
| Szolgáltatás| IPv6-támogatás a Azure Load Balancerhoz (általánosan elérhető) | Az Azure Load Balancer előtérben az IPv6-címek is megadhatók. Itt megtudhatja, hogyan [hozhat létre Dual stack-alkalmazást](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) |2020. április|
| Szolgáltatás| A TCP alaphelyzetbe állítása üresjárati időkorláton (általánosan elérhető)| A TCP-visszaállítók használatával kiszámítható alkalmazások viselkedése hozható létre. [További információ](load-balancer-tcp-reset.md)| 2020. február |

## <a name="known-issues"></a>Ismert problémák

A termékcsoport aktívan dolgozik a következő ismert problémák megoldásán:

|Probléma |Description  |Kockázatcsökkentés  |
| ---------- |---------|---------|
| Load Balancer a riasztási esemény és az állapot-mintavételi állapot naplófájljai | A naplózás nem működik Load Balancer riasztási eseményekre az alapszintű és a standard Load Balancer, illetve az alapszintű Load Balancer állapotának eseménynaplói esetében.  | [Használja Azure monitor a többdimenziós metrikák számára a standard Load Balancer](load-balancer-standard-diagnostics.md). Azure Monitor a többdimenziós metrikák olyan gazdag készletét biztosítja, amely naplóként is exportálható. Az előre konfigurált mérőszámok irányítópultot a Load Balancer elemzések alpaneljén keresztül is kihasználhatja. Ha az alapszintű Load Balancer az üzemi szintű mérőszámok figyelése standard szintre való [frissítését](upgrade-basic-standard.md) használja.

  

## <a name="next-steps"></a>Következő lépések

További információ a Azure Load Balancerről: [Mi az Azure Load Balancer?](load-balancer-overview.md) és [Gyakori kérdések](load-balancer-faqs.md).
