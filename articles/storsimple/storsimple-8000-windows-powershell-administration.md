---
title: PowerShell a StorSimple-eszközök kezeléséhez
description: Ismerje meg, hogyan kezelheti a StorSimple-eszközt a Windows PowerShell StorSimple-bővítménye használatával.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/25/2021
ms.author: alkohli
ms.openlocfilehash: 3292f337a39e2adb7dc5e78f12785ead8759f5d1
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611946"
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Eszköz felügyelete a Windows PowerShell StorSimple-bővítményével

## <a name="overview"></a>Áttekintés

A Windows PowerShell StorSimple-bővítménye parancssori felületet biztosít, amellyel kezelheti Microsoft Azure StorSimple eszközét. Ahogy a neve is sugallja, ez egy Windows PowerShell-alapú parancssori felület, amely korlátozott RunSpace épül. A felhasználó szemszögéből a parancssorban egy korlátozott RunSpace jelenik meg a Windows PowerShell korlátozott verziójaként. A Windows PowerShell néhány alapszintű funkciójának fenntartása mellett az illesztőfelület más, dedikált parancsmagokkal is rendelkezik, amelyek a Microsoft Azure StorSimple eszköz felügyeletére irányulnak.

Ez a cikk a Windows PowerShell StorSimple-bővítménye funkcióit ismerteti, beleértve az interfészhez való csatlakozás módját, valamint az ezen felületen keresztül elvégezhető lépésenkénti eljárásokra és munkafolyamatokra mutató hivatkozásokat is tartalmaz. A munkafolyamatok közé tartozik az eszköz regisztrálása, a hálózati adapter konfigurálása az eszközön, az eszköz karbantartási módba kapcsolását igénylő frissítések telepítése, az eszköz állapotának módosítása, valamint az esetlegesen felmerülő problémák elhárítása.

A cikk elolvasása után a következőket teheti:

* A Windows PowerShell StorSimple-bővítménye használatával csatlakozhat a StorSimple-eszközhöz.
* A StorSimple-eszköz felügyelete a Windows PowerShell StorSimple-bővítménye használatával.
* Segítség kérése Windows PowerShell StorSimple-bővítményeban.

> [!NOTE]
> * Windows PowerShell StorSimple-bővítménye-parancsmagokkal felügyelheti a StorSimple-eszközt soros konzolról vagy távolról a Windows PowerShell távelérés használatával. Az ezen felületen használható egyes parancsmagokkal kapcsolatos további információkért tekintse meg a [Windows PowerShell StorSimple-bővítménye parancsmag-referenciáját](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps&preserve-view=true).
> * A Azure PowerShell StorSimple-parancsmagok a parancsmagok egy másik gyűjteménye, amely lehetővé teszi a StorSimple-és áttelepítési feladatok automatizálását a parancssorból. A StorSimple Azure PowerShell-parancsmagokkal kapcsolatos további információkért nyissa meg az [Azure StorSimple parancsmag-referenciát](/powershell/module/servicemanagement/azure.service/#azure).


A Windows PowerShell StorSimple-bővítménye a következő módszerek egyikével érheti el:

* [Kapcsolódás a StorSimple-eszköz soros konzolhoz](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Távoli kapcsolódás a StorSimple a Windows PowerShell használatával](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Kapcsolódás Windows PowerShell StorSimple-bővítménye az eszköz soros konzolján keresztül

A Windows PowerShell StorSimple-bővítményehoz való kapcsolódáshoz [letöltheti a PuTTY](https://www.putty.org/) vagy hasonló terminál-emulációs szoftvert. A PuTTY-t kifejezetten a Microsoft Azure StorSimple eszköz eléréséhez kell konfigurálnia. Az alábbi témakörök részletesen ismertetik a PuTTY konfigurálásának és az eszközhöz való kapcsolódásnak a lépéseit. A soros konzolon a különböző menüpontokat is ismertetjük.

### <a name="putty-settings"></a>PuTTY-beállítások

Győződjön meg arról, hogy a következő Putty-beállításokat használja a soros konzolról a Windows PowerShell felületéhez való kapcsolódáshoz.

#### <a name="to-configure-putty"></a>A PuTTY konfigurálása

1. A PuTTY **újrakonfigurálása** párbeszédpanel **Kategória** ablaktábláján válassza a **billentyűzet** elemet.
2. Győződjön meg arról, hogy a következő beállítások vannak kiválasztva (az új munkamenet indításakor az alapértelmezett beállítások).
   
   | Billentyûzet eleme | Válassza ezt: |
   | --- | --- |
   | Backspace-kulcs |Control-? (127) |
   | Kezdőlap és a Befejezés kulcsa |Standard |
   | Funkcióbillentyűk és billentyűzet |ESC [n ~ |
   | Kurzor kulcsainak kezdeti állapota |Normál |
   | Numerikus billentyűzet kezdeti állapota |Normál |
   | További billentyűzet-funkciók engedélyezése |Control-Alt eltér a AltGr |
   
    ![Támogatott Putty-beállítások](./media/storsimple-windows-powershell-administration/IC740877.png)
3. Kattintson az **Alkalmaz** gombra.
4. A **Kategória** ablaktáblán válassza a **fordítás** lehetőséget.
5. A **távoli karakterkészlet** listából válassza az **UTF-8** elemet.
6. **A vonalas rajzolási karakterek** felügyelete alatt válassza a **Unicode-vonal rajzolási kódjának használata** lehetőséget. Az alábbi képernyőfelvételen a megfelelő Putty-kijelölések láthatók.
   
    ![UTF-Putty beállításai](./media/storsimple-windows-powershell-administration/IC740878.png)
7. Kattintson az **Alkalmaz** gombra.

Mostantól a PuTTY segítségével csatlakozhat az eszköz soros konzolhoz az alábbi lépésekkel.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>Tudnivalók a soros konzolról

Amikor a soros konzolon keresztül éri el a StorSimple-eszköz Windows PowerShell felületét, megjelenik egy szalagcím üzenet, amelyet a menüpontok követnek.

A szalagcím üzenet olyan alapszintű StorSimple-információkat tartalmaz, mint például a modell, a név, a telepített szoftver verziója és az elérni kívánt vezérlő állapota. Az alábbi képen egy szalagcímre mutató üzenet látható.

![Soros szalagcím üzenet](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> A szalagcím üzenettel azonosíthatja, hogy az a vezérlő, amelyhez csatlakozik, _aktív_ vagy _passzív_.

Az alábbi képen a soros konzol menüjében elérhető különböző RunSpace lehetőségek láthatók.

![Az eszköz regisztrálása 2](./media/storsimple-windows-powershell-administration/IC740906.png)

A következő beállítások közül választhat:

1. **Jelentkezzen be teljes körű hozzáféréssel.**
   Ezzel a beállítással kapcsolódhat (a megfelelő hitelesítő adatokkal) a helyi vezérlő **SSAdminConsole** RunSpace. (A helyi vezérlő az a vezérlő, amelyet jelenleg a StorSimple-eszköz soros konzolján keresztül érnek el.) Ezzel a beállítással engedélyezhető, hogy Microsoft ügyfélszolgálata a nem korlátozott RunSpace (a támogatási munkamenetet) a lehetséges eszközök hibáinak elhárításához. Ha az 1. lehetőséget használja a bejelentkezéshez, engedélyezheti, hogy a Microsoft ügyfélszolgálata mérnök egy adott parancsmag futtatásával hozzáférhessen a korlátlan RunSpace. További részletekért tekintse meg a [támogatási munkamenet elindítása című témakört](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).
   
2. **Jelentkezzen be a társ-vezérlőbe teljes hozzáféréssel.**
   Ez a beállítás megegyezik az 1. lehetőséggel, azzal a különbséggel, hogy (a megfelelő hitelesítő adatokkal) csatlakozik a **SSAdminConsole** RunSpace a társ-vezérlőn. Mivel a StorSimple-eszköz egy magas rendelkezésre állású eszköz, amely két vezérlővel rendelkezik aktív-passzív konfigurációban, a társ a soros konzolon keresztül elérni kívánt eszköz másik vezérlőjét jelöli.
   Az 1. lehetőséghez hasonlóan ez a beállítás is lehetővé teszi a Microsoft ügyfélszolgálata számára, hogy hozzáférjenek a nem korlátozott RunSpace egy társ-vezérlőn.

3. **Korlátozott hozzáférésű kapcsolódás.**
   Ez a beállítás a Windows PowerShell felületének korlátozott módban való elérésére szolgál. A rendszer nem kéri a hozzáférési hitelesítő adatokat. Ez a beállítás az 1. és a 2. lehetőséghez képest egy szűkebb RunSpace csatlakozik.  Az 1. beállításon keresztül elérhető feladatok némelyike *nem* hajtható végre a következő RunSpace:
   
   * Visszaállítás a gyári beállításokra
   * Jelszó módosítása
   * Támogatási hozzáférés engedélyezése vagy letiltása
   * Frissítések alkalmazása
   * Gyorsjavítások telepítése

     > [!NOTE]
     > Ez az előnyben részesített lehetőség, ha elfelejtette az eszköz rendszergazdai jelszavát, és nem tud kapcsolatot létesíteni az 1. vagy a 2. lehetőséggel.

4. **Nyelv módosítása**
   Ez a beállítás lehetővé teszi a megjelenítési nyelv módosítását a Windows PowerShell felületén. A támogatott nyelvek: angol, Japán, Orosz, francia, Dél-koreai, spanyol, olasz, német, kínai és portugál.

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Távoli kapcsolódás a StorSimple-hez Windows PowerShell StorSimple-bővítménye használatával

A StorSimple-eszközhöz való kapcsolódáshoz a Windows PowerShell-távelérést használhatja. Ha így tesz, a menü nem jelenik meg. (Csak akkor jelenik meg a menü, ha a soros konzolt használja az eszközön a kapcsolódáshoz. A csatlakozás távolról közvetlenül a soros konzolon elérhető "1. lehetőség – teljes hozzáférés" megfelelője.) A Windows PowerShell távelérési szolgáltatásával egy adott RunSpace csatlakozhat. Megadhatja a megjelenítési nyelvet is.

A megjelenítési nyelv független a soros konzol menüjének **nyelv módosítása** lehetőségével beállított nyelvtől. A távoli PowerShell automatikusan felveszi annak az eszköznek a területi beállítását, amelyhez csatlakozik, ha nincs megadva.

> [!NOTE]
> Ha Microsoft Azure Virtual hosts és StorSimple Cloud Appliances eszközt használ, a Windows PowerShell-távelérést és a virtuális gazdagépet használhatja a felhőalapú berendezéshez való kapcsolódáshoz. Ha olyan megosztási helyet állított be a gazdagépen, amelyen az adatokat a Windows PowerShell-munkamenetből szeretné menteni, vegye figyelembe, hogy a _mindenki_ csak hitelesített felhasználókat tartalmaz. Ezért ha úgy állította be a megosztást, hogy _mindenki_ hozzáférhessen, és a hitelesítő adatok megadása nélkül csatlakozik, a rendszer a nem hitelesített névtelen rendszerbiztonsági tag lesz használatban, és hibaüzenetet fog látni. A probléma megoldásához a megosztási gazdagépen engedélyeznie kell a vendég fiókot, majd a vendég fióknak teljes hozzáférést kell adnia a megosztáshoz, vagy érvényes hitelesítő adatokat kell megadnia a Windows PowerShell-parancsmaggal együtt.


HTTP-vagy HTTPS-kapcsolaton keresztül csatlakozhat a Windows PowerShell távelérési szolgáltatásához. Használja az alábbi oktatóanyagok utasításait:

* [Távoli kapcsolat HTTP használatával](storsimple-8000-remote-connect.md#connect-through-http)
* [Távoli kapcsolat HTTPS használatával](storsimple-8000-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>A kapcsolatok biztonsági szempontjai

Ha úgy dönt, hogyan csatlakozhat Windows PowerShell StorSimple-bővítményehoz, vegye figyelembe a következő tényezőket:

* Az eszköz soros konzoljának közvetlen csatlakoztatása biztonságos, de a soros konzol hálózati kapcsolókon keresztül történő csatlakoztatása nem. A hálózati kapcsolókhoz való kapcsolódáskor legyen óvatos a biztonsági kockázat.
* A HTTP-munkameneten keresztüli csatlakozás nagyobb biztonságot nyújthat, mint a soros konzol hálózaton keresztüli csatlakoztatása. Bár a HTTP-munkamenet nem a legbiztonságosabb kapcsolati módszer, a megbízható hálózatokon elfogadható.
* A HTTPS-munkameneten keresztüli csatlakozás a legbiztonságosabb és ajánlott lehetőség.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>A StorSimple-eszköz felügyelete a Windows PowerShell StorSimple-bővítménye használatával

A következő táblázat a StorSimple-eszköz Windows PowerShell-felületén végrehajtható általános felügyeleti feladatok és összetett munkafolyamatok összegzését tartalmazza. Az egyes munkafolyamatokkal kapcsolatos további információkért kattintson a megfelelő bejegyzésre a táblázatban.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Munkafolyamatok Windows PowerShell StorSimple-bővítménye

| Ha ezt szeretné tenni... | Használja ezt az eljárást. |
| --- | --- |
| Eszköz regisztrálása |[Az eszköz konfigurálása és regisztrálása a Windows PowerShell StorSimple-bővítménye használatával](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| A webproxy konfigurálása</br>Webproxy beállításainak megtekintése |[Webproxy konfigurálása a StorSimple-eszközhöz](storsimple-8000-configure-web-proxy.md) |
| Adat0 hálózati adapter beállításainak módosítása az eszközön |[A StorSimple-eszközhöz tartozó adat0 hálózati adapter módosítása](storsimple-8000-modify-data-0.md) |
| Vezérlő leállítása </br> Vezérlő újraindítása vagy leállítása </br> Eszköz leállítása</br>Az eszköz visszaállítása a gyári alapértelmezett beállításokra |[Eszközök vezérlőinek kezelése](storsimple-8000-manage-device-controller.md) |
| Karbantartási mód frissítéseinek és gyorsjavításának telepítése |[Az eszköz frissítése](storsimple-update-device.md) |
| Karbantartási mód megadása </br>Kilépés a karbantartási módból |[StorSimple-eszközök módjai](storsimple-8000-device-modes.md) |
| Támogatási csomag létrehozása</br>Támogatási csomag visszafejtése és szerkesztése |[Támogatási csomag létrehozása és kezelése](storsimple-8000-create-manage-support-package.md) |
| Támogatási munkamenet elindítása</br> |[Támogatási munkamenet elindítása Windows PowerShell StorSimple-bővítménye](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Segítség kérése Windows PowerShell StorSimple-bővítménye

Windows PowerShell StorSimple-bővítménye a parancsmag súgója érhető el. A Súgó egy online, naprakész verziója is elérhető, amely segítségével frissítheti a rendszeren található súgót.

A felület súgójának beszerzése hasonló a Windows PowerShell súgójának beszerzéséhez, és a legtöbb súgóval kapcsolatos parancsmag működni fog. Súgó a Windows PowerShell online-hoz: [Microsoft. PowerShell. Core](/powershell/module/Microsoft.PowerShell.Core/).

<!--The following is a brief description of the types of Help for this Windows PowerShell interface, including how to update the Help. - OK to remove? Transition not needed.-->

### <a name="to-get-help-for-a-cmdlet"></a>A parancsmag súgójának beszerzése

* Ha bármilyen parancsmaghoz vagy függvényhez szeretne segítséget kérni, használja a következő parancsot: `Get-Help <cmdlet-name>`
* Bármely parancsmag online súgójának beszerzéséhez használja az előző parancsmagot a következő `-Online` paraméterrel: `Get-Help <cmdlet-name> -Online`
* A teljes súgóhoz használhatja a `–Full` paramétert, és példákat is használhat a `–Examples` paraméterrel.

### <a name="to-update-help"></a>A Súgó frissítése

A súgót egyszerűen frissítheti a Windows PowerShell felületén. A következő lépések végrehajtásával frissítheti a rendszer súgóját.

#### <a name="to-update-cmdlet-help"></a>A parancsmag súgójának frissítése
1. Indítsa el a Windows PowerShellt a **Futtatás rendszergazdaként** beállítással.
2. A parancssorba írja be a következőt:  `Update-Help`
3. A frissített súgófájlok lesznek telepítve.
4. A súgófájlok telepítését követően írja be a következőt: `Get-Help Get-Command` a (z) azon parancsmagok listájának megjelenítése, amelyekhez a Súgó elérhető.

> [!NOTE]
> A RunSpace elérhető parancsmagok listájának lekéréséhez jelentkezzen be a megfelelő menüpontba, és futtassa a `Get-Command` parancsmagot.


## <a name="next-steps"></a>Következő lépések

Ha a fenti munkafolyamatok egyikének végrehajtása során problémákba ütközik a StorSimple-eszközzel, tekintse meg az [eszközök a StorSimple-telepítésekkel kapcsolatos hibaelhárítást ismertető témakört](storsimple-8000-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).