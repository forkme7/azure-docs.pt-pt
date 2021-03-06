---
title: Comparação de Azure Data Lake Store com o Blob de armazenamento do Azure | Microsoft Docs
description: Comparação de Azure Data Lake Store com o Blob de armazenamento do Azure
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: b199525b-84de-4f79-9eb6-69a613b8b217
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: d394c856b2f27446ab28c44fe4fed2dfd59ae62f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2018
---
# <a name="comparing-azure-data-lake-store-and-azure-blob-storage"></a>Comparar o Azure Data Lake Store e o armazenamento de Blobs do Azure
A tabela neste artigo resume as diferenças entre o Azure Data Lake Store e o armazenamento de Blobs do Azure ao longo de alguns aspetos fundamentais grande de processamento de dados. Armazenamento de Blobs do Azure é um objetivo geral, o arquivo de objeto dimensionável, que foi concebido para uma grande variedade de cenários de armazenamento. O Azure Data Lake Store é um repositório de hiper escala que está otimizado para cargas de trabalho de análise de macrodados.

|  | Arquivo do Azure Data Lake | Armazenamento de Blobs do Azure |
| --- | --- | --- |
| Finalidade |Armazenamento otimizado para cargas de trabalho de análise de macrodados |Arquivo de objeto de objetivo geral para uma grande variedade de cenários de armazenamento, incluindo a análise de macrodados |
| Casos de utilização |O batch, interativa, transmissão em fluxo de dados do machine learning e de análise, tais como dados de IoT ficheiros de registo, clique em fluxos de grandes conjuntos de dados |Qualquer tipo de dados de texto ou binários, tais como aplicações de back-end, dados de cópia de segurança, armazenamento de suporte de dados para dados de objetivo de transmissão em fluxo e geral. Suporte para cargas de trabalho de análise; além disso, inteiro o batch, interativa, transmissão em fluxo de dados do machine learning e de análise, tais como dados de IoT ficheiros de registo, clique em fluxos de grandes conjuntos de dados |
| Conceitos-chave |Conta do Data Lake Store contém pastas, que por sua vez, contém dados armazenados como ficheiros |Conta de armazenamento tem contentores, que por sua vez tem dados sob a forma de blobs |
| estrutura |Sistema de ficheiros hierárquico |Arquivo de objeto com o espaço de nomes simples |
| API |API de REST através de HTTPS |API de REST através de HTTP/HTTPS |
| API do lado do servidor |[API REST WebHDFS compatível](https://msdn.microsoft.com/library/azure/mt693424.aspx) |[API de REST do armazenamento de Blobs do Azure](https://msdn.microsoft.com/library/azure/dd135733.aspx) |
| Cliente de sistema de ficheiros de Hadoop |Sim |Sim |
| Operações de dados - autenticação |Com base no [identidades do Azure Active Directory](../active-directory/active-directory-authentication-scenarios.md) |Com base nos segredos partilhados - [chaves de conta de acesso](../storage/common/storage-create-storage-account.md#manage-your-storage-account) e [as chaves de assinatura de acesso partilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md). |
| Operações de dados - protocolo de autenticação |OAuth 2.0. Chamadas tem de conter um JWT válido (JSON Web Token) emitido pelo Azure Active Directory |Código de autenticação de mensagens com base em hash (HMAC). Chamadas tem de conter um hash SHA-256 com codificação Base64 através de uma parte do pedido de HTTP. |
| Operações de dados - autorização |Listas de controlo de acesso POSIX (ACLs).  As ACLs baseadas no Azure Active Directory identidades podem ser definidas ao nível do ficheiro e pasta. |Para autorização de nível de conta – utilize [chaves de conta de acesso](../storage/common/storage-create-storage-account.md#manage-your-storage-account)<br>Para a conta, contentor ou uma autorização de blob - utilizar [as chaves de assinatura de acesso partilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md) |
| Operações de dados - auditoria |Está disponível. Consulte [aqui](data-lake-store-diagnostic-logs.md) para obter informações. |Disponível |
| Dados de encriptação de Inativos |<ul><li>Transparente, lado do servidor</li> <ul><li>Com o serviço gerido chaves</li><li>Com chaves gerida pelo cliente no Azure KeyVault</li></ul></ul> |<ul><li>Transparente, lado do servidor</li> <ul><li>Com o serviço gerido chaves</li><li>Com chaves gerida pelo cliente no KeyVault do Azure (pré-visualização)</li></ul><li>Encriptação do lado do cliente</li></ul> |
| Operações de gestão (por exemplo, criar conta) |[Controlo de acesso baseado em funções](../role-based-access-control/overview.md) (RBAC) fornecida pelo Azure para gestão de contas |[Controlo de acesso baseado em funções](../role-based-access-control/overview.md) (RBAC) fornecida pelo Azure para gestão de contas |
| SDKs de programador |.NET, Java, Python, Node.js |.NET, Java, Python, Node.js, C++, Ruby, PHP, aceda, Android, iOS |
| Desempenho da carga de trabalho de análise |Desempenho otimizado para cargas de trabalho de análise paralela. Débito alto e IOPS. |Desempenho otimizado para cargas de trabalho de análise paralela. |
| Limites de tamanho |Não existem limites de tamanhos de conta, tamanhos de ficheiro ou o número de ficheiros |Os limites específicos documentados [aqui](../storage/common/storage-scalability-targets.md). Conta maior limita disponíveis contactando [suporte do Azure](https://azure.microsoft.com/support/faq/) |
| Redundância geográfica |Redundante localmente (várias cópias dos dados de uma região do Azure) |Localmente redundantes (LRS), de zona (ZRS) redundante, globalmente redundante (GRS), acesso de leitura globalmente redundante (RA-GRS). Consulte [aqui](../storage/common/storage-redundancy.md) para obter mais informações |
| Estado do serviço |Geralmente disponível |Geralmente disponível |
| Disponibilidade regional |Consulte [aqui](https://azure.microsoft.com/regions/#services) |Disponível em todas as regiões do Azure |
| Preço |Consulte [preços](https://azure.microsoft.com/pricing/details/data-lake-store/) |Consulte [preços](https://azure.microsoft.com/pricing/details/storage/) |


