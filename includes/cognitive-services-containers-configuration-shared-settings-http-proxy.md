---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7f51b7fe95445cbd3a8aff530f89ce55b5abfb1e
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630065"
---
Ha HTTP-proxyt kell konfigurálnia a kimenő kérelmek végrehajtásához, használja a következő két argumentumot:

| Name | Adattípus | Leírás |
|--|--|--|
|HTTP_PROXY|sztring|A használandó proxy, például: `http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|sztring|A proxyn való hitelesítéshez szükséges hitelesítő adatok, például: `username:password` . Ennek az értéknek **kisbetűvel kell lennie**. |
|`<proxy-user>`|sztring|A proxy felhasználója.|
|`<proxy-password>`|sztring|A `<proxy-user>` proxyhoz tartozó jelszó.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
