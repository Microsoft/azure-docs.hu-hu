---
title: Az Azure-beli API Management szolgáltatásiszintek | Microsoft Docs
description: Hasonlítsa API Management az egyes szinteket az ő szolgáltatásaik alapján. Tekintsen meg egy táblázatot, amely összefoglalja az egyes tarifacsomagok legfontosabb funkcióit.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 04/13/2021
ms.author: apimpm
ms.openlocfilehash: f111729d7d7707ed4f40ce8f89ce76975fb47400
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536445"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Az Azure-beli API Management szolgáltatásokon alapuló összehasonlítása

Minden API Management [tarifacsomag](https://aka.ms/apimpricing) különböző funkciókat és egységkapacitást [kínál.](api-management-capacity.md) Az alábbi táblázat összefoglalja az egyes szinteken elérhető legfontosabb szolgáltatásokat. Egyes funkciók eltérő módon működnek, vagy a szinttől függően eltérő képességekkel rendelkeznek. Ilyen esetekben az egyes funkciókat ismertető dokumentációs cikkek ismertetik a különbségeket.

> [!IMPORTANT]
> Vegye figyelembe, hogy a Fejlesztői szint nem éles használatú esetekhez és értékelésekhez használható. Nem nyújt SLA-t.

| Szolgáltatás                                                                                      | Használat | Fejlesztő | Alapszintű | Standard | Prémium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Azure AD-integráció<sup>1</sup>                                                             | Nem          | Igen       | Nem    | Igen      | Igen     |
| Virtual Network (VNet) támogatása                                                               | Nem          | Igen       | Nem    | Nem       | Igen     |
| Többrégiós üzembe helyezés                                                                      | Nem          | Nem        | Nem    | Nem       | Igen     |
| Rendelkezésreállási zónák                                                                           | Nem          | Nem        | Nem    | Nem       | Igen     |
| Több egyéni tartománynév                                                                 | Nem          | Igen        | Nem    | Nem       | Igen     |
| Fejlesztői portál<sup>2</sup>                                                                 | Nem          | Igen       | Igen   | Igen      | Igen     |
| Beépített gyorsítótár                                                                               | Nem          | Igen       | Igen   | Igen      | Igen     |
| Beépített elemzés                                                                           | Nem          | Igen       | Igen   | Igen      | Igen     |
| [Saját üzemeltetett átjáró](self-hosted-gateway-overview.md)<sup>3</sup>                           | Nem          | Igen       | Nem    | Nem       | Igen     |
| [TLS-beállítások](api-management-howto-manage-protocols-ciphers.md)                             | Igen         | Igen       | Igen   | Igen      | Igen     |
| [Külső gyorsítótár](./api-management-howto-cache-external.md)                                                    | Igen         | Igen       | Igen   | Igen      | Igen     |
| [Ügyfél-tanúsítvány hitelesítése](api-management-howto-mutual-certificates-for-clients.md) | Igen         | Igen       | Igen   | Igen      | Igen     |
| [Biztonsági mentés és visszaállítás](api-management-howto-disaster-recovery-backup-restore.md)               | Nem          | Igen       | Igen   | Igen      | Igen     |
| [Kezelés Giten keresztül](api-management-configuration-repository-git.md)                        | Nem          | Igen       | Igen   | Igen      | Igen     |
| Közvetlen felügyeleti API                                                                        | Nem          | Igen       | Igen   | Igen      | Igen     |
| Azure Monitor és metrikák                                                               | Nem          | Igen       | Igen   | Igen      | Igen     |
| Statikus IP-cím                                                                                    | Nem          | Igen       | Igen   | Igen      | Igen     |

<sup>1</sup> Engedélyezi az Azure AD (és Azure AD B2C) használatát a felhasználói bejelentkezéshez a fejlesztői portálon.<br/>
<sup>2</sup> Beleértve a kapcsolódó funkciókat, például a felhasználókat, csoportokat, problémákat, alkalmazásokat, e-mail-sablonokat és értesítéseket.<br/>
<sup>3</sup> A fejlesztői szinten a saját fejlesztésű átjárók egyetlen átjárócsomópontra vannak korlátozva.<br/>