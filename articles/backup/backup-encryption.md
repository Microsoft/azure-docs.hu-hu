---
title: Titkosítás az Azure Backupban
description: Ismerje meg, hogy a Azure Backup titkosítási funkciói hogyan védik a biztonsági mentési adatait, és megfelelnek a vállalat biztonsági igényeinek.
ms.topic: conceptual
ms.date: 08/04/2020
ms.custom: references_regions
ms.openlocfilehash: c9b1b2782a34285ae194f2998a7cd053cf3c0c70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96325678"
---
# <a name="encryption-in-azure-backup"></a>Titkosítás az Azure Backupban

Az Azure Storage encryption használatával a rendszer automatikusan titkosítja a biztonsági másolatok adatait a felhőben, ami segít a biztonsági és megfelelőségi kötelezettségek teljesítésében. Az inaktív adatok titkosítása 256 bites AES-titkosítással történik, amely az egyik legerősebb blokk titkosítási algoritmus, és az FIPS 140-2-kompatibilis. Amellett, hogy a titkosítás a nyugalmi állapotban van, a rendszer az összes átviteli biztonsági mentési adatát HTTPS-kapcsolaton keresztül továbbítja. Mindig az Azure gerinc hálózatán marad.

## <a name="levels-of-encryption-in-azure-backup"></a>Titkosítási szintek Azure Backup

A Azure Backup két szinten tartalmaz titkosítást:

- **Az Recovery Services-tárolóban lévő adattitkosítás**
  - A **platform által felügyelt kulcsok használata**: alapértelmezés szerint az összes adatait a platform által felügyelt kulcsok használatával titkosítja a rendszer. A titkosítás engedélyezéséhez nem szükséges explicit műveletet végrehajtani a végponttól. Ez a Recovery Services-tárolóba mentett összes munkaterhelésre vonatkozik.
  - Az **ügyfél által felügyelt kulcsok használata**: az Azure-Virtual Machines biztonsági mentése esetén úgy is dönthet, hogy az Ön által birtokolt és felügyelt titkosítási kulcsok használatával titkosítja az adatait. Azure Backup lehetővé teszi, hogy a Azure Key Vault tárolt RSA-kulcsait a biztonsági mentések titkosításához használja. A biztonsági mentések titkosításához használt titkosítási kulcs különbözhet a forráshoz használttól. Az adatait egy AES 256-alapú adattitkosítási kulccsal (ADATTITKOSÍTÁSI kulcsot) védik, amely viszont a kulcsok használatával védett. Ez teljes körű irányítást biztosít az adat és a kulcsok felett. A titkosítás engedélyezéséhez meg kell adnia a Recovery Services tár hozzáférését a Azure Key Vault titkosítási kulcsához. Ha szükséges, letilthatja a kulcsot, vagy visszavonhatja a hozzáférést. A titkosítást azonban a kulcsok használatával kell engedélyeznie, mielőtt bármilyen elemet a tárolóhoz próbál védelemmel ellátni. [További információt itt talál](encryption-at-rest-with-cmk.md).
  - **Infrastruktúra-szintű titkosítás**: az adatoknak a Recovery Services-tárolóban az ügyfél által felügyelt kulcsokkal történő titkosításán kívül további titkosítási réteget is beállíthat a tárolási infrastruktúrán. Ezt az infrastruktúra-titkosítást a platform kezeli. Az ügyfél által felügyelt kulcsokkal együtt a REST titkosítással együtt lehetővé teszi a biztonsági mentési adatok kétrétegű titkosítását. Az infrastruktúra titkosítása csak akkor konfigurálható, ha először a saját kulcsait szeretné használni a titkosításhoz a nyugalmi állapotban. Az infrastruktúra titkosítása platform által felügyelt kulcsokat használ az adattitkosításhoz.
- **A biztonsági mentést végző munkaterhelésre jellemző titkosítás**  
  - **Azure-beli virtuális gépek biztonsági mentése**: Azure Backup támogatja a [platform által felügyelt kulcsokkal](../virtual-machines/disk-encryption.md#platform-managed-keys)titkosított lemezeket, valamint az Ön által birtokolt és felügyelt [ügyfelek által felügyelt kulcsokat](../virtual-machines/disk-encryption.md#customer-managed-keys) . Emellett olyan Azure-beli virtuális gépek biztonsági mentését is elvégezheti, amelyeken a [Azure Disk Encryption](backup-azure-vms-encryption.md#encryption-support-using-ade)használatával titkosított operációs rendszer vagy adatlemez található. Az ADE a BitLockert használja a Windows rendszerű virtuális gépekhez, és DM-Crypt a Linux rendszerű virtuális gépekhez a vendégen belüli titkosítás végrehajtásához.

>[!NOTE]
>Az infrastruktúra-titkosítás jelenleg korlátozott előzetes verzióban érhető el, és az USA keleti, USA-beli West2, az USA déli középső régiójában, US Gov Arizona és USA Ha bármelyik régióban szeretné használni a szolgáltatást, töltse ki [ezt az űrlapot](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUN0VHNEpJS0ZUWklUNVdJSTEzR0hIOVRMVC4u) , és küldjön e-mailt a következő címre: [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="next-steps"></a>Következő lépések

- [Inaktív adatok Azure Storage-titkosítása](../storage/common/storage-service-encryption.md)
- A titkosítással kapcsolatos bármilyen kérdés [Azure Backup – gyakori](backup-azure-backup-faq.md#encryption) kérdések