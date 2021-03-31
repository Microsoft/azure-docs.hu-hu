---
title: Azure Media Player changelog
description: Azure Media Player changelog.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 09/23/2020
ms.openlocfilehash: c63a8efc4bbcf6e5a124d439e6f9f91397e2fa53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92370576"
---
# <a name="changelog"></a>Változásnapló

## <a name="236-official-update-september-21-2020"></a>2.3.6 (hivatalos frissítés, 21 2020. szeptember)

### <a name="features-236"></a>Szolgáltatások 2.3.6

A azureHtml5JS Tech (DASH) támogatása a hangvezérelt támogatáshoz – az élő átírások támogatásának késői megkezdése

### <a name="bug-fixes-236"></a>Hibajavítások 2.3.6

Ha az Apple-eszközökön a "playsinline" HLS-lejátszást használja, az "élő" gombra kattintva a videó újraindítja az AMP plakát képét, esetenként kivételt okoz a Volume gomb hiánya, amikor a HLS FairPlay [kisegítő lehetőség] a billentyűzet használatakor nincs meghatározva a gombokhoz. 1 a folyamatjelzőhöz [kisegítő lehetőségek] a billentyűzet fókusza időnként nem tér vissza a videó minősége gomb [kisegítő lehetőségek] vezérlői nem láthatók a videón képernyő, amely megakadályozza a Narrátor megkeresését

### <a name="changes-236"></a>Változások 2.3.6

Értelmes kézbesítési hibák visszaadása az alkalmazások meghívásához

## <a name="235-official-update-june-1-2020"></a>2.3.5 (hivatalos frissítés, 1 2020. június)

### <a name="bug-fixes-235"></a>Hibajavítások 2.3.5

- Hozzáférhetőség Az ESC billentyű-figyelő a beállítások panelen dokumentumhoz van csatolva
- Hozzáférhetőség Annak megakadályozása, hogy a lejátszó felhasználói felülete ne jelenjen meg, ha a vezérlő sáv vagy a beállítások menü fókuszt tartalmaz
- A vezérlő sáv nem megfelelő időpontot jelenít meg, ha a falióra-idő megjelenítési beállításai engedélyezve vannak

### <a name="changes-235"></a>Módosítások 2.3.5

- Hozzáadott hibaüzenet a hibakód 0x00400005 és dokumentálva

## <a name="234-official-update-march-4-2020"></a>2.3.4 (hivatalos frissítés, 4 2020. március)

### <a name="bug-fixes-234"></a>Hibajavítások 2.3.4

- Nem lehet beállítani a PlayReady overrideLicenseAcquistionUrl
- Nem lehet lejátszani egy tartalmat a megszakításokkal
- Hozzáférhetőség Az ID attribútum értékének egyedinek kell lennie a képernyőolvasó riasztásához
- Hozzáférhetőség A lezárt feliratok beállításai párbeszédpanel bezárásakor a fókusz kilépése párbeszédpanel

### <a name="changes-234"></a>A 2.3.4 módosítása

- Tartalom naplózása – a sikeres letöltés után a rendszer a visszafejtési hibák elemzését segíti a 2.3.3 (hivatalos frissítés november 12 2019)

### <a name="features-234"></a>A 2.3.4-es funkciók

- A videó lefedésének és a vezérlőelem sávján látható falióra-idő megjelenítésének támogatása

### <a name="bug-fixes-234"></a>Hibajavítások 2.3.4

- A hangkövetési kapcsoló működik, de a IE11 és a Windows7 objektum kimenete nem támogatja a (z) "enabled" tulajdonságot vagy metódust.
- A hangsávok kapcsolója meghiúsul, ha a puffer teljesen betöltődött
- A hangkövetési kapcsoló meghiúsul, ha a felhasználó gyorsan szünetelteti a videó-és váltást a hangsávok között
- Hozzáférhetőség Nem definiált elemleírások videó vezérlőelemhez a videolejátszó alatt
- Hiányzó hangerő-szabályozó gombok a Html5-ből attól függően, hogy a rendszer mikor fogadta a loadstart
- Hozzáférhetőség Nem lehet beállítani a plakáthoz tartozó kép helyettesítő szövegét
- Hozzáférhetőség Az alkalmazás fókusza elvész, miután kiválasztja a "kész" lehetőséget a képfeliratok beállításai párbeszédpanelen
- Hozzáférhetőség Helytelen ARIA-attribútumok vannak definiálva a "videó" elemhez a "szegmensek előzetese" alatt

### <a name="changes-234"></a>A 2.3.4 módosítása

- Üres felirat/műsorszám eltávolítva az iOS-és macOS-Safari-HLS lejátszásakor
- A IMSC1-feliratok 412s számának csökkentése
- Kimeneti figyelmeztetés a konzolon 10 egymást követő üres IMSC1 – az élő hibakeresést segítő feliratokra adott válaszok

## <a name="232-official-update-october-9-2019"></a>2.3.2 (hivatalos frissítés, október 9 2019)

### <a name="features-232"></a>A 2.3.2 funkció

-PlayReady-támogatás hozzáadva a Chromium Edge böngészőhöz készült DASH lejátszáshoz

### <a name="bug-fixes-232"></a>Hibajavítások 2.3.2

- Az aktuális lejátszási sebesség a lejátszási sebesség menüben nem jelenik meg, kivéve, ha a felhasználó manuálisan állítja be
- Hozzáférhetőség A "Settings" (beállítások) panel nincs összecsukva az "ESC" kulccsal
- Hozzáférhetőség A (z) "AMP" billentyűparancs nem működik, ha a Narrátor be van kapcsolva

### <a name="changes-232"></a>A 2.3.2 módosítása

- Az E-AC3 hangkodeket nem támogató böngészők esetén az E-AC3 hangsávok rejtettek a hangkövetési menüben
- Az E-AC3 hangkodeket támogató böngészők esetén az E-AC3 hangsáv alapértelmezés szerint ki van választva
- A hangkodekek váltását nem támogató böngészők esetén a hangsávokat a kijelölt vágányon lévő különböző kodekekkel együtt elrejtjük

## <a name="231-official-update-august-12-2019"></a>2.3.1 (hivatalos frissítés, 12 2019. augusztus)

### <a name="features-231"></a>Szolgáltatások 2.3.1

- Egy esemény jelzése, ha a emsg-mezők beérkeznek a DASH lejátszási támogatásával, hogy megjelenjenek az EK-3 hangsávok az EC-3-et támogató böngészők hang menüjében, és lehetővé teszik az AAC-EC3 való váltást

### <a name="bug-fixes-231"></a>Hibajavítások 2.3.1

- A hangsáv menüje sérült az EC-3 zeneszámok eltávolítása után
- Az aktuális idő nagy lehet, mint a videó időtartama
- A lejátszási sebesség initialSpeed-n keresztüli beállítása nem működik
- Előfordulhat, hogy egy keresés után a lejátszó beragadt
- Az Edge és az IE egy érintőképernyőn az oldalra nagyítás után a seekbar megnyomásával vagy fölé húzva nem pontosan a videó helyes szegmense jelenik meg.
- Hozzáférhetőség A Play/Pause ARIA-címkéje nem tartalmaz leírót a video Player Térkép élő szegmense számára, amely nem található a megfelelő amp hiba miatti hiba
- Hozzáférhetőség A lejátszáshoz/szüneteltetéshez használt ARIA-szerepköröknek érvényes értékeknek kell megfelelniük (. VJ-Text-Track-Display)
- Hozzáférhetőség Bizonyos ARIA-szerepköröket bizonyos szülőknek kell tartalmazniuk
- Hozzáférhetőség A videolejátszó IMSC1 feliratának lejátszási/szüneteltetési gombja nem definiált elemleírást az aktuális video/audio pufferben való keresés után.

### <a name="changes-231"></a>Változások 2.3.1

- Amikor beolvas egy segmentDecryptError, és a lejátszó már az élő szélén van, a lejátszó most frissíti a jegyzékfájlt a következő szegmens kipróbálása helyett
- További naplózás hozzáadása a diagnosztika szolgáltatáshoz
- Frissített dokumentáció, amely tartalmazza az iOS Safari FairPlay-támogatását
- Példa hozzáadva a "srclang" IMSC1-beállításhoz
- Hozzáadott kitöltés, textPadding, boxShadow felülbírálások a szöveges sávokhoz.
- Egy errorcode (0x0020025B) hozzáadásával megkülönböztetheti, hogy a szegmens letöltése nem sikerült, mert nem csak dobta a 0x00200259

## <a name="230-official-release-april-30-2019"></a>2.3.0 (hivatalos kiadás, 30 2019. április)

### <a name="features-230"></a>Funkciók 2.3.0

- A DASH IMSC1-feliratok támogatása hozzáadva
- Csak videós eszközök támogatása a KÖTŐJELhez
- API-presentationTimeOffsetInSec hozzáadva

### <a name="bug-fixes-230"></a>Hibajavítások 2.3.0

- Az AMP LowLatency heurisztikus profilja ütközik az iOS-videók "Mute" és "unmute" lejátszásával bizonyos nyelvekhez nem megfelelő fordítás
- A folyamatjelző csúszka csúszkájának ARIA-valuenow értéke néha helytelen
- A szöveges nyomkövetési képernyő ARIA-szerepkörének értéke helytelen

### <a name="changes-230"></a>2.3.0 módosítása

- A naplók mostantól a letöltött adathordozó-töredékek méretét is tartalmazzák
- IE 9 és IE 10 támogatása eltávolítva
- Frissített CEA708-minta a balra igazított feliratok megjelenítéséhez
- MediaError. üzenet belefoglalása a naplókba lejátszási hibák esetén

## <a name="224-official-update-february-22-2019"></a>2.2.4 (hivatalos frissítés, február 22 2019) ##

### <a name="bug-fixes-224"></a>Hibákra vonatkozó, 2.2.4-es hibajavítás ###

- [Hibajavítás] AMP Hozzáférhetőség Eltávolított egy elérhető fantom fület a hiba képernyő megjelenésekor
- [Hibajavítás] AMP A IE11 és a Edge kijavította a hotkey-t
- [Hibajavítás] AMP Rögzített kivétel a CEA708-feliratok számára
- [Hibajavítás] AMP Rögzített videó rögzítése az Edge böngésző számára

### <a name="changes-224"></a>Módosítások 2.2.4 ###

- Módosítása AMP Ha egy töredék-visszafejtési hiba történik, a lejátszó újrapróbálkozik a jelenlegi és a különböző töredékekkel a lejátszás helyreállításához.
- Módosítása AMP Az egymást átfedő videó-vagy hangtöredékek nagyobb toleráns

## <a name="223-official-update-january-9-2019"></a>2.2.3 (hivatalos frissítés, január 9 2019) ##

### <a name="features-223"></a>A 2.2.3 funkció ###

- Vonás HLS A hangkövetési menü hozzáadva a Safari HLS lejátszásához

### <a name="bug-fixes-223"></a>Hibajavítások 2.2.3 ###

- [Hibajavítás] AMP Hozzáférhetőség Az élő közvetítés lejátszásakor az "élő" gomb nem választható ki a billentyűzet használatával
- [Hibajavítás] AMP Kijavított hamis pozitív 0x0400003 hibák a sikertelen MSE-teszt miatt
- [Hibajavítás] AMP Kijavítva a probléma, hogy a videó lefagyhat az élő stream indításakor

### <a name="changes-223"></a>Módosítások 2.2.3 ###

- Módosítása AMP További információ a naplóban a jobb diagnosztika érdekében
- Módosítása AMP Ha több bitráta is elérhető ugyanazon a képernyőfelbontáson, az összes bitráta elérhető a kijelöléshez

## <a name="222-official-update"></a>2.2.2 (hivatalos frissítés) ##

### <a name="bug-fixes-222"></a>Hibajavítások 2.2.2 ###

- [Hibajavítás] AMP Ha a lejátszó átmeneti hálózati kimaradást észlel, a lejátszás azonnal leáll
- [Hibajavítás] AMP Hozzáférhetőség A hiba párbeszédpanel nem érhető el a billentyűzeten
- [Hibajavítás] AMP A végtelen tárcsa akkor jelenik meg, ha a nem támogatott hiba helyett hangalapú eszközt játszik

### <a name="changes-222"></a>Módosítások 2.2.2 ###

- Módosítása [AMP] honosított karakterláncok hozzáadása a hirdetmény felhasználói felületéhez

## <a name="221-official-update"></a>2.2.1 (hivatalos frissítés) ##

### <a name="features-221"></a>Szolgáltatások 2.2.1 ###

- Vonás CMAF A HLS-CMAF támogatása

### <a name="bug-fixes"></a>Hibajavítások ###

- [Hibajavítás] [AMP] nem törölt időzítők az újrapróbálkozási logikában, amely lejátszási hibákat eredményez
- [Hibajavítás] AMP A (z) [Firefox] véget ért esemény nem fut a Firefox és a Chrome alkalmazásban az élő program leállításakor
- [Hibajavítás] AMP A setsource után megjelenő vezérlőelemek akkor is, ha a vezérlők hamis értékre vannak állítva a lejátszó beállításainál

### <a name="changes"></a>Módosítások ###

- Módosítása [Élő felirat] A 608 és 708 közötti CEA-feliratok API-neve módosítva. További információ: CEA708- [feliratok beállításai](/javascript/api/azuremediaplayer/amp.player.cea708captionssettings)-->

## <a name="220-official-release"></a>2.2.0 (hivatalos kiadás) ##

### <a name="features-220"></a>Szolgáltatások 2.2.0 ###

- Vonás [Azurehtml5JS] Flash [LiveCaptions] CEA 708 feliratozási támogatás a Azurehtml5JS-ben és a Flash Tech-ben a tiszta és az AES-tartalomhoz.

### <a name="bug-fixes-220"></a>Hibajavítási 2.2.0 ###

- [Hibajavítás] A flash-verzió észlelése nem működik a Chrome-ban vagy az Edge-ben

### <a name="changes-220"></a>Változások a 2.2.0-ban ###

- Módosítása AMP Heurisztika A heurisztikus profil nevének megváltozása a gyors útmutatóból a LowLatency
- Módosítása Flash A Flash Player verzió észlelésének megváltoztatásával engedélyezheti az AES-tartalmak lejátszását az új Adobe Flash-frissítéssel.

## <a name="219-official-hotfix"></a>2.1.9 (hivatalos gyorsjavítás) ##

### <a name="bug-fixes-219"></a>Hibajavítások 2.1.9 ###

- [Hibajavítás] Live Kivétel történt, ha élő streamek áttérnek az igény szerinti videóra/élő archívumra

### <a name="changes-219"></a>2.1.9 módosítása ###

- Módosítása Flash AES A Flash műszaki logikát úgy módosította, hogy ne használja a sharedbytearrays for AES-visszafejtést, mivel az Adobe letiltotta a használatot a Flash 30 használatával. Vegye figyelembe, hogy a lejátszás csak akkor működik, ha az Adobe a V30 hibája miatt új flash-verziót helyez üzembe. További részletekért tekintse meg az [ismert problémákat](azure-media-player-known-issues.md)

## <a name="218-official-update"></a>2.1.8 (hivatalos frissítés) ##

### <a name="bug-fixes-218"></a>Hibajavítások 2.1.8 ###

- [Hibajavítás] A Spinner alkalmanként nem jeleníti meg a post-és a lejátszási keresést
- [Hibajavítás] A lejátszó nem indul el elnémul, ha engedélyezve van az elnémítva beállítás
- [Hibajavítás] Ha a vezérlők hamis értékre vannak állítva, a Volume csúszka jelenik meg
- [Hibajavítás] Lejátszás időnként megismételve, amikor a felhasználó kihagyja az élő szegélyt
- [Hibajavítás] Firefox A lejátszó időnként JavaScript-kivételt dob a terhelésnél
- [Hibajavítás] Hozzáférhetőség Lejátszás/szüneteltetés/hangerő gomb elveszíti a fókuszt, ha a billentyűzet vezérlőelemekkel van kiválasztva
- [Hibajavítás] A lejátszón lévő rögzített memória-szivárgás el van távolítva
- [Hibajavítás] Az src () hívása, miután a lejátszó hibái nem állítják vissza a forrást
- [Hibajavítás] Live Az AMP állandó betöltési állapotban van, ha a felhasználó az élő gombra kattint a szórás befejezése után
- [Hibajavítás] Chrome A lejátszó leáll, és a lejátszás meghiúsul, ha a böngésző a háttérben kis méretre van lebontva.

### <a name="changes-218"></a>2.1.8 módosítása ###

- Módosítása Frissített 0x0600001-hiba, amely akkor jelenik meg, ha az AES-tartalmat a Flash 30-as verziójával együtt játssza le, mert jelenleg nem támogatott. További részletekért tekintse meg az [ismert problémákat](azure-media-player-known-issues.md)
- Módosítása További újrapróbálkozások lettek hozzáadva élő forgatókönyvekhez, amikor a 404-es jegyzékfájl-kérés vagy üres jegyzékfájlt ad vissza.

## <a name="217-official-update"></a>2.1.7 (hivatalos frissítés) ##

### <a name="features-217"></a>Funkciók 2.1.7 ###

- Vonás [AzureHtml5JS] Konfigurációs beállítás hozzáadva az elavult adatforrások pufferének kiürítéséhez

### <a name="bug-fixes-217"></a>Hibajavítások 2.1.7 ###

- [Hibajavítás] Hozzáférhetőség [Képernyőolvasó] Az üres fejléc el lett távolítva, ha a cím nincs beállítva.
- [Hibajavítás] UWA Az AMP kidobja a kivételt az univerzális Windows-alkalmazásban való lejátszáskor
- [Hibajavítás] [OSX] setActiveTextTrack () nem működik a Safariban az OSx-ben
- [Hibajavítás] Live Kattintás az élő szélre a lejátszó hozamának ártalmatlanítása és újrainicializálása után
- [Hibajavítás] Bőr Bizonyos adategységek aktuális ideje csonkítva
- [Hibajavítás] [DRM] javítás a több CENC DRM-t támogató böngészőkben való lejátszás támogatásához

### <a name="changes-217"></a>2.1.7 módosítása ###

- Módosítása Minták Hozzáférhetőség Nyelvi címke hozzáadva az összes mintához

## <a name="216-official-update"></a>2.1.6 (hivatalos frissítés) ##

### <a name="bug-fixes-216"></a>Hibajavítások 2.1.6 ###

- [Hibajavítás] AMP helytelen időtartam megjelenítése adott eszköz esetében
- [Hibajavítás] [FairPlay-HLS] Fairplay hibák nem terjednek át a felhasználói felületre
- [Hibajavítás] Egyéni heurisztikus tulajdonságok figyelmen kívül hagyása az AMP 2.1.5-ben.

### <a name="changes-216"></a>2.1.6 módosítása ###

- Módosítása [FairPlayDRM] A PlayReady-és Widevine-implementációk paritásának megőrzése érdekében a rendszer eltávolította a FairPlay-re vonatkozó tanúsítvány-és tanúsítványkérelem időkorlátját.
- Módosítása Egyéb heurisztikus változások a homályos tartalom elleni küzdelemben

### <a name="features-216"></a>Funkciók 2.1.6 ###

- Vonás Támogatás: mpd-Time-CMAF formátum hozzáadva

## <a name="215-official-hotfix"></a>2.1.5 (hivatalos gyorsjavítás) ##

### <a name="bug-fixes-215"></a>Hibajavítások 2.1.5 ###

- [Hibajavítás] Feliratok A lejátszó nem megfelelően jeleníti meg a VTT stílusát
- [Hibajavítás] Hozzáférhetőség Az élő gomb nem rendelkezik ARIA-címkével

## <a name="214-official-update"></a>2.1.4 (hivatalos frissítés) ##

### <a name="bug-fixes-214"></a>Hibajavítások 2.1.4 ###

- [Hibajavítás] Hozzáférhetőség Fókusz A felhasználók nem láthatják a vezérlő sáv teljes képernyő gombjának jobb oldalán felvett egyéni gombokat.
- [Hibajavítás] [IE11] [Kötet sáv] A többoldalas felugró ablak a teljes képernyős képernyőt a teljes képernyős módban IE11 teszi
- [Hibajavítás] [Skin | Kiürítés] a vezérlő sáv és a kötet sáv előugró ablaka között megjelenő terület
- [Hibajavítás] AMP Feliratok A régi beágyazott számok nem törlődnek, ha a forrás módosul egy meglévő lejátszón
- [Hibajavítás] Hozzáférhetőség Narrátor A képernyőolvasó helytelenül olvassa be a kötet vezérlőelemet
- [Hibajavítás] [Flash] A lejátszási esemény alkalmanként nem indul el a Flash techből
- [Hibajavítás] AMP Fókusz A Play/Pause művelethez két kattintás szükséges, ha a lejátszó fókuszban van, és teljes képernyős módban van
- [Hibajavítás] AMP Bőr Helytelen időtartam jelenik meg egy adott eszköz folyamatjelző sávján
- [Hibajavítás] Hirdetések [Ad komornyik] A nagy elemző nem kezel olyan nagy fájlt, amely nem rendelkezik előrehaladási eseménnyel
- [Hibajavítás] Sdn [AMP 2.1.1] A kaptár SDN beépülő modul támogatásának hibája kijavítva
- [Hibajavítás] Hozzáférhetőség A Narrátor beolvassa a "Midnight Mute" gombot, ha a felhasználó a hangerő gombra koncentrál

### <a name="changes-214"></a>Módosítások 2.1.4 ###

- Módosítása Hozzáférhetőség [Kisegítő technológia] A gombok mostantól ARIA-Live tulajdonsággal javítják a kisegítő technológiákkal kapcsolatos tapasztalatokat
- Módosítása Hozzáférhetőség [Hangerő gomb | Narrátor] a hangerő-ellenőrzés jobb hozzáférhetősége a tabbing funkció és a csúszka működésének módosításával. Ezek a változtatások megkönnyítik a billentyűzet felhasználói számára a lejátszó kötetének módosítását
- Módosítása Megnövekedett inaktivitási helyi menü időtúllépése 3 és 5 másodperc között
- Módosítása Hozzáférhetőség Fényesség Továbbfejlesztett Fényerő-kontraszt arány a feliratok beállításaiban a legördülő menükben

## <a name="213-official-update"></a>2.1.3 (hivatalos frissítés) ##

### <a name="bug-fixes-213"></a>Hibajavítások 2.1.3 ###

- [Hibajavítás] [Plugins | Cím átfedésben] az átfedésben lévő beépülő modul a AMP v2-mel rendelkező JS-kivételeket veti fel. X +
- [Hibajavítás] A verziókövetés esemény a JavaScript-konzolra kerül, még akkor is, ha a naplózás ki van kapcsolva
- [Hibajavítás] Bőr A játékosi idő tippek a lejátszó kontextusán kívül jelennek meg, amikor a végponti időtartam sáv fölé mutat
- [Hibajavítás] Hozzáférhetőség [Képernyőolvasó] A Narrátor beolvassa a "régió Landmark" vagy a "videolejátszó régiójának nevezetessége" kifejezést, ha a megjelenítőn a Playerre fókuszál
- [Hibajavítás] AMP A lejátszó körvonala nem tiltható le CSS-n keresztül
- [Hibajavítás] Hozzáférhetőség Nem lehet tabulátort a teljes lejátszóra összpontosítani, ha a felhasználó teljes képernyős módban van
- [Hibajavítás] Bőr Live A bőr nem válaszol a honosított élő szövegre japán nyelven
- [Hibajavítás] Bőr Az időtartam és az aktuális idő kivágása a stream > 60 min-[hibajavítás] [iPhone | Élő] a lejátszó megjeleníti a vezérlőelem sávján az aktuális idő/időtartam szövegét
- [Hibajavítás] AMP A Player heurisztikus API-k hívása JavaScript-kivételeket eredményez
- [Hibajavítás] [Natív Html5 | iOS] A "playsinline" Videotag tulajdonság nem propagálható a lejátszóra
- [Hibajavítás] [iOS | iframe] A lejátszó nem tud teljes képernyős értéket megadni az iPhone-on, ha a lejátszó iframe-ben van betöltve
- [Hibajavítás] AMP Heurisztika Az AMP mindig a hibrid profillal működik, függetlenül a lejátszó lehetőségeitől
- [Hibajavítás] [AMP | Win 8.1] dob, ha a Win 8.1 alkalmazásban webnézettel rendelkezik

### <a name="changes-213"></a>Változások 2.1.3 ###

- Módosítása AMP CDN-végpont információinak hozzáadása a FragmentDownloadComplete-eseményben
- Módosítása AMP Live Továbbfejlesztett és optimalizált élő adatfolyam-késés

## <a name="212-official-hotfix"></a>2.1.2 (hivatalos gyorsjavítás) ##

### <a name="bug-fixes-212"></a>Hibajavítások 2.1.2 ####

- [Hibajavítás] Hozzáférhetőség [Windows-Narrátor] A Narrátor a "Progress Midnight" szöveget olvassa be, ha a felhasználó a folyamatjelző sáv és az aktuális idő tekintetében 0:00
- [Hibajavítás] [Skin] a logo méretének kódolása JavaScript-kódban
- Hozzáférhetőség Billentyűparancsok Ha a lejátszóra kattint, a gyorsbillentyűk nem engedélyezettek.

### <a name="changes-212"></a>Módosítások 2.1.2 ####

- Módosítása Naplózás Napló jegyzékfájljának URL-címe, ha a lejátszó nem tudja betölteni a jegyzékfájlt

### <a name="features-212"></a>Szolgáltatások 2.1.2 ###

- Módosítása Teljesítmény Optimalizálás Továbbfejlesztett lejátszó-betöltési és indítási idő

## <a name="211-official-update"></a>2.1.1 (hivatalos frissítés) ##

### <a name="bug-fixes-211"></a>Hibajavítások 2.1.1 ####

- [Hibajavítás] iOS Az automatikus lejátszás beállítása hamis értékre a Safariban iOS-hez készült végtelen Spinner
- [Hibajavítás] A tartalom időtartamát meghaladó időpontra való törekvés végtelen Spinner-t eredményez
- [Hibajavítás] A gyorsbillentyűk több billentyűparancsot igényelnek a lejátszó környezetének eléréséhez
- [Hibajavítás] A videó bizonyos eszközökön való átméretezése után néhány másodpercig lefagy
- [Hibajavítás] Végtelen Spinner (a keresés befejezése után), ha a felhasználó több gyors keresést hajt végre
- [Hibajavítás] A vezérlő sáv nem rejtett inaktivitás közben
- [Hibajavítás] Ha egy olyan webappot nyit meg, amely az AMP-t futtatja, akkor a weblap kétszer is betöltődik
- [Hibajavítás] Végtelen, miközben bizonyos adategységeket a Flash Tech használatával játszik
- [Hibajavítás] A további beállítások menü nem jelenik meg a harmadik féltől származó beépülő moduloknál
- [Hibajavítás] [Skin | Tube] [élő] két élő ikon jelenik meg, ha a lejátszó a program élő szélén van
- [Hibajavítás] Bőr Az embléma nem tiltható le
- [Hibajavítás] [DD + tartalom] A folyamatos tárcsa a Dolby Digital hangsávot tartalmazó eszközöket jeleníti meg
- [Hibajavítás] A legújabb AMP lefagy a hangnyelvi számok a livestream-ban való váltásakor
- [Hibajavítás] rögzített háttérbeli megszűnés a léptető esetében
- [Hibajavítás] Végtelen tárcsa az AES Flash-jogkivonat statikus mintáinak hibajavításai

### <a name="changes-211"></a>Változások 2.1.1 ####

- Módosítása A Widevine HTTPS-követelményhez hozzáadott hibakód: a Chrome-v58 a Widevine-tartalmat be kell tölteni/lejátszani a `https://` protokollon keresztül, máskülönben a lejátszás sikertelen lesz.
- Módosítása Hozzáadott ARIA-címke a Spinner betöltéséhez, így a kisegítő technológia a "videó betöltését" is elmesélheti a tartalom betöltésekor  

## <a name="210-official-release"></a>2.1.0 (hivatalos kiadás) ##

### <a name="features-210"></a>Funkciók 2.1.0 ###

- Vonás [AzureHtml5JS] VOD ad-támogatás a pre-Mid-post-Rolls szolgáltatáshoz
- Vonás Béta [AzureHtml5JS] Élő ad-támogatás a pre-Mid-post-Rolls szolgáltatáshoz
- Vonás Új skin-beállítás hozzáadva – AMP-flush
- Vonás Továbbfejlesztett ARIA-címkék hozzáadva a képernyőolvasók és a kisegítő technológiák jobb integrálásához
- Vonás Bőr A Skin mostantól jól mutatja az összes ikont és gombot kontrasztos módban

### <a name="bug-fixes-210"></a>Hibajavítások 2.1.0 ###

- [Hibajavítás] A kisegítő lehetőségek száma és a felhasználói felület javításai
- [Hibajavítás] Az AMP nem töltődik be megfelelően a IE9

### <a name="changes-210"></a>2.1.0 módosítása ###

- Módosítása DOM-elemek átstrukturálása a lejátszóban a hirdetések működéséhez
- Módosítása A CSS-ből a SCSS-re váltott
- Módosítása Minták Példa a VOD-hirdetések hozzáadására
- Módosítása Minták A lejátszási sebességhez hozzáadott minta
- Módosítása Minták Minta hozzáadva a kiürítési bőrre

## <a name="200-beta-release"></a>2.0.0 (bétaverzió) ##

- [Change] frissítve a VJS5
- vonás Új Fluid API-t adott meg a lejátszó rugalmassága
- Vonás Lejátszás sebessége
- Módosítása A CSS-ből a SCSS for skinre váltott

## <a name="183-official-hotfix-update"></a>1.8.3 (hivatalos gyorsjavítás frissítése) ##

### <a name="bug-fixes-183"></a>Hibajavítások 1.8.3 ###

- [Hibajavítás] [AzureHtml5JS] Bizonyos, a negatív DTS-vel nem rendelkező eszközök nem fognak lejátszani a Chrome-ban

## <a name="182-official-hotfix-update"></a>1.8.2 (hivatalos gyorsjavítás frissítése) ##

### <a name="bug-fixes-182"></a>Hibajavítások 1.8.2 ###

- [Hibajavítás] [AzureHtml5JS] A nagyobb hangsebességek nem kerülnek vissza a AzureHtml5JS-on keresztül

## <a name="181-official-update"></a>1.8.1 (hivatalos frissítés) ##

### <a name="bug-fixes-181"></a>Hibajavítások 1.8.1 ###

- [Hibajavítás] iOS Nem jelennek meg a natív lejátszón lévő feliratok/feliratok
- [Hibajavítás] AMP A CDN által támogatott folyamatos átviteli URL-címek, amelyek nem játszanak hitelesítési jogkivonatokkal
- [Hibajavítás] Fairplay FairPlay hibakód: hiányzó technikai azonosító (BITS [31-28], ErrorCode) további részletekért lásd a hibakódokat.
- [Hibajavítás] Safari PlayReady PlayReady-tartalom a Safariban végtelen Spinner

### <a name="changes-181"></a>A 1.8.1 módosítása ###

- Módosítása Html5 Natív Html5-technikai részletes naplók módosítása a VideoTag eseményeinek tárolására

## <a name="180-official-update"></a>1.8.0 (hivatalos frissítés) ##

### <a name="features-180"></a>Szolgáltatások 1.8.0 ###

- Szolgáltatások DRM FairPlay-támogatás hozzáadva (további információért lásd a [védett tartalmakat](azure-media-player-protected-content.md) )

### <a name="bug-fixes-180"></a>Hibajavítások 1.8.0 ###

- [Hibajavítás] AMP A felhasználói keresés nem indít el várakozási eseményt a hálózat szabályozásakor
- [Hibajavítás] [Flash] A minőség kiválasztása a Flash Tech throws szolgáltatásban kivétel
- [Hibajavítás] AMP A minőség dinamikus kiválasztása a helyi menüben jelenik meg
- [Hibajavítás] Bőr Nehéz kiválasztani a helyi menük utolsó menüelemét

### <a name="changes-180"></a>A 1.8.0 módosítása ###

- Módosítása Frissített lejátszó a jelenlegi Chrome EME-követelményekhez
- Módosítása Az alapértelmezett techOrder módosult, hogy megfeleljenek az új tech-html5FairPlayHLS (további információért lásd a [védett tartalmakat](azure-media-player-protected-content.md) )
- Módosítása [AzureHtml5JS] Engedélyezve MPEG-Dash a lejátszás a Safariban
- Módosítása Minták Módosította a többplatformos DRM-mintákat a FairPlay befogadásához

## <a name="174-official-hotfix-update"></a>1.7.4 (hivatalos gyorsjavítás frissítése) ##

### <a name="bug-fixes-174"></a>Hibajavítások 1.7.4 ###

- [Hibajavítás] Chrome Kék körvonal jelenik meg, ha a felhasználó a lejátszó kontextusában van
- [Hibajavítás] IE9 JavaScript-kivétel történt, amikor a lejátszó betöltődött a IE9

## <a name="173-official-hotfix-update"></a>1.7.3 (hivatalos gyorsjavítások frissítése) ##

### <a name="bug-fixes-173"></a>Hibajavítások 1.7.3 ###

- [Hibajavítás] [AzureHtml5JS] A lejátszó időzítése korlátozott hálózatokon

### <a name="changes-173"></a>Módosítások 1.7.3 ###

- Módosítása Webkriptográfia engedélyezése az Edge-ben az AES-tartalom visszafejtéséhez
- Módosítása Az AMP heurisztikus elemek optimalizálása a gyorsítótárazott adattömbök esetében
- Módosítása [AzureHtml5JS] Heurisztikus optimalizálás a sávszélesség-becslés késleltetésének csökkentésével

## <a name="172-official-hotfix-update"></a>1.7.2 (hivatalos gyorsjavítás frissítése) ##

### <a name="features-172"></a>Funkciók 1.7.2 ###
<!---API needs onboarding. Removed link to API until remedied.--->
- Vonás [AzureHtml5JS | Firefox] a Widevine-lejátszás engedélyezése az EME-vel a Firefox 47-es és újabb
- Vonás Esemény hozzáadása a lejátszó ártalmatlanításához
<!-- ([disposing](index.html#static-amp.eventname.disposing)) -->

### <a name="bug-fixes-172"></a>Hibajavítások 1.7.2 ###

- [Hibajavítás] A kódolt Akamai CDN URL-lekérdezési paraméterek helytelenül vannak dekódolva
- [Hibajavítás] Kivétel történt a manifestPlayableWindowLength ()-on való kidobáskor
- [Hibajavítás] A videó nem mindig kattinthat a videó lejátszására, miután a videó véget ért
- [Hibajavítás] A rugalmas méretezés nem felel meg a gyors ablakméret változásának
- [Hibajavítás] [Edge | IE] a rugalmas méretezés nem lép érvénybe a szélesség = x, height = automatikus terhelésnél.
- [Hibajavítás] [Android | Chrome] Chrome – kérdezés a DRM-tartalmak lejátszásához, ha a tartalom nincs titkosítva
- [Hibajavítás] Hozzáférhetőség Edge A billentyűzet vezérlőelem nem megfelelően jelöli ki a helyi menüelemeket
- [Hibajavítás] Hozzáférhetőség Hiányzó megjelenő szegély magas kontrasztú módban
- [Hibajavítás] [Flash] Az egérmutató nem távolítja el az esemény-figyelőt, miután a Player kidobja a kivételt
- [Hibajavítás] [Flash] A jegyzékfájl URL-címének elemzése a kódolt szóközökkel
- [Hibajavítás] iOS Írja be a hibát a tech. featuresVolumeControl kiértékelése során

### <a name="changes-172"></a>1.7.2 módosítása ###

- Módosítása DRM Áthelyezett DRM-ellenőrzések a forrás beállítása után, hogy csak a tartalom titkosítását ellenőrizze.
- Módosítása AES Nem definiált típus/egyszerű törzs eltávolítva a Key Delivery kérelemből
- Módosítása Hozzáférhetőség A Windows Narrátor most beolvassa a "Media Player" értéket, ha a környezet a tulajdonságok helyett a lejátszón van

## <a name="171-official-hotfix-update"></a>1.7.1 (hivatalos gyorsjavítás frissítése) ##

### <a name="features-171"></a>Funkciók 1.7.1 ###

- Vonás Hozzáadott lehetőség a hibrid heurisztikus profilhoz (alapértelmezés szerint ez a profil van beállítva)

### <a name="bug-fixes-171"></a>Hibajavítások 1.7.1 ###

- [Hibajavítás] A rugalmas kialakítás nem használható HTML5-szabványként (szélesség = 100%, height = automatikus)
- [Hibajavítás] A szélesség és a magasság százalékos értékei nem a várt módon jelennek meg a v 1.7.0

## <a name="170-official-update"></a>1.7.0 (hivatalos frissítés) ##

### <a name="features-170"></a>Funkciók 1.7.0 ###
<!---API needs onboarding. Removed link until remedied.--->
- Vonás [AzureHtml5JS] [Flash] A currentMediaTime () hozzáadása az aktuális idő kódoló adathordozójának lekéréséhez másodpercek alatt
- Vonás [Flash] Implementált telemetria API-k letöltése a videoBufferData () és a audioBufferData () segítségével<!-- (see [BufferData](index.html#amp.bufferdata) for more details) -->
- Vonás [Flash] "Downloadbitratechanged" esemény hozzáadva
- Vonás Betöltési idő javult a lejátszó régebbi verzióihoz képest
- Vonás A hibák a JavaScript-konzolra vannak naplózva

### <a name="bug-fixes-170"></a>Hibajavítások 1.7.0 ###

- [Hibajavítás] Kódolt poszter-URL-cím lekérdezési karakterlánc-paraméterekkel nem jeleníthető meg a lejátszón
- [Hibajavítás] Kivétel történt, ha nincs Tech betöltve és API amp. A Player. poszter () neve
- [Hibajavítás] Kivétel történt, ha a függvények kidobás után próbálnak hozzáférni a lejátszóhoz
- [Hibajavítás] Hozzáférhetőség Hiányzó körvonal a Fókuszban a folyamatjelző sáv számára
- [Hibajavítás] Hozzáférhetőség A helyi menük árnyéka kontrasztos módban van
- [Hibajavítás] [iOS] a natív lejátszó WebVTT feliratai nem működnek
- [Hibajavítás] [AzureHtml5JS] A 0x0100002 hibaüzenetet kell megjeleníteni, ha HTTP-adatfolyamot használ a HTTPS-helyen, amely Ehelyett a végtelen Spinner-t a kevert tartalom eredményeképpen eredményezi.
- [Hibajavítás] [AzureHtml5JS] Hiányzó végpont, amely az észlelt végtelen pufferelési állapotot eredményező állapot-ellenőrzési hibát okoz
- [Hibajavítás] [AzureHtml5JS] Helytelen a hangkövetés neve a menüben, ha a useManifestForLabel = false és a három betűs nyelvi kód van használatban
- [Hibajavítás] [AzureHtml5JS | A Chrome] a tartalom végén észlelte a végtelen puffer állapotát, amely a JavaScriptben a Chrome-ban az időtartam alatt a lebegőpontos pontatlanságot okozta.
- [Hibajavítás] [Flash] Nem végzetes időszakos hiba, amely egy pillanatra jelenik meg a Flash Player létrehozásakor
- [Hibajavítás] [Flash] A lejátszás meghiúsul, ha a videó-és hangadatfolyamok eltérő időkereteket használnak a "töredék URL-cím (...) lekerekítése miatt. nem sikerült létrehozni a FLVTags "
- [Hibajavítás] [Flash] Problémák a jegyzékfájl-URL-címek és a kódolt szóközök elemzésével
- [Hibajavítás] [Flash] Hiányzik annak megállapítása, hogy a Flash Player verziója >= 11,4, amely hibát okoz a lejátszásban, ahelyett, hogy visszakerül a techOrder következő technológiára.
- [Hibajavítás] [Flash] AES Az AES-tokenek és az aláhúzások elfogadásával kapcsolatos problémák
- [Hibajavítás] [Silverlight | OSX] "//" a jegyzékfájlnak a protokoll helyett történő előjavítása (HTTP vagy HTTPS) helyi fájlként a végtelen Spinner

### <a name="changes-170"></a>1.7.0 módosítása ###

- Módosítása [Flash] Egyesített SWF-parancsfájlok ("MSAdaptiveStreamingPlugin-osmf 2.0. swf" és "StrobeMediaPlayback. 2.0. swf") egyetlen, "StrobeMediaPlayback. 2.0. swf" nevű SWF-fájlba
- Módosítása [Flash] Frissített hibakód-propagálás a pontosabb hibakódok beszerzéséhez (pl. a 404s most az általános hiba 0x30200000 helyett 0x30200194 eredményez.

## <a name="163-official-hotfix-update"></a>1.6.3 (hivatalos gyorsjavítás frissítése) ##

### <a name="bug-fixes-163"></a>Hibajavítások 1.6.3 ###

- [Hibajavítás] JavaScript futásidejű kivétel, ha a hotkeys eseménykezelő a lejátszó ártalmatlanítása után fut
- [Hibajavítás] Android [AzureHtml5JS] A mobileszköz nem használ mobil hálózatot használó lejátszást
- [Hibajavítás] Frissített hamisítás a webes feldolgozóként való futtatáshoz a felhasználói felület felszabadítása érdekében

## <a name="162-official-hotfix-update"></a>1.6.2 (hivatalos gyorsjavítás frissítése) ##

### <a name="features-162"></a>Szolgáltatások 1.6.2 ###

- Vonás További nyelvek hozzáadása a honosításhoz (további részleteket a dokumentációban talál)

### <a name="bug-fixes-162"></a>Hibajavítások 1.6.2 ###

- [Hibajavítás] [IE9-10] Kattintson a lejátszó környékén található területekre a böngészőablakban, a IE9/IE10 hiba miatt, amely a window. blur ()-ra való lekicsinyítést eredményezi.
- [Hibajavítás] [Flash] Nem fogad el AES-tokeneket az aláhúzásokkal

## <a name="161-official-hotfix-update"></a>1.6.1 (hivatalos gyorsjavítás frissítése) ##

### <a name="bug-fixes-161"></a>Hibajavítások 1.6.1 ###

- [Hibajavítás] [Villanások | Edge, IE] [Silverlights | IE] nem lehet összpontosítani más felhasználói felületi elemekre a bemenetekhez vagy az IE/Edge-ben.
- [Hibajavítás] Az AES-lejátszás meghiúsul, ha nem definiált hamis
- [Hibajavítás] Android [AzureHtml5JS | Chrome] a folyamatos tárcsa nem játssza le a tartalmat, ha állapot-ellenőrzési hurokban van
- [Hibajavítás] [IE9] Console. log () nem támogatott az IE 9 okozza a kivételt

## <a name="160-official-update"></a>1.6.0 (hivatalos frissítés) ##

### <a name="features-160"></a>Funkciók 1.6.0 ###

- [Funkció] 33%-os méret csökkentése azuremediaplayer.min.js
- Vonás [AzureHtml5JS | Edge] [nem tesztelt] a DD + audio streamek támogatása az Edge-ben (a kezdeti választás után nincs szükség a kodekre). Az alkalmazásnak most ki kell választania a megfelelő hangadatfolyamot.
- Vonás Billentyűparancs-vezérlők (további részletekért lásd: docs)
- Vonás Időponthoz tartozó tipp – pontos keresés
- Vonás Beépülő modulok aszinkron észlelésének engedélyezése, ha a beépülő modulban létezik a setupDone metódus

### <a name="bug-fixes-160"></a>Hibajavítások 1.6.0 ###

- [Hibajavítás] A getMemoryLog nem üríti ki a memória naplóját (igaz)
- [Hibajavítás] A bitráta kiválasztási mezője alaphelyzetbe állítja az egér mozgatását, ami kijelöli az alsó bitrátát az egér vezérlőelemen keresztül
- [Hibajavítás] A Mac Office az alkalmazásban összeomlik a DRM-ellenőrzések végrehajtásakor
- [Hibajavítás] A CSS-osztályok egyszerűen felülírva
- [Hibajavítás] Chrome A felhasználó-ügynök sztring böngészőből származó azonosítás frissítése Edge
- [Hibajavítás] [AzureHtml5JS] A feliratok gomb nem jelenik meg az Edge (Win10) vagy a Chrome (Mac) eszköztárán
- [Hibajavítás] Android [AzureHtml5JS | Chrome] InvalidStateError kivétel a endOfStream () hívása rövid videókon
- [Hibajavítás] Firefox A Firefox által a böngésző képességeinek ellenőrzésekor okozott DRM-figyelmeztetés eltávolítása
- [Hibajavítás] Html5 Nem jelennek meg a progresszív MP4-tartalommal rendelkező feliratok/feliratok
- [Hibajavítás] [Flash] Az egyező időbélyegzővel rendelkező üzenetek naplózása fordított sorrendben történt
- [Hibajavítás] Hozzáférhetőség [Chrome | Firefox] lapon jelölje be a vezérlők automatikus kiválasztása az első menüelemből elemet.
- [Hibajavítás] Hozzáférhetőség Lap a Volume gomb vezérléséhez

### <a name="changes-160"></a>1.6.0 módosítása ###

- Módosítása AES-visszafejtési idő használata a minőségi szint kiválasztásakor
- Módosítása URL-újraíró frissítése a HLS v4 használatához, mielőtt HLS v3-t használ a több hangos adatfolyamok számára
- Módosítása A nativeControlsForTouch beállítása false (hamis) értékre alapértelmezettként (false-nak kell lennie ahhoz, hogy megfelelően működjön)

## <a name="150-official-update"></a>1.5.0 (hivatalos frissítés) ##

### <a name="features-150"></a>Szolgáltatások 1.5.0 ###

- Vonás Az általános webes biztonság fejlesztései (az injekció, az XSS stb.)
- Vonás SDN beépülő modul integrációs horgok sourceset-eseményekhez és-beállításokhoz. Sdn
- Vonás A 5XX és a 4XX hibák robusztus kezelését a lejátszás során

### <a name="bug-fixes-150"></a>Hibajavítások 1.5.0 ###

- [Hibajavítás] A CSS-minification frissítése a HTML-entitás betűkészlet-kódjának használatára a Unicode helyett a gombokhoz
- [Hibajavítás] [AzureHtml5JS] A multi-DRM-tartalom mindig kiválasztja az első elem tokenjét a protectionInfo, ami második DRM-t okoz
- [Hibajavítás] [AzureHtml5JS] A keresés soha nem fejeződik be, amikor egy hiányzó szegmenst tartalmazó régióban keres.
- [Hibajavítás] [AzureHtml5JS | Edge] a PlayReady lejátszásának engedélyezése a peremhálózati frissítéshez
- [Hibajavítás] [AzureHtml5JS | Firefox] a Firefox V42 + (MSE) szolgáltatással való tartalékának engedélyezése a Silverlight szolgáltatáshoz a védett tartalomhoz
- [Hibajavítás] [Flash] Frissítési hiba. üzenet a számtól a részletes sztringig

### <a name="changes-150"></a>A 1.5.0 módosítása ###

- Módosítása A plakátok jelenleg csak abszolút URL-címekként működnek.

## <a name="140-official-update"></a>1.4.0 (hivatalos frissítés) ##

### <a name="features-140"></a>Funkciók 1.4.0 ###

- Vonás [AzureHtml5JS | Chrome] egyszerű Widevine DRM-támogatás
- Vonás [AzureHtml5JS] 404/412 hibák robusztus feldolgozása lejátszás közben

### <a name="bug-fixes-140"></a>Hibajavítások 1.4.0 ###

- [Hibajavítás] [Flash] Paraméterek ellenőrzésének továbbfejlesztése

## <a name="130-official-update"></a>1.3.0 (hivatalos frissítés) ##

### <a name="features-130"></a>Funkciók 1.3.0 ###

- Vonás [AzureHtml5JS] [Flash] Ugyanazt a kodeket használó többszörös hangátviteli tartalom hangváltása

### <a name="bug-fixes-130"></a>Hibajavítások 1.3.0 ###

- [Hibajavítás] [AzureHtml5JS | Chrome] időszakos végtelen tárcsa
- [Hibajavítás] [AzureHtml5JS | IE] [Windows Phone-telefon] kivétel okozza Windows Phone-telefon lejátszási problémáit
- [Hibajavítás] [Flash] További példányok esetén az automatikus lejátszás beállítása hamisra
- [Hibajavítás] A felhasználói felületi menü méretezési problémái

## <a name="120-official-update"></a>1.2.0 (hivatalos frissítés) ##

### <a name="features-120"></a>Funkciók 1.2.0 ###

- Vonás [AzureHtml5JS | Firefox] támogatás az MSE engedélyezésekor
- Vonás Már nem szükséges, hogy az alkalmazás a tartalék Tech bináris fájljainak elérési útját adja meg (SWF, xap). Az elérési út a Azure Media Player parancsfájlhoz képest relatív.

### <a name="bug-fixes-120"></a>Hibajavítások 1.2.0 ###

- [Hibajavítás] [AzureHtml5JS | Chrome] a lejátszó az élő szél mögött sodródik a háttérben lévő játékossal
- [Hibajavítás] [AzureHtml5JS | Edge] a teljes képernyő nem működik
- [Hibajavítás] [AzureHtml5JS] A naplózás nem volt megfelelően engedélyezve, ha be van állítva a beállítások között
- [Hibajavítás] Flash A várakozási esemény során a "pufferelés" és a pufferelés ikon is látható
- [Hibajavítás] A lejátszás folytatásának engedélyezése, ha a kezdeti sávszélesség-kérelem sikertelen
- [Hibajavítás] A lejátszó nem tölthető be, ha nem definiált beállításokkal van inicializálva
- [Hibajavítás] Ha a lejátszót már eldobása után próbálja meg ártalmatlanítani, vdata kivétel történik.
- [Hibajavítás] Helytelenül leképezett minőségi sáv ikonjai

## <a name="111-official-hotfix-update"></a>1.1.1 (hivatalos gyorsjavítás frissítése) ##

### <a name="bug-fixes-111"></a>Hibajavítások 1.1.1 ###

- [Hibajavítás] Régebbi IE teljes képernyős probléma
- [Hibajavítás] A beépülő modulok már nem írhatók felül

## <a name="110-official-update"></a>1.1.0 (hivatalos frissítés) ##

### <a name="features-110"></a>Szolgáltatások 1.1.0 ###

- Vonás Felhasználói felület honosítási karakterláncának frissítése

### <a name="bug-fixes-110"></a>Hibajavítások 1.1.0 ###

- [Hibajavítás] A Big Play gomb nem rendelkezik elegendő kontraszttal
- [Hibajavítás] Vizualizáció lap fókuszának mutatója
- [Hibajavítás] Válassza ki a bitráta menüt a helyes megoldási információk használatával
- [Hibajavítás] Mostantól a további beállítások menü dinamikusan méretezi
- [Hibajavítás] Különböző felhasználói felületi problémák

## <a name="100-official-release"></a>1.0.0 (hivatalos kiadás) ##

### <a name="features-100"></a>Szolgáltatások 1.0.0 ###

- Vonás A TAB Control, a Focus Control, a képernyőolvasó és a nagy kontrasztú felhasználói felület alapszintű hozzáférhetőségének tesztelése
- Vonás Frissített felhasználói felület
- Vonás Fejlesztői naplózás
- Vonás API a feliratok és feliratok dinamikus beállításához
- Vonás Alapszintű lokalizációs funkciók
- Vonás Hibakódok összevonása a technológiák között
- Vonás Új hibakód, ha a beépülő modulok (például a Flash vagy a Silverlight) nincsenek telepítve
- Vonás [AzureHtml5JS] Alapszintű diagnosztikai események implementálva

### <a name="bug-fixes-100"></a>Hibajavítások 1.0.0 ###
<!---What is that actually supposed to say?--->
- [Hibajavítás] [AzureHtml5JS] Az MPD-frissítések élő lejátszásának lefagyasztása, ha kis pontatlanságok vannak az időbélyegen belül
- [Hibajavítás] [AzureHtml5JS] Több élő lejátszási probléma elhárítása
- [Hibajavítás] [AzureHtml5JS] Pufferek ürítése, ha az ablak mérete heurisztikus, és magasabb felbontású képernyőre mutat
- [Hibajavítás] [AzureHtml5JS] A Chrome mostantól megfelelően mutatja a befejezett eseményt. A Chrome korábbi ismert hibája miatt a *AzureHtml5JS használatakor nem fog megfelelően elküldeni a befejezett eseményt. Probléma van az alapul szolgáló böngészőben.*
- [Hibajavítás] [AzureHtml5JS] A tech számára letiltott Safari az *OSX Yosemite és a AzureHtml5JS Tech közötti lejátszási probléma megoldása érdekében. Az MSE megvalósítási problémái vannak. Ideiglenes megoldás: kényszeríti a villogást, a Silverlight-et technikai sorrendként ezekhez a felhasználói ügynökökhöz*
- [Hibajavítás] [Flashs] a loadstart hiba történt.

## <a name="020-beta"></a>0.2.0 (bétaverzió) ##

### <a name="features-020"></a>Funkciók 0.2.0 ###

- Vonás A PlayReady és az AES igény szerinti tesztelése, valamint a Live-lásd a kompatibilitási mátrix
- Vonás Megszakítások kezelésére
- Vonás 2 ^ 53-nál nagyobb időbélyegek támogatása
- Vonás Az URL-lekérdezési paraméter megmarad a jegyzékfájl iránti kérelemben
- Vonás Kipróbálatlan A `QuickStart` és a `HighQuality` heurisztikus profilok támogatása
- Vonás Kipróbálatlan Videó stream-információk kimutatása a AzureHtml5JS és a Villogáshoz szükséges bitráta, szélesség és magasság alapján
- Vonás Kipróbálatlan Válassza ki a AzureHtml5JS és a Flashs bitrátát (lásd: API-dokumentáció)

### <a name="bug-fixes-020"></a>Hibajavítások 0.2.0 ###

- [Hibajavítás] a nagy Lejátszás gomb már megtekinthető a WP 8.1-es verziójában
- [Hibajavítás] rögzített több élő lejátszási probléma
- [Hibajavítás] az unmute gomb mostantól a felhasználói felületen működik
- [Hibajavítás] frissített felhasználói felületi betöltési élmény az automatikus lejátszás üzemmódhoz
- [Hibajavítás] Az AMD-betöltő probléma-és metódus-ütközések meghatározása
- [Hibajavítás] A WP 8,1 Cordova alkalmazás betöltési hibája
- [Hibajavítás] A védett tartalom-lekérdezések platform/Tech támogatott Replikavédelemnek válassza ki a megfelelő technológiát a lejátszáshoz.  A "_PlayReady-tartalmak a Chrome-on (Desktop)/Safari 8 (az OSX Yosemite-on) való korábbi ismert hibája jelenleg nem érhető el a Silverlight-lejátszón_"
- [Hibajavítás] nem kezelt kivétel a WinServer 2012 R2-ben, mert Multimédia alaprendszer nincs telepítve erre a gépre.  A nem megvalósított HTML-videó címke API-k használatára történt kísérlet, ami hibát jelez. A jelenlegi megoldás a hiba észlelése és a hiba visszaküldése helyett false (hamis) érték befogása.
- [Hibajavítás] az inicializálási szegmens mindig a keresés vagy a HTTP-hiba után a hibák megelőzése a lejátszás során
- [Hibajavítás] a szimulált folyamat és a timeupdates követésének kikapcsolása, ha hiba történt.
- [Hibajavítás] Eltávolítás a jobb gombbal menü
- [Hibajavítás] [AzureHtml5JS] hibaüzenet nem jelenik meg, ha a PlayReady-tartalomhoz érvénytelen jogkivonat van beállítva.
- [Hibajavítás] Az élő lejátszás során a [AzureHtml5JS] teljes képernyős megjelenítést nem tartotta figyelembe
- [Hibajavítás] [Flash] A Strobe Media Player üzenet jelenik meg, hogy csak Azure Media Player üzenetek jelenjenek meg
- [Hibajavítás] [Silverlight] nem kap "keresett" eseményt, ha az időtartamon túl vagy kevesebb mint 0

## <a name="010-beta-release"></a>0.1.0 (bétaverzió) ##

Kezdeti előzetes kiadás

## <a name="next-steps"></a>Következő lépések ##

- [Azure Media Player rövid útmutató](azure-media-player-quickstart.md)
