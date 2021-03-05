---
title: Hálózatelkülönítés
description: A felhasználók korlátozhatják a QnA Maker erőforrásokhoz való nyilvános hozzáférést.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 8fe8c07866b23e5d990b71bfc9cd556c338634d3
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203368"
---
# <a name="recommended-settings-for-network-isolation"></a>A hálózati elkülönítés ajánlott beállításai

A QnA Maker erőforrásaihoz való nyilvános hozzáférés korlátozásához kövesse az alábbi lépéseket. [A virtuális hálózat konfigurálásával](../../cognitive-services-virtual-networks.md?tabs=portal)a Cognitive Services-erőforrást a nyilvános eléréssel védi.

## <a name="restrict-access-to-cognitive-search-resource"></a>Cognitive Search erőforráshoz való hozzáférés korlátozása

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

Cognitive Search konfigurálása privát végpontként egy VNET belül. Ha egy QnA Maker erőforrás létrehozása során hoz létre keresési példányt, akkor kényszerítheti a Cognitive Searcht, hogy támogassa a teljes mértékben az ügyfél VNet létrehozott privát végpont-konfigurációt.

A privát végpontok használatához minden erőforrást ugyanabban a régióban kell létrehozni.

* Erőforrás QnA Maker
* új Cognitive Search erőforrás
* új Virtual Network erőforrás

Hajtsa végre a következő lépéseket a [Azure Portalban](https://portal.azure.com):

1. [QnA Maker erőforrás](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)létrehozása.
2. Hozzon létre egy új Cognitive Search erőforrást a végponti kapcsolat ( adatforrások) beállításával. Hozza létre az erőforrást ugyanabban a régióban, mint az 1. lépésben létrehozott QnA Maker erőforrást. További információ [Cognitive Search erőforrás létrehozásáról](../../../search/search-create-service-portal.md), majd a hivatkozás használatával közvetlenül az [erőforrás létrehozás lapjára](https://ms.portal.azure.com/#create/Microsoft.Search)léphet.
3. Hozzon létre egy új [Virtual Network erőforrást](https://ms.portal.azure.com/#create/Microsoft.VirtualNetwork-ARM).
4. Konfigurálja a VNET az eljárás 1. lépésében létrehozott app Service-erőforráson. Hozzon létre egy új DNS-bejegyzést a VNET a 2. lépésben létrehozott új Cognitive Search-erőforráshoz. a Cognitive Search IP-címhez.
5. [Rendelje hozzá az App Service-t a 2. lépésben létrehozott új Cognitive Search erőforráshoz](../how-to/set-up-qnamaker-service-azure.md) . Ezután törölheti az 1. lépésben létrehozott eredeti Cognitive Search-erőforrást.
    
Az [QnA Maker portálon](https://www.qnamaker.ai/)hozza létre első tudásbázisát.

#  <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

[Hozzon létre privát végpontokat](../reference-private-endpoint.md) a Azure Search erőforráshoz.

---

## <a name="restrict-access-to-app-service-qna-runtime"></a>App Service elérésének korlátozása (QnA Runtime)

IP-címeket adhat hozzá az App Service engedélyezési, amelyekkel korlátozhatja a hozzáférést, vagy konfigurálhatja App Service Environemnt QnA Maker App Service gazdagépre.

#### <a name="add-ips-to-app-service-allowlist"></a>IP-címek hozzáadása App Service engedélyezési

1. Csak Cognitive Services IP-címekről érkező forgalom engedélyezése. Ezek már szerepelnek a Service címkében `CognitiveServicesManagement` . Ez szükséges az API-k létrehozásához (létrehozás/frissítés KB) az App Service meghívásához és ennek megfelelően a Azure Search szolgáltatás frissítéséhez. [További információ a szolgáltatási címkékről.](../../../virtual-network/service-tags-overview.md)
2. Győződjön meg arról, hogy más belépési pontokat is engedélyez, például a Azure Bot Servicet, QnA Maker portált stb.) a "GenerateAnswer" API-hozzáférés előrejelzéséhez.
3. Az IP-címtartományok engedélyezési való hozzáadásához kövesse az alábbi lépéseket:

   1. Töltse le [az IP-tartományokat az összes szolgáltatás címkéhez](https://www.microsoft.com/download/details.aspx?id=56519).
   2. Válassza ki a "CognitiveServicesManagement" IP-címeit.
   3. Nyissa meg a App Service erőforrás hálózatkezelés szakaszát, és kattintson a "hozzáférési korlátozás konfigurálása" lehetőségre az IP-címek engedélyezési való hozzáadásához.

    ![bejövő portok kivételeinek](../media/inbound-ports.png)

Egy automatikus szkripttel is rendelkezünk, hogy megegyezzenek a App Service. A [engedélyezési konfigurálására szolgáló PowerShell-parancsfájlt](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1) a githubon találhatja meg. Az előfizetés azonosítóját, az erőforráscsoportot és a tényleges App Service nevet kell megadni parancsfájl-paraméterként. A parancsfájl futtatásakor a rendszer automatikusan hozzáadja az IP-címeket App Service engedélyezési.

#### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>App Service Environment konfigurálása a gazdagép QnA Maker App Service
    
A App Service Environment (a bemutató) QnA Maker app Service üzemeltetésére használható. Kövesse az alábbi lépéseket:

1. Hozzon létre egy App Service Environment, és jelölje meg a "külső" jelölést. Kérjük, kövesse az [oktatóanyag](../../../app-service/environment/create-external-ase.md) utasításait.
2.  Hozzon létre egy app Service-t a App Service Environmenton belül.
    1. Tekintse át az App Service konfigurációját, és adja hozzá az "PrimaryEndpointKey" beállítást. A "PrimaryEndpointKey" értéket a "-PrimaryEndpointKey" értékre kell beállítani \<app-name\> . Az alkalmazás neve az App Service URL-címében van meghatározva. Ha például az App Service URL-címe "mywebsite.myase.p.azurewebsite.net", akkor az alkalmazás neve "mywebsite". Ebben az esetben a "PrimaryEndpointKey" értéket a "mywebsite-PrimaryEndpointKey" értékre kell beállítani.
    2. Hozzon létre egy Azure Search szolgáltatást.
    3. Győződjön meg arról, hogy a Azure Search és az Alkalmazásbeállítások megfelelően vannak konfigurálva. 
          Kérjük, kövesse ezt az [oktatóanyagot](../reference-app-service.md?tabs=v1#app-service).
3.  A App Service Environment társított hálózati biztonsági csoport frissítése
    1. Frissítse az előre létrehozott bejövő biztonsági szabályokat az igényeinek megfelelően.
    2. Vegyen fel egy új bejövő biztonsági szabályt a forrásként a "Service tag" és a forrásoldali szolgáltatás címkével "CognitiveServicesManagement" néven.
4.  Hozzon létre egy QnA Maker kognitív szolgáltatási példányt (Microsoft. CognitiveServices/accounts) Azure Resource Manager használatával, ahol QnA Maker végpontot a fent létrehozott App Service végpontra kell beállítani (https://mywebsite.myase.p.azurewebsite.net).
    
---
