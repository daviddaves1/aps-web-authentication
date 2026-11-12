## **Item de Informação 2.1 — Especificação de Requisitos de Software e Direcionadores Arquiteturais** 

* ### **Autor: David Daves Ferreira Pinto**

* ### **Revisora: Adriana Silveira de Souza**

* ### **Referências deste Item: \[ISO/IEC/IEEE 12207, 2026\]; \[SWEBOK V4.0, 2024\]; \[FIELDING; TAYLOR, 2002\]; \[BASS et al., 2021\].**

###   **1\. Escopo e Fronteiras do Sistema** 

#### **1.1. Objetivo**

Este documento especifica os requisitos funcionais, não funcionais e as decisões arquiteturais do serviço de autenticação de usuários para aplicações Web.

#### **1.2. Escopo**

O escopo do sistema limita-se à verificação de credenciais de usuários. O fluxo compreende receber as credenciais do cliente, validar a estrutura da requisição, consultar o usuário no banco de dados, comparar a senha informada com o hash armazenado e retornar o resultado da autenticação (sucesso com os dados do usuário ou erro padronizado).

#### **1.3. Fora de Escopo**

Estão expressamente fora do escopo do sistema as seguintes funcionalidades:

* Cadastro, alteração ou exclusão de usuários;  
* Gestão de status de conta (ativação, bloqueio ou suspensão);  
* Recuperação ou redefinição de senhas;  
* Emissão de tokens de sessão (JWT/OAuth), refresh tokens ou controle de acesso (RBAC).

### **2\. Partes Interessadas e Interesses Arquiteturais**

Alinhado com a norma ISO/IEC/IEEE 42010 e o SWEBOK v4.0, o projeto atende aos seguintes interesses das partes interessadas:

#### **2.1 \- Necessidades das Partes Interessadas**

* **STK01 — Segurança de Credenciais:** Garantir a proteção estrita de senhas e credenciais em trânsito e em repouso, prevenindo vazamentos, registro indevido em logs e ataques de enumeração ou temporais.  
* **STK02 \- Contrato de API Padronizado:** Disponibilizar uma interface HTTP/REST clara, previsível e baseada em JSON para facilitar a integração simples com aplicações clientes Web.  
* **STK03 \- Testabilidade Isolada:** Fornecer uma arquitetura modular que permita testar as regras de negócio de autenticação de forma automatizada e totalmente desprovida de dependências de infraestrutura (banco de dados, rede, HTTP).  
* **STK04 \- Desempenho sob Carga:** Assegurar respostas rápidas e alta disponibilidade do serviço de autenticação mesmo sob vazão constante de requisições em ambiente de produção.  
* **Aplicações Clientes:** Necessitam de contrato de API claro, respostas previsíveis e mensagens de erro padronizadas.  
* **Engenheiros de Software e Arquitetos:** Requerem código modular, desacoplamento entre camadas e facilidade para testes automatizados.  
* **Segurança e Operações:** Exigem proteção de credenciais em trânsito e em repouso, proibição de logs com senhas em texto plano e resiliência a falhas de infraestrutura.

* **STK05 \- Verificação de Identidade:** Garantir que o sistema confirme com precisão a autenticidade das credenciais informadas pelo usuário antes de conceder acesso às respostas de sucesso.  
* **STK06 \- Identificação do Usuário:** Retornar dados essenciais e seguros de identificação do usuário quando a autenticação for bem-sucedida, sem expor informações sensíveis.  
* **STK07 \- Comunicação de Falhas:** Fornecer mensagens de erro estruturadas e previsíveis em cenários de credenciais inválidas, falhas de validação ou indisponibilidade de infraestrutura.

### **3\. Requisitos Funcionais** 

#### **3.1. Especificação dos Requisitos Funcionais (RFs)**

Os requisitos funcionais definem os comportamentos atômicos do sistema:

* **RF01 \- Receber Requisição de Autenticação:** Ao receber uma solicitação no endpoint POST /auth/verify, o sistema deve receber os campos obrigatórios de identificador (string não vazia resolvida pelo repositório) e senha fornecidos pelo cliente web.  
  *\[Rationale: Capturar os dados de entrada de credenciais para o fluxo de autenticação \| Origem: Enunciado da APS \| Referência metodológica: ISO/IEC/IEEE 29148:2018, seção 9.6 \| Prioridade: Alta \| Estabilidade: Alta \| Verificação: Test\]*  
* **RF02 \- Validar Sintaxe dos Dados de Entrada:** Quando a requisição for recebida, o sistema deve validar a presença, os tipos de dados e a não-vacuidade dos campos obrigatórios identificador e senha.  
  *\[Rationale: Bloquear requisições malformadas na camada de controle antes do processamento \| Origem: Enunciado da APS \| Referência metodológica: ISO/IEC/IEEE 29148:2018, seção 9.6 \| Prioridade: Alta \| Estabilidade: Alta \| Verificação: Test\]*  
* **RF03 \- Consultar Cadastro do Usuário:** Após a validação sintática bem-sucedida, o sistema deve pesquisar a conta do usuário no repositório de dados utilizando o identificador informado.  
  *\[Rationale: Localizar o registro persistido e recuperar os parâmetros do hash para validação \| Origem: Enunciado da APS \| Referência metodológica: ISO/IEC/IEEE 29148:2018, seção 9.6 \| Prioridade: Alta \| Estabilidade: Alta \| Verificação: Test\]*  
* **RF04 \- Executar Verificação de Senha:** Quando a conta do usuário for localizada, o sistema deve comparar a senha informada com o hash armazenado utilizando o mecanismo de verificação de senhas compatível com o algoritmo empregado no armazenamento (Argon2id).  
  *\[Rationale: Confirmar a autenticidade da credencial fornecida pelo cliente mediante algoritmo adaptativo \| Origem: Enunciado da APS \| Referência metodológica: ISO/IEC/IEEE 29148:2018, seção 9.6 \| Prioridade: Alta \| Estabilidade: Alta \| Verificação: Test\]*  
* **RF05 \- Emitir Resposta de Autenticação Bem-Sucedida:** Quando a verificação de senha for confirmada com sucesso, o sistema deve retornar a resposta HTTP 200 OK contendo os dados de identificação do usuário (como id e e-mail), sem expor credenciais ou hashes.  
  *\[Rationale: Notificar o cliente quanto ao sucesso do procedimento e disponibilizar os dados básicos \| Origem: Enunciado da APS \| Referência metodológica: ISO/IEC/IEEE 29148:2018, seção 9.6 \| Prioridade: Alta \| Estabilidade: Alta \| Verificação: Test\]*  
* **RF06 \- Emitir Resposta de Credenciais Inválidas:** Quando o usuário não for encontrado ou a senha for divergente, o sistema deve retornar a resposta HTTP 401 Unauthorized com mensagem padronizada.  
  *\[Rationale: Informar a falha de autenticação sem revelar a existência do identificador e mitigar diferenças temporais observáveis \| Origem: Enunciado da APS \| Referência metodológica: ISO/IEC/IEEE 29148:2018, seção 9.6 \| Prioridade: Alta \| Estabilidade: Alta \| Verificação: Test\]*  
* **RF07 \- Emitir Resposta de Falha de Validação:** Quando os dados de entrada descumprirem as regras sintáticas, o sistema deve retornar a resposta HTTP 400 Bad Request detalhando os erros detectados.  
  *\[Rationale: Notificar o cliente sobre falhas no contrato de requisição \| Origem: Enunciado da APS \| Referência metodológica: ISO/IEC/IEEE 29148:2018, seção 9.6 \| Prioridade: Alta \| Estabilidade: Alta \| Verificação: Test\]*  
* **RF08 \- Tratamento de Falhas de Infraestrutura:** Em caso de indisponibilidade ou estouro de tempo limite na comunicação com a base de dados, o sistema deve retornar a resposta HTTP 503 Service Unavailable.  
  *\[Rationale: Isolar exceções técnicas e garantir resposta estruturada perante falhas externas \| Origem: Enunciado da APS \| Referência metodológica: ISO/IEC/IEEE 29148:2018, seção 9.6 \| Prioridade: Média \| Estabilidade: Alta \| Verificação: Test\]*

#### **3.2. Critérios de Aceitação e Fluxos Processuais**

A Tabela 1 especifica os critérios de aceitação para validação automatizada e testes de aceitação dos requisitos funcionais.

**Tabela 1 \- Critérios de Aceitação e Comportamento Esperado por Cenário**

| ID | Cenário / Fluxo | Entrada | Condição de Execução | Resultado Esperado (Saída) | Ref. |
| :---- | :---- | :---- | :---- | :---- | :---- |
| **CA01** | **Autenticação com Sucesso** | *identifier* e *password* válidos | Usuário cadastrado e hash de senha correspondente | Status *HTTP* *200* *OK* e dados do usuário | RF01, RF02, RF03, RF04, RF05 |
| **CA02** | **Usuário Não Encontrado** | *identifier* e *password* preenchidos | Identificador não cadastrado no banco de dados | Status *HTTP* *401* *Unauthorized* e mensagem de erro de credenciais | RF01, RF02, RF03, RF06 |
| **CA03** | **Senha Incorreta** | *identifier* e *password* preenchidos | Usuário localizado, porém a senha não corresponde ao hash | Status *HTTP* *401* *Unauthorized* e mensagem de erro de credenciais (mesma resposta do CA02) | RF01, RF02, RF03, RF04, RF06 |
| **CA04** | **Dados de Entrada Inválidos** | Payload sem *identifier*, sem *password* ou JSON malformado | Falha na validação dos campos obrigatórios | Status *HTTP* *400* *Ba*d Request e detalhes dos erros de validação | RF01, RF02, RF07 |
| **CA05** | **Falha do Banco de Dados** | *identifier* e *password* preenchidos | Indisponibilidade de conexão ou timeout na consulta ao repositório | Status *HTTP* *503* *Service* *Unavailable* e mensagem genérica de erro de serviço no cenário de indisponibilidade do banco de dados | RF01, RF03, RF08 |

Fonte: Elaborada pelo autor com base nos requisitos de domínio do Apêndice A.

### **4\. Requisitos Não Funcionais (RNFs) e Decisões Arquiteturais (ADRs)**

Esta seção separa os Requisitos Não Funcionais (metas de qualidade verificáveis) das Decisões Arquiteturais (mecanismos de projeto adotados).

* #### **4.1. Segurança (RNF01 / ADR01)**

  * **Requisito Não Funcional (RNF01 / ASR01):** O sistema deve garantir a confidencialidade das credenciais em trânsito, proibir o registro de senhas em texto claro em logs e exigir a mitigação de diferenças temporais observáveis para prevenir ataques temporais e de enumeração de usuários.  
  * **Decisão Arquitetural (ADR01):** Obrigatoriedade do protocolo HTTPS (TLS 1.3) na camada de transporte. O algoritmo de hash adotado é o Argon2id. Para mitigar diferenças temporais observáveis, o sistema executará um cálculo de hash fictício equivalente quando o identificador não for localizado na base de dados.

* #### **4.2. Contrato de API e Comunicação (RNF02 / ADR02)**

* **Requisito Não Funcional (RNF02):** O sistema deve disponibilizar uma interface HTTP padronizada baseada em JSON para integração simples com clientes Web.  
* **Decisão Arquitetural (ADR02):** Definição do endpoint *POST /auth/verify* aceitando o cabeçalho *Content-Type: application/json*. Especificação detalhada das estruturas de payload:  
  * **Método e Endpoint:** *POST /auth/verify* (Cabeçalho obrigatório: *Content-Type: application/json*).  
  * **Formato da Requisição (Entrada):**

```json
{
  "identifier": "usuario@dominio.com",
  "password": "SenhaSubmetidaPeloCliente"
}
```

*   
  **Resposta de Sucesso — Deferimento (HTTP 200 OK):**

```json
{
  "status": "AUTHENTICATED",
  "user": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "identifier": "usuario@dominio.com"
  }
}
```

*   
  **Resposta de Erro de Autenticação (HTTP 401 Unauthorized):**

```json
{
  "status": "UNAUTHENTICATED",
  "error": "INVALID_CREDENTIALS",
  "message": "Credenciais de autenticacao invalidas."
}
```

*   
  **Resposta de Erro de Validação (HTTP 400 Bad Request):**

```json
{
  "status": "BAD_REQUEST",
  "error": "VALIDATION_ERROR",
  "message": "Os campos 'identifier' e 'password' sao obrigatorios."
}
```

*   
  **Resposta de Erro Técnico (HTTP 503 Service Unavailable):**

```json
{
  "status": "ERROR",
  "error": "SERVICE_UNAVAILABLE",
  "message": "Servico temporariamente indisponivel para processamento."
}
```

#### 

#### **4.3. Desempenho, Disponibilidade e Concorrência (RNF03 / ADR03)**

* **Requisito Não Funcional (RNF03 / ASR03 \- Desempenho e Capacidade):** Sob uma carga constante de 100 requisições por segundo mantida durante um período contínuo de 10 minutos (conforme especificado no Plano de Verificação), o sistema deve manter o tempo de resposta no percentil 95 (P95) inferior a 200 milissegundos e a taxa de erros técnicos (HTTP 5xx) inferior a 0,1% em um ambiente de referência composto por 2 vCPUs e 4 GB de RAM.  
  *\[Rationale: Garantir capacidade de resposta e vazão dentro das métricas de desempenho sob carga operacional especificada \| Origem: Enunciado da APS \| Referência metodológica: ISO/IEC/IEEE 29148:2018, seção 9.6 \| Prioridade: Alta \| Estabilidade: Alta \| Verificação: Test\]*  
* **Decisão Arquitetural (ADR03):** Arquitetura stateless no servidor de aplicação, pool de conexões TCP gerenciado com a base de dados configurado com limite de tempo de conexão (connect-timeout) de 2000ms e tempo de leitura (read-timeout) de 3000ms para assegurar a meta de P95 inferior a 200ms, e tratamento global de exceções de infraestrutura.

#### **4.4. Modificabilidade e Testabilidade (RNF04 / ADR04)**

* **Requisito Não Funcional (RNF04 / ASR04):** A arquitetura do sistema deve focar na isolação de testes unitários da regra de negócio de autenticação, permitindo a execução de testes automatizados sem dependência direta de banco de dados, rede ou servidor HTTP.  
* **Decisão Arquitetural (ADR04):** Aplicação da Arquitetura Hexagonal através de interfaces abstratas para o repositório de usuários (UserRepositoryPort) e verificador de senha (PasswordVerifierPort).

#### **4.5. Requisitos Lógicos de Dados e Limites de Interface Externa (RLD01 / LIE01 / LIE02)**

Conforme as diretrizes das seções 9.6.11 e 9.6.15 da ISO/IEC/IEEE 29148, definem-se as especificações lógicas dos dados e as fronteiras das interfaces externas do serviço:

* **RLD01 \- Schema do Usuário (Repositório de Dados):** A entidade lógica do usuário deve conter os campos: id (UUID v4, 36 caracteres, chave primária), identifier (string não vazia resolvida pelo repositório, única) e password\_hash (String encodada em ASCII/Base64 contendo os parâmetros do algoritmo *Argon2id*, salt e hash gerado).  
* **LIE01 \- Interface HTTP/JSON Externa:** O serviço comunica-se via protocolo HTTPS (TLS 1.3) enviando e recebendo payloads serializados em formato JSON (UTF-8). O payload de entrada aceita no máximo 4 KB por requisição para prevenção de consumo excessivo de memória.  
* **LIE02 \- Interface de Repositório de Persistência:** Define o contrato abstrato de comunicação lógica entre a camada de aplicação e o repositório de persistência de dados para consulta e recuperação dos registros de usuários.

### **5\. Restrições e Premissas**

#### **5.1. Restrições de Escopo**

* **CON01 \- Limitação de Escopo:** Proibida a inclusão de endpoints ou esquemas de dados que não pertençam estritamente ao fluxo de verificação de credenciais.

#### **5.2. Restrições do Ambiente Web**

* **CON02 \- Ambiente Web:** O serviço deve funcionar integrado a servidores web proxy/reverse proxy com suporte a terminação TLS, conforme especificação detalhada no documento de ambiente Web da aplicação.

#### **5.3. Premissas Arquiteturais**

* **PRE01 \- Base de Dados Existente:** Assume-se que a base de dados já possui usuários cadastrados com senhas salvas em hash de formato compatível com o algoritmo configurado.


### **6\. Matriz de Rastreabilidade**

A Tabela 2 apresenta o mapeamento completo entre as etapas do processo, requisitos funcionais atômicos, critérios de aceitação e decisões de arquitetura. As referências mapeadas indicam as seções correspondentes da estrutura principal do documento PDF final da APS.

**Tabela 2 \- Matriz de Rastreabilidade Geral do Documento**

| Necessidade (STK) | ID Req. | Descrição Atômica | Critério Aceitação | Arquitetura (ADR) | Método Verificação | Prioridade | Etapa Domínio |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| STK02 | **RF01** | Receber requisição de autenticação (identifier e password) | CA01, CA02, CA03, CA04, CA05 | ADR02, LIE01 | Test | Alta | Entrada de dados |
| STK02 | **RF02** | Validar sintaxe dos dados de entrada | CA01, CA02, CA03, CA04 | ADR02, LIE01 | Test | Alta | Entrada de dados |
| STK03 | **RF03** | Consultar cadastro do usuário | CA01, CA02, CA03, CA05 | ADR04, RLD01, LIE02 | Test | Alta | Consulta usuário |
| STK01, STK03 | **RF04** | Executar verificação de senha | CA01, CA03 | ADR01, ADR04 | Test | Alta | Verificação senha |
| STK02 | **RF05** | Emitir resposta de autenticação bem-sucedida (200 OK) | CA01 | ADR02 | Test | Alta | Retorno resposta |
| STK01, STK02 | **RF06** | Emitir resposta de credenciais inválidas (401 Unauthorized) | CA02, CA03 | ADR01, ADR02 | Test | Alta | Retorno resposta |
| STK02 | **RF07** | Emitir resposta de falha de validação (400 Bad Request) | CA04 | ADR02 | Test | Alta | Retorno resposta |
| STK04 | **RF08** | Tratamento de falhas de infraestrutura (503 Service Unavailable) | CA05 | ADR03, LIE02 | Test | Média | Retorno resposta |

Fonte: Elaborada pelo autor com base em \[SWEBOK V4.0, 2024\] e \[KRUCHTEN, 1995\].