---
title: Azure Firewall telepítése virtuális WAN-központban
titleSuffix: Azure Virtual WAN
description: A Azure Firewall konfigurálásának lépései egy virtuális WAN-központban
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 577340e485550e84941a33d82b58aa6ff1c933d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90983662"
---
# <a name="configure-azure-firewall-in-a-virtual-wan-hub"></a>Azure Firewall konfigurálása virtuális WAN-központban

A **biztonságos központ** egy Azure Firewall-vel rendelkező Azure-beli virtuális WAN-központ. Ebből a cikkből megtudhatja, hogyan alakíthatja át a virtuális WAN-központot egy biztonságos hubhoz úgy, hogy Azure Firewall közvetlenül az Azure Virtual WAN portál oldaláról telepíti.

## <a name="before-you-begin"></a>Előkészületek

A cikkben ismertetett lépések azt feltételezik, hogy már telepített egy virtuális WAN-t egy vagy több hubhoz.

Új virtuális WAN és új központ létrehozásához kövesse az alábbi cikkekben ismertetett lépéseket:

* [Virtuális WAN létrehozása](virtual-wan-site-to-site-portal.md#openvwan)
* [Elosztó létrehozása](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-virtual-hubs"></a>Virtuális hubok megtekintése

A virtuális WAN **áttekintő** lapja a virtuális hubok és a biztonságos hubok listáját jeleníti meg. Az alábbi ábrán egy biztonságos hubok nélküli virtuális WAN látható.

[![Képernyőfelvétel: a virtuális WAN áttekintő lapja, amely a virtuális hubok listáját tartalmazza.](./media/howto-firewall/overview.png)](./media/howto-firewall/overview.png#lightbox)

## <a name="convert-to-secured-hub"></a>Konvertálás biztonságos hubhoz

1. A virtuális WAN **Áttekintés** lapján válassza ki azt a hubot, amelyet biztonságos hubhoz kíván átalakítani. A virtuális központ lapon két lehetőséget láthat a Azure Firewall üzembe helyezésére ebben a központban. Válassza az egyik lehetőséget.

   [![A képernyőfelvételen a virtuális WAN áttekintő lapja látható, ahol kiválaszthatja az átalakítás biztonságos hubhoz vagy Azure Firewall lehetőségre.](./media/howto-firewall/security.png)](./media/howto-firewall/security.png#lightbox)

1. Az egyik lehetőség kiválasztását követően megjelenik az **átalakítás biztonságos hubhoz** lapra. Válasszon ki egy konvertálandó központot, majd kattintson a **Next (Azure Firewall tovább** ) gombra a lap alján.

   [![hub kiválasztása](./media/howto-firewall/select-hub.png)](./media/howto-firewall/select-hub.png#lightbox)
1. A munkafolyamat befejezése után válassza a **Confirm (megerősítés**) lehetőséget.

   [![A képernyőfelvétel a biztonságos központi panelre való konvertálást mutatja, és a jóváhagyás lehetőséget választja.](./media/howto-firewall/confirm.png)](./media/howto-firewall/confirm.png#lightbox)

1. Miután a hubot egy biztonságos hubhoz alakította át, megtekintheti a virtuális WAN **Áttekintés** lapján.

   [![biztonságos központ megtekintése](./media/howto-firewall/secured-hub.png)](./media/howto-firewall/secured-hub.png#lightbox)

## <a name="view-hub-resources"></a>Hub-erőforrások megtekintése

A virtuális WAN **áttekintése** lapon válassza ki a biztonságos hubot. A központ lapon megtekintheti az összes virtuális központ erőforrását, beleértve a Azure Firewall is.

[![hub-erőforrások megtekintése](./media/howto-firewall/view-resources.png)](./media/howto-firewall/view-resources.png#lightbox)

Ha Azure Firewall beállításokat szeretne megtekinteni a biztonságos hubhoz, a **Biztonság** területen válassza a **biztonságos virtuális központ beállításai** lehetőséget.
[![központ beállításainak megtekintése](./media/howto-firewall/hub-settings.png)](./media/howto-firewall/hub-settings.png#lightbox)

## <a name="configure-additional-settings"></a>További beállítások konfigurálása

A virtuális központ további Azure Firewall beállításainak konfigurálásához válassza a **Azure Firewall Managerre** mutató hivatkozást. További információ a tűzfal-házirendekről: [Azure Firewall Manager](../firewall-manager/secure-cloud-network.md#create-a-firewall-policy-and-secure-your-hub).

[![További beállítások](./media/howto-firewall/additional-settings.png)](./media/howto-firewall/additional-settings.png#lightbox)

Ha vissza szeretne térni a hub **– Áttekintés** lapra, a következő ábrán látható nyílra kattintva visszatérhet a kívánt elérési útra.

[![Visszatérés az áttekintéshez](./media/howto-firewall/arrow.png)](./media/howto-firewall/arrow.png#lightbox)

## <a name="next-steps"></a>Következő lépések

A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.
