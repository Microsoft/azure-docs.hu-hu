---
title: Adattitkosítási modellek a Microsoft Azure
description: Ez a cikk áttekintést nyújt az adattitkosítási modellekről a Microsoft Azure.
services: security
documentationcenter: na
author: msmbaldwin
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mbaldwin
ms.openlocfilehash: 95ab5917779a73b7221a5b431126164aef88b494
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812119"
---
# <a name="data-encryption-models"></a>Adattitkosítási modellek

A különböző titkosítási modellek, illetve azok előnyeinek és hátrányának ismerete elengedhetetlen annak megértéséhez, hogy az Azure különböző erőforrás-szolgáltatói hogyan valósítják meg a titkosítást az Rest szolgáltatásban. Ezek a definíciók az Azure összes erőforrás-szolgáltatója között meg vannak osztva a közös nyelv és a taxonónia biztosítása érdekében.

A kiszolgálóoldali titkosításnak három forgatókönyve van:

- Kiszolgálóoldali titkosítás Service-Managed kulcsokkal
  - Az Azure erőforrás-szolgáltatók elvégzik a titkosítási és visszafejtési műveleteket
  - A kulcsokat a Microsoft kezeli
  - Teljes körű felhőfunkciók

- Kiszolgálóoldali titkosítás ügyfél által felügyelt kulcsokkal a Azure Key Vault
  - Az Azure erőforrás-szolgáltatók elvégzik a titkosítási és visszafejtési műveleteket
  - Az ügyfél a kulcsokat a Azure Key Vault
  - Teljes körű felhőfunkciók

- Kiszolgálóoldali titkosítás ügyfél által felügyelt kulcsokkal ügyfél által felügyelt hardveren
  - Az Azure erőforrás-szolgáltatók elvégzik a titkosítási és visszafejtési műveleteket
  - Ügyfél által vezérelt hardverkulcsok vezérlése
  - Teljes körű felhőfunkciók

A kiszolgálóoldali titkosítási modellek az Azure-szolgáltatás által végzett titkosítást jelentik. Ebben a modellben az erőforrás-szolgáltató végzi el a titkosítási és visszafejtési műveleteket. Az Azure Storage például egyszerű szöveges műveletekben fogadhat adatokat, és belsőleg elvégzi a titkosítást és a visszafejtést. Az erőforrás-szolgáltató a megadott konfigurációtól függően a Microsoft vagy az ügyfél által kezelt titkosítási kulcsokat is használhat.

![Kiszolgáló](./media/encryption-models/azure-security-encryption-atrest-fig3.png)

Az összes kiszolgálóoldali titkosítás az rest modellek esetében a kulcskezelés fontos jellemzőire utal. Ide tartozik a titkosítási kulcsok létrehozása és tárolása, valamint a hozzáférési modellek és a kulcsrotációs eljárások.  

Ügyféloldali titkosításhoz vegye figyelembe a következőket:

- Az Azure-szolgáltatások nem látják a visszafejtett adatokat
- Az ügyfelek a helyszínen (vagy más biztonságos tárolókban) kezelik és tárolják a kulcsokat. A kulcsok nem érhetők el az Azure-szolgáltatások számára
- Csökkentett felhőfunkciók

Az Azure-ban támogatott titkosítási modellek két fő csoportra vannak felosztva: "Ügyféloldali titkosítás" és "Kiszolgálóoldali titkosítás" a korábban említettek szerint. Az Azure-szolgáltatások mindig a használt titkosítási modelltől függetlenül javasolnak biztonságos szállítást, például TLS-t vagy HTTPS-t. Ezért az adatokat az átviteli protokollnak kell kezelnie, és nem lehet fontos tényező annak meghatározásában, hogy melyik titkosítási titkosítást kell használni az rest-modellben.

## <a name="client-encryption-model"></a>Ügyfél-titkosítási modell

Az ügyfél-titkosítási modell az erőforrás-szolgáltatón vagy az Azure-n kívül a szolgáltatás vagy a hívó alkalmazás által végrehajtott titkosításra vonatkozik. A titkosítást az Azure-beli szolgáltatásalkalmazás vagy az ügyfél adatközpontjában futó alkalmazás hajthatja végre. A titkosítási modell használata esetén az Azure erőforrás-szolgáltató mindkét esetben titkosított adatblobokat kap anélkül, hogy bármilyen módon visszafejtenie lenne az adatokat, vagy hozzáféréssel lenne a titkosítási kulcsokhoz. Ebben a modellben a kulcskezelést a hívó szolgáltatás/alkalmazás végez, és átlátszatlan az Azure-szolgáltatás számára.

![Ügyfél](./media/encryption-models/azure-security-encryption-atrest-fig2.png)

## <a name="server-side-encryption-using-service-managed-keys"></a>Kiszolgálóoldali titkosítás szolgáltatás által kezelt kulcsokkal

Sok ügyfél számára az alapvető követelmény az adatok titkosítása, amikor azok nem elérhetőek. A szolgáltatás által felügyelt kulcsokkal történő kiszolgálóoldali titkosítás lehetővé teszi ezt a modellt azáltal, hogy lehetővé teszi az ügyfelek számára az adott erőforrás (Tárfiók, SQL DB stb.) megjelölését a titkosításhoz, és meghagyják az összes kulcskezelési szempontot, például a kulcskiadást, a rotációt és a Microsoftnak történő biztonsági mentést. Az olyan Azure-szolgáltatások, amelyek támogatják az azure-beli titkosítást, általában támogatják ezt a modellt a titkosítási kulcsok felügyeletének az Azure-ba való kiszervezéséhez. Az Azure erőforrás-szolgáltató létrehozza a kulcsokat, elhelyezi őket egy biztonságos tárolóban, és szükség esetén lekéri őket. Ez azt jelenti, hogy a szolgáltatás teljes hozzáféréssel rendelkezik a kulcsokhoz, és teljes körű vezérléssel rendelkezik a hitelesítő adatok életciklusának kezelése felett.

![Kezelt](./media/encryption-models/azure-security-encryption-atrest-fig4.png)

A szolgáltatás által felügyelt kulcsokat használó kiszolgálóoldali titkosítás ezért gyorsan kezeli az ügyfél számára alacsony terheléssel és az éppen nem használt titkosítás felhasználásával kapcsolatos igényeket. Ha elérhető, az ügyfél általában megnyitja Azure Portal cél-előfizetéshez és erőforrás-szolgáltatóhoz, és ellenőrzi a jelölőnégyzetet, amely azt jelzi, hogy az adatokat titkosítani szeretnék. Egyes Erőforrás-kezelők kiszolgálóoldali titkosítása szolgáltatás által felügyelt kulcsokkal alapértelmezés szerint be van kapcsolva.

A Microsoft által felügyelt kulcsokkal való kiszolgálóoldali titkosítás azt jelenti, hogy a szolgáltatás teljes hozzáféréssel rendelkezik a kulcsok tárolására és kezelésére. Bár egyes ügyfelek azért kezelhetik a kulcsokat, mert úgy érzi, nagyobb biztonságot kapnak, az egyéni kulcstároló megoldáshoz kapcsolódó költségeket és kockázatokat is figyelembe kell venni a modell értékelésekor. A szervezetek sok esetben megállapíthatják, hogy egy helyszíni megoldás erőforráskorlátai vagy kockázatai nagyobbak lehetnek, mint az adattitkosítási kulcsok felhőalapú felügyeletének kockázata.  Előfordulhat azonban, hogy ez a modell nem elegendő olyan szervezetek számára, amelyek rendelkeznek a titkosítási kulcsok létrehozásának vagy életciklusának szabályozására vonatkozó követelményekkel, vagy amelyek esetében a szolgáltatás titkosítási kulcsait különböző személyek kezelik, mint a szolgáltatást kezelők (vagyis a kulcskezelés elkülönítése a szolgáltatás általános felügyeleti modelljétől).

### <a name="key-access"></a>Kulcselérés

A szolgáltatás által felügyelt kulcsokkal való kiszolgálóoldali titkosítás használata során a kulcs létrehozását, tárolását és a szolgáltatáshoz való hozzáférést is a szolgáltatás kezeli. Az alapvető Azure-erőforrásszolgáltatók általában olyan tárolóban tárolják az adattitkosítási kulcsokat, amely közel van az adatokhoz, és gyorsan elérhetők és elérhetők, amíg a kulcstitkosítási kulcsok egy biztonságos belső tárolóban vannak tárolva.

**Előnyök**

- Egyszerű beállítás
- A Microsoft kezeli a kulcsrotációt, a biztonsági mentést és a redundanciát
- Az ügyfél nem rendelkezik az implementáció költségeivel vagy az egyéni kulcskezelési séma kockázatával.

**Hátrányok**

- A titkosítási kulcsok (kulcsspecifikáció, életciklus, visszavonás stb.) nem szabályozható az ügyfelek számára
- Nem lehet elkülönítni a kulcskezelést a szolgáltatás általános felügyeleti modelljétől

## <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Kiszolgálóoldali titkosítás ügyfél által felügyelt kulcsokkal a Azure Key Vault

Olyan forgatókönyvek esetén, ahol az igény az, hogy titkosítani kell az tárolt adatokat, és szabályozni kell a titkosítási kulcsokat, az ügyfelek kiszolgálóoldali titkosítást használhatnak az ügyfél által felügyelt kulcsokkal a Key Vault. Egyes szolgáltatások csak a legfelső szintű kulcstitkosítási kulcsot Azure Key Vault a titkosított adattitkosítási kulcsot pedig az adatokhoz közelebbi belső helyen tárolják. Ebben a forgatókönyvben az ügyfelek a saját kulcsukat Key Vault (BYOK – Bring Your Own Key), vagy újakat hozhatnak létre, és a kívánt erőforrások titkosításához használhatja őket. Bár a titkosítási és visszafejtési műveleteket az erőforrás-szolgáltató végzi, az összes titkosítási művelethez a konfigurált kulcstitkosítási kulcsot használja legfelső szintű kulcsként.

A kulcstitkosítási kulcsok elvesztése adatvesztést jelent. Ezért a kulcsokat nem szabad törölni. A kulcsokról minden létrehozáskor vagy elforgatáskor biztonsági érdemes biztonsági okat létrehozni. [A kulcstitkosítási](../../key-vault/general/soft-delete-overview.md) kulcsokat tároló tárolókban engedélyezni kell a soft-delete funkciót. Kulcs törlése helyett állítsa az engedélyezve beállítást false (hamis) vagy a expiry date (lejárati dátum) beállításra.

### <a name="key-access"></a>Kulcselérés

A kiszolgálóoldali titkosítási modell ügyfél által felügyelt kulcsokkal a Azure Key Vault magában foglalja, hogy a szolgáltatás szükség szerint hozzáfér a kulcsokhoz a titkosításhoz és visszafejtéséhez. Az rest kulcsok titkosítása hozzáférés-vezérlési szabályzaton keresztül érhető el a szolgáltatások számára. Ez a szabályzat hozzáférést biztosít a szolgáltatásidentitás számára a kulcs fogadásához. Egy társított előfizetés nevében futó Azure-szolgáltatás konfigurálható az előfizetésben egy identitással. A szolgáltatás elvégezheti a Azure Active Directory hitelesítést, és kaphat egy hitelesítési jogkivonatot, amely azonosítja magát, mint az előfizetés nevében működő szolgáltatást. Ez a jogkivonat ezután Key Vault számára, hogy beszerezhető legyen egy kulcs, amelyhez hozzáféréssel rendelkezik.

A titkosítási kulcsokat használó műveletek esetén a szolgáltatásidentitás hozzáférést kaphat a következő műveletek bármelyikéhez: visszafejtés, titkosítás, unwrapKey, wrapKey, ellenőrzés, aláírás, get, list, update, create, import, delete, backup és restore.

Az adatok titkosításához vagy visszafejtéséhez használt kulcs beszerzéséhez az Resource Manager-szolgáltatáspéldány által futtatott szolgáltatásidentitásnak UnwrapKey (a visszafejtési kulcs beszerzéséhez) és WrapKey (kulcs beszúrása a Kulcstartóba új kulcs létrehozásakor) beállítással kell lennie.

>[!NOTE]
>Az engedélyezésről Key Vault key vault biztonságossá tétő oldalán talál további információt a [Azure Key Vault dokumentációjában.](../../key-vault/general/security-features.md)

**Előnyök**

- A használt kulcsok teljes körű vezérlése – a titkosítási kulcsok kezelése az ügyfél Key Vault az ügyfél ellenőrzése alatt áll.
- Több szolgáltatás titkosítása egyetlen főkiszolgálóra
- Elkülönítheti a kulcskezelést a szolgáltatás általános felügyeleti modelljétől
- Meghatározhatja a szolgáltatások és a kulcsok helyét a régiók között

**Hátrányok**

- Az ügyfél felelős a kulcselérések kezelésért
- Az ügyfél teljes felelősséggel rendelkezik a kulcs életciklus-kezelésért
- További beállítások és & többletterhelés

## <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Kiszolgálóoldali titkosítás ügyfél által felügyelt kulcsokkal az ügyfél által felügyelt hardverben

Egyes Azure-szolgáltatások lehetővé teszik a saját gazdagépkulcs (HYOK) kulcskezelési modelljét. Ez a felügyeleti mód olyan forgatókönyvekben hasznos, ahol szükség van az adatok titkosítására és a kulcsoknak egy, a Microsoft által nem vezérelt jogvédett adattárban való kezelésére. Ebben a modellben a szolgáltatásnak egy külső oldalról kell lekérnie a kulcsot. Ez hatással van a teljesítményre és a rendelkezésre állásra vonatkozó garanciákra, és a konfiguráció összetettebb. Továbbá, mivel a szolgáltatás hozzáfér az adattitkosítási kulcshoz a titkosítási és visszafejtési műveletek során, a modell általános biztonsági garanciái hasonlóak ahhoz, amikor a kulcsokat az ügyfél a Azure Key Vault.  Ennek eredményeképpen ez a modell nem megfelelő a legtöbb szervezet számára, kivéve, ha speciális kulcskezelési követelményekkel kell szembenük áll. Ezen korlátozások miatt a legtöbb Azure-szolgáltatás nem támogatja a kiszolgálóoldali titkosítást a kiszolgáló által felügyelt kulcsokkal az ügyfél által vezérelt hardverben.

### <a name="key-access"></a>Kulcselérés

Ha az ügyfél által felügyelt hardveren szolgáltatás által felügyelt kulcsokat használó kiszolgálóoldali titkosítást használ, a kulcsokat az ügyfél által konfigurált rendszeren kezeli a rendszer. Az ezt a modellt támogató Azure-szolgáltatások biztonságos kapcsolatot biztosítanak az ügyfél által biztosított kulcstárolóval.

**Előnyök**

- A használt legfelső szintű kulcs teljes körű vezérlése – a titkosítási kulcsokat az ügyfél által megadott tároló kezeli
- Több szolgáltatás titkosítása egyetlen főkiszolgálóra
- Elkülönítheti a kulcskezelést a szolgáltatás általános felügyeleti modelljétől
- Meghatározhatja a szolgáltatások és a kulcsok helyét a régiók között

**Hátrányok**

- Teljes felelősség a kulcstárolásért, biztonságért, teljesítményért és rendelkezésre állásért
- A kulcselérések kezelésével kapcsolatos teljes felelősség
- Teljes felelősség a kulcs életciklus-kezelésért
- Jelentős beállítási, konfigurációs és folyamatos karbantartási költségek
- Nagyobb függőség az ügyfél adatközpontjai és az Azure-adatközpontok közötti hálózati rendelkezésre állástól.

## <a name="supporting-services"></a>Támogató szolgáltatások
Az egyes titkosítási modelleket támogató Azure-szolgáltatások:

| Termék, funkció vagy szolgáltatás | Server-Side Kulcs Service-Managed használata   | Server-Side Kulcs Customer-Managed használata | Client-Side Kulcs Client-Managed használata  |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
| **Mesterséges intelligencia és gépi tanulás**      |                    |                    |                    |
| Azure Cognitive Search           | Igen                | Yes                | -                  |
| Azure Cognitive Services         | Igen                | Yes                | -                  |
| Azure Machine Learning           | Igen                | Yes                | -                  |
| Azure Machine Learning Studio (klasszikus) | Yes         | Előzetes verzió, RSA 2048 bites | -               |
| Content Moderator                | Igen                | Yes                | -                  |
| Face                             | Igen                | Yes                | -                  |
| Language Understanding           | Igen                | Yes                | -                  |
| Personalizer                     | Igen                | Yes                | -                  |
| QnA Maker                        | Igen                | Yes                | -                  |
| Beszédfelismerési szolgáltatások                  | Igen                | Yes                | -                  |
| Translator Text                  | Igen                | Yes                | -                  |
| Power BI                         | Yes                | Igen, RSA 4096 bites  | -                  |
| **Elemzés**                    |                    |                    |                    |
| Azure Stream Analytics           | Yes                | igen\*\*            | -                  |
| Event Hubs                       | Igen                | Yes                | -                  |
| Functions                        | Igen                | Yes                | -                  |
| Azure Analysis Services          | Igen                | -                  | -                  |
| Azure Data Catalog               | Yes                | -                  | -                  |
| Azure HDInsight                  | Yes                | Mind                | -                  |
| Azure Monitor Application Insights | Igen                | Yes                | -                  |
| Azure Monitor Log Analytics      | Igen                | Yes                | -                  |
| Azure Adatkezelő              | Igen                | Yes                | -                  |
| Azure Data Factory               | Igen                | Yes                | -                  |
| Azure Data Lake Store            | Igen                | Igen, RSA 2048 bites  | -                  |
| **Containers**                   |                    |                    |                    |
| Azure Kubernetes Service         | Igen                | Yes                | -                  |
| Container Instances              | Igen                | Yes                | -                  |
| Container Registry               | Igen                | Yes                | -                  |
| **Számítás**                      |                    |                    |                    |
| Virtual Machines                 | Igen                | Yes                | -                  |
| Virtuálisgép-méretezési készlet        | Igen                | Igen                | -                  |
| SAP HANA                         | Igen                | Yes                | -                  |
| App Service                      | Yes                | igen\*\*            | -                  |
| Automation                       | Yes                | igen\*\*            | -                  |
| Azure Functions                  | Yes                | igen\*\*            | -                  |
| Azure Portal                     | Yes                | igen\*\*            | -                  |
| Logic Apps                       | Igen                | Yes                | -                  |
| Azure által felügyelt alkalmazások       | Yes                | igen\*\*            | -                  |
| Service Bus                      | Igen                | Yes                | -                  |
| Site Recovery                    | Igen                | Yes                | -                  |
| **Adatbázisok**                    |                    |                    |                    |
| SQL Server on Virtual Machines   | Igen                | Igen                | Igen                |
| Azure SQL Database               | Igen                | Igen, RSA 3072 bites  | Yes                |
| Azure SQL Database For MariaDB-hez   | Yes                | -                  | -                  |
| Azure SQL Database MySQL-hez     | Igen                | Yes                | -                  |
| Azure SQL Database PostgreSQL-hez | Igen               | Yes                | -                  |
| Azure Synapse Analytics          | Yes                | Igen, RSA 3072 bites  | -                  |
| SQL Server Stretch Database      | Yes                | Igen, RSA 3072 bites  | Yes                |
| Table Storage                    | Igen                | Igen                | Igen                |
| Azure Cosmos DB                  | Igen                | Yes                | -                  |
| Azure Databricks                 | Igen                | Yes                | -                  |
| Azure Database Migration Service | Yes                | N/a\*              | -                  |
| **DevOps**                       |                    |                    |                    |
| Azure DevOps Services            | Yes                | -                  | -                  |
| Azure Repos                      | Yes                | -                  | -                  |
| **Identitás**                     |                    |                    |                    |
| Azure Active Directory           | Yes                | -                  | -                  |
| Azure Active Directory tartományi szolgáltatások | Igen          | Yes                | -                  |
| **Integráció**                  |                    |                    |                    |
| Service Bus                      | Igen                | Igen                | Yes                |
| Event Grid                       | Yes                | -                  | -                  |
| API Management                   | Yes                | -                  | -                  |
| **IoT-szolgáltatások**                 |                    |                    |                    |
| IoT Hub                          | Igen                | Igen                | Yes                |
| IoT Hub Device Provisioning      | Igen                | Yes                | -                  |
| **Felügyelet és cégirányítás**    |                    |                    |                    |
| Azure Site Recovery              | Yes                | -                  | -                  |
| Azure Migrate                    | Igen                | Yes                | -                  |
| **Média**                        |                    |                    |                    |
| Media Services                   | Igen                | Igen                | Yes                |
| **Biztonság**                     |                    |                    |                    |
| Azure Security Center for IoT    | Igen                | Yes                | -                  |
| Azure Sentinel                   | Igen                | Yes                | -                  |
| **Storage**                      |                    |                    |                    |
| Blob Storage                     | Igen                | Igen                | Yes                |
| Prémium Blob Storage             | Igen                | Igen                | Yes                |
| Disk Storage                     | Igen                | Yes                | -                  |
| utralemez Storage               | Igen                | Yes                | -                  |
| Felügyelt Disk Storage             | Igen                | Yes                | -                  |
| File Storage                     | Igen                | Yes                | -                  |
| Fájl Premium Storage             | Igen                | Yes                | -                  |
| File Sync                         | Igen                | Yes                | -                  |
| Queue Storage                    | Igen                | Igen                | Yes                |
| Avere vFXT                       | Yes                | -                  | -                  |
| Azure Cache for Redis            | Yes                | N/a\*              | -                  |
| Azure NetApp Files               | Igen                | Yes                | -                  |
| Archive Storage                  | Igen                | Yes                | -                  |
| StorSimple                       | Igen                | Igen                | Yes                |
| Azure Backup                     | Igen                | Igen                | Yes                |
| Data Box                         | Igen                | -                  | Yes                |
| Data Box Edge                    | Igen                | Yes                | -                  |

\* Ez a szolgáltatás nem őrz meg adatokat. Az átmeneti gyorsítótárak (ha vannak) Microsoft-kulccsal vannak titkosítva.

\*\* Ez a szolgáltatás támogatja az adatok tárolását a saját Key Vault- vagy Storage-fiókjában, vagy más olyan adatmegőrzési szolgáltatásban, amely már támogatja a Server-Side titkosítást Customer-Managed kulccsal.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, [hogyan használható a titkosítás az Azure-ban.](encryption-overview.md)
- Megtudhatja, hogyan használja az Azure [a kettős titkosítást](double-encryption.md) az adatok titkosításával járó fenyegetések elhárítására.
