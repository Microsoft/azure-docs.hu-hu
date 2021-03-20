---
title: Példa API Management-szabályzat – hibák küldése a Stackify a naplózáshoz
titleSuffix: Azure API Management
description: Azure API Management-szabályzat – példa – bemutatja, hogyan adhat hozzá hiba-naplózási szabályzatot, hogy hibákat küldjön a Stackify a naplózáshoz.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 89428e9a64c6d4ae78d333c0cf597531588b1638
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92072067"
---
# <a name="send-errors-to-stackify-for-logging"></a>Hibák küldése a Stackify a naplózáshoz

Ez a cikk egy Azure API Management-szabályzatot mutat be, amely bemutatja, hogyan adhat hozzá hiba-naplózási szabályzatot, hogy hibákat küldjön a Stackify a naplózáshoz. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-reference.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot az **on-Error** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>Következő lépések

További információ a APIM-házirendekről:

+ [Átalakítási szabályzatok](../api-management-transformation-policies.md)
+ [Szabályzatminták](../policy-reference.md)