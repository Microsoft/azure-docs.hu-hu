---
title: Helyi gyorsítótár
description: Megtudhatja, hogyan működik a helyi gyorsítótár a Azure App Serviceban, és hogyan engedélyezheti, átméretezheti és lekérdezheti az alkalmazás helyi gyorsítótárának állapotát.
tags: optional
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.topic: article
ms.date: 03/04/2016
ms.custom: seodec18
ms.openlocfilehash: 81782f63199a9fe8f43f56aeefcd1c68951d57a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96852252"
---
# <a name="azure-app-service-local-cache-overview"></a>Azure App Service a helyi gyorsítótár áttekintése

> [!NOTE]
> A helyi gyorsítótár nem támogatott a Function apps vagy a Container App Service alkalmazásokban, például [Windows-tárolókban](quickstart-custom-container.md?pivots=container-windows) vagy [Linuxon app Service](overview.md#app-service-on-linux).


Azure App Service a tartalmat az Azure Storage tárolja, és tartós módon, tartalmi megosztásként is felszínre kerül. Ez a kialakítás a különböző alkalmazásokkal való együttműködésre szolgál, és a következő tulajdonságokkal rendelkezik:  

* A tartalom megosztása az alkalmazás több virtuális gép (VM) példánya között történik.
* A tartalom tartós, és az alkalmazások futtatásával módosítható.
* A naplófájlok és a diagnosztikai adatfájlok ugyanabban a megosztott tartalom mappában érhetők el.
* Az új tartalom közzététele közvetlenül frissíti a tartalom mappát. Azonnal megtekintheti ugyanezeket a tartalmakat az SCM webhelyén és a futó alkalmazásban (általában bizonyos technológiák, például a ASP.NET), hogy a legújabb tartalom beszerzése érdekében egy alkalmazás újraindítását kezdeményezzen bizonyos fájlokon.

Habár számos alkalmazás használja ezeket a funkciókat, egyes alkalmazásokhoz csak nagy teljesítményű, csak olvasható tartalom-áruházra van szükség, amely magas rendelkezésre állással futtatható. Ezek az alkalmazások kihasználhatják egy adott helyi gyorsítótár virtuálisgép-példányát.

A Azure App Service helyi gyorsítótár szolgáltatás a tartalom webes szerepkörét jeleníti meg. Ez a tartalom az aszinkron módon a hely indításakor létrehozott tárolási tartalom írási és elvetési gyorsítótára. Ha a gyorsítótár elkészült, a hely a gyorsítótárazott tartalomon való futtatásra van állítva. A helyi gyorsítótárban futó alkalmazások a következő előnyöket nyújtják:

* Ezek a késések az Azure Storage-ban tárolt tartalomhoz való hozzáféréskor jelentkeznek.
* Ezek a tervezett frissítések és a nem tervezett leállások, valamint az Azure Storage szolgáltatással kapcsolatos egyéb fennakadások, amelyek a tartalom megosztását kiszolgáló kiszolgálókon történnek.
* A tárolási megosztás változásai miatt kevesebb alkalmazás újraindul.

## <a name="how-the-local-cache-changes-the-behavior-of-app-service"></a>Hogyan módosítja a helyi gyorsítótár a App Service viselkedését
* A _D:\home_ a helyi gyorsítótárra mutat, amely a virtuálisgép-példányon jön létre, amikor az alkalmazás elindul. A _D:\Local_ továbbra is az ideiglenes VM-specifikus tárterületre mutat.
* A helyi gyorsítótár a megosztott _/site_ és a _/siteextensions_ mappák egy egyszeri másolatát tartalmazza, a következő helyen: _D:\home\site_ és _D:\home\siteextensions_. Az alkalmazás indításakor a rendszer a helyi gyorsítótárba másolja a fájlokat. Az egyes alkalmazások két mappájának mérete alapértelmezés szerint 1 GB-ra van korlátozva, de 2 GB-ra is növelhető. Vegye figyelembe, hogy mivel a gyorsítótár mérete növekszik, hosszabb időt vesz igénybe a gyorsítótár betöltése. Ha megnövelte a helyi gyorsítótár korlátját 2 GB-ra, és a másolt fájlok mérete meghaladja a 2 GB-os maximális méretet, App Service csendben figyelmen kívül hagyja a helyi gyorsítótárat, és beolvassa a távoli fájlmegosztást. Ha nincs korlát meghatározva, vagy a korlát értéke 2 GB-nál kisebb, és a másolt fájlok mérete meghaladja a korlátot, akkor a telepítés vagy a swap hibával meghiúsulhat.
* A helyi gyorsítótár írható-olvasható. Ha azonban az alkalmazás a virtuális gépeket helyezi át, vagy újraindul, a módosítások elvesznek. Ne használja a helyi gyorsítótárat olyan alkalmazások esetében, amelyek kritikus fontosságú adatokat tárolnak a tartalom-tárolóban.
* A _D:\home\LogFiles_ és a _D:\home\Data_ naplófájlokat és alkalmazásadatokat tartalmaznak. A két almappát a rendszer helyileg tárolja a virtuálisgép-példányon, és rendszeres időközönként átmásolja őket a megosztott tartalom tárolójába. Az alkalmazások a naplófájlokat és az adatfájlokat a mappákba írással is megőrzik. A megosztott tartalom tárolójába való másolás azonban a legjobb megoldás, ezért lehetséges, hogy a naplófájlok és az adatmennyiség elvész a virtuálisgép-példányok hirtelen összeomlása miatt.
* A [naplózási adatfolyamra](troubleshoot-diagnostic-logs.md#stream-logs) a legalkalmasabb másolási lehetőség vonatkozik. Akár egy percet is megfigyelheti a továbbított naplókban.
* A megosztott tartalom tárolóban módosul a _naplófájlok_ _és az adatmappák mappa szerkezete_ a helyi gyorsítótárat használó alkalmazásokhoz. A bennük található almappákban az "egyedi azonosító" és az időbélyeg elnevezési mintája látható. Az almappák mindegyike egy olyan virtuálisgép-példánynak felel meg, amelyben az alkalmazás fut vagy fut.
* A _D:\home_ egyéb mappái a helyi gyorsítótárban maradnak, és nem másolódnak át a megosztott tartalom-tárolóba.
* Az alkalmazás központi telepítése bármely támogatott módszerrel közvetlenül a tartós megosztott tartalom tárolójába kerül. A helyi gyorsítótárban lévő _D:\home\site_ -és _D:\home\siteextensions_ -mappák frissítéséhez az alkalmazást újra kell indítani. Az életciklus zökkenőmentesvé tételéhez tekintse meg a cikk későbbi részében található információkat.
* Az SCM-hely alapértelmezett tartalmi nézete továbbra is megegyezik a megosztott tartalom tárolójával.

## <a name="enable-local-cache-in-app-service"></a>Helyi gyorsítótár engedélyezése a App Serviceban 

> [!NOTE]
> A helyi gyorsítótár nem támogatott az **F1** vagy a **D1** szinten. 

A helyi gyorsítótárat a fenntartott Alkalmazásbeállítások együttes használatával konfigurálhatja. Az Alkalmazásbeállítások a következő módszerekkel konfigurálhatók:

* [Azure Portal](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Helyi gyorsítótár konfigurálása a Azure Portal használatával
<a name="Configure-Local-Cache-Portal"></a>

A helyi gyorsítótárat a webalkalmazások alapján engedélyezheti az alkalmazás-beállítás használatával: `WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Azure Portal Alkalmazásbeállítások: helyi gyorsítótár](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Helyi gyorsítótár konfigurálása Azure Resource Manager használatával
<a name="Configure-Local-Cache-ARM"></a>

```json

...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],

    "properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "1000"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>A méret beállítás módosítása a helyi gyorsítótárban
Alapértelmezés szerint a helyi gyorsítótár mérete **1 GB**. Ebbe beletartozik a/site másolt és a/siteextensions mappa, valamint a helyileg létrehozott naplók és adatmappák. A korlát növeléséhez használja az alkalmazás beállítását `WEBSITE_LOCAL_CACHE_SIZEINMB` . Az alkalmazások mérete legfeljebb **2 GB** (2000 MB) lehet. Vegye figyelembe, hogy a méret növekedésével hosszabb időt vesz igénybe a helyi gyorsítótár betöltése.

## <a name="best-practices-for-using-app-service-local-cache"></a>Ajánlott eljárások App Service helyi gyorsítótár használatához
Javasoljuk, hogy a helyi gyorsítótárat az [átmeneti környezetek](../app-service/deploy-staging-slots.md) szolgáltatással együtt használja.

* Adja hozzá a *Sticky* app beállítást az `WEBSITE_LOCAL_CACHE_OPTION` `Always` **éles** tárolóhely értékével. Ha használja a `WEBSITE_LOCAL_CACHE_SIZEINMB` -t, az éles tárolóhelyként is hozzáadja az értéket.
* Hozzon létre egy **átmeneti** tárolóhelyet, és tegye közzé az átmeneti tárolóhelyen. Általában nem úgy állítja be az átmeneti tárolóhelyet, hogy a helyi gyorsítótárat használja, hogy zökkenőmentes Build-üzembe helyezés-tesztelési életciklust engedélyezzen az átmeneti tároláshoz, ha az üzemi tárolóhelyhez tartozó helyi gyorsítótár előnyeit kapja.
* Tesztelje a webhelyet az átmeneti tárolóhelyen.  
* Ha elkészült, állítson ki egy [swap-műveletet](../app-service/deploy-staging-slots.md#Swap) az átmeneti és az üzemi tárolóhelyek között.  
* A Sticky-beállítások közé tartozik a név és a Sticky to a slot. Tehát amikor az előkészítési pont az éles környezetbe kerül, örökli a helyi gyorsítótár-alkalmazás beállításait. Az újonnan felcserélt üzemi tárolóhely néhány perc elteltével a helyi gyorsítótárral fog futni, és a rendszer a swap után bemelegíti a slot bemelegedési részét. Tehát amikor a tárolóhely cseréje befejeződött, az éles tárolóhely a helyi gyorsítótáron fut.

## <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

### <a name="how-can-i-tell-if-local-cache-applies-to-my-app"></a>Honnan tudhatom meg, hogy a helyi gyorsítótár vonatkozik-e az alkalmazásra?
Ha az alkalmazásnak nagy teljesítményű, megbízható tartalom-tárolóra van szüksége, a nem használja a Content Store-t a kritikus adatokat futásidőben, és a teljes méretnél kevesebb, mint 2 GB-ot, a válasz igen. A/site és a/siteextensions mappák teljes méretének lekéréséhez használhatja az "Azure Web Apps lemezhasználat" nevű helyet.

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Honnan tudhatom meg, hogy a webhelyem a helyi gyorsítótár használatára váltott-e?
Ha az átmeneti környezetekben a helyi gyorsítótár szolgáltatást használja, a swap művelet nem fejeződik be, amíg a helyi gyorsítótár be nem fejeződik. Ha ellenőrizni szeretné, hogy a hely a helyi gyorsítótáron fut-e, ellenőrizze a munkavégző folyamat környezeti változóját `WEBSITE_LOCALCACHE_READY` . A [munkavégző folyamat környezeti változójának](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) lapján található utasítások segítségével több példányon is elérheti a munkavégző folyamat környezeti változóját.  

### <a name="i-just-published-new-changes-but-my-app-does-not-seem-to-have-them-why"></a>Most közzétettem az új módosításokat, de az alkalmazásom úgy tűnik, hogy nem rendelkezik velük. Miért?
Ha az alkalmazás helyi gyorsítótárat használ, a legújabb módosítások beszerzéséhez újra kell indítania a helyet. Nem kívánja közzétenni a módosításokat egy éles helyen? Tekintse meg a tárolóhelyek beállításait az előző ajánlott eljárások szakaszban.

> [!NOTE]
> A [csomag](deploy-run-package.md) központi telepítésének futtatása beállítás nem kompatibilis a helyi gyorsítótárral.

### <a name="where-are-my-logs"></a>Hol találhatók a naplók?
A helyi gyorsítótárral a naplók és az adatmappák egy kicsit máshogy néznek ki. Az almappák szerkezete azonban változatlan marad, azzal a különbséggel, hogy az almappák az "egyedi virtuálisgép-azonosító" és az időbélyeg formátuma alatt vannak.

### <a name="i-have-local-cache-enabled-but-my--app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Engedélyezve van a helyi gyorsítótár, de az alkalmazás továbbra is újraindul. Miért? Azt hittem, hogy a helyi gyorsítótár segített a gyakori alkalmazás-újraindítások során.
A helyi gyorsítótár segít megelőzni a tárolóval kapcsolatos alkalmazások újraindítását. Az alkalmazás azonban továbbra is elvégezhető a virtuális gép tervezett infrastruktúrájának frissítése során. Az alkalmazás általános újraindítása, ha a helyi gyorsítótár engedélyezve van, kevesebbnek kell lennie.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>A helyi gyorsítótár kizár minden könyvtárat a gyorsabb helyi meghajtóra?
A tárolási tartalmat átmásoló lépés részeként minden adattár nevű mappa ki van zárva. Ez segít olyan forgatókönyvek esetén, ahol a webhely tartalma tartalmazhat olyan verziókövetés-tárházat, amely nem feltétlenül szükséges az alkalmazás napi működéséhez. 

### <a name="how-to-flush-the-local-cache-logs-after-a-site-management-operation"></a>Hogyan ürítheti ki a helyi gyorsítótár naplóit a hely felügyeleti művelete után?
A helyi gyorsítótár naplófájljainak kiürítéséhez állítsa le és indítsa újra az alkalmazást. Ez a művelet törli a régi gyorsítótárat. 
