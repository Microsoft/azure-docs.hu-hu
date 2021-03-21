---
title: Speciális SAML-jogkivonat tanúsítvány-aláírási lehetőségei az Azure AD-alkalmazásokhoz
description: Megtudhatja, hogyan használhatja a speciális tanúsítvány-aláírási lehetőségeket az SAML-jogkivonatban az előre integrált alkalmazások Azure Active Directory
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: kenwith
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9dd6a7a34ac231f8af9d08aab99e3d552f33ea1d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259621"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Speciális tanúsítvány-aláírási beállítások az SAML-jogkivonatban a Gallery-alkalmazásokhoz Azure Active Directory

Ma Azure Active Directory (Azure AD) több ezer előre integrált alkalmazást támogat a Azure Active Directory app Galleryben. Az alkalmazások több mint 500 támogatja az egyszeri bejelentkezést az [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 2,0 protokoll, például a [NetSuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) alkalmazás használatával. Amikor az ügyfél az SAML használatával hitelesít egy alkalmazást az Azure AD-n keresztül, az Azure AD tokent küld az alkalmazásnak (HTTP-POSTon keresztül). Az alkalmazás ezután ellenőrzi és használja a jogkivonatot, hogy bejelentkezzen az ügyfélen a Felhasználónév és a jelszó kérése helyett. Ezek az SAML-tokenek az Azure AD-ben generált egyedi tanúsítvánnyal és meghatározott szabványos algoritmusokkal vannak aláírva.

Az Azure AD a katalógus alkalmazásaihoz tartozó alapértelmezett beállításokat használja. Az alapértelmezett értékeket az alkalmazás követelményei alapján állítja be a rendszer.

Az Azure AD-ben beállíthat tanúsítvány-aláírási beállításokat és a tanúsítvány-aláírási algoritmust.

## <a name="certificate-signing-options"></a>Tanúsítvány-aláírási lehetőségek

Az Azure AD három tanúsítvány-aláírási lehetőséget támogat:

* **SAML-állítás aláírása**. Ez az alapértelmezett beállítás a legtöbb katalógus-alkalmazás esetében be van állítva. Ha ezt a lehetőséget választja, az Azure AD (identitásszolgáltató) azonosítja az SAML-kijelentést és a tanúsítványt az alkalmazás [X. 509](https://wikipedia.org/wiki/X.509) tanúsítványával.

* **SAML-válasz aláírása**. Ha ezt a lehetőséget választja, az Azure AD-t identitásszolgáltató aláírja az SAML-választ az alkalmazás X. 509 tanúsítványával.

* **SAML-válasz és-érvényesítés aláírása**. Ha ezt a lehetőséget választja, az Azure AD identitásszolgáltató a teljes SAML-tokent az alkalmazás X. 509 tanúsítványával aláírja.

## <a name="certificate-signing-algorithms"></a>Tanúsítvány-aláírási algoritmusok

Az Azure AD két aláírási algoritmust vagy biztonságos kivonatoló algoritmust (SHA) támogat az SAML-válasz aláírásához:

* **SHA-256**. Az Azure AD ezt az alapértelmezett algoritmust használja az SAML-válasz aláírásához. Ez a legújabb algoritmus, és biztonságosabb, mint az SHA-1. Az alkalmazások többsége támogatja az SHA-256 algoritmust. Ha egy alkalmazás csak az SHA-1 algoritmust támogatja, akkor módosíthatja azt. Ellenkező esetben javasoljuk, hogy az SAML-válasz aláírásához használja az SHA-256 algoritmust.

* **SHA-1**. Ez az algoritmus régebbi, és kevésbé biztonságos, mint az SHA-256. Ha egy alkalmazás csak ezt az aláírási algoritmust támogatja, ezt a lehetőséget az **aláírási algoritmus** legördülő listájában választhatja ki. Az Azure AD ezután aláírja az SAML-választ az SHA-1 algoritmussal.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Tanúsítvány-aláírási beállítások és aláírási algoritmus módosítása

Az alkalmazás SAML-tanúsítvány-aláírási beállításainak és a tanúsítvány-aláírási algoritmus módosításához válassza ki a szóban forgó alkalmazást:

1. A [Azure Active Directory portálon](https://aad.portal.azure.com/)jelentkezzen be a fiókjába. Megjelenik a **Azure Active Directory felügyeleti központ** lap.
1. A bal oldali panelen válassza ki a **Vállalati alkalmazások** elemet. Megjelenik a fiókjában a vállalati alkalmazások listája.
1. Válasszon ki egy alkalmazást. Megjelenik az alkalmazás áttekintő lapja.

   ![Példa: alkalmazás – Áttekintés lap](./media/certificate-signing-options/application-overview-page.png)

Ezután módosítsa az alkalmazás SAML-jogkivonatának tanúsítvány-aláírási beállításait:

1. Az alkalmazás – Áttekintés lap bal oldali ablaktábláján válassza az **egyszeri bejelentkezés** lehetőséget.
1. Ha az **egyszeri Sign-On beállítása SAML-előnézettel** lap jelenik meg, ugorjon az 5. lépésre.
1. Ha az **egyszeri bejelentkezési módszer kiválasztása** lap nem jelenik meg, válassza az **egyszeri bejelentkezési módok módosítása** lehetőséget az oldal megjelenítéséhez.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget, ha elérhető. (Ha az **SAML** nem érhető el, az alkalmazás nem támogatja az SAML használatát, és figyelmen kívül hagyhatja az eljárás és a cikk további részét.)
1. Az **egyszeri Sign-On beállítása az SAML-előnézettel** lapon keresse meg az **SAML-aláíró tanúsítvány** fejlécét, és válassza a **Szerkesztés** ikont (egy ceruza). Megjelenik az **SAML aláíró tanúsítvány** lapja.

   ![Példa: SAML aláíró tanúsítvány lapja](./media/certificate-signing-options/saml-signing-page.png)

1. Az **aláírási lehetőség** legördülő listában válassza az **SAML-válasz aláírása**, az **SAML-érvényesítés** aláírása vagy az SAML- **Válasz és-állítás aláírása** lehetőséget. Ezen beállítások leírása a jelen cikk korábbi részében jelenik meg a [tanúsítvány-aláírási beállításokban](#certificate-signing-options).
1. Az **aláírási algoritmus** legördülő listában válassza az **SHA-1** vagy az **SHA-256** elemet. Ezen beállítások leírása a jelen cikk korábbi, a tanúsítvány- [aláírási algoritmusok](#certificate-signing-algorithms) szakaszban található.
1. Ha elégedett a lehetőségeivel, válassza a **Mentés** lehetőséget az új SAML-aláíró tanúsítvány beállításainak alkalmazásához. Ellenkező esetben válassza az **X-et** a módosítások elvetéséhez.

## <a name="next-steps"></a>Következő lépések

* [Egyszeri bejelentkezés konfigurálása olyan alkalmazásokhoz, amelyek nem szerepelnek a Azure Active Directory app Galleryben](./configure-saml-single-sign-on.md)
* [Az SAML-alapú egyszeri bejelentkezés hibaelhárítása](./debug-saml-sso-issues.md)