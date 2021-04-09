---
title: Előfizetési szűrők beállítása a Azure Service Busban | Microsoft Docs
description: Ez a cikk példákat tartalmaz a szűrők és műveletek definiálására Azure Service Bus témakör-előfizetésekben.
ms.topic: how-to
ms.date: 02/17/2021
ms.openlocfilehash: bcbb72901ed8e2dfe0932163ee18683e0011ce70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100654394"
---
# <a name="set-subscription-filters-azure-service-bus"></a>Előfizetési szűrők beállítása (Azure Service Bus)
Ez a cikk néhány példát mutat be a szűrők beállítására Service Bus témakör-előfizetések esetében. A szűrőkkel kapcsolatos elméleti információk: [szűrők](topic-filters.md).

## <a name="filter-on-system-properties"></a>Szűrés a rendszer tulajdonságainál
Ha egy szűrő rendszertulajdonságára szeretne hivatkozni, használja a következő formátumot: `sys.<system-property-name>` . 

```csharp
sys.label LIKE '%bus%'`
sys.messageid = 'xxxx'
sys.correlationid like 'abc-%'
```

## <a name="filter-on-message-properties"></a>Szűrés az üzenet tulajdonságainál
Íme néhány példa az üzenet tulajdonságainak egy szűrőben való használatára. Az üzenet tulajdonságai a vagy a használatával érhetők el `user.property-name` `property-name` .

```csharp
MessageProperty = 'A'
SuperHero like 'SuperMan%'
```

## <a name="filter-on-message-properties-with-special-characters"></a>Az üzenet tulajdonságainak szűrése speciális karakterekkel
Ha az üzenet tulajdonságának neve speciális karaktereket tartalmaz, használjon idézőjeleket ( `"` ) a tulajdonság nevének a megadásához. Ha például a tulajdonság neve `"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName"` , használja a következő szintaxist a szűrőben. 

```csharp
"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName" = 'account'
```

## <a name="filter-on-message-properties-with-numeric-values"></a>Az üzenet tulajdonságainak szűrése numerikus értékekkel
Az alábbi példák bemutatják, hogyan használhatók a tulajdonságok numerikus értékekkel a szűrőkben. 

```csharp
MessageProperty = 1
MessageProperty > 1
MessageProperty > 2.08
MessageProperty = 1 AND MessageProperty2 = 3
MessageProperty = 1 OR MessageProperty2 = 3
```

## <a name="parameter-based-filters"></a>Paramétereken alapuló szűrők
Íme néhány példa a paramétereken alapuló szűrők használatára. Ezekben a példákban `DataTimeMp` egy típusú üzenet tulajdonság, amely `DateTime` egy `@dtParam` objektumként a szűrőnek átadott paraméter `DateTime` .

```csharp
DateTimeMp < @dtParam
DateTimeMp > @dtParam

(DateTimeMp2-DateTimeMp1) <= @timespan //@timespan is a parameter of type TimeSpan
DateTimeMp2-DateTimeMp1 <= @timespan
```

## <a name="using-in-and-not-in"></a>Használata a-ben és nem

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

C#-minta esetén tekintse [meg a githubon a témakör szűrők minta](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters)című szakaszát.


## <a name="correlation-filter-using-correlationid"></a>Korrelációs szűrő a CorrelationID használatával

```csharp
new CorrelationFilter("Contoso");
```

A beállítással rendelkező üzeneteket szűri `CorrelationID` `Contoso` . 

## <a name="correlation-filter-using-system-and-user-properties"></a>Korrelációs szűrő a rendszer és a felhasználó tulajdonságaival

```csharp
var filter = new CorrelationFilter();
filter.Label = "Important";
filter.ReplyTo = "johndoe@contoso.com";
filter.Properties["color"] = "Red";
```

A következővel egyenértékű: `sys.ReplyTo = 'johndoe@contoso.com' AND sys.Label = 'Important' AND color = 'Red'`

## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő mintákat: 

- [.NET – alapszintű küldési és fogadási útmutató szűrőkkel](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET – témakör szűrők](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [Azure Resource Manager-sablon](/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)