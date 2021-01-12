---
title: Data Factory csatlakozása az Azure Purview-hoz
description: Tudnivalók a Data Factory az Azure hatáskörébe való kapcsolódásáról
services: data-factory
ms.author: lle
author: lrtoyou1223
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 7dc05c88416bb2a23221029bc04c506271a86652
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108347"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Data Factory összekötése az Azure hatáskörébe (előzetes verzió)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ebből a cikkből megtudhatja, hogyan csatlakoztatható az adatgyár az Azure hatáskörébe, és hogyan jelentheti az ADF-tevékenységek adatvonalának jelentését, az adatfolyamatokat és a SSIS-csomagok végrehajtását.

## <a name="connect-data-factory-to-azure-purview"></a>Az adatgyár összekötése az Azure-beli hatáskörébe
Az Azure hatáskörébe egy új felhőalapú szolgáltatás, amellyel az adatkezelők központilag kezelhetik az adatkezelést a Felhőbeli és a helyszíni környezetek között. Az adatgyárat az Azure hatáskörébe csatlakoztathatja, és a kapcsolat lehetővé teszi, hogy az Azure hatáskörébe tartozik a másolási, az adatfolyam-és a SSIS-csomag kibontási adatainak rögzítése. Az adatgyár Azure-beli hatáskörébe való regisztrálásával kapcsolatban lásd: [a Azure Data Factory és az Azure hatáskörébe való kapcsolódás](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory). 

## <a name="report-lineage-data-to-azure-purview"></a>Lineage-adatjelentések az Azure hatáskörébe
Ha az ügyfelek a másolást, az adatfolyamot vagy a SSIS-csomag végrehajtását Azure Data Factoryban futtatják, az ügyfelek megkapják a függőségi viszonyt, és magas szintű áttekintést nyújtanak a teljes munkafolyamat-folyamatról az adatforrások és a célhelyek között.
A Lineage Azure Data Factoryból való gyűjtésével kapcsolatban lásd: a "a" [adatfeldolgozási vonal](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory#supported-azure-data-factory-activities).

## <a name="next-steps"></a>Következő lépések
[A katalógus Lineage felhasználói útmutatója](https://docs.microsoft.com/azure/purview/catalog-lineage-user-guide)

[Oktatóanyag: a leküldéses Data Factory az Azure hatáskörébe](turorial-push-lineage-to-purview.md)
