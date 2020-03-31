---
title: 'Rövid útmutató: Vendégfelhasználó hozzáadása a PowerShell használatával – Azure AD'
description: Ebben a rövid útmutatóban megismerheti, hogyan küldhet az Azure AD B2B együttműködés külső felhasználó számára meghívót a PowerShell használatával.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0faaf0394bddc2e443afc194bbd0ecef72625f9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240925"
---
# <a name="quickstart-add-a-guest-user-with-powershell"></a>Rövid útmutató: Vendégfelhasználó hozzáadása PowerShell-lel

Az Azure Active Directory B2B együttműködés sokféle lehetőséget nyújt a külső partnerek alkalmazásokba és szolgáltatásokba történő meghívására. Az előző rövid útmutatóban láthattuk, hogyan lehet vendégfelhasználókat közvetlenül az Azure Active Directory admin portálon felvenni. Felhasználók felvételéhez a PowerShell is használható, akár egyenként, akár kötegben. Ebben a rövid útmutatóban a New-AzureADMSInvitation paranccsal adunk új vendég felhasználót az Azure bérlőhöz.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené. 

## <a name="prerequisites"></a>Előfeltételek

### <a name="install-the-latest-azureadpreview-module"></a>A legújabb AzureADPreview modul telepítése
Győződjön meg róla, hogy az Azure AD PowerShell for Graph module (AzureADPreview) legújabb verzióját telepíti. 

Először ellenőrizze, melyik modulokat telepítette. Nyissa meg a Windows PowerShellt rendszergazdaként (Futtatás rendszergazdaként), majd futtassa a következő parancsot:
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

Ha az AzureADPreview modul anélkül megjelenik anélkül, hogy újabb verzióra figyelmeztető üzenet jelenne meg, készen van. Ellenkező esetben a kimenet alapján, tegye a következők egyikét:

- Ha nem kapott eredményt, a következő parancs futtatásával telepítse az AzureADPreview modult:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Ha az eredmények között csak az Azure AD-modul jelenik meg, a következő parancsok futtatásával telepítse az AzureADPreview-modult: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Ha az eredmények között csak az AzureADPreview-modul jelenik meg, de kap egy üzenetet, amely jelzi, hogy van újabb verziója, futtassa a következő parancsokat a modul frissítésére: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
  ```

Előfordulhat, hogy kap egy kérdést, hogy a modult nem megbízható tárházból telepíti. Ez akkor fordul elő, ha korábban még nem állította be megbízható tárháznak a PSGallery tárházat. Nyomja meg **Y**-t a modul telepítéséhez.

### <a name="get-a-test-email-account"></a>Teszt e-mail-fiók létrehozása

Szüksége lesz egy próba e-mail fiókra, ahova a meghívót küldheti. A fióknak a szervezeten kívülinek kell lennie. Bármilyen típusú fiókot használhat, közösségi fiókot is például gmail.com vagy Outlook.com-os e-mail címet.

## <a name="sign-in-to-your-tenant"></a>Bejelentkezés bérlőhöz

Futtassa a következő parancsot a bérlőtartományhoz való csatlakozáshoz:

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```
Például: `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`.

Amikor a rendszer erre kéri, adja meg a hitelesítő adatait.

## <a name="send-an-invitation"></a>Meghívó küldése

1. Meghívó küldéséhez a teszt e-mail fiók, fuss a következő PowerShell parancs (cserélje le **a "Sanda"** és **a sanda\@fabrikam.com** a teszt e-mail fiók nevét és e-mail címét): 

   ```powershell
   New-AzureADMSInvitation -InvitedUserDisplayName "Sanda" -InvitedUserEmailAddress sanda@fabrikam.com -InviteRedirectURL https://myapps.microsoft.com -SendInvitationMessage $true
   ```
2. A parancs elküldi a meghívót a megadott e-mail-címre. Ellenőrizze a kimenetet, aminek a következőképpen kell kinéznie:

   ![Felhasználói elfogadásra várakozást mutató PowerShell kimenet](media/quickstart-invite-powershell/powershell-azureadmsinvitation-result.png)

## <a name="verify-the-user-exists-in-the-directory"></a>A felhasználó létezésének ellenőrzése a címtárban

1. Annak ellenőrzésére, hogy a meghívott felhasználót hozzáadták-e az Azure AD-hoz, futtassa a következő parancsot:
 
   ```powershell
   Get-AzureADUser -Filter "UserType eq 'Guest'"
   ```
3. Ellenőrizze a kimenetet, hogy a meghívott felhasználó szerepel-e a listán, egy egyszerű felhasználónévvel (UPN) az *e-mail cím*#EXT#\@*tartomány*formátumában. Például *sanda_fabrikam.com#EXT#\@contoso.onmicrosoft.com*, ahol contoso.onmicrosoft.com az a szervezet, amelytől a meghívókat küldte.

   ![A vendégfelhasználó felvételét mutató PowerShell kimenet](media/quickstart-invite-powershell/powershell-guest-user-added.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölheti a tesztfelhasználói fiókot a címtárban. A felhasználói fiók törléséhez a következő parancsot futtassa:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```
Például:`Remove-AzureADUser -ObjectId "sanda_fabrikam.com#EXT#@contoso.onmicrosoft.com"`


## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban egyetlen vendégfelhasználót hívtunk meg és adtunk hozzá a címtárhoz a PowerShell használatával. Ezután megnézzük, hogyan lehet a PowerShell segítségével vendégfelhasználókat tömegesen meghívni.

> [!div class="nextstepaction"]
> [Oktatóanyag: Azure AD B2B együttműködő felhasználók tömeges meghívása](tutorial-bulk-invite.md)
