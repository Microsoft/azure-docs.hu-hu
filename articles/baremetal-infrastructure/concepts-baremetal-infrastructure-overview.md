---
title: Az Azure-beli BareMetal-infrastruktúra áttekintése
description: Áttekintést nyújt a BareMetal-infrastruktúráról az Azure-ban.
ms.custom: references_regions
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: bccb171ce364a5129489c437f2f18156cc563a1b
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579054"
---
#  <a name="what-is-baremetal-infrastructure-on-azure"></a>Mi a BareMetal-infrastruktúra az Azure-ban?

A Microsoft Azure felhőalapú infrastruktúrát kínál, és számos integrált felhőalapú szolgáltatást biztosít az üzleti igények kielégítése érdekében. Bizonyos esetekben előfordulhat, hogy az operációs rendszer nélküli kiszolgálókon kell szolgáltatásokat futtatnia virtualizációs réteg nélkül. Szükség lehet a rendszergazdai hozzáférésre és az operációs rendszer (OS) vezérlésére. Ahhoz, hogy kielégítse az igényeket, az Azure számos nagy értékű és kritikus fontosságú alkalmazáshoz biztosít BareMetal-infrastruktúrát.

A BareMetal-infrastruktúra dedikált BareMetal-példányok (számítási példányok), nagy teljesítményű és alkalmazás-megfelelő tárolók (NFS, dNFS, ISCSI és Fibre Channel), valamint egy elkülönített virtuális helyi hálózat (VLAN-ok) készlete. A tárolók megoszthatók a BareMetal-példányok között, így olyan funkciók is engedélyezhetők, mint a kibővíthető fürtök, illetve magas rendelkezésre állású párok létrehozása a STONITH.
 
Ez a környezet speciális VLAN-okat is biztosít, ha virtuális gépeket (VM-ket) futtat egy vagy több Azure-beli virtuális hálózaton (virtuális hálózatok) az Azure-előfizetésében. Az Azure-előfizetésében a teljes környezet erőforráscsoport.

A BareMetal-infrastruktúrát több mint 30 SKU-ban kínáljuk 2 – 24 szoftvercsatorna-kiszolgáló és a memória 1,5 TB-tól 24 TBs-ig terjedő mennyiségig. A nagy mennyiségű SKU-készlet a oktánszámú memóriával is elérhető. Az Azure az operációs rendszer nélküli példányok legnagyobb tartományát kínálja a nagy kapacitású-felhőben.

## <a name="why-baremetal-infrastructure"></a>Miért BareMetal az infrastruktúrát?  

A vállalat egyes központi munkaterhelései olyan technológiákból állnak, amelyek egyszerűen nem tipikus virtualizált Felhőbeli beállításokon futnak. Speciális architektúrát, minősített hardvert vagy rendkívül nagy méreteket igényelnek. Habár ezek a technológiák rendelkeznek a legkifinomultabb adatvédelmi és üzletmenet-folytonossági funkciókkal, ezek a funkciók nem a virtualizált felhőhöz készültek. Érzékenyebbek a késésekre, a zajos szomszédokra, és sokkal nagyobb mértékű irányítást igényelnek a változások kezelésével és a karbantartási tevékenységgel szemben.

Az BareMetal-infrastruktúra kiépítése, minősítése és tesztelése megtörténik az ilyen alkalmazások kiválasztott készlete számára. Az Azure elsőként kínál ilyen megoldásokat, és azóta a legnagyobb portfólióval és a legkifinomultabb rendszerrel rendelkezik.

A BareMetal-infrastruktúra a következő előnyöket kínálja: 

- Dedikált példányok
- Minősített hardver speciális számítási feladatokhoz
    - SAP (lásd [: SAP-megjegyzés #1928533](https://launchpad.support.sap.com/#/notes/1928533))
    - Oracle (tekintse meg az [Oracle-dokumentum azonosítóját #948372.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=52088246571495&id=948372.1&_adf.ctrl-state=kwnkj1hzm_52))
- Operációs rendszer nélküli (nincs számítási virtualizálási)
- Kis késleltetés az Azure által üzemeltetett alkalmazás virtuális gépei között a BareMetal-példányok között (0,35 MS)
- Minden Flash SSD és NVMe
    - Legfeljebb 1 PB/bérlő 
    - Akár 1,2 millió/bérlő IOPS 
    - 40/100 GB-os hálózati sávszélesség
    - Elérhető NFS, dNFS, ISCSI és FC használatával
- Redundáns energiaellátás, tápegységek, hálózati adapterek, adatkezelési szolgáltatások, portok, WAN, Storage és Management
- Gyakori tartalékok a hibák pótlására (nincs szükség az újrakonfigurálásra)
- Ügyfél által koordinált karbantartási időszakok
- Alkalmazás-felismerő Pillanatképek, archiválás, tükrözés és klónozás


## <a name="sku-availability-in-azure-regions"></a>SKU elérhetősége az Azure-régiókban

A BareMetal-infrastruktúra több SKU-t biztosít a speciális munkaterhelésekhez. Az igényeinek megfelelő munkaterhelés-specifikus SKU-ket használhatja.

- Nagyméretű példányok – a két szoftvercsatorna négy szoftvercsatorna-rendszerig terjed.  
- Nagyon nagy példányok – a négy szoftvercsatorna és a húsz szoftvercsatorna közötti rendszertől kezdve. 

A speciális számítási feladatokhoz a BareMetal-infrastruktúra a következő Azure-régiókban érhető el:
- Nyugat-Európa
- Észak-Európa
- Középnyugat-Németország * zónák támogatása
- Az USA 2. keleti régiója – támogatott zónák
- Az USA keleti régiója * által támogatott zónák
- Az USA nyugati régiójában * támogatott zónák
- USA 2. nyugati régiója – támogatott zónák
- USA déli középső régiója

>[!NOTE]
>A **zónák támogatása** olyan régión belüli rendelkezésre állási zónákra vonatkozik, ahol a BareMetal-példányok a nagy rugalmasság és rendelkezésre állás érdekében a zónák között helyezhetők üzembe. Ez a funkció lehetővé teszi a többhelyes aktív-aktív skálázás támogatását.

## <a name="managing-baremetal-instances-in-azure"></a>BareMetal-példányok kezelése az Azure-ban 

Az igényektől függően a BareMetal-infrastruktúra alkalmazás-topológiái összetettek lehetnek. Több példányt is üzembe helyezhet egy vagy több helyen, megosztott vagy dedikált tárolóval, valamint speciális LAN-és WAN-kapcsolatokkal. A BareMetal-infrastruktúra esetében az Azure a kiépítési portálon található mezőben lévő CSA/GBB alapján nyújtja be az információk konzultatív rögzítését. 

A BareMetal-infrastruktúra kiépítésekor az operációs rendszer, a hálózatok, a tárolási kötetek, az elhelyezések a zónákban és a régiókban, valamint a helyszínek közötti WAN-kapcsolatok már előre konfigurálva vannak. Az operációsrendszer-licencek (BYOL) regisztrálására, az operációs rendszer konfigurálására és az alkalmazás rétegének telepítésére van beállítva.

Megtekintheti az összes BareMetal infrastruktúra-erőforrást, valamint azok állapotát és attribútumait a Azure Portalban. A példányokat és a szolgáltatási kérelmeket és a támogatási jegyeket is üzemeltetheti. 

## <a name="operational-model"></a>Működési modell
A BareMetal-infrastruktúra az ISO 27001, az ISO 27017, a SOC 1 és a SOC 2 szabványnak felel meg. Emellett a saját licencű (BYOL) modellt is használja: operációs rendszer, speciális munkaterhelés és külső alkalmazások.  

A root Access és a teljes hozzáférés megszerzését követően a következőkért kell felelősséget vállalnia:
- Biztonsági mentési és helyreállítási megoldások, magas rendelkezésre állás és vész-helyreállítás tervezése és megvalósítása.
- Az operációs rendszer és a harmadik féltől származó szoftverek licencelése, biztonsága és támogatása.

A Microsoft felelős a következőkért:
- A hardver speciális számítási feladatokhoz való biztosítása. 
- Az operációs rendszer üzembe helyezése.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-support-model.png" alt-text="A BareMetal-infrastruktúra támogatási modelljének ábrája." border="false":::

## <a name="baremetal-instance-stamp"></a>BareMetal-példány bélyegzője

A BareMetal-példány bélyegzője maga ötvözi a következő összetevőket:

- **Számítástechnika:** A kiszolgálók a szükséges számítástechnikai képességet biztosító Intel Xeon processzorok generációján alapulnak, és a speciális számítási feladatokhoz vannak hitelesítve.

- **Hálózat:** Az egységes, nagy sebességű hálózati hálók összekapcsolódnak a számítástechnikai, tárolási és LAN-összetevőkkel.

- **Tárterület:** Egy egységes hálózati hálón keresztül elért infrastruktúra.

A BareMetal Stamp több-bérlős infrastruktúráján belül az ügyfeleket elkülönített bérlők helyezik üzembe. Bérlő telepítésekor egy Azure-előfizetést kell megkeresnie az Azure-regisztráción belül. Ez az Azure-előfizetés a BareMetal-példányok számlája.

>[!NOTE]
>Egy BareMetal-példányt telepítő ügyfél elkülönített egy bérlőn. A bérlő a többi bérlő hálózati, tárolási és számítási rétegében el van különítve. A különböző bérlők számára hozzárendelt tárolási és számítási egységek nem látják egymást, és nem tudnak kommunikálni egymással a BareMetal példányain.

## <a name="operating-system"></a>Operációs rendszer
A BareMetal-példány kiépítés során kiválaszthatja a gépekre telepíteni kívánt operációs rendszert. 

>[!NOTE]
>Ne feledje, hogy a BareMetal-infrastruktúra egy BYOL-modell.

Az elérhető linuxos operációsrendszer-verziók a következők:
- Red Hat Enterprise Linux (RHEL)
- SUSE Linux Enterprise Server (SLES)

## <a name="storage"></a>Tárolás
A BareMetal-infrastruktúra nagyon redundáns NFS-tárolót és Fibre Channel-tárolót biztosít. Az infrastruktúra mély integrációt biztosít a vállalati számítási feladatokhoz, például az SAP, az SQL és más rendszerekhez. Emellett az alkalmazással konzisztens adatvédelmet és adatkezelési képességeket is biztosít. Az önkiszolgáló felügyeleti eszközök tárterület-hatékony pillanatképet, klónozást és szemcsés replikációs képességeket biztosítanak, valamint egyetlen ablaktáblán az üvegek figyelését. Az infrastruktúra lehetővé teszi a nulla RPO és a RTO képességet az adatelérhetőség és az üzletmenet folytonosságának kielégítéséhez. 

A tárolási infrastruktúra a következőket kínálja:
- Akár 4 x 100-GB-os kapcsolat.
- Akár 32 GB-os Fibre Channel-kapcsolat.
- Minden Flash SSD és NVMe meghajtó.
- Rendkívül alacsony késés és magas átviteli sebesség.
- Legfeljebb 4 PB nyers tárterületet méretezhető. 
- Akár 11 000 000 IOPS.

Ezek az adatelérési protokollok támogatottak: 
- iSCSI 
- NFS (v3 vagy v4) 
- Fibre Channel 
- NVMe az FC-en keresztül  

## <a name="networking"></a>Hálózatkezelés
Az Azure hálózati szolgáltatások architektúrája kulcsfontosságú összetevő a speciális számítási feladatok BareMetal-példányokban való sikeres üzembe helyezéséhez. Valószínű, hogy az összes IT-rendszer nem az Azure-ban található. Az Azure olyan hálózati technológiát kínál, amellyel az Azure úgy néz ki, mint egy virtuális adatközpont a helyszíni szoftverek központi telepítéséhez. Az BareMetal-példányokhoz szükséges Azure-hálózati funkciók a következők:

- Az Azure ExpressRoute áramkörhöz csatlakozó Azure-beli virtuális hálózatok, amelyek a helyszíni hálózati eszközökhöz csatlakoznak.
- A helyszíni és az Azure-hoz csatlakozó ExpressRoute-áramkörnek legalább 1 GB/s vagy annál nagyobb sávszélességgel kell rendelkeznie.
- Kiterjesztett Active Directory és DNS az Azure-ban, vagy teljesen fut az Azure-ban.

A ExpressRoute lehetővé teszi a helyszíni hálózat kibővítését a Microsoft-felhőbe a kapcsolati szolgáltató súgójában található privát kapcsolaton keresztül. A helyszíni hely és a kívánt Azure-régió között költséghatékony adatátvitelhez használhatja a **ExpressRoute helyi** környezetét. A különböző geopolitikai határokon átívelő kapcsolatok kibővítéséhez engedélyezheti a **ExpressRoute Premiumot**. 

Az BareMetal-példányok az Azure VNet-kiszolgáló IP-címtartomány keretében vannak kiépítve.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" alt-text="Az Azure BareMetal infrastruktúra diagramjának építészeti diagramja." lightbox="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" border="false":::

A bemutatott architektúra három szakaszra oszlik:
- **Balra:** Megjeleníti az ügyfél helyszíni infrastruktúráját, amely különböző alkalmazásokat futtat, és a partner vagy a helyi peremhálózati útválasztón keresztül csatlakozik, például Equinix. További információ [: kapcsolati szolgáltatók és helyszínek: Azure ExpressRoute](../expressroute/expressroute-locations.md).
- **Központ:** Az Azure-előfizetéssel az Azure Edge Network szolgáltatással létesített kapcsolattal kiépített [ExpressRoute](../expressroute/expressroute-introduction.md) jeleníti meg.
- **Jobb:** Az Azure IaaS jeleníti meg, és ebben az esetben a virtuális gépek használatával üzemeltetheti az alkalmazásokat, amelyek az Azure Virtual Networkben vannak kiépítve.
- **Alul:** Azt mutatja, hogy az ExpressRoute-átjárót a [ExpressRoute FastPath](../expressroute/about-fastpath.md) használatával engedélyezte a BareMetal-kapcsolat alacsony késéssel.   
   >[!TIP]
   >Ennek támogatásához a ExpressRoute-átjárónak UltraPerformance kell lennie. További információ: [a ExpressRoute virtuális hálózati átjárók ismertetése](../expressroute/expressroute-about-virtual-network-gateways.md).

## <a name="next-steps"></a>Következő lépések

A következő lépés a BareMetal-példányok azonosításának és kezelésének megismerése a Azure Portalon keresztül.

> [!div class="nextstepaction"]
> [BareMetal-példányok kezelése a Azure Portal](connect-baremetal-infrastructure.md)
