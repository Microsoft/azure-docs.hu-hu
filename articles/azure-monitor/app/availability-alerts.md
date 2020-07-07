---
title: Rendelkezésre állási riasztások beállítása az Azure Application Insightskal | Microsoft Docs
description: Webes teszteket állíthat be az Application Insightsban. Riasztásokat kaphat, ha egy webhely elérhetetlenné válik vagy lassan válaszol.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 5af6aec2267384c37f664522d075bf26c632e7e9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81382887"
---
# <a name="availability-alerts"></a>Rendelkezésre állási riasztások

Az [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) rendszeres időközönként, világszerte különböző helyekről webes kéréseket küld az alkalmazására. Riasztást küld, ha az alkalmazás nem válaszol, vagy ha túl lassan válaszol.

## <a name="enable-alerts"></a>Riasztások engedélyezése

Alapértelmezés szerint automatikusan engedélyezve vannak a riasztások, de a riasztás teljes konfigurálása érdekében először létre kell hoznia a rendelkezésre állási tesztet.

![Élmény létrehozása](./media/availability-alerts/create-test.png)

> [!NOTE]
>  Az [új egyesített riasztásokkal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)a riasztási szabály súlyosságát és az értesítési beállításokat [műveleti csoportokkal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) **kell konfigurálni a** riasztások terén. A következő lépések nélkül csak a portálon belüli értesítéseket fogja kapni.

1. A rendelkezésre állási teszt mentése után a Részletek lapon kattintson a három pontra az imént létrehozott teszt alapján. Kattintson a "riasztás szerkesztése" elemre.

   ![Szerkesztés mentés után](./media/availability-alerts/edit-alert.png)

2. Állítsa be a kívánt súlyossági szintet, a szabály leírását és a legfontosabb – a riasztási szabályhoz használni kívánt értesítési beállításokat tartalmazó műveleti csoportot.

   ![Szerkesztés mentés után](./media/availability-alerts/set-action-group.png)

> [!NOTE]
> Az ezzel a tapasztalattal létrehozott rendelkezésre állási riasztások állapota. Ez azt jelenti, hogy ha a riasztási feltételek teljesülnek, egyetlen riasztás jön létre, ha a rendszer nem elérhetőként észleli a helyet. Ha a hely továbbra is a riasztás feltételeinek legközelebb kiértékelése után következik be, akkor ez nem eredményez új riasztást. Így ha a hely egy órán belül leállt, és egy e-mail-riasztást állított be, akkor csak akkor kap e-mailt, ha a hely leállt, és egy további e-mailt, amikor a hely biztonsági mentése történt. Nem kap folyamatos riasztást arra vonatkozóan, hogy a hely még nem volt elérhető.

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Riasztás X-ből az Y-ben nem jelentett hibák

Az X/Y helyek riasztási szabálya alapértelmezés szerint engedélyezve van az [új, egyesített riasztások felületén](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), amikor új rendelkezésre állási tesztet hoz létre. A "klasszikus" lehetőség kiválasztásával vagy a riasztási szabály letiltásával választhat.

> [!NOTE]
> Konfigurálja úgy a műveleti csoportokat, hogy értesítéseket kapjanak, amikor a riasztást a fenti lépések követésével indítja el. Ennek a lépésnek a megkezdése nélkül csak a portálon belüli értesítéseket fogja kapni a szabály indításakor.
>

### <a name="alert-on-availability-metrics"></a>Riasztás a rendelkezésre állási metrikákkal kapcsolatban

Az [új egyesített riasztások](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)használatával riasztást kaphat a szegmentált összesített rendelkezésre állási és tesztelési időtartam mérőszámokról is:

1. Válasszon ki egy Application Insights erőforrást a mérőszámok felületén, és válasszon ki egy rendelkezésre állási mérőszámot:

    ![Rendelkezésre állási metrikák kiválasztása](./media/availability-alerts/select-metric.png)

2. A riasztások beállítása lehetőség a menüben az új felületre kerül, ahol kiválaszthatja a riasztási szabály beállításához megadott teszteket vagy helyszíneket. Ehhez a riasztási szabályhoz itt is konfigurálhatja a műveleti csoportokat.

### <a name="alert-on-custom-analytics-queries"></a>Riasztás egyéni elemzési lekérdezéseken

Az [új egyesített riasztások](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)használatával riasztást készíthet az [egyéni naplók lekérdezéséről](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log). Az egyéni lekérdezésekkel bármilyen tetszőleges feltételt megadhat, amely segít a legmegbízhatóbb rendelkezésre állási problémák megszerzésében. Ez akkor is alkalmazható, ha egyéni rendelkezésre állási eredményeket küld a TrackAvailability SDK használatával.

> [!Tip]
> A rendelkezésre állási adatok metrikái tartalmazzák az egyéni rendelkezésre állási eredményeket, amelyeket az TrackAvailability SDK meghívásával lehet elküldeni. A metrikai riasztások támogatásával riasztást kaphat az egyéni rendelkezésre állási eredményekről.
>

## <a name="automate-alerts"></a>Riasztások automatizálása

Ha Azure Resource Manager-sablonokkal szeretné automatizálni ezt a folyamatot, tekintse meg a [metrikák létrehozása a Resource Manager-sablonnal](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-an-availability-test-along-with-a-metric-alert) című dokumentumot.

## <a name="troubleshooting"></a>Hibaelhárítás

Dedikált [hibaelhárítási cikk](troubleshoot-availability.md).

## <a name="next-steps"></a>További lépések

* [Többlépéses webes tesztek](availability-multistep.md)
* [URL pingelése webes tesztek](monitor-web-app-availability.md)
