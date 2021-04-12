---
title: Gyakori kérdések (GYIK) a Azure Files | Microsoft Docs
description: Választ kaphat Azure Files gyakori kérdésekre. Az Azure-fájlmegosztás párhuzamosan csatlakoztatható a felhőben vagy a helyszíni Windows, Linux vagy macOS rendszerű környezetekben.
author: roygara
ms.service: storage
ms.date: 02/23/2020
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: ec8104a5fd8d1c524f75c7a5173015115d85a253
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064307"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Az Azure Filesszal kapcsolatos gyakori kérdések (GYIK)
A [Azure Files](storage-files-introduction.md) teljes körűen felügyelt fájlmegosztást biztosít a felhőben, amely az iparági szabványnak megfelelő [SMB protokollon](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) és a [Network File System (NFS) protokollon](https://en.wikipedia.org/wiki/Network_File_System) (előzetes verzió) keresztül érhető el. Az Azure-fájlmegosztás párhuzamosan csatlakoztatható a Felhőbeli vagy a Windows, Linux és macOS rendszerű helyszíni környezetekhez. Az Azure-fájlmegosztás a Windows Server rendszerű gépeken is gyorsítótárazható a Azure File Sync használatával a gyors eléréshez, ahol az adott adatforgalomhoz közeledik.

Ez a cikk a Azure Files szolgáltatásokkal és funkciókkal kapcsolatos gyakori kérdésekre ad választ, beleértve a Azure File Sync használatát Azure Files használatával. Ha nem látja a választ a kérdésére, felveheti velünk a kapcsolatot a következő csatornákon keresztül (növekvő sorrendben):

1. A cikk megjegyzések szakasza.
2. [A Microsoft Q&az Azure Storage-ra vonatkozó kérdés oldalát](/answers/topics/azure-file-storage.html).
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Microsoft ügyfélszolgálata. Új támogatási kérelem létrehozásához a Azure Portal **Súgó** lapján kattintson a **Súgó + támogatás** gombra, majd válassza az **új támogatási kérelem** lehetőséget.

## <a name="general"></a>Általános kérdések
* <a id="why-files-useful"></a>
  **Hogyan hasznos a Azure Files?**  
   A Azure Files használatával hozhat létre fájlmegosztást a felhőben anélkül, hogy a felelős a fizikai kiszolgáló, eszköz vagy berendezés terhelésének kezeléséért. Az Ön számára monoton munkát végezünk, beleértve az operációs rendszer frissítéseinek alkalmazását és a hibás lemezek cseréjét. Ha többet szeretne megtudni azokról a forgatókönyvekről, amelyeket a Azure Files segíthetnek, tekintse meg a [miért Azure Files hasznos](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
  **Milyen különböző módokon férhet hozzá a Azure Files lévő fájlokhoz?**  
    Az SMB-fájlmegosztás az SMB 3,0 protokoll használatával csatlakoztatható a helyi gépre, vagy használhat olyan eszközöket, mint a [Storage Explorer](https://storageexplorer.com/) a fájlmegosztás fájljainak eléréséhez. Az NFS-fájlmegosztás csatlakoztatható a helyi gépen a Azure Portal által megadott parancsfájl másolásával/beillesztésével. Az alkalmazásból a Storage ügyféloldali kódtárait, a REST API-kat, a PowerShellt vagy az Azure CLI-t használhatja az Azure-fájlmegosztás fájljainak eléréséhez.

* <a id="what-is-afs"></a>
  **Mi az Azure File Sync?**  
    A Azure File Sync segítségével központilag kezelheti a szervezete fájlmegosztást Azure Filesban, miközben megőrizheti a helyszíni fájlkiszolgáló rugalmasságát, teljesítményét és kompatibilitását. Azure File Sync átalakítja a Windows Server-gépeket az Azure-fájlmegosztás gyors gyorsítótárba. A Windows Serveren elérhető bármely protokoll használatával helyileg férhet hozzá az adataihoz, beleértve az SMB-t, a hálózati fájlrendszert (NFS) és a File Transfer Protocol szolgáltatást (FTPS). Tetszőleges számú gyorsítótárral rendelkezhet a világ minden tájáról.

* <a id="files-versus-blobs"></a>
  **Miért érdemes egy Azure-fájlmegosztást és az Azure Blob Storage-ot használni az adataim számára?**  
    A Azure Files és az Azure Blob Storage szolgáltatással a Felhőbeli nagy mennyiségű adattárolási módszer is használható, de ez némileg eltérő célokra is hasznos lehet. 
    
    Az Azure Blob Storage olyan nagy méretű, Felhőbeli natív alkalmazások esetében hasznos, amelyeknek strukturálatlan adatmennyiséget kell tárolniuk. A teljesítmény és a méretezés maximalizálása érdekében az Azure Blob Storage egy egyszerű tárolási absztrakció, mint a valódi fájlrendszer. Az Azure Blob Storage-t csak REST-alapú ügyféloldali kódtárak (vagy közvetlenül a REST-alapú protokollon keresztül) érheti el.

    Azure Files kifejezetten fájlrendszer. Azure Files rendelkezik a fájl összes olyan absztrakt nevével, amelyet a helyszíni operációs rendszerekkel kapcsolatos évek óta ismer és szeret. Az Azure Blob Storage-hoz hasonlóan a Azure Files REST-felületet és REST-alapú ügyféloldali kódtárakat is biztosít. Az Azure Blob Storage szolgáltatástól eltérően a Azure Files SMB-vagy NFS-hozzáférést biztosít az Azure-fájlmegosztás számára. A fájlmegosztás közvetlenül csatlakoztatható Windows, Linux vagy macOS rendszerű számítógépeken, akár helyszíni, akár Felhőbeli virtuális gépeken, anélkül, hogy kódot kellene írnia, vagy speciális illesztőprogramokat kellene csatolni a fájlrendszerhez. Az Azure SMB-fájlmegosztást a helyszíni fájlkiszolgálók esetében Azure File Sync használatával is gyorsítótárazhatja a gyors hozzáférés érdekében, az adatforgalom helyétől függően. 
   
    Az Azure Files és az Azure Blob Storage közötti különbségek részletesebb leírását lásd: [az alapszintű Azure Storage szolgáltatás bemutatása](../common/storage-introduction.md). További információ az Azure Blob Storage-ról: [Bevezetés a blob Storage](../blobs/storage-blobs-introduction.md)-ba.

* <a id="files-versus-disks"></a>**Miért érdemes Azure-fájlmegosztást használni az Azure-lemezek helyett?**  
    Az Azure-lemezeken található lemezek egyszerűen egy lemez. Az Azure-lemezek értékének lekéréséhez csatolni kell egy lemezt az Azure-ban futó virtuális géphez. Az Azure-lemezek minden olyan eszközhöz használhatók, amely a helyszíni kiszolgálók lemezét fogja használni. Használhatja operációs rendszer lemezként, mint egy operációs rendszer vagy egy alkalmazás dedikált tárterülete. Az Azure-lemezek érdekes használata egy olyan fájlkiszolgáló létrehozása a felhőben, amely ugyanazon a helyen használható, ahol Azure-fájlmegosztást is használhat. Egy fájlkiszolgáló Virtual Machines Azure-beli üzembe helyezése nagy teljesítményű megoldás az Azure-ban való tároláshoz, ha olyan központi telepítési beállításokat igényel, amelyeket a Azure Files jelenleg nem támogat. 

    Ha azonban az Azure-lemezeket futtató fájlkiszolgáló a háttérbeli tárolóként jellemzően sokkal drágább, mint az Azure-fájlmegosztás használata, néhány ok miatt. Először is, a lemezes tárolás kifizetése mellett fizetnie kell egy vagy több Azure-beli virtuális gép futtatásának költségeiért. Másodszor, a fájlkiszolgáló futtatásához használt virtuális gépeket is kezelnie kell. Tegyük fel, hogy az operációs rendszer frissítéseiért felelős. Végül, ha végül a helyszíni gyorsítótárba helyezi az adattárolást, a replikációs technológiák (például a elosztott fájlrendszer replikáció (DFSR)) beállítása és kezelése a következő módon történik.

    Az Azure Files és az Virtual Machines Azure-ban üzemeltetett fájlkiszolgáló (az Azure-lemezek háttér-tárolóként való használata mellett) egyik megközelítése, hogy a Azure File Synct egy felhőalapú virtuális gépen üzemeltetett fájlkiszolgálón telepítse. Ha az Azure-fájlmegosztás abban a régióban van, ahol a fájlkiszolgáló található, engedélyezheti a Felhőbeli adatmennyiséget, és beállíthatja a szabad terület százalékos arányát a maximális értékre (99%). Ez minimálisan duplikált adatmennyiséget biztosít. Használhatja a fájlkiszolgálók által kívánt alkalmazásokat, például az NFS protokoll támogatását igénylő alkalmazásokat is.

    A nagy teljesítményű és magas rendelkezésre állású fájlkiszolgáló Azure-beli beállításával kapcsolatos további információkért lásd: [IAAS VM-vendég fürtök üzembe helyezése Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). A Azure Files és az Azure-lemezek közötti különbségek részletesebb ismertetését lásd: [az alapszintű Azure Storage szolgáltatás bemutatása](../common/storage-introduction.md). További információ az Azure-lemezekről: [azure Managed Disks – áttekintés](../../virtual-machines/managed-disks-overview.md).

* <a id="get-started"></a>
  **Hogyan az Azure Files használatának megkezdése?**  
   A Azure Files első lépései egyszerűek. Először [hozzon létre egy SMB-fájlmegosztást](storage-how-to-create-file-share.md) , vagy hozzon [létre egy NFS-megosztást](storage-files-how-to-create-nfs-shares.md), majd csatlakoztassa az előnyben részesített operációs rendszerhez: 

  * [SMB-megosztás csatlakoztatása a Windowsban](storage-how-to-use-files-windows.md)
  * [SMB-megosztás csatlakoztatása Linux rendszeren](storage-how-to-use-files-linux.md)
  * [SMB-megosztás csatlakoztatása macOS-ben](storage-how-to-use-files-mac.md)
  * [NFS-fájlmegosztás csatlakoztatása](storage-files-how-to-mount-nfs-shares.md)

    Az Azure-fájlmegosztás a szervezeten belüli éles fájlmegosztás helyett történő üzembe helyezésével kapcsolatos részletes útmutatót a [Azure Files központi telepítésének tervezése](storage-files-planning.md)című témakörben talál.

* <a id="redundancy-options"></a>
  **Milyen tárolási redundancia-beállításokat támogat a Azure Files?**  
    Jelenleg a Azure Files támogatja a helyileg redundáns tárolást (LRS), a Zone redundáns tárolást (ZRS), a Geo-redundáns tárolást (GRS) és a Geo-zóna-redundáns tárolást (GZRS). A prémium szintű Azure Files jelenleg csak a LRS és a ZRS támogatja.

* <a id="tier-options"></a>
  **Milyen tárolási rétegek támogatottak a Azure Files?**  
    Azure Files támogatja a két tárolási szintet: prémium és standard. A standard fájlmegosztást az általános célú (GPv1 vagy GPv2) Storage-fiókok, a prémium fájlmegosztás pedig a FileStorage Storage-fiókokban hozza létre. További információ a [szabványos fájlmegosztás](storage-how-to-create-file-share.md) és a [prémium szintű fájlmegosztás](./storage-how-to-create-file-share.md)létrehozásáról. 
    
    > [!NOTE]
    > Azure-fájlmegosztás nem hozható létre blob Storage-fiókokból vagy *prémium* általános célú (GPv1 vagy GPv2) Storage-fiókból. A standard szintű Azure-fájlmegosztás csak *szabványos* általános célú fiókokban hozható létre, és a prémium szintű Azure-fájlmegosztás csak FileStorage Storage-fiókokban hozható létre. A *prémium* szintű általános célú (GPv1 és GPv2) Storage-fiókok csak a prémium szintű lapokat tartalmazó Blobok esetén érhetők el. 

* <a id="file-locking"></a>
  **Támogatja a Azure Files a fájlok zárolását?**  
    Igen, Azure Files teljes mértékben támogatja az SMB-/Windows-stílusú fájlok zárolását, [lásd: részletek](/rest/api/storageservices/managing-file-locks).

* <a id="give-us-feedback"></a>
  **Szeretném megtekinteni a Azure Fileshoz hozzáadott speciális funkciót. Felveheti?**  
    A Azure Files csapata érdekli a szolgáltatással kapcsolatos összes visszajelzés meghallgatása. Kérjük, szavazzon a szolgáltatásra vonatkozó kérésekre [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Örömmel várjuk, hogy számos új funkciót kínálunk.

## <a name="azure-file-sync"></a>Azure File Sync

* <a id="afs-region-availability"></a>
  **Milyen régiókat támogat a Azure File Sync?**  
    Az elérhető régiók listája a Azure File Sync tervezési útmutató [régió rendelkezésre állása](storage-sync-files-planning.md#azure-file-sync-region-availability) szakaszában található. Folyamatosan bővítjük a további régiókat, köztük a nem nyilvános régiókat is.

* <a id="cross-domain-sync"></a>
  **Vannak tartományhoz csatlakoztatott és tartományhoz nem csatlakoztatott kiszolgálók ugyanabban a szinkronizálási csoportban?**  
    Igen. A szinkronizálási csoport olyan kiszolgálói végpontokat tartalmazhat, amelyek különböző Active Directory tagsággal rendelkeznek, még akkor is, ha nincsenek tartományhoz csatlakoztatva. Bár ez a konfiguráció technikailag működik, ezt a konfigurációt nem javasoljuk, mert az egyik kiszolgálón lévő fájlokhoz és mappákhoz definiált hozzáférés-vezérlési listák (ACL-ek) nem kényszeríthető ki a szinkronizálási csoport többi kiszolgálója. A legjobb eredmény érdekében javasoljuk, hogy szinkronizálja az azonos Active Directory erdőben található kiszolgálók között, a különböző Active Directory erdőkben lévő kiszolgálók között, amelyek megbízhatósági kapcsolatot létesítettek, vagy olyan kiszolgálók között, amelyek nem egy tartományban találhatók. Javasoljuk, hogy ne használja a konfigurációk kombinációját.

* <a id="afs-change-detection"></a>
  **Létrehoztam egy fájlt közvetlenül az Azure-fájlmegosztást az SMB vagy a portál használatával. Mennyi ideig tart a fájl szinkronizálása a szinkronizálási csoportban lévő kiszolgálókhoz?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]


* <a id="afs-sync-time"></a>
  **Mennyi ideig tart a Azure File Sync az 1TiB feltöltéséhez?**
  
    A teljesítmény a környezeti beállításoktól, a konfigurációtól és attól függően változik, hogy ez egy kezdeti szinkronizálás vagy egy folyamatban lévő szinkronizálás. További információ: [Azure file Sync teljesítmény mérőszámai](storage-files-scale-targets.md#azure-file-sync-performance-metrics)

* <a id="afs-conflict-resolution"></a>**Ha ugyanezt a fájlt két kiszolgálón is megváltoztatják egy időben, mi történik?**  
    A Azure File Sync egy egyszerű ütközés-feloldási stratégiát használ: egyszerre két végponton módosult fájlok változásai is megmaradnak. A legutóbb írt módosítás megtartja az eredeti fájlnevet. A régebbi (LastWriteTime által meghatározott) fájl a végpont nevét és a fájlnévhez hozzáfűzött ütközési számot tartalmaz. A kiszolgálói végpontok esetében a végpont neve a kiszolgáló neve. A Felhőbeli végpontok esetén a végpont neve **felhő**. A név ezt a besorolást követi: 
   
    \<FileNameWithoutExtension\>-\<endpointName\>\[-#\].\<ext\>  

    Például az első ütközés a CompanyReport.docx CompanyReport-CentralServer.docx, ha a CentralServer, ahol a régebbi írás történt. A második ütközés neve CompanyReport-CentralServer-1.docx. A Azure File Sync fájlon keresztül támogatja az 100-es ütközési fájlokat. Ha elérte az ütköző fájlok maximális számát, a fájl szinkronizálása sikertelen lesz, amíg az ütköző fájlok száma nem haladja meg a 100-ot.

* <a id="afs-storage-redundancy"></a>
  **A Geo-redundáns tárolás Azure File Sync támogatott?**  
    Igen, Azure Files támogatja a helyileg redundáns tárolást (LRS) és a Geo-redundáns tárolást (GRS). Ha a GRS konfigurált fiókból kezdeményezi a tárolási fiók feladatátvételét a párosított régiók között, a Microsoft azt javasolja, hogy az új régiót csak az adatok biztonsági másolatának megfelelően kezelje. A Azure File Sync nem kezdi meg automatikusan a szinkronizálást az új elsődleges régióval. 

* <a id="sizeondisk-versus-size"></a>
  **Miért nem egyezik *meg* a fájl mérete a *méret tulajdonsággal* a Azure file Sync használata után?**  
  Lásd: [Azure file Sync felhőalapú rétegek megismerése](storage-sync-cloud-tiering-overview.md#tiered-vs-locally-cached-file-behavior).

* <a id="is-my-file-tiered"></a>
  **Honnan tudhatom meg, hogy van-e lépcsőzetesen egy fájl?**  
  Lásd: [Azure file Sync rétegű fájlok kezelése](storage-sync-how-to-manage-tiered-files.md#how-to-check-if-your-files-are-being-tiered).

* <a id="afs-recall-file"></a>**A használni kívánt fájlt a rendszer lépcsőzetesen felhasználta. Hogyan hívhatom fel a fájlt a lemezre helyileg való használatra?**  
  Lásd: [Azure file Sync rétegű fájlok kezelése](storage-sync-how-to-manage-tiered-files.md#how-to-recall-a-tiered-file-to-disk).

* <a id="afs-force-tiering"></a>
  **Hogyan egy fájl vagy könyvtár lépcsőzetes kikényszerítését?**  
  Lásd: [Azure file Sync rétegű fájlok kezelése](storage-sync-how-to-manage-tiered-files.md#how-to-force-a-file-or-directory-to-be-tiered).

* <a id="afs-effective-vfs"></a>
  **Hogyan történik a *kötetek szabad területének* értelmezése, ha több kiszolgálói végpontom van egy köteten?**  
  Lásd: [Azure file Sync felhő-rétegek kiválasztásának házirendje](storage-sync-cloud-tiering-policy.md#multiple-server-endpoints-on-a-local-volume).
  
* <a id="afs-tiered-files-tiering-disabled"></a>
  **Letiltottam a Felhőbeli letiltást, miért vannak a kiszolgálói végpontok helyen található, lépcsőzetes fájlok?**  
    A kiszolgálói végpont helyének két oka lehet a többrétegű fájlok:

    - Ha új kiszolgálói végpontot ad hozzá egy meglévő szinkronizálási csoporthoz, ha a kezdeti letöltési mód beállításnál a névtér felidézése vagy a csak a névtér felidézése lehetőséget választja, akkor a fájlok a következőként jelennek meg, amíg helyileg le nem töltik. Ennek elkerüléséhez jelölje be a rétegek közötti fájlok elkerülése lehetőséget a kezdeti letöltési módhoz. A fájlok manuális felidézéséhez használja a Call [-StorageSyncFileRecall](storage-sync-how-to-manage-tiered-files.md#how-to-recall-a-tiered-file-to-disk) parancsmagot.

    - Ha a felhő-rétegek engedélyezve lettek a kiszolgálói végponton, majd letiltották, akkor a fájlok addig lesznek feldolgozva, amíg el nem éri őket.

* <a id="afs-tiered-files-not-showing-thumbnails"></a>
  **A Windows Intézőben miért nem láthatók miniatűr vagy előzetes verziók a többrétegű fájlok számára?**  
    A többhelyes fájlok esetében a miniatűrök és az előzetes verziók nem láthatók a kiszolgálói végponton. Ez a viselkedés várható, mivel a Windows miniatűr gyorsítótár funkciója szándékosan kihagyja a fájlok olvasását az offline attribútummal. Ha engedélyezve van a felhőalapú rétegek beolvasása, a többoldalas fájlok olvasásával le lehet tölteni őket (visszahívásra).

    Ez a viselkedés nem jellemző a Azure File Syncre, a Windows Intéző megjeleníti a "szürke X" értéket minden olyan fájlnál, amelynél az offline attribútum be van állítva. Az X ikon jelenik meg, amikor SMB-kapcsolaton keresztül fér hozzá a fájlokhoz. A viselkedés részletes ismertetését a következő témakörben találja: [https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)

    A többplatformos fájlok kezelésével kapcsolatos kérdéseit lásd: [a többplatformos fájlok kezelése](storage-sync-how-to-manage-tiered-files.md).

* <a id="afs-files-excluded"></a>
  **Mely fájlokat és mappákat automatikusan kizárja a Azure File Sync?**  
  Lásd: [fájlok kihagyva](storage-sync-files-planning.md#files-skipped).

* <a id="afs-os-support"></a>
  **Használhatom Azure File Sync a Windows Server 2008 R2, a Linux vagy a saját hálózattal csatlakoztatott tároló (NAS) eszközzel?**  
    Jelenleg a Azure File Sync csak a Windows Server 2019, a Windows Server 2016 és a Windows Server 2012 R2 rendszert támogatja. Jelenleg nem rendelkezünk más csomagokkal, amelyeket megoszthatunk, de az ügyfelek igénye alapján további platformokat is támogatunk. Tudassa velünk [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) azokat a platformokat, amelyeket támogatni kíván.

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **Miért léteznek a többplatformos fájlok a kiszolgálói végpont névterén kívül?**  
    A Azure File Sync ügynök 3-as verziója előtt a Azure File Sync letiltotta a többplatformos fájlok áthelyezését a kiszolgálói végponton kívül, de a kiszolgálói végponttal megegyező köteten. A másolási műveletek, a nem többrétegű fájlok áthelyezése és a többrétegű kötetek áthelyezése nem érintett. Ennek a viselkedésnek az az oka, hogy a fájlkezelő és más Windows API-k a műveleteknek ugyanazon a köteten való mozgatásával (közel) a pillanatnyi átnevezési műveletek. Ez azt jelenti, hogy az áthelyezéssel a fájlkezelő vagy más áthelyezési módszer (például a parancssor vagy a PowerShell) nem válaszol, miközben Azure File Sync visszahívja az adatok felhőből való hívását. A [Azure file Sync ügynök verziójának 3.0.12.0](storage-files-release-notes.md#supported-versions)kezdődően az Azure file Sync lehetővé teszi, hogy egy többplatformos fájlt helyezzen át a kiszolgálói végponton kívül. Elkerüljük a korábban említett negatív hatásokat, mivel lehetővé teszik, hogy a többplatformos fájl a kiszolgálói végponton kívüli többplatformos fájlként is létezve legyen, majd a háttérben meghívja a fájlt. Ez azt jelenti, hogy az ugyanarra a kötetre irányuló mozdulatok azonnaliek, és az áthelyezés befejezése után az összes művelet visszahívja a fájlt a lemezre. 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **Probléma merült fel a Azure File Sync a saját kiszolgálón (szinkronizálás, felhőalapú rétegek stb.). El kell távolítani, majd újra létre kell hozni a kiszolgálói végpontot?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **Áthelyezhetem a Storage Sync szolgáltatást és/vagy a Storage-fiókot egy másik erőforráscsoport, előfizetés vagy Azure AD-bérlőbe?**  
   Igen, a Storage Sync szolgáltatás és/vagy a Storage-fiók áthelyezhető egy másik erőforráscsoport, előfizetés vagy Azure AD-bérlőbe. A Storage Sync szolgáltatás vagy a Storage-fiók áthelyezése után meg kell adnia a Microsoft. StorageSync alkalmazás elérését a Storage-fiókhoz (lásd: [Azure file Sync hozzáférése a Storage-fiókhoz](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Felhőbeli végpont létrehozásakor a Storage Sync szolgáltatásnak és a Storage-fióknak ugyanabban az Azure AD-bérlőben kell lennie. A Felhőbeli végpont létrehozása után a Storage Sync szolgáltatás és a Storage-fiók áthelyezhető a különböző Azure AD-bérlők között.
    
* <a id="afs-ntfs-acls"></a>
  **A Azure File Sync a könyvtár/fájl szintű NTFS ACL-ek és a Azure Files tárolt adathozzáférések megőrzése mellett?**

    Február 24-én a 2020-es verzióban az Azure file Sync által létrehozott új és meglévő ACL-ek NTFS-formátumban lesznek tárolva, és az Azure-fájlmegosztás felé irányuló ACL-módosítások szinkronizálása a szinkronizálási csoport összes kiszolgálójára történik. A Azure Files hozzáférés-vezérlési listáin végrehajtott módosítások az Azure file Sync használatával lesznek szinkronizálva. Az adatok Azure Filesba másolásakor győződjön meg arról, hogy olyan másolási eszközt használ, amely támogatja a szükséges "hűséget" ahhoz, hogy az attribútumokat, az időbélyegeket és az ACL-eket egy Azure-fájlmegosztásba másolja – SMB vagy REST használatával. Az Azure Copy-eszközök, például a AzCopy használata esetén fontos, hogy a legújabb verziót használja. Tekintse át a fájlmásolás- [eszközök táblázatát](storage-files-migration-overview.md#file-copy-tools) , és tekintse át az Azure Copy Tools eszközt, és győződjön meg arról, hogy a fájl összes fontos metaadatát át tudja másolni.

    Ha engedélyezte a Azure Backup a file Sync Managed file shares szolgáltatásban, a fájl ACL-ek továbbra is visszaállíthatók a biztonsági mentés visszaállítási munkafolyamatának részeként. Ez a teljes megosztásra vagy egyedi fájlokra/könyvtárakra is használható.

    Ha pillanatképeket használ a fájl-szinkronizálás által kezelt fájlmegosztás saját felügyeletű biztonsági mentési megoldásának részeként, akkor előfordulhat, hogy az ACL-ek nem állíthatók be megfelelően az NTFS ACL-re, ha a pillanatképek a 2020. február 24. előtt készültek. Ha ez történik, vegye fel a kapcsolatot az Azure ügyfélszolgálatával.

* <a id="afs-lastwritetime"></a>
  **Szinkronizálja Azure File Sync a LastWriteTime a címtárakban?**  
    Nem, Azure File Sync nem szinkronizálja a LastWriteTime a címtárakban. Ez az elvárt működés.
    
## <a name="security-authentication-and-access-control"></a>Biztonság, hitelesítés és hozzáférés-vezérlés
* <a id="ad-support"></a>
**Támogatott-e a Azure Files identitás-alapú hitelesítés és hozzáférés-vezérlés?**  
    
    Igen, Azure Files támogatja az identitás-alapú hitelesítést és a hozzáférés-vezérlést. Az identitás-alapú hozzáférés-vezérlés használatának két módja közül választhat: helyszíni Active Directory Domain Services vagy Azure Active Directory Domain Services (Azure AD DS). A helyszíni Active Directory Domain Services (AD DS) támogatja a hitelesítést a AD DS tartományhoz csatlakoztatott, helyszíni vagy Azure-beli gépek használatával az Azure-fájlmegosztás SMB-kapcsolaton keresztüli eléréséhez. Azure Files az Azure AD DS az SMB protokollon keresztüli hitelesítés lehetővé teszi, hogy az Azure AD DS tartományhoz csatlakoztatott Windows virtuális gépek hozzáférjenek a megosztásokhoz, könyvtárakhoz és fájlokhoz az Azure AD hitelesítő adataival. További részletekért lásd: [az SMB-hozzáférésre vonatkozó Azure Files identitás-alapú hitelesítés támogatásának áttekintése](storage-files-active-directory-overview.md). 

    A Azure Files két további módszert kínál a hozzáférés-vezérlés kezelésére:

    - A közös hozzáférésű aláírások (SAS) használatával olyan jogkivonatokat állíthat elő, amelyek meghatározott engedélyekkel rendelkeznek, és amelyek egy adott időintervallumra érvényesek. Létrehozhat például egy jogkivonatot csak olvasási hozzáféréssel egy adott fájlhoz, amely 10 perces lejáratú. Bárki, aki rendelkezik a jogkivonattal, míg a jogkivonat érvényes, csak olvasási hozzáféréssel rendelkezik a fájlhoz ezen a 10 percen belül. A közös hozzáférésű aláírási kulcsokat csak a REST API vagy az ügyféloldali kódtárak támogatják. A Storage-fiók kulcsainak használatával csatlakoztatnia kell az Azure-fájlmegosztást az SMB-kapcsolaton keresztül.

    - Azure File Sync megőrzi és replikálja az összes tulajdonosi ACL-t vagy DACL-t (akár Active Directory-alapú, akár helyi) az összes olyan kiszolgálói végpontra, amelyet szinkronizál. 
    
    Az Azure Storage szolgáltatásban támogatott összes protokoll átfogó megjelenítéséhez tekintse meg az [Azure Storage-hozzáférés engedélyezését](../common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ismertető témakört. 
    
* <a id="encryption-at-rest"></a>
**Hogyan biztosíthatom, hogy az Azure-fájlmegosztás titkosítatlan állapotban legyen?**  

    Igen. További információ: [Azure Storage Service encryption](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Hogyan biztosíthatok hozzáférést egy adott fájlhoz egy webböngésző használatával?**  

    A közös hozzáférési aláírások használatával olyan jogkivonatokat állíthat elő, amelyek meghatározott engedélyekkel rendelkeznek, és amelyek egy adott időintervallumra érvényesek. Létrehozhat például egy jogkivonatot, amely csak olvasási hozzáférést biztosít egy adott fájlhoz egy meghatározott ideig. Bárki, aki rendelkezik az URL-címmel, közvetlenül hozzáférhet a fájlhoz bármely webböngészőből, amíg a jogkivonat érvényes. Egyszerűen létrehozhat egy közös hozzáférési aláírási kulcsot egy olyan felhasználói felületen, mint például a Storage Explorer.

* <a id="file-level-permissions"></a>
**Lehetséges csak olvasási vagy csak írási engedélyeket adni a megosztáson belüli mappákhoz?**  

    Ha az SMB használatával csatlakoztatja a fájlmegosztást, nincs jogosultsága a mappa szintű vezérléssel. Ha azonban a REST API vagy az ügyféloldali kódtárak használatával hoz létre közös hozzáférési aláírást, megadhat csak olvasási vagy csak írási engedélyeket a megosztáson belüli mappákhoz.

* <a id="ip-restrictions"></a>
**Tudom megvalósítani az Azure-fájlmegosztás IP-korlátozásait?**  

    Igen. Az Azure-fájlmegosztás elérését korlátozhatja a Storage-fiók szintjén. További információ: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**Milyen adatmegfelelőségi szabályzatokat támogat az Azure Files?**  

   Azure Files ugyanazon a tárolási architektúrán fut, amelyet az Azure Storage más tárolási szolgáltatásaiban használ. Azure Files ugyanazokat az adatmegfelelőségi szabályzatokat alkalmazza, amelyek más Azure Storage-szolgáltatásokban is használhatók. Az Azure Storage-beli adatok megfelelőségével kapcsolatos további információkért tekintse meg az [Azure Storage megfelelőségi ajánlatait](../common/storage-compliance-offerings.md), és nyissa meg a [Microsoft adatvédelmi központját](https://microsoft.com/trustcenter/default.aspx).

* <a id="file-auditing"></a>
**Hogyan lehet naplózni a fájlok hozzáférését és a Azure Files változásait?**

  Két lehetőség áll rendelkezésre a Azure Files naplózási funkcióinak megadására:
  - Ha a felhasználók közvetlenül érik el az Azure-fájlmegosztást, az [Azure Storage-naplók (előzetes verzió)](../blobs/monitor-blob-storage.md?tabs=azure-powershell#analyzing-logs) segítségével nyomon követhetik a fájl módosításait és a felhasználói hozzáférést. Ezek a naplók hibaelhárítási célokra használhatók, és a kérelmeket a lehető legjobb módon naplózzák.
  - Ha a felhasználók egy olyan Windows Serveren keresztül érik el az Azure-fájlmegosztást, amelyen telepítve van a Azure File Sync ügynök, a Windows Serveren a fájl-és felhasználói hozzáférés nyomon követéséhez használjon [naplózási házirendet](/windows/security/threat-protection/auditing/apply-a-basic-audit-policy-on-a-file-or-folder) vagy harmadik féltől származó terméket. 
   
### <a name="ad-ds--azure-ad-ds-authentication"></a>AD DS & Azure AD DS Authentication
* <a id="ad-support-devices"></a>
**A Azure Files Azure Active Directory Domain Services (Azure AD DS) hitelesítés támogatja az Azure AD-beli hitelesítő adatokkal való SMB-hozzáférést az Azure AD-hez csatlakoztatott vagy regisztrált eszközökön?**

    Nem, ez a forgatókönyv nem támogatott.

* <a id="ad-vm-subscription"></a>
**Hozzáférhetek az Azure-fájlmegosztás Azure AD-beli hitelesítő adataival egy másik előfizetéshez tartozó virtuális gépről?**

    Ha az előfizetés, amely alatt a fájlmegosztás telepítve van, ugyanahhoz az Azure AD-bérlőhöz van társítva, mint az Azure AD DS-példány, amelyhez a virtuális gép tartományhoz csatlakozik, az Azure-fájlmegosztást ugyanazzal az Azure AD-beli hitelesítő adatokkal érheti el. A korlátozás nem az előfizetésre, hanem a kapcsolódó Azure AD-bérlőre is érvényes.
    
* <a id="ad-support-subscription"></a>
**Engedélyezhető az Azure-AD DS vagy helyszíni AD DS hitelesítés az Azure-fájlmegosztás esetében, amely eltér az Azure-fájlmegosztás elsődleges bérlőtől?**

    Nem, Azure Files csak az Azure AD DS vagy a helyszíni AD DS integrációt támogatja egy olyan Azure AD-Bérlővel, amely ugyanabban az előfizetésben található, mint a fájlmegosztás. Egy Azure AD-bérlőhöz csak egy előfizetés társítható. Ez a korlátozás az Azure AD DS és a helyszíni AD DS hitelesítési módszerekre is vonatkozik. Helyszíni AD DS hitelesítéshez való használatakor [a AD DS hitelesítő adatokat szinkronizálni kell az Azure ad](../../active-directory/hybrid/how-to-connect-install-roadmap.md) -vel, amelyhez a Storage-fiók társítva van.

* <a id="ad-linux-vms"></a>
**Támogatja az Azure AD DS vagy a helyszíni AD DS-hitelesítés az Azure-fájlmegosztás számára a Linux rendszerű virtuális gépeket?**

    Nem, a Linux rendszerű virtuális gépekről történő hitelesítés nem támogatott.

* <a id="ad-aad-smb-afs"></a>
**Azure File Sync által felügyelt fájlmegosztás támogatja az Azure AD DS vagy a helyszíni AD DS hitelesítést?**

    Igen, a Azure File Sync által felügyelt fájlmegosztás esetében engedélyezheti az Azure AD DS vagy a helyszíni AD DS hitelesítést. A könyvtár/fájl NTFS ACL-ek helyi fájlkiszolgálón való módosítása a Azure Files és fordítva történik.

* <a id="ad-aad-smb-files"></a>
**Hogyan ellenőrizhetem, hogy engedélyezve van-e a AD DS hitelesítés a Storage-fiókomban, és hogyan kérhető le a tartományi információ?**

    Útmutatásért lásd [itt](./storage-files-identity-ad-ds-enable.md#confirm-the-feature-is-enabled).

* <a id=""></a>
**Támogatja Azure Files Azure AD-hitelesítés a Linux rendszerű virtuális gépeket?**

    Nem, a Linux rendszerű virtuális gépekről történő hitelesítés nem támogatott.

* <a id="ad-multiple-forest"></a>
**A helyszíni AD DS az Azure-fájlmegosztás hitelesítése támogatja az integrációt egy AD DS környezettel több erdő használatával?**    

    Azure Files helyszíni AD DS hitelesítés csak annak a tartományi szolgáltatásnak az erdőjét integrálja, amelyhez a Storage-fiók regisztrálva van. Egy másik erdő hitelesítésének támogatásához a környezetnek megfelelően konfigurált erdőszintű megbízhatósági kapcsolattal kell rendelkeznie. A Azure Files regisztrálása AD DS szinte ugyanaz, mint egy normál fájlkiszolgáló, ahol identitást (számítógép vagy szolgáltatás bejelentkezési fiókját) hoz létre a hitelesítéshez AD DS. Az egyetlen különbség, hogy a Storage-fiók regisztrált SPN-je "file.core.windows.net" értékkel végződik, amely nem felel meg a tartomány utótagjának. Tekintse meg a tartományi rendszergazdát, és ellenőrizze, hogy szükséges-e az utótag-útválasztási házirend frissítése a különböző tartományi utótag miatti több erdős hitelesítés engedélyezéséhez. Az alábbi példát láthatjuk az utótag útválasztási házirendjének konfigurálásához.
    
    Példa: Ha egy erdőben lévő felhasználók egy olyan fájlmegosztást szeretnének elérni a B erdőben lévő tartományon regisztrált Storage-fiókkal, akkor ez nem fog automatikusan működni, mert a Storage-fiók egyszerű szolgáltatásnév nem egyezik az A erdőben található egyik tartomány utótagjának megfelelő utótaggal. Ezt a problémát a "file.core.windows.net" egyéni utótagja esetében az "A" erdő "B" erdőre való útválasztási szabályának manuális konfigurálásával lehet kezelni.
    Először hozzá kell adnia egy új egyéni utótagot a B erdőben. Ellenőrizze, hogy rendelkezik-e a megfelelő rendszergazdai engedélyekkel a konfiguráció módosításához, majd kövesse az alábbi lépéseket:   
    1. Bejelentkezés a B erdőhöz csatlakozó számítógép-tartományba
    2.  A "Active Directory tartományok és megbízhatósági kapcsolatok" konzol megnyitása
    3.  Kattintson a jobb gombbal a "Active Directory tartományok és megbízhatósági kapcsolatok" elemre.
    4.  Kattintson a Tulajdonságok elemre.
    5.  Kattintson a "Hozzáadás" gombra.
    6.  "File.core.windows.net" hozzáadása UPN-utótagként
    7.  A varázsló bezárásához kattintson az Apply (alkalmaz), majd az OK gombra.
    
    Ezután adja hozzá a utótag-útválasztási szabályt az A erdőben, hogy átirányítsa a B erdőre.
    1.  Bejelentkezés az A erdőhöz csatlakozó számítógép-tartományba
    2.  A "Active Directory tartományok és megbízhatósági kapcsolatok" konzol megnyitása
    3.  Kattintson a jobb gombbal arra a tartományra, amelyhez el szeretné érni a fájlmegosztást, majd kattintson a "Megbízhatóságok" fülre, és válassza az erdő B tartomány lehetőséget a kimenő Megbízhatóságok területen. Ha nem konfigurálta a megbízhatóságot a két erdő között, először a megbízhatóságot kell beállítania
    4.  Kattintson a "tulajdonságok..." elemre. Ezután a "név utótagjának útválasztása"
    5.  Ellenőrizze, hogy megjelenik-e a "*. file.core.windows.net" utótag. Ha nem, kattintson a frissítés gombra.
    6.  Válassza a "*. file.core.windows.net" lehetőséget, majd kattintson az "engedélyezés" és az "alkalmaz" gombra.

* <a id=""></a>
**Mely régiók érhetők el Azure Files AD DS hitelesítéshez?**

    További részletekért tekintse meg [AD DS regionális elérhetőségét](storage-files-identity-auth-active-directory-enable.md#regional-availability) .
    
* <a id="ad-aad-smb-afs"></a>
**Kihasználhatom Azure Files Active Directory (AD) hitelesítést a Azure File Sync által felügyelt fájlmegosztás esetében?**

    Igen, engedélyezheti az AD-hitelesítést az Azure file Sync által kezelt fájlmegosztás esetén. A könyvtár/fájl NTFS ACL-ek helyi fájlkiszolgálón való módosítása a Azure Files és fordítva történik.

* <a id="ad-aad-smb-files"></a>
**Van valamilyen különbség a saját Storage-fiókomat képviselő számítógépfiók vagy szolgáltatásbeli bejelentkezési fiók létrehozásakor?**

    Egy [számítógépfiók](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (alapértelmezett) vagy egy [szolgáltatás-bejelentkezési fiók](/windows/win32/ad/about-service-logon-accounts) létrehozása nem különbözik attól, hogy a hitelesítés hogyan működjön együtt Azure Filesokkal. Saját maga dönthet úgy, hogy a Storage-fiókot identitásként jelöli meg az AD-környezetben. A Join-AzStorageAccountForAuth parancsmagban beállított alapértelmezett DomainAccountType számítógépfiók. Azonban az AD-környezetben konfigurált jelszó lejárati kora eltérő lehet a számítógép vagy szolgáltatás bejelentkezési fiókja számára, és figyelembe kell vennie, hogy a [Storage-fiók identitásának frissítése az ad-ban](./storage-files-identity-ad-ds-update-password.md)című részében kell megfontolnia a jelszót.
 
* <a id="ad-support-rest-apis"></a>
**Vannak olyan REST API-k, amelyek támogatják a lekérési/beállítási/másolási és a fájlok Windows ACL-jeit?**

    Igen, támogatjuk a REST API-kat, amelyek a könyvtárakra vagy fájlokra vonatkozó NTFS ACL-ek beszerzését, beállítását vagy másolását használják az [2019-07-07](/rest/api/storageservices/versioning-for-the-azure-storage-services#version-2019-07-07) (vagy újabb) REST API használatakor. A Windows ACL-ek továbbra is támogatottak a REST-alapú eszközökön: [AzCopy v 10.4 +](https://github.com/Azure/azure-storage-azcopy/releases).

* <a id="ad-support-rest-apis"></a>
**A gyorsítótárazott hitelesítő adatok eltávolítása a Storage-fiók kulcsával, valamint a meglévő SMB-kapcsolatok törlése az Azure AD-vel vagy az AD-beli hitelesítő adatokkal való új kapcsolat inicializálása előtt**

    A következő két lépésből álló folyamattal eltávolíthatja a Storage-fiók kulcsához társított mentett hitelesítő adatokat, és eltávolíthatja az SMB-kapcsolatokat: 
    1. A hitelesítő adatok eltávolításához futtassa az alábbi parancsmagot a Windows Cmd.exeban. Ha nem találja az egyiket, az azt jelenti, hogy nem tartotta meg a hitelesítő adatokat, és ki tudja hagyni ezt a lépést.
    
       cmdkey/DELETE: tartomány: Target = Storage-Account-name.file.core.windows.net
    
    2. Törölje a fájlmegosztás meglévő kapcsolódását. A csatlakoztatási útvonalat a csatlakoztatott meghajtóbetűjel vagy a storage-account-name.file.core.windows.net elérési útjaként is megadhatja.
    
       net use <meghajtóbetűjel/megosztás – elérési út>/delete

## <a name="network-file-system"></a>Hálózati fájlrendszer

* <a id="when-to-use-nfs"></a>
**Mikor érdemes használni a Azure Files NFS-t?**

    Lásd: [NFS-megosztások (előzetes verzió)](storage-files-compare-protocols.md#nfs-shares-preview).

* <a id="backup-nfs-data"></a>
**Hogyan NFS-megosztásokban tárolt biztonsági mentési adatforgalom?**

    Az NFS-megosztásokon tárolt adatok biztonsági mentését elvégezheti az ismerős eszközök, például az rsync vagy a harmadik féltől származó biztonsági mentési partnereink termékeinek használatával. Több biztonsági mentési partner, például a [CommVault](https://documentation.commvault.com/commvault/v11/article?p=92634.htm), a [Veeam](https://www.veeam.com/blog/?p=123438)és a [Veritas](https://players.brightcove.net/4396107486001/default_default/index.html?videoId=6189967101001) része a kezdeti előzetes verziónak, és kibővítettük a megoldásaikat a Azure Files SMB 3,0 és NFS 4,1 használatával.

* <a id="migrate-nfs-data"></a>
**Áttelepíthetem a meglévőket NFS-megosztásba?**

    Egy régión belül olyan szabványos eszközöket használhat, mint az scp, az rsync vagy a SSHFS az adatátvitelhez. Mivel az Azure Files NFS több számítási példányból is elérhető egyszerre, a párhuzamos feltöltések segítségével javíthatja a sebességek másolását. Ha egy adott régión kívülről szeretne adatokat használni, VPN vagy Expressroute használatával csatlakoztassa a fájlrendszert a helyszíni adatközpontból.

## <a name="on-premises-access"></a>Helyszíni hozzáférés

* <a id="port-445-blocked"></a>
**Az INTERNETSZOLGÁLTATÓm vagy blokkolja a 445-es portot, amely nem Azure Files csatlakoztatást. Mit tegyek?**

    Az [445-es blokkolt port megkerülő megoldásának különböző módjairól itt](./storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked)olvashat. Azure Files csak a régión vagy adatközponton kívülről engedélyezi a kapcsolódást az SMB 3,0 (titkosítási támogatással) használatával. Az SMB 3,0 protokoll számos olyan biztonsági funkciót vezetett be, mint például a csatornák titkosítása, ami nagyon biztonságos az interneten keresztül. Lehetséges azonban, hogy a 445-es portot letiltották az alacsonyabb SMB-verziókban található sebezhetőségek korábbi okai miatt. Ideális esetben a portot csak az SMB 1,0-forgalomhoz kell letiltani, és az SMB 1,0-et minden ügyfélen ki kell kapcsolni.

* <a id="expressroute-not-required"></a>
**Az Azure ExpressRoute-t kell használnia a Azure Fileshoz való kapcsolódáshoz vagy a helyszíni Azure File Sync használatához?**  

    Nem. Az Azure-fájlmegosztás eléréséhez nem szükséges a ExpressRoute. Ha közvetlenül a helyszínen csatlakoztat egy Azure-fájlmegosztást, az összes szükséges az internet-hozzáféréshez nyitva lévő 445-as (TCP kimenő) portra (ez az a port, amelyet az SMB a kommunikációhoz használ). Ha Azure File Sync használ, az összes szükséges az 443-as port (TCP kimenő) HTTPS-hozzáféréshez (nincs szükség SMB-re). A ExpressRoute azonban a következő hozzáférési lehetőségek egyikével *is* használhatja.

* <a id="mount-locally"></a>
**Hogyan lehet csatlakoztatni egy Azure-fájlmegosztást a helyi gépen?**  

    A fájlmegosztást az SMB protokoll használatával csatlakoztathatja, ha a 445-as (TCP kimenő) port meg van nyitva, és az ügyfél támogatja az SMB 3,0 protokollt (például ha Windows 10 vagy Windows Server 2016 rendszert használ). Ha a szervezet házirendje vagy az INTERNETSZOLGÁLTATÓja blokkolja a 445-as portot, a Azure File Sync használatával férhet hozzá az Azure-fájlmegosztás eléréséhez.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Hogyan az Azure-fájlmegosztás biztonsági mentését?**  
    A véletlen törlések elleni védelemhez használhat rendszeres [megosztási pillanatképeket](storage-snapshots-files.md) . Használhatja a AzCopy, a Robocopy vagy egy külső gyártótól származó biztonsági mentési eszközt is, amely képes biztonsági másolatot készíteni egy csatlakoztatott fájlmegosztásról. A Azure Backup Azure Files biztonsági mentését kínálja. További információ az [Azure-fájlmegosztás Azure Backup általi biztonsági mentéséről](../../backup/backup-afs.md).

## <a name="share-snapshots"></a>Pillanatképek megosztása

### <a name="share-snapshots-general"></a>Pillanatképek megosztása: általános
* <a id="what-are-snaphots"></a>
**Mik azok a fájlmegosztási Pillanatképek?**  
    Az Azure-fájlmegosztás pillanatképeit használhatja a fájlmegosztás csak olvasható verziójának létrehozásához. A Azure Files használatával a tartalom egy korábbi verzióját visszamásolhatja ugyanarra a megosztásra, az Azure egy másik helyére, vagy a helyszínen további módosításokat is végezhet. A pillanatképek megosztásával kapcsolatos további tudnivalókért tekintse meg a [megosztás pillanatképének áttekintése](storage-snapshots-files.md)című témakört.

* <a id="where-are-snapshots-stored"></a>
**Hol tárolódnak a megosztási Pillanatképek?**  
    A megosztási Pillanatképek tárolása ugyanabban a Storage-fiókban történik, mint a fájlmegosztás.

* <a id="snapshot-consistency"></a>
**A megosztási Pillanatképek alkalmazása konzisztens?**  
    Nem, a megosztási Pillanatképek nem az alkalmazások konzisztensek. A megosztási pillanatkép megkezdése előtt a felhasználónak ki kell ürítenie az alkalmazásból a megosztásba írt írásokat.

* <a id="snapshot-limits"></a>
**Korlátozzák a használható megosztási Pillanatképek számát?**  
    Igen. A Azure Files legfeljebb 200 megosztási pillanatképet tud megőrizni. A megosztási Pillanatképek nem számítanak bele a megosztási kvótába, így a megosztási Pillanatképek által használt teljes területhez nem tartozik megosztási korlát. A Storage-fiókra vonatkozó korlátozások továbbra is érvényesek. A 200-es megosztási pillanatképek létrehozása után törölni kell a régebbi pillanatképeket új megosztási Pillanatképek létrehozásához.

* <a id="snapshot-cost"></a>
**Mennyibe kerül a pillanatképek megosztása?**  
    A standard szintű tranzakció és a standard szintű tárolási díj a pillanatképre lesz érvényes. A pillanatképek növekményes jellegűek. Az alap pillanatkép maga a megosztás. Az összes további pillanatkép növekményes, és csak az előző pillanatképből származó különbözetet fogja tárolni. Ez azt jelenti, hogy a számlán látható különbözeti változások minimálisak lesznek, ha a számítási feladatok elváltozása minimális. A standard Azure Files díjszabási információit a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/storage/files/) tekintheti meg. Napjainkban a megosztási pillanatkép által felhasznált méretet a felhasznált kapacitással összehasonlítva tekintheti meg. Dolgozunk az eszközökön a jelentéskészítés javítására.

* <a id="ntfs-acls-snaphsots"></a>
**A mappákban és fájlokban tárolt NTFS ACL-ek a megosztási pillanatképekben is megmaradtak?**  
    A könyvtárak és fájlok NTFS ACL-listái megmaradnak a megosztási pillanatképekben.

### <a name="create-share-snapshots"></a>Megosztási pillanatképek létrehozása
* <a id="file-snaphsots"></a>
**Létrehozhatok megosztási pillanatképet az egyes fájlokról?**  
    A megosztási Pillanatképek a fájlmegosztás szintjén jönnek létre. Az egyes fájlokat visszaállíthatja a fájlmegosztás pillanatképből, de a fájl szintű megosztási Pillanatképek nem hozhatók létre. Ha azonban egy megosztási szintű megosztási pillanatképet használ, és szeretné listázni a megosztási pillanatképeket, amelyekben egy adott fájl módosult, akkor ezt a Windows-csatlakoztatott megosztás **előző verzióiban** teheti meg. 
    
    Ha fájl-pillanatkép-szolgáltatásra van szüksége, tudassa velünk [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encrypted-snapshots"></a>
**Létrehozhatok egy titkosított fájlmegosztás megosztási pillanatképeit?**  
    Olyan Azure-fájlmegosztás megosztási pillanatképét is elvégezheti, amelyeken engedélyezve van a titkosítás. A megosztási Pillanatképek fájljait egy titkosított fájlmegosztás számára állíthatja vissza. Ha a megosztás titkosított, a megosztási pillanatkép is titkosítva lesz.

* <a id="geo-redundant-snaphsots"></a>
**A saját megosztási Pillanatképek földrajzilag redundánsak?**  
    A megosztási Pillanatképek ugyanazzal a redundanciával rendelkeznek, mint az általuk készített Azure-fájlmegosztás. Ha a fiókhoz a Geo-redundáns tárolást választotta, a megosztási pillanatképet a párosított régióban is redundánsan tárolja a rendszer.

### <a name="manage-share-snapshots"></a>Megosztási Pillanatképek kezelése
* <a id="browse-snapshots-linux"></a>
**Tallózhatok a Linuxon a megosztási Pillanatképek?**  
    Használhatja az Azure CLI-t a megosztási Pillanatképek létrehozásához, listázásához, tallózásához és visszaállításához Linuxon.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Másolhatom a megosztási pillanatképeket egy másik Storage-fiókba?**  
    Fájlokat másolhat a megosztási pillanatképekről egy másik helyre, a megosztási Pillanatképek azonban nem másolhatók.

### <a name="restore-data-from-share-snapshots"></a>Adatok visszaállítása a megosztási pillanatképekről
* <a id="promote-share-snapshot"></a>
**Elő lehet mozdítani a megosztási pillanatképet az alapszintű megosztásra?**  
    A megosztási Pillanatképek adatait bármely más célhelyre másolhatja. Megosztási pillanatkép nem előléptethető az alapmegosztásra.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Visszaállíthatók a megosztási pillanatképből származó adatok egy másik Storage-fiókba?**  
    Igen. A megosztási pillanatképből származó fájlok átmásolhatók az eredeti helyre vagy egy másik helyre, amely tartalmazza ugyanazt a Storage-fiókot vagy más Storage-fiókot, akár ugyanabban a régióban, akár különböző régiókban. A fájlokat egy helyszíni helyre vagy más felhőbe is másolhatja.    
  
### <a name="clean-up-share-snapshots"></a>Megosztási Pillanatképek tisztítása
* <a id="delete-share-keep-snapshots"></a>
**Törölhetem a megosztást, de nem törölhetem a megosztási pillanatképeket?**  
    Ha a megosztáson aktív megosztási Pillanatképek vannak, akkor nem törölheti a megosztást. A megosztási Pillanatképek törléséhez használhat API-t is. A megosztási pillanatképeket és a megosztást is törölheti a Azure Portalban.

* <a id="delete-share-with-snapshots"></a>
**Mi történik a megosztási pillanatképekkel, ha törölem a Storage-fiókomat?**  
    Ha törli a Storage-fiókját, a rendszer törli a megosztási pillanatképeket is.

## <a name="billing-and-pricing"></a>Számlázás és díjszabás
* <a id="vm-file-share-network-traffic"></a>
**Az Azure-beli virtuális gépek és az Azure-fájlmegosztás közötti hálózati forgalom az előfizetésre felszámított külső sávszélességnek számít?**  
    Ha a fájlmegosztás és a virtuális gép ugyanabban az Azure-régióban található, akkor a fájlmegosztás és a virtuális gép közötti forgalomért nem számítunk fel további díjat. Ha a fájlmegosztás és a virtuális gép különböző régiókban található, a köztük lévő forgalom külső sávszélességként lesz felszámítva.

* <a id="share-snapshot-price"></a>
**Mennyibe kerül a pillanatképek megosztása?**  
     Az előzetes verzió ideje alatt a megosztási Pillanatképek kapacitása díjmentes. A standard szintű tárolás és a tranzakciós költségek is érvényesek. Az általános elérhetőség után a rendszer előfizetéseket számít fel a megosztási Pillanatképek kapacitása és tranzakciói után.
     
     A megosztási Pillanatképek növekményes jellegűek. Az alap megosztási pillanatkép maga a megosztás. Az összes későbbi megosztási pillanatkép növekményes, és csak az előző megosztási pillanatképből származó különbséget tárolja. Csak a módosított tartalomért számítunk fel díjat. Ha a megosztás 100-es adatokkal van ellátva, de csak 5 GiB módosult az utolsó megosztási pillanatkép óta, a megosztási pillanatkép csak 5 további GiB-t használ fel, és a 105 GiB díját számítjuk fel. A tranzakciós és standard kimenő forgalommal kapcsolatos további információkért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Méretezés és teljesítmény
* <a id="files-scale-limits"></a>
**Mik a Azure Files méretezési korlátai?**  
    A Azure Files méretezhetőségével és teljesítményével kapcsolatos információkért lásd: [Azure Files skálázhatósági és teljesítményi célok](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**Milyen méretek érhetők el az Azure-fájlmegosztás esetében?**  
    Az Azure fájlmegosztás mérete (prémium és standard) akár 100 TiB-ra is méretezhető. A standard szinthez tartozó nagyobb fájlmegosztás előkészítéséhez tekintse meg a bevezetési útmutató a további [fájlmegosztás (standard szint)](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) című szakaszát.

* <a id="lfs-performance-impact"></a>
**Kibővíti a fájlmegosztás kvótáját a munkaterhelések vagy a Azure File Sync?**
    
    Nem. A kvóta kibővítése nem befolyásolja a számítási feladatokat vagy Azure File Sync.

* <a id="open-handles-quota"></a>
**Hány ügyfél fér hozzá egyszerre ugyanahhoz a fájlhoz?**   
    A 2 000-as számú nyitott leíró egyetlen fájlon van. Ha a 2 000-es megnyitott kezelővel rendelkezik, hibaüzenet jelenik meg, amely szerint a kvóta elérte a kvótát.

* <a id="zip-slow-performance"></a>
**A teljesítmény lassú, amikor kicsomagolom a fájlokat a Azure Files. Mit tegyek?**  
    Ha nagy számú fájlt szeretne átvinni Azure Filesre, javasoljuk, hogy használja a AzCopy (Windows rendszeren, a Linux és UNIX rendszerhez készült előzetes verzióban) vagy Azure PowerShell. Ezek az eszközök hálózati átvitelre vannak optimalizálva.

* <a id="slow-perf-windows-81-2012r2"></a>
**Miért lassú a teljesítményem az Azure-fájlmegosztás Windows Server 2012 R2 vagy Windows 8,1 rendszeren való csatlakoztatása után?**  
    Ismert hiba történt egy Azure-fájlmegosztás Windows Server 2012 R2 és Windows 8,1 rendszeren való csatlakoztatásakor. A problémát a Windows 8,1 és a Windows Server 2012 R2 összesített frissítésében 2014 javítottuk. Az optimális teljesítmény érdekében gondoskodjon arról, hogy a Windows Server 2012 R2 és a Windows 8,1 összes példánya alkalmazza ezt a javítást. (A Windows-javítások mindig Windows Updateon keresztül érkeznek.) További információ: a Microsoft tudásbázishoz tartozó cikk [lassú teljesítményű, ha a Windows 8,1 vagy a Server 2012 R2 rendszerhez fér hozzá Azure Files](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Funkciók és együttműködés más szolgáltatásokkal
* <a id="cluster-witness"></a>
**Használhatom az Azure-fájlmegosztást *tanúsító fájlmegosztásként* a Windows Server feladatátvevő fürthöz?**  
    Az Azure-fájlmegosztás jelenleg nem támogatja ezt a konfigurációt. További információ az Azure Blob Storage-hoz való beállításáról: [Felhőbeli tanúsító üzembe helyezése feladatátvevő fürtön](/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**Csatlakoztatható Azure-fájlmegosztás egy Azure Container-példányon?**  
    Igen, az Azure-fájlmegosztás jó lehetőség, ha a Container-példányok élettartama után szeretné megőrizni az adatokat. További információ: Azure- [fájlmegosztás csatlakoztatása az Azure Container instances szolgáltatással](../../container-instances/container-instances-volume-azure-files.md).

* <a id="rest-rename"></a>
**Van átnevezési művelet a REST APIban?**  
    Jelenleg nem.

* <a id="nested-shares"></a>
**Beállíthat beágyazott megosztásokat? Más szóval egy megosztás alatt található megosztás?**  
    Nem. *A fájlmegosztás az a virtuális* illesztőprogram, amelyet csatlakoztathat, így a beágyazott megosztások nem támogatottak.

* <a id="ibm-mq"></a>
**Hogyan Azure Files az IBM MQ-val?**  
    Az IBM kiadott egy olyan dokumentumot, amely segítséget nyújt az IBM MQ-ügyfeleknek az IBM szolgáltatással való Azure Files konfigurálásában. További információ: az [IBM MQ többpéldányos üzenetsor-kezelő beállítása Microsoft Azure Files szolgáltatással](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Lásd még
* [A Windows Azure Files hibáinak megoldása](storage-troubleshoot-windows-file-connection-problems.md)
* [A Linux Azure Files hibáinak megoldása](storage-troubleshoot-linux-file-connection-problems.md)
* [Azure File Sync – hibaelhárítás](storage-sync-files-troubleshoot.md)
