---
title: Jelszó kivonatszinkronizálás megvalósítása Azure AD Connect szinkronizálási | Microsoft Docs
description: A jelszó kivonatszinkronizálásának működését és beállítását adja meg.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee22ba3816e667bc58247fa81142e54587124fd6
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865296"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>A jelszókivonat-szinkronizálás implementálása Azure AD Connect-szinkronizálással
Ez a cikk tájékoztatást nyújt arról, hogy hogyan szinkronizálhatja a felhasználói jelszavakat egy helyi Active Directory-példányról egy felhőalapú Azure Active Directory (Azure AD) példányra.

## <a name="how-password-hash-synchronization-works"></a>A jelszókivonat-szinkronizálás működése
A Active Directory tartományi szolgáltatás kivonatérték-ábrázolás formájában tárolja a jelszavakat a tényleges felhasználói jelszó formájában. A kivonatérték egyedek matematikai függvényének (a kivonatolási *algoritmusnak) az eredménye.* Az egyirányú függvény eredménye semmilyen módszerrel nem fejthető vissza a jelszó egyszerű szöveges verziójára. 

A jelszó szinkronizálásához a Azure AD Connect kinyeri a jelszókivonatot a helyi Active Directory példányból. A rendszer további biztonsági feldolgozást alkalmaz a jelszó-kivonatra, mielőtt szinkronizálja azt a Azure Active Directory hitelesítési szolgáltatással. A jelszavak felhasználónként vannak szinkronizálva, időrendi sorrendben.

A jelszó-kivonat szinkronizálási folyamatának tényleges adatfolyama hasonló a felhasználói adatok szinkronizálásához. A jelszavak szinkronizálása azonban gyakoribb, mint más attribútumok szokásos címtár-szinkronizálási ablaka. A jelszó kivonatszinkronizálási folyamata 2 percenként fut le. A folyamat gyakorisága nem módosítható. Amikor szinkronizál egy jelszót, az felülírja a meglévő felhőbeli jelszót.

Amikor először engedélyezi a jelszó kivonatszinkronizálási funkcióját, az végrehajtja az összes hatókörbe tartozó felhasználó jelszavának kezdeti szinkronizálását. A szinkronizálni kívánt felhasználói jelszavak egy részkészletét nem határozhatja meg explicit módon. Ha azonban több összekötő is van, egyes összekötőknél letilthatja a jelszó kivonatszinkronizálását, másoknál azonban a [Set-ADSyncAADPasswordSyncConfiguration](../../active-directory-domain-services/tutorial-configure-password-hash-sync.md) parancsmagot használva.

Amikor módosít egy helyszíni jelszót, a rendszer szinkronizálja a frissített jelszót, legtöbbször percek alatt.
A jelszó kivonatszinkronizálási szolgáltatása automatikusan újra próbálkozik a sikertelen szinkronizálási kísérletekkel. Ha hiba történik a jelszó szinkronizálására tett kísérlet során, a rendszer hibát naplóz az eseménynaplóban.

A jelszó szinkronizálása nincs hatással az aktuálisan bejelentkezett felhasználóra.
A jelenlegi felhőszolgáltatás-munkamenetet nem érinti azonnal a szinkronizált jelszó módosítása, amely a felhőszolgáltatásba való bejelentkezve történik. Ha azonban a felhőszolgáltatás újra megköveteli a hitelesítést, meg kell adnia az új jelszót.

A felhasználónak másodszor is meg kell adnia a vállalati hitelesítő adatait az Azure AD-hitelesítéshez, függetlenül attól, hogy be van-e jelentkezve a vállalati hálózatra. Ez a minta azonban minimalizálható, ha a felhasználó a Bejelentkezve marad (KMSI) jelölőnégyzetet választja a bejelentkezésnél. Ez a beállítás egy munkamenet-cookie-t állít be, amely 180 napig mellőzi a hitelesítést. A KMSI viselkedését az Azure AD-rendszergazda is engedélyezheti vagy letilthatja. Emellett csökkentheti a jelszó kérését a közvetlen [egyszeri](how-to-connect-sso.md)bejelentkezés bekapcsolásával, amely automatikusan bejelentkezteti a felhasználókat, amikor a vállalati hálózathoz csatlakoztatott vállalati eszközeiken vannak.

> [!NOTE]
> A jelszó-szinkronizálás csak az objektumtípus-felhasználó számára támogatott a Active Directory. Az iNetOrgPerson objektumtípus nem támogatja.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>A jelszó kivonatszinkronizálásának részletes leírása

A következő szakasz részletesen bemutatja, hogyan működik a jelszó kivonatszinkronizálása a Active Directory Azure AD között.

![Részletes jelszófolyam](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. Az AD Connect-kiszolgáló jelszó-kivonatszinkronizálási ügynöke két percenként tárolt jelszó-kivonatokat (unicodePwd attribútumot) kér egy tartományvezérlőről.  Ez a kérés az adatok tartományvezérlők közötti szinkronizálására használt szabványos [MS-DRSR replikációs](/openspecs/windows_protocols/ms-drsr/f977faaa-673e-4f66-b9bf-48c640241d47) protokollon keresztül érhető el. A szolgáltatásfióknak rendelkeznie kell a Címtárváltozások replikálása és a Címtárváltozások replikálása minden AD-engedéllyel (a telepítéskor alapértelmezés szerint meg van jelölve) a jelszó-hashek beszerzéséhez.
2. Küldés előtt a tartományvezérlő egy olyan kulcs használatával titkosítja az MD4-jelszó kivonatát, amely az RPC-munkamenetkulcs [MD5-kivonata](https://www.rfc-editor.org/rfc/rfc1321.txt) és egy salt típusú kulcs. Ezután elküldi az eredményt a jelszó kivonatszinkronizálási ügynökének RPC-n keresztül. A tartományvezérlő a tartományvezérlő replikációs protokolljának használatával továbbítja a sót a szinkronizációs ügynöknek, így az ügynök vissza tudja fejteni a borítékot.
3. Miután a jelszó kivonatszinkronizálási ügynök megkapta a titkosított borítékot, [az MD5CryptoServiceProvider](/dotnet/api/system.security.cryptography.md5cryptoserviceprovider) és a salt használatával létrehoz egy kulcsot a fogadott adatok visszafejtéséhez az eredeti MD4 formátumba. A jelszó kivonatszinkronizálási ügynöke soha nem fér hozzá a tiszta szöveges jelszóhoz. A jelszó-kivonatszinkronizálási ügynök MD5-használatát szigorúan a tartományvezérlővel való replikációs protokoll kompatibilitására használja, és csak a helyszínen használja a tartományvezérlő és a jelszó-kivonatszinkronizálási ügynök között.
4. A jelszó-kivonatszinkronizálási ügynök 64 bájtra bővíti a 16 bájtos bináris jelszó-kivonatot úgy, hogy először átalakítja a kivonatot egy 32 bájtos hexadecimális sztringgé, majd ezt a sztringet utF-16 kódolással binárisra konvertálja vissza.
5. A jelszó-kivonatszinkronizálási ügynök felhasználónkénti sót ad hozzá, amely egy 10 bájtos salt értékből áll, és hozzáadja a 64 bájtos bináris fájlhoz az eredeti kivonat további védelme érdekében.
6. A jelszó kivonatszinkronizálási ügynök ezután kombinálja az MD4-kivonatot és a felhasználónkénti sót, és bemenő adatokat a [PBKDF2 függvénybe.](https://www.ietf.org/rfc/rfc2898.txt) A [HMAC-SHA256](/dotnet/api/system.security.cryptography.hmacsha256) kulcsos kivonatolási algoritmus 1000 iterációja van használva. 
7. A jelszó kivonatszinkronizálási ügynöke az eredményül kapott 32 bájtos kivonatot a felhasználónkénti sót és az SHA256 iterációk számát is összecsukja (az Azure AD-hez), majd továbbítja a sztringet az Azure AD Connect-ból az Azure AD-be TLS-kapcsolaton keresztül.</br> 
8. Amikor egy felhasználó megpróbál bejelentkezni az Azure AD-be, és beírja a jelszavát, a rendszer ugyanazt az MD4+salt+PBKDF2+HMAC-SHA256 folyamatot használja. Ha az eredményül kapott kivonat megegyezik az Azure AD-ban tárolt kivonatával, a felhasználó a megfelelő jelszót adta meg, és a rendszer hitelesíti.

> [!NOTE]
> Az eredeti MD4-kivonat nem lesz továbbítva az Azure AD-nek. Ehelyett az eredeti MD4-kivonat SHA256 kivonatát továbbítja a program. Ennek eredményeképpen az Azure AD-ban tárolt kivonat lekért kivonat nem használható helyszíni pass-the-hash támadásban.

### <a name="security-considerations"></a>Biztonsági szempontok

A jelszavak szinkronizálása során a jelszó egyszerű szöveges verziója nem lesz elérhető a jelszó kivonatszinkronizálási szolgáltatása, az Azure AD vagy a társított szolgáltatások számára.

A felhasználóhitelesítés az Azure AD-ban történik, nem pedig a szervezet saját Active Directory példányán. Az Azure AD-ben tárolt SHA256 jelszóadatok – az eredeti MD4-kivonat kivonata – biztonságosabbak, mint a Active Directory. Továbbá, mivel ez az SHA256-kivonat nem visszafejthető, nem lehet visszahozni a szervezet Active Directory-környezetéhez, és érvényes felhasználói jelszóként bemutatni egy pass-the-hash támadás során.

### <a name="password-policy-considerations"></a>Jelszó-szabályzatokkal kapcsolatos szempontok

A jelszó-kivonat szinkronizálásának engedélyezése kétféle jelszó-házirendet érint:

* Jelszó összetettségi szabályzata
* Jelszó-elévülési szabályzat

#### <a name="password-complexity-policy"></a>Jelszó összetettségi szabályzata

Ha a jelszó kivonatszinkronizálása engedélyezve van, az helyi Active Directory-példány jelszó-összetettségi szabályzatok felülbírálják a felhőben a szinkronizált felhasználókra vonatkozó összetettségi szabályzatokat. Az Azure AD-szolgáltatások eléréséhez használhatja a helyi Active Directory összes érvényes jelszavát.

> [!NOTE]
> A közvetlenül a felhőben létrehozott felhasználók jelszavaira továbbra is vonatkoznak a felhőben meghatározott jelszó-szabályzatok.

#### <a name="password-expiration-policy"></a>Jelszó-elévülési szabályzat

Ha egy felhasználó a jelszó-kivonat szinkronizálásának hatókörében van, a felhőfiók jelszava alapértelmezés szerint *Never Expire (Soha elévül)* lesz.

Továbbra is bejelentkezhet a felhőszolgáltatásokba egy olyan szinkronizált jelszóval, amely lejárt a helyszíni környezetben. A felhőbeli jelszó akkor frissül, amikor legközelebb módosítja a jelszót a helyszíni környezetben.

##### <a name="enforcecloudpasswordpolicyforpasswordsyncedusers"></a>EnforceCloudPasswordPolicyForPasswordSyncedUsers

Ha vannak olyan szinkronizált felhasználók, akik csak az Azure AD integrált szolgáltatásaival kommunikálnak, és meg kell felelniük egy jelszó-lejárati szabályzatnak, kényszerítheti őket, hogy megfeleljenek az Azure AD jelszó-lejárati szabályzatának a *EnforceCloudPasswordPolicyForPasswordSyncedUsers* funkció engedélyezésével.

Ha *a EnforceCloudPasswordPolicyForPasswordSyncedUsers* le van tiltva (ez az alapértelmezett beállítás), a Azure AD Connect a szinkronizált felhasználók PasswordPolicies attribútumát "DisablePasswordExpiration" beállításra állítja. Ez minden alkalommal meg történik, amikor egy felhasználó jelszava szinkronizálva van, és arra utasítja az Azure AD-t, hogy hagyja figyelmen kívül az adott felhasználó felhőbeli jelszó-elévülési szabályzatát. Az attribútum értékét az Azure AD PowerShell-modullal ellenőrizheti a következő paranccsal:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`

A EnforceCloudPasswordPolicyForPasswordSyncedUsers szolgáltatás engedélyezéséhez futtassa a következő parancsot az MSOnline PowerShell-modullal az alább látható módon. Az Enable paraméterhez igent kellene begépelni az alább látható módon:

```
Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers
cmdlet Set-MsolDirSyncFeature at command pipeline position 1
Supply values for the following parameters:
Enable: yes
Confirm
Continue with this operation?
[Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
```

Ha engedélyezve van, az Azure AD nem kerül az egyes szinkronizált felhasználókhoz, hogy eltávolítsa az értéket a `DisablePasswordExpiration` PasswordPolicies attribútumból. Ehelyett az érték el lesz távolítva a PasswordPolicies-ból az egyes felhasználók következő jelszó-kivonatszinkronizálása során, amikor legközelebb módosítják a jelszót a helyszíni `DisablePasswordExpiration` AD-ban.

Javasoljuk, hogy a jelszó-kivonatszinkronizálás engedélyezése előtt engedélyezze a EnforceCloudPasswordPolicyForPasswordSyncedUsers attribútumot, hogy a jelszó-kivonatok kezdeti szinkronizálása ne adja hozzá az értéket a `DisablePasswordExpiration` felhasználók PasswordPolicies attribútumához.

Az alapértelmezett Azure AD jelszó-szabályzat előírja, hogy a felhasználóknak 90 naponta módosítaniuk kell jelszavukat. Ha az AD-ben a szabályzat szintén 90 nap, a két szabályzatnak egyeznie kell. Ha azonban az AD-szabályzat nem 90 nap, az Azure AD jelszó-szabályzatát az Set-MsolPasswordPolicy PowerShell-paranccsal frissítheti.

Az Azure AD regisztrált tartományonként külön jelszó-elévülési szabályzatot támogat.

Figyelmeztetés: Ha vannak olyan szinkronizált fiókok, amelyekhez le nem járó jelszavakra van szükség az Azure AD-ban, explicit módon hozzá kell adni az értéket a felhasználói objektum PasswordPolicies attribútumához az `DisablePasswordExpiration` Azure AD-ban.  Ezt a következő parancs futtatásával használhatja.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> A Set-MsolPasswordPolicy PowerShell-parancs nem fog működni összevont tartományokon. 

#### <a name="synchronizing-temporary-passwords-and-force-password-change-on-next-logon"></a>Ideiglenes jelszavak szinkronizálása és "Jelszóváltozás kényszerítése a következő bejelentkezéskor"

Az első bejelentkezéskor általában arra kényszerítik a felhasználót, hogy változtassa meg a jelszavát, különösen a rendszergazdai jelszó alaphelyzetbe állítása után.  Ezt általában "ideiglenes" jelszó beállításának nevezik, és a "Felhasználónak meg kell változtatnia a jelszót a következő bejelentkezéskor" jelölővel kell végrehajtania a Active Directory (AD) felhasználói objektumán.
  
Az ideiglenes jelszó funkció segít biztosítani, hogy a hitelesítő adatok tulajdonjogának átadása az első használatkor befejeződik, így minimalizálható az az időtartam, amely alatt egynél több személy ismeri ezt a hitelesítő adatokat.

Ha engedélyezni szeretné az ideiglenes jelszavakat az Azure AD-ban a szinkronizált felhasználók számára, engedélyezheti a *ForcePasswordChangeOnLogOn* funkciót a következő parancs futtatásával a Azure AD Connect kiszolgálón:

`Set-ADSyncAADCompanyFeature -ForcePasswordChangeOnLogOn $true`

> [!NOTE]
> Ha arra kényszeríti a felhasználót, hogy a következő bejelentkezéskor változtassa meg a jelszavát, egyszerre kell módosítania a jelszót.  Azure AD Connect a jelszóváltozás kényszerítése jelzőt önmagában nem fogja használni; ez kiegészíti a jelszó kivonatszinkronizálása során észlelt jelszóváltozást.

> [!CAUTION]
> Ezt a funkciót csak akkor használja, ha az SSPR és a jelszóvisszaírás engedélyezve van a bérlőn.  Így ha egy felhasználó SSPR-en keresztül módosítja a jelszavát, az szinkronizálva lesz a Active Directory.

#### <a name="account-expiration"></a>Fiók lejárata

Ha a szervezet az accountExpires attribútumot használja a felhasználói fiókok felügyeletének részeként, a rendszer nem szinkronizálja ezt az attribútumot az Azure AD-be. Ennek eredményeképpen a lejárt Active Directory fiók továbbra is aktív lesz az Azure AD-ban a jelszó kivonatszinkronizálásra konfigurált környezetben. Javasoljuk, hogy ha a fiók lejárt, a munkafolyamat-műveletnek aktiváljon egy PowerShell-szkriptet, amely letiltja a felhasználó Azure AD-fiókját (használja a [Set-AzureADUser](/powershell/module/azuread/set-azureaduser) parancsmagot). Ezzel szemben a fiók bekapcsoláskor az Azure AD-példányt is be kell kapcsolva.

### <a name="overwrite-synchronized-passwords"></a>Szinkronizált jelszavak felülírása

A rendszergazdák manuálisan állíthatják alaphelyzetbe a jelszót a Windows PowerShell.

Ebben az esetben az új jelszó felülírja a szinkronizált jelszót, és a rendszer a felhőben meghatározott összes jelszó-szabályzatot alkalmazza az új jelszóra.

Ha ismét megváltoztatja a helyszíni jelszót, a rendszer szinkronizálja az új jelszót a felhőbe, és felülírja a manuálisan frissített jelszót.

A jelszó szinkronizálása nincs hatással a bejelentkezett Azure-felhasználóra. Az aktuális felhőszolgáltatás-munkamenetet nem érinti azonnal a szinkronizált jelszó módosítása, amely akkor következik be, amikor bejelentkezik egy felhőszolgáltatásba. A KMSI meghosszabbítja a különbség időtartamát. Amikor a felhőszolgáltatás megköveteli a hitelesítést, meg kell adnia az új jelszót.

### <a name="additional-advantages"></a>További előnyök

- A jelszó kivonatszinkronizálását általában egyszerűbb megvalósítani, mint egy összevonási szolgáltatást. Nem igényel további kiszolgálókat, és kiküszöböli a magas rendelkezésre állású összevonási szolgáltatástól való függést a felhasználók hitelesítéséhez.
- A jelszó kivonatszinkronizálása az összevonás mellett is engedélyezhető. Tartalékként is használható, ha az összevonási szolgáltatás kimaradást tapasztal.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Jelszó kivonatának szinkronizálási folyamata Azure AD Domain Services

Ha a Azure AD Domain Services-t használja a Kerberos, LDAP vagy NTLM protokollt használni szükséges alkalmazások és szolgáltatások régi hitelesítéséhez, néhány további folyamat is része a jelszó kivonatszinkronizálási folyamatának. Azure AD Connect a következő folyamattal szinkronizálja a jelszó-előjeleket az Azure AD-be a Azure AD Domain Services:

> [!IMPORTANT]
> Azure AD Connect csak helyszíni hálózati környezetekkel való szinkronizáláshoz kell telepíteni és AD DS konfigurálni. Az objektumok Azure AD-be Azure AD Connect felügyelt Azure AD DS való szinkronizálása nem támogatott.
>
> Azure AD Connect csak akkor szinkronizálja az örökölt jelszó-Azure AD DS, ha engedélyezi a Azure AD DS Azure AD-bérlő számára. A következő lépéseket nem használja a rendszer, ha csak a Azure AD Connect használja a helyszíni AD DS Azure AD-val való szinkronizáláshoz.
>
> Ha az örökölt alkalmazások nem használják az NTLM-hitelesítést vagy az EGYSZERŰ LDAP-kötéseket, javasoljuk, hogy tiltsa le az NTLM jelszó-kivonatszinkronizálást a Azure AD DS. További információ: Gyenge titkosítási csomagok letiltása és [NTLM hitelesítőadat-kivonatszinkronizálás.](../../active-directory-domain-services/secure-your-domain.md)

1. Azure AD Connect lekéri a bérlő példányának nyilvános kulcsát Azure AD Domain Services.
1. Amikor egy felhasználó megváltoztatja a jelszavát, a helyszíni tartományvezérlő két attribútumban tárolja a jelszóváltozás (hashes) eredményét:
    * Az NTLM-jelszó kivonata: *unicodePwd.*
    * *supplementalCredentials* a Kerberos-jelszó kivonatához.
1. Azure AD Connect a címtárreplikációs csatornán keresztül észleli a jelszóváltozásokat (az attribútumok módosításait más tartományvezérlőkre kell replikálni).
1. Minden olyan felhasználó esetében, akinek a jelszava megváltozott, a Azure AD Connect a következő lépéseket végzi el:
    * Véletlenszerű 256 bites szimmetrikus AES-kulcsot hoz létre.
    * Létrehoz egy véletlenszerű inicializálási vektort, amely a titkosítás első körének létrehozásához szükséges.
    * Kinyeri a Kerberos-jelszókivonatokat a *supplementalCredentials attribútumból.*
    * Ellenőrzi a Azure AD Domain Services *SyncNtlmPasswords beállítását.*
        * Ha ez a beállítás le van tiltva, a véletlenszerű, nagy energikus NTLM-kivonatot hoz létre (különbözik a felhasználó jelszavtól). Ezt a kivonatot ezután a *supplementalCrendetials* attribútumból származó pontos Kerberos-jelszó-kivonatokkal kombinálja egy adatstruktúrában.
        * Ha engedélyezve van, a egyetlen adatstruktúrában egyesíti az *unicodePwd* attribútum értékét a *supplementalCredentials* attribútumból kinyert Kerberos jelszókivonatokkal.
    * Az AES szimmetrikus kulccsal titkosítja az egyetlen adatstruktúrát.
    * Titkosítja az AES szimmetrikus kulcsot a bérlő nyilvános Azure AD Domain Services használatával.
1. Azure AD Connect továbbítja a titkosított szimmetrikus AES-kulcsot, a jelszó-hasitkokat tartalmazó titkosított adatstruktúrát és az inicializálási vektort az Azure AD-nek.
1. Az Azure AD tárolja a titkosított szimmetrikus AES-kulcsot, a titkosított adatstruktúrát és a felhasználó inicializálási vektorát.
1. Az Azure AD leküldi a titkosított szimmetrikus AES-kulcsot, a titkosított adatstruktúrát és az inicializálási vektort egy belső szinkronizálási mechanizmussal egy titkosított HTTP-munkameneten keresztül a Azure AD Domain Services.
1. Azure AD Domain Services lekéri a bérlő példányának titkos kulcsát az Azure Key Vaultból.
1. Az egyes titkosított adathalmazok (amelyek egyetlen felhasználó jelszóváltozását képviselik) Azure AD Domain Services a következő lépéseket:
    * A titkos kulcsával visszafejti az AES szimmetrikus kulcsot.
    * Az AES szimmetrikus kulcsát és az inicializálási vektort használva visszafejti a jelszó-előjeleket tartalmazó titkosított adatstruktúrát.
    * A kapott Kerberos-jelszó-Azure AD Domain Services írja. A kivonatok a felhasználói objektum *supplementalCredentials* attribútumába kerülnek, amely a Azure AD Domain Services tartományvezérlő nyilvános kulcsába van titkosítva.
    * Azure AD Domain Services az NTLM-jelszó kivonatát írja a Azure AD Domain Services tartományvezérlőre. A kivonat a felhasználói objektum *unicodePwd* attribútumába lesz mentve, amely az Azure AD Domain Services tartományvezérlő nyilvános kulcsára van titkosítva.

## <a name="enable-password-hash-synchronization"></a>Jelszókivonat szinkronizálásának engedélyezése

>[!IMPORTANT]
>Ha az áttelepítést egy AD FS (vagy más összevonási technológiáról) jelszó-kivonat-szinkronizálásra, javasoljuk, hogy kövesse az itt közzétett részletes üzembe helyezési [útmutatót.](https://aka.ms/adfstophsdpdownload)

Amikor a gyorsbeállítások Azure AD Connect telepíti, a jelszó kivonatszinkronizálása automatikusan engedélyezve lesz.  További információ: Ismerkedés a Azure AD Connect [gyorsbeállítások használatával.](how-to-connect-install-express.md)

Ha egyéni beállításokat használ a Azure AD Connect telepítésekor, a jelszó kivonatszinkronizálása a felhasználói bejelentkezési oldalon érhető el. További információ: [A](how-to-connect-install-custom.md)Azure AD Connect.

![Jelszókivonat szinkronizálásának engedélyezése](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Jelszó kivonatszinkronizálása és FIPS
Ha a kiszolgáló a Federal Information Processing Standard (FIPS) szerint zárolva lett, akkor az MD5 le van tiltva.

**Ha engedélyezni szeretné az MD5-öt a jelszó kivonatszinkronizálásához, hajtsa végre a következő lépéseket:**

1. Ugrás a %programfiles%\Microsoft Azure AD Sync\Bin elemre.
2. Nyissa meg miiserver.exe.config.
3. A fájl végén kattintson a konfigurációs/futásidejű csomópontra.
4. Adja hozzá a következő csomópontot: `<enforceFIPSPolicy enabled="false"/>`
5. Mentse a módosításokat.

Referenciaként ennek a kódrészletnek kell kinéznie:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

További információ a biztonságról és a FIPS-ről: Az Azure AD jelszó-kivonat [szinkronizálása, titkosítása és FIPS-megfelelősége.](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)

## <a name="troubleshoot-password-hash-synchronization"></a>Jelszó kivonatszinkronizálásának hibaelhárítása
Ha problémákat tapasztal a jelszó kivonatának szinkronizálásával kapcsolatban, tekintse meg a jelszó kivonatszinkronizálásának [hibaelhárításával kapcsolatos témakört.](tshoot-connect-password-hash-synchronization.md)

## <a name="next-steps"></a>Következő lépések
* [Azure AD Connect szinkronizálás: Szinkronizálási beállítások testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
* [Részletes üzembe helyezési terv az ADFS-ről jelszó-kivonatszinkronizálásra való áttelepítéshez](https://aka.ms/authenticationDeploymentPlan)
