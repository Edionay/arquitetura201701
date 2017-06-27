# Arquitetura SISB - Sistema de Informação em Saúde Bucal

## Apresentação
Este documento tem como finalidade apresentar soluções de arquitetura para o desenvolvimento e implementação do SISB - Sistema de Informação em Saúde Bucal.

## SISB
O SISB é um sistema de saúde voltado para a área odontológica com a finalidade de gerenciar agendamento de consultas, recursos humanos, informações de saúde e estoque relacionado à área de atuação.

### Descrição do Cenário
Para um melhor entendimento do problema a ser solucionado pelo SISB, foi necessário mapear alguns pontos pertinentes à dimensão que o sistema estima alcançar.

#### Distribuição Geográfica
Software a nível nacional (Brasil), apto a atender requisições de 10, 50, 100, 1.000 e 10.000 odontólogos e armazenar dados para 5000, 100.000 e 10.000.000 de pacientes.

#### Hardware Mínimo para Cliente
Hardware cliente desktop:
- Processador Intel ou compatível (4 núcleos); 
- RAM: 8GB DDR4 ou compatível; 
- Disco: SATA 2TB; 
- Placa de Rede PCI de 100 Mbps.
Hardware iOS: iPhone 5s (Armazenamento Interno: 16GB) ou superior
Hardware Android: 
- Processador Quad-core 1,2 GHz Cortex-A7
- RAM: 1GB
- Tela: 1280 x 720 pixels (16:9)
- Armazenamento Interno: 8GB

#### Número de Camadas de Hardware
- Camada cliente  
  - iOs  
  - Android  
  - Navegador Web  

- Camada servidor de aplicação  
  - Servidor em nuvem   
- Camada de dados externos   
  - Barramento SUS   
- Definição da Plataforma    
  - Navegadores web (últimas duas versões dos navegadores Google Chrome, Mozilla Firefox, Edge e Safari)    
  - Servidor de aplicação    
  - Execução em servidor baseado em nuvem   
  - TomCat com Apache  
  - Clientes Android: Versão 4.1 ou superior.    
  - Clientes iOs: Versão 10.0 ou superior.    
- Localização dos Processos de Dados     
  - Cliente     
   - Envia requisições e dados para o servidor  
    - Recebe e formata dados processados  
  - Servidor  
   - Recebe e processa requisições e dados enviados pelos clientes  
   - Acessa dados do barramento SUS  
   - Envia o resultado do processamento para o cliente  

## Requisitos de qualidade
De acordo com [o documento de especificação de requisitos](https://github.com/kyriosdata/sisb/blob/master/info/Requisitos.md), a arquitetura proposta deverá atender aos requisitos de qualidade pertinentes às seguintes áreas:

### Portabilidade
- O SISB deve tanto fazer uso quanto produzir artefatos livres (sem pagamento de royalties)
- O SISB permite a importação/exportação de dados geridos por ele com base em arquétipos a serem disponibilizados publicamente. 

### Manutenibilidade
- O SISB usufrui de serviços oferecidos por "nuvens" públicas.
- O SISB deve oferecer mecanismos para facilitar o seu uso por mais de uma língua ou cultura. Especificamente: estar preparado para internacionalização e localização
- O cycle time do SISB deve ser de, no máximo, 2 horas.

### Desempenho
- O SISB oferece suas funcionalidades e atender requisitos de qualidade usando hardware "convencional" (commodity hardware). A configuração de entrada são: (a) CPU: processador Intel ou compatível (4 núcleos); (b) RAM: 8GB DDR4 ou compatível; (c) Disco: SATA 2TB; (d) Placa de Rede PCI de 100 Mpbs.
- Execução pode ser em Windows, Linux ou MacOS.
- A execução pode ser feita utilizando os navegadores web: google chrome, mozilla firefox, safari e edge.
- Não poderá ficar indisponível por no máximo 2 horas por dia.
- O SISB deve ser capaz de se manter em operação por um período ininterrupto de 30 dias.
- O SISB deve estar apto a atender requisições de 10, 50, 100, 1.000 e 10.000 odontólogos.
- O SISB deve estar apto para armazenar dados para 5000, 100.000 e 10.000.000 de pacientes.

### Confiabilidade
- O SISB deve estar em conformidade com o [Manual de Certificação da SBIS (NGS2)](http://sbis.org.br/certificacao/Manual_Certificacao_SBIS-CFM_2016_v4-2.pdf). - A interação de clientes (interface com o usuário) com o SISB e os serviços oferecidos serão projetados em conformidade com as diretrizes contidas no [API Design Guid](https://cloud.google.com/apis/design/).
- O SISB estar em conformidade com os padrões adotado pelo Brasil para interoperabilidade semântica
- O SISB interage com os serviços do barramento do SUS: CNS, CNES, SIGTAP, e-SUS AB e Horus.

### Segurança
- Backup diário, em um período de no máximo 4 horas.
- Manutenção corretiva deve ser feita em no máximo 2 dias.

# Disposição do sistema
![Conceito do sistema](https://github.com/Edionay/arquitetura201701/blob/master/Diagramas/Conceito%20do%20Sistema.png)

O SISB terá módulos que atendem demandas a usuários do tipo administrador do sistema, odontólogo e recepcionista (lado esquerdo da figura acima). Apesar de não estar presente na figura, usuários do tipo paciente serão gerenciados e notificados pelo sistema, por meios externos, de assuntos de seu interesse . Além do desenvolvimento de suas funções nativas, o SISB fará uso e integração com serviços externos (lado direito da figura acima). A tabela abaixo descreve os papéis mencionados na figura acima.

Uso | Descrição
------------ | -------------
Odontólogo | Poderá verificar e gerar informação de pacientes
Administrador | Responsável pela manutenção do SISB e também pelo restabelecimento de seu funcionamento em caso de falha. Ainda possui a função de cadastrar novos usuários e seus papéis
Recepcionista | Poderá realizar agendamento de consultas além de realizar atividades pertinentes ao financeiro entre outros
Barramento SUS | Barramento com o qual o SISB deverá interagir para receber e enviar informações
NFe | Deverá emitir a nota fiscal eletrônica
AWS S3 | Serviço no qual o SISB será instalado e responsável pelo backup e processamento de dados, os quais também serão realizados e armazenados na nuvem
Bancos | Deverá realizar os pagamentos e controle de caixa
Operadoras de Cartão | Deverá receber pagamentos de serviços realizados via cartões de crédito ou débito


# Arquitetura proposta
## Visão geral em camadas
![Camadas](https://github.com/Edionay/arquitetura201701/blob/master/Diagramas/VIs%C3%A3o%20geral%20em%20camadas.png?raw=true)

Baseado em um esquema de Servidor-Web, o SISB contará com **3 principais clientes**: Android, iOs e navegador Web. Estes clientes terão um papel somente de  enviarão requisições através da API do SISB.
Baseado em uma arquitetura de três camadas, O SISB contará contará com uma camada de apresentação contendo a API de comunicação com clientes, a camada de negócio contendo os módulos principais, que também se comunicará com serviços externos e uma camada de persistência responsável pela manipulação e tratamento dos dados pertinentes ao sistema principal.

### Visão cliente servidor
![CLiente-servidor](https://github.com/Edionay/arquitetura201701/blob/master/Diagramas/Servidor%20web.png)

O SISB contará com aplicações externas nas plataformas Android e iOs e um módulo específico para um sistema baseado em Web, o ***SISB-WEB*** para a execução em navegadores. 
As aplicações cliente não realizarão processamento de dados complexos localmente, irão realizar a comunicação com o servidor SISB através de um protocolo http por meio da **API SISB**, a qual será desenvolvida levando em consideração os padrões do [Google API Design Guide](https://cloud.google.com/apis/design/).
Usando a linguagem de programação Java por meio da tecnologia JSP, o SISB sera constrído e terá suas requisições de clientes externos gerenciados por um servidor TurnKey Tomcat on Apache. O TomCat redirecionará as requisições dos clientes para a API SISB e o processamento será realizado no SISB. Particularmente as requisições feitas pelo cliente Navegador Web serão direcionadas para o SISB-WEB antes de chegarem ao processamento exato no SISB.

### Clientes Android e iOS
Tanto o cliente Android como o iOS fazem uso do protocolo http para se comunicarem com o SISB, por intermédio da API SISB. As requisições processadas pelo SISB nesse contexto serão respondidas no formato XML, que será interpretado pelos aplicativos e entregues ao usuário.

### Navegador WEB
O navegador WEB se comunica com o SISB fazendo uso de protocolo http e faz, através da API, requisições que passam pelo SISB-WEB e então é encaminhada e processada pelo SISB. O SISB, por sua vez, processa a requisição e devolve para o SISB-WEB.
O módulo SISB-WEB será construído fazendo uso das tecnologias HTML5, CSS3, JavaScript e Java.

(Affonso)

# Descrição das responsabilidades dos módulos do SISB
(Imagem)

## Agendamento
![Agendamento](https://github.com/Edionay/arquitetura201701/blob/master/Diagramas/agendamento.png)
 - Manter registros das consultas realizadas: Guarda um histórico de cada consulta realizada, contendo as informaçõe sobre: o respectivo paciente da consulta, data da(s) consulta(s), número de retorno(s), tipo de tratamento e odontólogo responsável pelo atendimento 
 - Sub módulo - Consulta: Responsável por concentrar os dados referentes à consulta do paciente no sistema
 - Manter registros das consultas marcadas e canceladas
 - Guarda um histórico das consultas que foram marcadas e canceladas, contendo as informações: paciente, data da consulta e odontólogo responsável.
 - Realizar agendamento de consultas: Permite ao paciente agendar uma consulta odontológica, por meio do registro único de saúde.
 - Cancelar consultas: Permite ao paciente cancelar uma consulta.
 - Reagendamento de consultas: Permite ao paciente solicitar uma nova data para uma consulta.
 - Ser capaz de informar a disponibilidade do profissional de saúde: Permite que  o paciente verifique a disponibilidade de um determinado odontólogo, em um período específico. 
 - Realizar agendamento de retorno: Permite ao paciente, marcar uma consulta de retorno para o dia mais adequado com o mesmo odontólogo.
 - Cancelamento de Retorno: Permite ao paciente, cancelar uma consulta de retorno.
 - Emissão de relatórios sobre consultas: Permite a emissão com os dados de uma consulta até o dado momento.

## Paciente

 - Informações pessoais do paciente: Como nome, endereço, telefone, CPF, etc...
Interface de comunicação com o Banco de Dados, tendo as seguintes funcionalidades: Realizar conexão com o BSUS, Requisição de Informações do BSUS e Atualização de dados dos pacientes do BSUS
 - Uma lista de atendimentos pendentes do paciente em questão, sob as seguintes especificações: Informações sobre a data de solicitação e a data marcada para a consulta, Informações sobre o prontuário e sobre o quadro do paciente e Informações sobre o profissional que o atendeu/atenderá
 - Sub módulo - Relógio: Com a função de gerenciar o tempo de espera e podendo ser usado para gerenciamento de filas de espera e Data de aniversário do paciente
 - Plano utilizado pelo paciente
 - Pendências financeiras

## Estoque
 - Controle de inventário de medicamentos e utilitários
 - Sub módulo - Produto: Categoria de modelo que engloba medicamentos e utilitários e Responsável por manter informações desses itens no inventário
 - Cadastro de produtos
 - Atualização de produtos
 - Seleção de produtos
 - Controle de categorias de produtos
 - Manutenção de registro de fornecedores
 - Sub módulo - Fornecedor: Modelo que define dentro do contexto do sistema um fornecedor de produtos
 - Emissão de relatórios sobre entradas e saídas de produtos

## Recursos Humanos
 - Sub módulo - Profissional de Saúde: Representação do Dentista ou Ajudante que compõe o corpo clínico do estabelecimento, Concentra informações pessoais destes profissionaise e Registra as competências deste profissional
 - Emissão de relatórios de produtividade do profissional, contendo: Horas trabalhadas e Pacientes atendidos

## Relatórios
 - Mantém armazenados relatórios gerados pelo corpo clínico sobre pacientes
 - Mantém armazenados relatórios sobre a remuneração do corpo clínico
 - Mantém relatórios sobre o controle de itens em estoque
 - Mantém relatórios sobre a produtividade do membros do corpo do clínico
 - Gera relatórios sobre pacientes redigidos pelo corpo clínico
 - Gera relatórios relacionados a remuneração do corpo clínico
 - Gera relatórios sobre as entradas e saídas de produto do estoque
 - Gera relatórios sobre a produtividade dos diversos membros do corpo clínico
 - Sub-módulo - Estatísticas: Registra dados de produtividade de membros do corpo clínico, Mantém dados de produtividade de membros do corpo clínico, Gera estatísticas baseadas nos dados previamente armazenados de desempenho do corpo clínice e Gera o Ranking entre os dentistas e outros membros do corpo clínico usando por base as estatísticas

## Sistemas Externos
 - Responsável pela integração entre os diversos sistemas externos com os quais o SISB se comunica e utiliza.
  - O módulo financeiro utiliza a API-Caixa do Banco para efetuar pagamento de boletos e o Bar_Convênios para o pagamento de convênios.
  - O módulo agendamento usa o Unimed-A200 no módulo Planos-de-saúde para realizar o agendamento de futuras consultas.
  - O módulo controle faz uso do OAuth2 no módulo Segurança, este que por sua vez usa o módulo Negócio, garantindo assim a proteção da aplicação.

## Financeiro
![Financeiro](https://github.com/Edionay/arquitetura201701/blob/master/Diagramas/Financeiro.jpg)
 - Repassa o capital para a gerência do estoque: Transfere o capital do governo, para a execução das atividades de gerência do estoque.
 - Repassa o capital para os recursos humanos: Transfere o capital do governo, para a execução das atividades de gerência dos recursos humanos.
 - Transfere o valor das taxas para o banco: Repassa os valores obtidos pela prestação de serviço do SISB, para a conta no banco
 - Presta contas: Emite relatórios das transações efetuadas dentro do SISB.
 - Realiza orçamentos para aquisições de material e pessoal: Responsável por fazer orçamentos pertinentes a compra de material e contratação de pessoal.

## Comunicação   
 - Alertar paciente sobre retorno via e-mail automaticamente: Envia um email para o paciente, alertando quanto ao retorno de sua consulta, com 3(três) dias de antecedência.
 - Alertar paciente sobre retorno via WhatsApp: Envia uma mensagem via whatsapp ao paciente, alertando quanto ao retorno de sua consulta, com 3(três) dias de antecedência.

## Pessoa 
 - Guarda informações sobre pacientes: Contém os dados e metadados sobre os clientes advindos do barramento BSUS.
 - Guarda informações sobre instituições: Contém os dados e metadados sobre a instituições que possuem convênio com o SISB. 

## Atenção à Saúde
 - Realiza o cadastro dos pacientes do SUS no sistema local: Classifica os pacientes utilizando o CID ( Código Internacional de Doenças).
 - Contabiliza as solicitações de exames e medicamentos vindas do corpo clínico: Essas solicitações por sua vez são repassadas ao estoque.





