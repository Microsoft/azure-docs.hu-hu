---
title: Mi az Az Azure BareMetal-infrastruktúrája?
description: Áttekintést nyújt az Azure BareMetal-infrastruktúráról.
ms.custom: references_regions
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: c0fd250a63ce93d3f8b62dfe76fe753c928801ce
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536885"
---
#  <a name="what-is-baremetal-infrastructure-on-azure"></a>Mi az Az Azure BareMetal-infrastruktúrája?

Microsoft Azure felhőalapú infrastruktúrát kínál, amely az üzleti igényeknek megfelelő integrált felhőszolgáltatások széles skáláját kínálja. Bizonyos esetekben azonban előfordulhat, hogy operációs rendszer nélküli kiszolgálókon kell szolgáltatásokat futtatnia virtualizálási réteg nélkül. Szükség lehet gyökér szintű hozzáférésre és az operációs rendszer (OS) vezérlésére. Az Azure számos nagy értékű és kritikus fontosságú alkalmazáshoz kínál BareMetal-infrastruktúrát, hogy megfeleljen ennek a nek.

Az BareMetal-infrastruktúra dedikált BareMetal-példányokat (számítási példányokat), nagy teljesítményű és alkalmazásspecifikus tárolókat (NFS, ISCSI és Fiber Channel), valamint függvényspecifikus virtuális LAN-okat (VLAN-okat) tartalmaz egy elkülönített környezetben. A tároló az BareMetal-példányok között megosztható olyan funkciók engedélyezéséhez, mint a horizontális felskálás fürtök, vagy a STONITH segítségével magas rendelkezésre állású párok létrehozása.
 
Ez a környezet speciális VNA-kat is biztosít, amelyekhez akkor férhet hozzá, ha virtuális gépeket futtat egy vagy több Azure-beli virtuális hálózaton (VNeten) az Azure-előfizetésében. A teljes környezet egy erőforráscsoportként van ábrázolva az Azure-előfizetésben.

Az BareMetal-infrastruktúra több mint 30 SKUs-t kínál 2 szoftvercsatornástól 24 szoftvercsatornás kiszolgálókig és 1,5 TB és 24 TB közötti memóriaig. Az Octane memóriával a SKUs nagy készlete is elérhető. Az Azure az operációs rendszer nélküli példányok legnagyobb körét kínálja a nagy kapacitású felhőkben.

## <a name="why-baremetal-infrastructure"></a>Miért érdemes az BareMetal-infrastruktúrát?  

A vállalat egyes központi számítási feladatai olyan technológiákból állnak, amelyek nem jellemzően virtualizált felhőalapú környezetben való futtatásra vannak tervezve. Speciális architektúrára, tanúsított hardverre vagy rendkívül nagy méretre van szükségük. Bár ezek a technológiák a legkifinomultabb adatvédelmi és üzletmenet-folytonossági funkciókkal is rendelkezik, ezek a funkciók nem a virtualizált felhőhöz készültek. Érzékenyebbek a késésre és a zajos szomszédokra, és sokkal nagyobb kontrollt igényelnek a változáskezelés és a karbantartási tevékenységek felett.

Az BareMetal-infrastruktúra egy adott alkalmazáskészlethez készült, minősített és tesztelt. Az Azure volt az első ilyen megoldás, és azóta a legnagyobb portfólióval és a legkifinomultabb rendszerekkel vezeti.

Az BareMetal-infrastruktúra a következő előnyöket kínálja: 

- Dedikált példányok
- Tanúsított hardver speciális számítási feladatokhoz
    - SAP (lásd: [SAP-#1928533](https://launchpad.support.sap.com/#/notes/1928533))
    - Oracle (Lásd: [Oracle-dokumentumazonosító #948372.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=52088246571495&id=948372.1&_adf.ctrl-state=kwnkj1hzm_52))
- Operációs rendszer nélküli (nincs számítási virtualizálás)
- Kis késés az Azure-ban üzemeltetett alkalmazás virtuális gépei és az BareMetal-példányok között (0,35 ms)
- Minden Flash SSD és NVMe
    - Legfeljebb 1 PB/bérlő 
    - IOPS legfeljebb 1,2 millió/bérlő 
    - 40/100 GB hálózati sávszélesség
    - NFS- , ISCSI- és FC-kapcsolaton keresztül érhető el
- Redundáns energiaellátás, tápegységek, hálózati csatlakozók, TOR-ek, portok, WAN-ek, tárolás és felügyelet
- Tartalékok tartalékok meghibásodás esetén (újrakonfigurálás nélkül)
- Ügyfél által koordinált karbantartási időszak
- Alkalmazásokat figyelembeő pillanatképek, archiválás, tükrözés és klónozás


## <a name="baremetal-benefits"></a>BareMetal-előnyök  

Az BareMetal-infrastruktúra olyan kritikus fontosságú számítási feladatokhoz való, amelyek hitelesítést igényelnek a vállalati alkalmazások futtatásához. Az BareMetal-példányokat csak Ön dedikálta, és teljes körű hozzáféréssel rendelkezik az operációs rendszerhez (OS). Az operációs rendszer és az alkalmazás telepítését az igényeinek megfelelően kezelheti. A biztonság érdekében a példányok internetkapcsolat nélkül vannak kiépítve az Azure Virtual Network (VNet) szolgáltatásban. Az BareMetal-példányokkal csak a virtuális gépeken futó szolgáltatások és az azonos 2. rétegbeli hálózaton lévő egyéb Azure-szolgáltatások kommunikálhatnak.  

Az BareMetal-infrastruktúra a következő előnyöket kínálja: 

- Tanúsított hardver speciális számítási feladatokhoz
- SAP (lásd: [SAP-#1928533](https://launchpad.support.sap.com/#/notes/1928533))
- Oracle (Lásd az [Oracle-dokumentumazonosítót #948372.1)](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=52088246571495&id=948372.1&_adf.ctrl-state=kwnkj1hzm_52)
- Nem hipervizált BareMetal-példány, egybérlős tulajdonjog
- Kis késés az Azure-ban üzemeltetett alkalmazás virtuális gépei és az BareMetal-példányok között (0,35 ms)
- Minden Flash SSD- és NVMe-támogatás
- Legfeljebb 1 PB/bérlő adatbázisa 
- IOPS legfeljebb 1,2 millió/bérlő 
- 50 GB hálózati sávszélesség 

## <a name="sku-availability-in-azure-regions"></a>Termékváltozatok rendelkezésre állása Azure-régiókban

Az BareMetal Infrastructure több, specializált számítási feladatokhoz hitelesített termékkel rendelkezik. Igény szerint használhatja a számítási feladatokra vonatkozó termékkódokat.

- Nagy méretű példányok – A kétcsatornás rendszerektől a négycsatornás rendszerekig.  
- Nagyon nagy méretű példányok – A négy foglalattól a húsz szoftvercsatornás rendszerekig. 

A speciális számítási feladatokhoz készült BareMetal-infrastruktúra a következő Azure-régiókban érhető el:
- Nyugat-Európa
- Észak-Európa
- Németország nyugati középső régiója *zónatámogatás
- USA 2. keleti régiója *zónák támogatása
- USA keleti régiója *zónák támogatása
- USA nyugati régiója *zónák támogatása
- AZ USA 2. nyugati régiója *zónatámogatás
- USA déli középső régiója

>[!NOTE]
>**A zónák támogatása** olyan rendelkezésre állási zónákra utal egy régión belül, ahol az BareMetal-példányok üzembe helyezhetők a zónák között a magas rugalmasság és a rendelkezésre állás érdekében. Ez a képesség lehetővé teszi a többhelyű aktív-aktív skálázás támogatását.

## <a name="managing-baremetal-instances-in-azure"></a>BareMetal-példányok kezelése az Azure-ban 

Az igényeitől függően az BareMetal infrastruktúra alkalmazás-topológiái összetettek lehetnek. Több példányt is üzembe helyezhet egy vagy több helyen, megosztott vagy dedikált tárolóval, valamint speciális LAN- és WAN-kapcsolatokkal. Így az BareMetal-infrastruktúra esetében az Azure egy CSA/GBB által a kiépítési portál mezőjében található információk tanácsadói rögzítését kínálja. 

Az Operációs rendszer nélküli infrastruktúra kiépítése után az operációs rendszer, a hálózatok, a tárolókötetek, a zónákban és régiókban való elhelyezések, valamint a helyek közötti WAN-kapcsolatok már előre konfigurálva vannak. Ön úgy van beállítva, hogy regisztrálja az operációsrendszer-licenceket (BYOL), konfigurálja az operációs rendszert, és telepítse az alkalmazásréteget.

Az összes BareMetal-erőforrást, valamint azok állapotát és attribútumát a Azure Portal. A példányokat üzemeltetheti is, és innen nyithatja meg a szolgáltatáskéréseket és a támogatási jegyeket. 

## <a name="operational-model"></a>Működési modell
Az BareMetal infrastruktúra ISO 27001, ISO 27017, SOC 1 és SOC 2 szabványnak megfelelő. Saját licences (BYOL) modellt is használ: operációs rendszert, speciális számítási feladatokat és külső alkalmazásokat.  

Amint megkapja a gyökér szintű hozzáférést és a teljes hozzáférést, ön vállalja a felelősséget a következőért:
- Biztonsági mentési és helyreállítási megoldások, magas rendelkezésre állás és vészhelyreállítás tervezése és megvalósítása.
- Az operációs rendszer és a harmadik féltől származó szoftverek licenceltetése, biztonsága és támogatása.

A Microsoft a következőért felelős:
- Hardver biztosítása speciális számítási feladatokhoz. 
- Az operációs rendszer kiépítése.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-support-model.png" alt-text="Az BareMetal-infrastruktúra támogatási modelljének ábrája." border="false":::

## <a name="baremetal-instance-stamp"></a>BareMetal példánybélyeg

Maga az BareMetal-példánybélyeg a következő összetevőket kombinálja:

- **Számítástechnika:** Az Intel Xeon processzorok generációján alapuló kiszolgálók, amelyek biztosítják a szükséges számítási képességeket, és tanúsítva vannak a speciális számítási feladatokhoz.

- **Hálózat:** Az egységes, nagy sebességű hálózati háló összekapcsolja a számítási, tárolási és LAN-összetevőket.

- **Storage (Tárolás):** Egy egységes hálózati hálón keresztül elért infrastruktúra.

Az BareMetal-bélyeg több-bérlős infrastruktúrája az ügyfeleket elkülönített bérlőkben telepíti. Bérlő üzembe helyezésekor az Azure-előfizetésnek az Azure-regisztráción belül kell nevet ad. Ez az Azure-előfizetés az, amely az BareMetal-példányokért van kiszámlázva.

>[!NOTE]
>Az BareMetal-példányt üzembe helyező ügyfél el van különítve a bérlőben. A bérlők a hálózati, tárolási és számítási rétegben elkülönülnek a többi bérlőtől. A különböző bérlőkhöz rendelt tárolási és számítási egységek nem láthatják egymást, és nem kommunikálhatnak egymással az BareMetal-példányaikon.

## <a name="operating-system"></a>Operációs rendszer
Az BareMetal-példány kiépítése során kiválaszthatja a gépekre telepíteni kívánt operációs rendszert. 

>[!NOTE]
>Ne feledje, hogy az BareMetal-infrastruktúra egy BYOL-modell.

A Linux operációs rendszer elérhető verziói a következőek:
- Red Hat Enterprise Linux (RHEL)
- SUSE Linux Enterprise Server (SLES)

## <a name="storage"></a>Tárolás
Az BareMetal infrastruktúra magas redundáns NFS-tárolót és Fiber Channel-tárolót biztosít. Az infrastruktúra mély integrációt biztosít a vállalati számítási feladatokhoz, például az SAP-hez, az SQL-hez és sok máshoz. Emellett alkalmazás-konzisztens adatvédelmi és adatkezelési képességeket is biztosít. Az önkiszolgáló felügyeleti eszközök területtakarékos pillanatkép-, klónozási és részletes replikációs képességeket, valamint egyablakos monitorozást biztosítanak. Az infrastruktúra nulla RPO- és RTO-képességet biztosít az adatok rendelkezésre állására és az üzletmenet folytonosságára vonatkozó igényekhez. 

A tárolási infrastruktúra a következő lehetőségeket kínálja:
- Legfeljebb 4 x 100 GB-os uplink.
- Legfeljebb 32 GB száloptikacsatornás kapcsolat.
- Minden flash SSD- és NVMe-meghajtó.
- Rendkívül alacsony késés és nagy átviteli sebesség.
- Akár 4 PB nyers tárterületet is skáláz. 
- Legfeljebb 11 millió IOPS.

Ezek az adatelérési protokollok támogatottak: 
- iSCSI 
- NFS (v3 vagy v4) 
- Fiber Channel 
- NVMe FC-n keresztül  

## <a name="networking"></a>Hálózatkezelés
Az Azure hálózati szolgáltatások architektúrája kulcsfontosságú összetevő a speciális számítási feladatok BareMetal-példányban való sikeres üzembe helyezéséhez. Valószínű, hogy még nem minden it-rendszer található az Azure-ban. Az Azure olyan hálózati technológiát kínál, amely lehetővé teszi, hogy az Azure virtuális adatközpontként nézzen ki a helyszíni szoftvertelepítések számára. Az BareMetal-példányokhoz szükséges Azure hálózati funkciók a következőket tartalmazzák:

- A helyszíni hálózati eszközökhöz csatlakozó Azure ExpressRoute azure-beli virtuális hálózatok.
- A helyszínen az Azure-hoz kapcsolatot hozó ExpressRoute-kapcsolat kapcsolatnak legalább 1 Gb/s vagy nagyobb sávszélességgel kell lennie.
- Kiterjesztett Active Directory dns az Azure-ban, vagy teljes mértékben az Azure-ban fut.

Az ExpressRoute lehetővé teszi a helyszíni hálózat kiterjesztését a Microsoft-felhőbe privát kapcsolaton keresztül egy kapcsolatszolgáltató segítségével. Az **ExpressRoute Local** használatával költséghatékonyan továbbíthat adatokat a helyszíni hely és a kívánt Azure-régió között. A geopolitikai határokon keresztüli kapcsolat kiterjesztéséhez engedélyezheti az **ExpressRoute Premiumot.** 

Az BareMetal-példányok az Azure VNet-kiszolgáló IP-címtartományában vannak kiépítve.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" alt-text="Az Azure BareMetal Infrastructure diagramjának architekturális diagramja." lightbox="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" border="false":::

A bemutatott architektúra három szakaszra oszlik:
- **Left (Balra):** Megjeleníti az ügyfél helyi infrastruktúráját, amely különböző alkalmazásokat futtat, és a partneren vagy a helyi peremhálózati útválasztón, például az Equinixen keresztül csatlakozik. További információ: [Kapcsolatszolgáltatók és helyek: Azure ExpressRoute.](../expressroute/expressroute-locations.md)
- **Középre:** Az [Azure Edge-hálózathoz](../expressroute/expressroute-introduction.md) való csatlakozást kínáló Azure-előfizetéssel kiépített ExpressRoute-et jeleníti meg.
- **Jobbra:** Megjeleníti az Azure IaaS-t, és ebben az esetben virtuális gépek használatát az Azure-beli virtuális hálózaton belül kiépített alkalmazásokhoz.
- **Alul:** Az [ExpressRoute FastPath-kapcsolattal engedélyezett ExpressRoute-átjáró](../expressroute/about-fastpath.md) használatát mutatja be az bareMetal kapcsolathoz, amely alacsony késést biztosít.   
   >[!TIP]
   >Ennek támogatásához az ExpressRoute-átjárónak UltraPerformance-nek kell lennie. További információ: [Az ExpressRoute virtuális hálózati átjárói.](../expressroute/expressroute-about-virtual-network-gateways.md)

## <a name="next-steps"></a>Következő lépések

A következő lépés az BareMetal-példányok azonosításának és a velük való interakciónak a Azure Portal.

> [!div class="nextstepaction"]
> [BareMetal-példányok kezelése a Azure Portal](connect-baremetal-infrastructure.md)
