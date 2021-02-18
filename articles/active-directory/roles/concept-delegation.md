---
title: A rendszergazdai szerepkör delegálásának megismerése – Azure Active Directory | Microsoft Docs
description: Delegálási modellek, példák és szerepkör-biztonság a Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9074fdbf31cdd8516b8d42f7cc13b4bc16f6634a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095734"
---
# <a name="delegate-administration-in-azure-active-directory"></a>Felügyelet delegálása Azure Active Directory

A szervezeti növekedés összetettsége miatt. Az egyik gyakori válasz, hogy csökkentse a hozzáférés-kezelés munkaterhelését Azure Active Directory (AD) rendszergazdai szerepkörökkel. A lehető legkevesebb jogosultságot rendelheti hozzá a felhasználókhoz az alkalmazásokhoz való hozzáféréshez és a feladatok elvégzéséhez. Akkor is, ha nem rendeli hozzá a globális rendszergazdai szerepkört minden alkalmazás-tulajdonoshoz, az alkalmazás-felügyeleti feladatait a meglévő globális rendszergazdákra helyezi. Számos oka lehet annak, hogy egy szervezet több decentralizált felügyelet felé mozdul. Ez a cikk segítséget nyújt a szervezeten belüli delegálás megtervezésében.

<!--What about reporting? Who has which role and how do I audit?-->

## <a name="centralized-versus-delegated-permissions"></a>Központosított és delegált engedélyek

A szervezet növekedésével nehéz lehet nyomon követni, hogy mely felhasználók rendelkeznek bizonyos rendszergazdai szerepkörökkel. Ha egy alkalmazott rendszergazdai jogosultságokkal rendelkezik, akkor a szervezete a biztonsági rések miatt érzékenyebb lehet. Általánosságban elmondható, hogy hány rendszergazda támogat, és hogy az engedélyeik részletessége milyen mértékben függ az üzembe helyezés méretétől és bonyolultságától.

* A kis-és a megvalósíthatósági környezetekben egy vagy több rendszergazda mindent megtesz; nincs delegálás. Ebben az esetben hozzon létre minden rendszergazdát a globális rendszergazdai szerepkörrel.
* Több gépre, alkalmazásra és asztali gépre nagyobb telepítések esetén több delegálásra van szükség. Több rendszergazda is rendelkezhet konkrétabb működési feladatokkal (szerepkörökkel). Előfordulhat például, hogy bizonyos jogosultsági szintű identitás-rendszergazdák, mások pedig az alkalmazás-rendszergazdák. Emellett a rendszergazdák csak bizonyos objektumokat, például eszközöket kezelhetnek.
* Még nagyobb méretű telepítések esetén még részletesebb engedélyek is megjelenhetnek, és lehetséges, hogy a rendszergazdák nem hagyományos vagy hibrid szerepkörökkel rendelkeznek.

Az Azure AD-portálon [megtekintheti bármely szerepkör összes tagját](manage-roles-portal.md), amely segítségével gyorsan megtekintheti a központi telepítést, és delegálhatja az engedélyeket.

Ha az Azure AD-ben való rendszergazdai hozzáférés helyett az Azure-erőforrásokhoz való hozzáférés delegálását érdekli, tekintse meg [Az Azure-szerepkörök a Azure Portal használatával történő hozzárendelését](../../role-based-access-control/role-assignments-portal.md)ismertető témakört.

## <a name="delegation-planning"></a>Delegálás tervezése

Olyan delegálási modellt fejleszt, amely megfelel az igényeinek. A delegálási modell fejlesztése egy iterációs tervezési folyamat, és javasoljuk, hogy kövesse az alábbi lépéseket:

* Adja meg a szükséges szerepköröket
* Alkalmazás-felügyelet delegálása
* Engedélyezze az alkalmazások regisztrálásának lehetőségét
* Alkalmazás tulajdonjogának delegálása
* Biztonsági terv kidolgozása
* Vészhelyzeti fiókok létrehozása
* Rendszergazdai szerepkörök védelme
* Emelt szintű Jogosultságszint-emelés ideiglenesvé tétele

## <a name="define-roles"></a>Szerepkörök definiálása

Határozza meg, hogy a rendszergazdák milyen Active Directory feladatokat hajtanak végre, és hogyan képezik le a szerepköröket. A Azure Portal [részletesebben is megtekintheti a szerepkörök leírását](manage-roles-portal.md) .

Az egyes feladatokat a gyakoriság, a fontosság és a nehézségek alapján kell kiértékelni. Ezek a feltételek a feladat-definíció alapvető szempontjai, mert szabályozzák, hogy az engedélyek delegálása szükséges-e:

* A rutinszerűen feltett feladatok korlátozott kockázattal rendelkeznek, és a legrövidebb betöltésük kiválóan alkalmas a delegálásra.
* Azok a feladatok, amelyeket ritkán tesznek elérhetővé, de nagy hatással vannak a szervezetre, és magas képzettségi szintet igényelnek, a delegálás előtt nagyon Körültekintőnek kell tekinteni. Ehelyett [ideiglenesen megemelheti a fiókot a szükséges szerepkörhöz](../privileged-identity-management/pim-configure.md) , vagy újra hozzárendelheti a feladatot.

## <a name="delegate-app-administration"></a>Alkalmazás-felügyelet delegálása

A szervezeten belüli alkalmazások elterjedése megterhelheti a delegálási modellt. Ha a globális rendszergazda számára az alkalmazás-hozzáférés felügyeletének terhét helyezi el, akkor valószínű, hogy a modell az idő függvényében növeli a terhelést. Ha olyan személyeknek adta meg a globális rendszergazdai szerepkört, mint például a vállalati alkalmazások konfigurálása, mostantól kioszthatja őket a következő kevésbé Kiemelt szerepkörökbe. Ez segít a biztonsági helyzet javításában, és csökkenti a nem szerencsés hibák lehetséges lehetőségét. A leginkább privilegizált alkalmazás-rendszergazdai szerepkörök a következők:

* Az **alkalmazás-rendszergazda** szerepkör, amely lehetővé teszi a címtárban lévő összes alkalmazás felügyeletét, beleértve a regisztrációt, az egyszeri bejelentkezés beállításait, a felhasználók és csoportok hozzárendelését, a licencelést, az alkalmazásproxy beállításait és a jóváhagyást. Nem biztosít lehetőséget a feltételes hozzáférés kezelésére.
* A **Cloud Application Administrator** szerepkör, amely az alkalmazás rendszergazdája által biztosított összes képességet biztosítja, kivéve, ha nem biztosít hozzáférést az alkalmazásproxy-beállításokhoz (mert nincs helyszíni engedélye).

## <a name="delegate-app-registration"></a>Alkalmazás-regisztráció delegálása

Alapértelmezés szerint minden felhasználó létrehozhat alkalmazás-regisztrációkat. Az alkalmazás-regisztrációk létrehozásának lehetősége szelektív módon:

* A felhasználók megadhatják, hogy az alkalmazások ne legyenek **regisztrálva** **felhasználói beállításokban**
* A felhasználó társítása az alkalmazás fejlesztői szerepköréhez

Ahhoz, hogy az alkalmazások hozzáférjenek az adathozzáféréshez, szelektív módon engedélyezzük a jóváhagyást:

* A **felhasználók megadhatják, hogy az alkalmazások a vállalati adatokhoz hozzáférjenek** a nem **felhasználói beállításokban**
* A felhasználó társítása az alkalmazás fejlesztői szerepköréhez

Amikor egy alkalmazás-fejlesztő létrehoz egy új alkalmazás-regisztrációt, a rendszer automatikusan hozzáadja őket az első tulajdonosként.

## <a name="delegate-app-ownership"></a>Alkalmazás tulajdonjogának delegálása

A még finomabb alkalmazás-hozzáférési delegáláshoz is hozzárendelhet tulajdonosi jogokat az egyes vállalati alkalmazásokhoz. Ez kiegészíti az alkalmazás-regisztrációs tulajdonosok hozzárendelésének meglévő támogatását. A tulajdonost a vállalati alkalmazások panelen, a vállalaton belüli alkalmazások alapján rendeli hozzá a rendszer. A tulajdonos csak a saját vállalati alkalmazásokat tudja kezelni. Például hozzárendelhet egy tulajdonost a Salesforce alkalmazáshoz, és a tulajdonos kezelheti a Salesforce való hozzáférést és konfigurálást, valamint más alkalmazásokat nem. A vállalati alkalmazások számos tulajdonossal rendelkezhetnek, és a felhasználók számos vállalati alkalmazás tulajdonosa lehet. Két alkalmazás-tulajdonosi szerepkör létezik:

* A **vállalati alkalmazás tulajdonosi** szerepköre lehetővé teszi a vállalati alkalmazások, például az egyszeri bejelentkezési beállítások, a felhasználói és csoportos hozzárendelések, valamint további tulajdonosok hozzáadásának kezelését. Nem biztosít lehetőséget az alkalmazásproxy-beállítások vagy a feltételes hozzáférés kezelésére.
* Az **alkalmazás-regisztráció tulajdonosi** szerepköre lehetővé teszi a felhasználó tulajdonában lévő alkalmazás-regisztrációk kezelését, beleértve az alkalmazás jegyzékfájlját és további tulajdonosok hozzáadását.

## <a name="develop-a-security-plan"></a>Biztonsági terv kidolgozása

Az Azure AD átfogó útmutatót biztosít az Azure AD-rendszergazdai szerepkörökre vonatkozó biztonsági csomag megtervezéséhez és végrehajtásához, valamint a [hibrid és a Felhőbeli üzembe helyezéshez szükséges emelt szintű hozzáférés biztosításához](security-planning.md).

## <a name="establish-emergency-accounts"></a>Vészhelyzeti fiókok létrehozása

Ha probléma merül fel, az Identitáskezelő-tárolóhoz való hozzáférés biztosítása érdekében hozzon létre vészhelyzeti hozzáférési fiókokat a [vészhelyzeti hozzáférésű rendszergazdai fiókok létrehozása](security-emergency-access.md)alapján.

## <a name="secure-your-administrator-roles"></a>Rendszergazdai szerepkörök védelme

A rendszerjogosultságú fiókok felügyeletét befogadó támadók óriási károkat okozhatnak, ezért először az alapszintű [hozzáférési szabályzattal](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/22/baseline-security-policy-for-azure-ad-admin-accounts-in-public-preview/) , amely az összes Azure ad-szervezet (nyilvános előzetes verzió) esetében alapértelmezés szerint elérhető. A házirend kikényszeríti a többtényezős hitelesítést a Kiemelt Azure AD-fiókokban. A következő Azure AD-szerepköröket az Azure AD alapszabálya szabályozza:

* Globális rendszergazda
* SharePoint-rendszergazda
* Exchange-rendszergazda
* Feltételes hozzáférésű rendszergazda
* Biztonsági rendszergazda

## <a name="elevate-privilege-temporarily"></a>Jogosultságszint-emelési jogosultság átmenetileg

A mindennapos tevékenységek esetében nem minden felhasználónak globális rendszergazdai jogosultságra van szüksége, és nem mindegyiknek véglegesen hozzá kell rendelnie a globális rendszergazdai szerepkörhöz. Ha a felhasználóknak a globális rendszergazda engedélyeire van szükségük, aktiválniuk kell a szerepkör-hozzárendelést az Azure AD-ban [Privileged Identity Management](../privileged-identity-management/pim-configure.md) a saját fiókjában vagy egy másik rendszergazdai fiókban.

## <a name="next-steps"></a>Következő lépések

Az Azure AD-szerepkörök leírásával kapcsolatos információkért lásd: [rendszergazdai szerepkörök társítása az Azure ad-ben](permissions-reference.md)