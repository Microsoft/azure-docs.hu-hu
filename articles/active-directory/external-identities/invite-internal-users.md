---
title: Belső felhasználók meghívása a B2B-együttműködésbe – Azure AD
description: Ha rendelkezik belső felhasználói fiókkal a partnerek, a terjesztők, a szállítók, a szállítók és az egyéb vendégek számára, az Azure AD B2B együttműködésre való meghívásával meghívja őket, hogy jelentkezzenek be a saját külső hitelesítő adataikkal vagy bejelentkezéssel. Használja a PowerShellt vagy a Microsoft Graph meghívó API-t.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 802307a21873d15242c2e387ec0defe35f50bb20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99576430"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>Belső felhasználók meghívása B2B együttműködésre

Az Azure AD B2B-együttműködés rendelkezésre állása előtt a szervezetek a belső hitelesítő adatok beállításával együttműködhetnek a forgalmazókkal, a szállítókkal, a szállítókkal és a többi vendég felhasználóval. Ha a felhasználókhoz hasonló belső vendégekkel rendelkezik, meghívhatja őket a B2B-együttműködés használatára. Ezek a B2B vendég felhasználók saját identitásokat és hitelesítő adatokat használhatnak a bejelentkezéshez, és nem kell megtartania a jelszavakat, és nem kell kezelnie a fiókok életciklusát.

Egy meglévő belső fiókra irányuló Meghívás küldése lehetővé teszi a felhasználó objektumazonosító, UPN, csoporttagság és alkalmazás-hozzárendelések megőrzését. Nem kell manuálisan törölnie és újból meghívnia a felhasználót, vagy újra hozzá kell rendelnie az erőforrásokat. A felhasználó meghívásához a meghívó API-val a belső felhasználói objektumot és a vendég felhasználó e-mail-címét is át kell adni a meghívóval együtt. Ha a felhasználó elfogadja a meghívót, a VÁLLALATKÖZI szolgáltatás megváltoztatja a meglévő belső felhasználói objektumot egy B2B-felhasználóhoz. A jövőben a felhasználónak a B2B hitelesítő adataival kell bejelentkeznie a felhőalapú erőforrások szolgáltatásaiba.

## <a name="things-to-consider"></a>Megfontolandó dolgok

- **Hozzáférés a helyszíni erőforrásokhoz**: miután a felhasználó meghívást kapott a B2B-együttműködésre, továbbra is használhatják belső hitelesítő adataikat a helyszíni erőforrások eléréséhez. Ezt a belső fiók jelszavának alaphelyzetbe állításával vagy módosításával is megakadályozhatja. A kivétel a [levelezés egyszeri jelszavas hitelesítése](one-time-passcode.md); Ha a felhasználó hitelesítési módszere egyszeri jelszóra változik, többé nem fogja tudni használni a belső hitelesítő adatokat.

- **Számlázás**: Ez a funkció nem változtatja meg a felhasználó UserType, így a felhasználó számlázási modellje nem vált automatikusan [külső identitásokra a havi aktív felhasználó (Mau) díjszabása](external-identities-pricing.md)alapján. A felhasználóra vonatkozó MAU-díjszabás aktiválásához módosítsa a felhasználó UserType `guest` . Azt is vegye figyelembe, hogy az Azure AD-bérlőt [egy Azure-előfizetéshez kell kapcsolni](external-identities-pricing.md#link-your-azure-ad-tenant-to-a-subscription) a Mau-számlázás aktiválásához.

- A **meghívás egyirányú**: meghívja a belső felhasználókat a B2B együttműködés használatára, de a hozzáadásuk után nem távolíthatja el a B2B hitelesítő adatokat. Ha a felhasználót csak belső felhasználónak szeretné módosítani, törölnie kell a felhasználói objektumot, és létre kell hoznia egy újat.

- **Csapatok**: Ha a felhasználó külső hitelesítő adataival fér hozzá a csapatokhoz, a bérlők kezdetben nem lesznek elérhetők a csapatok bérlői választóban. A felhasználó a bérlői kontextust tartalmazó URL-cím használatával férhet hozzá a csapatokhoz, például: `https://team.microsoft.com/?tenantId=<TenantId>` . Ezt követően a bérlő elérhetővé válik a csapat bérlői választóban.

- Helyszíni **szinkronizált felhasználók**: a helyszíni és a felhő között szinkronizált felhasználói fiókok esetében a helyszíni címtár a szolgáltató forrása marad, miután meghívást kapnak a B2B együttműködés használatára. A helyszíni fiók módosításai szinkronizálva lesznek a Felhőbeli fiókkal, beleértve a fiók letiltását vagy törlését is. Ezért nem akadályozhatja meg, hogy a felhasználó bejelentkezzen a helyszíni fiókjába, miközben a Felhőbeli fiókját egyszerűen törli a helyszíni fiókból. Ehelyett beállíthatja a helyszíni fiók jelszavát véletlenszerű GUID azonosítóra vagy más ismeretlen értékre.

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>Belső felhasználók meghívása B2B-együttműködésre

A PowerShell vagy a meghívó API használatával VÁLLALATKÖZI meghívást küldhet a belső felhasználónak. Győződjön meg arról, hogy a meghívóhoz használni kívánt e-mail-cím a belső felhasználói objektum külső e-mail-címe.

- A meghívóhoz tartozó user. mail tulajdonságban az e-mail-címet kell használnia.
- A felhasználó mail tulajdonságában szereplő tartománynak meg kell egyeznie a bejelentkezéshez használt fiókkal. Ellenkező esetben előfordulhat, hogy egyes szolgáltatások, például a csapatok nem fogják tudni hitelesíteni a felhasználót.

Alapértelmezés szerint a meghívás elküldi a felhasználónak egy e-mailt, amely arról tájékoztatja őket, hogy meghívást kaptak, de ezt az e-mailt elhagyhatja, és elküldheti saját helyette.

> [!NOTE]
> Saját e-mail-cím vagy más kommunikáció elküldéséhez `New-AzureADMSInvitation` a with paranccsal nyugodtan `-SendInvitationMessage:$false` meghívhatja a felhasználókat, majd elküldheti saját e-mail-üzenetét az átalakított felhasználónak. Lásd: [Az Azure ad B2B együttműködési API és a Testreszabás](customize-invitation-api.md).

## <a name="use-powershell-to-send-a-b2b-invitation"></a>B2B-Meghívás küldése a PowerShell használatával

Az Azure AD PowerShell-modul 2.0.2.130 vagy újabb verziójára lesz szüksége. A következő parancs használatával frissítsen a legújabb AzureAD PowerShell-modulra, és hívja meg a belső felhasználót a VÁLLALATKÖZI együttműködésre:

```powershell
Uninstall-Module AzureAD
Install-Module AzureAD
$ADGraphUser = Get-AzureADUser -objectID "UPN of Internal User"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>B2B-Meghívás küldése a meghívót használó API-val

Az alábbi minta azt szemlélteti, hogyan hívhatja meg a meghívó API-t egy belső felhasználó B2B-felhasználóként való meghívásához.

```json
POST https://graph.microsoft.com/v1.0/invitations
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
        "id": "<<ID for the user you want to convert>>"
    }
}
```

Az API-ra adott válasz ugyanazt a választ kapja, amikor új vendég felhasználót hív meg a címtárban.
## <a name="next-steps"></a>Következő lépések

- [VÁLLALATKÖZI együttműködés meghívásának beváltása](redemption-experience.md)
