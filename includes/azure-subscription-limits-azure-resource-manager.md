---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/15/2021
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: f02b7f0f80cfb875cc6207b542db90607b379b67
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799791"
---
| Erőforrás | Korlát |
| --- | --- |
| Egy új [bérlőhöz társított Azure Active Directory előfizetések](../articles/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) | Korlátlan |
| [Társadminisztrátorok](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) előfizetésenként |Korlátlan |
| [Erőforráscsoportok előfizetésenként](../articles/azure-resource-manager/management/overview.md) |980 |
| Azure Resource Manager API-kérés mérete |4 194 304 bájt |
| Címkék előfizetésenként<sup>1</sup> |50 |
| Egyedi címkeszámítások előfizetésenként<sup>1</sup> | 80,000 |
| [Előfizetés-szintű üzemelő példányok](../articles/azure-resource-manager/templates/deploy-to-subscription.md) helyenként | 800<sup>2</sup> |

<sup>1</sup> Legfeljebb 50 címkét alkalmazhat közvetlenül egy előfizetésre. Az előfizetés azonban korlátlan számú címkét tartalmazhat, amelyek az előfizetésen belüli erőforráscsoportokra és erőforrásokra vonatkoznak. A címkék erőforrásonként vagy erőforráscsoportonkénti száma legfeljebb 50 lehet. Resource Manager egyedi [címkenév](/rest/api/resources/tags) és -értékek listáját adja vissza az előfizetésben, csak akkor, ha a címkék száma 80 000 vagy kevesebb. Ha a szám meghaladja a 80 000-et, továbbra is találhat címkével egy erőforrást.

<sup>2</sup> Ha eléri a 800 üzemelő példány korlátját, törölje azokat az üzemelő példányokat, amelyekre már nincs szükség az előzményekből. Előfizetés-szintű üzemelő példányok törléséhez használja a [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) vagy [az az deployment sub delete parancsot.](/cli/azure/deployment/sub#az_deployment_sub_delete)
