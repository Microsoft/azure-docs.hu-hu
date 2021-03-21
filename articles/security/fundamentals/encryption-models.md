---
title: Adattitkosítási modellek Microsoft Azure
description: Ez a cikk áttekintést nyújt a Microsoft Azure adattitkosítási modelljeiről.
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
ms.openlocfilehash: 9194f38ddea5570d94ba6c87ea6a537cb41fb98c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051378"
---
# <a name="data-encryption-models"></a>Adattitkosítási modellek

A különböző titkosítási modellek és azok előnyeinek és hátrányainak megértése elengedhetetlen ahhoz, hogy a különböző Azure-beli erőforrás-szolgáltatók hogyan implementálják a titkosítást a nyugalmi állapotban. Ezek a definíciók az Azure összes erőforrás-szolgáltatóján megoszthatók a közös nyelv és taxonómia biztosítása érdekében.

A kiszolgálóoldali titkosításnak három forgatókönyve van:

- Kiszolgálóoldali titkosítás Service-Managed kulcsok használatával
  - Az Azure-erőforrás-szolgáltatók végzik a titkosítási és a visszafejtési műveleteket
  - A Microsoft kezeli a kulcsokat
  - Teljes körű Felhőbeli funkciók

- Kiszolgálóoldali titkosítás az ügyfél által felügyelt kulcsok használatával Azure Key Vault
  - Az Azure-erőforrás-szolgáltatók végzik a titkosítási és a visszafejtési műveleteket
  - Az ügyfelek Azure Key Vaulton keresztül vezérlik a kulcsokat
  - Teljes körű Felhőbeli funkciók

- Kiszolgálóoldali titkosítás az ügyfél által felügyelt kulcsokkal az ügyfél által vezérelt hardveren
  - Az Azure-erőforrás-szolgáltatók végzik a titkosítási és a visszafejtési műveleteket
  - Az ügyfél által vezérelt hardveren az ügyfelek vezérlik a kulcsokat
  - Teljes körű Felhőbeli funkciók

A kiszolgálóoldali titkosítási modellek az Azure-szolgáltatás által végrehajtott titkosításra vonatkoznak. Ebben a modellben az erőforrás-szolgáltató végrehajtja a titkosítási és a visszafejtési műveletet. Előfordulhat például, hogy az Azure Storage egyszerű szöveges műveletekben fogadja az adatgyűjtést, és belsőleg fogja végrehajtani a titkosítást és a visszafejtést. Előfordulhat, hogy az erőforrás-szolgáltató a Microsoft vagy az ügyfél által kezelt titkosítási kulcsokat használja a megadott konfigurációtól függően.

![Kiszolgáló](./media/encryption-models/azure-security-encryption-atrest-fig3.png)

A kiszolgálóoldali titkosítás a REST-modellekben a kulcskezelő megkülönböztető jellemzőit foglalja magában. Ide tartozik a titkosítási kulcsok létrehozásának helye és módja, valamint a hozzáférési modellek és a kulcsfontosságú rotációs eljárások.  

Ügyféloldali titkosítás esetén vegye figyelembe a következőket:

- Az Azure-szolgáltatások nem látják a visszafejtett információt
- Az ügyfelek a helyszínen (vagy más biztonságos áruházakban) kezelhetik és tárolják a kulcsokat. A kulcsok nem érhetők el az Azure-szolgáltatások számára
- Csökkentett Felhőbeli funkciók

Az Azure-ban támogatott titkosítási modellek két fő csoportra oszlanak: az "ügyfél-titkosítás" és a "kiszolgálóoldali titkosítás", ahogy korábban említettük. A REST-modellben használt titkosítástól függetlenül az Azure-szolgáltatások mindig a biztonságos átvitel, például a TLS vagy a HTTPS használatát ajánlják. Ezért a átvitel titkosítását az átviteli protokollnak kell megcéloznia, és nem lehet jelentős tényező annak meghatározásában, hogy melyik titkosítást használja a REST-modell.

## <a name="client-encryption-model"></a>Ügyfél-titkosítási modell

Az ügyfél-titkosítási modell az erőforrás-szolgáltatón vagy az Azure-on kívül a szolgáltatás vagy a hívó alkalmazás által végrehajtott titkosításra utal. A titkosítás az Azure-beli szolgáltatásalkalmazás vagy az ügyfél adatközpontjában futó alkalmazások által végezhető el. Mindkét esetben a titkosítási modell kihasználása esetén az Azure erőforrás-szolgáltató titkosított blobot kap az adattitkosítás bármilyen módon történő visszafejtése és a titkosítási kulcsok elérésének lehetősége nélkül. Ebben a modellben a kulcskezelő szolgáltatást/alkalmazást a Calling Service/Application végzi el, és az nem átlátható az Azure szolgáltatásban.

![Ügyfél](./media/encryption-models/azure-security-encryption-atrest-fig2.png)

## <a name="server-side-encryption-using-service-managed-keys"></a>Kiszolgálóoldali titkosítás a szolgáltatás által felügyelt kulcsokkal

Sok ügyfél esetében az alapvető követelmény az, hogy az adatok titkosítva legyenek, amikor nyugalmi állapotban van. A szolgáltatás által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás lehetővé teszi ezt a modellt azáltal, hogy lehetővé teszi az ügyfeleknek az adott erőforrás (Storage-fiók, SQL-adatbázis stb.) megjelölését a titkosításhoz, és elhagyják az összes kulcsfontosságú felügyeleti szempontot, mint például a kulcs kiadása, a rotáció és a biztonsági mentés A REST titkosítást támogató legtöbb Azure-szolgáltatás általában támogatja ezt a modellt, amellyel kiszervezheti a titkosítási kulcsok kezelését az Azure-ba. Az Azure erőforrás-szolgáltató létrehozza a kulcsokat, elhelyezi őket a biztonságos tárolóban, és szükség esetén lekéri őket. Ez azt jelenti, hogy a szolgáltatásnak teljes hozzáférése van a kulcsokhoz, és a szolgáltatás teljes körű vezérléssel rendelkezik a hitelesítő adatok életciklusának kezelése során.

![felügyelt](./media/encryption-models/azure-security-encryption-atrest-fig4.png)

A szolgáltatás által felügyelt kulcsokat használó kiszolgálóoldali titkosítás ezért gyors megoldást jelent a titkosítás nyugalmi állapotba való bevezetésének szükségessége az ügyfél számára alacsony terheléssel. Amikor egy ügyfél elérhetővé válik, általában megnyitja a cél-előfizetés és az erőforrás-szolgáltató Azure Portalét, és bejelöl egy mezőt, amely azt jelzi, hogy az adattitkosítást szeretné. Egyes erőforrás-kezelők kiszolgálóoldali titkosítása szolgáltatás által felügyelt kulcsokkal beállítás alapértelmezés szerint be van kapcsolva.

A Microsoft által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás azt jelenti, hogy a szolgáltatás teljes hozzáféréssel rendelkezik a kulcsok tárolásához és kezeléséhez. Előfordulhat, hogy egyes ügyfelek kezelhetik a kulcsokat, mivel úgy érzik, hogy nagyobb biztonságot nyújtanak, a modell kiértékelése során figyelembe kell venni az egyéni kulcsú tárolási megoldáshoz kapcsolódó költségeket és kockázatokat. Sok esetben a szervezet megállapíthatja, hogy az erőforrás-megszorítások vagy egy helyszíni megoldás kockázata nagyobb lehet, mint a REST-kulcsok titkosításának Felhőbeli felügyeletének kockázata.  Előfordulhat azonban, hogy ez a modell nem elegendő ahhoz, hogy olyan szervezetek rendelkezzenek, amelyek a titkosítási kulcsok létrehozását vagy életciklusát szabályozzák, vagy hogy a különböző személyzet kezelje a szolgáltatás titkosítási kulcsait, mint a szolgáltatás felügyeletét (azaz a szolgáltatás általános felügyeleti modelljéből származó kulcskezelő elkülönítését).

### <a name="key-access"></a>Kulcs elérése

A szolgáltatás által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás használata esetén a kulcs létrehozása, tárolása és a szolgáltatáshoz való hozzáférés mind a szolgáltatás által felügyelt. Az alapszintű Azure-erőforrás-szolgáltatók jellemzően az adattitkosítási kulcsokat tárolják egy olyan tárolóban, amely az adatforráshoz közeledik, és gyorsan elérhető és hozzáférhető, miközben a kulcs titkosítási kulcsait egy biztonságos belső tároló tárolja.

**Előnyök**

- Egyszerű beállítás
- A Microsoft a legfontosabb rotációt, biztonsági mentést és redundanciát kezeli
- Az ügyfél nem rendelkezik a megvalósítással vagy egy egyéni kulcskezelő séma kockázatával.

**Hátrányok**

- A titkosítási kulcsok (a kulcs specifikációja, az életciklus, a visszavonás stb.) nem adhatók meg az ügyfelek felett.
- Nincs lehetőség a kulcsfontosságú felügyelet elkülönítésére a szolgáltatás általános felügyeleti modelljéből

## <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Kiszolgálóoldali titkosítás az ügyfél által felügyelt kulcsok használatával Azure Key Vault

Olyan esetekben, amikor a követelmény az inaktív adatok titkosítása és a titkosítási kulcsok használata, a felhasználók a Key Vault felügyelt kulcsaival használhatják a kiszolgálóoldali titkosítást. Egyes szolgáltatások csak a legfelső szintű kulcs titkosítási kulcsát tárolhatják Azure Key Vaultban, és a titkosított adattitkosítási kulcsot egy belső helyen tárolják, amely közelebb áll az adatforgalomhoz. Ebben az esetben az ügyfelek saját kulcsaikat hozhatnak Key Vault (BYOK – Bring Your Own Key), vagy újakat generálnak, és a kívánt erőforrások titkosítására használhatják őket. Amíg az erőforrás-szolgáltató végrehajtja a titkosítási és a visszafejtési műveleteket, a konfigurált kulcs titkosítási kulcsát használja az összes titkosítási művelet legfelső szintű kulcsaként.

A kulcs titkosítási kulcsainak elvesztése adatvesztést jelent. Emiatt a kulcsokat nem szabad törölni. A kulcsok létrehozásakor vagy elforgatásakor biztonsági mentést kell készíteni. A helyreállítható [törlést](../../key-vault/general/soft-delete-overview.md) engedélyezni kell minden olyan tárolón, amely a kulcs titkosítási kulcsait tárolja. A kulcs törlése helyett állítsa a beállítást hamis értékre, vagy állítsa be a lejárati dátumot.

### <a name="key-access"></a>Kulcs elérése

A Azure Key Vault ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítási modell magában foglalja a kulcsoknak a titkosításhoz és a visszafejtéshez szükséges hozzáférését a szolgáltatáshoz. A REST kulcsok titkosítása hozzáférés-vezérlési házirend segítségével érhető el a szolgáltatás számára. Ez a szabályzat biztosítja, hogy a szolgáltatás identitása hozzáférjen a kulcs fogadásához. Egy társított előfizetés nevében futó Azure-szolgáltatás konfigurálható identitással az előfizetésben. A szolgáltatás elvégezheti Azure Active Directory hitelesítését, és olyan hitelesítési tokent kap, amely magát az előfizetés nevében működő szolgáltatásként azonosítja. Ezt a tokent ezután megtekintheti Key Vault egy olyan kulcs beszerzéséhez, amelyhez hozzáférést kapott.

A titkosítási kulcsokat használó műveletek esetében a szolgáltatás identitása a következő műveletek bármelyikéhez biztosítható: visszafejtés, titkosítás, unwrapKey, wrapKey, ellenőrzés, aláírás, beolvasás, Listázás, frissítés, létrehozás, importálás, törlés, biztonsági mentés és visszaállítás.

Az adatok titkosítása vagy visszafejtése során használt kulcs beszerzéséhez a Resource Manager-szolgáltatás példányának futtatásához szükséges UnwrapKey (a visszafejtési kulcs lekérése) és a WrapKey (kulcs behelyezése a Key vaultba új kulcs létrehozásakor).

>[!NOTE]
>Az Key Vault engedélyezésével kapcsolatos további információkért tekintse meg a [Azure Key Vault dokumentációjában](../../key-vault/general/secure-your-key-vault.md)a Key Vault biztonságossá tétele című oldalt.

**Előnyök**

- A használt kulcsok teljes körű vezérlése – a titkosítási kulcsok kezelése az ügyfél Key Vault az ügyfél vezérlése alatt történik.
- Több szolgáltatás titkosításának lehetősége egyetlen főkiszolgálóra
- A szolgáltatás általános felügyeleti modelljéből elkülönítheti a kulcskezelő szolgáltatást
- Meghatározhatja a szolgáltatás és a kulcs helyét a régiók között

**Hátrányok**

- Az ügyfél teljes felelősséggel rendelkezik a kulcs-hozzáférés kezeléséhez
- Az ügyfél teljes felelősséggel rendelkezik a fő életciklus-felügyelethez
- További beállítás & konfiguráció terhelése

## <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Kiszolgálóoldali titkosítás az ügyfél által felügyelt kulcsokkal az ügyfél által vezérelt hardverben

Egyes Azure-szolgáltatások lehetővé teszik a saját kulcsú (HYOK) kulcskezelő modell üzemeltetését. Ez a felügyeleti mód olyan forgatókönyvekben hasznos, ahol szükség van az inaktív adatok titkosítására és a Microsoft vezérlőn kívüli védett adattárban található kulcsok kezelésére. Ebben a modellben a szolgáltatásnak egy külső helyről kell lekérnie a kulcsot. A teljesítményre és a rendelkezésre állásra vonatkozó garanciák hatással vannak, és a konfiguráció összetettebb. Továbbá, mivel a szolgáltatás a titkosítási és a visszafejtési műveletek során is hozzáfér a ADATTITKOSÍTÁSI kulcsot, a modell általános biztonsági garanciái hasonlóak ahhoz, amikor a kulcsokat ügyfél által felügyelik Azure Key Vaultban.  Ennek eredményeképpen ez a modell nem felel meg a legtöbb szervezet számára, kivéve, ha meghatározott kulcskezelő követelményekkel rendelkeznek. A korlátozások miatt a legtöbb Azure-szolgáltatás nem támogatja a kiszolgálóoldali titkosítást a kiszolgáló által felügyelt kulcsok használatával az ügyfél által vezérelt hardverben.

### <a name="key-access"></a>Kulcs elérése

Ha a szolgáltatás által felügyelt kulcsokat használó kiszolgálóoldali titkosítást használ az ügyfél által vezérelt hardverben, a rendszer a kulcsokat az ügyfél által konfigurált rendszeren tartja karban. A modellt támogató Azure-szolgáltatások biztosítják a biztonságos kapcsolódást az ügyfél által megadott kulcstárolóhoz.

**Előnyök**

- Teljes hozzáférés a használt legfelső szintű kulcshoz – a titkosítási kulcsokat egy ügyfél által biztosított tároló kezeli
- Több szolgáltatás titkosításának lehetősége egyetlen főkiszolgálóra
- A szolgáltatás általános felügyeleti modelljéből elkülönítheti a kulcskezelő szolgáltatást
- Meghatározhatja a szolgáltatás és a kulcs helyét a régiók között

**Hátrányok**

- A kulcsok tárolásának, biztonságának, teljesítményének és rendelkezésre állásának teljes felelőssége
- A kulcsok hozzáférés-kezelésének teljes felelőssége
- A fő életciklus-felügyelet teljes felelőssége
- Jelentős telepítési, konfigurálási és folyamatos karbantartási költségek
- A hálózat rendelkezésre állásának növelése az ügyfél-adatközpont és az Azure-adatközpontok között.

## <a name="supporting-services"></a>Támogató szolgáltatások
Az egyes titkosítási modelleket támogató Azure-szolgáltatások:

| Termék, szolgáltatás vagy szolgáltatás | Server-Side Service-Managed kulcs használatával   | Server-Side Customer-Managed kulcs használatával | Client-Side Client-Managed kulcs használatával  |
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
| Power BI                         | Yes                | Igen, RSA 4096-bit  | -                  |
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
| Azure Data Lake Store            | Igen                | Igen, RSA 2048-bit  | -                  |
| **Containers**                   |                    |                    |                    |
| Azure Kubernetes Service         | Igen                | Yes                | -                  |
| Container Instances              | Igen                | Yes                | -                  |
| Container Registry               | Igen                | Yes                | -                  |
| **Számítás**                      |                    |                    |                    |
| Virtual Machines                 | Igen                | Yes                | -                  |
| Virtuálisgép-méretezési csoport        | Igen                | Igen                | -                  |
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
| Azure SQL Database               | Igen                | Igen, RSA 3072-bit  | Yes                |
| MariaDB Azure SQL Database   | Yes                | -                  | -                  |
| MySQL-Azure SQL Database     | Igen                | Yes                | -                  |
| Azure SQL Database PostgreSQL-hez | Igen               | Yes                | -                  |
| Azure Synapse Analytics          | Yes                | Igen, RSA 3072-bit  | -                  |
| SQL Server Stretch Database      | Yes                | Igen, RSA 3072-bit  | Yes                |
| Table Storage                    | Igen                | Igen                | Igen                |
| Azure Cosmos DB                  | Igen                | Yes                | -                  |
| Azure Databricks                 | Igen                | Yes                | -                  |
| Azure Database Migration Service | Yes                | N/A\*              | -                  |
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
| **IoT szolgáltatások**                 |                    |                    |                    |
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
| Ultra Disk Storage               | Igen                | Yes                | -                  |
| Felügyelt Disk Storage             | Igen                | Yes                | -                  |
| File Storage                     | Igen                | Yes                | -                  |
| Fájl Premium Storage             | Igen                | Yes                | -                  |
| File Sync                         | Igen                | Yes                | -                  |
| Queue Storage                    | Igen                | Igen                | Yes                |
| Avere vFXT                       | Yes                | -                  | -                  |
| Azure Cache for Redis            | Yes                | N/A\*              | -                  |
| Azure NetApp Files               | Igen                | Yes                | -                  |
| Archive Storage                  | Igen                | Yes                | -                  |
| StorSimple                       | Igen                | Igen                | Yes                |
| Azure Backup                     | Igen                | Igen                | Yes                |
| Data Box                         | Igen                | -                  | Yes                |
| Data Box Edge                    | Igen                | Yes                | -                  |

\* Ez a szolgáltatás nem tart fenn adatmegőrzést. Az átmeneti gyorsítótárak, ha vannak ilyenek, Microsoft-kulccsal vannak titkosítva.

\*\* Ez a szolgáltatás támogatja az adattárolást a saját Key Vaultban, a Storage-fiókban vagy más olyan adatmegőrzési szolgáltatásban, amely már támogatja a Server-Side titkosítást Customer-Managed kulccsal.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg [, hogyan használják a titkosítást az Azure-ban](encryption-overview.md).
- Ismerje meg, hogyan használja az Azure a [kettős titkosítást](double-encryption.md) az adattitkosítással kapcsolatos fenyegetések enyhítésére.
