---
title: VMware-szoftverek verziói
description: Támogatott VMware-verziók az Azure VMware-megoldáshoz.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 9a1781d163e67c2f9b6a4f756a616e59a969696f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462514"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


Az Azure VMware-megoldás privát felhők fürtjének új üzemelő példányában használt VMware-verziók a következők:

| Szoftverek              |    Verzió   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6,7 U3    | 
| ESXi                  |    6,7 U3    | 
| vSAN                  |    6,7 U3    |
| NSX – T <br />**Megjegyzés:** A NSX-T a NSX egyetlen támogatott verziója.               |      2.5     |


Új fürtök lettek hozzáadva egy meglévő privát felhőhöz, a jelenleg futó szoftververzió is érvényben van. További információ: a [VMware szoftver verziójának követelményei](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

A Private Cloud szoftvercsomag frissítései megőrzik a szoftvert a VMware-ből származó legújabb szoftvercsomag-kiadás egy verzióján belül. A Private Cloud Software-verziók eltérhetnek az egyes szoftver-összetevők legújabb verzióitól (ESXi, NSX-T, vCenter, vSAN). Az általános frissítési szabályzatokat és folyamatokat a [saját Felhőbeli frissítések és verziófrissítések](../concepts-upgrades.md)című cikkben ismertetett Azure VMware Solution platform szoftverhez tekintheti meg.

