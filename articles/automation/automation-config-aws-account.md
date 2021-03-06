---
title: Configurar a autenticação com os Amazon Web Services
description: Este artigo descreve como criar e validar uma credencial AWS para runbooks na Automatização do Azure gerindo recursos AWS.
keywords: autenticação aws, configurar aws
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 93e2a95e8b1ed2cf39b6e839908be54815f8fa63
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2018
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>Autenticar Runbooks com Amazon Web Services
Automatizar tarefas comuns com recursos nos Amazon Web Services (AWS) pode ser efetuado com runbooks de Automatização no Azure.  Pode automatizar muitas tarefas no AWS através de runbooks de Automatização, tal como o pode fazer com recursos no Azure.  Apenas são necessárias duas coisas:

* Uma subscrição do AWS e um conjunto de credenciais.  Mais especificamente, a Chave de Acesso do AWS e a Chave Secreta.  Para obter mais informações, veja o artigo [Using AWS Credentials (Com Credenciais AWS)](http://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).
* Uma subscrição do Azure e a conta de Automatização.  Para obter mais informações sobre como configurar uma conta de automatização do Azure, veja [Authentication planning (Planeamento de autenticação)](automation-offering-get-started.md#authentication-planning).  

Para efetuar a autenticação com o AWS, tem de especificar um conjunto de credenciais do AWS para autenticar os runbooks a partir da Automatização do Azure. Se já tiver uma conta de Automatização criada e pretende utilizá-la para efetuar a autenticação com o AWS, pode seguir os passos na secção seguinte.  Se pretender dedicar uma conta para runbooks direcionados para recursos do AWS, deve primeiro criar uma nova [Conta de Automatização](automation-offering-get-started.md) (ignore a opção para criar um principal de serviço) e, em seguida, siga os passos abaixo.

## <a name="configure-automation-account"></a>Configurar conta de Automatização
Para a Automatização do Azure comunicar com o AWS, primeiro tem de obter as suas credenciais do AWS e armazená-las como recursos na Automatização do Azure.  Execute os seguintes passos documentados no documento AWS [Gerir Chaves de Acesso para a sua Conta AWS](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) para criar uma Chave de Acesso e copiar o **ID da Chave de Acesso** e **Chave de Acesso Secreta** (opcionalmente, transfira o ficheiro de chave para o armazenar num local seguro).

Depois de ter criado e copiado as chaves de segurança AWS, tem de criar um recurso de Credencial com uma conta de Automatização do Azure para as armazenar em segurança as e referenciar com os seus runbooks.  Siga os passos na secção **Para criar uma nova credencial** nos [Recursos de credencial na Automatização do Azure](automation-credentials.md#to-create-a-new-credential-asset-with-the-azure-portal) e introduza as seguintes informações:

1. Na caixa **Nome**, introduza **AWScred** ou um valor adequado a seguir às normas de nomenclatura.  
2. Na caixa **Nome de utilizador**, escreva o **ID de Acesso** e a **Chave de Acesso Secreta** na caixa **Palavra-passe** e **Confirmar palavra-passe**.   

## <a name="next-steps"></a>Passos Seguintes
* Leia o artigo de solução [Automating deployment of a VM in Amazon Web Services (Automatizar a implementação de uma VM nos Amazon Web Services)](automation-scenario-aws-deployment.md) para saber como criar runbooks para automatizar as tarefas no AWS.

