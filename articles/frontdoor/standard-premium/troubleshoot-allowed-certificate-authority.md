---
title: Engedélyezett hitelesítésszolgáltatók az Azure-beli előtérben standard/prémium (előzetes verzió)
description: Ez a cikk felsorolja az összes, a saját tanúsítvány létrehozásakor engedélyezett hitelesítésszolgáltatót.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: troubleshooting
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: e31ad0734630fbd0b4960c26f8d562cea66ae675
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434866"
---
# <a name="allowed-certificate-authorities-for-azure-front-door-standardpremium-preview"></a>Engedélyezett hitelesítésszolgáltatók az Azure-beli előtérben standard/prémium (előzetes verzió)

Ha a HTTPS szolgáltatást a saját tanúsítványával engedélyezi egy Azure bejárati ajtó standard/prémium egyéni tartományához. A TLS/SSL-tanúsítvány létrehozásához engedélyezett hitelesítésszolgáltató (CA) szükséges. Ellenkező esetben, ha nem engedélyezett HITELESÍTÉSSZOLGÁLTATÓT vagy önaláírt tanúsítványt használ, a rendszer elutasítja a kérelmet.

[!INCLUDE [cdn-front-door-allowed-ca](../../../includes/cdn-front-door-allowed-ca.md)]
