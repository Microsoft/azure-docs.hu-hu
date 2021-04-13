---
title: Biztonságos hozzáférés egy kulcstartóhoz
description: A felhasználók hozzáférési modellje Azure Key Vault, beleértve Active Directory hitelesítést és az erőforrás-végpontokat.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: sudbalas
ms.openlocfilehash: b1ec89db7bc12ffbd21c6e302e065449eba3ac20
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366487"
---
# <a name="secure-access-to-a-key-vault"></a>Biztonságos hozzáférés egy kulcstartóhoz

Azure Key Vault egy felhőszolgáltatás, amely védi a titkosítási kulcsokat és titkos kódokat, például a tanúsítványokat, a kapcsolati sztringeket és a jelszavakat. Mivel ezek az adatok bizalmasak és üzleti szempontból kritikus fontosságúak, csak a jogosult alkalmazások és felhasználók engedélyezésével kell biztonságossá teszi a kulcstartókhoz való hozzáférést. Ez a cikk áttekintést nyújt a Key Vault hozzáférési modelljéről. Ismerteti a hitelesítést és az engedélyezést, valamint a kulcstartókhoz való hozzáférés biztonságossá való használatát.

További információ a Key Vault: [About Azure Key Vault](overview.md); A kulcstartóban tárolható adatokkal kapcsolatos további információkért lásd: [About keys, secrets, and certificates](about-keys-secrets-certificates.md)(Tudnivalók a kulcsokról, titkos kulcsokról és tanúsítványokról).

## <a name="access-model-overview"></a>Hozzáférési modell áttekintése

A kulcstartóhoz való hozzáférést két felületen  lehet vezérelni: a felügyeleti síkon és az **adatsíkon.** A felügyeleti síkon kezelheti magát Key Vault felügyeleti síkot. A síkon a műveletek közé tartozik a kulcstartók létrehozása és törlése, a Key Vault és a hozzáférési szabályzatok frissítése. Az adatsíkon dolgozhat a kulcstartóban tárolt adatokkal. Hozzáadhat, törölhet és módosíthat kulcsokat, titkos kulcsokat és tanúsítványokat.

Mindkét sík Azure Active Directory [(Azure AD) használja](../../active-directory/fundamentals/active-directory-whatis.md) a hitelesítéshez. Az engedélyezéshez [a](./assign-access-policy-portal.md) felügyeleti sík [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](../../role-based-access-control/overview.md) használ, az adatsík pedig egy Key Vault hozzáférési szabályzatot és az Azure RBAC-t az [Key Vault-alapú műveletekhez.](./rbac-guide.md)

A kulcstartók egyik síkon való eléréséhez minden hívónak (felhasználónak vagy alkalmazásnak) megfelelő hitelesítéssel és engedélyezéssel kell lennie. A hitelesítés meghatározza a hívó identitását. Az engedélyezés határozza meg, hogy a hívó mely műveleteket hajthatja végre. A Key Vault a Azure Active Directory [(Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md)használatával működik, amely az adott rendszerbiztonsági tag **identitásának hitelesítéséért felelős.**

A rendszerbiztonsági tag olyan objektum, amely az Azure-erőforrásokhoz hozzáférést kérő felhasználót, csoportot, szolgáltatást vagy alkalmazást jelöli. Az Azure minden rendszerbiztonsági **taghoz** egyedi objektumazonosítót rendel.

* A **felhasználó rendszerbiztonsági** tag azonosítja a profillal a Azure Active Directory.

* A **csoportbiztonsági** tag azonosítja a Azure Active Directory. A csoporthoz rendelt szerepkörök vagy engedélyek a csoport összes felhasználója számára meg vannak osztva.

* A **szolgáltatásnév** olyan rendszerbiztonsági tagtípus, amely egy alkalmazást vagy szolgáltatást azonosít, vagyis egy kóddarabot felhasználó vagy csoport helyett. A szolgáltatásnév objektumazonosítóját ügyfél-azonosítónak nevezik, és felhasználónévként viselkedik.  A szolgáltatásnév titkos **ügyféltitkja** vagy **tanúsítványa** a jelszavaként működik. Számos Azure-szolgáltatás támogatja a felügyelt [identitások](../../active-directory/managed-identities-azure-resources/overview.md) hozzárendelését az ügyfél-azonosító és a **tanúsítvány** automatizált **kezelésével.** A felügyelt identitás az Azure-ban való hitelesítés legbiztonságosabb és ajánlott lehetősége.

További információ a hitelesítésről a Key Vault: [Hitelesítés Azure Key Vault](authentication.md)

## <a name="key-vault-authentication-options"></a>Key Vault hitelesítési lehetőségek

Amikor létrehoz egy kulcstartót egy Azure-előfizetésben, az automatikusan társítva lesz az előfizetés Azure AD-bérlőjéhez. Mindkét síkon minden hívónak regisztrálnia kell ebben a bérlőben, és hitelesítenie kell magát a kulcstartó eléréséhez. Az alkalmazások mindkét esetben három Key Vault férhetnek hozzá:

- **Csak alkalmazás:** Az alkalmazás egy szolgáltatásnév vagy felügyelt identitás. Ez az identitás a leggyakoribb forgatókönyv olyan alkalmazások esetében, amelyek időnként tanúsítványokat, kulcsokat vagy titkos kulcsokat kell elérniük a kulcstartóból. Ahhoz, hogy ez a forgatókönyv működjön, az alkalmazás fájlját meg kell adni a hozzáférési házirendben, és a nem lehet megadva, vagy a következőnek `objectId` `applicationId` kell lennie:  `null` .
- **Csak felhasználó:** A felhasználó a bérlőben regisztrált bármely alkalmazásból hozzáfér a kulcstartóhoz. Ilyen típusú hozzáférés például a Azure PowerShell és a Azure Portal. Ahhoz, hogy ez a forgatókönyv működjön, a felhasználó felhasználóját meg kell adni a hozzáférési szabályzatban, és a nem lehet megadva, vagy a következőnek `objectId` `applicationId` kell lennie:  `null` .
- **Application-plus-user** (más néven összetett _identitás):_ A felhasználónak hozzá kell  férnie a kulcstartóhoz egy adott alkalmazásból, és az alkalmazásnak a felhasználó megszemélyesítéséhez a hitelesítési (OBO) folyamatot kell használnia. Ahhoz, hogy ez a forgatókönyv működjön, a és a értéket is meg kell `applicationId` `objectId` adni a hozzáférési szabályzatban. A `applicationId` azonosítja a szükséges alkalmazást, a pedig azonosítja a `objectId` felhasználót. Ez a lehetőség jelenleg nem érhető el az Adatsík Azure RBAC-hez.

Az alkalmazás minden típusú hozzáférés esetén az Azure AD-val hitelesíti magát. Az alkalmazás az alkalmazás [típusától](../../active-directory/develop/authentication-vs-authorization.md) függően bármilyen támogatott hitelesítési módszert használ. Az alkalmazás lekért egy jogkivonatot a síkon egy erőforráshoz a hozzáférés megadásához. Az erőforrás az Azure-környezeten alapuló végpont a felügyeleti vagy adatsíkon. Az alkalmazás a jogkivonatot használja, és REST API egy kérést a Key Vault. További tudnivalókért tekintse át a teljes [hitelesítési folyamatot.](../../active-directory/develop/v2-oauth2-auth-code-flow.md)

A két síkon való hitelesítés egyetlen mechanizmusának modellje számos előnyt biztosít:

- A szervezetek központilag vezérelheti a hozzáférést a szervezet összes kulcstartója számára.
- Ha egy felhasználó távozik, azonnal elveszíti a szervezet összes kulcstartó-hozzáférését.
- A szervezetek az Azure AD beállításaival szabhatják testre a hitelesítést, például engedélyezhetik a többtényezős hitelesítést a további biztonság érdekében.

## <a name="resource-endpoints"></a>Erőforrás-végpontok

Az alkalmazások végponton keresztül férnek hozzá a síkhoz. A két sík hozzáférés-vezérlése egymástól függetlenül működik. Ahhoz, hogy hozzáférést biztosítsunk egy alkalmazásnak a kulcstartó kulcsai számára, adatsík-hozzáférést kell biztosítanunk egy Key Vault szabályzat vagy az Azure RBAC használatával. Ha olvasási hozzáférést ad egy felhasználónak Key Vault tulajdonságokhoz és címkékhez, de az adatokhoz (kulcsokhoz, titkos kulcsokhoz vagy tanúsítványokhoz) nem, a felügyeleti sík azure RBAC-n keresztüli hozzáférését adja meg.

Az alábbi táblázat a felügyeleti és adatsíkok végpontját mutatja be.

| Hozzáférési &nbsp; sík | Hozzáférés végpontjai | Üzemeltetés | &nbsp;Hozzáférés-vezérlési mechanizmus |
| --- | --- | --- | --- |
| Felügyeleti sík | **Globális:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> management.microsoftazure.de:443 | Kulcstartók létrehozása, olvasása, frissítése és törlése<br><br>Hozzáférési Key Vault beállítása<br><br>Címkék Key Vault beállítása | Azure RBAC-vel |
| Adatsík | **Globális:**<br> &lt;tároló-neve&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;tároló-neve&gt;.vault.azure.cn:443<br><br> **Azure US Government:**<br> &lt;tároló-neve&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> &lt;tároló-neve&gt;.vault.microsoftazure.de:443 | Kulcsok: titkosítás, visszafejtés, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, recover, backup, restore, purge<br><br> Tanúsítványok: managecontacts, getissuers, listissuers, setissuers, deleteissuers, manageissuers, get, list, create, import, update, delete, recover, backup, restore, purge<br><br>  Titkos kulcsok: get, list, set, delete,recover, backup, restore, purge | Key Vault szabályzat vagy az Azure RBAC |

## <a name="management-plane-and-azure-rbac"></a>Felügyeleti sík és Azure RBAC

A felügyeleti síkon [az Azure szerepköralapú hozzáférés-vezérlésével (Azure RBAC)](../../role-based-access-control/overview.md) engedélyezheti a hívó által végrehajtható műveleteket. Az Azure RBAC-modellben minden Azure-előfizetés rendelkezik egy Azure AD-példányral. Ebből a címtárból adhat hozzáférést a felhasználóknak, csoportoknak és alkalmazásoknak. A hozzáférés az Azure-előfizetésben az üzembe helyezési modellt Azure Resource Manager kezeléséhez.

Létrehozhat egy kulcstartót egy erőforráscsoportban, és kezelheti a hozzáférést az Azure AD használatával. Lehetővé teszi a felhasználók vagy csoportok számára az erőforráscsoportok kulcstartóinak kezelését. A hozzáférést egy adott hatókörszinten adhatja meg a megfelelő Azure-szerepkörök hozzárendelése által. Ahhoz, hogy hozzáférést biztosítsunk egy felhasználónak a kulcstartók kezeléséhez, előre meghatározott Key Vault [közreműködői](../../role-based-access-control/built-in-roles.md#key-vault-contributor) szerepkört kell hozzárendelni a felhasználóhoz egy adott hatókörben. Az Azure-beli szerepkörhöz a következő hatókörszintek rendelhetők hozzá:

- **Előfizetés:** Az előfizetés szintjén hozzárendelt Azure-szerepkör az előfizetésben található összes erőforráscsoportra és erőforrásra vonatkozik.
- **Erőforráscsoport:** Az erőforráscsoport szintjén hozzárendelt Azure-szerepkör az adott erőforráscsoportban található összes erőforrásra vonatkozik.
- **Adott erőforrás:** Egy adott erőforráshoz hozzárendelt Azure-szerepkör az adott erőforrásra vonatkozik. Ebben az esetben az erőforrás egy adott kulcstartó.

Több előre definiált szerepkör is íme. Ha egy előre meghatározott szerepkör nem illik az igényeihez, saját szerepkört is definiálhat. További információ: [Beépített Azure-szerepkörök](../../role-based-access-control/built-in-roles.md). 

Rendelkeznie kell és engedélyekkel, például felhasználói hozzáférés `Microsoft.Authorization/roleAssignments/write` `Microsoft.Authorization/roleAssignments/delete` [rendszergazdájával](../../role-based-access-control/built-in-roles.md#user-access-administrator) vagy [tulajdonosával](../../role-based-access-control/built-in-roles.md#owner)

> [!IMPORTANT]
> Ha egy felhasználó engedélyekkel rendelkezik egy kulcstartó felügyeleti síkhoz, a felhasználó hozzáférést adhat magának az adatsíkhoz egy új Key Vault `Contributor` beállításával. Szigorú ellenőrzéssel kell szabályozni, hogy ki férhet hozzá szerepkörhöz a `Contributor` kulcstartókhoz. Győződjön meg arról, hogy csak arra jogosult személyek férhetnek hozzá és kezelhetik kulcstartóit, kulcsait, titkos kulcsait és tanúsítványait.
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>Adatsík- és hozzáférési szabályzatok

Adatsík-hozzáférést úgy adhat meg, Key Vault kulcstartó hozzáférési szabályzatának beállításával. A hozzáférési szabályzatok beállításához egy felhasználónak, csoportnak vagy alkalmazásnak engedélyekkel kell rendelkeznie a kulcstartó felügyeleti `Key Vault Contributor` síkja számára.

Hozzáférést adhat egy felhasználónak, csoportnak vagy alkalmazásnak a kulcstartóban található kulcsok vagy titkos kulcsok adott műveleteinek végrehajtásához. Key Vault kulcstartó legfeljebb 1024 hozzáférési szabályzatbejegyzést támogat. Ha több felhasználónak szeretne adatsík-hozzáférést adni, hozzon létre egy Azure AD biztonsági csoportot, és adjon hozzá felhasználókat.

A tárolók és titkos kulcsműveletek teljes listáját itt láthatja: [Key Vault Operation Reference](/rest/api/keyvault/#vault-operations)

<a id="key-vault-access-policies"></a> Key Vault hozzáférési szabályzatok külön ják meg az engedélyeket a kulcsokhoz, titkos kulcsokhoz és tanúsítványokhoz.  A kulcsok, titkos kulcsok és tanúsítványok hozzáférési engedélyei a tároló szintjén vannak. 

A kulcstartó-hozzáférési szabályzatok használatával kapcsolatos további információkért lásd: Key Vault [hozzáférési szabályzat hozzárendelése](assign-access-policy-portal.md)

> [!IMPORTANT]
> Key Vault hozzáférési szabályzatok a tároló szintjén érvényesek. Ha a felhasználó engedélyt kap a kulcsok létrehozására és törlésére, végrehajthatja ezeket a műveleteket a kulcstartó összes kulcsán.
Key Vault hozzáférési szabályzatok nem támogatják a részletes, objektumszintű engedélyeket, például egy adott kulcsot, titkos kulcsot vagy tanúsítványt. 
>

## <a name="data-plane-and-azure-rbac"></a>Adatsík és Azure RBAC

Az Azure szerepköralapú hozzáférés-vezérlés egy alternatív engedélymodell, amellyel szabályozható Azure Key Vault adatsíkhoz való hozzáférés, amely az egyes kulcstartókban engedélyezhető. Az Azure RBAC-engedélymodell kizárólagos, és a beállítása után a tároló-hozzáférési szabályzatok inaktívvá váltak. Azure Key Vault azure-beli beépített szerepköröket definiál, amelyek a kulcsok, titkos kulcsok vagy tanúsítványok eléréséhez használt általános engedélykészleteket foglalják magukban.

Ha egy Azure-szerepkör hozzá van rendelve egy Azure AD-rendszerbiztonsági taghoz, az Azure hozzáférést biztosít ezekhez az erőforrásokhoz a rendszerbiztonsági tag számára. A hozzáférés az előfizetés, az erőforráscsoport, a kulcstartó vagy egy egyedi kulcs, titkos kulcs vagy tanúsítvány szintjére terjedhet ki. Az Azure AD rendszerbiztonsági tag lehet felhasználó, csoport, alkalmazás-szolgáltatásnév vagy az Azure-erőforrások felügyelt [identitása.](../../active-directory/managed-identities-azure-resources/overview.md)

Az Azure RBAC-engedélyek tároló-hozzáférési szabályzatokkal való használatának fő előnyei a központosított hozzáférés-vezérlés kezelése és integrációja a Privileged Identity Management [(PIM)](../../active-directory/privileged-identity-management/pim-configure.md)használatával. Privileged Identity Management időalapú és jóváhagyásalapú szerepkör-aktiválást biztosít az Ön számára fontos erőforrásokra vonatkozó túlzott, szükségtelen vagy helytelen hozzáférési engedélyek kockázatainak csökkentése érdekében.

Az adatsík Azure RBAC Key Vault használatával való használatával kapcsolatos további információkért lásd: Key Vault kulcsok, tanúsítványok és titkos kulcsok használata [Azure szerepköralapú hozzáférés-vezérléssel.](rbac-guide.md)

## <a name="firewalls-and-virtual-networks"></a>Tűzfalak és virtuális hálózatok

További biztonsági rétegként tűzfalakat és virtuális hálózati szabályokat konfigurálhat. A tűzfalak Key Vault virtuális hálózatokat úgy konfigurálhatja, hogy alapértelmezés szerint megtagadják az összes hálózatról (beleértve az internetes forgalmat is) származó forgalomhoz való hozzáférést. Hozzáférést adhat adott [Azure-beli](../../virtual-network/virtual-networks-overview.md) virtuális hálózatokról és nyilvános internetes IP-címtartományból származó forgalomhoz, így biztonságos hálózathatárt építhet ki alkalmazásai számára.

Íme néhány példa a szolgáltatásvégpont használatára:

* A titkosítási kulcsok Key Vault titkos kódokat és tanúsítványokat tárolja, és szeretné letiltani a kulcstartó nyilvános internetről való hozzáférését.
* Szeretné zárolni a kulcstartóhoz való hozzáférést, hogy csak az alkalmazás vagy a kijelölt gazdagépek rövid listája kapcsolódjon a kulcstartóhoz.
* Az Azure-beli virtuális hálózaton egy alkalmazás fut, és ez a virtuális hálózat minden bejövő és kimenő forgalom számára zárolva van. Az alkalmazásnak továbbra is csatlakoznia kell a Key Vault titkos kulcsok vagy tanúsítványok lekérése vagy titkosítási kulcsok használata érdekében.

További információ a tűzfalak Key Vault virtuális hálózatokról: [Tűzfalak Azure Key Vault virtuális hálózatok konfigurálása](network-security.md)

> [!NOTE]
> Key Vault tűzfalak és virtuális hálózati szabályok csak a virtuális gépek adatsíkja Key Vault. Key Vault vezérlősík műveleteire (például létrehozási, törlési és módosítási műveletek, hozzáférési szabályzatok beállítása, tűzfalak és virtuális hálózati szabályok beállítása) a tűzfalak és virtuális hálózati szabályok nem vonatkoznak.

## <a name="private-endpoint-connection"></a>Privát végpont kapcsolata

Ha teljes mértékben blokkolni kell a Key Vault nyilvánosnak való kitettséget, [azure](../../private-link/private-endpoint-overview.md) privát végpontot is használhat. Az Azure privát végpontok olyan hálózati adapterek, amelyek privát módon és biztonságosan csatlakoztatják a Azure Private Link. A privát végpont a virtuális hálózat egyik magánhálózati IP-címét használja, így hatékonyan behozza a szolgáltatást a virtuális hálózatba. A szolgáltatás felé minden forgalom átirányítható a privát végponton keresztül, így nincs szükség átjárókra, NAT-eszközökre, ExpressRoute- vagy VPN-kapcsolatokra vagy nyilvános IP-címekre. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Csatlakozhat egy Azure-erőforráspéldányhoz, így a legmagasabb szintű részletességet biztosít a hozzáférés-vezérlésben.

Az Azure-szolgáltatásokhoz Private Link forgatókönyvek:

- **Szolgáltatások privát elérése az Azure platformon:** Virtuális hálózat csatlakoztatása az Azure szolgáltatásaihoz nyilvános IP-cím nélkül a forrásnál vagy a célnál. A szolgáltatók a szolgáltatásokat a saját virtuális hálózatukon renderelik, és a fogyasztók a helyi virtuális hálózatukon keresztül férhetnek hozzá ezekhez a szolgáltatásokhoz. A Private Link platform kezeli a fogyasztó és a szolgáltatások közötti kapcsolatot az Azure gerinchálózatán keresztül. 
 
- **Helyszíni és** társviszonyban lévő hálózatok: Az Azure-ban futó szolgáltatások elérése helyszíni környezetből ExpressRoute privát társviszony-létesítésen, VPN-alagutakon és virtuális társhálózaton keresztül privát végpontok használatával. A szolgáltatás eléréséhez nincs szükség nyilvános társviszony-létesítés beállítására vagy az internetre való bejárásra. Private Link lehetővé teszi a számítási feladatok biztonságos áttelepítését az Azure-ba.
 
- **Adatszivárgás elleni védelem:** A privát végpont egy PaaS-erőforrás egy példányára van leképezve a teljes szolgáltatás helyett. A felhasználók csak az adott erőforráshoz csatlakozhatnak. A szolgáltatásban minden más erőforráshoz való hozzáférés le van tiltva. Ez a mechanizmus védelmet nyújt az adatszivárgási kockázatok ellen. 
 
- **Globális elérés:** Privát csatlakozás más régiókban futó szolgáltatásokhoz. A fogyasztó virtuális hálózata az A régióban is lehet, és képes csatlakozni a B Private Link mögötti szolgáltatásokhoz.  
 
- **Kiterjesztése a saját szolgáltatásaira:** Ugyanezeket a funkciókat használva privát módon renderelheti a szolgáltatást a felhasználók számára az Azure-ban. Ha a szolgáltatást egy standard Azure Load Balancer mögött helyezi el, engedélyezheti a Private Link. A fogyasztó ezután közvetlenül csatlakozhat a szolgáltatáshoz egy privát végponttal a saját virtuális hálózatában. A csatlakozási kérelmeket egy jóváhagyási hívási folyamattal kezelheti. Azure Private Link a különböző bérlőkhöz tartozó fogyasztók és szolgáltatások Azure Active Directory működnek. 

További információ a privát végpontokkal kapcsolatban: Key Vault [a Azure Private Link](./private-link-service.md)

## <a name="example"></a>Példa

Ebben a példában egy olyan alkalmazást fejlesztünk, amely egy tanúsítványt használ a TLS/SSL-hez, az Azure Storage-hoz az adatok tárolásához, és egy RSA 2048 bites kulcsot az Adatok titkosításához az Azure Storage-ban. Az alkalmazás egy Azure-beli virtuális gépen (VM) (vagy egy virtuálisgép-méretezési készletben) fut. A titkos alkalmazáskulcsok tárolására kulcstartót használhatunk. Tárolhatja az alkalmazás által az Azure AD-val való hitelesítéshez használt bootstrap-tanúsítványt.

A következő tárolt kulcsokhoz és titkos kulcsokhoz kell hozzáférnünk:
- **TLS/SSL-tanúsítvány:** TLS/SSL-hez használatos.
- **Tárkulcs:** A Storage-fiók elérésére használatos.
- **RSA 2048 bites kulcs:** Az Azure Storage által használt adattitkosítási kulcs burkosítására és kicsomagosítására szolgál.
- **Alkalmazás által felügyelt identitás:** Az Azure AD-val való hitelesítéshez használatos. A hozzáférés Key Vault után az alkalmazás lekérheti a tárkulcsot és a tanúsítványt.

A következő szerepköröket kell definiálnunk annak meghatározásához, hogy ki kezelheti, helyezheti üzembe és naplózhatja az alkalmazást:
- **Biztonsági csapat:** a CSO irodája (biztonsági igazgató) vagy hasonló közreműködők it-dolgozói. A biztonsági csapat felelős a titkos kulcsok megfelelő biztonságáért. A titkos kulcsok tartalmazhatnak TLS-/SSL-tanúsítványokat, titkosításhoz használt RSA-kulcsokat, kapcsolati sztringeket és tárfiókkulcsokat.
- **Fejlesztők és operátorok:** Az alkalmazást fejlesztő és az Azure-ban üzembe helyező alkalmazottak. A csapat tagjai nem tagjai a biztonsági személyzetnek. Nem szabad hozzáférniük olyan bizalmas adatokhoz, mint a TLS-/SSL-tanúsítványok és az RSA-kulcsok. Csak az üzembe helyezett alkalmazás férhet hozzá a bizalmas adatokhoz.
- **Auditorok:** Ez a szerepkör olyan közreműködők számára fontos, akik nem tagjai a fejlesztési csapatnak vagy az általános it-csoportnak. A biztonsági szabványoknak való megfelelés biztosítása érdekében áttekintik a tanúsítványok, kulcsok és titkos kulcsok használatát és karbantartását.

Van egy másik szerepkör is, amely kívül esik az alkalmazás hatókörében: az előfizetési (vagy erőforráscsoport-) rendszergazdán. Az előfizetés rendszergazdája beállítja a kezdeti hozzáférési engedélyeket a biztonsági csapat számára. Egy olyan erőforráscsoport használatával biztosítják a hozzáférést a biztonsági csapatnak, amely rendelkezik az alkalmazáshoz szükséges erőforrásokkal.

A következő műveleteket kell engedélyeznünk a szerepköreinkhez:

**Biztonsági csapat**
- Kulcstartók létrehozása.
- Kapcsolja be a Key Vault naplózást.
- Kulcsok és titkos kulcsok hozzáadása.
- Hozzon létre biztonsági másolatokat a kulcsokról a vészhelyreállításhoz.
- Beállíthatja Key Vault hozzáférési szabályzatokat, és szerepköröket rendelhet hozzá, hogy engedélyeket adjon a felhasználóknak és az alkalmazásoknak adott műveletekhez.
- A kulcsokat és a titkos kulcsokat rendszeresen el kell dobni.

**Fejlesztők és üzemeltetők**
- Szerezze be a biztonsági csapattól a bootstrap- és TLS-/SSL-tanúsítványok (ujjlenyomatok), a tárkulcs (titkos URI) és az RSA-kulcs (kulcs URI) referenciáit a burkoltatáshoz/kiíráshoz.
- Az alkalmazás fejlesztése és üzembe helyezése a tanúsítványok és titkos kulcsok programozott eléréséhez.

**Ellenőrök**
- Tekintse át a Key Vault naplóit a kulcsok és titkos kulcsok megfelelő használatának, valamint az adatbiztonsági szabványoknak való megfelelés ellenőrzéséhez.

Az alábbi táblázat összefoglalja a szerepkörök és az alkalmazás hozzáférési engedélyeit.

| Szerepkör | Felügyeleti sík engedélyei | Adatsík engedélyei – tároló-hozzáférési szabályzatok | Adatsík engedélyei – Azure RBAC  |
| --- | --- | --- | --- |
| Biztonsági csapat | [Key Vault Közreműködő](../../role-based-access-control/built-in-roles.md#key-vault-contributor) | Tanúsítványok: minden művelet <br> Kulcsok: minden művelet <br> Titkos kulcsok: minden művelet | [Key Vault rendszergazda](../../role-based-access-control/built-in-roles.md#key-vault-administrator) |
| Fejlesztők és &nbsp; operátorok | Key Vault üzembe helyezési engedély<br><br> **Megjegyzés:** Ez az engedély lehetővé teszi, hogy az üzembe helyezett virtuális gépek titkos kulcsokat kérnek le egy kulcstartóból. | Nincsenek | Nincsenek |
| Ellenőrök | Nincsenek | Tanúsítványok: lista <br> Kulcsok: listája<br>Titkos kulcsok: listája<br><br> **Megjegyzés:** Ez az engedély lehetővé teszi az auditorok számára az attribútumok (címkék, aktiválási dátumok, lejárati dátumok) vizsgálatát olyan kulcsok és titkos kulcsok esetén, amelyek nem szerepelnek a naplókban. | [Key Vault Olvasó](../../role-based-access-control/built-in-roles.md#key-vault-reader) |
| Azure Storage-tárfiók neve | Nincsenek | Kulcsok: get, list, wrapKey, unwrapKey <br> | [Key Vault titkosítási szolgáltatás titkosítási felhasználója](../../role-based-access-control/built-in-roles.md#key-vault-crypto-service-encryption-user) |
| Alkalmazás | Nincsenek | Titkos kulcsok: lekért, listás <br> Tanúsítványok: get, list | [Key Vault olvasó,](../../role-based-access-control/built-in-roles.md#key-vault-reader) [Key Vault titkos felhasználó](../../role-based-access-control/built-in-roles.md#key-vault-secrets-user) |

A három csapatszerepkének hozzá kell férni más erőforrásokhoz és Key Vault is. A virtuális gépek üzembe helyezéséhez (vagy Web Apps szolgáltatásának Azure App Service) a fejlesztőknek és az operátoroknak üzembe kell helyezniük a hozzáférést. Az auditorok olvasási hozzáféréssel kell, hogy hozzáférjenek ahhoz a Storage-fiókhoz, Key Vault a naplókat.

A példánk egy egyszerű forgatókönyvet ismertet. A valós forgatókönyvek összetettebbek is. Az engedélyeket igény szerint módosíthatja a kulcstartóhoz. Feltételeztük, hogy a biztonsági csapat biztosítja a kulcs- és titkos referenciákat (URI-k és ujjlenyomatok), amelyeket a DevOps-munkatársak használnak az alkalmazásaikban. A fejlesztőknek és az operátoroknak nincs szükségük adatsík-hozzáférésre. A key vault biztonságossá tere volt a célja.

> [!NOTE]
> Ez a példa bemutatja, Key Vault hogyan zárolva van a hozzáférés éles környezetben. A fejlesztőknek rendelkezniük kell saját előfizetéssel vagy erőforráscsoporttal, amely teljes körű engedélyekkel rendelkezik a tárolók, virtuális gépek és az alkalmazást fejlesztő tárfiók kezeléséhez.

## <a name="resources"></a>Források

- [Információk az Azure Key Vaultról](overview.md)
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
- [Azure RBAC-vel](../../role-based-access-control/overview.md)
- [Privát kapcsolat](../../private-link/private-link-overview.md)

## <a name="next-steps"></a>Következő lépések

[Hitelesítés az Azure Key Vaulttal](authentication.md)

[Hozzáférési szabályzat Key Vault hozzárendelése](assign-access-policy-portal.md)

[Azure-szerepkör hozzárendelése kulcsokhoz, titkos kulcsokhoz és tanúsítványokhoz való hozzáféréshez](rbac-guide.md)

[Key Vault-tűzfalak és virtuális hálózatok konfigurálása](network-security.md)

[Privát kapcsolat létrehozása a Key Vault](private-link-service.md)
