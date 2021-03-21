---
title: SMS ügyféloldali kódtár – áttekintés az Azure kommunikációs szolgáltatásokhoz
titleSuffix: An Azure Communication Services concept document
description: Áttekintést nyújt az SMS-ügyfél könyvtáráról és ajánlatáról.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 11ab634ed4b25c5fd8c0079263094c393e9dcbe6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103496592"
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
