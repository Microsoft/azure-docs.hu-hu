---
title: Azure VMware-megoldás CloudSimple – CloudSimple-szolgáltatások biztonsága
description: Ismerteti a CloudSimple-szolgáltatások biztonságának közös felelősségi modelljeit
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d62eec72a884470ade03897a0533e62c6f1b0f71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97898793"
---
# <a name="cloudsimple-security-overview"></a>A CloudSimple biztonsági áttekintése

Ez a cikk áttekintést nyújt arról, hogy a CloudSimple szolgáltatás, az infrastruktúra és az adatközpont hogyan valósítja meg a biztonságot az Azure VMware-megoldáson. Megismerheti az adatvédelem és a biztonság, a hálózati biztonság, valamint a biztonsági rések és javítások kezelését.

## <a name="shared-responsibility"></a>Megosztott feladatkörök

A CloudSimple által nyújtott Azure VMware-megoldás megosztott felelősségi modellt használ a biztonsághoz. A Felhőbeli megbízható biztonság az ügyfelek és a Microsoft szolgáltatói felelősségével érhető el. A felelősségi mátrix nagyobb biztonságot nyújt, és kiküszöböli az egyes meghibásodási pontokat.

## <a name="azure-infrastructure"></a>Azure-infrastruktúra

Az Azure-infrastruktúra biztonsági szempontjai tartalmazzák az adatközpontok és a berendezések helyét.

### <a name="datacenter-security"></a>Adatközpont biztonsága

A Microsoft egy teljes részleggel rendelkezik, amely az Azure-t támogató fizikai létesítmények tervezésével, létrehozásával és működtetésével foglalkozik. A csapat a legkorszerűbb fizikai biztonság fenntartásával lett befektetve. A fizikai biztonsággal kapcsolatos részletekért tekintse meg az [Azure-létesítményeket, a telephelyeket és a fizikai biztonságot](../security/fundamentals/physical-security.md).

### <a name="equipment-location"></a>Berendezések helye

A privát felhőket futtató operációs rendszer nélküli hardveres berendezések az Azure-adatközpont helyein futnak.  Azok a ketrecek, amelyeken a berendezés van, biometrikus alapú kéttényezős hitelesítést igényelnek a hozzáféréshez.

## <a name="dedicated-hardware"></a>Dedikált hardver

A CloudSimple szolgáltatás részeként minden CloudSimple-ügyfél dedikált operációs rendszer nélküli gazdagépeket kap, amelyek helyi csatlakoztatott lemezekkel rendelkeznek, amelyek fizikailag el vannak különítve a többi bérlői hardvertől. VSAN-t futtató ESXi hypervisor minden csomóponton. A csomópontokat az ügyfél dedikált VMware vCenter és NSX kezeli. Ha nem oszt meg hardvert a bérlők között, egy további elkülönítési réteget és biztonsági védelmet biztosít.

## <a name="data-security"></a>Adatbiztonság

Az ügyfelek továbbra is megőrzik az adatkezelést és a tulajdonjogot. A vásárlói adatkezelés az ügyfél feladata.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Inaktív adatok és a belső hálózatokon belüli adatok védelme

A privát felhőalapú környezetben lévő inaktív adatok esetében vSAN titkosítást is használhat. a vSAN titkosítás a VMware Certified külső kulcskezelő kiszolgálókkal (KMS) működik a saját virtuális hálózatában vagy a helyszínen.  Saját maga vezérli az adattitkosítási kulcsokat. A vSphere a privát felhőben lévő adatok esetében támogatja az adatok titkosítását a dróton keresztül az összes vmkernel-forgalomra (beleértve a vMotion-forgalmat is).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Adatvédelem a nyilvános hálózatokon való áthelyezéshez szükséges adatvédelemben

A nyilvános hálózatokon átmenő adatforgalom védelme érdekében létrehozhat IPsec és TLS VPN-alagutakat a privát felhők számára. Az általános titkosítási módszerek támogatottak, beleértve a 128 bájtos és a 256 bájtos AES-t. Az átvitt adatforgalom (beleértve a hitelesítést, a rendszergazdai hozzáférést és a vásárlói adatforgalmat) szabványos titkosítási mechanizmusokkal (SSH, TLS 1,2 és biztonságos RDP) van titkosítva. A bizalmas adatokat továbbító kommunikáció a szabványos titkosítási mechanizmusokat használja.

### <a name="secure-disposal"></a>Biztonságos kivezetés

Ha a CloudSimple szolgáltatás lejár vagy leáll, Ön felelős az adatai eltávolításához vagy törléséhez. A CloudSimple együttműködik Önnel, hogy törölje vagy visszaállítsa az összes vásárlói adatát az ügyfél-szerződésben foglaltak szerint, kivéve, ha az alkalmazandó jogszabályok a személyes adat egy részét vagy egészét megőrzik a CloudSimple. Ha a személyes adatok megőrzéséhez szükséges, a CloudSimple archiválja az adatait, és ésszerű mértékeket valósít meg, hogy megakadályozza a vásárlói adatok további feldolgozását.

### <a name="data-location"></a>Adatok helye

A privát felhők beállításakor ki kell választania azt az Azure-régiót, ahol üzembe szeretné helyezni őket. A VMware virtuális gépek adatait nem helyezi át az adott fizikai adatközpontból, kivéve, ha adatáttelepítést vagy telephelyen kívüli biztonsági mentést végez. Az igényeknek megfelelően több Azure-régióban is üzemeltetheti a számítási feladatokat, és tárolhatja azokat.

A privát Felhőbeli Hyper-konvergált csomópontokban rezidens ügyféladatokat nem a bérlői rendszergazda kifejezett művelete nélkül, hanem a telephelyen halad át. Az Ön felelőssége, hogy a számítási feladatokat nagyon is elérhető módon implementálja.

### <a name="data-backups"></a>Adatok biztonsági mentése

A CloudSimple nem készít biztonsági mentést vagy archiválja az ügyféladatokat. A CloudSimple a vCenter és a NSX adatok rendszeres biztonsági mentését végzi a felügyeleti kiszolgálók magas rendelkezésre állásának biztosítása érdekében. A biztonsági mentést megelőzően az összes adattitkosítás a vCenter-forrásban a VMware API-k használatával történik. A titkosított adatforgalom az Azure-blobban van elküldve és tárolva. A biztonsági mentések titkosítási kulcsait egy, az Azure CloudSimple virtuális hálózatán futó, szigorúan biztonságos CloudSimple felügyelt tároló tárolja.

## <a name="network-security"></a>Hálózati biztonság

A CloudSimple megoldás a hálózati biztonság rétegeire támaszkodik.

### <a name="azure-edge-security"></a>Azure Edge-biztonság

A CloudSimple szolgáltatások az Azure által biztosított alap hálózati biztonságra épülnek. Az Azure a rendellenes bejövő vagy kimenő forgalmi mintákhoz és az elosztott szolgáltatásmegtagadási (DDoS) támadásokhoz kapcsolódó hálózati támadások észleléséhez és időben történő megválaszolásához nyújt védelmet részletes technikákat. Ez a biztonsági ellenőrzés a CloudSimple által fejlesztett saját felhőalapú környezetekre és a vezérlő síkja szoftverre vonatkozik.

### <a name="segmentation"></a>Szegmentálás

A CloudSimple szolgáltatás logikailag elkülöníti a 2. rétegbeli hálózatokat, amelyek korlátozzák a saját privát hálózatokhoz való hozzáférést a saját felhőalapú környezetében. A saját felhőalapú hálózatait tűzfal használatával tovább védetté teheti. A CloudSimple-portál lehetővé teszi az EW és az NS hálózati forgalom szabályozását az összes hálózati forgalomra vonatkozóan, beleértve a belső felhőalapú forgalmat, a belső Felhőbeli forgalmat, az internetre irányuló általános forgalmat, valamint a helyszíni hálózati forgalmat az IPsec VPN-vagy ExpressRoute-kapcsolaton keresztül.

## <a name="vulnerability-and-patch-management"></a>Sebezhetőség és javítás kezelése

A CloudSimple feladata a felügyelt VMware-szoftverek (ESXi, vCenter és NSX) rendszeres biztonsági javítása.

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

Az ügyfelek az Azure-fiókban (az Azure AD-ben) a többtényezős hitelesítést vagy az SSO-t használják előnyben részesítettként. A Azure Portal a hitelesítő adatok újbóli megadása nélkül is elindíthatja a CloudSimple-portált.

A CloudSimple támogatja a saját Felhőbeli vCenter tartozó személyazonossági forrás opcionális konfigurációját. Használhatja [a helyszíni identitás forrását](set-vcenter-identity.md), egy új személyazonossági forrást a saját felhőhöz vagy az [Azure ad](azure-ad.md)-hez.

Alapértelmezés szerint az ügyfelek a privát felhőben a vCenter napi műveleteihez szükséges jogosultságokat kapják meg. Ez a jogosultsági szint nem tartalmaz rendszergazdai hozzáférést a vCenter. Ha átmenetileg szükség van a rendszergazdai hozzáférésre, akkor a rendszergazdai feladatok elvégzése után a jogosultságokat korlátozott időtartamra is [kiterjesztheti](escalate-private-cloud-privileges.md) .
