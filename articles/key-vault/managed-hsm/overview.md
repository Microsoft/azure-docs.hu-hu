---
title: Az Azure Managed HSM áttekintése – Azure Managed HSM-| Microsoft Docs
description: Az Azure Managed HSM egy felhőszolgáltatás, amely a felhőalkalmazások titkosítási kulcsait védi.
services: key-vault
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: overview
ms.custom: mvc
ms.date: 04/01/2021
ms.author: mbaldwin
author: msmbaldwin
ms.openlocfilehash: a9df83535bf0ee7a6c484a7682392d2ad0ef59f7
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815323"
---
# <a name="what-is-azure-key-vault-managed-hsm"></a>Az Azure Key Vault Managed HSM bemutatása

Azure Key Vault Managed HSM egy teljes körűen felügyelt, magas rendelkezésre állékonyságú, egybérlős, szabványnak megfelelő felhőszolgáltatás, amely lehetővé teszi a felhőalkalmazások titkosítási kulcsának védelmét a **FIPS 140-2 3.** szintje szerint ellenőrzött HSM-ekkel. Díjszabási információkért tekintse meg a Managed HSM Pools (Felügyelt HSM-készletek) című szakaszt [Azure Key Vault oldalon.](https://azure.microsoft.com/pricing/details/key-vault/) 

## <a name="why-use-managed-hsm"></a>Miért érdemes a Managed HSM-et használni?

### <a name="fully-managed-highly-available-single-tenant-hsm-as-a-service"></a>Teljes körűen felügyelt, magas rendelkezésre áll, egybérlős HSM szolgáltatásként

- **Teljes körűen felügyelt:** A HSM-kiépítést, konfigurálást, javítást és karbantartást a szolgáltatás kezeli. 
- **Magas rendelkezésre állású és** zónaredigens (ahol a rendelkezésre állási zónák támogatottak): Minden HSM-fürt több HSM-partícióból áll, amelyek több rendelkezésre állási zónára is ki vannak ásva. Ha a hardver meghibásodik, a HSM-fürt tagpartíciói automatikusan át lesznek telepítve a kifogástalan állapotú csomópontokra.
- **Egybérlős:** Minden felügyelt HSM-példány egyetlen ügyfél számára van kiosztva, és több HSM-partícióból áll. Minden HSM-fürt külön ügyfélspecifikus biztonsági tartományt használ, amely kriptográfiailag elkülöníti az egyes ügyfelek HSM-fürtöt.


### <a name="access-control-enhanced-data-protection--compliance"></a>Hozzáférés-vezérlés, továbbfejlesztett adatvédelmi & megfelelőség

- **Központosított kulcskezelés:** Egy helyen kezelheti a kritikus fontosságú, nagy értékű kulcsokat a szervezetben. A kulcsonkénti részletes engedélyekkel szabályozhatja az egyes kulcsokhoz való hozzáférést a "legkevésbé emelt szintű hozzáférés" elve alapján.
- **Izolált** hozzáférés-vezérlés: A managed HSM "helyi RBAC" hozzáférés-vezérlési modellje lehetővé teszi a kijelölt HSM-fürt rendszergazdái számára, hogy teljes körű vezérléssel tudjanak kezelni azokat a HSM-eket, amelyek még a felügyeleti csoport, az előfizetés vagy az erőforráscsoport rendszergazdái számára sem bírálhatóak felül.
- **FIPS 140-2 3.** szint szerint ellenőrzött HSM-ek: Az adatok védelme és a megfelelőségi követelmények teljesítéséhez a FIPS ((Federal Information Protection Standard)) 140-2 3. szintje szerint ellenőrzött HSM-ekkel. A felügyelt HSM-ek a Fogl LiquidSecurity HSM-adaptereket használják.
- **Monitor és naplózás:** teljes mértékben integrálva van az Azure Monitorral. Az összes tevékenység teljes naplóit a Azure Monitor. Az Azure Log Analytics használata elemzéshez és riasztáshoz.
- **Adattároló hely:** A managed HSM nem tárol/feldolgoz ügyféladatokat azon a régión kívül, ahol az ügyfél üzembe helyezheti a HSM-példányt.

### <a name="integrated-with-azure-and-microsoft-paassaas-services"></a>Integrálva van az Azure-ral és a Microsoft PaaS-/SaaS-szolgáltatásokkal 

- Hozzon létre (vagy importáljon [BYOK](hsm-protected-keys-byok.md)használatával) kulcsokat, és használja őket az olyan Azure-szolgáltatásokban [tárolt](../../azure-sql/database/transparent-data-encryption-byok-overview.md)adatok titkosítására, mint az [Azure Storage,](../../storage/common/customer-managed-keys-overview.md)Azure SQL , [Azure Information Protection.](/azure/information-protection/byok-price-restrictions)

### <a name="uses-same-api-and-management-interfaces-as-key-vault"></a>Ugyanazt az API-t és felügyeleti felületet használja, mint Key Vault

- Könnyedén átveheti a tárolót (több-bérlős) tárolót használó meglévő alkalmazásokat felügyelt HSM-ek használatára.
- Használja ugyanazt az alkalmazásfejlesztési és üzembe helyezési mintákat az összes alkalmazáshoz a használt kulcskezelési megoldástól függetlenül: több-bérlős tárolók vagy egybérlős felügyelt HSM-ek

### <a name="import-keys-from-your-on-premise-hsms"></a>Kulcsok importálása a saját HSM-ről

- HSM által védett kulcsok létrehozása a saját hsm-környezetben, és azok biztonságos importálása a Managed HSM-be

## <a name="next-steps"></a>Következő lépések
- Lásd: [Rövid útmutató: Felügyelt HSM](quick-create-cli.md) kiépítése és aktiválása az Azure CLI használatával felügyelt HSM létrehozásához és aktiválásához
- Lásd: [Ajánlott eljárások a Azure Key Vault HSM-hez](best-practices.md)
