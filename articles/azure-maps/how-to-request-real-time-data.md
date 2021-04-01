---
title: A Microsoft Azure Maps mobilitási szolgáltatásokkal (előzetes verzió) kapcsolatos valós idejű nyilvános adatátviteli kérelmek igénylése
description: 'Megtudhatja, hogyan kérhet valós idejű nyilvános adatátviteli információkat, például a beérkezőket egy továbbítási leállásban. További információ: a Azure Maps mobilitási szolgáltatások (előzetes verzió) használata erre a célra.'
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d3e3dc4b0e3bc64a38856da8344583b744ea62b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96906046"
---
# <a name="request-real-time-public-transit-data-using-the-azure-maps-mobility-services-preview"></a>A Azure Maps mobilitási szolgáltatások (előzetes verzió) használatával valós idejű nyilvános adatátviteli kérelmeket igényelhet 

> [!IMPORTANT]
> Azure Maps mobilitási szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Ebből a cikkből megtudhatja, hogyan használhatja a Azure Maps [mobilitási szolgáltatásokat](/rest/api/maps/mobility) a valós idejű nyilvános adatforgalom igényléséhez.

Ebből a cikkből megtudhatja, hogyan kérheti le a következő valós idejű beérkezést az adott leállítás során beérkező összes sorra

## <a name="prerequisites"></a>Előfeltételek

Először rendelkeznie kell egy Azure Maps-fiókkal és egy előfizetési kulccsal, hogy bármilyen hívást lehessen kezdeményezni a Azure Maps nyilvános átviteli API-khoz. További információért kövesse a [fiók létrehozása](quick-demo-map-app.md#create-an-azure-maps-account) Azure Maps fiók létrehozásához című témakör utasításait. A fiók elsődleges kulcsának beszerzéséhez kövesse az [elsődleges kulcs beolvasása](quick-demo-map-app.md#get-the-primary-key-for-your-account) című témakör lépéseit. A Azure Maps-hitelesítéssel kapcsolatos további információkért lásd: a [Azure Maps hitelesítés kezelése](./how-to-manage-authentication.md).

Ez a cikk a [Poster alkalmazást](https://www.getpostman.com/apps) használja a REST-hívások létrehozásához. Bármilyen, Ön által előnyben részesített API-fejlesztési környezetet használhat.

## <a name="request-real-time-arrivals-for-a-stop"></a>Valós idejű érkezések igénylése leállításhoz

Egy adott nyilvános továbbítási leálláshoz tartozó valós idejű beérkező adatok igényléséhez a Azure Maps [mobilitási szolgáltatás (előzetes verzió)](/rest/api/maps/mobility) [valós idejű beérkezési API-](/rest/api/maps/mobility/getrealtimearrivalspreview) ját kell kérnie. A kérelem elvégzéséhez szüksége lesz a **metroID** és a **stopID** . Ha többet szeretne megtudni ezekről a paraméterekről, tekintse meg a [nyilvános átviteli útvonalak igénylését](./how-to-request-transit-data.md)ismertető útmutatót.

A "522" a Metro ID-ként használjuk, amely a "Seattle – Tacoma – Bellevue, WA" területen található Metro-azonosító. A "522---2060603" érték leállítási AZONOSÍTÓként való használata esetén ez a buszmegálló a következő címen érhető el: "ne 24 St & 162nd Ave ne, Bellevue WA". Ha a következő öt valós idejű beérkező adatbevitelt szeretné kérelmezni, az összes következő élő érkezésnél végezze el a következő lépéseket:

1. Nyissa meg a Poster alkalmazást, és hozzon létre egy gyűjteményt a kérések tárolásához. A Poster alkalmazás teteje közelében válassza az **új** lehetőséget. Az **új létrehozása** ablakban válassza a **gyűjtemény** elemet.  Nevezze el a gyűjteményt, és válassza a **Létrehozás** gombot.

2. A kérelem létrehozásához válassza az **új** újra lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem** lehetőséget. Adja meg a kérelem **nevét** . Válassza ki azt a gyűjteményt, amelyet az előző lépésben hozott létre, a kérelem mentési helyeként. Ezután válassza a **Mentés** lehetőséget.

    ![Kérelem létrehozása a Poster-ban](./media/how-to-request-transit-data/postman-new.png)

3. Válassza a http **beolvasása** metódust a Builder (szerkesztő) lapon, majd a Get kérelem létrehozásához adja meg a következő URL-címet. Cserélje le a értékét a `{subscription-key}` Azure Maps elsődleges kulcsára.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Sikeres kérés után a következő válasz jelenik meg.  Figyelje meg, hogy a "Scheduletype értéke" paraméter határozza meg, hogy a becsült beérkezési idő valós idejű vagy statikus adatértéken alapul-e.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 8,
                "scheduleType": "realTime",
                "patternId": "522---4143196",
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 25,
                "scheduleType": "realTime",
                "patternId": "522---3510227",
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan kérhet továbbítási információkat a mobilitási szolgáltatások (előzetes verzió) használatával:

> [!div class="nextstepaction"]
> [Adatátviteli adatkérés](how-to-request-transit-data.md)

Ismerje meg a Azure Maps mobilitási szolgáltatások (előzetes verzió) API dokumentációját:

> [!div class="nextstepaction"]
> [A mobilitási szolgáltatások API dokumentációja](/rest/api/maps/mobility)