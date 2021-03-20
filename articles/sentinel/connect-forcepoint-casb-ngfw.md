---
title: Forcepoint-termékek összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók a Forcepoint-termékek az Azure Sentinelhez.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: eb099a786a84f9b7d0a6f0dc6e6df9c3459af295
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "77588229"
---
# <a name="connect-your-forcepoint-products-to-azure-sentinel"></a>A Forcepoint-termékek összekapcsolhatók az Azure Sentinel-vel

> [!IMPORTANT]
> A Forcepoint Products adatösszekötő az Azure Sentinel szolgáltatásban jelenleg nyilvános előzetes verzióban érhető el. Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Ez a cikk azt ismerteti, hogyan csatlakoztathatók a Forcepoint-termékek az Azure Sentinelhez. 

A Forcepoint-alapú adatösszekötők lehetővé teszik a Forcepoint Cloud Access Security Broker és a Forcepoint következő generációs tűzfalak Azure Sentinel használatával való összekapcsolását. Így valós időben automatikusan exportálhatja a felhasználó által definiált naplókat az Azure Sentinelbe. Az összekötő biztosítja a Forcepoint-termékek által rögzített felhasználói tevékenységek átláthatóságát. Emellett lehetővé teszi az Azure-beli munkaterhelések és egyéb hírcsatornák adataival való további korrelációt, valamint az Azure Sentinelben lévő munkafüzetek figyelési képességének javítását.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.



## <a name="forward-forcepoint-product-logs-to-the-syslog-agent"></a>Forcepoint-naplók továbbítása a syslog-ügynöknek 

Konfigurálja úgy a Forcepoint terméket, hogy CEF formátumban továbbítsa a syslog-üzeneteket az Azure-munkaterületre a syslog-ügynök használatával.

1. Állítsa be a Forcepoint terméket az Azure Sentinel-integrációba a következő telepítési útmutatókban leírtak szerint:
 - [Forcepoint CASB-integrációs útmutató](https://frcpnt.com/casb-sentinel)
 - [Forcepoint NGFW-integrációs útmutató](https://frcpnt.com/ngfw-sentinel)

2. Keresse meg a CommonSecurityLog, hogy a megfelelő sémát használja Log Analytics a DeviceVendor neve tartalmazza a "Forcepoint" kifejezést. 

3. Folytassa a [3. lépéssel: a kapcsolat ellenőrzése](connect-cef-verify.md).



## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakoztathatók a Forcepoint-termékek az Azure Sentinel szolgáltatáshoz. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).

- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).

- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.