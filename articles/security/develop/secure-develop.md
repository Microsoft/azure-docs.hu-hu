---
title: Biztonságos alkalmazások fejlesztése Microsoft Azure
description: Ez a cikk a webalkalmazás-projekt megvalósítási és ellenőrzési fázisaiban megfontolandó ajánlott eljárásokat ismerteti.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 03/21/2021
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 8edceb31a0cdde36c987076e91350116a4f81255
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782401"
---
# <a name="develop-secure-applications-on-azure"></a>Biztonságos alkalmazások fejlesztése az Azure szolgáltatásban

Ebben a cikkben a Felhőbeli alkalmazások fejlesztésekor megfontolandó biztonsági tevékenységeket és ellenőrzéseket mutatjuk be. A Microsoft [biztonsági fejlesztési életciklus (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) megvalósítási és ellenőrzési szakaszaiban megfontolandó biztonsági kérdések és fogalmak. A cél az, hogy segítséget nyújtson olyan tevékenységek és Azure-szolgáltatások definiálásához, amelyeket a biztonságosabb alkalmazások fejlesztéséhez használhat.

Ebben a cikkben a következő SDL-fázisokat tárgyaljuk:

- Implementálás
- Ellenőrzés

## <a name="implementation"></a>Implementálás

A megvalósítási fázis középpontjában a korai megelőzéssel kapcsolatos ajánlott eljárások, valamint a kód biztonsági problémáinak észlelése és eltávolítása szükséges.
Tegyük fel, hogy az alkalmazás olyan módokon fog használatba venni, amelyeket nem kívánt használni. Ez segít megvédeni az alkalmazás véletlen vagy szándékos visszaélését.

### <a name="perform-code-reviews"></a>Kód felülvizsgálatának elvégzése

A kód beadása előtt a kód [felülvizsgálatával](/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) növelheti a kódok általános minőségét, és csökkentheti a hibák kockázatát. A [Visual Studio](/azure/devops/repos/tfvc/get-code-reviewed-vs) segítségével kezelheti a kód-felülvizsgálati folyamatot.

### <a name="perform-static-code-analysis"></a>Statikus kódelemzés végrehajtása

A [statikus kód elemzését](https://owasp.org/www-community/controls/Static_Code_Analysis) (más néven *forráskód-elemzést*) általában a kód felülvizsgálatának részeként hajtja végre a rendszer. A statikus kód elemzése általában arra utal, hogy statikus kód-elemzési eszközöket futtasson, hogy a nem futó kódban lévő lehetséges biztonsági réseket olyan technikák használatával találja meg, mint a [szennyező ellenőrzés](https://en.wikipedia.org/wiki/Taint_checking) és [az adatfolyam-elemzés](https://en.wikipedia.org/wiki/Data-flow_analysis).

Az Azure Marketplace olyan [fejlesztői eszközöket](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) kínál, amelyek statikus kódok elemzését végzik, és segítséget nyújtanak a kódok felülvizsgálatában.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>Az alkalmazás minden bemenetének ellenőrzése és megtisztítása

Az összes bemenetet nem megbízhatóként kezelheti az alkalmazás a leggyakoribb webalkalmazás-biztonsági rések elleni védelem érdekében. A nem megbízható adathalmazok az injekciós támadásoknak megfelelő járművek. Az alkalmazás bemenete magában foglalja az URL-cím paramétereit, a felhasználótól származó adatokat, az adatbázisból vagy egy API-ból származó adatokat, valamint minden olyan adatot, amelyet a felhasználó potenciálisan kezelhet. Az alkalmazásnak [ellenőriznie kell,](https://owasp.org/www-project-proactive-controls/v3/en/c5-validate-inputs) hogy az adatkezelési és szemantikai szempontból érvényesek-e, mielőtt az alkalmazás bármilyen módon használja az adatforrásokat (beleértve a felhasználó számára történő megjelenítést is).

Az adatfolyam elején ellenőrizze, hogy csak a megfelelően formázott adatok jelennek-e meg a munkafolyamatban. Nem kívánja, hogy a helytelenül formázott adatmegőrzést végezze az adatbázisban, vagy hibásan aktiválja az alsóbb rétegbeli összetevőket.

A Blocklisting és a allowlisting két általános módszer a bemeneti szintaxis érvényesítésének végrehajtásához:

  - A Blocklisting megkísérli annak ellenőrzését, hogy egy adott felhasználói bevitel nem tartalmazza-e az "ismert kártékony" tartalmat.

  - A Allowlisting megkísérli megállapítani, hogy egy adott felhasználói bevitel megfelel-e a "ismert jó" bemeneteknek. A karakteres allowlisting a allowlisting egy formája, ahol egy alkalmazás ellenőrzi, hogy a felhasználói bevitel csak az "ismert jó" karaktereket tartalmazza-e, vagy hogy a bemenet megfelel-e az ismert formátumnak.
    Előfordulhat például, hogy a Felhasználónév csak alfanumerikus karaktereket tartalmaz, vagy pontosan két számot tartalmaz.

A Allowlisting a biztonságos szoftverek létrehozásának előnyben részesített megközelítése.
A Blocklisting a hiba miatt nem lehetséges, mert a potenciálisan rossz bevitel teljes listáját nem lehet meggondolni.

Ezt a kiszolgálót nem az ügyfél oldalán (vagy a kiszolgálón és az ügyfél oldalon) hajtsa végre.

### <a name="verify-your-applications-outputs"></a>Az alkalmazás kimenetének ellenőrzése

Minden olyan kimenetet, amelyet vizuálisan vagy egy dokumentumon belül már tartalmaz, mindig kódolva kell lennie és el kell menekülnie. A [Escape](https://owasp.org/www-community/Injection_Theory#Escaping_.28aka_Output_Encoding.29)-, más néven a *kimeneti kódolás* a segítségével biztosítható, hogy a nem megbízható adatokat ne a jármű adja meg az injekciós támadásokhoz. Az adatellenőrzéssel együtt folytatott Escape-szolgáltatás többrétegű védelmet biztosít a rendszer egészének biztonságának növelésére.

A Escape-funkció biztosítja, hogy minden *kimenetként* jelenjen meg. A szökés azt is lehetővé teszi, hogy a tolmács tudja, hogy az adatok nem hajthatók végre, és ez megakadályozza a támadások működését. Ez egy, a *többhelyes parancsfájlok* (XSS) elnevezésű gyakori támadási módszer.

Ha egy harmadik féltől származó webes keretrendszert használ, ellenőrizheti, hogy a [OWASP XSS-megelőzési lapján](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md)a webhelyeken milyen kimeneti kódolásra van lehetősége.

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>Paraméteres lekérdezések használata az adatbázishoz való kapcsolódáskor

Soha ne hozzon létre egy beágyazott adatbázis-lekérdezést "menet közben" a kódban, és küldje el közvetlenül az adatbázisnak. Az alkalmazásba beszúrt kártékony kódok okozhatják az adatbázis ellopását, törlését vagy módosítását. Az alkalmazás az adatbázist futtató operációs rendszeren rosszindulatú operációsrendszer-parancsok futtatására is használható.

Ehelyett használjon paraméteres lekérdezéseket vagy tárolt eljárásokat. Ha paraméteres lekérdezéseket használ, az eljárást biztonságosan hívhatja meg, és nem kell átadnia egy karakterláncot, és nem kell aggódnia, hogy a lekérdezési utasítás részeként lesz kezelve.

### <a name="remove-standard-server-headers"></a>Szabványos kiszolgáló fejlécének eltávolítása

A kiszolgáló, az X-powered-by és az X-AspNet-Version fejlécek a kiszolgálók és a mögöttes technológiák információit mutatják be. Javasoljuk, hogy hagyja ki ezeket a fejléceket az alkalmazás ujjlenyomatának elkerülése érdekében.
Lásd: [a szabványos kiszolgálók fejlécének eltávolítása az Azure websites](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)szolgáltatásban.

### <a name="segregate-your-production-data"></a>A termelési adatai elkülönítése

A termelési adatait vagy a "valódi" adatait nem szabad olyan fejlesztési, tesztelési vagy egyéb célra használni, mint amit a vállalat szán. Az összes fejlesztéshez és teszteléshez maszkolt ([Névtelen](https://en.wikipedia.org/wiki/Data_anonymization)) adathalmazt kell használni.

Ez azt jelenti, hogy kevesebb ember fér hozzá a valós adataihoz, ami csökkenti a támadási felületet. Ez azt is jelenti, hogy kevesebb alkalmazott látja el személyes adataikat, ami kiküszöböli a titkosság esetleges megsértését.

### <a name="implement-a-strong-password-policy"></a>Erős jelszóházirend implementálása

A találgatásos és a szótáron alapuló találgatások elleni védelemhez erős jelszóházirend szükséges annak biztosításához, hogy a felhasználók összetett jelszót hozzanak létre (például 12 karakter hosszúságú, alfanumerikus és speciális karaktereket igénylő).

A Azure Active Directory B2C segítséget nyújt a jelszavas felügyelethez, az önkiszolgáló [jelszó-visszaállítást](../../active-directory-b2c/add-password-reset-policy.md), a [jelszó-visszaállítás kényszerítését](../../active-directory-b2c/force-password-reset.md)és egyebeket.

Az alapértelmezett fiókok elleni támadások elleni védelem érdekében ellenőrizze, hogy az összes kulcs és jelszó cserélhető-e, és hogy az erőforrások telepítése után létrejöttek vagy lecseréltek-e.

Ha az alkalmazásnak automatikusan kell létrehoznia a jelszavakat, ügyeljen arra, hogy a generált jelszavak véletlenszerűek legyenek, és magas entrópia legyenek.

### <a name="validate-file-uploads"></a>Fájlfeltöltés ellenőrzése

Ha az [alkalmazás lehetővé teszi a fájlfeltöltés használatát](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload), vegye figyelembe a kockázatos tevékenységhez szükséges óvintézkedéseket. A sok támadás első lépéseként egy rosszindulatú kód beszerzése egy támadás alatt álló rendszerbe. A fájlfeltöltés használatával a támadó elvégezheti ezt. A OWASP olyan megoldásokat kínál a fájlok érvényesítéséhez, amelyek biztosítják, hogy a feltöltött fájl biztonságos legyen.

A kártevők elleni védelem segítségével azonosíthatja és eltávolíthatja a vírusokat, kémprogramokat és egyéb kártevő szoftvereket. Telepítheti a [Microsoft antimalware](../fundamentals/antimalware.md) -t vagy egy Microsoft-partner Endpoint Protection-megoldását ([Trend Micro](https://www.trendmicro.com/azure/), [Broadcom](https://www.broadcom.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)és [Endpoint Protection](/configmgr/protect/deploy-use/endpoint-protection)).

A [Microsoft antimalware](../fundamentals/antimalware.md) olyan funkciókat tartalmaz, mint a valós idejű védelem, az ütemezett vizsgálat, a kártevők szervizelése, az aláírások frissítése, a motor frissítései, a minták jelentéskészítés és a kizárási események gyűjteménye. Az üzembe helyezéshez és a beépített észlelésekhez (riasztások és incidensek) egyszerűen integrálható a Microsoft antimalware és a partneri megoldások [Azure Security Center](../../security-center/security-center-partner-integration.md) használatával.

### <a name="dont-cache-sensitive-content"></a>Ne gyorsítótárazza a bizalmas tartalmat

Ne gyorsítótárazza a bizalmas tartalmat a böngészőben. A böngészők adatokat tárolhatnak a gyorsítótárazáshoz és az előzményekhez. Az Internet Explorer esetében a gyorsítótárazott fájlokat a rendszer egy olyan mappában tárolja, mint az ideiglenes Internet Files mappa. Ha ezek az oldalak ismét hivatkoznak rá, a böngésző megjeleníti a lapokat a gyorsítótárból. Ha a bizalmas adatok (a címe, a bankkártya adatai, a társadalombiztosítási szám, a Felhasználónév) megjelenik a felhasználó számára, előfordulhat, hogy az adatok a böngésző gyorsítótárában tárolódnak, és a böngésző gyorsítótárának vizsgálatával, vagy egyszerűen a böngésző **vissza** gombjának megnyomásával olvashatók be.

## <a name="verification"></a>Ellenőrzés

Az ellenőrzési fázis átfogó erőfeszítéseket tesz annak biztosítására, hogy a kód megfeleljen az előző fázisokban létrehozott biztonsági és adatvédelmi alapelveinek.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>A biztonsági rések megkeresése és javítása az alkalmazás függőségeiben

Az alkalmazás és a hozzá tartozó könyvtárak vizsgálatával azonosíthatja az összes ismert sebezhető összetevőt. A vizsgálat végrehajtásához rendelkezésre álló termékek közé tartozik a [OWASP függőségi ellenőrzés](https://www.owasp.org/index.php/OWASP_Dependency_Check), a[Snyk](https://snyk.io/)és a [fekete kacsa](https://www.blackducksoftware.com/).

A [sztaniol Security](https://www.tinfoilsecurity.com/) által működtetett sebezhetőségi vizsgálat a Azure app Service Web Apps számára érhető el. A [sztaniol biztonsági vizsgálata app Service révén](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) a fejlesztők és a rendszergazdák gyorsan, integrált és gazdaságos módon tudják feltárni és kezelni a biztonsági réseket, mielőtt egy rosszindulatú színész kihasználja őket.

> [!NOTE]
> [A sztaniol biztonság az Azure ad-vel is integrálható](../../active-directory/saas-apps/tinfoil-security-tutorial.md). A sztaniol biztonság és az Azure AD integrálásával a következő előnyöket nyújtja:
>  - Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a sztaniol biztonsághoz.
>  - A felhasználók az Azure AD-fiókjaik használatával automatikusan bejelentkezhetnek a sztaniol biztonságba (egyszeri bejelentkezés).
>  - A fiókokat egyetlen, központi helyen, a Azure Portal kezelheti.

### <a name="test-your-application-in-an-operating-state"></a>Az alkalmazás tesztelése operációs állapotban

A dinamikus Application Security Testing (Zsuzsanna) egy olyan folyamat, amely az alkalmazások működési állapotában történő tesztelését végzi a biztonsági rések megkeresése érdekében. A Zsuzsanna eszközök elemzik a programokat a biztonsági rések, például a memória sérülése, a kiszolgáló konfigurációja, a helyek közötti parancsfájlok, a felhasználói jogosultságok és az SQL-injektálások, valamint az egyéb kritikus fontosságú biztonsági problémák megtalálásához.

A Zsuzsanna eltér a statikus alkalmazások biztonsági tesztelésének (17:00). A 17:00-eszközök elemzik a forráskódot vagy a kód lefordított verzióit, ha a kód nem fut a biztonsági hibák megállapítása érdekében.

Zsuzsanna hajthat végre, lehetőleg egy biztonsági szakember (a [penetrációs teszter](../fundamentals/pen-testing.md) vagy a sebezhetőségi felmérés) segítségével. Ha egy biztonsági szakember nem érhető el, a Zsuzsanna saját kezűleg is elvégezheti egy webproxy-olvasóval és néhány betanítással. A Zsuzsanna-szkennert korán csatlakoztatva biztosíthatja, hogy ne vezessen be nyilvánvaló biztonsági problémákat a kódban. A webalkalmazási sebezhetőségi képolvasók listáját a [OWASP](https://owasp.org/www-community/Vulnerability_Scanning_Tools) webhelyén tekintheti meg.

### <a name="perform-fuzz-testing"></a>A fuzz-tesztelés végrehajtása

A [fuzz-tesztelés](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)során a program meghibásodását a helytelenül formázott vagy véletlenszerű adatmennyiség egy alkalmazásba való szándékos bevezetésével lehet kiváltani. Az előidéző program meghibásodása segít feltárni a potenciális biztonsági problémákat az alkalmazás felszabadítása előtt.

A [biztonsági kockázatok észlelése](https://www.microsoft.com/en-us/security-risk-detection/) a Microsoft egyedi fuzz Testing szolgáltatása, amely a szoftverek biztonság szempontjából kritikus fontosságú hibáinak megkeresésére szolgál.

### <a name="conduct-attack-surface-review"></a>Támadási felület felülvizsgálatának elvégzése

A támadási felületet a kód befejezése után felülvizsgálva segít meggyőződni arról, hogy az alkalmazás vagy a rendszer bármilyen tervezési vagy megvalósítási változását mérlegelték. Ezzel biztosítható, hogy a módosítások eredményeképpen létrehozott új támadási vektorok, beleértve a veszélyforrásokat, felülvizsgálva és enyhítve legyenek.

A támadási felületről az alkalmazás vizsgálatával készíthet képet. A Microsoft [a támadási](https://www.microsoft.com/download/details.aspx?id=58105)felület elemző eszközét is felhasználja. Számos kereskedelmi dinamikus tesztelési és sebezhetőségi vizsgálati eszköz vagy szolgáltatás közül választhat, például a [OWASP Zed Attack proxy Project](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project), a [Arachni](http://arachni-scanner.com/), a [Skipfish](https://code.google.com/p/skipfish/)és a [w3af](http://w3af.sourceforge.net/). Ezek a vizsgálati eszközök bemutatják az alkalmazást, és leképezik az alkalmazás azon részeit, amelyek elérhetők a weben keresztül. Az Azure Marketplace-en is kereshet hasonló [fejlesztői eszközöket](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1).

### <a name="perform-security-penetration-testing"></a>Biztonsági behatolási teszt végrehajtása

Győződjön meg arról, hogy az alkalmazás biztonságban van, mivel minden más funkciót tesztel. Az előkészítési és a üzembe helyezési folyamat standard részét a [behatolási teszt](../fundamentals/pen-testing.md) végzi. Rendszeres biztonsági tesztek és sebezhetőségi vizsgálat ütemezett végrehajtása a telepített alkalmazásokon, valamint a nyitott portok, végpontok és támadások figyelése.

### <a name="run-security-verification-tests"></a>Biztonsági ellenőrzési tesztek futtatása

Az Azure-hoz készült [Secure DevOps Kit](https://azsk.azurewebsites.net/index.html) (AzSK) az Azure platform több szolgáltatásának SVTs tartalmazza. Ezeket a SVTs rendszeresen futtatva biztosíthatja, hogy az Azure-előfizetése és az alkalmazást alkotó különböző erőforrások biztonságos állapotúak legyenek. Ezeket a teszteket a AzSK folyamatos integráció/folyamatos üzembe helyezés (CI/CD) bővítmények szolgáltatásával is automatizálhatja, amely a Visual Studio-bővítményként elérhetővé teszi a SVTs.

## <a name="next-steps"></a>Következő lépések

A következő cikkekben olyan biztonsági vezérlőket és tevékenységeket ajánlunk, amelyek segíthetnek a biztonságos alkalmazások tervezésében és üzembe helyezésében.

- [Biztonságos alkalmazások tervezése](secure-design.md)
- [Biztonságos alkalmazások telepítése](secure-deploy.md)