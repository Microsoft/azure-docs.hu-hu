---
title: Azure CLI-példaszkript – Függvényalkalmazás létrehozása kiszolgáló nélküli végrehajtáshoz | Microsoft Docs
description: Azure CLI-példaszkript – Függvényalkalmazás létrehozása kiszolgáló nélküli végrehajtáshoz
services: functions
documentationcenter: functions
author: ggailey777
manager: jeconnoc
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.service: azure-functions
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/03/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 265c4fa3c28783f0d9e5d789348c3cd2e249eb29
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60325721"
---
# <a name="create-a-function-app-for-serverless-code-execution"></a>Függvényalkalmazás létrehozása kiszolgáló nélküli kódvégrehajtáshoz

Ez az Azure Functions-példaszkript egy függvényalkalmazást hoz létre, amely az Ön függvényeinek tárolójaként szolgál. A függvényalkalmazás létrehozása a [használatalapú csomag](../functions-scale.md#consumption-plan) segítségével történik, amely ideális az eseményvezérelt, kiszolgáló nélküli számítási feladatokhoz.

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket. 

## <a name="sample-script"></a>Példaszkript

Ez a szkript egy Azure-függvényalkalmazást hoz létre a [használatalapú csomag](../functions-scale.md#consumption-plan) segítségével.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-consumption/create-function-app-consumption.sh "Create an Azure Function on a consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik. Ez a szkript a következő parancsokat használja:

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Létrehoz egy Azure Storage-fiókot. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Létrehoz egy függvényalkalmazást. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További Azure Functions CLI-példaszkripteket az [Azure Functions dokumentációjában](../functions-cli-samples.md) találhat.
