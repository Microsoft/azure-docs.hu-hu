---
title: Vezető felügyelet a Salesforce-ben – Microsoft kereskedelmi piactér
description: Ismerje meg, hogyan konfigurálhatja a Salesforce a Microsoft AppSource és az Azure Marketplace-hez készült érdeklődők konfigurálásához
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: 73caf848ab5c6f8e973469066ce4612a075a52f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94489318"
---
# <a name="configure-lead-management-for-salesforce"></a>A Salesforce vezető felügyeletének konfigurálása

Ez a cikk azt ismerteti, hogyan állíthatja be a Salesforce rendszerét, hogy feldolgozza a Microsoft AppSource és az Azure piactéren kínált értékesítési érdeklődőket.

> [!NOTE]
> Az Azure Marketplace nem támogatja az előre feltöltött listákat, például az **ország** mező értékeinek listáját. A folytatás előtt győződjön meg arról, hogy nincsenek beállítva felsorolások. Másik lehetőségként beállíthat egy [https-végpontot](./commercial-marketplace-lead-management-instructions-https.md) vagy egy [Azure-táblázatot](./commercial-marketplace-lead-management-instructions-azure-table.md) az érdeklődők fogadására.

## <a name="set-up-your-salesforce-system"></a>A Salesforce-rendszerek beállítása

1. Jelentkezzen be a Salesforce.
1. Navigáljon a **webes és a vezető** beállításokhoz. 
    
    Ha a Salesforce-világítási élményt használja
    1. Válassza a **telepítő** elemet a Salesforce kezdőlapján.

       ![Salesforce-telepítő](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. A **telepítés** lapon válassza a **platform eszközök**  >  **szolgáltatás beállítások**  >  **marketing**  >  **web – vezető** lehetőséget.

        ![Salesforce web – Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

    Ha a klasszikus Salesforce-élményt használja:

    1. Válassza a **telepítő** elemet a Salesforce kezdőlapján.

       ![Klasszikus Salesforce-telepítő](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. A **telepítés** lapon válassza a **Létrehozás**  >  **Testreszabás**  >  webes **érdeklődők**  >  **számára** lehetőséget.

        ![Salesforce klasszikus webes – vezető](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

   A fennmaradó lépések ugyanazok, mint a Salesforce-élmények esetében.

1. A **webes telepítés** lapon válassza a **webes létrehozás az űrlapon** gombot.
1. A **web – vezető beállításnál** válassza a **webes létrehozási űrlap létrehozása** lehetőséget.

    ![Salesforce-alapú webes telepítés](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

1. A **webes létrehozás űrlap létrehozása** lapon győződjön meg arról, hogy a `Include reCAPTCHA in HTML` beállítás törölve van, majd válassza a **Létrehozás** lehetőséget.

    ![Webes Salesforce létrehozásához](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

1. Néhány HTML-szöveg jelenik meg. Keressen rá az "OID" szövegre, és másolja az **"OID" értéket** a HTML-szövegből (csak az idézőjelek között lévő szöveg), majd mentse azt. Ezt az értéket a közzétételi portál **szervezeti azonosító** mezőjébe illeszti be.

    ![A Salesforce létrehoz egy webes – vezető űrlapot a HTML "OID" érték megjelenítéséhez](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

1. Válassza a **kész** lehetőséget.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Az ajánlat beállítása, hogy érdeklődőket küldjön a Salesforce

Ha készen áll az ajánlathoz tartozó érdeklődő-felügyeleti információk konfigurálására a közzétételi portálon, kövesse az alábbi lépéseket.

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).

1. Válassza ki az ajánlatot, és nyissa meg az **ajánlat beállítása** lapot.

1. Az **ügyfél-érdeklődők** szakaszban válassza a **kapcsolat** lehetőséget.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-salesforce/customer-leads.png" alt-text="Ügyfél-érdeklődők":::

1. A **kapcsolat részletei** előugró ablakban válassza a **Salesforce** lehetőséget a **vezető célhelyhez** , és illessze be az `oid` értéket a létrehozott webes és érdeklődő űrlapból a **szervezeti azonosító** mezőbe.

    ![Kapcsolat részletei – előugró ablak – a kapcsolattartási e-mail mező ellenőrzése](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)

1. A **kapcsolattartási e-mail** cím alatt adja meg a vállalat azon tagjainak e-mail-címeit, akiknek új érdeklődő fogadása esetén e-mail-értesítéseket kell kapniuk. A pontosvesszővel elválasztva több e-mailt is megadhat.

1. Válassza az **OK** lehetőséget.

Ha szeretné meggyőződni arról, hogy sikeresen csatlakozott egy érdeklődői célhoz, válassza az **Érvényesítés** lehetőséget. Ha a művelet sikeres, a vezető célhelyen egy teszt vezet.

>[!NOTE]
>Be kell fejeznie az ajánlat többi részének konfigurálását, és közzé kell tennie az ajánlathoz tartozó érdeklődők fogadása előtt.
