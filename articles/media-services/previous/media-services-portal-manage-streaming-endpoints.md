---
title: Adatfolyam-végpontok kezelése a Azure Portalokkal | Microsoft Docs
description: Ez a cikk bemutatja, hogyan kezelheti a folyamatos átviteli végpontokat a Azure Portal.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.assetid: bb1aca25-d23a-4520-8c45-44ef3ecd5371
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: bda244267e81d959abbc4ae0a8532ddce68d5726
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017392"
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Streamvégpontok kezelése az Azure Portalon

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Ez a cikk bemutatja, hogyan kezelhető a Azure Portal a folyamatos átviteli végpontok kezeléséhez. 

>[!NOTE]
>Ügyeljen rá, hogy tekintse át az [áttekintő](media-services-streaming-endpoints-overview.md) cikket. 

A folyamatos átviteli végpont skálázásával kapcsolatos információkért tekintse meg [ezt](media-services-portal-scale-streaming-endpoints.md) a cikket.

## <a name="start-managing-streaming-endpoints"></a>Adatfolyam-végpontok kezelésének megkezdése 

A fiókhoz tartozó streaming-végpontok kezelésének megkezdéséhez tegye a következőket.

1. Az [Azure-portálon](https://portal.azure.com/) válassza ki Azure Media Services-fiókját.
2. A **Beállítások** panelen válassza a **folyamatos átviteli végpontok** lehetőséget.
   
    ![Képernyőkép, amely a "beállítások" panelen kiválasztott "streaming endpoints" (Media Services) lapot jeleníti meg.](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> Csak akkor számítunk fel díjat, ha a folyamatos átviteli végpont fut állapotban van.

## <a name="adddelete-a-streaming-endpoint"></a>Adatfolyam-végpont hozzáadása/törlése

>[!NOTE]
>Az alapértelmezett folyamatos átviteli végpont nem törölhető.

Ha a Azure Portal használatával szeretne adatfolyam-végpontot hozzáadni/törölni, tegye a következőket:

1. Folyamatos átviteli végpont hozzáadásához kattintson a lap tetején található **+ végpontra** . 

    Előfordulhat, hogy több folyamatos átviteli végpontra van szüksége, ha eltérő CDNs vagy CDN-t vagy közvetlen hozzáférést tervez.

2. A folyamatos átviteli végpont törléséhez nyomja le a **delete (Törlés** ) gombot.      
3. A **Start** gombra kattintva indítsa el a folyamatos átviteli végpontot.
   
    ![Képernyőkép, amely megjeleníti a "végpont" műveletet, valamint a "streaming Endpoint details" panelt.](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a name="configuring-the-streaming-endpoint"></a><a id="configure_streaming_endpoints"></a>A folyamatos átviteli végpont konfigurálása
A folyamatos átviteli végpont lehetővé teszi a következő tulajdonságok konfigurálását:

* Hozzáférés-vezérlés
* Gyorsítótár-vezérlés
* Helyek közötti hozzáférési házirendek

További információ ezekről a tulajdonságokról: [streamvégpontok](/rest/api/media/operations/streamingendpoint).

>[!NOTE]
>Ha a CDN engedélyezve van, nem férhet hozzá az IP-hozzáféréshez. Az IP-hozzáférés csak akkor érvényes, ha nem rendelkezik CDN-vel.

Az adatfolyam-végpontot a következő módon konfigurálhatja:

1. Válassza ki a konfigurálni kívánt streaming-végpontot.
2. Kattintson a **Beállítások** elemre.

A mezők rövid leírása következik.

![A streaming végponthoz kiválasztott "beállítások" műveletet megjelenítő képernyőkép.](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. Maximális gyorsítótár-házirend: az ezen adatfolyam-végponton keresztül kiszolgált eszközök gyorsítótárazási élettartamának konfigurálására szolgál. Ha nincs megadva érték, a rendszer az alapértelmezett értéket használja. Az alapértelmezett értékek közvetlenül az Azure Storage-ban is meghatározhatók. Ha a Azure CDN engedélyezve van a folyamatos átviteli végpontnál, a gyorsítótár-házirend értékét ne állítsa 600 másodpercnél kisebbre.  
2. Engedélyezett IP-címek: azoknak az IP-címeknek a megadására használható, amelyek számára engedélyezett a közzétett adatfolyam-végponthoz való kapcsolódás. Ha nincs megadva IP-cím, az IP-címek csatlakozni tudnak. Az IP-címek megadhatók egyetlen IP-címként (például "10.0.0.1"), egy IP-cím és egy CIDR alhálózati maszk használatával (például "10.0.0.1/22"), vagy egy IP-címtartomány IP-cím és egy pontozott decimális alhálózati maszk (például "10.0.0.1 (255.255.255.0)") használatával.
3. Konfiguráció a Akamai aláírási fejlécének hitelesítéséhez: annak megadására szolgál, hogy a Akamai-kiszolgálók aláírási fejlécének hitelesítési kérelme hogyan legyen konfigurálva. A lejárat UTC szerint történik.

## <a name="scale-your-premium-streaming-endpoint"></a>A prémium szintű streaming-végpont skálázása

További információkért tekintse meg [ezt](media-services-portal-scale-streaming-endpoints.md) a cikket.

## <a name="enable-azure-cdn-integration"></a><a id="enable_cdn"></a>Azure CDN integráció engedélyezése

Új fiók létrehozásakor alapértelmezés szerint engedélyezve van az alapértelmezett folyamatos átviteli végpont Azure CDN integrációja.

Ha később le kívánja tiltani/engedélyezni szeretné a CDN-t, a folyamatos átviteli végpontnak **leállított** állapotban kell lennie. Akár két óráig is eltarthat, amíg a Azure CDN integrációja engedélyezve lesz, és a módosítások az összes CDN-pop-ban aktívak lesznek. Az adatfolyam-végpontot és a streamet azonban megszakítások nélkül is elindíthatja a streaming végpontról, és az integráció befejezését követően a stream a CDN-ből lesz továbbítva. A kiépítési időszak alatt a folyamatos átviteli végpont **kezdő** állapotba kerül, és előfordulhat, hogy megfigyelheti a teljesítményt.

A CDN-integráció az összes Azure-adatközpontban engedélyezve van, kivéve Kínát és a szövetségi kormányzati régiókat.

Ha engedélyezve van, a rendszer letiltja a * * Custom hostname és **Access Control** a **Akamai aláírás-hitelesítési** konfigurációját.
 
> [!IMPORTANT]
> A Azure Media Services integrációja Azure CDN-mel a Verizon standard streaming-végpontok **Azure CDNján** valósul meg. A prémium szintű streaming végpontok az összes **Azure CDN árképzési szint és szolgáltató** használatával konfigurálhatók. Azure CDN szolgáltatásokkal kapcsolatos további információkért tekintse meg a [CDN áttekintését](../../cdn/cdn-overview.md).
 
### <a name="additional-considerations"></a>Néhány fontos megjegyzés

* Ha a CDN engedélyezve van egy folyamatos átviteli végponton, az ügyfelek nem igényelhetnek tartalmat közvetlenül a forrástól. Ha a tartalmat CDN használatával vagy anélkül szeretné tesztelni, létrehozhat egy másik, CDN-t nem támogató streaming-végpontot.
* A folyamatos átviteli végpont állomásneve a CDN engedélyezése után is változatlan marad. A CDN engedélyezése után nem szükséges módosítania a Media Services-munkafolyamatot. Ha például az adatfolyam-végpont állomásneve a strasbourg.streaming.mediaservices.windows.net, a CDN engedélyezése után ugyanazt a gazdagépet használja a rendszer.
* Új folyamatos átviteli végpontok esetén a CDN-t egyszerűen új végpont létrehozásával engedélyezheti. meglévő folyamatos átviteli végpontok esetén először le kell állítania a végpontot, majd engedélyeznie/le kell tiltania a CDN-t.
* A standard folyamatos átviteli végpontot csak a klasszikus Azure portál használatával lehet a **Verizon standard CDN-szolgáltatóval** konfigurálni. A REST API-kkal azonban más Azure CDN szolgáltatók is engedélyezhetők.

## <a name="configure-cdn-profile"></a>CDN-profil konfigurálása

A CDN-profilt a felül található **CDN kezelése** gombra kattintva konfigurálhatja.

![Streamvégpont](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>Következő lépések
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
