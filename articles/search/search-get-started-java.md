---
title: 'Gyors útmutató: Azure Search index létrehozása javában'
description: Ismerteti, hogyan lehet indexet létrehozni, adatok betöltésére és lekérdezéseket futtatni a Java és a Azure Search REST API-k használatával.
services: search
author: jj09
manager: jlembicz
ms.service: search
ms.topic: conceptual
ms.date: 08/26/2018
ms.author: jjed
ms.custom: seodec2018, seo-java-july2019
ms.openlocfilehash: 7172cd01ca881ec3027854444107b0744b65feb3
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489790"
---
# <a name="quickstart-create-an-azure-search-index-in-java"></a>Gyors útmutató: Azure Search index létrehozása javában
> [!div class="op_single_selector"]
> * [Portál](search-get-started-portal.md)
> * [.NET](search-howto-dotnet-sdk.md)
> 
> 

Ismerje meg, hogyan hozhat létre olyan egyéni Java keresőalkalmazást, amely az Azure Search szolgáltatást használja a keresésekhez. Ez az oktatóanyag az [Azure Search szolgáltatás REST API](https://msdn.microsoft.com/library/dn798935.aspx)-ját használja ebben a gyakorlatban az objektumok és műveletek összeállításához.

A minta futtatásához rendelkeznie kell egy Azure Search-szolgáltatással, amelyre az [Azure Portalon](https://portal.azure.com) regisztrálhat. A részletes utasításokat lásd: [Azure Search szolgáltatás létrehozása a portálon](search-create-service-portal.md).

A minta összeállításához és teszteléséhez a következő szoftvereket használtuk:

* [Eclipse IDE for Java EE Developers](https://www.eclipse.org/downloads/packages/release/photon/r/eclipse-ide-java-ee-developers). Ügyeljen arra, hogy az EE-verziót töltse le. Az ellenőrzési lépések egyikének olyan funkcióra van szüksége, amely csak ebben a kiadásban található.
* [JDK 8u181](https://aka.ms/azure-jdks)
* [Apache Tomcat 8.5.33](https://tomcat.apache.org/download-80.cgi#8.5.33)

## <a name="about-the-data"></a>Tudnivalók az adatokról
A mintaalkalmazás az [Amerikai Egyesült Államok geológiai szolgáltatásainak (United States Geological Services, USGS)](https://geonames.usgs.gov/domestic/download_data.htm) adatait használja, az adatkészlet méretének csökkentése érdekében Rhode Island államra szűrve. Ezeket az adatokat fogjuk használni egy olyan keresőalkalmazás létrehozásához, amely jellegzetes épületeket, például kórházakat és iskolákat, valamint geológiai jellegzetességeket, például folyókat, tavakat és hegycsúcsokat ad vissza eredményül.

Ebben az alkalmazásban a **SearchServlet. Java** program egy indexelő szerkezet használatával építi fel [](https://msdn.microsoft.com/library/azure/dn798918.aspx) és tölti be az indexet, és beolvassa a szűrt USGS-adatkészletet egy Azure SQL Database. Az előre meghatározott hitelesítő adatokat és az online adatforrás kapcsolódási adatait a programkód tartalmazza. Az adatelérés szempontjából nincs szükség további konfigurációra.

> [!NOTE]
> Az adatkészlethez olyan szűrőt alkalmaztunk, hogy az ingyenes tarifacsomag 10 000 dokumentumos korlátja alatt maradjunk. Ha a standard csomagot használja, arra nem vonatkozik ez a korlátozás, és módosíthatja úgy a kódot, hogy nagyobb adatkészletet használhasson. Az egyes tarifacsomagok kapacitásával kapcsolatos részletes információkat lásd: [Korlátozások és megkötések](search-limits-quotas-capacity.md).
> 
> 

## <a name="about-the-program-files"></a>Tudnivalók a programfájlokról
Az alábbi lista a példához kapcsolódó fájlokat ismerteti.

* Search.jsp: Felhasználói felületet biztosít
* SearchServlet.java: Metódusok (az MVC vezérlőhöz hasonlóan)
* SearchServiceClient.java: HTTP-kérések kezelése
* SearchServiceHelper.java: Egy segítő osztály, amely statikus metódusokat biztosít
* Document. Java: Az adatmodellt biztosítja
* config. properties: Beállítja a keresési szolgáltatás URL-címét és`api-key`
* Pom. XML: Maven-függőség

<a id="sub-2"></a>

## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>A szolgáltatás nevének és `api-key` a Azure Search szolgáltatásának megkeresése
A Azure Search összes REST API hívásához meg kell adnia a szolgáltatás URL-címét `api-key`és a-t. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Az ugrás sávon kattintson a **Keresési szolgáltatás** elemre, hogy megjelenjen az előfizetéséhez kapcsolódó összes Azure Search szolgáltatás.
3. Válassza ki a használni kívánt szolgáltatást.
4. A szolgáltatás irányítópultján megjelennek az alapvető információkat tartalmazó csempék, valamint az adminisztrációs kulcsok eléréséhez szükséges kulcs ikon.
   
      ![A felügyeleti kulcsok a szolgáltatás irányítópultról való elérését bemutató képernyőkép][3]
5. Másolja át a szolgáltatás URL-címét és egy adminisztrációs kulcsot. Később lesz rájuk szüksége, amikor hozzáadja őket a **config.properties** fájlhoz.

## <a name="download-the-sample-files"></a>A mintafájlok letöltése
1. Lépjen a GitHubon található [search-node-indexer-demo](https://github.com/Azure-Samples/search-java-indexer-demo) elemre.
2. Kattintson a **Download ZIP** (ZIP-fájl letöltése) elemre, mentse a .zip-fájlt a lemezre, és bontsa ki a benne található összes fájlt. A Java-munkaterületére csomagolja ki a fájlokat, hogy később könnyebben megtalálja a projektet.
3. A mintafájlok csak olvashatók. Kattintson a jobb gombbal a mappa tulajdonságaira, és törölje a csak olvasható attribútumot.

Minden további fájlmódosítás és utasításfuttatás az ebben a mappában lévő fájlokra vonatkozóan fog történni.  

## <a name="import-project"></a>Projekt importálása
1. Az Eclipse-ben válassza ki a **File** (Fájl)  > **Import** (Importálás)  > **General** (Általános)  > **Existing Projects into Workspace** (Meglévő projekteket a munkaterületre) lehetőséget.
   
    ![Meglévő projekt importálását bemutató képernyőfelvétel][4]
2. A **Select root directory** (Gyökérkönyvtár kiválasztása) ablakban keresse meg a mintafájlokat tartalmazó mappát. Válassza ki a .project mappát tartalmazó mappát. A projektnek kiválasztott elemként meg kell jelennie a **Projects** (Projektek) listán.
   
    ![Képernyőfelvétel: a projektek listájának megjelenítése a projektek importálása ablakban][12]
3. Kattintson a **Befejezés**gombra.
4. A **Project Explorer** (Projektböngésző) segítségével megtekintheti és szerkesztheti a fájlokat. Ha az még nincs megnyitva, kattintson a **Window** (Ablak)  > **Show View** (Nézet megjelenítése)  > **Project Explorer** (Projektböngésző) lehetőségre, vagy nyissa meg a megfelelő parancsikonnal.

## <a name="configure-the-service-url-and-api-key"></a>Konfigurálja a szolgáltatás URL-címét és`api-key`
1. A **Project Explorerben**kattintson duplán a **config. properties** elemre a kiszolgáló nevét és `api-key`a-t tartalmazó konfigurációs beállítások szerkesztéséhez.
2. Tekintse át a jelen cikk korábbi részében ismertetett lépéseket, ahol megtalálta a szolgáltatás URL-címét és `api-key` a [Azure Portal](https://portal.azure.com), hogy beolvassa azokat az értékeket, amelyeket mostantól a **config. properties fájlban**fog megadni.
3. A **config. properties fájlban**cserélje le az "API-kulcs `api-key` " kifejezést a szolgáltatásra. A következő szolgáltatás neve (az URL https://servicename.search.windows.net) első összetevője a "szolgáltatás neve" kifejezés helyébe lép ugyanabban a fájlban.
   
    ![Az API-kulcs lecserélését bemutató képernyőkép][5]

## <a name="configure-the-project-build-and-runtime-environments"></a>A projekt, a build és a futtatókörnyezetek konfigurálása
1. Az Eclipse Project Explorer (Projektböngésző) nézetében kattintson a jobb gombbal a Project (Projekt) > **Properties** (Tulajdonságok)  > **Project Facets** (A projekt aspektusai) elemre.
2. Válassza ki a **Dynamic Web Module** (Dinamikus webmodul), a **Java** és a **JavaScript** elemet.
   
    ![A projekt dimenzióinak kiválasztását bemutató képernyőfelvétel][6]
3. Kattintson az **Alkalmaz** gombra.
4. Válassza ki a **Window** (Ablak)  > **Preferences** (Beállítások)  > **Server** (Kiszolgáló)  > **Runtime Environments** (Futtatókörnyezetek)  > **Add..** (Hozzáadás) lehetőséget.
5. Bontsa ki az Apache elemet, és válassza ki az Apache Tomcat-kiszolgáló korábban telepített verzióját. Mi a 8-as verziót telepítettük a rendszerünkre.
   
    ![Képernyőfelvétel: a futásidejű környezet ablakának helye, ahol kiválaszthatja az Apache Tomcat verzióját][7]
6. A következő oldalon adja meg a Tomcat telepítési könyvtárát. Windows rendszerű számítógépeken ez valószínűleg a következő lesz: C:\Program Files\Apache Software Foundation\Tomcat *verzió*.
7. Kattintson a **Befejezés**gombra.
8. Válassza ki a **Window** (Ablak)  > **Preferences** (Beállítások)  > **Java** > **Installed JREs** (Telepített JRE-k)  > **Add** (Hozzáadás) lehetőséget.
9. Az **Add JRE** (JRE hozzáadása) panelen válassza ki a **Standard VM** elemet.
10. Kattintson a **Tovább** gombra.
11. A JRE Definition (JRE_definíció) ablakban, a JRE kezdőlapján kattintson a **Directory** (Könyvtár) elemre.
12. Navigáljon a **Program Files** (Programfájlok)  > **Java** könyvtárra, és válassza ki a korábban telepített JDK-t. Fontos, hogy a JDK-t JRE-ként válassza ki.
13. Az Installed JREs (Telepített JRE-k) panelen válassza ki a **JDK** elemet. A beállításainak az alábbi képernyőfelvételhez hasonlóan kell kinéznie.
    
    ![Képernyőfelvétel a JDK kiválasztásáról a telepített JRE-ként][9]
14. Ha az alkalmazást egy külső böngészőablakban szeretné megnyitni, válassza ki a **Window** (Ablak)  > **Web Browser** (Webböngésző)  > **Internet Explorer** lehetőséget. Külső böngészővel fokozhatja a webalkalmazás használatának élményét.
    
    ![Az Internet Explorer külső böngészőablakként való kiválasztását bemutató képernyőkép][8]

Ezzel befejezte a konfigurálási feladatokat. A következő lépésben felépíti és futtatja a projektet.

## <a name="build-the-project"></a>A projekt felépítése
1. A projekt konfigurálásához a Project Explorer (Projektböngésző) nézetben kattintson a jobb gombbal a projekt nevére, és válassza ki a **Run As** (Futtatás másként)  > **Maven build...** elemet.
   
    ![Képernyőfelvétel: a Maven-Build kiválasztása a Project Explorer ablakában][10]
2. Az Edit Configuration (Konfiguráció szerkesztése) panelen a Goals (Célok) mezőbe írja be a „clean install” („tiszta telepítés”) kifejezést, majd kattintson a **Run** (Futtatás) gombra.

Az állapotüzenetek kimenetként a konzolablakban jelennek meg. A BUILD SUCCESS (Sikeres felépítés) üzenetnek kell megjelennie, amely azt jelzi, hogy a projekt hibák nélkül felépült.

## <a name="run-the-app"></a>Az alkalmazás futtatása
Utolsó lépésként futtassa le az alkalmazást egy helyi kiszolgáló futtatókörnyezetében.

Ha az Eclipse keretrendszerben még nem határozta meg egy kiszolgáló futtatókörnyezetét, először azt kell elvégeznie.

1. A Project Explorer (Projektböngésző) nézetben bontsa ki a **WebContent** elemet.
2. Kattintson a jobb gombbal a **Search.jsp** fájlra, majd kattintson a  > **Run As** (Futtatás másként)  > **Run on Server** (Futtatás a kiszolgálón) elemre. Válassza ki az Apache Tomcat kiszolgálót, majd kattintson a **Run** (Futtatás) gombra.

> [!TIP]
> Ha nem alapértelmezett munkaterületen tárolja a projektet, a kiszolgálóindítási hiba elkerülése érdekében úgy kell módosítania a **Run Configuration** (Konfiguráció futtatása) beállítást, hogy a projekt helyére mutasson. A Project Explorer (Projektböngésző) nézetben kattintson a jobb gombbal a **Search.jsp** fájlra, majd kattintson a  > **Run As** (Futtatás másként)  > **Run Configurations** (Konfigurációk futtatása) elemre. Válassza ki az Apache Tomcat kiszolgálót. Kattintson az **Arguments** (Argumentumok) elemre. A projektet tartalmazó mappa beállításához kattintson a **Workspace** (Munkaterület) vagy a **File System** (Fájlrendszer) elemre.
> 
> 

Az alkalmazás futtatásakor egy keresőmezőt tartalmazó böngészőablaknak kell megjelennie, ahová beírhatja a kifejezéseket.

Várjon körülbelül egy percet, mielőtt a **Search** (Keresés) gombra kattintana, hogy a szolgáltatásnak legyen elég ideje az index létrehozására és betöltésére. Ha a HTTP 404 hibaüzenet jelenik meg, csak egy kicsit tovább kell várnia az újrapróbálkozás előtt.

## <a name="search-on-usgs-data"></a>USGS-adatok keresése
Az USGS-adatkészlet a Rhode Island államra vonatkozó rekordokat tartalmaz. Ha rákattint egy üres keresőmező **Search** (Keresés) gombjára, megjelenik az 50 legfontosabb bejegyzés; ez az alapértelmezett viselkedés.

A keresett kifejezés beírása elindítja a keresőmotort. Próbáljon meg a helyhez kötődő nevet beírni. „Roger Williams” volt Rhode Island első kormányzója. Számos parkot, épületet és iskolát neveztek el róla.

![Az USGS-adatkeresés módját bemutató képernyőfelvétel][11]

Megpróbálhatja beírni az alábbi kifejezések bármelyikét is:

* Pawtucket
* Pembroke
* goose+cape

## <a name="next-steps"></a>További lépések
Ez az Azure Search első oktatóanyaga, amely Java és USGS-adatkészlet alapján készült. Idővel majd tovább bővítjük oktatóanyagunkat, és olyan kiegészítő keresési funkciókat fogunk bemutatni, amelyeket esetleg szívesen használna egyéni megoldásaiban.

Ha már rendelkezik bizonyos tapasztalattal az Azure Search használatában, ezt a mintát akár ugródeszkaként is használhatja a további kísérletezéshez, például bővítheti a [keresőoldalt](search-pagination-page-layout.md) vagy [jellemzőalapú navigációt](search-faceted-navigation.md) valósíthat meg. A keresési eredmények oldalát is tovább fejlesztheti számok és kötegelt dokumentumok hozzáadásával úgy, hogy a felhasználók lapozhassanak az eredmények között.

Mik az Azure Search újdonságai? Azt javasoljuk, próbáljon ki más oktatóanyagokat is, hogy jobban megismerhesse, mit hozhat létre. További forrásokat a [dokumentációs oldalunkon](https://azure.microsoft.com/documentation/services/search/) talál. 

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-1.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png
