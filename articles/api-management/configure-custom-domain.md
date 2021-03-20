---
title: Egyéni tartománynév konfigurálása az Azure API Management-példányhoz
titleSuffix: Azure API Management
description: Ez a témakör bemutatja, hogyan konfigurálhat egyéni tartománynevet az Azure API Management-példányához.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: a7032c64efa486c65830e013373239647a368540
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92311143"
---
# <a name="configure-a-custom-domain-name-for-your-azure-api-management-instance"></a>Egyéni tartománynév beállítása az Azure API Management-példányhoz

Amikor létrehoz egy Azure API Management Service-példányt, az Azure a (például) altartományát rendeli hozzá `azure-api.net` `apim-service-name.azure-api.net` . Az API Management-végpontokat azonban a saját egyéni tartománynevével, például a **contoso.com** teheti elérhetővé. Ez az oktatóanyag bemutatja, hogyan képezhető le egy meglévő egyéni DNS-név egy API Management-példány által közzétett végpontokra.

> [!IMPORTANT]
> A API Management csak olyan kérelmeket fogad el, amelyek [állomásfejléc](https://tools.ietf.org/html/rfc2616#section-14.23) -értékei megfelelnek az alapértelmezett tartománynévnek vagy a konfigurált egyéni tartományneveknek.

> [!WARNING]
> Azok az ügyfelek, akik a tanúsítvány-rögzítést szeretnék használni az alkalmazásaik biztonságának javítása érdekében, az alapértelmezett tanúsítvány helyett egyéni tartománynevet és tanúsítványt kell használniuk. Azok az ügyfelek, amelyek az alapértelmezett tanúsítványt rögzítik, a nem felügyelt tanúsítvány tulajdonságaitól, illetve a nem javasolt eljárástól függenek.

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések végrehajtásához a következőket kell tennie:

-   Aktív Azure-előfizetés.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Egy API Management példány. További információ: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).
-   Az Ön vagy a szervezete tulajdonában álló egyéni tartománynév. Ez a témakör nem nyújt útmutatást az Egyéni tartománynév beszerzéséhez.
-   Egy DNS-kiszolgálón tárolt CNAME rekord, amely az egyéni tartománynevet az API Management-példány alapértelmezett tartománynevére képezi le. Ez a témakör nem nyújt útmutatást a CNAME rekordok üzemeltetéséhez.
-   Rendelkeznie kell egy nyilvános és titkos kulccsal rendelkező érvényes tanúsítvánnyal (. PFX). A tulajdonos vagy a tulajdonos alternatív neve (SAN) meg kell egyeznie a tartománynévvel (Ez lehetővé teszi API Management példány számára, hogy biztonságosan tegye elérhetővé a TLS-t).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Egyéni tartománynév beállítása a Azure Portal használatával

1. Navigáljon a API Management-példányhoz a [Azure Portal](https://portal.azure.com/).
1. Válassza az **Egyéni tartományok** lehetőséget.

    Több végpont is van, amelyekhez egyéni tartománynevet rendelhet hozzá. Jelenleg a következő végpontok érhetők el:

    - **Átjáró** (alapértelmezett: `<apim-service-name>.azure-api.net` ),
    - **Fejlesztői portál (örökölt)** (alapértelmezés: `<apim-service-name>.portal.azure-api.net` ),
    - **Fejlesztői portál** (alapértelmezett: `<apim-service-name>.developer.azure-api.net` ).
    - **Felügyelet** (alapértelmezett: `<apim-service-name>.management.azure-api.net` ),
    - **SCM** (alapértelmezett: `<apim-service-name>.scm.azure-api.net` ),

    > [!NOTE]
    > Csak az **átjáró** végpontja érhető el a felhasználási szinten való konfiguráláshoz.
    > Frissítheti az összes végpontot vagy azok némelyikét. Általában az ügyfelek Update **Gateway** (ez az URL-cím a API Management) és a **portálon** (a fejlesztői portál URL-címe) elérhetővé tett API meghívására szolgál.
    > A **felügyeleti** és az **SCM** -végpontokat csak a API Management példány tulajdonosai használják, így ritkábban rendelnek hozzá egyéni tartománynevet.
    > A **prémium** szint támogatja több állomásnév beállítását az **átjáró** végpontja számára.

1. Válassza ki a frissíteni kívánt végpontot.
1. A jobb oldali ablakban kattintson az **Egyéni** elemre.

    - Az **egyéni tartomány neve** mezőben adja meg a használni kívánt nevet. Például: `api.contoso.com`.
    - A **tanúsítványban** válasszon ki egy tanúsítványt Key Vault. Egy érvényes is feltölthető. PFX-fájl, és adja meg a **jelszavát**, ha a tanúsítvány jelszavas védelemmel van ellátva.

    > [!NOTE]
    > A helyettesítő tartománynevek, például `*.contoso.com` az összes szinten támogatottak, kivéve a felhasználási szintet.

    > [!TIP]
    > Javasoljuk, hogy használjon [Azure Key Vault a tanúsítványok kezeléséhez](../key-vault/certificates/about-certificates.md) , és állítsa be őket az automegújít értékre.
    > Ha a Azure Key Vault használatával kezeli az egyéni tartomány TLS/SSL-tanúsítványát, akkor győződjön meg arról, hogy a tanúsítvány bekerül a Key Vaultba [ _tanúsítványként_](/rest/api/keyvault/createcertificate/createcertificate), nem _titkosként_.
    >
    > A TLS/SSL-tanúsítvány lekéréséhez API Managementnak tartalmaznia kell a listát, és meg kell kapnia a titkokat a tanúsítványt tartalmazó Azure Key Vault. Azure Portal használatakor a rendszer az összes szükséges konfigurációs lépést automatikusan végrehajtja. Parancssori eszközök vagy felügyeleti API használatakor ezeket az engedélyeket manuálisan kell megadni. Ez két lépésben történik. Először használja a API Management-példány felügyelt identitások lapját, és győződjön meg arról, hogy a felügyelt identitás engedélyezve van, és jegyezze fel az oldalon megjelenő résztvevő azonosítóját. Másodszor, adja meg az engedélyek listáját, és szerezzen be titkokat a rendszerbiztonsági tag számára a tanúsítványt tartalmazó Azure Key Vault.
    >
    > Ha a tanúsítvány automatikus megújításra van beállítva, API Management automatikusan a szolgáltatás leállása nélkül fogja tudni felvenni a legújabb verziót (ha a API Managementi szint SLA-i. e. az összes szinten, kivéve a fejlesztői szintet).

1. Kattintson az Alkalmaz gombra.

    > [!NOTE]
    > A tanúsítvány hozzárendelésének folyamata az üzembe helyezés méretétől függően 15 vagy több percet is igénybe vehet. A fejlesztői SKU állásidővel rendelkezik, az alapszintű és a magasabb SKU-ban nincs leállás.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>DNS-konfiguráció

Ha az egyéni tartománynévhez konfigurálja a DNS-t, két lehetőség közül választhat:

-   Állítson be egy olyan CNAME rekordot, amely a konfigurált Egyéni tartománynév végpontján mutat.
-   Konfiguráljon egy olyan rekordot, amely a API Management átjáró IP-címére mutat.

> [!NOTE]
> Bár az API Management-példány IP-címe statikus, néhány esetben változhat. Ezért javasoljuk, hogy használjon CNAME-t az egyéni tartomány konfigurálásakor. A DNS-konfigurációs módszer kiválasztásakor vegye figyelembe a figyelmet. További tudnivalókat az [IP-dokumentációs cikkben](api-management-howto-ip-addresses.md#changes-to-the-ip-addresses) és a [API Management GYIK](api-management-faq.md#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)tartalmaz.

## <a name="next-steps"></a>Következő lépések

[A szolgáltatás frissítése és méretezése](upgrade-and-scale.md)
