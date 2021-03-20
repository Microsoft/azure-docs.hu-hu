---
title: Az Azure hibrid felhőalapú identitásának kialakításához szükséges identitási követelmények | Microsoft Docs
description: Azonosítsa a vállalat üzleti igényeit, amely a hibrid identitás kialakítására vonatkozó követelmények meghatározását fogja eredményezni.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: de690978-84ef-41ad-9dfe-785722d343a1
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 074c203a0a5688855d4f7607a877b25ce6ee6ad7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89660572"
---
# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>A hibrid identitási megoldás identitási követelményeinek meghatározása
A hibrid identitás megoldásának első lépéseként meg kell határozni a megoldást használó üzleti szervezet követelményeit.  A hibrid identitás támogató szerepkörként indul (az összes többi felhőalapú megoldást támogatja a hitelesítés biztosításával), és új és érdekes képességeket biztosít a felhasználók új munkaterhelésének feloldásához.  Ezek a munkaterhelések vagy szolgáltatások, amelyeket a felhasználók számára kíván alkalmazni, a hibrid identitás kialakítására vonatkozó követelményeket határozzák meg.  Ezeknek a szolgáltatásoknak és munkaterheléseknek a hibrid identitást kell használniuk a helyszíni és a felhőben egyaránt.  

Ezeket a kulcsfontosságú szempontokat kell megismernie, hogy megértse, mi a követelmény most, és mi a vállalat tervei a jövőben. Ha nem látja el a hibrid identitások kialakítására vonatkozó hosszú távú stratégiát, akkor előfordulhat, hogy a megoldás nem lesz skálázható, mivel az üzleti igények növekedésének és változásának megléte. Az alábbi ábra egy hibrid identitás-architektúra és a felhasználók számára kinyitott munkaterhelések példáját mutatja be. Ez csak egy példa az összes olyan új lehetőségre, amely egy szilárd hibrid identitási stratégia használatával feloldható és elérhető. 

A hibrid identitás-architektúra hibrid identitás-architektúrájának részét képező összetevők ![](./media/plan-hybrid-identity-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Üzleti igények meghatározása
Minden vállalat különböző követelményekkel fog rendelkezni, még akkor is, ha ezek a vállalatok ugyanahhoz az iparághoz tartoznak, a valós üzleti követelmények eltérőek lehetnek. Továbbra is kihasználhatja az iparág ajánlott eljárásait, de végső soron a vállalat üzleti igényei alapján határozhatja meg a hibrid identitás kialakításának követelményeit. 

Győződjön meg arról, hogy az alábbi kérdésekre válaszol az üzleti igények azonosításához:

* A vállalata az informatikai működési költségeket szeretné kivágni?
* Vállalatának szeretné biztosítani a Felhőbeli eszközök (SaaS-alkalmazások, infrastruktúra) védelmét?
* Vállalatának szeretné modernizálni?
  * A felhasználók számára nagyobb a mobil, és megköveteli, hogy kivételeket hozzon létre a DMZ-ben, hogy a különböző típusú forgalom hozzáférjen a különböző erőforrásokhoz?
  * A vállalata rendelkezik olyan örökölt alkalmazásokkal, amelyeket közzé kell tenni a modern felhasználók számára, de nem könnyű újraírni?
  * A vállalatának el kell végeznie ezeket a feladatokat, és az irányítás alatt kell lennie egy időben?
* A vállalata szeretné a felhasználók identitásának védelmét és a kockázatok csökkentését azáltal, hogy olyan új eszközöket biztosít, amelyek a Microsoft Azure biztonsági szakértelmét használják a helyszínen?
* A vállalata megpróbálta megszabadulni a felkapott "külső" fiókokkal a helyszínen, és áthelyezi őket a felhőbe, ahol már nem áll fenn az alvó fenyegetés a helyszíni környezetben?

## <a name="analyze-on-premises-identity-infrastructure"></a>Helyszíni identitás-infrastruktúra elemzése
Most, hogy ötlete van a vállalati üzleti igényekkel kapcsolatban, értékelnie kell a helyszíni identitás-infrastruktúrát. Ez az értékelés fontos azon technikai követelmények meghatározásához, amelyekkel integrálható a jelenlegi személyazonossági megoldás a felhőalapú Identity Management rendszerbe. Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* Milyen hitelesítési és engedélyezési megoldást használ a vállalat a helyszínen? 
* A vállalata jelenleg rendelkezik helyszíni szinkronizációs szolgáltatásokkal?
* A vállalata harmadik féltől származó identitásszolgáltató-szolgáltatót használ?

Emellett tisztában kell lennie azzal, hogy a vállalata milyen felhőalapú szolgáltatásokat tartalmaz. Nagyon fontos, hogy elvégezze az SaaS-, IaaS-vagy Pásti-modellekkel való aktuális integráció megismerését. Ebben az értékelésben a következő kérdésekre kell válaszolnia:

* Rendelkezik a vállalata egy felhőalapú szolgáltatóval való integrációval?
* Ha igen, milyen szolgáltatásokat használnak?
* Ez az integráció jelenleg éles környezetben van, vagy pilóta?

> [!NOTE]
> Cloud Discovery elemzi a forgalmi naplókat a Microsoft Cloud App Security Cloud app 16 000 catalogján, amely több mint 70 kockázati tényező alapján rangsorolva és pontozással van kialakítva, így folyamatos láthatóságot biztosít a Felhőbeli használatban, az árnyékban és a szervezete által jelentett kockázati árnyékban. Első lépésként tekintse [meg a Cloud Discovery beállítása](/cloud-app-security/set-up-cloud-discovery)című témakört.
> 
> 

## <a name="evaluate-identity-integration-requirements"></a>Identitás-integrációs követelmények kiértékelése
Ezután ki kell értékelnie az identitás-integráció követelményeit. Ez az értékelés fontos, hogy meghatározza a felhasználók hitelesítésének technikai követelményeit, hogyan fog megjelenni a szervezet jelenléte a felhőben, hogyan teszi lehetővé a szervezet az engedélyezést, és hogy milyen felhasználói élményre lesz szükség. Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* A szervezete összevonást, normál hitelesítést vagy mindkettőt fog használni?
* Az összevonás követelmény?  A következők miatt:
  * Kerberos-alapú egyszeri bejelentkezés
  * A vállalata olyan helyszíni alkalmazásokkal rendelkezik (beépített vagy harmadik féltől származó), amely SAML-vagy hasonló összevonási képességeket használ.
  * MFA intelligens kártyákon keresztül. RSA-SecurID stb.
  * Az alábbi kérdésekkel foglalkozó ügyfél-hozzáférési szabályok:
    1. Letilthatom az Microsoft 365 összes külső hozzáférését az ügyfél IP-címe alapján?
    2. Letilthatom az Microsoft 365 összes külső hozzáférését, az Exchange ActiveSync kivételével?
    3. Letilthatom Microsoft 365 összes külső hozzáférését a böngészőalapú alkalmazások (OWA, Spongya) kivételével.
    4. Letilthatom a Microsoft 365 összes külső hozzáférését a kijelölt AD-csoportok tagjai számára
* Biztonsági/naplózási vonatkozások
* Már meglévő befektetés összevont hitelesítéssel
* Milyen nevet fog használni a szervezetünk a Felhőbeli tartományhoz?
* Rendelkezik a szervezet egyéni tartománnyal?
  1. A tartomány nyilvános és könnyen ellenőrizhető DNS-en keresztül?
  2. Ha nem, akkor van olyan nyilvános tartománya, amely használható egy alternatív UPN-t az AD-ben?
* Konzisztensek a felhasználói azonosítók a Felhőbeli ábrázoláshoz? 
* A szervezet rendelkezik olyan alkalmazásokkal, amelyek a Cloud Services szolgáltatással való integrációt igényelnek?
* A szervezetnek több tartománya van, és mindegyik szabványos vagy összevont hitelesítést használ?

## <a name="evaluate-applications-that-run-in-your-environment"></a>A környezetben futó alkalmazások kiértékelése
Most, hogy ötlete van a helyszíni és a felhőalapú infrastruktúrával kapcsolatban, ki kell értékelnie az ezekben a környezetekben futó alkalmazásokat. Ez az értékelés azért fontos, hogy meghatározza azokat a technikai követelményeket, amelyek integrálják ezeket az alkalmazásokat a felhőalapú Identity Management rendszerbe. Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* Hol fognak élni az alkalmazásaink?
* A felhasználók hozzáférhetnek a helyszíni alkalmazásokhoz?  A felhőben? Vagy mindkettő?
* Tervezik a meglévő alkalmazások számítási feladatait és a felhőbe való áthelyezését?
* Tervezik olyan új alkalmazások fejlesztését, amelyek a Felhőbeli hitelesítést használó, helyszíni vagy Felhőbeli környezetekben lesznek elérhetők?

## <a name="evaluate-user-requirements"></a>Felhasználói követelmények kiértékelése
Meg kell vizsgálnia a felhasználói követelményeket is. Ez az értékelés fontos azoknak a lépéseknek a meghatározásához, amelyeket a felhasználók a felhőbe való áttéréskor szükségesnek tartanak a beültetéshez és a felhasználóknak való segítségnyújtáshoz. Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* A felhasználók hozzáférhetnek a helyszíni alkalmazásokhoz?
* A felhasználók hozzáférhetnek a Felhőbeli alkalmazásokhoz?
* A felhasználók általában hogyan jelentkeznek be a helyszíni környezetbe?
* Hogyan jelentkeznek be a felhasználók a felhőbe?

> [!NOTE]
> Minden válaszról készítsen feljegyzéseket, és ismerje meg a válaszok indokait. Az [incidensekre adott válaszokra vonatkozó követelmények meghatározása](plan-hybrid-identity-design-considerations-incident-response-requirements.md) a rendelkezésre álló lehetőségeket, illetve az egyes lehetőségek előnyeit és hátrányait veszi igénybe.  A kérdések megválaszolása után kiválaszthatja, hogy melyik lehetőség felel meg legjobban az üzleti igényeinek.
> 
> 

## <a name="next-steps"></a>Következő lépések
[Címtár-szinkronizálási követelmények meghatározása](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Lásd még
[Tervezési szempontok áttekintése](plan-hybrid-identity-design-considerations-overview.md)

