---
title: Configure os parâmetros de serviço na base de dados do Azure para PostgreSQL
description: Este artigo descreve como configurar os parâmetros de serviço na base de dados do Azure para PostgreSQL utilizando a linha de comandos da CLI do Azure.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: df32578c92b1973568c9b811cb7f9e9db067915f
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2018
---
# <a name="customize-server-configuration-parameters-using-azure-cli"></a>Personalizar parâmetros de configuração de servidor utilizando a CLI do Azure
Pode listar, mostrar e atualizar os parâmetros de configuração para um servidor de PostgreSQL do Azure utilizando a Interface de linha de comandos (CLI do Azure). Um subconjunto das configurações de motor está exposto ao nível do servidor e pode ser modificado. 

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de procedimentos, tem de:
- Criar base de dados e uma base de dados do Azure para o servidor de PostgreSQL seguindo [criar uma base de dados do Azure para PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Instalar [Azure CLI 2.0](/cli/azure/install-azure-cli) interface de linha de comandos no seu computador ou utilize o [Shell de nuvem do Azure](../cloud-shell/overview.md) no portal do Azure com o seu browser.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Lista os parâmetros de configuração do servidor da base de dados do Azure para o servidor de PostgreSQL
Para listar todos os parâmetros modificável no servidor e os respetivos valores, execute o [lista de configuração de servidor de postgres az](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_list) comando.

Pode listar os parâmetros de configuração de servidor para o servidor **mydemoserver.postgres.database.azure.com** no grupo de recursos **myresourcegroup**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>Mostrar detalhes de parâmetro de configuração do servidor
Para mostrar os detalhes sobre um parâmetro de configuração específica para um servidor, execute o [mostrar de configuração de servidor de postgres az](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_show) comando.

Este exemplo mostra detalhes sobre o **registo\_min\_mensagens** parâmetro de configuração de servidor para servidor **mydemoserver.postgres.database.azure.com** no grupo de recursos **myresourcegroup.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>Modificar o valor de parâmetro de configuração do servidor
Também pode modificar o valor de um determinado servidor parâmetro de configuração, que atualiza o valor de configuração subjacente para o motor do PostgreSQL. Para atualizar a configuração, utilize o [az postgres server configuration set](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_set) comando. 

Para atualizar o **registo\_min\_mensagens** parâmetro de configuração do servidor do server **mydemoserver.postgres.database.azure.com** no grupo de recursos  **myresourcegroup.**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver --value INFO
```
Se pretender repor o valor de um parâmetro de configuração, basta optar por inclua o opcional `--value` parâmetro e o serviço aplica-se o valor predefinido. No acima exemplo, este seria aspeto:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
Este comando repõe o **registo\_min\_mensagens** configuração para o valor predefinido **aviso**. Para obter mais informações sobre a configuração do servidor e os valores permitidos, consulte a documentação de PostgreSQL no [configuração do servidor](https://www.postgresql.org/docs/9.6/static/runtime-config.html).

## <a name="next-steps"></a>Passos Seguintes
- Para configurar e os registos do servidor de acesso, consulte [registos do servidor na base de dados do Azure para PostgreSQL](concepts-server-logs.md)
