---
title: Az összes Azure Security Center javaslat hivatkozási táblázata
description: Ez a cikk Azure Security Center biztonsági javaslatait sorolja fel, amelyek segítségével megerősítheti és megvédheti erőforrásait.
author: memildin
ms.service: security-center
ms.topic: reference
ms.date: 03/22/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 2d9f76bd3c6ad0060a90f52abba1c7378b310e67
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801484"
---
# <a name="security-recommendations---a-reference-guide"></a>Biztonsági javaslatok – gyorsútmutató

Ez a cikk a Azure Security Centerban esetlegesen megjelenő javaslatokat sorolja fel. A környezetben megjelenő javaslatok a védeni kívánt erőforrásoktól és a testreszabott konfigurációtól függenek.

Security Center javaslatai az [Azure biztonsági teljesítményteszten](../security/benchmarks/introduction.md)alapulnak. Az Azure biztonsági teljesítményteszt a Microsoft által létrehozott, Azure-specifikus irányelvek a biztonsági és megfelelőségi szabályzatok közös megfelelőségi keretrendszereken alapuló bevált eljárásaihoz. Ez a széles körben tiszteletben lévő teljesítményteszt a [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) és a [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) által vezérelt vezérlőkre épül, és a felhő-központú biztonságra összpontosít.

Ha többet szeretne megtudni ezekről a javaslatokról, tekintse meg a [Azure Security Center javaslatainak szervizelésével](security-center-remediate-recommendations.md)foglalkozó témakört.

A biztonságos pontszám a befejezett Security Center javaslatok számán alapul. Annak eldöntéséhez, hogy mely ajánlásokat kell elsőként feloldani, tekintse meg a súlyosságát, és annak lehetséges hatását a biztonságos pontszámra.

> [!TIP]
> Ha egy javaslat leírása "nincs kapcsolódó szabályzat", ez általában azért van, mert a javaslat egy másik javaslattól és _annak_ házirendjétől függ. Tegyük fel például, hogy "az Endpoint Protection állapotával kapcsolatos hibák elhárítása szükséges..." arra a javaslatra támaszkodik, amely ellenőrzi, hogy az Endpoint Protection-megoldás még _telepítve_ van-e ("Endpoint Protection-megoldás telepítése..."). Az alapul szolgáló _javaslat szabályzattal rendelkezik._
> A házirendek csak az alapszintű javaslatokra való korlátozása egyszerűsíti a házirendek kezelését.

## <a name="appservices-recommendations"></a><a name='recs-appservices'></a>AppServices javaslatok

[!INCLUDE [asc-recs-appservices](../../includes/asc-recs-appservices.md)]

## <a name="compute-recommendations"></a><a name='recs-compute'></a>Számítási javaslatok

[!INCLUDE [asc-recs-compute](../../includes/asc-recs-compute.md)]

## <a name="container-recommendations"></a><a name='recs-container'></a>Tárolójavaslatok

[!INCLUDE [asc-recs-container](../../includes/asc-recs-container.md)]

## <a name="data-recommendations"></a><a name='recs-data'></a>Az adatjavaslatok

[!INCLUDE [asc-recs-data](../../includes/asc-recs-data.md)]

## <a name="identityandaccess-recommendations"></a><a name='recs-identityandaccess'></a>IdentityAndAccess javaslatok

[!INCLUDE [asc-recs-identityandaccess](../../includes/asc-recs-identityandaccess.md)]

## <a name="iot-recommendations"></a><a name='recs-iot'></a>IoT javaslatok

[!INCLUDE [asc-recs-iot](../../includes/asc-recs-iot.md)]

## <a name="networking-recommendations"></a><a name='recs-networking'></a>Hálózatokra vonatkozó javaslatok

[!INCLUDE [asc-recs-networking](../../includes/asc-recs-networking.md)]

## <a name="deprecated-recommendations"></a>Elavult javaslatok

|Ajánlás|Leírás & kapcsolódó szabályzat|Súlyosság|Gyors javítás engedélyezve? ([További információ](security-center-remediate-recommendations.md#quick-fix-remediation))|Erőforrás típusa|
|----|----|----|----|----|
|**A App Serviceshoz való hozzáférést korlátozni kell**|Korlátozza a hozzáférést a App Services a hálózati konfiguráció módosításával, hogy megtagadja a bejövő forgalmat a túl széles tartományokból.<br>(Kapcsolódó szabályzat: [előzetes verzió]: a App Services elérését korlátozni kell)|Magas|N|App Service|
|**A IaaS-NSG lévő webalkalmazások szabályait meg kell erősíteni**|Megerősítheti a webalkalmazásokat futtató virtuális gépek hálózati biztonsági csoportját (NSG), valamint a webalkalmazás-portok tekintetében túlzottan megengedhető NSG-szabályokat.<br>(Kapcsolódó szabályzat: a IaaS webalkalmazásaihoz tartozó NSG-szabályokat meg kell erősíteni)|Magas|N|Virtuális gép|
|**A pod biztonsági házirendeket úgy kell meghatározni, hogy csökkentse a támadási vektort a szükségtelen alkalmazási jogosultságok eltávolításával (előzetes verzió)**|Határozza meg a pod biztonsági szabályzatokat a szükségtelen alkalmazás-jogosultságok eltávolításával a támadási vektor csökkentése érdekében. Ajánlott a pod biztonsági szabályzatok konfigurálása, hogy a hüvelyek csak azokhoz az erőforrásokhoz férhessenek hozzá, amelyekhez hozzáférésük engedélyezett.<br>(Kapcsolódó szabályzat: [előzetes verzió]: a pod biztonsági szabályzatokat meg kell határozni a Kubernetes-szolgáltatásokon)|Közepes|N|Számítási erőforrások (tárolók)|
|**A IoT biztonsági modul Azure Security Center telepítése a IoT-eszközök láthatóságának megismeréséhez**|Telepítse Azure Security Center a IoT biztonsági modulhoz, és ismerkedjen meg a IoT-eszközökkel.|Alacsony|N|IoT-eszköz|

## <a name="next-steps"></a>Következő lépések

A javaslatokkal kapcsolatos további tudnivalókért tekintse meg a következőket:

- [Mik azok a biztonsági szabályzatok, kezdeményezések és javaslatok?](security-policy-concept.md)
- [Biztonsági javaslatok áttekintése](security-center-recommendations.md)
