---
title: Azure Traffic Analytics Schema Update – március 2020 | Microsoft Docs
description: A Traffic Analytics sémában lévő új mezőket tartalmazó lekérdezések.
services: network-watcher
documentationcenter: na
author: vinigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: d7c4f1853ff8dcb9249ab6ec4f536e1f8cfa10e5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018224"
---
# <a name="sample-queries-with-new-fields-in-the-traffic-analytics-schema-august-2019-schema-update"></a>A Traffic Analytics sémában új mezőket tartalmazó lekérdezések (a séma frissítése augusztus 2019)

A [Traffic Analytics naplózási sémája](./traffic-analytics-schema.md) a következő új mezőket tartalmazza: **SrcPublicIPs_s**, **DestPublicIPs_s**, **NSGRule_s**. Az új mezők a forrás-és cél IP-címekről nyújtanak információt, és leegyszerűsítik a lekérdezéseket.

A következő néhány hónapban a következő régebbi mezők elavultak lesznek: **VMIP_s**, **Subscription_g**, **Region_s**, **NSGRules_s**, **Subnet_s**, **VM_s**, **NIC_s**, **PublicIPs_s**, **FlowCount_d**.

Az alábbi három példa bemutatja, hogyan cserélheti le a régi mezőket az újakra.

## <a name="example-1-vmip_s-subscription_g-region_s-subnet_s-vm_s-nic_s-and-publicips_s-fields"></a>1. példa: VMIP_s, Subscription_g, Region_s, Subnet_s, VM_s, NIC_s és PublicIPs_s mezők

A AzurePublic és a ExternalPublic folyamatok esetében nem kell következtetni a forrás-és a rendeltetési esetekre a **FlowDirection_s** mezőből. A hálózati virtuális berendezés **FlowDirection_s** mezőjét is nem lehet használni.

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend isAzureOrExternalPublicFlows = FlowType_s in ("AzurePublic", "ExternalPublic")
| extend SourceAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), VM1_s),
SourceAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), SrcIP_s),
SourceAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subscription_g, "N/A"), Subscription1_g),
SourceAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Region_s, "N/A"), Region1_s),
SourceAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subnet_s, "N/A"), Subnet1_s),
SourceAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', NIC_s, "N/A"), NIC1_s),
DestAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), VM2_s),
DestAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), DestIP_s),
DestAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subscription_g, "N/A"), Subscription2_g),
DestAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Region_s, "N/A"), Region2_s),
DestAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subnet_s, "N/A"), Subnet2_s),
DestAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', NIC_s, "N/A"), NIC2_s),
SourcePublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'I', PublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'O', PublicIPs_s, "N/A")
```


```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend SourceAzureVM = iif(isnotempty(VM1_s), VM1_s, "N/A"),
SourceAzureVMIP = iif(isnotempty(SrcIP_s), SrcIP_s, "N/A"),
SourceAzureVMSubscription = iif(isnotempty(Subscription1_g), Subscription1_g, "N/A"),
SourceAzureRegion = iif(isnotempty(Region1_s), Region1_s, "N/A"),
SourceAzureSubnet = iif(isnotempty(Subnet1_s), Subnet1_s, "N/A"),
SourceAzureNIC = iif(isnotempty(NIC1_s), NIC1_s, "N/A"),
DestAzureVM = iif(isnotempty(VM2_s), VM2_s, "N/A"),
DestAzureVMIP = iif(isnotempty(DestIP_s), DestIP_s, "N/A"),
DestAzureVMSubscription = iif(isnotempty(Subscription2_g), Subscription2_g, "N/A"),
DestAzureRegion = iif(isnotempty(Region2_s), Region2_s, "N/A"),
DestAzureSubnet = iif(isnotempty(Subnet2_s), Subnet2_s, "N/A"),
DestAzureNIC = iif(isnotempty(NIC2_s), NIC2_s, "N/A"),
SourcePublicIPsAggregated = iif(isnotempty(SrcPublicIPs_s), SrcPublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isnotempty(DestPublicIPs_s), DestPublicIPs_s, "N/A")
```

## <a name="example-2-nsgrules_s-field"></a>2. példa: NSGRules_s mező

A régi mező a formátumot használta:

`<Index value 0)>|<NSG_ RuleName>|<Flow Direction>|<Flow Status>|<FlowCount ProcessedByRule>`

A továbbiakban nem összesítjük az adatokat egy hálózati biztonsági csoporton (NSG) keresztül. A frissített sémában **NSGList_s** csak egy NSG tartalmaz. A **NSGRules** is csak egy szabályt tartalmaz. A példában látható módon eltávolította a bonyolult formázást itt és más mezőkben.

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| extend NSGName = NSGList_s // remains same
| extend NSGRuleName = NSGRuleComponents[1],
         FlowDirection = NSGRuleComponents[2],
         FlowStatus = NSGRuleComponents[3],
         FlowCountProcessedByRule = NSGRuleComponents[4]
| project NSGName, NSGRuleName, FlowDirection, FlowStatus, FlowCountProcessedByRule
```

```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| project NSGName = NSGList_s,
NSGRuleName = NSGRule_s ,
FlowDirection = FlowDirection_s,
FlowStatus = FlowStatus_s,
FlowCountProcessedByRule = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d
```

## <a name="example-3-flowcount_d-field"></a>3. példa: FlowCount_d mező

Mivel nem a NSG találhatóak, a **FlowCount_d** egyszerűen:

**AllowedInFlows_d**  +  **DeniedInFlows_d**  +  **AllowedOutFlows_d**  +  **DeniedOutFlows_d**

A négy mező közül csak az egyik nem nulla. A másik három mező értéke nulla lesz. A mezők fel vannak töltve, hogy jelezze az állapotot és a darabszámot abban a hálózati adapterben, ahol a folyamat rögzített.

A következő feltételek szemléltetése:

- Ha a folyamat engedélyezve lett, az "engedélyezett" előre rögzített mezők egyike lesz feltöltve.
- Ha a folyamat meg lett tagadva, a "megtagadott" előre rögzített mezők egyike lesz feltöltve.
- Ha a folyamat bejövő volt, a rendszer a "InFlows_d" utótagú mezők egyikét tölti fel.
- Ha a folyamat kimenő, az egyik "OutFlows_d" utótagú mező fel lesz töltve.

A feltételektől függően tudjuk, hogy a négy mező egyike lesz kitöltve.

## <a name="next-steps"></a>Következő lépések

- Ha választ szeretne kapni a gyakori kérdésekre, tekintse meg a [Traffic Analytics gyakori](traffic-analytics-faq.md)kérdések című témakört.
- A funkciók részletes ismertetését itt találja: [Traffic Analytics dokumentáció](traffic-analytics.md).