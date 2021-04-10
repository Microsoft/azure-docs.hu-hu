---
title: Az Intel SGX ENKLÁVÉHOZ enklávék és igazolások megtervezése Azure SQL Database
description: Tervezze meg a Always Encrypted telepítését biztonságos enklávékkal Azure SQL Databaseban.
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
ms.openlocfilehash: 4448ce051b0c9e73865e8057cc4f224c9cbeb571
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98732744"
---
# <a name="plan-for-intel-sgx-enclaves-and-attestation-in-azure-sql-database"></a>Az Intel SGX ENKLÁVÉHOZ enklávék és igazolások megtervezése Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted a biztonságos enklávékkal a Azure SQL Database jelenleg **nyilvános előzetes** verzióban érhető el.

[Always encrypted biztonságos enklávékkal](/sql/relational-databases/security/encryption/always-encrypted-enclaves) Azure SQL Database [Intel Software Guard Extensions (Intel SGX enklávéhoz)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) enklávékat használ, és [Microsoft Azure igazolást](/sql/relational-databases/security/encryption/always-encrypted-enclaves#secure-enclave-attestation)igényel.

## <a name="plan-for-intel-sgx-in-azure-sql-database"></a>A Azure SQL Database Intel SGX ENKLÁVÉHOZ megtervezése

Az Intel SGX ENKLÁVÉHOZ egy hardveres, megbízható végrehajtási környezet technológiája. Az Intel SGX ENKLÁVÉHOZ olyan adatbázisokhoz érhető el, amelyek a [virtuális mag modellt](service-tiers-vcore.md) és a [DC sorozatú](service-tiers-vcore.md?#dc-series) hardveres generációt használják. Ezért annak biztosítása érdekében, hogy az adatbázisában a Always Encrypted biztonságos enklávékkal is használható legyen, a-adatbázis létrehozásakor ki kell választania a DC sorozatú hardver generációját, vagy a meglévő adatbázisát frissítheti a DC sorozatú hardveres generáció használatára.

> [!NOTE]
> Az Intel SGX ENKLÁVÉHOZ nem érhető el a DC sorozaton kívüli hardveres generációkban. Például az Intel SGX ENKLÁVÉHOZ nem érhető el a Gen5-hardverekhez, és nem érhető el a [DTU modellt](service-tiers-dtu.md)használó adatbázisokhoz.

> [!IMPORTANT]
> Mielőtt konfigurálja a DC sorozat hardveres generációját az adatbázishoz, ellenőrizze a DC-sorozat regionális elérhetőségét, és győződjön meg róla, hogy megérti a teljesítményre vonatkozó korlátozásait. Részletekért lásd: [DC-Series](service-tiers-vcore.md#dc-series).

## <a name="plan-for-attestation-in-azure-sql-database"></a>A Azure SQL Database igazolásának megtervezése

[Microsoft Azure igazolás](../../attestation/overview.md) (előzetes verzió) a megbízható végrehajtási környezetek (pólók) igazolására szolgáló megoldás, beleértve az Intel SGX enklávéhoz enklávékat az Azure SQL Database-ben a DC sorozatú hardveres generáció használatával.

Ha az Azure igazolást szeretné használni az Intel SGX ENKLÁVÉHOZ enklávék Azure SQL Database-ben való igazolásához, a következőket kell tennie:

1. Hozzon létre egy [igazolási szolgáltatót](../../attestation/basic-concepts.md#attestation-provider) , és konfigurálja azt egy igazolási házirenddel. 

2. Adja meg az Azure SQL logikai kiszolgáló hozzáférését a létrehozott igazolási szolgáltatóhoz.

## <a name="roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation"></a>Szerepkörök és felelősségi körök a SGX ENKLÁVÉHOZ enklávék és tanúsítványok konfigurálásakor

Ha úgy konfigurálja a környezetet, hogy támogassa az Intel SGX ENKLÁVÉHOZ enklávékat és a Azure SQL Database Always Encrypted igazolását, különböző típusú összetevőket kell beállítania: Microsoft Azure igazolások, Azure SQL Database és az enklávé-igazolást kiváltó alkalmazások. Az egyes típusok összetevőinek konfigurálását a felhasználók hajtják végre, feltételezve, hogy az alábbi különálló szerepkörök valamelyikét látják el:

- Igazolási rendszergazda – igazolási szolgáltatót hoz létre Microsoft Azure igazolásban, a szerzők az igazolási házirendet, engedélyezi az Azure SQL logikai kiszolgáló hozzáférését az igazolási szolgáltatóhoz, és megosztja az igazolási URL-címet, amely a szabályzatra mutat az alkalmazás-rendszergazdák számára.
- Azure SQL Database Administrator – lehetővé teszi a SGX ENKLÁVÉHOZ-enklávék adatbázisokban való használatát a DC sorozatú hardveres generáció kiválasztásával, és az igazolási rendszergazdát azon Azure SQL logikai kiszolgáló identitásával biztosítja, amelynek hozzá kell férnie az igazolási szolgáltatóhoz.
- Alkalmazás-rendszergazda – az igazolási rendszergazdától kapott igazolási URL-címmel konfigurálja az alkalmazásokat.

Éles környezetekben (valós bizalmas adatok kezelése) fontos, hogy a szervezet betartsa a szerepkör elkülönítését az igazolás konfigurálásakor, ahol a különböző szerepköröket más személyek feltételezik. Különösen, ha a szervezeten belüli Always Encrypted üzembe helyezésének célja a támadási felület csökkentése azáltal, hogy Azure SQL Database rendszergazdák nem férhetnek hozzá a bizalmas adatokhoz, Azure SQL Database a rendszergazdáknak nem szabad ellenőrizniük az igazolási házirendeket.

## <a name="next-steps"></a>Következő lépések

- [Az Intel SGX ENKLÁVÉHOZ engedélyezése az Azure SQL Database-adatbázishoz](always-encrypted-enclaves-enable-sgx.md)

## <a name="see-also"></a>Lásd még

- [Oktatóanyag: a Always Encrypted első lépései a biztonságos enklávékkal Azure SQL Database](always-encrypted-enclaves-getting-started.md)