---
title: 'Erstellen einer Funktions-App mit Azure Cosmos DB: Azure CLI'
description: 'Azure CLI-Skriptbeispiel: Erstellen einer Azure-Funktion, die eine Verbindung mit Azure Cosmos DB herstellt'
ms.topic: sample
ms.date: 07/03/2018
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 261ede1493b93ccd3cfed19125bbabc3be871698
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786287"
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Erstellen einer Azure-Funktion, die eine Verbindung mit Azure Cosmos DB herstellt

Dieses Azure Functions-Beispielskript erstellt eine Funktions-App und stellt eine Verbindung zwischen der Funktion und einer Azure Cosmos DB-Datenbank her. Die erstellte App-Einstellung mit der Verbindung kann mit einem [Azure Cosmos DB-Trigger oder einer Azure Cosmos DB-Bindung](../functions-bindings-cosmosdb.md) verwendet werden.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Für dieses Tutorial ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert. 

## <a name="sample-script"></a>Beispielskript

In diesem Beispiel werden eine Azure-Funktionen-App erstellt und ein Cosmos DB-Endpunkt sowie ein Zugriffsschlüssel zu den App-Einstellungen hinzugefügt.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Create an Azure Function that connects to an Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle: Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Erstellen Sie eine Ressourcengruppe mit einem Speicherort. |
| [az storage accounts create](/cli/azure/storage/account#az_storage_account_create) | Speicherkonto erstellen |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Erstellt eine Funktions-App im serverlosen [Verbrauchsplan](../consumption-plan.md). |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | Erstellen Sie eine Azure Cosmos DB-Datenbank. |
| [az cosmosdb show](/cli/azure/cosmosdb#az_cosmosdb_show)| Ruft die Verbindung des Datenbankkontos ab. |
| [az cosmosdb list-keys](/cli/azure/cosmosdb#az_cosmosdb_list_keys)| Ruft die Schlüssel für die Datenbank ab. |
| [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set) | Legt die Verbindungszeichenfolge als App-Einstellung in der Funktions-App fest. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Weitere Azure Functions-CLI-Skriptbeispiele finden Sie in der [Dokumentation zu Azure Functions](../functions-cli-samples.md).
