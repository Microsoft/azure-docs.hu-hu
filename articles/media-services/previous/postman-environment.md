---
title: A Poster-környezet importálása Azure Media Services REST-hívásokhoz
description: Ez a témakör a Poster-környezet definícióját ismerteti Azure Media Services REST-hívásokhoz.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 5b1705fb2b3b1b69f79158747827d764f1bef4f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103007841"
---
# <a name="import-the-postman-environment"></a>A Poster-környezet importálása

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

Ez a cikk **a Poster-környezet azon** változóinak definícióját tartalmazza, amelyek a Media Services REST API-kat meghívó CSOPORTOSÍTott http-kérelmeket tartalmazó [Poster-gyűjteményt](postman-collection.md) használják. A környezet és a gyűjtemény fájljait a [Poster beállítása a Media Services REST API-hívások](media-rest-apis-with-postman.md) oktatóanyagához használja.

> [!NOTE]
> A értéke `AzureADSTSEndpoint `  =  `https://login.microsoftonline.com/{{TenantId}}/oauth2/token` . A bérlői azonosító lekéréséhez vigye az egérmutatót a saját felhasználónevére a portálon (a jobb felső sarokban), és a "címtár: Microsoft ({{TENANTID}})" könyvtárba kerül.

```
{
  "id": "2dbce3ce-74c2-2ceb-0461-c4c2323f5b09",
  "name": "AzureMedia",
  "values": [
    {
      "enabled": true,
      "key": "TenantID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AzureADSTSEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "RESTAPIEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientSecret",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AccessToken",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAssetId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAccessPolicyId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "UploadURL",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "MediaFileName",
      "value": "BigBuckBunny.mp4",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastChannelId",
      "value": "",
      "type": "text"
    }
  ],
  "_postman_variable_scope": "environment",
  "_postman_exported_using": "Postman/5.5.0"
}
```
