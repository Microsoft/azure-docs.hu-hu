---
title: Áttelepítés az új fejlesztői portálra az örökölt fejlesztői portálról
titleSuffix: Azure API Management
description: Útmutató az örökölt fejlesztői portálról az új fejlesztői portálra való áttelepítéshez a API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: apimpm
ms.openlocfilehash: e4f9f3822b58886f7d453d52402b078d8401133f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738881"
---
# <a name="migrate-to-the-new-developer-portal"></a>Áttelepítés az új fejlesztői portálra

Ez a cikk azokat a lépéseket ismerteti, amelyek az elavult régi portálról az új fejlesztői portálra való áttelepítéshez API Management.

> [!IMPORTANT]
> Az örökölt fejlesztői portál elavult, és csak biztonsági frissítéseket fog kapni. Továbbra is használhatja a szokásos módon, a 2023. októberi kiesőgáig, amikor a rendszer eltávolítja az összes API Management szolgáltatásból.

![API Management fejlesztői portál](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="improvements-in-new-developer-portal"></a>Fejlesztések az új fejlesztői portálon

Az új fejlesztői portál az elavult portál számos korlátozásával foglalkozik. Tartalmaz egy vizuális [húzással szerkesztőt](api-management-howto-developer-portal-customize.md) a tartalom szerkesztéséhez, valamint egy dedikált panelt a tervezők számára a webhely stílusának beállításához. A lapok, testreszabások és konfigurációk Azure Resource Manager erőforrásokként vannak mentve a API Management szolgáltatásban, ami lehetővé teszi a portál üzembe [helyezésének automatizálását.](automate-portal-deployments.md) Végül a portál kódbázisa nyílt forráskódú, így egyéni funkciókkal [bővítheti.](api-management-howto-developer-portal.md#managed-vs-self-hosted)

## <a name="how-to-migrate-to-new-developer-portal"></a>Áttelepítés új fejlesztői portálra

Az új fejlesztői portál nem kompatibilis az elavult portállal, és az automatikus migrálás nem lehetséges. Manuálisan újra létre kell hoznia a tartalmat (oldalakat, szövegeket, médiafájlokat), és testre kell szabnia az új portált. A pontos lépések a portál testreszabásától és összetettségétől függően változnak. Útmutatásért tekintse meg [a fejlesztői portál oktatóanyagát.](api-management-howto-developer-portal-customize.md) A fennmaradó konfigurációk, például az API-k, a termékek, a felhasználók és az identitásszolgáltatók listája, automatikusan meg vannak osztva mindkét portálon.

> [!IMPORTANT]
> Ha korábban már elindította az új fejlesztői portált, de még nem végrehajtott módosításokat, állítsa alaphelyzetbe az alapértelmezett tartalmat, hogy az a legújabb verzióra frissüljon.

Az elavult portálról való áttelepítéskor tartsa szem előtt a következő módosításokat:

- Ha egyéni tartományon keresztül teszi elérhetővé a fejlesztői portált, rendeljen [hozzá](configure-custom-domain.md) egy tartományt az új fejlesztői portálhoz. Használja **a fejlesztői portál** lehetőséget a legördülő menüből, Azure Portal.
- [CorS-szabályzat alkalmazása](developer-portal-faq.md#cors) az API-kon az interaktív tesztkonzol engedélyezéséhez.
- Ha egyéni CSS-t injektál a portál stílusának kialakításához, a beépített tervezőpanel használatával replikálni kell a [stílust.](api-management-howto-developer-portal-customize.md) A CSS-injektálás nem engedélyezett az új portálon.
- Egyéni JavaScriptet csak az új portál saját üzemeltetett verziójába [injektálhat.](api-management-howto-developer-portal.md#managed-vs-self-hosted)
- Ha a API Management virtuális hálózaton található, és az interneten keresztül [Application Gateway,](api-management-howto-integrate-internal-vnet-appgateway.md) a konfigurációs lépések pontos lépéseit ebben a dokumentációban találhatja meg. A következőt kell:

    - Engedélyezze a API Management felügyeleti végponthoz való csatlakozást.
    - Engedélyezze az új portálvégponthoz való csatlakozást.
    - Tiltsa le a Web Application Firewall szabályokat.

- Ha úgy módosította az alapértelmezett e-mail-értesítési sablonokat, hogy tartalmazzanak egy explicit módon definiált, elavult portál URL-címet, módosítsa őket úgy, hogy a portál URL-paraméterét használják, vagy az új portál URL-címére mutasson. Ha a sablonok ehelyett a beépített portál URL-paraméterét használják, nincs szükség módosításokra.
- *Az* új *fejlesztői* portál nem támogatja a problémákat és az alkalmazásokat.
- Az új fejlesztői portál nem támogatja a Facebookkal, a Microsofttal, a Twittersel és a Google-lal való közvetlen integrációt identitásszolgáltatóként. A szolgáltatókkal az integrációt a Azure AD B2C.
- Ha delegálást használ, módosítsa a visszaadott URL-címet az alkalmazásokban, és használja a Get [ *Shared Access Token* API](/rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken) végpontot az *SSO URL-cím létrehozása végpont* helyett.
- Ha az Azure AD-t használja identitásszolgáltatóként:

    - Módosítsa a visszaadott URL-címet az alkalmazásban úgy, hogy az új fejlesztői portál tartományra mutasson.
    - Módosítsa az alkalmazásban visszaadott URL-cím utótagját a `/signin-aad` következőre: `/signin` .

- Ha a Azure AD B2C identitásszolgáltatóként használja:

    - Módosítsa a visszaadott URL-címet az alkalmazásban úgy, hogy az új fejlesztői portál tartományra mutasson.
    - Módosítsa az alkalmazásban a visszaadott URL-cím utótagját a `/signin-aad` következőre: `/signin` .
    - Az *alkalmazás jogcímek* közé  foglalja bele a Utónév, a *Vezetéknév* és a Felhasználó objektumazonosítóját.

- Ha az OAuth 2.0-t használja az interaktív tesztkonzolon, módosítsa a visszaadott URL-címet az alkalmazásban úgy, hogy az új fejlesztői portál tartományára mutasson, és módosítsa az utótagot:

    - Az engedélyezési kód engedélyezési folyamatának át és `/docs/services/[serverName]/console/oauth2/authorizationcode/callback` `/signin-oauth/code/callback/[serverName]` között.
    - Az `/docs/services/[serverName]/console/oauth2/implicit/callback` implicit `/signin-oauth/implicit/callback` engedélyfolyamhoz a következőtől a következőre: .
- Ha az interaktív OpenID Connect konzolon használja, módosítsa a visszaadott URL-címet az alkalmazásban úgy, hogy az új fejlesztői portál tartományra mutasson, és módosítsa az utótagot:

    - Az engedélyezési kód engedélyezési folyamatának át és `/docs/services/[serverName]/console/openidconnect/authorizationcode/callback` `/signin-oauth/code/callback/[serverName]` között.
    - Az `/docs/services/[serverName]/console/openidconnect/implicit/callback` implicit `/signin-oauth/implicit/callback` engedélyfolyamhoz a következőtől a következőre: .

## <a name="next-steps"></a>Következő lépések

További információ a fejlesztői portálról:

- [Az Azure API Management fejlesztői portál áttekintése](api-management-howto-developer-portal.md)
- [A fejlesztői portál elérése és testreszabása](api-management-howto-developer-portal-customize.md)