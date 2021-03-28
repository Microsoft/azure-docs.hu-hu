---
title: SMS-fogalmak az Azure kommunikációs szolgáltatásokban
titleSuffix: An Azure Communication Services concept document
description: Ismerje meg a kommunikációs szolgáltatások SMS-fogalmakat.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: bdba05dd75b6b6f4e32bf3f536e794e568fa13d7
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105642673"
---
# <a name="sms-concepts"></a>SMS-sel kapcsolatos alapfogalmak
[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Az Azure kommunikációs szolgáltatások lehetővé teszik SMS szöveges üzenetek küldését és fogadását a kommunikációs szolgáltatások SMS SDK-k használatával. Ezek az SDK-k az ügyfélszolgálati forgatókönyvek, a kinevezési emlékeztetők, a kétfaktoros hitelesítés és az egyéb valós idejű kommunikációs igények támogatásához használhatók. A kommunikációs szolgáltatások SMS-je lehetővé teszi az üzenetek megbízható küldését a Szabadító és a válasz metrikáinak kimutatása mellett.

Az Azure kommunikációs szolgáltatások SMS SDK-k főbb funkciói a következők:

-  **Egyszerű** telepítési élmény SMS-képesség alkalmazásokhoz való hozzáadásához.
- **Nagy sebességű** üzenetek támogatása a A2P (alkalmazás – személy) a Egyesült Államokban használt díjmentes számokkal.
- A **csoportos üzenetküldés** támogatott, amely lehetővé teszi, hogy egyszerre több címzettnek küldje az üzeneteket.
- **Kétirányú** beszélgetések olyan forgatókönyvek támogatásához, mint például az ügyfélszolgálat, a riasztások és a találkozók emlékeztetői.
- **Megbízható kézbesítés** valós idejű kézbesítési jelentésekkel az alkalmazásból küldött üzenetekhez.
- **Elemzés** az SMS használati minták nyomon követéséhez.
- A **letiltási** kezelési támogatással automatikusan észlelhetők és betartható a díjmentes számok. Az USA-beli díjmentes számokat az USA-beli szolgáltatók felhatalmazzák és érvényesítik.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerkedés az SMS küldésével](../../quickstarts/telephony-sms/send.md)

A következő dokumentumok érdekesek lehetnek:

- Ismerkedjen meg az [SMS SDK](../telephony-sms/sdk-features.md) -val
- SMS-kompatibilis [telefonszám](../../quickstarts/telephony-sms/get-phone-number.md) beszerzése
- [Telefonszám-típusok az Azure kommunikációs szolgáltatásokban](../telephony-sms/plan-solution.md)
