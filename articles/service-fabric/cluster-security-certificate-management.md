---
title: Tanúsítványkezelő egy Service Fabric-fürtben
description: További információ az X. 509 tanúsítvánnyal védett Service Fabric-fürtök tanúsítványainak kezeléséről.
ms.topic: conceptual
ms.date: 04/10/2020
ms.custom: sfrev
ms.openlocfilehash: a8a7e8954f3c9d5b54c2e1ed9caa330ef92d4512
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100099506"
---
# <a name="certificate-management-in-service-fabric-clusters"></a>Tanúsítványkezelő Service Fabric-fürtökben

Ez a cikk az Azure Service Fabric-fürtökkel folytatott kommunikáció biztonságossá tételéhez használt tanúsítványok felügyeleti szempontjait ismerteti, és kiegészíti a [Service Fabric-fürt biztonságával](service-fabric-cluster-security.md) , valamint az [X. 509 tanúsítványalapú hitelesítéssel](cluster-security-certificates.md)kapcsolatos bemutatót Service Fabric. Feltételezzük, hogy az olvasó ismeri az alapvető biztonsági fogalmakat, valamint azokat a vezérlőket is, amelyeket a Service Fabric a fürt biztonságának konfigurálására tesz elérhetővé.  

A cím alá tartozó szempontok:

* Pontosan mi a "tanúsítványkezelő"?
* A Tanúsítványkezelőben érintett szerepkörök és entitások
* Egy tanúsítvány útja
* Részletes példa
* Hibaelhárítás és gyakran ismételt kérdések

De az első – a jogi nyilatkozat: ebben a cikkben a gyakorlati példákkal is foglalkozunk, amelyekhez szükség van a szolgáltatások, technológiák és egyéb funkciókra. Mivel a célközönség jelentős része a Microsoft belső, a szolgáltatásokra, technológiákra és a Microsoft Azurera jellemző termékekre fogunk hivatkozni. Kérjük, forduljon a megjegyzések szakaszhoz, ahol pontosításokat vagy útmutatást talál, ahol a Microsoft-specifikus adatok nem érvényesek az adott esetben.

## <a name="defining-certificate-management"></a>A Tanúsítványkezelő meghatározása
Ahogy láttuk a [Companion-cikkben](cluster-security-certificates.md), a tanúsítvány egy kriptográfiai objektum, amely lényegében egy aszimmetrikus kulcspár kötése, amely az általa reprezentált entitást leíró attribútumokkal rendelkezik. Azonban ez egy "romlandó" objektum is, abban az esetben, ha az élettartama véges, és feltehetően veszélyt okoz a véletlen közzétételre, vagy egy sikeres támadás miatt a tanúsítvány használhatatlanná válik biztonsági szempontból. Ez azt jelenti, hogy a tanúsítványokat – akár rutinszerűen, akár biztonsági incidensekre adott válaszként – módosítani kell. A felügyelet egy másik aspektusa (és egy teljes témakör saját maga) a tanúsítvány titkos kulcsainak védelme, illetve a tanúsítványok beszerzése és kiépítés során érintett entitások identitásának védelmét biztosító titkos kulcsok védelme. A tanúsítványok beszerzéséhez és biztonságos (és biztonságos) továbbításához használt folyamatokról és eljárásokról a "tanúsítványkezelő" szükséges. Néhány felügyeleti művelet – például a beléptetés, a házirend-beállítás és az engedélyezési vezérlők – a jelen cikk hatókörén kívül esik. Még mások is – például a kiépítés, a megújítás, az ismételt beírás vagy a visszavonás – csak a Service Fabrichoz kapcsolódnak. Ennek ellenére bizonyos mértékig ezeket a műveleteket fogjuk kezelni, mivel ezek a műveletek segíthetnek az egyik fürt megfelelő biztonságossá tételében. 

A cél az, hogy a lehető legnagyobb mértékben automatizálja a Tanúsítványkezelőt a fürt folyamatos rendelkezésre állásának biztosítása érdekében, és biztonsági garanciákat biztosítson, mivel a folyamat a felhasználó számára ingyenes. Ez a cél jelenleg az Azure Service Fabric-fürtökben érhető el. a cikk hátralévő részében először a Tanúsítványkezelőt kell kibontania, később pedig az automatikus rollover engedélyezésére koncentrál.

A hatókör témakörei a következők:
  - a tulajdonos és a platform közötti kiosztások elkülönítésével kapcsolatos feltételezések a tanúsítványok kezelése kontextusban
  - a tanúsítványok hosszú folyamata a kibocsátástól a felhasználásig
  - tanúsítvány elforgatása – miért, hogyan és Mikor
  - Mi lehet a probléma?

Ennek a cikknek a hatóköre olyan aspektus, mint például a tartománynevek biztonságossá tétele vagy kezelése, tanúsítványok beléptetése vagy engedélyezési vezérlők beállítása a tanúsítvány kiállításának érvénybe léptetéséhez. Tekintse meg az Ön kedvenc nyilvános kulcsokra épülő infrastruktúrája (PKI) regisztrációs szolgáltatóját (RA). Microsoft – belső fogyasztók: forduljon az Azure Security szolgáltatáshoz.

## <a name="roles-and-entities-involved-in-certificate-management"></a>A Tanúsítványkezelőben érintett szerepkörök és entitások
A Service Fabric-fürt biztonsági megközelítése "a fürt tulajdonosa kijelenti, Service Fabric futtatókörnyezet kényszeríti azt". Ez azt jelenti, hogy a fürt működésében részt vevő identitások egyik tanúsítványa, kulcsa vagy más hitelesítő adatai a szolgáltatásból származnak. mindegyiket a fürt tulajdonosa deklarálja. Emellett a fürt tulajdonosa is felelős a tanúsítványok fürtbe való kiszámításához, szükség szerint megújítva és a tanúsítványok biztonságának biztosításához. Pontosabban a fürt tulajdonosának biztosítania kell a következőket:
  - a fürt jegyzékfájljának NodeType szakaszában deklarált tanúsítványok a [megjelenítési szabályoknak](cluster-security-certificates.md#presentation-rules) megfelelően megtalálhatók a típus minden egyes csomópontján.
  - a fent deklarált tanúsítványok a megfelelő titkos kulcsokkal együtt települnek.
  - a megjelenítési szabályokban deklarált tanúsítványoknak át kell adni az [érvényesítési szabályokat](cluster-security-certificates.md#validation-rules) . 

Service Fabric a saját részében a következő feladatokat feltételezi:
  - a deklarációhoz tartozó tanúsítványok megkeresése/keresése a fürt definíciójában  
  - a megfelelő titkos kulcsokhoz való hozzáférés biztosítása Service Fabric által vezérelt entitások számára "igény" alapon
  - a tanúsítványok szigorú ellenőrzése a bevált biztonsági eljárások és a fürt definíciója szerint
  - riasztások előléptetése a tanúsítványok közelgő lejárta után, vagy a tanúsítvány-ellenőrzés alapvető lépéseinek végrehajtásához szükséges hibák
  - annak ellenőrzése (bizonyos fokig), hogy a fürt definíciójának tanúsítványokkal kapcsolatos szempontjait a gazdagépek mögöttes konfigurációja teljesíti 

Ebből következik, hogy a tanúsítványkezelői terhek (aktív műveletek) kizárólag a fürt tulajdonosára tartoznak. A következő részekben részletesebben szemügyre vesszük az egyes felügyeleti műveleteket, valamint a rendelkezésre álló mechanizmusokat és azok hatását a fürtre.

## <a name="the-journey-of-a-certificate"></a>Egy tanúsítvány útja
Tegyük fel, hogy gyorsan újra felkeresjük a tanúsítvány előrehaladását a kibocsátásról a felhasználásra egy Service Fabric-fürt kontextusában:

  1. A tartomány tulajdonosa a PKI-t egy olyan tartományhoz vagy tulajdonoshoz regisztrálja, amelyet a következő tanúsítványokhoz szeretne rendelni: a tanúsítványok megalkotják az említett tartomány vagy tárgy tulajdonjogának igazolását.
  2. A tartomány tulajdonosa azt is meghatározza, hogy az RA a jogosult kérők azonosítói – az entitások, amelyek jogosultak a tanúsítványok beléptetésének igénylésére a megadott tartományba vagy tárgyba; Microsoft Azure az alapértelmezett identitás-szolgáltató a Azure Active Directory, és a megfelelő HRE-identitás (vagy biztonsági csoportok használatával) a jogosult kérelmezőket jelölik.
  3. Egy felhatalmazott kérelmező ezután egy titkos felügyeleti szolgáltatáson keresztül regisztrálja a tanúsítványokat. a Microsoft Azureban az SMS of Choice Azure Key Vault (AKV), amely biztonságosan tárolja és engedélyezi a titkok/tanúsítványok engedélyezését az engedélyezett entitások alapján. A AKV megújítja/újra felhasználja a tanúsítványt a kapcsolódó tanúsítvány-házirendben konfiguráltnak megfelelően. (A AKV a HRE-t használja identitás-szolgáltatóként.)
  4. Egy hivatalos Retriever – amelyet "kiépítési ügynöknek" nevezünk – lekéri a tanúsítványt a titkos kulcsával együtt, a tárolóból, és telepíti a fürtöt futtató gépekre.
  5. A Service Fabric szolgáltatás (amely az egyes csomópontokon fut) hozzáférést biztosít a tanúsítványhoz Service Fabric entitások számára. ezeket a helyi csoportok jelölik, és a ServiceFabricAdministrators és a ServiceFabricAllowedUsers között oszlik meg.
  6. Az Service Fabric futtatókörnyezet hozzáfér, és a tanúsítványt használja az összevonás létrehozásához, vagy a hitelesített ügyfelektől érkező bejövő kérések hitelesítéséhez
  7. A kiépítési ügynök figyeli a tár tanúsítványát, és a megújítás észlelése után elindítja a kiépítési folyamatot. ezt követően a fürt tulajdonosa frissíti a fürt definícióját, ha szükséges, hogy jelezze a tanúsítvány átadásának szándékát.
  8. A kiépítési ügynök vagy a fürt tulajdonosa is felelős a nem használt tanúsítványok törlésének és törlésének
  
A fentiekben ismertetett első két lépés nagyrészt nem kapcsolódó. az egyetlen kapcsolat, hogy a tanúsítvány tulajdonos köznapi neve a fürt definíciójában deklarált DNS-név.

Ezek a lépések az alábbi ábrán láthatók: Figyelje meg, hogy az ujjlenyomattal és a köznapi névvel deklarált tanúsítványok közötti különbségek kiépítve.

*1\. ábra* Az ujjlenyomattal deklarált tanúsítványok kiállítása és kiépítési folyamata.
![Az ujjlenyomattal deklarált tanúsítványok kiépítés][Image1]

*2. ábra.* A tanúsítvány kiállítása és kiépítési folyamata a tulajdonos köznapi neve szerint deklarálva.
![A tulajdonos köznapi neve által deklarált tanúsítványok kiépítési feltételei][Image2]

### <a name="certificate-enrollment"></a>Tanúsítványigénylés
Ez a témakör részletesen szerepel a Key Vault [dokumentációjában](../key-vault/certificates/create-certificate.md); a folytonosság és a könnyebb referenciák áttekintését itt olvashatja. Ha továbbra is az Azure-t használja kontextusként, és a Azure Key Vaultt a titkos felügyeleti szolgáltatásként használja, akkor egy jogosult tanúsítvány-kérelmezőnek legalább tanúsítványkezelő engedélyekkel kell rendelkeznie a tárolóban, amelyet a tár tulajdonosa biztosít; a kérelmező ezután a következőképpen regisztrálja a tanúsítványokat:
    - létrehoz egy Azure Key Vault (AKV) tanúsítvány-szabályzatot, amely meghatározza a tanúsítvány tartományát/tárgyát, a kívánt kiállítót, a kulcs típusát és hosszát, a használandó kulcshasználat és egyebeket. részletekért tekintse [meg Azure Key Vault tanúsítványait](../key-vault/certificates/certificate-scenarios.md) . 
    - létrehoz egy tanúsítványt ugyanabban a tárolóban a fent megadott házirenddel; Ez viszont létrehoz egy kulcspárt tároló-objektumokként, a titkos kulccsal aláírt tanúsítvány-aláírási kérelmet, amelyet aztán a kijelölt kiállítónak továbbít az aláíráshoz.
    - Miután a kiállító (hitelesítésszolgáltató) válaszol az aláírt tanúsítvánnyal, az eredmény bekerül a tárolóba, és a tanúsítvány elérhető a következő műveletekhez:
      - a (z) {vaultUri}/Certificates/{Name} alatt: a tanúsítvány, beleértve a nyilvános kulcsot és a metaadatokat
      - a (z) {vaultUri}/Keys/{Name}: a tanúsítvány titkos kulcsa, amely titkosítási műveletekhez érhető el (becsomagolás/kicsomagolás, aláírás/ellenőrzés)
      - a (z) {vaultUri}/Secrets/{Name}: a titkos kulcsát tartalmazó tanúsítvány, amely nem védett pfx-vagy PEM-fájlként letölthető.  
    Ne felejtse el, hogy a tároló tanúsítványa valójában a tanúsítvány példányainak kronológiai sora, egy szabályzat megosztása. A tanúsítványok verziói a szabályzat élettartama és megújítási attribútumai alapján jönnek létre. Erősen ajánlott, hogy a tár tanúsítványai ne osszanak meg tárgyakat vagy tartományokat/DNS-neveket; zavaró lehet a fürtben, hogy a különböző tár tanúsítványokból származó tanúsítványokat hozzon létre, azonos témákkal, de lényegében más attribútumokkal, például a kibocsátóval, a kulcshasználat stb.

Ezen a ponton a tárolóban létezik egy tanúsítvány, amely készen áll a felhasználásra. Ettől kezdve:

### <a name="certificate-provisioning"></a>Tanúsítvány kiépítés
Említettük a "kiépítési ügynököt", amely az a entitás, amely a saját titkos kulcsával együtt lekéri a tanúsítványt a tárolóból, és telepíti azt a fürt mindegyik gazdagépére. (Ne felejtse el, hogy Service Fabric nem hoz létre tanúsítványokat.) Kontextusban a fürtöt Azure-beli virtuális gépek és/vagy virtuálisgép-méretezési csoportok gyűjteménye fogja üzemeltetni. Az Azure-ban a tanúsítványnak a tárolóból egy virtuális gépre/VMSS való kiépítését a következő mechanizmusokkal lehet megvalósítani – feltéve, hogy a fentiek szerint a kiépítési ügynök korábban "Get" engedélyt kapott a tár tulajdonosának: 
  - ad-hoc: az operátor lekéri a tanúsítványt a tárolóból (pfx/PKCS #12 vagy PEM), és telepíti az egyes csomópontokon.
  - az üzembe helyezés során a "Secret" virtuálisgép-méretezési csoportként: a számítási szolgáltatás az első fél identitását használja a kezelő nevében, a tanúsítványt egy sablon – üzembe helyezést engedélyező tárolóból, és telepíti a virtuálisgép-méretezési csoport minden csomópontján ([például így](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates)van). vegye figyelembe, hogy ez csak a verziószámmal ellátott titkos kódok kiépítés teszi lehetővé.
  - a [Key Vault VM-bővítmény](../virtual-machines/extensions/key-vault-windows.md)használata; Ez lehetővé teszi a tanúsítványok verzió nélküli deklarációkkal történő kihelyezését a megfigyelt tanúsítványok rendszeres frissítésével. Ebben az esetben a virtuális gépnek és a VMSS rendelkeznie kell egy [felügyelt identitással](../virtual-machines/security-policy.md#managed-identities-for-azure-resources), egy olyan identitással, amely hozzáférést kapott a megfigyelt tanúsítványokat tartalmazó tároló (k) hoz.

Az ad-hoc mechanizmus több okból nem ajánlott, a biztonságtól a rendelkezésre állásig, és a továbbiakban nem lesz tárgyalva. részletekért tekintse meg a [virtuális gépek méretezési csoportjaiban található tanúsítványokat](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates).

A VMSS-/Compute-based kiépítés a biztonság és a rendelkezésre állás előnyeit mutatja be, de korlátozásokat is tartalmaz. Ehhez szükséges, hogy a tanúsítványokat verzióval ellátott titokként állapítsa meg, így csak az ujjlenyomattal deklarált tanúsítványokkal védett fürtök számára megfelelő. Ezzel szemben a Key Vault virtuálisgép-bővítményekre épülő kiépítés mindig telepíti a megfigyelt tanúsítványok legújabb verzióját, ami csak a tulajdonos köznapi neve által deklarált tanúsítványokkal védett fürtök esetében alkalmas. A kiemeléshez ne használjon autofrissítéses kiépítési mechanizmust (például a KVVM-bővítményt) a példány által deklarált tanúsítványok esetében (azaz ujjlenyomat alapján) – a rendelkezésre állás elvesztésének kockázata jelentős.

Előfordulhat, hogy más létesítési mechanizmusok is léteznek; a fentiek jelenleg elfogadva vannak az Azure Service Fabric-fürtökhöz.

### <a name="certificate-consumption-and-monitoring"></a>A tanúsítvány felhasználása és figyelése
Ahogy azt korábban említettük, a Service Fabric futtatókörnyezet feladata a fürt definíciójában deklarált tanúsítványok megkeresése és használata. A [tanúsítványalapú hitelesítéssel](cluster-security-certificates.md) kapcsolatos cikk részletesen ismerteti, hogyan valósítja meg a Service Fabric a bemutató és az érvényesítési szabályok végrehajtását, és itt nem lesz újra áttekintve. A hozzáférést és az engedélyek megadását, valamint a figyelést fogjuk megtekinteni.

Ne felejtse el, hogy a Service Fabricban lévő tanúsítványokat számos célra használják, az összevonási réteg kölcsönös hitelesítése és a felügyeleti végpontok TLS-hitelesítése céljából. Ehhez különböző összetevőkre vagy rendszerszolgáltatásokra van szükség, hogy hozzáférjenek a tanúsítvány titkos kulcsához. A Service Fabric futtatókörnyezet rendszeresen megvizsgálja a tanúsítványtárolót, és minden ismert megjelenítési szabályhoz egyezést keres. az összes egyező tanúsítvány esetében a megfelelő titkos kulcs található, és a tulajdonosi hozzáférés-vezérlési lista frissül, hogy tartalmazza az engedélyeket – általában az olvasási és végrehajtási jogosultságokat a megfelelő identitáshoz, amely megköveteli őket. (Ez a folyamat nem hivatalosan "hozzáférés" néven ismert.) A folyamat egy 1 perces ütemben fut, és az "alkalmazás" tanúsítványokra is vonatkozik, például a beállítások titkosítására vagy a végponti tanúsítványok használatára. A hozzáférés követi a bemutató szabályait, ezért fontos szem előtt tartani, hogy az ujjlenyomat által deklarált tanúsítványok, amelyek nem lesznek elérhetők az azt követő fürtkonfiguráció frissítése nélkül.    

### <a name="certificate-rotation"></a>Tanúsítvány elforgatása
Megjegyzés: az IETF [RFC 3647](https://tools.ietf.org/html/rfc3647) hivatalosan is meghatározza a [megújítást](https://tools.ietf.org/html/rfc3647#section-4.4.6) , mivel a tanúsítvány kiadása ugyanazokkal az attribútumokkal történik, mint a lecserélt tanúsítvány – a kibocsátó, a tulajdonos nyilvános kulcsa és információi megmaradnak [, és egy](https://tools.ietf.org/html/rfc3647#section-4.4.7) új kulcspárt tartalmazó tanúsítvány kibocsátása esetén nem korlátozható, hogy a kibocsátó megváltozhat-e. Mivel a különbségtétel fontos lehet (vegye figyelembe a tulajdonos által a kiállítói rögzítéssel rendelkező köznapi név által deklarált tanúsítványok esetét), a "rotációs" semleges időszakot is választhatja, hogy mindkét forgatókönyvre vonatkozzon. (Ne feledje, hogy ha az informálisan használatban van, a "megújítás" kifejezés az újbóli beírását jelenti.) 

Korábban láttuk, hogy Azure Key Vault támogatja az automatikus tanúsítvány-elforgatást: a tanúsítvány-hozzárendelési házirend határozza meg az időpontot, akár nappal a lejárat előtt, akár a teljes élettartam százalékát, amikor a tanúsítvány a tárolóban van elforgatva. A kiépítési ügynököt ezen időpont után kell meghívni, és a most már korábbi tanúsítvány lejárta előtt el kell juttatni az új tanúsítványt a fürt összes csomópontjára. Service Fabric segít az állapot-figyelmeztetések növelésében, ha a tanúsítvány lejárati dátuma (és amely jelenleg a fürtben van használatban van) az előre meghatározott intervallumnál hamarabb következik be. Egy automatikus kiépítési ügynök (azaz a kulcstartó virtuálisgép-bővítménye), amely a tár tanúsítványának megfigyelésére van konfigurálva, rendszeres időközönként lekérdezi a tárolót, észleli az elforgatást, és lekéri és telepíti az új tanúsítványt. A kiépítés a VM/VMSS "Secrets" szolgáltatáson keresztül történik, ha egy jogosult kezelőnek frissítenie kell a virtuális gépet/VMSS az új tanúsítványnak megfelelő, verzióval ellátott kulcstartó URI azonosítóval.

Mindkét esetben az elforgatott tanúsítvány már az összes csomópontra kiépítve van, és ismertetjük a mechanizmust, Service Fabric alkalmazva a Forgások észlelésére; Nézzük meg, hogy mi történik a következő lépéssel – feltételezve, hogy a tulajdonos köznapi neve szerint deklarált, a fürt tanúsítványára alkalmazott rotációt
  - az új, valamint a fürtön belüli kapcsolatok esetében a Service Fabric futtatókörnyezet megkeresi és kiválasztja a legutóbb kiállított megfelelő tanúsítványt (a "NotBefore" tulajdonság legnagyobb értéke). Vegye figyelembe, hogy ez a Service Fabric futtatókörnyezet korábbi verzióiról vált változást.
  - a meglévő kapcsolatok továbbra is életben maradnak/megengedettek a természet lejárta után, vagy más módon leállnak; egy belső kezelő értesítést kap arról, hogy létezik egy új egyezés

> [!NOTE] 
> A 7.2.445 (7,2 CU4) verzió előtt Service Fabric kiválasztotta a legtávolabbi lejáró tanúsítványt (a legtávolabbi "nem tAfter" tulajdonsággal rendelkező tanúsítványt).

Ez a következő fontos észrevételeket fordítja le:
  - A megújítási tanúsítvány figyelmen kívül hagyható, ha a lejárati dátuma korábbi, mint a jelenleg használt tanúsítvány.
  - A fürt vagy az üzemeltetett alkalmazások rendelkezésre állása elsőbbséget élvez a tanúsítvány elforgatására szolgáló irányelv felett; a fürt végül az új tanúsítványhoz közeledik, de az időzítési garanciák nélkül. Ennek a következőket kell tennie:
  - Előfordulhat, hogy a megfigyelő azonnal nem nyilvánvaló, hogy az elforgatott tanúsítvány teljesen lecserélte az elődjét; az egyetlen módszer annak biztosítására, hogy a a gazdagépek újraindításához szükséges (a fürt tanúsítványainak esetében). Vegye figyelembe, hogy nem elegendő a Service Fabric csomópontok újraindításához, mert a kernel módú összetevők, amelyek a fürt címbérleti kapcsolatainak formáját nem érintik. Azt is vegye figyelembe, hogy a virtuális gép/VMSS újraindítása a rendelkezésre állás átmeneti elvesztését eredményezheti. (Az alkalmazás tanúsítványainak esetében elegendő a megfelelő alkalmazás-példányok újraindítása.)
  - Az érvényesítési szabályoknak nem megfelelő, újra kulccsal rendelkező tanúsítvány bevezetése gyakorlatilag megszakíthatja a fürtöt. Ez a leggyakoribb példa egy váratlan kiállító esetére: a fürtözött tanúsítványokat a tulajdonosi rögzítéssel rendelkező köznapi név deklarálja, de az elforgatott tanúsítványt egy új/nem deklarált kiállító bocsátotta ki.     

### <a name="certificate-cleanup"></a>Tanúsítvány karbantartása
Jelenleg az Azure-ban nincsenek a tanúsítványok explicit eltávolítására szolgáló céltartalékok. Gyakran nem triviális feladat annak megállapítására, hogy egy adott tanúsítvány használatban van-e egy adott időpontban. Ez nehezebb az alkalmazások tanúsítványainak esetében, mint a fürt tanúsítványainak esetében. Service Fabric magát, nem pedig a kiépítési ügynököt, nem töröl semmilyen körülmények között a felhasználó által deklarált tanúsítványt. A standard létesítési mechanizmusok esetében:
  - A virtuális gép/VMSS titkos kulcsként deklarált tanúsítványokat akkor kell kiépíteni, ha a virtuális gép/VMSS definíciójában hivatkoznak rájuk, és a tárolóból lekérhető (a tár titkos vagy tanúsítványának törlése sikertelen lesz a virtuális gép/VMSS központi telepítések esetén)
  - A kulcstároló virtuálisgép-bővítményével kiépített tanúsítványok korábbi verziói esetleg nem jelennek meg a VM/VMSS csomóponton. Az ügynök csak az aktuális verziót kéri le és telepíti, és nem távolítja el a tanúsítványokat. A csomópontok (amely általában havonta történik) rendszerképének alaphelyzetbe állítása visszaállítja a tanúsítványtárolót az operációsrendszer-lemezkép tartalmára, így a korábbi verziók implicit módon lesznek eltávolítva. Vegye figyelembe azonban, hogy a virtuálisgép-méretezési csoport skálázása csak a megfigyelt tanúsítványok aktuális verzióját fogja eredményezni. ne Tételezzük fel, hogy a csomópontok homogének a telepített tanúsítványokkal kapcsolatban.   

## <a name="simplifying-management---an-autorollover-example"></a>A felügyelet egyszerűsítése – példa az autorolloverre
Ismertetjük a mechanizmusokat, a korlátozásokat, a bonyolult szabályokat és definíciókat, valamint az kimaradások szörnyű előrejelzéseit. Előfordulhat, hogy itt az idő, hogy bemutassa, hogyan kell beállítani az automatikus Tanúsítványkezelőt, hogy elkerülje az összes ilyen szempontot. Ezt egy PaaSv2 virtuálisgép-méretezési csoporton futó Azure Service Fabric-fürt kontextusában tesszük, amely a titkok kezeléséhez és a felügyelt identitások kihasználásához Azure Key Vault használatával történik:
  - A tanúsítványok érvényesítése az ujjlenyomat-rögzítési pontról a tulajdonos + kiállítói rögzítésre módosul: az adott kiállítótól származó összes tanúsítvány egyformán megbízható
    - A tanúsítványok regisztrálva vannak egy megbízható áruházból (Key Vault), és az ügynök által frissítettek – ebben az esetben a kulcstartó virtuálisgép-bővítménye
    - A tanúsítványok kiépítése az üzembe helyezési idő és a verziószám (a ComputeRP által elvégezve) alapján módosult a telepítés utáni és a verziószám nélküli kulcstartó URI-k használatával.
    - A kulcstartóhoz való hozzáférés a felhasználó által hozzárendelt felügyelt identitások használatával adható meg; az UA-identitás létrehozása és hozzárendelése a virtuálisgép-méretezési csoporthoz az üzembe helyezés során
    - Az üzembe helyezés után az ügynök (a KV-os virtuálisgép-bővítmény) lekérdezi és frissíti a megfigyelt tanúsítványokat a virtuálisgép-méretezési csoport minden csomópontján. a tanúsítvány elforgatása így teljesen automatizált, mivel az SF automatikusan a legtávolabbi érvényes tanúsítványt fogja felvenni.

A sorozat teljes mértékben parancsfájlként használható/automatizált, és lehetővé teszi a tanúsítvány automatikus átváltására konfigurált fürt felhasználói érintés nélküli üzembe helyezését. Alább részletes lépéseket talál. A PowerShell-parancsmagok és a JSON-sablonok töredékei vegyesen használhatók. Ugyanez a funkció az Azure-ban való interakció összes támogatott eszközével elérhető.

> [!NOTE]
> Ez a példa feltételezi, hogy már létezik egy tanúsítvány a tárolóban; a kulcstartó által felügyelt tanúsítvány beléptetéséhez és megújításához a cikkben korábban ismertetett előfeltételek kézi lépések szükségesek. Éles környezetekben a kulcstartó által felügyelt tanúsítványok használata – a Microsoft belső PKI-hez tartozó minta parancsfájlt alább találja.

> [!NOTE]
> A tanúsítvány autorollover csak a CA által kiállított tanúsítványokra van értelme; önaláírt tanúsítványok használata, beleértve azokat is, amelyek a Service Fabric-fürtnek a Azure Portal való telepítésekor jönnek létre, értelmetlenek, de a helyi és a fejlesztő által üzemeltetett üzemelő példányok esetében is lehetséges, ha a kibocsátói ujjlenyomatot a levél tanúsítványának megfelelően deklarálja.

### <a name="starting-point"></a>Kezdőpont
A rövidség kedvéért a következő indítási állapotot feltételezzük:
  - A Service Fabric-fürt létezik, és védett egy, az ujjlenyomat által deklarált CA által kiadott tanúsítvánnyal.
  - A tanúsítványt tárolóban tárolják, és virtuálisgép-méretezési csoport titka kell kiépíteni
  - Ugyanezt a tanúsítványt fogjuk használni a fürt köznapi név alapú tanúsítvány-deklarációba való átalakításához, így a tulajdonos és a kibocsátó is érvényesítheti azt. Ha ez nem így van, szerezze be az erre a célra szánt CA által kiállított tanúsítványt, és adja hozzá a fürt definícióját az ujjlenyomat alapján az [itt](service-fabric-cluster-security-update-certs-azure.md) leírtak szerint.

Itt található egy olyan JSON-kivonat, amely egy olyan sablonhoz tartozik, amely egy ilyen állapotnak felel meg – Megjegyzés: Ez számos szükséges beállítást kihagy, és csak a tanúsítványokkal kapcsolatos szempontokat mutatja be:
```json
  "resources": [
    {   ## VMSS definition
      "apiVersion": "[variables('vmssApiVersion')]",
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('vmNodeTypeName')]",
      "location": "[variables('computeLocation')]",
      "properties": {
        "virtualMachineProfile": {
          "extensionProfile": {
            "extensions": [
            {
                "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
                "properties": {
                  "type": "ServiceFabricNode",
                  "autoUpgradeMinorVersion": true,
                  "publisher": "Microsoft.Azure.ServiceFabric",
                  "settings": {
                    "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                    "nodeTypeRef": "[variables('vmNodeTypeName')]",
                    "dataPath": "D:\\SvcFab",
                    "durabilityLevel": "Bronze",
                    "certificate": {
                        "thumbprint": "[parameters('primaryClusterCertificateTP')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },
                  "typeHandlerVersion": "1.1"
                }
            },}},
          "osProfile": {
            "adminPassword": "[parameters('adminPassword')]",
            "adminUsername": "[parameters('adminUsername')]",
            "secrets": [
            {
                "sourceVault": {
                    "id": "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
                },
                "vaultCertificates": [
                {
                    "certificateStore": "[parameters('certificateStoreValue')]",
                    "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
                },
            ]}]
        },
    },
    {   ## cluster definition
        "apiVersion": "[variables('sfrpApiVersion')]",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "certificate": {
            "thumbprint": "[parameters('primaryClusterCertificateTP')]",
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },
    }
  ]
```   

A fentiek lényegében azt mondja, hogy az ujjlenyomattal rendelkező tanúsítvány és a kulcstartó ```json [parameters('primaryClusterCertificateTP')] ``` URI-ja ```json [parameters('clusterCertificateUrlValue')] ``` a fürt egyetlen tanúsítványa, ujjlenyomat alapján van deklarálva. A következő lépésben a tanúsítvány kiváltásának biztosításához szükséges további erőforrásokat fogjuk beállítani.

### <a name="setting-up-prerequisite-resources"></a>Előfeltételként szükséges erőforrások beállítása
Ahogy azt korábban említettük, a virtuálisgép-méretezési csoport titkos kulcsaként kiépített tanúsítványt a Microsoft. számítási erőforrás-szolgáltató szolgáltatása, az első féltől származó identitása és a telepítési operátor nevében kéri le. Az automatikus átváltáshoz, amely megváltozik – váltson egy felügyelt identitás használatára, amely a virtuálisgép-méretezési csoporthoz van rendelve, és amely engedélyeket kap a tár titkos kulcsaihoz.

Az összes ezt követő részletet egyidejűleg kell üzembe helyezni – a Play-by-play elemzés és a magyarázatok külön vannak felsorolva.

Először Definiáljon egy felhasználóhoz rendelt identitást (példaként az alapértelmezett értékeket) – tekintse meg a naprakész információk [hivatalos dokumentációját](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity) :
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "userAssignedIdentityName": {
      "type": "string",
      "defaultValue": "sftstuaicus",
      "metadata": {
        "description": "User-assigned managed identity name"
      }
    },
  },
  "variables": {
      "vmssApiVersion": "2018-06-01",
      "sfrpApiVersion": "2018-02-01",
      "miApiVersion": "2018-11-30",
      "kvApiVersion": "2018-02-14",
      "userAssignedIdentityResourceId": "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
  },    
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('userAssignedIdentityName')]",
      "apiVersion": "[variables('miApiVersion')]",
      "location": "[resourceGroup().location]"
    },
  ]}
```

Ezt követően adja meg ezt az identitást a tár titkos kulcsaihoz – tekintse meg az aktuális információk [hivatalos dokumentációját](/rest/api/keyvault/vaults/updateaccesspolicy) :
```json
  "resources":
  [{
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "[variables('kvApiVersion')]",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).tenantId]",
            "objectId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).principalId]",
            "dependsOn": [
              "[variables('userAssignedIdentityResourceId')]"
            ],
            "permissions": {
              "secrets": [
                "get",
                "list"
              ]}}]}}]
```

A következő lépésben:
  - a felhasználó által hozzárendelt identitás hozzárendelése a virtuálisgép-méretezési csoporthoz
  - deklarálja a virtuálisgép-méretezési csoport függőségét a felügyelt identitás létrehozásával, valamint a tárolóhoz való hozzáférés biztosításának eredményével
  - deklarálja a kulcstartó virtuálisgép-bővítményét, amely megköveteli, hogy beolvassa a megfigyelt tanúsítványokat az indításkor ([hivatalos dokumentáció](../virtual-machines/extensions/key-vault-windows.md))
  - frissítse a Service Fabric virtuálisgép-bővítmény definícióját, hogy az a KVVM-bővítménytől függjön, és a fürt tanúsítványát köznapi névre alakítsa (a módosításokat egyetlen lépésben végezzük el, mivel az azonos erőforrás hatóköre alá tartoznak)

```json
  "parameters": {
    "kvvmextPollingInterval": {
      "type": "string",
      "defaultValue": "3600",
      "metadata": {
        "description": "kv vm extension polling interval in seconds"
      }
    },
    "kvvmextLocalStoreName": {
      "type": "string",
      "defaultValue": "MY",
      "metadata": {
        "description": "kv vm extension local store name"
      }
    },
    "kvvmextLocalStoreLocation": {
      "type": "string",
      "defaultValue": "LocalMachine",
      "metadata": {
        "description": "kv vm extension local store location"
      }
    },
    "kvvmextObservedCertificates": {
      "type": "array",
      "defaultValue": [
                "https://sftestcus.vault.azure.net/secrets/sftstcncluster",
                "https://sftestcus.vault.azure.net/secrets/sftstcnserver"
            ],
      "metadata": {
        "description": "kv vm extension observed certificates versionless uri"
      }
    },
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
  },
  "resources": [
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[variables('vmNodeTypeName')]",
    "location": "[variables('computeLocation')]",
    "dependsOn": [
      "[variables('userAssignedIdentityResourceId')]",
      "[concat('Microsoft.KeyVault/vaults/', concat(parameters('keyVaultName'), '/accessPolicies/add'))]"
    ],
    "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "[variables('userAssignedIdentityResourceId')]": {}
      }
    },
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
        {
          "name": "KVVMExtension",
          "properties": {
            "publisher": "Microsoft.Azure.KeyVault",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": "[parameters('kvvmextPollingInterval')]",
                    "linkOnRenewal": false,
                    "observedCertificates": "[parameters('kvvmextObservedCertificates')]",
                    "requireInitialSync": true
                }
            }
          }
        },
        {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
        "properties": {
          "type": "ServiceFabricNode",
          "provisionAfterExtensions" : [ "KVVMExtension" ],
          "publisher": "Microsoft.Azure.ServiceFabric",
          "settings": {
            "certificate": {
                "commonNames": [
                    "[parameters('certificateCommonName')]"
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            }
            },
            "typeHandlerVersion": "1.0"
          }
        },
  ] } ## extension profile
  },  ## VM profile
  "osProfile": {
    "adminPassword": "[parameters('adminPassword')]",
    "adminUsername": "[parameters('adminUsername')]",
  } 
  }
  ]
```
Vegye figyelembe, hogy bár a fentiekben nem szerepel explicit módon, a rendszer eltávolította a tár tanúsítványának URL-címét a virtuálisgép-méretezési csoport "OsProfile" szakaszából.
Az utolsó lépés a fürt definíciójának frissítése, hogy módosítsa a tanúsítvány deklarációját az ujjlenyomatról a köznapi névre – itt a kiállítói ujjlenyomatai megfelelnek is rögzítjük:

```json
  "parameters": {
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "defaultValue": "1b45ec255e0668375043ed5fe78a09ff1655844d,d7fe717b5ff3593764f4d90654d86e8362ec26c8,3ac7c3cac8de0dd392c02789c8be97474f456960,96ea05926e2e42cc207e358668be2c316857fb5e",
      "metadata": {
        "description": "Certificate issuer thumbprints separated by comma"
      }
    },
  },
  "resources": [
    {
      "apiVersion": "[variables('sfrpApiVersion')]",
      "type": "Microsoft.ServiceFabric/clusters",
      "name": "[parameters('clusterName')]",
      "location": "[parameters('clusterLocation')]",
      "properties": {
        "certificateCommonNames": {
          "commonNames": [{
              "certificateCommonName": "[parameters('certificateCommonName')]",
              "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
          }],
          "x509StoreName": "[parameters('certificateStoreValue')]"
        },
  ]
```

Ezen a ponton a fent említett frissítéseket egyetlen üzemelő példányban futtathatja; a részeként a Service Fabric erőforrás-szolgáltató szolgáltatás több lépésben feldarabolja a fürt frissítését a [fürt tanúsítványainak ujjlenyomatról köznapi névre történő átalakításának](cluster-security-certificates.md#converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations)szegmensében leírtak szerint.

### <a name="analysis-and-observations"></a>Elemzés és észrevételek
Ez a szakasz a fentiekben ismertetett lépéseket ismerteti, és figyelemmel kíséri a fontos szempontokat.

#### <a name="certificate-provisioning-explained"></a>Tanúsítvány kiépítés, magyarázat
A KVVM-bővítmény kiépítési ügynökként folyamatosan fut előre meghatározott gyakorisággal. Ha nem sikerül beolvasni egy megfigyelt tanúsítványt, az továbbra is a következő sorba kerül, majd a következő ciklusig hibernálva lesz. A fürt betöltési ügynökének a SFVM-bővítménynek a deklarált tanúsítványokra van szüksége, mielőtt a fürt kikerül. Ez azt jelenti, hogy a SFVM-bővítmény csak a fürt tanúsítvány (ok) sikeres lekérése után futtatható, amelyet a ```json "provisionAfterExtensions" : [ "KVVMExtension" ]"``` záradék és az KeyVaultVM-bővítmény beállítása is jelez ```json "requireInitialSync": true``` . Ez azt jelzi, hogy az első futtatáskor (az üzembe helyezés után vagy egy újraindítás után) a KVVM-bővítménynek a megfigyelt tanúsítványokon keresztül kell futnia, amíg az összes letöltése nem sikerült. Ha a paraméter hamis értékre van állítva, és a fürt tanúsítványának lekérése sikertelen volt, a rendszer a fürt központi telepítésének meghibásodását eredményezi. Ezzel szemben, ha a megfigyelt tanúsítványok helytelen/érvénytelen listájával való kezdeti szinkronizálást igényel, a KVVM-bővítmény meghibásodását eredményezné, így a fürt üzembe helyezése nem sikerült.  

#### <a name="certificate-linking-explained"></a>Tanúsítvány összekapcsolása, magyarázat
Előfordulhat, hogy észrevette a KVVM-bővítmény "linkOnRenewal" jelzőjét, és az a tény, hogy hamis értékre van állítva. Itt részletesen ismertetjük az e jelző által vezérelt viselkedést, valamint a fürt működésével kapcsolatos következményeiket. Megjegyzés Ez a viselkedés a Windowsra jellemző.

A [definíciója](../virtual-machines/extensions/key-vault-windows.md#extension-schema)szerint:

```json
"linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
```

A TLS-kapcsolat létesítéséhez használt tanúsítványokat jellemzően az S-Channel biztonsági támogató szolgáltatón keresztül [kezelik](/windows/win32/api/sspi/nf-sspi-acquirecredentialshandlea) , vagyis az ügyfél nem fér hozzá közvetlenül a tanúsítvány titkos kulcsához. Az s-Channel támogatja a hitelesítő adatok átirányítását (összekapcsolása) a tanúsítvány kiterjesztésének ([CERT_RENEWAL_PROP_ID](/windows/win32/api/wincrypt/nf-wincrypt-certsetcertificatecontextproperty#cert_renewal_prop_id)) formájában: Ha ez a tulajdonság be van állítva, az értéke a "megújítás" tanúsítvány ujjlenyomatát jelöli, így az s-Channel Ehelyett megkísérli betölteni a csatolt tanúsítványt. Valójában ez a csatolt (és remélhetőleg aciklikus) listán halad át, amíg a végleges tanúsítvánnyal nem ér véget – egyet megújítási megjelölés nélkül. Ez a funkció, ha megfontoltan használatban van, kiváló megoldás a lejárt tanúsítványok (például) által okozott rendelkezésre állás elvesztésével szemben. Más esetekben előfordulhat, hogy az kimaradás oka nehéz diagnosztizálni és enyhíteni. Az S-Channel a tanúsítványokat a megújítási tulajdonságokat feltétlen módon hajtja végre, függetlenül attól, hogy a tulajdonos, a kiállítók vagy az ügyfél által az eredményül kapott tanúsítvány érvényesítésében részt vevő egyéb attribútumok szerepelnek-e. Valóban lehetséges, hogy az eredményül kapott tanúsítványhoz nem tartozik titkos kulcs, vagy a kulcs nem lett ACLed a leendő fogyasztónak. 
 
Ha a csatolás engedélyezve van, akkor a kulcstartó virtuálisgép-bővítmény a megfigyelt tanúsítványnak a tárolóból való lekérése után megkísérli megtalálni a megfelelő, meglévő tanúsítványokat, hogy azok a megújítási kiterjesztés tulajdonságával legyenek összekapcsolva. A megfeleltetés (kizárólag) a tulajdonos alternatív neve (SAN) alapján történik, és az alábbi példának megfelelően működik.
Tegyük fel, hogy két létező tanúsítvány van: A következő: A: CN = "Alice 's Accessories", SAN = {"alice.universalexports.com"}, megújítás = "" B: CN = "Bob BITS", SAN = {"bob.universalexports.com", "bob.universalexports.net"}, megújítás = ""
 
Tegyük fel, hogy a C tanúsítvány lekérése a KVVM ext: CN = "Mallory 's malware", SAN = {"alice.universalexports.com", "bob.universalexports.com", "mallory.universalexports.com"}
 
A SAN-lista teljes mértékben szerepel a C-ben, így A. megújítás = C. ujjlenyomat; A b SAN-listájának közös metszéspontja C, de nem teljes mértékben benne, így B. a megújítás üres marad.
 
A Acquirecredentialshandle függvény végrehajtása (S-Channel) ebben az állapotban való meghívására tett kísérletek az A tanúsítvány esetében valójában a C küldését a távoli fél felé. Service Fabric esetén a fürt [átviteli alrendszere](service-fabric-architecture.md#transport-subsystem) az S-Channel módszert használja a kölcsönös hitelesítéshez, és így a fent ismertetett viselkedés közvetlenül befolyásolja a fürt alapvető kommunikációját. Folytassa a fenti példát, és feltételezve, hogy a a fürt tanúsítványa, mi történik, a következő függ:
  - Ha a C titkos kulcsa nem a hálót futtató fiókhoz ACLd, akkor a titkos kulcs beolvasásához szükséges hibák (SEC_E_UNKNOWN_CREDENTIALS vagy hasonlók) jelennek meg.
  - Ha a C titkos kulcsa elérhető, akkor láthatjuk a többi csomópont által visszaadott hitelesítési hibákat (CertificateNotMatched, jogosulatlan stb.). 
 
Mindkét esetben a szállítás meghiúsul, és előfordulhat, hogy a fürt leáll; a tünetek eltérőek. Ahhoz, hogy a dolgok rosszabbak legyenek, a Csatolás a megújítási sorrendtől függ – amelyet a KVVM megfigyelt tanúsítványok listájának sorrendje, a tárolóban megújítási ütemterv, vagy akár olyan átmeneti hibák is diktálnak, amelyek megváltoztatják a lekérések sorrendjét.

Az ilyen incidensek elleni védekezéshez a következőket javasoljuk:
  - ne keverje össze a különböző tár tanúsítványait; minden egyes tár tanúsítványának külön célra kell szolgálnia, és a tulajdonosuk és a SAN-nak meg kell jelennie a sajátosságoknak
  - a tulajdonos köznapi nevének belefoglalása a SAN-listára (például: "CN = <subject common name> ")  
  - Ha nem biztos, tiltsa le az KVVM bővítménnyel kiépített tanúsítványok megújításához való csatolást 

#### <a name="why-use-a-user-assigned-managed-identity-what-are-the-implications-of-using-it"></a>Miért érdemes felhasználó által hozzárendelt felügyelt identitást használni? Mik a használatának következményei?
Ahogy az a fenti JSON-kódrészletből megjelent, az átalakítás sikerességének biztosításához, valamint a fürt rendelkezésre állásának fenntartásához szükség van az Operations/Updates egy adott sorrendre. Pontosabban, a virtuálisgép-méretezési csoport erőforrása deklarálja és használja az identitását, hogy a titkokat egyetlen (a felhasználó szemszögéből) frissítéssel beolvassa. A Service Fabric virtuálisgép-bővítmény (amely a fürtöt beindítja) a kulcstartó virtuálisgép-bővítményének befejeződésétől függ, amely a megfigyelt tanúsítványok sikeres lekérésének függvénye. Az KVVM bővítmény a virtuálisgép-méretezési csoport identitását használja a tároló eléréséhez, ami azt jelenti, hogy a tároló hozzáférési szabályzatát már frissíteni kell a virtuálisgép-méretezési csoport üzembe helyezése előtt. 

Felügyelt identitás létrehozásához vagy egy másik erőforráshoz való hozzárendeléséhez a telepítési operátornak rendelkeznie kell az előfizetés vagy az erőforráscsoport szükséges szerepkörével (ManagedIdentityOperator), valamint a sablonban hivatkozott többi erőforrás kezeléséhez szükséges szerepkörökkel. 

Biztonsági szempontból ne felejtse el, hogy a virtuális gép (méretezési csoport) biztonsági határnak minősül az Azure-identitásával kapcsolatban. Ez azt jelenti, hogy a virtuális gépen üzemeltetett bármely alkalmazás a nem hitelesített IMDS-végpontról szerezhet be olyan hozzáférési jogkivonatot, amely a virtuális gép által felügyelt identitás-hozzáférési jogkivonatokat jelképezi. Ha úgy gondolja, hogy a virtuális gép megosztott vagy több-bérlős környezetben van, akkor előfordulhat, hogy ez a metódus nem szerepel a fürt tanúsítványainak beolvasásakor. Azonban az egyetlen kiépítési mechanizmus, amely alkalmas a tanúsítványok autorolloverre.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Hibaelhárítás és gyakran ismételt kérdések

*K*: Hogyan lehet programozott módon regisztrálni egy kulcstartó által felügyelt tanúsítványba?
Válasz *: keresse* meg a kiállító nevét a kulcstartó dokumentációjában, majd cserélje le az alábbi szkriptre.  
```PowerShell
  $issuerName=<depends on your PKI of choice>
    $clusterVault="sftestcus"
    $clusterCertVaultName="sftstcncluster"
    $clusterCertCN="cus.cluster.sftstcn.system.servicefabric.azure-int"
    Set-AzKeyVaultCertificateIssuer -VaultName $clusterVault -Name $issuerName -IssuerProvider $issuerName
    $distinguishedName="CN=" + $clusterCertCN
    $policy = New-AzKeyVaultCertificatePolicy `
        -IssuerName $issuerName `
        -SubjectName $distinguishedName `
        -SecretContentType 'application/x-pkcs12' `
        -Ekus "1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2" `
        -ValidityInMonths 4 `
        -KeyType 'RSA' `
        -RenewAtPercentageLifetime 75        
    Add-AzKeyVaultCertificate -VaultName $clusterVault -Name $clusterCertVaultName -CertificatePolicy $policy
    
    # poll the result of the enrollment
    Get-AzKeyVaultCertificateOperation -VaultName $clusterVault -Name $clusterCertVaultName 
```

*K*: mi történik, ha egy tanúsítványt egy nem deklarált/meghatározatlan kiállító bocsát ki? Hol szerezhetem be az adott PKI aktív kiállítóinak teljes listáját?
*A*: Ha a tanúsítvány deklarációja megadja a kibocsátó ujjlenyomatai megfelelnek, és a tanúsítvány közvetlen kiállítója nem szerepel a rögzített kiállítók listáján, akkor a tanúsítvány érvénytelennek tekintendő – függetlenül attól, hogy az ügyfél megbízhatónak minősíti-e a gyökeret. Ezért fontos, hogy a kiállítók listája aktuális/naprakész legyen. Egy új kiállító bevezetésének ritka eseménynek kell lennie, és a tanúsítványok kiállításának megkezdése előtt széles körben nyilvánosságra kell állítani. 

Általánosságban elmondható, hogy a PKI az IETF [RFC 7382](https://tools.ietf.org/html/rfc7382)-es verziójának megfelelően közzéteszi és karbantartja a minősítési gyakorlatra vonatkozó nyilatkozatot. További információk többek között az összes aktív kiállítót is tartalmazzák. A lista programozott módon történő beolvasása eltérő lehet a PKI-ről egy másikra.   

A Microsoft – belső Erdőmbe kapcsolatban tekintse meg a jóváhagyott kiállítók beolvasásához használt végpontok/SDK-k belső dokumentációját; Ez a fürt tulajdonosának feladata a lista rendszeres mintavétele, és annak biztosítása, hogy a fürt definíciója tartalmazza az *összes* várható kiállítót.

*K*: több erdőmbe is támogatott? 
*A*: igen; Előfordulhat, hogy nem deklarál több KN-bejegyzést a fürt jegyzékfájljában ugyanazzal az értékkel, de a kiállítók listáját az ugyanahhoz a CN-hoz tartozó több Erdőmbe is listázhatja. Nem ajánlott eljárás, és a tanúsítvány-átláthatósági eljárások megakadályozhatják, hogy az ilyen tanúsítványok ki legyenek bocsátva. Azonban ahogy azt jelenti, hogy egy PKI-ről egy másikra kíván áttérni, ez egy elfogadható mechanizmus.

*K*: mi történik, ha az aktuális fürtcsomópont nem a CA által kiállított, vagy nem rendelkezik a kívánt tárggyal? 
*A*: szerezze be a kívánt tárgyú tanúsítványt, és adja hozzá a fürt definícióját másodlagosként ujjlenyomat alapján. A frissítés sikeres befejeződése után kezdeményezzen egy másik fürtkonfiguráció-frissítést a tanúsítvány deklarációjának köznapi névre való átalakításához. 

[Image1]:./media/security-cluster-certificate-mgmt/certificate-journey-thumbprint.png
[Image2]:./media/security-cluster-certificate-mgmt/certificate-journey-common-name.png
