---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 09/27/2018
ms.author: diberry
ms.openlocfilehash: 445434b3315d9316721656e15bc8886d82f6f1ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60530963"
---
Adja hozzá a következő kódblokkot a Program osztályon belül:

```csharp
public struct Response
{
    public HttpResponseHeaders headers;
    public string response;

    public Response(HttpResponseHeaders headers, string response)
    {
        this.headers = headers;
        this.response = response;
    }
}

static string PrettyPrint(string s)
{
    return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
}
```
