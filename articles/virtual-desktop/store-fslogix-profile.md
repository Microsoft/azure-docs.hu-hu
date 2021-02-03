---
title: Storage FSLogix-profil tároló Windows virtuális asztal – Azure
description: A Windows rendszerű virtuális asztali FSLogix-profil Azure Storage-beli tárolásának lehetőségei.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2ec166c1df9727052d4980f5d5758ece8c499880
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526602"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Tárolási beállítások a Windows rendszerű virtuális asztali FSLogix-profilok tárolói számára

Az Azure több tárolási megoldást is kínál, amelyek segítségével tárolhatja a FSLogix-profil tárolóját. Ez a cikk összehasonlítja azokat a tárolási megoldásokat, amelyeket az Azure kínál a Windows rendszerű virtuális asztali FSLogix felhasználói profilok tárolói számára. Javasoljuk, hogy a legtöbb ügyfelünk számára Azure Files a FSLogix-profilok tárolóit.

A Windows rendszerű virtuális asztal az ajánlott felhasználói profil-megoldásként kínál FSLogix-profilokat. A FSLogix a távoli számítástechnikai környezetekben, például a Windows Virtual Desktopban található profilok hordozására szolgál. A bejelentkezéskor a tároló dinamikusan csatlakozik a számítástechnikai környezethez egy natív módon támogatott virtuális merevlemez (VHD) és egy Hyper-V virtuális merevlemez (VHDX) használatával. A felhasználói profil azonnal elérhető, és pontosan ugyanúgy jelenik meg a rendszeren, mint a natív felhasználói profilok.

A következő táblázatok összehasonlítják a Storage Solutions Azure Storage-ajánlatokat a Windows rendszerű virtuális asztali FSLogix-profilok tárolójának felhasználói profiljaihoz.

## <a name="azure-platform-details"></a>Azure-platform – részletek

|Szolgáltatások|Azure Files|Azure NetApp Files|Tárolóhelyek – Közvetlen|
|--------|-----------|------------------|---------------------|
|Használati eset|Általános célú|Ultra teljesítmény vagy Migrálás a helyszíni NetApp-ből|Platformfüggetlen|
|Platform szolgáltatás|Igen, Azure-natív megoldás|Igen, Azure-natív megoldás|Nem, önállóan felügyelt|
|Régiónkénti rendelkezésre állás|Minden régió|[Régiók kiválasztása](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|Minden régió|
|Redundancia|Helyileg redundáns/Zone-redundáns/geo-redundáns/geo-Zone-redundáns|Helyileg redundáns|Helyileg redundáns/Zone-redundáns/geo-redundáns|
|Rétegek és teljesítmény| Standard (tranzakció optimalizált)<br>Prémium<br>Akár 100 000 000 IOPS 10 GB/s-onként, körülbelül 3 MS késéssel|Standard<br>Prémium<br>Ultra<br>Akár 320k (16K) IOPS 4,5 GB/s-onként, körülbelül 1 MS késéssel|Standard HDD: legfeljebb 500 IOPS korlát<br>Standard SSD: legfeljebb 4k IOPS korlát<br>Prémium SSD: legfeljebb 20000 IOPS korlát<br>A prémium szintű lemezeket ajánlott Közvetlen tárolóhelyek|
|Kapacitás|100 TiB/megosztás, legfeljebb 5 PiB általános célú fiókkal |100 TiB/kötet, akár 12,5 PiB/előfizetés|Maximális 32 TiB/lemez|
|Szükséges infrastruktúra|Minimális megosztás mérete 1 GiB|Minimális kapacitási készlet 4 TiB, min. kötet mérete 100 GiB|Két virtuális gép az Azure IaaS (+ Felhőbeli tanúsító) vagy legalább három virtuális gép nélkül, valamint a lemezek költségei|
|Protokollok|SMB 3.0/2.1, NFSv 4.1 (előzetes verzió), REST|NFSv3, NFSv 4.1 (előzetes verzió), SMB 3. x/2. x|NFSv3, NFSv 4.1, SMB 3,1|

## <a name="azure-management-details"></a>Azure-felügyelet részletei

|Szolgáltatások|Azure Files|Azure NetApp Files|Tárolóhelyek – Közvetlen|
|--------|-----------|------------------|---------------------|
|Access|Felhő, helyszíni és hibrid (Azure file Sync)|Felhő, helyszíni (ExpressRoute-n keresztül)|Felhő, helyszíni|
|Backup|Azure Backup pillanatkép-integráció|Pillanatképek Azure NetApp Files|Azure Backup pillanatkép-integráció|
|Biztonság és megfelelőség|[Az összes Azure által támogatott tanúsítvány](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO befejezve|[Az összes Azure által támogatott tanúsítvány](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Azure Active Directory-integráció|[Natív Active Directory és Azure Active Directory Domain Services](../storage/files/storage-files-active-directory-overview.md)|[Azure Active Directory Domain Services és natív Active Directory](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|Natív Active Directory vagy Azure Active Directory Domain Services csak támogatás|

Ha kiválasztotta a tárolási módszert, tekintse meg a [Windows rendszerű virtuális asztali díjszabást](https://azure.microsoft.com/pricing/details/virtual-desktop/) a díjszabási tervekkel kapcsolatos információkért.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a FSLogix, a felhasználói profil lemezekről és az egyéb felhasználói Profilos technológiákról, tekintse meg a [FSLogix-profilok és az Azure Files](fslogix-containers-azure-files.md)tábla táblázatát.

Ha készen áll a saját FSLogix-profil tárolóinak létrehozására, ismerkedjen meg az alábbi oktatóanyagok egyikével:

- [FSLogix-profilok tárolóinak első lépései a Windows rendszerű virtuális asztali Azure Files](create-file-share.md)
- [FSLogix-profil tároló létrehozása az Azure NetApp-fájlokat használó gazdagép-készletekhez](create-fslogix-profile-container.md)
- A [két csomópontos közvetlen tárolóhelyek kibővített fájlkiszolgáló üzembe helyezése az Azure-ban UPD-tárolóban](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) című témakör útmutatása akkor is érvényes, ha a FSLogix-profil tárolóját használja a felhasználói profil lemeze helyett.

A kezdettől kezdve a Windows rendszerű [virtuális asztali bérlő létrehozása](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)lehetőséggel is elindíthatja a saját Windowsos virtuális asztali megoldását.
