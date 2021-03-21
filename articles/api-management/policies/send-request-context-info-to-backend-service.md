---
title: Példa API Management-szabályzat – a kérelem környezeti információinak küldése a háttérbeli szolgáltatásnak
titleSuffix: Azure API Management
description: Azure API Management-szabályzat – példa – bemutatja, hogyan küldheti el a kérések környezeti információit a háttér-szolgáltatásnak.
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
ms.openlocfilehash: 7782af3c8a533ceb3a6d2bd3b412c21469f9a021
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92078816"
---
# <a name="send-request-context-information-to-the-backend-service"></a>A kérés környezetére vonatkozó információk küldése a háttérszolgáltatásnak

Ez a cikk egy Azure API Management-szabályzatot mutat be, amely bemutatja, hogyan kell elküldeni a kérések környezeti információit a háttér-szolgáltatásnak. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-reference.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Send request context information to the backend service.policy.xml)]

## <a name="next-steps"></a>Következő lépések

További információ a APIM-házirendekről:

+ [Átalakítási szabályzatok](../api-management-transformation-policies.md)
+ [Szabályzatminták](../policy-reference.md)