---
title: Az Azure-partneri szolgáltatás áttekintése
description: További tudnivalók az Azure-partneri szolgáltatás áttekintése
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 198ba23920179e71e095e498ee2173d7f0111d42
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026729"
---
# <a name="azure-peering-service-overview"></a>Az Azure-partneri szolgáltatás áttekintése

Az Azure társközi szolgáltatás olyan hálózati szolgáltatás, amely a Microsoft Cloud Services, például a Microsoft 365, a Dynamics 365, a szolgáltatott szoftver (SaaS) szolgáltatásai, az Azure vagy bármely, a nyilvános interneten keresztül elérhető Microsoft-szolgáltatás számára teszi lehetővé az ügyfelek kapcsolódását. A Microsoft az Internet szolgáltatók (internetszolgáltatók), az internetes Exchange-partnerek (IXPs-EK) és a szoftveresen definiált Cloud Interconnect-(SDCI-) szolgáltatókkal együttműködve megbízható és nagy teljesítményű nyilvános kapcsolatot biztosít az ügyfél és a Microsoft hálózata közötti optimális útválasztással.

A partneri szolgáltatással az ügyfelek kiválaszthatnak egy megfelelően csatlakoztatott partner-szolgáltatót az adott régióban. A nyilvános kapcsolat nagy megbízhatóságra és minimális késésre van optimalizálva a felhőalapú szolgáltatásokból a végfelhasználói helyre.

![Elosztott kapcsolat a Microsoft cloudtal](./media/peering-service-about/peering-service-what.png)

Az ügyfelek a telemetria is dönthetnek, például a felhasználói késleltetési mértékeket a Microsoft hálózat, a BGP-útvonalak figyelése, valamint a szivárgások és a eltérített riasztások ellen a Azure Portal. 

A partneri szolgáltatás használatához az ügyfeleknek nem kell regisztrálniuk a Microsofttal. Az egyetlen követelmény, hogy kapcsolatba kell lépnie egy [társ-szolgáltatási partnerrel](location-partners.md) a szolgáltatás beszerzéséhez. Ahhoz, hogy az ügyfelek telemetria a szolgáltatást, regisztrálniuk kell a Azure Portal.

A peering Service regisztrálásával kapcsolatos utasításokért lásd: [a társ-ellenőrzés regisztrálása a Azure Portal használatával](azure-portal.md). 

> [!NOTE]
> Ez a cikk olyan hálózati építészek számára készült, akik a felhővel és az internettel való nagyvállalati kapcsolatért felelősek.


## <a name="what-is-peering-service"></a>Mi az a Peering Service?

Az egyenrangú szolgáltatás:

- A nyilvános internetet használó IP-szolgáltatás. 
- Egy olyan csoportmunka-platform, amely a szolgáltatók és a értéknövelt szolgáltatások révén optimális és megbízható útválasztást biztosít az ügyfeleknek a szolgáltatói partnereken keresztül a Microsoft Cloud számára a nyilvános hálózaton keresztül.

Az egyenrangú szolgáltatás nem egy magánhálózati kapcsolati termék, például az Azure ExpressRoute vagy egy VPN-termék.

> [!NOTE]
> A ExpressRoute kapcsolatos további információkért lásd a [ExpressRoute dokumentációját](../expressroute/index.yml).
>

## <a name="background"></a>Háttér

A Microsoft 365, a Dynamics 365 és bármely más Microsoft SaaS-szolgáltatás több Microsoft-adatközpontban is üzemel, és bármely földrajzi helyről elérhető. A Microsoft globális hálózata a világ bármely pontján található Microsoft Edge (PoP) hellyel rendelkezik, ahol a felhasználók a szolgáltatókon keresztül csatlakozhatnak a végfelhasználóhoz. 

A Microsoft és a partner szolgáltatók gondoskodnak arról, hogy a társ-szolgáltatási kapcsolattal regisztrált előtagok forgalma a Microsoft globális hálózatának legközelebbi Microsoft Edge PoP-helyeiről lépjen be. A Microsoft biztosítja, hogy a egressing a társ-létesítési szolgáltatás kapcsolataiban regisztrált előtagok hálózati forgalma a Microsoft globális hálózatának legközelebbi Microsoft Edge PoP-helyeire kerül.

![Microsoft-hálózat és nyilvános kapcsolat](./media/peering-service-about/peering-service-background-final.png)

> [!NOTE]
> A Microsoft globális hálózattal kapcsolatos további információkért lásd: [Microsoft Global Network](../networking/microsoft-global-network.md).
>

## <a name="why-use-peering-service"></a>Miért érdemes az egyenrangú szolgáltatást használni?

Azok a vállalatok, akik először szeretnének hozzáférni a felhőhöz, vagy az SD-WAN architektúrát vagy nagy mértékben használják a Microsoft SaaS-szolgáltatásokat, robusztus és nagy teljesítményű internetkapcsolatra van szükségük. Az ügyfelek az átállást a társ-szolgáltatás használatával tehetik meg. A Microsoft és a szolgáltatók együttműködve megbízható és teljesítmény-központú nyilvános kapcsolatot biztosítanak a Microsoft-felhőhöz. A legfontosabb ügyfélszolgálati funkciók némelyike itt látható:

- A legjobb nyilvános útválasztás az interneten keresztül, hogy az optimális teljesítmény és megbízhatóság érdekében Microsoft Azure Cloud Services.
- Kiválaszthatja a kívánt szolgáltatót a Microsoft-felhőhöz való kapcsolódáshoz.
- A forgalmi adatok, például a késési jelentések és az előtagok figyelése.
- Optimális hálózati ugrások (ugrások) a Microsoft Cloud-ból.
- Route Analytics és statisztika: események ([BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)) útvonal-rendellenességek (szivárgás vagy eltérített észlelés) és az optimálisnál rosszabb útválasztás.

### <a name="robust-reliable-peering"></a>Robusztus, megbízható társ

A társítási szolgáltatás két típusú redundanciát használ:

- **Helyi redundancia**

   A Microsoft és a szolgáltatók több Microsoft Edge-beli PoP-helyet összekötnek a társ-szolgáltatás nyújtásához. Minden helyen az összekapcsolásnak támogatnia kell a két útválasztó közötti feladatátvételt.

   Minden egyes társítási hely redundáns és sokrétű kapcsolattal van kiépítve.

- **Geo-redundancia**

   A Microsoft több Metro-helyen is csatlakoztatta a szolgáltatókat, így ha az egyik peremhálózati csomópontnál a teljesítmény csökken, a forgalom a Microsoft felé irányuló és onnan érkező adatforgalmat is elvégezheti a másik helyen. A Microsoft az optimális teljesítmény érdekében az SDN-alapú útválasztási szabályzatok használatával irányítja át a forgalmat a globális hálózatában.

    Az ilyen típusú redundancia a legrövidebb útválasztási útvonalat használja, ha mindig kiválasztja a legközelebbi Microsoft Edge-PoP-t a végfelhasználóhoz, és biztosítja, hogy az ügyfél egy hálózati ugrás (ugrás) legyen a Microsofttól.

   ![Georedundancia](./media/peering-service-about/peering-service-geo-shortest.png)

### <a name="optimal-routing"></a>Optimális Útválasztás

A következő útválasztási módszer használata javasolt:

-  **Hideg-burgonya Útválasztás**

   A szoftver által definiált, hideg burgonya-útválasztási technika a Microsoft Cloud-ból származó hálózati forgalom vezérlését biztosítja. Gondoskodik arról, hogy a forgalom a nagy kapacitású, kis késésű és megbízható Microsoft globális hálózaton maradjon, amíg a lehető legközelebb nem lesz a célhelyhez.
   
   A hideg burgonya technikát nem használó útválasztást forró burgonya-útválasztásnak nevezzük. A forró burgonya-útválasztással a Microsoft-felhőből származó forgalom az interneten keresztül halad.

   ![Hideg-burgonya Útválasztás](./media/peering-service-about/peering-service-cold-potato.png)

### <a name="monitoring-platform"></a>Figyelési platform

   A szolgáltatás figyelése az ügyfelek forgalmának és útválasztásának elemzésére szolgál, és a következő képességeket biztosítja: 

-  **Internet BGP Route-rendellenességek észlelése**
          
   Ezzel a szolgáltatással észlelheti és riasztást küld minden olyan adatrendellenesség-eseményről, mint például az átirányítás az ügyfél előtagjai számára.

-  **Ügyfél késése**

   Ez a szolgáltatás figyeli az ügyfél helye és a Microsoft közötti útválasztási teljesítményt. 
   
   Az útválasztási teljesítmény mérése az ügyféltől a Microsoft Edge-PoP eléréséhez szükséges idő ellenőrzésével történik. Az ügyfelek megtekinthetik a késési jelentéseket a különböző földrajzi helyekhez.

   A figyelés a szolgáltatások romlása esetén rögzíti az eseményeket.

   ![Figyelési platform a társ-felügyeleti szolgáltatáshoz](media/peering-service-about/peering-service-latency-report.png)

### <a name="traffic-protection"></a>Forgalmi védelem

Az Útválasztás csak olyan előnyben részesített útvonalon történik, amelyet a rendszer az ügyfélnek a partneri szolgáltatással való regisztrálása során határoz meg.

A Microsoft garantálja, hogy a forgalmat az előnyben részesített útvonalakon keresztül irányítsa, még akkor is, ha kártékony tevékenységet észlel.

A BGP-útvonal rendellenességeit a Azure Portal, ha van ilyen.

## <a name="next-steps"></a>Következő lépések

- A társ-szolgáltatási kapcsolatokról további információt a következő témakörben talál: [társ-szolgáltatási kapcsolatok](connection.md).
- A társ-szolgáltatási kapcsolatok telemetria kapcsolatos további tudnivalókért lásd: társítási [szolgáltatás kapcsolódási telemetria](connection-telemetry.md).
- Szolgáltatói partner kereséséhez tekintse meg a következő témakört: [társ-szolgáltatási partnerek és helyszínek](location-partners.md).
- A társ-szolgáltatási kapcsolatok bevezetéséhez tekintse meg a következő témakört: előkészítési [szolgáltatási modell](onboarding-model.md).
- Ha a Azure Portal használatával szeretne regisztrálni egy kapcsolatot, tekintse meg a [társ-szolgáltatási kapcsolatok regisztrálása a Azure Portal használatával](azure-portal.md)című témakört.
- A telemetria méréséhez lásd: a [kapcsolatok telemetria mérése](measure-connection-telemetry.md).