---
title: Hálózatelkülönítés
description: A felhasználók korlátozhatják a QnA Maker erőforrásokhoz való nyilvános hozzáférést.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: afb396bc364a2fa2db923fbcbe6bfe1b7aedbc26
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467544"
---
# <a name="recommended-settings-for-network-isolation"></a>A hálózati elkülönítés ajánlott beállításai

Kövesse az alábbi lépéseket a QnA Maker erőforrásokhoz való nyilvános hozzáférés korlátozásához. [A virtuális hálózat konfigurálásával](../../cognitive-services-virtual-networks.md?tabs=portal)a Cognitive Services-erőforrást a nyilvános eléréssel védi.

## <a name="restrict-access-to-app-service-qna-runtime"></a>App Service elérésének korlátozása (QnA Runtime)

IP-címeket adhat hozzá az App Service engedélyezési listájához, hogy korlátozza a hozzáférést, vagy konfigurálja a App Service Environment QnA Maker App Service gazdagépre.

#### <a name="add-ips-to-app-service-allow-list"></a>IP-címek hozzáadása App Service engedélyezési listához

1. Csak Cognitive Services IP-címekről érkező forgalom engedélyezése. Ezek már szerepelnek a Service címkében `CognitiveServicesManagement` . Ez szükséges az API-k létrehozásához (létrehozás/frissítés KB) az App Service meghívásához és ennek megfelelően a Azure Search szolgáltatás frissítéséhez. [További információ a szolgáltatási címkékről.](../../../virtual-network/service-tags-overview.md)
2. Győződjön meg arról, hogy más belépési pontokat is engedélyez, például a Azure Bot Servicet, QnA Maker portált stb.) a "GenerateAnswer" API-hozzáférés előrejelzéséhez.
3. Az IP-címtartományok engedélyezési listához való hozzáadásához kövesse az alábbi lépéseket:

   1. Töltse le [az IP-tartományokat az összes szolgáltatás címkéhez](https://www.microsoft.com/download/details.aspx?id=56519).
   2. Válassza ki a "CognitiveServicesManagement" IP-címeit.
   3. Nyissa meg a App Service erőforrás hálózatkezelés szakaszát, és kattintson a "hozzáférési korlátozás konfigurálása" lehetőségre az IP-címek engedélyezési listához való hozzáadásához.

Egy automatikus szkripttel is rendelkezünk, hogy megegyezzenek a App Service. Az engedélyezési lista a GitHubon [való konfigurálásához a PowerShell-parancsfájlt](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1) találja. Az előfizetés azonosítóját, az erőforráscsoportot és a tényleges App Service nevet kell megadni parancsfájl-paraméterként. A parancsfájl futtatásakor a rendszer automatikusan hozzáadja az IP-címeket App Service engedélyezési listához.

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
       
    ![bejövő portok kivételeinek](../media/inbound-ports.png)

4.  Hozzon létre egy QnA Maker kognitív szolgáltatási példányt (Microsoft. CognitiveServices/accounts) Azure Resource Manager használatával, ahol QnA Maker végpontot a fent létrehozott App Service végpontra kell beállítani (https://mywebsite.myase.p.azurewebsite.net).
    
---

## <a name="restrict-access-to-cognitive-search-resource"></a>Cognitive Search erőforráshoz való hozzáférés korlátozása

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

A Cognitive Search példány egy privát végponton keresztül különíthető el a QnA Maker erőforrások létrehozása után. A magánhálózati végpontok kapcsolataihoz olyan VNet van szükség, amelyen keresztül a Search Service-példány elérhető. 

Ha a QnA Maker App Service App Service Environment használatával van korlátozva, használja ugyanazt a VNet, és hozzon létre egy privát végponti kapcsolódást az Cognitive Search-példánnyal. Hozzon létre egy új DNS-bejegyzést a VNet, hogy az Cognitive Search végpontot a Cognitive Search magánhálózati végpont IP-címére képezze. 

Ha nem használ App Service Environment a QnAMaker App Servicehoz, először hozzon létre egy új VNet-erőforrást, majd hozza létre a Cognitive Search példányhoz tartozó magánhálózati végponti kapcsolódást. Ebben az esetben a QnA Maker App Service [integrálni kell a VNet](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet) az Cognitive Search-példányhoz való kapcsolódáshoz. 

#  <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

[Hozzon létre privát végpontokat](../reference-private-endpoint.md) a Azure Search erőforráshoz.

---
