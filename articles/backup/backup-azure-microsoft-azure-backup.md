---
title: Számítási Azure Backup Server biztonságimentő szolgáltatás használata
description: Ebből a cikkből megtudhatja, hogyan készítheti elő a környezetet a számítási feladatok védelmére és biztonsági Microsoft Azure Backup a mabs használatával.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: b13eb22ad11535114b1cb82630bc1b490a03173f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517572"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Telepítési és frissítési Azure Backup Server

> [!div class="op_single_selector"]
>
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

> A következőkre vonatkozik: MABS v3. (A MABS v2 már nem támogatott. Ha a MABS v3-as verziójánál korábbi verziót használ, frissítsen a legújabb verzióra.)

Ez a cikk azt ismerteti, hogyan készítheti elő a környezetet a számítási feladatok biztonsági Microsoft Azure Backup (MABS) használatával. A Azure Backup Server segítségével egyetlen konzolról védheti az alkalmazás számítási feladatait, például a Hyper-V virtuális gépeket, a Microsoft SQL Server-t, a SharePoint Servert, a Microsoft Exchange-et és a Windows-ügyfeleket.

> [!NOTE]
> Azure Backup Server VMware virtuális gépek védelmét és továbbfejlesztett biztonsági képességeket biztosít. Telepítse a terméket az alábbi szakaszokban és a legújabb Azure Backup szerint. A VMware-kiszolgálók biztonsági Azure Backup Server A [VMware-kiszolgálók](backup-azure-backup-server-vmware.md)biztonsági Azure Backup Server használata cikkben talál további információt. A biztonsági funkciókkal kapcsolatos további információkért tekintse meg Azure Backup [biztonsági funkciók dokumentációját.](backup-azure-security-feature.md)
>
>

Az Azure-beli virtuális gépen üzembe helyezett MABS képes biztonsági másolatot készíteni az Azure-beli virtuális gépekről, de a biztonsági mentési művelet engedélyezéséhez ugyanabban a tartományban kell lennie. Az Azure-beli virtuális gépek biztonsági helyezésének folyamata változatlan marad, mint a helyszíni virtuális gépek biztonsági helyezése, a MABS Azure-ban való üzembe helyezésének azonban vannak bizonyos korlátai. A korlátozásokkal kapcsolatos további információkért lásd: [DPM Azure-beli virtuális gépként](/system-center/dpm/install-dpm#setup-prerequisites)

> [!NOTE]
> Az Azure két üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és a használathoz: a Resource Manager [és a klasszikus.](../azure-resource-manager/management/deployment-models.md) Ez a cikk az új modellel üzembe helyezett virtuális gépek visszaállítására vonatkozó információkat és eljárásokat Resource Manager be.
>
>

Azure Backup Server a számítási feladatok biztonsági mentési funkcióját a Data Protection Manager (DPM) biztosítja. Ez a cikk a DPM-dokumentációra mutató hivatkozásokat tartalmaz, amelyek a megosztott funkciókat ismertetik. Bár Azure Backup Server funkciók nagy része megegyezik a DPM-hez, a Azure Backup Server nem szalagra, és nem integrálható a System Center.

## <a name="choose-an-installation-platform"></a>Telepítési platform kiválasztása

Az első lépés a Azure Backup Server és futtatásához egy Windows Server beállítása. A kiszolgáló az Azure-ban vagy a helyszínen is lehet.

* A helyszíni számítási feladatok védelméhez a MABS-kiszolgálónak a helyszínen kell lennie.
* Az Azure-beli virtuális gépeken futó számítási feladatok védelméhez a MABS-kiszolgálónak az Azure-ban kell lennie, és Azure-beli virtuális gépként kell futnia.

### <a name="using-a-server-in-azure"></a>Kiszolgáló használata az Azure-ban

Amikor kiszolgálót választ a Azure Backup Server, javasoljuk, hogy a Windows Server 2016 Datacenter vagy a Windows Server 2019 Datacenter katalógusképét használja. A Create [your first Windows virtual machine in the Azure Portal](../virtual-machines/windows/quick-create-portal.md?toc=/azure/virtual-machines/windows/toc.json)(Az első Windows rendszerű virtuális gép létrehozása a Azure Portal-ban) cikk egy oktatóanyagot tartalmaz az Azure-ban ajánlott virtuális gép használatának első lépésekkel, még akkor is, ha még soha nem használta az Azure-t. A kiszolgáló virtuális gépre (VM) a következő minimális követelmények vonatkoznak: Standard_A4_v2 négy maggal és 8 GB RAM-mal.

A számítási feladatok Azure Backup Server számos árnyaltság van. A [MABS védelmi mátrixa](./backup-mabs-protection-matrix.md) segít ezeknek az árnyalásnak a magyarázatában. A gép üzembe helyezése előtt olvassa el ezt a cikket teljesen.

### <a name="using-an-on-premises-server"></a>Helyszíni kiszolgáló használata

Ha nem szeretné futtatni az alapkiszolgálót az Azure-ban, futtathatja a kiszolgálót Egy Hyper-V virtuális gépen, egy VMware virtuális gépen vagy egy fizikai gazdagépen. A kiszolgálóhardver ajánlott minimális követelményei a két mag és a 8 GB RAM. A támogatott operációs rendszerek a következő táblázatban vannak felsorolva:

| Operációs rendszer | Platform | Termékváltozat |
|:--- | --- |:--- |
| Windows Server 2019 |64 bit |Standard, Datacenter, Essentials |
| Windows Server 2016 és a legújabb SPS-k |64 bit |Standard, Datacenter, Essentials  |

A DPM-tároló deduplikációja Windows Server-deduplikációval is elérhető. További információ arról, hogyan működik együtt a DPM és a [deduplikáció](/system-center/dpm/deduplicate-dpm-storage) Hyper-V virtuális gépeken való üzembe helyezéskor.

> [!NOTE]
> Azure Backup Server dedikált, egycélú kiszolgálón való futtatásra tervezték. Nem telepíthet alkalmazásokat a Azure Backup Server:
>
> * Tartományvezérlőként futó számítógépre
> * Olyan számítógépre, amelyen telepítve van az Alkalmazáskiszolgáló szerepkör
> * Egy számítógép, amely System Center Operations Manager felügyeleti kiszolgáló
> * Olyan számítógépre, amelyen az Exchange Server fut
> * Olyan számítógép, amely egy fürt csomópontja
>
> A Azure Backup Server Windows Server Core és a Microsoft Hyper-V Server nem támogatja.

Mindig csatlakozzon Azure Backup Server tartományhoz. Ha a kiszolgálót egy másik tartományba tervezi áthelyezni, először telepítse Azure Backup Server, majd csatlakozzon az új tartományhoz. A meglévő virtuális gép Azure Backup Server új tartományba az üzembe helyezés után *nem támogatott.*

Akár biztonsági mentési adatokat küld az Azure-nak, akár helyileg tárolja őket, Azure Backup Server recovery services-tárolóban kell regisztrálni.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Tárreplikáció beállítása

A tárreplikáció lehetősége lehetővé teszi, hogy georedundáns tárolás és helyileg redundáns tárolás között válasszon. Alapértelmezés szerint a Recovery Services-tárolók georedundáns tárolást használnak. Ha ez a tároló az elsődleges tároló, hagyja meg a georedundáns tárolás beállítást. Ha egy olcsóbb, rövidebb élettartamú megoldást szeretne, válassza a helyileg redundáns tárolást. A georedundáns, [](../storage/common/storage-redundancy.md#locally-redundant-storage)a helyileg redundáns és a zónaredundáns tárolási lehetőségekről további információt az Azure Storage [replikációs](../storage/common/storage-redundancy.md#zone-redundant-storage) [áttekintésében talál.](../storage/common/storage-redundancy.md) [](../storage/common/storage-redundancy.md#geo-redundant-storage)

A tárreplikációs beállítás szerkesztése:

1. A **Recovery Services-tárolók panelen** válassza ki az új tárolót. A Beállítások **szakaszban** válassza a Tulajdonságok **lehetőséget.**
2. A **Tulajdonságok alatt,** a **Biztonsági mentés konfigurációja alatt** válassza a Frissítés **lehetőséget.**

3. Válassza ki a tárreplikáció típusát, majd válassza a **Mentés lehetőséget.**

     ![Az új tároló tárolási konfigurációjának beállítása](./media/backup-create-rs-vault/recovery-services-vault-backup-configuration.png)

## <a name="software-package"></a>Szoftvercsomag

### <a name="downloading-the-software-package"></a>A szoftvercsomag letöltése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Ha már megnyitott egy Recovery Services-tárolót, folytassa a 3. lépéssel. Ha még nincs megnyitva Recovery Services-tároló, de a Azure Portal van, a főmenüben válassza a Tallózás **lehetőséget.**

   * Az erőforrások listájába írja be a következőt: **Recovery Services**.
   * Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Amikor megjelennek a **Recovery Services-tárolók, válassza** ki.

     ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     A Recovery Services-tárolók listája megjelenik.
   * A Recovery Services-tárolók listájából válasszon ki egy tárolót.

     Megnyílik a kiválasztott tároló irányítópultja.

     ![Tároló irányítópultja](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. A **Beállítások** panel alapértelmezés szerint megnyílik. Ha be van zárva, válassza a **Beállítások lehetőséget** a beállítások panel megnyitásához.

    ![Beállítások panel](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Válassza **a Biztonsági mentés** lehetőséget az Első lépések varázsló megnyitásához.

    ![Biztonsági mentés – első lépések](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    A **megnyíló Első lépések a biztonsági** mentéssel panelen a biztonsági mentési célok automatikusan ki lesznek választva. 

    ![Backup-goals-default-opened](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. A Biztonsági **mentés célja panelen** a **Hol futnak** a számítási feladatok? menüben válassza a **Helyszíni lehetőséget.**

    ![helyszíni és számítási feladatok célokként](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    A **Miről szeretne** biztonságimentéseket? legördülő menüből válassza ki a védendékként használni kívánt számítási feladatokat a Azure Backup Server, majd kattintson az **OK gombra.**

    A **Getting Started with backup (Első lépések a biztonsági mentéssel)** varázsló átváltja az **Infrastruktúra** előkészítése lehetőséget a számítási feladatok Azure-ba való biztonsági mentéséhez.

   > [!NOTE]
   > Ha csak fájlokról és mappákról szeretne biztonsági mentéset, javasoljuk, hogy használja a Azure Backup-ügynököt, és a következő cikkben található útmutatást: Először keresse meg a fájlok és mappák biztonsági [mentésecímet.](./backup-windows-with-mars-agent.md) Ha nem csak fájlokat és mappákat fog védelemmel látni, vagy a jövőben bővíteni tervezi a védelmi igényeket, válassza ki ezeket a számítási feladatokat.
   >
   >

    ![Az Első lépések varázsló módosítása](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. A **megnyíló Infrastruktúra előkészítése** panelen válassza az Install Azure Backup Server and Download vault credentials (Tároló hitelesítő adatainak letöltése) Azure Backup Server a Download vault credentials (Tároló hitelesítő adatainak letöltése) hivatkozásokat.  A tároló hitelesítő adatait a helyreállítási tárba való Azure Backup Server használhatja. A hivatkozások arra a letöltőközpontra mutató hivatkozásokat tartalmaznak, ahonnan a szoftvercsomag letölthető.

    ![Infrastruktúra előkészítése Azure Backup Server](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Jelölje ki az összes fájlt, majd kattintson a **Tovább gombra.** Töltse le a fájl letöltési oldalának Microsoft Azure Backup érkező összes fájlt, és helyezze az összes fájlt ugyanoda.

    ![1. letöltőközpont](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Mivel az összes fájl együttes letöltési mérete 3 GB>, 10 Mb/s sebességű letöltési hivatkozáson akár 60 percet is igénybe vehet, hogy a letöltés befejeződik.

### <a name="extracting-the-software-package"></a>A szoftvercsomag kinyerés

Az összes fájl letöltése után válassza a **MicrosoftAzureBackupInstaller.exe.** Ezzel elindítja a **Microsoft Azure Backup telepítővarázsló,** hogy a telepítőfájlokat az Ön által megadott helyre bontsa ki. Folytassa a varázsló lépéseit, és válassza a **Kinyerés** gombot a kinyerési folyamat megkezdéséhez.

> [!WARNING]
> A telepítőfájlok kibontása legalább 4 GB szabad terület szükséges.
>
>

![Fájlok telepítéshez való kinyerésének beállítása](./media/backup-azure-microsoft-azure-backup/extract/03.png)

A kinyerési folyamat befejezése után jelölje be  a jelölőnégyzetet a frissen kinyertsetup.exea Microsoft Azure Backup Server telepítésének megkezdéséhez, majd kattintson a **Befejezés gombra.**

### <a name="installing-the-software-package"></a>A szoftvercsomag telepítése

1. A **Microsoft Azure Backup** varázsló elindításához válassza az Microsoft Azure Backup lehetőséget.

    ![Microsoft Azure Backup telepítővarázsló](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. A üdvözlőképernyő kattintson a **Tovább gombra.** Ezzel az Előfeltétel-ellenőrzések *szakaszra kerül.* Ezen a képernyőn válassza az **Ellenőrzés** lehetőséget annak megállapításához, hogy teljesülnek-e Azure Backup Server hardver- és szoftverkövetelményei. Ha minden előfeltétel teljesült, megjelenik egy üzenet, amely jelzi, hogy a gép megfelel a követelményeknek. Válassza a **Tovább** gombot.

    ![Azure Backup Server – Üdvözöljük és előfeltételek ellenőrzése](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. A Azure Backup Server telepítőcsomag a megfelelő bináris SQL Server tartalmazza. Új telepítés Azure Backup Server válassza az **Install new Instance of SQL Server with** this setup (Új példány telepítése ezzel a telepítővel) lehetőséget, és válassza az Ellenőrzés és **telepítés gombot.** Az előfeltételek sikeres telepítése után válassza a Tovább **lehetőséget.**

    >[!NOTE]
    >Ha saját SQL-kiszolgálót szeretne használni, a támogatott SQL Server a 2014 SP1 vagy újabb, 2016-os és 2017-es SQL Server támogatott verziók.  Minden SQL Server Standard vagy Enterprise 64 bites verziónak kell lennie.
    >Azure Backup Server távoli kapcsolatpéldánysal nem SQL Server működni. Az alkalmazás által használt Azure Backup Server helyinek kell lennie. Ha meglévő SQL-kiszolgálót használ a MABS-hez, a  MABS-beállítás csak az SQL Server megnevezett példányainak használatát támogatja.

    ![Azure Backup Server – SQL-ellenőrzés](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Ha hiba történik, és a számítógép újraindítására vonatkozó javaslat merül fel, tegye meg, és válassza az **Ellenőrzés lehetőséget.** Ha sql-konfigurációval kapcsolatos probléma van, konfigurálja újra az SQL-t az SQL-irányelveknek megfelelően, és próbálja meg újra telepíteni/frissíteni a MABS-t a meglévő SQL-példány használatával.

   **Manuális konfigurálás**

   Ha saját SQL-példányt használ, győződjön meg arról, hogy beépített\Rendszergazdák szerepkört ad hozzá a fő adatbázishoz.

    **SSRS-konfiguráció AZ SQL 2017-ben**

    Ha saját SQL 2017-példányt használ, manuálisan kell konfigurálnia az SSRS-t. Az SSRS konfigurálása után győződjön meg arról, hogy az *SSRS IsInitialized* tulajdonsága True (Igaz) *értékre van állítva.* Ha ez true (Igaz) értékre van állítva, a MABS feltételezi, hogy az SSRS már konfigurálva van, és kihagyja az SSRS-konfigurációt.

    Az SSRS-konfigurációhoz használja a következő értékeket:
    * Szolgáltatásfiók: A "Beépített fiók használata" legyen Hálózati szolgáltatás
    * Webszolgáltatás URL-címe: A "Virtual Directory" legyen ReportServer_\<SQLInstanceName>
    * Adatbázis: A DatabaseName paraméternek ReportServer$ kell lennie.\<SQLInstanceName>
    * Webportál URL-címe: A "Virtuális könyvtár" legyen Reports_\<SQLInstanceName>

    [További információ az](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode) SSRS konfigurációról.

    > [!NOTE]
    > Az MABS SQL Server adatbázisként használt szolgáltatások licencelését a [Microsoft Online Szolgáltatások Feltételei](https://www.microsoft.com/licensing/product-licensing/products) (OST) szabályozza. Az OST SQL Server MABS-sel csomagolt adatok csak a MABS adatbázisaként használhatók.

4. Adja meg a kiszolgálófájlok telepítési Microsoft Azure Backup, majd válassza a **Tovább lehetőséget.**

    ![Adja meg a fájlok telepítésének helyét](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    Az azure-ba való biztonsági biztonságiolás követelménye az új hely létrehozása. Győződjön meg arról, hogy az új hely az adatok legalább 5%-a, amelyekről biztonsági adatokat tervezünk a felhőbe biztonságimentetni. A lemezvédelemhez a telepítés befejezése után külön lemezeket kell konfigurálni. További információ a tárolókészletről: [Adattárolás előkészítése.](/system-center/dpm/plan-long-and-short-term-data-storage)

    A lemezes tárolás kapacitáskövetelményei elsősorban a védett adatok méretétől, a helyreállítási pont napi méretétől, a kötetek várható adatnövekedési arányától és a megőrzési időtartam célkitűzéseitől függnek. Javasoljuk, hogy a védett adatok méretének kétszeresét tegye a lemezes tárolásra. Ez a méret feltételezi, hogy a helyreállítási pont napi mérete a védett adatok méretének 10%-a, a megőrzési időtartam pedig 10 nap. A megfelelő méretbecsléshez tekintse át a [DPM-Capacity Planner.](https://www.microsoft.com/download/details.aspx?id=54301) 

5. Adjon meg egy erős jelszót a korlátozott helyi felhasználói fiókokhoz, majd válassza a **Tovább lehetőséget.**

    ![Erős jelszó megadása](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Válassza ki, hogy *szeretné-e* használni Microsoft Update frissítéseket, majd válassza a **Tovább lehetőséget.**

   > [!NOTE]
   > Javasoljuk, hogy Windows Update a Microsoft Update, amely biztonsági és fontos frissítéseket biztosít a Windowshoz és más termékekhez, például a Microsoft Azure Backup Serverhez.
   >
   >

    ![Microsoft Update Opt-In](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Tekintse át *a Beállítások összegzése et, és* válassza a Telepítés **lehetőséget.**

    ![Beállítások összegzése](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. A telepítés fázisok szerint történik. Az első fázisban a Microsoft Azure Recovery Services Agent lesz telepítve a kiszolgálón. A varázsló az internetkapcsolatot is ellenőrzi. Ha van internetkapcsolat, folytathatja a telepítést. Ha nem, meg kell adnia a proxy adatait az internethez való csatlakozáshoz.

    A következő lépés a helyreállítási Microsoft Azure konfigurálása. A konfiguráció részeként meg kell adnia a tároló hitelesítő adatait, hogy regisztrálja a gépet a Recovery Services-tárolóban. Emellett egy jelszót is meg kell adnia az Azure és a helyszín között küldött adatok titkosításához/visszafejtéséhez. Automatikusan létrehozhat egy jelszót, vagy saját, minimum 16 karakteres jelszót is létrehozhat. Folytassa a varázslóval, amíg az ügynök nincs konfigurálva.

    ![Kiszolgáló regisztrálása varázsló](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Miután a Microsoft Azure Backup-kiszolgáló regisztrációja sikeresen befejeződött, a telepítővarázsló továbblép a SQL Server és a Azure Backup Server telepítésére. A SQL Server telepítésének befejezése után a Azure Backup Server összetevők is telepítve vannak.

    ![Azure Backup Server beállítási folyamat lépése](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

A telepítési lépés befejezése után a termék asztali ikonjai is létrejönnek. Kattintson duplán az ikonra a termék elindításához.

### <a name="add-backup-storage"></a>Biztonsági mentési tár hozzáadása

Az első biztonsági másolat a virtuális géphez csatolt tárolón Azure Backup Server található. További információ a lemezek hozzáadásáról: [Tárolókészletek és lemezes tárolás konfigurálása.](./backup-mabs-add-storage.md)

> [!NOTE]
> Akkor is hozzá kell adni a Backup Storage-et, ha adatokat szeretne küldeni az Azure-nak. A tár jelenlegi architektúrája Azure Backup Server Azure Backup tároló tartalmazza  az adatok második másolatát, míg a helyi tároló tartalmazza az első (és kötelező) biztonsági másolati másolatot.
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Az Data Protection Manager védelmi ügynök telepítése és frissítése

A MABS a System Center Data Protection Manager ügynököt használja. [A védelmi ügynök](/system-center/dpm/deploy-dpm-protection-agent) védelmi kiszolgálókra való telepítésének lépései a következőek.

A következő szakaszok ismertetik, hogyan frissítheti az ügyfélszámítógépek védelmi ügynökeit.

1. A Backup Server felügyeleti konzol válassza a Management Agents **(Kezelőügynökök)**  >  **lehetőséget.**

2. A megjelenítési panelen válassza ki azokat az ügyfélszámítógépeket, amelyekhez frissíteni szeretné a védelmi ügynököt.

   > [!NOTE]
   > Az **Ügynökfrissítések** oszlop jelzi, hogy mikor érhető el a védelmi ügynök frissítése az egyes védett számítógépeken. A Műveletek **panelen** a Frissítés **művelet** csak akkor érhető el, ha védett számítógép van kiválasztva, és frissítések érhetők el.
   >
   >

3. A frissített védelmi ügynökök a kijelölt számítógépekre való telepítéséhez a Műveletek **ablaktáblán** válassza a Frissítés **lehetőséget.**

4. Olyan ügyfélszámítógépek számára, amelyek nem csatlakoznak a hálózathoz, amíg  a számítógép nem csatlakozik a hálózathoz, az Ügynök állapota oszlopban a Frissítés függőben állapot **látható.**

   Miután egy ügyfélszámítógép csatlakozott a  hálózathoz, az ügyfélszámítógép Ügynökfrissítések oszlopában a Frissítés **állapot jelenik meg.**

## <a name="move-mabs-to-a-new-server"></a>MABS áthelyezése új kiszolgálóra

Az alábbi lépéseket kell követve áthelyezni a MABS-t egy új kiszolgálóra, miközben meg kell őriznie a tárterületet. Ez csak akkor használhatja, ha az összes adat a modern biztonsági másolati tárhely.

  > [!IMPORTANT]
  >
  > * Az új kiszolgálónévnek ugyanaznak kell lennie, mint az eredeti Azure Backup Server példánynak. Az új Azure Backup Server-példány nevét nem módosíthatja, ha az előző tárolókészletet és a MABS-adatbázist (DPMDB) szeretné használni a helyreállítási pontok megőrzéséhez.
  > * A MABS-adatbázis (DPMDB) biztonsági másolatával kell lennie. Szüksége lesz rá az adatbázis visszaállításához.

1. A kijelzőpanelen válassza ki azokat az ügyfélszámítógépeket, amelyekhez frissíteni szeretné a védelmi ügynököt.
2. Állítsa le az eredeti Azure Backup kiszolgálót, vagy kapcsolja offline állapotba.
3. Állítsa alaphelyzetbe a számítógépfiókot a Active Directory.
4. Telepítse a Server 2016-ot egy új gépre, és adja meg ugyanazt a gépnevet, mint az eredeti Azure Backup kiszolgáló.
5. Csatlakozzon a tartományhoz.
6. Telepítse Azure Backup Server 3-as vagy újabb (a MABS-tárolókészlet lemezeit a régi kiszolgálóról és importálja).
7. Állítsa vissza az 1. lépésben tett DPMDB-t.
8. Csatlakoztassa az eredeti biztonsági mentési kiszolgáló tárterületét az új kiszolgálóhoz.
9. Az SQL-ről állítsa vissza a DPMDB-t.
10. Futtassa a CMD-t (rendszergazdaként) az új kiszolgálón. Ugrás a Microsoft Azure Backup és a bin mappára

    Elérési út – példa: `C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\"`

11. A Azure Backup való csatlakozáshoz futtassa a `DPMSYNC -SYNC`

    Ha a régiek áthelyezése helyett új lemezeket adott a DPM-tárolókészlethez, futtassa a következőt:  `DPMSYNC -Reallocatereplica` .

## <a name="network-connectivity"></a>Hálózati kapcsolat

Azure Backup Server kapcsolatra van szükség a Azure Backup szolgáltatáshoz, hogy a termék sikeresen működjön. Annak ellenőrzéséhez, hogy a gép rendelkezik-e kapcsolattal az Azure-ral, használja a parancsmagot ```Get-DPMCloudConnection``` Azure Backup Server PowerShell-konzolon. Ha a parancsmag kimenete IGAZ, akkor a kapcsolat létezik, ellenkező esetben nincs kapcsolat.

Az Azure-előfizetésnek ugyanakkor kifogástalan állapotban kell lennie. Az előfizetés állapotának kiderítéhez és kezeléséhez jelentkezzen be az előfizetési [portálra.](https://account.windowsazure.com/Subscriptions)

Ha már ismeri az Azure-kapcsolat és az Azure-előfizetés állapotát, az alábbi táblázat segítségével kiderítheti, milyen hatással van a kínált biztonsági mentési/visszaállítási funkció.

| Kapcsolat állapota | Azure-előfizetés | Biztonsági mentés az Azure platformra | Biztonságimentás lemezre | Visszaállítás az Azure-ból | Visszaállítás lemezről |
| --- | --- | --- | --- | --- | --- |
| Csatlakozva |Aktív |Engedélyezve |Engedélyezve |Engedélyezve |Engedélyezve |
| Csatlakozva |Lejárt |Leállítva |Leállítva |Engedélyezve |Engedélyezve |
| Csatlakozva |Megszüntetve |Leállítva |Leállítva |Leállítva és az Azure-beli helyreállítási pontok törölve |Leállítva |
| Megszakadt a kapcsolat > 15 nap alatt |Aktív |Leállítva |Leállítva |Engedélyezve |Engedélyezve |
| Megszakadt a kapcsolat > 15 nap alatt |Lejárt |Leállítva |Leállítva |Engedélyezve |Engedélyezve |
| Megszakadt a kapcsolat > 15 nap alatt |Megszüntetve |Leállítva |Leállítva |Leállítva és az Azure-beli helyreállítási pontok törölve |Leállítva |

### <a name="recovering-from-loss-of-connectivity"></a>Helyreállítás a kapcsolat megszakadása után

Ha a gépe korlátozott internetkapcsolattal rendelkezik, győződjön meg arról, hogy a gép vagy a proxy tűzfalbeállításai engedélyezik a következő URL-címeket és IP-címeket:

* URL-címek
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP-címek
  * 20.190.128.0/18
  * 40.126.0.0/18

ExpressRoute Microsoft-társviszony-létesítés használata esetén válassza ki a következő szolgáltatásokat/régiókat:

* Azure Active Directory (12076:5060)
* Microsoft Azure régió (a Recovery Services-tároló helye alapján)
* Azure Storage (a Recovery Services-tároló helye alapján)

További részletekért látogasson el az [ExpressRoute útválasztási követelményeit tartalmazó webhelyre.](../expressroute/expressroute-routing.md)

Miután helyreállította a kapcsolatot az Azure-ral Azure Backup Server gépre, a elvégezhető műveleteket az Azure-előfizetés állapota határozza meg. A fenti táblázat részletesen tartalmazza a gép "Csatlakoztatva" után engedélyezett műveleteit.

### <a name="handling-subscription-states"></a>Előfizetési államok kezelése

Lejárt vagy megszüntetett állapotból aktív  állapotba  is lehet vinni egy *Azure-előfizetést.* Ez azonban hatással van a termék viselkedésére, ha az állapot nem *Aktív:*

* A *megszüntetett előfizetés* funkciói a megszüntetés időszakában elvesznek. Aktív bekapcsolása *után* a biztonsági mentés/visszaállítás termékfunkciója helyreáll. A helyi lemezen lévő biztonsági mentési adatok akkor is lekértek, ha kellően nagy megőrzési időtartammal voltak megőrizve. Az Azure-beli biztonsági mentési adatok azonban visszavonhatatlan módon elvesznek, ha az előfizetés megszüntetett *állapotba* lép.
* A *lejárt előfizetés* csak akkor veszíti el a funkcióit, ha az újra aktívvá nem *válik.* Az előfizetés lejárt időszakára ütemezett biztonsági  mentések nem fognak futni.

## <a name="upgrade-mabs"></a>A MABS frissítése

Az ALÁBBI eljárásokkal frissítheti a MABS-t.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Frissítés MABS V2-ről 3-as verzióra

> [!NOTE]
>
> A MABS V2 nem előfeltétele a MABS V3 telepítésének. A MABS V3 verzióra azonban csak a MABS V2-ről frissíthet.

A MABS frissítését az alábbi lépésekkel használhatja:

1. A MABS V2-ről a MABS V3-ra való frissítéshez szükség esetén frissítse az operációs rendszert Windows Server 2016-ra vagy Windows Server 2019-re.

2. Frissítse a kiszolgálót. A lépések hasonlóak a [telepítéséhez.](#install-and-upgrade-azure-backup-server) Az SQL-beállításokhoz azonban lehetősége lesz frissíteni az SQL-példányt az SQL 2017-re, vagy saját SQL Server 2017-példányt használni.

   > [!NOTE]
   >
   > Ne lépjen ki, amíg az SQL-példány frissítése folyamatban van. A kilépéssel eltávolítja az SQL jelentéskészítési példányt, így a MABS újrafrissítésére tett kísérlet sikertelen lesz.

   > [!IMPORTANT]
   >
   >  Az SQL 2017 frissítésének részeként biztonsági másolatot készítünk az SQL-titkosítási kulcsokról, és eltávolítjuk a jelentéskészítési szolgáltatásokat. Az SQL Server frissítése után a reporting service(14.0.6827.4788) telepítve lesz, & titkosítási kulcsok visszaállnak.
   >
   > Az SQL 2017 manuális konfigurálásakor tekintse meg az *SSRS-konfiguráció AZ SQL 2017-ben* című szakaszt a Telepítési utasítások szakaszban.

3. Frissítse a védelmi ügynököket a védett kiszolgálókon.
4. A biztonsági mentéseket az éles kiszolgálók újraindítása nélkül kell folytatni.
5. Most megkezdheti az adatok védelmét. Ha a biztonsági mentésre modern biztonsági másolati tárhely, a védelem alatt kiválaszthatja a köteteket, amelyeken a biztonsági másolatokat tárolni szeretné, és ellenőrizheti, hogy a rendelkezésre álló terület alatt van-e. [További információ](backup-mabs-add-storage.md).

## <a name="troubleshooting"></a>Hibaelhárítás

Ha Microsoft Azure Backup a telepítési fázis (vagy biztonsági mentés vagy visszaállítás) [](https://support.microsoft.com/kb/3041338) során hibák lépnek fel, további információért tekintse meg ezt a hibakódok dokumentumát.
A kapcsolódó gyakori Azure Backup [is hivatkozhat](backup-azure-backup-faq.yml)

## <a name="next-steps"></a>Következő lépések

A környezet DPM-hez való előkészítéséről itt kaphat részletes [információt.](/system-center/dpm/prepare-environment-for-dpm) Emellett információkat tartalmaz a támogatott konfigurációkról, amelyeken Azure Backup Server üzembe helyezhetők és használhatók. Különböző műveletek végrehajtásához [több PowerShell-parancsmagot](/powershell/module/dataprotectionmanager/) is használhat.

Ezekkel a cikkekkel mélyebben is megértheti a számítási feladatok védelmét a Microsoft Azure Backup használatával.

* [SQL Server biztonsági mentés](backup-azure-backup-sql.md)
* [SharePoint-kiszolgáló biztonsági mentése](backup-azure-backup-sharepoint.md)
* [Másodlagos kiszolgáló biztonsági mentése](backup-azure-alternate-dpm-server.md)
