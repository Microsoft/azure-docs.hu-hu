---
title: A fizikai kiszolgáló értékelésének támogatása Azure Migrate
description: Tudnivalók a fizikai kiszolgálók értékelésének támogatásáról Azure Migrate Server Assessment szolgáltatással
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: cb5a1a51a7d622c1b0a605d155ade2f08022ab67
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592482"
---
# <a name="support-matrix-for-physical-server-assessment"></a>A fizikai kiszolgáló értékelésének támogatási mátrixa 

Ez a cikk az előfeltételeket és a támogatási követelményeket összegzi, ha a fizikai kiszolgálókat az Azure-ba való áttelepítésre értékeli a [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) Tool használatával. Ha fizikai kiszolgálókat szeretne áttelepíteni az Azure-ba, tekintse át az [áttelepítési támogatási mátrixot](migrate-support-matrix-physical-migration.md).


A fizikai kiszolgálók értékeléséhez létre kell hoznia egy Azure Migrate projektet, és hozzá kell adnia a kiszolgáló-értékelési eszközt a projekthez. Az eszköz hozzáadása után üzembe helyezi a [Azure Migrate berendezést](migrate-appliance.md). A készülék folyamatosan felfedi a helyszíni gépeket, és számítógép-metaadatokat és teljesítményadatokat küld az Azure-nak. A felderítés befejezése után összegyűjtheti a felderített gépeket csoportokba, és értékelést futtathat egy csoport számára.


## <a name="limitations"></a>Korlátozások

**Támogatás** | **Részletek**
--- | ---
**Értékelési korlátok** | Akár 35 000 fizikai kiszolgálót is felfedezheti és felderítheti egyetlen [Azure Migrate projektben](migrate-support-matrix.md#azure-migrate-projects).
**Projekt korlátai** | Egy Azure-előfizetésben több projektet is létrehozhat. A fizikai kiszolgálókon kívül egy projekt is tartalmazhatja a VMware virtuális gépeket és a Hyper-V virtuális gépeket, az egyes alkalmazásokra vonatkozó értékelési korlátokig.
**Felderítés** | A Azure Migrate berendezés akár 1000 fizikai kiszolgálót is képes észlelni.
**Értékelés** | Egyetlen csoportban legfeljebb 35 000 gépet adhat hozzá.<br/><br/> Egyetlen értékeléssel akár 35 000 gépet is megvizsgálhat.

[További](concepts-assessment-calculation.md) információ az értékelésekről.

## <a name="physical-server-requirements"></a>Fizikai kiszolgáló követelmények

**Fizikai kiszolgáló üzembe helyezése:** A fizikai kiszolgáló önálló vagy fürtben is üzembe helyezhető.

**Operációs rendszer:** Az összes Windows-és Linux-operációs rendszer kiértékelése áttelepítésre lehetséges.

**Engedélyek**
- Windows-kiszolgálók esetében tartományi fiókot használjon a tartományhoz csatlakoztatott gépekhez, valamint egy helyi fiókot a tartományhoz nem csatlakoztatott gépekhez. A felhasználói fiókot ezekhez a csoportokhoz kell hozzáadni: távfelügyeleti felhasználók, teljesítményfigyelő felhasználók és teljesítménynapló-felhasználók.
- Linux-kiszolgálók esetén rendszergazdai fiókra van szüksége a felderíteni kívánt Linux-kiszolgálókon. Másik lehetőségként az alábbi parancsokkal állíthatja be a nem gyökérszintű fiókot a szükséges képességekkel:

**Parancs** | **Cél**
--- | --- |
setcap CAP_DAC_READ_SEARCH + EIP/usr/sbin/fdisk <br></br> setcap CAP_DAC_READ_SEARCH + EIP/sbin/fdisk _(ha a/usr/sbin/fdisk nincs jelen)_ | A lemez konfigurációs adatainak összegyűjtése
setcap "cap_dac_override, cap_dac_read_search, cap_fowner, cap_fsetid, cap_setuid,<br>cap_setpcap, cap_net_bind_service, cap_net_admin, cap_sys_chroot, cap_sys_admin,<br>cap_sys_resource, cap_audit_control, cap_setfcap = + EIP "/sbin/LVM | A lemez teljesítményadatait tartalmazó adatok gyűjtése
setcap CAP_DAC_READ_SEARCH + EIP/usr/sbin/dmidecode | A BIOS sorozatszámának összegyűjtése
chmod a + r/sys/Class/DMI/ID/product_uuid | A BIOS GUID-azonosító gyűjtése



## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate-berendezés követelményei

A Azure Migrate a [Azure Migrate berendezést](migrate-appliance.md) használja a felderítéshez és értékeléshez. A fizikai kiszolgálók berendezése virtuális gépen vagy fizikai gépen is futtatható. 

- Ismerje meg a fizikai kiszolgálók [készülékre vonatkozó követelményeit](migrate-appliance.md#appliance---physical) .
- Ismerje meg azokat az URL-címeket, amelyekhez a készüléknek [nyilvános](migrate-appliance.md#public-cloud-urls) és [kormányzati](migrate-appliance.md#government-cloud-urls) felhőkben kell hozzáférnie.
- A készüléket a Azure Portalból letöltött [PowerShell-parancsfájl](how-to-set-up-appliance-physical.md) használatával állíthatja be.
A Azure Government-ben telepítse a készüléket a [parancsfájl használatával](deploy-appliance-script-government.md).

## <a name="port-access"></a>Port-hozzáférés

A következő táblázat összefoglalja az értékeléshez szükséges portokra vonatkozó követelményeket.

**Eszköz** | **Kapcsolat**
--- | ---
**Berendezés** | Bejövő kapcsolatok a 3389-as TCP-porton, hogy engedélyezze a távoli asztali kapcsolatokat a berendezéssel.<br/><br/> Bejövő kapcsolatok a 44368-as porton a berendezés-kezelő alkalmazás távoli eléréséhez az URL-cím használatával: ``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Kimenő kapcsolatok a 443-as porton (HTTPS), a felderítési és a teljesítménybeli metaadatok küldéséhez Azure Migrate.
**Fizikai kiszolgálók** | **Windows:** A WinRM port 5985 (HTTP) vagy 5986 (HTTPS) bejövő kapcsolata a konfiguráció és a teljesítmény metaadatainak lekéréséhez a Windows-kiszolgálókról. <br/><br/> **Linux:**  Bejövő kapcsolatok a 22-es porton (TCP) a konfiguráció és a teljesítmény metaadatainak lekéréséhez Linux-kiszolgálókról. |

## <a name="agent-based-dependency-analysis-requirements"></a>Ügynök-alapú függőségek elemzésének követelményei

A függőségek [elemzése](concepts-dependency-visualization.md) segít azonosítani az Azure-ba felmérni és áttelepíteni kívánt helyszíni gépek közötti függőségeket. A táblázat összefoglalja az ügynök-alapú függőségi elemzés beállításának követelményeit. A fizikai kiszolgálók jelenleg csak az ügynök-alapú függőségek elemzését támogatják.

**Követelmény** | **Részletek** 
--- | --- 
**Üzembe helyezés előtt** | Rendelkeznie kell egy Azure Migrate-projekttel, és a kiszolgáló-értékelési eszközzel hozzáadva a projekthez.<br/><br/>  A függőségi vizualizáció üzembe helyezése egy Azure Migrate berendezés beállítása után a helyszíni gépek felderítése érdekében<br/><br/> [Ismerje meg, hogyan](create-manage-projects.md) hozhat létre egy projektet első alkalommal.<br/> [Megtudhatja, hogyan](how-to-assess.md) adhat hozzá egy értékelési eszközt egy meglévő projekthez.<br/> Ismerje meg, hogyan állíthatja be a Azure Migrate készüléket a [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)vagy fizikai kiszolgálók értékeléséhez.
**Azure Government** | A függőségi vizualizáció nem érhető el Azure Governmentban.
**Naplóelemzés** | A Azure Migrate a [Service Map](../azure-monitor/vm/service-map.md) megoldást használja [Azure monitor naplókban](../azure-monitor/logs/log-query-overview.md) a függőségi vizualizációhoz.<br/><br/> Új vagy meglévő Log Analytics munkaterületet társít egy Azure Migrate projekthez. Egy Azure Migrate projekt munkaterülete nem módosítható a hozzáadása után. <br/><br/> A munkaterületnek ugyanahhoz az előfizetéshez kell tartoznia, mint a Azure Migrate projektnek.<br/><br/> A munkaterületnek az USA keleti régiójában, Délkelet-Ázsiában vagy Nyugat-európai régióban kell lennie. Más régiókban lévő munkaterületek nem társíthatók projekthez.<br/><br/> A munkaterületnek olyan régióban kell lennie, amelyben a [Service Map támogatott](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> Log Analytics a Azure Migratehoz társított munkaterület az áttelepítési projekt kulcsával és a projekt nevével van megjelölve.
**Szükséges ügynökök** | Telepítse az alábbi ügynököket minden egyes elemezni kívánt gépen:<br/><br/> A [Microsoft monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md).<br/> A [függőségi ügynök](../azure-monitor/agents/agents-overview.md#dependency-agent).<br/><br/> Ha a helyszíni gépek nem csatlakoznak az internethez, le kell töltenie és telepítenie kell Log Analytics-átjárót.<br/><br/> További információ a [függőségi ügynök](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) és az [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)telepítéséről.
**Log Analytics munkaterület** | A munkaterületnek ugyanahhoz az előfizetéshez kell tartoznia, mint a Azure Migrate projektnek.<br/><br/> Azure Migrate az USA keleti régiójában, Délkelet-Ázsiában és Nyugat-Európában található munkaterületeket támogatja.<br/><br/>  A munkaterületnek olyan régióban kell lennie, amelyben a [Service Map támogatott](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> Egy Azure Migrate projekt munkaterülete nem módosítható a hozzáadása után.
**Költségek** | Az Service Map-megoldás nem számít fel díjat az első 180 napra (az Log Analytics munkaterület Azure Migrate projekthez való hozzárendelésének napjától számítva)/<br/><br/> A 180 nap elteltével a standard Log Analytics-díjszabás vonatkozik rá.<br/><br/> A társított Log Analytics-munkaterületen a Service Maptól eltérő bármely megoldás használata esetén a Log Analytics [standard díjait](https://azure.microsoft.com/pricing/details/log-analytics/) kell fizetnie.<br/><br/> A Azure Migrate-projekt törlésekor a munkaterület nem törlődik vele együtt. A projekt törlését követően a Service Map használat nem ingyenes, és az egyes csomópontok a Log Analytics munkaterület fizetős szintjének megfelelően lesznek felszámítva.<br/><br/>Ha olyan projektekkel rendelkezik, amelyeket a Azure Migrate általános elérhetősége előtt hozott létre (GA-28. február 2018.), előfordulhat, hogy további Service Map díjat is felmerült. Ahhoz, hogy a fizetés csak 180 nap után legyen elérhető, javasoljuk, hogy hozzon létre egy új projektet, mivel a már meglévő munkaterületek továbbra is díjkötelesek.
**Felügyelet** | Amikor ügynököt regisztrál a munkaterületre, a Azure Migrate-projekt által megadott azonosítót és kulcsot használja.<br/><br/> A Log Analytics munkaterületet Azure Migraten kívül is használhatja.<br/><br/> Ha törli a társított Azure Migrate projektet, a munkaterület nem törlődik automatikusan. [Törölje manuálisan](../azure-monitor/logs/manage-access.md).<br/><br/> Ne törölje a Azure Migrate által létrehozott munkaterületet, hacsak nem törli a Azure Migrate projektet. Ha így tesz, a függőségi vizualizáció funkció nem a várt módon fog működni.
**Internetkapcsolat** | Ha a gépek nem kapcsolódnak az internethez, akkor telepítenie kell a Log Analytics átjárót.
**Azure Government** | Az ügynök-alapú függőség elemzése nem támogatott.

## <a name="next-steps"></a>Következő lépések

[Felkészülés a fizikai kiszolgáló értékelésére](./tutorial-discover-physical.md).