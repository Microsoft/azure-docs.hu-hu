---
title: A számítási feladatok biztonsági mentésének Azure Backup Server használata az Azure-ba
description: A munkaterhelések Azure Portal való ellátásához használja a Azure Backup Server.
ms.reviewer: kasinh
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: dacurwin
ms.openlocfilehash: 3f427726a128eed426a64bc533075ba0cdde9544
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71241076"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Azure Backup Server telepítése és frissítése

> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

> A következőkre vonatkozik: MABS v3. (A MABS v2 már nem támogatott. Ha a MABS v3-nál korábbi verziót használ, frissítsen a legújabb verzióra.)

Ez a cikk azt ismerteti, hogyan készítse elő a környezetet a munkaterhelések biztonsági mentésére Microsoft Azure Backup kiszolgáló (MABS) használatával. A Azure Backup Server segítségével az alkalmazások számítási feladatait, például a Hyper-V virtuális gépeket, a Microsoft SQL Server, a SharePoint Servert, a Microsoft Exchange-t és a Windows-ügyfeleket egyetlen konzolról biztosíthatja.

> [!NOTE]
> A Azure Backup Server mostantól védelmet nyújt a VMware virtuális gépek számára, és továbbfejlesztett biztonsági képességeket biztosít. Telepítse a terméket az alábbi részben leírtak szerint, valamint a legújabb Azure Backup-ügynököt. Ha többet szeretne megtudni a VMware-kiszolgálók Azure Backup Server használatával történő biztonsági mentéséről, olvassa el a következő cikket: a [VMware-kiszolgáló biztonsági mentésének Azure Backup Server használata](backup-azure-backup-server-vmware.md). A biztonsági képességekkel kapcsolatos további információkért tekintse meg az [Azure Backup biztonsági szolgáltatásainak dokumentációját](backup-azure-security-feature.md).
>
>

Az Azure-beli virtuális gépen üzembe helyezett MABS biztonsági mentést készíthetnek a virtuális gépekről az Azure-ban, de a biztonsági mentési művelet engedélyezéséhez ugyanabban a tartományban kell lenniük. Az Azure-beli virtuális gépek biztonsági mentésének folyamata ugyanaz marad, mint a helyszíni virtuális gépek biztonsági mentése, azonban a MABS az Azure-ban való üzembe helyezése bizonyos korlátozásokkal rendelkezik. A korlátozással kapcsolatos további információkért lásd: [DPM Azure-beli virtuális gép](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-1807#setup-prerequisites)

> [!NOTE]
> Az Azure két üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a Resource Manager-modell használatával üzembe helyezett virtuális gépek visszaállításával kapcsolatos információkat és eljárásokat ismerteti.
>
>

Azure Backup Server örökli a munkaterhelés biztonsági mentési funkcióinak nagy részét Data Protection Manager (DPM). Ez a cikk az DPM dokumentációra hivatkozik, amely ismerteti a megosztott funkciók némelyikét. Bár a Azure Backup Server a DPM azonos funkcionalitással rendelkezik, Azure Backup Server nem készít biztonsági mentést a szalagra, és nem integrálja a System centerrel.

## <a name="choose-an-installation-platform"></a>Telepítési platform kiválasztása

A Azure Backup Server működésének első lépése a Windows Server beállítása. A kiszolgáló lehet az Azure-ban vagy a helyszínen is.

### <a name="using-a-server-in-azure"></a>Kiszolgáló használata az Azure-ban

Azure Backup Server futtatására szolgáló kiszolgáló kiválasztásakor javasoljuk, hogy a Windows Server 2016 Datacenter vagy a Windows Server 2019 Datacenter katalógusával kezdjen. Az [első Windows rendszerű virtuális gép létrehozása a Azure Portalban](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)című cikk útmutatást nyújt az Azure-ban javasolt virtuális gép használatának megkezdéséhez, még akkor is, ha korábban még nem használta az Azure-t. A kiszolgáló virtuális géphez (VM) ajánlott minimális követelmény a következő: Standard_A4_v2 négy maggal és 8 GB RAM-mal.

A munkaterhelések Azure Backup Serversal való védelme számos árnyalattal rendelkezik. A cikk a [DPM Azure](https://technet.microsoft.com/library/jj852163.aspx)-beli virtuális gépként való telepítésével segít megmagyarázni ezeket az árnyalatokat. A gép üzembe helyezése előtt olvassa el ezt a cikket teljesen.

### <a name="using-an-on-premises-server"></a>Helyszíni kiszolgáló használata

Ha nem szeretné futtatni az alapkiszolgálót az Azure-ban, akkor futtathatja a kiszolgálót egy Hyper-V virtuális gépen, egy VMware virtuális gépen vagy egy fizikai gazdagépen. A kiszolgálói hardver ajánlott minimális követelményei két mag és 8 GB RAM. A támogatott operációs rendszerek a következő táblázatban láthatók:

| Operációs rendszer | Platform | SKU |
|:--- | --- |:--- |
| A Windows Server 2019 |64 bites |Standard, Datacenter, Essentials |
| Windows Server 2016 és legújabb SPs |64 bites |Standard, Datacenter, Essentials  |


A DPM-tárolót a Windows Server deduplikálása használatával lehet deduplikálni. További információ arról, hogyan működik együtt a [DPM és](https://technet.microsoft.com/library/dn891438.aspx) a deduplikálás a Hyper-V virtuális gépeken való üzembe helyezés során.

> [!NOTE]
> Azure Backup Server úgy lett kialakítva, hogy dedikált, egycélú kiszolgálón fusson. A Azure Backup Server nem telepíthető a következőre:
> - Tartományvezérlőként futó számítógépre
> - Olyan számítógépre, amelyen telepítve van az Alkalmazáskiszolgáló szerepkör
> - Olyan számítógépre, amely System Center Operations Manager felügyeleti kiszolgálóként működik
> - Olyan számítógépre, amelyen az Exchange Server fut
> - Olyan számítógépre, amely fürtcsomópontként működik

Mindig csatlakozzon Azure Backup Server tartományhoz. Ha azt tervezi, hogy másik tartományba helyezi át a kiszolgálót, először telepítse Azure Backup Server, majd csatlakoztassa a kiszolgálót az új tartományhoz. Meglévő Azure Backup Server gép áthelyezése új tartományba az üzembe helyezés után *nem támogatott*.

Függetlenül attól, hogy az Azure-ba küld biztonsági mentési vagy helyi tárolást, Azure Backup Server regisztrálni kell egy Recovery Services-tárolóban.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Tárreplikáció beállítása

A tárreplikáció lehetősége lehetővé teszi, hogy georedundáns tárolás és helyileg redundáns tárolás között válasszon. Alapértelmezés szerint a Recovery Services-tárolók a Geo-redundáns tárolást használják. Ha ez a tár az elsődleges tároló, hagyja a Storage beállítást a Geo-redundáns tárolás beállításnál. Ha egy olcsóbb, rövidebb élettartamú megoldást szeretne, válassza a helyileg redundáns tárolást. A [georedundáns](../storage/common/storage-redundancy-grs.md) és a [helyileg redundáns](../storage/common/storage-redundancy-lrs.md) tárolási lehetőségekről többet olvashat az [Azure tárreplikáció áttekintése](../storage/common/storage-redundancy.md) című cikkben.

A tárreplikációs beállítás szerkesztése:

1. A **Recovery Services-tárolók** panelen kattintson az új tárolóra. A **Beállítások** szakaszban kattintson a **Tulajdonságok**elemre.
2. A **Tulajdonságok**alatt a **biztonsági mentés konfigurálása**területen kattintson a **frissítés**elemre.

3. Válassza ki a tárolási replikálás típusát, majd kattintson a **Mentés**gombra.

     ![Az új tároló tárolási konfigurációjának beállítása](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## <a name="software-package"></a>Szoftvercsomag

### <a name="downloading-the-software-package"></a>A szoftvercsomag letöltése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Ha már meg van nyitva egy Recovery Services-tároló, folytassa a 3. lépéssel. Ha nincs megnyitva Recovery Services tároló, de a Azure Portal, a főmenüben kattintson a **Tallózás**gombra.

   * Az erőforrások listájába írja be a következőt: **Recovery Services**.
   * Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Amikor meglátja a **Recovery Services-tárolót**, kattintson rá.

     ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     A Recovery Services-tárolók listája megjelenik.
   * A Recovery Services-tárolók listájából válasszon ki egy tárolót.

     Megnyílik a kiválasztott tároló irányítópultja.

     ![Tároló panelének megnyitása](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. Alapértelmezés szerint a **Beállítások** panel nyílik meg. Ha be van zárva, kattintson a **Beállítások** elemre a beállítások panel megnyitásához.

    ![Tároló panelének megnyitása](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Kattintson a **Backup (biztonsági mentés** ) elemre a első lépések varázsló megnyitásához.

    ![Biztonsági mentés – első lépések](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    A megnyíló **első lépések a Backup (biztonsági mentés** ) paneljén a **biztonsági mentési célok** automatikusan ki lesznek választva.

    ![Backup-goals-default-opened](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. A **biztonsági mentés célja** panelen, a **Hol van a számítási feladatok futtatása** menü, válassza **a**helyszíni lehetőséget.

    ![a helyszíni és a számítási feladatok céljaként](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    A **Miről szeretne biztonsági másolatot készíteni?** legördülő menüből válassza ki a védelemmel ellátni kívánt munkaterheléseket Azure Backup Server használatával, majd kattintson **az OK**gombra.

    A **biztonsági mentési varázsló első lépések** a munkaterhelések Azure-ba való biztonsági mentésére vonatkozó előkészítő eszközre vált.

   > [!NOTE]
   > Ha csak a fájlokról és mappákról szeretne biztonsági mentést készíteni, javasoljuk, hogy használja a Azure Backup-ügynököt, és kövesse a cikk útmutatását, [első lépésként: fájlok és mappák biztonsági mentése](backup-try-azure-backup-in-10-mins.md). Ha több, mint fájlt és mappát szeretne védelemmel ellátni, vagy a jövőben szeretné kibővíteni a védelmi igényeket, válassza ki ezeket a számítási feladatokat.
   >
   >

    ![Első lépések varázsló módosítása](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. A megnyíló **infrastruktúra előkészítése** panelen kattintson a telepítési Azure Backup Server és a tár hitelesítő adatainak letöltése hivatkozásra. A tároló hitelesítő adatait a Azure Backup Server regisztrációja során használja a Recovery Services-tárolóba. A hivatkozások arra a letöltőközpontból állnak, ahová a szoftvercsomag letölthető.

    ![Infrastruktúra előkészítése Azure Backup Server számára](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Válassza ki az összes fájlt, és kattintson a **tovább**gombra. Töltse le a Microsoft Azure Backup letöltési oldaláról érkező összes fájlt, és helyezze el az összes fájlt ugyanabba a mappába.

    ![1\. letöltőközpont](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Mivel az összes fájl letöltésének mérete > 3G, 10 MB/s letöltési hivatkozás esetén akár 60 percet is igénybe vehet, amíg a letöltés be nem fejeződik.

### <a name="extracting-the-software-package"></a>A szoftvercsomag kibontása

Miután letöltötte az összes fájlt, kattintson a **MicrosoftAzureBackupInstaller. exe**fájlra. Ekkor elindul a **Microsoft Azure Backup** telepítővarázsló, hogy kicsomagolja a telepítőfájlokat az Ön által megadott helyre. Folytassa a varázslót, és kattintson a **Kibontás** gombra a kinyerési folyamat megkezdéséhez.

> [!WARNING]
> A telepítőfájlok kibontásához legalább 4 GB szabad terület szükséges.
>
>

![Microsoft Azure Backup telepítővarázslója](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Ha a kinyerési folyamat befejeződött, jelölje be a jelölőnégyzetet, hogy elindítsa a frissen kibontott *Setup. exe fájlt* a Microsoft Azure Backup kiszolgáló telepítésének megkezdéséhez, majd kattintson a **Befejezés** gombra.

### <a name="installing-the-software-package"></a>A szoftvercsomag telepítése

1. Kattintson **Microsoft Azure Backup** a telepítővarázsló elindításához.

    ![Microsoft Azure Backup telepítővarázslója](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Az üdvözlőképernyőn kattintson a **tovább** gombra. Ekkor megjelenik az *Előfeltételek ellenőrzése* szakasz. Ezen a képernyőn kattintson az **ellenőrzés** elemre annak megállapításához, hogy teljesülnek-e a Azure Backup Server hardver-és szoftver-előfeltételei. Ha minden előfeltétel teljesült, megjelenik egy üzenet, amely jelzi, hogy a gép megfelel a követelményeknek. Kattintson a **tovább** gombra.

    ![Azure Backup Server – Üdvözöljük és Előfeltételek ellenőrzése](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Microsoft Azure Backup a kiszolgálónak SQL Server Enterprise szükséges. Továbbá a Azure Backup Server telepítőcsomag a megfelelő SQL Server bináris fájljaival is együtt jár, ha nem kívánja saját SQL-t használni. Új Azure Backup Server telepítésének megkezdésekor válassza a **SQL Server új példányának telepítése** ezzel a telepítővel lehetőséget, majd kattintson az **ellenőrzések és telepítés** gombra. Az előfeltételek sikeres telepítése után kattintson a **tovább**gombra.

    ![Azure Backup Server – SQL-vizsgálat](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Ha hiba lép fel a gép újraindítására vonatkozó javaslattal, tegye a következőt, majd kattintson **ismét az ismételt vizsgálat**gombra. Ha vannak SQL-konfigurációs problémák, konfigurálja újra az SQL-t az SQL-irányelvek alapján, majd próbálja meg újra telepíteni/frissíteni a MABS a meglévő SQL-példány használatával.

   > [!NOTE]
   > A Azure Backup Server távoli SQL Server példánnyal nem fog működni. A Azure Backup Server által használt példánynak helyinek kell lennie. Ha meglévő SQL Servert használ a MABS, a MABS telepítője csak az SQL Server *nevesített példányainak* használatát támogatja.

   **Manuális konfiguráció**

   Ha a saját SQL-példányát használja, ügyeljen arra, hogy a Builtin\rendszergazda-t rendszergazda szerepkörhöz adja hozzá a Master DB-hez.

    **SSRS-konfiguráció SQL 2017-mel**

    Ha az SQL 2017 saját példányát használja, manuálisan kell konfigurálnia az SSRS-t. Az SSRS konfigurálása után ellenőrizze, hogy az SSRS *IsInitialized* tulajdonsága *true*értékre van-e állítva. Ha igaz értékre van állítva, a MABS feltételezi, hogy az SSRS már konfigurálva van, és kihagyja az SSRS-konfigurációt.

    Az SSRS konfigurálásához használja a következő értékeket: 
    - Szolgáltatásfiók: A "beépített fiók használata" hálózati szolgáltatásnak kell lennie
    - Webszolgáltatás URL-címe: A "virtuális könyvtár" ReportServer_ kell lennie<SQLInstanceName>
    - Adatbázis A DatabaseName ReportServer $<SQLInstanceName>
    - Webes portál URL-címe: A "virtuális könyvtár" Reports_ kell lennie<SQLInstanceName>

    [További](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) információ az SSRS-konfigurációról.

4. Adja meg Microsoft Azure Backup-kiszolgáló fájljainak telepítéséhez szükséges helyet, majd kattintson a **tovább**gombra.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    A semmiből vonatkozó hely az Azure-ba történő biztonsági mentésre vonatkozó követelmény. Győződjön meg arról, hogy a hely a felhőbe történő biztonsági mentéshez tervezett adatmennyiség legalább 5%-a. A lemezek védelme érdekében a telepítés befejeződése után külön lemezeket kell konfigurálni. További információ a Storage-készletekről: a [tárolási készletek és a lemezes tárolás konfigurálása](https://technet.microsoft.com/library/hh758075.aspx).
5. Adjon meg egy erős jelszót a korlátozott helyi felhasználói fiókokhoz, és kattintson a **tovább**gombra.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Válassza ki, hogy szeretné-e használni a *Microsoft Update* a frissítések kereséséhez, majd kattintson a **tovább**gombra.

   > [!NOTE]
   > Javasoljuk, hogy Windows Update átirányítást a Microsoft Updatera, amely biztonsági és fontos frissítéseket kínál a Windows és más termékek, például a Microsoft Azure Backup-kiszolgáló számára.
   >
   >

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Tekintse át a *Beállítások összegzését* , és kattintson a **telepítés**gombra.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. A telepítés fázisokban történik. Az első fázisban a Microsoft Azure Recovery Services ügynök telepítve van a kiszolgálón. A varázsló az internetkapcsolatot is ellenőrzi. Ha az internetkapcsolat elérhető, akkor folytathatja a telepítést, ha nem, meg kell adnia a proxy adatait az internethez való csatlakozáshoz.

    A következő lépés az Microsoft Azure Recovery Services-ügynök konfigurálása. A konfiguráció részeként meg kell adnia a tároló hitelesítő adatait, hogy regisztrálja a gépet a Recovery Services-tárolóban. Emellett meg kell adnia egy jelszót is az Azure és a telephelye között továbbított adattitkosításhoz/visszafejtéshez. Automatikusan létrehozhat egy jelszót, vagy megadhatja a saját minimum 16 karakterből álló jelszót. Folytassa a varázslóval, amíg az ügynök be nem fejeződik.

    ![Azure Backup Server PreReq2](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Ha a Microsoft Azure Backup-kiszolgáló regisztrációja sikeresen befejeződött, a teljes telepítővarázsló a SQL Server és a Azure Backup Server összetevők telepítését és konfigurálását végzi. Az SQL Server-összetevő telepítésének befejezése után a rendszer telepíti a Azure Backup Server összetevőket.

    ![Azure Backup Server](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

A telepítési lépés befejezése után a rendszer asztali ikonjait is létrehozta. A termék elindításához kattintson duplán a ikonra.

### <a name="add-backup-storage"></a>Biztonsági mentési tár hozzáadása

Az első biztonsági másolat a Azure Backup Server géphez csatolt tárterületen marad. A lemezek hozzáadásával kapcsolatos további információkért lásd: a [tárolási készletek és a lemezes tárolás konfigurálása](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

> [!NOTE]
> Akkor is fel kell vennie a biztonsági mentési tárolót, ha az Azure-ba történő adatküldést tervezi. A Azure Backup Server jelenlegi architektúrájában a Azure Backup-tároló tárolja az adatgyűjtés *második* másolatát, miközben a helyi tárterület az első (és a kötelező) biztonsági másolatot tárolja.
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>A Data Protection Manager védelmi ügynök telepítése és frissítése

A MABS a System Center Data Protection Manager védelmi ügynököt használja. Az alábbi [lépéseket követve](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-1807) telepítheti a védelmi ügynököt a védelmi kiszolgálókra.

Az alábbi szakaszok azt ismertetik, hogyan lehet frissíteni az ügyfélszámítógépek védelmi ügynökeit.

1. A biztonsági mentési kiszolgáló felügyeleti konzol válassza a **felügyeleti** > **ügynökök**lehetőséget.

2. A Megjelenítés ablaktáblán válassza ki azokat az ügyfélszámítógépeket, amelyeknek frissíteni kívánja a védelmi ügynököt.

   > [!NOTE]
   > Az **ügynök frissítése** oszlop jelzi, hogy minden védett számítógéphez elérhető-e védelmi ügynök. A **műveletek** ablaktáblán a **frissítés** művelet csak akkor érhető el, ha egy védett számítógép van kiválasztva, és elérhetők a frissítések.
   >
   >

3. A frissített védelmi ügynökök telepítéséhez a kijelölt számítógépeken a **műveletek** ablaktáblán válassza a **frissítés**lehetőséget.

4. Olyan ügyfélszámítógép esetén, amely nem csatlakozik a hálózathoz, amíg a számítógép nem csatlakozik a hálózathoz, az **ügynök állapota** oszlopban a **frissítés függő**állapota látható.

   Miután az ügyfélszámítógép csatlakoztatva van a hálózathoz, a **frissítés**állapota a-ügyfélszámítógép **ügynök-frissítések** oszlopában jelenik meg.

## <a name="move-mabs-to-a-new-server"></a>MABS áthelyezése egy új kiszolgálóra

Az alábbi lépéseket követve kell áthelyeznie a MABS egy új kiszolgálóra, miközben meg kell őriznie a tárolót. Ezt csak akkor teheti meg, ha az összes adattal modern biztonsági másolati tárhely van.


  > [!IMPORTANT]
  > - Az új kiszolgálónévnek meg kell egyeznie az eredeti Azure Backup Server példány nevével. Az új Azure Backup Server példány neve nem módosítható, ha az előző tárolási készletet és a MABS-adatbázist (DPMDB) szeretné használni a helyreállítási pontok megőrzése érdekében.
  > - Rendelkeznie kell biztonsági másolattal a MABS-adatbázisról (DPMDB). Vissza kell állítania az adatbázist.

1. A Megjelenítés ablaktáblán válassza ki azokat az ügyfélszámítógépeket, amelyeknek frissíteni kívánja a védelmi ügynököt.
2. Állítsa le az eredeti Azure Backup-kiszolgálót, vagy kapcsolja ki a vezetéket.
3. A számítógépfiók alaphelyzetbe állítása az Active Directoryban.
4. Telepítse a 2016-es kiszolgálót az új gépre, és nevezze el az eredeti Azure Backup-kiszolgálóval megegyező számítógépnévvel.
5. Csatlakozás a tartományhoz
6. Azure Backup Server v3 vagy újabb telepítése (a MABS Storage-lemezek áthelyezése a régi kiszolgálóról és az importálásból)
7. Állítsa vissza az 1. lépésben végrehajtott DPMDB.
8. Csatolja a tárolót az eredeti backup-kiszolgálóról az új kiszolgálóra.
9. Az SQL visszaállítása a DPMDB
10. A felügyeleti parancssorból az új kiszolgáló CD-ről Microsoft Azure Backup a telepítési hely és a bin mappa

    Elérési út példája: C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\"

11. Az Azure Backup szolgáltatásban futtassa a DPMSYNC-SYNC parancsot

    Ha a régiek áthelyezése helyett új lemezeket adott hozzá a DPM-tárolóhoz, akkor futtassa a DPMSYNC-Reallocatereplica

## <a name="network-connectivity"></a>Hálózati kapcsolat

Azure Backup Server a termék sikeres működéséhez kapcsolódnia kell a Azure Backup szolgáltatáshoz. Annak ellenőrzéséhez, hogy a számítógép rendelkezik-e az Azure- ```Get-DPMCloudConnection``` kapcsolattal, használja a parancsmagot a Azure Backup Server PowerShell-konzolon. Ha a parancsmag kimenete igaz, akkor a kapcsolat létezik, máskülönben nincs kapcsolat.

Ugyanakkor az Azure-előfizetésnek kifogástalan állapotban kell lennie. Az előfizetés állapotának megállapításához és a kezeléséhez jelentkezzen be az [előfizetési portálra](https://account.windowsazure.com/Subscriptions).

Ha már ismeri az Azure-kapcsolat állapotát és az Azure-előfizetést, az alábbi táblázat segítségével megtudhatja, milyen hatással van a biztonsági mentési/helyreállítási funkciókra.

| Kapcsolat állapota | Azure-előfizetés | Biztonsági mentés az Azure-ba | Biztonsági mentés lemezre | Visszaállítás az Azure-ból | Visszaállítás lemezről |
| --- | --- | --- | --- | --- | --- |
| Csatlakozva |Aktív |Engedélyezett |Engedélyezett |Engedélyezett |Engedélyezett |
| Csatlakozva |Elévült |Leállítva |Leállítva |Engedélyezett |Engedélyezett |
| Csatlakozva |Felfüggesztést |Leállítva |Leállítva |Leállított és Azure helyreállítási pontok törölve |Leállítva |
| Elveszett kapcsolat > 15 nap |Aktív |Leállítva |Leállítva |Engedélyezett |Engedélyezett |
| Elveszett kapcsolat > 15 nap |Elévült |Leállítva |Leállítva |Engedélyezett |Engedélyezett |
| Elveszett kapcsolat > 15 nap |Felfüggesztést |Leállítva |Leállítva |Leállított és Azure helyreállítási pontok törölve |Leállítva |

### <a name="recovering-from-loss-of-connectivity"></a>Helyreállítás a kapcsolat elvesztése miatt

Ha olyan tűzfallal vagy proxyval rendelkezik, amely megakadályozza az Azure-hoz való hozzáférést, engedélyeznie kell a következő tartományi címeket a tűzfal/proxy profilban:

* `http://www.msftncsi.com/ncsi.txt`
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

Ha az Azure-hoz való kapcsolódást visszaállították a Azure Backup Server gépre, az elvégezhető műveleteket az Azure-előfizetés állapota határozza meg. A fenti táblázat tartalmazza azokat a műveleteket, amelyeket a gép a csatlakozás után engedélyez.

### <a name="handling-subscription-states"></a>Előfizetési állapotok kezelésére

Az Azure-előfizetés *lejárt* vagy kiépített állapotból is *aktív* állapotba kerülhet. Azonban ez hatással van a termék viselkedésére, miközben az állapot nem *aktív*:

* A felépített előfizetés elveszti a funkciót a kiépített időszakra vonatkozóan. Az *aktív*állapot bekapcsolásakor a Backup/Restore termék funkcióit újra kell indítani. A helyi lemezen lévő biztonsági másolati adatok is beolvashatók, ha egy megfelelően nagy megőrzési időtartammal lettek tárolva. Az Azure-ban tárolt biztonsági mentési adatszolgáltatások azonban visszavonhatatlanul elvesznek , ha az előfizetés kiépített állapotba kerül.
* A *lejárt* előfizetések csak akkor veszítik el a funkcionalitást, ha még nem történt meg az *aktív* működés. A rendszer nem futtatja az előfizetés lejártára ütemezett biztonsági mentéseket.

## <a name="upgrade-mabs"></a>MABS frissítése

A MABS frissítéséhez kövesse az alábbi eljárásokat.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Frissítés a MABS v2-ről v3-re

> [!NOTE]
>
> A MABS v2 nem előfeltétel a MABS v3 telepítéséhez. A MABS v3-ra azonban csak a MABS v2-ből lehet frissíteni.

A MABS frissítéséhez kövesse az alábbi lépéseket:

1. Ha a MABS v2-ről MABS v3-re szeretne frissíteni, frissítse az operációs rendszert Windows Server 2016 vagy Windows Server 2019 rendszerre, ha szükséges.

2. Frissítse a kiszolgálót. A lépések hasonlóak a [telepítéshez](#install-and-upgrade-azure-backup-server). Az SQL-beállítások esetében azonban lehetőség van az SQL-példány SQL 2017-re való frissítésére, vagy az SQL Server 2017 saját példányának használatára.

   > [!NOTE]
   >
   > Ne lépjen ki az SQL-példány frissítésekor, a kilépés eltávolítja az SQL Reporting-példányt, ezért a MABS újrafrissítésére tett kísérlet sikertelen lesz.

   Fontos Tudnivaló:

   > [!IMPORTANT]
   >
   >  Az SQL 2017-frissítés részeként biztonsági mentést készítünk az SQL-titkosítási kulcsokról, és eltávolítjuk a Reporting Services szolgáltatást. Az SQL Server verziófrissítése után a Reporting Service (14.0.6827.4788) telepítve van & a titkosítási kulcsokat a rendszer visszaállítja.
   >
   > Az SQL 2017 manuális konfigurálásakor tekintse meg az *SSRS-konfiguráció az sql 2017* használatával című szakaszt a telepítési utasítások szakaszban.

3. Frissítse a védelmi ügynököket a védett kiszolgálókon.
4. A biztonsági mentéseket az üzemi kiszolgálók újraindítása nélkül kell folytatni.
5. Most már megkezdheti az adatok védelmét. Ha modern biztonsági másolati tárhelyre frissít, a védelem alatt kiválaszthatja azokat a köteteket, amelyeken a biztonsági másolatokat tárolni kívánja, és a kiépített terület területen is megkeresheti. [További információk](backup-mabs-add-storage.md).

## <a name="troubleshooting"></a>Hibaelhárítás

Ha Microsoft Azure Backup kiszolgáló hibával leáll a telepítési fázisban (vagy biztonsági mentés vagy visszaállítás), további információért tekintse meg ezt a hibakódot tartalmazó [dokumentumot](https://support.microsoft.com/kb/3041338) .
[Azure Backup kapcsolódó gyakori kérdések](backup-azure-backup-faq.md) is megtekinthetők

## <a name="next-steps"></a>További lépések

Részletes információkat a [környezet DPM való előkészítéséről](https://technet.microsoft.com/library/hh758176.aspx) a Microsoft TechNet webhelyén talál. Emellett olyan támogatott konfigurációkról is tartalmaz információkat, amelyeken Azure Backup Server telepíthető és használható. Számos művelet végrehajtásához több [PowerShell](https://docs.microsoft.com/powershell/module/dataprotectionmanager/?view=systemcenter-ps-2016) -parancsmagot is használhat.

Ezekkel a cikkekkel részletesebben megismerheti a munkaterhelés-védelmet Microsoft Azure Backup kiszolgáló használatával.

* [SQL Server biztonsági mentés](backup-azure-backup-sql.md)
* [SharePoint Server biztonsági másolat](backup-azure-backup-sharepoint.md)
* [Másodlagos kiszolgáló biztonsági mentése](backup-azure-alternate-dpm-server.md)
