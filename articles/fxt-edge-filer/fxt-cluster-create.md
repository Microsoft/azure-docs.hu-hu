---
title: 'Oktatóanyag: az Azure FXT Edge Filer gyorsítótár-fürt létrehozása'
description: Megtudhatja, hogyan hozhat létre hibrid tárolási gyorsítótár-fürtöt az Azure FXT Edge Filer-sel, és hogyan jelentkezhet be a fürt Vezérlőpultján a fürt beállításainak konfigurálásához.
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: d019f5df4bba6d223076c8ce35151510afedf2e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220805"
---
# <a name="tutorial-create-the-azure-fxt-edge-filer-cluster"></a>Oktatóanyag: az Azure FXT Edge Filer-fürt létrehozása

Miután telepítette és inicializálta az Azure FXT Edge Filer-beli hardveres csomópontjait a gyorsítótárhoz, a FXT-fürt szoftver használatával hozza létre a gyorsítótár-fürtöt.

Ez az oktatóanyag végigvezeti a hardveres csomópontok fürtként történő konfigurálásának lépésein.

Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
>
> * A fürt létrehozásának megkezdése előtt milyen információk szükségesek
> * A fürt felügyeleti hálózata, a fürt hálózata és az ügyfél felé irányuló hálózat közötti különbség
> * Kapcsolódás fürtcsomópont-csomóponthoz
> * Kezdeti fürt létrehozása egy Azure FXT Edge Filer-csomópont használatával
> * Bejelentkezés a fürt Vezérlőpultján a fürt beállításainak konfigurálásához

Ez az eljárás 15 – 45 percet vesz igénybe attól függően, hogy mennyi kutatást kell végeznie az IP-címek és a hálózati erőforrások azonosításához.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt végezze el az alábbi előfeltételeket:

* Az Azure FXT Edge Filer Hardware Systems telepítése az adatközpontban

  Csak egy csomópontra van szükség a fürt létrehozásához, de [legalább két csomópontot kell felvennie](fxt-add-nodes.md) a fürt konfigurálásához és a használatra való használatra.

* A megfelelő energiaellátási és hálózati kábelek csatlakoztatása a rendszerhez  
* Kapcsolja be legalább egy Azure FXT Edge Filer-csomópontot, és [állítsa be a gyökér jelszavát](fxt-node-password.md)

## <a name="gather-information-for-the-cluster"></a>A fürt adatainak összegyűjtése

Az Azure FXT Edge Filer-fürt létrehozásához a következő információk szükségesek:

* Fürt neve

* A fürthöz beállítani kívánt rendszergazdai jelszó

* IP-címek:

  * Egyetlen IP-cím a fürtszolgáltatáshoz, valamint a felügyeleti hálózathoz használandó hálózati maszk és útválasztó
  * Az első és az utolsó IP-cím a fürt IP-címeinek folytonos tartományában (csomópont – csomópont) kommunikáció. A részletekért tekintse meg az alábbi [IP-címek eloszlása](#ip-address-distribution)című szakaszt.
  * (Az ügyfelek felé irányuló IP-címek beállítása a fürt létrehozása után történik.)

* Hálózati infrastruktúra adatai:

  * A fürt DNS-kiszolgálójának IP-címe
  * A fürt DNS-tartományának neve
  * A fürt NTP-kiszolgálóinak neve vagy IP-címe (akár egy kiszolgáló, akár három vagy több)
  * Azt határozza meg, hogy engedélyezi-e az IEEE 802.1 AX-2008 hivatkozás összesítését a fürt felületén
  * Ha engedélyezi a kapcsolat összesítését, az IEEE 802.3 ad (LACP) dinamikus összesítését is használja

Ezeket a hálózati infrastruktúra-elemeket a fürt létrehozása után is konfigurálhatja, de jobb megoldás a létrehozáskor.

### <a name="ip-address-distribution"></a>IP-címek eloszlása

Az Azure FXT Edge Filer Hybrid Storage cache-fürt a három kategóriába tartozó IP-címeket használja:

* Felügyeleti IP-cím: egyetlen IP-cím a fürtözés kezeléséhez

  Ez a címe belépési pontként szolgál a fürtkonfiguráció-segédprogramok (a web-alapú Vezérlőpult vagy az XML-RPC API) eléréséhez. Ezt a címeket a rendszer automatikusan a fürt elsődleges csomópontjára rendeli hozzá, és automatikusan áthelyezi az elsődleges csomópont megváltozásakor.

  Más IP-címek használhatók a Vezérlőpult eléréséhez, de a felügyeleti IP-cím úgy van kialakítva, hogy az egyes csomópontok feladatátvétele esetén is biztosítson hozzáférést.

* Fürthálózat: A fürt kommunikációjának IP-címeinek tartománya

  A fürthálózat a fürtcsomópontok közötti kommunikációhoz, valamint a háttérbeli tárolóból (Core filers) származó fájlok beolvasására szolgál.

  **Ajánlott eljárás:** Az egyes Azure FXT Edge Filer-csomópontokon a fürt kommunikációjához használt fizikai portok közül egy IP-címet kell lefoglalni. A fürt automatikusan hozzárendeli a megadott tartományhoz tartozó címeket az egyes csomópontokhoz.

* Ügyfél felé irányuló hálózat: az ügyfelek által a fájlok igénylésére és írására használt IP-címek tartománya

  Az ügyfelek az ügyfél hálózati címeit használják az alapvető Filer-információknak a fürtön keresztüli eléréséhez. Előfordulhat például, hogy egy NFS-ügyfél csatlakoztatni kívánja az egyik címet.

  **Ajánlott eljárás:** Az ügyfél-kommunikációhoz használt fizikai portok egy IP-címének lefoglalása minden egyes FXT-adatsorozat-csomóponton.

  A fürt a lehető legegyenletesebb módon osztja el az ügyfelek felé irányuló IP-címeket az összetevő-csomópontok között.

  Az egyszerűség kedvéért sok rendszergazda állít be egyetlen DNS-nevet ciklikus multiplexelés DNS-(RRDNS-) konfigurációval, hogy könnyebb legyen az ügyfelek kéréseinek terjesztése a címtartomány között. Ez a beállítás azt is lehetővé teszi, hogy az összes ügyfél ugyanazt a csatlakoztatási parancsot használja a fürt eléréséhez. További információért olvassa el a [DNS konfigurálása](fxt-configure-network.md#configure-dns-for-load-balancing) című témakört.

Új fürt létrehozásához meg kell adni a felügyeleti IP-címet és a fürt különböző hálózati címeinek tartományát. Az ügyfél felé irányuló címek megadása a fürt létrehozása után történik.

## <a name="connect-to-the-first-node"></a>Kapcsolódás az első csomóponthoz

Bármelyik telepített FXT-csomóponthoz csatlakozhat, és az operációs rendszer szoftverét használva állíthatja be a fürtöt.

Ha még nem tette meg, kapcsolja be legalább az egyik FXT-csomópontot a fürthöz, és győződjön meg arról, hogy hálózati kapcsolatban és IP-címmel rendelkezik. A csomópont aktiválásához új legfelső szintű jelszót kell beállítania, ezért kövesse a [hardveres jelszavak beállítása](fxt-node-password.md) című témakör lépéseit, ha még nem tette meg.

A hálózati kapcsolat ellenőrzéséhez győződjön meg arról, hogy a csomópont hálózati kapcsolatának LED-je világítva van (és ha szükséges, a hálózati kapcsolón lévő mutatókat, amelyekhez csatlakoztatva van). A kijelző LED-ek az [Azure FXT Edge Filer-hardver állapotának monitorozása](fxt-monitor.md)című témakörben találhatók.

A csomópont indításakor a rendszer IP-címet kér. Ha egy DHCP-kiszolgálóhoz csatlakozik, akkor elfogadja a DHCP által biztosított IP-címet. (Ez az IP-cím ideiglenes. A fürt létrehozásakor megváltozik.)

Ha nem csatlakozik egy DHCP-kiszolgálóhoz, vagy nem kap választ, a csomópont a Bonjour szoftver használatával állítja be az önkiszolgáló IP-címet a 169,254 formátumban. \* . \* . A fürt létrehozásához azonban egy ideiglenes statikus IP-címet kell beállítania a csomópont egyik hálózati kártyáján. Ebben az örökölt dokumentumban az utasítások is szerepelnek. forduljon a Microsoft szolgáltatáshoz, és támogassa a frissített információkat: [a függelék: statikus IP-cím beállítása egy FXT-csomóponton](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

### <a name="find-the-ip-address"></a>Az IP-cím megkeresése

Kapcsolódjon az Azure FXT Edge Filer-csomóponthoz az IP-címének megkereséséhez. Használhat soros kábelt, közvetlen kapcsolatot az USB-és VGA-portokkal, vagy csatlakozhat egy KVM-kapcsolón keresztül. (A port kapcsolat részleteiért lásd a [kezdeti jelszavak beállítása](fxt-node-password.md)című témakört.)

A csatlakozás után jelentkezzen be a felhasználónévvel `root` és a jelszóval, amelyet a csomópont első indításakor adott meg.  

A bejelentkezést követően meg kell határoznia a csomópont IP-címét.

A parancs használatával `ifconfig` megtekintheti a rendszerhez rendelt címeket.

A parancs például `ifconfig | grep -B5 inet` internetes címmel rendelkező portokat keres, és öt sornyi kontextust biztosít a port azonosítójának megjelenítéséhez.

Jegyezze fel az ifconfig jelentésben látható bármely IP-címet. A portok neveivel (például e0a vagy e0b) felsorolt címek jó beállítások. Ne használjon E7 * névvel rendelkező IP-címeket, mivel ezek a nevek csak a iDRAC/IPMI szolgáltatás portjaihoz használatosak.

## <a name="load-the-cluster-configuration-wizard"></a>A fürt konfigurációs varázslójának betöltése

A fürt létrehozásához használja a böngészőalapú fürt konfigurációs eszközét.

Adja meg a csomópont IP-címét egy böngészőben. Ha a böngésző olyan üzenetet ad a helyről, amely nem megbízható, folytassa a helyet. (Az egyes Azure FXT Edge-alapú Filer-csomópontok nem rendelkeznek CA által biztosított biztonsági tanúsítványokkal.)

![Vezérlőpult bejelentkezési lapja a böngészőablakban](media/fxt-cluster-create/unconfigured-node-gui.png)

Hagyja üresen a **Felhasználónév** és a **jelszó** mezőket. A fürt létrehozási oldalának betöltéséhez kattintson a **Bejelentkezés** gombra.

![A nem konfigurált csomópont kezdeti telepítési képernyője a böngésző alapú grafikus felhasználói felület Vezérlőpultján. Megjeleníti a szoftverek frissítésének lehetőségeit, a fürt manuális konfigurálását, illetve a fürt konfigurálását egy telepítési fájlból.](media/fxt-cluster-create/setup-first-screen.png)

## <a name="create-the-cluster"></a>A fürt létrehozása

A fürtkonfiguráció eszköz végigvezeti az Azure FXT Edge Filer-fürt létrehozásához szükséges képernyők egy halmazán. Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a [szükséges információkkal](#gather-information-for-the-cluster) .

### <a name="creation-options"></a>Létrehozási beállítások

Az első képernyő három lehetőséget biztosít. Ha nem rendelkezik speciális utasításokkal a támogatási személyzettől, használja a manuális konfigurálási lehetőséget.

Kattintson a következőre: **manuálisan konfigurálja a fürtöt** az új fürtkonfiguráció-beállítási beállítások képernyő betöltéséhez.

A többi beállítást ritkán használják:

* "A rendszerkép frissítése" megkéri, hogy a fürt létrehozása előtt telepítse az új operációsrendszer-szoftvereket. (A jelenleg telepített szoftververzió megjelenik a képernyő tetején.) Meg kell adnia a szoftvercsomag fájlját – vagy egy URL-címet, egy felhasználónevet vagy jelszót, vagy egy fájlt kell feltöltenie a számítógépről.

* A fürt telepítési fájlját esetenként a Microsoft ügyfélszolgálata és támogatása is használja.

## <a name="cluster-options"></a>Fürt beállításai

A következő képernyőn az új fürt beállításainak konfigurálását kéri.

Az oldal két fő szakaszra, **alapszintű konfigurációra** és **hálózati konfigurációra** van osztva. A hálózatkezelési konfiguráció szakasz a következő alszakaszokat is tartalmazza: egy a **felügyeleti** hálózathoz, egy pedig a **fürthöz** .

### <a name="basic-configuration"></a>Alapszintű konfiguráció

A felső szakaszban adja meg az új fürt alapvető információit.

![Az "alapszintű konfiguráció" szakasz részletei a böngésző grafikus felhasználói felület lapján. Három mezőt mutat be (fürt neve, rendszergazdai jelszó, Jelszó megerősítése)](media/fxt-cluster-create/basic-configuration.png)

* **Fürt neve** – adjon meg egy egyedi nevet a fürt számára.

  A fürt nevének meg kell felelnie a következő feltételeknek:
  
  * 1 és 16 karakter közötti hosszúság
  * Tartalmazhat betűket, számokat és kötőjelet (-) és aláhúzás (_) karaktereket.
  * Nem tartalmazhat más írásjeleket vagy speciális karaktereket
  
  Ezt a nevet később is megváltoztathatja a **fürt**  >  **általános telepítési** konfigurációs lapján. (A fürt beállításaival kapcsolatos további információkért olvassa el a [fürt konfigurációs útmutatóját](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html), amely nem része ennek a dokumentáció-készletnek.)

  > [!NOTE]
  > A fürt neve azonosítja a figyelési vagy hibaelhárítási támogatáshoz feltöltött rendszerinformációkat, így hasznos lehet a vállalat nevének belefoglalása.

* **Rendszergazdai jelszó** – az alapértelmezett rendszergazda felhasználó jelszavának beállítása `admin` .
  
  Egyéni felhasználói fiókokat kell beállítania minden olyan személy számára, aki felügyeli a fürtöt, de nem távolíthatja el a felhasználót `admin` . Jelentkezzen be, `admin` Ha további felhasználókat kell létrehoznia.

  A jelszót a `admin`   >  fürt Vezérlőpultjának adminisztráció **felhasználói** beállítások lapján módosíthatja. Részletekért olvassa el a **felhasználói** dokumentációt a [fürtkonfiguráció útmutatóban](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_users.html).

<!-- to do: update "legacy" URLs when docs are ported to Microsoft site -->

### <a name="network-configuration"></a>Hálózati konfiguráció

A **hálózatkezelés** szakasz felszólítja a fürt által használt hálózati infrastruktúra megadására.

Két különálló hálózat konfigurálható:

* A *felügyeleti hálózat* rendszergazdai hozzáférést biztosít a fürthöz a konfiguráláshoz és a figyeléshez. Az itt megadott IP-címet a vezérlőpulthoz vagy SSH-hozzáféréshez való csatlakozáskor használja a rendszer.

  A legtöbb fürt csak egyetlen felügyeleti IP-címet használ, de ha felületeket szeretne hozzáadni, a fürt létrehozása után ezt megteheti.

* A *fürthálózat a fürtcsomópontok* és a fürtcsomópontok, valamint a háttérbeli tárolók (Core filers) közötti kommunikációhoz használatos.

Az ügyfél felé irányuló hálózat később, a fürt létrehozása után van konfigurálva.

Ez a szakasz a két hálózat által használt DNS-és NTP-kiszolgálók konfigurációját is tartalmazza.

### <a name="configure-the-management-network"></a>A felügyeleti hálózat konfigurálása

A **felügyeleti** szakaszban található beállítások olyan hálózatra vonatkoznak, amely rendszergazdai hozzáférést biztosít a fürthöz.

![Részletek a "felügyelet" szakaszhoz, amely 5 lehetőséggel és egy 1 GB-os felügyeleti hálózathoz tartozó jelölőnégyzettel rendelkezik.](media/fxt-cluster-create/management-network-filled.png)

* **Felügyeleti IP** -Cím – Itt adhatja meg azt az IP-címet, amelyet a fürt Vezérlőpultjának eléréséhez fog használni. Ezt a címeket a fürt elsődleges csomópontja igényli, de automatikusan egy kifogástalan állapotú csomópontra kerül, ha az eredeti elsődleges csomópont elérhetetlenné válik.

  A legtöbb fürt csak egy felügyeleti IP-címet használ. Ha egynél többre van szüksége, a fürt létrehozása után is hozzáadhatja őket a **fürt**  >  **felügyeleti hálózati** beállítások lapján. További információ a [fürt konfigurációs útmutatójában](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html)olvasható.

* **Hálózati maszk** – a felügyeleti hálózat hálózati maszkjának megadása.

* **Útválasztó** – adja meg a felügyeleti hálózat által használt alapértelmezett átjáró-címeket.

* **VLAN-címke (nem kötelező)** – ha a fürt VLAN-címkéket használ, adja meg a felügyeleti hálózat címkéjét.

  További VLAN-beállítások vannak konfigurálva a **fürt**  >  **VLAN** -beállításai lapon. További információért olvassa el a VLAN-ok és a [fürt > VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vlan.html) [használata](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) a fürt konfigurációs útmutatójában című témakört.

* **MTU** – ha szükséges, állítsa be a fürt felügyeleti hálózatának maximális átviteli egységét (MTU).

* **1 GB-os mgmt hálózat használata** – jelölje be ezt a jelölőnégyzetet, ha a FXT-csomópontokon a két 1 GbE hálózati portot csak a felügyeleti hálózathoz szeretné hozzárendelni. (Az összes többi forgalom számára elérhető 25GbE-/10 GbE-portok szükségesek.) Ha nem jelöli be ezt a jelölőnégyzetet, a felügyeleti hálózat az elérhető legnagyobb sebességű portot használja.

### <a name="configure-the-cluster-network"></a>A fürt konfigurálása

A fürt hálózati beállításai a fürtcsomópontok között, valamint a fürtcsomópontok és a fő filers közötti forgalomra vonatkoznak.

![a "cluster" szakasz részletei a hat érték megadására szolgáló mezőkkel](media/fxt-cluster-create/cluster-network-filled.png)

* **Első IP** -cím és **utolsó IP** – adja meg azokat az IP-címeket, amelyek meghatározzák a fürt belső kommunikációjához használt tartományt. Az itt használt IP-címeknek összefüggőnek kell lennie, és a DHCP nem rendeli hozzá őket.

  A fürt létrehozása után további IP-címeket is hozzáadhat. Használja a **fürt**  >  **hálózati** beállításait tartalmazó lapot (a [fürt konfigurációs útmutatójának dokumentációja](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_networks.html#gui-cluster-networks)).

  A tartományban lévő **IP-címek számának** értékét a rendszer automatikusan kiszámítja és megjeleníti.

* **Nem mgmt hálózati maszk (nem kötelező)** – a fürt hálózati maszkjának megadása.

  A rendszer automatikusan a felügyeleti hálózathoz megadott hálózati maszk értékét javasolja. szükség esetén módosítsa.

* **Fürt útválasztója (nem kötelező)** – Itt adhatja meg a fürt által használt alapértelmezett átjáró-címeket.

  A rendszer automatikusan a felügyeleti hálózathoz megadott átjáró-címeket javasolja.

* **Fürt VLAN-címkéje (nem kötelező)** – ha a fürt VLAN-címkéket használ, adja meg a fürthöz tartozó címkét.

* **Nem mgmt MTU (nem kötelező)** – ha szükséges, állítsa be a fürt számára a maximális átviteli egység (MTU) értékét.

### <a name="configure-cluster-dns-and-ntp"></a>A fürt DNS-és NTP-kiszolgálójának konfigurálása

A **fürt** szakasz alatt a DNS-és NTP-kiszolgálók megadására, valamint a hivatkozások összesítésének engedélyezésére szolgáló mezők találhatók. Ezek a beállítások a fürt által használt összes hálózatra érvényesek.

![A DNS-/NTP-konfiguráció szakaszának részletes adatai, három mező a DNS-kiszolgálók, a DNS-tartomány és a DNS-keresés mezőihez, három mező az NTP-kiszolgálókhoz, valamint egy legördülő menü a hivatkozás-összesítési beállításokhoz](media/fxt-cluster-create/dns-ntp-filled.png)

* **DNS-kiszolgáló (k)** – adja meg egy vagy több DNS-kiszolgáló IP-címét.

  A DNS használata ajánlott minden fürthöz, és kötelező, ha SMB-t, AD-t vagy Kerberost szeretne használni.
  
  Az optimális teljesítmény érdekében konfigurálja a fürt DNS-kiszolgálóját a ciklikus időszeleteléses terheléselosztáshoz a [DNS konfigurálása az Azure FXT Edge Filer-fürthöz](fxt-configure-network.md#configure-dns-for-load-balancing)című témakörben leírtak szerint.

* **DNS-tartomány** – adja meg a fürt által használt hálózati tartománynevet.

* **DNS-keresés** – nem kötelezően megadhat további tartományneveket, amelyeket a rendszernek a DNS-lekérdezések feloldására kell keresnie. Legfeljebb hat tartománynevet adhat hozzá szóközzel elválasztva.

* **NTP-kiszolgáló (k)** – adjon meg egy vagy három Network Time Protocol (NTP) kiszolgálót a megadott mezőkben. Használhat állomásnévket vagy IP-címeket.

* **Hivatkozás összesítése** – a hivatkozás összesítése lehetővé teszi, hogy a fürt FXT csomópontjain lévő Ethernet-portok különböző típusú forgalmat kezeljenek. További információért olvassa el a [hivatkozás összesítése](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_general_setup.html#link-aggregation) című részt a fürtkonfiguráció útmutatójában.

### <a name="click-the-create-button"></a>Kattintson a Létrehozás gombra

Az űrlap összes szükséges beállításának megadása után kattintson a **fürt létrehozása** gombra.

![a befejezett űrlap alja a jobb alsó sarokban lévő Create (létrehozás) gomb fölé](media/fxt-cluster-create/create-cluster.png)

A rendszer egy üzenetet jelenít meg a fürt létrehozásakor.

![fürtkonfiguráció állapotjelző üzenet a böngészőben: "a FXT csomópont most létrehozza a fürtöt. Ez több percet is igénybe vehet. A fürt létrehozása után látogasson el erre a hivatkozásra a konfiguráció befejezéséhez. " hivatkozással a "keresse meg ezt a hivatkozást"](media/fxt-cluster-create/creating-message.png)

Néhány pillanat elteltével az üzenetben található hivatkozásra kattintva nyissa meg a fürtöt a Vezérlőpulton. (Ez a hivatkozás a **felügyeleti IP**-címen megadott IP-címet veszi át.) A hivatkozás a Létrehozás gombra kattintás után 15 másodperctől egy percet vesz igénybe. Ha a webes felület nem töltődik be, várjon néhány másodpercet, majd kattintson újra a hivatkozásra.

A fürt létrehozása egy vagy több percet vesz igénybe, de a folyamat közben bejelentkezhet a Vezérlőpultba. Normális, hogy a Vezérlőpult irányítópult lapján megjelenjenek a figyelmeztetések, amíg a fürt létrehozási folyamata be nem fejeződik.

## <a name="open-the-settings-pages"></a>A beállítások lapok megnyitása

A fürt létrehozása után testre kell szabnia a hálózat és a munkafolyamat konfigurációját.

Az új fürt beállításához használja a Vezérlőpult webes felületét. Kövesse a fürt létrehozási állapota képernyő hivatkozását, vagy keresse meg a fürtön beállított felügyeleti IP-címet.

Jelentkezzen be a webes felületen a `admin` fürt létrehozásakor beállított felhasználónévvel és jelszóval.

![a Vezérlőpult bejelentkezési mezőinek megjelenítéséhez használt webböngésző](media/fxt-cluster-create/admin-login.png)

Megnyílik a Vezérlőpult, és megjelenik az **irányítópult** lapja. A fürt létrehozásakor a rendszer minden figyelmeztető üzenetet töröl a kijelzőből.

A fürt konfigurálásához kattintson a **Beállítások** fülre.

A **Beállítások** lapon a bal oldali oldalsáv a konfigurációs lapok menüjét jeleníti meg. A lapok kategória szerint vannak rendezve. Az egyes lapok kibontásához vagy elrejtéséhez kattintson a kategória neve tetején található + vagy-Control elemre.

![A Vezérlőpult beállítások lapja (böngészőben) a fürttel > általános telepítési oldal betöltve](media/fxt-cluster-create/settings-tab-populated.png)

## <a name="cluster-setup-steps"></a>A fürt beállításának lépései

A folyamat ezen pontján a fürt már létezik, de csak egyetlen csomóponttal rendelkezik, és nincs az ügyfél felé irányuló IP-cím, és nincs háttérbeli tárterület. További telepítési lépések szükségesek ahhoz, hogy egy újonnan létrehozott fürtről egy olyan gyorsítótárrendszer álljon, amely készen áll a munkafolyamat kezelésére.

### <a name="required-configuration"></a>Szükséges konfiguráció

Ezek a lépések a legtöbb vagy az összes fürthöz szükségesek.

* Csomópontok hozzáadása a fürthöz

  A három csomópont a standard, de számos üzemi fürt legfeljebb 24 csomóponttal rendelkezik.

  A [fürtcsomópontok hozzáadása](fxt-add-nodes.md) című cikkből megtudhatja, hogyan adhat hozzá további Azure FXT Edge Filer-egységeket a fürthöz, és hogyan engedélyezheti a magas rendelkezésre állást.

* Háttérbeli tároló meghatározása

  Adja hozzá a fürt által használt összes háttér-tárolási rendszer *alapvető Filer* -definícióit. További információért olvassa el a [háttérbeli tároló hozzáadása és a virtuális névtér konfigurálása](fxt-add-storage.md#about-back-end-storage) című témakört.

* Az ügyfél-hozzáférés és a virtuális névtér beállítása

  Hozzon létre legalább egy virtuális kiszolgálót (VServer), és rendeljen hozzá egy IP-címtartományt a használni kívánt ügyfélszámítógépekhez. A fürt névterét (más néven globális névteret vagy GNS) is konfigurálnia kell, amely lehetővé teszi a háttérbeli tárolók virtuális elérési utakra történő leképezését. A fürt névterében az ügyfelek konzisztens és elérhető fájlrendszer-struktúrát biztosítanak, még akkor is, ha átváltják a háttérbeli adathordozót. A névtér emellett felhasználóbarát virtuális tárolási hierarchiát is biztosíthat az Azure Blob-tárolók vagy más támogatott felhőalapú objektumok tárolásához.

  A részletekért olvassa el [a névtér konfigurálása](fxt-add-storage.md#configure-the-namespace) című leírást. Ez a lépés a következőket tartalmazza:
  * Vservers létrehozása
  * Csomópontok beállítása az ügyfél hálózati nézete és a háttérbeli tároló között
  * Az egyes VServer által kiszolgált ügyfél IP-címeinek meghatározása

  > [!Note]
  > A fürt GNS beállításának megkezdése előtt jelentős tervezés javasolt. Segítségért olvassa el a [globális névtér használata](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) és a VServers-csoportok [létrehozása és használata](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers) című szakaszt a fürt konfigurációs útmutatójában.

* [Hálózati beállítások módosítása](fxt-configure-network.md)

  Az új fürthöz több hálózattal kapcsolatos beállításnak kell szerepelnie, amelyeket ellenőrizni kell vagy testre kell szabni. Az alábbi elemekkel kapcsolatos részletekért olvassa el a [hálózati beállítások módosítása](fxt-configure-network.md) elemet:

  * DNS-és NTP-konfiguráció ellenőrzése
  * Címtárszolgáltatások konfigurálása, ha szükséges
  * VLAN-ok beállítása
  * Proxykiszolgálók konfigurálása
  * IP-címek hozzáadása a fürt hálózatához
  * Titkosítási tanúsítványok tárolása

* [Támogatás figyelésének beállítása](#enable-support)

  El kell fogadnia a konfigurációs eszköz adatvédelmi szabályzatát, és egyszerre kell konfigurálnia a támogatási feltöltési beállításokat.

  A fürt automatikusan feltöltheti a fürttel kapcsolatos hibaelhárítási adatokat, beleértve a statisztikát és a hibakeresési fájlokat is. Ezek a feltöltések lehetővé teszik a Microsoft ügyfél-és támogatási szolgálata számára a lehető legjobb szolgáltatást. Testreszabhatja a figyelt funkciót, és opcionálisan engedélyezheti a proaktív támogatást és a távoli hibaelhárítási szolgáltatást.  

### <a name="optional-configuration"></a>Választható konfiguráció

Ezek a lépések nem szükségesek az összes fürthöz. Ezek bizonyos típusú munkafolyamatokhoz vagy bizonyos fürtözött felügyeleti stílusokhoz szükségesek.

* Csomópont-beállítások testreszabása

  Megadhatja a csomópontok nevét, és konfigurálhatja a csomópontok IPMI-portjait a fürtre kiterjedő szinten, vagy egyénileg. Ha ezeket a beállításokat a csomópontok fürthöz adása előtt konfigurálja, az új csomópontok automatikusan kiválaszthatják a beállításokat a csatlakozáskor. A beállításokat a régi fürt létrehozása dokumentum a csomópont- [beállítások testreszabása](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_node.html)című dokumentumban találja.

  > [!TIP]
  > A termékhez tartozó dokumentáció még nem érhető el a Microsoft Azure dokumentációs webhelyen. A [fürt konfigurációs útmutatójának](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html) és a [fürt létrehozási útmutatójának](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/create_index.html) örökölt verziójára mutató hivatkozások egy külön githubon üzemeltetett webhelyre mutatnak.

* SMB konfigurálása

  Ha engedélyezni szeretné az SMB-hozzáférést a fürthöz, valamint az NFS-t, konfigurálnia kell az SMB-t, és be kell kapcsolni. Az SMB (más néven CIFS) általában a Microsoft Windows-ügyfelek támogatására szolgál.

  Az SMB megtervezése és konfigurálása több, mint a Vezérlőpult néhány gombjának kiválasztását jelenti. A rendszer követelményeitől függően az SMB befolyásolhatja az alapvető Filer-k definiálásának módját, a létrehozott vservers, valamint a csomópontok és a névtér, a hozzáférési engedélyek és az egyéb beállítások konfigurálásának módját.

  További információért olvassa el az [SMB-hozzáférés konfigurálása](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/smb_overview.html) című fürt konfigurációs útmutatóját.

* További licencek telepítése

  Ha az Azure Blobtól eltérő felhőalapú tárolót szeretne használni, telepítenie kell egy további szolgáltatás-licencet. A FlashCloud<sup>TM</sup> -licenc megvásárlásával kapcsolatos részletekért forduljon a Microsoft képviselőjéhez. A részleteket a [háttérbeli tárolás hozzáadása és a virtuális névtér konfigurálása](fxt-add-storage.md#about-back-end-storage)című részben ismertetjük.

### <a name="enable-support"></a>Támogatás engedélyezése

Az Azure FXT Edge Filer-fürt automatikusan feltöltheti a fürt támogatási adatait. Ezek a feltöltések lehetővé teszik a személyzet számára a lehető legjobb kiszolgálást.

A támogatási feltöltések beállításához kövesse az alábbi lépéseket.

1. Navigáljon a **fürt**  >  **támogatási** beállításai lapra. Fogadja el az adatvédelmi szabályzatot.

   ![Képernyőfelvétel: a Vezérlőpult és az előugró ablak, amely a Confirm (megerősítés) gombbal fogadja el az adatvédelmi szabályzatot](media/fxt-cluster-create/fxt-privacy-policy.png)

1. Kattintson az **ügyféladatok** bal oldalán található háromszögre a szakasz kibontásához.
1. Kattintson a **feltöltési adatok újraérvényesítése** gombra.
1. Adja meg a fürt támogatási nevét az **egyedi fürt neve** alatt – ügyeljen arra, hogy a fürt egyedi módon azonosítható legyen a munkatársak támogatásához.
1. A **statisztikák figyelésére**, az **általános adatok feltöltésére** és az **Összeomlási adatok feltöltésére** vonatkozó jelölőnégyzeteket itt találja.
1. Kattintson a **Submit (Küldés**) gombra.  

   ![A támogatási beállítások oldalának Completed Customer info szakaszt tartalmazó képernyőképe](media/fxt-cluster-create/fxt-support-info.png)

1. Kattintson a **biztonságos proaktív támogatás (SPS)** bal oldalán található háromszögre a szakasz kibontásához.
1. Jelölje be az **SPS-hivatkozás engedélyezése** jelölőnégyzetet.
1. Kattintson a **Submit (Küldés**) gombra.

   ![A támogatási beállítások lapon található, biztonságos proaktív támogatásról szóló szakaszt tartalmazó képernyőkép](media/fxt-cluster-create/fxt-support-sps.png)

## <a name="next-steps"></a>Következő lépések

Miután létrehozta az alapszintű fürtöt, és elfogadta az adatvédelmi szabályzatot, adja hozzá a fürt többi csomópontját.

> [!div class="nextstepaction"]
> [Fürtcsomópontok hozzáadása](fxt-add-nodes.md)
