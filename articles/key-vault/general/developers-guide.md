---
title: Az Azure Key Vault fejlesztői útmutatója
description: A fejlesztők a Azure Key Vault segítségével kezelhetik a titkosítási kulcsokat a Microsoft Azure környezetben.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: f2c0b82f14fd16ecb9eef163e7a263f4327cfc46
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364549"
---
# <a name="azure-key-vault-developers-guide"></a>Az Azure Key Vault fejlesztői útmutatója

Key Vault lehetővé teszi a bizalmas adatok biztonságos hozzáférését az alkalmazásokból:

- A kulcsok, titkos kódok és tanúsítványok védelem alatt állnak anélkül, hogy önnek kellene megírni a kódot, és könnyedén használhatja őket az alkalmazásokból.
- Lehetővé teszi, hogy az ügyfelek saját kulcsokat, titkos kulcsokat és tanúsítványokat saját tulajdonában és kezelésében biztosítsanak, így Ön az alapvető szoftveres funkciókra összpontosíthat. Ily módon az alkalmazások nem tartoznak az ügyfelek bérlőkulcsaiért, titkos kulcsaiért és tanúsítványaiért való felelősség vagy az esetleges felelősségekért.
- Az alkalmazás kulcsokat használhat az aláíráshoz és a titkosításhoz, de az alkalmazáson kívül is megtartja a kulcskezelést. A kulcsokkal kapcsolatos további információkért lásd: [About Keys (A kulcsokról)](../keys/about-keys.md)
- A hitelesítő adatokat, például jelszavakat, hozzáférési kulcsokat és SAS-jogkivonatokat úgy kezelheti, hogy titkos Key Vault tárolja őket, lásd: Információk a titkos [kulcsokról](../secrets/about-secrets.md)
- Tanúsítványok kezelése. További információ: Tudnivalók a [tanúsítványokról](../certificates/about-certificates.md)

További általános információk a [Azure Key Vault: Mi a Key Vault.](overview.md)

## <a name="public-previews"></a>Nyilvános előzetes verziók

Rendszeres időközönként kiadunk egy új előzetes verziójú Key Vault funkciót. Próbálja ki a nyilvános előzetes funkciókat, és küldje el véleményét a azurekeyvault@microsoft.com visszajelzési e-mail-címünk segítségével.

## <a name="creating-and-managing-key-vaults"></a>Kulcstartók létrehozása és kezelése

Key Vault azure-szolgáltatásokhoz hasonlóan a felügyelet az Azure Resource Manager történik. Az Azure Resource Manager az Azure üzembehelyezési és felügyeleti szolgáltatása. Egy olyan felügyeleti réteget biztosít, amely lehetővé teszi az Azure-fiókban lévő erőforrások létrehozását, frissítését és törlését. További információ: [Azure Resource Manager](../../azure-resource-manager/management/overview.md)

A felügyeleti réteghez való hozzáférést az [Azure szerepköralapú hozzáférés-vezérlése vezérli.](../../role-based-access-control/overview.md) A Key Vault felügyeleti rétegben, más néven felügyeleti vagy vezérlősíkként is ismert kulcstartókat és azok attribútumait hozhatja létre és kezelheti, beleértve a hozzáférési házirendeket, de nem az adatsíkon kezelt kulcsokat, titkos kulcsokat és tanúsítványokat. Előre meghatározott szerepkör használatával felügyeleti hozzáférést adhat `Key Vault Contributor` a Key Vault.     

**API-k és SDK-k a kulcstartó kezeléséhez:**

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referencia](/cli/azure/keyvault)<br>[Gyors útmutató](quick-create-cli.md)|[Referencia](/powershell/module/az.keyvault)<br>[Gyors útmutató](quick-create-powershell.md)|[Referencia](/rest/api/keyvault/)|[Referencia](/azure/templates/microsoft.keyvault/vaults)<br>[Gyors útmutató](./vault-create-template.md)|[Referencia](/dotnet/api/microsoft.azure.management.keyvault)|[Referencia](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[Referencia](/java/api/com.microsoft.azure.management.keyvault)|[Referencia](/javascript/api/@azure/arm-keyvault)|

A [telepítőcsomagokat és](client-libraries.md) a forráskódot lásd: Ügyfélkódtárak.

A felügyeleti síkról további Key Vault lásd: [Key Vault felügyeleti sík](./secure-your-key-vault.md#management-plane-and-azure-rbac)

## <a name="authenticate-to-key-vault-in-code"></a>Hitelesítés Key Vault kódban

Key Vault Azure AD-hitelesítést használ, amelyhez Azure AD-rendszerbiztonsági tag szükséges a hozzáférés megadásához. Az Azure AD rendszerbiztonsági tag lehet felhasználó, alkalmazás-szolgáltatásnév, [Azure-erőforrások](../../active-directory/managed-identities-azure-resources/overview.md)felügyelt identitása vagy bármilyen típusú rendszerbiztonsági tag csoportja.

### <a name="authentication-best-practices"></a>Ajánlott hitelesítési eljárások

Az Azure-ban üzembe helyezett alkalmazásokhoz ajánlott felügyelt identitást használni. Ha olyan Azure-szolgáltatásokat használ, amelyek nem támogatják a felügyelt identitást, vagy ha az alkalmazások a környezetben vannak telepítve, akkor [a](../../active-directory/develop/howto-create-service-principal-portal.md) tanúsítvánnyal is használható szolgáltatásnév. Ebben a forgatókönyvben a tanúsítványt egy tárolóban kell Key Vault gyakran elforgatni. A titkos okkal megadott szolgáltatásnév fejlesztési és tesztelési környezetekhez használható, és helyileg vagy Cloud Shell a felhasználónév használata javasolt.

Ajánlott rendszerbiztonsági tag környezetenként:
- **Éles környezet:**
  - Felügyelt identitás vagy szolgáltatásnév tanúsítvánnyal
- **Tesztelési és fejlesztési környezetek:**
  - Felügyelt identitás, szolgáltatásnév tanúsítvánnyal vagy szolgáltatásnévvel titkos okkal
- **Helyi fejlesztés:**
  - Egyszerű felhasználó vagy szolgáltatásnév titkos okkal

A fenti hitelesítési forgatókönyveket az **Azure Identity** ügyféloldali kódtára támogatja, és integrálva van Key Vault DK-okkal. Az Azure Identity Library a kód módosítása nélkül használható különböző környezetekben és platformokon. Az Azure Identity automatikusan lekéri a hitelesítési jogkivonatot az Azure-felhasználóba az Azure CLI, Visual Studio Visual Studio Code és egyebek használatával bejelentkezve. 

További információ az Azure Identity ügyféloldali könyvtárról:

**Azure Identity-ügyfélkódtárak**

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Azure Identity SDK .NET](/dotnet/api/overview/azure/identity-readme)|[Azure Identity SDK Python](/python/api/overview/azure/identity-readme)|[Azure Identity SDK Java](/java/api/overview/azure/identity-readme)|[Azure Identity SDK JavaScript](/javascript/api/overview/azure/identity-readme)|     

>[!Note]
> [Alkalmazáshitelesítési kódtár,](/dotnet/api/overview/azure/service-to-service-authentication) amelyet Key Vault .NET SDK 3-as verziójához ajánlott, amely jelenleg elavult. Kövesse az [AppAuthentication to Azure.Identity Migration Guidance (Az AppAuthentication to Azure.Identity migrálási](/dotnet/api/overview/azure/app-auth-migration) útmutatója) útmutatót az Key Vault .NET SDK 4-es verziójára való migráláshoz.

Az alkalmazásokban az alkalmazásokban Key Vault hitelesítését ismertető oktatóanyagokért lásd:
- [Hitelesítés Key Vault .NET-en virtuális gépen üzemeltetett alkalmazásban](./tutorial-net-virtual-machine.md)
- [Hitelesítés Key Vault pythonos virtuális gépen üzemeltetett alkalmazásban](./tutorial-python-virtual-machine.md)
- [Hitelesítés Key Vault App Service](./tutorial-net-create-vault-azure-web-app.md)

## <a name="manage-keys-certificates-and-secrets"></a>Kulcsok, tanúsítványok és titkos kulcsok kezelése

A kulcsokhoz, titkos kulcsokhoz és tanúsítványokhoz való hozzáférést adatsík vezérli. Az adatsík hozzáférés-vezérlése helyi tároló-hozzáférési szabályzatok vagy az Azure RBAC használatával is használhatja.

**Keys API-k és AZDK-k**

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referencia](/cli/azure/keyvault/key)<br>[Gyors útmutató](../keys/quick-create-cli.md)|[Referencia](/powershell/module/az.keyvault/)<br>[Gyors útmutató](../keys/quick-create-powershell.md)|[Referencia](/rest/api/keyvault/#key-operations)|[Referencia](/azure/templates/microsoft.keyvault/vaults/keys)<br>[Gyors útmutató](../keys/quick-create-template.md)|[Referencia](/dotnet/api/azure.security.keyvault.keys)<br>[Gyors útmutató](../keys/quick-create-net.md)|[Referencia](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[Gyors útmutató](../keys/quick-create-python.md)|[Referencia](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>[Gyors útmutató](../keys/quick-create-java.md)|[Referencia](/javascript/api/@azure/keyvault-keys/)<br>[Gyors útmutató](../keys/quick-create-node.md)|

**A Certificates API-k és az API-k**

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referencia](/cli/azure/keyvault/certificate)<br>[Gyors útmutató](../certificates/quick-create-cli.md)|[Referencia](/powershell/module/az.keyvault)<br>[Gyors útmutató](../certificates/quick-create-powershell.md)|[Referencia](/rest/api/keyvault/#certificate-operations)|N/A|[Referencia](/dotnet/api/azure.security.keyvault.certificates)<br>[Gyors útmutató](../certificates/quick-create-net.md)|[Referencia](/python/api/overview/azure/keyvault-certificates-readme)<br>[Gyors útmutató](../certificates/quick-create-python.md)|[Referencia](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>[Gyors útmutató](../certificates/quick-create-java.md)|[Referencia](/javascript/api/@azure/keyvault-certificates/)<br>[Gyors útmutató](../certificates/quick-create-node.md)|

**Titkos kulcsok API-i és AZDK-k**

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referencia](/cli/azure/keyvault/secret)<br>[Gyors útmutató](../secrets/quick-create-cli.md)|[Referencia](/powershell/module/az.keyvault/)<br>[Gyors útmutató](../secrets/quick-create-powershell.md)|[Referencia](/rest/api/keyvault/#secret-operations)|[Referencia](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[Gyors útmutató](../secrets/quick-create-template.md)|[Referencia](/dotnet/api/azure.security.keyvault.secrets)<br>[Gyors útmutató](../secrets/quick-create-net.md)|[Referencia](/python/api/overview/azure/keyvault-secrets-readme)<br>[Gyors útmutató](../secrets/quick-create-python.md)|[Referencia](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[Gyors útmutató](../secrets/quick-create-java.md)|[Referencia](/javascript/api/@azure/keyvault-secrets/)<br>[Gyors útmutató](../secrets/quick-create-node.md)|

A [telepítőcsomagokat és](client-libraries.md) a forráskódot lásd: Ügyfélkódtárak.

Az adatsík biztonságával kapcsolatos további Key Vault [](./secure-your-key-vault.md#data-plane-and-access-policies) az adatsík- és Key Vault-szabályzatok, valamint az adatsík és az [Azure RBAC](./secure-your-key-vault.md#data-plane-and-azure-rbac) Key Vault kapcsolatos Key Vault tartalmaz.

### <a name="code-examples"></a>Kódpéldák

Az alkalmazásokkal való Key Vault teljes példákért lásd:

- [Azure Key Vault kódminták –](https://azure.microsoft.com/resources/samples/?service=key-vault) Kódminták Azure Key Vault. 

## <a name="how-tos"></a>Használati útmutatók

A következő cikkek és forgatókönyvek feladatspecifikus útmutatást nyújtanak a Azure Key Vault:

- [Hozzáférés Key Vault](access-behind-firewall.md) tűzfal mögött – Egy kulcstartó eléréséhez a Key Vault-ügyfélalkalmazásnak több végponthoz is hozzá kell férni a különböző funkciókhoz.
- Tanúsítványok központi telepítése virtuális gépekre Key Vault – [Windows,](../../virtual-machines/extensions/key-vault-windows.md) [Linux](../../virtual-machines/extensions/key-vault-linux.md) – Egy Azure-beli virtuális gépen futó felhőalkalmazáshoz tanúsítványra van szükség. Hogyan jut el ez a tanúsítvány a virtuális géphez még ma?
- [Azure-webalkalmazás-tanúsítvány üzembe helyezése a Key Vault](../../app-service/configure-ssl-certificate.md#import-a-certificate-from-key-vault)
- Hozzáférési szabályzat hozzárendelése ([](assign-access-policy-cli.md)  |  [parancssori felület PowerShell-portálja).](assign-access-policy-powershell.md)  |  [](assign-access-policy-portal.md) 
- [A Key Vault](./key-vault-recovery.md) törlés és a CLI használata végigvezeti a kulcstartó és a különféle kulcstartó-objektumok használatán és életciklusán, amelyeken engedélyezve van a soft-delete.
- Biztonságos [értékek (például jelszavak)](../../azure-resource-manager/templates/key-vault-parameter.md) megadása az üzembe helyezés során – Ha az üzembe helyezés során biztonságos értéket (például jelszót) kell megadnia paraméterként, ezt az értéket titkosként tárolhatja egy Azure Key Vault-ban, és hivatkozhat rá más Resource Manager-sablonokban.

## <a name="integrated-with-key-vault"></a>Integrálva Key Vault

Ezek a cikkek olyan egyéb forgatókönyvekről és szolgáltatásokról szólnak, amelyek az Key Vault.

- [Az adattitkosítás](../../security/fundamentals/encryption-atrest.md) lehetővé teszi az adatok titkosítását (titkosítását) az adatok megőrzését. Az adattitkosítási kulcsokat gyakran egy kulcstitkosítási kulccsal titkosítják a Azure Key Vault korlátozza a hozzáférést.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) lehetővé teszi a saját bérlőkulcsa felett való felettesét. Például ahelyett, hogy a Microsoft felügyeli a bérlőkulcsot (ez az alapértelmezett beállítás), kezelheti a saját bérlőkulcsát, hogy megfeleljen a szervezetre vonatkozó konkrét szabályozásnak. A saját bérlőkulcs felügyelete más néven a saját kulcs használata (BYOK).
- [Azure Private Link Szolgáltatás](private-link-service.md) lehetővé teszi az Azure-szolgáltatások (például az Azure Key Vault, az Azure Storage és az Azure Cosmos DB) és az Azure által üzemeltetett ügyfél-/partnerszolgáltatások privát végponton keresztüli hozzáférését a virtuális hálózatban.
- Key Vault [integrációja](../../event-grid/event-schema-key-vault.md)  Event Grid lehetővé teszi, hogy a felhasználók értesítést kapnak, ha a Key Vaultban tárolt titkos kulcsok állapota megváltozott. A kimaradások elkerülése érdekében terjesztheti a titkos kulcsok új verzióját az alkalmazások között, vagy elforgathatja a lejárathoz közeli titkos kulcsok között.
- Megvédheti Az [Azure DevOps titkos](/azure/devops/pipelines/release/azure-key-vault) kulcsok a nemkívánatos hozzáféréstől a Key Vault.
- [A DataBricksben Key Vault titkos adatokat az Azure Storage-hoz való csatlakozáshoz](./integrate-databricks-blob-storage.md)
- Konfigurálja és futtassa a Azure Key Vault-szolgáltatót a Kubernetes titkos kulcsok [tárolójának](./key-vault-integrate-kubernetes.md) CSI-illesztőprogramjához

## <a name="key-vault-overviews-and-concepts"></a>Key Vault és alapfogalmak áttekintése

- [Key Vault helyreállított](soft-delete-overview.md) törlési viselkedés egy olyan funkciót ír le, amely lehetővé teszi a törölt objektumok helyreállítását, akár véletlen, akár szándékos volt a törlés.
- [Key Vault ügyfél szabályozása](overview-throttling.md) a szabályozás alapfogalmait és az alkalmazáshoz való megközelítést kínál.
- [Key Vault biztonsági világok](overview-security-worlds.md) a régiók és a biztonsági területek közötti kapcsolatokat ismertetik.

## <a name="social"></a>Közösségi

- [Key Vault Blog](/archive/blogs/kv/)
- [Key Vault fórum](https://aka.ms/kvforum)