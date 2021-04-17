---
title: Az Oracle-hez való BareMetal javítási szempontjai
description: Ismerje meg az operációs rendszer/kernel javításának szempontjait az Oracle-hez használt BareMetal-infrastruktúra esetében.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 5af713c776def8af558531c84013b221a8d30087
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559099"
---
# <a name="patching-considerations-for-baremetal-for-oracle"></a>Az Oracle-hez való BareMetal javítási szempontjai

Ebben a cikkben az Oracle-hez használt BareMetal-infrastruktúra fontos operációsrendszer-/kerneljavítási szempontjait fogjuk figyelembe venni.

A megfelelő hálózati teljesítmény és rendszerstabilitás érdekében telepítse az eNIC- és fNIC-illesztőprogramok operációs rendszerre vonatkozó verzióját az alábbi kompatibilitási táblázatban látható módon. 

A kiszolgálókat kompatibilis verziókkal kézbesíti a rendszer az ügyfeleknek. Az operációs rendszer (OS)/kerneljavítás során azonban az illesztőprogramok visszaállíthatóak az alapértelmezett illesztőprogram-verziókra. Ezért győződjön meg arról, hogy a megfelelő illesztőprogram-verzió fut az operációs rendszer/kernel javítási műveleteit követően.

| Operációs rendszer szállítója | Operációsrendszer-csomag verziója | Belső vezérlőprogram verziója | eNIC-illesztőprogram | fNIC-illesztőprogram |
| --- | --- | --- | --- | --- |
| Red Hat | RHEL 7.6 | 3.2.3i | 2.3.0.53 | 1.6.0.34 |
| Red Hat | RHEL 7.6 | 4.1.1b | 2.3.0.53 | 1.6.0.34 |

## <a name="next-steps"></a>Következő lépések

Ismerje meg az Oracle-hez való BareMetal Ethernet-konfigurációját.

> [!div class="nextstepaction"]
> [Az Oracle-hez való BareMetal Ethernet-konfigurációja](oracle-baremetal-ethernet.md)

