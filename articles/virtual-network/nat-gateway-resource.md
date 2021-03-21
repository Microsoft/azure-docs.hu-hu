---
title: Virtuális hálózatok tervezése NAT Gateway-erőforrásokkal
titleSuffix: Azure Virtual Network NAT
description: Ismerje meg, hogyan tervezhet virtuális hálózatokat NAT Gateway-erőforrásokkal.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
Customer intent: As an IT administrator, I want to learn more about how to design virtual networks with NAT gateway resources.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2021
ms.author: allensu
ms.openlocfilehash: 5c70c575464d82f714022291406418cdd1ca0f8d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102094966"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources"></a>Virtuális hálózatok tervezése NAT Gateway-erőforrásokkal

A NAT-átjáró erőforrásai [Virtual Network NAT](nat-overview.md) részét képezik, és kimenő internetkapcsolatot biztosítanak a virtuális hálózatok egy vagy több alhálózatához. Annak a virtuális hálózatnak az alhálózata, amelyet a NAT-átjáró használni fog. A NAT a forrás hálózati címfordítást (SNAT) biztosítja egy alhálózat számára.  A NAT-átjáró erőforrásai határozzák meg, hogy a virtuális gépek mely statikus IP-címeket használják a kimenő folyamatok létrehozásakor. A statikus IP-címek a nyilvános IP-címek erőforrásaiból (PIP), a nyilvános IP-előtag erőforrásaiból vagy mindkettőből származnak. Ha egy nyilvános IP-előtag erőforrást használ, a rendszer a teljes nyilvános IP-előtag-erőforrás összes IP-címét egy NAT-átjáró erőforrása használja fel. A NAT-átjáró erőforrásai akár 16 statikus IP-címet is használhatnak.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway resource that consumes all IP addresses for a public IP prefix and directs that traffic to and from two subnets of virtual machines and a virtual machine scale set." width="256" title="NAT Virtual Network az internet felé">
</p>

*Ábra: Virtual Network NAT a kimenő internethez*

## <a name="how-to-deploy-nat"></a>NAT üzembe helyezése

A NAT-átjáró konfigurálása és használata szándékosan egyszerű:  

NAT-átjáró erőforrása:
- Hozzon létre regionális vagy zóna (elkülönített) NAT Gateway-erőforrást,
- IP-címek kiosztása,
- Ha szükséges, módosítsa a TCP üresjárati időkorlátját (nem kötelező).  Az alapértelmezett beállítások módosítása <ins>előtt</ins> tekintse át az [időzítőket](#timers) .

Virtuális hálózat:
- Konfigurálja a virtuális hálózati alhálózatot NAT-átjáró használatára.

A felhasználó által megadott útvonalak nem szükségesek.

## <a name="resource"></a>Erőforrás

Az erőforrás úgy lett kialakítva, hogy egyszerű legyen, mint az alábbi Azure Resource Manager példa a sablonhoz hasonló formátumban.  Ez a sablon-szerű formátum itt látható, hogy bemutassa a fogalmakat és a struktúrát.  Módosítsa a példát az igényeinek megfelelően.  Ez a dokumentum nem az oktatóanyaghoz készült.

A következő ábrán a különböző Azure Resource Manager erőforrások közötti írható hivatkozások láthatók.  A nyíl a hivatkozás irányát jelzi, amelyből az írható. Áttekintés 

<p align="center">
  <img src="media/nat-overview/flow-map.svg" alt="Figure depicts a NAT receiving traffic from internal subnets and directing it to a public IP and an IP prefix." width="256" title="NAT-objektummodell Virtual Network">
</p>

*Ábra: Virtual Network NAT-objektummodell*

A legtöbb számítási feladathoz a NAT használata ajánlott, kivéve, ha a [készleten alapuló Load Balancer kimenő kapcsolathoz](../load-balancer/load-balancer-outbound-connections.md)adott függősége van.  

Áttelepítheti a standard Load Balancer-forgatókönyveket, beleértve a [kimenő szabályokat](../load-balancer/load-balancer-outbound-connections.md#outboundrules)is a NAT-átjáróra. A Migrálás érdekében helyezze át a nyilvános IP-címet és a nyilvános IP-előtag erőforrásait a terheléselosztó felületéről a NAT-átjáróra. A NAT-átjáró új IP-címei nem szükségesek. A standard nyilvános IP-címek erőforrásai és a nyilvános IP-előtag erőforrása újra felhasználható, ha az összeg nem haladja meg a 16 IP-címet. Tervezze meg a szolgáltatás-megszakítással való áttelepítést az áttérés során.  A folyamat automatizálásával csökkentheti a megszakítást. Először tesztelje az áttelepítést átmeneti környezetben.  Az áttérés során a bejövő folyamatokat nem érinti a rendszer.


A következő példa egy Azure Resource Manager sablonból származó kódrészlet.  Ez a sablon számos erőforrást telepít, beleértve a NAT-átjárót is.  Ebben a példában a sablon a következő paraméterekkel rendelkezik:

- **natgatewayname** – a NAT-átjáró neve.
- **hely** – az az Azure-régió, ahol az erőforrás található.
- **publicipname** – a NAT-átjáróhoz társított kimenő nyilvános IP-cím neve.
- **vnetname** – a virtuális hálózat neve.
- **subnetname** – a NAT-átjáróhoz társított alhálózat neve.

Az összes IP-cím és előtag-erőforrás által megadott IP-címek teljes száma nem haladhatja meg a 16 IP-cím összegét. Az 1 és 16 közötti IP-címek száma engedélyezett.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="81-96":::

A NAT-átjáró erőforrásának létrehozásakor a virtuális hálózat egy vagy több alhálózatán is használható. Itt adhatja meg, hogy mely alhálózatok használják ezt a NAT Gateway-erőforrást. A NAT-átjárók nem tudnak több virtuális hálózatot kiterjedni. Nem kell ugyanazt a NAT-átjárót hozzárendelni a virtuális hálózat összes alhálózatához. Az egyes alhálózatokat különböző NAT Gateway-erőforrásokkal lehet konfigurálni.

A rendelkezésre állási zónákat nem használó forgatókönyvek regionálisak lesznek (nincs megadva zóna). Ha rendelkezésre állási zónákat használ, megadhat egy zónát, amely elkülöníti a NAT-t egy adott zónához. A Zone-redundancia nem támogatott. Tekintse át a NAT [rendelkezésre állási zónáit](#availability-zones).

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="1-146" highlight="81-96":::

A NAT-átjárók egy, a virtuális hálózaton belüli alhálózaton lévő tulajdonsággal vannak definiálva. A virtuális hálózati **vnetname** alhálózati **subnetname** virtuális gépek által létrehozott folyamatok a NAT-átjárót fogják használni. Minden kimenő kapcsolat a **natgatewayname** társított IP-címeket fogja használni a forrás IP-címként.

Az ebben a példában használt Azure Resource Manager sablonról további információt a következő témakörben talál:

- [Gyors útmutató: NAT-átjáró létrehozása – Resource Manager-sablon](quickstart-create-nat-gateway-template.md)
- [Virtual Network NAT](https://azure.microsoft.com/resources/templates/101-nat-gateway-1-vm/)

## <a name="design-guidance"></a>Tervezési útmutató

Tekintse át ezt a szakaszt, és ismerkedjen meg a virtuális hálózatok NAT-beli kialakításával kapcsolatos szempontokkal.  

1. [Költségoptimalizálás](#cost-optimization)
1. [A bejövő és a kimenő együttes létezése](#coexistence-of-inbound-and-outbound)
2. [Alapszintű erőforrások kezelése](#managing-basic-resources)
3. [Rendelkezésre állási zónák](#availability-zones)

### <a name="cost-optimization"></a>Költségoptimalizálás

A [szolgáltatási végpontok](virtual-network-service-endpoints-overview.md) és a [magánjellegű hivatkozások](../private-link/private-link-overview.md) a költségeket optimalizáló beállítások. Ezekhez a szolgáltatásokhoz nincs szükség NAT-ra. A virtuális hálózat NAT-kapcsolata nem dolgozza fel a szolgáltatási végpontokra vagy privát hivatkozásokra irányuló forgalmat.  

A szolgáltatás-végpontok az Azure-szolgáltatások erőforrásainak a virtuális hálózatra való összekötését és az Azure-szolgáltatás erőforrásaihoz való hozzáférést vezérlik. Ha például az Azure Storage-hoz fér hozzá, a tároláshoz használjon szolgáltatási végpontot, hogy elkerülje az adatok feldolgozott NAT-díját. A szolgáltatási végpontok ingyenesek.

A privát hivatkozás a virtuális hálózaton belüli privát végpontként teszi elérhetővé az Azure Pásti szolgáltatást (vagy a privát hivatkozással üzemeltetett egyéb szolgáltatásokat).  A privát hivatkozás számlázása az időtartam és a feldolgozott adatmennyiség alapján történik.

Értékelje ki, hogy az egyik vagy mindkét megközelítés jól illeszkedik-e a forgatókönyvhöz, és igény szerint használja-e.

### <a name="coexistence-of-inbound-and-outbound"></a>A bejövő és a kimenő együttes létezése

A NAT-átjáró kompatibilis a rel:

 - Standard Load Balancer
 - Standard nyilvános IP-cím
 - Szabványos nyilvános IP-előtag

Új üzemelő példány fejlesztésekor a standard SKU-val kell kezdeni.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network." width="256" title="NAT Virtual Network az internet felé">
</p>

*Ábra: Virtual Network NAT a kimenő internethez*

A NAT-átjáró által biztosított internetes kimenő forgatókönyvek kiterjeszthetők az internetről érkező funkciókkal. Minden erőforrás ismeri a folyamat eredetének irányát. NAT-átjárót tartalmazó alhálózaton a NAT-átjáró felülírja az összes kimenő – internetes forgatókönyvet. Az internetes forgatókönyvek bejövő adatait a megfelelő erőforrás biztosíthatja.

#### <a name="nat-and-vm-with-instance-level-public-ip"></a>NAT és virtuális gép példány szintű nyilvános IP-címmel

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP." width="300" title="Virtual Network NAT és virtuális gép példány szintű nyilvános IP-címmel">
</p>

*Ábra: Virtual Network NAT és virtuális gép példány szintű nyilvános IP-címmel*

| Irány | Erőforrás |
|:---:|:---:|
| Bejövő | Példány szintű nyilvános IP-címmel rendelkező virtuális gép |
| Kimenő | NAT-átjáró |

A virtuális gép a NAT-átjárót fogja használni a kimenő forgalomhoz.  A bejövő származik nem érinti.

#### <a name="nat-and-vm-with-public-load-balancer"></a>NAT és virtuális gép nyilvános Load Balancer

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with a public load balancer." width="350" title="Virtual Network NAT és virtuális gép nyilvános Load Balancer">
</p>

*Ábra: Virtual Network NAT és virtuális gép nyilvános Load Balancer*

| Irány | Erőforrás |
|:---:|:---:|
| Bejövő | nyilvános Load Balancer |
| Kimenő | NAT-átjáró |

Egy terheléselosztási szabály vagy kimenő szabályok kimenő konfigurációját a NAT-átjáró váltja fel.  A bejövő származik nem érinti.

#### <a name="nat-and-vm-with-instance-level-public-ip-and-public-load-balancer"></a>NAT és virtuális gép példány-szintű nyilvános IP-címmel és nyilvános Load Balancer

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP and a public load balancer." width="425" title="Virtual Network NAT és virtuális gép példány szintű nyilvános IP-címmel és nyilvános Load Balancer">
</p>

*Ábra: Virtual Network NAT és virtuális gép példány szintű nyilvános IP-címmel és nyilvános Load Balancer*

| Irány | Erőforrás |
|:---:|:---:|
| Bejövő | Példány szintű nyilvános IP-címmel rendelkező virtuális gép és nyilvános Load Balancer |
| Kimenő | NAT-átjáró |

Egy terheléselosztási szabály vagy kimenő szabályok kimenő konfigurációját a NAT-átjáró váltja fel.  A virtuális gép a NAT-átjárót is használja a kimenő rendszerhez.  A bejövő származik nem érinti.

### <a name="managing-basic-resources"></a>Alapszintű erőforrások kezelése

A standard Load Balancer, a nyilvános IP-cím és a nyilvános IP-előtag kompatibilis a NAT-átjáróval. A NAT-átjárók egy alhálózat hatókörében működnek. Ezeknek a szolgáltatásoknak az alapszintű SKU-t NAT-átjáró nélküli alhálózatra kell telepíteni. Ez az elkülönítés lehetővé teszi, hogy mindkét SKU-változat egyidejűleg ugyanazzal a virtuális hálózattal is létezhet.

A NAT-átjárók elsőbbséget élveznek az alhálózat kimenő eseteivel szemben. Az alapszintű terheléselosztó vagy a nyilvános IP-cím (és a velük létrehozott felügyelt szolgáltatások) nem állíthatók be a megfelelő fordításokkal. A NAT-átjáró átirányítja a kimenő forgalmat egy alhálózat internetes forgalmára. Az alapszintű Load Balancer és a nyilvános IP-cím bejövő forgalma nem érhető el. Egy alapszintű terheléselosztó és egy virtuális gépen konfigurált nyilvános IP-cím bejövő forgalma nem lesz elérhető.

### <a name="availability-zones"></a>Rendelkezésre állási zónák

#### <a name="zone-isolation-with-zonal-stacks"></a>Zónák elkülönítése a többzónás készletekkel

<p align="center">
  <img src="media/nat-overview/az-directions.svg" alt="Figure depicts three zonal stacks, each of which contains a NAT gateway and a subnet." width="425" title="Virtual Network NAT a zónák elkülönítésével, több létrehozása "zonal stacks"">
</p>

*Ábra: Virtual Network NAT a zónák elkülönítésével, több "zónákhoz tartozó verem létrehozása"*

A NAT a rendelkezésre állási zónák nélkül is rugalmas, és képes túlélni több infrastruktúra-összetevő meghibásodását.  A rendelkezésre állási zónák erre a rugalmasságra épülnek a NAT zónák elkülönítési eseteivel.

A virtuális hálózatok és alhálózatok regionális szerkezetek.  Az alhálózatok nem korlátozódnak egy zónára.

Ha a NAT-átjárót használó virtuálisgép-példány egy, a NAT-átjáró erőforrásával megegyező zónában van, akkor a zóna elkülönítésére vonatkozó zónák is megtalálhatók. A zónák elkülönítéséhez használni kívánt minta létrehoz egy "zonay stack"-t egy rendelkezésre állási zónában.  Ez a "zónákra épülő verem" a virtuálisgép-példányok, a NAT-átjáró erőforrásai, a nyilvános IP-címek és/vagy az alhálózaton lévő előtag-erőforrások egy olyan alhálózaton találhatók, amelyet feltételeznek, hogy csak az azonos zónát   Ezután a vezérlési sík műveletei és az adatsíkon a megadott zónához vannak igazítva és korlátozva. 

Hiba történt egy olyan zónában, ahol a forgatókönyv nem lesz hatással a NAT-ra. Az ugyanabban a zónában lévő virtuális gépek kimenő forgalma a zónák elkülönítése miatt sikertelen lesz.  

#### <a name="integrating-inbound-endpoints"></a>Bejövő végpontok integrálása

Ha a forgatókönyvben bejövő végpontokra van szükség, két lehetőség közül választhat:

| Beállítás | Mintázat | Példa | Pro | CON |
|---|---|---|---|---|
| 1 | **Igazítsa** a bejövő végpontokat azokhoz a megfelelő, a kimenő számára létrehozott **zónákhoz** . | Hozzon létre egy standard Load balancert a zóna-előtérbeli felülettel. | Ugyanaz az állapot-modell és meghibásodási mód a bejövő és kimenő műveletekhez. Egyszerűbb működés. | Az egyes zónákhoz tartozó IP-címeket közös DNS-névvel kell eltakarni. |
| (2) | A zónák **közötti** bejövő végpontok **átfedésben** vannak. | Hozzon létre egy standard Load Balancer zónával redundáns előtérbeli felülettel. | Egyetlen IP-cím a bejövő végponthoz. | A bejövő és a kimenő állapot modell-és meghibásodási módjai eltérőek.  Összetettebb a működéshez. |

>[!NOTE]
> A Zone-elkülönített NAT-átjárók IP-címeket igényelnek a NAT-átjáró zónájának megfelelően. A NAT-átjáró erőforrásai egy másik zónából származó IP-címekkel vagy egy zóna nélkül nem engedélyezettek.

#### <a name="cross-zone-outbound-scenarios-not-supported"></a>A zónák közötti kimenő forgatókönyvek nem támogatottak

<p align="center">
  <img src="media/nat-overview/az-directions2.svg" alt="Figure depicts three zonal stacks, each of which contains a NAT gateway and a subnet, with the connections between to of the gateways and their subnets broken." width="425" title="Virtual Network NAT nem kompatibilis a zóna-átívelő alhálózattal">
</p>

*Ábra: Virtual Network NAT nem kompatibilis a zóna-átívelő alhálózattal*

Ha a virtuálisgép-példányok ugyanabban az alhálózaton belül több zónában vannak telepítve, a NAT-átjáró erőforrásaival nem érhet el zónákra vonatkozó ígéretet.   Ha pedig több, egy alhálózathoz csatlakoztatott, több zóna számára készült NAT-átjáró található, akkor a virtuálisgép-példány nem tudja, melyik NAT-átjáró erőforrást válassza ki.

Egy zóna-megígérő csinál ' létezik, ha a) a virtuálisgép-példány zónája és a zónákhoz tartozó NAT-átjáró zónája nincs igazítva, vagy b) egy regionális NAT-átjáró erőforrást használ a Zona virtuálisgép-példányokhoz.

Amíg a forgatókönyv működni fog, az állapot modellje és a meghibásodási mód nincs meghatározva a rendelkezésre állási zónák szempontjából. Ehelyett érdemes lehet zónákhoz vagy az összes regionális környezethez jutni.

>[!NOTE]
>Egy NAT-átjáró erőforrásának zónák tulajdonsága nem változtatható meg.  Telepítse újra a NAT-átjáró erőforrását a kívánt területi vagy zóna-beállítással.

>[!NOTE] 
>Önmagukban az IP-címek nem redundánsak, ha nincs megadva zóna.  Egy [standard Load Balancer előtér-zónája redundáns](../load-balancer/load-balancer-standard-availability-zones.md) , ha egy IP-cím nincs létrehozva egy adott zónában.  Ez nem vonatkozik a NAT-ra.  Csak regionális vagy zóna-elkülönítés támogatott.

## <a name="performance"></a>Teljesítmény

Az egyes NAT-átjáró-erőforrások akár 50 GB/s átviteli sebességet is biztosíthatnak. Az üzemelő példányokat több alhálózatra is feloszthatja, és az egyes alhálózatokat vagy alhálózatokat hozzárendelheti egy NAT-átjáróhoz a vertikális felskálázás érdekében.

Az egyes NAT-átjárók a TCP és az UDP 64 000 folyamatát támogatják a hozzárendelt kimenő IP-címekhez.  Tekintse át a következő, a forrásoldali hálózati címfordítással (SNAT) foglalkozó szakaszt, valamint a probléma megoldására vonatkozó útmutatást a [hibaelhárítási cikkben](./troubleshoot-nat.md) .

## <a name="source-network-address-translation"></a>Forrás hálózati címfordítás

A forrás hálózati címfordítás (SNAT) egy másik IP-címről származó folyamat forrását írja le.  A NAT-átjáró erőforrásai a SNAT gyakran hivatkoznak a port címfordítás (PAT) használatára. A PAT újraírja a forrás és a forrás portját. A SNAT nem rendelkezik rögzített kapcsolattal a privát címek száma és a lefordított nyilvános címek között.  

### <a name="fundamentals"></a>Alapismeretek

Lássunk egy példát négy folyamatra, hogy megismertesse az alapvető koncepciót.  A NAT-átjáró nyilvános IP-cím típusú erőforrás-65.52.1.1 használ, és a virtuális gép kapcsolatot hoz a 65.52.0.1.

| Folyamat | Forrás rekord | Cél rekord |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |

A folyamat a PAT megtörténte után is így néz ki:

| Folyamat | Forrás rekord | SNAT'ed-forrás rekordja | Cél rekord | 
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | **65.52.1.1:1234** | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | **65.52.1.1:1235** | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | **65.52.1.1:1236** | 65.52.0.1:80 |

A cél a folyamat forrását fogja látni a 65.52.0.1 (SNAT-forrás rekord), amely a hozzárendelt portot mutatja.  A PAT, ahogy az előző táblázatban is látható, a port maszkolása SNAT is nevezik.  A több privát forrás egy IP-cím és egy port mögött van.  

#### <a name="source-snat-port-reuse"></a>forrás (SNAT) portjának újrafelhasználása

A NAT-átjárók felhasználják a forrás-(SNAT-) portok használatát.  A következő példa bemutatja ezt a koncepciót a bájtértékre-készlethez kapcsolódó további folyamatként.  A példában szereplő virtuális gép a 65.52.0.2 folyamat.

| Folyamat | Forrás rekord | Cél rekord |
|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

Egy NAT-átjáró valószínűleg lefordítja a 4. folyamatot egy olyan portra, amely más célállomásokhoz is használható.  Az IP-címek kiosztásának megfelelő méretezésével kapcsolatban lásd: [Méretezés](#scaling) további megbeszélésekhez.

| Folyamat | Forrás rekord | SNAT'ed-forrás rekordja | Cél rekord | 
|:---:|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.1.1:**1234** | 65.52.0.2:80 |

A fenti példában nem kell függőséget kiosztani a forrás portjaihoz.  Az előző az alapvető koncepció szemléltetése.

A NAT által biztosított SNAT számos szempontból eltér a [Load Balancer](../load-balancer/load-balancer-outbound-connections.md) .

### <a name="on-demand"></a>Igény szerinti

A NAT igény szerinti SNAT-portokat biztosít az új kimenő forgalom forgalmához. A leltárban lévő összes elérhető SNAT-portot a NAT-nal konfigurált alhálózatokon található bármely virtuális gép használja. 

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" alt="Figure depicts inventory of all available SNAT ports used by any virtual machine on subnets configured with N A T." width="550" title="Virtual Network NAT igény szerinti kimenő SNAT">
</p>

*Ábra: Virtual Network NAT igény szerinti kimenő SNAT*

A virtuális gépek bármely IP-konfigurációja szükség szerint képes kimenő folyamatokat létrehozni igény szerint.  Az előzetes kiosztást, az egyes példányok esetében a legrosszabb esetek túlzott kiépítését, nem kötelező.  

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" alt="Figure depicts inventory of all available SNAT ports used by any virtual machine on subnets configured with N A T with exhaustion threshold." width="550" title="Különbségek a kimerülési forgatókönyvekben">
</p>

*Ábra: különbségek a kimerülési forgatókönyvekben*

A SNAT-portok kiadása után a NAT-nal konfigurált alhálózatokon található bármely virtuális gép használhatja azt.  Az igény szerinti foglalás lehetővé teszi, hogy az alhálózatok dinamikus és eltérő számítási feladatait a szükséges SNAT-portok használatára használják.  Amíg rendelkezésre áll a SNAT-portok készlete, a SNAT-folyamatok sikeresek lesznek. A SNAT port elérési helyei a nagyobb leltár előnyeit élvezik. A SNAT portok nem maradnak meg a virtuális gépeken, amelyek nem igényelnek aktívan rájuk.

### <a name="scaling"></a>Méretezés

A NAT méretezése elsősorban a megosztott, elérhető SNAT-készletek felügyeletének funkciója. A NAT-nak elegendő SNAT-portra van szüksége a NAT-átjáró erőforrásához csatolt összes alhálózat várható kimenő forgalmához.  SNAT-készlet létrehozásához használhatja a nyilvános IP-címek erőforrásait, a nyilvános IP-előtag erőforrásait vagy mindkettőt.  

>[!NOTE]
>Ha nyilvános IP-előtag-erőforrást rendel hozzá, a rendszer a teljes nyilvános IP-előtagot fogja használni.  Nem rendelhet nyilvános IP-előtag-erőforrást, majd kioszthatja az egyes IP-címeket más erőforrásokhoz való hozzárendeléshez.  Ha egyéni IP-címeket szeretne hozzárendelni egy nyilvános IP-előtagból több erőforráshoz, létre kell hoznia egy egyedi nyilvános IP-címet a nyilvános IP-előtag-erőforrásból, és szükség szerint hozzá kell rendelnie őket a nyilvános IP-előtagi erőforrás helyett.

A SNAT egy vagy több nyilvános IP-címre képezi le a magánhálózati címeket, a forrás-és a forrásport újraírásával a folyamatokban. A NAT-átjáró erőforrása a 64 000 portot (SNAT-port) konfigurált nyilvános IP-cím alapján fogja használni a fordításhoz. A NAT-átjáró erőforrásai legfeljebb 16 IP-címet és 1 millió SNAT-portot tudnak méretezni. Ha egy nyilvános IP-előtag-erőforrás van megadva, az előtagon belüli összes IP-cím biztosítja a SNAT-port leltározását. A több nyilvános IP-cím hozzáadása pedig növeli a rendelkezésre álló leltározási SNAT-portokat. A TCP és az UDP külön SNAT-portok és nem kapcsolódók.

A NAT-átjáró erőforrásai felhasználják a forrás-(SNAT-) portok használatát. A skálázási célokra szolgáló tervezési útmutatásként feltételezni kell, hogy minden folyamathoz új SNAT-port szükséges, és a kimenő forgalom számára elérhető IP-címek teljes számát meg kell méretezni.  Alaposan gondolja át, hogy milyen méretezést tervez, és ennek megfelelően kiépíti az IP-címek mennyiségét.

Ha lehetséges, a SNAT-portok a különböző célhelyekre való használata valószínűleg újra felhasználható. A SNAT-portok kimerülésének módszerei azonban sikertelenek lehetnek.  

Lásd például a [SNAT alapjai](#source-network-address-translation) című témakört.


### <a name="protocols"></a>Protokollok

A NAT-átjáró erőforrásai az UDP-és TCP-folyamatok IP-és IP-továbbítási fejlécével működnek együtt, és az alkalmazás rétegbeli hasznos adatokhoz is függetlenek.  Más IP-protokollok nem támogatottak.

### <a name="timers"></a>Időzítők

>[!IMPORTANT]
>A hosszú üresjárati időzítő szükségtelenül növelheti a SNAT kimerülésének valószínűségét. Minél hosszabb időzítőt ad meg, a hosszabb NAT a SNAT-portoknál tovább tart, amíg az Üresjárati időkorlát nem lesz. Ha a folyamatok üresjárati időkorláttal rendelkeznek, a folyamat végül amúgy is meghiúsul, és szükségtelenül felhasználja a SNAT-portok leltározását.  A 2 órán át meghiúsuló folyamatok az alapértelmezett 4 percen belül sikertelenek lesznek. Az Üresjárati időkorlát növelése az utolsó megoldás, amelyet takarékosan kell használni. Ha egy folyamat soha nem tétlen, a tétlen időzítő nem fogja befolyásolni.

A TCP Üresjárati időkorlát 4 perc (alapértelmezett) és 120 perc (2 óra) között állítható be minden folyamathoz.  Emellett alaphelyzetbe állíthatja az üresjárati időzítőt a folyamat forgalmával.  A hosszú üresjárati kapcsolatok és a végpontok közötti élő észlelések frissítésére szolgáló javasolt minta a TCP-Keepalives.  A TCP-Keepalives ismétlődő nyugták jelennek meg a végpontok számára, alacsony terheléssel és az alkalmazás rétegében láthatatlanok.

A következő időzítők használatosak a SNAT-port kiadásához:

| Időzítő | Érték |
|---|---|
| TCP FIN | 60 másodperc |
| ELSŐ TCP | 10 másodperc |
| TCP félig nyitott | 30 másodperc |

A SNAT-portok 5 másodperc elteltével újra felhasználhatók ugyanarra a cél IP-címhez és célport-portra.

>[!NOTE] 
>Ezek az időzítő beállítások változhatnak. Az értékek segítséget nyújtanak a hibaelhárításhoz, és jelenleg nem kell függőséget adni a megadott időzítők közül.

## <a name="limitations"></a>Korlátozások

- A NAT kompatibilis a standard SKU nyilvános IP-címmel, a nyilvános IP-előtaggal és a terheléselosztó erőforrásaival.   Az alapszintű erőforrások (például az alapszintű Load Balancer) és a belőlük származtatott termékek nem kompatibilisek a NAT-val.  Az alapszintű erőforrásokat a NAT-mel nem konfigurált alhálózatra kell helyezni.
- Az IPv4-címek családja támogatott.  A NAT nem támogatja az IPv6-cím családját.  A NAT nem helyezhető üzembe IPv6-előtaggal rendelkező alhálózaton.
- A NAT nem tud több virtuális hálózatot kifogni.
- Az IP-töredezettség nem támogatott.

## <a name="suggestions"></a>Javaslatok

Szeretnénk tudni, hogyan lehet javítani a szolgáltatást. Hiányzik egy képesség? Tegyük fel, hogy mi a következő lépés a [UserVoice for NAT](https://aka.ms/natuservoice)esetében.

## <a name="next-steps"></a>Következő lépések

* További tudnivalók a [Virtual Network NAT](nat-overview.md)-ról.
* Tudnivalók a [NAT-átjáró erőforrásaira vonatkozó mérőszámokról és riasztásokról](nat-metrics.md).
* További információ a [NAT-átjárók erőforrásainak hibaelhárításáról](troubleshoot-nat.md).
