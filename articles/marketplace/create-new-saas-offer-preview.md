---
title: Előzetes verzió célközönségének hozzáadása SaaS-ajánlathoz a Microsoft kereskedelmi piactéren
description: Előzetes verzió célközönségének hozzáadása a szolgáltatott szoftver (SaaS) ajánlatához a Microsoft partner Centerben.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 041df9b77e85f09d2cf680773edc995c6288acaa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96746420"
---
# <a name="how-to-add-a-preview-audience-for-your-saas-offer"></a>Előzetes verzió célközönségének hozzáadása SaaS-ajánlathoz

Amikor a partner Centerben hozza létre a szolgáltatott szoftvert (SaaS), meg kell határoznia az előnézeti célközönséget, aki áttekintheti az ajánlati listát, mielőtt működésbe lép. Ez a cikk az előzetes verzió célközönségének konfigurálását ismerteti.

> [!NOTE]
> Ha úgy dönt, hogy egymástól függetlenül dolgozza fel a tranzakciókat, akkor ez a beállítás nem jelenik meg. Ehelyett ugorjon az [SaaS-ajánlat értékesítésének módjára](create-new-saas-offer-marketing.md).

## <a name="define-a-preview-audience"></a>Előnézet célközönségének meghatározása

Az **előzetes verzió célközönsége** lapon meghatározhatja, hogy egy korlátozott célközönség hogyan tekintheti át SaaS-ajánlatát, mielőtt közzéteszi azt a szélesebb körű piactéren. Meghívhat a Microsoft-fiók (MSA) vagy a Azure Active Directory (Azure AD) e-mail-címére. Adjon hozzá legalább egy legfeljebb 10 e-mail-címet manuálisan, vagy importáljon akár 20-at egy. CSV-fájllal. Bármikor frissítheti az előnézeti célközönség listáját.

> [!NOTE]
> Az előzetes verzió célközönsége különbözik a privát közönségtől. Az előzetes verzió célközönsége az online áruházakban való közzététel előtt jogosult az ajánlathoz való hozzáférésre. Dönthet úgy is, hogy létrehoz egy csomagot, és csak a privát célközönség számára teszi elérhetővé. A Private-csomagokat a [SaaS-ajánlathoz tartozó csomagok létrehozásával](create-new-saas-offer-plans.md)kapcsolatban ismertetjük.

### <a name="add-email-addresses-manually"></a>E-mail címek manuális hozzáadása

1. A **célközönség előnézete** lapon adjon hozzá egy Azure ad-vagy MSA-e-mail-címet és egy opcionális leírást a megadott mezőkben.
1. Egy másik e-mail-cím hozzáadásához válassza a **másik E-mail hozzáadása** hivatkozást.
1. A következő lapra történő továbblépés előtt válassza a **Piszkozat mentése** lehetőséget: **technikai konfiguráció**.
1. Folytassa az [SaaS-ajánlat technikai részleteinek hozzáadásával](create-new-saas-offer-technical.md).

### <a name="add-email-addresses-using-the-csv-file"></a>E-mail-címek hozzáadása a CSV-fájl használatával

1. A **célközönség előnézete** lapon válassza ki a **célközönség exportálása (CSV)** hivatkozást.
1. Nyissa meg a t. CSV-fájl egy alkalmazásban, például a Microsoft Excelben.
1. A-ben. CSV-fájl, az **azonosító** oszlopban adja meg az előnézeti közönséghez hozzáadni kívánt e-mail-címeket.
1. A **Leírás** oszlopban opcionálisan megadhatja az egyes e-mail-címek leírását.
1. A **Type (típus** ) oszlopban adja hozzá a **MixedAadMsaId** minden olyan sorhoz, amelyhez e-mail-cím tartozik.
1. Mentse a fájlt. CSV-fájl.
1. A **célközönség előnézete** lapon válassza ki a **célközönség importálása (CSV)** hivatkozást.
1. A **megerősítés** párbeszédpanelen válassza az **Igen** lehetőséget.
1. Válassza a elemet. CSV-fájl, majd válassza a **Megnyitás** lehetőséget.
1. A következő lapra történő továbblépés előtt válassza a **Piszkozat mentése** lehetőséget: **technikai konfiguráció**.

## <a name="next-steps"></a>Következő lépések

- [Technikai részletek hozzáadása SaaS-ajánlathoz](create-new-saas-offer-technical.md)
