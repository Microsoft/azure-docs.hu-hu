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
ms.openlocfilehash: 95a7ae951edab8e75894b0e9aa85029a54a50acb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91664919"
---
# <a name="sms-client-library-overview"></a>Az SMS-ügyfélkódtár áttekintése

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

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
| Telefonszám      | Díjmentes számok                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| Szabályozási        | Letiltások kezelésére                                                                      | ✔️   | ✔️    | ✔️    | ✔️      |
| Nyomon követés        | A küldött és fogadott üzenetek használatának figyelése                                          | ✔️   | ✔️    | ✔️    | ✔️      |
| PSTN-hívás      | PSTN-hívási képességek hozzáadása SMS-kompatibilis díjmentes számhoz (privát előzetes verzió)                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedés az SMS küldésével](../../quickstarts/telephony-sms/send.md)

A következő dokumentumok érdekesek lehetnek:

- Ismerkedjen meg az általános [SMS-fogalmakkal](../telephony-sms/concepts.md)
- SMS-kompatibilis [telefonszám](../../quickstarts/telephony-sms/get-phone-number.md) beszerzése
- [Az SMS-megoldás megtervezése](../telephony-sms/plan-solution.md)
