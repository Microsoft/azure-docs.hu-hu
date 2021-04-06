---
title: Felügyelt szolgáltatási ajánlat létrehozása a Microsoft kereskedelmi piactéren
description: Ismerje meg, hogyan hozhat létre új, felügyelt szolgáltatási ajánlatot az Azure Marketplace-hez a kereskedelmi piactér program használatával a Microsoft partner Centerben.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 46a9c9d953a2311d83b5fd18b83727d6765734fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97918271"
---
# <a name="how-to-create-a-managed-service-offer-for-the-commercial-marketplace"></a>Felügyelt szolgáltatási ajánlat létrehozása a kereskedelmi piactérhez

Ez a cikk azt ismerteti, hogyan hozhat létre felügyelt szolgáltatási ajánlatot a Microsoft kereskedelmi piactérhez a partner Center használatával.

A felügyelt szolgáltatások ajánlatának közzétételéhez a Microsoft a Cloud platformon szerzett Gold vagy Silver Microsoft kompetenciával kell rendelkeznie. Ha még nem tette meg, olvassa el a [következőt: felügyelt szolgáltatás ajánlatának megtervezése a kereskedelmi piactéren](./plan-managed-service-offer.md). Segít előkészíteni azokat az eszközöket, amelyekre szüksége lesz az ajánlat a partner Centerben való létrehozásakor.

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).
2. A bal oldali navigációs menüben válassza a **kereskedelmi piactér**  >  **– Áttekintés** lehetőséget.
3. Az Áttekintés lapon válassza az **+ új ajánlat**  >  **felügyelt szolgáltatás** elemet.

:::image type="content" source="./media/new-offer-managed-service.png" alt-text="A bal oldali navigációs menü ábrázolása.":::

4. Az **új ajánlat** párbeszédpanelen adja meg az **ajánlat azonosítóját**. Ez a fiókban található egyes ajánlatok egyedi azonosítója. Ez az azonosító a kereskedelmi piactér-lista és a Azure Resource Manager-sablonok URL-címében látható, ha van ilyen. Ha például beírja a test-Offer-1 értéket ebben a mezőben, az ajánlat webes címe lesz `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .

    * A fiók minden ajánlatának egyedi ajánlat-AZONOSÍTÓval kell rendelkeznie.
    * Csak kisbetűket és számokat használjon. Tartalmazhat kötőjeleket és aláhúzásokat, de nem tartalmazhat szóközt, és legfeljebb 50 karakter hosszú lehet.
    * Az ajánlat azonosítója a **Létrehozás** gombra kattintva nem módosítható.

5. Adjon meg egy **ajánlat-aliast**. Ez a partner Centerben az ajánlathoz használt név. Nem látható az online áruházakban, és eltér az ügyfelek által megjelenített ajánlat nevétől.
6. Az ajánlat létrehozásához és a folytatáshoz válassza a **Létrehozás** lehetőséget.

## <a name="configure-lead-management"></a>Érdeklődők kezelésének konfigurálása

Az Ügyfélkapcsolat-kezelési (CRM) rendszer a kereskedelmi Piactéri ajánlattal csatlakoztatható, így az ügyfelek kapcsolattartási adatai is megjelenhetnek, ha az ügyfél a tanácsadási szolgáltatás iránt érdeklődik. Ezt a csatlakozást bármikor módosíthatja az ajánlat létrehozásakor vagy után. Részletes útmutatásért tekintse [meg a kereskedelmi Marketplace-ajánlat ügyfeleinek vezetőit](./partner-center-portal/commercial-marketplace-get-customer-leads.md).

Az érdeklődői felügyelet konfigurálása a partner Centerben:

1. A partner Centerben lépjen az **ajánlat beállítása** lapra.
2. Az **ügyfél-érdeklődők** területen válassza a **Kapcsolódás** hivatkozást.
3. A **kapcsolat részletei** párbeszédpanelen válasszon ki egy érdeklődő célhelyet a listából.
4. Fejezze be a megjelenő mezőket. A részletes lépésekért tekintse meg a következő cikkeket:

    * [Az ajánlat konfigurálása az érdeklődők Azure-táblázatba való küldésére](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [Az ajánlat beállítása a dynamics 365 Customer engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (korábban Dynamics CRM Online) felé irányuló érdeklődők küldésére
    * [Az ajánlat beállítása az érdeklődők HTTPS-végpontra küldéséhez](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [Az ajánlat beállítása, hogy érdeklődőket küldjön a Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [Az ajánlat beállítása, hogy érdeklődőket küldjön a Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5. A megadott konfiguráció ellenőrzéséhez válassza az **Érvényesítés hivatkozást**.
6. Ha konfigurálta a kapcsolat részleteit, válassza a **Csatlakozás** lehetőséget.
7. Válassza a **Piszkozat mentése** lehetőséget.

Miután elküldte az ajánlatát a partner Centerben való közzétételre, érvényesítjük a kapcsolatot, és elküldünk egy teszt-érdeklődőt. Miközben az ajánlat előzetes verziójának megtekintése előtt megtekinti az ajánlatot, tesztelje a vezetőjét, és próbálja meg saját magának megvásárolnia az ajánlatot az előzetes verziójú környezetben.

> [!TIP]
> Győződjön meg arról, hogy a vezető célhely kapcsolata frissül, így nem veszíti el az érdeklődőket.

## <a name="configure-offer-properties"></a>Ajánlat tulajdonságainak konfigurálása

A partner Center ajánlatának Tulajdonságok lapján megadhatja az ajánlatra érvényes kategóriákat, valamint a jogi szerződéseket. Ezek az információk gondoskodnak arról, hogy a felügyelt szolgáltatás megfelelően jelenjen meg az online áruházban, és elérhető legyen az ügyfelek megfelelő készlete számára.

### <a name="select-a-category"></a>Kategória kiválasztása

A **Kategóriák** területen válasszon ki legalább egy, legfeljebb öt kategóriát az ajánlatnak a megfelelő kereskedelmi Piactéri keresési területeken való csoportosításához.

### <a name="provide-terms-and-conditions"></a>Feltételek és kikötések megadása

A **jogi** területen adja meg az ajánlat használati feltételeit. Az ajánlat használata előtt el kell fogadnia az ügyfeleket. Megadhatja azt az URL-címet is, ahol a feltételek és kikötések megtalálhatók.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="next-step"></a>Következő lépés

* [A felügyelt szolgáltatás ajánlati listájának konfigurálása](./create-managed-service-offer-listing.md)