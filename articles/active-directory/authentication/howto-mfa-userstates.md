---
title: Felhasználónkénti Multi-Factor Authentication engedélyezése – Azure Active Directory
description: Megtudhatja, hogyan engedélyezheti a felhasználónkénti Azure AD-Multi-Factor Authentication a felhasználói állapot módosításával
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 08/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 749829f641119273813d3c8ca826daf8b4dc4d11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96742663"
---
# <a name="enable-per-user-azure-ad-multi-factor-authentication-to-secure-sign-in-events"></a>Felhasználónkénti Azure AD-Multi-Factor Authentication engedélyezése a bejelentkezési események biztonságossá tételéhez

Az Azure AD-ben a felhasználói bejelentkezési események biztonságossá tételéhez többtényezős hitelesítést (MFA) is igényelhet. Az Azure AD-Multi-Factor Authentication feltételes hozzáférési házirendek használatával történő engedélyezése a felhasználók elleni védelem ajánlott módszere. A feltételes hozzáférés egy prémium szintű Azure AD P1 vagy P2 funkció, amely lehetővé teszi, hogy a szabályok bizonyos helyzetekben szükségesek legyenek az MFA megkövetelésére. A feltételes hozzáférés használatának megkezdéséhez tekintse meg az [oktatóanyag: felhasználói bejelentkezési események biztonságossá tétele az Azure AD multi-Factor Authentication](tutorial-enable-azure-mfa.md)használatával című témakört.

A feltételes hozzáférés nélküli Azure AD-bérlők esetében az [alapértelmezett biztonsági beállítások használatával biztosítható a felhasználók védelme](../fundamentals/concept-fundamentals-security-defaults.md). A felhasználók szükség szerint kérik az MFA-t, de a viselkedés szabályozása érdekében nem határozhatja meg saját szabályait.

Szükség esetén Ehelyett engedélyezheti a felhasználónkénti Azure AD-Multi-Factor Authentication minden egyes fiókját. Ha a felhasználók külön engedélyezve vannak, a többtényezős hitelesítést a bejelentkezéskor minden alkalommal végrehajtják (bizonyos kivételekkel, például amikor bejelentkeznek a megbízható IP-címekről, vagy ha a _megbízható eszközök megjegyzése_ funkció be van kapcsolva).

A felhasználói állapotok módosítása nem ajánlott, kivéve, ha az Azure AD-licencei nem tartalmazzák a feltételes hozzáférést, és nem szeretné használni a biztonsági alapértékeket. További információ az MFA engedélyezésének különböző módjairól: [szolgáltatások és licencek az Azure AD multi-Factor Authenticationhoz](concept-mfa-licensing.md).

> [!IMPORTANT]
>
> Ez a cikk részletesen ismerteti a felhasználónkénti Azure AD-Multi-Factor Authentication állapotának megtekintését és módosítását. Ha a feltételes hozzáférést vagy a biztonsági beállításokat használja, a következő lépésekkel nem tekintheti át és nem engedélyezheti a felhasználói fiókokat.
>
> Az Azure AD Multi-Factor Authentication feltételes hozzáférési házirenddel való engedélyezése nem változtatja meg a felhasználó állapotát. Nem szabad riasztást adni, ha a felhasználók le vannak tiltva. A feltételes hozzáférés nem változtatja meg az állapotot.
>
> **Ha feltételes hozzáférési szabályzatokat használ, ne engedélyezze vagy kényszerítse a felhasználónkénti Azure AD-Multi-Factor Authentication.**

## <a name="azure-ad-multi-factor-authentication-user-states"></a>Azure AD Multi-Factor Authentication felhasználói állapotok

A felhasználó állapota azt mutatja, hogy a rendszergazda regisztrálta-e azokat felhasználónkénti Azure AD-Multi-Factor Authentication. Az Azure AD Multi-Factor Authentication felhasználói fiókjai a következő három különböző állapottal rendelkeznek:

| Állam | Leírás | Örökölt hitelesítés érintett | Érintett böngészőalapú alkalmazások | A modern hitelesítés érintett |
|:---:| --- |:---:|:--:|:--:|
| Disabled (Letiltva) | A felhasználónkénti Azure AD-Multi-Factor Authenticationban nem regisztrált felhasználók alapértelmezett állapota. | Nem | Nem | Nem |
| Engedélyezve | A felhasználó felhasználónkénti Azure AD-Multi-Factor Authentication regisztrálva van, de továbbra is használhatja a jelszavát az örökölt hitelesítéshez. Ha a felhasználó még nem regisztrált MFA hitelesítési módszereket, a rendszer felszólítja, hogy regisztrálja a következő alkalommal, amikor bejelentkeznek a modern hitelesítés használatával (például egy webböngészőn keresztül). | Nem. A korábbi hitelesítés továbbra is működni fog, amíg a regisztrációs folyamat be nem fejeződik. | Igen. A munkamenet lejárta után az Azure AD-Multi-Factor Authentication regisztrációra van szükség.| Igen. A hozzáférési jogkivonat lejárta után az Azure AD-Multi-Factor Authentication regisztrációra van szükség. |
| Kényszerítve | A felhasználó regisztrálása felhasználónként az Azure AD Multi-Factor Authenticationban. Ha a felhasználó még nem regisztrálta a hitelesítési módszereket, a rendszer felszólítja, hogy regisztrálja a következő alkalommal, amikor bejelentkeznek a modern hitelesítés használatával (például egy webböngészőn keresztül). Azok a felhasználók, akik az *engedélyezett* állapotban végzik a regisztrációt, automatikusan átkerülnek a *kényszerített* állapotba. | Igen. Az alkalmazásokhoz alkalmazások jelszava szükséges. | Igen. Az Azure AD Multi-Factor Authentication a bejelentkezéskor szükséges. | Igen. Az Azure AD Multi-Factor Authentication a bejelentkezéskor szükséges. |

Az összes felhasználó *le van tiltva*. Ha felhasználónkénti Azure AD-Multi-Factor Authentication regisztrál felhasználókat, az állapotuk *engedélyezve* értékre vált. Ha az engedélyezett felhasználók bejelentkeznek, és elvégzik a regisztrációs folyamatot, az állapotuk *kényszerítve* értékűre vált. A rendszergazdák áthelyezhetik a felhasználókat az államok között, *beleértve az* *engedélyezett* vagy a *letiltott* állapotot is.

> [!NOTE]
> Ha a felhasználónkénti MFA újra engedélyezve van egy felhasználónál, és a felhasználó nem regisztrálja újra, az MFA-állapota nem *válik lehetővé* , hogy az MFA felügyeleti felhasználói felületén *érvénybe* lépjen. A rendszergazdának közvetlenül kell áthelyeznie a felhasználót a *kényszerített* értékre.

## <a name="view-the-status-for-a-user"></a>Felhasználó állapotának megtekintése

A felhasználói állapotok megtekintéséhez és kezeléséhez végezze el a következő lépéseket a Azure Portal lap eléréséhez:

1. Jelentkezzen be rendszergazdaként a [Azure Portalba](https://portal.azure.com) .
1. Keresse meg és válassza ki *Azure Active Directory*, majd válassza a **felhasználók**  >  **minden felhasználó** lehetőséget.
1. Válassza a **Multi-Factor Authentication** lehetőséget. Előfordulhat, hogy a menüpontra kell görgetni a jobb oldalon. Válassza az alábbi képernyőképet a teljes Azure Portal ablak és a menü helyének megtekintéséhez: [ ![ válassza a multi-Factor Authentication lehetőséget az Azure ad felhasználók ablakában.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Megnyílik egy új lap, amely megjeleníti a felhasználói állapotot, ahogy az az alábbi példában is látható.
   ![Képernyőkép az Azure AD-beli felhasználói állapotadatokat bemutató Multi-Factor Authentication](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Felhasználó állapotának módosítása

Ha módosítani szeretné egy felhasználó felhasználónkénti Azure AD-Multi-Factor Authentication állapotát, hajtsa végre a következő lépéseket:

1. Az előző lépésekkel [megtekintheti egy felhasználó állapotát](#view-the-status-for-a-user) , hogy megkapja az Azure ad multi-Factor Authentication **felhasználók** lapját.
1. Keresse meg azt a felhasználót, aki számára engedélyezni szeretné a felhasználónkénti Azure AD-Multi-Factor Authentication. Előfordulhat, hogy a felül kell változtatnia a nézetet a **felhasználók számára**.
   ![Válassza ki a felhasználót az állapot módosításához a felhasználók lapon](./media/howto-mfa-userstates/enable1.png)
1. Jelölje be a felhasználó (k) neve melletti jelölőnégyzetet a (z) állapotának módosításához.
1. A jobb oldalon, a **gyors lépések** területen válassza az **Engedélyezés** vagy a **Letiltás** lehetőséget. A következő példában a *John Smith* felhasználó neve mellett egy pipa van, és engedélyezve van a használatra: a ![ kijelölt felhasználó engedélyezése a gyors lépések menüben az Engedélyezés gombra kattintva](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > Az *engedélyezett* felhasználók automatikusan *érvénybe* lettek állítva az Azure ad-multi-Factor Authentication való regisztráláskor. Ne módosítsa manuálisan a felhasználói állapotot *kényszerített* értékre, kivéve, ha a felhasználó már regisztrálva van, vagy ha a felhasználó számára elfogadható, hogy megszakítsa a kapcsolatot az örökölt hitelesítési protokollokkal.

1. Erősítse meg a kijelölést a megnyíló előugró ablakban.

A felhasználók engedélyezése után értesítse őket e-mailben. Tájékoztassa a felhasználókat arról, hogy egy üzenet jelenik meg, hogy regisztrálja őket a következő bejelentkezés alkalmával. Továbbá, ha a szervezete nem böngészőbeli alkalmazásokat használ, amelyek nem támogatják a modern hitelesítést, létre kell hozniuk az alkalmazáshoz tartozó jelszavakat. További információkért tekintse meg az [Azure AD multi-Factor Authentication végfelhasználói útmutatóját](../user-help/multi-factor-authentication-end-user-first-time.md) , amely segítséget nyújt az első lépésekhez.

## <a name="change-state-using-powershell"></a>Állapot módosítása a PowerShell használatával

Ha a felhasználói állapotot az [Azure ad PowerShell](/powershell/azure/)használatával szeretné módosítani, módosítsa `$st.State` egy felhasználói fiók paraméterét. Egy felhasználói fióknak három lehetséges állapota van:

* *Engedélyezve*
* *Kényszerítve*
* *Disabled*  

Általánosságban elmondható, hogy ne helyezze át a felhasználókat közvetlenül a *kényszerített* állapotba, kivéve, ha már regisztrálva vannak az MFA-hoz. Ha így tesz, az örökölt hitelesítési alkalmazások nem működnek, mert a felhasználó nem ment át az Azure AD Multi-Factor Authentication-regisztrációval, és nem kapott meg egy [alkalmazás jelszavát](howto-mfa-app-passwords.md). Bizonyos esetekben előfordulhat, hogy ez a viselkedés szükséges, de a felhasználó nem regisztrálja a felhasználói élményt.

Első lépésként telepítse a *MSOnline* modult a [install-Module](/powershell/module/powershellget/install-module) paranccsal a következőképpen:

```PowerShell
Install-Module MSOnline
```

Következő lépésként kapcsolódjon a [MsolService](/powershell/module/msonline/connect-msolservice):

```PowerShell
Connect-MsolService
```

A következő példa PowerShell-parancsfájl lehetővé teszi az MFA használatát egy nevű egyéni felhasználó számára *bsimon@contoso.com* :

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

A PowerShell használata jó megoldás, ha a felhasználók tömeges engedélyezésére van szükség. A következő szkript hurkokat mutat a felhasználók listáján, és lehetővé teszi az MFA használatát a fiókjaik számára. Adja meg a felhasználói fiókokat az első sorban a `$users` következőképpen:

   ```PowerShell
   # Define your list of users to update state in bulk
   $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"

   foreach ($user in $users)
   {
       $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
       $st.RelyingParty = "*"
       $st.State = "Enabled"
       $sta = @($st)
       Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
   }
   ```

Az MFA letiltásához a következő példa beolvas egy felhasználót a [Get-MsolUser](/powershell/module/msonline/get-msoluser), majd eltávolítja a [set-MsolUser](/powershell/module/msonline/set-msoluser)használatával a megadott felhasználóhoz tartozó *StrongAuthenticationRequirements* .

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

Az MFA-t közvetlenül is letilthatja egy felhasználó számára a [set-MsolUser](/powershell/module/msonline/set-msoluser) használatával, a következőképpen:

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access"></a>Felhasználók konvertálása felhasználónkénti MFA-ból feltételes hozzáférésre

A következő PowerShell segítséget nyújt a feltételes hozzáférésen alapuló Azure AD-Multi-Factor Authentication átalakításához.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Ha a többtényezős hitelesítés engedélyezve van egy felhasználón, és a felhasználó nem regisztrálja újra, az MFA-állapota nem *válik lehetővé* , hogy az MFA felügyeleti felhasználói felületén *érvénybe* lépjen. Ebben az esetben a rendszergazdának közvetlenül kell áthelyeznie a felhasználót a *kényszerített* értékre.

## <a name="next-steps"></a>Következő lépések

Az Azure AD Multi-Factor Authentication beállításainak konfigurálásával kapcsolatban lásd: az  [Azure ad multi-Factor Authentication beállításainak konfigurálása](howto-mfa-mfasettings.md).

Az Azure AD Multi-Factor Authentication felhasználói beállításainak kezeléséhez tekintse meg a [felhasználói beállítások kezelése az Azure ad-multi-Factor Authentication](howto-mfa-userdevicesettings.md)című témakört.

Ha meg szeretné tudni, hogy egy felhasználó miért lett megkérdezve vagy nem kéri az MFA-t, tekintse meg az [Azure AD multi-Factor Authentication jelentéseit](howto-mfa-reporting.md).
