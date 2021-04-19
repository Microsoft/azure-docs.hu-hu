---
title: Azure Migrate-projekt eltávolítása
description: Ebből a cikkből megtudhatja, hogyan törölhet egy Azure Migrate projektet a Azure Portal.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: how-to
ms.date: 10/22/2019
ms.openlocfilehash: 8c94bb23f5d514fef5cdacb855657efdf5219631
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714739"
---
# <a name="delete-an-azure-migrate-project"></a>Azure Migrate-projekt eltávolítása

Ez a cikk egy új projekt [törlését Azure Migrate](./migrate-services-overview.md) ismerteti.


## <a name="before-you-start"></a>Előkészületek

Projekt törlése előtt:

- Projekt törlésekor a projekt és a felderített gép metaadatai is törlődnek.
- Ha Log Analytics-munkaterületet csatolt a Server Assessment eszközhöz függőségelemzéshez, döntse el, hogy törölni szeretné-e a munkaterületet. 
    - A munkaterület nem törlődik automatikusan. Törölje manuálisan.
    - A törlés előtt ellenőrizze, hogy a munkaterület mire van használva. Ugyanaz a Log Analytics-munkaterület több forgatókönyvben is használható.
    - A projekt törlése előtt az OMS-munkaterület alatt talál egy hivatkozást a Azure Migrate **- Servers** Azure Migrate - Server Assessment ( Kiszolgálók Azure Migrate  >  **)** **munkaterületen.**
    - Ha törölni szeretne egy munkaterületet egy projekt törlése után, keresse meg a munkaterületet a megfelelő erőforráscsoportban, és kövesse [az alábbi utasításokat.](../azure-monitor/logs/delete-workspace.md)


## <a name="delete-a-project"></a>Projekt törlése


1. A Azure Portal nyissa meg azt az erőforráscsoportot, amelyben a projektet létrehozták.
2. Az erőforráscsoport oldalán válassza a **Rejtett típusok megjelenítése lehetőséget.**
3. Válassza ki a projektet és a törölni kívánt társított erőforrásokat.
    - Az erőforrástípus a Azure Migrate **Microsoft.Migrate/migrateprojects.**
    - A következő szakaszban tekintse át a felderítéshez, értékeléshez és migráláshoz létrehozott erőforrásokat egy Azure Migrate projektben.
    - Ha az erőforráscsoport csak a Azure Migrate projektet tartalmazza, a teljes erőforráscsoportot törölheti.
    - Ha törölni szeretne egy projektet az előző Azure Migrate, a lépések ugyanazok. Ezeknek a projekteknek az erőforrástípusa a **Migrálási projekt.**


## <a name="created-resources"></a>Létrehozott erőforrások

Ezek a táblák összefoglalják a felderítéshez, értékeléshez és migráláshoz létrehozott erőforrásokat egy Azure Migrate projektben.

> [!NOTE]
> A kulcstartót óvatosan törölje, mert biztonsági kulcsokat tartalmazhat.

### <a name="projects-with-public-endpoint-connectivity"></a>Nyilvános végponttal összekapcsolt projektek

#### <a name="vmwarephysical-server"></a>VMware/fizikai kiszolgáló

**Erőforrás** | **Típus**
--- | ---
"Appliancename"kv | Key Vault
"Berendezésnév"hely | Microsoft.OffAzure/VMwareSites
"ProjectName" | Microsoft.Migrate/migrateprojects
"ProjectName" projekt | Microsoft.Migrate/assessmentProjects
"ProjectName"rsvault | Recovery Services-tároló
"ProjectName"-MigrateVault-* | Recovery Services-tároló
migrateappligwsa* | Tárfiók
migrateapplilsa* | Tárfiók
migrateapplicsa* | Tárfiók
migrateapplikv* | Key Vault
migrateapplisbns* | Service Bus-névtér

#### <a name="hyper-v-vm"></a>Hyper-V virtuális gép

**Erőforrás** | **Típus**
--- | ---
"ProjectName" | Microsoft.Migrate/migrateprojects
"ProjectName" projekt | Microsoft.Migrate/assessmentProjects
HyperV*kv | Key Vault
HyperV*Hely | Microsoft.OffAzure/HyperVSites
"ProjectName"-MigrateVault-* | Recovery Services-tároló

<br/>
Az alábbi táblázatok összefoglalják a Azure Migrate által létrehozott erőforrásokat a kiszolgálók felderítéséhez, értékeléséhez és áttelepítéséhez egy magánhálózaton az [Azure privát kapcsolat használatával.](./how-to-use-azure-migrate-with-private-endpoints.md)

### <a name="projects-with-private-endpoint-connectivity"></a>Projektek privát végpontkapcsolattal

#### <a name="vmware-vms---agentless-migrations"></a>VMware virtuális gépek – ügynök nélküli migrálások

**Típus** | **Erőforrás** | **Privát végpont <br/>** |
--- | --- | ---
Microsoft.Migrate/migrateprojects | "ProjectName" | "ProjectName" \* pe 
Felderítési hely (fő hely) | "ProjectName"*masterite | "ProjectName" \* masterite \* pe 
Microsoft.Migrate/assessmentProjects | "ApplianceName"*project | "ApplianceName" \* \* projekt pe 
Key Vault | "ProjectName"*kv | "ProjectName" \* kv \* pe
Microsoft.OffAzure/VMwareSites | "ApplianceName"*site | NA
Recovery Services-tároló | "ApplianceName"*vault | NA
Tárfiók | "ApplianceName"*usa | "ApplianceName" \* usa \* pe
Recovery Services-tároló | "ProjectName"-MigrateVault-* | NA
Tárfiók | migrateappligwsa* | NA
Tárfiók | migrateapplilsa* | NA
Key Vault | migrateapplikv* | NA
Service Bus-névtér | migrateapplisbns* | NA

#### <a name="hyper-v-vms"></a>Hyper-V virtuális gépek 

**Típus** | **Erőforrás** | **Privát végpont <br/>** |
--- | --- | ---
Microsoft.Migrate/migrateprojects | "ProjectName" | "ProjectName" \* pe 
Felderítési hely (fő hely) | "ProjectName"*főhely | "ProjectName" \* masterite \* pe 
Microsoft.Migrate/assessmentProjects | "ApplianceName"*project | "ApplianceName" \* projekt \* pe 
Key Vault | "ProjectName"*kv | "ProjectName" \* kv \* pe
Microsoft.OffAzure/HyperVSites | "ApplianceName"*site | NA
Recovery Services-tároló | "ProjectName"-MigrateVault-* | "ProjectName"-MigrateVault-*pe

#### <a name="physical-servers--aws-vms--gcp-vms"></a>Fizikai kiszolgálók / AWS virtuális gépek / GCP virtuális gépek 

**Típus** | **Erőforrás** | **Privát végpont <br/>** |
--- | --- | ---
Microsoft.Migrate/migrateprojects | "ProjectName" | "ProjectName" \* pe 
Felderítési hely (fő hely) | "ProjectName"*masterite | "ProjectName" \* masterite \* pe 
Microsoft.Migrate/assessmentProjects | "ApplianceName"*project | "ApplianceName" \* \* projekt pe 
Key Vault | "ProjectName"*kv | "ProjectName" \* kv \* pe
Microsoft.OffAzure/serversites | "ApplianceName"*site | NA
Recovery Services-tároló | "ProjectName"-MigrateVault-* | "ProjectName"-MigrateVault-*pe


## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan adhat hozzá további [értékelési és](how-to-assess.md) [migrálási](how-to-migrate.md) eszközöket. 
