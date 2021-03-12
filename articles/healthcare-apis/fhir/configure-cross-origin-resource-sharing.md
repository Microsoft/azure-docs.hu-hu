---
title: A FHIR-hez készült Azure API-ban az idegen eredetű erőforrások megosztásának konfigurálása
description: Ez a cikk azt ismerteti, hogyan konfigurálható az FHIR-hez készült Azure API-ban az idegen eredetű erőforrások megosztása.
author: matjazl
ms.author: matjazl
ms.date: 3/11/2019
ms.topic: reference
ms.service: healthcare-apis
ms.subservice: fhir
ms.openlocfilehash: dc4c034b0821f1fe5ecb940591fca77d61edc3ce
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018830"
---
# <a name="configure-cross-origin-resource-sharing-in-azure-api-for-fhir"></a>A FHIR-hez készült Azure API-ban az idegen eredetű erőforrások megosztásának konfigurálása

Az Azure API a gyors egészségügyi együttműködési erőforrások (FHIR) támogatja a [több eredetű erőforrás-megosztást (CORS)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing). A CORS lehetővé teszi a beállítások konfigurálását, hogy az egyik tartományból (forrás) származó alkalmazások hozzáférhessenek egy másik tartomány erőforrásaihoz, más néven a tartományok közötti kérelemhez.

A CORS gyakran használják egy egyoldalas alkalmazásban, amelynek egy REST API-t kell meghívnia egy másik tartományba.

Ha a FHIR készült Azure API-ban szeretné konfigurálni a CORS beállítást, adja meg a következő beállításokat:

- **Eredet (hozzáférés-vezérlés-engedélyezés-forrás)**. Azoknak a tartományoknak a listája, amelyek számára engedélyezett a FHIR Azure API-hoz való átállási kérések elvégzése. Minden tartományt (forrás) külön sorban kell megadni. Megadhat egy csillagot (*), amely lehetővé teszi a hívások bármely tartományból való használatát, de nem ajánlott, mert biztonsági kockázatot jelent.

- **Fejlécek (hozzáférés-vezérlés-engedélyezés-fejlécek)**. Azon fejlécek listája, amelyekben a forrás kérelem szerepelni fog. Az összes fejléc engedélyezéséhez adjon meg egy csillagot (*).

- **Metódusok (hozzáférés-vezérlés-engedélyezés-metódusok)**. Az engedélyezett metódusok (PUT, GET, POST stb.) egy API-hívásban. Válassza az **összes kijelölése** lehetőséget az összes módszernél.

- **Maximális életkor (hozzáférés-vezérlés-Max-Age)**. A hozzáférés-vezérlés – engedélyezés – fejlécek és a hozzáférés-vezérlés – engedélyező metódusok által az elővizsgálati kérések eredményeinek gyorsítótárazására szolgáló érték másodpercben.

- **Hitelesítő adatok engedélyezése (hozzáférés-vezérlés-engedélyezés-hitelesítő adatok)**. A CORS kérelmek általában nem tartalmaznak cookie-kat a [helyek közötti kérelmek hamisításának (CSRF)](https://en.wikipedia.org/wiki/Cross-site_request_forgery) elleni támadások megelőzésére. Ha ezt a beállítást választja, a kérés elvégezhető a hitelesítő adatok (például a cookie-k) belefoglalásával is. Ez a beállítás nem konfigurálható, ha egy csillag (*) értékkel már beállította az eredetet.

![Több eredetű erőforrás-megosztás (CORS) beállításai](media/cors/cors.png)

>[!NOTE]
>Eltérő beállításokat nem adhat meg különböző tartománybeli eredetekhez. Az összes beállítás (**fejlécek**, **metódusok**, **Maximális életkor** és a **hitelesítő adatok engedélyezése**) az Origins beállításban megadott összes értékre vonatkozik.

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan konfigurálhatja az FHIR-hez készült Azure API-ban az adatforrások közötti megosztást. Ezután telepítsen egy teljes körűen felügyelt Azure API-t a FHIR-hez:
 
>[!div class="nextstepaction"]
>[Az Azure API for FHIR üzembe helyezése](fhir-paas-portal-quickstart.md)