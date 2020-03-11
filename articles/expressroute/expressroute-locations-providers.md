---
title: 'Helyek és kapcsolatszolgáltatók: Azure ExpressRoute | Microsoft Docs'
description: A cikk részletes áttekintést nyújt a szolgáltatási helyekről és az Azure-régiókhoz való csatlakozásról. Hely alapján rendezve.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/15/2020
ms.author: cherylmc
ms.openlocfilehash: aa0deaf00a5121d50a2621d146ac83f97ee45df4
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "79081709"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute-partnerek és társviszony-létesítési helyszínek.

> [!div class="op_single_selector"]
> * [Helyek szolgáltató alapján](expressroute-locations.md)
> * [Szolgáltatók hely alapján](expressroute-locations-providers.md)


A cikkben szereplő táblázatok a földrajzi lefedettséggel és a ExpressRoute, a ExpressRoute és a ExpressRoute rendszerintegrátorokkal (SIs) kapcsolatos információkat tartalmaznak.

> [!Note]
> Az Azure-régiók és a ExpressRoute két különböző fogalmat alkotnak, és a kettő közötti különbség kritikus fontosságú az Azure Hybrid Networking-kapcsolat megismeréséhez. 
>
>

## <a name="azure-regions"></a>Azure-régiók
Az Azure-régiók olyan globális adatközpontok, ahol az Azure számítási, hálózati és tárolási erőforrásai találhatók. Az Azure-erőforrások létrehozásakor az ügyfélnek ki kell választania egy erőforrás-helyet. Az erőforrás helye határozza meg, hogy az erőforrás melyik Azure-adatközpontban (vagy rendelkezésre állási zónában) jön létre.

## <a name="expressroute-locations"></a>ExpressRoute-helyek
A ExpressRoute helyek (más néven a társítási helyek vagy a saját helyek) olyan közös elhelyezési létesítmények, ahol a Microsoft Enterprise Edge (MSEE) eszközei találhatók. A ExpressRoute helye a Microsoft hálózatának belépési pontja – globálisan elosztott, így az ügyfelek a világ különböző pontjain csatlakozhatnak a Microsoft hálózatához. Ezek a helyszínek, ahol a ExpressRoute-partnerek és a ExpressRoute Direct-ügyfelek a Microsoft hálózatához csatlakoznak. Általánosságban elmondható, hogy a ExpressRoute helye nem kell, hogy egyezzen az Azure-régióval. Például egy ügyfél létrehozhat egy ExpressRoute-áramkört az *USA keleti*régiójában, a *Seattle* -beli összevonási helyen.

Ha legalább egy ExpressRoute-helyhez csatlakozott egy geopolitikai régióban, az adott geopolitikai régióban lévő összes régióban hozzáférhet az Azure-szolgáltatásokhoz. 

## <a name="locations"></a>Azure-régiók egy geopolitikai régión belüli ExpressRoute
A következő tábla az egyes geopolitikai régiókban lévő Azure-régiók és ExpressRoute-helyek térképét mutatja.

| **Geopolitikai régió** | **Azure-régiók** | **ExpressRoute-helyek** |
| --- | --- | --- |
| **Ausztrália kormánya** | Ausztrália középső régiója, Ausztrália 2. középső régiója |Canberra, Canberra2 |
| **Európa** | Közép-Franciaország, Dél-Franciaország, Észak-Németország, Középnyugat-Németország, Észak-Európa, Kelet-Norvégia, Norvégia nyugati régiója, Észak-Svájc, Nyugat-Svájc, Egyesült Királyság nyugati régiója, Egyesült Királyság déli régiója, Nyugat-Európa |Amszterdam, Amsterdam2, Koppenhága, Dublin, Frankfurt, Genf, London, London2, Marseille, Milánó, München, Newport (Wales), Oslo, Párizs, Stavanger, Stockholm, Zürich, München |
| **Észak-Amerika** | USA keleti régiója, USA nyugati régiója, USA 2. keleti régiója, USA 2. nyugati régiója, USA középső régiója, USA déli középső régiója, USA északi középső régiója, USA középnyugati régiója, Közép-Kanada, Kelet-Kanada |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Szilícium-völgy, szilícium-Valley2, Washington, D.C., Washington DC2, Montreal, Québec város, Toronto, Vancouver |
| **Ázsia** | Kelet-Ázsia, Délkelet-Ázsia | Bangkok, Hongkong, Hong Kong2, Jakarta, Kuala Lumpur, Szingapúr, Szingapúr2, Taipei |
| **India** | Nyugat-India, Közép-India, Dél-India |Csennai, Csennai2, Mumbai, Mumbai2 |
| **Japán** | Nyugat-Japán, Kelet-Japán |Osaka, Tokió, Tokyo2 |
| **Oceania** | Délkelet-Ausztrália, Kelet-Ausztrália |Auckland, Melbourne, Perth, Sydney, Sydney2 | 
| **Dél-Korea** | Dél-Korea középső régiója, Korea déli régiója |Busan, Szöul|
| **Egyesült Arab Emírségek** | UAE Central, Észak-Egyesült Arab | Dubai, Dubai2 |
| **Dél-afrikai Köztársaság** | Dél-Afrika nyugati régiója, Dél-Afrika északi régiója |Fokváros, Johannesburg |
| **Dél-Amerika** | Dél-Brazília |Sao Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Az Azure-régiók és az országos felhők geopolitikai határai
Az alábbi tábla a régiók és az országos felhők geopolitikai határainak információit tartalmazza.

| **Geopolitikai régió** | **Azure-régiók** | **ExpressRoute-helyek** |
| --- | --- | --- |
| **Az Egyesült Államok kormányának felhője** |US Gov Arizona, US Gov Iowa, US Gov Texas, US Gov Virginia, US DoD – középső régió, US DoD – keleti régió  |Atlanta, Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Szilícium-völgy, Washington, D.C. |
| **Kelet-Kína** |Kelet-Kína, Kelet-Kína2 |Shanghai, Shanghai2 |
| **Észak-Kína** |Észak-Kína, Észak-Kína2 |Peking, Beijing2 |
| **Németország** |Közép-Németország, Kelet-Németország |Berlin, Frankfurt |

A geopolitikai régiókon átívelő kapcsolódás nem támogatott a standard ExpressRoute-termékváltozatokban. A globális kapcsolatok támogatásához engedélyeznie kell az ExpressRoute prémium bővítményt. Az országos felhőkörnyezetekhez való kapcsolódás nem támogatott. Igény esetén tájékozódjon kapcsolatszolgáltatójánál a lehetőségekről.

## <a name="partners"></a>ExpressRoute-kapcsolatszolgáltatók

Az alábbi táblázatban a kapcsolati helyek és az egyes helyekhez tartozó szolgáltatók láthatók. A szolgáltatók és a szolgáltatási helyek megtekintéséhez lásd: [Helyek szolgáltatók szerint](expressroute-locations.md).

* A **helyi Azure-régiók** olyanok, amelyek az egyes [ExpressRoute helyi](expressroute-faqs.md) helyeken is hozzáférhetnek. az **n/a** azt jelzi, hogy a ExpressRoute helyi helye nem érhető el az adott egyenrangú helyen.

* A **zóna** a [díjszabásra](https://azure.microsoft.com/pricing/details/expressroute/)vonatkozik.


### <a name="global-commercial-azure"></a>Globális kereskedelmi Azure
| **Hely** | **Cím** | **Zóna** | **Helyi Azure-régiók** | **Közvetlen ER** | **Szolgáltatók** |
| --- | --- | --- | --- | --- | --- |
| **Amszterdam** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | Nyugat-Európa | 10G, 100G | Aryaka Networks Networks, & T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, Cloud, Interxion, KPN, IX REACH, 3. szintű kommunikáció, Megaport, NTT Communications, Orange, Tata Communications, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amszterdam2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | Nyugat-Európa | 10G, 100G | CenturyLink Cloud-kapcsolat, Colt, DE-CIX, euNetworks, Interxion, Orange, Vodafone |
| **Atlanta** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | n/a | n/a | Equinix, Megaport |
| **Auckland** | [Vocus csoport, NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | n/a | 10G | Devoli, Kordia, Megaport, Spark NZ, Vocus csoport, NZ |
| **Bangkok** | [AIS](http://business.ais.co.th/solution/microsoft-azure.html?category=cloud) | 2 | n/a | 10G | AIS |
| **Busan** | [LG CNS](https://www.lgcns.com/datacenter) | 2 | Dél-Korea déli régiója | n/a | LG CNS |
| **Canberra** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Ausztrália középső régiója | 10G, 100G | CDC |
| **Canberra2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Ausztrália 2. középső régiója| 10G, 100G | CDC |
| **Fokváros** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | Dél-Afrika nyugati régiója | 10G | BCX, Internet Solutions – Cloud-kapcsolat, Liquid Telecom, Teraco |
| **Csennai** | Tata Communications | 2 | Dél-India | 10G | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Csennai2** | Airtel | 2 | Dél-India | n/a | Airtel |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | USA északi középső régiója | 10G, 100G | Aryaka Networks hálózatok, & T NetBond, CenturyLink Cloud-kapcsolat, Cologix, Colt, Comcast, Coresite, Equinix, felhő, Internet2, level 3 Communications, Megaport, PacketFabric, PCCW globális korlátozott, sprint, Telia Carrier, Verizon, Zayo |
| **Koppenhága** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | n/a | 10G | Interxion |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | n/a | 10G, 100G | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | USA nyugati középső régiója | n/a | CoreSite, Megaport, Zayo |
| **Dubai** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | Észak-Egyesült Arab | n/a | Etisalat UAE |
| **Dubai2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | Észak-Egyesült Arab | n/a | du datamena, Megaport, Orange, Orixcom |
| **Dublin** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | Észak-Európa | 10G, 100G | Colt, EIR, Equinix, euNetworks, Interxion, Megaport |
| **Frankfurt** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | Középnyugat-Németország | 10G, 100G | CenturyLink Cloud-kapcsolat, Colt, DE-CIX, GEANT, Interxion, Megaport, Orange, Telia Carrier |
| **Genfi** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | Nyugat-Svájc | 10G, 100G | Equinix, Megaport |
| **Hongkong** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | Kelet-Ázsia | n/a | Aryaka Networks Networks, British Telecom, CenturyLink Cloud, a Telecom, a kínai távközlési globális, a Equinix, a felhő, a Megaport, az NTT Communications, a narancssárga, a PCCW globális korlátozott, a Tata Communications, a Telia Carrier, a Verizon |
| **Hong Kong2** | [MEGA-i](https://www.iadvantage.net/index.php/locations/mega-i) | 2 | n/a | 10G | |
| **Dzsakarta** | Telkom Indonézia | 4 | n/a | 10G | |
| **Johannesburg** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | Dél-Afrika északi régiója | 10G | British Telecom, Internet Solutions – Cloud-kapcsolat, Liquid Telecom, Orange, Teraco |
| **Kuala Lumpur** | [Az idő dotCom Menara célja](https://www.aims.com.my/co-location/points-of-presence.html) | 2 | n/a | n/a | TIME dotCom |
| **Las Vegas** | [LV kapcsoló](https://www.switch.com/las-vegas) | 1 | n/a | n/a | CenturyLink Cloud Connect, Megaport |
| **London** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | Az Egyesült Királyság déli régiója | 10G, 100G | & T NetBond, British Telecom, Colt, Equinix, euNetworks, Cloud, Internet Solutions – Cloud-kapcsolat, Interxion, Jisc, level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW globális korlátozott, Tata Communications, KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | [Ház, Észak-két](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | Az Egyesült Királyság déli régiója | 10G, 100G | CenturyLink Cloud-kapcsolat, Colt, IX REACH, Equinix, Megaport, Magánház-KDDI |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | n/a | n/a | CoreSite, Equinix, Megaport, Neutrona hálózatok, NTT, Transtelco, Zayo |
| **Marseille** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | Dél-Franciaország | n/a | DE-CIX, GEANT, Interxion, Jaguar Network, Ooredoo Cloud-kapcsolat |
| **Melbourne** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | Délkelet-Ausztrália | 10G, 100G | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | n/a | 10G | C3ntro, Equinix, Megaport, Neutrona hálózatok |
| **Milánó** | [IRIDEOS](https://irideos.it/en/data-centers/) | 1 | n/a | 10G | |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | n/a | 10G, 100G | Bell Canada, Cologix, Megaport, Telus, Zayo |
| **Mumbai** | Tata Communications | 2 | Nyugat-India | n/a | Globális CloudXchange (GCX), bizalom jio, Sify, Tata Communications, Verizon |
| **Mumbai2** | Airtel | 2 | Nyugat-India | n/a | Airtel, Sify, Vodafone Idea |
| **München** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/) | 1 | n/a | 10G, 100G | |
| **New York** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | n/a | n/a | CenturyLink Cloud-kapcsolat, Colt, Coresite, Equinix, felhő, Megaport, csomag, Zayo |
| **Newport (Wales)** | [Következő generációs adatok](https://www.nextgenerationdata.co.uk) | 1 | Az Egyesült Királyság nyugati régiója | n/a | British Telecom, Colt, 3. szintű kommunikáció, következő generációs adatforgalom |
| **Oszaka** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | Nyugat-Japán | 10G, 100G | Colt, Equinix, Internet Initiative Japan Inc. – IIJ, Megaport, NTT Communications, NTT SmartConnect, Softbank |
| **Oslo** | [DigiPlex Ulven](https://www.digiplex.com/locations/oslo-datacentre) | 1 | Kelet-Norvégia | 10G, 100G | Megaport, Telenor, Telia Carrier |
| **Párizs** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1 | Közép-Franciaország | 10G, 100G | CenturyLink Cloud Connect, Colt, Equinix, Intercloud, Interxion, Orange, Telia Carrier, Zayo |
| **Perth** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | n/a | 10G | Megaport, NextDC |
| **Quebec város** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | Kelet-Kanada | n/a | Bell Canada, Megaport |
| **San Antonio** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | USA déli középső régiója | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | Dél-Brazília | n/a | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | USA nyugati régiója, 2. | 10G, 100G | Aryaka Networks Networks, Equinix, level 3 Communications, Megaport, Telus, Zayo |
| **Szöul** | [KINX Gasan IDC](https://www.kinx.net/support/location/?lang=en) | 2 | Dél-Korea középső régiója | 10G, 100G | KINX, KT, LG CNS, Sejong Telecom |
| **Szilícium-völgy** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | USA nyugati régiója | 10G, 100G | Aryaka Networks hálózatok, & T NetBond, British Telecom, CenturyLink Cloud-kapcsolat, Colt, Comcast, Coresite, Equinix, felhő, Internet2, IX REACH, csomag, PacketFabric, level 3 Communications, Megaport, Orange, sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Szilícium Valley2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | USA nyugati régiója | 10G, 100G | Colt, Coresite | 
| **Szingapúr** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | Délkelet-Ázsia | 10G, 100G | Aryaka Networks Networks, AT & T NetBond, British Telecom, China Mobile International, epszilon globális kommunikáció, Equinix, felhő, szint 3 Communications, Megaport, NTT Communications, Orange, énekel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapore2** | [Globális kapcsoló, Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | Délkelet-Ázsia | 10G, 100G | Kínai Unicom globális, Colt, epszilon Global Communications, Megaport, énekel |
| **Stavanger** | [Zöld hegyi DC1](https://greenmountain.no/dc1-stavanger/) | 1 | Norvégia nyugati régiója | 10G, 100G | |
| **Stockholm** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | n/a | 10G | Equinix, Telia Carrier |
| **Sydney** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | Kelet-Ausztrália | 10G, 100G | AARNet, & T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus csoport NZ |
| **Sydney2** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | Kelet-Ausztrália | 10G, 100G | Megaport, NextDC |
| **Taipei** | Telekommunikációs vezető | 2 | n/a | 10G | Telekommunikációs vezető, FarEasTone |
| **Tokió** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | Kelet-Japán | 10G, 100G | Aryaka Networks Networks, & T NetBond, BBIX, British Telecom, CenturyLink Cloud csatlakozik, Colt, Equinix, Internet Initiative Japan Inc.-IIJ, Megaport, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Tokyo2** | [Tokióban](https://www.attokyo.com/) | 2 | Kelet-Japán | 10G, 100G | |
| **Toronto** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | Közép-Kanada | 10G, 100G | & T NetBond, Bell Canada, CenturyLink Cloud kapcsolatba, Cologix, Equinix, IX REACH Megaport, Telus, Verizon, Zayo |
| **Vancouver** | [Cologix VAN1](https://www.cologix.com/data-centers/vancouver/van1/) | 1 | n/a | 10G, 100G | |
| **Washington, D.C.** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | USA keleti régiója, USA 2. keleti régiója | 10G, 100G | Aryaka Networks hálózatok, & T NetBond, British Telecom, CenturyLink Cloud-kapcsolat, Cologix, Colt, Comcast, Coresite, Equinix, Internet2, felhő, IX elérés, 3. szintű kommunikáció, Megaport, Neutrona hálózatok, NTT Communications, Orange, PacketFabric, SES , Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Washington DC2** | [Coresite Restonban](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 1 | USA keleti régiója, USA 2. keleti régiója | 10G, 100G | CenturyLink Cloud-kapcsolat, Coresite, Intelsat, Viasat, Zayo | 
| **Zürich** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | Észak-Svájc | 10G, 100G | Felhő, Interxion, Megaport, Swisscom |

 **+** = hamarosan elérhető

### <a name="national-cloud-environments"></a>Országos felhőkörnyezetek

Az Azure nemzeti felhők el vannak különítve egymástól és a globális kereskedelmi Azure-tól. Az egyik Azure-beli felhőhöz tartozó ExpressRoute nem csatlakozhatnak a többi Azure-régióhoz.

### <a name="us-government-cloud"></a>Az Egyesült Államok kormányának felhője
| **Hely** | **Cím** | **Helyi Azure-régiók**| **Közvetlen ER** | **Szolgáltatók** |
| --- | --- | --- | --- | --- |
| **Atlanta** | [Equinix AT1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at1/) | n/a | 10G, 100G | Equinix |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | n/a | 10G, 100G | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | n/a | 10G, 100G | Equinix, Megaport, Verizon |
| **New York** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | n/a | 10G, 100G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | USA-beli államigazgatás – Arizona | n/a | AT&T NetBond, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | USA-beli államigazgatás – Texas | n/a | CenturyLink Cloud Connect, Megaport |
| **Szilícium-völgy** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | n/a | 10G, 100G | A következő címen: & T, Equinix, level 3 Communications, Verizon |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | n/a | n/a | Equinix, Megaport |
| **Washington, D.C.** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD – keleti régió, US Gov Virginia | 10G, 100G | AT&T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>Kína
| **Hely** | **Szolgáltatók** |
| --- | --- |
| **Peking** |China Telecom |
| **Beijing2** | Kínai Telekom, China Unicom, GDS |
| **Sanghaj** |China Telecom |
| **Shanghai2** | China Telecom, GDS |

További tudnivalókért lásd: [Az ExpressRoute Kínában](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Németország
| **Hely** | **Szolgáltatók** |
| --- | --- |
| **Berlin** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Kapcsolódás az Exchange-szolgáltatókon keresztül
Ha a kapcsolatszolgáltató nincs felsorolva az előző szakaszokban, akkor is létesíthet kapcsolatot.

* Ellenőrizze a kapcsolatszolgáltatójánál, hogy kapcsolódik-e a fenti táblában felsorolt adatcsere-szolgáltatások bármelyikéhez. A következő hivatkozásokon további információkat találhat az adatcsere-szolgáltatók által kínált szolgáltatásokkal kapcsolatban. Több kapcsolatszolgáltató már eleve kapcsolódik Ethernet-adatcserélőkhöz.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)
  
* Kérje meg a kapcsolatszolgáltatót, hogy terjessze ki a hálózatot a választott társviszony-létesítési helyszínre.
  * Győződjön meg róla, hogy a kapcsolatszolgáltató magas rendelkezésre állással terjesztette ki a kapcsolatot, tehát nincsenek kritikus hibapontok a rendszeren belül.
* Rendeljen meg egy ExpressRoute-kapcsolatcsoportot, ahol az adatcserélő a Microsofthoz kapcsolódó kapcsolatszolgáltató.
  * Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-classic.md) című témakör lépéseit a kapcsolat beállításához.

## <a name="connectivity-through-satellite-operators"></a>Csatlakozás műholdas operátorokon keresztül
Ha távoli, és nem rendelkezik száloptikás kapcsolattal, vagy más csatlakozási lehetőségeket szeretne felfedezni, tekintse meg a következő műhold-operátorokat. 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="c1partners"></a>Kapcsolódás további szolgáltatókon keresztül
| **Hely** | **Exchange** | **Kapcsolódási szolgáltatók** |
| --- | --- | --- |
| **Amszterdam** | Equinix, Interxion, 3. szint kommunikáció | BICS, CloudXpress, Eurofiber, Fastweb S. p. A, Gulf Bridge International, Kalaam Telecom Bahrain B. S. C, MainOne, Nianet, POST Telecom Luxembourg, proximus, RETN, TDC erhverv, Telecom Italia szikrázó, Telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| Crown-kastély
| **Fokváros** | Teraco | MTN |
| **Chicago** | Equinix| Crown Castle, Spectrum Enterprise, windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, Crown Castle, adatgyűjtő, Spectrum Enterprise, Transtelco |
| **Frankfurt** | Interxion | BICS, Cinia, Equinix, Nianet, QSC AG, Telekom Deutschland GmbH |
| **Hamburg** | Equinix | Cinia |
| **Hongkong (KKT)** | Equinix | Chief, Macroview Telecom |
| **Johannesburg** | Teraco | MTN |
| **London** | BICS, Equinix, euNetworks| Bezeq International Ltd., CoreAzure, epszilon Telecom Limited, exponenciális E, HSO, NexGen Networks, proximus, Tamar, az adatszolgáltató |
| **Los Angeles** | Equinix |Crown Castle, Spectrum Enterprise, Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Airgate Technologies, Inc. Aptum Technologies, Rogers, Zirro |
| **New York** |Equinix, Megaport | Altice Business, Crown Castle, Spectrum Enterprise, WEBAIR |
| **Párizs** | Equinix | Proximus |
| **Quebec város** | Megaport | Fibrenoire |
| **Sao Paula** | Equinix | Venha PRA nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Szilícium-völgy** |Coresite, Equinix | Cox Business, Spectrum Enterprise, Windstream, X2nsat Inc. |
| **Szingapúr** |Equinix |1CLOUDSTAR, BICS, CMC Telecom, epszilon távközlés Limited, LGA Telecom, Egyesült információs autópálya (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokió** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Airgate Technologies Inc., Beanfield Metroconnect, Aptum Technologies, IVedha Inc, Rogers, Thinktel, Zirro|
| **Washington, D.C.** |Equinix | Altice Business, BICS, Cox Business, Crown Castle, GTT Communications Inc, epszilon távközlés Limited, Masergy, windstream |

## <a name="expressroute-system-integrators"></a>ExpressRoute-rendszerintegrátorok
A privát kapcsolatok igény szerinti beállítása nehéz feladat lehet, a hálózat méretétől függően. A következő táblában felsorolt rendszerintegrátorok bármelyike segítségére lehet az ExpressRoute üzembe helyezésében.

| **Kontinens** | **Rendszerintegrátorok** |
| --- | --- |
| **Ázsia** |Avanade Inc., OneAs1a |
| **Ausztrália** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Európa** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **Észak-Amerika** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **Dél-Amerika** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>Következő lépések
* További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).
* Ellenőrizze, hogy minden előfeltétel teljesül-e. Lásd: [ExpressRoute-előfeltételek](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Helyek térképe"
