---
title: Offline biztonsági mentés Azure Data Box
description: Megtudhatja, hogyan használhatja a Azure Data Box nagy kezdeti biztonsági mentési adatok offline állapotba való átállítására a MARS-ügynökről egy Recovery Services-tárolóba.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 78adc479ce5733e208d2334d30d7b88e4edf8d6b
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576091"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Azure Backup biztonsági mentés készítése az Azure Data Box

A Azure Data Box [](../databox/data-box-overview.md) használatával a kezdeti Microsoft Azure (MARS) biztonsági másolatai offline állapotúra (hálózat használata nélkül) áttűnnek egy Recovery Services-tárolóba. Ez a folyamat időt és hálózati sávszélességet takarít meg, amely egyébként felhasználná a nagy mennyiségű biztonsági mentési adat online áthelyezését egy nagy késésű hálózaton. Ez a fejlesztés jelenleg előzetes verzióban érhető el. Az offline biztonsági mentés Azure Data Box az [Azure Import/Export](./backup-azure-backup-import-export.md)szolgáltatás alapján két különböző előnyt biztosít az offline biztonsági mentéshez:

- Nem kell beszereznie saját Azure-kompatibilis lemezeket és összekötőket. Azure Data Box a kiválasztott termékváltozathoz társított [lemezeket Data Box ki.](https://azure.microsoft.com/services/databox/data/)
- Azure Backup (MARS-ügynök) közvetlenül írhat biztonsági mentési adatokat a támogatott SKUs-Azure Data Box. Ezzel a képességgel nem kell átmeneti helyet kiépítenünk a kezdeti biztonsági mentési adatokhoz. A formázáshoz és az adatok lemezekre másolására sincs szükség segédprogramokkal.

## <a name="azure-data-box-with-the-mars-agent"></a>Azure Data Box MARS-ügynökkel

Ez a cikk bemutatja, hogyan használhatja a Azure Data Box nagy méretű kezdeti biztonsági mentési adatok offline állapotba való átállítására a MARS-ügynökről egy Recovery Services-tárolóba.

## <a name="supported-platforms"></a>Támogatott platformok

A MARS-ügynöktől a következő Windows-SKUS-Azure Data Box folyamat támogatott.

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
| Windows Server 2008 SP2, 64 bites        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Az adatok biztonsági mentése és a támogatott Data Box SKUs

| Biztonsági mentési adatok mérete (MARS által tömörítés után)* kiszolgálónként | Támogatott Azure Data Box termékváltozat                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <=7,2 TB                                                    | [Azure Data Box lemez](../databox/data-box-disk-overview.md) |
| >7,2 TB és <=80 TB**                                      | [Azure Data Box (100 TB)](../databox/data-box-overview.md) |

*A tömörítés jellemző aránya 10% és 20% között változhat. <br>
**Ha azt várja, hogy egyetlen MARS-kiszolgáló több mint 80 TB kezdeti biztonsági mentési adata legyen, lépjen kapcsolatba a [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) webkiszolgálóval.

>[!IMPORTANT]
>Az egyetlen kiszolgálóról származó kezdeti biztonsági mentési adatokat egyetlen Azure Data Box-példánynak vagy Azure Data Box-lemeznek kell tartalmazni, és nem lehet megosztani ugyanazon vagy eltérő SKUs-okkal rendelkező több eszköz között. A Azure Data Box több kiszolgálóról származó kezdeti biztonsági másolatokat is tartalmazhatnak.

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-subscription-and-required-permissions"></a>Azure-előfizetés és szükséges engedélyek

- A folyamathoz Azure-előfizetés szükséges.
- A folyamat megköveteli, hogy az offline biztonsági mentési szabályzat végrehajtásához kijelölt felhasználó az Azure-előfizetés tulajdonosa.
- Az Data Box feladatnak és a Recovery Services-tárolónak (amelyre az adatokat át kell adni) ugyanabban az előfizetésben kell lennie.
- Javasoljuk, hogy az Azure Data Box feladathoz és a Recovery Services-tárolóhoz társított cél tárfiók ugyanabban a régióban található. Ez azonban nem szükséges.

### <a name="get-azure-powershell-370"></a>A Azure PowerShell 3.7.0-s vagy

*Ez a folyamat legfontosabb előfeltétele.* Mielőtt telepít Azure PowerShell 3.7.0-s verziót, végezze el a következő ellenőrzéseket.

#### <a name="step-1-check-the-powershell-version"></a>1. lépés: A PowerShell verziójának ellenőrzése

1. Nyissa Windows PowerShell, és futtassa a következő parancsot:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1. Ha a kimenet a 3.7.0-snál újabb verziót jelenít meg, tegye a "2. lépést". Egyéb esetben ugorjon a "3. lépés" lépésre.

#### <a name="step-2-uninstall-the-powershell-version"></a>2. lépés: A PowerShell-verzió eltávolítása

Távolítsa el a PowerShell aktuális verzióját.

1. Távolítsa el a függő modulokat a következő parancs PowerShellben való futtatásával:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. Az összes függő modul sikeres törlésének biztosításához futtassa a következő parancsot:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>3. lépés: A PowerShell 3.7.0-s verziójának telepítése

Miután ellenőrizte, hogy nincsenek-e AzureRM-modulok, telepítse a 3.7.0-s verziót az alábbi módszerek egyikével:

- A GitHubon használja ezt [a hivatkozást.](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)

A következőt is használhatja:

- Futtassa a következő parancsot a PowerShell-ablakban:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell msi-fájllal is telepíthették volna. Az eltávolításhoz távolítsa el  az alkalmazás Programok eltávolítása lehetőségének Vezérlőpult.

### <a name="order-and-receive-the-data-box-device"></a>Az eszköz megrendelése és Data Box fogadása

A MARS-t és Azure Data Box használó offline biztonsági mentési folyamat megköveteli, hogy a Data Box-eszközök Kézbesítve állapotúak, mielőtt offline biztonsági mentést indítanának a MARS-ügynökkel. A követelménynek leginkább megfelelő termékváltozat megrendeléséhez tekintse meg a biztonsági mentési adatok méretét és a támogatott [termékváltozatokat Data Box termékváltozatokat.](#backup-data-size-and-supported-data-box-skus) Kövesse az [Oktatóanyag: Lemez megrendelése Azure Data Box](../databox/data-box-disk-deploy-ordered.md) a rendeléshez és a Data Box fogadásához.

> [!IMPORTANT]
> A Fiók fajtánál ne válassza a *BlobStorage* **lehetőséget.** A MARS-ügynökhöz olyan fiókra van szükség, amely támogatja a lapblobokat, ami a *BlobStorage kiválasztásakor* nem támogatott. Válassza a Storage V2 (általános  célú **v2)** lehetőséget a fiók fajtájaként, amikor létrehozza a tárfiókot a Azure Data Box számára.

![Fiók fajtája kiválasztása a példány részleteiben](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>A MARS-ügynök telepítése és beállítása

1. Győződjön meg arról, hogy eltávolította a MARS-ügynök korábbi telepítéseit.
1. Töltse le a legújabb MARS-ügynököt erről [a webhelyről.](https://aka.ms/azurebackup_agent)
1. Futtassa *aMARSAgentInstaller.exe,* és csak [](./install-mars-agent.md#install-and-register-the-agent) a következő lépéseket kell megtennie az ügynök telepítéséhez és abban a Recovery Services-tárolóban való regisztráláshoz, ahol a biztonsági másolatokat tárolni szeretné. 

   > [!NOTE]
   > A Recovery Services-tárolónak ugyanabban az előfizetésben kell lennie, mint a Azure Data Box feladatnak.

   Miután az ügynök regisztrálva van a Recovery Services-tárolóban, kövesse a következő szakaszokban található lépéseket.

## <a name="set-up-azure-data-box-devices"></a>A Azure Data Box beállítása

A megrendelt Azure Data Box termékváltozattól függően hajtsa végre a következő szakaszokban szereplő lépéseket. A lépések azt mutatják be, hogyan állíthatja be és készítheti elő a Data Box a MARS-ügynök számára a kezdeti biztonsági mentési adatok azonosításához és átviteléhez.

### <a name="set-up-azure-data-box-disks"></a>A Azure Data Box beállítása

Ha egy vagy több Azure Data Box (akár 8 TB-os) lemezt rendelt, kövesse az itt említett lépéseket a lemez kicsomagolása, csatlakoztatása és [zárolásának Data Box érdekében.](../databox/data-box-disk-deploy-set-up.md)

>[!NOTE]
>Lehetséges, hogy a MARS-ügynökkel rendelkező kiszolgáló nem rendelkezik USB-porttal. Ebben az esetben csatlakoztathatja a Azure Data Box lemezét egy másik kiszolgálóhoz vagy ügyfélhez, és hálózati megosztásként elérhetővé teszi az eszköz gyökerét.

### <a name="set-up-azure-data-box"></a>A Azure Data Box

Ha egy Azure Data Box (legfeljebb 100 TB) rendelt, kövesse az itt található lépéseket a Data Box [beállítására.](../databox/data-box-deploy-set-up.md)

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>A Azure Data Box csatlakoztatása helyi rendszerként

A MARS-ügynök a helyi rendszer környezetében működik, ezért ugyanolyan szintű jogosultságot kell biztosítani ahhoz a csatlakoztatási útvonalhoz, amelyhez a Azure Data Box példány csatlakozik.

Annak biztosítása érdekében, hogy Data Box eszköz helyi rendszerként is csatlakoztatva legyen az NFS protokoll használatával:

1. Engedélyezze az ügyfelet az NFS szolgáltatáshoz a Windows-kiszolgálón, amelyre telepítve van a MARS-ügynök. Adja meg a *WIM:D:\Sources\Install.wim:4 alternatív forrást.*
1. Töltse le a PsExecet a [Sysinternals oldalról](/sysinternals/downloads/psexec) arra a kiszolgálóra, amelyre telepítve van a MARS-ügynök.
1. Nyisson meg egy rendszergazda jogú parancssort, és  futtassa a következő parancsot az aktuális könyvtárkéntPSExec.exekönyvtárat tartalmazó könyvtárban.

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   Az előző parancs miatt megnyíló parancsablak a Helyi rendszer környezetben található. Ezzel a parancsablakkal hajthatja végre az Azure-lapblob-megosztás hálózati meghajtóként való csatlakoztatásának lépéseit a Windows-kiszolgálón.
1. A Connect [to Data Box](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box) (Csatlakozás a Data Box) lépésekkel csatlakoztassa a kiszolgálót a MARS-ügynökkel a Data Box eszközhöz NFS-en keresztül. Futtassa a következő parancsot a helyi rendszer parancssorában az Azure-lapblobok megosztásának csatlakoztatáshoz.

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   Miután csatlakoztatta a megosztást, ellenőrizze, hogy elérhető-e az X: a kiszolgálóról. Ha lehet, folytassa a cikk következő szakaszával.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Kezdeti biztonsági mentési adatok átvitele Azure Data Box eszközökre

1. Nyissa meg **a Microsoft Azure Backup** alkalmazást a kiszolgálón.
1. A Műveletek **panelen** válassza a Biztonsági **mentés ütemezése lehetőséget.**

    ![Válassza a Biztonsági mentés ütemezése lehetőséget](./media/offline-backup-azure-data-box/schedule-backup.png)

1. Kövesse a Biztonsági mentés ütemezése **varázsló lépéseit.**

1. Elemeket az Elemek hozzáadása **gomb kiválasztásával adhat** hozzá. A megrendelt és fogadott termékváltozat által támogatott méretkorláton [belül Azure Data Box](#backup-data-size-and-supported-data-box-skus) meg az elemek teljes méretét.

    ![Elemek hozzáadása a biztonsági mentéshez](./media/offline-backup-azure-data-box/add-items.png)

1. Válassza ki a megfelelő biztonsági mentési ütemezést és adatmegőrzési szabályzatot a **Fájlok és mappák és** a **Rendszerállapot számára.** A rendszerállapot csak a Windows-kiszolgálókra vonatkozik, a Windows-ügyfelekre nem.
1. A varázsló Kezdeti biztonsági mentés **típusának kiválasztása (Fájlok** és mappák) lapján válassza az Átvitel lemezekkel **Microsoft Azure Data Box lehetőséget,** majd kattintson a Tovább **gombra.**

    ![Kezdeti biztonsági mentés típusának kiválasztása](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. Amikor a rendszer kéri, jelentkezzen be az Azure-ba olyan felhasználói hitelesítő adatokkal, amelyek tulajdonosi hozzáféréssel rendelkezik az Azure-előfizetésben. Ha sikeresen ezt teszi, az alábbihoz hasonló oldalnak kell látszania.

    ![Erőforrások létrehozása és a szükséges engedélyek alkalmazása](./media/offline-backup-azure-data-box/creating-resources.png)

   A MARS-ügynök ezután lekéri Data Box kézbesített állapotban van az előfizetésben elérhető összes feladathoz.

    ![Előfizetés-Data Box feladatok beolvasása](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. Válassza ki Data Box megfelelő sorrendet, amelyhez kicsomagolta, csatlakoztatta és feloldotta a lemez Data Box zárolását. Kattintson a **Tovább** gombra.

    ![Válassza a Data Box lehetőséget](./media/offline-backup-azure-data-box/select-databox-order.png)

1. Válassza **az Eszköz észlelése** lehetőséget **Data Box Eszközészlelés lapon.** Ezzel a művelettel a MARS-ügynök helyileg csatlakoztatott lemezeket Azure Data Box, és észleli azokat.

    ![Data Box eszközészlelés](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Ha az Azure Data Box-példányt hálózati megosztásként csatlakoztatta (az USB-portok elérhetetlensége miatt, vagy a 100 TB-os Data Box-eszköz megrendelése és csatlakoztatása miatt), az észlelés először meghiúsul. Megadhatja a hálózati elérési utat a Data Box eszközhöz.

    ![Adja meg a hálózati elérési utat](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Adja meg a virtuális lemez gyökérkönyvtárának Azure Data Box elérési útját. Ennek a könyvtárnak Tartalmaznia kell egy *PageBlob nevű könyvtárat.*
    >
    >![Az Azure Data Box gyökérkönyvtára](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Ha például a lemez elérési útja, és a disk1 tartalmaz egy `\\mydomain\myserver\disk1\` *PageBlob* nevű könyvtárat, akkor a MARS-ügynök varázslólapján beírott elérési út a  `\\mydomain\myserver\disk1\` következő: .
    >
    >Ha [100 TB-Azure Data Box](#set-up-azure-data-box-devices)beállított egy eszközt, az eszköz hálózati elérési útjaként adja `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` meg a következőt: .

1. Válassza **a** Tovább  lehetőséget, majd a következő lapon válassza a Befejezés lehetőséget a biztonsági mentési és megőrzési szabályzat mentéséhez az offline biztonsági mentés konfigurációjának Azure Data Box.

   A következő oldal megerősíti, hogy a szabályzat mentése sikeres volt.

    ![A szabályzat mentése sikeresen megtörtént](./media/offline-backup-azure-data-box/policy-saved.png)

1. Az **előző oldalon** válassza a Bezárás lehetőséget.

1. A **MARS-ügynökkonzol** Műveletek paneljén válassza a Biztonságimentás most lehetőséget.  A **varázsló oldalán** válassza a Biztonságimentás lehetőséget.

    ![Biztonságimentás most varázsló](./media/offline-backup-azure-data-box/backup-now.png)

A MARS-ügynök megkezdi a kiválasztott adatok biztonságimentését a Azure Data Box eszközön. Ez a folyamat több órától akár néhány napig is eltelhet. Az idő mennyisége a fájlok számától és a KISZOLGÁLÓ ÉS a MARS-ügynök közötti kapcsolati sebességtől és a lemezen Azure Data Box függ.

Az adatok biztonsági mentésének befejezése után egy, az alábbihoz hasonló oldal fog látni a MARS-ügynökben.

![A biztonsági mentés folyamatának állapota](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Biztonsági mentés utáni lépések

Ez a szakasz ismerteti az adatok biztonsági mentésének sikeres biztonsági mentése után Azure Data Box Disk lépéseket.

- A cikkben található lépéseket követve a virtuális merevlemezt Azure Data Box [azure-ba.](../databox/data-box-disk-deploy-picked-up.md) Ha 100 TB-os Azure Data Box használt, kövesse az alábbi lépéseket a virtuális Azure Data Box Azure-ba való [szállításhoz.](../databox/data-box-deploy-picked-up.md)

- [Figyelje Data Box feladat a](../databox/data-box-disk-deploy-upload-verify.md) Azure Portal. Az Azure Data Box feladat befejezése után a MARS-ügynök a következő ütemezett biztonsági mentéskor automatikusan áthelyezi az adatokat a tárfiókból a Recovery Services-tárolóba. Ezután befejezett feladatként  jelöli meg a biztonsági mentési feladatot, ha a helyreállítási pont sikeresen létrejött.

    >[!NOTE]
    >A MARS-ügynök a szabályzat létrehozása során ütemezett időpontokban indítja el a biztonsági mentéseket. Ezek a feladatok a "Várakozás a Azure Data Box befejezésére" jelzőt jelzik a feladat befejezéséig.

- Miután a MARS-ügynök sikeresen létrehozott egy helyreállítási pontot, amely megfelel a kezdeti biztonsági mentésnek, törölheti a tárfiókot vagy a feladathoz társított Azure Data Box tartalmát.

## <a name="troubleshooting"></a>Hibaelhárítás

A Microsoft Azure Recovery Services- (MARS-) ügynök létrehoz egy Azure Active Directory-alkalmazást (Azure AD) a bérlőben. Az alkalmazásnak szüksége van egy tanúsítványra a hitelesítéshez, amely akkor jön létre és lesz feltöltve, amikor offline adatokat használ az adatokat beállítva. A tanúsítvány Azure PowerShell a tanúsítványt az Azure AD-alkalmazásba.

### <a name="problem"></a>Probléma

Az offline biztonsági mentés konfigurálásakor a hiba a parancsmag Azure PowerShell előfordulhat. Előfordulhat, hogy nem tud több tanúsítványt hozzáadni a MAB-ügynök által létrehozott Azure AD-alkalmazáshoz. Ez a probléma akkor is érinti, ha offline adatokat állítva konfigurált egy ugyanazon vagy egy másik kiszolgálóra vonatkozó adatokat.

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>Ellenőrizze, hogy a problémát az adott kiváltó ok okozza-e

Ha meg kell tudni, hogy a probléma ugyanaz-e, mint a korábban leírt probléma, tegye a következők egyikét.

#### <a name="step-1-of-verification"></a>Az ellenőrzés 1. lépése

Ellenőrizze, hogy a következő hibaüzenet jelenik-e meg a MAB-konzolon az offline biztonsági mentés konfigurálásakor.

![Nem hozható létre offline biztonsági mentési szabályzat az aktuális Azure-fiókhoz](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2-of-verification"></a>Az ellenőrzés 2. lépése

1. Nyissa meg **a Temp** mappát a telepítési útvonalon. Az alapértelmezett ideiglenes mappa elérési *útja: C:\Program Files\Microsoft Azure Recovery Services Agent\Temp.* Keresse meg a *CBUICurr* fájlt, és nyissa meg.

1. A *CBUICurr* fájlban görgessen az utolsó sorba, és ellenőrizze, hogy a probléma ugyanaz-e, mint a hibaüzenetben: `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed` .

### <a name="workaround"></a>Áthidaló megoldás

Megkerülő megoldásként a probléma megoldásához kövesse az alábbi lépéseket, és próbálja meg újra a szabályzatkonfigurációt.

#### <a name="step-1-of-workaround"></a>Az áthidaló megoldás 1. lépése

Jelentkezzen be a MAB felhasználói felületén megjelenő PowerShellbe egy másik fiókkal, amely rendszergazdai hozzáféréssel rendelkezik az előfizetéshez, és létrehozta a Data Box feladatot.

#### <a name="step-2-of-workaround"></a>Az áthidaló megoldás 2. lépése

Ha nincs konfigurálva másik kiszolgáló offline adatokat, és nincs más kiszolgáló, amely az alkalmazástól `AzureOfflineBackup_<Azure User Id>` függ, törölje ezt az alkalmazást. Válassza **a Azure Portal**  >  **Azure Active Directory**  >  **Alkalmazásregisztrációk** lehetőséget.

>[!NOTE]
> Ellenőrizze, hogy az alkalmazáshoz nincs-e más offline adatokat konfigurálva, valamint azt is, hogy nincs-e más kiszolgáló, amely `AzureOfflineBackup_<Azure User Id>` az alkalmazástól függ. A Nyilvános **kulcsok**  >  **szakaszban kattintson** a **Beállítási kulcsok** elemre. Nem lehet más nyilvános kulcs hozzáadva. Referenciaként tekintse meg az alábbi képernyőképet.
>
>![Nyilvános kulcsok](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>3. lépés

Az offline biztonsági mentéshez konfigurálni próbált kiszolgálón hajtsa végre a következő műveleteket.

1. A Számítógép-tanúsítványalkalmazás személyes **kezelése** lapon keresse meg a nevű  >   `CB_AzureADCertforOfflineSeeding_<Timestamp>` tanúsítványt.

2. Válassza ki a tanúsítványt, kattintson a jobb gombbal a **Minden** feladat elemre, és válassza az **Exportálás** titkos kulcs nélkül lehetőséget .cer formátumban.

3. Lépjen a 2. lépésben említett Azure offline biztonsági mentési alkalmazáshoz. Válassza a **Beállítási**  >  **kulcsok**  >  **Nyilvános kulcs feltöltése lehetőséget.** Töltse fel az előző lépésben exportált tanúsítványt.

    ![Nyilvános kulcs feltöltése](./media/offline-backup-azure-data-box/upload-public-key.png)

4. A kiszolgálón nyissa meg a beállításjegyzéket úgy, hogy beírása a **regedit gombra** a futtatás ablakban.

5. Ugrás a *beállításjegyzék-Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider.* Kattintson a jobb gombbal a **CloudBackupProvider elemre,** és adjon hozzá egy új sztringértéket `AzureADAppCertThumbprint_<Azure User Id>` néven.

    >[!NOTE]
    > Az Azure-beli felhasználói azonosító lekért műveletekkel hajtsa végre az alábbi műveletek valamelyikét:
    >
    >- Az Azure-hoz csatlakoztatott PowerShellben futtassa az `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` parancsot.
    > - A beállításjegyzék `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` *currentUserId* nevű elérési útjának elérési útja.

6. Kattintson a jobb gombbal az előző lépésben hozzáadott sztringre, majd válassza a **Módosítás lehetőséget.** Az értékben adja meg a 2. lépésben exportált tanúsítvány ujjlenyomatát. Válassza az **OK** lehetőséget.

7. Az ujjlenyomat értékének lekérelemhez kattintson duplán a tanúsítványra. Válassza a **Részletek lapot,** és görgessen lefelé, amíg meg nem látja az ujjlenyomat mezőt. Válassza **az Ujjlenyomat lehetőséget,** és másolja ki az értéket.

    ![Tanúsítvány ujjlenyomatmezője](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Kérdések

Ha bármilyen kérdése vagy pontosítása merült fel az ön által tapasztalt problémákkal kapcsolatban, lépjen kapcsolatba a következővel: [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .
