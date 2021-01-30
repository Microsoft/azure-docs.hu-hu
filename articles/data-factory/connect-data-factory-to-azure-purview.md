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
ms.openlocfilehash: 36eac4c60e0d7fd54ec304f0f17c2ecb454f0629
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063459"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Data Factory összekötése az Azure hatáskörébe (előzetes verzió)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk azt ismerteti, hogyan csatlakozhat Data Factory az Azure hatáskörébe, és hogyan jelentheti be az adatAzure Data Factory tevékenységek adatbontását, az adatfolyamatokat és a SSIS-csomagok végrehajtását.


## <a name="connect-data-factory-to-azure-purview"></a>Data Factory összekötése az Azure hatáskörébe
Az Azure hatáskörébe egy új felhőalapú szolgáltatás, amellyel az adatkezelők központilag kezelhetik az adatkezelést a Felhőbeli és a helyszíni környezetek között. A Data Factory összekapcsolhatja az Azure hatáskörébe, és a kapcsolat lehetővé teszi, hogy az Azure-t használja a másolási, az adatfolyam-és a SSIS-csomag begyűjtésére. A következő két módon csatlakoztatható az Azure hatáskörébe:
### <a name="register-azure-purview-account-to-data-factory"></a>Az Azure hatáskörébe tartozó fiók regisztrálása Data Factory
1. Az ADF-portálon nyissa meg az  ->  **Azure hatáskörébe** tartozó felügyeletet. Válassza **a Kapcsolódás a hatáskörébe fiókhoz** lehetőséget. 

:::image type="content" source="./media/data-factory-purview/register-purview-account.png" alt-text="A hatáskörébe tartozó fiók regisztrálására szolgáló képernyőkép.":::
2. Kiválaszthatja **Az Azure-előfizetést** , vagy **manuálisan is megadhatja**. Az **Azure-előfizetésből** kiválaszthatja azt a fiókot, amelyhez hozzá tud férni. 
3. A csatlakozás után látnia kell a hatáskörébe tartozó fiók nevét a TAB **hatáskörébe fiókban**. 
4. Az adatkereséshez használhatja a Azure Data Factory portál felső központjának keresési sávját. 

Ha az Azure hatáskörébe tartozó fiók Data Factory való regisztrálása után a figyelmeztetés az Azure adatfaktor-portálon jelenik meg, a probléma megoldásához kövesse az alábbi lépéseket:

:::image type="content" source="./media/data-factory-purview/register-purview-account-warning.png" alt-text="Képernyőkép a hatáskörébe tartozó fiók regisztrálásával kapcsolatos figyelmeztetésről.":::

1. Lépjen a Azure Portalra, és keresse meg az adatgyárat. Válassza a "címkék" szakaszt, és ellenőrizze, hogy van-e **catalogUri** nevű címke. Ha nem, válassza le és csatlakoztassa újra az Azure-beli hatáskörébe tartozó fiókot az ADF-portálon.

:::image type="content" source="./media/data-factory-purview/register-purview-account-tag.png" alt-text="Képernyőkép a hatáskörébe tartozó fiók regisztrálásához szükséges címkékről.":::

2. Ellenőrizze, hogy az engedélyt megadta-e az Azure-beli hatáskörébe tartozó fiókok Data Factory való regisztrálásához. Lásd: [a Azure Data Factory és az Azure hatáskörébe való kapcsolódás](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory#create-new-data-factory-connection)

### <a name="register-data-factory-in-azure-purview"></a>Data Factory regisztrálása az Azure hatáskörébe
Az Azure-beli Data Factory regisztrálásával kapcsolatban lásd: [a Azure Data Factory és az Azure hatáskörébe való kapcsolódás](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory). 

## <a name="report-lineage-data-to-azure-purview"></a>Lineage-adatjelentések az Azure hatáskörébe
Ha az ügyfelek a másolást, az adatfolyamot vagy a SSIS-csomag végrehajtását Azure Data Factoryban futtatják, az ügyfelek megkapják a függőségi viszonyt, és magas szintű áttekintést nyújtanak a teljes munkafolyamat-folyamatról az adatforrások és a célhelyek között.
A Lineage Azure Data Factoryból való gyűjtésével kapcsolatban lásd: a "a" [adatfeldolgozási vonal](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities).

## <a name="next-steps"></a>Következő lépések
[A katalógus Lineage felhasználói útmutatója](../purview/catalog-lineage-user-guide.md)

[Oktatóanyag: a leküldéses Data Factory az Azure hatáskörébe](turorial-push-lineage-to-purview.md)