---
title: A Dynamics 365-naplók összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Dynamics 365 Common Data Service (CDS) tevékenységek összekötőjét a folyamatos rendszergazdai, felhasználói és támogatási tevékenységekkel kapcsolatos információkhoz.
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
ms.date: 12/13/2020
ms.author: yelevin
ms.openlocfilehash: 018368b6284cf39edec01f0a9a943b8ea15c85d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98104181"
---
# <a name="connect-dynamics-365-activity-logs-to-azure-sentinel"></a>A Dynamics 365-tevékenység naplófájljainak összekapcsolása az Azure Sentinel-ben

A [Dynamics 365](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description) Common Data Service (CDS) tevékenységek összekötő betekintést nyújt a rendszergazdák, a felhasználók és a támogatási tevékenységekbe, valamint a Microsoft közösségi engagement naplózási eseményeibe. A Dynamics 365 CRM-naplók Azure Sentinelbe való csatlakoztatásával megtekintheti ezeket az adatait a munkafüzetekben, felhasználhatja egyéni riasztások létrehozására, és kihasználhatja azt a vizsgálati folyamat javítása érdekében. Ez az új Azure Sentinel-összekötő gyűjti a Dynamics CDS-adatokat az Office Management API-ból. Ha többet szeretne megtudni a Power platformon auditált Dynamics CDS-tevékenységekről, tekintse meg a [tevékenységek naplózásának engedélyezése és használata](/power-platform/admin/enable-use-comprehensive-auditing)című témakört.

> [!IMPORTANT]
>
> A Dynamics 365 Common Data Service (CDS) tevékenységek összekötője jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

## <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedélyekkel kell rendelkeznie az Azure Sentinel-munkaterületen.

- Rendelkeznie kell [Microsoft Dynamics 365 üzemi licenccel](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description). Ez az összekötő nem érhető el a homokozó környezetekhez.
    - A tevékenységek naplózásához Microsoft 365 Nagyvállalati verzió [E3 vagy E5](/power-platform/admin/enable-use-comprehensive-auditing#requirements) előfizetésre van szükség.

- Adatok lekérése az Office Management API-ból:
    - A bérlő globális rendszergazdájának kell lennie.

    - Az Office [biztonsági és megfelelőségi központban](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance)engedélyezni kell az [Office naplózási naplózását](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) .

## <a name="enable-the-dynamics-365-activities-data-connector"></a>A Dynamics 365 tevékenységek adatösszekötő engedélyezése

1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők** lehetőséget.

1. Az **adatösszekötők katalógusában** válassza a **Dynamics 365 (előzetes verzió)** lehetőséget, majd az előnézet ablaktáblán válassza az **összekötő megnyitása lapot** .

1. Az **utasítások** lap **konfiguráció** területén kattintson a **kapcsolat** elemre. 

    Az összekötő aktiválása után a rendszer körülbelül 30 percet vesz igénybe, amíg meg nem jelenik a gráfba érkező adatcsatorna. 

    A [megfelelőségi központban lévő Office](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#requirements-to-search-the-audit-log)-napló alapján akár 30 percet is igénybe vehet, akár 24 óráig is eltarthat, miután a megfelelő naplóbeli rekord visszakerül az eredményekbe.

1. A Microsoft Dynamics-naplózási naplók a táblázatban találhatók `Dynamics365Activity` . Tekintse meg a tábla [séma-referenciáját](/azure/azure-monitor/reference/tables/dynamics365activity).

## <a name="querying-the-data"></a>Az adatlekérdezés

1. Az Azure Sentinel navigációs menüjében válassza a **naplók** lehetőséget.

1. Futtassa a következő lekérdezést a naplók megérkezésének ellenőrzéséhez:

    ```kusto
    Dynamics365Activity
    | take 10
    ```


## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztathatók a Dynamics 365-tevékenységek az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [érheti el az adatait és a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerkedjen meg a fenyegetések észlelésével az Azure Sentinel használatával, [beépített](tutorial-detect-threats-built-in.md) vagy [Egyéni](tutorial-detect-threats-custom.md) szabályokkal.
