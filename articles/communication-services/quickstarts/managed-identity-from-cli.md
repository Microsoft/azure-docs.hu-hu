---
title: Azure Active Directory felügyelt identitás-alkalmazás létrehozása az Azure CLI-vel
titleSuffix: An Azure Communication Services quickstart
description: A felügyelt identitások lehetővé teszik az Azure kommunikációs szolgáltatások elérésének engedélyezését az Azure-beli virtuális gépeken, a Function apps-ben és más erőforrásokon futó alkalmazásokból. Ez a rövid útmutató az identitás az Azure CLI használatával történő felügyeletére koncentrál.
services: azure-communication-services
author: jbeauregardb
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: jbeauregardb
ms.reviewer: mikben
ms.openlocfilehash: a5ee7e8de85a1a53359f651a74e2f9f5e51edb70
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030780"
---
# <a name="authorize-access-with-managed-identity-to-your-communication-resource-in-your-development-environment"></a>Felügyelt identitással való hozzáférés engedélyezése a kommunikációs erőforráshoz a fejlesztési környezetben

Az Azure Identity SDK Azure Active Directory (Azure AD) jogkivonat-hitelesítési támogatást biztosít az Azure SDK-hoz. Az Azure kommunikációs szolgáltatások SDK-k legújabb verziói a .NET, a Java, a Python és a JavaScript integrálására az Azure Identity Library használatával egyszerű és biztonságos eszközöket biztosítanak ahhoz, hogy OAuth 2,0-tokent szerezzenek az Azure kommunikációs szolgáltatások kéréseinek engedélyezéséhez.

Az Azure Identity SDK előnye, hogy lehetővé teszi, hogy ugyanazt a kódot használja több szolgáltatás hitelesítésére is, függetlenül attól, hogy az alkalmazás a fejlesztői környezetben vagy az Azure-ban fut-e. Az Azure Identity SDK egy rendszerbiztonsági tag hitelesítésére szolgál. Ha a kód az Azure-ban fut, a rendszerbiztonsági tag felügyelt identitás az Azure-erőforrásokhoz. A fejlesztői környezetben a felügyelt identitás nem létezik, így az SDK tesztelési célból hitelesíti a felhasználót vagy a regisztrált alkalmazást.

## <a name="prerequisites"></a>Előfeltételek

 - Azure CLI. [Telepítési útmutató](/cli/azure/install-azure-cli)
 - Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free)

## <a name="setting-up"></a>Beállítás

A felügyelt identitásokat engedélyezni kell az Ön által engedélyezett Azure-erőforrásokon. Ha szeretné megtudni, hogyan engedélyezheti az Azure-erőforrások felügyelt identitásait, tekintse meg az alábbi cikkek egyikét:

- [Azure Portalra](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sablon](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [SDK-k Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [App Services](../../app-service/overview-managed-identity.md)

## <a name="authenticate-a-registered-application-in-the-development-environment"></a>Regisztrált alkalmazás hitelesítése a fejlesztői környezetben

Ha a fejlesztési környezet nem támogatja az egyszeri bejelentkezést vagy a bejelentkezést egy webböngészőn keresztül, akkor egy regisztrált alkalmazás használatával végezheti el a hitelesítést a fejlesztői környezetből.

### <a name="creating-an-azure-active-directory-registered-application"></a>Azure Active Directory regisztrált alkalmazás létrehozása

A regisztrált alkalmazások Azure CLI-ből való létrehozásához be kell jelentkeznie az Azure-fiókba, ahol a műveleteket végre szeretné hajtani. Ehhez a parancsot használhatja, `az login` és megadhatja a hitelesítő adatait a böngészőben. Miután bejelentkezett az Azure-fiókjába a CLI-ből, meghívjuk a `az ad sp create-for-rbac` parancsot a regisztrált alkalmazás létrehozásához.

Az alábbi példák az Azure CLI használatával hoznak létre egy új regisztrált alkalmazást

```azurecli
az ad sp create-for-rbac --name <application-name> 
```

A `az ad sp create-for-rbac` parancs egy egyszerű szolgáltatásnév-tulajdonságok listáját fogja visszaadni JSON formátumban. Másolja ezeket az értékeket, hogy felhasználhassa a szükséges környezeti változókat a következő lépésben.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```
> [!IMPORTANT]
> Az Azure-beli szerepkör-hozzárendelések eltartása néhány percet is igénybe vehet.

#### <a name="set-environment-variables"></a>Környezeti változók beállítása

Az Azure Identity SDK három környezeti változóból olvassa be az értékeket az alkalmazás hitelesítéséhez. A következő táblázat az egyes környezeti változókhoz beállított értéket ismerteti.

|Környezeti változó|Érték
|-|-
|`AZURE_CLIENT_ID`|`appId` a generált JSON-érték 
|`AZURE_TENANT_ID`|`tenant` a generált JSON-érték
|`AZURE_CLIENT_SECRET`|`password` a generált JSON-érték

> [!IMPORTANT]
> A környezeti változók beállítása után zárjuk be és nyissa meg újra a konzolablak ablakát. Ha a Visual studiót vagy más fejlesztési környezetet használ, előfordulhat, hogy újra kell indítania, hogy regisztrálja az új környezeti változókat.

Miután beállította ezeket a változókat, a kódban található DefaultAzureCredential objektum használatával hitelesítheti magát az Ön által választott szolgáltatási ügyfélen.


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A hitelesítés ismertetése](../concepts/authentication.md)

A következőket is érdemes elvégezheti:

- [További információ az Azure Identity Library szolgáltatásról](/dotnet/api/overview/azure/identity-readme)