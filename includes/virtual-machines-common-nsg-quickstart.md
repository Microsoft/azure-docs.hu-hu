---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 09/12/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ec6cbcbc93fe87634c87caeb0041b75ec916a22f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "67179089"
---
Az Azure-ban megnyithat egy portot, vagy létrehozhat egy végpontot egy virtuális gépre (VM), ha hálózati szűrőt hoz létre egy alhálózaton vagy egy VM-hálózati adapteren. Ezeket a szűrőket, amelyek a bejövő és a kimenő forgalmat is szabályozzák, a forgalmat fogadó erőforráshoz csatolt hálózati biztonsági csoporton.

A cikkben szereplő példa azt mutatja be, hogyan hozható létre olyan hálózati szűrő, amely a 80-es szabványos TCP-portot használja (feltételezi, hogy már elindította a megfelelő szolgáltatásokat, és megnyitotta a virtuális gépen az operációs rendszerhez tartozó tűzfalszabályok egyik szabályát).

Miután létrehozott egy virtuális gépet, amely a 80-es szabványos TCP-porton a webes kérelmek kiszolgálására van konfigurálva, a következőket teheti:

1. Hozzon létre egy hálózati biztonsági csoportot.

2. Hozzon létre egy bejövő biztonsági szabályt, amely engedélyezi a forgalmat, és értékeket rendel hozzá a következő beállításokhoz:

   - **Célport tartományai**: 80

   - **Forrásport-tartományok**: * (engedélyezi bármely forrásport)

   - **Prioritás értéke**: olyan értéket adjon meg, amely kisebb, mint 65 500, és az alapértelmezett "Catch" megtagadási szabálynál magasabb prioritású.

3. Társítsa a hálózati biztonsági csoportot a virtuális gép hálózati adapteréhez vagy az alhálózathoz.

Bár ez a példa egy egyszerű szabályt használ a HTTP-forgalom engedélyezéséhez, a hálózati biztonsági csoportok és szabályok segítségével összetettebb hálózati konfigurációkat is létrehozhat. 




