---
title: Hibrid identitások tervezése – adatvédelmi követelmények az Azure-ban | Microsoft Docs
description: A hibrid személyazonossági megoldás tervezésekor azonosítsa a vállalata adatvédelmi követelményeit, és hogy mely lehetőségek érhetők el legjobban a követelmények teljesítése érdekében.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 40dc4baa-fe82-4ab6-a3e4-f36fa9dcd0df
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
ms.openlocfilehash: 056d0caf2bfc6e99d5d659a2561bc41844feb79a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "64918778"
---
# <a name="plan-for-enhancing-data-security-through-a-strong-identity-solution"></a>Tervezze meg az adatok biztonságának növelését erős identitási megoldáson keresztül
Az adatok védelmének első lépése annak azonosítása, hogy ki férhet hozzá az adatokhoz. Emellett olyan identitási megoldásra van szükség, amely integrálható a rendszerrel a hitelesítési és engedélyezési képességek biztosítása érdekében. A hitelesítés és az engedélyezés gyakran zavaros egymással, és a szerepköreik félreértik. A valóságban ezek különböznek, ahogy az alábbi ábrán is látható:

![mobil eszközök életciklusa](./media/plan-hybrid-identity-design-considerations/mobile-devicemgt-lifecycle.png)

**A mobileszköz-kezelés életciklusának szakaszai**

A hibrid identitás megoldás megtervezése során ismernie kell a vállalat adatvédelmi követelményeit, és hogy mely lehetőségek érhetők el legjobban a követelmények teljesítése érdekében.

> [!NOTE]
> Miután befejezte az adatbiztonság tervezését, tekintse át a [többtényezős hitelesítési követelmények megállapítása](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md) című témakört, amely biztosítja, hogy a többtényezős hitelesítési követelményekkel kapcsolatos beállításokat a jelen szakaszban meghozott döntések nem érintik.
> 
> 

## <a name="determine-data-protection-requirements"></a>Adatvédelmi követelmények meghatározása
A mobilitás korában a legtöbb vállalatnak közös célja van: lehetővé teszi a felhasználók számára, hogy a hatékonyság növelése érdekében a mobil eszközeiket, helyszíni vagy távolról bárhonnan. Az ilyen követelményekkel rendelkező vállalatok is aggasztják azon fenyegetések számát, amelyeket mérsékelni kell a vállalat adatainak biztonsága és a felhasználó adatainak védelme érdekében. Az egyes vállalatok eltérő követelményekkel rendelkezhetnek ebben a tekintetben; különböző megfelelőségi szabályok, amelyek attól függően változnak, hogy a vállalat milyen iparágban működik, különböző kialakítási döntéseket fog hozni. 

Vannak azonban olyan biztonsági szempontok, amelyeket az iparágtól függetlenül meg kell vizsgálni és ellenőrizni kell.

## <a name="data-protection-paths"></a>Adatvédelmi útvonalak
![adatvédelmi útvonalak](./media/plan-hybrid-identity-design-considerations/data-protection-paths.png)

**Adatvédelmi útvonalak**

A fenti ábrán az Identity összetevő lesz az első, amelyet az adatok elérése előtt ellenőrizni kell. Ezek az adategységek azonban az elérésük időpontjában eltérő állapotba helyezhetők. A diagramon minden egyes szám egy olyan elérési utat jelöl, amelyben az adatmennyiség egy adott időpontban elhelyezhető. A következő számok magyarázata alább látható:

1. Adatvédelem az eszköz szintjén.
2. Adatvédelem az átvitel során.
3. Adatvédelem a helyszíni Rest-környezetekben.
4. Adatvédelem a felhőben való nyugalmi állapotban.

Szükség van arra, hogy a hibrid identitási megoldás képes legyen a helyszíni és a Felhőbeli identitás-felügyeleti erőforrások kihasználására, hogy a felhasználó azonosítható legyen az adatokhoz való hozzáférés biztosítása előtt. A hibrid identitás megoldás megtervezése során győződjön meg arról, hogy a szervezet követelményeinek megfelelően a következő kérdések válaszolnak:

## <a name="data-protection-at-rest"></a>Adatvédelem nyugalmi állapotban
Függetlenül attól, hogy az adatok hol vannak a nyugalmi állapotban (eszköz, felhő vagy helyszíni), fontos, hogy elvégezze az értékelést, hogy megértse a szervezet igényeit ebben a tekintetben. Ezen a területen győződjön meg arról, hogy a következő kérdéseket kéri:

* Szüksége van a vállalatának az inaktív adatok védelme érdekében?
  * Ha igen, a hibrid identitási megoldás képes integrálni a jelenlegi helyszíni infrastruktúrával?
  * Ha igen, a hibrid identitási megoldás képes integrálni a felhőben található számítási feladatokkal?
* Képes a Felhőbeli Identitáskezelés a felhasználó hitelesítő adatainak és a felhőben tárolt egyéb adatoknak a védelme érdekében?

## <a name="data-protection-in-transit"></a>Adatvédelem az átvitel során
Az eszköz és az adatközpont, illetve az eszköz és a felhő közötti adatátvitelt védeni kell. Az átvitel során azonban nem feltétlenül jelenti azt a kommunikációs folyamatot, amely a felhőalapú szolgáltatáson kívüli összetevővel is rendelkezik; belsőleg, például két virtuális hálózat között mozog. Ezen a területen győződjön meg arról, hogy a következő kérdéseket kéri:

* Szüksége van a vállalatának az adatforgalom védelme érdekében?
  * Ha igen, a hibrid identitási megoldás képes a biztonságos vezérlők, például az SSL/TLS integrálására?
* A Felhőbeli Identitáskezelés megtartja a (adatközpontok közötti és az adatközpontokon belüli) és a címtáron belüli adatforgalmat?

## <a name="compliance"></a>Megfelelőség
A szabályozások, törvények és szabályozások megfelelőségi követelményei a vállalat által birtokolt iparágtól függően változhatnak. A nagy mértékben szabályozott iparágakban lévő vállalatoknak a megfelelőségi problémákkal kapcsolatos, identitás-felügyeleti problémákra van szükségük. A szabályozások, mint például a Sarbanes-Oxley (SOX), az egészségbiztosítási hordozhatóság és az elszámoltathatósági törvény (HIPAA), a gramm-bemosódás-Bliley törvény (GLBA) és a Payment Card Industry adatbiztonsági szabványa (PCI DSS) szigorú az identitás és a hozzáférés tekintetében. A vállalat által elfogadott hibrid identitási megoldásnak rendelkeznie kell olyan alapvető képességekkel, amelyek eleget tesznek egy vagy több ilyen előírás követelményeinek. Ezen a területen győződjön meg arról, hogy a következő kérdéseket kéri:

* A hibrid identitási megoldás megfelel a vállalatra vonatkozó szabályozási követelményeknek?
* A Hybrid Identity megoldás beépített 
* olyan képességekkel, amelyek lehetővé teszik, hogy vállalata megfeleljen a megfelelőségi követelményeknek? 

> [!NOTE]
> Minden válaszról készítsen feljegyzéseket, és ismerje meg a válaszok indokait. Az [adatvédelmi stratégia meghatározása](plan-hybrid-identity-design-considerations-data-protection-strategy.md) a rendelkezésre álló lehetőségeket, az egyes lehetőségek előnyeit és hátrányait veszi át.  A kérdések megválaszolása után kiválaszthatja, hogy melyik lehetőség felel meg legjobban az üzleti igényeinek.
> 
> 

## <a name="next-steps"></a>Következő lépések
 [A tartalomkezeléshez kapcsolódó követelmények meghatározása](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)

## <a name="see-also"></a>Lásd még:
[Tervezési szempontok áttekintése](plan-hybrid-identity-design-considerations-overview.md)

