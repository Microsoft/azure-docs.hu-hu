---
title: A dinamikusan feltöltött csoportok tagságának szabályai – Azure AD | Microsoft Docs
description: Tagsági szabályok létrehozása a csoportok automatikus feltöltéséhez és a szabályra vonatkozó hivatkozások létrehozásához.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: overview
ms.date: 02/18/2021
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c25504e3313234ac6b6f80a6e00c77fce28b1400
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102174529"
---
# <a name="dynamic-membership-rules-for-groups-in-azure-active-directory"></a>A Azure Active Directory csoportok dinamikus tagsági szabályai

Az Azure Active Directory (Azure AD) szolgáltatásban létrehozhat összetett attribútum-alapú szabályokat a csoportok dinamikus tagságának engedélyezéséhez. A dinamikus csoporttagság csökkenti a felhasználók hozzáadásának és eltávolításának adminisztratív terheit. Ez a cikk részletesen ismerteti a tulajdonságokat és a szintaxist, hogy dinamikus tagsági szabályokat hozzon létre a felhasználók és az eszközök számára. Beállíthat egy szabályt a dinamikus tagsághoz a biztonsági csoportokban vagy Microsoft 365 csoportokban.

Ha a felhasználó vagy az eszköz bármely attribútuma megváltozik, a rendszer kiértékeli az összes dinamikus csoportosítási szabályt egy adott könyvtárban, hogy megtekintse, hogy a módosítás aktiválja-e az összes csoportot Hozzáadás vagy eltávolítás. Ha egy felhasználó vagy eszköz megfelel egy adott csoportra vonatkozó szabálynak, akkor a csoport tagjaként adja hozzá őket. Ha már nem felelnek meg a szabálynak, azok el lesznek távolítva. Nem lehet manuálisan hozzáadni vagy eltávolítani egy dinamikus csoport tagjait.

- Létrehozhat egy dinamikus csoportot eszközökhöz vagy felhasználókhoz, de nem hozhat létre olyan szabályt, amely a felhasználókat és az eszközöket is tartalmazza.
- Nem hozható létre eszközcsoport az eszköz tulajdonosai attribútumai alapján. Az eszközök tagsági szabályai csak az eszköz attribútumait hivatkozhatják.

> [!NOTE]
> Ehhez a szolgáltatáshoz egy prémium szintű Azure AD P1 licenc szükséges minden olyan egyedi felhasználóhoz, amely egy vagy több dinamikus csoport tagja. Nem kell licenceket hozzárendelni a felhasználókhoz, hogy azok a dinamikus csoportok tagjai legyenek, de az Azure AD-szervezetben minimális számú licenccel kell rendelkeznie az összes ilyen felhasználó lefedéséhez. Ha például összesen 1 000 egyedi felhasználó szerepelt a szervezet összes dinamikus csoportjában, akkor legalább 1 000 licenccel kell rendelkeznie a prémium szintű Azure AD P1-hez, hogy megfeleljenek a licenc követelményeinek.
> Nem szükséges licenc a dinamikus eszközcsoport tagjaihoz tartozó eszközökhöz.

## <a name="rule-builder-in-the-azure-portal"></a>Szabálykészlet a Azure Portalban

Az Azure AD egy olyan szabályt biztosít, amellyel gyorsabban hozhat létre és frissíthet fontos szabályokat. A szabály-szerkesztő legfeljebb öt kifejezés kiépítését támogatja. A szabály-szerkesztő megkönnyíti néhány egyszerű kifejezéssel rendelkező szabály kiépítését, azonban nem használható minden szabály újbóli létrehozásához. Ha a szabály-szerkesztő nem támogatja a létrehozni kívánt szabályt, használhatja a szövegmezőt.

Íme néhány példa a speciális szabályokra vagy szintaxisra, amelyekhez ajánlott a következő szövegmezővel létrehozni:

- Szabály ötnél több kifejezéssel
- A közvetlen jelentések szabálya
- [Operátor prioritásának](#operator-precedence) beállítása
- [Összetett kifejezésekkel rendelkező szabályok](#rules-with-complex-expressions); például: `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Előfordulhat, hogy a szabály-szerkesztő nem tudja megjeleníteni a szövegmezőben létrehozott egyes szabályokat. Előfordulhat, hogy egy üzenet jelenik meg, ha a szabály-szerkesztő nem tudja megjeleníteni a szabályt. A szabály-szerkesztő semmilyen módon nem módosítja a dinamikus csoport szabályainak támogatott szintaxisát, érvényesítését vagy feldolgozását.

További részletes útmutatásért lásd: [dinamikus csoport létrehozása vagy frissítése](groups-create-rule.md).

![Tagsági szabály hozzáadása dinamikus csoporthoz](./media/groups-dynamic-membership/update-dynamic-group-rule.png)

### <a name="rule-syntax-for-a-single-expression"></a>Egyetlen kifejezés szabályának szintaxisa

Egyetlen kifejezés a tagsági szabály legegyszerűbb formája, és csak a fent említett három részből áll. Az egyetlen kifejezéssel rendelkező szabály a következőhöz hasonlóan néz ki: `Property Operator Value` , ahol a tulajdonság szintaxisa a Object. Property neve.

A következő példa egy megfelelően felépített tagsági szabályt mutat be egyetlen kifejezéssel:

```
user.department -eq "Sales"
```

A zárójelek egyetlen kifejezés esetében nem kötelezőek. A tagsági szabály törzsének teljes hossza nem haladhatja meg a 2048 karaktert.

## <a name="constructing-the-body-of-a-membership-rule"></a>Tagsági szabály törzsének összeállítása

Olyan tagsági szabály, amely automatikusan feltölt egy csoportot a felhasználók vagy az eszközök számára egy bináris kifejezés, amely igaz vagy hamis eredményt eredményez. Egy egyszerű szabály három része:

- Tulajdonság
- Operátor
- Érték

Egy kifejezésen belüli részek sorrendje fontos a szintaktikai hibák elkerülése érdekében.

## <a name="supported-properties"></a>Támogatott tulajdonságok

A tagsági szabályok létrehozásához háromféle tulajdonságot lehet használni.

- Logikai érték
- Sztring
- Karakterlánc-gyűjtemény

A következő felhasználói tulajdonságokat használhatja egyetlen kifejezés létrehozásához.

### <a name="properties-of-type-boolean"></a>Logikai típusú tulajdonságok

| Tulajdonságok | Megengedett értékek | Használat |
| --- | --- | --- |
| accountEnabled |Igaz hamis |User. accountEnabled – EQ True |
| dirSyncEnabled |Igaz hamis |User. dirSyncEnabled – EQ True |

### <a name="properties-of-type-string"></a>Karakterlánc típusú tulajdonságok

| Tulajdonságok | Megengedett értékek | Használat |
| --- | --- | --- |
| city |Bármilyen sztring vagy *Null* érték |(User. City-EQ "value") |
| ország |Bármilyen sztring vagy *Null* érték |(User. Country-EQ "value") |
| companyName | Bármilyen sztring vagy *Null* érték | (User. cégnév – EQ "érték") |
| Részleg |Bármilyen sztring vagy *Null* érték |(User. Department-EQ "value") |
| displayName |Bármely karakterlánc-érték |(User. displayName-EQ "value") |
| Alkalmazottkód |Bármely karakterlánc-érték |(User. Alkalmazottkód-EQ "value")<br>(User. Alkalmazottkód-ne *Null*) |
| Érték facsimiletelephonenumber |Bármilyen sztring vagy *Null* érték |(User. érték facsimiletelephonenumber-EQ "value") |
| givenName |Bármilyen sztring vagy *Null* érték |(User. givenName-EQ "value") |
| jobTitle |Bármilyen sztring vagy *Null* érték |(User. beosztás-EQ "value") |
| Levelezés |Bármely karakterlánc-érték vagy *Null* (a felhasználó SMTP-címe) |(User. mail-EQ "érték") |
| mailNickName |Bármely karakterlánc-érték (a felhasználó levelezési aliasa) |(User. mailNickName-EQ "value") |
| mobil |Bármilyen sztring vagy *Null* érték |(User. Mobile-EQ "value") |
| objectId |A felhasználói objektum GUID azonosítója |(User. objectId-EQ "11111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | Helyszíni biztonsági azonosító (SID) azon felhasználók számára, akik a helyszínről a felhőbe szinkronizálva lettek. |(User. onPremisesSecurityIdentifier-EQ "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |Nincs DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(User. passwordPolicies-EQ "DisableStrongPassword") |
| physicalDeliveryOfficeName |Bármilyen sztring vagy *Null* érték |(User. physicalDeliveryOfficeName-EQ "value") |
| postalCode |Bármilyen sztring vagy *Null* érték |(User. irányítószám – EQ "érték") |
| preferredLanguage |ISO 639-1 kód |(User. preferredLanguage – EQ "en-US") |
| sipProxyAddress |Bármilyen sztring vagy *Null* érték |(User. sipProxyAddress-EQ "value") |
| állapot |Bármilyen sztring vagy *Null* érték |(User. State-EQ "value") |
| streetAddress |Bármilyen sztring vagy *Null* érték |(User. streetAddress-EQ "value") |
| surname |Bármilyen sztring vagy *Null* érték |(User. vezetéknév – EQ "érték") |
| telephoneNumber |Bármilyen sztring vagy *Null* érték |(User. telephoneNumber-EQ "value") |
| usageLocation |Két betűs ország/régió kódja |(User. usageLocation – EQ "US") |
| userPrincipalName |Bármely karakterlánc-érték |(User. userPrincipalName-EQ " alias@domain ") |
| userType |tag vendég *Null* |(User. userType-EQ "tag") |

### <a name="properties-of-type-string-collection"></a>String típusú gyűjtemény tulajdonságai

| Tulajdonságok | Megengedett értékek | Használat |
| --- | --- | --- |
| otherMails |Bármely karakterlánc-érték |(User. otherMails – a következőt tartalmazza: " alias@domain ") |
| proxyAddresses |SMTP: alias@domain SMTP: alias@domain |(User. proxyAddresses – az "SMTP: alias@domain " érték szerepel) |

Az eszköz szabályaihoz használt tulajdonságokért lásd: [eszközök szabályai](#rules-for-devices).

## <a name="supported-expression-operators"></a>Támogatott kifejezések operátorai

A következő táblázat felsorolja az összes támogatott operátort és azok szintaxisát egyetlen kifejezéshez. A operátorok kötőjel (-) előtaggal vagy anélkül is használhatók.

| Operátor | Syntax |
| --- | --- |
| Nem egyenlő |– ne |
| Egyenlő |– EQ |
| Nem kezdődik |-notStartsWith |
| Kezdete |– startsWith |
| Nem tartalmazza |-notContains |
| Contains |– tartalmazza |
| Nem egyezik |-notMatch |
| Match |– egyezés |
| In | – a |
| Nem a | -notIn |

### <a name="using-the--in-and--notin-operators"></a>A-in és a-notIn operátorok használata

Ha egy felhasználói attribútum értékét össze szeretné hasonlítani számos különböző értékkel, használhatja a-in vagy a-notIn operátort. Az értékek listájának megkezdéséhez és befejezéséhez használja a "[" és "]" zárójel szimbólumokat.

 A következő példában a kifejezés igaz értéket ad vissza, ha a User. Department értéke a listában szereplő értékek valamelyikének felel meg:

```
   user.department -in ["50001","50002","50003","50005","50006","50007","50008","50016","50020","50024","50038","50039","51100"]
```


### <a name="using-the--match-operator"></a>A-Match operátor használata 
A **-Match** operátor a reguláris kifejezések egyeztetésére szolgál. Angol nyelvű Példák:

```
user.displayName -match "Da.*"   
```
Da, Dav, David kiértékeli az igaz értéket, az aDa hamis értéket ad vissza.

```
user.displayName -match ".*vid"
```
David kiértékeli az igaz értéket, a DA hamis értékre értékeli.

## <a name="supported-values"></a>Támogatott értékek

Egy kifejezésben használt értékek többféle típusból állhatnak, többek között:

* Sztringek
* Boolean – igaz, hamis
* Számok
* Tömbök – szám tömb, karakterlánc-tömb

Ha egy kifejezésen belül értéket ad meg, fontos, hogy a hibák elkerülése érdekében a megfelelő szintaxist használja. Néhány szintaktikai Tipp:

* Idézőjelek megadása nem kötelező, kivéve, ha az érték karakterlánc.
* A karakterlánc és a regex művelet nem megkülönbözteti a kis-és nagybetűket.
* Ha egy sztring dupla idézőjelet tartalmaz, mindkét idézőjelet el kell kerülni a \` karakterrel, például a felhasználóval. részleg-EQ \` "Sales" \` a megfelelő szintaxis, ha az "értékesítés" az érték.
* NULL értékű ellenőrzéseket is végrehajthat, ha például null értéket használ `user.department -eq null` .

### <a name="use-of-null-values"></a>Null értékek használata

Ha NULL értéket szeretne megadni egy szabályban, használhatja a *Null* értéket. 

* A kifejezésben szereplő *Null* érték összehasonlításakor használja a-EQ vagy a-ne értéket.
* Csak akkor használjon *nullát* idézőjelek közé, ha azt szeretné, hogy literális karakterlánc-értékként legyen értelmezve.
* A-not operátor nem használható a null értékhez tartozó összehasonlító operátorként. Ha ezt használja, hibaüzenet jelenik meg, hogy null vagy $null használ-e.

A NULL értékre való hivatkozás helyes módja a következő:

```
   user.mail –ne null
```

## <a name="rules-with-multiple-expressions"></a>Több kifejezéssel rendelkező szabályok

A csoporttagság-szabály több, a-és,-vagy, és nem logikai operátorral összekapcsolt kifejezésből állhat. A logikai operátorok kombinálva is használhatók. 

A következő példák a több kifejezéssel rendelkező, megfelelően kiépített tagsági szabályokra mutatnak:

```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```

### <a name="operator-precedence"></a>Operátorok műveleti sorrendje

Az összes operátor az alábbi sorrendben jelenik meg, a legmagasabbtól a legalacsonyabb értékig. Az azonos sorban lévő operátorok egyenlő prioritással rendelkeznek:

```
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
-not
-and
-or
-any -all
```

Az alábbi példa az operátori prioritást szemlélteti, amelyben két kifejezés kerül kiértékelésre a felhasználó számára:

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

A zárójelek csak akkor szükségesek, ha a prioritás nem felel meg a követelményeknek. Ha például azt szeretné, hogy a részleg elsőként legyen kiértékelve, az alábbi ábrán látható, hogyan használhatók a sorrendek a sorrendben:

```
   user.country –eq "US" –and (user.department –eq "Marketing" –or user.department –eq "Sales")
```

## <a name="rules-with-complex-expressions"></a>Összetett kifejezésekkel rendelkező szabályok

A tagsági szabályok olyan összetett kifejezésekből állhatnak, amelyekben a tulajdonságok, a kezelők és az értékek összetettebb űrlapokat foglalnak magukban. A kifejezések akkor tekinthetők összetettnek, ha a következők bármelyike igaz:

* A tulajdonság értékek gyűjteményét tartalmazza; pontosabban a többértékű tulajdonságok
* A kifejezések a-any és az-All operátort használják
* A kifejezés értéke lehet egy vagy több kifejezés is.

## <a name="multi-value-properties"></a>Többszörös érték tulajdonságai

A többértékű tulajdonságok azonos típusú objektumok gyűjteményei. Felhasználhatók tagsági szabályok létrehozására a-any és az-All logikai operátorok használatával.

| Tulajdonságok | Értékek | Használat |
| --- | --- | --- |
| assignedPlans | A gyűjtemény minden objektuma a következő karakterlánc-tulajdonságokat teszi elérhetővé: capabilityStatus, Service, servicePlanId |User. assignedPlans – any (assignedPlan. servicePlanId-EQ "efb87545-963c-4e0d-99df-69c6916d9eb0"-és assignedPlan. capabilityStatus-EQ "engedélyezve") |
| proxyAddresses| SMTP: alias@domain SMTP: alias@domain | (User. proxyAddresses-any ( \_ -tartalmazza a "contoso")) |

### <a name="using-the--any-and--all-operators"></a>A-any és az-All operátorok használata

A-any és az-All operátor használatával feltételt alkalmazhat a gyűjtemény egyik vagy az összes elemére.

* -any (akkor teljesül, ha a gyűjtemény legalább egy eleme megfelel a feltételnek)
* -All (akkor teljesül, ha a gyűjtemény összes eleme megfelel a feltételnek)

#### <a name="example-1"></a>1\. példa

a assignedPlans egy többértékű tulajdonság, amely felsorolja a felhasználóhoz rendelt összes szervizcsomagot. A következő kifejezés kiválasztja azokat a felhasználókat, akik a (z) Exchange Online (2. csomag) szolgáltatáscsomag (GUID-érték) esetében is engedélyezve vannak:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

Az ilyen szabályok például az összes olyan felhasználó csoportosítására használhatók, akik számára engedélyezve van egy Microsoft 365 (vagy más Microsoft Online Service) képesség. Ezután alkalmazhat szabályzatokat a csoportra.

#### <a name="example-2"></a>2\. példa

A következő kifejezés kiválasztja azokat a felhasználókat, akik az Intune szolgáltatáshoz társított szolgáltatási csomaggal rendelkeznek (a "SCO" szolgáltatásnév azonosítja):

```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

#### <a name="example-3"></a>3\. példa

A következő kifejezés kiválasztja az összes olyan felhasználót, aki nem rendelkezik asigned-csomaggal:

```
user.assignedPlans -all (assignedPlan.servicePlanId -eq "")
```

### <a name="using-the-underscore-_-syntax"></a>Az aláhúzásjel ( \_ ) szintaxis használata

Az aláhúzás ( \_ ) szintaxis a többértékű karakterláncok gyűjteményének egyik tulajdonságában lévő adott érték előfordulásait hasonlítja a felhasználók vagy eszközök dinamikus csoportba való felvételéhez. Ez a-any vagy az-All operátorral együtt használatos.

Íme egy példa arra, hogy az aláhúzásjel ( \_ ) egy szabály használatával adja hozzá a tagokat a User. proxyAddress alapján (ez a felhasználó. otherMails esetében is működik). Ez a szabály bármely olyan felhasználót felvenni a csoportba, amely a "contoso" kifejezést tartalmazó proxy-címekkel rendelkezik.

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="other-properties-and-common-rules"></a>Egyéb tulajdonságok és közös szabályok

### <a name="create-a-direct-reports-rule"></a>"Közvetlen jelentések" szabály létrehozása

Létrehozhat egy olyan csoportot, amely tartalmazza a felettesek összes közvetlen jelentését. Ha a felettes közvetlen jelentései a jövőben változnak, a csoport tagsága automatikusan módosul.

A közvetlen jelentési szabály a következő szintaxissal hozható létre:

```
Direct Reports for "{objectID_of_manager}"
```

Az alábbi példa egy érvényes szabályt mutat be, ahol a "62e19b97-8b3d-4d4a-A106-4ce66896a863" a felettes objectID:

```
Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```

A következő tippek segíthetnek a szabály megfelelő használatához.

- A **kezelő azonosítója** a felettes objektumazonosító. A kezelő **profiljában** található.
- A szabály működéséhez győződjön meg arról, hogy a **felettes** tulajdonság helyesen van beállítva a szervezet felhasználói számára. A felhasználó **profiljában** megtekintheti az aktuális értéket.
- Ez a szabály csak a felettes közvetlen jelentéseit támogatja. Más szóval nem hozhat létre csoportot a felettes közvetlen jelentéseivel *és* jelentéseivel.
- Ez a szabály nem kombinálható más tagsági szabályokkal.

### <a name="create-an-all-users-rule"></a>"Minden felhasználó" szabály létrehozása

Létrehozhat egy olyan csoportot, amely a szervezeten belüli összes felhasználót egy tagsági szabály használatával hozza létre. Ha a későbbiekben a felhasználók hozzáadása vagy eltávolítása történik a szervezetből, a rendszer automatikusan módosítja a csoport tagságát.

A "minden felhasználó" szabályt egyetlen kifejezéssel kell kiépíteni a-ne operátor és a Null érték használatával. Ez a szabály VÁLLALATKÖZI vendég felhasználókat, valamint a csoport felhasználóit adja hozzá.

```
user.objectId -ne null
```
Ha azt szeretné, hogy a csoport kizárjon vendég felhasználókat, és csak a szervezet tagjait tartalmazza, a következő szintaxist használhatja:

```
(user.objectId -ne null) -and (user.userType -eq "Member")
```

### <a name="create-an-all-devices-rule"></a>"Minden eszköz" szabály létrehozása

Létrehozhat egy olyan csoportot, amely a szervezeten belüli összes eszközt egy tagsági szabály használatával hozza létre. Ha az eszközöket a későbbiekben hozzáadja vagy eltávolítja a szervezetből, a rendszer automatikusan módosítja a csoport tagságát.

A "minden eszköz" szabályt egyetlen kifejezéssel kell kiépíteni a-ne operátor és a Null érték használatával:

```
device.objectId -ne null
```

## <a name="extension-properties-and-custom-extension-properties"></a>Bővítmény tulajdonságai és egyéni bővítmény tulajdonságai

A bővítmény attribútumai és az egyéni bővítmény tulajdonságai a dinamikus tagsági szabályokban karakterlánc-tulajdonságokként használhatók. A [bővítmény attribútumai](/graph/api/resources/onpremisesextensionattributes) a helyszíni Windows Server ad-ből szinkronizálhatók, és a "ExtensionAttributeX" formátumot használják, ahol az X egyenlő 1-15. Íme egy példa egy olyan szabályra, amely egy Extension attribútumot használ tulajdonságként:

```
(user.extensionAttribute15 -eq "Marketing")
```

Az [Egyéni bővítmények tulajdonságai](../hybrid/how-to-connect-sync-feature-directory-extensions.md) szinkronizálva vannak a helyszíni Windows Server ad-ből vagy egy csatlakoztatott SaaS-alkalmazásból, és a következő formátumban vannak `user.extension_[GUID]_[Attribute]` :

* A [GUID] az Azure AD-ben egyedi azonosító azon alkalmazás számára, amely létrehozta a tulajdonságot az Azure AD-ben
* A (z) [Attribute] a létrehozott tulajdonság neve.

Egy egyéni kiterjesztési tulajdonságot használó szabály például a következőket használja:

```
user.extension_c272a57b722d4eb29bfe327874ae79cb_OfficeNumber -eq "123"
```

Az egyéni tulajdonságnév megtalálhatók a címtárban úgy, hogy a Graph Explorerben lekérdezi a felhasználó tulajdonságát, és megkeresi a tulajdonság nevét. Mostantól az **Egyéni bővítmény tulajdonságainak beolvasása** hivatkozásra kattintva is megadhat egy egyedi alkalmazás-azonosítót, és megkaphatja a dinamikus tagsági szabályok létrehozásakor használni kívánt egyéni bővítmény-tulajdonságok teljes listáját. Ez a lista az alkalmazás új egyéni bővítmény-tulajdonságainak beolvasására is frissíthető.

## <a name="rules-for-devices"></a>Eszközök szabályai

Létrehozhat egy olyan szabályt is, amely kijelöli a csoport tagjainak eszköz-objektumait. A felhasználók és az eszközök nem rendelkezhetnek csoport tagjaiként. 

> [!NOTE]
> A **organizationalUnit** attribútum már nem jelenik meg, és nem használható. Ezt a karakterláncot az Intune meghatározott esetekben állítja be, de az Azure AD nem ismeri fel, így az attribútum alapján egyetlen eszköz sincs hozzá a csoportokhoz.

> [!NOTE]
> a systemlabels egy írásvédett attribútum, amely nem állítható be az Intune-nal.
>
> A Windows 10 esetében a deviceOSVersion attribútum megfelelő formátuma a következő: (Device. deviceOSVersion-EQ "10.0.17763"). A formázás ellenőrizhető a Get-MsolDevice PowerShell-parancsmaggal.

A következő eszköz-attribútumok használhatók.

 Eszköz attribútuma  | Értékek | Példa
 ----- | ----- | ----------------
 accountEnabled | Igaz hamis | (Device. accountEnabled-EQ true)
 displayName | bármely karakterlánc-érték |(Device. displayName-EQ "Rob iPhone")
 deviceOSType | bármely karakterlánc-érték | (Device. deviceOSType-EQ "iPad") – vagy (Device. deviceOSType-EQ "iPhone")<br>(Device. deviceOSType – a "AndroidEnterprise" kifejezést tartalmazza)<br>(Device. deviceOSType-EQ "AndroidForWork")<br>(Device. deviceOSType-EQ "Windows")
 deviceOSVersion | bármely karakterlánc-érték | (Device. deviceOSVersion-EQ "9,1")<br>(Device. deviceOSVersion-EQ "10.0.17763.0")
 deviceCategory | egy érvényes eszköznév neve | (Device. deviceCategory-EQ "BYOD")
 deviceManufacturer | bármely karakterlánc-érték | (Device. deviceManufacturer-EQ "Samsung")
 deviceModel | bármely karakterlánc-érték | (Device. deviceModel-EQ "iPad Air")
 deviceOwnership | Személyes, vállalati, ismeretlen | (Device. deviceOwnership-EQ "vállalat")
 enrollmentProfileName | Apple-eszköz beléptetési profil neve, Android vállalati vállalati tulajdonú dedikált eszköz beléptetési profil neve vagy a Windows Autopilot-profil neve | (Device. enrollmentProfileName-EQ "DEP iPhones")
 isRooted | Igaz hamis | (Device. isRooted-EQ true)
 managementType | MDM (mobileszközök esetében)<br>SZÁMÍTÓGÉP (az Intune PC Agent által felügyelt számítógépek esetén) | (Device. managementType-EQ "MDM")
 deviceId | érvényes Azure AD-eszköz azonosítója | (Device. deviceId-EQ "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | érvényes Azure AD-objektumazonosító |  (Device. objectId-EQ "76ad43c9-32c5-45E8-A272-7b58b58f596d")
 devicePhysicalIds | az Autopilot által használt bármely karakterláncérték, például az összes Autopilot-eszköz, a Rendeléskód vagy a PurchaseOrderID  | (Device. devicePhysicalIDs – any _ – tartalmazza a következőt: "[ZTDId]") (Device. devicePhysicalIds – any _-EQ "[Rendeléskód]: 179887111881") (Device. devicePhysicalIds-any _-EQ "[PurchaseOrderId]: 76222342342")
 systemLabels | minden olyan karakterlánc, amely megfelel az Intune Device tulajdonságának a modern munkahelyi eszközök címkézéséhez | (device.systemLabels – a "M365Managed" kifejezést tartalmazza)

> [!Note]  
> Ahhoz, hogy a deviceOwnership dinamikus csoportokat hozzon létre az eszközökhöz, a "vállalat" értékkel egyenlő értéket kell megadnia. Az Intune-ban az eszköz tulajdonjoga a céges helyet képviseli. További részletekért tekintse meg a [OwnerTypes](/intune/reports-ref-devices#ownertypes) . 

## <a name="next-steps"></a>Következő lépések

Ezek a cikkek további információkat nyújtanak Azure Active Directory csoportjairól.

- [Meglévő csoportok megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Új csoport létrehozása és tagok hozzáadása](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Csoportbeállítások kezelése](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Csoporttagságok kezelése](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [A csoportban lévő felhasználók dinamikus szabályainak kezelése](groups-create-rule.md)
