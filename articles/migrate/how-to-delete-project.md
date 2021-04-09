---
title: Azure Migrate-projekt eltávolítása
description: Ebből a cikkből megtudhatja, hogyan törölhet egy Azure Migrate projektet a Azure Portal használatával.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: how-to
ms.date: 10/22/2019
ms.openlocfilehash: bfb4db5d3ebf69f9c7f552c175d33a8b817d1562
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595135"
---
# <a name="delete-an-azure-migrate-project"></a>Azure Migrate-projekt eltávolítása

Ez a cikk egy [Azure Migrate](./migrate-services-overview.md) -projekt törlését ismerteti.


## <a name="before-you-start"></a>Előkészületek

Projekt törlése előtt:

- Ha töröl egy projektet, a projekt és a felderített gép metaadatai törlődnek.
- Ha csatolt egy Log Analytics munkaterületet a kiszolgáló-értékelési eszközhöz a függőségek elemzéséhez, döntse el, hogy törölni kívánja-e a munkaterületet. 
    - A munkaterület nem törlődik automatikusan. Törölje manuálisan.
    - Győződjön meg arról, hogy a törlés előtt milyen munkaterület van használatban. Ugyanaz a Log Analytics munkaterület több forgatókönyv esetén is használható.
    - A projekt törlése előtt megtalálhatja a munkaterületre mutató hivatkozást **Azure Migrate-** Servers  >  **Azure Migrate-Server Assessment**( **OMS munkaterület**) területen.
    - Ha törölni szeretne egy munkaterületet a projekt törlése után, keresse meg a munkaterületet a megfelelő erőforráscsoporthoz, és kövesse az [alábbi utasításokat](../azure-monitor/logs/delete-workspace.md).


## <a name="delete-a-project"></a>Projekt törlése


1. A Azure Portal nyissa meg azt az erőforráscsoportot, amelyben a projekt létrejött.
2. Az erőforráscsoport lapon válassza a **rejtett típusok megjelenítése** elemet.
3. Válassza ki a projektet és a törölni kívánt erőforrásokat.
    - Azure Migrate projektek erőforrástípus a **Microsoft. migrálása/migrateprojects**.
    - A következő szakaszban tekintse át a felderítéshez, az értékeléshez és az áttelepítéshez létrehozott erőforrásokat egy Azure Migrate projektben.
    - Ha az erőforráscsoport csak a Azure Migrate projektet tartalmazza, akkor törölheti a teljes erőforráscsoportot.
    - Ha törölni szeretne egy projektet a Azure Migrate előző verziójáról, a lépések ugyanazok. Ezeknek a projekteknek az erőforrás-típusa **áttelepítési projekt**.


## <a name="created-resources"></a>Létrehozott erőforrások

Ezek a táblázatok összefoglalják a felderítéshez, értékeléshez és áttelepítéshez létrehozott erőforrásokat egy Azure Migrate projektben.

> [!NOTE]
> Törölje a Key vaultot körültekintően, mert biztonsági kulcsokat tartalmazhat.

### <a name="vmwarephysical-server"></a>VMware/fizikai kiszolgáló

**Erőforrás** | **Típus**
--- | ---
"Appliancename" kV | Key Vault
"Appliancename" webhely | Microsoft. OffAzure/VMwareSites
Projektnév | Microsoft. Migrálás/migrateprojects
"Projektnév" projekt | Microsoft. Migrálás/assessmentProjects
"Projektnév" rsvault | Recovery Services-tároló
"Projektnév"-MigrateVault-* | Recovery Services-tároló
migrateappligwsa* | Tárfiók
migrateapplilsa* | Tárfiók
migrateapplicsa* | Tárfiók
migrateapplikv* | Key Vault
migrateapplisbns16041 | Service Bus-névtér

### <a name="hyper-v-vm"></a>Hyper-V virtuális gép 

**Erőforrás** | **Típus**
--- | ---
Projektnév | Microsoft. Migrálás/migrateprojects
"Projektnév" projekt | Microsoft. Migrálás/assessmentProjects
HyperV * kV | Key Vault
HyperV * hely | Microsoft. OffAzure/HyperVSites
"Projektnév"-MigrateVault-* | Recovery Services-tároló


## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan adhat hozzá további [értékelési](how-to-assess.md) és [áttelepítési](how-to-migrate.md) eszközöket. 
