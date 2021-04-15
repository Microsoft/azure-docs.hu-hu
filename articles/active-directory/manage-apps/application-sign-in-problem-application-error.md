---
title: Miután bejelentkezik az alkalmazásba, megjelenik a hibaüzenet | Microsoft Docs
description: Az Azure AD-bejelentkezéssel kapcsolatos problémák megoldása, amikor az alkalmazás hibaüzenetet ad vissza.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ddebc4778d923bc3a002f14fc4b4db1b7bb730d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379298"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>Hibaüzenetet megjelenítő alkalmazásoldal jelenik meg, miután a felhasználó bejelentkezett

Ebben a forgatókönyvben a Azure Active Directory (Azure AD) bejelentkezteti a felhasználót. Az alkalmazás azonban hibaüzenetet jelenít meg, és nem hagyja, hogy a felhasználó befejezze a bejelentkezési folyamatot. A probléma az, hogy az alkalmazás nem válaszolt az Azure AD által kiadott válaszra.

Több oka is lehet annak, hogy az alkalmazás nem fogadja el az Azure AD válaszát. Ha a hibaüzenet nem azonosítja egyértelműen, hogy mi hiányzik a válaszból, próbálkozzon a következővel:

-   Ha az alkalmazás az Azure AD-katalógus, ellenőrizze, hogy követte-e az SAML-alapú egyszeri bejelentkezés hibakeresése az [Azure AD-alkalmazásokban lépéseit.](./debug-saml-sso-issues.md)

-   Az SAML-kérés, -válasz és -jogkivonat rögzítéséhez használjon egy olyan eszközt, mint a [Fiddler.](https://www.telerik.com/fiddler)

-   Küldje el az SAML-választ az alkalmazás gyártójának, és kérdezze meg, mi hiányzik.

## <a name="attributes-are-missing-from-the-saml-response"></a>Az SAML-válaszból hiányoznak attribútumok

Ha olyan attribútumot szeretne hozzáadni az Azure AD-konfigurációhoz, amely az Azure AD-válaszban lesz elküldve, kövesse az alábbi lépéseket:

1. Nyissa meg [**Azure Portal,**](https://portal.azure.com/) és jelentkezzen be globális rendszergazdaként vagy társadminisztrátként.

2. A bal oldali navigációs panel tetején válassza a Minden **szolgáltatás** lehetőséget az Azure AD-bővítmény megnyitásához.

3. Írja **Azure Active Directory** a szűrő keresőmezőbe, majd válassza a **Azure Active Directory.**

4. Az  Azure AD navigációs panelen válassza a Vállalati alkalmazások lehetőséget.

5. Válassza **a Minden alkalmazás** lehetőséget az alkalmazások listájának megtekintéséhez.

   > [!NOTE]
   > Ha nem látja a kívánt alkalmazást, használja  a Minden alkalmazás lista tetején található **Szűrő vezérlőt.** A Show **(Megjelenítése)** beállítást állítsa "Minden alkalmazás" lehetőségre.

6. Válassza ki az egyszeri bejelentkezéshez konfigurálni kívánt alkalmazást.

7. Az alkalmazás betöltése után válassza az **Egyszeri bejelentkezés** lehetőséget a navigációs panelen.

8. A Felhasználói **attribútumok szakaszban** válassza a Minden más felhasználói attribútum megtekintése és **szerkesztése lehetőséget.** Itt módosíthatja, hogy mely attribútumokat küldje el az alkalmazásnak az SAML-jogkivonatban, amikor a felhasználók bejelentkeznek.

   Attribútum hozzáadása:

   1. Válassza **az Attribútum hozzáadása lehetőséget.** Adja meg **a Nevet,** majd válassza ki az **Értéket** a legördülő listából.

   1.  Kattintson a **Mentés** gombra. Az új attribútum a táblában lesz látható.

9. Mentse a konfigurációt.

   Amikor a felhasználó legközelebb bejelentkezik az alkalmazásba, az Azure AD elküldi az SAML-válaszban az új attribútumot.

## <a name="the-app-doesnt-identify-the-user"></a>Az alkalmazás nem azonosítja a felhasználót

Az alkalmazásba való bejelentkezés sikertelen, mert az SAML-válaszból hiányzik egy attribútum, például egy szerepkör. Vagy az is meghiúsul, hogy az alkalmazás más formátumot vagy értéket vár a **NameID** (User Identifier) attribútumhoz.

Ha az [Azure AD](../app-provisioning/user-provisioning.md) automatizált felhasználóátépítést használ a felhasználók létrehozásához, karbantartásához és eltávolításához az alkalmazásban, ellenőrizze, hogy a felhasználó ki lett-e építve az SaaS-alkalmazásban. További információ: Nincsenek felhasználók kiépítve az [Azure AD katalógusából.](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Attribútum hozzáadása az Azure AD-alkalmazás konfigurációhoz

A felhasználói azonosító értékének a következő lépésekkel módosíthatja:

1. Nyissa meg [**Azure Portal,**](https://portal.azure.com/) és jelentkezzen be globális rendszergazdaként vagy társadminisztrátként.

2. Az  Azure AD-bővítmény megnyitásához válassza a bal oldali navigációs panel tetején található Minden szolgáltatás lehetőséget.

3. Írja **Azure Active Directory** a szűrő keresőmezőbe, majd válassza a **Azure Active Directory** lehetőséget.

4. Az  Azure AD navigációs panelen válassza a Vállalati alkalmazások lehetőséget.

5. Válassza **a Minden alkalmazás** lehetőséget az alkalmazások listájának megtekintéséhez.

   > [!NOTE]
   > Ha nem látja a kívánt alkalmazást, használja  a Minden alkalmazás lista tetején található **Szűrő vezérlőt.** Állítsa a **Show (Megjelenítése)** beállítást "Minden alkalmazás" lehetőségre.

6. Válassza ki az SSO-hoz konfigurálni kívánt alkalmazást.

7. Az alkalmazás betöltése után válassza az **Egyszeri bejelentkezés** lehetőséget a navigációs panelen.

8. A **Felhasználói attribútumok alatt** válassza ki a felhasználó egyedi azonosítóját a Felhasználói azonosító legördülő listából. 

## <a name="change-the-nameid-format"></a>A NameID formátum módosítása

Ha az alkalmazás más formátumot vár a **NameID (User** Identifier) attribútumhoz, a NameID formátumának módosítása a [NévAZONOSÍTÓ](../develop/active-directory-saml-claims-customization.md#editing-nameid) szerkesztése dokumentumban látható.

Az Azure AD az SAML AuthRequest kérelemben kiválasztott érték vagy az alkalmazás által kért formátum alapján választja ki a **NameID** attribútum (felhasználói azonosító) formátumát. További információkért lásd az egyszeri bejelentkezéses SAML protokoll "NameIDPolicy" [című szakaszát.](../develop/single-sign-on-saml-protocol.md#nameidpolicy)

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>Az alkalmazás eltérő aláírási metódust vár az SAML-válaszhoz

Az SAML-jogkivonat az Azure AD által digitálisan aláírt részeinek a módosítása érdekében kövesse az alábbi lépéseket:

1. Nyissa meg [Azure Portal,](https://portal.azure.com/) és jelentkezzen be globális rendszergazdaként vagy társadminisztrátként.

2. Az  Azure AD-bővítmény megnyitásához válassza a bal oldali navigációs panel tetején található Minden szolgáltatás lehetőséget.

3. Írja **Azure Active Directory** a szűrő keresőmezőbe, majd válassza a **Azure Active Directory.**

4. Az  Azure AD navigációs panelen válassza a Vállalati alkalmazások lehetőséget.

5. Válassza **a Minden alkalmazás** lehetőséget az alkalmazások listájának megtekintéséhez.

   > [!NOTE]
   > Ha nem látja a kívánt alkalmazást, használja  a Minden alkalmazás lista tetején található **Szűrő vezérlőt.** Állítsa a **Show (Megjelenítése)** beállítást "Minden alkalmazás" lehetőségre.

6. Válassza ki az egyszeri bejelentkezéshez konfigurálni kívánt alkalmazást.

7. Az alkalmazás betöltése után válassza az **Egyszeri bejelentkezés** lehetőséget a navigációs panelen.

8. Az **SAML aláíró tanúsítványa alatt válassza** a Speciális **tanúsítvány-aláírási beállítások megjelenítése lehetőséget.**

9. Válassza ki **az alábbi** lehetőségek közül azt az aláírási lehetőséget, amelytől az alkalmazás elvárja:

   * **SAML-válasz aláírása**
   * **SAML-válasz és helyességi feltétel aláírása**
   * **SAML-helyességi feltétel aláírása**

   Amikor a felhasználó legközelebb bejelentkezik az alkalmazásba, az Azure AD aláírja a kiválasztott SAML-válasz részét.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>Az alkalmazás az SHA-1 aláíró algoritmust várja

Alapértelmezés szerint az Azure AD a legbiztonságosabb algoritmus használatával aláírja az SAML-jogkivonatot. Javasoljuk, hogy ne módosítsa az aláíró algoritmust *SHA-1 algoritmusra,* ha az alkalmazás nem igényel SHA-1 algoritmust.

Az aláíró algoritmust az alábbi lépésekkel módosíthatja:

1. Nyissa meg [Azure Portal,](https://portal.azure.com/) és jelentkezzen be globális rendszergazdaként vagy társadminisztrátként.

2. Az  Azure AD-bővítmény megnyitásához válassza a bal oldali navigációs panel tetején található Minden szolgáltatás lehetőséget.

3. Írja **Azure Active Directory** a szűrő keresőmezőbe, majd válassza a **Azure Active Directory.**

4. Az  Azure AD navigációs panelen válassza a Vállalati alkalmazások lehetőséget.

5. Válassza **a Minden alkalmazás** lehetőséget az alkalmazások listájának megtekintéséhez.

   > [!NOTE]
   > Ha nem látja a kívánt alkalmazást, használja  a Minden alkalmazás lista tetején található **Szűrő vezérlőt.** A Show **(Megjelenítése)** beállítást állítsa "Minden alkalmazás" lehetőségre.

6. Válassza ki az egyszeri bejelentkezéshez konfigurálni kívánt alkalmazást.

7. Az alkalmazás betöltése után válassza az **Egyszeri bejelentkezés** lehetőséget az alkalmazás bal oldalán található navigációs panelen.

8. Az **SAML aláíró tanúsítványa alatt válassza** a Speciális **tanúsítvány-aláírási beállítások megjelenítése lehetőséget.**

9. Aláíró algoritmusként válassza az **SHA-1** **lehetőséget.**

   Amikor a felhasználó legközelebb bejelentkezik az alkalmazásba, az Azure AD aláírja az SAML-jogkivonatot az SHA-1 algoritmus használatával.

## <a name="next-steps"></a>Következő lépések
[SAML-alapú egyszeri bejelentkezés hibakeresése az Azure AD-alkalmazásokban.](./debug-saml-sso-issues.md)