---
title: Rendszerállapot biztonsági mentésének hibaelhárítása
description: Ebből a cikkből megtudhatja, hogyan háríthatja el a helyszíni Windows-kiszolgálók rendszerállapot-biztonsági mentésével kapcsolatos hibákat.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/22/2019
ms.openlocfilehash: 07e101fe87fb3f5db0bb716f0bc9ea6f8773aa3e
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518558"
---
# <a name="troubleshoot-system-state-backup"></a>Rendszerállapot biztonsági mentésének hibaelhárítása

Ez a cikk azokat a problémákat ismerteti, amelyek a rendszerállapot biztonsági mentése során előfordulhatnak.

## <a name="basic-troubleshooting"></a>Alapszintű hibaelhárítás

Javasoljuk, hogy a rendszerállapot biztonsági mentésének hibaelhárítása előtt hajtsa végre a következő érvényesítési lépéseket:

- [Győződjön Microsoft Azure, hogy a Recovery Services- (MARS-) ügynök naprakész](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Ellenőrizze, hogy van-e hálózati kapcsolat a MARS-ügynök és az Azure között](./backup-azure-mars-troubleshoot.md#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Győződjön meg arról, hogy a Microsoft Azure Recovery Services fut (a Szolgáltatás konzolon). Szükség esetén indítsa újra a műveletet, majd próbálja újra a műveletet
- [Győződjön meg arról, hogy 5-10% szabad hellyel rendelkezik az ideiglenes mappa helyén](./backup-azure-file-folder-backup-faq.yml#what-s-the-minimum-size-requirement-for-the-cache-folder-)
- [Ellenőrizze, hogy egy másik folyamat vagy víruskereső szoftver nem zavarja-e az Azure Backup működését](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- [Az ütemezett biztonsági mentés meghiúsul, de a manuális biztonsági mentés sikeres](./backup-azure-mars-troubleshoot.md#backups-dont-run-according-to-schedule)
- Ellenőrizze, hogy az operációs rendszer rendelkezik-e a legújabb frissítésekkel
- [Győződjön meg arról, hogy a nem támogatott meghajtók és fájlok nem támogatott attribútumokkal ki vannak zárva a biztonsági mentésből](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Győződjön meg arról, hogy a védett rendszer **rendszerórája** a megfelelő időzónára van állítva <br>
- [Győződjön meg arról, hogy a kiszolgáló rendelkezik a .NET-keretrendszer 4.5.2-es vagy annál frissebb verziójával](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Ha a kiszolgálót **egy** tárolóban próbálja újra regisztrálni, akkor: <br>
  - Győződjön meg arról, hogy az ügynök el lett távolítva a kiszolgálón, és törölve lett a portálról <br>
  - Használja ugyanazt a jelszót, amelyet kezdetben használt a kiszolgáló regisztrálásához <br>
- Ha ez egy offline biztonsági mentés, győződjön meg arról, Azure PowerShell 3.7.0-s verzió van telepítve a forrás- és a másolási számítógépre is, mielőtt megkezdi az offline biztonsági mentési műveletet
- [Megfontolni, hogy mikor fut a Backup ügynök egy Azure-beli virtuális gépen](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine)

### <a name="limitation"></a>Korlátozás

- A Microsoft nem ajánlott a rendszerállapot-helyreállítást használó különböző hardverre való helyreállítást
- A rendszerállapot biztonsági mentése jelenleg a "helyszíni" Windows-kiszolgálókat támogatja. Ez a funkció nem érhető el az Azure-beli virtuális gépekhez.

## <a name="prerequisites"></a>Előfeltételek

A rendszerállapot biztonsági mentésének hibaelhárítása előtt Azure Backup az alábbi előfeltétel-ellenőrzést.  

### <a name="verify-windows-server-backup-is-installed"></a>Ellenőrizze, Windows Server biztonsági másolat telepítve van-e

Győződjön Windows Server biztonsági másolat, hogy az telepítve és engedélyezve van a kiszolgálón. A telepítési állapot ellenőrzéséhez futtassa a következő PowerShell-parancsot:

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

Ha a kimenetben a **Telepítési** állapot elérhetőként jelenik **meg,** az azt jelenti, hogy a Windows Server biztonsági mentési szolgáltatás elérhető a telepítéshez, de nincs telepítve a kiszolgálón. Ha azonban Windows Server biztonsági másolat nincs telepítve, akkor az alábbi módszerek egyikével telepítse.

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>1. módszer: Windows Server biztonsági másolat Telepítése a PowerShell használatával

Az Windows Server biztonsági másolat PowerShell használatával való telepítéséhez futtassa a következő parancsot:

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>2. módszer: Windows Server biztonsági másolat telepítése a Kiszolgálókezelővel

Ha a Windows Server biztonsági másolat a Kiszolgálókezelővel, hajtsa végre a következő lépéseket:

1. A **Kiszolgálókezelőben válassza** a **Szerepkörök és szolgáltatások hozzáadása lehetőséget.** Megjelenik **a Szerepkörök és szolgáltatások hozzáadása** varázsló.

    ![Irányítópult](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Válassza **a Telepítés típusa,** majd a Tovább **lehetőséget.**

    ![Telepítés típusa](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Válasszon ki egy kiszolgálót a kiszolgálókészletből, majd válassza a **Tovább lehetőséget.** A Kiszolgálói szerepkör mezőben hagyja meg az alapértelmezett beállítást, és válassza a **Tovább lehetőséget.**
4. Válassza **Windows Server biztonsági másolat** a **Szolgáltatások lapon,** majd a Tovább **lehetőséget.**

    ![Funkciók kiválasztása ablak](./media/backup-azure-system-state-troubleshoot/features.png)

5. A Megerősítés **lapon** válassza a Telepítés **lehetőséget** a telepítési folyamat elkezdése érdekében.
6. Az Eredmények **lapon** megjelenik a Windows Server biztonsági másolat a Windows Serveren sikeresen telepített szolgáltatás.

    ![A telepítés eredményei](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>Rendszerkötet információinak engedélye

Győződjön meg arról, hogy a  Helyi RENDSZER teljes körű hozzáféréssel rendelkezik a Rendszerkötet adatai mappához, amely abban a kötetben található, ahol a Windows telepítve van. Ez általában a **C:\System Volume Information**. A Windows Server biztonsági mentése meghiúsulhat, ha a fenti engedélyek nincsenek megfelelően beállítva.

### <a name="dependent-services"></a>Függő szolgáltatások

Győződjön meg arról, hogy az alábbi szolgáltatások futnak:

**Szolgáltatásnév** | **Indítás típusa**
--- | ---
Távoli eljáráshívás (RPC) | Automatikus
COM+ eseményrendszer (EventSystem) | Automatikus
Rendszeresemény-értesítési szolgáltatás (SENS) | Automatikus
Kötet árnyékmásolata (VSS) | Kézi
Microsoft Szoftver árnyékmásolata szolgáltató (SWPRV) | Kézi

### <a name="validate-windows-server-backup-status"></a>A Windows Server biztonsági másolat ellenőrzése

A Windows Server biztonsági másolat ellenőrzéshez hajtsa végre a következő lépéseket:

- Győződjön meg arról, hogy a WSB PowerShell fut

  - Futtassa a parancsot egy emelt szintű PowerShellből, és győződjön meg arról, hogy az `Get-WBJob` nem a következő hibaüzenetet adja vissza:

    > [!WARNING]
    > Get-WBJob: A Get-WBJob kifejezést a rendszer nem ismeri fel parancsmagok, függvények, parancsfájlok vagy működőképes programok neveként. Ellenőrizze a név helyesírását, vagy ha elérési utat tartalmazott, ellenőrizze, hogy az elérési út helyes-e, majd próbálkozzon újra.

    - Ha ezzel a hibával meghiúsul, telepítse újra a Windows Server biztonsági másolat szolgáltatást a kiszolgálógépen az előfeltételek 1. lépésében említettek szerint.

  - A WSB biztonsági mentés megfelelő működését a következő parancs rendszergazda jogú parancssorból való futtatásával biztosíthatja:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Cserélje le az X-et annak a kötetnek a meghajtóbetűjelére, ahol a rendszerállapot biztonsági lemezképét tárolni szeretné.

    - Rendszeresen ellenőrizze a feladat állapotát az emelt szintű `Get-WBJob` PowerShellből futtatott parancs futtatásával
    - A biztonsági mentési feladat befejezése után a parancs futtatásával ellenőrizze a feladat végleges `Get-WBJob -Previous 1` állapotát

Ha a feladat meghiúsul, az WSB-problémát jelez, amely a MARS-ügynök Rendszerállapot biztonsági mentései hibát eredményezne.

## <a name="common-errors"></a>Gyakori hibák

### <a name="vss-writer-timeout-error"></a>VSS-író időtúllépési hibája

| Hibajelenség | Ok | Feloldás
| -- | -- | --
| – A MARS-ügynök a következő hibaüzenettel meghiúsul: "A WSB-feladat VSS-hibákkal meghiúsult. A hiba elhárításához ellenőrizze a VSS-eseménynaplókat"<br/><br/> – A VSS-alkalmazás eseménynaplóiban a következő hibanapló jelenik meg: "A VSS-író elutasított egy 0x800423f2 hibával jelzett eseményt, az író időtúllépése lejárt a Lefagyási és Thaw-események között."| A VSS-író nem tud időben befejeződni a gép processzor- és memória-erőforrásainak hiánya miatt <br/><br/> Egy másik biztonsági mentési szoftver már használja a VSS-írót, ennek következtében a pillanatkép-készítés nem fejeződött be a biztonsági mentéshez | Várja meg, amíg a rendszer felszabadítja a CPU-t/memóriát, vagy megszakítja a túl sok memóriát/cpu-t igénybe véve, és próbálkozzon újra a művelettel. <br/><br/>  Várja meg, amíg a folyamatban lévő biztonsági mentés befejeződik, és próbálkozzon a művelettel egy későbbi időpontban, amikor nem futnak biztonsági másolatok a gépen.

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Nincs elegendő lemezterület az árnyékmásolatok növekedésére

| Hibajelenség | Feloldás
| -- | --
| – A MARS-ügynök a következő hibaüzenettel meghiúsult: A biztonsági mentés sikertelen volt, mert az árnyékmásolat-kötet nem növekedett a rendszerfájlokat tartalmazó kötetek elegendő lemezterülete miatt <br/><br/> - A következő hiba-/figyelmeztető napló található a volsnap rendszer eseménynaplóiban: "Nincs elegendő lemezterület a C köteten: nincs elegendő lemezterület a C kötet árnyékmásolatai számára: e hiba következtében a C: kötet összes árnyékmásolata törölhető" | – Helyet szabadíthat fel a kiemelt köteten az eseménynaplóban, hogy elegendő hely legyen az árnyékmásolatok növekedéséhez, amíg a biztonsági mentés folyamatban van <br/><br/> – Az árnyékmásolati terület konfigurálásakor korlátozható az árnyékmásolathoz használt terület mennyisége. További információért tekintse meg ezt a [cikket](/windows-server/administration/windows-commands/vssadmin-resize-shadowstorage)

### <a name="efi-partition-locked"></a>EFI-partíció zárolva

| Hibajelenség | Feloldás
| -- | --
| A MARS-ügynök a következő hibaüzenettel meghiúsul: "A rendszerállapot biztonsági mentése meghiúsult, mert az EFI rendszerpartíció zárolva van. Ennek az lehet az oka, hogy a rendszerpartícióhoz külső biztonsági rendszer fér hozzá, vagy biztonságimentő szoftverről van szó. | – Ha a problémát egy harmadik féltől származó biztonsági szoftver okozza, akkor kapcsolatba kell lépnie a vírusirtó szállítóval, hogy engedélyeződhet a MARS-ügynök <br/><br/> – Ha egy külső biztonsági mentési szoftver fut, várjon, amíg befejeződik, majd próbálja meg újra a biztonsági mentést

## <a name="next-steps"></a>Következő lépések

- További információ a központi telepítésben található Windows rendszerállapotról Resource Manager a [Windows Server rendszerállapotának biztonsági helyezése.](backup-azure-system-state.md)
