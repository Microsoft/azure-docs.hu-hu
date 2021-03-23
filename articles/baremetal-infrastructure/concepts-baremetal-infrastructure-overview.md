---
title: A BareMetal-infrastruktúra előzetes verziójának áttekintése az Azure-ban
description: A BareMetal-infrastruktúra áttekintése az Azure-ban.
ms.custom: references_regions
ms.topic: conceptual
ms.subservice: workloads
ms.date: 1/4/2021
ms.openlocfilehash: 94cee52eccd8cc9e9631b47bdf84892d763d86d3
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104776217"
---
#  <a name="what-is-baremetal-infrastructure-preview-on-azure"></a>Mi a BareMetal-infrastruktúra előzetes verziója az Azure-on?

Az Azure BareMetal-infrastruktúra biztonságos megoldást kínál a vállalati egyéni munkaterhelések áttelepítésére. Az BareMetal-példányok nem megosztott gazdagépek vagy kiszolgálói hardverek. Feloldja a helyszíni megoldás portolása speciális számítási feladatokkal, amelyeken hitelesített hardver-, licencelési és támogatási szerződések szükségesek. Az Azure kezeli az infrastruktúra figyelését és karbantartását, a vendég operációs rendszer (OS) figyelése és az alkalmazások monitorozása pedig a saját tulajdonában van.

A BareMetal-infrastruktúra lehetővé teszi az infrastruktúra-környezet korszerűsítését a meglévő beruházások és architektúra fenntartása mellett. A BareMetal-infrastruktúrával speciális számítási feladatok helyezhetők üzembe az Azure-ban, így alacsony késéssel érheti el és integrálhatja az Azure-szolgáltatásokat.

## <a name="sku-availability-in-azure-regions"></a>SKU elérhetősége az Azure-régiókban
A BareMetal-infrastruktúra specializált és általános célú számítási feladatokhoz érhető el, amely négy, 4,2 (Rev 4,2) bélyegzőn alapuló régióval kezdődik:
- Nyugat-Európa
- Észak-Európa
- USA 2. keleti régiója
- USA déli középső régiója

>[!NOTE]
>A **rev 4,2** a legújabb, a meglévő Rev 4 architektúrát használó BareMetal-infrastruktúra.  A Rev 4 Az Azure-beli virtuális gépek (VM) gazdagépei számára biztosít szorosabb közelséget. Az Azure-beli virtuális gépek és a BareMetal-példányok közötti hálózati késés jelentős javulása a Rev 4-bélyegekben vagy-sorokban van üzembe helyezve.  A BareMetal-példányok a Azure Portalon keresztül érhetők el és kezelhetők. 

## <a name="support"></a>Támogatás
A BareMetal-infrastruktúra az ISO 27001, az ISO 27017, a SOC 1 és a SOC 2 szabványnak felel meg.  Emellett a saját licencű (BYOL) modellt is használja: operációs rendszer, speciális munkaterhelés és külső alkalmazások.  

A root Access és a teljes hozzáférés megszerzését követően a következőkért kell felelősséget vállalnia:
- Biztonsági mentési és helyreállítási megoldások tervezése és megvalósítása, magas rendelkezésre állás és vész-helyreállítás
- Az operációs rendszer és a harmadik féltől származó szoftverek licencelése, biztonsága és támogatása

A Microsoft felelős a következőkért:
- A hardver speciális számítási feladatokhoz való biztosítása 
- Az operációs rendszer üzembe helyezése

:::image type="content" source="media/baremetal-support-model.png" alt-text="BareMetal-infrastruktúra támogatási modellje" border="false":::

## <a name="compute"></a>Compute
A BareMetal-infrastruktúra több SKU-t kínál a speciális számítási feladatokhoz. Rendelkezésre álló SKU-tartomány a kisebb, két szoftvercsatorna-rendszerről a 24 socket rendszerre. A speciális számítási feladatokhoz használja a munkaterhelés-specifikus SKU-ket.

A BareMetal-példány bélyegzője maga ötvözi a következő összetevőket:

- **Számítástechnika:** Az Intel Xeon processzorok eltérő generációján alapuló kiszolgálók, amelyek biztosítják a szükséges számítástechnikai képességet, és amelyek a speciális számítási feladatokhoz vannak hitelesítve.

- **Hálózat:** Az egységes, nagy sebességű hálózati hálók összekapcsolódnak a számítástechnikai, tárolási és LAN-összetevőkkel.

- **Tárterület:** Egy egységes hálózati hálón keresztül elért infrastruktúra.

A BareMetal Stamp több-bérlős infrastruktúráján belül az ügyfeleket elkülönített bérlők helyezik üzembe. Bérlő telepítésekor egy Azure-előfizetést kell megkeresnie az Azure-regisztráción belül. Ez az Azure-előfizetés a BareMetal-példányok számlázására szolgál.

>[!NOTE]
>A BareMetal-példányban üzembe helyezett ügyfelek elkülönítettek egy bérlőbe. A bérlő a többi bérlő hálózati, tárolási és számítási rétegében el van különítve. A különböző bérlők számára hozzárendelt tárolási és számítási egységek nem látják egymást, és nem tudnak kommunikálni egymással a BareMetal példányokon.

## <a name="os"></a>Operációs rendszer
A BareMetal-példány kiépítés során kiválaszthatja a gépekre telepíteni kívánt operációs rendszert. 

>[!NOTE]
>Ne feledje, hogy a BareMetal-infrastruktúra egy BYOL-modell.

Az elérhető linuxos operációsrendszer-verziók a következők:
- Red Hat Enterprise Linux (RHEL) 7,6
- SUSE Linux Enterprise Server (SLES)
   - SLES 12 SP2
   - SLES 12 SP3
   - SLES 12 SP4
   - SLES 12 SP5
   - SLES 15 SP1

## <a name="storage"></a>Tárolás
Az adott SKU-típuson alapuló BareMetal-példányok előre definiált NFS-tárolóval rendelkeznek az adott munkaterhelés-típushoz. A BareMetal kiépítésekor a becsült növekedés alapján több tárhelyet is kiépítheti egy támogatási kérelem elküldésével. Az összes tárterület a 4,2-es verzióban található, az NFSv3-t és a Nfsv4 névleképezője-t támogató összes flash-lemezt tartalmaz. A 4,5 NVMe SSD újabb verziója elérhető lesz. A tárolók méretezésével kapcsolatos további információkért tekintse meg a [BareMetal munkaterhelés típusa](../virtual-machines/workloads/sap/get-started.md) szakaszt.

>[!NOTE]
>A BareMetal-hez használt tárterület megfelel a [Federal Information Processing standard (FIPS) 140-2-es kiadványnak](/microsoft-365/compliance/offering-fips-140-2) , amely alapértelmezés szerint a titkosítást kínál a nyugalmi állapotban. Az adatlemezek tárolása biztonságos.

## <a name="networking"></a>Hálózatkezelés
Az Azure hálózati szolgáltatások architektúrája kulcsfontosságú összetevő a speciális számítási feladatok BareMetal-példányokban való sikeres üzembe helyezéséhez. Valószínű, hogy az összes IT-rendszer nem az Azure-ban található. Az Azure olyan hálózati technológiát kínál, amellyel az Azure úgy néz ki, mint egy virtuális adatközpont a helyszíni szoftverek központi telepítéséhez. Az BareMetal-példányokhoz szükséges Azure-hálózati funkciók a következőek:

- Az Azure-beli virtuális hálózatok a helyszíni hálózati eszközökhöz csatlakozó ExpressRoute-áramkörhöz csatlakoznak.
- A helyszíni és az Azure-hoz csatlakozó ExpressRoute-áramkörnek legalább 1 GB/s vagy annál nagyobb sávszélességgel kell rendelkeznie.
- Kiterjesztett Active Directory és DNS az Azure-ban, vagy teljesen fut az Azure-ban.

A ExpressRoute használatával a helyszíni hálózatot kiterjesztheti a Microsoft Cloud-ra a kapcsolati szolgáltató súgójában található privát kapcsolaton keresztül. A **ExpressRoute prémium** szintű engedélyezésével kiterjesztheti a kapcsolatokat a geopolitikai határokon belül, vagy a **ExpressRoute helyi** használatával költséghatékony adatátvitelt biztosíthat a kívánt Azure-régióhoz közeli hely között.

Az BareMetal-példányok az Azure VNET-kiszolgáló IP-címtartomány keretében vannak kiépítve.

:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" alt-text="Azure BareMetal-infrastruktúra diagramja" lightbox="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" border="false":::

A bemutatott architektúra három szakaszra oszlik:
- **Left:** a helyszíni infrastruktúrát jeleníti meg, amely különböző alkalmazásokat futtat, és a partner vagy a helyi peremhálózati útválasztón keresztül csatlakozik, például Equinix. További információ [: kapcsolati szolgáltatók és helyszínek: Azure ExpressRoute](../expressroute/expressroute-locations.md).
- **Központ:** az Azure-előfizetéssel az Azure Edge Network szolgáltatással létesített kapcsolattal kiépített [ExpressRoute](../expressroute/expressroute-introduction.md) jeleníti meg.
- **Right:** megjeleníti az Azure IaaS, és ebben az esetben a virtuális gépeket az Azure Virtual Networkben üzembe helyezendő alkalmazások üzemeltetésére használja.
- **Alul:** az ExpressRoute-átjáró használatát mutatja be a [ExpressRoute FastPath](../expressroute/about-fastpath.md) a BareMetal-kapcsolathoz, amely alacsony késést biztosít.   
   >[!TIP]
   >Ennek támogatásához a ExpressRoute-átjárónak UltraPerformance kell lennie.  További információ: [a ExpressRoute virtuális hálózati átjárók ismertetése](../expressroute/expressroute-about-virtual-network-gateways.md).

## <a name="next-steps"></a>Következő lépések

A következő lépés, hogy megtudja, hogyan azonosíthatja és kezelheti a BareMetal-példányok egységeit a Azure Portalon keresztül.

> [!div class="nextstepaction"]
> [Operációs rendszer nélküli példányok kezelése az Azure Portalon](connect-baremetal-infrastructure.md)