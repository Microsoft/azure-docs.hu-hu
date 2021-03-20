---
title: Tanácsadási szolgáltatás ajánlatának létrehozása a Microsoft kereskedelmi piactéren
description: Ismerje meg, hogyan hozhat létre új tanácsadói szolgáltatást a Microsoft AppSource vagy az Azure Marketplace-hez a Microsoft partner Center kereskedelmi Marketplace programjának használatával.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: e9a0b2fe883fa46010fda74c58908128d05919e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92754454"
---
# <a name="how-to-create-a-consulting-service-offer-in-the-commercial-marketplace"></a>Tanácsadási szolgáltatás ajánlatának létrehozása a kereskedelmi piactéren

Ez a cikk azt ismerteti, hogyan hozható létre a Microsoft kereskedelmi piactérhez készült tanácsadói szolgáltatás ajánlata a partner Center használatával. 

## <a name="before-you-begin"></a>Előkészületek

A tanácsadási szolgáltatás ajánlatának közzétételéhez meg kell felelnie bizonyos támogathatósági követelményeknek, hogy be tudja mutatni a szakértelmet a saját területén. Ha még nem tette meg, olvassa el [a kereskedelmi piactérre vonatkozó tanácsadói szolgáltatás ajánlatának megtervezése](./plan-consulting-service-offer.md)című cikkét. Ismerteti a tanácsadási szolgáltatás előfeltételeit, valamint azokat az eszközöket, amelyekre szüksége lesz, amikor az ajánlatot a partner centerrel hozza létre.

## <a name="create-a-new-consulting-service-offer"></a>Új tanácsadói szolgáltatási ajánlat létrehozása

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).
2.  A bal oldali navigációs menüben válassza a **kereskedelmi piactér**  >  **– Áttekintés** lehetőséget.
3.  Az Áttekintés lapon válassza az **+ új ajánlat**-  >  **tanácsadási szolgáltatás** elemet.

    ![A bal oldali navigációs menü ábrázolása.](./media/new-offer-consulting-service.png)

4. Az **új ajánlat** párbeszédpanelen adja meg az **ajánlat azonosítóját**. Ez az azonosító a kereskedelmi Piactéri lista URL-címében látható. Ha például beírja a test-Offer-1 értéket ebben a mezőben, az ajánlat webes címe lesz `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .

    * A fiók minden ajánlatának egyedi ajánlat-AZONOSÍTÓval kell rendelkeznie.
    * Csak kisbetűket és számokat használjon. Az ajánlat-azonosító tartalmazhat kötőjeleket és aláhúzást, de nem tartalmazhat szóközt, és legfeljebb 50 karakter hosszú lehet.
    * Az ajánlat azonosítója a **Létrehozás** gombra kattintva nem módosítható.

5. Adjon meg egy **ajánlat-aliast**. Ez a partner Centerben az ajánlathoz használt név. Nem látható az online áruházakban, és eltér az ügyfelek által megjelenített ajánlat nevétől.
6. Az ajánlat létrehozásához és a folytatáshoz válassza a **Létrehozás** lehetőséget.

## <a name="configure-lead-management"></a>Érdeklődők kezelésének konfigurálása

Az Ügyfélkapcsolat-kezelési (CRM) rendszer a kereskedelmi Piactéri ajánlattal csatlakoztatható, így az ügyfelek kapcsolattartási adatai is megjelenhetnek, ha az ügyfél a tanácsadási szolgáltatás iránt érdeklődik. Ezt a csatlakozást bármikor módosíthatja az ajánlat létrehozásakor vagy után. Részletes útmutatásért tekintse [meg a kereskedelmi Marketplace-ajánlat ügyfeleinek vezetőit](./partner-center-portal/commercial-marketplace-get-customer-leads.md).

Az érdeklődői felügyelet konfigurálása a partner Centerben:

1.  A partner Centerben lépjen az **ajánlat beállítása** lapra.
2.  Az **ügyfél-érdeklődők** területen válassza a **Kapcsolódás** hivatkozást.
3.  A **kapcsolat részletei** párbeszédpanelen válasszon ki egy érdeklődő célhelyet a listából.
4.  Fejezze be a megjelenő mezőket. A részletes lépésekért tekintse meg a következő cikkeket:

    * [Az ajánlat konfigurálása az érdeklődők Azure-táblázatba való küldésére](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [Az ajánlat beállítása a dynamics 365 Customer engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (korábban Dynamics CRM Online) felé irányuló érdeklődők küldésére
    * [Az ajánlat beállítása az érdeklődők HTTPS-végpontra küldéséhez](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [Az ajánlat beállítása, hogy érdeklődőket küldjön a Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [Az ajánlat beállítása, hogy érdeklődőket küldjön a Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5.  A megadott konfiguráció ellenőrzéséhez válassza az **Érvényesítés hivatkozást**.
6.  Ha konfigurálta a kapcsolat részleteit, válassza a **Csatlakozás** lehetőséget.
7.  Válassza a **Piszkozat mentése** lehetőséget.

Miután elküldte az ajánlatát a partner Centerben való közzétételre, érvényesítjük a kapcsolatot, és elküldünk egy teszt-érdeklődőt. Miközben az ajánlat előzetes verziójának megtekintése előtt megtekinti az ajánlatot, tesztelje a vezetőjét, és próbálja meg saját magának megvásárolnia az ajánlatot az előzetes verziójú környezetben.

> [!TIP]
> Győződjön meg arról, hogy a vezető célhely kapcsolata frissül, így nem veszíti el az érdeklődőket.

## <a name="next-steps"></a>Következő lépések

* [A tanácsadási szolgáltatás ajánlati tulajdonságainak konfigurálása](./create-consulting-service-offer-properties.md)