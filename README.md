# EduVerse - Plataforma de Aprendizado Adaptativo

## Visão Geral

O EduVerse é uma plataforma de aprendizado adaptativo que utiliza inteligência artificial para personalizar a experiência educacional de cada estudante. O sistema analisa hábitos de estudo, desempenho acadêmico e consumo de conteúdo para gerar recomendações individualizadas de materiais e trilhas de aprendizagem.

Ela busca resolver o problema de ambientes tradicionais de ensino online, onde normalmente é oferecido o mesmo conteúdo para todos os estudantes, desconsiderando diferenças de ritmo, desempenho e preferências individuais.

## Estado Atual do Projeto

O projeto encontra-se na **Fase 3 do Mini Projeto O Arquiteto Decisor**, possuindo:

* Cenário de negócio definido.
* Requisitos funcionais e não funcionais documentados.
* Diagrama de Contexto (C4 Nível 1).
* Diagrama de Containers (C4 Nível 2).
* Arquitetura Monolítica Modular definida.
* Estratégia de persistência poliglota (MySQL, MongoDB e Redis).
* Estratégia de implantação em nuvem baseada em IaaS.
* Estratégias de resiliência documentadas.
* Architecture Decision Records (ADRs) registrados.

---

# Atributos de Qualidade Priorizados

* Segurança
* Performance
* Usabilidade
* Manutenibilidade
* Escalabilidade

---

# Arquitetura de Containers (C4 - Nível 2)

```mermaid
flowchart TB

%% Pessoas
Estudante["👤 Estudante<br/>Usuário que utiliza o sistema para estudar"]
Administrador["👤 Administrador<br/>Usuário que gerencia e configura o sistema"]

%% Sistema
subgraph EduVerse["EduVerse [Software System]"]

    SPA["Single-Page Application<br/>React<br/><br/>Permite acesso ao sistema em navegadores web"]

    APIGateway["API Gateway<br/>Express.js<br/><br/>Centraliza autenticação, segurança, monitoramento e roteamento"]

    Backend["Backend EduVerse<br/>Monólito Modular (Express.js)"]

    Usuarios["Módulo Usuários<br/><br/>Gerencia contas e perfis"]
    Conteudo["Módulo Conteúdo<br/><br/>Busca materiais didáticos"]
    Recomendacoes["Módulo Recomendações<br/><br/>Personalização e adaptação de conteúdo"]
    Analytics["Módulo Analytics<br/><br/>Coleta e processamento de dados de uso"]

    RabbitMQ["Fila<br/>RabbitMQ<br/><br/>Processamento assíncrono"]

    Worker["Worker Assíncrono<br/>Node.js<br/><br/>Processa tarefas em segundo plano"]

    MySQL[("Banco de Dados Relacional<br/>MySQL")]
    Redis[("Redis<br/>Cache e sessões")]
    MongoDB[("Banco de Dados Não Relacional<br/>MongoDB")]
end

%% Sistemas Externos
LMS["Sistemas de Gestão Acadêmica (LMS)"]
Repositorios["Repositórios de Conteúdo"]
Gemini["Google Gemini"]

%% Usuários
Estudante -->|"HTTPS/REST"| SPA
Administrador -->|"HTTPS/REST"| SPA

%% Entrada do sistema
SPA -->|"HTTPS/REST"| APIGateway
APIGateway -->|"HTTPS/REST"| Backend

%% Módulos internos
Backend -.-> Usuarios
Backend -.-> Conteudo
Backend -.-> Recomendacoes
Backend -.-> Analytics

%% Persistência
Usuarios -->|"SQL/TCP"| MySQL
Analytics -->|"SQL/TCP"| MySQL

Usuarios -->|"Driver/TCP"| Redis
Analytics -->|"Driver/TCP"| Redis

Worker -->|"Driver/TCP"| MongoDB

%% Integrações externas
Conteudo -->|"HTTPS/REST<br/>Busca materiais"| Repositorios

Recomendacoes -->|"HTTPS/REST<br/>Entrega recomendações"| LMS

%% Processamento assíncrono
Analytics -->|"AMQP<br/>Eventos e hábitos de estudo"| RabbitMQ

RabbitMQ -.->|"AMQP"| Worker

Worker -.->|"HTTPS/REST<br/>Análise e recomendações"| Gemini

Gemini -.->|"HTTPS/REST<br/>Resultado da análise"| Recomendacoes

%% Estilos
classDef person fill:#083F75,color:#fff,stroke:#06315C;
classDef container fill:#23A2D9,color:#fff,stroke:#0E7DAD;
classDef database fill:#4757A2,color:#fff,stroke:#BCBCBC;
classDef external fill:#8C8496,color:#fff,stroke:#736782;

class Estudante,Administrador person;
class SPA,APIGateway,Backend,Usuarios,Conteudo,Recomendacoes,Analytics,RabbitMQ,Worker container;
class MySQL,Redis,MongoDB database;
class LMS,Repositorios,Gemini external;
```

---

# Principais Tecnologias

| Camada           | Tecnologia               |
| ---------------- | ------------------------ |
| Frontend         | React                    |
| Backend          | Node.js                  |
| Banco Relacional | MySQL                    |
| Banco NoSQL      | MongoDB                  |
| Cache            | Redis                    |
| Mensageria       | RabbitMQ                 |
| Cloud            | GCP (IaaS)               |
| IA               | Gemini                   |

---

# Estratégia de Implantação

A arquitetura será implantada em ambiente Cloud utilizando o modelo **Infrastructure as a Service (IaaS)**.

Características principais:

* Escalabilidade vertical inicialmente.
* Evolução para escalabilidade horizontal quando necessário.
* Balanceamento de carga em cenários de crescimento.
* Monitoramento de infraestrutura e aplicação.
* Cache distribuído utilizando Redis.
* Processamento assíncrono de recomendações através de filas.

---

# Estratégias de Resiliência

Para aumentar disponibilidade e tolerância a falhas, a arquitetura utiliza:

* API Gateway
* Circuit Breaker
* Bulkhead
* Cache de recomendações
* Processamento assíncrono
* Fila de espera para solicitações à IA

---

# Architecture Decision Records (ADRs)

Os principais registros de decisão arquitetural podem ser encontrados em:

- [ADR 003 - Estratégia de implantação em Nuvem e Escalabilidade](./docs/adrs/ADR_003.md)
- [ADR 004 - Padrões de Resiliência (API Gateway, Circuit Breaker e Bulkhead)](./docs/adrs/ADR_004.md)
- [ADR 005 - Modelo de Comunicação Híbrido (Síncrono e Assíncrono)](./docs/adrs/ADR_005.md)