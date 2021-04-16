---
title: Azure Backup Server telepítése az Azure Stacken
description: Ebből a cikkből megtudhatja, hogyan használhatja a Azure Backup Server a számítási feladatok védelméhez vagy biztonsági Azure Stack.
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: d7c685a16db50e51a54387dde49ffb137fcfd626
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519476"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Azure Backup Server telepítése az Azure Stacken

Ez a cikk bemutatja, hogyan telepíthet Azure Backup Server-Azure Stack. A Azure Backup Server szolgáltatásként (IaaS) futó számítási feladatok, például a felhőben futó virtuális gépek védelmét Azure Stack. A számítási feladatok Azure Backup Server használatának előnye, hogy egyetlen konzolról kezelheti a számítási feladatok védelmét.

> [!NOTE]
> A biztonsági funkciókkal kapcsolatos további információkért tekintse meg Azure Backup [biztonsági funkciók dokumentációját.](backup-azure-security-feature.md)
>

## <a name="azure-backup-server-protection-matrix"></a>Az Azure Backup Server védelmi mátrixa

Azure Backup Server az alábbi Azure Stack virtuális gépek számítási feladatait védi.

| Védett adatforrás | Védelem és helyreállítás |
| --------------------- | ----------------------- |
| Windows Server féléves csatorna – Datacenter/Enterprise/Standard | Kötetek, fájlok, mappák |
| Windows Server 2016 – Datacenter/Enterprise/Standard | Kötetek, fájlok, mappák |
| Windows Server 2012 R2 – Datacenter/Enterprise/Standard | Kötetek, fájlok, mappák |
| Windows Server 2012 – Datacenter/Enterprise/Standard | Kötetek, fájlok, mappák |
| Windows Server 2008 R2 – Datacenter/Enterprise/Standard | Kötetek, fájlok, mappák |
| SQL Server 2016 | Adatbázis |
| SQL Server 2014 | Adatbázis |
| SQL Server 2012 SP1 | Adatbázis |
| SharePoint 2016 | Farm, adatbázis, előtere, webkiszolgáló |
| SharePoint 2013 | Farm, adatbázis, előtere, webkiszolgáló |
| SharePoint 2010 | Farm, adatbázis, előtere, webkiszolgáló |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>A Azure Backup Server előfeltételei

Vegye figyelembe az ebben a szakaszban található javaslatokat a Azure Backup Server telepítésekor a Azure Stack környezetben. A Azure Backup Server telepítő ellenőrzi, hogy a környezet megfelel-e a szükséges előfeltételeknek, de időt takaríthat meg a telepítés előtti előkészületekkel.

### <a name="determining-size-of-virtual-machine"></a>A virtuális gép méretének meghatározása

Ha egy Azure Backup Server virtuális gépen Azure Stack futtatni, használja az A2 vagy nagyobb méretet. Ha segítségre van szüksége a virtuális gép méretének kiválasztásához, töltse le a [virtuálisgép Azure Stack méret-kalkulátort.](https://www.microsoft.com/download/details.aspx?id=56832)

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Virtuális hálózatok virtuális Azure Stack virtuális gépeken

Az egy számítási feladatban használt Azure Stack azonos Azure-beli virtuális hálózathoz és Azure-előfizetéshez kell tartozni.

### <a name="azure-backup-server-vm-performance"></a>Azure Backup Server virtuális gép teljesítménye

Ha más virtuális gépekkel van megosztva, a tárfiók mérete és az IOPS-korlátok hatással Azure Backup Server virtuális gép teljesítményére. Ezért érdemes külön tárfiókot használnia az Azure Backup Server virtuális géphez. A Azure Backup futó Azure Backup Server ideiglenes tárterületre van szüksége a következőhöz:

- saját használat (gyorsítótár helye),
- a felhőből visszaállított adatok (helyi előkészítési terület)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Ideiglenes Azure Backup tároló konfigurálása

Minden Azure Stack virtuális géphez ideiglenes lemeztároló áll rendelkezésre, amely kötetként érhető el a felhasználó `D:\` számára. Az alkalmazás által Azure Backup helyi előkészítési terület konfigurálható úgy, hogy a helyen legyen, a gyorsítótár helye pedig `D:\` `C:\` a helyen. Így nem szükséges tárterületet eltárni a virtuális géphez csatlakoztatott adatlemezek Azure Backup Server el.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Biztonsági mentési adatok tárolása helyi lemezen és az Azure-ban

Azure Backup Server biztonsági mentési adatokat a virtuális géphez csatolt Azure-lemezeken tárolja a működés helyreállítása érdekében. Miután a lemezek és a tárterület csatlakoztatva van a virtuális géphez, a Azure Backup Server felügyeli a tárterületet. A biztonsági másolatok adattárolásának mennyisége az egyes virtuális gépekhez csatolt lemezek [számától és méretétől Azure Stack függ.](/azure-stack/user/azure-stack-storage-overview) A virtuális Azure Stack méretei a virtuális géphez csatolható lemezek maximális számában vannak. Az A2 például négy lemez. Az A3 nyolc lemez. Az A4 16 lemez. A lemezek mérete és száma határozza meg ismét a teljes biztonsági mentési tárolókészletet.

> [!IMPORTANT]
> Az operatív **helyreállítási** (biztonsági mentési) adatokat nem szabad öt napnál tovább Azure Backup Server csatlakoztatott lemezeken megőrizni.
>

A biztonsági mentési adatok Azure-ban való tárolása csökkenti a biztonsági mentési infrastruktúrát a Azure Stack. Ha az adatok öt napnál régiek, akkor az Azure-ban kell tárolni.

A biztonsági mentési adatok Azure-ban való tárolására hozzon létre vagy használjon egy Recovery Services-tárolót. Amikor a számítási feladat biztonsági Azure Backup Server készül, konfigurálnia kell [a Recovery Services-tárolót.](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault) A konfigurálás után a biztonsági mentési feladat minden futtatásakor létrejön egy helyreállítási pont a tárolóban. Minden Recovery Services-tároló legfeljebb 9999 helyreállítási ponttal rendelkezik. A létrehozott helyreállítási pontok számától és megőrzésének mértékétől függően sok évig megőrizheti a biztonsági mentési adatokat. Létrehozhat például havi helyreállítási pontokat, és öt évig megőrizheti őket.

### <a name="scaling-deployment"></a>Üzemelő példány skálázása

Ha skálázni szeretné az üzemelő példányát, a következő lehetőségek közül választhat:

- Horizontális felskálás – Növelje a virtuális gép méretét Azure Backup Server A sorozatúról D sorozatúra, és növelje a helyi tárterületet a virtuális gépre [vonatkozó Azure Stack szerint.](/azure-stack/user/azure-stack-manage-vm-disks)
- Adatok kiszervezése – régebbi adatokat küldhet az Azure-ba, és csak a legújabb adatokat őrizze meg a virtuális Azure Backup Server.
- Horizontális felskálás – További Azure Backup adhat hozzá a kiszolgálókhoz a számítási feladatok védelme érdekében.

### <a name="net-framework"></a>.NET-keretrendszer

.NET-keretrendszer 3.5 SP1 vagy újabb verziónak kell telepítve lennie a virtuális gépen.

### <a name="joining-a-domain"></a>Tartományhoz való csatlakozás

A Azure Backup Server virtuális gépnek tartományhoz kell csatlakozva lennie. A rendszergazdai jogosultságokkal rendelkező tartományi Azure Backup Server kell telepítenie a virtuális gépre.

## <a name="using-an-iaas-vm-in-azure-stack"></a>IaaS virtuális gép használata a Azure Stack

Amikor kiszolgálót választ a Azure Backup Server, kezdje a Windows Server 2012 R2 Datacenter vagy a Windows Server 2016 Datacenter katalógusának rendszerképét. A Create [your first Windows virtual machine in the Azure Portal](../virtual-machines/windows/quick-create-portal.md?toc=/azure/virtual-machines/windows/toc.json)(Az első Windows rendszerű virtuális gép létrehozása a(Azure Portal) cikkben található oktatóanyag az ajánlott virtuális gép első lépésekkel való ismerkedését bemutató oktatóanyagot tartalmaz. A kiszolgálói virtuális gép (VM) ajánlott minimális követelményei a következőek: A2 Standard két maggal és 3,5 GB RAM-mal.

A számítási feladatok Azure Backup Server számos árnyaltság van. A [MABS védelmi mátrixa](./backup-mabs-protection-matrix.md) segít ezeknek az árnyalásnak a magyarázatában. A gép üzembe helyezése előtt olvassa el ezt a cikket teljesen.

> [!NOTE]
> Azure Backup Server dedikált, egycélú virtuális gépeken való futtatásra tervezték. A következő gombra Azure Backup Server telepíthető:
>
> - Tartományvezérlőként futó számítógépre
> - Olyan számítógépre, amelyen telepítve van az Alkalmazáskiszolgáló szerepkör
> - Olyan számítógépre, amelyen az Exchange Server fut
> - Olyan számítógép, amely egy fürt csomópontja

Mindig csatlakozzon Azure Backup Server tartományhoz. Ha másik tartományba kell Azure Backup Server, először telepítse a Azure Backup Server, majd csatlakozzon az új tartományhoz. A Azure Backup Server után nem helyezheti át új tartományba.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Tárreplikáció beállítása

A Recovery Services-tároló tárreplikációs beállításával választhat a georedundáns és a helyileg redundáns tárolás között. Alapértelmezés szerint a Recovery Services-tárolók georedundáns tárolást használnak. Ha ez a tároló az elsődleges tároló, a tárolási beállítást hagyja georedundáns tárolásra beállítva. Válassza a helyileg redundáns tárolást, ha olcsóbb, kevésbé tartós megoldást szeretne. A georedundáns, a helyileg redundáns [és](../storage/common/storage-redundancy.md#locally-redundant-storage)a zónaredundáns tárolási lehetőségekről további információt az Azure Storage [replikációs](../storage/common/storage-redundancy.md#zone-redundant-storage) [áttekintésében talál.](../storage/common/storage-redundancy.md) [](../storage/common/storage-redundancy.md#geo-redundant-storage)

A tárreplikációs beállítás szerkesztése:

1. Válassza ki a tárolót a tároló irányítópultjának és a Beállítások menü megnyitásához. Ha a **Beállítások** menü nem nyílik meg, válassza az **Összes beállítás lehetőséget** a tároló irányítópultján.
2. A Beállítások **menüben** válassza a Biztonsági **mentési infrastruktúra** biztonsági mentési  >  **konfigurációja** lehetőséget a Biztonsági **mentés konfigurációja menü megnyitásához.** A Biztonsági **mentés konfigurációja menüben** válassza ki a tároló replikációs beállítását.

    ![A Backup-tárolók listája](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>A Azure Backup Server letöltése

A telepítőt kétféleképpen töltheti Azure Backup Server le. Az alkalmazástelepítőt Azure Backup Server Microsoft [letöltőközpontból töltheti le.](https://www.microsoft.com/download/details.aspx?id=55269) A helyreállítási tár Azure Backup Server közben is letöltheti a telepítőt. A következő lépések végigveszik a telepítő letöltésének lépéseit a Azure Portal recovery services-tároló konfigurálása során.

1. A virtuális Azure Stack jelentkezzen [be Az Azure-előfizetésbe a Azure Portal.](https://portal.azure.com/)
2. A bal oldali menüben válassza a **Minden szolgáltatás lehetőséget.**

    ![Válassza a Minden szolgáltatás lehetőséget a főmenüben](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. A Minden **szolgáltatás párbeszédpanelen** írja be a *Következőt: Recovery Services.* A gépelés megkezdése után a bemenet szűri az erőforrások listáját. Ha ezt látja, válassza a **Recovery Services-tárolók lehetőséget.**

    ![Írja be a Recovery Services parancsot a Minden szolgáltatás párbeszédpanelen](./media/backup-mabs-install-azure-stack/all-services.png)

    Megjelenik az előfizetésben található Recovery Services-tárolók listája.

4. A Recovery Services-tárolók listájából válassza ki a tárolót az irányítópult megnyitásához.

    ![Válassza ki a tárolót az irányítópult megnyitásához](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. A tároló Első lépések menüjében válassza a **Biztonsági** mentés lehetőséget az Első lépések varázsló megnyitásához.

    ![Biztonsági mentés – első lépések](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    Megnyílik a Biztonsági mentés menü.

    ![Backup-goals-default-opened](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. A biztonsági mentés menüjében a **Hol futnak** a számítási feladatok? menüből válassza a **Helyszíni lehetőséget.** A Miről szeretne biztonsági másolatot **készíteni?** legördülő menüben válassza ki azokat a számítási feladatokat, amelyek védelmét a Azure Backup Server. Ha nem biztos benne, hogy mely számítási feladatokat válassza, válassza a **Hyper-V virtuális** gép Virtual Machines az **Infrastruktúra előkészítése lehetőséget.**

    ![helyszíni és számítási feladatok mint célok](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    Megnyílik **az Infrastruktúra** előkészítése menü.

7. Az Infrastruktúra **előkészítése menüben** válassza a Letöltés **lehetőséget** egy weblap megnyitásához, és töltse le Azure Backup Server fájlokat.

    ![Az Első lépések varázsló módosítása](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    Megnyílik a Microsoft weboldala, amely az alkalmazás letölthető fájljait Azure Backup Server tartalmazza.

8. A Microsoft Azure Backup Server letöltési oldalán válasszon nyelvet, majd válassza a **Letöltés lehetőséget.**

    ![Megnyílik a letöltőközpont](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. A Azure Backup Server telepítő nyolc fájlból áll – egy telepítőből és hét .bin fájlból. Jelölje **be a Fájlnév jelölőnégyzetet** az összes szükséges fájl kiválasztásához, majd válassza a **Tovább lehetőséget.** Töltse le az összes fájlt ugyanoda a mappába.

    ![Letöltőközpont, kiválasztott fájlok](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    Az összes telepítőfájl letöltési mérete nagyobb 3 GB-nál. Egy 10 Mbps sebességű letöltési hivatkozáson az összes telepítőfájl letöltése akár 60 percet is igénybe vehet. A fájlok a megadott letöltési helyre tölthetők le.

## <a name="extract-azure-backup-server-install-files"></a>Fájlok Azure Backup Server és telepítése

Miután letöltötte az összes fájlt a Azure Stack virtuális gépére, a letöltési helyre kell továbbmenni. A fájlok telepítésének első Azure Backup Server a fájlok kibontása.

![MABS-telepítő letöltése](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. A telepítés elkezdése érdekében a letöltött fájlok listájából válassza a **MicrosoftAzureBackupserverInstaller.exe.**

    > [!WARNING]
    > A telepítőfájlok kinyeréhez legalább 4 GB szabad terület szükséges.
    >

2. A Azure Backup Server folytatáshoz válassza **a Tovább** lehetőséget.

    ![Microsoft Azure Backup telepítővarázsló](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Válassza ki a fájl Azure Backup Server elérési útját, majd kattintson a **Tovább gombra.**

   ![A fájlok célhelyének kiválasztása](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Ellenőrizze a kinyerési helyet, majd válassza a **Kinyerés lehetőséget.**

   ![A kinyerési hely ellenőrzése](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. A varázsló kibontja a fájlokat, és beolvassa a telepítési folyamatot.

   ![A varázsló fájlokat bont ki](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. A kinyerési folyamat befejezése után válassza a **Befejezés lehetőséget.** Alapértelmezés szerint a **Végrehajtási setup.exe** van kiválasztva. Ha a Befejezés **gombra Setup.exe,** a Microsoft Azure Backup telepíti a kiszolgálót a megadott helyre.

   ![A telepítő kinyeri Microsoft Azure Backup Server-fájlokat](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>A szoftvercsomag telepítése

Az előző lépésben válassza a **Befejezés** lehetőséget a kinyerési fázisból való kilépéshez, majd indítsa el a Azure Backup Server varázslót.

![Microsoft Azure Backup telepítővarázsló indítása](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Azure Backup Server megosztja a kódot a Data Protection Manager. A DPM-re és a DPM-re Data Protection Manager hivatkozásokat fog látni a Azure Backup Server telepítőjében. Bár Azure Backup Server termékek Data Protection Manager termékek, ezek a termékek szorosan kapcsolódnak egymáshoz.

1. A telepítővarázsló elindításához válassza a Microsoft Azure Backup **lehetőséget.**

   ![A Microsoft Azure Backup kiválasztása](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. Az **Üdvözlőképernyőn** válassza a Tovább **lehetőséget.**

    ![Azure Backup Server – Üdvözöljük](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. Az **Előfeltétel-ellenőrzések** képernyőn válassza az Ellenőrzés lehetőséget annak megállapításához, hogy teljesülnek-e a Azure Backup Server hardver- és szoftverkövetelményei. 

    ![Azure Backup Server – Előfeltételek ellenőrzése](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Ha a környezete rendelkezik a szükséges előfeltételekkel, megjelenik egy üzenet, amely jelzi, hogy a gép megfelel a követelményeknek. Kattintson a **Tovább** gombra.  

    ![Azure Backup Server – Megfelelt az előfeltételek ellenőrzése](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Ha a környezet nem felel meg a szükséges előfeltételeknek, a problémák meg lesznek adva. A nem teljesülő előfeltételek a DpmSetup.log fájlban is szerepelnek. Hárítsa el az előfeltételként szükséges hibákat, majd futtassa újra a **Check Again (Ellenőrzés) et.** A telepítés addig nem folytatódik, amíg az összes előfeltétel nem teljesül.

    ![Azure Backup Server – a telepítési előfeltételek nem teljesülnek](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. Microsoft Azure Backup Server használatához SQL Server. A Azure Backup Server telepítőcsomag a megfelelő bináris SQL Server tartalmazza. Ha saját SQL-telepítést szeretne használni, azt is használhatja. Az ajánlott megoldás azonban az, hogy a telepítő hozzáadja a SQL Server. Ha ellenőriznie kell, hogy a választás működik-e a környezetével, válassza az **Ellenőrzés és telepítés lehetőséget.**

   > [!NOTE]
   > Azure Backup Server távoli kapcsolatpéldánysal nem SQL Server működni. Az alkalmazás által használt Azure Backup Server helyinek kell lennie.
   >

    ![Azure Backup Server – SQL-beállítások](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Ha az ellenőrzés után a virtuális gép rendelkezik a virtuális gép telepítéséhez szükséges Azure Backup Server válassza a Tovább **lehetőséget.**

    ![Azure Backup Server – a követelmények teljesülnek](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Ha hiba történik a számítógép újraindítására vonatkozó javaslat esetén, indítsa újra a gépet. A gép újraindítása után indítsa újra a telepítőt, és amikor az **SQL-beállítások** képernyőre lép, válassza a **Check Again (Ellenőrzés újra) lehetőséget.**

5. A Telepítési **beállítások lapon adjon** meg egy helyet a kiszolgálófájlok Microsoft Azure Backup, majd válassza a Tovább **lehetőséget.**

    ![Adja meg a fájlok telepítésének helyét](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    Az Azure-ba való biztonságimenthez szükség van az új helyre. Győződjön meg arról, hogy az új hely mérete megegyezik az Azure-ba tervezett adatok legalább 5%-ának megfelelő mérettel. A lemezvédelemhez a telepítés befejezése után külön lemezeket kell konfigurálni. További információ a tárolókészletről: [Adattárolás előkészítése.](/system-center/dpm/plan-long-and-short-term-data-storage)

6. A Biztonsági **beállítások képernyőn** adjon meg egy erős jelszót a korlátozott helyi felhasználói fiókokhoz, majd válassza a Tovább **lehetőséget.**

    ![Biztonsági beállítások képernyő](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. A Microsoft Update válassza ki, hogy **szeretné-e** használni  a Microsoft Update frissítéseket, majd válassza a Tovább **lehetőséget.**

   > [!NOTE]
   > Javasoljuk, hogy Windows Update a Microsoft Update, amely biztonsági és fontos frissítéseket biztosít a Windowshoz és más termékekhez, például a Microsoft Azure Backup Serverhez.
   >

    ![Microsoft Update Opt-In képernyő](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Tekintse át *a Beállítások összegzése lehetőséget, és* válassza a Telepítés **lehetőséget.**

    ![Beállítások összegzése](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    Amikor Azure Backup Server telepítést, a telepítő azonnal elindítja a Microsoft Azure Recovery Services-ügynök telepítőjét.

9. Megnyílik Microsoft Azure Recovery Services-ügynök telepítője, és ellenőrzi az internetkapcsolatot. Ha van internetkapcsolat, folytassa a telepítést. Ha nincs kapcsolat, adja meg a proxy adatait az internethez való csatlakozáshoz. A proxybeállítások megadása után válassza a Tovább **lehetőséget.**

    ![Proxy konfigurálása](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. Az Microsoft Azure Recovery Services-ügynök telepítéséhez válassza a **Telepítés lehetőséget.**

    ![Ügynök telepítése](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    A Microsoft Azure Recovery Services-ügynök , más néven Azure Backup ügynök konfigurálja a Azure Backup Server a Recovery Services-tárolóhoz. A konfigurálás után Azure Backup Server a rendszer mindig ugyanannak a Recovery Services-tárolónak a biztonsági adatait fogja biztonsági adatokat.

11. Miután a Microsoft Azure Recovery Services-ügynök telepítése  befejeződik, válassza a Tovább lehetőséget a következő fázis elkezdéséhez: a Azure Backup Server regisztrálása a Recovery Services-tárolóban.

    ![Az ügynök telepítése sikeresen befejeződött](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    A telepítő elindítja a **Kiszolgáló regisztrálása varázslót.**

12. Váltson az Azure-előfizetésre és a Recovery Services-tárolóra. Az Infrastruktúra **előkészítése menüben** válassza a Letöltés **lehetőséget** a tároló hitelesítő adatainak letöltéséhez. Ha  a 2. lépésben található **Letöltés** gomb nem aktív, a gomb aktiválásához válassza a Már letöltött vagy a legújabb Azure Backup Server használata lehetőséget. A tároló hitelesítő adatai arra a helyre tölthetők le, ahol a letöltéseket tárolja. Vegye figyelembe ezt a helyet, mert a következő lépéshez szüksége lesz rá.

    ![A tároló hitelesítő adatainak letöltése](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. A Tároló **azonosítása menüben** válassza a **Tallózás lehetőséget** a Helyreállítási tár hitelesítő adatainak megkeresése érdekében.

    ![Tárolóazonosítási menü](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    A Tároló **hitelesítő adatainak kiválasztása** párbeszédpanelen nyissa meg a letöltési helyet, válassza ki a tároló hitelesítő adatait, majd válassza a Megnyitás **lehetőséget.**

    A hitelesítő adatok elérési útja megjelenik a Tároló azonosítása menüben. A **Titkosítási** beállítások beállításhoz válassza a **Tovább lehetőséget.**

14. A **Titkosítási beállítás párbeszédpanelen** adjon meg egy jelszót a biztonsági mentés titkosítására, és egy helyet a jelszó tárolására, majd válassza a Tovább **lehetőséget.**

    ![Titkosítási beállítások](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    Saját jelszót is meg lehet adni, vagy a jelszógenerátor használatával létrehozhat egyet. A jelszó az Ön sajátja, és a Microsoft nem menti és nem kezeli ezt a jelszót. A katasztrófákra való felkészüléshez mentse a jelszót egy elérhető helyre.

    A Tovább **gombra** való Azure Backup Server a rendszer regisztrálja a helyreállítási tárban. A telepítő folytatja a SQL Server és a Azure Backup Server.

    ![A telepítő telepíti az SQL-t és a Azure Backup Server](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. Amikor a telepítő befejeződik, az **Állapot** állapot azt jelzi, hogy az összes szoftver telepítése sikeresen megtörtént.

    ![A szoftver telepítése sikeresen megtörtént](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    A telepítés befejezése után a Azure Backup Server konzol és Azure Backup Server PowerShell-ikonok a kiszolgáló asztalán jelennek meg.

## <a name="add-backup-storage"></a>Biztonsági mentési tár hozzáadása

Az első biztonsági másolat a virtuális géphez csatolt tárolón Azure Backup Server található. További információ a lemezek hozzáadásáról: [Modern Backup-tároló hozzáadása.](/system-center/dpm/add-storage)

> [!NOTE]
> Akkor is hozzá kell adni a biztonsági mentési tárat, ha az Azure-ba szeretne adatokat küldeni. A Azure Backup Server recovery services-tároló az adatok  második példányát, a helyi tár pedig az első (és kötelező) biztonsági másolatát tartalmazza.
>
>

## <a name="network-connectivity"></a>Hálózati kapcsolat

Azure Backup Server termék sikeres használatához Azure Backup kapcsolatra van szükség a Azure Backup szolgáltatáshoz. Annak ellenőrzéséhez, hogy a gép rendelkezik-e kapcsolattal az Azure-ral, használja a parancsmagot ```Get-DPMCloudConnection``` Azure Backup Server PowerShell-konzolon. Ha a parancsmag kimenete IGAZ, akkor a kapcsolat létezik, ellenkező esetben nincs kapcsolat.

Az Azure-előfizetésnek ugyanakkor kifogástalan állapotban kell lennie. Az előfizetés állapotának megkereséhez és kezeléséhez jelentkezzen be az előfizetési [portálra.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)

Ha már ismeri az Azure-kapcsolat és az Azure-előfizetés állapotát, az alábbi táblázat segítségével kiderítheti, hogy ez milyen hatással van a biztonsági mentési/visszaállítási funkciókra.

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


Miután az Azure-ral való kapcsolat visszaállt a Azure Backup Server, az Azure-előfizetés állapota határozza meg a elvégezhető műveleteket. Miután a kiszolgáló **csatlakoztatva** van, használja a Hálózati kapcsolat táblázatot [az](backup-mabs-install-azure-stack.md#network-connectivity) elérhető műveletek végrehajtásához.

### <a name="handling-subscription-states"></a>Előfizetési államok kezelése

Az Azure-előfizetések Lejárt vagy  Megszüntetett állapotról Aktív állapotra *is átírhatóak.*  Az előfizetés állapota nem *aktív:*

- Amíg egy előfizetés ki van *építve,* a funkciói elvesznek. Az előfizetés aktívra *való visszaállítása* visszaállítja a biztonsági mentési/visszaállítási funkciót. Ha a helyi lemezen lévő biztonsági mentési adatokat kellően hosszú megőrzési időtartammal őrizték meg, akkor a biztonsági mentési adatok lekért adatok. Az Azure-beli biztonsági mentési adatok azonban visszavonhatatlan módon elvesznek, ha az előfizetés megszüntetett *állapotba* lép.
- Amíg egy előfizetés *lejárt,* funkcióját elveszíti. Az ütemezett biztonsági mentések nem futnak le, amíg egy előfizetés *lejárt.*

## <a name="troubleshooting"></a>Hibaelhárítás

Ha Microsoft Azure Backup a telepítési fázisban (vagy biztonsági mentéskor vagy visszaállításkor) hibák lépnek fel, tekintse meg a [hibakódok dokumentációját.](https://support.microsoft.com/kb/3041338)
A kapcsolódó gyakori Azure Backup [is hivatkozhat](backup-azure-backup-faq.yml)

## <a name="next-steps"></a>Következő lépések

A Környezet előkészítése [a DPM-hez](/system-center/dpm/prepare-environment-for-dpm)cikk a támogatott konfigurációkról Azure Backup Server tartalmaz.

A következő cikkekből jobban megértheti a számítási feladatok védelmét a Microsoft Azure Backup Server használatával.

- [SQL Server biztonsági mentés](./backup-mabs-sql-azure-stack.md)
- [SharePoint-kiszolgáló biztonsági mentése](./backup-mabs-sharepoint-azure-stack.md)
- [Másodlagos kiszolgáló biztonsági mentése](backup-azure-alternate-dpm-server.md)
