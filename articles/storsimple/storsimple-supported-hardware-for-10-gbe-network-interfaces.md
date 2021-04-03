---
title: Hardver a StorSimple 10 GbE felületek számára | Microsoft Docs
description: A StorSimple-eszköz 10 GbE hálózati adaptere számára támogatott kisméretű (SFP) adóvevők, kábelek és kapcsolók használatát ismerteti.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: df8d40c7-f5ad-4f84-93eb-779fbd5f7243
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli
ms.openlocfilehash: fd30c7ec15eadd3d7945349a8aae4ac824bfdc7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94954086"
---
# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>A StorSimple-eszköz 10 GbE hálózati adapterének támogatott hardverei

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk a Microsoft Azure StorSimple eszközzel használható kiegészítő hardverekkel kapcsolatos információkat tartalmaz.

## <a name="list-of-devices-tested-by-microsoft"></a>A Microsoft által tesztelt eszközök listája
A Microsoft a következő kisméretű (SFP) adóvevők, kábelek és kapcsolók tesztelésével biztosítja, hogy optimálisan működjenek az eszközökkel. (A következő táblázatok frissülnek az új hardver tesztelésekor.)

### <a name="sfp-transceivers"></a>SFP + adóvevők
| Gyártó | Modellezés |
| --- | --- |
| Cisco |SFP-10G-SR |

### <a name="cables"></a>Kábelek
| S. Nem. | Gyártó | Modellezés |
| --- | --- | --- |
| 1. |Cisco |SFP-H10GB-CU1M |
| 2. |Cisco |SFP-H10GB-CU2M |
| 3. |Cisco |SFP-H10GB-CU3M |
| 4. |Tripp-Lite |N820-05M (OM3) |

### <a name="switches"></a>Switchek
| S. Nem. | Gyártó | Modellezés |
| --- | --- | --- |
| 1. |Cisco |N3K-C3172PQ-10GE |
| 2. |Cisco |N3K-C3048-ZM-F |
| 3. |Cisco |N5K-C5596UP-FA |

## <a name="list-of-devices-tested-in-the-field"></a>A mezőben tesztelt eszközök listája
Ez a szakasz azoknak az eszközöknek a listáját tartalmazza, amelyeket a StorSimple ügyfelek a mezőben sikeresen üzembe helyezett. Ezeket a Microsoft nem tesztelte, de valószínűleg működik a StorSimple-eszközzel.

| Paraméter | Érték |
| --- | --- |
| Váltás a make |Juniper |
| Modell váltása |ex4550-32F |
| Operációs rendszer verziójának váltása |JunOs 12.3 R 9.4 |
| Panel modellje |Bevezető portok (PIC 0) |
| Adó-vevő |Juniper |
| Adóvevő-modell |Cikkszám 740-021308 <br></br> Cikkszám 740-030658 |
| Adóvevő belső vezérlőprogram-verziója |Rev 01 0,0-es verzió (jelentett) |
| Kábel modell |Duplex jumper LC/LC 50/125μ, OM3, LSZH |
| StorSimple modell |8600 |
| StorSimple szoftver verziója |6.3.9600.17491 |

## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Az OEM Provider által tesztelt eszközök listája (Mellanox)
A Mellanox a következő kisméretű (SFP) adóvevők, kábelek és kapcsolók tesztelésével biztosítja, hogy optimálisan működjenek a Mellanox hálózati adapterekkel, például a StorSimple-eszköz 10 GbE hálózati adapterével.

### <a name="cables-and-modules-supported-by-mellanox"></a>A Mellanox által támogatott kábelek és modulok
A következő táblázat felsorolja a Mellanox által támogatott kábeleket és modulokat. Ezeket a Microsoft nem tesztelte, de valószínűleg működik a StorSimple-eszközzel.

| S. Nem. | Sebesség | Modellezés | Leírás | Gyártó |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |CAB-SFP-SFP-1M |passzív réz kábel SFP + 10 GB/s 1M |Arista |
| 2. |10 GbE |CAB-SFP-SFP-2M |passzív réz kábel SFP + 10 GB/s 2m |Arista |
| 3. |10 GbE |CAB-SFP-SFP-3M |passzív réz kábel SFP + 10 GB/s 3m |Arista |
| 4. |10 GbE |CAB-SFP-SFP-5M |passzív réz kábel SFP + 10 GB/s 5m |Arista |
| 5. |10 GbE |Cisco SFP – H10GBCU1M |Cisco SFP + kábel |Cisco |
| 6. |10 GbE |Cisco SFP – H10GBCU3M |Cisco SFP + kábel |Cisco |
| 7. |10 GbE |Cisco SFP – H10GBCU5M |Cisco SFP + kábel |Cisco |
| 8. |10 GbE |J9281B HP X242 10G |SFP + – SFP + 1M Direct – vörösréz kábel csatlakoztatása |HP |
| 9. |10 GbE |455883 – B21 HP BLc |10Gb SR SFP + opt |HP |
| 10. |10 GbE |455886 – B21 HP BLc |10Gb LR SFP + opt |HP |
| 11. |10 GbE |487649 – B21 HP BLc |SFP + 0,5 m 10 GbE vörösréz kábel |HP |
| 12. |10 GbE |487652 – B21 HP BLc |SFP + 1M 10 GbE vörösréz kábel |HP |
| 13. |10 GbE |487655 – B21 HP BLc |SFP + 3m 10 GbE vörösréz kábel |HP |
| 14. |10 GbE |487658 – B21 HP BLc |SFP + 7m 10 GbE réz kábel |HP |
| 15. |10 GbE |537963 – B21 HP BLc |SFP + 5m 10 GbE vörösréz kábel |HP |
| 16. |10 GbE |AP784A HP |3m C sorozatú passzív réz SFP + kábel |HP |
| 17. |10 GbE |AP785A HP |5m C sorozatú passzív réz SFP + kábel |HP |
| 18. |10 GbE |AP818A HP |1M B sorozatú aktív réz SFP + kábel |HP |
| 19. |10 GbE |AP819A HP |3m B sorozatú aktív réz SFP + kábel |HP |
| 20. |10 GbE |J9150A HP |X132 10G SFP + LC SR adóvevő |HP |
| 21. |10 GbE |J9151A HP |X132 10G SFP + LC LR adóvevő |HP |
| 22. |10 GbE |J9283B HP |X242 10G SFP + SFP + 3m DAC-kábel |HP |
| 23. |10 GbE |J9285B HP |X242 10G SFP + SFP + 7m DAC-kábel |HP |
| 24. |10 GbE |JD095B HP |X240 10G SFP + SFP + 0.65 m DAC-kábel |HP |
| 25. |10 GbE |JD096B HP |X240 10G SFP + SFP + 1,2 m DAC-kábel |HP |
| 26. |10 GbE |JD097B HP |X240 10G SFP + SFP + 3m Dr kábel |HP |
| 27. |10 GbE |MAM1Q00A – QSA Mellanox |QSFP – SFP + adapter |Mellanox-technológiák |
| 28. |10 GbE |MC2309124 – 006 MT |Passzív réz kábel 1x SFP + QSFP 10Gb/s 24awg 7m |Mellanox-technológiák |
| 29. |10 GbE |MC2309124-007 MT |Passzív réz kábel 1x SFP + QSFP 10Gb/s 24awg 7m |Mellanox-technológiák |
| 30. |10 GbE |MC2309130 – 003 MT |Passzív réz kábel 1x SFP + QSFP 10Gb/s 30awg 3m |Mellanox-technológiák |
| 31. |10 GbE |MC2309130 – 00A MT |Passzív réz kábel 1x SFP + QSFP 10Gb/s 30awg 0,5 m |Mellanox-technológiák |
| 32. |10 GbE |MC3309124 – 005 MT |Passzív vörösréz kábel 1x SFP + 10Gb/s 24awg 5m |Mellanox-technológiák |
| 33. |10 GbE |MC3309124-007 MT |Passzív vörösréz kábel 1x SFP + 10 GB/s 24awg 7m |Mellanox-technológiák |
| 34. |10 GbE |MC3309130 – 003 MT |Passzív vörösréz kábel 1x SFP + 10Gb/s 30awg 3m |Mellanox-technológiák |
| 35. |10 GbE |MC3309130 – 00A MT |Passzív vörösréz kábel 1x SFP + 10Gb/s 30awg 0,5 m |Mellanox-technológiák |

### <a name="switches-supported-by-mellanox"></a>A Mellanox által támogatott kapcsolók
A következő táblázat felsorolja a Mellanox által támogatott kapcsolókat. Ezeket a Microsoft nem tesztelte, de valószínűleg működik a StorSimple-eszközzel.

| S. Nem. | Sebesség | Modellezés | Leírás | Gyártó |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |516733 – B21 |HP-előgörbe 6120XG 10 GbE Ethernet panel kapcsoló |HP |
| 2. |10 GbE |538113 – B21 |HP 10 GbE Pass-Through modul (PTM) |HP |
| 3. |10 GbE |EN4093 |IBM PureFlex System Fabric EN4093 10 Gigabit skálázható kapcsoló modul |IBM |
| 4. |1 GbE |3020 |Cisco Catalyst 3020 1 GbE kapcsoló panel |Cisco |
| 5. |1 GbE |3020X |Cisco Catalyst 3020X 1 GbE kapcsoló panelje |Cisco |
| 6. |1 GbE |438030 – B21 |HP 1 GbE kapcsoló modul – GbE2c réteg 2/3 Ethernet panel kapcsoló |HP |
| 7. |1 GbE |6120G |HP-görbe 6120G/XG 1 GbE kapcsoló panelje |HP |

## <a name="next-steps"></a>Következő lépések
[További információ a StorSimple hardveres összetevőiről és állapotáról](./storsimple-8000-monitor-hardware-status.md).