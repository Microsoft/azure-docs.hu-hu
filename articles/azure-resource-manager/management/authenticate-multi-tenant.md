---
title: Hitelesítés bérlőkön keresztül
description: Leírja, hogyan kezeli a Azure Resource Manager a különböző bérlők hitelesítési kérelmeit.
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 7a13ba6f6cbfc10c52484c45e4011da7a0d8ee4c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "75478824"
---
# <a name="authenticate-requests-across-tenants"></a>Kérelmek hitelesítése a bérlők között

Több-bérlős alkalmazások létrehozásakor előfordulhat, hogy a különböző bérlők erőforrásaihoz tartozó hitelesítési kérelmeket kell kezelnie. Gyakori eset az, amikor az egyik bérlőn lévő virtuális gépnek egy másik bérlőben lévő virtuális hálózathoz kell csatlakoznia. A Azure Resource Manager a kisegítő tokenek tárolására szolgáló fejléc-értéket biztosít a különböző bérlőknek küldött kérések hitelesítéséhez.

## <a name="header-values-for-authentication"></a>A hitelesítés fejlécének értékei

A kérelem a következő hitelesítési fejléc-értékekkel rendelkezik:

| Fejléc neve | Description | Példaérték |
| ----------- | ----------- | ------------ |
| Engedélyezés | Elsődleges jogkivonat | Tulajdonos &lt; elsődleges – token&gt; |
| x-MS-Authorization-kiegészítő | Kiegészítő tokenek | Tulajdonos &lt; -kiegészítő – token1 &gt; , EncryptedBearer &lt; -kiegészítő – token2 &gt; , tulajdonosi &lt; segéd – token3&gt; |

A kiegészítő fejléc legfeljebb három kiegészítő tokent tud tárolni. 

A több-bérlős alkalmazás kódjában szerezze be a többi bérlő hitelesítési jogkivonatát, és tárolja azokat a kiegészítő fejlécekben. Az összes tokennek ugyanahhoz a felhasználóhoz vagy alkalmazáshoz kell tartoznia. A felhasználót vagy alkalmazást vendégként kell meghívni a többi bérlőnek.

## <a name="processing-the-request"></a>A kérelem feldolgozása

Amikor az alkalmazás egy kérelmet küld a Resource managernek, a rendszer az elsődleges jogkivonat identitása alatt futtatja a kérést. Az elsődleges tokennek érvényesnek és lejártnak kell lennie. Ennek a tokennek egy olyan bérlőtől kell származnia, amely képes kezelni az előfizetést.

Ha a kérelem egy másik bérlőtől származó erőforrásra hivatkozik, a Resource Manager ellenőrzi a kisegítő jogkivonatokat annak megállapításához, hogy a kérés feldolgozható-e. A fejlécben szereplő összes kiegészítő tokennek érvényesnek és lejártnak kell lennie. Ha bármelyik jogkivonat lejárt, a Resource Manager egy 401-es választ ad vissza. A válasz tartalmazza az ügyfél-azonosítót és a bérlő AZONOSÍTÓját, amely érvénytelen a jogkivonat esetében. Ha a kiegészítő fejléc érvényes jogkivonatot tartalmaz a bérlőhöz, a rendszer feldolgozza a több-bérlős kérelmet.

## <a name="next-steps"></a>Következő lépések

* A hitelesítési kérelmekkel kapcsolatos további tudnivalókért lásd: [hitelesítési folyamatok és alkalmazási forgatókönyvek](../../active-directory/develop/authentication-flows-app-scenarios.md).
* További információ a tokenekről: [Azure Active Directory hozzáférési tokenek](../../active-directory/develop/access-tokens.md).
