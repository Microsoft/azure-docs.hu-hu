---
title: A SCOM, Zabbix és az Azure monitorban Nagios riasztások kezelése
description: A SCOM, Zabbix és az Azure monitorban Nagios riasztások kezelése
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: 48fb9d8eaf2003834a420b48d649c830c608fd6e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60712980"
---
# <a name="manage-alerts-from-scom-zabbix-and-nagios-in-azure-monitor"></a>A SCOM, Zabbix és az Azure monitorban Nagios riasztások kezelése

Most már megtekintheti a riasztásokat a Nagios, Zabbix és a System Center Operations Manager [Azure Monitor](https://aka.ms/azure-alerts-overview). Ezek a riasztások származhat integrációt Nagios-és Zabbix-kiszolgálók vagy a System Center Operations Manager a Log Analytics. 

## <a name="prerequisites"></a>Előfeltételek
A Log Analytics-tárházban riasztás típusú rekordokat fogja lekérése importálja az Azure Monitor, így ezeket a rekordokat összegyűjtéséhez szükséges konfigurációt kell végrehajtania.
1. A **Nagios** és **Zabbix** riasztások, [konfigurálja az ezeken a kiszolgálókon](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) való [küld riasztást,](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-alerts-nagios-zabbix?toc=%2Fazure%2Fazure-monitor%2Ftoc.json) a Log Analytics szolgáltatásba.
1. A **System Center Operations Manager** riasztások [az Operations Manager felügyeleti csoport csatlakozni a Log Analytics-munkaterület](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Ezt követően üzembe helyezése a [Riasztáskezelés](https://docs.microsoft.com/azure/azure-monitor/platform/alert-management-solution) megoldás az Azure-megoldások marketplace-ről. Ha végzett, a System Center Operations Managerben létrehozott riasztásokat a Log Analytics is importálja.

## <a name="view-your-alert-instances"></a>A riasztási példányok megtekintése
Miután konfigurálta a Log Analytics-ba való importálásuk, ezeket a szolgáltatásokat a figyelési riasztás példányok megtekintése elkezdheti [Azure Monitor](https://aka.ms/azure-alerts-overview). Után az Azure Monitor jelen, [felügyelhető a riasztási](https://aka.ms/managing-alert-instances), [intelligens, ezek a riasztások létrehozott csoportok kezelése](https://aka.ms/managing-smart-groups) és [az állapotváltozáshoz. a riasztások és az intelligens csoportok](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  1. Ez a megoldás lehetővé teszi az SCOM/Zabbix/Nagios aktivált riasztás példányok megtekintése az Azure monitorban csak. Riasztási szabály konfigurációjának a megfelelő figyelési eszközök megtekinteni és szerkeszteni csak lehet. 
>  1. Az összes aktivált riasztás példányok az Azure Monitor és az Azure Log Analytics is lesz. Jelenleg nincs lehetőség a kettő közül választhat, vagy csak adott aktivált riasztások fogadása.
>  1. A SCOM, Zabbix és Nagios összes riasztás a jel típusa "Ismeretlen" lehet, mert a az alapul szolgáló telemetriai adatok típusa nem érhető el.
>  1. Nagios-riasztások nem állnak állapot-nyilvántartó – például a [feltétel figyelése](https://aka.ms/azure-alerts-overview) riasztás nem kerül majd "Fired" a "Megoldva". Ehelyett a "Fired" és "Megoldva" jelennek meg külön riasztás példány. 

