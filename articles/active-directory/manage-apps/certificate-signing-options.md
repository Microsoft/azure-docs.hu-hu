---
title: Speciális SAML-jogkivonat-tanúsítvány-aláírási beállítások Azure AD-alkalmazásokhoz
description: Ismerje meg, hogyan használhatja a speciális tanúsítvány-aláírási beállításokat az SAML-jogkivonatban az alkalmazásokban előre integrált Azure Active Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: iangithinji
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bb58f59b3796311f52d78ef87c8918f9888c152
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377870"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Speciális tanúsítvány-aláírási beállítások az SAML-jogkivonatban katalógusbeli alkalmazásokhoz a Azure Active Directory

Napjainkban Azure Active Directory (Azure AD) több ezer előre integrált alkalmazást támogat az Azure Active Directory App Galleryben. Több mint 500 alkalmazás támogatja az egyszeri bejelentkezést az [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 2.0 protokoll, például a [NetSuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) alkalmazás használatával. Amikor egy ügyfél SAML használatával hitelesít egy alkalmazást az Azure AD-n keresztül, az Azure AD egy jogkivonatot küld az alkalmazásnak (HTTP POST-on keresztül). Az alkalmazás ezután ellenőrzi és felhasználja a jogkivonatot az ügyfélbe való bejelentkezéshez a felhasználónév és jelszó kérése helyett. Ezek az SAML-jogkivonatok az Azure AD-ban és adott szabványos algoritmusokkal létrehozott egyedi tanúsítvánnyal vannak aláírva.

Az Azure AD néhány alapértelmezett beállítást használ a katalógusbeli alkalmazásokhoz. Az alapértelmezett értékek az alkalmazás követelményei alapján vannak beállítva.

Az Azure AD-ban beállíthatja a tanúsítvány-aláírási beállításokat és a tanúsítvány-aláíró algoritmust.

## <a name="certificate-signing-options"></a>Tanúsítvány-aláírási lehetőségek

Az Azure AD három tanúsítvány-aláírási lehetőséget támogat:

* **Írja alá az SAML helyességi feltételt.** Ez az alapértelmezett beállítás a legtöbb katalógusalkalmazáshoz be van állítva. Ha ezt a lehetőséget választja, az Azure AD mint identitásszolgáltató aláírja az SAML helyességi feltételt és a tanúsítványt az alkalmazás [X.509-tanúsítványával.](https://wikipedia.org/wiki/X.509)

* **Írja alá az SAML-választ.** Ha ezt a lehetőséget választja, az Azure AD mint internetszolgáltató aláírja az SAML-választ az alkalmazás X.509-tanúsítványával.

* **Írja alá az SAML-választ és a helyességi feltételt.** Ha ezt a lehetőséget választja, az Azure AD mint internetszolgáltató aláírja a teljes SAML-jogkivonatot az alkalmazás X.509-tanúsítványával.

## <a name="certificate-signing-algorithms"></a>Tanúsítvány-aláíró algoritmusok

Az Azure AD két aláíró algoritmust vagy biztonságos kivonat-algoritmust (SHA) támogat az SAML-válasz aláírásához:

* **SHA-256**. Az Azure AD ezzel az alapértelmezett algoritmussal írja alá az SAML-választ. Ez a legújabb algoritmus, és biztonságosabb, mint az SHA-1. A legtöbb alkalmazás támogatja az SHA-256 algoritmust. Ha egy alkalmazás csak az SHA-1 algoritmust támogatja aláíró algoritmusként, módosíthatja azt. Ellenkező esetben az SAML-válasz aláírásához az SHA-256 algoritmus használatát javasoljuk.

* **SHA-1**. Ez az algoritmus régebbi, és kevésbé biztonságos, mint az SHA-256. Ha egy alkalmazás csak ezt az aláíró algoritmust támogatja, ezt a lehetőséget az **aláíró** algoritmus legördülő listában választhatja ki. Az Azure AD ezután aláírja az SAML-választ az SHA-1 algoritmussal.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Tanúsítvány-aláírási beállítások és aláíró algoritmus módosítása

Egy alkalmazás SAML-tanúsítvány-aláírási beállításainak és a tanúsítvány-aláíró algoritmusnak a beállításához válassza ki a szóban forgó alkalmazást:

1. A [Azure Active Directory portálon](https://aad.portal.azure.com/)jelentkezzen be a fiókjába. Megjelenik **Azure Active Directory Felügyeleti központ** oldal.
1. A bal oldali panelen válassza ki a **Vállalati alkalmazások** elemet. Megjelenik a fiókban található vállalati alkalmazások listája.
1. Válasszon ki egy alkalmazást. Megjelenik az alkalmazás áttekintő oldala.

   ![Példa: Alkalmazás áttekintő oldala](./media/certificate-signing-options/application-overview-page.png)

Ezután módosítsa a tanúsítvány-aláírási beállításokat az alkalmazás SAML-jogkivonatában:

1. Az alkalmazás áttekintő oldalának bal oldali panelen válassza az **Egyszeri bejelentkezés lehetőséget.**
1. Ha **megjelenik a Set up Single Sign-On with SAML - Preview** (Egyetlen Sign-On SAML-sel – előzetes verzió) lap, lépjen az 5. lépésre.
1. Ha az **Egyszeri bejelentkezési** módszer kiválasztása lap nem jelenik meg, válassza az **Egyszeri** bejelentkezési módok módosítása lehetőséget az oldal megjelenítéséhez.
1. Az Egyszeri **bejelentkezési módszer** kiválasztása lapon válassza az **SAML** lehetőséget, ha elérhető. (Ha **az SAML** nem érhető el, az alkalmazás nem támogatja az SAML-t, és figyelmen kívül hagyhatja az eljárás és a cikk további részét.)
1. A **Set up Single Sign-On with SAML - Preview** (Egyetlen alkalmazás beállítása  SAMLSign-On előzetes verzióval) lapon keresse meg az **SAML aláíró** tanúsítvány fejlécét, és válassza a Szerkesztés ikont (ceruza). Megjelenik **az SAML aláíró tanúsítványa** lap.

   ![Példa: SAML-aláíró tanúsítvány oldala](./media/certificate-signing-options/saml-signing-page.png)

1. Az **Aláírási beállítás legördülő** listában válassza az SAML-válasz aláírása, **az SAML-helyességi** feltétel aláírása vagy az SAML-válasz és **-helyességi** feltétel aláírása lehetőséget.  Ezeknek a beállításoknak a leírásai a cikk korábbi, Tanúsítvány-aláírási [beállításainál jelennek meg.](#certificate-signing-options)
1. Az Aláíró **algoritmus legördülő** listában válassza az **SHA-1** vagy **az SHA-256 lehetőséget.** Ezeknek a beállításoknak a leírásai a cikk korábbi részében, a Tanúsítvány-aláíró algoritmusok [szakaszban jelennek](#certificate-signing-algorithms) meg.
1. Ha elégedett a választásokkal, válassza a Mentés **lehetőséget** az SAML-aláíró tanúsítvány új beállításainak alkalmazáshoz. Ellenkező esetben az **X-et választva** elvetheti a módosításokat.

## <a name="next-steps"></a>Következő lépések

* [Egyszeri bejelentkezés konfigurálása olyan alkalmazásokhoz, amelyek nem a Azure Active Directory App Galleryben](./configure-saml-single-sign-on.md)
* [Az SAML-alapú egyszeri bejelentkezés hibaelhárítása](./debug-saml-sso-issues.md)