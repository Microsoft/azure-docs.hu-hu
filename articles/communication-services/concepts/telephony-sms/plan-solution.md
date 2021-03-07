---
title: Telefonszám-típusok az Azure kommunikációs szolgáltatásokban
titleSuffix: An Azure Communication Services concept document
description: Megtudhatja, hogyan használhatja a különböző típusú telefonszámokat az SMS-ben és a telefonos szolgáltatásban.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 10/05/2020
ms.topic: overview
ms.custom: references_regions
ms.service: azure-communication-services
ms.openlocfilehash: 8a811a033a2d6e2229c83899e237f996ccddd7b3
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430411"
---
# <a name="phone-number-types-in-azure-communication-services"></a>Telefonszám-típusok az Azure kommunikációs szolgáltatásokban



[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

> [!IMPORTANT]
> A telefonszámok rendelkezésre állása jelenleg olyan fizetős Azure-előfizetésekre korlátozódik, amelyek számlázási címükkel rendelkeznek az USA-beli adatterülettel rendelkező Egyesült Államok és kommunikációs szolgáltatások erőforrásaiban. A telefonszámok nem szerezhetők be próbaverziós fiókokon vagy Azure ingyenes kreditek használatával. További információkért tekintse meg a jelen dokumentum [előfizetési jogosultságok](#azure-subscriptions-eligibility) című szakaszát.


Az Azure kommunikációs szolgáltatások lehetővé teszik a telefonszámok használatát hanghívások készítésére és SMS-üzenetek küldésére a nyilvános kapcsolású telefonos hálózattal (PSTN). Ebben a dokumentumban áttekintjük a telefonszámokat, a konfigurációs beállításokat és a régió rendelkezésre állását a telefonos és SMS-megoldás kommunikációs szolgáltatásokkal történő megtervezéséhez.

## <a name="azure-subscriptions-eligibility"></a>Azure-előfizetések jogosultsága

Telefonszám beolvasásához fizetős Azure-előfizetésre van szükség. A telefonszámok nem szerezhetők be próbaverziós fiókokon vagy Azure ingyenes kreditekkel. 

A telefonszám rendelkezésre állása jelenleg olyan Azure-előfizetésekre korlátozódik, amelyek számlázási címükkel rendelkeznek az USA-beli adatterülettel rendelkező Egyesült Államok és kommunikációs szolgáltatások erőforrásaiban.


## <a name="number-types-and-features"></a>Típusok és szolgáltatások száma
A kommunikációs szolgáltatások kétféle telefonszámot biztosítanak: **helyi** és **díjmentes**. 

### <a name="local-numbers"></a>Helyi számok
A helyi (földrajzi) számok 10 számjegyű telefonszámok, amelyek a Egyesült Államokban található helyi körzetszámokból állnak. Például `+1 (206) XXX-XXXX` egy helyi szám, amelynek körzetszáma `206` . Ez a körzetszám a Seattle városához van rendelve. Ezeket a telefonszámokat általában magánszemélyek és helyi vállalatok használják. Az Azure kommunikációs szolgáltatások helyi számokat biztosítanak a Egyesült Államokban. Ezek a számok a telefonhívások elhelyezésére, de SMS-üzenetek küldésére is használhatók. 

### <a name="toll-free-numbers"></a>Díjmentes számok
Az ingyenesen hívható telefonszámok 10 számjegyű telefonszámok, amelyek különböző körzetszámmal hívhatók meg ingyenesen. Például `+1 (800) XXX-XXXX` egy díjmentes szám a Észak-Amerika régióban. Ezeket a telefonszámokat általában az ügyfélszolgálati célokra használják. Az Azure kommunikációs szolgáltatás díjmentes számokkal rendelkezik az Egyesült Államokban. Ezek a számok a telefonhívások és SMS-üzenetek küldésére használhatók. Az ingyenesen hívható számok nem használhatók a felhasználók számára, és csak alkalmazásokhoz rendelhetők hozzá.

#### <a name="choosing-a-phone-number-type"></a>Telefonszám típusának kiválasztása

Ha a telefonszámát egy alkalmazás fogja használni (például hívások kezdeményezéséhez vagy üzenetek küldéséhez a szolgáltatás nevében), akkor kiválaszthat egy díjmentes vagy helyi (földrajzi) számot. A díjmentes számot kiválaszthatja, ha az alkalmazás SMS-üzeneteket küld és/vagy hívásokat kezdeményez.

Ha a telefonszámot egy személy (például a hívó alkalmazás felhasználója) használja, akkor a helyi (földrajzi) telefonszámot kell használni. 

Az alábbi táblázat összefoglalja a következő telefonszámokat: 

| Telefonszám típusa | Példa                              | Ország elérhetősége    | Telefonszám-képesség |Gyakori használati eset                                                                                                     |
| ----------------- | ------------------------------------ | ----------------------- | ------------------------|------------------------------------------------------------------------------------------------------------------- |
| Helyi (földrajzi)        | + 1 (helyi körzetszám) XXX XX XX  | USA                      | Hívás (kimenő) | Telefonszámok kiosztása a felhasználók számára az alkalmazásokban  |
| Toll-Free         | + 1 (díjmentes terület *kódja*) xxx XX XX | USA                      | Calling (kimenő), SMS (bejövő/kimenő)| Telefonszámok kiosztása interaktív hangválaszi (IVR) rendszerekhez/robotokhoz, SMS-alkalmazásokhoz                                        |


### <a name="phone-number-features-in-azure-communication-services"></a>Az Azure kommunikációs szolgáltatások telefonszám-szolgáltatásai 

[!INCLUDE [Emergency Calling Notice](../../includes/emergency-calling-notice-include.md)]

A legtöbb telefonszám esetében lehetővé tesszük a "a la carte" szolgáltatások beállításának konfigurálását. Ezek a funkciók kiválaszthatók, ha az Azure kommunikációs szolgáltatásokban adhatja meg a telefonszámokat.

Az Ön számára elérhető funkciók attól függnek, hogy melyik országban működik, a használati eset és a kiválasztott telefonszám típusa. Ezek a szolgáltatások országonként változnak, a szabályozási követelmények miatt. Az Azure kommunikációs szolgáltatások a következő telefonszám-funkciókat kínálja:

- **Egyirányú kimenő SMS** Ez a beállítás lehetővé teszi, hogy SMS-üzeneteket küldjön a felhasználóknak. Ez az értesítési és a kétfaktoros hitelesítési helyzetekben lehet hasznos. 
- **Kétirányú bejövő és kimenő SMS** Ez a beállítás lehetővé teszi üzenetek küldését és fogadását a felhasználóktól a telefonszámok használatával. Ez az ügyfél-szolgáltatási forgatókönyvek esetében hasznos lehet.
- **Egyirányú kimenő telefonhívás** Ezzel a beállítással hívásokat indíthat a felhasználók számára, és konfigurálhatja a hívó AZONOSÍTÓját a szolgáltatás által elhelyezett kimenő hívásokhoz. Ez hasznos lehet az ügyfélszolgálatnál és a hangalapú értesítési forgatókönyvekben.

## <a name="countryregion-availability"></a>Ország/régió elérhetősége

A következő táblázat azt mutatja be, hogy hol szerezhetők be különböző típusú telefonszámok, valamint a bejövő és kimenő hívási és SMS-funkciók, amelyek az ilyen típusú telefonszámokhoz vannak társítva.

|Szám típusa| Számok beolvasása a | Hívások kezdeményezése                                        | Hívások fogadása innen:                                    |Üzenetek küldése a következőnek       | Üzenetek fogadása innen: |
|-----------| ------------------ | ---------------------------------------------------  |-------------------------------------------------------|-----------------------|--------|
| Helyi (földrajzi)  | USA                 | USA, Kanada, Egyesült Királyság, Németország, Franciaország,. + További *| USA, Kanada, Egyesült Királyság, Németország, Franciaország,. + További * |Nem érhető el| Nem érhető el |
| Toll-Free | USA                 | USA                                                   | USA                                                    |USA                | USA |

* A hívási célokkal és a díjszabással kapcsolatos további információkért tekintse meg a [díjszabási oldalt](../pricing.md).


## <a name="next-steps"></a>Következő lépések

### <a name="quickstarts"></a>Rövid útmutatók

- [Telefonszám beszerzése](../../quickstarts/telephony-sms/get-phone-number.md)
- [Hívás elhelyezése](../../quickstarts/voice-video-calling/calling-client-samples.md)
- [SMS küldése](../../quickstarts/telephony-sms/send.md)

### <a name="conceptual-documentation"></a>Fogalmi dokumentáció

- [A hang- és videóhívással kapcsolatos alapfogalmak](../voice-video-calling/about-call-types.md)
- [Telefonos fogalmak](./telephony-concept.md)
- [Folyamatok meghívása](../call-flows.md)
- [Díjszabás](../pricing.md)
