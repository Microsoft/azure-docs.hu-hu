---
title: A fizikai felderítés és értékelés támogatása a Azure Migrateban
description: Ismerje meg a fizikai felderítés és értékelés támogatását Azure Migrate felderítéssel és értékeléssel
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: b62160861f686c6ea5a8ebfd03d904da2ad5d80a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869446"
---
# <a name="support-matrix-for-physical-server-discovery-and-assessment"></a>A fizikai kiszolgáló felderítésének és értékelésének támogatási mátrixa 

Ez a cikk az előfeltételeket és a támogatási követelményeket összegzi, ha a fizikai kiszolgálókat az Azure-ba való áttelepítésre értékeli, a [Azure Migrate: Discovery and Assessment](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) Tool használatával. Ha fizikai kiszolgálókat szeretne áttelepíteni az Azure-ba, tekintse át az [áttelepítési támogatási mátrixot](migrate-support-matrix-physical-migration.md).

A fizikai kiszolgálók értékeléséhez létre kell hoznia egy projektet, és hozzá kell adnia a Azure Migrate: Discovery and Assessment eszközt a projekthez. Az eszköz hozzáadása után üzembe helyezi a [Azure Migrate berendezést](migrate-appliance.md). A készülék folyamatosan felfedi a helyszíni kiszolgálókat, és a kiszolgálók metaadatait és teljesítményadatait az Azure-ba küldi. A felderítés befejezése után a felderített kiszolgálókat csoportokba gyűjtheti, és értékelést futtathat egy csoport számára.

## <a name="limitations"></a>Korlátozások

**Támogatás** | **Részletek**
--- | ---
**Értékelési korlátok** | Egyetlen [projektben](migrate-support-matrix.md#project)akár 35 000 fizikai kiszolgálót is felderítheti és felbecsülheti.
**Projekt korlátai** | Egy Azure-előfizetésben több projektet is létrehozhat. A fizikai kiszolgálókon kívül a projektek a VMware-en és a Hyper-V-n lévő kiszolgálókat is magukban foglalhatják, az egyes eszközökre vonatkozó értékelési korlátokig.
**Felderítés** | A Azure Migrate berendezés akár 1000 fizikai kiszolgálót is képes észlelni.
**Értékelés** | Egyetlen csoportban akár 35 000-kiszolgálót is hozzáadhat.<br/><br/> Egyetlen értékeléssel akár 35 000-kiszolgálót is megvizsgálhat.

[További](concepts-assessment-calculation.md) információ az értékelésekről.

## <a name="physical-server-requirements"></a>Fizikai kiszolgáló követelmények

**Fizikai kiszolgáló üzembe helyezése:** A fizikai kiszolgáló önálló vagy fürtben is üzembe helyezhető.

**Operációs rendszer:** Az összes Windows-és Linux-operációs rendszer kiértékelése áttelepítésre lehetséges.

**Engedélyek**

- Windows-kiszolgálók esetén használjon tartományi fiókot tartományhoz csatlakoztatott kiszolgálók esetében, valamint egy helyi fiókot olyan kiszolgálók esetében, amelyek nem csatlakoznak a tartományhoz. A felhasználói fiókot ezekhez a csoportokhoz kell hozzáadni: távfelügyeleti felhasználók, teljesítményfigyelő felhasználók és teljesítménynapló-felhasználók.
- Linux-kiszolgálók esetén rendszergazdai fiókra van szüksége a felderíteni kívánt Linux-kiszolgálókon. Másik lehetőségként az alábbi parancsokkal állíthatja be a nem gyökérszintű fiókot a szükséges képességekkel:

**Parancs** | **Cél**
--- | --- |
setcap CAP_DAC_READ_SEARCH + EIP/usr/sbin/fdisk <br></br> setcap CAP_DAC_READ_SEARCH + EIP/sbin/fdisk _(ha a/usr/sbin/fdisk nincs jelen)_ | A lemez konfigurációs adatainak összegyűjtése
setcap "cap_dac_override, cap_dac_read_search, cap_fowner, cap_fsetid, cap_setuid,<br>cap_setpcap, cap_net_bind_service, cap_net_admin, cap_sys_chroot, cap_sys_admin,<br>cap_sys_resource, cap_audit_control, cap_setfcap = + EIP "/sbin/LVM | A lemez teljesítményadatait tartalmazó adatok gyűjtése
setcap CAP_DAC_READ_SEARCH + EIP/usr/sbin/dmidecode | A BIOS sorozatszámának összegyűjtése
chmod a + r/sys/Class/DMI/ID/product_uuid | A BIOS GUID-azonosító gyűjtése

## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate-berendezés követelményei

A Azure Migrate a [Azure Migrate berendezést](migrate-appliance.md) használja a felderítéshez és értékeléshez. A fizikai kiszolgálók berendezése virtuális gépen vagy fizikai kiszolgálón is futtatható.

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

A függőségek [elemzése](concepts-dependency-visualization.md) segít az Azure-ba felmérni és áttelepíteni kívánt helyszíni kiszolgálók közötti függőségek azonosításában. A táblázat összefoglalja az ügynök-alapú függőségi elemzés beállításának követelményeit. A fizikai kiszolgálók jelenleg csak az ügynök-alapú függőségek elemzését támogatják.

**Követelmény** | **Részletek**
--- | ---
**Üzembe helyezés előtt** | Rendelkeznie kell egy, a projekthez hozzáadott Azure Migrate: felderítési és értékelési eszközzel.<br/><br/>  A függőségi vizualizáció üzembe helyezése egy Azure Migrate berendezés beállítása után a helyszíni kiszolgálók felderítése érdekében<br/><br/> [Ismerje meg, hogyan](create-manage-projects.md) hozhat létre egy projektet első alkalommal.<br/> [Megtudhatja, hogyan](how-to-assess.md) adhat hozzá egy értékelési eszközt egy meglévő projekthez.<br/> Ismerje meg, hogyan állíthatja be a Azure Migrate készüléket a [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)vagy fizikai kiszolgálók értékeléséhez.
**Azure Government** | A függőségi vizualizáció nem érhető el Azure Governmentban.
**Naplóelemzés** | A Azure Migrate a [Service Map](../azure-monitor/vm/service-map.md) megoldást használja [Azure monitor naplókban](../azure-monitor/logs/log-query-overview.md) a függőségi vizualizációhoz.<br/><br/> Új vagy meglévő Log Analytics munkaterületet társít egy projekthez. A projekt munkaterülete nem módosítható a hozzáadása után. <br/><br/> A munkaterületnek ugyanahhoz az előfizetéshez kell tartoznia, mint a projektnek.<br/><br/> A munkaterületnek az USA keleti régiójában, Délkelet-Ázsiában vagy Nyugat-európai régióban kell lennie. Más régiókban lévő munkaterületek nem társíthatók projekthez.<br/><br/> A munkaterületnek olyan régióban kell lennie, amelyben a [Service Map támogatott](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> Log Analytics a Azure Migratehoz társított munkaterület az áttelepítési projekt kulcsával és a projekt nevével van megjelölve.
**Szükséges ügynökök** | Az elemezni kívánt összes kiszolgálón telepítse a következő ügynököket:<br/><br/> A [Microsoft monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md).<br/> A [függőségi ügynök](../azure-monitor/agents/agents-overview.md#dependency-agent).<br/><br/> Ha a helyszíni kiszolgálók nem kapcsolódnak az internethez, le kell töltenie és telepítenie kell Log Analytics-átjárót.<br/><br/> További információ a [függőségi ügynök](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) és az [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)telepítéséről.
**Log Analytics munkaterület** | A munkaterületnek ugyanahhoz az előfizetéshez kell tartoznia.<br/><br/> Azure Migrate az USA keleti régiójában, Délkelet-Ázsiában és Nyugat-Európában található munkaterületeket támogatja.<br/><br/>  A munkaterületnek olyan régióban kell lennie, amelyben a [Service Map támogatott](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> A projekt munkaterülete nem módosítható a hozzáadása után.
**Költségek** | Az Service Map-megoldás nem számít fel díjat az első 180 napra (az Log Analytics munkaterület projekthez való hozzárendelésének napjától számítva)/<br/><br/> A 180 nap elteltével a standard Log Analytics-díjszabás vonatkozik rá.<br/><br/> A társított Log Analytics-munkaterületen a Service Maptól eltérő bármely megoldás használata esetén a Log Analytics [standard díjait](https://azure.microsoft.com/pricing/details/log-analytics/) kell fizetnie.<br/><br/> A projekt törlésekor a munkaterület nem törlődik vele együtt. A projekt törlését követően a Service Map használat nem ingyenes, és az egyes csomópontok a Log Analytics munkaterület fizetős szintjének megfelelően lesznek felszámítva.<br/><br/>Ha olyan projektekkel rendelkezik, amelyeket a Azure Migrate általános elérhetősége előtt hozott létre (GA-28. február 2018.), előfordulhat, hogy további Service Map díjat is felmerült. Ahhoz, hogy a fizetés csak 180 nap után legyen elérhető, javasoljuk, hogy hozzon létre egy új projektet, mivel a már meglévő munkaterületek továbbra is díjkötelesek.
**Felügyelet** | Amikor ügynököt regisztrál a munkaterületre, a projekt által megadott azonosítót és kulcsot használja.<br/><br/> A Log Analytics munkaterületet Azure Migraten kívül is használhatja.<br/><br/> Ha törli a társított projektet, a munkaterület nem törlődik automatikusan. [Törölje manuálisan](../azure-monitor/logs/manage-access.md).<br/><br/> Ne törölje a Azure Migrate által létrehozott munkaterületet, hacsak nem törli a projektet. Ha így tesz, a függőségi vizualizáció funkció nem a várt módon fog működni.
**Internetkapcsolat** | Ha a kiszolgálók nem csatlakoznak az internethez, akkor telepítenie kell az Log Analytics-átjárót.
**Azure Government** | Az ügynök-alapú függőség elemzése nem támogatott.

## <a name="next-steps"></a>Következő lépések

[Felkészülés a fizikai felderítésre és értékelésre](./tutorial-discover-physical.md).