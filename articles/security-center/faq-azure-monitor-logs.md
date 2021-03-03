---
title: Azure Security Center GYIK – a meglévő Log Analytics ügynökökkel kapcsolatos kérdések
description: Ez a gyakori kérdések a Log Analytics-ügynököt használó ügyfelek kérdéseire válaszolnak, és Azure Security Center, amely a fenyegetések megelőzését, észlelését és reagálását megkönnyítő termékre vonatkozik.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 1a52b2fec6155959a570f2438a59c14d9f79f368
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701960"
---
# <a name="faq-for-customers-already-using-azure-monitor-logs"></a>Gyakori kérdések a Azure Monitor naplókat használó ügyfelek számára<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>A Security Center felülbírálja a virtuális gépek és a munkaterületek közötti meglévő kapcsolatokat?

Ha egy virtuális gépen már telepítve van a Log Analytics ügynök Azure-bővítményként, Security Center nem bírálja felül a meglévő munkaterület-kapcsolatokat. Ehelyett Security Center a meglévő munkaterületet használja. A virtuális gép védett lesz, ha a "biztonság" vagy a "SecurityCenterFree" megoldás telepítve van azon a munkaterületen, amelyre a jelentést küldi. 

Egy Security Center megoldás van telepítve az adatgyűjtési képernyőn kiválasztott munkaterületre, ha még nincs jelen, és a megoldás csak a megfelelő virtuális gépekre lesz alkalmazva. Megoldás hozzáadásakor a rendszer alapértelmezés szerint automatikusan telepíti a Log Analytics munkaterülethez csatlakoztatott összes Windows-és Linux-ügynököt. A [megoldás célcsoportjának](../azure-monitor/insights/solution-targeting.md) használatával hatókört alkalmazhat a megoldásokra.

> [!TIP]
> Ha a Log Analytics ügynök közvetlenül a virtuális gépre van telepítve (nem Azure-bővítményként), Security Center nem telepíti a Log Analytics-ügynököt, és a biztonsági figyelés korlátozott.

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Security Center telepítési megoldásokat a meglévő Log Analytics-munkaterületekre? Mik a számlázási következményei?
Ha Security Center megállapítja, hogy egy virtuális gép már csatlakoztatva van egy létrehozott munkaterülethez, Security Center engedélyezi a megoldásokat a munkaterületen a díjszabási konfigurációnak megfelelően. A megoldásokat csak a megfelelő Azure-beli virtuális gépekre alkalmazza a [megoldás célcsoportján](../azure-monitor/insights/solution-targeting.md)keresztül, így a számlázás változatlan marad.

- **Azure Defender off** – Security Center a "SecurityCenterFree" megoldást telepíti a munkaterületre. Nem számítunk fel díjat.
- **Azure Defender on** – Security Center a "biztonsági" megoldást telepíti a munkaterületre.

   ![Megoldások az alapértelmezett munkaterületen](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Már van munkaterületem a saját környezetben, használhatom a biztonsági adatok gyűjtésére?
Ha egy virtuális gépen már telepítve van a Log Analytics ügynök Azure-bővítményként, Security Center a meglévő csatlakoztatott munkaterületet használja. A munkaterületre egy Security Center megoldás van telepítve, ha még nincs jelen, és a megoldás csak a megfelelő virtuális gépekre vonatkozik, a [megoldás célcsoportján](../azure-monitor/insights/solution-targeting.md)keresztül.

Ha Security Center telepíti a Log Analytics-ügynököt a virtuális gépekre, akkor a Security Center által létrehozott alapértelmezett munkaterületet (ka) t használja, ha Security Center nem egy meglévő munkaterületre mutat.

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Már van biztonsági megoldásom a saját munkaterületeken. Mik a számlázási következményei?
A biztonsági & auditálási megoldás használatával engedélyezhető az **Azure Defender a kiszolgálók számára**. Ha a biztonsági & auditálási megoldás már telepítve van egy munkaterületen, Security Center a meglévő megoldást használja. A számlázás nem változik.