---
title: Mi az a csoport alapú licencelés – Azure Active Directory | Microsoft Docs
description: Ismerkedjen meg Azure Active Directory csoportos licenceléssel, beleértve a működését és az ajánlott eljárásokat.
services: active-directory
keywords: Az Azure AD licencelése
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0490334c759da6ef7ba7ff2535f5f561cdb7a9bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92369811"
---
# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>Mi a Azure Active Directory csoportos licencelése?

A Microsoft által fizetett felhőalapú szolgáltatások, például a Microsoft 365, a Enterprise Mobility + Security, a Dynamics 365 és más hasonló termékek licenceket igényelnek. A licenceket olyan felhasználókhoz kell rendelni, akiknek hozzáférésre van szükségük ezekhez a szolgáltatásokhoz. A licencek kezeléséhez a felügyeleti portálok (Office vagy Azure) valamelyikét vagy PowerShell-parancsmagokat használhatnak a rendszergazdák. Az Azure Active Directory (Azure AD) a Microsoft felhőszolgáltatásainak identitáskezelését támogató, alapul szolgál infrastruktúra. Az Azure AD tárolja a felhasználók licenc-hozzárendelési állapotával kapcsolatos információkat.

A licencek hozzárendelése eddig csak az egyes felhasználók szintjén volt lehetséges, ami megnehezíthette a kezelést nagy számú felhasználó esetén. A rendszergazdáknak például gyakran kellett összetett PowerShell-szkriptet írniuk a felhasználói licencek hozzáadásához vagy eltávolításához a szervezeti változásoknak megfelelően, például amikor felhasználók csatlakoztak a szervezethez vagy egy részleghez, illetve elhagyták azokat. A szkript egymástól független hívásokat kezdeményezett a felhőszolgáltatás felé.

Ezen kihívások kezelésére biztosítja az Azure AD a csoportalapú licencelést. A terméklicenceket hozzárendelheti egy csoporthoz. Ezután az Azure AD gondoskodik arról, hogy megtörténjen a licencek hozzárendelése a csoport mindegyik tagjához. A csoporthoz csatlakozó új tagokhoz is hozzárendeli a megfelelő licenceket. Amikor a felhasználók elhagyják a csoportot, eltávolítja a licenceket. Ez a licencelési felügyelet szükségtelenné teszi, hogy a PowerShell használatával automatizálja a licencek kezelését, hogy a szervezet és a részleg struktúrájának változásait felhasználónkénti alapon tükrözze.

## <a name="licensing-requirements"></a>Licenckövetelmények
A csoport alapú licencelés használatához a következő licencek egyike szükséges:

- Fizetős vagy próbaverziós előfizetés prémium szintű Azure AD P1 és újabb rendszerekhez

- Fizetős vagy próbaverziós Office 365 Enterprise E3 vagy Office 365 a3 vagy Office 365 GCC G3 vagy Office 365 E3 for GCCH vagy Office 365 E3 – DOD és újabb verziók

### <a name="required-number-of-licenses"></a>Szükséges licencek száma
A licenccel rendelkező csoportok esetében az egyes egyedi tagokhoz is licenccel kell rendelkeznie. Noha nem kell hozzárendelni az a csoport minden tagját, legalább annyi licenccel kell rendelkeznie, hogy tartalmazza az összes tagot. Ha például olyan egyedi tagokkal 1 000 rendelkezik, akik a bérlőhöz tartozó licencelt csoportok részét képezik, legalább 1 000 licenccel kell rendelkeznie a licencszerződés teljesítéséhez.

## <a name="features"></a>Funkciók

A csoportalapú licencelés fő funkciói az alábbiak:

- A licencek bármilyen biztonsági csoporthoz hozzárendelhetők az Azure AD-ben. A biztonsági csoportokat Azure AD Connect használatával lehet szinkronizálni a helyszínen. Biztonsági csoportokat közvetlenül az Azure AD-ben is létrehozhat (csak felhőalapú csoportok), vagy pedig automatikusan, az Azure AD dinamikus csoportok kezelésére szolgáló funkciójával.

- Egy terméklicencnek csoporthoz való hozzárendelésekor a rendszergazda letilthat szolgáltatáscsomagokat a termékben. Ezt a hozzárendelést általában akkor kell elvégezni, ha a szervezet még nem áll készen a termékben található szolgáltatás használatának megkezdésére. Előfordulhat például, hogy a rendszergazda Microsoft 365 rendel hozzá egy részleghez, de átmenetileg letiltja a Yammer szolgáltatást.

- A felhasználószintű licencelést igénylő Microsoft-felhőszolgáltatások mindegyike támogatott. Ez a támogatás magában foglalja az összes Microsoft 365 terméket, Enterprise Mobility + Security és a Dynamics 365-et.

- A csoport alapú licencelés jelenleg csak a [Azure Portalon](https://portal.azure.com)érhető el. Ha elsődlegesen más felügyeleti portálokat használ a felhasználók és a csoportok felügyeletéhez, például a [Microsoft 365 felügyeleti központot](https://admin.microsoft.com), továbbra is megteheti. A licencek csoportszintű kezeléséhez azonban az Azure Portalt kell használnia.

- Az Azure AD automatikusan kezeli a csoporttagság változásaiból eredő licencmódosításokat. A licencmódosítások általában a tagság változása után pár percen belül érvénybe lépnek.

- A felhasználók egyszerre több, eltérő licencszabályzattal rendelkező csoport tagjai is lehetnek. A felhasználók emellett rendelkezhetnek a csoportokon kívül, közvetlenül hozzárendelt licencekkel is. A megjelenő felhasználóállapot a hozzárendelt termék- és szolgáltatáslicencek mindegyikének összességét tükrözi. Ha egy felhasználóhoz több forrásból származó licenc van hozzárendelve, a licenc csak egyszer lesz felhasználva.

- Bizonyos esetekben nem rendelhető licenc a felhasználókhoz. Lehetséges például, hogy nincs elegendő rendelkezésre álló licenc a bérlőben, vagy ütköző szolgáltatások lettek egy időben hozzárendelve. A rendszergazdák hozzáférhetnek azokkal a felhasználókkal kapcsolatos információkhoz, akik esetén az Azure AD nem tudta teljes mértékben feldolgozni a csoportlicenceket. Ezután korrekciós műveleteket hajthatnak végre az információk alapján.

## <a name="your-feedback-is-welcome"></a>Szívesen vesszük visszajelzését!

Ha visszajelzést vagy szolgáltatási kérést használ, ossza meg velünk az [Azure ad felügyeleti Fórumával](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=162510).

## <a name="next-steps"></a>Következő lépések

A csoportalapú licencelés segítségével folytatott licenckezelés egyéb forgatókönyveivel kapcsolatos további tudnivalókért tekintse át az alábbi témaköröket:

* [Licencek hozzárendelése egy csoporthoz az Azure Active Directoryban](../enterprise-users/licensing-groups-assign.md)
* [A csoportok licencproblémáinak azonosítása és megoldása az Azure Active Directoryban](../enterprise-users/licensing-groups-resolve-problems.md)
* [Egyéni, licenccel rendelkező felhasználók migrálása csoportalapú licencelésre az Azure Active Directoryban](../enterprise-users/licensing-groups-migrate-users.md)
* [Felhasználók áttelepítése licencek között a csoport alapú licencelés használatával Azure Active Directory](../enterprise-users/licensing-groups-change-licenses.md)
* [Az Azure Active Directory csoportalapú licencelésének további forgatókönyvei](../enterprise-users/licensing-group-advanced.md)
* [PowerShell-példák csoportházirend-alapú licenceléshez Azure Active Directory](../enterprise-users/licensing-ps-examples.md)
