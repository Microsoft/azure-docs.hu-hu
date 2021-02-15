---
title: 'Oktatóanyag: Migrálás a Bing Mapsből a Azure Mapsba | Microsoft Azure térképek'
description: Útmutató a Bing Maps rendszerről Microsoft Azure Maps-re való áttelepítéshez. Az útmutató végigvezeti a Azure Maps API-k és SDK-k átváltásának lépésein.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 9bd0516889733a666bf15668cffd124dcc468f3e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388957"
---
# <a name="tutorial-migrate-from-bing-maps-to-azure-maps"></a>Oktatóanyag: Migrálás a Bing Mapsből a Azure Mapsba

Ebből az útmutatóból megtudhatja, hogyan telepíthet át webes, mobil-és kiszolgáló-alapú alkalmazásokat a Bing Maps-ből a Azure Maps platformra. Ez az útmutató a Azure Mapsre való áttelepítéshez szükséges összehasonlító kód mintákat, áttelepítési javaslatokat és ajánlott eljárásokat tartalmazza. 

Ezen oktatóanyag segítségével elsajátíthatja a következőket:

> [!div class="checklist"]
> * Magas szintű összehasonlítás a Azure Mapsban elérhető egyenértékű Bing Maps-funkciókhoz.
> * Milyen licencelési különbözeteket kell figyelembe venni.
> * Az áttelepítés megtervezése.
> * Hol találhat technikai erőforrásokat és támogatást.

## <a name="prerequisites"></a>Előfeltételek

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).
2. [Azure Maps fiók létrehozása](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Szerezzen be egy elsődleges előfizetési kulcsot](quick-demo-map-app.md#get-the-primary-key-for-your-account), más néven az elsődleges kulcsot vagy az előfizetési kulcsot. A Azure Maps-hitelesítéssel kapcsolatos további információkért lásd: a [Azure Maps hitelesítés kezelése](how-to-manage-authentication.md).

## <a name="azure-maps-platform-overview"></a>Azure Maps platform áttekintése

Azure Maps az összes iparágból álló, hatékony térinformatikai képességekkel rendelkező fejlesztőket biztosít, amelyekkel a webes és mobil alkalmazások földrajzi kontextusának biztosításához elérhető legfrissebb leképezési adatok állnak rendelkezésre. A Azure Maps egy API-kompatibilis REST API-készlet a Maps, a Search, az Útválasztás, a Traffic, az időzóna, a Geokerítések, az adatok leképezése, az időjárási adatok és számos további szolgáltatáshoz, valamint a webes és az Android SDK-k segítségével, így könnyen, rugalmasan és több platformon is elérhetővé teheti a fejlesztést. [A Azure Maps Power bi is elérhető](power-bi-visual-getting-started.md).

## <a name="high-level-platform-comparison"></a>Magas szintű platform-összehasonlítás

Az alábbi táblázat a Bing Maps szolgáltatásainak magas szintű listáját és a Azure Maps szolgáltatásainak relatív támogatását tartalmazza. Ez a lista nem tartalmaz olyan további Azure Maps szolgáltatásokat, mint a kisegítő lehetőségek, a geokerítések API-k, a forgalmi szolgáltatások, a térbeli műveletek, a közvetlen Térkép csempék és a Batch-szolgáltatások.

| Bing Maps szolgáltatás                     | Azure Maps támogatás |
|---------------------------------------|:------------------:|
| Web SDK                               | ✓                  |
| Android SDK                           | ✓                  |
| iOS SDK                               | Tervezve            |
| UWP SDK                               | N/A                 |
| WPF SDK                               | N/A                 |
| REST-szolgáltatás API-k                     | ✓                  |
| Automatikus kiegészítés                           | ✓                  |
| Irányok (beleértve a teherautót)          | ✓                  |
| Távolsági mátrix                       | ✓                  |
| Emelései                            | ✓ (Előzetes verzió)        |
| Képek – statikus Térkép                  | ✓                  |
| Képi metaadatok                      | ✓                  |
| Izokrón                            | ✓                  |
| Local Insights                        | ✓                  |
| Helyi keresés                          | ✓                  |
| Hely felismerése                  | ✓                  |
| Helyszínek (továbbítás/fordított helymeghatározáshoz) | ✓                  |
| Optimalizált útvonal-útvonalak            | Tervezve            |
| Illesztés az utakhoz                         | ✓                  |
| Térbeli Data Services (SDS)           | Részleges            |
| Időzóna                             | ✓                  |
| Forgalmi incidensek                     | ✓                  |
| Konfigurációs vezérelt térképek             | N/A                |

A Bing Maps alapvető kulcs-alapú hitelesítést biztosít. Azure Maps az alapszintű kulcs-alapú hitelesítést és a nagyon biztonságos Azure Active Directory hitelesítést is biztosítja.

## <a name="licensing-considerations"></a>Licencelési megfontolások

Ha a Bing Maps Azure Mapsra való áttelepítéskor a rendszer a következő információkat tekinti át a licenceléssel kapcsolatban.

* Az interaktív leképezések használatának Azure Maps díja a betöltött térképi csempék száma alapján, míg a Bing Maps a Térkép vezérlőelem (Sessions) betöltésének díját számítja fel. A fejlesztők költségeinek csökkentése érdekében Azure Maps automatikusan gyorsítótárazza a Térkép csempéit. Egy Azure Maps tranzakció jön létre minden betöltött 15 Térkép csempéhez. Az interaktív Azure Maps SDK-k 512 képpont méretű csempéket használnak, és átlagosan egy vagy kevesebb tranzakciót állítanak elő oldalanként.

* Azure Maps lehetővé teszi, hogy a platformról származó adatok az Azure-ban legyenek tárolva. Ezen kívül a használati [feltételek](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)alapján legfeljebb hat hónapig lehet gyorsítótárazni.

Íme néhány, a Azure Maps licenceléssel kapcsolatos erőforrása:

-   [Azure Maps díjszabási oldala](https://azure.microsoft.com/pricing/details/azure-maps/)
-   [Azure-díjkalkulátor](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
-   [Azure Maps használati idő](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (a Microsoft Online Services használati feltételeiben szerepel)
-   [Válassza ki a megfelelő díjszabási szintet Azure Maps](./choose-pricing-tier.md)

## <a name="suggested-migration-plan"></a>Javasolt áttelepítési terv

Íme egy példa egy magas szintű áttelepítési tervre.

1.  Készítsen leltárt arról, hogy az alkalmazás milyen Bing Maps SDK-kat és szolgáltatásokat használ, és ellenőrizze, hogy Azure Maps biztosít-e alternatív SDK-kat és-szolgáltatásokat, hogy áttelepítsen.
2.  Hozzon létre egy Azure-előfizetést (ha még nem rendelkezik ilyennel) a következő címen: <https://azure.com> .
3.  Hozzon létre egy Azure Maps fiókot ([dokumentációt](./how-to-manage-account-keys.md)) és hitelesítési kulcsot, vagy Azure Active Directory ([dokumentáció](./how-to-manage-authentication.md)).
4.  Telepítse át az alkalmazás kódját.
5.  Tesztelje az áttelepített alkalmazást.
6.  Telepítse az áttelepített alkalmazást éles környezetbe.

## <a name="create-an-azure-maps-account"></a>Azure Maps-fiók létrehozása

Azure Maps fiók létrehozásához és a Azure Maps platform eléréséhez kövesse az alábbi lépéseket:

1. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).
2. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
3. Hozzon létre egy [Azure Maps fiókot](./how-to-manage-account-keys.md).
4. [Szerezze be a Azure Maps előfizetési kulcsát](./how-to-manage-authentication.md#view-authentication-details) , vagy beállíthatja Azure Active Directory hitelesítését a fokozott biztonság eléréséhez.

## <a name="azure-maps-technical-resources"></a>Technikai erőforrások Azure Maps

Az alábbi lista a Azure Maps hasznos technikai erőforrásait sorolja fel.

* Áttekintés: <https://azure.com/maps>
* Dokumentáció <https://aka.ms/AzureMapsDocs>
* Web SDK-kód minták: <https://aka.ms/AzureMapsSamples>
* Fejlesztői fórumok: <https://aka.ms/AzureMapsForums>
* Videók <https://aka.ms/AzureMapsVideos>
* Blog <https://aka.ms/AzureMapsBlog>
* Azure Maps visszajelzés (UserVoice): <https://aka.ms/AzureMapsFeedback>

## <a name="migration-support"></a>Migrálás támogatása

A fejlesztők a [fórumok](/answers/topics/azure-maps.html) vagy a számos Azure-támogatási lehetőség közül választhatják a Migrálás támogatását: <https://azure.microsoft.com/support/options/>

## <a name="new-terminology"></a>Új terminológia

A következő lista a Bing Maps általános kifejezéseit és a hozzájuk tartozó Azure Mapsi feltételeket tartalmazza.

| Bing Maps-kifejezés                    | Azure Maps kifejezés                                                |
|-----------------------------------|----------------------------------------------------------------|
| Légi                            | Műhold vagy antenna                                            |
| Útmutatás                        | Más néven útválasztás lehet.                             |
| Entitások                          | Geometriák vagy funkciók                                         |
| `EntityCollection`                | Adatforrás vagy réteg                                           |
| `Geopoint`                        | Pozíció                                                       |
| `GeoXML`                          | XML-fájlok a térbeli IO-modulban                             |
| Felszín alatti átfedés                    | Képréteg                                                    |
| Hibrid (a Térkép típusának hivatkozása) | Szatellit utakkal                                           |
| Bezárása                           | Felugró ablak                                                          |
| Hely                          | Pozíció                                                       |
| `LocationRect`                    | Határolókeret                                                   |
| Leképezés típusa                          | Térkép stílusa                                                      |
| Navigációs sáv                    | Térkép stílusa Picker, nagyítás vezérlő, pitch Control, iránytű vezérlő |
| Gombostű                           | Buborék réteg, szimbólum réteg vagy HTML-jelölő                      |

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Nincsenek karbantartást igénylő erőforrások.

## <a name="next-steps"></a>Következő lépések

Ismerje meg a Bing Maps-alkalmazásnak a következő cikkekkel való áttelepítésének részleteit:

> [!div class="nextstepaction"]
> [Webalkalmazás migrálása](migrate-from-bing-maps-web-app.md)
