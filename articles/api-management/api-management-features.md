---
title: Az Azure API Management szintjeinek szolgáltatáson alapuló összehasonlítása | Microsoft Docs
description: Az általuk kínált funkciók alapján hasonlítsa össze az API Managementi szinteket. Tekintse meg az egyes díjszabási szinten elérhető főbb funkciókat összefoglaló táblázatot.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/15/2020
ms.author: apimpm
ms.openlocfilehash: eec913237ba978e52bb64fbd4c1f043a9214ffc8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92077847"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Az Azure API Management szintjeinek szolgáltatáson alapuló összehasonlítása

Az egyes API Management [díjszabási szintjei](https://aka.ms/apimpricing) különböző szolgáltatásokat és egységnyi [kapacitást](api-management-capacity.md)kínálnak. A következő táblázat összefoglalja az egyes szintjein elérhető főbb funkciókat. Egyes funkciók eltérően működhetnek, vagy a szinttől függően különböző képességekkel rendelkezhetnek. Ilyen esetekben a különbségeket az ezeket az egyéni funkciókat ismertető dokumentációs cikkekben nevezzük.

> [!IMPORTANT]
> Vegye figyelembe, hogy a fejlesztői réteg nem üzemi célú használati esetekre és értékelésekre szolgál. Nem biztosít SLA-t.

| Szolgáltatás                                                                                      | Használat | Fejlesztő | Alapszintű | Standard | Prémium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Azure AD-integráció<sup>1</sup>                                                             | Nem          | Igen       | Nem    | Igen      | Yes     |
| Virtual Network (VNet) támogatása                                                               | Nem          | Igen       | Nem    | Nem       | Igen     |
| Többrégiós üzembe helyezés                                                                      | Nem          | Nem        | Nem    | Nem       | Igen     |
| Több egyéni tartománynév                                                                 | Nem          | Igen        | Nem    | Nem       | Igen     |
| Fejlesztői portál<sup>2</sup>                                                                 | Nem          | Igen       | Igen   | Igen      | Yes     |
| Beépített gyorsítótár                                                                               | Nem          | Igen       | Igen   | Igen      | Yes     |
| Beépített Analitika                                                                           | Nem          | Igen       | Igen   | Igen      | Yes     |
| [Saját üzemeltetésű átjáró](self-hosted-gateway-overview.md)<sup>3</sup>                           | Nem          | Igen       | Nem    | Nem       | Igen     |
| [TLS-beállítások](api-management-howto-manage-protocols-ciphers.md)                             | Igen         | Igen       | Igen   | Igen      | Yes     |
| [Külső gyorsítótár](./api-management-howto-cache-external.md)                                                    | Igen         | Igen       | Igen   | Igen      | Yes     |
| [Ügyféltanúsítvány-alapú hitelesítés](api-management-howto-mutual-certificates-for-clients.md) | Igen         | Igen       | Igen   | Igen      | Yes     |
| [Biztonsági mentés és visszaállítás](api-management-howto-disaster-recovery-backup-restore.md)               | Nem          | Igen       | Igen   | Igen      | Yes     |
| [Felügyelet a git felett](api-management-configuration-repository-git.md)                        | Nem          | Igen       | Igen   | Igen      | Yes     |
| Közvetlen felügyeleti API                                                                        | Nem          | Igen       | Igen   | Igen      | Yes     |
| Naplók és mérőszámok Azure Monitor                                                               | Igen         | Igen       | Igen   | Igen      | Yes     |
| Statikus IP-cím                                                                                    | Nem          | Igen       | Igen   | Igen      | Yes     |

<sup>1</sup> lehetővé teszi az Azure ad (és Azure ad B2C) identitás-szolgáltatóként való használatát a fejlesztői portálon való felhasználói bejelentkezéshez.<br/>
<sup>2</sup> beleértve a kapcsolódó funkciókat, például a felhasználókat, a csoportokat, a problémákat, az alkalmazásokat és az e-mail sablonokat és az értesítéseket.<br/>
<sup>3</sup> a fejlesztői rétegben a saját üzemeltetésű átjárók egyetlen átjáró csomópontra korlátozódnak.<br/>