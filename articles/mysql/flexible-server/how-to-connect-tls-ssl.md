---
title: Titkosított kapcsolat a TLS/SSL használatával Azure Database for MySQL rugalmas kiszolgálón
description: Útmutatás és információk a TLS/SSL használatával történő kapcsolódáshoz Azure Database for MySQL-rugalmas kiszolgálón.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 6dbb1b46aef40986fc2d601aee152aed02591ac0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312603"
---
# <a name="connect-to-azure-database-for-mysql---flexible-server-with-encrypted-connections"></a>Csatlakozás Azure Database for MySQL-rugalmas kiszolgálóhoz titkosított kapcsolattal

> [!IMPORTANT]
> Azure Database for MySQL rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el

Azure Database for MySQL rugalmas kiszolgáló támogatja az ügyfélalkalmazások csatlakoztatását a MySQL-kiszolgálóhoz az SSL (SSL) és a Transport Layer Security (TLS) titkosítás használatával. A TLS egy iparági szabványnak megfelelő protokoll, amely biztosítja a titkosított hálózati kapcsolatokat az adatbázis-kiszolgáló és az ügyfélalkalmazások között, így biztosítva a megfelelőségi követelmények betartását.

Azure Database for MySQL rugalmas kiszolgáló támogatja a titkosított kapcsolatokat Transport Layer Security (TLS 1,2) használatával, alapértelmezés szerint a TLS 1,0 és a TLS 1,1 összes bejövő kapcsolata alapértelmezés szerint meg lesz tagadva. A titkosított kapcsolatok kényszerítésének vagy TLS-verziójának konfigurációja a rugalmas kiszolgálón a cikkben tárgyalt módon módosítható. 

A következőkben az SSL-és TLS-beállítások különböző konfigurációit használhatja a rugalmas kiszolgálóhoz:

| Eset   | Kiszolgáló paraméterének beállításai      | Description                                    |
|------------|--------------------------------|------------------------------------------------|
|SSL-kényszerítés letiltása | require_secure_transport = kikapcsolva |Ha az örökölt alkalmazás nem támogatja a MySQL-kiszolgálóval létesített titkosított kapcsolatokat, akkor a require_secure_transport = OFF beállítással letilthatja a titkosított kapcsolatok kényszerítését a rugalmas kiszolgálóval.|
|SSL kikényszerítés TLS-verzióval < 1,2 | require_secure_transport = ON és tls_version = TLSV1 vagy TLS 1.1| Ha az örökölt alkalmazás támogatja a titkosított kapcsolatokat, de a 1,2-es TLS-< verzióra van szükség, akkor engedélyezheti a titkosított kapcsolatokat, de a rugalmas kiszolgáló konfigurálásával engedélyezheti az alkalmazás által támogatott TLS-verzióhoz (v 1.0 vagy v 1.1) való csatlakozást.|
|SSL betartatása TLS-verzióval = 1.2 (alapértelmezett konfiguráció)|require_secure_transport = ON és tls_version = TLS 1.2| Ez a rugalmas kiszolgáló ajánlott és alapértelmezett konfigurációja.|
|SSL kikényszerített TLS-verzió = 1.3 (a MySQL v 8.0-s és újabb verzióival támogatott)| require_secure_transport = ON és tls_version = TLS 1.3| Ez hasznos és ajánlott az új alkalmazások fejlesztéséhez|

> [!Note]
> Az SSL-titkosítás rugalmas kiszolgálón történő módosítása nem támogatott. Az FIPS titkosítási csomagok alapértelmezés szerint érvényben vannak, ha tls_version a TLS 1,2-es verzióra van beállítva. Az 1,2-es verziótól eltérő TLS-verziók esetén az SSL-titkosítás a MySQL Közösség telepítésével kapcsolatos alapértelmezett beállításokra van beállítva.

Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:
* Rugalmas kiszolgáló konfigurálása 
  * SSL-sel letiltva 
  * SSL-vel kényszerítve TLS-verzióval < 1,2
* Kapcsolódás a rugalmas kiszolgálóhoz a MySQL parancssori felület használatával 
  * Titkosított kapcsolatok letiltásával
  * Titkosított kapcsolatok engedélyezése esetén
* A kapcsolatok titkosítási állapotának ellenőrzése
* Csatlakozás a rugalmas kiszolgálóhoz titkosított kapcsolattal különböző alkalmazási keretrendszerek használatával

## <a name="disable-ssl-enforcement-on-your-flexible-server"></a>Az SSL-kényszerítés letiltása a rugalmas kiszolgálón
Ha az ügyfélalkalmazás nem támogatja a titkosított kapcsolatokat, akkor le kell tiltania a titkosított kapcsolatok kényszerítését a rugalmas kiszolgálón. A titkosított kapcsolatok kényszerítésének letiltásához be kell állítania require_secure_transport Server paramétert úgy, hogy ki legyen kapcsolva a képernyőképen látható módon, és mentse a kiszolgálói paraméter konfigurációját az érvénybe léptetéséhez. a require_secure_transport egy **dinamikus kiszolgálói paraméter** , amely azonnal érvénybe lép, és nem igényli a kiszolgáló újraindítását a működés érvénybe léptetéséhez.

> :::image type="content" source="./media/how-to-connect-tls-ssl/disable-ssl.png" alt-text="A Azure Database for MySQL rugalmas kiszolgálóval való SSL letiltását bemutató képernyőkép.":::

### <a name="connect-using-mysql-command-line-client-with-ssl-disabled"></a>Kapcsolat a MySQL parancssori ügyféllel az SSL használatával letiltva

Az alábbi példa bemutatja, hogyan csatlakozhat a kiszolgálóhoz a MySQL parancssori felület használatával. A `--ssl-mode=DISABLED` kapcsolati karakterlánc beállítás használatával tiltsa le a TLS/SSL-kapcsolatokat a MySQL-ügyfélről. Cserélje le az értékeket a tényleges kiszolgáló nevére és jelszavára. 

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=DISABLED 
```
Fontos megjegyezni, hogy a require_secure_transport kikapcsolásának beállítása nem jelenti azt, hogy a kiszolgáló oldalán nem támogatottak a titkosított kapcsolatok. Ha a require_secure_transportt kikapcsolja a rugalmas kiszolgálón, de ha az ügyfél titkosított kapcsolattal csatlakozik, a rendszer továbbra is elfogadja. Az alábbi, a MySQL-ügyfelet használó, require_secure_transport = OFF-val konfigurált rugalmas kiszolgálóhoz való kapcsolódás az alább látható módon fog működni.

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED
```
```output
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 17
Server version: 5.7.29-log MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show global variables like '%require_secure_transport%';
+--------------------------+-------+
| Variable_name            | Value |
+--------------------------+-------+
| require_secure_transport | OFF   |
+--------------------------+-------+
1 row in set (0.02 sec)
```

Összefoglalva, require_secure_transport = OFF beállítás ellazítja a titkosított kapcsolatok kényszerítését a rugalmas kiszolgálón, és lehetővé teszi, hogy a titkosított kapcsolatok mellett nem titkosított kapcsolatok legyenek a kiszolgálóhoz az ügyféltől.

## <a name="enforce-ssl-with-tls-version--12"></a>SSL kikényszerítés TLS-verzióval < 1,2

Ha az alkalmazás támogatja a MySQL-kiszolgálóhoz való kapcsolódást SSL-kapcsolaton keresztül, de támogatja a TLS-verziót < 1,2, akkor a TLS-verziók kiszolgálói paramétert kell beállítania a rugalmas kiszolgálón. A rugalmas kiszolgáló támogatásához használni kívánt TLS-verziók beállításához tls_version Server paramétert kell beállítania a TLSV1, a TLS 1.1 vagy a TLSV1 és a TLS 1.1 értékre, ahogy azt a képernyőképen is láthatja, és a kiszolgálói paraméter konfigurációját mentse az életbe léptetéséhez. a tls_version egy **statikus kiszolgálói paraméter** , amely a paraméter érvénybe léptetéséhez a kiszolgáló újraindítását igényli.

> :::image type="content" source="./media/how-to-connect-tls-ssl/tls-version.png" alt-text="A Azure Database for MySQL rugalmas kiszolgáló TLS-verziójának beállítását bemutató képernyőkép.":::

## <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>Kapcsolat a MySQL parancssori ügyféllel a TLS/SSL használatával

### <a name="download-the-public-ssl-certificate"></a>Nyilvános SSL-tanúsítvány letöltése
Az ügyfélalkalmazások titkosított kapcsolatainak használatához le kell töltenie a [nyilvános SSL-tanúsítványt](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) , amely Azure Portal hálózati panelen is elérhető, ahogy az alábbi képernyőképen is látható.

> :::image type="content" source="./media/how-to-connect-tls-ssl/download-ssl.png" alt-text="A nyilvános SSL-tanúsítvány Azure Portalból való letöltését bemutató képernyőkép.":::

Mentse a tanúsítványfájl a kívánt helyre. Ez az oktatóanyag például a `c:\ssl` vagy a `\var\www\html\bin` helyi környezetét, vagy az alkalmazás által üzemeltetett ügyfél-környezetet használja. Ez lehetővé teszi az alkalmazások számára az SSL-kapcsolaton keresztüli biztonságos kapcsolódást az adatbázishoz.

Ha a rugalmas kiszolgálót *privát hozzáféréssel (VNet-integrációval)* hozta létre, akkor a kiszolgálóval egy olyan erőforráshoz kell csatlakoznia, amely a kiszolgálóval megegyező VNet belül található. Létrehozhat egy virtuális gépet, és hozzáadhatja azt a rugalmas kiszolgálóval létrehozott VNet.

Ha a rugalmas kiszolgálót *nyilvános hozzáféréssel (engedélyezett IP-címekkel)* hozta létre, akkor a helyi IP-címét felveheti a kiszolgálón található tűzfalszabályok listájára.

[mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) vagy [MySQL Workbench](./connect-workbench.md)--> közül választhat, ha a helyi környezetből kíván csatlakozni a kiszolgálóhoz. 

Az alábbi példa bemutatja, hogyan csatlakozhat a kiszolgálóhoz a MySQL parancssori felület használatával. A `--ssl-mode=REQUIRED` kapcsolati karakterlánc beállításával kényszerítheti a TLS/SSL-tanúsítványok ellenőrzését. Adja át a helyi tanúsítványfájl elérési útját a `--ssl-ca` paraméternek. Cserélje le az értékeket a tényleges kiszolgáló nevére és jelszavára. 

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!Note]
> Ellenőrizze, hogy az átadott érték megegyezik-e a `--ssl-ca` mentett tanúsítvány elérési útjával.

Ha nem titkosított kapcsolattal próbál csatlakozni a kiszolgálóhoz, a következőhöz hasonló hibaüzenet jelenik meg, amely nem biztonságos átvitelt használ:

```output
ERROR 3159 (HY000): Connections using insecure transport are prohibited while --require_secure_transport=ON.
```

## <a name="verify-the-tlsssl-connection"></a>A TLS/SSL-kapcsolat ellenőrzése

A MySQL **status** parancs végrehajtásával ellenőrizze, hogy a TLS/SSL használatával csatlakozott-e a MySQL-kiszolgálóhoz:

```dos
mysql> status
```
Ellenőrizze, hogy a kapcsolat titkosítva van-e a kimenet áttekintésével, amelynek a következőnek kell megjelennie: * * SSL: a használatban lévő rejtjel * *. Ez a titkosító csomag egy példát mutat be, és az ügyfél alapján egy másik titkosító csomagot láthat.

## <a name="connect-to-your-flexible-server-with-encrypted-connections-using-various-application-frameworks"></a>Csatlakozás a rugalmas kiszolgálóhoz titkosított kapcsolattal különböző alkalmazási keretrendszerek használatával

A (z) Azure Portal kiszolgálója számára elérhető "kapcsolati karakterláncok" lapon előre definiált kapcsolati karakterláncok tartalmazzák az adatbázis-kiszolgálóhoz TLS/SSL használatával való csatlakozáshoz szükséges paramétereket. A TLS/SSL-paraméter az összekötőtől függően változik. Például: "useSSL = true", "sslmode = Required" vagy "ssl_verify_cert = true" és más változatok.

Ha az alkalmazásból TLS/SSL protokollon keresztül titkosított kapcsolatot szeretne létesíteni a rugalmas kiszolgálóval, tekintse át az alábbi kódrészleteket:

### <a name="wordpress"></a>WordPress
Töltse le az [SSL nyilvános tanúsítványát](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) , és adja hozzá a következő sorokat a wp-config. php fájlhoz a sor után ```// ** MySQL settings - You can get this info from your web host ** //``` .

```php
//** Connect with SSL** //
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
//** SSL CERT **//
define('MYSQL_SSL_CERT','/FULLPATH/on-client/to/DigiCertGlobalRootCA.crt.pem');
```

### <a name="php"></a>PHP

```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

### <a name="php-using-pdo"></a>PHP (OEM használatával)

```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'myadmin', 'yourpassword', $options);
```

### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)

```python
try:
    conn = mysql.connector.connect(user='myadmin',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/DigiCertGlobalRootCA.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)

```python
conn = pymysql.connect(user='myadmin',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'})
```

### <a name="django-pymysql"></a>Django (PyMySQL)

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby

```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :sslca => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
    )
```

### <a name="golang"></a>Golang

```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/DigiCertGlobalRootCA.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')
db, _ := sql.Open("mysql", connectionString)
```

### <a name="java-mysql-connector-for-java"></a>Java (MySQL-összekötő Java-hoz)

```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="java-mariadb-connector-for-java"></a>Java (MariaDB-összekötő Java-hoz)

```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)

```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    SslCa = "DigiCertGlobalRootCA.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

## <a name="next-steps"></a>Következő lépések
- [A MySQL Workbench használata Azure Database for MySQL rugalmas kiszolgálóval való kapcsolódáshoz és adatlekérdezéshez](./connect-workbench.md)
- [A PHP használata a Azure Database for MySQL rugalmas kiszolgálóval való kapcsolódáshoz és az adatlekérdezéshez](./connect-php.md)
- [Azure Database for MySQL rugalmas kiszolgálói virtuális hálózat létrehozása és kezelése az Azure CLI-](./how-to-manage-virtual-network-cli.md)vel.
- További információ a [Azure Database for MySQL rugalmas kiszolgáló hálózatkezeléséről](./concepts-networking.md)
- További információ a [Azure Database for MySQL rugalmas kiszolgálói tűzfalszabályok](./concepts-networking.md#public-access-allowed-ip-addresses)
