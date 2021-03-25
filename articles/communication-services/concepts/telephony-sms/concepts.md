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
ms.openlocfilehash: 0ddc9bfeb0df32614d835e0eaef9da52e917ee91
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108434"
---
# <a name="sms-concepts"></a>SMS-sel kapcsolatos alapfogalmak

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Az Azure kommunikációs szolgáltatások lehetővé teszik SMS szöveges üzenetek küldését és fogadását a kommunikációs szolgáltatások SMS SDK-k használatával. Ezek az SDK-k az ügyfélszolgálati forgatókönyvek, a kinevezési emlékeztetők, a kétfaktoros hitelesítés és az egyéb valós idejű kommunikációs igények támogatásához használhatók. A kommunikációs szolgáltatások SMS-je lehetővé teszi az üzenetek megbízható küldését, miközben kiteszi a kampányok által megfigyelt kézbesítési és reagálási arányt.

Az Azure kommunikációs szolgáltatások SMS SDK-k főbb funkciói a következők:

-  **Egyszerű** telepítési élmény SMS-képesség alkalmazásokhoz való hozzáadásához.
- **Nagy sebességű** üzenetek támogatása a A2P (alkalmazás – személy) a Egyesült Államokban használt díjmentes számokkal.
- **Kétirányú** beszélgetések olyan forgatókönyvek támogatásához, mint például az ügyfélszolgálat, a riasztások és a találkozók emlékeztetői.
- **Megbízható kézbesítés** valós idejű kézbesítési jelentésekkel az alkalmazásból küldött üzenetekhez.
- **Elemzés** a használati minták és az ügyfelek részvételének nyomon követéséhez.
- A **letiltási** kezelési támogatással automatikusan észlelhetők és betartható a díjmentes számok. Az USA-beli díjmentes számokat az USA-beli szolgáltatók felhatalmazzák és érvényesítik.
  - Leállítás – ha egy szöveges üzenet címzettje szeretné letiltani a letiltást, a "Leállítás" üzenetet a díjmentes számra küldheti. A szolgáltató a következő alapértelmezett választ küldi le: *"hálózati msg: a" Leállítás "szót választotta, amely letiltja az ebből a számból küldött összes szöveget. A "Leállítás" szöveg visszakapcsolja az üzeneteket. "*
  - INDÍTÁS/leállítás – ha a címzett egy díjmentes számú szöveges üzenetre szeretne újrafizetni, akkor a "START" vagy a "Leállítás" lehetőséget a díjmentes számra küldheti. A szolgáltató a következő alapértelmezett választ küldi el a START/unstop számára: *"hálózati msg: megválaszolta a" leállítást ", és megkezdi az üzenetek fogadását erről a számról."*
  - Az Azure kommunikációs szolgáltatás a leállítási üzenetet fogja felderíteni, és letiltja az összes további üzenetet a címzettnek. A kézbesítési jelentés azt jelzi, hogy sikertelen kézbesítés történt az állapotjelző üzenettel, mert a feladó letiltotta a megadott címzettet.
  - A Leállítás, a leállítás és az indítási üzenetek vissza lesznek továbbítva Önnek. Az Azure kommunikációs szolgáltatásokkal megfigyelheti és implementálhatja ezeket a letiltásokat, így biztosíthatja, hogy a rendszer ne küldjön olyan üzenetet a címzetteknek, akik kiválasztották a kommunikációt.


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerkedés az SMS küldésével](../../quickstarts/telephony-sms/send.md)

A következő dokumentumok érdekesek lehetnek:

- Ismerkedjen meg az [SMS SDK](../telephony-sms/sdk-features.md) -val
- SMS-kompatibilis [telefonszám](../../quickstarts/telephony-sms/get-phone-number.md) beszerzése
- [Telefonszám-típusok az Azure kommunikációs szolgáltatásokban](../telephony-sms/plan-solution.md)
