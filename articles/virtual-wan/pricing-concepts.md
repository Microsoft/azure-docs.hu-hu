---
title: A Virtual WAN díjszabása
titleSuffix: Azure Virtual WAN
description: Ez a cikk a virtuális WAN díjszabásával kapcsolatos gyakori kérdéseket ismerteti
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: reyandap
ms.custom: references_pricing
ms.openlocfilehash: 13451291544f704000ab61d41ed5014fa69298e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100517688"
---
# <a name="about-virtual-wan-pricing"></a>A Virtual WAN díjszabása

Az Azure Virtual WAN több hálózati és biztonsági szolgáltatást is tartalmaz egy egységes keretrendszerben. Központi és küllős architektúrán alapul, ahol a központok a Microsoft által felügyelt különböző szolgáltatásokkal rendelkeznek, például VPN, ExpressRoute, felhasználói VPN (pont – hely), tűzfal, útválasztás stb.

A virtuális WAN minden szolgáltatása díjszabás szerint történik. Ezért arra utal, hogy egyetlen díj nem alkalmazható a virtuális WAN-ra. Az [Azure díjszabási kalkulátora](https://azure.microsoft.com/pricing/calculator/) egy mechanizmust biztosít a költség kinyeréséhez, amely a virtuális WAN-ban kiépített szolgáltatásokon alapul. Ez a cikk a virtuális WAN díjszabásával kapcsolatos gyakori kérdéseket tárgyalja.

>[!NOTE]
>A jelenlegi díjszabási információkért lásd: [virtuális WAN díjszabása](https://azure.microsoft.com/pricing/details/virtual-wan/). Az Inter-hub (hub – hub) díjak nem jelennek meg a virtuális WAN díjszabási oldalán, mivel a Inter-Region (a kontinensen belüli/kontinentális) díjak [Azure-adatátviteli díjakra](https://azure.microsoft.com/pricing/details/bandwidth/)vonatkoznak.

## <a name="common-pricing-questions"></a><a name="questions"></a>Gyakori díjszabási kérdések

### <a name="what-is-a-scale-unit"></a><a name="scale-unit"></a>Mi az a méretezési egység?

A **skálázási egység** biztosítja a helyek közötti (S2S), a pont – hely (P2S) és a EXPRESSROUTE (er) teljes kapacitásának egységét egy virtuális központban. Például:

* **1 a S2S VPN-méretezési egység** teljes kapacitást jelent a 500 Mbps-os VPN-átjáró számára (a kettős példányok a rugalmasság érdekében vannak üzembe helyezve) egy virtuális hub costing $0.361/óra.
* **1 a skálázási egység** összesen 2 GB/s-os átjárót foglal magában a virtuális hub costing $0.42/HR-ben.
* **5 a skálázási egységek** összesen 10 GB/s-os átjárót jelentenek a virtuális központon belüli VNet, amely ára a $0.42 * 5/HR. A $0,25/HR a 6 – 10 méretezési egységig növekszik.

### <a name="what-is-a-connection-unit"></a><a name="connection-unit"></a>Mi az a kapcsolatok egysége?

A **kapcsolati egység** minden olyan helyszíni/nem Microsoft-végpontra vonatkozik, amely az Azure-átjáróhoz csatlakozik. Helyek közötti VPN esetén ez az érték ágakat is magában foglalja. A felhasználói VPN (pont – hely) esetén ez az érték a távoli felhasználókat jelenti. A ExpressRoute esetében ez az érték a ExpressRoute áramköri kapcsolatait foglalja magában.<br>Például:

* Egy fiókirodai kapcsolat, amely az Azure VPN-hez csatlakozik egy virtuális hubhoz, a $0,05/HR-költségekkel. 100 ezért az Azure-beli virtuális hubhoz csatlakozó fiókirodai kapcsolatok ára 0,05 * 100/óra.

* A virtuális központhoz csatlakozó két ExpressRoute áramköri kapcsolat ára 0,05 * 2/óra.

* Az Azure Virtual hub P2S-átjáróhoz csatlakozó három távoli felhasználói kapcsolat ára: 0,03 * 3/HR.

### <a name="how-are-data-transfer-charges-calculated"></a><a name="data-transfer"></a>Hogyan számítják ki az adatátviteli díjakat?

* Az Azure-ba beérkező forgalomért nem számítunk fel díjat. Az Azure-t (VPN, ExpressRoute vagy pont – hely felhasználói VPN-kapcsolaton keresztül) elhagyó forgalomra a szabványos [Azure-adatátviteli díjak](https://azure.microsoft.com/pricing/details/bandwidth/)vonatkoznak.

* A virtuális WAN-központ és a VNet közötti adatátviteli díjak esetén az adatátviteli díjak a hub-t elhagyó forgalomra érvényesek. Példa: az USA keleti régiójában elhagyott forgalomért az USA nyugati régiója felé haladva 0,02/GB lesz felszámítva. Az USA nyugati régiójában érkező forgalomért nem számítunk fel díjat. Az összes hub-ra irányuló forgalom Inter-Region (a kontinensen belüli/kontinentális) díjköteles [Azure-adatátviteli díjat](https://azure.microsoft.com/pricing/details/bandwidth/)számítunk fel. 

### <a name="what-is-the-difference-between-a-standard-hub-fee-and-a-standard-hub-processing-fee"></a><a name="fee"></a>Mi a különbség a standard hub-díj és a standard hub-feldolgozási díj között?

A Virtual WAN két változatban érhető el:

* **Alapszintű virtuális WAN**, ahol A felhasználók több hubokat telepíthetnek, és a VPN-helyek közötti kapcsolatot is igénybe vehetik. Az alapszintű virtuális WAN nem rendelkezik olyan fejlett képességekkel, mint a teljes mértékben Mesh hubok, a ExpressRoute-kapcsolat, a felhasználói VPN/pont – hely VPN-kapcsolat, a VNet, a VNet tranzitív kapcsolat, a VPN-és a ExpressRoute-átviteli kapcsolat, vagy a Azure Firewall stb. Alapszintű virtuális WAN-beli hubok esetében nincs alapdíj vagy adatfeldolgozási díj.

* A **standard szintű virtuális WAN** olyan speciális képességeket biztosít, mint például a teljes mértékben felépített hubok, a ExpressRoute-kapcsolatok, a felhasználói VPN/pont – hely VPN-kapcsolat, a VNet, a VNet tranzitív kapcsolat, a VPN-és a ExpressRoute átviteli kapcsolat, Azure Firewall stb. Az összes virtuális központ útválasztását egy olyan útválasztó biztosítja, amely lehetővé teszi több szolgáltatás használatát egy virtuális központban. A hub alapdíja a 0,25 USD/óra áron érhető el. Az adatfeldolgozás díja a Virtual hub-útválasztón VNet-VNet átviteli kapcsolatra is vonatkozik. Ezzel kapcsolatban lásd a következő ábrát:

 Az alábbi **ábrán** két virtuális hálózatok található, a VNET 1 és a VNET 2. Tegyük fel, hogy 1 GB/s-os adatküldés érkezik egy virtuális gépről az 1. VNET egy másik virtuális gépre a 2. VNET. A következő díjak érvényesek:

* Virtual hub alapdíj (USD 0,25/óra)

* A virtuális hub adatfeldolgozási díja 0,02/GB 1 GB/s értékre

**Példa**

   :::image type="content" source="./media/pricing-concepts/figure-1.png" alt-text="Példa":::

## <a name="next-steps"></a>Következő lépések

* A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.

* A jelenlegi díjszabással kapcsolatban lásd: [Virtual WAN díjszabása](https://azure.microsoft.com/pricing/details/virtual-wan/).
