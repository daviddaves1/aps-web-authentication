# Serviço de Autenticação Web

**Documentação Técnica e Arquitetural — APS**

Este repositório reúne os artefatos desenvolvidos para a Atividade Prática Supervisionada (APS) da disciplina de **Domínios de Software**, do curso de Engenharia de Software da Universidade Federal de Goiás (UFG).

O projeto tem como objetivo documentar o ambiente operacional Web e especificar a arquitetura de um serviço responsável pela verificação de credenciais de usuários, considerando requisitos de software, decisões arquiteturais, segurança e organização dos componentes.

## 1. Objetivo do Projeto

Descrever e estruturar a arquitetura de um serviço de autenticação Web capaz de:

- Receber credenciais de identificação e senha.
- Validar os dados enviados pelo cliente.
- Consultar usuários em uma base de dados externa preexistente.
- Verificar a senha informada em relação ao hash armazenado.
- Retornar o resultado da autenticação por meio de respostas HTTP padronizadas.

O escopo está limitado à **verificação de credenciais**, não contemplando cadastro de usuários, recuperação de senhas, gerenciamento de sessões, emissão de tokens ou controle de permissões.

## 2. Arquitetura Adotada

O projeto utiliza a **Arquitetura Hexagonal (Ports and Adapters)**, com o objetivo de separar as regras de negócio das tecnologias de infraestrutura.

Os principais componentes especificados são:

| Componente | Responsabilidade |
|---|---|
| `AuthHttpController` | Receber requisições HTTP e retornar respostas padronizadas. |
| `AuthenticationUseCasePort` | Definir o contrato de entrada do caso de uso. |
| `AuthenticationService` | Coordenar o processo de verificação de credenciais. |
| `UserRepositoryPort` | Definir o contrato de consulta aos usuários. |
| `PasswordVerifierPort` | Definir o contrato de verificação criptográfica. |
| `DatabaseUserRepositoryAdapter` | Consultar a base externa de usuários em modo somente leitura. |
| `Argon2PasswordVerifierAdapter` | Verificar senhas utilizando Argon2id. |

A comunicação externa é especificada por meio de HTTPS e JSON, utilizando o endpoint `POST /auth/verify`.

## 3. Organização do Repositório

```text
aps-web-authentication/
├── docs/
│   ├── 01-ambiente-web/
│   │   ├── fundamentos.md
│   │   └── componentes-operacionais.md
│   ├── 02-arquitetura/
│   │   ├── especificacao-requisitos.md
│   │   └── descricao-arquitetural.md
│   ├── referencias.md
│   ├── registro-uso-ia.md
│   └── apendice-prompts-ia.md
├── diagramas/
│   ├── c4/
│   │   ├── contexto.puml
│   │   ├── conteineres.puml
│   │   └── componentes.puml
│   └── uml/
│       └── sequencia-autenticacao.puml
└── README.md
```

## 4. Diagramas e Modelagem

A documentação contém quatro diagramas elaborados em PlantUML:

1. **C4 — Contexto:** apresenta o serviço e suas interações com os elementos externos.
2. **C4 — Contêineres:** representa a organização dos componentes executáveis e suas comunicações.
3. **C4 — Componentes:** detalha a estrutura interna da aplicação segundo a Arquitetura Hexagonal.
4. **UML — Sequência:** descreve o fluxo de verificação de credenciais, incluindo os cenários de sucesso e falha.

Os arquivos-fonte dos diagramas estão disponíveis no diretório `diagramas/`.

## 5. Referências e Fundamentação

A elaboração da documentação considera conceitos e práticas apresentados em referências como:

- SWEBOK v4.0.
- ISO/IEC/IEEE 29148 — Engenharia de Requisitos.
- ISO/IEC/IEEE 42010 — Descrição Arquitetural.
- ISO/IEC/IEEE 12207 — Processos do Ciclo de Vida de Software.
- Modelo C4 e Arquitetura Hexagonal.
- Recomendações de segurança da OWASP.

As referências bibliográficas utilizadas estão registradas em [`docs/referencias.md`](docs/referencias.md).

## 6. Utilização de Inteligência Artificial

O ChatGPT (OpenAI) foi utilizado como ferramenta de apoio na organização, elaboração e revisão de partes da documentação, incluindo auxílio na modelagem arquitetural.

A participação da ferramenta está descrita nos seguintes arquivos:

- [`docs/registro-uso-ia.md`](docs/registro-uso-ia.md)
- [`docs/apendice-prompts-ia.md`](docs/apendice-prompts-ia.md)

As decisões finais e a responsabilidade pelo conteúdo acadêmico permanecem com o autor.

## 7. Informações Acadêmicas

**Instituição:** Universidade Federal de Goiás (UFG)  
**Curso:** Engenharia de Software  
**Disciplina:** Domínios de Software  
**Atividade:** Atividade Prática Supervisionada (APS)  
**Autor:** David Daves Ferreira Pinto  
**Docente responsável:** Profa. Adriana Silveira de Souza  
**Período:** 2026/2

---

**Observação:** Este repositório tem finalidade acadêmica e documental. Seu objetivo é especificar o ambiente operacional e a arquitetura do serviço de autenticação Web, não disponibilizar uma implementação funcional do sistema.