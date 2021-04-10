---
title: Az Azure arc-kompatibilis PostgreSQL nagy kapacitású korlátai
description: Az Azure arc-kompatibilis PostgreSQL nagy kapacitású korlátai
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: b1a56c8acf1789690c01f1c16b7c37a237720e39
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564874"
---
# <a name="limitations-of-azure-arc-enabled-postgresql-hyperscale"></a>Az Azure arc-kompatibilis PostgreSQL nagy kapacitású korlátai

Ez a cikk az Azure arc-kompatibilis PostgreSQL-nagy kapacitású korlátozásait ismerteti. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

- Az időponthoz való visszaállítás (például a megadott dátumra és időpontra történő visszaállítás) nem támogatott. Egy adott időpontra történő visszaállításkor a visszaállítás előtt egy másik, a visszaállítás előtt üzembe helyezett kiszolgálócsoport-csoportot kell visszaállítani. Az új kiszolgálócsoport visszaállítása után törölheti a származási kiszolgáló csoportját.
- Egy biztonsági másolat teljes tartalmának visszaállítása (az adott időpontra való visszaállítással szemben) a PostgreSQL 12-es verziójában támogatott. Nem támogatott a PostgreSQL 11-es verziójában, mert a PostgreSQL-motor időbeli korláttal rendelkezik. A 11-es verziójú PostgreSQL-kiszolgálócsoport teljes biztonsági másolatának visszaállításához vissza kell állítania azt egy másik kiszolgálócsoport számára.


## <a name="databases"></a>Adatbázisok

Egy kiszolgálócsoport egynél több adatbázisának üzemeltetése nem támogatott.


## <a name="security"></a>Biztonság

A felhasználók és a Szerepkörök kezelése nem támogatott. Egyelőre továbbra is használja a postgres standard felhasználót.

## <a name="roles-and-responsibilities"></a>Szerepkörök és felelősségek

A Microsoft és ügyfelei közötti szerepkörök és felelősségek eltérnek az Azure Pásti-szolgáltatások (platform szolgáltatás) és az Azure Hybrid (például az Azure arc engedélyezve PostgreSQL nagy kapacitású) között. 

### <a name="frequently-asked-questions"></a>Gyakori kérdések

Az alábbi táblázat összefoglalja a támogatási szerepkörökkel és feladatokkal kapcsolatos gyakori kérdésekre adott válaszokat.

| Kérdés                      | Azure platform szolgáltatásként (Péter) | Azure arc Hybrid Services |
|:----------------------------------|:------------------------------------:|:---------------------------:|
| Ki biztosítja az infrastruktúrát?  | Microsoft                          | Ügyfél                  |
| Ki biztosítja a szoftvert? *       | Microsoft                          | Microsoft                 |
| Kik végzik el a műveleteket? | Microsoft                          | Ügyfél                  |
| Biztosít a Microsoft SLA-kat?      | Igen                                | Nem                        |
| Ki a SLA-k díja? | Microsoft                          | Ügyfél                  |

\* Azure-szolgáltatások

__A Microsoft miért nem biztosít SLA-kat az Azure arc Hybrid Services szolgáltatásban?__ Mivel a Microsoft nem rendelkezik az infrastruktúrával, és nem működik. Ügyfeleinknek.

## <a name="next-steps"></a>Következő lépések

- **Próbálja ki.** Gyorsan elsajátíthatja az [Azure arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) az Azure Kubernetes Service (ak), az AWS rugalmas Kubernetes szolgáltatás (EKS), a Google Cloud Kubernetes Engine (GKE) vagy egy Azure-beli virtuális gépen. 

- **Hozza létre a sajátját.** Kövesse az alábbi lépéseket a saját Kubernetes-fürtön való létrehozásához: 
   1. [Az ügyféleszközök telepítése](install-client-tools.md)
   2. [Az Azure arc-adatkezelő létrehozása](create-data-controller.md)
   3. [Azure Database for PostgreSQL nagy kapacitású-kiszolgálócsoport létrehozása az Azure arc-ban](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [További információ az Azure arc-kompatibilis adatszolgáltatásokról](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Az Azure arc áttekintése](https://aka.ms/azurearc)
