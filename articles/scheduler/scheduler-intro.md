---
title: Mi az Azure Scheduler? | Microsoft Docs
description: Elsajátíthatja, hogyan hozhat létre, ütemezhet és futtathat automatizált feladatokat, amelyek szolgáltatásokat hívnak meg az Azure-ban vagy azon kívül
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 52aa6ae1-4c3d-43fb-81b0-6792c84bcfae
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 2f418a78f80d65cbb784685804a4cc6790c28b99
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300906"
---
# <a name="what-is-azure-scheduler"></a>Mi az Azure Scheduler?

> [!IMPORTANT]
> [Azure Logic apps](../logic-apps/logic-apps-overview.md) az Azure Scheduler cseréje [folyamatban](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)van. Ha továbbra is szeretne dolgozni a Feladatütemezőben beállított feladatokkal, akkor a lehető leghamarabb [telepítse át Azure Logic apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) .

Az [Azure Scheduler](https://azure.microsoft.com/services/scheduler/) segítségével a műveletek deklaratív leírásával felhőben futó [feladatokat](../scheduler/scheduler-concepts-terms.md) hozhat létre. A szolgáltatás ezt követően ezeknek a műveleteknek az ütemezését és futtatását automatikusan végzi el. Például szolgáltatásokat hívhat az Azure-ban és azon kívül, mint például a HTTP- vagy HTTPS-végpontok hívása, és küldhet is üzeneteket az Azure Storage-üzenetsorokba és az Azure Service Bus-üzenetsorokba vagy -témakörökbe. Futtathatja a feladatokat azonnal vagy egy későbbi időpontban. A Schedulerrel egyszerűen hozhat létre [komplex és speciális ismétlődő ütemezéseket](../scheduler/scheduler-advanced-complexity.md). A Scheduler meghatározza, mikor futtathat feladatokat, megőrzi a feladatok eredményeinek előzményeit, amelyeket megtekinthet, majd előre jelezhetően és megbízhatóan ütemezi a számítási feladatokat a futtatáshoz.

Bár a Schedulerrel létrehozhat, kezelhet, karban tarthat, ütemezhet és futtathat számítási feladatokat, a Scheduler nem üzemelteti a számítási feladatokat, és nem futtatja a kódot. A szolgáltatás csupán a máshol, például az Azure-ban, helyileg vagy egy másik szolgáltatónál üzemeltetett szolgáltatást vagy kódot *hívja meg*. A Scheduler meghívható a következőkön keresztül: HTTP, HTTPS, Storage-üzenetsor, Service Bus-üzenetsor vagy Service Bus-témakör. Feladatok létrehozására, kezelésére és ütemezésére használható az [Azure Portal](../scheduler/scheduler-get-started-portal.md), kód, a [Scheduler REST API](https://docs.microsoft.com/rest/api/scheduler/) vagy [Azure Scheduler PowerShell-parancsmaghivatkozás](scheduler-powershell-reference.md). Programozott módon létrehozhat, megtekinthet, frissíthet, kezelhet vagy törölhet például feladatokat és [feladatgyűjteményeket](../scheduler/scheduler-concepts-terms.md) parancsfájlok használatával és az Azure Portalon.

Más Azure-ütemezési szolgáltatások, például az [Azure WebJobs](../app-service/webjobs-create.md), amely az Azure App Service [Web Apps](https://azure.microsoft.com/services/app-service/web/) szolgáltatása, szintén használják a Schedulert a háttérben. Az ezekkel a műveletekkel való kommunikációt a [Scheduler REST API-val](https://docs.microsoft.com/rest/api/scheduler/) kezelheti. megkönnyíti a műveletekkel való kommunikációt.

Az alábbiakban néhány forgatókönyv következik, ahol a Scheduler a segítségére lehet:

* **Ismétlődő alkalmazás-műveletek futtatása**: Például rendszeresen gyűjthet adatokat a Twitterből egy hírcsatornába.

* **Napi karbantartás végrehajtása**: Ilyenek például a napi törlési naplók, a biztonsági mentések végrehajtása és egyéb karbantartási feladatok. 

  Rendszergazdaként például érdemes lehet biztonsági másolatot készíteni az adatbázisról minden nap 1:00 órakor a következő kilenc hónapban.

## <a name="next-steps"></a>További lépések

* [Ismerkedés a Scheduler szolgáltatással az Azure Portalon](scheduler-get-started-portal.md)
* Az [Azure Scheduler csomagjaira és számlázására](scheduler-plans-billing.md) vonatkozó információk
* [Komplex és speciális, ismétlődő ütemezések létrehozása az Azure Scheduler használatával](scheduler-advanced-complexity.md)