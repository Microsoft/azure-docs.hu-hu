---
title: Windows újraindítási hurok egy Azure-beli virtuális gépen | Microsoft Docs
description: Útmutató a Windows újraindítási ciklusának hibakereséséhez | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/15/2018
ms.author: genli
ms.openlocfilehash: 4c336fe9a65d7bcc44790a4bfb02bed44f028733
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86500922"
---
# <a name="windows-reboot-loop-on-an-azure-vm"></a>Windows újraindítási hurok Azure-beli virtuális gépen
Ez a cikk a Windows rendszerű virtuális gépeken (VM) tapasztalható újraindítási hurkot ismerteti Microsoft Azureban.

## <a name="symptom"></a>Hibajelenség

Ha [rendszerindítási diagnosztikát](./boot-diagnostics.md) használ egy virtuális gép képernyőképének beszerzéséhez, a rendszer elindítja a virtuális gépet, de a rendszerindítási folyamat megszakad, és a folyamat elindul.

![1. kezdőképernyő](./media/troubleshoot-reboot-loop/start-screen-1.png)

## <a name="cause"></a>Ok

Az újraindítási hurok a következő okok miatt fordul elő:

### <a name="cause-1"></a>1. ok

Létezik egy harmadik féltől származó szolgáltatás, amely kritikusként van megjelölve, és nem indítható el. Ez az operációs rendszer újraindítását okozza.

### <a name="cause-2"></a>2. ok

Néhány módosítás történt az operációs rendszeren. Ezek általában a frissítés telepítésével, az alkalmazások telepítésével vagy egy új házirenddel kapcsolatosak. További részletekért tekintse meg a következő naplókat:

- Eseménynaplók
- CBS. logWindows
- Update. log

### <a name="cause-3"></a>3. ok

A fájlrendszer sérülése ezt okozhatta. Azonban nehéz diagnosztizálni és azonosítani az operációs rendszer sérülését okozó változást.

## <a name="solution"></a>Megoldás

A probléma megoldásához [végezze el az operációsrendszer-lemez biztonsági mentését](../windows/snapshot-copy-managed-disk.md), és [csatlakoztassa az operációsrendszer-lemezt egy mentési virtuális géphez](./troubleshoot-recovery-disks-portal-windows.md), majd kövesse a megoldás beállításait, vagy próbálja meg egyenként a megoldásokat.

### <a name="solution-for-cause-1"></a>Megoldás az 1. ok esetén

1. Miután az operációsrendszer-lemez csatlakoztatva van egy működő virtuális géphez, ellenőrizze, hogy a lemez **online** állapotban van-e megjelölve a Lemezkezelés konzolon, és jegyezze fel a **\Windows** mappát tároló partíció meghajtóbetűjelét.

2. Ha a lemez offline értékre van beállítva, akkor állítsa **online** **állapotba**.

3. Hozzon létre egy másolatot a **\Windows\System32\config** mappából abban az esetben, ha a módosítások visszaállítására van szükség.

4. A mentési virtuális gépen nyissa meg a Windows rendszerleíróadatbázis-szerkesztőt (Regedit).

5. Válassza ki a **HKEY_LOCAL_MACHINE** kulcsot, majd válassza ki a **fájl**  >  **betöltése struktúra** elemet a menüből.

6. Keresse meg a rendszerfájlt a **\Windows\System32\config** mappában.

7. Válassza a **Megnyitás**elemet, írja be a **BROKENSYSTEM** nevet, bontsa ki a **HKEY_LOCAL_MACHINE** kulcsot, majd megjelenik egy további, **BROKENSYSTEM**nevű kulcs.

8. Győződjön meg arról, hogy a számítógép melyik ControlSet indul el. A kulcs számát a következő beállításkulcs fogja látni.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\Select\Current`

9. Győződjön meg arról, hogy a virtuálisgép-ügynök szolgáltatás kritikus fontosságú a következő beállításkulcs használatával.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\RDAgent\ErrorControl`

10. Ha a beállításkulcs értéke nem **2**, akkor folytassa a következő enyhítéssel.

11. Ha a beállításkulcs értéke **2**, akkor módosítsa az értéket **2** – **1**értékre.

12. Ha a következő kulcsok bármelyike létezik, és a **2** . vagy **3**. értékkel rendelkezik, majd módosítsa ezeket az értékeket **1** ennek megfelelően:

    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupCoordinatorSvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupInquirySvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupPluginSvc\ErrorControl`

13. Válassza ki a **BROKENSYSTEM** kulcsot, majd válassza ki a **fájl**  >  **kitöltése struktúrát** a menüből.

14. Válassza le az operációsrendszer-lemezt a hibaelhárítási virtuális gépről.

15. Távolítsa el a lemezt a hibaelhárítási virtuális gépről, és várjon 2 percet, amíg az Azure kiadja ezt a lemezt.

16. [Hozzon létre egy új virtuális gépet az operációsrendszer-lemezről](../windows/create-vm-specialized.md).

17. Ha a probléma kijavítva van, előfordulhat, hogy újra kell telepítenie a [RDAgent](/archive/blogs/mast/install-the-vm-agent-on-an-existing-azure-vm) (WaAppAgent.exe).

### <a name="solution-for-cause-2"></a>Megoldás a 2. ok esetén

Állítsa vissza a virtuális gépet az utolsó ismert helyes konfigurációra, kövesse az [Azure Windows rendszerű virtuális gép indítása az utolsó ismert jó konfigurációval](https://support.microsoft.com/help/4016731/)című témakör lépéseit.

### <a name="solution-for-cause-3"></a>3. ok megoldás
>[!NOTE]
>A következő eljárást csak utolsó erőforrásként kell használni. Míg a Regback-re való visszaállítás visszaállíthatja a számítógép elérését, az operációs rendszer nem tekinthető stabilnak, mivel a beállításjegyzékben a struktúra és az aktuális nap időbélyege között elveszett adatok. Létre kell hoznia egy új virtuális gépet, és el kell végeznie az adatmigrálás terveit.

1. Miután a lemez csatlakoztatva van egy hibaelhárítási virtuális géphez, ellenőrizze, hogy a lemez **online** állapotban van-e megjelölve a Lemezkezelés konzolon.

2. Hozzon létre egy másolatot a **\Windows\System32\config** mappából abban az esetben, ha a módosítások visszaállítására van szükség.

3. Másolja a fájlokat a **\Windows\System32\config\regback** mappába, és cserélje le a fájlokat a **\Windows\System32\config** mappába.

4. Távolítsa el a lemezt a hibaelhárítási virtuális gépről, és várjon 2 percet, amíg az Azure kiadja ezt a lemezt.

5. [Hozzon létre egy új virtuális gépet az operációsrendszer-lemezről](../windows/create-vm-specialized.md).
