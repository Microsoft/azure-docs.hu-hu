---
title: Nyilvános ügyfélalkalmazás regisztrálása az Azure AD-ben – Azure API a FHIR-hez
description: Ez a cikk azt ismerteti, hogyan regisztrálhat egy nyilvános ügyfélalkalmazás Azure Active Directoryban, a FHIR API Azure-beli üzembe helyezésének előkészítése során.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 3624812cf9119c270c30cf5a7cbebb57030a5113
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019006"
---
# <a name="register-a-public-client-application-in-azure-active-directory"></a>Nyilvános ügyfélalkalmazás regisztrálása Azure Active Directory

Ebből a cikkből megtudhatja, hogyan regisztrálhat egy nyilvános alkalmazást Azure Active Directory.  

Az ügyfélalkalmazások regisztrációja olyan alkalmazások Azure Active Directory, amelyek a felhasználók nevében hitelesíthetők és kérhetnek API-engedélyeket. A nyilvános ügyfelek olyan alkalmazások, mint például a mobil alkalmazások és az egyoldalas JavaScript-alkalmazások, amelyek nem tudják bizalmasan megőrizni a titkokat. Az eljárás hasonló a [bizalmas ügyfél regisztrálásához](register-confidential-azure-ad-client-app.md), de mivel a nyilvános ügyfelek nem megbízhatók egy alkalmazás titkos kódjának tárolására, nem kell hozzáadnia egyet.

A gyors útmutató általános információkat nyújt az [alkalmazások Microsoft Identity platformmal való regisztrálásáról](../../active-directory/develop/quickstart-register-app.md).

## <a name="app-registrations-in-azure-portal"></a>Alkalmazásregisztrációk a Azure Portal

1. Az [Azure Portal](https://portal.azure.com) bal oldali navigációs paneljén kattintson az **Azure Active Directory** elemre.

2. A **Azure Active Directory** panelen kattintson **Alkalmazásregisztrációk**:

    ![Azure Portal. Új alkalmazás regisztrálása.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Kattintson az **új regisztrációra**.

## <a name="application-registration-overview"></a>Az alkalmazás regisztrációjának áttekintése

1. Adja meg az alkalmazás megjelenítendő nevét.

2. Adja meg a válasz URL-címét. A válasz URL-címe, ahol a hitelesítési kódokat a rendszer visszaadja az ügyfélalkalmazás számára. Hozzáadhat további válasz URL-címeket, és szerkesztheti a meglévőket is.

    ![Azure Portal. Új nyilvános alkalmazás regisztrálása.](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-NAME.png)


[Asztali](../../active-directory/develop/scenario-desktop-app-registration.md), [mobil](../../active-directory/develop/scenario-mobile-app-registration.md) vagy [egylapos](../../active-directory/develop/scenario-spa-app-registration.md) alkalmazás konfigurálása nyilvános alkalmazásként:

1. A [Azure Portal](https://portal.azure.com) **Alkalmazásregisztrációk** területen válassza ki az alkalmazást, majd válassza a **hitelesítés** lehetőséget.

2. Válassza a **Speciális beállítások**  >  **alapértelmezett ügyfél típusa** lehetőséget. Ha **nyilvános ügyfélként** kívánja kezelni az alkalmazást, válassza az **Igen** lehetőséget.

3. Egyoldalas alkalmazás esetén válassza a **hozzáférési jogkivonatok** és **azonosító tokenek** lehetőséget az implicit folyamat engedélyezéséhez.

   - Ha az alkalmazás bejelentkezik a felhasználók között, válassza az **azonosító tokenek** lehetőséget.
   - Ha az alkalmazásnak egy védett webes API-t is meg kell hívnia, válassza a **hozzáférési jogkivonatok** lehetőséget.

## <a name="api-permissions"></a>API-engedélyek

A [bizalmas ügyfélalkalmazás](register-confidential-azure-ad-client-app.md)hasonlóan ki kell választania, hogy az alkalmazás milyen API-engedélyeket Kérjen a felhasználók nevében:

1. Nyissa meg az **API-engedélyeket**.

    Ha az Azure API-t használja a FHIR-hez, az Azure Healthcare API-kat a **saját szervezetem által használt API**-k alapján kell megkeresnie. Ha már [telepítette az Azure API-t a FHIR-hez](fhir-paas-powershell-quickstart.md), csak ezt fogja tudni megkeresni.

    
    Ha más erőforrás-alkalmazásra hivatkozik, válassza ki a korábban a **saját API**-k alatt létrehozott [FHIR API Resource Application-regisztrációt](register-resource-azure-ad-client-app.md) :

    ![Azure Portal. Új nyilvános API-engedélyek – FHIR alapértelmezett Azure API](media/public-client-app/api-permissions.png)


2. Válassza ki azokat az engedélyeket, amelyeket szeretne kérni az alkalmazásnak: ![ Azure Portal. Alkalmazásengedélyek](media/public-client-app/app-permissions.png)

## <a name="validate-fhir-server-authority"></a>FHIR-kiszolgáló szolgáltatójának ellenőrzése
Ha a cikkben regisztrált alkalmazás és a FHIR-kiszolgáló ugyanabban az Azure AD-bérlőben található, akkor jó, ha folytatja a következő lépésekkel.

Ha az ügyfélalkalmazás egy másik Azure AD-bérlőben van konfigurálva a FHIR-kiszolgálóról, akkor frissítenie kell a **szolgáltatót**. A FHIR készült Azure API-ban a szolgáltatót a beállítások – > hitelesítés területen állíthatja be. Állítsa be a szolgáltatót a következőre: **https://login.microsoftonline.com/\<TENANT-ID>** .

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan regisztrálhat egy nyilvános ügyfélalkalmazás Azure Active Directoryban. Ezt követően tesztelje a hozzáférést a FHIR-kiszolgálóhoz a Poster használatával.
 
>[!div class="nextstepaction"]
>[Az Azure API elérése a Poster FHIR](access-fhir-postman-tutorial.md)