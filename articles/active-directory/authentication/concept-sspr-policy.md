---
title: Önkiszolgáló jelszó-visszaállítási szabályzatok – Azure Active Directory
description: Tudnivalók a különböző Azure Active Directory önkiszolgáló jelszó-visszaállítási házirend-beállításokról
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 80be5ca22f3dfb673f09327108e66fccc9de6ddd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98918043"
---
# <a name="password-policies-and-account-restrictions-in-azure-active-directory"></a>Jelszóházirend és a fiókra vonatkozó korlátozások a Azure Active Directoryban

Azure Active Directory (Azure AD) esetében van olyan jelszóházirend, amely a jelszó bonyolultságát, hosszát vagy életkorát határozza meg. Létezik egy szabályzat is, amely a felhasználónevek elfogadható karaktereit és hosszát határozza meg.

Ha az önkiszolgáló jelszó-visszaállítás (SSPR) használatával módosítja vagy alaphelyzetbe állítja a jelszót az Azure AD-ben, a rendszer ellenőrzi a jelszó-házirendet. Ha a jelszó nem felel meg a házirend követelményeinek, a rendszer megkéri a felhasználót, hogy próbálkozzon újra. Az Azure-rendszergazdák bizonyos korlátozások vonatkoznak a normál felhasználói fiókoktól eltérő SSPR használatára.

Ez a cikk az Azure AD-bérlő felhasználói fiókjaihoz társított jelszóházirend-beállításokat és összetettségi követelményeket ismerteti, valamint azt, hogy miként használható a PowerShell a jelszó lejárati beállításainak megadásához vagy beállításához.

## <a name="username-policies"></a><a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Felhasználónév-házirendek

Minden, az Azure AD-be bejelentkező fióknak rendelkeznie kell egy egyedi egyszerű felhasználónév (UPN) attribútum értékkel, amely hozzá van rendelve a fiókjához. A helyszíni Active Directory Domain Services (AD DS) környezetet használó hibrid környezetekben az Azure AD-val az Azure AD Connect használatával szinkronizálva, alapértelmezés szerint az Azure AD UPN a helyszíni UPN-re van beállítva.

Az alábbi táblázat azokat a felhasználóneveket ismerteti, amelyek az Azure AD-vel szinkronizált helyszíni AD DS fiókokra vonatkoznak, valamint a közvetlenül az Azure AD-ben létrehozott felhőalapú felhasználói fiókok esetében:

| Tulajdonság | UserPrincipalName-követelmények |
| --- | --- |
| Engedélyezett karakterek |<ul> <li>A – Z</li> <li>a – z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Nem engedélyezett karakterek |<ul> <li>Bármely olyan \@ \" karakter, amely nem választja el a felhasználónevet a tartományból.</li> <li>A "." karakter nem szerepelhet közvetlenül a " \@ \" Symbol</li></ul> |
| Megkötések hossza |<ul> <li>A teljes hossz nem haladhatja meg a 113 karaktert</li><li>A "szimbólum előtt legfeljebb 64 karakter lehet. \@ \"</li><li>A "szimbólum után legfeljebb 48 karakter lehet. \@ \"</li></ul> |

## <a name="azure-ad-password-policies"></a><a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Azure AD-jelszóházirend

A rendszer az Azure AD-ben közvetlenül létrehozott és felügyelt összes felhasználói fiókra alkalmazza a jelszóházirend-szabályzatot. Ezen jelszóházirend-beállítások némelyike nem módosítható, de az [Azure ad jelszavas védelemhez vagy a fiókzárolási paraméterekhez egyéni tiltott jelszavakat is beállíthat](tutorial-configure-custom-password-protection.md) .

Alapértelmezés szerint a rendszer 10 sikertelen bejelentkezési kísérlet után zárolja a fiókot a helytelen jelszóval. A felhasználó egy percig ki van zárva. A további helytelen bejelentkezési kísérletek miatt a felhasználó kizárja az időtartam növelését. Az [intelligens zárolási](howto-password-smart-lockout.md) szolgáltatás az utolsó három rossz jelszó-kivonatot követi, így elkerülhető, hogy a zárolási számláló ugyanazon a jelszónál legyen növelve. Ha valaki többször is ugyanazt a jelszót adja meg, akkor ez a viselkedés nem eredményezi a fiók zárolását. Megadhatja az intelligens zárolás küszöbértékét és időtartamát.

Az Azure AD jelszóházirend nem vonatkozik a helyszíni AD DS környezetből Azure AD Connect használatával szinkronizált felhasználói fiókokra, hacsak nem engedélyezi a *EnforceCloudPasswordPolicyForPasswordSyncedUsers*.

A következő Azure AD-jelszóházirend-beállítások vannak meghatározva. A következő beállítások nem módosíthatók:

| Tulajdonság | Követelmények |
| --- | --- |
| Engedélyezett karakterek |<ul><li>A – Z</li><li>a – z</li><li>0 – 9</li> <li>@ # $% ^ & *-_! + = [] {} &#124; \: ',. ? / \` ~ " ( ) ;</li> <li>üres terület</li></ul> |
| Nem engedélyezett karakterek | Unicode-karakterek. |
| Jelszó korlátozásai |<ul><li>Legalább 8 karakterből és legfeljebb 256 karakterből állhat.</li><li>A három közül a következők közül hármat igényel:<ul><li>Kisbetűs karakterek.</li><li>Nagybetűs karakterek.</li><li>Számok (0-9).</li><li>Szimbólumok (lásd a jelszó korábbi korlátozásait).</li></ul></li></ul> |
| Jelszó lejárati időtartama (jelszó maximális kora) |<ul><li>Alapértelmezett érték: **90** nap.</li><li>Az érték konfigurálható a `Set-MsolPasswordPolicy` Windows PowerShell Azure Active Directory moduljának parancsmagjának használatával.</li></ul> |
| Jelszó lejárati értesítése (ha a felhasználók értesítést kapnak a jelszó lejáratáról) |<ul><li>Alapértelmezett érték: **14** nap (a jelszó lejárata előtt).</li><li>Az érték a parancsmag használatával konfigurálható `Set-MsolPasswordPolicy` .</li></ul> |
| Jelszó lejárata (hagyja, hogy a jelszavak soha ne járjanak le) |<ul><li>Alapértelmezett érték: **false** (azt jelzi, hogy a jelszó lejárati dátuma).</li><li>Az érték egyéni felhasználói fiókokhoz is konfigurálható a parancsmag használatával `Set-MsolUser` .</li></ul> |
| Jelszó-módosítási előzmények | Az utolsó jelszó *nem* használható újra, amikor a felhasználó megváltoztatja a jelszót. |
| Jelszó-visszaállítási előzmények | Az *utolsó jelszó újra használható,* amikor a felhasználó visszaállít egy elfelejtett jelszót. |

## <a name="administrator-reset-policy-differences"></a>A rendszergazdai visszaállítási szabályzat eltérései

Alapértelmezés szerint a rendszergazdai fiókok engedélyezve vannak az önkiszolgáló jelszó-visszaállításhoz, és egy erős alapértelmezett *kétkapus* jelszó-visszaállítási házirend van érvényben. Ez a szabályzat különbözhet a felhasználók számára megadott beállításoktól, és ez a szabályzat nem módosítható. A jelszó-visszaállítási funkciót mindig a felhasználó által hozzárendelt Azure rendszergazdai szerepkörök nélkül kell tesztelni.

A rendszergazdák kétkapus szabályzattal nem tudják használni a biztonsági kérdéseket.

A kétkapus szabályzathoz két hitelesítési adat szükséges, például e-mail-cím, hitelesítő alkalmazás vagy telefonszám. Kétkapus szabályzat a következő esetekben érvényes:

* A rendszer az alábbi Azure-rendszergazdai szerepköröket érinti:
  * Segélyszolgálat rendszergazdája
  * Szolgáltatás-rendszergazda
  * Számlázási adminisztrátor
  * Partneri Tier1-támogatás
  * Partneri szint-támogatás
  * Exchange-rendszergazda
  * Postaláda-rendszergazda
  * Skype Vállalati verzió-rendszergazda
  * Felhasználói rendszergazda
  * Címtár-írók
  * Globális rendszergazda vagy vállalati rendszergazda
  * SharePoint-rendszergazda
  * Megfelelőségi rendszergazda
  * Alkalmazás-rendszergazda
  * Biztonsági rendszergazda
  * Kiemelt szerepkörű rendszergazda
  * Intune-rendszergazda
  * Azure AD-hez csatlakoztatott eszköz helyi rendszergazdája
  * Alkalmazásproxy szolgáltatás rendszergazdája
  * Dynamics 365-rendszergazda
  * Power BI-szolgáltatásadminisztrátor
  * Hitelesítés rendszergazdája
  * Jelszókezelő
  * Kiemelt jogosultságú hitelesítés rendszergazdája

* Ha 30 nap telt el egy próbaverziós előfizetésben; vagy
* Egyéni tartomány lett konfigurálva az Azure AD-bérlőhöz, például *contoso.com*; vagy
* Azure AD Connect az identitások szinkronizálása a helyszíni címtárból

A [set-msolcompanysettings parancsmagjával PowerShell-](/powershell/module/msonline/set-msolcompanysettings) parancsmag használatával letilthatja a SSPR használatát a rendszergazdai fiókokhoz. A `-SelfServePasswordResetEnabled $False` paraméter letiltja a SSPR a rendszergazdák számára.

### <a name="exceptions"></a>Kivételek

Az egy-kapus szabályzathoz egy hitelesítő adat szükséges, például egy e-mail-cím vagy telefonszám. Az egykapus szabályzat a következő esetekben érvényes:

* A próbaverziós előfizetés első 30 napján belül van; vagy
* Nincs beállítva egyéni tartomány az Azure AD-bérlőhöz, ezért az alapértelmezett **. onmicrosoft.com* használja. Az alapértelmezett **. onmicrosoft.com* tartomány nem ajánlott éles használatra; és
* Az Azure AD Connect nem szinkronizálja az identitásokat

## <a name="password-expiration-policies"></a><a name="set-password-expiration-policies-in-azure-ad"></a>Jelszó-elévülési szabályzatok

A *globális rendszergazda* vagy a *felhasználó rendszergazdája* a [Windows PowerShell Microsoft Azure ad moduljának](/powershell/module/Azuread/) használatával állíthatja be, hogy a felhasználói jelszavak ne járjanak le.

PowerShell-parancsmagokkal is eltávolíthatja a soha nem lejáró konfigurációt, vagy megtekintheti, hogy a rendszer mely felhasználói jelszavakat állítsa le soha nem jár le.

Ez az útmutató más szolgáltatókra is vonatkozik, például az Intune-ra és a Microsoft 365ra, amely az identitás-és Címtárszolgáltatások Azure AD-ra is támaszkodik. A jelszó lejárata a szabályzat egyetlen része, amely módosítható.

> [!NOTE]
> Csak a Azure AD Connecton keresztül szinkronizált felhasználói fiókok jelszavai konfigurálhatók úgy, hogy ne járjanak le. További információ a címtár-szinkronizálásról: az [ad és az Azure ad közötti kapcsolat](../hybrid/whatis-hybrid-identity.md).

### <a name="set-or-check-the-password-policies-by-using-powershell"></a>Jelszószabályzatok beállítása vagy ellenőrzése a PowerShell-lel

Első lépésként [töltse le és telepítse az Azure ad PowerShell-modult](/powershell/module/Azuread/) , és [kapcsolja össze az Azure ad-Bérlővel](/powershell/module/azuread/connect-azuread#examples).

A modul telepítése után a következő lépésekkel hajtsa végre az egyes feladatokat szükség szerint.

### <a name="check-the-expiration-policy-for-a-password"></a>A jelszó elévülési szabályzatának keresése

1. Nyisson meg egy PowerShell-parancssort, és [kapcsolódjon az Azure ad-bérlőhöz](/powershell/module/azuread/connect-azuread#examples) egy *globális rendszergazdai* vagy *felhasználói rendszergazdai* fiók használatával.
1. Futtassa a következő parancsok egyikét egy adott felhasználóhoz vagy az összes felhasználóhoz:

   * A következő parancsmag futtatásával megtekintheti, hogy egy adott felhasználó jelszava úgy van-e beállítva, hogy soha ne járjon le. Cserélje le az `<user ID>` azonosítót az ellenőriznie kívánt felhasználó felhasználónevére, például *driley \@ contoso.onmicrosoft.com*:

       ```powershell
       Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

   * Ha látni szeretné, hogy a **jelszó soha ne járjon le** minden felhasználónál, futtassa a következő parancsmagot:

       ```powershell
       Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

### <a name="set-a-password-to-expire"></a>A lejárati jelszó beállítása

1. Nyisson meg egy PowerShell-parancssort, és [kapcsolódjon az Azure ad-bérlőhöz](/powershell/module/azuread/connect-azuread#examples) egy *globális rendszergazdai* vagy *felhasználói rendszergazdai* fiók használatával.
1. Futtassa a következő parancsok egyikét egy adott felhasználóhoz vagy az összes felhasználóhoz:

   * Az alábbi parancsmag futtatásával állíthatja be egy felhasználó jelszavát, hogy a jelszó lejárjon. Cserélje le az-t az `<user ID>` ellenőriznie kívánt felhasználó felhasználói azonosítójával, például *driley \@ contoso.onmicrosoft.com*

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
       ```

   * A szervezetben lévő összes felhasználó jelszavának beállításához használja a következő parancsmagot:

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
       ```

### <a name="set-a-password-to-never-expire"></a>Jelszó beállítása soha nem jár le

1. Nyisson meg egy PowerShell-parancssort, és [kapcsolódjon az Azure ad-bérlőhöz](/powershell/module/azuread/connect-azuread#examples) egy *globális rendszergazdai* vagy *felhasználói rendszergazdai* fiók használatával.
1. Futtassa a következő parancsok egyikét egy adott felhasználóhoz vagy az összes felhasználóhoz:

   * Az alábbi parancsmag futtatásával állíthatja be az egyik felhasználó jelszavát, hogy soha ne járjon le. Cserélje le az-t az `<user ID>` ellenőriznie kívánt felhasználó felhasználói azonosítójával, például *driley \@ contoso.onmicrosoft.com*

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
       ```

   * Ha egy szervezet összes felhasználójának jelszavát szeretné beállítani, hogy soha ne járjon le, futtassa a következő parancsmagot:

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
       ```

   > [!WARNING]
   > `-PasswordPolicies DisablePasswordExpiration`A jelszavak az attribútum alapján még mindig korra vannak állítva `pwdLastSet` . `pwdLastSet`Ha az attribútum alapján módosítja a lejárati `-PasswordPolicies None` időt, a `pwdLastSet` 90 napnál régebbi jelszavakhoz a felhasználónak a következő bejelentkezéskor módosítania kell a felhasználókat. Ez a változás nagy mennyiségű felhasználót érinthet.

## <a name="next-steps"></a>Következő lépések

A SSPR használatának megkezdéséhez tekintse meg az [oktatóanyag: a felhasználók számára a fiók zárolásának feloldását vagy a jelszavak visszaállítását Azure Active Directory önkiszolgáló jelszó-visszaállítással](tutorial-enable-sspr.md).

Ha Ön vagy a felhasználók problémákat tapasztalnak a SSPR kapcsolatban, olvassa el az [önkiszolgáló jelszó-visszaállítás hibaelhárítása](./troubleshoot-sspr.md) című témakört.
