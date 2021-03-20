---
title: B2B együttműködési API és testreszabás – Azure Active Directory
description: Azure Active Directory B2B együttműködés támogatja a vállalatok közötti kapcsolatokat azáltal, hogy lehetővé teszi az üzleti partnerek számára a vállalati alkalmazások szelektív elérését.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8160859bb782ee8ffc4fef5ee03b61b6f54be1bb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99548661"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Azure Active Directory B2B együttműködési API és testreszabás

Sok ügyfelünk azt közölte nekünk, hogy a Meghívási folyamatot testre szeretné szabni a szervezeteknek legmegfelelőbb módon. Az API-val csak ezt teheti meg. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](/graph/api/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>A meghívó API képességei

Az API a következő képességeket kínálja:

1. Külső felhasználó meghívása *bármely* e-mail-címmel.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Szabja testre a felhasználókat, hogy a meghívót elfogadják.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Válassza ki, hogy elküldje a szabványos meghívó e-mailt nekünk

    ```
    "sendInvitationMessage": true
    ```

   egy olyan üzenettel, amely testre szabható a címzett számára

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. És válassza ki a CC: olyan személyeket, akiket szeretne megtartani a hurokban, hogy meghívja ezt a közreműködőt.

5. Vagy teljes mértékben testreszabhatja a meghívót és a bevezetési munkafolyamatot úgy, hogy nem küld értesítést az Azure AD-n keresztül.

    ```
    "sendInvitationMessage": false
    ```

   Ebben az esetben olyan beváltási URL-címet kap vissza az API-ból, amelyet beágyazhat egy e-mail-sablonba, IM-re vagy más, tetszés szerinti terjesztési módszerbe.

6. Végül, ha Ön rendszergazda, meghívhatja a felhasználót tagként.

    ```
    "invitedUserType": "Member"
    ```

## <a name="determine-if-a-user-was-already-invited-to-your-directory"></a>Annak megállapítása, hogy a felhasználó már megkapta-e a címtárat

A meghívó API-val meghatározhatja, hogy egy felhasználó már létezik-e az erőforrás-bérlőben. Ez akkor lehet hasznos, ha olyan alkalmazást fejleszt, amely a meghívó API-t használja egy felhasználó meghívásához. Ha a felhasználó már létezik az erőforrás-címtárban, akkor nem kap meghívót, így a lekérdezés futtatásával megállapíthatja, hogy a már e-mail-cím UPN-ként vagy más bejelentkezési tulajdonságként van-e megnyitva.

1. Győződjön meg arról, hogy a felhasználó e-mail-tartománya nem része az erőforrás-bérlő ellenőrzött tartományának.
2. Az erőforrás-bérlőben használja a következő felhasználói lekérdezés beolvasása, ahol {0} a a meghívó e-mail cím:

   ```
   “userPrincipalName eq '{0}' or mail eq '{0}' or proxyAddresses/any(x:x eq 'SMTP:{0}') or signInNames/any(x:x eq '{0}') or otherMails/any(x:x eq '{0}')"
   ```

## <a name="authorization-model"></a>Engedélyezési modell

Az API-t a következő engedélyezési módokon lehet futtatni:

### <a name="app--user-mode"></a>Alkalmazás + felhasználói mód

Ebben a módban az API-t használó személyeknek az engedélyekkel kell rendelkezniük a B2B-meghívások létrehozásához.

### <a name="app-only-mode"></a>Csak alkalmazás mód

Csak az alkalmazás kontextusában az alkalmazásnak szüksége van a felhasználó. INVITE. All hatókörre a meghívás sikerességéhez.

További információkért lásd: https://developer.microsoft.com/graph/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell

A PowerShell használatával egyszerűen hozzáadhat és meghívhat külső felhasználókat a szervezet számára. Meghívás létrehozása a parancsmag használatával:

```powershell
New-AzureADMSInvitation
```

A következő lehetőségek közül választhat:

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

### <a name="invitation-status"></a>Meghívás állapota

Miután meghívót küldött egy külső felhasználótól, a **Get-AzureADUser** parancsmag segítségével megtekintheti, hogy elfogadták-e. A rendszer a következő tulajdonságokat tölti fel Get-AzureADUser, ha egy külső felhasználó meghívót küld:

* A **UserState** jelzi, hogy a meghívás **PendingAcceptance** vagy **elfogadva** van-e.
* A **UserStateChangedOn** a **UserState** tulajdonság legutóbbi módosításának időbélyegét jeleníti meg.

A **Filter (szűrés** ) lehetőséggel **UserState** alapján szűrheti az eredményeket. Az alábbi példa azt mutatja be, hogyan szűrheti az eredményeket, hogy csak a függőben lévő meghívóval rendelkező felhasználók jelenjenek meg. A példa a **Format-List** kapcsolót is megjeleníti, amely lehetővé teszi a megjelenítendő tulajdonságok megadását. 
 

```powershell
Get-AzureADUser -Filter "UserState eq 'PendingAcceptance'" | Format-List -Property DisplayName,UserPrincipalName,UserState,UserStateChangedOn
```

> [!NOTE]
> Győződjön meg arról, hogy a AzureAD PowerShell-modul vagy a AzureADPreview PowerShell-modul legújabb verziója van telepítve. 

## <a name="see-also"></a>Lásd még

Tekintse meg a Meghívási API-referenciát a alkalmazásban [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](/graph/api/resources/invitation) .

## <a name="next-steps"></a>Következő lépések

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [A B2B együttműködés Meghívási e-mail elemei](invitation-email-elements.md)
- [VÁLLALATKÖZI együttműködés meghívásának beváltása](redemption-experience.md)
- [B2B Collaboration-felhasználók hozzáadása meghívás nélkül](add-user-without-invite.md)