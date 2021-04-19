---
title: Csatlakoztassa Microsoft Defender for Identity (korábban Azure ATP) adatokat a Azure Sentinel| Microsoft Docs
description: Megtudhatja, hogyan streamelhet naplókat Microsoft Defender for Identity (korábban Azure Advanced Threat Protection) (ATP) Azure Sentinel egyetlen kattintással.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 7a26091d4985b6fdb17120c6fd70476a750c94a9
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714130"
---
# <a name="connect-data-from-microsoft-defender-for-identity-formerly-azure-advanced-threat-protection"></a>Adatok csatlakoztatása a Microsoft Defender for Identity (korábban Azure Advanced Threat Protection)

> [!IMPORTANT]
> A Microsoft Defender for Identity adat-összekötő jelenleg Azure Sentinel előzetes verzióban érhető el.
> Ez a funkció szolgáltatói szerződés nélkül biztosított, és nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk azt ismerteti, hogyan streamelhet biztonsági riasztásokat Microsoft Defender for Identity [a](/azure-advanced-threat-protection/what-is-atp) Azure Sentinel. 

Ha a biztonsági riasztások mellett állapotriasztásokat is továbbít, integrálja a Microsoft Defender for Identity syslog-kiszolgálóval. További információért tekintse meg a Microsoft Defender for Identity [dokumentációját.](/defender-for-identity/setting-syslog) 

## <a name="prerequisites"></a>Előfeltételek

- Globális rendszergazdai vagy biztonsági rendszergazdai engedélyekkel rendelkező felhasználó
- Előzetes verzióban elérhető ügyfélnek kell lennie a Microsoft Defender for Identity és engedélyeznie kell az integrációt a Microsoft Defender for Identity és Microsoft Cloud App Security. További információ: Integráció [Microsoft Defender for Identity.](/cloud-app-security/mdi-integration)

## <a name="connect-to-microsoft-defender-for-identity"></a>Csatlakozás Microsoft Defender for Identity

Győződjön meg arról, Microsoft Defender for Identity előzetes verzió engedélyezve [van a hálózaton.](/azure-advanced-threat-protection/install-atp-step1)
Ha Microsoft Defender for Identity üzembe helyezett és az adatokat bemenő adatokkal, a gyanús riasztások egyszerűen streamelhetők a Azure Sentinel. A riasztások streamelése akár 24 órát is Azure Sentinel.


1. A Microsoft Defender for Identity és Azure Sentinel való csatlakozáshoz először engedélyeznie kell az integrációt a Microsoft Defender for Identity és Microsoft Cloud App Security. További információ ennek a folyamatról: Microsoft Defender for Identity [integrációja.](/cloud-app-security/mdi-integration)

1. A Azure Sentinel válassza az **Adat-összekötők** lehetőséget, majd kattintson Microsoft Defender for Identity **(előzetes verzió) csempére.**

1. Kiválaszthatja, hogy a riasztások automatikusan Microsoft Defender for Identity generálnak-e incidenseket a Azure Sentinel között. Az **Incidensek létrehozása alatt** válassza az **Engedélyezés** lehetőséget az alapértelmezett analytic szabály engedélyezéséhez, amely automatikusan hoz létre incidenseket a csatlakoztatott biztonsági szolgáltatásban létrehozott riasztások alapján. Ezt a szabályt az  Elemzés, majd az Aktív szabályok alatt **szerkesztheti.**

1. Kattintson a **Csatlakozás** gombra.

1. A Log Analytics megfelelő sémáját úgy használhatja a riasztások Microsoft Defender for Identity, hogy a **SecurityAlert kifejezésre keres.**

> [!NOTE]
> Ha a riasztások nagyobbak 30 KB-Azure Sentinel, a riasztások nem jelenítik meg az Entitások mezőt.

## <a name="next-steps"></a>Következő lépések
Ebben a dokumentumban megtanulta, hogyan csatlakozhat Microsoft Defender for Identity Azure Sentinel. A további Azure Sentinel a következő cikkekben talál:
- Ismerje meg, [hogyan lehet betekintést szerezni az adatokba és a lehetséges fenyegetésekbe.](quickstart-get-visibility.md)
- A fenyegetések [észlelésének első Azure Sentinel.](tutorial-detect-threats-built-in.md)
