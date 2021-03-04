---
title: IT-szolgáltatásmenedzsmenti csatoló – biztonságos exportálás Azure Monitor – konfiguráció a ServiceNow
description: Ez a cikk bemutatja, hogyan csatlakoztathatók a ITSM-termékek/szolgáltatások a ServiceNow-mel a biztonságos exportálással Azure Monitorban.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: f296037a56b25255c7f7c7d48a8e8b7d4889052f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041637"
---
# <a name="connect-servicenow-to-azure-monitor"></a>A ServiceNow Azure Monitorhoz való kapcsolódása

A következő szakaszokban részletesen ismertetjük a ServiceNow-termék összekapcsolását és az Azure-beli biztonságos exportálást.

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy teljesítette a következő előfeltételeket:

* Az Azure AD regisztrálva van.
* A ServiceNow Event Management-ITOM támogatott verziója (New York-i vagy újabb verzió).
* A ServiceNow-példányra telepített [alkalmazás](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ac4c9c57dbb1d090561b186c1396191a/1.3.1?referer=%2Fstore%2Fsearch%3Flistingtype%3Dallintegrations%25253Bancillary_app%25253Bcertified_apps%25253Bcontent%25253Bindustry_solution%25253Boem%25253Butility%26q%3DEvent%2520Management%2520Connectors&sl=sh) .

## <a name="configure-the-servicenow-connection"></a>A ServiceNow-kapcsolatok konfigurálása

1. Használja a https://(példány neve). Service-Now. com/API/sn_em_connector/em/inbound_event? Source = azuremonitor a biztonságos exportálás definíciójának URI-JÁT.

2. Kövesse az utasításokat a verziónak megfelelően:
   * [Párizs](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Orlando](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [New York](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
