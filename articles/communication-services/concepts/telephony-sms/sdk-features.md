---
title: SMS ügyféloldali kódtár – áttekintés az Azure kommunikációs szolgáltatásokhoz
titleSuffix: An Azure Communication Services concept document
description: Áttekintést nyújt az SMS-ügyfél könyvtáráról és ajánlatáról.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 2448840fcbbea686972f8f948d428bda7b3c2bf2
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391745"
---
# <a name="sms-client-library-overview"></a>Az SMS-ügyfélkódtár áttekintése

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]
[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Az Azure kommunikációs szolgáltatások SMS-ügyfél-kódtárak használatával SMS-üzenetküldést adhat az alkalmazásaihoz.

## <a name="sms-client-library-capabilities"></a>Az SMS ügyféloldali kódtár képességei

Az alábbi lista az ügyféloldali kódtárakban jelenleg elérhető funkciók készletét mutatja be.

| Szolgáltatások csoportja | Képesség                                                                            | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------- | --- | ---- | ---- | ------ |
| Alapvető képességek | SMS-üzenetek küldése és fogadása </br> *Támogatott Unicode-hangulatjelek*                        | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Kézbesítési jelentések fogadása a küldött üzenetekhez                                            | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Minden karakterkészlet (nyelv/Unicode támogatás)                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Hosszú üzenetek támogatása (legfeljebb 2048 karakter)                                           | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Hosszú üzenetek automatikus összefűzése                                                   | ✔️   | ✔️    | ✔️    | ✔️      |
| Események            | A Event Grid használatával konfigurálhatja a webhookokat a bejövő üzenetek és a kézbesítési jelentések fogadására | ✔️   | ✔️    | ✔️    | ✔️      |
| Telefonszám      | Toll-Free számok                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| Szabályozási        | Opt-Out-kezelő                                                                      | ✔️   | ✔️    | ✔️    | ✔️      |
| Figyelés        | A küldött és fogadott üzenetek használatának figyelése                                          | ✔️   | ✔️    | ✔️    | ✔️      |
| PSTN-hívás      | PSTN-hívási képességek hozzáadása az SMS-kompatibilis díjmentes számhoz                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerkedés az SMS küldésével](../../quickstarts/telephony-sms/send.md)

A következő dokumentumok érdekesek lehetnek:

- Ismerkedjen meg az általános [SMS-fogalmakkal](../telephony-sms/concepts.md)
- SMS-kompatibilis [telefonszám](../../quickstarts/telephony-sms/get-phone-number.md) beszerzése
- [Telefonszám-típusok az Azure kommunikációs szolgáltatásokban](../telephony-sms/plan-solution.md)
