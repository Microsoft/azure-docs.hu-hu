---
title: Az erőforrás-szolgáltatóval kapcsolatos hibák elhárítása Azure NetApp Files | Microsoft Docs
description: A gyakori Azure NetApp Files erőforrás-szolgáltatói hibák okait, megoldásait és megkerülő megoldásait ismerteti.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 02/10/2021
ms.author: b-juche
ms.openlocfilehash: ac83e5a9366a12c5adce0e08f04f2bb28a7d788d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100374881"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Az Azure NetApp Files erőforrás-szolgáltatójának hibaelhárítása 

Ez a cikk a gyakori Azure NetApp Files erőforrás-szolgáltatói hibákat, azok okait, megoldásait és megkerülő megoldásait ismerteti (ha vannak ilyenek).

## <a name="common-azure-netapp-files-resource-provider-errors"></a>Gyakori Azure NetApp Files erőforrás-szolgáltatói hibák

***A létrehozása `netAppAccounts` ebben a régióban korlátozott.***

Ez a helyzet akkor fordul elő, ha az előfizetés Azure NetApp Files waitlisted, és a felhasználó egy NetApp-fiók létrehozását kísérli meg.

* Ok:   
Nem sikerült regisztrálni a Azure NetApp Files Azure-erőforrás-szolgáltatóját. 
 
* Megoldás:   
Az előfizetés waitlisted követően hajtsa végre az [Azure NetApp erőforrás-szolgáltató regisztrációjában](azure-netapp-files-register.md#resource-provider) ismertetett lépéseket.

***A BareMetalTenantId nem módosítható.***  

Ez a hiba akkor fordul elő, ha egy kötetet próbál frissíteni vagy javítani, és a `BaremetalTenantId` tulajdonság módosult értékkel rendelkezik.

* Ok:   
Egy kötetet próbál frissíteni, és a `BaremetalTenantId` tulajdonság értéke eltér az Azure-ban tárolt értéktől.
* Megoldás:   
Ne foglalja bele `BaremetalTenantId` a patch és a Update (Put) kérelembe. Azt is megteheti, hogy `BaremetalTenantId` a kérelemben megegyeznek.

***A ServiceLevel nem módosítható.***  

Ez a hiba akkor fordul elő, ha egy másik szolgáltatási szinttel rendelkező kapacitási készletet próbál frissíteni vagy kijavítani, ha a kapacitás-készlet már tartalmaz köteteket.

* Ok:   
Ha a készlet köteteket tartalmaz, frissítenie kell a kapacitási készlet szolgáltatási szintjét.
* Megoldás:   
Törölje az összes kötetet a kapacitási készletből, majd módosítsa a szolgáltatási szintet.
* Megkerülő megoldás:   
Hozzon létre egy másik kapacitási készletet, majd hozza létre újra a köteteket az új kapacitási készletben.

***A PoolId nem módosítható***  

Ez a hiba akkor fordul elő, ha módosított tulajdonsággal rendelkező kapacitási készletet próbál frissíteni vagy kijavítani `PoolId` .

* Ok:   
Egy Capacity Pool `PoolId` tulajdonságot próbál frissíteni. A `PoolId` tulajdonság csak olvasható tulajdonság, és nem módosítható.
* Megoldás:   
Ne foglalja bele `PoolId` a patch és a Update (Put) kérelembe.  Azt is megteheti, hogy `PoolId` a kérelemben megegyeznek.

***A CreationToken nem módosítható.***

Ez a hiba akkor fordul elő, amikor a kötet létrehozása után megpróbálja módosítani a fájl elérési útját ( `CreationToken` ). A kötet létrehozásakor meg kell adni a fájl elérési útját ( `CreationToken` ), és később nem módosítható.

* Ok:   
A kötet létrehozása után megpróbálja módosítani a fájl elérési útját ( `CreationToken` ), amely nem támogatott művelet. 
* Megoldás:   
Ha a fájl elérési útjának módosítása nem szükséges, akkor érdemes lehet eltávolítani a paramétert a kérelemből a hibaüzenet bezárásához.
* Megkerülő megoldás:   
Ha módosítania kell a fájl elérési útját ( `CreationToken` ), létrehozhat egy új kötetet új elérési úttal, majd áttelepítheti az új kötetre.

***A CreationToken legalább 16 karakter hosszúnak kell lennie.***

Ez a hiba akkor fordul elő, ha a fájl elérési útja ( `CreationToken` ) nem felel meg a hosszra vonatkozó követelménynek. A fájl elérési útjának hosszának legalább egy karakterből kell állnia.

* Ok:   
A fájl elérési útja üres.  Ha az API használatával hoz létre kötetet, a létrehozási jogkivonat megadása kötelező. Ha a Azure Portal használja, a fájl elérési útja automatikusan létrejön.
* Megoldás:   
Adjon meg legalább egy karaktert a fájl elérési útjaként ( `CreationToken` ).

***A tartománynév nem módosítható.***

Ez a hiba akkor fordul elő, ha a tartománynevet Active Directoryban próbálja megváltoztatni.

* Ok:   
A tartománynév tulajdonságot próbálja frissíteni.
* Megoldás:    
Nincsenek. A tartomány neve nem módosítható.
* Megkerülő megoldás:   
Törölje az összes kötetet a Active Directory konfiguráció használatával. Ezután törölje a Active Directory konfigurációt, és hozza létre újra a köteteket.

***Ismétlődő érték történt az objektum ExportPolicy. rules [RuleIndex] esetében.***

Ez a hiba akkor fordul elő, ha az exportálási házirend nincs definiálva egyedi indexszel. Az exportálási házirendek meghatározásakor minden exportálási házirendnek egyedi indexszel kell rendelkeznie 1 és 5 között.

* Ok:   
A megadott exportálási házirend nem felel meg az exportálási házirend szabályainak. A maximális értéknél egy exportálási házirendi szabályt kell megadnia a minimum és öt exportálási szabályhoz.
* Megoldás:   
Győződjön meg arról, hogy az index még nincs használatban, és hogy az 1 és 5 közötti tartományba esik.
* Megkerülő megoldás:   
Használjon másik indexet a beállítani kívánt szabályhoz.

***Hiba {Action} {resourceTypeName}***

Ez a hiba akkor jelenik meg, ha más hibakezelés nem tudta kezelni a hibát egy erőforráson végrehajtott művelet végrehajtása közben.   Ide tartozik a "hiba" szöveg. Az a következő lehet: `{action}` ( `getting` , `creating` , `updating` , vagy `deleting` ).  A a `{resourceTypeName}` `resourceTypeName` következő: (például, `netAppAccount` , `capacityPool` `volume` stb.).

* Ok:   
Ez a hiba nem kezelt kivétel, ha az ok ismeretlen.
* Megoldás:   
A naplók részletes okának jelentéséhez forduljon az Azure támogatási központjához.
* Megkerülő megoldás:   
Nincsenek.

***A fájl elérési útjának neve csak betűket, számokat és kötőjeleket (""-") tartalmazhat.***

Ez a hiba akkor fordul elő, ha a fájl elérési útja nem támogatott karaktereket tartalmaz, például egy pontot ("."), vesszőt (","), aláhúzást (_) vagy dollár jelet ("$").

* Ok:   
A fájl elérési útja nem támogatott karaktereket tartalmaz, például egy pontot ("."), vesszőt (","), aláhúzást ("_") vagy dollár-jelet ("$").
* Megoldás:   
A beírt fájl elérési útjából távolítsa el a betűket, számokat és kötőjeleket ("-").
* Megkerülő megoldás:   
Az aláhúzást lecserélheti egy kötőjelre, vagy a szóközök helyett a nagybetűket is használhatja, hogy az új szavak elejét adja.  Például az "új kötet" helyett használja a "NewVolume" értéket.

***A FileSystemId nem módosítható.***

Ez a hiba akkor fordul elő, ha megpróbálja módosítani `FileSystemId` .  A módosítás `FileSystemdId` nem támogatott művelet. 

* Ok:   
A rendszer a kötet létrehozásakor beállítja a fájlrendszer AZONOSÍTÓját. `FileSystemId` ezt követően nem módosítható.
* Megoldás:   
Ne szerepeljen a `FileSystemId` javítási és frissítési (Put) kérelemben.  Azt is megteheti, hogy `FileSystemId` ugyanaz a kérelemben.

***A (z) {string} azonosítójú ActiveDirectory nem létezik.***

A `{string}` rész a `ActiveDirectoryId` Active Directory-kapcsolatok tulajdonságában megadott érték.

* Ok:   
Amikor létrehozott egy fiókot a Active Directory-konfigurációval, a megadott értéknek `ActiveDirectoryId` üresnek kell lennie.
* Megoldás:   
Ne szerepeljenek `ActiveDirectoryId` a létrehozás (Put) kérelemben.

***Érvénytelen API-Version.***

Az API-verzió vagy nincs elküldve, vagy érvénytelen értéket tartalmaz.

* Ok:   
A lekérdezési paraméter értéke `api-version` érvénytelen értéket tartalmaz.
* Megoldás:   
A megfelelő API-verzió értékének használata.  Az erőforrás-szolgáltató számos API-verziót támogat. Az érték éééé-hh-nn formátumban van.

***Érvénytelen "{value}" érték érkezett a következőhöz: {1} .***

Ez az üzenet a,,,, és rendszer mezőinek hibáját jelzi `RuleIndex` `AllowedClients` `UnixReadOnly` `UnixReadWrite` `Nfsv3` `Nfsv4` .

* Ok:   
A bemeneti ellenőrzési kérelem sikertelen volt a következő mezők legalább egyike esetében:,,, `RuleIndex` `AllowedClients` `UnixReadOnly` `UnixReadWrite` , `Nfsv` 3 és `Nfsv4` .
* Megoldás:   
Ügyeljen arra, hogy az összes szükséges és nem ütköző paramétert a parancssorban állítsa be. Nem állíthatja be például a `UnixReadOnly` és a paramétereket egyszerre `UnixReadWrite` .
* Megkerülő megoldás:   
Tekintse meg a fenti megoldást.

***A VLAN-hoz tartozó IP-címtartomány {0} {1} {2} már használatban van***

Ez a hiba azért fordul elő, mert a használt IP-címtartományok belső rekordjai ütköznek az újonnan hozzárendelt IP-címmel.

* Ok:   
A kötet létrehozásához hozzárendelt IP-cím már regisztrálva van.
Ennek oka lehet egy korábbi sikertelen kötetek létrehozása.
* Megoldás:   
Forduljon az Azure támogatási központjához.

***A (z) {Property} tulajdonság értéke hiányzik.***

Ez a hiba azt jelzi, hogy hiányzik egy szükséges tulajdonság a kérelemből. A (z) {Property} karakterlánc a hiányzó tulajdonság nevét tartalmazza.

* Ok:   
A bemeneti ellenőrzési kérelem sikertelen volt legalább az egyik tulajdonságnál.
* Megoldás:   
Győződjön meg arról, hogy a kérelemben az összes szükséges és nem ütköző tulajdonságot (speciálisan a hibaüzenetből) adja meg.

***A MountTargets nem módosítható.***

Ez a hiba akkor fordul elő, ha a felhasználó frissíteni vagy javítani szeretné a Volume MountTargets tulajdonságot.

* Ok:   
A Volume `MountTargets` tulajdonságot próbálja frissíteni. A tulajdonság módosítása nem támogatott.
* Megoldás:   
Ne szerepeljen a `MountTargets` javítási és frissítési (Put) kérelemben.  Azt is megteheti, hogy `MountTargets` ugyanaz a kérelemben.

***A név már használatban van.***

Ez a hiba azt jelzi, hogy az erőforrás neve már használatban van.

* Ok:   
Egy meglévő erőforráshoz használt névvel rendelkező erőforrást próbál létrehozni.
* Megoldás:   
Az erőforrás létrehozásakor használjon egyedi nevet.

***A fájl elérési útja már használatban van.***

Ez a hiba azt jelzi, hogy a kötet elérési útja már használatban van.

* Ok:   
Olyan kötetet próbál létrehozni, amelynek elérési útja megegyezik egy meglévő kötettel.
* Megoldás:   
A kötet létrehozásakor használjon egyedi elérési utat.

***A név túl hosszú.***

Ez a hiba azt jelzi, hogy az erőforrás neve nem felel meg a maximális hosszra vonatkozó követelménynek.

* Ok:   
Az erőforrás neve túl hosszú.
* Megoldás:   
Használjon rövidebb nevet az erőforráshoz.

***A fájl elérési útja túl hosszú.***

Ez a hiba azt jelzi, hogy a kötethez megadott elérési út nem felel meg a maximális hosszra vonatkozó követelménynek.

* Ok:   
A kötet fájljának elérési útja túl hosszú.
* Megoldás:   
Használjon rövidebb elérési utat.

***A név túl rövid.***

Ez a hiba azt jelzi, hogy az erőforrás neve nem felel meg a minimális hosszra vonatkozó követelménynek.

* Ok:   
Az erőforrás neve túl rövid.
* Megoldás:   
Használjon egy hosszú nevet az erőforráshoz.

***A fájl elérési útja túl rövid.***

Ez a hiba azt jelzi, hogy a kötet fájljának elérési útja nem felel meg a minimális hosszra vonatkozó követelménynek.

* Ok:   
A kötet fájljának elérési útja túl rövid.
* Megoldás:   
Növelje a kötet fájl elérési útjának hosszát.

***Azure NetApp Files API nem érhető el.***

Az Azure API a Azure NetApp Files API-ra támaszkodik a kötetek kezeléséhez. Ez a hiba az API-kapcsolatok hibáját jelzi.

* Ok:   
A mögöttes API nem válaszol, ami belső hibát eredményez. Ez a hiba valószínűleg átmeneti.
* Megoldás:   
A probléma valószínűleg ideiglenes. Egy kis idő elteltével a kérelemnek sikeresnek kell lennie.
* Megkerülő megoldás:   
Nincsenek. Az alapul szolgáló API elengedhetetlen a kötetek kezeléséhez.

***Nem található műveleti eredmény-azonosító a következőhöz: " {0} ".***

Ez a hiba azt jelzi, hogy egy belső hiba megakadályozza a művelet befejezését.

* Ok:   
Belső hiba történt, és megakadályozta a művelet befejezését.
* Megoldás:   
Ez a hiba valószínűleg átmeneti. Várjon néhány percet, és próbálkozzon újra. Ha a probléma továbbra is fennáll, hozzon létre egy jegyet a technikai támogatással kapcsolatos kérdés kivizsgálásához.
* Megkerülő megoldás:   
Várjon néhány percet, és ellenőrizze, hogy a probléma továbbra is fennáll-e.

***A CIFS és NFS protokollok típusai nem engedélyezettek***

Ez a hiba akkor fordul elő, ha kötetet próbál létrehozni, és a kötet tulajdonságaiban mind a CIFS (SMB), mind az NFS protokoll típusa szerepel.

* Ok:   
A rendszer a CIFS (SMB) és az NFS protokollok típusát is használja a kötet tulajdonságaiban.
* Megoldás:   
Távolítsa el a protokollok egyik típusát.
* Megkerülő megoldás:   
Hagyja üresen a protokoll Type tulajdonságát, vagy a null értéket.

***Elemek száma: {Value} a következő objektumhoz: ExportPolicy. rules [RuleIndex] a min-max tartományon kívül esik.***

Ez a hiba akkor fordul elő, ha az exportálási házirend szabályai nem felelnek meg a minimális vagy a maximális tartományra vonatkozó követelménynek. Ha az exportálási szabályzatot határozza meg, akkor a minimális és öt exportálási házirend-szabálynak rendelkeznie kell a maximális értéknél.

* Ok:   
A megadott exportálási szabályzat nem felel meg a szükséges tartománynak.
* Megoldás:   
Győződjön meg arról, hogy az index még nincs használatban, és az 1 és 5 közötti tartományban van.
* Megkerülő megoldás:   
A kötetek exportálási házirendjének használata nem kötelező. Az exportálási szabályzatot teljes mértékben kihagyhatja, ha nem kell használnia az exportálási házirend szabályait.

***Csak egy Active Directory engedélyezett***

Ez a hiba akkor fordul elő, amikor megpróbál létrehozni egy Active Directory konfigurációt, és egy már létezik az előfizetéshez a régióban. A hiba akkor is előfordulhat, ha egynél több Active Directory konfigurációt próbál létrehozni.

* Ok:   
Egy Active Directoryt próbál létrehozni (nem frissíteni), de egy már létezik.
* Megoldás:   
Ha a Active Directory konfiguráció nincs használatban, először törölje a meglévő konfigurációt, majd próbálja megismételni a létrehozási műveletet.
* Megkerülő megoldás:   
Nincsenek. Csak egy Active Directory engedélyezett.

***A (z) {Operation} művelet nem támogatott.***

Ez a hiba azt jelzi, hogy a művelet nem érhető el az aktív előfizetéshez vagy erőforráshoz.

* Ok:   
A művelet nem érhető el az előfizetéshez vagy az erőforráshoz.
* Megoldás:   
Ellenőrizze, hogy a művelet helyesen van-e megadva, és hogy elérhető-e a használt erőforráshoz és előfizetéshez.

***A OwnerId nem módosítható***

Ez a hiba akkor fordul elő, amikor megpróbálja módosítani a kötet OwnerId tulajdonságát. A OwnerId módosítása nem támogatott művelet. 

* Ok:   
A `OwnerId` tulajdonság beállítása a kötet létrehozásakor történik. A tulajdonságot később nem lehet módosítani.
* Megoldás:   
Ne szerepeljen a `OwnerId` javítási és frissítési (Put) kérelemben. Azt is megteheti, hogy `OwnerId` ugyanaz a kérelemben.

***A szülő készlet nem található***

Ez a hiba akkor fordul elő, ha kötetet próbál létrehozni, és a kötetet létrehozó kapacitási készlet nem található.

* Ok:   
Nem található az a kapacitási készlet, amelyben a kötet létrehozása folyamatban van.
* Megoldás:   
A rendszer valószínűleg nem hozta létre teljesen a készletet, vagy már törölték a kötet létrehozásakor.

***Ez az erőforrástípus nem támogatja a javítási műveletet.***

Ez a hiba akkor fordul elő, amikor megpróbálja módosítani a csatlakoztatási célt vagy pillanatképet.

* Ok:   
A csatlakoztatási cél a létrehozáskor van definiálva, és a későbbiekben nem módosítható.
A pillanatképek nem tartalmaznak a megváltoztathatatlan tulajdonságokat.
* Megoldás:   
Nincsenek. Ezeknek az erőforrásoknak nincs módosítható tulajdonsága.

***A készlet mérete túl kicsi a teljes kötet méretéhez.***

Ez a hiba akkor fordul elő, ha frissíti a kapacitási készlet méretét, és a méret kisebb, mint az `usedBytes` adott kapacitású készlet összes kötetének teljes értéke.  Ez a hiba akkor is előfordulhat, ha új kötetet hoz létre, vagy egy meglévő kötetet átméretezi, és az új kötet mérete meghaladja a kapacitási készletben lévő szabad területet.

* Ok:   
A kapacitási készletet kisebb méretre próbálja frissíteni, mint a usedBytes lévő összes köteten.  Vagy olyan kötetet próbál létrehozni, amely nagyobb, mint a kapacitási készletben lévő szabad terület.  Azt is megteheti, hogy átméretezi a kötetet, és az új méret meghaladja a szabad területet a kapacitási készletben.
* Megoldás:   
Állítsa a kapacitási készlet méretét nagyobb értékre, vagy hozzon létre egy köteten egy kisebb kötetet.
* Megkerülő megoldás:   
Távolítson el elég köteteket, hogy a kapacitási készlet mérete frissíthető legyen erre a méretre.

***A tulajdonság: a pillanatkép helyének meg kell egyeznie a kötettel.***

Ez a hiba akkor fordul elő, ha olyan pillanatképet hoz létre, amely nem a pillanatképet birtokló köteten található.

* Ok:   
Érvénytelen érték szerepel a pillanatkép Location tulajdonságában.
* Megoldás:   
Érvényes karakterláncot állítson be a Location tulajdonságban.

***A (z) {resourceType} nevének meg kell egyeznie az erőforrás-azonosító nevével.***

Ez a hiba akkor fordul elő, ha erőforrást hoz létre, és a name tulajdonságot más értékkel tölti ki, mint a name tulajdonsága `resourceId` .

* Ok:   
Az erőforrás létrehozásakor érvénytelen érték szerepel a name tulajdonságban.
* Megoldás:   
Hagyja üresen a Name (név) tulajdonságot, vagy engedélyezze, hogy ugyanazt az értéket használja, mint a Name tulajdonság (az utolsó fordított perjel "/" és a "?" kérdőjel között) `resourceId` .

***A protokoll típusa ({Value}) ismeretlen***

Ez a hiba akkor fordul elő, ha ismeretlen protokoll típusú kötetet hoz létre.  Az érvényes értékek: "NFSv3", "Nfsv4 névleképezője" és "CIFS".

* Ok:   
Érvénytelen értéket próbál beállítani a Volume `protocolType` tulajdonságban.
* Megoldás:   
Állítson be egy érvényes karakterláncot a alkalmazásban `protocolType` .
* Megkerülő megoldás:   
`protocolType`Null értékűként van beállítva.

***A protokollok típusai nem módosíthatók***

Ez a hiba akkor fordul elő, ha egy kötet frissítését vagy javítását kísérli meg `ProtocolType` .  A ProtocolType módosítása nem támogatott művelet.

* Ok:   
A `ProtocolType` tulajdonság beállítása a kötet létrehozásakor történik.  Nem frissíthető.
* Megoldás:   
Nincsenek.
* Megkerülő megoldás:   
Hozzon létre egy másik kötetet új protokoll-típusokkal.

***A (z) {resourceType} típusú erőforrás létrehozása meghaladja a (z) {parentResourceType} {resourceType} típusú erőforrásokra vonatkozó kvótát. Az aktuális erőforrások száma {currentCount}, törölje az ilyen típusú erőforrásokat egy új létrehozása előtt.***

Ez a hiba akkor fordul elő, ha erőforrást ( `NetAppAccount` ,, `CapacityPool` vagy) próbál létrehozni `Volume` `Snapshot` , de a kvóta elérte a korlátot.

* Ok:   
Megpróbál létrehozni egy erőforrást, de elérte a kvóta korlátját (például: `NetAppAccounts` előfizetés vagy `CapacityPools` / `NetAppAccount` ).
* Megoldás:   
Növelje a kvóta korlátját.
* Megkerülő megoldás:   
Törölje a nem használt erőforrásokat ugyanazzal a típussal, majd hozza létre újra.

***A rendszer a (z) {propertyName} írásvédett tulajdonság értékét fogadta.***

Ez a hiba akkor fordul elő, ha olyan tulajdonság értékét definiálja, amely nem módosítható. A kötet azonosítója például nem módosítható.

* Ok:   
Egy olyan paramétert (például a kötet AZONOSÍTÓját) próbál módosítani, amely nem módosítható.
* Megoldás:   
Ne módosítsa a tulajdonság értékét.

***A kért {Resource} nem található.***

Ez a hiba akkor fordul elő, ha nem létező erőforrásra (például kötetre vagy pillanatképre) próbál hivatkozni. Lehetséges, hogy az erőforrás törölve lett, vagy hibásan van megadva az erőforrás neve.

* Ok:   
Olyan nem létező erőforrásra (például kötetre vagy pillanatképre) próbál hivatkozni, amely már törölve lett, vagy hibásan írt erőforrás-névvel rendelkezik.
* Megoldás:   
Ellenőrizze a helyesírási hibák kérését, és győződjön meg arról, hogy megfelelően hivatkozik rá.
* Megkerülő megoldás:   
Lásd a fenti megoldás szakaszt.

***A (z) {volumeServiceLevel} szolgáltatási szint magasabb a (z) {poolServiceLevel} szülőnél***

Ez a hiba akkor fordul elő, ha kötetet hoz létre vagy frissít, és a szolgáltatási szint magasabb szintre van állítva, mint az azt tartalmazó kapacitási készlet.

* Ok:   
Olyan kötetet próbál létrehozni vagy frissíteni, amely magasabb rangsorolt szolgáltatási szinttel rendelkezik, mint a szülő kapacitási készlet.
* Megoldás:   
Állítsa a szolgáltatási szintet a szülő kapacitási készletnél megegyezőre vagy alacsonyabb rangra.
* Megkerülő megoldás:   
Hozza létre a kötetet egy másik, megfelelő szolgáltatási szinttel rendelkező kapacitási készletben. Másik lehetőségként törölje az összes kötetet a kapacitási készletből, és állítsa be a kapacitási készlet szolgáltatási szintjét magasabb rangra.

***Az SMB-kiszolgáló neve nem lehet hosszabb 10 karakternél.***

Ez a hiba akkor fordul elő, ha egy fiók Active Directory konfigurációját hozza létre vagy frissíti.

* Ok:   
Az SMB-kiszolgáló nevének hossza meghaladja a 10 karaktert.
* Megoldás:   
Használjon rövidebb kiszolgálónevet. A maximális hossz 10 karakter.
* Megkerülő megoldás:   
Nincsenek.  Tekintse meg a fenti megoldást. 

***A denetid nem módosítható.***

Ez a hiba akkor fordul elő, ha a kötet létrehozása után megpróbálja módosítani a módosítást `subnetId` .  `SubnetId` a kötet létrehozásakor be kell állítani, és később nem módosítható.

* Ok:   
A `subnetId` kötet létrehozása után megpróbálja módosítani a kötetet, amely nem támogatott művelet. 
* Megoldás:   
Ha a módosítása `subnetId` nem szükséges, akkor érdemes lehet eltávolítani a paramétert a kérelemből a hibaüzenet elvetése érdekében.
* Megkerülő megoldás:   
Ha módosítania kell a `subnetId` -t, létrehozhat egy új kötetet új `subnetId` értékkel, majd áttelepítheti az új kötetre.

***A denetid formátuma érvénytelen.***

Ez a hiba akkor fordul elő, amikor új kötetet próbál létrehozni, de a `subnetId` nem `resourceId` alhálózathoz tartozik.

* Ok:   
Ez a hiba akkor fordul elő, amikor új kötetet próbál létrehozni, de a `subnetId` nem `resourceId` alhálózathoz tartozik. 
* Megoldás:   
Ellenőrizze a értékét, hogy az `subnetId` tartalmazza-e a `resourceId` használt alhálózatot.
* Megkerülő megoldás:   
Nincsenek. Tekintse meg a fenti megoldást. 

***Az alhálózatnak "Microsoft. NetApp/Volumes" delegálással kell rendelkeznie.***

Ez a hiba akkor fordul elő, ha kötetet hoz létre, és a kiválasztott alhálózat nincs delegálva `Microsoft.NetApp/volumes` .

* Ok:   
Megpróbált létrehozni kötetet, és olyan alhálózatot választott, amely nincs delegálva `Microsoft.NetApp/volumes` .
* Megoldás:   
Válasszon másik alhálózatot, amely delegált `Microsoft.NetApp/volumes` .
* Megkerülő megoldás:   
Adjon hozzá egy megfelelő delegálást az alhálózathoz.

***A megadott erőforrástípus ismeretlen/nem alkalmazható.***

Ez a hiba akkor fordul elő, ha a név-ellenőrzési kérelem nem alkalmazható erőforrástípus vagy ismeretlen erőforrástípus esetén lett kérelmezve.

* Ok:   
Ismeretlen vagy nem támogatott erőforrástípus számára kérték a név-ellenőrzését.
* Megoldás:   
Győződjön meg arról, hogy a kérést végző erőforrás támogatott vagy nem tartalmaz helyesírási hibát.
* Megkerülő megoldás:   
Tekintse meg a fenti megoldást.

***Ismeretlen Azure NetApp Files hiba.***

Az Azure API a Azure NetApp Files API-ra támaszkodik a kötetek kezeléséhez. A hiba az API-ra irányuló kommunikáció hibáját jelzi.

* Ok:   
Az alapul szolgáló API ismeretlen hibát küld. Ez a hiba valószínűleg átmeneti.
* Megoldás:   
A probléma valószínűleg ideiglenes lesz, és egy kis idő elteltével a kérésnek sikeresnek kell lennie. Ha a probléma továbbra is fennáll, hozzon létre egy támogatási jegyet a probléma kivizsgálásához.
* Megkerülő megoldás:   
Nincsenek. Az alapul szolgáló API elengedhetetlen a kötetek kezeléséhez.

***A (z) {propertyName} ismeretlen tulajdonsághoz kapott érték.***

Ez a hiba akkor fordul elő, ha nem létező tulajdonságok vannak megadva egy erőforráshoz, például a kötethez, a pillanatképhez vagy a csatlakoztatási célhoz.

* Ok:   
A kérelemben az egyes erőforrásokhoz használható tulajdonságok összessége látható. A kérelemben nem szerepelhetnek a nem létező tulajdonságok.
* Megoldás:   
Győződjön meg arról, hogy az összes tulajdonságnév helyesen van megjelölve, és hogy a tulajdonságok elérhetők az előfizetés és az erőforrás számára.
* Megkerülő megoldás:   
Csökkentse a kérésben definiált tulajdonságok számát a hibát okozó tulajdonság kizárása érdekében.

***Ez az erőforrástípus nem támogatja a frissítési műveletet.***

Csak kötetek frissíthetők. Ez a hiba akkor fordul elő, ha nem támogatott frissítési műveletet próbál végrehajtani, például egy pillanatkép frissítését.

* Ok:   
A frissíteni kívánt erőforrás nem támogatja a frissítési műveletet. Csak a kötetek módosíthatók a tulajdonságok.
* Megoldás:   
Nincsenek. A frissíteni kívánt erőforrás nem támogatja a frissítési műveletet. Ezért nem módosítható.
* Megkerülő megoldás:   
Kötet esetén hozzon létre egy új erőforrást a frissítéssel, és telepítse át az adatátvitelt.

***A kötet nem hozható létre olyan készletben, amely állapota nem sikerült.***

Ez a hiba akkor fordul elő, ha olyan kötetet próbál létrehozni egy olyan készletben, amely nem sikeres állapotban van. A kapacitási készlet létrehozási művelete valószínűleg valamilyen okból meghiúsult.

* Ok:   
Az új kötetet tartalmazó kapacitási készlet hibás állapotban van.
* Megoldás:   
Győződjön meg arról, hogy a kapacitás-készlet létrehozása sikeres volt, és hogy nem meghibásodott állapotban van.
* Megkerülő megoldás:   
Hozzon létre egy új kapacitási készletet, és hozza létre a kötetet az új készletben.

***A kötet létrehozása folyamatban van, és jelenleg nem törölhető.***

Ez a hiba akkor fordul elő, ha olyan kötetet próbál meg törölni, amely még létre van hozva.

* Ok:   
A kötet törlésének megkísérlésekor a kötet létrehozása még folyamatban van.
* Megoldás:   
Várjon, amíg a kötet létrehozása befejeződik, majd próbálja megismételni a törlést.
* Megkerülő megoldás:   
Tekintse meg a fenti megoldást.

***A kötet törlése folyamatban van, és jelenleg nem törölhető.***

Ez a hiba akkor fordul elő, ha olyan kötetet próbál törölni, amely már törölve van.

* Ok:   
A kötet törlésének megkísérlésekor a kötet már törölve lett.
* Megoldás:   
Várjon, amíg befejeződik az aktuális törlési művelet.
* Megkerülő megoldás:   
Tekintse meg a fenti megoldást.

***A kötet frissítése folyamatban van, és jelenleg nem törölhető.***

Ez a hiba akkor fordul elő, ha egy frissítendő kötetet próbál törölni.

* Ok:   
A kötet törlésének megkísérlésekor a kötet frissítése folyamatban van.
* Megoldás:   
Várjon, amíg a frissítési művelet befejeződik, majd próbálja megismételni a törlést.
* Megkerülő megoldás:   
Tekintse meg a fenti megoldást.

***A kötet nem található, vagy nem sikerült létrehozni.***

Ez a hiba akkor fordul elő, ha a kötet létrehozása meghiúsult, és megpróbálja módosítani a kötetet, vagy pillanatképet létrehozni a kötethez.

* Ok:   
A kötet nem létezik, vagy a létrehozás sikertelen volt.
* Megoldás:   
Győződjön meg arról, hogy a megfelelő kötetet módosítja, és hogy a kötet létrehozása sikeres volt. Vagy győződjön meg arról, hogy a pillanatképet létrehozó kötet létezik.
* Megkerülő megoldás:   
Nincsenek.  Tekintse meg a fenti megoldást. 

***A megadott létrehozási jogkivonat már létezik***

Ez a hiba akkor fordul elő, amikor kötetet próbál létrehozni, és megad egy létrehozási jogkivonatot (exportálási útvonalat), amelyhez a kötet már létezik.

* Ok:   
A kötet létrehozása során megadott létrehozási jogkivonat (exportálási útvonal) már egy másik kötethez van társítva. 
* Megoldás:   
Válasszon másik létrehozási tokent.  Másik lehetőségként törölje a másik kötetet.

***A megadott létrehozási jogkivonat foglalt***

Ez a hiba akkor fordul elő, amikor kötetet próbál létrehozni, és az "alapértelmezett" vagy "None" értéket adta meg a fájl elérési útjaként (létrehozási jogkivonat).

* Ok:    
Kötetet próbál létrehozni, és az "alapértelmezett" vagy "None" értéket adta meg a fájl elérési útjaként (létrehozási jogkivonat).
* Megoldás:   
Válasszon másik elérési utat (létrehozási jogkivonat).
 
***Active Directory hitelesítő adatok használatban vannak***

Ez a hiba akkor fordul elő, ha a Active Directory konfigurációt olyan fiókból próbálja meg törölni, amelyben legalább egy SMB-kötet még létezik.  Az SMB-kötet a törölni kívánt Active Directory konfiguráció használatával lett létrehozva.

* Ok:   
Egy fiókból kísérli meg törölni a Active Directory konfigurációt, de legalább egy SMB-kötet még létezik, amelyet eredetileg a Active Directory-konfiguráció használatával hoztak létre. 
* Megoldás:   
Először törölje a Active Directory konfiguráció használatával létrehozott összes SMB-kötetet.  Ezután próbálja megismételni a konfiguráció törlését.

***A szervezeti egység hozzárendelése nem módosítható, ha a hitelesítő adatok használatban vannak***

Ez a hiba akkor fordul elő, ha egy Active Directory konfiguráció szervezeti egységét próbálja meg módosítani, de legalább egy SMB-kötet továbbra is létezik.  Az SMB-kötetet a törölni kívánt Active Directory konfiguráció használatával hozták létre.

* Ok:   
Egy Active Directory konfiguráció szervezeti egységét próbálja módosítani.  De legalább egy SMB-kötet még létezik, amelyet kezdetben a Active Directory konfiguráció használatával hoztak létre.
* Megoldás:   
 Először törölje a Active Directory konfiguráció használatával létrehozott összes SMB-kötetet.  Ezután próbálja megismételni a konfiguráció törlését. 

***Active Directory frissítés már folyamatban van***

Ez a hiba akkor fordul elő, ha olyan Active Directory-konfigurációt próbál meg szerkeszteni, amelyhez már folyamatban van egy szerkesztési művelet.

* Ok:   
Egy Active Directory konfigurációt próbál szerkeszteni, de már folyamatban van egy másik szerkesztési művelet.
* Megoldás:   
Várjon, amíg a jelenleg futó szerkesztési művelet befejeződik.

***Az összes kötet törlése a kiválasztott hitelesítő adatokkal először***

Ez a hiba akkor fordul elő, amikor egy Active Directory konfigurációt próbál törölni, de legalább egy SMB-kötet továbbra is létezik.  Az SMB-kötet a törölni kívánt Active Directory konfiguráció használatával lett létrehozva.

* Ok:   
Egy Active Directory konfiguráció törlését kísérli meg, de legalább egy SMB-kötet még létezik, amelyet eredetileg a Active Directory-konfiguráció használatával hoztak létre.
* Megoldás:   
Először törölje a Active Directory konfiguráció használatával létrehozott összes SMB-kötetet.  Ezután próbálja megismételni a konfiguráció törlését. 

***Nem található Active Directory hitelesítő adat a régióban***

Ez a hiba akkor fordul elő, amikor SMB-kötetet próbál létrehozni, de nincs Active Directory konfiguráció hozzáadva a fiókhoz a régióhoz.

* Ok:   
SMB-kötetet próbál létrehozni, de nincs Active Directory konfiguráció hozzáadva a fiókhoz. 
* Megoldás:   
SMB-kötet létrehozása előtt adjon hozzá egy Active Directory konfigurációt a fiókhoz.

***Nem lehetett lekérdezni a DNS-kiszolgálót. Ellenőrizze, hogy a hálózati konfiguráció helyes-e, és hogy a DNS-kiszolgálók elérhetők-e.***

Ez a hiba akkor fordul elő, amikor SMB-kötetet próbál létrehozni, de a Active Directory konfigurációjában megadott DNS-kiszolgáló nem érhető el. 

* Ok:   
SMB-kötetet próbál létrehozni, de a Active Directory konfigurációjában megadott DNS-kiszolgáló nem érhető el.
* Megoldás:   
Tekintse át a Active Directory konfigurációját, és győződjön meg arról, hogy a DNS-kiszolgáló IP-címei helyesek és elérhetők.
Ha nincs probléma a DNS-kiszolgáló IP-címeivel, akkor győződjön meg arról, hogy egyetlen tűzfal sem blokkolja a hozzáférést.

***Túl sok egyidejű feladat***

Ez a hiba akkor fordul elő, amikor pillanatképet próbál létrehozni, ha az előfizetés három másik pillanatkép-létrehozási művelete már folyamatban van.

* Ok:   
Létre kell hoznia egy pillanatképet, ha három másik pillanatkép-létrehozási művelet már folyamatban van az előfizetéshez. 
* Megoldás:   
A pillanatkép-létrehozási feladatok elvégzése több másodpercig is eltarthat.  Várjon néhány másodpercet, és próbálkozzon újra a pillanatkép-létrehozási művelettel.

***További feladatok nem állíthatók be. Várjon, amíg a folyamatban lévő feladatok befejeződik, és próbálkozzon újra***

Ez a hiba akkor fordulhat elő, ha adott körülmények között megpróbál létrehozni vagy törölni egy kötetet.

* Ok:   
Adott körülmények között megpróbál létrehozni vagy törölni egy kötetet.
* Megoldás:   
Várjon egy percet, és ismételje meg a műveletet.

***A kötet már átmeneti állapotú***

Ez a hiba akkor fordulhat elő, ha olyan kötetet próbál törölni, amely jelenleg átmeneti állapotban van (azaz jelenleg a létrehozás, frissítés vagy törlés állapotban van).

* Ok:   
Olyan kötetet próbál törölni, amely jelenleg átmeneti állapotban van.
* Megoldás:   
Várjon, amíg a jelenleg futó (állapot-áttérési) művelet befejeződik, majd próbálja megismételni a műveletet.

***Nem sikerült az új kötet felosztása a forrás kötet-pillanatképből***

 Ez a hiba akkor fordulhat elő, ha kötetet próbál létrehozni egy pillanatképből.  

* Ok:   
Megpróbál létrehozni egy kötetet egy pillanatképből, és a kötet hibás állapotba végződik.
* Megoldás:   
Törölje a kötetet, majd próbálja megismételni a kötet-létrehozási műveletet a pillanatképből.

 
## <a name="next-steps"></a>Következő lépések

* [Fejlesztés az Azure NetApp Filesra a REST API-val](azure-netapp-files-develop-with-rest-api.md)
