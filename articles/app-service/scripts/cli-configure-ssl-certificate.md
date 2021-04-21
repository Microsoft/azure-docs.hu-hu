---
title: 'CLI: TLS-/SSL-tanúsítvány feltöltése és kötése egy alkalmazáshoz'
description: Megtudhatja, hogyan automatizálhatja a saját alkalmazás üzembe helyezését és felügyeletét az Azure CLI App Service használatával. Ez a minta bemutatja, hogyan köt egy egyéni TLS-/SSL-tanúsítványt egy alkalmazáshoz.
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 50f5c0f00daf025791eb0982ea4c5f626ded02ad
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782494"
---
# <a name="bind-a-custom-tlsssl-certificate-to-an-app-service-app-using-cli"></a>Egyéni TLS-/SSL-tanúsítvány kötése App Service cli használatával

Ez a példaszkprogram létrehoz egy alkalmazást a App Service a kapcsolódó erőforrásokkal, majd hozzáköti egy egyéni tartománynév TLS-/SSL-tanúsítványát. A példához a következők szükségesek:

* Hozzáférés a tartományregisztrálója DNS-konfigurációs oldalához.
* Egy érvényes . PFX-fájl és a feltölteni és kötéshez használni kívánt TLS-/SSL-tanúsítvány jelszava.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | Létrehoz egy App Service-csomagot. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | Létrehoz egy App Service alkalmazást. |
| [`az webapp config hostname add`](/cli/azure/webapp/config/hostname#az_webapp_config_hostname_add) | Leképez egy egyéni tartományt egy App Service alkalmazásra. |
| [`az webapp config ssl upload`](/cli/azure/webapp/config/ssl#az_webapp_config_ssl_upload) | Feltölt egy TLS-/SSL-tanúsítványt egy App Service alkalmazásba. |
| [`az webapp config ssl bind`](/cli/azure/webapp/config/ssl#az_webapp_config_ssl_bind) | Egy feltöltött TLS-/SSL-tanúsítványt köt egy App Service alkalmazáshoz. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További App Service CLI-példaszkripteket az [Azure App Service dokumentációjában](../samples-cli.md) találhat.
