---
title: Az OpenID Connecttel való regisztráció és bejelentkezés beállítása
titleSuffix: Azure AD B2C
description: A regisztráció és bejelentkezés beállítása bármely OpenID Connect Identity providerrel (identitásszolgáltató) Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 21f31a21921953148ae0438cee04d846706161cc
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382929"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Az OpenID Connecttel való regisztráció és bejelentkezés beállítása Azure Active Directory B2C használatával

Az [OpenID Connect](openid-connect.md) egy OAuth 2,0-re épülő hitelesítési protokoll, amely biztonságos felhasználói bejelentkezéshez használható. A protokollt használó legtöbb identitás-szolgáltató Azure AD B2C támogatott. Ez a cikk azt ismerteti, hogyan adhat hozzá egyéni OpenID Connect-szolgáltatókat a felhasználói folyamatokhoz.

## <a name="add-the-identity-provider"></a>Az identitás-szolgáltató hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy az Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben kattintson a **könyvtár + előfizetés** szűrőre, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza az **identitás-szolgáltatók**, majd az **új OpenID Connect Provider** lehetőséget.
1. Adjon meg egy **nevet**. Adja meg például a *contoso* értéket.

## <a name="configure-the-identity-provider"></a>Az identitás-szolgáltató konfigurálása

Minden OpenID Connect Identity szolgáltató olyan metaadat-dokumentumot ír le, amely a bejelentkezéshez szükséges információk nagy részét tartalmazza. Ide tartozik például a használni kívánt URL-címek és a szolgáltatás nyilvános aláíró kulcsainak helye. Az OpenID Connect metaadat-dokumentum mindig olyan végponton található, amely a-ben végződik `.well-known/openid-configuration` . A hozzáadni kívánt OpenID Connect-szolgáltatóhoz adja meg a metaadatok URL-címét.

## <a name="client-id-and-secret"></a>Ügyfél-azonosító és titok

Annak engedélyezéséhez, hogy a felhasználók bejelentkezzenek, az identitás-szolgáltató megköveteli a fejlesztőknek, hogy regisztráljanak egy alkalmazást a szolgáltatásban. Az alkalmazáshoz tartozik egy **ügyfél-azonosító** és egy **ügyfél-titok** néven ismert azonosító. Másolja ezeket az értékeket az identitás-szolgáltatóból, és írja be őket a megfelelő mezőkbe.

> [!NOTE]
> Az ügyfél titkos kulcsa nem kötelező. Ha azonban az [engedélyezési kód folyamatát](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)szeretné használni, meg kell adnia egy ügyfél titkos kulcsát, amely a titkos kódot használja a jogkivonat kódjának cseréjéhez.

## <a name="scope"></a>Hatókör

A hatókör határozza meg az identitás-szolgáltatótól begyűjtött információkat és engedélyeket, például: `openid profile` . Ahhoz, hogy az azonosító tokent megkapja az identitás-szolgáltatótól, `openid` meg kell adni a hatókört. Az azonosító jogkivonat nélkül a felhasználók nem tudnak bejelentkezni a Azure AD B2Cra az egyéni identitás-szolgáltató használatával. Más hatóköröket szóközzel lehet elválasztani egymástól. Tekintse át az egyéni identitás szolgáltatójának dokumentációját, amelyből megtudhatja, hogy milyen egyéb hatókörök is elérhetők.

## <a name="response-type"></a>Válasz típusa

A válasz típusa azt írja le, hogy milyen típusú információkat küld vissza a rendszer az `authorization_endpoint` egyéni identitás-szolgáltató kezdeti hívását. A következő típusú válaszokat lehet használni:

* `code`: Az [engedélyezési kód folyamata](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)során a rendszer visszaadja a kódot Azure ad B2C. Azure AD B2C folytatja a meghívását a `token_endpoint` tokenhez tartozó kód cseréjére.
* `id_token`: A rendszer visszaadja az azonosító jogkivonatot az egyéni identitás-szolgáltató Azure AD B2C.

## <a name="response-mode"></a>Válasz mód

A válasz mód határozza meg azt a módszert, amelyet az adatoknak az egyéni identitás szolgáltatótól a Azure AD B2Cba való visszaküldéséhez kell használni. A következő módokat használhatja:

* `form_post`: Ez a válasz mód ajánlott a legjobb biztonság érdekében. A válasz továbbítása a HTTP `POST` -metódussal történik, és a kódot vagy a tokent a törzsbe kódolja a `application/x-www-form-urlencoded` formátum használatával.
* `query`: A kód vagy token lekérdezési paraméterként lesz visszaadva.

## <a name="domain-hint"></a>Tartományi tipp

A tartomány-emlékeztetővel közvetlenül a megadott Identity Provider bejelentkezési lapjára ugorhat, így a felhasználó nem választhat a rendelkezésre álló identitás-szolgáltatók listáján. Az ilyen viselkedés engedélyezéséhez adjon meg egy értéket a tartományhoz. Az egyéni identitás-szolgáltatóra való ugráshoz fűzze hozzá a (z) paramétert a `domain_hint=<domain hint value>` kérés végéhez, amikor meghívja Azure ad B2C a bejelentkezéshez.

## <a name="claims-mapping"></a>Jogcím-hozzárendelés

Miután az egyéni identitás-szolgáltató visszaküldi az azonosító jogkivonatot a Azure AD B2Cnak, Azure AD B2Cnak képesnek kell lennie a kapott token jogcímeinek hozzárendelésére a Azure AD B2C által felismert és használt jogcímekre. A következő leképezések mindegyike esetében tekintse át az egyéni identitás szolgáltatójának dokumentációját, és Ismerje meg, hogy milyen jogcímeket ad vissza az identitás-szolgáltató jogkivonatában:

* **Felhasználói azonosító**: adja meg a bejelentkezett felhasználó *egyedi azonosítóját* biztosító jogcímet.
* **Megjelenítendő név**: adja meg azt a jogcímet, amely megadja a felhasználó *megjelenítendő nevét* vagy *teljes nevét* .
* **Utónév: adja** meg a *felhasználó vezetéknevét* biztosító jogcímet.
* **Vezetéknév**: adja meg a felhasználó *vezetéknevét* biztosító jogcímet.
* **E-mail**: adja meg a felhasználó *e-mail-címét* biztosító jogcímet.

## <a name="add-the-identity-provider-to-a-user-flow"></a>Az identitás-szolgáltató hozzáadása felhasználói folyamathoz 

1. A Azure AD B2C-bérlőben válassza a **felhasználói folyamatok** lehetőséget.
1. Kattintson arra a felhasználói folyamatra, amelyhez hozzá szeretné adni az identitás-szolgáltatót. 
1. A **közösségi identitás-szolgáltatók** területen válassza ki a hozzáadott identitás-szolgáltatót. Például: *contoso*.
1. Kattintson a **Mentés** gombra.
1. A szabályzat teszteléséhez válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Az **alkalmazás** lapon válassza ki a korábban regisztrált *testapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **felhasználói folyamat futtatása** gombra.
1. A regisztrációs vagy bejelentkezési oldalon válassza ki a bejelentkezni kívánt identitás-szolgáltatót. Például: *contoso*.

Ha a bejelentkezési folyamat sikeres, a rendszer átirányítja a böngészőt `https://jwt.ms` , amely a Azure ad B2C által visszaadott jogkivonat tartalmát jeleníti meg.
