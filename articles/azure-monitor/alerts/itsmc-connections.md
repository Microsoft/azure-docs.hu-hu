---
title: IT-szolgáltatásmenedzsmenti csatoló a Azure Monitor
description: Ez a cikk azt ismerteti, hogyan csatlakoztathatók a ITSM-termékek/szolgáltatások a IT-szolgáltatásmenedzsmenti csatoló (ITSMC) segítségével Azure Monitor a ITSM-munkaelemek központi monitorozásához és kezeléséhez.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: a372cdcd05267f3bdb093f676948a79c473ad955
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734774"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>ITSM-termékek/-szolgáltatások összekapcsolása IT Service Management-összekötővel
Ez a cikk azt ismerteti, hogyan konfigurálható a ITSM terméke/szolgáltatása és a IT-szolgáltatásmenedzsmenti csatoló (ITSMC) közötti kapcsolat a munkaelemek központilag felügyelhető Log Analyticsban. További információ a ITSMC: [Áttekintés](./itsmc-overview.md).

A következő ITSM-termékek/szolgáltatások támogatottak. Válassza ki a terméket a termék ITSMC való összekapcsolásával kapcsolatos részletes információk megtekintéséhez.

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

> [!NOTE]
> Javasoljuk, hogy a Cherwell és a felkínált ügyfeleinknek a [webhook műveleteit](./action-groups.md#webhook) a Cherwell és az Előa végpontot használják egy másik megoldásként az integrációhoz.

## <a name="next-steps"></a>Következő lépések

* [Hibaelhárítás az ITSM-összekötőben](./itsmc-resync-servicenow.md)