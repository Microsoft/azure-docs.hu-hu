---
title: A külső együttműködés biztonsági helyzetének meghatározása Azure Active Directory
description: A külső hozzáférési biztonsági terv végrehajtása előtt el kell döntenie, hogy mit szeretne elérni.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37c27e84f15a01a2d8832baae137518685de59a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98725441"
---
# <a name="determine-your-security-posture-for-external-access"></a>A külső hozzáférés biztonsági helyzetének meghatározása 

A külső hozzáférés szabályozása során figyelembe kell vennie a szervezet biztonsági és együttműködési igényeit, valamint az egyes forgatókönyveken belül. A szervezeti szinten vegye figyelembe, hogy az informatikai csapatnak szüksége van a napi együttműködésre. A szabályozott iparágakban működő szervezeteknek több informatikai felügyeletre lehet szükségük. Előfordulhat például, hogy egy védelmi vállalkozónak pozitívan kell azonosítania és dokumentálnia az egyes külső felhasználókat, az azokhoz való hozzáférést és a hozzáférés eltávolítását. Ez a követelmény minden hozzáféréshez, illetve adott helyzetekben vagy munkaterhelésekhez is tartozhat. A spektrum másik végén a tanácsadó cég általában lehetővé teszi a végfelhasználók számára, hogy meghatározzák azokat a külső felhasználókat, akiknek szükségük van az együttműködésre a alkalmazásban, az informatikai Gárda-síneken belül. 

![A felhasználók és az együttműködés végfelhasználói felügyelete](media/secure-external-access/1-overall-control.png)

> [!NOTE]
> Az együttműködés túlságosan szigorú szabályozása magasabb informatikai költségvetésekhez, a termelékenység csökkenéséhez és a késleltetett üzleti eredményekhez vezethet. Ha a hivatalos együttműködési csatornák túlságosan terhesnek minősülnek, a végfelhasználók általában az általa megadott rendszerekkel látják el a munkájukat, például a nem biztonságos dokumentumok e-mail-címére.

## <a name="think-in-terms-of-scenarios"></a>Forgatókönyvek szerint

Sok esetben a partnerek hozzáférését legalább bizonyos helyzetekben delegálhatja, miközben biztonsági réseket biztosít az Guard Rails számára. Az IT Guard Rails segítségével biztosítható, hogy a szellemi tulajdon biztonságban maradjon, miközben az alkalmazottak a partnerekkel együttműködve dolgozhatnak a munkával.

A szervezeten belüli forgatókönyvek figyelembevételével felméri az alkalmazottak és az üzleti partnerek erőforrásokhoz való hozzáférésének szükségességét. Előfordulhat, hogy egy bank olyan megfelelőségi szükségletekkel rendelkezik, amelyek bizonyos erőforrásokhoz, például a felhasználói fiók adataihoz való hozzáférést korlátozzák a belső alkalmazottak egy kis csoportjára. Ezzel szemben ugyanez a bank engedélyezheti a delegált hozzáférést a marketing kampányon dolgozó partnerek számára.

![az irányítás folytonossága forgatókönyv szerint](media\secure-external-access\1-scenarios.png)

Az egyes forgatókönyvekben vegye figyelembe az alábbiakat: 

* a veszélyeztetett információk érzékenysége

* azt határozza meg, hogy szeretné-e korlátozni, hogy mely partnerek láthatják a többi felhasználót

* a megsértés díja és a központosított szabályozás és a végfelhasználói súrlódás súlya

 Emellett központilag felügyelt vezérlőkkel is elindíthatja a megfelelőségi célok teljesítését és a vezérlés delegálását a végfelhasználók számára az idő múlásával. Az összes hozzáférés-kezelési modell egyidejűleg létezhet egy szervezeten belül. 

A partner által [felügyelt hitelesítő adatok](../external-identities/what-is-b2b.md) használata olyan alapvető jelzést biztosít a szervezet számára, amely leállítja az erőforrásokhoz való hozzáférést, ha a külső felhasználó elvesztette a saját vállalata erőforrásaihoz való hozzáférést.

## <a name="goals-of-securing-external-access"></a>A külső hozzáférés biztonságossá tételének céljai

Az informatikai irányítás és a delegált hozzáférés céljai eltérnek.

**Az informatikai irányítás elsődleges célja a következő:**

* Az irányítási, szabályozási és megfelelőségi (GRC) célok elérése. 

* A partnerek hozzáférésének szigorú szabályozása, valamint a tagokkal, csoportokkal és egyéb partnerekkel kapcsolatos felhasználók.

**A hozzáférés delegálásának elsődleges célja a következő:**

* Lehetővé teheti, hogy az üzleti tulajdonosok szabályozzák, hogy kik működjenek együtt a szolgáltatásban.

* Lehetővé teheti az üzleti partnerek számára, hogy az üzleti tulajdonosok által meghatározott szabályok alapján kérjenek hozzáférést.

A szervezete és a forgatókönyvei közül a következőket kell elvégeznie: 

* **Az alkalmazásokhoz, az adattartalomhoz és a tartalmakhoz való hozzáférés szabályozása**. Ez számos különböző módszerrel valósítható meg az [Azure ad](https://azure.microsoft.com/pricing/details/active-directory/) és a [Microsoft 365](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans)verziójától függően. 

* **Csökkentse a támadási felületet**. A [Kiemelt identitások kezelése](../privileged-identity-management/pim-configure.md), az [adatvesztés-megelőzés (DLP)](/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) és a [titkosítási képességek](/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) csökkentik a támadási felületet.

* **A megfelelőség megerősítéséhez rendszeresen tekintse át a tevékenységeket és a** naplót. A jogosultsági felügyeleten keresztül delegálhatja a hozzáférési döntéseket az üzleti tulajdonosoknak, míg a hozzáférési felülvizsgálatok lehetővé teszik a folyamatos hozzáférés rendszeres időközönkénti megerősítését. Az adatelemzési címkékkel rendelkező automatizált adatok besorolásával automatizálható a bizalmas tartalmak titkosítása, így az alkalmazottak a felhasználók számára könnyen betartják a felhasználókat.

## <a name="next-steps"></a>Következő lépések 

Tekintse meg a következő cikkeket az erőforrásokhoz való külső hozzáférés biztonságossá tételéhez. Javasoljuk, hogy a felsorolt sorrendben végezze el a műveleteket.

1. [Határozza meg a külső hozzáférés biztonsági](1-secure-access-posture.md) állapotát (itt van.)

2. [Aktuális állapot felderítése](2-secure-access-current-state.md)

3. [Irányítási terv létrehozása](3-secure-access-plan.md)

4. [Biztonsági csoportok használata](4-secure-access-groups.md)

5. [Áttérés az Azure AD B2B-re](5-secure-access-b2b.md)

6. [Biztonságos hozzáférés a jogosultságok kezelésével](6-secure-access-entitlement-managment.md)

7. [Biztonságos hozzáférés feltételes hozzáférési szabályzatokkal](7-secure-access-conditional-access.md)

8. [Biztonságos hozzáférés érzékenységi címkékkel](8-secure-access-sensitivity-labels.md)

9. [Biztonságos hozzáférés a Microsoft Teams, a OneDrive és a SharePoint rendszerhez](9-secure-access-teams-sharepoint.md)
 

