---
title: Azure Backup Server beállítása Azure VMware-megoldáshoz
description: Állítsa be az Azure VMware-megoldási környezetét a virtuális gépek biztonsági mentésére Azure Backup Server használatával.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 0dd2b16254e697a08d0ff542a5ddcb3fc7e4103d
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750615"
---
# <a name="set-up-azure-backup-server-for-azure-vmware-solution"></a>Azure Backup Server beállítása Azure VMware-megoldáshoz

Azure Backup Server egy robusztus nagyvállalati biztonsági mentési és helyreállítási rendszer, amely hozzájárul az üzletmenet-folytonossági és a vész-helyreállítási (BCDR) stratégiához. Az Azure VMware-megoldás előzetes verziójában a Azure Backup Server használatával konfigurálhatja a virtuális gép (VM) szintjének biztonsági mentését. 

A Azure Backup Server a következő helyre tudja tárolni a biztonsági mentési fájlokat:

- **Lemez**: a rövid távú tároláshoz Azure Backup Server biztonsági mentést készít az adatlemez-készletekre.
- **Azure**: mind a rövid, mind a hosszú távú tároláshoz, Azure Backup Server a lemezes készletekben tárolt adatairól biztonsági másolatot készíthet a Microsoft Azure felhőbe Azure Backup használatával.

Ha az kimaradások bekövetkeznek, és a forrásadatok nem érhetők el, a Azure Backup Server használatával egyszerűen visszaállíthatók az adatforrások vagy egy másik hely. Így ha az eredeti adat a tervezett vagy váratlan problémák miatt nem érhető el, egyszerűen visszaállíthatja az adatmennyiséget egy másik helyre.

Ebből a cikkből megtudhatja, hogyan készítheti elő Azure VMware-megoldási környezetét a virtuális gépek biztonsági mentésére Azure Backup Server használatával. Végigvezeti a következő lépéseken: 

> [!div class="checklist"]
> * Határozza meg a használni kívánt VM-lemez típusát és méretét.
> * Hozzon létre egy Recovery Services tárolót, amely a helyreállítási pontokat tárolja.
> * Recovery Services-tároló tárolási replikálásának beállítása.
> * Tároló hozzáadása a Azure Backup Serverhoz.

## <a name="supported-vmware-features"></a>Támogatott VMware-funkciók

- **Ügynök nélküli biztonsági mentés:** Azure Backup Server nem szükséges, hogy az ügynök telepítve legyen a vCenter vagy ESXi-kiszolgálón a virtuális gép biztonsági mentéséhez. Ehelyett csak adja meg az IP-címet vagy a teljes tartománynevet (FQDN), valamint a VMware-kiszolgáló Azure Backup Server használatával történő hitelesítéséhez használt bejelentkezési hitelesítő adatokat.
- **Felhőbe integrált biztonsági mentés:** Azure Backup Server védi a munkaterheléseket a lemezre és a felhőre. Azure Backup Server biztonsági mentési és helyreállítási munkafolyamata segítségével kezelheti a hosszú távú adatmegőrzést és a telephelyen kívüli biztonsági mentést.
- **A vCenter által felügyelt virtuális gépek észlelése és biztosítása:** Azure Backup Server észleli és védi a vCenter-vagy ESXi-kiszolgálón üzembe helyezett virtuális gépeket. A Azure Backup Server a vCenter által felügyelt virtuális gépeket is észleli, így a nagyméretű telepítések is védhetők.
- **Mappa szintű autoprotection:** a vCenter lehetővé teszi a virtuális gépek megszervezését a virtuálisgép-mappákban. Azure Backup Server észleli ezeket a mappákat, és felhasználhatja a virtuális gépeket a mappa szintjén, amely tartalmazza az összes almappát is. A mappák védelme során Azure Backup Server nem csak a mappában lévő virtuális gépeket védi, hanem a később hozzáadott virtuális gépeket is. A Azure Backup Server naponta észleli az új virtuális gépeket, és automatikusan megvédi azokat. Amikor a virtuális gépeket rekurzív mappákba rendezi, Azure Backup Server automatikusan észleli és védi a rekurzív mappákban üzembe helyezett új virtuális gépeket.
- **Azure Backup Server továbbra is biztosítja a fürtön belüli VMotioned virtuális gépeket:** Mivel a virtuális gépek a fürtön belüli terheléselosztás vMotioned, Azure Backup Server automatikusan észleli és folytatja a virtuális gép védelmét.
- A **szükséges fájlok gyorsabb helyreállítása:** A Azure Backup Server a teljes virtuális gép helyreállítása nélkül állíthatja helyre a fájlokat vagy mappákat egy Windows rendszerű virtuális gépről.

## <a name="limitations"></a>Korlátozások

- Telepíteni kell az 1. kumulatív frissítést a Azure Backup Server v3 verzióhoz.
- Nem lehet biztonsági másolatot készíteni a felhasználói pillanatképekről az első Azure Backup Server biztonsági mentés előtt. Miután Azure Backup Server befejezte az első biztonsági mentést, biztonsági másolatot készíthet a felhasználói pillanatképekről.
- A Azure Backup Server nem tudja biztosítani a VMware virtuális gépeket csatlakoztatott lemezekkel és fizikai RAW eszköz-hozzárendelésekkel (pRDMs).
- Azure Backup Server nem tudja felderíteni vagy védelemmel ellátni a VMware-Vapp.

Az Azure VMware-megoldás Azure Backup Serverának beállításához a következő lépéseket kell végrehajtania:

- Állítsa be az előfeltételeket és a környezetet.
- Recovery Services-tároló létrehozása.
- Azure Backup Server letöltése és telepítése.
- Tároló hozzáadása a Azure Backup Serverhoz.

### <a name="deployment-architecture"></a>Üzembe helyezési architektúra

Azure Backup Server üzembe helyezése Azure-beli infrastruktúra-szolgáltatásként (IaaS), amely az Azure VMware-megoldás virtuális gépei elleni védelemmel rendelkezik.

:::image type="content" source="media/avs-backup/deploy-mabs-avs-diagram.png" alt-text="Azure Backup Server üzembe helyezése Azure-beli infrastruktúra-szolgáltatásként (IaaS), amely az Azure VMware-megoldás virtuális gépei elleni védelemmel rendelkezik." border="false":::

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>A Azure Backup Server környezet előfeltételei

Ha az Azure-környezetben telepíti Azure Backup Server telepítését, vegye figyelembe az ebben a szakaszban ismertetett javaslatokat.

### <a name="azure-virtual-network"></a>Azure Virtual Network

Győződjön meg arról, hogy az [Azure-ban konfigurálja a VMware Private-felhő hálózatkezelését](tutorial-configure-networking.md).

### <a name="determine-the-size-of-the-virtual-machine"></a>A virtuális gép méretének meghatározása

Az előző lépésben létrehozott virtuális hálózaton létre kell hoznia egy Windows rendszerű virtuális gépet. Ha Azure Backup Server futtatására szolgáló kiszolgálót választ, kezdje a Windows Server 2019 Datacenter katalógusának rendszerképével. Az oktatóanyag [hozza létre az első Windows rendszerű virtuális gépet a Azure Portal az](../virtual-machines/windows/quick-create-portal.md) Azure-ban az ajánlott virtuális géppel indul el, még akkor is, ha soha nem használta az Azure-t.

A következő táblázat összefoglalja a védett munkaterhelések maximális számát az egyes Azure Backup Server virtuális gépek méretével kapcsolatban. Az információk belső teljesítmény- és méretezési teszteken alapulnak, amelyek a munkaterhelések méretére és forgalmára kanonikus értékeket alkalmaznak. A tényleges munkaterhelés mérete nagyobb is lehet, de a Azure Backup Server virtuális géphez csatlakoztatott lemezek számára is elérhetőnek kell lennie.

| Védett munkaterhelések maximális száma | Munkaterhelés átlagos mérete | Munkaterhelés átlagos forgalma (naponta) | Minimális tárterület IOPS | Ajánlott lemez típusa/mérete      | Ajánlott virtuális gép mérete |
|-------------------------|-----------------------|--------------------------------|------------------|-----------------------------------|---------------------|
| 20                      | 100 GB                | Nettó 5%-os forgalom                   | 2000             | Standard HDD (8 TB vagy újabb méret/lemez)  | A4V2       |
| 40                      | 150 GB                | Nettó 10%-os forgalom                  | 4.500             | Prémium SSD * (1 TB vagy nagyobb méretű lemez) | DS3_V2     |
| 60                      | 200 GB                | Nettó 10%-os forgalom                  | 10 500            | Prémium SSD * (8 TB vagy újabb méret/lemez) | DS3_V2     |

* A szükséges IOPs lekéréséhez használja a minimálisan ajánlott vagy magasabb méretű lemezeket. A kisebb méretű lemezek alacsonyabb IOPs kínálnak.

> [!NOTE]
> Azure Backup Server úgy lett kialakítva, hogy dedikált, egycélú kiszolgálón fusson. A Azure Backup Server nem telepíthető olyan számítógépre, amely a következőket teszi:
> * Tartományvezérlőként fut.
> * Telepítve van az Alkalmazáskiszolgáló szerepkör.
> * System Center Operations Manager felügyeleti kiszolgáló.
> * Futtatja az Exchange Servert.
> * Egy fürt csomópontja.

### <a name="disks-and-storage"></a>Lemezek és tárolás

A Azure Backup Server lemezeket igényel a telepítéshez, amely magában foglalja a rendszerfájlokat, a telepítési fájlokat, az előfeltételként szükséges szoftvereket, az adatbázisfájlok és a tároló dedikált lemezeit.

| Követelmény                      | Ajánlott méret  |
|----------------------------------|-------------------------|
| Azure Backup Server telepítés                | Telepítési hely: 3 GB<br />Adatbázisfájlok meghajtója: 900 MB<br />Rendszermeghajtó: 1 GB SQL Server telepítéshez<br /><br />Emellett a Azure Backup Server számára is szükség van arra, hogy archiválja a katalógusfájlt egy ideiglenes telepítési helyre.      |
| A tárolókészlet lemeze<br />(Alapszintű köteteket használ, nem lehet dinamikus lemezen) | A védett adatmennyiségek méretének kétszerese.<br />A tárterület részletes számítását lásd: [DPM Capacity Planner](https://www.microsoft.com/download/details.aspx?id=54301).   |

Ha szeretné megtudni, hogyan csatolhat új felügyelt adatlemezt egy meglévő Azure-beli virtuális géphez, tekintse meg [a felügyelt adatlemez csatlakoztatása Windows rendszerű virtuális géphez a Azure Portal használatával](../virtual-machines/windows/attach-managed-disk-portal.md)című témakört.

> [!NOTE]
> Egyetlen Azure Backup Server 120 TB-os puha korláttal rendelkezik a tárolási készlethez.

### <a name="store-backup-data-on-local-disk-and-in-azure"></a>A helyi lemezen és az Azure-ban tárolt biztonsági mentési adatgyűjtés

A biztonsági mentési adattárolók tárolása az Azure-ban csökkenti a biztonsági mentési infrastruktúrát a Azure Backup Server virtuális gépen. Az Operational Recovery (biztonsági mentés) esetében a Azure Backup Server a virtuális géphez csatolt Azure-lemezeken tárolja a biztonsági mentési adatkészleteket. Miután a lemezeket és a tárolóhelyet csatlakoztatta a virtuális géphez, Azure Backup Server kezeli a tárolót. A biztonsági mentési adattárolás mennyisége az egyes Azure-beli virtuális gépekhez csatolt lemezek számától és méretétől függ. Az Azure-beli virtuális gép minden méretének maximális száma csatlakoztatható. Az a2 például négy lemez, az a3 pedig nyolc lemez, az A4 pedig 16 lemez. A lemezek mérete és száma is meghatározza a biztonsági mentési tár teljes kapacitását.

> [!IMPORTANT]
> A Azure Backup Server csatolt lemezeken több mint öt napig *ne* őrizze meg az operatív helyreállítási adatait. Ha az adattábla öt napnál régebbi, akkor tárolja Recovery Services tárolóban.

A biztonsági mentési adattárolók Azure-ban való tárolásához hozzon létre vagy használjon Recovery Services-tárolót. Amikor előkészíti a Azure Backup Server munkaterhelés biztonsági mentését, [konfigurálja a Recovery Services](#create-a-recovery-services-vault)-tárolót. A konfigurálást követően minden alkalommal, amikor egy online biztonsági mentési feladatot futtat, a rendszer létrehoz egy helyreállítási pontot a tárolóban. Minden Recovery Services-tár legfeljebb 9 999 helyreállítási pontot tart. A létrehozott helyreállítási pontok számától és a megőrzött mennyiségtől függően sok évig megőrizheti a biztonsági másolatok mennyiségét. Létrehozhat például havi helyreállítási pontokat, és öt évig megtarthatja őket.

> [!IMPORTANT]
> Függetlenül attól, hogy az Azure-ba küldi a biztonsági mentési vagy helyi tárolást, Azure Backup Server Recovery Services-tárolóval kell regisztrálnia.

### <a name="scale-deployment"></a>Központi telepítés méretezése

Ha szeretné méretezni az üzemelő példányt, a következő lehetőségek közül választhat:

- Vertikális **felskálázás**: növelje a Azure Backup Server virtuális gép méretét sorozatról DS3 sorozatra, és növelje a helyi tárterületet.
- **Adat**-kiszervezés: a régebbi adatküldés az Azure-ba, és csak a legújabb adat marad a Azure Backup Server géphez csatolt tárolóban.
- Vertikális **felskálázás**: vegyen fel további Azure Backup Server gépeket a munkaterhelések elleni védelemhez.

### <a name="net-framework"></a>.NET-keretrendszer

A virtuális gépnek telepítve kell lennie a .NET-keretrendszer 3,5 SP1 vagy újabb verziójának.

### <a name="join-a-domain"></a>Csatlakozás tartományhoz

A Azure Backup Server virtuális gépnek csatlakoznia kell egy tartományhoz, és a virtuális gépen rendszergazdai jogosultságokkal rendelkező tartományi felhasználónak kell telepítenie Azure Backup Server.

Bár az előzetes verzió használatakor nem támogatott, az Azure-beli virtuális gépen üzembe helyezett Azure Backup Server biztonsági mentést készíthet a virtuális gépekről az Azure VMware megoldásban. A munkaterhelésnek ugyanabban a tartományban kell lennie, hogy engedélyezze a biztonsági mentési műveletet.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A Recovery Services-tároló egy olyan tárolási entitás, amely az idő múlásával létrehozott helyreállítási pontokat tárolja. Emellett a védett elemekhez társított biztonsági mentési házirendeket is tartalmaz.

1. Jelentkezzen be az előfizetésbe a [Azure Portalban](https://portal.azure.com/).

1. A bal oldali menüben válassza a **minden szolgáltatás**lehetőséget.

   ![A bal oldali menüben válassza a minden szolgáltatás lehetőséget.](../backup/media/backup-create-rs-vault/click-all-services.png)

1. A **minden szolgáltatás** párbeszédpanelen írja be a **Recovery Services** , és válassza ki **Recovery Services** tárolókat a listából.

   ![Adja meg és válassza Recovery Services tárolók lehetőséget.](../backup/media/backup-create-rs-vault/all-services.png)

   Megjelenik az előfizetésben található Recovery Services-tárolók listája.

1. A **Recovery Services** -tárolók irányítópultján válassza a **Hozzáadás**lehetőséget.

   ![Recovery Services-tároló hozzáadása.](../backup/media/backup-create-rs-vault/add-button-create-vault.png)

   Megnyílik az **Recovery Services** -tároló párbeszédpanel.

1. Adja meg a **név**, az **előfizetés**, az **erőforráscsoport**és a **hely**értékét.

   ![Konfigurálja a Recovery Services-tárolót.](../backup/media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Név**: adjon meg egy rövid nevet a tároló azonosításához. A névnek egyedinek kell lennie az Azure-előfizetésben. Olyan nevet adjon meg, amely legalább kettőnél több mint 50 karakterből áll. A névnek betűvel kell kezdődnie, és csak betűt, számot és kötőjelet tartalmazhat.
   - **Előfizetés**: válassza ki a használni kívánt előfizetést. Ha csak egy előfizetés tagja, akkor ezt a nevet fogja látni. Ha nem biztos abban, hogy melyik előfizetést szeretné használni, használja az alapértelmezett (javasolt) előfizetést. Több választási lehetőség is van, ha a munkahelyi vagy iskolai fiók egynél több Azure-előfizetéshez van társítva.
   - **Erőforráscsoport**: használjon meglévő erőforráscsoportot, vagy hozzon létre egy újat. Az előfizetésben elérhető erőforráscsoportok listájának megtekintéséhez válassza a **meglévő használata**lehetőséget, majd válasszon ki egy erőforrást a legördülő listából. Új erőforráscsoport létrehozásához válassza az **új létrehozása** lehetőséget, és adja meg a nevet.
   - **Hely**: válassza ki a tároló földrajzi régióját. Ha egy tárolót szeretne létrehozni az Azure VMware megoldás virtuális gépei számára, a tárolónak ugyanabban a régióban *kell lennie* , mint az Azure VMware-megoldás saját felhője.

1. Ha készen áll az Recovery Services-tároló létrehozására, válassza a **Létrehozás**lehetőséget.

   ![Hozza létre a Recovery Services-tárolót.](../backup/media/backup-create-rs-vault/click-create-button.png)

   A Recovery Services-tároló létrehozása eltarthat egy ideig. Figyelje az **értesítéseket** a portál jobb felső sarkában található értesítések területén. A tároló létrehozása után a Recovery Services-tárolók listájában látható. Ha nem látja a tárolót, válassza a **frissítés**lehetőséget.

   ![Frissítse a Backup-tárolók listáját.](../backup/media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-replication"></a>Tárolási replikáció beállítása

A tárolási replikáció lehetőséggel választhat a Geo-redundáns tárolók (az alapértelmezett) és a helyileg redundáns tárolás között. A Geo-redundáns tároló a Storage-fiókban lévő adatait egy másodlagos régióba másolja, amely az adatmegőrzést teszi lehetővé. A helyileg redundáns tárolás egy olcsóbb megoldás, amely nem tartós. További információ a Geo-redundáns és a helyileg redundáns tárolási lehetőségekről: [Azure Storage redundancia](../storage/common/storage-redundancy.md).

> [!IMPORTANT]
> Ha a tárolóban a biztonsági mentések konfigurálása előtt el szeretné végezni a tárolási replikálás beállításának módosítását **helyileg – redundáns/geo-redundánsan** a Recovery Services-tárolón. A biztonsági mentések konfigurálása után a módosítás lehetőség le van tiltva, és nem módosíthatja a tárolási replikálás típusát.

1. **Recovery Services**-tárolóból válassza ki az új tárolót. 

1. A **Beállítások** alatt válassza a **Tulajdonságok** elemet. A **biztonsági mentés konfigurálása**területen válassza a **frissítés**lehetőséget.

1. Válassza ki a tárolási replikálás típusát, majd kattintson a **Mentés**gombra.

   ![Adja meg az új tároló tárolási konfigurációját.](../backup/media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## <a name="download-and-install-the-software-package"></a>A szoftvercsomag letöltése és telepítése

Kövesse az ebben a szakaszban található lépéseket a szoftvercsomag letöltéséhez, kibontásához és telepítéséhez.

### <a name="download-the-software-package"></a>A szoftvercsomag letöltése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Ha már meg van nyitva egy Recovery Services-tároló, folytassa a következő lépéssel. Ha nem rendelkezik megnyitott Recovery Services-tárolóval, de a Azure Portal, válassza a főmenü **Tallózás**elemét.

   1. Az erőforrások listájában adja meg a **Recovery Services**.

   1. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Ha **Recovery Services**-tárolót lát, válassza ki.

   ![Hozzon létre Recovery Services Vault 1. lépését](../backup/media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

1. A Recovery Services-tárolók listájából válasszon ki egy tárolót.

   Megnyílik a kiválasztott tároló irányítópultja.

   ![Megnyílik a kiválasztott tároló irányítópultja.](../backup/media/backup-azure-microsoft-azure-backup/vault-dashboard.png)

   A **Beállítások** lehetőség alapértelmezés szerint megnyílik. Ha be van zárva, válassza a **Beállítások** lehetőséget a megnyitásához.

   ![A beállítások lehetőség alapértelmezés szerint megnyílik. Ha be van zárva, válassza a beállítások lehetőséget a megnyitásához.](../backup/media/backup-azure-microsoft-azure-backup/vault-setting.png)

1. A **első lépések** varázsló megnyitásához válassza a **biztonsági mentés** lehetőséget.

   ![A Első lépések varázsló megnyitásához válassza a biztonsági mentés lehetőséget.](../backup/media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

1. A megnyíló ablakban tegye a következőket:

   1. A **hol fut a számítási feladatok?** menüben válassza **a**helyszíni lehetőséget.

      :::image type="content" source="media/avs-backup/deploy-mabs-on-premises-workload.png" alt-text="Hol fut a munkaterhelés?":::

   1. A **Miről szeretne biztonsági másolatot készíteni?** menüben válassza ki a védelemmel ellátni kívánt munkaterheléseket Azure Backup Server használatával.

   1. Válassza az **infrastruktúra előkészítése** lehetőséget a Azure Backup Server és a tár hitelesítő adatainak letöltéséhez és telepítéséhez.

      :::image type="content" source="media/avs-backup/deploy-mabs-prepare-infrastructure.png" alt-text="Az infrastruktúra előkészítése":::

1. A megnyíló **előkészítési infrastruktúra** ablakban tegye a következőket:

   1. A Azure Backup Server telepítéséhez válassza a **letöltési** hivatkozást.

   1. Töltse le a tároló hitelesítő adatait a **már letöltött vagy a legújabb Azure Backup Server telepítés** jelölőnégyzet bejelölésével, majd válassza a **Letöltés**lehetőséget. A tároló hitelesítő adatait a Azure Backup Server regisztrációja során használja a Recovery Services-tárolóba. A hivatkozások a letöltőközpontból tölthetik le, ahol letölti a szoftvercsomagot.

   :::image type="content" source="media/avs-backup/deploy-mabs-prepare-infrastructure2.png" alt-text="Infrastruktúra előkészítése – Azure Backup Server":::

1. A letöltési oldalon válassza ki az összes fájlt, majd kattintson a **tovább**gombra.

   > [!NOTE]
   > Az összes fájlt ugyanabba a mappába kell letöltenie. Mivel a fájlok letöltésének mérete 3 GB-nál nagyobb, akár 60 percet is igénybe vehet, amíg a letöltés be nem fejeződik. 

   ![A letöltési oldalon válassza ki az összes fájlt, majd kattintson a Tovább gombra.](../backup/media/backup-azure-microsoft-azure-backup/downloadcenter.png)

### <a name="extract-the-software-package"></a>A szoftvercsomag kibontása

Ha a szoftvercsomagot egy másik kiszolgálóra töltötte le, másolja a fájlokat a Azure Backup Server üzembe helyezéséhez létrehozott virtuális gépre.

> [!WARNING]
> A telepítőfájlok kibontásához legalább 4 GB szabad terület szükséges.

1. Az összes fájl letöltése után kattintson duplán a **MicrosoftAzureBackupInstaller.exe** elemre a **Microsoft Azure Backup** telepítővarázslójának megnyitásához, majd válassza a **tovább**lehetőséget.

1. Válassza ki a helyet a fájlok kibontásához, majd kattintson a **tovább**gombra.

1. A kinyerési folyamat elindításához válassza a **Kibontás** lehetőséget.

   ![A kinyerési folyamat elindításához válassza a Kibontás lehetőséget.](../backup/media/backup-azure-microsoft-azure-backup/extract/03.png)

1. A kibontás után válassza a **setup.exevégrehajtásának ** lehetőségét, majd válassza a **Befejezés**lehetőséget.

> [!TIP]
> Megkeresheti a setup.exe fájlt abban a mappában is, ahová kicsomagolta a szoftvercsomagot.

### <a name="install-the-software-package"></a>A szoftvercsomag telepítése

1. A **telepítés ablak telepítés területén válassza**a **Microsoft Azure Backup** lehetőséget a telepítővarázsló megnyitásához.

   ![A telepítés ablak telepítés területén válassza a Microsoft Azure Backup lehetőséget a telepítővarázsló megnyitásához.](../backup/media/backup-azure-microsoft-azure-backup/launch-screen2.png)

1. Az **üdvözlőképernyőn** kattintson a **Next** (tovább) gombra az **Előfeltételek ellenőrzése** lap folytatásához.

1. Ha meg szeretné állapítani, hogy teljesülnek-e a Azure Backup Server hardver-és szoftver-előfeltételei, válassza az **ismételt ellenőrzés** lehetőséget. Ha a Met sikeresen megtörtént, válassza a **tovább**lehetőséget.

   ![ Ha meg szeretné állapítani, hogy teljesülnek-e a Azure Backup Server hardver-és szoftver-előfeltételei, válassza az ismételt ellenőrzés lehetőséget. Ha a Met sikeresen megtörtént, válassza a tovább lehetőséget.](../backup/media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

1. A Azure Backup Server telepítőcsomag a szükséges SQL Server bináris fájlokkal együtt van csomagolva. Új Azure Backup Server telepítésének indításakor válassza a **SQL Server új példányának telepítése** ezzel a telepítési lehetőséggel lehetőséget. Ezután válassza **az ellenőrzési és telepítési**lehetőséget.

   ![A Azure Backup Server telepítőcsomag a szükséges SQL Server bináris fájlokkal együtt van csomagolva.](../backup/media/backup-azure-microsoft-azure-backup/sql/01.png)

   > [!NOTE]
   > Ha saját SQL Server-példányát szeretné használni, a támogatott SQL Server-verziók SQL Server 2014 SP1 vagy újabb, 2016 és 2017. Minden SQL Server verziónak standard vagy Enterprise 64 bitesnek kell lennie. Azure Backup Server nem működik távoli SQL Server-példánnyal. A Azure Backup Server által használt példánynak helyinek kell lennie. Ha meglévő SQL Server-példányt használ a Azure Backup Serverhoz, a telepítő csak a SQL Server *példányainak* használatát támogatja.

   Ha hiba lép fel a gép újraindítására vonatkozó javaslattal, tegye a következőt, és válassza az **újbóli vizsgálat**lehetőséget. Ha vannak SQL Server konfigurációs problémák, konfigurálja újra SQL Server az SQL Server irányelvek alapján. Ezután próbálja megismételni a Azure Backup Server telepítését vagy frissítését a SQL Server meglévő példányának használatával.

   **Manuális konfigurálás**

   Ha a SQL Server saját példányát használja, ügyeljen arra, hogy Builtin\rendszergazda adjon hozzá a sysadmin szerepkörhöz a Master adatbázishoz.

   **SSRS-konfiguráció SQL Server 2017-vel**

   Ha a SQL Server 2017 saját példányát használja, akkor manuálisan kell konfigurálnia a SQL Server 2017 Reporting Services (SSRS) szolgáltatást. Az SSRS konfigurálása után győződjön meg arról, hogy az SSRS **IsInitialized** tulajdonsága **true**értékre van állítva. Ha a tulajdonság értéke TRUE ( **igaz**), Azure Backup Server feltételezi, hogy az SSRS már konfigurálva van, és kihagyja az SSRS-konfigurációt.

   Az SSRS konfigurációs állapotának megtekintéséhez futtassa a következő parancsot:

   ```powershell
   $configset =Get-WmiObject –namespace 
   "root\Microsoft\SqlServer\ReportServer\RS_SSRS\v14\Admin" -class 
   MSReportServer_ConfigurationSetting -ComputerName localhost

   $configset.IsInitialized
   ```

   Az SSRS konfigurálásához használja a következő értékeket:

   * **Szolgáltatásfiók**: **a beépített fiók használata** legyen **hálózati szolgáltatás**.
   * **Webszolgáltatás URL-címe**: a **virtuális könyvtárnak** **ReportServer_nak \<SQLInstanceName> **kell lennie.
   * **Adatbázis**: a **databasename** **reportserver $ \<SQLInstanceName> **-nek kell lennie.
   * **Webportál URL-címe**: a **virtuális könyvtárnak** **Reports_nak \<SQLInstanceName> **kell lennie.

   [További](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) információ az SSRS-konfigurációról.

   > [!NOTE]
   > A [Microsoft Online Services használati feltételei](https://www.microsoft.com/licensing/product-licensing/products) (OST) szabályozzák a Azure Backup Server-adatbázisként használt SQL Server licencelését. Az OST-nek megfelelően a SQL Server a csomagban Azure Backup Server csak a Azure Backup Server adatbázisa használható.

1. A telepítés sikerességét követően válassza a **tovább**lehetőséget.

1. Adja meg Microsoft Azure Backup Server-fájlok telepítésének helyét, majd kattintson a **Tovább gombra**.

   > [!NOTE]
   > Az Azure-ba történő biztonsági mentéshez meg kell adni a tartalék helyet. Győződjön meg arról, hogy a hely a felhőbe történő biztonsági mentéshez tervezett adatmennyiség legalább 5%-a. A lemezes védelemhez a telepítés befejezése után külön lemezeket kell konfigurálni. További információ a Storage-készletekről: a [tárolási készletek és a lemezes tárolás konfigurálása](/previous-versions/system-center/system-center-2012-r2/hh758075(v=sc.12)).

   ![Adja meg Microsoft Azure Backup Server-fájlok telepítésének helyét, majd kattintson a Tovább gombra.](../backup/media/backup-azure-microsoft-azure-backup/space-screen.png)

1. Adjon meg egy erős jelszót a korlátozott helyi felhasználói fiókokhoz, és kattintson a **Tovább gombra**.

   ![Adjon meg egy erős jelszót a korlátozott helyi felhasználói fiókokhoz, és kattintson a Tovább gombra.](../backup/media/backup-azure-microsoft-azure-backup/security-screen.png)

1. Válassza ki, hogy szeretné-e használni a Microsoft Update a frissítések kereséséhez, majd kattintson a **tovább**gombra.

   > [!NOTE]
   > Javasoljuk, hogy Windows Update átirányítást a Microsoft Updatera, amely biztonsági és fontos frissítéseket kínál a Windows és más termékek, például a Azure Backup Server számára.

   ![Válassza ki, hogy szeretné-e használni a Microsoft Update a frissítések kereséséhez, majd kattintson a Tovább gombra.](../backup/media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

1. Tekintse át a **Beállítások összegzését**, és válassza a **telepítés**lehetőséget.

   A telepítés fázisokban történik. Az első fázis telepíti a Microsoft Azure Recovery Services ügynököt, és a második fázis ellenőrzi az internetkapcsolatot. Ha az internetkapcsolat elérhető, folytathatja a telepítést. Ha nem, meg kell adnia a proxy adatait az internethez való csatlakozáshoz. Az utolsó fázis ellenőrzi az előfeltételként szükséges szoftvereket. Ha nincs telepítve, a hiányzó szoftverek a Microsoft Azure Recovery Services ügynökkel együtt települnek.

1. Válassza a **Tallózás** lehetőséget, hogy megkeresse a tároló hitelesítő adatait, hogy regisztrálja a gépet a Recovery Services-tárolóban, majd válassza a **tovább**lehetőséget.

1. Válasszon egy pass kifejezést az Azure és a telephely között továbbított adattitkosításhoz vagy visszafejtéshez.

   > [!TIP]
   > Automatikusan létrehozhat egy jelszót, vagy megadhatja a saját minimum 16 karakterből álló jelszót.

1. Adja meg a pass kifejezés mentési helyét, majd válassza a **tovább** lehetőséget a kiszolgáló regisztrálásához.

   > [!IMPORTANT]
   > Mentse a pass kifejezést a helyi kiszolgálótól eltérő biztonságos helyre. Javasoljuk, hogy az Azure Key Vault használatával tárolja a pass kifejezést.

   A Microsoft Azure Recovery Services ügynök telepítésének befejeződése után a telepítési lépés a SQL Server és a Azure Backup Server összetevők telepítésére és konfigurálására kerül át.

   ![A Microsoft Azure Recovery Services ügynök telepítésének befejeződése után a telepítési lépés a SQL Server és a Azure Backup Server összetevők telepítésére és konfigurálására kerül át.](../backup/media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

1. A telepítési lépés befejeződése után válassza a **Bezárás**lehetőséget.

### <a name="install-update-rollup-1"></a>1. kumulatív frissítés telepítése

Az 1. kumulatív frissítés telepítése a Azure Backup Server V3 esetében kötelező, mielőtt védelemmel látja el a munkaterheléseket. A hibajavítások listájának megtekintéséhez és az 1. kumulatív frissítéshez Azure Backup Server szükséges telepítési utasításokért lásd a Tudásbázis [4534062](https://support.microsoft.com/en-us/help/4534062/)-os cikkét.

## <a name="add-storage-to-azure-backup-server"></a>Tárterület hozzáadása az Azure Backup Serverhez

A Azure Backup Server v3 modern biztonsági másolati tárhely a következőket kínálja:

-  Tárterület-megtakarítás 50%-ban.
-  Háromszor gyorsabb biztonsági másolatok.
-  Hatékonyabb tárolás.
-  Munkaterhelés-kompatibilis tároló.

### <a name="volumes-in-azure-backup-server"></a>Kötetek a Azure Backup Serverban

Ha még nincs hozzáadva, adja hozzá az adatlemezeket a szükséges tárolókapacitással a Azure Backup Server virtuális géphez.

A Azure Backup Server v3 csak a tárolási köteteket fogadja el. Kötet hozzáadásakor Azure Backup Server formázza a kötetet a rugalmas fájlrendszer (ReFS) számára, amelyhez modern biztonsági másolati tárhely szükséges.

### <a name="add-volumes-to-azure-backup-server-disk-storage"></a>Kötetek hozzáadása Azure Backup Server lemezes tároláshoz

1. A **felügyelet** ablaktáblán Ellenőrizze újra a tárolót, majd válassza a **Hozzáadás**lehetőséget. 

1. Válasszon a rendelkezésre álló kötetek közül a tárolóhoz való hozzáadáshoz. 

1. Az elérhető kötetek hozzáadása után adjon meg egy felhasználóbarát nevet, amely segít a kezelésében. 

1. Kattintson **az OK gombra** a kötetek ReFS való formázásához, hogy a Azure Backup Server használhassa a modern biztonsági másolati tárhely előnyeit.

![Elérhető kötetek hozzáadása](../backup/media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="next-steps"></a>További lépések

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan konfigurálhatja az Azure VMware-megoldáson futó VMware virtuális gépek biztonsági mentését Azure Backup Server használatával.

> [!div class="nextstepaction"]
> [Azure VMware-megoldás virtuális gépek biztonsági mentésének konfigurálása](backup-avs-vms-with-mabs.md)
