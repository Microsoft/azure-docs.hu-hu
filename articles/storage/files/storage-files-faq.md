---
title: Gyakori kérdések (GYIK) a Azure Files | Microsoft Docs
description: Választ kaphat Azure Files gyakori kérdésekre. Az Azure-fájlmegosztásokat egyidejűleg csatlakoztathatja felhőbeli vagy helyszíni Windows-, Linux- vagy macOS-környezetben.
author: roygara
ms.service: storage
ms.date: 02/23/2020
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: 4d7123aa22d95e3e4c3850be775ddad96f28d280
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785306"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Az Azure Filesszal kapcsolatos gyakori kérdések (GYIK)
[Azure Files](storage-files-introduction.md) teljes körűen felügyelt felhőbeli fájlmegosztásokat kínál, amelyek az iparági szabvány [Server Message Block (SMB)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) protokollon és a hálózati fájlrendszer [(NFS)](https://en.wikipedia.org/wiki/Network_File_System) protokollon (előzetes verzió) keresztül érhetők el. Az Azure-fájlmegosztásokat egyidejűleg csatlakoztathatja a felhőben vagy a Windows, Linux és macOS rendszerek helyszíni üzemelő példányán. Az Azure-fájlmegosztásokat Windows Server rendszerű gépeken is gyorsítótárazhatja, ha Azure File Sync gyors hozzáférést biztosít az adatok használatának helyéhez közel.

Ez a cikk gyakori kérdésekre ad választ Azure Files funkciókkal és funkciókkal kapcsolatban, beleértve a Azure File Sync és Azure Files. Ha nem látja a választ a kérdésére, lépjen kapcsolatba velünk a következő csatornákon (eszkalációs sorrendben):

1. A cikk megjegyzések szakasza.
2. [Microsoft Q&Az Azure Storage kérdésoldala.](/answers/topics/azure-file-storage.html)
3. [Azure Files UserVoice .](https://feedback.azure.com/forums/217298-storage/category/180670-files) 
4. Microsoft ügyfélszolgálata. Új támogatási kérelem létrehozásához a súgó Azure Portal a  Súgó lapon válassza a Súgó **és** támogatás gombot, majd az Új **támogatási kérelem lehetőséget.**

## <a name="general"></a>Általános kérdések
* <a id="why-files-useful"></a>
  **Mi a Azure Files hasznos?**  
   A felhőben Azure Files hozhat létre fájlmegosztásokat anélkül, hogy egy fizikai kiszolgáló, eszköz vagy berendezés terhelésének kezeléséért felel. Ön helyett monoton munkát alkalmazunk, beleértve az operációs rendszer frissítéseit és a hibás lemezek cseréjét. Ha többet szeretne megtudni azokkal a forgatókönyvekkel kapcsolatban, amelyekben Azure Files, olvassa el a Miért Azure Files [hasznos?](storage-files-introduction.md#why-azure-files-is-useful)

* <a id="file-access-options"></a>
  **Milyen különböző módokon lehet hozzáférni a fájlokhoz a Azure Files?**  
    Az SMB-fájlmegosztások az SMB 3.0 protokollal csatlakoztathatóak [a](https://storageexplorer.com/) helyi géphez, vagy a fájlmegosztásban lévő fájlok eléréséhez Storage Explorer is használhatók. Az NFS-fájlmegosztások a helyi géphez a helyi gép által biztosított parancsfájl másolása/beillesztése Azure Portal. Az alkalmazásából a Storage ügyfélkódtárait, REST API-ját, a PowerShellt vagy az Azure CLI-t használhatja az Azure-fájlmegosztásban lévő fájlok eléréséhez.

* <a id="what-is-afs"></a>
  **Mi az Azure File Sync?**  
    A Azure File Sync központosíthatja a szervezet fájlmegosztását a Azure Files-ban, miközben a helyszíni fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását is biztosítja. Azure File Sync Windows Server rendszerű gépeket az Azure-fájlmegosztás gyors gyorsítótárába alakítja. A Windows Serveren elérhető bármely protokollt használhatja az adatok helyi eléréséhez, beleértve az SMB-t, a hálózati fájlrendszert (NFS) és a File Transfer Protocol szolgáltatást (FTPS). A világ minden táján annyi gyorsítótárral lehet, amennyire csak szüksége van.

* <a id="files-versus-blobs"></a>
  **Miért használnék Azure-fájlmegosztást és Azure Blob Storage-tárolót az adataimhoz?**  
    Azure Files azure blobtárolók és az Azure Blob Storage egyaránt kínálnak módokat nagy mennyiségű adat felhőben való tárolására, de némileg eltérő célokra hasznosak. 
    
    Az Azure Blob Storage olyan nagy méretű, natív felhőbeli alkalmazásokhoz hasznos, amelyek strukturálatlan adatokat tárolnak. A teljesítmény és a méretezés maximalizálása érdekében az Azure Blob Storage egy egyszerűbb tároló-absztrakció, mint egy valódi fájlrendszer. Az Azure Blob Storage csak REST-alapú ügyfélkódtárakon keresztül (vagy közvetlenül a REST-alapú protokollon keresztül) keresztül használhatja az Azure Blob Storage-et.

    Azure Files egy fájlrendszer. Azure Files rendelkezik az összes olyan fájllal, amit ismer és szeret, a helyszíni operációs rendszerekkel való munka során. Az Azure Blob Storage-hoz Azure Files REST-felületet és REST-alapú ügyfélkódtárakat is kínál. Az Azure Blob Storage-hoz Azure Files SMB- vagy NFS-hozzáférést biztosít az Azure-fájlmegosztások számára. A fájlmegosztások közvetlenül csatlakoztathatóak Windows, Linux vagy macOS rendszeren, akár helyszíni, akár felhőbeli virtuális gépeken anélkül, hogy kódot kellene írnia, vagy speciális illesztőprogramokat kellene csatolnia a fájlrendszerhez. Az Azure SMB-fájlmegosztásokat a helyszíni fájlkiszolgálókra is gyorsítótárazhatja a gyors Azure File Sync használatával, az adatok használatának helyéhez közel. 
   
    Az Azure Blob Storage és a Azure Files közötti különbségekkel kapcsolatos részletes leírásért lásd: Bevezetés az [alapvető Azure Storage-szolgáltatásokba.](../common/storage-introduction.md) További információ az Azure Blob Storage-ről: [Bevezetés a Blob Storage-ba.](../blobs/storage-blobs-introduction.md)

* <a id="files-versus-disks"></a>**Miért használnék Azure-fájlmegosztást az Azure Disks helyett?**  
    Az Azure Disksben a lemezek egyszerűen lemezek. Ahhoz, hogy értéket kapjon az Azure Disksből, hozzá kell csatolnia egy lemezt egy Azure-ban futó virtuális géphez. Az Azure Disks használható minden olyan szolgáltatáshoz, amihez lemezt használna egy helyszíni kiszolgálón. Használhatja operációsrendszer-lemezként, az operációs rendszer felcserélési tárhelyeként vagy egy alkalmazás dedikált tárolójaként. Az Azure Disks érdekes használata, ha létrehoz egy fájlkiszolgálót a felhőben, amely ugyanazon a helyen használható, ahol az Azure-fájlmegosztást is használhatja. A fájlkiszolgálók Azure Virtual Machines üzembe helyezésével nagy teljesítmény érhető el a fájltároláshoz az Azure-ban, ha olyan üzembehelyezési beállításokra van szüksége, amelyek jelenleg nem támogatottak a Azure Files. 

    Az Azure Diskset használó fájlkiszolgálók háttértárolóként való futtatása azonban általában sokkal drágább, mint egy Azure-fájlmegosztás használata, néhány okból. Először is a lemezes tárolásért való fizetés mellett fizetnie kell egy vagy több Azure-beli virtuális gép futtatásával kapcsolatos költségeket is. Másodszor a fájlkiszolgáló futtatásához használt virtuális gépeket is kezelnie kell. Például az Ön feladata az operációs rendszer frissítése. Végül, ha végső soron az adatok helyszíni gyorsítótárazása szükséges, Önnek kell beállítania és kezelnie az olyan replikációs technológiákat, mint az elosztott fájlrendszer Replication (DFSR), hogy ez megtörténjen.

    Az Azure Files és az Azure Virtual Machines-ban üzemeltetett fájlkiszolgálók (amellett, hogy az Azure Disks háttértárként való használata) egyik legjobb módja az Azure File Sync telepítése egy felhőalapú virtuális gépen üzemeltetett fájlkiszolgálóra. Ha az Azure-fájlmegosztás a fájlkiszolgálóval azonos régióban van, engedélyezheti a felhőbeli rétegezést, és beállíthatja a szabad terület százalékos mennyiségét maximálisra (99%). Ez biztosítja az adatok minimális duplikálását. A fájlkiszolgálókkal bármilyen kívánt alkalmazást használhat, például nfs protokolltámogatást igénylő alkalmazásokat.

    A nagy teljesítményű és magas rendelkezésre állménnyel elérhető fájlkiszolgálók Azure-ban való beállításával kapcsolatos információkért lásd: [IaaS](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/)virtuálisgép-vendégfürtök üzembe helyezése a Microsoft Azure. Az Azure-lemezek és az Azure Disks közötti Azure Files lásd: Bevezetés az [alapvető Azure Storage-szolgáltatásokba.](../common/storage-introduction.md) További információ az Azure Disks szolgáltatásról: [Az Azure Managed Disks áttekintése.](../../virtual-machines/managed-disks-overview.md)

* <a id="get-started"></a>
  **Hogyan a Azure Files?**  
   Az első lépések Azure Files egyszerű. Először hozzon létre [egy SMB-fájlmegosztást](storage-how-to-create-file-share.md) vagy egy [NFS-megosztás](storage-files-how-to-create-nfs-shares.md)létrehozása et, majd csatlakoztassa az előnyben részesített operációs rendszerben: 

  * [SMB-megosztás csatlakoztatása Windows rendszeren](storage-how-to-use-files-windows.md)
  * [SMB-megosztás csatlakoztatása Linux rendszeren](storage-how-to-use-files-linux.md)
  * [SMB-megosztás csatlakoztatása macOS rendszeren](storage-how-to-use-files-mac.md)
  * [NFS-fájlmegosztás csatlakoztatása](storage-files-how-to-mount-nfs-shares.md)

    Az éles fájlmegosztásokat lecserélő Azure-fájlmegosztások a szervezetben való üzembe helyezéséről a Planning for an Azure Files deployment (Tervezési útmutató) [Azure Files be.](storage-files-planning.md)

* <a id="redundancy-options"></a>
  **Milyen redundanciabeállításokat támogatnak a Azure Files?**  
    Jelenleg a Azure Files támogatja a helyileg redundáns tárolást (LRS), a zónaredundáns tárolást (ZRS), a georedundáns tárolást (GRS) és a geo-zónaredundáns tárolást (GZRS). Azure Files prémium szint jelenleg csak az LRS-t és a ZRS-t támogatja.

* <a id="tier-options"></a>
  **Milyen tárolási szintek támogatottak a Azure Files?**  
    Azure Files két tárolási szintet támogat: a prémium és a standard szintet. A standard fájlmegosztások általános célú (GPv1 vagy GPv2) tárfiókban, a prémium fájlmegosztások pedig a FileStorage tárfiókban vannak létrehozva. További információ a standard [fájlmegosztások](storage-how-to-create-file-share.md) és a [prémium fájlmegosztások létrehozásáról.](./storage-how-to-create-file-share.md) 
    
    > [!NOTE]
    > Nem hozhat létre Azure-fájlmegosztásokat  Blob Storage-fiókokból vagy prémium szintű általános célú (GPv1- vagy GPv2-) tárfiókból. A Standard Azure-fájlmegosztásokat csak standard általános célú fiókokban kell létrehozni, a prémium szintű Azure-fájlmegosztásokat pedig csak a FileStorage-tárfiókban lehet létrehozni.  *A* prémium szintű általános célú (GPv1- és GPv2-) tárfiókok csak prémium lapblobok számára használhatók. 

* <a id="file-locking"></a>
  **Támogatja Azure Files fájlok zárolását?**  
    Igen, Azure Files teljes mértékben támogatja az SMB-/Windows-stílusú fájlzárolást, lásd [a részleteket.](/rest/api/storageservices/managing-file-locks)

* <a id="give-us-feedback"></a>
  **Nagyon szeretném látni, hogy egy adott funkció hozzá van adva a Azure Files. Hozzá tudja adni?**  
    A Azure Files a szolgáltatással kapcsolatos visszajelzéseit és visszajelzéseit érdekli. Kérjük, szavazzon a funkcióigénylésre [a uservoice Azure Files oldalon!](https://feedback.azure.com/forums/217298-storage/category/180670-files) Örömmel jelentjük be, hogy számos új funkcióval rendelkezik.

## <a name="azure-file-sync"></a>Azure File Sync

* <a id="afs-region-availability"></a>
  **Milyen régiók támogatottak a Azure File Sync?**  
    Az elérhető régiók listája a [](../file-sync/file-sync-planning.md#azure-file-sync-region-availability) tervezési útmutató Régiónkénti rendelkezésre állás szakaszában Azure File Sync található. Folyamatosan adjuk hozzá a további régiók támogatását, beleértve a nem nyilvános régiókat is.

* <a id="cross-domain-sync"></a>
  **Lehet tartományhoz vagy tartományhoz nem csatlakozott kiszolgáló ugyanabban a szinkronizálási csoportban?**  
    Igen. A szinkronizálási csoportok eltérő tagságú kiszolgálóvégpontokat Active Directory, még akkor is, ha nem csatlakoznak tartományhoz. Bár ez a konfiguráció technikailag működik, ezt nem javasoljuk tipikus konfigurációként, mert előfordulhat, hogy a szinkronizálási csoport többi kiszolgálója nem tudja kényszerítni az egyik kiszolgálón lévő fájlokhoz és mappákhoz definiált hozzáférés-vezérlési listák (ACL-ek) használatát. A legjobb eredmény érdekében javasoljuk az azonos Active Directory-erdőben található kiszolgálók közötti szinkronizálást olyan kiszolgálók között, amelyek eltérő Active Directory-erdőkben találhatók, de megbízhatósági kapcsolatban állnak, vagy a tartományon nem található kiszolgálók között. Javasoljuk, hogy ezeket a konfigurációkat ne használja vegyesen.

* <a id="afs-change-detection"></a>
  **Közvetlenül az Azure-fájlmegosztásban létrehoztam egy fájlt az SMB használatával vagy a portálon. Mennyi ideig tart a fájl szinkronizálása a szinkronizálási csoportban található kiszolgálókkal?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]


* <a id="afs-sync-time"></a>
  **Mennyi ideig tart, Azure File Sync 1TiB adat feltöltése?**
  
    A teljesítmény a környezeti beállításoktól, a konfigurációtól, valamint attól függően változik, hogy kezdeti vagy folyamatos szinkronizálásról van-e szó. További információkért lásd a Azure File Sync [metrikákról](storage-files-scale-targets.md#azure-file-sync-performance-metrics)

* <a id="afs-conflict-resolution"></a>**Mi történik, ha ugyanazt a fájlt körülbelül egyszerre két kiszolgálón is megváltoztatják?**  
    Azure File Sync egyszerű ütközésfeloldási stratégiát használ: a két végponton módosított fájlok mindkét módosításait egyszerre tartjuk meg. A legutóbb írt módosítás megőrzi az eredeti fájlnevet. A régebbi fájl (amelyet a LastWriteTime határoz meg) rendelkezik a végpont nevével és a fájlnévhez fűzött ütközési számmal. Kiszolgálóvégpont esetén a végpont neve a kiszolgáló neve. Felhőbeli végpontok esetén a végpont neve **Felhő.** A név a következő elnevezést követi: 
   
    \<FileNameWithoutExtension\>-\<endpointName\>\[-#\].\<ext\>  

    A virtuális kiszolgáló első ütközése CompanyReport.docx, CompanyReport-CentralServer.docx CentralServer a régebbi írási hely. A második ütközés a következő lesz: CompanyReport-CentralServer-1.docx. Azure File Sync fájlonként 100 ütközési fájlt támogat. Az ütközési fájlok maximális számának elérése után a fájl szinkronizálása sikertelen lesz, amíg az ütközési fájlok száma 100-asnál kevesebb nem lesz.

* <a id="afs-storage-redundancy"></a>
  **Támogatott a georedundáns tárolás a Azure File Sync?**  
    Igen, Azure Files támogatja a helyileg redundáns tárolást (LRS) és a georedundáns tárolást (GRS). Ha egy GRS-hez konfigurált fiókból kezdeményezi a tárfiókok közötti feladatátvételt a párosított régiók között, a Microsoft azt javasolja, hogy az új régiót csak az adatok biztonsági másolataként kezelje. Azure File Sync a rendszer nem kezdi meg automatikusan a szinkronizálást az új elsődleges régióval. 

* <a id="sizeondisk-versus-size"></a>
  **Miért nem egyezik meg egy fájl *Size on disk* (Lemez mérete) tulajdonsága a *Size* (Méret) tulajdonságával a Azure File Sync?**  
  Lásd [a Azure File Sync rétegezését.](../file-sync/file-sync-cloud-tiering-overview.md#tiered-vs-locally-cached-file-behavior)

* <a id="is-my-file-tiered"></a>
  **Honnan tudom, hogy egy fájl rétegzett-e?**  
  Lásd: [Understanding Cloud Tiering (A felhőbeli rétegezés ismertetése).](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-check-if-your-files-are-being-tiered)

* <a id="afs-recall-file"></a>**A használni kívánt fájl rétegzett. Hogyan tudom visszahívni a fájlt a lemezre a helyi használathoz?**  
  Lásd: [Understanding Cloud Tiering (A felhőbeli rétegezés ismertetése).](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-recall-a-tiered-file-to-disk)

* <a id="afs-force-tiering"></a>
  **Hogyan egy fájl vagy könyvtár rétegzettre való kényszerítét?**  
  Lásd: [Understanding Cloud Tiering (A felhőbeli rétegezés ismertetése).](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-force-a-file-or-directory-to-be-tiered)

* <a id="afs-effective-vfs"></a>
  **Hogyan *értelmezi a* rendszer a kötet szabad helyének értelmezését, ha több kiszolgálóvégpontom van egy köteten?**  
  Lásd: [Understanding Cloud Tiering (A felhőbeli rétegezés ismertetése).](../file-sync/file-sync-cloud-tiering-policy.md#multiple-server-endpoints-on-a-local-volume)
  
* <a id="afs-tiered-files-tiering-disabled"></a>
  **Le van tiltva a felhőbeli rétegezés. Miért vannak rétegzett fájlok a kiszolgálóvégpont helyén?**  
    A rétegzett fájloknak két oka lehet a kiszolgálóvégpont helye esetén:

    - Ha új kiszolgálóvégpontot ad hozzá egy meglévő szinkronizálási csoporthoz, vagy a névtér visszahívása első vagy csak a kezdeti letöltési módhoz választja a névteret, a fájlok rétegzettként fognak adatokat látni, amíg helyileg le nem töltik őket. Ennek elkerülése érdekében válassza a rétegzett fájlok elkerülése lehetőséget a kezdeti letöltési módhoz. A fájlok manuális visszahívása az [Invoke-StorageSyncFileRecall](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-recall-a-tiered-file-to-disk) parancsmag használatával történik.

    - Ha a felhőbeli rétegezés engedélyezve volt a kiszolgálóvégponton, majd le lett tiltva, a fájlok a hozzáférésükig rétegzettek maradnak.

* <a id="afs-tiered-files-not-showing-thumbnails"></a>
  **Miért nem jelennek meg miniatűrök vagy előnézetek a rétegzett fájlokban a Windows Explorer?**  
    Rétegzett fájlok esetén a miniatűrök és az előnézetek nem jelennek meg a kiszolgálóvégponton. Ez a viselkedés várható, mivel a Windows miniatűr-gyorsítótár funkciója szándékosan kihagyja az offline attribútummal lévő fájlok olvasását. Ha a felhőbeli rétegezés engedélyezve van, a rétegzett fájlok olvasása a fájlok letöltését (visszahívott) okozná.

    Ez a viselkedés nem csak Azure File Sync, Windows Explorer "szürke X" jelenik meg minden olyan fájlhoz, amelyhez offline attribútum van beállítva. A fájlok SMB-ről való elérésekor megjelenik az X ikon. A viselkedés részletes magyarázatát lásd: [https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)

    A rétegzett fájlok kezeléséhez szükséges kérdésekért lásd: [Rétegzett fájlok kezelése.](../file-sync/file-sync-how-to-manage-tiered-files.md)

* <a id="afs-files-excluded"></a>
  **Mely fájlokat vagy mappákat zárják ki automatikusan Azure File Sync?**  
  Lásd: [Kihagyott fájlok.](../file-sync/file-sync-planning.md#files-skipped)

* <a id="afs-os-support"></a>
  **Használhatom a Azure File Sync Windows Server 2008 R2, Linux vagy a hálózati tárolóeszközöm (NAS) használatával?**  
    Jelenleg a Azure File Sync csak a Windows Server 2019, a Windows Server 2016 és a Windows Server 2012 R2 rendszert támogatja. Jelenleg nincs más megosztható csomagunk, de az ügyfelek igényei alapján további platformokat is támogatunk. A [UserVoice-Azure Files,](https://feedback.azure.com/forums/217298-storage/category/180670-files) mely platformokat szeretné támogatni.

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **Miért léteznek rétegzett fájlok a kiszolgálóvégpont-névtéren kívül?**  
    A Azure File Sync 3-as verziója előtt a Azure File Sync blokkolta a rétegzett fájlok áthelyezését a kiszolgálóvégponton kívülre, de ugyanazon a köteten, mint a kiszolgálói végpont. A másolási műveleteket, a nem rétegzett fájlok mozgatása és a rétegzett más kötetekbe való mozgatásokat ez nem érinti. Ennek a viselkedésnek az az implicit feltételezése volt, hogy Fájlkezelő és más Windows API-k azt feltételezik, hogy az ugyanazon a köteten történő áthelyezési műveletek (majdnem) azonnali átnevezéses műveletek. Ez azt jelenti, hogy Fájlkezelő vagy más áthelyezési metódusok (például a parancssor vagy a PowerShell) nem válaszolnak, miközben Azure File Sync a felhőből visszahívást ad vissza. A Azure File Sync [3.0.12.0-s](../file-sync/file-sync-release-notes.md#supported-versions)verziójától kezdve a Azure File Sync lehetővé teszi egy rétegzett fájl áthelyezését a kiszolgálóvégponton kívülre. Elkerüljük a korábban említett negatív hatásokat, mivel lehetővé teszi, hogy a rétegzett fájl rétegzett fájlként létezzen a kiszolgálóvégponton kívül, majd visszahívja a fájlt a háttérben. Ez azt jelenti, hogy az ugyanazon a köteten való áthelyezés azonnali, és minden munkát a fájl lemezre való visszahívása után kell végrehajtani. 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **Probléma adódnak a kiszolgáló Azure File Sync (szinkronizálás, felhőbeli rétegezés stb.) problémáim vannak. Távolítsam el, majd hozzam létre újra a kiszolgálóvégpontomat?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **Áthelyezhetim a társzinkronizálási szolgáltatást és/vagy tárfiókot egy másik erőforráscsoportba, előfizetésbe vagy Azure AD-bérlőbe?**  
   Igen, a társzinkronizálási szolgáltatás és/vagy tárfiók áthelyezható egy másik erőforráscsoportba, előfizetésbe vagy Azure AD-bérlőbe. Miután áthelyezte a társzinkronizálási szolgáltatást vagy a tárfiókot, hozzáférést kell biztosítania a Microsoft.StorageSync alkalmazásnak a tárfiókhoz (lásd: Győződjön meg arról, hogy az Azure File Sync hozzáféréssel rendelkezik a [tárfiókhoz).](../file-sync/file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)

    > [!Note]  
    > A felhőbeli végpont létrehozásakor a társzinkronizálási szolgáltatásnak és a tárfióknak ugyanabban az Azure AD-bérlőben kell lennie. A felhőbeli végpont létrehozása után a társzinkronizálási szolgáltatás és a tárfiók áthelyezható a különböző Azure AD-bérlőkbe.
    
* <a id="afs-ntfs-acls"></a>
  **Megőrzi Azure File Sync a könyvtár-/fájlszintű NTFS ACL-eket a könyvtárban tárolt Azure Files?**

    2020. február 24-től az Azure File Sync által rétegzett új és meglévő ACL-ek NTFS formátumban lesznek megőrzve, és a közvetlenül az Azure-fájlmegosztáson végzett ACL-módosítások szinkronizálva lesznek a szinkronizálási csoportban lévő összes kiszolgálóval. Az ACL-ekkel végrehajtott módosítások Azure Files Azure File Syncen keresztül lesznek szinkronizálva. Amikor adatokat másol az Azure Files-ba, győződjön meg arról, hogy olyan másolási eszközt használ, amely támogatja az attribútumok, időbélyegek és ACL-ek másolását egy Azure-fájlmegosztásba SMB-n vagy REST-en keresztül. Azure-beli másolási eszközök, például az AzCopy használata esetén fontos, hogy a legújabb verziót használja. A [fájlmásoló eszközök](storage-files-migration-overview.md#file-copy-tools) táblázatában áttekintheti az Azure-beli másolási eszközöket, és biztosíthatja, hogy átmásolja egy fájl összes fontos metaadatát.

    Ha engedélyezte a Azure Backup fájlszinkronizálás felügyelt fájlmegosztásán, a fájl ACL-ek a biztonsági mentés visszaállítási munkafolyamatának részeként továbbra is visszaállíthatóak. Ez a teljes megosztásra vagy az egyes fájlokra/könyvtárakra is vonatkozik.

    Ha a fájlszinkronizálás által felügyelt fájlmegosztások ön által felügyelt biztonsági mentési megoldásának részeként pillanatképeket használ, előfordulhat, hogy az ACL-ek nem állíthatók vissza megfelelően az NTFS ACL-eken, ha a pillanatképek 2020. február 24. előtt készültek el. Ha ez történik, vegye fel a kapcsolatot a Azure ügyfélszolgálata.

* <a id="afs-lastwritetime"></a>
  **Szinkronizálja Azure File Sync a LastWriteTime könyvtárat?**  
    Nem, Azure File Sync a LastWriteTime nem szinkronizálja a könyvtárakat. Ez az elvárt működés.
    
## <a name="security-authentication-and-access-control"></a>Biztonság, hitelesítés és hozzáférés-vezérlés
* <a id="ad-support"></a>
**Támogatja az identitásalapú hitelesítést és a hozzáférés-vezérlést Azure Files?**  
    
    Igen, Azure Files támogatja az identitásalapú hitelesítést és a hozzáférés-vezérlést. Kétféleképpen használhatja az identitásalapú hozzáférés-vezérlést: helyi Active Directory Domain Services vagy Azure Active Directory Domain Services (Azure AD DS). A helyszíni Active Directory Domain Services (AD DS) támogatja AD DS helyszíni vagy azure-beli, tartományhoz csatlakozott gépeken keresztüli hitelesítést az Azure-fájlmegosztások SMB-kapcsolaton keresztüli eléréséhez. Azure AD DS SMB-alapú hitelesítés Azure Files lehetővé teszi Azure AD DS tartományhoz csatlakozott Windows rendszerű virtuális gépek számára a megosztások, könyvtárak és fájlok Azure AD hitelesítő adatokkal történő hozzáférését. További részletekért lásd: [Overview of Azure Files authentication support for SMB access (Az SMB-hozzáférés](storage-files-active-directory-overview.md)identitásalapú hitelesítésének Azure Files áttekintése). 

    Azure Files hozzáférés-vezérlés két további módját is kínálja:

    - A közös hozzáférésű jogosultságkódot (SAS) olyan jogkivonatok létrehozásához használhatja, amelyek adott engedélyekkel rendelkezik, és amelyek egy adott időtartamra érvényesek. Létrehozhat például egy jogkivonatot, amely csak olvasási hozzáféréssel rendelkezik egy 10 perces lejárati idővel rendelkezik egy adott fájlhoz. Bárki, aki érvényes jogkivonattal rendelkezik, csak olvasási hozzáféréssel rendelkezik a fájlhoz a 10 percig. A közös hozzáférésű jogosultságkódkulcsok csak a REST API vagy ügyfélkódtárakban támogatottak. Az Azure-fájlmegosztást SMB-ről kell csatlakoztatnia a tárfiókkulcsok használatával.

    - Azure File Sync megőrzi és replikálja az összes tulajdonosi ACL-t (akár Active Directory-alapú, akár helyi) az összes kiszolgálóvégpontra, amelybe szinkronizál. 
    
    Az Azure [Storage-szolgáltatások által](../common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) támogatott összes protokoll átfogó megjelenítéséhez tekintse meg az Azure Storage-hoz való hozzáférésről való hozzáférést. 
    
* <a id="encryption-at-rest"></a>
**Hogyan biztosítható, hogy az Azure-fájlmegosztásom titkosítva legyen használatban?**  

    Igen. További információ: [Azure Storage Service Encryption.](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

* <a id="access-via-browser"></a>
**Hogyan nyújthatok hozzáférést egy adott fájlhoz egy webböngésző használatával?**  

    A közös hozzáférésű jogosultságkódot adott engedélyekkel rendelkező, meghatározott időtartamra érvényes jogkivonatok létrehozásához használhatja. Létrehozhat például egy jogkivonatot, amely csak olvasási hozzáférést biztosít egy adott fájlhoz egy adott időtartamra. Az URL-cím birtokában bárki közvetlenül elérheti a fájlt bármely webböngészőből, amíg a jogkivonat érvényes. A közös hozzáférésű jogosultság jogosultságkulcsát egyszerűen létrehozhatja egy olyan felhasználói felületről, mint Storage Explorer.

* <a id="file-level-permissions"></a>
**Megadható csak olvasási vagy írási jogosultság a megosztáson belüli mappákhoz?**  

    Ha a fájlmegosztást SMB használatával csatlakoztatja, akkor nem rendelkezik mappaszintű vezérléssel az engedélyek felett. Ha azonban közös hozzáférésű jogosultságkódokat hoz létre a REST API vagy ügyfélkódtárak használatával, írási vagy írási engedélyeket is megadhat a megosztáson belüli mappákhoz.

* <a id="ip-restrictions"></a>
**Megvalósít tudok IP-korlátozásokat egy Azure-fájlmegosztáshoz?**  

    Igen. Az Azure-fájlmegosztáshoz való hozzáférés a tárfiók szintjén korlátozható. További információ: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása.](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

* <a id="data-compliance-policies"></a>
**Milyen adat megfelelőségi szabályzatokat Azure Files?**  

   Azure Files az Azure Storage más tárolási szolgáltatásaiban használt tárolási architektúrán fut. Azure Files azure storage-szolgáltatásokban használt adat megfelelőségi szabályzatokat alkalmazza. Az Azure Storage-adatok megfelelőségére vonatkozó további információkért tekintse meg az [Azure Storage](../common/storage-compliance-offerings.md)megfelelőségi ajánlatát, és tekintse meg a [Microsoft adatvédelmi központot.](https://microsoft.com/trustcenter/default.aspx)

* <a id="file-auditing"></a>
**Hogyan naplólhatja a fájlelérést és a módosításokat a Azure Files?**

  A naplózási funkciókat két lehetőség biztosítja a Azure Files:
  - Ha a felhasználók közvetlenül férnek hozzá az Azure-fájlmegosztáshoz, az Azure Storage-naplók [(előzetes verzió)](../blobs/monitor-blob-storage.md?tabs=azure-powershell#analyzing-logs) a fájlváltozások és a felhasználói hozzáférés nyomon követésére használhatók. Ezek a naplók hibaelhárítási célokra használhatók, és a kéréseket a lehető legjobb körülmények között naplózhatja.
  - Ha a felhasználók egy olyan Windows Serveren keresztül férnek hozzá az Azure-fájlmegosztáshoz, amelyre telepítve van a Azure File Sync-ügynök, használjon naplózási szabályzatot vagy harmadik féltől származó terméket a fájlváltozások és a felhasználói hozzáférés nyomon követéséhez a Windows Serveren. [](/windows/security/threat-protection/auditing/apply-a-basic-audit-policy-on-a-file-or-folder) 
   
### <a name="ad-ds--azure-ad-ds-authentication"></a>AD DS & Azure AD DS hitelesítés
* <a id="ad-support-devices"></a>
**Támogatja Azure Files Azure Active Directory Domain Services (Azure AD DS) a hitelesítés az Azure AD-hez csatlakozott vagy az Azure AD-ben regisztrált eszközök Azure AD-beli hitelesítő adataival történő SMB-hozzáférést?**

    Nem, ez a forgatókönyv nem támogatott.

* <a id="ad-vm-subscription"></a>
**Hozzáférhetek az Azure-fájlmegosztások Azure AD-beli hitelesítő adataihoz egy másik előfizetésben található virtuális gépről?**

    Ha a fájlmegosztás üzembe helyezéséhez használt előfizetés ugyan ahhoz az Azure AD-bérlőhöz van társítva, mint az Azure AD DS üzemelő példány, amelyhez a virtuális gép tartományhoz csatlakozik, akkor ugyanezekkel az Azure AD-hitelesítő adatokkal férhet hozzá az Azure-fájlmegosztáshoz. A korlátozás nem az előfizetésre, hanem a társított Azure AD-bérlőre vonatkozik.
    
* <a id="ad-support-subscription"></a>
**Engedélyezem az Azure-fájlmegosztások Azure AD DS vagy helyszíni AD DS-hitelesítését egy olyan Azure AD-bérlővel, amely eltér az Azure-fájlmegosztás elsődleges bérlőjéhez?**

    Nem, Azure Files a Azure AD DS helyszíni AD DS a fájlmegosztással azonos előfizetésben található Azure AD-bérlővel való integrációt. Egy Azure AD-bérlőhöz csak egy előfizetés társítható. Ez a korlátozás mind a Azure AD DS, mind a AD DS hitelesítési módszerekre vonatkozik. Ha helyszíni AD DS használ a hitelesítéshez, az AD DS hitelesítő adatait szinkronizálni kell azzal az [Azure AD-val,](../../active-directory/hybrid/how-to-connect-install-roadmap.md) amelyhez a tárfiók társítva van.

* <a id="ad-linux-vms"></a>
**Támogatja Azure AD DS Azure-fájlmegosztások AD DS vagy helyszíni hitelesítése a Linux rendszerű virtuális gépeket?**

    Nem, a Linux rendszerű virtuális gépekről való hitelesítés nem támogatott.

* <a id="ad-aad-smb-afs"></a>
**Az Azure File Sync által felügyelt fájlmegosztások támogatják a Azure AD DS vagy a AD DS hitelesítést?**

    Igen, engedélyezheti a Azure AD DS helyszíni AD DS hitelesítést a felügyelt fájlmegosztáson, Azure File Sync. A helyi fájlkiszolgálókon a könyvtár/fájl NTFS ACL-ek módosításai a következő szintre lesznek rétegűek Azure Files és fordítva.

* <a id="ad-aad-smb-files"></a>
**Hogyan tudom ellenőrizni, hogy engedélyeztem-e AD DS hitelesítést a tárfiókomon, és lekérem a tartományi adatokat?**

    Útmutatásért lásd [itt:](./storage-files-identity-ad-ds-enable.md#confirm-the-feature-is-enabled).

* <a id=""></a>
**Támogatja Azure Files Azure AD-hitelesítés a Linux rendszerű virtuális gépeket?**

    Nem, a Linux rendszerű virtuális gépekről való hitelesítés nem támogatott.

* <a id="ad-multiple-forest"></a>
**Támogatja a helyszíni AD DS azure-fájlmegosztások hitelesítése a több erdőt használó AD DS-környezettel való integrációt?**    

    Azure Files helyszíni AD DS hitelesítés csak annak a tartományi szolgáltatásnak az erdőjével van integrálva, amelybe a tárfiók regisztrálva van. Egy másik erdőből való hitelesítés támogatásához a környezetben megfelelően konfigurált erdőszintű megbízhatósági kapcsolatnak kell lennie. A Azure Files regisztrációja AD DS megegyezik egy normál fájlkiszolgálóval, ahol identitást (számítógép- vagy szolgáltatás-bejelentkezési fiókot) hoz létre a AD DS hitelesítéshez. Az egyetlen különbség az, hogy a tárfiók regisztrált SPN-je "file.core.windows.net" végződik, amely nem egyezik a tartomány utótaggal. Forduljon a tartományi rendszergazdához, és nézze meg, hogy szükség van-e az utótag útválasztási szabályzatának frissítésére a különböző tartomány-utótag miatt több erdő hitelesítésének engedélyezéséhez. Alább egy példát mutatunk be az utótag útválasztási szabályzatának konfigurálásához.
    
    Példa: Ha az A tartomány egy olyan fájlmegosztást szeretne elérni, amely a B erdőben található tartományhoz van regisztrálva, ez nem fog automatikusan működni, mert a tárfiók szolgáltatásnevének utótagja nem egyezik meg az A erdőben található tartományok utótagjának megfelelő utótaggal. A probléma megoldásához manuálisan konfigurálhat egy utótag-útválasztási szabályt az A erdőből a B erdőbe egy "file.core.windows.net" egyéni utótaghoz.
    Először hozzá kell adni egy új egyéni utótagot a B erdőhöz. Győződjön meg arról, hogy rendelkezik a megfelelő rendszergazdai engedélyekkel a konfiguráció módosítása érdekében, majd kövesse az alábbi lépéseket:   
    1. Bejelentkezés a B erdőhöz csatlakozott számítógéptartományba
    2.  Nyissa meg a "Active Directory és megbízhatóságok" konzolt
    3.  Kattintson a jobb gombbal a "Active Directory és megbízhatóságok" elemre
    4.  Kattintson a "Tulajdonságok" elemre
    5.  Kattintson a Hozzáadás gombra
    6.  Az UPN file.core.windows.net utótagként adja hozzá a "file.core.windows.net" utótagot
    7.  Kattintson az "Alkalmaz", majd az "OK" gombra a varázsló bezáráshoz
    
    Ezután adja hozzá az utótag útválasztási szabályát az A erdőhöz, hogy az átirányítsa a B erdőbe.
    1.  Bejelentkezés az A erdőhöz csatlakozott géptartományba
    2.  Nyissa meg a "Active Directory és megbízhatóságok" konzolt
    3.  Kattintson a jobb gombbal arra a tartományra, amelyhez hozzá szeretne férni a fájlmegosztáshoz, majd kattintson a "Megbízhatóságok" lapra, és válassza ki a B erdő tartományát a kimenő bizalmi kapcsolatokból. Ha nem konfigurálta a megbízhatósági kapcsolatot a két erdő között, először be kell állítania a megbízhatóságot
    4.  Kattintson a "Tulajdonságok..." elemre. majd "Name Suffix Routing"
    5.  Ellenőrizze, hogy megjelenik-e a *.file.core.windows.net utótag. Ha nem, kattintson a Frissítés gombra
    6.  Válassza a *.file.core.windows.net lehetőséget, majd kattintson az "Engedélyezés" és az "Alkalmaz" elemre

* <a id=""></a>
**Milyen régiók érhetők el a Azure Files AD DS hitelesítéshez?**

    További [részleteket AD DS a regionális rendelkezésre állásról.](storage-files-identity-auth-active-directory-enable.md#regional-availability)
    
* <a id="ad-aad-smb-afs"></a>
**Kihasználható a Azure Files Active Directory által felügyelt fájlmegosztások AD-hitelesítés Azure File Sync e?**

    Igen, engedélyezheti az AD-hitelesítést az Azure File Sync által felügyelt fájlmegosztáson. A helyi fájlkiszolgálókon a könyvtár/fájl NTFS ACL-ek módosításai a következő szintre lesznek rétegűek Azure Files és fordítva.

* <a id="ad-aad-smb-files"></a>
**Van különbség a számítógépfiók vagy a szolgáltatás bejelentkezési fiókja között, amely a tárfiókomat képviseli az AD-ban?**

    A [számítógépfiók](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (alapértelmezett) vagy [a](/windows/win32/ad/about-service-logon-accounts) szolgáltatás bejelentkezési fiókjának létrehozása nem számít, hogyan működne a hitelesítés a Azure Files. Saját maga is választhatja, hogyan képviselhet egy tárfiókot identitásként az AD-környezetben. A parancsmagban beállított alapértelmezett DomainAccountType Join-AzStorageAccountForAuth számítógépfiók. Az AD-környezetben konfigurált jelszó lejárati ideje azonban eltérő lehet a számítógép- vagy szolgáltatás bejelentkezési fiókjainál, és ezt figyelembe kell vennie a tárfiók identitásának jelszavának frissítéséhez az [AD-ban.](./storage-files-identity-ad-ds-update-password.md)
 
* <a id="ad-support-rest-apis"></a>
**Vannak OLYAN REST API-k, amelyek támogatják a Get/Set/Copy directory/file Windows ACL-eket?**

    Igen, a [2019-07-07](/rest/api/storageservices/versioning-for-the-azure-storage-services#version-2019-07-07) -es (vagy újabb) időpontban használt könyvtárak vagy fájlok NTFS ACL-jeit lekért, beállító vagy másoló REST API-kat REST API. A Windows ACL-ek REST-alapú eszközökben való megőrzését is támogatjuk: [AzCopy v10.4+](https://github.com/Azure/azure-storage-azcopy/releases).

* <a id="ad-support-rest-apis"></a>
**Gyorsítótárazott hitelesítő adatok eltávolítása tárfiókk kulccsal és meglévő SMB-kapcsolatok törlése az Azure AD- vagy AD-hitelesítő adatokkal való új kapcsolat inicializálása előtt**

    Az alábbi két lépésből eltávolíthatja a tárfiókkulcshoz társított mentett hitelesítő adatokat, és eltávolíthatja az SMB-kapcsolatot: 
    1. Futtassa az alábbi parancsmagot a Windows Cmd.exe a hitelesítő adatok eltávolításához. Ha nem talál ilyet, az azt jelenti, hogy nem őrzött meg hitelesítő adatokat, és kihagyhatja ezt a lépést.
    
       cmdkey /delete:Domain:target=storage-account-name.file.core.windows.net
    
    2. Törölje a fájlmegosztás meglévő kapcsolatát. A csatlakoztatási útvonalat megadhatja a csatlakoztatott meghajtó betűjeleként vagy a storage-account-name.file.core.windows.net elérési útjaként.
    
       net use <drive-letter/share-path> /delete

## <a name="network-file-system"></a>Hálózati fájlrendszer

* <a id="when-to-use-nfs"></a>
**Mikor használjak nfs Azure Files t?**

    Lásd: [NFS-megosztások (előzetes verzió)](storage-files-compare-protocols.md#nfs-shares-preview).

* <a id="backup-nfs-data"></a>
**Hogyan NFS-megosztásban tárolt biztonsági mentési adatokat?**

    Az adatok NFS-megosztáson való biztonsági mentése vagy az olyan ismerős eszközök használatával vezényelható, mint az rsync, vagy az egyik külső biztonsági mentési partnertől származó termékek. Több biztonsági mentési partner, köztük a [Commvault,](https://documentation.commvault.com/commvault/v11/article?p=92634.htm) [a Veeam](https://www.veeam.com/blog/?p=123438)és a [Veritas](https://players.brightcove.net/4396107486001/default_default/index.html?videoId=6189967101001) is része volt a kezdeti előzetes verziónak, és kiterjesztettük a megoldásukat az SMB 3.0 és az NFS 4.1 Azure Files.

* <a id="migrate-nfs-data"></a>
**Át tudom miolni a meglévő adatokat egy NFS-megosztásba?**

    Egy régión belül az adatok áthelyezéséhez olyan standard eszközöket használhat, mint az scp, az rsync vagy az SSHFS. Mivel Azure Files NFS egyszerre több számítási példányból is elérhető, párhuzamos feltöltésekkel javíthatja a másolási sebességet. Ha egy régión kívülről szeretne adatokat behozni, VPN-en vagy ExpressRoute-on keresztül csatlakoztatja a fájlrendszert a helyszíni adatközpontból.

## <a name="on-premises-access"></a>Helyszíni hozzáférés

* <a id="port-445-blocked"></a>
**Az internetszolgáltató vagy az it-Azure Files blokkolja a 445-ös portot. Mit tegyek?**

    A [445-ös port](./storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked)letiltásán keresztüli áthidaló megoldásokról itt olvashat. Azure Files SMB 3.0-t (titkosítási támogatással) használó kapcsolatokat csak a régión vagy adatközponton kívülről engedélyezi. Az SMB 3.0 protokoll számos biztonsági funkciót vezetett be, beleértve az interneten keresztül rendkívül biztonságos csatornatitkosítást. Lehetséges azonban, hogy a 445-ös port le lett tiltva az alacsonyabb SMB-verziókban található biztonsági rések korábbi okai miatt. Ideális esetben a portot csak az SMB 1.0-forgalom esetében kell blokkolni, és az SMB 1.0-t minden ügyfélen ki kell kapcsolva.

* <a id="expressroute-not-required"></a>
**A helyszíni Azure ExpressRoute kell Azure Files a Azure File Sync való csatlakozáshoz?**  

    Nem. Az ExpressRoute nem szükséges az Azure-fájlmegosztások eléréséhez. Ha közvetlenül a helyszínen hoz létre Azure-fájlmegosztást, mindössze meg kell nyitnia a 445-ös (TCP kimenő) portot az internet-hozzáféréshez (ez az SMB által a kommunikációhoz használt port). Ha hálózati protokollt Azure File Sync, csak a 443-as (TCP kimenő) portra van szükség a HTTPS-hozzáféréshez (SMB nélkül). Az ExpressRoute-et azonban bármelyik hozzáférési lehetőséggel használhatja. 

* <a id="mount-locally"></a>
**Hogyan csatlakoztatok egy Azure-fájlmegosztást a helyi gépemhez?**  

    A fájlmegosztást az SMB protokoll használatával csatlakoztathatja, ha a 445-ös (TCP kimenő) port meg van nyitva, és az ügyfél támogatja az SMB 3.0 protokollt (például ha Windows 10 vagy Windows Server 2016-ot használ). Ha a szervezet szabályzata vagy az internetszolgáltató blokkolja a 445-ös portot, a Azure File Sync használhatja az Azure-fájlmegosztás eléréséhez.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Hogyan azure-fájlmegosztásomról?**  
    A véletlen törlésekkel [szembeni](storage-snapshots-files.md) védelem érdekében rendszeres megosztási pillanatképeket használhat. Használhatja az AzCopyt, a Robocopyt vagy egy külső biztonsági mentési eszközt is, amely képes biztonsági másolatot készíteni egy csatlakoztatott fájlmegosztásról. Azure Backup biztonsági másolatot készít a Azure Files. További információ az [Azure-fájlmegosztások](../../backup/backup-afs.md)biztonsági mentése Azure Backup.

## <a name="share-snapshots"></a>Pillanatképek megosztása

### <a name="share-snapshots-general"></a>Megosztási pillanatképek: Általános
* <a id="what-are-snaphots"></a>
**Mik azok a fájlmegosztási pillanatképek?**  
    Az Azure-fájlmegosztások pillanatképei segítségével létrehozhatja a fájlmegosztások csak olvasható verzióját. A tartalom korábbi Azure Files is visszamásolódhat ugyanabba a megosztásba, az Azure-ban egy másik helyre vagy a helyszínen további módosításokhoz. A megosztási pillanatképekkel kapcsolatos további információkért lásd a [pillanatképek megosztásának áttekintését.](storage-snapshots-files.md)

* <a id="where-are-snapshots-stored"></a>
**Hol vannak tárolva a megosztási pillanatképek?**  
    A megosztási pillanatképek a fájlmegosztással azonos tárfiókban vannak tárolva.

* <a id="snapshot-consistency"></a>
**A megosztási pillanatképek alkalmazás-konzisztensek?**  
    Nem, a megosztási pillanatképek nem alkalmazás-konzisztensek. A felhasználónak a megosztási pillanatkép készítése előtt ki kell ürítenie az írásokat az alkalmazásból a megosztásba.

* <a id="snapshot-limits"></a>
**Van korlátja a használható megosztási pillanatképek számának?**  
    Igen. Azure Files legfeljebb 200 megosztási pillanatképet őrizhet meg. A megosztási pillanatképek nem számítanak bele a megosztási kvótába, így nincs megosztásonkénti korlát az összes megosztási pillanatkép által használt teljes területre vonatkozóan. A tárfiók korlátai továbbra is érvényben vannak. 200 megosztási pillanatképet követően törölnie kell a régebbi pillanatképeket az új megosztási pillanatképek létrehozásához.

* <a id="snapshot-cost"></a>
**Mennyibe kerül a megosztási pillanatképek készítése?**  
    A standard tranzakciós és standard tárolási költségek a pillanatképre vonatkoznak. A pillanatképek növekményes jellegűek. Az alap pillanatkép maga a megosztás. Az ezt követő pillanatképek növekményesek, és csak az előző pillanatkép különbségét tárolják. Ez azt jelenti, hogy a számlán látható változásváltozások minimálisak lesznek, ha a számítási feladat lemorzsolódása minimális. A [Standard díjszabási](https://azure.microsoft.com/pricing/details/storage/files/) információkért Azure Files tekintse meg a Díjszabás oldalt. Napjainkban a megosztási pillanatképek által felhasznált méreteket a kiszámlázott kapacitás és a felhasznált kapacitás összehasonlításával lehet megösszehasonlítani. Dolgozunk a jelentéskészítés javításán.

* <a id="ntfs-acls-snaphsots"></a>
**A könyvtárakon és fájlokon található NTFS ACL-ek megosztási pillanatképben vannak megőrzve?**  
    A könyvtárakon és fájlokon található NTFS ACL-ek megosztási pillanatképben vannak megőrzve.

### <a name="create-share-snapshots"></a>Megosztási pillanatképek létrehozása
* <a id="file-snaphsots"></a>
**Létrehozhatok megosztási pillanatképet az egyes fájlokról?**  
    A megosztási pillanatképek a fájlmegosztás szintjén vannak létrehozva. Egyes fájlokat visszaállíthat a fájlmegosztási pillanatképből, fájlszintű megosztási pillanatképek azonban nem hozhatók létre. Ha azonban megosztási szintű megosztási pillanatképet készített, és szeretné listába sorolni azokat a  megosztási pillanatképeket, amelyekben egy adott fájl módosult, ezt a Korábbi verziók alatt használhatja egy Windows által csatlakoztatott megosztáson. 
    
    Ha szüksége van egy fájl-pillanatkép funkcióra, a [UserVoice Azure Files tudassa velünk.](https://feedback.azure.com/forums/217298-storage/category/180670-files)

* <a id="encrypted-snapshots"></a>
**Létrehozhatok megosztási pillanatképeket egy titkosított fájlmegosztásról?**  
    Pillanatképet készíthet az olyan Azure-fájlmegosztások megosztási pillanatképről, amelyeken engedélyezve van az aktív titkosítás. A fájlokat visszaállíthatja egy megosztási pillanatképből egy titkosított fájlmegosztásba. Ha a megosztás titkosítva van, a megosztási pillanatkép is titkosítva lesz.

* <a id="geo-redundant-snaphsots"></a>
**Georedundánsak a megosztási pillanatképek?**  
    A megosztási pillanatképek ugyanolyan redundanciával vannak, mint az Azure-fájlmegosztás, amelyhez készült. Ha georedundáns tárolást választott a fiókjához, a megosztási pillanatkép is redundánsan lesz tárolva a párosított régióban.

### <a name="manage-share-snapshots"></a>Megosztási pillanatképek kezelése
* <a id="browse-snapshots-linux"></a>
**Böngészhetünk a megosztási pillanatképek között Linuxról?**  
    Az Azure CLI használatával megosztási pillanatképeket hozhat létre, listózhat, tallózhat és visszaállíthat Linux rendszeren.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Átmásolhatja a megosztási pillanatképeket egy másik tárfiókba?**  
    A fájlokat átmásolhatja a megosztási pillanatképekből egy másik helyre, de maguk a megosztási pillanatképek nem másolhatóak.

### <a name="restore-data-from-share-snapshots"></a>Adatok visszaállítása megosztási pillanatképből
* <a id="promote-share-snapshot"></a>
**Előléptethető megosztási pillanatkép az alap megosztásra?**  
    Az adatokat átmásolhatja egy megosztási pillanatképből bármely más célhelyre. A megosztási pillanatképek nem előléptethetőek az alap megosztásra.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Visszaállíthatóak adatok a megosztási pillanatképből egy másik tárfiókba?**  
    Igen. A megosztási pillanatképből származó fájlok átmásolhatók az eredeti helyre vagy egy másik helyre, amely ugyanazt a tárfiókot vagy egy másik tárfiókot tartalmaz, akár ugyanabban a régióban, akár különböző régiókban. A fájlokat helyszíni helyre vagy bármely más felhőbe is másolhatja.    
  
### <a name="clean-up-share-snapshots"></a>Megosztási pillanatképek tisztítása
* <a id="delete-share-keep-snapshots"></a>
**Törölhető a megosztásom, de a megosztási pillanatképek nem?**  
    Ha aktív megosztási pillanatképekkel van a megosztáson, nem törölheti a megosztást. Az API-k használatával a megosztási pillanatképek és a megosztások is törölhetők. A megosztási pillanatképeket és a megosztást is törölheti a Azure Portal.

* <a id="delete-share-with-snapshots"></a>
**Mi történik a megosztási pillanatképekkel, ha törölöm a tárfiókomat?**  
    Ha törli a tárfiókot, a megosztási pillanatképek is törlődnek.

## <a name="billing-and-pricing"></a>Számlázás és díjszabás
* <a id="vm-file-share-network-traffic"></a>
**Az Azure-beli virtuális gép és az Azure-fájlmegosztás közötti hálózati forgalom az előfizetésért felszámított külső sávszélességnek számít?**  
    Ha a fájlmegosztás és a virtuális gép ugyanabban az Azure-régióban található, a fájlmegosztás és a virtuális gép közötti forgalomért nem számítunk fel további díjat. Ha a fájlmegosztás és a virtuális gép különböző régiókban található, a köztük található forgalomért külső sávszélességként kell fizetni.

* <a id="share-snapshot-price"></a>
**Mennyibe kerül a megosztási pillanatképek készítése?**  
     Az előzetes verzió ideje alatt a megosztási pillanatképek kapacitása díjmentes. A standard tárolási ki- és tranzakciós költségek érvényesek. Az általánosan elérhető kapacitásért és a megosztási pillanatképek tranzakcióiért az előfizetések díjat számítunk fel.
     
     A megosztási pillanatképek növekményes jellegűek. Az alap megosztás pillanatképe maga a megosztás. Minden további megosztási pillanatkép növekményes, és csak az előző megosztási pillanatképtől való különbséget tárolja. Csak a módosított tartalomért kell fizetni. Ha 100 GiB adatot tartalmaz, de csak 5 GiB változott a legutóbbi megosztási pillanatkép óta, a megosztás pillanatképe csak 5 giB-t fogyaszt, és 105 GiB-ra lesz számlázva. További információ a tranzakciós és standard bejövő forgalom díjárairól: [Díjszabás.](https://azure.microsoft.com/pricing/details/storage/files/)

## <a name="scale-and-performance"></a>Méretezés és teljesítmény
* <a id="files-scale-limits"></a>
**Mik a méretezési korlátok a Azure Files?**  
    További információ a méretezési és teljesítménycélokról a Azure Files: Azure Files [és teljesítménycélok.](storage-files-scale-targets.md)

* <a id="need-larger-share"></a>
**Milyen méretek érhetők el az Azure-fájlmegosztások esetében?**  
    Az Azure-fájlmegosztások mérete (prémium és standard) akár 100 TiB-ra is skálázhat. A standard szintű csomag nagyobb fájlmegosztásokbe való bevezető útmutatóját a tervezési útmutató Nagy fájlmegosztások létrehozása [(standard szintű)](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) című szakaszában láthatja.

* <a id="lfs-performance-impact"></a>
**Befolyásolja a fájlmegosztási kvóta bővítése a számítási feladatokat vagy Azure File Sync?**
    
    Nem. A kvóta bővítése nem befolyásolja a számítási feladatokat és Azure File Sync.

* <a id="open-handles-quota"></a>
**Hány ügyfél férhet hozzá egyidejűleg egy fájlhoz?**   
    Egy fájlhoz 2000 megnyitott leíróra vonatkozó kvóta van. Ha 2000 megnyitott leírója van, egy hibaüzenet jelenik meg, amely szerint elérte a kvótát.

* <a id="zip-slow-performance"></a>
**A teljesítmény lassú, amikor kicsomagolok fájlokat a Azure Files. Mit tegyek?**  
    Ha nagy mennyiségű fájlt szeretnénk áthelyezni a Azure Files, javasoljuk, hogy használja az AzCopyt (Windows rendszeren, előzetes verzióban Linux és UNIX esetén) vagy Azure PowerShell. Ezek az eszközök hálózati átvitelre vannak optimalizálva.

* <a id="slow-perf-windows-81-2012r2"></a>
**Miért lassú a teljesítmény, miután csatlakoztattam az Azure-fájlmegosztásomat Windows Server 2012 R2 vagy Windows 8.1 rendszeren?**  
    Az Azure-fájlmegosztások Windows Server 2012 R2 és Windows 8.1 rendszeren való csatlakoztatása ismert probléma. A problémát a Windows 8.1 és a Windows Server 2012 R2 2014. áprilisi összegző frissítésében javítottuk. Az optimális teljesítmény érdekében győződjön meg arról, hogy a Windows Server 2012 R2 és Windows 8.1 összes példánya alkalmazva van erre a javításra. (A Windows-javításokat mindig a Windows Update.) További információért tekintse meg a Microsoft tudásbázis lassú teljesítményt a [Azure Files.1 vagy Server 2012 R2](https://support.microsoft.com/kb/3114025)Windows 8-ről való elérésekor.

## <a name="features-and-interoperability-with-other-services"></a>Funkciók és együttműködés más szolgáltatásokkal
* <a id="cluster-witness"></a>
**Használhatom az Azure-fájlmegosztásomat tanúsító fájlmegosztásként *a* Windows Server feladatátvevő fürthöz?**  
    Az Azure-fájlmegosztások jelenleg nem támogatják ezt a konfigurációt. További információ arról, hogyan állíthatja be ezt az Azure Blob Storage-hoz: Felhőbeli tanúsító üzembe helyezése [feladatátvevő fürthöz.](/windows-server/failover-clustering/deploy-cloud-witness)

* <a id="containers"></a>
**Csatlakoztatok Egy Azure-fájlmegosztást egy Azure Container Instance-példányhoz?**  
    Igen, az Azure-fájlmegosztások akkor jó választásak, ha a tárolópéldányok élettartamán túl is meg szeretné jelenni az adatokat. További információ: [Azure-fájlmegosztás csatlakoztatása az Azure Container Instances használatával.](../../container-instances/container-instances-volume-azure-files.md)

* <a id="rest-rename"></a>
**Van átnevezés művelet a REST API?**  
    Jelenleg nem.

* <a id="nested-shares"></a>
**Be tudok állítani beágyazott megosztásokat? Más szóval egy megosztás egy megosztás alatt?**  
    Nem. A fájlmegosztás *az* a virtuális illesztőprogram, amelyet csatlakoztathat, ezért a beágyazott megosztások nem támogatottak.

* <a id="ibm-mq"></a>
**Hogyan az AZURE FILES AZ IBM MQ-val?**  
    Az IBM kiadott egy dokumentumot, amely segítséget nyújt az IBM MQ-ügyfeleknek a Azure Files az IBM szolgáltatással való konfigurálásban. További információ: [Ibm MQ](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service)többpéldányos üzenetsor-kezelő beállítása a Microsoft Azure Files szolgáltatással.

## <a name="see-also"></a>Lásd még
* [Hibaelhárítás Azure Files Windowsban](storage-troubleshoot-windows-file-connection-problems.md)
* [Hibaelhárítás Azure Files Linux rendszeren](storage-troubleshoot-linux-file-connection-problems.md)
* [Azure File Sync – hibaelhárítás](../file-sync/file-sync-troubleshoot.md)
