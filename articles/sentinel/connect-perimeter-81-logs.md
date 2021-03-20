---
title: A szegélyhálózati 81-adatkapcsolat összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan csatlakoztatható a peremhálózati 81-beli adatközpontok az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: e70bfd12b018e785e7b3b91e098c99fea1101c04
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100093097"
---
# <a name="connect-your-perimeter-81-activity-logs-to-azure-sentinel"></a>A peremhálózati 81-tevékenység naplófájljainak összekapcsolása az Azure Sentinel használatával

> [!IMPORTANT]
> A peremhálózati 81 adatösszekötő az Azure Sentinel szolgáltatásban jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk azt ismerteti, hogyan csatlakoztatható a [peremhálózati 81 Activity-napló](https://www.perimeter81.com/) berendezése az Azure sentinelhez. A peremhálózati 81 Activity naplók összekötője lehetővé teszi, hogy könnyedén hozza létre a peremhálózati 81-adatait az Azure Sentinel szolgáltatásba, így megtekintheti a munkafüzetekben, felhasználhatja egyéni riasztások létrehozásához és a vizsgálat javítására.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedélyekkel kell rendelkeznie az Azure Sentinel munkaterületen.

- Olvasási jogosultsággal kell rendelkeznie a munkaterület megosztott kulcsaihoz.

## <a name="configure-and-connect-perimeter-81-activity-logs"></a>A szegélyhálózati 81-tevékenység naplófájljainak konfigurálása és összekapcsolása

A peremhálózati 81-tevékenység naplófájljai közvetlenül az Azure Sentinelbe integrálhatók és exportálhatók.

1. Az Azure Sentinel-portálon kattintson a navigációs menü **adatösszekötők** elemére.

1. A katalógusból válassza ki a **peremhálózati 81 Activity naplókat** , majd kattintson az **összekötő lap megnyitása** gombra.

1. A peremhálózati 81 Activity naplók összekötője lapon másolja a **munkaterület-azonosítót** és az **elsődleges kulcsot** , és illessze be azokat a peremhálózati 81-be, az [itt leírtak szerint](https://support.perimeter81.com/hc/en-us/articles/360012680780).

1. Az utasítások elvégzése után megjelenik a csatlakoztatott adattípusok az Azure Sentinel Connector oldalán.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat megjelenik a **naplók** területen a **CustomLogs**  -  **Perimeter81_CL**.

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható a peremhálózati 81-tevékenység naplófájljai az Azure Sentinel szolgáltatáshoz. Az adatösszekötőhöz beépített képességek teljes kihasználásához kattintson a **következő lépések** lapra az adatösszekötő lapon. Itt talál egy előre elkészített munkafüzetet és néhány mintául szolgáló lekérdezést, hogy megismerkedjen a hasznos információk megkeresésével.

Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
