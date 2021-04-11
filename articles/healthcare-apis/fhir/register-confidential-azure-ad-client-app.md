---
title: Bizalmas ügyfélalkalmazás regisztrálása az Azure AD-ben – Azure API a FHIR-hez
description: Regisztráljon egy bizalmas ügyfélalkalmazás Azure Active Directoryban, amely hitelesíti a felhasználó nevében, és hozzáférést kér az erőforrás-alkalmazásokhoz.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: matjazl
ms.openlocfilehash: c10b27d375e2bfb8c64130eceb416a633241cf68
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284433"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Bizalmas ügyfélalkalmazás regisztrálása a Azure Active Directoryban

Ebből az oktatóanyagból megtudhatja, hogyan regisztrálhat egy bizalmas ügyfélalkalmazás Azure Active Directory (Azure AD) alkalmazásban.  

Az ügyfélalkalmazás regisztrálása egy olyan alkalmazás Azure AD-ábrázolása, amely a felhasználó nevében történő hitelesítéshez és az [erőforrás-alkalmazásokhoz](register-resource-azure-ad-client-app.md)való hozzáféréshez használható. A bizalmas ügyfélalkalmazás egy olyan alkalmazás, amely megbízhatónak tartja a titkos kulcsot, és a hozzáférési jogkivonatok kérésekor megmutatják a titkos kulcsot. Példa a bizalmas alkalmazásokra a kiszolgálóoldali alkalmazások. 

Új bizalmas ügyfélalkalmazás regisztrálásához tekintse meg az alábbi lépéseket. 

## <a name="register-a-new-application"></a>Új alkalmazás regisztrálása

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Azure Active Directory** lehetőséget.

1. Válassza az **Alkalmazásregisztrációk** lehetőséget. 

    :::image type="content" source="media/how-to-aad/portal-aad-new-app-registration.png" alt-text="Azure Portal. Új alkalmazás regisztrálása.":::

1. Válassza az **új regisztráció** lehetőséget.

1. Adja meg az alkalmazásnak a felhasználó felé néző megjelenítendő nevet.

1. A **támogatott fiókok típusainál** válassza ki, hogy ki használhatja az alkalmazást, vagy elérheti az API-t.

1. Választható Adjon meg egy **átirányítási URI**-t. Ezeket az adatokat később is megváltoztathatja, de ha ismeri az alkalmazás válaszának URL-címét, adja meg most.

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png" alt-text="Új bizalmas ügyfélalkalmazás regisztrálása.":::

1. Válassza a **Regisztráció** lehetőséget.

## <a name="api-permissions"></a>API-engedélyek

Most, hogy regisztrálta az alkalmazását, ki kell választania, hogy mely API-engedélyeket kell megadnia az alkalmazásnak a felhasználók nevében.

1. Válassza az **API-engedélyek** lehetőséget.

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png" alt-text="Bizalmas ügyfél. API-engedélyek.":::

1. Válassza **az engedély hozzáadása** lehetőséget.

    Ha a FHIR készült Azure API-t használja, akkor az Azure Healthcare **API** -t a **szervezetem által használt API**-k területen megkeresve fogja hozzáadni. Az Azure Healthcare API keresési eredménye csak akkor tér vissza, ha már [telepítette az Azure API](fhir-paas-powershell-quickstart.md)-t a FHIR.

    Ha más erőforrás-alkalmazásra hivatkozik, válassza ki a korábban a **saját API**-k alatt létrehozott [FHIR API Resource Application-regisztrációt](register-resource-azure-ad-client-app.md) .


    :::image type="content" source="media/conf-client-app/confidential-client-org-api.png" alt-text="Bizalmas ügyfél. Saját szervezeti API-k" lightbox="media/conf-client-app/confidential-app-org-api-expanded.png":::
    

1. Válassza ki azokat a hatóköröket (engedélyeket), amelyeket a bizalmas ügyfélalkalmazás a felhasználó nevében kér. Válassza a **user_impersonation** lehetőséget, majd kattintson az **engedélyek hozzáadása** lehetőségre.

    :::image type="content" source="media/conf-client-app/confidential-client-add-permission.png" alt-text="Bizalmas ügyfél. Delegált engedélyek":::


## <a name="application-secret"></a>Alkalmazás titkos kódja

1. Válassza a **tanúsítványok & titkok** lehetőséget, majd válassza az **új ügyfél titka** lehetőséget. 

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png" alt-text="Bizalmas ügyfél. Alkalmazás titka.":::

1. Adja meg az ügyfél titkos kódjának **leírását** . A lejárat időkeretének kiválasztásához válassza a **lejárat** legördülő menüt, majd kattintson a **Hozzáadás** gombra.

   :::image type="content" source="media/how-to-aad/add-a-client-secret.png" alt-text="Adja hozzá az ügyfél titkos kulcsát.":::

1. Az ügyfél titkos karakterláncának létrehozása után másolja az **értékét** és **azonosítóját**, és tárolja azokat biztonságos helyen.

   :::image type="content" source="media/how-to-aad/client-secret-string-password.png" alt-text="Az ügyfél titkos karakterlánca."::: 

> [!NOTE]
>Az ügyfél titkos karakterlánca csak egyszer látható a Azure Portalban. Ha a tanúsítványok & titkok weboldaláról navigál, és visszaadja azt, akkor az érték sztring maszkolásra kerül. Fontos, hogy a létrehozása után azonnal készítsen másolatot az ügyfél titkos karakterláncáról. Ha nem rendelkezik az ügyfél titkos kulcsának biztonsági másolatával, a fenti lépéseket újra kell ismételnie.
 
## <a name="next-steps"></a>Következő lépések

Ez a cikk végigvezeti a bizalmas ügyfélalkalmazás Azure AD-ben való regisztrálásának lépésein. Emellett az API-engedélyek Azure Healthcare API-hoz való hozzáadásának lépésein is áttekintheti. Végül megmutatta, hogyan hozhat létre egy alkalmazás-titkot. Emellett megtudhatja, hogyan férhet hozzá a FHIR-kiszolgálóhoz a Poster használatával.
 
>[!div class="nextstepaction"]
>[Az Azure API elérése a Poster FHIR](access-fhir-postman-tutorial.md)
