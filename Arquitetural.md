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


