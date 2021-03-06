---
title: 'Azure Policy: Gastkonfigurationserweiterung'
description: Hier erfahren Sie mehr über die Erweiterung, die zum Überprüfen/Konfigurieren von Einstellungen auf virtuellen Computern verwendet wird.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgreenegit
ms.author: migreene
ms.date: 04/15/2021
ms.openlocfilehash: 2fda3cc2cf9adc3a734780209a0c9cc06a04e7cf
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368494"
---
# <a name="overview-of-the-azure-policy-guest-configuration-extension"></a>Übersicht über die Azure Policy-Gastkonfigurationserweiterung

Die Gastkonfigurationserweiterung ist eine Azure Policy-Komponente, die Überprüfungs- und Konfigurationsvorgänge auf virtuellen Computern ausführt.
Richtlinien wie Definitionen von Sicherheitsbaselines für [Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc9b3da7-8347-4380-8e70-0a0361d8dedd) und [Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72650e9f-97bc-4b2a-ab5f-9781a9fcecbc) können Einstellungen auf Computern erst überprüfen, wenn die Erweiterung installiert ist.

## <a name="prerequisites"></a>Voraussetzungen

Damit sich der Computer beim Gastkonfigurationsdienst authentifizieren kann, muss der Computer über eine [vom System zugewiesene verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md) verfügen.
Die Identitätsanforderung auf einem virtuellen Computer ist erfüllt, wenn die folgende Eigenschaft festgelegt ist.

  ```json
  "identity": {
    "type": "SystemAssigned"
  }
  ```

### <a name="operating-systems"></a>Betriebssysteme

Die Unterstützung für die Gastkonfigurationserweiterung ist identisch mit der Betriebssystemunterstützung, die für [die End-to-End-Lösung dokumentiert](../../governance/policy/concepts/guest-configuration.md#supported-client-types) ist.

### <a name="internet-connectivity"></a>Internetkonnektivität

Der von der Gastkonfigurationserweiterung installierte Agent muss Inhaltspakete erreichen können, die unter Gastkonfigurationszuweisungen aufgeführt sind, und den Status an den Gastkonfigurationsdienst melden.
Der Computer kann eine Verbindung über ausgehenden HTTPS-Datenverkehr über TCP-Port 443 herstellen, oder, wenn eine Verbindung über private Netzwerke bereitgestellt wird.
Weitere Informationen zu privaten Netzwerken finden Sie in folgenden Artikeln:

- [Gastkonfiguration: Kommunikation über private Verbindung in Azure](../../governance/policy/concepts/guest-configuration.md#communicate-over-private-link-in-azure)
- [Verwenden privater Endpunkte für Azure Storage](../../storage/common/storage-private-endpoints.md)

## <a name="how-can-i-install-the-extension"></a>Wie kann ich eine Erweiterung installieren?

Um die neueste Version der Erweiterung einschließlich der Identitätsanforderungen in großem Umfang bereitzustellen, weisen Sie die Azure-Richtlinie zu und schaffen Sie die Voraussetzungen, um [Richtlinien für die Gastkonfiguration auf virtuellen Computern zu aktivieren](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json).
Für einzelne Computer kann die Erweiterung mithilfe der Azure CLI, PowerShell, Resource Manager-Vorlagen oder Tools von Drittanbietern bereitgestellt werden.

Der Instanzname der Erweiterung muss auf „AzurePolicyforWindows“ oder „AzurePolicyforLinux“ festgelegt werden, da die oben genannten Richtlinien diese spezifischen Zeichenfolgen erfordern.

Standardmäßig werden alle Bereitstellungen auf die neueste Version aktualisiert. Der Wert der Eigenschaft _autoUpgradeMinorVersion_ ist standardmäßig auf „true“ festgelegt, sofern nichts anderes angegeben ist. Sie müssen sich keine Gedanken über das Aktualisieren Ihres Codes machen, wenn neue Versionen der Erweiterung veröffentlicht werden.

### <a name="azure-cli"></a>Azure CLI

So stellen Sie die Erweiterung für Linux bereit:


```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforLinux --extension-instance-name AzurePolicyforLinux --resource-group myResourceGroup --vm-name myVM
```

So stellen Sie die Erweiterung für Windows bereit:

```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforWindows --extension-instance-name AzurePolicyforWindows --resource-group myResourceGroup --vm-name myVM
```

### <a name="powershell"></a>PowerShell

So stellen Sie die Erweiterung für Linux bereit:

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforLinux' -Name 'AzurePolicyforLinux' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM'
```

So stellen Sie die Erweiterung für Windows bereit:

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforWindows' -Name 'AzurePolicyforWindows' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM'
```

### <a name="resource-manager-template"></a>Resource Manager-Vorlage

So stellen Sie die Erweiterung für Linux bereit:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforLinux')]",
  "apiVersion": "2019-07-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforLinux",
    "typeHandlerVersion": "1.0"
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

So stellen Sie die Erweiterung für Windows bereit:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforWindows')]",
  "apiVersion": "2019-07-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforWindows",
    "typeHandlerVersion": "1.0"
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

### <a name="terraform"></a>Terraform

So stellen Sie die Erweiterung für Linux bereit:

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                  = "AzurePolicyforLinux"
  virtual_machine_id    = "myVMID"
  publisher             = "Microsoft.GuestConfiguration"
  type                  = "ConfigurationforLinux"
  type_handler_version  = "1.0"
}
```

So stellen Sie die Erweiterung für Windows bereit:

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                  = "AzurePolicyforWindows"
  virtual_machine_id    = "myVMID"
  publisher             = "Microsoft.GuestConfiguration"
  type                  = "ConfigurationforWindows"
  type_handler_version  = "1.0"
}
```

## <a name="settings"></a>Einstellungen

Es ist nicht erforderlich, Einstellungen oder Eigenschaften mit geschützten Einstellungen in die Erweiterung aufzunehmen.
Alle diese Informationen werden vom Agent aus Ressourcen für die [Gastkonfigurationszuweisung](/rest/api/guestconfiguration/guestconfigurationassignments) abgerufen. Beispielsweise werden die Eigenschaften [ConfigurationUri](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#guestconfigurationnavigation), [Mode](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationmode) und [ConfigurationSetting](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationsetting) pro Konfiguration und nicht in der VM-Erweiterung verwaltet.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur Azure Policy-Gastkonfiguration finden Sie in den [Informationen zur Gastkonfiguration von Azure Policy](../../governance/policy/concepts/guest-configuration.md).
* Weitere Informationen zur Funktionsweise des Linux-Agents und der Erweiterungen finden Sie unter [Informationen zu Erweiterungen und Features für virtuelle Computer für Linux](features-linux.md).
* Weitere Informationen zur Funktionsweise des Windows-Gast-Agents und der Erweiterungen finden Sie unter [Azure VM-Erweiterungen und -Features für Windows](features-windows.md).  
* Informationen zur Installation des Windows-Gast-Agents finden Sie unter [Azure Virtual Machine Agent overview](agent-windows.md) (Übersicht über den Agent für virtuelle Azure-Computer).  
* Informationen zur Installation des Linux-Agents finden Sie unter [Understanding and using the Azure Linux Agent](agent-linux.md) (Grundlegendes zum Azure Linux-Agent und seiner Verwendung).  
