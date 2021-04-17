---
title: Vendégfelhasználó érvényesítési állapotának alaphelyzetbe állítása – Azure AD
description: Megtudhatja, hogyan állíthatja alaphelyzetbe a meghívó érvényesítési állapotát Azure Active Directory B2B vendégfelhasználók számára a Azure AD External Identities.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/06/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5bfce7ef2621cbe3bbbfdd95bf9a75e427c8cbd
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531872"
---
# <a name="reset-redemption-status-for-a-guest-user-preview"></a>Vendégfelhasználó érvényesítési állapotának alaphelyzetbe állítása (előzetes verzió)

Miután egy vendégfelhasználó beváltotta a B2B-együttműködésre szóló meghívót, előfordulhat, hogy frissítenie kell a bejelentkezési adatait, például:

- A felhasználó egy másik e-mail-címmel és identitásszolgáltatóval szeretne bejelentkezni
- A felhasználó saját bérlőben létrehozott fiókja törölve lett, és újra létre lett hozva
- A felhasználó egy másik vállalatba helyezték át, de továbbra is ugyanolyan hozzáférésre van szüksége az erőforrásokhoz
- A felhasználó feladatkörei egy másik felhasználónak is át vannak va.

A korábbi forgatókönyvek kezeléséhez manuálisan törölnie kellett a vendégfelhasználó fiókját a címtárból, és újra kellett indítania a felhasználót. Mostantól a PowerShell vagy a Microsoft Graph invitation API használatával visszaállíthatja a felhasználó érvényesítési állapotát, és újra meghívhatja a felhasználót, miközben megtarthatja a felhasználó objektumazonosítóját, csoporttagságát és alkalmazás-hozzárendelését. Amikor a felhasználó beváltja az új meghívót, a felhasználó UPN-jét nem módosítja, de a felhasználó bejelentkezési neve az új e-mailre változik. A felhasználó ezután bejelentkezhet az új e-mail-címmel vagy a felhasználói objektum tulajdonságához hozzáadott `otherMails` e-mail-címmel.

## <a name="reset-the-email-address-used-for-sign-in"></a>A bejelentkezéshez használt e-mail-cím alaphelyzetbe állítása

Ha egy felhasználó egy másik e-mail-címmel szeretne bejelentkezni:

1. Győződjön meg arról, hogy az új e-mail-cím hozzá van adva a felhasználói objektum `mail` vagy `otherMails` tulajdonságához. 
2.  Cserélje le a tulajdonságban található `InvitedUserEmailAddress` e-mail-címet az új e-mail-címre.
3. Az alábbi módszerek egyikével visszaállíthatja a felhasználó érvényesítési állapotát.

> [!NOTE]
>A nyilvános előzetes verzióban két javaslatunk van:
>- Amikor új címre visszaállítja a felhasználó e-mail-címét, javasoljuk a tulajdonság `mail` beállítását. Így a felhasználó beválthatja a meghívót, ha bejelentkezik a címtárba, és a meghívóban az érvényesítési hivatkozást is használja.
>- Amikor visszaállítja egy B2B-vendégfelhasználó állapotát, ezt a felhasználói környezetben tegye meg. A csak alkalmazáshívások jelenleg nem támogatottak.
>
## <a name="use-powershell-to-reset-redemption-status"></a>Érvényesítési állapot alaphelyzetbe állítása a PowerShell használatával

Telepítse a legújabb AzureADPreview PowerShell-modult, és hozzon létre egy új meghívót az új e-mail-cím beállítással, és állítsa a `InvitedUserEmailAddress` `ResetRedemption` következőre: `true` .

```powershell  
Uninstall-Module AzureADPreview 
Install-Module AzureADPreview 
Connect-AzureAD 
$ADGraphUser = Get-AzureADUser -objectID "UPN of User to Reset"  
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId 
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser -ResetRedemption $True 
```

## <a name="use-microsoft-graph-api-to-reset-redemption-status"></a>Érvényesítési állapot Microsoft Graph a Microsoft Graph API használatával

A Microsoft Graph [API használatával](/graph/api/resources/invitation?view=graph-rest-beta&preserve-view=true)állítsa a tulajdonságot a következőre: , és adja meg az új `resetRedemption` `true` e-mail-címet a `invitedUserEmailAddress` tulajdonságban.

```json
POST https://graph.microsoft.com/beta/invitations  
Authorization: Bearer eyJ0eX...  
ContentType: application/json  
{  
   "invitedUserEmailAddress": "<<external email>>",  
   "sendInvitationMessage": true,  
   "invitedUserMessageInfo": {  
      "messageLanguage": "en-US",  
      "ccRecipients": [  
         {  
            "emailAddress": {  
               "name": null,  
               "address": "<<optional additional notification email>>"  
            }  
         } 
      ],  
      "customizedMessageBody": "<<custom message>>"  
},  
"inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",  
"invitedUser": {  
   "id": "<<ID for the user you want to reset>>"  
}, 
"resetRedemption": true 
}
```

## <a name="next-steps"></a>Következő lépések

- [B2B Azure Active Directory felhasználók hozzáadása a PowerShell használatával](customize-invitation-api.md#powershell)
- [Az Azure AD B2B vendégfelhasználó tulajdonságai](user-properties.md)
