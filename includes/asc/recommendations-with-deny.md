---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 03/21/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 5fc36e327a9530105182f0a23b3ef22ab324e01c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803569"
---
- A Storage-fiókokhoz a tűzfal és a virtuális hálózati konfigurációk hozzáférését korlátozni kell
- Az Automation-fiók változóit titkosítani kell
- A Redis tartozó Azure cache-nek egy virtuális hálózaton belül kell lennie
- Azure Cosmos DB fiókoknak az ügyfelek által felügyelt kulcsokat kell használniuk a REST-adatok titkosításához
- Azure Machine Learning-munkaterületeket ügyfél által felügyelt kulccsal kell titkosítani (CMK)
- Az Azure Spring Cloud-nak hálózati befecskendezést kell használnia
- Cognitive Services fiókoknak engedélyeznie kell az adattitkosítást az ügyfél által felügyelt kulccsal (CMK)
- A tároló CPU-és memória-korlátozásait kényszeríteni kell
- A tároló lemezképeit csak megbízható kibocsátásiegység-forgalmi jegyzékből kell telepíteni
- A tároló-beállításjegyzékeket ügyfél által felügyelt kulccsal kell titkosítani (CMK)
- A jogosultság-eszkalációs tárolót el kell kerülni
- A bizalmas gazdagépek névtereit megosztó tárolókat el kell kerülni
- A tárolóknak csak az engedélyezett portok figyelésére kell figyelniük
- Nem módosítható (csak olvasható) rendszerindító fájlrendszert kell kikényszeríteni a tárolók számára
- Key Vault kulcsoknak lejárati dátummal kell rendelkezniük
- Key Vault a titkoknak lejárati dátummal kell rendelkezniük
- A kulcstárolóknak engedélyezve kell lennie a kiürítési védelemmel
- A Key vaultok esetében engedélyezve kell lennie a helyreállítható törlésnek
- A minimálisan privilegizált Linux-funkciókat kell kikényszeríteni a tárolók számára
- Csak a Redis Cache biztonságos kapcsolatai legyenek engedélyezve
- A tárolók AppArmor-profiljának felülbírálását vagy letiltását korlátozni kell
- Az emelt szintű tárolókat el kell kerülni
- A tárolók futtatását root felhasználóként el kell kerülni
- Engedélyezni kell a tárfiókokba történő biztonságos átvitelt
- Service Fabric-fürtökön a ClusterProtectionLevel tulajdonságot EncryptAndSign értékre kell beállítani
- Service Fabric-fürtök esetében csak Azure Active Directoryt kell használnia az ügyfél-hitelesítéshez
- A szolgáltatásoknak csak a megengedett portok figyelésére kell figyelniük
- A Storage-fiók nyilvános hozzáférését nem szabad engedélyezni
- A Storage-fiókokat át kell telepíteni az új Azure Resource Manager erőforrásokra
- A Storage-fiókoknak a virtuális hálózati szabályok használatával kell korlátoznia a hálózati hozzáférést
- A gazdagép hálózatkezelésének és portjainak használatát korlátozni kell
- A pod HostPath mennyiségi csatlakoztatások használatát egy ismert listára kell korlátozni, hogy korlátozza a csomópontok hozzáférését a feltört tárolók között
- A Azure Key Vault tárolt tanúsítványok érvényességi időtartama nem haladhatja meg a 12 hónapot
- A virtuális gépeket át kell telepíteni az új Azure Resource Manager erőforrásokra
- A webalkalmazási tűzfalat (WAF) engedélyezni kell Application Gateway
- Engedélyezni kell a webalkalmazási tűzfalat (WAF) az Azure bejárati szolgáltatásának szolgáltatásához

