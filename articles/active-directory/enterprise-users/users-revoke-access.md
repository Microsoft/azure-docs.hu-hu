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
ms.date: 03/29/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 578e8f5f3126542c579cd573c82b732049d407b6
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105959822"
---
# <a name="revoke-user-access-in-azure-active-directory"></a>Felhasználói hozzáférés visszavonása Azure Active Directory

Olyan forgatókönyvek, amelyek megkövetelik, hogy a rendszergazda visszavonja a felhasználók összes hozzáférését, beleértve a feltört fiókokat, az alkalmazottak megszűnését és más bennfentes fenyegetéseket. A környezet összetettsége függvényében a rendszergazdák több lépést is igénybe vehetnek a hozzáférés visszavonása érdekében. Bizonyos esetekben előfordulhat, hogy a hozzáférés visszavonásának megkezdése és a hozzáférés hatékony visszavonása között időszak van.

A kockázatok enyhítéséhez ismernie kell a tokenek működését. Sokféle token létezik, amelyek az alábbi részekben felsorolt minták egyikéhez tartoznak.

## <a name="access-tokens-and-refresh-tokens"></a>Hozzáférési tokenek és frissítési tokenek

A hozzáférési jogkivonatok és a frissítési tokenek gyakran használatosak vastag ügyfélalkalmazások esetében, és olyan böngészőalapú alkalmazásokban is használhatók, mint például az egylapos alkalmazások.

- Amikor a felhasználók hitelesítik magukat az Azure AD-ben, a rendszer kiértékeli az engedélyezési házirendeket annak megállapítására, hogy a felhasználó kaphat-e hozzáférést egy adott erőforráshoz.  

- Ha engedélyezve van, az Azure AD egy hozzáférési jogkivonatot és egy frissítési jogkivonatot bocsát ki az erőforráshoz.  

- Az Azure AD által kiállított hozzáférési tokenek alapértelmezés szerint 1 óráig tartanak. Ha a hitelesítési protokoll lehetővé teszi, az alkalmazás csendesen újrahitelesítheti a felhasználót úgy, hogy a hozzáférési jogkivonat lejárta után átadja a frissítési jogkivonatot az Azure AD-nek.

Az Azure AD ezután újraértékeli az engedélyezési házirendeket. Ha a felhasználó továbbra is engedélyezve van, az Azure AD új hozzáférési jogkivonatot bocsát ki, és frissíti a tokent.

A hozzáférési tokenek biztonsági szempontból fontosak lehetnek, ha a hozzáférést a jogkivonat élettartama alatt rövidebb idő alatt kell visszavonni, ami általában egy óra körüli. Ezért a Microsoft aktívan dolgozik az Office 365-alkalmazások [folyamatos hozzáférésének kiértékelésében](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation) , így biztosítva, hogy a hozzáférési tokenek közel valós időben legyenek érvényben.  

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

1. Tiltsa le a felhasználót a Active Directoryban. Tekintse meg a [disable-ADAccount](https://docs.microsoft.com/powershell/module/addsadministration/disable-adaccount?view=win10-ps).

    ```PowerShell
    Disable-ADAccount -Identity johndoe  
    ```

2. A felhasználó jelszavának visszaállítása kétszer a Active Directory. Tekintse [meg a set-ADAccountPassword](https://docs.microsoft.com/powershell/module/addsadministration/set-adaccountpassword?view=win10-ps).

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

1. Tiltsa le a felhasználót az Azure AD-ben. Tekintse [meg a set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/Set-AzureADUser?view=azureadps-2.0).

    ```PowerShell
    Set-AzureADUser -ObjectId johndoe@contoso.com -AccountEnabled $false
    ```

2. A felhasználó Azure AD frissítési jogkivonatának visszavonása. Lásd: [Visszavonás – AzureADUserAllRefreshToken](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0).

    ```PowerShell
    Revoke-AzureADUserAllRefreshToken -ObjectId johndoe@contoso.com
    ```

3. Tiltsa le a felhasználó eszközeit. Lásd: [Get-AzureADUserRegisteredDevice](https://docs.microsoft.com/powershell/module/azuread/get-azureaduserregistereddevice?view=azureadps-2.0).

    ```PowerShell
    Get-AzureADUserRegisteredDevice -ObjectId johndoe@contoso.com | Set-AzureADDevice -AccountEnabled $false
    ```
## <a name="when-access-is-revoked"></a>A hozzáférés visszavonásakor

Miután a rendszergazdák elvégezték a fenti lépéseket, a felhasználó nem szerezhet új jogkivonatokat minden olyan alkalmazáshoz, amely Azure Active Directoryhoz van kötve. A visszavonás és a felhasználó hozzáférésének elvesztése közötti eltelt idő attól függ, hogy az alkalmazás hogyan adja meg a hozzáférést:

- **Hozzáférési jogkivonatokat használó alkalmazások** esetén a felhasználó elveszíti a hozzáférést, amikor lejár a hozzáférési jogkivonat.

- A **munkamenet-jogkivonatokat használó alkalmazások** esetében a meglévő munkamenetek a jogkivonat lejárta után azonnal véget tartanak. Ha a felhasználó letiltott állapota szinkronizálva van az alkalmazással, akkor az alkalmazás automatikusan visszavonhatja a felhasználó meglévő munkameneteit, ha erre van konfigurálva.  A szükséges idő az alkalmazás és az Azure AD közötti szinkronizálás gyakoriságának függvénye.

## <a name="best-practices"></a>Ajánlott eljárások

- Automatizált kiépítési és megszüntetési megoldás üzembe helyezése. A felhasználók az alkalmazásokból való megszüntetésének hatékony módja a hozzáférés visszavonása, különösen a munkamenet-jogkivonatokat használó alkalmazások esetében. Dolgozzon ki egy olyan folyamatot, amely nem támogatja a felhasználók automatikus üzembe helyezését és megszüntetését. Győződjön meg arról, hogy az alkalmazások visszavonják a saját munkamenet-jogkivonatait, és ne fogadják el az Azure AD hozzáférési jogkivonatok fogadását még akkor is, ha

  - Az [Azure ad SaaS-alkalmazás üzembe](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)helyezése. Az Azure AD SaaS-alkalmazások üzembe helyezése általában 20-40 percenként automatikusan lefut. Az [Azure ad-kiépítés beállítása](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) a letiltott felhasználók megszüntetésére vagy inaktiválására az alkalmazásokban.
  
  - Az Azure AD SaaS app kiépítését nem használó alkalmazások esetében használja az [Identity Managert](https://docs.microsoft.com/microsoft-identity-manager/mim-how-provision-users-adds) vagy egy harmadik féltől származó megoldást a felhasználók megszüntetésének automatizálására.  
  - Azonosíthatja és fejlesztheti a manuális megszüntetést igénylő alkalmazások folyamatát. Győződjön meg arról, hogy a rendszergazdák gyorsan futtathatják a szükséges manuális feladatokat, hogy szükség esetén kiépítsék a felhasználót ezekből az alkalmazásokból.
  
- [Az eszközök és alkalmazások kezelése Microsoft Intuneokkal](https://docs.microsoft.com/mem/intune/remote-actions/device-management). Az Intune által felügyelt eszközök visszaállíthatók [a gyári beállításokra](https://docs.microsoft.com/mem/intune/remote-actions/devices-wipe). Ha az eszköz nem felügyelt, [a vállalati adatok a felügyelt alkalmazásokból](https://docs.microsoft.com/mem/intune/apps/apps-selective-wipe)törölhetők. Ezek a folyamatok hatékonyak a végfelhasználói eszközök potenciálisan bizalmas adatainak eltávolításához. Ahhoz azonban, hogy bármelyik folyamat aktiválva legyen, az eszköznek csatlakoznia kell az internethez. Ha az eszköz offline állapotban van, akkor az eszköz továbbra is hozzáférhet a helyileg tárolt adatmennyiségekhez.

> [!NOTE]
> Az eszközön lévő adatok törlés után nem állíthatók vissza.

- [A Microsoft Cloud app Security (MCAS) használatával blokkolhatja az adatletöltést,](https://docs.microsoft.com/cloud-app-security/use-case-proxy-block-session-aad) ha szükséges. Ha az információ csak online érhető el, a szervezetek figyelheti a munkameneteket, és valós idejű házirend-kényszerítést érhet el.

- [A folyamatos hozzáférés kiértékelésének (CAE) engedélyezése az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation). A CAE lehetővé teszi, hogy a rendszergazdák visszavonják a munkamenet-jogkivonatokat és hozzáférési jogkivonatokat a CAE-kompatibilis alkalmazásokhoz.  

## <a name="next-steps"></a>Következő lépések

- [Biztonságos hozzáférési eljárások az Azure AD-rendszergazdák számára](https://docs.microsoft.com/azure/active-directory/roles/security-planning)
- [Felhasználói profil adatainak hozzáadása vagy frissítése](../fundamentals/active-directory-users-profile-azure-portal.md)
