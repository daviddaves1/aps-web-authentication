# Item de Informação 2.2 — Descrição Arquitetural e Especificação dos Componentes

* **Autor:** David Daves Ferreira Pinto
* **Docente Responsável:** Adriana Silveira de Souza
* **Referências deste Item:** (COCKBURN, 2005); (IEEE COMPUTER SOCIETY, 2024); (MDN WEB DOCS, s.d.-a); (MDN WEB DOCS, s.d.-b).

---

## 1. Visão Geral da Arquitetura

### 1.1. Objetivo da Arquitetura

A presente descrição arquitetural estabelece a organização estrutural, os contratos de comunicação e os limites operacionais do **Serviço de Autenticação Web**, traduzindo os requisitos funcionais (RF01 a RF08) e não funcionais (RNF01 a RNF04) definidos na [Especificação de Requisitos](../02-arquitetura/especificacao-requisitos.md) em componentes de software claramente delimitados. Em conformidade com os conceitos e práticas da área de conhecimento de Arquitetura de Software consolidados pela IEEE Computer Society (2024), este documento estabelece a relação entre a especificação de requisitos e as decisões que orientam a implementação, direcionando o sistema ao cumprimento de sua finalidade estrita: receber credenciais de acesso, consultar a existência do usuário, verificar a equivalência da senha em face do hash armazenado e retornar a decisão correspondente.

Para além do comportamento funcional, a arquitetura foi desenhada para estruturar a verificação de credenciais diante das exigências e padrões de comunicação do ambiente operacional Web (CON02) (MDN WEB DOCS, s.d.-a; MDN WEB DOCS, s.d.-b). O projeto estabelece mecanismos arquiteturais destinados a atender aos requisitos de qualidade e às restrições operacionais do sistema — abrangendo a proteção das credenciais (RNF01/ADR01), a padronização da comunicação HTTP/JSON (RNF02/ADR02), o desempenho e a capacidade operacional (RNF03/ADR03) e a testabilidade e modificabilidade (RNF04/ADR04) —, permitindo sua posterior verificação por meio de testes e avaliações técnicas, amparadas pelas visões arquiteturais descritas nos modelos C4 (IEEE COMPUTER SOCIETY, 2024).

### 1.2. Estilo Arquitetural Adotado

A organização interna da aplicação adota o estilo de **Arquitetura Hexagonal**, igualmente denominado **Portas e Adaptadores (*Ports and Adapters*)** (COCKBURN, 2005), conforme registrado na decisão arquitetural ADR04 e alinhado aos princípios de modularidade e separação de preocupações (*separation of concerns*) descritos pela IEEE Computer Society (2024). Sob esse paradigma, o software é particionado em um núcleo central de aplicação — detentor exclusivo da lógica do caso de uso de autenticação — e uma camada periférica composta por adaptadores técnicos que medeiam a interação do sistema com o ambiente externo.

A concretização desse isolamento ocorre por meio da inversão de dependências: o núcleo da aplicação não conhece nem importa implementações concretas de servidores HTTP, bancos de dados ou bibliotecas criptográficas, comunicando-se exclusivamente através de contratos abstratos (interfaces) denominados portas (COCKBURN, 2005). As portas de entrada (*Driving Ports*) definem como agentes externos acionam o núcleo, ao passo que as portas de saída (*Driven Ports*) especificam as capacidades que o núcleo exige da infraestrutura para concluir seu processamento. A Tabela 1 sintetiza a distribuição dos elementos do projeto segundo essa estrutura, em alinhamento com o modelo C4 proposto por Brown (2018) e representado no [Diagrama C4 de Componentes](../../diagramas/c4/componentes.puml).

**Tabela 1 — Organização dos Elementos na Arquitetura Hexagonal**

| Camada ou Elemento | Componente no Projeto e Responsabilidade Síntese |
| :--- | :--- |
| **Adaptador Primário** | `AuthHttpController` — recebe e valida sintaticamente as requisições HTTP/JSON, traduzindo o desfecho do caso de uso nos códigos de status previstos (MDN WEB DOCS, s.d.-b). |
| **Porta de Entrada** | `AuthenticationUseCasePort` — contrato abstrato que expõe a operação do caso de uso de verificação de credenciais. |
| **Núcleo da Aplicação** | `AuthenticationService` — coordena o fluxo de autenticação, determinando a validade das credenciais (IEEE COMPUTER SOCIETY, 2024). |
| **Portas de Saída** | `UserRepositoryPort` e `PasswordVerifierPort` — contratos abstratos requeridos pelo núcleo para leitura de dados e verificação criptográfica. |
| **Adaptadores Secundários** | `DatabaseUserRepositoryAdapter` e `Argon2PasswordVerifierAdapter` — implementam as portas de saída, integrando o SGBD externo e o algoritmo Argon2id. |

*Fonte: Elaborada pelo autor com base no [Diagrama C4 de Componentes](../../diagramas/c4/componentes.puml).*

### 1.3. Justificativa da Arquitetura Hexagonal

A escolha da Arquitetura Hexagonal justifica-se pela necessidade de proteger a regra decisória de autenticação contra o acoplamento direto a tecnologias de infraestrutura e protocolos de rede (COCKBURN, 2005; IEEE COMPUTER SOCIETY, 2024). Em serviços de segurança, a regra que determina quando um usuário deve ser considerado autenticado ou rejeitado precisa permanecer clara, estável e independente de detalhes de transporte ou armazenamento. Ao isolar o `AuthenticationService` atrás de interfaces abstratas, evita-se que particularidades do protocolo Web (como cabeçalhos HTTP, serialização JSON ou códigos de status `200`, `400`, `401`, `500` e `503`) se misturem à lógica central de verificação, mantendo as responsabilidades de comunicação restritas ao `AuthHttpController`.

Adicionalmente, esse desacoplamento atende diretamente às diretrizes de testabilidade e modificabilidade (RNF04/ADR04) (IEEE COMPUTER SOCIETY, 2024). Essa organização permite verificar isoladamente as regras do caso de uso de autenticação por meio de testes unitários automatizados, utilizando *mocks* ou *stubs* para simular as dependências externas (`UserRepositoryPort` e `PasswordVerifierPort`) (COCKBURN, 2005). A validação integral dos critérios de aceitação (CA01 a CA05), incluindo os contratos HTTP no adaptador primário e as integrações reais de infraestrutura nos adaptadores secundários, deverá ser complementada por testes de integração e de aceitação. Do mesmo modo, eventuais substituições do banco de dados ou da biblioteca criptográfica podem ser realizadas nos adaptadores secundários, sem exigir alterações no núcleo, desde que os contratos das portas permaneçam compatíveis.

### 1.4. Fronteiras e Dependências Externas

Conforme formalizado no [Diagrama C4 de Contexto](../../diagramas/c4/contexto.puml) e no [Diagrama C4 de Contêineres](../../diagramas/c4/conteineres.puml), a fronteira arquitetural do **Serviço de Autenticação Web** abrange o contêiner de borda (**Servidor Web / Reverse Proxy**) e o contêiner de execução da regra de negócio (**Aplicação de Autenticação**), alinhando-se aos princípios de estruturação cliente-servidor e intermediação na Web descritos em MDN Web Docs (s.d.-a). O escopo funcional contido nessa fronteira submete-se rigorosamente à restrição CON01 e às diretrizes de delimitação funcional (IEEE COMPUTER SOCIETY, 2024), limitando-se à verificação de credenciais e vedando qualquer operação de cadastro, edição cadastral, controle de status de conta (ativo/inativo) ou gerenciamento de sessões pós-autenticação.

Fora dos limites do sistema situam-se dois elementos externos que interagem com a solução:
1. **Aplicação Cliente (Consumidor Externo):** Sistema executado no ambiente do usuário (como uma aplicação em navegador Web) que coleta o identificador e a senha informados pelo ator humano e solicita a autenticação (`POST /auth/verify`) via HTTPS/JSON ao serviço (MDN WEB DOCS, s.d.-b), recebendo de volta a identificação do usuário ou a mensagem de erro correspondente.
2. **Base de Usuários (Dependência Externa Preexistente):** Em observância à premissa arquitetural PRE01, o repositório que armazena os registros de usuários e seus respectivos hashes de senha pertence a um sistema externo preexistente, sem responsabilidade de provisionamento ou gerência por parte do serviço em análise. A **Aplicação de Autenticação** interage com essa base exclusivamente em modo de leitura (*read-only*) por intermédio do `DatabaseUserRepositoryAdapter`, tratando eventuais falhas de conectividade ou *timeout* como erros de infraestrutura (RF08) distintos da rejeição por credenciais inválidas (RF06).

---

## 2. Organização Arquitetural

### 2.1. Organização em Contêineres

A visão de contêineres, representada no [Diagrama C4 de Contêineres](../../diagramas/c4/conteineres.puml), apresenta a distribuição das responsabilidades operacionais entre as unidades de execução do sistema para atender à restrição do ambiente Web (CON02) e ao requisito de segurança em trânsito (RNF01/ADR01) (MDN WEB DOCS, s.d.-a; MDN WEB DOCS, s.d.-b).

A comunicação HTTP entre o **Servidor Web / Reverse Proxy** e a **Aplicação de Autenticação** ocorre exclusivamente em uma rede interna isolada e controlada, sem exposição direta à Internet. Caso atravesse redes não confiáveis, essa comunicação deverá ser protegida por TLS, preservando o requisito RNF01. A Tabela 2 identifica cada contêiner e sistema interagente, detalhando seu papel operacional.

**Tabela 2 — Especificação dos Contêineres e Sistemas Interagentes (C4 Nível 2)**

| Elemento / Contêiner | Escopo | Protocolo de Comunicação | Responsabilidade Operacional |
| :--- | :--- | :--- | :--- |
| **Aplicação Cliente** | Externo | `HTTPS / JSON` | Coleta as credenciais informadas pelo usuário, submete a requisição de autenticação ao serviço e conduz o fluxo subsequente conforme a decisão retornada. |
| **Servidor Web / Reverse Proxy** | Interno | `HTTPS` (Externa) e `HTTP` (Rede Interna Isolada) | Ponto de entrada de rede do sistema. Recebe conexões externas seguras, executa a terminação criptográfica TLS (ADR01) e encaminha as requisições HTTP para a aplicação em rede interna controlada (MDN WEB DOCS, s.d.-b). |
| **Aplicação de Autenticação** | Interno | `HTTP / JSON` e `TCP (SGBD)` | Contêiner de aplicação *stateless* (ADR03) que expõe a interface HTTP interna, valida os dados recebidos, coordena a consulta ao usuário e a verificação da senha, e emite o resultado da autenticação. |
| **Base de Usuários** | Externo | `TCP / Driver SGBD` (Somente Leitura) | Repositório preexistente (PRE01) que armazena de forma persistente os registros de identificação e os hashes de senhas dos usuários. |

*Fonte: Elaborada pelo autor com base no [Diagrama C4 de Contêineres](../../diagramas/c4/conteineres.puml).*

### 2.2. Componentes e Responsabilidades

No interior do contêiner **Aplicação de Autenticação**, os módulos de código organizam-se conforme o [Diagrama C4 de Componentes](../../diagramas/c4/componentes.puml). As portas (`AuthenticationUseCasePort`, `UserRepositoryPort` e `PasswordVerifierPort`) correspondem a contratos abstratos de comunicação entre componentes dentro do mesmo espaço de endereçamento, não constituindo unidades independentes de execução (COCKBURN, 2005). A Tabela 3 define as responsabilidades e as dependências estáticas de cada componente.

**Tabela 3 — Especificação dos Componentes Internos da Aplicação (C4 Nível 3)**

| Componente | Classificação Arquitetural | Dependências Declaradas | Responsabilidade Técnica Detalhada |
| :--- | :--- | :--- | :--- |
| **`AuthHttpController`** | Adaptador Primário (Entrada HTTP) | `AuthenticationUseCasePort` | Recebe a requisição HTTP/JSON encaminhada pelo Reverse Proxy, valida a presença e o formato dos campos obrigatórios, aciona a porta de entrada e converte o resultado ou exceções nas respostas HTTP previstas (`200`, `400`, `401`, `503` ou `500`). |
| **`AuthenticationUseCasePort`** | Porta de Entrada (Interface) | Nenhuma (Contrato do Núcleo) | Define a assinatura abstrata de invocação do caso de uso de verificação de credenciais, isolando o controlador HTTP da implementação do serviço. |
| **`AuthenticationService`** | Serviço de Aplicação / Caso de Uso | `UserRepositoryPort`, `PasswordVerifierPort` | Implementa `AuthenticationUseCasePort`. Consulta o usuário, solicita a verificação da senha (aplicando verificação com hash fictício pré-configurado quando o identificador não existe) e determina a aprovação ou rejeição da autenticação. |
| **`UserRepositoryPort`** | Porta de Saída (Interface) | Nenhuma (Contrato do Núcleo) | Define a operação abstrata de busca de usuário por identificador, desacoplando o núcleo da tecnologia de banco de dados. |
| **`PasswordVerifierPort`** | Porta de Saída (Interface) | Nenhuma (Contrato do Núcleo) | Define a operação abstrata de verificação criptográfica de equivalência entre a senha informada e o hash armazenado. |
| **`DatabaseUserRepositoryAdapter`** | Adaptador Secundário (Persistência) | `UserRepositoryPort` e Driver SGBD | Implementa `UserRepositoryPort`. Executa a consulta somente leitura na Base de Usuários externa e converte falhas de conexão ou *timeout* em erros de infraestrutura reconhecíveis pela aplicação. |
| **`Argon2PasswordVerifierAdapter`** | Adaptador Secundário (Criptografia) | `PasswordVerifierPort` e Biblioteca Argon2id | Implementa `PasswordVerifierPort`. Executa a verificação segura de senhas utilizando o algoritmo Argon2id a partir dos parâmetros de custo e *salt* registrados no próprio hash. |

*Fonte: Elaborada pelo autor com base no [Diagrama C4 de Componentes](../../diagramas/c4/componentes.puml).*

---

## 3. Interfaces e Comportamento

### 3.1. Contratos das Portas

Para assegurar a consistência na integração entre os componentes e orientar a implementação e os testes automatizados (IEEE COMPUTER SOCIETY, 2024), esta subseção formaliza os contratos das interfaces internas e das estruturas de dados do núcleo da aplicação. Adota-se a convenção de tipos `String`, `Boolean`, `Throwable` e `Optional<T>` (indicando valor opcional que pode estar presente ou ausente/vazio).

O contrato externo HTTP/JSON (`POST /auth/verify`) permanece regido pelas definições da [Especificação de Requisitos](../02-arquitetura/especificacao-requisitos.md), cabendo ao `AuthHttpController` realizar o mapeamento explícito entre os nomes internos do núcleo (como `userId` em `AuthDecisionResult`) e os campos externos do payload JSON (como `user.id` na resposta `HTTP 200 OK`), conforme detalhado nas Tabelas 4 e 5.

**Tabela 4 — Estruturas de Dados e Exceções de Contrato do Núcleo**

| Estrutura ou Tipo | Atributos e Tipagem | Semântica e Regras de Integridade |
| :--- | :--- | :--- |
| **`AuthCredentialsInput`** | `identifier: String`; `password: String` | Dados de entrada fornecidos à porta do caso de uso. Pré-condição: ambos os atributos devem ser não nulos, não vazios e validados sintaticamente pelo adaptador primário. |
| **`UserRecord`** | `id: String`; `identifier: String`; `passwordHash: String` | Entidade de leitura retornada pelo repositório quando o identificador é localizado. `passwordHash` contém a representação completa do Argon2id (algoritmo, versão, parâmetros de custo, *salt* e resumo). |
| **`AuthDecisionResult`** | `authenticated: Boolean`; `userId: Optional<String>`; `identifier: Optional<String>` | Resultado do caso de uso. Quando `authenticated == true`, `userId` (recebendo `userRecord.id`) e `identifier` estão obrigatoriamente presentes (RF05). Quando `authenticated == false`, ambos permanecem vazios (`Optional.empty`), sem expor o motivo da rejeição (RF06). |
| **`PersistenceInfrastructureError`** | `message: String`; `cause: Throwable` | Exceção técnica lançada pelo adaptador de persistência diante de indisponibilidade, recusa de conexão ou *timeout* na Base de Usuários (RF08). Seus atributos internos (`message` e `cause`) destinam-se apenas a diagnóstico interno e jamais são serializados para o cliente; o `AuthHttpController` converte a falha em resposta `HTTP 503` genérica. |
| **`CryptographicVerificationError`** | `message: String`; `cause: Throwable` | Exceção técnica interna lançada pelo adaptador criptográfico caso o hash armazenado/configurado esteja corrompido ou ocorra falha inesperada no motor Argon2id. Não é exposta ao cliente, sendo convertida pelo `AuthHttpController` em resposta `HTTP 500 Internal Server Error` genérica. |

*Fonte: Elaborada pelo autor.*

**Tabela 5 — Especificação Formal das Operações nas Portas da Arquitetura Hexagonal**

| Porta (Interface) | Operação | Parâmetros de Entrada | Retorno Esperado | Pré-condições e Pós-condições | Exceções e Condições de Erro |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **`AuthenticationUseCasePort`** | `verifyCredentials` | `input: AuthCredentialsInput` | `AuthDecisionResult` | **Pré:** `identifier` e `password` validados sintaticamente. **Pós:** Retorna a decisão de autenticação e, em caso positivo, a identidade pública do usuário (`userId` e `identifier`), sem manter estado de sessão entre requisições. | Propaga `PersistenceInfrastructureError` (falha de acesso ao banco, CA05) ou `CryptographicVerificationError` (falha interna inesperada na verificação). |
| **`UserRepositoryPort`** | `findByIdentifier` | `identifier: String` | `Optional<UserRecord>` | **Pré:** `identifier` não vazio. **Pós:** Executa leitura sem alterar registros na base externa (CON01/PRE01), retornando `Optional.of(UserRecord)` se encontrado ou `Optional.empty` se inexistente. | Lança `PersistenceInfrastructureError` se ocorrer falha de comunicação, erro de driver ou *timeout* no SGBD (RF08). |
| **`PasswordVerifierPort`** | `verify` | `rawPassword: String`; `encodedHash: String` | `Boolean` | **Pré:** `encodedHash` em formato Argon2id válido (do registro real ou do hash fictício pré-configurado). **Pós:** Retorna `true` se a senha corresponder ao hash e `false` se divergir; a senha original não é persistida nem registrada em log. | Retorna `false` para senha incorreta (tratada como credencial inválida, `401`). Lança `CryptographicVerificationError` apenas se `encodedHash` estiver corrompido ou inválido (tratado como erro interno, `500`). |

*Fonte: Elaborada pelo autor.*

### 3.2. Fluxos de Autenticação e Tratamento de Erros

O comportamento dinâmico dos componentes durante o processamento de uma requisição, modelado no [Diagrama de Sequência UML](../../diagramas/uml/sequencia-autenticacao.puml), estrutura-se em três etapas sequenciais que cobrem os cinco critérios de aceitação principais (CA01 a CA05) e o tratamento de falhas internas inesperadas:

1. **Etapa 1 — Recebimento e Validação Sintática na Borda HTTP (`CA04`):** A requisição `POST /auth/verify` atravessa o **Servidor Web / Reverse Proxy** e alcança o `AuthHttpController` (RF01). O controlador verifica imediatamente a validade estrutural do JSON e o preenchimento dos atributos `identifier` e `password` (RF02). Caso a entrada seja inválida (**CA04**), o fluxo é encerrado no próprio adaptador primário com o retorno de `HTTP 400 Bad Request` (`VALIDATION_ERROR`, RF07), impedindo qualquer invocação ao `AuthenticationService` ou ao banco de dados.
2. **Etapa 2 — Consulta ao Repositório e Isolamento de Falha de Persistência (`CA05`):** Sendo a entrada válida, o controlador invoca `verifyCredentials` na porta `AuthenticationUseCasePort`. O `AuthenticationService` aciona `UserRepositoryPort.findByIdentifier`, executada pelo `DatabaseUserRepositoryAdapter` junto à **Base de Usuários** (RF03). Caso ocorra falha de conexão ou *timeout* (**CA05**), o adaptador intercepta a falha nativa do SGBD e lança `PersistenceInfrastructureError`, interrompendo o caso de uso antes da etapa de verificação de senha. O erro é propagado pelo serviço e convertido pelo `AuthHttpController` em uma resposta `HTTP 503 Service Unavailable` (`SERVICE_UNAVAILABLE`, RF08) genérica, sem serializar a exceção ou sua causa (`cause`), garantindo que falhas operacionais do banco não sejam confundidas com credenciais inválidas nem exponham detalhes internos de infraestrutura.
3. **Etapa 3 — Verificação Criptográfica e Decisão de Autenticação (`CA01`, `CA02`, `CA03` e Erro Interno):** Concluída a leitura no repositório sem falha técnica, o `AuthenticationService` avalia o retorno:
   * **Caminho de Usuário Inexistente (`CA02`):** Quando `findByIdentifier` retorna `Optional.empty`, o `AuthenticationService` mitiga diferenças temporais observáveis (ADR01) acionando igualmente `PasswordVerifierPort.verify`, passando a senha recebida e um **hash Argon2id fictício válido, previamente configurado (`DUMMY_HASH`)**, de custo computacional equivalente aos hashes reais. O retorno booleano dessa verificação fictícia é ignorado, o serviço devolve `AuthDecisionResult(authenticated = false, userId = Optional.empty, identifier = Optional.empty)` e o controlador responde com `HTTP 401 Unauthorized` (`INVALID_CREDENTIALS`, RF06).
   * **Caminho de Usuário Existente (`CA01` e `CA03`):** Quando o registro `UserRecord` é encontrado, o serviço aciona `PasswordVerifierPort.verify(password, userRecord.passwordHash)` (RF04), executada pelo `Argon2PasswordVerifierAdapter`. Se o retorno for `true` (**CA01**), o serviço emite `AuthDecisionResult(authenticated = true, userId = Optional.of(userRecord.id), identifier = Optional.of(userRecord.identifier))` e o controlador o mapeia para `HTTP 200 OK` com o objeto JSON `{ "status": "AUTHENTICATED", "user": { "id": "...", "identifier": "..." } }` (RF05). Se o retorno for `false` (**CA03**), o serviço emite `AuthDecisionResult(authenticated = false, userId = Optional.empty, identifier = Optional.empty)` e o controlador retorna `HTTP 401 Unauthorized` com exatamente o mesmo contrato JSON e código de status do cenário **CA02** (RF06).
   * **Tratamento Excepcional de Falha Interna na Verificação:** Caso `PasswordVerifierPort.verify` lance a exceção `CryptographicVerificationError` (por exemplo, diante de um hash corrompido na base externa ou falha interna inesperada da biblioteca criptográfica), essa falha não é classificada como senha incorreta (`401`) nem como indisponibilidade do banco de dados (`503`). O erro é propagado até o `AuthHttpController`, que retorna uma resposta genérica `HTTP 500 Internal Server Error` sem divulgar detalhes técnicos ao cliente.

---

## 4. Decisões e Rastreabilidade

### 4.1. Decisões Arquiteturais e Requisitos de Qualidade

A Tabela 6 sintetiza as decisões arquiteturais (ADR01 a ADR04) adotadas para endereçar os atributos de qualidade (RNF01 a RNF04), explicitando os mecanismos técnicos incorporados aos componentes e as limitações conhecidas (*trade-offs*) de cada escolha (IEEE COMPUTER SOCIETY, 2024).

**Tabela 6 — Síntese das Decisões Arquiteturais, Qualidades e Limitações Conhecidas**

| Decisão e Requisito | Mecanismo Arquitetural Adotado | Componentes Envolvidos | Trade-offs e Limitações Conhecidas |
| :--- | :--- | :--- | :--- |
| **ADR01 — Segurança de Credenciais** *(RNF01)* | Terminação TLS no Reverse Proxy; tráfego interno em rede isolada; verificação via Argon2id (`verify`); respostas `401` idênticas para CA02 e CA03; verificação com hash fictício pré-configurado quando o usuário não existe; vedação de senhas e exceções internas em logs e respostas. | `Servidor Web`, `AuthenticationService`, `Argon2PasswordVerifierAdapter`, `AuthHttpController` | O uso de hash fictício no CA02 consome CPU deliberadamente em tentativas com usuários inexistentes e reduz diferenças temporais observáveis, embora não garanta tempos de resposta idênticos devido a variações de rede e SGBD. |
| **ADR02 — Interoperabilidade HTTP/JSON** *(RNF02)* | Exposição de endpoint `POST /auth/verify` com payloads JSON padronizados e mapeamento semântico para `200`, `400`, `401`, `503` (e `500` para falhas internas inesperadas) (MDN WEB DOCS, s.d.-b). | `AuthHttpController` | Restringe o protocolo de entrada a clientes compatíveis com HTTP/JSON, exigindo a criação de um novo adaptador primário caso outros protocolos sejam demandados no futuro. |
| **ADR03 — Execução Stateless e Concorrência** *(RNF03)* | Decisões projetadas para favorecer o comportamento operacional concorrente: processamento isolado sem retenção de estado de sessão entre requisições, controle de *timeout* na leitura do banco e conversão imediata de falhas. | `AuthenticationService`, `DatabaseUserRepositoryAdapter` | Como o serviço não mantém cache de sessão nem emite tokens (CON01), toda requisição de verificação exige consulta ao repositório e cálculo do Argon2id. O atendimento efetivo às metas de desempenho, latência e capacidade depende de verificação prática por testes de carga e medições. |
| **ADR04 — Desacoplamento Hexagonal** *(RNF04)* | Inversão de dependências por meio das portas `AuthenticationUseCasePort`, `UserRepositoryPort` e `PasswordVerifierPort` (COCKBURN, 2005). | Todos os componentes da `Aplicação de Autenticação` | Introduz interfaces e abstrações adicionais em comparação a uma organização com dependências diretas entre componentes, em troca de maior isolamento tecnológico, testabilidade e facilidade de manutenção. |

*Fonte: Elaborada pelo autor com base na [Especificação de Requisitos](../02-arquitetura/especificacao-requisitos.md).*

### 4.2. Matriz de Rastreabilidade

A Tabela 7 consolida as relações entre os requisitos funcionais (RF01 a RF08), os requisitos não funcionais e decisões arquiteturais (RNF01/ADR01 a RNF04/ADR04), os critérios de aceitação (CA01 a CA05), os componentes responsáveis e os artefatos de modelagem do repositório (IEEE COMPUTER SOCIETY, 2024).

**Tabela 7 — Matriz de Rastreabilidade Arquitetural**

| Requisito | Descrição Sintética | Componentes Responsáveis | Critérios de Aceitação | Decisões e RNFs Relacionados | Artefatos de Modelagem Associados |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **RF01** | Receber credenciais via requisição HTTP | `Servidor Web`, `AuthHttpController` | CA01 a CA05 | RNF01/ADR01, RNF02/ADR02, CON02 | `contexto.puml`, `conteineres.puml`, `componentes.puml`, `sequencia-autenticacao.puml` |
| **RF02** | Validar sintaxe e campos obrigatórios de entrada | `AuthHttpController` | CA04 | RNF02/ADR02 | `componentes.puml`, `sequencia-autenticacao.puml` |
| **RF03** | Consultar existência do usuário no repositório | `AuthenticationService`, `UserRepositoryPort`, `DatabaseUserRepositoryAdapter` | CA01, CA02, CA03, CA05 | RNF03/ADR03, RNF04/ADR04, CON01, PRE01 | `contexto.puml`, `conteineres.puml`, `componentes.puml`, `sequencia-autenticacao.puml` |
| **RF04** | Verificar senha utilizando o hash armazenado (ou fictício) | `AuthenticationService`, `PasswordVerifierPort`, `Argon2PasswordVerifierAdapter` | CA01, CA02, CA03 | RNF01/ADR01, RNF04/ADR04 | `componentes.puml`, `sequencia-autenticacao.puml` |
| **RF05** | Retornar sucesso com a identificação do usuário | `AuthenticationService` (decisão), `AuthHttpController` (HTTP 200) | CA01 | RNF02/ADR02 | `contexto.puml`, `componentes.puml`, `sequencia-autenticacao.puml` |
| **RF06** | Retornar erro genérico para credenciais inválidas | `AuthenticationService` (rejeição), `AuthHttpController` (HTTP 401) | CA02, CA03 | RNF01/ADR01, RNF02/ADR02 | `contexto.puml`, `componentes.puml`, `sequencia-autenticacao.puml` |
| **RF07** | Retornar erro de validação para entrada inválida | `AuthHttpController` (HTTP 400) | CA04 | RNF02/ADR02 | `componentes.puml`, `sequencia-autenticacao.puml` |
| **RF08** | Tratar e retornar erro técnico de falha de infraestrutura | `DatabaseUserRepositoryAdapter`, `AuthenticationService`, `AuthHttpController` (HTTP 503) | CA05 | RNF02/ADR02, RNF03/ADR03 | `componentes.puml`, `sequencia-autenticacao.puml` |

*Fonte: Elaborada pelo autor.*