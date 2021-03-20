---
title: Migrálás az új fejlesztői portálra az örökölt fejlesztői portálról
titleSuffix: Azure API Management
description: Megtudhatja, hogyan telepítheti át a régi fejlesztői portálról az új fejlesztői portálra a API Managementon.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: f5105c685de4b3ccdffe69eec8ee8eeb32976c1f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92325993"
---
# <a name="migrate-to-the-new-developer-portal"></a>Migrálás az új fejlesztői portálra

Ez a cikk azokat a lépéseket ismerteti, amelyeket át kell telepítenie az elavult örökölt portálról az új fejlesztői portálra API Managementban.

> [!IMPORTANT]
> A régi fejlesztői portál elavult, és csak a biztonsági frissítéseket fogja kapni. Továbbra is használhatja, a szokásos módon, a 2023 októberében való kivonulás után, amikor az összes API Management szolgáltatásból el lesz távolítva.

![API Management fejlesztői portál](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="improvements-in-new-developer-portal"></a>Újdonságok az új fejlesztői portálon

Az új fejlesztői portál az elavult portál számos korlátozásával foglalkozik. A szolgáltatás egy [vizualizációs drag-drop szerkesztőt tartalmaz a tartalmak szerkesztéséhez](api-management-howto-developer-portal-customize.md) , valamint egy dedikált panelt a tervezők számára a webhely stílusához. A lapok, a testreszabások és a konfiguráció Azure Resource Manager erőforrásként lesznek mentve a API Management szolgáltatásban, amely lehetővé teszi a [portálon üzemelő példányok automatizálását](api-management-howto-developer-portal.md#automate). Végül pedig a portál kód is nyílt forráskódú, így [kiterjesztheti az egyéni funkciókkal](api-management-howto-developer-portal.md#managed-vs-self-hosted).

## <a name="how-to-migrate-to-new-developer-portal"></a>Migrálás az új fejlesztői portálra

Az új fejlesztői portál nem kompatibilis az elavult portálral, és az automatikus áttelepítés nem lehetséges. Manuálisan újra létre kell hoznia a tartalmat (lapok, szöveg, médiafájlok), és testre kell szabnia az új portál megjelenését. A pontos lépések a portál testreszabásainak és összetettségének függvényében változhatnak. Útmutatásért tekintse meg [a fejlesztői portál oktatóanyagát](api-management-howto-developer-portal-customize.md) . A fennmaradó konfiguráció, például az API-k, a termékek, a felhasználók és az identitás-szolgáltatók listája is automatikusan megoszlik mindkét portálon.

> [!IMPORTANT]
> Ha korábban már elindította az új fejlesztői portált, de nem módosította a módosításokat, [állítsa alaphelyzetbe az alapértelmezett tartalmat](api-management-howto-developer-portal.md#preview-to-ga) a legújabb verzióra való frissítéshez.

Ha az elavult portálról végez áttelepítést, vegye figyelembe a következő változásokat:

- Ha egyéni tartományon keresztül teszi elérhetővé a fejlesztői portált, [rendeljen hozzá egy tartományt](configure-custom-domain.md) az új fejlesztői portálhoz. A Azure Portal legördülő menüjében használja a **fejlesztői portál** lehetőséget.
- [Alkalmazzon CORS szabályzatot](api-management-howto-developer-portal.md#cors) az API-kra az interaktív tesztelési konzol engedélyezéséhez.
- Ha egyéni CSS-t szúr be a portál stílusára, a stílust [a beépített kialakítási panel használatával kell replikálni](api-management-howto-developer-portal-customize.md). A CSS-injektálás nem engedélyezett az új portálon.
- Egyéni JavaScriptet csak az [új portál saját üzemeltetésű verziójában](api-management-howto-developer-portal.md#managed-vs-self-hosted)lehet beszúrni.
- Ha a API Management virtuális hálózatban található, és Application Gateway keresztül teszi elérhetővé az internetet, tekintse meg a részletes konfigurációs lépéseket a [jelen dokumentációs cikkben](api-management-howto-integrate-internal-vnet-appgateway.md) . A következőket kell tennie:

    - A API Management felügyeleti végponthoz való kapcsolódás engedélyezése.
    - Engedélyezze a kapcsolatot az új portál végpontján.
    - A webalkalmazási tűzfalszabályok kiválasztott szabályainak letiltása.

- Ha módosította az alapértelmezett e-mail értesítési sablonokat, hogy egy explicit módon definiált elavult portál URL-címet tartalmazzon, módosítsa azokat a portál URL-címének használatára, vagy mutasson az új portál URL-címére. Ha a sablonok Ehelyett a beépített portál URL-címét használják, nincs szükség módosításra.
- A *problémák* és az *alkalmazások* nem támogatottak az új fejlesztői portálon.
- Az új fejlesztői portálon nem támogatott a Facebook, a Microsoft, a Twitter és a Google közvetlen integrációja az identitás-szolgáltatóként. Ezeket a szolgáltatókat Azure AD B2C használatával integrálhatja.
- Ha delegálást használ, módosítsa a visszatérési URL-címet az alkalmazásaiban, és használja az *egyszeri bejelentkezési URL-cím létrehozása* végpont helyett a [ *megosztott hozzáférési jogkivonat* API-végpontjának beolvasása](/rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken) parancsot.
- Ha az Azure AD-t identitás-szolgáltatóként használja:

    - Módosítsa az alkalmazás visszatérési URL-címét úgy, hogy az az új fejlesztői portál tartományra mutasson.
    - Módosítsa a visszatérési URL-cím utótagját az alkalmazásban a verzióról a verzióra `/signin-aad` `/signin` .

- Ha Azure AD B2C identitás-szolgáltatóként használja:

    - Módosítsa az alkalmazás visszatérési URL-címét úgy, hogy az az új fejlesztői portál tartományra mutasson.
    - Módosítsa a visszatérési URL-cím utótagját az alkalmazásban a verzióról a verzióra `/signin-aad` `/signin` .
    - Adja meg a *név*, a *vezetéknév* és a *felhasználó objektumazonosítót* az alkalmazás jogcímeiben.

- Ha a OAuth 2,0-et használja az interaktív tesztelési konzolon, módosítsa az alkalmazás visszatérési URL-címét úgy, hogy az az új fejlesztői portál tartományra mutasson, és módosítsa az utótagot:

    - A-től a- `/docs/services/[serverName]/console/oauth2/authorizationcode/callback` ig `/signin-oauth/code/callback/[serverName]` az engedélyezési kód engedélyezési folyamata.
    - Az `/docs/services/[serverName]/console/oauth2/implicit/callback` `/signin-oauth/implicit/callback` implicit engedélyezési folyamathoz.
- Ha az interaktív tesztelési konzolon OpenID connectet használ, módosítsa az alkalmazás visszatérési URL-címét úgy, hogy az az új fejlesztői portál tartományra mutasson, és módosítsa az utótagot:

    - A-től a- `/docs/services/[serverName]/console/openidconnect/authorizationcode/callback` ig `/signin-oauth/code/callback/[serverName]` az engedélyezési kód engedélyezési folyamata.
    - Az `/docs/services/[serverName]/console/openidconnect/implicit/callback` `/signin-oauth/implicit/callback` implicit engedélyezési folyamathoz.

## <a name="next-steps"></a>Következő lépések

További információ a fejlesztői portálról:

- [Az Azure API Management fejlesztői portál áttekintése](api-management-howto-developer-portal.md)
- [A fejlesztői portál elérése és testreszabása](api-management-howto-developer-portal-customize.md)