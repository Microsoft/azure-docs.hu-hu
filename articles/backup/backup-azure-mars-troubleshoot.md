---
title: A Azure Backup ügynök hibáinak megoldása
description: Ebből a cikkből megtudhatja, hogyan lehet elhárítani a Azure Backup ügynök telepítését és regisztrálását.
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: 3203d5604f1bd5db9cf579af01b2ae6f34032d89
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467612"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>A Microsoft Azure Recovery Services-(MARS-) ügynök hibáinak megoldása

Ez a cikk a konfiguráció, a regisztráció, a biztonsági mentés és a visszaállítás során esetlegesen megjelenő hibák megoldását ismerteti.

## <a name="basic-troubleshooting"></a>Alapszintű hibaelhárítás

Javasoljuk, hogy a Microsoft Azure Recovery Services (MARS) ügynökével kapcsolatos hibaelhárítás megkezdése előtt győződjön meg a következőkről:

- [Győződjön meg arról, hogy a Mars-ügynök naprakész](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409).
- [Győződjön meg arról, hogy van hálózati kapcsolat a Mars-ügynök és az Azure között](#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup).
- Győződjön meg arról, hogy a MARS fut (a szolgáltatás konzolján). Ha szükséges, indítsa újra, majd próbálja megismételni a műveletet.
- Ügyeljen arra, hogy a kihelyezett [mappa helyén 5% – 10% szabad lemezterület álljon rendelkezésre](./backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder).
- [Ellenőrizze, hogy egy másik folyamat vagy víruskereső szoftver nem zavarja-e az Azure Backup működését](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup).
- Ha a biztonsági mentési feladat figyelmeztetésekkel fejeződött be, olvassa el a [biztonsági mentési feladatok befejezése figyelmeztetéssel](#backup-jobs-completed-with-warning) című témakört.
- Ha az ütemezett biztonsági mentés sikertelen, de a manuális biztonsági mentés működik, [a biztonsági mentések nem az ütemezés szerint futnak](#backups-dont-run-according-to-schedule).
- Győződjön meg arról, hogy az operációs rendszer rendelkezik a legújabb frissítésekkel.
- [Győződjön meg arról, hogy a nem támogatott, nem támogatott attribútumokkal rendelkező meghajtók és fájlok ki vannak zárva a biztonsági mentésből](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Győződjön meg arról, hogy a védett rendszer órája a megfelelő időzónára van konfigurálva.
- [Győződjön meg arról, hogy a .NET-keretrendszer 4.5.2-es vagy újabb verziója telepítve van a kiszolgálón](https://www.microsoft.com/download/details.aspx?id=30653).
- Ha a kiszolgálót egy tárolóba próbálja újra regisztrálni:
  - Győződjön meg arról, hogy az ügynök el lett távolítva a kiszolgálón, és hogy a portálról törölve lett.
  - Ugyanazt a jelszót használja, amelyet eredetileg a kiszolgáló regisztrálásához használt.
- Offline biztonsági mentések esetén győződjön meg arról, hogy a biztonsági mentés megkezdése előtt a forráson és a másolási számítógépen Azure PowerShell 3.7.0 van telepítve.
- Ha a biztonsági mentési ügynök Azure-beli virtuális gépen fut, tekintse meg [ezt a cikket](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine).

## <a name="invalid-vault-credentials-provided"></a>A tároló megadott hitelesítő adatai érvénytelenek

**Hibaüzenet**: Érvénytelen tároló hitelesítő adatok lettek megadva. A fájl sérült, vagy nem rendelkezik a helyreállítási szolgáltatással társított legújabb hitelesítő adatokkal. (AZONOSÍTÓ: 34513)

| Ok | Ajánlott műveletek |
| ---     | ---    |
| **A tár hitelesítő adatai nem érvényesek** <br/> <br/> Előfordulhat, hogy a tár hitelesítő adatai sérültek, esetleg lejártak, vagy a *. vaultCredentials* eltérő fájlkiterjesztés van. (Előfordulhat például, hogy a regisztráció időpontja előtt több mint 10 nappal letöltötte őket.)| [Töltse le az új hitelesítő adatokat](backup-azure-file-folder-backup-faq.md#where-can-i-download-the-vault-credentials-file) a Azure Portal Recovery Services tárból. Ezután hajtsa végre az alábbi lépéseket a megfelelő módon: <ul><li> Ha már telepítette és regisztrálta a MARSot, nyissa meg a Microsoft Azure Backup Agent MMC konzolt. Ezután válassza a **kiszolgáló regisztrálása** lehetőséget a **műveletek** ablaktáblán a regisztráció az új hitelesítő adatokkal való elvégzéséhez. <br/> <li> Ha az új telepítés meghiúsul, próbálja meg újratelepíteni az új hitelesítő adatokkal.</ul> **Megjegyzés**: Ha több tároló hitelesítő adatait letöltötte, csak a legújabb fájl érvényes a következő 10 napra. Azt javasoljuk, hogy töltsön le egy új, a tároló hitelesítő adatait tartalmazó fájlt.
| **A proxykiszolgáló/tűzfal blokkolja a regisztrációt** <br/>vagy <br/>**Nincs internetkapcsolat** <br/><br/> Ha a számítógép vagy a proxykiszolgáló korlátozott internetkapcsolattal rendelkezik, és nem biztosít hozzáférést a szükséges URL-címekhez, a regisztráció sikertelen lesz.| Hajtsa végre a következő lépéseket:<br/> <ul><li> Az informatikai csapattal együttműködve gondoskodhat arról, hogy a rendszeren legyen internetkapcsolat.<li> Ha nincs proxykiszolgáló, győződjön meg arról, hogy a proxy beállítás nincs kiválasztva az ügynök regisztrálása során. [Keresse meg a proxybeállításokat](#verifying-proxy-settings-for-windows).<li> Ha tűzfal-vagy proxykiszolgáló van, a hálózati csapattal együttműködve biztosíthatja, hogy ezek az URL-címek és IP-címek hozzáférhessenek:<br/> <br> **URL-címek**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>`www.msftconnecttest.com`<br><br>**IP-címek**<br>  20.190.128.0/18 <br>  40.126.0.0/18<br> <br/></ul></ul>Az előző hibaelhárítási lépések elvégzése után próbálkozzon újra a regisztrálással.<br></br> Ha a kapcsolatai az Azure ExpressRoute keresztül érhetők el, győződjön meg arról, hogy a beállítások az [Azure ExpressRoute-támogatás](backup-support-matrix-mars-agent.md#azure-expressroute-support)című témakörben leírtak szerint vannak konfigurálva.
| **A víruskereső szoftver blokkolja a regisztrációt** | Ha a kiszolgálón telepítve van víruskereső szoftver, adja hozzá a szükséges kizárási szabályokat a következő fájlokhoz és mappákhoz tartozó víruskereső vizsgálathoz: <br/><ul> <li> CBengine.exe <li> CSC.exe<li> A Scratch mappa. Alapértelmezett helye a C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch. <li> A bin mappa a C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>További ajánlások

- Lépjen a C:/Windows/Temp értékre, és győződjön meg arról, hogy a. tmp kiterjesztéssel több mint 60 000 vagy 65 000 fájl található. Ha vannak ilyenek, törölje ezeket a fájlokat.
- Győződjön meg arról, hogy a gép dátumának és időpontjának egyeznie kell a helyi időzónával.
- Győződjön meg arról, hogy [ezek a helyek](install-mars-agent.md#verify-internet-access) hozzá vannak adva a megbízható helyekhez az Internet Explorerben.

### <a name="verifying-proxy-settings-for-windows"></a>Proxybeállítások ellenőrzése Windows rendszeren

1. Töltse le a PsExec a [Sysinternals](/sysinternals/downloads/psexec) lapról.
1. Futtatás `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` rendszergazda jogú parancssorból.

   Ezzel a paranccsal megnyílik az Internet Explorer.
1. Lépjen az **eszközök**  >  **internetes beállítások**  >  **kapcsolatok**  >  **LAN-beállítások menüpontra**.
1. Keresse meg a rendszerfiók proxybeállításait.
1. Ha nincs proxy konfigurálva, és a proxy adatai meg vannak adva, távolítsa el a részleteket.
1. Ha egy proxy konfigurálva van, és a proxy adatai helytelenek, győződjön meg arról, hogy a **proxy IP-címe** és a **port** adatai helyesek.
1. Zárja be az Internet Explorert.

## <a name="unable-to-download-vault-credential-file"></a>Nem tölthető le a tár hitelesítőadat-fájlja

| Hiba   | Ajánlott műveletek |
| ---     | ---    |
|Nem sikerült letölteni a tároló hitelesítőadat-fájlját. (AZONOSÍTÓ: 403) | <ul><li> Próbálja meg letölteni a tár hitelesítő adatait egy másik böngészőben, vagy hajtsa végre a következő lépéseket: <ul><li> Indítsa el az Internet Explorert. Válassza az F12 lehetőséget. </li><li> Nyissa meg a **hálózat** lapot, és törölje a gyorsítótárat és a cookie-kat. </li> <li> Frissítse az oldalt.<br></li></ul> <li> Ellenőrizze, hogy az előfizetés le van-e tiltva/lejárt-e.<br></li> <li> Ellenőrizze, hogy a tűzfalszabály blokkolja-e a letöltést. <br></li> <li> Gondoskodjon róla, hogy a tárolón ne legyen kimerítve a korlát (50 gép/tároló).<br></li>  <li> Győződjön meg arról, hogy a felhasználó rendelkezik a tár hitelesítő adatainak letöltéséhez szükséges Azure Backup engedélyekkel, és regisztrálja a kiszolgálót a tárolóban. Lásd: [Az Azure szerepköralapú hozzáférés-vezérlés használata Azure Backup helyreállítási pontok kezeléséhez](backup-rbac-rs-vault.md).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>A Microsoft Azure Recovery Service-ügynök nem tudott kapcsolódni a Microsoft Azure Backuphoz

| Hiba  | Lehetséges ok | Ajánlott műveletek |
| ---     | ---     | ---    |
| <br /><ul><li>A Microsoft Azure Recovery szolgáltatás ügynökének nem sikerült kapcsolódnia a Microsoft Azure Backuphoz. (AZONOSÍTÓ: 100050) Ellenőrizze a hálózati beállításokat, és győződjön meg arról, hogy tud-e csatlakozni az internethez.<li>(407) proxy hitelesítés szükséges. |A proxy blokkolja a csatlakozást. |  <ul><li>Az Internet Explorerben nyissa meg a **Tools**  >  **Internet Options**  >  **Security**  >  **Internet** menüpontot. Válassza az **Egyéni szint** lehetőséget, majd görgessen le a **Fájl letöltése** szakaszhoz. Válassza az **Engedélyezés** lehetőséget.<p>Előfordulhat, hogy az Internet Explorerben [URL-címeket és IP-címeket](install-mars-agent.md#verify-internet-access) is hozzá kell adnia a megbízható helyekhez.<li>Módosítsa a beállításokat proxykiszolgáló használatára. Ezután adja meg a proxykiszolgáló részleteit.<li> Ha a számítógépén korlátozott az Internet-hozzáférés, győződjön meg arról, hogy a számítógépen vagy a proxyn a tűzfalbeállítások engedélyezik ezeket az [URL-címeket és IP-címeket](install-mars-agent.md#verify-internet-access). <li>Ha a kiszolgálón telepítve van a víruskereső szoftver, zárja ki ezeket a fájlokat a víruskeresőből: <ul><li>CBEngine.exe (dpmra.exe helyett).<li>CSC.exe (a .NET-keretrendszerhez kapcsolódóan). A .NET-keretrendszer minden olyan verziójához CSC.exe, amely telepítve van a kiszolgálóra. CSC.exe fájlok kizárása a .NET-keretrendszer összes verziójához az érintett kiszolgálón. <li>A mappa vagy a gyorsítótár helye. <br>A Scratch mappa vagy a gyorsítótár elérési útjának alapértelmezett helye: C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.<li>A bin mappa a C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

## <a name="the-specified-vault-credential-file-cannot-be-used-as-it-is-not-downloaded-from-the-vault-associated-with-this-server"></a>A tár megadott hitelesítő adatait tartalmazó fájl nem használható, mert nem tölthető le a kiszolgálóhoz társított tárolóból.

| Hiba  | Lehetséges ok | Ajánlott műveletek |
| ---     | ---     | ---    |
| A tár megadott hitelesítő adatait tartalmazó fájl nem használható, mert nem tölthető le a kiszolgálóhoz társított tárolóból. (AZONOSÍTÓ: 100110) Adja meg a megfelelő tároló hitelesítő adatait. | A tár hitelesítőadat-fájlja egy másik tárolóból származik, mint az a kiszolgáló, amely már regisztrálva van. | Győződjön meg arról, hogy a célszámítógép és a forrásszámítógép ugyanahhoz a Recovery Services-tárolóhoz van regisztrálva. Ha a célkiszolgáló már regisztrálva van egy másik tárolóban, akkor a **kiszolgáló regisztrálása** lehetőséggel regisztrálhat a megfelelő tárba.  

## <a name="backup-jobs-completed-with-warning"></a>A biztonsági mentési feladatok figyelmeztetéssel fejeződött be

- Ha a MARS-ügynök a biztonsági mentés során megismétli a fájlokat és mappákat, előfordulhat, hogy a biztonsági mentés a figyelmeztetésekkel befejezettként lesz megjelölve. Ilyen körülmények között a feladatok figyelmeztetésekkel fejeződött be. Ez igen, de ez azt jelenti, hogy legalább egy fájlt nem sikerült biztonsági mentést készíteni. Így a rendszer kihagyta a fájlt, de az adatforráson lévő összes többi fájlról biztonsági másolatot készített.

  ![A biztonsági mentési feladatok figyelmeztetésekkel fejeződött be](./media/backup-azure-mars-troubleshoot/backup-completed-with-warning.png)

- Azok a feltételek, amelyek miatt a biztonsági másolatok kihagyhatják a fájlokat, a következők:
  - Nem támogatott fájlattribútumok (például: OneDrive mappában, tömörített adatfolyam, újraelemzési pontok). A teljes listát a [támogatási mátrixban](./backup-support-matrix-mars-agent.md#supported-file-types-for-backup)találja.
  - Fájlrendszerbeli probléma
  - Egy másik folyamat megzavarja (például: a fájlok kezelői kezelik a fájlokat, megakadályozhatja, hogy a MARS-ügynök hozzáférjen a fájlokhoz)
  - Egy alkalmazás által zárolt fájlok  

- A Backup szolgáltatás a naplófájlban a következő elnevezési szabály szerint jelöli meg ezeket a fájlokat: *LastBackupFailedFilesxxxx.txt* a *C:\Program Files\Microsoft Azure Recovery Service Agent\temp* mappában.
- A probléma megoldásához tekintse át a naplófájlt a probléma természetének megismeréséhez:

  | Hibakód             | Okokból                                             | Javaslatok                                              |
  | ---------------------- | --------------------------------------------------- | ------------------------------------------------------------ |
  | 0x80070570             | A fájl vagy a könyvtár sérült, és nem olvasható. | Futtassa a **chkdsk** parancsot a forrás köteten.                             |
  | 0x80070002, 0x80070003 | A számítógép nem találja a megadott fájlt.         | [Győződjön meg arról, hogy a semmiből nem telt el a mappa](./backup-azure-file-folder-backup-faq.md#manage-the-backup-cache-folder)  <br><br>  Ellenőrizze, hogy van-e olyan kötet, amelyben a tárolóhely konfigurálva van (nincs törölve)  <br><br>   [Győződjön meg arról, hogy a MARS-ügynök ki van zárva a gépen telepített víruskeresőből](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)  |
  | 0x80070005             | Hozzáférés megtagadva                                    | [Ellenőrizze, hogy a víruskereső vagy más, harmadik féltől származó szoftver blokkolja-e a hozzáférést](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)     |
  | 0x8007018b             | A felhőalapú fájlhoz való hozzáférés megtagadva.                | OneDrive-fájlok, git-fájlok vagy bármely más olyan fájl, amely offline állapotban lehet a gépen |

- A biztonsági másolatok sikeres biztonsági mentése érdekében a [kizárási szabályok hozzáadása a meglévő szabályzathoz](./backup-azure-manage-mars.md#add-exclusion-rules-to-existing-policy) lehetőséggel kizárhatja a nem támogatott, hiányzó vagy törölt fájlokat a biztonsági mentési szabályzatból.

- Ne törölje és ne hozza létre újra a védett mappákat a legfelső szintű mappában található azonos névvel. Ez azt eredményezheti, hogy a biztonsági mentés figyelmeztetésekkel fejeződik be, és a hibát *kritikus inkonzisztencia észlelte, ezért a módosítások nem replikálhatók.*  Ha törölnie és újból létre kell hoznia a mappákat, akkor a védett felső szintű mappa almappáiban kell megfontolnia.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Nem sikerült a biztonsági mentések biztonságossá tételéhez szükséges titkosítási kulcs beállítása

| Hiba | Lehetséges okok | Ajánlott műveletek |
| ---     | ---     | ---    |
| <br />Nem sikerült beállítani a biztonságos biztonsági mentések titkosítási kulcsát. Az aktiválás nem sikerült, de a titkosítási jelszó a következő fájlba lett mentve. |<li>A kiszolgáló már regisztrálva van egy másik tárolóban.<li>A konfiguráció során a jelszó megsérült.| Szüntesse meg a kiszolgáló regisztrációját a tárból, és regisztrálja újra az új jelszóval.

## <a name="the-activation-did-not-complete-successfully"></a>Az aktiválás nem fejeződött be sikeresen

| Hiba  | Lehetséges okok | Ajánlott műveletek |
|---------|---------|---------|
|<br />Az aktiválás sikertelenül zárult. Az aktuális művelet végrehajtása egy belső szolgáltatási hiba miatt meghiúsult [0x1FC07]. Próbálja meg újra a műveletet később. If the issue persists, please contact Microsoft support. (Az Azure Key Vault-művelet meghiúsult. Próbálja meg újból végrehajtani a műveletet. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához.)     | <li> A kaparós mappa olyan köteten található, amely nem rendelkezik elegendő hellyel. <li> A Scratch mappa helytelenül lett áthelyezve. <li> A OnlineBackup. KEK fájl hiányzik.         | <li>Frissítsen a MARS-ügynök [legújabb verziójára](https://aka.ms/azurebackup_agent) .<li>Helyezze át a kihelyezett mappát vagy a gyorsítótár helyét egy olyan kötetre, amelynek szabad területe a biztonsági mentési adatmennyiség 5%-a és 10%-a között van. A gyorsítótár helyének megfelelő áthelyezéséhez tekintse meg a [fájlok és mappák biztonsági mentésével kapcsolatos gyakori kérdések](./backup-azure-file-folder-backup-faq.md#manage-the-backup-cache-folder)című témakör lépéseit.<li> Győződjön meg arról, hogy a OnlineBackup. KEK fájl megtalálható. <br>A *Scratch mappa vagy a gyorsítótár elérési útjának alapértelmezett helye a C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>A titkosítási jelszó helytelenül van konfigurálva

| Hiba  | Lehetséges okok | Ajánlott műveletek |
|---------|---------|---------|
| <br />34506-es hiba. A számítógépen tárolt titkosítási jelszó nincs megfelelően konfigurálva.    | <li> A kaparós mappa olyan köteten található, amely nem rendelkezik elegendő hellyel. <li> A Scratch mappa helytelenül lett áthelyezve. <li> A OnlineBackup. KEK fájl hiányzik.        | <li>Frissítsen a MARS-ügynök [legújabb verziójára](https://aka.ms/azurebackup_agent) .<li>Helyezze át a kihelyezett mappát vagy a gyorsítótár helyét egy olyan kötetre, amelynek szabad területe a biztonsági mentési adatmennyiség 5%-a és 10%-a között van. A gyorsítótár helyének megfelelő áthelyezéséhez tekintse meg a [fájlok és mappák biztonsági mentésével kapcsolatos gyakori kérdések](./backup-azure-file-folder-backup-faq.md#manage-the-backup-cache-folder)című témakör lépéseit.<li> Győződjön meg arról, hogy a OnlineBackup. KEK fájl megtalálható. <br>A *Scratch mappa vagy a gyorsítótár elérési útjának alapértelmezett helye a C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |

## <a name="backups-dont-run-according-to-schedule"></a>A biztonsági mentések nem az ütemezés szerint futnak

Ha az ütemezett biztonsági mentések nem indulnak el automatikusan, de a manuális biztonsági mentések megfelelően működnek, próbálkozzon a következő műveletekkel:

- Győződjön meg arról, hogy a Windows Server biztonsági másolatának ütemterve nem ütközik az Azure Files és mappák biztonsági mentési ütemtervével.

- Győződjön meg arról, hogy az online biztonsági mentés állapota beállítás **engedélyezve** értékre van állítva. Az állapot ellenőrzéséhez hajtsa végre a következő lépéseket:

  1. A Feladatütemezőben bontsa ki a **Microsoft** elemet, majd válassza az **online biztonsági mentés** lehetőséget.
  1. Kattintson duplán a **Microsoft-OnlineBackup** elemre, és lépjen az **Eseményindítók** lapra.
  1. Ellenőrizze, hogy az állapot **engedélyezve** értékre van-e állítva. Ha nem, válassza a **Szerkesztés** lehetőséget, válassza az **engedélyezve** lehetőséget, majd kattintson **az OK gombra**.

- Győződjön meg arról, hogy a feladat futtatásához kiválasztott felhasználói fiók vagy **rendszer** vagy **helyi Rendszergazdák csoport** a kiszolgálón. A felhasználói fiók ellenőrzéséhez lépjen az **általános** lapra, és ellenőrizze a **biztonsági** beállításokat.

- Győződjön meg arról, hogy a PowerShell 3,0-es vagy újabb verziója telepítve van a kiszolgálón. A PowerShell verziójának ellenőrzéséhez futtassa ezt a parancsot, és ellenőrizze, hogy a `Major` verziószáma 3 vagy újabb:

  `$PSVersionTable.PSVersion`

- Győződjön meg arról, hogy ez az elérési út a `PSMODULEPATH` környezeti változó része:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Ha a PowerShell-végrehajtási házirend a értékre `LocalMachine` van állítva `restricted` , akkor a biztonsági mentési feladatot kiváltó PowerShell-parancsmag sikertelen lehet. Futtassa az alábbi parancsokat emelt szintű módban a végrehajtási házirend a vagy a értékre való beállításához `Unrestricted` `RemoteSigned` .

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
 ```

- Győződjön meg arról, hogy nincs hiányzó vagy sérült PowerShell-modul MSOnlineBackup-fájlja. Ha vannak hiányzó vagy sérült fájlok, hajtsa végre a következő lépéseket:

  1. Másolja a MSOnlineBackup mappát a C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules. webhelyről minden olyan gépről, amely megfelelően működik a MARS-ügynökkel.
  1. A problémás gépen illessze be a másolt fájlokat ugyanazon a mappában (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules).

     Ha már van MSOnlineBackup mappa a gépen, illessze be a fájlokat, vagy cserélje le a meglévő fájlokat.

> [!TIP]
> A módosítások következetes alkalmazása érdekében indítsa újra a kiszolgálót az előző lépések végrehajtása után.

## <a name="resource-not-provisioned-in-service-stamp"></a>Az erőforrás nincs kiépítve a szolgáltatási bélyegzőben

Hiba | Lehetséges okok | Ajánlott műveletek
--- | --- | ---
Az aktuális művelet végrehajtása sikertelen volt, mert egy belső szolgáltatáshiba "az erőforrás nincs kiépítve a szolgáltatási bélyegzőben". Némi idő elteltével próbálja megismételni a műveletet. (AZONOSÍTÓ: 230006) | A védett kiszolgáló át lett nevezve. | <li> Nevezze vissza a kiszolgálót az eredeti névre a tárolóban regisztráltként. <br> <li> Regisztrálja újra a kiszolgálót a tárolóba az új névvel.

## <a name="job-could-not-be-started-as-another-job-was-in-progress"></a>A feladatot nem lehetett elindítani, mert folyamatban van egy másik feladata

Ha a **Mars-konzol** feladat-előzményeiben figyelmeztető üzenet jelenik meg  >  , a "feladat nem indítható el, mert egy másik feladat folyamatban volt", ezt a Feladatütemező által aktivált feladat duplikált példánya okozhatja.

![A feladatot nem lehetett elindítani, mert folyamatban van egy másik feladata](./media/backup-azure-mars-troubleshoot/job-could-not-be-started.png)

A probléma megoldása:

1. Indítsa el a Feladatütemező beépülő modult a *taskschd. msc* parancs beírásával a futtatási ablakban
1. A bal oldali ablaktáblában navigáljon a Feladatütemező **könyvtár**  ->  **Microsoft**  ->  **OnlineBackup**.
1. A könyvtár minden feladatához kattintson duplán a feladatra a tulajdonságok megnyitásához, majd hajtsa végre a következő lépéseket:
    1. Váltson a **Beállítások** lapra.

         ![Beállítások lap](./media/backup-azure-mars-troubleshoot/settings-tab.png)

    1. Módosítsa a beállítást **, ha a feladat már fut, majd alkalmazza a következő szabályt**. Válassza **a ne indítson el új példányt** lehetőséget.

         ![Módosítsa a szabályt úgy, hogy ne indítsa el az új példányt](./media/backup-azure-mars-troubleshoot/change-rule.png)

## <a name="troubleshoot-restore-problems"></a>Visszaállítási problémák elhárítása

Előfordulhat, hogy a Azure Backup néhány perc elteltével sem sikerült csatlakoztatni a helyreállítási kötetet. A folyamat során hibaüzenetek jelenhetnek meg. A normál helyreállítás megkezdéséhez hajtsa végre a következő lépéseket:

1. Ha több percig is fut, szakítsa meg a csatlakoztatási folyamatot.

2. Ellenőrizze, hogy a biztonsági mentési ügynök legújabb verziója van-e telepítve. A verzió vizsgálatához a MARS-konzol **műveletek** paneljén válassza az **Microsoft Azure Recovery Services ügynök** elemet. Győződjön meg arról, hogy a **verziószám** értéke vagy magasabb, mint a [cikkben](https://go.microsoft.com/fwlink/?linkid=229525)említett verzió. Válassza ezt a hivatkozást [a legújabb verzió letöltéséhez](https://go.microsoft.com/fwLink/?LinkID=288905).

3. Nyissa meg **Eszközkezelő**  >  **Storage-vezérlőket** , és keresse meg a **Microsoft iSCSI-kezdeményezőt**. Ha megtalálta, lépjen közvetlenül a 7. lépésre.

4. Ha nem találja a Microsoft iSCSI-kezdeményező szolgáltatást, próbálja meg megkeresni a bejegyzést az  >  **ismeretlen eszköz** nevű Eszközkezelő **Storage-vezérlőkben** a **ROOT\ISCSIPRT** hardver-azonosítóval.

5. Kattintson a jobb gombbal az **ismeretlen eszköz** elemre, és válassza az **illesztőprogram-szoftver frissítése** lehetőséget.

6. Frissítse az illesztőprogramot úgy, hogy kijelöli a  **frissített illesztőprogram-szoftver automatikus keresésének** lehetőségét. A frissítésnek **ismeretlen eszközt** kell megváltoztatnia a **Microsoft iSCSI-kezdeményezőnek**:

    ![Képernyőkép a Azure Backup Eszközkezelőról, a Storage-vezérlők kiemelésével](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. Lépjen a **Task Manager**  >  **Services (helyi)**  >  **Microsoft iSCSI-kezdeményező szolgáltatáshoz**:

    ![Képernyőkép a Azure Backup Task Managerről, a szolgáltatások (helyi) kiemelve](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Indítsa újra a Microsoft iSCSI-kezdeményező szolgáltatást. Ehhez kattintson a jobb gombbal a szolgáltatásra, és válassza a **Leállítás** lehetőséget. Ezután kattintson rá a jobb gombbal, és válassza az **Indítás** lehetőséget.

9. Próbálja megismételni a helyreállítást az [azonnali visszaállítással](backup-instant-restore-capability.md).

Ha a helyreállítás továbbra is sikertelen, indítsa újra a kiszolgálót vagy az ügyfelet. Ha nem kívánja újraindítani a rendszert, vagy ha a helyreállítás még a kiszolgáló újraindítása után is sikertelen, próbálja meg [egy másik gépről helyreállítani](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="troubleshoot-cache-problems"></a>Gyorsítótárral kapcsolatos problémák elhárítása

A biztonsági mentési művelet sikertelen lehet, ha a gyorsítótár mappája (más néven a Scratch mappa) helytelenül van konfigurálva, hiányoznak az előfeltételek, vagy korlátozott hozzáférése van.

### <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy a MARS-ügynök műveletei sikeresek legyenek, a gyorsítótár mappájának meg kell felelnie az alábbi követelményeknek:

- [Győződjön meg arról, hogy a kihelyezett mappa helye 5% – 10% szabad lemezterülettel rendelkezik](backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Győződjön meg arról, hogy a mappa helye érvényes és elérhető](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)
- [Győződjön meg arról, hogy a gyorsítótár mappában található fájlattribútumok támogatottak](backup-azure-file-folder-backup-faq.md#are-there-any-attributes-of-the-cache-folder-that-arent-supported)
- [Győződjön meg arról, hogy a lefoglalt árnyékmásolat tárolóhelye elegendő a biztonsági mentési folyamathoz.](#increase-shadow-copy-storage)
- [Győződjön meg arról, hogy nincsenek más folyamatok (például víruskereső szoftverek) a gyorsítótár mappájához való hozzáférés korlátozásához](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>Árnyékmásolat-tároló javítása

A biztonsági mentési műveletek sikertelenek lehetnek, ha nincs elegendő árnyékmásolat-tárolóhely, amely az adatforrás védelme érdekében szükséges. A probléma megoldásához növelje az árnyékmásolat tárolóhelyét a védett köteten a **vssadmin** használatával az alábbi ábrán látható módon:

- A rendszergazda jogú parancssorban keresse meg az aktuális árnyékmásolat-tárolóhelyet:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- Növelje az árnyék tárolóhelyét a következő parancs használatával:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>Egy másik folyamat vagy víruskereső szoftver blokkolja a gyorsítótár mappához való hozzáférést

Ha a kiszolgálón telepítve van víruskereső szoftver, adja hozzá a szükséges kizárási szabályokat a következő fájlokhoz és mappákhoz tartozó víruskereső vizsgálathoz:  

- A Scratch mappa. Az alapértelmezett helye a `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
- A bin mappája a következő helyen: `C:\Program Files\Microsoft Azure Recovery Services Agent\Bin`
- CBengine.exe
- CSC.exe

## <a name="common-issues"></a>Gyakori problémák

Ez a szakasz a MARS-ügynök használata során felmerülő gyakori hibákat ismerteti.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

Hibaüzenet | Javasolt művelet
--|--
A Microsoft Azure Recovery Services-ügynök nem tudta elérni a biztonsági mentés ideiglenes helyen tárolt ellenőrzőösszegét | A probléma megoldásához hajtsa végre a következő lépéseket, és indítsa újra a kiszolgálót <br/> - [Ellenőrizze, hogy van-e olyan víruskereső vagy más folyamat, amely zárolja a fájlok a kaparós helyét](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Ellenőrizze, hogy a kiinduló hely érvényes-e, és elérhető-e a MARS-ügynök számára.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

Hibaüzenet | Javasolt művelet
--|--
A Microsoft Azure Recovery Services-ügynök nem tudta elérni az ideiglenes helyet virtuális merevlemez inicializálásához | A probléma megoldásához hajtsa végre a következő lépéseket, és indítsa újra a kiszolgálót <br/> - [Ellenőrizze, hogy a víruskereső vagy más folyamatok zárolják-e a fájlok a kaparós hely fájljait](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Ellenőrizze, hogy a kiinduló hely érvényes-e, és elérhető-e a MARS-ügynök számára.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

Hibaüzenet | Javasolt művelet
--|--
A biztonsági mentés nem sikerült, mert nincs elég hely a kötetben, ahol a kaparós mappa található. | A probléma megoldásához ellenőrizze a következő lépéseket, majd próbálja megismételni a műveletet:<br/>- [Győződjön meg arról, hogy a MARS-ügynök legújabb](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [A biztonsági másolatok területét érintő tárolási problémák ellenőrzése és feloldása](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

Hibaüzenet | Javasolt művelet
--|--
Nem találhatók módosítások a fájlban. Ennek több oka lehet. Próbálkozzon újra a művelettel | A probléma megoldásához ellenőrizze a következő lépéseket, majd próbálja megismételni a műveletet:<br/> - [Győződjön meg arról, hogy a MARS-ügynök legújabb](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [A biztonsági másolatok területét érintő tárolási problémák ellenőrzése és feloldása](#prerequisites)

## <a name="next-steps"></a>Következő lépések

- További információ a [Windows Server biztonsági mentéséről a Azure Backup ügynökkel](tutorial-backup-windows-server-to-azure.md).
- Ha vissza kell állítania egy biztonsági mentést, olvassa el a [fájlok visszaállítása Windows rendszerű gépre](backup-azure-restore-windows-server.md)című témakört.
