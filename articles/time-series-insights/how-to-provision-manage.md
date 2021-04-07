---
title: 2. generációs környezet kezelése – Azure Time Series | Microsoft Docs
description: Megtudhatja, hogyan kezelheti Azure Time Series Insights 2. generációs környezetét.
author: shipra1mishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/15/2020
ms.custom: seodec18
ms.openlocfilehash: cbd28bdf5318bdaf932447f5d1f936d2c614a7f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103461895"
---
# <a name="manage-azure-time-series-insights-gen2"></a>Azure Time Series Insights Gen2 kezelése

Miután létrehozta Azure Time Series Insights Gen2-környezetét [Az Azure CLI](./how-to-create-environment-using-cli.md) vagy [a Azure Portal](./how-to-create-environment-using-portal.md)használatával, módosíthatja a hozzáférési házirendeket és az egyéb környezeti attribútumokat az üzleti igényeinek megfelelően.

## <a name="manage-the-environment"></a>A környezet kezelése

Azure Time Series Insights Gen2-környezetét a [Azure Portal](https://portal.azure.com/)használatával kezelheti. Van néhány lényeges különbség a Gen2-környezet és a Gen1 S1 vagy Gen1 S2 környezet között, hogy figyelembe vegye a környezetét a Azure Portal:

* Az Azure Portal Gen2 **Áttekintés** ablaktáblája a következő módosításokat hajtja végre:

  * A rendszer eltávolítja a kapacitást, mert az nem vonatkozik a Gen2-környezetekre.
  * A rendszer hozzáadja az **idősorozat-azonosító** tulajdonságot. Meghatározza, hogyan particionálja az adatait.
  * A rendszer eltávolítja a hivatkozási adatkészleteket.
  * A megjelenített URL-cím a [Azure Time Series Insights Explorerre](./concepts-ux-panels.md)irányítja.
  * Megjelenik az Azure Storage-fiók neve.

* A rendszer eltávolítja a Azure Portal **konfigurálási** paneljét, mert a skálázási egységek nem vonatkoznak Azure Time Series Insights Gen2 környezetekre. A **tárolási konfigurációval** azonban az újonnan bevezetett meleg tárolót is konfigurálhatja.

* A Azure Portal **hivatkozási** adatpanelje el lesz távolítva a Azure Time Series Insights Gen2, mert a hivatkozási adatkoncepciót a [Time Series Model (TSM)](./concepts-model-overview.md)váltotta fel.

:::image type="content" source="media/v2-update-manage/create-and-manage-overview-confirm.png" alt-text="Azure Time Series Insights Gen2-környezet a Azure Portal":::

## <a name="next-steps"></a>Következő lépések

* Tekintse át az [adatfolyamok betöltésének ajánlott eljárásainak](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices) listáját
* A [diagnosztizálás és a hibakeresés](./how-to-diagnose-troubleshoot.md) ismertetése
