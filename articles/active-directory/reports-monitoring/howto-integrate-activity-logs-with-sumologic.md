---
title: Stream-naplók a SumoLogic Azure Monitor használatával | Microsoft Docs
description: Ismerje meg, hogyan integrálhat Azure Active Directory naplókat a SumoLogic Azure Monitor használatával.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51e1f45c787c319c32358e7f310108131647d60e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91335833"
---
# <a name="integrate-azure-active-directory-logs-with-sumologic-using-azure-monitor"></a>Azure Active Directory-naplók integrálása a SumoLogic a Azure Monitor használatával

Ebből a cikkből megtudhatja, hogyan integrálhatja a Azure Active Directory-(Azure AD-) naplókat a SumoLogic Azure Monitor használatával. Először az Azure Event hub-ba irányítja a naplókat, majd az Event hub-t integrálja a SumoLogic.

## <a name="prerequisites"></a>Előfeltételek

A szolgáltatás használatához a következőkre lesz szüksége:
* Az Azure AD-tevékenységek naplóit tartalmazó Azure Event hub. Megtudhatja, hogyan [továbbíthatja a tevékenység naplóit egy Event hubhoz](./tutorial-azure-monitor-stream-logs-to-event-hub.md). 
* SumoLogic egyszeri bejelentkezésre alkalmas előfizetés.

## <a name="steps-to-integrate-azure-ad-logs-with-sumologic"></a>Az Azure AD-naplók és a SumoLogic integrálásának lépései 

1. Először [továbbítsa az Azure ad-naplókat egy Azure Event hub](./tutorial-azure-monitor-stream-logs-to-event-hub.md)-ba.
2. Konfigurálja a SumoLogic-példányt a [Azure Active Directory naplók gyűjtésére](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory).
3. [Telepítse az Azure ad SumoLogic alkalmazást](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards) a környezet valós idejű elemzését biztosító előre konfigurált irányítópultok használatára.

   ![Irányítópult](./media/howto-integrate-activity-logs-with-sumologic/overview-dashboard.png)

## <a name="next-steps"></a>Következő lépések

* [A naplózási naplók sémájának értelmezése Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [A bejelentkezési naplók sémájának értelmezése az Azure Monitorban](reference-azure-monitor-sign-ins-log-schema.md)
* [Gyakori kérdések és ismert hibák](concept-activity-logs-azure-monitor.md#frequently-asked-questions)