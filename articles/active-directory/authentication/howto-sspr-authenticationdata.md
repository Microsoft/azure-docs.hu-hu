---
title: Az önkiszolgáló jelszó-visszaállításhoz szükséges kapcsolattartási adatok előzetes feltöltése – Azure Active Directory
description: Ismerje meg, hogy miként lehet előre kitölteni a kapcsolattartási adatokat Azure Active Directory önkiszolgáló jelszó-visszaállítás (SSPR) felhasználói számára, hogy a funkciót a regisztrációs folyamat befejezése nélkül is használhassák.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 10/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ed93944c2a94370250fb75a679c1ac37a2a8418c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861153"
---
# <a name="pre-populate-user-authentication-contact-information-for-azure-active-directory-self-service-password-reset-sspr"></a>Felhasználói hitelesítési kapcsolattartási adatok előzetes feltöltése Azure Active Directory önkiszolgáló jelszó-visszaállításhoz (SSPR)

A Azure Active Directory (Azure AD) önkiszolgáló jelszó-visszaállítás (SSPR) használatához a felhasználó hitelesítési kapcsolattartási adatai jelennek meg. Egyes szervezetek maguk is regisztrálják a hitelesítési jelszavukat. Más szervezetek is szívesebben szinkronizálnak a Active Directory Domain Services (AD DS) már létező hitelesítési adatokból. A szinkronizált adatokat az Azure AD és a SSPR felhasználói beavatkozás nélkül elérhetővé teszi. Ha a felhasználóknak meg kell változtatniuk vagy alaphelyzetbe kell állítaniuk a jelszavukat, akkor is megtehetik, ha korábban még nem regisztrálták a kapcsolattartási adatokat.

Ha megfelel a következő követelményeknek, előre feltöltheti a hitelesítési kapcsolattartási adatokat:

* Megfelelően formázotta az adatait a helyszíni címtárban.
* Konfigurálta [Azure ad Connect](../hybrid/how-to-connect-install-express.md) az Azure ad-bérlőhöz.

A telefonszámokat a *+ országhívószám telefonszám* formátumban kell megadni, például: *+ 1 4251234567*.

> [!NOTE]
> Az országkód és a telefonszám között szóköznek kell lennie.
>
> A jelszó-visszaállítás nem támogatja a telefonos bővítményeket. A rendszer a hívás elhelyezése előtt is eltávolítja a bővítményeket a *+ 1 4251234567X12345* formátumban.

## <a name="fields-populated"></a>Mezők kitöltve

Ha a Azure AD Connect az alapértelmezett beállításokat használja, a következő leképezések lesznek feltöltve a hitelesítési kapcsolattartási adatok SSPR való feltöltéséhez:

| Helyszíni Active Directory | Azure AD     |
|------------------------------|--------------|
| telephoneNumber              | Munkahelyi telefon |
| mobil                       | Mobiltelefon |

Miután egy felhasználó ellenőrizte mobiltelefonszámát, az Azure AD-ben a **hitelesítési kapcsolattartási adatok** területen lévő *telefonos* mező is fel lesz töltve.

## <a name="authentication-contact-info"></a>Hitelesítési kapcsolattartási adatok

A Azure Portal egy Azure AD-felhasználó **hitelesítési módszerei** lapon a globális rendszergazda manuálisan állíthatja be a hitelesítési kapcsolattartási adatokat. A meglévő metódusokat áttekintheti a *használható hitelesítési módszerek* szakaszban, vagy a **+ hitelesítési módszerek hozzáadásával**, ahogy az a következő képernyőképen is látható:

:::image type="content" source="media/howto-sspr-authenticationdata/user-authentication-contact-info.png" alt-text="Hitelesítési módszerek kezelése a Azure Portal":::

Ehhez a hitelesítési kapcsolattartási adatokhoz a következő szempontokat kell figyelembe venni:

* Ha a *telefon* mező fel van töltve, és a *mobiltelefon* engedélyezve van a SSPR-házirendben, a felhasználó ezt a számot látja a jelszó-visszaállítási regisztrációs lapon és a jelszó-visszaállítási munkafolyamatban.
* Ha az *e* -mail-mező fel van töltve, és az *e-mailek* engedélyezve vannak a SSPR szabályzatban, a felhasználó a jelszó-visszaállítási regisztrációs oldalon és a jelszó-visszaállítási munkafolyamatban láthatja a levelezést.

## <a name="security-questions-and-answers"></a>Biztonsági kérdések és válaszok

A biztonsági kérdéseket és válaszokat a rendszer biztonságosan tárolja az Azure AD-bérlőben, és csak a [SSPR regisztrációs portálon](https://aka.ms/ssprsetup)keresztül érhető el a felhasználók számára. A rendszergazdák nem láthatják, nem állíthatják be és nem módosíthatják más felhasználók kérdéseit és válaszait.

## <a name="what-happens-when-a-user-registers"></a>Mi történik, ha egy felhasználó regisztrálja

Amikor egy felhasználó regisztrál, a regisztrációs oldal a következő mezőket állítja be:

* **Hitelesítő telefon**
* **Hitelesítési E-mail**
* **Biztonsági kérdések és válaszok**

Ha a *mobiltelefon* vagy a *másodlagos e-mail* esetében értéket adott meg, a felhasználók azonnal felhasználhatják ezeket az értékeket a jelszavuk visszaállítására, még akkor is, ha nincsenek regisztrálva a szolgáltatáshoz.

A felhasználók akkor is láthatják ezeket az értékeket, amikor első alkalommal regisztrálják magukat, és ha szeretné, módosíthatja azokat. A sikeres regisztrációt követően ezek az értékek megmaradnak a *hitelesítési telefonos* és a *hitelesítő e-mailek* mezőiben.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>A hitelesítési információk beállítása és olvasása a PowerShell-lel

A következő mezők állíthatók be a PowerShell használatával:

* *Másodlagos e-mail cím*
* *Mobiltelefon*
* *Munkahelyi telefon*
    * Csak akkor állítható be, ha nem a helyszíni címtárral van szinkronizálva.

> [!IMPORTANT]
> A PowerShell v1 és a PowerShell V2 közötti parancs-funkciókban a paritás hiánya ismert. A [hitelesítési módszerek Microsoft Graph REST API (Beta)](/graph/api/resources/authenticationmethods-overview) a jelenlegi mérnöki hangsúly a modern interakció biztosításához.

### <a name="use-powershell-version-1"></a>A PowerShell 1-es verziójának használata

Első lépésként [töltse le és telepítse az Azure ad PowerShell-modult](/previous-versions/azure/jj151815(v=azure.100)#bkmk_installmodule). A telepítése után a következő lépésekkel konfigurálhatja az egyes mezőket.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>A hitelesítési adatértékek beállítása a PowerShell 1-es verziójával

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 4251234567"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 4252345678"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 4251234567" -PhoneNumber "+1 4252345678"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>A hitelesítési információk beolvasása a PowerShell 1-es verziójával

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>A hitelesítés telefonos és hitelesítési e-mail-beállításainak beolvasása

Ha a PowerShell 1-es verziójának használatakor szeretné beolvasni a **hitelesítési telefonos** és a **hitelesítési e-mailt** , használja a következő parancsokat:

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>A PowerShell 2-es verziójának használata

Első lépésként [töltse le és telepítse az Azure ad 2-es verziójú PowerShell-modulját](/powershell/module/azuread/).

Az alábbi parancsok futtatásával gyorsan telepítheti a PowerShell legújabb verzióit `Install-Module` . Az első sor ellenőrzi, hogy a modul már telepítve van-e:

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

A modul telepítése után a következő lépésekkel konfigurálhatja az egyes mezőket.

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>A hitelesítési adatértékek beállítása a PowerShell 2-es verziójával

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 4251234567"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 4252345678"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 4251234567" -TelephoneNumber "+1 4252345678"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>A hitelesítési információk beolvasása a PowerShell 2-es verziójával

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Következő lépések

Miután a hitelesítési kapcsolattartási adatok előre ki vannak töltve a felhasználók számára, végezze el a következő oktatóanyagot az önkiszolgáló jelszó-visszaállítás engedélyezéséhez:

> [!div class="nextstepaction"]
> [Az Azure AD önkiszolgáló jelszó-visszaállítás engedélyezése](tutorial-enable-sspr.md)
