---
title: IT-szolgáltatásmenedzsmenti csatoló a Azure Monitor
description: Ez a cikk azt ismerteti, hogyan csatlakoztathatók a ITSM-termékek/szolgáltatások a IT-szolgáltatásmenedzsmenti csatoló (ITSMC) segítségével Azure Monitor a ITSM-munkaelemek központi monitorozásához és kezeléséhez.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: 7ac842df17e80b49990d89d1623330c4e31ab566
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100617935"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>ITSM-termékek/-szolgáltatások összekapcsolása IT Service Management-összekötővel
Ez a cikk azt ismerteti, hogyan konfigurálható a ITSM terméke/szolgáltatása és a IT-szolgáltatásmenedzsmenti csatoló (ITSMC) közötti kapcsolat a munkaelemek központilag felügyelhető Log Analyticsban. További információ a ITSMC: [Áttekintés](../platform/itsmc-overview.md).

A következő ITSM-termékek/szolgáltatások támogatottak. Válassza ki a terméket a termék ITSMC való összekapcsolásával kapcsolatos részletes információk megtekintéséhez.

- [ServiceNow](../platform/itsmc-connections-servicenow.md)
- [System Center Service Manager](../platform/itsmc-connections-scsm.md)
- [Cherwell](../platform/itsmc-connections-cherwell.md)
- [Provance](../platform/itsmc-connections-provance.md)

> [!NOTE]
> Javasoljuk, hogy a Cherwell és a felkínált ügyfeleinknek a [webhook műveleteit](../platform/action-groups.md#webhook) a Cherwell és az Előa végpontot használják egy másik megoldásként az integrációhoz.

## <a name="next-steps"></a>Következő lépések

* [Hibaelhárítás az ITSM-összekötőben](../platform/itsmc-resync-servicenow.md)