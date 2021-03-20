---
title: Az MFA regisztrációs szabályzatának konfigurálása – Azure Active Directory Identity Protection
description: Ismerje meg, hogyan konfigurálhatja a Azure AD Identity Protection multi-Factor Authentication regisztrációs szabályzatot.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 072db1d47abd95844075aeedfeddc4f8cf6bf936
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94835866"
---
# <a name="how-to-configure-the-azure-ad-multi-factor-authentication-registration-policy"></a>Útmutató: az Azure AD Multi-Factor Authentication regisztrációs szabályzatának konfigurálása

Azure AD Identity Protection segít az Azure AD Multi-Factor Authentication (MFA) regisztrációjának kezelésében egy feltételes hozzáférési szabályzat konfigurálásával, amely az MFA-regisztráció megkövetelését írja elő, függetlenül attól, hogy milyen modern hitelesítési alkalmazásba jelentkezik be.

## <a name="what-is-the-azure-ad-multi-factor-authentication-registration-policy"></a>Mi az Azure AD Multi-Factor Authentication regisztrációs szabályzata?

Az Azure AD Multi-Factor Authentication segítségével ellenőrizheti, hogy ki használ-e egynél több felhasználónevet és jelszót. Egy második biztonsági réteget biztosít a felhasználói bejelentkezésekhez. Ahhoz, hogy a felhasználók képesek legyenek válaszolni az MFA-kérésekre, először regisztrálniuk kell az Azure AD-Multi-Factor Authentication.

Azt javasoljuk, hogy a felhasználói bejelentkezésekhez az Azure AD-Multi-Factor Authentication szükséges, mert:

- Erős hitelesítést biztosít számos ellenőrzési lehetőség használatával.
- Kulcsszerepet játszik abban, hogy a szervezet felkészítse az Identity Protection kockázati észlelési feladatait.

További információ az Azure AD Multi-Factor Authenticationről: [Mi az az Azure ad multi-Factor Authentication?](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>Szabályzatkonfiguráció

1. Navigáljon a [Azure Portal](https://portal.azure.com).
1. Tallózással keresse meg **Azure Active Directory**  >  **biztonsági**  >  **Identity Protection**  >  **MFA regisztrációs szabályzatát**.
   1. A **hozzárendelések** alatt
      1. **Felhasználók** – válassza a **minden felhasználó** lehetőséget **, vagy válassza az egyének és csoportok lehetőséget,** ha korlátozza a bevezetést.
         1. Opcionálisan dönthet úgy is, hogy kizárja a felhasználókat a szabályzatból.
   1. A **vezérlők** területen
      1. Győződjön meg arról, hogy az **Azure ad MFA-regisztráció megkövetelése** jelölőnégyzet be van jelölve, majd válassza a **kiválasztás** lehetőséget
   1. Szabályzat érvénybe **léptetése**  -  **Bekapcsolva**
   1. **Mentés**

## <a name="user-experience"></a>Felhasználó felület

Azure Active Directory Identity Protection fogja kérni a felhasználókat, hogy regisztrálják a következő bejelentkezést, amikor interaktív módon jelentkeznek be, és a regisztráció befejezéséhez 14 nap szükséges. Ez a 14 napos időszak során megkerülheti a regisztrációt, de az időszak végén, hogy a bejelentkezési folyamat elvégzése előtt regisztrálnia kell.

A kapcsolódó felhasználói élmény áttekintését lásd:

- A [Azure ad Identity Protectionekkel való bejelentkezési élmény](concept-identity-protection-user-experience.md).  

## <a name="next-steps"></a>Következő lépések

- [Bejelentkezési és felhasználói kockázati házirendek engedélyezése](howto-identity-protection-configure-risk-policies.md)

- [Az Azure AD önkiszolgáló jelszó-visszaállítás engedélyezése](../authentication/howto-sspr-deployment.md)

- [Az Azure AD többtényezős hitelesítés engedélyezése](../authentication/howto-mfa-getstarted.md)
