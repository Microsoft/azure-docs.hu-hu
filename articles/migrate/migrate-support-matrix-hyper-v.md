---
title: A Hyper-V értékelésének támogatása a Azure Migrateban
description: Ismerkedjen meg a Hyper-V felmérés támogatásával Azure Migrate felderítéssel és értékeléssel
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: bdd8fd336da12b5549875cf53f9e221cc431c2b9
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773215"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>A Hyper-V felmérés támogatási mátrixa

Ez a cikk az előfeltételeket és a támogatási követelményeket összegzi a Hyper-V környezetben futó helyszíni kiszolgálók felderítésére és értékelésére az Azure-ba való Migrálás során a [Azure Migrate: Discovery and Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) Tool használatával. Ha a Hyper-V-n futó kiszolgálókat át szeretné telepíteni az Azure-ra, tekintse át az [áttelepítési támogatási mátrixot](migrate-support-matrix-hyper-v-migration.md).

A Hyper-V-n futó kiszolgálók felderítésének és értékelésének beállításához létre kell hoznia egy projektet, és hozzá kell adnia a Azure Migrate: Discovery and Assessment eszközt a projekthez. Az eszköz hozzáadása után üzembe helyezi a [Azure Migrate berendezést](migrate-appliance.md). A készülék folyamatosan felfedi a helyszíni kiszolgálókat, és kiszolgálói metaadatokat és teljesítményadatokat küld az Azure-nak. A felderítés befejezése után a felderített kiszolgálókat csoportokba gyűjtheti, és értékelést futtathat egy csoport számára.

## <a name="limitations"></a>Korlátozások

**Támogatás** | **Részletek**
--- | ---
**Értékelési korlátok** | Egyetlen [projektben](migrate-support-matrix.md#azure-migrate-projects)akár 35 000-kiszolgálót is felderítheti és felbecsülheti.
**Projekt korlátai** | Egy Azure-előfizetésben több projektet is létrehozhat. A Hyper-V-kiszolgálókon kívül a projektek a VMware és a fizikai kiszolgálókon is tartalmazhatnak kiszolgálókat, az egyes eszközökre vonatkozó értékelési korlátokig.
**Felderítés** | A Azure Migrate berendezés a Hyper-V-n futó, akár 5000-es kiszolgálót is képes észlelni.<br/><br/> A készülék legfeljebb 300 Hyper-V gazdagéphez tud csatlakozni.
**Értékelés** | Egyetlen csoportban akár 35 000-kiszolgálót is hozzáadhat.<br/><br/> Egy csoportra vonatkozóan akár 35 000-kiszolgálót is megvizsgálhat egyetlen értékeléssel.

[További](concepts-assessment-calculation.md) információ az értékelésekről.

## <a name="hyper-v-host-requirements"></a>A Hyper-V gazdagépre vonatkozó követelmények

| **Támogatás**                | **Részletek**
| :-------------------       | :------------------- |
| **Hyper-V gazdagép**       | A Hyper-V-gazdagép önálló vagy fürtben is üzembe helyezhető.<br/><br/> A Hyper-V-gazdagép a Windows Server 2019, a Windows Server 2016 vagy a Windows Server 2012 R2 rendszert futtathatja. Ezen operációs rendszerek Server Core telepítései szintén támogatottak. <br/>A Windows Server 2012 operációs rendszert futtató Hyper-V-gazdagépeken található kiszolgálók nem mérhetők fel.
| **Engedélyek**           | Rendszergazdai engedélyekkel kell rendelkeznie a Hyper-V-gazdagépen. <br/> Ha nem szeretne rendszergazdai jogosultságokat rendelni, hozzon létre egy helyi vagy tartományi felhasználói fiókot, és adja hozzá a felhasználói fiókot ezekhez a csoportokhoz – a távfelügyeleti felhasználók, a Hyper-V-rendszergazdák és a Teljesítményfigyelő felhasználói számára. |
| **PowerShell távoli eljáráshívás**   | A [PowerShell-távelérést](/powershell/module/microsoft.powershell.core/enable-psremoting) minden Hyper-V-gazdagépen engedélyezni kell. |
| **Hyper-V replika**       | Ha a Hyper-V replikát használja (vagy több kiszolgálóval rendelkezik ugyanazzal a kiszolgáló-azonosítóval), és az eredeti és a replikált kiszolgálókat is felderíti a Azure Migrate használatával, előfordulhat, hogy a Azure Migrate által létrehozott értékelés nem pontos. |

## <a name="server-requirements"></a>Kiszolgálókövetelmények

| **Támogatás**                  | **Részletek**
| :----------------------------- | :------------------- |
| **Operációs rendszer** | Az összes operációs rendszer kiértékelése áttelepítésre végezhető.  |
| **Integrációs szolgáltatások**       | A [Hyper-V integrációs szolgáltatásoknak](/virtualization/hyper-v-on-windows/reference/integration-services) az értékelés alatt álló kiszolgálókon kell futniuk az operációs rendszer adatainak rögzítése érdekében. |
| **Storage** | Helyi lemez, DAS, JBOD, Storage Spaces, CSV, SMB. Ezek a Hyper-V gazdagépek tárolói, amelyeken a VHD-/VHDX vannak tárolva, támogatottak. <br/> Az IDE és az SCSI virtuális vezérlők támogatottak|

## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate-berendezés követelményei

A Azure Migrate a [Azure Migrate berendezést](migrate-appliance.md) használja a felderítéshez és értékeléshez. A készüléket telepítheti a portálról letöltött tömörített Hyper-V VHD-vel vagy egy [PowerShell-parancsfájl](deploy-appliance-script.md)használatával.

- A Hyper-V [készülékre vonatkozó követelmények](migrate-appliance.md#appliance---hyper-v) ismertetése.
- Ismerje meg azokat az URL-címeket, amelyekhez a készüléknek [nyilvános](migrate-appliance.md#public-cloud-urls) és [kormányzati](migrate-appliance.md#government-cloud-urls) felhőkben kell hozzáférnie.
- Azure Government a készüléket [a szkript használatával](deploy-appliance-script-government.md)kell telepítenie.

## <a name="port-access"></a>Port-hozzáférés

A következő táblázat összefoglalja az értékeléshez szükséges portokra vonatkozó követelményeket.

**Eszköz** | **Kapcsolat**
--- | ---
**Berendezés** | Bejövő kapcsolatok a 3389-as TCP-porton, hogy engedélyezze a távoli asztali kapcsolatokat a berendezéssel.<br/><br/> Bejövő kapcsolatok a 44368-as porton a berendezés-kezelő alkalmazás távoli eléréséhez az URL-cím használatával: ``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Kimenő kapcsolatok a 443-as porton (HTTPS), a felderítési és a teljesítménybeli metaadatok küldéséhez Azure Migrate.
**Hyper-V-gazdagép/-fürt** | Bejövő kapcsolat a WinRM port 5985 (HTTP) vagy 5986 (HTTPS) használatával, hogy lekérje a metaadatokat és teljesítményadatokat a Hyper-V-kiszolgálókon a CIM (CIM) munkamenet használatával.

## <a name="agent-based-dependency-analysis-requirements"></a>Ügynök-alapú függőségek elemzésének követelményei

A függőségek [elemzése](concepts-dependency-visualization.md) segít az Azure-ba felmérni és áttelepíteni kívánt helyszíni kiszolgálók közötti függőségek azonosításában. A táblázat összefoglalja az ügynök-alapú függőségi elemzés beállításának követelményeit. A Hyper-V jelenleg csak az ügynök-alapú függőségi vizualizációt támogatja.

**Követelmény** | **Részletek**
--- | --- 
**Üzembe helyezés előtt** | Rendelkeznie kell egy, a projekthez hozzáadott Azure Migrate: felderítési és értékelési eszközzel.<br/><br/>  A függőségi vizualizációt egy Azure Migrate berendezés beállítása után telepítheti a helyszíni kiszolgálók felderítése érdekében.<br/><br/> [Ismerje meg, hogyan](create-manage-projects.md) hozhat létre egy projektet első alkalommal.<br/> [Megtudhatja, hogyan](how-to-assess.md) adhat hozzá Azure Migrate: felderítési és értékelési eszközt egy meglévő projekthez.<br/> Ismerje meg, hogyan állíthatja be a készüléket a [Hyper-V-kiszolgálók](how-to-set-up-appliance-hyper-v.md)felderítéséhez és értékeléséhez.
**Azure Government** | A függőségi vizualizáció nem érhető el Azure Governmentban.
**Naplóelemzés** | A Azure Migrate a [Service Map](../azure-monitor/vm/service-map.md) megoldást használja [Azure monitor naplókban](../azure-monitor/logs/log-query-overview.md) a függőségi vizualizációhoz.<br/><br/> Új vagy meglévő Log Analytics munkaterületet társít egy projekthez. A projekt munkaterülete nem módosítható a hozzáadása után. <br/><br/> A munkaterületnek ugyanahhoz az előfizetéshez kell tartoznia, mint a projektnek.<br/><br/> A munkaterületnek az USA keleti régiójában, Délkelet-Ázsiában vagy Nyugat-európai régióban kell lennie. Más régiókban lévő munkaterületek nem társíthatók projekthez.<br/><br/> A munkaterületnek olyan régióban kell lennie, amelyben a [Service Map támogatott](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> Log Analytics a Azure Migratehoz társított munkaterület az áttelepítési projekt kulcsával és a projekt nevével van megjelölve.
**Szükséges ügynökök** | Az elemezni kívánt összes kiszolgálón telepítse a következő ügynököket:<br/><br/> A [Microsoft monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md).<br/> A [függőségi ügynök](../azure-monitor/agents/agents-overview.md#dependency-agent).<br/><br/> Ha a helyszíni kiszolgálók nem kapcsolódnak az internethez, le kell töltenie és telepítenie kell Log Analytics-átjárót.<br/><br/> További információ a [függőségi ügynök](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) és az [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)telepítéséről.
**Log Analytics munkaterület** | A munkaterületnek ugyanahhoz az előfizetéshez kell tartoznia, mint a projektnek.<br/><br/> Azure Migrate az USA keleti régiójában, Délkelet-Ázsiában és Nyugat-Európában található munkaterületeket támogatja.<br/><br/>  A munkaterületnek olyan régióban kell lennie, amelyben a [Service Map támogatott](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> A projekt munkaterülete nem módosítható a hozzáadása után.
**Költségek** | Az Service Map-megoldás nem számít fel díjat az első 180 napra (az Log Analytics munkaterület projekthez való hozzárendelésének napjától számítva)/<br/><br/> A 180 nap elteltével a standard Log Analytics-díjszabás vonatkozik rá.<br/><br/> A társított Log Analytics-munkaterületen a Service Maptól eltérő bármely megoldás használata esetén a Log Analytics [standard díjait](https://azure.microsoft.com/pricing/details/log-analytics/) kell fizetnie.<br/><br/> A projekt törlésekor a munkaterület nem törlődik vele együtt. A projekt törlését követően a Service Map használat nem ingyenes, és az egyes csomópontok a Log Analytics munkaterület fizetős szintjének megfelelően lesznek felszámítva.<br/><br/>Ha olyan projektekkel rendelkezik, amelyeket a Azure Migrate általános elérhetősége előtt hozott létre (GA-28. február 2018.), előfordulhat, hogy további Service Map díjat is felmerült. Ahhoz, hogy a fizetés csak 180 nap után legyen elérhető, javasoljuk, hogy hozzon létre egy új projektet, mivel a már meglévő munkaterületek továbbra is díjkötelesek.
**Felügyelet** | Amikor ügynököt regisztrál a munkaterületre, a projekt által megadott azonosítót és kulcsot használja.<br/><br/> A Log Analytics munkaterületet Azure Migraten kívül is használhatja.<br/><br/> Ha törli a társított projektet, a munkaterület nem törlődik automatikusan. [Törölje manuálisan](../azure-monitor/logs/manage-access.md).<br/><br/> Ne törölje a Azure Migrate által létrehozott munkaterületet, hacsak nem törli a projektet. Ha így tesz, a függőségi vizualizáció funkció nem a várt módon fog működni.
**Internetkapcsolat** | Ha a kiszolgálók nem csatlakoznak az internethez, akkor telepítenie kell az Log Analytics-átjárót.
**Azure Government** | Az ügynök-alapú függőség elemzése nem támogatott.

## <a name="next-steps"></a>Következő lépések

[Felkészülés a Hyper-V-t futtató kiszolgálók értékelésére](./tutorial-discover-hyper-v.md).