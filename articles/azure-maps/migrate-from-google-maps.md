---
title: Oktatóanyag – Migrálás a Google Mapsből a Azure Mapsba | Microsoft Azure térképek
description: Útmutató a Google Maps szolgáltatásból Microsoft Azure Maps-re való áttelepítéshez. Az útmutató végigvezeti a Azure Maps API-k és SDK-k átváltásának lépésein.
author: rbrundritt
ms.author: richbrun
ms.date: 09/23/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6241f6156b01c3c90f00578ae5416e4e77270930
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100386798"
---
# <a name="tutorial-migrate-from-google-maps-to-azure-maps"></a>Oktatóanyag: Migrálás a Google Maps szolgáltatásból a Azure Mapsba

Ez a cikk azt ismerteti, hogyan telepíthet át webes, mobil-és kiszolgáló-alapú alkalmazásokat a Google Mapsből a Microsoft Azure Maps platformra. Ez az oktatóanyag a Azure Mapsre való áttelepítéshez szükséges összehasonlító kód-mintákat, áttelepítési javaslatokat és ajánlott eljárásokat tartalmazza. Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Magas szintű összehasonlítás a Azure Mapsban elérhető, egyenértékű Google Maps-funkciókkal.
> * Milyen licencelési különbözeteket kell figyelembe venni.
> * Az áttelepítés megtervezése.
> * Hol találhat technikai erőforrásokat és támogatást.

## <a name="prerequisites"></a>Előfeltételek

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).
2. [Azure Maps fiók létrehozása](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Szerezzen be egy elsődleges előfizetési kulcsot](quick-demo-map-app.md#get-the-primary-key-for-your-account), más néven az elsődleges kulcsot vagy az előfizetési kulcsot. A Azure Maps-hitelesítéssel kapcsolatos további információkért lásd: a [Azure Maps hitelesítés kezelése](how-to-manage-authentication.md).

## <a name="azure-maps-platform-overview"></a>Azure Maps platform áttekintése

Azure Maps az összes iparágban hatékony térinformatikai képességeket biztosít a fejlesztőknek. A funkciók a gyakran frissített térképi információkkal vannak becsomagolva, amelyek földrajzi kontextust biztosítanak a webes és a mobil alkalmazások számára. Azure Maps rendelkezik egy Azure-beli API-kompatibilis REST API-készlettel. A REST API-k a térképek renderelését, keresését, útválasztását, forgalmát, időzónáit, földrajzi elhelyezkedését, Geokerítések, leképezési adatokat, időjárási, mobilitási és térbeli műveleteit teszik lehetővé. A műveletek a webes és az Android SDK-k együttes használatával könnyen, rugalmasan és több platformon is elérhetővé teszik a fejlesztést.

## <a name="high-level-platform-comparison"></a>Magas szintű platform-összehasonlítás

A táblázat a Google Maps szolgáltatásainak megfelelő Azure Maps-funkciók magas szintű listáját tartalmazza. Ez a lista nem jeleníti meg az összes Azure Maps funkciót. A további Azure Maps funkciók közé tartoznak a következők: kisegítő lehetőségek, geokerítések, izokrón, térbeli műveletek, közvetlen Térkép csempe-hozzáférés, batch-szolgáltatások és adatlefedettség-összehasonlítás (azaz a képkezelési lefedettség).

| Google Maps szolgáltatás         | Azure Maps támogatás                     |
|-----------------------------|:--------------------------------------:|
| Web SDK                     | ✓                                      |
| Android SDK                 | ✓                                      |
| iOS SDK                     | Tervezve                                |
| REST-szolgáltatás API-k           | ✓                                      |
| Irányok (Útválasztás)        | ✓                                      |
| Távolsági mátrix             | ✓                                      |
| Jogosultságszint                   | ✓ (Előzetes verzió)                            |
| Helymeghatározáshoz (előre/hátra) | ✓                                      |
| Földrajzi hely                 | N/A                                    |
| Legközelebbi utak               | ✓                                      |
| Helyek keresése               | ✓                                      |
| Helyek részletei              | N/A – A webhely & telefonszáma elérhető |
| Fényképek elhelyezése               | N/A                                    |
| Automatikus kiegészítés          | ✓                                      |
| Elérési út                | ✓                                      |
| Sebességkorlátozások                | ✓                                      |
| Statikus térképek                 | ✓                                      |
| Statikus utcai nézet          | N/A                                    |
| Időzóna                   | ✓                                      |
| Maps Embedded API           | N/A                                    |
| Térkép URL-címei                    | N/A                                    |

A Google Maps alapszintű kulcs-alapú hitelesítést biztosít. Azure Maps az alapszintű kulcs-alapú hitelesítés és a Azure Active Directory hitelesítés is rendelkezésre áll. Azure Active Directory hitelesítés nagyobb biztonsági funkciókat biztosít, mint az alapszintű kulcs-alapú hitelesítés.

## <a name="licensing-considerations"></a>Licencelési megfontolások

Ha a Google Maps szolgáltatásból Azure Mapsra végez áttelepítést, vegye figyelembe a következő szempontokat a licenceléssel kapcsolatban:.

* Az interaktív térképek használatának Azure Maps díja, amely a betöltött Térkép csempék számától függ. Másfelől a Google a Térkép vezérlőelem betöltésének díját is leképezi. Az interaktív Azure Maps SDK-k esetében a Térkép csempéi automatikusan gyorsítótárazva vannak, hogy csökkentsék a fejlesztési költségeket. Egy Azure Maps tranzakció jön létre minden betöltött 15 Térkép csempéhez. Az interaktív Azure Maps SDK-k 512 képpont méretű csempéket használnak, és átlagosan csak egy vagy kevesebb tranzakciót állítanak ki oldalanként.
* Az Azure Maps web SDK-val gyakran a Google Maps webszolgáltatásokból származó statikus térképi rendszerképek cseréjére is költséghatékony. A Azure Maps web SDK Térkép csempéket használ. Kivéve, ha a felhasználó serpenyőben és kicsinyíti a térképet, a szolgáltatás gyakran csak egy tranzakció töredékét generálja a leképezések terhelése alapján. A Azure Maps web SDK-val lehetőség van a pásztázás és a nagyítás letiltására, ha szükséges. Emellett a Azure Maps web SDK sokkal több vizualizációs lehetőséget kínál, mint a statikus Térkép webszolgáltatása.
* Azure Maps lehetővé teszi, hogy a platformról származó adatok az Azure-ban legyenek tárolva. Emellett a használati [feltételeknek](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46)megfelelően akár hat hónapig is gyorsítótárazhatja az adatmennyiséget.

Íme néhány kapcsolódó erőforrás a Azure Mapshoz:

* [Azure Maps díjszabási oldala](https://azure.microsoft.com/pricing/details/azure-maps/)
* [Azure-díjkalkulátor](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
* [Azure Maps használati idő](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (a Microsoft Online Services használati feltételeiben szerepel)
* [Válassza ki a megfelelő díjszabási szintet Azure Maps](./choose-pricing-tier.md)

## <a name="suggested-migration-plan"></a>Javasolt áttelepítési terv

A következő egy magas szintű áttelepítési terv.

1. Az alkalmazás által használt Google Maps SDK-k és szolgáltatások leltárba vétele. Ellenőrizze, hogy a Azure Maps biztosít-e alternatív SDK-kat és szolgáltatásokat.
2. Ha még nem rendelkezik ilyennel, hozzon létre egy Azure-előfizetést a következő címen: [https://azure.com](https://azure.com) .
3. Hozzon létre egy Azure Maps fiókot ([dokumentációt](./how-to-manage-account-keys.md)) és hitelesítési kulcsot, vagy Azure Active Directory ([dokumentáció](./how-to-manage-authentication.md)).
4. Telepítse át az alkalmazás kódját.
5. Tesztelje az áttelepített alkalmazást.
6. Telepítse az áttelepített alkalmazást éles környezetbe.

## <a name="create-an-azure-maps-account"></a>Azure Maps-fiók létrehozása

Azure Maps fiók létrehozásához és a Azure Maps platform eléréséhez kövesse az alábbi lépéseket:

1. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).
2. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
3. Hozzon létre egy [Azure Maps fiókot](./how-to-manage-account-keys.md). 
4. [Szerezze be a Azure Maps előfizetési kulcsát](./how-to-manage-authentication.md#view-authentication-details) , vagy beállíthatja Azure Active Directory hitelesítését a fokozott biztonság eléréséhez.

## <a name="azure-maps-technical-resources"></a>Technikai erőforrások Azure Maps

Az alábbi lista a Azure Maps hasznos technikai erőforrásait sorolja fel.

- Áttekintés [https://azure.com/maps](https://azure.com/maps)
- Dokumentáció [https://aka.ms/AzureMapsDocs](./index.yml)
- Web SDK-kód minták: [https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- Fejlesztői fórumok: [https://aka.ms/AzureMapsForums](/answers/topics/azure-maps.html)
- Videók [https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Blog [https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Technikai blog: [https://aka.ms/AzureMapsTechBlog](https://aka.ms/AzureMapsTechBlog)
- Azure Maps visszajelzés (UserVoice): [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)
- [Azure Maps Jupyter Notebook](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)

## <a name="migration-support"></a>Migrálás támogatása

A fejlesztők a [fórumok](/answers/topics/azure-maps.html) vagy a számos Azure-támogatási lehetőség közül választhatják a Migrálás támogatását: [https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Nincs kitakarítható erőforrás.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan telepítheti át Google Maps-alkalmazását a következő cikkekkel:

> [!div class="nextstepaction"]
> [Webalkalmazás migrálása](migrate-from-google-maps-web-app.md)
