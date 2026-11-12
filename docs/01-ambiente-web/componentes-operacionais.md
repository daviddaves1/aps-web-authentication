# Componentes Operacionais do Ambiente Web

## 1. Navegador Web

No ambiente da internet, há diversos serviços que fazem a comunicação coexistir a ponto de compartilhar dados ininterruptamente. São necessárias infraestruturas inteiras, como servidores, satélites, cabos, roteadores, para que os dados trafeguem por essa rede de forma eficaz. Contudo, essa trafegabilidade geralmente ocorre mediante ao uso de softwares que intermediam a comunicação diretamente entre o cliente e servidor. Os mais usados são os navegadores web (como Google Chrome, Safari e Firefox).
Diferente dos motores de busca (como o Google ou Bing), que possuem funções de rastreabilidade e indexação sobre as páginas da internet, o papel do navegador é designado a requisitar, carregar, processar e renderizar as páginas web para a interação com o usuário. Sempre que um site é acessado, o navegador solicita os arquivos ao servidor e interpreta códigos em HTML (que estrutura o conteúdo), CSS (que define o design visual) e JavaScript (responsável pelo comportamento dinâmico e pela comunicação de dados de volta ao servidor). É através dessa interpretação visual que a maior parte dos usuários consome e interage com os dados na internet.

A documentação da MDN Web Docs descreve o navegador como o software responsável por solicitar, interpretar e apresentar recursos da Web, em comunicação com servidores por meio de protocolos de rede (MDN WEB DOCS, s.d.-a).

### 1.1. Responsabilidades

O navegador possui 4 tipos de responsabilidades específicas que garantem que tudo funcione bem entre quem usa a tela e os sistemas que guardam os sites:

**Comunicação e rede:** Ele participa do processo de resolução de nomes (em conjunto com serviços do sistema operacional e servidores DNS) e estabelece conexões com os servidores para solicitar e receber os arquivos nos endereços corretos.

**Leitura e desenho da página:** O navegador interpreta os recursos recebidos do site, como textos, imagens e estilos, e transforma tudo isso na página organizada e visual que vemos na tela.

**Memória e armazenamento:** Ele guarda pequenas informações no seu aparelho, como histórico, senhas e arquivos temporários, para que os sites carreguem mais rápido na próxima visita.

**Segurança e proteção:** Ele valida certificados TLS para autenticar a conexão segura com o servidor (embora isso não garanta, por si só, que o conteúdo do site seja legítimo), bloqueia arquivos suspeitos e mantém cada aba isolada para que um problema em uma página não comprometa o restante do sistema.

### 1.2. Funcionamento e processamento

Quando você digita um endereço e pressiona enter, o navegador segue um passo a passo rápido para renderizar a página no dispositivo do usuário:

**Busca do endereço:** Primeiro, o navegador procura onde o site está hospedado na rede e abre um canal seguro de conversa com aquele servidor.

**Pedido e entrega:** Com a conexão aberta, ele pede os arquivos da página, e o servidor começa a enviar os documentos de volta, começando pela estrutura básica em texto.

**Organização do conteúdo:** O navegador lê o código recebido e separa o que é texto e estrutura do que é cor, fonte e estilo visual.

**Montagem na tela:** Ele junta essas informações, calcula o tamanho e a posição de cada bloco e pinta os elementos na janela do seu aparelho.

**Ações e movimentos:** Scripts responsáveis pela interatividade da página, permitindo o funcionamento de botões, a reprodução de mídias e a abertura de menus de maneira dinâmica, sem exigir a atualização total da tela.

### 1.3. Papel do Navegador na Autenticação

Quando é preciso entrar em uma conta na internet, o navegador atua como intermediário na comunicação entre o usuário e o servidor responsável pela autenticação. Todo esse processo acontece em cinco passos simples e rápidos:

**Exibição do formulário:** Primeiro, o navegador interpreta o código da página e exibe na tela os campos destinados às suas credenciais, acompanhados do botão de confirmação.

**Leitura das informações:** Quando você insere seu identificador e a senha, o navegador retém esses dados temporariamente na página, ocultando visualmente os caracteres da senha para dificultar sua observação por terceiros.

**Envio protegido:** Ao confirmar o acesso, o navegador reúne essas informações e as encaminha pela rede por meio de uma conexão criptografada. Esse recurso protege os dados durante o trajeto para proteger a confidencialidade e a integridade das informações durante a transmissão.

**Recebimento da resposta:** O servidor verifica as credenciais recebidas e retorna ao navegador a identificação do usuário, caso sejam válidas, ou uma resposta de erro quando a autenticação falhar.

**Exibição do resultado:** Por fim, o navegador interpreta a resposta recebida e apresenta o resultado da autenticação ao usuário, informando se a operação foi realizada com sucesso ou se houve alguma falha.

## 2. Servidor Web

Enquanto o navegador atua na ponta do usuário solicitando recursos, o servidor web opera na outra extremidade como o sistema encarregado de armazenar, processar e disponibilizar os arquivos de um site. Trata-se de uma combinação entre infraestrutura física de alta capacidade e softwares especializados que permanecem em operação contínua para atender aos pedidos vindos da rede mundial de computadores.

A MDN Web Docs diferencia o servidor Web como software e infraestrutura que recebem requisições HTTP e fornecem recursos aos clientes, diretamente ou por meio de aplicações (MDN WEB DOCS, s.d.-b).

### 2.1. Responsabilidades

O servidor web desempenha 4 atribuições fundamentais para assegurar a disponibilidade e a integridade das aplicações online:

**Recepção e atendimento de solicitações:** Ele monitora constantemente as portas de comunicação para receber os pedidos enviados pelos navegadores e entregar os arquivos correspondentes, como documentos HTML, imagens e folhas de estilo.

**Gerenciamento de conexões simultâneas:** Ele administra o tráfego de múltiplos usuários ao mesmo tempo, distribuindo os recursos de processamento e memória para evitar lentidão ou interrupções no serviço.

**Controle de acesso e segurança:** Ele configura e utiliza certificados digitais TLS (apresentando seu certificado ao cliente durante o estabelecimento da conexão HTTPS e realizando a terminação criptográfica), além de aplicar regras de controle para impedir o acesso não autorizado a diretórios restritos.

**Registro e auditoria de atividades:** Ele mantém históricos detalhados sobre cada acesso realizado, registrando endereços de origem, horários e eventuais falhas para fins de monitoramento e manutenção técnica.

### 2.2. Recebimento e encaminhamento de requisições

Quando um pedido chega pela rede, o servidor web executa uma sequência estruturada de triagem e direcionamento antes de devolver uma resposta ao navegador:

**Escuta e validação da conexão:** O software do servidor identifica a chegada de um pedido através da rede, verifica a integridade do protocolo utilizado e estabelece o canal seguro de comunicação.

**Análise do pedido recebido:** Ele examina o cabeçalho da requisição para identificar qual recurso está sendo solicitado e qual método HTTP está sendo empregado pelo cliente.

**Triagem entre conteúdo estático e dinâmico:** Caso o pedido se refira a um arquivo pronto, como uma imagem ou documento fixo, o próprio servidor localiza o item em seu armazenamento. Se a solicitação exigir processamento de dados, como uma consulta ao banco de dados ou uma validação de acesso, ele atua como *Reverse Proxy* e encaminha o pedido para a aplicação responsável pelo processamento lógico.

**Consolidação e envio da resposta:** A atribuição do código de status HTTP pode pertencer ao servidor Web (para recursos estáticos ou erros de gateway) ou à própria aplicação. No Serviço de Autenticação Web proposto, o adaptador HTTP da aplicação (`AuthHttpController`) determina os códigos de status (`200`, `400`, `401`, `500` e `503`) conforme o resultado da autenticação, cabendo ao servidor Web (*Reverse Proxy*) encaminhar a resposta estruturada de volta ao navegador.


## 3. Servidor de Aplicação

Diferente do servidor web, que concentra sua atuação na entrega de arquivos prontos e no gerenciamento das conexões de rede, o servidor de aplicação constitui o núcleo lógico do sistema. É nesse ambiente que as regras de negócio são executadas, permitindo que as páginas respondam de forma dinâmica e personalizada às ações de cada usuário.

Essa separação de responsabilidades pode ser compreendida a partir dos princípios de organização arquitetural apresentados no SWEBOK, especialmente quanto à definição de componentes e suas interações (IEEE COMPUTER SOCIETY, 2024).

### 3.1. Responsabilidades

O servidor de aplicação desempenha 4 funções essenciais para o processamento das funcionalidades das aplicações:

**Execução das regras de negócio:** Ele processa os códigos da aplicação, realiza cálculos, aplica validações e determina o comportamento do sistema diante de cada solicitação recebida.

**Integração com bancos de dados:** Ele estabelece a comunicação com os sistemas de armazenamento para consultar, inserir, atualizar ou excluir registros conforme a necessidade da operação.

**Gerenciamento de estados:** Dependendo da arquitetura adotada, servidores de aplicação podem manter informações de sessão e contexto dos usuários. Entretanto, o Serviço de Autenticação Web proposto opera de forma estritamente *stateless* (sem estado de sessão entre requisições), conforme estabelecido na decisão arquitetural ADR03.

**Comunicação entre serviços:** Ele atua como ponto de integração com sistemas externos, como plataformas de pagamento, serviços de envio de mensagens ou interfaces de consulta de dados.

### 3.2. Processamento da autenticação

Quando o servidor web encaminha um pedido de autenticação, o servidor de aplicação conduz a verificação das credenciais para verificar as credenciais do solicitante em etapas rigorosas:

**Recepção e extração das credenciais:** O servidor recebe os dados encaminhados pela camada web, valida a presença e o formato dos campos obrigatórios e extrai o identificador e a senha fornecidos no pedido.

**Consulta e verificação criptográfica:** O servidor de aplicação consulta o registro do usuário e utiliza o algoritmo Argon2id para verificar a correspondência entre a senha informada e o hash armazenado, sem persistir ou registrar a senha original (executando verificação contra um hash fictício pré-configurado caso o identificador não seja localizado, conforme a decisão ADR01).

**Decisão de autenticação:** O escopo do serviço restringe-se exclusivamente à verificação das credenciais (CON01), sem avaliar status cadastral da conta, bloqueios por tentativas ou permissões de acesso. O serviço consolida a decisão considerando unicamente a existência do registro correspondente ao identificador informado e a validade criptográfica da senha verificada.

**Retorno do resultado da autenticação:** Caso as credenciais sejam válidas, o servidor de aplicação retorna a identificação pública do usuário (`id` e `identifier`). Caso sejam inválidas, retorna uma decisão de rejeição, que será convertida em uma resposta HTTP genérica (`HTTP 401`), sem revelar se o identificador existe na base de dados e sem emitir identificadores de sessão ou tokens de acesso persistentes (CON01).

## 4. Banco de Dados

O banco de dados constitui a camada permanente de memória de um sistema, sendo o repositório estruturado onde as informações são registradas e organizadas. Enquanto os servidores web e de aplicação atuam no tráfego e no processamento temporário das requisições, esse ambiente dispõe de mecanismos voltados à preservação da integridade, consistência e disponibilidade dos dados essenciais ao funcionamento da aplicação.

As práticas de armazenamento de senhas devem considerar mecanismos de hash específicos para essa finalidade, conforme as recomendações de segurança da OWASP (OWASP FOUNDATION, s.d.).

### 4.1. Armazenamento de usuários

O registro de identidades no banco de dados obedece a critérios técnicos de organização e proteção para resguardar a privacidade dos titulares e a segurança da operação:

**Estruturação em tabelas e registros:** Em bancos de dados relacionais, as informações dos usuários, como nome, endereço eletrônico e identificador único, são organizadas em tabelas, registros e campos. Restrições de unicidade podem ser aplicadas a identificadores específicos, evitando cadastros duplicados.

**Proteção criptográfica de senhas:** Em um sistema de autenticação seguro, as senhas não devem ser armazenadas em texto puro. Antes do armazenamento, elas são processadas por algoritmos de hash específicos para senhas, como o Argon2id, utilizando um salt individual para cada credencial.

**Controle de status e permissões:** Dependendo dos requisitos de cada sistema externo, os registros de usuários em bancos de dados corporativos podem conter atributos adicionais, embora o Serviço de Autenticação Web proposto limite-se estritamente à leitura dos atributos necessários à verificação de credenciais (CON01).

**Garantia de integridade e auditoria:** O banco de dados pode aplicar restrições de integridade, como campos obrigatórios, chaves primárias e restrições de unicidade, para assegurar a conformidade dos cadastros. Adicionalmente, o registro de alterações para fins de auditoria pode ser implementado conforme as necessidades da aplicação.

### 4.2. Consultas e recuperação de credenciais

No Serviço de Autenticação Web proposto, a base de usuários é um repositório externo preexistente e acessado exclusivamente para leitura, sem operações de cadastro ou modificação por parte do serviço, conforme estabelecido na premissa arquitetural PRE01. Quando o servidor de aplicação necessita validar um pedido de acesso, o banco de dados executa uma rotina de localização e entrega das informações solicitadas:

**Recepção da instrução de consulta:** O banco de dados recebe um comando estruturado de leitura proveniente da camada de aplicação, contendo o identificador informado na tentativa de acesso como parâmetro de busca.

**Localização indexada do registro:** O banco de dados procura o registro correspondente ao identificador informado. Quando existem índices adequados, eles podem tornar a localização mais eficiente, reduzindo a necessidade de examinar todos os registros armazenados.

**Extração seletiva dos atributos:** Em vez de transferir todo o cadastro, a consulta recupera somente os atributos necessários à autenticação, como o identificador único do usuário (`id`), o identificador de login (`identifier`) e o hash da senha armazenada (`password_hash`) com os parâmetros necessários para sua verificação.

**Devolução segura ao solicitante:** O banco de dados retorna as informações solicitadas ao componente responsável pela persistência, que as disponibiliza ao servidor de aplicação para a conferência das credenciais. Essa comunicação deve ser protegida de acordo com a infraestrutura adotada.

## 5. Integração entre os Componentes

O funcionamento completo da autenticação depende da comunicação coordenada entre todas as camadas da arquitetura, formando um fluxo contínuo de solicitação, processamento e resposta. Essa dinâmica segue o modelo de requisição e resposta empregado pelo HTTP (MDN WEB DOCS, s.d.-b):

**Envio da requisição pelo cliente:** O navegador reúne as credenciais inseridas no formulário e as transmite pela rede por meio de uma requisição HTTPS direcionada ao servidor Web.

**Recepção e encaminhamento pela camada web:** O servidor Web recebe a requisição HTTPS, realiza a terminação TLS, identifica o recurso solicitado e, conforme as regras de encaminhamento configuradas (*Reverse Proxy*), direciona a solicitação ao servidor de aplicação.

**Consulta e recuperação de dados:** O servidor de aplicação valida os campos recebidos, extrai o identificador informado e solicita ao banco de dados preexistente a leitura do registro correspondente àquela conta, obtendo o hash da senha e os parâmetros necessários para a verificação.

**Validação e emissão de parecer:** De posse dos dados recuperados, o servidor de aplicação verifica a senha informada em relação ao hash armazenado utilizando o algoritmo Argon2id e determina o resultado da autenticação. Caso as credenciais sejam válidas, retorna a identificação pública do usuário; caso contrário, retorna uma resposta padronizada de erro.

**Retorno e exibição ao usuário:** Por fim, o servidor Web encaminha a resposta HTTP gerada pela aplicação ao navegador, que interpreta o resultado recebido e apresenta a confirmação de autenticação ou a mensagem de falha ao usuário.