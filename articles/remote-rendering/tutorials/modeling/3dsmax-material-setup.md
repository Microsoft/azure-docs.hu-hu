---
title: A PBR-anyagok beállítása a 3ds Max-ban
description: Elmagyarázza, hogyan állíthat be fizikailag alapú renderelési anyagokat a 3ds Max-ban, és hogyan exportálhatja őket FBX formátumba.
author: FlorianBorn71
ms.author: flborn
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 12407d6344c69c747230e9db6fa4d53b4520dc82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96020279"
---
# <a name="tutorial-set-up-physically-based-rendering-materials-in-3ds-max"></a>Oktatóanyag: fizikailag vezérelt renderelési anyagok beállítása a 3ds Max-ban

## <a name="overview"></a>Áttekintés
Ebből az oktatóanyagból az alábbiakat sajátíthatja el:

>[!div class="checklist"]
>
> * A speciális megvilágítás használatával olyan anyagokat rendelhet hozzá egy adott jelenet objektumaihoz.
> * Objektumok és anyagok egypéldányos kezelése.
> * FBX-formátumba exportálhatja a jeleneteket, és kiválaszthatja a fontos beállítások lehetőséget.

> [!Note]
> Az ebben az oktatóanyagban ismertetett eljárás a 3ds Max 2019-es és a 3ds Max 2020-es verziójában működik.
> A 3ds Max 2021-as verziójának a megváltozása azt jelenti, hogy az átalakítási szolgáltatás nem találja a normál térképeket, ha ezt a verziót használják.

A a 3ds Max-ban létrehozott [fizikai alapú renderelési (pbr) anyagok](../../overview/features/pbr-materials.md) egyszerű feladat. A PBR számos módon hasonló a más tartalom-létrehozási alkalmazásokhoz, például a Mayahoz. Ez az oktatóanyag az Azure-beli távoli renderelési projektek alapszintű PBR shader-telepítésének és FBX-exportálásának útmutatója.

Az oktatóanyagban szereplő minta jelenet számos sokszög típusú objektumot tartalmaz. Különböző anyagokat rendeltek hozzájuk, mint például a fa, a fém, a festett fém, a műanyag és a gumi. Általánosságban elmondható, hogy minden anyag a következő textúrákat tartalmazza:

* **Albedó**, amely az anyag színtérképe, és más néven **diffúz** és **BaseColor**.
* Fémesség, amely meghatározza, hogy egy anyag fémes- **e, és** hogy mely részek fémesek. 
* **Érdesség**, amely meghatározza, hogy a felület milyen durva vagy zökkenőmentes legyen.
Emellett hatással van a reflexiók és a csúcsfények élességére vagy homályos is a felületen.
* **Normál**, amely részletes adatokat ad hozzá egy felülethez további sokszögek hozzáadása nélkül. A részletek közé tartoznak például a kimagozás és a horpadások egy fém felületen vagy gabonán a fában.
* **Környezeti elzáródás**, amely lágy árnyékolás hozzáadására és az árnyékok a modellhez való kapcsolódására szolgál. Ez egy szürkeárnyalatos Térkép, amely azt jelzi, hogy a modell mely területein kap teljes világítást (fehér) vagy teljes árnyalatot (fekete).

## <a name="prepare-the-scene"></a>A jelenet előkészítése
A 3ds Max-ben a PBR-anyagok beállításának folyamata a következő.

A kezdéshez számos Box-objektumot hozunk létre, amelyek mindegyike más típusú anyagokat képvisel.

>[!TIP]
>Érdemes megjegyezni, mielőtt elkezdi létrehozni az eszközöket a távoli rendereléshez, amely mérőműszereket használ a méréshez.  
>
>Ezért érdemes beállítani a jelenet rendszeregységeit méterre. Érdemes lehet **mértékegységeket** is beállítani a FBX exportálási beállításaiban a jelenet exportálásakor.

A következő képernyőfelvételen a rendszeregységek a 3ds Max-ban való beállításának lépései láthatók. 

1. A főmenüben **válassza az**  >  **egység beállítása** a  >  **rendszeregységek beállítása beállítást**. A **Rendszeregységek méretezése** területen válassza a **mérőműszerek**: ![ képernyőképet, amely bemutatja, hogyan kell beállítani a rendszeregységeket.](media/3dsmax/system-units.jpg)

1. Most már elkezdjük létrehozni a modelleket. A minta jelenetben több Box objektumot hozunk létre, amelyek mindegyike egy másik anyag típusát jelképezi. Például: fém, gumi és műanyag. 

   >[!TIP]
   >Az adategységek létrehozásakor célszerű a megfelelő módon átnevezni őket. Így később könnyebben megtalálhatja őket, ha a jelenet sok objektumot tartalmaz.

1. Nevezze át az objektumokat az alábbi képernyőképen látható módon: 

   ![Az objektumok átnevezését bemutató képernyőkép.](media/3dsmax/rename-objects.jpg)

## <a name="assign-materials"></a>Anyagok kiosztása

Most, hogy már van néhány objektumunk a jelenetben, ebben az esetben több kocka is megkezdheti a PBR telepítését:

1. A fő eszköztáron válassza az **anyag-szerkesztő** ikont, ahogy az alábbi képernyőképen is látható. Az **M** billentyűt is kiválaszthatja a billentyűzeten a szerkesztő megnyitásához. Az anyag-szerkesztő két **mód közül választhat** : **kompakt anyag szerkesztő** mód és az **anyag-szerkesztő** mód. Mivel ez a jelenet viszonylag egyszerű, a kompakt módot fogjuk használni.

1. Az anyag-szerkesztőben számos gömb jelenik meg. Ezek a gömbök az anyagok. Ezen anyagok egyikét a jelenet minden objektumához (mindegyik dobozhoz) hozzárendeljük. Az anyagok hozzárendeléséhez először válassza ki az egyik objektumot a fő nézetablakban. Ezután válassza ki az első gömbet az anyag-szerkesztőben. Miután hozzárendelte egy objektumhoz, a kiválasztott anyag ki lesz emelve, ahogy az a következő képen is látható.

1. Jelölje be az **anyag kijelölése a kijelöléshez** lehetőséget, amint az látható. Az anyag már hozzá van rendelve a kijelölt objektumhoz.

   ![Az anyagok hozzárendelését bemutató képernyőkép.](media/3dsmax/assign-material.jpg)

    Az anyag-szerkesztőben az igényeinek megfelelően széles választékban választhat az alapanyagok típusától függően. Az anyag típusa általában alapértelmezés szerint a **standard** értékre van beállítva. Ez az anyag olyan alapszintű anyag, amely nem alkalmas a PBR beállítására. Ezért módosítani kell az anyag típusát egy PBR-anyagra. A fizikai anyagok az Azure távoli renderelési projektjeinek előnyben részesített 3ds Max-anyagok.

1. Az anyag-szerkesztőben válassza a **standard** fület. Az **anyag/Térkép böngészőben** válassza a **fizikai anyag** elemet. Ez a művelet átalakítja a hozzárendelt **standard** anyagokat egy pbr fizikai anyagba.

   ![Képernyőkép, amely bemutatja, hogyan módosíthatja az anyagot.](media/3dsmax/physical-material.jpg)

    Az anyag-szerkesztőben ekkor megjelennek a fizikai anyagok tulajdonságai, ahogy az alábbi képernyőképen is látható. Most már megkezdheti a textúrák hozzárendelését az eszközhöz.

   ![A textúrák listáját bemutató képernyőkép.](media/3dsmax/textures-list.jpg)

Amint láthatja, számos térképet és textúrákat adhat hozzá az anyaghoz. Ebben az oktatóanyagban csak öt textúra-tárolóhelyet használunk az anyagban.

>[!TIP]
>A fenti képernyőképen látható módon érdemes az anyagok nevét megfelelően feltüntetni.

A textúrák létrehozása a preferencia vagy a használat alapján változhat. Előfordulhat például, hogy olyan csempe-textúrákat szeretne használni, amelyeket bármilyen eszközre alkalmazhat. Vagy előfordulhat, hogy egy projekt vagy eszköz meghatározott részeire van szüksége a saját textúrák egyéni készletének megköveteléséhez. Előfordulhat, hogy olyan általános csempe-textúrákat szeretne használni, amelyek online állapotba kerülhetnek. Saját maga is létrehozhat olyan alkalmazásokban, mint például a Photoshop, a Quixel Suite és az anyag Suite.

A textúrák hozzárendelésének megkezdése előtt figyelembe kell venni az eszköz textúrájának koordinátáit (UVW). Az ajánlott eljárás, ha bármilyen textúrát alkalmaz a modellre, hogy a modell kicsomagolása megtörténjen. (A textúrák megfelelő UV-kicsomagolás nélkül nem jelennek meg megfelelően.) Ez különösen fontos a felhasználási célokra, mert a modellünk környezeti elzáródás (AO) leképezését szeretnénk használni. A Maya-ben lévő rája Shadertől eltérően a 3ds Max fizikai anyagai nem rendelkeznek dedikált AO Texture-tárolóhelytel. Ezért alkalmazzuk az AO térképet egy másik tárolóhelyre. Annak engedélyezéséhez, hogy azt a többi textúráktól elkülönítve lehessen használni (például a csempe textúrája), egy saját UVW-leképezési csatornát rendelünk hozzá. 

Először társítson egy kibontott UVW-módosítót a modellhez, ahogy az alábbi képernyőképen is látható. 

- A kijelölt objektumok tulajdonságok szerkesztőjében válassza ki a módosító listát. A megjelenő legördülő listában görgessen lefelé, és válassza a **UVW kicsomagolása** lehetőséget. Ez a művelet egy kibontott UVW-módosítót alkalmaz az eszközre.
![A UVW kicsomagolásának kiválasztását bemutató képernyőkép.](media/3dsmax/unwrap-modifier.jpg)

  A Térkép csatorna értéke 1. Az 1. csatornán általában a fő kicsomagolást hajtja végre. Ebben az esetben az objektum ki lett csomagolva az átfedésben lévő textúra koordinátái (UV) nélkül.
![A nem burkolt textúra koordinátáit (UVW) megjelenítő képernyőkép.](media/3dsmax/unwrapped-uvw.jpg)

A következő lépés egy második UV-Térkép csatorna létrehozása.

1. Ha nyitva van, zárjuk be az UV-szerkesztőt. A **UVs szerkesztése** menü **csatorna** szakaszában módosítsa a csatorna számát **2** értékre. A Térkép Channel 2 a várt csatorna az AO Maps-hez. 

1. A **csatorna módosítása figyelmeztetés** párbeszédpanelen **áthelyezheti** az 1. csatorna meglévő UVs az új 2. csatornára, vagy **lemondhatja** a meglévő UVs, amely automatikusan létrehoz egy új UV-kicsomagolást. Válassza a **felhagyás** csak lehetőséget, ha azt tervezi, hogy új UV-kicsomagolást hoz létre az Ao térképhez, amely eltér a Térkép Channel 1. UVs. (Ha például az 1. csatornán szeretné használni a csempe-textúrákat.) Ebben az oktatóanyagban áthelyezzük a UVs az egyik csatornáról a 2-es csatornára, mert nem kell szerkeszteni az új UV-csatornát.

   >[!NOTE]
   >Még akkor is, ha átmásolta (áthelyezte) az UV kicsomagolását az 1. csatornáról a 2. csatornára, a szükséges módosításokat az új csatorna UVs is elvégezheti anélkül, hogy az hatással lenne az eredeti Térkép csatornára.

   ![A csatorna változásának figyelmeztetését bemutató képernyőkép.](media/3dsmax/channel-change.jpg)

Most, hogy létrehoztuk az új Térkép csatornát, visszatérhetünk az anyag-szerkesztőben lévő fizikai anyagokhoz, és megkezdjük a textúrák hozzáadását. Először is hozzáadjuk az AO térképet, mert van egy másik lépés, amely lehetővé teszi, hogy megfelelően működjön. Miután az AO-térképet csatlakoztatta az anyaghoz, konfigurálnia kell, hogy használhassa a Map Channel 2-t.

Ahogy korábban már említettük, a 3ds Max fizikai anyagában nincs dedikált tárolóhely az AO Maps-hez. Ehelyett alkalmazza az AO-térképet a **diffúz durva** tárolóhelyre.

1. A fizikai anyag **általános térképek** listájában válassza ki a **nincs leképező** tárolóhelyet a **diffúz durvaség** mellett, és töltse be az Ao-térképet.

1. Az AO textúrák tulajdonságainál a Térkép csatorna alapértelmezett értéke **1** . Módosítsa ezt az értéket **2** értékre. Ez a művelet végrehajtja az AO-Térkép hozzáadásához szükséges lépéseket.

   >[!IMPORTANT]
   >Ez egy fontos lépés, különösen akkor, ha a 2. csatornán lévő UVs eltérnek az 1. csatornán lévők, mert az Ao nem fog megfelelően leképezni, ha a megfelelő csatorna van kiválasztva.

   ![Képernyőkép, amely bemutatja, hogyan rendeljen hozzá egy AO-térképet.](media/3dsmax/assign-ao-map.jpg)

Most hozzárendeljük a normál leképezést a PBR-anyaghoz. Ez a művelet némileg eltér a Maya folyamattól. A normál térképet nem alkalmazza közvetlenül a bump Map tárolóhelyre. (A 3ds Max fizikai anyagában nincs normál térképi tárolóhely.) Ehelyett a normál leképezést egy normál Térkép-módosítóhoz adja hozzá, amely maga a normál tárolóhelyre van csatlakoztatva.

1. A fizikai anyag tulajdonságainak **speciális térképek** szakaszában (az anyag-szerkesztőben) válassza ki a **nem Térkép** tárolóhelyet a **map bump** mellett. 

1. Az **anyag/Térkép böngészőben** keresse meg és válassza ki a **normál bump** elemet. Ez a művelet hozzáadja a **normál bump** -módosítót az anyaghoz.

1. A **normál bump** -módosítóban válassza a **Nincs leképezés** a **normál** mellett lehetőséget. Keresse meg és töltse be normál térképét.

1. Győződjön meg arról, hogy a metódus **tangens** értékre van állítva. (Alapértelmezés szerint ez a következő:.) Ha szükséges, kapcsolja be a **flip Green (Y)** billentyűkombinációt.

   ![Képernyőkép, amely bemutatja a normál bump kiválasztásának módját. ](media/3dsmax/normal-bump.jpg)
    ![ A normál Térkép betöltését bemutató képernyőkép.](media/3dsmax/load-normal-map.jpg)

A normál leképezés megfelelően kiosztva a fizikai anyagok telepítésének befejezéséhez a fennmaradó textúrákat is hozzárendelheti. Ez a folyamat egyszerű. Nincsenek megfontolandó speciális beállítások. A következő képernyőképen az anyaghoz rendelt textúrák teljes készletét láthatja: 

![Képernyőkép, amely az anyaghoz rendelt textúrák teljes készletét jeleníti meg.](media/3dsmax/all-textures.jpg)

Most, hogy létrehozták és beállították a PBR-anyagokat, érdemes meggondolni a jelenet egypéldányos objektumait. A jelenet hasonló objektumai, például a NUTS, a csavarok, a csavarok és a mosók. Minden olyan objektum, amely azonos, jelentős megtakarítást eredményezhet a fájlméret szempontjából. A főobjektum példányai rendelkezhetnek saját méretezéssel, rotációval és átalakítással, így szükség szerint helyezhetik el őket a jelenetben. A 3ds Max-ben a egypéldányos folyamata egyszerű.

1. A fő nézetablakban jelölje ki az exportálni kívánt objektumot vagy objektumokat.

1. Tartsa lenyomva a **SHIFT** billentyűt, és húzza felfelé az eszközöket az átalakító (áthelyezés) eszköz használatával. 

1. A **klónozási beállítások** párbeszédpanelen állítsa az **objektum** **példányt példányra** , majd kattintson **az OK gombra**:

   ![Képernyőfelvétel a klónozási beállítások párbeszédpanelről.](media/3dsmax/instance-object.jpg)

Ez a művelet létrehozza az objektum egy példányát, amely a szülőtől és a szülő más példányaitól függetlenül helyezhető el, forgatható el vagy méretezhető.

>[!IMPORTANT]
>A példányon végrehajtott módosításokat az objektum összes példánya számára továbbítjuk. Tehát ha egy példányban lévő objektum összetevőit (például a csúcspontokat és a sokszögeket) használja, ügyeljen arra, hogy az összes példányra hatással legyen a módosítások. Ne feledje, hogy minden példányban lévő objektum bármikor elvégezhető egy egyedi objektumba. 

>[!TIP]
>Ha egypéldányos a jelenetben, érdemes lehet példányokat létrehozni a következő lépésekkel. A példányokat tartalmazó példányok későbbi cseréje nehéz. 

Az exportálási folyamatba való áttérés előtt megfontolandó utolsó lépés az, hogy hogyan érdemes a jelenetét/eszközét a megosztáshoz becsomagolni. Ideális esetben, ha az adategységet az ügyfelek vagy a csapattagok számára adja át, érdemes lehet megnyitni és megtekinteni az adategységet, ahogy azt a minimálisan felhasználható mennyiséggel kell látni. Ezért fontos, hogy az eszköz textúrájának elérési útja a jelenet fájlhoz képest legyen. Ha az adott eszköz textúrájának elérési útja helyi meghajtóra vagy abszolút elérési útra/helyre mutat, akkor nem töltődik be a jelenetbe, ha egy másik számítógépen van megnyitva, még akkor is, ha a. max fájl ugyanabban a mappában található, mint a textúrák. A textúra elérési útjaihoz képest a 3ds Max a probléma megoldásához és viszonylag egyszerűvé tételéhez.

1. A fő eszköztáron válassza a **fájl**  >  **hivatkozási**  >  **eszköz követése váltógomb** lehetőséget. 

1. Az eszköz nyomon követése ablakban megjelenik az összes olyan textúra, amelyet a **Maps/Shaders** OSZLOPBAN felsorolt pbr-anyagokra alkalmazott.

1. Mellettük a **teljes elérési út** oszlopban látható a textúrák helyének elérési útja, amely valószínűleg a helyi gépen lévő helyük elérési útját fogja látni.

1. Végül egy **állapot** nevű oszlopot fog látni. Ebben az oszlopban látható, hogy egy adott textúra található-e, és alkalmazva lett-e a színtérre. A textúra a következő kifejezések egyikével van megjelölve: **OK**, **talált** vagy **hiányzó fájl**. Az első kettő azt jelzi, hogy a fájl megtalálható és be van töltve. Az utóbbi nyilvánvalóan azt jelenti, hogy a Szemléző nem találta meg a fájlt.
 
   ![Képernyőkép, amely megjeleníti az eszköz követési ablakát.](media/3dsmax/texture-paths.jpg)

Észreveheti, hogy az összes textúrája nem szerepel az eszköz nyomon követése ablakban az első megnyitásakor. Ez nem érinti a következőt:. Az elérési út alatt futtatott folyamat egyszer vagy kétszer megkeresi a jelenet összes textúráját. A Path-Finding folyamat a következő: 

1. Az eszköz nyomon követése ablakban tartsa lenyomva a **SHIFT** billentyűt, és válassza ki a felső textúrát a **Maps/Shaders** listán, és folytassa a **SHIFT** billentyűt a lista utolsó textúrájának kiválasztásával. Ez a művelet kiválasztja a listában szereplő összes textúrát. A kiválasztott textúrák kék színnel vannak kiemelve. (Lásd az előző képernyőképet.)

1. Kattintson a jobb gombbal a kijelölésre, és válassza a **beállítás elérési útja** lehetőséget.

1. Az **eszköz elérési útjának megadása** mezőben válassza ki a textúrák helyi elérési útját, és cserélje le a következőre: `.\` .  Válassza az **OK** lehetőséget. 

    Az eszköz nyomon követésére szolgáló ablak a következő képernyőképen látható módon fog frissülni. A frissítés eltarthat egy ideig, attól függően, hogy a jelenet hány textúrákat tartalmaz, és milyen nagy a jelenet.
![Screensthot, amely megjeleníti a frissített eszköz követési ablakát.](media/3dsmax/resolve-textures.jpg)

Figyelje meg, hogy a **teljes elérési út** oszlop most üres. Ez azt jelenti, hogy a jelenet már nem keresi a megfelelő textúrákat egy adott (abszolút) helyen. A rendszer mindig megkeresi őket, amíg a. max fájl vagy a kapcsolódó FBX ugyanabban a mappában található, mint a textúrák. 

>[!NOTE]
>Előfordulhat, hogy többször is meg kell ismételnie ezt a folyamatot, hogy megkeresse és megoldja az összes textúrákat és elérési utat. Ez nem érinti a következőt:. Csak ismételje meg a folyamatot, amíg az összes érintett eszközt el nem veszi. Bizonyos esetekben egyes fájlok nem találhatók. Ebben az esetben egyszerűen jelölje ki az összes objektumot a listában, majd válassza a **Hiányzó elérési utak eltávolítása** lehetőséget. (Lásd az előző képet.)

## <a name="fbx-export"></a>FBX-exportálás

Most, hogy elvégezte a textúra elérési útjaihoz viszonyított relatív értéket, átléphetünk a FBX exportálására. A folyamat is egyszerű, és több módon is elvégezhető. 

>[!TIP]
>Hacsak nem szeretné a teljes jelenetet exportálni, érdemes kiválasztani, hogy csak a szükséges eszközöket exportálja. Az erőforrás-igényes jelenetek esetében az Exportálás hosszú időt is igénybe vehet.
>
>Ha olyan módosítókat használ, mint például a Turbosmooth vagy az Open SubDiv, érdemes összecsukni őket az exportálás előtt, mert problémákat okozhatnak az exportálás során. Az összecsukás előtt mentse a jelenetet. 

1. A jelenetben válassza ki az exportálni kívánt eszközöket. A fő eszköztáron válassza a **fájl**  >  **Exportálás exportálás**  >  **kiválasztva lehetőséget**.

1. Az **exportálandó fájl kiválasztása** párbeszédpanelen írja be vagy válassza ki a kimeneti fájl nevét. A **Fájltípus** listáról válassza az **Autodesk (*. FBX)** lehetőséget. Ez a művelet megnyitja a FBX exportálási ablakát.

  >[!IMPORTANT] 
  >Ha a jelenetben példányokat hozott létre, fontos, hogy a **példányok megőrzése** lehetőséget válassza a FBX exportálási beállításaiban. 

  ![Képernyőkép, amely bemutatja, hogyan exportálhat a FBX.](media/3dsmax/fbx-export.jpg)

  Ne feledje, hogy a fájl exportálásának néhány módja van. Ha a cél a FBX és a hozzá tartozó textúra-fájlok megosztása egy mappában/könyvtárban, az alábbi képernyőképen látható beállítások jól működnek. 

   Ha inkább a nagy méretű mappákat/könyvtárakat szeretné megosztani a FBX, akkor a textúrákat a FBX ágyazhatja be. A textúrák beágyazásával a teljes eszköz, beleértve a textúrákat is, egyetlen FBX kerül. Ezzel egyesíti az exportálást egyetlen objektumba, de ennek eredményeképpen a FBX-fájl jelentősen nagyobb lesz.

   >[!IMPORTANT]
   >Ha az eredményül kapott FBX-fájl mérete 2,4 GB-nál nagyobb, akkor a FBX exportálási beállításaiban megadott minimális verziónak 2016 vagy újabb értéknek kell lennie. (Lásd az előző képernyőképet.) Az újabb verziók 64 bites támogatással rendelkeznek, így nagyobb fájlokat támogatnak.

1. Ha a jeleneteket a beépített textúrákkal szeretné exportálni, akkor a * FBX exportálása ablakban válassza az **adathordozó beágyazása** elemet. 

1. Válassza ki a többi beállítást, majd kattintson **az OK gombra**:

    ![Képernyőkép, amely megjeleníti a FBX exportálási beállításait.](media/3dsmax/fbx-settings.jpg)


   Ha fizikai anyag használata közben FBX exportál, akkor a FBX exportálási ablakában **a következő** figyelmeztetés jelenik meg: 

   ![A lényeges exportálási figyelmeztetést megjelenítő képernyőkép.](media/3dsmax/export-warnings.jpg)

   Ez a figyelmeztetés arról tájékoztat, hogy az exportált anyagok esetleg nem kompatibilisek más szoftvercsomagok. Mivel a fizikai anyagok kompatibilisek az Azure távoli renderelésével, nem kell aggódnia erről a figyelmeztetésről. 

1. A folyamat befejezéséhez és az ablak bezárásához kattintson **az OK gombra** .

## <a name="conclusion"></a>Összegzés

Általánosságban elmondható, hogy az ilyen típusú anyagok reálisak, mert a fény valós fizikáján alapulnak. Ez egy további alámerülés hatást eredményez, amely úgy tűnik, hogy a jelenet valóban létezik a valós világban.

## <a name="next-steps"></a>Következő lépések

Most már tudja, hogyan állíthat be speciális világítású anyagokat a jelenetekben található objektumokhoz. Arról is tájékozódhat, hogyan exportálhat objektumokat FBX formátumba, amelyet az Azure távoli renderelése támogat. A következő lépés a FBX fájl átalakítása és megjelenítése az Azure-beli távoli renderelésben.

>[!div class="nextstepaction"]
>[Gyorsútmutató: Modell konvertálása a renderelés előtt](../../quickstarts\convert-model.md)
