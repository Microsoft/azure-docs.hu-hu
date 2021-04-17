---
author: baanders
description: tartalmazza a bérlők közötti korlátozást leíró fájlt a Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 16684f8c5947f8b6a09a9a785968dabf3e644c18
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589343"
---
Ennek eredményeképpen a Azure Digital Twins API-khoz való kéréshez olyan felhasználóra vagy szolgáltatásnévre van szükség, amely ugyanannak a bérlőnek a része, amelyben a Azure Digital Twins példány található. A végpontok rosszindulatú Azure Digital Twins érdekében az eredeti bérlőn kívülről származó hozzáférési jogkivonatokkal küldött kérések a "404 Sub-Domain nem található" hibaüzenetet kapják. A rendszer akkor  is ezt a hibát fogja visszaadni, ha a felhasználó vagy [](../articles/digital-twins/concepts-security.md) szolgáltatásnév Azure [AD B2B-együttműködésen](../articles/active-directory/external-identities/what-is-b2b.md) keresztül Azure Digital Twins adattulajdonos vagy Azure Digital Twins adatolvasó szerepkört kapott. 