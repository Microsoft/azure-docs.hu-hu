---
title: Vendég felhasználó visszaváltási állapotának alaphelyzetbe állítása – Azure AD
description: Megtudhatja, hogyan állíthatja alaphelyzetbe a meghívások beváltási állapotát egy Azure Active Directory B2B vendég felhasználói számára az Azure AD külső Identitásokban.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc51a8af85b85db28265d1fca420a5b9b368b078
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576915"
---
# <a name="reset-redemption-status-for-a-guest-user"></a>Vendég felhasználó beváltási állapotának visszaállítása

Miután a vendég felhasználó beváltotta a B2B-együttműködésre vonatkozó meghívót, előfordulhat, hogy a bejelentkezési adatai frissítésére van szükség, például a következő esetekben:

- A felhasználó egy másik e-mail-cím és egy identitás-szolgáltató használatával szeretne bejelentkezni
- A rendszer törölte és újra létrehozta a felhasználó fiókját a saját bérlőben
- A felhasználó át lett helyezve egy másik vállalatba, de továbbra is ugyanazokat az erőforrásokat kell elérnie
- A felhasználó feladatai egy másik felhasználóhoz lettek továbbítva

A fenti forgatókönyvek kezeléséhez manuálisan törölnie kell a vendég felhasználói fiókját a címtárból, és újra kell hívnia a felhasználót. Most már használhatja a PowerShellt vagy a Microsoft Graph meghívó API-t a felhasználó visszaváltási állapotának alaphelyzetbe állításához és újbóli meghívásához a felhasználó objektumazonosító, csoporttagság és alkalmazás-hozzárendelései megőrzése mellett. Amikor a felhasználó beváltja az új meghívót, az új e-mail-cím lesz a felhasználó egyszerű felhasználóneve. A felhasználó később bejelentkezhet az új e-mailben vagy egy, a `otherMails` felhasználói objektum tulajdonságához hozzáadott e-mailben.

## <a name="use-powershell-to-reset-redemption-status"></a>A beváltási állapot visszaállítása a PowerShell használatával

Telepítse a legújabb AzureADPreview PowerShell-modult, és hozzon létre egy új meghívást `InvitedUserEMailAddress` az új e-mail-címmel, és állítsa a következőre: `ResetRedemption` `true` .

```powershell  
Uninstall-Module AzureADPreview 
Install-Module AzureADPreview 
Connect-AzureAD 
$ADGraphUser = Get-AzureADUser -objectID "UPN of User to Reset"  
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId 
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser -ResetRedemption $True 
```

## <a name="use-microsoft-graph-api-to-reset-redemption-status"></a>A beváltási állapot alaphelyzetbe állítása Microsoft Graph API használatával

A [Microsoft Graph meghívó API](/graph/api/resources/invitation?view=graph-rest-1.0)használatával állítsa be a `resetRedemption` tulajdonságot, `true` és adja meg az új e-mail-címet a `invitedUserEmailAddress` tulajdonságban.

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

- [Azure Active Directory B2B csoportmunka-felhasználók hozzáadása a PowerShell használatával](customize-invitation-api.md#powershell)
- [Az Azure AD B2B vendég felhasználóinak tulajdonságai](user-properties.md)
