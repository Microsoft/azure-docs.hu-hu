---
title: A bejelentkezés automatikus gyorsításának konfigurálása a kezdőlap-felderítéssel
description: Ismerje meg, hogyan konfigurálhatja a kezdőlap-felderítési szabályzatot Azure Active Directory összevont felhasználók hitelesítéséhez, beleértve az automatikus gyorsítást és a tartománymutatókat.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/12/2021
ms.author: iangithinji
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1af80979a4712f6d25d994835128f9d5d2205f42
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534729"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Az Azure Active Directory bejelentkezési viselkedésének konfigurálása egy alkalmazáshoz a Kezdőlapfelderítési szabályzat használatával

Ez a cikk bevezetőt nyújt az összevont Azure Active Directory a kezdőlap-felderítési (HRD) házirendet használó összevont felhasználók hitelesítési viselkedésének konfigurálásához.  Ez a cikk az automatikus gyorsítás használatának a felhasználónevek beviteli képernyőjét kihagyja, és automatikusan továbbítja a felhasználókat az összevont bejelentkezési végpontokra.  A Microsoft már nem javasolja az automatikus gyorsítás konfigurálását, mivel megakadályozhatja erősebb hitelesítési módszerek, például a FIDO használatát, és akadályozhatja az együttműködést.

## <a name="home-realm-discovery"></a>Kezdőtartomány felderítése

A kezdőlap-felderítés (HOME Realm Discovery, HRD) az a folyamat, amely lehetővé teszi az Azure Active Directory (Azure AD) számára annak megállapítását, hogy a felhasználónak melyik identitásszolgáltatóval (IdP) kell hitelesítést végeznie a bejelentkezéskor.  Amikor egy felhasználó bejelentkezik egy Azure AD-bérlőbe egy erőforrás eléréséhez, vagy az Azure AD közös bejelentkezési oldalára, begépel egy felhasználónevet (UPN). Az Azure AD ezzel deríti fel, hogy a felhasználónak hol kell bejelentkeznie.

A felhasználó a következő identitásszolgáltatók egyikéhez lesz áthozva a hitelesítéshez:

- A felhasználó otthoni bérlője (lehet ugyanaz a bérlő, mint a felhasználó által elérni kívánt erőforrás).

- Microsoft-fiók lehetőséget.  A felhasználó az erőforrás-bérlő vendége, amely egy felhasználói fiókot használ a hitelesítéshez.

- Helyszíni identitásszolgáltató, például Active Directory összevonási szolgáltatások (AD FS) (AD FS).

- Egy másik identitásszolgáltató, amely össze van ásva az Azure AD-bérlővel.

## <a name="auto-acceleration"></a>Automatikus gyorsítás

Egyes szervezetek a saját bérlőjükben Azure Active Directory konfigurálják a tartományokat, hogy más, például felhasználói hitelesítésre AD FS idP-t használjanak.  

Amikor egy felhasználó bejelentkezik egy alkalmazásba, először jelennek meg egy Azure AD bejelentkezési oldal. Miután begépelték az UPN-jüket, összevont tartományban vannak, a tartományt kiszolgáló internetszolgáltató bejelentkezési oldalára lépnek. Bizonyos körülmények között előfordulhat, hogy a rendszergazdák a bejelentkezési oldalra szeretnék irányítani a felhasználókat, amikor adott alkalmazásokba jelentkeznek be.

Ennek eredményeképpen a felhasználók kihagyhatja a kezdeti Azure Active Directory lapot. Ezt a folyamatot "bejelentkezés automatikus gyorsításának" nevezzük.

Abban az esetben, ha a bérlőt egy másik, bejelentkezéshez való idP-hez összevonták, az automatikus gyorsítás egyszerűbbé teszi a felhasználói bejelentkezést.  Az automatikus gyorsítást konfigurálhatja az egyes alkalmazásokhoz.

>[!NOTE]
>Ha automatikus gyorsításra konfigurál egy alkalmazást, a felhasználók nem használhatnak felügyelt hitelesítő adatokat (például FIDO), és a vendégfelhasználók nem tudnak bejelentkezni. Ha egy felhasználót közvetlenül egy összevont azonosítóhoz használ a hitelesítéshez, nincs mód arra, hogy visszajöjjön a Azure Active Directory bejelentkezési oldalára. Azok a vendégfelhasználók, akiknek más bérlőkhöz vagy egy külső, például egy Microsoft-fiók-hoz kell irányítaniuk, nem tudnak bejelentkezni az alkalmazásba, mert kihagyják a Kezdőlap-felderítés lépést.  

Az összevont idP-be való automatikus gyorsítás háromféleképpen szabályozható:

- Tartományi [tipp használata](#domain-hints) egy alkalmazás hitelesítési kéréséhez.
- Konfigurálja a Kezdőlapfelderítési szabályzatot [az automatikus gyorsítás kényszerítésében.](#home-realm-discovery-policy-for-auto-acceleration)
- Konfigurálja úgy a Kezdőlapfelderítési szabályzatot, hogy figyelmen [kívül](prevent-domain-hints-with-home-realm-discovery.md) hagyja az adott alkalmazásoktól vagy bizonyos tartományoktól származó tartománymutatókat.

### <a name="domain-hints"></a>Tartománymutatók

A tartománymutatók olyan direktívák, amelyek egy alkalmazás hitelesítési kérésében szerepelnek. Ezek segítségével felgyorsítható a felhasználó számára az összevont idP bejelentkezési oldala. Vagy egy több-bérlős alkalmazás is használhatja őket, hogy felgyorsítsa a felhasználót közvetlenül a bérlőhöz használt, védjegyes Azure AD bejelentkezési oldalára.  

A "largeapp.com" alkalmazás például lehetővé teheti, hogy az ügyfeleik egy egyéni URL-címen ,"contoso.largeapp.com" Az alkalmazás tartalmazhat egy tartománymutatót is, contoso.com a hitelesítési kérelemben.

A tartománymutató szintaxisa a használt protokolltól függően változik, és általában az alkalmazásban van konfigurálva.

**WS-Federation:** whr=contoso.com a lekérdezési sztringben.

**SAML:** Egy tartománymutatót tartalmazó SAML-hitelesítési kérelem vagy egy whr=contoso.com lekérdezési sztring.

**Open ID Connect:** A lekérdezési sztring domain_hint=contoso.com.

Alapértelmezés szerint az Azure AD megkísérli átirányítani a bejelentkezést a tartományhoz konfigurált internetszolgáltatóra, ha az alábbiak közül mindkettő igaz: 

- Az alkalmazástól és az alkalmazástól származó hitelesítési kérelem tartománymutatót **tartalmaz,**
- A bérlő ezzel a tartománnyal van össze egyesülve.

Ha a tartománymutató nem hivatkozik ellenőrzött összevont tartományra, a rendszer figyelmen kívül hagyja.

További információ az automatikus gyorsításról a Azure Active Directory által támogatott tartománymutatók használatával: [Enterprise Mobility + Security blog.](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/)

>[!NOTE]
>Ha egy tartománymutatót tartalmaz egy hitelesítési kérelem, és azt figyelembe kell [venni,](#home-realm-discovery-policy-to-prevent-auto-acceleration)a jelenléte felülírja az alkalmazáshoz a HRD-házirendben beállított automatikus gyorsítást.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Kezdőlap-felderítési szabályzat automatikus gyorsításhoz

Egyes alkalmazások nem biztosítanak lehetőséget az általa kibocsátott hitelesítési kérelem konfigurálára. Ezekben az esetekben nem lehet tartománymutatókat használni az automatikus gyorsítás vezérléséhez. Az automatikus gyorsítás a kezdőlap-felderítési szabályzaton keresztül konfigurálható úgy, hogy ugyanezt a viselkedést éri el.  

### <a name="home-realm-discovery-policy-to-prevent-auto-acceleration"></a>Kezdőlap-felderítési szabályzat az automatikus gyorsítás megakadályozásához

Egyes Microsoft- és SaaS-alkalmazások automatikusan tartalmaznak domain_hints -t (például egy hozzáfűző bejelentkezési kérést ad vissza), ami megzavarhatja a felügyelt hitelesítő adatok, például `https://outlook.com/contoso.com` a `&domain_hint=contoso.com` FIDO használatát.  A [Kezdőlaptartomány-felderítési](/graph/api/resources/homeRealmDiscoveryPolicy) szabályzat használatával figyelmen kívül hagyhatja bizonyos alkalmazások vagy bizonyos tartományok tartománymutatóit a felügyelt hitelesítő adatok telepítése során.  

## <a name="enable-direct-ropc-authentication-of-federated-users-for-legacy-applications"></a>Összevont felhasználók közvetlen ROPC-hitelesítésének engedélyezése örökölt alkalmazásokhoz

Az ajánlott eljárás az, hogy az alkalmazások AAD-kódtárakat és interaktív bejelentkezést használjanak a felhasználók hitelesítéséhez. A kódtárak gondoskodnak az összevont felhasználói folyamatokról.  Előfordulhat, hogy az örökölt alkalmazások, különösen azok, amelyek ROPC-t használnak, közvetlenül az Azure AD-be küldik a felhasználónevet és a jelszót, és nem az összevonás érthetőségéhez vannak megírva. Nem végeznek otthoni tartományfelderítést, és nem lépnek kapcsolatba a megfelelő összevont végponttal a felhasználók hitelesítéséhez. Ha úgy dönt, a HRD-szabályzattal engedélyezheti az olyan örökölt alkalmazásokat, amelyek a ROPC-engedély használatával küldik el a felhasználónév-jelszó hitelesítő adatokat a közvetlen hitelesítéshez a Azure Active Directory. Engedélyezni kell a jelszó kivonatszinkronizálását.

> [!IMPORTANT]
> Csak akkor engedélyezze a közvetlen hitelesítést, ha be van kapcsolva a jelszó kivonatszinkronizálása, és tudja, hogy az alkalmazás hitelesítése nem szükséges a helyszíni identitásszolgáltató által megvalósított szabályzatok nélkül. Ha bármilyen okból kikapcsolja a jelszó-kivonatszinkronizálást, vagy bármilyen okból kikapcsolja a címtár-szinkronizálást az AD Connect használatával, távolítsa el ezt a szabályzatot, hogy megakadályozza az elavult jelszó-kivonatot használó közvetlen hitelesítés lehetőségét.

## <a name="set-hrd-policy"></a>HRD-szabályzat beállítása

Három lépésből áll a HRD-szabályzat beállítása egy alkalmazáshoz az összevont bejelentkezés automatikus gyorsításához vagy közvetlen felhőalapú alkalmazásokhoz:

1. HRD-szabályzat létrehozása.

2. Keresse meg azt a szolgáltatásnév, amelyhez hozzá kell csatolni a szabályzatot.

3. Csatolja a szabályzatot a szolgáltatásnévhez.

A szabályzatok csak egy adott alkalmazásra vonatkoznak, ha szolgáltatásnévhez vannak csatolva.

Egy szolgáltatásnéven egyszerre csak egy HRD-szabályzat lehet aktív.  

A hrd-Azure Active Directory powerShell-parancsmagok használatával hozhatja létre és kezelheti.

Az alábbiakban egy HRD-szabályzatdefiníciót mutatunk be:

 ```JSON
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu",
    "AllowCloudPasswordValidation":false,    
    }
   }
```

A szabályzat típusa "[HomeRealmDiscoveryPolicy](/graph/api/resources/homeRealmDiscoveryPolicy)".

**Az AccelerateToFederatedDomain nem** kötelező. Ha **az AccelerateToFederatedDomain** értéke hamis, a szabályzatnak nincs hatása az automatikus gyorsításra. Ha **az AccelerateToFederatedDomain** igaz, és csak egy ellenőrzött és összevont tartomány található a bérlőben, akkor a felhasználók közvetlenül az összevont internetszolgáltatóhoz lesznek rendelve a bejelentkezéshez. Ha igaz, és a bérlőben egynél több ellenőrzött tartomány található, meg kell adni a **PreferredDomain** értéket.

**A PreferredDomain** paraméter megadása nem kötelező. **A PreferredDomain tulajdonságnak** azt a tartományt kell jeleznie, amely felé a gyorsítást el kell látni. Kihagyható, ha a bérlő csak egy összevont tartománnyal rendelkezik.  Ha nincs megadva, és több ellenőrzött összevont tartomány van, a szabályzatnak nincs hatása.

 Ha **a PreferredDomain** (Előnyben részesített tartomány) meg van adva, akkor egyeznie kell a bérlő ellenőrzött, összevont tartományának. Az alkalmazás összes felhasználója bejelentkezhet ebbe a tartományba – azok a felhasználók, akik nem tudnak bejelentkezni az összevont tartományba, nem fognak tudni bejelentkezni, és nem tudnak bejelentkezni.

**Az AllowCloudPasswordValidation paraméter megadása** nem kötelező. Ha az **AllowCloudPasswordValidation** igaz, akkor az alkalmazás hitelesíthet egy összevont felhasználót azáltal, hogy a felhasználónév/jelszó hitelesítő adatokat közvetlenül a Azure Active Directory jogkivonat végpontjának adja meg. Ez csak akkor működik, ha a jelszó kivonatszinkronizálása engedélyezve van.

Emellett két bérlőszintű HRD-beállítás is létezik, amelyek a fentiekben nem láthatók:

- **Az AlternateIdLogin nem** kötelező.  Ha engedélyezve van, a felhasználók az Azure AD bejelentkezési oldalán, az [UPN-jük](../authentication/howto-authentication-use-email-signin.md) helyett az e-mail-címüket használva jelentkeznek be.  A másodlagos azonosítók arra támaszkodnak, hogy a felhasználó nincs automatikusan felgyorsítva egy összevont idP-hez.

- **A DomainHintPolicy** egy nem kötelező, összetett objektum, amely megakadályozza, hogy a tartománymutatók automatikusan felgyorsítsák a felhasználókat [  összevont tartományokra.](prevent-domain-hints-with-home-realm-discovery.md) Ezzel a bérlői szintű beállítással biztosítható, hogy a tartománymutatókat küldeni képes alkalmazások ne akadályozzák meg a felhasználókat a felhőben felügyelt hitelesítő adatokkal való bejelentkezésben.

### <a name="priority-and-evaluation-of-hrd-policies"></a>A HRD-szabályzatok prioritása és értékelése

HRD-szabályzatokat lehet létrehozni, majd hozzárendelni adott szervezetekhez és szolgáltatásnévhez. Ez azt jelenti, hogy egy adott alkalmazásra több szabályzat is vonatkozhat, ezért az Azure AD-nek kell eldöntenie, melyik élvez elsőbbséget. A szabályok halmaza dönti el, hogy melyik (több alkalmazott HRD-szabályzat) lép életbe:

- Ha tartománymutató található a hitelesítési kérelemben, a rendszer ellenőrzi a bérlő HRD-szabályzatát (a bérlő alapértelmezett szabályzata), és ellenőrzi, hogy a tartománymutatókat [figyelmen kívül kell-e hagyni.](prevent-domain-hints-with-home-realm-discovery.md) Ha a tartománymutatók engedélyezettek, a rendszer a tartománymutató által meghatározott viselkedést használja.

- Ellenkező esetben, ha egy szabályzat explicit módon hozzá van rendelve a szolgáltatásnévhez, a rendszer kényszeríti azt.

- Ha nincs tartománymutató, és nincs explicit módon hozzárendelve szabályzat a szolgáltatásnévhez, a rendszer kényszeríti a szolgáltatásnév szülőszervezetéhez explicit módon hozzárendelt szabályzatot.

- Ha nincs tartománymutató, és nincs hozzárendelve szabályzat a szolgáltatásnévhez vagy a szervezethez, a rendszer az alapértelmezett HRD-viselkedést használja.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Oktatóanyag a HRD-szabályzat alkalmazáson való beállításához

Az Azure AD PowerShell-parancsmagok segítségével végigveszünk néhány forgatókönyvet, például a következőket:

- HRD-szabályzat beállítása egy egyetlen összevont tartománnyal rendelkező bérlőben található alkalmazás automatikus gyorsításához.

- HRD-szabályzat beállítása egy alkalmazás automatikus gyorsításához a bérlő által ellenőrzött tartományok egyikéhez.

- HRD-szabályzat beállítása, hogy egy örökölt alkalmazás közvetlen felhasználónév-jelszó hitelesítést Azure Active Directory összevont felhasználó számára.

- Felsorolja az alkalmazásokat, amelyekhez szabályzat van konfigurálva.

### <a name="prerequisites"></a>Előfeltételek

A következő példákban szabályzatokat hozhat létre, frissíthet, kapcsolhet össze és törölhet az Azure AD-beli alkalmazás-szolgáltatásnévekkel.

1. Először töltse le az Azure AD PowerShell-parancsmagok legújabb előzetes verzióját.

2. Miután letöltötte az Azure AD PowerShell-parancsmagokat, futtassa a Connect parancsot az Azure AD-be való bejelentkezéshez a rendszergazdai fiókjával:

    ``` powershell
    Connect-AzureAD -Confirm
    ```

3. Futtassa a következő parancsot a szervezet összes szabályzatának a futtatásával:

    ``` powershell
    Get-AzureADPolicy
    ```

Ha semmit nem ad vissza, az azt jelenti, hogy nem hozott létre szabályzatokat a bérlőben.

### <a name="example-set-an-hrd-policy-for-an-application"></a>Példa: HRD-szabályzat beállítása egy alkalmazáshoz

Ebben a példában egy olyan szabályzatot hoz létre, amely akkor lesz hozzárendelve egy alkalmazáshoz, ha:

- Automatikusan felgyorsítja a felhasználók AD FS bejelentkezési képernyőjére, amikor bejelentkeznek egy alkalmazásba, ha a bérlő egyetlen tartományból áll.
- A felhasználók automatikus felgyorsítása AD FS bejelentkezési képernyőn a bérlőben több összevont tartomány is található.
- Engedélyezi a nem interaktív felhasználónév/jelszó közvetlen bejelentkezést Azure Active Directory az összevont felhasználók számára a szabályzathoz rendelt alkalmazásokhoz.

#### <a name="step-1-create-an-hrd-policy"></a>1. lépés: HRD-szabályzat létrehozása

Az alábbi szabályzat automatikusan felgyorsítja a AD FS bejelentkezési képernyőjére, amikor bejelentkeznek egy alkalmazásba, amikor a bérlő egyetlen tartományt rendelkezik.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Az alábbi szabályzat automatikusan felgyorsítja a felhasználókat egy AD FS bejelentkezési képernyőjére, amely több összevont tartományt is a bérlőn található. Ha több összevont tartománya van, amely hitelesíti a felhasználókat az alkalmazásokhoz, meg kell adnia a tartományt az automatikus gyorsításhoz.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Ha olyan szabályzatot hoz létre, amely engedélyezi a felhasználónevet/jelszót használó hitelesítést az összevont felhasználók számára közvetlenül, Azure Active Directory adott alkalmazásokhoz tartozó házirendeket, futtassa a következő parancsot:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```

Futtassa a következő parancsot az új szabályzat és az **ObjectID** lekért adatokat:

``` powershell
Get-AzureADPolicy
```

A HRD-szabályzatot a létrehozása után több alkalmazás-szolgáltatásnévhez is hozzárendelheti.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>2. lépés: Keresse meg azt a szolgáltatásnév, amelyhez hozzá kell rendelni a szabályzatot

Szüksége lesz annak a szolgáltatásnévnek az **ObjectID-ére,** amelyhez hozzá szeretné rendelni a szabályzatot. A szolgáltatásnév **objektumazonosítóját** többféleképpen is megkereshet.

Használhatja a portált, vagy lekérdezheti a [Microsoft Graph.](/graph/api/resources/serviceprincipal) A Graph [Explorer](https://developer.microsoft.com/graph/graph-explorer) eszközt is használhatja, és bejelentkezhet Azure AD-fiókjába, és láthatja a szervezet összes egyszerű szolgáltatását.

Mivel a PowerShellt használja, a következő parancsmag használatával listázódhat a szolgáltatásnévvel és azok az adatokat használhatja.

``` powershell
Get-AzureADServicePrincipal
```

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>3. lépés: A szabályzat hozzárendelése a szolgáltatásnévhez

Miután megvan annak az **alkalmazásnak** az ObjectID-értéke, amelyhez az automatikus gyorsítást konfigurálni szeretné, futtassa a következő parancsot. Ez a parancs társítja az 1. lépésben létrehozott HRD-szabályzatot a 2. lépésben található szolgáltatásnévvel.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Ezt a parancsot megismételheti minden olyan szolgáltatásnévvel, amelyhez hozzá szeretné adni a szabályzatot.

Abban az esetben, ha egy alkalmazáshoz már hozzá van rendelve egy HomeRealmDiscovery szabályzat, nem adhat hozzá újabbat.  Ebben az esetben módosítsa az alkalmazáshoz rendelt Kezdőlap-felderítési szabályzat definícióját további paraméterek hozzáadásához.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>4. lépés: Annak ellenőrzése, hogy a HRD-szabályzat mely alkalmazás-szolgáltatásnévkhöz van hozzárendelve

A **Get-AzureADPolicyAppliedObject** parancsmag használatával ellenőrizheti, hogy mely alkalmazásokra van HRD-szabályzat konfigurálva. Adja meg annak a **szabályzatnak** az ObjectID-ját, amelyről ellenőrizni szeretne.

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

#### <a name="step-5-youre-done"></a>5. lépés: Kész!

Próbálja ki az alkalmazást, és ellenőrizze, hogy működik-e az új szabályzat.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Példa: Azon alkalmazások felsorolása, amelyekhez HRD-szabályzat van konfigurálva

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>1. lépés: A szervezetben létrehozott összes szabályzat felsorolása

``` powershell
Get-AzureADPolicy
```

Jegyezze fel annak a szabályzatnak az **ObjectID-ját,** amely számára a hozzárendeléseket listába szeretné sorolni.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>2. lépés: Azon szolgáltatásnév felsorolása, amelyekhez a szabályzat hozzá van rendelve  

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-from-an-application"></a>Példa: HRD-szabályzat eltávolítása egy alkalmazásból

#### <a name="step-1-get-the-objectid"></a>1. lépés: Az ObjectID lekért száma

Az előző példában  lekérte a szabályzat ObjectID-ját, valamint annak az alkalmazás-szolgáltatásnévnek az objektumazonosítóját, amelyből el szeretné távolítani.

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>2. lépés: A szabályzat-hozzárendelés eltávolítása az alkalmazás szolgáltatásnévből  

``` powershell
Remove-AzureADServicePrincipalPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>3. lépés: Az eltávolítás ellenőrzése azokkal a szolgáltatásnévvel, amelyekhez a szabályzat hozzá van rendelve

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

## <a name="next-steps"></a>Következő lépések

- További információ az Azure AD-hitelesítésről: Hitelesítési [forgatókönyvek az Azure AD-hez.](../develop/authentication-vs-authorization.md)
- További információ a felhasználói egyszeri bejelentkezésről: Egyszeri bejelentkezés az alkalmazásokba a [Azure Active Directory.](what-is-single-sign-on.md)
- Keresse fel [a Microsoft identitásplatformját,](../develop/v2-overview.md) és áttekintheti a fejlesztőkre vonatkozó összes tartalmat.