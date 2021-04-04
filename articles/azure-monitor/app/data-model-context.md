---
title: Azure Application Insights Telemetria adatmodell – telemetria-környezet | Microsoft Docs
description: Application Insights telemetria környezeti adatmodellje
ms.topic: conceptual
ms.date: 05/15/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 76f3be510494a1f005b0080ee8f2390a3fbc3622
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91767848"
---
# <a name="telemetry-context-application-insights-data-model"></a>Telemetria kontextus: Application Insights adatmodell

Minden telemetria-tételnek lehet egy szigorúan beírt környezeti mezője. Minden mező egy adott figyelési forgatókönyvet tesz lehetővé. Az egyéni tulajdonságok gyűjtemény használatával egyéni vagy alkalmazás-specifikus környezetfüggő információkat tárolhat.


## <a name="application-version"></a>Alkalmazás verziója

Az alkalmazás környezeti mezőiben szereplő információk mindig a telemetria küldő alkalmazásra mutatnak. Az alkalmazás verziószámával elemezhető az alkalmazás viselkedésének változásai, valamint annak korrelációja a központi telepítésekhez.

Maximális hossz: 1024


## <a name="client-ip-address"></a>Ügyfél IP-címe

Az ügyféleszközök IP-címe. Az IPv4 és az IPv6 protokoll támogatott. Ha a telemetria egy szolgáltatásból küldik el, a hely környezete arról a felhasználóról szól, aki kezdeményezte a műveletet a szolgáltatásban. Application Insights bontsa ki a Geo-hely adatait az ügyfél IP-címéből, majd vágja ki. Így az ügyfél IP-címe önmagában nem használható végfelhasználói azonosításra alkalmas adatokként. 

Maximális hossz: 46


## <a name="device-type"></a>Eszköz típusa

Ezt a mezőt eredetileg az alkalmazás végfelhasználója által használt eszköz típusának jelzésére használták. Napjainkban elsősorban a JavaScript telemetria megkülönböztetni a "böngésző" típusú telemetria a "PC" típusú eszköz használatával.

Maximális hossz: 64


## <a name="operation-id"></a>Művelet azonosítója

A gyökérszintű művelet egyedi azonosítója. Ez az azonosító lehetővé teszi, hogy több összetevőn keresztül csoportosítsa a telemetria. Részletekért lásd: [telemetria korreláció](./correlation.md) . A művelet azonosítóját egy kérelem vagy egy oldal nézet hozza létre. Az összes többi telemetria ezt a mezőt az azt tartalmazó kérelem vagy az oldal nézet értékére állítja be. 

Maximális hossz: 128


## <a name="parent-operation-id"></a>Szülő műveleti azonosító

Az telemetria elem közvetlen szülője egyedi azonosítója. Részletekért lásd: [telemetria korreláció](./correlation.md) .

Maximális hossz: 128


## <a name="operation-name"></a>Művelet neve

A művelet neve (csoport). A művelet nevét egy kérelem vagy egy oldal nézet hozza létre. Az összes többi telemetria-elem ezt a mezőt az azt tartalmazó kérelem vagy az oldal nézet értékére állítja be. A művelet neve a műveletek telemetria összes elemének megkeresésére szolgál (például a "Home/index" LEKÉRDEZÉSsel). Ez a környezeti tulajdonság olyan kérdések megválaszolására szolgál, mint a "mi a leggyakoribb kivételek az adott oldalon."

Maximális hossz: 1024


## <a name="synthetic-source-of-the-operation"></a>A művelet szintetikus forrása

A szintetikus forrás neve. Az alkalmazás egyes telemetria szintetikus forgalmat is jelenthetnek. Előfordulhat, hogy a webes webbejáró indexeli a webhelyet, a webhely rendelkezésre állási tesztjét vagy a nyomkövetést a diagnosztikai könyvtárakból, például a Application Insights SDK-ból.

Maximális hossz: 1024


## <a name="session-id"></a>Munkamenet-azonosító

Munkamenet-azonosító – a felhasználó az alkalmazással való interakciójának példánya. A munkamenet-környezet mezőiben szereplő információk mindig a végfelhasználóra mutatnak. Ha a telemetria egy szolgáltatásból küldik, a munkamenet-környezet arról a felhasználóról szól, aki kezdeményezte a műveletet a szolgáltatásban.

Maximális hossz: 64


## <a name="anonymous-user-id"></a>Névtelen felhasználói azonosító

Névtelen felhasználói azonosító. Az alkalmazás végfelhasználóját jelöli. Ha a telemetria egy szolgáltatásból küldik, a felhasználói környezet arra a felhasználóra szól, aki kezdeményezte a műveletet a szolgáltatásban.

A [mintavétel](./sampling.md) az egyik módszer, amellyel minimálisra csökkenthető a begyűjtött telemetria mennyisége. A mintavételezési algoritmus megkísérli az összes korrelált telemetria vagy annak mintavételezését. A rendszer névtelen felhasználói azonosítót használ a mintavételi pontszám generálásához. Így a névtelen felhasználói azonosítónak elég véletlenszerű értéknek kell lennie. 

Ha névtelen felhasználói azonosítót használ a Felhasználónév tárolására, a mező helytelenül van használva. Hitelesített felhasználói azonosító használata.

Maximális hossz: 128


## <a name="authenticated-user-id"></a>Hitelesített felhasználói azonosító

Hitelesített felhasználói azonosító. A névtelen felhasználói azonosító ellentéte, ez a mező a felhasználót a felhasználóbarát névvel jelöli. Ezt csak a ASP.NET Framework SDK-val gyűjti a rendszer [`AuthenticatedUserIdTelemetryInitializer`](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/WEB/Src/Web/Web/AuthenticatedUserIdTelemetryInitializer.cs) .  

Maximális hossz: 1024


## <a name="account-id"></a>Fiókazonosító

Több-bérlős alkalmazásokban ez a fiók azonosítója vagy neve, amelyet a felhasználó alkalmaz. Ilyen lehet például az előfizetés azonosítója a blogírás platform Azure Portal vagy blogjának nevéhez.

Maximális hossz: 1024


## <a name="cloud-role"></a>Felhőbeli szerepkör

Annak a szerepkörnek a neve, amelyhez az alkalmazás tartozik. Közvetlenül a szerepkör nevére mutat az Azure-ban. Használható a mikro-szolgáltatások megkülönböztetésére is, amelyek egyetlen alkalmazás részét képezik.

Maximális hossz: 256


## <a name="cloud-role-instance"></a>Felhőalapú szerepkör-példány

Annak a példánynak a neve, amelyen az alkalmazás fut. A helyi számítógép neve, az Azure-példány neve.

Maximális hossz: 256


## <a name="internal-sdk-version"></a>Belső: SDK-verzió

SDK-verzió. További információért tekintse meg [ezt a cikket](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/EndpointSpecs/SDK-VERSIONS.md) .

Maximális hossz: 64


## <a name="internal-node-name"></a>Belső: csomópont neve

A mező a számlázási célra használt csomópont nevét jelöli. Ezzel felülbírálhatja a csomópontok normál észlelését.

Maximális hossz: 256


## <a name="next-steps"></a>Következő lépések

- Ismerje meg [, hogyan bővítheti és szűrheti a telemetria](./api-filtering-sampling.md).
- Lásd: [adatmodell](data-model.md) Application Insights típusokhoz és adatmodellekhez.
- Tekintse meg a szabványos környezeti tulajdonságok gyűjteményének [konfigurációját](./configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet).

