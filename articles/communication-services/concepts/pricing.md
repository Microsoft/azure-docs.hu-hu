---
title: Díjszabás a telefonáláshoz (hang/videó) és csevegéshez
titleSuffix: An Azure Communication Services concept document
description: Ismerje meg a kommunikációs szolgáltatások díjszabási modelljét.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5f7b1e6d600f5d3652ce6a66a72cbfbf33b336c4
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091871"
---
# <a name="pricing-scenarios"></a>Díjszabási forgatókönyvek

Az Azure kommunikációs szolgáltatások díjszabása általában az utólagos elszámolású modellen alapul. A következő példákban szereplő díjak szemléltető célokat szolgálnak, és nem feltétlenül tükrözik a legújabb Azure-díjszabást.

## <a name="voicevideo-calling-and-screen-sharing"></a>Hang/videó hívása és a képernyő megosztása

Az Azure kommunikációs szolgáltatások lehetővé teszik hang-és videohívások hozzáadását az alkalmazásokhoz. A felhasználói élményt JavaScript, Objective-C (Apple), Java (Android) vagy .NET SDK-k használatával ágyazhatja be alkalmazásaiba. Tekintse meg az [elérhető SDK-k teljes listáját](./sdk-options.md).

### <a name="pricing"></a>Díjszabás

A hívási és a képernyő-megosztási szolgáltatásokat felhasználónként percenként $0,004-kor számoljuk el a csoportos hívások esetében. A különböző hívások lehetséges folyamatainak megismeréséhez tekintse meg [ezt a lapot](./call-flows.md).

A hívás minden résztvevője a meghíváshoz kapcsolódó percenkénti számlázással fog számolni. Ez attól függetlenül igaz, hogy a felhasználó videohívás, hanghívás vagy képernyő-megosztás.

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-sdks"></a>Díjszabási példa: hang/videó hívásának csoportosítása JS és iOS SDK-k használatával

Alice csoportos hívást készített a kollégáival, Bob és Charlie-val. Alice és Bob használta a JS SDK-kat, Charlie iOS SDK-kat.

- A hívás összesen 60 percig tart.
- Alice és Bob részt vett a teljes hívásban. Alice öt percen belül bekapcsolta a videóját, és 23 percen keresztül megosztotta a képernyőjét. Bob megkapta a videót a teljes híváshoz (60 perc), és 12 percig megosztva a képernyőt.
- Charlie 43 perc elteltével elhagyta a hívást. Charlie használta a hang-és video-használati időt (43 perc).

**Költségszámítások**

- 2 résztvevő x 60 perc x $0,004/résztvevő/perc = $0,48 [a videó és az audió díja azonos a díjszabásban]
- 1 résztvevő x 43 perc x $0,004/résztvevő/perc = $0,172 [a videó és az audió díja azonos a díjszabásban]

**A csoportos hívás teljes költsége**: $0,48 + $0,172 = $0,652


### <a name="pricing-example-outbound-call-from-app-using-js-sdk-to-a-pstn-number"></a>Díjszabási példa: az alkalmazás kimenő hívása a JS SDK-val egy PSTN-számra

Alice PSTN-hívást kezdeményez az alkalmazásból az USA-beli telefonszámán kezdődően `+1-425` .

- Alice használta a JS SDK-t az alkalmazás létrehozásához.
- A hívás összesen 5 percet vesz igénybe.

**Költségszámítások**

- 1 résztvevő a VoIP-lábát (Alice) az alkalmazásról a kommunikációs szolgáltatások kiszolgálóira x 10 perc x $0,004/percben résztvevő percenként = $0,04
- 1 résztvevő a kommunikációs szolgáltatások kiszolgálóiról egy USA-beli telefonszámra x 10 perc x $0,013/perc = $0,13.

Megjegyzés: az USA vegyes díjszabása `+1-425` $0,013. A részletekért tekintse meg a következő hivatkozást: https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv)

**A csoportos hívás teljes költsége**: $0,04 + $0,13 = $0,17


### <a name="pricing-example-group-audio-call-using-js-sdk-and-1-pstn-leg"></a>Díjszabási példa: hanghívás csoportosítása JS SDK és 1 PSTN-láb használatával

Alice és Bob VOIP-híváson keresztül. Bob megkezdte a Charlie-ra irányuló, Charlie-beli PSTN-számon, az Egyesült államokbeli telefonszámon megjelenő hívást `+1-425` .

- Alice használta a JS SDK-t az alkalmazás létrehozásához. Körülbelül 10 percet beszéltünk, mielőtt meghívja Charlie-t a PSTN-számon.
- Miután Bob megkapta a hívást a Charlie-ra a PSTN-számon, a hárman egy másik 10 percig szólalnak meg.

**Költségszámítások**

- 2 résztvevői a VoIP-láb (Alice és Bob) az alkalmazásról a kommunikációs szolgáltatások kiszolgálóira x 20 perc x $0,004/résztvevős percenként = $0,16
- 1 résztvevő a kommunikációs szolgáltatások kiszolgálóinak a PSTN kimenő lábán (Charlie) a US telefonszám x 10 perc x $0,013/résztvevős percenként = $0,13

Megjegyzés: az USA vegyes díjszabása `+1-425` $0,013. A részletekért tekintse meg a következő hivatkozást: https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv)

**A VoIP + eszkalációs hívás teljes költsége**: $0,16 + $0,13 = $. 29


### <a name="pricing-example-a-user-of-the-communication-services-javascript-sdk-joins-a-scheduled-microsoft-teams-meeting"></a>Díjszabási példa: a kommunikációs szolgáltatások JavaScript SDK-felhasználója egy ütemezett Microsoft Teams-találkozóhoz csatlakozik

Alice egy orvos, aki a betegével találkozott, Bob. Alice csatlakozik a csapat asztali alkalmazásban található látogatáshoz. Bob a kommunikációs szolgáltatások JavaScript SDK-val való összekapcsolására szolgáló hivatkozást kap, amely az egészségügyi szolgáltató webhelyét használja. Bob a mobiltelefonnal fogja használni az értekezletet egy webböngészővel (iPhone Safari használatával). A csevegés a virtuális látogatás során lesz elérhető.

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
- Felhasználói csatlakozás a kommunikációs szolgáltatások JavaScript SDK használatával: $0,12 + $0,0024 = $0,1224
- Felhasználói csatlakozás a Teams Desktop alkalmazáshoz: $0 (a csapat licence vonatkozik rájuk)


## <a name="chat"></a>Csevegés

A kommunikációs szolgáltatások segítségével javíthatja az alkalmazást, hogy két vagy több felhasználó között küldjön és fogadhat csevegési üzeneteket. A csevegési SDK-k JavaScript, .NET, Python és Java rendszerekhez érhetők el. Az [SDK-k megismeréséhez](./sdk-options.md) tekintse meg ezt a lapot

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
