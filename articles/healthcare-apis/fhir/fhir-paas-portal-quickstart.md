---
title: 'Gyors útmutató: Azure API üzembe helyezése a FHIR Azure Portal használatával'
description: Ebből a rövid útmutatóból megtudhatja, hogyan helyezheti üzembe az Azure API-t a FHIR, és hogyan konfigurálhatja a beállításokat a Azure Portal használatával.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 03/15/2020
ms.author: cavoeg
ms.openlocfilehash: cabacd5204f4a9ac5c17c7bd66924482b5bf688a
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018576"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-portal"></a>Gyors útmutató: Azure API üzembe helyezése a FHIR Azure Portal használatával

Ebből a rövid útmutatóból megtudhatja, hogyan helyezheti üzembe az Azure API-t a FHIR a Azure Portal használatával.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-new-resource"></a>Új erőforrás létrehozása

Nyissa meg a [Azure Portal](https://portal.azure.com) , és kattintson az **erőforrás létrehozása** elemre.

![Erőforrás létrehozása](media/quickstart-paas-portal/portal-create-resource.png)

## <a name="search-for-azure-api-for-fhir"></a>A FHIR készült Azure API keresése

A FHIR készült Azure API-t a "FHIR" kifejezés beírásával keresheti meg a keresőmezőbe:

:::image type="content" source="media/quickstart-paas-portal/portal-search-healthcare-apis.png" alt-text="Egészségügyi API-k keresése":::

## <a name="create-azure-api-for-fhir-account"></a>Azure API létrehozása FHIR-fiókhoz

Válassza a **Létrehozás** lehetőséget egy új Azure API FHIR-fiók létrehozásához:

:::image type="content" source="media/quickstart-paas-portal/portal-create-healthcare-apis.png" alt-text="Azure API létrehozása FHIR-fiókhoz":::

## <a name="enter-account-details"></a>Adja meg a fiók adatait

Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat, válassza ki a fiók nevét, és végül kattintson a **felülvizsgálat + létrehozás** gombra:

:::image type="content" source="media/quickstart-paas-portal/portal-new-healthcareapi-details.png" alt-text="Új egészségügyi API – részletek":::

Erősítse meg a létrehozását, és várja meg a FHIR API üzembe helyezését.

## <a name="additional-settings-optional"></a>További beállítások (nem kötelező)

A hitelesítési beállítások megtekintéséhez kattintson a **Tovább gombra: további beállítások** . A FHIR készült Azure API alapértelmezett konfigurációja az [Azure RBAC használata az adatsíkok szerepköreinek hozzárendeléséhez](configure-azure-rbac.md). Ha ebben a módban van konfigurálva, a FHIR szolgáltatás "Authority" beállítása az előfizetés Azure Active Directory bérlője lesz:

:::image type="content" source="media/rbac/confirm-azure-rbac-mode-create.png" alt-text="Alapértelmezett hitelesítési beállítások":::

Figyelje meg, hogy az engedélyezett objektumazonosítók beírására szolgáló mező szürkén jelenik meg, mert ebben az esetben az Azure RBAC-t használjuk a szerepkör-hozzárendelések konfigurálásához.

Ha a FHIR szolgáltatást külső vagy másodlagos Azure Active Directory bérlő használatára szeretné konfigurálni, akkor megváltoztathatja a szolgáltatót, és megadhatja azokat a felhasználókat és csoportokat, akik számára engedélyezni kívánja a hozzáférést a kiszolgálóhoz. További információ: a [helyi RBAC konfigurációs](configure-local-rbac.md) útmutatója.

## <a name="fetch-fhir-api-capability-statement"></a>FHIR API-képesség utasításának beolvasása

Annak ellenőrzéséhez, hogy az új FHIR API-fiók ki van-e építve, egy böngészőn keresztül megjelenítheti a képesség utasításait `https://<ACCOUNT-NAME>.azurehealthcareapis.com/metadata` .

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölheti az erőforráscsoportot, a FHIR készült Azure API-t és az összes kapcsolódó erőforrást. Ehhez válassza ki az FHIR-fiókhoz tartozó Azure API-t tartalmazó erőforráscsoportot, válassza az **erőforráscsoport törlése** elemet, majd erősítse meg a törölni kívánt erőforráscsoport nevét.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezte a FHIR készült Azure API-t az előfizetésében. Ha további beállításokat szeretne megadni a FHIR készült Azure API-ban, folytassa a további beállítások útmutatóval. Ha készen áll a FHIR készült Azure API használatára, olvassa el az alkalmazások regisztrálásával foglalkozó témakört.

>[!div class="nextstepaction"]
>[További beállítások a FHIR készült Azure API-ban](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Alkalmazások regisztrálása – áttekintés](fhir-app-registration.md)
