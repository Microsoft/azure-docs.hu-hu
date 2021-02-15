---
title: Az ADF-ben lévő adatfelderítés és-vizsgálat a hatáskörébe
description: Ismerje meg, hogyan derítheti fel Azure Data Factory az adataikat a hatáskörébe
ms.service: data-factory
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 01/15/2021
ms.openlocfilehash: 031e7ceee739ffd666ad367bd6778865a297a25d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386271"
---
# <a name="discover-and-explore-data-in-adf-using-purview"></a>Az ADF-ben lévő adatfelderítés és-vizsgálat a hatáskörébe

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ebben a cikkben egy Azure-beli hatáskörébe tartozó fiókot fog regisztrálni egy Data Factory. Ez a kapcsolat lehetővé teszi az Azure hatáskörébe tartozó eszközök felderítését és a velük való interakciót ADF-képességekkel. 

Az ADF-ben a következő feladatokat végezheti el: 
- A felső keresőmező használatával megkeresheti a hatáskörébe tartozó eszközöket kulcsszavak alapján 
- Az adatok megértése metaadatok, Lineage, jegyzetek alapján 
- Az adatokat csatlakoztathatja az adat-előállítóhoz társított szolgáltatásokkal vagy adatkészletekkel 

## <a name="prerequisites"></a>Előfeltételek 
- [Azure-beli hatáskörébe tartozó fiók](../purview/create-catalog-portal.md) 
- [Data Factory](./quickstart-create-data-factory-portal.md) 
- [Azure-beli hatáskörébe tartozó fiók összekötése Data Factory](./connect-data-factory-to-azure-purview.md) 

## <a name="using-azure-purview-in-data-factory"></a>Az Azure hatáskörébe használata Data Factory 

Az Data Factory Azure-beli hatáskörébe való használata megköveteli, hogy hozzáférést biztosítson a hatáskörébe tartozó fiókhoz. Data Factory továbbítja a hatáskörébe tartozó engedélyeit. Ha például egy kurátori jogosultsági szerepkörrel rendelkezik, az Azure hatáskörébe tartozó metaadatokat is szerkesztheti. 

### <a name="data-discovery-search-datasets"></a>Adatfelderítés: az adathalmazok keresése 

Az Azure hatáskörébe tartozó, regisztrált és beolvasott adatmennyiségek felderítéséhez használhatja a Data Factory portál felső középső sávján található keresési sávot. Győződjön meg arról, hogy az Azure hatáskörébe kattint az összes szervezeti adattal való kereséshez. 

:::image type="content" source="./media/data-factory-purview/search-dataset.png" alt-text="Képernyőfelvétel az adatkészletek végrehajtására.":::

### <a name="actions-that-you-can-perform-over-datasets-with-data-factory-resources"></a>Data Factory erőforrásokkal rendelkező adatkészleteken végrehajtható műveletek 
Közvetlenül is létrehozhat társított szolgáltatást, adatkészletet vagy adatfolyam az Azure hatáskörébe tartozó keresések során.

:::image type="content" source="./media/data-factory-purview/actions-over-purview-data.png" alt-text="Képernyőkép, amely bemutatja, hogyan hozhat létre közvetlenül társított szolgáltatást, adatkészletet vagy adatfolyam az Azure hatáskörébe tartozó keresések során.":::

##  <a name="nextsteps"></a>További lépések 

- [Azure Data Factory-eszközök regisztrálása és vizsgálata az Azure hatáskörébe](../purview/register-scan-azure-synapse-analytics.md)
- [Adatkeresés az Azure-beli hatáskörébe Data Catalog](../purview/how-to-search-catalog.md)