---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: 1858317d40efa59b188ce894534be93a1f11b287
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96026491"
---
## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>Az APIM-proxykiszolgáló válasza SSL-tanúsítványokkal a TLS-kézfogásban

### <a name="clients-calling-with-sni-header"></a>SNI-fejlécet hívó ügyfelek
Ha az ügyfél egy vagy több, proxyhoz konfigurált egyéni tartománnyal rendelkezik, a APIM válaszolhat a HTTPS-kérelmekre az egyéni tartomány (ok) ból (például contoso.com) és az alapértelmezett tartományba (például apim-service-name.azure-api.net). A (z) Kiszolgálónév jelzése (SNI) fejlécben található információk alapján a APIM a megfelelő kiszolgálói tanúsítvánnyal válaszol.

### <a name="clients-calling-without-sni-header"></a>SNI-fejléc nélküli hívást igénylő ügyfelek
Ha az ügyfél olyan ügyfelet használ, amely nem küldi el a [SNI](https://tools.ietf.org/html/rfc6066#section-3) -fejlécet, a APIM a következő logika alapján hoz létre válaszokat:

* Ha a szolgáltatáshoz csak egy egyéni tartomány van konfigurálva a proxyhoz, az alapértelmezett tanúsítvány a proxy egyéni tartomány számára kiállított tanúsítvány.
* Ha a szolgáltatás több egyéni tartományt konfigurált a proxyhoz (a **fejlesztő** és a **prémium** szint támogatott), az ügyfél kijelölheti, hogy melyik tanúsítvány legyen az alapértelmezett tanúsítvány. Az alapértelmezett tanúsítvány beállításához a [defaultSslBinding](/rest/api/apimanagement/2019-12-01/apimanagementservice/createorupdate#hostnameconfiguration) tulajdonságot igaz értékre kell beállítani ("defaultSslBinding": "true"). Ha az ügyfél nem állítja be a tulajdonságot, az alapértelmezett tanúsítvány az a tanúsítvány, amelyet a rendszer a *. azure-api.net-ben üzemeltetett alapértelmezett proxy tartományhoz állít ki.

## <a name="support-for-putpost-request-with-large-payload"></a>Nagy adattartalommal rendelkező PUT/POST kérelem támogatása

A APIM proxykiszolgáló támogatja a nagy adattartalommal rendelkező kérelmeket, amikor ügyféloldali tanúsítványokat használ a HTTPS-ben (például adattartalom > 40 KB). Ha meg szeretné akadályozni, hogy a kiszolgáló lefagyjon, a "NegotiateClientCertificate" tulajdonságot a proxy állomásnévnél állíthatja be [: "true" (igaz](/rest/api/apimanagement/2019-12-01/ApiManagementService/CreateOrUpdate#hostnameconfiguration) ). Ha a tulajdonság értéke TRUE (igaz), az ügyféltanúsítvány SSL/TLS kapcsolati időpontban, a HTTP-kérések cseréje előtt szükséges. Mivel a beállítás a **proxy állomásnév** szintjén érvényes, az összes kapcsolatkérelem kéri az ügyféltanúsítványt. Az ügyfelek legfeljebb 20 egyéni tartományt állíthatnak be a proxyhoz (csak a **prémium** szint esetében támogatott), és megkerülheti ezt a korlátozást.