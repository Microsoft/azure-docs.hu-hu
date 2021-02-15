---
title: Biztonságos hozzáférés egy kulcstartóhoz
description: Azure Key Vaulthoz való hozzáférési modell, beleértve a Active Directory hitelesítési és erőforrás-végpontokat.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: sudbalas
ms.openlocfilehash: f82c7060f703aff6c19f0082454779b8fea1ba76
ms.sourcegitcommit: e3151d9b352d4b69c4438c12b3b55413b4565e2f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2021
ms.locfileid: "100526255"
---
# <a name="secure-access-to-a-key-vault"></a>Biztonságos hozzáférés egy kulcstartóhoz

A Azure Key Vault egy felhőalapú szolgáltatás, amely védelmet biztosít a titkosítási kulcsok és a titkok, például a tanúsítványok, a kapcsolatok karakterláncai és a jelszavak számára. Mivel ezek az adatok érzékenyek és üzleti szempontból kritikus fontosságúak, a kulcstartóhoz való hozzáférést csak a jogosult alkalmazások és felhasználók engedélyezésével kell biztosítani. Ez a cikk a Key Vault hozzáférési modell áttekintését tartalmazza. Ismerteti a hitelesítést és az engedélyezést, valamint ismerteti a kulcstartók hozzáférésének biztonságossá tételét.

További információ a Key Vaultről: [About Azure Key Vault](overview.md); a Key vaultban tárolt adatokkal kapcsolatos további információkért lásd: [a kulcsok, a titkok és a tanúsítványok ismertetése](about-keys-secrets-certificates.md).

## <a name="access-model-overview"></a>Hozzáférési modell áttekintése

A kulcstartóhoz való hozzáférést két interfész szabályozza: a **felügyeleti síkon** és az **adatsíkon**. A felügyeleti síkon a Key Vault saját maga felügyeli. Az ebben a síkban található műveletek közé tartozik a kulcstartók létrehozása és törlése, Key Vault tulajdonságok beolvasása és a hozzáférési házirendek frissítése. Az adatsíkon a Key vaultban tárolt adatmennyiséggel dolgozik. Kulcsok, titkos kódok és tanúsítványok hozzáadására, törlésére és módosítására is lehetőség van.

Mindkét síkon [Azure Active Directoryt (Azure ad)](../../active-directory/fundamentals/active-directory-whatis.md) használ a hitelesítéshez. Az engedélyezéshez a felügyeleti sík [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](../../role-based-access-control/overview.md) használ, és az adatsík egy [Key Vault hozzáférési házirendet](./assign-access-policy-portal.md) és az [Azure-RBAC használja Key Vault adatsík-műveletekhez](./rbac-guide.md).

A kulcstartók bármelyik síkon való eléréséhez minden hívónak (felhasználónak vagy alkalmazásnak) megfelelő hitelesítéssel és engedélyezéssel kell rendelkeznie. A hitelesítés létrehozza a hívó identitását. Az engedélyezés meghatározza, hogy a hívó milyen műveleteket hajthat végre. A Key Vault-alapú hitelesítés az [Azure Active Directory (Azure ad)](../../active-directory/fundamentals/active-directory-whatis.md)szolgáltatással együtt működik, amely az adott **rendszerbiztonsági tag** identitásának hitelesítéséhez felelős.

A rendszerbiztonsági tag egy olyan objektum, amely az Azure-erőforrásokhoz hozzáférést kérő felhasználót, csoportot, szolgáltatást vagy alkalmazást jelöl. Az Azure egy egyedi **objektumazonosítót** rendel minden rendszerbiztonsági tag számára.

* A **felhasználói** rendszerbiztonsági tag azonosítja azt a személyt, aki Azure Active Directory profillal rendelkezik.

* A **csoport** rendszerbiztonsági tagja a Azure Active Directoryban létrehozott felhasználók készletét azonosítja. A csoportba tartozó összes szerepkör vagy engedély a csoport összes felhasználója számára elérhető.

* Az **egyszerű szolgáltatásnév** olyan rendszerbiztonsági tag, amely egy alkalmazás vagy szolgáltatás identitását jelenti, azaz egy kódrészletet, amely nem felhasználó vagy csoport. Egy egyszerű szolgáltatásnév AZONOSÍTÓjának neve **ügyfél-azonosítóként** működik, és a neve, mint a felhasználóneve. A szolgáltatáshoz tartozó **ügyfél titka** vagy **tanúsítványa** úgy viselkedik, mint a jelszava. Számos Azure-szolgáltatás támogatja a [felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md) hozzárendelését az **ügyfél-azonosító** és a **tanúsítvány** automatikus kezelésével. A felügyelt identitás a legbiztonságosabb és ajánlott megoldás az Azure-ban történő hitelesítéshez.

További információ a Key Vault hitelesítéséről: [hitelesítés Azure Key Vault](authentication.md)

## <a name="key-vault-authentication-options"></a>Key Vault hitelesítési lehetőségek

Amikor kulcstartót hoz létre egy Azure-előfizetésben, az automatikusan társítva lesz az előfizetés Azure AD-bérlője számára. Mindkét síkon lévő hívónak regisztrálnia kell ebben a bérlőben, és hitelesítenie kell magát a kulcstartó eléréséhez. Mindkét esetben az alkalmazások háromféle módon férhetnek hozzá a Key Vaulthoz:

- **Csak alkalmazás**: az alkalmazás az egyszerű szolgáltatásnév vagy a felügyelt identitást jelöli. Ez az identitás a leggyakoribb forgatókönyv azon alkalmazások esetében, amelyeknek időnként szükségük van tanúsítványokra, kulcsokra vagy titkos kulcsok elérésére a kulcstartóból. Ahhoz, hogy ez a forgatókönyv működjön, a `objectId` hozzáférési házirendben meg kell adni az alkalmazást, és `applicationId` a _nem_ adható meg vagy kötelező `null` .
- **Csak felhasználó**: a felhasználó hozzáfér a Key vaulthoz a bérlőben regisztrált bármely alkalmazásból. Ilyen típusú hozzáférés például a Azure PowerShell és a Azure Portal. Ahhoz, hogy ez a forgatókönyv működjön, a `objectId` felhasználónak meg kell adni a hozzáférési szabályzatot, és `applicationId` a _nem_ adható meg vagy kötelező `null` .
- **Alkalmazás-Plus-User** (más néven _összetett identitás_): a felhasználónak egy adott alkalmazásból kell hozzáférnie a kulcstartóhoz _, és_ az alkalmazásnak a felhasználó megszemélyesítéséhez a hitelesítési (OBO) folyamatot kell használnia. Ahhoz, hogy ez a forgatókönyv működjön, mindkettőt `applicationId` `objectId` meg kell adni a hozzáférési házirendben. Az `applicationId` azonosítja a szükséges alkalmazást, és `objectId` azonosítja a felhasználót. Ez a lehetőség jelenleg nem érhető el az Azure RBAC (előzetes verzió) adatsíkon.

Az alkalmazás minden típusú hozzáférés esetén az Azure AD-vel hitelesíti magát. Az alkalmazás a [támogatott hitelesítési módszereket](../../active-directory/develop/authentication-vs-authorization.md) használja az alkalmazás típusától függően. Az alkalmazás jogkivonatot ad a síkon lévő erőforráshoz a hozzáférés biztosításához. Az erőforrás az Azure-környezet alapján a felügyelet vagy az adatsík végpontja. Az alkalmazás a jogkivonatot használja, és egy REST API kérelmet küld Key Vaultnak. További információért tekintse át a [teljes hitelesítési folyamatot](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

A mindkét síkon történő hitelesítéshez használt egyetlen mechanizmus modellje számos előnnyel jár:

- A szervezetek központilag vezérelhetik a hozzáférést a szervezet összes kulcstartója számára.
- Ha a felhasználó elhagyja, azonnal elveszíti a hozzáférést a szervezet összes kulcstartójának.
- A szervezetek az Azure AD lehetőségeit használva szabhatják testre a hitelesítést, például lehetővé teszik a többtényezős hitelesítés használatát a további biztonság érdekében.

## <a name="resource-endpoints"></a>Erőforrás-végpontok

Az alkalmazások végpontokon keresztül férnek hozzá a síkokhoz. A két sík hozzáférés-vezérlése egymástól függetlenül működik. Ahhoz, hogy egy alkalmazás hozzáférjen a kulcsok használatához a kulcstartóban, Key Vault hozzáférési szabályzattal vagy Azure RBAC (előzetes verzió) biztosíthatja az adatsíkok elérését. Ahhoz, hogy a felhasználó olvasási hozzáférést biztosítson Key Vault tulajdonságokhoz és címkékhez, de nem fér hozzá az adatokhoz (kulcsokhoz, titkokhoz vagy tanúsítványokhoz), a felügyeleti sík hozzáférést biztosít az Azure RBAC.

A következő táblázat a felügyeleti és adatsíkok végpontját mutatja be.

| Hozzáférési &nbsp; sík | Hozzáférés végpontjai | Üzemeltetés | Hozzáférés- &nbsp; vezérlési mechanizmus |
| --- | --- | --- | --- |
| Felügyeleti sík | **Globális**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure US government:**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> management.microsoftazure.de:443 | Kulcstartók létrehozása, olvasása, frissítése és törlése<br><br>Key Vault hozzáférési szabályzatok beállítása<br><br>Key Vault címkék beállítása | Azure RBAC-vel |
| Adatsík | **Globális**<br> &lt;tároló-neve&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;tároló-neve&gt;.vault.azure.cn:443<br><br> **Azure US government:**<br> &lt;tároló-neve&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> &lt;tároló-neve&gt;.vault.microsoftazure.de:443 | Kulcsok: titkosítás, visszafejtés, wrapKey, unwrapKey, aláírás, ellenőrzés, beolvasás, Listázás, létrehozás, frissítés, importálás, törlés, helyreállítás, biztonsági mentés, visszaállítás, kiürítés<br><br> Tanúsítványok: managecontacts, getissuers, listissuers, setissuers, deleteissuers, manageissuers, beolvasás, Listázás, létrehozás, importálás, frissítés, törlés, helyreállítás, biztonsági mentés, visszaállítás, kiürítés<br><br>  Titkok: beolvasás, Listázás, beállítás, törlés, helyreállítás, biztonsági mentés, visszaállítás, kiürítés | Key Vault hozzáférési szabályzat vagy Azure RBAC (előzetes verzió)|

## <a name="management-plane-and-azure-rbac"></a>Felügyeleti sík és Azure RBAC

A felügyeleti síkon az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md) használatával engedélyezheti a hívó által végrehajtható műveleteket. Az Azure RBAC modellben minden Azure-előfizetéshez tartozik egy Azure AD-példány. Hozzáférést biztosít a felhasználóknak, csoportoknak és alkalmazásoknak ebben a címtárban. A hozzáférés a Azure Resource Manager üzemi modellt használó Azure-előfizetés erőforrásainak kezeléséhez van megadva.

Hozzon létre egy kulcstartót egy erőforráscsoporthoz, és kezelje a hozzáférést az Azure AD használatával. A felhasználók vagy csoportok számára engedélyezheti az erőforráscsoport kulcstárolóinak kezelését. A hozzáférést adott hatóköri szinten kell megadni a megfelelő Azure-szerepkörök hozzárendelésével. Ahhoz, hogy hozzáférést biztosítson egy felhasználónak a kulcstartók kezeléséhez, egy előre definiált [Key Vault közreműködői](../../role-based-access-control/built-in-roles.md#key-vault-contributor) szerepkört kell hozzárendelni a felhasználóhoz egy adott hatókörben. Az Azure-szerepkörökhöz a következő hatóköröket lehet hozzárendelni:

- **Előfizetés**: az előfizetés szintjén hozzárendelt Azure-szerepkörök az adott előfizetésen belüli összes erőforráscsoport és erőforrásra érvényesek.
- **Erőforráscsoport**: az erőforráscsoport szintjén hozzárendelt Azure-szerepkör az adott erőforráscsoport összes erőforrására vonatkozik.
- **Adott** erőforrás: egy adott erőforráshoz hozzárendelt Azure-szerepkör az adott erőforrásra vonatkozik. Ebben az esetben az erőforrás egy adott kulcstartó.

Számos előre definiált szerepkör létezik. Ha egy előre meghatározott szerepkör nem felel meg az igényeinek, megadhatja saját szerepkörét. További információ: [Beépített Azure-szerepkörök](../../role-based-access-control/built-in-roles.md). 

Önnek `Microsoft.Authorization/roleAssignments/write` és `Microsoft.Authorization/roleAssignments/delete` engedélyeinek, például a [felhasználói hozzáférés rendszergazdájának](../../role-based-access-control/built-in-roles.md#user-access-administrator) vagy [tulajdonosának](../../role-based-access-control/built-in-roles.md#owner) kell lennie

> [!IMPORTANT]
> Ha a felhasználó rendelkezik `Contributor` engedéllyel egy kulcstartó felügyeleti síkon, a felhasználó hozzáférést biztosíthat az adatsíkon az Key Vault hozzáférési szabályzat beállításával. Szigorúan szabályozhatja, `Contributor` hogy kik rendelkeznek szerepkör-hozzáféréssel a kulcstartóhoz. Győződjön meg arról, hogy csak a jogosult személyek férhetnek hozzá és kezelhetik a kulcstartókat, kulcsokat, titkos kulcsokat és tanúsítványokat.
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>Adatsík és hozzáférési szabályzatok

A kulcstartóhoz Key Vault hozzáférési szabályzatok beállításával adhatja meg az adatsíkok elérését. A hozzáférési szabályzatok beállításához a felhasználónak, csoportnak vagy alkalmazásnak `Key Vault Contributor` engedélyekkel kell rendelkeznie az adott kulcstartó felügyeleti síkja számára.

Egy kulcstartóban felhasználói, csoport-vagy alkalmazás-hozzáférési jogosultságot adhat adott műveletek végrehajtásához a kulcsok és titkok számára. A Key Vault a Key vaulthoz legfeljebb 1 024 hozzáférési szabályzatot támogat. Ha több felhasználó számára kíván hozzáférést biztosítani az adatsíkon, hozzon létre egy Azure AD biztonsági csoportot, és vegyen fel felhasználókat a csoportba.

A tár és a titkos művelet teljes listáját itt tekintheti meg: [Key Vault művelet leírása](/rest/api/keyvault/#vault-operations)

<a id="key-vault-access-policies"></a> Key Vault hozzáférési házirendek külön engedélyeket biztosítanak a kulcsokhoz, a titkokhoz és a tanúsítványokhoz.  A kulcsok, titkos kódok és tanúsítványok hozzáférési engedélyei a tároló szintjén találhatók. 

A Key Vault hozzáférési házirendjeinek használatával kapcsolatos további információkért lásd: [Key Vault hozzáférési szabályzat kiosztása](assign-access-policy-portal.md)

> [!IMPORTANT]
> Key Vault hozzáférési szabályzatok a tár szintjén érvényesek. Ha a felhasználó engedélyt kap a kulcsok létrehozására és törlésére, akkor ezeket a műveleteket a kulcstartó összes kulcsán végrehajthatja.
Key Vault hozzáférési házirendek nem támogatják a szemcsés, az objektum szintű engedélyeket, például egy adott kulcsot, titkos kulcsot vagy tanúsítványt. 
>

## <a name="data-plane-and-azure-rbac-preview"></a>Adatsíkon és Azure RBAC (előzetes verzió)

Az Azure szerepköralapú hozzáférés-vezérlés egy alternatív engedélyezési modell a Azure Key Vault adatsíkon való hozzáférés szabályozására, amely az egyes kulcstartók esetében engedélyezhető. Az Azure RBAC engedélyezési modellje kizárólagos, és egyszer van beállítva, a tár-hozzáférési szabályzatok inaktívak lettek. Azure Key Vault az Azure beépített szerepköreinek készletét határozza meg, amelyek magukban foglalják a kulcsok, titkos kódok vagy tanúsítványok eléréséhez használt általános engedélyeket.

Ha az Azure-szerepköröket egy Azure AD-rendszerbiztonsági tag számára rendeli hozzá, az Azure hozzáférést biztosít az adott rendszerbiztonsági tag erőforrásaihoz. A hozzáférés hatóköre az előfizetés, az erőforráscsoport, a kulcstartó, vagy egy adott kulcs, titkos kód vagy tanúsítvány szintjére is kiterjed. Az Azure AD rendszerbiztonsági tag lehet egy felhasználó, egy csoport, egy egyszerű alkalmazás vagy egy [felügyelt identitás az Azure-erőforrásokhoz](../../active-directory/managed-identities-azure-resources/overview.md).

Az Azure RBAC-engedélyek a tár-hozzáférési házirendekkel való használatának fő előnyei a központosított hozzáférés-vezérlés kezelése és a [Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md)integrációja. A Privileged Identity Management időalapú és jóváhagyási alapú szerepkör-aktiválást biztosít az Ön számára fontos erőforrásokra vonatkozó túlzott, szükségtelen vagy nem használt hozzáférési engedélyek kockázatának csökkentése érdekében.

További információ az Azure RBAC Key Vault adatsíkon [Key Vault: kulcsok, tanúsítványok és titkos kódok az Azure szerepköralapú hozzáférés-vezérléssel](rbac-guide.md)

## <a name="firewalls-and-virtual-networks"></a>Tűzfalak és virtuális hálózatok

További biztonsági réteg esetén a tűzfalakat és a virtuális hálózati szabályokat is konfigurálhatja. A Key Vault tűzfalak és virtuális hálózatok konfigurálásával megtagadhatja az adatforgalom elérését az összes hálózatról (beleértve az internetes forgalmat is), alapértelmezés szerint. Hozzáférést biztosíthat bizonyos Azure-beli [virtuális hálózatokból](../../virtual-network/virtual-networks-overview.md) és nyilvános internetes IP-címtartományok érkező adatforgalomhoz, így biztonságos hálózati határt hozhat létre az alkalmazásai számára.

Íme néhány példa a szolgáltatási végpontok használatára:

* A Key Vault használatával tárolja a titkosítási kulcsokat, az alkalmazási titkokat és a tanúsítványokat, és le szeretné tiltani a Key vaulthoz való hozzáférést a nyilvános internetről.
* Le szeretné zárni a kulcstartóhoz való hozzáférést, hogy csak az alkalmazása vagy a kijelölt gazdagépek rövid listája csatlakozhasson a kulcstartóhoz.
* Rendelkezik egy, az Azure-beli virtuális hálózaton futó alkalmazással, és ez a virtuális hálózat le van zárva az összes bejövő és kimenő forgalom esetében. Az alkalmazásnak továbbra is csatlakoznia kell Key Vaulthoz a titkok vagy tanúsítványok lekéréséhez, vagy titkosítási kulcsokat kell használnia.

A Key Vault tűzfallal és virtuális hálózatokkal kapcsolatos további információkért lásd: [Azure Key Vault tűzfalak és virtuális hálózatok konfigurálása](network-security.md)

> [!NOTE]
> Key Vault tűzfalak és virtuális hálózati szabályok csak Key Vault adatsíkon érvényesek. A tűzfalak és a virtuális hálózati szabályok nem érintik a vezérlési sík műveleteit (például a létrehozási, törlési és módosítási műveletek, a hozzáférési házirendek beállítása, a tűzfalak beállítása és a virtuális hálózati szabályok). Key Vault

## <a name="private-endpoint-connection"></a>Magánhálózati végponti kapcsolatok

Ha teljes mértékben le kell tiltani Key Vault a nyilvánosságnak való kitettséget, egy [Azure-beli magánhálózati végpont](../../private-link/private-endpoint-overview.md) is használható. Az Azure Private-végpontok olyan hálózati adapterek, amelyek az Azure Private-kapcsolaton keresztül csatlakoznak a szolgáltatáshoz. A privát végpont egy magánhálózati IP-címet használ a VNet, és hatékonyan hozza a szolgáltatást a VNet. A szolgáltatás felé irányuló összes forgalom a privát végponton keresztül irányítható, így nincs szükség átjáróra, NAT-eszközre, ExpressRoute vagy VPN-kapcsolatra, vagy nyilvános IP-címekre. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Kapcsolódhat egy Azure-erőforrás egy példányához, amely a legmagasabb szintű részletességet nyújtja a hozzáférés-vezérlésben.

Az Azure-szolgáltatások privát hivatkozásának használatának gyakori forgatókönyvei:

- **Saját hozzáférésű szolgáltatások az Azure platformon**: virtuális hálózatának összekapcsolása az Azure-ban, nyilvános IP-cím nélkül a forráson vagy a célhelyen. A szolgáltatók saját virtuális hálózatban tehetik a szolgáltatásaikat, és a felhasználók a helyi virtuális hálózatban érhetik el a szolgáltatásokat. A privát kapcsolati platform a fogyasztó és a szolgáltatások közötti kapcsolatot fogja kezelni az Azure-beli gerinc hálózaton. 
 
- Helyszíni és egymással **összekapcsolt hálózatok**: az Azure-ban futó, privát végpontokat használó EXPRESSROUTE, VPN-alagutakra és a virtuális hálózatokra épülő, az Azure-ban futtatott hozzáférési szolgáltatások. A szolgáltatás eléréséhez nincs szükség a nyilvános vagy az Internet bejárására. A privát hivatkozás biztonságos módszert biztosít a számítási feladatok Azure-ba történő átirányításához.
 
- **Védelem az adatszivárgás ellen**: a rendszer a teljes szolgáltatás helyett egy privát végpontot rendel egy Pásti erőforrás egy példányához. A felhasználók csak az adott erőforráshoz tudnak csatlakozni. A szolgáltatás bármely más erőforrásához való hozzáférés le van tiltva. Ez a mechanizmus védelmet nyújt az adatszivárgási kockázatokkal szemben. 
 
- **Globális elérhetőség**: kapcsolódjon a más régiókban futó szolgáltatásokhoz. A fogyasztó virtuális hálózata az A régióba kerülhet, és a B régióban található privát hivatkozás mögött található szolgáltatásokhoz is csatlakozhat.  
 
- **Kiterjesztheti saját szolgáltatásait**: az Azure-beli felhasználók számára is lehetővé teszi, hogy a szolgáltatás magántulajdonban legyen. Ha a szolgáltatást egy szabványos Azure Load Balancer mögött helyezi el, akkor azt privát hivatkozásként is engedélyezheti. A fogyasztó ezután közvetlenül kapcsolódhat a szolgáltatáshoz a saját virtuális hálózatában található privát végpont használatával. A kapcsolatkérelmeket a jóváhagyási hívási folyamat használatával kezelheti. Az Azure Private link a különböző Azure Active Directory bérlők számára tartozó felhasználók és szolgáltatások számára is működik. 

További információ a privát végpontokról: [Key Vault az Azure Private linkkel](./private-link-service.md)

## <a name="example"></a>Példa

Ebben a példában egy olyan alkalmazást fejlesztünk, amely egy tanúsítványt használ a TLS/SSL-hez, az Azure Storage-hoz az adattároláshoz, valamint egy RSA 2 048 bites kulcsot az Azure Storage-ban tárolt adattitkosításhoz. Az alkalmazás egy Azure-beli virtuális gépen (VM) (vagy egy virtuálisgép-méretezési csoporton) fut. Az alkalmazás titkos kulcsainak tárolására kulcstartót használhatunk. Az alkalmazás által az Azure AD-vel való hitelesítéshez használt rendszerindító tanúsítványt tároljuk.

Hozzáférésre van szükségünk a következő tárolt kulcsokhoz és titkos kulcsokhoz:
- **TLS/SSL-tanúsítvány**: TLS/SSL-hez használatos.
- **Tárolási kulcs**: a Storage-fiók eléréséhez használatos.
- **RSA 2 048 bites kulcs**: az Azure Storage által az adattitkosítási kulcs becsomagolására/kicsomagolására használatos.
- **Alkalmazás által felügyelt identitás**: az Azure ad-vel való hitelesítéshez használatos. A Key Vaulthoz való hozzáférés engedélyezése után az alkalmazás beolvashatja a tárolási kulcsot és a tanúsítványt.

Meg kell határoznia a következő szerepköröket annak meghatározásához, hogy ki kezelheti, telepítheti és naplózhatja az alkalmazást:
- **Biztonsági csapat**: a KSH (a biztonsági tisztviselő) vagy hasonló közreműködők irodájában dolgozó személyzet. A biztonsági csapat feladata a titkok megfelelő védelme. A titkok közé tartoznak a TLS/SSL-tanúsítványok, az RSA-kulcsok a titkosításhoz, a kapcsolati karakterláncok és a Storage-fiók kulcsainak.
- **Fejlesztők és operátorok**: az alkalmazást fejlesztő és az Azure-ban üzembe helyezett munkatársak. A csapat tagjai nem tartoznak a biztonsági személyzetbe. Nem férhetnek hozzá a bizalmas adatokhoz, például a TLS/SSL-tanúsítványokhoz és az RSA-kulcsokhoz. Csak az általuk telepített alkalmazások férhetnek hozzá a bizalmas adatokhoz.
- **Könyvvizsgálók**: Ez a szerepkör olyan közreműködők számára szól, akik nem tagjai a fejlesztési vagy általános informatikai munkatársainak. A biztonsági szabványoknak való megfelelés biztosítása érdekében áttekintik a tanúsítványok, kulcsok és titkok használatát és karbantartását.

Van egy másik szerepkör, amely az alkalmazás hatókörén kívül esik: az előfizetés (vagy erőforráscsoport) rendszergazdája. Az előfizetés-rendszergazda beállítja a kezdeti hozzáférési engedélyeket a biztonsági csapat számára. Hozzáférést biztosítanak a biztonsági csapathoz egy olyan erőforráscsoport használatával, amely az alkalmazás által igényelt erőforrásokkal rendelkezik.

A következő műveleteket kell engedélyeznie a szerepkörökhöz:

**Biztonsági csapat**
- Key vaultok létrehozása.
- Key Vault naplózás bekapcsolása.
- Kulcsok és titkos kódok hozzáadása.
- Biztonsági másolatokat készíthet a kulcsokról a vész-helyreállításhoz.
- Key Vault hozzáférési házirendek beállítása és szerepkörök hozzárendelése a felhasználók és alkalmazások számára adott műveletekhez szükséges engedélyek megadásához.
- Rendszeresen a kulcsokat és a titkokat.

**Fejlesztők és üzemeltetők**
- A rendszerindító és a TLS/SSL-tanúsítványok (ujjlenyomatai megfelelnek), a tárolási kulcs (titkos URI) és az RSA-kulcs (kulcs URI) a becsomagolás/kicsomagoláshoz való hivatkozásait a biztonsági csapattól kérheti le.
- Fejlesztheti és telepítheti az alkalmazást a tanúsítványok és titkos kódok programozott módon való eléréséhez.

**Ellenőrök**
- Tekintse át a Key Vault naplókat a kulcsok és titkok megfelelő használatának megerősítéséhez, valamint az adatbiztonsági szabványoknak való megfelelés ellenőrzéséhez.

A következő táblázat összefoglalja a szerepkörök és alkalmazások hozzáférési engedélyeit.

| Szerepkör | Felügyeleti sík engedélyei | Adatsík engedélyei – tár-hozzáférési szabályzatok | Adatsík engedélyei – Azure RBAC (előzetes verzió)  |
| --- | --- | --- | --- |
| Biztonsági csapat | [Key Vault közreműködő](../../role-based-access-control/built-in-roles.md#key-vault-contributor) | Tanúsítványok: minden művelet <br> Kulcsok: minden művelet <br> Titkok: minden művelet | [Key Vault rendszergazda (előzetes verzió)](../../role-based-access-control/built-in-roles.md#key-vault-administrator-preview) |
| Fejlesztők és &nbsp; operátorok | Key Vault üzembe helyezési engedély<br><br> **Megjegyzés**: ez az engedély lehetővé teszi, hogy a telepített virtuális gépek a kulcstartóból beolvassák a titkos kulcsokat. | Nincs | Nincs |
| Ellenőrök | Nincs | Tanúsítványok: lista <br> Kulcsok: listája<br>Titkos kulcsok: listája<br><br> **Megjegyzés**: ez az engedély lehetővé teszi, hogy a könyvvizsgálók megvizsgálják a naplókban nem kibocsátott kulcsok és titkos kódok attribútumait (címkéket, aktiválási dátumokat, lejárati dátumokat). | [Key Vault olvasó (előzetes verzió)](../../role-based-access-control/built-in-roles.md#key-vault-reader-preview) |
| Azure Storage-tárfiók neve | Nincs | Kulcsok: beolvasás, Listázás, wrapKey, unwrapKey <br> | [Titkosítási szolgáltatás titkosítása Key Vault](../../role-based-access-control/built-in-roles.md#key-vault-crypto-service-encryption-user-preview) |
| Alkalmazás | Nincs | Titkok: lekérés, Listázás <br> Tanúsítványok: lekérés, Listázás | [Key Vault olvasó (előzetes verzió)](../../role-based-access-control/built-in-roles.md#key-vault-reader-preview), [Key Vault Secret User (előzetes verzió)](../../role-based-access-control/built-in-roles.md#key-vault-secrets-user-preview) |

A három csoport szerepköreinek más erőforrásokhoz való hozzáférésre van szükségük Key Vault engedélyekkel együtt. A virtuális gépek (vagy a Azure App Service Web Apps funkciójának üzembe helyezéséhez) a fejlesztőknek és a kezelőknek telepíteniük kell a hozzáférést. A könyvvizsgálóknak olvasási hozzáféréssel kell rendelkezniük ahhoz a Storage-fiókhoz, ahol a Key Vault-naplókat tárolják.

Példánkban egy egyszerű forgatókönyvet ismertetünk. A valós életbeli forgatókönyvek összetettebbek lehetnek. Az engedélyeket a Key Vault igényei szerint módosíthatja. Feltételezzük, hogy a biztonsági csapat biztosítja a kulcs-és titkos referenciákat (URI-k és ujjlenyomatai megfelelnek), amelyeket az DevOps munkatársai használnak az alkalmazásaikban. A fejlesztőknek és az operátoroknak nincs szükségük adatsíkon való hozzáférésre. A Key Vault biztonságossá tételére koncentrálunk.

> [!NOTE]
> Ez a példa azt mutatja be, hogy a Key Vault hozzáférés hogyan legyen zárolva az éles környezetben. A fejlesztőknek saját előfizetéssel vagy erőforráscsoporthoz kell rendelkezniük, amely teljes körű engedélyekkel rendelkezik a tárolók, virtuális gépek és az alkalmazás fejlesztéséhez szükséges Storage-fiók kezeléséhez.

## <a name="resources"></a>Források

- [Információk az Azure Key Vaultról](overview.md)
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
- [Azure RBAC-vel](../../role-based-access-control/overview.md)
- [Privát kapcsolat](../../private-link/private-link-overview.md)

## <a name="next-steps"></a>Következő lépések

[Hitelesítés az Azure Key Vaulttal](authentication.md)

[Key Vault hozzáférési szabályzat kiosztása](assign-access-policy-portal.md)

[Azure-szerepkör kiosztása a kulcsokhoz, titkokhoz és tanúsítványokhoz való hozzáféréshez](rbac-guide.md)

[Key Vault-tűzfalak és virtuális hálózatok konfigurálása](network-security.md)

[Magánhálózati kapcsolat létesítése Key Vault](private-link-service.md)