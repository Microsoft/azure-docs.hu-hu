---
title: Azure arc-kompatibilis kiszolgálók virtuálisgép-bővítményekkel kapcsolatos problémáinak elhárítása
description: Ez a cikk azt ismerteti, hogyan lehet elhárítani az Azure-beli virtuálisgép-bővítményekkel kapcsolatos problémákat, és elhárítani azokat az Azure arc-kompatibilis kiszolgálókon.
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: 799e5c8ec00a894c6a54c64494edd8f259faf2dc
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584664"
---
# <a name="troubleshoot-arc-enabled-servers-vm-extension-issues"></a>Az ív használatára képes kiszolgálók virtuálisgép-bővítményekkel kapcsolatos hibák elhárítása

Ez a cikk az Azure virtuálisgép-bővítményeknek az arc-kompatibilis kiszolgálókon való üzembe helyezése vagy eltávolítása során esetlegesen előforduló problémák elhárításával és megoldásával kapcsolatos információkat tartalmaz. Általános információk: Azure virtuálisgép- [Bővítmények kezelése és használata](./manage-vm-extensions.md).

## <a name="general-troubleshooting"></a>Általános hibaelhárítás

A bővítmények állapotával kapcsolatos adatok a Azure Portalból kérhetők le.

Az alábbi hibaelhárítási lépések minden virtuálisgép-bővítményre érvényesek.

1. A vendég ügynök naplójának vizsgálatához tekintse meg a tevékenységet, ha a bővítmény a Windows rendszerhez lett kiépítve `%SystemDrive%\ProgramData\GuestConfig\ext_mgr_logs` , és a Linux alatt `/var/lib/GuestConfig/ext_mgr_logs` .

2. A Windows rendszerre vonatkozó további részletekért olvassa el a bővítmények naplóit az adott bővítménynél `%SystemDrive%\ProgramData\GuestConfig\extension_logs\<Extension>` . A bővítmény kimenetét a rendszer a Linux rendszeren telepített minden bővítmény fájljában naplózza `/var/lib/GuestConfig/extension_logs` .

3. A bővítményekre vonatkozó dokumentációs hibaelhárítási szakaszt a hibakódok, az ismert problémák stb. című szakaszban tekintheti meg. Az egyes bővítményekkel kapcsolatos további hibaelhárítási információk a bővítmény áttekintésében, a **Hibaelhárítás és támogatás** szakaszban találhatók. Ez magában foglalja a naplóba írt hibakódok leírását. A bővítmények [táblázata a bővítmények táblában](manage-vm-extensions.md#extensions)van csatolva.

4. Tekintse meg a rendszernaplókat. Keresse meg azokat a műveleteket, amelyek esetleg zavart okozhattak a bővítménnyel, például egy olyan alkalmazás hosszú futású telepítését, amely kizárólagos csomagkezelő-hozzáférést igényelt.

## <a name="troubleshooting-specific-extension-scenarios"></a>Konkrét kiterjesztési forgatókönyvek hibaelhárítása

### <a name="vm-insights"></a>VM-ismeretek

- Ha egy Azure arc-kompatibilis kiszolgáló virtuálisgép-megállapításait engedélyezi, akkor telepíti a függőséget és Log Analytics ügynököt. Lassú gépen vagy egy lassú hálózati kapcsolaton keresztül a telepítési folyamat időtúllépéseket láthat. A Microsoft lépéseket tesz, hogy ezt a feltételt a csatlakoztatott számítógép ügynökében is javítsa. Az átmeneti időszak alatt a telepítés újrapróbálkozása sikeres lehet.

### <a name="log-analytics-agent-for-linux"></a>Linux-Log Analytics ügynök

- A Log Analytics ügynök verziójának 1.13.9 (a megfelelő bővítmény verziója 1.13.15) nem megfelelően jelöli meg a feltöltött adatmennyiséget az Azure arc-kompatibilis kiszolgáló erőforrás-azonosítójával. Bár a rendszer a naplókat a szolgáltatásba küldi, amikor a **naplók** **vagy az** elemzések kiválasztása után megpróbálja megtekinteni a kijelölt kiszolgáló adatait, az adatok nem lesznek visszaadva. Az adatok megtekinthetők úgy, hogy a munkaterületre hatókörrel rendelkező Azure Monitor naplókból vagy Azure Monitor for VMsból futtatnak lekérdezéseket.

- A Linux Log Analytics ügynöke jelenleg nem támogatja a disztribúciókat. Az ügynöknek további függőségeket kell telepítenie, beleértve a Python 2-t is. Tekintse át a támogatási mátrixot [](../../azure-monitor/agents/agents-overview.md#supported-operating-systems)és az előfeltételeket.

- Az állapotjelző üzenet 52-es hibakódja hiányzó függőséget jelez. A kimenet és a naplók további információkkal szolgálnak arról, hogy mely függőségek hiányoznak.

- Ha egy telepítés meghiúsul, tekintse át a bővítmény áttekintésében a **hibakeresés és támogatás** című szakaszt. A legtöbb esetben az állapotjelző üzenet tartalmaz egy hibakódot. A Linux rendszerhez készült Log Analytics-ügynök állapotüzenetek [itt](../../virtual-machines/extensions/oms-linux.md#troubleshoot-and-support)találhatók meg, valamint a virtuálisgép-bővítmény általános hibaelhárítási információi.

## <a name="next-steps"></a>Következő lépések

Ha itt nem találja a problémát, vagy nem tudja elhárítani a problémát, próbálja ki a következő csatornák egyikét a további támogatáshoz:

- Választ kaphat az Azure szakértőitől a [Microsoft Q&A](/answers/topics/azure-arc.html)-n keresztül.

- A szolgáltatással való együttműködéshez [@AzureSupport](https://twitter.com/azuresupport) a hivatalos Microsoft Azure fiók a felhasználói élmény javítása érdekében. Az Azure-támogatás a válaszokat, támogatást és szakértőket az Azure-Közösséggel köti össze.

- Azure-támogatási incidens küldése. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/), és válassza a **támogatás kérése** lehetőséget.
