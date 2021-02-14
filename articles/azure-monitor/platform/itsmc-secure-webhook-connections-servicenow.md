---
title: IT-szolgáltatásmenedzsmenti csatoló – biztonságos exportálás Azure Monitor – konfiguráció a ServiceNow
description: Ez a cikk bemutatja, hogyan csatlakoztathatók a ITSM-termékek/szolgáltatások a ServiceNow-mel a biztonságos exportálással Azure Monitorban.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 7801145ba1bcaa2ffd543becfe190f05d232e8c8
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104960"
---
# <a name="connect-servicenow-to-azure-monitor"></a>A ServiceNow Azure Monitorhoz való kapcsolódása

A következő szakaszokban részletesen ismertetjük a ServiceNow-termék összekapcsolását és az Azure-beli biztonságos exportálást.

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy teljesítette a következő előfeltételeket:

* Az Azure AD regisztrálva van.
* A ServiceNow Event Management-ITOM támogatott verziója (New York-i vagy újabb verzió).

## <a name="configure-the-servicenow-connection"></a>A ServiceNow-kapcsolatok konfigurálása

1. Használja a https://(példány neve). Service-Now. com/API/sn_em_connector/em/inbound_event? Source = azuremonitor a biztonságos exportálás definíciójának URI-JÁT.

2. Kövesse az utasításokat a verziónak megfelelően:
   * [Párizs](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Orlando](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [New York](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
