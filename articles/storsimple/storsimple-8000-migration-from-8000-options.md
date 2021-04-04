---
title: Adatáttelepítési lehetőségek a StorSimple 8000 Series-eszközökről
description: Áttekintést nyújt az adatok StorSimple 8000 sorozatból való átadásának lehetőségeiről.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 03/25/2020
ms.author: alkohli
ms.openlocfilehash: 4839f8211e678f5fc2fb3572c7eaa545fbee6c6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94961192"
---
# <a name="options-to-migrate-data-from-storsimple-8000-series"></a>Beállítások az adatok áttelepíthetők a StorSimple 8000 sorozatból

> [!IMPORTANT]
> 2022. december 31-én a StorSimple 8000-sorozat eléri a támogatási (EOS) állapot végét. Javasoljuk, hogy a StorSimple 8000 Series-ügyfelek a dokumentumban ismertetett alternatívák egyikére váltsanak át.

A StorSimple 8000-es sorozat [támogatásának vége a 2022 decemberében megszűnik](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) . A StorSimple 8000 sorozatú ügyfeleknek lehetősége van arra, hogy más Azure-beli hibrid szolgáltatásokra frissítsen. Ez a cikk az adatáttelepítés során elérhető Azure Hybrid-beállításokat ismerteti.

## <a name="migration-options"></a>Migrálási lehetőségek

Az StorSimple 8000 sorozatú ügyfelek az Azure-ban vagy a külső felektől származó lehetőségeket is igénybe vehetik.

### <a name="azure-options"></a>Azure-lehetőségek

#### <a name="migrate-to-azure-file-sync"></a>Migrálás az Azure File Syncre

Ez a vadonatúj áttelepítési lehetőség lehetővé teszi, hogy az ügyfelek a szervezet fájlmegosztást a Azure Files tárolják. Ezeket a fájlokat a rendszer az Azure File Sync (AFS) használatával központosítja a helyszíni hozzáféréshez. Az AFS telepíthető Windows Server-gazdagépre. A tényleges adatáttelepítés ezután a gazdagép másolata vagy az áttelepítési eszköz használatával történik.

Az adatok Azure File Syncba való áttelepítésével kapcsolatos további információkért nyissa meg a [StorSimple 8100 és a 8600 áttelepítését Azure file Syncre](../storage/files/storage-files-migration-storsimple-8000.md).

### <a name="third-party-options"></a>Harmadik féltől származó beállítások

#### <a name="migrate-to-panzura-freedom-nas"></a>Migrálás a Panzura Freedom NAS-ra

A StorSimple 5000-7000 Series és a StorSimple 8000 Series ügyfelei áttelepíthetik a Panzura Freedom NAS-ra, hogy megőrizze az Azure-ban tárolt adatait. A Panzura Freedom megoldás olyan NAS-megoldást biztosít, amely az adatközpontok, az irodák, a nyilvános és a privát felhők számára is elérhetővé vált. A megoldás lehetővé teszi a helyi, hibrid és Felhőbeli munkafolyamatokat az NFS-, SMB-és mobil ügyfelek számára.

Ezt az áttelepítést a Panzura és az ügyfelek is támogatják, ha a [Panzura webhely](https://panzura.com/migrate-storsimple-panzura/)áttelepítési támogatását kéri.

#### <a name="migrate-to-nasuni"></a>Migrálás a Nasuni

A teljes StorSimple-környezet egy stabil, biztonságos, nagy teljesítményű Fájlszolgáltatások-platformra való áthelyezésével egyszerűen Nasuni. A Nasuni az Azure méretezhetőségével és tartósságával ötvözi a helyszíni fájlok tárterületének biztonságát és teljesítményét.  A vezető Azure független szoftvergyártók (ISV-Nasuni) révén a StorSimple-adatokat egy modern platformra helyezheti át, amely lehetővé teszi a fájlok megosztását és együttműködését több helyen.

Ismerkedjen meg még ma: [Nasuni-webhely](https://info.nasuni.com/storsimple8000-webinar).

<!-- 04/09/2020 v-grpr (priestlg) - As per request, commenting out this section because the information that will go into this section is forthcoming
#### Migrate to Cohesity

Cohesity enables you to migrate data from your current StorSimple 5000–7000 to the Cohesity Data Platform on Azure. The Cohesity Data Platform is a software-defined web-scale solution that consolidates files, backups, objects, and VMs onto a single cloud-native solution. After migration to the Data Platform, you can manage, protect, and provision data and apps from cloud to core through a single pane of glass. With Cohesity, start with as few as three nodes. 

Learn more on [migration to the Cohesity Data Platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### Migrate to Nasuni

Nasuni makes it easy for StorSimple 5000-7000 customers to migrate and keep their data in Azure.  Nasuni is a leading Azure-based NAS storage solution, giving customers the performance and security they expect from on-prem solutions, with cloud economics and scale.  In addition to high performance file storage, Nasuni and Azure handle backup and DR, while allowing you to share and collaborate on your data around the globe with centralized file storage management. 

Nasuni has the experience to make your migration easy – get started today: https://info.nasuni.com/nasuni-storsimple-migration

#### Migrate to Talon FAST

Talon makes it easy for StorSimple 5000-7000 customers to continue to leverage the benefits they valued so much in the StorSimple platform (small on-site footprint backed by unlimited cloud resources) with even greater function.  With the Talon FAST solution, customers can migrate and keep their data in Azure, while now having an even smaller software-only onsite footprint and adding benefits such as global file locking, global namespace, and multi-site collaboration.  Talon is a leading Azure ecosystem solution, working with global customers to migrate their on-premises file server workloads into a consolidated, Azure-based footprint without compromising user workflow or experience.  

Learn more about how to evolve to a cloud-consolidated enterprise at https://www.talonstorage.com/alliances/microsoft-storsimple.
-->

## <a name="migration---frequently-asked-questions"></a>Migrálás – gyakori kérdések

### <a name="q-when-do-the-storsimple-8000-series-devices-reach-end-of-service"></a>K. Mikor éri el a StorSimple 8000 Series-eszközök a szolgáltatás végét?

A. A StorSimple 8000-es sorozat a 2022 decemberében ér [véget](https://support.microsoft.com/[lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) . A támogatás vége azt jelenti, hogy a Microsoft már nem tud támogatást nyújtani ezeknek az eszközöknek a hardver-és szoftverekhez a 2022. december után. Nyomatékosan javasoljuk, hogy kezdjen el egy tervet készíteni az adatok az eszközökről való átimportálásához.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>K. Mi történik az Azure-ban tárolt adattal?  

A. Ha egy újabb szolgáltatásba telepíti át, továbbra is használhatja az Azure-ban tárolt adatátvitelt.

### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>K. Mi történik az StorSimple-eszközön helyileg tárolt adataival?

A. A helyi eszközön lévő információk átmásolhatók az újabb szolgáltatásba az áttelepítési dokumentumokban leírtak szerint.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-8000-series-appliance"></a>K. Mi történik, ha szeretném megőrizni a StorSimple 8000 Series készüléket?

A. Amíg a szolgáltatások továbbra is működhetnek, a Microsoft többé nem fog tudni hardveres és szoftveres támogatást biztosítani. Az üzletmenet folytonossága érdekében erősen ajánlott az áttelepítés.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-8000-series-devices"></a>K. Milyen lehetőségek állnak rendelkezésre az adatok áttelepíthetők a StorSimple 8000 Series-eszközökről?

A. A forgatókönyvtől függően a StorSimple 8000 Series felhasználói a következő áttelepítési lehetőségek közül választhatnak:

* **Áttelepítés Azure file Syncre**: ezt a beállítást akkor használja, ha az Azure natív formátumára szeretne váltani. A fájlmegosztás központi felügyeletéhez Azure File Sync is használhatja.

* **Egyéb lehetőségek**: a Microsoft ügyfélszolgálata az itt felsorolt áttelepítési lehetőségek megvitatásához fordulhat.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>K. Támogatott-e az áttelepítés más tárolási megoldásokra?

A. Igen. A rendszer a más tárolási megoldásokra való áttelepítést is támogatja az adatfogadó példány használatával.

### <a name="q-is-migration-supported-by-microsoft"></a>K. Támogatja a Microsoft a Migrálás szolgáltatást?

A. A 8000-es sorozatból való Migrálás teljes mértékben támogatott művelet. A Microsoft a Migrálás megkezdése előtt javasolja a támogatás elérését. Az áttelepítés jelenleg egy támogatott művelet. Ha az StorSimple 8000 Series-eszközről kívánja áttelepíteni az adatait, [forduljon a StorSimple támogatási szolgálatához](mailto:storsimp@microsoft.com).

### <a name="q-what-is-the-pricing-model-for-migration-to-azure-file-sync"></a>K. Mi az a díjszabási modell a Azure File Syncre való áttelepítéshez?

A. Azure File Sync használata esetén előfordulhat, hogy a szolgáltatáshoz tartozó előfizetési díjak érvényesek. Az ügyfeleknek a folyamatos tárolási költségeket is meg kell fizetniük. A becslést az [AFS-díjszabásban]( https://azure.microsoft.com/pricing/details/storage/files/) találja.

### <a name="q-how-long-does-it-take-to-complete-a-migration"></a>K. Mennyi ideig tart az áttelepítés befejezése?

A. Az adatok áttelepítésének ideje az adatok mennyiségétől és a kiválasztott verziófrissítési lehetőségtől függ.

## <a name="next-steps"></a>Következő lépések

* [Adatok migrálása StorSimple 8000-sorozatból Azure File Sync](../storage/files/storage-files-migration-storsimple-8000.md)