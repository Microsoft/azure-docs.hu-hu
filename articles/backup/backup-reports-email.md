---
title: E-mail-Azure Backup jelentések
description: Automatizált feladatok létrehozása az időszakos jelentések e-mailben történő fogadásához
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: 8c18d4c7a3c7a9ba343296961fa9a44614366405
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510471"
---
# <a name="email-azure-backup-reports"></a>E-mail-Azure Backup jelentések

A biztonsági mentési jelentésekben elérhető **e-mail-jelentés** funkció használatával automatizált feladatokat hozhat létre, amelyekkel rendszeres jelentéseket fogadhat e-mailben. Ez a funkció egy olyan logikai alkalmazás üzembe helyezését mutatja be az Azure-környezetben, amely az Ön által megadott bemenetek alapján lekérdezi a kiválasztott Log Analytics (LA) munkaterületről származó adatokat. [További információ a Logic apps szolgáltatásról és azok díjszabásáról](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="getting-started"></a>Első lépések

Az e-mail-feladatok biztonsági mentési jelentésekkel történő konfigurálásához hajtsa végre a következő lépéseket:

1.  Navigáljon a **Backup Center**  >  **biztonsági mentési jelentéseihez** , és kattintson az **e-mail-jelentés** lapra.
2.  Hozzon létre egy feladatot a következő információk megadásával:
    * **Feladat részletei** – a létrehozandó logikai alkalmazás neve, valamint az előfizetés, erőforráscsoport és hely, ahol létre kell hozni. Vegye figyelembe, hogy a logikai alkalmazás több előfizetésben, erőforráscsoporthoz és helyen is lekérdezheti az adatait (a jelentések szűrőinek szakaszban kiválasztottak szerint), de egy előfizetés, erőforráscsoport és hely kontextusában jön létre.
    * **Exportálandó** Exportálás – az exportálni kívánt lap. Egy lapon létrehozhat egyetlen feladatot, vagy egyetlen feladattal elküldheti az összes lapot egyetlen feladat használatával. Ehhez válassza a **minden lap** lehetőséget.
    * **E-mail-beállítások**: az e-mailek gyakorisága, a címzett e-mail-azonosító (k) és az e-mail tárgya.

    ![E-mail lap](./media/backup-azure-configure-backup-reports/email-tab.png)

3.  Miután a **Submit (elküldés** ) gombra **kattint, a** rendszer létrehoz egy logikai alkalmazást. A logikai alkalmazás és a hozzá tartozó API-kapcsolatok a **UsedByBackupReports: true** címkével hozhatók létre a könnyen felderíthetővé tételhez. Végre kell hajtania egy egyszeri engedélyezési lépést a logikai alkalmazás sikeres futtatásához az alábbi részben leírtak szerint.

## <a name="authorize-connections-to-azure-monitor-logs-and-office-365"></a>Kapcsolatok engedélyezése Azure Monitor naplókhoz és az Office 365-hez

A logikai alkalmazás az [azuremonitorlogs](https://docs.microsoft.com/connectors/azuremonitorlogs/) -összekötőt használja a La munkaterület lekérdezéséhez, és a [Office 365 Outlook](https://docs.microsoft.com/connectors/office365connector/) Connectort használja az e-mailek küldéséhez. Ehhez a két összekötőhöz egyszeri hitelesítést kell végrehajtania. 
 
Az engedélyezés végrehajtásához kövesse az alábbi lépéseket:

1.  Navigáljon **Logic apps** a Azure Portal.
2.  Keresse meg a létrehozott logikai alkalmazás nevét, és navigáljon az erőforráshoz.

    ![Logic Apps](./media/backup-azure-configure-backup-reports/logic-apps.png)

3.  Kattintson az **API-kapcsolatok** menüpontra.

    ![API-kapcsolatok](./media/backup-azure-configure-backup-reports/api-connections.png)

4.  Két kapcsolatot fog látni a következő formátumban: `<location>-azuremonitorlogs` `<location>-office365` ,-azaz, _eastus-azuremonitorlogs_ és _eastus-Office 365_.
5.  Navigáljon a kapcsolatok mindegyikéhez, és válassza az **API-kapcsolat szerkesztése** menüpontot. A megjelenő képernyőn válassza az **Engedélyezés** lehetőséget, és mentse a-t az engedélyezés befejeződése után.

    ![Kapcsolat engedélyezése](./media/backup-azure-configure-backup-reports/authorize-connections.png)

6.  Annak ellenőrzéséhez, hogy a logikai alkalmazás működik-e az engedélyezés után, térjen vissza a logikai alkalmazáshoz, nyissa meg az **áttekintést** , és válassza az **trigger futtatása** lehetőséget a felső ablaktáblán annak ellenőrzéséhez, hogy sikeresen létrejött-e az e-mail.

## <a name="contents-of-the-email"></a>Az e-mail tartalma

* A portálon megjelenített diagramok és grafikonok az e-mailben beágyazott tartalomként érhetők el.
* A portálon megjelenő rácsok *. csv mellékletként érhetők el az e-mailben.
* Az e-mailben megjelenő adatok a jelentésben a felhasználó által kiválasztott jelentési szintű szűrőket használják az e-mail feladat létrehozásakor.
* A rendszer nem alkalmazza a lap szintű szűrőket, például a **biztonságimásolat-példány nevét**, a **házirend nevét** és így tovább. Ez alól az egyetlen kivétel, **hogy az optimalizálás lapon a** **megőrzési optimalizálások** rács látható, ahol a **napi**, **heti**, **havi** és **éves** RP-adatmegőrzési szűrők érvényesek.
* Az időtartomány és az összesítési típus (diagramok esetében) a felhasználó időtartományának a jelentésekben való kiválasztásán alapul. Ha például az utolsó 60 nap (a heti összesítési típusra való fordítás) és az e-mailek gyakorisága naponta történik, a címzett minden nap e-mailt kap az utolsó 60 napos időszakra kiterjedő, a heti szinten összesített adatokat tartalmazó diagramokkal.

## <a name="troubleshooting-issues"></a>Problémák hibaelhárítása

Ha nem kap a várt e-maileket a logikai alkalmazás sikeres üzembe helyezése után is, kövesse az alábbi lépéseket a konfiguráció hibaelhárításához:

### <a name="scenario-1-receiving-neither-a-successful-email-nor-an-error-email"></a>1. forgatókönyv: a sikeres e-mailek és hibaüzenetek fogadása

* Ez a probléma akkor fordulhat elő, ha az Outlook API-összekötő nincs engedélyezve. A kapcsolódás engedélyezéséhez kövesse a fent megadott engedélyezési lépéseket.

* Ez a probléma akkor is előfordulhat, ha helytelen e-mail címet adott meg a logikai alkalmazás létrehozásakor. Annak ellenőrzéséhez, hogy az e-mail címzettje helyesen van-e megadva, navigáljon a Azure Portal logikai alkalmazásra, nyissa meg a Logic app Designer alkalmazást, és válassza az e-mail-cím lehetőséget, és ellenőrizze, hogy a megfelelő e-mail azonosítók használatban vannak-e.

### <a name="scenario-2-receiving-an-error-email-that-says-that-the-logic-app-failed-to-execute-to-completion"></a>2. forgatókönyv: hibaüzenet küldése, amely szerint a logikai alkalmazás nem hajtható végre a befejezéshez

A probléma megoldásához:
1.  Navigáljon a logikai alkalmazáshoz a Azure Portal.
2.  Az **Áttekintés** képernyő alján megjelenik a **futtatási előzmények** szakasz. Megnyithatja a legutóbbi futtatást, és megtekintheti, hogy a munkafolyamat mely lépései sikertelenek. Néhány lehetséges ok:
    * **Azure monitor naplók összekötője nincs engedélyezve**: a probléma megoldásához kövesse a fentiekben ismertetett engedélyezési lépéseket.
    * Hiba a (z) **La lekérdezésben**: abban az esetben, ha a logikai alkalmazást saját lekérdezésekkel testreszabta, előfordulhat, hogy valamelyik La lekérdezésben hibát okoz a logikai alkalmazás meghibásodása. Kiválaszthatja a megfelelő lépést, és megtekintheti azt a hibát, amely miatt a lekérdezés helytelenül fut.

Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához.

## <a name="next-steps"></a>Következő lépések
[További információ a biztonsági mentési jelentésekről](https://docs.microsoft.com/azure/backup/configure-reports)
