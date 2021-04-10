---
title: Azure Service Bus előfizetési szabály SQL-műveletének szintaxisa | Microsoft Docs
description: Ez a cikk az SQL-szabály műveleti szintaxisára mutató hivatkozást tartalmaz. A műveletek olyan SQL-nyelv-alapú szintaxisban íródnak, amely egy üzeneten történik.
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: 75ff437bace59d7f4de07342277f0760480a5b0f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652836"
---
# <a name="subscription-rule-sql-action-syntax"></a>Előfizetési szabály SQL-műveletének szintaxisa

A rendszer egy *SQL-műveletet* használ az üzenetek metaadatainak kezeléséhez, miután egy előfizetési szabály szűrője kiválasztott egy üzenetet. Ez egy szöveges kifejezés, amely az SQL-92 szabvány egy részhalmazára támaszkodik. A műveleti kifejezések a `sqlExpression` Azure Resource Manager sablonban található Service Bus "Action" tulajdonságának elemével `Rule` vagy az [](service-bus-resource-manager-namespace-topic-with-rule.md)Azure CLI `az servicebus topic subscription rule create` parancs [`--action-sql-expression`](/cli/azure/servicebus/topic/subscription/rule#az_servicebus_topic_subscription_rule_create) ARGUMENTUMával, valamint számos olyan SDK-függvénnyel használhatók, amelyek lehetővé teszik az előfizetés-szabályok kezelését.
  
  
```  
<statements> ::=
    <statement> [, ...n]  
  
```  
  
```  
<statement> ::=
    <action> [;]
    Remarks
    -------
    Semicolon is optional.  
  
```  
  
```  
<action> ::=
    SET <property> = <expression>
    REMOVE <property>  
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
  
### <a name="remarks"></a>Megjegyzések  

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
  
 Ez olyan karakterláncot jelent, amely betűvel kezdődik, és egy vagy több aláhúzás/betű/számjegy követi.  
  
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
  
-   `<integer_constant>` egy olyan számsorozat, amely nem idézőjelek közé esik, és nem tartalmaz tizedes pontokat. Az értékeket belsőleg tárolja a rendszer `System.Int64` , és ugyanazt a tartományt követi.  
  
     A következő példák hosszú állandókat mutatnak be:  
  
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
  
A logikai konstansok a kulcsszavak vagy a alapján jelennek meg `TRUE` `FALSE` . Az értékek a következőképpen tárolódnak: `System.Boolean` .  
  
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

## <a name="examples"></a>Példák
Példák: [Service Bus szűrési példák](service-bus-filter-examples.md).
  
## <a name="considerations"></a>Megfontolandó szempontok

- A SET egy új tulajdonság létrehozására szolgál, vagy egy meglévő tulajdonság értékét frissíti.
- Az Eltávolítás a tulajdonságok eltávolítására szolgál.
- Ha lehetséges, akkor a SET implicit konverziót hajt végre, ha a kifejezés típusa és a meglévő tulajdonság típusa eltérő.
- A művelet meghiúsul, ha nem létező Rendszertulajdonságok lettek hivatkozva.
- A művelet nem sikerül, ha a nem létező felhasználói tulajdonságok hivatkoztak.
- A nem létező felhasználói tulajdonságokat belsőleg "ismeretlen" értékként értékeli ki a rendszer, és a [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) a kezelők kiértékelése során megjelenő szemantikat követve.

## <a name="next-steps"></a>Következő lépések

- [SQLRuleAction osztály (.NET-keretrendszer)](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [SQLRuleAction osztály (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlruleaction)
- [SqlRuleAction osztály (Java)](/java/api/com.microsoft.azure.servicebus.rules.sqlruleaction)
- [SqlRuleAction (JavaScript)](/javascript/api/@azure/service-bus/sqlruleaction)
- [`az servicebus topic subscription rule`](/cli/azure/servicebus/topic/subscription/rule)
- [Új – AzServiceBusRule](/powershell/module/az.servicebus/new-azservicebusrule)