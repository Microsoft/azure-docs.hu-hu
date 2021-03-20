---
title: Azure Cloud Services (klasszikus) def. NetworkTrafficRules séma | Microsoft Docs
description: Ismerje meg a NetworkTrafficRules, amely korlátozza azokat a szerepköröket, amelyek hozzáférhetnek egy szerepkör belső végpontokhoz. Egy szolgáltatás-definíciós fájlban lévő szerepkörökkel kombinálja.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2c8ab53068b71652d03d03bf79a224fe5e34dff3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98739768"
---
# <a name="azure-cloud-services-classic-definition-networktrafficrules-schema"></a>Azure Cloud Services (klasszikus) definíciós NetworkTrafficRules séma

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

A `NetworkTrafficRules` csomópont egy opcionális elem a szolgáltatás-definíciós fájlban, amely megadja, hogy a szerepkörök hogyan kommunikáljanak egymással. Ez korlátozza, hogy mely szerepkörök férhetnek hozzá az adott szerepkör belső végpontjaihoz. A `NetworkTrafficRules` nem önálló elem; a szolgáltatás definíciós fájljában kettő vagy több szerepkörrel együtt.

A szolgáltatás definíciós fájljának alapértelmezett kiterjesztése. csdef.

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

AllowAllTraffic elem

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

| Attribútum      | Típus     | Description |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Kötelező. Annak a végpontnak a neve, amely engedélyezi a forgalmat.|
| `roleName`     | `string` | Kötelező. Annak a webes szerepkörnek a neve, amely lehetővé teszi a kommunikációt.|

## <a name="allowalltraffic-element"></a>AllowAllTraffic elem
Az `AllowAllTraffic` elem egy olyan szabály, amely lehetővé teszi, hogy az összes szerepkör kommunikáljon a csomópontban definiált végpontokkal `Destinations` .

##  <a name="whensource-element"></a><a name="WhenSource"></a> WhenSource elem
Az `WhenSource` elem olyan szerepkörök gyűjteményét írja le, amelyek nem tudnak kommunikálni a csomópontban definiált végpontokkal `Destinations` .

| Attribútum | Típus     | Description |
| --------- | -------- | ----------- |
| `matches` | `string` | Kötelező. Meghatározza a kommunikáció engedélyezésekor alkalmazandó szabályt. Az egyetlen érvényes érték jelenleg `AnyRule` .|
  
##  <a name="fromrole-element"></a><a name="FromRole"></a> FromRole elem
Az `FromRole` elem meghatározza azokat a szerepköröket, amelyek kommunikálhatnak a csomópontban definiált végpontokkal `Destinations` . Több elemet is megadhat, `FromRole` Ha több olyan szerepkör is van, amely képes kommunikálni a végpontokkal.

| Attribútum  | Típus     | Description |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Kötelező. Annak a szerepkörnek a neve, amelyről engedélyezni szeretné a kommunikációt.|

## <a name="see-also"></a>Lásd még:
[Cloud Service (klasszikus) definíciós séma](schema-csdef-file.md)




