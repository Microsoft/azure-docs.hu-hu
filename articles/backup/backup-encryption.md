---
title: Titkosítás az Azure Backupban
description: Megtudhatja, hogy a Azure Backup titkosítási funkciói hogyan segítenek megvédeni a biztonsági mentési adatokat, és megfelelnek vállalata biztonsági igényeinek.
ms.topic: conceptual
ms.date: 08/04/2020
ms.custom: references_regions
ms.openlocfilehash: 28d165ccc8a966091a96fc433660899d8eef1595
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518473"
---
# <a name="encryption-in-azure-backup"></a>Titkosítás az Azure Backupban

A biztonságimentett adatok automatikusan titkosítva vannak, ha a felhőben vannak tárolva az Azure Storage-titkosítás használatával, ami segít a biztonsági és megfelelőségi követelmények teljesítésében. Ezek az adatok 256 bites AES-titkosítással vannak titkosítva, amely az egyik legerősebb elérhető blokktitkosítás, és FIPS 140-2-kompatibilis. Az összes átvitt biztonsági mentési adat a titkosításon kívül HTTPS-kapcsolaton keresztül is át lesz adva. Mindig az Azure gerinchálózatán marad.

## <a name="levels-of-encryption-in-azure-backup"></a>Titkosítási szintek a Azure Backup

Azure Backup két szinten tartalmazza a titkosítást:

- **Adatok titkosítása a Recovery Services-tárolóban**
  - **Platform által felügyelt kulcsok használata:** Alapértelmezés szerint minden adat titkosítása platform által kezelt kulcsokkal történik. Ennek a titkosításnak az engedélyezéséhez nem kell explicit műveletet tennie. Minden olyan számítási feladatra vonatkozik, amelyről biztonságimentés van a Helyreállítási tárban.
  - **Felhasználó által kezelt kulcsok** használata: Az Azure Virtual Machines biztonsági Virtual Machines az adatokat az Ön tulajdonában lévő és kezelt titkosítási kulcsokkal titkosíthatja. Azure Backup a biztonsági másolatok titkosításához használhatja a Azure Key Vault rsa-kulcsait. A biztonsági másolatok titkosításához használt titkosítási kulcs különbözhet a forráshoz használttól. Az adatok védelme egy AES 256-alapú adattitkosítási kulccsal (DEK), amely a kulcsokkal védett. Így teljes körűen szabályozhatja az adatokat és a kulcsokat. A titkosítás engedélyezése érdekében hozzáférést kell adni a Recovery Services-tárolónak a titkosítási kulcshoz a Azure Key Vault. Letilthatja a kulcsot, vagy szükség esetén visszavonhatja a hozzáférést. A kulcsokat használó titkosítást azonban engedélyeznie kell, mielőtt megkíséreli megvédeni a tároló elemeit. [További információt itt talál](encryption-at-rest-with-cmk.md).
  - **Infrastruktúraszintű titkosítás:** A Recovery Services-tárolóban tárolt adatok ügyfél által kezelt kulcsokkal való titkosítása mellett egy további titkosítási réteget is konfigurálhat a tárolási infrastruktúrán. Ezt az infrastruktúra-titkosítást a platform kezeli. Az ügyfél által kezelt kulcsokkal kezelt, az adatok biztonsági másolatának kétrétegű titkosítását teszi lehetővé az adatok titkosításával együtt. Az infrastruktúra-titkosítás csak akkor konfigurálható, ha először a saját kulcsait használja az adattitkosításhoz. Az infrastruktúra-titkosítás platform által felügyelt kulcsokat használ az adatok titkosításához.
- **A biztonságimentett számítási feladatra jellemző titkosítás**  
  - **Azure-beli** virtuális gépek biztonsági mentése: Azure Backup támogatja a platform [](../virtual-machines/disk-encryption.md#platform-managed-keys)által felügyelt kulcsokkal [](../virtual-machines/disk-encryption.md#customer-managed-keys) titkosított lemezeket használó virtuális gépek biztonsági mentését, valamint az Ön tulajdonában lévő és felügyelt, felhasználó által kezelt kulcsokat. Emellett az operációs rendszer vagy az adatlemezeik titkosításával az Azure-beli virtuális gépekről is biztonsági [Azure Disk Encryption.](backup-azure-vms-encryption.md#encryption-support-using-ade) Az ADE a BitLockert használja a Windows rendszerű virtuális gépekhez, DM-Crypt linuxos virtuális gépekhez pedig DM-Crypt a vendégen történő titkosításhoz.

>[!NOTE]
>Az infrastruktúra-titkosítás jelenleg korlátozott előzetes verzióban érhető el, és csak az USA keleti régiójában, az USA 2. nyugati régiójában, az USA déli középső régiójában, a US Gov Arizona-ban és az US GOV Virginia régióban érhető el. Ha a funkciót ezen régiók bármelyikében használni [](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUN0VHNEpJS0ZUWklUNVdJSTEzR0hIOVRMVC4u) szeretné, töltse ki ezt az űrlapot, és küldjön nekünk e-mailt a [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) címre.

## <a name="next-steps"></a>Következő lépések

- [Inaktív adatok Azure Storage-titkosítása](../storage/common/storage-service-encryption.md)
- [Azure Backup titkosítással](/backup-azure-backup-faq.yml#encryption) kapcsolatos esetleges kérdéseire vonatkozó gyakori kérdések