---
title: Azure Resource Manager-sablonok
description: Azure Resource Manager sablonok a Azure Site Recovery szolgáltatások használatához.
services: site-recovery
author: rishjai
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 02/04/2021
ms.author: rishjai
ms.openlocfilehash: efd5c1ab3e23348373be76c79d1b9689bc5f4941
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720528"
---
# <a name="azure-resource-manager-templates-for-azure-site-recovery"></a>Azure Site Recovery Azure Resource Manager sablonjai

Az alábbi táblázat a Azure Site Recovery szolgáltatások használatához Azure Resource Manager sablonokra mutató hivatkozásokat tartalmaz.

| Sablon | Leírás |
|---|---|
|**Azure – Azure** | |
| [Recovery Services-tároló létrehozása](./quickstart-create-vault-template.md)| Recovery Services-tároló létrehozása. A tár használható az Azure Backup és az Azure Site Recovery szolgáltatásokkal. |
| [Azure-beli virtuális gépek replikálásának engedélyezése](https://aka.ms/asr-arm-enable-replication) | Engedélyezze a replikálást az Azure-beli virtuális gépek számára a meglévő tároló és az egyéni cél beállítások használatával.|
| [Feladatátvétel elindítása és ismételt védelem](https://aka.ms/asr-arm-failover-reprotect) | Feladatátvételi és ismételt védelmi művelet elindítása Azure-beli virtuális gépekhez. |
| [Az Azure-beli virtuális gépekre vonatkozó teljes körű DR folyamat futtatása](https://aka.ms/asr-arm-e2e-flow) | Indítsa el a teljes befejezést a vész-helyreállítási folyamat végén (a replikáció és a feladatátvétel engedélyezése, valamint az Azure-beli virtuális gépekhez, valamint az 540 °-os folyamat néven is ismert ismételt védelem engedélyezéséhez).|
|   |   |