---
title: Webalkalmazás-oktatóanyag – Azure API beállítása a FHIR
description: Ez az oktatóanyag végigvezeti egy egyszerű webalkalmazás üzembe helyezésének példáján. Ez az első oktatóanyag a FHIR készült Azure API előfeltételeit és üzembe helyezését ismerteti.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.custom: devx-track-js
ms.openlocfilehash: 9b1dc7d7eee263b781d39c4beccf9388efbaa3d8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334137"
---
# <a name="deploy-javascript-app-to-read-data-from-fhir-service"></a>JavaScript-alkalmazás üzembe helyezése a FHIR-szolgáltatásból származó adatok olvasásához
Ebben az oktatóanyagban egy kis JavaScript-alkalmazást fog üzembe helyezni, amely adatokat olvas be egy FHIR-szolgáltatásból. Az oktatóanyag lépései a következők:
1. FHIR-kiszolgáló üzembe helyezése
1. Nyilvános ügyfélalkalmazás regisztrálása
1. Az alkalmazáshoz való hozzáférés tesztelése
1. Webalkalmazás létrehozása, amely beolvassa ezt a FHIR-adatokat

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyagok elindítása előtt a következő elemekre lesz szüksége:
1. Azure-előfizetés
1. Egy Azure Active Directory-bérlő
1. [Poster](https://www.getpostman.com/) telepítve

> [!NOTE]
> Ebben az oktatóanyagban a FHIR szolgáltatás, az Azure AD-alkalmazás és az Azure AD-felhasználók mindegyike ugyanabban az Azure AD-bérlőben található. Ha ez nem igaz, továbbra is követheti ezt az oktatóanyagot, de előfordulhat, hogy a hivatkozott dokumentumok némelyikére további lépéseket kell tennie.

## <a name="deploy-azure-api-for-fhir"></a>Az Azure API for FHIR üzembe helyezése
Az oktatóanyag első lépéseként be kell szereznie az Azure API-t a FHIR telepítőjének megfelelően.

1. A [FHIR készült Azure API](fhir-paas-portal-quickstart.md) üzembe helyezése
1. Miután telepítette az Azure API-t a FHIR, konfigurálja a [CORS](configure-cross-origin-resource-sharing.md) BEÁLLÍTÁSAIT a FHIR Azure API-ban, majd válassza a CORS lehetőséget. 
    1. Az **eredet** beállítása *
    1. **Fejlécek** beállítása *
    1. A **módszerek**területen válassza **az összes kijelölése** lehetőséget.
    1. A **maximális kor** beállítása **600** -re

## <a name="next-steps"></a>Következő lépések
Most, hogy már telepítette az Azure API-t a FHIR üzembe helyezéséhez, készen áll a nyilvános ügyfélalkalmazás regisztrálására.

>[!div class="nextstepaction"]
>[Nyilvános ügyfélalkalmazás regisztrálása](tutorial-web-app-public-app-reg.md)
