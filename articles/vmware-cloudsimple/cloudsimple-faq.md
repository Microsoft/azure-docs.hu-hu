---
title: Gyakori kérdések – Azure VMware-megoldás a CloudSimple
description: 'Ezek a gyakori kérdések az Azure VMware-megoldással kapcsolatban: kapcsolat, hálózatkezelés, biztonság, tárolás, Azure-integráció és egyéb CloudSimple.'
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3551327f9aa07fe218d3b05f73057e36f92abcad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895563"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Gyakori kérdések a VMware-megoldásról a CloudSimple

## <a name="cloudsimple-service"></a>CloudSimple szolgáltatás

**Mi az az Azure VMware-megoldás a CloudSimple?**

A CloudSimple által nyújtott Azure VMware-megoldás átalakítja és kibővíti a VMware számítási feladatokat az Azure-ban, percek alatt, dedikált felhőben. A CloudSimple gondoskodik a kiépítésről, az infrastruktúra kezeléséről, valamint a helyszíni és az Azure közötti számítási feladatok előkészítéséről. Mivel az alkalmazásai pontosan ugyanúgy futnak a helyszínen és az Azure-ban, a felhő rugalmasságát és szolgáltatásait is kihasználhatja az alkalmazások újratervezésének bonyolultsága nélkül. A CloudSimple csökkenti a teljes tulajdonlási költségeket egy Felhőbeli fogyasztási modellel, amely igény szerinti kiosztást, az elszámolást és a kapacitás optimalizálását biztosítja.  Ismerje [meg, mi az Azure](cloudsimple-vmware-solutions-overview.md) -beli VMware-megoldás a funkciók, előnyök és forgatókönyvek CloudSimple.

**Mi a CloudSimple privát felhő?**

A CloudSimple privát felhő egy privát, dedikált felhő, amely egy nagy teljesítményű számítási, tárolási és hálózatkezelési környezetből áll, amelyet az Azure-beli Microsoft Azure infrastruktúra (hardver és adatközpont-terület) helyez üzembe.  A privát felhő egy natív VMware platformot biztosít a szolgáltatásként. A VMware kifejezésekben minden egyes privát felhő pontosan egy példányt tartalmaz a vCenter Server. A vCenter Server felügyel több ESXi-csomópontot egy vagy több vSphere-fürtben, a hozzá tartozó virtuális SAN-(vSAN-) tárolóval együtt. A CloudSimple szolgáltatás több privát felhőt is tartalmazhat az Azure-előfizetésében.  További részletekért lásd: a [privát felhő áttekintése](cloudsimple-private-cloud.md).

**Hol érhető el a CloudSimple szolgáltatás?**

A CloudSimple az USA keleti régiójában, az USA nyugati régiójában és a Nyugat-európai régióban is elérhető, és hamarosan további régiókban érhető el.

**Hogyan engedélyezni a CloudSimple-előfizetést?**

A [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) CloudSimple szolgáltatás előfizetésének engedélyezéséhez vegye fel a kapcsolatot a Microsoft-fiók képviselőjével. Adja meg az előfizetés-AZONOSÍTÓját az e-mailben, amelyhez engedélyezni szeretné a CloudSimple szolgáltatást.  

**Hogyan hozzáférni a CloudSimple-portálhoz?**

A CloudSimple-portálhoz a Azure Portal férhet hozzá.  Részletekért lásd: [hozzáférés a VMware-megoldáshoz a CloudSimple portálról a Azure Portal](access-cloudsimple-portal.md).

**Hogyan a saját felhő kapacitásának növelését?**

A kapacitás növeléséhez vásároljon további csomópontokat a Azure Portal, majd a csomópontok segítségével bontsa ki a saját Felhőjét a CloudSimple portálról.  Hozzáadhat további csomópontokat egy meglévő vSphere-fürthöz, vagy hozzáadhat egy új vSphere-fürthöz.  Részletekért lásd: [CloudSimple privát felhő kibontása](expand-private-cloud.md).

**Mi történik a saját felhővel a karbantartás során?**

A CloudSimple egy ütemezett karbantartási időszak előtt több nappal biztosít értesítést.  A karbantartást nem zavaró módon végezheti el a privát felhő rendelkezésre állásának biztosítása érdekében.  A karbantartás a következő típusú lehet:

* **CloudSimple-infrastruktúra**.  A CloudSimple-infrastruktúra kiválóan elérhető.  Az ilyen típusú karbantartási időszak során a redundáns összetevők egyszerre frissülnek, hogy elkerülje a szolgáltatás megszakadását. Ön gondoskodik a saját felhőalapú vCenter, az összes virtuális géphez, a saját Felhőbeli internetkapcsolatról, valamint a helyszíni vagy az Azure-hoz való csatlakozásról.
* **CloudSimple-portál**. Az ilyen típusú karbantartási időszak során előfordulhat, hogy a CloudSimple-portál néhány funkciója le van tiltva vagy nem érhető el.  A karbantartási időközt megelőzően az értesítés a szolgáltatásra vonatkozó korlátozásokkal kapcsolatos részleteket tartalmaz, miközben karbantartás zajlik.

## <a name="connectivity"></a>Kapcsolatok

**Milyen csatlakozási lehetőségek vannak a CloudSimple-régió hálózatához?**

A CloudSimple a következő csatlakozási lehetőségeket biztosítja a CloudSimple-régió hálózatához való csatlakozáshoz. Egyszerre több lehetőség is használható.

* **ExpressRoute a helyszíni adatközpontból a CloudSimple-régió hálózatára**. Ez egy nagy sebességű, kis késleltetésű, biztonságos magánhálózati kapcsolat, amely Global Reacht használ a helyszíni ExpressRoute áramkör a CloudSimple ExpressRoute-áramkörhöz való áthidalása érdekében. A kapcsolat beállításával kapcsolatos útmutatásért lásd: [Kapcsolódás a helyszínről a CloudSimple a ExpressRoute használatával](on-premises-connection.md).
* **ExpressRoute az Azure-beli virtuális hálózatról a CloudSimple-régió hálózatához való kapcsolódást**. Ez egy nagy sebességű, kis késleltetésű, biztonságos magánhálózati kapcsolat, amely virtuális hálózati átjárókat használ az Azure-beli virtuális hálózat CloudSimple ExpressRoute-áramkörre való áthidalása érdekében. A kapcsolat beállításával kapcsolatos útmutatásért lásd: [a CloudSimple saját felhőalapú környezetének csatlakoztatása az Azure Virtual networkhez a ExpressRoute használatával](azure-expressroute-connection.md).
* **Helyek közötti VPN-kapcsolat a helyszíni adatközpontból a CloudSimple-régió hálózatára**. Ez egy biztonságos virtuális magánhálózat a helyszíni VPN-eszközről a CloudSimple saját felhő-régiójába.  Részletekért lásd: [VPN-átjárók beállítása a CloudSimple hálózaton](vpn-gateway.md).

**Hogyan csatlakozni a privát felhőhöz?**

A privát felhő részleteit a CloudSimple-portálon tekintheti meg. A privát felhőnek megfelelő vCenter való csatlakozáshoz először ellenőrizze, hogy a hálózati kapcsolat létrejött-e a helyek közötti VPN, pont – hely típusú VPN vagy ExpressRoute használatával. Ezután indítsa el az CloudSimple-portált a Azure Portal, majd kattintson a kezdőlapon vagy a privát felhő részletei lapon található **vSphere-ügyfél elindítása** lehetőségre.

**Mi a ExpressRoute-áramkörök előnye?**

Az Azure ExpressRoute áramkör nagy sebességű, kis késleltetésű és biztonságos kapcsolat.  A CloudSimple egy régiónként dedikált ExpressRoute-áramkört biztosít az ügyfelek számára.  Ezen áramkör használatával biztonságos kapcsolatot létesíthet a helyszínen vagy az Azure-előfizetésében.

**Milyen hálózati költségekkel csatlakozhat a CloudSimple?  A kimenő forgalomra vonatkozó díjak a CloudSimple és az Azure, illetve az egyes régiók között is érvényesek?**

A hálózati kimenő forgalomért nem számítunk fel CloudSimple díjat.  Az Azure standard díjszabása a virtuális hálózatról vagy a helyszíni ExpressRoute áramkörről érkező kimenő forgalomra vonatkozik.

## <a name="networking"></a>Hálózatkezelés

**Milyen hálózatkezelési funkciók érhetők el a saját felhőhöz?**

Kiépítheti a VLAN-okat (és az alhálózatokat) és a tűzfalakat, valamint hozzárendelhet nyilvános IP-címeket, amelyek a saját felhőben futó virtuális gépekre képezhetők le. A hálózatkezelési funkciókkal kapcsolatos részletekért lásd: a [VLAN-ok és az alhálózatok áttekintése](cloudsimple-vlans-subnets.md), a [tűzfalak áttekintése](cloudsimple-firewall-tables.md)és a [nyilvános IP-cím áttekintése](cloudsimple-public-ip-address.md).

**Hogyan különböző alhálózatokat beállítani az alkalmazásaim számára a saját felhőben?**

A CloudSimple-portálról VLAN-okat hozhat létre a privát felhőben.  Miután létrehozta a VLAN-t, létrehozhat egy elosztott portot a saját Felhőbeli vCenter a VLAN használatával, és létrehozhat olyan virtuális gépeket, amelyek az elosztott port csoportjához vannak csatlakoztatva.  Engedélyezheti a tűzfal-táblákat a VLAN/alhálózat számára, és megadhatja a tűzfalszabályok a hálózati forgalom biztonságossá tételéhez.

**Milyen tűzfal-beállítások érhetők el a privát Felhőkhöz?**

Az észak-déli és a kelet-nyugati forgalomra vonatkozó szabályokat is konfigurálhat.  A szabályok definiálva vannak egy tűzfal táblában.  A tűzfal tábla a privát felhőben lévő VLAN-okra is csatlakoztatható.  Részletekért lásd: [a tűzfalak és a privát felhők szabályainak beállítása](firewall.md).

**Kioszthatok nyilvános IP-címeket a saját felhőalapú környezetében lévő virtuális gépekhez?**

A CloudSimple-portálon lefoglalhat egy új nyilvános IP-címet, és társíthatja azt egy virtuális gép vagy berendezés magánhálózati IP-címével.  Új tűzfalszabályok is létrehozhatók, vagy a meglévő tűzfalszabályok alkalmazása lehetővé teszi, hogy a portálon az adott portokból és IP-címekről érkező forgalom is elérhető legyen. Részletekért lásd: [nyilvános IP-címek lefoglalása privát felhőalapú környezethez](public-ips.md).

## <a name="security"></a>Biztonság

**Mik a CloudSimple biztonsági beállításai?**

A CloudSimple a következő biztonsági funkciókat biztosítja a saját felhőalapú környezetének biztonságossá tételéhez:

* Inaktív **adatok titkosítása**. A privát felhőben lévő vSAN-tárolóban lévő REST-adatok titkosítva lehetnek. a vSAN támogatja a külső kulcsokat kezelő kiszolgálókat, amelyek üzembe helyezhetők az Azure vNet vagy a helyszíni környezetben.  Részletekért lásd: [a vSAN-titkosítás konfigurálása a CloudSimple privát felhőhöz](vsan-encryption.md).
* **Hálózati biztonság**. Szabályozhatja a hálózati forgalom áramlását a saját felhő és az Internet, a saját felhő és a helyszíni környezet, illetve a saját felhő alhálózatai között alkalmazott tűzfalszabályok használatával.
* **Biztonságos, magánhálózati kapcsolatok**. Biztonságos, magánhálózati kapcsolat áll fenn a helyszíni hálózat és az Azure-előfizetés között.

## <a name="compute"></a>Compute

**Milyen típusú gazdagépek érhetők el?**

A CloudSimple a következő gazdagép-típusokat kínálja:

* **CS28 csomópont:** CPU: 2x 2,2 GHz, összesen 28 mag, 48 HT.  RAM: 256 GB.  Tárterület: 1600 GB NVMe cache, 5760 GB adat (all-Flash). Hálózat: 4x25Gbe hálózati adapter
* **CS36 csomópont:** CPU 2x 2,3 GHz, összesen 36 mag, 72 HT.  RAM: 512 GB.  Storage: 3200 GB NVMe cache 11520 GB adat (teljes Flash).  Hálózat: 4x25Gbe hálózati adapter
* **CS36m csomópont:** CPU 2x 2,3 GHz, összesen 36 mag, 72 HT.  RAM: 576 GB.  Storage: 3200 GB NVMe cache 13360 GB adat (teljes Flash).  Hálózat: 4x25Gbe hálózati adapter

**Hogyan történik a hardveres hibák kezelése?**

A CloudSimple platform és a szolgáltatási operatív csapatok folyamatosan figyelik az összes CloudSimple-infrastruktúrát.  Ha hardverhiba észlelhető, egy új csomópont kerül a saját felhőbe, és a rendszer eltávolítja a meghibásodott csomópontot.

## <a name="storage"></a>Tárolás

**Milyen típusú tárterület támogatott a privát felhőben?**

A CloudSimple minden privát felhőben biztosít minden Flash VMware vSAN-tárolót.  Minden vSphere a saját vSAN adattárával jön létre.  Részletekért lásd: [Private Cloud VMware Components-vSAN Storage](vmware-components.md#vsan-storage).

**Támogatott az adattitkosítás?**
Igen.  A vSAN-tárolót a saját felhőben is beállíthatja, hogy a helyszíni vagy az Azure-ban üzembe helyezett kulcskezelő kiszolgáló (KMS) használatával Titkosítsa a vSAN tárolt adatait.

**Hogyan történik a hibás lemezek kezelése?**

A CloudSimple folyamatosan figyeli a saját felhő összes hardveres összetevőjét.  Ha a rendszer hibát észlel, vagy ha egy lemez hibásként van azonosítva (heurisztikus alapján), a rendszer automatikusan hozzáadja az új csomópontot a privát felhőhöz.  A rendszer eltávolítja a meghibásodott vagy meghibásodott lemezt tartalmazó csomópontot a privát felhőből.

## <a name="vmware"></a>VMware

**Hogyan a helyszíni alkalmazások és adatok nagy léptékű feltöltését vagy áttelepítését?**

A CloudSimple natív VMware vSphere megoldást biztosít.  Az összes VMware-eszköz a tömeges adatáttelepítéshez használhatja a saját Felhőjét.  A lehetőségek a következők:

* VMware-HCX az adatfeldolgozás tömeges áttelepítéséhez.
* Az adatok hideg áttelepítése a helyszíni vMotion a CloudSimple-be.

**Telepíthetek VMware-eszközöket?**

A CloudSimple natív VMware vSphere megoldást biztosít.  A helyszíni vSphere-környezet kezeléséhez használt összes VMware-eszköz használható a CloudSimple-on.  A CloudSimple támogatja a VMware-eszközök telepítésének saját Licences (BYOL) modelljét.

**Hogyan kezelik a frissítéseket és a frissítéseket?**

A CloudSimple zökkenőmentesen nem zavaró módon kezeli és frissíti a saját felhő összes infrastruktúra-összetevőjét.  A VMware-vagy infrastruktúra-szolgáltatók által kiadott összes frissítés és biztonsági javítás ütemezett frissítésre van ütemezve, amint a CloudSimple-nek minősítette őket.

A CloudSimple nem hajtja végre a privát felhőre telepített alkalmazások frissítéseit és frissítéseit.

## <a name="azure-integration"></a>Azure-integráció

**Mely Azure-szolgáltatások támogatottak?**

A CloudSimple Azure-beli ExpressRoute-kapcsolódást biztosít az Azure-előfizetéshez.  Az előfizetésben futó összes szolgáltatás kapcsolódhat a saját felhőhöz.  Példák:

* **Azure Active Directory** identitás forrásaként a CloudSimple-vCenter.
* **Azure Storage** biztonsági másolatok, képek és egyéb adatok tárolására a saját felhőből.
* **Hibrid alkalmazások** a nyilvános és a privát felhőkre kiterjedő alkalmazás-architektúrával.  Létrehozhat például olyan webkiszolgálókat az Azure-ban, amelyek hozzáférnek az alkalmazás-és adatbázis-kiszolgálókhoz a saját felhőben.
* Az **Azure monitor** és az **Azure Security Center** a VMware-támogatás naplózásával, teljesítmény-metrikákkal és biztonsági felügyelettel futó munkaterhelésekhez.

**Hogyan a VMware-bérlőket az Azure-ba?**

A CloudSimple lehetővé teszi, hogy a VMware virtuális gépeket a Azure Portalból kezelje a saját felhőben.  A kívánt erőforrás-korlátozásokkal konfigurált vCenter erőforráskészlet a globális rendszergazda által előfizetéshez rendelhető hozzá.  

**Milyen licencelési előnyökkel jár az Azure?**

A CloudSimple használatával kihasználhatja az Azure Hybrid használati juttatás előnyeit, és akár 90%-ot is megtakaríthat a licencek esetében. Ez az előny megőrzi a Microsoft-licencek beruházásait, és csökkenti a TCO-t más felhőalapú megoldásokhoz képest. A Windows Server 2008-es és a Microsoft SQL Server 2008-os bővített biztonsági frissítései is megszerezhetők.  A saját licencű (BYOL) modell segítségével alacsonyan tarthatók a költségek a gyakori alkalmazások, például a Veeam és a Zerto esetében.  
