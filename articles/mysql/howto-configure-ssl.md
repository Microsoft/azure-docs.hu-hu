---
title: Az SSL konfigurálása – Azure Database for MySQL
description: Útmutatás a Azure Database for MySQL és a társított alkalmazások megfelelő konfigurálásához az SSL-kapcsolatok megfelelő használatához
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 07/08/2020
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 1c3e5a44e01f3fa43b82644103066f5a03684ad2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94591580"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Az SSL-kapcsolat konfigurálása az alkalmazásban a Azure Database for MySQLhoz való biztonságos csatlakozáshoz

A Azure Database for MySQL támogatja a Azure Database for MySQL-kiszolgáló SSL (SSL) használatával történő ügyfélalkalmazások összekapcsolását. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kikényszerítése elősegíti a „köztes” támadások elleni védelmet, mert titkosítja a kiszolgáló és az alkalmazás közötti streameket.

## <a name="step-1-obtain-ssl-certificate"></a>1. lépés: SSL-tanúsítvány beszerzése

Töltse le az SSL protokollon keresztüli kommunikációhoz szükséges tanúsítványt a Azure Database for MySQL-kiszolgálóról [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) , és mentse a tanúsítványt a helyi meghajtóra (ez az oktatóanyag az c:\ssl-t használja például).
**A Microsoft Internet Explorer és a Microsoft Edge esetében:** A letöltés befejezése után nevezze át a tanúsítványt a BaltimoreCyberTrustRoot. CRT. PEM névre.

>[!NOTE]
> Az ügyfelek visszajelzései alapján kibővítettük a meglévő Baltimore legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ főtanúsítványának elavulttá tételét a 2021. február 15-ig (02/15/2021).

> [!IMPORTANT] 
> Az SSL-főtanúsítvány az 2021-as (02/15/2021-as) 15. február 15-től lejár. Frissítse az alkalmazást az [új tanúsítvány](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)használatára. További információ: [tervezett tanúsítványok frissítései](concepts-certificate-rotation.md)

Tekintse meg a következő hivatkozásokat a szuverén felhőkben található kiszolgálók tanúsítványainak esetében: [Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure China](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)és [Azure Germany](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).

## <a name="step-2-bind-ssl"></a>2. lépés: SSL kötése

Adott programozási nyelvi kapcsolódási karakterláncok esetén tekintse meg az alábbi [mintakód](howto-configure-ssl.md#sample-code) .

### <a name="connecting-to-server-using-mysql-workbench-over-ssl"></a>Csatlakozás a kiszolgálóhoz a MySQL Workbench használatával SSL-en keresztül

Konfigurálja a MySQL Workbench-t a biztonságos SSL-kapcsolathoz.

1. Az új kapcsolat beállítása párbeszédablakban navigáljon az **SSL** lapra.

1. Frissítse az **SSL használata** mezőt a kötelező értékre.

1. Az **SSL-hitelesítésszolgáltató fájlja:** mezőben adja meg a **BaltimoreCyberTrustRoot. CRT. PEM** fájljának helyét.

   :::image type="content" source="./media/howto-configure-ssl/mysql-workbench-ssl.png" alt-text="SSL-konfiguráció mentése":::

A meglévő kapcsolatok esetében a jobb gombbal a kapcsolat ikonjára kattintva, majd a szerkesztés lehetőséget választva lehet kötni az SSL-t. Ezután navigáljon az **SSL** lapra, és kösse a tanúsítvány-fájlt.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Kapcsolódás a kiszolgálóhoz a MySQL CLI-vel SSL használatával

Az SSL-tanúsítvány kötésének másik módja a MySQL parancssori felület használata a következő parancsok végrehajtásával.

```bash
mysql.exe -h mydemoserver.mysql.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> A MySQL parancssori felület Windows rendszeren való használatakor hibaüzenetet kaphat `SSL connection error: Certificate signature check failed` . Ha ez történik, cserélje le a paramétereket a következőre: `--ssl-mode=REQUIRED --ssl-ca={filepath}` `--ssl` .

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>3. lépés: az SSL-kapcsolatok kényszerítése az Azure-ban

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

A Azure Portal használatával keresse fel a Azure Database for MySQL-kiszolgálót, majd kattintson a **kapcsolatbiztonsági** elemre. A váltógomb használatával engedélyezheti vagy letilthatja az **SSL-kapcsolat érvényesítése** beállítást, majd kattintson a **Mentés** gombra. A Microsoft azt javasolja, hogy mindig engedélyezze az **SSL-kapcsolat betartatása** beállítást a fokozott biztonság érdekében.

:::image type="content" source="./media/howto-configure-ssl/enable-ssl.png" alt-text="Képernyőkép az Azure Portalról az SSL-kapcsolatok kikényszerített Azure Database for MySQL":::

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

Az **SSL-kényszerítési** paramétert engedélyezheti vagy letilthatja az Azure CLI-ben engedélyezett vagy letiltott értékek használatával.

```azurecli-interactive
az mysql server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>4. lépés: az SSL-kapcsolat ellenőrzése

Futtassa a MySQL **status** parancsot annak ellenőrzéséhez, hogy az SSL használatával csatlakozott-e a MySQL-kiszolgálóhoz:

```dos
mysql> status
```

Ellenőrizze, hogy a kapcsolat titkosítva van-e a kimenet áttekintésével, amelynek a következőnek kell megjelennie:  **SSL: a használatban lévő REJTJEL AES256-SHA**

## <a name="sample-code"></a>Mintakód

Ha biztonságos kapcsolatot szeretne létesíteni az alkalmazásból az SSL-kapcsolaton keresztül Azure Database for MySQL, tekintse meg az alábbi kódrészleteket:

Tekintse meg a Azure Database for MySQL szolgáltatás által támogatott [kompatibilis illesztőprogramok](concepts-compatibility.md) listáját.

### <a name="php"></a>PHP

```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

### <a name="php-using-pdo"></a>PHP (OEM használatával)

```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'username@mydemoserver', 'yourpassword', $options);
```

### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)

```python
try:
    conn = mysql.connector.connect(user='myadmin@mydemoserver',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)

```python
conn = pymysql.connect(user='myadmin@mydemoserver',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'})
```

### <a name="django-pymysql"></a>Django (PyMySQL)

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin@mydemoserver',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby

```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin@mydemoserver',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :sslca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
    )
```

### <a name="golang"></a>Golang

```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/BaltimoreCyberTrustRoot.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')
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
properties.setProperty("user", 'myadmin@mydemoserver');
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
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)

```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin@mydemoserver",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    SslCa = "BaltimoreCyberTrustRoot.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

## <a name="next-steps"></a>Következő lépések

Tekintse át a különböző alkalmazás-csatlakozási lehetőségeket [a Azure Database for MySQLhoz tartozó kapcsolati kódtárak](concepts-connection-libraries.md) között
