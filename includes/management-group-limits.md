---
title: fájl belefoglalása
description: fájl belefoglalása
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: cdcf6215973755444da9e513761de7ac71e479d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98738682"
---
| Erőforrás | Korlát |
| --- | --- |
| Felügyeleti csoportok száma Azure AD-bérlőn | 10,000 |
| Előfizetések felügyeleti csoportonként | Korlátlan. |
| Felügyeleti csoport hierarchiájának szintjei | Gyökérszintű szint plusz 6 szint<sup>1</sup> |
| Felügyeleti csoport közvetlen fölérendelt felügyeleti csoportja | Eggyel |
| [Felügyeleti csoport szintjén üzemelő központi telepítések](../articles/azure-resource-manager/templates/deploy-to-management-group.md) száma | 800<sup>2</sup> |

<sup>1</sup> A 6 szint nem tartalmazza az előfizetési szintet.

<sup>2</sup> Ha eléri a 800-es üzemelő példányok korlátját, törölje a már nem szükséges előzményekből származó központi telepítéseket. A felügyeleti csoport szintű központi telepítések törléséhez használja a [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) vagy [az az Deployment mg delete](/cli/azure/deployment/mg#az-deployment-mg-delete)parancsot.
