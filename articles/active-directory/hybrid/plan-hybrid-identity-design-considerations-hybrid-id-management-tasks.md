---
title: Hibrid Identity design – felügyeleti feladatok az Azure-ban | Microsoft Docs
description: Az Azure AD ellenőrzi a felhasználók hitelesítése és a feltételes hozzáférés-vezérléssel való hozzáférés engedélyezése előtt a kiválasztott feltételeket.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 65f80aea-0426-4072-83e1-faf5b76df034
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
ms.openlocfilehash: c416bf19acb1736eeed679c16dbd87de1cc98537
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90986522"
---
# <a name="plan-for-hybrid-identity-lifecycle"></a>A hibrid identitás életciklusának tervezése
Az identitás a nagyvállalati mobilitás és az alkalmazás-hozzáférési stratégia egyik alapja. Akár a mobileszközön, akár SaaS-alkalmazásba jelentkezik be, az identitása a legfontosabb, hogy mindenhez hozzáférjen. A legmagasabb szinten a személyazonosság-kezelési megoldás magában foglalja az identitás-Tárházak egységesítését és szinkronizálását, amely magában foglalja az erőforrások kiépítési folyamatának automatizálását és központosítását. Az Identity megoldásnak központilag központosított identitásnak kell lennie a helyszíni és a felhőben, valamint az identitás-összevonás valamilyen formáját is használhatja a központosított hitelesítés fenntartásához, valamint a külső felhasználók és vállalkozások biztonságos megosztásához és a velük való együttműködéshez. Az erőforrások az operációs rendszertől és az alkalmazásoktól az-ban vagy a-ban, a szervezeten belüli felhasználókig terjedhetnek. A szervezeti struktúra úgy módosítható, hogy megfeleljen a kiépítési szabályzatoknak és eljárásoknak.

Az is fontos, hogy a felhasználók számára biztosítson olyan személyazonossági megoldást, amely az önkiszolgáló élmény biztosításával gondoskodik a felhasználóknak. Az Identity megoldás robusztusabb, ha lehetővé teszi az egyszeri bejelentkezést a felhasználók számára az összes szükséges erőforráshoz. A rendszergazdák minden szinten szabványos eljárásokat használhatnak a felhasználói hitelesítő adatok kezeléséhez. Az adminisztráció bizonyos szintjei a kiépítési felügyeleti megoldás szélessége alapján csökkenthetők vagy megvonhatók. Emellett az adminisztrációs képességek biztonságosan, manuálisan vagy automatikusan is elterjeszthetők a különböző szervezetek között. A tartományi rendszergazdák például csak az adott tartományban lévő személyeket és erőforrásokat tudják kiszolgálni. Ez a felhasználó felügyeleti és kiépítési feladatokat végezhet el, de nem rendelkezik jogosultsággal a konfigurációs feladatok végrehajtásához, például munkafolyamatok létrehozásához.

## <a name="determine-hybrid-identity-management-tasks"></a>Hibrid Identitáskezelés kezelési feladatainak meghatározása
A szervezet adminisztratív feladatainak terjesztése javítja az adminisztráció pontosságát és hatékonyságát, és javítja a szervezet munkaterhelésének egyensúlyát. Az alábbiakban a robusztus Identitáskezelés rendszerét meghatározó kimutatások láthatók.

 ![Identitáskezelés – szempontok](./media/plan-hybrid-identity-design-considerations/Identity_management_considerations.png)

A hibrid Identitáskezelés megadásához meg kell ismernie a szervezet olyan lényeges jellemzőit, amelyek hibrid identitást hoznak létre. Fontos megérteni az identitási forrásokhoz használt aktuális adattárakat. A legfontosabb elemek ismeretében az alapvető követelményekkel fog rendelkezni, és azon alapul, hogy részletesebb kérdéseket kell megtennie, amelyek az Ön személyazonossági megoldásának jobb tervezési döntéséhez vezetnek.  

A követelmények meghatározása során győződjön meg arról, hogy legalább az alábbi kérdésekre válaszol

* Üzembe helyezési lehetőségek: 
  
  * Támogatja a hibrid identitási megoldás a fiókokhoz való hozzáférés felügyeletét és a kiépítési rendszerét?
  * Hogyan lesznek felügyelve a felhasználók, csoportok és jelszavak?
  * Az identitás-életciklus kezelése reagál? 
    * Mennyi ideig tart a jelszó-frissítési fiók felfüggesztése?
* Licencek kezelése: 
  
  * Kezeli a hibrid identitás megoldás a licencek kezelését?
    * Ha igen, milyen lehetőségek állnak rendelkezésre?
  * Kezeli a megoldás a csoport alapú licencek kezelését? 
  
    * Ha igen, van lehetőség biztonsági csoport hozzárendelésére? 
    * Ha igen, a Cloud Directory automatikusan hozzárendeli a licenceket a csoport összes tagjához? 
    * Mi történik, ha egy felhasználót később hozzáadnak vagy eltávolítanak a csoportból, a rendszer automatikusan hozzárendeli vagy eltávolítja a licencet a megfelelő módon? 
* Integráció más, harmadik féltől származó identitás-szolgáltatókkal:
  * Integrálható ez a hibrid megoldás a harmadik féltől származó identitás-szolgáltatókkal az egyszeri bejelentkezés megvalósításához?
  * Lehet egyesíteni az összes különböző Identity providert egy egységes identitásrendszer használatával?
  * Ha igen, hogyan és milyen lehetőségek állnak rendelkezésre?

## <a name="synchronization-management"></a>Szinkronizálás kezelése
Az Identity Manager egyik célja, hogy képes legyen az összes identitás-szolgáltatót bevezetni, és szinkronizálni őket. Megtartja az adatok szinkronizálását egy mérvadó főazonosító-szolgáltató alapján. Hibrid identitás esetén a szinkronizált felügyeleti modellel minden felhasználó és eszköz identitását felügyelheti egy helyszíni kiszolgálón, és szinkronizálhatja a fiókokat, és opcionálisan a jelszavakat a felhőbe. A felhasználó ugyanazt a jelszót adja meg a helyszínen, mint a felhőben, és bejelentkezéskor a jelszót az Identity megoldás ellenőrzi. Ez a modell egy címtár-szinkronizáló eszközt használ.

![címtár-szinkronizálás a ](./media/plan-hybrid-identity-design-considerations/Directory_synchronization.png) megfelelő kialakításhoz a hibrid identitás megoldásának szinkronizálása érdekében győződjön meg arról, hogy a következő kérdések válaszolnak:
*    Milyen szinkronizálási megoldások érhetők el a hibrid identitás megoldáshoz?
*    Milyen funkciók érhetők el az egyszeri bejelentkezéshez?
*    Mik a B2B és B2C közötti identitás-összevonás lehetőségei?

## <a name="next-steps"></a>Következő lépések
[A hibrid Identitáskezelés bevezetési stratégiájának meghatározása](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)

## <a name="see-also"></a>Lásd még:
[Tervezési szempontok áttekintése](plan-hybrid-identity-design-considerations-overview.md)

