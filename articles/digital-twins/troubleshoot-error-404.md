---
title: 'Azure Digital Twins kérés a következő állapottal meghiúsult: 404 Sub-Domain nem található'
description: "\"A szolgáltatáskérés sikertelen volt\" okai és megoldásai. Állapot: 404 Sub-Domain nem található\" a Azure Digital Twins."
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 4/13/2021
ms.openlocfilehash: e3fe3ad22098d796faa309ff3509c318a7e1df4d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590248"
---
# <a name="service-request-failed-status-404-sub-domain-not-found"></a>A szolgáltatáskérés sikertelen volt. Állapot: 404 Sub-Domain nem található

Ez a cikk a 404-es hiba fogadásának okait és megoldási lépéseit ismerteti a szolgáltatáskérések és a Azure Digital Twins. 

## <a name="symptoms"></a>Hibajelenségek

Ez a hiba akkor fordulhat elő, ha egy Azure Digital Twins-példányt a példánytól [](../active-directory/develop/quickstart-create-new-tenant.md) eltérő Azure Active Directory-bérlőhöz tartozó szolgáltatásnévvel vagy felhasználói fiókkal fér hozzá. Úgy [tűnik, hogy](concepts-security.md) a megfelelő szerepkörök vannak hozzárendelve az identitáshoz, de az API-kérések meghiúsulnak hibaállapottal. `404 Sub-Domain not found`

## <a name="causes"></a>Okok

### <a name="cause-1"></a>Ok #1

Azure Digital Twins szükséges, hogy minden hitelesítő felhasználó ugyanabba az Azure AD-bérlőbe tartozni, mint a Azure Digital Twins példány.

[!INCLUDE [digital-twins-tenant-limitation](../../includes/digital-twins-tenant-limitation.md)]

## <a name="solutions"></a>Megoldások

### <a name="solution-1"></a>Megoldás #1

Ezt a problémát úgy oldhatja meg, ha egy másik bérlő minden összevont identitása jogkivonatot kér **a** Azure Digital Twins példány "kezdőlapja" bérlőtől. 

[!INCLUDE [digital-twins-tenant-solution-1](../../includes/digital-twins-tenant-solution-1.md)]

### <a name="solution-2"></a>Megoldás #2

Ha a kódban a osztályt használja, és a jogkivonat beszerzése után továbbra is ezzel a hibával találkozik, megadhatja az otthoni bérlőt a beállítások között, hogy tisztázza a bérlőt, még akkor is, ha a hitelesítés alapértelmezés szerint egy másik típusra `DefaultAzureCredential` `DefaultAzureCredential` van beállítva.

[!INCLUDE [digital-twins-tenant-solution-2](../../includes/digital-twins-tenant-solution-2.md)]

## <a name="next-steps"></a>Következő lépések

További információ a biztonságról és az engedélyekről a Azure Digital Twins:
* [*Alapfogalmak: Biztonsági Azure Digital Twins megoldásokhoz*](concepts-security.md)
