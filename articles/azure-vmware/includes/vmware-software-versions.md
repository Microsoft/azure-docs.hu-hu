---
title: VMware-szoftverek verziói
description: Támogatott VMware-verziók az Azure VMware-megoldáshoz.
ms.topic: include
ms.date: 03/31/2021
ms.openlocfilehash: a6441b55bbc6a8f694c50bbf022a6a2ae52d60bf
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097908"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


Az Azure VMware-megoldás privát felhők fürtjének új üzemelő példányában használt VMware-verziók a következők:

| Szoftverek              |    Verzió   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6,7 U3l    | 
| ESXi                  |    6,7 U3l    | 
| vSAN                  |    6,7 U3l    |
| NSX – T <br />**Megjegyzés:** A NSX-T a NSX egyetlen támogatott verziója.               |      3.1.1     |


Új fürtök lettek hozzáadva egy meglévő privát felhőhöz, a jelenleg futó szoftververzió is érvényben van. További információ: a [VMware szoftver verziójának követelményei](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

A Private Cloud szoftvercsomag frissítései megőrzik a szoftvert a VMware-ből származó legújabb szoftvercsomag-kiadás egy verzióján belül. A Private Cloud Software-verziók eltérhetnek az egyes szoftver-összetevők legújabb verzióitól (ESXi, NSX-T, vCenter, vSAN). Az általános frissítési szabályzatokat és folyamatokat a [saját Felhőbeli frissítések és verziófrissítések](../concepts-upgrades.md)című cikkben ismertetett Azure VMware Solution platform szoftverhez tekintheti meg.

