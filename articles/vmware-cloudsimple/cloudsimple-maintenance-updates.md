---
title: CloudSimple-karbantartás és-frissítések
titleSuffix: Azure VMware Solution by CloudSimple
description: Ismerteti a CloudSimple szolgáltatási folyamatát az ütemezett karbantartáshoz és frissítésekhez
author: sharaths-cs
ms.author: dikamath
ms.date: 03/09/2021
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1cbd708f6c1f5159655099d5d72900ab0c608147
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102522308"
---
# <a name="cloudsimple-maintenance-and-updates"></a>CloudSimple-karbantartás és-frissítések

A privát felhőalapú környezet úgy van kialakítva, hogy ne legyen egyetlen meghibásodási pont.

* Az ESXi-fürtök vSphere magas rendelkezésre állással (HA) vannak konfigurálva. A fürtök mérete legalább egy tartalék csomópont legyen a rugalmasság érdekében.
* A redundáns elsődleges tárterületet a vSAN biztosítja, amelynek legalább három csomóponttal kell rendelkeznie az egyetlen hiba elleni védelem biztosításához. a vSAN konfigurálható úgy, hogy magasabb rugalmasságot biztosítson a nagyobb fürtök számára.
* a vCenter, a PSC és a NSX Manager virtuális gépek RAID-10 tárolóval vannak konfigurálva a tárolási hibák elleni védelemhez. A virtuális gépek a vSphere és a hálózati hibák ellen védettek.
* Az ESXi-gazdagépek redundáns ventilátorokkal és hálózati adapterekkel rendelkeznek.
* A TOR-és gerinc-kapcsolók a rugalmasság biztosításához a HA párokban vannak konfigurálva.

A CloudSimple folyamatosan figyeli a következő virtuális gépeket az üzemidő és a rendelkezésre állás érdekében, és rendelkezésre állási SLA-kat biztosít:

* ESXi-gazdagépek
* vCenter
* PBB
* NSX-kezelő

A CloudSimple a következő folyamatos hibák figyelését is figyeli:

* Merevlemezek
* Fizikai NIC-portok
* Kiszolgálók
* Ventilátor
* Energiaellátás
* Switchek
* Portok váltása

Ha egy lemez vagy csomópont meghibásodik, a rendszer automatikusan hozzáadja az érintett VMware-fürthöz egy új csomópontot, amely azonnal visszaállítja az állapotot.

A CloudSimple biztonsági mentést készít, karbantartja és frissíti ezeket a VMware-elemeket a privát felhőkben:

* ESXi
* vCenter platform szolgáltatásai
* Vezérlő
* vSAN
* NSX

## <a name="back-up-and-restore"></a>Biztonsági mentés és visszaállítás

A CloudSimple biztonsági mentése a következőket tartalmazza:

* A vCenter, a PSC és a DVS szabályainak éjszakai növekményes biztonsági mentései.
* natív API-kat vCenter az összetevők biztonsági mentéséhez az alkalmazás rétegében.
* Automatikus biztonsági mentés a VMware felügyeleti szoftver frissítése vagy frissítése előtt.
* vCenter az adattitkosítást a forrásnál, mielőtt az adatátvitelt TLS 1.2 titkosított csatornán keresztül az Azure-ba viszi át. Az adattárolást egy olyan Azure-Blob tárolja, amelyben a régiókban replikálódik.

A visszaállítást [support Request](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)megnyitásával kérheti le.

## <a name="maintenance"></a>Karbantartás

A CloudSimple többféle típusú karbantartást végez.

### <a name="backendinternal-maintenance"></a>Háttér/belső karbantartás

Ez a karbantartás általában a fizikai eszközök újrakonfigurálását vagy a szoftverfrissítések telepítését foglalja magában. Ez nem befolyásolja a kiszolgált eszközök normál felhasználását. A redundáns hálózati adapterek és az egyes fizikai állványok, a normál hálózati forgalom és a privát Felhőbeli műveletek nem érintettek. A teljesítményre gyakorolt hatás csak akkor tapasztalható, ha a szervezet a teljes redundáns sávszélességet szeretné használni a karbantartási időszakban.

### <a name="cloudsimple-portal-maintenance"></a>CloudSimple-portál karbantartása

A CloudSimple-vezérlő síkja vagy az infrastruktúra frissítésekor bizonyos korlátozott szolgáltatási állásidőre van szükség. Jelenleg a karbantartási időközök lehetnek olyan gyakoriak, mint havonta egyszer. A gyakoriság várhatóan az idő múlásával csökken. A CloudSimple értesítést biztosít a portál karbantartásáról, és a lehető legrövidebb időn belül megtartja az intervallumot. A portál karbantartási időszaka alatt a következő szolgáltatások továbbra is hatással vannak a működésre:

* VMware felügyeleti sík és alkalmazások
* vCenter-hozzáférés
* Minden Hálózatkezelés és tárterület
* Minden Azure-forgalom

### <a name="vmware-infrastructure-maintenance"></a>VMware-infrastruktúra karbantartása

Időnként szükség van a VMware-infrastruktúra konfigurációjának módosítására.  Jelenleg ezek az intervallumok minden 1-2 hónapban előfordulhatnak, de a gyakoriság várhatóan az idő múlásával csökken. Az ilyen típusú karbantartás általában a CloudSimple-szolgáltatások normál fogyasztásának megszakítása nélkül végezhető el. A VMware karbantartási időköze során a következő szolgáltatások továbbra is hatással vannak a működésre:

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

A CloudSimple kritikus biztonsági javítást végez, amint a VMware-ről elérhetővé válik. 

A dokumentált VMware-megkerülő megoldások a megfelelő javítás telepítése helyett a következő ütemezett frissítések telepítésekor lesznek implementálva. 

## <a name="next-steps"></a>Következő lépések

[Munkaterhelési virtuális gépek biztonsági mentése a Veeam használatával](backup-workloads-veeam.md)
