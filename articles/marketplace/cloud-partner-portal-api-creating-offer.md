---
title: Ajánlat létrehozása vagy módosítása – Azure Marketplace
description: API új meglévő ajánlat létrehozásához vagy frissítéséhez.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/29/2020
ms.openlocfilehash: dba47073a6676b17c091af048f583ca3e7712999
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87420226"
---
# <a name="create-or-modify-an-offer"></a>Ajánlat létrehozása vagy módosítása

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a-vel, és továbbra is működnek a partner Centerben. Az áttérés kis változásokat mutat be. Tekintse át a [Cloud Partner Portal API-hivatkozásban](./cloud-partner-portal-api-overview.md) felsorolt módosításokat, hogy a kód továbbra is működőképes legyen a partneri központba való áttérés után. A CPP API-kat csak olyan meglévő termékekhez szabad használni, amelyek már integrálva lettek a partneri központba való áttérés előtt. az új termékeknek a partner Center beküldési API-kat kell használniuk.

Ez a hívás egy adott ajánlatot frissít a közzétevő névterén belül, vagy új ajánlatot hoz létre.

  `PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI-paraméterek

|  **Név**         |  **Leírás**                      |  **Adattípus**  |
|  --------         |  ----------------                     |  -------------  |
| publisherId       |  Közzétevő azonosítója, például: `contoso` |   Sztring |
| offerId           |  Ajánlat azonosítója                     |   Sztring        |
| api-verzió       |  Az API legújabb verziója            |   Date           |
|  |  |  |

## <a name="header"></a>Fejléc

|  **Név**        |  **Érték**               |
|  ---------       |  ----------              | 
| Content-Type     | `application/json`       |
| Engedélyezés    | `Bearer YOUR_TOKEN`      |
|  |  |

## <a name="body-example"></a>Példa szövegtörzsre

A következő példa egy ajánlatot hoz létre a offerID `contosovirtualmachine` .

### <a name="request"></a>Kérés

``` json
  {
      "publisherId": "contoso",
      "offerTypeId": "microsoft-azure-virtualmachines",
      "id": "contosovirtualmachine",
      "offerTypeVersions": {
          "microsoft-azure-virtualmachines": 87,
          "microsoft-azure-marketplace": 39
      },
      "definition": {
          "displayText": "Contoso Virtual Machine Offer",
          "offer": {
          "microsoft-azure-marketplace.title": "Contoso App",
          "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
          "microsoft-azure-marketplace.allowedSubscriptions": ["59160c40-2e25-4dcf-a2fd-6514cb08bf08"],
          "microsoft-azure-marketplace.usefulLinks": [{
              "linkTitle": "Contoso App for Azure",
              "linkUrl": "https://azuremarketplace.microsoft.com"
          }],
          "microsoft-azure-marketplace.categoryMap": [
                {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                        "visualization-and-reporting"
                    ]
                },
                {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning":[
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
          "microsoft-azure-marketplace.heroLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/c46ec74d-d214-4fb5-9082-3cee55200eba.png?sv=2014-02-14&sr=b&sig=RfDvjowFGpP4WZGAHylbF2CuXwO2NXOrwycrXEJvJI4%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.screenshots": [],
          "microsoft-azure-marketplace.videos": [],
          "microsoft-azure-marketplace.leadDestination": "None",
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
      "plans": 
      [
          {
              "planId": "contososkuidentifier",
              "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
              "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
              "microsoft-azure-virtualmachines.cloudAvailability": ["PublicAzure"],
              "virtualMachinePricing": {
                  "isByol": true,
                  "freeTrialDurationInMonths": 0
              },
              "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
              "microsoft-azure-virtualmachines.windowsOSType": "Other",
              "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
              "microsoft-azure-virtualmachines.recommendedVMSizes": ["a0-basic", "a0-standard", "a1-basic", "a1-standard", "a2-basic", "a2-standard"],
              "microsoft-azure-virtualmachines.openPorts": [],
              "microsoft-azure-virtualmachines.vmImages": 
              {
                  "1.0.1": 
                  {
                      "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                      "lunVhdDetails": []
                  }
              },
              "regions": ["AZ"]
          }
          ]
      },
      "eTag": "W/\"datetime'2017-06-07T06%3A15%3A40.4771399Z'\"",
      "version": 5
  }
```

### <a name="response"></a>Reagálás

``` json
 {
         "offerTypeId": "microsoft-azure-virtualmachines",
         "publisherId": "contoso",
         "status": "neverPublished",
         "id": "contosovirtualmachine",
         "version": 1,
         "definition": {
         "displayText": "Contoso Virtual Machine Offer",
         "offer": 
         {
             "microsoft-azure-marketplace-testdrive.videos": [],
             "microsoft-azure-marketplace.title": "Contoso App",
             "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
             "microsoft-azure-marketplace.allowedSubscriptions": 
             [
                 "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
             ],
             "microsoft-azure-marketplace.usefulLinks": 
             [
                 {
                     "linkTitle": "Contoso App for Azure",
                     "linkUrl": "https://azuremarketplace.microsoft.com"
                 }
             ],
             "microsoft-azure-marketplace.categoryMap":
             [
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
             "microsoft-azure-marketplace.smallLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/8affcd28-60a5-4839-adf8-c560e069fd61.png?sv=2014-02-14&sr=b&sig=nGErAgn%2BDUecrX892wcmk32kh0MHgIZeJ5jcKyY%2Fuew%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.mediumLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/39550bca-1110-432c-9ea9-e12b3a2288cd.png?sv=2014-02-14&sr=b&sig=4X0hlkXYtuZOmcYq%2BsbYVZz3k5k26kngcFX6yBAJjNI%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.largeLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/ce3576e3-df12-4074-b0a3-0b8d3f329df1.png?sv=2014-02-14&sr=b&sig=mFhtCUQh%2FbFz10nlIWbqsz6jq5MBZ0M%2F5cIREE9P6V0%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.wideLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/476d6edd-12d3-4414-9def-d2970c4a9de4.png?sv=2014-02-14&sr=b&sig=pg4MDSZjAb8w8D%2FrQ9RT%2BodpynSy%2FlYOvpx0yeam2Bw%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.heroLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/46c85b7b-4438-4e0d-8218-24fb5651727a.png?sv=2014-02-14&sr=b&sig=wIsCOO5%2BDj8NsLVSwwzwTgogF71oA7Q1XjKhNB1ni5g%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
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
         "plans": 
         [
             {
                 "planId": "contososkuidentifier",
                 "microsoft-azure-virtualmachines.skuTitle": "Contoso App (Old Title)",
                 "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                 "microsoft-azure-virtualmachines.cloudAvailability": 
                 [
                     "PublicAzure"
                 ],
                 "microsoft-azure-virtualmachines.certificationsFairfax": [],
                 "virtualMachinePricing": {
                     "isByol": true,
                     "freeTrialDurationInMonths": 0
                 },
                 "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                 "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                 "microsoft-azure-virtualmachines.recommendedVMSizes": 
                 [
                     "a0-basic",
                     "a0-standard",
                     "a1-basic",
                     "a1-standard",
                     "a2-basic",
                     "a2-standard"
                 ],
                 "microsoft-azure-virtualmachines.openPorts": [],
                 "microsoft-azure-virtualmachines.vmImages": 
                 {
                     "1.0.1": 
                     {
                         "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                         "lunVhdDetails": []
                     }
                 },
                 "regions":
                 [
                     "AZ"
                 ]
             } 
         ]
     },
     "changedTime": "2018-03-28T22:27:13.8363879Z"
 }
```

> [!NOTE]
> Az ajánlat módosításához adjon hozzá egy **IF-Match** fejlécet * értékre a fenti kérelemhez. Használja ugyanazt a kérési törzset, mint a fentieket, de szükség szerint módosítsa az értékeket. 

### <a name="response-status-codes"></a>Válasz-állapotkódok

| **Kód**  |  **Leírás**                                                                            |
| --------  |  ---------------                                                                            |
|  200      | `OK`. A kérés feldolgozása sikeres volt, és az ajánlat sikeresen módosítva lett.           |
|  201      | `Created`. A kérés feldolgozása sikeres volt, és az ajánlat sikeresen létrejött.   |
|  400      | `Bad/Malformed request`. A hiba-válasz törzse további információkat biztosíthat.            |
|  403      | `Forbidden`. Az ügyfél nem fér hozzá a kért névtérhez.                     |
|  404      | `Not found`. Az ügyfél által hivatkozott entitás nem létezik.                           |
|  412      | A kiszolgáló nem felel meg a kérelmező által a kérelemben megadott előfeltételek egyikének. Az ügyfélnek ellenőriznie kell a kéréssel ellátott ETAG. |
|  |  |

## <a name="uploading-artifacts"></a>Összetevők feltöltése

Az összetevők, például a képek és az emblémák megosztását úgy kell megosztani, hogy feltölti őket egy elérhető helyre a weben, majd a PUT kérelemben szereplő URI-t is beleértve, a fenti példában látható módon. A rendszer felismeri, hogy ezek a fájlok nem jelennek meg az Azure piactér-tárolóban, és nem tölthetik le ezeket a fájlokat a tárolóba.  Ennek eredményeképpen azt tapasztalhatja, hogy a jövőbeli GET-kérések az Azure piactér szolgáltatás URL-címét fogják visszaadni ezekhez a fájlokhoz.

## <a name="categories-and-industries"></a>Kategóriák és iparágak

Új ajánlat létrehozásakor meg kell adnia egy kategóriát az ajánlathoz a piactéren. Bizonyos ajánlati típusok esetében lehetőség van az iparágak megadására is. Az ajánlat típusa alapján adja meg az ajánlathoz tartozó kategóriákat/iparágakat a következő táblázatokból származó adott kulcs értékekkel.

### <a name="azure-marketplace-categories"></a>Azure Marketplace-kategóriák

Ezek a kategóriák és a hozzájuk tartozó kulcsok az Azure-alkalmazások, a Virtual Machines, a Core Virtual Machines, a tárolók, a Container apps, a IoT Edge modulok és az SaaS-ajánlatok típusaira alkalmazhatók. A félkövér dőlt (például ***analitikai***) elemek a kategóriák és a szabványos szöveges elemek (például az adatelemzések) a következő alkategóriákba tartoznak. A pontos kulcs értékeket a térköz vagy a nagybetűk módosítása nélkül használhatja.

| Kategória | SaaS-kulcsok | Azure-alkalmazások kulcsai | Virtuális gépek, tárolók, Container apps, IoT Edge modul, alapszintű virtuálisgép-kulcsok |
| --- | --- | --- | --- |
| ***Analitika** _ | _*_Analytics_*_ | _*_elemzés – Azure-alkalmazások_*_ | _ *_Analitika – amp_** |
| Adatelemzések | adatelemzések | adatelemzések | adatelemzések |
| Adatelemzés | adatelemzés | adatelemzés | adatelemzés |
| Big Data | Big-az adatmennyiség | bigData | Big-az adatmennyiség |
| Prediktív elemzés | prediktív elemzés | prediktív elemzés | prediktív elemzés |
| Valós idejű/adatfolyam-elemzés | valós idejű adatfolyam-elemzés | valós idejű adatfolyam-elemzés | valós idejű adatfolyam-elemzés |
| Egyéb | egyéb | egyéb – analitikai | egyéb |
| ***AI + Machine learning** _ | _*_ArtificialIntelligence_*_ | _*_AI-Plus-Machine-learning_*_ | _ *_AI-Plus-Machine-learning_** |
| Bot Services | robot – szolgáltatások | robot – szolgáltatások | robot – szolgáltatások |
| Cognitive Services | kognitív szolgáltatások | kognitív szolgáltatások | kognitív szolgáltatások |
| ML szolgáltatás | ml – szolgáltatás | ml – szolgáltatás | ml – szolgáltatás |
| Automatizált ML | automatizált – ml | automatizált – ml | automatizált – ml |
| Üzleti/Robotos folyamatok automatizálása | üzleti – robot – folyamat – automatizálás | üzleti – robot – folyamat – automatizálás | üzleti – robot – folyamat – automatizálás |
| Adatfelirat | adatfelirat | adatfelirat | adatfelirat |
| Adatelőkészítés | adatfeldolgozás | adatfeldolgozás | adatfeldolgozás |
| Tudás bányászata | tudás – bányászat | tudás – bányászat | tudás – bányászat |
| ML-műveletek | ml – műveletek | ml – műveletek | ml – műveletek |
| Egyéb | egyéb-AI-Plus-Machine-learning | egyéb | egyéb |
| ***Blockchain** _ | _*_blockchain_*_ | _*_blockchain_*_ | _ *_blockchain_** |
| Alkalmazás-gyorsítók | alkalmazások – gyorssegédek | alkalmazások – gyorssegédek | alkalmazások – gyorssegédek |
| Egycsomópontos Főkönyv | egycsomópontos Főkönyv | egycsomópontos Főkönyv | egycsomópontos Főkönyv |
| Több csomópontos Főkönyv | több csomópontos Főkönyv | több csomópontos Főkönyv | több csomópontos Főkönyv |
| Eszközök | tools | tools | tools |
| Egyéb | egyéb | egyéb | egyéb |
| ***Számítás** _ | _*_számítás – Saas_*_ | _*_számítás – Azure-alkalmazások_*_ | _ *_számítás_** |
| Alkalmazás-infrastruktúra | appInfra | appInfrastructure | alkalmazás – infrastruktúra |
| Operációs rendszerek | clientOS | clientOS | operációs rendszerek |
| Gyorsítótár | cache | cache | cache |
| Egyéb | egyéb – számítás | egyéb – számítás | egyéb |
| ***Tárolók** _ | _*_konténerek_*_ | _*_konténerek_*_ | _ *_tárolók_** |
| Tároló alkalmazások | tároló – alkalmazások | tároló – alkalmazások | tároló – alkalmazások |
| Tároló lemezképei | tároló – lemezképek | tároló – lemezképek | tároló – lemezképek |
| Ismerkedés a tárolókkal | első lépések – tárolók | első lépések – tárolók | első lépések – tárolók |
| Egyéb | egyéb | egyéb | egyéb |
| ***Adatbázisok** _ | _*_adatbázisok – Saas_*_ | _*_adatbázis_*_ | _ *_adatbázisok_** |
| NoSQL-adatbázisok | NoSQL – adatbázisok | NoSQL – adatbázisok | NoSQL – adatbázisok |
| Relációs adatbázisok | kapcsolatok – adatbázisok | kapcsolatok – adatbázisok | kapcsolatok – adatbázisok |
| Főkönyv/Blockchain-adatbázisok | Főkönyv – blockchain – adatbázisok | Főkönyv – blockchain – adatbázisok | Főkönyv – blockchain – adatbázisok |
| Adattavak | adattavak | adattavak | adattavak |
| -adatraktár | adatraktár | adatraktár | adatraktár |
| Egyéb | egyéb adatbázisok | egyéb adatbázisok | egyéb |
| ***Fejlesztői eszközök** _ | _*_fejlesztői-eszközök-Saas_*_ | _*_fejlesztői – eszközök – Azure-alkalmazások_*_ | _ *_Developer – eszközök_** |
| Eszközök | eszközök – fejlesztői-eszközök | eszközök – fejlesztői-eszközök | eszközök – fejlesztői-eszközök |
| Parancsfájlok | parancsfájlok | parancsfájlok | parancsfájlok |
| Fejlesztői szolgáltatás | devService | devService | fejlesztő – szolgáltatás |
| Egyéb | egyéb – fejlesztői – eszközök | egyéb – fejlesztői – eszközök | egyéb |
| ***DevOps** _ | _*_devops_*_ | _*_devops_*_ | _ *_devops_** |
| Egyéb | egyéb | egyéb | egyéb |
| ***Identitás** _ | _*_identitás_*_ | _*_identitás_*_ | _ *_identitás_** |
| Hozzáférés-kezelés | hozzáférés-kezelés | hozzáférés-kezelés | hozzáférés-kezelés |
| Egyéb | egyéb | egyéb | egyéb |
| ***Integráció** _ | _*_integrációs_*_ | _*_integrációs_*_ | _ *_integráció_** |
| Üzenetkezelés | üzenetek | üzenetek | üzenetek |
| Egyéb | egyéb | egyéb | egyéb |
| ***Eszközök internetes hálózata** _ | _*_IoT_*_ | _*_Internet-of-Things – Azure-apps_*_ | _ –*_egyebek_** |
| IoT Core-szolgáltatások | N/A | IOT-Core-Services | IOT-Core-Services |
| IoT Edge modulok | N/A | IOT – Edge – modulok | IOT – Edge – modulok |
| IoT-megoldások | IOT – megoldások | IOT – megoldások | IOT – megoldások |
| Adatelemzési & vizualizáció | az adatelemzés és a vizualizáció | az adatelemzés és a vizualizáció | az adatelemzés és a vizualizáció |
| IoT-kapcsolat | IOT – kapcsolat | IOT – kapcsolat | IOT – kapcsolat |
| Egyéb | egyéb-Internet-of-Things | egyéb-Internet-of-Things | egyéb |
| ***IT & felügyeleti eszközök** _ | _*_ITandAdministration_*_ | _*_IT-és-felügyeleti eszközök – Azure-alkalmazások_*_ | _ *_IT-és-felügyeleti eszközök_** |
| Felügyeleti megoldások | felügyelet – megoldások | felügyelet – megoldások | felügyelet – megoldások |
| Üzleti alkalmazások | businessApplication | businessApplication | üzleti alkalmazások |
| Egyéb | egyéb it-felügyeleti eszközök | egyéb it-felügyeleti eszközök | egyéb |
| ***Monitorozás & diagnosztika** _ | _*_monitorozás és diagnosztika_*_ | _*_monitorozás és diagnosztika_*_ | _ *_monitorozás és diagnosztika_** |
| Egyéb | egyéb | egyéb | egyéb |
| ***Média** _ | _*_Media_*_ | _*_Media_*_ | _ *_média_** |
| Media Services | media-services | media-services | media-services |
| Content Protection | tartalomvédelem | tartalomvédelem | tartalomvédelem |
| Igény szerinti streaming élő & | élő és igény szerinti közvetítés | élő és igény szerinti közvetítés | élő és igény szerinti közvetítés |
| Egyéb | egyéb | egyéb | egyéb |
| ***Migrálás** _ | _*_áttelepítési_*_ | _*_áttelepítési_*_ | _ *_Migrálás_** |
| Adatok áttelepítése | adatáttelepítés | adatáttelepítés | adatáttelepítés |
| Egyéb | egyéb | egyéb | egyéb |
| ***Vegyes valóság** _ | _*_vegyes – valóság_*_ | _*_vegyes – valóság_*_ | _ *_vegyes – valóság_** |
| Egyéb | egyéb | egyéb | egyéb |
| ***Hálózatkezelés** _ | _*_hálózati_*_ | _*_hálózati_*_ | _ *_hálózatkezelés_** |
| Berendezések kezelői | berendezés – vezetők | berendezés – vezetők | berendezés – vezetők |
| Kapcsolatok | kapcsolatok | kapcsolatok | kapcsolatok |
| Tűzfalak | tűzfalak | tűzfalak | tűzfalak |
| Terheléselosztók | terheléselosztó | terheléselosztó | terheléselosztó |
| Egyéb | egyéb | egyéb | egyéb |
| ***Biztonság** _ | _*_biztonsági_*_ | _*_biztonsági_*_ | –*_Biztonság_** |
| Identitás & hozzáférés-kezelés | identitás-és hozzáférés-kezelés | identitás-és hozzáférés-kezelés | identitás-és hozzáférés-kezelés |
| Veszélyforrások elleni védelem | veszélyforrások elleni védelem | veszélyforrások elleni védelem | veszélyforrások elleni védelem |
| Information Protection | információ – védelem | információ – védelem | információ – védelem |
| Egyéb | egyéb | egyéb | egyéb |
| ***Storage** _ | _*_Storage – Saas_*_ | _*_tárolás – Azure-apps_*_ | _ *_tároló_** |
| Biztonsági mentés & helyreállítás | biztonsági mentés | biztonsági mentés | biztonsági mentés és helyreállítás |
| Vállalati hibrid tárolás | nagyvállalati hibrid – tárterület | nagyvállalati hibrid – tárterület | nagyvállalati hibrid – tárterület |
| Fájlmegosztás | fájlmegosztás | fájlmegosztás | fájlmegosztás |
| Az adatéletciklus-kezelés | adatéletciklus – felügyelet | adatéletciklus – felügyelet | adatéletciklus – felügyelet |
| Egyéb | egyéb – tárterület | egyéb – tárterület | egyéb |
| ***Web** _ | _*_Web_*_ | _*_Web_*_ | _ *_web_** |
| Blogok & CMSs | Blogok és CMSS | Blogok és CMSS | Blogok és CMSS |
| Kezdő Web Apps | Starter – Web-Apps | Starter – Web-Apps | Starter – Web-Apps |
| ECommerce | eCommerce | eCommerce | eCommerce |
| Web Apps keretrendszerek | webes alkalmazások – keretrendszerek | webes alkalmazások – keretrendszerek | webes alkalmazások – keretrendszerek |
| Web Apps | webalkalmazások | webalkalmazások | webalkalmazások |
| Egyéb | egyéb | egyéb | egyéb |
||||

### <a name="microsoft-appsource-categories"></a>Microsoft AppSource kategóriák

Ezek a kategóriák és a hozzájuk tartozó kulcsok a SaaS, a PowerBI alkalmazás, a Dynamics 365 Business Central, a Dynamics 365 for Customer engagement és a Dynamics 365 for Operations-típusok esetében alkalmazhatók. A félkövér dőlt (például az ***analitikai***) elemek a kategóriák és a szabványos szöveges elemek (például a speciális elemzések) alá tartoznak. A pontos kulcs értékeket a térköz vagy a nagybetűk módosítása nélkül használhatja.

| Kategória | SaaS-kulcsok | Dynamics 365 Business Central, Dynamics 365 for Customer engagement, Dynamics 365 műveleti kulcsokhoz | PowerBI |
| --- | --- | --- | --- |
| ***Analitika** _ | _*_Analytics_*_ | _*_Elemzés_*_ | _ *_Analitika_** |
| Bővített analitika | Advanced-Analytics | Advanced-Analytics | Advanced-Analytics |
| Vizualizáció & jelentéskészítés | vizualizáció – jelentéskészítés | vizualizáció – jelentéskészítés | vizualizáció – jelentéskészítés |
| Egyéb | egyéb | egyéb – analitikai | egyéb – analitikai |
| ***AI + Machine learning** _ | _*_ArtificialIntelligence_*_ | _*_AI-Plus-Machine-learning-Dynamics_*_ | _ *_AI-Plus-Machine-learning-appsource_** |
| AI vállalatoknak | AI-for-Business | AI-for-Business | AI-for-Business |
| Bot-alkalmazások | robot – alkalmazások | robot – alkalmazások | robot – alkalmazások |
| Egyéb | egyéb-AI-Plus-Machine-learning | egyéb-AI-Plus-Machine-learning | egyéb-AI-Plus-Machine-learning |
| ***Együttműködés** _ | _*_Együttműködés_*_ | _*_Együttműködés_*_ | _ *_együttműködés_** |
| Kapcsolatfelvétel & személyekkel | Kapcsolatfelvétel – személyek | Kapcsolatfelvétel – személyek | Kapcsolatfelvétel és személyek |
| Értekezletek kezelése | értekezletek kezelése | értekezletek kezelése | értekezletek kezelése |
| Hely kialakításának & kezelése | hely – kialakítás – felügyelet | hely – kialakítás – felügyelet | hely – tervezés és felügyelet |
| Feladat & projektmenedzsment | feladat – projekt – felügyelet | feladat – projekt – felügyelet | feladat-és projektmenedzsment |
| Hang & videokonferencia | hang-videó-konferencia | hang-videó-konferencia | hang-és video-konferencia |
| Egyéb | egyéb – együttműködés | egyéb – együttműködés | egyéb |
| ***Megfelelőség & jogi** _ | _*_betartásának_*_ | _*_betartásának_*_ | _ *_megfelelőség – és jogi_** |
| Adózási & naplózása | adózás – naplózás | adózás – naplózás | adó-és audit |
| Jogi tudnivalók | Jogi tudnivalók | Jogi tudnivalók | jogi |
| Adatok, irányítási & adatvédelem | adatkezelés – adatvédelem | adatkezelés – adatvédelem | adatkezelés és adatvédelem |
| Állapot & biztonság | állapot – biztonság | állapot – biztonság | állapot és biztonság |
| Egyéb | egyéb megfelelőség – jogi | egyéb megfelelőség – jogi | egyéb |
| ***Ügyfélszolgálat** _ | _*_CustomerService_*_ | _*_CustomerService_*_ | _ *_ügyfél-szolgáltatás_** |
| Kapcsolattartási központ | Contact-Center | Contact-Center | Contact-Center |
| Szembenézés a Face szolgáltatással | Face-to-Face-Service | Face-to-Face-Service | Face-to-Face-Service |
| Office & Employee Service | Back-Office – alkalmazott – szolgáltatás | Back-Office – alkalmazott – szolgáltatás | Back-Office-és-Employee-Service |
| Knowledge & – ügyvitel | tudás – eset – felügyelet | tudás – eset – felügyelet | ismeretek és esetek kezelése |
| Közösségi média & Omnichannel engagement | közösségi média – omnichannel – engagement | közösségi média – omnichannel – engagement | közösségi – média-és omnichannel – engagement |
| Egyéb | egyéb – ügyfél-szolgáltatás | egyéb – ügyfél-szolgáltatás | egyéb |
| ***Pénzügy** _ | _*_Finance_*_ | _*_Finance_*_ | *_Pénzügy_** |
| Könyvelés | könyvelés | könyvelés | könyvelés |
| Asset Management (Eszközkezelés) | eszköz – felügyelet | eszköz – felügyelet | eszköz – felügyelet |
| Elemzés, konszolidáció & jelentéskészítés | elemzés – konszolidáció – jelentéskészítés | elemzés – konszolidáció – jelentéskészítés | elemzés – konszolidáció és jelentéskészítés |
| Kredit & gyűjtemények | kreditek gyűjteményei | kreditek gyűjteményei | kreditek és gyűjtemények |
| Megfelelőség & kockázatkezelés | megfelelőség – kockázatkezelés | megfelelőség – kockázatkezelés | megfelelőség – kockázatkezelés |
| Egyéb | egyéb – Pénzügy | egyéb – Pénzügy | egyéb |
| ***Emberi erőforrások** _ | _*_Emberierőforrások_*_ | _*_Emberierőforrások_*_ | _ *_emberi erőforrások_** |
| Tehetség megszerzése | tehetség – beszerzés | tehetség – beszerzés | tehetség – beszerzés |
| Tehetségek kezelése | Talent – felügyelet | Talent – felügyelet | Talent – felügyelet |
| HR-műveletek | HR – műveletek | HR – műveletek | HR – műveletek |
| A munkaerő megtervezése & Analitika | munkaerő – tervezés – elemzés | munkaerő – tervezés – elemzés | munkaerő – tervezés és elemzés |
| Egyéb | egyéb – emberi erőforrások | egyéb – emberi erőforrások | egyéb |
| ***Eszközök internetes hálózata** _ | _*_IoT_*_ | _*_Internet-of-Things – Dynamics_*_ | _ *_– appsource_** |
| Asset Management & műveletek | eszköz – felügyelet – műveletek | eszköz – felügyelet – műveletek | Asset-Management-and-Operations |
| Csatlakoztatott termékek | csatlakoztatott termékek | csatlakoztatott termékek | csatlakoztatott termékek |
| Intelligens ellátási lánc | intelligens ellátási lánc | intelligens ellátási lánc | intelligens ellátási lánc |
| Prediktív karbantartás | prediktív karbantartás | prediktív karbantartás | prediktív karbantartás |
| Távoli monitorozás | távoli figyelés | távoli figyelés | távoli figyelés |
| Biztonsági & biztonság | biztonság – biztonság | biztonság – biztonság | biztonság és biztonság |
| Intelligens infrastruktúra-& erőforrások | intelligens infrastruktúra – erőforrások | intelligens infrastruktúra – erőforrások | intelligens infrastruktúra és erőforrások |
| Járművek & mobilitás | járművek – mobilitás | járművek – mobilitás | járművek – mobilitás |
| Egyéb | egyéb-Internet-of-Things | egyéb-Internet-of-Things | egyéb |
| ***IT & felügyeleti eszközök** _ | _*_ITandAdministration_*_ | _*_ITandAdministration_*_ | _ *_IT-és-felügyeleti eszközök_** |
| Felügyeleti megoldások | felügyelet – megoldások | felügyelet – megoldások | felügyelet – megoldások |
| Üzleti alkalmazások | businessApplication | businessApplication | üzleti alkalmazások |
| Egyéb | egyéb it-felügyeleti eszközök | egyéb it-felügyeleti eszközök | egyéb |
| ***Marketing** _ | _*_Marketing_*_ | _*_Marketing_*_ | _ *_marketing_** |
| Hirdetmény | hirdetmény | hirdetmény | hirdetmény |
| Elemzés | elemzés – marketing | elemzés – marketing | elemzés – marketing |
| Kampány-felügyeleti & Automation | kampány – felügyelet – automatizálás | kampány – felügyelet – automatizálás | kampány – felügyelet és automatizálás |
| E-mail marketing | e-mail – marketing | e-mail – marketing | e-mail – marketing |
| L2 – események & erőforrás-kezelés | események – erőforrás-kezelés | események – erőforrás-kezelés | események és erőforrások kezelése |
| Kutatási & elemzése | kutatás – elemzés | kutatás – elemzés | kutatás és elemzés |
| Közösségi média | közösségi média | közösségi média | közösségi média |
| Egyéb | egyéb – marketing | egyéb – marketing | egyéb |
| ***Operations & ellátási lánc** _ | _*_OperationsSupplyChain_*_ | _*_OperationsSupplyChain_*_ | _ *_művelet-és-ellátási lánc_** |
| Eszköz & üzemi felügyelet | eszköz – éles üzem – felügyelet | eszköz – éles üzem – felügyelet | eszköz-és-gyártás – felügyelet |
| Igény-előrejelzés | igény-előrejelzés | igény-előrejelzés | igény-előrejelzés |
| Information Management & kapcsolat | információkezelés – kapcsolat | információkezelés – kapcsolat | információ – felügyelet és kapcsolat |
| & jelentéskészítés tervezése, beszerzése | tervezés – beszerzés – jelentéskészítés | tervezés – beszerzés – jelentéskészítés | tervezés – beszerzés és jelentéskészítés |
| Minőségi & szolgáltatások kezelése | minőség – szolgáltatás-felügyelet | minőség – szolgáltatás-felügyelet | minőség-és szolgáltatás-felügyelet |
| Értékesítési & rendelés kezelése | értékesítési rendelés – felügyelet | értékesítési rendelés – felügyelet | értékesítés és rendelés – felügyelet |
| Szállítás & Warehouse Management | szállítás – raktár – felügyelet | szállítás – raktár – felügyelet | szállítás és raktározás – felügyelet |
| Egyéb | egyéb műveletek – ellátási lánc | egyéb műveletek – ellátási lánc | egyéb |
| ***Termelékenység** _ | _*_Termelékenység_*_ | _*_Termelékenység_*_ | –*_termelékenység_** |
| Tartalom létrehozása & kezelése | tartalom létrehozása – felügyelet | tartalom létrehozása – felügyelet | tartalom létrehozása és kezelése |
| Nyelvi & fordítás | nyelv – fordítás | nyelv – fordítás | nyelv és fordítás |
| Dokumentumkezelés | Dokumentumkezelés | Dokumentumkezelés | Dokumentumkezelés |
| E-mailek kezelése | e-mailek kezelése | e-mailek kezelése | e-mailek kezelése |
| Keresési & referenciája | Keresés – hivatkozás | Keresés – hivatkozás | Keresés és hivatkozás |
| Egyéb | egyéb – termelékenység | egyéb – termelékenység | egyéb |
| Játékossá tétel | Játékossá tétel | Játékossá tétel | gamification |
| ***Értékesítés** _ | _*_Sales_*_ | _*_Sales_*_ | _ *_Értékesítés_** |
| Telesales | telesales | telesales | telesales |
| Konfigurálás, Ár, árajánlat (CPQ) | az árajánlat konfigurálása | az árajánlat konfigurálása | az árajánlat konfigurálása |
| Szerződések kezelése | szerződések kezelése | szerződések kezelése | szerződések kezelése |
| CRM | crm | crm | crm |
| E-kereskedelmi | e-kereskedelmi | e-kereskedelmi | e-kereskedelmi |
| Üzleti adatelemzés | üzleti – adatelemzés | üzleti – adatelemzés | üzleti – adatelemzés |
| Értékesítés engedélyezése | értékesítés – engedélyezés | értékesítés – engedélyezés | értékesítés – engedélyezés |
| Egyéb | egyéb – értékesítés | egyéb – értékesítés | egyéb – értékesítés |
| ***Térinformatikai** _ | _*_térinformatikai_*_ | _*_térinformatikai_*_ | _ *_térinformatika_** |
| Maps | maps | maps | maps |
| Hírek & Időjárás | Hírek és időjárás | Hírek és időjárás | Hírek és időjárás |
| Egyéb | egyéb földrajzi hely | egyéb földrajzi hely | egyéb földrajzi hely |
||||

### <a name="microsoft-appsource-industries"></a>Microsoft AppSource Industries

Ezek az iparágak és a hozzájuk tartozó kulcsok az SaaS, a PowerBI app, a Dynamics 365 Business Central, a Dynamics 365 for Customer engagement és a Dynamics 365 for Operations-típusok esetében alkalmazhatók. A félkövér dőlt (például ***autóipari***) elemek a kategóriák és a szabványos szöveges elemek (például a AutomotiveL2) a következő alkategóriákba tartoznak. A pontos kulcs értékeket a térköz vagy a nagybetűk módosítása nélkül használhatja.

| Iparág | SaaS, Dynamics 365 Business Central, Dynamics 365 for Customer engagement, Dynamics 365 műveleti kulcsokhoz | PowerBI-alkalmazások kulcsai |
| --- | --- | --- |
| ***Autóipar** _ | _*_Autóipar_*_ | _ *_autóipar_** |
| Autóipar | AutomotiveL2 | AutomotiveL2 |
| ***Mezőgazdaság** _ | _*_Mezőgazdaság_*_ | _ *_mezőgazdaság_** |
| Egyéb – nem szegmentált | Mezőgazdaság \_ OtherUnsegmented | egyéb – nem szegmentált |
| ***Eloszlás** _ | _*_Disztribúció_*_ | _ *_eloszlás_** |
| Nagykereskedelem | Nagykereskedelem | nagykereskedelem |
| Parcella & csomag szállítása | ParcelAndPackageShipping | csomagok és csomagok szállítása |
| ***Education** _ | _*_Education_*_ | _ *_Education_** |
| Felsőoktatás | HigherEducation | magasabb szintű oktatás |
| Elsődleges & középfokú oktatás/K-12 | PrimaryAndSecondaryEducationK12 | elsődleges és másodlagos – oktatás |
| Könyvtárak & múzeumok | LibrariesAndMuseums | könyvtárak és múzeumok |
| ***Pénzügyi szolgáltatások** _ | _*_FinancialServices_*_ | –*_pénzügyi szolgáltatások_** |
| Banki & tőkepiacok | BankingAndCapitalMarkets | banki szolgáltatások és tőkepiacok |
| Biztosítási | Biztosítási | biztosítási |
| ***Government** _ | _*_Államigazgatás_*_ | _ *_Government_** |
| Védelmi & intelligencia | DefenseAndIntelligence | védelem és intelligencia |
| Közbiztonság & igazságszolgáltatás | PublicSafetyAndJustice | nyilvános biztonság és jogérvényesülés |
| Polgári kormányzat | CivilianGovernment | civil kormány |
| ***Egészségügyi** _ | _*_HealthCareandLifeSciences_*_ | *_egészségügyi ellátás_** |
| Állapot költségviselő | HealthPayor | állapot – költségviselő |
| Állapot szolgáltatója | HealthProvider | állapotfigyelő szolgáltató |
| Gyógyszerek | Gyógyszerek | gyógyszerek |
| ***Gyártási & erőforrások** _ | _*_Gyártás_*_ | _ *_feldolgozóipar és erőforrások_** |
| Kémiai & Agrokémiai | ChemicalAndAgrochemical | kémiai és Agrokémiai |
| Diszkrét gyártás | DiscreteManufacturing | diszkrét – gyártás |
| Energia | Energia | energy |
| ***Lakossági & fogyasztási cikkek** _ | _*_RetailandConsumerGoods_*_ | _ –*_kiskereskedelmi és fogyasztó – termékek_** |
| Fogyasztási cikkek | ConsumerGoods | fogyasztó – termékek |
| Kiskereskedők | Kiskereskedők | kiskereskedők |
| ***Media & Communications** _ | _*_MediaAndCommunications_*_ | _ *_média és kommunikáció_** |
| Media & Entertainment | MediaandEntertainment | média és szórakoztatás |
| Távközlés | Távközlés | távközlési |
| ***Professional Services** _ | _*_ProfessionalServices_*_ | _ *_Professional – szolgáltatások_** |
| Jogi tudnivalók | Jogi tudnivalók | jogi |
| Partneri szakmai szolgáltatások | PartnerProfessionalServices | partner-Professional – szolgáltatások |
| ***Architektúra & Construction** _ | _*_ArchitectureAndConstruction_*_ | _ *_architektúra és építés_** |
| Egyéb – nem szegmentált | ArchitectureAndConstruction \_ OtherUnsegmented | egyéb – nem szegmentált |
| ***Vendéglátás & Travel** _ | _*_HospitalityandTravel_*_ | –*_vendéglátás és utazás_** |
|    Hotelek & szabadidő | HotelsAndLeisure | szállodák és szabadidő |
| Utazási & szállítás | TravelAndTransportation | utazás és szállítás |
| Éttermek & Food Services | RestaurantsAndFoodServices | éttermek és élelmiszer-szolgáltatások |
| ***Más állami szektorbeli iparágak** _ | _*_OtherPublicSectorIndustries_*_ | –*_egyéb – állami szektor – iparágak_** |
| Erdészeti & horgászat | ForestryAndFishing | erdészet és halászat |
| Nonprofit szervezeteknek | Nonprofit szervezeteknek | nonprofit szervezeteknek |
| ***Real Estate** _ | _*_RealEstate_*_ | _ *_Real-Estate_** |
| Egyéb – nem szegmentált | RealEstate \_ OtherUnsegmented | egyéb – nem szegmentált |
|||
