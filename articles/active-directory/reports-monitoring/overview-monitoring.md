---
title: Mit jelent az Azure Active Directory monitorozása? | Microsoft Docs
description: Ez a cikk általános áttekintést nyújt az Azure Active Directory-monitorozásról.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 763e628183e5f6ad7b7bdbb8ee7ce6db572f44ad
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577783"
---
# <a name="what-is-azure-active-directory-monitoring"></a>Mit jelent az Azure Active Directory monitorozása?

Az Azure Active Directory (Azure AD) monitorozásával mostantól különböző végpontokra irányíthatja az Azure AD-tevékenységnaplókat. Ezután megőrizheti őket hosszú távú használatra, vagy külső biztonságiinformáció- és eseménykezelési (SIEM-) eszközökkel integrálva elemezheti a környezetet.

A naplókat jelenleg a következő helyekre irányíthatja:

- Egy Azure-tárfiók.
- Egy Azure-eseményközpontba, amelyen keresztül integrálhatja őket a Splunk- és Sumologic-példányokba.
- Egy Azure Log Analytics-munkaterületre, ahol elemezheti az adatokat, irányítópultokat készíthet, és riasztásokat hozhat létre a specifikus eseményekhez.

**Előfeltételként szükséges szerepkör**: globális rendszergazda

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Az Azure AD jelentéskészítési és figyelési licencelése és előfeltételei

Az Azure AD bejelentkezési naplóihoz való hozzáféréshez szüksége lesz egy prémium szintű Azure AD-licencre.

Részletes funkció-és licencelési információk a [Azure Active Directory díjszabási útmutatójában](https://azure.microsoft.com/pricing/details/active-directory/).

Az Azure AD-figyelés és-jelentéskészítés üzembe helyezéséhez szüksége lesz egy olyan felhasználóra, aki az Azure AD-bérlő globális rendszergazdai vagy biztonsági rendszergazdája.

A naplózási adatai végső céljától függően a következők egyikére lesz szüksége:

* Egy Azure Storage-fiók, amelyen ListKeys jogosultsággal rendelkezik. Azt javasoljuk, hogy általános tárfiókot használjon, ne Blob Storage-fiókot. A tárolás díjszabásával kapcsolatban lásd az [Azure Storage-díjkalkulátort](https://azure.microsoft.com/pricing/calculator/?service=storage).

* Egy Azure Event Hubs névtér, amely integrálható a harmadik féltől származó SIEM-megoldásokkal.

* Egy Azure Log Analytics-munkaterület, amely naplókat küld Azure Monitor naplókba.

## <a name="diagnostic-settings-configuration"></a>Diagnosztikai beállítások konfigurálása

Az Azure AD-tevékenységnaplók monitorozási beállításainak konfigurálásához először jelentkezzen be az [Azure Portalra](https://portal.azure.com), majd válassza az **Azure Active Directory** lehetőséget. Innen kétféleképpen érheti el a diagnosztikai beállítások konfigurációs oldalát:

* Válassza a **Diagnosztikai beállítások** lehetőséget a **Monitorozás** szakaszban.

    ![Diagnosztikai beállítások](./media/overview-monitoring/diagnostic-settings.png)
    
* Válassza az **Auditnaplók** vagy a **Bejelentkezések**, majd a **Beállítások exportálása** lehetőséget. 

    ![Beállítások exportálása](./media/overview-monitoring/export-settings.png)


## <a name="route-logs-to-storage-account"></a>Naplók átirányítása egy tárfiókba

Ha egy Azure-tárfiókba irányítja a naplókat, ott az [adatmegőrzési szabályzatainkban](reference-reports-data-retention.md) vázolt alapértelmezett megőrzési időtartamnál hosszabb ideig megőrizheti őket. További információ [az adatok a tárfiókokba való irányításáról](quickstart-azure-monitor-route-logs-to-storage-account.md).

## <a name="stream-logs-to-event-hub"></a>Naplók streamelése egy eseményközpontba

Ha egy Azure-eseményközpontba irányítja a naplókat, olyan külső SIEM-eszközökkel integrálhatja őket, mint a Sumologic és a Splunk. Ez az integráció lehetővé teszi, hogy az Azure AD-tevékenységek naplójának adatait kombinálja a SIEM által kezelt egyéb adatszolgáltatásokkal, így a környezet mélyebb betekintést biztosít. További információ [a naplók eseményközpontokba való streameléséről](tutorial-azure-monitor-stream-logs-to-event-hub.md).

## <a name="send-logs-to-azure-monitor-logs"></a>Naplók elküldése Azure Monitor naplókba

[Azure monitor a naplók](../../azure-monitor/logs/log-query-overview.md) egy olyan megoldás, amely összevonta a különböző forrásokból származó megfigyelési adatok mennyiségét, és olyan lekérdezési nyelvet és elemzési motort biztosít, amely betekintést nyújt az alkalmazások és erőforrások működésére. Az Azure AD-tevékenység naplófájljainak Azure Monitor naplókba küldésével gyorsan lekérheti, figyelheti és riasztást gyűjthet az összegyűjtött adatokról. Megtudhatja, hogyan [küldhet adatküldést Azure monitor naplókba](howto-integrate-activity-logs-with-log-analytics.md).

Az Azure AD előre elkészített nézeteinek telepítésével monitorozhatja az olyan gyakori eseményeket, mint a bejelentkezések vagy a naplóesemények. Ismerje meg, hogyan [telepítheti és használhatja a log Analytics-nézeteket az Azure ad-tevékenységek naplóihoz](howto-install-use-log-analytics-views.md).

## <a name="next-steps"></a>Következő lépések

* [Tevékenységnaplók az Azure Monitorban](concept-activity-logs-azure-monitor.md)
* [Naplók streamelése egy eseményközpontba](tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Naplók elküldése Azure Monitor naplókba](howto-integrate-activity-logs-with-log-analytics.md)
