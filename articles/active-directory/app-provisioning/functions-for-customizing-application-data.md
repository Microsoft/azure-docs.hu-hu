---
title: Az attribútum-hozzárendelésekhez tartozó kifejezések írásának referenciája Azure Active Directory
description: Megtudhatja, hogyan alakíthatja át a kifejezés-hozzárendeléseket az attribútumok értékének elfogadható formátumba való átalakításához az SaaS-alkalmazások objektumainak automatikus kiépítés során Azure Active Directoryban. A függvények hivatkozási listáját tartalmazza.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: kenwith
ms.custom: contperf-fy21q2
ms.openlocfilehash: 0334f52b87071c8f363a0dfcc793170316747096
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198506"
---
# <a name="reference-for-writing-expressions-for-attribute-mappings-in-azure-ad"></a>Az Azure AD-beli attribútum-hozzárendelésekhez tartozó kifejezések írásához való hivatkozás

Ha egy SaaS-alkalmazásra konfigurálja az üzembe helyezést, a megadható attribútumok egyike egy kifejezés-hozzárendelés. Ilyen esetben olyan parancsfájl-szerű kifejezést kell írnia, amely lehetővé teszi, hogy a felhasználói adatait olyan formátumokba alakítsa át, amelyek az SaaS-alkalmazás számára elfogadhatóak.

## <a name="syntax-overview"></a>Szintaxis áttekintése

Az attribútum-hozzárendelések kifejezések szintaxisa Visual Basic for Applications (VBA) függvények emlékeztetője.

* A teljes kifejezést a functions kifejezésben kell definiálni, amely egy, a zárójelben szereplő argumentumokat, valamint a *függvénynév ( `<<argument 1>>` , `<<argument N>>` )* függvényeket tartalmazza.
* A függvények egymásba ágyazhatók. Például:  *FunctionOne (FunctionTwo ( `<<argument1>>` ))*
* A függvények három különböző típusú argumentumot adhat át:
  
  1. Attribútumok, amelyeket szögletes zárójelbe kell foglalni. Például: [attributeName]
  2. Karakterlánc-konstansok, amelyek idézőjelek közé kell, hogy legyenek. Például: "Egyesült Államok"
  3. Egyéb függvények. Például: FunctionOne ( `<<argument1>>` , FunctionTwo ( `<<argument2>>` ))
* Karakterlánc-konstansok esetén, ha a karakterláncban a fordított perjel (\) vagy az idézőjel (") értékre van szüksége, akkor azt a fordított perjel (\) szimbólummal kell megadnia. Például: "cég neve: \\ " contoso \\ ""
* A szintaxis megkülönbözteti a kis-és nagybetűket, és a rendszer a karakterláncként írja be őket egy függvénybe, és másolja be őket közvetlenül innen. 

## <a name="list-of-functions"></a>Függvények listája

[](#append) &nbsp; &nbsp; Hozzáfűzés &nbsp; &nbsp; [](#bitand) &nbsp; &nbsp; BitAnd &nbsp; &nbsp; [](#cbool) &nbsp; &nbsp; CBool &nbsp; &nbsp; [](#coalesce) &nbsp; &nbsp; Egyesítés &nbsp; &nbsp; [](#converttobase64) &nbsp; &nbsp; ConvertToBase64 &nbsp; &nbsp; [](#converttoutf8hex) &nbsp; &nbsp; ConvertToUTF8Hex &nbsp; &nbsp; [](#count) &nbsp; &nbsp; Darabszám &nbsp; &nbsp; [](#cstr) &nbsp; &nbsp; CStr &nbsp; &nbsp; [DateFromNum](#datefromnum) &nbsp; [](#formatdatetime) &nbsp; &nbsp; FormatDateTime &nbsp; &nbsp; [GUID](#guid) &nbsp; &nbsp; azonosító &nbsp; &nbsp; [](#iif) &nbsp; &nbsp; IIf &nbsp; &nbsp; A bejelentkező [](#instr) &nbsp; &nbsp; &nbsp; &nbsp; [](#isnull) &nbsp; &nbsp; IsNull &nbsp; &nbsp; [](#isnullorempty) &nbsp; &nbsp; IsNullOrEmpty &nbsp; &nbsp; [](#ispresent) &nbsp; &nbsp; IsPresent &nbsp; &nbsp; [](#isstring) &nbsp; &nbsp; IsString &nbsp; &nbsp; [](#item) &nbsp; &nbsp; Elemek &nbsp; &nbsp; [](#join) &nbsp; &nbsp; Csatlakozás &nbsp; &nbsp; [Bal](#left) &nbsp; &nbsp; oldali &nbsp; &nbsp; [Mid](#mid) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [NormalizeDiacritics](#normalizediacritics) [not](#not) &nbsp; &nbsp; &nbsp; &nbsp; [NumFromDate](#numfromdate) &nbsp; &nbsp; &nbsp; &nbsp; [RemoveDuplicates](#removeduplicates) &nbsp; &nbsp; &nbsp; &nbsp; [replace](#replace) &nbsp; &nbsp; &nbsp; &nbsp; [SelectUniqueValue](#selectuniquevalue) &nbsp; &nbsp; &nbsp; &nbsp; [SingleAppRoleAssignment](#singleapproleassignment) &nbsp; &nbsp; &nbsp; &nbsp; [Split](#split) &nbsp; &nbsp; &nbsp; &nbsp; [StripSpaces](#stripspaces) &nbsp; &nbsp; &nbsp; &nbsp; [switch](#switch) &nbsp; &nbsp; &nbsp; &nbsp; [ToLower](#tolower) &nbsp; &nbsp; &nbsp; &nbsp; [ToUpper](#toupper) &nbsp; &nbsp; &nbsp; &nbsp; [Word](#word)

---
### <a name="append"></a>Append (Hozzáfűzés)

**Függvény:** Hozzáfűzés (forrás, utótag)

**Leírás:** A forrás sztring értéket veszi fel, és hozzáfűzi az utótagot a végéhez.

**Paraméterek**

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **forrás** |Kötelező |Sztring |Az attribútum neve általában a forrásoldali objektumban. |
| **utótag** |Kötelező |Sztring |A forrás érték végéhez hozzáfűzni kívánt karakterlánc. |


### <a name="append-constant-suffix-to-user-name"></a>Állandó utótag hozzáfűzése a felhasználónévhez
Példa: Ha Salesforce-homokozót használ, előfordulhat, hogy a szinkronizálás előtt hozzá kell fűzni egy további utótagot az összes felhasználónevehöz.

**Kifejezés** 
`Append([userPrincipalName], ".test")`

**Minta bemenet/kimenet:** 

* **Bemenet**: (userPrincipalName): " John.Doe@contoso.com "
* **Kimenet**: " John.Doe@contoso.com.test "


---
### <a name="bitand"></a>BitAnd
**Függvény:** BitAnd (érték1, érték2)

**Leírás:** Ez a függvény mindkét paramétert a bináris ábrázolásra konvertálja, és egy kis mennyiséget állít be a következőre:

- 0 – Ha a érték1-ben és a érték2-ben található megfelelő bitek közül egyet vagy mindkettőt 0
- 1 – Ha a megfelelő bitek mindegyike 1.

Más szóval a 0 értéket adja vissza minden esetben, kivéve, ha mindkét paraméternek megfelelő bitek értéke 1.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **érték1** |Kötelező |num |Numerikus érték, amelynek AND'ed kell lennie a érték2|
| **érték2** |Kötelező |num |Numerikus érték, amelynek AND'ed kell lennie a érték1|

**Például**
`BitAnd(&HF, &HF7)`

11110111 és 00000111 = 00000111 tehát `BitAnd` a 7 értéket adja vissza, az 00000111 bináris értékét.

---
### <a name="cbool"></a>CBool
**Függvény** 
`CBool(Expression)`

**Leírás:**  
 `CBool` egy logikai értéket ad vissza a kiértékelt kifejezés alapján. Ha a kifejezés kiértékelése nullától eltérő értékre történik, akkor az `CBool` *igaz* értéket adja vissza, máskülönben *hamis* értéket ad vissza.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **expression** |Kötelező | kifejezés | Bármely érvényes kifejezés |

**Például**
`CBool([attribute1] = [attribute2])`                                                                    
Igaz értéket ad vissza, ha mindkét attribútum ugyanazzal az értékkel rendelkezik.

---
### <a name="coalesce"></a>Coalesce
**Függvény:** Egyesítés (source1, source2,..., defaultValue)

**Leírás:** Az első olyan értéket adja vissza, amely nem NULL. Ha az összes argumentum értéke NULL, és a defaultValue szerepel, a rendszer a defaultValue értéket adja vissza. Ha az összes argumentum NULL értékű, és a defaultValue értéke nem létezik, az egyesítés NULL értéket ad vissza.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **source1 ... sourceN** | Kötelező | Sztring |Kötelező, változó számú alkalommal. Az attribútum neve általában a forrásoldali objektumban. |
| **defaultValue** | Választható | Sztring | Az alapértelmezett érték, amelyet akkor kell használni, ha az összes forrás értéke NULL. Üres karakterlánc ("") lehet.

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>A flow e-mail-értéke, ha nem NULL értékű, máskülönben flow userPrincipalName
Példa: ha jelen van, a mail attribútumot szeretné átvezetni. Ha nem, akkor inkább a userPrincipalName értékét kell átvennie.

**Kifejezés** 
`Coalesce([mail],[userPrincipalName])`

**Minta bemenet/kimenet:** 

* **Bemenet** (e-mail): NULL
* **Bemenet** (userPrincipalName): " John.Doe@contoso.com "
* **Kimenet**: " John.Doe@contoso.com "


---
### <a name="converttobase64"></a>ConvertToBase64
**Függvény:** ConvertToBase64 (forrás)

**Leírás:** A ConvertToBase64 függvény egy karakterláncot Unicode Base64 karakterlánccá alakít át.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **forrás** |Kötelező |Sztring |Az alap 64-re konvertálandó karakterlánc|

**Például**
`ConvertToBase64("Hello world!")`

A "SABlAGwAbABvACAAdwBvAHIAbABkACEA" értéket adja vissza

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Függvény:** ConvertToUTF8Hex (forrás)

**Leírás:** A ConvertToUTF8Hex függvény egy karakterláncot UTF8 hexadecimális kódolású értékre alakít át.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **forrás** |Kötelező |Sztring |Az UTF8 hexadecimális értékre konvertálandó karakterlánc|

**Például**
`ConvertToUTF8Hex("Hello world!")`

48656C6C6F20776F726C6421 visszaadása

---
### <a name="count"></a>Darabszám
**Függvény:** Darabszám (attribútum)

**Leírás:** A Count függvény egy többértékű attribútum elemeinek számát adja vissza.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **attribútum** |Kötelező |attribute |Többértékű attribútum, amely megszámolja az elemeket|

---
### <a name="cstr"></a>CStr
**Függvény:** CStr (érték)

**Leírás:** A CStr függvény egy értéket karakterlánc típusú adattípusra konvertál.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **value** |Kötelező | numerikus, hivatkozási vagy logikai | Számérték, hivatkozási attribútum vagy logikai érték lehet. |

**Például**
`CStr([dn])`

A "CN = Joe, DC = contoso, DC = com" értéket adja vissza.

---
### <a name="datefromnum"></a>DateFromNum
**Függvény:** DateFromNum (érték)

**Leírás:** A DateFromNum függvény egy értéket AD meg az AD dátumformátum dátum és idő típusára.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **value** |Kötelező | Date | A DateTime típusúra konvertálandó AD-dátum |

**Például**
`DateFromNum([lastLogonTimestamp])`

`DateFromNum(129699324000000000)`

Egy DateTime értéket ad vissza, amely 2012 január 1-től 11:00-kor.

---
### <a name="formatdatetime"></a>FormatDateTime
**Függvény:** FormatDateTime (forrás, DateTimeStyles paraméter, inputFormat, outputFormat)

**Leírás:** Egy dátum sztringet vesz fel az egyik formátumból, és átalakítja azt más formátumra.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **forrás** |Kötelező |Sztring |Az attribútum neve általában a forrásoldali objektumban. |
| **DateTimeStyles paraméter** | Választható | Sztring | Ezzel a beállítással megadhatja a formázási beállításokat, amelyekkel testre szabhatja a karakterlánc-elemzést a dátum-és időelemzési módszerekhez. A támogatott értékekért lásd: [DateTimeStyles paraméter doc](/dotnet/api/system.globalization.datetimestyles). Ha üresen hagyja, a rendszer az alapértelmezett értéket használja DateTimeStyles paraméter. RoundtripKind, DateTimeStyles paraméter. AllowLeadingWhite, DateTimeStyles paraméter. AllowTrailingWhite  |
| **inputFormat** |Kötelező |Sztring |A forrás értékének várt formátuma. Támogatott formátumok: [/DotNet/standard/Base-types/Custom-Date-and-Time-Format-Strings](/dotnet/standard/base-types/custom-date-and-time-format-strings). |
| **outputFormat** |Kötelező |Sztring |A kimeneti dátum formátuma. |



### <a name="output-date-as-a-string-in-a-certain-format"></a>Kimeneti dátum karakterláncként egy adott formátumban
Példa: szeretné elküldeni a dátumokat egy SaaS-alkalmazásba, például a ServiceNow egy bizonyos formátumban. A következő kifejezést érdemes használni. 

**Kifejezés** 

`FormatDateTime([extensionAttribute1], , "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Minta bemenet/kimenet:**

* **Bemenet** (extensionAttribute1): "20150123105347.1 z"
* **Kimenet**: "2015-01-23"


---
### <a name="guid"></a>Guid
**Függvény:** GUID ()

**Leírás:** A függvény GUID-azonosítója új véletlenszerű GUID azonosítót hoz létre

---
### <a name="iif"></a>IIF
**Függvény:** IIF (feltétel, valueIfTrue, valueIfFalse)

**Leírás:** Az IIF függvény a lehetséges értékek egy halmazát adja vissza egy megadott feltétel alapján.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **feltétel** |Kötelező |Változó vagy kifejezés |Bármely érték vagy kifejezés, amelynek kiértékelése igaz vagy hamis lehet. |
| **valueIfTrue** |Kötelező |Változó vagy karakterlánc | Ha a feltétel igaz értéket ad vissza, a visszaadott érték. |
| **valueIfFalse** |Kötelező |Változó vagy karakterlánc |Ha a feltétel hamis értéket ad vissza, a visszaadott érték.|

**Például**
`IIF([country]="USA",[country],[department])`

---
### <a name="instr"></a>InStr
**Függvény:** Érték1 (érték2, Start, compareType)

**Leírás:** A beosztási függvény megkeresi egy karakterláncban szereplő alsztring első előfordulását.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **érték1** |Kötelező |Sztring |Keresendő karakterlánc |
| **érték2** |Kötelező |Sztring |A megtalálni kívánt karakterlánc |
| **Start** |Választható |Egész szám |A kiindulási pozíció a karakterlánc megkereséséhez|
| **compareType** |Választható |Enumeráció |VbTextCompare vagy vbBinaryCompare lehet |

**Például**
`InStr("The quick brown fox","quick")`

Értékelés 5

`InStr("repEated","e",3,vbBinaryCompare)`

Értékelés 7-re

---
### <a name="isnull"></a>IsNull
**Függvény:** IsNull (kifejezés)

**Leírás:** Ha a kifejezés értéke null, akkor a IsNull függvény Igaz értéket ad vissza. Attribútum esetén a null értéket az attribútum hiánya fejezi ki.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **expression** |Kötelező |kifejezés |Kiértékelni kívánt kifejezés |

**Például**
`IsNull([displayName])`

Igaz értéket ad vissza, ha az attribútum nincs jelen.

---
### <a name="isnullorempty"></a>IsNullorEmpty
**Függvény:** IsNullOrEmpty (kifejezés)

**Leírás:** Ha a kifejezés null értékű vagy üres karakterlánc, akkor a IsNullOrEmpty függvény Igaz értéket ad vissza. Egy attribútum esetében ez igaz értékre értékeli, ha az attribútum hiányzik vagy létezik, de egy üres karakterlánc.
A függvény inverzének neve IsPresent.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **expression** |Kötelező |kifejezés |Kiértékelni kívánt kifejezés |

**Például**
`IsNullOrEmpty([displayName])`

Igaz értéket ad vissza, ha az attribútum nincs jelen, vagy üres karakterlánc.

---
### <a name="ispresent"></a>IsPresent
**Függvény:** IsPresent (kifejezés)

**Leírás:** Ha a kifejezés olyan karakterláncot ad vissza, amely nem null értékű, és nem üres, akkor a IsPresent függvény Igaz értéket ad vissza. A függvény inverzének neve IsNullOrEmpty.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **expression** |Kötelező |kifejezés |Kiértékelni kívánt kifejezés |

**Például**
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="isstring"></a>IsString
**Függvény:** IsString (kifejezés)

**Leírás:** Ha a kifejezés kiértékelhető karakterlánc típusúra, akkor a IsString függvény Igaz értéket ad vissza.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **expression** |Kötelező |kifejezés |Kiértékelni kívánt kifejezés |

---
### <a name="item"></a>Elem
**Függvény:** Elem (attribútum, index)

**Leírás:** Az Item függvény egy elemet ad vissza egy többértékű karakterlánc/attribútumból.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **attribútum** |Kötelező |Attribútum |Keresendő többértékű attribútum |
| **index** |Kötelező |Egész szám | Index a Többértékű karakterlánc egyik elemébe|

**Példa:** 
 `Item([proxyAddresses], 1)` a többértékű attribútum második tételét adja vissza.

---
### <a name="join"></a>Csatlakozás
**Függvény:** Join (elválasztó, source1, source2,...)

**Leírás:** A JOIN () hasonló a hozzáfűzéshez (), azzal a kivétellel, hogy több **forrás** sztringet is egyesít egyetlen karakterláncban, és az egyes értékeket **elválasztó sztring választja** el egymástól.

Ha a forrásadatok egyike egy többértékű attribútum, akkor az adott attribútum minden értéke együtt lesz egyesítve, az elválasztó értékkel elválasztva.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **elválasztó** |Kötelező |Sztring |A forrásadatok elválasztására szolgáló karakterlánc, amely egyetlen sztringbe van fűzve. Lehet "", ha nem kötelező elválasztó. |
| **source1 ... sourceN** |Kötelező, változó – ennyiszer |Sztring |A egyesíteni kívánt karakterlánc-értékek. |

---
### <a name="left"></a>Bal
**Függvény:** Left (karakterlánc, NumChars)

**Leírás:** A Left függvény egy karakterlánctól balra megadott számú karaktert ad vissza. Ha a numChars = 0, üres karakterláncot ad vissza.
Ha a numChars < 0 értéket, adja vissza a bemeneti karakterláncot.
Ha a karakterlánc null értékű, üres karakterláncot ad vissza.
Ha a sztring kevesebb karaktert tartalmaz a numChars megadott számnál, akkor a rendszer a karakterlánctal megegyező karakterláncot (azaz az 1. paraméterben szereplő összes karaktert tartalmazza) adja vissza.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **Sztring** |Kötelező |Attribútum | A karakterlánc, amelyből karaktereket kell visszaadni |
| **NumChars** |Kötelező |Egész szám | Egy szám, amely a sztring elejétől (balra) való visszatéréshez szükséges karakterek számát azonosítja.|

**Például**
`Left("John Doe", 3)`

A "Joh" értéket adja vissza.

---
### <a name="mid"></a>Közepes
**Függvény:** Mid (forrás, Kezdés, hossz)

**Leírás:** A forrás értékének egy alsztringjét adja vissza. Az alsztringek olyan karakterláncok, amelyek csak néhány karaktert tartalmaznak a forrás sztringből.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **forrás** |Kötelező |Sztring |Az attribútum neve általában. |
| **Start** |Kötelező |egész szám |Az index a **forrás** sztringben, ahol az alsztringnek el kell indulnia. A karakterlánc első karakterének indexe 1, a második karakter pedig a 2. indexet fogja tartalmazni. |
| **length** (hossz) |Kötelező |egész szám |Az alsztring hossza. Ha a hossz a **forrás** sztringen kívülre végződik, a függvény a **kezdő** indexből származó alsztringet ad vissza a **forrás** sztring végéig. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Függvény:** NormalizeDiacritics (forrás)

**Leírás:** Egy karakterlánc-argumentumot igényel. A karakterláncot adja vissza, de bármilyen diakritikus karakterrel egyenértékű, nem diakritikus karakterrel helyettesíthető. Jellemzően a különböző felhasználói azonosítókban, például egyszerű felhasználónevek, SAM-fiókok és e-mail-címekben használható, mellékjeleket és vezetékneveket tartalmazó nevek és vezetéknévek átalakítására szolgálnak.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **forrás** |Kötelező |Sztring | Általában utónév vagy vezetéknév attribútum. |


### <a name="remove-diacritics-from-a-string"></a>Mellékjelek eltávolítása egy sztringből
Példa: az ékezetes jeleket tartalmazó karaktereket olyan karakterekkel kell helyettesíteni, amelyek nem tartalmaznak ékezetes jeleket.

**Kifejezés:** NormalizeDiacritics ([givenName])

**Minta bemenet/kimenet:** 

* **Bemenet** (givenName): "Zoë"
* **Kimenet**: "Zoe"


---
### <a name="not"></a>Not
**Függvény:** Nem (forrás)

**Leírás:** Megfordítja a **forrás** logikai értékét. Ha a **forrás** értéke TRUE (igaz), a hamis értéket ad vissza. Ellenkező esetben igaz értéket ad vissza.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **forrás** |Kötelező |Logikai karakterlánc |A várt **források** értéke "true" vagy "false". |

---
### <a name="numfromdate"></a>NumFromDate
**Függvény:** NumFromDate (érték)

**Leírás:** A NumFromDate függvény egy DateTime értéket konvertál Active Directory formátumra, amely az attribútumok (például a [accountExpires](/windows/win32/adschema/a-accountexpires)) beállításához szükséges. Ezzel a függvénnyel a Felhőbeli HR-alkalmazásokból (például a munkanapokból és a SuccessFactors) kapott dátum-és időértékek konvertálhatók 

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **value** |Kötelező | Sztring | A dátum és idő karakterlánca támogatott formátumban. Támogatott formátumok: https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx . |

**Példa**
* A munkanap példája, amely azt feltételezi, hogy a *ContractEndDate* attribútumot szeretné leképezni, amely az ad-ben a *2020-12-31-08:00* érték *accountExpires* mezőjében szerepel, itt látható, hogyan használhatja ezt a függvényt, és módosíthatja az időzóna-eltolást a területi beállításnak megfelelően. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], ,"yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* SuccessFactors példa arra, hogy a *endDate* attribútumot szeretné leképezni a SuccessFactors-ből, amely a következő formátumban van *: M/d/éééé óó: PP: SS tt* to *accountExpires* mező az ad-ben, a függvény használata és az időzóna eltolásának módosítása a területi beállításnak megfelelően.
  `NumFromDate(Join("",FormatDateTime([endDate], ,"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>RemoveDuplicates
**Függvény:** RemoveDuplicates (attribútum)

**Leírás:** A RemoveDuplicates függvény többértékű karakterláncot használ, és minden érték egyedi.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **attribútum** |Kötelező |Többértékű attribútum |Több értékkel rendelkező attribútum, amelyből duplikált elemek lesznek eltávolítva|

**Példa:** 
 `RemoveDuplicates([proxyAddresses])` Egy megtisztított proxyAddress attribútumot ad vissza, amelyben az összes duplikált érték el lett távolítva.

---
### <a name="replace"></a>Csere
**Függvény:** Replace (forrás, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, sablon)

**Leírás:** A karakterláncban szereplő értékeket kis-és nagybetűk megkülönböztetésével helyettesíti. A függvény a megadott paraméterektől függően másképp viselkedik:

* A **OldValue** és a **replacementValue** megadása esetén:
  
  * Lecseréli a **forrásban** lévő **OldValue** összes előfordulását a **replacementValue**
* A **OldValue** és a **sablon** megadásakor:
  
  * A **sablonban** lévő **OldValue** összes előfordulását lecseréli a **forrás** értékkel
* A **regexPattern** és a **replacementValue** megadása esetén:

  * A függvény a **regexPattern** alkalmazza a **forrás** sztringre, és a regex-csoportok nevét használhatja a **replacementValue** karakterlánc létrehozásához.
* A **regexPattern**, a **regexGroupName** és a **replacementValue** megadásakor:
  
  * A függvény a **regexPattern** alkalmazza a **forrás** sztringre, és lecseréli az összes olyan értéket, amely megfelel a **regexGroupName** a **replacementValue**
* A **regexPattern**, a **regexGroupName** és a **replacementAttributeName** megadásakor:
  
  * Ha a **forrásnak** nincs értéke, a rendszer visszaadja a **forrást** .
  * Ha a **forrás** értékkel rendelkezik, a függvény a **regexPattern** alkalmazza a **forrás** sztringre, és lecseréli az összes olyan értéket, amely megfelel a **regexGroupName** a **replacementAttributeName** társított értéknek.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **forrás** |Kötelező |Sztring |Az attribútum neve általában a **forrásoldali** objektumban. |
| **oldValue** |Választható |Sztring |A **forrásban** vagy **sablonban** cserélni kívánt érték. |
| **regexPattern** |Választható |Sztring |A **forrásban** lecserélni kívánt érték regex-mintája. Vagy ha **replacementPropertyName** használ, a **replacementPropertyName** származó érték kinyerésére szolgáló minta. |
| **regexGroupName** |Választható |Sztring |A csoport neve a **regexPattern** belül. Csak **replacementPropertyName** használata esetén a csoport értékének kinyerése a **replacementPropertyName** **replacementValue** történik. |
| **replacementValue** |Választható |Sztring |Új érték a régi helyett. |
| **replacementAttributeName** |Választható |Sztring |A helyettesítő értékhez használandó attribútum neve |
| **sablon** |Választható |Sztring |Ha meg van adni a **sablon** értéke, megkeresjük a **OldValue** a sablonon belül, és lecseréljük a **forrás** értékre. |

### <a name="replace-characters-using-a-regular-expression"></a>Karakterek cseréje reguláris kifejezés használatával
Példa: meg kell keresnie a reguláris kifejezés értékének megfelelő karaktereket, és el kell távolítani őket.

**Kifejezés** 

Replace ([mailNickname],, "[a-zA-Z_] *",, "",,)

**Minta bemenet/kimenet:**

* **Bemenet** (mailNickname: "john_doe72"
* **Kimenet**: "72"


---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Függvény:** SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, ...)

**Leírás:** Legalább két argumentumot igényel, amelyek a kifejezések használatával definiált egyedi érték-létrehozási szabályok. A függvény kiértékeli az egyes szabályokat, majd ellenőrzi az egyediséghez generált értéket a cél alkalmazásban vagy könyvtárban. A rendszer az első egyedi értéket találta vissza. Ha az összes érték már létezik a célhelyen, a bejegyzést letétbe helyezi a rendszer, és az OK bekerül a naplóba. Nincs felső korlát a megadható argumentumok számához.


 - Ez egy legfelső szintű függvény, nem ágyazható be.
 - Ez a függvény nem alkalmazható olyan attribútumokra, amelyek egyező elsőbbséggel rendelkeznek.     
 - Ez a függvény csak a bejegyzések létrehozásához használható. Ha attribútummal használja, állítsa a **leképezés alkalmazása** tulajdonságot csak az **objektum létrehozása során**.
 - Ez a függvény jelenleg csak a "munkanap Active Directory a felhasználók kiosztása" és a "SuccessFactors Active Directory a felhasználó kiosztása" esetén támogatott. Más kiépítési alkalmazásokkal nem használható. 


**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **uniqueValueRule1 ... uniqueValueRuleN** |Legalább 2 szükséges, nincs felső korlát |Sztring | A kiértékelni kívánt egyedi érték-létrehozási szabályok listája. |

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Egyedi érték előállítása a userPrincipalName (UPN) attribútumhoz
Példa: a felhasználó keresztneve, középső neve és vezetékneve alapján értéket kell létrehoznia az UPN-attribútumhoz, és meg kell adnia annak egyediségét a cél AD-címtárban, mielőtt hozzárendeli az értéket az UPN-attribútumhoz.

**Kifejezés** 

```ad-attr-mapping-expr
    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )
```

**Minta bemenet/kimenet:**

* **Bemenet** (PreferredFirstName): "John"
* **Bemenet** (PreferredLastName): "Kovács"
* **Output**: " John.Smith@contoso.com ", ha John.Smith@contoso.com még nem létezik UPN-érték a címtárban
* **Output**: " J.Smith@contoso.com ", ha John.Smith@contoso.com a címtárban már létezik UPN-érték
* **Kimenet**: " Jo.Smith@contoso.com ", ha a fenti két UPN-érték már létezik a címtárban



---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Függvény:** SingleAppRoleAssignment ([appRoleAssignments])

**Leírás:** Egyetlen appRoleAssignment ad vissza egy adott alkalmazás felhasználóhoz rendelt összes appRoleAssignments listájáról. Ez a függvény szükséges ahhoz, hogy a appRoleAssignments objektumot egyetlen szerepkör-nevet megadó karakterláncba alakítsa át. Vegye figyelembe, hogy az ajánlott eljárás annak biztosítása, hogy egyszerre csak egy appRoleAssignment legyen hozzárendelve egy felhasználóhoz, és ha több szerepkör van hozzárendelve, a visszaadott szerepkör-karakterlánc nem lehet előre jelezhető. 

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **AppRoleAssignments** |Kötelező |Sztring |**[appRoleAssignments]** objektum. |

---
### <a name="split"></a>Felosztás
**Függvény:** Felosztás (forrás, elválasztó karakter)

**Leírás:** A karakterláncot egy többértékű tömbre osztja fel a megadott elválasztó karakter használatával.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **forrás** |Kötelező |Sztring |a frissítendő **forrás** értéke. |
| **elválasztó** |Kötelező |Sztring |Meghatározza a karakterlánc felosztására szolgáló karaktert (példa: ",") |

### <a name="split-a-string-into-a-multi-valued-array"></a>Sztring felosztása többértékű tömbbe
Példa: a karakterláncok vesszővel tagolt listáját kell megadnia, és azokat egy olyan tömbbe kell bontani, amely egy többértékű attribútumhoz, például a Salesforce PermissionSets attribútumához csatlakoztatható. Ebben a példában az extensionAttribute5 az Azure AD-ben az engedélyezési készletek listája lett feltöltve.

**Kifejezés:** Split ([extensionAttribute5], ",")

**Minta bemenet/kimenet:** 

* **Bemenet** (extensionAttribute5): "PermissionSetOne, PermissionSetTwo"
* **Kimenet**: ["PermissionSetOne", "PermissionSetTwo"]


---
### <a name="stripspaces"></a>StripSpaces
**Függvény:** StripSpaces (forrás)

**Leírás:** Eltávolítja az összes szóköz ("") karaktert a forrás sztringből.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **forrás** |Kötelező |Sztring |a frissítendő **forrás** értéke. |

---
### <a name="switch"></a>Kapcsoló (switch)
**Függvény:** Kapcsoló (forrás, defaultValue, key1, érték1, key2, érték2,...)

**Leírás:** Ha a **forrás** értéke megegyezik egy **kulccsal**, az adott **kulcs** **értékét** adja vissza. Ha a **forrás** értéke nem felel meg a kulcsoknak, a a **defaultValue** értéket adja vissza.  A **kulcsok** és **értékek** paramétereit mindig párokban kell megadni. A függvény mindig páros számú paramétert vár. A függvény nem használható hivatkozási attribútumokhoz, például a kezelőhöz. 

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **forrás** |Kötelező |Sztring |A frissítendő **forrás** értéke. |
| **defaultValue** |Választható |Sztring |Az alapértelmezett érték, amelyet akkor kell használni, ha a forrás nem felel meg a kulcsoknak. Üres karakterlánc ("") lehet. |
| **kulcs** |Kötelező |Sztring |A **kulcs** a **forrás** értékének összehasonlításához a következővel:. |
| **value** |Kötelező |Sztring |A kulcsnak megfelelő **forrás** helyettesítő értéke. |

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Érték cseréje előre megadott beállítások alapján
Példa: meg kell határoznia a felhasználó időzónáját az Azure AD-ben tárolt állapot kódja alapján. Ha az állapotkód nem egyezik az előre definiált beállításokkal, használja az "Australia/Sydney" alapértelmezett értékét.

**Kifejezés** 
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Minta bemenet/kimenet:**

* **Bemenet** (állapot): "QLD"
* **Kimenet**: "Ausztrália/Brisbane"


---
### <a name="tolower"></a>ToLower
**Függvény:** ToLower (forrás, kulturális környezet)

**Leírás:** Egy *forrás* sztring értéket vesz igénybe, és a megadott kulturális szabályok alapján átalakítja a kisbetűsre. Ha nincs megadva *kulturális* információ, akkor a rendszer a semleges kultúrát fogja használni.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **forrás** |Kötelező |Sztring |Az attribútum neve általában a forrásoldali objektumból |
| **kulturális környezet** |Választható |Sztring |Az RFC 4646 alapján a kulturális név formátuma *languagecode2-ország/regioncode2*, ahol a *languagecode2* a kétbetűs nyelvi kód, az *ország/regioncode2* pedig a kétbetűs alkulturális kód. Ilyenek például a japán (Japán) és az en-US angol (Egyesült Államok). Azokban az esetekben, amikor a kétbetűs nyelvi kód nem érhető el, az ISO 639-2-ből származtatott hárombetűs kód van használatban.|

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Generált userPrincipalName (UPN) értékének kisbetűvé alakítása
Példa: az UPN-értéket az PreferredFirstName és a PreferredLastName forrás mezőinek összefűzésével, valamint az összes karakter kisbetűvé alakításával szeretné előállítani. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Minta bemenet/kimenet:**

* **Bemenet** (PreferredFirstName): "John"
* **Bemenet** (PreferredLastName): "Kovács"
* **Kimenet**: " john.smith@contoso.com "


---
### <a name="toupper"></a>ToUpper
**Függvény:** ToUpper (forrás, kulturális környezet)

**Leírás:** Egy *forrás* sztring értékét veszi át, és a megadott kulturális szabályok alapján átalakítja a nagybetűre. Ha nincs megadva *kulturális* információ, akkor a rendszer a semleges kultúrát fogja használni.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **forrás** |Kötelező |Sztring |Az attribútum neve általában a forrásoldali objektumban. |
| **kulturális környezet** |Választható |Sztring |Az RFC 4646 alapján a kulturális név formátuma *languagecode2-ország/regioncode2*, ahol a *languagecode2* a kétbetűs nyelvi kód, az *ország/regioncode2* pedig a kétbetűs alkulturális kód. Ilyenek például a japán (Japán) és az en-US angol (Egyesült Államok). Azokban az esetekben, amikor a kétbetűs nyelvi kód nem érhető el, az ISO 639-2-ből származtatott hárombetűs kód van használatban.|

---
### <a name="word"></a>Word
**Függvény:** Szó (karakterlánc, WordNumber, határolójelek)

**Leírás:** A Word függvény egy karakterláncon belül található szót ad vissza, amely a használni kívánt határolójeleket és a visszaadni kívánt szó paramétereit írja le. A rendszer az elválasztó karakterek egyikével elválasztott karakterláncban szereplő összes karakterláncot szavakként azonosítja:

Ha a szám < 1, üres karakterláncot ad vissza.
Ha a sztring null, üres karakterláncot ad vissza.
Ha a karakterlánc kevesebb, mint szám szót tartalmaz, vagy a sztring nem tartalmaz határolójelekkel azonosított szavakat, akkor a rendszer üres karakterláncot ad vissza.

**Paraméterek** 

| Name | Szükséges/ismétlődő | Típus | Jegyzetek |
| --- | --- | --- | --- |
| **Sztring** |Kötelező |Többértékű attribútum |Karakterlánc egy szó a következőből való visszaküldéséhez:.|
| **WordNumber** |Kötelező | Egész szám | Az a szám, amelyből vissza kell adni a Word-számot|
| **határolójelek** |Kötelező |Sztring| A szavak azonosításához használandó elválasztó karakter (eke) t jelölő sztring|

**Például**
`Word("The quick brown fox",3," ")`

A "barna" értéket adja vissza.

`Word("This,string!has&many separators",3,",!&#")`

A "has" értéket adja vissza.

---

## <a name="examples"></a>Példák
Ez a szakasz további kifejezéseket tartalmaz a függvények használati példái között. 

### <a name="strip-known-domain-name"></a>Szalag ismert tartományneve
A Felhasználónév beszerzéséhez a felhasználó e-mail-címéből egy ismert tartománynevet kell megadnia. Ha például a tartomány "contoso.com", akkor a következő kifejezést használhatja:

**Kifejezés** 
`Replace([mail], "@contoso.com", , ,"", ,)`

**Minta bemenet/kimenet:** 

* **Bemenet** (e-mail): " john.doe@contoso.com "
* **Kimenet**: "John. DOE"


### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Felhasználói alias előállítása az utónév és a vezetéknév összefűzésével
Felhasználói aliast kell létrehoznia úgy, hogy az első 3 betűt a felhasználó utónevét és az első 5 betűt adja meg.

**Kifejezés** 
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Minta bemenet/kimenet:** 

* **Bemenet** (givenName): "John"
* **Bemenet** (vezetéknév): "DOE"
* **Kimenet**: "JohDoe"


## <a name="related-articles"></a>Kapcsolódó cikkek
* [A felhasználók üzembe helyezésének és megszüntetésének automatizálása az SaaS-alkalmazásokban](../app-provisioning/user-provisioning.md)
* [Attribútum-hozzárendelések testreszabása a felhasználók üzembe helyezéséhez](../app-provisioning/customize-application-attributes.md)
* [Felhasználói kiépítési szűrők hatóköre](define-conditional-rules-for-provisioning-user-accounts.md)
* [SCIM használata a felhasználók és csoportok automatikus üzembe helyezésének engedélyezéséhez az Azure Active Directoryból az alkalmazásokba](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Alkalmazáskiépítési értesítések](../app-provisioning/user-provisioning.md)
* [Az SaaS-alkalmazások integrálásával foglalkozó oktatóanyagok listája](../saas-apps/tutorial-list.md)
