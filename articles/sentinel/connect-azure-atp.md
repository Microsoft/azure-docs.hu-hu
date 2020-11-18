---
title: A Microsoft Defender for Identity (korábbi nevén Azure ATP) adatainak összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan továbbíthatja a naplókat a Microsoft Defender for Identity (korábban az Azure Advanced Threat Protection) (ATP) szolgáltatásba az Azure Sentinel szolgáltatásba egyetlen kattintással.
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
ms.openlocfilehash: b0cafcbc9fa6f0fcb31f44b44dfbe4fc3e10c17d
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656071"
---
# <a name="connect-data-from-microsoft-defender-for-identity-formerly-azure-advanced-threat-protection"></a>Adatok összekapcsolása a Microsoft Defender identitásával (korábbi nevén az Azure Advanced Threat Protection)

> [!IMPORTANT]
> A Microsoft Defender for Identity adatösszekötő az Azure Sentinel szolgáltatásban jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Egyetlen kattintással továbbíthatja a [Microsoft Defender for Identity](/azure-advanced-threat-protection/what-is-atp) naplóit az Azure sentinelbe.

## <a name="prerequisites"></a>Előfeltételek

- Globális rendszergazdai vagy biztonsági rendszergazdai engedélyekkel rendelkező felhasználó
- A Microsoft Defender előzetes verziójú ügyfelének kell lennie az identitáshoz, és lehetővé kell tennie a Microsoft Defender és a Microsoft Cloud App Security közötti integrációt. További információ: [a Microsoft Defender identitás-integrációja](https://www.microsoft.com/microsoft-365/identity/advance-threat-protection).

## <a name="connect-to-microsoft-defender-for-identity"></a>Kapcsolódás a Microsoft Defenderhez az identitáshoz

Győződjön meg arról, hogy a Microsoft Defender for Identity Preview verziója engedélyezve van a [hálózaton](/azure-advanced-threat-protection/install-atp-step1).
Ha a Microsoft Defender for Identity üzembe helyezését és az adatok betöltését végzi, a gyanús riasztások könnyen továbbíthatók az Azure Sentinel szolgáltatásba. Akár 24 óráig is eltarthat, amíg a riasztások elkezdik a folyamatos átvitelt az Azure Sentinel szolgáltatásba.


1. Ahhoz, hogy a Microsoft Defender identitását az Azure Sentinelhez lehessen kapcsolni, először engedélyeznie kell a Microsoft Defender és a Microsoft Cloud App Security közötti integrációt. Ennek módjáról további információt a [Microsoft Defender identitás-integrációs szolgáltatásával](https://www.microsoft.com/microsoft-365/identity/advance-threat-protection)foglalkozó témakörben talál.

1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget, majd kattintson a **Microsoft Defender for Identity (előzetes verzió)** csempére.

1. Kiválaszthatja, hogy a Microsoft Defender által kiadott riasztások automatikusan előállítanak-e incidenseket az Azure Sentinelben. Az **incidensek létrehozása** területen válassza az **Engedélyezés** lehetőséget az alapértelmezett analitikus szabály engedélyezéséhez, amely automatikusan létrehozza az incidenseket a csatlakoztatott biztonsági szolgáltatásban létrehozott riasztásokból. Ezt a szabályt az **elemzés** , majd az **aktív szabályok** területen módosíthatja.

1. Kattintson a **Csatlakozás** gombra.

1. Ha a Microsoft Defender for Identity-riasztások esetében szeretné használni a Log Analytics vonatkozó sémát, keresse meg a **SecurityAlert**.

> [!NOTE]
> Ha a riasztások 30 KB-nál nagyobbak, az Azure Sentinel nem jeleníti meg az entitások mezőt a riasztásokban.

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható a Microsoft Defender for Identity az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).