---
title: Titkosított kapcsolat a TLS/SSL használatával Azure Database for MySQL rugalmas kiszolgálón
description: Útmutatás és információk a TLS/SSL használatával történő kapcsolódáshoz Azure Database for MySQL-rugalmas kiszolgálón.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 2e66b27f7f0c731e17bb9811e2376bbe09c1bc12
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950144"
---
# <a name="connect-to-azure-database-for-mysql---flexible-server-over-tls12ssl"></a>Kapcsolódás Azure Database for MySQL – rugalmas kiszolgáló TLS 1.2/SSL protokollon keresztül

> [!IMPORTANT]
> Azure Database for MySQL rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el

Azure Database for MySQL rugalmas kiszolgáló támogatja az ügyfélalkalmazások a MySQL szolgáltatáshoz való csatlakoztatását Transport Layer Security (TLS), korábbi nevén SSL (SSL) használatával. A TLS egy iparági szabványnak megfelelő protokoll, amely biztosítja a titkosított hálózati kapcsolatokat az adatbázis-kiszolgáló és az ügyfélalkalmazások között, így biztosítva a megfelelőségi követelmények betartását.

Azure Database for MySQL a rugalmas kiszolgáló csak a titkosított kapcsolatokat támogatja Transport Layer Security (TLS 1,2) használatával, és a TLS 1,0 és a TLS 1,1 összes bejövő kapcsolata meg lesz tagadva. A TLS-kapcsolatok kényszerítését engedélyező rugalmas kiszolgálók esetében a TLS/SSL nem tiltható le a rugalmas kiszolgálóhoz való csatlakozáshoz.

## <a name="download-the-public-ssl-certificate"></a>Nyilvános SSL-tanúsítvány letöltése
A alkalmazással való használathoz töltse le a [nyilvános SSL-tanúsítványt](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem).

Mentse a tanúsítványfájl a kívánt helyre. Ez az oktatóanyag például a `c:\ssl` vagy a `\var\www\html\bin` helyi környezetét, vagy az alkalmazás által üzemeltetett ügyfél-környezetet használja. Ez lehetővé teszi az alkalmazások számára az SSL-kapcsolaton keresztüli biztonságos kapcsolódást az adatbázishoz. 

### <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>Kapcsolat a MySQL parancssori ügyféllel a TLS/SSL használatával

Ha a rugalmas kiszolgálót *privát hozzáféréssel (VNet-integrációval)* hozta létre, akkor a kiszolgálóval egy olyan erőforráshoz kell csatlakoznia, amely a kiszolgálóval megegyező VNet belül található. Létrehozhat egy virtuális gépet, és hozzáadhatja azt a rugalmas kiszolgálóval létrehozott VNet.

Ha a rugalmas kiszolgálót *nyilvános hozzáféréssel (engedélyezett IP-címekkel)* hozta létre, akkor a helyi IP-címét felveheti a kiszolgálón található tűzfalszabályok listájára.

[mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) vagy [MySQL Workbench](./connect-workbench.md)--> közül választhat, ha a helyi környezetből kíván csatlakozni a kiszolgálóhoz. 

Az alábbi példa bemutatja, hogyan csatlakozhat a kiszolgálóhoz a MySQL parancssori felület használatával. A `--ssl-mode=REQUIRED` kapcsolati karakterlánc beállításával kényszerítheti a TLS/SSL-tanúsítványok ellenőrzését. Adja át a helyi tanúsítványfájl elérési útját a `--ssl-ca` paraméternek. Cserélje le az értékeket a tényleges kiszolgáló nevére és jelszavára. 

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\DigiCertGlobalRootCA.crt.pem
```
> [!Note]
> Ellenőrizze, hogy az átadott érték megegyezik-e a `--ssl-ca` mentett tanúsítvány elérési útjával.

### <a name="verify-the-tlsssl-connection"></a>A TLS/SSL-kapcsolat ellenőrzése

A MySQL **status** parancs végrehajtásával ellenőrizze, hogy a TLS/SSL használatával csatlakozott-e a MySQL-kiszolgálóhoz:

```dos
mysql> status
```
Ellenőrizze, hogy a kapcsolat titkosítva van-e a kimenet áttekintésével, amelynek meg kell jelennie a következőnek:  **SSL: a használatban lévő REJTJEL AES256-SHA**. Ez a titkosító csomag egy példát mutat be, és az ügyfél alapján egy másik titkosító csomagot láthat.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Győződjön meg arról, hogy az alkalmazás vagy a keretrendszer támogatja a TLS-kapcsolatokat

Néhány alkalmazás-keretrendszer, amely a MySQL-t használja az adatbázis-szolgáltatásaihoz, alapértelmezés szerint nem engedélyezi a TLS-t a telepítés során. A MySQL-kiszolgáló kikényszeríti a TLS-kapcsolatokat, de ha az alkalmazás nincs konfigurálva a TLS-hez, előfordulhat, hogy az alkalmazás nem tud csatlakozni az adatbázis-kiszolgálóhoz. A TLS-kapcsolatok engedélyezésével kapcsolatos információkért tekintse meg az alkalmazás dokumentációját.

## <a name="sample-code"></a>Mintakód
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
