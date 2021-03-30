---
title: Private Cloud VMware-összetevők
titleSuffix: Azure VMware Solution by CloudSimple
description: Ismerje meg, hogy a CloudSimple szolgáltatás lehetővé teszi, hogy natív módon telepítse a VMware-et az Azure-beli helyszíneken. A privát felhők integrálva vannak az Azure-felhő többi részébe.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ff9aec1904be754990958869666e9d67038e4fb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88142502"
---
# <a name="private-cloud-vmware-components"></a>Private Cloud VMware-összetevők

A privát felhő egy elszigetelt VMware stack (ESXi gazdagépek, vCenter, vSAN és NSX) környezet, amelyet egy vCenter-kiszolgáló felügyel egy felügyeleti tartományban.  A CloudSimple szolgáltatás lehetővé teszi, hogy natív módon telepítse a VMware-et az Azure-beli operációs rendszer nélküli Azure-infrastruktúrában.  A privát felhők integrálva vannak az Azure-felhő többi részébe.  A privát felhő a következő VMware stack-összetevőkkel van üzembe helyezve:

* **VMWare ESXi –** Hypervisor az Azure dedikált csomópontjain
* **VMware vCenter –** A saját felhőalapú vSphere-környezet központi felügyeletére szolgáló berendezés
* **VMware vSAN –** Hyper-konvergens infrastruktúra-megoldás
* **VMware NSX adatközpont –** Hálózati virtualizálási és biztonsági szoftverek  

## <a name="vmware-component-versions"></a>VMware-összetevők verziói

A Private Cloud VMware stack a következő szoftverrel van üzembe helyezve.

| Összetevő | Verzió | Licencelt verzió |
|-----------|---------|------------------|
| ESXi | 6.7 U2 | Enterprise Plus |
| vCenter | 6.7 U2 | vCenter standard |
| vSAN | 6.7 | Enterprise |
| NSX adatközpont | 2.4.1 | Felsőfokú |

## <a name="esxi"></a>ESXi

A VMware ESXi a kiépített CloudSimple-csomópontokra van telepítve a privát felhő létrehozásakor.  Az ESXi biztosítja a számítási feladatok virtuális gépei (VM-EK) üzembe helyezését.  A csomópontok Hyper-konvergens infrastruktúrát (számítási és tárolási) biztosítanak a saját felhőben.  A csomópontok a vSphere-fürt részét képezik a privát felhőben.  Mindegyik csomóponthoz négy fizikai hálózati adapter csatlakozik az alátét hálózathoz.  A rendszer két fizikai hálózati adaptert használ egy **vSphere elosztott kapcsoló (VDS)** létrehozására a vCenter, a kettő pedig egy **NSX által felügyelt virtuális elosztott kapcsoló (N-VDS)** létrehozásához használatos.  A hálózati adapterek aktív-aktív módban vannak konfigurálva a magas rendelkezésre állás érdekében.

További információ a VMware ESXi

## <a name="vcenter-server-appliance"></a>vCenter-kiszolgáló készülék

a vCenter Server Appliance (VCSA) a CloudSimple által biztosított VMware-megoldás hitelesítési, felügyeleti és előkészítési funkcióit biztosítja. A VCSA a beágyazott platform Services-vezérlővel (PSC) együtt helyezik üzembe a saját felhő létrehozásakor.  A VCSA telepítve van a vSphere-fürtön, amely a saját felhő telepítésekor jön létre.  Mindegyik privát felhő saját VCSA rendelkezik.  A privát felhő bővítése a csomópontokat hozzáadja a VCSA a privát felhőben.

### <a name="vcenter-single-sign-on"></a>vCenter egyszeri bejelentkezés

A VCSA Embedded platform Services-vezérlője egy **vCenter egyetlen Sign-On tartománnyal** van társítva.  A tartománynév a **cloudsimple. local** nevet adja meg.  A rendszer létrehoz egy alapértelmezett felhasználót a **CloudOwner@cloudsimple.com** vCenter eléréséhez.  Felveheti a helyszíni/Azure Active Directory [Identity sources for vCenter](set-vcenter-identity.md).

## <a name="vsan-storage"></a>vSAN-tároló

A privát felhők teljesen konfigurált összes Flash vSAN-tárolóval jönnek létre a fürtben.  Az vSAN adattárral rendelkező vSphere-fürtök létrehozásához legalább három csomópontnak kell futnia ugyanazon SKU-ban.  A vSAN adattárolón alapértelmezés szerint engedélyezve van a Duplikálás és a tömörítés.  Két lemezcsoport jön létre a vSphere-fürt minden egyes csomópontján. Mindegyik lemezcsoport egy gyorsítótár-lemezt és három kapacitású lemezt tartalmaz.

A rendszer létrehoz egy alapértelmezett vSAN-tárolási házirendet a vSphere-fürtön, és alkalmazza őket a vSAN adattárra.  Ez a házirend határozza meg, hogy a virtuális gép tárolási objektumai hogyan legyenek kiépítve és lefoglalva az adattáron belül, hogy garantálható legyen a szükséges szolgáltatási szint.  A tárolási házirend határozza meg a **tolerálható (tranzakciós)** és a **hibatűrési módszer** hibáit.  Új tárolási szabályzatokat hozhat létre, és alkalmazhatja őket a virtuális gépekre. Az SLA fenntartása érdekében a 25%-os tartalék kapacitást a vSAN adattáron kell karbantartani.  

### <a name="default-vsan-storage-policy"></a>Alapértelmezett vSAN tárolási házirend

Az alábbi táblázat a vSAN alapértelmezett tárolási házirendjének paramétereit mutatja be.

| Csomópontok száma a vSphere-fürtben | FTT | Hibatűrési módszer |
|------------------------------------|-----|--------------------------|
| 3 és 4 csomópont | 1 | RAID 1 (Tükrözés) – 2 másolat létrehozása |
| 5 – 16 csomópont | 2 | RAID 1 (Tükrözés) – 3 másolat létrehozása |

## <a name="nsx-data-center"></a>NSX adatközpont

A NSX adatközpont hálózati virtualizációs, mikro-szegmentálási és hálózati biztonsági képességeket biztosít a saját felhőben.  A NSX adatközpont által támogatott összes szolgáltatást a NSX használatával konfigurálhatja a privát felhőben.  Privát felhő létrehozásakor a következő NSX-összetevők vannak telepítve és konfigurálva.

* NSXT-kezelő
* Szállítási zónák
* Gazdagép és Edge kimenő profil
* Logikai kapcsoló az Edge Transport, az Ext1 és az EXT2 rendszerhez
* Az ESXi átviteli csomópontjának IP-készlete
* Az Edge Transport csomópont IP-készlete
* Peremhálózati csomópontok
* DRS-affinitási szabály a vezérlő és a peremhálózati virtuális gépek számára
* 0. rétegű útválasztó
* A BGP engedélyezése a Tier0-útválasztón

## <a name="vsphere-cluster"></a>vSphere-fürt

Az ESXi-gazdagépek fürtként vannak konfigurálva, hogy magas rendelkezésre állást biztosítsanak a privát felhő számára.  Privát felhő létrehozásakor a rendszer az első fürtön telepíti a vSphere felügyeleti összetevőit.  Létrejön egy erőforráskészlet a felügyeleti összetevőkhöz, és az összes felügyeleti virtuális gép üzembe van hozva ebben az erőforráskészletban. Az első fürtöt nem lehet törölni a privát felhő összezsugorodása érdekében.  a vSphere-fürt magas rendelkezésre állást biztosít a **vSphere**-t használó virtuális gépek számára.  A tolerálható hibák a fürtben elérhető csomópontok számától függenek.  Használhatja azt a képletet, ahol a a fellépő ```Number of nodes = 2N+1``` ```N``` hibák száma.

### <a name="vsphere-cluster-limits"></a>vSphere-fürt korlátai

| Erőforrás | Korlát |
|----------|-------|
| A privát felhő létrehozásához szükséges csomópontok minimális száma (első vSphere-fürt) | 3 |
| VSphere-fürt csomópontjainak maximális száma privát felhőben | 16 |
| A privát felhőben lévő csomópontok maximális száma | 64 |
| VSphere-fürtök maximális száma egy privát felhőben | 21 |
| Csomópontok minimális száma egy új vSphere-fürtön | 3 |

## <a name="vmware-infrastructure-maintenance"></a>VMware-infrastruktúra karbantartása

Időnként szükség van a VMware-infrastruktúra konfigurációjának módosítására. Jelenleg ezek az intervallumok minden 1-2 hónapban előfordulhatnak, de a gyakoriság várhatóan az idő múlásával csökken. Az ilyen típusú karbantartás általában a CloudSimple-szolgáltatások normál fogyasztásának megszakítása nélkül végezhető el. A VMware karbantartási időköze során a következő szolgáltatások továbbra is hatással vannak a működésre:

* VMware felügyeleti sík és alkalmazások
* vCenter-hozzáférés
* Minden Hálózatkezelés és tárterület
* Minden Azure-forgalom

## <a name="updates-and-upgrades"></a>Frissítések és frissítések

A CloudSimple feladata a VMware-szoftverek (ESXi, vCenter, PSC és NSX) életciklus-kezelése a privát felhőben.

A szoftverfrissítések a következők:

* **Javítások**. A VMware által kiadott biztonsági javítások vagy hibajavítások.
* **Frissítések**. Egy VMware stack-összetevő másodlagos verziója megváltozik.
* **Frissítések**. VMware stack-összetevő főverziójának módosítása.

A CloudSimple kritikus biztonsági javítást végez, amint a VMware-ről elérhetővé válik. A CloudSimple egy héten belül kivezeti a biztonsági javítást a privát felhőalapú környezetekhez.

A CloudSimple negyedévente karbantartási frissítéseket biztosít a VMware szoftver összetevőihez. Ha elérhetővé válik a VMware szoftver új főverziója, a CloudSimple együttműködik az ügyfelekkel, hogy koordinálja a megfelelő karbantartási időszakot a frissítéshez.  

## <a name="next-steps"></a>Következő lépések

* [CloudSimple-karbantartás és-frissítések](cloudsimple-maintenance-updates.md)
