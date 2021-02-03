---
title: Service Fabric háttérrendszer beállítása az Azure API Managementban | Microsoft Docs
description: Service Fabric szolgáltatásbeli háttér létrehozása az Azure API Management a Azure Portal használatával
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.openlocfilehash: f6474dbd02c501612b951ddae490385a5d843fbf
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500629"
---
# <a name="set-up-a-service-fabric-backend-in-api-management-using-the-azure-portal"></a>Service Fabric-háttér beállítása a API Management a Azure Portal használatával

Ez a cikk bemutatja, hogyan konfigurálhat egy [Service Fabric](../service-fabric/service-fabric-api-management-overview.md) szolgáltatást egyéni API-háttérként a Azure Portal használatával. Demonstrációs célokra azt mutatja be, hogyan állíthat be egy alapszintű állapot nélküli ASP.NET Core megbízható szolgáltatást Service Fabric háttérként.

A háttérben tekintse meg a [API Management](backends.md)háttérrendszer című témakört.

## <a name="prerequisites"></a>Előfeltételek

A Service Fabric-fürtön futó minta-szolgáltatás egyéni háttérként való konfigurálásának előfeltételei:

* **Windows-fejlesztési környezet** – a [Visual Studio 2019](https://www.visualstudio.com) és az **Azure Development**, **ASP.net és Web Development**, valamint a **.net Core platformfüggetlen fejlesztési** számítási feladatok telepítése. Ezután hozzon létre egy [.NET fejlesztési környezet](../service-fabric/service-fabric-get-started.md).

* **Service Fabric-fürt** – [útmutató: Windows rendszerű Service Fabric-fürt üzembe helyezése egy Azure-beli virtuális hálózatban](../service-fabric/service-fabric-tutorial-create-vnet-and-windows-cluster.md). Létrehozhat egy meglévő X. 509 tanúsítvánnyal rendelkező fürtöt, vagy tesztelési célból létrehozhat egy új, önaláírt tanúsítványt. A fürt virtuális hálózatban jön létre.

* **Példa Service Fabric alkalmazásra** – webes API-alkalmazás létrehozása és üzembe helyezése a Service Fabric-fürtön az Azure-beli [Service Fabric integrálásával API Management](../service-fabric/service-fabric-tutorial-deploy-api-management.md).

    Ezek a lépések egy alapszintű állapot nélküli ASP.NET Core megbízható szolgáltatást hoznak létre az alapértelmezett webes API-projekt sablon használatával. Később elérhetővé teszi a szolgáltatáshoz tartozó HTTP-végpontot az Azure API Managementon keresztül.

    Jegyezze fel például az alkalmazás nevét `fabric:/myApplication/myService` . 

* **API Management példány** – egy meglévő vagy új API Management-példány a **prémium** vagy  **fejlesztői** szinten, valamint a Service Fabric-fürtöt tartalmazó régióban. Ha szüksége van erre, [hozzon létre egy API Management példányt](get-started-create-service-instance.md).

* **Virtuális hálózat** – adja hozzá API Management példányát a Service Fabric-fürthöz létrehozott virtuális hálózathoz. API Management dedikált alhálózatot igényel a virtuális hálózaton.

  A API Management példány virtuális hálózati kapcsolatának engedélyezéséhez lásd: az [Azure API Management használata virtuális hálózatokkal](api-management-using-with-vnet.md).

## <a name="create-backend---portal"></a>Háttérrendszer létrehozása – portál

### <a name="add-service-fabric-cluster-certificate-to-api-management"></a>Service Fabric fürt tanúsítványának hozzáadása API Management

A rendszer a fürthöz társított Azure Key vaultban tárolja és kezeli a Service Fabric-fürt tanúsítványát. Adja hozzá ezt a tanúsítványt a API Management-példányhoz ügyféltanúsítványként.

A tanúsítványnak a API Management-példányhoz való hozzáadásának lépéseiért lásd: [a háttér-szolgáltatások biztonságossá tétele az ügyféltanúsítvány-alapú hitelesítés használatával az Azure-ban API Management](api-management-howto-mutual-certificates.md). 

> [!NOTE]   
> Javasoljuk, hogy adja hozzá a tanúsítványt a API Managementhoz a Key Vault-tanúsítványra való hivatkozással. 

### <a name="add-service-fabric-backend"></a>Service Fabric háttér hozzáadása

1. A [Azure Portal](https://portal.azure.com)navigáljon a API Management-példányhoz.
1. Az **API**-k területen válassza a **háttérrendszer**  >  **+ Hozzáadás** lehetőséget.
1. Adja meg a háttér nevét és leírását (nem kötelező)
1. A **Típus mezőben** válassza a **Service Fabric** lehetőséget.
1. A **futásidejű URL-cím** mezőben adja meg annak a Service Fabric háttér-szolgáltatásnak a nevét, amelybe a rendszer a kérelmeket API Management továbbítja. Példa: `fabric:/myApplication/myService`. 
1. A **partíció-feloldási újrapróbálkozások maximális száma** mezőben adjon meg egy 0 és 10 közötti számot.
1. Adja meg a Service Fabric-fürt felügyeleti végpontját. Ez a végpont a porton lévő fürt URL-címe `19080` , például: `https://mysfcluster.eastus.cloudapp.azure.com:19080` .
1. Az **ügyféltanúsítvány** területen válassza ki az előző szakaszban a API Management-példányhoz hozzáadott Service Fabric-fürt tanúsítványát.
1. A **felügyeleti végpont engedélyezési módszerében** adja meg a TLS-kommunikációhoz Service Fabric fürtszolgáltatás által használt tanúsítvány ujjlenyomatát vagy X509 nevét.
1. Engedélyezze a **tanúsítványlánc ellenőrzése** és a **tanúsítvány nevének ellenőrzése** beállítást.
1. Az **engedélyezési hitelesítő adatok** területen adja meg a hitelesítő adatokat, ha szükséges, a konfigurált háttér-szolgáltatás eléréséhez Service Fabric. Az ebben a forgatókönyvben használt minta alkalmazás esetében az engedélyezési hitelesítő adatok nem szükségesek.
1. Válassza a **Létrehozás** lehetőséget.

:::image type="content" source="media/backends/create-service-fabric-backend.png" alt-text="Service Fabric-háttér létrehozása":::

## <a name="use-the-backend"></a>A háttér használata

Ha egyéni hátteret szeretne használni, hivatkozzon a [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) szabályzat használatára. Ez a szabályzat átalakítja egy bejövő API-kérelem alapértelmezett háttérbeli szolgáltatásának URL-címét egy adott háttérre, ebben az esetben a Service Fabric háttérrendszer-felületre. 

A `set-backend-service` házirend hasznos lehet egy meglévő API-val, hogy egy bejövő kérést egy másik háttérbe alakítsanak, mint az API-beállításokban megadott érték. Ennek a cikknek a szemléltetése érdekében hozzon létre egy tesztelési API-t, és állítsa be a szabályzatot úgy, hogy az API-kérelmeket a Service Fabric háttérbe 

### <a name="create-api"></a>API létrehozása

Ha üres API-t szeretne létrehozni, kövesse az [API hozzáadása manuálisan](add-api-manually.md) című témakör lépéseit.

* Az API-beállítások területen hagyja üresen a **webszolgáltatás URL-címét** .
* Adjon hozzá egy **API URL-utótagot**, például a *hálót*.

  :::image type="content" source="media/backends/create-blank-api.png" alt-text="Üres API létrehozása":::

### <a name="add-get-operation-to-the-api"></a>GET művelet hozzáadása az API-hoz

Ahogy az [egy Service Fabric háttérrendszer üzembe](../service-fabric/service-fabric-tutorial-deploy-api-management.md#deploy-a-service-fabric-back-end-service)helyezése című ábrán is látható, a Service Fabric-fürtön telepített minta ASP.net Core szolgáltatás egyetlen HTTP Get műveletet támogat az URL-cím elérési útján `/api/values` .

Az elérési út alapértelmezett válasza egy két sztring JSON-tömbje:

```json
["value1", "value2"]
```

A API Management fürthöz való integrálásának teszteléséhez adja hozzá a megfelelő GET műveletet az API-hoz az elérési úton `/api/values` :

1. Válassza ki az előző lépésben létrehozott API-t.
1. Válassza a **+ Művelet hozzáadása** lehetőséget.
1. A **frontend** ablakban adja meg a következő értékeket, majd kattintson a **Mentés** gombra.

     | Beállítás             | Érték                             | 
    |---------------------|-----------------------------------|
    | **Megjelenített név**    | *Tesztelési háttér*                       |  
    | **URL-cím** | GET                               | 
    | **URL-cím**             | `/api/values`                           | 
    
    :::image type="content" source="media/backends/configure-get-operation.png" alt-text="GET művelet hozzáadása az API-hoz":::

### <a name="configure-set-backend-policy"></a>`set-backend`Házirend konfigurálása

Adja hozzá a [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) szabályzatot a teszt API-hoz.

1. A **tervezés** lap **bejövő feldolgozás** szakaszában válassza a Kódszerkesztő ( **</>** ) ikont. 
1. A kurzor elhelyezése a **&lt; bejövő &gt;** elemen belül
1. Adja hozzá a következő házirend-utasítást. A alkalmazásban `backend-id` cserélje ki a Service Fabric háttér nevét.

   Az `sf-resolve-condition` újrapróbálkozási feltétel, ha a fürt partíciója nincs feloldva. Az újrapróbálkozások száma a háttér konfigurálásakor lett beállítva.

    ```xml
    <set-backend-service backend-id="mysfbackend" sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")"  />
    ```
1. Kattintson a **Mentés** gombra.

    :::image type="content" source="media/backends/set-backend-service.png" alt-text="Set-backend-Service házirend konfigurálása":::

### <a name="test-backend-api"></a>Háttérrendszer-API tesztelése

1. A **teszt** lapon válassza ki az előző szakaszban létrehozott **Get** műveletet.
1. Kattintson a **Küldés** gombra.

Ha megfelelően van konfigurálva, a HTTP-válasz egy HTTP-sikerességi kódot jelenít meg, és megjeleníti a háttérbeli Service Fabric szolgáltatás által visszaadott JSON-t.

:::image type="content" source="media/backends/test-backend-service.png" alt-text="Tesztelés Service Fabric háttérrendszer":::

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan [konfigurálhat házirendeket](api-management-advanced-policies.md) a kérelmeknek a háttérbe való továbbításához
* A háttérrendszer a API Management [REST API](/rest/api/apimanagement/2020-06-01-preview/backend), [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)vagy [Azure Resource Manager sablonok](../service-fabric/service-fabric-tutorial-deploy-api-management.md) használatával is konfigurálható

