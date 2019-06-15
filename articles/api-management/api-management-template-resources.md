---
title: Az Azure API Management-sablon erőforrásainak |} A Microsoft Docs
description: További információ a típusú erőforrásokat az Azure API Management fejlesztői portál sablonjainak érhető el.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 51a1b4c6-a9fd-4524-9e0e-03a9800c3e94
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 673dcbeb630899eebc328cd4fae16f7fe8f47a55
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60557885"
---
# <a name="azure-api-management-template-resources"></a>Az Azure API Management-sablon erőforrásainak
Az Azure API Management biztosít a következő típusú erőforrásokat használja, a fejlesztői portál sablonjainak.  
  
-   [Karakterlánc-erőforrások](#strings)  
  
-   [Erőforrások szimbóluma](#glyphs)  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="strings"></a> Karakterlánc-erőforrások  
 Az API Management biztosítja a karakterlánc-erőforrások széles köréhez használható a fejlesztői portálon. Ezek az erőforrások van honosítva API Management által támogatott nyelveket. Sablonok alapértelmezett készletét oldalfejlécben, címkéket és semmilyen állandó karakterláncokba, amelyeket a jelennek meg a fejlesztői portál ezeket az erőforrásokat használ. A sablonok egy karakterlánc-erőforrást használ, adja meg az erőforrás karakterlánc előtag karakterlánc neve követ, az alábbi példában látható módon.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 Az alábbi példa a termék lista sablonból, és megjeleníti a **termékek** az oldal tetején.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
A következő honosítási beállítások támogatottak:

| Területi beállítás    | Nyelv               |
|-----------|------------------------|
| „hu”      | "Angol"              |
| "cs"      | "Čeština"              |
| "de"      | "Deutsch"              |
| "es"      | "Español"              |
| "fr"      | "Franciaország"             |
| "hu"      | "Magyar"               |
| ""      | "Italiano"             |
| "ja-JP"   | "日本語"                |
| "ko"      | "한국어"                |
| "nl"      | "Nederlands"           |
| "pl."      | "Polski"               |
| "pt-br"   | "Portugál (Brazília)"   |
| "pt-pt"   | "Portugál (Portugália)" |
| "ru"      | "Русский"              |
| "sv"      | "Svenska"              |
| "tr"      | "Türkçe"               |
| "zh-hans" | "中文(简体)"           |
| "zh-hant" | "中文(繁體)"           |

 Tekintse meg a következő táblákat használható a fejlesztői portál sablonjainak erőforrásait. A tábla neve a karakterlánc-erőforrásokat, az adott tábla előtagot használja.  
  
-   [ApisStrings](#ApisStrings)  
  
-   [ApplicationListStrings](#ApplicationListStrings)  
  
-   [AppDetailsStrings](#AppDetailsStrings)  
  
-   [AppStrings](#AppStrings)  
  
-   [CommonResources](#CommonResources)  
  
-   [CommonStrings](#CommonStrings)  
  
-   [Dokumentáció](#Documentation)  
  
-   [ErrorPageStrings](#ErrorPageStrings)  
  
-   [IssuesStrings](#IssuesStrings)  
  
-   [NotFoundStrings](#NotFoundStrings)  
  
-   [ProductDetailsStrings](#ProductDetailsStrings)  
  
-   [ProductsStrings](#ProductsStrings)  
  
-   [ProviderInfoStrings](#ProviderInfoStrings)  
  
-   [SigninResources](#SigninResources)  
  
-   [SigninStrings](#SigninStrings)  
  
-   [SignupStrings](#SignupStrings)  
  
-   [SubscriptionListStrings](#SubscriptionListStrings)  
  
-   [SubscriptionStrings](#SubscriptionStrings)  
  
-   [UpdateProfileStrings](#UpdateProfileStrings)  
  
-   [UserProfile](#UserProfile)  
  
###  <a name="ApisStrings"></a> ApisStrings  
  
|Name (Név)|Text|  
|----------|----------|  
|PageTitleApis|API-k|  
  
###  <a name="AppDetailsStrings"></a> AppDetailsStrings  
  
|Name (Név)|Text|  
|----------|----------|  
|WebApplicationsDetailsTitle|Alkalmazás előnézete|  
|WebApplicationsRequirementsHeader|Követelmények|  
|WebApplicationsScreenshotAlt|Képernyőfelvétel|  
|WebApplicationsScreenshotsHeader|Képernyőképek|  
  
###  <a name="ApplicationListStrings"></a> ApplicationListStrings  
  
|Name (Név)|Text|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|Biztosan el kívánja távolítani az alkalmazást?|  
|WebDevelopersAppNotPublished|Not published|  
|WebDevelopersAppNotSubmitted|Nincs elküldve|  
|WebDevelopersAppTableCategoryHeader|Category|  
|WebDevelopersAppTableNameHeader|Name (Név)|  
|WebDevelopersAppTableStateHeader|Állapot|  
|WebDevelopersEditLink|Szerkesztés|  
|WebDevelopersRegisterAppLink|Alkalmazás regisztrálása|  
|WebDevelopersRemoveLink|Eltávolítás|  
|WebDevelopersSubmitLink|Küldés|  
|WebDevelopersYourApplicationsHeader|Az alkalmazások|  
  
###  <a name="AppStrings"></a> AppStrings  
  
|Name (Név)|Text|  
|----------|----------|  
|WebApplicationsHeader|Alkalmazások|  
  
###  <a name="CommonResources"></a> CommonResources  
  
|Name (Név)|Text|  
|----------|----------|  
|NoItemsToDisplay|Nincs eredmény.|  
|GeneralExceptionMessage|Valami nem megfelelő. Ez lehet egy ideiglenes problémáról vagy egy hiba. Kérjük próbálkozzon újra.|  
|GeneralJsonExceptionMessage|Valami nem megfelelő. Ez lehet egy ideiglenes problémáról vagy egy hiba. Kérjük frissítse az oldalt, és próbálkozzon újra.|  
|ConfirmationMessageUnsavedChanges|Néhány nem mentett módosítások vannak. Biztosan visszavonja és elveti a módosításokat szeretne?|  
|AzureActiveDirectory|Azure Active Directory|  
|HttpLargeRequestMessage|HTTP-kérés törzse túl nagy.|  
  
###  <a name="CommonStrings"></a> CommonStrings  
  
|Name (Név)|Text|  
|----------|----------|  
|ButtonLabelCancel|Mégse|  
|ButtonLabelSave|Mentés|  
|GeneralExceptionMessage|Valami nem megfelelő. Ez lehet egy ideiglenes problémáról vagy egy hiba. Kérjük próbálkozzon újra.|  
|NoItemsToDisplay|Nem találhatók elemek megjelenítéséhez.|  
|PagerButtonLabelFirst|Első|  
|PagerButtonLabelLast|Vezetéknév|  
|PagerButtonLabelNext|Következő lépés|  
|PagerButtonLabelPrevious|Előző|  
|PagerLabelPageNOfM|Oldal {0} , {1}|  
|PasswordTooShort|A jelszó túl rövid|  
|EmailAsPassword|Ne használja a e-mail-címét jelszóként|  
|PasswordSameAsUserName|A jelszó nem tartalmazhatja a felhasználónevet|  
|PasswordTwoCharacterClasses|Használjon eltérő karakterosztályokat|  
|PasswordTooManyRepetitions|Túl sokszor ismétlődik|  
|PasswordSequenceFound|A jelszó szekvenciákat tartalmaz|  
|PagerLabelPageSize|Oldalméret|  
|CurtainLabelLoading|Betöltése...|  
|TablePlaceholderNothingToDisplay|Nem szerepel megjeleníthető adat a kijelölt időszak és a hatókör|  
|ButtonLabelClose|Bezárás|  
  
###  <a name="Documentation"></a> Dokumentáció  
  
|Name (Név)|Text|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|Érvénytelen fejléc "{0}"|  
|WebDocumentationInvalidRequestErrorMessage|Érvénytelen a kérelem URL-címe|  
|TextboxLabelAccessToken|Hozzáférési jogkivonat *|  
|DropdownOptionPrimaryKeyFormat|Elsődleges –{0}|  
|DropdownOptionSecondaryKeyFormat|Másodlagos-{0}|  
|WebDocumentationSubscriptionKeyText|Az előfizetési kulcs|  
|WebDocumentationTemplatesAddHeaders|Adja hozzá a szükséges HTTP-fejlécek|  
|WebDocumentationTemplatesBasicAuthSample|Basic Authorization – minta|  
|WebDocumentationTemplatesCurlForBasicAuth|a Basic Authorization használata:--{username} felhasználó: {password}|  
|WebDocumentationTemplatesCurlValuesForPath|Adja meg az elérési út paramétereihez (így jelölve: {...}), az előfizetési kulcs és a lekérdezési paraméterek értékeinek értékek|  
|WebDocumentationTemplatesDeveloperKey|Az előfizetési kulcs megadása|  
|WebDocumentationTemplatesJavaApache|Ez a minta webhelyről () HTTP Components Apache HTTP-ügyfelét használja http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDocumentationTemplatesOptionalParams|Az opcionális paraméterek értékeinek megadása szükség szerint|  
|WebDocumentationTemplatesPhpPackage|Ez a minta a http_request2 típusú csomagot használja. (További információ: https://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|Adja meg az értékeket az elérési út paramétereihez (így jelölve: {...}) és a kérés törzsének, szükség esetén|  
|WebDocumentationTemplatesRequestBody|Kéréstörzs megadása|  
|WebDocumentationTemplatesRequiredParams|Adja meg az értékeket az alábbi kötelező paraméterekhez|  
|WebDocumentationTemplatesValuesForPath|Adja meg az értékeket az elérési út paramétereihez (így jelölve: {...})|  
|OAuth2AuthorizationEndpointDescription|Az engedélyezési végpont az erőforrás tulajdonosa használhatnak, és engedélyek beszerzésére szolgál.|  
|OAuth2AuthorizationEndpointName|engedélyezési végpont|  
|OAuth2TokenEndpointDescription|A jogkivonat-végpont segítségével az ügyfél hozzáférési jogkivonat beszerzése az engedélyezési típust szabályzatkérelem vagy a frissítési jogkivonatot.|  
|OAuth2TokenEndpointName|jogkivonat-végpont|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|< p\> az ügyfél kezdeményezi a folyamat során az erőforrás tulajdonosa felhasználói ügynököt az engedélyezési végpontra irányítja.  Az ügyfél tartalmazza az ügyfél-azonosítója, a megadott hatókörben, a helyi állapotot és a egy átirányítási URI-t, amelyhez az engedélyezési kiszolgáló elküldi a felhasználói ügynök vissza, ha hozzáférést (engedélyezett vagy tiltott).     < /p\> < p\> az engedélyezési kiszolgáló hitelesíti az erőforrás tulajdonosa (a felhasználói ügynök) n keresztül, és megállapítja, hogy az erőforrás tulajdonosa vagy az ügyfél-hozzáférési kérés elutasítása.     < /p\> < p\> feltéve, hogy az erőforrás tulajdonosa megadja a hozzáférést, az engedélyezési kiszolgáló visszairányítja a felhasználói ügynököt az ügyfélnek, az átirányítás, a megadott URI azonosító használatával korábbi (a kérésben vagy az ügyfél registrati során a).  Az átirányítási URI tartalmazza az engedélyezési kódot, és az ügyfél által korábban megadott összes helyi állapotot.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|< p\> Ha a felhasználó elutasítja a hozzáférési kérést, vagy ha a kérés érvénytelen, a rendszer értesíti az ügyfélt az átirányításhoz hozzáfűzött alábbi paraméterek használatával: < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Engedélyezési kérés|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|< p\> az ügyfélalkalmazás kell küldenie a felhasználót az engedélyezési végpont az OAuth-folyamat kezdeményezése céljából.          Az engedélyezési végponton a felhasználó elvégzi a hitelesítést, és ezután vagy megtagadja a hozzáférést az alkalmazáshoz.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|< p\> feltéve, hogy az erőforrás tulajdonosa megadja a hozzáférést, az engedélyezési kiszolgáló átirányítja a felhasználói ügynököt az ügyfélhez, az átirányítás, a megadott URI azonosító használata korábbi (a kérésben vagy ügyfél-regisztráció során).  Az átirányítási URI tartalmazza az engedélyezési kódot, és az ügyfél által korábban megadott összes helyi állapotot. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|< p\> az ügyfél hozzáférési jogkivonatot kér az engedélyezési kiszolgáló: "% s jogkivonat-végpont az előző lépésben kapott engedélyezési kód együtt.  A kérés küldésekor az ügyfél elvégzi a hitelesítést az engedélyezési kiszolgálón.  Az ügyfél tartalmazza az átirányítási URI-t az ellenőrzéshez az engedélyezési kód beszerzésére szolgál. < /p\> < p\> az engedélyezési kiszolgáló hitelesíti az ügyfelet, érvényesíti az engedélyezési kódot, és biztosítja, hogy az átirányítási URI kapott megegyezik-e (C) lépésben az ügyfél átirányítására használt URI-ja.  Érvényes, ha az engedélyezési kiszolgáló válaszként vissza egy hozzáférési jogkivonatot és szükség esetén a frissítési jogkivonatot. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|< p\> a kérés ügyfél-hitelesítése sikertelen volt, vagy nem érvényes, ha az engedélyezési kiszolgáló válaszol a HTTP 400 (hibás kérés) állapotkódot (kivéve, ha másképpen megadva), és a válasz az alábbi paramétereket. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|< p\> az ügyfél kérést küld a jogkivonat-végpont az alábbi paramétereket az "application/x-www-form-urlencoded" formátum használata és az UTF-8 a HTTP-kérelem entitástörzsében küldésével. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|< p\> az engedélyezési kiszolgáló kiállítja a hozzáférési jogkivonat és opcionális frissítési jogkivonat, és a 200 (OK) állapotkódot a HTTP-válasz törzsében a következő paraméterek hozzáadásával a válasz entitástörzséhez. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|< p\> az engedélyezési kiszolgáló hitelesíti az ügyfél és a egy hozzáférési jogkivonatot kér a jogkivonat-végpont. < /p\> < p\> az engedélyezési kiszolgáló hitelesíti az ügyfelet, és érvényesség esetén kiállítja a hozzáférési jogkivonatot. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> , ha a kérés ügyfél-hitelesítése sikertelen, vagy nem érvényes az engedélyezési kiszolgáló válaszol a HTTP 400 (hibás kérés) állapotkódot (kivéve, ha másképpen megadva), és a válasz az alábbi paramétereket. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> az ügyfél kérést küld a jogkivonat-végpont az alábbi paramétereket az "application/x-www-form-urlencoded" formátum használata és az UTF-8 a HTTP-kérelem entitástörzsében hozzáadásával. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> Ha a hozzáférési jogkivonat kérése érvényes és engedélyezett, az engedélyezési kiszolgáló egy hozzáférési jogkivonatot és szükség esetén a frissítési jogkivonatot, és a válasz entitástörzséhez a 200-as HTTP-válasz törzsében a következő paraméterek hozzáadásával  (OK) állapotkódot. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|< p\> az ügyfél a folyamatot kezdeményező az erőforrás tulajdonosa megkérnek: "% s felhasználói ügynököt az engedélyezési végpontra.  Az ügyfél tartalmazza az ügyfél-azonosítója, a megadott hatókörben, a helyi állapotot és a egy átirányítási URI-t, amelyhez az engedélyezési kiszolgáló elküldi a felhasználói ügynök vissza, ha hozzáférést (engedélyezett vagy tiltott). < /p\> < p\> az engedélyezési kiszolgáló hitelesíti az erőforrás tulajdonosa (a felhasználói ügynök) n keresztül, és megállapítja, hogy az erőforrás tulajdonosa vagy az ügyfél elutasítása: "% s hozzáférési kérelem. < /p\> < p\> feltéve, hogy az erőforrás tulajdonosa megadja a hozzáférést, az engedélyezési kiszolgáló visszairányítja a felhasználói ügynököt az ügyfélnek az átirányítási URI-t korábban megadott használatával.  Az átirányítási URI az URI töredék a hozzáférési jogkivonatot tartalmazza. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|< p\> , ha az erőforrás tulajdonosa elutasítja a hozzáférési kérést, vagy ha a kérelem egy hiányzó vagy érvénytelen átirányítási URI-t leszámítva bármilyen okból meghiúsul, az engedélyezési kiszolgáló értesíti az ügyfélt az alábbi paramétereket ad hozzá a töredék összetevő o az átirányítási URI-t az "application/x-www-form-urlencoded" formátumban. f. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|< p\> az ügyfélalkalmazás kell küldenie a felhasználót az engedélyezési végpont az OAuth-folyamat kezdeményezése céljából.      Az engedélyezési végponton a felhasználó elvégzi a hitelesítést, és ezután vagy megtagadja a hozzáférést az alkalmazáshoz. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|< p\> az erőforrás tulajdonosa jóváhagyja a hozzáférési kérést, ha az engedélyezési kiszolgáló kiad egy jogkivonatot, és elküldi azt az ügyfél a következő paramétereket ad hozzá az átirányítási URI-t használja az "application/x-www töredék összetevője -űrlap - kulcstárolókat"formátumban. </p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|Az engedélyezési kód folyamata (pl.: webes alkalmazások PHP, Java, Python, Ruby, ASP.NET, stb.) hitelesítő adatok titkosságának alkalmas ügyfelek van optimalizálva.|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Engedélyezési kód|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|Az ügyfélhitelesítő adatok folyamata olyan esetekben, ahol az ügyfél (az alkalmazás) kér az ellenőrzése alatt a védett erőforrásokhoz való hozzáférés megfelelő. Az ügyfél minősül erőforrás tulajdonosának, ezért végfelhasználói beavatkozásra szükség.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Ügyfélhitelesítő adatok|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|Implicit folyamat adott átirányítási URI működtetéséhez szükséges hitelesítő adatok titkosságát nem alkalmas ügyfelek számára van optimalizálva. Ezek az ügyfelek általában egy parancsnyelv, például a JavaScript használatával vannak megvalósítva.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Implicit engedélyezés|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|Erőforrás tulajdonosának jelszavas hitelesítő adatainak folyamata olyan esetekben, ahol az erőforrás tulajdonosa megbízhatósági kapcsolattal rendelkezik az ügyféllel (az alkalmazás), például az eszköz operációs rendszere vagy a magas jogosultsági szintű alkalmazások megfelelő. Ez a folyamat ideális az ügyfelek képesek beszerezni az erőforrás-tulajdonos hitelesítő adatait (felhasználónév és jelszó, jellemzően interaktív űrlap használatával).|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Erőforrás tulajdonosának jelszavas hitelesítése|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|< p\> az erőforrás tulajdonosa az ügyfél kínál a felhasználónévvel és jelszóval. < /p\> < p\> az ügyfél hozzáférési jogkivonatot kér az engedélyezési kiszolgáló: "% s jogkivonat-végpont által, beleértve az erőforrás tulajdonosától kapott hitelesítő adatokat.  A kérés küldésekor az ügyfél elvégzi a hitelesítést az engedélyezési kiszolgálón. < /p\> < p\> az engedélyezési kiszolgáló hitelesíti az ügyfelet, és ellenőrzi a az erőforrás-tulajdonos hitelesítő adatokat, és érvényesség esetén kiállítja a hozzáférési jogkivonatot. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> , ha a kérés ügyfél-hitelesítése sikertelen, vagy nem érvényes az engedélyezési kiszolgáló válaszol a HTTP 400 (hibás kérés) állapotkódot (kivéve, ha másképpen megadva), és a válasz az alábbi paramétereket. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> az ügyfél kérést küld a jogkivonat-végpont az alábbi paramétereket az "application/x-www-form-urlencoded" formátum használata és az UTF-8 a HTTP-kérelem entitástörzsében hozzáadásával. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> Ha a hozzáférési jogkivonat kérése érvényes és engedélyezett, az engedélyezési kiszolgáló egy hozzáférési jogkivonatot és szükség esetén a frissítési jogkivonatot, és a válasz entitástörzséhez entitástörzsében 20 a HTTP-válasz a következő paraméterek hozzáadásával 0 (OK) állapotkódot. </p\>|  
|OAuth2Step_AccessTokenRequest_Name|Hozzáférési jogkivonat kérése|  
|OAuth2Step_AuthorizationRequest_Name|Engedélyezési kérés|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|SZÜKSÉGES. A hozzáférési jogkivonatot az engedélyezési kiszolgáló által kiadott.|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|SZÜKSÉGES. A hozzáférési jogkivonatot az engedélyezési kiszolgáló által kiadott.|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|SZÜKSÉGES. A hozzáférési jogkivonatot az engedélyezési kiszolgáló által kiadott.|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|SZÜKSÉGES. A hozzáférési jogkivonatot az engedélyezési kiszolgáló által kiadott.|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|SZÜKSÉGES. Ügyfél-azonosítója.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|Ha az ügyfél nem végez hitelesítést az engedélyezési kiszolgáló szükséges.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|SZÜKSÉGES. Az ügyfél-azonosítója.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|SZÜKSÉGES. Az engedélyezési kiszolgáló által generált engedélyezési kód.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|SZÜKSÉGES. Az engedélyezési kiszolgálótól kapott engedélyezési kód.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|NEM KÖTELEZŐ. Emberek számára olvasható ASCII szöveges kezeléséről további információt.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|NEM KÖTELEZŐ. Emberek számára olvasható ASCII szöveges kezeléséről további információt.|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|NEM KÖTELEZŐ. Emberek számára olvasható ASCII szöveges kezeléséről további információt.|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|NEM KÖTELEZŐ. Emberek számára olvasható ASCII szöveges kezeléséről további információt.|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|NEM KÖTELEZŐ. Emberek számára olvasható ASCII szöveges kezeléséről további információt.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|NEM KÖTELEZŐ. A hibával kapcsolatos információkat az emberek számára olvasható weblap azonosító URI.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|NEM KÖTELEZŐ. A hibával kapcsolatos információkat az emberek számára olvasható weblap azonosító URI.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|NEM KÖTELEZŐ. A hibával kapcsolatos információkat az emberek számára olvasható weblap azonosító URI.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|NEM KÖTELEZŐ. A hibával kapcsolatos információkat az emberek számára olvasható weblap azonosító URI.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|NEM KÖTELEZŐ. A hibával kapcsolatos információkat az emberek számára olvasható weblap azonosító URI.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|SZÜKSÉGES. Egy egyetlen ASCII-hibakód a következők közül: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|SZÜKSÉGES. Egy egyetlen ASCII-hibakód a következők közül: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|SZÜKSÉGES. Egy egyetlen ASCII-hibakód a következők közül: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|SZÜKSÉGES. Egy egyetlen ASCII-hibakód a következők közül: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|SZÜKSÉGES. Egy egyetlen ASCII-hibakód a következők közül: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|AJÁNLOTT. Az élettartam (másodperc), a hozzáférési jogkivonat.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|AJÁNLOTT. Az élettartam (másodperc), a hozzáférési jogkivonat.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|AJÁNLOTT. Az élettartam (másodperc), a hozzáférési jogkivonat.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|AJÁNLOTT. Az élettartam (másodperc), a hozzáférési jogkivonat.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|SZÜKSÉGES. A "authorization_code" értéket kell beállítani.|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|SZÜKSÉGES. A "client_credentials" értéket kell beállítani.|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|SZÜKSÉGES. A "password" értéket kell beállítani.|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|SZÜKSÉGES. Az erőforrás-tulajdonos jelszava.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|NEM KÖTELEZŐ. Az átirányítási végpont URI-t egy abszolút URI Azonosítónak kell lennie.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|SZÜKSÉGES, ha a "redirect_uri" paraméter szerepelt az engedélyezési kérésben, és az értékeknek azonosnak kell lenniük.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|NEM KÖTELEZŐ. Az átirányítási végpont URI-t egy abszolút URI Azonosítónak kell lennie.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|NEM KÖTELEZŐ. A frissítési jogkivonat, amely új hozzáférési jogkivonatok beszerzésére használható.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|NEM KÖTELEZŐ. A frissítési jogkivonat, amely új hozzáférési jogkivonatok beszerzésére használható.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|NEM KÖTELEZŐ. A frissítési jogkivonat, amely új hozzáférési jogkivonatok beszerzésére használható.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|SZÜKSÉGES. A "code" értéket kell beállítani.|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|SZÜKSÉGES. Értéket kell megadni a "token".|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|NEM KÖTELEZŐ. A hozzáférési kérés hatóköre.|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|Nem kötelező, ha megegyezik az ügyfél; által kért hatókörrel Ellenkező esetben kötelező.|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|NEM KÖTELEZŐ. A hozzáférési kérés hatóköre.|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|Nem kötelező, ha megegyezik az ügyfél; által kért hatókörrel Ellenkező esetben kötelező.|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|NEM KÖTELEZŐ. A hozzáférési kérés hatóköre.|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|Nem kötelező, ha megegyezik az ügyfél; által kért hatókörrel Ellenkező esetben kötelező.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|NEM KÖTELEZŐ. A hozzáférési kérés hatóköre.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Nem kötelező, ha megegyezik az ügyfél; által kért hatókörrel Ellenkező esetben kötelező.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|SZÜKSÉGES, ha a "state" paraméter szerepelt az ügyfél engedélyezési kérésben.  Az ügyféltől kapott pontos érték.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|AJÁNLOTT. A kérelem és a visszahívás közötti állapot karbantartására használ az ügyfél által használt átlátszatlan érték.  Az engedélyezési kiszolgáló tartalmazza ezt az értéket, amikor az átirányítás, a felhasználói ügynököt az ügyfélhez.  A paraméter használandó, a webhelyközi kérések hamisításának megakadályozása.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|SZÜKSÉGES, ha a "state" paraméter szerepelt az ügyfél engedélyezési kérésben.  Az ügyféltől kapott pontos érték.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|SZÜKSÉGES, ha a "state" paraméter szerepelt az ügyfél engedélyezési kérésben.  Az ügyféltől kapott pontos érték.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|AJÁNLOTT. A kérelem és a visszahívás közötti állapot karbantartására használ az ügyfél által használt átlátszatlan érték.  Az engedélyezési kiszolgáló tartalmazza ezt az értéket, amikor az átirányítás, a felhasználói ügynököt az ügyfélhez.  A paraméter használandó, a webhelyközi kérések hamisításának megakadályozása.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|SZÜKSÉGES, ha a "state" paraméter szerepelt az ügyfél engedélyezési kérésben.  Az ügyféltől kapott pontos érték.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|SZÜKSÉGES. A kiállított biztonsági jogkivonat típusa.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|SZÜKSÉGES. A kiállított biztonsági jogkivonat típusa.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|SZÜKSÉGES. A kiállított biztonsági jogkivonat típusa.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|SZÜKSÉGES. A kiállított biztonsági jogkivonat típusa.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|SZÜKSÉGES. Az erőforrás-tulajdonos felhasználóneve.|  
|OAuth2UnsupportedTokenType|Jogkivonat-típus "{0}" nem támogatott.|  
|OAuth2InvalidState|Érvénytelen válasz érkezett az engedélyezési kiszolgáló|  
|OAuth2GrantType_AuthorizationCode|engedélyezési kód|  
|OAuth2GrantType_Implicit|Implicit|  
|OAuth2GrantType_ClientCredentials|Ügyfél-hitelesítő adatok|  
|OAuth2GrantType_ResourceOwnerPassword|Erőforrás tulajdonosának jelszava|  
|WebDocumentation302Code|302 Found|  
|WebDocumentation400Code|400 (hibás kérés)|  
|OAuth2SendingMethod_AuthHeader|Engedélyeztetési fejléc|  
|OAuth2SendingMethod_QueryParam|Lekérdezési paraméter|  
|OAuth2AuthorizationServerGeneralException|Hiba történt az objektumon keresztüli hozzáférés engedélyezésekor {0}|  
|OAuth2AuthorizationServerCommunicationException|Nem sikerült létrehozni az engedélyezési kiszolgáló HTTP-kapcsolat, vagy váratlanul megszűnt.|  
|WebDocumentationOAuth2GeneralErrorMessage|Váratlan hiba történt.|  
|AuthorizationServerCommunicationException|Engedélyezési kiszolgálón kommunikációs kivétel történt. Lépjen kapcsolatba a rendszergazdával.|  
|TextblockSubscriptionKeyHeaderDescription|Előfizetési kulcs, amely hozzáférést biztosít az API-ra. Található a < a href ='/ developer "\>profil < /a\>.|  
|TextblockOAuthHeaderDescription|OAuth 2.0 hozzáférési jogkivonat érkezett < i\>{0}< /i\>. Támogatott engedélyezési típusok: < i\>{1}< /i\>.|  
|TextblockContentTypeHeaderDescription|Az API-nak küldött törzs médiatípusa.|  
|ErrorMessageApiNotAccessible|A meghívni próbált API jelenleg nem érhető el. Lépjen kapcsolatba az API közzétevőjével < a href = "/ issues"\>ide < /a\>.|  
|ErrorMessageApiTimedout|A meghívni próbált API válasz visszatéréshez a szokásosnál tovább tart. Lépjen kapcsolatba az API közzétevőjével < a href = "/ issues"\>ide < /a\>.|  
|BadRequestParameterExpected|""{0}"paraméternek"|  
|TooltipTextDoubleClickToSelectAll|Kattintson duplán az összes kijelöléséhez.|  
|TooltipTextHideRevealSecret|Megjelenítése/elrejtése|  
|ButtonLinkOpenConsole|Kipróbálom|  
|SectionHeadingRequestBody|A kérés törzse|  
|SectionHeadingRequestParameters|A kérés paraméterei|  
|SectionHeadingRequestUrl|Kérés URL-címe|  
|SectionHeadingResponse|Válasz|  
|SectionHeadingRequestHeaders|Kérelemfejlécek|  
|FormLabelSubtextOptional|Nem kötelező|  
|SectionHeadingCodeSamples|Kódminták|  
|TextblockOpenidConnectHeaderDescription|OpenID Connect azonosítói jogkivonat érkezett < i\>{0}< /i\>. Támogatott engedélyezési típusok: < i\>{1}< /i\>.|  
  
###  <a name="ErrorPageStrings"></a> ErrorPageStrings  
  
|Name (Név)|Text|  
|----------|----------|  
|LinkLabelBack|vissza|  
|LinkLabelHomePage|Kezdőlap|  
|LinkLabelSendUsEmail|E-mail küldése|  
|PageTitleError|Sajnos hiba történt a kért lap kiszolgálásakor|  
|TextblockPotentialCauseIntermittentIssue|Ez lehet egy átmeneti hozzáférési hibát, amely már nem látható.|  
|TextblockPotentialCauseOldLink|A hivatkozás, amelyre kattintott, elavult és nem a megfelelő helyre mutat.|  
|TextblockPotentialCauseTechnicalProblem|Előfordulhatnak technikai hiba található a rendszerünkben.|  
|TextblockPotentialSolutionRefresh|Próbálja meg frissíteni az oldalt.|  
|TextblockPotentialSolutionStartOver|Az újrakezdés az {0}.|  
|TextblockPotentialSolutionTryAgain|Nyissa meg {0} , és próbálja meg újra végrehajtani a műveletet.|  
|TextReportProblem|{0} amely leírja, mi történt, és áttekintjük, hogy, amint tudunk.|  
|TitlePotentialCause|Lehetséges ok|  
|TitlePotentialSolution|Esetleg csak átmeneti hibáról, néhány az alábbiakkal próbálkozhat|  
  
###  <a name="IssuesStrings"></a> IssuesStrings  
  
|Name (Név)|Text|  
|----------|----------|  
|WebIssuesIndexTitle|Hibák|  
|WebIssuesNoActiveSubscriptions|Ön nem rendelkezik aktív előfizetéssel. Elő kell fizetnie egy termékre, hibát.|  
|WebIssuesNotSignin|Nem jelentkezett be. Adjon {0} jelentéséhez vagy hozzászólás írásához.|  
|WebIssuesReportIssueButton|Probléma bejelentése|  
|WebIssuesSignIn|bejelentkezés|  
|WebIssuesStatusReportedBy|Állapot: {0} &#124; által jelentett {1}|  
  
###  <a name="NotFoundStrings"></a> NotFoundStrings  
  
|Name (Név)|Text|  
|----------|----------|  
|LinkLabelHomePage|Kezdőlap|  
|LinkLabelSendUsEmail|E-mail küldése|  
|PageTitleNotFound|Sajnos nem találjuk a keresett lap|  
|TextblockPotentialCauseMisspelledUrl|Előfordulhat, hogy elgépelte az URL-címet, ha a beírt.|  
|TextblockPotentialCauseOldLink|A hivatkozás, amelyre kattintott, elavult és nem a megfelelő helyre mutat.|  
|TextblockPotentialSolutionRetype|Próbálja meg újra beírni az URL-címet.|  
|TextblockPotentialSolutionStartOver|Az újrakezdés az {0}.|  
|TextReportProblem|{0} amely leírja, mi történt, és áttekintjük, hogy, amint tudunk.|  
|TitlePotentialCause|Lehetséges ok|  
|TitlePotentialSolution|Lehetséges megoldás|  
  
###  <a name="ProductDetailsStrings"></a> ProductDetailsStrings  
  
|Name (Név)|Text|  
|----------|----------|  
|WebProductsAgreement|Ha előfizet {0} termék elfogadom a `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>`.|  
|WebProductsLegalTermsLink|Használati feltételek|  
|WebProductsSubscribeButton|Feliratkozás|  
|WebProductsUsageLimitsHeader|Használati korlátozások|  
|WebProductsYouAreNotSubscribed|A termékre előfizetett.|  
|WebProductsYouRequestedSubscription|A termékre való előfizetést igényelt.|  
|ErrorYouNeedToAgreeWithLegalTerms|A folytatás előtt el kell fogadnia a használati feltételeket.|  
|ButtonLabelAddSubscription|Előfizetés hozzáadása|  
|LinkLabelChangeSubscriptionName|Módosítása|  
|ButtonLabelConfirm|Megerősítés|  
|TextblockMultipleSubscriptionsCount|Rendelkezik {0} előfizetés a termékre:|  
|TextblockSingleSubscriptionsCount|Rendelkezik {0} előfizetés a termékre:|  
|TextblockSingleApisCount|Ez a termék {0} API:|  
|TextblockMultipleApisCount|Ez a termék {0} API-kat:|  
|TextblockHeaderSubscribe|Előfizetés a termékre|  
|TextblockSubscriptionDescription|Egy új előfizetést hoz létre a következő:|  
|TextblockSubscriptionLimitReached|Elérte az előfizetési korlátot.|  
  
###  <a name="ProductsStrings"></a> ProductsStrings  
  
|Name (Név)|Text|  
|----------|----------|  
|PageTitleProducts|Products|  
  
###  <a name="ProviderInfoStrings"></a> ProviderInfoStrings  
  
|Name (Név)|Text|  
|----------|----------|  
|TextboxExternalIdentitiesDisabled|Bejelentkezés le van tiltva a rendszergazdák által jelenleg.|  
|TextboxExternalIdentitiesSigninInvitation|Vagy bejelentkezés a következővel:|  
|TextboxExternalIdentitiesSigninInvitationPrimary|Bejelentkezés a következővel:|  
  
###  <a name="SigninResources"></a> SigninResources  
  
|Name (Név)|Text|  
|----------|----------|  
|PrincipalNotFound|Rendszerbiztonsági tag nem található vagy az aláírás érvénytelen|  
|ErrorSsoAuthenticationFailed|Az SSO-hitelesítés nem sikerült|  
|ErrorSsoAuthenticationFailedDetailed|Érvénytelen megadott biztonsági jogkivonat vagy aláírása nem ellenőrizhető.|  
|ErrorSsoTokenInvalid|Az SSO-jogkivonat érvénytelen|  
|ValidationErrorSpecificEmailAlreadyExists|E-mail "{0}" már regisztrálva van|  
|ValidationErrorSpecificEmailInvalid|E-mail "{0}" érvénytelen|  
|ValidationErrorPasswordInvalid|Jelszó érvénytelen. Javítsa a hibákat, és próbálkozzon újra.|  
|PropertyTooShort|{0} túl rövid|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|Érvénytelen e-mail cím.|  
|ValidationMessageNewPasswordConfirmationRequired|Az új jelszó megerősítése|  
|ValidationErrorPasswordConfirmationRequired|Megerősítésre szolgáló jelszó üres|  
|WebAuthenticationEmailChangeNotice|Változás megerősítő e-mailt a jelszómódosításra vonatkozó van {0}. Kövesse az abban található utasításokat az új e-mail cím megerősítéséhez. Ha az e-mail nem érkezik meg a postafiókjába pár percen belül, ellenőrizze a Levélszemét mappa.|  
|WebAuthenticationEmailChangeNoticeHeader|Az e-mailek módosítási kérésének feldolgozása sikerült|  
|WebAuthenticationEmailChangeNoticeTitle|E-mailek módosítási kérése|  
|WebAuthenticationEmailHasBeenRevertedNotice|E-mail-cím már létezik. Kérést visszavontuk|  
|ValidationErrorEmailAlreadyExists|E-mail-cím már létezik.|  
|ValidationErrorEmailInvalid|Érvénytelen e-mail cím|  
|TextboxLabelEmail|E-mail|  
|ValidationErrorEmailRequired|E-mail cím megadása kötelező.|  
|WebAuthenticationErrorNoticeHeader|Hiba|  
|WebAuthenticationFieldLengthErrorMessage|{0} a maximális hossza {1}|  
|TextboxLabelEmailFirstName|Utónév|  
|ValidationErrorFirstNameRequired|Keresztnév megadása kötelező.|  
|ValidationErrorFirstNameInvalid|Érvénytelen Keresztnév|  
|NoticeInvalidInvitationToken|Vegye figyelembe, hogy megerősítésben szereplő hivatkozások csak 48 óráig érvényes. Ha ezen az időtartamon belül, ellenőrizze, hogy a hivatkozás helyes-e. Ha a hivatkozás lejárt, ismételje meg a műveletet ellenőrizheti.|  
|NoticeHeaderInvalidInvitationToken|Érvénytelen meghívói biztonsági jogkivonat|  
|NoticeTitleInvalidInvitationToken|Megerősítési hiba|  
|WebAuthenticationLastNameInvalidErrorMessage|Érvénytelen Vezetéknév|  
|TextboxLabelEmailLastName|Vezetéknév|  
|ValidationErrorLastNameRequired|Vezetéknév megadása kötelező.|  
|WebAuthenticationLinkExpiredNotice|Ön elküldött megerősítési hivatkozás lejárt. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpired|A jelszó-visszaállítási hivatkozás érvénytelen vagy lejárt.|  
|WebAuthenticationLinkExpiredNoticeTitle|Hivatkozás elküldve|  
|WebAuthenticationNewPasswordLabel|Új jelszó|  
|ValidationMessageNewPasswordRequired|Új jelszó megadása kötelező.|  
|TextboxLabelNotificationsSenderEmail|Az értesítések feladói e-mail|  
|TextboxLabelOrganizationName|Szervezet neve|  
|WebAuthenticationOrganizationRequiredErrorMessage|Szervezet neve nincs megadva|  
|WebAuthenticationPasswordChangedNotice|A jelszó frissítése sikerült|  
|WebAuthenticationPasswordChangedNoticeTitle|Jelszó frissítve|  
|WebAuthenticationPasswordCompareErrorMessage|A jelszavak nem egyeznek|  
|WebAuthenticationPasswordConfirmLabel|Jelszó megerősítése|  
|ValidationErrorPasswordInvalidDetailed|A jelszava túl gyenge.|  
|WebAuthenticationPasswordLabel|Jelszó|  
|ValidationErrorPasswordRequired|Jelszó megadása kötelező.|  
|WebAuthenticationPasswordResetSendNotice|Módosítsa jelszavát megerősítő e-mailt a módszer van {0}. Kövesse az e-mailben található utasításokat a jelszómódosítási folyamat folytatásához.|  
|WebAuthenticationPasswordResetSendNoticeHeader|A jelszó igénylésére vonatkozó kérés feldolgozása sikerült|  
|WebAuthenticationPasswordResetSendNoticeTitle|Új jelszó kérése|  
|WebAuthenticationRequestNotFoundNotice|A kérelem nem található|  
|WebAuthenticationSenderEmailRequiredErrorMessage|Értesítések feladói e-mail-címe üres|  
|WebAuthenticationSigninPasswordLabel|A jelszó beírásával erősítse meg a módosítást|  
|WebAuthenticationSignupConfirmNotice|Regisztrációs megerősítő e-mailt az felé {0}. < br /\> kövesse az e-mailben található utasításokat a fiók aktiválásához. < br /\> az e-mailt nem érkezik meg a postafiókjába pár percen belül, ha Ellenőrizze a Levélszemét mappát.|  
|WebAuthenticationSignupConfirmNoticeHeader|A fiók sikeresen létrejött.|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|Regisztrációs megerősítő e-mailt újraküldtük|  
|WebAuthenticationSignupConfirmNoticeTitle|Fiók létrehozva|  
|WebAuthenticationTokenRequiredErrorMessage|A token üres|  
|WebAuthenticationUserAlreadyRegisteredNotice|Úgy tűnik, ezzel az e-mail a felhasználó már regisztrálva van a rendszerben. Ha elfelejtette a jelszavát, próbálja meg visszaállítani azt, vagy forduljon az ügyfélszolgálathoz.|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|Már regisztrált felhasználó|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|Már regisztrálva van|  
|ButtonLabelChangePassword|Jelszó módosítása|  
|ButtonLabelChangeAccountInfo|Fiókinformációk módosítása|  
|ButtonLabelCloseAccount|Fiók bezárása|  
|WebAuthenticationInvalidCaptchaErrorMessage|Megadott szöveg nem egyezik a képen látható szöveget. Kérjük, próbálkozzon újból.|  
|ValidationErrorCredentialsInvalid|E-mailben vagy a jelszó érvénytelen. Javítsa a hibákat, és próbálkozzon újra.|  
|WebAuthenticationRequestIsNotValid|Kérés nem érvényes|  
|WebAuthenticationUserIsNotConfirm|Bejelentkezési kísérlet előtt erősítse meg a regisztrációt.|  
|WebAuthenticationInvalidEmailFormated|Érvénytelen e-mail-cím: {0}|  
|WebAuthenticationUserNotFound|A felhasználó nem található|  
|WebAuthenticationTenantNotRegistered|A fiókja tagja egy Azure Active Directory-bérlőjéhez, amely nem jogosult a portál elérésére.|  
|WebAuthenticationAuthenticationFailed|Hitelesítés nem sikerült.|  
|WebAuthenticationGooglePlusNotEnabled|Hitelesítés nem sikerült. Ha Ön hitelesítette az alkalmazást majd forduljon a rendszergazdához, győződjön meg arról, hogy a Google-hitelesítés megfelelően van konfigurálva.|  
|ValidationErrorAllowedTenantIsRequired|Az engedélyezett bérlő megadása kötelező|  
|ValidationErrorTenantIsNotValid|Az Azure Active Directory-bérlőhöz "{0}" nem érvényes.|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|Jelentkezzen be a {0} fiók|  
|WebAuthenticationUserLimitNotice|Ez a szolgáltatás elérte az engedélyezett felhasználók maximális számát. Adjon `<a href="mailto:{0}"\>contact the administrator</a\>` frissítse a szolgáltatást, és a felhasználói regisztráció újbóli engedélyezéséhez.|  
|WebAuthenticationUserLimitNoticeHeader|Felhasználói regisztráció le van tiltva|  
|WebAuthenticationUserLimitNoticeTitle|Felhasználói regisztráció le van tiltva|  
|WebAuthenticationUserRegistrationDisabledNotice|A felhasználói regisztrációt a rendszergazda letiltotta. Jelentkezzen be a külső identitásszolgáltató.|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|Felhasználói regisztráció le van tiltva|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|Felhasználói regisztráció le van tiltva|  
|WebAuthenticationSignupPendingConfirmationNotice|A fiók létrehozásának befejezése előtt ellenőriznünk kell a e-mail címét. E-mailt elküldtük Önnek {0}. Kövesse az e-mailben található utasításokat a fiók aktiválásához. Ha az e-mail nem érkezik meg a postafiókjába pár percen belül, ellenőrizze a Levélszemét mappa.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|Egy meg nem erősített fiókot találtunk az e-mail-cím {0}. A fiók létrehozásának befejezéséhez ellenőrizze az e-mail-címet kell. E-mailt elküldtük Önnek {0}. Kövesse az e-mailben található utasításokat a fiók aktiválásához. Ha az e-mail nem érkezik meg a postafiókjába pár percen belül, ellenőrizze a Levélszemét mappa|  
|WebAuthenticationSignupConfirmationAlmostDone|Majdnem kész|  
|WebAuthenticationSignupConfirmationEmailSent|E-mailt elküldtük Önnek {0}. Kövesse az e-mailben található utasításokat a fiók aktiválásához. Ha az e-mail nem érkezik meg a postafiókjába pár percen belül, ellenőrizze a Levélszemét mappa.|  
|WebAuthenticationEmailSentNotificationMessage|Sikerült elküldeni e-mailben {0}|  
|WebAuthenticationNoAadTenantConfigured|Nincs konfigurálva a szolgáltatáshoz Azure Active Directory-bérlővel.|  
|CheckboxLabelUserRegistrationTermsConsentRequired|Elfogadom a `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`.|  
|TextblockUserRegistrationTermsProvided|Tekintse át `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`|  
|DialogHeadingTermsOfUse|Használati feltételek|  
|ValidationMessageConsentNotAccepted|A folytatás előtt el kell fogadnia a használati feltételeket.|  
  
###  <a name="SigninStrings"></a> SigninStrings  
  
|Name (Név)|Text|  
|----------|----------|  
|WebAuthenticationForgotPassword|Elfelejtette jelszavát?|  
|WebAuthenticationIfAdministrator|Ha rendszergazdaként kell bejelentkeznie a `<a href="{0}"\>here</a\>`.|  
|WebAuthenticationNotAMember|Még nem tag? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|Emlékezzen rám ezen a számítógépen|  
|WebAuthenticationSigininWithPassword|Jelentkezzen be a felhasználónevét és jelszavát|  
|WebAuthenticationSigninTitle|Bejelentkezés|  
|WebAuthenticationSignUpNow|Regisztráljon most|  
  
###  <a name="SignupStrings"></a> SignupStrings  
  
|Name (Név)|Text|  
|----------|----------|  
|PageTitleSignup|Regisztráció|  
|WebAuthenticationAlreadyAMember|Már tag?|  
|WebAuthenticationCreateNewAccount|Új API Management-fiók létrehozása|  
|WebAuthenticationSigninNow|Jelentkezzen be most|  
|ButtonLabelSignup|Regisztráció|  
  
###  <a name="SubscriptionListStrings"></a> SubscriptionListStrings  
  
|Name (Név)|Text|  
|----------|----------|  
|SubscriptionCancelConfirmation|Biztos, hogy szeretné-e az előfizetést?|  
|SubscriptionRenewConfirmation|Biztos, hogy szeretné-e az előfizetés meghosszabbításához?|  
|WebDevelopersManageSubscriptions|Előfizetések kezelése|  
|WebDevelopersPrimaryKey|Elsődleges kulcs|  
|WebDevelopersRegenerateLink|Újragenerálás|  
|WebDevelopersSecondaryKey|Másodlagos kulcs|  
|ButtonLabelShowKey|Megjelenítés|  
|ButtonLabelRenewSubscription|Frissítés|  
|WebDevelopersSubscriptionRequested|Kérelem dátuma {0}|  
|WebDevelopersSubscriptionRequestedState|A kért|  
|WebDevelopersSubscriptionTableNameHeader|Name (Név)|  
|WebDevelopersSubscriptionTableStateHeader|Állapot|  
|WebDevelopersUsageStatisticsLink|Analytics-jelentések|  
|WebDevelopersYourSubscriptions|Az előfizetések|  
|SubscriptionPropertyLabelRequestedDate|Kérelem dátuma|  
|SubscriptionPropertyLabelStartedDate|A következőn:|  
|PageTitleRenameSubscription|Előfizetés átnevezése|  
|SubscriptionPropertyLabelName|Előfizetés neve|  
  
###  <a name="SubscriptionStrings"></a> SubscriptionStrings  
  
|Name (Név)|Text|  
|----------|----------|  
|SectionHeadingCloseAccount|Szeretné megszüntetni a fiók?|  
|PageTitleDeveloperProfile|Profil|  
|ButtonLabelHideKey|Elrejtése|  
|ButtonLabelRegenerateKey|Újragenerálás|  
|InformationMessageKeyWasRegenerated|Biztos, hogy szeretné-e újragenerálja a kulcsot?|  
|ButtonLabelShowKey|Megjelenítés|  
  
###  <a name="UpdateProfileStrings"></a> UpdateProfileStrings  
  
|Name (Név)|Text|  
|----------|----------|  
|ButtonLabelUpdateProfile|Profil frissítése|  
|PageTitleUpdateProfile|Fiókadatok frissítése|  
  
###  <a name="UserProfile"></a> Felhasználói profil  
  
|Name (Név)|Text|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|Fiókinformációk módosítása|  
|ButtonLabelChangePassword|Jelszó módosítása|  
|ButtonLabelCloseAccount|Fiók bezárása|  
|TextboxLabelEmail|E-mail|  
|TextboxLabelEmailFirstName|Utónév|  
|TextboxLabelEmailLastName|Vezetéknév|  
|TextboxLabelNotificationsSenderEmail|Az értesítések feladói e-mail|  
|TextboxLabelOrganizationName|Szervezet neve|  
|SubscriptionStateActive|Aktív|  
|SubscriptionStateCancelled|Megszakítva|  
|SubscriptionStateExpired|Elévült|  
|SubscriptionStateRejected|Elutasított|  
|SubscriptionStateRequested|A kért|  
|SubscriptionStateSuspended|Felfüggesztve|  
|DefaultSubscriptionNameTemplate|{0}  (alapértelmezett)|  
|SubscriptionNameTemplate|Fejlesztői hozzáférés #{0}|  
|TextboxLabelSubscriptionName|Előfizetés neve|  
|ValidationMessageSubscriptionNameRequired|Előfizetés neve nem lehet üres.|  
|ApiManagementUserLimitReached|Ez a szolgáltatás elérte az engedélyezett felhasználók maximális számát. Frissítsen egy magasabb díjszabási csomagot.|  
  
##  <a name="glyphs"></a> Erőforrások szimbóluma  
 Az API Management fejlesztői portál sablonjainak használhatja az ikonokra a [a rendszerindítási Glyphicons](https://getbootstrap.com/components/#glyphicons). Karaktertábla készlete magában foglalja a több mint 250 Karaktertábla betűtípus formátumban a [Glyphicon](https://glyphicons.com/) Halflings beállítása. A készletből egy szimbóluma használatához használja a következő szintaxist.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 Karaktertábla teljes listáját lásd: [a rendszerindítási Glyphicons](https://getbootstrap.com/components/#glyphicons).

## <a name="next-steps"></a>További lépések
Sablonok használatának ismertetését lásd: [testreszabása sablonok használatával, az API Management fejlesztői portálon](api-management-developer-portal-templates.md).
