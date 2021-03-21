---
title: Hitelesítés
description: Elmagyarázza, hogyan működik a hitelesítés?
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: how-to
ms.openlocfilehash: 35fd78a9d55dc684045fdb4b83691c1613801421
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97724881"
---
# <a name="configure-authentication"></a>A hitelesítés konfigurálása

Az Azure távoli renderelés ugyanazt a hitelesítési mechanizmust használja, mint az [Azure térbeli horgonyok (ASA)](../../spatial-anchors/concepts/authentication.md?tabs=csharp). Az ügyfeleknek a következők *egyikét* kell megadniuk a REST API-k sikeres meghívásához:

* **AccountKey**: a Azure Portal távoli renderelési fiókjához tartozó Keys (kulcsok) lapon szerezhető be. A fiók kulcsai csak fejlesztési/prototípus-készítéshez ajánlottak.
    ![Fiókazonosító](./media/azure-account-primary-key.png)

* **AccountDomain**: a távoli megjelenítési fiók "áttekintés" lapján olvasható be a Azure Portal.
    ![Fiók tartománya](./media/azure-account-domain.png)

* **AuthenticationToken**: egy Azure ad-jogkivonat, amelyet a [MSAL-könyvtár](../../active-directory/develop/msal-overview.md)használatával lehet beszerezni. Több különböző folyamat is elérhető a felhasználói hitelesítő adatok elfogadásához és a hitelesítő adatok használatával hozzáférési jogkivonat beszerzéséhez.

* **MRAccessToken**: egy Mr token, amely az Azure Mixed Reality biztonságijogkivonat-szolgáltatás (STS) használatával szerezhető be. A `https://sts.<accountDomain>` végpontról a következőhöz hasonló Rest-hívással lehet lekérni:

    ```rest
    GET https://sts.southcentralus.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
    Host: sts.southcentralus.mixedreality.azure.com
    Connection: Keep-Alive

    HTTP/1.1 200 OK
    Date: Tue, 24 Mar 2020 09:09:00 GMT
    Content-Type: application/json; charset=utf-8
    Content-Length: 1153
    Accept: application/json
    MS-CV: 05JLqWeKFkWpbdY944yl7A.0
    {"AccessToken":"eyJhbGciOiJSUzI1<truncated>uLkO2FvA"}
    ```

    Ahol az engedélyezési fejléc a következőképpen van formázva: `Bearer <Azure_AD_token>` vagy `Bearer <accoundId>:<accountKey>` . Az előző a biztonság számára ajánlott. A REST-hívás által visszaadott jogkivonat az MR hozzáférési jogkivonat.

## <a name="authentication-for-deployed-applications"></a>Hitelesítés telepített alkalmazásokhoz

A fiók kulcsai a gyors prototípusokhoz ajánlottak, csak a fejlesztés során. Azt javasoljuk, hogy az alkalmazást az éles környezetben lévő beágyazott fiók használatával ne szállítsa az éles környezetbe. Az ajánlott módszer a felhasználó-vagy a szolgáltatáson alapuló Azure AD-alapú hitelesítési módszer használata.

 Az Azure AD-hitelesítést az Azure [térbeli horgonyok (ASA)](../../spatial-anchors/index.yml) szolgáltatás [Azure ad felhasználói hitelesítés](../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-user-authentication) szakasza ismerteti.

 További információkért tekintse meg a következő [oktatóanyagot: az Azure Remote rendering és a Model Storage biztonságossá tétele – Azure Active Directory hitelesítés](../tutorials/unity/security/security.md#azure-active-directory-azure-ad-authentication)

## <a name="azure-role-based-access-control"></a>Azure-beli szerepköralapú hozzáférés-vezérlés

A szolgáltatáshoz biztosított hozzáférés szintjének szabályozása érdekében a szerepköralapú hozzáférés megadásakor használja az alábbi szerepköröket:

* **Távoli renderelési rendszergazda**: lehetővé teszi a felhasználó számára a konverziót, a munkamenetek kezelését, a renderelést és a diagnosztikai képességeket az Azure távoli rendereléshez.
* **Távoli renderelési ügyfél**: a felhasználók számára biztosít munkamenet-, renderelési és diagnosztikai képességeket az Azure távoli rendereléshez.

## <a name="next-steps"></a>Következő lépések

* [Fiók létrehozása](create-an-account.md)
* [Az Azure frontend API-k használata a hitelesítéshez](frontend-apis.md)
* [PowerShell-példaszkriptek](../samples/powershell-example-scripts.md)