---
title: Gen2-környezet beállítása a Azure Portal-Azure Time Series Insights Gen2 használatával | Microsoft Docs
description: Ismerje meg, hogyan állíthat be környezetet Azure Time Series Insights Gen2 Azure Portal használatával.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: 09068d966df871d4b6804978a543db50bccbee37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952847"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-portal"></a>Azure Time Series Insights Gen2-környezet létrehozása a Azure Portal használatával

Ez a cikk azt ismerteti, hogyan hozható létre Azure Time Series Insights Gen2-környezet a [Azure Portal](https://portal.azure.com/)használatával.

A környezet-létesítési oktatóanyag végigvezeti a folyamaton. Megtudhatja, hogyan választhatja ki a megfelelő idősorozat-azonosítót, és hogyan tekinthet meg példákat két JSON-tartalmon.</br>

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWzk3P]

## <a name="overview"></a>Áttekintés

Azure Time Series Insights Gen2-környezet kiépítésekor ezeket az Azure-erőforrásokat hozza létre:

* Az utólagos elszámolású árképzési modellt követő Azure Time Series Insights Gen2-környezet
* Azure Storage-fiók
* Egy opcionális meleg tároló a gyorsabb és korlátlan lekérdezésekhez

> [!TIP]
>
> * Ismerje meg [, hogyan tervezze meg a környezetét](./how-to-plan-your-environment.md).
> * További információ az [Event hub-források hozzáadásáról](./how-to-ingest-data-event-hub.md) , illetve [az IoT hub-forrás hozzáadásáról](./how-to-ingest-data-iot-hub.md).

Az alábbiakat fogja elsajátítani:

1. Társítson minden Azure Time Series Insights Gen 2 környezetet egy eseményforrás. Egy időbélyeg-azonosító tulajdonságot és egy egyedi fogyasztói csoportot is meg kell adnia annak biztosításához, hogy a környezet hozzáférjen a megfelelő eseményekhez.

1. A kiépítés befejezése után módosíthatja a hozzáférési házirendeket és az egyéb környezeti attribútumokat az üzleti igényeinek megfelelően.

   > [!NOTE]
   > A környezet kiépítés esetén az első lépés nem kötelező. Ha kihagyja ezt a lépést, akkor később csatlakoztatnia kell egy eseményforrást a környezethez, így az adatforgalom a környezetbe áramlik, és a lekérdezésen keresztül érhető el.

## <a name="create-the-environment"></a>A környezet létrehozása

Azure Time Series Insights 2. generációs környezet létrehozása:

1. Hozzon létre egy Azure Time Series Insights erőforrást a [Azure Portal](https://portal.azure.com/) *eszközök internetes hálózata* alatt.

1. Válassza ki a **Gen2 (L1)** **szintet**. Adja meg a környezet nevét, és válassza ki a használni kívánt előfizetési csoportot és erőforráscsoportot. Ezután válasszon ki egy támogatott helyet a környezet üzemeltetéséhez.

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration.png" alt-text="Hozzon létre egy Azure Time Series Insights példányt." lightbox="media/how-to-create-environment-using-portal/environment-configuration.png":::

1. Adjon meg egy idősorozat-azonosítót.

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration-2.png" alt-text="Azure Time Series Insights környezeti konfiguráció létrehozása, folytatás." lightbox="media/how-to-create-environment-using-portal/environment-configuration-2.png":::

   > [!NOTE]
   >
   > * Az idősorozat-azonosító *megkülönbözteti a kis-* *és* nagybetűket. (Ez a beállítás után nem módosítható.)
   > * Az idősorozat-azonosítók legfeljebb *három* kulccsal rendelkezhetnek. Gondoljon rá elsődleges kulcsként egy adatbázisban, amely egyedileg jelöli az összes olyan érzékelőt, amely az adott környezetnek küldi az adatküldést. Ez lehet egy tulajdonság vagy legfeljebb három tulajdonság kombinációja.
   > * További információ az [idősorozat-azonosítók kiválasztásáról](./how-to-select-tsid.md)

1. Hozzon létre egy Azure Storage-fiókot a Storage-fiók nevének, a fióknak a kiválasztásával és a [replikációs](../storage/common/redundancy-migration.md?tabs=portal) választás kijelölésével. Ezzel automatikusan létrehoz egy Azure Storage-fiókot. Alapértelmezés szerint a rendszer az [általános célú v2](../storage/common/storage-account-overview.md) -fiókot hozza létre. A fiók ugyanabban a régióban jön létre, mint a korábban kiválasztott Azure Time Series Insights Gen2-környezet.
Azt is megteheti, hogy egy új Azure Time Series Gen2-környezet létrehozásakor saját tárolót (BYOS) használ egy [Azure Resource Manager-sablonon](./time-series-insights-manage-resources-using-azure-resource-manager-template.md) keresztül.

1. **(Nem kötelező)** Ha szeretné, hogy a környezetében a legutóbbi adatmennyiségnél gyorsabban és korlátlanul lehessen lekérdezéseket használni, engedélyezze a meleg tárolást a környezet számára. Egy Azure Time Series Insights Gen2-környezet létrehozása után a bal oldali navigációs ablaktáblán a **tárolási konfiguráció** lehetőséggel is létrehozhat vagy törölhet egy meleg áruházat.

1. **(Nem kötelező)** Most hozzáadhat egy eseményforrás-forrást. Azt is megvárhatja, amíg a példány üzembe lett helyezve.

   * Azure Time Series Insights támogatja az [azure IoT hub](./how-to-ingest-data-iot-hub.md) és az [Azure Event Hubs](./how-to-ingest-data-event-hub.md) használatát az eseményforrás lehetőségeinek megfelelően. Bár a környezet létrehozásakor csak egyetlen eseményforrás adhatók hozzá, később további eseményforrás is felvehető.

     Az eseményforrás hozzáadásakor kiválaszthat egy meglévő felhasználói csoportot, vagy létrehozhat egy új fogyasztói csoportot is. Győződjön meg arról, hogy az eseményforrás egy egyedi fogyasztói csoportot használ a környezet számára, hogy beolvassa az adatait.

   * Válassza ki a megfelelő időbélyeg-tulajdonságot. Alapértelmezés szerint a Azure Time Series Insights az várólistán lévő időt használja az egyes eseményekhez.

     > [!TIP]
     > Előfordulhat, hogy az várólistán lévő idő nem a legjobb konfigurált beállítás a Batch-eseményekben vagy a korábbi adatfeltöltési forgatókönyvekben. Ilyen esetekben ügyeljen arra, hogy ellenőrizze a használati vagy nem időbélyeg-tulajdonság használatát.

   :::image type="content" source="media/how-to-create-environment-using-portal/configure-event-source.png" alt-text="Eseményforrás konfigurációja lap" lightbox="media/how-to-create-environment-using-portal/configure-event-source.png":::

1. Válassza a **felülvizsgálat + létrehozás lehetőséget annak ellenőrzéséhez** , hogy a környezete ki van-e választva, és hogy konfigurálta-e a kívánt módszert.

    :::image type="content" source="media/how-to-create-environment-using-portal/environment-confirmation.png" alt-text="Felülvizsgálat + Létrehozás lap" lightbox="media/how-to-create-environment-using-portal/environment-confirmation.png":::

## <a name="next-steps"></a>Következő lépések

* További információ Azure Time Series Insights általánosan elérhető környezetekről és Gen2 környezetekről a [környezet megtervezése](./how-to-plan-your-environment.md)című témakörben olvasható.
* Ismerkedjen meg az Azure Time Series Insights Gen2-környezet adatfolyam-betöltési [eseményeinek forrásaival](./concepts-streaming-ingestion-event-sources.md) .
* További információ a [környezet kezeléséről](./how-to-provision-manage.md).
