---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 66aaabb942e6296243aada9951a232496e7734e9
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843085"
---
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>A Microsoft Graph API meghívása egy JavaScript egyetlen lapon alkalmazásból (SPA)

Ez az útmutató azt ismerteti, hogyan bejelentkezhet egy JavaScript egyetlen lapon alkalmazás (SPA) személyes, munkahelyi és iskolai fiókokhoz, hozzáférési jogkivonatot kapjon, és a Microsoft Graph API vagy más hozzáférési jogkivonatok az Azure Active Directory v2 végpontot a szükséges API-hívás.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Ez az útmutató által létrehozott mintaalkalmazás működése

![Ez az útmutató által létrehozott mintaalkalmazás működése](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>További információ

Ez az útmutató által létrehozott alkalmazás lehetővé teszi, hogy egy JavaScript SPA lekérdezni a Microsoft Graph API vagy egy webes API-t, amely az Azure Active Directory v2 végpontot jogkivonatokat fogad. Ebben a forgatókönyvben egy felhasználó bejelentkezése után az, egy hozzáférési jogkivonatot, a kért és hozzáadni az engedélyezési fejléc via HTTP-kérelmekre. Token beszerzése és megújítása a Microsoft hitelesítési tár (MSAL) kezeli.

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Kódtárak

Ez az útmutató használja a következő könyvtárban:

|Erőforrástár|Leírás|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|A Microsoft Authentication Library for JavaScript-előzetes verzió|

> [!NOTE]
> *msal.js* célok a *Azure Active Directory v2 végpontot* – lehetővé teszi a személyes, iskolai és a munkahelyi fiókok jelentkezik be, és a jogkivonatok beszerzéséhez. A *Azure Active Directory v2 végpontot* rendelkezik [bizonyos korlátozások](..\articles\active-directory\develop\active-directory-v2-limitations.md).
> Olvassa el a v1 és v2 végpontok közötti különbségek megértése a [v1-v2 összehasonlítása](../articles/active-directory/develop/azure-ad-endpoint-comparison.md).

<!--end-collapse-->
