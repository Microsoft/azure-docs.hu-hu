---
title: Átmeneti kapcsolódási hibák – Azure Database for MySQL
description: Megtudhatja, hogyan kezelheti az átmeneti kapcsolódási hibákat, és hogyan csatlakozhat hatékonyan a Azure Database for MySQLhoz.
keywords: MySQL-kapcsolat, kapcsolati karakterlánc, kapcsolódási problémák, átmeneti hiba, kapcsolódási hiba, hatékony csatlakozás
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 89673c14c38947dc5aeb91cacde1eb2755e84138
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542609"
---
# <a name="handle-transient-errors-and-connect-efficiently-to-azure-database-for-mysql"></a>Átmeneti hibák kezelése és hatékony kapcsolódás Azure Database for MySQL

Ez a cikk bemutatja, hogyan kezelheti az átmeneti hibákat, és hogyan csatlakozhat hatékonyan a Azure Database for MySQLhoz.

## <a name="transient-errors"></a>Átmeneti hibák

Egy átmeneti hiba (más néven átmeneti hiba) egy olyan hiba, amely magát a megoldást fogja megoldani. Ezek a hibák általában az eldobott adatbázis-kiszolgálóval létesített kapcsolatok. Nem nyitható meg a kiszolgálóval létesített új kapcsolatok. Átmeneti hibák merülhetnek fel, például ha hardveres vagy hálózati hiba történik. Egy másik ok lehet a Pásti-szolgáltatás új verziója. Az események többségét a rendszer automatikusan csökkenti a 60 másodpercnél kisebb mértékben. A Felhőbeli alkalmazások tervezéséhez és fejlesztéséhez ajánlott eljárás az átmeneti hibák elvárható. Tegyük fel, hogy bármikor megtörténhetnek bármely összetevőben, és az ilyen helyzetek kezeléséhez megfelelő logikával kell rendelkeznie.

## <a name="handling-transient-errors"></a>Átmeneti hibák elhárítása

Az átmeneti hibákat az újrapróbálkozási logikával kell kezelni. Olyan helyzetek, amelyeket figyelembe kell venni:

* Hiba történik a kapcsolatok megnyitásakor
* Az üresjárati kapcsolatok el lettek dobva a kiszolgáló oldalán. Amikor megpróbál kiadni egy parancsot, nem hajtható végre
* A rendszer eldobott egy olyan aktív kapcsolatokat, amely jelenleg egy parancsot futtat.

Az első és a második eset viszonylag azonnal továbbítható a kezelésére. Próbálja meg újra megnyitni a kapcsolódást. Ha a művelet sikeres, az átmeneti hibát a rendszeren enyhítjük. Újra használhatja a Azure Database for MySQL. Azt javasoljuk, hogy várjon a kapcsolódás megkísérlése előtt. Vissza, ha a kezdeti újrapróbálkozások sikertelenek lesznek. Így a rendszeren az összes rendelkezésre álló erőforrás felhasználható a hiba helyzetének leküzdésére. A követendő példa a következő:

* Várjon 5 másodpercig az első újrapróbálkozás előtt.
* Minden egyes újrapróbálkozáskor a várakozás exponenciálisan növekszik, akár 60 másodpercre.
* Állítsa be az újrapróbálkozások maximális számát, amikor az alkalmazás nem tudta végrehajtani a műveletet.

Ha egy aktív tranzakcióval rendelkező kapcsolat meghiúsul, nehezebb a helyreállítás megfelelő kezelése. Két eset létezik: Ha a tranzakció csak olvasható, akkor a rendszer biztonságosan újra megnyithatja a kapcsolódást, és újrapróbálkozhat a tranzakcióval. Ha azonban a tranzakciót is megírta az adatbázisba, meg kell határoznia, hogy a tranzakció vissza lett-e állítva, vagy az átmeneti hiba miatt sikeres volt-e. Ebben az esetben előfordulhat, hogy nem fogadta el az adatbázis-kiszolgálóról a véglegesítő nyugtát.

Ennek egyik módja egy egyedi azonosító létrehozása az ügyfélen, amely az összes újrapróbálkozáshoz használatos. Ezt az egyedi azonosítót a tranzakció részeként adja át a kiszolgálónak, és egy egyedi korlátozással rendelkező oszlopban tárolja. Így biztonságosan újrapróbálkozhat a tranzakcióval. A művelet sikeres lesz, ha az előző tranzakció vissza lett állítva, és az ügyfél által generált egyedi azonosító még nem létezik a rendszeren. Ha az egyedi azonosítót korábban tárolta, a rendszer duplikált kulcs megsértését jelzi, mert az előző tranzakció sikeresen befejeződött.

Ha a program a Azure Database for MySQL harmadik féltől származó middleware-n keresztül kommunikál, kérje meg a gyártót, hogy az átmeneti hibák esetén a middleware tartalmazza-e az újrapróbálkozási logikát.

Ügyeljen arra, hogy tesztelje újra a logikát. Próbálja meg például végrehajtani a kódot a Azure Database for MySQL-kiszolgáló számítási erőforrásainak felfelé vagy lefelé skálázásakor. Az alkalmazásnak gond nélkül kell kezelnie a művelet során észlelt rövid állásidőt.

## <a name="connect-efficiently-to-azure-database-for-mysql"></a>Hatékony kapcsolódás Azure Database for MySQL

Az adatbázis-kapcsolatok korlátozott erőforrást jelentenek, így a kapcsolatok készletezésének hatékony felhasználása Azure Database for MySQL a teljesítmény optimalizálása érdekében. Az alábbi szakasz azt ismerteti, hogyan használhatók a kapcsolatok készletezési vagy állandó kapcsolatai a hatékonyabb hozzáférés Azure Database for MySQL.

## <a name="access-databases-by-using-connection-pooling-recommended"></a>Adatbázisok elérése a kapcsolati készletezés használatával (ajánlott)

Az adatbázis-kapcsolatok kezelése jelentős hatással lehet az alkalmazás teljesítményére. Az alkalmazás teljesítményének optimalizálása érdekében a cél az, hogy csökkentse a kapcsolatok számát, valamint a kapcsolatok létrehozásának idejét a kulcstartó elérési útjain. Javasoljuk, hogy az adatbázis-kapcsolat készletezésével vagy állandó kapcsolataival kapcsolódjon Azure Database for MySQLhoz. Az adatbázis-kapcsolat készletezése kezeli az adatbázis-kapcsolatok létrehozását, kezelését és kiosztását. Amikor egy program adatbázis-kapcsolatot kér, az új kapcsolat létrehozása helyett rangsorolja a meglévő tétlen adatbázis-kapcsolatok kiosztását. Miután a program befejezte az adatbázis-kapcsolatok használatát, a rendszer az előkészítés során visszaállítja a kapcsolatokat a további használatra, nem pedig egyszerűen bezárták.

A jelen cikk jobb illusztrációként tartalmaz [egy olyan mintakód-kódot](./sample-scripts-java-connection-pooling.md) , amely példaként a Java-t használja. További információ: [apache Common DBCP](https://commons.apache.org/proper/commons-dbcp/).

> [!NOTE]
> A kiszolgáló egy időtúllépési mechanizmust konfigurál egy olyan kapcsolat bezárásához, amely inaktív állapotban van egy bizonyos ideig, hogy erőforrásokat szabadítson fel. Ügyeljen arra, hogy az ellenőrzési rendszert úgy állítsa be, hogy biztosítsa az állandó kapcsolatok hatékonyságát a használatakor. További információ: az [ellenőrzési rendszerek konfigurálása az ügyféloldali oldalon az állandó kapcsolatok hatékonyságának biztosítása érdekében](concepts-connectivity.md#configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections).

## <a name="access-databases-by-using-persistent-connections-recommended"></a>Adatbázisok elérése állandó kapcsolatok használatával (ajánlott)

Az állandó kapcsolatok fogalma hasonló a kapcsolati készletezéshez. Az állandó kapcsolatokkal rendelkező rövid kapcsolatok cseréje csak kisebb módosításokat igényel a kódban, de jelentős hatással van a teljesítmény javítására számos tipikus alkalmazási helyzetben.

## <a name="access-databases-by-using-wait-and-retry-mechanism-with-short-connections"></a>Adatbázisok elérése a várakozási és újrapróbálkozási mechanizmussal rövid kapcsolatok használatával

Ha erőforrás-korlátozásokkal rendelkezik, javasoljuk, hogy adatbázis-készletezési vagy állandó kapcsolatokat használjon az adatbázisok eléréséhez. Ha az alkalmazás rövid kapcsolatokat használ, és kapcsolódási hibákat tapasztal, ha az egyidejű kapcsolatok számának felső határát közelíti meg, próbálkozzon a várakozással és az újrapróbálkozási mechanizmussal. Megadhatja a megfelelő várakozási időt, az első kísérlet után rövidebb várakozási idővel. Ezt követően többször is megpróbálhat eseményeket várni.

## <a name="configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections"></a>Ellenőrzési mechanizmusok konfigurálása az ügyfeleken az állandó kapcsolatok hatékonyságának megerősítéséhez

A kiszolgáló egy időtúllépési mechanizmust konfigurál egy üresjárati állapotban lévő kapcsolat bezárására egy kis idő elteltével, hogy erőforrásokat szabadítson fel. Amikor az ügyfél ismét hozzáfér az adatbázishoz, azzal egyenértékű az ügyfél és a kiszolgáló közötti új kapcsolódási kérelem létrehozásával. Ha biztosítani szeretné a kapcsolatok hatékonyságát a használatuk során, állítson be egy ellenőrzési mechanizmust az ügyfélen. Ahogy az az alábbi példában is látható, a Tomcat JDBC-kapcsolatok készletezési szolgáltatásával konfigurálhatja ezt az ellenőrzési mechanizmust.

Ha a TestOnBorrow paramétert új kérelemre állítja be, a kapcsolati készlet automatikusan ellenőrzi a rendelkezésre álló tétlen kapcsolatok hatékonyságát. Ha ez a kapcsolat érvényes, akkor a közvetlenül visszaadott másik kapcsolati készlet visszavonja a kapcsolatokat. A kapcsolatfájl Ezután létrehoz egy új, hatékony és visszaadott kapcsolatokat. Ez a folyamat biztosítja, hogy az adatbázis hatékonyan legyen elérhető. 

Az adott beállításokkal kapcsolatos további információkért lásd a [JDBC-kapcsolatok készletének hivatalos bevezetési dokumentumát](https://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html#Common_Attributes). Elsősorban a következő három paramétert kell megadnia: TestOnBorrow (True értékre állítva), ValidationQuery (1. kiválasztva) és ValidationQueryTimeout (1 értékre állítva). Az adott mintakód alább látható:

```java
public class SimpleTestOnBorrowExample {
      public static void main(String[] args) throws Exception {
          PoolProperties p = new PoolProperties();
          p.setUrl("jdbc:mysql://localhost:3306/mysql");
          p.setDriverClassName("com.mysql.jdbc.Driver");
          p.setUsername("root");
          p.setPassword("password");
            // The indication of whether objects will be validated by the idle object evictor (if any). 
            // If an object fails to validate, it will be dropped from the pool. 
            // NOTE - for a true value to have any effect, the validationQuery or validatorClassName parameter must be set to a non-null string. 
          p.setTestOnBorrow(true); 

            // The SQL query that will be used to validate connections from this pool before returning them to the caller.
            // If specified, this query does not have to return any data, it just can't throw a SQLException.
          p.setValidationQuery("SELECT 1");

            // The timeout in seconds before a connection validation queries fail. 
            // This works by calling java.sql.Statement.setQueryTimeout(seconds) on the statement that executes the validationQuery. 
            // The pool itself doesn't timeout the query, it is still up to the JDBC driver to enforce query timeouts. 
            // A value less than or equal to zero will disable this feature.
          p.setValidationQueryTimeout(1);
            // set other useful pool properties.
          DataSource datasource = new DataSource();
          datasource.setPoolProperties(p);

          Connection con = null;
          try {
            con = datasource.getConnection();
            // execute your query here
          } finally {
            if (con!=null) try {con.close();}catch (Exception ignore) {}
          }
      }
  }
```

## <a name="next-steps"></a>Következő lépések

* [Az Azure Database for MySQL csatlakoztatási hibáinak elhárítása](howto-troubleshoot-common-connection-issues.md)
