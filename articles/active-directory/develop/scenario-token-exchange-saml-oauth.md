---
title: Microsoft Identity platform token Exchange-forgatókönyv SAML-vel és OIDC/OAuth-mel Azure Active Directory
description: Ismerkedjen meg a gyakori jogkivonat-Exchange-forgatókönyvekkel, amikor az SAML-t és a OIDC-t/OAuth-t a Azure Active Directory
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.openlocfilehash: 812bf7bd68362667fcd1b636d0d28fdbb21c1409
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99582349"
---
# <a name="microsoft-identity-platform-token-exchange-scenarios-with-saml-and-oidcoauth"></a>Microsoft Identity platform token Exchange-forgatókönyvek SAML-vel és OIDC/OAuth

Az SAML és az OpenID Connect (OIDC)/OAuth az egyetlen Sign-On (SSO) megvalósításához használt népszerű protokollok. Egyes alkalmazások csak az SAML megvalósítását tehetik meg, mások pedig csak a OIDC/OAuth implementálják. Mindkét protokoll jogkivonatokat használ a titkok közlésére. További információ az SAML-ról: [Single Sign-On SAML protokoll](single-sign-on-saml-protocol.md). További információ a OIDC/OAuth: [OAuth 2,0 és OpenID Connect protokollok a Microsoft Identity platformon](active-directory-v2-protocols.md).

Ez a cikk egy olyan gyakori forgatókönyvet vázol fel, amelyben az alkalmazás SAML-t valósít meg, de meghívja a Graph API, amely a OIDC/OAuth-t használja. Az ebben a forgatókönyvben dolgozó személyek számára alapvető útmutatást biztosítunk.

## <a name="scenario-you-have-a-saml-token-and-want-to-call-the-graph-api"></a>Forgatókönyv: SAML-jogkivonattal rendelkezik, és meg szeretné hívni a Graph API
Számos alkalmazás az SAML-vel lett implementálva. A Graph API azonban a OIDC/OAuth protokollt használja. A OIDC/OAuth funkciók egy SAML-alkalmazásba való felvételének lehetséges, bár nem triviális. Ha a OAuth funkció elérhető egy alkalmazásban, a Graph API használható.

Az általános stratégia az OIDC-/OAuth-verem hozzáadása az alkalmazáshoz. A mindkét szabványt megvalósító alkalmazással munkamenet-cookie-t is használhat. Nem kell explicit módon cserélnie a jogkivonatot. Az SAML használatával naplóz egy felhasználót, amely létrehoz egy munkamenet-cookie-t. Amikor a Graph API meghívja a OAuth folyamatot, a rendszer a munkamenet-cookie-t használja a hitelesítéshez. Ez a stratégia feltételezi, hogy a feltételes hozzáférés ellenőrzése sikeres, a felhasználó pedig jogosult.

> [!NOTE]
> A OIDC/OAuth viselkedés hozzáadására javasolt függvénytár a Microsoft Authentication Library (MSAL). További információ a MSAL: [a Microsoft Authentication Library (MSAL) áttekintése](msal-overview.md). Az előző függvénytár neve Active Directory-hitelesítési tár (ADAL) volt, azonban nem ajánlott, mert a MSAL lecseréli.

## <a name="next-steps"></a>Következő lépések
- [Hitelesítési folyamatok és alkalmazási helyzetek](authentication-flows-app-scenarios.md)
