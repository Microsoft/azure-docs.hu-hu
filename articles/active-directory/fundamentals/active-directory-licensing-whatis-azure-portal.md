---
title: Mit jelent a Csoportalapú licencelés az Azure Active Directoryban? | Microsoft Docs
description: További információ az Azure Active Directory Csoportalapú licencelést, beleértve annak működését és ajánlott eljárásokat.
services: active-directory
keywords: Az Azure AD licencelése
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.component: fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 3f23b28c1b20155e50fddf17db90cd2a53c04855
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50209819"
---
# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>Mit jelent a Csoportalapú licencelés az Azure Active Directoryban?

A Microsoft fizetős felhőszolgáltatásainak, például az Office 365, az Enterprise Mobility + Security, a Dynamics 365 és más hasonló termékek használatához licencre van szükség. A licenceket olyan felhasználókhoz kell rendelni, akiknek hozzáférésre van szükségük ezekhez a szolgáltatásokhoz. A licencek kezeléséhez a felügyeleti portálok (Office vagy Azure) valamelyikét vagy PowerShell-parancsmagokat használhatnak a rendszergazdák. Az Azure Active Directory (Azure AD) a Microsoft felhőszolgáltatásainak identitáskezelését támogató, alapul szolgál infrastruktúra. Az Azure AD tárolja a felhasználók licenc-hozzárendelési állapotával kapcsolatos információkat.

A licencek hozzárendelése eddig csak az egyes felhasználók szintjén volt lehetséges, ami megnehezíthette a kezelést nagy számú felhasználó esetén. A rendszergazdáknak például gyakran kellett összetett PowerShell-szkriptet írniuk a felhasználói licencek hozzáadásához vagy eltávolításához a szervezeti változásoknak megfelelően, például amikor felhasználók csatlakoztak a szervezethez vagy egy részleghez, illetve elhagyták azokat. A szkript egymástól független hívásokat kezdeményezett a felhőszolgáltatás felé.

Ezen kihívások kezelésére biztosítja az Azure AD a csoportalapú licencelést. A terméklicenceket hozzárendelheti egy csoporthoz. Ezután az Azure AD gondoskodik arról, hogy megtörténjen a licencek hozzárendelése a csoport mindegyik tagjához. A csoporthoz csatlakozó új tagokhoz is hozzárendeli a megfelelő licenceket. Amikor a felhasználók elhagyják a csoportot, eltávolítja a licenceket. Így már nincs szükség arra, hogy a PowerShell segítségével megvalósított automatikus licenckezeléssel, a felhasználók szintjén kelljen kezelni a szervezet és a részlegek szerkezetében történt változásokat.


## <a name="features"></a>Szolgáltatások

A csoportalapú licencelés fő funkciói az alábbiak:

- A licencek bármilyen biztonsági csoporthoz hozzárendelhetők az Azure AD-ben. Biztonsági csoportok is szinkronizálható a helyszíni, az Azure AD Connect használatával. Biztonsági csoportokat közvetlenül az Azure AD-ben is létrehozhat (csak felhőalapú csoportok), vagy pedig automatikusan, az Azure AD dinamikus csoportok kezelésére szolgáló funkciójával.

- Egy terméklicencnek csoporthoz való hozzárendelésekor a rendszergazda letilthat szolgáltatáscsomagokat a termékben. Ez általában akkor szükséges, amikor a szervezet még nem áll készen a termékben található valamelyik szolgáltatás használatának a megkezdéséhez. A rendszergazda hozzárendelheti például az Office 365-öt egy részleghez, de átmenetileg letilthatja a Yammer szolgáltatást.

- A felhasználószintű licencelést igénylő Microsoft-felhőszolgáltatások mindegyike támogatott. Ez magában foglalja az összes Office 365-terméket, az Enterprise Mobility + Security és a Dynamics 365 szolgáltatást.

- A csoportalapú licencelés jelenleg csak az [Azure Portalon](https://portal.azure.com) érhető el. Ha a felhasználók és csoportok kezelésének elsődleges eszköze egy másik felügyeleti portál, például az Office 365 portál, folytathatja annak használatát. A licencek csoportszintű kezeléséhez azonban az Azure Portalt kell használnia.

- Az Azure AD automatikusan kezeli a csoporttagság változásaiból eredő licencmódosításokat. A licencmódosítások általában a tagság változása után pár percen belül érvénybe lépnek.

- A felhasználók egyszerre több, eltérő licencszabályzattal rendelkező csoport tagjai is lehetnek. A felhasználók emellett rendelkezhetnek a csoportokon kívül, közvetlenül hozzárendelt licencekkel is. A megjelenő felhasználóállapot a hozzárendelt termék- és szolgáltatáslicencek mindegyikének összességét tükrözi. A felhasználó ugyanolyan licenc van hozzárendelve, több forrásból származó, a licenc csak egyszer fognak használni.

- Bizonyos esetekben nem rendelhető licenc a felhasználókhoz. Lehetséges például, hogy nincs elegendő rendelkezésre álló licenc a bérlőben, vagy ütköző szolgáltatások lettek egy időben hozzárendelve. A rendszergazdák hozzáférhetnek azokkal a felhasználókkal kapcsolatos információkhoz, akik esetén az Azure AD nem tudta teljes mértékben feldolgozni a csoportlicenceket. Ezután korrekciós műveleteket hajthatnak végre az információk alapján.

- A fizetős vagy próba előfizetést az Azure AD alapszintű vagy egy fizetős vagy próba Office 365 nagyvállalati E3 csomag, az Office 365 a3-as vagy újabb verziója esetén a bérlő felügyeleti csoport alapú licenc használatához szükséges. Ez a funkció a csoporthoz hozzárendelt licencet mindegyik egyedi felhasználói licencre van szükség. Licencek hozzárendelése a felhasználókhoz a számukra licenc hozzárendelt csoportok tagjai nem szükséges, de minimálisan hány licencet kell rendelkeznie ahhoz, hogy biztosítsák a minden ilyen felhasználót a bérlőben. Például ha 1000 egyedi felhasználók összesen elérhető összes a bérlőhöz hozzárendelt licencekkel rendelkeznek, kell legalább 1000 licencek, a licenc követelmény kielégítése érdekében.

## <a name="your-feedback-is-welcome"></a>Szívesen vesszük visszajelzését!

Ha visszajelzést vagy funkciókérést küldene, ossza meg velünk azokat [az Azure AD rendszergazdai fórumán](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=162510).

## <a name="next-steps"></a>További lépések

A csoportalapú licencelés segítségével folytatott licenckezelés egyéb forgatókönyveivel kapcsolatos további tudnivalókért tekintse át az alábbi témaköröket:

* [Licencek hozzárendelése egy csoporthoz az Azure Active Directoryban](../users-groups-roles/licensing-groups-assign.md)
* [A csoportok licencproblémáinak azonosítása és megoldása az Azure Active Directoryban](../users-groups-roles/licensing-groups-resolve-problems.md)
* [Egyéni, licenccel rendelkező felhasználók migrálása csoportalapú licencelésre az Azure Active Directoryban](../users-groups-roles/licensing-groups-migrate-users.md)
* [Felhasználók az Azure Active Directoryban Csoportalapú licencelést használ terméklicencek közötti migrálása](../users-groups-roles/licensing-groups-change-licenses.md)
* [Az Azure Active Directory csoportalapú licencelésének további forgatókönyvei](../users-groups-roles/licensing-group-advanced.md)
* [PowerShell forgatókönyvek Csoportalapú licenceléshez az Azure Active Directoryban](../users-groups-roles/licensing-ps-examples.md)
