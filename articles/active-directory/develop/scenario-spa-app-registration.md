---
title: Egyoldalas alkalmazások regisztrálása (SPA) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy egyoldalas alkalmazást (alkalmazás-regisztráció)
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: hahamil
ms.custom: aaddev
ms.openlocfilehash: 9366bb5b2bb5820245ec1b699bbf2ddda0dd9f9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103175"
---
# <a name="single-page-application-app-registration"></a>Egyoldalas alkalmazás: alkalmazás regisztrálása

Ha egy egyoldalas alkalmazást (SPA) szeretne regisztrálni a Microsoft Identity platformon, hajtsa végre az alábbi lépéseket. A regisztrációs lépések eltérnek a MSAL.js 1,0 között, amely támogatja az implicit engedélyezési folyamatot, és MSAL.js 2,0, amely támogatja az engedélyezési kódot a PKCE.

## <a name="create-the-app-registration"></a>Az alkalmazás regisztrációjának létrehozása

A MSAL.js 1,0-és 2,0-alapú alkalmazásokhoz a kezdeti alkalmazás regisztrációjának létrehozásához végezze el a következő lépéseket.

1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kíván egy alkalmazást.
1. Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **kezelés** területen válassza a **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.
1. Adja meg az alkalmazás **nevét** . Előfordulhat, hogy az alkalmazás felhasználói láthatják ezt a nevet, és később is megváltoztathatók.
1. Válassza ki az alkalmazás **által támogatott fióktípus-típusokat** . Ne **adjon meg** **átirányítási URI**-t. A különböző fióktípus leírását az [alkalmazás regisztrálása](quickstart-register-app.md)című témakörben tekintheti meg.
1. Az alkalmazás regisztrációjának létrehozásához válassza a **regisztráció** lehetőséget.

Ezután konfigurálja az alkalmazás regisztrációját egy **átirányítási URI** -val annak megadásához, hogy a Microsoft Identity platform hová irányítsa át az ügyfelet a biztonsági jogkivonatokkal együtt. Használja az alkalmazásban használt MSAL.js verziójának megfelelő lépéseket:

- [ 2,0MSAL.js az Auth Code flow-val](#redirect-uri-msaljs-20-with-auth-code-flow) (ajánlott)
- [MSAL.js 1,0 implicit folyamattal](#redirect-uri-msaljs-10-with-implicit-flow)

## <a name="redirect-uri-msaljs-20-with-auth-code-flow"></a>Átirányítási URI: [MSAL.js 2,0 az Auth Code flow-val](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)

A következő lépésekkel adhat hozzá átirányítási URI-t egy MSAL.js 2,0-es vagy újabb verziót használó alkalmazáshoz. A MSAL.js 2.0 és a PKCE és a CORS által biztosított engedélyezési kód a [böngésző harmadik féltől származó cookie-korlátozásokra](reference-third-party-cookies-spas.md)reagálva támogatja. Az implicit engedélyezési folyamat MSAL.js 2.0 + verzióban nem támogatott.

1. A Azure Portal válassza ki az alkalmazás [regisztrációjának létrehozásakor](#create-the-app-registration)korábban létrehozott regisztrációs alkalmazást.
1. A **kezelés** területen válassza   >  **a hitelesítés platform hozzáadása** elemet.
1. A **webalkalmazások** területen válassza az **egylapos alkalmazás** csempét.
1. Az **átirányítási** URI-k területen adjon meg egy [átirányítási URI](reply-url.md)-t. Ne **jelölje be** a jelölőnégyzetet az **implicit engedélyezés és a hibrid folyamatok** területen.
1. Válassza a **Konfigurálás** lehetőséget az átirányítási URI hozzáadásának befejezéséhez.

Ezzel befejezte az egyoldalas alkalmazás (SPA) regisztrációját, és konfigurált egy átirányítási URI-t, amelybe az ügyfél átirányítja az ügyfelet, és a rendszer minden biztonsági jogkivonatot elküld. Ha az átirányítási URI-t egy **egyoldalas alkalmazás** csempével konfigurálja a **platform hozzáadása** panelen, az alkalmazás regisztrációja úgy van konfigurálva, hogy támogassa az engedélyezési kódot a PKCE és a CORS használatával.

További útmutatásért kövesse az [oktatóanyagot](tutorial-v2-javascript-auth-code.md) .

## <a name="redirect-uri-msaljs-10-with-implicit-flow"></a>Átirányítási URI: [MSAL.js 1,0 implicit folyamattal](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)

A következő lépésekkel adhat hozzá átirányítási URI-t egy egyoldalas alkalmazáshoz, amely MSAL.js 1,3-es vagy korábbi verzióját, valamint az implicit engedélyezési folyamatot használja. A MSAL.js 1,3-es vagy korábbi verzióját használó alkalmazások nem támogatják az Auth-kód folyamatát.

1. A Azure Portal válassza ki az alkalmazás [regisztrációjának létrehozásakor](#create-the-app-registration)korábban létrehozott regisztrációs alkalmazást.
1. A **kezelés** területen válassza   >  **a hitelesítés platform hozzáadása** elemet.
1. A **webalkalmazások** területen válassza az **egylapos alkalmazás** csempét.
1. Az **átirányítási** URI-k területen adjon meg egy [átirányítási URI](reply-url.md)-t.
1. Az **implicit engedélyezés és a hibrid folyamatok** engedélyezése:
    - Ha az alkalmazás bejelentkezik a felhasználók között, válassza az **azonosító tokenek** lehetőséget.
    - Ha az alkalmazásnak egy védett webes API-t is meg kell hívnia, válassza a **hozzáférési jogkivonatok** lehetőséget. További információ ezekről a jogkivonat-típusokról: [azonosító jogkivonatok](id-tokens.md) és [hozzáférési tokenek](access-tokens.md).
1. Válassza a **Konfigurálás** lehetőséget az átirányítási URI hozzáadásának befejezéséhez.

Ezzel befejezte az egyoldalas alkalmazás (SPA) regisztrációját, és konfigurált egy átirányítási URI-t, amelybe az ügyfél átirányítja az ügyfelet, és a rendszer minden biztonsági jogkivonatot elküld. Egy vagy több **azonosító token** és **hozzáférési jogkivonat** kiválasztásával engedélyezte az implicit engedélyezési folyamatot.

További útmutatásért kövesse az [oktatóanyagot](tutorial-v2-javascript-spa.md) .

## <a name="note-about-authorization-flows"></a>Megjegyzés az engedélyezési folyamatokról

Alapértelmezés szerint egy egyoldalas alkalmazás-platform konfiguráció használatával létrehozott alkalmazás-regisztráció engedélyezi az engedélyezési kód folyamatát. Ennek a folyamatnak a kihasználásához az alkalmazásnak MSAL.js 2,0 vagy újabb verziót kell használnia.

Ahogy korábban említettük, a MSAL.js 1,3-et használó egylapos alkalmazások az implicit engedélyezési folyamatra korlátozódnak. A jelenlegi [OAuth 2,0 ajánlott eljárások](v2-oauth2-auth-code-flow.md) az engedélyezési kód folyamatának használatát ajánljuk a gyógyfürdők implicit folyamata helyett. A korlátozott élettartamú frissítési tokenek segítségével az alkalmazás alkalmazkodik a [modern böngésző cookie-i adatvédelmi korlátaihoz](reference-third-party-cookies-spas.md), például a Safari itp-hez.

Ha az alkalmazás regisztrációja által képviselt összes éles üzemi egyoldalas alkalmazás a MSAL.js 2,0 és az engedélyezési kód folyamatát használja, törölje a jelet az alkalmazás regisztrációjának hitelesítése ablaktáblán a Azure Portal az implicit engedélyezési beállítások **jelölőnégyzetből** . Az MSAL.js 1. x és az implicit folyamat használatával folytatott alkalmazások továbbra is működhetnek, azonban ha az implicit folyamat engedélyezve van (be van jelölve).

## <a name="next-steps"></a>Következő lépések

Az alkalmazás kódjának konfigurálásával használhatja az előző lépések során létrehozott regisztrációs alkalmazást: az [alkalmazás kódjának konfigurálása](scenario-spa-app-configuration.md).
