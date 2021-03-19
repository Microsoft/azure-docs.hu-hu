---
title: Egy adott ajánlati API beolvasása – Azure Marketplace
description: API a megadott ajánlat lekéréséhez a közzétevő névterén belül.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 9f3ba6b2f13b9f2bb1d538db84723e3a9baaef12
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87271841"
---
# <a name="retrieve-a-specific-offer"></a>Adott ajánlat beolvasása

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a-vel, és továbbra is működnek a partner Centerben. Az áttérés kis változásokat mutat be. Tekintse át a [Cloud Partner Portal API-hivatkozásban](./cloud-partner-portal-api-overview.md) felsorolt módosításokat, hogy a kód továbbra is működőképes legyen a partneri központba való áttérés után. A CPP API-kat csak olyan meglévő termékekhez szabad használni, amelyek már integrálva lettek a partneri központba való áttérés előtt. az új termékeknek a partner Center beküldési API-kat kell használniuk.

Lekéri a megadott ajánlatot a közzétevő névterén belül.  

Lekérheti az ajánlat egy adott verzióját is, vagy lekérheti az ajánlatot Piszkozat, megtekintés vagy éles tárolóhelyek használatával. Ha nincs megadva tárolóhely, az alapértelmezett érték `draft` . A nem előzetesen megtekintett vagy közzétett ajánlat beolvasására tett kísérlet `404 Not Found` hibát eredményez.

> [!WARNING]
> Ez az API nem kéri le a titkos típusok mezőinek titkos értékeit.

``` http
    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/versions/<version>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/slot/<slotId>?api-version=2017-10-31

```

## <a name="uri-parameters"></a>URI-paraméterek

| **Név**    | **Leírás**                                                                          | **Adattípus** |
|-------------|------------------------------------------------------------------------------------------|---------------|
| publisherId | publisherId. Például: contoso                                                        | Sztring        |
| offerId     | Az ajánlatot egyedileg azonosító GUID.                                                 | Sztring        |
| version     | A beolvasott ajánlat verziója. Alapértelmezés szerint a rendszer beolvassa a legújabb ajánlat verzióját. | Egész szám       |
| slotId      | Az a tárolóhely, amelyből az ajánlatot le kell kérni, az alábbiak egyike lehet:      <br/>  - `Draft` (alapértelmezés) lekérdezi a jelenleg vázlatos ajánlat verzióját.  <br/>  -  `Preview` a jelenleg előzetes verzióban elérhető ajánlat verziószámának beolvasása.     <br/>  -  `Production` az aktuálisan éles üzemben lévő ajánlat verziószámának beolvasása.          |      Enum |
| api-verzió | Az API legújabb verziója                                                                    | Date          |
|  |  |  |

## <a name="header"></a>Fejléc

|  **Név**          |   **Érték**            |
|  ---------------   |  --------------        |
|  Content-Type      | `application/json`     |
|  Engedélyezés     | `Bearer YOUR_TOKEN`    |
|  |  |

## <a name="body-example"></a>Példa szövegtörzsre

### <a name="response"></a>Reagálás

``` json
{
    "offerTypeId": "microsoft-azure-virtualmachines",
    "publisherId": "contoso",
    "status": "failed",
    "id": "059afc24-07de-4126-b004-4e42a51816fe",
    "version": 5,
    "definition": {
        "displayText": "Contoso Virtual Machine Offer",
        "offer": {
            "microsoft-azure-marketplace-testdrive.enabled": false,
            "microsoft-azure-marketplace-testdrive.videos": [],
            "microsoft-azure-marketplace.title": "Contoso App",
            "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
            "microsoft-azure-marketplace.allowedSubscriptions": [
                "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
            ],
            "microsoft-azure-marketplace.usefulLinks": [
            {
                "linkTitle": "Contoso App for Azure",
                "linkUrl": "https://azuremarketplace.microsoft.com"
            }
            ],
                "microsoft-azure-marketplace.categoryMap": [
                {
                "categoryL1": "analytics",
                "categoryL2-analytics": [
                "visualization-and-reporting"
                ]
                },
                {
                "categoryL1": "ai-plus-machine-learning",
                "categoryL2-ai-plus-machine-learning": [
                "bot-services",
                "cognitive-services",
                "other"
                ]
                }
            ],
            "microsoft-azure-marketplace.smallLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/6218c455-9cbc-450c-9920-f2e7a69ee132.png?sv=2014-02-14&sr=b&sig=6O8MM9dgiJ48VK0MwddkyVbprRAnBszyhVkVHGShhkI%3D&se=2019-03-28T19%3A46%3A50Z&sp=r",
            "microsoft-azure-marketplace.mediumLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/557e714b-2f31-4e12-b0cc-e48dd840edf4.png?sv=2014-02-14&sr=b&sig=NwL67NTQf9Gc9VScmZehtbHXpYmxhwZc2foy3o4xavs%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.largeLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/142485da-784c-44cb-9523-d4f396446258.png?sv=2014-02-14&sr=b&sig=xaMxhwx%2FlKYfz33mJGIg8UBdVpsOwVvqhjTJ883o0iY%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.wideLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/48af9013-1df7-4c94-8da8-4626e5039ce0.png?sv=2014-02-14&sr=b&sig=%2BnN7f2tprkrqb45ID6JlT01zXcy1PMTkWXtLKD6nfoE%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.screenshots": [],
            "microsoft-azure-marketplace.videos": [],
            "microsoft-azure-marketplace.leadDestination": "None",
            "microsoft-azure-marketplace.tableLeadConfiguration": {},
            "microsoft-azure-marketplace.blobLeadConfiguration": {},
            "microsoft-azure-marketplace.salesForceLeadConfiguration": {},
            "microsoft-azure-marketplace.crmLeadConfiguration": {},
            "microsoft-azure-marketplace.httpsEndpointLeadConfiguration": {},
            "microsoft-azure-marketplace.marketoLeadConfiguration": {},
            "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
            "microsoft-azure-marketplace.termsOfUse": "Terms of use",
            "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
            "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
            "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
            "microsoft-azure-marketplace.supportContactName": "Jon Doe",
            "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
            "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
            "microsoft-azure-marketplace.publicAzureSupportUrl": "",
            "microsoft-azure-marketplace.fairfaxSupportUrl": ""
            },
            "plans": [
            {
                "planId": "contososkuidentifier",
                "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
                "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                "microsoft-azure-virtualmachines.cloudAvailability": [
                    "PublicAzure"
                ],
                "microsoft-azure-virtualmachines.certificationsFairfax": [],
                "virtualMachinePricing": {
                    "isByol": true,
                    "freeTrialDurationInMonths": 0
                },
                "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                "microsoft-azure-virtualmachines.windowsOSType": "Other",
                "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                "microsoft-azure-virtualmachines.recommendedVMSizes": [
                    "a0-basic",
                    "a0-standard",
                    "a1-basic",
                    "a1-standard",
                    "a2-basic",
                    "a2-standard"
                ],
                "microsoft-azure-virtualmachines.openPorts": [],
                "microsoft-azure-virtualmachines.vmImages": {
                    "1.0.1": {
                    "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                    "lunVhdDetails": []
                    }
                },
                "regions": [
                    "DZ",
                    "AR"
                ]
            }
            ]
        },
        "changedTime": "2017-06-07T06:15:39.7349221Z"
    }
}
```

### <a name="response-body-properties"></a>Válasz törzsének tulajdonságai

|  **Név**       |   **Leírás**                                                                                                               |
|  -------------  |   -----------------------------------------------------------------------------------------------------                         |
|  offerTypeId    | Az ajánlat típusát azonosítja.                                                                                                    |
|  publisherId    | A közzétevő egyedi azonosítója                                                                                              |
|  status         | Az ajánlat állapota. A lehetséges értékek listájáért lásd az [ajánlat állapota](#offer-status) alább.                                  |
|  Id             | Az ajánlatot egyedileg azonosító GUID                                                                                         |
|  version        | Az ajánlat aktuális verziója. Az ügyfél nem tudja módosítani a Version tulajdonságot. Minden közzététel után növekszik.    |
|  definíció     | A munkaterhelés tényleges definíciója                                                                                               |
|  changedTime    | Az ajánlat utolsó módosításának UTC szerinti dátuma                                                                                   |
|  |  |

### <a name="response-status-codes"></a>Válasz-állapotkódok

| **Kód**  | **Leírás**                                                                                                                 |
|  ------   | ------------------------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` – A kérés feldolgozása sikeres volt, és a közzétevő alatt lévő összes ajánlat vissza lett küldve az ügyfélnek.               |
|  400      | `Bad/Malformed request` – A hiba-válasz törzse több információt is tartalmazhat.                                                 |
|  403      | `Forbidden` – Az ügyfél nem fér hozzá a megadott névtérhez.                                                        |
|  404      | `Not found` – A megadott entitás nem létezik. Az ügyfélnek ellenőriznie kell a publisherId, a offerId és a verziót (ha meg van adva).      |
|  |  |

### <a name="offer-status"></a>Ajánlat állapota

|  **Név**                   |   **Leírás**                             |
| --------------------------- |  -------------------------------------------- |
|  NeverPublished             | Az ajánlatot még soha nem tették közzé.               |
|  NotStarted                 | Az ajánlat új, de nem indult el.              |
|  WaitingForPublisherReview  | Az ajánlat a közzétevő jóváhagyására vár.      |
|  Futó                    | Az ajánlatok beküldése folyamatban van.          |
|  Sikeres                  | Az ajánlat küldése befejeződött a feldolgozás során.    |
|  Megszakítva                   | Az ajánlat beküldését megszakították.                |
|  Sikertelen                     | Az ajánlat küldése nem sikerült.                      |
|  |  |
