---
title: Microsoft Azure SAP-minősítések | Microsoft Docs
description: Az SAP aktuális konfigurációinak és tanúsítványának frissített listája az Azure platformon.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/21/2020
ms.author: juergent
ms.custom: ''
ms.openlocfilehash: 76f67204b41a36d10541974e78a22f7d065e144b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669702"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Microsoft Azureon futó SAP-tanúsítványok és-konfigurációk

Az SAP és a Microsoft hosszú múltra tekint át egy erős partneri együttműködésben, amely kölcsönös előnyökkel jár az ügyfelek számára. A Microsoft folyamatosan frissíti a platformját, és új hitelesítési adatokat küld az SAP-nek, hogy Microsoft Azure a legjobb platform, amelyre az SAP-munkaterheléseket futtatni kívánja. Az alábbi táblázatok az Azure által támogatott konfigurációkat és a növekvő SAP-tanúsítványok listáját ismertetik. Ez a lista egy áttekintő lista, amely itt és a hivatalos SAP-listákból is eltérhet. A részletes információkkal kapcsolatos tudnivalókat az Azure-beli [üzembe helyezést támogató SAP-szoftvereket](./sap-supported-product-on-azure.md) ismertető cikkben találja.

## <a name="sap-hana-certifications"></a>SAP HANA minősítések
Hivatkozik

- [SAP HANA Certified IaaS platformot](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) a natív Azure-beli virtuális gépek és a HANA nagyméretű példányok SAP HANA támogatásához.

| SAP-termék | Támogatott operációs rendszer | Azure-ajánlatok |
| --- | --- | --- |
| SAP HANA fejlesztői kiadás (beleértve a HANA ügyfélszoftvert is, amely SQLODBC, csak ODBO-Windows, ODBC, JDBC illesztőprogramok, HANA Studio és HANA Database) | Red Hat Enterprise Linux, SUSE Linux Enterprise | D sorozatú VM-család |
| Business One on HANA | SUSE Linux Enterprise | DS14_v2, M32ts, M32ls, M64ls, M64s <br /> [SAP HANA Certified IaaS platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | A GS5 ellenőrzött rendelkezésre állása. Teljes körű támogatás a M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2, <br /> SAP HANA az Azure-ban (nagyméretű példányok) [SAP HANA Certified IaaS platformon](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Suite on HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA az Azure-ban (nagyméretű példányok) [SAP HANA tanúsított IaaS platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| HANA Enterprise a BW-hez, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA az Azure-ban (nagyméretű példányok) [SAP HANA tanúsított IaaS platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA az Azure-ban (nagyméretű példányok) <br /> [SAP HANA Certified IaaS platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

Vegye figyelembe, hogy az SAP a "fürtözés" kifejezést használja [SAP HANA Certified IaaS platformon](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) a "kibővíthető" szinonimájának, és nem a magas rendelkezésre állás "fürtözéshez".

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver-minősítések
A Microsoft Azure a következő SAP-termékekhez van hitelesítve, a Microsoft és az SAP teljes körű támogatásával.
Hivatkozik

- [1928533 – SAP-alkalmazások az Azure-on: támogatott termékek és Azure](https://launchpad.support.sap.com/#/notes/1928533) -beli virtuálisgép-típusok az összes SAP NetWeaver-alapú alkalmazáshoz, beleértve az SAP-TREX, az SAP-LiveCache és az SAP Content Servert. És minden adatbázis, kivéve a SAP HANA.


| SAP-termék | Vendég operációs rendszer | RDBMS | Virtuális gépek típusai |
| --- | --- | --- | --- |
| SAP Business Suite szoftver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (csak Windows és Oracle Linux), DB2, SAP bemutató |A5-től A11-ig, a D14, a DS11 a DS15_v2 DS11_v2 DS14, a GS1, a GS5, a M64s, a D64s_v3 D2s_v3 a D2as_v4 D64as_v4, E2s_v3 a E64s_v3, E2as_v4 D11, M64ms, M128s, M128ms, M64ls, M32ls, M32ts,, E64as_v4, M208s_v2, M208ms_v2, M416s_v2 |
| SAP Business all-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (csak Windows és Oracle Linux), DB2, SAP bemutató |A5-től A11-ig, a D14, a DS11 a DS15_v2 DS11_v2 DS14, a GS1, a GS5, a M64s, a D64s_v3 D2s_v3 a D2as_v4 D64as_v4, E2s_v3 a E64s_v3, E2as_v4 D11, M64ms, M128s, M128ms, M64ls, M32ls, M32ts,, E64as_v4, M208s_v2, M208ms_v2, M416s_v2 |
| SAP BusinessObjects BI | Windows |N/A |A5-től A11-ig, a D14, a DS11 a DS15_v2 DS11_v2 DS14, a GS1, a GS5, a M64s, a D64s_v3 D2s_v3 a D2as_v4 D64as_v4, E2s_v3 a E64s_v3, E2as_v4 D11, M64ms, M128s, M128ms, M64ls, M32ls, M32ts,, E64as_v4, M208s_v2, M208ms_v2, M416s_v2 |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (csak Windows és Oracle Linux), DB2, SAP bemutató |A5-től A11-ig, a D14, a DS11 a DS15_v2 DS11_v2 DS14, a GS1, a GS5, a M64s, a D64s_v3 D2s_v3 a D2as_v4 D64as_v4, E2s_v3 a E64s_v3, E2as_v4 D11, M64ms, M128s, M128ms, M64ls, M32ls, M32ts,, E64as_v4, M208s_v2, M208ms_v2, M416s_v2 |

## <a name="other-sap-workload-supported-on-azure"></a>Az Azure-ban támogatott egyéb SAP-munkaterhelések

| SAP-termék | Vendég operációs rendszer | RDBMS | Virtuális gépek típusai |
| --- | --- | --- | --- |
| SAP Business One on SQL Server | Windows  | SQL Server | Minden NetWeaver Certified VM-típus<br /> [SAP-Megjegyzés #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BITES 10,01 MS SP08 | Windows és Linux | | Minden NetWeaver Certified VM-típus<br /> SAP-Megjegyzés #2451795 |
| SAP Business Objects BI platform | Windows és Linux | | SAP-Megjegyzés #2145537 |
| SAP Data Services 4,2 | | | SAP-Megjegyzés #2288344 |
| SAP Hybris kereskedelmi platform  | Windows | SQL Server, Oracle | Minden NetWeaver Certified VM-típus <br /> [A Hybris dokumentációja](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP Hybris kereskedelmi platform  | SLES 12 vagy újabb | SAP HANA | Minden NetWeaver Certified VM-típus <br /> [A Hybris dokumentációja](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP Hybris kereskedelmi platform  | RHEL 7 vagy újabb verzió | SAP HANA | Minden NetWeaver Certified VM-típus <br /> [Hybris dokumentáció]https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP (Hybris) kereskedelmi platform 1811 és újabb verziók  | Windows, SLES vagy RHEL | ADATBÁZIS SQL Azure | Minden NetWeaver Certified VM-típus <br /> [A Hybris dokumentációja](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html) |
