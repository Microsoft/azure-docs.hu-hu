---
title: A hibás konfigurációk megelőzése Azure Security Center
description: Megtudhatja, hogyan használhatja Security Center "betartatása" és "megtagadás" beállításait a javaslatok részleteinek oldalain.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: a3da9cdea543894aa7aec66112e28658beac84b5
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99558193"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>Helytelen konfigurációk megelőzése Kényszerítés/Megtagadás javaslatokkal

A biztonsági incidensek jelentős oka a biztonság. A Security Center mostantól lehetővé teszi az új erőforrások helytelen konfigurációjának *megelőzését* az adott javaslatok tekintetében. 

Ez a funkció segít megőrizni a számítási feladatokat, és stabilizálni tudja a biztonságos pontszámot.

A biztonságos konfiguráció egy adott javaslat alapján történő érvényesítése két üzemmódban érhető el:

- A Azure Policy **megtagadásának** hatására leállíthatja a nem megfelelő állapotú erőforrásokat
- A **kikényszerítés** lehetőség használatával kihasználhatja az Azure-szabályzat **DeployIfNotExist** hatását, és a létrehozáskor automatikusan elháríthatja a nem megfelelő erőforrásokat.

Ez az erőforrás részletei lap tetején található a kiválasztott biztonsági javaslatok esetében (lásd: [javaslatok a megtagadási/kényszerítő beállításokkal](#recommendations-with-denyenforce-options)).

## <a name="prevent-resource-creation"></a>Erőforrás-létrehozás megakadályozása

1. Nyissa meg azt a javaslatot, amely szerint az új erőforrásoknak meg kell felelniük, és válassza a **Megtagadás** gombot az oldal tetején.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="Javaslat lap Kiemelt megtagadás gombja":::

    Megnyílik a konfigurációs ablaktábla, amely felsorolja a hatókör beállításait. 

1. A hatókör beállításához válassza ki a megfelelő előfizetést vagy felügyeleti csoportot.

    > [!TIP]
    > A sor végén található három pont használatával egyetlen előfizetés módosítható, vagy a jelölőnégyzetek használatával több előfizetést vagy csoportot is kijelölhet, majd kiválaszthatja, **hogy megtagadja a módosítást**.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="Azure Policy megtagadás hatókörének beállítása":::


## <a name="enforce-a-secure-configuration"></a>Biztonságos konfiguráció érvényesítése

1. Nyissa meg azt a javaslatot, amelybe telepíteni kívánja a sablon központi telepítését, ha az új erőforrások nem elégítik ki azt, majd kattintson a **kikényszerítés** gombra az oldal tetején.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="Javaslat lap Kiemelt kikényszerített gombja":::

    Megnyílik a konfigurációs ablaktábla az összes házirend-konfigurációs lehetőséggel. 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="Konfigurációs beállítások érvényesítése":::

1. Adja meg a hatókört, a hozzárendelés nevét és az egyéb kapcsolódó beállításokat.

1. Válassza az **Áttekintés + létrehozás** lehetőséget.

## <a name="recommendations-with-denyenforce-options"></a>Megtagadási/kikényszerítő lehetőségekkel kapcsolatos javaslatok

A következő javaslatok használhatók a **megtagadási** beállítással:

- A Storage-fiókokhoz a tűzfal és a virtuális hálózati konfigurációk hozzáférését korlátozni kell
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
- A Storage-fiókokat át kell telepíteni az új Azure Resource Manager erőforrásokra
- A Storage-fiókoknak a virtuális hálózati szabályok használatával kell korlátoznia a hálózati hozzáférést
- A gazdagép hálózatkezelésének és portjainak használatát korlátozni kell
- A pod HostPath mennyiségi csatlakoztatások használatát egy ismert listára kell korlátozni, hogy korlátozza a csomópontok hozzáférését a feltört tárolók között
- A Azure Key Vault tárolt tanúsítványok érvényességi időtartama nem haladhatja meg a 12 hónapot
- A virtuális gépeket át kell telepíteni az új Azure Resource Manager erőforrásokra
- A webalkalmazási tűzfalat (WAF) engedélyezni kell Application Gateway
- Engedélyezni kell a webalkalmazási tűzfalat (WAF) az Azure bejárati szolgáltatásának szolgáltatásához

A következő javaslatok használhatók a **kényszerítő** beállítással:

- Az SQL Server naplózását engedélyezni kell
- Azure Backup engedélyezni kell a virtuális gépeket
- Az SQL-alapú Azure Defender-t engedélyeznie kell az SQL-kiszolgálókon
- A Azure Stream Analytics lévő diagnosztikai naplókat engedélyezni kell
- A Batch-fiókokban lévő diagnosztikai naplókat engedélyezni kell
- A Data Lake Analytics lévő diagnosztikai naplókat engedélyezni kell
- Az Event hub diagnosztikai naplóit engedélyezni kell
- A Key Vault lévő diagnosztikai naplókat engedélyezni kell
- A Logic Apps lévő diagnosztikai naplókat engedélyezni kell
- A keresési szolgáltatásokban engedélyezni kell a diagnosztikai naplókat.
- A Service Bus lévő diagnosztikai naplókat engedélyezni kell