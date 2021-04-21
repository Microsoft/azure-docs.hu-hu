---
title: Az Azure Logic Apps összekötői
description: Összekötők áttekintése automatizált munkafolyamatok Azure Logic Apps. Megtudhatja, hogyan működnek a különböző típusú összekötők, eseményindítók és műveletek.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: 9e138cb7fb591728e7bc6a02ff61b3167d13da30
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771352"
---
# <a name="connectors-for-azure-logic-apps"></a>Az Azure Logic Apps összekötői

Ebben Azure Logic Apps *összekötők* segítségével gyorsan hozzáférhet más alkalmazásokból, szolgáltatásokból, rendszerekből, protokollokból és platformokról származó eseményekhez, adatokhoz és műveletekhez. Ezeket a feladatokat gyakran további kód nélkül is el lehet elvégezni. Összekötők használatával olyan munkafolyamatokat Logic Apps, amelyek ezeket az információkat felhőalapú, helyszíni vagy hibrid környezetekben használják.

Több száz összekötő érhető el a Logic Apps. Ennek eredményeképpen ez a dokumentáció a leggyakoribb és leggyakrabban használt összekötőkre összpontosít a Logic Apps. Az összekötők, a Microsoft Logic Apps és a Microsoft Power Automate összekötőinek teljes Power Apps lásd: [Összekötők dokumentációja.](/connectors) 

A díjszabásról az Logic Apps díjszabási modelljében [és](../logic-apps/logic-apps-pricing.md) [Logic Apps talál.](https://azure.microsoft.com/pricing/details/logic-apps/)

Ha a logikai alkalmazást olyan szolgáltatással vagy API-val integrálja, amely nem rendelkezik összekötővel, hívja meg a szolgáltatást egy protokollon, például HTTP-n keresztül, vagy hozzon létre egy [egyéni összekötőt.](#custom-apis-and-connectors)

## <a name="what-are-connectors"></a>Mik azok az összekötők?

A Azure Logic Apps összekötők olyan  eseményindítókat és műveleteket biztosítanak, amelyek a logikai alkalmazás munkafolyamatában feladatok végrehajtásához használhatók.  Minden eseményindító és művelet rendelkezik konfigurálható tulajdonságokkal. Egyes eseményindítókhoz és [](#connection-configuration) műveletekhez kapcsolatokat kell létrehozni és konfigurálni, hogy a munkafolyamat hozzáférjen egy adott szolgáltatáshoz vagy rendszerhez.

### <a name="triggers"></a>Triggerek

Az *eseményindító* minden munkafolyamat első lépése, amely meghatározza a munkafolyamatot elindító eseményt. Az eseményindítóknak több típusa létezik:

- *A lekérdezéses eseményindítók* a megadott ütemezés szerint rendszeresen ellenőrzik az adott szolgáltatást vagy rendszert az új adatok vagy egy adott esemény ellenőrzéséhez. Ha új adatok érhetők el, vagy az adott esemény bekövetkezik, ezek az eseményindítók létrehoznak és futtatnak egy új munkafolyamat-példányt. Az új példány ezután használhatja a bemenetként átadott adatokat.
- *A leküldéses eseményindítók* lekérdezés nélkül figyelik az új adatokat vagy egy esemény bekövetkezését. Amikor új adatok érhetők el, vagy ha az esemény bekövetkezik, ezek az eseményindítók létrehoznak és futtatnak egy új logikai alkalmazást. Ez az új példány ezután felhasználhatja a bemenetként átadott adatokat.

Előfordulhat például, hogy olyan munkafolyamatot szeretne felépíteni, amely valamit tesz, amikor feltölt egy fájlt az FTP-kiszolgálóra. A munkafolyamat első lépéseként hozzáadhatja **a Fájl** hozzáadásakor vagy módosításakor nevű FTP-összekötő eseményindítót. Ezután megadhat egy ütemezést, amely rendszeresen ellenőrzi a feltöltési eseményeket.

Az eseményindító emellett továbbítja a bemeneteket és egyéb szükséges adatokat a munkafolyamatba, ahol a későbbi műveletek hivatkozni tudnak az adatokra, és felhasználhatja őket a munkafolyamatban. Előfordulhat például, hogy az Új e-mail érkezésekor nevű Office 365 Outlook-eseményindítót **szeretné használni.** Ez az eseményindító konfigurálható úgy, hogy minden új e-mail tartalma, például a feladó, a tárgysor, a törzs, a mellékletek stb. tartalmát továbbkedje. Ezután műveletek használatával feldolgozhatja az adatokat a logikai alkalmazásban.

### <a name="actions"></a>Műveletek

A *művelet* olyan művelet, amely követi az eseményindítót, és végrehajt valamilyen feladatot a munkafolyamatban. A logikai alkalmazásban több műveletet is használhat. Előfordulhat például, hogy egy SQL-eseményindító új ügyféladatokat észlel egy SQL-adatbázisban. A munkafolyamat tartalmazhat egy első SQL-műveletet, amely lekérte az ügyféladatokat, majd egy másik műveletet, amely nem feltétlenül EGY SQL-művelet, amely feldolgozza az adatokat.

## <a name="connector-categories"></a>Összekötő-kategóriák

A Logic Apps általában az eseményindítók és műveletek beépített vagy felügyelt összekötő-verziói vannak. Mindkét verzióban kevés eseményindító és művelet érhető el. Az egyes verziók attól függnek, hogy több-bérlős logikai alkalmazást vagy egybérlős logikai alkalmazást hoz létre, amely jelenleg csak az [előzetes verzióban Logic Apps érhető el.](../logic-apps/logic-apps-overview-preview.md)

[A beépített eseményindítók](built-in.md) és műveletek natív módon futnak a Logic Apps,nincs szükség kapcsolatok létrehozására és az alábbi feladatok elvégzésére:

- [Futtassa a kódot a munkafolyamatokban.](built-in.md#run-code-from-workflows)
- [Az adatok rendszerezése és szabályozása.](built-in.md#control-workflow)
- [Adatok kezelése vagy kezelése.](built-in.md#manage-or-manipulate-data)

[A felügyelt összekötőket](managed.md) a Microsoft telepíti, üzemelteti és kezeli. Ezek az összekötők eseményindítókat és műveleteket biztosítanak a felhőszolgáltatásokhoz, a helyszíni rendszerekhez vagy mindkettőhöz. Ezek a következők:

- [Helyszíni összekötők,](managed.md#on-premises-connectors) amelyek segítségével hozzáférhet a helyszíni rendszerekben található adatokhoz és erőforrásokhoz.
- [Vállalati összekötők,](managed.md#enterprise-connectors)amelyek a vállalati rendszerekhez hozzáférést Logic Apps összekötők egyes verziói.
- [Integrációs fiókok összekötői,](managed.md#integration-account-connectors)amelyek támogatják a vállalat közötti (B2B) kommunikációs forgatókönyveket.
- [integrációs szolgáltatási környezet (ISE)](managed.md#ise-connectors) összekötők, amelyek felügyelt összekötők kis csoportja, és csak az [ISE-k számára érhetők el.](#ise-and-connectors)

## <a name="connection-configuration"></a>Kapcsolat konfigurációja

A legtöbb összekötő megköveteli, hogy először kapcsolatot hozzon létre *a* célszolgáltatással vagy rendszerrel, mielőtt az eseményindítóit vagy a logikai alkalmazásban műveleteket használhat. A kapcsolat létrehozásához hitelesítenie kell identitását a fiók hitelesítő adataival és néha más kapcsolati adatokkal. Ahhoz például, hogy a munkafolyamat hozzáférjen az Office 365 Outlook e-mail-fiókjához egy logikai alkalmazásban, engedélyeznie kell az ahhoz a fiókhoz való csatlakozást.

Az Azure Active Directory (Azure AD) OAuth-hitelesítést (például Office 365, Salesforce vagy GitHub) felhasználó összekötők esetén [](../security/fundamentals/encryption-overview.md) be kell jelentkeznie a szolgáltatásba, ahol a hozzáférési jogkivonat titkosítva van, és biztonságosan van tárolva egy Azure titkos kódban. Más összekötőknek, például az FTP-nek és az SQL-nek olyan kapcsolatra van szükségük, amely tartalmazza a konfigurációs adatokat, például a kiszolgáló címét, felhasználónevét és jelszavát. Ezek a kapcsolatkonfigurációs adatok titkosítva és biztonságosan vannak tárolva [az Azure-ban.](../security/fundamentals/encryption-overview.md)

A létrehozott kapcsolatok addig férhetnek hozzá a célszolgáltatáshoz vagy rendszerhez, amíg a szolgáltatás vagy a rendszer engedélyezi. Az Azure AD OAuth-kapcsolatokat (például az Office 365-öt és a Dynamics-ot) Logic Apps a hozzáférési jogkivonatokat határozatlan időre frissíti. Más szolgáltatásoknál előfordulhat, hogy a Logic Apps mennyi ideig használhat jogkivonatot frissítés nélkül. Bizonyos műveletek, például a jelszó módosítása általában érvényteleníti az összes hozzáférési jogkivonatot.

Bár a kapcsolatokat a munkafolyamaton belül hozza létre, a kapcsolatok különálló Azure-erőforrások, amelyek saját erőforrás-definícióval vannak megszabadva. A kapcsolati erőforrás-definíciók áttekintéséhez töltse le a logikai alkalmazást az [Azure-ból a Visual Studio.](../logic-apps/manage-logic-apps-with-visual-studio.md) Ez a módszer a legegyszerűbb módszer egy érvényes, paraméteres logikaialkalmazás-sablon létrehozására, amely többnyire üzembe helyezésre kész.

> [!TIP]
> Ha a szervezete nem engedélyezi adott erőforrások hozzáférését Logic Apps-összekötők [](../logic-apps/block-connections-connectors.md) segítségével, letilthatja az ilyen kapcsolatok létesítését a [Azure Policy.](../governance/policy/overview.md)

## <a name="recurrence-behavior"></a>Ismétlődés viselkedése

Az ismétlődő beépített eseményindítók, [](../connectors/connectors-native-recurrence.md)például az Ismétlődés eseményindító, natív módon futnak az Azure Logic Apps-ban, és eltérnek az olyan ismétlődő kapcsolatalapú eseményindítóktól, mint az Office 365 Outlook összekötő eseményindítója, ahol először létre kell hoznia egy kapcsolatot.

Mindkét eseményindító esetén, ha az ismétlődés nem ad meg konkrét kezdő dátumot és időpontot, az első ismétlődés a logikai alkalmazás mentésekor vagy üzembe helyezésekor azonnal lefut az eseményindító ismétlődési beállításai ellenére. Ennek elkerülése érdekében adjon meg egy kezdő dátumot és időpontot arra az időpontra, amikor az első ismétlődést szeretné futtatni.

### <a name="recurrence-for-built-in-triggers"></a>Ismétlődés a beépített triggerek esetén

Az ismétlődő beépített eseményindítók a beállított ütemezést követik, beleértve a megadott időzónát is. Ha azonban az ismétlődés nem ad meg más speciális ütemezési beállításokat, például a jövőbeli ismétlődéseket futtató konkrét időpontokat, akkor az ismétlődéseket az eseményindító legutóbbi végrehajtása határozza meg. Ennek eredményeképpen az ismétlődés kezdési idejei olyan tényezők miatt eltérést okozhatnak, mint a tárolási hívások késése. Hibaelhárítási segítségért tekintse meg az ismétlődési [problémák szakaszt.](#recurrence-issues)

### <a name="recurrence-for-connection-based-triggers"></a>Ismétlődés kapcsolatalapú eseményindítók esetén

Az ismétlődő kapcsolatalapú eseményindítókban, például az Office 365 Outlookban nem az ütemezés az egyetlen illesztőprogram, amely szabályozza a végrehajtást. Az időzóna csak a kezdeti kezdési időt határozza meg. A későbbi futtatás az ismétlődési ütemezéstől, az utolsó eseményindító-végrehajtástól, valamint egyéb olyan tényezőktől függ, amelyek miatt a futási idők eltérést okozhatnak, vagy váratlan viselkedést okozhatnak. Ezek a következők:

- Azt határozza meg, hogy az eseményindító olyan kiszolgálóhoz fér-e hozzá, amely több adatot tartalmaz, és amelyet az eseményindító azonnal megpróbál lekérni.
- Az eseményindító által előidézett hibák vagy újratitkosítások.
- Késés a tárhívások során.
- Nem tartja fenn a megadott ütemezést, amikor a nyári időszámítás (DST) elindul és véget ér.
- Egyéb tényezők, amelyek befolyásolhatják a következő futási idő bekövetkeztét.

A hibaelhárítással kapcsolatos segítségért lásd az Ismétlődési [problémák szakaszt.](#recurrence-issues) 

### <a name="recurrence-issues"></a>Ismétlődési problémák

A következő megoldásokkal győződjön meg arról, hogy a munkafolyamat a megadott kezdési időpontban fut, és nem hagy ki ismétlődést, különösen akkor, ha a gyakoriság napokban vagy hosszabban van.

A DST hatályba lépése után manuálisan állítsa be az ismétlődést, hogy a munkafolyamat a várt időpontban tovább fusson. Ellenkező esetben a kezdési idő egy órával előrettol a DST kezdetekor, és egy órával visszafelé, amikor a DST véget ér. További információkért és példákért lásd: Ismétlődés a nyári időszámítás és a [normál idő esetén.](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time)

Ha Ismétlődési **eseményindítót** használ, adjon meg egy időzónát, egy kezdő dátumot és egy időpontot. Emellett konfigurálja a megadott időpontokat úgy,  hogy az ezt követő ismétlődéseket a  Következő időpontokban és időpontokban tulajdonságban futtassa, amelyek csak a Nap és a **Hét** gyakorisághoz érhetők el.  Bizonyos időablakok azonban továbbra is problémákat okozhatnak az időváltáskor. 

Az Ismétlődés [  eseményindító helyett](../connectors/connectors-native-sliding-window.md) érdemes lehet **csúszóablakos** eseményindítót használni, hogy elkerülje a kihagyott ismétlődéseket.

## <a name="custom-apis-and-connectors"></a>Egyéni API-k és összekötők

Az egyéni kódot vagy összekötőként nem elérhető API-kat egyéni kód létrehozásával bővítheti Logic Apps platform [API Apps mal.](../logic-apps/logic-apps-create-api-app.md) 

Bármely [REST-](../logic-apps/custom-connector-overview.md) vagy SOAP-alapú API-hoz létrehozhat egyéni összekötőket is, amelyek elérhetővé teszik ezeket az API-kat az Azure-előfizetés bármely logikai alkalmazása számára. 

Ha az egyéni API Apps vagy összekötőket bárki számára elérhetővé teszi az Azure-ban, beküldheti az összekötőket [Microsoft-minősítésre.](/connectors/custom-connectors/submit-certification)

## <a name="ise-and-connectors"></a>ISE és összekötők

Az Olyan munkafolyamatok számára, amelyek közvetlen hozzáférést igényelnek egy Azure-beli virtuális hálózat erőforrásaihoz, létrehozhat egy dedikált integrációs szolgáltatási környezetet [(ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) amelyben dedikált erőforrásokon hozhat létre, helyezhet üzembe és futtathat munkafolyamatokat. Az ISE-k létrehozásával kapcsolatos további információkért lásd: Csatlakozás Azure-beli virtuális [hálózatokhoz a Azure Logic Apps.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

Az ISE-környezetben létrehozott egyéni összekötők nem működnek a helyszíni adatátjáróval. Ezek az összekötők azonban közvetlenül elérhetik az ISE-t üzemeltető Azure-beli virtuális hálózathoz csatlakozó helyszíni adatforrásokat. Az ISE-beli logikai alkalmazásoknak tehát valószínűleg nincs szükségük az adatátjáróra, amikor kommunikálnak az erőforrásokkal. Ha olyan egyéni összekötőket hozott létre egy ISE-n kívül, amelyek megkövetelik a helyszíni adatátjárót, az ISE logikai alkalmazásai is használhatja ezeket az összekötőket.

Az Logic Apps Designerben a logikai alkalmazásokhoz használni kívánt összekötők tallózásakor egy **CORE-címke** jelenik meg a beépített eseményindítókban és műveletekben, míg az **ISE-címke** egyes összekötőkben megjelenik.

:::row:::
    :::column:::
        ![Példa CORE-összekötőre](./media/apis-list/example-core-connector.png)
        \
        \
        **Core**
        \
        \
        Az ezzel a címkével bíró beépített eseményindítók és műveletek ugyanabban az ISE-ben futnak, mint a logikai alkalmazások.
    :::column-end:::
    :::column:::
        ![Példa ISE-összekötőre](./media/apis-list/example-ise-connector.png)
        \
        \
        **Ise**
        \
        \
        Az ezzel a címkével ellátott felügyelt összekötők ugyanabban az ISE-ban futnak, mint a logikai alkalmazások. Ha olyan helyszíni rendszerrel rendelkezik, amely egy [Azure-beli](../logic-apps/logic-apps-gateway-connection.md)virtuális hálózathoz csatlakozik, az ISE lehetővé teszi, hogy a logikai alkalmazások közvetlenül, a helyszíni adatátjáró nélkül férnek hozzá a rendszerhez. Ehelyett használhatja a rendszer **ISE-összekötőját,** ha elérhető, egy HTTP-műveletet vagy egy [egyéni összekötőt.](connectors-overview.md#custom-apis-and-connectors) Az **ISE-összekötővel** nem ellátott helyszíni rendszerekhez használjon helyszíni adatátjárót. Az elérhető ISE-összekötők áttekintését lásd: [ISE-összekötők.](#ise-and-connectors)
    :::column-end:::
    :::column:::
        ![Példa több-bérlős összekötőre](./media/apis-list/example-multi-tenant-connector.png)
        \
        \
        Nincs címke     \
        \
        A **CORE** vagy **ISE** címke nélküli összes többi összekötőt, amelyeket továbbra is használhat, futtassa a globális, több-bérlős Logic Apps szolgáltatásban.
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="known-issues"></a>Ismert problémák

Az alábbiakban ismert problémákat ismertetünk a Logic Apps kapcsolatban.

#### <a name="error-badgateway-client-request-id-guid"></a>Hiba: BadGateway. Ügyfélkérés azonosítója: "{GUID}"

Ez a hiba a címkék frissítésének eredménye egy logikai alkalmazásban, ahol egy vagy több kapcsolat nem támogatja az Azure Active Directory-alapú OAuth-hitelesítést(például SFTP ad SQL), ami miatt megszakadnak ezek a kapcsolatok. Ennek elkerülése érdekében ne frissítse ezeket a címkéket.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyéni API-k létrehozása, amelyek a Logic Apps](/logic-apps/logic-apps-create-api-app)
