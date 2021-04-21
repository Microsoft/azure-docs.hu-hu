---
title: Háttér Service Fabric beállítása az Azure API Management | Microsoft Docs
description: Új Service Fabric háttérszolgáltatás létrehozása az Azure API Management a Azure Portal
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3dda6f18c2bf92b537c2f4be1c6a0a3b70cdc28a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815881"
---
# <a name="set-up-a-service-fabric-backend-in-api-management-using-the-azure-portal"></a>Háttér Service Fabric beállítása a API Management a Azure Portal

Ez a cikk bemutatja, hogyan konfigurálható egy [Service Fabric](../service-fabric/service-fabric-api-management-overview.md) szolgáltatás egyéni API-háttérszolgáltatásként a Azure Portal. Bemutatási célból bemutatja, hogyan állíthat be alapszintű állapot nélküli ASP.NET Core Reliable Service-t Service Fabric háttérkiszolgálóként.

Háttérinformációkért lásd: [Háttér](backends.md)API Management.

## <a name="prerequisites"></a>Előfeltételek

A Windowst futtató Service Fabric egyéni háttéralkalmazásként futó mintaszolgáltatás konfigurálásának előfeltételei:

* **Windows fejlesztési környezet** – Telepítse [Visual Studio 2019-es](https://www.visualstudio.com) és az **Azure** fejlesztési, ASP.NET- és **webfejlesztési,** valamint a **.NET Core** platformfüggetlen fejlesztési számítási feladatait. Ezután hozzon létre egy [.NET fejlesztési környezet](../service-fabric/service-fabric-get-started.md).

* **Service Fabric –** Oktatóanyag: Windows rendszert Service Fabric fürt üzembe helyezése [Azure-beli virtuális hálózaton.](../service-fabric/service-fabric-tutorial-create-vnet-and-windows-cluster.md) Fürtöt létrehozhat meglévő X.509-tanúsítvánnyal, vagy tesztelési célból létrehozhat egy új, önaírt tanúsítványt. A fürt egy virtuális hálózatban jön létre.

* **Mintaalkalmazás Service Fabric** – Hozzon létre egy webes API-alkalmazást, és telepítse azt a Service Fabric-fürtre az Integráció az [Azure-API Management](../service-fabric/service-fabric-tutorial-deploy-api-management.md)és Service Fabric leírtak szerint.

    Ezek a lépések létrehoznak egy alapszintű állapot nélküli ASP.NET a Core Reliable Service-hez az alapértelmezett Webes API-projektsablon használatával. Később elérhetővé teszi a szolgáltatás HTTP-végpontját az Azure API Management.

    Jegyezze fel az alkalmazás nevét, `fabric:/myApplication/myService` például: . 

* **API Management példány** – Egy meglévő vagy API Management példány a  **Prémium** vagy a Fejlesztői szinten, valamint az Service Fabric régióban. Ha szüksége van rá, [hozzon létre egy API Management példányt.](get-started-create-service-instance.md)

* **Virtuális hálózat** – Adja hozzá API Management-példányt a saját fürthöz létrehozott virtuális Service Fabric számára. API Management virtuális hálózatban dedikált alhálózatra van szükség.

  További lépések a virtuális hálózati kapcsolat engedélyezéséhez a API Management példány esetében: [Az Azure API Management használata virtuális hálózatokkal.](api-management-using-with-vnet.md)

## <a name="create-backend---portal"></a>Háttér létrehozása – portál

### <a name="add-service-fabric-cluster-certificate-to-api-management"></a>Fürt Service Fabric hozzáadása a API Management

A Service Fabric tanúsítvány tárolása és kezelése a fürthöz társított Azure-kulcstartóban történt. Adja hozzá ezt a tanúsítványt a API Management-példányhoz ügyfél-tanúsítványként.

A tanúsítvány saját példányhoz való hozzáadásának lépéseiért lásd: Háttérszolgáltatások biztonságossá API Management ügyfél-tanúsítványos hitelesítéssel az [Azure API Management.](api-management-howto-mutual-certificates.md) 

> [!NOTE]   
> Javasoljuk, hogy adja hozzá a tanúsítványt API Management a Key Vault-tanúsítványra való hivatkozással. 

### <a name="add-service-fabric-backend"></a>Háttér Service Fabric hozzáadása

1. A [Azure Portal](https://portal.azure.com)keresse meg a API Management példányát.
1. Az **API-k alatt** válassza a **Háttér + Hozzáadás**  >  **lehetőséget.**
1. Adja meg a háttér nevét és leírását (nem kötelező)
1. A **Típus mezőben** válassza a **Service Fabric** lehetőséget.
1. A **Futásidejű URL-cím** mezőben adja meg annak Service Fabric háttérszolgáltatásnak a nevét, API Management a kérelmeket. Példa: `fabric:/myApplication/myService`. 
1. A **Partíciófeloldási** újraküldetek maximális száma alatt adjon meg egy 0 és 10 közötti számot.
1. Adja meg a fürt felügyeleti Service Fabric végpontját. Ez a végpont a fürt URL-címe a `19080` porton, `https://mysfcluster.eastus.cloudapp.azure.com:19080` például: .
1. Az **Ügyfél tanúsítványa** területen válassza Service Fabric az előző szakaszban a API Management-példányhoz hozzáadott fürtözött tanúsítványt.
1. A **Felügyeleti végpont hitelesítési módszere** alatt adja meg a TLS-kommunikációhoz használt tanúsítvány ujjlenyomatát vagy X509-Service Fabric nevét.
1. Engedélyezze a **Tanúsítványlánc ellenőrzése és** **a Tanúsítványnév ellenőrzése** beállításokat.
1. Az **Engedélyezési hitelesítő adatok beállításnál** adja meg a hitelesítő adatokat, ha szükséges, hogy elérjék a konfigurált háttérszolgáltatást a Service Fabric. Az ebben a forgatókönyvben használt mintaalkalmazáshoz nincs szükség hitelesítő adatokra.
1. Válassza a **Létrehozás** lehetőséget.

:::image type="content" source="media/backends/create-service-fabric-backend.png" alt-text="Service Fabric-háttérszolgáltatás létrehozása":::

## <a name="use-the-backend"></a>A háttér használata

Egyéni háttér használata esetén hivatkozhat rá a szabályzat [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) használatával. Ez a szabályzat egy bejövő API-kérelem alapértelmezett háttérszolgáltatás-alap URL-címét alakítja át egy megadott háttérkiszolgálóra, ebben az esetben a Service Fabric háttérszolgáltatásra. 

A szabályzat akkor lehet hasznos, ha egy meglévő API-val egy bejövő kérést az API-beállításokban megadott háttértől eltérő `set-backend-service` háttérbe alakít át. A jelen cikkben bemutatási célokra hozzon létre egy tesztelési API-t, és állítsa be a szabályzatot, amely az API-kéréseket a Service Fabric háttérhez irányítja. 

### <a name="create-api"></a>API létrehozása

Üres API létrehozásához kövesse [az API manuális hozzáadása](add-api-manually.md) lépéseit.

* Az API-beállításokban hagyja üresen **a webszolgáltatás URL-címét.**
* Adjon hozzá **egy API URL-cím utótagját,** például *a hálót.*

  :::image type="content" source="media/backends/create-blank-api.png" alt-text="Üres API létrehozása":::

### <a name="add-get-operation-to-the-api"></a>GET művelet hozzáadása az API-hoz

Ahogy az Service Fabric-háttérszolgáltatás üzembe helyezésecímű példában látható, a Service Fabric-fürtön üzembe helyezett ASP.NET [Core-mintaszolgáltatás](../service-fabric/service-fabric-tutorial-deploy-api-management.md#deploy-a-service-fabric-back-end-service)egyetlen HTTP GET műveletet támogat az URL-útvonalon. `/api/values`

Az alapértelmezett válasz ezen az útvonalon egy két sztringet tartalmazó JSON-tömb:

```json
["value1", "value2"]
```

A fürttel való API Management teszteléséhez adja hozzá a megfelelő GET műveletet az API-hoz a elérési `/api/values` úton:

1. Válassza ki az előző lépésben létrehozott API-t.
1. Válassza a **+ Művelet hozzáadása** lehetőséget.
1. Az **Előtere ablakban** adja meg a következő értékeket, majd válassza a **Mentés lehetőséget.**

     | Beállítás             | Érték                             | 
    |---------------------|-----------------------------------|
    | **Megjelenített név**    | *Háttér tesztelése*                       |  
    | **URL-cím** | GET                               | 
    | **URL-cím**             | `/api/values`                           | 
    
    :::image type="content" source="media/backends/configure-get-operation.png" alt-text="GET művelet hozzáadása az API-hoz":::

### <a name="configure-set-backend-policy"></a>Szabályzat `set-backend` konfigurálása

Adja hozzá [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) a szabályzatot a tesztelési API-hoz.

1. A Tervezés **lap** Bejövő feldolgozás szakaszában **válassza** a kódszerkesztő ( ) **</>** ikont. 
1. A kurzor elhelyezése az **&lt; inbound elemen &gt;** belül
1. Adja hozzá a következő szabályzati utasítást. A `backend-id` fájlban helyettesítse be a Service Fabric nevét.

   A `sf-resolve-condition` egy újrapróbálkozási feltétel, ha a fürtpartíció nincs feloldva. A háttérkészlet konfigurálásakor beállított újrakonfigurált újrakomikák száma.

    ```xml
    <set-backend-service backend-id="mysfbackend" sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")"  />
    ```
1. Kattintson a **Mentés** gombra.

    :::image type="content" source="media/backends/set-backend-service.png" alt-text="A set-backend-service szabályzat konfigurálása":::

### <a name="test-backend-api"></a>Háttér-API tesztelése

1. A Teszt **lapon** válassza ki az előző szakaszban létrehozott **GET** műveletet.
1. Kattintson a **Küldés** gombra.

Ha megfelelően van konfigurálva, a HTTP-válasz egy HTTP-sikerkódot jelenít meg, és megjeleníti a háttérszolgáltatás által visszaadott JSON Service Fabric kódot.

:::image type="content" source="media/backends/test-backend-service.png" alt-text="A Service Fabric tesztelése":::

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan [konfigurálhatja a szabályzatokat](api-management-advanced-policies.md) a kérések háttérnek való továbbítása érdekében
* A háttért az API Management [REST API](/rest/api/apimanagement/2020-06-01-preview/backend), [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)vagy Azure Resource Manager is [konfigurálhatja](../service-fabric/service-fabric-tutorial-deploy-api-management.md)

