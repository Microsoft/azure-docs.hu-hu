---
title: Oktatóanyag az N8-identitás konfigurálásához Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Oktatóanyag a TheAccessHub felügyeleti eszköznek a Azure Active Directory B2C segítségével történő konfigurálásához az ügyfelek fiókjainak áttelepítéséhez és az ügyfél-szolgáltatási kérelmek (CSR) felügyeletéhez.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/26/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 337275cef0f2159cb5fac40ac0435408baf3bbef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96170922"
---
# <a name="tutorial-for-configuring-theaccesshub-admin-tool-with-azure-active-directory-b2c"></a>Oktatóanyag a TheAccessHub felügyeleti eszköz konfigurálásához Azure Active Directory B2C

Ebben a példában útmutatást adunk a Azure Active Directory (AD) B2C és a [TheAccessHub felügyeleti eszköz](https://n8id.com/products/theaccesshub-admintool/) integrálásához az N8 Identity-től. Ez a megoldás az ügyfelek fiókjainak áttelepítését és az ügyfélszolgálati kérelmeket (CSR) kezeli.  

Ez a megoldás az Ön számára megfelelő, ha a következő igények közül egyet vagy többet tartalmaz:

- Rendelkezik egy meglévő hellyel, és át szeretne térni Azure AD B2Cra. Azonban az ügyfelek fiókjainak, például a jelszavaknak az áttelepítésével küzd

- Szükség van egy eszközre a CSR számára Azure AD B2C-fiókok felügyeletéhez.

- A delegált felügyelet használatára van szükség az ügyfélszolgálati munkatársak számára.

- Szinkronizálni és egyesíteni szeretné az adatokat számos tárházból a Azure AD B2Cba.

## <a name="pre-requisites"></a>Előfeltételek

A kezdéshez a következőkre lesz szüksége:

- Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.

- [Azure ad B2C bérlő](./tutorial-create-tenant.md). A bérlőt az Azure-előfizetéshez kell kapcsolni.

- Egy TheAccessHub felügyeleti eszköz környezete: lépjen kapcsolatba az [N8 Identity](https://n8id.com/contact/) szolgáltatással egy új környezet kiépítéséhez.

- Választható Kapcsolati és hitelesítő adatok minden olyan adatbázishoz vagy Lightweight Directory Access Protocol (LDAPs) szolgáltatáshoz, amelyre adatokat kíván áttelepíteni.

- Választható Az [Egyéni házirendek](./custom-policy-get-started.md)használatára konfigurált Azure ad B2C környezet, ha integrálni szeretné a TheAccessHub-felügyeleti eszközt a regisztrációs házirend folyamatba.

## <a name="scenario-description"></a>Forgatókönyv leírása

A TheAccessHub felügyeleti eszköz az Azure bármely más alkalmazásához hasonlóan fut. Az eszköz az N8 Identity Azure-előfizetésében vagy az ügyfél előfizetésében is futtatható. A következő architektúra-diagram a megvalósítást mutatja be.

![N8identity architektúra diagramot ábrázoló kép](./media/partner-n8identity/n8identity-architecture-diagram.png)

|Lépés | Description |
|:-----| :-----------|
| 1. | A felhasználó egy bejelentkezési oldalon érkezik. A felhasználók a regisztráció gombra kattintva létrehozhatnak egy új fiókot, és beírhatják az adatokat az oldalra. A Azure AD B2C a felhasználói attribútumokat gyűjti.
| 2. | Azure AD B2C meghívja a TheAccessHub felügyeleti eszközét, és átadja a felhasználói attribútumokat
| 3. | A TheAccessHub felügyeleti eszköze ellenőrzi a meglévő adatbázisát az aktuális felhasználói adatokhoz.  
| 4. | A rendszer szinkronizálja a felhasználói rekordot az adatbázisból a TheAccessHub felügyeleti eszközre.
| 5. | A TheAccessHub felügyeleti eszköz megosztja az adatközpontot a delegált CSR/segélyszolgálat rendszergazdájával.
| 6. | A TheAccessHub felügyeleti eszköz szinkronizálja a felhasználói rekordokat a Azure AD B2Csal.
| 7. |A TheAccessHub felügyeleti eszköz sikeres/sikertelen válasza alapján Azure AD B2C egy testreszabott üdvözlő e-mailt küld a felhasználónak.

## <a name="create-a-global-admin-in-your-azure-ad-b2c-tenant"></a>Globális rendszergazda létrehozása a Azure AD B2C-bérlőben

A TheAccessHub-felügyeleti eszköznek engedélyekkel kell rendelkeznie a globális rendszergazda nevében a felhasználói adatok olvasásához és a Azure AD B2C-bérlőn végzett módosítások végrehajtásához. A normál rendszergazdák által megnyert módosítások; a t hatására a TheAccessHub felügyeleti eszköz képes kommunikálni a Bérlővel.

Globális rendszergazda létrehozásához kövesse az alábbi lépéseket:

1. A Azure Portal jelentkezzen be a Azure AD B2C-bérlőbe rendszergazdaként. Navigáljon **Azure Active Directory**  >  **felhasználókhoz**
2. **Új felhasználó** kiválasztása
3. Válassza a **felhasználó létrehozása** lehetőséget egy normál címtárbeli felhasználó létrehozásához, és nem az ügyfél
4. Az azonosító adatok űrlapjának befejezése

   a. Adja meg a felhasználónevet (például "theaccesshub")

   b. Adja meg a nevet, például "TheAccessHub szolgáltatásfiók"

5. Válassza a **jelszó megjelenítése** lehetőséget, és másolja a kezdeti jelszót későbbi használatra
6. Globális rendszergazdai szerepkör kiosztása

   a. Válassza ki a felhasználó aktuális szerepkör- **felhasználóját** a módosításhoz

   b. A globális rendszergazda rekordjának bejelölése

   c. **Válassza ki**  >  **Létrehozás**

## <a name="connect-theaccesshub-admin-tool-with-your-azure-ad-b2c-tenant"></a>A TheAccessHub-felügyeleti eszköz összekötése a Azure AD B2C-Bérlővel

A TheAccessHub felügyeleti eszköze a Microsoft Graph API használja a címtár olvasására és módosítására. A bérlő globális rendszergazdájaként működik. További engedélyre van szükség a TheAccessHub felügyeleti eszköz számára, amelyet az eszközön belül adhat meg.

A TheAccessHub-felügyeleti eszköz a címtárhoz való hozzáférésének engedélyezéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a TheAccessHub felügyeleti eszközbe az N8 Identity által biztosított hitelesítő adatok használatával

2. Navigáljon a **rendszergazda**  >  **Azure ad B2C config**

3. Válassza a **kapcsolatok engedélyezése** lehetőséget.

4. Az új ablakban jelentkezzen be a globális rendszergazdai fiókjával. Előfordulhat, hogy a rendszer arra kéri, hogy állítsa alaphelyzetbe a jelszót, ha első alkalommal jelentkezik be az új szolgáltatásfiók használatával.

5. Kövesse az utasításokat, és válassza az **elfogadás** lehetőséget a TheAccessHub felügyeleti eszköz a kért engedélyek megadásához.

## <a name="configure-a-new-csr-user-using-your-enterprise-identity"></a>Új CSR-felhasználó konfigurálása a vállalati identitás használatával

Hozzon létre egy CSR/ügyfélszolgálati felhasználót, aki hozzáfér a TheAccessHub felügyeleti eszközhöz meglévő vállalati Azure Active Directory hitelesítő adataival.

Ha az egyszeri bejelentkezéssel (SSO) szeretné konfigurálni a CSR/segélyszolgálat szolgáltatást, a következő lépéseket javasoljuk:

1. Jelentkezzen be a TheAccessHub felügyeleti eszközbe az N8 Identity által biztosított hitelesítő adatok használatával.

2. Navigáljon a **kezelő eszközök**  >  **munkatársainak kezeléséhez**

3. Válassza a **munkatárs hozzáadása** lehetőséget

4. Válassza ki a **kolléga típusát Azure Administrator**

5. Adja meg a munkatárs profiljának adatait

   a. A hazai szervezet választása azt szabályozza, hogy ki jogosult a felhasználó felügyeletére.

   b. A bejelentkezési AZONOSÍTÓhoz/Azure AD-felhasználónévhez adja meg a felhasználó egyszerű felhasználónevét a felhasználó Azure Active Directory fiókjából.

   c. A TheAccessHub-szerepkörök lapon keresse meg a felügyelt szerepkörű ügyfélszolgálatot. Lehetővé teszi, hogy a felhasználó hozzáférjen a munkatársak kezelése nézethez. A felhasználónak továbbra is egy csoportba kell helyeznie, vagy a szervezet tulajdonosának kell elvégeznie az ügyfeleken való teendőket.

6. Válassza a **Küldés** lehetőséget.

## <a name="configure-a-new-csr-user-using-a-new-identity"></a>Új CSR-felhasználó konfigurálása új identitás használatával

Hozzon létre egy CSR/ügyfélszolgálati felhasználót, aki a TheAccessHub felügyeleti eszközhöz egy új helyi hitelesítő adatokkal fog hozzáférni, amely a TheAccessHub felügyeleti eszközhöz egyedi Ezt elsősorban olyan szervezetek fogják használni, amelyek nem használnak Azure AD-t a vállalatuk számára.

Ha [a CSR/helpdesk](https://youtu.be/iOpOI2OpnLI) felhasználót SSO nélkül szeretné beállítani, kövesse az alábbi lépéseket:

1. Bejelentkezés a TheAccessHub felügyeleti eszközbe az N8 Identity által biztosított hitelesítő adatok használatával

2. Navigáljon a **kezelő eszközök**  >  **munkatársainak kezeléséhez**

3. Válassza a **munkatárs hozzáadása** lehetőséget

4. Válassza ki a **kolléga típusát helyi rendszergazda**

5. Adja meg a munkatárs profiljának adatait

   a. A hazai szervezet választása azt szabályozza, hogy ki jogosult a felhasználó felügyeletére.

   b. A **TheAccessHub-szerepkörök** lapon válassza ki a felügyelt szerepkörű **ügyfélszolgálatot**. Lehetővé teszi, hogy a felhasználó hozzáférjen a munkatársak kezelése nézethez. A felhasználónak továbbra is egy csoportba kell helyeznie, vagy a szervezet tulajdonosának kell elvégeznie az ügyfeleken való teendőket.

6. Másolja a **bejelentkezési azonosítót/e-mailt** és **egy jelszó** -attribútumot. Adja meg az új felhasználónak. Ezeket a hitelesítő adatokat használják a TheAccessHub felügyeleti eszközre való bejelentkezéshez. A rendszer kérni fogja a felhasználótól, hogy az első bejelentkezéskor új jelszót adjon meg.

7. **Küldés** kiválasztása

## <a name="configure-partitioned-csr-administration"></a>Particionált CSR-felügyelet konfigurálása

A TheAccessHub felügyeleti eszközben az ügyfél és a CSR/helpdesk felhasználók felügyeletére vonatkozó engedélyek a szervezeti hierarchia használatával kezelhetők. Az összes munkatárs és ügyfél rendelkezik egy otthoni szervezettel, ahol a lakóhelyük van. Az adott munkatársak vagy munkacsoportok a szervezetek tulajdonosaként rendelhetők el.  A szervezet tulajdonosai kezelhetik (módosíthatják) a munkatársakat és az ügyfeleket a saját szervezeteknél vagy alszervezetekben. Ha több munkatárs számára is lehetővé szeretné tenni a felhasználók felügyeletét, a csoportok számos taggal hozhatók létre. A csoport ezután hozzárendelhető szervezeti tulajdonosként, és az összes csoport tagjai kezelhetik a munkatársaikat és az ügyfeleket a szervezeten belül.

### <a name="create-a-new-group"></a>Új csoport létrehozása

1. Jelentkezzen be a TheAccessHub felügyeleti eszközbe az N8 Identity által biztosított **hitelesítő adatok** használatával

2. Navigáljon a **szervezet > csoportok kezelése** elemre

3. Válassza > **Csoport hozzáadása** lehetőséget

4. Adja meg a **csoport nevét**, a **csoport leírását** és a **csoport tulajdonosát**

5. Keresse meg és jelölje ki azokat a munkatársakat, akik számára a csoport tagjai lesznek, majd válassza a >**Hozzáadás** lehetőséget.

6. A lap alján láthatja a csoport összes tagját.

7. Ha a szükséges tagokat el lehet távolítani, válassza a sor végén található **x betűt**

8. **Küldés** kiválasztása

### <a name="create-a-new-organization"></a>Új szervezet létrehozása

1. Jelentkezzen be a TheAccessHub felügyeleti eszközbe az N8 Identity által biztosított hitelesítő adatok használatával

2. Navigáljon a szervezet > szervezet **kezelése** elemre

3. Válassza > **szervezet hozzáadása** lehetőséget

4. Adja meg a **szervezet nevét**, a **szervezet tulajdonosát** és a **szülő szervezetet**.

    a. A szervezet neve ideális esetben olyan érték, amely megfelel az ügyfél adatainak. Ha betölti a munkatársat és az ügyféladatokat, és a terhelésben megadja a szervezet nevét, a munkatárs automatikusan elhelyezhető a szervezeten belül.

    b. A tulajdonos azt a személyt vagy csoportot jelöli, aki a szervezeten belüli ügyfeleket és munkatársakat felügyeli, valamint az összes alszervezetet.

    c. A fölérendelt szervezet azt jelzi, hogy a szervezet számára melyik más szervezet is felelős.

5. Válassza a **Küldés** lehetőséget.

### <a name="modify-the-hierarchy-via-the-tree-view"></a>A hierarchia módosítása a fanézeten keresztül

1. Jelentkezzen be a TheAccessHub felügyeleti eszközbe az N8 Identity által biztosított hitelesítő adatok használatával

2. Navigáljon a **felettes eszközök**  >  **fanézetéhez**

3. Ebben a képviseletben megjelenítheti, hogy mely munkatársak és csoportok kezelhetik a szervezeteket.

4. A hierarchiák úgy módosíthatók, hogy olyan szervezeteket kimagaslik, amelyeknek a alapján a szülőt el szeretné állítani.

5. Ha befejezte a hierarchia módosítását, válassza a **Mentés** lehetőséget.

## <a name="customize-welcome-notification"></a>Üdvözlő értesítés testreszabása

Noha a TheAccessHub használatával áttelepítheti a felhasználókat egy korábbi hitelesítési megoldásból a Azure AD B2Cba, érdemes lehet testreszabni a felhasználói üdvözlő értesítést, amelyet a rendszer az áttelepítés során TheAccessHub küld a felhasználónak. Ez az üzenet általában tartalmazza azt a hivatkozást, amellyel az ügyfél új jelszót állíthat be a Azure AD B2C könyvtárban.

Az értesítés testreszabása:

1. Jelentkezzen be a TheAccessHub az N8 Identity által megadott hitelesítő adatok használatával

2. Navigáljon a **rendszergazdák**  >  **értesítéseire**

3. Válassza ki a **kolléga létrehozása sablont**

4. **Szerkesztés** kiválasztása

5. Szükség szerint módosítsa az üzenet és a sablon mezőket. A sablon mező HTML-kompatibilis, és HTML formátumú értesítéseket küldhet az ügyfél e-mail-címére.

6. Ha elkészült, válassza a **Mentés** lehetőséget.

## <a name="migrate-data-from-external-data-sources-to-azure-ad-b2c"></a>Adatok migrálása külső adatforrásokból Azure AD B2Cba

A TheAccessHub felügyeleti eszköz használatával különböző adatbázisokból, LDAP-fájlokból és CSV-fájlokból importálhatja az adatait, majd leküldheti azokat a Azure AD B2C bérlőnek. A TheAccessHub felügyeleti eszközön belül betöltődik az adatAzure AD B2C felhasználói kolléga típusba.  Ha az adatforrásnak nincs saját maga az Azure-ban, a rendszer a TheAccessHub felügyeleti eszközbe és Azure AD B2Cba helyezi. Ha a külső adatforrások forrása nem egyszerű. csv-fájl a gépen, állítson be egy adatforrást az adatterhelés megkezdése előtt. Az alábbi lépések leírják az adatforrás létrehozását és az adatterhelést.

### <a name="configure-a-new-data-source"></a>Új adatforrás konfigurálása

1. Jelentkezzen be a TheAccessHub felügyeleti eszközbe az N8 Identity által biztosított hitelesítő adatok használatával

2. Navigáljon a **rendszergazda**  >  **adatforrásaihoz**

3. **Adatforrás hozzáadása** lehetőség kiválasztása

4. Adja meg az adatforrás **nevét** és **típusát**

5. Adja meg az űrlap adatait az adatforrás típusától függően:

   **Adatbázisok esetén**

   a. **Típus** – adatbázis

   b. **Adatbázis típusa** – válasszon egy adatbázist az egyik támogatott adatbázis-típusból.

   c. **Kapcsolatok URL-címe** – adjon meg egy jól formázott JDBC-kapcsolatok karakterláncot. Például: ``jdbc:postgresql://myhost.com:5432/databasename``

   d. **Username (Felhasználónév** ) – Itt adhatja meg az adatbázis eléréséhez használt felhasználónevet

   e. **Password (jelszó** ) – Itt adhatja meg az adatbázis eléréséhez szükséges jelszót

   f. **Lekérdezés** – adja meg az SQL-lekérdezést az ügyfél adatainak kinyeréséhez. Például: ``SELECT * FROM mytable;``

   : Válassza a **kapcsolat tesztelése** lehetőséget, majd megjelenik egy minta az adatairól a kapcsolat működésének biztosításához.

   **LDAPs esetén**

   a. **Típus** – LDAP

   b. **Gazdagép** – adja meg annak a számítógépnek az állomásnevét vagy IP-címét, amelyben az LDAP-kiszolgáló fut. Például: ``mysite.com``

   c. **Port** – Itt adhatja meg azt a portszámot, amelyben az LDAP-kiszolgáló figyel.

   d. **SSL** – jelölje be a jelölőnégyzetet, ha a TheAccessHub felügyeleti eszköznek SSL használatával kell kommunikálnia az LDAP-lel. Az SSL használata kifejezetten ajánlott.

   e. **Bejelentkezés DN** – adja meg a bejelentkezéshez használandó felhasználói fiók megkülönböztető nevét, és végezze el az LDAP-keresést

   f. **Password (jelszó** ) – adja meg a felhasználó jelszavát

   : **Alap DN** – Itt adhatja meg annak a hierarchiának a felső részén található DN-t, amelyben el szeretné végezni a keresést

   h. **Filter (szűrő** ) – Itt adhatja meg az LDAP-szűrő sztringjét, amely beolvassa az ügyfél rekordjait

   i. **Attribútumok** – adja meg az ügyfelek rekordjainak vesszővel tagolt listáját, amelyeket át kell adni a TheAccessHub felügyeleti eszköznek

   j. A kapcsolat **teszteléséhez** válassza ki az adatait, és ellenőrizze, hogy működik-e a kapcsolat.

   **OneDrive**

   a. **Típus** – OneDrive for Business

   b. Válassza a **kapcsolatok engedélyezése** lehetőséget.

   c. Egy új ablak kérni fogja, hogy jelentkezzen be a **OneDrive**-be, jelentkezzen be egy olyan felhasználóval, aki olvasási hozzáféréssel rendelkezik a OneDrive-fiókjához. A TheAccessHub felügyeleti eszköz segítségével a felhasználó CSV-fájlok betöltését olvashatja el.

   d. Kövesse az utasításokat, és válassza az **elfogadás** lehetőséget a TheAccessHub felügyeleti eszköz a kért engedélyek megadásához.

6. Ha elkészült, válassza a **Mentés** lehetőséget.  

### <a name="synchronize-data-from-your-data-source-into-azure-ad-b2c"></a>Adatok szinkronizálása az adatforrásból a Azure AD B2Cba

1. Jelentkezzen be a TheAccessHub felügyeleti eszközbe az N8 Identity által biztosított hitelesítő adatok használatával

2. Navigáljon a **rendszeradminisztrátor**  >  **adatszinkronizálása elemre**

3. **Új betöltés** kiválasztása

4. Válassza ki a **kolléga típusát** Azure ad B2C felhasználó

5. Válassza ki a **forrás** lehetőséget, majd az előugró ablakban válassza ki az adatforrást. Ha létrehozott egy OneDrive-adatforrást, válassza ki a fájlt is.

6. Ha nem szeretne új ügyfél-fiókot létrehozni ezzel a terheléssel, akkor módosítsa az első szabályzatot: **Ha a munkatársa nem található a TheAccessHub-ben, akkor** **semmit sem kell tennie**

7. Ha nem szeretné frissíteni a meglévő ügyfelek fiókját ezzel a terheléssel, akkor módosítsa a második szabályzatot, **Ha a forrás-és a TheAccessHub nem felel** meg a **műveletnek** .

8. Kattintson a **Tovább** gombra.

9. A **keresési leképezés konfigurációjában** bemutatjuk, hogyan korrelálhatja a betöltési rekordokat a TheAccessHub felügyeleti eszközbe már betöltött ügyfelekkel. Válasszon ki egy vagy több azonosító attribútumot a forrásban. Egyeznek meg a TheAccessHub felügyeleti eszközben található, azonos értékeket tartalmazó attribútumokkal. Ha talál egyezést, akkor a meglévő rekord felül lesz bírálva. Ellenkező esetben új ügyfél lesz létrehozva. Az ellenőrzések száma több sorba is felhasználható. Például megtekintheti az e-maileket, majd az utónév és a vezetéknév nevet.

10. A bal oldali menüben válassza az **adatleképezés** lehetőséget.

11. A Data-Mapping konfigurációban rendelje hozzá, hogy mely TheAccessHub-felügyeleti eszköz attribútumai legyenek kitöltve a forrás attribútumaiból. Nem kell az összes attribútumot leképeznie. A nem leképezett attribútumok változatlanok maradnak a meglévő ügyfelek esetében.

12. Ha az attribútumhoz org_name egy meglévő szervezet nevét, akkor a rendszer az új ügyfeleket hozza létre a szervezetbe.

13. Kattintson a **Tovább** gombra.

14. Ha a terhelést újra fel kell állítani, a napi/heti vagy havi ütemterv is megadható. Ellenkező esetben hagyja meg **az alapértelmezett beállítást**.

15. **Küldés** kiválasztása

16. Ha a **most már** beállította az ütemtervet, a rendszer azonnal új rekordot ad hozzá az adatszinkronizálási képernyőhöz. Miután az ellenőrzési fázis elérte a 100%-ot, válassza az **új rekordot** a terhelés várt eredményének megtekintéséhez. Az ütemezett terhelések esetében ezek a rekordok csak az ütemezett időpont után jelennek meg.

17. Ha nincsenek hibák, válassza a **Futtatás** lehetőséget a módosítások elvégzéséhez. Ellenkező esetben válassza az **Eltávolítás** lehetőséget a **további** menüben a terhelés eltávolításához. Ezután javítsa ki a forrásadatokat, vagy töltse ki a hozzárendeléseket, és próbálkozzon újra. Ehelyett, ha a hibák száma kicsi, manuálisan frissítheti a rekordokat, és kiválaszthatja a **frissítés** lehetőséget az egyes rekordokon a javítások elvégzéséhez. Végezetül folytathatja a hibákat, és később is megoldhatók a TheAccessHub felügyeleti eszköz **támogatási beavatkozásai** .

18. Ha az **adatszinkronizálási** rekord 100%-ra változik a betöltési fázisban, a rendszer a betöltéstől kapott összes változást kezdeményezte. Az ügyfeleknek meg kell kezdeniük a Azure AD B2C változásait.

## <a name="synchronize-azure-ad-b2c-customer-data"></a>Azure AD B2C ügyféladatok szinkronizálása 

A TheAccessHub felügyeleti eszköz egyszeri vagy folyamatban lévő műveletként szinkronizálhatja a Azure AD B2C összes ügyfelének adatait a TheAccessHub felügyeleti eszközre. Ez biztosítja, hogy a CSR/segélyszolgálat rendszergazdái naprakész ügyfél-információkat kapjanak.

Azure AD B2C adatainak szinkronizálása a TheAccessHub felügyeleti eszközön:

1. Jelentkezzen be a TheAccessHub felügyeleti eszközbe az N8 Identity által biztosított hitelesítő adatok használatával

2. Navigáljon a **rendszeradminisztrátor**  >  **adatszinkronizálása elemre**

3. **Új betöltés** kiválasztása

4. Válassza ki a **kolléga típusát** Azure ad B2C felhasználó

5. A **Beállítások** lépésnél hagyja meg az alapértelmezett értékeket.

6. Kattintson a **Tovább** gombra.

7. Az **adatleképezés & a keresés** lépésnél hagyja meg az alapértelmezett értékeket. Ha a **org_name** attribútumot egy meglévő szervezet nevével rendelkező értékre képezi le, akkor a rendszer az új ügyfeleket a szervezetbe helyezi.

8. Kattintson a **Tovább** gombra.

9. Ha a terhelést újra fel kell állítani, a napi/heti vagy havi ütemterv is megadható. Ellenkező esetben tartsa meg az alapértelmezettet: **most**. Javasoljuk, hogy rendszeres időközönként szinkronizáljon Azure AD B2C.

10. **Küldés** kiválasztása

11. Ha a **most már** beállította az ütemtervet, a rendszer azonnal új rekordot ad hozzá az adatszinkronizálási képernyőhöz. Miután az ellenőrzési fázis elérte a 100%-ot, válassza az új rekordot a terhelés várt eredményének megtekintéséhez. Az ütemezett terhelések esetében ezek a rekordok csak az ütemezett időpont után jelennek meg.

12. Ha nincsenek hibák, válassza a **Futtatás** lehetőséget a módosítások elvégzéséhez. Ellenkező esetben válassza az **Eltávolítás** lehetőséget a további menüben a terhelés eltávolításához. Ezután javítsa ki a forrásadatokat, vagy töltse ki a hozzárendeléseket, és próbálkozzon újra. Ehelyett, ha a hibák száma kicsi, manuálisan frissítheti a rekordokat, és kiválaszthatja a **frissítés** lehetőséget az egyes rekordokon a javítások elvégzéséhez. Végezetül folytathatja a hibákat, és később is megoldhatók a TheAccessHub felügyeleti eszköz támogatási beavatkozásai.

13. Ha az **adatszinkronizálási** rekord 100%-ra változik a betöltési fázisban, a rendszer a betöltéstől kapott összes változást kezdeményezte.

## <a name="configure-azure-ad-b2c-policies"></a>Azure AD B2C házirendek konfigurálása

A TheAccessHub felügyeleti eszköz időnkénti szinkronizálása korlátozva van a Azure AD B2Cával való naprakész állapotának megőrzésére. A TheAccessHub felügyeleti eszköz API-ját és Azure AD B2C szabályzatait kihasználva tájékoztatjuk a TheAccessHub felügyeleti eszköz változásait. Ehhez a megoldáshoz [Azure ad B2C egyéni szabályzatok](./custom-policy-get-started.md)technikai ismerete szükséges. A következő szakaszban bemutatunk egy példát a szabályzat lépéseire és egy biztonságos tanúsítványra, amely értesíti az új fiókok TheAccessHub felügyeleti eszközét az Sign-Up egyéni szabályzatokban.

### <a name="create-a-secure-credential-to-invoke-theaccesshub-admin-tools-api"></a>Biztonságos hitelesítő adat létrehozása a TheAccessHub felügyeleti eszköz API-ját meghívásához

1. Jelentkezzen be a TheAccessHub felügyeleti eszközbe az N8 Identity által biztosított hitelesítő adatok használatával

2. Navigáljon a **rendszeradminisztrátor**  >  **felügyeleti eszközök**  >  **API-biztonság** elemre

3. A **készítés** kiválasztása

4. A **tanúsítvány jelszavának** másolása

5. Az ügyféltanúsítvány beszerzéséhez kattintson a **Letöltés** gombra.

6. Ezt az [oktatóanyagot](./secure-rest-api.md#https-client-certificate-authentication ) követve vegye fel az ügyféltanúsítványt Azure ad B2Cba.

### <a name="retrieve-your-custom-policy-examples"></a>Egyéni házirend-példák beolvasása

1. Jelentkezzen be a TheAccessHub felügyeleti eszközbe az N8 Identity által biztosított hitelesítő adatok használatával

2. Navigáljon a **rendszergazdák**  >  **felügyeleti eszközök**  >  **Azure B2C-szabályzatok**

3. Adja meg Azure AD B2C bérlői tartományát és a két identitási élmény keretrendszer-azonosítóját az identitási élmény keretrendszere konfigurációjában

4. Válassza a **Mentés** lehetőséget

5. Válassza a **Letöltés** lehetőséget, ha olyan alapszintű szabályzatokat tartalmazó zip-fájlt szeretne beolvasni, amely ügyfeleket regisztrál a TheAccessHub felügyeleti eszközbe.

6. Ezt az [oktatóanyagot](./custom-policy-get-started.md) követve megkezdheti az egyéni szabályzatok megtervezését Azure ad B2Cban.

## <a name="next-steps"></a>Következő lépések

További információkért tekintse át a következő cikkeket:

- [Egyéni szabályzatok az Azure AD B2C-ben](./custom-policy-overview.md)

- [Ismerkedés az egyéni szabályzatokkal Azure AD B2C](./custom-policy-get-started.md?tabs=applications)