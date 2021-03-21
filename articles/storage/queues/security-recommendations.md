---
title: A Queue Storage biztonsági javaslatai
titleSuffix: Azure Storage
description: A Queue Storage biztonsági javaslatainak megismerése. Ennek az útmutatónak a megvalósítása a megosztott felelősségi modellben leírtak szerint biztosítja a biztonsági kötelezettségek teljesítését.
author: tamram
services: storage
ms.author: tamram
ms.date: 03/11/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: queues
ms.custom: security-recommendations
ms.openlocfilehash: db0e033adf553c25c6b7b401f8d0df1a2cd5995f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97592160"
---
# <a name="security-recommendations-for-queue-storage"></a>A Queue Storage biztonsági javaslatai

Ez a cikk a Queue Storage biztonsági javaslatait tartalmazza. A javaslatok megvalósítása a közös felelősségi modellben leírtaknak megfelelően segíti a biztonsági kötelezettségek teljesítését. További információ arról, hogy a Microsoft hogyan teljesíti a szolgáltatói felelősségeket, lásd: [a felhőalapú számítástechnika megosztott feladatai](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf).

A cikkben szereplő ajánlások némelyikét a Azure Security Center automatikusan nyomon követheti. A Azure Security Center az Azure-beli erőforrások védelmének első védelmi vonala. További információ a Azure Security Centerről: [Mi az Azure Security Center?](../../security-center/security-center-introduction.md)

Azure Security Center rendszeresen elemzi az Azure-erőforrások biztonsági állapotát az esetleges biztonsági rések azonosítása érdekében. Ezután javaslatokat tesz a megoldására. Azure Security Center javaslatokkal kapcsolatos további információkért lásd: [biztonsági javaslatok a Azure Security Centerban](../../security-center/security-center-recommendations.md).

## <a name="data-protection"></a>Adatvédelem

| Ajánlás | Megjegyzések | Security Center |
|-|----|--|
| A Azure Resource Manager telepítési modell használata | Hozzon létre új Storage-fiókokat a Azure Resource Manager üzembe helyezési modellel a fontos biztonsági fejlesztésekhez, beleértve a kiváló Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) és a naplózást, a Resource Manager-alapú üzembe helyezést és irányítást, a felügyelt identitásokhoz való hozzáférést, a Secrets Azure Key Vault elérését, valamint az Azure AD-alapú hitelesítést, valamint az Azure Storage-adatok és- Ha lehetséges, telepítse át a klasszikus üzemi modellt használó meglévő Storage-fiókokat a Azure Resource Manager használatára. További információ a Azure Resource Managerről: [Azure Resource Manager áttekintése](../../azure-resource-manager/management/overview.md). | - |
| A komplex veszélyforrások elleni védelem engedélyezése az összes Storage-fiókhoz | Az Azure Storage komplex veszélyforrások elleni védelme egy további biztonsági intelligenciát biztosít, amely szokatlan és potenciálisan ártalmas kísérleteket észlel a Storage-fiókok eléréséhez vagy kiaknázásához. A biztonsági riasztások Azure Security Center, ha a tevékenységben észlelt rendellenességek bekövetkeznek, és e-mailben is elküldik az előfizetési rendszergazdáknak, a gyanús tevékenységek részleteivel és a fenyegetések kivizsgálására és elhárítására vonatkozó javaslatokkal kapcsolatban. További információ: [Az Azure Storage komplex veszélyforrások elleni védelme](../common/azure-defender-storage-configure.md). | [Igen](../../security-center/security-center-remediate-recommendations.md) |
| Közös hozzáférésű aláírási (SAS-) tokenek korlátozása csak HTTPS-kapcsolatokra | HTTPS megkövetelése, ha az ügyfél SAS-jogkivonattal fér hozzá a várólista-adatforgalomhoz, segít csökkenteni a lehallgatás kockázatát. További információ: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](../common/storage-sas-overview.md). | - |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Ajánlás | Megjegyzések | Security Center |
|-|----|--|
| A Azure Active Directory (Azure AD) használata az üzenetsor-adatelérés engedélyezéséhez | Az Azure AD kiváló biztonságot és könnyű használatot biztosít a megosztott kulcsos engedélyezéshez a kérelmek Queue Storage való engedélyezéséhez. További információ: az [Azure-blobok és-várólisták hozzáférésének engedélyezése Azure Active Directory használatával](../common/storage-auth-aad.md). | - |
| Ne feledje, hogy az Azure AD rendszerbiztonsági tag Azure RBAC | Ha egy szerepkört egy felhasználóhoz, csoporthoz vagy alkalmazáshoz rendel, a rendszerbiztonsági tag csak azokat az engedélyeket adja meg, amelyek szükségesek a feladataik elvégzéséhez. Az erőforrásokhoz való hozzáférés korlátozása segít megakadályozni az adataival való szándékos és rosszindulatú visszaéléseket. | - |
| A fiók-hozzáférési kulcsok biztonságossá tétele Azure Key Vault | A Microsoft azt javasolja, hogy az Azure AD használatával engedélyezze az Azure Storage-ba irányuló kéréseket. Ha azonban megosztott kulcsos hitelesítést kell használnia, akkor a fiók kulcsainak védelmét Azure Key Vault. A kulcsokat a Key vaultból is lekérheti futásidőben, az alkalmazással való mentés helyett. | - |
| A fiók kulcsai rendszeres újragenerálása | A fiókok kulcsainak rendszeres elforgatása csökkenti annak kockázatát, hogy az adatok rosszindulatú szereplőkkel legyenek kitéve. | - |
| Tartsa szem előtt a legalacsonyabb jogosultsági szintű rendszerbiztonsági tag számára az engedélyek SAS-hez való hozzárendelését. | SAS létrehozásakor csak azokat az engedélyeket kell megadnia, amelyekre az ügyfélnek szüksége van a funkció végrehajtásához. Az erőforrásokhoz való hozzáférés korlátozása segít megakadályozni az adataival való szándékos és rosszindulatú visszaéléseket. | - |
| Visszavonási tervvel kell rendelkeznie minden olyan SAS számára, amelyet az ügyfelek számára ad ki | Ha egy SAS biztonsága sérül, a lehető leghamarabb vissza kell vonnia az SAS-t. Ha vissza szeretne vonni egy felhasználói delegálási SAS-t, vonja vissza a felhasználói delegálási kulcsot, hogy gyorsan érvénytelenítse a kulcshoz társított összes aláírást. Egy tárolt hozzáférési szabályzattal társított szolgáltatási SAS visszavonásához törölheti a tárolt hozzáférési szabályzatot, átnevezheti a szabályzatot, vagy módosíthatja annak lejárati idejét egy múltbeli időpontra. További információ: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](../common/storage-sas-overview.md).  | - |
| Ha egy szolgáltatás SAS-je nem egy tárolt hozzáférési szabályzathoz van társítva, akkor a lejárati időt állítsa egy órára vagy kevesebbre. | Nem lehet visszavonni egy olyan szolgáltatáshoz tartozó SAS-t, amely nincs hozzárendelve egy tárolt hozzáférési szabályzathoz. Emiatt a lejárati időt úgy kell korlátozni, hogy az SAS egy órán vagy kevesebb ideig érvényes legyen. | - |

## <a name="networking"></a>Hálózatkezelés

| Ajánlás | Megjegyzések | Security Center |
|-|----|--|
| Konfigurálja Transport Layer Security (TLS) minimálisan szükséges verzióját a Storage-fiókhoz.  | Megkövetelheti, hogy az ügyfelek a TLS egy biztonságosabb verzióját használják az Azure Storage-fiókra irányuló kérések elvégzéséhez, ha a TLS minimális verzióját konfigurálja ehhez a fiókhoz. További információ: [Transport Layer Security (TLS) minimálisan szükséges verziójának konfigurálása Storage-fiókhoz](../common/transport-layer-security-configure-minimum-version.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)| - |
| A **biztonságos átvitel szükséges** beállításának engedélyezése az összes Storage-fiókban | Ha engedélyezi a **biztonságos átvitel szükséges** beállítást, a Storage-fiókra irányuló összes kérést biztonságos kapcsolatokon keresztül kell végrehajtani. A HTTP-n keresztül végrehajtott kérelmek sikertelenek lesznek. További információ: [biztonságos átvitel megkövetelése az Azure Storage-ban](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). | [Igen](../../security-center/security-center-remediate-recommendations.md) |
| Tűzfalszabályok engedélyezése | A tűzfalszabályok konfigurálásával korlátozza a Storage-fiókhoz való hozzáférést a megadott IP-címekről vagy tartományokból származó kérelmekre, vagy egy Azure-beli virtuális hálózat (VNet) alhálózatának listájára. A tűzfalszabályok konfigurálásával kapcsolatos további információkért lásd: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). | - |
| Megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése a Storage-fiókhoz | Ha bekapcsolja a tűzfalszabályok bekapcsolását a Storage-fiókhoz, az alapértelmezés szerint letiltja a bejövő adatkéréseket, kivéve, ha a kérelmek egy Azure-VNet vagy engedélyezett nyilvános IP-címről származó szolgáltatásból származnak. A letiltott kérések közé tartoznak a más Azure-szolgáltatások, a Azure Portal, a naplózási és a metrikai szolgáltatások, valamint így tovább. A többi Azure-szolgáltatástól érkező kéréseket engedélyezheti egy kivétel hozzáadásával, amely lehetővé teszi, hogy a megbízható Microsoft-szolgáltatások hozzáférjenek a Storage-fiókhoz. A megbízható Microsoft-szolgáltatások kivételének hozzáadásával kapcsolatos további információkért lásd: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).| - |
| Privát végpontok használata | Egy privát végpont egy magánhálózati IP-címet rendel hozzá az Azure-VNet a Storage-fiókhoz. A VNet és a Storage-fiók közötti összes forgalmat a privát kapcsolaton keresztül biztosítja. A privát végpontokkal kapcsolatos további információkért lásd: [magánhálózati kapcsolat létrehozása egy Azure Private-végponton keresztül a Storage-fiókhoz](../../private-link/tutorial-private-endpoint-storage-portal.md). | - |
| VNet szolgáltatásbeli címkék használata | A szolgáltatás címkéje egy adott Azure-szolgáltatás IP-címeinek egy csoportját jelöli. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával. További információ az Azure Storage által támogatott szolgáltatási címkékről: [Azure-szolgáltatás címkék – áttekintés](../../virtual-network/service-tags-overview.md). A következő témakörben talál egy oktatóanyagot, amely bemutatja, hogyan használhatók a szolgáltatási címkék a kimenő hálózati szabályok létrehozásához: a [hozzáférés korlátozása a Pásti erőforrásaihoz](../../virtual-network/tutorial-restrict-network-access-to-resources.md). | - |
| Bizonyos hálózatokhoz való hálózati hozzáférés korlátozása | A hozzáférést igénylő ügyfelek hálózati hozzáférésének korlátozása csökkenti az erőforrások hálózati támadásoknak való kitettségét. | [Igen](../../security-center/security-center-remediate-recommendations.md) |

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

| Ajánlás | Megjegyzések | Security Center |
|-|----|--|
| A kérések engedélyezésének nyomon követése | Az Azure Storage naplózásának engedélyezése az Azure Storage-ba irányuló kérelmek engedélyezésének nyomon követésére. A naplók azt jelzik, hogy egy kérelem névtelenül történt-e egy OAuth 2,0-token használatával, megosztott kulcs használatával vagy közös hozzáférésű aláírás (SAS) használatával. További információ: az [Azure Storage Analytics naplózása](../common/storage-analytics-logging.md). | - |

## <a name="next-steps"></a>Következő lépések

- [Az Azure biztonsági dokumentációja](../../security/index.yml)
- [Biztonságos fejlesztői dokumentáció](../../security/develop/index.yml).
