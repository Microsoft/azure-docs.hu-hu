---
title: 'Gyakori kérdések: SRE és kódolás | Microsoft Docs'
titleSuffix: Azure
description: 'Gyakori kérdések: a SRE és a kódolás közötti kapcsolat ismertetése'
author: dnblankedelman
manager: ScottCa
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 09/14/2020
ms.author: dnb
ms.openlocfilehash: b8865fdd53f4947b17a3621a128fc83f3d93d3e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90089071"
---
# <a name="frequently-asked-questions-do-i-need-to-know-how-to-code-to-get-involved-with-sre"></a>Gyakori kérdések: szeretném tudni, hogyan kell kódokat bevenni a SRE?

Ha az egyének azt fontolgatják, hogy részt vesznek a SRE és a Teams szolgáltatásban, a SRE-gyakorlatok bevezetésével foglalkoznak, egy gyakori kérdés, hogy mit is tesznek a kód?

A rövid válasz: igen. 

A teljes válasz azonban egy kicsit árnyaltabb. Nézzük meg a három olyan helyet, ahol a kódolás a site megbízhatóságának fejlesztésében, valamint az egyes alkalmazásokhoz szükséges kódolási szakértelem szintjével együtt fog játszani. Ez a lista nem fejeződött be, de ezek a forgatókönyvek a leggyakoribb felhasználási esetek.

## <a name="scenario-1-removing-toil-through-automation"></a>1. forgatókönyv: a fáradságos megoldás eltávolítása az Automation használatával

A site megbízhatósági mérnökök és mások, akik a SRE-eljárásokat használják, ahol lehetséges, hogy el kellene távolítani a fáradságot. A "fáradságos" kifejezés egy konkrét dolgot jelent a SRE. A fáradságos tevékenység olyan emberi műveletekre utal, amelyek bizonyos jellemzőkkel rendelkeznek. A robotolásnak nincs hosszú távon megtérülő értéke. Semmilyen jelentős módon nem mozdítja elő a szolgáltatás javulását. Gyakran ismétlődő és nagymértékben manuális (bár lehetne automatizálni is). Miközben a szolgáltatás vagy a rendszer idővel növekszik, valószínűleg ezzel arányosan a rendszerbe érkező kérelmek száma is nő, és még több manuális munkát igényel.

Ha például egy szolgáltatás megköveteli, hogy a SRE csapat hetente alaphelyzetbe állítson valamit, vagy új fiókokat vagy lemezterületet szeretne kiépíteni kézzel, vagy többször is újra kell indítania őket – ez az üzemi terhelés. Ezeknek a műveleteknek a végrehajtása egyáltalán nem tette jobbá a szolgáltatást hosszú távon, tartós módon. Ezeket a műveleteket valószínűleg újra és újra meg kell ismételni.

Az SRE-szakemberek nem szeretik a robotolást. Azon dolgoznak, hogy ezt eltöröljék, ha csak lehetséges és helyénvaló. Az SRE-ben ez az egyike azoknak a helyeknek, ahol az automatizálás szerepet kap. Ha ezek a kérések automatikusan kezelhetők, így a csapat felszabadul, hogy hatékonyabban és hatásosan működjön.

*Kódolási szakértelem*: az automatizálásnak bizonyos kódolási szakértelemre van szüksége, de nem szükséges a teljes szoftverfejlesztés szaktudásához. Ha kisméretű parancsfájlokat (például a PowerShell-vagy a Bourne-rendszerhéjban) is írhat [https://docs.microsoft.com/azure/logic-apps/logic-apps-overview](Azure Logic app) , vagy ha alig bármilyen kódot hoz létre, akkor ez az alkalmazás továbbra is segíthet a fáradságos megoldásban.

## <a name="scenario-2-control-through-apisdomain-specific-languages-dslstemplates"></a>2. forgatókönyv: az API-k/tartomány-specifikus nyelvek (DSLs)/templates való vezérlése

Bár a SRE működéséhez nem feltétlenül szükséges, a környezeteket API-k, DSLs és sablonok (különösen a Felhőbeli környezetek) segítségével vezérelheti, így a SREs felskálázást végezhetnek a munkájuk során. Az infrastruktúra kiépítése/kiépítése, a figyelés konfigurálása és számos szolgáltatás integrálása mind sokkal hatékonyabbá válik a kódoláson keresztül.

*Kódolási szakértelem*: az előző forgatókönyvhöz hasonlóan ez bizonyos kódolási szakértelmet igényel, de nem szükséges a teljes szoftverfejlesztői ismeretek megkövetelése. A korábban említett szkriptek és logikai alkalmazások mellett [https://docs.microsoft.com/azure/azure-resource-manager/templates/overview](Azure Resource Manager Templates) minimális kódolási élmény mellett is használható.

## <a name="scenario-3-fixing-the-code"></a>3. forgatókönyv: a kód kijavítása

A site megbízhatósági mérnökök a rendszer megbízhatóságának javítására néznek ki. Ennek a célnak néha szüksége van egy rendszer forráskódjának beásására, a probléma meghatározására, és gyakran a kód alapjainak javítására. Habár a munka bonyolultsági szintje a helyzettől függően nagy mértékben változhat, a kódolási szaktudás ilyen esetekben határozott követelmény.

*Kódolási szakértelem*: ebben a forgatókönyvben gyakran szükséges a teljes szoftverfejlesztés szaktudása.


## <a name="next-steps"></a>Következő lépések

Szeretne többet megtudni a site megbízhatósági mérnöki és az alacsony kódú működésről? Tekintse meg a [site megbízhatósági mérnöki](../index.yml)központot, a fent csatolt termékdokumentációt.
