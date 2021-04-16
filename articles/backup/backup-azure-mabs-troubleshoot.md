---
title: Az Azure Backup Server hibaelhárítása
description: Az alkalmazás számítási feladatainak telepítésével, Azure Backup Server biztonsági mentésével és visszaállításával kapcsolatos hibák elhárítása.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 644946ca90c2893ba3d87f9d2ff8bfd8325f4715
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514750"
---
# <a name="troubleshoot-azure-backup-server"></a>Az Azure Backup Server hibaelhárítása

Az alábbi táblázatokban található információk segítségével háríthatja el a hibákat, amelyek a hibakeresés során Azure Backup Server.

## <a name="basic-troubleshooting"></a>Alapszintű hibaelhárítás

Javasoljuk, hogy a következő ellenőrzést végezze el, mielőtt elkezdené a Microsoft Azure Backup (MABS) hibaelhárítását:

- [Győződjön Microsoft Azure, hogy a Recovery Services- (MARS-) ügynök naprakész](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Ellenőrizze, hogy van-e hálózati kapcsolat a MARS-ügynök és az Azure között](./backup-azure-mars-troubleshoot.md#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Győződjön meg arról, hogy a Microsoft Azure Recovery Services fut (a Szolgáltatás konzolon). Szükség esetén indítsa újra a műveletet, majd próbálja újra a műveletet
- [Győződjön meg arról, hogy 5-10% szabad hellyel rendelkezik az ideiglenes mappa helyén](./backup-azure-file-folder-backup-faq.yml#what-s-the-minimum-size-requirement-for-the-cache-folder-)
- Ha a regisztráció sikertelen, győződjön meg arról, hogy a kiszolgáló, amelyre a Azure Backup Server még nincs regisztrálva egy másik tárolóban
- Ha a leküldéses telepítés sikertelen, ellenőrizze, hogy a DPM-ügynök már jelen van-e a rendszeren. Ha igen, távolítsa el az ügynököt, és próbálkozzon újra a telepítéssel
- [Győződjön meg arról, hogy nem zavarja másik folyamat vagy víruskereső szoftver az Azure Backup működését](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)<br>
- Győződjön meg arról, hogy az SQL Agent szolgáltatás fut, és automatikusra van állítva a MABS-kiszolgálón<br>

## <a name="configure-antivirus-for-mabs-server"></a>Víruskereső konfigurálása MABS-kiszolgálóhoz

A MABS kompatibilis a legnépszerűbb vírusvédelmi szoftverekkel. Az ütközések elkerülése érdekében az alábbi lépéseket javasoljuk:

1. **Valós idejű figyelés letiltása** – tiltsa le a valós idejű monitorozást a víruskereső szoftverben a következőkre:
    - `C:\Program Files<MABS Installation path>\XSD` Mappa
    - `C:\Program Files<MABS Installation path>\Temp` Mappa
    - Kötet meghajtóbetűjele modern biztonsági másolati tárhely meghajtóbetűjele
    - Replika- és átviteli naplók: Ehhez **** tiltsa le a mappában találhatódpmra.exenaplók valós idejű monitorozását. `Program Files\Microsoft Azure Backup Server\DPM\DPM\bin` A valós idejű figyelés teljesítménye csökken, mivel a víruskereső szoftver minden alkalommal megvizsgálja a replikákat, amikor a MABS szinkronizál a védett kiszolgálóval, és megvizsgálja az összes érintett fájlt minden alkalommal, amikor a MABS módosításokat alkalmaz a replikákon.
    - Felügyeleti konzol: A teljesítményre gyakorolt hatás elkerülése érdekében  tiltsa le a felügyeleticsc.exemonitorozását. A **csc.exe** folyamat a C-fordító, a valós idejű figyelés pedig csökkentheti a teljesítményt, mivel a víruskereső megvizsgálja azokat a fájlokat,csc.exea folyamat \# XML-üzeneteket hoz létre.  **CSC.exe** a következő elérési utakon található:
        - `\Windows\Microsoft.net\Framework\v2.0.50727\csc.exe`
        - `\Windows\Microsoft.NET\Framework\v4.0.30319\csc.exe`
    - A MABS-kiszolgálón telepített MARS-ügynökhöz ajánlott kizárni a következő fájlokat és helyeket:
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\bin\cbengine.exe` folyamatként
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\folder`
        - Az scratch hely (ha nem a szokásos helyet használja)
2. **Valós idejű figyelés** letiltása a védett kiszolgálón: Tiltsa le adpmra.exemappájában található alkalmazás valós idejű figyelése a védett **** `C:\Program Files\Microsoft Data Protection Manager\DPM\bin` kiszolgálón.
3. Vírusirtó szoftver konfigurálása a védett kiszolgálókon és a **MABS-kiszolgálón** található fertőzött fájlok törlésére: A replikák és helyreállítási pontok adatsérülésének elkerülése érdekében konfigurálja a víruskereső szoftvert a fertőzött fájlok automatikus tisztítása vagy quarantolása helyett a fertőzött fájlok törlésére. Az automatikus tisztítás és a quarantining okozhatja, hogy a víruskereső szoftver módosítja a fájlokat, és olyan módosításokat eszközli, amelyekre a MABS nem képes.

Manuális szinkronizálást kell futtatni konzisztenciával. Ellenőrizze a feladatot minden alkalommal, amikor a víruskereső szoftver töröl egy fájlt a replikáról, annak ellenére, hogy a replika inkonzisztensként van megjelölve.

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
| Regisztráció tárolóba | A tároló megadott hitelesítő adatai érvénytelenek. A fájl sérült, vagy nem rendelkezik a helyreállítási szolgáltatáshoz társított legújabb hitelesítő adatokkal. | Javasolt művelet: <br> <ul><li> Töltse le a legújabb hitelesítőadat-fájlt a tárolóból, és próbálkozzon újra. <br>(OR)</li> <li> Ha az előző művelet nem működött, próbálja meg letölteni a hitelesítő adatokat egy másik helyi könyvtárba, vagy hozzon létre egy új tárolót. <br>(OR)</li> <li> Próbálja meg frissíteni a dátum- és időbeállításokat a [cikkben leírtak szerint.](./backup-azure-mars-troubleshoot.md#invalid-vault-credentials-provided) <br>(OR)</li> <li> Ellenőrizze, hogy a c:\windows\temp mappában több mint 65000 fájl található-e. Helyezze át az elavult fájlokat egy másik helyre, vagy törölje a Temp mappa elemeit. <br>(OR)</li> <li> Ellenőrizze a tanúsítványok állapotát. <br> a. Nyissa **meg a Számítógép-tanúsítványok kezelése (a** Vezérlőpult). <br> b. Bontsa **ki a Személyes** csomópontot és annak gyermekcsomópontját Tanúsítványok **csomópontot.**<br> c.  Távolítsa el a **Windows Azure Tools tanúsítványt.** <br> d. Próbálja meg újra a regisztrációt a Azure Backup ügyfélen. <br> (OR) </li> <li> Ellenőrizze, hogy van-e csoportházirend. </li></ul> |

## <a name="replica-is-inconsistent"></a>A replika inkonzisztens

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Backup | A replika inkonzisztens | Ellenőrizze, hogy a Védelmi csoport varázsló automatikus konzisztencia-ellenőrzése be van-e kapcsolva. A replikációs lehetőségekkel és a konzisztencia-ellenőrzésekkel kapcsolatos további információkért tekintse meg [ezt a cikket.](/system-center/dpm/create-dpm-protection-groups)<br> <ol><li> Rendszerállapot/BMR biztonsági mentés esetén ellenőrizze, hogy a Windows Server biztonsági másolat telepítve van-e a védett kiszolgálón.</li><li> Ellenőrizze a DPM/Microsoft Azure Backup-kiszolgálón található DPM-tárolókészletben a területekkel kapcsolatos problémákat, és szükség szerint foglalja le a tárolót.</li><li> Ellenőrizze a védett Kötet árnyékmásolata szolgáltatás állapotának állapotát. Ha letiltott állapotban van, állítsa be manuális indításra. Indítsa el a szolgáltatást a kiszolgálón. Ezután vissza a DPM/Microsoft Azure Backup-kiszolgáló konzoljára, és indítsa el a szinkronizálást a konzisztencia-ellenőrzés feladatával.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Az online helyreállítási pont létrehozása nem sikerült

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Backup | Az online helyreállítási pont létrehozása nem sikerült | **Hibaüzenet:** A Windows Azure Backup Agent nem tudott pillanatképet készíteni a kiválasztott kötetről. <br> **Megkerülő** megoldás: Próbálja meg növelni a replika és a helyreállítási pont kötetének lemezterületét.<br> <br> **Hibaüzenet:** A Windows Azure Backup Agent nem tud csatlakozni az OBEngine szolgáltatáshoz <br> **Megkerülő** megoldás: ellenőrizze, hogy az OBEngine létezik-e a számítógépen futó szolgáltatások listájában. Ha az OBEngine szolgáltatás nem fut, a "net start OBEngine" paranccsal indítsa el az OBEngine szolgáltatást. <br> <br> **Hibaüzenet:** A kiszolgáló titkosítási jelszója nincs beállítva. Konfiguráljon egy titkosítási jelszót. <br> **Megkerülő** megoldás: Próbáljon meg konfigurálni egy titkosítási jelszót. Ha sikertelen, kövesse az alábbi lépéseket: <br> <ol><li>Ellenőrizze, hogy létezik-e az új hely. Ez az a hely, amely szerepel a **beállításjegyzékbenHKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config,** **és a ScratchLocation** névvel kell léteznie.</li><li> Ha létezik az új hely, próbáljon meg újra regisztrálni a régi jelszóval. *Amikor konfigurál egy titkosítási jelszót, mentse biztonságos helyre.*</li><ol>|

## <a name="the-original-and-external-dpm-servers-must-be-registered-to-the-same-vault"></a>Az eredeti és a külső DPM-kiszolgálókat ugyanabban a tárolóban kell regisztrálni

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Visszaállítás | **Hibakód:** CBPServerRegisteredVaultDontMatchWithCurrent/Vault Credentials Error: 100110 <br/> <br/>**Hibaüzenet:** Az eredeti és külső DPM-kiszolgálókat ugyanannak a tárolónak kell regisztrálnia | **Ok:** Ez a probléma akkor fordul elő, ha az eredeti kiszolgálóról próbál fájlokat visszaállítani az eredeti kiszolgálóról a Külső DPM helyreállítási beállítással, és ha a helyreállított kiszolgáló és az eredeti kiszolgáló, amelyről az adatok biztonsági másolata található, nincs társítva ugyanazhoz a Recovery Services-tárolóhoz.<br/> <br/>**Áthidaló megoldás** A probléma megoldásához győződjön meg arról, hogy az eredeti és a másodlagos kiszolgáló is ugyanabban a tárolóban van regisztrálva.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>A VMware virtuális gép online helyreállításipont-létrehozási feladatának meghiúsulása

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Backup | A VMware virtuális gépek online helyreállításipont-létrehozási feladatok meghiúsulnak. A DPM hibát észlelt a VMware-ben a ChangeTracking-információk lekérte közben. ErrorCode – FileFaultFault (ID 33621) |  <ol><li> Állítsa alaphelyzetbe a CTK-t a VMware-ben az érintett virtuális gépeken.</li> <li>Ellenőrizze, hogy nincs-e független lemez a VMware-ben.</li> <li>Állítsa le az érintett virtuális gépek védelmét, és a Frissítés gombbal állítsa le **az ismételt védelmet.** </li><li>Futtatassa a cc-t az érintett virtuális gépeken.</li></ol>|

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>Az ügynökművelet a kiszolgálón a DPM-ügynökkoordinátor szolgáltatással való kommunikáció hibája miatt meghiúsult

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Ügynök(ök) leküldve védett kiszolgálókra | Az ügynökművelet nem sikerült, mert kommunikációs hiba lépett fel a DPM-ügynökkoordinátor szolgáltatásával \<ServerName> a következőn: . | **Ha a termékben javasolt művelet nem működik, hajtsa végre a következő lépéseket:** <ul><li> Ha nem megbízható tartományból csatlakoztat számítógépet, kövesse az [alábbi lépéseket.](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains) <br> (OR) </li><li> Ha megbízható tartományból csatlakoztat számítógépet, a hibaelhárítást a blogban ismertetett [lépésekkel háríthatja el.](https://techcommunity.microsoft.com/t5/system-center-blog/data-protection-manager-agent-network-troubleshooting/ba-p/344726) <br>(OR)</li><li> Hibaelhárítási lépésként próbálja meg letiltani a víruskeresőt. Ha ez megoldja a problémát, módosítsa a víruskereső beállításait a [cikkben javasoltak szerint.](/system-center/dpm/run-antivirus-server)</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>A telepítő nem tudta frissíteni a beállításjegyzékbeli metaadatokat

| Művelet | A hiba részletei | Áthidaló megoldás |
|-----------|---------------|------------|
|Telepítés | A telepítő nem tudta frissíteni a beállításjegyzék metaadatait. Ez a frissítési hiba a tárterület-használat túlhasználódását okozhatja. Ennek elkerülése érdekében frissítse az ReFS Trimming beállításjegyzék-bejegyzést. | Állítsa be a **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim beállításkulcsot.** Állítsa a Dword értékét 1-re. |
|Telepítés | A telepítő nem tudta frissíteni a beállításjegyzék metaadatait. Ez a frissítési hiba a tárterület-használat túlhasználódását okozhatja. Ennek elkerülése érdekében frissítse a Kötet beépülő modulOptimizálás beállításjegyzék-bejegyzést. | Hozza létre a **SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds** beállításkulcsot egy üres sztringértékkel. |

## <a name="registration-and-agent-related-issues"></a>Regisztrációval és ügynökkel kapcsolatos problémák

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Ügynök(ök) leküldve védett kiszolgálókra | A kiszolgálóhoz megadott hitelesítő adatok érvénytelenek. | Ha a termékben látható ajánlott művelet nem **működik, kövesse az alábbi lépéseket:** <br> Próbálja meg manuálisan telepíteni a védelmi ügynököt az éles kiszolgálóra a [cikkben megadottak szerint.](/system-center/dpm/deploy-dpm-protection-agent)|
| Azure Backup Agent nem tudott csatlakozni a Azure Backup szolgáltatáshoz (azonosító: 100050) | A Azure Backup Agent nem tudott csatlakozni a Azure Backup szolgáltatáshoz. | Ha a termékben látható ajánlott művelet nem **működik, kövesse az alábbi lépéseket:** <br>1. Futtassa a következő parancsot egy rendszergazda jogú parancssorból: **psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe.** Ez megnyitja a Internet Explorer ablakot. <br/> 2. Az Eszközök  >  **Internetbeállítások Kapcsolatok**  >  **LAN-beállításai**  >  **között.** <br/> 3. Módosítsa a beállításokat proxykiszolgáló használatára. Ezután adja meg a proxykiszolgáló adatait.<br/> 4. Ha a gépe korlátozott internet-hozzáféréssel rendelkezik, győződjön [](install-mars-agent.md#verify-internet-access) meg arról, hogy a gép vagy a proxy tűzfalbeállításai engedélyezik ezeket az URL-címeket és [IP-címeket.](install-mars-agent.md#verify-internet-access)|
| Azure Backup ügynök telepítése meghiúsult | A Microsoft Azure Recovery Services telepítése sikertelen volt. A helyreállítási időkorrekta által a rendszeren végrehajtott Microsoft Azure Recovery Services-telepítés vissza lett gördülve. (Azonosító: 4024) | Telepítse manuálisan az Azure Agentet.

## <a name="configuring-protection-group"></a>Védelmi csoport konfigurálása

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Védelmi csoportok konfigurálása | A DPM nem tudta enumerálni az alkalmazás-összetevőt a védett számítógépen (védett számítógépnév). | A **védelmi** csoport konfigurálás felhasználói felületének képernyőjén válassza a Frissítés lehetőséget a megfelelő adatforrás/összetevő szintjén. |
| Védelmi csoportok konfigurálása | Nem konfigurálható védelem | Ha a védett kiszolgáló egy SQL-kiszolgáló, ellenőrizze, hogy a rendszergazdai szerepkör engedélyei meg vannak-e biztosítanak-e a rendszerfiókhoz (NTAuthority\System) a védett számítógépen a cikkben leírtak [szerint.](/system-center/dpm/back-up-sql-server)
| Védelmi csoportok konfigurálása | Nincs elegendő szabad hely a tárolókészletben ehhez a védelmi csoporthoz. | A tárolókészlethez hozzáadott lemezek nem tartalmazhatnak [partíciót.](/system-center/dpm/create-dpm-protection-groups) Törölje a lemezeken lévő összes meglévő kötetet. Ezután adja hozzá őket a tárolókészlethez.|
| Szabályzat módosítása |A biztonsági mentési szabályzatot nem lehetett módosítani. Hiba: Az aktuális művelet egy belső szolgáltatáshiba miatt meghiúsult [0x29834]. Próbálja meg újra a műveletet egy idő eltelte után. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához. | **Ok:**<br/>Ez a hiba három feltétel teljesülésében fordul elő: ha a biztonsági beállítások engedélyezve vannak, amikor a megőrzési tartományt a korábban megadott minimális értékek alá próbálja csökkenteni, és ha nem támogatott verziót futtat. (A nem támogatott verziók alacsonyabbak, mint a Microsoft Azure Backup Server 2.0.9052-es és Azure Backup Server 1-es frissítése.) <br/>**Javasolt művelet:**<br/> A szabályzattal kapcsolatos frissítésekhez állítsa a megőrzési megőrzési időszakot a megadott minimális megőrzési időtartam fölé. (A minimális megőrzési időszak naponta hét nap, hetente négy hét, havonta három hét, éves pedig egy év.) <br><br>Másik lehetőségként frissítheti a biztonsági mentési ügynököt, és Azure Backup Server a biztonsági frissítések kihasználja. |

## <a name="backup"></a>Backup

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Backup | Váratlan hiba történt a feladat futása közben. Az eszköz nem áll készen. | **Ha a termékben javasolt művelet nem működik, kövesse az alábbi lépéseket:** <br> <ul><li>Állítsa korlátlanra az árnyékmásolat tárterületét a védelmi csoport elemein, majd futtassa a konzisztencia-ellenőrzést.<br></li> (OR) <li>Próbálja meg törölni a meglévő védelmi csoportot, és hozzon létre több új csoportot. Minden új védelmi csoportban külön elemnek kell lennie.</li></ul> |
| Backup | Ha csak a rendszerállapotról készít biztonsági másolatot, ellenőrizze, hogy a védett számítógépen elegendő szabad terület van-e a rendszerállapot biztonsági másolatának tárolására. | <ol><li>Ellenőrizze, Windows Server biztonsági másolat telepítve van-e a védett számítógépen.</li><li>Ellenőrizze, hogy a védett számítógépen elegendő hely van-e a rendszerállapot számára. Ezt úgy ellenőrizheti a legegyszerűbben, ha megnyitja a védett számítógépet, megnyitja a Windows Server biztonsági másolat, végigkattint a kiválasztások között, majd kiválasztja a BMR lehetőséget. A felhasználói felület ezután közli, hogy mennyi lemezterületre van szükség. Nyissa **meg a WSB**  >  **helyi biztonsági**  >  **mentésének ütemezését**  >  **Válassza a Biztonsági mentés konfigurációja** Teljes kiszolgáló  >   lehetőséget (a méret jelenik meg). Ezt a méretet használja az ellenőrzéshez.</li></ol>
| Backup | BMR biztonságimentés-hibája | Ha a BMR mérete nagy, helyezzen át néhány alkalmazásfájlt az operációsrendszer-meghajtóra, és próbálja újra. |
| Backup | A VMware virtuális gépek ismételt védelme egy új kiszolgálón Microsoft Azure Backup nem érhető el hozzáadásra. | A VMware-tulajdonságok a kiszolgáló egy régi, visszavont példányára Microsoft Azure Backup mutatnak. A probléma megoldása:<br><ol><li>A VCenterben (SC-VMM egyenértékű) válassza az Összegzés lapot, majd az Egyéni **attribútumok lapot.** </li>  <li>Törölje a régi Microsoft Azure Backup a **DPMServer értékből.**</li>  <li>Vissza az új Microsoft Azure Backup kiszolgálóra, és módosítsa a PG-t.  A Frissítés gomb **kiválasztása** után a virtuális gép megjelenik a védelemhez hozzáadható jelölőnégyzetekkel.</li></ol> |
| Backup | Hiba a fájlok/megosztott mappák elérésekor | Próbálja meg módosítani a víruskereső beállításait a Run antivirus software on the DPM server (Víruskereső szoftver futtatása a [DPM-kiszolgálón) cikkben javasoltak szerint.](/system-center/dpm/run-antivirus-server)|

## <a name="change-passphrase"></a>Jelszó módosítása

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Jelszó módosítása |A megadott biztonsági PIN-kód helytelen. Adja meg a megfelelő biztonsági PIN-kódot a művelet végrehajtásához. |**Ok:**<br/> Ez a hiba akkor fordul elő, ha érvénytelen vagy lejárt biztonsági PIN-kódot ad meg egy kritikus művelet (például jelszó módosítása) végrehajtása közben. <br/>**Javasolt művelet:**<br/> A művelet végrehajtásához meg kell adnia egy érvényes biztonsági PIN-kódot. A PIN-kód leállításhoz jelentkezzen be a Azure Portal, és a Recovery Services-tárolóhoz. Ezután válassza a **Beállítások tulajdonságai**  >    >  **Biztonsági PIN-kód létrehozása lehetőséget.** A PIN-kód használatával módosíthatja a jelszót. |
| Jelszó módosítása |A művelet sikertelen volt. Azonosító: 120002 |**Ok:**<br/>Ez a hiba akkor fordul elő, ha a biztonsági beállítások engedélyezve vannak, vagy ha nem támogatott verzió használata esetén próbálja módosítani a jelszót.<br/>**Javasolt művelet:**<br/> A jelszó módosításhoz először frissítenie kell a biztonsági mentési ügynököt a minimális verzióra( 2.0.9052). Frissítenie kell a Azure Backup Server az 1. frissítésre, majd érvényes biztonsági PIN-kódot kell megadnia. A PIN-kód leállításhoz jelentkezzen be a Azure Portal, és a Recovery Services-tárolóhoz. Ezután válassza a **Beállítások tulajdonságai**  >    >  **Biztonsági PIN-kód létrehozása lehetőséget.** A PIN-kód használatával módosíthatja a jelszót. |

## <a name="configure-email-notifications"></a>E-mail-értesítések konfigurálása

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| E-mail-értesítések beállítása munkahelyi vagy iskolai fiókkal |Hibaazonosító: 2013| **Ok:**<br> Munkahelyi vagy iskolai fiókot próbál használni <br>**Javasolt művelet:**<ol><li> Az első dolog, amit meg kell győződni arról, hogy a DPM-kiszolgáló számára a "Névtelen továbbítás engedélyezése fogadási összekötőn" beállítás az Exchange-re van beállítva. További információ ennek konfigurálásról: Allow Anonymous Relay on a Receive Connector (Névtelen továbbítás engedélyezése [fogadási összekötőn).](/exchange/mail-flow/connectors/allow-anonymous-relay)</li> <li> Ha nem tud belső SMTP-továbbítást használni, és az Office 365-kiszolgálóval kell beállítania, beállíthatja, hogy az IIS továbbító legyen. Konfigurálja a DPM-kiszolgálót az [SMTP Office 365-be való](/exchange/mail-flow/test-smtp-with-telnet)továbbítására az IIS használatával.<br><br>  Ne a tartomány\felhasználó, domain.com \@ a  felhasználó formátumát használja.<br><br><li>A DPM arra mutat, hogy a helyi kiszolgáló nevét SMTP-kiszolgálóként használja, az 587-es portot. Ezután küldje el a felhasználó e-mail-címére, amelyről az e-maileknek kell kapniuk.<li> A DPM SMTP-beállítási oldalán található felhasználónévnek és jelszónak a DPM-et küldő tartomány tartományfiókjára kell beállítania. </li><br> Az SMTP-kiszolgáló címének módosításakor módosítsa az új beállításokat, zárja be a beállításokat, majd nyissa meg újra, hogy biztosan az új értéket tükrözze.  Előfordulhat, hogy a módosítás és a tesztelés nem mindig váltja ki az új beállításokat, ezért ajánlott így tesztelni.<br><br>A folyamat során bármikor törölheti ezeket a beállításokat a DPM-konzol bezárásával és a következő beállításkulcsok szerkesztésével: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> Delete SMTPPassword and SMTPUserName keys**. Újra hozzáadhatja őket a felhasználói felülethez, amikor újra elindítja.

## <a name="common-issues"></a>Gyakori problémák

Ez a szakasz azokat a gyakori hibákat tartalmazza, amelyek a hiba Azure Backup Server.

### <a name="cbpsourcesnapshotfailedreplicamissingorinvalid"></a>CBPSourceSnapshotFailedReplicaMissingOrInvalid

Hibaüzenet | Javasolt művelet |
-- | --
A biztonsági mentés sikertelen, mert a lemez biztonsági másolati replikája érvénytelen vagy hiányzik. | A probléma megoldásához ellenőrizze az alábbi lépéseket, majd próbálja újra a műveletet: <br/> 1. Lemez-helyreállítási pont létrehozása<br/> 2. Konzisztencia-ellenőrzés futtatása az adatforráson <br/> 3. Állítsa le az adatforrás védelmét, majd konfigurálja újra a védelmet ehhez az adatforráshoz

### <a name="cbpsourcesnapshotfailedreplicametadatainvalid"></a>CBPSourceSnapshotFailedReplicaMetadataInvalid

Hibaüzenet | Javasolt művelet |
-- | --
A forráskötet pillanatképe nem sikerült, mert a replikán található metaadatok érvénytelenek. | Hozzon létre egy lemez-helyreállítási pontot erről az adatforrásról, és próbálja meg újra az online biztonsági mentést

### <a name="cbpsourcesnapshotfailedreplicainconsistent"></a>CBPSourceSnapshotFailedReplicaInconsistent

Hibaüzenet | Javasolt művelet |
-- | --
A forráskötet pillanatképe inkonzisztens adatforrás-replika miatt nem sikerült. | Futtason konzisztencia-ellenőrzést ezen az adatforráson, és próbálkozzon újra

### <a name="cbpsourcesnapshotfailedreplicacloningissue"></a>CBPSourceSnapshotFailedReplicaCloningIssue

Hibaüzenet | Javasolt művelet |
-- | --
A biztonsági mentés nem sikerült, mert a lemez biztonsági másolati replikáját nem lehetett klónozni.| Győződjön meg arról, hogy a lemezes biztonsági másolat összes korábbi replikafájlja (.vhdx) le van különülve, és nincs folyamatban lemezről lemezre való biztonsági mentés az online biztonsági mentések során
