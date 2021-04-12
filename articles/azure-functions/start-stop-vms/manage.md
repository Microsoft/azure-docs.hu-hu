---
title: Virtuális gépek indításának és leállításának v2 (előzetes verzió) kezelése
description: Ez a cikk azt ismerteti, hogyan figyelheti az Azure-beli virtuális gépek állapotát a Start/Stop VM v2 (előzetes verzió) funkció által kezelt és egyéb felügyeleti feladatok végrehajtásához.
services: azure-functions
ms.subservice: ''
ms.date: 03/16/2021
ms.topic: conceptual
ms.openlocfilehash: 477e3fe51f05bb4323642f56233f1995e6394eec
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111956"
---
# <a name="how-to-manage-startstop-vms-v2-preview"></a>Virtuális gépek indítása és leállítása v2 (előzetes verzió)

## <a name="azure-dashboard"></a>Azure-irányítópult

A VM v2 indítása/leállítása (előzetes verzió) tartalmaz egy [irányítópultot](../../azure-monitor/visualizations.md#azure-dashboards) , amely segít megérteni a felügyeleti hatókört és a virtuális gépekkel kapcsolatos legutóbbi műveleteket. Gyors és egyszerű módszer az Azure-beli virtuális gépeken végrehajtott műveletek állapotának ellenőrzésére. Az egyes csempék vizualizációi egy naplófájlon alapulnak, és a lekérdezés megjelenítéséhez a csempe jobb felső sarkában a **Megnyitás a naplók** paneljén lehetőséget kell választani. Ekkor megnyílik a [log Analytics](../../azure-monitor/logs/log-analytics-overview.md#starting-log-analytics) eszköz a Azure Portalban, és innen kiértékelheti a lekérdezést, és módosíthatja az igényeket, például az egyéni [naplók riasztásait](../../azure-monitor/alerts/alerts-log.md), egy egyéni [munkafüzetet](../../azure-monitor/visualize/workbooks-overview.md)stb.

A rendszer óránként frissíti az irányítópult összes csempéjét, ha az adott vizualizációban a **frissítés** ikonra kattint, vagy a teljes irányítópult frissítését választja.

A log-alapú irányítópultok használatáról a következő [oktatóanyagban](../../azure-monitor/visualize/tutorial-logs-dashboards.md)olvashat bővebben.

## <a name="configure-email-notifications"></a>E-mail-értesítések konfigurálása

Ha módosítani szeretné az e-mail-értesítéseket a virtuális gépek v2 (előzetes verzió) elindítása/leállítása után, módosíthatja az üzembe helyezés során létrehozott műveleti csoportot.

1. A Azure Portal navigáljon a **figyelés**, majd a **riasztások** elemre. Válassza a **műveletek kezelése** lehetőséget.

1. A **műveletek kezelése** lapon válassza ki a **StartStopV2_VM_Notication** nevű műveleti csoportot.

    :::image type="content" source="media/manage/alerts-action-groups.png" alt-text="Képernyőfelvétel a műveleti csoportok lapról.":::

1. A **StartStopV2_VM_Notification** lapon módosíthatja az e-mailek/SMS/leküldéses/hang értesítési beállításait. Vegyen fel más műveleteket, vagy frissítse a meglévő konfigurációt erre a műveleti csoportra, majd kattintson **az OK** gombra a módosítások mentéséhez.

    :::image type="content" source="media/manage/email-notification-type-example.png" alt-text="Képernyőkép az e-mailek/SMS/push/Voice lapról, amely egy példaként frissített e-mail-címet mutat be.":::

    A műveleti csoportokkal kapcsolatos további információkért lásd: [műveleti csoportok](../../azure-monitor/alerts/action-groups.md)

A következő képernyőkép egy példaként szolgáló e-mailt küld, amikor a szolgáltatás leállítja a virtuális gépeket.

:::image type="content" source="media/manage/email-notification-example.png" alt-text="A szolgáltatás által a virtuális gépek leállításakor küldött példa e-mail-üzenet képernyőképe.":::

## <a name="next-steps"></a>Következő lépések

A virtuális gépek kezelése során felmerülő problémák kezeléséhez lásd: a [VM v2 (előzetes verzió) indítási és leállítási hibáinak elhárítása](troubleshoot.md) .