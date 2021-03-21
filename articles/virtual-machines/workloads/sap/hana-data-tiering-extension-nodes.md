---
title: Az Azure-ban SAP HANA adatrétegek és bővítmények csomópontjai (nagyméretű példányok) | Microsoft Docs
description: Az Azure-ban SAP HANA adatrétegek és bővítmények csomópontjai (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: eb780d10996cb22f5e6fe5bc8889e897e8c3854d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666777"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>SAP HANA adatrétegek és kiterjesztésű csomópontok használata

Az SAP támogatja a különböző SAP NetWeaver-kiadások és a SAP BW/4HANA SAP BWához szükséges adatrétegek modellezését. Az adatrétegek modelljével kapcsolatos további információkért tekintse meg az SAP [-dokumentum SAP BW/4HANA és a SAP BW a HANA-ben SAP HANA kiterjesztésű csomópontokkal](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#)című témakört.
A HANA nagyméretű példányával a gyakori kérdések és az SAP-blog dokumentumaiban a SAP HANA bővítmények csomópontjainak 1. kapcsolóját használhatja. Az Option-2 konfigurációk a következő HANA Large instance SKU-val állíthatók be: S72m, S192, S192m, S384 és S384m. 

Ha megtekinti a dokumentációt, előfordulhat, hogy az előny nem látható azonnal. Ha azonban megtekinti az SAP-méretezési irányelveket, a-1 és a-2 SAP HANA kiterjesztésű csomópontok lehetőségével megtekintheti az előnyt. Például:

- SAP HANA Méretezési irányelvek esetében általában a memóriának kell megdupláznia az adatmennyiséget. Ha a SAP HANA-példányt a forró adatokkal futtatja, akkor az adatokkal kitöltött memória 50%-a vagy annál kevesebbnek kell lennie. A memória hátralévő része ideális SAP HANA a munkája elvégzéséhez.
- Ez azt jelenti, hogy egy HANA nagyméretű példány S192 egysége 2 TB memóriával rendelkezik, és egy SAP BW adatbázist futtat, és csak 1 TB adatmennyiséggel rendelkezik.
- Ha a-1 lehetőség további SAP HANA kiterjesztési csomópontját használja, a nagyméretű HANA-példány S192 is, akkor további 2 TB-os kapacitást biztosít az adatmennyiséghez. A 2. lehetőség konfigurációjában további 4 TB-nyi meleg adatmennyiséget kap. A gyors elérésű csomóponthoz képest a "meleg" kiterjesztésű csomópont teljes memóriájának kapacitása használható az 1. lehetőséghez tartozó adattároláshoz. Duplán a memória felhasználható adatmennyiséghez az Option-2 SAP HANA a bővítmény csomópontjának konfigurációjában.
- Az adatokhoz 3 TB-os kapacitást, az 1. lehetőséghez pedig a 1:2-es meleg-meleg arányt használhatja. 5 TB adat és egy 1:4-es arány az Option-2 kiterjesztésű csomópont-konfigurációval.

Minél nagyobb az adatmennyiség a memóriához képest, annál nagyobb az esélye, hogy a kért meleg adatmennyiséget a lemezes tárolás tárolja.

**Következő lépések**
- Tekintse át [SAP HANA (nagyméretű példányok) architektúráját az Azure-](hana-architecture.md) ban