---
title: A Hyper-V értékelésének támogatása a Azure Migrateban
description: Ismerkedjen meg a Hyper-V felmérés támogatásával Azure Migrate kiszolgáló értékelésével
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/14/2020
ms.openlocfilehash: 4407f9544a7d3a19e3ff7d404669fdb27b881d30
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590630"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>A Hyper-V felmérés támogatási mátrixa

Ez a cikk a Hyper-V virtuális gépek Azure-ba történő áttelepítésére vonatkozó előfeltételeket és támogatási követelményeket összegzi a [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) Tool használatával. Ha a Hyper-V virtuális gépeket az Azure-ba szeretné áttelepíteni, tekintse át az [áttelepítési támogatási mátrixot](migrate-support-matrix-hyper-v-migration.md).

A Hyper-V virtuális gép értékelésének beállításához létre kell hoznia egy Azure Migrate projektet, és hozzá kell adnia a kiszolgáló-értékelési eszközt a projekthez. Az eszköz hozzáadása után üzembe helyezi a [Azure Migrate berendezést](migrate-appliance.md). A készülék folyamatosan felfedi a helyszíni gépeket, és számítógép-metaadatokat és teljesítményadatokat küld az Azure-nak. A felderítés befejezése után összegyűjtheti a felderített gépeket csoportokba, és értékelést futtathat egy csoport számára.


## <a name="limitations"></a>Korlátozások

**Támogatás** | **Részletek**
--- | ---
**Értékelési korlátok** | Egyetlen [Azure Migrate-projektben](migrate-support-matrix.md#azure-migrate-projects)akár 35 000 Hyper-V virtuális gépet is felfedezhet és felderítheti.
**Projekt korlátai** | Egy Azure-előfizetésben több projektet is létrehozhat. A Hyper-V-alapú virtuális gépeken kívül a projektek tartalmazhatják a VMware virtuális gépeket és a fizikai kiszolgálókat is, az egyes alkalmazásokra vonatkozó értékelési korlátokig.
**Felderítés** | A Azure Migrate készülék legfeljebb 5000 Hyper-V virtuális gépet képes felderíteni.<br/><br/> A készülék legfeljebb 300 Hyper-V gazdagéphez tud csatlakozni.
**Értékelés** | Egyetlen csoportban legfeljebb 35 000 gépet adhat hozzá.<br/><br/> Egy csoportra vonatkozóan akár 35 000 virtuális gépet is megvizsgálhat egyetlen értékeléssel.

[További](concepts-assessment-calculation.md) információ az értékelésekről.



## <a name="hyper-v-host-requirements"></a>A Hyper-V gazdagépre vonatkozó követelmények

| **Támogatás**                | **Részletek**               
| :-------------------       | :------------------- |
| **Hyper-V gazdagép**       | A Hyper-V-gazdagép önálló vagy fürtben is üzembe helyezhető.<br/><br/> A Hyper-V-gazdagép a Windows Server 2019, a Windows Server 2016 vagy a Windows Server 2012 R2 rendszert futtathatja. Ezen operációs rendszerek Server Core telepítése is támogatott. <br/>A Windows Server 2012-t futtató Hyper-V-gazdagépeken található virtuális gépeket nem lehet értékelni.
| **Engedélyek**           | Rendszergazdai engedélyekkel kell rendelkeznie a Hyper-V-gazdagépen. <br/> Ha nem szeretne rendszergazdai jogosultságokat rendelni, hozzon létre egy helyi vagy tartományi felhasználói fiókot, és adja hozzá a felhasználói fiókot ezekhez a csoportokhoz – a távfelügyeleti felhasználók, a Hyper-V-rendszergazdák és a Teljesítményfigyelő felhasználói számára. |
| **PowerShell távoli eljáráshívás**   | A [PowerShell-távelérést](/powershell/module/microsoft.powershell.core/enable-psremoting?view=powershell-7) minden Hyper-V-gazdagépen engedélyezni kell. |
| **Hyper-V replika**       | Ha a Hyper-V replikát használja (vagy több virtuális GÉPET ugyanazzal a virtuálisgép-azonosítóval), és az eredeti és a replikált virtuális gépeket Azure Migrate használatával észleli, előfordulhat, hogy a Azure Migrate által létrehozott értékelés nem pontos. |


## <a name="vm-requirements"></a>VIRTUÁLIS gépekre vonatkozó követelmények

| **Támogatás**                  | **Részletek**               
| :----------------------------- | :------------------- |
| **Operációs rendszer** | Az összes operációs rendszer kiértékelése áttelepítésre végezhető.  |
| **Integrációs szolgáltatások**       | Az operációs rendszer adatainak rögzítéséhez a [Hyper-V integrációs szolgáltatásoknak](/virtualization/hyper-v-on-windows/reference/integration-services) olyan virtuális gépeken kell futniuk, amelyeket Ön értékelni fog. |
| **Storage** | Helyi lemez, DAS, JBOD, Storage Spaces, CSV, SMB. Ezek a Hyper-V gazdagép-tárolók, amelyeken a VHD-/VHDX vannak tárolva, támogatottak. <br/> Az IDE és az SCSI virtuális vezérlők támogatottak| 

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
**Hyper-V-gazdagép/-fürt** | Bejövő kapcsolat a WinRM port 5985 (HTTP) vagy 5986 (HTTPS) használatával, hogy lekérje a metaadatokat és teljesítményadatokat a Hyper-V virtuális gépek számára CIM (CIM) munkamenettel.

## <a name="agent-based-dependency-analysis-requirements"></a>Ügynök-alapú függőségek elemzésének követelményei

A függőségek [elemzése](concepts-dependency-visualization.md) segít azonosítani az Azure-ba felmérni és áttelepíteni kívánt helyszíni gépek közötti függőségeket. A táblázat összefoglalja az ügynök-alapú függőségi elemzés beállításának követelményeit. A Hyper-V jelenleg csak az ügynök-alapú függőségi vizualizációt támogatja. 

**Követelmény** | **Részletek** 
--- | --- 
**Üzembe helyezés előtt** | Rendelkeznie kell egy Azure Migrate-projekttel, és a kiszolgáló-értékelési eszközzel hozzáadva a projekthez.<br/><br/>  A függőségi vizualizáció üzembe helyezése egy Azure Migrate berendezés beállítása után a helyszíni gépek felderítése érdekében<br/><br/> [Ismerje meg, hogyan](create-manage-projects.md) hozhat létre egy projektet első alkalommal.<br/> [Megtudhatja, hogyan](how-to-assess.md) adhat hozzá egy értékelési eszközt egy meglévő projekthez.<br/> Ismerje meg, hogyan állíthatja be a Azure Migrate berendezést a [Hyper-V virtuális gépek](how-to-set-up-appliance-hyper-v.md)kiértékeléséhez.
**Azure Government** | A függőségi vizualizáció nem érhető el Azure Governmentban.
**Naplóelemzés** | A Azure Migrate a [Service Map](../azure-monitor/vm/service-map.md) megoldást használja [Azure monitor naplókban](../azure-monitor/logs/log-query-overview.md) a függőségi vizualizációhoz.<br/><br/> Új vagy meglévő Log Analytics munkaterületet társít egy Azure Migrate projekthez. Egy Azure Migrate projekt munkaterülete nem módosítható a hozzáadása után. <br/><br/> A munkaterületnek ugyanahhoz az előfizetéshez kell tartoznia, mint a Azure Migrate projektnek.<br/><br/> A munkaterületnek az USA keleti régiójában, Délkelet-Ázsiában vagy Nyugat-európai régióban kell lennie. Más régiókban lévő munkaterületek nem társíthatók projekthez.<br/><br/> A munkaterületnek olyan régióban kell lennie, amelyben a [Service Map támogatott](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> Log Analytics a Azure Migratehoz társított munkaterület az áttelepítési projekt kulcsával és a projekt nevével van megjelölve.
**Szükséges ügynökök** | Telepítse az alábbi ügynököket minden egyes elemezni kívánt gépen:<br/><br/> A [Microsoft monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md).<br/> A [függőségi ügynök](../azure-monitor/agents/agents-overview.md#dependency-agent).<br/><br/> Ha a helyszíni gépek nem csatlakoznak az internethez, le kell töltenie és telepítenie kell Log Analytics-átjárót.<br/><br/> További információ a [függőségi ügynök](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) és az [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)telepítéséről.
**Log Analytics munkaterület** | A munkaterületnek ugyanahhoz az előfizetéshez kell tartoznia, mint a Azure Migrate projektnek.<br/><br/> Azure Migrate az USA keleti régiójában, Délkelet-Ázsiában és Nyugat-Európában található munkaterületeket támogatja.<br/><br/>  A munkaterületnek olyan régióban kell lennie, amelyben a [Service Map támogatott](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> Egy Azure Migrate projekt munkaterülete nem módosítható a hozzáadása után.
**Költségek** | Az Service Map-megoldás nem számít fel díjat az első 180 napra (az Log Analytics munkaterület Azure Migrate projekthez való hozzárendelésének napjától számítva)/<br/><br/> A 180 nap elteltével a standard Log Analytics-díjszabás vonatkozik rá.<br/><br/> A társított Log Analytics-munkaterületen a Service Maptól eltérő bármely megoldás használata esetén a Log Analytics [standard díjait](https://azure.microsoft.com/pricing/details/log-analytics/) kell fizetnie.<br/><br/> A Azure Migrate-projekt törlésekor a munkaterület nem törlődik vele együtt. A projekt törlését követően a Service Map használat nem ingyenes, és az egyes csomópontok a Log Analytics munkaterület fizetős szintjének megfelelően lesznek felszámítva.<br/><br/>Ha olyan projektekkel rendelkezik, amelyeket a Azure Migrate általános elérhetősége előtt hozott létre (GA-28. február 2018.), előfordulhat, hogy további Service Map díjat is felmerült. Ahhoz, hogy a fizetés csak 180 nap után legyen elérhető, javasoljuk, hogy hozzon létre egy új projektet, mivel a már meglévő munkaterületek továbbra is díjkötelesek.
**Felügyelet** | Amikor ügynököt regisztrál a munkaterületre, a Azure Migrate-projekt által megadott azonosítót és kulcsot használja.<br/><br/> A Log Analytics munkaterületet Azure Migraten kívül is használhatja.<br/><br/> Ha törli a társított Azure Migrate projektet, a munkaterület nem törlődik automatikusan. [Törölje manuálisan](../azure-monitor/logs/manage-access.md).<br/><br/> Ne törölje a Azure Migrate által létrehozott munkaterületet, hacsak nem törli a Azure Migrate projektet. Ha így tesz, a függőségi vizualizáció funkció nem a várt módon fog működni.
**Internetkapcsolat** | Ha a gépek nem kapcsolódnak az internethez, akkor telepítenie kell a Log Analytics átjárót.
**Azure Government** | Az ügynök-alapú függőség elemzése nem támogatott.

## <a name="next-steps"></a>Következő lépések

[Felkészülés a Hyper-V virtuális gépek felmérésére](./tutorial-discover-hyper-v.md)