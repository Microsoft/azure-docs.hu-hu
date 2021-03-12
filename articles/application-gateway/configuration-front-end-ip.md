---
title: Azure Application Gateway előtér-IP-cím konfigurálása
description: Ez a cikk az Azure Application Gateway előtér-IP-címének konfigurálását ismerteti.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: eff63510f70dd7b4cdd522cc5a2a68096cda7166
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102548716"
---
# <a name="application-gateway-front-end-ip-address-configuration"></a>Application Gateway előtér-IP-cím konfigurálása

Beállíthatja, hogy az Application Gateway nyilvános IP-címmel, magánhálózati IP-címmel vagy mindkettővel rendelkezzen. Egy nyilvános IP-címet kell megadni, ha a háttérrendszer futtatásakor az ügyfeleknek internetre irányuló virtuális IP-címmel (VIP) keresztül kell elérniük az internetet.

## <a name="public-and-private-ip-address-support"></a>Nyilvános és magánhálózati IP-cím támogatása

A Application Gateway v2 jelenleg nem támogatja csak a magánhálózati IP-módot. A következő kombinációkat támogatja:

* Magánhálózati IP-cím és nyilvános IP-cím
* Csak nyilvános IP-cím

További információ: [Application Gatewayokkal kapcsolatos gyakori kérdések](application-gateway-faq.yml#how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address).


Nem szükséges egy nyilvános IP-cím egy olyan belső végponthoz, amely nem érhető el az interneten. Ezt nevezzük *belső terheléselosztó-* (ILB-) végpontnak vagy privát előtér-IP-nek. Az Application Gateway ILB olyan belső üzletági alkalmazások esetében hasznos, amelyek nem érhetők el az interneten. A többrétegű alkalmazások szolgáltatásai és szintjei is hasznosak egy olyan biztonsági határon belül, amely nem érhető el az interneten, azonban a ciklikus multiplexelés, a munkamenet-stickiion vagy a TLS-lezárást igényli.

Csak egy nyilvános IP-cím és egy magánhálózati IP-cím támogatott. Az előtérbeli IP-címet az Application Gateway létrehozásakor választhatja ki.

- Nyilvános IP-cím esetén létrehozhat egy új nyilvános IP-címet, vagy használhat egy meglévő nyilvános IP-címet az Application Gateway szolgáltatással megegyező helyen. További információ: statikus vagy [dinamikus nyilvános IP-cím](./application-gateway-components.md#static-versus-dynamic-public-ip-address).

- Privát IP-cím esetén megadhat egy magánhálózati IP-címet abban az alhálózatban, amelyben az Application Gateway létrejött. Ha nem ad meg ilyet, a rendszer automatikusan kijelöl egy tetszőleges IP-címet az alhálózatból. A kiválasztott IP-cím típusa (statikus vagy dinamikus) később nem módosítható. További információ: [Application Gateway létrehozása belső terheléselosztó használatával](./application-gateway-ilb-arm.md).

Az előtér-IP-cím egy *figyelőhöz* van társítva, amely az ELŐTÉR-IP-címen érkező kérelmeket ellenőrzi.

## <a name="next-steps"></a>Következő lépések

- [A figyelő konfigurációjának megismerése](configuration-listeners.md)
