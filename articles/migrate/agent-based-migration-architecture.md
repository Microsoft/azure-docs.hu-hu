---
title: Ügynök-alapú áttelepítés Azure Migrate kiszolgáló áttelepítésekor
description: Áttekintést nyújt az ügynök alapú VMware VM-Migrálás Azure Migrate-ben való áttelepítéséről.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: c605c21307cda874f34ae5ea9f4e4959e5e6c183
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97861951"
---
# <a name="agent-based-migration-architecture"></a>Ügynökalapú migrálási architektúra

Ez a cikk áttekintést nyújt a VMware virtuális gépek ügynök alapú replikálásához használt architektúráról és folyamatokról a [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-assessment-tool) Tool eszközzel.

A Azure Migrate használatával: a kiszolgáló áttelepítése a VMware virtuális gépeket több lehetőséggel is replikálhatja:

- Telepítse át a virtuális gépeket az ügynök-alapú replikáció használatával a jelen cikkben leírtak szerint.
- VMware virtuális gépek migrálása ügynök nélküli replikáció használatával. Ez áttelepíti a virtuális gépeket anélkül, hogy bármit telepítenie kellene rájuk.

További információ a VMware virtuális gépek áttelepítési módszereinek [kiválasztásáról és összehasonlításáról](server-migrate-overview.md) . 


## <a name="agent-based-migration"></a>Ügynök alapú áttelepítés

Az ügynök-alapú áttelepítés a helyszíni VMware virtuális gépek és fizikai kiszolgálók Azure-ba történő áttelepítésére szolgál. Más helyszíni virtualizált kiszolgálók, valamint magán-és nyilvános Felhőbeli virtuális gépek, például AWS-példányok és GCP virtuális gépek áttelepítésére is használható. Az ügynök-alapú áttelepítés Azure Migrate a [Azure site Recovery](../site-recovery/site-recovery-overview.md) szolgáltatás néhány háttérbeli funkcióját használja.


## <a name="architectural-components"></a>Az architektúra összetevői

A diagram az ügynök-alapú áttelepítés során érintett összetevőket ábrázolja.

![A diagram az ügynök alapú áttelepítés összetevőit jeleníti meg, amelyeket a táblázat ismertet.](./media/agent-based-replication-architecture/architecture.png)

A tábla összegzi az ügynök alapú áttelepítéshez használt összetevőket.

**Összetevő** | **Részletek** | **Telepítés**
--- | --- | ---
**Replikációs berendezés** | A replikációs berendezés (konfigurációs kiszolgáló/folyamat kiszolgáló) egy helyszíni számítógép, amely hidat képez a helyszíni környezet és a kiszolgáló áttelepítése között. A készülék felfedi a helyszíni számítógép leltározását, így a kiszolgáló áttelepítése képes a replikáció és az áttelepítés koordinálására. A készülék két összetevőből áll:<br/><br/> **Konfigurációs kiszolgáló**: Kapcsolódás a kiszolgáló áttelepítéséhez és a replikáció koordinálása.<br/> **Process Server**: kezeli az adatreplikációt. A Process Server adatokat fogad, tömöríti és titkosítja, és elküldi az Azure-nak. Az Azure-ban a kiszolgáló áttelepítése felügyelt lemezekre írja az adatot. | Alapértelmezés szerint a Process Server a replikációs berendezés konfigurációs kiszolgálójával együtt települ.
**Mobilitási szolgáltatás** | A mobilitási szolgáltatás egy olyan ügynök, amely minden replikálni és áttelepíteni kívánt gépre telepítve van. Replikációs adatokat küld a gépről a Process Server rendszernek. | A mobilitási szolgáltatás különböző verzióihoz tartozó telepítési fájlok a replikációs berendezésen találhatók. Töltse le és telepítse a szükséges ügynököt a replikálni kívánt gép operációs rendszerének és verziójának megfelelően.

## <a name="mobility-service-installation"></a>A mobilitási szolgáltatás telepítése

A mobilitási szolgáltatást a következő módszerekkel telepítheti:

- **Leküldéses telepítés**: a folyamat-kiszolgáló telepíti a mobilitási szolgáltatást, ha engedélyezi a gép védelmét. 
- **Manuális telepítés**: a mobilitási szolgáltatást manuálisan is telepítheti az egyes gépeken a felhasználói felületen vagy a parancssorban.

A mobilitási szolgáltatás kommunikál a replikációs berendezéssel és a replikált számítógépekkel. Ha a replikációs berendezésen fut a víruskereső szoftver, a kiszolgálókat vagy a replikált gépeket, a következő mappákat ki kell zárni a vizsgálatból:


- C:\Program Files\Microsoft Azure Recovery Services ügynök
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86) \Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (Windows rendszerű gépeken, amelyen telepítve van a mobilitási szolgáltatás)

## <a name="replication-process"></a>Replikációs folyamat

1. Amikor engedélyezi a replikációt egy gépen, az Azure-ba történő kezdeti replikáció megkezdődik.
2. A kezdeti replikálás során a mobilitási szolgáltatás beolvassa az adatokat a gépi lemezekről, és elküldi azt a folyamat-kiszolgálónak.
3. Ezek az adatmennyiségek a lemez egy példányának az Azure-előfizetésben való összevetésére szolgálnak. 
4. A kezdeti replikálás befejeződése után a változásokat az Azure-ba replikálva megkezdődik. A replikáció blokk szintű, és közel folyamatos.
4. A mobilitási szolgáltatás a lemez memóriába írását elfogja az operációs rendszer tárolási alrendszerének integrálásával. Ezzel a módszerrel elkerülhetők a replikálást végző gép lemezes I/O-műveletei a növekményes replikációhoz. 
5. A gép nyomon követett módosításait a rendszer a HTTPS 9443 bejövő porton továbbítja a folyamat-kiszolgálónak. Ez a port módosítható. A Process Server tömöríti és titkosítja azt, és elküldi az Azure-nak. 

## <a name="ports"></a>Portok

**Eszköz** | **Kapcsolat**
--- | --- 
**Gépek replikálása** | A virtuális gépeken futó mobilitási szolgáltatás a HTTPS 443 bejövő porton keresztül kommunikál a helyszíni replikációs berendezéssel a replikálás kezeléséhez.<br/><br/> A számítógépek a HTTPS 9443 bejövő porton küldik el a replikációs adatküldést a folyamat kiszolgálójára. Ez a port módosítható.
**Replikációs berendezés** | A replikációs berendezés az Azure-ba irányuló replikációt a HTTPS 443 kimenő porton keresztül hangolja össze.
**Folyamatkiszolgáló** | A Process Server replikációs adatokat fogad, optimalizálja és titkosítja, majd az Azure Storage-ba küldi az 443-as porton keresztül.


## <a name="performance-and-scaling"></a>Teljesítmény és skálázás

Alapértelmezés szerint egyetlen replikációs berendezést telepít, amely a konfigurációs kiszolgálót és a Process Servert is futtatja. Ha csak néhány gépet replikál, akkor ez a központi telepítés elegendő. Ha azonban több száz gép replikálását és áttelepítését végzi, előfordulhat, hogy egyetlen folyamat-kiszolgáló nem tudja kezelni az összes replikációs forgalmat. Ebben az esetben telepíthet további, kibővíthető folyamat-kiszolgálókat is.

### <a name="plan-vmware-deployment"></a>A VMware üzembe helyezésének megtervezése

Ha VMware virtuális gépeket replikál, használhatja a [VMware-hez készült Site Recovery Deployment Planner](../site-recovery/site-recovery-deployment-planner.md)a teljesítményre vonatkozó követelmények meghatározásához, beleértve a napi adatváltozási arányt és a szükséges folyamat-kiszolgálókat.

### <a name="replication-appliance-capacity"></a>Replikációs berendezés kapacitása

Az ebben a táblázatban szereplő értékek alapján megállapíthatja, hogy szüksége van-e egy további Process Serverre a telepítésben.

- Ha a napi változási arány (a forgalom aránya) meghaladja a 2 TB-ot, helyezzen üzembe egy további kiszolgálót.
- Ha több mint 200 gépet replikál, helyezzen üzembe egy további replikációs készüléket.

**CPU** | **Memória** | **Szabad terület – az adatgyorsítótárazás** | **Adatforgalom aránya** | **Replikációs korlátok**
--- | --- | --- | --- | ---
8 vCPU (2 szoftvercsatorna * 4 mag \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB vagy kevesebb | < 100 gép 
12 vCPU (2 szoftvercsatorna * 6 mag \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB – 1 TB | 100-150 gép.
16 vCPU (2 szoftvercsatorna * 8 mag \@ 2,5 GHz) | 32 GB |  1 TB | 1 TB – 2 TB | 151-200 gép.

### <a name="sizing-scale-out-process-servers"></a>Méretezési Felskálázási folyamat kiszolgálói

Ha egy kibővíthető folyamat-kiszolgálót kell üzembe helyeznie, használja ezt a táblázatot, hogy kiderítse a kiszolgálók méretezését.

**Folyamatkiszolgáló** | **Szabad terület az adatgyorsítótárazáshoz** | **Adatforgalom aránya** | **Replikációs korlátok**
--- | --- | --- | --- 
4 vCPU (2 szoftvercsatorna * 2 mag \@ 2,5 GHz), 8 GB memória | 300 GB | 250 GB vagy kevesebb | Akár 85 gép 
8 vCPU (2 szoftvercsatorna * 4 mag \@ 2,5 GHz), 12 GB memória | 600 GB | 251 GB – 1 TB | 86-150 gép.
12 vCPU (2 szoftvercsatorna * 6 mag \@ 2,5 GHz), 24 GB memória | 1 TB | 1-2 TB | 151-225 gép.

## <a name="throttle-upload-bandwidth"></a>Sávszélesség-feltöltés szabályozása.

Az Azure-ba replikált VMware-forgalom egy adott folyamat-kiszolgálón halad át. A feltöltési sebességet korlátozhatja a folyamat-kiszolgálóként futó gépek sávszélességének szabályozásával. A következő beállításkulcs használatával befolyásolhatja a sávszélességet:

- A HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM beállításazonosító meghatározza, hogy hány szálat kell használni a lemez adatátviteléhez (a kezdeti vagy a különbözeti replikációhoz). A magasabb érték növeli a replikáláshoz használt hálózati sávszélességet. Az alapértelmezett érték négy. A maximális érték 32. Az optimális érték kiválasztásához kövesse figyelemmel a forgalmat.
- Emellett a következő módon szabályozhatja a sávszélességet a Process Server-gépen:

    1. A Process Server gépen nyissa meg az Azure Backup MMC beépülő modult. Parancsikon található az asztalon vagy a következő mappában: C:\Program Files\Microsoft Azure Recovery Services Agent\bin. 
    2. A beépülő modulban válassza a **Tulajdonságok módosítása** lehetőséget.
    3. A **szabályozás** alatt jelölje be a **biztonsági mentési műveletek internetes sávszélesség-szabályozásának engedélyezése** jelölőnégyzetet. A munkamennyiség és a munkaidőn kívüli munkaidő korlátozásának beállítása. Az érvényes tartományok 512 kbps és 1 023 Mbps között vannak.


## <a name="next-steps"></a>Következő lépések

Próbálja ki a [VMware](tutorial-migrate-vmware-agent.md) vagy a [fizikai kiszolgálók](tutorial-migrate-physical-virtual-machines.md) [ügynök-alapú áttelepítését](tutorial-migrate-vmware-agent.md) .
