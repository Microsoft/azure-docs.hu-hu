---
title: Bejelentkezés automatikus gyorsításának beállítása a Kezdőlap tartományának felderítésével
description: Ismerje meg, hogyan konfigurálhatja a Kezdőlap tartomány-felderítési házirendet az összevont felhasználók Azure Active Directory hitelesítéséhez, beleértve az automatikus gyorsítást és a tartományi tippeket.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1300ecff416962bda4da800c5eff134951658846
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159165"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Az alkalmazások Azure Active Directory bejelentkezési viselkedésének konfigurálása egy otthoni tartomány felderítési házirendjének használatával

Ez a cikk bevezetést nyújt az összevont felhasználók Azure Active Directory hitelesítési viselkedésének konfigurálásához. Magában foglalja az összevont tartományokban lévő felhasználók automatikus gyorsítási és hitelesítési korlátozásainak konfigurálását.

## <a name="home-realm-discovery"></a>Kezdőtartomány felderítése
A Kezdőlap tartományának felderítése (HRD) az a folyamat, amely lehetővé teszi a Azure Active Directory (Azure AD) számára annak meghatározását, hogy a felhasználó milyen időpontban kell hitelesítenie magát a bejelentkezéskor.  Amikor egy felhasználó bejelentkezik egy Azure AD-bérlőbe egy erőforrás eléréséhez vagy az Azure AD Common bejelentkezési oldalához, a Felhasználónév (UPN) nevet adja meg. Az Azure AD használatával felderítheti, hogy a felhasználónak hol kell bejelentkeznie. 

Előfordulhat, hogy a felhasználót a következő webhelyek hitelesítésére kell elvégezni:

- A felhasználó otthoni bérlője (lehet ugyanaz a bérlő, mint a felhasználó által elérni próbált erőforrás). 

- Microsoft-fiók.  A felhasználó egy vendég az erőforrás-bérlőben.

-  Helyszíni identitás-szolgáltató, például Active Directory összevonási szolgáltatások (AD FS) (AD FS).

- Egy másik, az Azure AD-Bérlővel összevont identitás-szolgáltató.

## <a name="auto-acceleration"></a>Automatikus gyorsítás 
Egyes szervezetek úgy konfigurálja a Azure Active Directory-bérlő tartományait, hogy összevonása egy másik identitásszolgáltató, például AD FS a felhasználói hitelesítéshez.  

Amikor egy felhasználó bejelentkezik egy alkalmazásba, először egy Azure AD-bejelentkezési oldal jelenik meg. Miután beírtak az UPN-t, ha egy összevont tartományban vannak, akkor a rendszer a tartományt kiszolgáló identitásszolgáltató bejelentkezési lapjára kerül. Bizonyos esetekben előfordulhat, hogy a rendszergazdák a bejelentkezési oldalra irányítják a felhasználókat, amikor egy adott alkalmazásba jelentkeznek be. 

Így a felhasználók kihagyhatják a kezdeti Azure Active Directory lapot. Ezt a folyamatot "bejelentkezési automatikus gyorsításnak" nevezzük.

Azokban az esetekben, amikor a bérlő egy másik identitásszolgáltató összevonása a bejelentkezéshez, az automatikus gyorsítás egyszerűbbé teszi a felhasználói bejelentkezést.  Az automatikus gyorsítást az egyes alkalmazásokhoz is konfigurálhatja.

>[!NOTE]
>Ha automatikus gyorsításra konfigurál egy alkalmazást, a vendég felhasználók nem jelentkezhetnek be. Ha a felhasználó egyenesen egy összevont identitásszolgáltató végez hitelesítésre, nincs lehetőség arra, hogy visszakerüljön a Azure Active Directory bejelentkezési oldalára. A vendég felhasználókat, akiket esetleg más bérlők vagy külső identitásszolgáltató kell átirányítani, például egy Microsoft-fiók, nem tud bejelentkezni az alkalmazásba, mert kihagyják a Kezdőlap tartomány felderítése lépést.  

Az automatikus gyorsítást kétféleképpen lehet vezérelni egy összevont identitásszolgáltató:   

- Egy alkalmazáshoz tartozó hitelesítési kérelmekre vonatkozó tartományi emlékeztető használata. 
- Konfiguráljon egy otthoni tartomány-felderítési házirendet az automatikus gyorsítás engedélyezéséhez.

### <a name="domain-hints"></a>Tartományi útmutatók    
A tartományi útmutatók olyan irányelvek, amelyek egy alkalmazás hitelesítési kérelmében szerepelnek. Felhasználhatják a felhasználót az összevont identitásszolgáltató bejelentkezési oldalára. Vagy egy több-bérlős alkalmazás is felhasználhatja, hogy a felhasználó egyenesen a márkás Azure AD bejelentkezési oldalára felgyorsítsa a bérlőt.  

A "largeapp.com" alkalmazás például lehetővé teheti ügyfelei számára, hogy a "contoso.largeapp.com" egyéni URL-címen férjenek hozzá az alkalmazáshoz. Az alkalmazás tartalmazhat olyan tartományi emlékeztetőt is, amely contoso.com a hitelesítési kérelemben. 

A tartomány-emlékeztető szintaxisa a használt protokolltól függ, és általában az alkalmazásban van konfigurálva.

**WS-Federation**: whr = contoso. com a lekérdezési karakterláncban.

**SAML**: vagy egy olyan SAML hitelesítési kérelem, amely tartalmaz egy tartományi emlékeztetőt vagy egy whr = contoso. com lekérdezési karakterláncot.

**Open ID-kapcsolat**: A lekérdezési karakterlánc domain_hint = contoso. com. 

Ha egy tartományi emlékeztető szerepel az alkalmazás hitelesítési kérelmében, és a bérlő összevonta az adott tartománnyal, az Azure AD megkísérli átirányítani a bejelentkezést az adott tartományhoz konfigurált identitásszolgáltató. 

Ha a tartományra mutató hivatkozás nem egy ellenőrzött összevont tartományra hivatkozik, a rendszer figyelmen kívül hagyja, és meghívja a Kezdőlap tartományának normál felderítését.

Az Azure Active Directory által támogatott tartományi útmutatók használatával kapcsolatos további információkért tekintse meg a [Enterprise Mobility + Security blogot](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/).

>[!NOTE]
>Ha a hitelesítési kérelem tartalmaz egy tartományi emlékeztetőt, annak jelenléte felülbírálja az alkalmazáshoz beállított automatikus gyorsítást a HRD-házirendben.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Kezdőlap tartomány-felderítési szabályzata automatikus gyorsításhoz
Egyes alkalmazások nem biztosítják az általuk kibocsátott hitelesítési kérelem konfigurálásának módját. Ezekben az esetekben nem lehetséges a tartományi útmutatók használata az automatikus gyorsítás szabályozására. Az automatikus gyorsítás a szabályzaton keresztül konfigurálható az azonos viselkedés érdekében.  

## <a name="enable-direct-authentication-for-legacy-applications"></a>Közvetlen hitelesítés engedélyezése örökölt alkalmazásokhoz
Az ajánlott eljárás az, ha az alkalmazások HRE-kódtárakat és interaktív bejelentkezést használnak a felhasználók hitelesítéséhez. A kódtárak gondoskodnak az összevont felhasználói folyamatokról.  Előfordulhat, hogy az örökölt alkalmazások nem az összevonás megismerésére vannak írva. Nem végeznek otthoni tartományi felderítést, és nem működnek együtt a megfelelő összevont végponttal a felhasználók hitelesítéséhez. Ha úgy dönt, hogy a-t használja, a HRD házirend segítségével engedélyezheti a felhasználóneveket és jelszavakat hitelesítő adatokat elküldő örökölt alkalmazásokat, hogy közvetlenül a Azure Active Directory használatával hitelesítsék magukat. A jelszó-kivonatolási szinkronizálást engedélyezni kell. 

> [!IMPORTANT]
> Csak akkor engedélyezze a közvetlen hitelesítést, ha a jelszó-kivonat szinkronizálása be van kapcsolva, és tudja, hogy a helyszíni identitásszolgáltató által megvalósított szabályzatok nélkül hitelesíti az alkalmazást. Ha kikapcsolja a jelszó-kivonatolási szinkronizálást, vagy kikapcsolja a címtár-szinkronizálást az AD-csatlakozással bármilyen okból, távolítsa el ezt a házirendet, hogy megakadályozza a közvetlen hitelesítés használatát elavult jelszó-kivonattal.

## <a name="set-hrd-policy"></a>HRD házirend beállítása
Az összevont bejelentkezési automatikus gyorsítás vagy a közvetlen felhőalapú alkalmazások esetében három lépés van a HRD szabályzat beállítására:

1. Hozzon létre egy HRD szabályzatot.

2. Keresse meg azt a szolgáltatásnevet, amelyhez csatolni szeretné a szabályzatot.

3. Csatolja a szabályzatot az egyszerű szolgáltatáshoz. 

A szabályzatok csak akkor lépnek érvénybe, ha egy adott alkalmazáshoz csatolva vannak egy egyszerű szolgáltatáshoz. 

Egyszerre csak egy HRD-házirend lehet aktív egy egyszerű szolgáltatásban.  

A HRD szabályzat létrehozásához vagy kezeléséhez használhatja közvetlenül a Microsoft Azure Active Directory Graph API-t, vagy a Azure Active Directory PowerShell-parancsmagokat.

A házirendet kezelő Graph API az MSDN-beli [Operations on Policy](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) című cikk ismerteti.

A következő példa egy HRD házirend-definíciót mutat be:
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu",
    "AllowCloudPasswordValidation":true
    }
   }
```

A házirend típusa "HomeRealmDiscoveryPolicy".

A **AccelerateToFederatedDomain** nem kötelező. Ha a **AccelerateToFederatedDomain** hamis, a házirend nem befolyásolja az automatikus gyorsítást. Ha a **AccelerateToFederatedDomain** értéke igaz, és a bérlőn csak egy ellenőrzött és összevont tartomány található, akkor a felhasználók egyenesen az összevont identitásszolgáltató lesznek elküldve a bejelentkezéshez. Ha az értéke igaz, és több ellenőrzött tartomány van a bérlőben, meg kell adni a **PreferredDomain** .

A **PreferredDomain** nem kötelező. A **PreferredDomain** meg kell határoznia azt a tartományt, amelyre fel kell gyorsítani. A kihagyható, ha a bérlő csak egy összevont tartománnyal rendelkezik.  Ha nincs megadva, és több ellenőrzött összevont tartomány van, akkor a házirendnek nincs hatása.

 Ha a **PreferredDomain** meg van adva, akkor meg kell egyeznie egy ellenőrzött, összevont tartománnyal a bérlő számára. Az alkalmazás összes felhasználójának képesnek kell lennie arra, hogy bejelentkezzen az adott tartományba.

A **AllowCloudPasswordValidation** nem kötelező. Ha a **AllowCloudPasswordValidation** értéke igaz, akkor az alkalmazásnak hitelesítenie kell egy összevont felhasználót úgy, hogy a Felhasználónév/jelszó hitelesítő adatait közvetlenül az Azure Active Directory jogkivonat-végponthoz mutatja. Ez csak akkor működik, ha a jelszó kivonatának szinkronizálása engedélyezve van.

### <a name="priority-and-evaluation-of-hrd-policies"></a>A HRD házirendek prioritása és kiértékelése
HRD szabályzatok hozhatók létre, majd hozzárendelhetők adott szervezetekhez és egyszerű szolgáltatásokhoz. Ez azt jelenti, hogy több házirend is alkalmazható egy adott alkalmazásra. A hatályba HRD szabályzat a következő szabályokat követi:


- Ha egy tartományi tipp szerepel a hitelesítési kérelemben, akkor a rendszer figyelmen kívül hagyja a HRD-házirendet az automatikus gyorsításhoz. A rendszer a tartomány-emlékeztető által megadott viselkedést használja.

- Ellenkező esetben, ha egy házirendet explicit módon rendel hozzá az egyszerű szolgáltatáshoz, a rendszer kényszeríti. 

- Ha nincs tartományi javaslat, és nincs kifejezetten hozzárendelve az egyszerű szolgáltatáshoz, a rendszer kikényszeríti az egyszerű szolgáltatás fölérendelt szervezetéhez hozzárendelt szabályzatot. 

- Ha nincs tartományi emlékeztető, és nem rendelt hozzá szabályzatot a szolgáltatásnév vagy a szervezet számára, a rendszer az alapértelmezett HRD-viselkedést használja.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Oktatóanyag a HRD szabályzat alkalmazáshoz való beállításához 
Az Azure AD PowerShell-parancsmagok használatával néhány forgatókönyvet ismertetünk, többek között:


- A HRD házirend beállítása egy összevont tartománnyal rendelkező bérlőn lévő alkalmazás automatikus gyorsítására.

- A HRD házirend beállítása úgy, hogy az alkalmazás automatikus gyorsítást végezzen a bérlő számára ellenőrzött több tartomány egyikén.

- A HRD házirend beállítása, amely lehetővé teszi egy örökölt alkalmazás számára, hogy közvetlen felhasználónevet vagy jelszót engedélyezzen a Azure Active Directory egy összevont felhasználó számára.

- Azon alkalmazások listázása, amelyekhez házirend van konfigurálva.


### <a name="prerequisites"></a>Előfeltételek
Az alábbi példákban az Azure AD-ben létrehozhatja, frissítheti, összekapcsolhatja és törölheti a házirendeket az Application Service-rendszerbiztonsági tag-ben.

1.  A kezdéshez töltse le az Azure AD PowerShell-parancsmag legújabb előzetes verzióját. 

2.  Az Azure AD PowerShell-parancsmagok letöltése után futtassa a kapcsolódás parancsot az Azure AD-be a rendszergazdai fiókjával való bejelentkezéshez:

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  A következő parancs futtatásával tekintheti meg a szervezet összes házirendjét:

    ``` powershell
    Get-AzureADPolicy
    ```

Ha a rendszer nem ad vissza semmit, az azt jelenti, hogy nincsenek szabályzatok létrehozva a bérlőben.

### <a name="example-set-hrd-policy-for-an-application"></a>Példa: HRD szabályzat beállítása egy alkalmazáshoz 

Ebben a példában egy olyan házirendet hoz létre, amely akkor van hozzárendelve egy alkalmazáshoz, ha a következők valamelyike: 
- Automatikusan felgyorsítja a felhasználókat egy AD FS bejelentkezési képernyőre, amikor bejelentkeznek egy alkalmazásba, ha a bérlő egyetlen tartományba esik. 
- A felhasználók automatikus felgyorsítása a AD FS bejelentkezési képernyőjén a bérlő több összevont tartománya van.
- A nem interaktív Felhasználónév-/jelszó-bejelentkezés engedélyezése közvetlenül a Azure Active Directory az összevont felhasználók számára azon alkalmazások esetében, amelyekhez a szabályzat hozzá van rendelve.

#### <a name="step-1-create-an-hrd-policy"></a>1\. lépés: HRD szabályzat létrehozása

A következő házirend automatikusan felgyorsítja a felhasználókat egy AD FS bejelentkezési képernyőre, amikor bejelentkeznek egy alkalmazásba, ha a bérlő egyetlen tartományba esik.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```
A következő házirend automatikusan felgyorsítja a felhasználókat a AD FS bejelentkezési képernyőjén, ha több összevont tartomány van a bérlőben. Ha egynél több összevont tartománnyal rendelkezik, amely hitelesíti a felhasználókat az alkalmazásokhoz, a tartományt az automatikus felgyorsításhoz kell megadnia.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Ha olyan házirendet szeretne létrehozni, amely lehetővé teszi a Felhasználónév/jelszó hitelesítését az összevont felhasználók számára közvetlenül Azure Active Directory adott alkalmazásokhoz, futtassa a következő parancsot:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```


A következő parancs futtatásával tekintheti meg az új szabályzatot, és kérheti le a **ObjectId**:

``` powershell
Get-AzureADPolicy
```


Ha az HRD szabályzatot a létrehozása után szeretné alkalmazni, azt hozzárendelheti több egyszerű alkalmazás-szolgáltatáshoz is.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>2\. lépés: az egyszerű szolgáltatásnév megkeresése, amelyhez a szabályzatot hozzá kell rendelni  
Szüksége lesz azon **ObjectId** , amelyekhez hozzá szeretné rendelni a szabályzatot. Az egyszerű szolgáltatások **ObjectId** többféleképpen is megtalálhatja.    

Használhatja a portált, vagy lekérdezheti [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Lépjen a [Graph Explorer eszközre](https://developer.microsoft.com/graph/graph-explorer) , és jelentkezzen be az Azure ad-fiókjába, és tekintse meg az összes szervezet egyszerű szolgáltatását. 

Mivel a PowerShellt használja, az alábbi parancsmaggal listázhatja az egyszerű szolgáltatásokat és azok azonosítóit.

``` powershell
Get-AzureADServicePrincipal
```

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>3\. lépés: a szabályzat társítása a szolgáltatáshoz  
Miután megtörtént annak az alkalmazásnak a **ObjectId** , amelyre az automatikus gyorsítást konfigurálni kívánja, futtassa a következő parancsot. Ez a parancs az 1. lépésben létrehozott HRD-házirendet társítja a 2. lépésben található egyszerű szolgáltatással.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Ezt a parancsot minden olyan egyszerű szolgáltatásnév esetében megismételheti, amelyhez hozzá kívánja adni a szabályzatot.

Abban az esetben, ha egy alkalmazáshoz már hozzá van rendelve egy HomeRealmDiscovery szabályzat, nem adhat hozzá egy másodikat.  Ebben az esetben módosítsa az alkalmazáshoz hozzárendelt Kezdőlap tartomány-felderítési szabályzat definícióját további paraméterek hozzáadásához.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>4\. lépés: annak meghatározása, hogy az HRD szabályzat melyik egyszerű szolgáltatáshoz van rendelve
A **Get-AzureADPolicyAppliedObject** parancsmaggal ellenőrizhető, hogy mely alkalmazások rendelkeznek HRD szabályzattal. Adja át a bejelentkezni kívánt szabályzat **ObjectId** .

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>5\. lépés: elkészült!
Próbálja ki az alkalmazást, és győződjön meg arról, hogy az új házirend működik.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Példa: azoknak az alkalmazásoknak a listája, amelyekhez a HRD házirend konfigurálva van

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>1\. lépés: a szervezetben létrehozott összes szabályzat listázása 

``` powershell
Get-AzureADPolicy
```

Jegyezze fel annak a szabályzatnak a **ObjectId** , amelynek hozzárendeléseit listázni szeretné.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>2\. lépés: azoknak a szolgáltatásoknak a listája, amelyekre a szabályzat hozzá van rendelve  

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-for-an-application"></a>Példa: egy alkalmazás HRD szabályzatának eltávolítása
#### <a name="step-1-get-the-objectid"></a>1\. lépés: a ObjectID beolvasása
Az előző példával beolvashatja a házirend **ObjectId** , valamint azt, hogy az alkalmazás melyik egyszerű szolgáltatását kívánja eltávolítani. 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>2\. lépés: a szabályzat-hozzárendelés eltávolítása az Application Service-résztvevőből  

``` powershell
Remove-AzureADApplicationPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>3\. lépés: az Eltávolítás ellenőrzéséhez a Szabályzathoz hozzárendelt egyszerű szolgáltatások listázásával 

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
## <a name="next-steps"></a>Következő lépések
- További információ a hitelesítés működéséről az Azure AD-ben: [hitelesítési forgatókönyvek az Azure ad-hez](../develop/authentication-scenarios.md).
- A felhasználói egyszeri bejelentkezéssel kapcsolatos további információkért lásd: [egyszeri bejelentkezés a Azure Active Directory alkalmazásaihoz](what-is-single-sign-on.md).
- Látogasson el a [Microsoft Identity platformra](../develop/v2-overview.md) , és tekintse át az összes fejlesztővel kapcsolatos tartalmat.
