---
title: 'Kapcsolatszolgáltatók és helyek: Azure ExpressRoute | Microsoft Docs'
description: A cikk részletes áttekintést nyújt a szolgáltatási helyekről és az Azure-régiókhoz való csatlakozásról. Kapcsolatszolgáltatók szerint rendezve.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/10/2021
ms.author: duau
ms.openlocfilehash: 741fd7e4554ffe07de08e96233c440210eefa2fa
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498407"
---
# <a name="expressroute-connectivity-partners-and-peering-locations"></a>ExpressRoute-kapcsolati partnerek és társviszony-létesítési helyszínek

> [!div class="op_single_selector"]
> * [Helyek szolgáltató szerint](expressroute-locations.md)
> * [Szolgáltatók hely szerint](expressroute-locations-providers.md)


A cikkben található táblázatok az ExpressRoute földrajzi lefedettségéről és helyéről, az ExpressRoute-kapcsolatszolgáltatókról és az ExpressRoute-rendszerintegrátorokról (SI-kről) nyújtanak információt.

> [!Note]
> Az Azure-régiók és az ExpressRoute-helyek két különböző fogalomból állnak, a kettő közötti különbség megértése kritikus fontosságú az Azure hibrid hálózati kapcsolatának feltárása szempontjából. 
>
>

## <a name="azure-regions"></a>Azure-régiók
Az Azure-régiók globális adatközpontok, ahol az Azure számítási, hálózati és tárolási erőforrásai találhatók. Azure-erőforrás létrehozásakor az ügyfélnek ki kell választania egy erőforrás helyét. Az erőforrás helye határozza meg, hogy melyik Azure-adatközpontban (vagy rendelkezésre állási zónában) jön létre az erőforrás.

## <a name="expressroute-locations"></a>ExpressRoute-helyek
Az ExpressRoute-helyek (más néven társviszony-létesítés vagy találkozóhely) közös elhelyezésű létesítmények, ahol Microsoft Enterprise Edge- (MSEE-) eszközök találhatók. Az ExpressRoute-helyek a Belépési pont a Microsoft hálózatához – és globálisan elosztottak, így az ügyfelek világszerte csatlakozhatnak a Microsoft hálózatához. Ezeken a helyeken az ExpressRoute-partnerek és a ExpressRoute Direct a Microsoft hálózatához való keresztkapcsolatokat hoznak létre. Általánosságban elmondható, hogy az ExpressRoute-helynek nem kell egyeznie az Azure-régióval. Egy ügyfél például létrehozhat egy ExpressRoute-kapcsolatcsoportot az USA keleti régiója erőforrás-helyről Seattle *társviszony-létesítés* helyén. 

Ha legalább egy ExpressRoute-helyhez csatlakozott egy geopolitikai régióban, az adott geopolitikai régióban lévő összes régióban hozzáférhet az Azure-szolgáltatásokhoz.

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Az egyes geopolitikai régiókban lévő Azure-régiók és ExpressRoute-helyek.
A következő tábla az egyes geopolitikai régiókban lévő Azure-régiók és ExpressRoute-helyek térképét mutatja.

| **Geopolitikai régió** | **Azure-régiók** | **ExpressRoute-helyek** |
| --- | --- | --- |
| **Ausztrália kormányzati** |Ausztrália középső régiója, Ausztrália 2. középső régiója |Canberra, Canberra2 |
| **Európa** | Közép-Franciaország, Dél-Franciaország, Észak-Németország, Nyugat-Németország, Észak-Európa, Kelet-Németország, Nyugat-Afrika, Észak-Svájc, Nyugat-Svájc, Egyesült Királyság nyugati régiója, Egyesült Királyság déli régiója, Nyugat-Európa |Amszterdam, Amszterdam2, Amszterdam, Amszterdam, Dublin, Dublin,Cal2, Paris, London, London2, Valamint, Dublin, Majd, Valamint, Newport (Wales), Oslo, Párizs, Stavanger,Cal, Zugl |
| **Észak-Amerika** |USA keleti régiója, USA nyugati régiója, USA 2. keleti régiója, USA 2. nyugati régiója, USA középső régiója, USA déli középső régiója, USA északi középső régiója, USA középnyugati régiója, Közép-Kanada, Kelet-Kanada |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Los Angeles2, Miami, Minneapolis, Montréal, New York, Phoenix, Québec város, Queretaro (Mexikó), Quincy, San Antonio, Seattle, Szilícium-völgy, Szilícium-völgy2, Toronto, Toronto2, Seattle, Washington, D.C., Washington, D.C. |
| **Ázsia** | Kelet-Ázsia, Délkelet-Ázsia | Hongkong, Hongkong, Hongkong2, Jakarta, Kuala Lumpur, Szingapúr, Szingapúr2, Taipei |
| **India** | Nyugat-India, Közép-India, Dél-India |Csennai, Csennai2, Mumbai, Mumbai2 |
| **Japán** | Nyugat-Japán, Kelet-Japán |Oszaka, Tokió, Tokió2 |
| **Óceánia** | Délkelet-Ausztrália, Kelet-Ausztrália |Auckland, Melbourne, Perth, Sydney, Sydney2 |
| **Dél-Korea** | Dél-Korea középső régiója, Korea déli régiója |Busan, Szöul|
| **EAE** | Egyesült Arab Emírségek középső, Egyesült Arab Emírségek északi iránya | Február, 2. |
| **Dél-afrikai Köztársaság** | Dél-Afrika nyugati régiója, Dél-Afrika északi régiója |Fokváros, Johannesburg |
| **Dél-Amerika** | Dél-Brazília |Moota, Rio de Janeiro, Sao Paulo |


## <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>A régiók és az országos felhők geopolitikai határai
Az alábbi tábla a régiók és az országos felhők geopolitikai határainak információit tartalmazza.

| **Geopolitikai régió** | **Azure-régiók** | **ExpressRoute-helyek** |
| --- | --- | --- |
| **Az Egyesült Államok kormányának felhője** |US Gov Arizona, US Gov Iowa, US Gov Texas, US Gov Virginia, US DoD – középső régió, US DoD – keleti régió  |Atlanta, Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Szilícium-völgy, Washington, D.C. |
| **Kelet-Kína** |Kelet-Kína, Kelet-Kína2 |Hajhaj, Shajhaj2 |
| **Észak-Kína** |Észak-Kína, Észak-Kína2 |Foga, 2. város |
| **Németország** |Közép-Németország, Kelet-Németország |Berlin, Frankfurt |

A geopolitikai régiókon átívelő kapcsolódás nem támogatott a standard ExpressRoute-termékváltozatokban. A globális kapcsolatok támogatásához engedélyeznie kell az ExpressRoute prémium bővítményt. Az országos felhőkörnyezetekhez való kapcsolódás nem támogatott. Igény esetén tájékozódjon kapcsolatszolgáltatójánál a lehetőségekről.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>ExpressRoute-kapcsolatszolgáltatók

Az alábbi táblázat a szolgáltatók szerint jeleníti meg a helyeket. Ha az elérhető szolgáltatókat hely szerint kívánja megtekinteni, tekintse meg a következőt: [Szolgáltatók hely szerint](expressroute-locations-providers.md).


### <a name="global-commercial-azure"></a>Globális kereskedelmi Azure

| **Szolgáltató** | **Microsoft Azure** | **Microsoft 365**  | **Helyek** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/connectivity-services/azure-expressroute)** |Támogatott |Támogatott |Melbourne, Sydney |
| **[Airtel](https://www.airtel.in/business/#/)** | Támogatott | Támogatott | Csennai2, Mumbai2 |
| **[Ais](https://business.ais.co.th/solution/en/azure-expressroute.html)** | Támogatott | Támogatott | Bangkok |
| **[Aryaka Networks](https://www.aryaka.com/)** |Támogatott |Támogatott |Amszterdam, Chicago, Dallas, Hongkong KKT, Sao Paulo, Seattle, Szilícium-völgy, Szingapúr, Tokió, Washington, D.C. |
| **[Ascenty Data Centers](https://www.ascenty.com/en/cloud/microsoft-express-route)** |Támogatott |Támogatott |Sao Paulo |
| **[AT&amp;T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Támogatott |Támogatott |Amszterdam, Chicago, Dallas, Tokió, London, Szilícium-völgy, Szingapúr, Sydney, Tokió, Toronto, Washington, D.C. |
| **[TOKIÓBAN](https://www.attokyo.com/connectivity/azure.html)** | Támogatott | Támogatott | Oszaka, Tokió2 |
| **[PHOENIXIX (UTÓTAG)](https://www.bbix.net/en/service/ix/)** | Támogatott | Támogatott | Tokió |
| **[BCX](https://www.bcx.co.za/solutions/connectivity/data-networks)** |Támogatott |Támogatott |Fokváros, Johannesburg|
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Támogatott |Támogatott |Montréal, Toronto, Québec város |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/cloud-connect-azure)** |Támogatott |Támogatott |Amszterdam, Amszterdam2, Chicago, HongkongI KKT, Percesburg, London, London2, Newport (Wales), Párizs, Sao Paulo, Szilícium-völgy, Szingapúr, Sydney, Tokió, Washington, D.C. |
| **[Bsnl](https://www.bsnl.co.in/opencms/bsnl/BSNL/services/enterprises/cloudway.html)** |Támogatott |Támogatott |Csennai, Mumbai |
| **[C3ntro](https://www.c3ntro.com/data1/express-route1.php)** |Támogatott |Támogatott |Miami |
| **CDC** | Támogatott | Támogatott | Canberra, Canberra2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Támogatott |Támogatott |Amszterdam2, Chicago, Dublin, Dublin, Hongkong, Las Vegas, London2, New York, Párizs, San Antonio, Szilícium-völgy, Tokió, Toronto, Washington, D.C., Washington, D.C. |
| **[Távközlési igazgató](https://www.chief.com.tw/)** |Támogatott |Támogatott |Hongkong, Taipei |
| **China Mobile International** |Támogatott |Támogatott | Hongkong, Hongkong2, Szingapúr |
| **China Telecom Global** |Támogatott |Támogatott |Hongkong, Hongkong2 |
| **China Unicom Global** |Támogatott |Támogatott | Hongkong, Szingapúr2 |
| **[Hamarosan Telecom](https://www.cht.com.tw/en/home/cht/about-cht/products-and-services/International/Cloud-Service)** |Támogatott |Támogatott |Taipei |
| **[Claro](https://www.usclaro.com/enterprise-mnc/connectivity/mpls/)** |Támogatott |Támogatott |Miami |
| **[Cologix](https://www.cologix.com/hyperscale/microsoft-azure/)** |Támogatott |Támogatott |Chicago, Dallas, Minneapolis, Montréal, Toronto, Chicago, Washington, D.C. |
| **[Colt](https://www.colt.net/direct-connect/azure/)** |Támogatott |Támogatott |Amszterdam, Amszterdam2, Dublin, Chicago, Dublin, Dublin, Hongkong, London, London2, Innen, Newport, New York, Oszaka, Párizs, Szilícium-völgy, Szilícium-völgy2, Szingapúr2, Tokió, Washington, D.C. |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Támogatott |Támogatott |Chicago, Szilícium-völgy, Washington, D.C. |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Támogatott |Támogatott |Chicago, Denver, Los Angeles, New York, Szilícium-völgy, Szilícium-völgy2, Washington, D.C., Washington, D.C2 |
| **[DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange/find-a-cloud-service/detail/microsoft-azure)** | Támogatott |Támogatott |Amszterdam2, Majd2, Majd, Perc, Mumbai, Mumbai, London, New York |
| **[Devoli](https://devoli.com/expressroute)** | Támogatott |Támogatott | Auckland, Melbourne, Sydney |
| **du datamena** |Támogatott |Támogatott | 2. év |
| **eir** |Támogatott |Támogatott |Dublin|
| **[Epsilon Global Communications](https://www.epsilontel.com/solutions/direct-cloud-connect)** |Támogatott |Támogatott |Szingapúr, Szingapúr2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Támogatott |Támogatott |Amszterdam, Amszterdam2, Atlanta, Berlin, Falota, Canberra2, Chicago, Dallas,Cal2, Dublin, Dublin, Tajvan2, Genova, Hongkong KKT, London2, Los Angeles, Los Angeles2, Melbourne, Miami, Miami, Párizs, New York, Oszaka, Párizs, Rio de Janeiro, Sao Paulo, Seattle, Seoul, Szilícium-völgy, Szingapúr, Sydney, Tokió, Toronto, Washington, D.C. |
| **Etisalat UAE** |Támogatott |Támogatott |Dubai|
| **[euNetworks](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |Támogatott |Támogatott |Amszterdam, Amszterdam2, Dublin, Dublin, London |
| **[FarEasTone (FarEasTone)](https://www.fetnet.net/corporate/en/Enterprise.html)** |Támogatott |Támogatott |Taipei|
| **[Fastweb](https://www.fastweb.it/grandi-aziende/cloud/scheda-prodotto/fastcloud-interconnect/)** | Támogatott |Támogatott |Milánó|
| **[Fibrenoire](https://fibrenoire.ca/en/services/cloudextn-2/)** |Támogatott |Támogatott |Montreal|
| **[GÉANT](https://www.geant.org/Networks)** |Támogatott |Támogatott |Amszterdam, Amszterdam2, Dublin, Valamint |
| **[GlobalConnect]()** | Támogatott |Támogatott | Oslo, Stavanger | 
| **Gtt** |Támogatott |Támogatott |London2 |
| **[Global Cloud Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Támogatott| Támogatott | Csennai, Mumbai |
| **[iAdvantage](https://www.scx.sunevision.com/)** | Támogatott | Támogatott | Hongkong2 |
| **Intelsat** | Támogatott | Támogatott | Washington DC2 |
| **[InterCloud](https://www.intercloud.com/)** |Támogatott |Támogatott |Amszterdam, Chicago, Tajvan, Hongkong, London, New York, Párizs, Szilícium-völgy, Szingapúr, Washington, D.C., Zu ltd. |
| **[Internet2](https://internet2.edu/services/cloud-connect/#service-cloud-connect)** |Támogatott |Támogatott |Chicago, Dallas, Szilícium-völgy, Washington, D.C. |
| **[Internet Initiative Japan Inc. - IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Támogatott |Támogatott |Oszaka, Tokió |
| **[Internet Solutions – Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Támogatott |Támogatott |Fokváros, Johannesburg, London |
| **[Interxion (Interxion)](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Támogatott |Támogatott |Amszterdam, Amszterdam2, Amszterdam, Dublin, Dublin, London, Párizs,Cal, Párizs, Zugl |
| **[IRIDEOS](https://irideos.it/)** |Támogatott |Támogatott |Milánó |
| **Iron Mountain** | Támogatott |Támogatott |Washington DC |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Támogatott |Támogatott | Amszterdam, London2, Szilícium-völgy, Toronto, Washington, D.C. |
| **Jaguar Network** |Támogatott |Támogatott |Foga, Párizs |
| **[Jisc](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |Támogatott |Támogatott |London, Newport (Wales) |
| **[KINX](https://www.kinx.net/service/cloudhub/ms-expressroute/?lang=en)** |Támogatott |Támogatott |Szöul |
| **[Kordia](https://www.kordia.co.nz/cloudconnect)** | Támogatott |Támogatott |Auckland, Sydney |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Támogatott | Támogatott | Amszterdam |
| **[Kt](https://cloud.kt.com/)** | Támogatott | Támogatott | Szöul |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Támogatott |Támogatott |Amszterdam, Chicago, Dallas, London, Newport (Wales), Sao Paulo, Seattle, Szilícium-völgy, Szingapúr, Washington, D.C. |
| **LG CNS** |Támogatott |Támogatott |Busan, Szöul |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Támogatott |Támogatott |Fokváros, Johannesburg |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Támogatott |Támogatott |Amszterdam, Atlanta, Auckland, Chennai, Chicago, Dallas, Denver, Denver2, Dublin, Érdes, Hongkong, Hongkong, Hongkong2, Las Vegas, London, London2, Los Angeles, Melbourne, Miami, Minneapolis, Montréal, New York, Oszaka, Oslo, Párizs, Perth, Qubec város, San Antonio, Seattle, Szilícium-völgy, Szingapúr, Szingapúr2, Stavanger, Sydney, Sydney2, Tokió, Tokió2 Toronto, Washington, Washington, Washington, D.C. |
| **[Mtn](https://www.mtnbusiness.co.za/en/Cloud-Solutions/Pages/microsoft-express-route.aspx)** |Támogatott |Támogatott |London |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |Támogatott |Támogatott |Dallas, Los Angeles, Miami, Sao Paulo, Washington, D.C. |
| **[Következő generációs adatok](https://vantage-dc-cardiff.co.uk/)** |Támogatott |Támogatott |Newport (Wales) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Támogatott |Támogatott |Melbourne, Perth, Sydney, Sydney2 |
| **[MI](https://www.nos.pt/empresas/corporate/cloud/cloud/Pages/nos-cloud-connect.aspx)** |Támogatott |Támogatott |Amszterdam2 |
| **[NTT Communications](https://www.ntt.com/en/services/network/virtual-private-network.html)** |Támogatott |Támogatott |Amszterdam, Hongkong KKT, London, Los Angeles, Oszaka, Szingapúr, Sydney, Tokió, Washington, D.C. |
| **[NTT EAST](https://business.ntt-east.co.jp/service/crossconnect/)** |Támogatott |Támogatott |Tokió |
| **[NTT Global DataCenters EMEA](https://hello.global.ntt/)** |Támogatott |Támogatott |Amszterdam2, Amszterdam |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |Támogatott |Támogatott |Oszaka |
| **[Ooredoo Cloud Connect](https://www.ooredoo.qa/portal/OoredooQatar/cloud-connect-expressroute)** |Támogatott |Támogatott |Marseille |
| **[Optus](https://www.optus.com.au/enterprise/)** |Támogatott |Támogatott |Melbourne, Sydney |
| **[Narancssárga](https://www.orange-business.com/en/products/business-vpn-galerie)** |Támogatott |Támogatott |Amszterdam, Amszterdam2, Majd2, Hongkong, Hongkong KKT,Öte-burk, London, Párizs, Sao Paulo, Szilícium-völgy, Szingapúr, Sydney, Tokió, Washington, D.C. |
| **[Orixcom](https://www.orixcom.com/cloud-solutions/)** | Támogatott | Támogatott | 2. év |
| **[PacketFabric (Csomagfabric)](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)** |Támogatott |Támogatott |Chicago, Dallas, Las Vegas, Szilícium-völgy, Washington, D.C. |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |Támogatott |Támogatott |Chicago, Hongkong, Hongkong2, London, Szingapúr2 |
| **[REANNZ](https://www.reannz.co.nz/products-and-services/cloud-connect/)** | Támogatott | Támogatott | Auckland |
| **[Újratelit](https://www.retelit.it/EN/Home.aspx)** | Támogatott | Támogatott | Milánó | 
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Támogatott |Támogatott |Szöul |
| **[Ses](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)** | Támogatott |Támogatott | London2, Washington, D.C. |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Támogatott |Támogatott |Csennai, Mumbai2 |
| **[SingTel](https://www.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Támogatott |Támogatott |Hongkong2, Szingapúr, Szingapúr2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Támogatott |Támogatott |Oszaka, Tokió |
| **[Sohonet](https://www.sohonet.com/fastlane/)** |Támogatott |Támogatott |London2 |
| **[Spark NZ](https://www.sparkdigital.co.nz/solutions/connectivity/cloud-connect/)** |Támogatott |Támogatott |Auckland, Sydney |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |Támogatott |Támogatott |Chicago, Szilícium-völgy, Washington, D.C. |
| **[Swisscom](https://www.swisscom.ch/en/business/enterprise/offer/cloud-data-center/microsoft-cloud-services/microsoft-azure-von-swisscom.html)** | Támogatott | Támogatott | Genova, Zugl |
| **[Tata Communications](https://www.tatacommunications.com/solutions/network/cloud-ready-networks/)** |Támogatott |Támogatott |Amszterdam, Csennai, Hongkong, KKT, London, Mumbai, Sao Paulo, Szilícium-völgy, Szingapúr, Washington, D.C. |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Támogatott |Támogatott |Amszterdam, Sao Paulo |
| **[Telehouse – KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |Támogatott |Támogatott |London, London2, Szingapúr2 |
| **Telenor** |Támogatott |Támogatott |Amszterdam, London, Oslo |
| **[Telia Carrier](https://www.teliacarrier.com/)** | Támogatott | Támogatott |Amszterdam, Chicago, Dallas, Tajvan, Hongkong, London, Oslo, Párizs, Szilícium-völgy, London, Washington, D.C. |
| **[Telin](https://www.telin.net/product/data-connectivity/telin-cloud-exchange)** | Támogatott | Támogatott |Jakarta |
| **Telmex Uninet**| Támogatott | Támogatott | Dallas |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Támogatott |Támogatott |Melbourne, Sydney, Szingapúr |
| **[Telus](https://www.telus.com)** |Támogatott |Támogatott |Montréal, Seattle, Québec város, Toronto, Seattle |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Támogatott |Támogatott |Fokváros, Johannesburg |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/direct-cloud)** | Támogatott | Támogatott | Kuala Lumpur |
| **[Tokai Communications](https://www.tokai-com.co.jp/en/)** | Támogatott | Támogatott | Oszaka, Tokió2 |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |Támogatott |Támogatott |Dallas, Queretaro (Mexikó)|
| **T-Systems** |Támogatott |Támogatott |Frankfurt|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Támogatott |Támogatott |Sao Paulo |
| **[UIH](https://www.uih.co.th/en/network-solutions/global-network/cloud-direct-for-microsoft-azure-expressroute)** | Támogatott | Támogatott | Bangkok |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Támogatott |Támogatott |Amszterdam, Chicago, Dallas, Hongkong KKT, London, Mumbai, Szilícium-völgy, Szingapúr, Sydney, Tokió, Toronto, Washington, D.C. |
| **[Viasat](http://www.directcloud.viasatbusiness.com/)** | Támogatott | Támogatott | Washington DC2 |
| **[Vocus Group NZ](https://www.vocus.co.nz/business/cloud-data-centres)** | Támogatott | Támogatott | Auckland, Sydney |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Támogatott |Támogatott |Amszterdam2, London, Szingapúr |
| **[Vodafone Idea](https://www.vodafone.in/business/enterprise-solutions/connectivity/vpn-extended-connect)** | Támogatott | Támogatott | Mumbai2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Támogatott |Támogatott |Amszterdam, Chicago, Dallas, Denver, London, Los Angeles, Montréal, New York, Párizs, Seattle, Szilícium-völgy, Toronto, Washington, D.C. |

 **+** azt jelzi, hogy hamarosan érkezik

### <a name="national-cloud-environment"></a>Országos felhőkörnyezet

Az országos Azure-felhők el vannak különítve egymástól és a globális kereskedelmi Azure-tól. Az egyik Azure-felhőhöz elérhető ExpressRoute nem tud csatlakozni a többi Azure-régióhoz. 

### <a name="us-government-cloud"></a>Az Egyesült Államok kormányának felhője

| **Szolgáltató** | **Microsoft Azure** | **Office 365** | **Helyek** |
| --- | --- | --- | --- |
| **[AT&amp;T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Támogatott |Támogatott |Chicago, Phoenix, Szilícium-völgy, Washington, D.C. |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Támogatott |Támogatott |New York, Phoenix, San Antonio, Washington, D.C. |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Támogatott |Támogatott |Atlanta, Chicago, Dallas, New York, Seattle, Szilícium-völgy, Washington, D.C. |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Támogatott |Támogatott |Chicago, Szilícium-völgy, Washington, D.C. |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Támogatott | Támogatott | Chicago, Dallas, San Antonio, Seattle, Washington, D.C. |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Támogatott |Támogatott |Chicago, Dallas, New York, Szilícium-völgy, Washington, D.C. |

### <a name="china"></a>Kína

| **Szolgáltató** | **Microsoft Azure** | **Office 365** | **Helyek** |
| --- | --- | --- | --- |
| **China Telecom** |Támogatott |Nem támogatott |Misa, 2., Shajhaj, 2. |
| **China Unicom** | Támogatott | Nem támogatott | 2. év, 2. július |
| **[Gds](http://www.gds-services.com/en/about_2.html)** |Támogatott |Nem támogatott |2. év, 2. július |

További tudnivalókért lásd: [Az ExpressRoute Kínában.](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>Németország

| **Szolgáltató** | **Microsoft Azure** | **Office 365** | **Helyek** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/direct-connect/azure/)** |Támogatott |Nem támogatott |Frankfurt |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Támogatott |Nem támogatott |Frankfurt |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroute)** |Támogatott |Nem támogatott |Berlin |
| **Interxion** |Támogatott |Nem támogatott |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Támogatott  | Nem támogatott | Berlin |
| **T-Systems** |Támogatott |Nem támogatott |Berlin |

## <a name="connectivity-through-exchange-providers"></a>Kapcsolódás Exchange-szolgáltatókon keresztül

Ha a kapcsolatszolgáltató nincs felsorolva az előző szakaszokban, akkor is létesíthet kapcsolatot.

* Ellenőrizze a kapcsolatszolgáltatójánál, hogy kapcsolódik-e a fenti táblában felsorolt adatcsere-szolgáltatások bármelyikéhez. A következő hivatkozásokon további információkat találhat az adatcsere-szolgáltatók által kínált szolgáltatásokkal kapcsolatban. Több kapcsolatszolgáltató már eleve kapcsolódik Ethernet-adatcserélőkhöz.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure) 
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)

* Kérje meg a kapcsolatszolgáltatót, hogy terjessze ki a hálózatot a választott társviszony-létesítési helyszínre.
  * Győződjön meg róla, hogy a kapcsolatszolgáltató magas rendelkezésre állással terjesztette ki a kapcsolatot, tehát nincsenek kritikus hibapontok a rendszeren belül.
* Rendeljen meg egy ExpressRoute-kapcsolatcsoportot, ahol az adatcserélő a Microsofthoz kapcsolódó kapcsolatszolgáltató.
  * Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-classic.md) című témakör lépéseit a kapcsolat beállításához.

## <a name="connectivity-through-satellite-operators"></a>Kapcsolat műholdas operátorokkal
Ha távoli, és nem rendelkezik száloptikás kapcsolattal, vagy más kapcsolati lehetőségeket szeretne felfedezni, tekintse meg a következő műholdas operátorokat. 

* Intelsat
* [Ses](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat (Viasat)](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a>Kapcsolódás további szolgáltatókon keresztül

| **Kapcsolatszolgáltató** | **Exchange** | **Helyek** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** | Equinix |Szingapúr |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/)** | Equinix, Cologix | Toronto, Montreal |
| **[Alaska Communications](https://www.alaskacommunications.com/Business)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |New York, Washington, D.C. |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Tokió |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |Dallas|
| **[Beanfield Automatikusan csatlakozik](https://www.beanfield.com/business/cloud-exchange)** |Megaport |Toronto|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | London |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Amszterdam, Amszterdam, London, Szingapúr, Washington, D.C. |
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Tokió |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix, Megaport | Dallas |
| **[Chief](https://www.chief.com.tw/)** | Equinix | Hongkong (KKT) |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Frankfurt, Hamburg |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Amszterdam | 
| **[CMC Telecom](https://cmctelecom.vn/san-pham/value-added-service-and-it/cmc-telecom-cloud-express-en/)** | Equinix | Szingapúr | 
| **[Aptum-technológiák](https://aptum.com/services/cloud/managed-azure/)**| Equinix | Montréal, Toronto |
| **[CoreAzure](https://www.coreazure.com/)**| Equinix | London |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | Dallas, Szilícium-völgy, Washington, D.C. |
| **[Fogares](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | Atlanta, Chicago, Dallas, Los Angeles, New York, Washington, D.C. |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/solutions/cloud-connect/)** | Equinix | London, Szingapúr, Washington, D.C. |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amszterdam |
| **[Exponential E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | London |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amszterdam |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Quebec város |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | Washington DC |
| **[Gulf Bridge International](https://gbiinc.com/)** | Equinix | Amszterdam |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | London, Slough |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **[Kaalam Telecom Bahrain B.S.C](http://www.kalaam-telecom.com/azure/)**| Level 3 Communications |Amszterdam |
| **LGA Telecom** |Equinix |Szingapúr|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |Hongkong (KKT) 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Amszterdam |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[Mtn](https://www.mtnbusiness.co.za/en/Cloud-Solutions/Pages/microsoft-express-route.aspx)** | Teraco | Fokváros, Johannesburg |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | London |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Equinix | Amsterdam, Frankfurt |
| **[Oncore Cloud Service Inc](https://www.oncore.cloud/services/ue-for-expressroute)**| Equinix | Toronto |
| **[POST Telecom Luxembourg](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Amszterdam |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Amszterdam, Dublin, London, Párizs |
| **[QSC AG](https://www2.qbeyond.de/en/)** |Interxion | Frankfurt |  
| **[RETN](https://retn.net/services/cloud-connect/)** | Equinix | Amszterdam |
| **[Tata Teleservices](https://www.tatateleservices.com/business-services/data-services/secure-cloud-connect)** | Tata Communications | Csennai, Mumbai |
| **Rogers** | Cologix, Equinix | Montréal, Toronto |
| **[Spectrum Enterprise](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | Chicago, Dallas, Los Angeles, New York, Szilícium-völgy | 
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Equinix | London | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | Amszterdam | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Amszterdam |
| **[Telekom Deutschland GmbH](https://cloud.telekom.de/de/infrastruktur/managed-it-services/managed-hybrid-infrastructure-mit-microsoft-azure)** | Interxion | Amsterdam, Frankfurt |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Amszterdam |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Szingapúr |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | Sao Paulo |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | New York |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Szilícium-völgy, Washington, D.C. |
| **[X2nsat Inc.](https://www.x2nsat.com/expressroute/)** |Coresite (Magwebhely) |Szilícium-völgy, Szilícium-völgy 2|
| **Zain** |Equinix |London|
| **[Zertia](https://www.zertia.es)**| 3-as szint | Madrid |
| **[Zirro](https://zirro.com/services/)**| Cologix, Equinix | Montréal, Toronto |

## <a name="connectivity-through-datacenter-providers"></a>Kapcsolódás adatközpont-szolgáltatókon keresztül

| **Szolgáltató** | **Exchange** |
| --- | --- |
| **[CyrusOne](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport, PacketFabric |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport, PacketFabric |
| **[Adatbank](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[DataFoundry](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | IX Reach, Megaport PacketFabric |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport, PacketFabric |
| **[Rugalmas](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | IX Reach, Megaport, PacketFabric |
| **[QTS-adatközpontok](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Megaport, PacketFabric |
| **[Adatközpontok streamelése]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[RagingWire Data Centers](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach, Megaport, PacketFabric |
| **[vXchnge](https://www.vxchnge.com/colocation-services/interconnection)** | IX Reach, Megaport |
| **[T5 Data Centers](https://t5datacenters.com/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Kapcsolódás országos kutatási és oktatási hálózatokon (NREN) keresztül

| **Szolgáltató**|
| --- |
| **AARNET**| 
| **DeIC, a GÉANT-on keresztül**|
| **GARR, a GÉANT-on keresztül**|
| **GÉANT**|
| **HEAnet, a GÉANT-on keresztül**|
| **Internet2**|
| **JISC**|
| **RedIRIS, a GÉANT-on keresztül**|
| **SINET**|
| **Surfnet, a GÉANT-on keresztül**|

* Ha a kapcsolatszolgáltatója nem szerepel itt, ellenőrizze, hogy kapcsolódik-e a fent felsorolt ExpressRoute adatcsere-partnerek bármelyikéhez.

## <a name="expressroute-system-integrators"></a>ExpressRoute-rendszerintegrátorok
A privát kapcsolatok igény szerinti beállítása nehéz feladat lehet, a hálózat méretétől függően. A következő táblában felsorolt rendszerintegrátorok bármelyike segítségére lehet az ExpressRoute üzembe helyezésében.

| **Rendszerintegrátor** | **Kontinens** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | Európa |
| **[Avanade Inc.](https://www.avanade.com/)** | Ázsia, Európa, Észak-Amerika, Dél-Amerika |
| **[Bright Skies GmbH](https://bskies.io/expressroute)** | Európa
| **[Ensyst](https://www.ensyst.com.au)** | Ázsia
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | Észak-Amerika |
| **[FlexManage](https://www.flexmanage.com/cloud)** | Észak-Amerika |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | Észak-Amerika |
| **[The IT Consultancy Group](https://itconsult.com.au/)** | Ausztrália |
| **[MOQdigital](https://www.moqdigital.com/insights)** | Ausztrália |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Európa (Németország) |
| **[Nelite](https://www.exakis-nelite.com/offres/)** | Európa |
| **[Új aláírás](https://newsignature.com/technologies/express-route/)** | Európa |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | Ázsia |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Európa |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Észak-Amerika |
| **[Presidio](https://www.presidio.com/subpage/1107/microsoft-azure)** | Észak-Amerika |
| **[sol-tec](https://www.sol-tec.com/what-we-do/)** | Európa |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Dél-Amerika |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Ausztrália |

## <a name="next-steps"></a>Következő lépések
* További információ az ExpressRoute-ról: [ExpressRoute – gyakori kérdések.](expressroute-faqs.md)
* Ellenőrizze, hogy minden előfeltétel teljesül-e. Lásd: [ExpressRoute-előfeltételek](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Helytérkép"
