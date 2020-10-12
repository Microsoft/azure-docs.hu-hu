---
title: Azure Cloud Services NetworkConfiguration séma | Microsoft Docs
description: Ismerje meg a szolgáltatás konfigurációs fájljának NetworkConfiguration elemének alárendelt elemeit, amelyek a Virtual Network és a DNS-értékeket határozzák meg.
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 28
author: tgore03
ms.author: tagore
ms.openlocfilehash: 695ba3acfd5af8797de6e6f7454e493d7863627c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "79529288"
---
# <a name="azure-cloud-services-config-networkconfiguration-schema"></a>Azure Cloud Services konfiguráció NetworkConfiguration sémája

A `NetworkConfiguration` szolgáltatás konfigurációs fájljának eleme Virtual Network és DNS-értékeket ad meg. Ezek a beállítások a Cloud Services esetében nem kötelezőek.

A virtuális hálózatokkal és a kapcsolódó sémákkal kapcsolatos további tudnivalókért a következő erőforrással tájékozódhat:

- [Cloud Service (klasszikus) konfigurációs séma](schema-cscfg-file.md)
- [Cloud Service (klasszikus) definíciós séma](schema-csdef-file.md)
- [Virtual Network létrehozása (klasszikus)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)

## <a name="networkconfiguration-element"></a>NetworkConfiguration elem
Az alábbi példa az `NetworkConfiguration` elemet és annak alárendelt elemeit mutatja be.

```xml
<ServiceConfiguration>
  <NetworkConfiguration>
    <AccessControls>
      <AccessControl name="aclName1">
        <Rule order="<rule-order>" action="<rule-action>" remoteSubnet="<subnet-address>" description="rule-description"/>
      </AccessControl>
    </AccessControls>
    <EndpointAcls>
      <EndpointAcl role="<role-name>" endpoint="<endpoint-name>" accessControl="<acl-name>"/>
    </EndpointAcls>
    <Dns>
      <DnsServers>
        <DnsServer name="<server-name>" IPAddress="<server-address>" />
      </DnsServers>
    </Dns>
    <VirtualNetworkSite name="Group <RG-VNet> <VNet-name>"/>
    <AddressAssignments>
      <InstanceAddress roleName="<role-name>">
        <Subnets>
          <Subnet name="<subnet-name>"/>
        </Subnets>
      </InstanceAddress>
      <ReservedIPs>
        <ReservedIP name="<reserved-ip-name>"/>
      </ReservedIPs>
    </AddressAssignments>
  </NetworkConfiguration>
</ServiceConfiguration>
```

Az alábbi táblázat az elem alárendelt elemeit ismerteti `NetworkConfiguration` .

| Elem       | Leírás |
| ------------- | ----------- |
| AccessControl | Választható. Meghatározza a felhőben lévő végpontokhoz való hozzáférés szabályait. A hozzáférés-vezérlés nevét az attribútum karakterlánca határozza meg `name` . Az `AccessControl` elem egy vagy több `Rule` elemet tartalmaz. Egynél több `AccessControl` elem is definiálható.|
| Szabály | Választható. Meghatározza azt a műveletet, amelyet az IP-címek megadott alhálózati tartománya esetében kell végrehajtani. A szabály sorrendjét az attribútum egy karakterlánc-értéke határozza meg `order` . Minél alacsonyabb a szabály száma, annál magasabb a prioritás. Például a szabályok megadhatók a 100, 200 és 300 sorszámokkal. A 100-as sorszámú szabály elsőbbséget élvez a 200-os sorrendtel rendelkező szabállyal szemben.<br /><br /> A szabályhoz tartozó műveletet az attribútum egy karakterlánca határozza meg `action` . Lehetséges értékek:<br /><br /> -   `permit` – Azt adja meg, hogy csak a megadott alhálózati tartományból származó csomagok kommunikáljanak a végponttal.<br />-   `deny` – Megadja, hogy a rendszer megtagadja a hozzáférést a megadott alhálózati tartomány végpontjai számára.<br /><br /> A szabály által érintett IP-címek alhálózatának tartományát az attribútum egy karakterlánca határozza meg `remoteSubnet` . A szabály leírását az attribútum egy karakterlánca határozza meg `description` .|
| EndpointAcl | Választható. Meghatározza a hozzáférés-vezérlési szabályok hozzárendelését egy végponthoz. A végpontot tartalmazó szerepkör nevét az attribútum egy karakterlánca határozza meg `role` . A végpont nevét az attribútum egy karakterlánca határozza meg `endpoint` . A végpontra alkalmazni kívánt szabályok halmazának neve az `AccessControl` attribútum egy karakterláncában van definiálva `accessControl` . Egynél több `EndpointAcl` elem is definiálható.|
| DNS | Választható. A DNS-kiszolgáló beállításait adja meg. A DNS-kiszolgálók beállításait Virtual Network nélkül is megadhatja. A DNS-kiszolgáló nevét az attribútum egy karakterlánca határozza meg `name` . A DNS-kiszolgáló IP-címét az attribútum egy karakterlánca határozza meg `IPAddress` . Az IP-címnek érvényes IPv4-címnek kell lennie.|
| VirtualNetworkSite | Választható. Annak a Virtual Network-helynek a nevét adja meg, amelyben telepíteni szeretné a felhőalapú szolgáltatást. Ez a beállítás nem hoz létre Virtual Network helyet. Egy olyan webhelyre hivatkozik, amely korábban meg van határozva a Virtual Network hálózati fájljában. A felhőalapú szolgáltatás csak egy Virtual Network tagja lehet. Ha nem megadja ezt a beállítást, a rendszer nem telepíti a Cloud Service-t egy Virtual Network. Az Virtual Network hely nevét az attribútum egy karakterlánca határozza meg `name` .|
| InstanceAddress | Választható. Meghatározza egy szerepkör társítását egy alhálózathoz vagy alhálózatokhoz a Virtual Networkban. Amikor egy szerepkör-nevet társít egy példány címéhez, megadhatja azokat az alhálózatokat, amelyekhez társítani szeretné ezt a szerepkört. Az `InstanceAddress` alhálózatok elemet tartalmaz. Az alhálózathoz vagy alhálózatokhoz társított szerepkör nevét az attribútum egy karakterlánca határozza meg `roleName` .|
| Alhálózat | Választható. Meghatározza azt az alhálózatot, amely megfelel az alhálózat nevének a hálózati konfigurációs fájlban. Az alhálózat nevét az attribútum egy karakterlánca határozza meg `name` .|
| Foglalt IP | Választható. Meghatározza azt a fenntartott IP-címet, amelyet hozzá kell rendelni a központi telepítéshez. A fenntartott IP-cím létrehozásához a Create Fenntartott IP címet kell használnia. A felhőalapú szolgáltatásokban minden üzembe helyezés egy fenntartott IP-címmel társítható. A fenntartott IP-cím nevét az attribútum egy karakterlánca határozza meg `name` .|

## <a name="see-also"></a>Lásd még:
[Cloud Service (klasszikus) konfigurációs séma](schema-cscfg-file.md)
