---
title: Offline biztonsági mentés az Azure import/export szolgáltatással
description: Ebből a témakörből megtudhatja, hogy az Azure import/export szolgáltatással hogyan küldhet adatküldést a hálózatról a Azure Backup használatával. Ez a cikk az Azure import/export szolgáltatással végzett kezdeti biztonsági mentési információ kapcsolat nélküli kiosztását ismerteti.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 3ea470c2e732b7e0ef46e9e5fa78c744aa30c955
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98704363"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Offline biztonsági mentési munkafolyamat a Azure Backup

A Azure Backup számos beépített hatékonyságot biztosít, amelyek a hálózati és tárolási költségeket az Azure-ba irányuló összes adat kezdeti teljes biztonsági mentése során megtakarítják. A kezdeti teljes biztonsági mentések általában nagy mennyiségű adat átvitelét igénylik, és nagyobb hálózati sávszélességet igényelnek, ha az azokat követő biztonsági mentések során csak a különbözeteket/növekményeket továbbítják. Az offline előkészítési folyamat során a Azure Backup lemezek használatával töltheti fel az offline biztonsági mentési adatok az Azure-ba.

Az Azure Backup offline-előkészítési folyamat szorosan integrálva van az [Azure import/export szolgáltatással](../import-export/storage-import-export-service.md). Ezzel a szolgáltatással a kezdeti biztonsági mentési adatok átvitele az Azure-ba lemezek használatával végezhető el. Ha terabájtos (TBs) típusú kezdeti biztonsági mentési adatról van szükség, amelyet nagy késésű és alacsony sávszélességű hálózaton kell átvinni, az offline előkészítési munkafolyamattal elküldheti a kezdeti biztonsági másolatot az Azure-adatközpontba egy vagy több merevlemezen. A következő ábra áttekintést nyújt a munkafolyamat lépéseiről.

  ![Az offline importálás munkafolyamat-folyamatának áttekintése](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Az offline biztonsági mentési folyamat a következő lépéseket foglalja magában:

1. A biztonsági mentési adatok hálózaton keresztüli elküldése helyett a biztonsági mentési adatokat egy átmeneti helyre kell írni.
1. A *AzureOfflineBackupDiskPrep* segédprogrammal írhatja be az előkészítési helyen található adattípust egy vagy több SATA-lemezre.
1. Az előkészítő munka részeként a *AzureOfflineBackupDiskPrep* segédprogram létrehoz egy Azure importálási feladatot. Küldje el a SATA-meghajtókat a legközelebbi Azure-adatközpontba, és hivatkozzon az importálási feladatokra a tevékenységek összekapcsolásához.
1. Az Azure-adatközpontban a lemezeken lévő adattárakat egy Azure Storage-fiókba másolja a rendszer.
1. Azure Backup a biztonsági mentési adatokat a Storage-fiókból a Recovery Services-tárolóba másolja, és a növekményes biztonsági mentések ütemezve lesznek.

## <a name="supported-configurations"></a>Támogatott konfigurációk

Az alábbi Azure Backup szolgáltatások vagy munkaterhelések támogatják az offline biztonsági mentés használatát a következőhöz:

> [!div class="checklist"]
>
> * Fájlok és mappák biztonsági mentése a Microsoft Azure Recovery Services-(MARS-) ügynökkel, más néven Azure Backup ügynök.
> * A számítási feladatok és fájlok biztonsági mentése a System Center Data Protection Manager (DPM) szolgáltatással.
> * Az összes munkaterhelés és fájl biztonsági mentése Microsoft Azure Backup-kiszolgálóval.

   > [!NOTE]
   > Az offline biztonsági mentés nem támogatott a rendszerállapot-biztonsági másolatok számára a Azure Backup ügynök használatával.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Előfeltételek

  > [!NOTE]
  > A következő előfeltételek és munkafolyamatok csak a fájlok és mappák offline biztonsági mentésére vonatkoznak a [legújabb Azure Recovery Services ügynök](https://aka.ms/azurebackup_agent)használatával. A System Center DPM vagy a Azure Backup Servert használó munkaterhelések offline biztonsági mentésének végrehajtásához tekintse meg a [DPM és Azure Backup Server offline biztonsági mentési munkafolyamatát](backup-azure-backup-server-import-export.md).

Az offline biztonsági mentési munkafolyamat elindítása előtt végezze el a következő előfeltételeket:

* Hozzon létre egy [Recovery Services](backup-azure-recovery-services-vault-overview.md)-tárolót. Tár létrehozásához kövesse az Recovery Services-tároló [létrehozása](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)című témakör lépéseit.
* Győződjön meg arról, hogy csak a [Azure Backup ügynök legújabb verziója](https://aka.ms/azurebackup_agent) van telepítve a Windows Serverre vagy a Windows-ügyfélre, ha van ilyen, és a számítógép regisztrálva van a Recovery Services-tárolóban.
* Azure PowerShell 3.7.0 van szükség a Azure Backup ügynököt futtató számítógépen. Töltse le és [telepítse a Azure PowerShell 3.7.0 verzióját](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* A Azure Backup ügynököt futtató számítógépen ellenőrizze, hogy a Microsoft Edge vagy az Internet Explorer 11 telepítve van-e, és hogy a JavaScript engedélyezve van-e.
* Hozzon létre egy Azure Storage-fiókot a Recovery Services-tárolóval megegyező előfizetésben.
* Győződjön meg arról, hogy rendelkezik a [szükséges engedélyekkel](../active-directory/develop/howto-create-service-principal-portal.md) a Azure Active Directory alkalmazás létrehozásához. Az offline biztonsági mentési munkafolyamat létrehoz egy Azure Active Directory alkalmazást az Azure Storage-fiókhoz társított előfizetésben. Az alkalmazás célja, hogy biztonságos és hatókörrel rendelkező Azure Backup biztosítson az Azure import/export szolgáltatáshoz, amely az offline biztonsági mentési munkafolyamathoz szükséges.
* Regisztrálja a *Microsoft. ImportExport* erőforrás-szolgáltatót az Azure Storage-fiókot tartalmazó előfizetéssel. Az erőforrás-szolgáltató regisztrálása:
    1. A főmenüben válassza az **előfizetések** lehetőséget.
    1. Ha több előfizetésre is feliratkozott, válassza ki az offline biztonsági mentéshez használni kívánt előfizetést. Ha csak egy előfizetést használ, megjelenik az előfizetése.
    1. Az előfizetés menüben válassza az **erőforrás-szolgáltatók** lehetőséget a szolgáltatók listájának megtekintéséhez.
    1. A szolgáltatók listájában görgessen le a *Microsoft. ImportExport* elemre. Ha az **állapot** **NotRegistered**, válassza a **regisztráció** lehetőséget.

        ![Az erőforrás-szolgáltató regisztrálása](./media/backup-azure-backup-import-export/registerimportexport.png)

* A rendszer létrehoz egy átmeneti helyet, amely lehet hálózati megosztás vagy a számítógép belső vagy külső meghajtója, amely elegendő lemezterülettel rendelkezik a kezdeti másolat tárolásához. Ha például egy 500 GB-os fájlkiszolgáló biztonsági mentését kívánja végezni, győződjön meg arról, hogy az átmeneti területen legalább 500 GB. (A tömörítés miatt kisebb mennyiség van használatban.)
* Ha lemezeket küld az Azure-ba, csak a 2,5 hüvelykes SSD-vagy 2,5-es vagy 3,5 hüvelykes SATA II/III-merevlemezeket használja. A merevlemezeket akár 10 TB-ig is használhatja. Az [Azure import/export szolgáltatás dokumentációjában](../import-export/storage-import-export-requirements.md#supported-hardware) keresse meg a szolgáltatás által támogatott meghajtók legújabb készletét.
* A SATA-meghajtóknak csatlakoztatva kell lennie egy számítógéphez (ez a *másolási számítógép*), ahonnan a biztonsági mentési adatok másolata az átmeneti helyről a SATA-meghajtókra történik. Győződjön meg arról, hogy a BitLocker engedélyezve van a másolási számítógépen.

## <a name="workflow"></a>Munkafolyamat

Ez a szakasz az offline biztonsági mentési munkafolyamatot ismerteti, hogy az adatai továbbíthatók legyenek egy Azure-adatközpontba, és fel legyenek töltve az Azure Storage szolgáltatásba. Ha kérdése van az importálási szolgáltatással vagy a folyamat bármely aspektusával kapcsolatban, tekintse meg az [Azure import/export szolgáltatás áttekintés dokumentációját](../import-export/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Offline biztonsági mentés indítása

1. Ha a Recovery Services ügynökön készít biztonsági másolatot, ez a lap jelenik meg.

    ![Importálási oldal](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

1. Válassza az átvitel lehetőséget a **saját lemezek használatával**.

    > [!NOTE]
    > A kezdeti biztonsági mentési adatok kapcsolat nélküli átviteléhez használja a Azure Data Box lehetőséget. Ez a beállítás elmenti a saját Azure-kompatibilis lemezek beszerzéséhez szükséges erőfeszítést. Biztosítja a Microsoft által védett, biztonságos és tamperproof Azure Data Box eszközöket, amelyekhez közvetlenül a Recovery Services-ügynök írhat be biztonsági mentési adatkészleteket.

1. Kattintson a **Tovább gombra**, és gondosan töltse ki a mezőket.

    ![Adja meg a lemez adatait](./media/backup-azure-backup-import-export/your-disk-details.png)

   A kitöltendő mezők a következők:

    * **Átmeneti hely**: az az ideiglenes tárolási hely, ahová a rendszer a kezdeti biztonsági másolatot írja. Előfordulhat, hogy az előkészítési hely egy hálózati megosztáson vagy egy helyi számítógépen található. Ha a számítógép és a forrásszámítógép másolása eltérő, akkor az előkészítési hely teljes hálózati elérési útját kell megadni.
    * **Azure Resource Manager Storage-fiók**: a Resource Manager típusú Storage-fiók (általános célú v1 vagy általános célú v2) neve bármely Azure-előfizetésben.
    * **Azure Storage-tároló**: a cél blob Storage-tároló neve abban az Azure Storage-fiókban, ahol a biztonsági mentési adatmennyiséget importálja a rendszer a Recovery Services-tárolóba való másolás előtt.
    * **Azure-előfizetés azonosítója**: annak az Azure-előfizetésnek az azonosítója, amelyhez az Azure Storage-fiókot létrehozták.
    * **Azure importálási feladatok neve**: az az egyedi név, amellyel az Azure import/export szolgáltatás és Azure Backup nyomon követheti a lemezeken az Azure-ba továbbított adatok átvitelét.
  
   A mezők kitöltése után válassza a **tovább** lehetőséget. Mentse az **előkészítési helyet** és az **Azure-beli importálási feladatok nevét** . A lemezek előkészítéséhez szükséges.

1. Ha a rendszer kéri, jelentkezzen be az Azure-előfizetésbe. Be kell jelentkeznie, hogy Azure Backup létre tudja hozni a Azure Active Directory alkalmazást. Adja meg az Azure import/export szolgáltatás eléréséhez szükséges engedélyeket.

    ![Azure-előfizetés bejelentkezési lapja](./media/backup-azure-backup-import-export/azure-login.png)

1. Fejezze be a munkafolyamatot. A Azure Backup-ügynök konzolján válassza a **biztonsági mentés most** lehetőséget.

    ![Biztonsági mentés most](./media/backup-azure-backup-import-export/backupnow.png)

1. A varázsló **megerősítés** lapján válassza a **biztonsági mentés** lehetőséget. A kezdeti biztonsági mentés a telepítés részeként íródik az átmeneti területre.

   ![Erősítse meg, hogy most már készen áll a biztonsági mentésre](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    A művelet befejeződése után az előkészítési hely készen áll a lemez-előkészítésre.

   ![Biztonsági mentés most varázsló lap](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>A SATA-meghajtók előkészítése és a szállítás az Azure-ba

A *AzureOfflineBackupDiskPrep* segédprogram előkészíti a legközelebbi Azure-adatközpontba küldendő SATA-meghajtókat. Ez a segédprogram a Azure Backup ügynök telepítési könyvtárában érhető el a következő elérési úton:

```*\Microsoft Azure Recovery Services Agent\Utils\\*```

1. Nyissa meg a könyvtárat, és másolja a *AzureOfflineBackupDiskPrep* könyvtárat egy másik számítógépre, AMELYEN a SATA-meghajtók csatlakoznak. A csatlakoztatott SATA meghajtókkal rendelkező számítógépen ügyeljen a következőre:

   * A másolási számítógép a (z) "kapcsolat nélküli biztonsági mentés kezdeményezése" szakaszban található munkafolyamatban megadott hálózati elérési út használatával férhet hozzá az offline tesztelési munkafolyamat előkészítési helyéhez.
   * A BitLocker engedélyezve van a másolási számítógépen.
   * Azure PowerShell 3.7.0 telepítve van.
   * A legújabb kompatibilis böngészők (Microsoft Edge vagy Internet Explorer 11) telepítve vannak, és a JavaScript engedélyezve van.
   * A másolási számítógép elérheti a Azure Portal. Ha szükséges, a másolási számítógép lehet ugyanaz, mint a forrásszámítógép.

     > [!IMPORTANT]
     > Ha a forrásszámítógép virtuális gép, akkor a számítógépnek egy másik fizikai kiszolgálónak vagy ügyfélszámítógépnek kell lennie a forrásszámítógépen.

1. Nyisson meg egy rendszergazda jogú parancssort a másolási számítógépen a *AzureOfflineBackupDiskPrep* segédprogram címtárával az aktuális könyvtárként. Futtassa az alábbi parancsot:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Paraméter | Leírás |
    | --- | --- |
    | s: &lt; *átmeneti hely elérési útja*&gt; |Ez a kötelező bemenet arra szolgál, hogy megadja a munkafolyamatban megadott átmeneti hely elérési útját a "kapcsolat nélküli biztonsági mentés indítása" szakaszban. |
    | p: &lt; *a PublishSettingsFile elérési útja*&gt; |Ez a választható bemenet az Azure publish Settings fájl elérési útjának megadására szolgál.  |

    A parancs futtatásakor a segédprogram kéri az Azure importálási feladatoknak a kiválasztását, amely megfelel az előkészíteni kívánt meghajtóknak. Ha csak egyetlen importálási feladattípus van társítva a megadott átmeneti helyhez, a következőhöz hasonló oldal jelenik meg.

    ![Azure Disk-előkészítő eszköz bemenete](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

1. Adja meg a meghajtóbetűjelet az Azure-ba való átvitelre előkészíteni kívánt csatlakoztatott lemez záró kettőspontja nélkül.
1. Ha a rendszer kéri, adja meg a meghajtó formázásának megerősítését.
1. A rendszer felszólítja, hogy jelentkezzen be az Azure-előfizetésbe. Adja meg hitelesítő adatait.

    ![Azure-előfizetés bejelentkezés](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Az eszköz ezután megkezdi a lemez előkészítését és a biztonsági mentési Adatmásolást. Előfordulhat, hogy további lemezeket kell csatlakoztatnia, amikor az eszköz kéri, ha a megadott lemez nem rendelkezik elegendő hellyel a biztonsági mentési információkhoz. <br/>

    Az eszköz sikeres végrehajtása végén a parancssor három információt nyújt:

   1. Egy vagy több megadott lemez készen áll az Azure-ba történő szállításra.
   1. A rendszer megerősíti, hogy az importálási feladatot létrehozták. Az importálási feladatokhoz a megadott nevet kell használnia.
   1. Az eszköz az Azure-adatközponthoz tartozó szállítási címet jeleníti meg.

      ![Az Azure Disk-előkészítés befejeződött](./media/backup-azure-backup-import-export/console2.png)<br/>

1. A parancs végrehajtásának végén frissítheti a szállítási adatokat.

1. A lemezek szállítása az eszköz által biztosított címnek. Tartsa meg a követési számot későbbi használatra.

   > [!IMPORTANT]
   > Két Azure-beli importálási feladat nem rendelkezhet ugyanazzal a követési számmal. Győződjön meg arról, hogy egyetlen Azure-beli importálási feladatban a segédprogram által készített meghajtók egyetlen csomagban vannak elküldve, és hogy a csomaghoz egyetlen egyedi nyomkövetési szám tartozik. Ne egyesítse a különálló Azure-beli importálási feladatok részeként előkészített meghajtókat egyetlen csomagban.

## <a name="update-shipping-details-on-the-azure-import-job"></a>A szállítási adatok frissítése az Azure importálási feladatban

Az alábbi eljárás az Azure importálási feladat szállítási adatait frissíti. Ez az információ a következőkről tartalmaz részleteket:

* A lemezeket az Azure-ba kézbesítő szolgáltató neve.
* A lemezek visszaküldési adatainak visszaadása.

1. Jelentkezzen be az Azure-előfizetésbe.
1. A főmenüben válassza a **minden szolgáltatás** lehetőséget. A **minden szolgáltatás** párbeszédpanelen írja be az **Importálás** értéket. Amikor megjelenik az **importálási/exportálási feladatok**, válassza ki azt.

    ![Adja meg a szállítási adatokat](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Megnyílik az **importálási/exportálási feladatok** menü, és megjelenik a kiválasztott előfizetés összes importálási/exportálási feladatának listája.

1. Ha több előfizetéssel rendelkezik, válassza ki a biztonsági másolatok importálásához használt előfizetést. Ezután válassza ki az újonnan létrehozott importálási feladatot a részletek megnyitásához.

    ![Szállítási információk áttekintése](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

1. Az importálási feladathoz tartozó **Beállítások** menüben válassza a **szállítási adatok kezelése** lehetőséget. Adja meg a visszáru szállítás részleteit.

    ![Szállítási információk tárolása](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

1. Ha a szállítási szolgáltató nyomon követi a nyomkövetési számot, válassza ki a szalagcímet az Azure importálási feladatok áttekintése lapon, és adja meg a következő adatokat.

   > [!IMPORTANT]
   > Győződjön meg arról, hogy az Azure importálási feladat létrehozásától számított két héten belül frissítve legyenek a szolgáltató adatai és a nyomkövetési azonosító szám. Ha két héten belül nem tudja ellenőrizni ezeket az adatokat, a rendszer törli a feladatot, és nem dolgozza fel a meghajtókat.

   ![Nyomkövetési adatok frissítési riasztása](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Szállítói információ és követési szám](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>A meghajtók feldolgozásának ideje

Az Azure-beli importálási feladatok feldolgozásához szükséges idő mennyisége változó. A feldolgozási idő olyan tényezőkön alapul, mint a szállítási idő, a feladattípus, a másolt adatok típusa és mérete, valamint a megadott lemezek mérete. Az Azure import/export szolgáltatás nem rendelkezik SLA-val. A lemezek fogadása után a szolgáltatás arra törekszik, hogy az Azure Storage-fiókba 7 – 10 nap elteltével fejezze be a biztonsági másolat adatait.

### <a name="monitor-azure-import-job-status"></a>Azure-beli importálási feladatok állapotának figyelése

Az importálási feladatok állapotát a Azure Portal követheti nyomon. Nyissa meg az **importálási/exportálási feladatok** lapot, és válassza ki a feladatot. Az importálási feladatok állapotával kapcsolatos további információkért lásd: [Mi az az Azure import/export szolgáltatás?](../import-export/storage-import-export-service.md).

### <a name="finish-the-workflow"></a>A munkafolyamat befejezése

Az importálási feladatok sikeres befejezését követően a kezdeti biztonsági mentési adatok elérhetők a Storage-fiókban. A következő ütemezett biztonsági mentés időpontjában a Azure Backup átmásolja az adatok tartalmát a Storage-fiókból a Recovery Services-tárolóba.

   ![Adatmásolás Recovery Services-tárolóba](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

A következő ütemezett biztonsági mentés időpontjában a Azure Backup növekményes biztonsági mentést hajt végre.

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A kezdeti biztonsági mentés befejezését követően biztonságosan törölheti az Azure Storage-tárolóba importált és az átmeneti helyen található biztonsági mentési adataikat.

## <a name="next-steps"></a>Következő lépések

* Az Azure import/export szolgáltatás munkafolyamatával kapcsolatos kérdésekért lásd: [a Microsoft Azure import/export szolgáltatás használata az adatok blob Storage-tárolóba történő átviteléhez](../import-export/storage-import-export-service.md).
