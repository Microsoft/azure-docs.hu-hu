---
title: Az Azure Files vizsgálata
description: Ez az útmutató ismerteti az Azure-fájlok vizsgálatának részleteit.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/01/2020
ms.openlocfilehash: a0bd7a4cd8afafc16f05b4a37cd5723304ad931e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96553119"
---
# <a name="register-and-scan-azure-files"></a>Azure Files regisztrálása és vizsgálata

## <a name="supported-capabilities"></a>Támogatott képességek

Azure Files támogatja a teljes és növekményes vizsgálatokat a metaadatok rögzítéséhez és besorolások alkalmazásához a metaadatokon, a rendszer és az ügyfél besorolása alapján.

## <a name="prerequisites"></a>Előfeltételek

- Az adatforrások regisztrálása előtt hozzon létre egy Azure-beli hatáskörébe tartozó fiókot. A hatáskörébe tartozó fiókok létrehozásával kapcsolatos további információkért tekintse meg a rövid útmutató [: Azure hatáskörébe tartozó fiók létrehozása](create-catalog-portal.md)című témakört.
- A vizsgálatok megkezdéséhez adatforrás-rendszergazdának kell lennie, a részletekért tekintse meg a [katalógus engedélyeit](catalog-permissions.md) .

## <a name="register-an-azure-files-storage-account"></a>Azure Files Storage-fiók regisztrálása

Ha új Azure Files fiókot szeretne regisztrálni a adatkatalógusban, tegye a következőket:

1. Navigáljon a hatáskörébe Data Catalog.
1. A bal oldali navigációs sávon válassza a **felügyeleti központ** lehetőséget.
1. Válassza ki **az adatforrásokat** a **források és a vizsgálat** alatt.
1. Válassza a **+ Új** lehetőséget.
1. A **források regisztrálása** lapon válassza a **Azure Files** lehetőséget. Válassza a **Folytatás** lehetőséget.

:::image type="content" source="media/register-scan-azure-files/register-new-data-source.png" alt-text="új adatforrás regisztrálása" border="true":::

A **források regisztrálása (Azure Files)** képernyőn tegye a következőket:

1. Adja meg azt a **nevet** , amelyben az adatforrás szerepelni fog a katalógusban.
1. Válassza ki, hogyan szeretné a kívánt Storage-fiókra mutatni:
   1. Válasszon az **Azure-előfizetésből**, válassza ki a megfelelő előfizetést az **Azure-előfizetés** legördülő listából, és a megfelelő Storage-fiókot a Storage- **fiók neve** legördülő listából.
   1. Az **ENTER billentyűt manuálisan** is kiválaszthatja, és megadhat egy szolgáltatási végpontot (URL-címet).
1. **Fejezze** be az adatforrás regisztrálását.

:::image type="content" source="media/register-scan-azure-files/register-sources.png" alt-text="források regisztrálása – beállítások" border="true":::

## <a name="set-up-authentication-for-a-scan"></a>Hitelesítés beállítása vizsgálathoz

Ha a Azure Files Storage-hoz szeretne hitelesítést beállítani a fiók kulcsa alapján, tegye a következőket:

1. Válassza a hitelesítési módszer lehetőséget a **fiók kulcsaként**.
2. Válasszon **Az Azure-előfizetési lehetőség közül** .
3. Válassza ki az Azure-előfizetését, ahol a Azure Files fiók létezik.
4. Válassza ki a Storage-fiók nevét a listából.
5. Kattintson a **Finish** (Befejezés) gombra.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Következő lépések

- [Az Azure-beli hatáskörébe tartozó adatkatalógus tallózása](how-to-browse-catalog.md)
- [Keresés az Azure-beli hatáskörébe Data Catalog](how-to-search-catalog.md)