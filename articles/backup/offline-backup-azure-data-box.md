---
title: Offline biztonsági mentés Azure Data Box használatával
description: Ebből a témakörből megtudhatja, hogyan használhatja a Azure Data Box a nagyméretű kezdeti biztonsági mentési adatoknak a MARS-ügynökből egy Recovery Services-tárolóba való összevetéséhez
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: e789b6c9f4ff2e8cd168e6b5c138d423911d4743
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96752583"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Offline biztonsági mentés Azure Backup Azure Data Box használatával

A [Azure Data Box](../databox/data-box-overview.md) használatával a nagy kezdeti Microsoft Azure Recovery Services (MARS) biztonsági mentések a Recovery Services-tárolóra kapcsolat nélküli üzemmódban (hálózat nélkül) is kiírhatók. Ezzel a folyamattal időt és hálózati sávszélességet takaríthat meg, amely egyébként nagy mennyiségű biztonsági mentési adat online állapotba helyezésével nagy késleltetésű hálózaton keresztül történik. Ez a fejlesztés jelenleg előzetes verzióban érhető el. A Azure Data Boxon alapuló offline biztonsági mentés két különböző előnyt biztosít [Az Azure import/export szolgáltatás alapján az offline biztonsági mentéshez](./backup-azure-backup-import-export.md):

- Nincs szükség a saját Azure-kompatibilis lemezek és összekötők beszerzésére. Azure Data Box hajók a kiválasztott [Data Box SKU](https://azure.microsoft.com/services/databox/data/)-hoz társított lemezeket.
- A Azure Backup (MARS-ügynök) közvetlenül a Azure Data Box támogatott SKU-jéről írhatja fel a biztonsági mentési adatmennyiséget. Ezzel a képességgel nem kell kiépíteni egy átmeneti helyet a kezdeti biztonsági mentési adatai számára. Emellett a segédprogramok nem szükségesek az adatlemezek formázásához és másolásához.

## <a name="azure-data-box-with-the-mars-agent"></a>Azure Data Box a MARS-ügynökkel

Ez a cikk azt ismerteti, hogyan használható a Azure Data Box a nagyméretű kezdeti biztonsági mentési adatoknak a MARS-ügynökből egy Recovery Services-tárolóba való összevetéséhez.

## <a name="supported-platforms"></a>Támogatott platformok

A MARS-ügynökről Azure Data Box használatával történő adatfeldolgozási folyamat a következő Windows-SKU-kal támogatott.

| **OS**                                 | **Termékváltozat**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **Munkaállomás**                        |                                                              |
| Windows 10, 64 bites                     | Enterprise, Pro, Home                                       |
| Windows 8.1, 64 bites                    | Enterprise, Pro                                             |
| Windows 8, 64 bites                      | Enterprise, Pro                                             |
| Windows 7, 64 bites                      | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| **Kiszolgáló**                             |                                                              |
| Windows Server 2019, 64 bites            | Standard, Datacenter, Essentials                            |
| Windows Server 2016, 64 bites            | Standard, Datacenter, Essentials                            |
| Windows Server 2012 R2, 64 bites         | Standard, Datacenter, Foundation                            |
| Windows Server 2012, 64 bites            | Datacenter, Foundation, Standard                            |
| Windows Storage Server 2016, 64 bites    | Standard, Workgroup                                         |
| Windows Storage Server 2012 R2, 64 bites | Standard, Workgroup, Essential                              |
| Windows Storage Server 2012, 64 bites    | Standard, Workgroup                                         |
| Windows Server 2008 R2 SP1, 64 bites     | Standard, Enterprise, Datacenter, Foundation                |
| Windows Server 2008 SP2 64 bites        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Biztonsági másolatok méretének és támogatott Data Box SKU-i

| Biztonsági mentési adatméret (a MARS tömörítése után) * kiszolgálónként | Támogatott Azure Data Box SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <= 7,2 TB                                                    | [Azure Data Box lemez](../databox/data-box-disk-overview.md) |
| >7,2 TB és <= 80 TB * *                                      | [Azure Data Box (100 TB)](../databox/data-box-overview.md) |

* A tipikus tömörítési arányok 10% és 20% között változnak. <br>
* * Ha várhatóan több mint 80 TB kezdeti biztonsági mentési adatra van szüksége egyetlen MARS-kiszolgálóról, forduljon a következőhöz: [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

>[!IMPORTANT]
>Az egyetlen kiszolgálóról származó kezdeti biztonsági mentési adatoknak egyetlen Azure Data Box példányon vagy Azure Data Box lemezen kell szerepelniük, és nem oszthatók meg több azonos vagy eltérő SKU-beli eszköz között. Egy Azure Data Box eszköz azonban több kiszolgálóról is tartalmazhat kezdeti biztonsági másolatokat.

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-subscription-and-required-permissions"></a>Azure-előfizetés és szükséges engedélyek

- A folyamathoz Azure-előfizetés szükséges.
- A folyamat megköveteli, hogy az offline biztonsági mentési szabályzat végrehajtásához kijelölt felhasználó az Azure-előfizetés tulajdonosa legyen.
- A Data Box-és a Recovery Services-tárolónak (amelybe az adatok betöltését kell végezni) ugyanahhoz az előfizetéshez kell tartoznia.
- Azt javasoljuk, hogy a Azure Data Box feladatokhoz és a Recovery Services-tárolóhoz társított célként megadott Storage-fiók ugyanabban a régióban legyen. Azonban ez nem szükséges.

### <a name="get-azure-powershell-370"></a>Azure PowerShell 3.7.0 beolvasása

*Ez a folyamat legfontosabb előfeltétele*. A Azure PowerShell telepítése előtt végezze el a következő ellenőrzéseket a 3.7.0 verzióban.

#### <a name="step-1-check-the-powershell-version"></a>1. lépés: a PowerShell verziójának megkeresése

1. Nyissa meg a Windows PowerShellt, és futtassa a következő parancsot:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1. Ha a kimenet a 3.7.0-nál nagyobb verziószámot jelenít meg, tegye a következőt: 2. lépés. Ellenkező esetben ugorjon a "3. lépés" elemre.

#### <a name="step-2-uninstall-the-powershell-version"></a>2. lépés: a PowerShell verziójának eltávolítása

Távolítsa el a PowerShell aktuális verzióját.

1. Távolítsa el a függő modulokat a következő parancs futtatásával a PowerShellben:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. Az összes függő modul sikeres törlésének biztosításához futtassa a következő parancsot:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>3. lépés: a PowerShell-verzió 3.7.0 telepítése

Miután ellenőrizte, hogy nincsenek-e AzureRM modulok, telepítse a 3.7.0 verzióját az alábbi módszerek egyikének használatával:

- A GitHubról használja [ezt a hivatkozást](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).

Vagy a következőket teheti:

- Futtassa a következő parancsot a PowerShell-ablakban:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

A Azure PowerShell MSI-fájl használatával is telepíthető. A eltávolításához távolítsa el azt a Vezérlőpult **Programok eltávolítása** beállításával.

### <a name="order-and-receive-the-data-box-device"></a>A Data Box eszköz megrendelése és fogadása

Az offline biztonsági mentési folyamat a MARS és a Azure Data Box használatával az offline biztonsági mentés a MARS-ügynök használatával történő elindítása előtt a Data Box-eszközöknek kézbesítési állapotban kell lennie. A szükséglethez legmegfelelőbb SKU rendeléséhez tekintse meg a [biztonsági másolatok méretének és a támogatott Data Box SKU](#backup-data-size-and-supported-data-box-skus)-nak a megadását ismertető témakört. Kövesse az [oktatóanyag: Azure Data Box lemez megrendelése](../databox/data-box-disk-deploy-ordered.md) és a Data Box-eszközök fogadása című témakör lépéseit.

> [!IMPORTANT]
> Ne válassza a *BlobStorage* lehetőséget a **fiókhoz**. A MARS-ügynöknek olyan fiókra van szüksége, amely támogatja a Blobok használatát, ami nem támogatott, ha a *BlobStorage* van kiválasztva. Válassza a **Storage v2 (általános célú v2)** lehetőséget **, ha a** cél Storage-fiókot hozza létre a Azure Data Box feladathoz.

![A fiók típusának kiválasztása a példány részleteiben](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>A MARS-ügynök telepítése és beállítása

1. Győződjön meg arról, hogy eltávolítja a MARS-ügynök korábbi telepítését.
1. Töltse le a legújabb MARS-ügynököt [erről a webhelyről](https://aka.ms/azurebackup_agent).
1. Futtassa a *MARSAgentInstaller.exet*, és *csak* az [ügynök telepítéséhez és regisztrálásához](./install-mars-agent.md#install-and-register-the-agent) szükséges lépéseket a Recovery Services-tárolóban, ahol a biztonsági másolatokat tárolni szeretné.

   > [!NOTE]
   > A Recovery Services-tárolónak ugyanabban az előfizetésben kell lennie, mint a Azure Data Box feladatoknak.

   Miután regisztrálta az ügynököt az Recovery Services-tárolóba, kövesse a következő szakaszokban ismertetett lépéseket.

## <a name="set-up-azure-data-box-devices"></a>Azure Data Box eszközök beállítása

A megrendelt Azure Data Box SKU-tól függően végezze el az alábbi, a megfelelő szakaszokban ismertetett lépéseket. A lépések bemutatják, hogyan állíthatja be és készítheti elő a MARS-ügynök Data Box eszközeit a kezdeti biztonsági mentési adatok azonosításához és átviteléhez.

### <a name="set-up-azure-data-box-disks"></a>Azure Data Box lemezek beállítása

Ha egy vagy több Azure Data Box lemezt (egyenként 8 TB-ig) rendelt, kövesse az itt leírt lépéseket a [Data Box lemez kicsomagolásához, összekapcsolásához és feloldásához](../databox/data-box-disk-deploy-set-up.md).

>[!NOTE]
>Lehetséges, hogy a MARS-ügynökkel rendelkező kiszolgáló nem rendelkezik USB-porttal. Ebben az esetben összekapcsolhatók a Azure Data Box lemez egy másik kiszolgálóval vagy ügyféllel, és elérhetővé tehetik az eszköz gyökerét hálózati megosztásként.

### <a name="set-up-azure-data-box"></a>Azure Data Box beállítása

Ha Azure Data Box-példányt (akár 100 TB-ot) rendelt el, kövesse az alábbi lépéseket a [Data Box példány beállításához](../databox/data-box-deploy-set-up.md).

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>Azure Data Box-példány csatlakoztatása helyi rendszerként

A MARS-ügynök a helyi rendszerkörnyezetben működik, ezért ugyanazt a jogosultsági szintet kell megadni ahhoz a csatlakoztatási útvonalhoz, ahol a Azure Data Box-példány csatlakozik.

Annak biztosítása érdekében, hogy az Data Box eszközt helyi rendszerként csatlakoztassa az NFS protokoll használatával:

1. Engedélyezze az ügyfél számára az NFS-szolgáltatást azon a Windows Serveren, amelyen telepítve van a MARS-ügynök. Határozza meg a másodlagos forrás *wim: D: \Sources\Install.wim: 4*.
1. Töltse le a PsExec a [Sysinternals](/sysinternals/downloads/psexec) lapról a kiszolgálóra, amelyen telepítve van a Mars-ügynök.
1. Nyisson meg egy rendszergazda jogú parancssort, és futtassa a következő parancsot az *PSExec.exet* tartalmazó könyvtárral az aktuális könyvtárként.

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   Az előző parancs miatt megnyíló parancssori ablak a helyi rendszerkörnyezetben található. Ezen a parancsablakban hajthatja végre az Azure-oldal blob-megosztásának hálózati meghajtóként való csatlakoztatásához szükséges lépéseket a Windows-kiszolgálón.
1. A kiszolgáló és a MARS-ügynök összekapcsolásához a Data Box eszköz NFS-en keresztül történő csatlakoztatásához kövesse a [kapcsolódás Data boxhoz](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box) című témakör lépéseit. Futtassa az alábbi parancsot a helyi rendszerparancssorban az Azure-oldal Blobok megosztásának csatlakoztatásához.

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   A megosztás csatlakoztatása után ellenőrizze, hogy elérhető-e az X: a kiszolgálóról. Ha tud, folytassa a cikk következő szakaszával.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>A kezdeti biztonsági mentési adatok átvitele Azure Data Box eszközökre

1. Nyissa meg a **Microsoft Azure Backup** alkalmazást a kiszolgálón.
1. A **műveletek** ablaktáblán válassza a **biztonsági mentés időzítése** elemet.

    ![Biztonsági mentés ütemezett kiválasztása](./media/offline-backup-azure-data-box/schedule-backup.png)

1. Kövesse a **biztonsági mentés időzítése varázsló** lépéseit.

1. Elemeket adhat hozzá az **elemek hozzáadása** gomb kiválasztásával. Tartsa meg az elemek teljes méretét a megrendelt és fogadott [Azure Data Box SKU által támogatott méreteken](#backup-data-size-and-supported-data-box-skus) belül.

    ![Elemek hozzáadása a biztonsági mentéshez](./media/offline-backup-azure-data-box/add-items.png)

1. Válassza ki a megfelelő biztonsági mentési ütemtervet és adatmegőrzési szabályzatot a **fájlokhoz és mappákhoz** és a **rendszerállapothoz**. A rendszerállapot csak Windows-kiszolgálók esetében alkalmazható, Windows-ügyfelek esetén nem.
1. A varázsló **kezdeti biztonsági mentési típus (fájlok és mappák)** lapján válassza az **átvitel lehetőséget Microsoft Azure Data Box lemezek használatával** , majd kattintson a **tovább** gombra.

    ![A kezdeti biztonsági mentés típusának kiválasztása](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. Ha a rendszer kéri, jelentkezzen be az Azure-ba az Azure-előfizetéshez tartozó tulajdonosi hozzáféréssel rendelkező felhasználói hitelesítő adatok használatával. A sikeres művelet után egy olyan oldalt kell látnia, amely ehhez hasonlít.

    ![Erőforrások létrehozása és a szükséges engedélyek alkalmazása](./media/offline-backup-azure-data-box/creating-resources.png)

   A MARS-ügynök ezután beolvassa az előfizetésben lévő Data Box feladatokat a leszállított állapotban.

    ![Data Box feladatok beolvasása az előfizetés-AZONOSÍTÓhoz](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. Válassza ki a helyes Data Box sorrendet, amelyhez kicsomagolta, csatlakoztatta és zárolta a Data Box lemezt. Kattintson a **Tovább** gombra.

    ![Data Box rendelések kiválasztása](./media/offline-backup-azure-data-box/select-databox-order.png)

1. Válassza **az eszköz** észlelése lehetőséget a **Data Box eszköz észlelése** lapon. Ezzel a művelettel a MARS-ügynök bekeresi a helyileg csatlakoztatott Azure Data Box lemezeket, és felismeri azokat.

    ![Eszköz Data Box észlelése](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Ha a Azure Data Box-példányt hálózati megosztásként csatlakoztatta (az USB-portok nem rendelkezésre állása miatt, vagy azért, mert az 100-TB Data Box eszközt megrendelte és csatlakoztatta), az észlelés először sikertelen lesz. Megadhatja a Data Box eszköz hálózati elérési útját.

    ![Adja meg a hálózati elérési utat](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Adja meg a Azure Data Box lemez gyökérkönyvtárának hálózati elérési útját. Ennek a könyvtárnak tartalmaznia kell egy könyvtárat a *PageBlob* néven.
    >
    >![Azure Data Box lemez gyökérkönyvtára](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Ha például a lemez elérési útja, a `\\mydomain\myserver\disk1\` *Disk1* pedig a *PageBlob* nevű KÖNYVTÁRAT tartalmazza, akkor a Mars-ügynök varázsló lapján megadott elérési út `\\mydomain\myserver\disk1\` .
    >
    >Ha [Azure Data Box 100 – TB eszközt állít](#set-up-azure-data-box-devices)be, adja meg az `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` eszköz hálózati elérési útját.

1. Válassza a **tovább** lehetőséget, majd a következő lapon válassza a **Befejezés** lehetőséget a biztonsági mentési és adatmegőrzési szabályzat mentéséhez az offline biztonsági mentés konfigurálásával Azure Data Box használatával.

   A következő oldal megerősíti, hogy a házirend mentése sikeresen megtörtént.

    ![A szabályzat mentése sikerült](./media/offline-backup-azure-data-box/policy-saved.png)

1. Válassza a **Bezárás** lehetőséget az előző oldalon.

1. A MARS-ügynök konzoljának **műveletek** paneljén válassza a **biztonsági mentés most** lehetőséget. A varázsló lapon válassza a **biztonsági mentés** lehetőséget.

    ![Most biztonsági mentés varázsló](./media/offline-backup-azure-data-box/backup-now.png)

A MARS-ügynök elindítja a kiválasztott adatAzure Data Box eszközre történő biztonsági mentést. Ez a folyamat több óráig is eltarthat néhány napig. Az idő mennyisége attól függ, hogy hány fájl van a kiszolgáló és a kapcsolat sebessége között a MARS-ügynökkel és a Azure Data Box lemezzel.

Az adatbiztonsági mentés befejezése után egy olyan oldal jelenik meg a MARS-ügynökön, amely ehhez hasonlít.

![A biztonsági mentés állapota látható](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Biztonsági mentés utáni lépések

Ez a szakasz ismerteti azokat a lépéseket, amelyeket az Azure Data Box Disk sikeres biztonsági mentése után kell végrehajtani.

- A [Azure Data Box lemez Azure-ba való szállításához](../databox/data-box-disk-deploy-picked-up.md)kövesse az ebben a cikkben ismertetett lépéseket. Ha Azure Data Box 100-TB eszközt használt, kövesse az alábbi lépéseket az [Azure Data Box eszköz Azure-ba történő szállításához](../databox/data-box-deploy-picked-up.md).

- [Figyelje a Data Box feladatot](../databox/data-box-disk-deploy-upload-verify.md) a Azure Portalban. A Azure Data Box feladatok befejezése után a MARS-ügynök automatikusan áthelyezi az adatait a Storage-fiókból a Recovery Services-tárolóba a következő ütemezett biztonsági mentés időpontjában. Ezután a biztonsági mentési feladatot *befejezettként* jelöli meg, ha a helyreállítási pont sikeresen létrejött.

    >[!NOTE]
    >A MARS-ügynök a házirendek létrehozásakor ütemezett időközönként elindítja a biztonsági mentéseket. Ezek a feladatok jelző "Várakozás a Azure Data Box feladat befejeződésére", amíg a feladat befejeződik.

- Miután a MARS-ügynök sikeresen létrehoz egy helyreállítási pontot, amely megfelel a kezdeti biztonsági mentésnek, törölheti a Storage-fiókot vagy a Azure Data Box-feladatokhoz tartozó konkrét tartalmakat.

## <a name="troubleshooting"></a>Hibaelhárítás

A Microsoft Azure Recovery Services-(MARS-) ügynök létrehoz egy Azure Active Directory (Azure AD) alkalmazást a bérlőben. Az alkalmazáshoz szükség van egy tanúsítványra az offline kiindulási házirend konfigurálásakor létrehozott és feltöltött hitelesítéshez. A Azure PowerShell használatával hozza létre és töltse fel a tanúsítványt az Azure AD-alkalmazásba.

### <a name="problem"></a>Probléma

A kapcsolat nélküli biztonsági mentés konfigurálásakor előfordulhat, hogy a Azure PowerShell parancsmagban fellépő hiba miatt probléma merülhet fel. Előfordulhat, hogy nem tud több tanúsítványt hozzáadni ugyanahhoz az Azure AD-alkalmazáshoz, amelyet a Mohácsi-ügynök hozott létre. Ez a probléma akkor lép érvénybe, ha egy offline előkészítési házirendet konfigurált ugyanahhoz vagy egy másik kiszolgálóhoz.

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>Annak ellenőrzése, hogy a problémát az adott alapvető ok okozza-e

Ha szeretné megtekinteni, hogy a probléma megegyezik-e a korábban leírttal, hajtsa végre az alábbi lépések egyikét.

#### <a name="step-1-of-verification"></a>Az ellenőrzés 1. lépése

Ellenőrizze, hogy a következő hibaüzenet jelenik-e meg a Mohácsi-konzolon, amikor konfigurálta az offline biztonsági mentést.

![Nem lehet offline biztonsági mentési szabályzatot létrehozni az aktuális Azure-fiókhoz](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2-of-verification"></a>Ellenőrzés 2. lépése

1. Nyissa meg a **temp** mappát a telepítési útvonalon. Az alapértelmezett ideiglenes mappa elérési útja a *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*. Keresse meg a *CBUICurr* fájlt, és nyissa meg a fájlt.

1. A *CBUICurr* fájlban görgessen az utolsó sorra, és ellenőrizze, hogy a probléma megegyezik-e a hibaüzenettel: `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed` .

### <a name="workaround"></a>Áthidaló megoldás

A probléma megoldásához hajtsa végre a következő lépéseket, majd próbálja megismételni a házirend-konfigurációt.

#### <a name="step-1-of-workaround"></a>Áthidaló megoldás 1. lépése

Jelentkezzen be a Mohácsi felhasználói felületén megjelenő PowerShellbe egy másik, rendszergazdai hozzáféréssel rendelkező fiókkal azon az előfizetésen, amely a Data Box-feladatot létrehozta.

#### <a name="step-2-of-workaround"></a>Áthidaló megoldás 2. lépése

Ha egy másik kiszolgáló nem rendelkezik kapcsolat nélküli előkészítéssel, és az alkalmazástól nem függ más kiszolgáló `AzureOfflineBackup_<Azure User Id>` , törölje az alkalmazást. Válassza ki **Azure Portal**  >  **Azure Active Directory**  >  **Alkalmazásregisztrációk**.

>[!NOTE]
> Ellenőrizze, hogy az `AzureOfflineBackup_<Azure User Id>` alkalmazás nem rendelkezik-e más kapcsolat nélküli előkészítéssel, és hogy nem függ-e az alkalmazástól más kiszolgálók.   >  A **nyilvános kulcsok** szakaszban lépjen a beállítások **kulcsra** . Nem adhat hozzá további nyilvános kulcsokat. Tekintse meg a következő képernyőképet a hivatkozáshoz.
>
>![Nyilvános kulcsok](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>3. lépés

Az offline biztonsági mentéshez konfigurálni kívánt kiszolgálóról végezze el a következő műveleteket.

1. Nyissa meg a **számítógép-tanúsítvány alkalmazása**  >  **személyes** kezelése lapot, és keresse meg a nevet tartalmazó tanúsítványt `CB_AzureADCertforOfflineSeeding_<ResourceId>` .

2. Válassza ki a tanúsítványt, kattintson a jobb gombbal a **minden feladat** elemre, majd válassza az **Exportálás** a titkos kulcs nélkül. cer formátumban lehetőséget.

3. Nyissa meg a 2. lépésben említett Azure offline Backup alkalmazást. Válassza a **Beállítások**  >  **kulcsok**  >  **feltöltés nyilvános kulcs** lehetőséget. Töltse fel az előző lépésben exportált tanúsítványt.

    ![Nyilvános kulcs feltöltése](./media/offline-backup-azure-data-box/upload-public-key.png)

4. A-kiszolgálón nyissa meg a beállításjegyzéket úgy, hogy beírja a **Regedit parancsot** a Futtatás ablakba.

5. Lépjen a beállításjegyzék *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider.* Kattintson a jobb gombbal a **CloudBackupProvider** elemre, és adjon hozzá egy új karakterlánc-értéket a névvel `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Az Azure User ID beszerzéséhez hajtsa végre az alábbi műveletek egyikét:
    >
    >- Az Azure-hoz csatlakoztatott PowerShellben futtassa a `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` parancsot.
    > - Nyissa meg a beállításjegyzék elérési útját `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` a *CurrentUserId* néven.

6. Kattintson a jobb gombbal az előző lépésben hozzáadott sztringre, majd válassza a **módosítás** lehetőséget. Az érték mezőben adja meg a 2. lépésben exportált tanúsítvány ujjlenyomatát. Válassza az **OK** lehetőséget.

7. Az ujjlenyomat értékének lekéréséhez kattintson duplán a tanúsítványra. Válassza a **részletek** lapot, és görgessen lefelé, amíg meg nem jelenik az ujjlenyomat mező. Válassza az **ujjlenyomat** lehetőséget, és másolja ki az értéket.

    ![Tanúsítvány ujjlenyomatának mezője](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Kérdések

Ha bármilyen kérdése vagy pontosítása merülne fel, forduljon a következőhöz: [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .
