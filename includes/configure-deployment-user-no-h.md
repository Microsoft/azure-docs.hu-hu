---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/14/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 8f1fe6ae38c708c5205f8c1230da05457d6b7010
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764303"
---
Az FTP és a helyi Git üzembe helyezhető egy Azure-webalkalmazásban egy üzembe *helyező felhasználó használatával.* Miután konfigurálta az üzembe helyezési felhasználót, azt az összes Azure-beli üzemelő példányhoz használhatja. A fiókszintű üzembe helyezési felhasználónév és jelszó eltér az Azure-előfizetés hitelesítő adataitól. 

Az üzembe helyezési felhasználó konfiguráláshoz futtassa [az az webapp deployment user set](/cli/azure/webapp/deployment/user#az_webapp_deployment_user_set) parancsot a Azure Cloud Shell. Cserélje le \<username> \<password> a és a helyére az üzembe helyezési felhasználó felhasználónevét és jelszavát. 

- A felhasználónévnek egyedinek kell lennie az Azure-ban, és a helyi Git-leküldések számára nem tartalmazhatja a " \@ szimbólumot. 
- A jelszónak legalább nyolc karakter hosszúságúnak kell lennie, és a következő három elemből kettőnek kell lennie: betűk, számok és szimbólumok. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

A JSON-kimenetben a jelszó a `null` következő: . `'Conflict'. Details: 409` hibaüzenet esetén változtassa meg a felhasználónevet. `'Bad Request'. Details: 400` hibaüzenet esetén használjon erősebb jelszót. 

Jegyezheti fel a webalkalmazások üzembe helyezéséhez használt felhasználónevét és jelszavát.
