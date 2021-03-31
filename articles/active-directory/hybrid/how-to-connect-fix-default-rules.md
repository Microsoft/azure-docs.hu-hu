---
title: Módosított alapértelmezett szabályok javítása – Azure AD Connect | Microsoft Docs
description: Megtudhatja, hogyan javíthatja a Azure AD Connecthoz tartozó módosított alapértelmezett szabályokat.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0fc1bc3158e04c9b1f677af7ef2375ac3ed2ce7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91320047"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Módosított alapértelmezett szabályok javítása Azure AD Connect

A Azure Active Directory (Azure AD) kapcsolat alapértelmezett szabályokat használ a szinkronizáláshoz.  Sajnos ezek a szabályok nem vonatkoznak általánosan az összes szervezetre. A követelmények alapján előfordulhat, hogy módosítania kell őket. Ez a cikk két példát ismertet a leggyakoribb testreszabásokra, és ismerteti a testreszabások helyes elérési módját.

>[!NOTE] 
> A meglévő alapértelmezett szabályok módosítása a szükséges Testreszabás eléréséhez nem támogatott. Ha így tesz, a későbbi kiadásokban megakadályozza a szabályok frissítését a legújabb verzióra. A szükséges hibajavítások és új funkciók nem lesznek elérhetők. Ez a dokumentum azt ismerteti, hogyan lehet ugyanazt az eredményt elérni a meglévő alapértelmezett szabályok módosítása nélkül. 

## <a name="how-to-identify-modified-default-rules"></a>Módosított alapértelmezett szabályok azonosítása
Azure AD Connect verziójának 1.3.7.0 kezdve egyszerűen azonosítható a módosított alapértelmezett szabály. Nyissa **meg az alkalmazások Desktop alkalmazást**, és válassza a **szinkronizációs szabályok szerkesztő** elemet.

![Azure AD Connect, a szinkronizálási szabályok szerkesztője kiemelve](media/how-to-connect-fix-default-rules/default1.png)

A szerkesztőben minden módosított alapértelmezett szabály megjelenik egy figyelmeztetés ikonnal a név előtt.

![Figyelmeztetés ikon](media/how-to-connect-fix-default-rules/default2.png)

 A mellette egy ugyanilyen nevű letiltott szabály is megjelenik (ez a szabványos alapértelmezett szabály).

![Szinkronizációs szabályok szerkesztője, amely a szabványos alapértelmezett szabályt és a módosított alapértelmezett szabályt mutatja](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Gyakori testreszabások
Az alapértelmezett szabályok az alábbi általános beállítások:

- Attribútum folyamatának módosítása
- Hatókör-szűrő módosítása
- Csatlakozási feltétel módosítása

A szabályok módosítása előtt:

- Tiltsa le a szinkronizálási ütemező funkciót. Alapértelmezés szerint az ütemező 30 percenként fut. Győződjön meg róla, hogy nem indul el, amíg a módosításokat végez, és elhárítja az új szabályokat. Az ütemező ideiglenes letiltásához indítsa el a PowerShellt, és futtassa a parancsot `Set-ADSyncScheduler -SyncCycleEnabled $false` .
 ![PowerShell-parancsok a szinkronizálási ütemező letiltásához](media/how-to-connect-fix-default-rules/default3.png)

- A hatókörbeli szűrő módosítása a cél könyvtárban lévő objektumok törlését eredményezheti. Ügyeljen arra, hogy az objektumok hatókörében bármilyen változást ne végezzen. Azt javasoljuk, hogy az aktív kiszolgálón végzett módosítások előtt módosítsa az átmeneti kiszolgálót.
- Egy új szabály hozzáadása után futtasson egy előnézetet egyetlen objektumon, a [szinkronizálási szabály érvényesítése](#validate-sync-rule) szakaszban említettek szerint.
- Új szabály hozzáadása vagy egyéni szinkronizálási szabály módosítása után futtasson teljes szinkronizálást. Ez a szinkronizálás új szabályokat alkalmaz az összes objektumra.

## <a name="change-attribute-flow"></a>Attribútum folyamatának módosítása
Három különböző forgatókönyv van az attribútum folyamatának módosítására:
- Új attribútum hozzáadása.
- Egy meglévő attribútum értékének felülbírálása.
- Meglévő attribútum szinkronizálásának mellőzése.

Ezeket a szabványos alapértelmezett szabályok módosítása nélkül végezheti el.

### <a name="add-a-new-attribute"></a>Új attribútum hozzáadása
Ha azt tapasztalja, hogy egy attribútum nem áramlik a forrás könyvtárából a célkiszolgálóra, akkor a [Azure ad Connect Sync: Directory-bővítmények](how-to-connect-sync-feature-directory-extensions.md) segítségével javítsa ezt.

Ha a bővítmények nem működnek, próbálkozzon a következő szakaszokban ismertetett két új szinkronizálási szabály hozzáadásával.


#### <a name="add-an-inbound-sync-rule"></a>Bejövő szinkronizálási szabály hozzáadása
A bejövő szinkronizálási szabály azt jelenti, hogy az attribútum forrása egy összekötő terület, és a cél a metaverse. Ha például egy új attribútumot szeretne létrehozni a helyszíni Active Directoryról Azure Active Directoryra, hozzon létre egy új bejövő szinkronizálási szabályt. Indítsa el a **szinkronizálási szabályok szerkesztőjét**, válassza a **bejövő** lehetőséget irányként, majd kattintson az **új szabály hozzáadása** lehetőségre. 

 ![Képernyőfelvétel: "beérkező" és "új szabály hozzáadása" beállítású "szinkronizációs szabályok szerkesztője".](media/how-to-connect-fix-default-rules/default3a.png)

A szabály elnevezéséhez kövesse a saját elnevezési konvenciót. Itt a Custom (egyéni) lehetőséget használjuk az **ad-User** szolgáltatásból. Ez azt jelenti, hogy a szabály egy egyéni szabály, amely a Active Directory-összekötő területéről a metaverse felé irányuló Bejövő szabály.   

 ![Bejövő szinkronizálási szabály létrehozása](media/how-to-connect-fix-default-rules/default3b.png)

Adja meg a szabály saját leírását, hogy a szabály jövőbeli karbantartása egyszerű legyen. A Leírás lehet például a szabály célja, és miért van rá szükség.

Adja meg a **csatlakoztatott rendszer**, a **csatlakoztatott rendszerobjektum típusa** és a **metaverse objektumtípus** mezők beállításait.

Határozza meg a 0 és 99 közötti prioritási értéket (minél kisebb a szám, annál nagyobb a prioritás). A **címke**, a **Jelszó-szinkronizálás engedélyezése** és a **letiltott** mezők beállításnál használja az alapértelmezett beállításokat.

Hagyja üresen a **hatókör-szűrőt** . Ez azt jelenti, hogy a szabály a Active Directory csatlakoztatott rendszer és a metaverse között csatlakozó összes objektumra vonatkozik.

Az **illesztési szabályok** megtartása üres. Ez azt jelenti, hogy ez a szabály a szabványos alapértelmezett szabályban definiált illesztési feltételt használja. Ez egy másik ok, amiért nem lehet letiltani vagy törölni a szabványos alapértelmezett szabályt. Ha nincs összekapcsolási feltétel, az attribútum nem fog folyni. 

Adja hozzá a megfelelő átalakításokat az attribútumhoz. Állandó érték kiosztásával állandó értéket adhat a célként megadott attribútumnak. A forrás vagy a cél attribútum között közvetlen hozzárendelést használhat. Vagy használhat egy kifejezést is az attribútumhoz. Itt láthatja a különböző [kifejezési függvényeket](./reference-connect-sync-functions-reference.md) .

#### <a name="add-an-outbound-sync-rule"></a>Kimenő szinkronizálási szabály hozzáadása
Ahhoz, hogy az attribútumot a célként megadott könyvtárba lehessen kapcsolni, létre kell hoznia egy kimenő szabályt. Ez azt jelenti, hogy a forrás a metaverse, és a cél a csatlakoztatott rendszer. Kimenő szabály létrehozásához indítsa el a **szinkronizálási szabályok szerkesztőjét**, módosítsa az **irányt** a **kimenő** értékre, majd kattintson az **új szabály hozzáadása** lehetőségre. 

![Szinkronizációs szabályok szerkesztője](media/how-to-connect-fix-default-rules/default3c.png)

A bejövő szabályhoz hasonlóan a szabály elnevezéséhez használhatja a saját elnevezési konvencióját is. Válassza ki a **csatlakoztatott rendszerállapotot** az Azure ad-bérlőben, és válassza ki azt a csatlakoztatott rendszerobjektumot, amelyhez az attribútumérték értéket meg szeretné határozni. Állítsa be a sorrendet 0 és 99 között. 

![Kimenő szinkronizálási szabály létrehozása](media/how-to-connect-fix-default-rules/default3d.png)

A **hatókör-szűrő** és a **csatlakoztatási szabályok** üresek maradnak. Adja meg az átalakítást állandó, közvetlen vagy kifejezésként. 

Most már tudja, hogyan hozhat új attribútumot egy felhasználói objektumhoz Active Directoryról Azure Active Directoryre. Ezekkel a lépésekkel bármely objektumból leképezheti a forrás és a cél bármely attribútumát. További információ: [Egyéni szinkronizálási szabályok létrehozása](how-to-connect-create-custom-sync-rule.md) és [felkészülés a felhasználók](/office365/enterprise/prepare-for-directory-synchronization)kiépítésére.

### <a name="override-the-value-of-an-existing-attribute"></a>Meglévő attribútum értékének felülbírálása
Érdemes lehet felülbírálni egy már leképezett attribútum értékét. Ha például az Azure AD-ben mindig null értéket szeretne beállítani egy attribútumhoz, egyszerűen csak egy bejövő szabályt hozzon létre. Végezze el az állandó értéket, a `AuthoritativeNull` folyamatot a TARGET attribútumnak. 

>[!NOTE] 
> Ebben az esetben használja a `AuthoritativeNull` helyett `Null` . Ennek az az oka, hogy a nem null értékű érték a null értéket váltja fel, még akkor is, ha az alacsonyabb prioritással rendelkezik (a szabályban magasabb számérték szerepel). `AuthoritativeNull`a másik viszont nem helyettesít más szabályok által nem null értékű értéket. 

### <a name="dont-sync-existing-attribute"></a>Ne szinkronizálja a meglévő attribútumot
Ha ki szeretne zárni egy attribútumot a szinkronizálásból, használja a Azure AD Connectben megadott Attribute Filtering szolgáltatást. Indítsa el **Azure ad Connect** az asztal ikonján, majd válassza a **szinkronizálási beállítások testreszabása lehetőséget**.

![Azure AD Connect további feladatok beállításai](media/how-to-connect-fix-default-rules/default4.png)

 Győződjön meg arról, hogy az **Azure ad-alkalmazás és az attribútumok szűrése** be van jelölve, majd kattintson a **Tovább gombra**.

![Azure AD Connect választható funkciók](media/how-to-connect-fix-default-rules/default5.png)

Törölje azokat az attribútumokat, amelyeket ki szeretne zárni a szinkronizálásból.

![Azure AD Connect attribútumai](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>Hatókör-szűrő módosítása
Azure AD-szinkronizáló a legtöbb objektumról gondoskodik. Csökkentheti az objektumok hatókörét, és csökkentheti az exportálandó objektumok számát a normál alapértelmezett szinkronizálási szabályok módosítása nélkül. 

A szinkronizált objektumok hatókörének csökkentéséhez használja az alábbi módszerek egyikét:

- cloudFiltered attribútum
- Szervezeti egység szűrése

Ha csökkenti a szinkronizált felhasználók hatókörét, a jelszó-kivonatolás szinkronizálása is leáll a kiszűrt felhasználók számára. Ha az objektumok szinkronizálása már megtörtént, a hatókör csökkentése után a rendszer törli a kiszűrt objektumokat a cél könyvtárából. Ezért ügyeljen arra, hogy a hatókör nagyon körültekintően járjon el.

>[!IMPORTANT] 
> A Azure AD Connect által konfigurált objektumok hatókörének növelése nem ajánlott. Ezzel megnehezíti a Microsoft támogatási csapatának a testreszabások megismerését. Ha növelje az objektumok hatókörét, szerkessze a meglévő szabályt, klónozottan, és tiltsa le az eredeti szabályt. 

### <a name="cloudfiltered-attribute"></a>cloudFiltered attribútum
Ez az attribútum nem állítható be Active Directoryban. Állítsa be az attribútum értékét egy új bejövő szabály hozzáadásával. Ezután az **átalakítás** és a **kifejezés** használatával állíthatja be ezt az attribútumot a metaverse-ban. Az alábbi példa azt mutatja, hogy nem kívánja szinkronizálni az összes olyan felhasználót, akinek a részlegének neve **HRD** (kis-és nagybetűk megkülönböztetése nélkül) kezdődik:

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Az osztályt először a forrástól (Active Directory) a kisbetűsre alakítottuk át. Ezután a függvény használatával `Left` csak az első három karaktert vettük fel, és összehasonlítjuk `hrd` . Ha megfelel, az érték a következőre van beállítva: `True` , ellenkező esetben `NULL` . Ha az értéket NULL értékre állítja, akkor egy másik, alacsonyabb prioritású szabály (egy magasabb érték) más feltétellel írható. Futtassa az előzetes verziót egy objektumon a szinkronizálási szabály érvényesítéséhez, ahogy az a [szinkronizálási szabály ellenőrzése](#validate-sync-rule) szakaszban is szerepel.

![Bejövő szinkronizálási szabály beállításainak létrehozása](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>Szervezeti egység szűrése
Létrehozhat egy vagy több szervezeti egységet (OU), és áthelyezheti azokat az objektumokat, amelyeket nem szeretne szinkronizálni ezekkel a szervezeti egységekkel. Ezután konfigurálja a szervezeti egység szűrését Azure AD Connect. Indítsa el **Azure ad Connect** az asztal ikonján, és válassza ki a következő beállításokat. A szervezeti egység szűrését a Azure AD Connect telepítésének időpontjában is konfigurálhatja. 

![Azure AD Connect további feladatok](media/how-to-connect-fix-default-rules/default8.png)

Kövesse a varázslót, és törölje azokat a szervezeti egységeket, amelyeket nem szeretne szinkronizálni.

![Tartományok és szervezeti egységek szűrési beállításainak Azure AD Connect](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>Csatlakozási feltétel módosítása
Használja az Azure AD Connect által konfigurált alapértelmezett illesztési feltételeket. Az alapértelmezett csatlakoztatási feltételek módosítása megnehezíti a Microsoft támogatási szolgálatának a termék testreszabásának és támogatásának megértését.

## <a name="validate-sync-rule"></a>Szinkronizálási szabály érvényesítése
Az újonnan hozzáadott szinkronizálási szabályt a teljes szinkronizálási ciklus futtatása nélkül is érvényesítheti az előzetes verzió funkció használatával. A Azure AD Connect területen válassza a **szinkronizálási szolgáltatás** elemet.

![Azure AD Connect a szinkronizációs szolgáltatás kiemelten](media/how-to-connect-fix-default-rules/default10.png)

Válassza a **metaverse-keresés** lehetőséget. Válassza ki a hatókör objektumot **személyként**, válassza a **Hozzáadás záradékot**, és adja meg a keresési feltételeket. Ezután válassza a **Keresés** lehetőséget, majd kattintson duplán az objektumra a keresési eredmények között. A lépés futtatása előtt győződjön meg arról, hogy a Azure AD Connectban lévő adatok naprakészek az adott objektumra vonatkozóan. Ehhez futtassa az Importálás és a szinkronizálás az erdőben parancsot.

![Szinkronizálási szolgáltatáskezelő](media/how-to-connect-fix-default-rules/default11.png)

A **metaverse-objektum tulajdonságainál** válassza az **Összekötők** lehetőséget, válassza ki az objektumot a megfelelő összekötőben (erdőben), és válassza a **Tulajdonságok...** lehetőséget.

![Metaverse-objektum tulajdonságai](media/how-to-connect-fix-default-rules/default12.png)

Előnézet kiválasztása. **..**

![Összekötő terület objektumának tulajdonságai](media/how-to-connect-fix-default-rules/default13a.png)

Az előnézet ablakban válassza az **előnézet előállítása** és az **attribútum importálása** a bal oldali ablaktáblán lehetőséget.

![Képernyőfelvétel: az "Előnézet" ablak "az attribútum importálása" és az "előnézet előállítása" beállítás látható.](media/how-to-connect-fix-default-rules/default14.png)
 
Itt figyelje meg, hogy az újonnan hozzáadott szabály fut az objektumon, és az attribútum értéke `cloudFiltered` true (igaz).

![Előnézet](media/how-to-connect-fix-default-rules/default15a.png)
 
Ha a módosított szabályt az alapértelmezett szabállyal szeretné összehasonlítani, mindkét szabályt külön, szövegfájlként exportálja. Ezeket a szabályokat PowerShell-parancsfájlként exportálja a rendszer. Ezeket összehasonlíthatja bármilyen fájl-összehasonlító eszközzel (például Windiff) a módosítások megtekintéséhez. 
 
Figyelje meg, hogy a módosított szabályban az `msExchMailboxGuid` attribútum a **kifejezés** típusára változik a **közvetlen** helyett. Az érték a **Null** és a **ExecuteOnce** beállításra is módosul. Figyelmen kívül hagyhatja az azonosított és a prioritási különbségeket. 

![Windiff eszköz kimenete](media/how-to-connect-fix-default-rules/default17.png)
 
Ha a szabályokat úgy szeretné kijavítani, hogy azok az alapértelmezett beállításokra váltsanak vissza, törölje a módosított szabályt, és engedélyezze az alapértelmezett szabályt. Ügyeljen arra, hogy ne veszítse el az elérni kívánt testreszabást. Ha elkészült, futtassa a **teljes szinkronizálást**.

## <a name="next-steps"></a>Következő lépések
- [Hardver és előfeltételek](how-to-connect-install-prerequisites.md) 
- [Gyorsbeállítások](how-to-connect-install-express.md)
- [Testreszabott beállítások](how-to-connect-install-custom.md)