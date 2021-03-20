---
title: A DPM és az Azure Backup Server kapcsolat nélküli biztonsági mentése
description: A Azure Backup segítségével az Azure import/export szolgáltatással küldhet adathálózatot a hálózatról. Ez a cikk a DPM és a Azure Backup Server offline biztonsági mentési munkafolyamatát ismerteti.
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: 006c0fa4d67c9a85426d7a007912df65876313da
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98701813"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server-mabs"></a>A DPM és a Azure Backup Server offline biztonsági mentési munkafolyamata (MABS)

>[!IMPORTANT]
> Ezek a lépések a DPM 2019 UR1 (vagy újabb) és a MABS v3 UR1 (vagy újabb) esetén alkalmazhatók.

A System Center Data Protection Manager és Azure Backup Server (MABS) integrálása a Azure Backup használatával történik, és számos beépített hatékonyságot biztosít, amelyek hálózati és tárolási költségeket takarítanak meg az Azure-ba irányuló összes adat kezdeti teljes biztonsági mentése során. A kezdeti teljes biztonsági mentések általában nagy mennyiségű adat átvitelét igénylik, és nagyobb hálózati sávszélességet igényelnek, ha az azokat követő biztonsági mentések során csak a különbözeteket/növekményeket továbbítják. Azure Backup tömöríti a kezdeti biztonsági mentéseket. Az offline kivetés folyamatán keresztül a Azure Backup lemezek használatával feltölthetik a tömörített kezdeti biztonsági mentési adatok az Azure-ba.

Azure Backup kapcsolat nélküli előkészítési folyamata szorosan integrálva van az [Azure import/export szolgáltatással](../import-export/storage-import-export-service.md). Ezzel a szolgáltatással lemezeket vihet át az Azure-ba. Ha terabájt (TBs) típusú kezdeti biztonsági mentési adat szükséges, amelyet nagy késésű és alacsony sávszélességű hálózaton kell átvinni, az offline előkészítési munkafolyamattal egy vagy több merevlemezen is elküldheti az Azure-adatközpontba a kezdeti biztonsági másolatot. Ez a cikk áttekintést nyújt, és további lépéseket tesz a munkafolyamat a System Center Data Protection Manager (DPM) és a Microsoft Azure Backup Server (MABS) rendszerhez való befejezéséhez.

> [!NOTE]
> A Microsoft Azure Recovery Services-(MARS-) ügynök offline biztonsági mentésének folyamata különbözik a DPM és a MABS. Az offline biztonsági mentés a MARS-ügynökkel való használatával kapcsolatban lásd: [Offline biztonsági mentési munkafolyamat a Azure Backup](backup-azure-backup-import-export.md). Az offline biztonsági mentés nem támogatott a rendszerállapot-biztonsági másolatok számára a Azure Backup ügynök használatával.
>
> A MABS UR1-frissítés az offline biztonsági mentés előzetes verzióját is megjeleníti a MABS-ben lévő Azure Data Box használatával. További információért forduljon a következőhöz: [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com) .

## <a name="overview"></a>Áttekintés

Az Azure Backup és az Azure import/export szolgáltatás offline kicsomagolási funkciójának használatával egyszerűen tölthetők fel az adatok az Azure-ba a lemezekkel. Az offline biztonsági mentési folyamat az alábbi lépéseket foglalja magában:

> [!div class="checklist"]
>
> * A biztonsági mentési adat a hálózaton keresztüli küldés helyett átmeneti helyre íródik.
> * Az előkészítési helyen lévő adatai ezután egy vagy több SATA-lemezre íródnak a *AzureOfflineBackupDiskPrep* segédprogram használatával.
> * A segédprogram automatikusan létrehoz egy Azure-beli importálási feladatot.
> * Ezután a rendszer elküldi a SATA-meghajtókat a legközelebbi Azure-adatközpontba.
> * Miután befejeződött a biztonsági mentési adatok feltöltése az Azure-ba, Azure Backup másolja a biztonsági mentési adatokat a Backup-tárolóba, és a növekményes biztonsági mentések ütemezve lesznek.

## <a name="prerequisites"></a>Előfeltételek

Az offline biztonsági mentési munkafolyamat elindítása előtt győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

* A rendszer létrehozta [Recovery Services](backup-azure-recovery-services-vault-overview.md) -tárolót. A létrehozásához kövesse az Recovery Services-tároló [létrehozása](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)– biztonsági mentés – Windows-kiszolgáló – Azure # Create-a-Recovery-Services-Vault című témakör lépéseit.
* Győződjön meg arról, hogy csak az [Microsoft Azure Recovery Services ügynök legújabb verziója](https://aka.ms/azurebackup_agent) van telepítve az SC DPM vagy a MABS, és regisztrálja őket Recovery Services-tárolóban.
* Az 1. kumulatív frissítés az SC DPM 2019 vagy MABS v3 verzióra van telepítve.

  > [!NOTE]
  > A DPM 2019 UR1 és a MABS v3 UR1 az offline kiindulási hitelesítés Azure Active Directory használatával történik.

* A DPM vagy a MABS-kiszolgálón győződjön meg arról, hogy a Microsoft Edge vagy az Internet Explorer 11 telepítve van, és a JavaScript engedélyezve van.
* Hozzon létre egy Azure Storage-fiókot a Recovery Services-tárolóval megegyező előfizetésben.
* Győződjön meg arról, hogy rendelkezik a [szükséges engedélyekkel](../active-directory/develop/howto-create-service-principal-portal.md) a Azure Active Directory alkalmazás létrehozásához. Az offline biztonsági mentési munkafolyamat létrehoz egy Azure Active Directory alkalmazást az Azure Storage-fiókhoz társított előfizetésben. Az alkalmazás célja, hogy az offline biztonsági mentési munkafolyamathoz szükséges biztonságos és hatókörrel rendelkező Azure Backup biztosítson az Azure importálási szolgáltatáshoz.
* Regisztrálja a Microsoft. ImportExport erőforrás-szolgáltatót az Azure Storage-fiókot tartalmazó előfizetéssel. Az erőforrás-szolgáltató regisztrálása:
    1. A főmenüben válassza az **előfizetések** lehetőséget.
    2. Ha több előfizetésre is feliratkozott, válassza ki azt az előfizetést, amelyet az offline biztonsági mentéshez használ. Ha csak egy előfizetést használ, megjelenik az előfizetése.
    3. Az előfizetés menüben válassza az **erőforrás-szolgáltatók** lehetőséget a szolgáltatók listájának megtekintéséhez.
    4. A szolgáltatók listájában görgessen le a Microsoft. ImportExport. Ha az állapot NotRegistered, válassza a **regisztráció** lehetőséget.

       ![Az erőforrás-szolgáltató regisztrálása](./media/backup-azure-backup-server-import-export/register-import-export.png)

* A rendszer létrehoz egy átmeneti helyet, amely lehet hálózati megosztás vagy a számítógép belső vagy külső meghajtója, amely elegendő lemezterülettel rendelkezik a kezdeti másolat tárolásához. Ha például egy 500 GB-os fájlkiszolgáló biztonsági mentését kívánja végezni, győződjön meg arról, hogy az átmeneti területen legalább 500 GB. (A tömörítés miatt kisebb mennyiség van használatban.)
* Az Azure-ba küldendő lemezek esetében ügyeljen arra, hogy csak a 2,5 hüvelykes SSD-vagy 2,5-es vagy 3,5 hüvelykes SATA II/III-alapú merevlemezek legyenek használatban. A merevlemezeket akár 10 TB-ig is használhatja. Az [Azure import/export szolgáltatás dokumentációjában](../import-export/storage-import-export-requirements.md#supported-hardware) keresse meg a szolgáltatás által támogatott meghajtók legújabb készletét.
* A SATA-meghajtóknak csatlakoztatva kell lennie egy számítógéphez (ez a *másolási számítógép*), ahonnan a biztonsági mentési adatok másolata az átmeneti helyről a SATA-meghajtókra történik. Győződjön meg arról, hogy a BitLocker engedélyezve van a másolási számítógépen.

## <a name="workflow"></a>Munkafolyamat

Az ebben a szakaszban található információk segítségével elvégezheti az offline biztonsági mentés munkafolyamatát, így az adatok továbbíthatók egy Azure-adatközpontba, és feltölthetők az Azure Storage-ba. Ha kérdése van az importálási szolgáltatással vagy a folyamat bármely aspektusával kapcsolatban, tekintse meg a korábban hivatkozott [importálási szolgáltatás áttekintése](../import-export/storage-import-export-service.md) dokumentációt.

## <a name="initiate-offline-backup"></a>Offline biztonsági mentés indítása

1. Ha új védelmi csoportot hoz létre az online védelemmel, vagy online védelmet szeretne hozzáadni a meglévő védelmi csoportokhoz, az alábbi képernyő jelenik meg. A kezdeti online replikációs módszer kiválasztásához válassza **az átvitel saját lemez használatával** lehetőséget, majd kattintson a **tovább** gombra.

    ![Importálási képernyő](./media/backup-azure-backup-server-import-export/create-new-protection-group.png)

2. Ekkor megnyílik az Azure bejelentkezési oldala. Jelentkezzen be a *tulajdonosi* szerepkörrel rendelkező Azure-beli felhasználói fiókjával az Azure-előfizetésben.

    ![Azure bejelentkezési oldal](./media/backup-azure-backup-server-import-export/choose-initial-online-replication.png)

3. Adja meg a bemeneteket a **saját lemez használata** oldalon.

   ![Saját lemez használatára szolgáló bemenetek](./media/backup-azure-backup-server-import-export/use-your-own-disk.png)

   A bemenetek leírása a következő:

   * **Átmeneti hely**: az az ideiglenes tárolási hely, ahová a rendszer a kezdeti biztonsági másolatot írja. Előfordulhat, hogy az előkészítési hely egy hálózati megosztáson vagy egy helyi számítógépen található. Ha a számítógép és a forrásszámítógép másolása eltérő, akkor az előkészítési hely teljes hálózati elérési útját kell megadni.
   * **Azure Resource Manager Storage-fiók**: a Resource Manager típusú Storage-fiók (általános célú v1 vagy általános célú v2) neve bármely Azure-előfizetésben.
   * **Azure Storage-tároló**: a cél blob Storage-tároló neve abban az Azure Storage-fiókban, ahová a biztonsági mentési adatmennyiséget importálja.
   * **Azure-előfizetés azonosítója**: annak az Azure-előfizetésnek az azonosítója, amelyhez az Azure Storage-fiókot létrehozták.
   * **Azure importálási feladattípus**: az az egyedi név, amellyel az Azure import service és Azure Backup nyomon követheti a lemezeken továbbított adatok átvitelét az Azure-ba.

    Mentse az **előkészítési helyet** és az **Azure-beli importálási feladathoz megadott nevet** . A lemezek előkészítéséhez szükséges.

4. Fejezze be a munkafolyamatot a védelem létrehozásához vagy frissítéséhez. Az offline biztonsági mentést tartalmazó másolat kezdeményezéséhez kattintson a jobb gombbal a **védelmi csoportra**, majd válassza a **helyreállítási pont létrehozása** lehetőséget. Ezután válassza az **online védelem** lehetőséget.

   ![Helyreállítási pont létrehozása](./media/backup-azure-backup-server-import-export/create-recovery-point.png)

5. Figyelje meg az online replika-létrehozási feladatot a figyelés ablaktáblán. A feladatsornak sikeresen végre kell hajtania az *Azure-beli importálási feladatok befejezésére vonatkozó* figyelmeztetést.

   ![Helyreállítási pont befejezése](./media/backup-azure-backup-server-import-export/complete-recovery-point.png)

6. A művelet befejeződése után az előkészítési hely készen áll a lemez-előkészítésre.

## <a name="prepare-sata-drives-and-ship-to-azure"></a>A SATA-meghajtók előkészítése és a szállítás az Azure-ba

A *AzureOfflineBackupDiskPrep* segédprogram előkészíti a legközelebbi Azure-adatközpontba küldendő SATA-meghajtókat. Ez a segédprogram a Azure Backup Agent telepítési könyvtárában érhető el (a következő elérési úton): `*\Microsoft Azure Recovery Services Agent\Utils\\*`

1. Nyissa meg a könyvtárat, és másolja a **AzureOfflineBackupDiskPrep** könyvtárat egy másik számítógépre, AMELYEN a SATA-meghajtók csatlakoznak. A csatlakoztatott SATA meghajtókkal rendelkező számítógépen ügyeljen a következőre:

   * A másolási számítógép a (z) "kapcsolat nélküli biztonsági mentés kezdeményezése" szakaszban található munkafolyamatban megadott hálózati elérési út használatával férhet hozzá az offline tesztelési munkafolyamat előkészítési helyéhez.
   * A BitLocker engedélyezve van a másolási számítógépen.
   * Azure PowerShell 3.7.0 telepítve van a másolási számítógépen (nem szükséges, ha a DPM vagy a MABS-kiszolgálón futtatja a AzureOfflineBackupDiskPrep segédprogramot).
   * A legújabb kompatibilis böngészők (Microsoft Edge vagy Internet Explorer 11) telepítve vannak, és a JavaScript engedélyezve van.
   * A másolási számítógép elérheti a Azure Portal. Ha szükséges, a másolási számítógép lehet ugyanaz, mint a forrásszámítógép.

     > [!IMPORTANT]
     > Ha a forrásszámítógép egy virtuális gép, akkor a másolási számítógépként egy másik fizikai kiszolgálót vagy ügyfélszámítógépet kell használnia.

1. Nyisson meg egy rendszergazda jogú parancssort a másolási számítógépen a *AzureOfflineBackupDiskPrep* segédprogram címtárával az aktuális könyvtárként. Futtassa az alábbi parancsot:

    ```console
    .\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>
    ```

    | Paraméter | Leírás |
    | --- | --- |
    | s: &lt; *átmeneti hely elérési útja*&gt; |Ez a kötelező bemenet arra szolgál, hogy megadja a munkafolyamatban megadott átmeneti hely elérési útját a "kapcsolat nélküli biztonsági mentés indítása" szakaszban. |
    | p: &lt; *a PublishSettingsFile elérési útja*&gt; |Ez a választható bemenet az Azure publish Settings fájl elérési útjának megadására szolgál. |

    A parancs futtatásakor a segédprogram kéri az Azure importálási feladatoknak a kiválasztását, amely megfelel az előkészíteni kívánt meghajtóknak. Ha csak egyetlen importálási feladattípus van társítva a megadott átmeneti helyhez, a következőhöz hasonló képernyő jelenik meg.

      ![Lemez-előkészítési konzol](./media/backup-azure-backup-server-import-export/disk-prep-console.png)

1. Adja meg a meghajtóbetűjelet az Azure-ba való átvitelre előkészíteni kívánt csatlakoztatott lemez záró kettőspontja nélkül.
1. Ha a rendszer kéri, adja meg a meghajtó formázásának megerősítését.
1. A rendszer felszólítja, hogy jelentkezzen be az Azure-előfizetésbe. Adja meg a hitelesítő adatait.

    ![Azure bejelentkezési képernyő](./media/backup-azure-backup-server-import-export/signin-disk-prep.png)

    Az eszköz ezután megkezdi a lemez előkészítését és a biztonsági mentési Adatmásolást. Előfordulhat, hogy további lemezeket kell csatlakoztatnia, amikor az eszköz kéri, ha a megadott lemez nem rendelkezik elegendő hellyel a biztonsági mentési információkhoz. <br/>

    Az eszköz sikeres végrehajtása végén a parancssor három információt nyújt:
    * Egy vagy több megadott lemez készen áll az Azure-ba történő szállításra.
    * A rendszer megerősíti, hogy az importálási feladatot létrehozták. Az importálási feladatokhoz a megadott nevet kell használnia.
    * Az eszköz az Azure-adatközponthoz tartozó szállítási címet jeleníti meg.

     ![Az Azure Disk-előkészítés befejeződött](./media/backup-azure-backup-server-import-export/console.png)

1. A parancs végrehajtásának végén a szállítási információk frissítésének lehetősége is megjelenik.

1. Küldje le a lemezeket az eszköz által biztosított címnek, és tartsa meg a nyomon követési számot későbbi használatra.

   > [!IMPORTANT]
   > Két Azure-beli importálási feladat nem rendelkezhet ugyanazzal a követési számmal. Győződjön meg arról, hogy egyetlen Azure-beli importálási feladatban a segédprogram által készített meghajtók egyetlen csomagban vannak elküldve, és hogy a csomaghoz egyetlen egyedi nyomkövetési szám tartozik. Egyetlen csomagban ne egyesítse a különböző Azure-beli importálási feladatok részeként előkészített meghajtókat.

## <a name="update-shipping-details-on-the-azure-import-job"></a>A szállítási adatok frissítése az Azure importálási feladatban

Az alábbi eljárás az Azure importálási feladat szállítási adatait frissíti. Ez az információ a következőkről tartalmaz részleteket:

* a lemezeket az Azure-ba kézbesítő szállító neve
* a lemezek visszaszállítási adatainak visszaküldése

   1. Jelentkezzen be az Azure-előfizetésbe.
   2. A főmenüben válassza a **minden szolgáltatás** lehetőséget, majd a minden szolgáltatás párbeszédpanelen írja be az importálás értéket. Amikor megjelenik az **importálási/exportálási feladatok**, válassza ki azt.
       ![Szállítási adatok megadása](./media/backup-azure-backup-server-import-export/search-import-job.png)

       Megnyílik az **importálási/exportálási feladatok** menü, és megjelenik a kiválasztott előfizetés összes importálási/exportálási feladatának listája.

   3. Ha több előfizetéssel rendelkezik, ügyeljen arra, hogy kiválassza a biztonsági másolatok importálásához használt előfizetést. Ezután válassza ki az újonnan létrehozott importálási feladatot a részletek megnyitásához.

       ![Szállítási információk áttekintése](./media/backup-azure-backup-server-import-export/import-job-found.png)

   4. Az importálási feladathoz tartozó beállítások menüben válassza a **szállítási adatok kezelése** lehetőséget, és adja meg a visszaszállítás részleteit.

       ![Szállítási információk tárolása](./media/backup-azure-backup-server-import-export/shipping-info.png)

   5. Ha a szállítási szolgáltató nyomon követi a nyomkövetési számot, válassza ki a szalagcímet az Azure importálási feladatok áttekintése lapon, és adja meg a következő adatokat:

      > [!IMPORTANT]
      > Győződjön meg arról, hogy az Azure importálási feladat létrehozásától számított két héten belül frissítve legyenek a szolgáltató adatai és a nyomkövetési azonosító szám. Ha két héten belül nem tudja ellenőrizni ezeket az adatokat, a rendszer törli a feladatot, és nem dolgozza fel a meghajtókat.

      ![A feladatok áttekintése](./media/backup-azure-backup-server-import-export/job-overview.png)

      ![Követési információk](./media/backup-azure-backup-server-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>A meghajtók feldolgozásának ideje

Az Azure-beli importálási feladatok feldolgozásához szükséges idő mennyisége változó. A folyamat ideje olyan tényezőktől függ, mint a szállítási idő, a feladattípus, a másolt adatok típusa és mérete, valamint a megadott lemezek mérete. Az Azure import/export szolgáltatás nem rendelkezik SLA-val. A lemezek fogadása után a szolgáltatás arra törekszik, hogy a biztonsági másolat adatait 7 – 10 nap múlva fejezze be az Azure Storage-fiókba. A következő szakasz azt ismerteti, hogyan figyelheti az Azure importálási feladatok állapotát.

### <a name="monitor-azure-import-job-status"></a>Azure-beli importálási feladatok állapotának figyelése

Az importálási feladat állapotát a Azure Portal figyelheti az **importálási/exportálási feladatok** lapra, és kiválaszthatja a feladatát. Az importálási feladatok állapotával kapcsolatos további információkért tekintse meg a [tároló importálása exportálási szolgáltatásról](../import-export/storage-import-export-service.md) szóló cikket.

### <a name="complete-the-workflow"></a>A munkafolyamat befejezése

Az importálási feladatok befejeződése után a kezdeti biztonsági mentési adatok elérhetők a Storage-fiókban. A következő ütemezett biztonsági mentés időpontjában a Azure Backup átmásolja az adatok tartalmát a Storage-fiókból a Recovery Services-tárolóba.

A következő ütemezett online replika-létrehozási feladat időpontjában Data Protection Manager a kezdeti biztonsági másolaton végzi a növekményes biztonsági mentést.

## <a name="next-steps"></a>Következő lépések

* Az Azure import/export szolgáltatás munkafolyamatával kapcsolatos kérdésekért lásd: [a Microsoft Azure import/export szolgáltatás használata az adatok blob Storage-tárolóba történő átviteléhez](../import-export/storage-import-export-service.md).
