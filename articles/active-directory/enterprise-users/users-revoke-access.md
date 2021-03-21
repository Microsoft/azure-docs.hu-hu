---
title: A felhasználói hozzáférés visszavonása vészhelyzetben Azure Active Directoryban | Microsoft Docs
description: Felhasználók összes hozzáférésének visszavonása Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
author: curtand
ms.author: curtand
manager: daveba
ms.reviewer: krbain
ms.date: 12/02/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 826ca9fc20d8bbcf9a5f90ccc895b9f9867a6be1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96860575"
---
# <a name="revoke-user-access-in-azure-active-directory"></a>Felhasználói hozzáférés visszavonása Azure Active Directory

Az olyan forgatókönyvek között, amelyekhez a rendszergazdának a felhasználók minden hozzáférését vissza kell vonnia, beleértve a feltört fiókokat, az alkalmazottak megszűnését és más bennfentes fenyegetéseket. A környezet összetettsége függvényében a rendszergazdák több lépést is igénybe vehetnek a hozzáférés visszavonása érdekében. Bizonyos esetekben előfordulhat, hogy a hozzáférés visszavonásának kezdeményezése és a hozzáférés hatékony visszavonása között egy időszak van.

A kockázatok enyhítéséhez ismernie kell a tokenek működését. Sokféle token létezik, amelyek az alábbi részekben felsorolt minták egyikéhez tartoznak.

## <a name="access-tokens-and-refresh-tokens"></a>Hozzáférési tokenek és frissítési tokenek

A hozzáférési jogkivonatok és a frissítési tokenek gyakran használatosak vastag ügyfélalkalmazások esetében, és olyan böngészőalapú alkalmazásokban is használhatók, mint például az egylapos alkalmazások.

- Amikor a felhasználók hitelesítik magukat az Azure AD-ben, a rendszer kiértékeli az engedélyezési házirendeket annak megállapítására, hogy a felhasználó kaphat-e hozzáférést egy adott erőforráshoz.  

- Ha engedélyezve van, az Azure AD egy hozzáférési jogkivonatot és egy frissítési jogkivonatot bocsát ki az erőforráshoz.  

- Az Azure AD által kiállított hozzáférési tokenek alapértelmezés szerint 1 óráig tartanak. Ha a hitelesítési protokoll lehetővé teszi, az alkalmazás csendesen újrahitelesítheti a felhasználót úgy, hogy a hozzáférési jogkivonat lejárta után átadja a frissítési jogkivonatot az Azure AD-nek.

Az Azure AD ezután újraértékeli az engedélyezési házirendeket. Ha a felhasználó továbbra is engedélyezve van, az Azure AD új hozzáférési jogkivonatot és frissítési jogkivonatot bocsát ki.

A hozzáférési tokenek biztonsági szempontból fontosak lehetnek, ha a hozzáférést a jogkivonat élettartama alatt rövidebb idő alatt kell visszavonni, ami általában egy óra körüli. Emiatt a Microsoft aktívan dolgozik a [folyamatos hozzáférés kiértékelésének](../conditional-access/concept-continuous-access-evaluation.md) biztosításán Microsoft 365 alkalmazások számára, ami segít biztosítani a hozzáférési jogkivonatok érvénytelenítését a közel valós időben.  

## <a name="session-tokens-cookies"></a>Munkamenet-tokenek (cookie-k)

A legtöbb böngésző alapú alkalmazás munkamenet-jogkivonatokat használ a hozzáférési és frissítési tokenek helyett.  

- Amikor egy felhasználó megnyit egy böngészőt, és az Azure AD-n keresztül hitelesít egy alkalmazást, a felhasználó két munkamenet-tokent kap. Egyet az Azure AD-ből, és egy másikat az alkalmazásból.  

- Ha egy alkalmazás kiadja a saját munkamenet-jogkivonatát, az alkalmazáshoz való hozzáférést az alkalmazás munkamenete szabályozza. Ezen a ponton csak azok az engedélyezési házirendek érintik a felhasználót, akikkel az alkalmazás tisztában van.

- Az Azure AD engedélyezési házirendjeit a rendszer olyan gyakran újraértékeli, ahogy az alkalmazás visszaküldi a felhasználót az Azure AD-nek. Az újraértékelés általában csendesen történik, bár a gyakoriság az alkalmazás konfigurációjától függ. Előfordulhat, hogy az alkalmazás soha nem küld vissza felhasználót az Azure AD-nek, amíg a munkamenet-jogkivonat érvényes.

- Ahhoz, hogy egy munkamenet-jogkivonat visszavonható legyen, az alkalmazásnak vissza kell vonnia a hozzáférést a saját engedélyezési házirendjei alapján. Az Azure AD nem tudja közvetlenül visszavonni az alkalmazás által kiadott munkamenet-jogkivonatot.  

## <a name="revoke-access-for-a-user-in-the-hybrid-environment"></a>Felhasználó hozzáférésének visszavonása a hibrid környezetben

A helyszíni Active Directoryokkal Azure Active Directory szinkronizált hibrid környezetek esetében a Microsoft azt javasolja, hogy a rendszergazdák a következő műveleteket hajtsák végre.  

### <a name="on-premises-active-directory-environment"></a>Helyszíni Active Directory környezet

Rendszergazdaként a Active Directoryban kapcsolódjon a helyszíni hálózathoz, nyissa meg a PowerShellt, és végezze el a következő műveleteket:

1. Tiltsa le a felhasználót a Active Directoryban. Tekintse meg a [disable-ADAccount](/powershell/module/addsadministration/disable-adaccount).

    ```PowerShell
    Disable-ADAccount -Identity johndoe  
    ```

1. A felhasználó jelszavának visszaállítása kétszer a Active Directory. Tekintse [meg a set-ADAccountPassword](/powershell/module/addsadministration/set-adaccountpassword).

    > [!NOTE]
    > A felhasználó jelszavának kétszeres módosításának oka az, hogy enyhítse a pass-The-hash kockázatát, különösen akkor, ha késés van a helyszíni jelszó-replikálásban. Ha nyugodtan feltételezi, hogy ez a fiók nem sérült, akkor csak egyszer állíthatja alaphelyzetbe a jelszót.

    > [!IMPORTANT] 
    > Ne használja a példákban szereplő jelszavakat a következő parancsmagokban. Ügyeljen arra, hogy a jelszavakat egy véletlenszerű karakterláncra módosítsa.

    ```PowerShell
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd1" -Force)
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd2" -Force)
    ```

### <a name="azure-active-directory-environment"></a>Azure Active Directory környezet

Azure Active Directory rendszergazdaként nyissa meg a PowerShellt, futtassa ``Connect-AzureAD`` a parancsot, és hajtsa végre a következő műveleteket:

1. Tiltsa le a felhasználót az Azure AD-ben. Tekintse [meg a set-AzureADUser](/powershell/module/azuread/Set-AzureADUser).

    ```PowerShell
    Set-AzureADUser -ObjectId johndoe@contoso.com -AccountEnabled $false
    ```
1. A felhasználó Azure AD frissítési jogkivonatának visszavonása. Lásd: [Visszavonás – AzureADUserAllRefreshToken](/powershell/module/azuread/revoke-azureaduserallrefreshtoken).

    ```PowerShell
    Revoke-AzureADUserAllRefreshToken -ObjectId johndoe@contoso.com
    ```

1. Tiltsa le a felhasználó eszközeit. Lásd: [Get-AzureADUserRegisteredDevice](/powershell/module/azuread/get-azureaduserregistereddevice).

    ```PowerShell
    Get-AzureADUserRegisteredDevice -ObjectId johndoe@contoso.com | Set-AzureADDevice -AccountEnabled $false
    ```

## <a name="optional-steps"></a>Nem kötelező lépések

- [Vállalati adatok törlése az Intune által felügyelt alkalmazásokból](/mem/intune/apps/apps-selective-wipe).

- [A vállalati tulajdonban lévő eszközök törlésével alaphelyzetbe állíthatja az eszköz gyári beállításait](/mem/intune/remote-actions/devices-wipe).

> [!NOTE]
> Az eszközön lévő adatok törlés után nem állíthatók vissza.

## <a name="when-access-is-revoked"></a>A hozzáférés visszavonásakor

Miután a rendszergazdák elvégezték a fenti lépéseket, a felhasználó nem szerezhet új jogkivonatokat minden olyan alkalmazáshoz, amely Azure Active Directoryhoz van kötve. A visszavonás és a felhasználó hozzáférésének elvesztése közötti eltelt idő attól függ, hogy az alkalmazás hogyan adja meg a hozzáférést:

- **Hozzáférési jogkivonatokat használó alkalmazások** esetén a felhasználó elveszíti a hozzáférést, amikor lejár a hozzáférési jogkivonat.

- A **munkamenet-jogkivonatokat használó alkalmazások** esetében a meglévő munkamenetek a jogkivonat lejárta után azonnal véget tartanak. Ha a felhasználó letiltott állapota szinkronizálva van az alkalmazással, akkor az alkalmazás automatikusan visszavonhatja a felhasználó meglévő munkameneteit, ha erre van konfigurálva.  A szükséges idő az alkalmazás és az Azure AD közötti szinkronizálás gyakoriságának függvénye.

## <a name="next-steps"></a>Következő lépések

- [Biztonságos hozzáférési eljárások az Azure AD-rendszergazdák számára](../roles/security-planning.md)
- [Felhasználói profil adatainak hozzáadása vagy frissítése](../fundamentals/active-directory-users-profile-azure-portal.md)
