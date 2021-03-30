---
title: Az Azure Event Hubshoz való hozzáférés engedélyezése
description: Ez a cikk az Azure Event Hubs-erőforrásokhoz való hozzáférés engedélyezésének különböző lehetőségeiről nyújt információt.
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: 5fc35387f4ac28ad4dd28bea0013bcdf1e1e9f02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92332382"
---
# <a name="authorize-access-to-azure-event-hubs"></a>Az Azure Event Hubshoz való hozzáférés engedélyezése
Minden alkalommal, amikor egy Event hub-ből tesz közzé vagy használ eseményeket/információkat, az ügyfél megpróbál hozzáférni Event Hubs erőforrásokhoz. A biztonságos erőforrásokra vonatkozó minden kérést engedélyezni kell, hogy a szolgáltatás biztosítsa, hogy az ügyfél rendelkezik a szükséges engedélyekkel az adatközzétételhez/felhasználáshoz. 

Az Azure Event Hubs a következő lehetőségeket kínálja a biztonságos erőforrásokhoz való hozzáférés engedélyezéséhez:

- Azure Active Directory
- Közös hozzáférésű jogosultságkód

> [!NOTE]
> Ez a cikk a Event Hubs és [Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) forgatókönyvekre egyaránt vonatkozik. 

## <a name="azure-active-directory"></a>Azure Active Directory
A Azure Active Directory (Azure AD) integrációja Event Hubs erőforrásokhoz Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) biztosít az ügyfelek erőforrásaihoz való hozzáférés részletes szabályozásához. Az Azure RBAC segítségével engedélyeket biztosíthat a rendszerbiztonsági tag számára, amely lehet egy felhasználó, egy csoport vagy egy egyszerű alkalmazás. A rendszerbiztonsági tag hitelesítése az Azure AD által OAuth 2,0 token visszaküldésével történik. A token használatával engedélyezhető egy Event Hubs erőforrás elérésére irányuló kérés.

Az Azure AD-vel történő hitelesítéssel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Az Azure Event Hubs-kérelmek hitelesítése a Azure Active Directory használatával](authenticate-application.md)
- [Azure Active Directory használatával engedélyezze a hozzáférést Event Hubs erőforrásokhoz](authorize-access-azure-active-directory.md).

## <a name="shared-access-signatures"></a>Közös hozzáférésű aláírások 
A Event Hubs erőforrásokhoz tartozó közös hozzáférésű aláírások (SAS) korlátozott delegált hozzáférést biztosítanak Event Hubs erőforrásaihoz. Megkötések hozzáadása olyan időintervallumhoz, amely esetében az aláírás érvényes, vagy az általa nyújtott engedélyekkel az erőforrások kezelése rugalmasságot biztosít. További információ: a [hitelesítés közös hozzáférésű aláírásokkal (SAS)](authenticate-shared-access-signature.md). 

Az Azure AD által visszaadott OAuth 2,0 tokent használó felhasználók vagy alkalmazások engedélyezése kiváló biztonságot és egyszerű használatot biztosít a közös hozzáférésű aláírások (SAS) számára. Az Azure AD-ben nem kell a hozzáférési jogkivonatokat a kóddal és a potenciális biztonsági rések használatával tárolni. Habár továbbra is használhatja a közös hozzáférésű aláírásokat (SAS) a Event Hubs erőforrások részletes elérésének biztosításához, az Azure AD hasonló képességeket kínál anélkül, hogy az SAS-tokeneket kellene kezelnie, vagy nem kell aggódnia a sérült SAS visszavonásával kapcsolatban. 

Alapértelmezés szerint az összes Event Hubs erőforrás biztonságos, és csak a fiók tulajdonosa számára érhető el. Bár a fentiekben ismertetett hitelesítési stratégiák bármelyikét felhasználhatja az ügyfelek számára az Event hub erőforrásaihoz való hozzáférés biztosításához. A Microsoft az Azure AD használatát javasolja, ha lehetséges, a maximális biztonság és a könnyű használat érdekében.

Az SAS használatával történő engedélyezéssel kapcsolatos további információkért lásd: [hozzáférés engedélyezése Event Hubs erőforrásokhoz közös hozzáférési aláírások használatával](authorize-access-shared-access-signature.md).

## <a name="next-steps"></a>Következő lépések
- Tekintse át a GitHub-tárházban közzétett [Azure RBAC-mintákat](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) . 
- Lásd az alábbi cikkeket:
    - [Kérelmek hitelesítése az Azure Event Hubs alkalmazásból Azure Active Directory használatával](authenticate-application.md)
    - [Felügyelt identitás hitelesítése Azure Active Directory használatával Event Hubs erőforrások eléréséhez](authenticate-managed-identity.md)
    - [Kérelmek hitelesítése az Azure Event Hubs megosztott hozzáférési aláírások használatával](authenticate-shared-access-signature.md)
    - [Hozzáférés engedélyezése Event Hubs erőforrásokhoz a Azure Active Directory használatával](authorize-access-azure-active-directory.md)
    - [Hozzáférés engedélyezése Event Hubs erőforrásokhoz közös hozzáférési aláírások használatával](authorize-access-shared-access-signature.md)

