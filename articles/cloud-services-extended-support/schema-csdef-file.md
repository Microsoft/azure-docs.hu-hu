---
title: Azure Cloud Services (bővített támogatás) definíciós sémája (csdef-fájl) | Microsoft Docs
description: A Cloud Services definíciós sémájával (csdef) kapcsolatos információk (kiterjesztett támogatás)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: d9bf1b54f1bfeebacbb406a50c8496817857204c
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102507568"
---
# <a name="azure-cloud-services-extended-support-definition-schema-csdef-file"></a>Azure Cloud Services (bővített támogatás) definíciós sémája (csdef-fájl)

A szolgáltatás definíciós fájlja határozza meg az alkalmazás szolgáltatási modelljét. A fájl tartalmazza a felhőalapú szolgáltatás számára elérhető szerepkörök definícióit, meghatározza a szolgáltatási végpontokat, és létrehozza a szolgáltatás konfigurációs beállításait. A konfigurációs beállítás értékei a szolgáltatás konfigurációs fájljában vannak megadva, a [Cloud Service (bővített támogatás) konfigurációs sémája](schema-cscfg-file.md)által leírtak szerint.

Alapértelmezés szerint a rendszer a Azure Diagnostics konfigurációs sémafájl fájlját telepíti a `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` könyvtárba. Cserélje le az helyére az `<version>` [Azure SDK](https://www.windowsazure.com/develop/downloads/)telepített verzióját.

A szolgáltatás-definíciós fájl alapértelmezett bővítménye a csdef.

## <a name="basic-service-definition-schema"></a>Alapszintű szolgáltatás-definíciós séma
A szolgáltatás definíciós fájljának tartalmaznia kell egy `ServiceDefinition` elemet. A szolgáltatási definíciónak tartalmaznia kell legalább egy szerepkör ( `WebRole` vagy `WorkerRole` ) elemet. Akár 25, egyetlen definícióban definiált szerepkört is tartalmazhat, és a szerepkörök típusait vegyesen is elvégezheti. A szolgáltatás definíciója tartalmazza azt a választható `NetworkTrafficRules` elemet is, amely korlátozza, hogy mely szerepkörök tudnak kommunikálni a megadott belső végpontokkal. A szolgáltatás definíciója tartalmazza a választható `LoadBalancerProbes` elemet is, amely tartalmazza a végpontok ügyfél által meghatározott állapotának mintavételét.

A szolgáltatás definíciós fájljának alapszintű formátuma a következő.

```xml
<ServiceDefinition name="<service-name>" topologyChangeDiscovery="<change-type>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" upgradeDomainCount="<number-of-upgrade-domains>" schemaVersion="<version>">
  
  <LoadBalancerProbes>
         …
  </LoadBalancerProbes>
  
  <WebRole …>
         …
  </WebRole>
  
  <WorkerRole …>
         …
  </WorkerRole>
  
  <NetworkTrafficRules>
         …
  </NetworkTrafficRules>

</ServiceDefinition>
```

## <a name="schema-definitions"></a>Séma-definíciók
A következő témakörök ismertetik a sémát:

- [LoadBalancerProbe-séma](schema-csdef-loadbalancerprobe.md)
- [WebRole-séma](schema-csdef-webrole.md)
- [WorkerRole-séma](schema-csdef-workerrole.md)
- [NetworkTrafficRules-séma](schema-csdef-networktrafficrules.md)

##  <a name="servicedefinition-element"></a><a name="ServiceDefinition"></a> ServiceDefinition elem
Az `ServiceDefinition` elem a szolgáltatás definíciós fájljának legfelső szintű eleme.

Az alábbi táblázat az elem attribútumait ismerteti `ServiceDefinition` .

| Attribútum               | Leírás |
| ----------------------- | ----------- |
| name                    |Kötelező. A szolgáltatás neve. A névnek egyedinek kell lennie a szolgáltatásfiók keretén belül.|
| topologyChangeDiscovery | Választható. Meghatározza a topológia változási értesítésének típusát. Lehetséges értékek:<br /><br /> -   `Blast` – A frissítést a lehető leghamarabb elküldi az összes szerepkör-példánynak. Ha a beállítás lehetőséget választja, a szerepkörnek újra kell tudnia kezelni a topológia frissítését.<br />-   `UpgradeDomainWalk` – Az egyes szerepkör-példányok frissítését szekvenciális módon küldi el, miután az előző példány sikeresen elfogadta a frissítést.|
| sémaverzióval           | Választható. Megadja a szolgáltatás definíciós sémájának verzióját. A séma verziója lehetővé teszi a Visual Studio számára, hogy kiválassza a séma-ellenőrzéshez használandó megfelelő SDK-eszközöket, ha az SDK több verziója is telepítve van egymás mellett.|
| upgradeDomainCount      | Választható. Meghatározza, hogy hány frissítési tartomány van lefoglalva a szolgáltatás szerepkörei között. A szerepkör-példányok egy frissítési tartományhoz vannak lefoglalva, ha a szolgáltatás telepítve van. További információ: [Cloud Service-szerepkör vagy-telepítés frissítése](sample-update-cloud-service.md) és [a virtuális gépek rendelkezésre állásának kezelése](../virtual-machines/availability.md) , legfeljebb 20 frissítési tartományt adhat meg. Ha nincs megadva, a frissítési tartományok alapértelmezett száma 5.|

## <a name="see-also"></a>Lásd még

[Azure Cloud Services (bővített támogatás) konfigurációs séma (cscfg-fájl)](schema-cscfg-file.md).