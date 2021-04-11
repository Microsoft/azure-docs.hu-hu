---
title: Azure Files és Azure NetApp Files összehasonlítás | Microsoft Docs
description: Azure Files és Azure NetApp Files összehasonlítása.
author: jeffpatt24
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 3/19/2021
ms.author: jeffpatt
ms.openlocfilehash: 652d362a11e80a488c9278dfeff38e715acee784
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641932"
---
# <a name="azure-files-and-azure-netapp-files-comparison"></a>Azure Files és Azure NetApp Files összehasonlítása

Ez a cikk a Azure Files és Azure NetApp Files összehasonlítását ismerteti. 

A Cloud file Storage-t igénylő legtöbb számítási feladat Azure Files vagy Azure NetApp Files esetén is jól működik. A számítási feladatok legmegfelelőbb méretének meghatározásához tekintse át a jelen cikkben ismertetett információkat. További információ: [Azure Files](https://docs.microsoft.com/azure/storage/files/) és [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/) dokumentáció.

## <a name="features"></a>Funkciók

| Kategória | Azure Files | Azure NetApp Files |
|---------|-------------------------|---------|
| Leírás | A [Azure Files](https://azure.microsoft.com/services/storage/files/) egy teljes körűen felügyelt, magasan elérhető szolgáltatás, amely a helyi adatfrissítésekkel rendelkező, véletlenszerű hozzáférésű számítási feladatokhoz van optimalizálva.<br><br> Azure Files ugyanarra az Azure Storage platformra épül, mint az Azure-Blobok, mint más szolgáltatások. | A [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) egy teljes körűen felügyelt, magas rendelkezésre állású, nagyvállalati szintű NAS szolgáltatás, amely képes kezelni a speciális adatkezelési képességeket igénylő legigényesebb, nagy teljesítményű, kis késleltetésű számítási feladatokat. Lehetővé teszi a munkaterhelések áttelepítését, amelyek "nem áttelepíthető" állapotnak minősülnek.<br><br>  A ANF a NetApp operációs rendszer nélküli számítógépére épül, amely az Azure-adatközpontban futó ONTAP Storage operációs rendszert használja egységes Azure-élményhez és helyszíni, például teljesítményhez. |
| Protokollok | Prémium<br><ul><li>SMB 2,1, 3,0</li><li>NFS 4,1 (előzetes verzió)</li><li>REST</li></ul><br>Standard<br><ul><li>SMB 2,1, 3,0</li><li>REST</li></ul><br> További információ: [elérhető fájlmegosztási protokollok](https://docs.microsoft.com/azure/storage/files/storage-files-compare-protocols). | Minden szintje<br><ul><li>SMB 1, 2. x, 3. x</li><li>NFS 3,0, 4,1</li><li>Kettős protokollos hozzáférés (NFSv3/SMB)</li></ul><br> További információ: [NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)-, [SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb)-vagy [kettős protokollú](https://docs.microsoft.com/azure/azure-netapp-files/create-volumes-dual-protocol) kötetek létrehozása. |
| Régió elérhetősége | Prémium<br><ul><li>30 + régió</li></ul><br>Standard<br><ul><li>Minden régió</li></ul><br> További információért tekintse meg a [régiónként elérhető termékeket](https://azure.microsoft.com/global-infrastructure/services/?products=storage). | Minden szintje<br><ul><li>25 + régió</li></ul><br> További információért tekintse meg a [régiónként elérhető termékeket](https://azure.microsoft.com/global-infrastructure/services/?products=storage). |
| Redundancia | Prémium<br><ul><li>LRS</li><li>ZRS</li></ul><br>Standard<br><ul><li>LRS</li><li>ZRS</li><li>GRS</li><li>GZRS</li></ul><br> További információk: [redundancia](https://docs.microsoft.com/azure/storage/files/storage-files-planning#redundancy). | Minden szintje<br><ul><li>Beépített helyi HA</li><li>[Régiók közötti replikáció](https://docs.microsoft.com/azure/azure-netapp-files/cross-region-replication-introduction)</li></ul> |
| Service-Level szerződés (SLA)<br><br> Vegye figyelembe, hogy a Azure Files és a Azure NetApp Files SLA-kat eltérő módon számítjuk ki. | [SLA a Azure Files](https://azure.microsoft.com/support/legal/sla/storage/) | [SLA a Azure NetApp Files](https://azure.microsoft.com/support/legal/sla/netapp) |  
| Hitelesítés és engedélyezés Identity-Based | SMB<br><ul><li>Active Directory tartományi szolgáltatások (AD DS)</li><li>Azure Active Directory Domain Services (Azure AD DS)</li></ul><br> Vegye figyelembe, hogy az azonosítás-alapú hitelesítés csak SMB protokoll használata esetén támogatott. További információ: [Gyakori kérdések](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control). | SMB<br><ul><li>Active Directory tartományi szolgáltatások (AD DS)</li><li>Azure Active Directory Domain Services (Azure AD DS)</li></ul><br> NFS/SMB kettős protokoll<ul><li>Hozzáadás/LDAP-integráció</li></ul><br>NFSv3/NFSv 4.1<ul><li>Hozzáadás/LDAP-integráció (Coming)</li><li>NFS bővített csoportok (Coming)</li></ul><br> További információ: [Gyakori kérdések](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs). |
| Titkosítás | SMB<br><ul><li>Titkosítás inaktív állapotban (AES 256) ügyfelekkel vagy Microsoft által felügyelt kulcsokkal</li><li>Kerberos-titkosítás AES 256 vagy RC4-HMAC használatával</li><li>Titkosítás az átvitel során</li></ul><br>NFS<br><ul><li>Titkosítás inaktív állapotban (AES 256) ügyfelekkel vagy Microsoft által felügyelt kulcsokkal</li></ul><br>REST<br><ul><li>Titkosítás inaktív állapotban (AES 256) ügyfelekkel vagy Microsoft által felügyelt kulcsokkal</li><li>Titkosítás az átvitel során</li></ul><br> További információ: [Biztonság](https://docs.microsoft.com/azure/storage/files/storage-files-compare-protocols#security). | Minden protokoll<br><ul><li>Titkosítás inaktív állapotban (AES 256) Microsoft által felügyelt kulcsokkal </li></ul><br>NFS 4,1<ul><li>Titkosítás az átvitelben a Kerberos használatával AES 256</li></ul><br> További információ: [biztonsági GYIK](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#security-faqs). |
| Hozzáférési beállítások | <ul><li>Internet</li><li>Biztonságos VNet-hozzáférés</li><li>VPN Gateway</li><li>ExpressRoute</li><li>Azure File Sync</li></ul><br> További információt a [hálózati megfontolások](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview)című témakörben talál. | <ul><li>Biztonságos VNet-hozzáférés</li><li>VPN Gateway</li><li>ExpressRoute</li><li>[Globális fájl-gyorsítótár](https://cloud.netapp.com/global-file-cache/azure)</li><li>[HPC Cache](https://docs.microsoft.com/azure/hpc-cache/hpc-cache-overview)</li></ul><br> További információt a [hálózati megfontolások](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)című témakörben talál. |
| Adatvédelem  | <ul><li>Növekményes Pillanatképek</li><li>Fájl-vagy címtár-felhasználó saját visszaállítása</li><li>Visszaállítás új helyre</li><li>Helyi REVERT</li><li>Megosztási szintű Soft delete</li><li>Azure Backup integráció</li></ul><br> További információ: [Azure Files javítja az adatvédelmi képességeket](https://azure.microsoft.com/blog/azure-files-enhances-data-protection-capabilities/). | <ul><li>Pillanatképek (255/kötet)</li><li>Fájl-vagy címtár-felhasználó saját visszaállítása</li><li>Visszaállítás új kötetre</li><li>Helyi REVERT</li><li>[Régiók közötti replikáció](https://docs.microsoft.com/azure/azure-netapp-files/cross-region-replication-introduction) (nyilvános előzetes verzió)</li></ul><br> További információ: [How Azure NetApp Files Snapshots Work](https://docs.microsoft.com/azure/azure-netapp-files/snapshots-introduction). |
| Áttelepítési eszközök  | <ul><li>Azure Data Box</li><li>Azure File Sync</li><li>Storage áttelepítési szolgáltatás</li><li>AzCopy</li><li>Robocopy</li></ul><br> További információ: [Migrálás az Azure file shares](https://docs.microsoft.com/azure/storage/files/storage-files-migration-overview)szolgáltatásba. | <ul><li>[Globális fájl-gyorsítótár](https://cloud.netapp.com/global-file-cache/azure)</li><li>[CloudSync](https://cloud.netapp.com/cloud-sync-service), [XCP](https://xcp.netapp.com/)</li><li>Storage áttelepítési szolgáltatás</li><li>AzCopy</li><li>Robocopy</li><li>Alkalmazás-alapú (például HSR, adatvédelem, AOAG)</li></ul> |
| Szolgáltatási szintek | <ul><li>Prémium</li><li>Tranzakció optimalizálva</li><li>Gyakori</li><li>Ritka elérésű</li></ul><br> További információ: [tárolási rétegek](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers). | <ul><li>Ultra</li><li>Prémium</li><li>Standard</li></ul><br> Az összes szint ALMS minimális késést biztosít.<br><br> További információt a [szolgáltatási szintek](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) és a [teljesítménnyel kapcsolatos szempontok](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-performance-considerations)című témakörben talál. |
| Díjszabás | [Díjszabás Azure Files](https://azure.microsoft.com/pricing/details/storage/files/) | [Díjszabás Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/) |

## <a name="scalability-and-performance"></a>Méretezhetőség és teljesítmény

| Kategória | Azure Files | Azure NetApp Files |
|---------|---------|---------|
| Minimális megosztás/kötet mérete | Prémium<br><ul><li>100 GiB</li></ul><br>Standard<br><ul><li>1 GiB</li></ul> | Minden szintje<br><ul><li>100 GiB (minimális kapacitású készlet mérete: 4 TiB)</li></ul> |
| Maximális megosztás/kötet mérete | Prémium<br><ul><li>100 TiB</li></ul><br>Standard<br><ul><li>100 TiB</li></ul> | Minden szintje<br><ul><li>100 TiB (500 – TiB kapacitási készlet korlátja)</li></ul><br>Akár 12,5 PiB egy Azure NetApp-fiókkal. |
| Maximális megosztás/kötet IOPS | Prémium<br><ul><li>Akár 100k-ig</li></ul><br>Standard<br><ul><li>Legfeljebb 10k</li></ul> | Ultra és prémium<br><ul><li>Akár 450k </li></ul><br>Standard<br><ul><li>Akár 320k</li></ul> |
| Maximális megosztás/kötet átviteli sebessége | Prémium<br><ul><li>Legfeljebb 10 GiB/s</li></ul><br>Standard<br><ul><li>Akár 300 MiB/s</li></ul> | Ultra és prémium<br><ul><li>Akár 4,5 GiB/s</li></ul><br>Standard<br><ul><li>Akár 3,2 GiB/s</li></ul> |
| Maximális fájlméret | Prémium<br><ul><li>4 TiB</li></ul><br>Standard<br><ul><li>1 TiB</li></ul> | Minden szintje<br><ul><li>16 TiB</li></ul> |
| IOPS maximális száma | Prémium<br><ul><li>Akár 8 000</li></ul><br>Standard<br><ul><li>1,000</li></ul> | Minden szintje<br><ul><li>Legfeljebb mennyiségi korlát</li></ul> |
| Maximális átviteli sebesség fájlonként | Prémium<br><ul><li>300 MiB/s (legfeljebb 1 GiB/s többcsatornás SMB-vel)</li></ul><br>Standard<br><ul><li>60 MiB/s</li></ul> | Minden szintje<br><ul><li>Legfeljebb mennyiségi korlát</li></ul> |
| Többcsatornás SMB | Igen ([előzetes](https://docs.microsoft.com/azure/storage/files/storage-files-smb-multichannel-performance)verzió) | Yes |
| Késés | Egy ezredmásodperces minimális késés (2ms – 3 MS kis IO esetében) | Ezredmásodperc minimális késése (<1ms a véletlenszerű IO esetében)<br><br>További információ: teljesítmény- [referenciaértékek](https://docs.microsoft.com/azure/azure-netapp-files/performance-benchmarks-linux). |

A méretezhetőséggel és a teljesítményre vonatkozó célokkal kapcsolatos további információkért lásd: [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-files-scale-targets) és [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits).

## <a name="next-steps"></a>Következő lépések
* [Az Azure Files dokumentációja](https://docs.microsoft.com/azure/storage/files/)
* [Azure NetApp Files dokumentáció](https://docs.microsoft.com/azure/azure-netapp-files/)
