---
title: A közös riasztási séma integrálása a Logic Apps
description: Megtudhatja, hogyan hozhat létre egy logikai alkalmazást, amely a gyakori riasztási sémát használja az összes riasztás kezeléséhez.
ms.service: azure-monitor
ms.subservice: alerts
ms.topic: conceptual
author: ananthradhakrishnan
ms.author: robb
ms.date: 05/27/2019
ms.openlocfilehash: 50a6067d271ad824f17df1ece36c3dd919c7b55b
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555648"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>A közös riasztási séma integrálása a Logic Apps

Ez a cikk bemutatja, hogyan hozhat létre egy logikai alkalmazást, amely kihasználja a közös riasztási sémát az összes riasztás kezeléséhez.

## <a name="overview"></a>Áttekintés

A [Common Alert séma](https://aka.ms/commonAlertSchemaDocs) szabványosított és bővíthető JSON-sémát biztosít az összes különböző riasztási típusban. A gyakori riasztási séma a leggyakrabban a webhookok, a runbookok és a Logic Apps használatával történő programozott használat esetén hasznos. Ebben a cikkben bemutatjuk, hogyan hozhat létre egyetlen logikai alkalmazást az összes riasztás kezeléséhez. Ugyanezen alapelvek alkalmazhatók más programozott módszerekre is. Az ebben a cikkben ismertetett logikai alkalmazás jól definiált változókat hoz létre az ["Essential" mezőkhöz](alerts-common-schema-definitions.md#essentials), valamint leírja, hogyan kezelheti a [riasztás típusának](alerts-common-schema-definitions.md#alert-context) adott logikáját.


## <a name="prerequisites"></a>Előfeltételek 

Ez a cikk azt feltételezi, hogy az olvasó ismeri a következőt 
* Riasztási szabályok beállítása ([metrika](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric), [napló](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log), [műveletnapló](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log))
* [Műveleti csoportok](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) beállítása
* A [Common Alert-séma](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema) engedélyezése a műveleti csoportokon belül

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>A Common Alert sémát használó logikai alkalmazás létrehozása

1. Kövesse a [logikai alkalmazás létrehozásához szükséges lépéseket](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app). 

1.  Válassza ki a triggert: **http-kérés fogadásakor**.

    ![Logikai alkalmazás-eseményindítók](media/action-groups-logic-app/logic-app-triggers.png "A logikai alkalmazások eseményindítói")

1.  A HTTP-kérelem triggerének módosításához válassza a **Szerkesztés** lehetőséget.

    ![HTTP-kérelem eseményindítói](media/action-groups-logic-app/http-request-trigger-shape.png "HTTP-kérelem eseményindítói")


1.  Másolja és illessze be a következő sémát:

    ```json
        {
            "type": "object",
            "properties": {
                "schemaId": {
                    "type": "string"
                },
                "data": {
                    "type": "object",
                    "properties": {
                        "essentials": {
                            "type": "object",
                            "properties": {
                                "alertId": {
                                    "type": "string"
                                },
                                "alertRule": {
                                    "type": "string"
                                },
                                "severity": {
                                    "type": "string"
                                },
                                "signalType": {
                                    "type": "string"
                                },
                                "monitorCondition": {
                                    "type": "string"
                                },
                                "monitoringService": {
                                    "type": "string"
                                },
                                "alertTargetIDs": {
                                    "type": "array",
                                    "items": {
                                        "type": "string"
                                    }
                                },
                                "originAlertId": {
                                    "type": "string"
                                },
                                "firedDateTime": {
                                    "type": "string"
                                },
                                "resolvedDateTime": {
                                    "type": "string"
                                },
                                "description": {
                                    "type": "string"
                                },
                                "essentialsVersion": {
                                    "type": "string"
                                },
                                "alertContextVersion": {
                                    "type": "string"
                                }
                            }
                        },
                        "alertContext": {
                            "type": "object",
                            "properties": {}
                        }
                    }
                }
            }
        }
    ```

1. Válassza ki **+** **új lépést** , majd válassza **a művelet hozzáadása**lehetőséget.

    ![Művelet hozzáadása](media/action-groups-logic-app/add-action.png "Művelet hozzáadása")

1. Ebben a szakaszban számos összekötőt (Microsoft Teams, Slack, Salesforce stb.) adhat hozzá az adott üzleti követelmények alapján. Az "Essential Fields" nem használható. 

    ![Alapvető mezők](media/alerts-common-schema-integrations/logic-app-essential-fields.png "Alapvető mezők")
    
    Azt is megteheti, hogy a "kifejezés" beállítás használatával feltételes logikát hoz létre a riasztás típusa alapján.

    ![Logikai alkalmazás kifejezése](media/alerts-common-schema-integrations/logic-app-expressions.png "Logikai alkalmazás kifejezése")
    
     A ["monitoringService" mező](alerts-common-schema-definitions.md#alert-context) lehetővé teszi, hogy egyedileg azonosítsa a riasztás típusát, amely alapján létrehozhatja a feltételes logikát.

    
    Az alábbi kódrészlet például ellenőrzi, hogy a riasztás Application Insights-alapú napló-riasztás-e, és ha igen, kiírja a keresési eredményeket. Más esetben a "NA"-t nyomtatja ki.

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     További információ a [Logic app-kifejezések írásáról](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions).

    


## <a name="next-steps"></a>Következő lépések

* [További információ a műveleti csoportokról](../../azure-monitor/platform/action-groups.md).
* [További információ a közös riasztási sémáról](https://aka.ms/commonAlertSchemaDocs).

