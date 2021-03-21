---
title: Azure Key Vault diagnosztikai naplók összekötése az Azure Sentinel szolgáltatással
description: Ismerje meg, hogyan csatlakoztathatók a Azure Key Vault diagnosztikai naplók az Azure Sentinelhez Azure Policy használatával.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 03/07/2021
ms.author: yelevin
ms.openlocfilehash: 00e634ec62d1fda4b1f2763a8c69b8e950d54beb
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102505188"
---
# <a name="connect-azure-key-vault-diagnostics-logs"></a>Azure Key Vault diagnosztikai naplók összekötése

A Azure Key Vault egy felhőalapú szolgáltatás a titkok biztonságos tárolásához és eléréséhez. A titkos kulcs minden, ami szigorúan szabályozni kívánja a hozzáférést, például az API-kulcsokat, a jelszavakat, a tanúsítványokat és a titkosítási kulcsokat.

Ez az összekötő lehetővé teszi a Azure Key Vault diagnosztikai naplók Azure Sentinelbe való továbbítását, így folyamatosan figyelheti a tevékenységeket az összes példányban.

További információ a [Azure Key Vault figyeléséről](../azure-monitor/insights/key-vault-insights-overview.md) és a [Azure Key Vault diagnosztikai telemetria](../key-vault/general/logging.md).

## <a name="prerequisites"></a>Előfeltételek

Azure Key Vault naplók betöltése az Azure Sentinelbe:

- Olvasási és írási engedélyekkel kell rendelkeznie az Azure Sentinel munkaterületen.

- Ha Azure Policy szeretné használni a naplózási adatfolyam-szabályzatot Azure Key Vault erőforrásokhoz, akkor a házirend-hozzárendelési hatókörhöz hozzá kell rendelnie a tulajdonosi szerepkört.

## <a name="connect-to-azure-key-vault"></a>Kapcsolódás Azure Key Vaulthoz

Ez az összekötő a Azure Policy használatával egyetlen Azure Key Vault log-streaming konfigurációt alkalmaz a példányok egy gyűjteményére, amely hatókörként van definiálva. Az összekötő lap bal oldalán, az **adattípusok** területen láthatók a Azure Key Vault betöltött naplók.

1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők** lehetőséget.

1. Válassza a **Azure Key Vault** lehetőséget az adatösszekötők katalógusában, majd az előnézet ablaktáblán válassza az **összekötő megnyitása lapot** .

1. Az összekötő oldal **konfiguráció** szakaszában bontsa ki a **diagnosztikai naplók engedélyezése Azure Key Vaulton** elemet.

1. Jelölje be a **Azure Policy-hozzárendelés varázsló indítása** gombot.

    Megnyílik a szabályzat-hozzárendelés varázsló, amely készen áll az üzembe helyezés nevű új szabályzat létrehozására **– diagnosztikai beállítások konfigurálása Azure Key Vault log Analytics munkaterületre**.

    1. Az **alapvető beállítások** lapon kattintson a három pontra a **hatókör** területen az előfizetés kiválasztásához (és opcionálisan egy erőforráscsoporthoz). Leírást is hozzáadhat a szolgáltatáshoz.

    1. A **Parameters (paraméterek** ) lapon válassza ki az Azure Sentinel-munkaterületet a **log Analytics munkaterület** legördülő listából. A fennmaradó legördülő mezők a rendelkezésre álló diagnosztikai naplók típusát jelölik. A betöltéshez használni kívánt összes naplózási típust "true"-ként hagyja.

    1. Ha alkalmazni szeretné a szabályzatot a meglévő erőforrásokon, válassza a **szervizelés** fület, és jelölje be a **szervizelési feladat létrehozása** jelölőnégyzetet.

    1. A **felülvizsgálat + létrehozás** lapon kattintson a **Létrehozás** gombra. A szabályzat most már hozzá van rendelve a kiválasztott hatókörhöz.

> [!NOTE]
>
> Ezzel az adatösszekötővel a kapcsolati állapot mutatói (az adatösszekötők katalógusában és az adattípusok neve melletti kapcsolati ikonokban lévő színcsíkok) csak akkor jelennek *meg, ha* az adatok betöltése az elmúlt két hétben egy bizonyos pontján történik. Ha két hét telt el az adatfeldolgozás nélkül, az összekötő leválasztva jelenik meg. Minél több információ érkezik, a *csatlakoztatott* állapot vissza fog térni.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtanulta, hogyan csatlakozhat Azure Key Vault az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan [érheti el az adatait és a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
