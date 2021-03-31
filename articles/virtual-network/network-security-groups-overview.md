---
title: Azure hálózati biztonsági csoportok – áttekintés
titlesuffix: Azure Virtual Network
description: További tudnivalók a hálózati biztonsági csoportokról. A hálózati biztonsági csoportok segítséget nyújtanak az Azure-erőforrások közötti hálózati forgalom szűrésében.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: kumud
ms.reviewer: kumud
ms.custom: contperf-fy21q1
ms.openlocfilehash: 4e23c6f25145724a5300c9e5cdcb55431fb0b4f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97028767"
---
# <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)
<a name="network-security-groups"></a>

Egy Azure-beli hálózati biztonsági csoport használatával szűrheti az Azure-beli virtuális hálózatban lévő Azure-erőforrások felé irányuló és onnan érkező hálózati forgalmat. A hálózati biztonsági csoportok olyan [biztonsági szabályokat](#security-rules) tartalmaznak, amelyek engedélyezik vagy letiltják a különböző típusú Azure-erőforrások bejövő vagy kimenő hálózati forgalmát. Az egyes szabályokhoz meghatározhatja a forrást és a célt, valamint a használni kívánt portot és protokollt.

Ez a cikk a hálózati biztonsági csoport szabályának tulajdonságait, az alkalmazott [alapértelmezett biztonsági szabályokat](#default-security-rules) , valamint a [kibővített biztonsági](#augmented-security-rules)szabályok létrehozásához módosítható szabály-tulajdonságokat ismerteti.

## <a name="security-rules"></a><a name="security-rules"></a> Biztonsági szabályok

A hálózati biztonsági csoportok nulla vagy tetszőleges számú szabályt tartalmazhatnak, az Azure-előfizetések [korlátain](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) belül. Az egyes szabályok az alábbi tulajdonságokat határozzák meg:

|Tulajdonság  |Magyarázat  |
|---------|---------|
|Név|Egy egyedi név a hálózati biztonsági csoporton belül.|
|Prioritás | Egy 100 és 4096 közötti szám. A szabályok feldolgozása prioritási sorrendben történik. Az alacsonyabb sorszámúak feldolgozása a magasabb sorszámúak előtt történik, mivel az alacsonyabb sorszámok magasabb prioritást jelölnek. Ha az adatforgalom megfelel valamelyik szabálynak, a feldolgozás leáll. Ennek eredményeképp az olyan alacsonyabb prioritású (magasabb számú) szabályokat, amelyek attribútumai megegyeznek a magasabb prioritású szabályokéival, a rendszer nem dolgozza fel.|
|Forrás vagy cél| Bármelyik, vagy egy egyéni IP-cím, Classless Inter-Domain Routing- (CIDR-) blokk (például 10.0.0.0/24), szolgáltatáscímke vagy alkalmazásbiztonsági csoport. Ha egy Azure-erőforrás címét adja meg, az erőforráshoz rendelt magánhálózati IP-címet adja meg. A hálózati biztonsági csoportok feldolgozása azután történik, hogy az Azure a bejövő forgalomhoz a nyilvános IP-címeket magánhálózati IP-címekre fordítja le, de még mielőtt, hogy a magánhálózati IP-címeket nyilvános IP-címekre fordítaná le a kimenő forgalomhoz. . Tartományok, szolgáltatáscímkék vagy alkalmazásbiztonsági csoportok megadásával kevesebb biztonsági szabályt kell majd létrehoznia. Több egyedi IP-cím és tartomány megadásának lehetősége (nem adhat meg több szolgáltatási címkét vagy alkalmazáscsoport) egy szabályban [kibővített biztonsági szabályoknak](#augmented-security-rules)nevezzük. Kibővített biztonsági szabályok kizárólag a Resource Manager-alapú üzemi modellben létrehozott hálózati biztonsági csoportokban hozhatóak létre. A klasszikus üzemi modellben létrehozott hálózati biztonsági csoportokban nem adhat meg több IP-címet vagy -címtartományt.|
|Protokoll     | TCP, UDP, ICMP vagy any.|
|Irány| Megadja, hogy a szabály a bejövő vagy a kimenő adatforgalomra vonatkozik.|
|Porttartomány     |Megadhat egy egyéni portot vagy egy porttartományt is. Megadhatja például a 80-as portot vagy a 10000–10005 tartományt. Tartományok megadásával kevesebb biztonsági szabályt kell majd létrehoznia. Kibővített biztonsági szabályok kizárólag a Resource Manager-alapú üzemi modellben létrehozott hálózati biztonsági csoportokban hozhatóak létre. A klasszikus üzemi modellben létrehozott hálózati biztonsági csoportokban egyazon szabályban nem adhat meg több portot vagy porttartományt.   |
|Művelet     | Engedélyezés vagy letiltás        |

A hálózati biztonsági csoportok biztonsági szabályait a rendszer prioritásuk szerint, a rekordokkal kapcsolatos 5 információ (forrás, forrásport, cél, célport és protokoll) alapján értékeli ki, hogy a forgalom engedélyezve vagy tiltva legyen. Előfordulhat, hogy nem hoz létre két biztonsági szabályt ugyanazzal a prioritással és iránysal. Egy folyamatrekord jön létre a meglévő kapcsolatokhoz. A kommunikáció a folyamatrekordok kapcsolati állapota alapján lesz engedélyezve vagy tiltva. A folyamatrekord teszi lehetővé a hálózati biztonsági csoport állapotalapú működését. Ha bármely címre meghatároz egy kimenő biztonsági szabályt a 80-as porton keresztül, nem szükséges biztonsági szabályt megadnia a bejövő forgalomra a válaszhoz. Ha a kommunikáció kívülről indul, csak egy bejövő biztonsági szabályt kell meghatároznia. Ennek az ellenkezője is igaz. Ha egy porton engedélyezett a bejövő forgalom, nem szükséges egy kimenő biztonsági szabályt is megadni ugyanazon a porton történő válaszadáshoz.

Előfordulhat, hogy a meglévő kapcsolatok nem szakadnak meg az adatfolyamot engedélyező biztonsági szabály eltávolításakor. Az adatfolyam megszakad, ha a kapcsolatokat leállítják, és legalább néhány percig nincs forgalom egyik irányban sem.

Az egy hálózati biztonsági csoporton belül létrehozható biztonsági szabályok száma korlátozott. További részletek: [Az Azure korlátai](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

### <a name="default-security-rules"></a><a name="default-security-rules"></a> Alapértelmezett biztonsági szabályok

Az Azure a következő alapértelmezett szabályokat hozza létre a létrehozott hálózati biztonsági csoportokban:

#### <a name="inbound"></a>Bejövő

##### <a name="allowvnetinbound"></a>AllowVNetInBound

|Prioritás|Forrás|Forrásportok|Cél|Célportok|Protokoll|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Bármelyik|Engedélyezés|

##### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Prioritás|Forrás|Forrásportok|Cél|Célportok|Protokoll|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Bármelyik|Engedélyezés|

##### <a name="denyallinbound"></a>DenyAllInbound

|Prioritás|Forrás|Forrásportok|Cél|Célportok|Protokoll|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Bármelyik|Megtagadás|

#### <a name="outbound"></a>Kimenő

##### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Prioritás|Forrás|Forrásportok| Cél | Célportok | Protokoll | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Bármelyik | Engedélyezés |

##### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Prioritás|Forrás|Forrásportok| Cél | Célportok | Protokoll | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Bármelyik | Engedélyezés |

##### <a name="denyalloutbound"></a>DenyAllOutBound

|Prioritás|Forrás|Forrásportok| Cél | Célportok | Protokoll | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Bármelyik | Megtagadás |

A **Forrás** és a **Cél** oszlopban a *VirtualNetwork*, *AzureLoadBalancer* és *Internet* értékek [szolgáltatáscímkék](service-tags-overview.md), nem IP-címek. A protokoll oszlopban **minden** a TCP, UDP és ICMP protokollt foglalja magában. Szabály létrehozásakor megadhatja a TCP, UDP, ICMP vagy bármelyik lehetőséget. A *0.0.0.0/0* érték a **Forrás** és a **Cél** oszlopban az összes címet jelöli. Az olyan ügyfelek, mint a Azure Portal, az Azure CLI vagy a PowerShell a * vagy bármelyiket használhatja ehhez a kifejezéshez.
 
Az alapértelmezett szabályok nem távolíthatók el, azonban magasabb prioritású szabályok létrehozásával felülírhatók.

### <a name="augmented-security-rules"></a><a name="augmented-security-rules"></a> Kibővített biztonsági szabályok

A kibővített biztonsági szabályok megkönnyítik a virtuális hálózatok biztonsági definícióinak megadását, így nagyobb és összetettebb hálózati biztonsági szabályok alakíthatók ki kevesebb szabállyal. Több portot, több konkrét IP-címet és -tartományt foglalhat egyetlen, könnyen érthető biztonsági szabályba. Kibővített szabályokat a szabályok forrás, cél és port mezőiben is használhat. A biztonsági szabály definíciójának karbantartásának leegyszerűsítése érdekében kombinálja a kibővített biztonsági szabályokat a [szolgáltatási címkék](service-tags-overview.md) vagy az [alkalmazás biztonsági csoportjaival](#application-security-groups). A szabályokban megadható címek, tartományok és portok száma korlátozott. További részletek: [Az Azure korlátai](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

#### <a name="service-tags"></a>Szolgáltatáscímkék

A szolgáltatás címkéje egy adott Azure-szolgáltatás IP-címeinek egy csoportját jelöli. Segít csökkenteni a hálózati biztonsági szabályok gyakori frissítéseinek összetettségét.

További információ: Azure- [szolgáltatás címkéi](service-tags-overview.md). A hálózati hozzáférés korlátozásához a Storage szolgáltatás címkével kapcsolatos példát a [hálózati hozzáférés korlátozása a Pásti-erőforrásokhoz](tutorial-restrict-network-access-to-resources.md)című témakörben talál.

#### <a name="application-security-groups"></a>Alkalmazásbiztonsági csoportok

Az alkalmazásbiztonsági csoportokkal az alkalmazás struktúrájának természetes bővítményeként konfigurálhatja a hálózati biztonságot, így csoportosíthatja a virtuális gépeket, és ezen csoportok alapján meghatározhatja a hálózati biztonsági szabályokat. A biztonsági szabályokat újrahasznosíthatja nagy léptékben is a konkrét IP-címek manuális karbantartása nélkül. További információ: [alkalmazás biztonsági csoportjai](application-security-groups.md).

## <a name="azure-platform-considerations"></a>Tudnivalók az Azure platformhoz

- **A gazdagép csomópontjának virtuális IP-** címe: az alapszintű infrastruktúra-szolgáltatások, például a DHCP, a DNS, a IMDS és az állapotfigyelő szolgáltatás a virtualizált GAZDAGÉP IP-címeinek 168.63.129.16 és 169.254.169.254 keresztül érhető el. Ezek az IP-címek a Microsofthoz tartoznak, és az összes régióban használt virtualizált IP-címek erre a célra szolgálnak. A hatályos biztonsági szabályok és a hatályos útvonalak nem tartalmazzák ezeket a platform-szabályokat. Az alapszintű infrastruktúra-kommunikáció felülbírálásához hozzon létre egy biztonsági szabályt, amely megtagadja a forgalmat a hálózati biztonsági csoportra vonatkozó szabályokban a következő [szolgáltatási címkék](service-tags-overview.md) használatával: AzurePlatformDNS, AzurePlatformIMDS, AzurePlatformLKM. Útmutató a [hálózati forgalom szűrésének diagnosztizálásához](diagnose-network-traffic-filter-problem.md) és a [hálózati útválasztás diagnosztizálásához](diagnose-network-routing-problem.md).
- **Licencelés (kulcskezelő szolgáltatás)**: A virtuális gépeken futó Windows-rendszerképeket licencelni kell. A licenceléshez el kell küldeni egy licencelési kérelmet a Kulcskezelő szolgáltatás ilyen kérelmeket kezelő kiszolgálóinak. A kérelmet az 1688-as kimenő porton küldi el a rendszer. Az [alapértelmezett 0.0.0.0/0 útvonalat](virtual-networks-udr-overview.md#default-route) használó konfigurációkban ez a platformszabály le van tiltva.
- **Virtuális gépek az elosztott terhelésű készletekben**: Az alkalmazott forrásport és címtartomány a forrásszámítógépről származik, nem a terheléselosztóról. A célport és a címtartomány nem a terheléselosztóhoz, hanem a célszámítógéphez tartozik.
- **Azure szolgáltatáspéldányok**: Több Azure szolgáltatás, például a HDInsight, az alkalmazásszolgáltatási környezetek és a virtuálisgép-méretezési csoportok példányai is virtuális hálózati alhálózatokon vannak üzembe helyezve. A virtuális hálózatokon üzembe helyezhető szolgáltatások teljes listájához lásd a [Virtuális hálózatok az Azure-szolgáltatásokhoz](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) című témakört. Mindenképp ismerkedjen meg behatóan az egyes szolgáltatások portkövetelményeivel, mielőtt egy hálózati biztonsági csoportot alkalmazna az erőforrást üzemeltető alhálózatra. Ha a valamely szolgáltatás által használt portokat letiltja, a szolgáltatás nem megfelelően fog működni.
- **Kimenő e-mailek küldése**: a Microsoft azt javasolja, hogy használja a hitelesített SMTP Relay-szolgáltatásokat (általában a 587-es TCP-porton keresztül, de gyakran másokat is) az Azure-beli Virtual Machinesról küldött e-mailek Az SMTP-továbbítási szolgáltatások a feladói jellegzetességek biztosítására szakosodtak, így minimalizálják annak lehetőségét, hogy a külső e-mail-szolgáltatók visszautasítsák az üzeneteket. Ilyen SMTP-továbbítási szolgáltatás például, a teljesség igénye nélkül, az Exchange Online Protection és a SendGrid. Az SMTP-továbbítási szolgáltatások használata nincsen korlátozva az Azure-ban, függetlenül attól, hogy milyen előfizetése van. 

  Amennyiben 2017. november 15. előtt hozta létre Azure-előfizetését, az SMTP-továbbítási szolgáltatások használata mellett közvetlenül a 25-ös TCP-porton keresztül is küldhet e-maileket. Amennyiben 2017. november 15. után fizetett elő, nem biztos hogy küldhet e-maileket közvetlenül a 25-ös porton keresztül. A 25-ös porton keresztül folytatott kimenő kommunikáció viselkedése az előfizetés típusától függ, amely lehet:

     - **Nagyvállalati szerződés**: 25-ös porton keresztüli kimenő kommunikáció engedélyezve. Közvetlenül a virtuális gépekről küldhet kimenő e-maileket a külső e-mail-szolgáltatóknak, az Azure platform korlátozásai nélkül. 
     - **Használatalapú fizetés**: a 25-ös porton keresztüli kimenő kommunikáció minden erőforráson blokkolva van. Ha közvetlenül a virtuális gépéről szeretne e-mailt küldenie egy külső e-mail-szolgáltatónak (hitelesített SMTP-továbbítás használata nélkül), kérheti a korlátozás feloldását. A kérelmeket a Microsoft saját belátása szerint értékeli és hagyja jóvá, a visszaélések kiküszöbölésére szolgáló megfelelő ellenőrzések elvégzése után. Kérelem benyújtásához támogatási esetet kell nyitnia a *Technikai*, *Virtuális hálózati kapcsolat*, *Sikertelen e-mail-küldés (SMTP/25-ös port)* problématípus kiválasztásával. A támogatási esetben részletesen indokolja, hogy előfizetésének miért kell közvetlenül a levelezési szolgáltatónak e-mailt küldenie a hitelesített SMTP-továbbítás használata helyett. Amennyiben előfizetését felmentik a korlátozás alól, csak a mentesítés dátuma után létrehozott virtuális gépek képesek a 25-ös porton keresztüli kimenő kommunikációra.
     - **MSDN, Azure Pass, Azure in Open, Education, BizSpark és ingyenes próbaverzió**: a 25-ös porton keresztüli kimenő kommunikáció minden erőforráson blokkolva van. Nem küldhető kérelem a korlátozás feloldására, mert a kérelmek nem teljesíthetők. Ha mindenképp szeretne e-mailt küldeni a virtuális gépről, használjon SMTP-továbbítási szolgáltatást.
     - **Felhőszolgáltató**: Az Azure-erőforrásokat felhőszolgáltatón keresztül használó ügyfelek létrehozhatnak egy támogatási esetet a felhőszolgáltatónál, és kérhetik, hogy a szolgáltató hozzon létre a nevükben egy feloldási esetet, ha nem használható egy biztonságos SMTP-továbbító.

  Amennyiben az Azure engedélyezi az e-mailek küldését a 25-ös porton keresztül, a Microsoft nem tudja garantálni, hogy a levelező szolgáltatók elfogadják a virtuális gépről érkező bejövő e-maileket. Ha egy szolgáltató elutasítja az Ön virtuális gépéről érkező leveleket, vele együttműködésben oldja meg az üzenetküldési vagy levélszemétszűrési problémákat, vagy használjon hitelesített SMTP-továbbítási szolgáltatást.

## <a name="next-steps"></a>Következő lépések

* Ha szeretné megtudni, hogy mely Azure-erőforrások telepíthetők egy virtuális hálózatba, és hogyan rendelkezhetnek hozzájuk társított hálózati biztonsági csoportokkal, tekintse meg a [Virtual Network Integration for Azure Services](virtual-network-for-azure-services.md) című témakört.
* A hálózati biztonsági csoportokkal való adatforgalom kiértékelésével kapcsolatos további információkért lásd: a [hálózati biztonsági csoportok működése](network-security-group-how-it-works.md).
* Ha korábban még nem hozott létre hálózati biztonsági csoportot, ebben a rövid [oktatóanyagban](tutorial-filter-network-traffic.md) némi gyakorlatra tehet szert.
* Ha már ismeri a hálózati biztonsági csoportok működését, és kezelni szeretné őket, tekintse meg a [hálózati biztonsági csoportok kezelését](manage-network-security-group.md) bemutató témakört. 
* Ha kommunikációs problémákat tapasztal, és hibaelhárítást végezne a hálózati biztonsági csoportokon, tekintse meg [a virtuális gépek hálózatiforgalom-szűrési problémáinak diagnosztizálását](diagnose-network-traffic-filter-problem.md) ismertető rövid útmutatót. 
* Megtudhatja, hogyan engedélyezheti a [hálózati biztonsági csoport folyamatábráit](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a társított hálózati biztonsági csoporttal rendelkező erőforrások felé irányuló hálózati forgalom elemzéséhez.
