---
title: O que é o Azure SQL Data Warehouse? | Microsoft Docs
description: Base de dados distribuída de nível empresarial capaz de processar volumes de petabytes de dados relacionais e não relacionais. É o primeiro armazém de dados em nuvem da indústria que pode aumentar, diminuir e ser colocado em pausa em segundos.
services: sql-data-warehouse
author: igorstanko
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: overview
ms.component: design
ms.date: 04/17/2018
ms.author: igorstan
ms.reviewer: igorstan
ms.openlocfilehash: c8e6af4eabc96845ff6c237735cdb0da2ed24743
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2018
---
# <a name="what-is-azure-sql-data-warehouse"></a>O que é o Azure SQL Data Warehouse?

O SQL Data Warehouse é um Armazém de Dados Empresarial (EDW) baseado na cloud que tira partido do Processamento Paralelo em Massa (MPP) para executar rapidamente consultas complexas em petabytes de dados. Utilize o SQL Data Warehouse como um componente fundamental de uma solução de macrodados. Importe macrodados para o SQL Data Warehouse com consultas simples de PolyBase T-SQL e, em seguida, utilize o poder do MPP para executar análises de elevado desempenho. À medida que integra e analisa, o armazém de dados tornar-se-á a única versão real com que a sua empresa pode contar para obter informações.  


## <a name="key-component-of-big-data-solution"></a>Componente fundamental da solução de macrodados
O SQL Data Warehouse é um componente fundamental de uma solução de macrodados ponto a ponto na cloud.

![Solução de armazém de dados](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

Numa solução de dados da cloud, os dados são ingeridos em arquivos de macrodados a partir de várias origens. Uma vez num arquivo de macrodados, os algoritmos do Hadoop, do Spark e de machine learning preparam os dados. Quando os dados estão prontos para análises complexas, o SQL Data Warehouse utiliza o PolyBase para consultar os arquivos de macrodados. O PolyBase utiliza consultas de T-SQL padrão para colocar os dados no SQL Data Warehouse.
 
O SQL Data Warehouse armazena os dados em tabelas relacionais através do armazenamento em colunas. Este formato reduz significativamente os custos de armazenamento de dados e melhora o desempenho das consultas. Depois de os dados serem armazenados no SQL Data Warehouse, pode executar análises em grande escala. Em comparação com os sistemas de bases de dados tradicionais, as consultas de análise terminam em segundos em vez de minutos ou horas em vez de dias. 

Os resultados das análises podem ser enviados para aplicações ou bases de dados de relatórios em todo o mundo. Em seguida, os analistas empresariais podem obter informações para tomar decisões mais adequadas.


## <a name="next-steps"></a>Passos seguintes
Agora que já sabe um pouco sobre o SQL Data Warehouse, saiba como [criar um SQL Data Warehouse][create a SQL Data Warehouse] e [carregar dados de exemplo][load sample data] rapidamente. Se não estiver familiarizado com o Azure, poderá achar útil o [Glossário do Azure][Azure glossary] quando se deparar com terminologia nova. Em alternativa, veja alguns destes outros Recursos do SQL Data Warehouse.  

* [Histórias de sucesso de clientes]
* [Blogues]
* [Pedidos de funcionalidades]
* [Vídeos]
* [Blogues da Equipa Customer Advisory]
* [Criar pedido de suporte]
* [Fórum do MSDN]
* [Fórum do Stack Overflow]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Criar pedido de suporte]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Histórias de sucesso de clientes]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Blogues]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogues da Equipa Customer Advisory]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Pedidos de funcionalidades]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum do MSDN]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Fórum do Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
