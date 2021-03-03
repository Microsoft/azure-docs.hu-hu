---
title: A Google hozzáadása identitás-szolgáltatóként a B2B-Azure AD-ben
description: A Google összevonása lehetővé teszi a vendég felhasználók számára, hogy saját Gmail-fiókjával jelentkezzenek be az Azure AD-alkalmazásokba.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9e7ec5569dd0de3b0535c3b0e3b3304848a5207
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653319"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Google hozzáadása identitás-szolgáltatóként a B2B vendég felhasználói számára

A Google-nal való összevonás beállításával engedélyezheti a meghívott felhasználóknak, hogy a saját Gmail-fiókjával jelentkezzenek be a megosztott alkalmazásaiba és erőforrásaiba anélkül, hogy Microsoft-fiókokat kellene létrehozniuk.

Miután hozzáadta a Google-t az alkalmazás bejelentkezési lehetőségeinek egyikéhez **, a felhasználó** egyszerűen megadhatja a Google-ba való bejelentkezéshez használt e-mail-címet, vagy kiválaszthatja a bejelentkezés **lehetőséget** , és kiválaszthatja a **Bejelentkezés a Google-** ba lehetőséget. Mindkét esetben a rendszer átirányítja a Google bejelentkezési oldalára a hitelesítéshez.

![Bejelentkezési beállítások a Google-felhasználók számára](media/google-federation/sign-in-with-google-overview.png)

> [!NOTE]
> A Google Federation kifejezetten a Gmail-felhasználók számára készült. A G Suite-tartományokkal való összevonása használja a [közvetlen összevonás](direct-federation.md)lehetőséget.

> [!IMPORTANT]
> **2021. január 4-én kezdődően** a Google [elavult webnézet-bejelentkezési támogatást jelenít meg](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Ha Google-összevonást vagy önkiszolgáló regisztrációt használ a Gmail szolgáltatással, az üzletági [natív alkalmazásokat tesztelje a kompatibilitás](google-federation.md#deprecation-of-webview-sign-in-support)érdekében.

## <a name="what-is-the-experience-for-the-google-user"></a>Mi a Google-felhasználó tapasztalata?

Ha egy Google-felhasználó beváltja a meghívót, a felhasználói élmény attól függően változhat, hogy már bejelentkezett-e a Google-ba:

- A rendszer a Google-ba nem bejelentkezett vendégeket fogja kérni.
- A Google-ba már bejelentkezett vendég felhasználók a használni kívánt fiók kiválasztását kérik. Ki kell választania azt a fiókot, amellyel meghívja őket.

A "fejléc túl hosszú" hibát mutató vendég felhasználók törölhetik a cookie-kat, vagy megnyithatnak egy privát vagy inkognitóban-ablakot, és megpróbálnak újra bejelentkezni.

![Képernyőkép, amely a Google bejelentkezési oldalát jeleníti meg.](media/google-federation/google-sign-in.png)

## <a name="sign-in-endpoints"></a>Bejelentkezési végpontok

A Google vendég felhasználók mostantól bejelentkezhetnek a több-bérlős vagy a Microsoft-alkalmazásokba egy [közös végpont](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint) használatával (azaz egy általános alkalmazás URL-címe, amely nem tartalmazza a bérlői környezetet). A következő példák gyakori végpontokra mutatnak:

- `https://teams.microsoft.com`
- `https://myapps.microsoft.com`
- `https://portal.azure.com`

A bejelentkezési folyamat során a vendég felhasználó kiválasztja a **bejelentkezési beállításokat**, majd kiválasztja a **Bejelentkezés egy szervezetbe** lehetőséget. A felhasználó ezután beírja a szervezet nevét, és folytatja a bejelentkezést a Google hitelesítő adataival.

A Google vendég felhasználói használhatják a bérlői adatokat tartalmazó alkalmazás-végpontokat is, például:

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

Azt is megteheti, hogy a Google vendég felhasználóinak közvetlen hivatkozása van egy alkalmazásra vagy erőforrásra, például a bérlői információkkal együtt `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>` .

## <a name="deprecation-of-webview-sign-in-support"></a>A Webview bejelentkezési támogatásának elavult változata

2021. január 4-én kezdődően a Google [elavult beágyazott webnézet-bejelentkezési támogatást](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Ha Google-összevonást vagy [önkiszolgáló regisztrációt használ a Gmail](identity-providers.md)szolgáltatással, az üzletági natív alkalmazásokat tesztelje a kompatibilitás érdekében. Ha az alkalmazásai olyan Webnézeti tartalmat tartalmaznak, amely hitelesítést igényel, a Google Gmail-felhasználók nem fognak tudni hitelesítést végezni. A következő ismert forgatókönyvek hatással lesznek a Gmail-felhasználókra:

- Beágyazott webnézetet vagy WebAccountManager (WAM) használó Windows-alkalmazások a Windows régebbi verzióiban.
- Egyéb natív alkalmazások, amelyek beágyazott böngésző-keretrendszert használnak a hitelesítéshez.

Ez a változás nincs hatással a következőre:

- Beágyazott webnézetet vagy WebAccountManager (WAM) használó Windows-alkalmazások a Windows legújabb verzióiban
- Microsoft iOS-alkalmazások
- G Suite-identitások, például ha SAML-alapú [közvetlen összevonást](direct-federation.md) használ a g Suite használatával

Továbbra is teszteljük a különböző platformokat és forgatókönyveket, és ennek megfelelően frissíti a cikket.
### <a name="to-test-your-apps-for-compatibility"></a>Az alkalmazások kompatibilitásának tesztelése

1. A [Google útmutatását](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html) követve megállapíthatja, hogy az alkalmazások érintettek-e.
2. A Hegedűs vagy más tesztelési eszköz használatával befecskendezheti a fejlécet a bejelentkezéshez, és egy Google külső identitást használhat a bejelentkezés teszteléséhez:

   1. Adja hozzá a Google-accounts-Request-OAuth-login: True értéket a HTTP-kérések fejlécéhez, ha a rendszer elküldi a kérelmeket a accounts.google.com.
   1. Próbáljon meg bejelentkezni az alkalmazásba úgy, hogy beírja a Gmail-címeket a accounts.google.com bejelentkezési oldalára.
   1. Ha a bejelentkezés sikertelen, és egy hibaüzenet jelenik meg, például "Ez a böngésző vagy alkalmazás nem biztonságos," a Google külső identitása le lesz tiltva a bejelentkezéskor.

3. A probléma megoldásához tegye a következők egyikét:

   - Ha a Windows-alkalmazás a Windows egy régebbi verziójában beágyazott webnézetet vagy WebAccountManager (WAM) használ, frissítsen a Windows legújabb verziójára.
   - Az alkalmazások módosításával a rendszerböngészőt használhatja a bejelentkezéshez. Részletekért lásd: a beágyazott és a [rendszer webes felhasználói felülete](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui) a MSAL.net dokumentációjában.  


## <a name="step-1-configure-a-google-developer-project"></a>1. lépés: a Google fejlesztői projekt konfigurálása
Először hozzon létre egy új projektet a Google fejlesztői konzolon egy ügyfél-azonosító és egy ügyfél-titok beszerzéséhez, amelyet később hozzáadhat a Azure Active Directory (Azure AD) szolgáltatáshoz. 
1. Nyissa meg a Google API-kat a címen https://console.developers.google.com , és jelentkezzen be Google-Fiókjával. Javasoljuk, hogy a megosztott csapat Google-fiókját használja.
2. Ha a rendszer kéri, fogadja el a szolgáltatási feltételeket.
3. Hozzon létre egy új projektet: a lap bal felső sarkában válassza ki a projekt listáját, majd a **projekt kiválasztása** lapon válassza az **új projekt** lehetőséget.
4. Az **új projekt** lapon adja meg a projekt nevét (például **Azure ad B2B**), majd válassza a **Létrehozás** lehetőséget: 
   
   ![Képernyőkép, amely egy új projekt oldalt jelenít meg.](media/google-federation/google-new-project.png)

4. Az API-k **& szolgáltatások** lapon válassza a **nézet** lehetőséget az új projekt alatt.

5. Válassza az **Ugrás az API-khoz – áttekintés** az API-k kártyán. Válassza a **OAuth-beleegyezési képernyő** lehetőséget.

6. Válassza a **külső** lehetőséget, majd válassza a **Létrehozás** lehetőséget. 

7. Az **OAuth-beleegyezés képernyőn** adja meg az **alkalmazás nevét**:

   ![Képernyőkép, amely a Google OAuth-beleegyezési képernyőjét jeleníti meg.](media/google-federation/google-oauth-consent-screen.png)

8. Görgessen a **jóváhagyott tartományok** szakaszhoz, és adja meg a **microsoftonline.com**:

   ![A jogosultsággal rendelkező tartományok szakaszt bemutató képernyőkép.](media/google-federation/google-oauth-authorized-domains.PNG)

9. Kattintson a **Mentés** gombra.

10. Válassza a **Hitelesítő adatok** lehetőséget. A **hitelesítő adatok létrehozása** menüben válassza a **OAUTH ügyfél-azonosító**:

    ![A Google API-k hitelesítő adatok létrehozása menüt bemutató képernyőkép.](media/google-federation/google-api-credentials.png)

11. Az **alkalmazás típusa** területen válassza a **webalkalmazás** lehetőséget. Adjon megfelelő nevet az alkalmazásnak, például az **Azure ad B2B**-nek. A **hitelesítő átirányítási URI**-k területen adja meg a következő URI-ket:
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br>(hol `<tenant ID>` van a bérlő azonosítója)
   
    > [!NOTE]
    > A bérlő AZONOSÍTÓjának megkereséséhez nyissa meg a [Azure Portal](https://portal.azure.com). A **Azure Active Directory** alatt válassza a **Tulajdonságok** lehetőséget, és másolja a **bérlői azonosítót**.

    ![Az átirányítási URI-k engedélyezését bemutató képernyőkép.](media/google-federation/google-create-oauth-client-id.png)

12. Válassza a **Létrehozás** lehetőséget. Másolja az ügyfél-azonosítót és az ügyfél titkos kulcsát. Ezeket akkor fogja használni, amikor hozzáadja az identitás-szolgáltatót a Azure Portal.

    ![Képernyőkép, amely a OAuth ügyfél-azonosítót és az ügyfél titkos kulcsát mutatja.](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>2. lépés: a Google-összevonás konfigurálása az Azure AD-ben 
Ekkor megadhatja a Google ügyfél-azonosítót és az ügyfél titkos kulcsát. Ezt a Azure Portal vagy a PowerShell használatával teheti meg. Győződjön meg arról, hogy saját maga hívja meg a Google összevonási konfigurációját. Használjon Gmail-címeket, és próbálja meg beváltani a meghívót a meghívott Google-fiókkal. 

**A Google Federation konfigurálása a Azure Portalban** 
1. Nyissa meg az [Azure Portal](https://portal.azure.com). A bal oldali ablaktáblán válassza a **Azure Active Directory** lehetőséget. 
2. Válassza a **külső identitások** lehetőséget.
3. Válassza a **minden identitás szolgáltató** lehetőséget, majd kattintson a **Google** gombra.
4. Adja meg a korábban beszerzett ügyfél-azonosítót és az ügyfél-titkot. Válassza a **Mentés** lehetőséget: 

   ![A Google Identity Provider hozzáadása oldalt megjelenítő képernyőkép.](media/google-federation/google-identity-provider.png)

**A Google-összevonás konfigurálása a PowerShell használatával**
1. Telepítse az Azure AD PowerShell for Graph modul ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) legújabb verzióját.
2. Futtassa a következő parancsot: `Connect-AzureAD`
3. A bejelentkezési kérésben jelentkezzen be a felügyelt globális rendszergazdai fiókkal.  
4. Futtassa az alábbi parancsot: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId <client ID> -ClientSecret <client secret>`
 
   > [!NOTE]
   > Használja az ügyfél-azonosítót és az ügyfél titkos kulcsát az "1. lépés: Google fejlesztői projekt konfigurálása" című témakörben létrehozott alkalmazásból. További információ: [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview). 
 
## <a name="how-do-i-remove-google-federation"></a>Hogyan eltávolítja a Google Federation szolgáltatást?
A Google összevonási telepítőt törölheti. Ha így tesz, a Google vendég felhasználóinak, akik már beváltották a meghívót, nem tudnak majd bejelentkezni. Ha azonban ismét hozzáférést ad az erőforrásokhoz, törölje őket a címtárból, és hívja fel őket. 
 
**A Google-összevonás törlése az Azure AD-portálon**
1. Nyissa meg az [Azure Portal](https://portal.azure.com). A bal oldali ablaktáblán válassza a **Azure Active Directory** lehetőséget. 
2. Válassza a **külső identitások** lehetőséget.
3. Válassza ki **az összes identitás szolgáltatót**.
4. A **Google** sorban válassza a három pontot (**...**), majd kattintson a **Törlés** gombra. 
   
   ![A közösségi személyazonosság-szolgáltató törlés gombját megjelenítő képernyőkép.](media/google-federation/google-social-identity-providers.png)

1. A törlés megerősítéséhez válassza az **Igen** lehetőséget. 

**A Google-összevonás törlése a PowerShell használatával** 
1. Telepítse az Azure AD PowerShell for Graph modul ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) legújabb verzióját.
2. Futtassa a `Connect-AzureAD` parancsot.  
4. A bejelentkezési kérésben jelentkezzen be a felügyelt globális rendszergazdai fiókkal.  
5. Írja be a következő parancsot:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > További információ: [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview).
