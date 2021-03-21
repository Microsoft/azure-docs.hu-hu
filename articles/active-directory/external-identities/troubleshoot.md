---
title: B2B-együttműködés hibaelhárítása – Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B-együttműködés gyakori problémáinak elhárítása
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: troubleshooting
ms.date: 02/12/2021
tags: active-directory
ms.author: mimart
author: msmimart
ms.reviewer: mal
ms.custom:
- it-pro
- seo-update-azuread-jan"
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60cd944ecb144a30e872259f6e959a11c3ea6319
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100365429"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Azure Active Directory B2B-együttműködés hibaelhárítása

Íme néhány jogorvoslat az Azure Active Directory (Azure AD) B2B-együttműködés gyakori problémáira.

   > [!IMPORTANT]
   > - **2021. január 4-én kezdődően** a Google [elavult webnézet-bejelentkezési támogatást jelenít meg](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Ha Google-összevonást vagy önkiszolgáló regisztrációt használ a Gmail szolgáltatással, az üzletági [natív alkalmazásokat tesztelje a kompatibilitás](google-federation.md#deprecation-of-webview-sign-in-support)érdekében.
   > - **Október 2021-től kezdve** a Microsoft nem fogja támogatni a meghívások beváltását azáltal, hogy nem felügyelt Azure ad-fiókokat és bérlőket hoz létre vállalatközi együttműködési forgatókönyvek létrehozásához. A felkészülés során javasoljuk, hogy az ügyfelek [e-mail-egyszeri jelszavas hitelesítést](one-time-passcode.md)kérjenek. Szívesen fogadjuk visszajelzését ezen a nyilvános előzetes verzióban, és örömmel vesszük, hogy még több módszert is létre lehetne hozni az együttműködésre.

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Hozzáadtam egy külső felhasználót, de nem látják őket a globális címjegyzékben vagy a People Picker

Azokban az esetekben, amikor a külső felhasználók nincsenek kitöltve a listában, az objektum eltarthat néhány percig.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>A B2B vendég felhasználó nem jelenik meg a SharePoint Online-ban/OneDrive People Picker

A meglévő vendég felhasználók keresésének lehetősége a SharePoint Online-ban (Spongya) az emberek választója alapértelmezés szerint ki van kapcsolva a régi viselkedésnek megfelelően.

Ezt a funkciót a bérlői és a webhelycsoport szintjén a "ShowPeoplePickerSuggestionsForGuestUsers" beállítás használatával engedélyezheti. A szolgáltatást a Set-SPOTenant és Set-SPOSite parancsmagok segítségével állíthatja be, amelyek lehetővé teszik a tagok számára a címtárban lévő összes vendég felhasználó keresését. A bérlői hatókör változásai nincsenek hatással a már kiépített Spongya-webhelyekre.

## <a name="invitations-have-been-disabled-for-directory"></a>A meghívások le lettek tiltva a címtárban

Ha értesítést kap arról, hogy nincs engedélye a felhasználók meghívására, ellenőrizze, hogy a felhasználói fiókja jogosult-e külső felhasználók meghívására Azure Active Directory > felhasználói beállítások > külső felhasználók számára a külső együttműködési beállítások kezelése >.

![A külső felhasználók beállításait ábrázoló képernyőfelvétel](media/troubleshoot/external-user-settings.png)

Ha nemrég módosította ezeket a beállításokat, vagy hozzárendelte a vendég meghívó szerepkört egy felhasználóhoz, előfordulhat, hogy a módosítások érvénybe léptetéséhez 15-60 perces késleltetés szükséges.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>A meghívott felhasználó hibaüzenetet kap a beváltáskor

Gyakori hibák a következők:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>A meghívó rendszergazdája nem engedélyezte, hogy a EmailVerified-felhasználók a bérlőn jöjjenek létre

Ha olyan felhasználókat hívja meg, akik szervezete Azure Active Directory használ, de az adott felhasználó fiókja nem létezik (például a felhasználó nem létezik az Azure AD-contoso.com). Előfordulhat, hogy a contoso.com rendszergazdája olyan házirendet tartalmaz, amely megakadályozza a felhasználók létrehozását. A felhasználónak ellenőriznie kell a rendszergazdát annak megállapításához, hogy a külső felhasználók engedélyezettek-e. Előfordulhat, hogy a külső felhasználó rendszergazdájának engedélyeznie kell az e-mailek ellenőrzött felhasználóit a tartományban (lásd ezt a [cikket](/powershell/module/msonline/set-msolcompanysettings) az e-mailek ellenőrzött felhasználóinak engedélyezéséről).

![Hiba történt a bérlő nem engedélyezi az e-mailek ellenőrzött felhasználóinak használatát](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>A külső felhasználó már nem létezik egy összevont tartományban

Ha összevonási hitelesítést használ, és a felhasználó még nem létezik a Azure Active Directoryban, a felhasználó nem hívható meg.

A probléma megoldásához a külső felhasználó rendszergazdájának szinkronizálnia kell a felhasználó fiókját a Azure Active Directory.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Hogyan működik a " \# ", amely nem általában érvényes karakter, szinkronizáljon az Azure ad-vel?

a (z) " \# " az Azure ad B2B Collaboration vagy a külső felhasználók számára az UPN-ben foglalt karakter, mert a meghívott fiók user@contoso.com user_contoso. com # ext # lesz @fabrikam.onmicrosoft.com . Ezért a helyszíni UPN-ből \# érkező egyszerű felhasználónevek nem jelentkezhetnek be a Azure Portalba. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Hibaüzenet jelenik meg, amikor külső felhasználókat veszek fel egy szinkronizált csoportba

A külső felhasználók csak a "hozzárendelt" vagy "biztonsági" csoportokba vehetők fel, és nem a helyszínen elsajátított csoportokba.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>A külső felhasználó nem kapott e-mailt a beváltáshoz

A meghívás az INTERNETSZOLGÁLTATÓval vagy a levélszemét szűrővel ellenőrizze, hogy a következő címek engedélyezettek-e: Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Megfigyelhető, hogy az egyéni üzenet nem jelenik meg a Meghívási üzenetekben időnként

Az adatvédelmi törvények betartása érdekében az API-k nem tartalmaznak egyéni üzeneteket az e-mail-meghívóban a következő esetekben:

- A meghívó nem rendelkezik e-mail-címmel a meghívó bérlőben
- Ha egy appservice-résztvevő elküldi a meghívót

Ha ez a forgatókönyv fontos Önnek, letilthatja az API-meghívói e-mail-címét, és elküldheti azt az Ön által választott e-mail mechanizmuson keresztül. Forduljon a szervezet jogi tanácsadásához, és győződjön meg arról, hogy az Ön által küldött e-mailek is megfelelnek az adatvédelmi törvényeknek.

## <a name="you-receive-an-aadsts65005-error-when-you-try-to-log-in-to-an-azure-resource"></a>"AADSTS65005" hibaüzenet jelenik meg, amikor megpróbál bejelentkezni egy Azure-erőforrásba

A vendég fiókkal rendelkező felhasználók nem jelentkezhetnek be, és a következő hibaüzenetet kapják:

```plaintext
    AADSTS65005: Using application 'AppName' is currently not supported for your organization contoso.com because it is in an unmanaged state. An administrator needs to claim ownership of the company by DNS validation of contoso.com before the application AppName can be provisioned.
```

A felhasználó rendelkezik Azure-beli felhasználói fiókkal, és olyan vírusos bérlő, aki el lett hagyva vagy nem felügyelt. Továbbá nincsenek globális rendszergazdák a bérlőben.

A probléma megoldásához át kell vennie az elhagyott bérlőt. Tekintse át a nem  [felügyelt címtár átvétele rendszergazdaként Azure Active Directory-ben](../enterprise-users/domains-admin-takeover.md)című témakört. Az internetre irányuló DNS-t a szóban forgó tartományi utótaghoz is el kell érni ahhoz, hogy közvetlen bizonyítékot szolgáltasson a névtér irányításához. Ha a bérlőt visszaküldi a felügyelt állapotba, beszéljen az ügyféllel, hogy a felhasználók és az ellenőrzött tartománynév a legjobb megoldás-e a szervezet számára.

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>Egy igény szerinti vagy "vírusos" Bérlővel rendelkező vendég felhasználó nem tudja alaphelyzetbe állítani a jelszavát

Ha az identitás bérlője egy igény szerinti (JIT) vagy vírusos bérlő (azaz egy különálló, nem felügyelt Azure-bérlő), csak a vendég felhasználó állíthatja alaphelyzetbe a jelszavát. Néha a szervezet [átveszi az olyan vírusos bérlők felügyeletét](../enterprise-users/domains-admin-takeover.md) , amelyek akkor jönnek létre, amikor az alkalmazottak a munkahelyi e-mail-címeiket használják a szolgáltatások regisztrálására. Miután a szervezet átvesz egy vírusos bérlőt, csak az adott szervezet rendszergazdája állíthatja alaphelyzetbe a felhasználó jelszavát, vagy engedélyezheti a SSPR. Ha szükséges, a meghívó szervezetnél távolítsa el a vendég felhasználói fiókot a címtárból, és küldje el újra a meghívót.

## <a name="a-guest-user-is-unable-to-use-the-azuread-powershell-v1-module"></a>A vendég felhasználó nem tudja használni a AzureAD PowerShell v1-modult

November 18-án 2019-én a címtárban található vendég felhasználók a AzureAD PowerShell v1 modullal le vannak tiltva a **userType** tulajdonságot futtató felhasználói fiókok.  A jövőben a felhasználónak **egy tagnak (** ahol a **userType** egyenlőnek kell lennie) vagy a AzureAD PowerShell V2 modullal kell rendelkeznie.

## <a name="in-an-azure-us-government-tenant-i-cant-invite-a-b2b-collaboration-guest-user"></a>Egy Azure-beli Egyesült államokbeli kormányzati bérlő nem hívhat meg VÁLLALATKÖZI együttműködési vendég felhasználót

Az Amerikai Egyesült Államok kormányzati felhője keretében a B2B-együttműködés jelenleg csak az USA-beli kormányzati felhőben és a B2B-együttműködés támogatását támogató bérlők között támogatott. Ha olyan bérlőn hívja meg a felhasználót, amely nem része az Azure US government-felhőnek, vagy amely még nem támogatja a B2B-együttműködést, hibaüzenetet kap. A részleteket és a korlátozásokat lásd: [prémium szintű Azure Active Directory P1 és P2 változatok](../../azure-government/compare-azure-government-global-azure.md#azure-active-directory-premium-p1-and-p2).

## <a name="i-receive-the-error-that-azure-ad-cannot-find-the-aad-extensions-app-in-my-tenant"></a>Azt a hibaüzenetet kapom, hogy az Azure AD nem találja a HRE-Extensions-app a saját bérlőben

Az önkiszolgáló bejelentkezési funkciók, például az egyéni felhasználói attribútumok vagy a felhasználói folyamatok használatakor a rendszer automatikusan létrehoz egy nevű alkalmazást `aad-extensions-app. Do not modify. Used by AAD for storing user data.` . Az Azure AD külső identitások használják a regisztrálásra és az egyéni attribútumok begyűjtésére szolgáló felhasználók adatainak tárolására.

Ha véletlenül törölte a `aad-extensions-app` alkalmazást, 30 napig helyreállíthatja. Az alkalmazást az Azure AD PowerShell-modul használatával állíthatja vissza.

1. Indítsa el az Azure AD PowerShell-modult, és futtassa a parancsot `Connect-AzureAD` .
1. Jelentkezzen be globális rendszergazdaként azon Azure AD-bérlőhöz, amely számára vissza kívánja állítani a törölt alkalmazást.
1. Futtassa a PowerShell-parancsot `Get-AzureADDeletedApplication` .
1. Keresse meg az alkalmazást abban a listában, ahol a megjelenítendő név kezdődik, `aad-extensions-app` és másolja a `ObjectId` tulajdonság értékét.
1. Futtassa a PowerShell-parancsot `Restore-AzureADDeletedApplication -ObjectId {id}` . Cserélje le a `{id}` parancs részét az `ObjectId` előző lépésből származóra.

Ekkor látnia kell a visszaállított alkalmazást a Azure Portalban.

## <a name="next-steps"></a>Következő lépések

[Támogatás kérése B2B-együttműködéshez](../fundamentals/active-directory-troubleshooting-support-howto.md)
