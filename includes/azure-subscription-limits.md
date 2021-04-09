---
title: fájl belefoglalása
description: fájl belefoglalása
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 05/18/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 74f9c5304237ec77a629bc914d95c345882b784f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95561978"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| vCPU/ [előfizetés](https://azure.microsoft.com/pricing/)<sup>1</sup> |20 |10,000 |
| A [rendszergazdák](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) /előfizetés |200 |200 |
| [Storage-fiókok](../articles/storage/common/storage-account-create.md) /előfizetés<sup>2</sup> |100 |100 |
| [Felhőszolgáltatások](../articles/cloud-services/cloud-services-choose-me.md) előfizetésenként |20 |200 |
| [Helyi hálózatok](/previous-versions/azure/reference/jj157100(v=azure.100)) /előfizetés |10 |500 |
| DNS-kiszolgálók/előfizetés |9 |100 |
| Fenntartott IP-címek/előfizetés |20 |100 |
| [Affinitáscsoportok](/previous-versions/azure/virtual-network/virtual-networks-migrate-to-regional-vnet) előfizetésenként |256 |256 |
| Előfizetés nevének hossza (karakter) | 64 | 64 |

<sup>1</sup> Az extra kisméretű példányok egy részleges CPU-mag használata ellenére a vCPU-korlát felé vCPU számítanak.

<sup>2</sup> A Storage-fiók korlátja a standard és a Premium Storage-fiókokat is tartalmazza.