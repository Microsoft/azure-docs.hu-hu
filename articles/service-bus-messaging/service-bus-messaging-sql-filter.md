---
title: Azure Service Bus előfizetési szabály SQL-szűrő szintaxisa | Microsoft Docs
description: Ez a cikk az SQL-szűrési nyelvtan részleteit ismerteti. Az SQL-szűrők az SQL-92 szabvány egy részhalmazát támogatják.
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: 022f6cb1d698a10dc216db8d41c172691f7535ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100652941"
---
# <a name="subscription-rule-sql-filter-syntax"></a>Előfizetési szabály SQL-szűrési szintaxisa

Az *SQL-szűrő* a Service Bus témakör-előfizetések egyik elérhető szűrőtípus. Ez egy szöveges kifejezés, amely az SQL-92 szabvány egy részhalmazára támaszkodik. A szűrési kifejezések a `sqlExpression` Azure Resource Manager sablonban található Service Bus "sqlFilter" tulajdonságának elemével `Rule` vagy [](service-bus-resource-manager-namespace-topic-with-rule.md)az Azure CLI `az servicebus topic subscription rule create` parancs [`--filter-sql-expression`](/cli/azure/servicebus/topic/subscription/rule#az_servicebus_topic_subscription_rule_create) ARGUMENTUMával, valamint számos olyan SDK-függvénnyel használhatók, amelyek lehetővé teszik az előfizetés-szabályok kezelését.

Az Service Bus Premium a [JMS SQL-üzenet választójának szintaxisát](https://docs.oracle.com/javaee/7/api/javax/jms/Message.html) is támogatja a JMS 2,0 API-n keresztül.

  
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
  
-   `<scope>` egy opcionális karakterlánc, amely a hatókörét jelzi `<property_name>` . Az érvényes értékek a következők: `sys` vagy `user` . Az `sys` érték azt a rendszerhatókört jelöli, ahol a a `<property_name>` [BrokeredMessage osztály](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)egy nyilvános tulajdonságának neve. `user` Megadja a felhasználói hatókört, ahol a a `<property_name>` [BrokeredMessage osztály](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) szótárának kulcsa. `user` a hatókör az alapértelmezett hatókör, ha `<scope>` nincs megadva.  
  
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
  
`<quoted_identifier>` minden olyan karakterlánc, amely dupla idézőjelekkel van ellátva. Az azonosító dupla idézőjele két idézőjel. Az idézőjelek használata nem ajánlott, mert könnyen összetéveszthető karakterlánc-konstanssal. Ha lehetséges, használjon tagolt azonosítót. Íme egy példa `<quoted_identifier>` :  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>minta  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Megjegyzések
  
`<pattern>` karakterláncként kiértékelt kifejezésnek kell lennie. A LIKE operátor mint mintaként használható.      A következő helyettesítő karaktereket tartalmazhatja:  
  
-   `%`: Nulla vagy több karakterből álló karakterlánc.  
  
-   `_`: Egyetlen karakter.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Megjegyzések  

`<escape_char>` egy olyan kifejezésnek kell lennie, amely 1. hosszúságú sztringként van kiértékelve. A LIKE operátor Escape-karakterként használható.  
  
 Például a `property LIKE 'ABC\%' ESCAPE '\'` egyezés `ABC%` helyett a karakterrel kezdődő sztring szerepel `ABC` .  
  
## <a name="constant"></a>állandó  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumentumok  
  
-   `<integer_constant>` egy olyan számsorozat, amely nem idézőjelek közé esik, és nem tartalmaz tizedes pontokat. Az értékeket belsőleg tárolja a rendszer `System.Int64` , és ugyanazt a tartományt követi.  
  
     Íme néhány példa a hosszú konstansokra:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` egy olyan számokból álló karakterlánc, amely nem idézőjelek közé esik, és tizedes pontot tartalmaz. Az értékeket belsőleg tárolja a rendszer `System.Double` , és kövesse ugyanazt a tartományt/pontosságot.  
  
     Egy későbbi verzióban ezt a számot egy másik adattípusban tárolhatja a pontos számú szemantika támogatásához, ezért nem szabad arra támaszkodnia, hogy az alapul szolgáló adattípus a `System.Double` következő: `<decimal_constant>` .  
  
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

A logikai konstansokat a **true** vagy a **false** kulcsszó jelöli. Az értékek a következőképpen tárolódnak: `System.Boolean` .  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Megjegyzések  

A karakterlánc-konstansok szimpla idézőjelek közé vannak lefoglalva, és tartalmaznak bármilyen érvényes Unicode-karaktert. Egy karakterlánc-konstansba ágyazott idézőjelek két szimpla idézőjelet jelölnek.  
  
## <a name="function"></a>A  függvény  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Megjegyzések
  
A `newid()` függvény a `System.Guid` metódus által generált értéket adja vissza `System.Guid.NewGuid()` .  
  
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
  
- A nem létező tulajdonságok belső kiértékelése **ismeretlenként** történik.  
  
  Ismeretlen kiértékelés az aritmetikai operátorokban:  
  
- A bináris operátorok esetében, ha az operandusok bal vagy jobb oldalán **ismeretlen** érték van kiértékelve, akkor az eredmény **ismeretlen**.  
  
- Egyoperandusú operátorok esetén, ha egy operandust **ismeretlenként** értékelnek ki, akkor az eredmény **ismeretlen**.  
  
  Ismeretlen kiértékelés a bináris összehasonlító operátorok esetében:  
  
- Ha az operandusok bal vagy jobb oldalán az **ismeretlen** érték van kiértékelve, akkor az eredmény **ismeretlen**.  
  
  Ismeretlen kiértékelés `[NOT] LIKE` :  
  
- Ha bármely operandus **ismeretlenként** van kiértékelve, akkor az eredmény **ismeretlen**.  
  
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

## <a name="examples"></a>Példák
Példák: [Service Bus szűrési példák](service-bus-filter-examples.md).

## <a name="next-steps"></a>Következő lépések

- [SQLFilter osztály (.NET-keretrendszer)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [SQLFilter osztály (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [SqlFilter osztály (Java)](/java/api/com.microsoft.azure.servicebus.rules.SqlFilter)
- [SqlRuleFilter (JavaScript)](/javascript/api/@azure/service-bus/sqlrulefilter)
- [`az servicebus topic subscription rule`](/cli/azure/servicebus/topic/subscription/rule)
- [Új – AzServiceBusRule](/powershell/module/az.servicebus/new-azservicebusrule)