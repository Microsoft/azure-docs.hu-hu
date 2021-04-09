---
title: Az Azure ExpressRoute CrossConnnections API fejlesztése és integrációja
description: Ez a cikk részletes áttekintést nyújt a expressRouteCrossConnections erőforrástípus ExpressRoute-partnereiről.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: duau
ms.openlocfilehash: b80392231dba26a10141dcd8247b092e8171894c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011982"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>ExpressRoute CrossConnnections API-fejlesztés és-integráció

A ExpressRoute partner Resource Manager API lehetővé teszi, hogy a ExpressRoute-partnerek kezelni tudják az ügyfél ExpressRoute-áramkörök 2. és 3. rétegbeli konfigurációját. A ExpressRoute partner Resource Manager API egy új erőforrástípust ( **expressRouteCrossConnections**) vezet be. A partnerek ezt az erőforrást használják az ügyfél-ExpressRoute áramkörök kezelésére.

## <a name="workflow"></a>Munkafolyamat

A expressRouteCrossConnections erőforrás a ExpressRoute áramkörhöz tartozó árnyékmásolat-erőforrás. Amikor egy Azure-ügyfél létrehoz egy ExpressRoute áramkört, és kiválaszt egy adott ExpressRoute-partnert, a Microsoft létrehoz egy expressRouteCrossConnections-erőforrást a partner Azure ExpressRoute-felügyeleti előfizetésében. Ennek során a Microsoft definiál egy erőforráscsoportot a expressRouteCrossConnections-erőforrás létrehozásához a alkalmazásban. Az erőforráscsoport elnevezési szabványa **CrossConnection-* PeeringLocation * * *; ahol a PeeringLocation = a ExpressRoute helye. Ha például egy ügyfél létrehoz egy ExpressRoute-áramkört Denverben, a CrossConnection a partner Azure-előfizetésében jön létre a következő erőforráscsoporthoz: **CrossConnnection-Denver**.

A ExpressRoute-partnerek a 2. rétegbeli és 3. rétegbeli konfigurációt a expressRouteCrossConnections-erőforrásra vonatkozó REST-műveletek kiadásával kezelik.

## <a name="benefits"></a>Előnyök

A expressRouteCrossConnections-erőforrásra való áttérés előnyei:

* A ExpressRoute-partnerek jövőbeni továbbfejlesztései elérhetővé válnak a ExpressRouteCrossConnection-erőforráson.

* A partnerek [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](../role-based-access-control/overview.md) alkalmazhatnak a expressRouteCrossConnection-erőforrásra. Ezek a vezérlőelemek meghatározhatják, hogy mely felhasználói fiókok módosíthatják a expressRouteCrossConnection-erőforrást, és hogyan lehet hozzáadni/frissíteni vagy törölni a társ-konfigurálási konfigurációkat.

* A expressRouteCrossConnection erőforrás olyan API-kat tesz elérhetővé, amelyek segíthetnek a ExpressRoute-kapcsolatok hibaelhárításában. Ide tartozik az ARP-tábla, a BGP-útválasztási táblázat összefoglalása és a BGP-útválasztási táblázat részletei. Ezt a képességet a klasszikus üzembe helyezési API-k nem támogatják.

* A partnerek a *RouteFilter* -erőforrás használatával is megkereshetik a meghirdetett közösségeket a Microsoft-partnereken.

## <a name="api-development-and-integration-steps"></a>API-fejlesztési és integrációs lépések

A partner API-val való fejlesztéshez a ExpressRoute-partnerek kihasználják az ügyfél és a tesztelési partner beállításait. A tesztelési ügyfél beállításával ExpressRoute-áramkörök hozhatók létre olyan tesztelési társítási helyen, amelyek a dummy-eszközökre és-portokra vannak leképezve. A tesztelési partner beállítása a tesztelési társítási helyen létrehozott ExpressRoute-áramkörök kezelésére szolgál.

### <a name="1-enlist-subscriptions"></a>1. előfizetések

A tesztelési partner igényléséhez és az ügyfél beállításának teszteléséhez két utólagos elszámolású Azure-előfizetést kell igénybe vennie a ExpressRoute Engineering Contact szolgáltatásban:
* **ExpressRoute_API_Dev_Provider_Sub:** Ez az előfizetés a próbabábu-eszközök és portok tesztelési célú helyein létrehozott ExpressRoute áramkörök felügyeletére szolgál.

* **ExpressRoute_API_Dev_Customer_Sub:** Ezt az előfizetést ExpressRoute-áramkörök létrehozásához használja a rendszer a dummy-eszközökre és-portokra leképező tesztelési társítási helyein.

A tesztelési társítási helyszínek: a dummy-eszközök és-portok alapértelmezés szerint nem érhetők el az üzemi ügyfelek számára. Ahhoz, hogy ExpressRoute-áramköröket lehessen létrehozni, amelyek leképezik a tesztet, engedélyezni kell egy előfizetési szolgáltatás jelölőjét.

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2. regisztrálja a Dev_Provider-előfizetést a expressRouteCrossConnections API eléréséhez

A expressRouteCrossConnections API-hoz való hozzáféréshez a partner-előfizetést regisztrálni kell a **Microsoft. Network erőforrás-szolgáltatóban**. A regisztrációs folyamat befejezéséhez kövesse az [Azure Resource Providers és types](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) című cikk lépéseit.

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3. Azure Resource Manager REST API-hívások hitelesítésének beállítása

A legtöbb Azure-szolgáltatáshoz a Service API-k meghívása előtt érvényes hitelesítő adatokkal kell rendelkeznie az ügyfél kódjával a Resource Manager használatával történő hitelesítéshez. A hitelesítés az Azure AD különböző résztvevői között történik, és a hitelesítést igazoló hozzáférési jogkivonattal látja el az ügyfelet.

A hitelesítési folyamat két fő lépést tesz szükségessé:

1. [Regisztrálja az ügyfelet](/rest/api/azure/#register-your-client-application-with-azure-ad).
2. [Hozza létre a hozzáférési kérelmet](/rest/api/azure/#create-the-request).

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4. adja meg a hálózati közreműködői engedélyt az ügyfélalkalmazás számára

A hitelesítés sikeres konfigurálása után meg kell adnia a hálózat közreműködői hozzáférését az ügyfélalkalmazás számára a Dev_Provider_Sub alatt. Az engedély megadásához jelentkezzen be a [Azure Portalba](https://ms.portal.azure.com/#home) , és hajtsa végre a következő lépéseket:

1. Navigáljon az előfizetésekhez, és válassza ki a Dev_Provider_Sub
2. Navigáljon Access Control (IAM)
3. Szerepkör-hozzárendelés hozzáadása
4. Hálózati közreműködő szerepkör kiválasztása
5. Hozzáférés kiosztása az Azure AD-felhasználóhoz,-csoporthoz vagy egyszerű szolgáltatáshoz
6. Ügyfélalkalmazás kiválasztása
7. Módosítások mentése

### <a name="5-develop"></a>5. fejlesztés

Fejlesztés a [EXPRESSROUTECROSSCONNECTIONS API](/rest/api/expressroute/expressroutecrossconnections)-val.

## <a name="rest-api"></a>REST API

REST API dokumentációért lásd: [ExpressRoute CrossConnections REST API](/rest/api/expressroute/expressroutecrossconnections) .

## <a name="next-steps"></a>Következő lépések

További információ az összes ExpressRoute REST API-ról: [EXPRESSROUTE REST API](/rest/api/expressroute/)-k.