---
title: Felügyelt alkalmazások értesítésekkel
description: A felügyelt alkalmazások webhook-végpontokkal történő konfigurálásával értesítéseket kaphat a felügyelt alkalmazás példányain létrehozott létrehozási, frissítési, törlési és hibákról.
ms.topic: conceptual
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: 2a2e9d429d494c35c49a5b0a3e10b291fd8f24a6
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633937"
---
# <a name="azure-managed-applications-with-notifications"></a>Azure által felügyelt alkalmazások értesítésekkel

Az Azure Managed Application Notifications lehetővé teszi, hogy a közzétevők a felügyelt alkalmazás példányainak Életciklus-eseményei alapján automatizálják a műveleteket. A közzétevők egyéni értesítési webhook-végpontokat is megadhatnak az új és a meglévő felügyelt alkalmazási példányokkal kapcsolatos esemény-értesítések fogadásához. A kiadók egyéni munkafolyamatokat állíthatnak be az alkalmazások üzembe helyezése, frissítése és törlése során.

## <a name="getting-started"></a>Első lépések
A felügyelt alkalmazások fogadásának megkezdéséhez hozzon létre egy nyilvános HTTPS-végpontot, és a Service Catalog alkalmazás-definíció vagy az Azure Marketplace-ajánlat közzétételekor tegye meg.

A gyors kezdéshez a következő lépéseket javasoljuk:
1. Hozzon létre egy nyilvános HTTPS-végpontot, amely naplózza a beérkező POST kéréseket, és visszatér `200 OK` .
2. Adja hozzá a végpontot a Service Catalog alkalmazás-definícióhoz vagy az Azure Marketplace-ajánlathoz a cikk későbbi részében leírtak szerint.
3. Hozzon létre egy felügyelt alkalmazás-példányt, amely az alkalmazás-definícióra vagy az Azure Marketplace ajánlatra hivatkozik.
4. Ellenőrizze, hogy az értesítések fogadása megtörtént-e.
5. Engedélyezze az engedélyezést a jelen cikk **végpont-hitelesítés** című szakaszában leírtak szerint.
6. A jelen cikk **értesítési séma** szakaszának utasításait követve elemezheti az értesítési kérelmeket, és az értesítés alapján megvalósíthatja az üzleti logikát.

## <a name="add-service-catalog-application-definition-notifications"></a>Service Catalog alkalmazás-definíciós értesítések hozzáadása
#### <a name="azure-portal"></a>Azure Portal
Első lépésként tekintse meg [a Service Catalog-alkalmazás közzététele Azure Portal segítségével](./publish-portal.md)című témakört.

![A Service Catalog alkalmazás-definíciós értesítései a Azure Portal](./media/publish-notifications/service-catalog-notifications.png)

#### <a name="rest-api"></a>REST API

> [!NOTE]
> Jelenleg csak egy végpontot adhat meg az `notificationEndpoints` alkalmazás definíciójának tulajdonságaiban.

``` JSON
    {
      "properties": {
        "isEnabled": true,
        "lockLevel": "ReadOnly",
        "displayName": "Sample Application Definition",
        "description": "Notification-enabled application definition.",
        "notificationPolicy": {
            "notificationEndpoints": [
                {
                    "uri": "https://isv.azurewebsites.net:1214?sig=unique_token"
                }
            ]
        },
        "authorizations": [
          {
            "principalId": "d6b7fbd3-4d99-43fe-8a7a-f13aef11dc18",
            "roleDefinitionId": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635"
          },
        ...

```
## <a name="add-azure-marketplace-managed-application-notifications"></a>Azure Marketplace által felügyelt alkalmazások értesítéseinek hozzáadása
További információ: Azure- [alkalmazási ajánlat létrehozása](../../marketplace/create-new-azure-apps-offer.md).

![Az Azure Marketplace által felügyelt alkalmazások értesítései a Azure Portal](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Eseményindítók
A következő táblázat a EventType és a ProvisioningState lehetséges kombinációit és azok eseményindítóit ismerteti:

EventType | ProvisioningState | Értesítési trigger
---|---|---
PUT | Elfogadva | A felügyelt erőforráscsoport létre lett hozva, és az alkalmazás üzembe helyezése után sikeresen befejeződött (a felügyelt erőforráscsoporthoz való központi telepítés elindítását megelőzően).
PUT | Sikeres | A felügyelt alkalmazás teljes kiépítés sikerült egy PUT után.
PUT | Sikertelen | Az alkalmazás-példány üzembe helyezésének meghibásodása bármely ponton.
JAVÍTÁS | Sikeres | A felügyelt alkalmazás példányának sikeres JAVÍTÁSát követően a címkék, a JIT hozzáférési házirend vagy a felügyelt identitás frissítése sikerült.
DELETE | Törlés | Amint a felhasználó elindít egy felügyelt alkalmazás példányának TÖRLÉSét.
DELETE | Törölve | A felügyelt alkalmazás teljes és sikeres törlése után.
DELETE | Sikertelen | A törlést blokkoló megszüntetési folyamat során felmerülő bármilyen hiba.
## <a name="notification-schema"></a>Értesítési séma
Amikor felgyorsítja a webhook-végpontot az értesítések kezeléséhez, elemezni kell a hasznos adatokat, hogy a fontos tulajdonságok beolvasása után az értesítésre reagáljon. A Service Catalog és az Azure Marketplace által felügyelt alkalmazások értesítései számos azonos tulajdonságot biztosítanak. A mintákat követő táblázatban két kis különbség szerepel.

#### <a name="service-catalog-application-notification-schema"></a>Service Catalog-alkalmazás értesítési sémája
A következő egy minta Service Catalog-értesítés a felügyelt alkalmazás példányának sikeres kiépítés után:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "applicationDefinitionId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>"    
}

```

Ha a kiépítés sikertelen, a rendszer a hiba részleteivel kapcsolatos értesítést küld a megadott végpontnak.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "applicationDefinitionId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>",
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

#### <a name="azure-marketplace-application-notification-schema"></a>Azure Marketplace-alkalmazás értesítési sémája

A következő egy minta Service Catalog-értesítés a felügyelt alkalmazás példányának sikeres kiépítés után:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    }
}

```

Ha a kiépítés sikertelen, a rendszer a hiba részleteivel kapcsolatos értesítést küld a megadott végpontnak.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    },
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

Paraméter | Leírás
---|---
eventType | Az értesítést kiváltó esemény típusa. (Például PUT, PATCH, DELETE.)
applicationId | Annak a felügyelt alkalmazásnak a teljes erőforrás-azonosítója, amelyhez az értesítés aktiválva lett.
eventTime | Az értesítést kiváltó esemény időbélyegzője. (Dátum és idő UTC ISO 8601 formátumban)
provisioningState | A felügyelt alkalmazás példányának kiépítési állapota. (Például sikeres, sikertelen, törlés, törölt.)
error | *Csak akkor van megadva, ha a ProvisioningState sikertelen*. A hibát okozó probléma kódját, üzenetét és részleteit tartalmazza.
applicationDefinitionId | *Csak a Service Catalog által felügyelt alkalmazásokhoz van megadva*. Annak az alkalmazás-definíciónak a teljes erőforrás-azonosítóját jelöli, amelyhez a felügyelt alkalmazás példánya ki lett építve.
csomag | *Csak az Azure Marketplace által felügyelt alkalmazásokhoz van megadva*. A felügyelt alkalmazás példányának közzétevőjét, ajánlatát, SKU-jának és verziószámát jelöli.
billingDetails | *Csak az Azure Marketplace által felügyelt alkalmazásokhoz van megadva.* A felügyelt alkalmazás példányának számlázási adatai. Azokat a resourceUsageId tartalmazza, amelyekkel lekérdezheti az Azure Marketplace-en a használati adatokat.

## <a name="endpoint-authentication"></a>Végponti hitelesítés
A webhook-végpont biztonságossá tételéhez és az értesítés hitelességének biztosításához:
1. Adjon meg egy lekérdezési paramétert a webhook URI-ja felett, például: https \: //Your-Endpoint.com? SIG = GUID. Minden értesítésnél győződjön meg arról, hogy a lekérdezési paraméter `sig` a várt értékkel rendelkezik `Guid` .
2. Adja ki a beolvasást a felügyelt alkalmazás példányán a applicationId használatával. Ellenőrizze, hogy a provisioningState megegyezik-e az értesítés provisioningState, hogy a konzisztencia biztosítható legyen.

## <a name="notification-retries"></a>Értesítési újrapróbálkozások

A felügyelt alkalmazás értesítési szolgáltatása a `200 OK` webhook-végponttól érkező választ vár az értesítésre. Az értesítési szolgáltatás újra próbálkozik, ha a webhook-végpont 500-as vagy annál nagyobb HTTP-hibakódot ad vissza, ha 429-es hibakódot ad vissza, vagy ha a végpont átmenetileg nem érhető el. Ha a webhook végpontja 10 órán belül nem érhető el, az értesítési üzenet el lesz dobva, és az újrapróbálkozások le fognak állni.
