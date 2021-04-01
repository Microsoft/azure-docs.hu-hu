---
title: Naplófájlok konfigurálása – Azure Portal-Azure Database for MySQL – rugalmas kiszolgáló
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni és elérni a naplókat Azure Database for MySQL rugalmas kiszolgálóról a Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: ebb980aa257fc09c3d6a407febbf60f2d1a26a4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94536472"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Azure Database for MySQL rugalmas kiszolgálóhoz tartozó naplók konfigurálása és elérése a Azure Portal használatával

> [!IMPORTANT]
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Konfigurálhatja a Azure Database for MySQL rugalmas kiszolgálói [naplózási naplókat](concepts-audit-logs.md) és a diagnosztikai beállításokat a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek
A cikkben ismertetett lépések megkövetelik, hogy [rugalmas kiszolgálóval](quickstart-create-server-portal.md)rendelkezzen.

## <a name="configure-audit-logging"></a>Naplózás konfigurálása

>[!IMPORTANT]
> Azt javasoljuk, hogy csak azokat az eseményeket és felhasználókat naplózza, amelyek szükségesek a naplózáshoz, hogy a kiszolgáló teljesítménye ne legyen nagy hatással.

A naplózás engedélyezése és konfigurálása.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza ki a rugalmas kiszolgálót.

1. Az oldalsáv **Beállítások** szakaszában válassza ki a **kiszolgáló paramétereit**.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/server-parameters.png" alt-text="Kiszolgálóparaméterek":::

1. Frissítse a **audit_log_enabled** PARAMÉTERt a következőre:.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/audit-log-enabled.png" alt-text="Naplók engedélyezése":::

1. Válassza ki a naplózni kívánt [események típusát](concepts-audit-logs.md#configure-audit-logging) a **audit_log_events** paraméter frissítésével.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/audit-log-events.png" alt-text="Naplózási események":::

1. Vegyen fel minden olyan MySQL-felhasználót, amelyet be szeretne vonni, vagy ki szeretne zárni a naplózásból a **audit_log_exclude_users** és **audit_log_include_users** paraméterek frissítésével. Adja meg a felhasználókat a MySQL-Felhasználónév megadásával.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/audit-log-exclude-users.png" alt-text="Naplók kizárása a felhasználók számára":::

1. A paraméterek módosítása után kattintson a **Mentés** gombra. Vagy **elvetheti** a módosításokat.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/save-parameters.png" alt-text="Mentés":::

## <a name="set-up-diagnostics"></a>Diagnosztika beállítása

A naplófájlok integrálva vannak Azure Monitor diagnosztikai beállításokkal, amelyek lehetővé teszik a naplók Azure Monitor naplókba, Event Hubs vagy Azure Storage-ba történő átadását.

1. Az oldalsáv **figyelés** területén válassza a **diagnosztikai beállítások** elemet.

1. Kattintson a "+ diagnosztikai beállítás hozzáadása" :::image type="content" source="./media/how-to-configure-audit-logs-portal/add-diagnostic-setting.png" alt-text="diagnosztikai beállítás hozzáadása"::: elemre.

1. Adja meg a diagnosztikai beállítások nevét.

1. Határozza meg, hogy mely célhelyek legyenek elküldve a naplóknak (Storage-fiók, Event hub és/vagy Log Analytics munkaterület).

1. Válassza a **MySqlAuditLogs** lehetőséget a napló típusaként.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/configure-diagnostic-setting.png" alt-text="Diagnosztikai beállítás konfigurálása":::

1. Miután konfigurálta az adattárolást a naplókba, kattintson a **Save (Mentés**) gombra.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/save-diagnostic-setting.png" alt-text="Diagnosztikai beállítás mentése":::

1. A naplókat úgy érheti el, hogy a konfigurált adattárolókban vizsgálja őket. A naplók megjelenése akár 10 percet is igénybe vehet.

Ha a naplókat Azure Monitor naplókba (Log Analytics) használja, tekintse meg az elemzéshez használható [példákat](concepts-audit-logs.md#analyze-logs-in-azure-monitor-logs) .  

## <a name="next-steps"></a>Következő lépések

- További információ a [naplókról](concepts-audit-logs.md)
- A [lassú lekérdezési naplók](concepts-slow-query-logs.md) ismertetése
<!-- - Learn how to configure audit logs in the [Azure CLI](howto-configure-audit-logs-cli.md)-->