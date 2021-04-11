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
ms.openlocfilehash: 550084ccbb1df24fe0cbc0a4630efe19e6685e5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709903"
---
# <a name="azure-key-vault-developers-guide"></a>Az Azure Key Vault fejlesztői útmutatója

Key Vault lehetővé teszi a bizalmas adatok biztonságos elérését az alkalmazásokon belülről:

- A kulcsok, titkos kódok és tanúsítványok védelme a kód írása nélkül történik, és az alkalmazások egyszerűen használhatók.
- Lehetővé teszi, hogy az ügyfelek saját kulcsaikat, titkos kulcsokat és tanúsítványokat tudjanak kezelni, így az alapvető szoftver-funkciók biztosítására koncentrálhat. Így az alkalmazásai nem felelnek meg az ügyfeleknek a bérlői kulcsokkal, titkokkal és tanúsítványokkal kapcsolatos felelősségének vagy lehetséges felelősségének.
- Az alkalmazás a kulcsokat az aláíráshoz és a titkosításhoz is használhatja, és az alkalmazásból külső kulcskezelő is megtartja. A kulcsokkal kapcsolatos további információkért lásd: [a kulcsok ismertetése](../keys/about-keys.md)
- A hitelesítő adatokat, például a jelszavakat, a hozzáférési kulcsokat és az SAS-jogkivonatokat úgy kezelheti, ha a Key Vault titkos kulcsként tárolja őket, lásd a [titkokat](../secrets/about-secrets.md)
- Tanúsítványok kezelése. További információ: [a tanúsítványok ismertetése](../certificates/about-certificates.md)

További általános információk a Azure Key Vaultről: [Mi az Key Vault](overview.md).

## <a name="public-previews"></a>Nyilvános előzetes verziók

Időnként új Key Vault szolgáltatás nyilvános előzetes verzióját bocsátjuk rendelkezésére. Próbálja ki a nyilvános előzetes verzió funkcióit, és tudassa velünk, mit gondol azurekeyvault@microsoft.com , mi a visszajelzés e-mail-címe.

## <a name="creating-and-managing-key-vaults"></a>Kulcstartók létrehozása és kezelése

Az egyéb Azure-szolgáltatásokhoz hasonlóan Key Vault felügyelet Azure Resource Manager szolgáltatáson keresztül történik. Az Azure Resource Manager az Azure üzembehelyezési és felügyeleti szolgáltatása. Egy olyan felügyeleti réteget biztosít, amely lehetővé teszi az Azure-fiókban lévő erőforrások létrehozását, frissítését és törlését. További információ: [Azure Resource Manager](../../azure-resource-manager/management/overview.md)

A felügyeleti réteghez való hozzáférést az [Azure szerepköralapú hozzáférés-vezérlés](../../role-based-access-control/overview.md)szabályozza. Key Vault a felügyeleti réteg, más néven felügyeleti vagy vezérlési folyamat lehetővé teszi a kulcstartók és attribútumaik létrehozását és kezelését, beleértve a hozzáférési házirendeket, az adatsíkon felügyelt kulcsokat, titkokat és tanúsítványokat is. Az előre definiált `Key Vault Contributor` szerepkörökkel felügyeleti hozzáférést adhat a Key Vaulthoz.     

**A Key Vault felügyeletéhez szükséges API-k és SDK-k:**

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referencia](/cli/azure/keyvault)<br>[Gyors útmutató](quick-create-cli.md)|[Referencia](/powershell/module/az.keyvault)<br>[Gyors útmutató](quick-create-powershell.md)|[Referencia](/rest/api/keyvault/)|[Referencia](/azure/templates/microsoft.keyvault/vaults)<br>[Gyors útmutató](./vault-create-template.md)|[Referencia](/dotnet/api/microsoft.azure.management.keyvault)|[Referencia](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[Referencia](/java/api/com.microsoft.azure.management.keyvault)|[Referencia](/javascript/api/@azure/arm-keyvault)|

Lásd: [ügyféloldali kódtárak](client-libraries.md) telepítési csomagok és forráskódok számára.

További információ a Key Vault felügyeleti síkon: [Key Vault felügyeleti sík](./secure-your-key-vault.md#management-plane-and-azure-rbac)

## <a name="authenticate-to-key-vault-in-code"></a>Hitelesítés Key Vault kódban

Key Vault olyan Azure AD-hitelesítést használ, amelyhez hozzáférést kell biztosítani az Azure AD rendszerbiztonsági tag számára. Az Azure AD rendszerbiztonsági tag lehet egy felhasználó, egy egyszerű alkalmazás, egy [felügyelt identitás az Azure-erőforrásokhoz](../../active-directory/managed-identities-azure-resources/overview.md), vagy bármilyen rendszerbiztonsági tag egy csoportja.

### <a name="authentication-best-practices"></a>Ajánlott eljárások a hitelesítéshez

Ajánlott felügyelt identitást használni az Azure-ban üzembe helyezett alkalmazásokhoz. Ha olyan Azure-szolgáltatásokat használ, amelyek nem támogatják a felügyelt identitást, vagy ha az alkalmazások üzembe helyezése a helyszínen történik, az [egyszerű szolgáltatásnév egy](../../active-directory/develop/howto-create-service-principal-portal.md) lehetséges alternatíva. Ebben az esetben a tanúsítványt Key Vault kell tárolni, és gyakran el kell forgatni. A titkos kulccsal rendelkező szolgáltatásnév fejlesztési és tesztelési környezetekhez használható, valamint helyileg vagy Cloud Shell felhasználói rendszerbiztonsági tag használata ajánlott.

Ajánlott rendszerbiztonsági tag/környezet:
- **Éles környezet**:
  - Felügyelt identitás vagy egyszerű szolgáltatásnév tanúsítvánnyal
- **Tesztelési és fejlesztési környezetek**:
  - Felügyelt identitás, egyszerű tanúsítvány vagy egyszerű szolgáltatás, titkos kulccsal
- **Helyi fejlesztés**:
  - Felhasználó vagy egyszerű szolgáltatás titkos kulccsal

A fenti hitelesítési forgatókönyveket az **Azure Identity Client Library** támogatja, és integrálva van Key Vault SDK-val. Az Azure Identity Library különböző környezetekben és platformokon is használható a kód módosítása nélkül. Az Azure Identity azt is automatikusan lekéri a hitelesítési tokent, hogy bejelentkezett az Azure-felhasználónak az Azure CLI-vel, a Visual Studióval, a Visual Studio Code-val és másokkal. 

Az Azure Identity Client libarary kapcsolatos további információkért lásd:

**Azure Identity ügyféloldali kódtárak**

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Azure Identity SDK .NET](/dotnet/api/overview/azure/identity-readme)|[Azure Identity SDK Python](/python/api/overview/azure/identity-readme)|[Azure Identity SDK Java](/java/api/overview/azure/identity-readme)|[Azure Identity SDK JavaScript](/javascript/api/overview/azure/identity-readme)|     

>[!Note]
> A jelenleg depracated Key Vault .NET SDK 3-as verziójához ajánlott [alkalmazás-hitelesítési függvénytár](/dotnet/api/overview/azure/service-to-service-authentication) . Kövesse a AppAuthentication az Azure-ban című témakört [. az identitás áttelepítési útmutatója](/dotnet/api/overview/azure/app-auth-migration) Key Vault .net SDK 4-es verziójára való áttelepítéshez.

Az alkalmazásokban való Key Vault hitelesítésével kapcsolatos oktatóanyagok:
- [Hitelesítés Key Vault a virtuális gépen üzemeltetett alkalmazásban a .NET-ben](./tutorial-net-virtual-machine.md)
- [Hitelesítés Key Vault a virtuális gépen futó alkalmazásban a Pythonban](./tutorial-python-virtual-machine.md)
- [Hitelesítés Key Vault a App Service](./tutorial-net-create-vault-azure-web-app.md)

## <a name="manage-keys-certificates-and-secrets"></a>Kulcsok, tanúsítványok és titkos kódok kezelése

A kulcsok, titkok és tanúsítványok elérését az adatsík vezérli. Az adatsík hozzáférés-vezérlése a helyi tár hozzáférési szabályzatai vagy az Azure RBAC (előzetes verzió) használatával végezhető el.

**Kulcsok API-k és SDK-k**

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referencia](/cli/azure/keyvault/key)<br>[Gyors útmutató](../keys/quick-create-cli.md)|[Referencia](/powershell/module/az.keyvault/)<br>[Gyors útmutató](../keys/quick-create-powershell.md)|[Referencia](/rest/api/keyvault/#key-operations)|[Referencia](/azure/templates/microsoft.keyvault/vaults/keys)<br>[Gyors útmutató](../keys/quick-create-template.md)|[Referencia](/dotnet/api/azure.security.keyvault.keys)<br>[Gyors útmutató](../keys/quick-create-net.md)|[Referencia](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[Gyors útmutató](../keys/quick-create-python.md)|[Referencia](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>[Gyors útmutató](../keys/quick-create-java.md)|[Referencia](/javascript/api/@azure/keyvault-keys/)<br>[Gyors útmutató](../keys/quick-create-node.md)|

**Tanúsítványok API-k és SDK-k**

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referencia](/cli/azure/keyvault/certificate)<br>[Gyors útmutató](../certificates/quick-create-cli.md)|[Referencia](/powershell/module/az.keyvault)<br>[Gyors útmutató](../certificates/quick-create-powershell.md)|[Referencia](/rest/api/keyvault/#certificate-operations)|N/A|[Referencia](/dotnet/api/azure.security.keyvault.certificates)<br>[Gyors útmutató](../certificates/quick-create-net.md)|[Referencia](/python/api/overview/azure/keyvault-certificates-readme)<br>[Gyors útmutató](../certificates/quick-create-python.md)|[Referencia](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>[Gyors útmutató](../certificates/quick-create-java.md)|[Referencia](/javascript/api/@azure/keyvault-certificates/)<br>[Gyors útmutató](../certificates/quick-create-node.md)|

**Secrets API és SDK-k**

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referencia](/cli/azure/keyvault/secret)<br>[Gyors útmutató](../secrets/quick-create-cli.md)|[Referencia](/powershell/module/az.keyvault/)<br>[Gyors útmutató](../secrets/quick-create-powershell.md)|[Referencia](/rest/api/keyvault/#secret-operations)|[Referencia](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[Gyors útmutató](../secrets/quick-create-template.md)|[Referencia](/dotnet/api/azure.security.keyvault.secrets)<br>[Gyors útmutató](../secrets/quick-create-net.md)|[Referencia](/python/api/overview/azure/keyvault-secrets-readme)<br>[Gyors útmutató](../secrets/quick-create-python.md)|[Referencia](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[Gyors útmutató](../secrets/quick-create-java.md)|[Referencia](/javascript/api/@azure/keyvault-secrets/)<br>[Gyors útmutató](../secrets/quick-create-node.md)|

Lásd: [ügyféloldali kódtárak](client-libraries.md) telepítési csomagok és forráskódok számára.

További információ a Key Vault adatsík biztonságáról: [Key Vault adatsíkon és hozzáférési szabályzatok](./secure-your-key-vault.md#data-plane-and-access-policies) és [Key Vault Adatsík és az Azure RBAC (előzetes verzió)](./secure-your-key-vault.md#data-plane-and-azure-rbac-preview)

### <a name="code-examples"></a>Kódpéldák

A Key Vault alkalmazással való használatának teljes példáit a következő témakörben tekintheti meg:

- [Azure Key Vault kód mintái](https://azure.microsoft.com/resources/samples/?service=key-vault) – a Azure Key Vaulthoz tartozó példák. 

## <a name="how-tos"></a>Használati útmutatók

A következő cikkek és forgatókönyvek feladat-specifikus útmutatást nyújtanak a Azure Key Vault használatához:

- A [tűzfal mögötti Key Vault elérése](access-behind-firewall.md) – egy kulcstartó eléréséhez a Key Vault-ügyfélalkalmazás számára a különböző funkciókhoz több végpontot is el kell érnie.
- Tanúsítványok telepítése virtuális gépekre Key Vault – [Windows](../../virtual-machines/extensions/key-vault-windows.md), [Linux](../../virtual-machines/extensions/key-vault-linux.md) – az Azure-beli virtuális gépen futó felhőalapú alkalmazásnak tanúsítványra van szüksége. Hogyan szerezheti be ezt a tanúsítványt még ma a virtuális gépre?
- [Azure webalkalmazás-tanúsítvány üzembe helyezése Key Vault](../../app-service/configure-ssl-certificate.md#import-a-certificate-from-key-vault)
- Hozzáférési szabályzat ([CLI](assign-access-policy-cli.md)  |  [PowerShell](assign-access-policy-powershell.md)  |  -[portál](assign-access-policy-portal.md)) társítása. 
- A [Key Vault Soft-delete használata a parancssori](./key-vault-recovery.md) felülettel végigvezeti a kulcstartó használatának és életciklusának, valamint a helyreállítható törlést lehetővé tett különböző kulcstartó-objektumoknak a használatával.
- [Biztonságos értékek (például jelszavak)](../../azure-resource-manager/templates/key-vault-parameter.md) átadása az üzembe helyezés során – ha egy biztonságos értéket (például jelszót) kell megadni paraméterként az üzembe helyezés során, az értéket tárolhatja titkosként egy Azure Key Vault, és hivatkozhat az értékre más Resource Manager-sablonokban.

## <a name="integrated-with-key-vault"></a>Integrálva van Key Vault

Ezek a cikkek olyan egyéb forgatókönyvekkel és szolgáltatásokkal kapcsolatosak, amelyek a Key Vault-t használják vagy integrálják.

- A [nyugalmi](../../security/fundamentals/encryption-atrest.md) állapotban lévő titkosítás lehetővé teszi az adatok kódolását (titkosítását), amikor a rendszer megőrzi a tartalmat. Az adattitkosítási kulcsokat gyakran titkosítja Azure Key Vault a hozzáférés további korlátozása érdekében a kulcs titkosítási kulcsával.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) lehetővé teszi a saját bérlői kulcsának felettesét. Például ahelyett, hogy a Microsoft a bérlői kulcsát (az alapértelmezettet) kezelhesse, a saját bérlői kulcsát úgy kezelheti, hogy megfeleljen a szervezetére vonatkozó egyes előírásoknak. A saját bérlőkulcs felügyelete más néven a saját kulcs használata (BYOK).
- Az [Azure Private link Service](private-link-service.md) lehetővé teszi az Azure-szolgáltatások (például az Azure Key Vault, az Azure Storage és a Azure Cosmos db) és az Azure által üzemeltetett ügyfél-és partneri szolgáltatások elérését a virtuális hálózat privát végpontján keresztül.
- Key Vault integrációja [Event Grid](../../event-grid/event-schema-key-vault.md)  lehetővé teszi a felhasználók számára, hogy a Key vaultban tárolt titkos kód állapotának változásakor értesítést kapjanak. A kiesések elkerülése érdekében a titkok új verzióit terjesztheti az alkalmazásokra, vagy elforgathatja közel lejáró titkokat.
- Az [Azure Devops](/azure/devops/pipelines/release/azure-key-vault) -titkokat a Key Vaultban található nemkívánatos hozzáférésekkel biztosíthatja.
- [A DataBricks Key Vault tárolt titkos kulcs használata az Azure Storage-hoz való kapcsolódáshoz](./integrate-databricks-blob-storage.md)
- A [Secrets Store CSI-illesztőprogram](./key-vault-integrate-kubernetes.md) Azure Key Vault-szolgáltatójának konfigurálása és futtatása a Kubernetes-on

## <a name="key-vault-overviews-and-concepts"></a>Key Vault áttekintések és fogalmak

- Key Vault a helyreállítható [törlési viselkedés](soft-delete-overview.md) olyan funkciót ismertet, amely lehetővé teszi a törölt objektumok helyreállítását, függetlenül attól, hogy a törlés véletlen vagy szándékos volt-e.
- [Key Vault az ügyfelek szabályozása](overview-throttling.md) a szabályozás alapvető fogalmait, és az alkalmazás megközelítését kínálja.
- [Key Vault biztonsági világok](overview-security-worlds.md) ismertetik a régiók és a biztonsági területek közötti kapcsolatokat.

## <a name="social"></a>Közösségi

- [Key Vault blog](/archive/blogs/kv/)
- [Key Vault fórum](https://aka.ms/kvforum)