---
title: Hitelesítési jogkivonatok átadása Media Services v3-ra | Microsoft Docs
description: Megtudhatja, hogyan küldhet hitelesítési jogkivonatokat az ügyfélről a Media Services v3 Key Delivery Service-be
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 590309ad392876ba3c5cb6d21abe777ab5a93efb
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282195"
---
# <a name="pass-tokens-to-the-azure-media-services-v3-key-delivery-service"></a>Jogkivonatok átadása az Azure Media Services v3 kulcs kézbesítési szolgáltatásának

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Az ügyfelek gyakran kérdezik le, hogy egy játékos hogyan adhat jogkivonatokat a Azure Media Services Key Delivery szolgáltatásnak ellenőrzés céljából, hogy a lejátszó beszerezze a kulcsot. Media Services támogatja az egyszerű webes jogkivonat (SWT) és a JSON Web Token (JWT) formátumait. A jogkivonat-hitelesítés bármilyen típusú kulcsra vonatkozik, függetlenül attól, hogy a rendszer közös titkosítási vagy Advanced Encryption Standard (AES) titkosítást használ-e.

 A megcélzott lejátszótól és platformtól függően a tokent a következő módokon adhatja át a lejátszójának:

## <a name="pass-a-token-through-the-http-authorization-header"></a>Token továbbítása a HTTP-engedélyezési fejlécen keresztül

> [!NOTE]
> A "tulajdonos" előtagot a OAuth 2,0 specifikációja alapján kell elvárni. A videó forrásának beállításához válassza az **AES (JWT token)** vagy az **AES (SWT token)** elemet. A jogkivonat az engedélyezési fejlécen keresztül lesz átadva.

## <a name="pass-a-token-via-the-addition-of-a-url-query-parameter-with-tokentokenvalue"></a>Adjon át egy tokent egy URL-lekérdezési paraméter hozzáadásával "token = tokenvalue" értékkel.

> [!NOTE]
> A "tulajdonos" előtag nem várt. Mivel a tokent egy URL-címen keresztül küldik el, a jogkivonat karakterláncát kell megadnia. Itt látható egy C#-mintakód, amely bemutatja, hogyan végezheti el:

```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
```

## <a name="pass-a-token-through-the-customdata-field"></a>Token továbbítása a CustomData mezőben

Ez a beállítás csak a PlayReady-licencek beszerzéséhez használható, a PlayReady-licenc beszerzése kihívás CustomData mezőjében. Ebben az esetben a tokennek az XML-dokumentumban kell lennie az itt leírtak szerint:

```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
```

Helyezze a hitelesítési tokent a jogkivonat elembe.
