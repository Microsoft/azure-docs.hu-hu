---
title: Az Azure Files üzembe helyezésének megtervezése |} A Microsoft Docs
description: Ismerje meg az Azure Files üzembe helyezésének tervezése során megfontolandó szempontokat.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 06/12/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 0701049eb1aa86398e90484dbf21ef3781270fba
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2018
ms.locfileid: "48831381"
---
# <a name="planning-for-an-azure-files-deployment"></a>Az Azure Files üzembe helyezésének megtervezése
[Az Azure Files](storage-files-introduction.md) teljes körűen felügyelt fájlmegosztást kínáló, amely az iparági szabvány SMB protokollon keresztül érhető el a felhőben. Mivel az Azure Files teljes körűen felügyelt, üzembe helyezés éles forgatókönyvekben, sokkal egyszerűbb, mint az üzembe helyezése és kezelése a fájlkiszolgáló vagy NAS-eszköz. Ez a cikk foglalkozik a témakörök figyelembe kell venni üzembe helyezése az Azure-fájlmegosztás a szervezeten belül éles környezetben való használatra.

## <a name="management-concepts"></a>Tartománykezelési fogalmaival
 Az alábbi ábra az Azure Files felügyeleti eszközökkel:

![Fájlstruktúra](./media/storage-files-introduction/files-concepts.png)

* **Storage-fiók**: Minden Azure Storage-hozzáférés tárfiókon keresztül valósítható meg. A tárfiókok kapacitásával kapcsolatos további információkért lásd: [Scalability and Performance Targets](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (Méretezhetőségi és teljesítménycélok).

* **Megosztás**: A File Storage-megosztás egy SMB-fájlmegosztás az Azure-ban. Minden könyvtárnak és fájlnak egy szülőmegosztásban kell létrejönnie. Egy fiók korlátlan számú megosztást tartalmazhat, és a egy megosztás korlátlan számú fájl legfeljebb 5 TiB teljes kapacitását a fájlmegosztás tárolhatja.

* **Könyvtár**: Egy választható könyvtár-hierarchia.

* **Fájl**: A megosztásban található fájl. Előfordulhat, hogy a fájl mérete legfeljebb 1 Tib-ra.

* **URL-formátum**: Azure-fájlmegosztások a fájl REST protokoll végrehajtott kérelmek, a fájlok a következő URL-formátummal:

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/directories>/<file>
    ```

## <a name="data-access-method"></a>Adat-hozzáférési módszer
Az Azure Files ajánlatok két, beépített és kényelmes adat-hozzáférési módszereit, hogy önállóan vagy együtt egymással, az adatok eléréséhez:

1. **A közvetlen felhőhozzáférést**: bármely Azure-fájlmegosztás által is csatlakoztathatók [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md), és/vagy [Linux](storage-how-to-use-files-linux.md) olyan az iparági szabványos Server Message Block (SMB) protokoll vagy a fájl REST API-n keresztül. SMB, az olvasási és írási műveletek a megosztáson található fájlok készülnek el közvetlenül a fájlmegosztás az Azure-ban. Csatlakoztatása egy Azure-beli virtuális gép által, az operációs rendszerben az SMB-ügyfél legalább kell támogatja SMB 2.1. A helyszíni, csatlakoztatni, például a felhasználói munkaállomáson, a munkaállomás által támogatott SMB-ügyfél legalább támogatnia SMB 3.0 (a titkosítás). SMB, valamint új alkalmazások vagy szolgáltatások előfordulhat, hogy tud közvetlenül hozzáférni a fájl REST, amely egy egyszerű és méretezhető alkalmazásprogramozási felületet biztosít a szoftverfejlesztést fájlmegosztást.
2. **Az Azure File Sync**: az Azure File Sync, a megosztások replikálhatók a helyszíni Windows-kiszolgálók vagy az Azure-ban. A felhasználók számára szeretné hozzáférnie a fájlmegosztás Windows-kiszolgálón keresztül például az SMB vagy NFS-megosztások. Ez akkor hasznos, amelyben az adatok elérni és módosítani milyen távolságra egy Azure-adatközpontban, mint például a fiókirodák esetében forgatókönyvekhez. Előfordulhat, hogy lehet replikálja az adatokat között több Windows Server-végpontot, például több fiókirodák között. Végül előfordulhat, hogy lehet rétegzett adatok az Azure Files, úgy, hogy az összes adat a kiszolgáló továbbra is elérhetők, de a kiszolgáló nem rendelkezik az adatok teljes másolata. Adatok, zökkenőmentesen idézni, amikor a felhasználó megnyitja.

Az alábbi táblázat mutatja be, hogy a felhasználók és alkalmazások férhet hozzá az Azure-fájlmegosztásba:

| | Közvetlen felhőhozzáférést | Azure File Sync |
|------------------------|------------|-----------------|
| Milyen protokollokat szeretné használni? | Az Azure Files SMB 2.1, az SMB 3.0-s és a fájl REST API támogatja. | Hozzáférés az Azure-fájlmegosztás segítségével bármely támogatott protokoll a Windows Server (SMB, NFS, FTPS, stb.) |  
| Hol futnak az alkalmazások és szolgáltatások? | **Az Azure-ban**: az Azure Files az adatok közvetlen hozzáférést biztosít. | **A helyszínen a lassú hálózati**: Windows, Linux és MacOS rendszerű ügyfelek is egy helyi Windows-fájlmegosztás csatlakoztatása az Azure-fájlmegosztás gyors gyorsítótárként. |
| Milyen szintű hozzáférés-vezérlési listák van szüksége? | Megosztás és a fájl szintje. | Megosztás, a fájl és a felhasználói szintű. |

## <a name="data-security"></a>Adatbiztonság
Az Azure Files számára biztosítja az adatok biztonságát több beépített lehetőséggel rendelkezik:

* Mindkét keresztüli átvitel közbeni protokollt a titkosítás támogatása: SMB 3.0 titkosítás és a fájl REST HTTPS-kapcsolaton keresztül. Alapértelmezés szerint: 
    * Ügyfelek, amelyek támogatják az SMB 3.0 titkosítás adatokat küldeni és fogadni egy titkosított csatornán keresztül.
    * Ügyfelek, amelyek nem támogatják az SMB 3.0-s titkosítással kommunikálhatunk belüli adatközpontot SMB 2.1 vagy SMB 3.0 titkosítás nélkül. SMB-ügyfelek nem engedélyezettek az inter-datacenter SMB 2.1 vagy SMB 3.0 protokollt használó kommunikációra titkosítás nélkül.
    * A HTTP vagy HTTPS fájl REST az ügyfelek kommunikálhatnak.
* Titkosítás inaktív ([Azure Storage Service Encryption](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): minden tárfiókhoz a Storage Service Encryption (SSE) engedélyezve van. Inaktív adatok teljes körűen felügyelt kulcsokkal van titkosítva. Titkosítás inaktív nem növelje a tárolási költségeket, és csökkentheti a teljesítményt. 
* A titkosított adatokat átvitel nem kötelező követelménye: kiválasztásakor az Azure Files elutasítja a hozzáférési adatokat nem titkosított csatornákon keresztül. Pontosabban a csak HTTPS és SMB 3.0 titkosítás kapcsolatok engedélyezettek. 

    > [!Important]  
    > Az adatok biztonságos átvitel megkövetelése eredményezi, hogy a régebbi SMB-ügyfelek nem képes kommunikálni az SMB 3.0, a titkosítás a nem sikerült. További információkért lásd: [Windows csatlakoztassa](storage-how-to-use-files-windows.md), [csatlakoztatás linuxon](storage-how-to-use-files-linux.md), és [macOS rendszeren csatlakoztatási](storage-how-to-use-files-mac.md).

A maximális biztonság érdekében javasoljuk, hogy mindig mindkét titkosítás inaktív engedélyezése és az adatok átvitel titkosításának engedélyezésével, amikor az ügyfelek a modern használ az adatok eléréséhez. Például ha van szüksége a Windows Server 2008 R2 rendszerű, csak az SMB 2.1 támogatja, amelyek a megosztás csatlakoztatásához szüksége a tárfiók nem titkosított forgalom engedélyezésére, mivel az SMB 2.1 nem támogatja a titkosítást.

Ha az Azure File Sync használatával az Azure-fájlmegosztás eléréséhez, mindig használjuk HTTPS és SMB 3.0-s titkosítással szinkronizálja az adatokat a Windows-kiszolgálók, függetlenül attól, inaktív adatok titkosításának megkövetelése.

## <a name="file-share-performance-tiers"></a>A fájl megosztási teljesítményszintek
Az Azure Files két teljesítményszinttel támogatja: standard és prémium szintű.

* **Standard fájlmegosztások** élvezik forgó merevlemez-meghajtók (HDD), amely megbízható teljesítményt, amelyek kevésbé érzékenyek a teljesítményingadozásra, például az általános célú fájlmegosztások és a fejlesztési és tesztelési környezetek i/o-munkaterhelések esetében. Standard fájlmegosztások csak egy használatalapú számlázási modell érhető el.
* **Prémium szintű fájlmegosztások (előzetes verzió)** élvezik tartós állapotú lemezeket (SSD-kkel), amely egységes nagy teljesítményű és kis késésű, belül a legtöbb i/o-műveletek, a legtöbb i/o-igényes munkaterhelések esetében egy számjegyű ideje ezredmásodpercben. Ez teszi őket a megfelelő számos különböző számítási feladatokhoz – például adatbázisokat, webhelyszolgáltatás, fejlesztőkörnyezetet, stb. Prémium szintű fájlmegosztások csak egy üzembe helyezett számlázási modell érhető el.

### <a name="provisioned-shares"></a>Kiépített megosztások
Prémium szintű fájlmegosztások vannak üzembe helyezve egy rögzített GiB/IOPS/teljesítmény arány alapján. Az egyes üzembe helyezett GiB a megosztás fogja kiállítani egy IOPS és 0,1 MiB/s átviteli legfeljebb fájlmegosztás maximális vonatkozó korlátok. Kiépítés engedélyezett minimum 100 GB, a minimális IOPS és átviteli sebesség. Megosztás méretének bármikor növelhető, és bármikor csökkent, de 24 óránként óta az utolsó növelését a csökkenthető.

Az elérhető legjobb lehetőség alapján minden megosztás is megnövelheti arra GiB kiosztott tárolás három IOPS legfeljebb 60 percet vagy hosszabb ideig a megosztás méretétől függően. Új megosztások indítsa el a teljes burst kreditet kap, a kiépített lemezkapacitás alapján.

| Kiosztott kapacitást | 100 GiB | 500 GiB | 1 TiB | 5 TiB | 
|----------------------|---------|---------|-------|-------|
| Alapkonfiguráció IOPS | 100 | 500 | 1,024 | 5,120 | 
| Hirtelen korlát | 300 | 1,500 | 3,072 | 15,360 | 
| Teljesítmény | 110 MiB/mp | 150 MiB/mp | 202-es MiB/mp | 612 MiB/mp |

## <a name="file-share-redundancy"></a>Fájl megosztási redundancia
Az Azure Files három adatredundanciával kapcsolatos lehetőségek támogatja: a helyileg redundáns tárolás (LRS), a zónaredundáns tárolás (ZRS) és a georedundáns tárolás (GRS). A következő szakaszok ismertetik a különböző adatredundanciával kapcsolatos lehetőségek közötti különbségek:

### <a name="locally-redundant-storage"></a>Helyileg redundáns tárolás
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>Zónaredundáns tárolás
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>Georedundáns tárolás
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="data-growth-pattern"></a>Növekedési adatmintát
Még ma az Azure-fájlmegosztás maximális méretének 5 Tib-ra. Ezen jelenlegi korlátozás miatt meg kell fontolnia a várható adatmennyiség növekedését, Azure-fájlmegosztások üzembe helyezésekor. 

Több Azure-fájlmegosztások az Azure File Sync használatával egyetlen Windows fájlkiszolgálóhoz szinkronizálási lehetőség. Ez lehetővé teszi, hogy győződjön meg arról, hogy a régebbi, nagy mennyiségű fájlmegosztások, hogy előfordulhat, hogy a helyszíni az Azure File Sync tehető. További információkért lásd: [Azure File Sync üzembe helyezésének megtervezése](storage-files-planning.md).

## <a name="data-transfer-method"></a>Adatátviteli módszer
Átviteli adatokat egy már létező fájlt oszt meg, például egy helyszíni fájlmegosztás, az Azure Files tömeges számos egyszerű lehetőség van. Néhány népszerű eszközök közé tartozik a (nem teljes lista):

* **Az Azure File Sync**: egy Azure-fájlmegosztások ("Felhőbeli végpont") és a egy Windows-könyvtár névtér ("kiszolgálói végpont") között első szinkronizálási részeként az Azure File Sync replikálja az összes adat a meglévő fájlmegosztást a az Azure Files.
* **[Az Azure Import/Export](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)**: az Azure Import/Export szolgáltatás lehetővé teszi, hogy biztonságosan továbbíthat nagy mennyiségű adatot az Azure-fájlmegosztások merevlemezes meghajtók egy Azure-adatközpontban való szállításával. 
* **[A Robocopy](https://technet.microsoft.com/library/cc733145.aspx)**: Robocopy egy jól ismert másolás eszköz részét képező Windows és Windows Server. A Robocopy adatok átviteléhez az Azure Files helyileg a fájlmegosztás csatlakoztatása és a csatlakoztatott helyet használva a Robocopy parancsot a célként használható.
* **[Az AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#upload-files-to-an-azure-file-share)**: egyszerű parancs használatával az optimális teljesítmény érdekében az AzCopy egy parancssori segédprogram, és az Azure Files, valamint az Azure Blob storage, az adatok másolása szolgál. Az AzCopy Windows és Linux rendszerekhez érhető el.

## <a name="next-steps"></a>További lépések
* [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
* [Az Azure Files üzembe helyezése](storage-files-deployment-guide.md)
* [Az Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md)
