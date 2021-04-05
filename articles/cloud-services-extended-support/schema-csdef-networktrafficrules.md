---
title: Azure Cloud Services (bővített támogatás) def. NetworkTrafficRules séma | Microsoft Docs
description: A Cloud Serviceshoz társított hálózati forgalmi szabályokkal kapcsolatos információk (kiterjesztett támogatás)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 0064794701e87419da086c458673f7ccee4f37dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98744550"
---
# <a name="azure-cloud-services-extended-support-definition-networktrafficrules-schema"></a>Azure Cloud Services (bővített támogatás) definíciós NetworkTrafficRules séma

A `NetworkTrafficRules` csomópont egy opcionális elem a szolgáltatás-definíciós fájlban, amely megadja, hogy a szerepkörök hogyan kommunikáljanak egymással. Ez korlátozza, hogy mely szerepkörök férhetnek hozzá az adott szerepkör belső végpontjaihoz. A `NetworkTrafficRules` nem önálló elem; a szolgáltatás definíciós fájljában kettő vagy több szerepkörrel együtt.

A szolgáltatás-definíciós fájl alapértelmezett bővítménye a csdef.

> [!NOTE]
>  A `NetworkTrafficRules` csomópont csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Alapszintű szolgáltatás-definíciós séma a hálózati forgalom szabályaihoz
A hálózati forgalmi definíciókat tartalmazó szolgáltatási definíciós fájlok alapformátuma a következő.

```xml
<ServiceDefinition …>
   <NetworkTrafficRules>
      <OnlyAllowTrafficTo>
         <Destinations>
            <RoleEndpoint endpointName="<name-of-the-endpoint>" roleName="<name-of-the-role-containing-the-endpoint>"/>
         </Destinations>
         <AllowAllTraffic/>
         <WhenSource matches="[AnyRule]">
            <FromRole roleName="<name-of-the-role-to-allow-traffic-from>"/>
         </WhenSource>
      </OnlyAllowTrafficTo>
   </NetworkTrafficRules>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Séma elemei
A `NetworkTrafficRules` szolgáltatás definíciós fájljának csomópontja tartalmazza ezeket az elemeket, amelyeket a jelen témakör következő részeiben talál:

[NetworkTrafficRules elem](#NetworkTrafficRules)

[OnlyAllowTrafficTo elem](#OnlyAllowTrafficTo)

[Célhelyek elem](#Destinations)

[RoleEndpoint elem](#RoleEndpoint)

[AllowAllTraffic elem](#AllowAllTraffic)

[WhenSource elem](#WhenSource)

[FromRole elem](#FromRole)

##  <a name="networktrafficrules-element"></a><a name="NetworkTrafficRules"></a> NetworkTrafficRules elem
Az `NetworkTrafficRules` elem határozza meg, hogy mely szerepkörök kommunikálhatnak egymással egy másik szerepkörhöz tartozó végponttal. Egy szolgáltatás egy `NetworkTrafficRules` definíciót is tartalmazhat.

##  <a name="onlyallowtrafficto-element"></a><a name="OnlyAllowTrafficTo"></a> OnlyAllowTrafficTo elem
Az `OnlyAllowTrafficTo` elem ismerteti a cél végpontok gyűjteményét, valamint azokat a szerepköröket, amelyek kommunikálni tudnak velük. Több csomópontot is megadhat `OnlyAllowTrafficTo` .

##  <a name="destinations-element"></a><a name="Destinations"></a> Célhelyek elem
Az `Destinations` elem a RoleEndpoints gyűjteményét írja le, amely a használatával kommunikálhat.

##  <a name="roleendpoint-element"></a><a name="RoleEndpoint"></a> RoleEndpoint elem
Az `RoleEndpoint` elem egy olyan szerepkörhöz tartozó végpontot ismertet, amely lehetővé teszi a kommunikációt a szolgáltatással. Több elemet is megadhat, `RoleEndpoint` Ha a szerepkörhöz több végpont is van.

| Attribútum      | Típus     | Leírás |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Kötelező. Annak a végpontnak a neve, amely engedélyezi a forgalmat.|
| `roleName`     | `string` | Kötelező. Annak a webes szerepkörnek a neve, amely lehetővé teszi a kommunikációt.|

## <a name="allowalltraffic-element"></a><a name="AllowAllTraffic"></a> AllowAllTraffic elem
Az `AllowAllTraffic` elem egy olyan szabály, amely lehetővé teszi, hogy az összes szerepkör kommunikáljon a csomópontban definiált végpontokkal `Destinations` .

##  <a name="whensource-element"></a><a name="WhenSource"></a> WhenSource elem
Az `WhenSource` elem olyan szerepkörök gyűjteményét írja le, amelyek nem tudnak kommunikálni a csomópontban definiált végpontokkal `Destinations` .

| Attribútum | Típus     | Leírás |
| --------- | -------- | ----------- |
| `matches` | `string` | Kötelező. Meghatározza a kommunikáció engedélyezésekor alkalmazandó szabályt. Az egyetlen érvényes érték jelenleg `AnyRule` .|
  
##  <a name="fromrole-element"></a><a name="FromRole"></a> FromRole elem
Az `FromRole` elem meghatározza azokat a szerepköröket, amelyek kommunikálhatnak a csomópontban definiált végpontokkal `Destinations` . Több elemet is megadhat, `FromRole` Ha több olyan szerepkör is van, amely képes kommunikálni a végpontokkal.

| Attribútum  | Típus     | Leírás |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Kötelező. Annak a szerepkörnek a neve, amelyről engedélyezni szeretné a kommunikációt.|

## <a name="see-also"></a>Lásd még
A [Cloud Service (bővített támogatás) definíciós sémája](schema-csdef-file.md).




