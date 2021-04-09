---
title: Offline biztonsági mentés a Data Protection Manager (DPM) és a Microsoft Azure Backup Server (MABS) szolgáltatáshoz – korábbi verziók
description: A Azure Backup segítségével az Azure import/export szolgáltatással küldhet adathálózatot a hálózatról. Ez a cikk a DPM és a Azure Backup Server korábbi verzióihoz tartozó offline biztonsági mentési munkafolyamatot ismerteti.
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 0405ab66b7714f00349419e94bb064267ca711a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98702185"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server-previous-versions"></a>DPM és Azure Backup Server offline biztonsági mentési munkafolyamata (korábbi verziók)

>[!IMPORTANT]
>Ezek a lépések a DPM 2019 RTM-es és korábbi verzióira, valamint a MABS v3 RTM és korábbi verziókra vonatkoznak.

A Azure Backup számos beépített hatékonyságot biztosít, amelyek a hálózati és tárolási költségeket az Azure-ba irányuló összes adat kezdeti teljes biztonsági mentése során megtakarítják. A kezdeti teljes biztonsági mentések általában nagy mennyiségű adat átvitelét igénylik, és nagyobb hálózati sávszélességet igényelnek, ha az azokat követő biztonsági mentések során csak a különbözeteket/növekményeket továbbítják. Azure Backup tömöríti a kezdeti biztonsági mentéseket. Az offline kivetés folyamatán keresztül a Azure Backup lemezek használatával feltölthetik a tömörített kezdeti biztonsági mentési adatok az Azure-ba.

Azure Backup kapcsolat nélküli előkészítési folyamata szorosan integrálva van az [Azure import/export szolgáltatással](../import-export/storage-import-export-service.md). Ezzel a szolgáltatással lemezeket vihet át az Azure-ba. Ha terabájt (TBs) típusú kezdeti biztonsági mentési adat szükséges, amelyet nagy késésű és alacsony sávszélességű hálózaton kell átvinni, az offline előkészítési munkafolyamattal egy vagy több merevlemezen is elküldheti az Azure-adatközpontba a kezdeti biztonsági másolatot. Ez a cikk áttekintést nyújt, és további lépéseket tesz a munkafolyamat a System Center Data Protection Manager (DPM) és a Microsoft Azure Backup Server (MABS) rendszerhez való befejezéséhez.

> [!NOTE]
> A Microsoft Azure Recovery Services-(MARS-) ügynök offline biztonsági mentésének folyamata különbözik a DPM és a MABS. Az offline biztonsági mentés a MARS-ügynökkel való használatával kapcsolatban lásd: [Offline biztonsági mentési munkafolyamat a Azure Backup](backup-azure-backup-import-export.md). Az offline biztonsági mentés nem támogatott a rendszerállapot-biztonsági másolatok számára a Azure Backup ügynök használatával.
>

## <a name="overview"></a>Áttekintés

Az Azure Backup és az Azure import/export szolgáltatás offline kicsomagolási funkciójának használatával egyszerűen tölthetők fel az adatok az Azure-ba a lemezekkel. Az offline biztonsági mentési folyamat az alábbi lépéseket foglalja magában:

> [!div class="checklist"]
>
> * A biztonsági mentési adat a hálózaton keresztüli küldés helyett átmeneti helyre íródik.
> * Az előkészítési helyen lévő adatai ezután egy vagy több SATA-lemezre íródnak a *AzureOfflineBackupDiskPrep* segédprogram használatával.
> * A segédprogram automatikusan létrehoz egy Azure-beli importálási feladatot.
> * Ezután a rendszer elküldi a SATA-meghajtókat a legközelebbi Azure-adatközpontba.
> * Miután befejeződött a biztonsági mentési adatok feltöltése az Azure-ba, Azure Backup másolja a biztonsági mentési adatokat a Backup-tárolóba, és a növekményes biztonsági mentések ütemezve lesznek.

## <a name="supported-configurations"></a>Támogatott konfigurációk

Az offline biztonsági mentés a Azure Backup összes olyan üzemi modelljénél támogatott, amely a helyszíni adatok biztonsági mentését végzi a Microsoft felhőbe. Ezek a modellek a következők:

> [!div class="checklist"]
>
> * Fájlok és mappák biztonsági mentése a MARS-ügynökkel vagy a Azure Backup ügynökkel.
> * Az összes munkaterhelés és fájl biztonsági mentése a DPM-mel.
> * Az összes munkaterhelés és fájl biztonsági mentése a MABS-mel.

>[!NOTE]
>Az Azure CSP-előfizetések nem támogatottak a DPM 2019 RTM és korábbi verziók esetében, valamint a MABS v3 RTM és korábbi verziók esetében. Az online biztonsági mentések továbbra is támogatottak a hálózaton.

## <a name="prerequisites"></a>Előfeltételek

Az offline biztonsági mentési munkafolyamat elindítása előtt győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

* A rendszer létrehozta [Recovery Services](backup-azure-recovery-services-vault-overview.md) -tárolót. A létrehozásához kövesse az Recovery Services-tároló [létrehozása](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault) című témakör lépéseit.
* Egy Azure Backup ügynök vagy MABS vagy DPM telepítve van a Windows Serverre vagy egy Windows-ügyfélre, ha van ilyen, és a számítógép regisztrálva van a Recovery Services-tárolóban. Győződjön meg arról, hogy a Azure Backup csak a [legújabb verzióját](https://go.microsoft.com/fwlink/?linkid=229525) használja.
* [Töltse le az Azure publish Settings fájlt](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) azon a számítógépen, amelyről biztonsági másolatot kíván készíteni az adatokról. Az előfizetés, amelyről letölti a közzétételi beállítások fájlját, különbözhet az Recovery Services-tárolót tartalmazó előfizetéstől. Ha az előfizetés szuverén Azure-felhőben található, akkor az Azure publish Settings fájl letöltéséhez használja az alábbi hivatkozásokat.

    | Szuverén felhő régiója | Az Azure közzétételi beállítások fájljának hivatkozása |
    | --- | --- |
    | Egyesült Államok | [Hivatkozás](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | Kína | [Hivatkozás](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* A Resource Manager-alapú üzemi modellel rendelkező Azure Storage-fiók olyan előfizetésben lett létrehozva, amelyről letöltötte a közzétételi beállítások fájlját. A Storage-fiókban hozzon létre egy új BLOB-tárolót, amely célként lesz használva.

  ![Storage-fiók létrehozása Resource Manager-fejlesztéssel](./media/offline-backup-dpm-mabs-previous-versions/storage-account-resource-manager.png)

* A rendszer létrehoz egy átmeneti helyet, amely lehet hálózati megosztás vagy a számítógép belső vagy külső meghajtója, amely elegendő lemezterülettel rendelkezik a kezdeti másolat tárolásához. Ha például egy 500 GB-os fájlkiszolgáló biztonsági mentését kívánja végezni, győződjön meg arról, hogy az átmeneti területen legalább 500 GB. (A tömörítés miatt kisebb mennyiség van használatban.)
* Az Azure-ba küldendő lemezek esetében ügyeljen arra, hogy csak a 2,5 hüvelykes SSD-vagy 2,5-es vagy 3,5 hüvelykes SATA II/III-alapú merevlemezek legyenek használatban. A merevlemezeket akár 10 TB-ig is használhatja. Az [Azure import/export szolgáltatás dokumentációjában](../import-export/storage-import-export-requirements.md#supported-hardware) keresse meg a szolgáltatás által támogatott meghajtók legújabb készletét.
* A SATA-meghajtóknak csatlakoztatva kell lennie egy számítógéphez (ez a *másolási számítógép*), ahonnan a biztonsági mentési adatok másolata az átmeneti helyről a SATA-meghajtókra történik. Győződjön meg arról, hogy a BitLocker engedélyezve van a másolási számítógépen.

## <a name="prepare-the-server-for-the-offline-backup-process"></a>A kiszolgáló előkészítése az offline biztonsági mentési folyamathoz

>[!NOTE]
> Ha nem találja a felsorolt segédprogramokat, például a *AzureOfflineBackupCertGen.exet* a Mars-ügynök telepítésekor, írja be a következőt: AskAzureBackupTeam@microsoft.com .

* Nyisson meg egy rendszergazda jogú parancssort a kiszolgálón, és futtassa a következő parancsot:

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    Az eszköz létrehoz egy Azure offline biztonsági mentési Active Directory alkalmazást, ha még nem létezik.

    Ha egy alkalmazás már létezik, akkor ez a végrehajtható fájl kéri a tanúsítvány manuális feltöltését az alkalmazásba a bérlőben. Az [ebben a szakaszban](#manually-upload-an-offline-backup-certificate) leírt lépéseket követve töltse fel manuálisan a tanúsítványt az alkalmazásba.

* A *AzureOfflineBackupCertGen.exe* eszköz létrehoz egy *OfflineApplicationParams.xml* fájlt. Másolja ezt a fájlt a kiszolgálóra a MABS vagy a DPM használatával.
* Telepítse a [legújabb Mars-ügynököt](https://aka.ms/azurebackup_agent) a DPM-példányra vagy a Azure Backup-kiszolgálóra.
* Regisztrálja a kiszolgálót az Azure-ban.
* Futtassa az alábbi parancsot:

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname to be used for offline backup>
    ```

* Az előző parancs létrehozza a fájlt `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml` .

## <a name="manually-upload-an-offline-backup-certificate"></a>Offline biztonsági mentési tanúsítvány manuális feltöltése

A következő lépésekkel manuálisan feltöltheti az offline biztonsági mentési tanúsítványt egy korábban létrehozott Azure Active Directory alkalmazásba az offline biztonsági mentéshez.

1. Jelentkezzen be az Azure Portalra.
1. Nyissa meg **Azure Active Directory**  >  **Alkalmazásregisztrációk**.
1. A **tulajdonban lévő alkalmazások** lapon keresse meg a megjelenítendő név formátumot tartalmazó alkalmazást `AzureOfflineBackup _<Azure User Id` .

    ![Alkalmazás megkeresése a birtokolt alkalmazások lapon](./media/offline-backup-dpm-mabs-previous-versions/owned-applications.png)

1. Válassza ki az alkalmazást. A bal oldali ablaktábla **kezelés** területén lépjen a **tanúsítványok & Secrets** elemre.
1. Előzetesen létező tanúsítványok vagy nyilvános kulcsok keresése. Ha nincs, az alkalmazás **Áttekintés** lapján található **Törlés** gombra kattintva biztonságosan törölheti az alkalmazást. Ezután újra elvégezheti a [kiszolgáló előkészítésének lépéseit az offline biztonsági mentési](#prepare-the-server-for-the-offline-backup-process) folyamathoz, és kihagyhatja a következő lépéseket. Ellenkező esetben folytassa a következő lépésekkel a DPM-példány vagy Azure Backup-kiszolgáló, amelyen az offline biztonsági mentést szeretné konfigurálni.
1. Az **Indítás** – **Futtatás** mezőbe írja be a következőt: *Certlm. msc*. A **tanúsítványok – helyi** számítógép ablakban válassza a **tanúsítványok – helyi számítógép**  >  **személyes** lapja lehetőséget. Keresse meg a tanúsítványt a névvel `CB_AzureADCertforOfflineSeeding_<ResourceId>` .
1. Válassza ki a tanúsítványt, kattintson a jobb gombbal a **minden feladat** elemre, majd válassza az **Exportálás** lehetőséget, titkos kulcs nélkül,. cer formátumban.
1. Nyissa meg az Azure offline Backup alkalmazást a Azure Portal.
1. Válassza a tanúsítványok **kezelése**  >  **& titkok**  >  **feltöltési tanúsítványa** lehetőséget. Töltse fel az előző lépésben exportált tanúsítványt.

    ![A tanúsítvány feltöltése](./media/offline-backup-dpm-mabs-previous-versions/upload-certificate.png)

1. A kiszolgálón nyissa meg a beállításjegyzéket úgy, hogy beírja a **Regedit parancsot** a Futtatás ablakba.
1. Lépjen a *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider* beállításjegyzékbeli bejegyzéshez.
1. Kattintson a jobb gombbal a **CloudBackupProvider** elemre, és adjon hozzá egy új karakterlánc-értéket a névvel `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Az Azure felhasználói AZONOSÍTÓjának megkereséséhez hajtsa végre a következő lépések egyikét:
    >
    >* Az Azure-hoz csatlakoztatott PowerShellben futtassa a `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as appears in the portal"` parancsot.
    >* Nyissa meg a beállításjegyzék elérési útját `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;` .

1. Kattintson a jobb gombbal az előző lépésben hozzáadott sztringre, majd válassza a **módosítás** lehetőséget. Az érték mezőben adja meg a 7. lépésben exportált tanúsítvány ujjlenyomatát. Ez után válassza az **OK** gombot.
1. Az ujjlenyomat értékének lekéréséhez kattintson duplán a tanúsítványra. Válassza a **részletek** lapot, és görgessen lefelé, amíg meg nem jelenik az ujjlenyomat mező. Válassza az **ujjlenyomat** lehetőséget, és másolja ki az értéket.

    ![Érték másolása az ujjlenyomat mezőből](./media/offline-backup-dpm-mabs-previous-versions/thumbprint-field.png)

1. Folytassa a [munkafolyamat](#workflow) szakaszával az offline biztonsági mentési folyamat folytatásához.

## <a name="workflow"></a>Munkafolyamat

Az ebben a szakaszban található információk segítségével befejezheti az offline biztonsági mentési munkafolyamatot, így az adatok továbbíthatók egy Azure-adatközpontba, és feltölthetők az Azure Storage-ba. Ha kérdése van az importálási szolgáltatással vagy a folyamat bármely aspektusával kapcsolatban, tekintse meg a korábban hivatkozott [importálási szolgáltatás áttekintése dokumentációt](../import-export/storage-import-export-service.md) .

### <a name="initiate-offline-backup"></a>Offline biztonsági mentés indítása

1. Amikor ütemezett biztonsági mentést készít, a következő oldal jelenik meg a Windows Serverben, a Windows-ügyfélben vagy a DPM.

    ![Importálási oldal](./media/offline-backup-dpm-mabs-previous-versions/offline-backup-screen-inputs.png)

    Itt látható a DPM megfelelő lapja. <br/>

    ![DPM és Azure Backup Server importálási lap](./media/offline-backup-dpm-mabs-previous-versions/dpm-offline.png)

    A kitöltendő mezők a következők:

   * **Átmeneti hely**: az az ideiglenes tárolási hely, ahová a rendszer a kezdeti biztonsági másolatot írja. Előfordulhat, hogy az előkészítési hely egy hálózati megosztáson vagy egy helyi számítógépen található. Ha a számítógép és a forrásszámítógép másolása eltérő, akkor az előkészítési hely teljes hálózati elérési útját kell megadni.
   * **Azure közzétételi beállítások**: a közzétételi beállítások fájljának helyi elérési útja.
   * **Azure importálási feladatok neve**: az az egyedi név, amellyel az Azure import/export szolgáltatás és Azure Backup nyomon követheti a lemezeken az Azure-ba továbbított adatok átvitelét.
   * **Azure-előfizetés azonosítója**: annak az előfizetésnek az Azure-ELŐfizetési azonosítója, amelyből letöltötte az Azure közzétételi beállítások fájlját.
   * **Azure Storage-fiók**: az Azure-közzétételi beállítási fájlhoz társított Azure-előfizetéshez tartozó Storage-fiók neve.
   * **Azure Storage-tároló**: annak az Azure Storage-fióknak a neve, amelybe a biztonsági mentési adatmennyiséget importálja.

   Mentse az **előkészítési helyet** és az **Azure-beli importálási feladathoz megadott nevet** . A lemezek előkészítéséhez szükséges.

1. Fejezze be a munkafolyamatot. Az offline biztonsági másolat elindításához válassza a **biztonsági mentés most** lehetőséget a Azure Backup ügynök felügyeleti konzolján. Ennek a lépésnek a részeként a kezdeti biztonsági mentés az átmeneti területre íródik.

    ![Biztonsági mentés most](./media/offline-backup-dpm-mabs-previous-versions/backup-now.png)

    Ha a megfelelő munkafolyamatot szeretné befejezni a DPM vagy a Azure Backup Serverban, kattintson a jobb gombbal a **védelmi csoportra**. Válassza a **helyreállítási pont létrehozása** lehetőséget. Ezután válassza az **online védelem** lehetőséget.

    ![DPM és MABS biztonsági mentés most](./media/offline-backup-dpm-mabs-previous-versions/dpm-backup-now.png)

    A művelet befejeződése után az előkészítési hely készen áll a lemez-előkészítésre.

    ![Biztonsági mentés állapota](./media/offline-backup-dpm-mabs-previous-versions/op-backup-now.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>A SATA-meghajtók előkészítése és a szállítás az Azure-ba

A *AzureOfflineBackupDiskPrep* segédprogram a legközelebbi Azure-adatközpontba eljuttatott SATA-meghajtók előkészítésére szolgál. Ez a segédprogram a Recovery Services ügynök telepítési könyvtárában érhető el a következő elérési úton:

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. Nyissa meg a könyvtárat, és másolja a *AzureOfflineBackupDiskPrep* könyvtárat arra a másolási számítógépre, amelyen az előkészíteni kívánt SATA-meghajtók csatlakoztatva vannak. Győződjön meg a következőket:

   * A másolási számítógép a (z) "kapcsolat nélküli biztonsági mentés kezdeményezése" szakaszban található munkafolyamatban megadott hálózati elérési út használatával férhet hozzá az offline tesztelési munkafolyamat előkészítési helyéhez.
   * A BitLocker engedélyezve van a másolási számítógépen.
   * A másolási számítógép elérheti a Azure Portal. Ha szükséges, a másolási számítógép lehet ugyanaz, mint a forrásszámítógép.

     > [!IMPORTANT]
     > Ha a forrásszámítógép egy virtuális gép, akkor a másolási számítógépként egy másik fizikai kiszolgálót vagy ügyfélszámítógépet kell használnia.

1. Nyisson meg egy rendszergazda jogú parancssort a másolási számítógépen a *AzureOfflineBackupDiskPrep* segédprogram címtárával az aktuális könyvtárként. Futtassa az alábbi parancsot:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Paraméter | Leírás |
    | --- | --- |
    | s: &lt; *átmeneti hely elérési útja*&gt; |Ez a kötelező bemenet arra szolgál, hogy megadja a munkafolyamatban megadott átmeneti hely elérési útját a "kapcsolat nélküli biztonsági mentés indítása" szakaszban. |
    | p: &lt; *a PublishSettingsFile elérési útja*&gt; |Ez a választható bemenet a "kapcsolat nélküli biztonsági mentés indítása" szakaszban a munkafolyamatban megadott Azure közzétételi beállítási fájl elérési útjának megadására szolgál. |

    > [!NOTE]
    > A &lt; AzurePublishSettingFile értékének elérési útja &gt; kötelező, ha a számítógép és a forrásoldali számítógép másolása eltérő.
    >
    >

    A parancs futtatásakor a segédprogram kéri az Azure importálási feladatoknak a kiválasztását, amely megfelel az előkészíteni kívánt meghajtóknak. Ha csak egyetlen importálási feladattípus van társítva a megadott átmeneti helyhez, a következőhöz hasonló oldal jelenik meg.

    ![Azure Disk-előkészítő eszköz bemenete](./media/offline-backup-dpm-mabs-previous-versions/azure-disk-preparation-tool-drive-input.png) <br/>

1. Adja meg a meghajtóbetűjelet az Azure-ba való átvitelre előkészíteni kívánt csatlakoztatott lemez záró kettőspontja nélkül. Ha a rendszer kéri, adja meg a meghajtó formázásának megerősítését.

    Az eszköz ezután megkezdi a lemez előkészítését és a biztonsági mentési Adatmásolást. Előfordulhat, hogy további lemezeket kell csatlakoztatnia, amikor az eszköz kéri, ha a megadott lemez nem rendelkezik elegendő hellyel a biztonsági mentési információkhoz. <br/>

    Az eszköz sikeres befejeződése után egy vagy több megadott lemez készen áll az Azure-ba történő szállításra. Az Azure-ban az "Offline biztonsági mentés kezdeményezése" című szakaszban megadott névvel létrehozott importálási feladatok is létrejönnek. Végül az eszköz megjeleníti a szállítási címet arra az Azure-adatközpontba, ahol a lemezeket el kell szállítani.

    ![Az Azure Disk-előkészítés befejeződött](./media/offline-backup-dpm-mabs-previous-versions/azure-disk-preparation-tool-success.png)<br/>

1. A parancs végrehajtásának végén a szállítási információk frissítésének lehetősége is megjelenik.

    ![Szállítási információ frissítése lehetőség](./media/offline-backup-dpm-mabs-previous-versions/update-shipping-utility.png)<br/>

1. A részleteket azonnal megadhatja. Az eszköz végigvezeti Önt a folyamaton, amely magában foglalja a bemeneti adatsorozatot. Ha nem rendelkezik olyan információkkal, mint a követési szám vagy a szállítással kapcsolatos egyéb adatok, akkor befejezheti a munkamenetet. A szállítási adatok későbbi frissítésének lépéseit ebben a cikkben találja.

1. A lemezek szállítása az eszköz által biztosított címnek. Tartsa meg a követési számot későbbi használatra.

   > [!IMPORTANT]
   > Két Azure-beli importálási feladat nem rendelkezhet ugyanazzal a követési számmal. Győződjön meg arról, hogy egyetlen Azure-beli importálási feladatban a segédprogram által készített meghajtók egyetlen csomagban vannak elküldve, és hogy a csomaghoz egyetlen egyedi nyomkövetési szám tartozik. Egyetlen csomagban ne egyesítse a különböző Azure-beli importálási feladatok részeként előkészített meghajtókat.

1. Ha rendelkezik a követési számmal kapcsolatos információval, nyissa meg a forrásoldali számítógépet, amely az importálási feladatok befejezésére vár. Futtassa az alábbi parancsot egy rendszergazda jogú parancssorban az *AzureOfflineBackupDiskPrep* segédprogram címtárával az aktuális könyvtárként.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   A következő parancsot igény szerint futtathatja egy másik számítógépről, például a másolási számítógépről, a *AzureOfflineBackupDiskPrep* segédprogram címtárával, amely az aktuális könyvtár.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Paraméter | Leírás |
    | --- | --- |
    | u | Ez a kötelező bemenet egy Azure-beli importálási feladathoz tartozó szállítási adatok frissítésére szolgál. |
    | s: &lt; *átmeneti hely elérési útja*&gt; | Ezt a kötelező bemenetet akkor kell használni, ha a parancs nem fut a forrásszámítógépen. Ezzel a beállítással megadhatja a munkafolyamatban megadott átmeneti hely elérési útját a "kapcsolat nélküli biztonsági mentés indítása" szakaszban. |
    | p: &lt; *a PublishSettingsFile elérési útja*&gt; | Ezt a kötelező bemenetet akkor kell használni, ha a parancs nem fut a forrásszámítógépen. Ezzel a beállítással megadhatja az Azure közzétételi beállítások fájljának elérési útját, amelyet a "kapcsolat nélküli biztonsági mentés indítása" szakaszban megadott a munkafolyamatban. |

    A segédprogram automatikusan észleli azt az importálási feladatot, amelyet a forrásszámítógép várakozik, vagy az átmeneti helyhez társított importálási feladatok, ha a parancs egy másik számítógépen fut. Ezután lehetőséget biztosít a szállítási információk frissítésére egy adatsorozaton keresztül.

    ![Adja meg a szállítási adatokat](./media/offline-backup-dpm-mabs-previous-versions/shipping-inputs.png)<br/>

1. Az összes bemenet megadása után alaposan tekintse át a részleteket, és véglegesítse a megadott szállítási adatokat az **Igen** érték megadásával.

    ![Szállítási információk áttekintése](./media/offline-backup-dpm-mabs-previous-versions/review-shipping-information.png)<br/>

1. A szállítási információk sikeres frissítése után a segédprogram helyi helyet biztosít a megadott szállítási adatok tárolásához.

    ![Szállítási információk tárolása](./media/offline-backup-dpm-mabs-previous-versions/storing-shipping-information.png)<br/>

   > [!IMPORTANT]
   > Győződjön meg arról, hogy a meghajtók két héten belül elérik az Azure-adatközpontot a szállítási információk *AzureOfflineBackupDiskPrep* segédprogrammal történő biztosításával. Ennek elmulasztása miatt a meghajtókat nem lehet feldolgozni.

Az előző lépések elvégzése után az Azure-adatközpont készen áll a meghajtók fogadására és annak feldolgozására, hogy a meghajtókról a létrehozott klasszikus típusú Azure Storage-fiókba vigye át a biztonsági mentési adatok átvitelét.

### <a name="time-to-process-the-drives"></a>A meghajtók feldolgozásának ideje

Az Azure-beli importálási feladatok feldolgozásához szükséges idő mennyisége változó. A folyamat ideje olyan tényezőktől függ, mint a szállítási idő, a feladattípus, a másolt adatok típusa és mérete, valamint a megadott lemezek mérete. Az Azure import/export szolgáltatás nem rendelkezik SLA-val. A lemezek fogadása után a szolgáltatás arra törekszik, hogy a biztonsági másolat adatait 7 – 10 nap múlva fejezze be az Azure Storage-fiókba. A következő szakasz azt ismerteti, hogyan figyelheti az Azure importálási feladatok állapotát.

### <a name="monitor-azure-import-job-status"></a>Azure-beli importálási feladatok állapotának figyelése

Amíg a meghajtók továbbítása vagy az Azure-adatközpont átmásolásra kerül a Storage-fiókba, a Azure Backup ügynök vagy DPM vagy a MABS-konzol a forrásszámítógépen a következő feladatot jeleníti meg az ütemezett biztonsági mentésekhez:

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Az importálási feladatok állapotának ellenõrzése:

1. Nyisson meg egy rendszergazda jogú parancssort a forrásoldali számítógépen, és futtassa a következő parancsot:

     `AzureOfflineBackupDiskPrep.exe u:`

1. A kimenet az importálási feladatok aktuális állapotát jeleníti meg.

    ![Importálási feladatok állapotának keresése](./media/offline-backup-dpm-mabs-previous-versions/import-job-status-reporting.png)<br/>

Az Azure importálási feladat különböző állapotával kapcsolatos további információkért lásd: [Azure import/export feladatok állapotának megtekintése](../import-export/storage-import-export-view-drive-status.md).

### <a name="finish-the-workflow"></a>A munkafolyamat befejezése

Az importálási feladatok befejeződése után a kezdeti biztonsági mentési adatok elérhetők a Storage-fiókban. A következő ütemezett biztonsági mentés időpontjában a Azure Backup átmásolja az adatok tartalmát a Storage-fiókból a Recovery Services-tárolóba.

   ![Adatmásolás Recovery Services-tárolóba](./media/offline-backup-dpm-mabs-previous-versions/copying-from-storage-account-to-azure-backup.png)<br/>

A következő ütemezett biztonsági mentés időpontjában a Azure Backup növekményes biztonsági mentést hajt végre a kezdeti biztonsági másolaton.

## <a name="next-steps"></a>Következő lépések

* Az Azure import/export szolgáltatás munkafolyamatával kapcsolatos kérdésekért lásd: [a Microsoft Azure import/export szolgáltatás használata az adatok blob Storage-tárolóba történő átviteléhez](../import-export/storage-import-export-service.md).
