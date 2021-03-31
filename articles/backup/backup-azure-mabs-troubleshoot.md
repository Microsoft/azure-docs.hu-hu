---
title: Az Azure Backup Server hibaelhárítása
description: Hibaelhárítás, Azure Backup Server regisztrációja, valamint az alkalmazások számítási feladatainak biztonsági mentése és helyreállítása.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 09e5fe5da7e316257cbbdcb89074fe8a4bc692c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91403007"
---
# <a name="troubleshoot-azure-backup-server"></a>Az Azure Backup Server hibaelhárítása

A következő táblázatokban található információk segítségével elháríthatja a Azure Backup Server használata során felmerülő hibákat.

## <a name="basic-troubleshooting"></a>Alapszintű hibaelhárítás

Javasoljuk, hogy a következő érvényesítést a hibaelhárítás megkezdése előtt Microsoft Azure Backup Server (MABS) használata előtt végezze el:

- [Győződjön meg arról, Microsoft Azure Recovery Services (MARS) ügynök naprakész](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Ellenőrizze, hogy van-e hálózati kapcsolat a MARS-ügynök és az Azure között](./backup-azure-mars-troubleshoot.md#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Győződjön meg arról, hogy a Microsoft Azure Recovery Services fut (a Szolgáltatás konzolon). Szükség esetén indítsa újra, és ismételje meg a műveletet.
- [Ügyeljen arra, hogy a szabad lemezterület 5-10%-a a mappa helyén legyen elérhető](./backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- Ha a regisztráció sikertelen, akkor győződjön meg arról, hogy a kiszolgáló, amelyre telepíteni kívánja, Azure Backup Server még nincs regisztrálva egy másik tárolóban.
- Ha a leküldéses telepítés sikertelen, ellenőrizze, hogy a DPM-ügynök már jelen van-e a rendszeren. Ha igen, távolítsa el az ügynököt, és próbálkozzon újra a telepítéssel
- [Győződjön meg arról, hogy nem zavarja másik folyamat vagy víruskereső szoftver az Azure Backup működését](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)<br>
- Győződjön meg arról, hogy az SQL Agent szolgáltatás fut, és állítsa automatikusra a MABS-kiszolgálón<br>

## <a name="configure-antivirus-for-mabs-server"></a>A MABS-kiszolgáló víruskereső konfigurálása

A MABS kompatibilis a legnépszerűbb víruskereső szoftveres termékekkel. Az ütközések elkerülése érdekében a következő lépéseket javasoljuk:

1. A **valós idejű figyelés letiltása** – tiltsa le a víruskereső szoftver valós idejű figyelését a következőkhöz:
    - `C:\Program Files<MABS Installation path>\XSD` mappa
    - `C:\Program Files<MABS Installation path>\Temp` mappa
    - modern biztonsági másolati tárhely kötet meghajtóbetűjele
    - Replika-és adatátviteli naplók: ehhez tiltsa le a **dpmra.exe** valós idejű figyelését, amely a mappában található `Program Files\Microsoft Azure Backup Server\DPM\DPM\bin` . A valós idejű figyelés csökkenti a teljesítményt, mert a víruskereső szoftver minden alkalommal megvizsgálja a replikákat, amikor a MABS szinkronizálja a védett kiszolgálóval, és minden alkalommal megvizsgálja az összes érintett fájlt, amikor a MABS módosítja a replikákat.
    - Felügyeleti konzol: Ha el szeretné kerülni a teljesítményre gyakorolt hatást, tiltsa le a **csc.exe** folyamat valós idejű figyelését. A **csc.exe** folyamat a C \# fordító, és a valós idejű figyelés csökkentheti a teljesítményt, mert a víruskereső szoftver megvizsgálja azokat a fájlokat, amelyeket a **csc.exe** folyamat bocsát ki, amikor XML-üzeneteket hoz létre. **CSC.exe** a következő elérési utakon található:
        - `\Windows\Microsoft.net\Framework\v2.0.50727\csc.exe`
        - `\Windows\Microsoft.NET\Framework\v4.0.30319\csc.exe`
    - A MABS-kiszolgálóra telepített MARS-ügynök esetében javasoljuk, hogy zárja ki a következő fájlokat és helyet:
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\bin\cbengine.exe` folyamatként
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\folder`
        - Üres hely (ha nem a standard helyet használja)
2. A **védett kiszolgálón lévő valós idejű figyelés letiltása**: tiltsa le a védett kiszolgálón a mappában található **dpmra.exe** valós idejű figyelését `C:\Program Files\Microsoft Data Protection Manager\DPM\bin` .
3. A **védett kiszolgálókon és a MABS-kiszolgálón található fertőzött fájlok törlésére szolgáló víruskereső szoftver beállítása**: a replikák és a helyreállítási pontok adatsérülésének megakadályozása érdekében konfigurálja a víruskereső szoftvert a fertőzött fájlok törléséhez, nem pedig automatikusan a tisztításhoz vagy a karanténba helyezéshez. Az automatikus tisztítás és a karanténba helyezés okozhatja, hogy a víruskereső szoftver módosítja a fájlokat, így a MABS által nem észlelhető módosítások is megjelenhetnek.

A manuális szinkronizálást konzisztencia-ellenőrzéssel kell futtatni. Győződjön meg arról, hogy a víruskereső szoftver minden alkalommal töröl egy fájlt a replikából, még akkor is, ha a replika inkonzisztensként van megjelölve.

### <a name="mabs-installation-folders"></a>MABS telepítési mappák

A DPM alapértelmezett telepítési mappái a következők:

- `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM`

A telepítési mappa elérési útját a következő parancs futtatásával is megkeresheti:

```cmd
Reg query "HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Setup"
```

## <a name="invalid-vault-credentials-provided"></a>A tároló megadott hitelesítő adatai érvénytelenek

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Regisztráció egy tárba | A tároló megadott hitelesítő adatai érvénytelenek. A fájl sérült, vagy nem rendelkezik a helyreállítási szolgáltatáshoz társított legújabb hitelesítő adatokkal. | Javasolt művelet: <br> <ul><li> Töltse le a legújabb hitelesítő adatokat tartalmazó fájlt a tárból, és próbálkozzon újra. <br>VAGY</li> <li> Ha az előző művelet nem működött, próbálja meg letölteni a hitelesítő adatokat egy másik helyi könyvtárba, vagy hozzon létre egy új tárolót. <br>VAGY</li> <li> Próbálja meg frissíteni a dátum-és időbeállításokat a [jelen cikkben](./backup-azure-mars-troubleshoot.md#invalid-vault-credentials-provided)leírtak szerint. <br>VAGY</li> <li> Ellenőrizze, hogy a c:\Windows\Temp több mint 65000 fájllal rendelkezik-e. Helyezze át az elavult fájlokat egy másik helyre, vagy törölje a temp mappában lévő elemeket. <br>VAGY</li> <li> A tanúsítványok állapotának ellenőrzését. <br> a. Nyissa meg a **számítógép-tanúsítványok kezelése** (a Vezérlőpulton). <br> b. Bontsa ki a **személyes** csomópontot és annak alárendelt csomópontjának **tanúsítványait**.<br> c.  Távolítsa el a **Windows Azure-eszközök** tanúsítványát. <br> d. Próbálja megismételni a regisztrációt az Azure Backup-ügyfélen. <br> VAGY </li> <li> Ellenőrizze, hogy van-e érvényben a csoportházirend. </li></ul> |

## <a name="replica-is-inconsistent"></a>A replika inkonzisztens

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Backup | A replika inkonzisztens | Ellenőrizze, hogy a védelmi csoport varázslóban be van-e kapcsolva az automatikus konzisztencia-ellenőrzés lehetőség. A replikációs beállításokkal és a konzisztencia-ellenőrzésekkel kapcsolatos további információkért tekintse meg [ezt a cikket](/system-center/dpm/create-dpm-protection-groups) .<br> <ol><li> Rendszerállapot/BMR biztonsági mentés esetén ellenőrizze, hogy a Windows Server biztonsági másolat telepítve van-e a védett kiszolgálón.</li><li> Győződjön meg arról, hogy a DPM/Microsoft Azure Backup-kiszolgálón a DPM-tárolóban a tárterülettel kapcsolatos problémák vannak, és szükség szerint foglalja le a tárolót.</li><li> A védett kiszolgálón lévő Kötet árnyékmásolata szolgáltatás állapotának megtekintése. Ha letiltott állapotban van, állítsa be manuálisan a kezdéshez. Indítsa el a szolgáltatást a kiszolgálón. Ezután térjen vissza a DPM/Microsoft Azure Backup Server-konzolra, és indítsa el a szinkronizálást a konzisztencia-ellenőrzési feladatokkal.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Az online helyreállítási pont létrehozása nem sikerült

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Backup | Az online helyreállítási pont létrehozása nem sikerült | **Hibaüzenet**: a Windows Azure Backup ügynök nem tudott pillanatképet készíteni a kijelölt kötetről. <br> **Áthidaló megoldás**: próbálja meg növelni a helyet a replika és a helyreállítási pont kötetén.<br> <br> **Hibaüzenet**: a Windows Azure Backup ügynök nem tud csatlakozni a OBEngine szolgáltatáshoz <br> **Áthidaló megoldás**: Ellenőrizze, hogy a OBEngine létezik-e a számítógépen futó szolgáltatások listájában. Ha a OBEngine szolgáltatás nem fut, használja a "net start OBEngine" parancsot a OBEngine szolgáltatás elindításához. <br> <br> **Hibaüzenet**: nincs beállítva a kiszolgáló titkosítási jelszava. Konfigurálja a titkosítási jelszót. <br> **Áthidaló megoldás**: próbálkozzon titkosítási jelszó konfigurálásával. Ha nem sikerül, hajtsa végre a következő lépéseket: <br> <ol><li>Ellenőrizze, hogy létezik-e a megkarcolt hely. Ez az a hely, amely szerepel a beállításjegyzékben **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Configban**, és a név **ScratchLocation** léteznie kell.</li><li> Ha a megjelenő hely létezik, próbálja meg újból regisztrálni a régi jelszó használatával. *Ha titkosítási jelszót állít be, mentse biztonságos helyre.*</li><ol>|

## <a name="the-original-and-external-dpm-servers-must-be-registered-to-the-same-vault"></a>Az eredeti és a külső DPM-kiszolgálókat ugyanahhoz a tárolóhoz kell regisztrálni

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Visszaállítás | **Hibakód**: CBPServerRegisteredVaultDontMatchWithCurrent/tároló hitelesítő adatai hiba: 100110 <br/> <br/>**Hibaüzenet**: az eredeti és a külső DPM-kiszolgálókat ugyanahhoz a tárolóhoz kell regisztrálni. | **OK**: Ez a probléma akkor fordul elő, ha a fájlokat az eredeti kiszolgálóról a külső DPM helyreállítási lehetőség használatával szeretné visszaállítani a másodlagos kiszolgálóra, és ha a helyreállított kiszolgáló és az az eredeti kiszolgáló, ahonnan az adatok biztonsági mentése történik, nem ugyanahhoz a Recovery Services-tárolóhoz van társítva.<br/> <br/>**Áthidaló megoldás** A probléma megoldásához ellenőrizze, hogy az eredeti és az alternatív kiszolgáló is regisztrálva van-e ugyanahhoz a tárolóhoz.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>A VMware virtuális gép online helyreállítási pontjának létrehozási feladatai sikertelenek

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Backup | A VMware virtuális gép online helyreállítási pontjának létrehozási feladatai sikertelenek. A DPM a VMware hibába ütközött, miközben megpróbálta beolvasni a változáskövetési-információkat. ErrorCode-FileFaultFault (azonosító: 33621) |  <ol><li> Állítsa alaphelyzetbe a VMware-CTK az érintett virtuális gépeken.</li> <li>Győződjön meg arról, hogy a független lemez nem áll helyre a VMware-en.</li> <li>Állítsa le a védelmet az érintett virtuális gépek számára, és a **refresh (frissítés** ) gombbal tegye meg újra a védelmet. </li><li>Egy CC futtatása az érintett virtuális gépekhez.</li></ol>|

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>Az ügynök művelete sikertelen volt, mert kommunikációs hiba történt a DPM-ügynök koordinátori szolgáltatásával a kiszolgálón

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Ügynök (ek) leküldése a védett kiszolgálókra | Az ügynök művelete sikertelen volt, mert kommunikációs hiba történt a DPM-ügynökkoordinátor szolgáltatással \<ServerName> . | **Ha a termékben bemutatott javasolt művelet nem működik, hajtsa végre a következő lépéseket**: <ul><li> Ha nem megbízható tartományból csatlakoztat egy számítógépet, kövesse [az alábbi lépéseket](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains). <br> VAGY </li><li> Ha megbízható tartományból csatlakoztat egy számítógépet, akkor a [jelen blogban](https://techcommunity.microsoft.com/t5/system-center-blog/data-protection-manager-agent-network-troubleshooting/ba-p/344726)ismertetett lépések végrehajtásával hibaelhárítást végezhet. <br>VAGY</li><li> Próbálja meg letiltani a víruskeresőt hibaelhárítási lépésként. Ha feloldja a problémát, módosítsa a vírusvédelmi beállításokat a [jelen cikkben](/system-center/dpm/run-antivirus-server)javasolt módon.</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>A telepítő nem tudta frissíteni a beállításjegyzékbeli metaadatokat

| Művelet | A hiba részletei | Áthidaló megoldás |
|-----------|---------------|------------|
|Telepítés | A telepítő nem tudja frissíteni a beállításjegyzék metaadatait. Ez a frissítési hiba a tárterület-használat túlterheléséhez vezethet. Ennek elkerüléséhez frissítse a ReFS-vágási beállításjegyzékbeli bejegyzést. | Módosítsa a **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim** beállításkulcsot. Állítsa a DWORD értéket 1-re. |
|Telepítés | A telepítő nem tudja frissíteni a beállításjegyzék metaadatait. Ez a frissítési hiba a tárterület-használat túlterheléséhez vezethet. Ennek elkerüléséhez frissítse a kötet SnapOptimization beállításjegyzékbeli bejegyzését. | Hozza létre a SOFTWARE\Microsoft-adatvédelmi **Manager\Configuration\VolSnapOptimization\WriteIds** beállításkulcsot egy üres karakterlánc-értékkel. |

## <a name="registration-and-agent-related-issues"></a>Regisztráció és ügynökkel kapcsolatos problémák

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Ügynök (ek) leküldése a védett kiszolgálókra | A kiszolgálóhoz megadott hitelesítő adatok érvénytelenek. | **Ha a termékben bemutatott javasolt művelet nem működik, hajtsa végre a következő lépéseket**: <br> Próbálja meg manuálisan telepíteni a védelmi ügynököt az üzemi kiszolgálón az ebben a [cikkben](/system-center/dpm/deploy-dpm-protection-agent)megadott módon.|
| Azure Backup ügynök nem tudott csatlakozni a Azure Backup szolgáltatáshoz (azonosító: 100050) | A Azure Backup ügynök nem tudott kapcsolódni a Azure Backup szolgáltatáshoz. | **Ha a termékben bemutatott javasolt művelet nem működik, hajtsa végre a következő lépéseket**: <br>1. Futtassa a következő parancsot egy emelt szintű parancssorból: **PsExec-i-s "C:\Program Files\Internet Explorer\iexplore.exe**. Ekkor megnyílik az Internet Explorer ablaka. <br/> 2. Lépjen az **eszközök**  >  **internetes beállítások**  >  **kapcsolatok**  >  **LAN-beállítások menüpontra**. <br/> 3. módosítsa a beállításokat a proxykiszolgáló használatára. Ezután adja meg a proxykiszolgáló részleteit.<br/> 4. Ha a számítógépén korlátozott az Internet-hozzáférés, győződjön meg arról, hogy a számítógép vagy a proxy tűzfal beállításai lehetővé teszik ezeket az [URL-címeket](install-mars-agent.md#verify-internet-access) és [IP-címeket](install-mars-agent.md#verify-internet-access).|
| Azure Backup ügynök telepítése nem sikerült | A Microsoft Azure Recovery Services telepítése sikertelen volt. A rendszer által a Microsoft Azure Recovery Services telepítés során végrehajtott összes módosítás vissza lett állítva. (AZONOSÍTÓ: 4024) | Manuálisan telepítse az Azure-ügynököt.

## <a name="configuring-protection-group"></a>Védelmi csoport konfigurálása

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Védelmi csoportok konfigurálása | A DPM nem tudja felsorolni az alkalmazás összetevőjét a védett számítógépen (a védett számítógép neve). | Válassza a **frissítés** lehetőséget a védelmi csoport felhasználói felületének konfigurálása képernyőn a megfelelő DataSource/Component szinten. |
| Védelmi csoportok konfigurálása | Nem konfigurálható védelem | Ha a védett kiszolgáló egy SQL-kiszolgáló, ellenőrizze, hogy az [ebben a cikkben](/system-center/dpm/back-up-sql-server)leírtak szerint meg lett-e biztosítva a rendszerfiók (NTAuthority\System) rendszergazdai szerepkör engedélyei a védett számítógépen.
| Védelmi csoportok konfigurálása | Nincs elegendő szabad terület a tárolóban ehhez a védelmi csoporthoz. | A tárolóhelyhez hozzáadott lemezek [nem tartalmazhatnak partíciót](/system-center/dpm/create-dpm-protection-groups). Törölje a lemezeken lévő összes meglévő kötetet. Ezután adja hozzá őket a Storage-készlethez.|
| Szabályzat módosítása |Nem lehet módosítani a biztonsági mentési szabályzatot. Hiba: az aktuális művelet végrehajtása egy belső szolgáltatáshiba ([0x29834]) miatt meghiúsult. Némi idő elteltével próbálja megismételni a műveletet. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához. | **Ok:**<br/>Ez a hiba három feltétel teljesülése esetén fordul elő: Ha a biztonsági beállítások engedélyezve vannak, akkor a rendszer a korábban megadott minimális értékek alatti megőrzési időtartamot próbálja csökkenteni, és ha nem támogatott verziót használ. (A nem támogatott verziók a Microsoft Azure Backup Server 2.0.9052 és a Azure Backup Server Update 1 verziónál alacsonyabbak.) <br/>**Javasolt művelet:**<br/> A házirendekkel kapcsolatos frissítések folytatásához állítsa a megadott megőrzési időszakot a minimális megőrzési időtartam fölé. (A minimális megőrzési idő napi hét nap, heti három hét, havi vagy egy év éves időszakra szól.) <br><br>Egy másik előnyben részesített módszer a biztonsági mentési ügynök és a Azure Backup Server frissítése az összes biztonsági frissítés kihasználása érdekében. |

## <a name="backup"></a>Backup

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Backup | Váratlan hiba történt a feladatok futása közben. Az eszköz nem áll készen. | **Ha a termékben bemutatott javasolt művelet nem működik, hajtsa végre a következő lépéseket:** <br> <ul><li>Állítsa az árnyékmásolat tárolóhelyét korlátlanra a védelmi csoport elemeinél, majd futtassa a konzisztencia-ellenőrzés parancsot.<br></li> VAGY <li>Próbálja meg törölni a meglévő védelmi csoportot, és hozzon létre több új csoportot. Minden új védelmi csoportnak külön elemmel kell rendelkeznie.</li></ul> |
| Backup | Ha csak a rendszerállapotról készít biztonsági másolatot, ellenőrizze, hogy van-e elegendő szabad hely a védett számítógépen a rendszerállapot biztonsági másolatának tárolásához. | <ol><li>Ellenőrizze, hogy a Windows Server biztonsági másolat telepítve van-e a védett gépen.</li><li>Ellenőrizze, hogy van-e elegendő hely a védett számítógépen a rendszerállapothoz. Ennek a legegyszerűbb módja a védett számítógép megnyitása, a Windows Server biztonsági másolat megnyitása, a kijelölések átadása, majd a BMR kiválasztása. A felhasználói felület ekkor megadja, hogy mekkora lemezterületre van szükség. Nyissa meg a **WSB**  >  **helyi biztonsági mentési**  >  **ütemtervét**  >  ,**válassza a biztonsági mentési konfiguráció**  >  **teljes kiszolgáló** elemet (a méret megjelenik). Az ellenőrzéshez használja ezt a méretet.</li></ol>
| Backup | A BMR biztonsági mentése sikertelen | Ha a BMR mérete nagyméretű, helyezzen át néhány alkalmazást az operációsrendszer-meghajtóra, és próbálkozzon újra. |
| Backup | A VMware virtuális gépek új Microsoft Azure Backup-kiszolgálón történő ismételt védetté tétele nem érhető el a hozzáadáshoz. | A VMware-tulajdonságokat a Microsoft Azure Backup kiszolgáló egy régi, kivont példánya irányítja. A probléma megoldása:<br><ol><li>A VCenter (SC-VMM egyenértékű) lapon lépjen az **Összefoglalás** lapra, majd az **Egyéni attribútumok** elemre.</li>  <li>Törölje a régi Microsoft Azure Backup-kiszolgáló nevét a **DPMServer** értékből.</li>  <li>Térjen vissza az új Microsoft Azure Backup-kiszolgálóra, és módosítsa a PG-t.  A **frissítés** gomb kiválasztását követően a virtuális gép a védelemhez való hozzáadáshoz elérhető jelölőnégyzettel jelenik meg.</li></ol> |
| Backup | Hiba történt a fájlok/megosztott mappák elérésekor | Próbálja meg módosítani a vírusvédelmi beállításokat a cikkben javasolt módon, [futtassa a víruskereső szoftvert a DPM-kiszolgálón](/system-center/dpm/run-antivirus-server).|

## <a name="change-passphrase"></a>Hozzáférési kód módosítása

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Hozzáférési kód módosítása |A megadott biztonsági PIN-kód helytelen. A művelet végrehajtásához adja meg a helyes biztonsági PIN-kódot. |**Ok:**<br/> Ez a hiba akkor fordul elő, ha érvénytelen vagy lejárt biztonsági PIN-kódot ad meg a kritikus művelet végrehajtása közben (például egy jelszó módosítása). <br/>**Javasolt művelet:**<br/> A művelet végrehajtásához érvényes biztonsági PIN-kódot kell megadnia. A PIN-kód beszerzéséhez jelentkezzen be a Azure Portalba, és nyissa meg a Recovery Services-tárolót. Ezután lépjen a **Beállítások**  >  **Tulajdonságok**  >  **biztonsági PIN-kód létrehozásához**. Ezt a PIN-kódot használhatja a jelszó megváltoztatásához. |
| Hozzáférési kód módosítása |A művelet sikertelen volt. AZONOSÍTÓ: 120002 |**Ok:**<br/>Ez a hiba akkor fordul elő, ha a biztonsági beállítások engedélyezve vannak, vagy ha nem támogatott verziót használ, amikor megpróbál módosítani egy jelszót.<br/>**Javasolt művelet:**<br/> A jelszó módosításához először frissítenie kell a Backup ügynököt a 2.0.9052 minimális verziójára. Emellett frissítenie kell Azure Backup Server az 1. frissítés minimumára, majd érvényes biztonsági PIN-kódot kell megadnia. A PIN-kód beszerzéséhez jelentkezzen be a Azure Portalba, és nyissa meg a Recovery Services-tárolót. Ezután lépjen a **Beállítások**  >  **Tulajdonságok**  >  **biztonsági PIN-kód létrehozásához**. Ezt a PIN-kódot használhatja a jelszó megváltoztatásához. |

## <a name="configure-email-notifications"></a>E-mail-értesítések konfigurálása

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| E-mail-értesítések beállítása munkahelyi vagy iskolai fiók használatával |Hiba azonosítója: 2013| **Ok:**<br> Munkahelyi vagy iskolai fiók használatának megkísérlése <br>**Javasolt művelet:**<ol><li> Az első dolog, hogy a "névtelen továbbító engedélyezése egy fogadási összekötőn" a DPM-kiszolgálóhoz az Exchange-kiszolgálón legyen beállítva. További információ a konfigurálásáról: [Névtelen továbbító engedélyezése fogadási összekötőn](/exchange/mail-flow/connectors/allow-anonymous-relay).</li> <li> Ha nem használ belső SMTP-továbbítót, és az Office 365-kiszolgáló használatával kell beállítania, beállíthatja, hogy az IIS továbbító legyen. Konfigurálja úgy a DPM-kiszolgálót, hogy [az IIS használatával továbbítsa az SMTP-t az Office 365](/exchange/mail-flow/test-smtp-with-telnet)-be.<br><br>  Ügyeljen arra, hogy a felhasználói \@ domain.com formátumot használja, és *ne* megadásakor<br><br><li>A DPM pont a helyi kiszolgáló nevét használja SMTP-kiszolgálóként, 587-as porton. Ezután mutasson arra a felhasználói e-mailre, amelyről az e-mailek származnak.<li> A DPM SMTP-beállítás lapjának felhasználónevének és jelszavának a DPM tartományában lévő tartományi fiókhoz kell tartoznia. </li><br> Ha megváltoztatja az SMTP-kiszolgáló címe beállítást, végezze el a módosítást az új beállításokra, majd kattintson a beállítások mezőre, majd nyissa meg újra, hogy az tükrözze az új értéket.  Előfordulhat, hogy a módosítás és a tesztelés nem mindig okozza az új beállítások érvénybe léptetését, így a tesztelés így a legjobb megoldás.<br><br>A folyamat során bármikor törölheti ezeket a beállításokat a DPM-konzol bezárásával és a következő beállításkulcsok szerkesztésével: **HKLM\SOFTWARE\Microsoft\Microsoft adatvédelem Manager\Notification\ <br/> SMTPPassword és SMTPUserName kulcsok törlése**. Ha újra elindítják, a felhasználói felülethez is hozzáadhatók.

## <a name="common-issues"></a>Gyakori problémák

Ez a szakasz a Azure Backup Server használata során előforduló gyakori hibákat ismerteti.

### <a name="cbpsourcesnapshotfailedreplicamissingorinvalid"></a>CBPSourceSnapshotFailedReplicaMissingOrInvalid

Hibaüzenet | Javasolt művelet |
-- | --
A biztonsági mentés sikertelen, mert a lemez biztonsági másolati replikája érvénytelen vagy hiányzik. | A probléma megoldásához ellenőrizze az alábbi lépéseket, majd próbálja megismételni a műveletet: <br/> 1. lemezes helyreállítási pont létrehozása<br/> 2. konzisztencia-ellenőrzés futtatása az adatforráson <br/> 3. állítsa le az adatforrás védelmét, majd konfigurálja újra az adatforrás védelmét.

### <a name="cbpsourcesnapshotfailedreplicametadatainvalid"></a>CBPSourceSnapshotFailedReplicaMetadataInvalid

Hibaüzenet | Javasolt művelet |
-- | --
A forrás kötetének pillanatképe sikertelen volt, mert a replikán található metaadatok érvénytelenek. | Hozzon létre egy lemez-helyreállítási pontot az adatforrásból, és próbálkozzon újra az online biztonsági mentéssel

### <a name="cbpsourcesnapshotfailedreplicainconsistent"></a>CBPSourceSnapshotFailedReplicaInconsistent

Hibaüzenet | Javasolt művelet |
-- | --
A forrásoldali kötet pillanatképe nem sikerült, mert inkonzisztens adatforrás-replika. | Konzisztencia-ellenőrzés futtatása az adatforráson, majd próbálkozzon újra

### <a name="cbpsourcesnapshotfailedreplicacloningissue"></a>CBPSourceSnapshotFailedReplicaCloningIssue

Hibaüzenet | Javasolt művelet |
-- | --
A biztonsági mentés nem sikerült, mert a lemez biztonsági másolati replikáját nem lehetett klónozni.| Győződjön meg arról, hogy az összes korábbi lemezes biztonsági másolat másodpéldány-fájlja (. vhdx) le van választva, és nincs folyamatban lemezről lemezre történő biztonsági mentés az online biztonsági mentések során.
