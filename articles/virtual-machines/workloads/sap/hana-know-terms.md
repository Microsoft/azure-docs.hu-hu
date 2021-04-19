---
title: Az Azure-SAP HANA (nagyméretű példányok) használati szerződésének | Microsoft Docs
description: Az Azure-beli SAP HANA (nagy méretű példányok) használati szerződésének.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 4/16/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a243b348c01e6d1297a6a1fe016e3b6bc8d98d47
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719078"
---
# <a name="know-the-terms"></a>A feltételek megismerése

Az architektúra és a műszaki üzembe helyezési útmutató számos gyakori definíciót használ. Figyelje meg a következő kifejezéseket és azok jelentését:

- **IaaS:** Szolgáltatásként nyújt infrastruktúrát.
- **PaaS:** Szolgáltatásként platform.
- **SaaS:** Szolgáltatott szoftver.
- **SAP-összetevő:** Egyéni SAP-alkalmazás, például ERP Central Component (ECC), Business Warehouse (BW), Solution Manager vagy Enterprise Portal (EP). Az SAP-összetevők alapulhat hagyományos ABAP- vagy Java-technológiákon, vagy nem NetWeaver-alapú alkalmazásokon, például üzleti objektumokon.
- **SAP-környezet:** Egy vagy több SAP-összetevő logikailag van csoportosítva egy üzleti funkció végrehajtásához, például fejlesztés, minőségbiztosítási, betanítási, vészhelyreállítási vagy éles környezet.
- **SAP-környezet:** Az IT-környezet teljes SAP-adateszközére vonatkozik. Az SAP-környezet az összes éles és nem éles környezetet magában foglalja.
- **SAP-rendszer:** A DBMS réteg és az alkalmazásréteg, például egy SAP ERP fejlesztési rendszer, egy SAP BW-tesztrendszer és egy SAP CRM éles rendszer kombinációja. Az Azure-beli üzemelő példányok nem támogatják a két réteg helyszíni és Azure-beli osztódásait. Az SAP-rendszerek vagy a helyszínen, vagy az Azure-ban vannak telepítve. Az SAP-környezet különböző rendszereit az Azure-ban vagy a helyszínen is üzembe helyezheti. Üzembe helyezheti például az SAP CRM fejlesztési és tesztelési rendszereit az Azure-ban, miközben az ÉLES SAP CRM rendszert a helyszínen telepíti. Az SAP HANA azure-beli (nagy méretű példányok) esetében az a cél, hogy az SAP-rendszerek SAP-alkalmazásrétegét virtuális gépeken, a kapcsolódó SAP HANA-példányt pedig az azure-beli SAP HANA-bélyeg egy egységében használja.
- **Nagyméretű példánybélyeg:** TDI SAP HANA tanúsítvánnyal rendelkező és dedikált hardverinfra SAP HANA az Azure-ban.
- **SAP HANA Azure-ban (nagyméretű példányok):** Az Azure-beli ajánlat hivatalos neve, amely SAP HANA TDI-tanúsítvánnyal rendelkező hardveren futtat HANA-példányokat a különböző Azure-régiókban található nagyméretű példányok bélyegén. A *nagy haNA-példány* kifejezés röviden az *Azure SAP HANA (nagy méretű példányok)* szolgáltatásra röviden, és széles körben használatos ebben a műszaki üzembe helyezési útmutatóban.
- **Létesítmények** közötti: Egy olyan forgatókönyvet ismertet, amelyben a virtuális gépeket olyan Azure-előfizetésben kell üzembe helyezni, amely a helyszíni adatközpontok és az Azure között Azure ExpressRoute helyközi, több telephelyes vagy Azure ExpressRoute kapcsolattal rendelkezik. Az Azure gyakori dokumentációjában az ilyen típusú üzembe helyezéseket létesítmények közötti forgatókönyveknek is írják le. A kapcsolat oka a helyszíni tartományok, a helyszíni Azure Active Directory/OpenLDAP és a helyszíni DNS kiterjesztése az Azure-ba. A helyszíni környezet kiterjeszthető az Azure-előfizetések Azure-eszközeire is. Ezzel a bővítvánnyal a virtuális gépek a helyszíni tartomány részei is lehetnek. 

   A helyszíni tartomány tartományi felhasználói hozzáférhetnek a kiszolgálókhoz, és szolgáltatásokat futtatnak a virtuális gépeken (például DBMS-szolgáltatások). A helyszíni és az Azure-ban üzembe helyezett virtuális gépek közötti kommunikáció és névfeloldás lehetséges. Ez a forgatókönyv jellemzően a legtöbb SAP-eszköz üzembe helyezésének módját jelenti. További információ: [Azure VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md) virtuális hálózat létrehozása hely–hely kapcsolattal [az Azure Portal.](../../../vpn-gateway/tutorial-site-to-site-portal.md)
- **Bérlő:** A HANA nagyméretű példányok bélyegén üzembe helyezett ügyfelek el vannak különítve a *bérlőben.* A bérlők a hálózati, tárolási és számítási rétegben elkülönülnek a többi bérlőtől. A különböző bérlőkhöz rendelt tárolási és számítási egységek nem láthatják egymást, és nem kommunikálhatnak egymással a HANA nagyméretű példányok bélyegszintén. Az ügyfelek dönthetnek úgy, hogy különböző bérlőkben üzemelő példányokat hoznak üzembe. A haNA nagyméretű példányok bélyegszintén a bérlők között ekkor sem lesz kommunikáció.
- **Termékváltozat-kategória:** Nagyméretű HANA-példányok esetében a következő két termékváltozat-kategória áll rendelkezésre:
    - **I osztály** típusa: S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 és S224m
    - **II. típusosztály:** S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm és S960m
- **Bélyeg:** A HANA nagyméretű példányainak belső üzembe helyezési méretét határozza meg a Microsoftnál. Mielőtt a HANA nagyméretű példányegységei üzembe helyezhetők, üzembe kell helyezni egy nagy méretű HANA-bélyeget, amely számítási, hálózati és tárolóállványokból áll. Az ilyen üzemelő példány neve HANA nagyméretű példánybélyeg, vagy a 4. változatból (lásd alább) a nagyméretű példány sor alternatív **kifejezését használjuk**
- **Változat:** A HANA nagyméretű példányok bélyegei két különböző bélyeg-változatban vannak. Ezek az architektúra és az Azure-beli virtuálisgép-gazdagépek közelében különböznek.
    - A "3. változat" (Rev 3) a 2016 közepétől üzembe helyezett eredeti kialakítás.
    - A "4.2 változat" (Rev 4.2) egy új kialakítás, amely közelebb nyújt az Azure-beli virtuálisgép-gazdagépekhez. A Rev 4.2 rendkívül alacsony hálózati késést biztosít az Azure-beli virtuális gépek és a HANA nagyméretű példányok között. A Azure Portal az erőforrásokat BareMetal-infrastruktúrának nevezzük. Az ügyfelek BareMetal-példányként férhetnek hozzá az erőforrásaikhoz a Azure Portal. 

Az SAP számítási feladatok felhőben való üzembe helyezéséhez számos további erőforrás áll rendelkezésre. Ha egy azure-beli SAP HANA üzembe helyezését tervezi, tisztában kell lennie az Azure IaaS alapelveivel és az SAP számítási feladatok Azure IaaS-beli üzembe helyezésével. A folytatás előtt további információért lásd: SAP-megoldások használata [Azure-beli](get-started.md) virtuális gépeken. 

## <a name="next-steps"></a>Következő lépések
- Lásd: [HLI-tanúsítvány.](hana-certification.md)