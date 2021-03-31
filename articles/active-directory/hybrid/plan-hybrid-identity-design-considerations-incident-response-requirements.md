---
title: Hybrid Identity design – incidensekre adott válaszok – Azure | Microsoft Docs
description: A hibrid identitási megoldás monitorozási és jelentéskészítési képességeinek meghatározása, amelyeket az informatikai részleg felhasználhat a lehetséges fenyegetések azonosítására és enyhítésére
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: a3d2a459-599b-4b67-8e51-7369ee25082d
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c68be4e46693621f77cb25dd8f34f8e0b7d75dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89278411"
---
# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>Az incidensekre vonatkozó válaszokra vonatkozó követelmények meghatározása a hibrid identitás megoldásához
A nagy vagy közepes méretű szervezetek valószínűleg [biztonsági incidensre reagálnak](/previous-versions/tn-archive/cc700825(v=technet.10)) , hogy az incidensek szintjének megfelelően tegyenek lépéseket. Az Identity Management rendszer fontos összetevő az incidensek válaszának folyamatában, mivel felhasználható annak a meghatározására, hogy ki hajtott végre egy adott műveletet a célra. A hibrid identitási megoldásnak képesnek kell lennie olyan figyelési és jelentéskészítési funkciók biztosítására, amelyekkel az informatikai részleg felhasználhatja a potenciális fenyegetések azonosítására és enyhítésére szolgáló műveleteket. Egy tipikus incidens-válasz tervében a következő fázisok jelennek meg a csomag részeként:

1. Kezdeti Értékelés.
2. Incidensek kommunikációja.
3. A károsodások szabályozása és a kockázatok csökkentése.
4. Annak azonosítása, hogy mi volt a biztonság és a súlyosság.
5. Bizonyítékok megőrzése.
6. Értesítés a megfelelő feleknek.
7. A rendszerek helyreállítása.
8. Dokumentáció.
9. Kár és költséghatékonyság.
10. A folyamat és a terv felülvizsgálata.

A biztonság és a súlyosság fázisának azonosítása során meg kell határozni a feltört rendszereket, az elért fájlokat, és meg kell határozni a fájlok érzékenységét. A hibrid azonosító rendszernek képesnek kell lennie a követelmények teljesítésére, hogy segítséget nyújtson a módosításokat használó felhasználó azonosításában. 

## <a name="monitoring-and-reporting"></a>Figyelés és jelentéskészítés
Az identitásrendszer többször is segíthet a kezdeti értékelési fázisban, főként akkor, ha a rendszer a naplózási és jelentéskészítési képességekkel rendelkezik. A kezdeti értékelés során a rendszergazdának képesnek kell lennie egy gyanús tevékenység azonosítására, vagy a rendszernek képesnek kell lennie arra, hogy automatikusan egy előre konfigurált feladat alapján aktiválja. Számos tevékenység jelentheti a lehetséges támadásokat, azonban más esetekben a helytelenül konfigurált rendszerek számos téves riasztást okozhatnak a behatolás-felismerő rendszerekben. 

Az identitáskezelési rendszeren a rendszergazdáknak a gyanús tevékenységek azonosítására és jelentésére kell segíteniük. Ezek a műszaki követelmények általában az összes rendszer figyelésével és olyan jelentéskészítési képességgel teljesíthetők, amely képes kiemelni a potenciális fenyegetéseket. Az alábbi kérdések segítségével megtervezheti a hibrid identitási megoldást, miközben figyelembe veszi az incidensekre adott válaszokra vonatkozó követelményeket:

* A vállalata rendelkezik a biztonsági incidensekre adott válaszokkal?
  * Ha igen, a folyamat részeként használt aktuális Identity Management rendszer?
* A vállalatának azonosítania kell a gyanús bejelentkezési kísérleteket a felhasználók között különböző eszközökön?
* A vállalatának fel kell deríteni a potenciális feltört felhasználó hitelesítő adatait?
* A vállalatának szüksége van a felhasználó hozzáférésének és működésének naplózására?
* A vállalatának tudnia kell, hogy egy felhasználó visszaállítja-e a jelszavát?

## <a name="policy-enforcement"></a>Szabályzatbetartatás
A károsodások szabályozása és a kockázatok csökkentése – fázisban fontos, hogy gyorsan csökkentse a támadás tényleges és lehetséges hatásait. Az ezen a ponton elvégezhető művelet elvégezheti a különbséget egy kisebb és egy fő között. A pontos válasz a szervezettől és az Ön által szembenéző támadás természetétől függ. Ha a kezdeti értékelés arra a következtetésre jutott, hogy egy fiókot feltörtek, ki kell kényszeríteni a fiók blokkolására vonatkozó szabályzatot. Ez csak egy példa az Identitáskezelés rendszer kihasználása esetén. Az alábbi kérdések segítségével megtervezheti a hibrid identitási megoldást, miközben figyelembe veszi, hogyan kényszeríti ki a szabályzatokat a folyamatban lévő incidensekre való reagálásra:

* Rendelkezik a vállalata szabályzatokkal, hogy letiltsa a felhasználóknak a hálózat elérését, ha szükséges?
  * Ha igen, a jelenlegi megoldás integrálva van a hibrid Identitáskezelés rendszerével, amelyet el fog fogadni?
* A vállalatának kötelezővé kell tennie a feltételes hozzáférést a karanténba helyezett felhasználók számára? 

> [!NOTE]
> Minden válaszról készítsen feljegyzéseket, és ismerje meg a válaszok indokait. Az [adatvédelmi stratégia meghatározása](plan-hybrid-identity-design-considerations-data-protection-strategy.md) a rendelkezésre álló lehetőségeket, az egyes lehetőségek előnyeit és hátrányait veszi át.  A kérdések megválaszolása után kiválaszthatja, hogy melyik lehetőség felel meg legjobban az üzleti igényeinek.
> 
> 

## <a name="next-steps"></a>Következő lépések
[Adatvédelmi stratégia definiálása](plan-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Lásd még:
[Tervezési szempontok áttekintése](plan-hybrid-identity-design-considerations-overview.md)