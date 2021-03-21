---
title: Vezető felügyelet az Azure Table Storage szolgáltatással – Microsoft kereskedelmi piactér
description: Ismerje meg, hogyan konfigurálhatja az Azure Table Storage-t a Microsoft AppSource és az Azure Marketplace-hez készült érdeklődők konfigurálásához
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 08/25/2020
ms.openlocfilehash: 3c8b9444344e5bae414145e2b3367c265114a423
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586839"
---
# <a name="use-azure-table-storage-to-manage-commercial-marketplace-leads"></a>Az Azure Table Storage használata a kereskedelmi Piactéri érdeklődők felügyeletéhez

Ha az Ügyfélkapcsolat-kezelési (CRM) rendszer nincs kifejezetten támogatva a partner Centerben a Microsoft AppSource és az Azure Marketplace-érdeklődők fogadásához, az Azure Table Storage használatával kezelheti ezeket az érdeklődőket. Ezután exportálhatja az adatait, és importálhatja azt a CRM-rendszerbe. Ez a cikk azt ismerteti, hogyan hozhat létre egy Azure Storage-fiókot és egy táblát a fiókban. Emellett létrehozhat egy új folyamatot a Power automatizálás használatával, amely e-mailben értesítést küld, ha az ajánlata érdeklődőt kap.

## <a name="configure-an-azure-storage-account"></a>Azure Storage-fiók konfigurálása

1. Ha nem rendelkezik Azure-fiókkal, [létrehozhat egy ingyenes próbaverziós fiókot](https://azure.microsoft.com/pricing/free-trial/).
1. Ha az Azure-fiókja aktív, jelentkezzen be a [Azure Portalba](https://portal.azure.com).
1. A Azure Portal a következő eljárással hozzon létre egy Storage-fiókot:

    1. Válassza az **+ erőforrás létrehozása** lehetőséget a bal oldali menüsorban. Megjelenik az **új** ablaktábla a jobb oldalon.
    1. Válassza a **tároló** lehetőséget az **új** ablaktáblán. Megjelenik egy **Kiemelt** lista a jobb oldalon.
    1. A fiók létrehozásának megkezdéséhez válassza a **Storage-fiók** lehetőséget. Kövesse a Storage- [fiók létrehozása](../../storage/common/storage-account-create.md?tabs=azure-portal)című témakör utasításait.

        :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png" alt-text="Azure Storage-fiók létrehozásának lépései.":::

        További információ a Storage-fiókokról: gyors [útmutató](../../storage/index.yml). További információ a Storage díjszabásáról: [Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/).

1. Várjon, amíg megtörténik a Storage-fiók üzembe helyezése. Ez a folyamat általában néhány percet vesz igénybe.

## <a name="create-a-table-in-your-storage-account"></a>Tábla létrehozása a Storage-fiókban

1. A Azure Portal **kezdőlapján** válassza az **összes erőforrás** megjelenítése lehetőséget a Storage-fiók eléréséhez. Kiválaszthatja az **összes erőforrás** lehetőséget is a Azure Portal bal oldali menüjében.

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png" alt-text="Nyissa meg az Azure Storage-fiókját.":::

1. A Storage-fiók ablaktáblán válassza a **hozzáférési kulcsok** lehetőséget, és másolja a kulcshoz tartozó **kapcsolati sztring** értéket. Mentse ezt az értéket, mert ez a **Storage-fiók kapcsolódási karakterláncának** értéke, amelyet a közzétételi portálon kell megadnia az Azure Marketplace-ajánlathoz tartozó érdeklődők fogadásához.

    Íme egy példa a kapcsolatok karakterláncára.

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.screens.net
    ```

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png" alt-text="Azure Storage-kulcs.":::


1. A Storage-fiók ablaktáblán válassza a **táblák** lehetőséget, majd a táblázat létrehozásához válassza a **+ Table** elemet. Adja meg a tábla nevét, majd kattintson **az OK gombra**. Mentse ezt az értéket, mert szüksége lesz rá, ha be szeretne állítani egy folyamatot, amely e-mail-értesítéseket fogad, ha az érdeklődők érkeznek.

    ![Azure-táblák](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    A Storage-táblában található információk megtekintéséhez [Azure Storage Explorer](https://www.storageexplorer.com) vagy bármely más eszköz használható. Az Azure-táblában lévő adatexportálást is elvégezheti.

## <a name="optional-use-power-automate-to-get-lead-notifications"></a>Választható A Power automatizálás használata a vezető értesítések beszerzéséhez

A [Power automatizálás](/flow/) segítségével automatizálhatja az értesítéseket az Azure Storage-táblához vezető összes alkalommal. Ha nincs fiókja, regisztrálhat [egy ingyenes fiókot](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Példa vezető értesítésre

A példa létrehoz egy folyamatot, amely automatikusan értesítő e-mailt küld, ha új érdeklődőt adnak hozzá az Azure Table Storage-hoz. Ez a példa egy ismétlődést állít be az érdeklődői adatok óránkénti elküldéséhez, ha a tábla tárterülete frissül.

1. Jelentkezzen be a Power automatizáló fiókjába.
1. A bal oldali sávon válassza a **saját folyamatok** lehetőséget.
1. A felső sávon válassza az **+ új** lehetőséget.
1. A legördülő listában válassza a **+ ütemezett – üres** lehetőséget.

   ![Saját folyamatok + ütemezett – üresről](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

1. Az **ütemezett folyamat létrehozása** ablakban az **ismétlődéshez** válassza az **1** értéket az intervallum és az **óra** értéknél. Adja meg a folyamat nevét is, ha szeretné. Válassza a **Létrehozás** lehetőséget.

   >[!NOTE]
   >Bár ez a példa egy órás időközt használ, kiválaszthatja az üzleti igényeinek leginkább megfelelő intervallumot és gyakoriságot.

   ![Ütemezett folyamat létrehozása](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

1. Válassza az **+ Új lépés** gombot.
1. A **válasszon műveletet** ablakban keresse meg a **beolvasás időpontját**. Ezután a **műveletek** területen válassza a **beolvasás ideje** lehetőséget.

   ![Válasszon műveletet](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

1. A **múltbeli idő lekérése** ablakban állítsa az **intervallumot** **1-re**. Az **időegység** legördülő listából válassza az **óra** lehetőséget.

    >[!IMPORTANT]
    >Győződjön meg arról, hogy a 8. lépésben eljuttatott intervallum és időegység egyezik az 5. lépésben megismétlődéshez konfigurált intervallummal és gyakorisággal.

    ![Az elmúlt időintervallum beállítása](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

   >[!TIP]
   >Bármikor ellenőrizheti, hogy az egyes lépések megfelelően vannak-e konfigurálva. A folyamat ellenőrzését a **folyamat menüsorában** válassza ki a flow- **ellenőr** lehetőséget.

   A következő lépésekben csatlakozni fog a táblához, és beállítja a feldolgozási logikát az új érdeklődők kezeléséhez.

1. Válassza az **+ Új lépés** gombot. Ezután keressen rá az **entitások beolvasása** elemre a **művelet választása** ablakban.
1. A **műveletek** területen válassza az **entitások beolvasása (Azure Table Storage)** lehetőséget.
1. Az **Azure Table Storage** ablakban adja meg a következő mezők adatait, majd válassza a **Létrehozás** lehetőséget:

    * **Kapcsolat neve**: adjon meg egy értelmes nevet a folyamat és a tábla közötti kapcsolathoz.
    * **Storage-fiók neve**: adja meg a tábla Storage-fiókjának nevét. Ezt a nevet a Storage-fiók **hozzáférési kulcsainak** oldalán találja.
    * **Megosztott tárolási kulcs**: adja meg a tábla tárolási fiókjának kulcs értékét. Ezt az értéket a Storage-fiók **hozzáférési kulcsainak** oldalán találja.

      ![Azure Table Storage ablak](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

   A **Létrehozás** gombra kattintva megjelenik az **entitások beolvasása** ablak. Itt válassza a **Speciális beállítások megjelenítése** lehetőséget, és adja meg a következő mezők adatait:

   * **Tábla**: válassza ki a tábla nevét ( [tábla létrehozása](#create-a-table-in-your-storage-account)). Az alábbi képen látható, hogy mikor `marketplaceleads` van kiválasztva a táblázat a példához.

     ![Entitások beolvasása ablak](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

   * **Lekérdezés szűrése**: jelölje be ezt a jelölőnégyzetet, és illessze be ezt a függvényt a mezőbe: `Timestamp gt datetime'@{body('Get_past_time')}'`

     ![Entitások beolvasása, lekérdezés szűrése mező](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

1. Most, hogy befejezte a kapcsolódást az Azure-táblához, válassza az **új lépés** lehetőséget, hogy felvegyen egy feltételt az új érdeklődők Azure-táblázatának vizsgálatához.

1. A **művelet kiválasztása** ablakban válassza a **műveletek** lehetőséget. Ezután válassza a **feltétel vezérlőelem** elemet.

    ![Művelet ablakának kiválasztása](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

1. A **feltétel** ablakban válassza az **érték kiválasztása** lehetőséget. Ezután válassza ki a **kifejezést** az előugró ablakban.

1. Illessze be `length(body('Get_entities')?['value'])` az **FX** Box-ba. A függvény hozzáadásához kattintson **az OK gombra** .

1. A feltétel beállításának befejezéséhez:
    1. A Select **érték nagyobb, mint** a legördülő listából.
    2. Adja meg a **0** értéket.

        ![Feltétel ablak](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

   A következő néhány lépésben a feltétel eredményén alapuló műveletet állíthatja be:

   * Ha a feltétel a **nem** értékre van feloldva, semmit sem kell tennie.
   * Ha a feltétel az **Igen** értékre van feloldva, aktiváljon egy műveletet, amely összekapcsolja a munkahelyi vagy iskolai fiókját e-mailek küldéséhez. 

1. Válassza a **művelet hozzáadása** lehetőséget, **Ha igen**.

    ![Feltétel ablak, ha igen, művelet hozzáadása](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

1. Válassza **az E-mail küldése (Office 365 Outlook)** lehetőséget.

    ![Feltétel ablak, ha igen, küldjön e-mailt](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!NOTE]
    >Ha másik e-mail szolgáltatót szeretne használni, keresse meg az **e-mailes értesítés küldése (e-mail)** lehetőséget a művelet helyett. Az utasítások bemutatják, hogyan konfigurálhatja az Office 365 Outlookot, de az utasítások hasonlóak egy másik e-mail-szolgáltatóhoz.

1. Az Office 365 Outlook ablakban adja meg a következő mezők adatait:

    1. Ide: adjon meg egy e-mail-címet mindenki **számára, aki** megkapja ezt az értesítést.
    1. **Tárgy**: adjon meg egy tárgyat az e-mailhez. Ilyenek például az **új érdeklődők!**
    1. **Törzs**: adja hozzá az egyes e-mailekben szerepeltetni kívánt szöveget (opcionális), majd illessze be `body('Get_entities')?['value']` .

    >[!NOTE]
    >További statikus vagy dinamikus adatpontokat is beszúrhat az e-mailek törzséhez.

    ![Feltétel ablak, ha igen, az Office 365 Outlook ablak](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

1. A folyamat mentéséhez válassza a **Mentés** lehetőséget. A Power automatizálás automatikusan ellenőrzi a folyamatot hibák esetén. Ha nincsenek hibák, a folyamat a mentés után elindul.

Az alábbi képen látható egy példa arra, hogy a végső folyamatnak hogyan kell kinéznie.

![Példa a végső folyamatra](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>A folyamat kezelése

A folyamat a futtatása után egyszerűen kezelhető. A folyamat teljes mértékben szabályozható. Leállíthatja például, szerkesztheti, megtekintheti a futtatási előzményeket, és lekérheti az elemzést. Az alábbi képen a folyamatok kezeléséhez rendelkezésre álló beállítások láthatók.

 ![Folyamat kezelése](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

A folyamat addig fut, amíg le nem állítja a **folyamatot a kikapcsolás** kikapcsolása lehetőség használatával.

Ha nem kap vezető e-mailes értesítéseket, az azt jelenti, hogy az új érdeklődők nem lettek hozzáadva az Azure-táblához. Ha bármilyen folyamat meghibásodása van, egy e-mailt fog kapni, mint például ez a példa.

 ![Sikertelen folyamat – e-mail-értesítés](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Az ajánlat konfigurálása az érdeklődők Azure-táblázatba való küldésére

Ha készen áll az ajánlathoz tartozó érdeklődő-felügyeleti információk konfigurálására a közzétételi portálon, kövesse az alábbi lépéseket.

1. Nyissa meg az ajánlat **telepítési** lapját.

1. Az **ügyfél-érdeklődők** szakaszban válassza a **kapcsolat** lehetőséget.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-azure-table/customer-leads.png" alt-text="Ügyfél-érdeklődők":::

1. A **kapcsolat részletei** előugró ablakban válassza ki az **Azure Table** elemet az **érdeklődő célhelyéhez**. 
     ![Érdeklődők felügyelete, kapcsolat részletei](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

1. Illessze be a létrehozott Azure Storage-fiókhoz tartozó, a **Storage-fiók kapcsolatainak karakterlánca** mezőben a korábbi lépéseket követve.
     ![Érdeklődők felügyelete, kapcsolat részletei Storage-fiók](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

1. **Kapcsolattartási e-mail cím**: adja meg az e-maileket a vállalatnál, akinek új érdeklődő fogadása esetén e-mail-értesítéseket kell kapnia. A pontosvesszővel elválasztva több e-mailt is megadhat.

1. Válassza az **OK** lehetőséget.

Az **Érvényesítés** gombra kattintva győződjön meg arról, hogy sikeresen csatlakozott egy érdeklődői célhoz. Ha a művelet sikeres, a vezető célhelyen egy teszt vezet.

>[!NOTE]
>Be kell fejeznie az ajánlat többi részének konfigurálását, és közzé kell tennie az ajánlathoz tartozó érdeklődők fogadása előtt.

Az érdeklődők létrehozásakor a Microsoft elküldi az érdeklődőket az Azure-táblába. Ha konfigurált egy folyamatot, a rendszer e-mailt küld a konfigurált e-mail-címre is.

## <a name="next-steps"></a>Következő lépések

- [Az érdeklődők felügyeletével kapcsolatos gyakori kérdések és hibaelhárítás](../lead-management-faq.md)
