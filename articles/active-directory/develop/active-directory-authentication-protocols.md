---
title: Microsoft Identity platform hitelesítési protokollok
description: A Microsoft Identity platform által támogatott hitelesítési protokollok áttekintése
author: rwike77
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ROBOTS: NOINDEX
ms.openlocfilehash: 803f52666fb09a836eae3294410584ebbd71a1b6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98755887"
---
# <a name="microsoft-identity-platform-authentication-protocols"></a>Microsoft Identity platform hitelesítési protokollok

A Microsoft Identity platform számos, a legszélesebb körben használt hitelesítési és engedélyezési protokollt támogat. A szakasz témakörei a támogatott protokollokat és azok megvalósítását írják le a Microsoft Identity platformon. A témakörökbe beletartozik a támogatott jogcím-típusok áttekintése, az összevonási metaadatok használatának bemutatása, részletes OAuth 2,0. és a SAML 2,0 protokoll dokumentációját, valamint egy hibaelhárítási szakaszt.

## <a name="authentication-protocols-articles-and-reference"></a>A hitelesítési protokollok cikkei és referenciái

* [Fontos információk a kulcs átváltásáról a Microsoft Identity platformon](active-directory-signing-key-rollover.md) – további információ a Microsoft Identity platform aláírási kulcsának átváltási ritmusáról, a kulcs automatikus frissítéséhez szükséges módosítások, valamint a leggyakoribb alkalmazás-forgatókönyvek frissítésének ismertetése.
* [Támogatott jogkivonatok és jogcímek](id-tokens.md) – megismerheti a Microsoft Identity platform által felmerülő jogkivonatokban lévő jogcímeket.
* [OAuth 2,0 a Microsoft Identity platformon](v2-oauth2-auth-code-flow.md) – Ismerje meg a OAuth 2,0 megvalósítását a Microsoft Identity platformon.
* [OpenID Connect 1,0](v2-protocols-oidc.md) – Ismerje meg, hogyan használható az OAuth 2,0, hitelesítési protokoll a hitelesítéshez.
* [Szolgáltatások közötti hívások ügyfél-hitelesítő adatokkal](v2-oauth2-client-creds-grant-flow.md) – megtudhatja, hogyan használhatja a OAuth 2,0-ügyfél hitelesítő adatait a szolgáltatás és a szolgáltatás közötti hívások megadására.
* [A szolgáltatás és a szolgáltatás közötti hívások hívása folyamaton](v2-oauth2-on-behalf-of-flow.md) keresztül – megtudhatja, hogyan használhatja a OAuth 2,0-et a szolgáltatás és a szolgáltatás közötti hívásokhoz.
* [SAML protokoll referenciája](active-directory-saml-protocol-reference.md) – megismerheti a Microsoft Identity platform egyetlen Sign-On és egyszeri kijelentkezési SAML-profilját.

## <a name="see-also"></a>Lásd még

* [A Microsoft Identity platform áttekintése](v2-overview.md)
* [Active Directory kód mintái](sample-v2-code.md)
