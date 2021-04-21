---
title: Fájlkiszolgálók védelmének biztosítása az Azure Site Recovery használatával
description: Ez a cikk bemutatja, hogyan biztosítható a fájlkiszolgálók védelme az Azure Site Recovery használatával
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: sharrai
ms.custom: mvc
ms.openlocfilehash: 5209e715fab422a50e31810b5eb0d370d5fc61cd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792524"
---
# <a name="protect-a-file-server-by-using-azure-site-recovery"></a>Fájlkiszolgálók védelmének biztosítása az Azure Site Recovery használatával 

Az [Azure Site Recovery](site-recovery-overview.md) működőképes állapotban tartja az üzleti alkalmazásokat a tervezett és nem tervezett leállások idején, így segít a vállalatoknak az üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégia megvalósításában. A Site Recovery felügyeli és koordinálja a helyszíni gépek és az Azure-beli virtuális gépek vészhelyreállítását. A vészhelyreállítás magában foglalja a replikációt, a feladatátvételt és a különféle számítási feladatok helyreállítását is.

Ez a cikk bemutatja, hogyan biztosítható a fájlkiszolgálók védelme a Site Recovery használatával, valamint további ajánlásokat tartalmaz a különböző környezetekre vonatkozóan. 

- [Azure IaaS-fájlkiszolgáló gépek replikálása](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Helyszíni fájlkiszolgáló replikálása a Site Recovery használatával](#replicate-an-on-premises-file-server-by-using-site-recovery)

## <a name="file-server-architecture"></a>Fájlkiszolgáló architektúrája
A nyílt forráskódú fájlmegosztó rendszerek célja, hogy olyan környezetet biztosítsanak, amelyben a földrajzilag eltérő helyen található felhasználók csoportja hatékony együttműködéssel dolgozhat a fájlokon, miközben az integritási követelményeik betartása is biztosítva van. Egy tipikus helyszíni fájlkiszolgáló ökoszisztéma, amely nagy számú párhuzamos felhasználót, valamint nagy számú tartalomelemet támogat, Elosztott fájlrendszer-replikációt (DFSR) használ az ütemezéshez és a sávszélesség-szabályozáshoz. 

Az Elosztott fájlrendszer-replikáció a Távoli különbözeti tömörítés (RDC) néven ismert tömörítési algoritmust használja, amely hatékonyan alkalmazható a fájlok frissítésére a korlátozott sávszélességű hálózatokon. Észleli az adatok beszúrását, eltávolítását és átcsoportosítását a fájlokban. A DFSR csak a módosult fájlblokkokat replikálhatja a fájlok frissítése során. Emellett léteznek olyan fájlkiszolgáló-környezetek is, ahol a napi rendszerességű biztonsági mentéseket csúcsidőn kívül végzi el a rendszer, ezzel kiszolgálva a vészhelyreállításra vonatkozó követelményeket. A DFSR nincs implementálva.

A következő ábra a fájlkiszolgáló-környezetet mutatja be implementált DFSR-rel.
        
![DFSR-architektúra](media/site-recovery-file-server/dfsr-architecture.JPG)

Az előző ábrán több fájlkiszolgáló, ún. tag vesz részt aktívan a fájlok replikációjában egy replikációs csoporton belül. A replikált mappa tartalma minden ügyfél számára elérhető, amely kérést küld a tagok egyikének, még akkor is ha egy tag offline állapotra vált.

## <a name="disaster-recovery-recommendations-for-file-servers"></a>Vészhelyreállítással kapcsolatos javaslatok fájlkiszolgálókhoz

* **Fájlkiszolgáló replikálása a Site Recovery használatával**: A fájlkiszolgálók replikálhatók az Azure-ba a Site Recovery használatával. Ha egy vagy több helyszíni fájlkiszolgáló nem érhető el, a helyreállítási virtuális gépek üzembe helyezhetők az Azure-ban. A virtuális gépek ezt követően képesek az ügyfelektől érkező kérések helyszíni kiszolgálására, feltéve, hogy rendelkezésre áll helyek közötti VPN-kapcsolat, és az Active Directory konfigurálva van az Azure-ban. Ezt a módszert DFSR-hez konfigurált környezetben vagy DFSR nélküli egyszerű fájlkiszolgáló-környezetben is használhatja. 

* **Elosztott fájlrendszer-replikáció (DFSR) kiterjesztése Azure IaaS virtuális gépekre**: Azokban a fürtözött fájlkiszolgáló-környezetekben, amelyekben implementálva van az elosztott fájlrendszer-replikáció, kiterjesztheti a helyi DFSR-t az Azure-ra. Ezt követően egy Azure-beli virtuális gép felveheti a fájlkiszolgálói szerepkört. 

    * A helyek közötti VPN-kapcsolat és az Active Directory függőségeinek kielégítését, valamint a DFSR beállítását követően, ha egy vagy több helyszíni fájlkiszolgáló nem érhető el, az ügyfelek csatlakozhatnak az Azure-beli virtuális géphez, amely kiszolgálja az adott kéréseket.

    * Ezt a módszert akkor használhatja, ha a virtuális gépek a Site Recovery által nem támogatott konfigurációkkal rendelkeznek. Ilyen például egy megosztott fürtlemez, amelyek gyakran használatosak a fájlkiszolgáló-környezetekben. A DFSR a közepes forgalommal rendelkező, kis sávszélességű környezetekben is kiválóan alkalmazható. Gondolja végig, mennyivel több költséggel jár egy folyamatosan működő Azure-beli virtuális gép fenntartása. 

* **Használja Azure File Sync fájlok replikálása:** Ha a felhő használatát tervezi, vagy már használ egy Azure-beli virtuális gépet, használhatja a Azure File Sync. Azure File Sync teljes körűen felügyelt felhőbeli fájlmegosztások szinkronizálását kínálja, amelyek az iparági [szabványnak](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) megfelelő Server Message Block (SMB) protokollon keresztül érhetők el. Az Azure-fájlmegosztások párhuzamosan több felhőalapú vagy helyszíni Windows, Linux vagy macOS rendszerű üzemelő példány által is csatlakoztathatóak. 

Az alábbi ábra segít meghatározni, milyen stratégiát használjon a fájlkiszolgáló-környezethez.

![Döntési fa](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-in-your-decisions-about-disaster-recovery-to-azure"></a>Az Azure-ba irányuló vészhelyreállítással kapcsolatos döntések során megfontolandó tényezők

|Környezet  |Ajánlás  |Megfontolandó szempontok |
|---------|---------|---------|
|Fájlkiszolgáló-környezet DFSR-rel vagy anélkül|   [A Site Recovery használata replikációhoz](#replicate-an-on-premises-file-server-by-using-site-recovery)   |    A Site Recovery nem támogatja a megosztott lemezfürtöket és a hálózati tárolókat (NAS). Ha a környezet ezeket a konfigurációkat használja, alkalmazza az egyéb megközelítések valamelyikét igény szerint. <br> A Site Recovery nem támogatja az SMB 3.0-s verziót. A replikált virtuális gépen csak akkor jelennek meg a módosítások, ha a fájlok módosításait azok eredeti helyén is frissíti.<br>  Site Recovery közel szinkron adatreplikációs folyamatot kínál, ezért nem tervezett feladatátvétel esetén adatvesztéssel és USN-eltéréssel kapcsolatos problémákat okozhat.
|Fájlkiszolgáló-környezet DFSR-rel     |  [A DFSR kiterjesztése egy Azure IaaS virtuális gépre](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |    A DFSR kiválóan működik a rendkívüli sávszélesség-terheltségű környezetekben. Ehhez egy folyamatosan működő Azure-beli virtuális gépre van szükség. A tervezés során a virtuális gép költségét is számításba kell venni.         |
|Azure IaaS virtuális gép     |     File Sync     |     Ha a vészhelyreállítási forgatókönyvekhez a File Syncet használja, a feladatátvétel során manuális műveletekkel kell megoldania, hogy a fájlmegosztások átlátható módon legyenek elérhetők az ügyfélszámítógép számára. A File Sync működéséhez a 445-ös portot meg kell nyitni az ügyfélszámítógépen.     |


### <a name="site-recovery-support"></a>Site Recovery támogatása
Mivel a Site Recovery replikációja alkalmazásfüggetlen, várhatóan az alábbi ajánlásokat érdemes követni a következő forgatókönyvek esetében.

| Forrás  |Egy másodlagos helyre  |Az Azure-ba
|---------|---------|---------|
|Azure|  -|Yes|
|Hyper-V|  Igen  |Yes
|VMware  |Igen|  Yes
|Fizikai kiszolgáló|  Igen  |Yes
 

> [!IMPORTANT]
> Mielőtt továbblépne a következő három módszer valamelyikére, biztosítsa az alábbi függőségeket.



**Helyek közötti kapcsolat**: A kiszolgálók közötti kommunikáció engedélyezéséhez a helyszíni hely és az Azure-hálózat között közvetlen kapcsolatot kell létesíteni. Használjon egy Azure virtuális hálózat felé irányuló, biztonságos, helyek közötti VPN-kapcsolatot, amely vészhelyreállítási helyként fog szolgálni. További információkért lásd: [Helyek közötti VPN-kapcsolat létesítése egy helyszíni hely és egy Azure virtuális hálózat között](../vpn-gateway/tutorial-site-to-site-portal.md).

**Active Directory**: A DFSR az Azure Active Directorytól függ. Ez azt jelenti, hogy a helyi tartományvezérlőkkel rendelkező Active Directory-erdő kiterjed a vészhelyreállítási helyre az Azure-ban. Még ha nem is használ DFSR-t, ha a kívánt felhasználók számára hozzáférést kell biztosítania, vagy ellenőrizni kell őket a hozzáféréshez, hajtsa végre ezeket a lépéseket. További információk: [Helyszíni Active Directory kiterjesztése az Azure-ba](./site-recovery-active-directory.md).

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Vészhelyreállítással kapcsolatos javaslatok Azure IaaS virtuális gépekhez

Az Azure IaaS virtuális gépeken üzemeltetett fájlkiszolgálók vészhelyreállításának konfigurációja és felügyelete esetén két lehetőség közül választhat attól függően, hogy át kívánja-e helyezni az adatokat az [Azure Filesba](../storage/files/storage-files-introduction.md):

* [A File Sync használata](#use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine)
* [A Site Recovery használata](#replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery)

## <a name="use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine"></a>Egy IaaS virtuális gépen üzemeltetett fájlok replikálása a File Sync használatával

Az Azure Files használatával teljes mértékben lecserélheti vagy kiegészítheti a hagyományos helyszíni fájlkiszolgálókat vagy NAS-eszközöket. Az Azure-fájlmegosztások az Azure File Sync használatával replikálhatók helyszíni vagy felhőalapú Windows-kiszolgálókra az adatok használat helyéhez közeli nagy teljesítményű és elosztott gyorsítótárazása érdekében. Az alábbi lépések egy vészhelyreállításra vonatkozó ajánlást írnak le olyan Azure-beli virtuális gépek esetében, amelyek a hagyományos fájlkiszolgálókéval azonos funkciókat látnak el:
* Számítógépek védelme a Site Recovery használatával. Kövesse az [Azure-beli virtuális gép replikálása másik Azure-régióba](azure-to-azure-quickstart.md) című útmutatóban szereplő lépéseket.
* A File Sync használatával replikálja a fájlokat a fájlkiszolgálóként működő virtuális gépről a felhőbe.
* A Site Recovery [helyreállítási terv](site-recovery-create-recovery-plans.md) funkciójának használatával adjon hozzá szkripteket [az Azure-fájlmegosztás csatlakoztatásához](../storage/files/storage-how-to-use-files-windows.md) és a megosztás eléréséhez a virtuális gépen.

A következő lépések röviden bemutatják a File Sync használatát:

1. [Hozzon létre egy tárfiókot az Azure-ban](../storage/common/storage-account-create.md?toc=/azure/storage/files/toc.json). Ha a tárfiókokhoz írásvédett georedundáns tárolási módot választ, katasztrófa esetén a másodlagos régióból olvasási hozzáféréssel fog rendelkezni az adataihoz. További információ: [Vészhelyreállítás és tárfiók feladatátvétele.](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2ffiless%2ftoc.json)
2. [Hozzon létre egy fájlmegosztást.](../storage/files/storage-how-to-create-file-share.md)
3. [Indítsa el az Azure File Sync-et](../storage/file-sync/file-sync-deployment-guide.md) az Azure-beli fájlkiszolgálón.
4. Hozzon létre egy szinkronizálási csoportot. A szinkronizálási csoporton belüli végpontokat a rendszer szinkronban tartja egymással. Egy szinkronizálási csoportnak tartalmaznia kell legalább egy felhővégpontot, amely egy Azure-fájlmegosztást képvisel. A szinkronizálási csoportnak egy kiszolgálóvégpontot is tartalmaznia kell, amely egy útvonalat képvisel egy Windows-kiszolgálón.
5. A fájlokat a rendszer az Azure-fájlmegosztás és a helyszíni kiszolgáló között mostantól szinkronban tartja.
6. Ha a helyszíni környezetben katasztrófa következik be, a [helyreállítási terv](site-recovery-create-recovery-plans.md) használatával elvégezheti a feladatátvételt. Adja hozzá a szkriptet [az Azure-fájlmegosztás csatlakoztatásához](../storage/files/storage-how-to-use-files-windows.md) és a megosztás virtuális gépen való eléréséhez.

### <a name="replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery"></a>IaaS-fájlkiszolgáló virtuális gép replikálása a Site Recovery használatával

Ha olyan helyszíni ügyfelekkel rendelkezik, amelyek hozzáférnek az IaaS-fájlkiszolgáló virtuális géphez, sorban végezze el az alábbi lépéseket. Máskülönben folytassa a 3. lépéssel.

1. Létesítsen helyek közötti VPN-kapcsolatot a helyszíni hely és az Azure-hálózat között.
2. Terjessze ki a helyszíni Active Directoryt.
3. [Állítsa be a vészhelyreállítást](azure-to-azure-tutorial-enable-replication.md) az IaaS-fájlkiszolgáló géphez egy másodlagos régióba.


A másodlagos régióba történő vészhelyreállítással kapcsolatos további információkért tekintse meg [ezt a cikket](./azure-to-azure-architecture.md).


## <a name="replicate-an-on-premises-file-server-by-using-site-recovery"></a>Helyszíni fájlkiszolgáló replikálása a Site Recovery használatával

Az alábbi lépések egy VMware virtuális gép replikációját mutatják be. A Hyper-V virtuális gépek replikációinak lépéseit [ebben az útmutatóban](./hyper-v-azure-tutorial.md) találja.

1. [Azure-erőforrások előkészítése](tutorial-prepare-azure.md) helyszíni gépek replikálásához.
2. Létesítsen helyek közötti VPN-kapcsolatot a helyszíni hely és az Azure-hálózat között. 
3. Terjessze ki a helyszíni Active Directoryt.
4. [Helyszíni VMware-kiszolgálók előkészítése.](./vmware-azure-tutorial-prepare-on-premises.md)
5. Az Azure-ba irányuló [vészhelyreállítás beállítása](./vmware-azure-tutorial.md) helyszíni virtuális gépekhez.

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>DFSR kiterjesztése egy Azure IaaS virtuális gépre

1. Létesítsen helyek közötti VPN-kapcsolatot a helyszíni hely és az Azure-hálózat között. 
2. Terjessze ki a helyszíni Active Directoryt.
3. [Fájlkiszolgáló virtuális gép létrehozása és kiépítése](../virtual-machines/windows/quick-create-portal.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) az Azure virtuális hálózaton.
Győződjön meg róla, hogy a virtuális gép ugyanahhoz az Azure virtuális hálózathoz van hozzáadva, amely kapcsolatban áll a helyszíni környezettel. 
4. Telepítse és [konfigurálja a DFSR-t](https://techcommunity.microsoft.com/t5/storage-at-microsoft/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of/ba-p/424877) Windows Serveren.
5. [Implementáljon egy elosztott fájlrendszerbeli névteret](/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).
6. Az elosztott fájlrendszerbeli névtér implementálásával a megosztott mappák éles környezetből vészhelyreállítási helyekre történő feladatátvétele az elosztott fájlrendszerbeli névtér mappacéljainak frissítésével végezhető el. Miután a rendszer replikálta az elosztott fájlrendszerbeli névtér változásait az Active Directoryn keresztül, a felhasználók átlátható módon csatlakoznak a megfelelő mappacélokhoz.

## <a name="use-file-sync-to-replicate-your-on-premises-files"></a>A File Sync használata a helyszíni fájlok replikálásához
A File Sync használatával replikálhatja a fájlokat a felhőbe. Katasztrófa esetén, ha a helyszíni fájlkiszolgáló nem érhető el, csatlakoztathatja a kívánt fájlhelyeket a felhőből, és a kérések kiszolgálását folytathatja az ügyfélszámítógépekről.
A File Sync és a Site Recovery integrálása:

* Gondoskodjon a fájlkiszolgáló számítógépek védelméről a Site Recovery használatával. Kövesse az [ebben az oktatóanyagban](./vmware-azure-tutorial.md) szereplő lépéseket.
* A File Sync használatával replikálja a fájlokat a fájlkiszolgálóként működő számítógépről a felhőbe.
* A Site Recovery helyreállítási terv funkciójának használatával adjon hozzá szkripteket az Azure-fájlmegosztás csatlakoztatásához a feladatátvételi virtuális géphez az Azure-ban.

Kövesse az alábbi lépéseket a File Sync használatához:

1. [Hozzon létre egy tárfiókot az Azure-ban](../storage/common/storage-account-create.md?toc=/azure/storage/files/toc.json). Ha a tárfiókokhoz írásvédett georedundáns tárolási módot (ajánlott) választ, katasztrófa esetén a másodlagos régióból olvasási hozzáféréssel fog rendelkezni az adataihoz. További információ: [Vészhelyreállítás és tárfiók feladatátvétele.](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2ffiless%2ftoc.json)
2. [Hozzon létre egy fájlmegosztást.](../storage/files/storage-how-to-create-file-share.md)
3. [Helyezze üzembe a File Sync szolgáltatást](../storage/file-sync/file-sync-deployment-guide.md) a helyszíni fájlkiszolgálón.
4. Hozzon létre egy szinkronizálási csoportot. A szinkronizálási csoporton belüli végpontokat a rendszer szinkronban tartja egymással. Egy szinkronizálási csoportnak tartalmaznia kell legalább egy felhővégpontot, amely egy Azure-fájlmegosztást képvisel. A szinkronizálási csoportnak egy kiszolgálóvégpontot is tartalmaznia kell, amely egy útvonalat képvisel a helyszíni Windows-kiszolgálón.
5. A fájlokat a rendszer az Azure-fájlmegosztás és a helyszíni kiszolgáló között mostantól szinkronban tartja.
6. Ha a helyszíni környezetben katasztrófa következik be, a [helyreállítási terv](site-recovery-create-recovery-plans.md) használatával elvégezheti a feladatátvételt. Adja hozzá a szkriptet az Azure-fájlmegosztás csatlakoztatásához és a megosztásnak a virtuális gépen való eléréséhez.

> [!NOTE]
> Győződjön meg róla, hogy a 445-ös port nyitva van. Az Azure Files az SMB protokollt használja. Az SMB protokoll a 445-ös TCP porton keresztül kommunikál. Ellenőrizze, hogy a tűzfal nem blokkolja-e a 445-ös TCP-portot az ügyfél gépéről.


## <a name="do-a-test-failover"></a>Futtasson egy feladatátvételi tesztet

1. Lépjen az Azure Portalra, és válassza ki a helyreállítási tárat.
2. Válassza ki a fájlkiszolgáló-környezethez létrehozott helyreállítási tervet.
3. Kattintson a **Feladatátvétel tesztelése** elemre.
4. A feladatátvételi folyamat indításához válassza ki a helyreállítási pontot és az Azure virtuális hálózatot.
5. A másodlagos környezet kialakítása után hajtsa végre az ellenőrzéseket.
6. Az ellenőrzések befejezését követően kattintson a **Feladatátvételi teszt eltávolítása** elemre a helyreállítási terven. Ezzel törölheti a feladatátvételi tesztkörnyezetet.

A feladatátvételi tesztek elvégzésével kapcsolatos további információk: [Site Recovery feladatátvételi teszt](site-recovery-test-failover-to-azure.md).

Az Active Directory és DNS feladatátvételi tesztjére vonatkozó útmutatásért lásd: [Az Active Directoryra és a DNS-re vonatkozó feladatátvétel szempontjai](site-recovery-active-directory.md).

## <a name="do-a-failover"></a>Feladatátvétel futtatása

1. Lépjen az Azure Portalra, és válassza ki a helyreállítási tárat.
2. Válassza ki a fájlkiszolgáló-környezethez létrehozott helyreállítási tervet.
3. Válassza a **Feladatátvétel** lehetőséget.
4. A feladatátvételi folyamat indításához válassza ki a helyreállítási pontot.

A feladatátvétel végrehajtásával kapcsolatban további információt a [Feladatátvétel a Site Recoveryben](site-recovery-failover.md) című cikk tartalmaz.