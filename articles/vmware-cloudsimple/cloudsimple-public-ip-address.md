---
title: Azure VMware-megoldás CloudSimple szerint – nyilvános IP-cím
description: Ismerje meg a nyilvános IP-címeket és azok előnyeit az Azure VMware-megoldás CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 147ab6b5ace4493e1a0b303c320cb2e81d829b29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "77024976"
---
# <a name="cloudsimple-public-ip-address-overview"></a>CloudSimple nyilvános IP-cím áttekintése

A nyilvános IP-cím lehetővé teszi az internetes erőforrások számára, hogy privát IP-címen keresztül kommunikáljanak a privát Felhőbeli erőforrásokkal. A magánhálózati IP-cím egy virtuális gép vagy egy szoftveres terheléselosztó a saját Felhőbeli vCenter. A nyilvános IP-cím lehetővé teszi, hogy elérhetővé tegye a saját felhőben futó szolgáltatásokat az interneten.

A nyilvános IP-cím a magánhálózati IP-címhez van hozzárendelve, amíg meg nem rendeli a hozzárendelést. Egy nyilvános IP-cím csak egy magánhálózati IP-címhez rendelhető hozzá.

Egy nyilvános IP-címhez társított erőforrás mindig a nyilvános IP-címet használja az internet-hozzáféréshez. Alapértelmezés szerint csak a kimenő internet-hozzáférés engedélyezett a nyilvános IP-címen.  A nyilvános IP-cím bejövő forgalma megtagadva.  A bejövő forgalom engedélyezéséhez hozzon létre egy tűzfalszabályot a nyilvános IP-címhez az adott porton.

## <a name="benefits"></a>Előnyök

Nyilvános IP-cím használata a bejövő kommunikációhoz:

* Elosztott szolgáltatásmegtagadási (DDoS) támadások megelőzése. Ez a védelem automatikusan engedélyezve van a nyilvános IP-címhez.
* A forgalom figyelése és a gyakori hálózati szintű támadások valós idejű enyhítése. Ezek a védelem a Microsoft online szolgáltatások által használt védelem.
* Az Azure globális hálózatának teljes skálája. A hálózat használható a különböző régiók közötti támadási forgalom elosztására és enyhítésére.  

## <a name="next-steps"></a>Következő lépések

* Útmutató [nyilvános IP-cím lefoglalásához](public-ips.md)