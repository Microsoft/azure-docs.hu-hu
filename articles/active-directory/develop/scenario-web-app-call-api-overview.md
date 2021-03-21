---
title: Webes API-kat meghívó Webalkalmazás létrehozása | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre webes API-kat meghívó webalkalmazást (áttekintés)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1fdbdada54320ef28f6a4b04a7f415c835acc9dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98756291"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>Forgatókönyv: webes API-kat meghívó webalkalmazás

Megtudhatja, hogyan hozhat létre olyan webalkalmazást, amely aláírja a felhasználókat a Microsoft Identity platformon, majd meghívja a webes API-kat a bejelentkezett felhasználó nevében.

## <a name="prerequisites"></a>Előfeltételek

Ez a forgatókönyv feltételezi, hogy már elvégezte [a következő forgatókönyvet: webalkalmazás, amely bejelentkezik a felhasználók számára](scenario-web-app-sign-user-overview.md).

## <a name="overview"></a>Áttekintés

A webalkalmazáshoz hozzá kell adnia a hitelesítést, így a felhasználók a bejelentkezett felhasználó nevében is bejelentkezhetnek a webes API-ra.

![Webes API-kat hívó webalkalmazás](./media/scenario-webapp/web-app.svg)

A webes API-kat meghívó webalkalmazások bizalmas ügyfélalkalmazások.
Ezért regisztrálják a titkos kódot (az alkalmazás jelszavát vagy tanúsítványát) Azure Active Directory (Azure AD). Ezt a titkot a rendszer átadja az Azure AD-nek a jogkivonat beszerzésére irányuló hívásakor.

## <a name="specifics"></a>Sajátosságai

> [!NOTE]
> Ha a bejelentkezést egy webalkalmazásba helyezi, a webalkalmazás védelmét is maga a szolgáltatás védi. Ez a védelem *middleware* -kódtárak, nem a Microsoft Authentication Library (MSAL) használatával érhető el. Az előző forgatókönyv, a [webalkalmazás, amely aláírja a felhasználókat](scenario-web-app-sign-user-overview.md), a tárgyat lefedi.
>
> Ez a forgatókönyv ismerteti, hogyan hívhat webes API-kat egy webalkalmazásból. Ehhez a webes API-khoz hozzáférési jogkivonatokat kell beszereznie. Ezeket a jogkivonatokat a MSAL-kódtárak használatával szerzi be.

Ehhez a forgatókönyvhöz a következő konkrét feladatok szükségesek:

- Az [alkalmazás regisztrálása](scenario-web-app-call-api-app-registration.md)során meg kell adnia egy válasz URI-t, titkos kulcsot vagy tanúsítványt az Azure ad-vel való megosztáshoz. Ha az alkalmazást több helyre is telepíti, minden helyhez meg kell adnia egy válasz URI-t.
- Az [alkalmazás-konfigurációnak](scenario-web-app-call-api-app-configuration.md) meg kell adnia az Azure ad-vel megosztott ügyfél-hitelesítő adatokat az alkalmazás regisztrálása során.

## <a name="recommended-reading"></a>Ajánlott olvasás

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Következő lépések

Lépjen tovább a forgatókönyv következő cikkére, amely az [alkalmazás regisztrálását](scenario-web-app-call-api-app-registration.md)ismerteti.