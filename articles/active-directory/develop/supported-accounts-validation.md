---
title: Az érvényesítési különbségek támogatott fióktípus alapján | Azure
titleSuffix: Microsoft identity platform
description: Az alkalmazás Microsoft Identity platformmal való regisztrálásakor megismerheti a különböző támogatott fióktípus különböző tulajdonságainak érvényesítési különbségeit.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 07/21/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: eea2e587a075d774a25f479ec61575a002b57f75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98937819"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>Érvényesítési eltérések támogatott fióktípus (signInAudience) alapján

Amikor a fejlesztők számára a Microsoft Identity platformmal regisztrál egy alkalmazást, a rendszer megkéri, hogy válassza ki az alkalmazás által támogatott fióktípus típusát. Az Application objektumban és a manifest-ben ez a tulajdonság `signInAudience` .

A lehetőségek a következők:

- **AzureADMyOrg**: csak a szervezeti könyvtárban lévő azon fiókok, amelyeken az alkalmazás regisztrálva van (egybérlős).
- **AzureADMultipleOrgs**: bármely szervezeti könyvtárban (több-bérlős) lévő fiókok.
- **AzureADandPersonalMicrosoftAccount**: fiókok bármely szervezeti címtárban (több-bérlős) és személyes Microsoft-fiókokban (például Skype, Xbox és Outlook.com).

A regisztrált alkalmazások esetében a támogatott fióktípus értékét az alkalmazás **hitelesítés** szakaszában találja. `signInAudience`A **jegyzékfájlban** található tulajdonság alatt is megtalálhatja.

Az ehhez a tulajdonsághoz kiválasztott érték hatással van az alkalmazás egyéb tulajdonságaira. Ennek eredményeképpen, ha módosítja ezt a tulajdonságot, akkor előfordulhat, hogy először módosítania kell a többi tulajdonságot.

Tekintse meg a következő táblázatot a különböző támogatott fióktípus különböző tulajdonságainak érvényesítési eltéréseit illetően.

| Tulajdonság | `AzureADMyOrg` | `AzureADMultipleOrgs` | `AzureADandPersonalMicrosoftAccount` és `PersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| Alkalmazás-azonosító URI `identifierURIs` -ja ()  | Egyedinek kell lennie a bérlőben <br><br> a urn://-sémák támogatottak <br><br> A helyettesítő karakterek használata nem támogatott <br><br> A lekérdezési karakterláncok és töredékek támogatottak <br><br> Legfeljebb 255 karakter hosszú lehet <br><br> Nincs korlát * a identifierURIs száma alapján  | Globálisan egyedinek kell lennie <br><br> a urn://-sémák támogatottak <br><br> A helyettesítő karakterek használata nem támogatott <br><br> A lekérdezési karakterláncok és töredékek támogatottak <br><br> Legfeljebb 255 karakter hosszú lehet <br><br> Nincs korlát * a identifierURIs száma alapján | Globálisan egyedinek kell lennie <br><br> a urn://-sémák nem támogatottak <br><br> Helyettesítő karakterek, töredékek és lekérdezési karakterláncok nem támogatottak <br><br> Legfeljebb 120 karakter hosszú lehet <br><br> Legfeljebb 50 identifierURIs |
| Tanúsítványok ( `keyCredentials` ) | Szimmetrikus aláíró kulcs | Szimmetrikus aláíró kulcs | Titkosítási és aszimmetrikus aláírási kulcs | 
| Ügyfél-titkok ( `passwordCredentials` ) | Nincs korlát * | Nincs korlát * | Ha a liveSDK engedélyezve van: legfeljebb 2 ügyfél titka | 
| Átirányítási URI `replyURLs` -k () | További információért lásd: [átirányítási URI/válasz URL-címekre vonatkozó korlátozások és korlátozások](reply-url.md) . | | | 
| API-engedélyek ( `requiredResourceAccess` ) | Nincs korlát * | Nincs korlát * | Legfeljebb 50 erőforrás/alkalmazás és 30 engedély erőforrás alapján (például Microsoft Graph). 200-os teljes korlát (erőforrás x engedélyek). | 
| Az API által definiált hatókörök ( `oauth2Permissions` ) | A hatókör nevének maximális hossza 120 karakter <br><br> Nincs korlát * a definiált hatókörök számán | A hatókör nevének maximális hossza 120 karakter <br><br> Nincs korlát * a definiált hatókörök számán |  A hatókör nevének maximális hossza 40 karakter <br><br> Legfeljebb 100 hatókör definiálva | 
| Felhatalmazott ügyfélalkalmazások ( `preAuthorizedApplications` ) | Nincs korlát * | Nincs korlát * | Maximális 500 összesen <br><br> Legfeljebb 100 ügyfél-alkalmazás definiálva <br><br> Ügyfél által definiált maximális 30 hatókör | 
| appRoles | Támogatott <br> Nincs korlát * | Támogatott <br> Nincs korlát * | Nem támogatott | 
| Előcsatornás kijelentkezési URL-cím | https://localhost engedélyezett <br><br> `http` a séma használata nem engedélyezett <br><br> Legfeljebb 255 karakter hosszú lehet | https://localhost engedélyezett <br><br> `http` a séma használata nem engedélyezett <br><br> Legfeljebb 255 karakter hosszú lehet | <br><br> https://localhost engedélyezett, http://localhost sikertelen a MSA <br><br> Legfeljebb 255 karakter hosszú lehet <br><br> `http` a séma használata nem engedélyezett <br><br> A helyettesítő karakterek használata nem támogatott | 

* Az alkalmazás-objektum összes gyűjteményi tulajdonságában a 1000-es elemek globális korlátja látható.

## <a name="next-steps"></a>Következő lépések

- További információ az [alkalmazások regisztrálásáról](app-objects-and-service-principals.md).
- Az [alkalmazás jegyzékfájljának](reference-app-manifest.md)megismerése.
