---
title: Jelentkezzen be az e-mail-címmel, alternatív bejelentkezési AZONOSÍTÓként Azure Active Directory
description: Megtudhatja, hogyan konfigurálhatja és engedélyezheti a felhasználók számára, hogy bejelentkezzenek Azure Active Directory az e-mail-címükkel, alternatív bejelentkezési AZONOSÍTÓként (előzetes verzió)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 10/01/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: calui
ms.openlocfilehash: 4e39d7f15e3ca3c6e241c767a5f881d7170c6379
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255967"
---
# <a name="sign-in-to-azure-active-directory-using-email-as-an-alternate-login-id-preview"></a>Bejelentkezés Azure Active Directory az e-mail-cím használata másodlagos bejelentkezési AZONOSÍTÓként (előzetes verzió)

> [!NOTE]
> Jelentkezzen be az Azure AD-be a másodlagos bejelentkezési AZONOSÍTÓként a Azure Active Directory nyilvános előzetes verziójára. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Számos szervezet szeretné engedélyezni, hogy a felhasználók a helyszíni címtár-környezettel megegyező hitelesítő adatokkal jelentkezzenek be Azure Active Directoryba (Azure AD-ba). A hibrid hitelesítésnek nevezett megközelítéssel a felhasználóknak csak egy hitelesítő adatot kell megemlékezniük.

Néhány szervezet nem helyezte át a hibrid hitelesítést a következő okok miatt:

* Alapértelmezés szerint az Azure AD egyszerű felhasználóneve (UPN) ugyanarra az egyszerű felhasználónévre van beállítva, mint a helyszíni címtár.
* Az Azure AD UPN módosítása helytelen egyezést hoz létre az olyan helyszíni és Azure AD-környezetek között, amelyek bizonyos alkalmazásokkal és szolgáltatásokkal kapcsolatos problémákat okozhatnak.
* Az üzleti vagy megfelelőségi okok miatt a szervezet nem szeretné a helyszíni UPN-t használni az Azure AD-be való bejelentkezéshez.

Ha segítségre van szüksége a hibrid hitelesítésre való áttéréshez, beállíthatja az Azure AD-t, hogy a felhasználók a hitelesített tartományba tartozó e-mailben jelentkezzenek be egy másik bejelentkezési AZONOSÍTÓként. Ha például a *contoso* a *Fabrikam*-re lett átnevezve, és nem kívánja tovább használni az örökölt `balas@contoso.com` UPN-t, a rendszer mostantól másodlagos Bejelentkezési azonosítóként is használhatja az e-mailt. Egy alkalmazáshoz vagy szolgáltatáshoz való hozzáféréshez a felhasználók a hozzárendelt e-mail-cím használatával bejelentkeznek az Azure AD-be, például: `balas@fabrikam.com` .

Ez a cikk bemutatja, hogyan engedélyezheti és használhatja az e-maileket alternatív bejelentkezési AZONOSÍTÓként. Ez a funkció a ingyenes Azure AD Edition és újabb verziókban érhető el.

> [!NOTE]
> Ez a funkció csak a felhőben hitelesített Azure AD-felhasználók számára érhető el.

> [!NOTE]
> Ez a funkció jelenleg nem támogatott az Azure AD-hez csatlakoztatott Windows 10-es eszközökön a felhőalapú hitelesítéssel rendelkező bérlők esetében. Ez a funkció nem alkalmazható a hibrid Azure AD-hez csatlakoztatott eszközökre.

## <a name="overview-of-azure-ad-sign-in-approaches"></a>Az Azure AD bejelentkezési módszereinek áttekintése

Az Azure AD-be való bejelentkezéshez a felhasználók olyan nevet adjon meg, amely egyedileg azonosítja a fiókját. Történelmileg csak az Azure AD UPN-t használhatja bejelentkezési névként.

Olyan szervezetek esetében, ahol a helyszíni UPN a felhasználó elsődleges bejelentkezési e-mail-címe, ez a megközelítés nagyszerű volt. Ezek a szervezetek az Azure AD UPN-t a helyszíni egyszerű felhasználónévvel megegyező értékre állítja be, és a felhasználóknak egységes bejelentkezési felülettel kell rendelkezniük.

Egyes szervezeteknél azonban a helyszíni UPN nem használatos bejelentkezési névként. A helyszíni környezetekben a helyi AD DS úgy konfigurálja, hogy engedélyezze a bejelentkezést egy másik bejelentkezési AZONOSÍTÓval. Ha az Azure AD UPN-t ugyanarra az értékre állítja be, mint a helyszíni UPN-t, az Azure AD-ben a felhasználók ezt az értéket kell bejelentkezniük.

A probléma tipikus megkerülő megoldás volt az Azure AD UPN beállítása arra az e-mail-címre, amellyel a felhasználó bejelentkezik. Ez a megközelítés működik, de a helyszíni AD és az Azure AD között különböző UPN-ket eredményez, és ez a konfiguráció nem kompatibilis az összes Microsoft 365 munkaterheléssel.

Egy másik módszer, hogy szinkronizálja az Azure AD-t és a helyszíni UPN-ket ugyanarra az értékre, majd konfigurálja az Azure AD-t, hogy a felhasználók ellenőrzött e-mail-címmel jelentkezzenek be az Azure AD-be. Ennek a képességnek a biztosításához meg kell adnia egy vagy több e-mail-címet a felhasználó *ProxyAddresses* attribútumában a helyszíni címtárban. A *ProxyAddresses* ezután automatikusan szinkronizálva lesznek az Azure ad-vel Azure ad Connect használatával.

## <a name="preview-limitations"></a>Előzetes verzió korlátozásai

Jelentkezzen be az Azure AD-be az e-mailben, alternatív bejelentkezési AZONOSÍTÓként pedig elérhető a ingyenes Azure AD Edition és újabb verziókban.

Az aktuális előzetes verzióban a következő korlátozások érvényesek, amikor egy felhasználó egy nem UPN-alapú e-mail-címmel jelentkezik be alternatív bejelentkezési AZONOSÍTÓként:

* A felhasználók akkor is láthatják az UPN-t, ha a nem UPN-alapú e-mail-címmel jelentkezett be. A következő példa az alábbi viselkedést tapasztalhatja:
    * A rendszer felszólítja a felhasználót, hogy jelentkezzen be az UPN-be, amikor az Azure AD-be irányítja a-ba `login_hint=<non-UPN email>` .
    * Ha a felhasználó egy nem UPN-alapú e-mail-címmel jelentkezik be, és helytelen jelszót ad meg, a "jelszó megadása" lapon az egyszerű felhasználónév megjelenítéséhez az *"adja meg a jelszót"* oldal változik.
    * Bizonyos Microsoft-webhelyek és-alkalmazások (például a és a Microsoft Office) esetében [https://portal.azure.com](https://portal.azure.com) a **Account Manager** vezérlőelem általában a jobb felső sarokban jelenhet meg a bejelentkezéshez használt nem UPN e-mail helyett.

* Egyes folyamatok jelenleg nem kompatibilisek a nem UPN-alapú e-mailekkel, például a következőkkel:
    * Az Identity Protection jelenleg nem felel meg az e-mailek másodlagos bejelentkezési azonosítóinak a *kiszivárgott hitelesítő adatok* kockázatának észlelésével. Ez a kockázati észlelés az UPN-t használja a kiszivárgott hitelesítő adatok egyeztetéséhez. További információ: [Azure ad Identity Protection kockázatkezelés és szervizelés][identity-protection].
    * A másodlagos bejelentkezési AZONOSÍTÓra küldött B2B-meghívók nem teljes mértékben támogatottak. Miután elfogadta a meghívó e-mailként való elküldését egy másik bejelentkezési AZONOSÍTÓként, a másodlagos e-mail-címmel való bejelentkezés nem működik a bérlői végponton.

## <a name="synchronize-sign-in-email-addresses-to-azure-ad"></a>Bejelentkezési e-mail-címek szinkronizálása az Azure AD-vel

A hagyományos Active Directory Domain Services (AD DS) vagy Active Directory összevonási szolgáltatások (AD FS) (AD FS) hitelesítés közvetlenül a hálózaton történik, és a AD DS-infrastruktúra kezeli. A hibrid hitelesítéssel a felhasználók Ehelyett közvetlenül az Azure AD-be is jelentkezhetnek be.

Ennek a hibrid hitelesítési módszernek a támogatásához szinkronizálja helyszíni AD DS környezetét az Azure AD-be a [Azure ad Connect][azure-ad-connect] használatával, és konfigurálja a jelszó-kivonatoló szinkronizálás (PHS) vagy a Pass-Through hitelesítés (PTA ESP) használatára.

A felhasználó mindkét konfigurációs beállításban beküldi felhasználónevét és jelszavát az Azure AD-nek, amely ellenőrzi a hitelesítő adatokat, és kibocsát egy jegyet. Amikor a felhasználók bejelentkeznek az Azure AD-be, azzal megszűnik a szervezet számára a AD FS infrastruktúra üzemeltetése és kezelése.

![Az Azure AD hibrid identitás diagramja jelszó-kivonatolási szinkronizálással](media/howto-authentication-use-email-signin/hybrid-password-hash-sync.png)

![Az Azure AD hibrid identitás diagramja átmenő hitelesítéssel](media/howto-authentication-use-email-signin/hybrid-pass-through-authentication.png)

A Azure AD Connect által automatikusan szinkronizált felhasználói attribútumok egyike a *ProxyAddresses*. Ha a felhasználók e-mail-címmel rendelkeznek a helyszíni AD DS környezetben a *ProxyAddresses* attribútum részeként, az automatikusan szinkronizálva lesz az Azure ad-vel. Ezt az e-mail-címet később közvetlenül az Azure AD bejelentkezési folyamatában használhatja alternatív bejelentkezési AZONOSÍTÓként.

> [!IMPORTANT]
> A rendszer csak a bérlő ellenőrzött tartományában lévő e-maileket szinkronizálja az Azure AD-vel. Minden egyes Azure AD-bérlő egy vagy több ellenőrzött tartománnyal rendelkezik, amelyekhez Ön bizonyította a tulajdonjogát, és a bérlőhöz egyedi módon kötve van.
>
> További információ: [Egyéni tartománynév hozzáadása és ellenőrzése az Azure ad-ben][verify-domain].

További információ: [válassza ki a megfelelő hitelesítési módszert az Azure ad Hybrid Identity megoldáshoz][hybrid-auth-methods].

## <a name="enable-user-sign-in-with-an-email-address"></a>Felhasználói bejelentkezés engedélyezése e-mail-címmel

Ha a *ProxyAddresses* attribútummal rendelkező felhasználók szinkronizálása az Azure ad-vel Azure ad Connect használatával történik, engedélyeznie kell a szolgáltatást a felhasználóknak, hogy alternatív Bejelentkezési azonosítóként jelentkezzen be a bérlő számára. Ez a funkció azt jelzi, hogy az Azure AD-beli bejelentkezési kiszolgálók nem csak az UPN-értékekkel ellenőrzik a bejelentkezési nevet, hanem az e-mail-cím *ProxyAddresses* is.

Az előzetes verzió ideje alatt a PowerShell használatával jelenleg csak az e-mail-címmel engedélyezheti a bejelentkezést másodlagos bejelentkezési AZONOSÍTÓként. A következő lépések végrehajtásához *bérlői rendszergazdai* engedélyekre van szükség:

1. Nyisson meg egy PowerShell-munkamenetet rendszergazdaként, majd telepítse a *AzureADPreview* modult az [install-Module][Install-Module] parancsmag használatával:

    ```powershell
    Install-Module AzureADPreview
    ```

    Ha a rendszer kéri, válassza az **Y** elemet a NuGet telepítéséhez vagy nem megbízható adattárból történő telepítéshez.

1. Jelentkezzen be az Azure AD-bérlőbe *bérlői rendszergazdaként* a (z) [AzureAD][Connect-AzureAD] parancsmag használatával:

    ```powershell
    Connect-AzureAD
    ```

    A parancs a fiókjával, a környezettel és a bérlői AZONOSÍTÓval kapcsolatos információkat ad vissza.

1. Ellenőrizze, hogy a *HomeRealmDiscoveryPolicy* -házirend már létezik-e a bérlőben a [Get-AzureADPolicy][Get-AzureADPolicy] parancsmag használatával a következőképpen:

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

1. Ha jelenleg nincs beállítva házirend, a parancs nem ad vissza semmit. Ha a rendszer visszaadja a szabályzatot, hagyja ki ezt a lépést, és folytassa a következő lépéssel egy meglévő szabályzat frissítéséhez.

    A *HomeRealmDiscoveryPolicy* házirend bérlőhöz való hozzáadásához használja a [New-AzureADPolicy][New-AzureADPolicy] parancsmagot, és állítsa a *AlternateIdLogin* attribútumot *"engedélyezve" értékre: true (igaz* ), ahogy az alábbi példában látható:

    ```powershell
    $AzureADPolicyDefinition = @(
      @{
         "HomeRealmDiscoveryPolicy" = @{
            "AlternateIdLogin" = @{
               "Enabled" = $true
            }
         }
      } | ConvertTo-JSON -Compress
    )
    $AzureADPolicyParameters = @{
      Definition            = $AzureADPolicyDefinition
      DisplayName           = "BasicAutoAccelerationPolicy"
      IsOrganizationDefault = $true
      Type                  = "HomeRealmDiscoveryPolicy"
    }
    New-AzureADPolicy @AzureADPolicyParameters
    ```

    A szabályzat sikeres létrehozása után a parancs visszaadja a szabályzat AZONOSÍTÓját, ahogy az a következő példában látható:

    ```powershell
    Id                                   DisplayName                 Type                     IsOrganizationDefault
    --                                   -----------                 ----                     ---------------------
    5de3afbe-4b7a-4b33-86b0-7bbe308db7f7 BasicAutoAccelerationPolicy HomeRealmDiscoveryPolicy True
    ```

1. Ha már van konfigurált házirend, ellenőrizze, hogy az *AlternateIdLogin*   attribútum engedélyezve van-e, ahogy az alábbi példában látható:

    ```powershell
    Id : 5de3afbe-4b7a-4b33-86b0-7bbe308db7f7
    OdataType :
    AlternativeIdentifier :
    Definition : {{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}}
    DisplayName : BasicAutoAccelerationPolicy
    IsOrganizationDefault : True
    KeyCredentials : {}
    Type : HomeRealmDiscoveryPolicy
    ```

    Ha a házirend létezik, de a *AlternateIdLogin* attribútum nincs jelen vagy engedélyezve, vagy ha más attribútumok vannak a megőrizni kívánt szabályzatban, frissítse a meglévő szabályzatot a [set-AzureADPolicy][Set-AzureADPolicy] parancsmaggal.

    > [!IMPORTANT]
    > Amikor frissíti a szabályzatot, ügyeljen rá, hogy a régi és az új *AlternateIdLogin* attribútumot is tartalmazza.

    A következő példa hozzáadja a *AlternateIdLogin* attribútumot, és megőrzi a *AllowCloudPasswordValidation* attribútumot, amely esetleg már be van állítva:

    ```powershell
    $AzureADPolicyDefinition = @(
      @{
         "HomeRealmDiscoveryPolicy" = @{
            "AllowCloudPasswordValidation" = $true
            "AlternateIdLogin" = @{
               "Enabled" = $true
            }
         }
      } | ConvertTo-JSON -Compress
    )
    $AzureADPolicyParameters = @{
      ID                    = "b581c39c-8fe3-4bb5-b53d-ea3de05abb4b"
      Definition            = $AzureADPolicyDefinition
      DisplayName           = "BasicAutoAccelerationPolicy"
      IsOrganizationDefault = $true
      Type                  = "HomeRealmDiscoveryPolicy"
    }
    
    Set-AzureADPolicy @AzureADPolicyParameters
    ```

    Győződjön meg arról, hogy a frissített szabályzat megjeleníti a módosításokat, valamint azt, hogy a *AlternateIdLogin* attribútum mostantól engedélyezve van:

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

A házirend alkalmazása esetén akár egy órát is igénybe vehet, hogy a felhasználók bejelentkezhetnek az alternatív bejelentkezési AZONOSÍTÓjuk használatával.

## <a name="test-user-sign-in-with-email"></a>Felhasználói bejelentkezés tesztelése e-mailben

Annak ellenőrzéséhez, hogy a felhasználók bejelentkezhetnek-e e-mail-címmel, az [https://myprofile.microsoft.com][my-profile] e-mail-címük alapján egy felhasználói fiókkal, például a (z `balas@fabrikam.com` ), nem pedig az egyszerű felhasználónévvel is bejelentkezhetnek `balas@contoso.com` . A bejelentkezési élménynek ugyanúgy kell kinéznie, mint az UPN-alapú bejelentkezési eseménnyel.

## <a name="enable-staged-rollout-to-test-user-sign-in-with-an-email-address"></a>A bevezetésének engedélyezése a felhasználói bejelentkezés e-mail-címmel való teszteléséhez  

A [kétlépcsős][staged-rollout] bevezetéssel a bérlői rendszergazdák engedélyezhetik bizonyos csoportok funkcióinak használatát. Javasoljuk, hogy a bérlői rendszergazdák szakaszos bevezetést használjanak a felhasználói bejelentkezés e-mail-címmel való teszteléséhez. Ha a rendszergazdák készen állnak a szolgáltatás központi telepítésére a teljes bérlőn, a Kezdőlap tartomány-felderítési házirendet kell használniuk.  


A következő lépések végrehajtásához *bérlői rendszergazdai* engedélyekre van szükség:

1. Nyisson meg egy PowerShell-munkamenetet rendszergazdaként, majd telepítse a *AzureADPreview* modult az [install-Module][Install-Module] parancsmag használatával:

    ```powershell
    Install-Module AzureADPreview
    ```

    Ha a rendszer kéri, válassza az **Y** elemet a NuGet telepítéséhez vagy nem megbízható adattárból történő telepítéshez.

2. Jelentkezzen be az Azure AD-bérlőbe *bérlői rendszergazdaként* a (z) [AzureAD][Connect-AzureAD] parancsmag használatával:

    ```powershell
    Connect-AzureAD
    ```

    A parancs a fiókjával, a környezettel és a bérlői AZONOSÍTÓval kapcsolatos információkat ad vissza.

3. Sorolja fel az összes létező bevezetési szabályzatot a következő parancsmag használatával:
   
   ```powershell
   Get-AzureADMSFeatureRolloutPolicy
   ``` 

4. Ha a szolgáltatáshoz nem tartozik meglévő szakaszos bevezetési szabályzat, hozzon létre egy új, szakaszos bevezetési házirendet, és jegyezze fel a házirend-azonosítót:

   ```powershell
   $AzureADMSFeatureRolloutPolicy = @{
      Feature    = "EmailAsAlternateId"
      DisplayName = "EmailAsAlternateId Rollout Policy"
      IsEnabled   = $true
   }
   New-AzureADMSFeatureRolloutPolicy @AzureADMSFeatureRolloutPolicy
   ```

5. Keresse meg a szakaszos bevezetési szabályzatba felvenni kívánt csoport directoryObject-AZONOSÍTÓját. Jegyezze fel az *azonosító* paraméternek visszaadott értéket, mert a következő lépésben fogja használni.
   
   ```powershell
   Get-AzureADMSGroup -SearchString "Name of group to be added to the staged rollout policy"
   ```

6. Vegye fel a csoportot a szakaszos bevezetési szabályzatba az alábbi példában látható módon. Cserélje le a *-ID* paraméter értékét a 4. lépésben a házirend-azonosítóhoz visszaadott értékre, és cserélje le a *-RefObjectId* paraméter értékét az 5. lépésben feljegyzett *azonosítóra* . Akár 1 órát is igénybe vehet, mielőtt a csoportba tartozó felhasználók a proxy címüket használhatják a bejelentkezéshez.

   ```powershell
   Add-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID" -RefObjectId "GROUP_OBJECT_ID"
   ```
   
A csoportba felvett új tagok esetében akár 24 óráig is eltarthat, mielőtt a proxy címeiket felhasználhatják a bejelentkezéshez.

### <a name="removing-groups"></a>Csoportok eltávolítása

Ha egy csoportot a szakaszos bevezetési szabályzatból szeretne eltávolítani, futtassa a következő parancsot:

```powershell
Remove-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID" -ObjectId "GROUP_OBJECT_ID" 
```

### <a name="removing-policies"></a>Szabályzatok eltávolítása

A szakaszos bevezetési szabályzat eltávolításához először tiltsa le a szabályzatot, majd távolítsa el a rendszerből:

```powershell
Set-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID" -IsEnabled $false 
Remove-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID"
```

## <a name="troubleshoot"></a>Hibaelhárítás

Ha a felhasználók az e-mail-címükkel ütköznek a bejelentkezési eseményekkel, tekintse át a következő hibaelhárítási lépéseket:

1. Ellenőrizze, hogy a felhasználói fiók rendelkezik-e e-mail-címmel, amely a *ProxyAddresses* attribútumhoz van beállítva a helyszíni AD DS környezetben.
1. Ellenőrizze, hogy a Azure AD Connect konfigurálva van-e, és sikeresen szinkronizálja-e a felhasználói fiókokat a helyszíni AD DS-környezetből az Azure AD-be.
1. Győződjön meg arról, hogy az Azure AD- *HomeRealmDiscoveryPolicy* szabályzata *"enabled"* értékre van állítva a *AlternateIdLogin* attribútum: True:

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a hibrid identitásról, például a Azure AD alkalmazás proxyról vagy Azure AD Domain Servicesról, tekintse [meg az Azure ad hibrid identitást a helyszíni számítási feladatok eléréséhez és kezeléséhez][hybrid-overview].

További információ a hibrid identitási műveletekről: [jelszó-kivonatolási szinkronizálás][phs-overview] vagy [átmenő hitelesítés][pta-overview] szinkronizálása.

<!-- INTERNAL LINKS -->
[verify-domain]: ../fundamentals/add-custom-domain.md
[hybrid-auth-methods]: ../hybrid/choose-ad-authn.md
[azure-ad-connect]: ../hybrid/whatis-azure-ad-connect.md
[hybrid-overview]: ../hybrid/cloud-governed-management-for-on-premises.md
[phs-overview]: ../hybrid/how-to-connect-password-hash-synchronization.md
[pta-overview]: ../hybrid/how-to-connect-pta-how-it-works.md
[identity-protection]: ../identity-protection/overview-identity-protection.md#risk-detection-and-remediation

<!-- EXTERNAL LINKS -->
[Install-Module]: /powershell/module/powershellget/install-module
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
[Get-AzureADPolicy]: /powershell/module/azuread/get-azureadpolicy
[New-AzureADPolicy]: /powershell/module/azuread/new-azureadpolicy
[Set-AzureADPolicy]: /powershell/module/azuread/set-azureadpolicy
[staged-rollout]: /powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged-rollout
[my-profile]: https://myprofile.microsoft.com
