---
title: Felhasználói profil attribútumai a Azure Active Directory B2C
description: A Azure AD B2C Directory felhasználói profil által támogatott felhasználói erőforrástípus attribútumainak megismerése. Ismerje meg a beépített attribútumokat és bővítményeket, valamint azt, hogy az attribútumok hogyan Microsoft Graph.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7dfad71d05a882e3a3941a96e12489adb5fb3234
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102500529"
---
# <a name="user-profile-attributes"></a>Felhasználói profilattribútumok

A Azure Active Directory (Azure AD) B2C címtár felhasználói profilja egy beépített attribútummal rendelkezik, például a név, a vezetéknév, a város, az irányítószám és a telefonszám számára. A felhasználói profilt külső adattár nélkül is kiterjesztheti saját alkalmazásadatok használatával. 

Microsoft Graph a Azure AD B2C felhasználói profilokkal használható attribútumok többségét is támogatja. Ez a cikk a felhasználói profilok támogatott Azure AD B2C attribútumait ismerteti. Emellett azokat az attribútumokat is megjegyzi, amelyeket a Microsoft Graph nem támogat, valamint olyan Microsoft Graph attribútumokat, amelyeket nem kíván használni a Azure AD B2C.

> [!IMPORTANT]
> Ne használjon beépített vagy kiterjesztési attribútumokat a bizalmas személyes adatok, például a fiók hitelesítő adatainak, a kormányzati azonosító számok, a kártyatulajdonos-adatok, a pénzügyi fiókadatok, az egészségügyi információk vagy a bizalmas háttér-információk tárolásához.

A külső rendszerekkel is integrálható. Használhatja például a Azure AD B2Ct a hitelesítéshez, de a delegálást egy külső ügyfélkapcsolat-kezelési (CRM) vagy ügyfél-törzsvásárlói adatbázisra, amely az ügyféladatok mérvadó forrása. További információt a [távoli profil](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) megoldásában talál.

## <a name="azure-ad-user-resource-type"></a>Azure AD felhasználói erőforrástípus

Az alábbi táblázat az Azure AD B2C Directory felhasználói profil által támogatott [felhasználói erőforrástípus](/graph/api/resources/user) attribútumokat sorolja fel. A következő információkat adja meg az egyes attribútumokról:

- Az Azure AD B2C által használt attribútum neve (amelyet a Microsoft Graph neve zárójelben, ha más)
- Attribútum adattípusa
- Attribútum leírása
- Ha az attribútum elérhető a Azure Portal
- Ha az attribútum felhasználói folyamatokban is használható
- Ha az attribútum az [Azure ad egy adott technikai profiljában](active-directory-technical-profile.md) és a ( &lt; szabályzattípushoz &gt; , &lt; OutputClaims &gt; vagy &lt; PersistedClaims &gt; ) szakaszban is használható.

|Név     |Típus     |Leírás|Azure Portal|Felhasználói folyamatok|Egyéni szabályzat|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Logikai|Azt jelzi, hogy a felhasználói fiók engedélyezve van vagy le van tiltva: **igaz** , ha a fiók engedélyezve van, ellenkező esetben **hamis**.|Igen|Nem|Megőrzött, kimenet|
|korcsoport        |Sztring|A felhasználó korcsoport. Lehetséges értékek: NULL, nem definiált, kisebb, felnőtt, NotAdult.|Igen|Nem|Megőrzött, kimenet|
|alternativeSecurityId ([identitások](#identities-attribute))|Sztring|A külső identitás szolgáltatójának egyetlen felhasználói identitása.|Nem|Nem|Bemenet, megőrzött, kimenet|
|alternativeSecurityIds ([identitások](#identities-attribute))|alternatív securityId-gyűjtemény|A külső identitás-szolgáltatók felhasználói identitásának gyűjteménye.|Nem|Nem|Megőrzött, kimenet|
|city            |Sztring|A város, amelyben a felhasználó található. Maximális hosszúság 128.|Igen|Yes|Megőrzött, kimenet|
|consentProvidedForMinor|Sztring|Azt határozza meg, hogy a beleegyezik-e a kiskorú számára. Megengedett értékek: NULL, engedélyezett, megtagadva vagy notRequired.|Igen|Nem|Megőrzött, kimenet|
|ország         |Sztring|Az ország/régió, amelyben a felhasználó található. Példa: "US" vagy "UK". Maximális hosszúság 128.|Igen|Yes|Megőrzött, kimenet|
|createdDateTime|DateTime|A felhasználói objektum létrehozásának dátuma. Csak olvasható.|Nem|Nem|Megőrzött, kimenet|
|creationType    |Sztring|Ha a felhasználói fiók helyi fiókként lett létrehozva egy Azure Active Directory B2C bérlő számára, akkor az érték a LocalAccount vagy a nameCoexistence. Csak olvasható.|Nem|Nem|Megőrzött, kimenet|
|dateOfBirth     |Date|születési dátum.|Nem|Nem|Megőrzött, kimenet|
|Részleg      |Sztring|Annak a részlegnek a neve, amelyben a felhasználó működik. Maximális hosszúság 64.|Igen|Nem|Megőrzött, kimenet|
|displayName     |Sztring|A felhasználó megjelenítendő neve. Maximális hosszúság 256.|Igen|Yes|Megőrzött, kimenet|
|<sup>1</sup> . érték facsimiletelephonenumber|Sztring|A felhasználó üzleti faxhoz tartozó telefonszám.|Igen|Nem|Megőrzött, kimenet|
|givenName       |Sztring|A felhasználó megadott neve (keresztnév). Maximális hosszúság 64.|Igen|Yes|Megőrzött, kimenet|
|jobTitle        |Sztring|A felhasználó beosztása. Maximális hosszúság 128.|Igen|Yes|Megőrzött, kimenet|
|immutableId     |Sztring|Olyan azonosító, amely általában a helyszíni Active Directory áttelepített felhasználók számára használatos.|Nem|Nem|Megőrzött, kimenet|
|legalAgeGroupClassification|Sztring|Jogi korcsoport besorolása. Írásvédett, és a Beszerzésimennyiség és a consentProvidedForMinor tulajdonságok alapján számítható ki. Megengedett értékek: NULL, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult és Adult.|Igen|Nem|Megőrzött, kimenet|
|<sup>1</sup> . legalCountry  |Sztring|Ország/régió jogi célokra.|Nem|Nem|Megőrzött, kimenet|
|Levelezés            |Sztring|A felhasználó SMTP-címe, például: " bob@contoso.com ". Csak olvasható.|Nem|Nem|Megőrzött, kimenet|
|mailNickName    |Sztring|A felhasználó levelezési aliasa. Maximális hosszúság 64.|Nem|Nem|Megőrzött, kimenet|
|mobil (mobiltelefon) |Sztring|A felhasználó elsődleges mobil telefonszáma. Maximális hosszúság 64.|Igen|Nem|Megőrzött, kimenet|
|a netI           |Sztring|NET-azonosító.|Nem|Nem|Megőrzött, kimenet|
|objectId        |Sztring|Globálisan egyedi azonosító (GUID), amely a felhasználó egyedi azonosítója. Példa: 12345678-9ABC-def0-1234-56789abcde. Csak olvasható, nem módosítható.|Csak olvasás|Yes|Bemenet, megőrzött, kimenet|
|otherMails      |Karakterlánc-gyűjtemény|A felhasználóhoz tartozó egyéb e-mail-címek listája. Példa: [" bob@contoso.com ", " Robert@fabrikam.com "].|Igen (másodlagos e-mail)|No|Megőrzött, kimenet|
|jelszó        |Sztring|A helyi fiók jelszava a felhasználó létrehozásakor.|Nem|Nem|Kitartott|
|passwordPolicies     |Sztring|A jelszó szabályzata. Ez egy olyan karakterlánc, amely különböző nevű nevet tartalmaz vesszővel elválasztva. Például: "DisablePasswordExpiration, DisableStrongPassword".|Nem|Nem|Megőrzött, kimenet|
|physicalDeliveryOfficeName (officeLocation)|Sztring|Az iroda helye a felhasználó üzleti helyén. Maximális hosszúság 128.|Igen|Nem|Megőrzött, kimenet|
|postalCode      |Sztring|A felhasználó postai levélcímének postai irányítószáma. A postai irányítószám a felhasználó országára/régiójára vonatkozik. Az Amerikai Egyesült Államokban ez az attribútum tartalmazza a ZIP-kódot. Maximális hosszúság 40.|Igen|Nem|Megőrzött, kimenet|
|preferredLanguage    |Sztring|A felhasználó előnyben részesített nyelve. Az előnyben részesített nyelvi formátum a 4646-es RFC-dokumentumon alapul. A név a nyelvhez társított ISO 639 2-Letter kisbetűs nyelvi kód, valamint az országhoz vagy régióhoz tartozó ISO 3166 2-Letter nagybetűs alkulturális kód kombinációja. Példa: "en-US" vagy "es-ES".|Nem|Nem|Megőrzött, kimenet|
|refreshTokensValidFromDateTime (signInSessionsValidFromDateTime)|DateTime|A jelen időpont előtt kiadott frissítési tokenek érvénytelenek, és az alkalmazások hibaüzenetet kapnak, amikor érvénytelen frissítési tokent használnak új hozzáférési jogkivonat beszerzéséhez. Ha ez történik, az alkalmazásnak új frissítési jogkivonatot kell bekérnie egy kérelemnek az engedélyezés végpontba való beszerzésével. Csak olvasható.|Nem|Nem|Kimenet|
|signInNames ([identitások](#identities-attribute)) |Sztring|A helyi fiók felhasználójának egyedi bejelentkezési neve a címtárban. Ezzel az attribútummal beolvashatja a bejelentkezési értékkel rendelkező felhasználót a helyi fiók típusának megadása nélkül.|Nem|Nem|Bevitel|
|signInNames. userName ([identitások](#identities-attribute)) |Sztring|A helyi fiók felhasználójának egyedi felhasználóneve a címtárban. Ezzel az attribútummal lehet létrehozni vagy beolvasni egy adott bejelentkezési felhasználónévvel rendelkező felhasználót. Ha ezt a PersistedClaims a javítási művelet során egyedül adja meg, a más típusú signInNames is törlődik. Ha új típusú signInNames szeretne hozzáadni, akkor is meg kell őriznie a meglévő signInNames.|Nem|Nem|Bemenet, megőrzött, kimenet|
|signInNames. telefonszám ([identitások](#identities-attribute)) |Sztring|A helyi fiók felhasználójának egyedi telefonszáma a címtárban. Ezzel az attribútummal lehet létrehozni vagy beolvasni egy adott bejelentkezési telefonszámmal rendelkező felhasználót. Ha ezt az attribútumot csak a PersistedClaims adja meg, a javítási művelet során a rendszer eltávolítja a más típusú signInNames. Ha új típusú signInNames szeretne hozzáadni, akkor is meg kell őriznie a meglévő signInNames.|Nem|Nem|Bemenet, megőrzött, kimenet|
|signInNames. emailAddress ([identitások](#identities-attribute))|Sztring|A címtárban lévő helyi fiók felhasználójának egyedi e-mail-címe. Ezzel a paranccsal létrehozhat vagy beszerezhet egy adott bejelentkezési e-mail-címmel rendelkező felhasználót. Ha ezt az attribútumot csak a PersistedClaims adja meg, a javítási művelet során a rendszer eltávolítja a más típusú signInNames. Ha új típusú signInNames szeretne hozzáadni, akkor is meg kell őriznie a meglévő signInNames.|Nem|Nem|Bemenet, megőrzött, kimenet|
|állapot           |Sztring|A felhasználó címe szerinti állam vagy tartomány. Maximális hosszúság 128.|Igen|Yes|Megőrzött, kimenet|
|streetAddress   |Sztring|A felhasználó üzletviteli helyének címe. Maximális hosszúság 1024.|Igen|Yes|Megőrzött, kimenet|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|Sztring|A felhasználó másodlagos telefonszáma, amely a többtényezős hitelesítéshez használatos.|Igen|Nem|Megőrzött, kimenet|
|<sup>1</sup> . strongAuthenticationEmailAddress|Sztring|A felhasználó SMTP-címe. Példa: " bob@contoso.com " Ez az attribútum a Felhasználónév-szabályzattal való bejelentkezéshez használatos a felhasználói e-mail-cím tárolásához. Az e-mail-cím, amelyet a rendszer a jelszó-visszaállítási folyamat során használ.|Igen|Nem|Megőrzött, kimenet|
|<sup>2</sup> . strongAuthenticationPhoneNumber|Sztring|A felhasználó elsődleges telefonszáma, amely a többtényezős hitelesítéshez használatos.|Igen|Nem|Megőrzött, kimenet|
|surname         |Sztring|A felhasználó vezetékneve (családi név vagy vezetéknév). Maximális hosszúság 64.|Igen|Yes|Megőrzött, kimenet|
|telephoneNumber (businessPhones első bejegyzése)|Sztring|A felhasználó üzleti helyének elsődleges telefonszáma.|Igen|Nem|Megőrzött, kimenet|
|userPrincipalName    |Sztring|A felhasználó egyszerű felhasználóneve (UPN). Az UPN a felhasználó Internet-stílusú bejelentkezési neve, amely az Internet standard RFC 822. A tartománynak jelen kell lennie a bérlő ellenőrzött tartományok gyűjteményében. Ez a tulajdonság a fiók létrehozásakor szükséges. Nem módosítható.|Nem|Nem|Bemenet, megőrzött, kimenet|
|usageLocation   |Sztring|Olyan felhasználók számára szükséges, akik licenceket kapnak, mert az országok/régiók szolgáltatásainak rendelkezésre állását jogi követelménynek tekintik. Nem üres. Két betűs ország/régió kódja (ISO standard 3166). Példák: "US", "JP" és "GB".|Igen|Nem|Megőrzött, kimenet|
|userType        |Sztring|Karakterlánc-érték, amely a címtárban lévő felhasználói típusok besorolására használható. Az értéknek tagnak kell lennie. Csak olvasható.|Csak olvasás|No|Megőrzött, kimenet|
|userState (externalUserState)<sup>3</sup>|Sztring|Csak az Azure AD B2B-fiók esetében jelzi, hogy a meghívás PendingAcceptance vagy elfogadva van-e.|Nem|Nem|Megőrzött, kimenet|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|DateTime|Megjeleníti a UserState tulajdonság legutóbbi módosításának időbélyegét.|Nem|Nem|Megőrzött, kimenet|

<sup>1 </sup> Microsoft Graph nem támogatja<br><sup>2 </sup> További információ: [MFA telefonszám-attribútum](#mfa-phone-number-attribute)<br><sup>3 </sup> Nem használható a Azure AD B2C

## <a name="display-name-attribute"></a>Megjelenítendő név attribútuma

A a `displayName` felhasználó Azure Portal felhasználói felügyeletében megjelenítendő név, a hozzáférési jogkivonat Azure ad B2C pedig visszaadja az alkalmazásnak. Ezt a tulajdonságot kötelező megadni.

## <a name="identities-attribute"></a>Identitások attribútuma

Az alábbi identitás-típusokhoz lehet hozzárendelni egy felhasználói fiókot, amely lehet fogyasztó, partner vagy polgár:

- **Helyi** identitás – a felhasználónevet és a jelszót a rendszer helyileg tárolja a Azure ad B2C könyvtárban. Gyakran hivatkozunk ezekre az identitásokra "helyi fiókokként".
- **Összevont** identitás – más néven *közösségi* vagy *vállalati* fiók, a felhasználó identitását egy összevont identitás-szolgáltató kezeli, például Facebook, Microsoft, ADFS vagy Salesforce.

Az ügyfél fiókkal rendelkező felhasználók több identitással is bejelentkezhetnek. Ilyen például a Felhasználónév, az e-mail, az alkalmazott azonosítója, a kormányzati azonosító és egyebek. Egyetlen fiók több identitással is rendelkezhet, amelyek helyi és közösségi, ugyanazzal a jelszóval. 

A Microsoft Graph API-ban a helyi és az összevont identitások is a User `identities` attribútumban tárolódnak, amely [objectIdentity](/graph/api/resources/objectidentity)típusú. A `identities` gyűjtemény a felhasználói fiókba való bejelentkezéshez használt identitások készletét jelöli. Ez a gyűjtemény lehetővé teszi, hogy a felhasználó a hozzá tartozó identitásokkal jelentkezzen be a felhasználói fiókba. Az identitások attribútum legfeljebb tíz [objectIdentity](/graph/api/resources/objectidentity) objektumot tartalmazhat. Minden objektum a következő tulajdonságokat tartalmazza:

| Név   | Típus |Leírás|
|:---------------|:--------|:----------|
|signInType|sztring| Megadja a felhasználói bejelentkezési típusokat a címtárban. Helyi fiók esetén:,,,,  `emailAddress` `emailAddress1` `emailAddress2` `emailAddress3`  `userName` vagy bármely más hasonló típus. A közösségi fiókot a következőre kell beállítani:  `federated` .|
|kiállító|sztring|Megadja az identitás kiállítóját. Helyi fiókok esetében (ahol a **signInType** nem `federated` ), ez a tulajdonság a helyi B2C-bérlő alapértelmezett tartománynevét adja meg, például: `contoso.onmicrosoft.com` . A közösségi identitás (ahol  a signInType `federated` ) értéke a kibocsátó neve, például:`facebook.com`|
|issuerAssignedId|sztring|A kiállító által a felhasználóhoz rendelt egyedi azonosítót határozza meg. A **kibocsátó** és a **issuerAssignedId** kombinációjának egyedinek kell lennie a bérlőn belül. Helyi fiók esetén, ha a **signInType** értéke `emailAddress` vagy `userName` , a felhasználó bejelentkezési nevét jelöli.<br>Ha a **signInType** értéke: <ul><li>`emailAddress` (vagy kezdődik a `emailAddress` Like `emailAddress1` ) **issuerAssignedId** érvényes e-mail-címnek kell lennie</li><li>`userName`(vagy bármely más érték) a **issuerAssignedId** egy [e-mail-cím érvényes helyi részének](https://tools.ietf.org/html/rfc3696#section-3) kell lennie</li><li>`federated`, a **issuerAssignedId** az összevont fiók egyedi azonosítóját jelöli.</li></ul>|

A következő **identitások** attribútum egy helyi fiók identitásával, egy bejelentkezési névvel, egy e-mail-címmel és egy közösségi identitással. 

 ```json
 "identities": [
     {
       "signInType": "userName",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "johnsmith"
     },
     {
       "signInType": "emailAddress",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "jsmith@yahoo.com"
     },
     {
       "signInType": "federated",
       "issuer": "facebook.com",
       "issuerAssignedId": "5eecb0cd"
     }
   ]
 ```

Az összevont identitások esetében az azonosítótól függően a **issuerAssignedId** egy adott felhasználó egyedi értéke egy alkalmazás-vagy fejlesztési fiókban. Konfigurálja a Azure AD B2C szabályzatot ugyanazzal az alkalmazás-AZONOSÍTÓval, amelyet korábban a közösségi szolgáltató vagy egy másik alkalmazás rendelt hozzá ugyanazon a fejlesztési fiókon belül. 

## <a name="password-profile-property"></a>Password Profile tulajdonság

Helyi identitás esetén a **passwordProfile** attribútum megadása kötelező, és tartalmazza a felhasználó jelszavát. Az `forceChangePasswordNextSignIn` attribútum azt jelzi, hogy a felhasználónak vissza kell-e állítania a jelszót a következő bejelentkezéskor. A kényszerített jelszó-visszaállítási [folyamat kezeléséhez állítsa be a kényszerített jelszó-visszaállítási folyamatot](force-password-reset.md).

Összevont (közösségi) identitás esetén a **passwordProfile** attribútum nem szükséges.

```json
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

## <a name="password-policy-attribute"></a>Jelszóházirend attribútuma

A Azure AD B2C jelszóházirend (helyi fiókok esetében) a Azure Active Directory [erős jelszó-erősségi](../active-directory/authentication/concept-sspr-policy.md) házirenden alapul. A Azure AD B2C regisztrációs vagy bejelentkezési és jelszó-visszaállítási szabályzatok ezt az erős jelszót igénylik, és nem járnak le a jelszavak.

A felhasználói áttelepítési forgatókönyvek esetében, ha az áttelepíteni kívánt fiókok gyengébb jelszóval rendelkeznek, mint az Azure AD B2C által kényszerített [erős jelszó](../active-directory/authentication/concept-sspr-policy.md) , letilthatja a jelszó erős követelményét. Az alapértelmezett jelszóházirend módosításához állítsa az attribútumot a következőre: `passwordPolicies` `DisableStrongPassword` . A felhasználói kérelem létrehozása például a következőképpen módosítható:

```json
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="mfa-phone-number-attribute"></a>MFA telefonszám-attribútum

A többtényezős hitelesítés (MFA) telefonjának használatakor a rendszer a mobiltelefont használja a felhasználói identitás ellenőrzéséhez. Új telefonszám programozott módon [hozzáadásához](/graph/api/authentication-post-phonemethods) , a telefonszám [frissítéséhez](/graph/api/b2cauthenticationmethodspolicy-update), [lekéréséhez](/graph/api/b2cauthenticationmethodspolicy-get)vagy [törléséhez](/graph/api/phoneauthenticationmethod-delete) használja az MS Graph API [telefonos hitelesítési módszert](/graph/api/resources/phoneauthenticationmethod).

Azure AD B2C [Egyéni házirendekben](custom-policy-overview.md)a telefonszám a `strongAuthenticationPhoneNumber` jogcím típusán keresztül érhető el.

## <a name="extension-attributes"></a>Bővítmény attribútumai

Minden ügyfélre kiterjedő alkalmazás egyedi követelményekkel rendelkezik a gyűjtött adatokhoz. A Azure AD B2C bérlője a tulajdonságok területen tárolt beépített információkkal, például a megadott névvel, a vezetéknévvel és az irányítószámmal rendelkezik. A Azure AD B2C használatával kiterjesztheti az egyes felhasználói fiókokban tárolt tulajdonságok készletét. További információ: [felhasználói attribútumok hozzáadása és felhasználói bevitel testreszabása Azure Active Directory B2C](configure-user-input.md)

A bővítmény attribútumai [kiterjesztik](/graph/extensibility-overview#schema-extensions) a címtárban lévő felhasználói objektumok sémáját. A bővítmény attribútumai csak egy alkalmazás-objektumon regisztrálhatók, annak ellenére, hogy egy felhasználóhoz tartozó adatmennyiséget tartalmaznak. A bővítmény attribútum a nevű alkalmazáshoz van csatolva `b2c-extensions-app` . Ne módosítsa ezt az alkalmazást, mert az Azure AD B2C használja a felhasználói adattároláshoz. Az alkalmazás az Azure Active Directory Alkalmazásregisztrációk alatt található.

> [!NOTE]
> - Akár 100-es kiterjesztési attribútum is írható bármely felhasználói fiókba.
> - Ha a B2C-Extensions-App alkalmazás törölve lett, a rendszer eltávolítja ezeket a bővítmény-attribútumokat az összes felhasználótól a bennük található adatokkal együtt.
> - Ha az alkalmazás töröl egy Extension attribútumot, a rendszer eltávolítja az összes felhasználói fiókból, és törli az értékeket.

A Graph API-bővítmény attribútumai az egyezmény használatával vannak elnevezve `extension_ApplicationClientID_AttributeName` , ahol a a `ApplicationClientID` (z) az alkalmazás **alkalmazás-(ügyfél-) azonosítója** `b2c-extensions-app` (a Azure Portal **Alkalmazásregisztrációk**  >  **összes alkalmazásában** található). Vegye figyelembe, hogy a bővítmény attribútumának nevében szereplő **alkalmazás-azonosító (ügyfél)** nem tartalmaz kötőjelet. Például:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

A következő adattípusok támogatottak a séma-bővítményben lévő attribútumok definiálásakor:

|Típus |Megjegyzések  |
|--------------|---------|
|Logikai    | Lehetséges értékek: **true** vagy **false**. |
|DateTime   | ISO 8601 formátumban kell megadni. A rendszer UTC formátumban tárolja.   |
|Egész szám    | 32 bites érték.               |
|Sztring     | 256 karakter maximális száma.     |

## <a name="next-steps"></a>Következő lépések

További információ a bővítmény attribútumairól:

- [Sémakiterjesztések](/graph/extensibility-overview#schema-extensions)
- [Egyéni attribútumok meghatározása](user-flow-custom-attributes.md)
