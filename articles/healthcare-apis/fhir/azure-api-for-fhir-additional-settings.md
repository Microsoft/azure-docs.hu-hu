---
title: További beállítások a FHIR készült Azure API-hoz
description: A FHIR készült Azure API-hoz kapcsolódó további beállítások áttekintése
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: matjazl
ms.author: cavoeg
author: CaitlinV39
ms.date: 11/22/2019
ms.openlocfilehash: 23c4fb6be4e30e78faa1ce411037f828d6518f50
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019113"
---
# <a name="additional-settings-for-azure-api-for-fhir"></a>További beállítások a FHIR készült Azure API-hoz

Ebben a útmutatóban áttekintjük a további beállításokat, amelyeket a FHIR Azure API-ban szeretne beállítani. További lapokat is talál, amelyek részletesen részletezik a részleteket.

## <a name="configure-database-settings"></a>Adatbázis-beállítások konfigurálása

Az Azure API for FHIR az adatbázis használatával tárolja az adattárolási adatbázist. Az alapul szolgáló adatbázis teljesítménye attól függ, hogy a szolgáltatás üzembe helyezése vagy a szolgáltatás üzembe helyezése után hány kérési egység (RU) van kiválasztva.

Az átviteli sebességet úgy kell kiépíteni, hogy mindig elegendő rendszererőforrás álljon rendelkezésre az adatbázishoz. Az alkalmazástól függően hány RUs szükséges a végrehajtott műveletekhez. A műveletek az egyszerű olvasás és írások között összetettebb lekérdezésekre is terjedhetnek.

További információ az alapértelmezett beállítások módosításáról: az [adatbázis beállításainak konfigurálása](configure-database.md).

## <a name="access-control"></a>Hozzáférés-vezérlés

A FHIR készült Azure API csak a jogosult felhasználók számára engedélyezi a FHIR API elérését. A jogosultsággal rendelkező felhasználókat két különböző mechanizmuson keresztül állíthatja be. A hozzáférés-vezérlés konfigurálásának elsődleges és ajánlott módja az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/index.yml)használata, amely a **hozzáférés-vezérlés (iam)** panelen érhető el. Az Azure RBAC csak akkor működik, ha az előfizetéshez társított Azure Active Directory Bérlővel szeretné védeni az adatsíkok elérését. Ha másik bérlőt szeretne használni, a FHIR készült Azure API helyi FHIR-adatközpont-hozzáférés-vezérlési mechanizmust kínál. A konfigurációs beállítások nem annyira gazdagok, mint a helyi RBAC mechanizmus használatakor. Részletekért válasszon a következő lehetőségek közül:

* [Azure-RBAC a FHIR-adatsíkon](configure-azure-rbac.md). Ez az előnyben részesített lehetőség, ha az előfizetéshez társított Azure Active Directory bérlőt használja.
* [Helyi FHIR adatsíkok hozzáférés-vezérlése](configure-local-rbac.md). Ezt a lehetőséget csak akkor használja, ha külső Azure Active Directory bérlőt kell használnia az adatsík hozzáférés-vezérléséhez. 

## <a name="enable-diagnostic-logging"></a>Diagnosztikai naplózás engedélyezése
Előfordulhat, hogy a telepítés részeként engedélyezni szeretné a diagnosztikai naplózást, hogy képes legyen figyelni a szolgáltatást, és pontos jelentéskészítéssel rendelkezzen a megfelelőség szempontjából. A diagnosztikai naplózás beállításával kapcsolatos részletekért tekintse meg a következő témakört: [útmutató](enable-diagnostic-logging.md) a diagnosztikai naplózás beállításához, valamint néhány minta lekérdezéshez. 

## <a name="use-custom-headers-to-add-data-to-audit-logs"></a>Az egyéni fejlécek használata a naplókba való adathozzáadáshoz
Előfordulhat, hogy a FHIR készült Azure API-ban további információkat szeretne szerepeltetni a naplófájlokban, amelyek a hívó rendszerből származnak. Ehhez az információhoz egyéni fejléceket is használhat.

A különböző típusú információk rögzítéséhez egyéni fejléceket használhat. Például:

* Identitás-vagy engedélyezési információk
* A hívó forrása
* Kezdeményező szervezet
* Ügyfélrendszer részletei (elektronikus egészségügyi rekord, beteg-portál)

Ha ezeket az adatnaplókat szeretné felvenni a naplókba, tekintse meg az [egyéni HTTP-fejlécek használata az adatgyűjtés naplózása a naplókhoz](use-custom-headers.md) útmutató című témakört.

## <a name="next-steps"></a>Következő lépések

Ebben a útmutatóban további beállításokat állíthat be a FHIR készült Azure API-hoz.

Ezután tekintse át a FHIR-adatokat olvasó webalkalmazások létrehozásához szükséges oktatóanyagok sorozatát.

>[!div class="nextstepaction"]
>[JavaScript-alkalmazás üzembe helyezése](tutorial-web-app-fhir-server.md)