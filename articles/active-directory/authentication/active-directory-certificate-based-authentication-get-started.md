---
title: Tanúsítványalapú hitelesítés – Azure Active Directory
description: Megtudhatja, hogyan konfigurálhatja a tanúsítvány alapú hitelesítést a környezetben
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 3ba84bb3ee38981217e72f8372a836b03647083d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861340"
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>A tanúsítványalapú hitelesítés első lépései az Azure Active Directoryban

A tanúsítványalapú hitelesítés lehetővé teszi, hogy egy Windows-, Android-vagy iOS-eszközön lévő ügyféltanúsítványt Azure Active Directory hitelesítse, amikor az Exchange online-fiókot a következőhöz csatlakoztatja:

- Microsoft Mobile-alkalmazások, például a Microsoft Outlook és a Microsoft Word
- Exchange ActiveSync-(EAS-) ügyfelek

A szolgáltatás konfigurálásával nem kell megadnia a Felhasználónév és a jelszó kombinációját bizonyos levelezési és Microsoft Office alkalmazásokban a mobileszközön.

Ez a témakör:

- A következő lépésekkel konfigurálhatja és használhatja a tanúsítvány alapú hitelesítést a bérlők felhasználói számára az Office 365 Enterprise, az üzleti, az oktatási és az Egyesült Államok kormányzati csomagjaiban. Ez a funkció előzetes verzióban érhető el az Office 365 Kínában, az USA kormányzati védelmi és az USA kormányzati szövetségi terveiben.
- Feltételezi, hogy már rendelkezik egy [nyilvános kulcsokra épülő infrastruktúrával (PKI)](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831740(v=ws.11)) és [AD FS](../hybrid/how-to-connect-fed-whatis.md) konfigurálva.

## <a name="requirements"></a>Követelmények

A tanúsítványalapú hitelesítés konfigurálásához a következő utasításoknak igaznak kell lenniük:

- A tanúsítványalapú hitelesítés (CBA) csak a böngészőalapú alkalmazások, a modern hitelesítést (ADAL) használó natív ügyfelek és a MSAL-kódtárak összevont környezetei esetében támogatott. Az egyetlen kivétel Exchange Active Sync (EAS) az Exchange Online-hoz (EXO), amely összevont és felügyelt fiókokhoz is használható.
- A legfelső szintű hitelesítésszolgáltató és a köztes hitelesítésszolgáltatók konfigurálását Azure Active Directory kell beállítani.
- Minden hitelesítésszolgáltatónál szerepelnie kell egy olyan visszavont tanúsítványok listájának (CRL), amely egy internetre irányuló URL-cím használatával hivatkozható.
- Rendelkeznie kell legalább egy, Azure Active Directory konfigurált hitelesítésszolgáltatóval. A kapcsolódó lépések a [hitelesítésszolgáltatók konfigurálása](#step-2-configure-the-certificate-authorities) szakaszban találhatók.
- Az Exchange ActiveSync-ügyfelek esetében az ügyféltanúsítványt a felhasználónak az Exchange Online-ban lévő, a tulajdonos alternatív neve mező egyszerű neve vagy RFC822 neve mezőjében kell megadnia. Azure Active Directory leképezi a RFC822 értékét a címtárban található proxy címe attribútumra.
- Az eszköznek hozzáféréssel kell rendelkeznie legalább egy olyan hitelesítésszolgáltatóhoz, amely ügyféltanúsítványt bocsát ki.
- Az ügyfél-hitelesítéshez meg kell adni az ügyfél tanúsítványát.

>[!IMPORTANT]
>A sikeres letöltéshez és gyorsítótárhoz Azure Active Directory visszavont tanúsítványok listájának maximális mérete 20MB, a CRL letöltéséhez szükséges idő pedig nem haladhatja meg a 10 másodpercet.  Ha Azure Active Directory nem tudja letölteni a CRL-t, a tanúsítvány alapú hitelesítés sikertelen lesz, ha a megfelelő HITELESÍTÉSSZOLGÁLTATÓ által kiadott tanúsítványokat használ. Ajánlott eljárások a CRL-fájlok méretének korlátozására a tanúsítványok élettartamának ésszerű korlátokon belüli megőrzése, valamint a lejárt tanúsítványok törlése érdekében.

## <a name="step-1-select-your-device-platform"></a>1. lépés: válassza ki az eszköz platformját

Első lépésként a megfelelő eszköz platformját kell áttekintenie:

- Az Office Mobile-alkalmazások támogatása
- A konkrét megvalósítási követelmények

A kapcsolódó információk a következő eszköz platformokon érhetők el:

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)

## <a name="step-2-configure-the-certificate-authorities"></a>2. lépés: a hitelesítésszolgáltatók konfigurálása

A hitelesítésszolgáltatók Azure Active Directoryban való konfigurálásához minden hitelesítésszolgáltatónál töltse fel a következőket:

* A tanúsítvány nyilvános része *. cer* formátumban
* Az internetre irányuló URL-címek, ahol a visszavont tanúsítványok listái (CRL-ek) találhatók

A hitelesítésszolgáltató sémája a következőképpen néz ki:

```csharp
    class TrustedCAsForPasswordlessAuth
    {
       CertificateAuthorityInformation[] certificateAuthorities;
    }

    class CertificateAuthorityInformation

    {
        CertAuthorityType authorityType;
        X509Certificate trustedCertificate;
        string crlDistributionPoint;
        string deltaCrlDistributionPoint;
        string trustedIssuer;
        string trustedIssuerSKI;
    }

    enum CertAuthorityType
    {
        RootAuthority = 0,
        IntermediateAuthority = 1
    }
```

A konfigurációhoz használhatja a [Azure Active Directory PowerShell 2-es verzióját](/powershell/azure/active-directory/install-adv2):

1. Indítsa el a Windows PowerShellt rendszergazdai jogosultságokkal.
2. Telepítse az Azure AD-modul [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) vagy újabb verzióját.

```powershell
    Install-Module -Name AzureAD –RequiredVersion 2.0.0.33
```

Első konfigurálási lépésként létre kell hoznia egy kapcsolatot a Bérlővel. Amint létezik a bérlőhöz való kapcsolódás, áttekintheti, hozzáadhatja, törölheti és módosíthatja a címtárban definiált megbízható hitelesítésszolgáltatók listáját.

### <a name="connect"></a>Csatlakozás

Ha kapcsolatot szeretne létesíteni a Bérlővel, használja a [AzureAD](/powershell/module/azuread/connect-azuread) parancsmagot:

```azurepowershell
    Connect-AzureAD
```

### <a name="retrieve"></a>Beolvasni

A címtárban definiált megbízható hitelesítésszolgáltatók beolvasásához használja a [Get-AzureADTrustedCertificateAuthority](/powershell/module/azuread/get-azureadtrustedcertificateauthority) parancsmagot.

```azurepowershell
    Get-AzureADTrustedCertificateAuthority
```

### <a name="add"></a>Hozzáadás

Megbízható hitelesítésszolgáltató létrehozásához használja a [New-AzureADTrustedCertificateAuthority](/powershell/module/azuread/new-azureadtrustedcertificateauthority) parancsmagot, és állítsa be a **vlelérésihelye** attribútumot helyes értékre:

```azurepowershell
    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint="<CRL Distribution URL>"
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca
```

### <a name="remove"></a>Eltávolítás

Megbízható hitelesítésszolgáltató eltávolításához használja a [Remove-AzureADTrustedCertificateAuthority](/powershell/module/azuread/remove-azureadtrustedcertificateauthority) parancsmagot:

```azurepowershell
    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]
```

### <a name="modify"></a>Módosítás

Egy megbízható hitelesítésszolgáltató módosításához használja a [set-AzureADTrustedCertificateAuthority](/powershell/module/azuread/set-azureadtrustedcertificateauthority) parancsmagot:

```azurepowershell
    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]
```

## <a name="step-3-configure-revocation"></a>3. lépés: visszavonás konfigurálása

Ügyféltanúsítvány visszavonásához Azure Active Directory beolvassa a visszavont tanúsítványok listáját (CRL) a hitelesítésszolgáltató adatainak részeként feltöltött URL-címekről, és gyorsítótárazza azt. A visszavont tanúsítványok listájának utolsó közzétételi időbélyegzője (**hatálybalépés dátuma** tulajdonsága) annak biztosítására szolgál, hogy a CRL továbbra is érvényes. A CRL rendszeres időközönként hivatkozik a lista részét képező tanúsítványokhoz való hozzáférés visszavonására.

Ha további azonnali visszavonásra van szükség (például ha egy felhasználó elveszíti az eszközt), akkor a felhasználó engedélyezési jogkivonata érvényteleníthető. Az engedélyezési jogkivonat érvénytelenítéséhez állítsa az adott felhasználóhoz tartozó **StsRefreshTokenValidFrom** mezőt a Windows PowerShell használatával. Minden olyan felhasználó esetében frissítenie kell a **StsRefreshTokenValidFrom** mezőt, amelynek hozzáférését vissza kívánja vonni.

Annak érdekében, hogy a visszavonás megmaradjon, be kell állítania a CRL **érvényességi dátumát** a **StsRefreshTokenValidFrom** által beállított érték után, és biztosítania kell, hogy a kérdéses tanúsítvány a CRL-ben legyen.

Az alábbi lépések az engedélyezési jogkivonat frissítésének és érvénytelenítésének folyamatát ismertetik a **StsRefreshTokenValidFrom** mező beállításával.

**Visszavonás beállítása:**

1. Kapcsolódás rendszergazdai hitelesítő adatokkal a MSOL szolgáltatáshoz:

```powershell
        $msolcred = get-credential
        connect-msolservice -credential $msolcred
```

2. Egy felhasználó aktuális StsRefreshTokensValidFrom-értékének beolvasása:

```powershell
        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom
```

3. Új StsRefreshTokensValidFrom-érték konfigurálása a felhasználó számára az aktuális időbélyeggel egyenlő:

```powershell
        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")
```

A beállított dátumnak a jövőben kell lennie. Ha a dátum nem a jövőben, a **StsRefreshTokensValidFrom** tulajdonság nincs beállítva. Ha a dátum a jövőben, a **StsRefreshTokensValidFrom** az aktuális időpontra van állítva (nem a Set-MsolUser parancs által jelzett dátumra).

## <a name="step-4-test-your-configuration"></a>4. lépés: a konfiguráció tesztelése

### <a name="testing-your-certificate"></a>A tanúsítvány tesztelése

Első konfigurációs tesztként próbáljon meg bejelentkezni az [Outlook Web Accessbe](https://outlook.office365.com) vagy a [SharePoint Online](https://microsoft.sharepoint.com) -ba az **eszközön futó böngésző** használatával.

Ha a bejelentkezés sikeres, akkor tudja, hogy:

- A felhasználói tanúsítvány kiépítve a tesztelési eszközre
- A AD FS megfelelően van konfigurálva

### <a name="testing-office-mobile-applications"></a>Office Mobile-alkalmazások tesztelése

**A tanúsítványalapú hitelesítés tesztelése a Mobile Office-alkalmazásban:**

1. A tesztelési eszközön telepítsen egy Office Mobile-alkalmazást (például OneDrive).
3. Indítsa el az alkalmazást.
4. Adja meg a felhasználónevét, majd válassza ki a használni kívánt felhasználói tanúsítványt.

Sikeresen be kell jelentkeznie.

### <a name="testing-exchange-activesync-client-applications"></a>Exchange ActiveSync-ügyfélalkalmazások tesztelése

Az Exchange ActiveSync (EAS) tanúsítvány alapú hitelesítésen keresztüli eléréséhez az ügyféltanúsítványt tartalmazó EAS-profilnak elérhetőnek kell lennie az alkalmazás számára.

Az EAS-profilnak tartalmaznia kell a következő információkat:

- A hitelesítéshez használandó felhasználói tanúsítvány

- Az EAS-végpont (például outlook.office365.com)

Az EAS-profilok a mobileszköz-felügyelet (MDM), például az Intune használatával konfigurálhatók és helyezhetők el az eszközön, vagy a tanúsítvány manuális elhelyezése az eszköz EAS-profiljában.

### <a name="testing-eas-client-applications-on-android"></a>EAS-ügyfélalkalmazások tesztelése Androidon

**A tanúsítvány hitelesítésének tesztelése:**

1. Konfiguráljon egy EAS-profilt az alkalmazásban, amely megfelel az előző szakaszban foglalt követelményeknek.
2. Nyissa meg az alkalmazást, és ellenőrizze, hogy a levelek szinkronizálása folyamatban van-e.

## <a name="next-steps"></a>Következő lépések

[További információ a tanúsítványalapú hitelesítésről Android-eszközökön.](active-directory-certificate-based-authentication-android.md)

[További információ a tanúsítványalapú hitelesítésről iOS-eszközökön.](active-directory-certificate-based-authentication-ios.md)
