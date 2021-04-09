---
title: Azure Monitor-munkafüzetek saját tárhelyet hozhatnak
description: Megtudhatja, hogyan védheti meg a munkafüzetet a munkafüzet tartalmának tárolóba való mentéséhez.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: lagayhar
author: lgayhardt
ms.openlocfilehash: 16dbd7f7cd178a76b34b58f4bc6f9a0bc00fac73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100618945"
---
# <a name="bring-your-own-storage-to-save-workbooks"></a>Saját tároló használata a munkafüzetek mentéséhez

Bizonyos esetekben előfordulhat, hogy egy lekérdezés vagy egy üzleti logikát szeretne védeni. A munkafüzetek lehetővé teszi a munkafüzetek biztonságossá tételét azáltal, hogy a munkafüzet tartalmát a tárolóba menti. A Storage-fiók ezután titkosítható a Microsoft által felügyelt kulcsokkal, vagy a saját kulcsainak ellátásával kezelheti a titkosítást. [Tekintse meg Storage Service Encryption Azure-dokumentációját.](../../storage/common/storage-service-encryption.md)

## <a name="saving-workbook-with-managed-identities"></a>Munkafüzet mentése felügyelt identitásokkal

1. Mielőtt menteni szeretné a munkafüzetet a tárhelyre, létre kell hoznia egy felügyelt identitást (minden szolgáltatás-> felügyelt identitást), és hozzáférést kell biztosítania a `Storage Blob Data Contributor` Storage-fiókhoz. [Lásd: Azure-dokumentáció a felügyelt identitásokról.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

    [![Szerepkör-hozzárendelés hozzáadását bemutató képernyőkép](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png)](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png#lightbox)

2. Hozzon létre egy új munkafüzetet.
3. Kattintson a **Save (Mentés** ) gombra a munkafüzet mentéséhez.
4. Lehetőség van arra, hogy `Save content to an Azure Storage Account` Az Azure Storage-fiókba való mentéshez jelölje be a jelölőnégyzetet.

    ![A mentett párbeszédpanelt megjelenítő képernyőkép](./media/workbooks-bring-your-own-storage/saved-dialog-default.png)

5. Válassza ki a Desire Storage-fiókot és-tárolót. A Storage-fiók listája a fent kiválasztott előfizetésből származik.

    ![A tárolási lehetőséget tartalmazó Save (Mentés) párbeszédpanelt bemutató képernyőfelvétel](./media/workbooks-bring-your-own-storage/save-dialog-with-storage.png)

6. Ezután válassza a **módosítás** lehetőséget a korábban létrehozott felügyelt identitás kiválasztásához.

    [![Az identitás módosítása párbeszédpanelt ábrázoló képernyőkép](./media/workbooks-bring-your-own-storage/change-managed-identity.png)](./media/workbooks-bring-your-own-storage/change-managed-identity.png#lightbox)

7. Miután kiválasztotta a tárolási beállításokat, kattintson a **Save (Mentés** ) gombra a munkafüzet mentéséhez.

## <a name="limitations"></a>Korlátozások

- Az egyéni tárolóba való mentéskor a munkafüzet egyes részeit nem rögzítheti egy irányítópultra, mivel az egyéni PIN-kód védett adatokat tartalmaz az irányítópulton. Egyéni tároló használata esetén csak a munkafüzetre mutató hivatkozásokat rögzíthet az irányítópultokra.
- Miután a munkafüzetet mentette az egyéni tárolóba, mindig az egyéni tárolóba lesz mentve, és ez a funkció nem kapcsolható ki. Máshol való mentéshez használhatja a "Mentés másként" lehetőséget, és úgy dönt, hogy nem menti a másolatot az egyéni tárolóba.
- Application Insights erőforrásban található munkafüzetek "örökölt" munkafüzetek, és nem támogatják az egyéni tárolókat. A Application Insights erőforrás legújabb munkafüzetei a "... További "kiválasztás. A régi munkafüzetek nem rendelkeznek előfizetési lehetőségekkel mentéskor.

   ![Az örökölt munkafüzetet ábrázoló képernyőfelvétel](./media/workbooks-bring-your-own-storage/legacy-workbooks.png)

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan hozhat létre [Térkép](workbooks-map-visualizations.md) -vizualizációkat a munkafüzetekben.
- Megtudhatja, hogyan használhatja [a csoportokat a munkafüzetekben](../visualize/workbooks-groups.md).