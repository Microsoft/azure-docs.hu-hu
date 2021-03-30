---
title: Automatikus leállítási házirendek kezelése Azure DevTest Labs és számítási virtuális gépeken | Microsoft Docs
description: Megtudhatja, hogyan állíthatja be az automatikus leállítási szabályzatot egy laborhoz, hogy a virtuális gépek automatikusan leálljanak, ha nincsenek használatban.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: cd7974580ea30c9d0591c88380a4e626711bad1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93318978"
---
# <a name="configure-autoshutdown-for-lab-and-compute-virtual-machines-in-azure-devtest-labs"></a>Az automatikus leállítás konfigurálása a tesztkörnyezet és a számítási virtuális gépek számára a Azure DevTest Labsban

Ez a cikk bemutatja, hogyan konfigurálhatja a laboratóriumi virtuális gépek automatikus leállítási beállításait a DevTest Labs-ben és a számítási virtuális gépeken.

## <a name="configure-autoshutdown-for-lab-vms-devtest-labs"></a>Laboratóriumi virtuális gépek automatikus leállításának konfigurálása (DevTest Labs)

A Azure DevTest Labs lehetővé teszi, hogy az egyes laborokhoz tartozó szabályzatok (beállítások) kezelésével szabályozza a költségeket, és csökkentse a saját laborokban lévő hulladékokat. Ez a cikk bemutatja, hogyan konfigurálhatja az automatikus leállítási szabályzatot egy laborhoz.  Azt is bemutatja, hogyan konfigurálható az automatikus leállítási beállítások egy Tesztkörnyezet virtuális gépe számára. Ha meg szeretné tekinteni, hogyan kell beállítani az összes tesztkörnyezet házirendjét, tekintse meg [a labor-házirendek definiálása a Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

### <a name="set-autoshutdown-policy-for-a-lab"></a>Tesztkörnyezet automatikus leállítási házirendjének beállítása

A labor tulajdonosaként beállíthatja a tesztkörnyezetben lévő összes virtuális gép leállítási ütemtervét. Ennek köszönhetően a nem használt gépektől (inaktív) származó költségek is megmenthetők. Központilag kényszerítheti a leállítási szabályzatokat az összes laboratóriumi virtuális gépen, és mentheti a labor-felhasználókat arra, hogy ütemezést állítsanak be az egyes gépekhez. Ez a funkció lehetővé teszi a szabályzat beállítását a labor-ütemterven, amely lehetővé teszi a labor felhasználók számára, hogy teljes körűen szabályozzák a virtuális gép leállítási ütemtervét, hogy a virtuális gép leállítása ne legyen szabályozható. A labor tulajdonosaként a következő lépésekkel konfigurálhatja ezt a házirendet:

1. A labor kezdőlapján válassza a **konfiguráció és szabályzatok** lehetőséget.
2. Válassza a bal oldali menü **ütemtervek** szakaszának **automatikus leállítás házirend** elemét.
3. Válasszon egyet a lehetőségek közül. A következő fejezetekben részletesebb információkat talál ezekről a lehetőségekről:

    ![Házirend-beállítások automatikus leállítása](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

> [!IMPORTANT]
> A leállítási szabályzat módosításai csak a tesztkörnyezetben létrehozott új virtuális gépekre, és nem a már meglévő virtuális gépekre vonatkoznak.

### <a name="configure-autoshutdown-settings"></a>Automatikus leállítás beállításainak konfigurálása

Az automatikus leállítási szabályzat segít a laborban lévő virtuális gépek leállításának időpontjának meghatározásában.

A laborhoz tartozó szabályzatok megtekintéséhez vagy módosításához kövesse az alábbi lépéseket:

1. A labor kezdőlapján válassza a **konfiguráció és szabályzatok** lehetőséget.
2. Válassza az **automatikus leállítás** lehetőséget a bal oldali menü **ütemtervek** szakaszában.
3. Ezzel **a** beállítással engedélyezheti a szabályzatot **, és** letilthatja azt.
     ![Automatikus leállítás részletei](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
4. Ha engedélyezi ezt a házirendet, az aktuális laborban lévő összes virtuális gép leállításához válassza ki az időt (és az időzónát).
5. Adja meg az **Igen** vagy a **nem** lehetőséget arra a lehetőségre, hogy 30 perccel a megadott automatikus leállítási idő előtt küldjön értesítést. Ha az **Igen** lehetőséget választja, adjon meg egy WEBHOOK URL-címet vagy e-mail-címet, amely megadja, hogy hová szeretné közzétenni vagy elküldeni az értesítést. A felhasználó értesítést kap, és a Leállítás késleltetését adja meg. További információ: [értesítések](#notifications) szakasz.
6. Kattintson a **Mentés** gombra.

    Alapértelmezés szerint, ha engedélyezve van, ez a házirend az aktuális laborban található összes virtuális gépre vonatkozik. Ha egy adott virtuális gépről el szeretné távolítani ezt a beállítást, nyissa meg a virtuális gép felügyeleti paneljét, és módosítsa az **automatikus leállítási** beállítást.

> [!NOTE]
> Ha a labor vagy egy adott labor virtuális gép automatikus leállítási ütemezését a jelenlegi ütemezett időponttól számított 30 percen belül frissíti, a frissített leállítási idő a következő napi ütemezés szerint fog vonatkozni.

### <a name="user-sets-a-schedule-and-can-opt-out"></a>A felhasználó beállít egy ütemtervet, és letilthatja

Ha a laborban ezt a házirendet állítja be, akkor a labor felhasználói felülbírálják vagy letiltják a labor-ütemtervet. Ez a beállítás lehetővé teszi a tesztkörnyezet felhasználói számára a virtuális gépek automatikus leállítási ütemtervének teljes körű felügyeletét. A labor felhasználói nem változnak a virtuális gép automatikus leállításának ütemterve lapon.

![Automatikus leállítási házirend-beállítás – 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>A felhasználó beállít egy ütemtervet, és nem tud letiltani

Ha a laborban ezt a házirendet állítja be, a labor felhasználói felülbírálhatja a laborok ütemtervét. Azonban nem tilthatják le az automatikus leállítási szabályzatot. Ez a beállítás biztosítja, hogy a laborban lévő összes gép automatikus leállítási ütemterv alá kerüljön. A labor-felhasználók frissíthetik a virtuális gépek automatikus leállítási ütemtervét, és beállítják a leállítási értesítéseket.

![Automatikus leállítási házirend-beállítás – 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>A felhasználónak nincs hozzáférése a labor rendszergazdája által beállított ütemtervhez

Ha a laborban ezt a házirendet állítja be, a labor-felhasználók nem tudják felülbírálni vagy letiltani a labor-ütemtervet. Ez a beállítás a tesztkörnyezet rendszergazdájának teljes körű vezérlését kínálja a laborban lévő összes gép számára. A labor felhasználói csak automatikus leállítási értesítéseket állíthatnak be a virtuális gépekhez.

![Automatikus leállítás házirend-beállítás – 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="configure-autoshutdown-for-compute-vms"></a>Automatikus leállítás konfigurálása a számítási virtuális gépekhez

1. A **virtuális gép** lapon válassza az **automatikus leállítás** lehetőséget a **műveletek** szakasz bal oldali menüjében.
2. Az **automatikus leállítás** lapon válassza **a** be lehetőséget a szabályzat engedélyezéséhez **, majd a** letiltásához.
3. Ha engedélyezi ezt a házirendet, itt adhatja meg a virtuális gép leállításának **időpontját** (és **időzónáját**).
4. Válassza az **Igen** vagy a **nem** lehetőséget, ha 30 perccel a megadott automatikus leállítási időpont előtt szeretne értesítést küldeni. Ha az **Igen** lehetőséget választja, adjon meg egy WEBHOOK URL-címet vagy e-mail-címet, amely megadja, hogy hová szeretné közzétenni vagy elküldeni az értesítést. A felhasználó értesítést kap, és a Leállítás késleltetését adja meg. További információ: [értesítések](#notifications) szakasz.
5. Kattintson a **Mentés** gombra.

    ![Automatikus leállítás beállítása számítási virtuális géphez](./media/devtest-lab-auto-shutdown/comnpute-auto-shutdown.png)

### <a name="view-activity-logs-for-auto-shutdown-updates"></a>Eseménynaplók megtekintése az automatikus leállítási frissítésekhez

Amikor frissíti az automatikus leállítási beállítást, megjelenik a virtuális gép tevékenységi naplójában naplózott tevékenység.

1. A [Azure Portal](https://portal.azure.com)navigáljon a virtuális gép kezdőlapjára.
2. Válassza a bal oldali menüből a **műveletnapló** elemet.
3. **Erőforrás eltávolítása: mycomputevm** a szűrőkből.
4. Győződjön meg arról, hogy a tevékenység naplójában megjelenik az **ütemterv hozzáadása vagy módosítása** művelet. Ha nem látja, várjon egy kis ideig, és frissítse a tevékenység naplóját.

    ![Tevékenység naplójának bejegyzése](./media/devtest-lab-auto-shutdown/activity-log-entry.png)
5. Az **Összefoglalás** lapon a következő információk megjelenítéséhez válassza a **Hozzáadás vagy az ütemterv módosítása** műveletet:

    - Művelet neve (ütemtervek hozzáadása vagy módosítása)
    - Az automatikus leállítási beállítás frissítésének dátuma és időpontja.
    - A beállítást frissített felhasználó e-mail-címe.

        ![Tevékenység naplójának bejegyzéseinek összefoglalása](./media/devtest-lab-auto-shutdown/activity-log-entry-summary.png)
6. Váltson az **ütemtervek hozzáadása vagy módosítása** lap **change History (változások előzményei** ) lapjára, ahol megtekintheti a beállítás módosítási előzményeit. A következő példában a leállítási idő 7 és 6 óra között lett módosítva április 10-én, 2020-kor: 15:18:47 EST. És a beállítás le lett tiltva a 15:25:09 EST-on.

    ![Activity napló – változások előzményei](./media/devtest-lab-auto-shutdown/activity-log-entry-change-history.png)
7. A művelet további részleteinek megtekintéséhez váltson a **JSON** lapra az **ütemterv hozzáadása vagy módosítása** lapon.

## <a name="notifications"></a>Értesítések

Ha az automatikus leállítás be van állítva, az értesítések a labor felhasználói számára 30 perccel az automatikus leállítás előtt lesznek elküldve, ha a virtuális gépek érintettek lesznek. Ez a beállítás lehetővé teszi a laboratóriumi felhasználók számára, hogy a leállítás előtt megmentsek a munkájukat. Az értesítés az egyes virtuális gépekre mutató hivatkozásokat is tartalmaz a következő műveletekhez, ha valakinek továbbra is működnie kell a virtuális gépen.

- Automatikus leállítás kihagyása erre az időre
- Az automatikus leállítás késleltetése egy órára
- Az automatikus leállítás késleltetése 2 órán keresztül

A rendszer értesítést küld a webhook URL-címére, ha meg van adva egy webhook.  Ha meg van adva e-mail-cím az automatikus leállítási beállításokban, a rendszer e-mailt küld az e-mail-címre. A webhookok lehetővé teszik bizonyos eseményekre előfizetett integrációk készítését vagy beállítását. Az események egyikének aktiválásakor a DevTest Labs HTTP POST-adattartalmat küld a webhook konfigurált URL-címére. További információ a webhookokra való reagálásról: [Azure FUNCTIONS http-eseményindítók és-kötések – áttekintés](../azure-functions/functions-bindings-http-webhook.md) vagy [http-eseményindító hozzáadása a Azure Logic Appshoz](../connectors/connectors-native-http.md#add-an-http-trigger).

Javasoljuk, hogy használjon webhookokat, mivel azokat számos különböző alkalmazás, például a Azure Logic Apps és a Slack széles körben támogatja.  A webhookok lehetővé teszik saját módszer megvalósítását értesítések küldéséhez. Ez a cikk bemutatja, hogyan konfigurálhatja az automatikus leállítási értesítést, hogy a Azure Logic Apps használatával küldjön e-mailt a virtuális gép tulajdonosának. Először is ugorjon végig az alapszintű lépéseken, amelyekkel engedélyezheti az automatikus leállítási értesítést a laborban.

### <a name="create-a-logic-app-that-receives-email-notifications"></a>E-mail értesítéseket fogadó logikai alkalmazás létrehozása

[Azure Logic apps](../logic-apps/logic-apps-overview.md) számos összekötőt biztosít, amelyek megkönnyítik a szolgáltatások integrálását más ügyfelekkel, például az Office 365 és a Twitter használatával. A logikai alkalmazás e-mailes értesítéshez való beállításának lépései a következő négy fázisra oszthatók fel:

- Hozzon létre egy logikai alkalmazást.
- Konfigurálja a beépített sablont.
- Integrálás az e-mail ügyfélprogrammal
- A webhook URL-címének beolvasása.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

Első lépésként hozzon létre egy logikai alkalmazást az Azure-előfizetésében az alábbi lépések segítségével:

1. Válassza az **+ erőforrás létrehozása** lehetőséget a bal oldali menüben, majd az **integráció** lehetőséget, és válassza a **logikai alkalmazás** lehetőséget.

    ![Új logikai alkalmazás menü](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. A **logikai alkalmazás létrehozása** lapon kövesse az alábbi lépéseket:
    1. Adja meg a logikai alkalmazás **nevét** .
    2. Válassza ki az Azure- **előfizetését**.
    3. Hozzon létre egy új **erőforráscsoportot** , vagy válasszon ki egy meglévő erőforráscsoportot.
    4. Válassza ki a logikai alkalmazás **helyét** .

        ![Új logikai alkalmazás – beállítások](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. Az **értesítések** területen válassza az **Ugrás erőforráshoz** lehetőséget az értesítésben.

    ![Erőforrás megnyitása](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. Válassza a **Logic app Designer** elemet a **telepítési eszközök** kategóriában.

    ![HTTP-kérelem/válasz kiválasztása](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. A **http-kérelem – válasz** lapon válassza a **sablon használata** lehetőséget.

    ![Válassza a sablon használata lehetőséget](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. Másolja a következő JSON-t a **kérelem törzse JSON-séma** szakaszba:

    ```json
    {
        "$schema": "http://json-schema.org/draft-04/schema#",
        "properties": {
            "delayUrl120": {
                "type": "string"
            },
            "delayUrl60": {
                "type": "string"
            },
            "eventType": {
                "type": "string"
            },
            "guid": {
                "type": "string"
            },
            "labName": {
                "type": "string"
            },
            "owner": {
                "type": "string"
            },
            "resourceGroupName": {
                "type": "string"
            },
            "skipUrl": {
                "type": "string"
            },
            "subscriptionId": {
                "type": "string"
            },
            "text": {
                "type": "string"
            },
            "vmName": {
                "type": "string"
            },
            "vmUrl": {
                "type": "string"
            },
            "minutesUntilShutdown": {
                "type": "string"
            }
        },
        "required": [
            "skipUrl",
            "delayUrl60",
            "delayUrl120",
            "vmName",
            "guid",
            "owner",
            "eventType",
            "text",
            "subscriptionId",
            "resourceGroupName",
            "labName",
            "vmUrl",
            "minutesUntilShutdown"
        ],
        "type": "object"
    }
    ```

    ![A "kérelem törzse JSON-sémát" megjelenítő képernyőkép.](./media/devtest-lab-auto-shutdown/request-json.png)
7. Válassza az **+ új lépés** lehetőséget a tervezőben, és kövesse az alábbi lépéseket:
    1. Keresse meg az **Office 365 Outlook – E-mail küldése lehetőséget**.
    2. Válassza **az E-mail küldése** **műveletből** lehetőséget.

        ![E-mail küldése lehetőség](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. A **Bejelentkezés** gombra kattintva jelentkezzen be az e-mail-fiókjába.
    4. Válassza ki a mezőt, és válassza **a** tulajdonos lehetőséget.
    5. Válassza ki a **Tárgy** elemet, és adja meg az e-mail-értesítés tárgyát. Például: "a gép vmName leállítása a laborhoz: labName".
    6. Válassza ki a **törzs** elemet, és adja meg az e-mail értesítés törzsének tartalmát. Például: "a vmName 15 percen belül leállításra van ütemezve. Hagyja ki ezt a leállítást az URL-cím lehetőségre kattintva. Késleltetés leállítása egy órára: delayUrl60. Késleltetés leállítása 2 órára: delayUrl120. "

        ![Kérelem törzsének JSON-sémája](./media/devtest-lab-auto-shutdown/email-options.png)
8. Válassza az eszköztár **Save** (Mentés) elemét. Most másolhatja a **http post URL-címet**. A Másolás gombra kattintva másolja az URL-címet a vágólapra.

    ![Webhook URL-címe](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="next-steps"></a>Következő lépések

Az összes házirend beállításának megismeréséhez tekintse meg [a tesztkörnyezet-szabályzatok meghatározása a Azure DevTest Labsban](devtest-lab-set-lab-policy.md)című témakört.
