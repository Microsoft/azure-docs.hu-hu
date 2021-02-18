---
title: Saját forrásának védelme az Azure-beli előtérben standard/prémium (előzetes verzió)
description: Ezen a lapon megtudhatja, hogyan biztonságossá teheti a forrásokhoz való kapcsolódást a privát hivatkozás használatával.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: tyao
ms.custom: references_regions
ms.openlocfilehash: dead60b9d8e0872f3d46b1f223ccf5e6697cbd90
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099200"
---
# <a name="secure-your-origin-with-private-link-in-azure-front-door-standardpremium-preview"></a>Saját forrásának védelme az Azure-beli előtérben standard/prémium (előzetes verzió)

> [!Note]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Tekintse meg az Azure-beli előtérben lévő [dokumentumokat](../front-door-overview.md).

## <a name="overview"></a>Áttekintés

Az [Azure Private-hivatkozás](../../private-link/private-link-overview.md) lehetővé teszi, hogy a virtuális hálózat privát végpontján keresztül hozzáférjen az Azure Pásti-szolgáltatásokhoz és az Azure által üzemeltetett szolgáltatásokhoz. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget.

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure bevezető Premium SKU a Private link Service használatával tud csatlakozni a forráshoz. Alkalmazásai a saját virtuális hálózatban vagy egy, a nyilvános internetről nem elérhető, a saját virtuális hálózaton vagy egy Pásti-szolgáltatás mögött helyezhetők el.

:::image type="content" source="../media/concept-private-link/front-door-private-endpoint-architecture.png" alt-text="Az első ajtós privát végpontok architektúrája":::

Ha engedélyezi a saját forrásra mutató privát hivatkozást az Azure bejárati ajtó prémium konfigurációjában, akkor a bevezető ajtó egy privát végpontot hoz létre az Ön nevében az első ajtó regionális magánhálózati hálózatán. Ezt a végpontot az Azure-beli bejárati ajtó kezeli. Az Azure-beli bejárati ajtóhoz tartozó privát végpontra vonatkozó kérést kap a forrásként szolgáló jóváhagyási üzenethez. Miután jóváhagyta a kérést, egy magánhálózati IP-cím lesz hozzárendelve a bejárati ajtó virtuális hálózatáról, az Azure bejárati ajtó és a forrás közötti forgalom áthalad a létrehozott privát kapcsolaton az Azure hálózati gerincén. A forráshoz való bejövő forgalom mostantól védett az Azure bejárati ajtajáról.

:::image type="content" source="../media/concept-private-link/enable-private-endpoint.png" alt-text="Privát végpont engedélyezése":::

Az Azure bejárati Premium számos különböző típusú forrást támogat. Ha a forrás a magánhálózaton lévő virtuális gépek készletén található, először létre kell hoznia egy belső standard Load balancert, engedélyeznie kell a Private link Service-t a standard Load Balancer számára, majd ki kell választania az egyéni forrás típusát. A privát kapcsolat konfigurálása lapon válassza a "Microsoft. Network/PrivateLinkServices as Resource Type (erőforrás típusa) lehetőséget. A Pásti-szolgáltatások, például az Azure webalkalmazás és a Storage-fiók esetében először engedélyezheti a Private link Service-t a megfelelő szolgáltatásokból, és kiválaszthatja a Microsoft. Web/Sites elemet a webalkalmazáshoz és a Microsoft. Storage/StorageAccounts a Storage-fiók magánhálózati kapcsolati szolgáltatásaihoz.

## <a name="limitations"></a>Korlátozások

Az Azure Door Private-végpontok a nyilvános előzetes verzióban érhetők el a következő régiókban: USA keleti régiója, 2. nyugati régiója és az USA déli középső régiója.

A legjobb késés érdekében mindig válasszon ki egy, a forráshoz legközelebb eső Azure-régiót, amikor a bejárati ajtó privát kapcsolati végpontjának engedélyezését választja.

Az Azure-beli előtérben lévő privát végpontok a platform és az Azure bejárati ajtó előfizetése alatt kezelhetők. Az Azure-beli bejárati ajtó lehetővé teszi, hogy a privát kapcsolat kapcsolatai ugyanahhoz az ügyfél-előfizetéshez legyenek használva, amely az előtérben lévő profil

## <a name="next-steps"></a>Következő lépések

* Ha az Azure bejárati Premiumot a Private link Service használatával Virtual Machines csatlakoztatni, tekintse meg [a privát végpont létrehozása](../../private-link/create-private-endpoint-portal.md)című témakört.
* Az Azure bejárati prémium csatlakoztatása a webalkalmazáshoz Private link Service [használatával: csatlakozás egy webalkalmazáshoz privát végponton](../../private-link/tutorial-private-endpoint-webapp-portal.md)keresztül.
* Ha az Azure bejárati Premiumot privát kapcsolaton keresztül szeretné csatlakoztatni a Storage-fiókjához, olvassa el [a Kapcsolódás a Storage-fiókhoz privát végpont használatával](../../private-link/tutorial-private-endpoint-storage-portal.md)című témakört.
