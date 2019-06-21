---
title: Az Azure Active Directoryban attribútumleképezések kifejezések írása |} A Microsoft Docs
description: Ismerje meg, hogyan SaaS-alkalmazás objektumok az Azure Active Directoryban az Automatikus kiépítés során elfogadható formátumot attribútumértékek alakítsa át a kifejezés-leképezések használatával.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12b75c2df7d11b0e90c5dccc3bc2aae4e0fb0c1e
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204483"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Az Azure Active Directoryban attribútumleképezések kifejezések írása
Amikor konfigurál egy SaaS-alkalmazáshoz való üzembe helyezést, az Ön által megadott attribútum-leképezéshez típusú egyik egy kifejezés-hozzárendelést. Ezeknél a parancsfájl-szerű kifejezés, amely lehetővé teszi, hogy a felhasználók adatokat alakíthatja, amelyek esetében a SaaS-alkalmazás több elfogadható formátumok kell írnia.

## <a name="syntax-overview"></a>Szintaxis áttekintése
Attribútum-leképezéshez kifejezések szintaxisa reminiscent a Visual Basic függvényeihez kapcsolódó alkalmazások (VBA).

* A teljes kifejezésnek funkciók, amelyek követi zárójelben argumentumok nevét kell meghatározni: <br>
  *Függvénynév (`<<argument 1>>`,`<<argument N>>`)*
* Függvények beágyazhatók egymásba előfordulhat, hogy ágyazhatók egymásba. Példa: <br> *FunctionOne (FunctionTwo (`<<argument1>>`))*
* Az funkciók három eltérő típusú argumentumokat adhat át:
  
  1. Attribútumok, amelyek szögletes zárójelek közé kell tenni. Például: [attributeName]
  2. A karakterlánc-állandókat, amelyek dupla idézőjelek közé kell tenni. Példa: "Egyesült Államok"
  3. Más funkciók. Példa: FunctionOne (`<<argument1>>`, FunctionTwo (`<<argument2>>`))
* A karakterlánc-állandókat Ha egy fordított perjel (\) vagy az idézőjel (") a karakterláncban van szüksége, kell megjelölni a fordított perjel (\) szimbólum. Példa: "Cég neve: \\"Contoso\\""

## <a name="list-of-functions"></a>Függvények listája.
[Hozzáfűzés](#append) &nbsp; &nbsp; &nbsp; &nbsp; [FormatDateTime](#formatdatetime) &nbsp; &nbsp; &nbsp; &nbsp; [csatlakozzon](#join) &nbsp; &nbsp; &nbsp; &nbsp; [Mid](#mid) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [NormalizeDiacritics](#normalizediacritics) [nem](#not) &nbsp; &nbsp; &nbsp; &nbsp; [cseréje](#replace) &nbsp; &nbsp; &nbsp; &nbsp; [SelectUniqueValue](#selectuniquevalue) &nbsp; &nbsp; &nbsp; &nbsp; [SingleAppRoleAssignment](#singleapproleassignment) &nbsp; &nbsp; &nbsp; &nbsp; [Split](#split) &nbsp; &nbsp; &nbsp; &nbsp; [ StripSpaces](#stripspaces) &nbsp; &nbsp; &nbsp; &nbsp; [kapcsoló](#switch) &nbsp; &nbsp; &nbsp; &nbsp; [ToLower](#tolower) &nbsp; &nbsp; &nbsp; &nbsp; [ToUpper](#toupper)

---
### <a name="append"></a>Hozzáfűzés
**Függvény:**<br> Append(Source, suffix)

**Leírás:**<br> Forrás karakterlánc értéket vesz fel, és az utótagot fűz a végén.

**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Karakterlánc |Általában az attribútum neve, az adatforrás-objektum. |
| **suffix** |Szükséges |Karakterlánc |Az adatforrás-értéke végére hozzáfűzni kívánt karakterlánc. |

---
### <a name="formatdatetime"></a>formatDateTime
**Függvény:**<br> FormatDateTime (forrás, inputFormat, outputFormat)

**Leírás:**<br> Egy adott formátumból egy dátum karakterláncot vesz fel, és a egy másik formátumba konvertálja.

**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Karakterlánc |Általában az attribútum neve, az adatforrás-objektum. |
| **inputFormat** |Szükséges |Karakterlánc |Az adatforrás-értéke formátumával. Tekintse meg a támogatott formátumok [ https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx ](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** |Szükséges |Karakterlánc |A kimeneti dátum formátuma. |

---
### <a name="join"></a>Csatlakozás
**Függvény:**<br> Csatlakozás (elválasztó, source1, source2,...)

**Leírás:**<br> JOIN() hasonlít Append(), azzal a különbséggel, hogy több képes kombinálni **forrás** karakterláncot egyetlen karakterlánccá értéket, majd az egyes értékek fogja elválasztani a **elválasztó** karakterlánc.

Ha a forrás-értékeket egy többértékű attribútumot, majd minden egyes értékhez ezt az attribútumot a tartományhoz fog csatlakozni együttesen elválasztó értéke elválasztva.

**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **separator** |Szükséges |Karakterlánc |Forrásértékek elválasztó, amikor azok vannak összefűzött egy karakterlánc, karakterlánc. Lehet "", ha nincs elválasztó nem szükséges. |
| **source1... sourceN** |Szükség esetén a változó-hányszor |Karakterlánc |A karakterlánc-értékek egymáshoz csatlakoztatni. |

---
### <a name="mid"></a>Mid
**Függvény:**<br> Mid (forrás, indítás, hossz)

**Leírás:**<br> Az érték egy részét adja vissza. Egy karakterláncrészletet csak egy részét a forrás-karakterlánc származó karaktereket tartalmazó karakterláncot.

**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Karakterlánc |Általában az attribútum neve. |
| **start** |Szükséges |egész szám |Az index a **forrás** karakterláncot, ahol karakterláncrészletet el kell indulnia. A karakterlánc első karaktere 1 index fog rendelkezni, a második karaktere index 2 rendelkezik, és így tovább. |
| **Hossza** |Szükséges |egész szám |A karakterláncrész hossza. Ha hossza kívül ér véget a **forrás** karakterlánc, a függvény a karakterláncrészt adja vissza **indítsa el** végén indextől **forrás** karakterlánc. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Függvény:**<br> NormalizeDiacritics(source)

**Leírás:**<br> Egy karakterlánc-argumentum szükséges. A karakterláncot ad vissza, de bármilyen diakritikus karakterrel egyenértékű nem diakritikus karakterek cserélni. Jogi különböző felhasználói azonosítókat, például az egyszerű felhasználónevek, SAM-fiók nevét és e-mail címek felhasználható értékekké jellemzően keresztnevét és vezetéknevét diakritikus karaktert (ékezetes) konvertálni.

**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |String | Általában egy Utónév vagy utolsó név attribútum. |

---
### <a name="not"></a>nem
**Függvény:**<br> Not(Source)

**Leírás:**<br> Logikai érték, amely tükrözi a **forrás**. Ha **forrás** értéke "*igaz*", adja vissza "*hamis*". Ellenkező esetben adja vissza "*igaz*".

**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Logikai típusú karakterlánc |A várt **forrás** értékek: "True" vagy "False". |

---
### <a name="replace"></a>Csere
**Függvény:**<br> Cserélje le a (forrás, oldValue, regexPattern, regexGroupName, helyettesítő értéke, replacementAttributeName, sablon)

**Leírás:**<br>
Lecseréli az értékeket egy karakterláncból. A megadott paraméterek függően eltérően működik:

* Amikor **oldValue** és **helyettesítő értéke** biztosított:
  
  * Lecseréli az összes előfordulását a forrás oldValue helyettesítő értéke
* Amikor **oldValue** és **sablon** biztosított:
  
  * Összes előfordulását lecseréli a **oldValue** a a **sablon** együtt a **forrás** érték
* Amikor **regexPattern**, **regexGroupName**, **helyettesítő értéke** biztosított:
  
  * Lecseréli az összes értékeket oldValueRegexPattern helyettesítő értéke a forrás karakterláncot az egyező
* Amikor **regexPattern**, **regexGroupName**, **replacementPropertyName** biztosított:
  
  * Ha **forrás** nem rendelkezik értékkel, **forrás** adja vissza
  * Ha **forrás** értékkel rendelkezik, használja a **regexPattern** és **regexGroupName** érték kinyerésére rendelkező tulajdonság **replacementPropertyName** . Helyettesítő értéket adja vissza eredményként

**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Karakterlánc |Általában az attribútum neve, az adatforrás-objektum. |
| **oldValue** |Optional |Karakterlánc |Értéket kell cserélni a **forrás** vagy **sablon**. |
| **regexPattern** |Optional |Karakterlánc |Az érték cseréli a Regex minta **forrás**. Vagy, ha replacementPropertyName használják, csere tulajdonság értékének kinyerése mintát. |
| **regexGroupName** |Optional |Karakterlánc |A csoport a nevét **regexPattern**. Csak akkor, ha replacementPropertyName használják, ez a csoport értékét azt fogja bontsa ki, helyettesítő helyettesítő tulajdonság értéke. |
| **replacementValue** |Optional |Karakterlánc |Új értéket cserélje le a régit. |
| **replacementAttributeName** |Optional |Karakterlánc |Használandó érték, ha a forrás nem rendelkezik értékkel az attribútum neve. |
| **sablon** |Optional |Karakterlánc |Amikor **sablon** érték van megadva, alábbiakban tájékozódhat **oldValue** belül a sablont, és cserélje le az adatforrás-értéke. |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Függvény:**<br> SelectUniqueValue (uniqueValueRule1, uniqueValueRule2, uniqueValueRule3,...)

**Leírás:**<br> Legalább két argumentumot, amelyek egyedi érték létrehozási szabályok definiált kifejezések használatával van szükség. A függvény minden egyes szabály kiértékeli, és ellenőrzi, az érték egyedi-e a cél alkalmazás/könyvtárban jönnek létre. Az első egyedi érték található egy adja vissza. Összes érték már létezik a célkiszolgálón, ha a bejegyzés lesz első szétválasztást és okát az auditnaplókban rendszer naplózza. Nincs megadható argumentumok számának felső korlátja.

> [!NOTE]
>1. Ez egy legfelső szintű függvényt, nem ágyazhatók egymásba.
>2. Ez a függvény csak hivatott bejegyzés létrehozások használható. Használhatja azt egy attribútumot, ha a **leképezése a alkalmazni** tulajdonságot **csak objektum létrehozásakor**.


**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **uniqueValueRule1... uniqueValueRuleN** |Legalább a 2 szükséges, nem felső határérték |String | Egyedi érték létrehozási szabályok kiértékelése listája. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Függvény:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Leírás:**<br> Egyetlen appRoleAssignment rendelve egy felhasználó egy adott alkalmazás összes hozzárendeléseinek listáját adja vissza. Ez a függvény van szükség az alkalmazások szerepkör-hozzárendelései objektum átalakítása egy egységes szerepkör karakterláncát. Vegye figyelembe, hogy a legjobb módszer annak biztosítására, csak egy appRoleAssignment egyszerre egy felhasználóhoz rendelt, és ha több szerepkör hozzá van rendelve a visszaadott szerepkör karakterlánc nem lehet előre jelezhető. 

**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **[hozzárendeléseinek]** |Szükséges |Karakterlánc |**[hozzárendeléseinek]**  objektum. |

---
### <a name="split"></a>Felosztás
**Függvény:**<br> Split (forrás, elválasztó karakter)

**Leírás:**<br> Egy karakterlánc felosztja a mulit értékű tömb a megadott elválasztó karakter használatával.

**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Karakterlánc |**forrás** érték frissítéséhez. |
| **delimiter** |Kötelező |String |Adja meg a karakter, amely a sztring felosztásához használandó (Példa: ",") |

---
### <a name="stripspaces"></a>StripSpaces
**Függvény:**<br> StripSpaces(source)

**Leírás:**<br> Eltávolítja az összes szóközt ("") karaktert a forrás karakterláncot.

**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Karakterlánc |**forrás** érték frissítéséhez. |

---
### <a name="switch"></a>Kapcsoló
**Függvény:**<br> Switch (forrás, defaultValue, 1. kulcs, érték1, 2. kulcs, value2,...)

**Leírás:**<br> Amikor **forrás** értéke megegyezik egy **kulcs**, adja vissza **érték** , amely a **kulcs**. Ha **forrás** értéke nem egyezik a bármely kulcsok beolvasása **defaultValue**.  **Kulcs** és **érték** paraméterek mindig párosával kell származnia. A függvény mindig paraméterek páros számú vár.

**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Karakterlánc |**forrás** érték frissítéséhez. |
| **defaultValue** |Optional |Karakterlánc |Alapértelmezett érték használható, ha a forrás nem felel meg minden olyan kulcsokat. Üres karakterlánc lehet (""). |
| **key** |Szükséges |Karakterlánc |**Kulcs** összehasonlítására **forrás** értéket. |
| **value** |Szükséges |Karakterlánc |Az érték a **forrás** összekapcsolja a kulcsot. |

---
### <a name="tolower"></a>toLower
**Függvény:**<br> ToLower (forrás, kulturális környezet)

**Leírás:**<br> Vesz igénybe egy *forrás* értékből, és konvertálja azt a kulturális környezet használatával kisbetűsre szabályok, amelyek a megadott karakterlánc. Ha nincs *kulturális környezet* adatai megadva, Invariáns kulturális környezetet fogja használni.

**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Karakterlánc |Általában az attribútum az az adatforrás-objektum neve |
| **culture** |Optional |String |A kulturális környezet neve alapján RFC 4646 formátuma *languagecode2 – ország/regioncode2*, ahol *languagecode2* a kétbetűs nyelvi kódja és *ország/regioncode2*a kétbetűs szubkultúrákhoz kódja. Például ja-JP Japán (japán) és a hu-hu az angol (Egyesült Államok). Azokban az esetekben, ahol a kétbetűs nyelvkód nem érhető el egy ISO 639-2 származó hárombetűs kódot használja.|

---
### <a name="toupper"></a>ToUpper
**Függvény:**<br> ToUpper (forrás, kulturális környezet)

**Leírás:**<br> Vesz igénybe egy *forrás* értékből, és konvertálja azt a kulturális környezet használatával nagybetűs szabályok, amelyek a megadott karakterlánc. Ha nincs *kulturális környezet* adatai megadva, Invariáns kulturális környezetet fogja használni.

**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Karakterlánc |Általában az attribútum neve, az adatforrás-objektum. |
| **culture** |Optional |String |A kulturális környezet neve alapján RFC 4646 formátuma *languagecode2 – ország/regioncode2*, ahol *languagecode2* a kétbetűs nyelvi kódja és *ország/regioncode2*a kétbetűs szubkultúrákhoz kódja. Például ja-JP Japán (japán) és a hu-hu az angol (Egyesült Államok). Azokban az esetekben, ahol a kétbetűs nyelvkód nem érhető el egy ISO 639-2 származó hárombetűs kódot használja.|

## <a name="examples"></a>Példák
### <a name="strip-known-domain-name"></a>Sáv ismert tartománynév
Szerezzen be egy felhasználónevet a felhasználó e-mailben egy ismert tartománynévnek sáv kell. <br>
Például ha a tartomány a "contoso.com", majd használhatja a következő kifejezést:

**Kifejezés:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Bemeneti / kimeneti mintát:** <br>

* **BEMENETI** (levelezés): "john.doe@contoso.com"
* **KIMENETI**: "john.doe"

### <a name="append-constant-suffix-to-user-name"></a>Állandó utótagok hozzáfűzése a felhasználónevet
Ha egy Salesforce védőfal használja, szüksége lehet egy további utótagok hozzáfűzése a felhasználónevek őket szinkronizálása előtt.

**Kifejezés:** <br>
`Append([userPrincipalName], ".test")`

**Bemeneti/kimeneti. példa:** <br>

* **BEMENETI**: (userPrincipalName): "John.Doe@contoso.com"
* **KIMENETI**: "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Utónév és Vezetéknév összetűzésének részei felhasználói alias létrehozása
Létre kell hoznia egy felhasználói alias, a felhasználó utónevét első 3 betűket és a felhasználó vezetéknevét első 5 karakterét végrehajtásával.

**Kifejezés:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Bemeneti/kimeneti. példa:** <br>

* **BEMENETI** (givenName): "János"
* **BEMENETI** (Vezetéknév): "Jakab"
* **KIMENETI**:  "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Távolítsa el az e mellékjeleket egy karakterláncból.
Cserélje le a megfelelő karakterek, ékezetes nem tartalmazó ékezetes karakter hosszúságúnak kell.

**Kifejezés:** <br>
NormalizeDiacritics([givenName])

**Bemeneti/kimeneti. példa:** <br>

* **BEMENETI** (givenName): "Zoë"
* **KIMENETI**:  "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Egy sztring felosztása többértékű tömb
Karakterláncok vesszővel tagolt listáját, illetve ossza fel őket egy tömb, amely egy többértékű attribútum, például a Salesforce PermissionSets attribútumot lehet csatlakoztatni kell. Ebben a példában egy engedélycsoportok listájában feltöltődtek az extensionAttribute5 az Azure ad-ben.

**Kifejezés:** <br>
Split ([extensionAttribute5], ",")

**Bemeneti/kimeneti. példa:** <br>

* **BEMENETI** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
* **KIMENETI**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Egy bizonyos formátumú karakterlánc formájában kimeneti dátum
Egy SaaS-alkalmazás egy bizonyos formátumú dátumok küldeni szeretné. <br>
Ha például szeretné formátumához servicenow-hoz készült.

**Kifejezés:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Bemeneti/kimeneti. példa:**

* **BEMENETI** (extensionAttribute1): "20150123105347.1Z"
* **KIMENETI**:  "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Cserélje le az előre megadott beállítások alapján értéket

Adja meg a felhasználó az Azure AD-ben tárolt kódja alapján az időzóna kell. <br>
Ha az állapot-kód nem egyezik az előre definiált beállításokat, használja a "Ausztrália/Sydney" alapértelmezett értékét.

**Kifejezés:** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Bemeneti/kimeneti. példa:**

* **BEMENETI** (állapot): "QLD"
* **KIMENETI**: "Ausztrália/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Cserélje le a karaktereket a reguláris kifejezés használata
Meg kell keresnie a karakter, amely egy reguláris kifejezés érték egyezik, és távolítsa el őket.

**Kifejezés:** <br>

Cserélje le ([mailNickname,], "[a-zA-Z_] *", "",)

**Bemeneti/kimeneti. példa:**

* **BEMENETI** (mailNickname: "john_doe72"
* **KIMENETI**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Generált userPrincipalName (UPN) érték átalakítása kisbetű
Az alábbi példában az egyszerű felhasználónevet a PreferredFirstName és PreferredLastName forrás mezők összefűzésével jön létre, és a ToLower függvény a létrehozott karakterlánc összes karakter átalakítása kisbetű működik. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Bemeneti/kimeneti. példa:**

* **BEMENETI** (PreferredFirstName): "János"
* **BEMENETI** (PreferredLastName): "Smith"
* **KIMENETI**: "john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Egyedi érték a userPrincipalName (UPN) attribútum létrehozása
Alapján a felhasználó utónevét, a középső név és vezetéknevét, létre kell hoznia az UPN attribútum értékét, és ellenőrizze az egyedi-e a cél az AD címtárban az UPN attribútum értéke hozzárendelése előtt.

**Kifejezés:** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com")
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Bemeneti/kimeneti. példa:**

* **BEMENETI** (PreferredFirstName): "János"
* **BEMENETI** (PreferredLastName): "Smith"
* **KIMENETI**: "John.Smith@contoso.com" Ha UPN értékét John.Smith@contoso.com még nem létezik a címtárban
* **KIMENETI**: "J.Smith@contoso.com" Ha UPN értékét John.Smith@contoso.com a könyvtár már létezik
* **KIMENETI**: "Jo.Smith@contoso.com" Ha a fenti két UPN-érték már létezik a címtárban

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Felhasználói kiépítés és megszüntetés SaaS-alkalmazások automatizálása](user-provisioning.md)
* [A felhasználók átadásának attribútumleképezések testreszabása](customize-application-attributes.md)
* [A felhasználók átadásának Hatókörszűrő](define-conditional-rules-for-provisioning-user-accounts.md)
* [SCIM használata a felhasználók és csoportok automatikus üzembe helyezésének engedélyezéséhez az Azure Active Directoryból az alkalmazásokba](use-scim-to-provision-users-and-groups.md)
* [Alkalmazáskiépítési értesítések](user-provisioning.md)
* [Az SaaS-alkalmazások integrálásával foglalkozó oktatóanyagok listája](../saas-apps/tutorial-list.md)
