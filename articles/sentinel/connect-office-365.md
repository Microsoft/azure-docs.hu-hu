---
title: Az Office 365-naplók összekapcsolhatók az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan használhatja az Office 365 log Connectort az Exchange-, a Team-és a SharePoint-alapú folyamatos felhasználói és rendszergazdai tevékenységekkel kapcsolatos információkra, beleértve a OneDrive is.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: 05848eb2761284669e659b3875e96acdfa71f90f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98632182"
---
# <a name="connect-office-365-logs-to-azure-sentinel"></a>Office 365-naplók Azure Sentinelhez csatlakoztatása

Az [Office 365](/office/) log Connector Azure Sentinel-információkat biztosít az **Exchange** -ben és a **SharePointban** (beleértve a **OneDrive**-t is) és most a **Teams** szolgáltatásban zajló folyamatos felhasználói és rendszergazdai tevékenységekről. Ezek az információk többek között olyan műveletek részleteit foglalják magukban, mint a fájlok letöltése, a hozzáférési kérelmek, az események csoportosításának módosításai, a postaláda-műveletek, a csapat eseményei (például csevegés, csapat, tag és csatorna eseményei), valamint a műveleteket végrehajtó felhasználó adatai. Ha az Office 365-naplókat az Azure Sentinelhez csatlakoztatja, lehetővé teszi az adatok megtekintését és elemzését a munkafüzetekben, lekérdezheti az egyéni riasztásokat, és beépítheti azt a vizsgálati folyamat javítására, így jobban betekintést nyerhet az Office 365-biztonságba.

## <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedélyekkel kell rendelkeznie az Azure Sentinel-munkaterületen.

- A bérlőn globális rendszergazdai vagy biztonsági rendszergazdai jogosultsággal kell rendelkeznie.

- Az Office 365 üzemelő példányának az Azure Sentinel-munkaterülettel azonos bérlőn kell lennie.

> [!IMPORTANT]
> - Ahhoz, hogy az összekötő hozzáférhessen az adataihoz az Office 365 felügyeleti tevékenység API-n keresztül, az Office 365-telepítésben engedélyezve kell lennie az **egyesített naplózási naplózásnak** . Az Office 365/Microsoft 365 licenc típusától függően előfordulhat, hogy alapértelmezés szerint nem engedélyezhető. Az [Office 365 biztonsági és megfelelőségi központban](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) ellenőrizze az egyesített naplózási naplózás állapotát a licenc típusa alapján.
> - Manuálisan is engedélyezheti, letilthatja és megtekintheti az Office 365 egyesített naplózási naplózásának aktuális állapotát. Útmutatásért lásd: [az Office 365 naplózási naplójának keresése be-és kikapcsolása](/office365/securitycompliance/turn-audit-log-search-on-or-off).
> - További információt az [Office 365 felügyeleti tevékenység API-referenciája](/office/office-365-management-api/office-365-management-activity-api-reference)című témakörben talál.


   > [!NOTE]
   > Amint azt a fentiekben említettük, és ahogy az **adattípusok** alatt az összekötő oldalon látható, az Azure Sentinel Office 365-összekötő jelenleg csak a Microsoft Exchange és a SharePoint (beleértve a OneDrive) és a **Teams** szolgáltatásban is támogatja a naplók betöltését. Vannak azonban olyan külső megoldások is, amelyek érdeklik a [más Office-adatforrások](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-office-365-alerts-with-graph-security-api/ba-p/984888) Azure sentinelbe való bevezetését. 

## <a name="enable-the-office-365-log-connector"></a>Az Office 365 log Connector engedélyezése

### <a name="instructions-tab"></a>Utasítások lap

1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők** lehetőséget.

1. Az **adatösszekötők katalógusában** válassza az **Office 365** lehetőséget, majd az előnézet ablaktáblán válassza az **összekötő megnyitása lapot** .

1. A **konfiguráció** feliratú szakaszban jelölje be azon Office 365-tevékenységek naplófájljainak jelölőnégyzetét, amelyekhez csatlakozni szeretne az Azure sentinelhez, majd kattintson a **módosítások alkalmazása** lehetőségre. 

   > [!NOTE]
   > Ha korábban több bérlőt csatlakoztatott az Azure Sentinelhez, az Office 365-összekötő egy régebbi verziójával, amely ezt támogatta, megtekintheti és módosíthatja az egyes bérlők által gyűjtött naplókat. Nem adhat hozzá további bérlőket, de a korábban hozzáadott bérlőket is eltávolíthatja.

### <a name="next-steps-tab"></a>Következő lépések lap

- Tekintse meg a SharePoint, a OneDrive, az Exchange és **365** a Teams naplózási adatait tartalmazó ajánlott munkafüzetek, lekérdezési minták és elemzési szabályok sablonjait.

- Ha manuálisan szeretné lekérdezni az Office 365 naplófájljait a **naplókban**, adja meg a `OfficeActivity` lekérdezési ablak első sorát.
   - Egy adott naplózási típus lekérdezésének szűréséhez írja be a `| where OfficeWorkload == "<logtype>"` következőt a lekérdezés második sorába: where *\<logtype\>* is,,, `SharePoint` `OneDrive` `Exchange` vagy `MicrosoftTeams` .

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtanulta, hogyan csatlakoztatható az Office 365 az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [érheti el az adatait és a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerkedjen meg a fenyegetések észlelésével az Azure Sentinel használatával, [beépített](tutorial-detect-threats-built-in.md) vagy [Egyéni](tutorial-detect-threats-custom.md) szabályokkal.