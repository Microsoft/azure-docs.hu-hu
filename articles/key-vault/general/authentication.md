---
title: Hitelesítés az Azure Key Vaulttal
description: Útmutató a Azure Key Vault való hitelesítéshez
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 08/27/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: ed83d52d19df126ef9b0e68f984f88f5dfd40c42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99492940"
---
# <a name="authenticate-to-azure-key-vault"></a>Hitelesítés az Azure Key Vaulttal

Azure Key Vault lehetővé teszi a titkok tárolását és a terjesztésük irányítását egy központi, biztonságos Felhőbeli tárházban, amely szükségtelenné teszi a hitelesítő adatok tárolását az alkalmazásokban. Az alkalmazásoknak csak a Key Vault futtatásával kell hitelesíteniük a titkok eléréséhez.

## <a name="app-identity-and-security-principals"></a>Alkalmazás-identitás és rendszerbiztonsági tag

A Key Vault-alapú hitelesítés az [Azure Active Directory (Azure ad)](../../active-directory/fundamentals/active-directory-whatis.md)szolgáltatással együtt működik, amely az adott **rendszerbiztonsági tag** identitásának hitelesítéséhez felelős.

A rendszerbiztonsági tag egy olyan objektum, amely az Azure-erőforrásokhoz hozzáférést kérő felhasználót, csoportot, szolgáltatást vagy alkalmazást jelöl. Az Azure egy egyedi **objektumazonosítót** rendel minden rendszerbiztonsági tag számára.

* A **felhasználói** rendszerbiztonsági tag azonosítja azt a személyt, aki Azure Active Directory profillal rendelkezik.

* A **csoport** rendszerbiztonsági tagja a Azure Active Directoryban létrehozott felhasználók készletét azonosítja. A csoportba tartozó összes szerepkör vagy engedély a csoport összes felhasználója számára elérhető.

* Az **egyszerű szolgáltatásnév** olyan rendszerbiztonsági tag, amely egy alkalmazás vagy szolgáltatás identitását jelenti, azaz egy kódrészletet, amely nem felhasználó vagy csoport. Egy egyszerű szolgáltatásnév AZONOSÍTÓjának neve **ügyfél-azonosítóként** működik, és a neve, mint a felhasználóneve. Az egyszerű szolgáltatás **ügyfél-titka** úgy viselkedik, mint a jelszava.

Alkalmazások esetében kétféleképpen szerezhet be egyszerű szolgáltatást:

* Ajánlott: engedélyezze a rendszerhez rendelt **felügyelt identitást** az alkalmazáshoz.

    A felügyelt identitással az Azure belsőleg kezeli az alkalmazás egyszerű szolgáltatását, és automatikusan hitelesíti az alkalmazást más Azure-szolgáltatásokkal. A felügyelt identitás számos szolgáltatáshoz központilag telepített alkalmazásokhoz érhető el.

    További információ: a [felügyelt identitás áttekintése](../../active-directory/managed-identities-azure-resources/overview.md). Tekintse meg a [felügyelt identitást támogató Azure-szolgáltatásokat](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)is, amelyek az adott szolgáltatások felügyelt identitásának engedélyezését írják le (például App Service, Azure Functions, Virtual Machines stb.).

* Ha nem tudja használni a felügyelt identitást, az alkalmazást az Azure AD-Bérlővel **regisztrálja** , a gyors útmutató [: alkalmazás regisztrálása az Azure Identity platformon](../../active-directory/develop/quickstart-register-app.md)című témakörben leírtak szerint. A regisztráció egy második alkalmazásobjektum-objektumot is létrehoz, amely az alkalmazást az összes bérlőn azonosítja.

## <a name="authorize-a-security-principal-to-access-key-vault"></a>Rendszerbiztonsági tag engedélyezése Key Vaulthoz való hozzáféréshez

Key Vault két külön engedélyezési szinttel működik:

- A **hozzáférési házirendek** határozzák meg, hogy egy felhasználó, csoport vagy egyszerű szolgáltatás jogosult-e a titkok, kulcsok és tanúsítványok elérésére egy meglévő Key Vault erőforráson *belül* (más néven "adatsík" művelet). A hozzáférési házirendek általában a felhasználók, csoportok és alkalmazások számára adhatók meg.

    Hozzáférési szabályzatok hozzárendeléséhez tekintse meg a következő cikkeket:

    - [Azure Portal](assign-access-policy-portal.md)
    - [Azure CLI](assign-access-policy-cli.md)
    - [Azure PowerShell](assign-access-policy-portal.md)

- A **szerepkör-engedélyek** azt szabályozzák, hogy egy felhasználó, csoport vagy egyszerű szolgáltatásnév jogosult-e a Key Vault erőforrás létrehozására, törlésére és egyéb kezelésére (más néven "felügyeleti sík" műveletre). Ezeket a szerepköröket leggyakrabban csak a rendszergazdák kapják meg.
 
    A szerepkörök hozzárendeléséhez és kezeléséhez tekintse meg a következő cikkeket:

    - [Azure Portal](../../role-based-access-control/role-assignments-portal.md)
    - [Azure CLI](../../role-based-access-control/role-assignments-cli.md)
    - [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)

    Key Vault jelenleg támogatja a [közreműködő](../../role-based-access-control/built-in-roles.md#key-vault-contributor) szerepkört, amely lehetővé teszi a felügyeleti műveleteket Key Vault erőforrásokon. Számos más szerepkör jelenleg előzetes verzióban érhető el. Egyéni szerepköröket is létrehozhat az [Azure Custom roles](../../role-based-access-control/custom-roles.md)című témakörben leírtak szerint.

    A szerepkörökkel kapcsolatos általános információkért lásd: [Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../../role-based-access-control/overview.md).


> [!IMPORTANT]
> A legnagyobb biztonság érdekében mindig kövesse a legalacsonyabb szintű jogosultságokat, és csak a legpontosabban szükséges hozzáférési házirendeket és szerepköröket adja meg. 
    
## <a name="configure-the-key-vault-firewall"></a>A Key Vault tűzfal konfigurálása

Alapértelmezés szerint a Key Vault nyilvános IP-címeken keresztül engedélyezi az erőforrásokhoz való hozzáférést. A nagyobb biztonság érdekében az adott IP-tartományokra, szolgáltatási végpontokra, virtuális hálózatokra vagy magánhálózati végpontokra is korlátozhatja a hozzáférést.

További információ: [hozzáférés Azure Key Vault tűzfal mögött](./access-behind-firewall.md).


## <a name="the-key-vault-authentication-flow"></a>A Key Vault hitelesítési folyamat

1. Egyszerű szolgáltatás kérése az Azure AD-vel való hitelesítéshez, például:
    * A felhasználó felhasználónévvel és jelszóval jelentkezik be a Azure Portalba.
    * Egy alkalmazás meghívja az Azure REST API, amely egy ügyfél-azonosítót és egy titkos kulcsot vagy egy ügyféltanúsítványt mutat be.
    * Egy Azure-erőforrás, például egy felügyelt identitású virtuális gép kapcsolatba lép az [azure instance metadata Service (IMDS)](../../virtual-machines/windows/instance-metadata-service.md) Rest-végponttal egy hozzáférési jogkivonat beszerzéséhez.

1. Ha az Azure AD-val való hitelesítés sikeres, az egyszerű szolgáltatásnév OAuth-tokent kap.

1. Az egyszerű szolgáltatásnév a Key Vault végpontján (URI) keresztül kezdeményezi a Key Vault REST API.

1. Key Vault a tűzfal a következő feltételeket ellenőrzi. Ha bármelyik feltétel teljesül, a hívás engedélyezett. Ellenkező esetben a rendszer letiltja a hívást, és egy tiltott választ ad vissza.

    * A tűzfal le van tiltva, és a Key Vault nyilvános végpontja elérhető a nyilvános internetről.
    * A hívó egy [Key Vault megbízható szolgáltatás](./overview-vnet-service-endpoints.md#trusted-services), amely lehetővé teszi a tűzfal megkerülését.
    * A hívó a tűzfalon IP-cím, virtuális hálózat vagy szolgáltatási végpont alapján jelenik meg.
    * A hívó elérheti Key Vault egy konfigurált magánhálózati kapcsolaton keresztül.    

1. Ha a tűzfal engedélyezi a hívást, Key Vault meghívja az Azure AD-t, hogy ellenőrizze az egyszerű szolgáltatásnév hozzáférési jogkivonatát.

1. Key Vault ellenőrzi, hogy az egyszerű szolgáltatásnév rendelkezik-e a kért művelethez szükséges hozzáférési házirenddel. Ha nem, Key Vault egy tiltott választ ad vissza.

1. Key Vault végrehajtja a kért műveletet, és visszaadja az eredményt.

A következő ábra egy olyan alkalmazás folyamatát szemlélteti, amely egy Key Vault "titkos kulcs beszerzése" API-t hív meg:

![A Azure Key Vault hitelesítési folyamat](../media/authentication/authentication-flow.png)

> [!NOTE]
> Key Vault a titkokhoz, tanúsítványokhoz és kulcsokhoz tartozó SDK-ügyfelek további hívást biztosítanak Key Vault hozzáférési jogkivonat nélkül, ami 401 választ kér a bérlői adatok lekérésére. További információ: [hitelesítés, kérések és válaszok](authentication-requests-and-responses.md)

## <a name="code-examples"></a>Kódpéldák

Az alábbi táblázat különböző cikkekre mutat, amelyek bemutatják, hogyan használható az alkalmazás kódjában Key Vault az Azure SDK-kódtárak használatával az adott nyelvhez. Az egyéb felületek, például az Azure CLI és a Azure Portal is kényelmi funkciókat biztosítanak.

| Key Vault titkok | Kulcsok Key Vault | Tanúsítványok Key Vault |
|  --- | --- | --- |
| [Python](../secrets/quick-create-python.md) | [Python](../keys/quick-create-python.md) | [Python](../certificates/quick-create-python.md) | 
| [.NET](../secrets/quick-create-net.md) | [.NET](../keys/quick-create-net.md) | [.NET](../certificates/quick-create-net.md) |
| [Java](../secrets/quick-create-java.md) | [Java](../keys/quick-create-java.md) | [Java](../certificates/quick-create-java.md) |
| [JavaScript](../secrets/quick-create-node.md) | [JavaScript](../keys/quick-create-node.md) | [JavaScript](../certificates/quick-create-node.md) | 
| [Azure Portal](../secrets/quick-create-portal.md) | [Azure Portal](../keys/quick-create-portal.md) | [Azure Portal](../certificates/quick-create-portal.md) |
| [Azure CLI](../secrets/quick-create-cli.md) | [Azure CLI](../keys/quick-create-cli.md) | [Azure CLI](../certificates/quick-create-cli.md) |
| [Azure PowerShell](../secrets/quick-create-powershell.md) | [Azure PowerShell](../keys/quick-create-powershell.md) | [Azure PowerShell](../certificates/quick-create-powershell.md) |
| [ARM-sablon](../secrets/quick-create-net.md) | -- | -- |

## <a name="next-steps"></a>Következő lépések

- [Key Vault hozzáférési szabályzat hibaelhárítása](troubleshooting-access-issues.md)
- [Key Vault REST API hibakódok](rest-error-codes.md)
- [Key Vault fejlesztői útmutató](developers-guide.md)
- [Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../../role-based-access-control/overview.md)
