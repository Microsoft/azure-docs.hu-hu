---
title: Az Azure Data Lake Analytics kezelése az Azure SDK for Node.js használatával
description: Ez a cikk azt ismerteti, hogyan használhatja az Azure SDK for Node.js szolgáltatást a Data Lake Analytics-fiókok, -adatforrások, -feladatok és -felhasználók kezeléséhez.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 9de1bcf4-b15b-4d0b-9284-8889ecf0c438
ms.topic: how-to
ms.date: 12/05/2016
ms.openlocfilehash: f821f8f8fddfafbdba060fd3f9940ce32c138755
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121350"
---
# <a name="manage-azure-data-lake-analytics-using-azure-sdk-for-nodejs"></a>Az Azure Data Lake Analytics kezelése az Azure SDK for Node.js használatával
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Ebből a cikkből megtudhatja, hogyan kezelheti az Azure Data Lake Analytics-fiókokat, -adatforrásokat, -feladatokat és -felhasználókat egy, az Azure SDK for Node.js segítségével írt alkalmazás használatával. 

Az alábbi verziók támogatottak:
* **Node.js-verzió: 0.10.0-s vagy újabb**
* **Fiókhoz tartozó REST API-verzió: 2015. 10. 01. előzetes verzió**
* **Katalógushoz tartozó REST API-verzió: 2015. 10. 01. előzetes verzió**
* **Feladathoz tartozó REST API-verzió: 2016. 03. 20. előzetes verzió**

## <a name="features"></a>Szolgáltatások
* Fiókkezelés: létrehozás, lekérés, felsorolás, frissítés és törlés.
* Feladatkezelés: küldés, lekérés, listázás és visszavonás.
* Katalóguskezelés: lekérés és listázás.

## <a name="how-to-install"></a>A telepítés módja
```bash
npm install azure-arm-datalake-analytics
```

## <a name="authenticate-using-azure-active-directory"></a>Hitelesítés az Azure Active Directory használatával
 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-analytics-client"></a>Data Lake Analytics-ügyfél létrehozása
```javascript
var adlaManagement = require("azure-arm-datalake-analytics");
var accountClient = new adlaManagement.DataLakeAnalyticsAccountClient(credentials, 'your-subscription-id');
var jobClient = new adlaManagement.DataLakeAnalyticsJobClient(credentials, 'azuredatalakeanalytics.net');
var catalogClient = new adlaManagement.DataLakeAnalyticsCatalogClient(credentials, 'azuredatalakeanalytics.net');
```

## <a name="create-a-data-lake-analytics-account"></a>Data Lake Analytics-fiók létrehozása
```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlaacct';
var location = 'eastus2';

// A Data Lake Store account must already have been created to create
// a Data Lake Analytics account. See the Data Lake Store readme for
// information on doing so. For now, we assume one exists already.
var datalakeStoreAccountName = 'existingadlsaccount';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location,
  properties: {
    defaultDataLakeStoreAccount: datalakeStoreAccountName,
    dataLakeStoreAccounts: [
      {
        name: datalakeStoreAccountName
      }
    ]
  }
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="get-a-list-of-jobs"></a>A feladatok listájának lekérése
```javascript
var util = require('util');
var accountName = 'testadlaacct';
jobClient.job.list(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="get-a-list-of-databases-in-the-data-lake-analytics-catalog"></a>A Data Lake Analytics-katalógus adatbázislistáinak lekérése
```javascript
var util = require('util');
var accountName = 'testadlaacct';
catalogClient.catalog.listDatabases(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>Lásd még
* [Microsoft Azure SDK for Node.js](https://github.com/azure/azure-sdk-for-node)
