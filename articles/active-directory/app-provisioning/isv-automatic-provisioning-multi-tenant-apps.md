---
title: Automatikus felhasználó-kiépítés engedélyezése több-bérlős alkalmazások számára – Azure AD
description: Útmutató független szoftvergyártók számára az automatikus kiépítés engedélyezéséhez
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 07/23/2019
ms.author: kenwith
ms.reviewer: zhchia
ms.openlocfilehash: 7bd0fc634109beb6cc674d89f56666e7035d33ef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99255695"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application"></a>Automatikus felhasználó-kiépítés engedélyezése a több-bérlős alkalmazáshoz

A felhasználók automatikus kiépítésének folyamata a felhasználói identitások létrehozásának, karbantartásának és eltávolításának automatizálását célozza meg a megcélzott rendszerekben, például a szoftveres szolgáltatás alkalmazásaiban.

## <a name="why-enable-automatic-user-provisioning"></a>Miért érdemes engedélyezni az automatikus felhasználó-kiépítés szolgáltatást?

Azok az alkalmazások, amelyek megkövetelik, hogy egy felhasználói rekord megtalálható legyen az alkalmazásban, mielőtt a felhasználó először bejelentkeznek a felhasználók üzembe helyezéséhez. Előnyökkel jár a szolgáltató, valamint az ügyfelek számára nyújtott előnyök.

### <a name="benefits-to-you-as-the-service-provider"></a>A szolgáltatónak nyújtott előnyök

* Növelje az alkalmazás biztonságát a Microsoft Identity platform használatával.

* Csökkentse a tényleges és érzékelt felhasználói erőfeszítéseket az alkalmazás elfogadásához.

* Csökkentse a több Identity Provider (IDP) integrálásával járó költségeket a felhasználók automatikus kiépítéséhez a rendszer használatával a tartományok közötti Identitáskezelés (SCIM) alapú kiépítés során.

* Többek között a felhasználók kiépítési problémáinak elhárításához szükséges részletes naplók segítségével csökkentheti a támogatási költségeket.

* Növelje az alkalmazás láthatóságát az [Azure ad app Galleryben](https://azuremarketplace.microsoft.com/marketplace/apps).

* Rangsorolt lista beszerzése az alkalmazás-oktatóanyagok oldalon.

### <a name="benefits-to-your-customers"></a>Előnyök az ügyfelek számára

* Növelje a biztonságot úgy, hogy automatikusan eltávolítja az alkalmazáshoz való hozzáférést olyan felhasználók számára, akik a szerepköröket módosítják, vagy a szervezetet az alkalmazásba hagyják.

* Egyszerűsítse az alkalmazás felhasználói felügyeletét azáltal, hogy elkerüli az emberi hibákat és a manuális kiépítés során ismétlődő munkát.

* Csökkentheti a saját fejlesztésű üzembe helyezési megoldások üzemeltetésének és karbantartásának költségeit.

## <a name="choose-a-provisioning-method"></a>Az üzembehelyezési módszer kiválasztása

Az Azure AD számos integrációs elérési utat biztosít, amelyek lehetővé teszik az alkalmazás automatikus felhasználó általi üzembe helyezését.

* Az [Azure ad-kiépítési szolgáltatás](../app-provisioning/user-provisioning.md) felügyeli az Azure ad-ből származó felhasználók kiépítését és megszüntetését az alkalmazásba (kimenő kiépítés) és az alkalmazásból az Azure ad-be (bejövő kiépítés). A szolgáltatás az alkalmazás által biztosított tartományok közötti Identitáskezelés (SCIM) felhasználói felügyeleti API-végpontok rendszeréhez csatlakozik.

* A [Microsoft Graph](/graph/)használatakor az alkalmazás az Microsoft Graph API lekérdezésével kezeli a felhasználók és csoportok bejövő és kimenő üzembe helyezését az Azure ad-ből az alkalmazásba.

* Az igény szerinti Security Assertion Markup Language (SAML JIT) felhasználói kiépítés akkor engedélyezhető, ha az alkalmazás SAML-t használ az összevonáshoz. Az SAML-jogkivonatban eljuttatott jogcímeket használja a felhasználók kiépítéséhez.

Ha meg szeretné határozni, hogy melyik integrációs lehetőséget szeretné használni az alkalmazáshoz, tekintse meg a magas szintű összehasonlító táblázatot, és tekintse meg az egyes lehetőségek részletes információit.

| Az automatikus kiépítés által engedélyezett vagy továbbfejlesztett képességek| Azure AD-kiépítési szolgáltatás (SCIM 2,0)| Microsoft Graph API (OData v 4.0)| SAML JIT |
|---|---|---|---|
| Felhasználók és csoportok kezelése az Azure AD-ben| √| √| Csak felhasználó |
| A helyszíni Active Directoryról szinkronizált felhasználók és csoportok kezelése| √*| √*| Csak felhasználó * |
| A felhasználókon és a csoportokon kívüli adathozzáférés a Microsoft 365-adatkapcsolathoz való kiépítés során (csapatok, SharePoint, E-mail, naptár, dokumentumok stb.)| X +| √| X |
| Felhasználók létrehozása, olvasása és frissítése üzleti szabályok alapján| √| √| √ |
| Felhasználók törlése üzleti szabályok alapján| √| √| X |
| Automatikus felhasználó-kiépítés kezelése a Azure Portal összes alkalmazásához| √| X| √ |
| Több Identity Provider támogatása| √| X| √ |
| Támogató vendég fiókok (B2B)| √| √| √ |
| Nem vállalati fiókok (B2C) támogatása| X| √| √ |

<sup>*</sup> – Azure AD Connect telepítés szükséges a felhasználók az AD-ből az Azure AD-be való szinkronizálásához.  
<sup>+</sup >– A SCIM használata az üzembe helyezéshez nem zárja ki, hogy az alkalmazás integrálható legyen Microsoft Graph egyéb célokra.

## <a name="azure-ad-provisioning-service-scim"></a>Azure AD kiépítési szolgáltatás (SCIM)

Az Azure AD-kiépítési szolgáltatások a [scim](https://aka.ms/SCIMOverview)-t, az iparági szabványokat használják számos Identity Provider (IDP) és alkalmazások (például Slack, g Suite, Dropbox) által támogatott üzembe helyezéshez. Azt javasoljuk, hogy az Azure AD kiépítési szolgáltatását használja, ha az Azure AD mellett szeretné támogatni a IDP-t, mivel bármely SCIM-kompatibilis identitásszolgáltató csatlakozhat a SCIM-végponthoz. Egy egyszerű/User-végpont létrehozása nélkül is engedélyezheti a kiépítést, anélkül, hogy a saját szinkronizálási motorját kellene fenntartania. 

Az Azure AD kiépítési szolgáltatás felhasználói SCIM kapcsolatos további információkért lásd: 

* [További információ a SCIM standardról](https://aka.ms/SCIMOverview)

* [Rendszer használata a tartományok közötti Identitáskezelés (SCIM) számára a felhasználók és csoportok automatikus kiépítéséhez Azure Active Directoryról alkalmazásokba](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [Az Azure AD SCIM implementációjának ismertetése](../app-provisioning/use-scim-to-provision-users-and-groups.md)

## <a name="microsoft-graph-for-provisioning"></a>Kiépítés Microsoft Graph

Ha Microsoft Grapht használ a kiépítés során, akkor a Graph-ban elérhető összes gazdag felhasználói adattal hozzáférhet. A felhasználók és csoportok részletein kívül további információkat is beolvashat, például a felhasználó szerepköreit, felettesét és közvetlen jelentéseit, a tulajdonában lévő és regisztrált eszközöket, valamint a [Microsoft Graphban](/graph/api/overview)elérhető több száz más adatmennyiséget. 

Több mint 15 000 000 szervezet és 90% of Fortune 500 vállalat használja az Azure AD-t a Microsoft Cloud Services, például a Microsoft 365, a Microsoft Azure vagy a nagyvállalati mobilitási csomagra való feliratkozáskor. A Microsoft Graph használatával integrálhatja az alkalmazást a felügyeleti munkafolyamatokkal, például az alkalmazottak bevezetésével (és megszüntetésével), a profil karbantartásával és egyéb lehetőségekkel. 

További információ a kiépítés Microsoft Graph használatáról:

* [Microsoft Graph Kezdőlap](https://developer.microsoft.com/graph)

* [A Microsoft Graph áttekintése](/graph/overview)

* [Microsoft Graph hitelesítés áttekintése](/graph/auth/)

* [A Microsoft Graph első lépései](https://developer.microsoft.com/graph/get-started)

## <a name="using-saml-jit-for-provisioning"></a>Az SAML JIT használata az üzembe helyezéshez

Ha csak az első bejelentkezés alkalmával szeretné kiépíteni a felhasználókat az alkalmazásba, és nem kell automatikusan kiépíteni a felhasználókat, az SAML JIT egy lehetőség. Az alkalmazásnak támogatnia kell az SAML 2,0-et összevonási protokollként az SAML JIT használatához.

Az SAML JIT az SAML-jogkivonat jogcím-információi alapján hozza létre és frissíti a felhasználói adatokat az alkalmazásban. Az ügyfelek igény szerint konfigurálhatják ezeket a szükséges jogcímeket az Azure AD-alkalmazásban. Időnként az JIT-kiépítés engedélyezése szükséges az alkalmazás oldaláról, hogy az ügyfél használhassa ezt a funkciót. Az SAML JIT hasznos a felhasználók létrehozásához és frissítéséhez, de nem tudja törölni vagy inaktiválni a felhasználókat az alkalmazásban.

## <a name="next-steps"></a>Következő lépések

* [Egyszeri bejelentkezés engedélyezése az alkalmazáshoz](../develop/v2-howto-app-gallery-listing.md)

* [Küldje el alkalmazásait](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) a Microsoft webhelyéről, és ossza meg a Microsofttal.

* [Csatlakozzon a Microsoft partner Networkhoz (ingyenes), és hozzon létre egy piacra lépési tervet](https://partner.microsoft.com/explore/commercial).
