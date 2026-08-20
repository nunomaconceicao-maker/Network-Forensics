# Analyzing and Exploiting Network Traffic to Identify Credentials and File Leaks

## Cenário
Num cenário típico de cibersegurança, um atacante pode tentar intercetar e analisar o tráfego de rede para obter acesso não autorizado a informações sensíveis, tais como credenciais de autenticação ou ficheiros transmitidos através da rede. 

Ao utilizar ferramentas como o Wireshark, o tráfego de rede pode ser capturado e analisado para identificar vulnerabilidades em protocolos de comunicação não encriptados, como HTTP, FTP, SMTP, Telnet e Kerberos. Num teste de intrusão (*pentest*) ou numa auditoria de segurança, a análise de tráfego de rede ajuda os profissionais a identificar fraquezas de infraestrutura — como palavras-passe a circular em texto limpo, configurações incorretas ou transferências de ficheiros vulneráveis — permitindo a implementação de medidas robustas para proteger as comunicações e prevenir potenciais explorações.

## Objetivo
Aprender a capturar e analisar dados de autenticação HTTP, extrair imagens e ficheiros de capturas de tráfego (PCAP), analisar tráfego FTP/SMTP e interpretar dados de autenticação do protocolo Kerberos utilizando o Wireshark.

## Visão Geral
Neste projeto, vais adquirir experiência prática avançada na utilização do Wireshark como analisador de protocolos de rede. Aprenderás a capturar e inspecionar diferentes tipos de tráfego para detetar fugas de informação (*data leaks*). 

Os exercícios práticos irão guiar-te passo a passo na extração de artefactos (imagens e ficheiros) a partir de ficheiros PCAP, enquanto interpretas os protocolos subjacentes. A análise abrangerá a identificação de transferências de ficheiros FTP, a interceção de credenciais de correio eletrónico (SMTP) e sessões remotas (Telnet), bem como a examinação dos processos de autenticação do Kerberos. No final deste laboratório, terás uma base sólida na utilização do Wireshark para a condução de investigações forenses e análises táticas de segurança.

## Tarefas do Laboratório

1. [Exercise 01: Capturing and Analyzing HTTP Authentication Data with Wireshark](#exercise-01-capturing-and-analyzing-http-authentication-data-with-wireshark)
2. [Exercise 02: Extract Images from PCAP File Using Wireshark](#exercise-02-extract-images-from-pcap-file-using-wireshark)
3. [Exercise 03: Extracting FTP Files and Analyzing FTP Traffic with Wireshark](#exercise-03-extracting-ftp-files-and-analyzing-ftp-traffic-with-wireshark)
4. [Exercise 04: Capturing Telnet Credentials with Wireshark](#exercise-04-capturing-telnet-credentials-with-wireshark)
5. [Exercise 05: Extracting SMTP Credentials from Network Traffic Using Wireshark](#exercise-05-extracting-smtp-credentials-from-network-traffic-using-wireshark)
6. [Exercise 06: Analyzing Kerberos Authentication Traffic with Wireshark](#exercise-06-analyzing-kerberos-authentication-traffic-with-wireshark)

---

## Exercise 01: Capturing and Analyzing HTTP Authentication Data with Wireshark

### Cenário
O tráfego HTTP pode revelar informações críticas, como nomes de utilizador e palavras-passe, uma vez que é transmitido de forma nativamente insegura. Ao capturar pacotes de rede, é possível intercetar e analisar a comunicação entre um cliente (*browser*) e uma aplicação web (neste caso, a *Damn Vulnerable Web App* - DVWA). Esta atividade sublinha a importância absoluta de proteger o tráfego web através de protocolos de encriptação (HTTPS) e demonstra a eficácia dos filtros do Wireshark na identificação rápida de credenciais expostas na rede.

### Objetivo
Capturar e analisar dados de autenticação HTTP, com foco na interceção de credenciais de *login* em texto limpo (*plaintext*), utilizando o Wireshark durante o processo de autenticação numa aplicação web.

### Metodologia e Execução

1. **Preparação do Ambiente Alvo:** 
   * Na máquina alvo (Windows), ativaram-se os serviços Apache e MySQL através do *XAMPP Control Panel* para inicializar e alojar a aplicação DVWA.
2. **Início da Captura (*Packet Sniffing*):** 
   * Na máquina de análise (Kali Linux), abriu-se o Wireshark e iniciou-se a monitorização passiva de tráfego na interface de rede `eth0`.
3. **Geração de Tráfego Vulnerável:** 
   * Utilizando o *browser* Firefox, acedeu-se à página de autenticação da aplicação (`10.10.1.15/dvwa`).
   * Submeteu-se o formulário de *login* utilizando credenciais de teste (`admin` / `password`). De seguida, a captura no Wireshark foi interrompida.
4. **Análise e Extração (Método 1 - TCP Stream):** 
   * Aplicou-se o filtro genérico `http` para isolar o tráfego web do restante ruído da rede.
   * Localizou-se o pacote correspondente à submissão dos dados (método POST).
   * Clicando com o botão direito sobre o pacote, selecionou-se `Follow > TCP Stream`. Esta ação reconstruiu a sessão inteira, revelando o cabeçalho HTTP completo e o *payload* com o nome de utilizador e a palavra-passe perfeitamente legíveis.
5. **Análise e Extração (Método 2 - Inspeção Profunda e Filtros Específicos):** 
   * Para uma abordagem mais cirúrgica, aplicou-se o *Display Filter* específico: `http.request.method==POST`.
   * Ao selecionar o pacote isolado, expandiu-se a secção `HTML Form URL Encoded` no painel de detalhes (inferior). 
   * Este método permitiu dissecar estruturalmente o pacote, expondo as variáveis do formulário e os respetivos valores submetidos em texto limpo (`username=admin` e `password=password`).

### Análise e Conclusão
O laboratório evidenciou de forma clara a vulnerabilidade crítica associada aos protocolos não encriptados. Uma vez que o HTTP transmite os seus *payloads* em texto limpo, qualquer agente malicioso com acesso à rede local (ou posicionado numa rota intermédia) consegue capturar credenciais sem necessitar de ataques de força bruta ou ferramentas de desencriptação avançadas.

A exploração através do Wireshark provou que filtros direcionados (como a filtragem do método POST) e a reconstrução de sessões TCP são técnicas analíticas altamente eficazes para a extração imediata de dados sensíveis, reforçando a urgência corporativa na adoção de TLS/HTTPS para mitigar ataques *Man-in-the-Middle* (MitM).

---

## Exercise 02: Extract Images from PCAP File Using Wireshark

### Cenário
A extração de ficheiros a partir de capturas de tráfego de rede (PCAP) é uma técnica fundamental em análise forense e resposta a incidentes. Sendo o HTTP um protocolo de comunicação em texto limpo, ele não protege a confidencialidade dos ficheiros transferidos (como imagens, documentos ou até executáveis) entre o cliente e o servidor. O Wireshark possui funcionalidades avançadas que permitem não apenas inspecionar os pacotes individuais, mas também remontar os fragmentos de dados espalhados pela rede e reconstruir os objetos originais na sua totalidade.

### Objetivo
Extrair e analisar artefactos (ficheiros de imagem) a partir de um ficheiro de captura de tráfego (PCAP) utilizando o Wireshark, com foco na identificação e exportação de objetos HTTP.

### Metodologia e Execução

As operações foram realizadas num ambiente Kali Linux:

1. **Carregamento da Captura de Rede:**
   * Abriu-se a aplicação Wireshark.
   * Através do menu `File > Open`, importou-se o ficheiro de tráfego pré-capturado `images.pcap`, que continha um histórico de comunicações de rede a serem analisadas.

2. **Extração Automática de Objetos HTTP:**
   * Recorreu-se à funcionalidade nativa de reconstrução de ficheiros do Wireshark, navegando até `File > Export Objects > HTTP`.
   * *Mecânica:* Esta ferramenta isola e analisa automaticamente todos os fluxos HTTP presentes no ficheiro PCAP, listando os ficheiros (objetos) que foram transferidos durante as sessões capturadas, sem necessidade de isolar os pacotes manualmente.

3. **Identificação e Gravação do Artefacto:**
   * Na janela de objetos exportáveis, identificou-se a transferência de um ficheiro de imagem denominado `acunetix-logo.png`.
   * Selecionou-se o ficheiro específico e utilizou-se a opção `Save` para guardá-lo localmente na diretoria de `Downloads`.

4. **Validação da Integridade do Ficheiro:**
   * Utilizando o gestor de ficheiros do sistema (Thunar), acedeu-se à pasta de destino.
   * O ficheiro foi aberto com sucesso, revelando a imagem perfeitamente intacta. Esta ação confirmou que o Wireshark conseguiu remontar corretamente o artefacto a partir dos pacotes capturados.

### Análise e Conclusão
Este laboratório demonstrou na prática a facilidade com que ficheiros transmitidos sem encriptação podem ser intercetados e integralmente reconstruídos por terceiros. 

Do ponto de vista ofensivo, isto ilustra uma clara vulnerabilidade de fuga de informação (*Data Leakage*). Do ponto de vista defensivo e de investigação forense (*Blue Team*), a funcionalidade *Export Objects* do Wireshark é inestimável. Ela permite aos analistas de SOC investigar incidentes de forma célere, seja para verificar que dados sensíveis foram exfiltrados da organização, ou para reconstruir e analisar *malware* descarregado por um utilizador durante um ataque de *phishing* ou navegação web comprometida.

---

## Exercise 03: Extracting FTP Files and Analyzing FTP Traffic with Wireshark

### Cenário
O FTP (File Transfer Protocol) é um protocolo clássico para transferência de ficheiros em rede, mas possui uma falha arquitetónica grave sob a perspetiva da segurança moderna: toda a comunicação, incluindo a autenticação, é feita em texto limpo. Numa auditoria de segurança ou investigação forense, a interceção do tráfego FTP permite não só a captura de credenciais de acesso, mas também a extração total dos ficheiros transferidos durante a sessão. Ao contrário da extração automatizada de objetos HTTP, a reconstrução de ficheiros via FTP exige frequentemente a manipulação direta do fluxo de dados (TCP Stream) e a conversão de formatos de visualização para preservar a integridade binária do ficheiro.

### Objetivo
Capturar e analisar tráfego FTP utilizando o Wireshark, demonstrar a captura de credenciais em texto limpo e executar a reconstrução e extração manual de um ficheiro PDF a partir dos pacotes de rede.

### Metodologia e Execução

1. **Preparação do Servidor FTP (Alvo):**
   * Na máquina Windows, desativou-se o serviço nativo *Microsoft FTP Service* para evitar conflitos de portas.
   * Iniciou-se o servidor **FileZilla** através do *XAMPP Control Panel*, garantindo um ambiente FTP limpo no IP `10.10.1.15`.

2. **Início da Captura e Geração de Tráfego:**
   * Na máquina de análise (Kali Linux), iniciou-se a monitorização da interface `eth0` no Wireshark.
   * Utilizando o gestor de ficheiros do sistema operativo, acedeu-se ao servidor através do protocolo FTP (`ftp://10.10.1.15`).
   * A autenticação foi realizada com credenciais de utilizador registado (`qwerty` / `qwerty`).
   * Acedeu-se a um ficheiro remoto denominado `dummy.pdf`, gerando tráfego de transferência. Após esta ação, a captura no Wireshark foi interrompida.

3. **Análise do Canal de Controlo e Extração de Credenciais:**
   * Aplicou-se o filtro `ftp.request.command` para isolar apenas os comandos enviados pelo cliente para o servidor (geralmente na porta 21).
   * A análise aos pacotes revelou inequivocamente os comandos `USER qwerty` e `PASS qwerty`, confirmando a exposição total das credenciais de autenticação em *plaintext*.

4. **Análise do Canal de Dados e Extração do Ficheiro (File Carving):**
   * Aplicou-se o filtro específico `ftp-data`. (O FTP utiliza uma porta diferente para a transferência real de ficheiros, separando-a dos comandos).
   * Identificou-se o pacote referente à transferência do ficheiro `dummy.pdf`.
   * Através do clique direito, selecionou-se `Follow > TCP Stream` para agrupar todos os pacotes associados a essa transferência.
   * Inicialmente, os dados foram apresentados no formato *ASCII*, o que corrompe ficheiros não-texto. O formato de visualização foi alterado para **Raw**, preservando a codificação binária original do PDF.
   * Os dados *Raw* foram guardados (Save as) com o nome `dummyfile.pdf` na diretoria do Desktop.
   * **Resultado:** O ficheiro PDF foi aberto localmente com sucesso, validando a integridade da extração.

### Análise e Conclusão
Este exercício destacou dois riscos críticos inerentes ao protocolo FTP tradicional. Em primeiro lugar, o roubo de credenciais é trivial, bastando filtrar os comandos de pedido. Em segundo lugar, a interceção de propriedade intelectual, dados corporativos ou PII contidos em ficheiros transmitidos é exequível por qualquer atacante que consiga isolar o canal `ftp-data` e manipular o fluxo TCP. 

Do ponto de vista técnico e forense, o laboratório demonstrou a importância de dominar o `TCP Stream` e compreender a diferença entre a codificação *ASCII* e *Raw* para reconstruir artefactos com sucesso. Para mitigar estas ameaças, as organizações devem descontinuar o FTP em favor de alternativas robustas, como o SFTP (SSH File Transfer Protocol) ou o FTPS (FTP over SSL/TLS).

---

## Exercise 04: Capturing Telnet Credentials with Wireshark

### Cenário
O Telnet é um protocolo de comunicação de rede legado, historicamente utilizado para acesso remoto e administração de servidores através da porta TCP 23. O seu problema arquitetónico mais grave é a ausência total de encriptação: todos os dados, incluindo credenciais de autenticação e os próprios comandos executados, são transmitidos em texto limpo (*plaintext*). Num ambiente corporativo, a utilização de Telnet permite que qualquer atacante com capacidade de *sniffing* na rede consiga capturar credenciais com privilégios administrativos. Este cenário reforça a urgência absoluta da sua descontinuação em favor de protocolos seguros, como o SSH (Secure Shell).

### Objetivo
Capturar, analisar e intercetar tráfego Telnet utilizando o Wireshark, extraindo credenciais de autenticação e comandos de sessão, com o intuito de demonstrar empiricamente a insegurança de protocolos de administração remota não encriptados.

### Metodologia e Execução

1. **Início da Captura de Rede:**
   * Na máquina de análise (Kali Linux), abriu-se o Wireshark e iniciou-se a monitorização contínua na interface `eth0`.

2. **Estabelecimento da Sessão Remota e Interação:**
   * Através do emulador de terminal do Kali, iniciou-se uma ligação remota ao Windows Server (alvo) utilizando o comando: `telnet 10.10.1.8`.
   * Realizou-se a autenticação no sistema com a conta de maior privilégio (`Administrator`) e a respetiva palavra-passe (`Pa$$w0rd`).
   * Para simular a atividade de um administrador (ou de um atacante pós-exploração), executaram-se comandos de enumeração de sistema e de rede: `whoami` (validando o acesso como administrador do servidor) e `ipconfig /all`.
   * A sessão foi encerrada e a captura de pacotes no Wireshark foi interrompida.

3. **Filtragem e Reconstrução da Sessão (TCP Stream):**
   * No Wireshark, aplicou-se o filtro específico `telnet` para remover o ruído de fundo da rede e focar apenas na comunicação do protocolo.
   * Selecionou-se um dos pacotes de dados da comunicação, acedendo-se de seguida ao menu contextual através do botão direito: `Follow > TCP Stream`.
   * Assegurou-se que o formato de apresentação de dados na janela do TCP Stream estava definido para **ASCII**.

### Análise e Conclusão
A análise do fluxo de dados revelou a exposição total e crítica da sessão de administração remota. A janela do *TCP Stream* apresentou, de forma perfeitamente legível, o *prompt* de autenticação, o nome de utilizador (`Administrator`) e a palavra-passe utilizada, confirmando a interceção bem-sucedida das credenciais.

Mais grave do que a simples exposição da *password*, a reconstrução do tráfego através do Wireshark evidenciou que cada tecla pressionada pelo utilizador e cada resposta do servidor — incluindo o *output* completo dos comandos `whoami` e `ipconfig /all` — foi capturada. Do ponto de vista de um teste de intrusão ou de segurança defensiva, este laboratório comprova que a presença de serviços Telnet ativos numa rede é uma vulnerabilidade severa que compromete instantaneamente o controlo de acessos, sublinhando que qualquer interação administrativa em texto limpo equivale a entregar os acessos do sistema a qualquer observador passivo na rede.

---

## Exercise 05: Extracting SMTP Credentials from Network Traffic Using Wireshark

### Cenário
Na análise de tráfego de rede, protocolos de correio eletrónico tradicionais, como o SMTP (Simple Mail Transfer Protocol), operam frequentemente sem encriptação. Isto permite que um atacante posicionado na rede intercete informações sensíveis. 

Um detalhe técnico crucial do SMTP tradicional é a forma como lida com a autenticação: o nome de utilizador e a palavra-passe não são enviados em texto completamente limpo, mas sim codificados no formato **Base64**. Contudo, o Base64 é apenas um algoritmo de codificação de dados (ofuscação) e não um mecanismo de encriptação. Através da interceção do tráfego com o Wireshark e da extração do fluxo de dados (TCP Stream), é possível isolar estas credenciais codificadas e revertê-las para o seu estado original utilizando utilitários básicos de linha de comandos.

### Objetivo
Capturar e extrair credenciais SMTP (nome de utilizador e palavra-passe) a partir de tráfego de rede utilizando o Wireshark, e efetuar a sua descodificação manual através do utilitário de terminal `base64`.

### Metodologia e Execução

O laboratório foi executado na máquina de análise (Kali Linux) recorrendo a uma amostra de tráfego de rede real:

1. **Obtenção do Ficheiro de Tráfego:**
   * Utilizando o *browser*, acedeu-se ao repositório público oficial do Wireshark (*SampleCaptures*).
   * Localizou-se e descarregou-se o ficheiro de captura `smtp.pcap`, que contém o registo de uma comunicação de e-mail vulnerável.

2. **Identificação do Processo de Autenticação:**
   * O ficheiro PCAP foi aberto no Wireshark.
   * Inspecionaram-se os pacotes do protocolo SMTP para localizar os comandos transacionais enviados entre o cliente e o servidor.
   * Identificou-se especificamente o pacote contendo o comando `AUTH LOGIN`, que assinala o início do processo de autenticação por parte do cliente de correio eletrónico.

3. **Extração do Fluxo e Identificação dos Hashes Base64:**
   * Através do clique direito sobre o pacote de autenticação, executou-se a funcionalidade `Follow > TCP Stream` para visualizar a conversa completa de forma sequencial.
   * Na janela do fluxo TCP, identificaram-se claramente os *prompts* do servidor a solicitar o *username* e a *password*, seguidos pelas respostas do cliente. Ambas as respostas consistiam em *strings* ofuscadas (formato Base64).

4. **Descodificação de Credenciais via Terminal:**
   * Copiou-se a *string* em Base64 correspondente ao nome de utilizador a partir do Wireshark.
   * No terminal do Kali Linux, utilizou-se a ferramenta nativa de descodificação redirecionando a *string* como *input*: `base64 -d <<< [STRING_BASE64_DO_USERNAME]`. A execução do comando revelou o nome de utilizador em texto limpo.
   * O processo foi repetido para a segunda *string* (a palavra-passe): `base64 -d <<< [STRING_BASE64_DA_PASSWORD]`, extraindo a credencial de acesso com sucesso.

### Análise e Conclusão
Este laboratório ilustrou perfeitamente os riscos severos associados à utilização de protocolos não encriptados para a transmissão de dados sensíveis. Demonstrou-se que a codificação Base64, frequentemente confundida com segurança em sistemas antigos, não oferece qualquer proteção contra interceções. 

A combinação da capacidade analítica do Wireshark (para isolar o `AUTH LOGIN` e o fluxo TCP) com ferramentas de linha de comandos Linux (`base64`) prova que a extração de credenciais é um processo direto e trivial para qualquer atacante. Como medida de mitigação e remediação, este cenário reforça a necessidade imperativa da adoção de protocolos seguros, nomeadamente o **SMTPS (SMTP Secure)** ou a imposição de túneis TLS na configuração dos servidores de correio, garantindo que toda a autenticação e transmissão de dados ocorre dentro de canais criptograficamente seguros.

---

## Exercise 06: Analyzing Kerberos Authentication Traffic with Wireshark

### Cenário
O Kerberos é um protocolo de autenticação robusto e amplamente adotado em redes corporativas (como o Microsoft Active Directory). Baseia-se em criptografia de chaves simétricas e depende de um serviço terceiro confiável, o *Key Distribution Center* (KDC), para autenticar utilizadores e serviços. 

Ao analisar o tráfego Kerberos no Wireshark, elementos-chave como o *CNameString* (Client Name String), o *Realm* (Domínio) e o *etype* (Encryption Type) fornecem uma visão profunda sobre a arquitetura da rede. Através da filtragem e inspeção destes campos, um analista consegue descobrir identidades de utilizadores, endereços de rede, mapeamento de serviços e os métodos de encriptação que estão a proteger as comunicações, sendo um passo fundamental para auditar a segurança da infraestrutura ou identificar vetores de ataque.

### Objetivo
Analisar e extrair informações críticas de infraestrutura e identidade a partir de pacotes de autenticação do protocolo Kerberos, utilizando funcionalidades avançadas de filtragem e inspeção de pacotes no Wireshark.

### Metodologia e Execução

O laboratório foi executado na máquina de análise (Kali Linux) utilizando uma amostra de tráfego de rede pública:

1. **Obtenção e Preparação da Captura:**
   * Através do *browser*, acedeu-se ao repositório público do Wireshark (*SampleCaptures*) e descarregou-se o arquivo `krb-816.zip`.
   * O ficheiro foi descompactado localmente e a captura de tráfego `krb-816.cap` foi aberta no Wireshark.

2. **Isolamento de Pedidos Kerberos:**
   * Aplicou-se o filtro base `kerberos.CNameString` na barra de pesquisa para isolar os pacotes de rede que continham nomes de clientes (excluindo tráfego irrelevante).

3. **Inspeção Profunda do Pacote (AS-REQ):**
   * Selecionou-se um pacote de Pedido de Serviço de Autenticação (*Authentication Service Request* - AS-REQ). No painel de detalhes, expandiu-se a árvore de informação: `Kerberos > as-req > req-body`.
   * **Identificação do Domínio:** Verificou-se o valor do campo `realm`, que revelou o nome do domínio onde a autenticação estava a decorrer (neste caso, `DENYDC`).
   * **Validação de Criptografia:** Expandiu-se a secção `etype`. Esta ação listou os algoritmos de encriptação suportados e propostos pelo cliente para a geração de *tickets* (informação crítica para garantir que a rede não está a usar algoritmos obsoletos).
   * **Mapeamento de Rede:** Expandiu-se a secção `addresses > HostAddress`, identificando os endereços IP do cliente e do servidor (KDC) envolvidos na troca.
   * **Identidade do Cliente:** Expandiu-se a secção `cname > cname-string`, que revelou o *Canonical Name* (nome principal) da entidade que estava a solicitar a autenticação.

4. **Otimização da Interface de Análise:**
   * Para facilitar a leitura de grandes volumes de tráfego, selecionou-se o campo `CNameString` e, com o clique direito, escolheu-se `Apply as Column` (atalho: `Ctrl+Shift+I`). Isto adicionou uma coluna dedicada na interface principal do Wireshark, permitindo identificar instantaneamente o utilizador associado a cada pacote.

5. **Filtragem Avançada (Contas de Utilizador vs Contas de Máquina):**
   * Observou-se que muitas entradas no *CNameString* terminavam com o símbolo cifrão (`$`). Em ambientes Windows, o sufixo `$` indica que a identidade pertence a uma **conta de máquina** ou serviço (ex: `PC-01$`), e não a um utilizador humano.
   * Para limpar a visualização e focar a análise exclusivamente em contas de utilizador (alvos preferenciais em ataques e auditorias), aplicou-se o seguinte filtro booleano avançado:
     `kerberos.CNameString and !(kerberos.CNameString contains $)`
   * **Resultado:** O Wireshark excluiu todos os pacotes das máquinas, listando apenas os pedidos de autenticação iniciados por utilizadores.

### Análise e Conclusão
A dissecção de tráfego Kerberos demonstra o poder do Wireshark no reconhecimento de infraestruturas lógicas. Sem executar um único comando interativo contra o servidor alvo, foi possível mapear o nome do domínio interno (`DENYDC`), identificar IPs críticos (Controladores de Domínio), listar os algoritmos de encriptação permitidos pela política da rede e extrair nomes de contas de utilizador válidas.

A utilização de filtros booleanos excludentes (`!(contains $)`) provou ser uma técnica inestimável para *Threat Hunting* e análise de *logs*, permitindo aos analistas de segurança separar rapidamente o "ruído" gerado pelas autenticações automáticas de máquinas da atividade real e potencialmente suspeita de contas de utilizador.