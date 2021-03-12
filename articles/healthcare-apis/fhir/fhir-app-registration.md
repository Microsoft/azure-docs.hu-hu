---
title: Az Azure API-hoz készült Azure Active Directory-alkalmazások regisztrálása a FHIR-hez
description: Ez az oktatóanyag azt ismerteti, hogy mely alkalmazásokat kell regisztrálni az Azure API-hoz a FHIR és a FHIR Server for Azure számára.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 10/13/2019
ms.openlocfilehash: b01f65b6be31007cebc7a5b03a1c57f6b901fe53
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019103"
---
# <a name="register-the-azure-active-directory-apps-for-azure-api-for-fhir"></a>Az Azure API-hoz készült Azure Active Directory-alkalmazások regisztrálása a FHIR-hez

Több konfigurációs lehetőség közül választhat, ha beállítja az Azure API-t a FHIR-hez vagy az Azure-hoz készült FHIR-kiszolgálóhoz (OSS). A nyílt forráskód létrehozásához létre kell hoznia a saját erőforrás-alkalmazás regisztrációját. Az Azure API for FHIR esetében ez az erőforrás-alkalmazás automatikusan létrejön.

## <a name="application-registrations"></a>Alkalmazás-regisztrációk

Ahhoz, hogy egy alkalmazás működjön az Azure AD-vel, regisztrálni kell. A FHIR-kiszolgáló kontextusában kétféle alkalmazás-regisztráció található:

1. Erőforrás-alkalmazás regisztrációja.
1. Ügyfélalkalmazások regisztrációja.

Az **erőforrás-alkalmazások** az Azure ad-vel védett API-k vagy erőforrások Azure ad-beli képviseletei, konkrétan a FHIR készült Azure API. A szolgáltatás kiépítésekor a rendszer automatikusan létrehozza a FHIR készült Azure API-ra vonatkozó erőforrás-alkalmazást, de ha a nyílt forráskódú kiszolgálót használja, [regisztrálnia kell egy erőforrás-alkalmazást](register-resource-azure-ad-client-app.md) az Azure ad-ben. Ez az erőforrás-alkalmazás azonosító URI-t tartalmaz. Azt javasoljuk, hogy ez az URI ugyanaz legyen, mint a FHIR-kiszolgáló URI-ja. Ezt az URI-t a `Audience` FHIR-kiszolgálóként kell használni. Egy ügyfélalkalmazás hozzáférést kérhet ehhez a FHIR-kiszolgálóhoz, amikor jogkivonatot kér.

Az *ügyfélalkalmazások* a jogkivonatokat kérő ügyfelek regisztrációi. Az 2,0-OAuth gyakran legalább három különböző típusú alkalmazás különböztethető meg:

1. **Bizalmas ügyfelek**, más néven webes alkalmazások az Azure ad-ben. A bizalmas ügyfelek olyan alkalmazások, amelyek az [engedélyezési kód áramlását](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) használják a tokenek beszerzésére egy bejelentkezett felhasználó nevében, amely érvényes hitelesítő adatokat mutat be. Ezeket bizalmas ügyfélnek nevezzük, mert képesek titkos kulcsot tárolni, és ezt a titkot bemutatják az Azure AD-nek a jogkivonat hitelesítési kódjának cseréjekor. Mivel a bizalmas ügyfelek képesek magukat hitelesíteni magukat az ügyfél titkával, több mint nyilvános ügyfelet bíznak meg, és hosszabb élettartamú tokenekkel rendelkezhetnek, és frissítési jogkivonatot kapnak. Olvassa el a [bizalmas ügyfél regisztrálásának](register-confidential-azure-ad-client-app.md)részleteit. Fontos megjegyezni, hogy a válasz URL-címének regisztrálása, amelyen az ügyfél megkapja az engedélyezési kódot.
1. **Nyilvános ügyfelek**. Ezek olyan ügyfelek, amelyek nem tudnak titkos kulcsot tárolni. Ez általában egy mobileszköz-alkalmazás vagy egy egyoldalas JavaScript-alkalmazás, amelyben egy felhasználó felfedezheti az ügyfél titkos kulcsát. A nyilvános ügyfelek az engedélyezési kód folyamatát is használják, de a token beszerzése során nem jelenhetnek meg titkos kódok, és előfordulhat, hogy rövidebb élettartamú tokenekkel rendelkeznek, és nincs frissítési jogkivonat. Olvassa el a [nyilvános ügyfél regisztrálásának](register-public-azure-ad-client-app.md)részleteit.
1. Szolgáltatás-ügyfelek. Ezek az ügyfelek saját maguk nevében (nem a felhasználó nevében) kapják meg a jogkivonatokat az [ügyfél hitelesítő adatainak](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)használatával. Ezek jellemzően olyan alkalmazásokat jelentenek, amelyek nem interaktív módon férnek hozzá a FHIR-kiszolgálóhoz. Erre példa a betöltési folyamat. Szolgáltatás-ügyfél használata esetén nem szükséges elindítani egy jogkivonat lekérésének folyamatát a végpontra irányuló hívással `/authorize` . A szolgáltatás-ügyfelek közvetlenül a végponthoz juthatnak, `/token` és megszerezhetik az ügyfél-azonosítót és az ügyfél titkos kulcsát a jogkivonat beszerzéséhez. A [szolgáltatás-ügyfél regisztrálásának](register-service-azure-ad-client-app.md) részletei

## <a name="next-steps"></a>Következő lépések

Ebben az áttekintésben elvégezte a FHIR API-k használatához szükséges alkalmazás-regisztrációk típusait.

A telepítője alapján az alkalmazások regisztrálásának útmutatói: útmutatók

* [Erőforrás-alkalmazás regisztrálása](register-resource-azure-ad-client-app.md)
* [Bizalmas ügyfélalkalmazás regisztrálása](register-confidential-azure-ad-client-app.md)
* [Nyilvános ügyfélalkalmazás regisztrálása](register-public-azure-ad-client-app.md)
* [Szolgáltatásalkalmazás regisztrálása](register-service-azure-ad-client-app.md)

Az alkalmazások regisztrálása után üzembe helyezheti a FHIR készült Azure API-t.

>[!div class="nextstepaction"]
>[Az Azure API for FHIR üzembe helyezése](fhir-paas-powershell-quickstart.md)