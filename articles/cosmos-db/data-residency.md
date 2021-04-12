---
title: Az adattárolási követelmények teljesítése Azure Cosmos DB
description: megtudhatja, hogyan teljesítheti az adatokat és a biztonsági mentéseket a Azure Cosmos DB az adattárolási követelményekkel, hogy egyetlen régióban maradjanak.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: c5f8a4361774368e3934d1e2b16c8311876f5caa
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491865"
---
# <a name="how-to-meet-data-residency-requirements-in-azure-cosmos-db"></a>Az adattárolási követelmények teljesítése Azure Cosmos DB

A Azure Cosmos DBban úgy konfigurálhatja az adatokat és a biztonsági mentéseket, hogy egyetlen régióban maradjanak, hogy megfeleljenek a[ rezidensi követelményeknek.](https://azure.microsoft.com/en-us/global-infrastructure/data-residency/)

## <a name="residency-requirements-for-data"></a>Az adattárolásra vonatkozó követelmények

Azure Cosmos DBban explicit módon konfigurálnia kell a régiók közötti adatreplikációt. Ismerje meg, hogyan konfigurálhatja a Geo-replikálást a [Azure Portal](how-to-manage-database-account.md#addremove-regions-from-your-database-account), az [Azure CLI](scripts/cli/common/regions.md)használatával. Az adattárolási követelmények teljesítéséhez létrehozhat egy Azure-szabályzatot, amely lehetővé teszi bizonyos régiók számára, hogy megakadályozza az adatreplikációt a nemkívánatos régiók számára.

## <a name="residency-requirements-for-backups"></a>A biztonsági másolatok tartózkodási feltételei

**Folyamatos üzemmódú biztonsági másolatok**: ezek a biztonsági másolatok alapértelmezés szerint rezidensek, mivel a helyileg redundáns vagy a zóna redundáns tárolóban vannak tárolva. További információt a [folyamatos biztonsági mentéssel](continuous-backup-restore-portal.md) foglalkozó cikkben talál.

**Rendszeres üzemmódú biztonsági mentések**: alapértelmezés szerint a rendszer az időszakos módú fiók biztonsági mentését a Geo-redundáns tárolóban tárolja. Az időszakos biztonsági mentési módok esetében a fiók szintjén állíthatja be az adatredundanciát. A biztonsági mentési tár három redundancia-beállítással rendelkezik. Helyi redundancia, zóna-redundancia vagy geo-redundancia. További információ: a [Backup redundancia konfigurálása](configure-periodic-backup-restore.md#configure-backup-interval-retention) a portál használatával.

## <a name="use-azure-policy-to-enforce-the-residency-requirements"></a>A tartózkodási feltételek betartatása Azure Policy használatával

Ha olyan adattárolási követelményekkel rendelkezik, amelyeknek egyetlen Azure-régióban kell megőriznie az összes adatát, akkor a fiókja Azure Policy segítségével kényszerítheti ki a fiókjával kapcsolatos redundáns vagy helyileg redundáns biztonsági mentéseket.  Olyan szabályzatot is kikényszerítheti, amelyet a Azure Cosmos DB fiókok nem replikálhatók más régiókba.

A Azure Policy egy olyan szolgáltatás, amellyel olyan szabályzatokat hozhat létre, rendelhet hozzá és kezelhet, amelyek szabályokat alkalmaznak az Azure-erőforrásokra. Azure Policy segít megőrizni ezeket az erőforrásokat a vállalati szabványoknak és a szolgáltatói szerződéseknek. További információ: a [Azure Policy](policy.md) használata az irányítás és a vezérlők Azure Cosmos db erőforrásokhoz való megvalósításához

## <a name="next-steps"></a>Következő lépések

* Rendszeres biztonsági mentés konfigurálása és kezelése [Azure Portal](configure-periodic-backup-restore.md) használatával
* A folyamatos biztonsági mentés konfigurálása és kezelése [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [parancssori](continuous-backup-restore-command-line.md)felület vagy [Azure Resource Manager](continuous-backup-restore-template.md)használatával.
