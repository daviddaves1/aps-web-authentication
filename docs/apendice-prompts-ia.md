# Apêndice I — Registro dos Prompts de Inteligência Artificial

## I.1. Identificação da Ferramenta

- **Ferramenta:** ChatGPT
- **Organização:** OpenAI
- **Endereço eletrônico:** https://chatgpt.com/
- **Finalidade:** Apoio metodológico, revisão técnica, verificação de consistência e análise dos artefatos da APS.

## I.2. Prompts Utilizados

Este apêndice apresenta uma seleção dos principais prompts utilizados durante o desenvolvimento da APS, priorizando as interações que contribuíram diretamente para a estruturação da documentação, a especificação dos requisitos, a modelagem arquitetural e a revisão dos artefatos.

### I.2.1. Organização da Documentação

**Artefatos relacionados:** Estrutura documental e organização do repositório.

**Prompt original:**

> `Olá, quero que analise muito bem a fundo e com extrema atenção o que o trabalho pede. Preciso realizar a documentação de algo relacionado a Web e uma documentação inteira, nisso o trabalho deverá ser armazenado no GitHub além de commites serem frequentes. Me informe o que devo fazer passo a passo. Eu disponibilizei também o capítulo do Swebook que fala sobre a arquitetura, acredito que pode ajudar.`

**Contribuição obtida:** Orientações para organizar a documentação conforme as partes exigidas na APS.

> `Quero que analise esses 2 docs e leia os capitulos 6.4.2 e 6.4.3 da norma 15288. Após isso, avalie como elas se relacionam com o trabalho feito até agora. Issó é pelo fato de que eu refiz o nosso documento adaptando-o conforme o Swebok-v4 e a norma ISO 15288. O novo documento que remodelei está em formato .docx`

### I.2.2. Especificação de Requisitos

**Artefato relacionado:** `especificacao-requisitos.md`

**Prompt original:**

> `Certo, vamos começar os proximos passos, se forem os requisitos, o cuidado deve ser redobrado e seguir uma extrutura muito bem estabelecida. Eu já possuo um documento extruturado para isso, mas ele não está 100% completo, então preciso que você me ajude a complementá-lo.`

> `Você não acha que esses requisitos estão grandes e detalhados demais? Tenho a impressão que ocorreu uma fusão nos requisitos, geralmente eu costumo vê-los mais fragmentados, mas posso estar 100% enganado.`

**Contribuição obtida:** Identificação de inconsistências e sugestões de melhoria na especificação.

### I.2.3. Esboço e desenvolvimento dos diagramas

**Artefatos relacionados:** sequencia-autenticacao.puml e conteineres.puml

**Prompt original:**

> `Para o próximo diagrama, vamos detalhar como o sistema de autenticação funciona por dentro, que é nível de Containers. Precisamos incluir os seguintes elementos: Servidor Web/Reverse Proxy: vai receber as conexões Https e repassar as requisições para a aplicação, seguindo o que pede a restrição CON02; Aplicação de Autenticação: onde rodará as regras de negócio e que expõe a interface HTTP; Base de Usuários externa: continua de fora da nossa fronteira arquitetural por enquanto; Aplicação Cliente: segue como um sistema externo que consome o serviço.`

> `Sobre o escopo dos fragmentos alt (prioridade alta): este é o ponto mais importante para ajustarmos antes de finalizar esse diagrama, pois notei um problema na estrutura. Na primeira etapa, você define que uma entrada inválida retorna HTTP 400 e uma entrada válida prossegue, mas como o fragmento alt fecha antes da etapa 2, o diagrama não garante formalmente que a consulta ao repositório vai acontecer apenas se a entrada for válida. O mesmo problema ocorre no cenário CA05, onde a etapa de verificação criptográfica ficou fora do fragmento alternativo após o retorno do HTTP 503, o que me passa impressão de que o sistema continua processando a requisição mesmo depois de uma falha na infraestrutura.`

**Contribuição obtida:** Correção técnica sobre a modelagem arquitetural.

### I.2.4. Verificação Final dos Artefatos

**Artefatos relacionados:** Documentação, referências e diagramas.

**Prompt original:**

> `Certo, o diagrama C4 de containeres foi corrigido conforme você pediu. Agora temos o de componentes, peço que analise esse com mais cuidado, está mais complexo.`

**Contribuição obtida:** Conferência de rastreabilidade, referências, links e coerência entre os artefatos.

> `Aqui estão as referencias, eles tiveram alterações.`