---
title: Azure AD Connect Cloud Sync-kifejezések és-függvények referenciája
description: referencia
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 869d60d4ccb3bae58924fe4221f83b1b0125ae04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98613534"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Kifejezések írása a Azure Active Directory attribútum-hozzárendelésekhez
A Cloud Sync konfigurálásakor a megadható attribútumok egyik típusa kifejezés-hozzárendelés. 

A kifejezés-hozzárendelés lehetővé teszi az attribútumok testreszabását egy parancsfájl-szerű kifejezés használatával.  Ez lehetővé teszi a helyszíni értékek új vagy eltérő értékre alakítását.  Előfordulhat például, hogy két attribútumot szeretne egyesíteni egyetlen attribútummal, mert ezt az egyetlen attribútumot használja az egyik felhőalapú alkalmazás.

A következő dokumentum az adatátalakításhoz használt parancsfájl-szerű kifejezéseket fedi le.  Ez csak a folyamat része.  Ezután ezt a kifejezést kell használnia, és egy webes kérelembe kell helyeznie a bérlője számára.  További információ az [átalakításokról](how-to-transformation.md) :

## <a name="syntax-overview"></a>Szintaxis áttekintése
Az attribútum-hozzárendelések kifejezések szintaxisa Visual Basic for Applications (VBA) függvények emlékeztetője.

* A teljes kifejezést a functions kifejezésben kell definiálni, amely egy, a zárójelben szereplő argumentumokkal kiegészített nevet tartalmaz: <br>
  *Függvénynév ( `<<argument 1>>` , `<<argument N>>` )*
* A függvények egymásba ágyazhatók. Például: <br> *FunctionOne (FunctionTwo ( `<<argument1>>` ))*
* A függvények három különböző típusú argumentumot adhat át:
  
  1. Attribútumok, amelyeket szögletes zárójelbe kell foglalni. Például: [attributeName]
  2. Karakterlánc-konstansok, amelyek idézőjelek közé kell, hogy legyenek. Például: "Egyesült Államok"
  3. Egyéb függvények. Például: FunctionOne ( `<<argument1>>` , FunctionTwo ( `<<argument2>>` ))
* Karakterlánc-konstansok esetén, ha a karakterláncban a fordított perjel (\) vagy az idézőjel (") értékre van szüksége, akkor azt a fordított perjel (\) szimbólummal kell megadnia. Például: "cég neve: \\ " contoso \\ ""

## <a name="list-of-functions"></a>Függvénylista
| Függvénylista | Leírás |
|-----|----|
|[Append](#append)|A forrás sztring értéket veszi fel, és hozzáfűzi az utótagot a végéhez.|
|[BitAnd](#bitand)|A BitAnd függvény a megadott biteket egy értékre állítja be.|
|[CBool](#cbool)|A CBool függvény egy logikai értéket ad vissza a kiértékelt kifejezés alapján.|
|[ConvertFromBase64](#convertfrombase64)|A ConvertFromBase64 függvény a megadott Base64 kódolású értéket egy normál karakterlánccá alakítja át.|
|[ConvertToBase64](#converttobase64)|A ConvertToBase64 függvény egy karakterláncot Unicode Base64 karakterlánccá alakít át. |
|[ConvertToUTF8Hex](#converttoutf8hex)|A ConvertToUTF8Hex függvény egy karakterláncot UTF8 hexadecimális kódolású értékre alakít át.|
|[Száma](#count)|A Count függvény egy többértékű attribútum elemeinek számát adja vissza.|
|[CStr](#cstr)|A CStr függvény karakterlánc típusú adattípusra konvertál.|
|[DateFromNum](#datefromnum)|A DateFromNum függvény egy értéket AD meg az AD dátumformátum dátum és idő típusára.|
|[DNComponent](#dncomponent)|A DNComponent függvény a megadott DN-összetevő értékét adja vissza balról.|
|[Hiba](#error)|A Error függvény egyéni hiba visszaküldésére szolgál.|
|[FormatDateTime](#formatdatetime) |Egy dátum sztringet vesz fel az egyik formátumból, és átalakítja azt más formátumra.| 
|[GUID](#guid)|A függvény GUID-azonosítója új véletlenszerű GUID azonosítót hoz létre.|           
|[IIF](#iif)|Az IIF függvény a lehetséges értékek egy halmazát adja vissza egy megadott feltétel alapján.|
|[InStr](#instr)|A beosztási függvény megkeresi egy karakterlánc első előfordulását.|
|[IsNull](#isnull)|Ha a kifejezés értéke null, akkor a IsNull függvény Igaz értéket ad vissza.|
|[IsNullOrEmpty](#isnullorempty)|Ha a kifejezés null értékű vagy üres karakterlánc, akkor a IsNullOrEmpty függvény Igaz értéket ad vissza.|         
|[IsPresent](#ispresent)|Ha a kifejezés olyan karakterláncot ad vissza, amely nem null értékű, és nem üres, akkor a IsPresent függvény Igaz értéket ad vissza.|    
|[IsString](#isstring)|Ha a kifejezés kiértékelhető karakterlánc típusúra, akkor a IsString függvény Igaz értéket ad vissza.|
|[Elem](#item)|Az Item függvény egy elemet ad vissza egy többértékű karakterlánc/attribútumból.|
|[Csatlakozás](#join) |A JOIN () hasonló a hozzáfűzéshez (), azzal a kivétellel, hogy több **forrás** sztringet is egyesít egyetlen karakterláncban, és az egyes értékeket **elválasztó sztring választja** el egymástól.| 
|[Bal](#left)|A Left függvény egy karakterlánctól balra megadott számú karaktert ad vissza.|
|[Közepes](#mid) |A forrás értékének egy alsztringjét adja vissza. Az alsztringek olyan karakterláncok, amelyek csak néhány karaktert tartalmaznak a forrás sztringből.|
|[NormalizeDiacritics](#normalizediacritics)|Egy karakterlánc-argumentumot igényel. A karakterláncot adja vissza, de bármilyen diakritikus karakterrel egyenértékű, nem diakritikus karakterrel helyettesíthető.|
|[Nem](#not) |Megfordítja a **forrás** logikai értékét. Ha a **forrás** értéke "*true*" (igaz), a "*false*" értéket adja vissza. Ellenkező esetben az "*igaz*" értéket adja vissza.| 
|[RemoveDuplicates](#removeduplicates)|A RemoveDuplicates függvény többértékű karakterláncot használ, és minden érték egyedi.| 
|[Csere](#replace) |Egy karakterláncon belüli értékeket cserél le. | 
|[SelectUniqueValue](#selectuniquevalue)|Legalább két argumentumot igényel, amelyek a kifejezések használatával definiált egyedi érték-létrehozási szabályok. A függvény kiértékeli az egyes szabályokat, majd ellenőrzi az egyediséghez generált értéket a cél alkalmazásban vagy könyvtárban.| 
|[SingleAppRoleAssignment](#singleapproleassignment)|Egyetlen appRoleAssignment ad vissza egy adott alkalmazás felhasználóhoz rendelt összes appRoleAssignments listájáról.| 
|[Felosztása](#split)|A karakterláncot egy többértékű tömbre osztja fel a megadott elválasztó karakter használatával.|
|[StringFromSID](#stringfromsid)|A StringFromSid függvény egy olyan byte tömböt alakít át, amely biztonsági azonosítót tartalmaz egy karakterláncra.| 
|[StripSpaces](#stripspaces) |Eltávolítja az összes szóköz ("") karaktert a forrás sztringből.| 
|[Kapcsoló (switch)](#switch)|Ha a **forrás** értéke megegyezik egy **kulccsal**, az adott **kulcs** **értékét** adja vissza. | 
|[ToLower](#tolower)|Egy *forrás* sztring értéket vesz igénybe, és a megadott kulturális szabályok alapján átalakítja a kisbetűsre.| 
|[ToUpper](#toupper)|Egy *forrás* sztring értékét veszi át, és a megadott kulturális szabályok alapján átalakítja a nagybetűre.|
|[Trim](#trim)|A Trim függvény eltávolítja a kezdő és záró szóközöket egy karakterláncból.|
|[Word](#word)|A Word függvény egy karakterláncon belül található szót ad vissza, amely a használni kívánt határolójeleket és a visszaadni kívánt szó paramétereit írja le.|

---
### <a name="append"></a>Append (Hozzáfűzés)
**Függvény**<br> Hozzáfűzés (forrás, utótag)

**Leírás:**<br> A forrás sztring értéket veszi fel, és hozzáfűzi az utótagot a végéhez.

**Paraméterek**<br> 

   | Name | Szükséges/ismétlődő | Típus | Jegyzetek |
   | --- | --- | --- | --- |
   | **forrás** |Kötelező |Sztring |Az attribútum neve általában a forrásoldali objektumban. |
   | **utótag** |Kötelező |Sztring |A forrás érték végéhez hozzáfűzni kívánt karakterlánc. |

---
### <a name="bitand"></a>BitAnd
**Leírás:**  
A BitAnd függvény a megadott biteket egy értékre állítja be.

**Szintaxis**  
`num BitAnd(num value1, num value2)`

* érték1, érték2: numerikus értékek, amelyeket össze kell AND'ed

**Megjegyzéseket tartalmazó**  
Ez a függvény mindkét paramétert a bináris ábrázolásra konvertálja, és egy kis mennyiséget állít be a következőre:

* 0 – Ha a *érték1* -ben és a *érték2* -ben található megfelelő bitek közül egyet vagy mindkettőt 0
* 1 – Ha a megfelelő bitek mindegyike 1.

Más szóval a 0 értéket adja vissza minden esetben, kivéve, ha mindkét paraméternek megfelelő bitek értéke 1.

**Példa**  
 
 `BitAnd(&HF, &HF7)`</br>
 A 7 értéket adja vissza, mert a hexadecimális "F" és "F7" kifejezés kiértékelése erre az értékre történik.

---

### <a name="cbool"></a>CBool
**Leírás:**  
A CBool függvény egy logikai értéket ad vissza a kiértékelt kifejezés alapján.

**Szintaxis**  
`bool CBool(exp Expression)`

**Megjegyzéseket tartalmazó**  
Ha a kifejezés kiértékelése nem nulla értékű, akkor a CBool igaz értéket ad vissza, máskülönben hamis értéket ad vissza.

**Példa**  
`CBool([attrib1] = [attrib2])`  

Igaz értéket ad vissza, ha mindkét attribútum ugyanazzal az értékkel rendelkezik.

---
### <a name="convertfrombase64"></a>ConvertFromBase64
**Leírás:**  
A ConvertFromBase64 függvény a megadott Base64 kódolású értéket egy normál karakterlánccá alakítja át.

**Szintaxis**  
`str ConvertFromBase64(str source)` -Unicode kódolást feltételez  
`str ConvertFromBase64(str source, enum Encoding)`

* Forrás: Base64 kódolású karakterlánc  
* Kódolás: Unicode, ASCII, UTF8

**Példa**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Mindkét példa "*Helló világ!*" értéket ad vissza.

---
### <a name="converttobase64"></a>ConvertToBase64
**Leírás:**  
A ConvertToBase64 függvény egy karakterláncot Unicode Base64 karakterlánccá alakít át.  
Egész számokból álló tömb értékét konvertálja az egyenértékű karakterlánc-ábrázolásra, amely Base-64 számjegyekkel van kódolva.

**Szintaxis**  
`str ConvertToBase64(str source)`

**Példa**  
`ConvertToBase64("Hello world!")`  
A "SABlAGwAbABvACAAdwBvAHIAbABkACEA" értéket adja vissza

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Leírás:**  
A ConvertToUTF8Hex függvény egy karakterláncot UTF8 hexadecimális kódolású értékre alakít át.

**Szintaxis**  
`str ConvertToUTF8Hex(str source)`

**Megjegyzéseket tartalmazó**  
A függvény kimeneti formátumát a Azure Active Directory használja DN-attribútum formátumként.

**Példa**  
`ConvertToUTF8Hex("Hello world!")`  
48656C6C6F20776F726C6421 visszaadása

---
### <a name="count"></a>Darabszám
**Leírás:**  
A Count függvény egy többértékű attribútum elemeinek számát adja vissza.

**Szintaxis**  
`num Count(mvstr attribute)`

---
### <a name="cstr"></a>CStr
**Leírás:**  
A CStr függvény karakterlánc típusú adattípusra konvertál.

**Szintaxis**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* Value: numerikus érték, hivatkozási attribútum vagy logikai érték lehet.

**Példa**  
`CStr([dn])`  
Visszatérhet a "CN = Joe, DC = contoso, DC = com" értékre.

---
### <a name="datefromnum"></a>DateFromNum
**Leírás:**  
A DateFromNum függvény egy értéket AD meg az AD dátumformátum dátum és idő típusára.

**Szintaxis**  
`dt DateFromNum(num value)`

**Példa**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
A 2012-01-01 23:00:00-et jelölő DateTime értéket ad vissza.

---
### <a name="dncomponent"></a>DNComponent
**Leírás:**  
A DNComponent függvény a megadott DN-összetevő értékét adja vissza balról.

**Szintaxis**  
`str DNComponent(ref dn, num ComponentNumber)`

* DN: az értelmezni kívánt hivatkozási attribútum
* ComponentNumber: a DN által visszaadott összetevő

**Példa**  
`DNComponent(CRef([dn]),1)`  
Ha a DN a következő: "CN = Joe, ou =...", visszaadja a következőt: Joe

---
### <a name="error"></a>Hiba
**Leírás:**  
A Error függvény egyéni hiba visszaküldésére szolgál.

**Szintaxis**  
`void Error(str ErrorMessage)`

**Példa**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Ha a accountName attribútum nem található, hibát jelez az objektumon.

---
### <a name="formatdatetime"></a>FormatDateTime
**Függvény**<br> FormatDateTime (forrás, inputFormat, outputFormat)

**Leírás:**<br> Egy dátum sztringet vesz fel az egyik formátumból, és átalakítja azt más formátumra.

**Paraméterek**<br> 

   | Name | Szükséges/ismétlődő | Típus | Jegyzetek |
   | --- | --- | --- | --- |
   | **forrás** |Kötelező |Sztring |Az attribútum neve általában a forrásoldali objektumban. |
   | **inputFormat** |Kötelező |Sztring |A forrás értékének várt formátuma. Támogatott formátumok: [/DotNet/standard/Base-types/Custom-Date-and-Time-Format-Strings](/dotnet/standard/base-types/custom-date-and-time-format-strings). |
   | **outputFormat** |Kötelező |Sztring |A kimeneti dátum formátuma. |

---
### <a name="guid"></a>Guid
**Leírás:**  
A függvény GUID-azonosítója új véletlenszerű GUID azonosítót hoz létre

**Szintaxis**  
`str Guid()`

---
### <a name="iif"></a>IIF
**Leírás:**  
Az IIF függvény a lehetséges értékek egy halmazát adja vissza egy megadott feltétel alapján.

**Szintaxis**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* feltétel: bármely olyan érték vagy kifejezés, amelynek kiértékelése igaz vagy hamis lehet.
* valueIfTrue: Ha a feltétel igaz értéket ad vissza, a visszaadott érték.
* valueIfFalse: Ha a feltétel hamis értéket ad vissza, a visszaadott érték.

**Példa**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Ha a felhasználó egy gyakornok, a a "t-" értékkel rendelkező felhasználó aliasát adja vissza, a másik pedig a felhasználó aliasát adja vissza.

---
### <a name="instr"></a>InStr
**Leírás:**  
A beosztási függvény megkeresi egy karakterláncban szereplő alsztring első előfordulását.

**Szintaxis**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start, enum compare)`

* karakterlánc: keresendő karakterlánc
* keresettkarakterlánc: a rendszer megtalálni kívánt karakterláncot
* Kezdés: a pozíció megkeresése az alsztring megtalálásához
* összehasonlítás: vbTextCompare vagy vbBinaryCompare

**Megjegyzéseket tartalmazó**  
Azt a pozíciót adja vissza, ahol az alkarakterlánc található vagy 0, ha nem található.

**Példa**  
`InStr("The quick brown fox","quick")`  
Evalues – 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Értékelés 7-re

---
### <a name="isnull"></a>IsNull
**Leírás:**  
Ha a kifejezés értéke null, akkor a IsNull függvény Igaz értéket ad vissza.

**Szintaxis**  
`bool IsNull(var Expression)`

**Megjegyzéseket tartalmazó**  
Attribútum esetén a null értéket az attribútum hiánya fejezi ki.

**Példa**  
`IsNull([displayName])`  
Igaz értéket ad vissza, ha az attribútum nem szerepel a CS vagy az MV-ban.

---
### <a name="isnullorempty"></a>IsNullOrEmpty
**Leírás:**  
Ha a kifejezés null értékű vagy üres karakterlánc, akkor a IsNullOrEmpty függvény Igaz értéket ad vissza.

**Szintaxis**  
`bool IsNullOrEmpty(var Expression)`

**Megjegyzéseket tartalmazó**  
Egy attribútum esetében ez igaz értékre értékeli, ha az attribútum hiányzik vagy létezik, de egy üres karakterlánc.  
A függvény inverzének neve IsPresent.

**Példa**  
`IsNullOrEmpty([displayName])`  
Igaz értéket ad vissza, ha az attribútum nincs jelen, vagy üres karakterlánc a CS vagy az MV.

---
### <a name="ispresent"></a>IsPresent
**Leírás:**  
Ha a kifejezés olyan karakterláncot ad vissza, amely nem null értékű, és nem üres, akkor a IsPresent függvény Igaz értéket ad vissza.

**Szintaxis**  
`bool IsPresent(var expression)`

**Megjegyzéseket tartalmazó**  
A függvény inverzének neve IsNullOrEmpty.

**Példa**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Elem
**Leírás:**  
Az Item függvény egy elemet ad vissza egy többértékű karakterlánc/attribútumból.

**Szintaxis**  
`var Item(mvstr attribute, num index)`

* attribútum: többértékű attribútum
* index: index a Többértékű karakterlánc egyik elemébe.

**Megjegyzéseket tartalmazó**  
Az Item függvény a tartalmaz függvénnyel együtt használható, mivel az utóbbi függvény az indexet a többértékű attribútum egy elemébe adja vissza.

Hibát jelez, ha az index tartományon kívül esik.

**Példa**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Az elsődleges e-mail-címet adja vissza.

---
### <a name="isstring"></a>IsString
**Leírás:**  
Ha a kifejezés kiértékelhető karakterlánc típusúra, akkor a IsString függvény Igaz értéket ad vissza.

**Szintaxis**  
`bool IsString(var expression)`

**Megjegyzéseket tartalmazó**  
Annak megállapítására szolgál, hogy a CStr () sikeres lehet-e a kifejezés értelmezéséhez.

---
### <a name="join"></a>Csatlakozás
**Függvény**<br> Join (elválasztó, source1, source2,...)

**Leírás:**<br> A JOIN () hasonló a hozzáfűzéshez (), azzal a kivétellel, hogy több **forrás** sztringet is egyesít egyetlen karakterláncban, és az egyes értékeket **elválasztó sztring választja** el egymástól.

Ha a forrásadatok egyike egy többértékű attribútum, akkor az adott attribútum minden értéke együtt lesz egyesítve, az elválasztó értékkel elválasztva.

**Paraméterek**<br> 

   | Name | Szükséges/ismétlődő | Típus | Jegyzetek |
   | --- | --- | --- | --- |
   | **elválasztó** |Kötelező |Sztring |A forrásadatok elválasztására szolgáló karakterlánc, amely egyetlen sztringbe van fűzve. Lehet "", ha nem kötelező elválasztó. |
   | **source1 ... sourceN** |Kötelező, változó – ennyiszer |Sztring |A egyesíteni kívánt karakterlánc-értékek. |

---
### <a name="left"></a>Bal
**Leírás:**  
A Left függvény egy karakterlánctól balra megadott számú karaktert ad vissza.

**Szintaxis**  
`str Left(str string, num NumChars)`

* string: az a karakterlánc, amelyből karaktereket kell visszaadni
* NumChars: a sztring elejétől (balra) visszaadott karakterek számát azonosító szám.

**Megjegyzéseket tartalmazó**  
A karakterlánc első numChars karaktert tartalmazó karakterlánc:

* Ha a numChars = 0, üres karakterláncot ad vissza.
* Ha a numChars < 0 értéket, adja vissza a bemeneti karakterláncot.
* Ha a karakterlánc null értékű, üres karakterláncot ad vissza.

Ha a sztring kevesebb karaktert tartalmaz a numChars megadott számnál, akkor a rendszer a karakterlánctal megegyező karakterláncot (azaz az 1. paraméterben szereplő összes karaktert tartalmazza) adja vissza.

**Példa**  
`Left("John Doe", 3)`  
Visszatérési érték `Joh` .

---
### <a name="mid"></a>Közepes
**Függvény**<br> Mid (forrás, Kezdés, hossz)

**Leírás:**<br> A forrás értékének egy alsztringjét adja vissza. Az alsztringek olyan karakterláncok, amelyek csak néhány karaktert tartalmaznak a forrás sztringből.

**Paraméterek**<br> 

   | Name | Szükséges/ismétlődő | Típus | Jegyzetek |
   | --- | --- | --- | --- |
   | **forrás** |Kötelező |Sztring |Az attribútum neve általában. |
   | **Start** |Kötelező |egész szám |Az index a **forrás** sztringben, ahol az alsztringnek el kell indulnia. A karakterlánc első karakterének indexe 1, a második karakter pedig a 2. indexet fogja tartalmazni. |
   | **length** (hossz) |Kötelező |egész szám |Az alsztring hossza. Ha a hossz a **forrás** sztringen kívülre végződik, a függvény a **kezdő** indexből származó alsztringet ad vissza a **forrás** sztring végéig. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Függvény**<br> NormalizeDiacritics (forrás)

**Leírás:**<br> Egy karakterlánc-argumentumot igényel. A karakterláncot adja vissza, de bármilyen diakritikus karakterrel egyenértékű, nem diakritikus karakterrel helyettesíthető. Jellemzően a különböző felhasználói azonosítókban, például egyszerű felhasználónevek, SAM-fiókok és e-mail-címekben használható, mellékjeleket és vezetékneveket tartalmazó nevek és vezetéknévek átalakítására szolgálnak.

**Paraméterek**<br> 

   | Name | Szükséges/ismétlődő | Típus | Jegyzetek |
   | --- | --- | --- | --- |
   | **forrás** |Kötelező |Sztring | Általában utónév vagy vezetéknév attribútum. |

---
### <a name="not"></a>Not
**Függvény**<br> Nem (forrás)

**Leírás:**<br> Megfordítja a **forrás** logikai értékét. Ha a **forrás** értéke "*true*" (igaz), a "*false*" értéket adja vissza. Ellenkező esetben az "*igaz*" értéket adja vissza.

**Paraméterek**<br> 

   | Name | Szükséges/ismétlődő | Típus | Jegyzetek |
   | --- | --- | --- | --- |
   | **forrás** |Kötelező |Logikai karakterlánc |A várt **források** értéke "true" vagy "false". |

---
### <a name="removeduplicates"></a>RemoveDuplicates
**Leírás:**  
A RemoveDuplicates függvény többértékű karakterláncot használ, és minden érték egyedi.

**Szintaxis**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Példa**  
`RemoveDuplicates([proxyAddresses])`  
Egy megtisztított proxyAddress attribútumot ad vissza, amelyben az összes duplikált érték el lett távolítva.

---
### <a name="replace"></a>Csere
**Függvény**<br> Replace (forrás, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, sablon)

**Leírás:**<br>
Egy karakterláncon belüli értékeket cserél le. A megadott paraméterektől függően másképp működik:

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

**Paraméterek**<br> 

   | Name | Szükséges/ismétlődő | Típus | Jegyzetek |
   | --- | --- | --- | --- |
   | **forrás** |Kötelező |Sztring |Az attribútum neve általában a **forrásoldali** objektumban. |
   | **oldValue** |Választható |Sztring |A **forrásban** vagy **sablonban** cserélni kívánt érték. |
   | **regexPattern** |Választható |Sztring |A **forrásban** lecserélni kívánt érték regex-mintája. Vagy ha **replacementPropertyName** használ, a **replacementPropertyName** származó érték kinyerésére szolgáló minta. |
   | **regexGroupName** |Választható |Sztring |A csoport neve a **regexPattern** belül. Csak **replacementPropertyName** használata esetén a csoport értékének kinyerése a **replacementPropertyName** **replacementValue** történik. |
   | **replacementValue** |Választható |Sztring |Új érték a régi helyett. |
   | **replacementAttributeName** |Választható |Sztring |A helyettesítő értékhez használandó attribútum neve |
   | **sablon** |Választható |Sztring |Ha meg van adni a **sablon** értéke, megkeresjük a **OldValue** a sablonon belül, és lecseréljük a **forrás** értékre. |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Függvény**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, ...)

**Leírás:**<br> Legalább két argumentumot igényel, amelyek a kifejezések használatával definiált egyedi érték-létrehozási szabályok. A függvény kiértékeli az egyes szabályokat, majd ellenőrzi az egyediséghez generált értéket a cél alkalmazásban vagy könyvtárban. A rendszer az első egyedi értéket találta vissza. Ha az összes érték már létezik a célhelyen, a bejegyzést letétbe helyezi a rendszer, és az OK bekerül a naplóba. Nincs felső korlát a megadható argumentumok számához.

> [!NOTE]
> - Ez egy legfelső szintű függvény, nem ágyazható be.
> - Ez a függvény nem alkalmazható olyan attribútumokra, amelyek egyező elsőbbséggel rendelkeznek.  
> - Ez a függvény csak a bejegyzések létrehozásához használható. Ha attribútummal használja, állítsa a **leképezés alkalmazása** tulajdonságot csak az **objektum létrehozása során**.
> - Ez a függvény jelenleg csak a "munkanap Active Directory a felhasználók kiosztásához" támogatott. Más kiépítési alkalmazásokkal nem használható. 


**Paraméterek**<br> 

   | Name | Szükséges/ismétlődő | Típus | Jegyzetek |
   | --- | --- | --- | --- |
   | **uniqueValueRule1 ... uniqueValueRuleN** |Legalább 2 szükséges, nincs felső korlát |Sztring | A kiértékelni kívánt egyedi érték-létrehozási szabályok listája. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Függvény**<br> SingleAppRoleAssignment ([appRoleAssignments])

**Leírás:**<br> Egyetlen appRoleAssignment ad vissza egy adott alkalmazás felhasználóhoz rendelt összes appRoleAssignments listájáról. Ez a függvény szükséges ahhoz, hogy a appRoleAssignments objektumot egyetlen szerepkör-nevet megadó karakterláncba alakítsa át. Vegye figyelembe, hogy az ajánlott eljárás annak biztosítása, hogy egyszerre csak egy appRoleAssignment legyen hozzárendelve egy felhasználóhoz, és ha több szerepkör van hozzárendelve, a visszaadott szerepkör-karakterlánc nem lehet előre jelezhető. 

**Paraméterek**<br> 

  | Name | Szükséges/ismétlődő | Típus | Jegyzetek |
  |--- | --- | --- | --- |
  | **AppRoleAssignments** |Kötelező |Sztring |**[appRoleAssignments]** objektum. |

---
### <a name="split"></a>Felosztás
**Függvény**<br> Felosztás (forrás, elválasztó karakter)

**Leírás:**<br> A karakterláncot egy többértékű tömbre osztja fel a megadott elválasztó karakter használatával.

**Paraméterek**<br> 

   | Name | Szükséges/ismétlődő | Típus | Jegyzetek |
   | --- | --- | --- | --- |
   | **forrás** |Kötelező |Sztring |a frissítendő **forrás** értéke. |
   | **elválasztó** |Kötelező |Sztring |Meghatározza a karakterlánc felosztására szolgáló karaktert (példa: ",") |

---
### <a name="stringfromsid"></a>StringFromSid
**Leírás:**  
A StringFromSid függvény egy olyan byte tömböt alakít át, amely biztonsági azonosítót tartalmaz egy karakterláncra.

**Szintaxis**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="stripspaces"></a>StripSpaces
**Függvény**<br> StripSpaces (forrás)

**Leírás:**<br> Eltávolítja az összes szóköz ("") karaktert a forrás sztringből.

**Paraméterek**<br> 

   | Name | Szükséges/ismétlődő | Típus | Jegyzetek |
   | --- | --- | --- | --- |
   | **forrás** |Kötelező |Sztring |a frissítendő **forrás** értéke. |

---
### <a name="switch"></a>Kapcsoló (switch)
**Függvény**<br> Kapcsoló (forrás, defaultValue, key1, érték1, key2, érték2,...)

**Leírás:**<br> Ha a **forrás** értéke megegyezik egy **kulccsal**, az adott **kulcs** **értékét** adja vissza. Ha a **forrás** értéke nem felel meg a kulcsoknak, a a **defaultValue** értéket adja vissza.  A **kulcsok** és **értékek** paramétereit mindig párokban kell megadni. A függvény mindig páros számú paramétert vár.

**Paraméterek**<br> 

   | Name | Szükséges/ismétlődő | Típus | Jegyzetek |
   | --- | --- | --- | --- |
   | **forrás** |Kötelező |Sztring |Az ellenőrzési **forrás** értéke. |
   | **defaultValue** |Választható |Sztring |Az alapértelmezett érték, amelyet akkor kell használni, ha a forrás nem felel meg a kulcsoknak. Üres karakterlánc ("") lehet. |
   | **kulcs** |Kötelező |Sztring |A **kulcs** a **forrás** értékének összehasonlításához a következővel:. |
   | **value** |Kötelező |Sztring |A kulcsnak megfelelő **forrás** helyettesítő értéke. |

---
### <a name="tolower"></a>ToLower
**Függvény**<br> ToLower (forrás, kulturális környezet)

**Leírás:**<br> Egy *forrás* sztring értéket vesz igénybe, és a megadott kulturális szabályok alapján átalakítja a kisbetűsre. Ha nincs megadva *kulturális* információ, akkor a rendszer a semleges kultúrát fogja használni.

**Paraméterek**<br> 

   | Name | Szükséges/ismétlődő | Típus | Jegyzetek |
   | --- | --- | --- | --- |
   | **forrás** |Kötelező |Sztring |Az attribútum neve általában a forrásoldali objektumból |
   | **kulturális környezet** |Választható |Sztring |Az RFC 4646 alapján a kulturális név formátuma *languagecode2-ország/regioncode2*, ahol a *languagecode2* a kétbetűs nyelvi kód, az *ország/regioncode2* pedig a kétbetűs alkulturális kód. Ilyenek például a japán (Japán) és az en-US angol (Egyesült Államok). Azokban az esetekben, amikor a kétbetűs nyelvi kód nem érhető el, az ISO 639-2-ből származtatott hárombetűs kód van használatban.|

---

### <a name="toupper"></a>ToUpper
**Függvény**<br> ToUpper (forrás, kulturális környezet)

**Leírás:**<br> Egy *forrás* sztring értékét veszi át, és a megadott kulturális szabályok alapján átalakítja a nagybetűre. Ha nincs megadva *kulturális* információ, akkor a rendszer a semleges kultúrát fogja használni.

**Paraméterek**<br> 

  | Name | Szükséges/ismétlődő | Típus | Jegyzetek |
  | --- | --- | --- | --- |
  | **forrás** |Kötelező |Sztring |Az attribútum neve általában a forrásoldali objektumban. |
  | **kulturális környezet** |Választható |Sztring |Az RFC 4646 alapján a kulturális név formátuma *languagecode2-ország/regioncode2*, ahol a *languagecode2* a kétbetűs nyelvi kód, az *ország/regioncode2* pedig a kétbetűs alkulturális kód. Ilyenek például a japán (Japán) és az en-US angol (Egyesült Államok). Azokban az esetekben, amikor a kétbetűs nyelvi kód nem érhető el, az ISO 639-2-ből származtatott hárombetűs kód van használatban.|

---

### <a name="trim"></a>Trim
**Leírás:**  
A Trim függvény eltávolítja a kezdő és záró szóközöket egy karakterláncból.

**Szintaxis**  
`str Trim(str value)`  

**Példa**  
`Trim(" Test ")`  
A "test" értéket adja vissza.

`Trim([proxyAddresses])`  
Eltávolítja a kezdő és záró szóközöket a proxyAddress attribútum minden értékéhez.

---
### <a name="word"></a>Word
**Leírás:**  
A Word függvény egy karakterláncon belül található szót ad vissza, amely a használni kívánt határolójeleket és a visszaadni kívánt szó paramétereit írja le.

**Szintaxis**  
`str Word(str string, num WordNumber, str delimiters)`

* string: az a karakterlánc, amelyből a szót vissza kell adni.
* WordNumber: az a szám, amelyből a szót vissza kell adni.
* határolójelek: a szavak azonosítására szolgáló elválasztó karakter (eke) t jelölő sztring

**Megjegyzéseket tartalmazó**  
A rendszer az elválasztó karakterek egyikével elválasztott karakterláncban szereplő összes karakterláncot szavakként azonosítja:

* Ha a szám < 1, üres karakterláncot ad vissza.
* Ha a sztring null, üres karakterláncot ad vissza.

Ha a karakterlánc kevesebb, mint szám szót tartalmaz, vagy a sztring nem tartalmaz határolójelekkel azonosított szavakat, akkor a rendszer üres karakterláncot ad vissza.

**Példa**  
`Word("The quick brown fox",3," ")`  
A "barna" értéket adja vissza.

`Word("This,string!has&many separators",3,",!&#")`  
Visszatérési érték: "has"

## <a name="examples"></a>Példák
### <a name="strip-known-domain-name"></a>Szalag ismert tartományneve
A Felhasználónév beszerzéséhez a felhasználó e-mail-címéből egy ismert tartománynevet kell megadnia. <br>
Ha például a tartomány "contoso.com", akkor a következő kifejezést használhatja:

**Kifejezés** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Minta bemenet/kimenet:** <br>

* **Bemenet** (e-mail): " john.doe@contoso.com "
* **Kimenet**: "John. DOE"

### <a name="append-constant-suffix-to-user-name"></a>Állandó utótag hozzáfűzése a felhasználónévhez
Ha Salesforce-munkaterületet használ, előfordulhat, hogy a szinkronizálás előtt hozzá kell fűzni egy további utótagot az összes felhasználónevehöz.

**Kifejezés** <br>
`Append([userPrincipalName], ".test")`

**Minta bemenet/kimenet:** <br>

* **Bemenet**: (userPrincipalName): " John.Doe@contoso.com "
* **Kimenet**: " John.Doe@contoso.com.test "

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Felhasználói alias előállítása az utónév és a vezetéknév összefűzésével
Felhasználói aliast kell létrehoznia úgy, hogy az első 3 betűt a felhasználó utónevét és az első 5 betűt adja meg.

**Kifejezés** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Minta bemenet/kimenet:** <br>

* **Bemenet** (givenName): "John"
* **Bemenet** (vezetéknév): "DOE"
* **Kimenet**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Mellékjelek eltávolítása egy sztringből
Az ékezetes jeleket tartalmazó karaktereket olyan karakterekkel kell helyettesíteni, amelyek nem tartalmaznak ékezetes jeleket.

**Kifejezés** <br>
NormalizeDiacritics ([givenName])

**Minta bemenet/kimenet:** <br>

* **Bemenet** (givenName): "Zoë"
* **Kimenet**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Sztring felosztása többértékű tömbbe
A karakterláncok vesszővel tagolt listáját kell megadnia, és azokat egy olyan tömbbe kell bontani, amely egy többértékű attribútumhoz, például a Salesforce PermissionSets attribútumához csatlakoztatható. Ebben a példában az extensionAttribute5 az Azure AD-ben az engedélyezési készletek listája lett feltöltve.

**Kifejezés** <br>
Split ([extensionAttribute5], ",")

**Minta bemenet/kimenet:** <br>

* **Bemenet** (extensionAttribute5): "PermissionSetOne, PermissionSetTwo"
* **Kimenet**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Kimeneti dátum karakterláncként egy adott formátumban
Bizonyos formátumban szeretné elküldeni a dátumokat egy SaaS-alkalmazásnak. <br>
Például a ServiceNow dátumát szeretné formázni.

**Kifejezés** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Minta bemenet/kimenet:**

* **Bemenet** (extensionAttribute1): "20150123105347.1 z"
* **Kimenet**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Érték cseréje előre megadott beállítások alapján

Meg kell határoznia a felhasználó időzónáját az Azure AD-ben tárolt állapot kódja alapján. <br>
Ha az állapotkód nem egyezik az előre definiált beállításokkal, használja az "Australia/Sydney" alapértelmezett értékét.

**Kifejezés** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Minta bemenet/kimenet:**

* **Bemenet** (állapot): "QLD"
* **Kimenet**: "Ausztrália/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Karakterek cseréje reguláris kifejezés használatával
Meg kell keresnie a reguláris kifejezés értékének megfelelő karaktereket, és el kell távolítani őket.

**Kifejezés** <br>

Replace ([mailNickname],, "[a-zA-Z_] *",, "",,)

**Minta bemenet/kimenet:**

* **Bemenet** (mailNickname: "john_doe72"
* **Kimenet**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Generált userPrincipalName (UPN) értékének kisbetűvé alakítása
Az alábbi példában az UPN-érték a PreferredFirstName és a PreferredLastName forrás mezőinek összefűzésével jön létre, és a ToLower függvény a generált karakterláncon működik, hogy az összes karaktert kisbetűvé alakítsa. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Minta bemenet/kimenet:**

* **Bemenet** (PreferredFirstName): "John"
* **Bemenet** (PreferredLastName): "Kovács"
* **Kimenet**: " john.smith@contoso.com "

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Egyedi érték előállítása a userPrincipalName (UPN) attribútumhoz
A felhasználó utóneve, középső neve és vezetékneve alapján értéket kell létrehoznia az UPN-attribútumhoz, és meg kell adnia annak egyediségét a cél AD-címtárban, mielőtt az értéket az UPN-attribútumhoz rendeli.

**Kifejezés** <br>

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


## <a name="next-steps"></a>Következő lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)