---
title: Azure Security teljesítményteszt v2 – biztonsági mentés és helyreállítás
description: Azure Security teljesítményteszt v2 – biztonsági mentés és helyreállítás
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fd9a01868230efd9e9078171359d81302b472cd3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724421"
---
# <a name="security-control-v2-backup-and-recovery"></a>Security Control v2: biztonsági mentés és helyreállítás

A biztonsági mentés és helyreállítás kiterjed a vezérlőkre, így biztosítva, hogy az adatokat és a konfigurációkat a különböző szolgáltatási rétegek végzik, ellenőrzik és védik.

A megfelelő beépített Azure Policy megjelenítéséhez tekintse meg [Az Azure biztonsági teljesítményteszt szabályozási megfelelőségének beépített kezdeményezését ismertető témakört: biztonsági mentés és helyreállítás](../../governance/policy/samples/azure-security-benchmark#backup-and-recovery)

## <a name="br-1-ensure-regular-automated-backups"></a>BR-1: rendszeres automatikus biztonsági mentések biztosítása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| BR-1 | 10.1 | CP-2, CP4, CP-6, CP-9 |

Győződjön meg arról, hogy biztonsági mentést készít a rendszerekről és az adatbázisokról, hogy egy váratlan esemény után fenntartsa az üzletmenet folytonosságát. Ezt a helyreállítási pontok célkitűzésének (RPO) és a helyreállítási idő célkitűzésének (RTO) bármely célkitűzése határozza meg.

Engedélyezze Azure Backup és konfigurálja a biztonsági mentési forrást (például Azure-beli virtuális gépeket, SQL Server, HANA-adatbázisokat vagy fájlmegosztást), valamint a kívánt gyakoriságot és megőrzési időt.

A magasabb szintű védelem érdekében engedélyezheti a Geo-redundáns tárolási lehetőséget, hogy a biztonsági mentési adatokat egy másodlagos régióba replikálja, és a helyreállítást a régiók közötti visszaállítás használatával végezze el.

- [Nagyvállalati szintű üzletmenet-folytonosság és vészhelyreállítás](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

- [A Azure Backup engedélyezése](../../backup/index.yml)

- [Hogyan engedélyezhető a régiók közötti visszaállítás?](../../backup/backup-azure-arm-restore-vms.md#cross-region-restore)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Szabályzat és szabványok](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [Biztonsági architektúra](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Incidens előkészítése](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-2-encrypt-backup-data"></a>BR-2: biztonsági mentési adataik titkosítása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| BR – 2 | 10,2 | CP-9 |

Győződjön meg arról, hogy a biztonsági másolatok védve vannak a támadásokkal szemben. Ennek tartalmaznia kell a biztonsági másolatok titkosítását a titkosság elvesztése elleni védelem érdekében.

Az Azure Backup-t használó helyszíni biztonsági másolatok esetén a rendszer az Ön által megadott hozzáférési kód használatával biztosítja a titkosítást. Az Azure-szolgáltatások rendszeres biztonsági mentése esetén a biztonsági mentési adatokat az Azure platform által felügyelt kulcsok használatával automatikusan titkosítja a rendszer. Megadhatja, hogy a biztonsági mentéseket az ügyfél által felügyelt kulcs használatával titkosítsa. Ebben az esetben ügyeljen arra, hogy az ügyfél által felügyelt kulcs a Key vaultban is a biztonsági mentési hatókörben legyen.

A biztonsági másolatok és az ügyfelek által felügyelt kulcsok elleni védelem érdekében használja az Azure szerepköralapú hozzáférés-vezérlést Azure Backup, Azure Key Vault vagy más erőforrásokkal. Emellett speciális biztonsági funkciók is engedélyezhetők az MFA megköveteléséhez, mielőtt a biztonsági másolatok módosíthatók vagy törölhetők.

- [A Azure Backup biztonsági funkcióinak áttekintése](../../backup/security-overview.md)

- [Biztonsági mentési adatok titkosítása ügyfelek által felügyelt kulcsok használatával](../../backup/encryption-at-rest-with-cmk.md) 

- [Key Vault kulcsok biztonsági mentése az Azure-ban](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

- [Biztonsági funkciók a hibrid biztonsági mentések elleni védelemhez](../../backup/backup-azure-security-feature.md#prevent-attacks)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági architektúra](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Incidens előkészítése](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Az összes biztonsági másolat és az ügyfelek által kezelt kulcsok ellenőrzése

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| BR – 3 | 10,3 | CP-4, CP-9 |

Rendszeres időközönként végezze el a biztonsági mentés adatvisszaállítását. Győződjön meg arról, hogy visszaállíthatja az ügyfél által felügyelt kulcsok biztonsági mentését.

- [Fájlok helyreállítása az Azure-beli virtuális gépek biztonsági másolatából](../../backup/backup-azure-restore-files-from-vm.md)

- [Key Vault kulcsok visszaállítása az Azure-ban](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Incidens előkészítése](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: A kulcsok elvesztésével járó kockázat csökkentése

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| BR – 4 | 10,4 | CP-9 |

Győződjön meg arról, hogy rendelkezik olyan mértékekkel, amelyek megakadályozzák a kulcsok elvesztését és helyreállítását. Engedélyezze a helyreállítható törlést és a végleges törléssel szembeni védelmet az Azure Key Vaultban, hogy megvédje a kulcsokat a véletlen vagy rosszindulatú törléssel szemben.

- [Helyreállítható törlés és a végleges törléssel szembeni védelem engedélyezése kulcstartóban](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági architektúra](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Incidens előkészítése](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Adatbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-data-security)