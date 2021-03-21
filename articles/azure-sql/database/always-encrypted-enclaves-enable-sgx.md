---
title: Az Intel SGX ENKLÁVÉHOZ engedélyezése a Azure SQL Database számára
description: Megtudhatja, hogyan engedélyezheti az Intel SGX ENKLÁVÉHOZ-t a biztonságos enklávékkal való Always Encryptedhez Azure SQL Database egy SGX ENKLÁVÉHOZ-kompatibilis hardveres generáció kiválasztásával.
keywords: adatok titkosítása, SQL-titkosítás, adatbázis-titkosítás, bizalmas adatok, Always Encrypted, biztonságos enklávék, SGX ENKLÁVÉHOZ, igazolás
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: ded1406c47bb3f00c366da7a5b28319f3712f8a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98733756"
---
# <a name="enable-intel-sgx-for-your-azure-sql-database"></a>Az Intel SGX ENKLÁVÉHOZ engedélyezése a Azure SQL Database számára 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted a biztonságos enklávékkal a Azure SQL Database jelenleg **nyilvános előzetes** verzióban érhető el.

A [Always encrypted a Azure SQL Database Secure enklávés szolgáltatásával](/sql/relational-databases/security/encryption/always-encrypted-enclaves) [Intel Software Guard Extensions (Intel SGX enklávéhoz)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) enklávékat használ. Ahhoz, hogy az Intel SGX ENKLÁVÉHOZ elérhető legyen, az adatbázisnak a [virtuális mag modellt](service-tiers-vcore.md) és a [DC sorozatú](service-tiers-vcore.md#dc-series) hardveres generációt kell használnia.

A DC sorozatú hardverek létrehozásának beállítása az Intel SGX ENKLÁVÉHOZ enklávék engedélyezéséhez az Azure SQL Database rendszergazdája feladata. Tekintse meg a [szerepköröket és a felelősségi köröket a SGX enklávéhoz enklávék és igazolások konfigurálásakor](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation).

> [!NOTE]
> Az Intel SGX ENKLÁVÉHOZ nem érhető el a DC sorozaton kívüli hardveres generációkban. Például az Intel SGX ENKLÁVÉHOZ nem érhető el a Gen5-hardverekhez, és nem érhető el a [DTU modellt](service-tiers-dtu.md)használó adatbázisokhoz.

> [!IMPORTANT]
> Mielőtt konfigurálja a DC sorozat hardveres generációját az adatbázishoz, ellenőrizze a DC-sorozat regionális elérhetőségét, és győződjön meg róla, hogy megérti a teljesítményre vonatkozó korlátozásait. További információ: [DC-Series](service-tiers-vcore.md#dc-series).

Ha részletes útmutatást szeretne arról, hogyan konfigurálhat új vagy meglévő adatbázist egy adott hardveres generáció használatára, tekintse meg [a hardveres generáció kiválasztása](service-tiers-vcore.md#selecting-a-hardware-generation)című témakört.
   
## <a name="next-steps"></a>Következő lépések

- [Azure-tanúsítvány konfigurálása az Azure SQL Database-kiszolgálóhoz](always-encrypted-enclaves-configure-attestation.md)

## <a name="see-also"></a>Lásd még

- [Oktatóanyag: a Always Encrypted első lépései a biztonságos enklávékkal Azure SQL Database](always-encrypted-enclaves-getting-started.md)