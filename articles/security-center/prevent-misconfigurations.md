---
title: A hibás konfigurációk megelőzése Azure Security Center
description: Megtudhatja, hogyan használhatja Security Center "betartatása" és "megtagadás" beállításait a javaslatok részleteinek oldalain.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/17/2021
ms.author: memildin
ms.openlocfilehash: fabbd255f64e5614ae052c9d7c8d65fc35d45856
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600496"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>Helytelen konfigurációk megelőzése Kényszerítés/Megtagadás javaslatokkal

A biztonsági incidensek jelentős oka a biztonság. Security Center mostantól lehetővé teszi az új erőforrások helytelen konfigurációjának *megelőzését* az adott javaslatok tekintetében. 

Ez a funkció segít megőrizni a számítási feladatokat, és stabilizálni tudja a biztonságos pontszámot.

A biztonságos konfiguráció egy adott javaslat alapján történő érvényesítése két üzemmódban érhető el:

- A Azure Policy **megtagadásának** hatására leállíthatja a nem megfelelő állapotú erőforrásokat
- A **kikényszerítés** lehetőség használatával kihasználhatja az Azure-szabályzat **DeployIfNotExist** hatását, és a létrehozáskor automatikusan elháríthatja a nem megfelelő erőforrásokat.

Ez az erőforrás részletei lap tetején található a kiválasztott biztonsági javaslatok esetében (lásd: [javaslatok a megtagadási/kényszerítő beállításokkal](#recommendations-with-denyenforce-options)).

## <a name="prevent-resource-creation"></a>Erőforrás-létrehozás megakadályozása

1. Nyissa meg azt a javaslatot, amely szerint az új erőforrásoknak meg kell felelniük, és válassza a **Megtagadás** gombot az oldal tetején.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="Javaslat lap Kiemelt megtagadás gombja":::

    Megnyílik a konfigurációs ablaktábla, amely felsorolja a hatókör beállításait. 

1. A hatókör beállításához válassza ki a megfelelő előfizetést vagy felügyeleti csoportot.

    > [!TIP]
    > A sor végén található három pont használatával egyetlen előfizetés módosítható, vagy a jelölőnégyzetek használatával több előfizetést vagy csoportot is kijelölhet, majd kiválaszthatja, **hogy megtagadja a módosítást**.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="Azure Policy megtagadás hatókörének beállítása":::


## <a name="enforce-a-secure-configuration"></a>Biztonságos konfiguráció érvényesítése

1. Nyissa meg azt a javaslatot, amelybe telepíteni kívánja a sablon központi telepítését, ha az új erőforrások nem elégítik ki azt, majd kattintson a **kikényszerítés** gombra az oldal tetején.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="Javaslat lap Kiemelt kikényszerített gombja":::

    Megnyílik a konfigurációs ablaktábla az összes házirend-konfigurációs lehetőséggel. 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="Konfigurációs beállítások érvényesítése":::

1. Adja meg a hatókört, a hozzárendelés nevét és az egyéb kapcsolódó beállításokat.

1. Válassza az **Áttekintés + létrehozás** lehetőséget.

## <a name="recommendations-with-denyenforce-options"></a>Megtagadási/kikényszerítő lehetőségekkel kapcsolatos javaslatok

A következő javaslatok használhatók a **megtagadási** beállítással:

[!INCLUDE [azure-security-center-recommendations-deny](../../includes/asc/recommendations-with-deny.md)]

A következő javaslatok használhatók a **kényszerítő** beállítással:

- Az SQL Server naplózását engedélyezni kell
- Azure Backup engedélyezni kell a virtuális gépeket
- Az SQL-alapú Azure Defender-t engedélyeznie kell az SQL-kiszolgálókon
- A Kubernetes Azure Policy bővítményét telepíteni és engedélyezni kell a fürtökön
- A Azure Stream Analytics lévő diagnosztikai naplókat engedélyezni kell
- A Batch-fiókokban lévő diagnosztikai naplókat engedélyezni kell
- A Data Lake Analytics lévő diagnosztikai naplókat engedélyezni kell
- Az Event hub diagnosztikai naplóit engedélyezni kell
- A Key Vault lévő diagnosztikai naplókat engedélyezni kell
- A Logic Apps lévő diagnosztikai naplókat engedélyezni kell
- A keresési szolgáltatásokban engedélyezni kell a diagnosztikai naplókat.
- A Service Bus lévő diagnosztikai naplókat engedélyezni kell
