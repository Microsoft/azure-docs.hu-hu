---
title: Riasztások és értesítések Azure Monitor hibaelhárítása
description: Azure Monitor riasztásokkal és lehetséges megoldásokkal kapcsolatos gyakori problémák.
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: 856aeb4dabc64edc2ade2869fddc3d14cb21a81b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942166"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>Azure Monitor riasztásokkal kapcsolatos problémák elhárítása

Ez a cikk Azure Monitor riasztásokkal és értesítésekkel kapcsolatos gyakori problémákat ismerteti.

Azure Monitor riasztások proaktívan értesítik Önt, ha fontos feltételek találhatók a megfigyelési adataiban. Lehetővé teszik a problémák azonosítását és megcímzését, mielőtt a felhasználók a rendszerértesítéseket. A riasztással kapcsolatos további információkért lásd: [a Microsoft Azure riasztások áttekintése](alerts-overview.md).

Ha problémája van egy riasztás égetésével vagy a várt módon nem égetéssel, tekintse meg az alábbi cikkeket. A Azure Portal "fired" riasztásokat láthat.

- [Azure Monitor metrikai riasztások hibaelhárítása Microsoft Azure](alerts-troubleshoot-metric.md)  
- [Azure Monitor naplózási riasztások hibaelhárítása Microsoft Azure](alerts-troubleshoot-metric.md)

Ha a riasztás a Azure Portal alapján, de a megfelelő értesítések nem lépnek fel, a jelen cikk további részében található információk segítségével elháríthatja a problémát.

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>A riasztáson elvégzett művelet vagy értesítés nem a várt módon működik

Ha egy kilőtt riasztás jelenik meg a Azure Portalban, de a műveleteivel vagy értesítéseivel kapcsolatban probléma merül fel, tekintse meg a következő részeket.

## <a name="did-not-receive-expected-email"></a>Nem érkezett meg a várt e-mail

Ha látja az aktivált riasztást az Azure Portalon, de nem kapta meg a hozzá beállított e-mailt, kövesse az alábbi lépéseket:

1. **Egy [műveleti szabály](alerts-action-rules.md)letiltotta az e-mailt**?

    Ennek az ellenőrzéséhez kattintson az aktivált riasztásra a portálon, és tekintse meg a letiltott [műveletcsoportok](action-groups.md) előzménylapját:

    ![Riasztási műveleti szabály elnyomásának előzményei](media/alerts-troubleshoot/history-action-rule.png)

1. **Az "e-mail-Azure Resource Manager szerepkör" művelet típusa?**

    Ez a művelet csak az előfizetési hatókörben és a *felhasználó* típusú Azure Resource Manager szerepkör-hozzárendeléseket vizsgálja.  Győződjön meg arról, hogy az előfizetés szintjén rendelte hozzá a szerepkört, nem pedig az erőforrás vagy az erőforráscsoport szintjén.

1. **Az e-mail-kiszolgáló és a postaláda külső e-maileket fogad el?**

    Ellenőrizze, hogy nincsenek-e letiltva az alábbi három címről érkező e-mailek:
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    A belső levelezőlisták vagy terjesztési listák gyakran letiltják a külső e-mail-címekről érkező e-maileket. Engedélyeznie kell a fenti e-mail-címekről érkező leveleket.  
    A teszteléshez adjon hozzá egy normál munkahelyi e-mail-címet (nem levelezési listát) a műveleti csoporthoz, és ellenőrizze, hogy érkeznek-e riasztások a címre.

1. **A beérkezett e-mail-szabályok vagy a levélszemét-szűrők feldolgozása megtörtént?**

    Ellenőrizze, hogy nincsenek-e a beérkezett e-mailekre vonatkozó olyan szabályok, amelyek az ilyen e-maileket más mappába irányítják át vagy törlik. A beérkezett fájlok szabályai például tartalmazhatnak konkrét küldőket vagy adott szavakat a tárgyban.

    Emellett ellenőrizze az alábbiakat:

   - Levelezőprogramja (Outlook, Gmail) levélszemét-beállításait
      - a feladó korlátozza/levélszemét beállítások/karantén beállítások az e-mail kiszolgáló (például Exchange, Microsoft 365, G-Suite)
      - az e-mail-biztonsági berendezés beállításai, ha vannak ilyenek (például a Barracuda, a Cisco).

1. **Véletlenül leiratkozott a műveleti csoportból?**

    A riasztási e-mailek a műveletcsoportról való leiratkozásra mutató hivatkozást tartalmaznak. Kétféle módon ellenőrizheti, hogy véletlenül leiratkozott-e a műveletcsoportról:

    1. Nyissa meg a műveletcsoportot a portálon, és ellenőrizze az Állapot oszlopot:

    ![műveletcsoport állapotát jelző oszlop](media/alerts-troubleshoot/action-group-status.png)

    2. Keresse meg az e-mailben a leiratkozásra vonatkozó megerősítést:

    ![leiratkozva a riasztási műveletcsoportról](media/alerts-troubleshoot/unsubscribe-action-group.png)

    Az újbóli feliratkozáshoz használja a leiratkozás megerősítését tartalmazó e-mailben található hivatkozást, vagy távolítsa el az e-mail-címet a műveletcsoportból, és adja hozzá újra. 
 
1. **A minősítés korlátozott, mert számos e-mail-cím egyetlen e-mail-címre fog rendelkezni?**

    Az egyes e-mail-címekre egy óránként legfeljebb 100 e-mailes [korlátozás](alerts-rate-limiting.md) vonatkozik. Ha átlépi ezt a küszöbértéket, a további e-mailekről a rendszer nem küld értesítést.  Ellenőrizze, hogy kapott-e olyan üzenetet, amely szerint e-mail-címe ideiglenesen korlátozva lett: 
 
   ![E-mail-cím korlátozva](media/alerts-troubleshoot/email-paused.png)

   Ha nagy mennyiségű értesítést szeretne kapni korlátozás nélkül, fontolja meg egy másik művelet, például egy webhook, logikai alkalmazás, Azure-függvény vagy Automation-runbook használatát (egyik sincs korlátozva). 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>Nem érkezett meg a várt SMS-, hanghívási vagy leküldéses értesítés

Ha látja az aktivált riasztást a portálon, de nem kapta meg a hozzá beállított SMS-t, hanghívást vagy leküldéses értesítést, kövesse az alábbi lépéseket: 

1. **Egy [műveleti szabály](alerts-action-rules.md)letiltotta a műveletet?**

    Ennek az ellenőrzéséhez kattintson az aktivált riasztásra a portálon, és tekintse meg a letiltott [műveletcsoportok](action-groups.md) előzménylapját: 

    ![Riasztási műveleti szabály elnyomásának előzményei](media/alerts-troubleshoot/history-action-rule.png)

   Ha a letiltás nem volt szándékos, módosíthatja, letilthatja vagy törölheti a műveleti szabályt.
 
1. **SMS/Voice: helyes a telefonszáma?**

   Nézze meg, hogy nincsenek-e elírások az SMS-művelet országkódjában vagy telefonszámában.
 
1. **SMS/Voice: korlátozott a díjszabás?**

    Az SMS-ek és a hanghívások ötpercenként legfeljebb egy értesítésre vannak korlátozva telefonszámonként. Ha átlépi ezt a küszöbértéket, a rendszer elveti az értesítéseket.

      - Hanghívás – tekintse meg a híváslistát, és ellenőrizze, hogy érkezett-e más hívás az Azure-tól az elmúlt öt percben.
      - SMS – tekintse meg az SMS-előzményeknél, hogy kapott-e olyan üzenetet, amely szerint a telefonszáma korlátozva lett.

    Ha nagy mennyiségű értesítést szeretne kapni korlátozás nélkül, fontolja meg egy másik művelet, például egy webhook, logikai alkalmazás, Azure-függvény vagy Automation-runbook használatát (egyik sincs korlátozva). 
 
1. **SMS: véletlenül leiratkozott a műveleti csoportból?**

    Nyissa meg az SMS-előzményeket, és ellenőrizze, hogy kikapcsolta-e az SMS-kézbesítést az adott műveleti csoportból (a LETILTÁS action_group_short_name válasz használatával) vagy az összes műveleti csoportból (a leállítási válasz használatával). Az újbóli feliratkozáshoz küldje el a megfelelő SMS-parancsot (ENABLE action_group_short_name vagy START), vagy távolítsa el az SMS-műveletet a műveletcsoportból, és adja hozzá újra.  További információkért lásd [az SMS-riasztás viselkedését a műveletcsoportokban](alerts-sms-behavior.md).

1. **Véletlenül letiltotta az értesítéseket a telefonon?**

   A legtöbb mobiltelefon lehetővé teszi a bizonyos telefonszámokról vagy rövid hívókódokról érkező hívások vagy SMS-ek, vagy a bizonyos alkalmazások (például az Azure mobile app) által küldött leküldéses értesítések letiltását. Ha ellenőrizni szeretné, hogy nem tiltotta-e le véletlenül az értesítéseket a telefonján, keresse meg a telefon operációs rendszerére és a modelljére vonatkozó dokumentációt, vagy végezzen tesztet egy másik telefonnal és telefonszámmal.

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>A program egy másik típusú műveletet várt, de nem 
   
Ha lát egy aktivált riasztást a portálon, de a hozzá konfigurált művelet nem aktiválódott, kövesse az alábbi lépéseket:

1. **Egy műveleti szabály letiltotta a műveletet?**

    Ennek az ellenőrzéséhez kattintson az aktivált riasztásra a portálon, és tekintse meg a letiltott [műveletcsoportok](action-groups.md) előzménylapját:

    ![Riasztási műveleti szabály elnyomásának előzményei](media/alerts-troubleshoot/history-action-rule.png)
 
    Ha a letiltás nem volt szándékos, módosíthatja, letilthatja vagy törölheti a műveleti szabályt.

1. **Nem aktiválódik a webhook?**

    1. **Le lettek tiltva a forrás IP-címei?**
    
       Adja hozzá azokat az [IP-címeket](action-groups.md#action-specific-information) , amelyeket a webhook meghív az engedélyezési listára.

    1. **A webhook-végpont megfelelően működik?**

       Ellenőrizze, hogy a konfigurált webhook-végpont helyes-e, és hogy a végpont megfelelően működik-e. Tekintse meg a webhook naplóit, vagy instrumentálja úgy a kódját, hogy átvizsgálható legyen (például naplózza a bejövő hasznos adatokat).

    1. **A Slack vagy a Microsoft Teams szolgáltatást hívja?**  
    Ezek a végpontok meghatározott JSON-formátumot várnak el. Kövesse [ezeket az utasításokat](action-groups-logic-app.md), hogy inkább egy logikai alkalmazási műveletet konfiguráljon.

    1. **A webhook nem válaszol, vagy hibát adott vissza?** 

        A webhook-válaszok időkorlátja 10 másodperc. A rendszer legfeljebb két alkalommal próbálkozik újra a webhook meghívásával, ha a következő HTTP-állapotkódokat kapja: 408, 429, 503, 504, illetve ha a HTTP-végpont nem válaszol. Az első újrapróbálkozás 10 másodperc után történik. A második és egyben utolsó újrapróbálkozás 100 másodperc elteltével történik. Ha a második újrapróbálkozás sikertelen, a rendszer 30 percig semelyik műveletcsoport számára nem hívja meg újra a végpontot.

## <a name="action-or-notification-happened-more-than-once"></a>A művelet vagy az értesítés többször is történt 

Ha többször is kapott értesítést egy riasztásról (például e-mail vagy SMS formájában), vagy a riasztás művelete (például egy webhook vagy Azure-függvény) többször is aktiválódott, kövesse az alábbi lépéseket: 

1. **Valóban ugyanez a riasztás?** 

    Vannak olyan esetek, amikor több hasonló riasztás is aktiválódik nagyjából ugyanabban az időpontban. Ezért úgy tűnhet, mintha ugyanaz a riasztás aktiválta volna többször a műveleteit. Például, ha nem szűri az esemény állapota mezőt, előfordulhat, hogy a tevékenységnapló riasztási szabálya úgy lesz beállítva, hogy az esemény kezdetekor és befejezésekor (mindegy, hogy sikeres vagy sikertelen) is aktiválódjon. 

    Ha ellenőrizni szeretné, hogy az érintett műveletek vagy értesítések különböző riasztásokhoz tartoznak-e, tekintse meg a riasztás részleteit, például az időbélyegét és a riasztás azonosítóját vagy korrelációs azonosítóját. Másik megoldásként tekintse meg az aktivált riasztások listáját a portálon. Ebben az esetben módosítania kell a riasztási szabály logikáját, vagy más módon konfigurálnia kell a riasztás forrását. 

1. **Ismétlődik a művelet több műveleti csoportban?** 

    Egy riasztás aktiválásakor a rendszer egymástól függetlenül dolgozza fel annak műveletcsoportjait. Így ha egy művelet (például egy e-mail-cím) több aktivált műveletcsoportban is szerepel, a rendszer műveletcsoportonként egyszer meg fogja hívni. 

    Annak ellenőrzéséhez, hogy mely műveletcsoportok aktiválódtak, tekintse meg a riasztási előzmények lapját. Itt a riasztási szabályban meghatározott mindkét műveletcsoportot és a műveleti szabály által hozzáadott műveletcsoportokat is látni fogja: 

    ![Több műveleti csoportban ismétlődő művelet](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>A művelet vagy az értesítés nem várt tartalmat tartalmaz

Ha megkapta a riasztást, de úgy véli, hogy egyes mezők hiányoznak belőle vagy helytelenek, kövesse az alábbi lépéseket: 

1. **A megfelelő formátumot választotta a művelethez?** 

    Minden Művelettípus (e-mail-cím, webhook stb.) két formátumban van – az alapértelmezett, örökölt formátum és az [újabb általános séma formátuma](alerts-common-schema.md). Műveletcsoport létrehozásakor a kívánt formátumot műveletenként kell megadnia – a műveletcsoportok különböző műveletei különböző formátumokkal rendelkezhetnek. 

    A webhook művelethez például: 

    ![webhook műveleti séma beállítása](media/alerts-troubleshoot/webhook.png)

    Ellenőrizze, hogy a műveleti szinten megadott formátum az elvárt-e. Előfordulhat például, hogy olyan riasztásokra válaszoló programkódot (webhook, függvény, logikai alkalmazás stb.) fejlesztett, amely egy adott formátumot vár el, de a művelet során később Ön vagy egy másik személy eltérő formátumot adott meg.  

    Továbbá ellenőrizze a hasznosadat-formátumot (JSON) a [tevékenységnapló-riasztásokhoz](activity-log-alerts-webhook.md), a [naplókeresési riasztásokhoz](alerts-log-webhook.md) (Application Insights és naplóelemzés), a [metrikariasztásokhoz](alerts-metric-near-real-time.md#payload-schema), az [általános riasztási sémához](alerts-common-schema-definitions.md) és az elavult [klasszikus metrikariasztásokhoz](alerts-webhooks.md).

 
1. **Műveletnapló riasztásai: a tevékenység naplójában elérhető információk?** 

    A [műveletnapló riasztásai](activity-log-alerts.md) olyan riasztások, amelyek az Azure-beli tevékenységi naplóba írt eseményeken alapulnak, például az Azure-erőforrások, a szolgáltatás állapotára és az erőforrás-állapotra vonatkozó események létrehozására, frissítésére vagy törlésére, valamint Azure Advisor és Azure Policy eredményeire vonatkozó eseményeket. Ha a tevékenységnaplón alapuló riasztást kapott, de egyes szükséges mezők hiányoznak vagy helytelenül lettek kitöltve, első lépésként tekintse meg az eseményeket a tevékenységnaplóban. Ha az Azure-erőforrás nem töltötte ki a tevékenységnaplójában a keresett mezőket, akkor ezek a mezők nem kerülnek bele a megfelelő riasztásba. 

## <a name="action-rule-is-not-working-as-expected"></a>A műveleti szabály nem a várt módon működik 

Ha látja az aktivált riasztást a portálon, de a kapcsolódó műveleti szabály nem a várt módon működött, kövesse az alábbi lépéseket: 

1. **Engedélyezve van a műveleti szabály?** 

    Tekintse meg a műveleti szabály állapotoszlopát, és ellenőrizze, hogy engedélyezve van-e a kapcsolódó műveleti szabály. 

    ![grafikus](media/alerts-troubleshoot/action-rule-status.png) 

    Ha nincs engedélyezve, az engedélyezéshez jelölje ki a műveleti szabályt, majd kattintson az Engedélyezés gombra. 

1. **A szolgáltatás állapota riasztás?** 

    A szolgáltatásállapot-riasztásokat (monitorozási szolgáltatás = „Service Health”) nem befolyásolják a műveleti szabályok. 

1. **Reagált a riasztásra a műveleti szabály?** 

    Ellenőrizze, hogy a műveleti szabály feldolgozta-e a riasztást. Ehhez kattintson az aktivált riasztásra a portálon, és tekintse meg az előzménylapot.

    Példa egy műveleti szabályra, amely az összes műveletcsoportot letiltja: 
 
     ![Riasztási műveleti szabály elnyomásának előzményei](media/alerts-troubleshoot/history-action-rule.png)

    Példa egy műveleti szabályra, amely hozzáad egy másik műveletcsoportot:

    ![Több műveleti csoportban ismétlődő művelet](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **Megfelel a műveleti szabály hatóköre és szűrője az aktív riasztásnak?** 

    Ha úgy gondolja, hogy a műveleti szabálynak aktiválódnia kellett volna, de nem aktiválódott vagy fordítva, vizsgálja meg alaposan a műveleti szabály hatókörét és szűrőfeltételeit, és hasonlítsa össze őket az aktivált riasztás tulajdonságaival. 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>Kilőtt riasztás riasztási AZONOSÍTÓjának megkeresése

Ha egy adott kilőtt riasztásra vonatkozó esetet nyit meg (például – ha nem kapta meg az e-mail-értesítést), meg kell adnia a riasztás AZONOSÍTÓját. 

A megkereséséhez kövesse az alábbi lépéseket:

1. A Azure Portal navigáljon a kilőtt riasztások listájához, és keresse meg az adott riasztást. A szűrők segítségével megkeresheti. 

1. A riasztás részleteinek megnyitásához kattintson a riasztásra. 

1. Görgessen lefelé az első lap riasztás mezőiben (az összefoglalás lapon), amíg meg nem találja, és másolja. Ebben a mezőben a "másolás a vágólapra" segítő gomb is megtalálható.  

    ![riasztás AZONOSÍTÓjának keresése](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>Probléma a műveleti szabályok létrehozásával, frissítésével vagy törlésével kapcsolatban a Azure Portalban

Ha hiba történt a [műveleti szabály](alerts-action-rules.md)létrehozása, frissítése vagy törlése során, kövesse az alábbi lépéseket: 

1. **Hibaüzenetet kapott?**  

    Rendelkeznie kell a [figyelő közreműködői beépített szerepkörrel](../../role-based-access-control/built-in-roles.md#monitoring-contributor), vagy a műveleti szabályokkal és riasztásokkal kapcsolatos konkrét engedélyekkel.

1. **Ellenőrizte a műveleti szabály paramétereit?**  

    Győződjön meg a [műveleti szabály dokumentációjában](alerts-action-rules.md)vagy a [műveleti szabály PowerShell set-AzActionRule](/powershell/module/az.alertsmanagement/set-azactionrule) parancsának megtekintéséhez. 


## <a name="next-steps"></a>Következő lépések
- Ha naplózási riasztást használ, tekintse meg a [riasztások hibaelhárítása](./alerts-troubleshoot-log.md)című témakört is.
- Lépjen vissza a [Azure Portal](https://portal.azure.com) , és ellenőrizze, hogy megoldotta-e a problémát a fenti útmutatás alapján 
