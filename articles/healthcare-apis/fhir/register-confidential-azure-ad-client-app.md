---
title: Bizalmas ügyfélalkalmazás regisztrálása az Azure AD-ben – Azure API a FHIR-hez
description: Regisztráljon egy bizalmas ügyfélalkalmazás Azure Active Directoryban, amely hitelesíti a felhasználó nevében, és hozzáférést kér az erőforrás-alkalmazásokhoz.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 8021fb3fa9f11ef895569f48a2ae21b3f7adcd36
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019026"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Bizalmas ügyfélalkalmazás regisztrálása a Azure Active Directoryban

Ebből az oktatóanyagból megtudhatja, hogyan regisztrálhat egy bizalmas ügyfélalkalmazás Azure Active Directoryban. 

Az ügyfélalkalmazás regisztrálása egy olyan alkalmazás Azure Active Directory ábrázolása, amely a felhasználó nevében történő hitelesítéshez és az [erőforrás-alkalmazásokhoz](register-resource-azure-ad-client-app.md)való hozzáféréshez használható. A bizalmas ügyfélalkalmazás egy olyan alkalmazás, amely megbízhatónak tartja a titkos kulcsot, és a hozzáférési jogkivonatok kérésekor megmutatják a titkos kulcsot. Példa a bizalmas alkalmazásokra a kiszolgálóoldali alkalmazások.

Ha új bizalmas alkalmazást szeretne regisztrálni a portálon, kövesse az alábbi lépéseket.

## <a name="register-a-new-application"></a>Új alkalmazás regisztrálása

1. A [Azure Portal](https://portal.azure.com)navigáljon a **Azure Active Directory**.

1. Válassza az **Alkalmazásregisztrációk** lehetőséget.

    ![Azure Portal. Új alkalmazás regisztrálása.](media/how-to-aad/portal-aad-new-app-registration.png)

1. Válassza az **új regisztráció** lehetőséget.

1. Adja meg az alkalmazás megjelenítendő nevét.

1. Adja meg a válasz URL-címét. Ezeket az adatokat később is megváltoztathatja, de ha ismeri az alkalmazás válaszának URL-címét, adja meg most.

    ![Új bizalmas ügyfélalkalmazás regisztrálása.](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)
1. Válassza a **Regisztráció** lehetőséget.

## <a name="api-permissions"></a>API-engedélyek

Most, hogy regisztrálta az alkalmazását, ki kell választania, hogy mely API-engedélyeket kell megadnia az alkalmazásnak a felhasználók nevében:

1. Válassza az **API-engedélyek** lehetőséget.

    ![Bizalmas ügyfél. API-engedélyek](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

1. Válassza **az engedély hozzáadása** lehetőséget.

    Ha az Azure API-t használja a FHIR-hez, az Azure Healthcare API-kat a **saját szervezetem által használt API**- **k alapján kell** megkeresnie. Ha már [telepítette az Azure API-t a FHIR-hez](fhir-paas-powershell-quickstart.md), csak ezt fogja tudni megkeresni.

    Ha más erőforrás-alkalmazásra hivatkozik, válassza ki a korábban a **saját API**-k alatt létrehozott [FHIR API Resource Application-regisztrációt](register-resource-azure-ad-client-app.md) .


    :::image type="content" source="media/conf-client-app/confidential-client-org-api.png" alt-text="Bizalmas ügyfél. Saját szervezeti API-k" lightbox="media/conf-client-app/confidential-app-org-api-expanded.png":::
    

3. Válassza ki azokat a hatóköröket (engedélyeket), amelyeket a bizalmas alkalmazásnak meg kell tudnia kérni a felhasználó nevében:

    :::image type="content" source="media/conf-client-app/confidential-client-add-permission.png" alt-text="Bizalmas ügyfél. Delegált engedélyek":::

## <a name="application-secret"></a>Alkalmazás titkos kódja

1. Válassza ki a **tanúsítványok & Secrets** elemet.
1. Válassza az **Új titkos ügyfélkód** lehetőséget. 

    ![Bizalmas ügyfél. Alkalmazás titka](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. Adja meg a titok leírását és időtartamát (1 év, 2 év vagy soha).

3. A létrehozást követően csak egyszer fog megjelenni a portálon. Jegyezze fel, és tárolja biztonságosan.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan regisztrálhat egy bizalmas ügyfélalkalmazás Azure Active Directoryban. Ezután a Poster használatával férhet hozzá a FHIR-kiszolgálóhoz
 
>[!div class="nextstepaction"]
>[Az Azure API elérése a Poster FHIR](access-fhir-postman-tutorial.md)
