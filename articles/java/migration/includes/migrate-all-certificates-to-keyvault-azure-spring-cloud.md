---
author: yevster
ms.author: yebronsh
ms.date: 7/17/2020
ms.openlocfilehash: 1e7957a03cb96254a0318774a1620c7143ae6fc4
ms.sourcegitcommit: 4036ac08edd7fc6edf8d11527444061b0e4531ef
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/28/2020
ms.locfileid: "89062053"
---
### <a name="migrate-all-certificates-to-keyvault"></a>Migrieren aller Zertifikate zu Key Vault

Azure Spring Cloud bietet keinen Zugriff auf den JRE-Keystore. Daher müssen Sie Zertifikate zu Azure Key Vault migrieren und den Anwendungscode ändern, um auf Zertifikate in Key Vault zuzugreifen. Weitere Informationen finden Sie unter [Erste Schritte mit Key Vault-Zertifikaten](/azure/key-vault/certificates/certificate-scenarios) sowie unter [Azure Key Vault-Zertifikat: Clientbibliothek für Java (Version 4.0.3)](/java/api/overview/azure/security-keyvault-certificates-readme).