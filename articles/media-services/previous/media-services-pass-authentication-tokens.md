---
title: Hitelesítési jogkivonatok átadása Azure Media Servicesba | Microsoft Docs
description: Megtudhatja, hogyan küldhet hitelesítési jogkivonatokat az ügyfélről a Azure Media Services Key Delivery Service-be
services: media-services
keywords: tartalomvédelem, DRM, jogkivonat-hitelesítés
author: IngridAtMicrosoft
manager: femila
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 1fe692e1eb20956f339c9b861f50163cee9c5063
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564653"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Ismerje meg, hogy az ügyfelek hogyan adják át a jogkivonatokat a Azure Media Services Key Delivery Service-nek

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Az ügyfelek gyakran kérdezik le, hogy egy játékos hogyan adhat jogkivonatokat a Azure Media Services Key Delivery szolgáltatásnak ellenőrzés céljából, hogy a lejátszó beszerezze a kulcsot. Media Services támogatja az egyszerű webes jogkivonat (SWT) és a JSON Web Token (JWT) formátumait. A jogkivonat-hitelesítés bármilyen típusú kulcsra vonatkozik, függetlenül attól, hogy a rendszer közös titkosítási vagy Advanced Encryption Standard (AES) titkosítást használ-e.

 A megcélzott lejátszótól és platformtól függően a tokent a következő módokon adhatja át a lejátszójának:

- A HTTP-engedélyezési fejlécen keresztül.
    > [!NOTE]
    > A "tulajdonos" előtagot a OAuth 2,0 specifikációja alapján kell elvárni. A videó forrásának beállításához válassza az **AES (JWT token)** vagy az **AES (SWT token)** elemet. A jogkivonat az engedélyezési fejlécen keresztül lesz átadva.

- Egy URL-lekérdezési paraméter hozzáadásával "token = tokenvalue".  
    > [!NOTE]
    > A "tulajdonos" előtag nem várt. Mivel a tokent egy URL-címen keresztül küldik el, a jogkivonat karakterláncát kell megadnia. Itt látható egy C#-mintakód, amely bemutatja, hogyan végezheti el:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- A CustomData mezőn keresztül.
Ez a beállítás csak a PlayReady-licencek beszerzéséhez használható, a PlayReady-licenc beszerzése kihívás CustomData mezőjében. Ebben az esetben a tokennek az XML-dokumentumban kell lennie az itt leírtak szerint:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Helyezze a hitelesítési tokent a jogkivonat elembe.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
