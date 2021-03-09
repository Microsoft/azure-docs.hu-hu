---
title: Díjszabás a telefonáláshoz (hang/videó) és csevegéshez
titleSuffix: An Azure Communication Services concept document
description: Ismerje meg a kommunikációs szolgáltatások díjszabási modelljét.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/29/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5c8c4808e735a872b31deb6855646dae90c407c9
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487678"
---
# <a name="pricing-scenarios"></a>Díjszabási forgatókönyvek

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


Az Azure kommunikációs szolgáltatások díjszabása általában az utólagos elszámolású modellen alapul. A következő példákban szereplő díjak szemléltető célokat szolgálnak, és nem feltétlenül tükrözik a legújabb Azure-díjszabást.

## <a name="voicevideo-calling-and-screen-sharing"></a>Hang/videó hívása és a képernyő megosztása

Az Azure kommunikációs szolgáltatások lehetővé teszik hang-és videohívások hozzáadását az alkalmazásokhoz. A felhasználói élményt a JavaScript, Objective-C (Apple), Java (Android) vagy .NET ügyféloldali kódtárak használatával ágyazhatja be alkalmazásaiba. Tekintse meg az [elérhető ügyféloldali kódtárak teljes listáját](./sdk-options.md).

### <a name="pricing"></a>Díjszabás

A hívási és a képernyő-megosztási szolgáltatásokat felhasználónként percenként $0,004-kor számoljuk el a csoportos hívások esetében. A különböző hívások lehetséges folyamatainak megismeréséhez tekintse meg [ezt a lapot](./call-flows.md).

A hívás minden résztvevője a meghíváshoz kapcsolódó percenkénti számlázással fog számolni. Ez attól függetlenül igaz, hogy a felhasználó videohívás, hanghívás vagy képernyő-megosztás.

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-client-libraries"></a>Díjszabási példa: hang/videó hívásának csoportosítása JS-és iOS-ügyféloldali kódtárak használatával

Alice csoportos hívást készített a kollégáival, Bob és Charlie-val. Alice és Bob használta a JS ügyféloldali kódtárakat, Charlie iOS ügyféloldali kódtárakat.

- A hívás összesen 60 percig tart.
- Alice és Bob részt vett a teljes hívásban. Alice öt percen belül bekapcsolta a videóját, és 23 percen keresztül megosztotta a képernyőjét. Bob megkapta a videót a teljes híváshoz (60 perc), és 12 percig megosztva a képernyőt.
- Charlie 43 perc elteltével elhagyta a hívást. Charlie használta a hang-és video-használati időt (43 perc).

**Költségszámítások**

- 2 résztvevő x 60 perc x $0,004/résztvevő/perc = $0,48 [a videó és az audió díja azonos a díjszabásban]
- 1 résztvevő x 43 perc x $0,004/résztvevő/perc = $0,172 [a videó és az audió díja azonos a díjszabásban]

**A csoportos hívás teljes költsége**: $0,48 + $0,172 = $0,652

### <a name="pricing-example-a-user-of-the-communication-services-js-client-library-joins-a-scheduled-microsoft-teams-meeting"></a>Díjszabási példa: a kommunikációs szolgáltatások JS ügyféloldali kódtár felhasználója egy ütemezett Microsoft Teams-találkozóhoz csatlakozik

Alice egy orvos, aki a betegével találkozott, Bob. Alice csatlakozik a csapat asztali alkalmazásban található látogatáshoz. Bob kap egy hivatkozást az egészségügyi szolgáltató webhelyéről való csatlakozásra, amely a kommunikációs szolgáltatások JS ügyféloldali kódtár használatával csatlakozik az értekezlethez. Bob a mobiltelefonnal fogja használni az értekezletet egy webböngészővel (iPhone Safari használatával). A csevegés a virtuális látogatás során lesz elérhető.

- A hívás összesen 30 percet vesz igénybe.
- Alice és Bob részt vesz a teljes hívásban. Alice bekapcsolja a videót öt perccel a hívás elindítása után, és megosztja a képernyőjét 13 percig. Bob a teljes híváshoz tartozó videóval rendelkezik.
- Alice öt üzenetet küld, Bob válasza három üzenettel.


**Költségszámítások**

- 1 résztvevő (Bob) x 30 perc x $0,004/résztvevő/perc = $0,12 [a videó és az audió díja azonos a díjszabásban]
- 1 résztvevő (Alice) x 30 perc x $0,000/résztvevő/perc = $0,0 *.
- 1 résztvevő (Bob) x 3 csevegési üzenet x $0,0008 = $0,0024.
- 1 résztvevő (Alice) x 5 csevegési üzenet x $0,000 = $0,0 *.

* Alice részvételét a csapatának licence szabályozza. Az Azure-számlán megtekintheti azokat a perceket és csevegéseket, amelyeket a felhasználók a kommunikációs szolgáltatásokat használó felhasználók számára biztosítanak az Ön kényelme érdekében, de ezek a percek és a Teams-ügyféltől származó üzenetek nem kerülnek be

**A látogatás teljes költsége**:
- Felhasználói csatlakozás a kommunikációs szolgáltatások JS-ügyféloldali kódtár használatával: $0,12 + $0,0024 = $0,1224
- Felhasználói csatlakozás a Teams Desktop alkalmazáshoz: $0 (a csapat licence vonatkozik rájuk)


## <a name="chat"></a>Csevegés

A kommunikációs szolgáltatások segítségével javíthatja az alkalmazást, hogy két vagy több felhasználó között küldjön és fogadhat csevegési üzeneteket. A csevegési ügyféloldali kódtárak a JavaScript, a .NET, a Python és a Java esetében érhetők el. Tekintse meg [ezt a lapot az ügyféloldali kódtárak megismeréséhez](./sdk-options.md)

### <a name="price"></a>Ár

Minden elküldött Csevegésnél $0,0008 díjat számítunk fel.

### <a name="pricing-example-chat-between-two-users"></a>Díjszabási példa: csevegés két felhasználó között

A-vel egy olyan csevegési szálat indít el, amely az Emily használatával megoszt egy frissítést, és 5 üzenetet küld. A csevegés 10 percet vesz igénybe. A és az Emily egy másik 15 üzenetet küld.

**Költségszámítások**
- Küldött üzenetek száma (5 + 15 + 15) x $0,0008 = $0,028

### <a name="pricing-example-group-chat-with-multiple-users"></a>Díjszabási példa: csoportos csevegés több felhasználóval

Charlie elindít egy csevegési szálat barátaival Casey & Jasmine-vel, hogy megtervezze a nyaralást. Beszélgetnek egy darabig, amelyben Charlie, Casey & jázmin 20, 30 és 18 üzenetet küld. Tisztában vagyunk azzal, hogy a barát Rose-nak érdekes lehet az utazáshoz való csatlakozás is, így hozzá kell adnia a csevegési szálhoz, és meg kell osztania az összes korábbi üzenetet.

Rose látja az üzeneteket, és elindítja a csevegést. Az időpontban egy hívást kezdeményeznek, és később úgy dönt, hogy felveszi a beszélgetést. Charlie, jázmin & Rose dönt az utazási dátumokról, és egy újabb 30, 25, 35 üzenetet küld.

**Költségszámítások**

- Küldött üzenetek száma (20 + 30 + 18 + 30 + 25 + 35) x $0,0008 = $0,1264


## <a name="telephony-and-sms"></a>Telefonos funkciók és SMS

## <a name="price"></a>Ár

A telefonos szolgáltatások díjszabása percenként történik, az SMS-t pedig az üzenetek díjszabása alapján számítjuk fel. A díjszabást a használt szám típusa és helye, valamint a hívások és az SMS-üzenetek rendeltetése határozza meg.

### <a name="telephone-number-leasing"></a>Telefonszám-lízing

A telefonszám-bérlet díjait előre fel kell tölteni, majd a hónapról hónapra megismétlődnek a következők:

|Számtípus   |Havi díj   |
|--------------|-----------|
|Helyi (Egyesült Államok)     |1/hó        |
|Díjmentes (Egyesült Államok) |2/hó |


### <a name="telephone-calling"></a>Telefonos hívás

A hagyományos telefonhívás (amely a nyilvános kapcsolós telefonvonalon keresztül fordul elő) a Egyesült Államokon alapuló telefonszámok utólagos elszámolású díjszabásával érhető el. Az ár percenkénti díj, amely a felhasznált szám és a hívás célhelye alapján történik. A legnépszerűbb hívási célok díjszabását az alábbi táblázat tartalmazza. A célhelyek teljes listájáért tekintse meg a [részletes díjszabási listát](https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv) .


#### <a name="united-states-calling-prices"></a>Meghívási díjak Egyesült Államok

A következő díjak a szükséges kommunikációs adókat és díjakat tartalmazzák a 2021. június 30-ig:

|Számtípus   |Hívások kezdeményezése   |Hívások fogadása|
|--------------|-----------|------------|
|Helyi     |Kezdés: $0.013/perc       |$0.0085/perc        |
|Díjmentes |$0.013/perc   |$0.0220/perc |

#### <a name="other-calling-destinations"></a>Egyéb hívási célhelyek

A következő díjak a szükséges kommunikációs adókat és díjakat tartalmazzák a 2021. június 30-ig:

|Hívások kezdeményezése   |Percenkénti díj|
|-----------|------------|
|Kanada     |Kezdés: $0.013/perc   |
|Egyesült Királyság     |Kezdés: $0.015/perc   |
|Németország     |Kezdés: $0.015/perc   |
|Franciaország     |Kezdés: $0.016/perc   |


### <a name="sms"></a>SMS

Az SMS utólagos elszámolású díjszabást kínál. Az ár az üzenet célján alapuló díj. Az üzeneteket ingyenesen hívható telefonszámok is elküldhetik a Egyesült Államokban lévő telefonszámokra. Vegye figyelembe, hogy a helyi (földrajzi) telefonszámok nem használhatók SMS-üzenetek küldésére.

A következő díjak a szükséges kommunikációs adókat és díjakat tartalmazzák a 2021. június 30-ig:

|Ország   |Üzenetek küldése|Üzenetek fogadása|
|-----------|------------|------------|
|USA (díjmentes)    |$0.0075/msg   | $0.0075/msg |
