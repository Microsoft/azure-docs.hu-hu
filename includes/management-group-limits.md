---
title: fájl belefoglalása
description: fájl belefoglalása
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 53e3f37d14153f3a2d7b5886a49b08ca9052b128
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800258"
---
| Erőforrás | Korlát |
| --- | --- |
| Felügyeleti csoportok Azure AD-bérlőnként | 10,000 |
| Előfizetések felügyeleti csoportonként | Korlátlan. |
| Felügyeleticsoport-hierarchia szintjei | Gyökérszint plusz 6<sup>szint 1</sup> |
| Közvetlen szülő felügyeleti csoport felügyeleti csoportonként | Eggyel |
| [Felügyeleti csoportszintű központi telepítések](../articles/azure-resource-manager/templates/deploy-to-management-group.md) hely szerint | 800<sup>2</sup> |

<sup>1</sup> A 6 szint nem tartalmazza az előfizetési szintet.

<sup>2</sup> Ha eléri a 800 üzemelő példány korlátját, törölje az üzemelő példányokat a már nem szükséges előzményekből. Felügyeleticsoport-szintű üzemelő példányok törléséhez használja a [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) vagy [az az deployment mg delete parancsot.](/cli/azure/deployment/mg#az_deployment_mg_delete)
