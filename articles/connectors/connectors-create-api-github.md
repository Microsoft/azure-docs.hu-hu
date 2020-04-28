---
title: A GitHub-tárház elérése, monitorozása és kezelése
description: A GitHub-események monitorozása és a GitHub-tárház kezelése a Azure Logic Apps használatával automatizált munkafolyamatok létrehozásával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: b67d2e860e8c69f6f2f43441144ef2f60ed3a104
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75378449"
---
# <a name="monitor-and-manage-your-github-repo-by-using-azure-logic-apps"></a>A GitHub-tárház monitorozása és kezelése Azure Logic Apps használatával

A GitHub egy webalapú git-tárház, amely a git és más szolgáltatások összes elosztott változat-és forráskód-kezelési (SCM) funkcióját kínálja.

Első lépésként [hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md)a GitHub-összekötő megkezdéséhez.

## <a name="create-a-connection-to-github"></a>Hozzon létre egy kapcsolódást a GitHubhoz

Ha logikai alkalmazásban szeretné használni a GitHub-összekötőt, először létre kell hoznia egy *kapcsolatokat* , majd meg kell adnia a következő tulajdonságokkal kapcsolatos adatokat: 

| Tulajdonság | Kötelező | Leírás | 
| -------- | -------- | ----------- | 
| Jogkivonat | Igen | Adja meg a GitHub hitelesítő adatait. |

A kapcsolatok létrehozása után végrehajthatja a műveleteket, és figyelheti a cikkben ismertetett eseményindítókat.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Összekötő-referencia

Az eseményindítókkal, műveletekkel és korlátokkal kapcsolatos technikai részletekért lásd az összekötő OpenAPI (korábban: hencegés) leírását, tekintse át az [összekötő hivatkozási oldalát](/connectors/github/).

## <a name="next-steps"></a>További lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése