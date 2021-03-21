---
title: API Management háttér védelme az ügyféltanúsítvány-alapú hitelesítés használatával
titleSuffix: Azure API Management
description: Ismerje meg, hogyan kezelheti az Ügyféltanúsítványok és a háttérbeli szolgáltatások védelmét az Azure API Managementban az ügyféltanúsítvány-alapú hitelesítés használatával.
services: api-management
documentationcenter: ''
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 01/26/2021
ms.author: apimpm
ms.openlocfilehash: 2e4a398ab71878134887fb8fba025cd8aa6122ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99492834"
---
# <a name="secure-backend-services-using-client-certificate-authentication-in-azure-api-management"></a>A háttérbeli szolgáltatások biztonságossá tétele az Azure-beli ügyféltanúsítvány-alapú hitelesítés használatával API Management

API Management lehetővé teszi az API-k háttér-szolgáltatáshoz való hozzáférésének védelmét az Ügyféltanúsítványok használatával. Ez az útmutató bemutatja, hogyan kezelheti a tanúsítványokat egy Azure API Management Service-példányban a Azure Portal használatával. Azt is ismerteti, hogyan konfigurálhat egy API-t a háttér-szolgáltatáshoz való hozzáféréshez használt tanúsítvány használatára.

API Management tanúsítványokat a [API Management REST API](/rest/api/apimanagement/2020-06-01-preview/certificate)használatával is kezelheti.

## <a name="certificate-options"></a>Tanúsítvány beállításai

API Management két lehetőséget kínál a háttér-szolgáltatásokhoz való hozzáférés biztosításához használt tanúsítványok kezelésére:

* [Azure Key Vault](../key-vault/general/overview.md) felügyelt tanúsítvány referenciája 
* Tanúsítványfájl hozzáadása közvetlenül a API Management

A Key Vault-tanúsítványok használata ajánlott, mert segít javítani a API Management biztonságot:

* A Key vaultokban tárolt tanúsítványok újra felhasználhatók a szolgáltatások között
* A részletes [hozzáférési szabályzatok](../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies) alkalmazhatók a Key vaultban tárolt tanúsítványokra is
* A Key vaultban frissített tanúsítványok API Management automatikusan el lesznek forgatva. A Key vaultban történt frissítés után a API Management 4 órán belül frissül a tanúsítvány. A tanúsítványt manuálisan is frissítheti a Azure Portal vagy a felügyeleti REST API segítségével.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Ha még nem hozott létre API Management Service-példányt, tekintse meg [a API Management-szolgáltatás példányának létrehozása][Create an API Management service instance]című témakört.
* Az ügyféltanúsítvány-alapú hitelesítéshez konfigurálnia kell a háttér-szolgáltatást. A tanúsítványalapú hitelesítés konfigurálásához a Azure App Service tekintse meg [ezt a cikket][to configure certificate authentication in Azure WebSites refer to this article]. 
* A tanúsítványhoz és az Azure Key vaultban való felügyelethez szükséges jelszóhoz, vagy a API Management szolgáltatáshoz való feltöltéshez van szüksége. A tanúsítványnak **pfx** formátumúnak kell lennie. Az önaláírt tanúsítványok engedélyezettek.

### <a name="prerequisites-for-key-vault-integration"></a>A Key Vault-integráció előfeltételei

1. A kulcstartó létrehozásának lépéseiért lásd: gyors útmutató [: kulcstartó létrehozása a Azure Portal használatával](../key-vault/general/quick-create-portal.md).
1. A rendszer által hozzárendelt vagy felhasználó által hozzárendelt [felügyelt identitás](api-management-howto-use-managed-service-identity.md) engedélyezése a API Management-példányban.
1. Rendeljen hozzá egy [Key Vault-hozzáférési szabályzatot](../key-vault/general/assign-access-policy-portal.md) a felügyelt identitáshoz, amely engedélyekkel rendelkezik tanúsítványok lekéréséhez és listázásához a tárból. A szabályzat hozzáadása:
    1. A portálon navigáljon a kulcstartóhoz.
    1. Válassza a **beállítások > hozzáférési szabályzatok > + hozzáférési házirend hozzáadása** lehetőséget.
    1. Válassza a **tanúsítvány engedélyei** lehetőséget, majd a **beolvasás** és a **lista** lehetőséget.
    1. A **rendszerbiztonsági tag kiválasztása** területen válassza ki a felügyelt identitás erőforrásának nevét. Ha rendszer által hozzárendelt identitást használ, a rendszerbiztonsági tag a API Management példányának neve.
1. Tanúsítvány létrehozása vagy importálása a kulcstartóba. Lásd [: gyors útmutató: tanúsítvány beállítása és lekérése Azure Key Vault a Azure Portal használatával](../key-vault/certificates/quick-create-portal.md).

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-a-key-vault-certificate"></a>Key Vault-tanúsítvány hozzáadása

Lásd: [a Key Vault-integráció előfeltételei](#prerequisites-for-key-vault-integration).

> [!CAUTION]
> Ha API Management Key Vault-tanúsítványt használ, ügyeljen arra, hogy ne törölje a Key Vault eléréséhez használt tanúsítványt, kulcstartót vagy felügyelt identitást.

Key Vault-tanúsítvány hozzáadása a API Managementhoz:

1. A [Azure Portal](https://portal.azure.com)navigáljon a API Management-példányhoz.
1. A **Biztonság** területen válassza a **tanúsítványok** lehetőséget.
1. Válassza a **tanúsítványok**  >  **+ Hozzáadás** lehetőséget.
1. Az **azonosító** mezőben adja meg az Ön által választott nevet.
1. A **tanúsítvány** területen válassza a **Key Vault** elemet.
1. Adja meg a Key Vault-tanúsítvány azonosítóját, vagy válassza a **kiválasztás** lehetőséget a tanúsítvány kiválasztásához a kulcstartóból.
    > [!IMPORTANT]
    > Ha saját maga adja meg a Key Vault-tanúsítvány azonosítóját, győződjön meg róla, hogy nem rendelkezik a verzióval kapcsolatos információkkal. Ellenkező esetben a tanúsítvány nem forog automatikusan API Management a Key Vault frissítését követően.
1. Az **ügyfél identitása** területen válasszon ki egy rendszer által hozzárendelt vagy egy meglévő felhasználó által hozzárendelt felügyelt identitást. Ismerje meg, hogyan [adhat hozzá vagy módosíthat felügyelt identitásokat a API Management szolgáltatásban](api-management-howto-use-managed-service-identity.md).
    > [!NOTE]
    > Az identitásnak engedélyre van szüksége a tanúsítvány lekéréséhez és listázásához a kulcstartóból. Ha még nem konfigurálta a Key vaulthoz való hozzáférést, API Management felszólítja, hogy automatikusan konfigurálja az identitást a szükséges engedélyekkel.
1. Válassza a **Hozzáadás** lehetőséget.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-kv.png" alt-text="Key Vault-tanúsítvány hozzáadása":::

## <a name="upload-a-certificate"></a>Tanúsítvány feltöltése

Ügyféltanúsítvány feltöltése a API Managementba: 

1. A [Azure Portal](https://portal.azure.com)navigáljon a API Management-példányhoz.
1. A **Biztonság** területen válassza a **tanúsítványok** lehetőséget.
1. Válassza a **tanúsítványok**  >  **+ Hozzáadás** lehetőséget.
1. Az **azonosító** mezőben adja meg az Ön által választott nevet.
1. A **tanúsítvány** területen válassza az **Egyéni** lehetőséget.
1. Tallózással válassza ki a Certificate. pfx fájlt, és adja meg a jelszavát.
1. Válassza a **Hozzáadás** lehetőséget.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-add.png" alt-text="Ügyféltanúsítvány feltöltése":::

A tanúsítvány feltöltése után az a **tanúsítványok** ablakban jelenik meg. Ha sok tanúsítványa van, jegyezze fel a kívánt tanúsítvány ujjlenyomatát, hogy az API-t úgy konfigurálja, hogy az ügyfél tanúsítványát használja az [átjáró-hitelesítéshez](#configure-an-api-to-use-client-certificate-for-gateway-authentication).

> [!NOTE]
> Ha például önaláírt tanúsítványt használ a tanúsítványlánc-ellenőrzés kikapcsolásához, kövesse a jelen cikk későbbi, [önaláírt tanúsítványok](#self-signed-certificates)című részében ismertetett lépéseket.

## <a name="configure-an-api-to-use-client-certificate-for-gateway-authentication"></a>API konfigurálása az ügyfél-tanúsítvány használatára az átjáró hitelesítéséhez

1. A [Azure Portal](https://portal.azure.com)navigáljon a API Management-példányhoz.
1. Az **API**-k területen válassza az **API**-k elemet.
1. Válasszon ki egy API-t a listából. 
2. A **tervezés** lapon válassza a szerkesztő ikont a **háttér** szakaszban.
3. Az **átjáró hitelesítő adatai** területen válassza ki az **ügyfél-tanúsítvány** elemet, majd válassza ki a tanúsítványt a legördülő listából.
1. Kattintson a **Mentés** gombra.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png" alt-text="Ügyféltanúsítvány használata átjáró-hitelesítéshez":::

> [!CAUTION]
> Ez a változás azonnal hatályba lép, és az API műveleteinek meghívása a tanúsítványt használja a háttér-kiszolgálón történő hitelesítéshez.

> [!TIP]
> Ha egy API háttérbeli szolgáltatásához tanúsítvány van megadva az átjáró-hitelesítéshez, az az adott API szabályzatának részévé válik, és megtekinthető a házirend-szerkesztőben.

## <a name="self-signed-certificates"></a>Önaláírt tanúsítványok

Ha önaláírt tanúsítványokat használ, le kell tiltania a tanúsítványlánc érvényesítését API Management a háttérrendszer használatával folytatott kommunikációhoz. Ellenkező esetben 500 hibakódot ad vissza. Ennek konfigurálásához használhatja az [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) (új háttérrendszer esetében) vagy a [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (meglévő háttérrendszer esetében) PowerShell-parancsmagokat, és beállíthatja a `-SkipCertificateChainValidation` paramétert a következőre: `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

## <a name="delete-a-client-certificate"></a>Ügyféltanúsítvány törlése

A tanúsítvány törléséhez jelölje ki azt, majd válassza a helyi menü **Törlés** elemét (**...**).

:::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png" alt-text="Tanúsítvány törlése":::

> [!IMPORTANT]
> Ha a tanúsítványra bármely szabályzat hivatkozik, megjelenik egy figyelmeztető képernyő. A tanúsítvány törléséhez először el kell távolítania a tanúsítványt a használatára konfigurált összes szabályzatból.

## <a name="next-steps"></a>Következő lépések

* [API-k biztonságossá tétele ügyféltanúsítvány-alapú hitelesítéssel az API Managementben](api-management-howto-mutual-certificates-for-clients.md)
* További tudnivalók a [API Management szabályzatokról](api-management-howto-policies.md)


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

