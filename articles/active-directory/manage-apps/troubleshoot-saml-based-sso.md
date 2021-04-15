---
title: SAML-alapú egyszeri bejelentkezés hibaelhárítása az Azure Active Directoryban
description: SAML-alapú egyszeri bejelentkezésre konfigurált Azure AD-alkalmazásokkal kapcsolatos problémák elhárítása.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.openlocfilehash: c4a4f7bfad4254e7c3fa5851e62532ed427ced8b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376426"
---
# <a name="troubleshoot-saml-based-single-sign-on-in-azure-active-directory"></a>SAML-alapú egyszeri bejelentkezés hibaelhárítása az Azure Active Directoryban
Ha problémába ütközik egy alkalmazás konfigurálásakor. Ellenőrizze, hogy követte-e az oktatóanyagban található összes lépést az alkalmazáshoz. Az alkalmazás konfigurációjában beágyazott dokumentációt talál az alkalmazás konfigurálásához. Emellett az [SaaS-alkalmazások](../saas-apps/tutorial-list.md) és -alkalmazások Azure Active Directory való integrálásával kapcsolatos oktatóanyagok listájában részletes útmutatást kaphat.

## <a name="cant-add-another-instance-of-the-application"></a>Nem lehet hozzáadni az alkalmazás egy másik példányát
Egy alkalmazás második példányának hozzáadásához a következőre van szükség:
-   Konfigurálja a második példány egyedi azonosítóját. Nem konfigurálhatja az első példányhoz használt azonosítót.
-   Konfigurálja az első példányhoz használttól eltérő tanúsítványt.

Ha az alkalmazás nem támogatja a fentiek valamelyikét. Ezután nem konfigurálhatja a második példányt.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Nem lehet hozzáadni az azonosítót vagy a válasz URL-címet
Ha nem tudja konfigurálni az azonosítót vagy a válasz URL-címet, ellenőrizze, hogy az Azonosító és a Válasz URL-cím értékei megegyeznek-e az alkalmazáshoz előre konfigurált mintákkal.

Az alkalmazáshoz előre konfigurált minták:
1. Nyissa meg [**Azure Portal,**](https://portal.azure.com/) és jelentkezzen be globális **rendszergazdaként** vagy **társadminisztrátként.** Lépjen a 7. lépésre. Ha már az Azure AD alkalmazáskonfigurációs panelen van.
2. Nyissa meg **Azure Active Directory bővítményt**  a bal oldali fő navigációs menü tetején található Minden szolgáltatás elemre kattintva.
3. Írja be **a "Azure Active Directory"** elemet a szűrő keresőmezőbe, és válassza ki **Azure Active Directory** elemet.
4. A **bal oldali navigációs Azure Active Directory** kattintson a Vállalati alkalmazások elemre.
5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.
   * Ha itt nem látja a kívánt alkalmazást, használja a Minden alkalmazás  lista tetején található  Szűrő vezérlőt, és állítsa a Show (Megjelenítése) beállítást a Minden **alkalmazás lehetőségre.** 
6. Válassza ki az egyszeri bejelentkezés konfigurálni kívánt alkalmazást.
7. Az alkalmazás betöltése után kattintson az **Egyszeri bejelentkezés** elemre az alkalmazás bal oldali navigációs menüjében.
8. A Mode (Mód) legördülő menüben válassza az **SAML-alapú** **bejelentkezés** lehetőséget.
9. A Tartomány és **URL-címek** szakaszban kattintson az Azonosító vagy a Válasz **URL-cím szövegmezőre.** 
10. Az alkalmazás támogatott mintáit háromféleképpen lehet ismerni:
    * A szövegmezőben a támogatott minta(okat) helyőrzőként láthatja. *Példa:* <https://contoso.com> .
    * Ha a minta nem támogatott, piros felkiáltójelet fog látni, amikor megpróbálja beírni az értéket a szövegmezőbe. Ha az egérmutatót a piros felkiáltójel fölé húzza, láthatja a támogatott mintákat.
    * Az alkalmazás oktatóanyagában a támogatott mintákkal kapcsolatos információkat is lekért. Az **Azure AD egyszeri bejelentkezés konfigurálása szakaszban.** Lépjen a Tartomány és URL-címek szakasz értékeinek **konfigurált lépéséhez.**

Ha az értékek nem egyeznek az Azure AD-ban előre konfigurált mintákkal. A következőket teheti:
-   Az Azure AD-ban előre konfigurált mintának megfelelő értékek lekérte az alkalmazás gyártójával
-   Vagy kapcsolatba léphet az Azure AD csapatával a következő elérhetőségen: , vagy megjegyzésben hagyhatja az oktatóanyagban, hogy kérje az alkalmazás támogatott mintái <aadapprequest@microsoft.com> frissítését

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Hol állíthatom be az EntityID (Felhasználói azonosító) formátumot?
Nem választhatja ki azt az EntityID (Felhasználói azonosító) formátumot, amit az Azure AD a felhasználói hitelesítést követően a válaszban küld az alkalmazásnak.

Az Azure AD az SAML-hitelesítési kérelemben kiválasztott érték vagy az alkalmazás által kért formátum alapján válassza ki a NameID attribútum (felhasználói azonosító) formátumát. További információt a [Single Sign-On SAML protocol](../develop/single-sign-on-saml-protocol.md#authnrequest) című cikkben, a NameIDPolicy című szakaszban talál.

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Nem találom az Azure AD metaadatait az alkalmazással való konfigurálás befejezéséhez
Az alkalmazás metaadatainak vagy tanúsítványának az Azure AD-ból való letöltéséhez kövesse az alábbi lépéseket:
1. Nyissa meg [**Azure Portal,**](https://portal.azure.com/) és jelentkezzen be globális **rendszergazdaként** vagy **társadminisztrátként.**
2. Nyissa meg **Azure Active Directory bővítményt**  a bal oldali fő navigációs menü tetején található Minden szolgáltatás elemre kattintva.
3. Írja be **a "Azure Active Directory"** elemet a szűrő keresőmezőbe, és válassza ki **Azure Active Directory** elemet.
4. A **bal oldali navigációs Azure Active Directory** kattintson a Vállalati alkalmazások elemre.
5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.
   * Ha itt nem látja a kívánt alkalmazást,  használja a Minden alkalmazás lista tetején található  Szűrő vezérlőt, és állítsa a Show (Megjelenítése) beállítást a All Applications (Minden **alkalmazás) lehetőségre.** 
6. Válassza ki azt az alkalmazást, amelybe egyszeri bejelentkezést konfigurált.
7. Az alkalmazás betöltése után kattintson az **Egyszeri bejelentkezés** elemre az alkalmazás bal oldali navigációs menüjében.
8. Válassza az **SAML aláíró tanúsítvány szakaszt,** majd kattintson az **Oszlop értékének** letöltése elemre. Attól függően, hogy az alkalmazásnak mire van szüksége az egyszeri bejelentkezés konfigurálásához, lehetősége van letölteni a metaadat-XML-t vagy a tanúsítványt.

Az Azure AD nem ad meg a metaadatok lekért URL-címét. A metaadatok csak XML-fájlként olvashatók be.

## <a name="customize-saml-claims-sent-to-an-application"></a>Az alkalmazásoknak elküldött SAML-jogcímek testreszabása
Az alkalmazásnak küldött SAML-attribútum jogcímek testreszabásával kapcsolatos további információkért tekintse meg a jogcím-leképezést a Azure Active Directory oldalon. [](../develop/active-directory-claims-mapping.md)

## <a name="next-steps"></a>Következő lépések
* [Gyorsútmutató-sorozat az alkalmazáskezelésről](view-applications-portal.md)