---
title: API-k biztonságossá tétele az API Management-ben az ügyféltanúsítvány-alapú hitelesítés használatával
titleSuffix: Azure API Management
description: Megtudhatja, hogyan védheti meg az API-kat az Ügyféltanúsítványok használatával. A bejövő tanúsítványok érvényesítéséhez házirend-kifejezéseket használhat.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 553b4527796db3e5d0f430afd6c5e614626187e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99988884"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>API-k biztonságossá tétele ügyféltanúsítvány-alapú hitelesítéssel az API Managementben

A API Management az Ügyféltanúsítványok használatával biztonságossá teheti az API-khoz (például az ügyféltől a API Managementig) való hozzáférést. Érvényesítheti a bejövő tanúsítványokat, és ellenőrizheti a tanúsítvány tulajdonságait a kívánt értékekkel a házirend-kifejezések használatával.

További információ az API-k az Ügyféltanúsítványok használatával történő elérésének biztosításáról (azaz API Management a háttérrendszer számára): a [háttérbeli szolgáltatások biztonságossá tétele ügyféltanúsítvány-alapú hitelesítés használatával](./api-management-howto-mutual-certificates.md)

> [!IMPORTANT]
> Az ügyféltanúsítványok a fejlesztői, alapszintű, standard vagy prémium szinteken HTTP/2 protokollon keresztüli fogadásához és ellenőrzéséhez be kell kapcsolni az "egyéni tartományok" panel "ügyfél-tanúsítvány egyeztetése" beállítását az alábbi ábrán látható módon.

![Ügyféltanúsítvány egyeztetése](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> Az ügyféltanúsítványok a használati szinten való fogadásához és ellenőrzéséhez be kell kapcsolni az "egyéni tartományok" panel "ügyfél-tanúsítvány kérése" beállítását az alábbi ábrán látható módon.

![Ügyféltanúsítvány kérése](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>A kiállító és a tárgy ellenőrzése

Az alábbi házirendek konfigurálhatók a kiállító és az ügyféltanúsítvány tárgyának vizsgálatához:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> A tanúsítvány-visszavonási lista használatának ellenőrzésének letiltása a `context.Request.Certificate.VerifyNoRevocation()` helyett `context.Request.Certificate.Verify()` .
> Ha az ügyféltanúsítvány önaláírt, a legfelső szintű (vagy köztes) HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány (oka) t fel kell [tölteni](api-management-howto-ca-certificates.md) a API Managementre `context.Request.Certificate.Verify()` és `context.Request.Certificate.VerifyNoRevocation()` a működésre.

## <a name="checking-the-thumbprint"></a>Az ujjlenyomat ellenőrzése

Az alábbi házirendek konfigurálhatók az ügyféltanúsítvány ujjlenyomatának vizsgálatához:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "DESIRED-THUMBPRINT-IN-UPPER-CASE")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> A tanúsítvány-visszavonási lista használatának ellenőrzésének letiltása a `context.Request.Certificate.VerifyNoRevocation()` helyett `context.Request.Certificate.Verify()` .
> Ha az ügyféltanúsítvány önaláírt, a legfelső szintű (vagy köztes) HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány (oka) t fel kell [tölteni](api-management-howto-ca-certificates.md) a API Managementre `context.Request.Certificate.Verify()` és `context.Request.Certificate.VerifyNoRevocation()` a működésre.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Ujjlenyomat ellenőrzése a API Management feltöltött tanúsítványokkal szemben

Az alábbi példa bemutatja, hogyan ellenőrizhető az ügyféltanúsítvány ujjlenyomata a API Managementba feltöltött tanúsítványokkal szemben:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> A tanúsítvány-visszavonási lista használatának ellenőrzésének letiltása a `context.Request.Certificate.VerifyNoRevocation()` helyett `context.Request.Certificate.Verify()` .
> Ha az ügyféltanúsítvány önaláírt, a legfelső szintű (vagy köztes) HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány (oka) t fel kell [tölteni](api-management-howto-ca-certificates.md) a API Managementre `context.Request.Certificate.Verify()` és `context.Request.Certificate.VerifyNoRevocation()` a működésre.

> [!TIP]
> A jelen [cikkben](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) ismertetett ügyféltanúsítvány-alapú holtpont-probléma többféleképpen is megnyilvánulhat, például a kérések lefagyása után az időtúllépés után a kérések állapotkódot eredményeznek `403 Forbidden` `context.Request.Certificate` `null` . Ez a probléma általában `POST` a `PUT` tartalom hossza körülbelül 60KB vagy nagyobb.
> Ha meg szeretné akadályozni, hogy ez a probléma ne jelenjen meg, kapcsolja be az "ügyfél-tanúsítvány egyeztetése" beállítást a kívánt állomásnevek számára az "egyéni tartományok" panelen, ahogy az a jelen dokumentum első képében is látható. Ez a funkció nem érhető el a felhasználási szinten.

## <a name="certificate-validation-in-self-hosted-gateway"></a>Tanúsítvány érvényesítése önkiszolgáló átjáróban

Az alapértelmezett API Management [saját](self-hosted-gateway-overview.md) üzemeltetésű átjáró rendszerképe nem támogatja a kiszolgáló és az Ügyféltanúsítványok érvényesítését a API Management példányra feltöltött [hitelesítésszolgáltatói főtanúsítványok](api-management-howto-ca-certificates.md) használatával. A saját üzemeltetésű átjáróhoz egyéni tanúsítványt bemutató ügyfelek lassú válaszokat tapasztalhatnak, mivel a visszavont tanúsítványok listájának (CRL) ellenőrzése hosszú időt vehet igénybe az átjárón. 

Az átjáró futtatásakor megkerülő megoldásként beállíthatja, hogy a PKI IP-címe a API Management példány helyett a localhost-címmé mutasson. Ez azt eredményezi, hogy a CRL-ellenőrzés gyorsan leáll, amikor az átjáró megkísérli érvényesíteni az ügyféltanúsítványt. Az átjáró konfigurálásához vegyen fel egy DNS-bejegyzést a API Management példányhoz a tárolóban található fájlban lévő localhost-ra való feloldáshoz `/etc/hosts` . Ezt a bejegyzést az átjáró üzembe helyezése során adhatja hozzá:
 
* A Docker-telepítéshez adja hozzá a `--add-host <hostname>:127.0.0.1` paramétert a `docker run` parancshoz. További információ: [bejegyzések hozzáadása tároló gazdagépekhez fájl](https://docs.docker.com/engine/reference/commandline/run/#add-entries-to-container-hosts-file---add-host)
 
* A Kubernetes üzembe helyezéséhez – adjon hozzá egy `hostAliases` specifikációt a `myGateway.yaml` konfigurációs fájlhoz. További információ: [bejegyzések hozzáadása a pod/etc/hosts-hez a gazdagép aliasnevei alapján](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/).




## <a name="next-steps"></a>Következő lépések

-   [Háttérbeli szolgáltatások biztonságossá tétele ügyféltanúsítvány-alapú hitelesítés használatával](./api-management-howto-mutual-certificates.md)
-   [Tanúsítványok feltöltése](./api-management-howto-mutual-certificates.md)
