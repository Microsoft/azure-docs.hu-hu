---
title: Alkalmazások végfelhasználói élményei – Azure Active Directory
description: Azure Active Directory (Azure AD) számos testreszabható módszert kínál az alkalmazások végfelhasználók számára történő központi telepítéséhez a szervezetben.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: kenwith
ms.reviewer: arvindh
ms.openlocfilehash: 0d3f1cab0637c7409cbc45f4f5a5374b2b5b6a77
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259253"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>A Azure Active Directory alkalmazások végfelhasználói élményei

Azure Active Directory (Azure AD) számos testreszabható módszert kínál az alkalmazások végfelhasználók számára történő központi telepítéséhez a szervezetben:

* Azure AD-alkalmazások
* Microsoft 365 Application Launcher
* Közvetlen bejelentkezés az összevont alkalmazásokba
* Mélyhivatkozások az összevont, jelszóalapú vagy meglévő alkalmazásokhoz

Saját belátása szerint melyik módszer (ek) et szeretné üzembe helyezni a szervezetében.

## <a name="azure-ad-my-apps"></a>Azure AD-alkalmazások

A My apps on https://myapps.microsoft.com egy webalapú portál, amely lehetővé teszi, hogy a végfelhasználók a Azure Active Directory szervezeti fiókkal megtekintsék és elindítsák azokat az alkalmazásokat, amelyekre az Azure ad-rendszergazda hozzáférést kapott. Ha Ön a [prémium szintű Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)-t használó felhasználó, az alkalmazásokon keresztül önkiszolgáló csoport-felügyeleti képességeket is használhat.

Alapértelmezés szerint az összes alkalmazás egyetlen oldalon jelenik meg. Azonban a gyűjtemények segítségével csoportosíthatja a kapcsolódó alkalmazásokat, és egy külön lapon is bemutathatja őket, így könnyebben megtalálhatja őket. A gyűjtemények segítségével például létrehozhat logikai csoportosításokat az alkalmazások számára adott feladatok, feladatok, projektek és így tovább. További információ: [gyűjtemények létrehozása a saját alkalmazások portálon](access-panel-collections.md). 

A saját alkalmazások el vannak különítve a Azure Portaltól, és nincs szükség arra, hogy a felhasználók Azure-előfizetéssel vagy Microsoft 365-előfizetéssel rendelkezzenek.

Az Azure AD-vel kapcsolatos további információkért tekintse meg az [alkalmazások bevezetését](../user-help/my-apps-portal-end-user-access.md)ismertető témakört.

## <a name="microsoft-365-application-launcher"></a>Microsoft 365 Application Launcher

A Microsoft 365 üzembe helyezett szervezetek esetében az Azure AD-n keresztül a felhasználókhoz rendelt alkalmazások az Office 365 portálon is megjelennek [https://portal.office.com/myapps](https://portal.office.com/myapps) . Így a szervezet felhasználói egyszerűen és kényelmesen indíthatják el alkalmazásaikat anélkül, hogy egy második portált kellene használniuk, és az ajánlott alkalmazás-indítási megoldás a Microsoft 365 használó szervezeteknek.

Az Office 365 Application Launcher szolgáltatással kapcsolatos további információkért tekintse [meg az alkalmazás megjelenése az office 365 app launcherben](/previous-versions/office/office-365-api/)című témakört.

## <a name="direct-sign-on-to-federated-apps"></a>Közvetlen bejelentkezés az összevont alkalmazásokba

Az SAML 2,0, a WS-Federation vagy az OpenID Connect szolgáltatást támogató legtöbb összevont alkalmazás támogatja a felhasználók számára az alkalmazások indításának lehetőségét, majd az Azure AD-be való bejelentkezést az automatikus átirányítás vagy egy hivatkozásra kattintva. Ezt a szolgáltatást szolgáltató által kezdeményezett bejelentkezésnek nevezik, és az Azure AD-alkalmazás-katalógusban található legtöbb összevont alkalmazás támogatja ezt (lásd az alkalmazás egyszeri bejelentkezési konfigurációs varázslójával csatolt dokumentációt a részletekért Azure Portal).

## <a name="direct-sign-on-links"></a>Közvetlen bejelentkezési hivatkozások

Az Azure AD Emellett támogatja a közvetlen egyszeri bejelentkezést a jelszó-alapú egyszeri bejelentkezést, a csatolt egyszeri bejelentkezést és az összevont egyszeri bejelentkezést támogató egyes alkalmazásokra.

Ezek a hivatkozások kifejezetten olyan URL-címek, amelyek egy adott alkalmazáshoz az Azure AD bejelentkezési folyamatán keresztül küldik el a felhasználót anélkül, hogy a felhasználónak el kellene indítania azokat az Azure AD saját alkalmazásaiból vagy Microsoft 365. Ezek a **felhasználói hozzáférési URL-címek** az elérhető vállalati alkalmazások tulajdonságai alatt találhatók. A Azure Portal válassza a **Azure Active Directory**  >  **vállalati alkalmazások** lehetőséget. Válassza ki az alkalmazást, majd válassza a **Tulajdonságok** lehetőséget.

![Felhasználói hozzáférési URL-cím – példa a Twitter tulajdonságaiban](media/end-user-experiences/direct-sign-on-link.png)

Ezeket a hivatkozásokat bárhol másolhatja és beillesztheti, ha bejelentkezési hivatkozást szeretne megadni a kijelölt alkalmazáshoz. Ez lehet egy e-mailben vagy bármely olyan egyéni webalapú portálon, amelyet a felhasználói alkalmazás eléréséhez beállított. Íme egy példa az Azure AD Direct egyszeri bejelentkezési URL-címére a Twitteren:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Az alkalmazásokhoz hasonló szervezeti URL-címekhez hasonlóan az URL-cím az *MyApps.microsoft.com* tartomány után a címtárhoz tartozó aktív vagy ellenőrzött tartományok egyikének hozzáadásával is testreszabható. Ez biztosítja, hogy a céges védjegyezés azonnal betöltődik a bejelentkezési oldalon anélkül, hogy a felhasználónak először meg kell adnia a felhasználói azonosítót:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Ha egy jogosult felhasználó rákattint az adott alkalmazásra vonatkozó hivatkozások egyikére, először a szervezeti bejelentkezési oldal jelenik meg (feltéve, hogy még nincsenek bejelentkezve), és a bejelentkezést követően a rendszer átirányítja az alkalmazásba az alkalmazások első leállítása nélkül. Ha a felhasználó nem rendelkezik az alkalmazás eléréséhez szükséges előfeltételekkel, például a jelszó-alapú egyszeri bejelentkezés böngésző bővítményével, akkor a hivatkozás arra kéri a felhasználót, hogy telepítse a hiányzó bővítményt. A hivatkozás URL-címe szintén állandó marad, ha az alkalmazás egyszeri bejelentkezési konfigurációja megváltozik.

Ezek a hivatkozások ugyanazokat a hozzáférés-vezérlési mechanizmusokat használják, mint az alkalmazások és a Microsoft 365, és csak azok a felhasználók vagy csoportok lesznek sikeresek a hitelesítésben, amelyek a Azure Portal alkalmazáshoz vannak rendelve. Azonban minden olyan felhasználó számára, aki nem engedélyezett, megjelenik egy üzenet arról, hogy nem kaptak hozzáférést, és az alkalmazások betöltésére szolgáló hivatkozásra kattintva megtekintheti azokat az elérhető alkalmazásokat, amelyekhez hozzáféréssel rendelkeznek.

## <a name="next-steps"></a>Következő lépések

* [Gyorsindítás sorozat az alkalmazás-kezelésben](view-applications-portal.md)
* [Mi az egyszeri bejelentkezés?](what-is-single-sign-on.md)
* [Azure Active Directory integrálása az első lépések útmutató alkalmazásokkal](plan-an-application-integration.md)