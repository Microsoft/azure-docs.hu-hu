---
title: Azure Key Vault biztonsági áttekintés
description: A biztonsági szolgáltatások és az ajánlott eljárások áttekintése Azure Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: fe88933049ad39de57f879789e8c1b86ed7a54f5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819390"
---
# <a name="azure-key-vault-security"></a>Az Azure Key Vault biztonsága

Azure Key Vault a titkosítási kulcsok és titkos kulcsok (például tanúsítványok, kapcsolati sztringek és jelszavak) védelmét a felhőben. Bizalmas és üzleti szempontból kritikus adatok tárolásakor azonban lépéseket kell tenni a tárolók és a bennük tárolt adatok biztonságának maximalizálása érdekében.

Ez a cikk áttekintést nyújt a biztonsági szolgáltatásokról és az Azure Key Vault.

> [!NOTE]
> A biztonsági javaslatok átfogó listájáért Azure Key Vault tekintse meg a biztonsági alapkonfigurációt [a Azure Key Vault.](security-baseline.md)

## <a name="network-security"></a>Hálózati biztonság

Ha megadja, hogy mely IP-címek férnek hozzájuk, csökkentheti a tárolók kitettségét. A virtuális hálózati szolgáltatásvégpontjai Azure Key Vault lehetővé teszik egy adott virtuális hálózathoz való hozzáférés korlátozását. A végpontok emellett lehetővé teszik az IPv4-címtartományok listájához való hozzáférés korlátozását is. A kulcstartóhoz ezen forrásokon kívülről csatlakozó bármely felhasználónak nincs hozzáférése.  Részletes információ: Virtuális [hálózati szolgáltatásvégpont a Azure Key Vault](overview-vnet-service-endpoints.md)

A tűzfalszabályok életbe lépését követően a felhasználók csak akkor olvashatnak adatokat a Key Vault, ha a kéréseik engedélyezett virtuális hálózatokról vagy IPv4-címtartományból származnak. Ez vonatkozik a Key Vault hozzáférése Azure Portal. Bár a felhasználók megkereshetnek egy kulcstartót a Azure Portal, előfordulhat, hogy nem tudják listába sorolni a kulcsokat, titkos kulcsokat vagy tanúsítványokat, ha az ügyfélszámítógépük nem szerepel az engedélyezett listában. Az implementáció lépéseiért [lásd: Azure Key Vault tűzfalak és virtuális hálózatok konfigurálása](network-security.md)

Azure Private Link Szolgáltatás lehetővé teszi, hogy a virtuális Azure Key Vault privát végponton keresztül hozzáférjen az azure-Azure Key Vault és az Azure által üzemeltetett ügyfél-/partnerszolgáltatásokhoz. Az Azure privát végpontok olyan hálózati adapterek, amelyek privát módon és biztonságosan csatlakoztatják a virtuális Azure Private Link. A privát végpont a virtuális hálózat egyik magánhálózati IP-címét használja, így hatékonyan behozza a szolgáltatást a virtuális hálózatba. A szolgáltatás felé minden forgalom átirányítható a privát végponton keresztül, így nincs szükség átjárókra, NAT-eszközökre, ExpressRoute- vagy VPN-kapcsolatokra vagy nyilvános IP-címekre. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Csatlakozhat egy Azure-erőforráspéldányhoz, így a legmagasabb szintű részletességet biztosít a hozzáférés-vezérlésben.  Az implementáció lépéseiért lásd: [Key Vault integrálása a Azure Private Link](private-link-service.md)

## <a name="tls-and-https"></a>TLS és HTTPS

- A Key Vault (adatsík) egy több-bérlős kiszolgáló. Ez azt jelenti, hogy a különböző ügyfelek kulcstartói ugyanazt a nyilvános IP-címet oszthatják meg. Az elkülönítés érdekében minden HTTP-kérés hitelesítése és hitelesítése a többi kéréstől függetlenül történik.
- A biztonsági rések jelentése érdekében azonosíthatja a TLS régebbi verzióit, de mivel a nyilvános IP-cím meg van osztva, a Key Vault szolgáltatás csapata nem tilthatja le a TLS régi verzióit az egyes kulcstartókhoz az átviteli szinten.
- A HTTPS protokoll lehetővé teszi, hogy az ügyfél részt vegyen a TLS-egyeztetésben. **Az ügyfelek kikényszerítheti** a TLS legújabb verzióját, és amikor egy ügyfél ezt teszi, a teljes kapcsolat a megfelelő szintű védelmet fogja használni. Az, Key Vault továbbra is támogatja a régebbi TLS-verziókat, nem rontja az újabb TLS-verziókat használó kapcsolatok biztonságát.
- A TLS protokoll ismert biztonsági rései ellenére nincs olyan ismert támadás, amely lehetővé tenné egy rosszindulatú ügynök számára, hogy bármilyen információt kinyerje a kulcstartóból, amikor a támadó biztonsági réseket tároló TLS-verzióval kezdeményez kapcsolatot. A támadónak így is hitelesítenie és hitelesítenie kell magát, és amíg a megbízható ügyfelek mindig a legutóbbi TLS-verziókhoz csatlakoznak, addig a hitelesítő adatok nem szivárogtak ki a régi TLS-verziók biztonsági réseiből.

## <a name="identity-management"></a>Identitáskezelés

Amikor létrehoz egy kulcstartót egy Azure-előfizetésben, az automatikusan társítva lesz az előfizetés Azure AD-bérlőjéhez. Az Azure AD-nek hitelesítenie kell bárkit, aki tartalmat próbál kezelni vagy lekérni egy tárolóból. Az alkalmazások mindkét esetben három Key Vault férhetnek hozzá:

- **Csak alkalmazás:** Az alkalmazás egy szolgáltatásnév vagy felügyelt identitás. Ez az identitás a leggyakoribb forgatókönyv olyan alkalmazások esetében, amelyeknek rendszeres időközönként hozzá kell férni a key vaultból származó tanúsítványokhoz, kulcsokhoz vagy titkos kulcsokhoz. Ahhoz, hogy ez a forgatókönyv működjön, az alkalmazás fájlját meg kell adni a hozzáférési szabályzatban, és a nem lehet megadva, vagy a következőnek `objectId` `applicationId` kell lennie:  `null` .
- **Csak felhasználó:** A felhasználó a bérlőben regisztrált bármely alkalmazásból hozzáfér a kulcstartóhoz. Ilyen típusú hozzáférés például a Azure PowerShell és a Azure Portal. Ahhoz, hogy ez a forgatókönyv működjön, a felhasználó felhasználóját meg kell adni a hozzáférési szabályzatban, és a nem lehet megadva, vagy a következőnek `objectId` `applicationId` kell lennie:  `null` .
- **Application-plus-user** (más néven összetett _identitás):_ A felhasználónak hozzá kell  férnie a kulcstartóhoz egy adott alkalmazásból, és az alkalmazásnak a felhasználó megszemélyesítéséhez a hitelesítési (OBO) folyamatot kell használnia. Ahhoz, hogy ez a forgatókönyv működjön, a és a értéket is meg kell `applicationId` `objectId` adni a hozzáférési szabályzatban. A `applicationId` azonosítja a szükséges alkalmazást, a pedig azonosítja a `objectId` felhasználót. Ez a lehetőség jelenleg nem érhető el az Adatsík Azure RBAC-hez.

Az alkalmazás minden típusú hozzáférés esetén az Azure AD-val hitelesíti magát. Az alkalmazás az alkalmazás [típusától](../../active-directory/develop/authentication-vs-authorization.md) függően bármilyen támogatott hitelesítési módszert használ. Az alkalmazás lekért egy jogkivonatot a síkon egy erőforráshoz a hozzáférés megadásához. Az erőforrás az Azure-környezeten alapuló végpont a felügyeleti vagy adatsíkon. Az alkalmazás a jogkivonatot használja, és REST API egy kérést a Key Vault. További tudnivalókért tekintse át a teljes [hitelesítési folyamatot.](../../active-directory/develop/v2-oauth2-auth-code-flow.md)

További részletekért lásd: A [Key Vault alapjai](authentication-fundamentals.md)

## <a name="key-vault-authentication-options"></a>Key Vault hitelesítési beállítások

Amikor létrehoz egy kulcstartót egy Azure-előfizetésben, az automatikusan társítva lesz az előfizetés Azure AD-bérlőjéhez. Mindkét síkon minden hívónak regisztrálnia kell ebben a bérlőben, és hitelesítenie kell magát a kulcstartó eléréséhez. Az alkalmazások mindkét esetben három Key Vault férhetnek hozzá:

- **Csak alkalmazás:** Az alkalmazás egy szolgáltatásnév vagy felügyelt identitás. Ez az identitás a leggyakoribb forgatókönyv olyan alkalmazások esetében, amelyek időnként tanúsítványokat, kulcsokat vagy titkos kulcsokat kell elérniük a kulcstartóból. Ahhoz, hogy ez a forgatókönyv működjön, az alkalmazás fájlját meg kell adni a hozzáférési házirendben, és a nem lehet megadva, vagy a következőnek `objectId` `applicationId` kell lennie:  `null` .
- **Csak felhasználó:** A felhasználó a bérlőben regisztrált bármely alkalmazásból hozzáfér a kulcstartóhoz. Ilyen típusú hozzáférés például a Azure PowerShell és a Azure Portal. Ahhoz, hogy ez a forgatókönyv működjön, a felhasználó felhasználóját meg kell adni a hozzáférési szabályzatban, és a nem lehet megadva, vagy a következőnek `objectId` `applicationId` kell lennie:  `null` .
- **Alkalmazás-plusz felhasználó** (más néven összetett _identitás):_ A felhasználónak hozzá kell  férnie a kulcstartóhoz egy adott alkalmazásból, és az alkalmazásnak a felhasználó megszemélyesítéséhez a hitelesítő (OBO) folyamatot kell használnia. Ahhoz, hogy ez a forgatókönyv működjön, a és a értéket is meg kell `applicationId` `objectId` adni a hozzáférési szabályzatban. A `applicationId` azonosítja a szükséges alkalmazást, a pedig a `objectId` felhasználót. Ez a lehetőség jelenleg nem érhető el az Azure RBAC (előzetes verzió) adatsíkhoz.

Az alkalmazás minden típusú hozzáférés esetén az Azure AD-val hitelesíti magát. Az alkalmazás az alkalmazás [típusától függően](../../active-directory/develop/authentication-vs-authorization.md) bármilyen támogatott hitelesítési módszert használ. Az alkalmazás lekért egy jogkivonatot a síkon egy erőforráshoz a hozzáférés megadásához. Az erőforrás az Azure-környezeten alapuló végpont a felügyeleti vagy adatsíkon. Az alkalmazás a jogkivonatot használja, és egy REST API küld a Key Vault. További tudnivalókért tekintse át a teljes [hitelesítési folyamatot.](../../active-directory/develop/v2-oauth2-auth-code-flow.md)

A két síkon való hitelesítés egyetlen mechanizmusának modelljének több előnye is van:

- A szervezetek központilag vezérelheti a hozzáférést a szervezet összes kulcstartója számára.
- Ha egy felhasználó távozik, azonnal elveszíti a szervezet összes kulcstartó-hozzáférését.
- A szervezetek az Azure AD beállításaival szabhatják testre a hitelesítést, például engedélyezhetik a többtényezős hitelesítést a további biztonság érdekében.

## <a name="access-model-overview"></a>Hozzáférési modell áttekintése

A kulcstartóhoz való hozzáférést két felületen  lehet vezérelni: a felügyeleti síkon és az **adatsíkon.** A felügyeleti síkon kezelheti magát Key Vault felügyeleti síkot. A síkon a műveletek közé tartozik a kulcstartók létrehozása és törlése, a Key Vault és a hozzáférési szabályzatok frissítése. Az adatsíkon dolgozhat a kulcstartóban tárolt adatokkal. Hozzáadhat, törölhet és módosíthat kulcsokat, titkos kulcsokat és tanúsítványokat.

Mindkét sík Azure Active Directory [(Azure AD) használja](../../active-directory/fundamentals/active-directory-whatis.md) a hitelesítéshez. Az engedélyezéshez [a](./assign-access-policy-portal.md) felügyeleti sík [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](../../role-based-access-control/overview.md) használ, az adatsík pedig egy Key Vault hozzáférési szabályzatot és az Azure RBAC-t az [Key Vault-alapú műveletekhez.](./rbac-guide.md)

A kulcstartók egyik síkon való eléréséhez minden hívónak (felhasználónak vagy alkalmazásnak) megfelelő hitelesítéssel és engedélyezéssel kell lennie. A hitelesítés meghatározza a hívó identitását. Az engedélyezés határozza meg, hogy a hívó mely műveleteket hajthatja végre. A Key Vault a Azure Active Directory [(Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md)használatával működik, amely az adott rendszerbiztonsági tag **identitásának hitelesítéséért felelős.**

A rendszerbiztonsági tag olyan objektum, amely az Azure-erőforrásokhoz hozzáférést kérő felhasználót, csoportot, szolgáltatást vagy alkalmazást jelöli. Az Azure minden rendszerbiztonsági taghoz egyedi **objektumazonosítót** rendel.

- A **felhasználói rendszerbiztonsági** tag azonosít egy felhasználót, aki profillal rendelkezik a Azure Active Directory.
- A **csoportbiztonsági** tag azonosítja a Azure Active Directory. A csoporthoz rendelt szerepkörök vagy engedélyek a csoport összes felhasználója számára meg vannak osztva.
- A **szolgáltatásnév** olyan rendszerbiztonsági tagtípus, amely egy alkalmazást vagy szolgáltatást azonosít, vagyis egy kóddarabot felhasználó vagy csoport helyett. A szolgáltatásnév objektumazonosítóját ügyfél-azonosítónak nevezik, és felhasználónévként viselkedik.  A szolgáltatásnév titkos **ügyféltitkja** vagy **tanúsítványa** a jelszavaként működik. Számos Azure-szolgáltatás támogatja a felügyelt [identitások](../../active-directory/managed-identities-azure-resources/overview.md) hozzárendelését az ügyfél-azonosító és a **tanúsítvány** automatizált **kezelésével.** A felügyelt identitás az Azure-ban való hitelesítés legbiztonságosabb és ajánlott lehetősége.

További információ a hitelesítésről a Key Vault: [Hitelesítés](authentication.md) Azure Key Vault

## <a name="privileged-access"></a>Emelt szintű hozzáférés

Az engedélyezés határozza meg, hogy a hívó milyen műveleteket hajthat végre. A Key Vault Azure [szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md) és a hozzáférési szabályzatok Azure Key Vault használ.

A tárolókhoz való hozzáférés két felületen vagy síkon keresztül történik. Ezek a síkok a felügyeleti sík és az adatsík.

- A *felügyeleti síkon* kezelheti magát Key Vault, és ez a tárolók létrehozására és törlésére használt felület. Emellett olvashatja a kulcstartó tulajdonságait, és kezelheti a hozzáférési szabályzatokat.
- Az *adatsík* lehetővé teszi a kulcstartóban tárolt adatokkal való munkát. Hozzáadhat, törölhet és módosíthat kulcsokat, titkos kulcsokat és tanúsítványokat.

Az alkalmazások végponton keresztül férnek hozzá a síkhoz. A két sík hozzáférés-vezérlése egymástól függetlenül működik. Ahhoz, hogy hozzáférést biztosítsunk egy alkalmazás számára a kulcstartóban található kulcsokhoz, adatsík-hozzáférést kell biztosítanunk egy Key Vault szabályzat vagy az Azure RBAC használatával. Ha olvasási hozzáférést ad egy felhasználónak Key Vault tulajdonságokhoz és címkékhez, de az adatokhoz (kulcsokhoz, titkos kulcsokhoz vagy tanúsítványokhoz) nem, a felügyeleti sík azure RBAC-n keresztüli hozzáférését adja meg.

Az alábbi táblázat a felügyeleti és adatsíkok végpontját mutatja be.

| Hozzáférési &nbsp; sík | Hozzáférés végpontjai | Üzemeltetés | &nbsp;Hozzáférés-vezérlési mechanizmus |
| --- | --- | --- | --- |
| Felügyeleti sík | **Globális:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> management.microsoftazure.de:443 | Kulcstartók létrehozása, olvasása, frissítése és törlése<br><br>Hozzáférési Key Vault beállítása<br><br>Címkék Key Vault beállítása | Azure RBAC-vel |
| Adatsík | **Globális:**<br> &lt;tároló-neve&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;tároló-neve&gt;.vault.azure.cn:443<br><br> **Azure US Government:**<br> &lt;tároló-neve&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> &lt;tároló-neve&gt;.vault.microsoftazure.de:443 | Kulcsok: titkosítás, visszafejtés, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, recover, backup, restore, purge<br><br> Tanúsítványok: managecontacts, getissuers, listissuers, setissuers, deleteissuers, manageissuers, get, list, create, import, update, delete, recover, backup, restore, purge<br><br>  Titkos kulcsok: get, list, set, delete,recover, backup, restore, purge | Key Vault szabályzat vagy az Azure RBAC |

### <a name="managing-administrative-access-to-key-vault"></a>Felügyeleti hozzáférés kezelése Key Vault

Amikor kulcstartót hoz létre egy erőforráscsoportban, a hozzáférést az Azure AD használatával kezelheti. Lehetővé teszi a felhasználók vagy csoportok számára az erőforráscsoportok kulcstartóinak kezelését. A megfelelő Azure-szerepkörök hozzárendelése segítségével adott hatókörszinten adhat hozzáférést. Ha hozzáférést szeretne ad egy felhasználónak a kulcstartók kezeléséhez, előre meghatározott szerepkört kell hozzárendelni `key vault Contributor` a felhasználóhoz egy adott hatókörben. Az Azure-beli szerepkörhöz a következő hatókörszintek rendelhetők hozzá:

- **Előfizetés:** Az előfizetés szintjén hozzárendelt Azure-szerepkör az előfizetésben található összes erőforráscsoportra és erőforrásra vonatkozik.
- **Erőforráscsoport:** Az erőforráscsoport szintjén hozzárendelt Azure-szerepkör az adott erőforráscsoportban található összes erőforrásra vonatkozik.
- **Adott erőforrás:** Egy adott erőforráshoz hozzárendelt Azure-szerepkör az adott erőforrásra vonatkozik. Ebben az esetben az erőforrás egy adott kulcstartó.

Több előre definiált szerepkör is íme. Ha egy előre meghatározott szerepkör nem illik az igényeihez, saját szerepkört is definiálhat. További információ: [Azure RBAC: Beépített szerepkörök.](../../role-based-access-control/built-in-roles.md)

> [!IMPORTANT]
> Ha egy felhasználó engedélyekkel rendelkezik egy kulcstartó felügyeleti síkhoz, a felhasználó hozzáférést adhat magának az adatsíkhoz egy új Key Vault `Contributor` beállításával. Szigorú ellenőrzéssel kell szabályozni, hogy ki férhet `Contributor` hozzá szerepkörhöz a kulcstartókhoz. Győződjön meg arról, hogy csak arra jogosult személyek férhetnek hozzá és kezelhetik kulcstartóit, kulcsait, titkos kulcsait és tanúsítványait.

### <a name="controlling-access-to-key-vault-data"></a>Az adatokhoz való Key Vault szabályozása

Key Vault hozzáférési szabályzatok külön ják meg az engedélyeket a kulcsokhoz, titkos kulcsokhoz vagy tanúsítványokhoz. A felhasználóknak csak a kulcsokhoz adhat hozzáférést, a titkos kulcsokhoz nem. A kulcsok, titkos kulcsok és tanúsítványok hozzáférési engedélyei tárolószinten kezelhetők.

> [!IMPORTANT]
> Key Vault hozzáférési szabályzatok nem támogatják a részletes, objektumszintű engedélyeket, például egy adott kulcsot, titkos kulcsot vagy tanúsítványt. Ha a felhasználó engedélyt kap a kulcsok létrehozására és törlésére, végrehajthatja ezeket a műveleteket a kulcstartó összes kulcsán.

A kulcstartók hozzáférési szabályzatát az [Azure Portal,](assign-access-policy-portal.md)az [Azure CLI,](assign-access-policy-cli.md) [Azure PowerShell](assign-access-policy-powershell.md)vagy a Key Vault Management [REST API-k használatával állíthatja be.](/rest/api/keyvault/)

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

Key Vault naplózás a tárolón végrehajtott tevékenységekkel kapcsolatos adatokat menti. További részletekért lásd: Key Vault [naplózása.](logging.md)

Integrálhatja a Key Vault a Event Grid, hogy értesítést kap a key vaultban tárolt kulcs, tanúsítvány vagy titkos kulcs állapotának változásáról. Részletekért lásd: [Monitorozási Key Vault a Azure Event Grid](event-grid-overview.md)

Fontos a kulcstartó állapotának figyelése is, hogy a szolgáltatás a kívánt működést biztosítsa. További információért lásd: [Figyelés és riasztások a Azure Key Vault.](alert.md)

## <a name="backup-and-recovery"></a>Biztonsági másolat és helyreállítás

Azure Key Vault helyreállítható törlés és végleges törlés elleni védelem lehetővé teszi a törölt tárolók és tárolóobjektumok helyreállítását. További részletekért lásd a Azure Key Vault [törlés áttekintését.](soft-delete-overview.md)

Emellett rendszeres biztonságimentéseket kell készítsen a tárolóról a tárolón belüli objektumok frissítések/ törlése/létrehozása során.  

## <a name="next-steps"></a>Következő lépések

- [Azure Key Vault biztonsági alapkonfiguráció](security-baseline.md)
- [Azure Key Vault ajánlott eljárások](security-baseline.md)
- [Virtuális hálózati szolgáltatásvégpontok az Azure Key Vaulthoz](overview-vnet-service-endpoints.md)
- [Azure RBAC: Beépített szerepkörök](../../role-based-access-control/built-in-roles.md)