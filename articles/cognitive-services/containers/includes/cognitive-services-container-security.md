---
title: Tárolóbiztonság
titleSuffix: Azure Cognitive Services
description: Útmutató a tároló biztonságossá tételéhez
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 12/17/2020
ms.author: aahi
ms.openlocfilehash: 4b106ebc5606c4e5a290e12728d4e2011e80f6a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97861840"
---
## <a name="azure-cognitive-services-container-security"></a>Azure Cognitive Services tároló biztonsága

Az alkalmazások fejlesztésekor a biztonságnak elsődleges fókusznak kell lennie. A biztonság fontossága a siker mérőszáma. Cognitive Services tárolókat tartalmazó szoftver-megoldás tervezésekor elengedhetetlen az Ön számára elérhető korlátozások és képességek megismerése. A hálózati biztonsággal kapcsolatos további információkért lásd: az [Azure Cognitive Services Virtual Networks konfigurálása][az-security].

> [!IMPORTANT]
> Alapértelmezés szerint nincs *Biztonság* a Cognitive Services Container API-ban. Ennek az az oka, hogy a tároló a legtöbb esetben egy olyan Pod részeként fut, amely egy hálózati híddal kívülről van védve. Azonban engedélyezhető a hitelesítés, amely azonos módon működik a [felhőalapú Cognitive Serviceshoz][request-authentication]való hozzáféréskor használt hitelesítéssel.

Az alábbi ábra az alapértelmezett és **nem biztonságos** megközelítést mutatja be:

![Tárolóbiztonság](../media/container-security.svg)

Alternatív és *biztonságos* megközelítésként a Cognitive Services tárolók felhasználói a tárolót egy előtérben lévő összetevővel bővíthetik, így a tároló végpontja magánjellegű marad. Vegyünk egy olyan forgatókönyvet, amelyben a [Istio][istio] -t bejövő átjáróként használjuk. A Istio támogatja a HTTPS/TLS-t és az ügyféltanúsítvány-alapú hitelesítést. Ebben a forgatókönyvben a Istio előtér teszi elérhetővé a tárolók hozzáférését, és bemutatja az Istio-mel előzetesen jóváhagyott ügyféltanúsítványt.

[Nginx][nginx] egy másik népszerű választás ugyanabban a kategóriában. Mind a Istio, mind az Nginx Service meshként működik, és további funkciókat kínál, például a terheléselosztást, az útválasztást és a díjszabást.

### <a name="container-networking"></a>Tárolóalapú hálózatkezelés

A Cognitive Services tárolók számlázási célú mérési adatok küldéséhez szükségesek. Nem sikerült engedélyezni a különböző hálózati csatornák listáját, amelyeken a Cognitive Services tárolók támaszkodnak, így nem fog működni a tároló.

#### <a name="allow-list-cognitive-services-domains-and-ports"></a>Engedélyezési lista Cognitive Services tartományok és portok

A gazdagépnek engedélyezni kell a 443-es **port** és a következő tartományok listáját:

* `*.cognitive.microsoft.com`
* `*.cognitiveservices.azure.com`

#### <a name="disable-deep-packet-inspection"></a>Mélyreható csomagok ellenőrzésének letiltása

A [Deep Packet-ellenőrzés](https://en.wikipedia.org/wiki/Deep_packet_inspection) (dpi) olyan adatfeldolgozási típus, amely részletesen megvizsgálja a számítógépes hálózaton keresztül küldött adatokat, és általában blokkolja, átirányítja vagy naplózza a műveletet.

Tiltsa le a DPI-t azon biztonságos csatornákon, amelyeket a Cognitive Services tárolók a Microsoft-kiszolgálókhoz hoznak létre. Ennek elmulasztása megakadályozza, hogy a tároló megfelelően működjön.

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../cognitive-services-virtual-networks.md
