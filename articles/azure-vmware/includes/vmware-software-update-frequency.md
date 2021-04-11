---
title: VMware szoftverfrissítés gyakorisága
description: A VMware-szoftverfrissítés támogatott gyakorisága az Azure VMware megoldásban.
ms.topic: include
ms.date: 03/22/2021
ms.openlocfilehash: a3290ed704b493fae3e86223857ff9f1c5617f1a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869863"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->

A Microsoft feladata a VMware-szoftverek (ESXi, vCenter, PSC és NXS) életciklus-kezelése az Azure VMware-megoldás saját felhőben.

A privát felhőalapú szoftverek olyan ütemterv szerint frissülnek, amely nyomon követi a szoftveres csomag VMware-ről való kiadását. A saját felhő nem igényel állásidőt a frissítésekhez.

A Private Cloud szoftvercsomag frissítései megőrzik a szoftvert a VMware-ből származó legújabb szoftvercsomag-kiadás egy verzióján belül. A Private Cloud Software-verziók eltérhetnek az egyes szoftver-összetevők legújabb verzióitól (ESXi, NSX-T, vCenter, vSAN).

A szoftverfrissítések a következők:

- **Javítások** – a VMware által kiadott biztonsági javítások vagy hibajavítások
- **Frissítések** – VMware stack-összetevő másodlagos verziójának módosítása
- **Frissítések** – VMware stack-összetevő főverziójának módosítása

A Microsoft egy kritikus biztonsági javítást tesztel, amint elérhetővé válik a VMware-ről.

A dokumentált VMware megkerülő megoldás a megfelelő javítás telepítése helyett kerül megvalósításra, amíg a következő ütemezett frissítések telepítve nem lesznek. 
