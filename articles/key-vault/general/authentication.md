---
title: Hitelesítés az Azure Key Vaulttal
description: Útmutató a hitelesítéshez a Azure Key Vault
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/31/2021
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 7d219b752b894bbce9815911658c804ecb850ea1
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753433"
---
# <a name="authenticate-to-azure-key-vault"></a>Hitelesítés az Azure Key Vaulttal

Azure Key Vault lehetővé teszi, hogy titkos adatokat tároljon, és egy központosított, biztonságos felhőadattárban vezérelje azok terjesztését, így nincs szükség hitelesítő adatok tárolására az alkalmazásokban. Az alkalmazásoknak csak a Key Vault kell hitelesíteniük a titkos kulcsok eléréséhez.

## <a name="app-identity-and-security-principals"></a>Alkalmazásidentitás és rendszerbiztonsági tag

A Key Vault a Azure Active Directory [(Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md)használatával működik, amely az adott rendszerbiztonsági tag **identitásának hitelesítéséért felelős.**

A rendszerbiztonsági tag olyan objektum, amely az Azure-erőforrásokhoz hozzáférést kérő felhasználót, csoportot, szolgáltatást vagy alkalmazást jelöli. Az Azure minden rendszerbiztonsági taghoz egyedi **objektumazonosítót** rendel.

* A **felhasználói rendszerbiztonsági** tag azonosít egy felhasználót, aki profillal rendelkezik a Azure Active Directory.

* A **csoportbiztonsági** tag azonosítja a Azure Active Directory. A csoporthoz rendelt szerepkörök és engedélyek a csoport összes felhasználója számára meg vannak osztva.

* A **szolgáltatásnév** egy olyan rendszerbiztonsági tag, amely egy alkalmazást vagy szolgáltatást, azaz egy kód egy darabját identitást biztosít felhasználó vagy csoport helyett. A szolgáltatásnév objektumazonosítóját ügyfél-azonosítónak nevezik, és felhasználónévként viselkedik.  A szolgáltatásnév titkos **ügyféltitkja** a jelszavaként működik.

Az alkalmazások esetében a szolgáltatásnév beszerzésének két módja van:

* Ajánlott: engedélyezze a rendszer által hozzárendelt **felügyelt identitást** az alkalmazáshoz.

    A felügyelt identitással az Azure belsőleg felügyeli az alkalmazás szolgáltatását, és automatikusan hitelesíti az alkalmazást más Azure-szolgáltatásokkal. A felügyelt identitás a különböző szolgáltatásokban üzembe helyezett alkalmazásokhoz érhető el.

    További információ: A felügyelt identitás [áttekintése.](../../active-directory/managed-identities-azure-resources/overview.md) Lásd [](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)még a felügyelt identitást támogató Azure-szolgáltatásokat, amelyek olyan cikkekre mutató hivatkozásokat tartalmaznak, amelyek ismertetik, hogyan engedélyezhető a felügyelt identitás adott szolgáltatásokhoz (például App Service, Azure Functions, Virtual Machines stb.).

* Ha nem tudja használni a  felügyelt identitást, regisztrálja az alkalmazást az Azure AD-bérlőben a rövid útmutató: Alkalmazás regisztrálása az Azure identitásplatformjáncímen [leírtak szerint.](../../active-directory/develop/quickstart-register-app.md) A regisztráció egy második alkalmazásobjektumot is létrehoz, amely azonosítja az alkalmazást az összes bérlőn.

## <a name="authorize-a-security-principal-to-access-key-vault"></a>Rendszerbiztonsági tag hozzáférésének Key Vault

Key Vault két különböző hitelesítési szinttel működik:

- A **hozzáférési** szabályzatok azt szabályozják, hogy egy felhasználó, csoport vagy  szolgáltatásnév hozzáférhet-e a titkos kulcsokhoz, kulcsokhoz és tanúsítványokhoz egy meglévő Key Vault-erőforráson belül (más néven "adatsíkműveletekkel"). A hozzáférési szabályzatok általában a felhasználók, csoportok és alkalmazások számára vannak megszabadva.

    A hozzáférési szabályzatok hozzárendelését a következő cikkekben talál:

    - [Azure Portal](assign-access-policy-portal.md)
    - [Azure CLI](assign-access-policy-cli.md)
    - [Azure PowerShell](assign-access-policy-portal.md)

- A **szerepkör-engedélyek** azt ják meg, hogy egy felhasználó, csoport vagy szolgáltatásnév jogosult-e Key Vault-erőforrás létrehozására, törlésére és egyéb kezelésére (más néven "felügyeleti síkon" típusú műveletekre). Az ilyen szerepköröket leggyakrabban csak a rendszergazdák kapják meg.
 
    A szerepkörök hozzárendeléséhez és kezeléséhez tekintse meg a következő cikkeket:

    - [Azure Portal](../../role-based-access-control/role-assignments-portal.md)
    - [Azure CLI](../../role-based-access-control/role-assignments-cli.md)
    - [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)

    A szerepkörökkel kapcsolatos általános információkért lásd: [Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../../role-based-access-control/overview.md).


> [!IMPORTANT]
> A legnagyobb biztonság érdekében mindig kövesse a legkisebb jogosultságok rendszerbiztonsági tagját, és csak a szükséges konkrét hozzáférési házirendeket és szerepköröket adja meg. 
    
## <a name="configure-the-key-vault-firewall"></a>Az Key Vault konfigurálása

Alapértelmezés szerint a Key Vault nyilvános IP-címeken keresztül engedélyezi az erőforrásokhoz való hozzáférést. A nagyobb biztonság érdekében korlátozhatja a hozzáférést adott IP-tartományokra, szolgáltatásvégpontokra, virtuális hálózatokra vagy privát végpontokra.

További információ: [Azure Key Vault tűzfal mögötti hozzáférési adatok.](./access-behind-firewall.md)


## <a name="the-key-vault-authentication-flow"></a>A Key Vault hitelesítési folyamat

1. A szolgáltatásnév az Azure AD-val való hitelesítést kéri, például:
    * A felhasználó felhasználónévvel és jelszóval Azure Portal jelentkezik be a fiókba.
    * Az alkalmazások meghívnak egy Azure-REST API, amely egy ügyfél-azonosítót és egy titkos ügyfélszámítógépet vagy egy ügyfél-tanúsítványt ad meg.
    * Egy Azure-erőforrás, például egy felügyelt identitással rendelkezik virtuális gép, kapcsolatba lép az [Azure Instance Metadata Service (IMDS) REST-végponttal](../../virtual-machines/windows/instance-metadata-service.md) egy hozzáférési jogkivonat beszerzése érdekében.

1. Ha az Azure AD-val való hitelesítés sikeres, a szolgáltatásnév OAuth-jogkivonatot kap.

1. A szolgáltatásnév a szolgáltatásvégpontján (URI Key Vault REST API keresztül Key Vault hívást a szolgáltatásnévnek.

1. Key Vault tűzfal a következő feltételeket ellenőrzi. Ha bármely feltétel teljesül, a hívás engedélyezett. Ellenkező esetben a hívás blokkolva lesz, és a rendszer tiltott választ ad vissza.

    * A tűzfal le van tiltva, és a Key Vault nyilvános végpontja elérhető a nyilvános internetről.
    * A hívó egy [megbízható Key Vault szolgáltatás,](./overview-vnet-service-endpoints.md#trusted-services)amely lehetővé teszi a tűzfal megkerülését.
    * A hívó IP-cím, virtuális hálózat vagy szolgáltatásvégpont alapján szerepel a tűzfalon.
    * A hívó egy konfigurált Key Vault kapcsolaton keresztül elérheti a kapcsolatot.    

1. Ha a tűzfal engedélyezi a hívást, Key Vault az Azure AD-t a szolgáltatásnév hozzáférési jogkivonatának érvényesítéséhez.

1. Key Vault ellenőrzi, hogy a szolgáltatásnév rendelkezik-e a kért művelethez szükséges hozzáférési szabályzatokkal. Ha nem, a Key Vault tiltott választ ad vissza.

1. Key Vault végrehajtja a kért műveletet, és visszaadja az eredményt.

Az alábbi ábra egy olyan alkalmazás folyamatát mutatja be, amely egy titkos Key Vault "Get Secret" API-t hív meg:

![A Azure Key Vault hitelesítési folyamat](../media/authentication/authentication-flow.png)

> [!NOTE]
> Key Vault, tanúsítványok és kulcsok SDK-ügyfelei további hívásokat hoznak a Key Vault-hoz hozzáférési jogkivonat nélkül, ami 401-es választ ad a bérlői adatok lekérésére. További információ: [Hitelesítés, kérések és válaszok](authentication-requests-and-responses.md)

## <a name="code-examples"></a>Kódpéldák

Az alábbi táblázatban található hivatkozások különböző cikkekre mutatnak, amelyek bemutatják, hogyan lehet Key Vault az alkalmazáskódban az adott nyelvHez tartozó Azure SDK-kódtárak használatával. Az egyszerűség kedvéért más felületek is Azure Portal, mint az Azure CLI és a Azure Portal.

| Key Vault titkos kulcsok | Key Vault kulcsok | Key Vault tanúsítványok |
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
