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
ms.openlocfilehash: c927682231bab1fc92f198c56a2f6aeff2f4c516
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553532"
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

Az Azure bevezető Premium SKU a saját forráshoz is kapcsolódhat a Private link Service használatával. Az alkalmazásai a saját VNet vagy egy, a webalkalmazás-és a Storage-fiók mögötti, a saját forrásának nyilvánosan elérhetővé tételéhez szükségesek.

:::image type="content" source="../media/concept-private-link/front-door-private-endpoint-architecture.png" alt-text="Az első ajtós privát végpontok architektúrája":::

Ha engedélyezi a saját forrásra mutató privát hivatkozást az Azure bejárati ajtó prémium konfigurációjában, akkor a bevezető ajtó egy privát végpontot hoz létre az Ön nevében az első ajtó regionális magánhálózati hálózatán. Ezt a végpontot az Azure-beli bejárati ajtó kezeli. Az Azure-beli bejárati ajtóhoz tartozó privát végpontra vonatkozó kérést kap a forrásként szolgáló jóváhagyási üzenethez. Miután jóváhagyta a kérést, egy magánhálózati IP-cím lesz hozzárendelve a bejárati ajtó virtuális hálózatáról, az Azure bejárati ajtó és a forrás közötti forgalom áthalad a létrehozott privát kapcsolaton az Azure hálózati gerincén. A forráshoz való bejövő forgalom mostantól védett az Azure bejárati ajtajáról.

:::image type="content" source="../media/concept-private-link/enable-private-endpoint.png" alt-text="Privát végpont engedélyezése":::

> [!NOTE]
> Miután engedélyezte a privát kapcsolat forrását, és jóváhagyja a privát végpont conenction, néhány percet vesz igénybe a kapcsolat létesítése. Ebben az időszakban a forráshoz intézett kérések egy előtérben lévő hibaüzenetet kapnak. A hibaüzenet a kapcsolatok létrehozása után fog elindulni.

## <a name="limitations"></a>Korlátozások

Az Azure Door Private-végpontok a nyilvános előzetes verzióban érhetők el a következő régiókban: USA keleti régiója, 2. nyugati régiója, USA déli középső régiója és Egyesült Királyság déli régiója.

A legjobb késés érdekében mindig válasszon ki egy, a forráshoz legközelebb eső Azure-régiót, amikor a bejárati ajtó privát kapcsolati végpontjának engedélyezését választja.

Az Azure-beli előtérben lévő privát végpontok a platform és az Azure bejárati ajtó előfizetése alatt kezelhetők. Az Azure-beli bejárati ajtó lehetővé teszi, hogy a privát kapcsolat kapcsolatai ugyanahhoz az ügyfél-előfizetéshez legyenek használva, amely az előtérben lévő profil

## <a name="next-steps"></a>Következő lépések

* Az Azure bejárati prémium csatlakoztatása a webalkalmazáshoz Private link Service [használatával: csatlakozás egy webalkalmazáshoz privát végponton](../../private-link/tutorial-private-endpoint-webapp-portal.md)keresztül.
* Ha az Azure bejárati Premiumot privát kapcsolaton keresztül szeretné csatlakoztatni a Storage-fiókjához, olvassa el [a Kapcsolódás a Storage-fiókhoz privát végpont használatával](../../private-link/tutorial-private-endpoint-storage-portal.md)című témakört.
