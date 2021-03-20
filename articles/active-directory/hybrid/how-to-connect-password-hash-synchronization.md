---
title: Jelszó-kivonatolási szinkronizálás implementálása Azure AD Connect szinkronizálással | Microsoft Docs
description: A jelszó-kivonatok szinkronizálásának működéséről és beállításáról nyújt információt.
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
ms.openlocfilehash: 47d7d541ed7d9805641ffdfde381d482c8700006
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96858739"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>A jelszókivonat-szinkronizálás implementálása Azure AD Connect-szinkronizálással
Ez a cikk azokat az információkat tartalmazza, amelyekkel szinkronizálhatja a felhasználói jelszavakat egy helyszíni Active Directory-példányról egy felhőalapú Azure Active Directory-(Azure AD-) példányra.

## <a name="how-password-hash-synchronization-works"></a>A jelszókivonat-szinkronizálás működése
A Active Directory tartományi szolgáltatás a jelszavakat a tényleges felhasználói jelszó kivonatoló értékének ábrázolási formájában tárolja. A kivonatoló érték egy egyirányú matematikai függvény (a *kivonatoló algoritmus*) eredménye. Az egyirányú függvény eredménye semmilyen módszerrel nem fejthető vissza a jelszó egyszerű szöveges verziójára. 

A jelszó szinkronizálásához Azure AD Connect Sync kivonja a jelszó kivonatát a helyszíni Active Directory példányból. A rendszer további biztonsági feldolgozást alkalmaz a jelszó-kivonatra, mielőtt szinkronizálja azt a Azure Active Directory hitelesítési szolgáltatással. A jelszavak felhasználónkénti és időrendi sorrendben lesznek szinkronizálva.

A jelszó-kivonat szinkronizációs folyamatának tényleges adatfolyama hasonló a felhasználói adatok szinkronizálásához. A jelszavak azonban gyakrabban vannak szinkronizálva, mint a szabványos címtár-szinkronizálási ablak más attribútumok esetében. A jelszó-kivonat szinkronizációs folyamata 2 percenként fut. A folyamat gyakorisága nem módosítható. Amikor szinkronizál egy jelszót, felülírja a meglévő Felhőbeli jelszót.

Amikor első alkalommal engedélyezi a jelszó-kivonatolási szinkronizálási funkciót, az végrehajtja az összes hatókörrel rendelkező felhasználó jelszavának kezdeti szinkronizálását. Explicit módon nem határozhatja meg a szinkronizálni kívánt felhasználói jelszavak részhalmazát. Ha azonban több összekötő is van, akkor letilthatja a jelszó-kivonatok szinkronizálását egyes összekötők esetében, de a [set-ADSyncAADPasswordSyncConfiguration](../../active-directory-domain-services/tutorial-configure-password-hash-sync.md) parancsmag használatával nem.

Amikor módosít egy helyszíni jelszót, a frissített jelszó szinkronizálva lesz, a leggyakrabban percek alatt.
A jelszó-kivonat szinkronizációs funkciója automatikusan újrapróbálkozik a sikertelen szinkronizálási kísérletekkel. Ha egy jelszó szinkronizálására tett kísérlet során hiba lép fel, a rendszer hibát naplóz az eseménynaplóban.

A jelszó szinkronizálása nincs hatással a jelenleg bejelentkezett felhasználóra.
A jelenlegi Cloud Service-munkamenetet nem érinti azonnal a szinkronizált jelszó módosítása, amely a bejelentkezéskor egy felhőalapú szolgáltatásba kerül. Ha azonban a Cloud Service-nek újra kell hitelesítenie magát, meg kell adnia az új jelszót.

A felhasználóknak Másodszor kell megadniuk a vállalati hitelesítő adataikat az Azure AD-ben való hitelesítéshez, függetlenül attól, hogy be vannak-e jelentkezve a vállalati hálózatba. Ez a minta a lehető legkisebbre csökkenthető, ha azonban a felhasználó a bejelentkezéskor bejelöli a Keep Me bejelentkezve (KMSI) jelölőnégyzetet. Ez a beállítás olyan munkamenet-cookie-t állít be, amely 180 napig megkerüli a hitelesítést. Az Azure AD rendszergazdája engedélyezheti vagy letilthatja a KMSI viselkedését. Emellett a [zökkenőmentes SSO](how-to-connect-sso.md)bekapcsolásával csökkentheti a jelszó kéréseit, amely automatikusan aláírja a felhasználókat a vállalati hálózathoz csatlakozó vállalati eszközökön.

> [!NOTE]
> A jelszó-szinkronizálás csak az Objektumtípus felhasználója számára támogatott Active Directoryban. Az iNetOrgPerson objektum típusa nem támogatott.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>A jelszó-kivonatoló szinkronizálás működésének részletes leírása

A következő szakasz részletesen ismerteti, hogyan működik a jelszó-kivonatok szinkronizálása Active Directory és az Azure AD között.

![Részletes jelszavas folyamat](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. Az AD összekapcsolási kiszolgáló jelszavas kivonat-szinkronizálási ügynöke két percenként kér tárolt jelszó-kivonatokat (a unicodePwd attribútumot) egy TARTOMÁNYVEZÉRLŐről.  Ez a kérelem a tartományvezérlők közötti adatszinkronizáláshoz használt szabványos [MS-drsr blokkméretéhez](/openspecs/windows_protocols/ms-drsr/f977faaa-673e-4f66-b9bf-48c640241d47) replikációs protokollon keresztül történik. A szolgáltatási fióknak replikálnia kell a címtár-módosításokat, és replikálnia kell a címtárat a jelszó-kivonatok beszerzéséhez szükséges összes AD-engedély (a telepítéskor alapértelmezés szerint megadva).
2. A küldés előtt a tartományvezérlő titkosítja a MD4-jelszó kivonatát egy olyan kulccsal, amely az RPC-munkamenet kulcsának [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) kivonata és egy só. Ezután elküldi az eredményt a jelszó-kivonatoló szinkronizációs ügynöknek az RPC protokollon keresztül. A tartományvezérlő a tartományvezérlő replikációs protokolljának használatával is átadja a sót a szinkronizációs ügynöknek, így az ügynök visszafejtheti a borítékot.
3. Miután a jelszó-kivonat szinkronizációs ügynöke titkosított borítékot tartalmaz, a [MD5CryptoServiceProvider](/dotnet/api/system.security.cryptography.md5cryptoserviceprovider) és a só használatával generált egy kulcsot, amely visszafejti a kapott adatokat az eredeti MD4 formátumával. A jelszó-kivonat szinkronizációs ügynöke soha nem fér hozzá a tiszta szöveges jelszóhoz. A jelszó-kivonat szinkronizációs ügynökének MD5-használata szigorúan a DC-vel való kompatibilitást biztosító replikációs protokoll, és csak a tartományvezérlő és a jelszó-kivonat szinkronizációs ügynöke között használatos.
4. A jelszó-kivonatoló szinkronizációs ügynök kibontja a 16 bájtos bináris jelszó kivonatát 64 bájtra, először konvertálja a kivonatot egy 32 bájtos hexadecimális karakterláncra, majd átalakítja a karakterláncot a binárisba UTF-16 kódolással.
5. A jelszó-kivonatoló szinkronizálási ügynök egy 10 bájtos hosszúságú, a 64 bájtos bináris értékkel rendelkező felhasználónkénti sót hoz létre az eredeti kivonat további védelemmel való ellátása érdekében.
6. A Password hash szinkronizációs ügynök ezután egyesíti a MD4-kivonatot és a felhasználónkénti sót, és beírja azt a [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) függvénybe. 1000 az [HMAC-sha256](/dotnet/api/system.security.cryptography.hmacsha256) kulcsos kivonatoló algoritmust használó iterációk. 
7. A jelszó-kivonat szinkronizációs ügynöke az eredményül kapott 32 bájtos kivonatot, a felhasználónkénti sót és a hozzá tartozó SHA256-iterációk számát (az Azure AD általi használatra) a karakterláncot a Azure AD Connectról az Azure AD-be a TLS protokollon keresztül továbbítja.</br> 
8. Amikor egy felhasználó megpróbál bejelentkezni az Azure AD-be, és megadja a jelszavát, a jelszó ugyanazon MD4 + Salt + PBKDF2 + HMAC-SHA256 folyamaton keresztül fut. Ha az eredményül kapott kivonat megegyezik az Azure AD-ben tárolt kivonattal, a felhasználó megadta a megfelelő jelszót, és hitelesítve van.

> [!NOTE]
> Az eredeti MD4-kivonatot a rendszer nem továbbítja az Azure AD-nek. Ehelyett a rendszer az eredeti MD4 kivonat SHA256 kivonatát továbbítja. Ennek eredményeképpen, ha az Azure AD-ben tárolt kivonatot beszerezték, nem használható helyszíni pass-The-hash típusú támadásban.

### <a name="security-considerations"></a>Biztonsági szempontok

A jelszavak szinkronizálásakor a jelszó egyszerű szöveges verziója nem érhető el a jelszó-kivonat szinkronizálási szolgáltatásához, az Azure AD-hez vagy a társított szolgáltatásokhoz.

A felhasználó hitelesítése a szervezet saját Active Directory példánya helyett az Azure AD-vel történik. A SHA256 az Azure AD-ben tárolt jelszavas adatokat – az eredeti MD4-kivonat kivonata – biztonságosabb, mint amit a Active Directory tárol. Továbbá, mivel ez a SHA256-kivonat nem fejthető vissza, nem állítható vissza a szervezet Active Directory környezetére, és érvényes felhasználói jelszóként jelenik meg egy pass-The-hash típusú támadásban.

### <a name="password-policy-considerations"></a>Jelszóházirend-megfontolások

A jelszó-kivonat szinkronizálásának engedélyezéséhez két típusú jelszóházirend vonatkozik:

* Jelszó-összetettségi szabályzat
* Jelszó elévülési szabályzata

#### <a name="password-complexity-policy"></a>Jelszó-összetettségi szabályzat

Ha engedélyezve van a jelszó-kivonatolási szinkronizálás, a helyszíni Active Directory-példány jelszavas bonyolultsági szabályzatai felülbírálják a Felhőbeli összetettségi szabályzatokat a szinkronizált felhasználók számára. Az Azure AD-szolgáltatások eléréséhez használhatja a helyszíni Active Directory-példány összes érvényes jelszavát.

> [!NOTE]
> A felhőben közvetlenül létrehozott felhasználók jelszavai a felhőben definiált jelszavas szabályzatoknak is érvényesek.

#### <a name="password-expiration-policy"></a>Jelszó elévülési szabályzata

Ha a felhasználó a jelszó-kivonatolási szinkronizálás hatókörében van, alapértelmezés szerint a Cloud Account jelszó értéke *soha nem jár le*.

Továbbra is bejelentkezhet a Cloud servicesbe egy szinkronizált jelszó használatával, amely a helyszíni környezetben lejárt. A felhő jelszava frissül, amikor legközelebb megváltoztatja a jelszót a helyszíni környezetben.

##### <a name="enforcecloudpasswordpolicyforpasswordsyncedusers"></a>EnforceCloudPasswordPolicyForPasswordSyncedUsers

Ha olyan szinkronizált felhasználók vannak, akik csak az Azure AD integrált szolgáltatásaival működnek együtt, és meg kell felelniük a jelszó lejárati házirendjének, akkor a *EnforceCloudPasswordPolicyForPasswordSyncedUsers* funkció engedélyezésével kényszerítheti az Azure ad-jelszó lejárati szabályzatának betartását.

Ha a *EnforceCloudPasswordPolicyForPasswordSyncedUsers* le van tiltva (ez az alapértelmezett beállítás), Azure ad Connect a szinkronizált felhasználók PasswordPolicies attribútumát a "DisablePasswordExpiration" értékre állítja. Ez minden alkalommal megtörténik, amikor a felhasználó jelszava szinkronizálva van, és arra utasítja az Azure AD-t, hogy figyelmen kívül hagyja az adott felhasználóhoz tartozó Felhőbeli jelszó lejárati szabályzatát. Az attribútum értékét a következő paranccsal tekintheti meg az Azure AD PowerShell-modul használatával:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`

A EnforceCloudPasswordPolicyForPasswordSyncedUsers funkció engedélyezéséhez futtassa a következő parancsot a MSOnline PowerShell-modullal az alább látható módon. Az engedélyezés paraméternél az Igen értéket kell beírnia az alábbi módon:

```
Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers
cmdlet Set-MsolDirSyncFeature at command pipeline position 1
Supply values for the following parameters:
Enable: yes
Confirm
Continue with this operation?
[Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
```

Ha engedélyezve van, az Azure AD nem minden szinkronizált felhasználóhoz csatlakozik, hogy eltávolítsa az `DisablePasswordExpiration` értéket a PasswordPolicies attribútumból. Ehelyett a `DisablePasswordExpiration` rendszer eltávolítja az értéket a PasswordPolicies az egyes felhasználók következő jelszavas kivonatának szinkronizálásakor, a helyi ad-ben a következő jelszó megváltozása után.

Javasoljuk, hogy engedélyezze a EnforceCloudPasswordPolicyForPasswordSyncedUsers a jelszó-kivonat szinkronizálásának engedélyezése előtt, hogy a jelszó-kivonatok kezdeti szinkronizálása ne adja hozzá az `DisablePasswordExpiration` értéket a felhasználók PasswordPolicies attribútumához.

Az alapértelmezett Azure AD-jelszóházirend megköveteli, hogy a felhasználók 90 naponta megváltoztassák a jelszavukat. Ha az AD-szabályzat 90 nap is, a két házirendnek egyeznie kell. Ha azonban az AD-házirend nem 90 nap, akkor a Set-MsolPasswordPolicy PowerShell-paranccsal módosíthatja az Azure AD-jelszó házirendjét.

Az Azure AD egy regisztrált tartományon belüli külön jelszó-elévülési szabályzatot támogat.

Figyelmeztetés: ha vannak olyan szinkronizált fiókok, amelyeknek az Azure AD-ben nem lejáró jelszavakkal kell rendelkezniük, explicit módon hozzá kell adnia az `DisablePasswordExpiration` értéket az Azure ad felhasználói objektumának PasswordPolicies attribútumához.  Ezt a következő parancs futtatásával teheti meg.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> Az Set-MsolPasswordPolicy PowerShell-parancs összevont tartományokon nem fog működni. 

#### <a name="synchronizing-temporary-passwords-and-force-password-change-on-next-logon"></a>Ideiglenes jelszavak szinkronizálása és a "jelszó módosítása a következő bejelentkezéskor"

Általában arra kényszeríti a felhasználót, hogy az első bejelentkezéskor változtassa meg a jelszavát, különösen a rendszergazdai jelszó alaphelyzetbe állítása után.  Ezt általában "ideiglenes" jelszóként kell beállítani, és a rendszer azt ellenőrzi, hogy a "felhasználónak meg kell-e változtatni a jelszót a következő bejelentkezéskor" jelzőn a Active Directory (AD) felhasználói objektumán.
  
Az ideiglenes jelszó funkció segítségével biztosítható, hogy a hitelesítő adatok tulajdonjogának átruházása az első használatnál legyen elvégezve, hogy minimálisra csökkentse azt az időtartamot, ameddig több személy ismeri a hitelesítő adatokat.

Az Azure AD-ben a szinkronizált felhasználók számára az ideiglenes jelszavak támogatásához engedélyezheti a *ForcePasswordChangeOnLogOn* szolgáltatást a következő parancs futtatásával a Azure ad Connect-kiszolgálón:

`Set-ADSyncAADCompanyFeature -ForcePasswordChangeOnLogOn $true`

> [!NOTE]
> Ha arra kényszeríti a felhasználót, hogy a következő bejelentkezéskor megváltoztassa a jelszavát, a jelszó módosítására is szükség van.  A Azure AD Connect nem fogja önállóan felvenni a kényszerített jelszó-módosítási jelzőt. Ez a jelszó-kivonat szinkronizálásakor megjelenő észlelt jelszóval kiegészítve történik.

> [!CAUTION]
> Ezt a funkciót csak akkor használja, ha a SSPR és a jelszó visszaírási engedélyezve vannak a bérlőn.  Ez azért van így, hogy ha a felhasználó a SSPR-n keresztül módosítja a jelszavát, a rendszer szinkronizálja Active Directory.

#### <a name="account-expiration"></a>Fiók lejárata

Ha a szervezet a accountExpires attribútumot használja a felhasználói fiókok felügyelete részeként, ez az attribútum nincs szinkronizálva az Azure AD-vel. Ennek eredményeképpen a jelszó-kivonat szinkronizálására konfigurált környezet lejárt Active Directory fiókja továbbra is aktív lesz az Azure AD-ben. Javasoljuk, hogy ha a fiók lejárt, egy munkafolyamat-műveletnek aktiválnia kell egy PowerShell-parancsfájlt, amely letiltja a felhasználó Azure AD-fiókját (használja a [set-AzureADUser](/powershell/module/azuread/set-azureaduser) parancsmagot). Ezzel szemben a fiók bekapcsolásakor az Azure AD-példányt be kell kapcsolni.

### <a name="overwrite-synchronized-passwords"></a>Szinkronizált jelszavak felülírása

A rendszergazdák a Windows PowerShell használatával manuálisan állíthatják alaphelyzetbe a jelszót.

Ebben az esetben az új jelszó felülbírálja a szinkronizált jelszót, a felhőben definiált összes jelszóházirend pedig az új jelszóra lesz alkalmazva.

Ha ismét módosítja a helyszíni jelszót, az új jelszó szinkronizálva lesz a felhőbe, és felülbírálja a manuálisan frissített jelszót.

A jelszó szinkronizálása nincs hatással a bejelentkezett Azure-felhasználóra. A jelenlegi Cloud Service-munkamenetet nem érinti azonnal a szinkronizált jelszó módosítása, amely akkor jelentkezik, amikor bejelentkezett egy felhőalapú szolgáltatásba. A KMSI kiterjeszti a különbség időtartamát. Ha a Cloud Service-nek újra kell hitelesítenie magát, meg kell adnia az új jelszót.

### <a name="additional-advantages"></a>További előnyök

- Általánosságban elmondható, hogy a jelszó-kivonat szinkronizálása egyszerűbben megvalósítható, mint az összevonási szolgáltatás. Nem igényel további kiszolgálókat, és a felhasználók hitelesítéséhez kizárja a nagyfokú rendelkezésre állású összevonási szolgáltatástól való függőséget.
- A jelszó-kivonat szinkronizálása az összevonás mellett is engedélyezhető. Tartalékként használható, ha az összevonási szolgáltatás leállás miatt leáll.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Jelszó-kivonat szinkronizálási folyamata Azure AD Domain Services

Ha a Azure AD Domain Services segítségével örökölt hitelesítést biztosít a Kerberos, az LDAP vagy az NTLM protokollt használó alkalmazásokhoz és szolgáltatásokhoz, néhány további folyamat a jelszó-kivonat szinkronizációs folyamatának részét képezi. Azure AD Connect a következő folyamattal szinkronizálja a jelszó-kivonatokat az Azure AD-be a Azure AD Domain Servicesban való használathoz:

> [!IMPORTANT]
> A Azure AD Connect csak a helyszíni AD DS környezetekkel való szinkronizálásra kell telepíteni és konfigurálni. Nem támogatott Azure AD Connect telepítése Azure AD DS felügyelt tartományba az objektumok Azure AD-be való visszaszinkronizálásához.
>
> Azure AD Connect csak a régi jelszavakat szinkronizálja, amikor engedélyezi az Azure AD DSt az Azure AD-bérlő számára. A következő lépések nem használhatók, ha a helyszíni AD DS-környezet Azure AD-vel való szinkronizálásához csak Azure AD Connect használ.
>
> Ha az örökölt alkalmazásai nem használnak NTLM-hitelesítést vagy LDAP egyszerű kötéseket, javasoljuk, hogy tiltsa le az NTLM-jelszó kivonatának szinkronizálását az Azure AD DS. További információ: a [gyenge titkosítási csomagok letiltása és az NTLM hitelesítő adatok kivonatának szinkronizálása](../../active-directory-domain-services/secure-your-domain.md).

1. Azure AD Connect lekéri a bérlő Azure AD Domain Services példányának nyilvános kulcsát.
1. Amikor egy felhasználó megváltoztatja a jelszavát, a helyszíni tartományvezérlő két attribútumban tárolja a jelszó-módosítás (kivonatok) eredményét:
    * *unicodePwd* az NTLM-jelszó kivonatához.
    * *supplementalCredentials* a Kerberos-jelszó kivonatához.
1. Azure AD Connect észleli a jelszó-módosításokat a címtár-replikációs csatornán keresztül (az attribútum módosításait más tartományvezérlőkre kell replikálni).
1. A Azure AD Connect a következő lépéseket hajtja végre minden olyan felhasználónál, akinek a jelszava módosult:
    * Véletlenszerűen generált AES 256-bites szimmetrikus kulcsot hoz létre.
    * Létrehoz egy véletlenszerű inicializálási vektort, amely az első kör titkosításához szükséges.
    * A *supplementalCredentials* attribútumaiból Kinyeri a Kerberos-jelszó kivonatait.
    * Ellenőrzi a Azure AD Domain Services biztonsági konfiguráció *SyncNtlmPasswords* -beállítását.
        * Ha ez a beállítás le van tiltva, a létrehoz egy véletlenszerű, nagy entrópia-alapú NTLM-kivonatot (amely eltér a felhasználó jelszavával). Ezt a kivonatot ezután a *supplementalCrendetials* attribútum által a pontosan megadott Kerberos-jelszó kivonatokkal kombinálva egyetlen adatstruktúrába.
        * Ha engedélyezve van, a a *unicodePwd* attribútum értékét kombinálja a kinyert Kerberos-jelszó kivonatokkal a *supplementalCredentials* attribútumból egy adatstruktúrába.
    * Titkosítja az egyetlen adatszerkezetet az AES szimmetrikus kulcs használatával.
    * Titkosítja az AES szimmetrikus kulcsot a bérlő Azure AD Domain Services nyilvános kulcsa alapján.
1. Azure AD Connect továbbítja a titkosított AES szimmetrikus kulcsot, a jelszó-kivonatokat tartalmazó titkosított adatstruktúrát, valamint az inicializálási vektort az Azure AD-nek.
1. Az Azure AD tárolja a titkosított AES szimmetrikus kulcsot, a titkosított adatstruktúrát és a felhasználó inicializálási vektorát.
1. Az Azure AD leküldi a titkosított AES szimmetrikus kulcsot, a titkosított adatstruktúrát és az inicializálási vektort egy belső szinkronizálási mechanizmus használatával, amely egy titkosított HTTP-munkameneten keresztül Azure AD Domain Services.
1. Azure AD Domain Services lekéri a bérlő példányának titkos kulcsát az Azure Key vaultból.
1. Az egyes titkosított adatkészletek esetében (amely az egyetlen felhasználó jelszavainak módosítását jelenti) Azure AD Domain Services ezután végrehajtja a következő lépéseket:
    * A titkos kulcsát használja az AES szimmetrikus kulcs visszafejtéséhez.
    * Az AES szimmetrikus kulcsot használja az inicializálási vektor használatával a jelszó-kivonatokat tartalmazó titkosított adatstruktúra visszafejtéséhez.
    * A Kerberos-jelszó kivonatait írja a Azure AD Domain Services tartományvezérlőnek. A rendszer menti a kivonatokat a felhasználói objektum *supplementalCredentials* attribútumában, amely a Azure ad Domain Services tartományvezérlő nyilvános kulcsára van titkosítva.
    * Azure AD Domain Services írja be a Azure AD Domain Services tartományvezérlőnek fogadott NTLM-jelszó kivonatát. A rendszer menti a kivonatot a felhasználói objektum *unicodePwd* attribútumában, amely a Azure ad Domain Services tartományvezérlő nyilvános kulcsához van titkosítva.

## <a name="enable-password-hash-synchronization"></a>Jelszókivonat szinkronizálásának engedélyezése

>[!IMPORTANT]
>Ha AD FSról (vagy más összevonási technológiáról) végez áttelepítést jelszó-kivonatolási szinkronizálásra, javasoljuk, hogy kövesse az [itt](https://aka.ms/adfstophsdpdownload)közzétett részletes telepítési útmutatót.

Ha az **expressz beállítások** lehetőséggel telepíti a Azure ad Connectt, a jelszó-kivonatolási szinkronizálás automatikusan engedélyezve lesz. További információ: az [Azure ad Connect használatának első lépései az expressz beállítások használatával](how-to-connect-install-express.md).

Ha Azure AD Connect telepítésekor egyéni beállításokat használ, a jelszó-kivonatolási szinkronizálás elérhető a felhasználói bejelentkezési oldalon. További információ: [Azure ad Connect egyéni telepítése](how-to-connect-install-custom.md).

![Jelszókivonat szinkronizálásának engedélyezése](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Jelszó-kivonat szinkronizálása és FIPS
Ha a kiszolgálót a Federal Information Processing standard (FIPS) szerint zárolták, akkor az MD5 le van tiltva.

**A jelszó-kivonatok szinkronizálásához az MD5 engedélyezéséhez hajtsa végre a következő lépéseket:**

1. Ugrás a%programfiles%\Azure AD Sync\Bin.
2. Nyissa meg miiserver.exe.config.
3. Nyissa meg a fájl végén található Configuration/Runtime csomópontot.
4. Adja hozzá a következő csomópontot: `<enforceFIPSPolicy enabled="false"/>`
5. Mentse a módosításokat.

Hivatkozásként ez a kódrészlet az alábbihoz hasonló:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

A biztonsággal és az FIPS-vel kapcsolatos további információkért lásd: az [Azure ad Password hash-szinkronizálás, a titkosítás és az FIPS megfelelősége](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/).

## <a name="troubleshoot-password-hash-synchronization"></a>Jelszó-kivonatolási szinkronizálás hibáinak megoldása
Ha problémái vannak a jelszó kivonatának szinkronizálásával kapcsolatban, olvassa el a [jelszó-kivonat szinkronizálásának hibaelhárítása](tshoot-connect-password-hash-synchronization.md)című témakört

## <a name="next-steps"></a>Következő lépések
* [Azure AD Connect Sync: szinkronizálási beállítások testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
* [Lépésenkénti üzembe helyezési terv az ADFS-ből a jelszó-kivonatolási szinkronizálásba való áttelepítéshez](https://aka.ms/authenticationDeploymentPlan)
