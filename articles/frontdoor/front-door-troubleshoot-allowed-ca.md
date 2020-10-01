---
title: Engedélyezett HITELESÍTÉSSZOLGÁLTATÓ az egyéni HTTPS engedélyezéséhez az Azure-beli bejárati ajtón
description: Ha saját tanúsítványt használ a HTTPS engedélyezéséhez egy Azure-beli előtérben egyéni tartományon, akkor a létrehozásához egy engedélyezett hitelesítésszolgáltatót (CA) kell használnia.
services: frontdoor
documentationcenter: ''
author: duongau
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 20c5d611272ee2159ce8ddcc2865797a225a7ebb
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91613679"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Engedélyezett hitelesítésszolgáltatók az egyéni HTTPS engedélyezéséhez az Azure-beli bejárati ajtón

Ha [engedélyezi a https-szolgáltatást a saját tanúsítványával](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate) egy Azure-beli előtérben egyéni tartományhoz. A TLS/SSL-tanúsítvány létrehozásához engedélyezett hitelesítésszolgáltató (CA) szükséges. Ellenkező esetben, ha nem engedélyezett HITELESÍTÉSSZOLGÁLTATÓT vagy önaláírt tanúsítványt használ, a rendszer elutasítja a kérelmet.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
