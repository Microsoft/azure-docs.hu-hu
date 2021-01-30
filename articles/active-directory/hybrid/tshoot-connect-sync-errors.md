---
title: 'Azure AD Connect: hibák elhárítása szinkronizálás közben | Microsoft Docs'
description: Ez a cikk azt ismerteti, hogyan lehet elhárítani a Azure AD Connect használatával végzett szinkronizálás során fellépő hibákat.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 2209d5ce-0a64-447b-be3a-6f06d47995f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88fda4ec810d0b410dcd75ac9c6be69bd54b16d9
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092649"
---
# <a name="troubleshooting-errors-during-synchronization"></a>Hibaelhárítási hibák a szinkronizálás során
Hibák merülhetnek fel, ha a rendszer a Windows Server Active Directory (AD DS) identitási adatait szinkronizálja a Azure Active Directory (Azure AD) szolgáltatásba. Ez a cikk áttekintést nyújt a különböző típusú szinkronizálási hibákról, a hibákat okozó lehetséges forgatókönyvekről és a hibák kijavításának lehetséges módjairól. Ez a cikk a gyakori hibákat tartalmazza, és nem feltétlenül fedi le az összes lehetséges hibát.

 Ez a cikk azt feltételezi, hogy az olvasó ismeri az [Azure ad és a Azure ad Connect alapjául szolgáló tervezési fogalmakat](plan-connect-design-concepts.md).

A Azure AD Connect augusztus 2016-es vagy újabb verziójának legújabb verziójával a \( \) szinkronizálási hibákról szóló jelentés a Azure ad Connect Health szinkronizálásának részeként érhető el a [Azure Portal](https://aka.ms/aadconnecthealth) .

2016. szeptember 1-től a [Azure Active Directory duplikált attribútum rugalmassági](how-to-connect-syncservice-duplicate-attribute-resiliency.md) funkciója alapértelmezés szerint engedélyezve lesz az összes *új* Azure Active Directory-bérlő esetében. A szolgáltatás a következő hónapokban automatikusan engedélyezve lesz a meglévő bérlők számára.

A Azure AD Connect három típusú műveletet hajt végre a szinkronizált könyvtárakban: importálás, szinkronizálás és exportálás. A hibák az összes művelet során elhelyezhetők. Ez a cikk elsősorban az Azure AD-ba való exportálás során felmerülő hibákra koncentrál.

## <a name="errors-during-export-to-azure-ad"></a>Hibák az Azure AD-ba való exportálás során
A következő szakasz az Azure ad-összekötő használatával az exportálási művelet során fellépő különböző típusú szinkronizálási hibákat ismerteti. Ezt az összekötőt a "contoso" néven lehet azonosítani. *onmicrosoft.com*".
Az Azure AD-ba való exportálás során felmerülő hibák azt jelzik, hogy a művelet a \( Azure Active Directory nem tudta felvenni, frissíteni, törölni stb. \) kísérletet tett Azure ad Connect \( szinkronizálási motor \) .

![Exportálási hibák áttekintése](./media/tshoot-connect-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Az adateltérési hibák
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Description
* Ha Azure AD Connect \( szinkronizáló motor \) arra utasítja a Azure Active Directory, hogy objektumokat adjon hozzá vagy frissít, az Azure ad a **sourceAnchor** attribútumot használja a bejövő objektumnak az Azure ad-beli objektumok **immutableId** attribútumának használatával. Ezt a megfeleltetést **nehéz egyezésnek** nevezzük.
* Ha az Azure AD nem **talál** olyan objektumot, amely megfelel a **immutableId** attribútumnak a bejövő objektum **sourceAnchor** attribútumával, akkor az új objektum kiépítés előtt vissza kell térnie a ProxyAddresses és a userPrincipalName attribútumokra, hogy megtalálja a megfelelőt. Ezt a megfeleltetést **lágy egyezésnek** nevezzük. A puha egyezés úgy van kialakítva, hogy megfeleljen az Azure AD-ben már meglévő objektumoknak (amelyek az Azure AD-ben vannak kiadva) a szinkronizálás során hozzáadott/frissített objektumok, amelyek ugyanazt az entitást (felhasználókat, csoportokat) képviselik a helyszínen.
* **InvalidSoftMatch** hiba történik, ha a rögzített egyezés nem talál egyező objektumot **, és** a Soft Match megkeresi a megfelelő objektumot, de az objektum eltérő *immutableId* rendelkezik, mint a bejövő objektum *SourceAnchor*, ami arra utal, hogy a megfelelő objektumot szinkronizálták a helyszíni Active Directory egy másik objektumával.

Vagyis ahhoz, hogy a Soft Match működjön, a nem megfelelő értéknek kell lennie a *immutableId*. Ha bármely olyan objektum, amelynél *immutableId* van beállítva, akkor a művelet InvalidSoftMatch szinkronizációs hibát eredményezhet, ha nem felel meg a rögzített feltételeknek.

Azure Active Directory séma nem teszi lehetővé két vagy több objektum számára a következő attribútumok azonos értékét. \(Ez nem kimerítő lista.\)

* ProxyAddresses
* UserPrincipalName
* onPremisesSecurityIdentifier
* ObjectId

> [!NOTE]
> Az [Azure ad attribútum duplikált attribútumának rugalmassági](how-to-connect-syncservice-duplicate-attribute-resiliency.md) funkcióját a Azure Active Directory alapértelmezett viselkedése is kivezeti.  Ez csökkenti a Azure AD Connect (és más szinkronizálási ügyfelek) által látott szinkronizálási hibák számát azáltal, hogy az Azure AD-t rugalmasabban kezeli a helyszíni AD-környezetekben található duplikált ProxyAddresses és UserPrincipalName attribútumok kezelése során. Ez a funkció nem javítja az ismétlődési hibákat. Így továbbra is rögzíteni kell az adatmennyiséget. Lehetővé teszi azonban az olyan új objektumok kiépített kihelyezését, amelyek egyébként le vannak tiltva az Azure AD-ben duplikált értékek miatt. Ez a művelet csökkenti a szinkronizációs ügyfélnek visszaküldött szinkronizálási hibák számát is.
> Ha ez a funkció engedélyezve van a bérlő számára, nem fogja látni az új objektumok kiépítés során észlelt InvalidSoftMatch szinkronizálási hibákat.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Példák a InvalidSoftMatch
1. Két vagy több, a ProxyAddresses attribútummal megegyező értékkel rendelkező objektum létezik a helyszíni Active Directoryban. Csak egy van kiépítve az Azure AD-ben.
2. Két vagy több, a userPrincipalName attribútummal azonos értékkel rendelkező objektum létezik a helyszíni Active Directoryban. Csak egy van kiépítve az Azure AD-ben.
3. Egy objektum lett hozzáadva a helyszíni Active Directory a ProxyAddresses attribútummal megegyező értékkel, mint a Azure Active Directory egy meglévő objektuma. A helyszínen hozzáadott objektum nem lesz kiépítve a Azure Active Directoryban.
4. Egy objektum lett hozzáadva a helyszíni Active Directory a userPrincipalName attribútummal megegyező értékkel, mint a Azure Active Directory-fiókban. Az objektum nem lesz kiépítve a Azure Active Directoryban.
5. Egy szinkronizált fiók lett áthelyezve az A erdőből a B erdőbe. Azure AD Connect (szinkronizáló motor) a ObjectGUID attribútum használatával számítja ki a SourceAnchor. Az erdő áthelyezése után a SourceAnchor értéke eltér. Az új objektum (a B erdőből) nem tud szinkronizálni az Azure AD-ben meglévő objektummal.
6. Egy szinkronizált objektum véletlenül törölve lett a helyszíni Active Directoryból, és egy új objektum lett létrehozva a Active Directoryban ugyanahhoz az entitáshoz (például a felhasználóhoz), a fiók törlése nélkül Azure Active Directory. Az új fiók nem tud szinkronizálni a meglévő Azure AD-objektummal.
7. A Azure AD Connect el lett távolítva és újratelepítette. Az újratelepítés során egy másik attribútum lett kiválasztva SourceAnchor. A korábban szinkronizált összes objektum leállt a InvalidSoftMatch hibával való szinkronizálással.

#### <a name="example-case"></a>Példa:
1. **Bob Smith** egy szinkronizált felhasználó Azure Active Directory a helyszíni Active Directory *contoso.com*
2. Bob Smith **userPrincipalName** a **bobot \@ contoso.com** van beállítva.
3. a **"abcdefghijklmnopqrstuv = ="** a Azure ad Connect által kiszámított **SourceAnchor** , amelyet Bob Smith **ObjectGUID** használ a helyszíni Active Directory, amely a Azure Active Directoryban található Bob Smith **immutableId** .
4. Bob a következő értékeket is tartalmazta a **ProxyAddresses** attribútumhoz:
   * SMTP- bobs@contoso.com
   * SMTP- bob.smith@contoso.com
   * **SMTP: Bob \@ contoso.com**
5. Egy új felhasználó, **Bob Taylor**, hozzá lesz adva a helyszíni Active Directoryhoz.
6. Bob Taylor **userPrincipalName** **bobt \@ contoso.com** van beállítva.
7. a **"abcdefghijkl0123456789 = ="** "a Azure ad Connect által kiszámított **SourceAnchor** , amelyet Bob Taylor **ObjectGUID** használ a helyszíni Active Directory. Bob Taylor objektuma még nem lett szinkronizálva Azure Active Directory.
8. A Bob Taylor a következő értékeket tartalmazta a proxyAddresses attribútumhoz:
   * SMTP- bobt@contoso.com
   * SMTP- bob.taylor@contoso.com
   * **SMTP: Bob \@ contoso.com**
9. A szinkronizálás során Azure AD Connect felismeri a Bob Taylor hozzáadását a helyszíni Active Directory, és megkéri az Azure AD-t, hogy végezze el ugyanezt a módosítást.
10. Az Azure AD először a rögzített egyezést fogja végrehajtani. Ez azt eredményezi, hogy megkeresi, hogy van-e olyan objektum, amelynek immutableId egyenlő a "abcdefghijkl0123456789 = =" értékkel. A rögzített egyezés sikertelen lesz, mert az Azure AD-ben nincs más objektum a immutableId.
11. Az Azure AD ezután megkísérli a Soft-Match Bob Taylor-egyeztetést. Ez azt eredményezi, hogy megkeresi, hogy van-e olyan objektum, amely proxyAddresses egyenlő a három értékkel, beleértve az SMTP-t: bob@contoso.com
12. Az Azure AD megkeresi a Bob Smith objektumát, hogy megfeleljen a Soft-Match feltételnek. Ez az objektum azonban a immutableId = "abcdefghijklmnopqrstuv = =" értékkel rendelkezik. Ez azt jelzi, hogy ez az objektum a helyszíni Active Directory egy másik objektumáról lett szinkronizálva. Az Azure AD ezért nem képes az ilyen objektumok **InvalidSoftMatch** , és egy szinkronizálási hibát eredményez.

#### <a name="how-to-fix-invalidsoftmatch-error"></a>InvalidSoftMatch-hiba elhárítása
A InvalidSoftMatch hibájának leggyakoribb oka, hogy két, eltérő SourceAnchor immutableId objektum \( \) értéke megegyezik a ProxyAddresses és/vagy a userPrincipalName attribútumokkal, amelyeket az Azure ad-beli Soft-Match folyamat során használ a rendszer. Az érvénytelen lágy egyezés kijavításához

1. Azonosítsa a hibát okozó duplikált proxyAddresses, userPrincipalName vagy más attribútumérték értékét. Határozza meg azt is \( , hogy mely két vagy több \) objektum vesz részt az ütközésben. A Azure AD Connect Health által a [szinkronizáláshoz](./how-to-connect-health-sync.md) létrehozott jelentés segít azonosítani a két objektumot.
2. Azonosítsa, hogy az objektumnak továbbra is legyen a duplikált értéke, és hogy melyik objektum ne legyen.
3. Távolítsa el a duplikált értéket abból az objektumból, amely nem rendelkezhet ezzel az értékkel. Végezze el a módosítást abban a könyvtárban, ahol az objektum forrása származik. Bizonyos esetekben előfordulhat, hogy törölnie kell az egyik objektumot az ütközésben.
4. Ha a helyszíni AD-ben végzett módosítást hajtotta végre, Azure AD Connect szinkronizálja a változást.

A szinkronizálási hibajelentések Azure AD Connect Healthon belüli szinkronizálása 30 percenként frissül, és tartalmazza a legutóbbi szinkronizálási kísérlet hibáit.

> [!NOTE]
> A ImmutableId definíció szerint nem változhatnak az objektum élettartamában. Ha Azure AD Connect nem a fenti listán szereplő forgatókönyvekkel lett konfigurálva, akkor előfordulhat, hogy a Azure AD Connect kiszámítja a SourceAnchor egy másik értékét, amely ugyanazt az entitást jelöli (ugyanaz a felhasználó/csoport/kapcsolattartó, stb.), amely egy meglévő Azure AD-objektummal rendelkezik, amelyet továbbra is használni kíván.
>
>

#### <a name="related-articles"></a>Kapcsolódó cikkek
* [Ismétlődő vagy érvénytelen attribútumok megakadályozzák a címtár-szinkronizálást Microsoft 365](https://support.microsoft.com/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Description
Ha az Azure AD két objektumra kísérli meg a műveletet, lehetséges, hogy a különböző "objektumtípus" (például a felhasználó, a csoport, a kapcsolattartó stb.) két objektumának ugyanazokkal az értékekkel kell rendelkeznie a puha egyezés végrehajtásához használt attribútumoknál. Mivel az attribútumok ismétlődése nem engedélyezett az Azure AD-ben, a művelet "ObjectTypeMismatch" szinkronizálási hibát eredményezhet.

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Példa ObjectTypeMismatch-hiba esetére
* A rendszer létrehoz egy levelezésre alkalmas biztonsági csoportot Microsoft 365. A rendszergazda új felhasználót vagy kapcsolattartót ad hozzá a helyszíni AD-ben (ez még nincs szinkronizálva az Azure AD-vel), és ugyanazzal az értékkel rendelkezik a ProxyAddresses attribútumhoz, mint a Microsoft 365 csoport.

#### <a name="example-case"></a>Példa esetre
1. A rendszergazda létrehoz egy új, e-mailek használatára képes biztonsági csoportot az adórendszer Microsoft 365jában, és e-mail-címet biztosít tax@contoso.com . Ez a csoport a ProxyAddresses attribútum értékét rendeli hozzá az **SMTP: tax \@ contoso.com**
2. Egy új felhasználó csatlakozik a Contoso.com-hez, és létrehoz egy fiókot a felhasználó számára a proxyAddress SMTP-ként **: tax \@ contoso.com**
3. Ha Azure AD Connect szinkronizálja az új felhasználói fiókot, az "ObjectTypeMismatch" hibaüzenetet kapja.

#### <a name="how-to-fix-objecttypemismatch-error"></a>ObjectTypeMismatch-hiba elhárítása
A ObjectTypeMismatch hibájának leggyakoribb oka, hogy a különböző típusú (felhasználó, csoport, kapcsolattartó stb.) objektumoknak ugyanaz az értéke a ProxyAddresses attribútumhoz. A ObjectTypeMismatch kijavítása érdekében:

1. Azonosítsa a hibát okozó duplikált proxyAddresses (vagy más attribútum) értékét. Határozza meg azt is \( , hogy mely két vagy több \) objektum vesz részt az ütközésben. A Azure AD Connect Health által a [szinkronizáláshoz](./how-to-connect-health-sync.md) létrehozott jelentés segít azonosítani a két objektumot.
2. Azonosítsa, hogy az objektumnak továbbra is legyen a duplikált értéke, és hogy melyik objektum ne legyen.
3. Távolítsa el a duplikált értéket abból az objektumból, amely nem rendelkezhet ezzel az értékkel. Vegye figyelembe, hogy a módosítást arra a könyvtárba kell tenni, amelyben az objektum forrása származik. Bizonyos esetekben előfordulhat, hogy törölnie kell az egyik objektumot az ütközésben.
4. Ha a helyszíni AD-ben végzett módosítást hajtotta végre, Azure AD Connect szinkronizálja a változást. A szinkronizálási hibajelentés Azure AD Connect Healthn belüli szinkronizálása 30 percenként frissül, és a legutóbbi szinkronizálási kísérlet során fellépő hibákat is tartalmazza.

## <a name="duplicate-attributes"></a>Ismétlődő attribútumok
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>Description
Azure Active Directory séma nem teszi lehetővé két vagy több objektum számára a következő attribútumok azonos értékét. Az Azure AD-ben minden objektumnak egyedi értékkel kell rendelkeznie ezeknek az attribútumoknak egy adott példánynál.

* ProxyAddresses
* UserPrincipalName

Ha Azure AD Connect új objektum hozzáadását vagy egy meglévő objektum frissítését kísérli meg a fenti attribútumokhoz, amelyek már hozzá vannak rendelve a Azure Active Directory egy másik objektumához, a művelet "AttributeValueMustBeUnique" szinkronizálási hibát eredményez.

#### <a name="possible-scenarios"></a>Lehetséges forgatókönyvek:
1. Ismétlődő érték van hozzárendelve egy már szinkronizált objektumhoz, amely ütközik egy másik szinkronizált objektummal.

#### <a name="example-case"></a>Példa:
1. **Bob Smith** egy szinkronizált felhasználó Azure Active Directory a helyszíni Active Directory contoso.com
2. Bob Smith **userPrincipalName** a helyszínen **bobot \@ contoso.com** van beállítva.
3. Bob a következő értékeket is tartalmazta a **ProxyAddresses** attribútumhoz:
   * SMTP- bobs@contoso.com
   * SMTP- bob.smith@contoso.com
   * **SMTP: Bob \@ contoso.com**
4. Egy új felhasználó, **Bob Taylor**, hozzá lesz adva a helyszíni Active Directoryhoz.
5. Bob Taylor **userPrincipalName** **bobt \@ contoso.com** van beállítva.
6. **Bob Taylor** a következő értékekkel rendelkezik a **ProxyAddresses** attribútumhoz: i. SMTP: bobt@contoso.com II. SMTP- bob.taylor@contoso.com
7. A Bob Taylor objektuma sikeresen szinkronizálva van az Azure AD-vel.
8. A rendszergazda úgy döntött, hogy Bob Taylor **ProxyAddresses** attribútumát a következő értékkel frissítette: i. **SMTP: Bob \@ contoso.com**
9. Az Azure AD megpróbálja frissíteni Bob Taylor objektumát az Azure AD-ben a fenti értékkel, de a művelet sikertelen lesz, mivel a ProxyAddresses érték már hozzá van rendelve Bob Smith-hez, ami "AttributeValueMustBeUnique" hibát eredményez.

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>AttributeValueMustBeUnique-hiba elhárítása
A AttributeValueMustBeUnique hibájának leggyakoribb oka, hogy a \( \) ProxyAddresses és/vagy userPrincipalName attribútumok esetében a két objektumnak ugyanaz a értéke, mint a különböző SourceAnchor-immutableId. AttributeValueMustBeUnique-hiba javítása érdekében

1. Azonosítsa a hibát okozó duplikált proxyAddresses, userPrincipalName vagy más attribútumérték értékét. Határozza meg azt is \( , hogy mely két vagy több \) objektum vesz részt az ütközésben. A Azure AD Connect Health által a [szinkronizáláshoz](./how-to-connect-health-sync.md) létrehozott jelentés segít azonosítani a két objektumot.
2. Azonosítsa, hogy az objektumnak továbbra is legyen a duplikált értéke, és hogy melyik objektum ne legyen.
3. Távolítsa el a duplikált értéket abból az objektumból, amely nem rendelkezhet ezzel az értékkel. Vegye figyelembe, hogy a módosítást arra a könyvtárba kell tenni, amelyben az objektum forrása származik. Bizonyos esetekben előfordulhat, hogy törölnie kell az egyik objektumot az ütközésben.
4. Ha a helyszíni AD-ben végzett módosítást hajtotta végre, Azure AD Connect szinkronizálni a hiba változását, hogy kijavítva legyen.

#### <a name="related-articles"></a>Kapcsolódó cikkek
-[Ismétlődő vagy érvénytelen attribútumok megakadályozzák a címtár-szinkronizálást Microsoft 365](https://support.microsoft.com/kb/2647098)

## <a name="data-validation-failures"></a>Adatérvényesítési hibák
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>Description
Azure Active Directory a különböző korlátozásokat kényszeríti az adatvédelemre, mielőtt engedélyezi, hogy a rendszer beírja az adott adatlemezt a könyvtárba. Ezek a korlátozások biztosítják, hogy a végfelhasználók a lehető legjobb élményt nyújtsanak az adatoktól függő alkalmazások használatakor.

#### <a name="scenarios"></a>Forgatókönyvek
a. A UserPrincipalName attribútum értéke érvénytelen/nem támogatott karaktereket tartalmaz.
b. A UserPrincipalName attribútum nem követi a szükséges formátumot.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>IdentityDataValidationFailed-hiba elhárítása
a. Győződjön meg arról, hogy a userPrincipalName attribútumnak támogatott karakterek és a szükséges formátuma van.

#### <a name="related-articles"></a>Kapcsolódó cikkek
* [Felkészülés a felhasználók címtár-szinkronizálással való kiépítésére Microsoft 365](https://support.office.com/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>FederatedDomainChangeError
#### <a name="description"></a>Description
Ez az eset **"FederatedDomainChangeError"** szinkronizálási hibát eredményez, ha egy felhasználó userPrincipalName utótagja egy összevont tartományból egy másik összevont tartományba módosul.

#### <a name="scenarios"></a>Forgatókönyvek
Szinkronizált felhasználó esetén a UserPrincipalName utótagot az egyik összevont tartományból egy másik összevont tartományra módosították a helyszínen. A *userPrincipalName = bob \@ contoso.com* például a következőre változott: *userPrincipalName = Bob \@ fabrikam.com*.

#### <a name="example"></a>Példa
1. Bob Smith, a Contoso.com fiókja új felhasználóként lesz hozzáadva Active Directory a UserPrincipalName bob@contoso.com
2. Bob a Fabrikam.com nevű másik Contoso.com kerül át, és a UserPrincipalName a következőre módosult bob@fabrikam.com
3. A contoso.com és a fabrikam.com tartományok összevont tartományok Azure Active Directorykal.
4. Bob userPrincipalName nem frissül, és "FederatedDomainChangeError" szinkronizálási hibát eredményez.

#### <a name="how-to-fix"></a>A hiba kijavítása
Ha a felhasználó UserPrincipalName-utótagjának frissítése bob@**contoso.com** -ből Bob \@ **fabrikam.com**-ra történt, ahol a **contoso.com** és a **fabrikam.com** is **összevont tartományok**, akkor a szinkronizálási hiba elhárításához kövesse az alábbi lépéseket.

1. Frissítse a felhasználó UserPrincipalName az Azure AD-ból bob@contoso.com a verzióra bob@contoso.onmicrosoft.com . Az Azure AD PowerShell-modullal a következő PowerShell-parancs használható: `Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. A szinkronizálás megkezdéséhez engedélyezze a következő szinkronizálási ciklust. Az idő szinkronizálása sikeres lesz, és a UserPrincipalName a várt módon frissíti a Bob-t bob@fabrikam.com .

#### <a name="related-articles"></a>Kapcsolódó cikkek
* [A Azure Active Directory Sync Tool nem szinkronizálja a módosításokat, miután a felhasználói fiók egyszerű felhasználónevét más összevont tartomány használatára változtatta](/azure/active-directory/hybrid/howto-troubleshoot-upn-changes)

## <a name="largeobject"></a>LargeObject
### <a name="description"></a>Description
Ha egy attribútum túllépi a megengedett mérethatárt, a hosszúsági korlátot vagy a Azure Active Directory sémában beállított korlátot, akkor a szinkronizálási művelet a **LargeObject** vagy a **ExceededAllowedLength** szinkronizálási hibát eredményezi. Ez a hiba általában a következő attribútumok esetén fordul elő

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>Lehetséges forgatókönyvek
1. Bob userCertificate attribútuma túl sok olyan tanúsítványt tárol, amely hozzá van rendelve a Bob-hoz. Ilyenek lehetnek a régebbi, lejárt tanúsítványok. A rögzített korlát 15 tanúsítvány. A LargeObject hibák userCertificate attribútummal való kezelésével kapcsolatos további információkért tekintse meg a [userCertificate attribútum által okozott LargeObject hibák kezelése](tshoot-connect-largeobjecterror-usercertificate.md)című cikket.
2. Bob userSMIMECertificate attribútuma túl sok olyan tanúsítványt tárol, amely hozzá van rendelve a Bob-hoz. Ilyenek lehetnek a régebbi, lejárt tanúsítványok. A rögzített korlát 15 tanúsítvány.
3. A Active Directory Bob thumbnailPhoto-készlete túl nagy az Azure AD-ben való szinkronizáláshoz.
4. A Active Directory ProxyAddresses attribútumának automatikus populációja során az objektum túl sok ProxyAddresses van hozzárendelve.

### <a name="how-to-fix"></a>A hiba kijavítása
1. Győződjön meg arról, hogy a hibát okozó attribútum a megengedett korlátozáson belül van.

## <a name="existing-admin-role-conflict"></a>Meglévő rendszergazdai szerepkör ütközik

### <a name="description"></a>Description
Egy **meglévő rendszergazdai szerepkör ütközik** egy felhasználói objektumon a szinkronizálás során, amikor a felhasználói objektum:

- rendszergazdai engedélyek és
- a meglévő Azure AD-objektummal megegyező UserPrincipalName

A Azure AD Connect nem lehet a helyszíni AD-ből származó felhasználói objektumhoz tartozó, az Azure AD-ben egy olyan felhasználói objektummal, amely rendszergazdai szerepkörrel rendelkezik.  További információ: [Azure ad userPrincipalName-populáció](plan-connect-userprincipalname.md)

![Meglévő rendszergazda](media/tshoot-connect-sync-errors/existingadmin.png)


### <a name="how-to-fix"></a>A hiba kijavítása
A probléma megoldásához tegye a következőket:

1. Távolítsa el az Azure AD-fiókot (tulajdonos) az összes rendszergazdai szerepkörből. 
2. **Törölje** a karanténba helyezett objektumot a felhőben. 
3. A következő szinkronizálási ciklus gondoskodik a helyszíni felhasználó Felhőbeli fiókkal való zökkenőmentes megfeleltetéséről (mivel a Felhőbeli felhasználó már nem globális GA). 
4. Állítsa vissza a tulajdonos szerepkör-tagságait. 

>[!NOTE]
>A rendszergazdai szerepkört a meglévő felhasználói objektumhoz is hozzárendelheti, miután a helyi felhasználói objektum és az Azure AD felhasználói objektum közötti lágy egyezés befejeződött.

## <a name="related-links"></a>Kapcsolódó hivatkozások
* [Active Directory objektumok megkeresése Active Directory felügyeleti központ](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560661(v=ws.10))
* [Azure Active Directory lekérdezése egy objektumhoz Azure Active Directory PowerShell használatával](/previous-versions/azure/jj151815(v=azure.100))