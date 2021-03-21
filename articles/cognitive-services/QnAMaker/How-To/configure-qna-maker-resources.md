---
title: QnA Maker szolgáltatás konfigurálása – QnA Maker
description: Ez a dokumentum a QnA Maker erőforrásaihoz tartozó speciális konfigurációkat ismerteti.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: 3c6f75eafad51c99f60b78ce49862d2488d5926f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102220127"
---
# <a name="configure-qna-maker-resources"></a>QnA Maker erőforrások konfigurálása

A felhasználó beállíthatja, hogy QnA Maker egy másik kognitív keresési erőforrást használjon. Az App Service beállításait is megadhatják, ha QnA Maker GA-t használnak.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

### <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>QnA Maker konfigurálása különböző Cognitive Search-erőforrások használatára

Ha létrehoz egy QnA szolgáltatást és annak függőségeit (például a keresést) a portálon keresztül, akkor létrejön egy keresési szolgáltatás, amely a QnA Maker szolgáltatáshoz van csatolva. Az erőforrások létrehozása után frissítheti az App Service beállítást egy korábban meglévő keresési szolgáltatás használatára, és eltávolíthatja az imént létrehozott szolgáltatást.

A QnA Maker **app Service** erőforrása a Cognitive Search erőforrást használja. A QnA Maker által használt Cognitive Search erőforrás módosításához módosítania kell a beállítást a Azure Portalban.

1. Kérje le a használni QnA Maker kívánt Cognitive Search erőforrás **rendszergazdai kulcsát** és **nevét** .

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és keresse meg a QnA Maker erőforráshoz társított **app Service** . Mindkettő ugyanazzal a névvel van ellátva.

1. Válassza a **Beállítások**, majd a **konfiguráció** lehetőséget. Ekkor megjelenik a QnA Maker App Service összes meglévő beállítása.

    > [!div class="mx-imgBorder"]
    > ![A App Service konfigurációs beállításait bemutató Azure Portal képernyőképe](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. Módosítsa a következő kulcsok értékeit:

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. Az új beállítások használatához újra kell indítania az App Service-t. Válassza az **Áttekintés**, majd az **Újraindítás** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a Azure Portal újraindításáról App Service a konfigurációs beállítások módosítása után](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Ha Azure Resource Manager sablonokon keresztül hoz létre QnA szolgáltatást, az összes erőforrást létrehozhatja, és szabályozhatja a App Service létrehozását egy meglévő keresési szolgáltatás használatára.

További információ a App Service [alkalmazás beállításainak](../../../app-service/configure-common.md#configure-app-settings)konfigurálásáról.

### <a name="get-the-latest-runtime-updates"></a>A legújabb futtatókörnyezet-frissítések beszerzése

Az QnAMaker Runtime a Azure App Service példány része, amelyet akkor telepítenek, amikor [létrehoz egy QnAMaker szolgáltatást](./set-up-qnamaker-service-azure.md) a Azure Portal. A rendszer rendszeresen frissíti a frissítéseket a futtatókörnyezetben. A QnA Maker App Service példánya automatikus frissítési módban van az április 2019-es hely kiterjesztésének kiadása után (5. verzió +). Ez a frissítés úgy lett kialakítva, hogy a frissítések során ne legyenek nulla állásidő.

A jelenlegi verzióját a következő címen tekintheti meg: https://www.qnamaker.ai/UserSettings . Ha a verziószáma 5. x verziónál régebbi, a legújabb frissítések alkalmazásához újra kell indítania App Servicet:

1. Lépjen a QnAMaker szolgáltatásra (erőforráscsoport) a [Azure Portal](https://portal.azure.com).

    > [!div class="mx-imgBorder"]
    > ![QnAMaker Azure-erőforráscsoport](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Válassza ki a App Service példányt, és nyissa meg az **Áttekintés** szakaszt.

    > [!div class="mx-imgBorder"]
    > ![QnAMaker App Service-példány](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. App Service újraindítása. A frissítési folyamatnak néhány másodpercen belül be kell fejeződnie. A QnAMaker szolgáltatást használó függő alkalmazások vagy robotok nem lesznek elérhetők a végfelhasználók számára az újraindítási időszakban.

    ![A QnAMaker App Service példány újraindítása](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

### <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>Az App Service tétlen beállítása az időkorlát elkerüléséhez

Az App Service, amely a közzétett Tudásbázis QnA Maker előrejelzési futtatókörnyezetét szolgálja, Üresjárati időkorlát-konfigurációval rendelkezik, amely alapértelmezés szerint automatikusan időtúllépést okoz, ha a szolgáltatás üresjáratban van. A QnA Maker esetében ez azt jelenti, hogy az előrejelzési futtatókörnyezet generateAnswer API-ját időnként a forgalom nélküli időszakok után időtúllépés jelzi.

Annak érdekében, hogy az előrejelzési végpont alkalmazás betöltődik, még akkor is, ha nincs forgalom, állítsa az inaktív állapotot mindig bekapcsolt értékre.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keresse meg és válassza ki a QnA Maker erőforrás app Service-t. Ugyanaz a neve, mint a QnA Maker erőforrásnak, de más **típusú** app Service is fog rendelkezni.
1. Keresse meg a **beállításokat** , majd válassza a **Konfigurálás** lehetőséget.
1. A konfiguráció ablaktáblán válassza az **általános beállítások**, majd a **mindig bekapcsolva** lehetőséget, **és válassza a be értéket** .

    > [!div class="mx-imgBorder"]
    > ![A konfiguráció ablaktáblán válassza a * * általános beállítások * * lehetőséget, majd a * * always on * * elemet, és válassza a * * * * elemet az értékként.](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. A konfiguráció mentéséhez kattintson a **Mentés** gombra.
1. A rendszer megkérdezi, hogy szeretné-e újraindítani az alkalmazást az új beállítás használatához. Válassza a **Folytatás** lehetőséget.

További információ a App Service [általános beállításainak](../../../app-service/configure-common.md#configure-general-settings)konfigurálásáról.

### <a name="business-continuity-with-traffic-manager"></a>Üzletmenet-folytonosság a Traffic Managerrel

Az üzletmenet-folytonossági terv elsődleges célja, hogy egy rugalmas Tudásbázis-végpontot hozzon létre, amely nem gondoskodik a bot vagy az alkalmazás elfogyasztásának leállásáról.

> [!div class="mx-imgBorder"]
> ![QnA Maker BCP-terv](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

A fentiekben képviselt magas szintű ötlet a következő:

1. Két párhuzamos QnA Maker- [szolgáltatás](set-up-qnamaker-service-azure.md) beállítása az [Azure párosított régiókban](../../../best-practices-availability-paired-regions.md).

1. [Készítsen biztonsági másolatot](../../../app-service/manage-backup.md) az elsődleges QnA Maker app Service-ről, és [állítsa vissza](../../../app-service/web-sites-restore.md) a másodlagos telepítésben. Ez biztosítja, hogy mindkét beállítás ugyanazzal az állomásnévvel és kulcsokkal működjön.

1. Tartsa szinkronban az elsődleges és a másodlagos Azure Search-indexeket. A GitHub [-minta használatával](https://github.com/pchoudhari/QnAMakerBackupRestore) megtekintheti, hogyan állíthatja vissza az Azure indexeket.

1. A Application Insights biztonsági mentése [folyamatos exportálással](../../../azure-monitor/app/export-telemetry.md).

1. Az elsődleges és másodlagos csomagok beállítása után a [Traffic Manager](../../../traffic-manager/traffic-manager-overview.md) segítségével konfigurálja a két végpontot, és állítson be útválasztási módszert.

1. Létre kell hoznia egy Transport Layer Security (TLS), korábbi nevén SSL (SSL) tanúsítványt a Traffic Manager-végponthoz. [A TLS/SSL-tanúsítvány kötése](../../../app-service/configure-ssl-bindings.md) az App Servicesben.

1. Végül használja a Traffic Manager-végpontot a robotban vagy az alkalmazásban.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

### <a name="configure-qna-maker-managed-preview-service-to-use-different-cognitive-search-resource"></a>QnA Maker felügyelt (előzetes verzió) szolgáltatás konfigurálása különböző Cognitive Search-erőforrások használatára

Ha létrehoz egy QnA szolgáltatást (előzetes verzió) és annak függőségeit (például a keresést) a portálon keresztül, a rendszer létrehoz egy keresési szolgáltatást, és a QnA Maker felügyelt (előzetes verzió) szolgáltatáshoz kapcsolódik. Miután létrehozta ezeket az erőforrásokat, a **Configuration (konfiguráció** ) lapon frissítheti a Search szolgáltatást.

1. Lépjen a QnA Maker felügyelt (előzetes verzió) szolgáltatásra a Azure Portal.

1. Válassza a **konfiguráció** lehetőséget, majd válassza ki azt az Azure Cognitive Search szolgáltatást, amelyet a QnA Maker felügyelt (előzetes verzió) szolgáltatással szeretne összekapcsolni.

    ![A QnA Maker felügyelt (előzetes verzió) konfigurációs oldalának képernyőképe](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-configuration.png)

1. Kattintson a **Mentés** gombra.

> [!NOTE]
> Ha módosítja a QnA Makerhoz társított Azure Search-szolgáltatást, a rendszer elveszíti az összes már meglévő tudásbázishoz való hozzáférést. A Azure Search szolgáltatás módosítása előtt győződjön meg arról, hogy a meglévő tudásbázisokat exportálja.

---
