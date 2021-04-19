---
title: Azure Cloud Services (kiterjesztett támogatás) NetworkConfiguration Schema | Microsoft Docs
description: A hálózati konfigurációs sémával kapcsolatos információk Cloud Services (kiterjesztett támogatás)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: ed2d48288bf97fe3ebaa1e8ffc1336d8a82d940e
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719024"
---
# <a name="azure-cloud-services-extended-support-config-networkconfiguration-schema"></a>Azure Cloud Services (kiterjesztett támogatás) konfigurálása networkConfiguration séma

A `NetworkConfiguration` szolgáltatáskonfigurációs fájl eleme meghatározza a Virtual Network DNS-értékeket. Ezek a beállítások nem kötelezőek a Cloud Services.

A következő forrásból többet is megtudhat a virtuális hálózatokról és a kapcsolódó sémákról:

- [Felhőszolgáltatás (kiterjesztett támogatás) konfigurációs sémája.](schema-cscfg-file.md)
- [Cloud Service (kiterjesztett támogatás) Definíciós séma.](schema-csdef-file.md)
- [Hozzon létre egy Virtual Network.](../virtual-network/manage-virtual-network.md)

## <a name="networkconfiguration-element"></a>NetworkConfiguration elem
Az alábbi példa az `NetworkConfiguration` elemet és annak gyermekelemét mutatja be.

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

Az alábbi táblázat a elem gyermekelemét `NetworkConfiguration` ismerteti.

| Elem       | Leírás |
| ------------- | ----------- |
| AccessControl (Hozzáférés-vezérlő) | Választható. Megadja a felhőszolgáltatásban a végpontok elérésére vonatkozó szabályokat. A hozzáférés-vezérlés nevét egy attribútum sztringe `name` határozza meg. A `AccessControl` elem egy vagy több `Rule` elemet tartalmaz. Több elem `AccessControl` is definiálható.|
| Szabály | Választható. Megadja az IP-címek megadott alhálózati tartományán szükséges műveletet. A szabály sorrendjét az attribútum sztringértéke határozza `order` meg. Minél alacsonyabb a szabályszám, annál magasabb a prioritás. A szabályok például 100, 200 és 300 rendelésszámmal is megadva. A 100-as sorszámú szabály elsőbbséget élvez a 200-as sorrendet elrendelő s idővel.<br /><br /> A szabály műveletét az attribútum sztringe `action` határozza meg. Lehetséges értékek:<br /><br /> -   `permit` – Azt adja meg, hogy csak a megadott alhálózati tartományból származó csomagok kommunikálhatnak a végponttal.<br />-   `deny` – Megadja, hogy a rendszer megtagadja a hozzáférést a megadott alhálózati tartományban található végpontok számára.<br /><br /> A szabály által érintett IP-címek alhálózati tartományát az attribútum sztringe határozza `remoteSubnet` meg. A szabály leírását az attribútum sztringe `description` határozza meg.|
| EndpointAcl | Választható. Megadja a hozzáférés-vezérlési szabályok végponthoz való hozzárendelését. A végpontot tartalmazó szerepkör nevét a attribútum sztringe határozza `role` meg. A végpont nevét az attribútum sztringe határozza `endpoint` meg. A végpontra alkalmazandó szabálykészlet neve az attribútum `AccessControl` sztringében van `accessControl` definiálva. Több elem `EndpointAcl` is definiálható.|
| DnsServer (Dns-kiszolgáló) | Választható. Megadja a DNS-kiszolgáló beállításait. A DNS-kiszolgálók beállításait nem kell Virtual Network. A DNS-kiszolgáló nevét az attribútum sztringe határozza `name` meg. A DNS-kiszolgáló IP-címét az attribútum sztringe határozza `IPAddress` meg. Az IP-címnek érvényes IPv4-címnek kell lennie.|
| VirtualNetworkSite | Választható. Megadja annak a Virtual Network helynek a nevét, amelyen a Cloud Service-t üzembe szeretné helyezni. Ez a beállítás nem hoz létre Virtual Network helyhez. Olyan webhelyre hivatkozik, amely korábban már meg lett határozva a hálózati fájlban a Virtual Network. Egy felhőszolgáltatás csak egy felhőszolgáltatás Virtual Network. Ha nem adja meg ezt a beállítást, a Cloud Service nem lesz üzembe 8 Virtual Network. A Virtual Network hely nevét a attribútum sztringe `name` határozza meg.|
| InstanceAddress (Példánycím) | Választható. Egy szerepkör alhálózathoz vagy alhálózatcsoporthoz való társítását adja meg a Virtual Network. Amikor szerepkörnevet társít egy példánycímhez, megadhatja azokat az alhálózatokat, amelyekhez hozzá szeretné társítani ezt a szerepkört. A `InstanceAddress` tartalmaz egy Alhálózatok elemet. Az alhálózathoz vagy alhálózathoz társított szerepkör nevét az attribútum sztringe `roleName` határozza meg.|
| Alhálózat | Választható. Megadja a hálózati konfigurációs fájlban az alhálózat nevének megfelelő alhálózatot. Az alhálózat nevét az attribútum sztringe határozza `name` meg.|
| ReservedIP (FenntartottIP) | Választható. Megadja az üzemelő példányhoz társítható fenntartott IP-címet. A fenntartott IP-címek lefoglalási módját sablon- és `Static` PowerShell-környezetekként kell megadni. Egy felhőszolgáltatás minden üzemelő példánya csak egy fenntartott IP-címhez társítható. A fenntartott IP-cím nevét a attribútum sztringe határozza `name` meg.|

## <a name="see-also"></a>Lásd még
[Felhőszolgáltatás (kiterjesztett támogatás) konfigurációs sémája.](schema-cscfg-file.md)
