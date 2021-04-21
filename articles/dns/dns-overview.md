---
title: Mi az Azure DNS?
description: A Microsoft Azure DNS-szolgáltatásának áttekintése. Saját tartományt üzemeltethet a Microsoft Azure-on.
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 4/20/2021
ms.author: rohink
ms.openlocfilehash: a04e9aa2a0c342ff86b8f603c02a78952c55af88
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817647"
---
# <a name="what-is-azure-dns"></a>Mi az Azure DNS?

Az Azure DNS egy üzemeltetési szolgáltatás DNS-tartományokhoz, amely a Microsoft Azure infrastruktúrájával történő névfeloldást nyújt. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti.

Az Azure DNS-t nem használhatja tartománynév vásárlására. Egy éves díjért tartománynevet vásárolhat tartománynév App Service vagy egy külső tartománynév-regisztráló használatával. [](../app-service/manage-custom-dns-buy-domain.md#buy-an-app-service-domain) A tartományokat ezután üzemeltetheti az Azure DNS-ben rekordok kezeléséhez. További információ: [Tartomány delegálása a Azure DNS.](dns-domain-delegation.md)

Az Azure DNS a következő funkciókat tartalmazza.

## <a name="reliability-and-performance"></a>Megbízhatóság és teljesítmény

Az Azure DNS-beli DNS-tartományok az Azure globális DNS-névkiszolgálói hálózatán üzemelnek. Az Azure DNS anycast hálózatkezelést használ. A tartomány gyors teljesítménye és magas rendelkezésre állása érdekében minden DNS-lekérdezésre a legközelebbi elérhető DNS-kiszolgáló válaszol.

## <a name="security"></a>Biztonság

 Az Azure DNS az Azure Resource Managerre épül, amely többek között a következő szolgáltatásokat nyújtja:

* [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../azure-resource-manager/management/overview.md) annak szabályozása érdekében, hogy ki férhet hozzá adott műveletekhez a szervezet számára.

* [Tevékenységnaplók](../azure-resource-manager/management/overview.md) – nyomon követheti, hogy a vállalat felhasználói hogyan módosították az erőforrásokat, vagy megkeresheti a hibákat a hibaelhárításkor.

* [Erőforrás-zárolás](../azure-resource-manager/management/lock-resources.md) – zárolhat egy előfizetést, erőforráscsoportot vagy erőforrást. A zárolás megakadályozza, hogy a cég vagy intézmény felhasználói véletlenül töröljék vagy módosítsák a kritikus erőforrásokat.

További információkat a [DNS-zónák és -rekordok védelmével](dns-protect-zones-recordsets.md) kapcsolatos témakörben olvashat. 

## <a name="dnssec"></a>Dnssec

Azure DNS nem támogatja a DNSSEC-et. A legtöbb esetben a HTTPS/TLS alkalmazáson keresztüli konzisztens használatával csökkentheti a DNSSEC iránti kérelmet. Ha a DNSSEC kritikus követelmény a DNS-zónák számára, ezeket a zónákat külső DNS-üzemeltetési szolgáltatókkal is gazdagépként használhatja.

## <a name="ease-of-use"></a>Egyszerű használat

 Az Azure DNS képes kezelni az Azure-szolgáltatások DNS-rekordjait, és DNS-t biztosít a külső erőforrásoknak. Az Azure DNS integrálva van az Azure Portallal, és ugyanazokat a hitelesítő adatokat, számlázási információkat és támogatási szerződést használja, mint a többi Azure-szolgáltatás. 

Az Azure DNS díjszabása az Azure-ban üzemeltetett DNS-zónák és a fogadott DNS-lekérdezések számán alapul. A díjszabással kapcsolatos további információkért tekintse meg [az Azure DNS díjszabásával](https://azure.microsoft.com/pricing/details/dns/) kapcsolatos cikket.

A tartományok és a rekordok az Azure Portal, az Azure PowerShell-parancsmagok és a platformfüggetlen Azure CLI segítségével kezelhetőek. Az automatikus DNS-kezelést igénylő alkalmazások a REST API és az SDK-k segítségével integrálhatók a szolgáltatásban.

## <a name="customizable-virtual-networks-with-private-domains"></a>Saját tartományokkal rendelkező, testreszabható virtuális hálózatok

Azure DNS a privát DNS-tartományokat is támogatja. Ez a szolgáltatás lehetővé teszi, hogy a jelenleg elérhető, Azure által biztosított nevek helyett egyéni tartományneveket használjon a virtuális magánhálózatokon.

További információkat az [Azure DNS privát tartományokhoz való használatát](private-dns-overview.md) ismertető cikkben olvashat.

## <a name="alias-records"></a>Aliasrekordok

Az Azure DNS az aliasrekord-készletek használatát is támogatja. Aliasrekord-készlet használatával hivatkozhat egy Azure-erőforrásra, például egy nyilvános Azure IP-címre, egy Azure Traffic Manager-profilra vagy egy Azure Content Delivery Network- (CDN-) végpontra. Ha a mögöttes erőforrás IP-címe módosul, az aliasrekord-készlet a DNS feloldása során zökkenőmentesen frissíti magát. Az aliasrekord-készlet a szolgáltatáspéldányra mutat, a szolgáltatáspéldány pedig egy IP-címmel van társítva.

Most már a csúcs- vagy csupasz tartományt egy Traffic Manager profilra vagy CDN-végpontra is mutathatja aliasrekord használatával. Például: contoso.com.

További információért lásd: [Az Azure DNS-aliasrekordok áttekintése](dns-alias.md).

## <a name="next-steps"></a>Következő lépések

* További tudnivalókat a DNS-zónákról és -rekordokról [a DNS-zónák és -rekordok áttekintésében](dns-zones-records.md) olvashat.

* Az Azure DNS-ben a zónák létrehozásáról a [DNS-zónák létrehozását](./dns-getstarted-portal.md) ismertető témakörben olvashat.

* Az Azure DNS-sel kapcsolatos gyakori kérdésekért lásd az [Azure DNS gyakori kérdéseket](dns-faq.md).
