---
title: Azure Active Directory-Azure Database for PostgreSQL – egyetlen kiszolgáló használata
description: Ismerje meg, hogyan állíthat be Azure Active Directory (HRE) a Azure Database for PostgreSQL – egyetlen kiszolgálóval történő hitelesítéshez
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 07/23/2020
ms.openlocfilehash: 729879bb472786165b21a47a7baf058294a4db1f
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961523"
---
# <a name="use-azure-active-directory-for-authentication-with-postgresql"></a>Azure Active Directory használata a PostgreSQL-sel való hitelesítéshez

Ebből a cikkből megtudhatja, hogyan konfigurálhatja Azure Active Directory hozzáférését Azure Database for PostgreSQL és hogyan csatlakozhat Azure AD-jogkivonat használatával.

## <a name="setting-the-azure-ad-admin-user"></a>Az Azure AD-rendszergazda felhasználó beállítása

Csak az Azure AD-rendszergazda felhasználók hozhatnak létre/engedélyezhet felhasználókat az Azure AD-alapú hitelesítéshez. Azt javasoljuk, hogy ne használja az Azure AD-rendszergazdát a normál adatbázis-műveletekhez, mivel ez emelt szintű felhasználói engedélyekkel rendelkezik (például CREATEDB).

Az Azure AD-rendszergazda beállításához (egy felhasználót vagy egy csoportot is használhat) kövesse az alábbi lépéseket

1. A Azure Portal válassza ki a Azure Database for PostgreSQL azon példányát, amelyet engedélyezni kíván az Azure AD-ben.
2. A beállítások területen válassza a Active Directory rendszergazda elemet:

![Az Azure ad-rendszergazda beállítása][2]

3. Válasszon ki egy érvényes Azure AD-felhasználót az ügyfél-bérlőben az Azure AD-rendszergazdaként.

> [!IMPORTANT]
> A rendszergazda beállításakor a rendszer új felhasználót ad hozzá a Azure Database for PostgreSQL-kiszolgálóhoz teljes körű rendszergazdai engedélyekkel. Az Azure AD rendszergazdai felhasználója Azure Database for PostgreSQL a szerepkört fogja tartalmazni `azure_ad_admin` .

PostgreSQL-Kiszolgálónként csak egy Azure AD-rendszergazda hozható létre, és a másik lehetőség kiválasztása esetén a rendszer felülírja a kiszolgálóhoz konfigurált meglévő Azure AD-rendszergazdát. Egy Azure AD-csoportot az egyes felhasználók helyett több rendszergazdával is megadhat. Vegye figyelembe, hogy ezt követően a csoport nevét kell bejelentkeznie adminisztrációs célokra.

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>Csatlakozás az Azure Database for PostgreSQLhoz az Azure AD használatával

A következő magas szintű diagram összefoglalja az Azure AD-hitelesítés Azure Database for PostgreSQL használatával történő használatának munkafolyamatát:

![hitelesítési folyamat][1]

Az Azure AD-integrációt úgy terveztük, hogy közös PostgreSQL-eszközökkel (például psql) működjön együtt, amelyek nem ismerik az Azure AD-t, és csak a Felhasználónév és a jelszó megadását támogatják a PostgreSQL-hez való csatlakozáskor. Az Azure AD-tokent jelszóként adjuk át a fenti képen látható módon.

Jelenleg a következő ügyfeleket teszteltük:

- psql commandline (használja a PGPASSWORD változót a jogkivonat átadásához, lásd alább)
- Azure Data Studio (a PostgreSQL bővítmény használatával)
- Más libpq-alapú ügyfelek (például közös alkalmazás-keretrendszerek és ORMs)

> [!NOTE]
> Vegye figyelembe, hogy az Azure AD-jogkivonat a pgAdmin-mel való használata jelenleg nem támogatott, mivel a jelszavakra vonatkozó 256 karakteres korlátozást tartalmaz (a jogkivonat mérete meghaladja a tokent).

Ezek a lépések, amelyekkel egy felhasználónak/alkalmazásnak a következőkben ismertetett Azure AD-hitelesítéssel kell rendelkeznie:

### <a name="prerequisites"></a>Előfeltételek

A Azure Cloud Shell, egy Azure-beli virtuális gép vagy a helyi gépen is követheti. Győződjön meg arról, hogy az [Azure CLI telepítve](/cli/azure/install-azure-cli)van.

### <a name="step-1-authenticate-with-azure-ad"></a>1. lépés: hitelesítés az Azure AD-vel

Először jelentkezzen be az Azure AD-vel az Azure CLI eszköz használatával. Ez a lépés nem szükséges a Azure Cloud Shellban.

```
az login
```

A parancs egy böngészőablakot indít az Azure AD-hitelesítés lapra. Ehhez meg kell adnia az Azure AD-beli felhasználói azonosítót és a jelszót.

### <a name="step-2-retrieve-azure-ad-access-token"></a>2. lépés: az Azure AD hozzáférési jogkivonatának beolvasása

Hívja meg az Azure CLI eszközt az 1. lépésben az Azure AD hitelesített felhasználó hozzáférési jogkivonatának beszerzéséhez az Azure Database for PostgreSQL eléréséhez.

Példa (nyilvános felhő esetén):

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

A fenti erőforrás-értéket pontosan az ábrán látható módon kell megadni. Más felhők esetében az erőforrás értéke a következő használatával kereshető fel:

```azurecli-interactive
az cloud show
```

Az Azure CLI 2.0.71-es és újabb verziói esetén a parancs a következő kényelmesebb verzióban adható meg az összes felhőhöz:

```azurecli-interactive
az account get-access-token --resource-type oss-rdbms
```

A sikeres hitelesítés után az Azure AD egy hozzáférési jogkivonatot fog visszaadni:

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

A jogkivonat egy alapszintű 64 karakterlánc, amely kódolja a hitelesített felhasználóra vonatkozó összes információt, és amely a Azure Database for PostgreSQL szolgáltatásra irányul.

> [!NOTE]
> A hozzáférési jogkivonat érvényessége 5 perc és 60 perc között lehet. Javasoljuk, hogy közvetlenül a bejelentkezés megkezdése előtt szerezze be a hozzáférési jogkivonatot a Azure Database for PostgreSQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>3. lépés: a token használata jelszóként a PostgreSQL-ben való bejelentkezéshez

Kapcsolódáskor a hozzáférési tokent a PostgreSQL felhasználói jelszavának kell használnia.

Ha a `psql` parancssori ügyfelet használja, a hozzáférési tokent át kell adni a `PGPASSWORD` környezeti változón keresztül, mivel a hozzáférési jogkivonat túllépi a jelszó megadásához szükséges hosszt `psql` :

Windows-példa:

```cmd
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

```PowerShell
$env:PGPASSWORD='<copy/pasted TOKEN value from step 2>'
```

Linux/macOS példa:

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Mostantól a következőhöz hasonló módon kezdeményezheti a kapcsolatokat Azure Database for PostgreSQLhoz:

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres sslmode=require"
```

Fontos szempontok a csatlakozáskor:

* `user@tenant.onmicrosoft.com` annak az Azure AD-felhasználónak vagy-csoportnak a neve, amelyhez csatlakozni próbál
* Mindig fűzze hozzá a kiszolgálónevet az Azure AD-felhasználó/csoport neve után (például `@mydb` )
* Ügyeljen arra, hogy pontosan az Azure AD-felhasználó vagy-csoport nevének pontos módját használja
* Az Azure AD felhasználói és csoportjai neve megkülönbözteti a kis-és nagybetűket
* Csoportként való csatlakozáskor csak a csoport nevét használja (például). `GroupName@mydb`
* Ha a név szóközöket tartalmaz, akkor az `\` egyes területek előtt használja a megszökni

Most már hitelesítve van a PostgreSQL-kiszolgálón az Azure AD-hitelesítéssel.

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>Azure AD-felhasználók létrehozása a Azure Database for PostgreSQLban

Ha Azure AD-felhasználót szeretne hozzáadni a Azure Database for PostgreSQL-adatbázishoz, hajtsa végre a következő lépéseket a csatlakozás után (lásd: a kapcsolódási útmutató későbbi szakasza):

1. Először győződjön meg arról, hogy az Azure AD `<user>@yourtenant.onmicrosoft.com` -felhasználó érvényes felhasználó az Azure ad-bérlőben.
2. Jelentkezzen be az Azure Database for PostgreSQL-példányba az Azure AD-rendszergazda felhasználóként.
3. Szerepkör létrehozása `<user>@yourtenant.onmicrosoft.com` a Azure Database for PostgreSQLban.
4. `<user>@yourtenant.onmicrosoft.com`A szerepkör azure_ad_user tagja legyen. Ezt csak az Azure AD-felhasználók kapják meg.

**Példa**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> Egy felhasználó Azure AD-n keresztüli hitelesítése nem biztosít semmilyen engedélyt a felhasználónak a Azure Database for PostgreSQL-adatbázisban lévő objektumok eléréséhez. A szükséges engedélyeket manuálisan kell megadnia a felhasználónak.

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>Azure AD-csoportok létrehozása a Azure Database for PostgreSQLban

Ha engedélyezni szeretné az Azure AD-csoport számára az adatbázishoz való hozzáférést, használja ugyanazt a mechanizmust, mint a felhasználók számára, hanem adja meg a csoport nevét:

**Példa**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

Bejelentkezéskor a csoport tagjai a személyes hozzáférési jogkivonatokat fogják használni, de a felhasználónévként megadott csoport nevével jelentkezhetnek be.

## <a name="token-validation"></a>Jogkivonat ellenőrzése

Azure Database for PostgreSQL Azure AD-hitelesítés biztosítja, hogy a felhasználó létezik a PostgreSQL-kiszolgálón, és ellenőrzi a jogkivonat érvényességét a jogkivonat tartalmának ellenőrzésével. A rendszer a következő jogkivonat-ellenőrzési lépéseket hajtja végre:

- Az Azure AD aláírja a tokent, és nem módosították
- A tokent az Azure AD adta ki a kiszolgálóhoz társított bérlőhöz
- A jogkivonat nem járt le
- Token a Azure Database for PostgreSQL erőforráshoz (és nem egy másik Azure-erőforráshoz)

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>Meglévő PostgreSQL-felhasználók áttelepítése Azure AD-alapú hitelesítésre

Engedélyezheti az Azure AD-hitelesítést a meglévő felhasználók számára. Két esetet érdemes figyelembe venni:

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>1. eset: a PostgreSQL felhasználóneve megegyezik az Azure AD egyszerű felhasználónevével

Abban az esetben, ha a meglévő felhasználók már megfelelnek az Azure AD-beli felhasználóneveknek, a szerepkört megadhatja a `azure_ad_user` számukra az Azure ad-hitelesítés engedélyezése érdekében:

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

Mostantól képesek lesznek bejelentkezni az Azure AD hitelesítő adataival ahelyett, hogy a korábban konfigurált PostgreSQL felhasználói jelszavukat használják.

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>2. eset: a PostgreSQL felhasználóneve eltér az Azure AD egyszerű Felhasználónevetől

Ha egy PostgreSQL-felhasználó vagy nem létezik az Azure AD-ben, vagy más felhasználóneve van, akkor az Azure AD-csoportok használatával hitelesítheti magát a PostgreSQL-felhasználóként. A meglévő Azure Database for PostgreSQL felhasználókat áttelepítheti az Azure AD-ba úgy, hogy létrehoz egy olyan nevű Azure AD-csoportot, amelynek a neve megegyezik a PostgreSQL-felhasználóval, majd megadhatja a szerepkört azure_ad_user a meglévő PostgreSQL-felhasználónak:

```sql
GRANT azure_ad_user TO "DBReadUser";
```

Ez azt feltételezi, hogy létrehozott egy "DBReadUser" csoportot az Azure AD-ben. Az adott csoportba tartozó felhasználók mostantól a felhasználóként bejelentkezhetnek az adatbázisba.

## <a name="next-steps"></a>Következő lépések

* Tekintse át a [Azure Database for PostgreSQL – egyetlen kiszolgálóval Azure Active Directory hitelesítéssel](concepts-aad-authentication.md) kapcsolatos általános fogalmakat

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
