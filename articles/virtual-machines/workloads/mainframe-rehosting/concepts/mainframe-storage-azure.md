---
title: Nagyszámítógépes tároló áthelyezése az Azure Storage-ba
description: A nagymértékben méretezhető Azure Storage-erőforrások segítségével a nagyvállalati szintű szervezetek áttelepíthetik és modernizálják az IBM z14-alkalmazásokat.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: 8f57210ff6b65ee3a893fb344a48629466e90004
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97095355"
---
# <a name="move-mainframe-storage-to-azure"></a>Nagyszámítógépes tároló áthelyezése az Azure-ba

A nagyvállalati munkaterhelések Microsoft Azure-on való futtatásához tudnia kell, hogyan hasonlítják össze a mainframe képességeit az Azure-ban. A nagymértékben méretezhető tárolási erőforrások segítik a szervezeteket abban, hogy az általuk felhasznált alkalmazások megszakítása nélkül is modernizálják magukat.

Az Azure olyan nagyvállalati funkciókat és tárolókapacitást biztosít, amelyek hasonlóak az IBM z14-alapú rendszerekhez (ez a jelenlegi modell ebben az írásban). Ebből a cikkből megtudhatja, hogyan érhet el hasonló eredményeket az Azure-ban.

## <a name="mainframe-storage-at-a-glance"></a>A mainframe-tárolók áttekintése

Az IBM mainframe két módon jellemzi a tárterületet. Az első egy közvetlen hozzáférésű tárolóeszköz (DASD). A második a szekvenciális tárterület. A tárolók kezeléséhez a nagyszámítógép biztosítja az adattárolási felügyeleti alrendszer (DFSMS) szolgáltatását. A szolgáltatás a különböző tárolóeszközökhöz való adathozzáférést kezeli.

A [DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device) egy külön eszközre hivatkozik a másodlagos (nem memóriában lévő) tárolóra, amely lehetővé teszi, hogy a rendszer egyedi címeket lehessen használni az adateléréshez. Eredetileg a DASD, a mágneses dob vagy az adatcellákra alkalmazott kifejezés. A kifejezés azonban a Solid-State Storage-eszközökre (SSD), a tárterület-hálózatokra (SANs), a hálózati csatlakoztatott tárolóra (NAS) és az optikai meghajtókra is érvényes lehet. A jelen dokumentum esetében a DASD a fonási lemezekre, a SANs-re és az SSD-re vonatkozik.

A DASD-tárolóval ellentétben a nagyszámítógépeken lévő szekvenciális tárterület olyan eszközökre vonatkozik, mint a szalagos meghajtók, amelyekben az adatok egy kiindulási pontról érhetők el, majd olvashatók vagy írhatók egy sorba.

A tárolóeszközök általában Fiber-kapcsolattal (FICON) vannak csatolva, vagy közvetlenül a nagyvállalati IO-buszon érhetők el a [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm)használatával, egy IBM-technológiával, amely nagy sebességű kommunikációt biztosít a kiszolgálók partíciói között egy Hypervisort használó kiszolgálón.

A legtöbb nagyszámítógép-rendszer két típusba különíti el a tárolót:

- A napi műveletekhez szükség van az *online tárterületre* (más néven a gyors tárolásra is). A DASD-tárolót általában erre a célra használják. Azonban a szekvenciális tárolás, például a napi szalagos biztonsági másolatok (logikai vagy fizikai) is használható erre a célra.

- Az *archiválási tár* (más néven a hűtőházi tároló) nem garantált, hogy egy adott időpontban legyen csatlakoztatva. Ehelyett szükség szerint csatlakoztatva van és elérhető. Az archiválási tár gyakran a tároláshoz szükséges szekvenciális szalagos biztonsági másolatok (logikai vagy fizikai) használatával valósítható meg.

## <a name="mainframe-versus-io-latency-and-iops"></a>Nagyszámítógépek és IO-késések és IOPS

A nagyszámítógépeket gyakran használják olyan alkalmazások esetében, amelyek nagy teljesítményű IO-t és alacsony IO-késést igényelnek. Ezt az IO-eszközök és a HiperSockets FICON-kapcsolatainak használatával teheti meg. Ha a HiperSockets az alkalmazások és az eszközök közvetlenül a mainframe IO-csatornához való csatlakoztatására szolgálnak, a másodpercenkénti késések is elérhetők.

## <a name="azure-storage-at-a-glance"></a>Azure Storage – áttekintés

Az Azure infrastruktúra-szolgáltatás ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)) tárolási lehetőségei hasonló nagyszámítógépi kapacitást biztosítanak.

A Microsoft petabájt kínál az Azure-ban üzemeltetett alkalmazásokhoz, és több tárolási lehetőség is rendelkezésre áll. Ezen tartomány az SSD-tárolóból a nagy teljesítményű, alacsony árú blob Storage-tárolók és-archívumok számára. Az Azure emellett egy adatredundancia-lehetőséget is biztosít a tároláshoz – ami több erőfeszítést tesz a mainframe-környezetekben való beállításra.

Az Azure Storage Azure- [lemezként](../../../managed-disks-overview.md), [Azure Filesként](../../../../storage/files/storage-files-introduction.md)és [Azure-blobként](../../../../storage/blobs/storage-blobs-overview.md) érhető el az alábbi táblázat összefoglalása során. További információ a [használatáról](../../../../storage/common/storage-introduction.md).

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Típus</th><th>Description</th><th>A következő esetekben használja:</th></tr>
</thead>
<tbody>
<tr><td>Azure Files
</td>
<td>
Egy SMB-felületet, egy ügyféloldali kódtárat és egy <a href="/rest/api/storageservices/file-service-rest-api">Rest</a> -felületet biztosít, amely lehetővé teszi a hozzáférést bárhonnan a tárolt fájlokhoz.
</td>
<td><ul>
<li>Egy alkalmazás átemelése a felhőbe, ha az alkalmazás a natív fájlrendszer API-kat használja az Azure-ban futó más alkalmazások közötti adatmegosztáshoz.</li>
<li>A számos virtuális gépről elérhető fejlesztési és hibakeresési eszközöket tárolja.</li>
</ul>
</td>
</tr>
<tr><td>Azure Blobs
</td>
<td>Az ügyféloldali kódtárakat és egy <a href="/rest/api/storageservices/blob-service-rest-api">Rest</a> -felületet biztosít, amely lehetővé teszi a strukturálatlan adatok tárolását és elérését a blokkos Blobok nagy méretekben. A <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a> is támogatja a vállalati Big Data elemzési megoldásokhoz.
</td>
<td><ul>
<li>Támogatja a folyamatos átviteli és véletlenszerű hozzáférési forgatókönyveket az alkalmazásokban.</li>
<li>Bárhonnan hozzáférhet az alkalmazásadatok eléréséhez.</li>
<li>Hozzon létre egy vállalati adattárat az Azure-ban, és hajtson végre big data elemzéseket.</li>
</ul></td>
</tr>
<tr><td>Azure Disks
</td>
<td>Az ügyféloldali kódtárakat és egy <a href="/rest/api/compute/disks">Rest</a> -felületet biztosít, amely lehetővé teszi az adatok tartós tárolását és elérését egy csatlakoztatott virtuális merevlemezről.
</td>
<td><ul>
<li>A natív fájlrendszert használó API-k használatával olyan alkalmazásokat helyezhet át és helyezhet át, amelyek állandó lemezekre olvasnak és írhatnak.</li>
<li>A nem szükséges adatok tárolása azon a virtuális gépen kívülről, amelyhez a lemez csatlakoztatva van.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure-beli meleg (online) és hideg (archív) tároló

Egy adott rendszer tárolási típusa a rendszer követelményeitől függ, beleértve a tárterület méretét, az átviteli sebességet és a IOPS. A nagyszámítógépeken található DASD-alapú tároláshoz az Azure-beli alkalmazások általában az Azure Disks Drive Storage-t használják helyette. A nagyszámítógépek archiválásához a blob Storage használata az Azure-ban történik.

Az SSD-k biztosítják a legnagyobb tárolási teljesítményt az Azure-ban. A következő beállítások érhetők el (a dokumentum írásakor):

| Típus         | Méret           | IOPS                  |
|--------------|----------------|-----------------------|
| Ultra SSD    | 4 GB – 64 TB  | 1 200 – 160 000 IOPS |
| Prémium SSD  | 32 GB – 32 TB | 12 – 15 000 IOPS     |
| Standard SSD | 32 GB – 32 TB | 12 – 2 000 IOPS      |

A blob Storage a legnagyobb tárterületet biztosítja az Azure-ban. A tárterület mérete mellett az Azure felügyelt és nem felügyelt tárolást is biztosít. A felügyelt tárolók esetében az Azure gondoskodik a mögöttes Storage-fiókok kezeléséről. A nem felügyelt tárolással a felhasználó felelősséget vállal a megfelelő méretű Azure Storage-fiókok beállításához a tárolási követelmények kielégítése érdekében.

## <a name="next-steps"></a>Következő lépések

- [Nagyszámítógép migrálása](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Az Azure Virtual Machines-t futtató nagyszámítógépek](../overview.md)
- [Nagyszámítógépek számítási teljesítményének áthelyezése az Azure-ba](mainframe-compute-Azure.md)
- [Az Azure-Blobok, a Azure Files-vagy az Azure-lemezek használatának eldöntése](../../../../storage/common/storage-introduction.md)
- [standard SSD Managed Disks Azure-beli virtuális gépek számítási feladataihoz](../../../disks-types.md#standard-ssd)

### <a name="ibm-resources"></a>IBM-erőforrások

- [Párhuzamos Sysplex az IBM Z-ben](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [Az IBM CICS és a kapcsolási létesítmény: az alapjain túl](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Szükséges felhasználók létrehozása a DB2 pureScale szolgáltatás telepítéséhez](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt – példány létrehozása parancs](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [DB2 pureScale fürtözött adatbázis-megoldás](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM-es adatstúdió](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Microsoft Azure Government felhő nagyszámítógépi alkalmazások számára](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft és FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>További áttelepítési erőforrások

- [Az Azure Virtual adatközpontjának átemelési és átváltási útmutatója](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://glusterdocs.readthedocs.io/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)