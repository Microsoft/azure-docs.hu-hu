---
title: Kombinált regisztráció – Azure Active Directory
description: Az Azure AD Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítás együttes regisztrációjának hibaelhárítása
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: db87887fc2b51c7cb8cb300eb8e711d3ae9b6ac8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98610784"
---
# <a name="troubleshooting-combined-security-information-registration"></a>A kombinált biztonsági információk regisztrációjának hibaelhárítása

A cikkben található információk arra szolgálnak, hogy miként lehet a felhasználók által a kombinált regisztrációs élményben jelentett problémákkal kapcsolatos hibaelhárítást végző rendszergazdákat irányítani.

## <a name="audit-logs"></a>Naplók

A kombinált regisztrációhoz naplózott események az Azure AD-naplók hitelesítési módszerek szolgáltatásában találhatók.

![Az Azure AD naplóinak kezelőfelülete, amely a regisztrációs eseményeket mutatja](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

A következő táblázat felsorolja a kombinált regisztráció által generált összes naplózási eseményt:

| Tevékenység | Állapot | Ok | Description |
| --- | --- | --- | --- |
| A felhasználó az összes szükséges biztonsági adatot regisztrálta | Siker | A felhasználó regisztrálta az összes szükséges biztonsági adatot. | Ez az esemény akkor következik be, amikor egy felhasználó sikeresen befejezte a regisztrációt.|
| A felhasználó az összes szükséges biztonsági adatot regisztrálta | Hiba | A felhasználó megszakította a biztonsági adatok regisztrálását. | Ez az esemény akkor következik be, amikor egy felhasználó megszakítja a regisztrációt a megszakítási módból.|
| Felhasználó által regisztrált biztonsági adatok | Siker | Felhasználó által regisztrált *metódus*. | Ez az esemény akkor következik be, amikor egy felhasználó egy egyéni metódust regisztrál. A *metódus* lehet hitelesítő alkalmazás, telefon, E-mail, biztonsági kérdés, alkalmazás jelszava, alternatív telefon stb.| 
| A felhasználó által felülvizsgált biztonsági információ | Siker | A felhasználó sikeresen áttekintette a biztonsági adatokat. | Ez az esemény akkor következik be, amikor a felhasználó a biztonsági adatok áttekintése oldalon kiválasztja a **megfelelőt** .|
| A felhasználó által felülvizsgált biztonsági információ | Hiba | A felhasználó nem tudta áttekinteni a biztonsági adatokat. | Ez az esemény akkor következik be, amikor a felhasználó a biztonsági adatok áttekintése lapon kiválasztja a **megfelelőt** , de a háttér nem működik.|
| A felhasználó törölte a biztonsági adatokat | Siker | Felhasználó által törölt *metódus*. | Ez az esemény akkor következik be, amikor egy felhasználó töröl egy egyéni metódust. A *metódus* lehet hitelesítő alkalmazás, telefon, E-mail, biztonsági kérdés, alkalmazás jelszava, alternatív telefon stb.|
| A felhasználó törölte a biztonsági adatokat | Hiba | A felhasználó nem tudta törölni a *metódust*. | Ez az esemény akkor következik be, amikor egy felhasználó megpróbál törölni egy metódust, de a kísérlet valamilyen okból meghiúsul. A *metódus* lehet hitelesítő alkalmazás, telefon, E-mail, biztonsági kérdés, alkalmazás jelszava, alternatív telefon stb.|
| A felhasználó módosította az alapértelmezett biztonsági adatokat | Siker | A felhasználó megváltoztatta a *metódus* alapértelmezett biztonsági adatait. | Ez az esemény akkor következik be, amikor a felhasználó megváltoztatja az alapértelmezett metódust. A *metódus* lehet a hitelesítő alkalmazás értesítése, a saját hitelesítő alkalmazásból vagy tokenből származó kód, a Call + X xxxxxxxxxx, a szöveg a kód + x XXXXXXXXX, és így tovább.|
| A felhasználó módosította az alapértelmezett biztonsági adatokat | Hiba | A felhasználó nem tudta módosítani az alapértelmezett biztonsági adatokat a *metódushoz*. | Ez az esemény akkor következik be, amikor egy felhasználó megpróbálja módosítani az alapértelmezett metódust, de a kísérlet valamilyen okból meghiúsul. A *metódus* lehet a hitelesítő alkalmazás értesítése, a saját hitelesítő alkalmazásból vagy tokenből származó kód, a Call + X xxxxxxxxxx, a szöveg a kód + x XXXXXXXXX, és így tovább.|

## <a name="troubleshooting-interrupt-mode"></a>Megszakítási mód hibaelhárítása

| Hibajelenség | Hibaelhárítási lépések |
| --- | --- |
| Nem látom a várt metódusokat. | 1. Ellenőrizze, hogy a felhasználó rendelkezik-e Azure AD-rendszergazdai szerepkörrel. Ha igen, tekintse meg a SSPR rendszergazdai szabályzatának eltéréseit. <br> 2. állapítsa meg, hogy a felhasználó meg van-e szakítva Multi-Factor Authentication regisztráció kényszerítése vagy a SSPR regisztrációjának kényszerítése miatt. Tekintse meg a [folyamatábrát](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) a "kombinált regisztrációs üzemmódok" alatt, és határozza meg, hogy mely metódusokat kell megjeleníteni. <br> 3. határozza meg, hogy a Multi-Factor Authentication-vagy a SSPR-szabályzat Hogyan módosult a közelmúltban. Ha a módosítás nemrég történt, eltarthat egy ideig, amíg a frissített házirend propagálva lenne.|

## <a name="troubleshooting-manage-mode"></a>Kezelési mód hibaelhárítása

| Hibajelenség | Hibaelhárítási lépések |
| --- | --- |
| Nincs lehetőség egy adott metódus hozzáadására. | 1. Ellenőrizze, hogy a metódus engedélyezve van-e Multi-Factor Authentication vagy SSPR. <br> 2. Ha a metódus engedélyezve van, mentse újra a szabályzatokat, és várjon 1-2 órával a tesztelés előtt. <br> 3. Ha a metódus engedélyezve van, győződjön meg arról, hogy a felhasználó még nem állította be a beállított módszer maximális számát.|

## <a name="disable-combined-registration"></a>Kombinált regisztráció letiltása

Amikor egy felhasználó egy telefonszámot és/vagy egy Mobile-alkalmazást regisztrál az új kombinált felületen, a szolgáltatási bélyegzőket (StrongAuthenticationMethods) tartalmazó készletet hoz létre az adott felhasználó számára. Ez a funkció lehetővé teszi, hogy a felhasználó Multi-Factor Authenticationt hajtson végre ezekkel a módszerekkel, amikor Multi-Factor Authentication szükséges.

Ha egy rendszergazda engedélyezte az előzetes verziót, a felhasználók regisztrálhatnak az új felhasználói felületre, majd a rendszergazda letiltja az előzetes verziót, és előfordulhat, hogy a felhasználók nem tudnak regisztrálni Multi-Factor Authentication is.

Ha az egyesített regisztrációt befejező felhasználó az aktuális önkiszolgáló jelszó-visszaállítási (SSPR) regisztrációs oldalra kerül [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) , a rendszer a felhasználótól a laphoz való hozzáférés előtt megkéri a multi-Factor Authentication elvégzésére. Ez a lépés technikai szempontból várható, de olyan felhasználók számára is új, akik korábban csak SSPR voltak regisztrálva. Bár ez az extra lépés javítja a felhasználó biztonsági állapotát azáltal, hogy egy másik biztonsági szintet biztosít, a rendszergazdák visszaállíthatják a felhasználókat, hogy már ne tudják végrehajtani a Multi-Factor Authentication.  

### <a name="how-to-roll-back-users"></a>Felhasználók visszaállítása

Ha rendszergazdaként szeretné alaphelyzetbe állítani a felhasználó Multi-Factor Authentication beállításait, használhatja a következő szakaszban megadott PowerShell-parancsfájlt. A szkript törli a felhasználó StrongAuthenticationMethods tulajdonságát és/vagy telefonszámát. Ha ezt a parancsfájlt a felhasználók számára futtatja, akkor újra kell regisztrálnia Multi-Factor Authentication, ha szükségük van rá. Javasoljuk, hogy az összes érintett felhasználó visszaállítása előtt tesztelje a visszaállítást egy vagy két felhasználóval.

Az alábbi lépések segítséget nyújtanak a felhasználók vagy felhasználócsoportok visszaállításában.

#### <a name="prerequisites"></a>Előfeltételek

1. Telepítse a megfelelő Azure AD PowerShell-modulokat. A PowerShell-ablakban futtassa a következő parancsokat a modulok telepítéséhez:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Mentse az érintett felhasználói objektumok azonosítóinak listáját a számítógépre szövegfájlként egy AZONOSÍTÓval, soronként. Jegyezze fel a fájl helyét.
1. Mentse a következő parancsfájlt a számítógépre, és jegyezze fel a parancsfájl helyét:

   ```powershell
   <# 
   //********************************************************
   //*                                                      *
   //*   Copyright (C) Microsoft. All rights reserved.      *
   //*                                                      *
   //********************************************************
   #>

   param($path)

   # Define Remediation Fn
   function RemediateUser {

       param  
       (
           $ObjectId
       )

       $user = Get-MsolUser -ObjectId $ObjectId

       Write-Host "Checking if user is eligible for rollback: UPN: "  $user.UserPrincipalName  " ObjectId: "  $user.ObjectId -ForegroundColor Yellow

       $hasMfaRelyingParty = $false
       foreach($p in $user.StrongAuthenticationRequirements)
       {
           if ($p.RelyingParty -eq "*")
           {
               $hasMfaRelyingParty = $true
               Write-Host "User was enabled for per-user MFA." -ForegroundColor Yellow
           }
       }

       if ($user.StrongAuthenticationMethods.Count -gt 0 -and -not $hasMfaRelyingParty)
       {
           Write-Host $user.UserPrincipalName " is eligible for rollback" -ForegroundColor Yellow
           Write-Host "Rolling back user ..." -ForegroundColor Yellow
           Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName $user.UserPrincipalName
           Write-Host "Successfully rolled back user " $user.UserPrincipalName -ForegroundColor Green
       }
       else
       {
           Write-Host $user.UserPrincipalName " is not eligible for rollback. No action required."
       }

       Write-Host ""
       Start-Sleep -Milliseconds 750
   }

   # Connect
   Import-Module MSOnline
   Connect-MsolService

   foreach($line in Get-Content $path)
   {
       RemediateUser -ObjectId $line
   }
   ```

#### <a name="rollback"></a>Visszaállítás

A PowerShell-ablakban futtassa a következő parancsot, amely biztosítja a parancsfájl-és a felhasználói fájlok helyét. Ha a rendszer kéri, adja meg a globális rendszergazdai hitelesítő adatokat. A parancsfájl az egyes felhasználói frissítési műveletek eredményét fogja kiadni.

`<script location> -path <user file location>`

### <a name="disable-the-updated-experience"></a>A frissített felhasználói felület letiltása

A frissített felhasználói élmény letiltásához hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be a Azure Portal felhasználói rendszergazdaként.
2. Nyissa meg **Azure Active Directory**  >  **felhasználói beállítások**  >  **kezelése beállítások a hozzáférési panel előzetes verziójának funkcióit**.
3. A **felhasználók a biztonsági adatok regisztrálásához és kezeléséhez az előzetes verziójú funkciókat használhatják**, a választót a **none** értékre állíthatja, majd a **Save (Mentés**) lehetőséget is választhatja.

A rendszer a továbbiakban nem fogja tudni regisztrálni a felhasználókat a frissített felhasználói élmény használatával.

## <a name="next-steps"></a>Következő lépések

* [További információ az önkiszolgáló jelszó-visszaállítás és az Azure AD-Multi-Factor Authentication együttes regisztrálásáról](concept-registration-mfa-sspr-combined.md)
