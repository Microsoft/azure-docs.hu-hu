---
title: Az Azure vendég operációs rendszerre vonatkozó támogatás és a nyugdíjazási szabályzat útmutatója | Microsoft Docs
description: Információt nyújt arról, hogy a Microsoft milyen támogatást biztosít a Cloud Services által használt Azure vendég operációs rendszerhez.
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 919dd781-4dc6-4e50-bda8-9632966c5458
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 9/20/2017
ms.author: raiye
ms.openlocfilehash: fbe9b3379799fe3cf0a56d921ab257bc87606ca9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "68945452"
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Az Azure vendég operációs rendszer támogatása és a nyugdíjazási szabályzat
Az oldalon található információk az Azure vendég operációs rendszer ([vendég operációs](cloud-services-guestos-update-matrix.md)rendszer) Cloud Services Worker és a web roles (Péter) szolgáltatáshoz kapcsolódnak. Virtual Machinesra (IaaS) nem vonatkozik.

A Microsoft közzétett [támogatási szabályzattal rendelkezik a vendég operációs rendszerhez](https://support.microsoft.com/gp/azure-cloud-lifecycle-faq). Az éppen olvasott lap leírja, hogyan valósítja meg a házirendet.

A szabályzat

1. A Microsoft **legalább a vendég operációs rendszer legújabb két családját** fogja támogatni. A család kivonása után az ügyfelek a hivatalos nyugdíjazás dátumától számítva 12 hónapig érvényesek egy újabb támogatott vendég operációsrendszer-családra.
2. A Microsoft **legalább a támogatott vendég operációs rendszer családjának legújabb két verzióját** fogja támogatni.
3. A Microsoft **legalább az Azure SDK legújabb két verzióját** fogja támogatni. Az SDK egy verziójának kivonása után az ügyfeleknek 12 hónapig kell eljutniuk a hivatalos nyugdíjazás dátumától a újabb verzióra való frissítéshez.

Időnként több mint két családot vagy kiadást lehet támogatni. A vendég operációs rendszer hivatalos támogatási információi megjelennek az [Azure vendég operációs rendszer kiadásait és az SDK kompatibilitási mátrixát](cloud-services-guestos-update-matrix.md).

## <a name="when-a-guest-os-version-is-retired"></a>A vendég operációs rendszer verziójának kivonásakor
A rendszer minden hónapban új vendég operációsrendszer- **verziót** vezet be a legújabb MSRC-frissítések beépítéséhez. A rendszeres havi frissítések miatt a vendég operációs rendszer verziója általában 60 nappal a megjelenése után le van tiltva. Ez a tevékenység legalább két vendég operációsrendszer-verziót tart fenn minden használatra elérhető család számára.

### <a name="process-during-a-guest-os-family-retirement"></a>Folyamat a vendég operációs rendszer családjának kivonulása során
A nyugdíjazás bejelentése után az ügyfeleknek 12 hónapos "áttérési" időszakra van lehetőségük, mielőtt a régi családot hivatalosan eltávolítottak a szolgáltatásból. Ez az áttérési idő a Microsoft belátása szerint bővíthető. A frissítések az [Azure vendég operációs rendszer kiadásaiban és az SDK-kompatibilitási mátrixban](cloud-services-guestos-update-matrix.md)lesznek közzétéve.

A fokozatos nyugdíjazási folyamat hat (6) hónapot kezd az átmeneti időszakba. Ebben az időszakban:

1. A Microsoft értesíti az ügyfeleket a nyugdíjazásról.
2. Az Azure SDK újabb verziója nem támogatja a kivont vendég operációsrendszer-családot.
3. Cloud Services új központi telepítései és újratelepítései nem engedélyezettek a kivont családban

A Microsoft továbbra is bevezeti az új vendég operációs rendszer verzióját, amely a legújabb MSRC-frissítéseket tartalmazza, az átmeneti időszak utolsó napjáig ("lejárati dátum"). A lejárati dátum Cloud Services továbbra is fut lesz az Azure SLA-ban. A Microsoft saját belátása szerint kényszerítheti a szolgáltatások frissítését, törlését vagy leállítását ezen időpont után.

### <a name="process-during-a-guest-os-version-retirement"></a>Folyamat a vendég operációs rendszer verziójának kivonulása során
Ha az ügyfelek a vendég operációs rendszert automatikusan frissítik, soha nem kell aggódniuk a vendég operációs rendszer verzióinak kezelésével kapcsolatban. Mindig a vendég operációs rendszer legújabb verzióját fogják használni.

A vendég operációsrendszer-verzióit minden hónapban kiadjuk. A normál kiadások aránya miatt minden verzió rögzített élettartammal rendelkezik.

A 60 napon belül a verzió "*Letiltva*". A "Letiltva" érték azt jelenti, hogy a verzió el lesz távolítva a portálról. A verzió már nem állítható be a CSCFG konfigurációs fájlból. A meglévő telepítések továbbra is futnak. A meglévő központi telepítések esetében azonban a rendszer nem engedélyezi az új központi telepítéseket, valamint a kódok és a konfigurációs frissítések telepítését.

A "Letiltva", a vendég operációs rendszer verziója "lejár", és a lejárt verziót futtató telepítések biztonsági és sebezhetőségi problémákra vannak kitéve. Általánosságban elmondható, hogy a lejárati idő kötegekben történik, így a lejáratig tartó időszak változhat.

Azok az ügyfelek, akik konfigurálja a szolgáltatásaikat a vendég operációs rendszer manuális frissítésére, gondoskodni kell arról, hogy a szolgáltatásaik egy támogatott vendég operációs rendszeren fussanak. Ha egy szolgáltatás úgy van konfigurálva, hogy automatikusan frissítse a vendég operációs rendszert, akkor az alapul szolgáló platform biztosítja a megfelelőséget, és a legújabb vendég operációs rendszerre fog frissíteni.

Ezek az időszakok a Microsoft saját belátása szerint továbbra is megtarthatók az ügyfelek átállásának megkönnyítésére. A rendszer a módosításokat az [Azure vendég operációs rendszer kiadásaiban és az SDK-kompatibilitási mátrixban](cloud-services-guestos-update-matrix.md)küldi el.

### <a name="notifications-during-retirement"></a>Értesítések a nyugdíjazás során
* **Családi nyugdíjazás** <br>A Microsoft a blogbejegyzések és a portál értesítését fogja használni. Azok az ügyfelek, akik továbbra is kivont vendég operációsrendszer-családot használnak, közvetlen kommunikáción (e-mailben, portálon, telefonhíváson) keresztül kapnak értesítést a szolgáltatás-rendszergazdák számára. Minden módosítás az [Azure vendég operációs rendszer kiadásaiban és az SDK-kompatibilitási mátrixban](cloud-services-guestos-update-matrix.md)lesz közzétéve.
* **Verzió-nyugdíjazás** <br>Az összes változás és a dátumok bekerülnek az [Azure vendég operációs rendszer kiadásaiba és az SDK kompatibilitási mátrixba](cloud-services-guestos-update-matrix.md), beleértve a kiadást, a letiltást és a lejáratot is. A szolgáltatások rendszergazdái e-maileket kapnak, ha az üzemelő példányok letiltott vendég operációs rendszeren vagy termékcsaládon futnak. Az e-mailek időzítése eltérő lehet. Általánosságban elmondható, hogy legalább egy hónappal a használat előtt, de ez az időzítés nem hivatalos SLA.

## <a name="frequently-asked-questions"></a>Gyakori kérdések
**Hogyan csökkentheti az áttelepítés hatásait?**

Javasoljuk, hogy a Cloud Services megtervezéséhez használja a legújabb vendég operációsrendszer-családot.

1. Kezdje meg az áttelepítés megtervezését egy újabb családba.
2. Állítsa be az ideiglenes tesztelési üzembe helyezést az új családon futó Cloud Service teszteléséhez.
3. Állítsa a vendég operációs rendszer verzióját **automatikusra** (osVersion = * a [. cscfg](cloud-services-model-and-package.md#cscfg) fájlban), hogy az új vendég operációs rendszer verzióra való áttelepítés automatikusan megtörténjen.

**Mi a teendő, ha a webalkalmazásom mélyebb integrációt igényel az operációs rendszerrel?**

Ha a webalkalmazás-architektúra az operációs rendszer alapjául szolgáló funkcióktól függ, használja a platform által támogatott funkciókat, például az [indítási feladatokat](cloud-services-startup-tasks.md) vagy más bővíthetőségi mechanizmusokat. Azt is megteheti, hogy az [Azure Virtual Machines](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS-infrastruktúra) szolgáltatást is használja, ahol Ön felelős a mögöttes operációs rendszer fenntartásáért.

## <a name="next-steps"></a>Következő lépések
Tekintse át a [vendég operációs rendszer legújabb kiadásait](cloud-services-guestos-update-matrix.md).
