---
title: Azure CLI-példaszkript – Egyéni SSL-tanúsítvány kötése egy függvényalkalmazáshoz | Microsoft Docs
description: Azure CLI-példaszkript – Egyéni SSL-tanúsítvány kötése egy függvényalkalmazáshoz az Azure-ban
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: azure-functions
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/03/2013
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 84cc4e92419e683f8d2c29883b26148fdef23356
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60325731"
---
# <a name="bind-a-custom-ssl-certificate-to-a-function-app"></a>Egyéni SSL-tanúsítvány kötése egy függvényalkalmazáshoz

Ez a példaszkript egy függvényalkalmazást hoz létre az App Service-ben a kapcsolódó erőforrásokkal együtt, majd hozzáköti egy egyéni tartománynév SSL-tanúsítványát. A példához a következők szükségesek:

* Hozzáférés a tartományregisztrálója DNS-konfigurációs oldalához.
* Egy érvényes .PFX-fájl, valamint a kapcsolódó jelszó a feltölteni és hozzákötni kívánt SSL-tanúsítványhoz.
* Egy olyan A rekord konfigurálása az egyéni tartományban, amely a webalkalmazás alapértelmezett tartománynevére mutat. További információ: [Egyéni tartományutasítások leképezése az Azure App Service-ben](https://aka.ms/appservicecustomdns).

Egy SSL-tanúsítvány kötéséhez a függvényalkalmazást nem használatalapú, hanem App Service-csomagban kell létrehoznia.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket. 

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Létrehoz egy tárfiókot, amely a függvényalkalmazáshoz szükséges. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az-appservice-plan-create) | Létrehoz egy App Service-csomagot, amely az SSL-tanúsítványok kötéséhez szükséges. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Létrehoz egy függvényalkalmazást az App Service-csomagban. |
| [az functionapp config hostname add](https://docs.microsoft.com/cli/azure/functionapp/config/hostname#az-functionapp-config-hostname-add) | Leképez egy egyéni tartományt egy függvényalkalmazásra. |
| [az functionapp config ssl upload](https://docs.microsoft.com/cli/azure/functionapp/config/ssl#az-functionapp-config-ssl-upload) | Feltölt egy SSL-tanúsítványt egy függvényalkalmazásba. |
| [az functionapp config ssl bind](https://docs.microsoft.com/cli/azure/functionapp/config/ssl#az-functionapp-config-ssl-bind) | Hozzáköt egy feltöltött SSL-tanúsítványt egy függvényalkalmazáshoz. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További App Service CLI-példaszkripteket az [Azure App Service dokumentációjában](../functions-cli-samples.md) találhat.
