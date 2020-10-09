---
title: Azure Service Bus SQLFilter szintaxisának leírása | Microsoft Docs
description: Ez a cikk a SQLFilter nyelvtani adatait ismerteti. A SqlFilter az SQL-92 szabvány egy részhalmazát támogatja.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 8412dea583ae119b30976e53d4751411b45339a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85341599"
---
# <a name="sqlfilter-syntax"></a>SQLFilter-szintaxis

A *SqlFilter* objektum a [SqlFilter osztály](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)egy példánya, és egy SQL Language-alapú szűrési kifejezést jelöl, amelyet a rendszer egy [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)kiértékel. A SqlFilter az SQL-92 szabvány egy részhalmazát támogatja.  
  
 Ez a témakör a SqlFilter nyelvtanának részleteit sorolja fel.  
  
```  
<predicate ::=  
      { NOT <predicate> }  
      | <predicate> AND <predicate>  
      | <predicate> OR <predicate>  
      | <expression> { = | <> | != | > | >= | < | <= } <expression>  
      | <property> IS [NOT] NULL  
      | <expression> [NOT] IN ( <expression> [, ...n] )  
      | <expression> [NOT] LIKE <pattern> [ESCAPE <escape_char>]  
      | EXISTS ( <property> )  
      | ( <predicate> )  
  
```  
  
```  
<expression> ::=  
      <constant>   
      | <function>  
      | <property>  
      | <expression> { + | - | * | / | % } <expression>  
      | { + | - } <expression>  
      | ( <expression> )  
  
```  
  
```  
<property> :=   
       [<scope> .] <property_name>  
  
```  
  
## <a name="arguments"></a>Argumentumok  
  
-   `<scope>` egy opcionális karakterlánc, amely a hatókörét jelzi `<property_name>` . Az érvényes értékek a következők: `sys` vagy `user` . Az `sys` érték azt a rendszerhatókört jelöli, ahol a a `<property_name>` [BrokeredMessage osztály](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)egy nyilvános tulajdonságának neve. `user` Megadja a felhasználói hatókört, ahol a a `<property_name>` [BrokeredMessage osztály](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) szótárának kulcsa. `user` a hatókör az alapértelmezett hatókör, ha nincs `<scope>` megadva.  
  
## <a name="remarks"></a>Megjegyzések

Egy nem létező rendszertulajdonság elérésére tett kísérlet hibát jelez, míg egy nem létező felhasználói tulajdonság elérésére tett kísérlet nem hiba. Ehelyett egy nem létező felhasználói tulajdonságot belsőleg kiértékel a rendszer ismeretlen értékként. Az operátorok kiértékelése során az ismeretlen értéket külön kezeli a rendszer.  
  
## <a name="property_name"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumentumok  

 `<regular_identifier>` a következő reguláris kifejezéssel jelölt karakterlánc:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
Ez a nyelvtan olyan karakterláncot jelent, amely betűvel kezdődik, és egy vagy több aláhúzás/levél/számjegy követi.  
  
`[:IsLetter:]` olyan Unicode-karakter, amely Unicode betűként van kategorizálva. `System.Char.IsLetter(c)` a értéket adja vissza `true` `c` , ha az egy Unicode betű.  
  
`[:IsDigit:]` olyan Unicode-karakter, amely decimális számjegyként van kategorizálva. `System.Char.IsDigit(c)` a értéket adja vissza `true` `c` , ha az egy Unicode számjegy.  
  
A `<regular_identifier>` nem lehet foglalt kulcsszó.  
  
`<delimited_identifier>` a bal/jobb oldali szögletes zárójelek ([]) közé zárt karakterlánc. A jobb oldali szögletes zárójel két jobb oldali szögletes zárójelként jelenik meg. Az alábbi példák a következőkre mutatnak `<delimited_identifier>` :  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>` minden olyan karakterlánc, amely dupla idézőjelekkel van ellátva. Az azonosító dupla idézőjele két idézőjel. Az idézőjelek használata nem ajánlott, mert könnyen összetéveszthető karakterlánc-konstanssal. Ha lehetséges, használjon tagolt azonosítót. Az alábbi példa a következőket szemlélteti `<quoted_identifier>` :  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>minta  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Megjegyzések
  
`<pattern>` karakterláncként kiértékelt kifejezésnek kell lennie. A rendszer mintaként használja a hasonló operátorhoz.      A következő helyettesítő karaktereket tartalmazhatja:  
  
-   `%`: Nulla vagy több karakterből álló karakterlánc.  
  
-   `_`: Egyetlen karakter.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Megjegyzések  

`<escape_char>` egy olyan kifejezésnek kell lennie, amely 1. hosszúságú sztringként van kiértékelve. A hasonló operátorhoz tartozó Escape-karakterként használható.  
  
 Például a `property LIKE 'ABC\%' ESCAPE '\'` egyezés `ABC%` helyett a karakterrel kezdődő sztring szerepel `ABC` .  
  
## <a name="constant"></a>állandó  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumentumok  
  
-   `<integer_constant>` egy olyan számsorozat, amely nem idézőjelek közé esik, és nem tartalmaz decimális pontokat. Az értékeket belsőleg tárolja a rendszer `System.Int64` , és ugyanazt a tartományt követi.  
  
     Ilyenek például a hosszú állandók:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` egy olyan számokból álló karakterlánc, amely nem idézőjelek közé esik, és tizedes pontot tartalmaz. Az értékeket belsőleg tárolja a rendszer `System.Double` , és kövesse ugyanazt a tartományt/pontosságot.  
  
     Egy későbbi verzióban ezt a számot egy másik adattípusban tárolhatja a pontos számú szemantika támogatásához, ezért nem szabad az alapul szolgáló adattípusra támaszkodni `System.Double` `<decimal_constant>` .  
  
     A következő példák decimális konstansokra mutatnak:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` egy tudományos jelöléssel írt szám. Az értékeket belsőleg tárolja a rendszer `System.Double` , és kövesse ugyanazt a tartományt/pontosságot. Az alábbi példákban megközelítheti a szám konstansait:  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="boolean_constant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Megjegyzések  

A logikai konstansokat a **true** vagy a **false**kulcsszó jelöli. Az értékek a következőképpen tárolódnak: `System.Boolean` .  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Megjegyzések  

A karakterlánc-konstansok szimpla idézőjelek közé vannak lefoglalva, és tartalmaznak bármilyen érvényes Unicode-karaktert. Egy karakterlánc-konstansba ágyazott idézőjelek két szimpla idézőjelet jelölnek.  
  
## <a name="function"></a>függvény  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Megjegyzések
  
A `newid()` függvény a metódus által generált **System. GUID azonosítót** adja vissza `System.Guid.NewGuid()` .  
  
A `property(name)` függvény a által hivatkozott tulajdonság értékét adja vissza `name` . Az `name` érték bármely érvényes kifejezés lehet, amely egy karakterlánc-értéket ad vissza.  
  
## <a name="considerations"></a>Megfontolandó szempontok
  
Vegye figyelembe a következő [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) szemantikai műveleteket:  
  
-   A tulajdonságok nevei kis-és nagybetűk megkülönböztetése nélkül.  
  
-   A kezelők a C# implicit átalakítási szemantikai műveletet követik, amikor csak lehetséges.  
  
-   A rendszer tulajdonságai a [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) -példányokban elérhető nyilvános tulajdonságok.  
  
    Vegye figyelembe a következő `IS [NOT] NULL` szemantikai lépéseket:  
  
    -   `property IS NULL` a kiértékelése úgy történik, mintha `true` a tulajdonság nem létezik, vagy a tulajdonság értéke `null` .  
  
### <a name="property-evaluation-semantics"></a>Tulajdonság-értékelési szemantika  
  
- Egy nem létező rendszertulajdonság kiértékelésére tett kísérlet egy [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) kivételt jelez.  
  
- A nem létező tulajdonságok belső kiértékelése **ismeretlenként**történik.  
  
  Ismeretlen kiértékelés az aritmetikai operátorokban:  
  
- A bináris operátorok esetében, ha az operandusok bal és/vagy jobb oldalán az **ismeretlen**érték van kiértékelve, akkor az eredmény **ismeretlen**.  
  
- Egyoperandusú operátorok esetén, ha egy operandust **ismeretlenként**értékelnek ki, akkor az eredmény **ismeretlen**.  
  
  Ismeretlen kiértékelés a bináris összehasonlító operátorok esetében:  
  
- Ha az operandusok bal és/vagy jobb oldalán az **ismeretlen**érték van kiértékelve, akkor az eredmény **ismeretlen**.  
  
  Ismeretlen kiértékelés `[NOT] LIKE` :  
  
- Ha bármely operandus **ismeretlenként**van kiértékelve, akkor az eredmény **ismeretlen**.  
  
  Ismeretlen kiértékelés `[NOT] IN` :  
  
- Ha a bal oldali operandus értéke **ismeretlen**, akkor az eredmény **ismeretlen**.  
  
  Ismeretlen kiértékelés a **és** operátorban:  
  
```  
+---+---+---+---+  
|AND| T | F | U |  
+---+---+---+---+  
| T | T | F | U |  
+---+---+---+---+  
| F | F | F | F |  
+---+---+---+---+  
| U | U | F | U |  
+---+---+---+---+  
```  
  
 Ismeretlen kiértékelés **vagy** operátor:  
  
```  
+---+---+---+---+  
|OR | T | F | U |  
+---+---+---+---+  
| T | T | T | T |  
+---+---+---+---+  
| F | T | F | U |  
+---+---+---+---+  
| U | T | U | U |  
+---+---+---+---+  
```  
  
### <a name="operator-binding-semantics"></a>Operátor kötési szemantika
  
-   Összehasonlító operátorok, mint például a,,,,, `>` `>=` `<` `<=` `!=` és `=` az adattípusok és az implicit konverziók esetében ugyanazokat a szemantikai műveleteket követik, mint a C# operátor kötése.  
  
-   Aritmetikai operátorok, mint például a `+` ,, `-` ,, `*` `/` és `%` ugyanazokat a szemantikai műveleteket követik, mint a C# operátor kötése adattípusú promóciók és implicit konverziók esetében.

## <a name="next-steps"></a>További lépések

- [SQLFilter osztály (.NET-keretrendszer)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [SQLFilter osztály (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [SQLRuleAction osztály](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
