---
title: Verwalten von Azure Data Lake Analytics mithilfe des Azure SDK für Node.js
description: In diesem Artikel erfahren Sie, wie Sie das Azure SDK für Node.js zum Verwalten von Data Lake Analytics-Konten, -Datenquellen, -Aufträgen und -Benutzern verwenden.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 12/05/2016
ms.custom: devx-track-js
ms.openlocfilehash: ca1c865b31739e7163b0db26cb666a8a42edbaa2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92218458"
---
# <a name="manage-azure-data-lake-analytics-using-azure-sdk-for-nodejs"></a>Verwalten von Azure Data Lake Analytics mithilfe des Azure SDK für Node.js
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

In diesem Artikel erfahren Sie, wie Sie Azure Data Lake Analytics-Konten, -Datenquellen, -Benutzer und -Aufträge mit einer App verwalten, die mithilfe des Azure SDK für Node.js geschrieben wurde. 

Die folgenden Versionen werden unterstützt:
* **Node.js-Version: 0.10.0 oder höher**
* **REST-API-Version für Konten: 2015-10-01-preview**
* **REST-API-Version für Kataloge: 2015-10-01-preview**
* **REST-API-Version für Aufträge: 2016-03-20-preview**

## <a name="features"></a>Features
* Kontoverwaltung: Erstellen, Abrufen, Auflisten, Aktualisieren und Löschen
* Auftragsverwaltung: Senden, Abrufen, Auflisten und Abbrechen
* Katalogverwaltung: Abrufen und Auflisten

## <a name="how-to-install"></a>Installation
```bash
npm install azure-arm-datalake-analytics
```

## <a name="authenticate-using-azure-active-directory"></a>Authentifizieren mit Azure Active Directory
 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-analytics-client"></a>Erstellen des Data Lake Analytics-Clients
```javascript
var adlaManagement = require("azure-arm-datalake-analytics");
var accountClient = new adlaManagement.DataLakeAnalyticsAccountClient(credentials, 'your-subscription-id');
var jobClient = new adlaManagement.DataLakeAnalyticsJobClient(credentials, 'azuredatalakeanalytics.net');
var catalogClient = new adlaManagement.DataLakeAnalyticsCatalogClient(credentials, 'azuredatalakeanalytics.net');
```

## <a name="create-a-data-lake-analytics-account"></a>Erstellen eines Data Lake Analytics-Kontos
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

## <a name="get-a-list-of-jobs"></a>Abrufen einer Liste von Aufträgen
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

## <a name="get-a-list-of-databases-in-the-data-lake-analytics-catalog"></a>Abrufen einer Liste von Datenbanken im Data Lake Analytics-Katalog
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

## <a name="see-also"></a>Siehe auch
* [Microsoft Azure SDK für Node.js](https://github.com/azure/azure-sdk-for-node)
