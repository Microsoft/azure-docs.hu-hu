---
title: SPA-háttérrendszer biztosítása az Azure API Managementban Active Directory B2C
description: A OAuth 2,0-mel biztosíthatja az API-t az Azure Active Directory B2C, az Azure API Management és az Easy Auth használatával, hogy a JavaScript SPA-ból hívható a PKCE enabled SPA hitelesítési folyamat használatával.
services: api-management, azure-ad-b2c, app-service
documentationcenter: ''
author: WillEastbury
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2021
ms.author: wieastbu
ms.custom: fasttrack-new, fasttrack-update, devx-track-js
ms.openlocfilehash: baa6a0a6995e206924d14de25b98700e450f3a0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954921"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>Az OAuth 2,0, a Azure Active Directory B2C és az Azure API Management által védett SPA-háttérrendszer

Ez a forgatókönyv bemutatja, hogyan konfigurálhatja az Azure API Management-példányát az API-k elleni védelemhez.
A Azure AD B2C SPA (Auth Code + PKCE) flow-t használjuk a tokenek beszerzéséhez, a API Management mellett pedig az EasyAuth-t használó Azure Functions-háttér védelméhez.

## <a name="aims"></a>Célja

Látni fogjuk, hogyan használhatók a API Management egy egyszerűsített forgatókönyvben Azure Functions és Azure AD B2C. Létre fog hozni egy API-t meghívó JavaScript-(JS-) alkalmazást, amely a felhasználók Azure AD B2C használatával jelentkezik be. Ezt követően API Management érvényesítő-JWT, CORS és díjszabási korlátját fogja használni a fő házirend-funkciókkal a háttérrendszer API-ja elleni védelem érdekében.

A védelem részletesen a EasyAuth használatával érvényesíti a tokent a háttérbeli API-ban, és gondoskodjon arról, hogy az API Management az egyetlen olyan szolgáltatás, amely meghívja a Azure Functions háttérrendszer-kezelőt.

## <a name="what-will-you-learn"></a>Mit fog tanulni?

> [!div class="checklist"]
> * Egyoldalas alkalmazás és háttérbeli API beállítása Azure Active Directory B2C
> * Azure Functions háttérrendszer API létrehozása
> * Azure Functions API importálása az Azure-ba API Management
> * Az API biztonságossá tétele az Azure API Management
> * A Azure Active Directory B2C engedélyezési végpontok meghívása a Microsoft Identity platform kódtárai (MSAL.js) használatával
> * HTML/Vanilla JS egyoldalas alkalmazás tárolása és egy Azure Blob Storage-végpontról való kiszolgálás

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések végrehajtásához a következőket kell tennie:

* Egy Azure (StorageV2) általános célú v2 Storage-fiók a frontend JS egyoldalas alkalmazás üzemeltetéséhez.
* Egy Azure API Management-példány (bármely szint működni fog, beleértve a "fogyasztást" is, de a teljes forgatókönyvre vonatkozó bizonyos funkciók nem érhetők el ebben a rétegben (a díjszabási kulcs és a dedikált virtuális IP-cím), ezek a korlátozások a cikkben olvashatók.
* Egy üres Azure Function-alkalmazás (amely a V 3.1 .NET Core futtatókörnyezetet futtatja egy használati csomagon) a nevezett API üzemeltetéséhez
* Egy előfizetéshez csatolt Azure AD B2C bérlő.

Annak ellenére, hogy a gyakorlatban a termelési számítási feladatokban ugyanabban a régióban használja az erőforrásokat, ebben az útmutatóban az üzembe helyezés régiója nem fontos.

## <a name="overview"></a>Áttekintés

Íme egy illusztráció a használatban lévő összetevőkről és a folyamatok között a folyamat befejezése után.
![Használatban és folyamatban található összetevők](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "Használatban és folyamatban található összetevők")

Íme egy gyors áttekintés a következő lépésekről:

1. A Azure AD B2C hívó (frontend, API Management) és API-alkalmazások létrehozása hatókörökkel és API-hozzáférés biztosítása
1. A regisztrációs és bejelentkezési szabályzatok létrehozása, hogy a felhasználók bejelentkezhetnek Azure AD B2C 
1. API Management konfigurálása az új Azure AD B2C ügyfél-azonosítókkal és kulcsokkal a OAuth2 felhasználói hitelesítésének engedélyezéséhez a Fejlesztői konzolon
1. A Function API létrehozása
1. Konfigurálja a Function API-t úgy, hogy engedélyezze a EasyAuth az új Azure AD B2C ügyfél-AZONOSÍTÓval és kulccsal, és zárolja a APIM VIP-t
1. Az API-definíció létrehozása a API Managementban
1. Oauth2 beállítása a API Management API-konfigurációhoz
1. Állítsa be a **CORS** házirendet, és adja hozzá a **validate-JWT** szabályzatot a OAuth-jogkivonat ellenőrzéséhez minden bejövő kérelem esetében
1. A hívó alkalmazás felépítése az API használatára
1. A JS SPA-minta feltöltése
1. A minta JS-ügyfélalkalmazás konfigurálása az új Azure AD B2C ügyfél-azonosító és-kulcsok használatával 
1. Az ügyfélalkalmazás tesztelése

   > [!TIP]
   > Rengeteg információt és kulcsot fogunk rögzíteni, stb. ebben a dokumentumban ezt a dokumentumot érdemes lehet megnyitni, hogy egy szövegszerkesztőben a következő konfigurációs elemek ideiglenesen legyenek tárolva.  
   >
   > B2C HÁTTÉR-ÜGYFÉL AZONOSÍTÓJA:  
   > B2C-HÁTTÉRBELI ÜGYFÉL TITKOS KULCSA:  
   > B2C-HÁTTÉR API-HATÓKÖRÉNEK URI-JA:  
   > B2C FELÜLETI ÜGYFÉL AZONOSÍTÓJA:  
   > B2C FELHASZNÁLÓI FOLYAMAT VÉGPONTJÁNAK URI-JA:  
   > B2C JÓL ISMERT OPENID-VÉGPONT:   
   > B2C-szabályzat neve: Frontendapp_signupandsignin függvény URL-címe:  
   > APIM API-ALAP URL-CÍME: TÁROLÓ ELSŐDLEGES VÉGPONTJÁNAK URL-CÍME:  

## <a name="configure-the-backend-application"></a>A háttérbeli alkalmazás konfigurálása

Nyissa meg a Azure AD B2C panelt a portálon, és hajtsa végre a következő lépéseket.

1. Válassza az **alkalmazás-regisztrációk** lapot.
1. Kattintson az új regisztráció gombra.
1. Az átirányítási URI kiválasztása mezőben válassza a "web" lehetőséget.
1. Most állítsa be a megjelenítendő nevet, válassza ki a létrehozni kívánt szolgáltatáshoz tartozó egyedi és releváns elemet. Ebben a példában a "háttérbeli alkalmazás" nevet fogjuk használni.
1. Használjon helyőrzőket a válasz URL-címekhez, például a https://jwt.ms (z) "" (A Microsoft tulajdonában lévő jogkivonat-dekódolási hely), majd később frissíti ezeket az URL-címeket.
1. Győződjön meg arról, hogy a "fiókok bármely identitás-szolgáltatóban vagy szervezeti címtárban (a felhasználók hitelesítéséhez felhasználói folyamatokkal) lehetőséget választotta
1. Ehhez a mintához szüntesse meg a "rendszergazdai jóváhagyás megadása" mezőt, mivel még ma nem lesz szükség offline_access engedélyekre.
1. Kattintson a „Regisztrálás” parancsra.
1. Jegyezze fel a háttérbeli alkalmazás ügyfél-AZONOSÍTÓját későbbi használatra (az "alkalmazás (ügyfél) azonosítója" alatt látható).
1. Válassza ki a *tanúsítványok és titkok* lapot (a kezelés alatt), majd kattintson az "új ügyfél titka" elemre az Auth kulcs létrehozásához (fogadja el az alapértelmezett beállításokat, és kattintson a "Hozzáadás" gombra).
1. Ha a Hozzáadás gombra kattint, másolja a kulcsot (az "érték" alatt) valahol biztonságos értékre a későbbi, "háttérbeli ügyfél-titokként" való használatra. vegye figyelembe, hogy ez a párbeszédpanel az egyetlen lehetséges lehetőség a kulcs másolására.
1. Most válassza ki az *API közzététele* lapot (a kezelés alatt).
1. A rendszer felszólítja, hogy állítsa be a AppID URI-JÁT, majd válassza ki és jegyezze fel az alapértelmezett értéket.
1. Hozza létre és nevezze el a "Hello" hatókört a Function API számára, a "Hello" kifejezést használhatja az összes beírható beállításhoz, rögzítheti a teljes hatókör értékének URI-JÁT, majd kattintson a "hatókör hozzáadása" elemre.
1. Térjen vissza a Azure AD B2C panel gyökeréhez a portál bal felső részén található "Azure AD B2C" navigációs elem kiválasztásával.

   > [!NOTE]
   > Azure AD B2C hatókörök hatékony engedélyek az API-n belül, hogy más alkalmazások is igényelhetnek hozzáférést az API-hozzáférés panelről az alkalmazásokból, gyakorlatilag csak a nevezett API-hoz létrehozott alkalmazás-engedélyeket.

## <a name="configure-the-frontend-application"></a>A frontend alkalmazás konfigurálása

1. Válassza az **alkalmazás-regisztrációk** lapot.
1. Kattintson az új regisztráció gombra.
1. Az átirányítási URI kiválasztása mezőben válassza az "egyoldalas alkalmazás (SPA)" lehetőséget.
1. Most állítsa be a megjelenítendő nevet és a AppID URI-t, válassza ki a AAD B2C alkalmazás regisztrációját használó előtér-alkalmazáshoz tartozó egyedi és releváns elemet. Ebben a példában a "frontend Application" kifejezést használhatja
1. Az első alkalmazás regisztrálásának megfelelően hagyja meg a támogatott fióktípus beállítást az alapértelmezett értékre (a felhasználók hitelesítése felhasználói folyamatokkal)
1. Használjon helyőrzőket a válasz URL-címekhez, például a https://jwt.ms (z) "" (A Microsoft tulajdonában lévő jogkivonat-dekódolási hely), majd később frissíti ezeket az URL-címeket.
1. Hagyja bejelölve az engedélyezési rendszergazda jóváhagyása jelölőnégyzetet.
1. Kattintson a „Regisztrálás” parancsra.
1. Jegyezze fel a előtér-alkalmazás ügyfél-AZONOSÍTÓját későbbi használatra (az "alkalmazás (ügyfél) azonosítója" alatt látható).
1. Váltson az *API-engedélyek* lapra.
1. Hozzáférés biztosítása a háttérbeli alkalmazáshoz az "engedély hozzáadása", majd a "saját API-k" elemre kattintva válassza ki a "háttérbeli alkalmazás" lehetőséget, válassza az "engedélyek" lehetőséget, válassza ki az előző szakaszban létrehozott hatókört, és kattintson az "engedélyek hozzáadása" elemre.
1. Kattintson a "rendszergazdai jóváhagyás megadása a következőhöz: {bérlő}" elemre, majd kattintson az igen lehetőségre az előugró ablakban. Ez a felugró ablak beleegyezett a "előtér-alkalmazás" használatára a korábban létrehozott "háttérbeli alkalmazás" című "Hello" engedély használatához.
1. Az összes engedélynek most az Állapot oszlopban szereplő zöld pipaként kell megjelennie az alkalmazásban.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>"Regisztráció és bejelentkezés" felhasználói folyamat létrehozása

1. Térjen vissza a B2C panel gyökeréhez a Azure AD B2C morzsa kiválasztásával.
1. Váltson a "Felhasználókövetés" (a házirendek alatt) fülre.
1. Kattintson az "új felhasználói folyamat" elemre.
1. Válassza a "regisztráció és bejelentkezés" felhasználói folyamat típusát, és válassza az "ajánlott", majd a "létrehozás" lehetőséget.
1. Adja meg a szabályzat nevét, és jegyezze fel később. Ebben a példában a "Frontendapp_signupandsignin" kifejezést használhatja, hogy ez a "B2C_1_" előtaggal lesz ellátva, hogy "B2C_1_Frontendapp_signupandsignin"
1. Az "Identity Providers" és a "helyi fiókok" területen jelölje be az "e-mail-regisztráció" (vagy a "felhasználói azonosító regisztrálása" lehetőséget a B2C-bérlő konfigurációjától függően), majd kattintson az OK gombra. Ennek a konfigurációnak az az oka, hogy a helyi B2C-fiókokat regisztrálni fogjuk, nem pedig egy másik identitás-szolgáltatóra (például egy közösségi identitás-szolgáltatóra), hogy egy felhasználó meglévő közösségi adathordozó-fiókját használják.
1. Hagyja meg az MFA és a feltételes hozzáférési beállításokat az alapértelmezett értékeken.
1. Kattintson a "felhasználói attribútumok és jogcímek" lehetőségre, majd a "továbbiak..." elemre. Ezután válassza ki azokat a jogcím-beállításokat, amelyeket a felhasználók megadhatnak, és amelyeket visszaadottak a jogkivonatban. Győződjön meg arról, hogy a "megjelenítendő név" és az "e-mail-cím" mezőben a "megjelenítendő név" és az "e-mail-címek" értéket adja vissza (Ügyeljen arra, hogy az EmailAddress, a szám, és az e-mail-címek visszaadását kéri, és kattintson az OK gombra, majd kattintson a "létrehozás" gombra.
1. Kattintson a listában létrehozott felhasználói folyamatra, majd kattintson a "felhasználói folyamat futtatása" gombra.
1. Ezzel a művelettel megnyílik a felhasználói folyamat futtatása panel, válassza ki a előtér-alkalmazást, másolja a felhasználói folyamat végpontját, és mentse később.
1. Másolja és tárolja a hivatkozást a tetején, és rögzítse a "jól ismert OpenID konfigurációs végpontként" a későbbi használat érdekében.

   > [!NOTE]
   > A B2C-szabályzatok lehetővé teszik a Azure AD B2C bejelentkezési végpontok elérhetővé tételét, így különböző módon rögzíthetik a különböző adatösszetevőket és jelentkezhetnek be a felhasználókba.
   > 
   > Ebben az esetben konfiguráltunk egy regisztrációs vagy bejelentkezési folyamatot (szabályzatot). Ez egy jól ismert konfigurációs végpontot is feltett, amely mindkét esetben a létrehozott szabályzatot a "p =" lekérdezési karakterlánc paraméterrel együtt azonosította az URL-ben.  
   >
   > Ha ezt elvégezte, most már rendelkezik egy működőképes üzleti identitás platformmal, amely több alkalmazásba is aláírja a felhasználókat.

## <a name="build-the-function-api"></a>A Function API létrehozása

1. Váltson vissza a standard Azure AD-bérlőre a Azure Portalban, hogy ismét be lehessen állítani az előfizetésben lévő elemeket.
1. Lépjen a Azure Portal Function apps paneljére, nyissa meg az üres Function alkalmazást, majd kattintson a függvények elemre, majd a Hozzáadás gombra.
1. A megjelenő menüben válassza a "fejlesztés a portálon" lehetőséget a "sablon kiválasztása" területen, majd válassza a "HTTP-trigger" lehetőséget a sablon részletei alatt a "Hello" (engedélyezési szint "Function"), majd válassza a Hozzáadás lehetőséget.
1. Váltson a Code + test (kód + tesztelés) panelre, és másolja a vágólapra a kódot a megjelenő *meglévő kód fölé* .
1. Kattintson a Mentés gombra.

   ```csharp
   
   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   
   public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
   {
      log.LogInformation("C# HTTP trigger function processed a request.");
      
      return (ActionResult)new OkObjectResult($"Hello World, time and date are {DateTime.Now.ToString()}");
   }
   
   ```

   > [!TIP]
   > Az imént beillesztett c# parancsfájl-függvény kódja egyszerűen naplóz egy sort a függvények naplóiba, és a "Hello World" szöveget adja vissza néhány dinamikus adattal (a dátummal és idővel).

1. Válassza az "integráció" lehetőséget a bal oldali panelen, majd kattintson a http (REQ) hivatkozásra az "trigger" mezőben.
1. A "kiválasztott HTTP-metódusok" legördülő menüből törölje a HTTP POST metódust, hagyja bejelölve a csak a kiválasztott lehetőséget, majd kattintson a Mentés gombra.
1. Váltson vissza a Code + test lapra, kattintson a "függvény URL-címének beolvasása" elemre, majd másolja ki a megjelenő URL-címet, és mentse később.

   > [!NOTE]
   > Az imént létrehozott kötések egyszerűen csak azt mondják, hogy a függvények válaszolnak a névtelen http GET kérelmekre az imént másolt URL-címre ( `https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey` ). Most már van egy skálázható, kiszolgáló nélküli HTTPS API-je, amely képes egy nagyon egyszerű hasznos adat visszaküldésére.
   >
   > Most tesztelheti ezt az API-t egy webböngészőből az imént másolt és mentett URL-cím használatával. Eltávolíthatja a lekérdezési karakterlánc paramétereit is: "? Code = secretkey" az URL-címet, majd tesztelje újra, hogy a Azure Functions 401 hibát ad vissza.

## <a name="configure-and-secure-the-function-api"></a>A Function API konfigurálása és biztonságossá tétele

1. A Function app két további területét kell konfigurálni (engedélyezési és hálózati korlátozásokkal).
1. Először konfigurálja a hitelesítést/engedélyezést, ezért a navigációs felületen keresztül térjen vissza a Function alkalmazás gyökeréhez.
1. Ezután válassza a "hitelesítés/engedélyezés" lehetőséget (a "beállítások" alatt).
1. Kapcsolja be a App Service hitelesítési funkciót.
1. Állítsa be az elvégzendő műveletet, ha a kérés nincs hitelesítve a legördülő menüben a "Bejelentkezés a Azure Active Directory" művelettel.
1. A "hitelesítő szolgáltatók" területen válassza a "Azure Active Directory" lehetőséget.
1. Válassza a "speciális" lehetőséget a felügyeleti mód kapcsolóján.
1. Illessze be a háttérbeli alkalmazás [alkalmazás] ügyfél-AZONOSÍTÓját (Azure AD B2C) az "ügyfél-azonosító" mezőbe.
1. Illessze be a jól ismert Open-ID konfigurációs végpontot a regisztrációs és bejelentkezési szabályzatba a kiállító URL-címe mezőbe (ezt a konfigurációt korábban rögzítették).
1. Kattintson a "titok megjelenítése" gombra, és illessze be a háttérbeli alkalmazás ügyfelének titkos kódját a megfelelő mezőbe.
1. Válassza az OK gombot, amely visszaadja az identitás-szolgáltató kijelölési paneljét vagy képernyőjét.
1. Hagyja engedélyezve a [jogkivonat-tárolót](../app-service/overview-authentication-authorization.md#token-store) a speciális beállítások területen (alapértelmezett).
1. Kattintson a Save (Mentés) gombra a panel bal felső részén.

   > [!IMPORTANT]
   > Most már telepítette a Function API-t, és 401-válaszokat kell dobnia, ha a megfelelő JWT nincs megadva engedélyként: tulajdonosi fejléc, és az érvényes kérelem beérkezése esetén vissza kell térnie az adatokra.  
   > A nem hitelesített kérelmek kezeléséhez a "Bejelentkezés az Azure AD szolgáltatással" beállítással további védelmi részletes biztonsági EasyAuth bővült. Vegye figyelembe, hogy ez megváltoztatja a háttérbeli függvényalkalmazás és a frontend SPA közötti jogosulatlan kérelmek viselkedését, mivel a EasyAuth a 401-re nem jogosult válasz helyett 302-es átirányítást ad ki, ezért API Management később kell kijavítani.  
   >
   > Még mindig nem alkalmazunk IP-biztonságot, ha rendelkezik érvényes kulcs-és OAuth2 jogkivonattal, bárki meghívhatja ezt bárhonnan – ideális esetben az API Management-on keresztüli összes kérést kényszeríteni szeretné.  
   > 
   > Ha APIM-használati szintet használ, akkor [nincs dedikált Azure API Management virtuális IP-cím](./api-management-howto-ip-addresses.md#ip-addresses-of-consumption-tier-api-management-service) , amely lehetővé teszi a-listához a függvények hozzáférés-korlátozásait. Az Azure API Management standard SKU-ban és [a VIP-nél egyetlen bérlő, az erőforrás élettartama pedig](./api-management-howto-ip-addresses.md#changes-to-the-ip-addresses). Az Azure API Management-használati szinten az API-hívásokat a fent másolt URI-azonosító részét képező közös titkos kulcs használatával zárolhatja. Továbbá a felhasználási réteghez – az alábbi 12-17-es lépések nem érvényesek.

1. A "hitelesítés/engedélyezés" panel lezárása 
1. Nyissa meg a *portál API Management* paneljét, majd nyissa meg a *példányt*.
1. Rögzítse az Áttekintés lapon látható privát VIP-címet.
1. Térjen vissza a *portál Azure functions* paneljére, majd nyissa meg újra *a példányt* .
1. Válassza a hálózatkezelés lehetőséget, majd válassza a "hozzáférési korlátozások konfigurálása" lehetőséget.
1. Kattintson a szabály hozzáadása elemre, és adja meg a fenti 3. lépésben átmásolt virtuális IP-címet a következő formátumban: xx. xx. xx. xx/32.
1. Ha továbbra is használni szeretné a functions-portált, és az alábbi opcionális lépéseket is el kívánja végezni, adja hozzá a saját nyilvános IP-címét vagy CIDR-tartományát.
1. Ha egy engedélyezési bejegyzés szerepel a listán, az Azure egy implicit megtagadási szabályt ad hozzá az összes többi cím blokkolásához.

A CIDR formázott blokkokat kell hozzáadnia az IP-korlátozások panelhez. Ha egy címet (például a API Management VIP-t) kell hozzáadnia, hozzá kell adnia azt a következő formátumban: xx. xx. xx. xx/32.

   > [!NOTE]
   > A Function API mostantól nem hívható meg, mint az API Management vagy a címe.

1. Nyissa meg a *API Management* panelt, majd nyissa meg a *példányt*.
1. Válassza az API-k panelt.
1. Az "új API hozzáadása" panelen válassza a "függvényalkalmazás" lehetőséget, majd az előugró ablak tetején válassza a "teljes" lehetőséget.
1. Kattintson a Tallózás gombra, válassza ki az API-t üzemeltető Function alkalmazást, és kattintson a Kiválasztás gombra. Ezután kattintson ismét a kijelölés gombra.
1. Adja meg az API számára a API Management belső használatra vonatkozó nevét és leírását, és adja hozzá a "korlátlan" termékhez.
1. Másolja és jegyezze fel az API alapszintű URL-címét, majd kattintson a Létrehozás gombra.
1. Kattintson a beállítások fülre, majd az előfizetés elemre, kapcsolja ki az "előfizetés szükséges" jelölőnégyzetet, mert ebben az esetben a OAuth JWT tokent fogjuk használni. Vegye figyelembe, hogy ha a felhasználási szintet használja, akkor az éles környezetben továbbra is szükséges lenne. 

   > [!TIP]
   > Ha a APIM felhasználási szintjét használja, a korlátlan termék nem lesz elérhető a dobozból. Ehelyett navigáljon a "termékek" elemre az "API-k" alatt, és nyomja meg a "Hozzáadás" lehetőséget.  
   > Írja be a "korlátlan" kifejezést a terméknév és a Leírás mezőbe, és válassza ki az imént hozzáadott API-t a képernyő bal alsó sarkában található "+" API-ábrafeliratból. Jelölje be a "közzétett" jelölőnégyzetet. Hagyja meg a többi értéket alapértelmezettként. Végül nyomja meg a "létrehozás" gombot. Ezzel létrehozta a "korlátlan" terméket, és hozzárendelte az API-hoz. Az új terméket később is testreszabhatja.

## <a name="configure-and-capture-the-correct-storage-endpoint-settings"></a>A megfelelő tárolási végpont beállításainak konfigurálása és rögzítése

1. Nyissa meg a Storage-fiókok panelt a Azure Portal 
1. Válassza ki a létrehozott fiókot, és válassza a "statikus webhely" panelt a beállítások szakaszban (ha nem jelenik meg a "statikus webhely" beállítás, ellenőrizze, hogy létrehozott-e v2-fiókot).
1. Állítsa a statikus web hosting szolgáltatást "engedélyezve" értékre, és állítsa az index dokumentum nevét "index.html" értékre, majd kattintson a Save (Mentés) gombra.
1. Jegyezze fel később az "elsődleges végpont" tartalmát, mivel ez a hely, ahol a előtér-helyet üzemeltetni fogja.

   > [!TIP]
   > Használhatja az Azure Blob Storage + CDN újraírását, vagy Azure App Service a SPA üzemeltetéséhez, de a Blob Storage statikus webhely-üzemeltetési funkciója egy alapértelmezett tárolót biztosít számunkra, amely az Azure Storage-ban statikus webes tartalmakat, illetve HTML/js-t/CSS-t szolgál ki, és egy alapértelmezett oldalt fog kikövetkeztetni a nulla működés érdekében.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>A **CORS** és a **validate-JWT** szabályzatok beállítása

> A következő részeket kell követni, függetlenül attól, hogy milyen APIM-réteg van használatban. A Storage-fiók URL-címe abból a Storage-fiókból származik, amelyet a cikk elején lévő előfeltételekben elérhetővé tesz.
1. Váltson a portál API Management paneljére, és nyissa meg a példányát.
1. Válassza az API-k lehetőséget, majd válassza az "összes API" lehetőséget.
1. A "bejövő feldolgozás" alatt kattintson a "</>" kód nézetre a szabályzat-szerkesztő megjelenítéséhez.
1. Szerkessze a bejövő szakaszt, és illessze be az alábbi XML-fájlt, hogy az a következőképpen hangzik.
1. A szabályzat következő paramétereinek cseréje
1. {PrimaryStorageEndpoint} (Az előző szakaszban másolt elsődleges tárolási végpontot), a {Szabályzathoz-Well-known-OpenID} (a korábban másolt "jól ismert" OpenID konfigurációs végpontot ") és a {backend-API-Application-Client-ID} (B2C-alkalmazás/ügyfél-azonosító a **háttérrendszer API**-hoz) a korábban mentett megfelelő értékekkel.
1. Ha a API Management fogyasztási szintjét használja, akkor mindkét díjszabási korlátot el kell távolítania, mivel ez a házirend nem érhető el az Azure-API Management energiafogyasztási szintje használata esetén.

   ```xml
   <inbound>
      <cors allow-credentials="true">
            <allowed-origins>
                <origin>{PrimaryStorageEndpoint}</origin>
            </allowed-origins>
            <allowed-methods preflight-result-max-age="120">
                <method>GET</method>
            </allowed-methods>
            <allowed-headers>
                <header>*</header>
            </allowed-headers>
            <expose-headers>
                <header>*</header>
            </expose-headers>
        </cors>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid." require-expiration-time="true" require-signed-tokens="true" clock-skew="300">
         <openid-config url="{b2cpolicy-well-known-openid}" />
         <required-claims>
            <claim name="aud">
               <value>{backend-api-application-client-id}</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <rate-limit-by-key calls="300" renewal-period="120" counter-key="@(context.Request.IpAddress)" />
      <rate-limit-by-key calls="15" renewal-period="60" counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
   </inbound>
   ```

   > [!NOTE]
   > Az Azure API Management mostantól képes reagálni a JavaScript SPA-alkalmazásokból származó kereszthivatkozásokra, és a kérésnek a Function API-ra való továbbítása előtt a JWT hitelesítési token korlátozását és előzetes érvényesítését fogja végezni.
   > 
   > Gratulálunk, mostantól Azure AD B2C, API Management és Azure Functions együtt dolgozhat az API-k közzétételével, biztonságossá tételével és felhasználásával.

   > [!TIP]
   > Ha a API Management használati szintet használja, és a JWT tulajdonos vagy a bejövő IP-cím alapján korlátozza a korlátozást (a hívások korlátozása a kulcsfontosságú házirend szerint nem támogatott a "fogyasztás" szinten), akkor a hívási arány kvótája [itt](./api-management-access-restriction-policies.md#LimitCallRate)látható.  
   > Mivel ez a példa egy JavaScript-alapú egyoldalas alkalmazás, a API Management kulcsot csak a ráta-korlátozási és számlázási hívások esetében használjuk. A tényleges engedélyezést és hitelesítést Azure AD B2C kezeli, és a rendszer a JWT ágyazza be, amely a API Management, majd a háttérbeli Azure-függvény által kétszer érvényesítve lesz.

## <a name="upload-the-javascript-spa-sample-to-static-storage"></a>A JavaScript SPA-minta feltöltése statikus tárolóba

1. Továbbra is a Storage-fiók panelen válassza a "tárolók" panelt a blob szolgáltatás szakaszban, majd kattintson a jobb oldali ablaktáblán megjelenő $web tárolóra.
1. Mentse az alábbi kódot egy, a gépen helyileg található fájlba index.html néven, majd töltse fel a index.html fájlt a $web tárolóba.

   ```html
    <!doctype html>
    <html lang="en">
    <head>
         <meta charset="utf-8">
         <meta http-equiv="X-UA-Compatible" content="IE=edge">
         <meta name="viewport" content="width=device-width, initial-scale=1">
         <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-BmbxuPwQa2lc/FVzBcNJ7UAyJxM6wuqIj61tLrc4wSX0szH/Ev+nYRRuWlolflfl" crossorigin="anonymous">
         <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.11.1/js/msal-browser.min.js"></script>
    </head>
    <body>
         <div class="container-fluid">
             <div class="row">
                 <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
                        <div class="container-fluid">
                            <a class="navbar-brand" href="#">Azure Active Directory B2C with Azure API Management</a>
                            <div class="navbar-nav">
                                <button class="btn btn-success" id="signinbtn"  onClick="login()">Sign In</a>
                            </div>
                        </div>
                    </nav>
                 </div>
             </div>
             <div class="row">
                 <div class="col-md-12">
                     <div class="card" >
                        <div id="cardheader" class="card-header">
                            <div class="card-text"id="message">Please sign in to continue</div>
                        </div>
                        <div class="card-body">
                            <button class="btn btn-warning" id="callapibtn" onClick="getAPIData()">Call API</a>
                            <div id="progress" class="spinner-border" role="status">
                                <span class="visually-hidden">Loading...</span>
                            </div>
                        </div>
                     </div>
                 </div>
             </div>
         </div>
         <script lang="javascript">
                // Just change the values in this config object ONLY.
                var config = {
                    msal: {
                        auth: {
                            clientId: "{CLIENTID}", // This is the client ID of your FRONTEND application that you registered with the SPA type in AAD B2C
                            authority:  "{YOURAUTHORITYB2C}", // Formatted as https://{b2ctenantname}.b2clogin.com/tfp/{b2ctenantguid or full tenant name including onmicrosoft.com}/{signuporinpolicyname}
                            redirectUri: "{StoragePrimaryEndpoint}", // The storage hosting address of the SPA, a web-enabled v2 storage account - recorded earlier as the Primary Endpoint.
                            knownAuthorities: ["{B2CTENANTDOMAIN}"] // {b2ctenantname}.b2clogin.com
                        },
                        cache: {
                            cacheLocation: "sessionStorage",
                            storeAuthStateInCookie: false 
                        }
                    },
                    api: {
                        scopes: ["{BACKENDAPISCOPE}"], // The scope that we request for the API from B2C, this should be the backend API scope, with the full URI.
                        backend: "{APIBASEURL}/hello" // The location that we will call for the backend api, this should be hosted in API Management, suffixed with the name of the API operation (in the sample this is '/hello').
                    }
                }
                document.getElementById("callapibtn").hidden = true;
                document.getElementById("progress").hidden = true;
                const myMSALObj = new msal.PublicClientApplication(config.msal);
                myMSALObj.handleRedirectPromise().then((tokenResponse) => {
                    if(tokenResponse !== null){
                        console.log(tokenResponse.account);
                        document.getElementById("message").innerHTML = "Welcome, " + tokenResponse.account.name;
                        document.getElementById("signinbtn").hidden = true;
                        document.getElementById("callapibtn").hidden = false;
                    }}).catch((error) => {console.log("Error Signing in:" + error);
                });
                function login() {
                    try {
                        myMSALObj.loginRedirect({scopes: config.api.scopes});
                    } catch (err) {console.log(err);}
                }
                function getAPIData() {
                    document.getElementById("progress").hidden = false; 
                    document.getElementById("message").innerHTML = "Calling backend ... "
                    document.getElementById("cardheader").classList.remove('bg-success','bg-warning','bg-danger');
                    myMSALObj.acquireTokenSilent({scopes: config.api.scopes, account: getAccount()}).then(tokenResponse => {
                        const headers = new Headers();
                        headers.append("Authorization", `Bearer ${tokenResponse.accessToken}`);
                        fetch(config.api.backend, {method: "GET", headers: headers})
                            .then(async (response)  => {
                                if (!response.ok)
                                {
                                    document.getElementById("message").innerHTML = "Error: " + response.status + " " + JSON.parse(await response.text()).message;
                                    document.getElementById("cardheader").classList.add('bg-warning');
                                }
                                else
                                {
                                    document.getElementById("cardheader").classList.add('bg-success');
                                    document.getElementById("message").innerHTML = await response.text();
                                }
                                }).catch(async (error) => {
                                    document.getElementById("cardheader").classList.add('bg-danger');
                                    document.getElementById("message").innerHTML = "Error: " + error;
                                });
                    }).catch(error => {console.log("Error Acquiring Token Silently: " + error);
                        return myMSALObj.acquireTokenRedirect({scopes: config.api.scopes, forceRefresh: false})
                    });
                    document.getElementById("progress").hidden = true;
             }
            function getAccount() {
                var accounts = myMSALObj.getAllAccounts();
                if (!accounts || accounts.length === 0) {
                    return null;
                } else {
                    return accounts[0];
                }
            }
        </script>
     </body>
    </html>
   ```

1. Keresse meg az előző szakaszban korábban tárolt statikus webhely elsődleges végpontját.

   > [!NOTE]
   > Gratulálunk, csak egy JavaScript egyoldalas alkalmazást helyezett üzembe az Azure Storage statikus tartalmak üzemeltetéséhez.  
   > Mivel még nem konfigurálta a JS alkalmazást a Azure AD B2C részleteivel – a lap még nem működik, ha megnyitja.

## <a name="configure-the-javascript-spa-for-azure-ad-b2c"></a>A JavaScript SPA Azure AD B2C konfigurálása

1. Most már tudjuk, hol vannak: az SPA-t konfigurálhatja a megfelelő API Management API-címnek, valamint a megfelelő Azure AD B2C alkalmazás-/ügyfél-azonosítók használatával.
1. Visszatérés a Azure Portal Storage panelre 
1. Válassza a tárolók lehetőséget (a beállítások alatt) 
1. Válassza ki a "$web" tárolót a listából.
1. index.html blob kiválasztása a listából 
1. Kattintson a "szerkesztés" gombra. 
1. Frissítse a msal konfigurációs szakaszban található hitelesítési értékeket a B2C-ben korábban regisztrált *előtér-* alkalmazásnak megfelelően. A kód megjegyzéseit a konfigurációs értékek kikeresésének módjához használhatja.
A *hitelesítésszolgáltató* értékének a következő formátumúnak kell lennie:-https://{b2ctenantname}. b2clogin. com/TFP/{b2ctenantname}. onmicrosoft. com}/{signupandsigninpolicyname}, ha használta a minta neveit, és a B2C-bérlő neve "contoso", akkor a szolgáltatónak "" értékűnek kell lennie https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com}/Frontendapp_signupandsignin .
1. Állítsa be az API-értékeket úgy, hogy megfeleljenek a háttérbeli címnek (a korábban feljegyzett API-alap URL-címet, az "b2cScopes" értékeket pedig korábban rögzítették a *háttérbeli alkalmazásnál*).
1. Kattintson a Save (Mentés) gombra.

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Az átirányítási URI-k beállítása a Azure AD B2C előtér-alkalmazáshoz

1. Nyissa meg a Azure AD B2C panelt, és navigáljon az alkalmazás regisztrálásához a JavaScript frontend alkalmazáshoz.
1. Kattintson az "URI-k átirányítása" elemre, és törölje a https://jwt.ms korábban megadott helyőrzőt.
1. Adjon hozzá egy új URI-t az elsődleges (tárolási) végponthoz (mínusz a záró perjel).

   > [!NOTE]
   > Ez a konfiguráció azt eredményezi, hogy az előtér-alkalmazás egy olyan hozzáférési jogkivonatot kap, amely a Azure AD B2Ctól megfelelő jogcímeket fogad.  
   > A SPA a háttér-API hívásakor a https-fejlécben szereplő tulajdonosi jogkivonatként fogja tudni felvenni ezt a tulajdonságot.  
   > 
   > API Management a tokent előzetesen érvényesíti, és az Azure ID (a felhasználó) által kiállított JWT, valamint a hívó IP-címe (az API Management szolgáltatási szintjétől függően a fenti megjegyzést tekinti meg), mielőtt átadná a kérést az Azure Function API-nak, adja hozzá a functions biztonsági kulcsot.  
   > A SPA a választ a böngészőben fogja megjeleníteni.
   >
   > *Gratulálunk, Ön konfigurálta Azure AD B2C, Azure API Management, Azure Functions Azure App Service engedélyezését, hogy tökéletes harmóniában működjön!*

Most már van egy egyszerű alkalmazásunk egy egyszerű, biztonságos API-val, teszteljük.

## <a name="test-the-client-application"></a>Az ügyfélalkalmazás tesztelése

1. Nyissa meg a korábban létrehozott Storage-fiókban megjegyzett minta alkalmazás URL-címét.
1. Kattintson a "Bejelentkezés" gombra a jobb felső sarokban, ez a gombra kattintva megnyithatja a Azure AD B2C regisztrációs vagy bejelentkezési profilját.
1. Az alkalmazásnak a B2C-profil neve alapján kell megjelennie.
1. Most kattintson a "hívás API" elemre, és a lapon frissítse a biztonságos API-ból visszaadott értékeket.
1. Ha *többször* is rákattint a hívási API gombra, és a fejlesztői szinten vagy a API Managementon fut, vegye figyelembe, hogy a megoldás megkezdi az API korlátozását, és ezt a funkciót a megfelelő üzenettel kell jelenteni az alkalmazásban.

## <a name="and-were-done"></a>És készen vagyunk

A fenti lépések módosíthatók és szerkeszthetők úgy, hogy a Azure AD B2C számos különböző felhasználási igényét lehetővé tegyék a API Management.

## <a name="next-steps"></a>Következő lépések

* További információ a [Azure Active Directory és a OAuth 2.0-s](../active-directory/develop/authentication-vs-authorization.md)verzióról.
* További [videók](https://azure.microsoft.com/documentation/videos/index/?services=api-management) a API Managementról.
* A háttér-szolgáltatás biztonságossá tételének egyéb módjaival kapcsolatban lásd: [kölcsönös Tanúsítványos hitelesítés](api-management-howto-mutual-certificates.md).
* [Hozzon létre egy API Management Service-példányt](get-started-create-service-instance.md).
* [Az első API kezelése](import-and-publish.md).