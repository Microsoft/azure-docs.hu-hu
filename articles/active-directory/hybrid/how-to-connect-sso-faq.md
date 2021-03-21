---
title: 'Azure AD Connect: zökkenőmentes egyetlen Sign-On – gyakori kérdések | Microsoft Docs'
description: Válaszok az Azure Active Directory zökkenőmentes egyszeri bejelentkezéssel kapcsolatos gyakori kérdésekre.
services: active-directory
keywords: Mi az Azure AD Connect, az Azure AD, az egyszeri bejelentkezéshez szükséges összetevők telepítése Active Directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2dac4b461d4506015f0ef374eae37f67c445791d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98107871"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory zökkenőmentes egyszeri bejelentkezés: gyakori kérdések

Ebben a cikkben a Azure Active Directory zökkenőmentes egyszeri Sign-On (zökkenőmentes SSO) kapcsolatos gyakori kérdéseket tárgyaljuk. Az új tartalom ellenőrzésének visszatartása.

**K: milyen bejelentkezési módszerek használhatók a zökkenőmentes egyszeri bejelentkezéshez**

A zökkenőmentes egyszeri bejelentkezést a jelszó- [kivonatoló szinkronizálással](how-to-connect-password-hash-synchronization.md) vagy az [átmenő hitelesítési](how-to-connect-pta.md) bejelentkezési módszerekkel kombinálhatja. Ez a funkció azonban nem használható Active Directory összevonási szolgáltatások (AD FS) (ADFS) használatával.

**K: a zökkenőmentes egyszeri bejelentkezés ingyenes funkció?**

A zökkenőmentes egyszeri bejelentkezés ingyenes szolgáltatás, és nincs szüksége az Azure AD fizetős kiadásaira a használatához.

**K: a [Microsoft Azure Germany felhőben](https://www.microsoft.de/cloud-deutschland) és a [Microsoft Azure Government felhőben](https://azure.microsoft.com/features/gov/)is elérhető a zökkenőmentes egyszeri bejelentkezés?**

A Azure Government felhőhöz zökkenőmentes egyszeri bejelentkezés érhető el. A részletekért tekintse meg [a Azure Government hibrid identitásával kapcsolatos szempontokat](./reference-connect-government-cloud.md).

**K: milyen alkalmazásokat használ a `domain_hint` `login_hint` zökkenőmentes egyszeri bejelentkezés vagy a paraméter funkció?**

Az alábbiakban felsoroljuk azokat az alkalmazásokat, amelyek elküldhetik ezeket a paramétereket az Azure AD-be, így a felhasználók csendes bejelentkezési élményt biztosítanak a zökkenőmentes egyszeri bejelentkezés (azaz nem szükséges, hogy a felhasználók be tudják írni a felhasználóneveket vagy a jelszavukat):

| Alkalmazásnév | Használandó alkalmazás-URL-cím |
| -- | -- |
| Hozzáférési panel | https: \/ /MyApps.microsoft.com/contoso.com |
| Webes Outlook | https: \/ /Outlook.office365.com/contoso.com |
| Office 365 portálok | https: \/ /Portal.Office.com? domain_hint = contoso. com, https: \/ /www.office.com? domain_hint = contoso. com |

Emellett a felhasználók csendes bejelentkezési élményt kapnak, ha egy alkalmazás olyan bejelentkezési kérelmeket küld az Azure AD-végpontoknak, amelyek bérlőként vannak beállítva – vagyis https: \/ /login.microsoftonline.com/contoso.com/<.. > vagy https: \/ /login.microsoftonline.com/<tenant_ID>/<.. > – az Azure AD közös végpontja helyett – vagyis a https: \/ /login.microsoftonline.com/common/<... >. Az alábbi lista az ilyen típusú bejelentkezési kérelmeket elvégező alkalmazások nem teljes listáját tartalmazza.

| Alkalmazásnév | Használandó alkalmazás-URL-cím |
| -- | -- |
| SharePoint Online | https: \/ /contoso.SharePoint.com |
| Azure Portal | https: \/ /Portal.Azure.com/contoso.com |

A fenti táblázatokban cserélje le a "contoso.com" kifejezést a tartománynevére, hogy a bérlőnek megfelelő alkalmazás URL-címei legyenek.

Ha azt szeretné, hogy más alkalmazások is csendes bejelentkezési élményt használjanak, tudassa velünk a visszajelzések szakaszban.

**K: támogatja a zökkenőmentes egyszeri bejelentkezést `Alternate ID` a Felhasználónév helyett `userPrincipalName` ?**

Igen. A zökkenőmentes egyszeri bejelentkezés támogatja `Alternate ID` a felhasználónevet, ha a Azure ad Connect az [itt](how-to-connect-install-custom.md)látható módon van konfigurálva. Nem minden Microsoft 365-alkalmazás támogatja `Alternate ID` . Tekintse meg az adott alkalmazás dokumentációját a támogatási nyilatkozathoz.

**K: mi a különbség az [Azure ad JOIN](../devices/overview.md) és a zökkenőmentes SSO által nyújtott egyszeri bejelentkezés?**

Az [Azure ad JOIN](../devices/overview.md) egyszeri bejelentkezést biztosít a felhasználók számára, ha az eszközük regisztrálva van az Azure ad-ben. Ezeket az eszközöket nem feltétlenül kell tartományhoz csatlakoztatni. Az egyszeri bejelentkezés az *elsődleges frissítési tokenekkel* vagy a *PRT*-vel van megadva, és nem Kerberos. A Windows 10-es eszközökön a felhasználói élmény a legoptimálisabb. Az egyszeri bejelentkezés automatikusan megtörténik a Microsoft Edge böngészőben. Emellett a Chrome-ban is működik egy böngésző-bővítmény használatával.

Az Azure AD Joint és a zökkenőmentes egyszeri bejelentkezést is használhatja a bérlőn. Ez a két funkció kiegészíti egymást. Ha mindkét funkció be van kapcsolva, az Azure AD JOIN szolgáltatásból származó egyszeri bejelentkezés elsőbbséget élvez a zökkenőmentes egyszeri bejelentkezéssel szemben.

**K: nem Windows 10 rendszerű eszközöket kívánok regisztrálni az Azure AD-vel, AD FS használata nélkül. Használhatom a zökkenőmentes egyszeri bejelentkezést?**

Igen, ehhez a forgatókönyvhöz a [munkahelyi csatlakoztatási ügyfél 2,1-](https://www.microsoft.com/download/details.aspx?id=53554)es vagy újabb verziójára van szükség.

**K: Hogyan lehet átvenni a számítógépfiók Kerberos-visszafejtési kulcsát `AZUREADSSO` ?**

Fontos a helyszíni `AZUREADSSO` ad-erdőben létrehozott számítógépfiók (amely az Azure ad-t jelképező) Kerberos-visszafejtési kulcsának gyakori átadása.

>[!IMPORTANT]
>Javasoljuk, hogy legalább 30 naponként átadja a Kerberos-visszafejtési kulcsot.

Kövesse az alábbi lépéseket azon a helyszíni kiszolgálón, amelyen a Azure AD Connect futtatja:

   > [!NOTE]
   >Az alábbi lépésekhez a tartományi rendszergazda és a globális rendszergazdai hitelesítő adatok is szükségesek.
   >Ha Ön nem tartományi rendszergazda, és a tartományi rendszergazda hozzárendelte az engedélyeket, akkor hívja meg a `Update-AzureADSSOForest -OnPremCredentials $creds -PreserveCustomPermissionsOnDesktopSsoAccount`

   **1. lépés. Azon AD-erdők listájának beolvasása, amelyeken engedélyezve van a zökkenőmentes egyszeri bejelentkezés**

   1. Először töltse le és telepítse az [Azure ad PowerShellt](/powershell/azure/active-directory/overview).
   2. Lépjen a `%programfiles%\Microsoft Azure Active Directory Connect` mappába.
   3. Importálja a zökkenőmentes SSO PowerShell-modult a következő parancs használatával: `Import-Module .\AzureADSSO.psd1` .
   4. Futtassa a PowerShellt rendszergazdaként. A PowerShellben hívja meg a t `New-AzureADSSOAuthenticationContext` . Ennek a parancsnak meg kell adnia egy előugró ablakban, hogy megadja a bérlő globális rendszergazdai hitelesítő adatait.
   5. Hívás `Get-AzureADSSOStatus | ConvertFrom-Json` . Ez a parancs az AD-erdők listáját tartalmazza (tekintse meg a "tartományok" listát), amelyen a funkció engedélyezve van.

   **2. lépés. Frissítse a Kerberos-visszafejtési kulcsot minden olyan AD-erdőben, amelyen be lett állítva**

   1. Hívás `$creds = Get-Credential` . Ha a rendszer kéri, adja meg a kívánt Active Directory-erdő tartományi rendszergazdai hitelesítő adatait.

   > [!NOTE]
   >A tartományi rendszergazdai hitelesítő adatok felhasználónevet a SAM-fiók nevének formátumában kell megadni (contoso\johndoe vagy contoso. com\johndoe). A Felhasználónév tartomány részeként a tartományi rendszergazda tartományvezérlőjét kell megkeresni a DNS használatával.

   >[!NOTE]
   >A használt tartományi rendszergazdai fiók nem lehet a Protected Users csoport tagja. Ha igen, a művelet sikertelen lesz.

   2. Hívás `Update-AzureADSSOForest -OnPremCredentials $creds` . Ez a parancs frissíti az `AZUREADSSO` adott ad-erdőben lévő számítógépfiók Kerberos-visszafejtési kulcsát, és frissíti azt az Azure ad-ben.
   
   3. Ismételje meg a fenti lépéseket minden olyan AD-erdőnél, amelyre beállította a szolgáltatást.
   
  >[!NOTE]
   >Ha olyan erdőt frissít, amely nem a Azure AD Connect, akkor győződjön meg arról, hogy a globáliskatalógus-kiszolgáló (TCP 3268 és TCP 3269) kapcsolata elérhető.

   >[!IMPORTANT]
   >Győződjön meg arról, hogy _nem_ futtatja többször a `Update-AzureADSSOForest` parancsot. Ellenkező esetben a funkció addig leáll, amíg a felhasználó Kerberos-jegye lejár, és a helyszíni Active Directory újra kiadja.

**K: Hogyan lehet letiltani a zökkenőmentes egyszeri bejelentkezést?**

   **1. lépés. A szolgáltatás letiltása a bérlőn**

   **A. lehetőség: letiltás a Azure AD Connect használatával**
    
   1. Futtassa Azure AD Connect, válassza a **felhasználói bejelentkezés módosítása lapot** , és kattintson a **tovább** gombra.
   2. Törölje az **egyszeri bejelentkezés engedélyezése** lehetőséget. Folytassa a varázslót.

   A varázsló befejezése után a rendszer letiltja a zökkenőmentes egyszeri bejelentkezést a bérlőn. A képernyőn megjelenő üzenet azonban a következőképpen jelenik meg:

   "Az egyszeri bejelentkezés le van tiltva, de további manuális lépéseket is végre kell hajtani a tisztítás befejezéséhez. További információ "

   A tisztítási folyamat befejezéséhez hajtsa végre a 2. és a 3. lépést azon a helyszíni kiszolgálón, amelyen a Azure AD Connect fut.

   **B. lehetőség: letiltás a PowerShell használatával**

   Futtassa az alábbi lépéseket azon a helyszíni kiszolgálón, amelyen a Azure AD Connect futtatja:

   1. Először töltse le és telepítse az [Azure ad PowerShellt](/powershell/azure/active-directory/overview).
   2. Lépjen a `%programfiles%\Microsoft Azure Active Directory Connect` mappába.
   3. Importálja a zökkenőmentes SSO PowerShell-modult a következő parancs használatával: `Import-Module .\AzureADSSO.psd1` .
   4. Futtassa a PowerShellt rendszergazdaként. A PowerShellben hívja meg a t `New-AzureADSSOAuthenticationContext` . Ennek a parancsnak meg kell adnia egy előugró ablakban, hogy megadja a bérlő globális rendszergazdai hitelesítő adatait.
   5. Hívás `Enable-AzureADSSO -Enable $false` .
   
   Ezen a ponton a zökkenőmentes egyszeri bejelentkezés le van tiltva, de a tartományok konfigurálva lesznek abban az esetben, ha a zökkenőmentes egyszeri bejelentkezést vissza szeretné állítani. Ha teljesen el szeretné távolítani a tartományokat a zökkenőmentes SSO-konfigurációból, a fenti 5. lépés elvégzése után hívja meg a következő parancsmagot: `Disable-AzureADSSOForest -DomainFqdn <fqdn>` .

   >[!IMPORTANT]
   >A zökkenőmentes egyszeri bejelentkezés letiltása a PowerShell használatával nem változtatja meg Azure AD Connect állapotát. A zökkenőmentes egyszeri bejelentkezés a **felhasználó bejelentkezési** oldalának módosítása lapon engedélyezettként jelenik meg.

   **2. lépés. Azon AD-erdők listájának beolvasása, amelyeken engedélyezve van a zökkenőmentes egyszeri bejelentkezés**

   Ha a Azure AD Connect használatával letiltotta a zökkenőmentes egyszeri bejelentkezést, kövesse az alábbi 1 – 4. feladatot. Ha letiltotta a zökkenőmentes egyszeri bejelentkezést a PowerShell használatával, ugorjon az alábbi 5. feladatra.

   1. Először töltse le és telepítse az [Azure ad PowerShellt](/powershell/azure/active-directory/overview).
   2. Lépjen a `%programfiles%\Microsoft Azure Active Directory Connect` mappába.
   3. Importálja a zökkenőmentes SSO PowerShell-modult a következő parancs használatával: `Import-Module .\AzureADSSO.psd1` .
   4. Futtassa a PowerShellt rendszergazdaként. A PowerShellben hívja meg a t `New-AzureADSSOAuthenticationContext` . Ennek a parancsnak meg kell adnia egy előugró ablakban, hogy megadja a bérlő globális rendszergazdai hitelesítő adatait.
   5. Hívás `Get-AzureADSSOStatus | ConvertFrom-Json` . Ez a parancs az AD-erdők listáját tartalmazza (tekintse meg a "tartományok" listát), amelyen a funkció engedélyezve van.

   **3. lépés. Törölje kézzel a `AZUREADSSO` számítógépfiókot a felsorolt ad-erdőkből.**

## <a name="next-steps"></a>Következő lépések

- Rövid [**útmutató – az**](how-to-connect-sso-quick-start.md) Azure ad zökkenőmentes egyszeri bejelentkezésének beszerzése és futtatása.
- [**Technikai**](how-to-connect-sso-how-it-works.md) részletes útmutató – a funkció működésének megismerése.
- [**Hibaelhárítás**](tshoot-connect-sso.md) – megismerheti a szolgáltatással kapcsolatos gyakori problémák megoldását.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – új szolgáltatásokra vonatkozó kérelmek bejelentése.
