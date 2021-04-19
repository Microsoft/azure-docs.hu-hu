---
title: Referenciatábla az összes Azure Security Center számára
description: Ez a cikk Azure Security Center az erőforrások védelmét és védelmét segítő biztonsági javaslatokat.
author: memildin
ms.service: security-center
ms.topic: reference
ms.date: 04/06/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: e994aead1840fd3ef9b57e92cf95e94837608d7a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719132"
---
# <a name="security-recommendations---a-reference-guide"></a>Biztonsági javaslatok – gyorsútmutató

Ez a cikk felsorolja a cikkben esetleg látható Azure Security Center. A környezetben megjelenő javaslatok a védett erőforrásoktól és a testreszabott konfigurációtól függenek.

Security Center javaslatok az Azure biztonsági [teljesítménytesztjére épülnek.](https://docs.microsoft.com/security/benchmark/azure/introduction)
Az Azure biztonsági teljesítménytesztje a Microsoft által írt, Azure-specifikus irányelvek készlete a közös megfelelőségi keretrendszeren alapuló biztonsági és megfelelőségi ajánlott eljárásokhoz. Ez a széles körben elterjedt teljesítményteszt a [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) és a National Institute of Standards and Technology [(NIST)](https://www.nist.gov/) vezérlőire épül, és a felhőközpontú biztonságra összpontosít.

A javaslatokra való válaszadással kapcsolatos további információkért lásd: Javaslatok szervize [a Azure Security Center.](security-center-remediate-recommendations.md)

A biztonsági pontszám az ön által Security Center javaslatok számán alapul. Annak eldöntését, hogy mely javaslatokat kell elsőként megoldani, nézze meg az egyes ajánlások súlyosságát és lehetséges hatását a biztonsági pontszámra.

> [!TIP]
> Ha egy javaslat leírása szerint "Nincs kapcsolódó szabályzat", annak általában az az oka, hogy a javaslat egy másik javaslattól és annak _szabályzatán függ._ Például az "Endpoint Protection állapothibáit helyre kell orvosolni..." javaslat arra a javaslatra támaszkodik, amely ellenőrzi, hogy telepítve van-e végpontvédelmi megoldás _("Endpoint_ Protection-megoldást kell telepíteni..."). Az alapul szolgáló _javaslat rendelkezik_ szabályzatokkal.
> A szabályzatok csak az alap ajánlásra való korlátozása leegyszerűsíti a szabályzatkezelést.

## <a name="appservices-recommendations"></a><a name='recs-appservices'></a>AppServices-javaslatok

[!INCLUDE [asc-recs-appservices](../../includes/asc-recs-appservices.md)]

## <a name="compute-recommendations"></a><a name='recs-compute'></a>Számítási javaslatok

[!INCLUDE [asc-recs-compute](../../includes/asc-recs-compute.md)]

## <a name="container-recommendations"></a><a name='recs-container'></a>Tárolójavaslatok

[!INCLUDE [asc-recs-container](../../includes/asc-recs-container.md)]

## <a name="data-recommendations"></a><a name='recs-data'></a>Adatara vonatkozó javaslatok

[!INCLUDE [asc-recs-data](../../includes/asc-recs-data.md)]

## <a name="identityandaccess-recommendations"></a><a name='recs-identityandaccess'></a>IdentityAndAccess-javaslatok

[!INCLUDE [asc-recs-identityandaccess](../../includes/asc-recs-identityandaccess.md)]

## <a name="iot-recommendations"></a><a name='recs-iot'></a>IoT-javaslatok

[!INCLUDE [asc-recs-iot](../../includes/asc-recs-iot.md)]

## <a name="networking-recommendations"></a><a name='recs-networking'></a>Hálózatokra vonatkozó javaslatok

[!INCLUDE [asc-recs-networking](../../includes/asc-recs-networking.md)]

## <a name="deprecated-recommendations"></a>Elavult javaslatok

|Ajánlás|A & szabályzat leírása|Súlyosság|
|----|----|----|
|A hozzáférés App Services korlátozva kell lennie|Korlátozza a App Services hozzáférését a hálózati konfiguráció módosításával, hogy letiltsa a túl tág tartományokból bejövő forgalmat.<br>(Kapcsolódó szabályzat: [Előzetes verzió]: A App Services hozzáférését korlátozni kell)|Magas|
|Az IaaS NSG-k webalkalmazásaira vonatkozó szabályokat meg kell ásni|A webalkalmazásokat futtató virtuális gépek hálózati biztonsági csoportjának (NSG-nek) a webalkalmazások portjaira vonatkozó túl megengedő NSG-szabályokkal való megeredését.<br>(Kapcsolódó szabályzat: Az IaaS-alapú webalkalmazások NSG-szabályait meg kell fektetni)|Magas|
|A podok biztonsági szabályzatait úgy kell meghatározni, hogy a szükségtelen alkalmazás-jogosultságok eltávolításával csökkentse a támadási vektort (előzetes verzió)|Podbiztonsági szabályzatok definiálása a támadási vektor csökkentéséhez a szükségtelen alkalmazás-jogosultságok eltávolításával. Ajánlott úgy konfigurálni a podbiztonsági szabályzatokat, hogy a podok csak olyan erőforrásokhoz férnek hozzá, amelyekhez hozzáféréssel rendelkezik.<br>(Kapcsolódó szabályzat: [Előzetes verzió]: A podok biztonsági szabályzatát meg kell határozni a Kubernetes Servicesben)|Közepes|
|Az Azure Security Center Az IoT biztonsági modul telepítése az IoT-eszközök jobb átláthatósága érdekében|Telepítse Azure Security Center IoT biztonsági modulhoz, hogy jobban áttekintsen az IoT-eszközökbe.|Alacsony|
|A rendszerfrissítések alkalmazásához újra kell indítani a gépeket|Indítsa újra a gépeket a rendszerfrissítések alkalmazásához és a gép biztonsági résekkel szembeni biztonságához. (Kapcsolódó szabályzat: A rendszerfrissítéseket telepíteni kell a gépekre)|Közepes|
|A monitorozási ügynököt telepíteni kell a gépekre|Ez a művelet telepít egy figyelőügynöket a kiválasztott virtuális gépekre. Válasszon ki egy munkaterületet, amely számára az ügynök jelentést készít. (Nincs kapcsolódó szabályzat)|Magas|
||||

## <a name="next-steps"></a>Következő lépések

A javaslatokkal kapcsolatos további információkért tekintse meg a következőket:

- [Mik azok a biztonsági szabályzatok, kezdeményezések és javaslatok?](security-policy-concept.md)
- [Biztonsági javaslatok áttekintése](security-center-recommendations.md)
