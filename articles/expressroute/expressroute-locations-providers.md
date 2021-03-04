---
title: 'Helyek és kapcsolatszolgáltatók: Azure ExpressRoute | Microsoft Docs'
description: A cikk részletes áttekintést nyújt a szolgáltatási helyekről és az Azure-régiókhoz való csatlakozásról. Hely alapján rendezve.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/10/2021
ms.author: duau
ms.openlocfilehash: c4a50645ae1e7b412c5c98bafbd7746c7c435d85
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040753"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute-partnerek és társviszony-létesítési helyszínek.

> [!div class="op_single_selector"]
> * [Telephelyek szolgáltató szerint](expressroute-locations.md)
> * [Szolgáltatók hely szerint](expressroute-locations-providers.md)


A cikkben szereplő táblázatok a földrajzi lefedettséggel és a ExpressRoute, a ExpressRoute és a ExpressRoute rendszerintegrátorokkal (SIs) kapcsolatos információkat tartalmaznak.

> [!Note]
> Az Azure-régiók és a ExpressRoute két különböző fogalmat alkotnak, és a kettő közötti különbség kritikus fontosságú az Azure Hybrid Networking-kapcsolat megismeréséhez. 
>
>

## <a name="azure-regions"></a>Azure-régiók
Az Azure-régiók olyan globális adatközpontok, ahol az Azure számítási, hálózati és tárolási erőforrásai találhatók. Az Azure-erőforrások létrehozásakor az ügyfélnek ki kell választania egy erőforrás-helyet. Az erőforrás helye határozza meg, hogy az erőforrás melyik Azure-adatközpontban (vagy rendelkezésre állási zónában) jön létre.

## <a name="expressroute-locations"></a>ExpressRoute-helyek
A ExpressRoute helyek (más néven a társítási helyek vagy a saját helyek) olyan közös elhelyezési létesítmények, ahol a Microsoft Enterprise Edge (MSEE) eszközei találhatók. A ExpressRoute helye a Microsoft hálózatának belépési pontja – globálisan elosztott, így az ügyfelek a világ különböző pontjain csatlakozhatnak a Microsoft hálózatához. Ezek a helyszínek, ahol a ExpressRoute-partnerek és a ExpressRoute Direct-ügyfelek a Microsoft hálózatához csatlakoznak. Általánosságban elmondható, hogy a ExpressRoute helye nem kell, hogy egyezzen az Azure-régióval. Például egy ügyfél létrehozhat egy ExpressRoute-áramkört az *USA keleti* régiójában, a *Seattle* -beli összevonási helyen.

Ha legalább egy ExpressRoute-helyhez csatlakozott egy geopolitikai régióban, az adott geopolitikai régióban lévő összes régióban hozzáférhet az Azure-szolgáltatásokhoz. 

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Azure-régiók egy geopolitikai régión belüli ExpressRoute
A következő tábla az egyes geopolitikai régiókban lévő Azure-régiók és ExpressRoute-helyek térképét mutatja.

| **Geopolitikai régió** | **Azure-régiók** | **ExpressRoute-helyek** |
| --- | --- | --- |
| **Ausztrália kormányzati** | Ausztrália középső régiója, Ausztrália 2. középső régiója |Canberra, Canberra2 |
| **Európa** | Közép-Franciaország, Dél-Franciaország, Észak-Németország, Középnyugat-Németország, Észak-Európa, Kelet-Norvégia, Norvégia nyugati régiója, Észak-Svájc, Nyugat-Svájc, Egyesült Királyság nyugati régiója, Egyesült Királyság déli régiója, Nyugat-Európa |Amszterdam, Amsterdam2, Berlin, Koppenhága, Dublin, Frankfurt, Genf, London, London2, Marseille, Milánó, München, Newport (Wales), Oslo, Párizs, Stavanger, Stockholm, Zürich |
| **Észak-Amerika** | USA keleti régiója, USA nyugati régiója, USA 2. keleti régiója, USA 2. nyugati régiója, USA középső régiója, USA déli középső régiója, USA északi középső régiója, USA középnyugati régiója, Közép-Kanada, Kelet-Kanada |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Los Angeles2, Miami, Minneapolis, Montreal, New York, Phoenix, Québec város, Queretaro (Mexikó), Quincy, San Antonio, Seattle, Szilícium-völgy, szilícium-Valley2, Toronto, Vancouver, Washington, Washington DC2 |
| **Ázsia** | Kelet-Ázsia, Délkelet-Ázsia | Bangkok, Hongkong, Hong Kong2, Jakarta, Kuala Lumpur, Szingapúr, Szingapúr2, Taipei |
| **India** | Nyugat-India, Közép-India, Dél-India |Csennai, Csennai2, Mumbai, Mumbai2 |
| **Japán** | Nyugat-Japán, Kelet-Japán |Osaka, Tokió, Tokyo2 |
| **Óceánia** | Délkelet-Ausztrália, Kelet-Ausztrália |Auckland, Melbourne, Perth, Sydney, Sydney2 | 
| **Dél-Korea** | Dél-Korea középső régiója, Korea déli régiója |Busan, Szöul|
| **EAE** | UAE Central, Észak-Egyesült Arab | Dubai, Dubai2 |
| **Dél-afrikai Köztársaság** | Dél-Afrika nyugati régiója, Dél-Afrika északi régiója |Fokváros, Johannesburg |
| **Dél-Amerika** | Dél-Brazília |Bogota, Sao Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Az Azure-régiók és az országos felhők geopolitikai határai
Az alábbi tábla a régiók és az országos felhők geopolitikai határainak információit tartalmazza.

| **Geopolitikai régió** | **Azure-régiók** | **ExpressRoute-helyek** |
| --- | --- | --- |
| **Az Egyesült Államok kormányának felhője** |US Gov Arizona, US Gov Iowa, US Gov Texas, US Gov Virginia, US DoD – középső régió, US DoD – keleti régió  |Atlanta, Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Szilícium-völgy, Washington, D.C. |
| **Kelet-Kína** |Kelet-Kína, Kelet-Kína2 |Shanghai, Shanghai2 |
| **Észak-Kína** |Észak-Kína, Észak-Kína2 |Peking, Beijing2 |
| **Németország** |Közép-Németország, Kelet-Németország |Berlin, Frankfurt |

A geopolitikai régiókon átívelő kapcsolódás nem támogatott a standard ExpressRoute-termékváltozatokban. A globális kapcsolatok támogatásához engedélyeznie kell az ExpressRoute prémium bővítményt. Az országos felhőkörnyezetekhez való kapcsolódás nem támogatott. Igény esetén tájékozódjon kapcsolatszolgáltatójánál a lehetőségekről.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>ExpressRoute-kapcsolatszolgáltatók

Az alábbi táblázatban a kapcsolati helyek és az egyes helyekhez tartozó szolgáltatók láthatók. A szolgáltatók és a szolgáltatási helyek megtekintéséhez lásd: [Helyek szolgáltatók szerint](expressroute-locations.md).

* A **helyi Azure-régiók** olyanok, amelyek az egyes [ExpressRoute helyi](expressroute-faqs.md) helyeken is hozzáférhetnek. az **n/a** azt jelzi, hogy a ExpressRoute helyi helye nem érhető el az adott egyenrangú helyen.

* A **zóna** a [díjszabásra](https://azure.microsoft.com/pricing/details/expressroute/)vonatkozik.


### <a name="global-commercial-azure"></a>Globális kereskedelmi Azure
| **Hely** | **Cím** | **Zóna** | **Helyi Azure-régiók** | **Közvetlen ER** | **Szolgáltatók** |
| --- | --- | --- | --- | --- | --- |
| **Amszterdam** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | Nyugat-Európa | 10G, 100G | Aryaka Networks hálózatok,&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, felhő, Interxion, KPN, IX REACH, 3. szintű kommunikáció, Megaport, NTT Communications, Orange, Tata Communications, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amszterdam2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | Nyugat-Európa | 10G, 100G | British Telecom, CenturyLink Cloud-kapcsolat, Colt, DE-CIX, Equinix, euNetworks, GÉANT, Interxion, NOS, NTT globális adatközpontok EMEA, Orange, Vodafone |
| **Atlanta** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | n.a. | 10G, 100G | Equinix, Megaport |
| **Auckland** | [Vocus csoport, NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | n.a. | 10G | Devoli, Kordia, Megaport, REANNZ, Spark NZ, Vocus csoport NZ |
| **Bangkok** | [AIS](https://business.ais.co.th/solution/en/azure-expressroute.html) | 2 | n.a. | 10G | AIS, UIH |
| **Berlin** | [NTT GDC](https://www.e-shelter.de/en/location/berlin-1-data-center) | 1 | Észak-Németország | 10G | Equinix, NTT globális adatközpontok EMEA|
| **Bogota** | [Equinix BG1](https://www.equinix.com/locations/americas-colocation/colombia-colocation/bogota-data-centers/bg1/) | 4 | n.a. | 10G | Equinix |
| **Busan** | [LG CNS](https://www.lgcns.com/En/Service/DataCenter) | 2 | Dél-Korea déli régiója | n.a. | LG CNS |
| **Canberra** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Ausztrália középső régiója | 10G, 100G | CDC |
| **Canberra2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Ausztrália 2. középső régiója| 10G, 100G | CDC, Equinix |
| **Fokváros** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | Dél-Afrika nyugati régiója | 10G | BCX, Internet Solutions – Cloud-kapcsolat, Liquid Telecom, Teraco |
| **Csennai** | Tata Communications | 2 | Dél-India | 10G | Globális CloudXchange (GCX), SIFY, Tata Communications, VodafoneIdea |
| **Csennai2** | Airtel | 2 | Dél-India | 10G | Airtel |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | USA északi középső régiója | 10G, 100G | Aryaka Networks Networks,&T NetBond, British Telecom, CenturyLink Cloud-kapcsolat, Cologix, Colt, Comcast, Coresite, Equinix, felhő, Internet2, level 3 Communications, Megaport, PacketFabric, PCCW globális korlátozott, sprint, Telia Carrier, Verizon, Zayo |
| **Koppenhága** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | n.a. | 10G | Interxion |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | n.a. | 10G, 100G | Aryaka Networks hálózatok,&T NetBond, Cologix, Equinix, Internet2, level 3 Communications, Megaport, Neutrona Networks, Telmex uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | USA nyugati középső régiója | n.a. | CoreSite, Megaport, Zayo |
| **Dubai** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | Észak-Egyesült Arab | n.a. | Etisalat UAE |
| **Dubai2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | Észak-Egyesült Arab | n.a. | DE-CIX, du datamena, Equinix, Megaport, Orange, Orixcom |
| **Dublin** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | Észak-Európa | 10G, 100G | CenturyLink Cloud-kapcsolat, Colt, EIR, Equinix, GEANT, euNetworks, Interxion, Megaport |
| **Frankfurt** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | Középnyugat-Németország | 10G, 100G | &T NetBond, CenturyLink Cloud kapcsolatba, Colt, DE-CIX, Equinix, euNetworks, GEANT, Cloud, Interxion, Megaport, Orange, Telia Carrier, T-Systems |
| **Frankfurt2** | [Equinix FR7](https://www.equinix.com/locations/europe-colocation/germany-colocation/frankfurt-data-centers/fr7/) | 1 | Középnyugat-Németország | 10G, 100G | Equinix |
| **Genfi** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | Nyugat-Svájc | 10G, 100G | Equinix, Megaport, Swisscom |
| **Hongkong** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | Kelet-Ázsia | 10G | Aryaka Networks Networks, British Telecom, CenturyLink Cloud, a Telecom, a kínai távközlési globális, a kínai Unicom, a Equinix, a felhő, a Megaport, az NTT Communications, a narancssárga, a PCCW globális korlátozott, Tata Communications, Telia Carrier, Verizon |
| **Hong Kong2** | [MEGA-i](https://www.iadvantage.net/index.php/locations/mega-i) | 2 | Kelet-Ázsia | 10G | China Mobile International, kínai távközlési globális, Megaport, PCCW, globálisan korlátozott, énekel |
| **Dzsakarta** | Telin, Telkom Indonézia | 4 | n.a. | 10G | Telin |
| **Johannesburg** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | Dél-Afrika északi régiója | 10G | BCX, British Telecom, Internet Solutions – Cloud-kapcsolat, Liquid Telecom, Orange, Teraco |
| **Kuala Lumpur** | [Az idő dotCom Menara célja](https://www.time.com.my/enterprise/connectivity/direct-cloud) | 2 | n.a. | n.a. | TIME dotCom |
| **Las Vegas** | [LV kapcsoló](https://www.switch.com/las-vegas) | 1 | n.a. | 10G, 100G | CenturyLink Cloud-kapcsolat, Megaport, PacketFabric |
| **London** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | Az Egyesült Királyság déli régiója | 10G, 100G | &T NetBond, British Telecom, Colt, Equinix, euNetworks, Cloud, Internet Solutions – Cloud-kapcsolat, Interxion, Jisc, level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW globális korlátozott, Tata Communications, KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | [Ház, Észak-két](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | Az Egyesült Királyság déli régiója | 10G, 100G | British Telecom, CenturyLink Cloud-kapcsolat, Colt, GTT, IX REACH, Equinix, Megaport, SES, Sohonet,-KDDI |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | n.a. | 10G, 100G | CoreSite, Equinix, Megaport, Neutrona hálózatok, NTT, Zayo |
| **Los Angeles2** | [Equinix LA1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/los-angeles-data-centers/la1/) | 1 | n.a. | 10G, 100G | Equinix |
| **Madrid** | [Interxion MAD1](https://www.interxion.com/es/donde-estamos/europa/madrid) | 1 | Nyugat-Európa | 10G, 100G | |
| **Marseille** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | Dél-Franciaország | n.a. | DE-CIX, GEANT, Interxion, Jaguar Network, Ooredoo Cloud-kapcsolat |
| **Melbourne** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | Délkelet-Ausztrália | 10G, 100G | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | n.a. | 10G, 100G | Claro, C3ntro, Equinix, Megaport, Neutrona hálózatok |
| **Milánó** | [IRIDEOS](https://irideos.it/en/data-centers/) | 1 | n.a. | 10G | Colt, Equinix, Fastweb, IRIDEOS, Retelit |
| **Minneapolis** | [Cologix MIN1](https://www.cologix.com/data-centers/minneapolis/min1/) | 1 | n.a. | 10G, 100G | Cologix, Megaport |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | n.a. | 10G, 100G | Bell Canada, Cologix, Fibrenoire, Megaport, Telus, Zayo |
| **Mumbai** | Tata Communications | 2 | Nyugat-India | 10G | DE-CIX, globális CloudXchange (GCX), bizalom jio, Sify, Tata Communications, Verizon |
| **Mumbai2** | Airtel | 2 | Nyugat-India | 10G | Airtel, Sify, Vodafone Idea |
| **München** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/munich/) | 1 | n.a. | 10G | DE-CIX |
| **New York** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | n.a. | 10G, 100G | CenturyLink Cloud-kapcsolat, Colt, Coresite, DE-CIX, Equinix, felhő, Megaport, csomag, Zayo |
| **Newport (Wales)** | [Következő generációs adatok](https://www.nextgenerationdata.co.uk) | 1 | Az Egyesült Királyság nyugati régiója | n.a. | British Telecom, Colt, Jisc, 3. szintű kommunikáció, következő generációs adatforgalom |
| **Oszaka** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | Nyugat-Japán | 10G, 100G | Tokió, Colt, Equinix, Internet Initiative Japan Inc. – IIJ, Megaport, NTT Communications, NTT SmartConnect, Softbank, Tokai Communications |
| **Oslo** | [DigiPlex Ulven](https://www.digiplex.com/locations/oslo-datacentre) | 1 | Kelet-Norvégia | 10G, 100G | GlobalConnect, Megaport, Telenor, Telia Carrier |
| **Párizs** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1 | Közép-Franciaország | 10G, 100G | British Telecom, CenturyLink Cloud-kapcsolat, Colt, Equinix, Cloud, Interxion, Jaguar Network, Megaport, Orange, Telia Carrier, Zayo |
| **Perth** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | n.a. | 10G | Megaport, NextDC |
| **Phoenix** | [EdgeConneX PHX01](https://www.edgeconnex.com/locations/north-america/phoenix-az/) | 1 | n.a. | 10G, 100G | |
| **Quebec város** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | Kelet-Kanada | 10G, 100G | Bell Canada, Megaport, Telus |
| **Queretaro (Mexikó)** | [KIO hálózatok QR01](https://www.kionetworks.com/es-mx/) | 4 | n.a. | 10G | Transtelco|
| **Quincy** | [Sabey Datacenter – A](https://sabeydatacenters.com/data-center-locations/central-washington-data-centers/quincy-data-center) | 1 | USA 2. nyugati régiója | 10G, 100G | |
| **Río de Janeiro** | [Equinix – RJ2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/rio-de-janeiro-data-centers/rj2/) | 3 | Dél-Brazília | 10G | Equinix |
| **San Antonio** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | USA déli középső régiója | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | Dél-Brazília | 10G, 100G | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | USA 2. nyugati régiója | 10G, 100G | Aryaka Networks Networks, Equinix, level 3 Communications, Megaport, Telus, Zayo |
| **Szöul** | [KINX Gasan IDC](https://www.kinx.net/?lang=en) | 2 | Dél-Korea középső régiója | 10G, 100G | KINX, KT, LG CNS, Equinix, Sejong Telecom |
| **Szilícium-völgy** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | USA nyugati régiója | 10G, 100G | Aryaka Networks hálózatok,&T NetBond, British Telecom, CenturyLink Cloud-kapcsolat, Colt, Comcast, Coresite, Equinix, felhő, Internet2, IX REACH, csomag, PacketFabric, level 3 Communications, Megaport, Orange, sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Szilícium Valley2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | USA nyugati régiója | 10G, 100G | Colt, Coresite | 
| **Szingapúr** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | Délkelet-Ázsia | 10G, 100G | Aryaka Networks Networks,&T NetBond, British Telecom, China Mobile International, epszilon globális kommunikáció, Equinix, felhő, level 3 Communications, Megaport, NTT Communications, Orange, énekel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapore2** | [Globális kapcsoló, Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | Délkelet-Ázsia | 10G, 100G | Kínai Unicom Global, Colt, epszilon, globális kommunikáció, Megaport, PCCW globális korlátozott, énekel, a KDDI |
| **Stavanger** | [Zöld hegyi DC1](https://greenmountain.no/dc1-stavanger/) | 1 | Norvégia nyugati régiója | 10G, 100G |GlobalConnect, Megaport |
| **Stockholm** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | n.a. | 10G | Equinix, Telia Carrier |
| **Sydney** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | Kelet-Ausztrália | 10G, 100G | AARNet,&T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus csoport NZ |
| **Sydney2** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | Kelet-Ausztrália | 10G, 100G | Megaport, NextDC |
| **Taipei** | Telekommunikációs vezető | 2 | n.a. | 10G | Telekommunikációs vezető, Chunghwa Telecom, FarEasTone |
| **Tokió** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | Kelet-Japán | 10G, 100G | Aryaka Networks Networks,&T NetBond, BBIX, British Telecom, CenturyLink Cloud csatlakozik, Colt, Equinix, Internet Initiative Japan Inc.-IIJ, Megaport, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Tokyo2** | [TOKIÓBAN](https://www.attokyo.com/) | 2 | Kelet-Japán | 10G, 100G | Tokió, Megaport, Tokai Communications |
| **Toronto** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | Közép-Kanada | 10G, 100G | &T NetBond, Bell Canada, CenturyLink Cloud kapcsolatba, Cologix, Equinix, IX REACH Megaport, Telus, Verizon, Zayo |
| **Toronto2** | [Szövetséges REIT](https://www.alliedreit.com/property/905-king-st-w/) | 1 | Közép-Kanada | 10G, 100G | |
| **Vancouver** | [Cologix VAN1](https://www.cologix.com/data-centers/vancouver/van1/) | 1 | n.a. | 10G | Cologix, Megaport, Telus |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | USA keleti régiója, USA 2. keleti régiója | 10G, 100G | Aryaka Networks Networks, AT&T NetBond, British Telecom, CenturyLink Cloud-kapcsolat, Cologix, Colt, Comcast, Coresite, Equinix, Internet2, felhő, Iron Mountain, IX elérés, level 3 Communications, Megaport, Neutrona Networks, NTT Communications, Orange, PacketFabric, SES, sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Washington DC2** | [Coresite Restonban](https://www.coresite.com/data-center-locations/northern-virginia-washington-dc) | 1 | USA keleti régiója, USA 2. keleti régiója | 10G, 100G | CenturyLink Cloud-kapcsolat, Coresite, Intelsat, Megaport, Viasat, Zayo | 
| **Zürich** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | Észak-Svájc | 10G, 100G | Equinix, felhőalapú, Interxion, Megaport, Swisscom |

 **+** a hamarosan megjelenő megjegyzések

### <a name="national-cloud-environments"></a>Országos felhőkörnyezetek

Az Azure nemzeti felhők el vannak különítve egymástól és a globális kereskedelmi Azure-tól. Az egyik Azure-beli felhőhöz tartozó ExpressRoute nem csatlakozhatnak a többi Azure-régióhoz.

### <a name="us-government-cloud"></a>Az Egyesült Államok kormányának felhője
| **Hely** | **Cím** | **Helyi Azure-régiók**| **Közvetlen ER** | **Szolgáltatók** |
| --- | --- | --- | --- | --- |
| **Atlanta** | [Equinix AT1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at1/) | n.a. | 10G, 100G | Equinix |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | n.a. | 10G, 100G | &T NetBond, British Telecom, Equinix, level 3 Communications, Verizon |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | n.a. | 10G, 100G | Equinix, Megaport, Verizon |
| **New York** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | n.a. | 10G, 100G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | USA-beli államigazgatás – Arizona | 10G, 100G | &T NetBond, CenturyLink Cloud-kapcsolat, Megaport |
| **San Antonio** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | USA-beli államigazgatás – Texas | n.a. | CenturyLink Cloud Connect, Megaport |
| **Szilícium-völgy** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | n.a. | 10G, 100G | A következő címen:&T, Equinix, level 3 Communications, Verizon |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | n.a. | 10G, 100G | Equinix, Megaport |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD – keleti régió, US Gov Virginia | 10G, 100G | &T NetBond, CenturyLink Cloud-kapcsolat, Equinix, level 3 Communications, Megaport, Verizon |

### <a name="china"></a>Kína
| **Hely** | **Szolgáltatók** |
| --- | --- |
| **Peking** |China Telecom |
| **Beijing2** | Kínai Telekom, China Unicom, GDS |
| **Sanghaj** |China Telecom |
| **Shanghai2** | Kínai Telekom, China Unicom, GDS |

További tudnivalókért lásd: [Az ExpressRoute Kínában](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Németország
| **Hely** | **Szolgáltatók** |
| --- | --- |
| **Berlin** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="connectivity-through-exchange-providers"></a><a name="c1partners"></a>Kapcsolódás az Exchange-szolgáltatókon keresztül
Ha a kapcsolatszolgáltató nincs felsorolva az előző szakaszokban, akkor is létesíthet kapcsolatot.

* Ellenőrizze a kapcsolatszolgáltatójánál, hogy kapcsolódik-e a fenti táblában felsorolt adatcsere-szolgáltatások bármelyikéhez. A következő hivatkozásokon további információkat találhat az adatcsere-szolgáltatók által kínált szolgáltatásokkal kapcsolatban. Több kapcsolatszolgáltató már eleve kapcsolódik Ethernet-adatcserélőkhöz.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange)
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

## <a name="connectivity-through-additional-service-providers"></a><a name="c1partners"></a>Kapcsolódás további szolgáltatókon keresztül
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
| **Montreal** | Cologix| Airgate Technologies, Inc. Aptum Technologies, Rogers, Zirro |
| **New York** |Equinix, Megaport | Altice Business, Crown Castle, Spectrum Enterprise, WEBAIR |
| **Párizs** | Equinix | Proximus |
| **Quebec város** | Megaport | Fibrenoire |
| **Sao Paulo** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Szilícium-völgy** |Coresite, Equinix | Cox Business, Spectrum Enterprise, windstream, X2nsat Inc. |
| **Szingapúr** |Equinix |1CLOUDSTAR, BICS, CMC Telecom, epszilon távközlés Limited, LGA Telecom, Egyesült információs autópálya (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokió** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Airgate Technologies Inc., Beanfield Metroconnect, Aptum Technologies, IVedha Inc, Oncore Cloud Services Inc., Rogers, Thinktel, Zirro|
| **Washington DC** |Equinix | Altice Business, BICS, Cox Business, Crown Castle, GTT Communications Inc, epszilon távközlés Limited, Masergy, windstream |

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
* A ExpressRoute kapcsolatos további információkért tekintse meg a [ExpressRoute gyakori kérdések](expressroute-faqs.md)című témakört.
* Ellenőrizze, hogy minden előfeltétel teljesül-e. Lásd: [ExpressRoute-előfeltételek](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Hely térképe"
