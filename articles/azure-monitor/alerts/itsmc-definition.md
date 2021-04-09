---
title: IT-szolgáltatásmenedzsmenti csatoló a Log Analytics
description: Ez a cikk áttekintést nyújt a IT-szolgáltatásmenedzsmenti csatolóokról (ITSMC), valamint arról, hogyan használható az alkalmazás a ITSM-munkaelemek figyelésére és felügyeletére Log Analytics és a problémák gyors megoldásához.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: 98f53ec1b6506a6d47146377e837576254f445e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103601066"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-solution"></a>Az Azure és az ITSM-eszközök összekötése az informatikai szolgáltatás-kezelési megoldás használatával

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

Ez a cikk azt ismerteti, hogyan konfigurálható a IT-szolgáltatásmenedzsmenti csatoló (ITSMC) a Log Analytics az IT szolgáltatás-felügyeleti (ITSM) munkaelemek központi kezelésére.

## <a name="add-it-service-management-connector"></a>IT-szolgáltatásmenedzsmenti csatoló hozzáadása

A kapcsolatok létrehozása előtt telepítenie kell a ITSMC.

1. A Azure Portal válassza az **erőforrás létrehozása** lehetőséget:

   ![Képernyőkép, amely megjeleníti az erőforrás-létrehozás menüelemét.](media/itsmc-overview/azure-add-new-resource.png)

2. **It-szolgáltatásmenedzsmenti csatoló** keresése az Azure Marketplace-en. Ezután válassza a **Létrehozás** elemet:

   ![Képernyőkép, amely megjeleníti a létrehozás gombot az Azure piactéren.](media/itsmc-overview/add-itsmc-solution.png)

3. Az **La munkaterület** szakaszban válassza ki azt a log Analytics munkaterületet, ahol a ITSMC telepíteni kívánja.
   > [!NOTE]
   > A ITSMC-t csak a következő régiókban telepítheti Log Analytics munkaterületeken: USA keleti régiója, USA 2. nyugati régiója, az USA déli középső régiója, az USA nyugati középső régiója, a US Gov Arizona, a US Gov Virginia, a Közép-Európa, Nyugat-Európa, Dél-Ausztrália, Délkelet-Ázsia, Kelet-Japán, Közép-India

4. A **log Analytics munkaterület** szakaszban válassza ki azt az erőforráscsoportot, amelyben létre kívánja hozni a ITSMC-erőforrást:

   ![A Log Analytics munkaterület szakaszt bemutató képernyőkép.](media/itsmc-overview/itsmc-solution-workspace.png)
   
   > [!NOTE]
   > A Microsoft Operations Management Suite (OMS) és a Azure Monitor közötti folyamatos áttérés részeként a OMS-munkaterületek már *log Analytics munkaterületek*.

5. Válassza az **OK** lehetőséget.

A ITSMC-erőforrás telepítésekor az ablak jobb felső sarkában megjelenik egy értesítés.

## <a name="create-an-itsm-connection"></a>ITSM-kapcsolatok létrehozása

A ITSMC telepítése után elő kell prep a ITSM eszközt, hogy engedélyezze a kapcsolódást a ITSMC. Azon ITSM-termék alapján, amelyhez csatlakozni szeretne, válassza a következő hivatkozások egyikét az utasításokhoz:

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

Miután elő a ITSM eszközt, hajtsa végre a következő lépéseket a kapcsolatok létrehozásához:

1. Az **összes erőforrás** területen keresse meg a **ügyfélszolgálati (*a munkaterület neve*)**:

   ![A Azure Portal legutóbbi erőforrásait bemutató képernyőkép.](media/itsmc-definition/create-new-connection-from-resource.png)

1. A bal oldali ablaktábla **munkaterület-adatforrások** területén válassza a **ITSM-kapcsolatok** elemet:

   ![Képernyőkép, amely megjeleníti a ITSM-kapcsolatok menüpontot.](media/itsmc-overview/add-new-itsm-connection.png)

1. Válassza a **kapcsolatok hozzáadása** lehetőséget.

1. Az Ön által használt ITSM-termék alapján határozza meg a kapcsolódási beállításokat:

    - [ServiceNow](./itsmc-connections-servicenow.md)
    - [System Center Service Manager](./itsmc-connections-scsm.md)
    - [Cherwell](./itsmc-connections-cherwell.md)
    - [Provance](./itsmc-connections-provance.md)

   > [!NOTE]
   > Alapértelmezés szerint a ITSMC 24 óránként egyszer frissíti a kapcsolatok konfigurációs szolgáltatásait. Ha azonnal frissíteni szeretné a kapcsolatok adatait, hogy azok megfeleljenek a módosítások vagy a sablonok frissítéseinek, válassza a **szinkronizálás** gombot a kapcsolatok ablaktábláján:
   >
   > ![A szinkronizálás gombot a kapcsolatok ablaktábláján ábrázoló képernyőkép.](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="create-itsm-work-items-from-azure-alerts"></a>ITSM-munkaelemek létrehozása az Azure-riasztásokból

A ITSM-kapcsolatok létrehozása után a ITMC használatával létrehozhat munkaelemeket a ITSM eszközön az Azure-riasztások alapján. A munkaelemek létrehozásához használja a ITSM műveletet a műveleti csoportokban.

A műveleti csoportok moduláris és újrafelhasználható módot biztosítanak az Azure-riasztások műveleteinek elindításához. A műveleti csoportokat metrikus riasztásokkal, műveletnapló riasztásokkal és a Azure Portal Log Analytics riasztásokkal is használhatja.

> [!NOTE]
> A ITSM-kapcsolatok létrehozása után 30 percet kell várnia, amíg a szinkronizálási folyamat befejeződik.

## <a name="define-a-template"></a>Sablon definiálása

Bizonyos munkaelem-típusok használhatják a ITSM eszközben definiált sablonokat. A sablonok segítségével megadhatja azokat a mezőket, amelyeket a rendszer automatikusan kitölt a műveleti csoport rögzített értékei szerint. Megadhatja, hogy melyik sablont szeretné használni a műveleti csoport definíciójának részeként. A sablonok létrehozásáról a ServiceNow docs (itt) [] című témakörben talál további információt https://docs.servicenow.com/bundle/paris-platform-administration/page/administer/form-administration/task/t_CreateATemplateUsingTheTmplForm.html .

Műveleti csoport létrehozása:

1. A Azure Portal válassza a  **riasztások** elemet.
2. A képernyő felső részén található menüben válassza a **kezelés műveletek** elemet:

    ![Képernyőkép, amely a műveletek kezelése menüelemet jeleníti meg.](media/itsmc-overview/action-groups-selection-big.png)

   Megjelenik a **műveleti csoport létrehozása** ablak.

3. Válassza ki azt az **előfizetést** és **erőforráscsoportot** , amelyben létre szeretné hozni a műveleti csoportot. Adjon meg értékeket a műveleti **csoport nevében** és a **megjelenítendő névben** a műveleti csoport számára. Ezután válassza a **Tovább: értesítések** lehetőséget.

    ![A műveleti csoport létrehozása ablakot megjelenítő képernyőkép.](media/itsmc-overview/action-groups-details.png)

4. Az **értesítések** lapon válassza a **Tovább: műveletek** elemet.
5. A **műveletek** lapon válassza a **ITSM** elemet a **művelet típusa** listában. A **név mezőben** adja meg a művelet nevét. Ezután kattintson a **Szerkesztés részleteit** jelölő toll gombra.

    ![Képernyőkép, amely a műveleti csoport létrehozásához szükséges beállításokat jeleníti meg.](media/itsmc-definition/action-group-pen.png)

6. Az **előfizetés** listában válassza ki a log Analytics munkaterületet tartalmazó előfizetést. A **kapcsolatok** listájában válassza ki a ITSM-összekötő nevét. Ezt a munkaterület neve követi majd. Példa: *MyITSMConnector (sajátmunkaterület)*.

7. Válasszon **munkaelem** -típust.

8. Ha rögzített értékekkel rendelkező, beépített mezőket szeretne kitölteni, válassza az **egyéni sablon használata** lehetőséget. Ellenkező esetben válasszon egy meglévő [sablont](#define-a-template) a **sablon** listáról, és adja meg a rögzített értékeket a sablon mezőiben.

9. Az ITSM műveleti csoport létrehozásához használt felület utolsó szakaszában megadhatja, hogy hány munkaelem jön létre az egyes riasztásokhoz.

   > [!NOTE]
   > Ez a szakasz csak a naplók keresési értesítéseire vonatkozik. Minden más riasztási típus esetében riasztásként egy munkaelemet fog létrehozni.

   * Ha az **incidens** vagy a **riasztás** lehetőséget választotta a **munkaelem** legördülő listában, lehetősége van egyéni munkaelemeket létrehozni az egyes konfigurációs elemekhez.
    
     ![Képernyőkép, amely az I T S M jegy területét mutatja munkaelemként kiválasztott incidenssel.](media/itsmc-overview/itsm-action-configuration.png)
    
     * Ha bejelöli az egyes **konfigurációs elemek egyéni** munkaelemek létrehozása jelölőnégyzetet, minden riasztásban minden konfigurációs elem új munkaelemet fog létrehozni. Mivel az érintett konfigurációs elemek esetében több riasztás is előfordul, az egyes konfigurációs elemekhez egynél több munkaelem fog vonatkozni.

       Egy három konfigurációs elemet tartalmazó riasztás például három munkaelemet fog létrehozni. Egy olyan riasztás, amely egyetlen konfigurációs elemmel fog létrehozni egy munkaelemet.
        
     * Ha törli az egyes **konfigurációs elemek egyéni munkaelemeinek létrehozása** jelölőnégyzet jelölését, akkor a ITSMC egyetlen munkaelemet hoz létre minden egyes riasztási szabályhoz, és hozzáfűzi az összes érintett konfigurációs elemet. Új munkaelem jön létre, ha az előző lezárult.

       >[!NOTE]
       > Ebben az esetben a kilőtt riasztások némelyike nem hoz majd új munkaelemeket a ITSM eszközben.

       Egy három konfigurációs elemet tartalmazó riasztás például egy munkaelemet fog létrehozni. Ha az előző példához hasonló riasztási szabályhoz tartozik egy konfigurációs elem, a konfigurációs elem csatolva lesz a létrehozott munkaelemben érintett konfigurációs elemek listájához. Egy másik, egy konfigurációs elemmel rendelkező riasztási szabályhoz tartozó riasztás egy munkaelemet fog létrehozni.

   * Ha a **munkaelem** legördülő listában az **esemény** lehetőséget választotta, dönthet úgy, hogy egyéni munkaelemeket hoz létre minden naplóbejegyzés vagy mindegyik konfigurációs elem számára.
    
     ![Képernyőkép, amely az I T S M jegy területét mutatja munkaelemként kiválasztott eseménnyel.](media/itsmc-overview/itsm-action-configuration-event.png)

     * Ha **az egyéni munkaelemek létrehozása lehetőséget választja minden naplóbejegyzés esetében (a konfigurációs elem mezője nincs kitöltve. Nagy számú munkaelemet eredményezhet.)** a log Search riasztási lekérdezés keresési eredményei között minden sorhoz létrejön egy munkaelem. A munkaelem tartalmában található Description tulajdonság a keresési eredmények sorát fogja tartalmazni.
      
     * Ha **az egyes konfigurációs elemekhez az egyes** munkaelemek létrehozása lehetőséget választja, minden egyes riasztás konfigurációs eleme új munkaelemet fog létrehozni. Minden konfigurációs elemnek több munkaeleme is lehet a ITSM-rendszeren. Ez a beállítás megegyezik a jelölőnégyzet bejelölésével, amely akkor jelenik meg, ha a munkaelem típusaként kiválasztja az **incidens** elemet.

10. Válassza az **OK** lehetőséget.

Azure-riasztási szabály létrehozásakor vagy szerkesztésekor használjon egy ITSM műveletet tartalmazó műveleti csoportot. A riasztás indításakor a rendszer létrehozza vagy frissíti a munkaelemet a ITSM eszközben.

> [!NOTE]
> A ITSM művelet díjszabásával kapcsolatos információkért tekintse meg a műveleti csoportok [díjszabási lapját](https://azure.microsoft.com/pricing/details/monitor/) .
>
> A riasztási szabály definíciójának rövid leírása mezője 40 karakterre van korlátozva, ha a ITSM művelettel küldi el.

## <a name="next-steps"></a>Következő lépések

* [ITSMC kapcsolatos problémák elhárítása](./itsmc-resync-servicenow.md)
