---
title: Az alkalmazások magas rendelkezésre állásának biztosítása az Azure-beli VMware-ben való futtatáskor
description: A CloudSimple magas rendelkezésre állási funkcióit ismerteti a CloudSimple privát felhőben futó alkalmazások általános alkalmazáshiba-forgatókönyvei esetében
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6c80b9fd65588fe6c390f44b34509168f3bfb549
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077686"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>Az alkalmazások magas rendelkezésre állásának biztosítása az Azure-beli VMware-ben való futtatáskor

A CloudSimple megoldás magas rendelkezésre állást biztosít a VMware-en futó alkalmazások számára az Azure-környezetben. A következő táblázat felsorolja a hibák forgatókönyveit és a hozzájuk kapcsolódó magas rendelkezésre állási funkciókat.

|  Meghibásodási forgatókönyv  |  Az alkalmazás védett?  |  Platform HA funkció  |  VMware HA funkció  |  Azure HA szolgáltatás  |
|----------------------------------------|------------------------|-------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------|
|  Lemez meghibásodása  |  IGEN  |  Sikertelen csomópont gyors cseréje  |  [Tudnivalók a vSAN alapértelmezett tárolási házirendjéről](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html)  |  |
|  Ventilátor meghibásodása  |  IGEN  |  Redundáns ventilátorok, sikertelen csomópont gyors cseréje  |  |  |
|  NIC-hiba  |  IGEN  |  Redundáns hálózati adapter, sikertelen csomópont gyors cseréje  |  |  |
|  Gazdagép áramszünet  |  IGEN  |  Redundáns tápegység  |  |  |
|  ESXi-gazdagép meghibásodása  |  IGEN  |  sikertelen csomópont gyors cseréje  |  [VMware vSphere magas rendelkezésre állás](https://www.vmware.com/products/vsphere/high-availability.html)  |  |
|  VM-hiba  |  IGEN  |  [Terheléselosztó](load-balancers.md)  |  [VMware vSphere magas rendelkezésre állás](https://www.vmware.com/products/vsphere/high-availability.html)  |  Állapot nélküli VMware virtuális gépek Azure Load Balancer  |
|  Levél-kapcsoló portjának hibája  |  IGEN  |  Redundáns hálózati adapter  |  |  |
|  A levél kapcsolójának hibája  |  IGEN  |  Redundáns levél kapcsolók  |  |  |
|  Rack-hiba  |  IGEN  |  Elhelyezési csoportok  |  |  |
|  Hálózati kapcsolat a helyszíni TARTOMÁNYVEZÉRLŐvel  |  IGEN  |  Redundáns hálózati szolgáltatások  |  |  Redundáns ER-áramkörök  |
|  Az Azure-hoz való hálózati kapcsolat  |  IGEN  |  |  |  Redundáns ER-áramkörök  |
|  Adatközpont-hiba  |  IGEN  |  |  |  Rendelkezésre állási zónák  |
|  Regionális hiba  |  IGEN  |  |  |  Azure-régiók  |

A CloudSimple által biztosított Azure VMware-megoldás a következő magas rendelkezésre állási funkciókat biztosítja.

## <a name="fast-replacement-of-failed-node"></a>Sikertelen csomópont gyors cseréje

A CloudSimple vezérlő síkja szoftver folyamatosan figyeli a VMware-fürtök állapotát, és észleli, ha egy ESXi-csomópont meghibásodik. Ezután automatikusan felvesz egy új ESXi-gazdagépet az érintett VMware-fürtbe az azonnal elérhető csomópontok készletéből, és a meghibásodott csomópontot a fürtből veszi át. Ez a funkció biztosítja, hogy a VMware-fürtön lévő tartalék kapacitást gyorsan visszaállítsa a rendszer, hogy a fürt vSAN és a VMware hektár által biztosított rugalmassága helyreálljon.

## <a name="placement-groups"></a>Elhelyezési csoportok

Egy privát felhőt létrehozó felhasználó kijelölhet egy Azure-régiót és egy elhelyezési csoportot a kiválasztott régión belül. Az elhelyezési csoport több állványon, de ugyanazon a gerincen lévő hálózati szegmensen belül elterjedt csomópontok halmaza. Az azonos elhelyezési csoportban lévő csomópontok legfeljebb két további ugrással rendelkezhetnek egymással. Az elhelyezési csoportok mindig egyetlen Azure rendelkezésre állási zónán belül találhatók, és több állványra is kiterjednek. A CloudSimple-vezérlési sík a saját felhő csomópontjait több állványra osztja el, a legjobb erőfeszítés alapján. A különböző elhelyezési csoportokban lévő csomópontok garantáltan különböző állványokra helyezhetők.

## <a name="availability-zones"></a>Rendelkezésreállási zónák

A rendelkezésre állási zónák olyan magas rendelkezésre állású ajánlat, amely védelmet nyújt alkalmazásai és adatai számára az adatközpont hibáiból. A rendelkezésre állási zónák egy Azure-régióban található speciális fizikai helyek. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. Minden régió rendelkezik egy rendelkezésre állási zónával. További információ: [Mi a Availability Zones az Azure-ban?](../availability-zones/az-overview.md).

## <a name="redundant-azure-expressroute-circuits"></a>Redundáns Azure ExpressRoute-áramkörök

Az Azure vNet-hez a ExpressRoute használatával létesített adatközpont-kapcsolat redundáns áramkörökkel rendelkezik, így biztosítva a jól elérhető hálózati kapcsolati kapcsolatot.

## <a name="redundant-networking-services"></a>Redundáns hálózati szolgáltatások

A CloudSimple összes hálózati szolgáltatását (beleértve a VLAN-t, a tűzfalat, a nyilvános IP-címeket, az internetet és a VPN-t) úgy tervezték, hogy kiválóan elérhetők legyenek, és képesek legyenek támogatni a szolgáltatást.

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>Azure Layer 7 Load Balancer az állapot nélküli VMware virtuális gépekhez

A felhasználók a VMware-környezetben futó állapot nélküli webes rétegbeli virtuális gépek előtt helyezhetnek el egy Azure Layer 7 Load Balancert, hogy magas rendelkezésre állást érjenek el a webes réteg számára.

## <a name="azure-regions"></a>Azure-régiók

Az Azure-régiók olyan adatközpontok halmaza, amelyek egy késés által meghatározott kerületen belül üzemelnek, és egy dedikált regionális, kis késleltetésű hálózaton keresztül kapcsolódnak egymáshoz. Részletekért lásd: [Azure-régiók](https://azure.microsoft.com/global-infrastructure/regions).
