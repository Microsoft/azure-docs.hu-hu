---
title: Példa Azure API Management-szabályzatra – a OAuth2 használata az átjáró és a háttér közötti engedélyezéshez
titleSuffix: Azure API Management
description: Azure API Management-szabályzat – példa – bemutatja, hogyan használható a OAuth2 az átjáró és a háttér közötti hitelesítéshez. Bemutatja, hogyan szerezheti be a hozzáférési jogkivonatot az AAD-tól, majd továbbíthatja azt a háttérrendszernek.
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
ms.openlocfilehash: 0f5a10eb2ebc3b3a7c527dd718e37faf03c927bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92076096"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>OAuth2 használata az átjáró és a háttér közötti engedélyezéshez

Ez a cikk egy Azure API Management-szabályzatot mutat be, amely bemutatja, hogyan használható a OAuth2 az átjáró és a háttér közötti hitelesítéshez. Bemutatja, hogyan szerezheti be a hozzáférési jogkivonatot az AAD-tól, majd továbbíthatja azt a háttérrendszernek. 

A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-reference.md).

A következő parancsfájl a {{Property}} alkalmazásban megjelenő tulajdonságokat használja. További információ a tulajdonságokról és azok használatáról API Management házirendekben: [ebben a](../api-management-howto-properties.md) témakörben.
 
## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>Következő lépések

További információ a APIM-házirendekről:

+ [Átalakítási szabályzatok](../api-management-transformation-policies.md)
+ [Szabályzatminták](../policy-reference.md)