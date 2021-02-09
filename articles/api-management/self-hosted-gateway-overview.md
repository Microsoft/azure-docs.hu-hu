---
title: Saját üzemeltetésű átjáró – áttekintés | Microsoft Docs
description: Ismerje meg, hogy az Azure API Management saját üzemeltetésű átjáró szolgáltatásával hogyan kezelheti a szervezetek az API-kat hibrid és többfelhős környezetekben.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/25/2021
ms.author: apimpm
ms.openlocfilehash: 48abce693ca22163c0a1742ba71faf36fc6156a1
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989097"
---
# <a name="self-hosted-gateway-overview"></a>Saját üzemeltetésű átjáró áttekintése

Ez a cikk azt ismerteti, hogy az Azure API Management saját üzemeltetésű átjáró szolgáltatása hogyan teszi lehetővé a hibrid és a többfelhős API-felügyeletet, bemutatja a magas szintű architektúrát, és kiemeli a képességeit.

## <a name="hybrid-and-multi-cloud-api-management"></a>Hibrid és többfelhős API Management

A saját üzemeltetésű átjáró szolgáltatás kibővíti a hibrid és a többfelhős környezetek API Management támogatását, és lehetővé teszi, hogy a szervezetek hatékonyan és biztonságosan kezeljék a helyszíni és a felhőben üzemeltetett API-kat az Azure egyetlen API Management szolgáltatásával.

A saját üzemeltetésű átjáróval rendelkező ügyfelek rugalmasan telepíthetik az API Management Gateway összetevő egy tárolós verzióját ugyanarra a környezetbe, ahol az API-kat üzemeltetik. Az összes saját üzemeltetésű átjáró felügyelve van a API Management szolgáltatásból, amelyet összevontak, így biztosítva az ügyfelek számára a belső és külső API-k láthatóságát és egységes felügyeleti élményét. Az átjárók az API-khoz való közelsége lehetővé teszi az ügyfeleknek az API-forgalom forgalmának optimalizálását, valamint a biztonsági és megfelelőségi követelmények kielégítését.

Minden API Management szolgáltatás a következő fő összetevőkből áll:

-   Felügyeleti sík, amely API-ként van kitéve a szolgáltatás konfigurálásához a Azure Portal, a PowerShell és más támogatott mechanizmusok használatával.
-   Az átjáró (vagy az adatsík) feladata az API-kérelmek proxyja, a házirendek alkalmazása és a telemetria begyűjtése.
-   Fejlesztői portál a fejlesztők számára az API-k használatának felderítésére, megismerésére és bevezetésére

Alapértelmezés szerint az összes összetevő üzembe helyezése az Azure-ban történik, így az összes API-forgalom (a lenti képen látható folytonos fekete nyíl) az Azure-on keresztül áramlik, függetlenül attól, hogy az API-kat végrehajtó háttérrendszer hol üzemel. A modell működésének egyszerűsége többek között a késés, a megfelelőségi problémák és bizonyos esetekben a további adatátviteli díjak költségeiből származik.

![API-forgalom saját üzemeltetésű átjárók nélkül](media/self-hosted-gateway-overview/without-gateways.png)

A saját üzemeltetésű átjárók üzembe helyezése azonos környezetekben, ahol a háttérbeli API-implementációk futnak, lehetővé teszi, hogy az API-forgalom közvetlenül a háttérbeli API-khoz áramlson, ami javítja a késést, optimalizálja az adatátviteli költségeket, és lehetővé teszi a megfelelőséget, miközben megőrizte az előnyeit, hogy a megvalósításuk helyétől függetlenül egyetlen felügyeleti, a

![API-forgalom önkiszolgáló átjárókkal](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Csomagolás és szolgáltatások

A saját üzemeltetésű átjáró az Azure-ban üzembe helyezett felügyelt átjáró az összes API Management szolgáltatás részeként telepített, funkcionálisan egyenértékű verziója. A saját üzemeltetésű átjáró Linux-alapú Docker- [tárolóként](https://aka.ms/apim/sputnik/dhub) érhető el a Microsoft Container Registry. Üzembe helyezhető a Docker, a Kubernetes, vagy bármely más, a helyszínen, a felhő-infrastruktúrában, illetve a kiértékelési és fejlesztési célokra szolgáló, a személyes számítógépeken futó tároló-előkészítési megoldásban.

A felügyelt átjárókban található következő funkciók **nem érhetők el** a saját üzemeltetésű átjárókban:

- Azure Monitor-naplók
- Felsőbb rétegbeli (háttérbeli) TLS-verzió és titkosítás kezelése
- Kiszolgálói és Ügyféltanúsítványok ellenőrzése a API Management szolgáltatásba feltöltött [hitelesítésszolgáltatói főtanúsítványok](api-management-howto-ca-certificates.md) használatával. További információ: [tanúsítvány ellenőrzése önkiszolgáló átjáróban](api-management-howto-mutual-certificates-for-clients.md#certificate-validation-in-self-hosted-gateway).
- Integráció a [Service Fabric](../service-fabric/service-fabric-api-management-overview.md)
- TLS-munkamenet folytatása
- Ügyféltanúsítvány újraegyeztetése. Ez azt jelenti, hogy a munkahelyi [API-felhasználóknak a kezdeti](api-management-howto-mutual-certificates-for-clients.md) TLS-kézfogás részeként be kell mutatniuk a tanúsítványokat. Ennek biztosításához engedélyezze az ügyféltanúsítvány egyeztetése beállítást a saját üzemeltetésű átjáró egyéni állomásnév beállításakor.
- Beépített gyorsítótár. Ebből a [dokumentumból](api-management-howto-cache-external.md) megtudhatja, hogyan használhatja a külső gyorsítótárat a saját üzemeltetésű átjárókban.

## <a name="connectivity-to-azure"></a>Kapcsolódás az Azure-hoz

A saját üzemeltetésű átjárók a 443-es porton keresztül kimenő TCP/IP-kapcsolatot igényelnek az Azure-hoz. Minden saját üzemeltetésű átjárót egyetlen API Management szolgáltatáshoz kell társítani, és a felügyeleti síkon keresztül kell konfigurálni. A saját üzemeltetésű átjáró az Azure-hoz való kapcsolódást használja a következőhöz:

-   Az állapot jelentése percenkénti szívverési üzenetek küldésével
-   Rendszeres ellenőrzés (10 másodpercenként) és konfigurációs frissítések alkalmazása, ha elérhetők
-   Kérelmek naplóinak és metrikáinak küldése Azure Monitorre, ha erre van konfigurálva
-   Események küldése Application Insightsre, ha erre van beállítva

Ha az Azure-kapcsolat megszakad, a saját üzemeltetésű átjáró nem tudja fogadni a konfigurációs frissítéseket, jelenteni annak állapotát, vagy fel kell töltenie a telemetria.

A saját üzemeltetésű átjáró úgy lett kialakítva, hogy "nem statikus", és képes túlélni az Azure-hoz való kapcsolódás ideiglenes elvesztését. Helyi konfiguráció biztonsági mentéssel vagy anélkül is üzembe helyezhető. A korábbi esetekben a saját üzemeltetésű átjárók rendszeresen mentik a legújabb letöltött konfiguráció biztonsági másolatát a tárolóhoz vagy Pod-hoz csatolt állandó köteten.

Ha a konfiguráció biztonsági mentése ki van kapcsolva, és az Azure-kapcsolat megszakadt:

-   A saját üzemeltetésű átjárók futtatása továbbra is működni fog a konfiguráció memóriában tárolt példányával.
-   A saját üzemeltetésű átjárók leállítása nem fog tudni elindulni

Ha a konfigurációs biztonsági mentés be van kapcsolva, és az Azure-kapcsolat megszakadt:

-   A saját üzemeltetésű átjárók futtatása továbbra is működni fog a konfiguráció memóriában tárolt példányával.
-   A saját üzemeltetésű átjárók leállítása a konfiguráció biztonsági másolatának használatával megkezdhető

Ha a kapcsolat helyreáll, a leállás által érintett összes saját üzemeltetésű átjáró automatikusan újra csatlakozik a társított API Management szolgáltatáshoz, és letölti az összes olyan konfigurációs frissítést, amely az átjáró kapcsolat nélküli üzemmódban történt.

## <a name="next-steps"></a>Következő lépések

-   [A témakör további hátteréről szóló tanulmány elolvasása](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Saját üzemeltetésű átjáró üzembe helyezése a Docker-ben](how-to-deploy-self-hosted-gateway-docker.md)
-   [Saját üzemeltetésű átjáró üzembe helyezése a Kubernetes-ben](how-to-deploy-self-hosted-gateway-kubernetes.md)
