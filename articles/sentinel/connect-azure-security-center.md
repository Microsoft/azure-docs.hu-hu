---
title: Azure Defender-adatkapcsolatok az Azure Sentinelhez
description: Ismerje meg, hogyan csatlakoztathatók az Azure Defender-riasztások Azure Security Centerről, és hogyan továbbíthatja őket az Azure Sentinel szolgáltatásba.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: bb188aa79015c2123b9d9d8b6baf277dfadf2f9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98633044"
---
# <a name="connect-azure-defender-alert-data-from-azure-security-center"></a>Azure Defender-riasztási adatok összekötése Azure Security Center

Az Azure Defender riasztási összekötő használatával betöltheti az Azure Defender-riasztásokat a [Azure Security Centerból](../security-center/security-center-introduction.md) , és továbbíthatja őket az Azure sentinelbe. 

## <a name="prerequisites"></a>Előfeltételek

- A felhasználónak rendelkeznie kell a biztonsági olvasó szerepkörrel a továbbított naplók előfizetésében.

- Engedélyeznie kell az Azure Defender-t Azure Security Centeron belül. (A standard szint már nem létezik, és már nem a licencre vonatkozó követelmény.)

## <a name="connect-to-azure-defender"></a>Kapcsolódás az Azure Defenderhez

1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget a navigációs menüből.

1. Az adatösszekötők katalógusában válassza az **Azure Defender-riasztások az ASC-ből** lehetőséget (továbbra is meghívható Azure Security Center), majd kattintson az **összekötő megnyitása** gombra.

1. A **konfiguráció** alatt kattintson a **Kapcsolódás** elemre minden olyan előfizetés mellett, amelynek a riasztásait továbbítani szeretné az Azure Sentinel szolgáltatásba. A csatlakozás gomb csak akkor lesz elérhető, ha rendelkezik a szükséges engedélyekkel.

1. Kiválaszthatja, hogy az Azure Defender riasztásai automatikusan előállítanak-e incidenseket az Azure Sentinel szolgáltatásban. Az **incidensek létrehozása** területen válassza az **engedélyezve** lehetőséget az alapértelmezett elemzési szabály bekapcsolásához, amely automatikusan létrehozza az incidenseket a riasztásokból. Ezt a szabályt az **elemzés** területen módosíthatja az  **aktív szabályok** lapon.

1. Ha az Azure Defender-riasztások esetében a Log Analytics vonatkozó sémát szeretné használni, keresse meg a **SecurityAlert**.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtanulta, hogyan csatlakoztatható az Azure Defender az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
