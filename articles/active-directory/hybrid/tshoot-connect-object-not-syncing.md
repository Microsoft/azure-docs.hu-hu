---
title: A Azure Active Directorykal nem szinkronizált objektumok hibáinak megoldása | Microsoft Docs "
description: A Azure Active Directorykal nem szinkronizált objektumok hibáinak megoldása.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a0c8a42edad08308095469039c048f8dd8552af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94413462"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-with-azure-active-directory"></a>Az Azure Active Directory-val nem szinkronizált objektumok hibáinak megoldása

Ha egy objektum nem a várt módon szinkronizálódik a Microsoft Azure Active Directory (Azure AD) használatával, több okból is előfordulhat. Ha hibaüzenetet kapott az Azure AD-től, vagy a Azure AD Connect Health hibát észlelt, olvassa el a [hibaelhárítási hibákat a szinkronizáció során](tshoot-connect-sync-errors.md) . Ha azonban olyan problémát észlel, amelyben az objektum nem az Azure AD-ben van, akkor ez a cikk Önnek szól. Ismerteti, hogyan lehet hibákat keresni a helyszíni összetevőben Azure AD Connect a szinkronizálást.

>[!IMPORTANT]
>A 1.1.749.0 vagy újabb verzióval rendelkező Azure AD Connect telepítéséhez használja a varázsló [hibaelhárítási feladatát](tshoot-connect-objectsync.md) az objektumok szinkronizálásával kapcsolatos problémák elhárításához. 

## <a name="synchronization-process"></a>Szinkronizálási folyamat

A szinkronizálási problémák megvizsgálása előtt Ismerkedjen meg a Azure AD Connect szinkronizálási folyamattal:

  ![Azure AD Connect szinkronizálási folyamat ábrája](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminológia**

* **CS:** Összekötő terület, adatbázisbeli tábla
* **MV:** Metaverse, egy tábla egy adatbázisban

### <a name="synchronization-steps"></a>**Szinkronizálás lépései**
A szinkronizálási folyamat a következő lépéseket foglalja magában:

1. **Importálás az ad-ből:** Active Directory objektumokat a rendszer a Active Directory CS-ba helyezi át.

2. **Importálás az Azure ad-ből:** Az Azure AD-objektumok bekerülnek az Azure AD CS-ba.

3. **Szinkronizálás:** A bejövő szinkronizálási szabályok és a kimenő szinkronizálási szabályok elsőbbségi szám sorrendben futnak, az alacsonyabbtól a magasabbig. A szinkronizálási szabályok megtekintéséhez nyissa meg a szinkronizálási szabályok szerkesztőjét az asztali alkalmazásokban. A bejövő szinkronizálási szabályok a CS és az MV közötti adatbevitelt eredményezik. A kimenő szinkronizálási szabályok MV-ból CS-ra helyezik át az adatforgalmat.

4. **Exportálás az ad-be:** A szinkronizálást követően a rendszer az objektumokat a Active Directory CS-ból Active Directoryba exportálja.

5. **Exportálás az Azure ad-be:** A szinkronizálást követően az objektumok az Azure AD CS-ből az Azure AD-be lesznek exportálva.

## <a name="troubleshooting"></a>Hibaelhárítás

A hibák megkereséséhez tekintse meg néhány különböző helyet a következő sorrendben:

1. A [művelet](#operations) a szinkronizálási motor által az Importálás és a szinkronizálás során azonosított hibákat keresi.
2. Az [összekötő területe](#connector-space-object-properties) hiányzó objektumok és szinkronizálási hibák kereséséhez.
3. A [metaverse](#metaverse-object-properties) az adatokkal kapcsolatos problémákat keresi.

A lépések megkezdése előtt indítsa el [synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) .

## <a name="operations"></a>Üzemeltetés
A Synchronization Service Manager **Operations (műveletek** ) lapján kell elindítania a hibaelhárítást. Ezen a lapon a legutóbbi műveletek eredményei láthatók. 

![Synchronization Service Manager képernyőképe – a műveletek lap kijelölve](./media/tshoot-connect-object-not-syncing/operations.png)  

Az **Operations (műveletek** ) lap felső fele az összes Futtatás időrendi sorrendben jelenik meg. Alapértelmezés szerint az operatív napló az elmúlt hét nap adatait tárolja, de ez a beállítás az [ütemező](how-to-connect-sync-feature-scheduler.md)segítségével módosítható. Keresse meg az összes olyan futtatást, amely nem jeleníti meg a **sikerességi** állapotot. A rendezést a fejlécek lehetőségre kattintva módosíthatja.

Az **állapot** oszlop a legfontosabb információkat tartalmazza, és megjeleníti a Futtatás legsúlyosabb problémáját. Íme egy rövid összefoglalás a leggyakoribb állapotokról a vizsgálat prioritási sorrendjében (ahol a * több lehetséges hibát jelez).

| Állapot | Megjegyzés |
| --- | --- |
| leállítva – * |A Futtatás nem fejezhető be. Ez akkor fordulhat elő, ha például a távoli rendszer nem érhető el, és nem lehet kapcsolatba lépni vele. |
| leállítva – hiba – korlát |Több mint 5 000 hiba van. A Futtatás a hibák nagy száma miatt automatikusan leállt. |
| befejezve – \* hibák |A Futtatás befejeződött, de vannak olyan hibák (kevesebb mint 5 000), amelyet meg kell vizsgálni. |
| befejezve – \* figyelmeztetések |A Futtatás befejeződött, de bizonyos adatértékek nem a várt állapotban vannak. Ha hibákat észlel, ez az üzenet általában csak tünet. Ne vizsgálja meg a figyelmeztetéseket, amíg nem válaszol a hibákra. |
| sikeres |Nincs probléma. |

Amikor kijelöl egy sort, a rendszer frissíti az **Operations (műveletek** ) lap alját, hogy megjelenítse a Futtatás részleteit. A terület bal oldali részén lehet, hogy egy lista szerepel a következő **lépésben: #**. Ez a lista csak akkor jelenik meg, ha az erdőben több tartomány található, és minden tartományt egy lépés képvisel. A tartománynév a fejléc **partíció** alatt található. A **szinkronizálási statisztika** fejlécében további információkat talál a feldolgozott módosítások számáról. Válassza ki a hivatkozásokat a módosított objektumok listájának lekéréséhez. Ha hibás objektumok vannak, ezek a hibák a **szinkronizálási hibák** fejlécében jelennek meg.

### <a name="errors-on-the-operations-tab"></a>Hibák az Operations (műveletek) lapon
Ha hibák léptek fel, a Synchronization Service Manager a hibás objektumot és a hibát is a további információkat tartalmazó hivatkozásként jeleníti meg.

![Képernyőkép a Synchronization Service Manager lévő hibákról](./media/tshoot-connect-object-not-syncing/errorsync.png)  
Először válassza a hiba karakterláncot. (Az előző ábrán a hiba sztring **szinkronizálási szabály – hiba – függvény – aktiválva**.) Először az objektum áttekintését mutatjuk be. A tényleges hiba megtekintéséhez válassza a **verem nyomon követése** elemet. Ez a nyomkövetés hibakeresési szintű információt biztosít a hibához.

Kattintson a jobb gombbal a **hívási verem adatai** mezőre, kattintson az **összes kijelölése** elemre, majd válassza a **Másolás** lehetőséget. Ezután másolja a verembe, és tekintse meg a hibát a kedvenc szerkesztőben, például a Jegyzettömbben.

Ha a hiba a **SyncRulesEngine**-ből származik, a hívási verem adatai először az objektum összes attribútumát listázza. Görgessen lefelé, amíg meg nem jelenik a **InnerException =>** fejléc.  

  ![Képernyőkép a Synchronization Service Managerről, amely a InnerException => vámtarifaszám alá tartozó hibákat mutatja.](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)
  
A fejléc utáni sor a hibát mutatja. Az előző ábrán a hiba egy, a fabrikam által létrehozott egyéni szinkronizálási szabályból származik.

Ha a hiba nem ad elég információt, ideje megtekinteni az adatokat. Válassza ki az objektumazonosító hivatkozását, és folytassa a hibaelhárítást az [összekötő terület importált objektumával](#cs-import)kapcsolatban.

## <a name="connector-space-object-properties"></a>Összekötőtér-objektum tulajdonságai
Ha az [**Operations (műveletek**](#operations) ) lap nem jelenít meg hibákat, kövesse az összekötő területét Active Directoryról a metaverse – Azure ad-be. Ezen az elérési úton megtalálja a probléma helyét.

### <a name="searching-for-an-object-in-the-cs"></a>Objektum keresése a CS-ban

Az Synchronization Service Manager területen válassza az **Összekötők** lehetőséget, válassza ki a Active Directory-összekötőt, és válassza a **Keresés összekötő terület** lehetőséget.

A **hatókör** mezőben válassza a **RDN** elemet, ha a CN attribútumon szeretne keresni, vagy válassza a **DN vagy a Anchor** lehetőséget, ha a **distinguishedName** attribútumon szeretne keresni. Adjon meg egy értéket, és válassza a **Keresés** lehetőséget. 
 
![Képernyőkép az összekötő területének kereséséről](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Ha nem találja a keresett objektumot, előfordulhat, hogy a [rendszer tartományalapú szűréssel](how-to-connect-sync-configure-filtering.md#domain-based-filtering) vagy [ou-alapú szűréssel](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)szűrt. Annak ellenőrzéséhez, hogy a szűrés a várt módon van-e konfigurálva, olvassa el [Azure ad Connect szinkronizálás: a szűrés konfigurálása](how-to-connect-sync-configure-filtering.md).

Az Azure AD-összekötő kiválasztásával más hasznos keresést is végrehajthat. A **hatókör** mezőben válassza a **függőben lévő importálás** lehetőséget, majd jelölje be a **Hozzáadás** jelölőnégyzetet. Ez a Keresés az Azure AD összes olyan szinkronizált objektumát tartalmazza, amely nem társítható helyszíni objektumhoz.  

![Képernyőkép az árvákról az összekötő területének keresésekor](./media/tshoot-connect-object-not-syncing/cssearchorphan.png) 
 
Ezeket az objektumokat egy másik szinkronizálási motor vagy egy másik szűrési konfigurációval rendelkező szinkronizációs motor hozta létre. Ezek az árva objektumok már nem kezelhetők. Tekintse át a listát, és távolítsa el ezeket az objektumokat az [Azure ad PowerShell](/previous-versions/azure/jj151815(v=azure.100)) -parancsmagok használatával.

### <a name="cs-import"></a>CS importálás
A CS-objektumok megnyitásakor több lap van a tetején. Az **Importálás** lapon az importálás után elkészített adatértékek láthatók.  

![Képernyőkép az összekötő terület objektumról Tulajdonságok ablak, az importálás lapon kiválasztva](./media/tshoot-connect-object-not-syncing/csobject.png)    

A **régi érték** oszlopban látható, hogy mit tárol a rendszer a kapcsolatban, és az **új érték** oszlopban látható, hogy mit kapott a forrásrendszer, és még nincs alkalmazva. Ha hiba van az objektumon, a módosítások nem lesznek feldolgozva.

A **szinkronizálási hiba** lap csak akkor látható az **összekötő terület objektum tulajdonságai** ablakban, ha probléma van az objektummal. További információkért tekintse át [a szinkronizálási hibák elhárítása a **műveletek** lapon](#errors-on-the-operations-tab)című témakört.

![Képernyőkép a szinkronizációs hiba lapról az összekötő terület objektumban Tulajdonságok ablak](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  

### <a name="cs-lineage"></a>CS-Lineage
Az **összekötő terület objektum tulajdonságai** ablakban a **Lineage** lap mutatja, hogy az összekötő terület objektum a metaverse objektumhoz kapcsolódik-e. Láthatja, ha az összekötő utoljára importálta a csatlakoztatott rendszer változását, és hogy mely szabályok vonatkoznak a metaverse adatainak feltöltésére.  

![Az összekötő terület objektumának Lineage lapját ábrázoló képernyőkép Tulajdonságok ablak](./media/tshoot-connect-object-not-syncing/cslineage.png)  

Az előző ábrán a **művelet** oszlop egy bejövő szinkronizálási szabályt mutat be a művelet **kiépítésekor**. Ez azt jelzi, hogy ha ez az összekötő szóköz van jelen, a metaverse objektum marad. Ha a szinkronizálási szabályok listája Ehelyett egy **kiépítési** művelettel rendelkező kimenő szinkronizálási szabályt mutat, akkor ez az objektum törlődik a metaverse-objektum törlésekor.  

![Képernyőkép az összekötő-terület objektumának Lineage lapján lévő Lineage (lenyíló) ablakról Tulajdonságok ablak](./media/tshoot-connect-object-not-syncing/cslineageout.png)  

Az előző ábrán látható, hogy a **PasswordSync** oszlopban a bejövő összekötő területe is hozzájárulhat a jelszó módosításához, mert az egyik szinkronizálási szabály értéke TRUE ( **igaz**). Ezt a jelszót a rendszer a kimenő szabályon keresztül elküldi az Azure AD-nek.

A **Lineage** lapon a metaverse [**objektum tulajdonságainak**](#mv-attributes)kiválasztásával érheti el a metaverse-t.

### <a name="preview"></a>Előnézet
Az **összekötő terület objektum Tulajdonságok** ablakának bal alsó sarkában az **előnézet** gomb jelenik meg. Ezzel a gombbal megnyithatja az **előnézet** lapot, ahol egyetlen objektumot is szinkronizálhat. Ez az oldal akkor hasznos, ha néhány egyéni szinkronizálási szabályt elhárít, és szeretné megtekinteni egy adott objektum változásának hatását. Választhatja a **teljes szinkronizálást** vagy a **különbözeti szinkronizálást**. Kiválaszthatja az **előnézet létrehozása** lehetőséget is, amely csak a memóriában megjelenő változást tárolja. Vagy válassza a **véglegesítés várható eredménye** lehetőséget, amely frissíti a metaverse-t, és a megcélzott összekötők tárolóhelyeit érintő összes változást.  

![Képernyőkép az előnézet oldalról, a Start Preview kiválasztva](./media/tshoot-connect-object-not-syncing/preview.png)  

Az előzetes verzióban megvizsgálhatja az objektumot, és megtekintheti, hogy melyik szabály vonatkozik egy adott attribútum folyamatára.  

![Képernyőkép az előnézet oldalról, amely az attribútum-folyamat importálását mutatja](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Napló
Az **előnézet** gomb mellett kattintson a **napló** gombra a **napló** oldal megnyitásához. Itt láthatja a jelszó-szinkronizálás állapotát és előzményeit. További információ: jelszó- [kivonatolási szinkronizálás hibáinak megoldása Azure ad Connect szinkronizálással](tshoot-connect-password-hash-synchronization.md).

## <a name="metaverse-object-properties"></a>Metaverse-objektum tulajdonságai
Általában jobb, ha megkezdi a keresést a forrás Active Directory-összekötő területéről. De a metaverse használatával is megkezdheti a keresést.

### <a name="searching-for-an-object-in-the-mv"></a>Objektum keresése az MV-ban
A Synchronization Service Managerban válassza a **metaverse Search** lehetőséget, ahogy az az alábbi ábrán is látható. Hozzon létre egy lekérdezést, amelyről tudja, hogy megkeresi a felhasználót. Általános attribútumok, például **accountName** (**sAMAccountName**) és **userPrincipalName** keresése. További információ: [Sync Service Manager metaverse Search](how-to-connect-sync-service-manager-ui-mvsearch.md).

![Képernyőkép a Synchronization Service Managerről, és a metaverse-Keresés lap van kiválasztva](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

A **keresési eredmények** ablakban kattintson az objektumra.

Ha nem találta meg az objektumot, még nem érte el a metaverse-t. Folytassa az objektum keresését az Active Directory- [összekötő területén](#connector-space-object-properties). Ha megtalálta az objektumot a Active Directory-összekötő területén, akkor lehet, hogy szinkronizálási hiba történt, amely blokkolja az objektumot a metaverse felé, vagy a szinkronizálási szabály hatóköre szűrőt is alkalmazhatja.

### <a name="object-not-found-in-the-mv"></a>Az objektum nem található az MV-ban
Ha az objektum a Active Directory CS, de nem szerepel az MV-ban, a rendszer egy hatókör-szűrőt alkalmaz. A hatókör szűrő megjelenítéséhez nyissa meg a Desktop alkalmazás menüt, és válassza a **szinkronizációs szabályok szerkesztő** elemet. Szűrje az objektumra vonatkozó szabályokat az alábbi szűrő módosításával.

  ![Képernyőkép a szinkronizálási szabályok szerkesztőjéről, amely a bejövő szinkronizálási szabályok keresését mutatja](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

Tekintse meg a lista egyes szabályait, és tekintse meg a **hatókör szűrőt**. A következő hatókör-szűrőben, ha a **isCriticalSystemObject** értéke null vagy hamis vagy üres, akkor a hatóköre.

  ![Képernyőfelvétel a bejövő szinkronizálási szabályban található hatóköri szűrőről](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

Lépjen a [cs importálási](#cs-import) attribútumok listájához, és győződjön meg arról, hogy melyik szűrő blokkolja az OBJEKTUMOT az MV-ra való áttéréssel. Az **összekötő terület** attribútuma listában csak a nem null és a nem üres attribútumok jelennek meg. Ha például a **isCriticalSystemObject** nem jelenik meg a listában, akkor az attribútum értéke null vagy üres.

### <a name="object-not-found-in-the-azure-ad-cs"></a>Az objektum nem található az Azure AD CS-ban
Ha az objektum nem található az Azure AD-összekötő területén, de az MV-ban található, tekintse meg a megfelelő összekötő-terület kimenő szabályainak hatóköri szűrőjét, és ellenőrizze, hogy az objektum ki van-e szűrve, mert az [MV attribútumok](#mv-attributes) nem felelnek meg a feltételeknek.

A kimenő hatóköri szűrő megkereséséhez válassza ki az objektumra vonatkozó szabályokat az alábbi szűrő beállításával. Tekintse meg az egyes szabályokat, és tekintse meg a megfelelő [MV attribútumérték](#mv-attributes) értékét.

  ![Képernyőkép a kimenő szinkronizálási szabályok kereséséről a szinkronizálási szabályok szerkesztőjében](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>MV-attribútumok
Az **attribútumok** lapon megtekintheti az értékeket, és hogy mely összekötők járultak hozzájuk.  

![Képernyőfelvétel a metaverse-objektumról Tulajdonságok ablakról, az attribútumok lapon kiválasztva](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Ha egy objektum nem szinkronizál, kérdezze meg a következő kérdéseket a metaverse attribútum-állapotáról:
- Az attribútum **cloudFiltered** van, és **igaz** értékre van állítva? Ha igen, az [attribútum-alapú szűrés](how-to-connect-sync-configure-filtering.md#attribute-based-filtering)lépéseinek megfelelően szűrve lett.
- A **sourceAnchor** attribútum jelen van? Ha nem, van egy fiók-erőforrás erdő-topológiája? Ha egy objektum csatolt postaládaként van azonosítva (a **msExchRecipientTypeDetails** attribútum **2** értékkel rendelkezik), akkor a **sourceAnchor** az erdő az engedélyezett Active Directory fiókkal járul hozzá. Győződjön meg arról, hogy a fő fiók importálása és szinkronizálása megfelelő volt. A fő fióknak szerepelnie kell az objektum [összekötői](#mv-connectors) között.

### <a name="mv-connectors"></a>MV-összekötők
Az **Összekötők** lapon az összes olyan összekötő látható, amely az objektum ábrázolásával rendelkezik. 
 
![Képernyőfelvétel a metaverse-objektumról Tulajdonságok ablakről, az összekötők lapon kiválasztva](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  

Rendelkeznie kell egy összekötővel a következőhöz:

- Minden Active Directory erdő, amelyet a felhasználó képvisel. Ez a képviselet tartalmazhat **foreignSecurityPrincipals** és **kapcsolattartási** objektumokat is.
- Egy összekötő az Azure AD-ben.

Ha hiányzik az összekötő az Azure AD-hoz, tekintse át az [MV-attribútumok](#mv-attributes) szakaszt az Azure ad-ben való kiépítés feltételeinek ellenőrzéséhez.

Az **Összekötők lapon az** [összekötő terület objektumra](#connector-space-object-properties)is léphet. Jelöljön ki egy sort, és kattintson a **Tulajdonságok** elemre.

## <a name="next-steps"></a>Következő lépések
- További információ a [Azure ad Connect szinkronizálásról](how-to-connect-sync-whatis.md).
- További információ a [hibrid identitásról](whatis-hybrid-identity.md).