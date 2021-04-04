---
title: Azure Active Directory Hybrid Identity kialakítási szempontjai – áttekintés | Microsoft Docs
description: A hibrid identitás kialakításával kapcsolatos megfontolások áttekintése és a tartalom térképe
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 100509c4-0b83-4207-90c8-549ba8372cf7
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7f8dd49f3668b8f68753681123a04d21edac46c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997732"
---
# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Azure Active Directory hibrid identitáskezelés – kialakítási szempontok
A vállalati és a felhőalapú szoftveres (SaaS) alkalmazások egyszerűen elsajátítják a céges eszközöket. Ennek eredményeképpen a felhasználói alkalmazások hozzáférésének fenntartása a belső adatközpontokban és a felhőalapú platformokon is kihívást jelent.  

A Microsoft identitáskezelési megoldásai kiterjednek a helyszíni és felhőalapú képességekre is, így egyetlen felhasználói identitás jön létre hitelesítés és engedélyezés céljából az összes erőforráshoz, helytől függetlenül. Ezt a koncepciót hibrid identitásnak nevezzük. A hibrid identitás különböző tervezési és konfigurációs lehetőségeket kínál a Microsoft-megoldások használatával, és bizonyos esetekben nehéz lehet meghatározni, hogy melyik kombináció felel meg legjobban a szervezet igényeinek. 

A hibrid identitás kialakításával kapcsolatos szempontokat ismertető útmutató segítségével megismerheti, hogyan tervezhet olyan hibrid identitási megoldást, amely leginkább megfelel a szervezet üzleti és technológiai igényeinek.  Ez az útmutató részletesen ismerteti azokat a lépéseket és feladatokat, amelyek segítségével megtervezheti a szervezet egyedi követelményeinek megfelelő hibrid identitás-megoldást. A lépések és feladatok során az útmutató bemutatja a szervezetek számára elérhető technológiákat és funkciókat, hogy azok megfeleljenek a funkcionális és a szolgáltatás minőségének (például a rendelkezésre állás, a méretezhetőség, a teljesítmény, a kezelhetőség és a biztonság) szintjére vonatkozó követelményeknek. 

A hibrid identitás kialakításával kapcsolatos megfontolások a következő kérdésekre adnak választ: 

* Milyen kérdésekre van szükségem egy hibrid identitás-specifikus terv megadásához egy olyan technológiához vagy problémás tartományhoz, amely a legjobban megfelel az igényeiknek?
* Milyen tevékenységeket kell befejezni a technológia vagy a probléma tartományához tartozó hibrid identitási megoldás kialakításához? 
* Milyen hibrid identitás-technológiák és konfigurációs lehetőségek érhetők el a követelmények teljesítése érdekében? Milyen kompromisszumok vannak a lehetőségek között, hogy kiválaszthatom a legjobb lehetőséget a saját vállalkozásom számára?

## <a name="who-is-this-guide-intended-for"></a>Kinek szól ez az útmutató?
 Az informatikai vezető, a CITO, a vállalati építészek, valamint a hibrid identitási megoldások közepes vagy nagyméretű szervezeteknek való kialakításához felelős informatikai építészek.

## <a name="how-can-this-guide-help-you"></a>Miben segít ez az útmutató?
Ebből az útmutatóból megtudhatja, hogyan tervezhet olyan hibrid identitási megoldást, amely képes a felhőalapú Identitáskezelés-felügyeleti rendszer integrálására a jelenlegi helyszíni identitás-megoldással. 

Az alábbi ábra egy olyan hibrid identitási megoldást mutat be, amely lehetővé teszi a rendszergazdák számára, hogy a helyi Windows Server-Active Directory megoldását Microsoft Azure Active Directory, hogy a felhasználók a felhőben és a helyszínen található alkalmazások egyetlen Sign-On (SSO) használatát engedélyezzék.

![Példa](media/plan-hybrid-identity-design-considerations/hybridID-example.png)

A fenti ábra egy olyan hibrid identitási megoldásra mutat példát, amely a felhőalapú szolgáltatásokat használja a helyszíni képességekkel való integrációhoz, hogy egyetlen felhasználói élményt nyújtson a végfelhasználói hitelesítési folyamat számára, és megkönnyítse az erőforrások kezelését. Bár ez a példa gyakori forgatókönyv lehet, minden szervezet hibrid identitásának kialakítása valószínűleg eltérő lehet az 1. ábrán látható példától eltérő követelmények miatt. 

Ez az útmutató számos olyan lépést és feladatot tartalmaz, amelyeket követve megtervezheti a szervezet egyedi igényeinek megfelelő hibrid identitás-megoldást. A következő lépések és feladatok során az útmutató ismerteti a megfelelő technológiákat és funkciókat, amelyekkel a szervezet működési és szolgáltatási minőségi szintjére vonatkozó követelményekkel találkozhat.

**Feltételezések**: a Windows Server, a Active Directory Domain Services és a Azure Active Directory némi tapasztalattal rendelkezik. Ez a dokumentum azt feltételezi, hogy Ön azt keresi, hogy ezek a megoldások hogyan tudják kielégíteni saját üzleti igényeit, vagy egy integrált megoldásban.

## <a name="design-considerations-overview"></a>Tervezési szempontok áttekintése
Ez a dokumentum olyan lépéseket és feladatokat tartalmaz, amelyeket követve megtervezheti az igényeinek leginkább megfelelő hibrid identitás-megoldást. Az egyes lépések a megfelelő sorrendben követik egymást. A későbbi lépésekben megjelenő tervezési szempontok miatt előfordulhat, hogy módosítania kell a korábbi lépések során hozott döntéseket, azonban ütköző kialakítási lehetőségek miatt. Minden kísérlet során riasztást kap a lehetséges tervezési ütközésekről a dokumentumban. 

A követelményeknek leginkább megfelelő kialakítást csak akkor kell megismételni, ha a lépéseket a dokumentumban lévő összes szempont beépítéséhez szükséges számú lépésben megismétli. 

| Hibrid identitás fázisa | Témakörök listája |
| --- | --- |
| Identitáshoz kapcsolódó követelmények meghatározása |[Üzleti igények meghatározása](plan-hybrid-identity-design-considerations-business-needs.md)<br> [Címtár-szinkronizálási követelmények meghatározása](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [A multi-Factor Authentication követelményeinek meghatározása](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Hibrid identitás-bevezetési stratégia definiálása](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md) |
| Az adatbiztonság növelésének megtervezése erős identitású megoldáson keresztül |[Adatvédelmi követelmények meghatározása](plan-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [A tartalomkezeléshez kapcsolódó követelmények meghatározása](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [A hozzáférés-vezérlésre vonatkozó követelmények meghatározása](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Az incidensmegoldáshoz kapcsolódó követelmények meghatározása](plan-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Adatvédelmi stratégia definiálása](plan-hybrid-identity-design-considerations-data-protection-strategy.md) |
| A hibrid identitáskezelés életciklusának megtervezése |[Hibrid Identitáskezelés kezelési feladatainak meghatározása](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Szinkronizálás kezelése](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [A hibrid Identitáskezelés bevezetési stratégiájának meghatározása](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |

## <a name="next-steps"></a>Következő lépések
[Identitáshoz kapcsolódó követelmények meghatározása](plan-hybrid-identity-design-considerations-business-needs.md)

