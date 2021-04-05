---
title: Engedélyezett HITELESÍTÉSSZOLGÁLTATÓ az egyéni HTTPS engedélyezéséhez
titleSuffix: Azure Content Delivery Network
description: Ha saját tanúsítványt használ a HTTPS engedélyezéséhez egy egyéni tartományon, a létrehozásához egy engedélyezett hitelesítésszolgáltatót (CA) kell használnia.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: troubleshooting
ms.date: 02/04/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: f98e28c89fa70831108cfbbbaca6e2f316d1b039
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99573398"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https"></a>Engedélyezett hitelesítésszolgáltatók az egyéni HTTPS engedélyezéséhez

Az adott tanúsítványra vonatkozó követelmények akkor szükségesek, ha [engedélyezi a https szolgáltatást](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) egy Azure CDN (Content Delivery Network) egyéni tartományhoz tartozó saját tanúsítvány használatával. 

* A **Microsoft profil Azure CDN szabványának** az alábbi listán szereplő jóváhagyott HITELESÍTÉSSZOLGÁLTATÓK (CA) tanúsítványát kell megadnia. Ha egy tanúsítvány nem jóváhagyott HITELESÍTÉSSZOLGÁLTATÓTÓL származik, vagy ha önaláírt tanúsítványt használ, a rendszer elutasítja a kérelmet. 

* **Azure CDN a** Verizon és **a prémium szintű Azure CDN a Verizon** -profilokból származó, érvényes tanúsítványokat bármely érvényes hitelesítésszolgáltatótól elfogadják. A Verizon-profilok nem támogatják az önaláírt tanúsítványokat.

> [!NOTE]
> A saját tanúsítvány használata az egyéni tartomány HTTPS-funkciójának engedélyezéséhez *nem* érhető el **Azure CDN standard Akamai-** profilokból. 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

