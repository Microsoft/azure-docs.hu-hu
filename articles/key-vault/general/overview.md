---
title: Azure Key Vault áttekintése – Azure Key Vault
description: A Azure Key Vault egy biztonságos titkos kulcsokat tároló eszköz, amely a hardveres biztonsági modulok által támogatott titkokat, kulcsokat és tanúsítványokat biztosít.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.custom: mvc
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 3e6ed6bc8ba426a981b7a2390e7d00d9c3b1e785
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643458"
---
# <a name="about-azure-key-vault"></a>Információk az Azure Key Vaultról

A Azure Key Vault a következő problémák megoldásában nyújt segítséget:

- **Titkos kódok kezelése** – Az Azure Key Vaulttal biztonságosan tárolhatók a jogkivonatok, jelszavak, tanúsítványok, API-kulcsok és egyéb titkos kulcsok, valamint szigorúan szabályozható az ezekhez való hozzáférés
- **Kulcskezelés** – Az Azure Key Vault kulcskezelési megoldásként is használható. Az Azure Key Vaulttal egyszerűen létrehozhatja és vezérelheti az adatok titkosításához használt titkosítási kulcsokat. 
- A **tanúsítványkezelő** – Azure Key Vault egy olyan szolgáltatás is, amely lehetővé teszi a nyilvános és magánhálózati Transport Layer Security/SSL (TLS/SSL) tanúsítványok egyszerű üzembe helyezését, kezelését és telepítését az Azure-ral és a belső csatlakoztatott erőforrásokkal való használatra.

A Azure Key Vault két szolgáltatási szintet tartalmaz: standard, amely egy szoftver kulcsával titkosítja, és egy prémium szintű csomag, amely tartalmazza a hardveres biztonsági modul (HSM) által védett kulcsokat. A standard és a prémium szint közötti összehasonlítás megtekintéséhez tekintse meg a [Azure Key Vault díjszabási oldalát](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="why-use-azure-key-vault"></a>Miért érdemes az Azure Key Vaultot használni?

### <a name="centralize-application-secrets"></a>Titkos alkalmazáskulcsok központosítása

A titkos alkalmazáskulcsok tárolásának központosítása az Azure Key Vaultban lehetővé teszi azok elosztásának irányítását. A Key Vault nagymértékben csökkenti a veszélyét annak, hogy a titkos kulcsok véletlenül kiszivárogjanak. A Key Vault használatával az alkalmazásfejlesztőknek többé nem kell az alkalmazásban tárolniuk a biztonsági információkat. Nem kell a biztonsági adatokat tárolni az alkalmazásokban, így nem kell ezt az információt a kód részévé tenni. Tegyük fel például, hogy egy alkalmazásnak egy adatbázishoz kell csatlakoznia. Ahelyett, hogy az alkalmazás kódjában tárolja a kapcsolatok karakterláncát, biztonságosan tárolhatja Key Vaultban.

Az alkalmazások az URI-k használatával biztonságosan érhetik el a szükséges információkat. Ezek az URI-k lehetővé teszik az alkalmazások számára a titkos kód adott verzióinak lekérését. Nem kell egyéni kódot írnia, hogy megvédje a Key Vaultban tárolt titkos adatokat.

### <a name="securely-store-secrets-and-keys"></a>A titkos kulcsok és a kulcsok biztonságos tárolása

A kulcstartóhoz való hozzáférés előtt a hívónak (felhasználónak vagy alkalmazásnak) megfelelő hitelesítésre és engedélyezésre van szüksége. A hitelesítés a hívó azonosítását szolgálja, az engedélyezés pedig meghatározza, milyen műveletek elvégzésére jogosult.

A hitelesítés az Azure Active Directory használatával történik. Az engedélyezés az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) vagy Key Vault hozzáférési szabályzat használatával végezhető el. Az Azure RBAC a tárolók kezelésével és a kulcstartó-hozzáférési szabályzattal való együttműködéshez használatos, amikor egy tárban tárolt adathozzáférésre próbál hozzáférni.

Az Azure Key Vaultok szoftveres védelemmel vagy a Azure Key Vault Premium csomaggal, hardveres biztonsági modulok (HSM-EK) által védettek lehetnek. A szoftveres védelemmel ellátott kulcsokat, titkokat és tanúsítványokat az Azure biztosítja az iparági szabványnak megfelelő algoritmusok és a kulcsok hosszának biztosításával.  Olyan helyzetekben, ahol további garanciára van szükség, a HSM olyan kulcsokat importálhat vagy generálhat, amelyek soha nem hagyják el a HSM-határt. A Azure Key Vault nCipher HSM használ, amelyek a Federal Information Processing Standards (FIPS) 140-2 2. szint ellenőrzése alatt állnak. A nCipher-eszközök segítségével áthelyezhet egy kulcsot a HSM-ből Azure Key Vaultba.

Végül pedig az Azure Key Vault kialakításából adódóan a Microsoft nem tekintheti meg és nem fejtheti vissza az adatokat.

### <a name="monitor-access-and-use"></a>A hozzáférés és használat monitorozása

Miután létrehozott néhány kulcstartót, monitorozhatja, hogyan és mikor férnek hozzá a kulcsokhoz és a titkos kulcsokhoz. A tevékenységek figyeléséhez engedélyezheti a naplózást a tárolók számára. Az Azure Key Vaultot beállíthatja az alábbiak elvégzésére:

- Archiválás tárfiókba
- Streamelés eseményközpontba
- Küldje el a naplókat Azure Monitor naplókba.

Engedélyekkel rendelkezik a naplók felett, és meg is védheti őket a hozzáférés korlátozásával, illetve törölheti azokat a naplókat, amelyekre már nincs szüksége.

### <a name="simplified-administration-of-application-secrets"></a>A titkos alkalmazáskulcsok egyszerűsített adminisztrációja

Értékes adatok tárolásakor el kell végeznie néhány lépést. A biztonsági információknak védettnek kell lenniük, meg kell tartaniuk egy életciklust, és a szolgáltatásnak erősen elérhetőnek kell lennie. Azure Key Vault leegyszerűsíti az alábbi követelmények teljesítésének folyamatát:

- A hardveres biztonsági modulok belső ismeretének szükségessége.
- Horizontális felskálázás a szervezet használati feladatainak kielégítése érdekében.
- A kulcstartója tartalmát egy régión belül és egy másodlagos régióba replikálja. Az adatreplikáció biztosítja a magas rendelkezésre állást, és elveszi a rendszergazda által a feladatátvétel elindításához szükséges bármilyen művelet szükségességét.
- Standard Azure felügyeleti lehetőségeket biztosít a Portal, az Azure CLI és a PowerShell segítségével.
- Automatizálja a nyilvános hitelesítésszolgáltatóktól vásárolt tanúsítványokon elvégzett egyes műveleteket, például a regisztrálást és a megújítást.

Továbbá az Azure Key Vault-kulcstartók lehetővé teszik a titkos alkalmazáskulcsok elkülönítését. Az alkalmazások csak azt a tárolót érhetik el, amelyhez hozzáféréssel rendelkeznek, és csak meghatározott műveletekre korlátozhatók. Alkalmazásonként egy Azure Key Vault-kulcstárolót hozhat létre, és a benne tárolt titkos kulcsok használatát csak egy adott alkalmazásra és fejlesztői csapatra korlátozhatja.

### <a name="integrate-with-other-azure-services"></a>Integráció más Azure-szolgáltatásokkal

Az Azure-beli Biztonságos tár Key Vault a következő forgatókönyvek egyszerűsítésére lett felhasználva:
-  [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md)
-  Az [Always encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) és [TRANSZPARENS ADATTITKOSÍTÁS](/sql/relational-databases/security/encryption/transparent-data-encryption) funkció az SQL serverben és a Azure SQL Database
- [Azure app Service](/azure/app-service/configure-ssl-certificate).

A Key Vault integrálható tárfiókokkal, eseményközpontokkal és a naplóelemzéssel.

## <a name="next-steps"></a>Következő lépések

- További információ a [kulcsokról, a titkokról és a tanúsítványokról](about-keys-secrets-certificates.md)
- [Rövid útmutató: Azure Key Vault-kulcstartó létrehozása a parancssori felülettel](../secrets/quick-create-cli.md)
- [Hitelesítés, kérések és válaszok](../general/authentication-requests-and-responses.md)
- [Key Vault fejlesztői útmutató](../general/developers-guide.md)
