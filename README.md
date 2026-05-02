# C4 Model Software Architecture

Este repositório documenta a arquitetura do **SIMA — Sistema Integrado de Monitoramento e Gestão de Configuração para Máquinas Agrícolas**, usando o **C4 Model** para representar o sistema em relação aos seus usuários, integrações externas e responsabilidades principais.

## Visão Geral

O SIMA é uma plataforma corporativa voltada ao monitoramento e à gestão de configuração de frotas agrícolas conectadas. O sistema centraliza dados de telemetria, dashboards operacionais, alertas, configurações remotas, atualizações de firmware e integrações com serviços externos, como clima, ERP, banco de dados central e provedor de identidade.

## Objetivos do Projeto

- Representar o **diagrama de contexto** do SIMA no C4 Model.
- Identificar atores humanos, sistemas externos e fronteiras de responsabilidade.
- Documentar os principais fluxos de comunicação entre o SIMA e seu ecossistema.
- Manter os diagramas como código, facilitando versionamento e evolução.

## Documentação

- [`docs.md`](docs.md): documentação completa do contexto, atores, sistemas externos, fronteiras e relacionamentos.
- [`diagrams/sima-context.mmd`](diagrams/sima-context.mmd): diagrama de contexto em Mermaid.

## Diagrama de Contexto

```mermaid
flowchart TB
    %% ===== ATORES (Pessoas) =====
    operador(["Operador Agrícola<br/>[Pessoa]<br/>Opera as máquinas no campo<br/>e monitora desempenho"])
    admin(["Administrador de Configuração<br/>[Pessoa]<br/>Gerencia perfis operacionais,<br/>firmware e parâmetros"])
    gestor(["Gestor da Fazenda<br/>[Pessoa]<br/>Consome relatórios gerenciais<br/>e KPIs de produtividade"])
    manutencao(["Equipe de Manutenção<br/>[Pessoa]<br/>Recebe alertas e ordens<br/>de serviço"])

    %% ===== SISTEMA CENTRAL =====
    sima["SIMA<br/>[Software System]<br/>Sistema Integrado de Monitoramento<br/>e Gestão de Configuração para<br/>Máquinas Agrícolas"]

    %% ===== SISTEMAS EXTERNOS =====
    maquinas["Frota de Máquinas Agrícolas<br/>[Sistema Externo]<br/>Tratores, colheitadeiras e<br/>pulverizadores com sensores IoT"]
    middleware["Plataforma de Integração<br/>[Sistema Externo]<br/>Broker MQTT/AMQP que abstrai<br/>a comunicação com dispositivos"]
    clima["Serviço de Dados Meteorológicos<br/>[Sistema Externo]<br/>API externa de previsão e<br/>alertas climáticos"]
    banco[("Banco de Dados Central<br/>[Sistema Externo]<br/>Repositório de telemetria,<br/>configurações e auditoria")]
    erp["Sistema ERP da Fazenda<br/>[Sistema Externo]<br/>Gestão financeira, insumos,<br/>estoque e custos"]
    idp["Provedor de Identidade (IdP)<br/>[Sistema Externo]<br/>Autenticação SSO/OAuth2<br/>corporativa"]

    %% ===== RELACIONAMENTOS COM ATORES =====
    operador -->|"Monitora máquinas e visualiza<br/>alertas em tempo real<br/>[HTTPS / WebSocket]"| sima
    admin -->|"Define e publica configurações<br/>da frota<br/>[HTTPS]"| sima
    gestor -->|"Consulta dashboards e<br/>relatórios gerenciais<br/>[HTTPS]"| sima
    sima -->|"Notifica alertas e abre<br/>ordens de serviço<br/>[Email / Push / SMS]"| manutencao

    %% ===== RELACIONAMENTOS COM SISTEMAS EXTERNOS =====
    sima -->|"Publica comandos e configurações;<br/>assina tópicos de telemetria<br/>[MQTT / AMQP]"| middleware
    middleware -->|"Encaminha comandos e<br/>coleta telemetria<br/>[MQTT-SN / 4G / LoRaWAN]"| maquinas
    maquinas -->|"Envia telemetria de sensores<br/>(RPM, temperatura, GPS, consumo)<br/>[MQTT-SN / 4G]"| middleware

    sima -->|"Consulta previsão e alertas<br/>meteorológicos por região<br/>[HTTPS / REST]"| clima
    sima -->|"Persiste e consulta dados<br/>operacionais e configurações<br/>[JDBC / SQL / TLS]"| banco
    sima -->|"Sincroniza dados de custos,<br/>insumos e ordens de serviço<br/>[REST / Webhook]"| erp
    sima -->|"Autentica e autoriza usuários<br/>[OAuth2 / OIDC / SAML]"| idp

    %% ===== ESTILOS C4 =====
    classDef pessoa fill:#08427B,stroke:#052E56,color:#ffffff,stroke-width:2px
    classDef sistema fill:#1168BD,stroke:#0B4884,color:#ffffff,stroke-width:3px
    classDef externo fill:#999999,stroke:#6B6B6B,color:#ffffff,stroke-width:2px

    class operador,admin,gestor,manutencao pessoa
    class sima sistema
    class maquinas,middleware,clima,banco,erp,idp externo
```

## Estrutura

```text
.
├── README.md
├── docs.md
└── diagrams/
    └── sima-context.mmd
```

## Como Visualizar

O diagrama Mermaid pode ser visualizado diretamente em plataformas com suporte a Mermaid, como GitHub, GitLab e editores compatíveis. Para uma explicação completa dos atores, sistemas externos e relacionamentos, consulte [`docs.md`](docs.md).