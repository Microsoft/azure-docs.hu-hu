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
ms.openlocfilehash: 08ac1ae09741b63648aec2b51b6a774a46b9af7c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818439"
---
# <a name="azure-key-vault-developers-guide"></a>Az Azure Key Vault fejlesztői útmutatója

Key Vault lehetővé teszi a bizalmas információk biztonságos hozzáférését az alkalmazásokból:

- A kulcsok, titkos kulcsok és tanúsítványok védelme a kód saját megírása nélkül is védett, és könnyen használhatja őket az alkalmazásokból.
- Lehetővé teszi, hogy az ügyfelek saját kulcsokat, titkos kulcsokat és tanúsítványokat tudjanak tulajdonában tartani és kezelni, így Ön az alapvető szoftveres funkciókra összpontosíthat. Ily módon az alkalmazások nem tartoznak az ügyfelek bérlőkulcsaiért, titkos kulcsaiért és tanúsítványaiért, és nem vállalnak felelősséget ezekért a felelősségekért.
- Az alkalmazás kulcsokat használhat az aláíráshoz és a titkosításhoz, de megtartja a kulcskezelést az alkalmazáson kívül. A kulcsokkal kapcsolatos további információkért lásd: [About Keys (A kulcsokról)](../keys/about-keys.md)
- A hitelesítő adatokat, például jelszavakat, hozzáférési kulcsokat és SAS-jogkivonatokat úgy kezelheti, hogy titkos Key Vault tárolja őket, lásd: Információk a titkos [kulcsokról](../secrets/about-secrets.md)
- Tanúsítványok kezelése. További információ: Tudnivalók a [tanúsítványokról](../certificates/about-certificates.md)

További általános információk a [Azure Key Vault: Mi a Key Vault.](overview.md)

## <a name="public-previews"></a>Nyilvános előzetes verziók

Rendszeres időközönként kiadunk egy új előzetes verziójú Key Vault funkciót. Próbálja ki a nyilvános előzetes verzió funkcióit, és küldje el véleményét a azurekeyvault@microsoft.com visszajelzési e-mail-címünk segítségével.

## <a name="creating-and-managing-key-vaults"></a>Kulcstartók létrehozása és kezelése

Key Vault Azure-szolgáltatásokhoz hasonlóan a felügyelet is egy Azure Resource Manager történik. Az Azure Resource Manager az Azure üzembehelyezési és felügyeleti szolgáltatása. Egy olyan felügyeleti réteget biztosít, amely lehetővé teszi az Azure-fiókban lévő erőforrások létrehozását, frissítését és törlését. További információ: [Azure Resource Manager](../../azure-resource-manager/management/overview.md)

A felügyeleti réteghez való hozzáférést az [Azure szerepköralapú hozzáférés-vezérlése vezérli.](../../role-based-access-control/overview.md) A Key Vault felügyeleti rétegben, más néven felügyeleti vagy vezérlősíkként is ismert kulcstartókat és azok attribútumait hozhatja létre és kezelheti, beleértve a hozzáférési házirendeket, de a kulcsokat, titkos kulcsokat és tanúsítványokat, amelyeket az adatsíkon kezel. Előre meghatározott szerepkör használatával felügyeleti hozzáférést adhat `Key Vault Contributor` a Key Vault.     

**API-k és SDK-k a Key Vault kezeléséhez:**

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referencia](/cli/azure/keyvault)<br>[Gyors útmutató](quick-create-cli.md)|[Referencia](/powershell/module/az.keyvault)<br>[Gyors útmutató](quick-create-powershell.md)|[Referencia](/rest/api/keyvault/)|[Referencia](/azure/templates/microsoft.keyvault/vaults)<br>[Gyors útmutató](./vault-create-template.md)|[Referencia](/dotnet/api/microsoft.azure.management.keyvault)|[Referencia](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[Referencia](/java/api/com.microsoft.azure.management.keyvault)|[Referencia](/javascript/api/@azure/arm-keyvault)|

A [telepítőcsomagokat és](client-libraries.md) a forráskódot lásd: Ügyfélkódtárak.

A felügyeleti síkról további Key Vault lásd: Azure Key Vault [biztonsági funkciók](security-features.md)

## <a name="authenticate-to-key-vault-in-code"></a>Hitelesítés Key Vault kódban

Key Vault Azure AD-hitelesítést használ, amelyhez Azure AD-rendszerbiztonsági tag szükséges a hozzáférés megadásához. Az Azure AD rendszerbiztonsági tagjai lehetnek felhasználók, alkalmazás-szolgáltatásnév, [Azure-erőforrások](../../active-directory/managed-identities-azure-resources/overview.md)felügyelt identitásai, vagy bármilyen típusú rendszerbiztonsági tag csoportja.

### <a name="authentication-best-practices"></a>Ajánlott hitelesítési eljárások

Az Azure-ban üzembe helyezett alkalmazásokhoz javasolt felügyelt identitást használni. Ha olyan Azure-szolgáltatásokat használ, amelyek nem támogatják a felügyelt identitást, vagy ha alkalmazások vannak telepítve a [környezetben,](../../active-directory/develop/howto-create-service-principal-portal.md) akkor a tanúsítvánnyal is rendelkezik szolgáltatásnév. Ebben a forgatókönyvben a tanúsítványt a Key Vault kell tárolni, és gyakran kell elforgatni. A titkos okkal megadott szolgáltatásnév fejlesztési és tesztelési környezetekhez használható, és helyileg vagy Cloud Shell felhasználói rendszerbiztonsági tag használata ajánlott.

Ajánlott rendszerbiztonsági tagok környezetenként:
- **Éles környezet:**
  - Felügyelt identitás vagy szolgáltatásnév tanúsítvánnyal
- **Tesztelési és fejlesztési környezetek:**
  - Felügyelt identitás, szolgáltatásnév tanúsítvánnyal vagy titkos okkal
- **Helyi fejlesztés:**
  - Egyszerű felhasználó vagy szolgáltatásnév titkos okkal

A fenti hitelesítési forgatókönyveket az **Azure Identity** ügyféloldali kódtára támogatja, és integrálva van Key Vault SZOFTVERDK-okkal. Az Azure Identity Library a kód módosítása nélkül használható különböző környezetekben és platformokon. Az Azure Identity emellett automatikusan lekéri a hitelesítési jogkivonatot az Azure CLI, a Visual Studio, a Visual Studio Code és egyebek használatával az Azure-felhasználóba bejelentkezve. 

További információ az Azure Identity ügyféloldali könyvtárról:

**Azure Identity-ügyfélkódtárak**

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Azure Identity SDK .NET](/dotnet/api/overview/azure/identity-readme)|[Azure Identity SDK Python](/python/api/overview/azure/identity-readme)|[Azure Identity SDK Java](/java/api/overview/azure/identity-readme)|[Azure Identity SDK JavaScript](/javascript/api/overview/azure/identity-readme)|     

>[!Note]
> [Alkalmazáshitelesítési](/dotnet/api/overview/azure/service-to-service-authentication) kódtár, amelyet a Key Vault .NET SDK 3-as verziójához ajánlott, amely jelenleg elavult. A .NET SDK 4-es verziójára való migráláshoz kövesse az [AppAuthentication to Azure.Identity Migration Key Vault Guidance (Az AppAuthentication to Azure.Identity](/dotnet/api/overview/azure/app-auth-migration) migrálási útmutatója) útmutatót.

Az alkalmazásokban az alkalmazásokban Key Vault hitelesítését ismertető oktatóanyagokért lásd:
- [Hitelesítés a Key Vault .NET-en virtuális gépen üzemeltetett alkalmazásban](./tutorial-net-virtual-machine.md)
- [Hitelesítés Key Vault Pythonban virtuális gépen üzemeltetett alkalmazásban](./tutorial-python-virtual-machine.md)
- [Hitelesítés Key Vault App Service](./tutorial-net-create-vault-azure-web-app.md)

## <a name="manage-keys-certificates-and-secrets"></a>Kulcsok, tanúsítványok és titkos kulcsok kezelése

A kulcsokhoz, titkos kulcsokhoz és tanúsítványokhoz való hozzáférést adatsík vezérli. Az adatsík hozzáférés-vezérlése helyi tároló-hozzáférési szabályzatokkal vagy az Azure RBAC-vel használhatja.

**Keys API-k és AZDK-k**

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referencia](/cli/azure/keyvault/key)<br>[Gyors útmutató](../keys/quick-create-cli.md)|[Referencia](/powershell/module/az.keyvault/)<br>[Gyors útmutató](../keys/quick-create-powershell.md)|[Referencia](/rest/api/keyvault/#key-operations)|[Referencia](/azure/templates/microsoft.keyvault/vaults/keys)<br>[Gyors útmutató](../keys/quick-create-template.md)|[Referencia](/dotnet/api/azure.security.keyvault.keys)<br>[Gyors útmutató](../keys/quick-create-net.md)|[Referencia](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[Gyors útmutató](../keys/quick-create-python.md)|[Referencia](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>[Gyors útmutató](../keys/quick-create-java.md)|[Referencia](/javascript/api/@azure/keyvault-keys/)<br>[Gyors útmutató](../keys/quick-create-node.md)|

**Certificates API-k és AZDK-k**

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referencia](/cli/azure/keyvault/certificate)<br>[Gyors útmutató](../certificates/quick-create-cli.md)|[Referencia](/powershell/module/az.keyvault)<br>[Gyors útmutató](../certificates/quick-create-powershell.md)|[Referencia](/rest/api/keyvault/#certificate-operations)|N/A|[Referencia](/dotnet/api/azure.security.keyvault.certificates)<br>[Gyors útmutató](../certificates/quick-create-net.md)|[Referencia](/python/api/overview/azure/keyvault-certificates-readme)<br>[Gyors útmutató](../certificates/quick-create-python.md)|[Referencia](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>[Gyors útmutató](../certificates/quick-create-java.md)|[Referencia](/javascript/api/@azure/keyvault-certificates/)<br>[Gyors útmutató](../certificates/quick-create-node.md)|

**Titkos kulcsok API-i és AZDK-k**

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referencia](/cli/azure/keyvault/secret)<br>[Gyors útmutató](../secrets/quick-create-cli.md)|[Referencia](/powershell/module/az.keyvault/)<br>[Gyors útmutató](../secrets/quick-create-powershell.md)|[Referencia](/rest/api/keyvault/#secret-operations)|[Referencia](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[Gyors útmutató](../secrets/quick-create-template.md)|[Referencia](/dotnet/api/azure.security.keyvault.secrets)<br>[Gyors útmutató](../secrets/quick-create-net.md)|[Referencia](/python/api/overview/azure/keyvault-secrets-readme)<br>[Gyors útmutató](../secrets/quick-create-python.md)|[Referencia](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[Gyors útmutató](../secrets/quick-create-java.md)|[Referencia](/javascript/api/@azure/keyvault-secrets/)<br>[Gyors útmutató](../secrets/quick-create-node.md)|

A [telepítőcsomagokat és](client-libraries.md) a forráskódot lásd: Ügyfélkódtárak.

További információ az adatsík Key Vault biztonságával kapcsolatban: Azure Key Vault [funkciók.](security-features.md)

### <a name="code-examples"></a>Kódpéldák

Az alkalmazásokkal való Key Vault teljes példákért lásd:

- [Azure Key Vault kódminták –](https://azure.microsoft.com/resources/samples/?service=key-vault) Kódminták Azure Key Vault. 

## <a name="how-tos"></a>Használati útmutatók

Az alábbi cikkek és forgatókönyvek feladatspecifikus útmutatást nyújtanak a Azure Key Vault:

- [Hozzáférés Key Vault](access-behind-firewall.md) tűzfal mögött – Egy kulcstartó eléréséhez a Key Vault-ügyfélalkalmazásnak több végpontot is el kell tudnia férni a különböző funkciókhoz.
- Tanúsítványok központi telepítése virtuális gépekre Key Vault – [Windows,](../../virtual-machines/extensions/key-vault-windows.md) [Linux](../../virtual-machines/extensions/key-vault-linux.md) – Egy Azure-beli virtuális gépen futó felhőalkalmazáshoz tanúsítványra van szükség. Hogyan jut el ez a tanúsítvány ehhez a virtuális géphez még ma?
- [Azure-webalkalmazás-tanúsítvány üzembe helyezése a Key Vault](../../app-service/configure-ssl-certificate.md#import-a-certificate-from-key-vault)
- Hozzáférési szabályzat hozzárendelése ([CLI](assign-access-policy-cli.md)  |  [PowerShell-portál](assign-access-policy-powershell.md)  |  [](assign-access-policy-portal.md)). 
- [A Key Vault](./key-vault-recovery.md) és a cli használatával való használata végigvezeti a kulcstartó és a különböző kulcstartó-objektumok használatán és életciklusán, amelyeken engedélyezve van a soft-delete.
- Biztonságos [értékek (például jelszavak)](../../azure-resource-manager/templates/key-vault-parameter.md) megadása az üzembe helyezés során – Ha az üzembe helyezés során biztonságos értéket (például jelszót) kell megadnia paraméterként, ezt az értéket titkosként tárolhatja egy Azure Key Vault-ban, és hivatkozhat rá más Resource Manager-sablonokban.

## <a name="integrated-with-key-vault"></a>Integrálva Key Vault

Ezek a cikkek olyan egyéb forgatókönyvekről és szolgáltatásokról szólnak, amelyek a Key Vault.

- [Az adattitkosítás](../../security/fundamentals/encryption-atrest.md) lehetővé teszi az adatok kódolását (titkosítását) az adatok megőrzését. Az adattitkosítási kulcsokat gyakran kulcstitkosítási kulccsal titkosítják a Azure Key Vault a hozzáférés további korlátozása érdekében.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) lehetővé teszi a saját bérlőkulcsa felett való felettesét. Például ahelyett, hogy a Microsoft felügyeli a bérlőkulcsot (ez az alapértelmezett beállítás), kezelheti a saját bérlőkulcsát, hogy megfeleljen a szervezetre vonatkozó konkrét szabályozásnak. A saját bérlőkulcs felügyelete más néven a saját kulcs használata (BYOK).
- [Azure Private Link Szolgáltatás](private-link-service.md) lehetővé teszi az Azure-szolgáltatások (például az Azure Key Vault, az Azure Storage és az Azure Cosmos DB) és az Azure által üzemeltetett ügyfél-/partnerszolgáltatások privát végponton keresztüli hozzáférését a virtuális hálózatban.
- Key Vault integrációja [Event Grid](../../event-grid/event-schema-key-vault.md)  lehetővé teszi, hogy a felhasználók értesítést kapnak, ha a Key Vaultban tárolt titkos kulcsok állapota megváltozott. A kimaradások elkerülése érdekében terjesztheti a titkos kulcsok új verzióját az alkalmazások között, vagy a lejárati idő közelében elforgathatja őket.
- Megvédheti Az [Azure DevOps titkos](/azure/devops/pipelines/release/azure-key-vault) kulcsok a nemkívánatos hozzáféréstől a Key Vault.
- [A DataBricksben tárolt Key Vault az Azure Storage-hoz való csatlakozáshoz](./integrate-databricks-blob-storage.md)
- Konfigurálja és futtassa a Azure Key Vault-szolgáltatót a Kubernetes-beli [Secrets Store CSI-illesztő](./key-vault-integrate-kubernetes.md) számára

## <a name="key-vault-overviews-and-concepts"></a>Key Vault és alapfogalmak áttekintése

- [Key Vault helyreállító törlési](soft-delete-overview.md) viselkedés egy olyan funkciót ír le, amely lehetővé teszi a törölt objektumok helyreállítását, akár véletlen, akár szándékos volt a törlés.
- [Key Vault ügyfél szabályozása](overview-throttling.md) a szabályozás alapfogalmait és az alkalmazáshoz való megközelítést kínál.
- [Key Vault biztonsági világok](overview-security-worlds.md) a régiók és a biztonsági területek közötti kapcsolatokat ismertetik.

## <a name="social"></a>Közösségi

- [Key Vault Blog](/archive/blogs/kv/)
- [Key Vault fórum](https://aka.ms/kvforum)