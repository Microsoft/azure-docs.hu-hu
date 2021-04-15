---
title: Alkalmazások végfelhasználói élménye – Azure Active Directory
description: Azure Active Directory (Azure AD) számos testreszabható módszert kínál az alkalmazások telepítésére a szervezet végfelhasználói számára.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: iangithinji
ms.reviewer: arvindh
ms.openlocfilehash: c555899a65a5e8cf4c8fcc6214e4dcbda3931f08
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374233"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Végfelhasználói élmények a Azure Active Directory

Azure Active Directory (Azure AD) számos testreszabható módszert kínál az alkalmazások üzembe helyezésére a szervezet végfelhasználói számára:

* Azure AD-Saját alkalmazások
* Microsoft 365 alkalmazásindító használata
* Közvetlen bejelentkezés az összevont alkalmazásokba
* Mélyhivatkozások az összevont, jelszóalapú vagy meglévő alkalmazásokhoz

Az Ön belátása szerint melyik módszer(et) választja a szervezetben való üzembe helyezéshez.

## <a name="azure-ad-my-apps"></a>Azure AD-Saját alkalmazások

Saját alkalmazások egy webalapú portál, amely lehetővé teszi, hogy az Azure Active Directory-ban szervezeti fiókkal rendelkező végfelhasználó megtekintsen és elindítson olyan alkalmazásokat, amelyekhez az Azure AD-rendszergazda hozzáférést https://myapps.microsoft.com kapott. Ha Ön egy olyan végfelhasználó, prémium szintű Azure Active Directory [az](https://azure.microsoft.com/pricing/details/active-directory/)önkiszolgáló csoportkezelési képességeket is használhatja a Saját alkalmazások.

Alapértelmezés szerint az összes alkalmazás egyetlen lapon van felsorolva. Gyűjteményekkel azonban csoportosíthatja a kapcsolódó alkalmazásokat, és egy külön lapon is bemutathatja őket, így könnyebben megtalálja őket. Gyűjtemények használatával például alkalmazások logikai csoportosítását hozhatja létre adott feladatszerepk szerepkörökhöz, tevékenységekhez, projektekhez stb. További információ: [Gyűjtemények létrehozása a Saját alkalmazások portálon.](access-panel-collections.md) 

Saját alkalmazások elkülönül a Azure Portal, és nem követeli meg, hogy a felhasználók Azure-előfizetéssel vagy előfizetéssel Microsoft 365 előfizetéssel.

Az Azure AD-Saját alkalmazások lásd a [Saját alkalmazások.](../user-help/my-apps-portal-end-user-access.md)

## <a name="microsoft-365-application-launcher"></a>Microsoft 365 alkalmazásindító használata

Az Azure AD-Microsoft 365 felhasználókhoz rendelt alkalmazások az Office 365 portálon is megjelennek a [https://portal.office.com/myapps](https://portal.office.com/myapps) webhelyen. Így a szervezet felhasználói egyszerűen és kényelmesen, második portál használata nélkül is elindítják az alkalmazásokat, és ez az ajánlott alkalmazásindítási megoldás az olyan szervezetek számára, amelyek Microsoft 365.

Az Office 365 alkalmazásindítóval kapcsolatos további információkért lásd: Az alkalmazás megjelenése az [Office 365 alkalmazásindítójában.](/previous-versions/office/office-365-api/)

## <a name="direct-sign-on-to-federated-apps"></a>Közvetlen bejelentkezés az összevont alkalmazásokba

Az SAML 2.0-t, WS-Federationt vagy OpenID Connectet támogató legtöbb összevont alkalmazás támogatja azt a képességet is, hogy a felhasználók az alkalmazástól indulva automatikus átirányítással vagy egy hivatkozásra kattintva bejelentkeznek az Azure AD-ben. Ezt szolgáltató által kezdeményezett bejelentkezésnek nevezik, és az Azure AD alkalmazáskatatár legtöbb összevont alkalmazása támogatja ezt (részletekért tekintse meg az alkalmazás egyszeri bejelentkezési konfigurációs varázslójának dokumentációját a Azure Portal).

## <a name="direct-sign-on-links"></a>Közvetlen bejelentkezési hivatkozások

Az Azure AD támogatja a közvetlen egyszeri bejelentkezési hivatkozásokat is, amelyek támogatják a jelszóalapú egyszeri bejelentkezést, a csatolt egyszeri bejelentkezést és az összevont egyszeri bejelentkezés bármilyen formáját.

Ezek a hivatkozások kifejezetten olyan URL-címek, amelyek az Azure AD bejelentkezési folyamatán keresztül küldik el a felhasználót egy adott alkalmazáshoz anélkül, hogy a felhasználónak el kell indítania őket az Azure AD Saját alkalmazások-ból vagy Microsoft 365. Ezek **a felhasználói hozzáférési URL-címek** az elérhető vállalati alkalmazások tulajdonságai között találhatók. A Azure Portal válassza a **Vállalati Azure Active Directory**  >  **lehetőséget.** Válassza ki az alkalmazást, majd válassza a **Tulajdonságok lehetőséget.**

![Példa a Felhasználói hozzáférés URL-címére a Twitter tulajdonságai között](media/end-user-experiences/direct-sign-on-link.png)

Ezek a hivatkozások bárhol másolhatók és bemásolhatók, ahol meg szeretné adni a kiválasztott alkalmazásra mutató bejelentkezési hivatkozást. Ez lehet egy e-mailben vagy bármely olyan egyéni webes portálon, amely felhasználói alkalmazás-hozzáférésre van beállítva. Az alábbi példa egy Közvetlen Azure AD egyszeri bejelentkezési URL-címet mutat be a Twitterhez:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

A szervezetspecifikus URL-címekhez hasonlóan a Saját alkalmazások is testre szabhatja ezt az URL-címet, ha hozzáadja az egyik aktív vagy ellenőrzött tartományt a címtárhoz a myapps.microsoft.com *után.* Ez biztosítja, hogy a céges arculat azonnal betöltődik a bejelentkezési oldalon anélkül, hogy a felhasználónak először meg kellene adnia a felhasználói azonosítóját:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Amikor egy jogosult felhasználó az alkalmazásspecifikus hivatkozások egyikére kattint, először látja a vállalati bejelentkezési oldalt (feltéve, hogy még nincs bejelentkezve), és a bejelentkezés után anélkül lesz átirányítva az alkalmazásba, hogy Saját alkalmazások leállna. Ha a felhasználónak hiányoznak az alkalmazás eléréséhez szükséges előfeltételek, például a jelszóalapú egyszerijel-böngészőbővítmény, akkor a hivatkozás felszólítja a felhasználót, hogy telepítse a hiányzó bővítményt. A hivatkozás URL-címe akkor is állandó marad, ha az alkalmazás egyszeri bejelentkezési konfigurációja megváltozik.

Ezek a hivatkozások ugyanazt a hozzáférés-vezérlési mechanizmusokat használják, mint az Saját alkalmazások és Microsoft 365, és csak azok a felhasználók vagy csoportok lesznek sikeres hitelesítésre képesek, akik hozzá vannak rendelve az alkalmazáshoz a Azure Portal alkalmazásban. A jogosulatlan felhasználók számára azonban megjelenik egy üzenet, amely tudatja vele, hogy nem kapott hozzáférést, és kap egy hivatkozást az Saját alkalmazások betöltésére az elérhető alkalmazások megtekintéséhez, amelyekhez hozzáféréssel rendelkezik.

## <a name="next-steps"></a>Következő lépések

* [Gyorsútmutató-sorozat az alkalmazáskezelésről](view-applications-portal.md)
* [Mi az az egyszeri bejelentkezés?](what-is-single-sign-on.md)
* [Integrációs Azure Active Directory alkalmazások első lépések útmutatója](plan-an-application-integration.md)