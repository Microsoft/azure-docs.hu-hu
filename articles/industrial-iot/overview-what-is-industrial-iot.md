---
title: Az Azure Ipari IoT áttekintése
description: Ez a cikk áttekintést nyújt az ipari IoT-ről. Ismerteti az IIoT üzletház-kapcsolatát és biztonsági összetevőit.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 3/22/2021
ms.openlocfilehash: ce582f810f483f2e5d3fdda2c3379ecad3842d51
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813271"
---
# <a name="what-is-industrial-iot-iiot"></a>Mi az az ipari IoT (IIoT)?

![Ipari iot](media/overview-what-is-Industrial-IoT/icon-255-px.png)

Microsoft Azure Industrial eszközök internetes hálózata (IIoT) olyan Azure-modulok és -szolgáltatások csomagja, amelyek a felhő erejét integrálják az ipari és gyártási üzletpadlókba. Az iparági szabványnak megfelelő nyílt felületek, például a nyílt platformon működő kommunikációs egyesített architektúra [(OPC UA)](https://opcfoundation.org/about/opc-technologies/opc-ua/)használatával az Azure IIoT lehetővé teszi, hogy az eszközökből és érzékelőkből származó adatokat integrálja az Azure-felhőbe , beleértve azokat is, amelyek már a gyár területén üzemelnek. Az adatok felhőbeli használatával gyorsabban és rugalmasan használhatók visszajelzésként az átalakító üzleti és ipari folyamatok fejlesztéséhez.

## <a name="discover-register-and-manage-your-industrial-assets-with-azure"></a>Ipari eszközök felderítése, regisztrálása és kezelése az Azure-ban

Az Ipari Azure IoT lehetővé teszi a gyárüzemeltetők számára, hogy opC UA-kompatibilis kiszolgálókat derítsnek fel egy gyári hálózaton, és regisztrálják Azure IoT Hub. Az üzemeltetési munkatársak a világ bármely tájáról előfizetnek a gyári eseményekre, reagálnak rájuk, riasztásokat és riasztásokat kapnak, és valós időben reagálnak rájuk.

Az IIoT olyan mikroszolgáltatásokat biztosít, amelyek OPC UA-funkciókat valósítanak meg. A Mikroszolgáltatások REST API-k az OPC UA-szolgáltatások peremoldali tükrözött részét tükrözik. Ezeket OAUTH-hitelesítéssel és hitelesítéssel biztosítjuk, Azure Active Directory (AAD). A felhőalkalmazások így tallózhatnak a kiszolgáló címterei között vagy olvashatnak/írhatnak változókat, és metódusokat hajthatnak végre HTTPS és egyszerű OPC UA JSON hasznos kódot használva. A peremhálózati szolgáltatások Azure IoT Edge, és a helyszínen futnak. A felhőalapú mikroszolgáltatások REST-ASP.NET-alapú mikroszolgáltatásokként vannak megvalósítva, és felügyelt Azure Kubernetes-szolgáltatásokon vagy önálló virtuális Azure App Service. A peremhálózati és a felhőszolgáltatásokhoz is az IIoT előre felépített Docker-tárolókat biztosít a Microsoft Container Registry (MCR) szolgáltatásban, ezzel eltávolítva ezt a lépést az ügyfél számára. A peremhálózat és a felhőszolgáltatások egymással együtt használják egymást. Az IIoT könnyen használható üzembe helyezési szkripteket is biztosít, amelyek lehetővé teszik, hogy egyetlen paranccsal üzembe helyezzék a teljes platformot.

Emellett a REST API-k bármely programozási nyelvvel használhatók a elérhető Open API-specifikáción (Swaggeren) keresztül. Ez azt jelenti, hogy amikor az OPC UA-t felhőalapú felügyeleti megoldásokba integrálják, a fejlesztők szabadon választhatnak olyan technológiát, amely megfelel a készségeiknek, érdeklődési körüknek és architektúrájuknak. Például egy teljes körű webfejlesztő, aki egy riasztáshoz és egy esemény-irányítópulthoz fejleszt egy alkalmazást, olyan logikát írhat, amely JavaScript- vagy TypeScript-eseményekre reagál anélkül, hogy az OPC UA SDK-ra, C-ra, C++-ra, Javára vagy C#-ra kellene felpukkodni.

## <a name="manage-certificates-and-trust-groups"></a>Tanúsítványok és megbízhatósági csoportok kezelése

Az Ipari Azure IoT az OPC UA-alkalmazástanúsítványokat és a gyári gépek és vezérlőrendszerek megbízhatósági listáit kezeli az OPC UA-ügyfél és a kiszolgálói kommunikáció biztonságának biztosítása érdekében. Ez korlátozza, hogy mely ügyfél számára engedélyezett a kiszolgálóval való beszélgetés. A titkos kulcsok tárolását és a tanúsítványok aláírását Azure Key Vault, amely támogatja a hardveralapú biztonságot (HSM).

## <a name="industrial-iot-components"></a>Ipari IoT-összetevők

Az Azure IIoT-megoldások adott összetevőkből épülnek fel. Ezek közé tartoznak a következők.

- **Legalább egy Azure IoT Hub.**
- **IoT Edge eszközök.**
- **Ipari peremhálózati modulok.**

### <a name="iot-hub"></a>IoT Hub
A [Azure IoT Hub]( központi üzenetközpontként működik az IoT-alkalmazások és az azok által felügyelt eszközök közötti biztonságos, kétirányú https://azure.microsoft.com/services/iot-hub/ kommunikációhoz. Ez egy nyílt és rugalmas felhőalapú platformszolgáltatás (PaaS), amely támogatja a nyílt forráskódú SZOFTVERDK-okat és több protokollt. 

Ipari és üzleti adatainak gyűjtése egy IoT Hub segítségével biztonságosan tárolhatja az adatokat, üzleti és hatékonysági elemzéseket végezhet rajta, és jelentéseket hozhat létre az adatokból. A konszolidált adatokra Microsoft Azure szolgáltatásokat és [](https://powerbi.microsoft.com)eszközöket is alkalmazhat, például Power BI szolgáltatásokat és eszközöket.

### <a name="iot-edge-devices"></a>IoT Edge eszközök
A [peremhálózati szolgáltatások](https://azure.microsoft.com/services/iot-edge/) Azure IoT Edge modulokként vannak megvalósítva, és a helyszínen futnak. A felhőalapú mikroszolgáltatások REST-ASP.NET-alapú mikroszolgáltatásokként vannak megvalósítva, és felügyelt Azure Kubernetes-szolgáltatásokon vagy önálló virtuális Azure App Service. A peremhálózati és a felhőszolgáltatásokhoz előre felépített Docker-tárolókat biztosítunk a Microsoft Container Registry (MCR) szolgáltatásban, ezzel eltávolítva ezt a lépést az ügyfél számára. A peremhálózati és a felhőszolgáltatások használják egymást, és együtt kell használni őket. Könnyen használható üzembe helyezési szkripteket is biztosítunk, amelyek lehetővé teszik, hogy egyetlen paranccsal üzembe helyezzék a teljes platformot.

A IoT Edge edge-modulokból és Edge-modulokból állnak.
- **Az Edge-modulok** docker-tárolók, amelyek a számítási műveletek legkisebb egységei, például az OPC-közzétevő és az OPC-iker. 
- **Az Edge-eszköz** az OPC UA-kiszolgáló és a felhőbeli virtuális gép közötti közvetítőként IoT Hub modulok üzembe helyezésére szolgál.

### <a name="industrial-edge-modules"></a>Ipari peremhálózati modulok
- **OPC-közzétevő:** Az OPC-közzétevő a IoT Edge. OPC UA-kiszolgálókhoz csatlakozik, és OPC UA "Pub/Sub" formátumban közzétesz ezekről a kiszolgálókról JSON-kódolású telemetriai adatokat a Azure IoT Hub. A Azure IoT Hub SDK által támogatott átviteli protokollok használhatók, például HTTPS, AMQP és MQTT.
- **OPC-iker:** Az OPC-iker olyan mikroszolgáltatásokból áll, amelyek Azure IoT Edge és IoT Hub a felhő és a gyári hálózat csatlakoztatásához. Az OPC-ikereszköz REST API-kon keresztül biztosítja az ipari eszközök felderítését, regisztrációját és távvezérlését. Az OPC-ikereszköz nem igényel OPC unified Architecture (OPC UA) SDK-t. Programozási nyelvtől független, és egy kiszolgáló nélküli munkafolyamatban is része lehet.
- **Felderítés:** A felderítési modul, amelyet a felderítési identitás képvisel, felderítési szolgáltatásokat biztosít a peremhálózaton, például az OPC UA-kiszolgálófelderítést. Ha a felderítés be van állítva és engedélyezve van, a modul elküldi a vizsgálat eredményeit a IoT Edge és IoT Hub telemetriai útvonalat az onboarding service-nek. A szolgáltatás feldolgozza az eredményeket, és frissíti a regisztrációs adatbázisban az összes kapcsolódó identitást.

## <a name="next-steps"></a>Következő lépések
Most, hogy már tudja, mi az az ipari IoT, elolvashatja az ipari IoT-platformot és az OPC-közzétevőt:

> [!div class="nextstepaction"]
> [Mi az az OPC-közzétevő?](overview-what-is-opc-publisher.md)