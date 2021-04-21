---
title: A API Management biztonságának biztosítása ügyfél-tanúsítvány hitelesítése használatával
titleSuffix: Azure API Management
description: Megtudhatja, hogyan kezelheti az ügyféltanúsítványokat és a háttérszolgáltatásokat ügyféltanúsítvány-alapú hitelesítéssel az Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 01/26/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9d198f34abe826af2dce0c1ed9950600d4c7bbd3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812294"
---
# <a name="secure-backend-services-using-client-certificate-authentication-in-azure-api-management"></a>Háttérszolgáltatások biztonságossá tere ügyfél-tanúsítványos hitelesítéssel az Azure API Management

API Management lehetővé teszi, hogy ügyféltanúsítványok használatával biztonságossá teszi az API háttérszolgáltatásához való hozzáférést. Ez az útmutató bemutatja, hogyan kezelheti a tanúsítványokat egy Azure API Management szolgáltatáspéldányban a Azure Portal. Azt is bemutatja, hogyan konfigurálható egy API egy tanúsítvány használatára a háttérszolgáltatáshoz való hozzáféréshez.

A tanúsítványokat a API Management is [API Management REST API.](/rest/api/apimanagement/2020-06-01-preview/certificate)

## <a name="certificate-options"></a>Tanúsítványbeállítások

API Management két lehetőséget kínál a háttérszolgáltatásokhoz való hozzáférés biztonságossá teszi a tanúsítványok kezelését:

* Hivatkozhat egy felügyelt tanúsítványra a [Azure Key Vault](../key-vault/general/overview.md) 
* Tanúsítványfájl hozzáadása közvetlenül a API Management

A Key Vault-tanúsítványok használata azért ajánlott, mert segít a API Management javításában:

* A kulcstartókban tárolt tanúsítványok újra felhasználhatók a szolgáltatások között
* A [kulcstartókban](../key-vault/general/security-features.md#privileged-access) tárolt tanúsítványokra részletes hozzáférési szabályzatok alkalmazhatók
* A kulcstartóban frissített tanúsítványok automatikusan elforgatódnak a API Management. A kulcstartóban való frissítés után a tanúsítvány a API Management 4 órán belül frissül. A tanúsítványt manuálisan is frissítheti a Azure Portal vagy a felügyeleti REST API.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Ha még nem hozott létre API Management szolgáltatáspéldányt, tekintse meg a API Management [szolgáltatáspéldány létrehozása részt.][Create an API Management service instance]
* A háttérszolgáltatásnak konfigurálva kell lennie az ügyfél-tanúsítvány hitelesítéséhez. Ha tanúsítványhitelesítést konfigurál a Azure App Service, tekintse meg [ezt a cikket.][to configure certificate authentication in Azure WebSites refer to this article] 
* Hozzá kell férni a tanúsítványhoz és a jelszóhoz az Azure Key Vaultban való felügyelethez, vagy fel kell töltenie az API Management szolgáltatásba. A tanúsítványnak **PFX formátumúnak kell** lennie. Az önaírt tanúsítványok használata engedélyezett.

### <a name="prerequisites-for-key-vault-integration"></a>A Key Vault-integráció előfeltételei

1. A kulcstartók létrehozási lépéseiért lásd: Rövid útmutató: Kulcstartó létrehozása [a Azure Portal.](../key-vault/general/quick-create-portal.md)
1. Rendszer által hozzárendelt vagy felhasználó által hozzárendelt felügyelt identitás [engedélyezése a](api-management-howto-use-managed-service-identity.md) API Management példányban.
1. Rendeljen hozzá [egy kulcstartó-hozzáférési](../key-vault/general/assign-access-policy-portal.md) szabályzatot a felügyelt identitáshoz, amely rendelkezik a tárolóból származó tanúsítványok le- és listához szükséges engedélyekkel. A szabályzat hozzáadása:
    1. A portálon keresse meg a kulcstartót.
    1. Válassza **a Beállítások > hozzáférési szabályzatok > + Hozzáférési szabályzat hozzáadása lehetőséget.**
    1. Válassza **a Tanúsítványengedélyek,** majd a **Lekér és lista** **lehetőséget.**
    1. A **Rendszerbiztonsági tag kiválasztása** mezőben válassza ki a felügyelt identitás erőforrásnevét. Ha rendszer által hozzárendelt identitást használ, a rendszerbiztonsági tag a API Management neve.
1. Hozzon létre vagy importálja a tanúsítványt a kulcstartóba. Lásd: [Rövid útmutató: Tanúsítvány](../key-vault/certificates/quick-create-portal.md)beállítása és lekérése a Azure Key Vault a Azure Portal.

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-a-key-vault-certificate"></a>Key Vault-tanúsítvány hozzáadása

Lásd: [A Key Vault-integráció előfeltételei.](#prerequisites-for-key-vault-integration)

> [!CAUTION]
> Ha kulcstartó-tanúsítványt használ a API Management ügyeljen arra, hogy ne törölje a kulcstartó eléréséhez használt tanúsítványt, kulcstartót vagy felügyelt identitást.

Kulcstartó-tanúsítvány hozzáadása a API Management:

1. A [Azure Portal](https://portal.azure.com)keresse meg a API Management példányát.
1. A **Biztonság alatt** válassza a Tanúsítványok **lehetőséget.**
1. Válassza a **Tanúsítványok**  >  **+ Hozzáadás lehetőséget.**
1. Az **Id (Azonosító)** alatt adjon meg egy ön által választott nevet.
1. A **Tanúsítvány mezőben** válassza a **Key Vault lehetőséget.**
1. Adja meg egy kulcstartó tanúsítványának  azonosítóját, vagy válassza a Kijelölés lehetőséget a tanúsítvány kulcstartóból való kiválasztásához.
    > [!IMPORTANT]
    > Ha saját maga adja meg a kulcstartó tanúsítványazonosítóját, győződjön meg arról, hogy az nem rendelkezik verzióinformációkkal. Ellenkező esetben a tanúsítvány nem lesz automatikusan elforgatva a API Management a kulcstartó frissítése után.
1. Az **Ügyfélidentitás** beállításban válasszon ki egy rendszer által hozzárendelt vagy egy meglévő, felhasználó által hozzárendelt felügyelt identitást. Megtudhatja, hogyan adhat hozzá vagy módosíthat felügyelt [identitásokat a API Management szolgáltatásban.](api-management-howto-use-managed-service-identity.md)
    > [!NOTE]
    > Az identitásnak engedélyekre van szüksége a tanúsítvány kulcstartóból való lekérése és listába való listához. Ha még nem konfigurálta a kulcstartóhoz való hozzáférést, a API Management kéri, hogy automatikusan konfigurálni tudja az identitást a szükséges engedélyekkel.
1. Válassza a **Hozzáadás** lehetőséget.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-kv.png" alt-text="Key Vault-tanúsítvány hozzáadása":::

## <a name="upload-a-certificate"></a>Tanúsítvány feltöltése

Ügyfél-tanúsítvány feltöltése a API Management: 

1. A [Azure Portal](https://portal.azure.com)nyissa meg a API Management példányát.
1. A **Biztonság alatt** válassza a Tanúsítványok **lehetőséget.**
1. Válassza a **Tanúsítványok**  >  **+ Hozzáadás lehetőséget.**
1. Az **Id (Azonosító)** alatt adjon meg egy ön által választott nevet.
1. A **Tanúsítvány mezőben** válassza az Egyéni **lehetőséget.**
1. Tallózással válassza ki a tanúsítvány .pfx fájlját, és adja meg a jelszavát.
1. Válassza a **Hozzáadás** lehetőséget.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-add.png" alt-text="Ügyfél-tanúsítvány feltöltése":::

A tanúsítvány feltöltése után megjelenik a Tanúsítványok **ablakban.** Ha sok tanúsítvánnyal rendelkezik, jegyezze fel a kívánt tanúsítvány ujjlenyomatát, hogy az API-t úgy konfigurálja, hogy ügyféltanúsítványt használjon az [átjáró hitelesítéséhez.](#configure-an-api-to-use-client-certificate-for-gateway-authentication)

> [!NOTE]
> Ha például egy önaírt tanúsítvány használata esetén ki kell kapcsolnia a tanúsítványlánc érvényesítését, kövesse a cikk későbbi, önaírt tanúsítványokról ( [)](#self-signed-certificates)vonatkozó lépéseit.

## <a name="configure-an-api-to-use-client-certificate-for-gateway-authentication"></a>API konfigurálása ügyfél-tanúsítvány használatára az átjáró hitelesítéséhez

1. A [Azure Portal](https://portal.azure.com)keresse meg a API Management példányát.
1. Az **API-k alatt** válassza az **API-k lehetőséget.**
1. Válasszon ki egy API-t a listából. 
2. A Tervezés **lapon** válassza a Háttér szakaszban található **szerkesztőikont.**
3. Az **Átjáró hitelesítő adatai alatt** válassza az **Ügyfél-tanúsítvány lehetőséget,** majd válassza ki a tanúsítványt a legördülő menüből.
1. Kattintson a **Mentés** gombra.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png" alt-text="Ügyfél-tanúsítvány használata az átjáró hitelesítéséhez":::

> [!CAUTION]
> Ez a módosítás azonnal hatályba lép, és az API műveleteinek hívásai a tanúsítvánnyal hitelesítik magukat a háttérkiszolgálón.

> [!TIP]
> Ha meg van adva egy tanúsítvány az API háttérszolgáltatásának átjáróhitelesítéséhez, az az API házirendjének része lesz, és megtekinthető a házirendszerkesztőben.

## <a name="self-signed-certificates"></a>Önaírt tanúsítványok

Ha önaírt tanúsítványokat használ, le kell tiltania a tanúsítványlánc érvényesítését a API Management a háttérrendszerrel való kommunikációhoz. Ellenkező esetben 500-as hibakódot fog visszaadni. A konfiguráláshoz használhatja az (új háttérkészlethez) vagy (meglévő háttérhez) PowerShell-parancsmagokat, és a paramétert a következőre [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) `-SkipCertificateChainValidation` állíthatja: `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

## <a name="delete-a-client-certificate"></a>Ügyfél-tanúsítvány törlése

Tanúsítvány törléséhez jelölje ki, majd válassza a Törlés **lehetőséget** a helyi menüből (**...**).

:::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png" alt-text="Tanúsítvány törlése":::

> [!IMPORTANT]
> Ha a tanúsítványra bármely házirend hivatkozik, megjelenik egy figyelmeztető képernyő. A tanúsítvány törléséhez először el kell távolítania a tanúsítványt minden olyan házirendből, amely a használatára van konfigurálva.

## <a name="next-steps"></a>Következő lépések

* [API-k biztonságossá tétele ügyféltanúsítvány-alapú hitelesítéssel az API Managementben](api-management-howto-mutual-certificates-for-clients.md)
* Tudnivalók a [szabályzatok API Management](api-management-howto-policies.md)


[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: ./api-management-caching-policies.md
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

