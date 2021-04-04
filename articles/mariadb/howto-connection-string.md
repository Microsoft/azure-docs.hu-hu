---
title: Kapcsolatok karakterláncai – Azure Database for MariaDB
description: Ez a dokumentum felsorolja a jelenleg támogatott kapcsolati karakterláncokat, amelyekkel az alkalmazások csatlakozhatnak a Azure Database for MariaDBhoz, beleértve a ADO.NET (C#), a JDBC, a Node.js, az ODBC, a PHP, a Python és a Ruby alkalmazást.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 8119f0bfd1f9007cab0df93ad2c7ef22b4db2199
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662177"
---
# <a name="how-to-connect-applications-to-azure-database-for-mariadb"></a>Alkalmazások csatlakoztatása az Azure Database for MariaDB-hez
Ez a témakör felsorolja a Azure Database for MariaDB által támogatott, a sablonokkal és példákkal együtt támogatott kapcsolatok karakterlánc-típusokat. Előfordulhat, hogy a kapcsolatok karakterláncában különböző paraméterek és beállítások vannak.

- A tanúsítvány beszerzéséhez lásd: [az SSL konfigurálása](./howto-configure-ssl.md).
- {your_host} = [servername]. MariaDB. database. Azure. com
- {your_user} @ {servername} = userID-formátum a hitelesítéshez.  Ha csak a felhasználóazonosító-t használja, a hitelesítés sikertelen lesz.

## <a name="adonet"></a>ADO.NET
```csharp
Server={your_host}; Port=3306; Database={your_database}; Uid={username@servername}; Pwd={your_password}; SslMode=Preferred;
```

Ebben a példában a kiszolgáló neve `mydemoserver` , az adatbázis neve `wpdb` , a Felhasználónév `WPAdmin` és a jelszó `mypassword!2` . Ennek eredményeképpen a kapcsolatok karakterláncának a következőképpen kell szerepelnie:

```csharp
Server= "mydemoserver.mariadb.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```java
String url ="jdbc:mariadb://{your_host}:3306/{your_database}?useSSL=true&trustServerCertificate=true"; myDbConn = DriverManager.getConnection(url, "{username@servername}", {your_password});
```

## <a name="nodejs"></a>Node.js
```javascript
var conn = mysql.createConnection({host: "{your_host}", user: "{your_username}", password: {your_password}, database: {your_database}, port: 3306, ssl:{ca:fs.readFileSync({ca-cert filename})}});
```

## <a name="odbc"></a>ODBC
```cpp
DRIVER={MARIADB ODBC 3.0 Driver}; Server="{your_host}"; Port=3306; Database={your_database}; Uid="{username@servername}"; Pwd={your_password}; sslca={ca-cert filename}; sslverify=1;
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "{your_host}", "{username@servername}", {your_password}, {your_database}, 3306);
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user="{username@servername}", password={your_password}, host="{your_host}", port=3306, database={your_database}, ssl_ca={ca-cert filename}, ssl_verify_cert=true)
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: "{username@servername}", password: {your_password}, database: {your_database}, host: "{your_host}", port: 3306, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA')
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>A kapcsolati sztring részleteinek beolvasása a Azure Portal
A [Azure Portal](https://portal.azure.com)nyissa meg a Azure Database for MariaDB-kiszolgálót, majd kattintson a **kapcsolódási karakterláncok** lehetőségre a példányhoz tartozó karakterlánc-lista lekéréséhez: ![ a kapcsolódási karakterláncok panel a Azure Portal](./media/howto-connection-strings/connection-strings-on-portal.png)

A karakterlánc olyan adatokat tartalmaz, mint például az illesztőprogram, a kiszolgáló és más adatbázis-kapcsolati paraméterek. Módosítsa ezeket a példákat saját paraméterek használatára, például az adatbázis nevére, jelszavára stb. Ezt a karakterláncot használhatja a kód és az alkalmazások kiszolgálóhoz való kapcsolódáshoz.

<!-- 
## Next steps
- For more information about connection libraries, see [Concepts - Connection libraries](./concepts-connection-libraries.md).
- -->
