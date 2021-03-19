---
title: A Hybrid Identity design hozzáférés-vezérlési követelményei az Azure-ban | Microsoft Docs
description: Az identitás pilléreit fedi le, valamint a hibrid környezetben lévő felhasználók erőforrásaihoz való hozzáférési követelmények azonosítását.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: e3b3b984-0d15-4654-93be-a396324b9f5e
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84b786a1701892823554a83fa2015ac88d6eff4d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "60295143"
---
# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>A hibrid identitási megoldás hozzáférés-vezérlési követelményeinek meghatározása
Ha egy szervezet megtervezi a hibrid személyazonossági megoldást, a lehetőség használatával áttekintheti a felhasználók számára elérhetővé tenni kívánt erőforrások hozzáférési követelményeit. Az adatokhoz való hozzáférés mind a négy pillér identitását keresztezi:

* Felügyelet
* Hitelesítés
* Engedélyezés
* Naplózás

A következő szakaszokban a hitelesítés és az engedélyezés a további részletek, a felügyelet és a naplózás részét képezi a hibrid identitás életciklusának. A képességekkel kapcsolatos további információkért olvassa el a [hibrid identitás-kezelési feladatok meghatározása](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) című témakört.

> [!NOTE]
> Tekintse át [az Identity-Identity Management négy pillérét a hibrid korában](https://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) , ahol további információkat olvashat az egyes pillérekről.
> 
> 

## <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés
A hitelesítéshez és az engedélyezéshez különböző forgatókönyvek tartoznak, ezek a forgatókönyvek konkrét követelményekkel rendelkeznek, amelyeket a vállalat által bevezetni kívánt hibrid identitási megoldásnak teljesítenie kell. Az üzleti (B2B) kommunikációt érintő forgatókönyvek további kihívást jelenthetnek a rendszergazdák számára, mivel biztosítaniuk kell, hogy a szervezet által használt hitelesítési és engedélyezési módszer képes legyen kommunikálni az üzleti partnerekkel. A hitelesítési és engedélyezési követelmények tervezési folyamata során győződjön meg arról, hogy a következő kérdéseket kell megválaszolni:

* A szervezete hitelesíti és engedélyezi a csak az Identity Management rendszeren található felhasználókat?
  * Vannak a B2B-forgatókönyvekre vonatkozó csomagok?
  * Ha igen, már tudja, hogy mely protokollokat (SAML, OAuth, Kerberos vagy tanúsítványok) használja a rendszer a két vállalat összekapcsolásához?
* A hibrid identitási megoldás fogja támogatni ezeket a protokollokat?

Egy másik fontos szempont, hogy hol található a felhasználók és a partnerek által használt hitelesítési tárház és a használni kívánt felügyeleti modell. Vegye figyelembe a következő két alapvető lehetőséget:

* Központosított: ebben a modellben a felhasználó hitelesítő adatai, a házirendek és a felügyelet központosítható a helyszínen vagy a felhőben is.
* Hibrid: ebben a modellben a felhasználó hitelesítő adatai, a házirendek és az adminisztráció a helyszínen lesz központosítva, és a felhőben replikálódik.

A szervezete által elfogadott modell az üzleti követelményektől függően változhat, és az alábbi kérdésekre kell válaszolnia, hogy azonosítsa, hol található az Identity Management rendszer, és hogy milyen felügyeleti módot kíván használni:

* A szervezete jelenleg rendelkezik a helyszíni Identitáskezelés kezelésével?
  * Ha igen, tervezi, hogy megtartják?
  * Van olyan előírás vagy megfelelőségi követelmény, amelyet a szervezetnek követnie kell, hogy hol kell lennie az Identity Management rendszernek?
* A szervezete használja az egyszeri bejelentkezést a helyszínen vagy a felhőben található alkalmazásokhoz?
  * Ha igen, a hibrid identitás-modell bevezetése hatással van erre a folyamatra?

## <a name="access-control"></a>Hozzáférés-vezérlés
A hitelesítés és az engedélyezés olyan alapszintű elemek, amelyek lehetővé teszik a vállalati adatokat a felhasználó ellenőrzésén keresztül, továbbá azt is fontos, hogy szabályozzák a felhasználók hozzáférésének szintjét, és a hozzáférési rendszergazdák szintjének az általuk kezelt erőforrásokon keresztül. A hibrid identitási megoldásnak képesnek kell lennie részletes hozzáférés biztosítására az erőforrásokhoz, a delegáláshoz és a szerepkör-alapú hozzáférés-vezérléshez. Győződjön meg arról, hogy a hozzáférés-vezérléssel kapcsolatban a következő kérdés van megválaszolva:

* A vállalata több, emelt szintű jogosultsággal rendelkező felhasználóval rendelkezik az identitási rendszer kezeléséhez?
  * Ha igen, minden felhasználónak ugyanazt a hozzáférési szintet kell használnia?
* Szeretné, hogy a vállalata delegálja a hozzáférést a felhasználóknak az egyes erőforrások kezeléséhez?
  * Ha igen, milyen gyakran történik ez?
* A vállalatának integrálnia kell a hozzáférés-vezérlési képességeket a helyszíni és a Felhőbeli erőforrások között?
* A vállalatának bizonyos feltételek szerint korlátoznia kell az erőforrásokhoz való hozzáférést?
* A vállalata rendelkezik olyan alkalmazásokkal, amelyeknek egyéni vezérlési hozzáférésre van szükségük bizonyos erőforrásokhoz?
  * Ha igen, hol találhatók az alkalmazások (a helyszínen vagy a felhőben)?
  * Ha igen, hol találhatók a célként megadott erőforrások (a helyszínen vagy a felhőben)?

> [!NOTE]
> Minden válaszról készítsen feljegyzéseket, és ismerje meg a válaszok indokait. Az [adatvédelmi stratégia meghatározása](plan-hybrid-identity-design-considerations-data-protection-strategy.md) a rendelkezésre álló lehetőségeket, az egyes lehetőségek előnyeit és hátrányait veszi át.  E kérdések megválaszolásával kiválaszthatja, hogy melyik lehetőség felel meg legjobban az üzleti igényeinek.
> 
> 

## <a name="next-steps"></a>Következő lépések
[Az incidensmegoldáshoz kapcsolódó követelmények meghatározása](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Lásd még:
[Tervezési szempontok áttekintése](plan-hybrid-identity-design-considerations-overview.md)

