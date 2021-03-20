---
title: Vezető felügyelet a Marketo-ben – Microsoft kereskedelmi piactér
description: Megtudhatja, hogyan kezelheti a Marketo CRM-rendszert a Microsoft AppSource és az Azure Marketplace-en található érdeklődők kezeléséhez.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: cbd12481312d310add15bf3d41e21e9c416f1c39
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94491075"
---
# <a name="use-marketo-to-manage-commercial-marketplace-leads"></a>A Marketo használata a kereskedelmi Piactéri érdeklődők kezelésére

Ez a cikk azt ismerteti, hogyan állíthatja be az Marketo CRM-rendszert a Microsoft AppSource és az Azure piactéren kínált értékesítési érdeklődők feldolgozásához.

## <a name="set-up-your-marketo-crm-system"></a>A Marketo CRM rendszer beállítása

1. Jelentkezzen be a Marketo.

1. Válassza a **Design Studio** elemet.

    ![Marketo Design Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

1.  Válassza az **új űrlap** lehetőséget.

    ![Új Marketo űrlap](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

1.  Töltse ki a kötelező mezőket az **új űrlap** párbeszédpanelen, majd válassza a **Létrehozás** lehetőséget.

    ![Új űrlap létrehozása a Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

1.  A **mező részletei** lapon válassza a **Befejezés** lehetőséget.

    ![Marketo befejezési űrlapja](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

1.  Jóváhagyás és bezárás.

1. A **MarketplaceLeadBackend** lapon válassza a **beágyazási kód** lehetőséget. 

    ![Marketo beágyazási kód](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

1. A Marketo beágyazási kód az alábbi példához hasonló kódot jelenít meg.

    ```html
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

1. Másolja a beágyazási kód űrlapon látható következő mezők értékeit. Ezeknek az értékeknek a használatával konfigurálhatja, hogy az ajánlat a következő lépésben fogadja az érdeklődőket. Használja a következő példát útmutatóként a szükséges azonosítók beolvasásához a Marketo beágyazási kódjának példája alapján.

    - Kiszolgáló azonosítója = **ys12**
    - Munchkin-azonosító = **123-PQR-789**
    - Űrlap azonosítója = **1179**

    A következő értékek kiszűrésének másik módja:

    - A kiszolgáló-azonosító a Marketo-példány URL-címében található, például: `serverID.marketo.com` .
    - Az előfizetés Munchkin-azonosítójának beszerzéséhez nyissa meg a **rendszergazda**  >  **Munchkin** menüt a **Munchkin-fiók azonosítója** mezőben, vagy a Marketo első részén REST API gazdagép altartománya: `https://{Munchkin ID}.mktorest.com` .
    - Az űrlap-azonosító a 7. lépésben létrehozott beágyazási kód űrlap azonosítója, amely az érdeklődőket a piactérről irányítja át.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Az ajánlat beállítása, hogy érdeklődőket küldjön a Marketo

Ha készen áll az ajánlathoz tartozó érdeklődő-felügyeleti információk konfigurálására a közzétételi portálon, kövesse az alábbi lépéseket. 

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).

1. Válassza ki az ajánlatot, és nyissa meg az **ajánlat beállítása** lapot.

1. Az **ügyfél-érdeklődők** szakaszban válassza a **kapcsolat** lehetőséget.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-marketo/customer-leads.png" alt-text="Ügyfél-érdeklődők":::

1. A **kapcsolat részletei** előugró ablakban válassza a **Marketo** lehetőséget az **érdeklődő célhelyéhez**.

    ![Érdeklődő célhelyének kiválasztása](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

1. Adja meg a **kiszolgáló azonosítóját**, a **Munchkin** és az **űrlap azonosítóját**.

    > [!NOTE]
    > Be kell fejeznie az ajánlat többi részének konfigurálását, és közzé kell tennie az ajánlathoz tartozó érdeklődők fogadása előtt. 

1. A **kapcsolattartási e-mail** cím alatt adja meg a vállalat azon tagjainak e-mail-címeit, akiknek új érdeklődő fogadása esetén e-mail-értesítéseket kell kapniuk. A pontosvesszővel elválasztva több e-mail-címet is megadhat.

1. Válassza az **OK** lehetőséget.

   Ha szeretné meggyőződni arról, hogy sikeresen csatlakozott egy érdeklődői célhoz, válassza az **Érvényesítés** lehetőséget. Ha a művelet sikeres, a vezető célhelyen egy teszt vezet.

   ![Kapcsolat részletei előugró ablak](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)
