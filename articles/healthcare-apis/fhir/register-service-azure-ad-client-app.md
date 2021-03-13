---
title: Szolgáltatási alkalmazás regisztrálása az Azure AD-ben – Azure API a FHIR-hez
description: Megtudhatja, hogyan regisztrálhat egy szolgáltatás-ügyfélalkalmazás Azure Active Directoryban.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 6f7bf122b292ca144eac406957f19a13c7ba6662
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018976"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>Szolgáltatásbeli ügyfélalkalmazás regisztrálása a Azure Active Directoryban

Ebből a cikkből megtudhatja, hogyan regisztrálhat egy szolgáltatás-ügyfélalkalmazás Azure Active Directoryban. Az ügyfélalkalmazások regisztrációja olyan alkalmazások Azure Active Directory, amelyek a jogkivonatok hitelesítéséhez és beszerzéséhez használhatók. A szolgáltatási ügyfelet arra tervezték, hogy egy alkalmazás a felhasználó interaktív hitelesítése nélkül szerezze be a hozzáférési jogkivonatot. A hozzáférési jogkivonatok beszerzése során bizonyos alkalmazási engedélyekkel fog rendelkezni, és az alkalmazás titkos kulcsát (jelszót) kell használnia.

Az alábbi lépéseket követve hozzon létre egy új szolgáltatás-ügyfelet.

## <a name="app-registrations-in-azure-portal"></a>Alkalmazásregisztrációk a Azure Portal

1. A [Azure Portal](https://portal.azure.com)navigáljon a **Azure Active Directory**.

2. Válassza az **Alkalmazásregisztrációk** lehetőséget.

    ![Azure Portal. Új alkalmazás regisztrálása.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Válassza az **új regisztráció** lehetőséget.

4. Adja meg a szolgáltatás ügyfelének megjelenítendő nevet. A szolgáltatás-ügyfélalkalmazások általában nem használnak válasz URL-címet.

    :::image type="content" source="media/service-client-app/service-client-registration.png" alt-text="Azure Portal. Új szolgáltatás-ügyfélalkalmazás regisztrálása.":::

5. Válassza a **Regisztráció** lehetőséget.

## <a name="api-permissions"></a>API-engedélyek

Most, hogy regisztrálta az alkalmazását, ki kell választania, hogy mely API-engedélyeket kell megadnia az alkalmazásnak a felhasználók nevében:

1. Válassza az **API-engedélyek** lehetőséget.
1. Válassza **az engedély hozzáadása** lehetőséget.

    Ha az Azure API-t használja a FHIR-hez, az Azure Healthcare API-kat a **saját szervezetem által használt API**- **k alapján kell** megkeresnie. 

    Ha más erőforrás-alkalmazásra hivatkozik, válassza ki a korábban a **saját API**-k alatt létrehozott [FHIR API Resource Application-regisztrációt](register-resource-azure-ad-client-app.md) .

    :::image type="content" source="media/service-client-app/service-client-org-api.png" alt-text="Bizalmas ügyfél. Saját szervezeti API-k" lightbox="media/service-client-app/service-client-org-api-expanded.png":::

1. Válassza ki azokat a hatóköröket (engedélyeket), amelyeket a bizalmas alkalmazásnak meg kell tudnia kérni a felhasználó nevében:

    :::image type="content" source="media/service-client-app/service-client-add-permission.png" alt-text="Szolgáltatási ügyfél. Delegált engedélyek":::

1. Adja meg az alkalmazás jóváhagyását. Ha nem rendelkezik a szükséges engedélyekkel, forduljon a Azure Active Directory rendszergazdájához:

    :::image type="content" source="media/service-client-app/service-client-grant-permission.png" alt-text="Szolgáltatási ügyfél. Jóváhagyás megadása":::

## <a name="application-secret"></a>Alkalmazás titkos kódja

A szolgáltatás ügyfelének titkos (jelszó) értékkel kell rendelkeznie a jogkivonat beszerzéséhez.

1. Válassza ki a **tanúsítványok & Secrets** elemet.
2. Válassza az **Új titkos ügyfélkód** lehetőséget.

    ![Azure Portal. Szolgáltatás-ügyfél titka](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. Adja meg a titok leírását és időtartamát (1 év, 2 év vagy soha).

4. A titkos kód létrehozása után csak egyszer jelenik meg a portálon. Jegyezze fel, és tárolja biztonságosan.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan regisztrálhat egy szolgáltatás-ügyfélalkalmazás Azure Active Directoryban. Ezt követően tesztelje a hozzáférést a FHIR-kiszolgálóhoz a Poster használatával.
 
>[!div class="nextstepaction"]
>[Az Azure API elérése a Poster FHIR](access-fhir-postman-tutorial.md)
