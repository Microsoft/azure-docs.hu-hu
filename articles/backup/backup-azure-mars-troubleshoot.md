---
title: A Azure Backup hibaelhárítása
description: Ebből a cikkből megtudhatja, hogyan háríthatja el a hibakeresést a Azure Backup telepítésével és Azure Backup telepítésével.
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: c662bf8c8d9490691f45254bef01618f17bd6e2a
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518184"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Az Microsoft Azure Recovery Services- (MARS-) ügynök hibaelhárítása

Ez a cikk a konfiguráció, regisztráció, biztonsági mentés és visszaállítás során előforduló hibák elhárítását ismerteti.

## <a name="basic-troubleshooting"></a>Alapszintű hibaelhárítás

Javasoljuk, hogy mielőtt elkezdené a Microsoft Azure Recovery Services- (MARS-) ügynök hibaelhárítását, ellenőrizze a következőket:

- [Győződjön meg arról, hogy a MARS-ügynök naprakész.](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Győződjön meg arról, hogy rendelkezik hálózati kapcsolattal a MARS-ügynök és az Azure között.](#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Győződjön meg arról, hogy a MARS fut (a Szolgáltatáskonzolon). Ha szükséges, indítsa újra, majd próbálja újra a műveletet.
- [Győződjön meg arról, hogy 5–10%](./backup-azure-file-folder-backup-faq.yml#what-s-the-minimum-size-requirement-for-the-cache-folder-)szabad lemezterület áll rendelkezésre az üres mappa helyén.
- [Ellenőrizze, hogy egy másik folyamat vagy víruskereső szoftver nem zavarja-e az Azure Backup működését](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup).
- Ha a biztonsági mentési feladat figyelmeztetésekkel fejeződött be, tekintse meg a következőt: Backup Jobs Completed with Warning (A biztonsági mentési feladatok [figyelmeztetéssel fejeződtek be)](#backup-jobs-completed-with-warning)
- Ha az ütemezett biztonsági mentés sikertelen, de a manuális biztonsági mentés működik, lásd: A biztonsági [mentések nem az ütemezés szerint futnak.](#backups-dont-run-according-to-schedule)
- Ellenőrizze, hogy az operációs rendszer rendelkezik-e a legújabb frissítésekkel.
- [Győződjön meg arról, hogy a](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)nem támogatott meghajtók és fájlok nem támogatott attribútumokkal ki vannak zárva a biztonsági mentésből.
- Győződjön meg arról, hogy a védett rendszer órája a megfelelő időzónára van konfigurálva.
- [Győződjön .NET-keretrendszer, hogy a 4.5.2-es](https://www.microsoft.com/download/details.aspx?id=30653)vagy újabb rendszer telepítve van a kiszolgálón.
- Ha egy tárolóban próbálja újra regisztrálni a kiszolgálót:
  - Győződjön meg arról, hogy az ügynök el lett távolítva a kiszolgálón, és törölve lett a portálról.
  - Használja ugyanazt a jelszót, amely eredetileg a kiszolgáló regisztrálására volt használva.
- Offline biztonsági mentések esetén a biztonsági mentés Azure PowerShell előtt győződjön meg arról, hogy a 3.7.0-s és a forrás- és a másolási számítógépre is telepítve van.
- Ha a Backup ügynök egy Azure-beli virtuális gépen fut, tekintse meg [ezt a cikket.](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine)

## <a name="invalid-vault-credentials-provided"></a>A tároló megadott hitelesítő adatai érvénytelenek

**Hibaüzenet:** A tároló megadott hitelesítő adatai érvénytelenek. A fájl sérült, vagy nem rendelkezik a helyreállítási szolgáltatással társított legújabb hitelesítő adatokkal. (Azonosító: 34513)

| Ok | Javasolt műveletek |
| ---     | ---    |
| **A tároló hitelesítő adatai nem érvényesek** <br/> <br/> Előfordulhat, hogy a tároló hitelesítőadat-fájljai sérültek, lejártak, vagy más fájlkiterjesztésük lehet, mint a *.vaultCredentials fájlnak.* (Előfordulhat például, hogy a regisztráció előtt több mint 10 nappal letöltötték őket.)| [Töltse le az új hitelesítő adatokat](backup-azure-file-folder-backup-faq.yml#where-can-i-download-the-vault-credentials-file-) a helyreállítási tárból a Azure Portal. Ezután szükség szerint kövesse az alábbi lépéseket: <ul><li> Ha már telepítette és regisztrálta a MARS-t, nyissa meg Microsoft Azure Backup ügynök MMC-konzolját. Ezután válassza **a Kiszolgáló regisztrálása** lehetőséget a **Műveletek panelen** a regisztráció befejezéséhez az új hitelesítő adatokkal. <br/> <li> Ha az új telepítés sikertelen, próbálkozzon újra az újratelepítéssel az új hitelesítő adatokkal.</ul> **Megjegyzés:** Ha több tároló hitelesítőadat-fájlja lett letöltve, csak a legújabb fájl érvényes a következő 10 napra. Javasoljuk, hogy töltsön le egy új hitelesítőadat-fájlt a tárolóhoz.
| **A proxykiszolgáló/tűzfal blokkolja a regisztrációt** <br/>vagy <br/>**Nincs internetkapcsolat** <br/><br/> Ha a gép vagy a proxykiszolgáló korlátozott internetkapcsolattal rendelkezik, és nem biztosít hozzáférést a szükséges URL-címekhez, a regisztráció sikertelen lesz.| Kövesse az alábbi lépéseket:<br/> <ul><li> Működjön együtt az it-csoporttal annak biztosítása érdekében, hogy a rendszer rendelkezik internetkapcsolattal.<li> Ha nem használ proxykiszolgálót, győződjön meg arról, hogy a proxybeállítás nincs kiválasztva az ügynök regisztrálásakor. [Ellenőrizze a proxybeállításokat.](#verifying-proxy-settings-for-windows)<li> Ha rendelkezik tűzfal-/proxykiszolgálóval, működjön együtt a hálózatkezelő csapattal annak biztosítása érdekében, hogy ezek az URL-címek és IP-címek hozzáférnek:<br/> <br> **URL-címek**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>`www.msftconnecttest.com`<br><br>**IP-címek**<br>  20.190.128.0/18 <br>  40.126.0.0/18<br> <br/></ul></ul>Az előző hibaelhárítási lépések befejezése után próbálkozzon újra a regisztrációval.<br></br> Ha a kapcsolat egy Azure ExpressRoute keresztül történik, győződjön meg arról, hogy a beállítások a következő [Azure ExpressRoute vannak konfigurálva:](backup-support-matrix-mars-agent.md#azure-expressroute-support).
| **A víruskereső szoftver blokkolja a regisztrációt** | Ha a kiszolgálón víruskereső szoftver van telepítve, adja hozzá a szükséges kizárási szabályokat a víruskeresőben a következő fájlok és mappák kereséséhez: <br/><ul> <li> CBengine.exe <li> CSC.exe<li> Az scratch mappa. Az alapértelmezett helye a C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch. <li> A C:\Program Files\Microsoft Azure Recovery Services Agent\Bin mappában.

### <a name="additional-recommendations"></a>További ajánlások

- A C:/Windows/Temp mappában ellenőrizze, hogy több mint 60 000 vagy 65 000 .tmp kiterjesztésű fájl található-e. Ha vannak, törölje ezeket a fájlokat.
- Győződjön meg arról, hogy a gép dátuma és ideje megegyezik a helyi időzónában.
- Győződjön [meg arról, hogy](install-mars-agent.md#verify-internet-access) ezek a helyek hozzá vannak adva a megbízható helyekhez a Internet Explorer.

### <a name="verifying-proxy-settings-for-windows"></a>Proxybeállítások ellenőrzése Windows rendszeren

1. Töltse le a PsExecet a [Sysinternals oldalról.](/sysinternals/downloads/psexec)
1. Futtassa `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` a parancsot egy rendszergazda jogú parancssorból.

   Ez a parancs megnyitja a Internet Explorer.
1. Kattintson az **Eszközök** Internetes  >  **beállítások**  >  **Kapcsolatok**  >  **LAN-beállításai elemre.**
1. Ellenőrizze a rendszerfiók proxybeállítását.
1. Ha nincs konfigurálva proxy, és a proxy részletei meg vannak állítva, távolítsa el a részleteket.
1. Ha proxy van konfigurálva, és a proxy adatai helytelenek, ellenőrizze, hogy a **proxy IP-címe** és **a port** adatai helyesek-e.
1. Zárja be az Internet Explorert.

## <a name="unable-to-download-vault-credential-file"></a>A tároló hitelesítőadat-fájlja nem tölthető le

| Hiba   | Javasolt műveletek |
| ---     | ---    |
|Nem sikerült letölteni a tároló hitelesítőadat-fájlját. (Azonosító: 403) | <ul><li> Próbálja meg másik böngészővel letölteni a tároló hitelesítő adatait, vagy kövesse az alábbi lépéseket: <ul><li> Kezdje Internet Explorer. Válassza az F12 lehetőséget. </li><li> A Hálózat **lapon** törölje a gyorsítótárat és a cookie-kat. </li> <li> Frissítse az oldalt.<br></li></ul> <li> Ellenőrizze, hogy az előfizetés le van-e tiltva/lejárt-e.<br></li> <li> Ellenőrizze, hogy valamelyik tűzfalszabály blokkolja-e a letöltést. <br></li> <li> Győződjön meg arról, hogy nem merült ki a tároló korlátja (tárolónként 50 gép).<br></li>  <li> Győződjön meg arról, hogy a Azure Backup rendelkezik a tároló hitelesítő adatainak letöltéséhez és a kiszolgáló a tárolóban való regisztrálásához szükséges jogosultságokkal. Lásd: Use Azure role-based access control to manage Azure Backup recovery points (Az Azure szerepköralapú [hozzáférés-vezérlésének használata Azure Backup helyreállítási pontok kezeléséhez).](backup-rbac-rs-vault.md)</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>A Microsoft Azure Recovery Service-ügynök nem tudott kapcsolódni a Microsoft Azure Backuphoz

| Hiba  | Lehetséges ok | Javasolt műveletek |
| ---     | ---     | ---    |
| <br /><ul><li>Az Microsoft Azure Recovery Service Agent nem tudott csatlakozni a Microsoft Azure Backup. (Azonosító: 100050) Ellenőrizze a hálózati beállításokat, és ellenőrizze, hogy tud-e csatlakozni az internethez.<li>(407) Proxyhitelesítés szükséges. |Egy proxy blokkolja a kapcsolatot. |  <ul><li>A Internet Explorer a **Tools** Internet options Security Internet (Eszközök internetes  >    >  **beállításai– Biztonsági**  >  **internet) elemre.** Válassza **az Egyéni szint lehetőséget,** és görgessen le a Fájl letöltése **szakaszhoz.** Válassza az **Engedélyezés** lehetőséget.<p>Előfordulhat, hogy URL-címeket és [IP-címeket](install-mars-agent.md#verify-internet-access) is hozzá kell adni a megbízható helyekhez a Internet Explorer.<li>Módosítsa a beállításokat proxykiszolgáló használatára. Ezután adja meg a proxykiszolgáló adatait.<li> Ha a gépe korlátozott internetkapcsolattal rendelkezik, győződjön meg arról, hogy a gép vagy a proxy tűzfalbeállításai engedélyezik ezeket az [URL-címeket és IP-címeket.](install-mars-agent.md#verify-internet-access) <li>Ha a kiszolgálón víruskereső szoftver van telepítve, zárja ki ezeket a fájlokat a víruskereső vizsgálatból: <ul><li>CBEngine.exe (nem dpmra.exe).<li>CSC.exe (kapcsolódó .NET-keretrendszer). A kiszolgálón telepített CSC.exe minden .NET-keretrendszer külön-külön van meg. Zárja CSC.exe a fájlokat az érintett .NET-keretrendszer összes verziójához. <li>Az scratch mappa vagy a gyorsítótár helye. <br>Az új mappa vagy a gyorsítótár elérési útja alapértelmezett helye a C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.<li>A C:\Program Files\Microsoft Azure Recovery Services Agent\Bin mappában.

## <a name="the-specified-vault-credential-file-cannot-be-used-as-it-is-not-downloaded-from-the-vault-associated-with-this-server"></a>A megadott tároló hitelesítőadat-fájlja nem használható, mert nincs letöltve a kiszolgálóhoz társított tárolóból

| Hiba  | Lehetséges ok | Javasolt műveletek |
| ---     | ---     | ---    |
| A megadott tároló hitelesítőadat-fájlja nem használható, mert nincs letöltve a kiszolgálóhoz társított tárolóból. (Azonosító: 100110) Adja meg a tároló megfelelő hitelesítő adatait. | A tároló hitelesítőadat-fájlja nem abból a tárolóból található, amelybe ez a kiszolgáló már regisztrálva van. | Győződjön meg arról, hogy a célgép és a forrásgép ugyanabban a Recovery Services-tárolóban van regisztrálva. Ha a célkiszolgáló már regisztrálva van egy  másik tárolóban, a Kiszolgáló regisztrálása lehetőséggel regisztrálhat a megfelelő tárolóra.  

## <a name="backup-jobs-completed-with-warning"></a>A biztonsági mentési feladatok figyelmeztetéssel fejeződtek be

- Amikor a MARS-ügynök a biztonsági mentés során fájlokat és mappákat iterál, különböző feltételekkel találkozhat, amelyek miatt a biztonsági mentés figyelmeztetésekkel kiegészítettként lesz megjelölve. Ezekben a feltételekben a feladat figyelmeztetésekkel befejezettként jelenik meg. Ez rendben van, de ez azt jelenti, hogy legalább egy fájlról nem volt biztonsági mentése. Ezért a feladat kihagyta ezt a fájlt, de az adatforráson lévő összes többi fájlról biztonságimentett.

  ![A biztonsági mentési feladat figyelmeztetésekkel fejeződött be](./media/backup-azure-mars-troubleshoot/backup-completed-with-warning.png)

- A fájlok biztonsági mentésének kihagyását okozható feltételek többek között a következők:
  - Nem támogatott fájlattribútumok (például egy OneDrive-mappában, Tömörített stream, újraelemzési pontok). A teljes listát lásd a támogatási [mátrixban.](./backup-support-matrix-mars-agent.md#supported-file-types-for-backup)
  - Fájlrendszerrel kapcsolatban probléma
  - Egy másik folyamat zavarja a fájlokat (például a fájlkezelő víruskereső szoftver megakadályozhatja, hogy a MARS-ügynök hozzáférjen a fájlokhoz)
  - Alkalmazás által zárolt fájlok  

- A biztonsági mentési szolgáltatás sikertelenként fogja megjelölni ezeket a fájlokat a naplófájlban, a következő elnevezési konvencióval: *LastBackupFailedFilesxxxx.txt* *a C:\Program Files\Microsoft Azure Recovery Service Agent\temp* mappában.
- A probléma megoldásához tekintse át a naplófájlt a probléma természetének áttekintésével:

  | Hibakód             | Okok miatt                                             | Javaslatok                                              |
  | ---------------------- | --------------------------------------------------- | ------------------------------------------------------------ |
  | 0x80070570             | A fájl vagy könyvtár sérült és olvashatatlan. | Futtassa **a chkdsk-et** a forrásköteten.                             |
  | 0x80070002, 0x80070003 | A rendszer nem találja a megadott fájlt.         | [Győződjön meg arról, hogy az scratch mappa nem tele van](/backup-azure-file-folder-backup-faq.yml#manage-the-backup-cache-folder)  <br><br>  Ellenőrizze, hogy létezik-e az a kötet, ahol az üres terület konfigurálva van (nem lett törölve)  <br><br>   [Győződjön meg arról, hogy a MARS-ügynök ki van zárva a gépen telepített víruskeresőből](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)  |
  | 0x80070005             | A hozzáférés megtagadva                                    | [Ellenőrizze, hogy a víruskereső vagy más külső szoftver blokkolja-e a hozzáférést](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)     |
  | 0x8007018b             | A felhőalapú fájlhoz való hozzáférés le van tiltva.                | OneDrive-fájlok, Git-fájlok vagy bármilyen más olyan fájl, amely offline állapotban lehet a gépen |

- A [kizárási szabályok meglévő](./backup-azure-manage-mars.md#add-exclusion-rules-to-existing-policy) szabályzathoz való hozzáadásával kizárhat nem támogatott, hiányzó vagy törölt fájlokat a biztonsági mentési házirendből a sikeres biztonsági mentések biztosítása érdekében.

- Kerülje az azonos nevű védett mappák törlését és újralétrehozását a legfelső szintű mappában. Ez azt eredményezheti, hogy a biztonsági mentés figyelmeztetésekkel befejeződik, és a következő hibaüzenet jelenik meg: Kritikus *inkonzisztencia észlelhető,* ezért a módosítások nem replikálhatók.  Ha törölnie és újból létre kell hoznia a mappákat, fontolja meg ezt a védett legfelső szintű mappa almappákban.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Nem sikerült a biztonsági mentések biztonságossá tételéhez szükséges titkosítási kulcs beállítása

| Hiba | Lehetséges okok | Javasolt műveletek |
| ---     | ---     | ---    |
| <br />Nem sikerült beállítani a titkosítási kulcsot a biztonságos biztonsági mentéshez. Az aktiválás nem sikerült teljes mértékben, de a titkosítási jelszót a rendszer a következő fájlba mentette. |<li>A kiszolgáló már regisztrálva van egy másik tárolóval.<li>A konfigurálás során a jelszó sérült.| A kiszolgáló regisztrációjának a tárolóban való regisztrációja és regisztrálása egy új jelszóval.

## <a name="the-activation-did-not-complete-successfully"></a>Az aktiválás nem fejeződött be sikeresen

| Hiba  | Lehetséges okok | Javasolt műveletek |
|---------|---------|---------|
|<br />Az aktiválás sikertelenül zárult. Az aktuális művelet egy belső szolgáltatáshiba miatt meghiúsult [0x1FC07]. Próbálja meg újra a műveletet később. If the issue persists, please contact Microsoft support. (Az Azure Key Vault-művelet meghiúsult. Próbálja meg újból végrehajtani a műveletet. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához.)     | <li> Az üres mappa egy olyan köteten található, amely nem rendelkezik elegendő szabad hellyel. <li> Az scratch mappa helytelenül lett áthelyezve. <li> Az OnlineBackup.KEK fájl hiányzik.         | <li>Frissítsen [a MARS-ügynök legújabb](https://aka.ms/azurebackup_agent) verziójára.<li>Helyezze át az üres mappát vagy a gyorsítótárat egy olyan kötetre, amely a biztonsági mentési adatok teljes méretének 5–10%-a közötti szabad területtel van eltolás. A gyorsítótár helyének helyes áthelyezéséhez tekintse meg a fájlok és mappák biztonsági mentése gyakori [kérdéseit.](/backup-azure-file-folder-backup-faq.yml#manage-the-backup-cache-folder)<li> Győződjön meg arról, hogy az OnlineBackup.KEK fájl jelen van. <br>Az alapértelmezett hely az új mappa vagy a gyorsítótár elérési útja: *C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.*        |

## <a name="encryption-passphrase-not-correctly-configured"></a>A titkosítási jelszó helytelenül van konfigurálva

| Hiba  | Lehetséges okok | Javasolt műveletek |
|---------|---------|---------|
| <br />34506-os hiba. A számítógépen tárolt titkosítási jelszó nincs megfelelően konfigurálva.    | <li> Az üres mappa egy olyan köteten található, amely nem rendelkezik elegendő szabad hellyel. <li> Az scratch mappa helytelenül lett áthelyezve. <li> Az OnlineBackup.KEK fájl hiányzik.        | <li>Frissítsen [a MARS-ügynök legújabb](https://aka.ms/azurebackup_agent) verziójára.<li>Helyezze át az üres mappát vagy a gyorsítótár helyét egy olyan kötetre, amely a biztonsági mentési adatok teljes méretének 5–10%-a között van szabad területtel. A gyorsítótár helyének helyes áthelyezéséhez tekintse meg a fájlok és mappák biztonsági mentése gyakori [kérdéseit.](/backup-azure-file-folder-backup-faq.yml#manage-the-backup-cache-folder)<li> Győződjön meg arról, hogy az OnlineBackup.KEK fájl jelen van. <br>Az alapértelmezett hely az új mappa vagy a gyorsítótár elérési útja: *C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.*         |

## <a name="backups-dont-run-according-to-schedule"></a>A biztonsági mentések nem az ütemezés szerint futnak

Ha az ütemezett biztonsági mentések nem aktiválódnak automatikusan, de a manuális biztonsági mentések megfelelően működnek, próbálkozzon a következő műveletekkel:

- Győződjön meg arról, hogy a Windows Server biztonsági mentés ütemezése nem ütközik az Azure-fájlok és -mappák biztonsági mentésének ütemezésében.

- Győződjön meg arról, hogy az online biztonsági mentés állapota Enable **(Engedélyezés)**. Az állapot ellenőrzéséhez kövesse az alábbi lépéseket:

  1. A Feladatütemező bontsa ki a **Microsoft et,** és válassza **az Online biztonsági mentés lehetőséget.**
  1. Kattintson duplán a **Microsoft-OnlineBackup elemre,** és válton az **Eseményindítók lapra.**
  1. Ellenőrizze, hogy az állapot Engedélyezve **állapotú-e.** Ha nem, válassza a **Szerkesztés** lehetőséget, válassza az **Engedélyezve** lehetőséget, majd kattintson az **OK gombra.**

- Győződjön meg arról, hogy a feladat futtatásához kiválasztott felhasználói fiók **SYSTEM** vagy Helyi rendszergazdák **csoportja** a kiszolgálón. A felhasználói fiók ellenőrzéséhez  válassza az Általános lapot, és jelölje be a **Biztonsági beállításokat.**

- Győződjön meg arról, hogy a PowerShell 3.0-s vagy újabb telepítve van a kiszolgálón. A PowerShell verziójának ellenőrzéséhez futtassa ezt a parancsot, és ellenőrizze, hogy a `Major` verziószám 3 vagy újabb:

  `$PSVersionTable.PSVersion`

- Győződjön meg arról, hogy ez az elérési út a környezeti `PSMODULEPATH` változó része:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Ha a PowerShell végrehajtási szabályzata a következőre van beállítva: , akkor a biztonsági mentési feladatot aktiváló `LocalMachine` `restricted` PowerShell-parancsmag sikertelen lehet. Futtassa ezeket a parancsokat emelt szintű módban a végrehajtási szabályzat ellenőrzéshez és a vagy a `Unrestricted` `RemoteSigned` beállításhoz:

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
 ```

- Győződjön meg arról, hogy nincsenek hiányzó vagy sérült MSOnlineBackup PowerShell-modulfájlok. Ha vannak hiányzó vagy sérült fájlok, kövesse az alábbi lépéseket:

  1. Minden olyan gépről, amely megfelelően működik a MARS-ügynökkel, másolja az MSOnlineBackup mappát a C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules mappából.
  1. A problémás gépen illessze be a másolt fájlokat ugyanabba a mappába (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules).

     Ha már van MSOnlineBackup mappa a gépen, illessze be a fájlokat a fájlba, vagy cserélje le a meglévő fájlokat.

> [!TIP]
> A módosítások konzisztens alkalmazása érdekében indítsa újra a kiszolgálót az előző lépések végrehajtása után.

## <a name="resource-not-provisioned-in-service-stamp"></a>Az erőforrás nincs kiépítve a szolgáltatásbélyegzőben

Hiba | Lehetséges okok | Javasolt műveletek
--- | --- | ---
Az aktuális művelet a következő belső szolgáltatási hiba miatt meghiúsult: "Az erőforrás nincs kiépítve a szolgáltatásbélyegben". Próbálja meg később újra a műveletet. (Azonosító: 230006) | A védett kiszolgáló új nevet ad. | <li> Nevezze át a kiszolgálót a tárolóban regisztrált eredeti névre. <br> <li> Regisztrálja újra a kiszolgálót a tárolóban az új névvel.

## <a name="job-could-not-be-started-as-another-job-was-in-progress"></a>A feladat nem indult el, mert egy másik feladat van folyamatban

Ha egy figyelmeztető üzenet jelenik meg a **MARS-konzol** feladatelőzményében, amely szerint "A feladat nem indítható el, mert egy másik feladat van folyamatban", ennek oka a feladat duplikált példánya lehet, amelyet a  >  Feladatütemező.

![A feladat nem indult el, mert egy másik feladat van folyamatban](./media/backup-azure-mars-troubleshoot/job-could-not-be-started.png)

A probléma megoldása:

1. Indítsa el Feladatütemező beépülő modult úgy, hogy beírja a *taskschd.msc helyére* a Futtatás ablakba
1. A bal oldali panelen lépjen a **Feladatütemező**  ->  **Microsoft**  ->  **OnlineBackup könyvtárba.**
1. A kódtár minden feladatához kattintson duplán a feladatra a tulajdonságok megnyitásához és a következő lépések végrehajtásához:
    1. Váltson a **Beállítások lapra.**

         ![Beállítások lap](./media/backup-azure-mars-troubleshoot/settings-tab.png)

    1. Módosítsa a Ha a feladat már fut **beállítást, akkor a következő szabály lesz érvényes.** Válassza **a Ne indíts el új példányt lehetőséget.**

         ![Módosítsa a szabályt úgy, hogy ne indítsa el az új példányt](./media/backup-azure-mars-troubleshoot/change-rule.png)

## <a name="troubleshoot-restore-problems"></a>Visszaállítási problémák elhárítása

Azure Backup, hogy néhány perc után sem tudja sikeresen csatlakoztatni a helyreállítási kötetet. A folyamat során hibaüzenetek jelenhetnek meg. A normál helyreállítás megkezdéséhez kövesse az alábbi lépéseket:

1. Ha már több percig fut, mondja le a csatlakoztatási folyamatot.

2. Ellenőrizze, hogy a Backup ügynök legújabb verziója van-e rendelkezésre. A verzió ellenőrzéséhez  a MARS-konzol Műveletek paneljén válassza az **About Microsoft Azure Recovery Services Agent lehetőséget.** Győződjön meg arról, hogy a **Verziószám** a cikkben említett verzióval egyenlő vagy annál [magasabb.](https://go.microsoft.com/fwlink/?linkid=229525) Kattintson erre a hivatkozásra [a legújabb verzió letöltéséhez.](https://go.microsoft.com/fwLink/?LinkID=288905)

3. Keresse meg **Eszközkezelő** Tárolóvezérlők gombra, és keresse meg a  >   **Microsoft iSCSI-kezdeményezőt.** Ha megtalálja, lépjen közvetlenül a 7. lépésre.

4. Ha nem találja a Microsoft iSCSI-kezdeményező szolgáltatást, próbáljon meg megkeresni egy bejegyzést a **Eszközkezelő** Ismeretlen eszköz nevű tárolóvezérlők alatt a  >    **ROOT\ISCSIPRT hardverazonosítóval.**

5. Kattintson a jobb gombbal **az Ismeretlen eszköz elemre,** és válassza **az Illesztőprogram-szoftver frissítése lehetőséget.**

6. Frissítse az illesztőt a Frissített illesztőprogram-szoftverek **automatikus keresése lehetőség kiválasztásával.** A frissítésnek Az ismeretlen **eszközről** **Microsoft iSCSI-kezdeményezőre kell változnia:**

    ![Képernyőkép a Azure Backup Eszközkezelő, a Storage-vezérlők kiemeléssel](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. Ugrás a **feladatkezelő**  >  **(helyi)**  >  **Microsoft iSCSI-kezdeményező szolgáltatásra:**

    ![Képernyőkép a Azure Backup feladatkezelő, kiemelt Szolgáltatások (helyi) gombra](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Indítsa újra a Microsoft iSCSI-kezdeményező szolgáltatást. Ehhez kattintson a jobb gombbal a szolgáltatásra, és válassza a **Stop (Leállítás) lehetőséget.** Ezután kattintson rá ismét a jobb gombbal, és válassza az **Indítás lehetőséget.**

9. Próbálja meg újra a helyreállítást az [Azonnali visszaállítás használatával.](backup-instant-restore-capability.md)

Ha a helyreállítás továbbra is sikertelen, indítsa újra a kiszolgálót vagy az ügyfelet. Ha nem szeretné újraindítani a rendszert, vagy ha a helyreállítás a kiszolgáló újraindítása után is sikertelen, próbálkozzon egy másik gép [visszaállításával.](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)

## <a name="troubleshoot-cache-problems"></a>Gyorsítótárral kapcsolatos problémák elhárítása

A biztonsági mentési művelet meghiúsulhat, ha a gyorsítótármappa (más néven az új mappa) helytelenül van konfigurálva, hiányoznak az előfeltételek, vagy korlátozott hozzáféréssel rendelkezik.

### <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy a MARS-ügynök műveletei sikeresek legyenek, a gyorsítótár mappájának meg kell felelnie az alábbi követelményeknek:

- [Győződjön meg arról, hogy 5–10% szabad kötettér áll rendelkezésre az üres mappa helyén](backup-azure-file-folder-backup-faq.yml#what-s-the-minimum-size-requirement-for-the-cache-folder-)
- [Győződjön meg arról, hogy az scratch mappa helye érvényes és elérhető](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-)
- [Győződjön meg arról, hogy a gyorsítótármappa fájlattribútumai támogatottak](backup-azure-file-folder-backup-faq.yml#are-there-any-attributes-of-the-cache-folder-that-aren-t-supported-)
- [Győződjön meg arról, hogy a lefoglalt árnyékmásolati tárterület elegendő a biztonsági mentési folyamathoz](#increase-shadow-copy-storage)
- [Győződjön meg arról, hogy nincsenek más folyamatok (például víruskereső szoftverek), amelyek korlátozzák a gyorsítótármappákhoz való hozzáférést](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>Árnyékmásolatok tárterületének növelése

A biztonsági mentési műveletek meghiúsulnak, ha nincs elegendő árnyékmásolati tárterület az adatforrás védelméhez. A probléma megoldásához növelje az árnyékmásolat tárterületét a védett köteten a **vssadmin használatával** az alábbi módon:

- Ellenőrizze az aktuális árnyéktárolóhelyet az emelt szintű parancssorból:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- Növelje az árnyéktárhelyet a következő paranccsal:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>Egy másik folyamat vagy víruskereső szoftver blokkolja a gyorsítótármappához való hozzáférést

Ha a kiszolgálón víruskereső szoftver van telepítve, adja hozzá a szükséges kizárási szabályokat a víruskeresőben a következő fájlok és mappák kereséséhez:  

- Az scratch mappa. Az alapértelmezett helye: `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
- A bin mappa a következőnél: `C:\Program Files\Microsoft Azure Recovery Services Agent\Bin`
- CBengine.exe
- CSC.exe

## <a name="common-issues"></a>Gyakori problémák

Ez a szakasz a MARS-ügynök használata során előforduló gyakori hibákat tartalmazza.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

Hibaüzenet | Javasolt művelet
--|--
A Microsoft Azure Recovery Services-ügynök nem tudta elérni a biztonsági mentés ideiglenes helyen tárolt ellenőrzőösszegét | A probléma megoldásához hajtsa végre a következő lépéseket, és indítsa újra a kiszolgálót <br/> - [Ellenőrizze, hogy van-e víruskereső vagy más folyamat, amely zárolja az ideiglenes hely fájljait](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Ellenőrizze, hogy az új hely érvényes-e és elérhető-e a MARS-ügynök számára.](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

Hibaüzenet | Javasolt művelet
--|--
A Microsoft Azure Recovery Services-ügynök nem tudta elérni az ideiglenes helyet virtuális merevlemez inicializálásához | A probléma megoldásához hajtsa végre a következő lépéseket, és indítsa újra a kiszolgálót <br/> - [Ellenőrizze, hogy a víruskereső vagy más folyamatok zárolják-e az ideiglenes hely fájljait](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Ellenőrizze, hogy az új hely érvényes-e és elérhető-e a MARS-ügynök számára.](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

Hibaüzenet | Javasolt művelet
--|--
A biztonsági mentés nem sikerült, mert nincs elegendő tárterület a köteten, ahol az scratch mappa található | A probléma megoldásához ellenőrizze a következő lépéseket, majd próbálja újra a műveletet:<br/>- [Győződjön meg arról, hogy a MARS-ügynök a legfrissebb](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [Ellenőrizze és oldja meg a biztonsági mentést az üres tárhelyet negatívan befolyásoló tárolási problémákat](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

Hibaüzenet | Javasolt művelet
--|--
Nem találhatók módosítások a fájlban. Ennek több oka lehet. Próbálkozzon újra a művelettel | A probléma megoldásához ellenőrizze a következő lépéseket, majd próbálja újra a műveletet:<br/> - [Győződjön meg arról, hogy a MARS-ügynök a legfrissebb](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [Ellenőrizze és oldja meg a biztonsági mentést az üres tárhelyet negatívan befolyásoló tárolási problémákat](#prerequisites)

## <a name="next-steps"></a>Következő lépések

- További részletek a Windows Server biztonsági frissítésének biztonsági Azure Backup [ügynökkel.](tutorial-backup-windows-server-to-azure.md)
- Ha vissza kell állítania egy biztonsági másolatot, tekintse meg a fájlok [Windows rendszerű gépekre történő visszaállítását.](backup-azure-restore-windows-server.md)
