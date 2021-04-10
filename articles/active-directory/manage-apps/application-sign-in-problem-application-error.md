---
title: Hibaüzenet jelenik meg az alkalmazás oldalán a bejelentkezés után | Microsoft Docs
description: Az Azure AD-bejelentkezéssel kapcsolatos hibák elhárítása, amikor az alkalmazás hibaüzenetet ad vissza.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c96209f33491645510d8592997c418472d4f227c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258812"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>Hibaüzenetet megjelenítő alkalmazásoldal jelenik meg, miután a felhasználó bejelentkezett

Ebben az esetben Azure Active Directory (Azure AD) aláírja a felhasználót a alkalmazásban. Az alkalmazás azonban hibaüzenetet jelenít meg, és nem teszi lehetővé, hogy a felhasználó befejezze a bejelentkezési folyamatot. A probléma az, hogy az alkalmazás nem fogadta el az Azure AD által kiadott választ.

Számos lehetséges oka van annak, hogy az alkalmazás miért nem fogadta el a választ az Azure AD-től. Ha a hibaüzenet nem azonosítja egyértelműen a válaszból hiányzókat, próbálkozzon a következőkkel:

-   Ha az alkalmazás az Azure AD-katalógus, ellenőrizze, hogy követte-e az [SAML-alapú egyszeri bejelentkezés az Azure ad-ben való hibakeresésének](./debug-saml-sso-issues.md)lépéseit.

-   Használjon olyan eszközt, mint a [Hegedűs](https://www.telerik.com/fiddler) , és rögzítse az SAML-kérelmet, a választ és a tokent.

-   Küldje el az SAML-választ az alkalmazás forgalmazójától, és kérje meg, hogy mi hiányzik.

## <a name="attributes-are-missing-from-the-saml-response"></a>Hiányzó attribútumok az SAML-válaszból

Az Azure ad-válaszban elküldeni kívánt attribútum hozzáadásához kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be globális rendszergazdaként vagy társ-rendszergazdaként.

2. A bal oldali navigációs ablaktábla tetején a **minden szolgáltatás** elemre kattintva nyissa meg az Azure ad-bővítményt.

3. Írja be a **Azure Active Directory** kifejezést a szűrő keresőmezőbe, majd válassza a **Azure Active Directory** lehetőséget.

4. Válassza a **vállalati alkalmazások** lehetőséget az Azure ad navigációs ablaktábláján.

5. Az alkalmazások listájának megtekintéséhez kattintson a **minden alkalmazás** elemre.

   > [!NOTE]
   > Ha nem látja a kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet. Állítsa a **show (megjelenítés** ) lehetőséget a "minden alkalmazás" értékre.

6. Válassza ki az egyszeri bejelentkezéshez konfigurálni kívánt alkalmazást.

7. Az alkalmazás betöltése után válassza az **egyszeri bejelentkezés** lehetőséget a navigációs ablaktáblán.

8. A **felhasználói attribútumok** szakaszban válassza az **összes többi felhasználói attribútum megtekintése és szerkesztése** lehetőséget. Itt módosíthatja, hogy mely attribútumok legyenek elküldve az alkalmazásnak az SAML-jogkivonatban, amikor a felhasználók bejelentkeznek.

   Attribútum hozzáadása:

   1. Válassza az **attribútum hozzáadása** elemet. Adja meg a **nevet**, és válassza ki az **értéket** a legördülő listából.

   1.  Kattintson a **Mentés** gombra. Ekkor megjelenik az új attribútum a táblában.

9. Mentse a konfigurációt.

   Amikor a felhasználó legközelebb bejelentkezik az alkalmazásba, az Azure AD az SAML-válaszban fogja elküldeni az új attribútumot.

## <a name="the-app-doesnt-identify-the-user"></a>Az alkalmazás nem azonosítja a felhasználót

Az alkalmazásba való bejelentkezés sikertelen, mert az SAML-válaszból hiányzik egy attribútum, például egy szerepkör. Vagy sikertelen, mert az alkalmazás eltérő formátumot vagy értéket vár a **NameID** (felhasználói azonosító) attribútumhoz.

Ha az [Azure ad automatizált felhasználói üzembe](../app-provisioning/user-provisioning.md) helyezését használja az alkalmazásban lévő felhasználók létrehozására, karbantartására és eltávolítására, ellenőrizze, hogy a felhasználó ki lett-e építve az SaaS-alkalmazásba. További információ: [egyetlen felhasználó sincs kiépítve egy Azure ad Gallery-alkalmazásba](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md).

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Attribútum hozzáadása az Azure AD-alkalmazás konfigurációjához

A felhasználói azonosító értékének módosításához kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be globális rendszergazdaként vagy társ-rendszergazdaként.

2. Az Azure AD-bővítmény megnyitásához kattintson a bal oldalon található navigációs ablaktábla tetején található **összes szolgáltatás** elemre.

3. Írja be a **Azure Active Directory** kifejezést a szűrő keresőmezőbe, majd válassza a **Azure Active Directory** lehetőséget.

4. Válassza a **vállalati alkalmazások** lehetőséget az Azure ad navigációs ablaktábláján.

5. Az alkalmazások listájának megtekintéséhez kattintson a **minden alkalmazás** elemre.

   > [!NOTE]
   > Ha nem látja a kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet. Állítsa a **show (megjelenítés** ) lehetőséget a "minden alkalmazás" értékre.

6. Válassza ki az egyszeri bejelentkezéshez konfigurálni kívánt alkalmazást.

7. Az alkalmazás betöltése után válassza az **egyszeri bejelentkezés** lehetőséget a navigációs ablaktáblán.

8. A **felhasználói attribútumok** területen válassza ki a felhasználó egyedi azonosítóját a **felhasználói azonosító** legördülő listából.

## <a name="change-the-nameid-format"></a>NameID formátumának módosítása

Ha az alkalmazás egy másik formátumot vár a **NameID** (felhasználóazonosító) attribútumhoz, tekintse meg a [NameID szerkesztése](../develop/active-directory-saml-claims-customization.md#editing-nameid) című témakört a NameID formátumának módosításához.

Az Azure AD kiválasztja a **NameID** attribútum (felhasználói azonosító) formátumát a kiválasztott érték vagy az SAML-AuthRequest alkalmazás által kért formátum alapján. További információ: az [egyszeri bejelentkezéses SAML protokoll](../develop/single-sign-on-saml-protocol.md#nameidpolicy)"NameIDPolicy" szakasza.

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>Az alkalmazás eltérő aláírási módszert vár az SAML-válaszhoz

Az alábbi lépéseket követve módosíthatja, hogy az SAML-jogkivonat mely részeit digitálisan aláírta az Azure AD:

1. Nyissa meg a [Azure Portal](https://portal.azure.com/) , és jelentkezzen be globális rendszergazdaként vagy társ-rendszergazdaként.

2. Az Azure AD-bővítmény megnyitásához kattintson a bal oldalon található navigációs ablaktábla tetején található **összes szolgáltatás** elemre.

3. Írja be a **Azure Active Directory** kifejezést a szűrő keresőmezőbe, majd válassza a **Azure Active Directory** lehetőséget.

4. Válassza a **vállalati alkalmazások** lehetőséget az Azure ad navigációs ablaktábláján.

5. Az alkalmazások listájának megtekintéséhez kattintson a **minden alkalmazás** elemre.

   > [!NOTE]
   > Ha nem látja a kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet. Állítsa a **show (megjelenítés** ) lehetőséget a "minden alkalmazás" értékre.

6. Válassza ki az egyszeri bejelentkezéshez konfigurálni kívánt alkalmazást.

7. Az alkalmazás betöltése után válassza az **egyszeri bejelentkezés** lehetőséget a navigációs ablaktáblán.

8. Az **SAML-aláíró tanúsítvány** területen válassza a  **speciális tanúsítvány-aláírási beállítások megjelenítése** lehetőséget.

9. Válassza ki azt az **aláírási lehetőséget** , amelyet az alkalmazás a következő lehetőségek közül vár:

   * **SAML-válasz aláírása**
   * **SAML-válasz és-érvényesítés aláírása**
   * **SAML-állítás aláírása**

   Amikor a felhasználó legközelebb bejelentkezik az alkalmazásba, az Azure AD aláírja a kiválasztott SAML-válasz részét.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>Az alkalmazás az SHA-1 aláíró algoritmust várja.

Alapértelmezés szerint az Azure AD a legbiztonságosabb algoritmus használatával aláírja az SAML-tokent. Azt javasoljuk, hogy ne módosítsa az aláíró algoritmust *SHA-1* értékre, kivéve, ha az alkalmazáshoz SHA-1 szükséges.

Az aláíró algoritmus módosításához kövesse az alábbi lépéseket:

1. Nyissa meg a [Azure Portal](https://portal.azure.com/) , és jelentkezzen be globális rendszergazdaként vagy társ-rendszergazdaként.

2. Az Azure AD-bővítmény megnyitásához kattintson a bal oldalon található navigációs ablaktábla tetején található **összes szolgáltatás** elemre.

3. Írja be a **Azure Active Directory** kifejezést a szűrő keresőmezőbe, majd válassza a **Azure Active Directory** lehetőséget.

4. Válassza a **vállalati alkalmazások** lehetőséget az Azure ad navigációs ablaktábláján.

5. Az alkalmazások listájának megtekintéséhez kattintson a **minden alkalmazás** elemre.

   > [!NOTE]
   > Ha nem látja a kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet. Állítsa a **show (megjelenítés** ) lehetőséget a "minden alkalmazás" értékre.

6. Válassza ki az egyszeri bejelentkezéshez konfigurálni kívánt alkalmazást.

7. Az alkalmazás betöltése után válassza az **egyszeri bejelentkezés** lehetőséget az alkalmazás bal oldalán található navigációs ablaktáblán.

8. Az **SAML-aláíró tanúsítvány** területen válassza a **speciális tanúsítvány-aláírási beállítások megjelenítése** lehetőséget.

9. Válassza az **SHA-1** lehetőséget az **aláíró algoritmusként**.

   Amikor a felhasználó legközelebb bejelentkezik az alkalmazásba, az Azure AD aláírja az SAML-jogkivonatot az SHA-1 algoritmus használatával.

## <a name="next-steps"></a>Következő lépések
Az [SAML-alapú egyszeri bejelentkezések hibakeresése az Azure ad-beli alkalmazásokban](./debug-saml-sso-issues.md).