---
title: Az Azure Digital Twins felhasználói adatkérési funkciói
titleSuffix: Azure Digital Twins
description: Ez a cikk azokat a folyamatokat mutatja be, amelyekkel a személyes és az Azure-beli digitális Twins-beli adatexportálás
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: conceptual
ms.service: digital-twins
services: digital-twins
ms.openlocfilehash: 9942056e59a46c61b4e9da32aab191d4b72db968
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "92461759"
---
# <a name="azure-digital-twins-customer-data-request-features"></a>Azure digitális Twins-ügyfél adatkérési funkciói

Az Azure Digital Twins egy fejlesztői platform, amely egy üzleti környezet biztonságos digitális ábrázolását hozza létre. A reprezentációkat a felhasználók által kiválasztott adatforrások élő állapotú adatok vezérlik.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Az Azure Digital Twins *digitális Twins* nevű digitális képviselete valós környezetekben lévő entitásokat képvisel, és azonosítókkal van társítva. A Microsoft nem tart fenn információt, és nem fér hozzá olyan adatokhoz, amelyek lehetővé tennék az azonosítók összekapcsolását a felhasználók számára. 

Az Azure Digital Twins számos digitális kisvárosa nem képviseli közvetlenül a személyes entitásokat – a tipikusan reprezentált objektumok lehetnek Office-értekezletek, vagy a gyári padlók. Előfordulhat azonban, hogy a felhasználók bizonyos entitásokat személyesen azonosíthatók, és saját belátásuk szerint saját eszközeiket vagy leltározási követési módszereket tartanak fenn a digitális ikrek egyéni felhasználók számára. Az Azure digitális Twins a digitális Twins-hoz kapcsolódó összes adattal kezeli és tárolja a személyes adataikat.

Az adattulajdonosi kérelemben hivatkozott személyes adatok megtekintéséhez, exportálásához és törléséhez az Azure digitális Twins rendszergazdája használhatja a [**Azure Portal**](https://portal.azure.com/) a felhasználók és szerepkörök számára, illetve a digitális ikrekhez készült [**Azure Digital Twins REST API-kat**](/rest/api/azure-digitaltwins/) . A Azure Portal és a REST API-k különböző módszereket biztosítanak a felhasználók számára az ilyen adattulajdonosi kérelmek kiszolgálásához.

## <a name="identifying-customer-data"></a>Ügyféladatok azonosítása

Az Azure Digital Twins a *személyes adatait* a rendszergazdákkal és a felhasználókkal kapcsolatos adatnak tekinti. 

Az Azure digitális Twins a környezethez hozzáféréssel rendelkező felhasználók [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) *objektum-azonosítóját* tárolja. Az Azure Digital Twins a Azure Portal megjeleníti a felhasználói e-mail-címeket, de ezek az e-mail-címek nem tárolódnak az Azure digitális Twins szolgáltatásban. Ezeket a rendszer dinamikusan felkeresi Azure Active Directoryban a Azure Active Directory objektumazonosító használatával.

## <a name="deleting-customer-data"></a>Ügyféladatok törlése

Az Azure Digital Twins-rendszergazdák a Azure Portal használatával törölhetik a felhasználókhoz kapcsolódó adatforrásokat. Az Azure Digital Twins REST API-k használatával is végezhet törlési műveleteket az egyes digitális ikreken. Az elérhető API-kkal kapcsolatos további információkért lásd: [Azure digitális ikrek REST API-k dokumentációja](/rest/api/azure-digitaltwins/).

## <a name="exporting-customer-data"></a>Ügyféladatok exportálása

Az Azure Digital Twins a digitális ikrekhez kapcsolódóan tárolja az adattárolást. A felhasználók megkereshetik és megtekinthetik ezeket az adatok REST API-kon keresztül, és a másolás és Beillesztés paranccsal exportálhatók. 

A vásárlói adatok, beleértve a felhasználói szerepköröket és a szerepkör-hozzárendeléseket, kiválaszthatják, másolhatják és beillesztheti őket a Azure Portalból. 

## <a name="links-to-additional-documentation"></a>További dokumentációra mutató hivatkozások

Az Azure Digital Twins szolgáltatás API-jai teljes listáját az [Azure digitális ikrek REST API-jai dokumentációjában](/rest/api/azure-digitaltwins/)találja.