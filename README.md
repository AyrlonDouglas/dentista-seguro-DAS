### **Documento Arquitetural do Sistema (DAS)**
### **Projeto Dentista Seguro**

**Versão:** 2.0 (Resultado da Sprint 2)
**Data:** 19 de outubro de 2025
**Autor(es):** Ayrlon Vilarim

---

### **1. Introdução**

#### **1.1. Propósito do Documento**
O propósito deste Documento Arquitetural do Sistema (DAS) é descrever a visão, os drivers e o design da arquitetura de software para a plataforma **Dentista Seguro**. Este documento serve como um guia técnico para a futura equipe de desenvolvimento, garantindo que a implementação da solução esteja alinhada com os requisitos funcionais e não funcionais, e que siga as melhores práticas de escalabilidade, segurança e manutenibilidade.

#### **1.2. Escopo do Projeto**
O Dentista Seguro é uma plataforma digital concebida como uma Comunidade de Prática (CoP) para a comunidade odontológica brasileira. O sistema visa combater a crise de gestão do conhecimento clínico-colaborativo, fornecendo um ambiente estruturado para que cirurgiões-dentistas e estudantes possam discutir casos clínicos complexos, buscar segundas opiniões qualificadas e construir uma base de conhecimento permanente e pesquisável.

Este documento foca na arquitetura de uma solução **cloud-native**, com um backend organizado em uma **arquitetura modular** (monolítico modular), um frontend mobile desenvolvido em **React Native com Expo** e um backend em **Node.js**.

#### **1.3. Definições, Acrônimos e Abreviações**
* **DAS:** Documento Arquitetural do Sistema
* **C4 Model:** Modelo para visualização de arquitetura de software (Contexto, Contêineres, Componentes, Código)
* **RF:** Requisito Funcional
* **RNF:** Requisito Não Funcional
* **API:** Application Programming Interface
* **LGPD:** Lei Geral de Proteção de Dados
* **AWS:** Amazon Web Services
* **CRO/CRP:** Conselho Regional de Odontologia / Conselho Regional Profissional

---

### **2. Contexto e Drivers Arquiteturais**

#### **2.1. Contexto do Projeto**
O desafio central abordado reside na efemeridade do conhecimento e na fragmentação da rede de apoio na odontologia brasileira. Ferramentas informais como o WhatsApp são ineficientes para discussões clínicas complexas, resultando em perda de contexto, desorganização e falta de um registro histórico. O Dentista Seguro visa resolver este problema, oferecendo uma plataforma dedicada, segura e organizada.

#### **2.2. Drivers Arquiteturais (Requisitos Chave)**
A arquitetura proposta é fortemente influenciada pelos seguintes requisitos, que são cruciais para o sucesso da plataforma:

* **Colaboração Estruturada (RF2.1, RF3.2):** A necessidade de postar casos com múltiplas mídias (fotos, radiografias) e ter discussões organizadas (threads) impulsiona a escolha de uma arquitetura que separe o gerenciamento de mídias do processamento de dados textuais, mesmo dentro de um monólito modular.
* **Escalabilidade (RNF1.1):** A plataforma deve suportar um crescimento exponencial de usuários e dados sem degradação de performance. Este é o principal driver para a adoção de uma arquitetura cloud-native e um design modular que permita a escalabilidade vertical e horizontal do monólito.
* **Segurança e Conformidade com a LGPD (RNF2.4):** A gestão de dados de pacientes, mesmo que anonimizados, exige um alto nível de segurança, controle de acesso e criptografia. Isso influencia o design do módulo de autenticação e autorização, e a forma como os dados são armazenados e transmitidos.
* **Disponibilidade (RNF4.1):** Como ferramenta de apoio à decisão clínica, a plataforma precisa estar altamente disponível. Isso justifica o uso de serviços gerenciados em nuvem e uma arquitetura resiliente.

---

### **3. Visão da Arquitetura**

#### **3.1. Padrões e Decisões Arquiteturais**
* **Cloud-Native:** A solução será projetada para ser totalmente executada em um provedor de nuvem (AWS/Azure), aproveitando serviços gerenciados para otimizar a escalabilidade, disponibilidade e reduzir a sobrecarga operacional.
* **Arquitetura Monolítica Modular:** O backend será organizado em módulos lógicos bem definidos e desacoplados, mas implantados como uma única unidade. Isso facilita o desenvolvimento inicial, a gestão da base de código e o deploy, ao mesmo tempo que permite clareza na separação de responsabilidades.
* **API-First:** A comunicação entre o frontend e o backend será feita através de uma API RESTful bem definida, servindo como a interface principal do sistema.

#### **3.2. Modelo C4 - Nível 1: Diagrama de Contexto**
O Diagrama de Contexto posiciona o sistema Dentista Seguro no centro de seu ecossistema.

* **Sistema:** Dentista Seguro
* **Atores:**
    * **Cirurgião-Dentista / Estudante:** O usuário principal, que interage com o sistema através de um aplicativo móvel para criar, visualizar e discutir casos clínicos.
* **Sistemas Externos:**
    * **Serviço de E-mail:** Utilizado para notificações transacionais, como redefinição de senha e confirmação de cadastro.
    * **Serviço de Notificação Push (Expo Push notification):** Utilizado para enviar alertas em tempo real sobre novas interações nos casos clínicos.
    * **Serviço de monitoramento de erros:** Utilizado para monitorar falhas no sistema para que possam ser atuadas mais rapidamente.

<img width="2484" height="3328" alt="image" src="https://github.com/user-attachments/assets/3bdf082b-5879-4523-a1c7-2740c2a3924a" />


#### **3.3. Modelo C4 - Nível 2: Diagrama de Contêineres**
Este diagrama detalha os principais blocos tecnológicos que compõem o sistema Dentista Seguro.

* **Mobile App (Frontend):**
    * **Descrição:** Aplicativo móvel para iOS e Android que serve como ponto de interação para o usuário.
    * **Tecnologia:** React Native com Expo.
* **Backend Application (Monolítico Modular):**
    * **Descrição:** Aplicação central que encapsula todas as funcionalidades de negócio (gerenciamento de usuários, casos clínicos, comentários, notificações). Responsável por expor a API RESTful para o frontend.
    * **Tecnologia:** Node.js (com framework como Express/NestJS).
* **Banco de Dados Relacional:**
    * **Descrição:** Armazena dados estruturados como informações de usuários, casos clínicos (textos), comentários, etc.
    * **Tecnologia:** PostgreSQL (via Amazon RDS / Azure Database for PostgreSQL).
* **Armazenamento de Objetos (Blob Storage):**
    * **Descrição:** Armazena arquivos binários grandes, como imagens, radiografias e vídeos anexados aos casos clínicos.
    * **Tecnologia:** Amazon S3 / Azure Blob Storage.
* **Armazenamento de Dados em memória**
    * **Descrição:** Armazena em memória os dados mais solicitados durante as requisições para aumentar a performance das consultas.
    * **Tecnologia:** Redis.

<img width="3444" height="4084" alt="image" src="https://github.com/user-attachments/assets/a71bbc08-2d30-41bd-acc6-a9dd610d49c4" />


#### **3.4. Modelo C4 - Nível 3: Diagrama de Componentes**
Para o Backend API, que adota uma arquitetura monolítica modular em Node.js, o detalhamento no Nível 3 do C4 Model ilustra os principais componentes lógicos e suas interações internas. Este nível de detalhe é crucial para entender como as funcionalidades de negócio são orquestradas dentro da aplicação principal.

<img width="4244" height="6644" alt="image" src="https://github.com/user-attachments/assets/5ca5c892-eb4d-43b3-82a8-f9b514a18344" />

Descrição dos Componentes Principais:

    Módulo de autenticação: Componente responsável por todas as operações de autenticação (login, registro, redefinição de senha) e geração/validação de tokens de acesso. Interage com o UsuarioService para gerenciar as credenciais do usuário.

    Módulo de usuário: Gerencia as informações de perfil dos usuários (criação, leitura, atualização, exclusão de dados do perfil, especialidades).

    Módulo de caso clínico: Contém a lógica de negócio para a criação, visualização, edição, exclusão e pesquisa de casos clínicos. Ainda no mesmo, Gerencia todas as operações relacionadas a comentários e respostas em casos clínicos.

    Módulo comum: Responsáel por agrupar serviços que são de uso comum dos módulos. Dentro dele há atualmente 4 serviços. O AnexoService é responsável pelo upload, gerenciamento e recuperação de mídias (imagens, vídeos) associadas aos casos clínicos. O NotificationService que notificações por push para os usuários. O EmailService é responsável pelo envio de emails para os usuários. O MonitoramentoService é responsável pelo registro de logs e errors que ocorrem na aplicações.


#### **3.5. Modelo de Dados Inicial**
O Modelo de Dados Lógico/Conceitual inicial define as principais entidades de informação do sistema Dentista Seguro, seus atributos e os relacionamentos entre elas. Este modelo serve como a base para o design do banco de dados relacional (PostgreSQL) e garante a integridade e a consistência dos dados do sistema.

<img width="915" height="623" alt="Screenshot 2025-10-19 at 18 41 53" src="https://github.com/user-attachments/assets/ffe79636-ae25-4be6-8e92-850d92ed4e61" />

Este modelo de dados assegura que todas as informações necessárias para as funcionalidades do sistema sejam armazenadas de forma organizada e eficiente.

---

### **4. Justificativa Tecnológica**

1.  **Backend: Node.js (com frameworks como Express/NestJS)**
    * **Justificativa:** Escolha devido à sua alta performance para operações de I/O intensivas, modelo de programação assíncrono e unificação da stack tecnológica (JavaScript/TypeScript) com o frontend. Node.js é ideal para a construção de uma aplicação modular, permitindo a separação lógica das responsabilidades enquanto mantém um deploy simplificado.

2.  **Frontend (Mobile): React Native com Expo**
    * **Justificativa:** Optamos por React Native com o ecossistema Expo para o aplicativo móvel. Essa combinação permite o desenvolvimento rápido de aplicativos iOS e Android a partir de uma única base de código JavaScript/TypeScript, otimizando o reuso e a manutenção. Expo simplifica significativamente o ambiente de desenvolvimento, facilitando o prototipagem e o deploy.

3.  **Nuvem: AWS / Azure**
    * **Justificativa:** A arquitetura é projetada para ser implementada em qualquer um dos líderes de mercado em computação em nuvem. Ambos oferecem um portfólio completo de serviços gerenciados (computação, banco de dados, armazenamento, rede, segurança) essenciais para uma solução cloud-native, com alta disponibilidade e escalabilidade.

---

### **5. Apêndice**

### **Tabela de Requisitos do Projeto Dentista seguro**

#### **Requisitos Funcionais (RFs)**

| ID     | Tipo       | Descrição do Requisito                                                                                                                                                                                | Justificativa (Opcional)                                                                                                                                                                                      |
| :----- | :--------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **RF1.1** | Cadastro   | O sistema deve permitir que novos usuários (Cirurgião-Dentista, Estudante) se cadastrem, fornecendo nome, registro profissional (CRO/CRP, se aplicável), instituição de ensino, especialidade/interesse. | Essencial para o onboarding de novos membros e para a diferenciação dos perfis profissionais.                                                                                                                   |
| **RF1.2** | Autenticação | O sistema deve permitir que os usuários façam login e logout com segurança.                                                                                                                             | Base para acesso seguro e controle de sessão.                                                                                                                                                                 |
| **RF1.3** | Perfil     | O sistema deve permitir que os usuários editem seu perfil (informações pessoais, foto, especialidades).                                                                                                 | Permite que os usuários mantenham suas informações atualizadas e se apresentem adequadamente à comunidade.                                                                                                     |
| **RF1.4** | Autenticação | O sistema deve permitir que os usuários redefinam sua senha de forma segura.                                                                                                                           | Funcionalidade padrão para recuperação de acesso.                                                                                                                                                             |
| **RF1.5** | Perfil     | O sistema deve exibir o perfil de outros usuários, incluindo suas informações e especialidades.                                                                                                        | Facilita o networking, a identificação de especialistas e a construção de confiança na comunidade.                                                                                                              |
| **RF2.1** | Criação Caso | O sistema deve permitir que um usuário crie um novo caso clínico, incluindo título, descrição detalhada, anexo de mídias (fotos, radiografias, tomografias, vídeos) e tags/categorias.                    | Core da funcionalidade, permitindo a postagem e contextualização de casos para discussão.                                                                                                                     |
| **RF2.2** | Visualização | O sistema deve permitir que os usuários visualizem os detalhes de um caso clínico.                                                                                                                     | Permite o acesso à informação detalhada de um caso para análise e colaboração.                                                                                                                                 |
| **RF2.3** | Gerenc. Caso | O sistema deve permitir que os usuários editem ou excluam seus próprios casos clínicos.                                                                                                               | Garante controle e autonomia do autor sobre o conteúdo postado.                                                                                                                                                |
| **RF2.4** | Pesquisa   | O sistema deve permitir que os usuários pesquisem casos clínicos por tags, categorias, palavras-chave ou especialidade.                                                                                  | Essencial para a descoberta de conhecimento e localização de casos relevantes na base de conhecimento.                                                                                                         |
| **RF2.5** | Feed       | O sistema deve exibir um feed de casos clínicos, ordenado por data de postagem ou relevância.                                                                                                         | Proporciona uma visão dinâmica e atualizada das discussões na plataforma.                                                                                                                                      |
| **RF2.6** | Solução    | O sistema deve permitir que o autor de um caso marque um comentário como "Solução" ou "Melhor Resposta".                                                                                             | Ajuda a identificar soluções eficazes, organizando o conhecimento e facilitando a busca futura por casos resolvidos.                                                                                            |
| **RF3.1** | Comentário | O sistema deve permitir que os usuários comentem em um caso clínico.                                                                                                                                  | Base para a colaboração e discussão de casos.                                                                                                                                                                 |
| **RF3.2** | Resposta   | O sistema deve permitir que os usuários respondam a comentários existentes, criando threads de discussão.                                                                                             | Organiza as discussões e mantém o contexto das interações sobre cada ponto específico do caso.                                                                                                               |
| **RF3.3** | Gerenc. Com.| O sistema deve permitir que os usuários editem ou excluam seus próprios comentários.                                                                                                                  | Garante controle e autonomia do usuário sobre suas contribuições.                                                                                                                                              |
| **RF3.4** | Avaliação  | O sistema deve permitir que os usuários "curtam" ou deem "upvote" em casos clínicos e comentários.                                                                                                     | Promove o engajamento, ajuda a destacar conteúdo de valor e a reputação dos colaboradores.                                                                                                                    |
| **RF3.5** | Notificação | O sistema deve gerar notificações para o autor de um caso quando houver um novo comentário ou marcação de solução.                                                                                     | Mantém o autor informado sobre as interações relevantes em seus casos.                                                                                                                                         |
| **RF3.6** | Notificação | O sistema deve gerar notificações para usuários que comentaram em um caso quando houver novas respostas.                                                                                               | Mantém os participantes da discussão informados sobre a evolução do caso.                                                                                                                                      |
| **RF4.1** | Filtragem  | O sistema deve permitir que os usuários filtrem casos clínicos por especialidade, tipo de caso (ex: resolvido, em aberto) e tags.                                                                       | Aprimora a capacidade de pesquisa e descoberta de conhecimento específico.                                                                                                                                     |
| **RF4.2** | Favoritos  | O sistema deve permitir que os usuários salvem casos clínicos em uma lista de favoritos/coleções pessoais.                                                                                             | Ajuda na organização pessoal do conhecimento e na revisão de casos de interesse.                                                                                                                               |

#### **Requisitos Não Funcionais (RNFs)**

| ID     | Tipo             | Descrição do Requisito                                                                                                                       | Justificativa (Opcional)                                                                                                                                                                              |
| :----- | :--------------- | :------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **RNF1.1** | **Desempenho - Escalabilidade** | O sistema deve ser capaz de escalar para suportar até 10.000 usuários ativos concorrentes sem degradação perceptível de desempenho. | Essencial para o crescimento da plataforma e para garantir que a experiência do usuário não seja comprometida com o aumento da base de usuários.                                                                   |
| **RNF1.2** | **Desempenho - Tempo de Resposta** | O tempo de resposta para a visualização de um caso clínico (excluindo o carregamento inicial de mídias grandes) não deve exceder 2 segundos para 90% das requisições. | Crucial para a usabilidade e para manter o engajamento dos usuários, evitando frustração com a lentidão.                                                                                                       |
| **RNF1.3** | **Desempenho - Carregamento Mídia** | Mídias (fotos de até 5MB) devem ser carregadas em no máximo 5 segundos em redes 4G/Wi-Fi estáveis.                              | Importante para a postagem e visualização eficaz de casos clínicos que dependem fortemente de evidências visuais.                                                                                            |
| **RNF2.1** | **Segurança - Autenticação** | O sistema deve implementar autenticação segura de usuários (ex: via JWT, OAuth2) com proteção contra ataques de força bruta.         | Protege o acesso às contas dos usuários e a integridade da plataforma.                                                                                                                                      |
| **RNF2.2** | **Segurança - Autorização** | O sistema deve garantir que usuários só possam acessar, editar ou excluir seus próprios dados, a menos que explicitamente autorizado (ex: moderador). | Garante o controle de acesso e a privacidade dos dados do usuário, prevenindo acessos indevidos.                                                                                                          |
| **RNF2.3** | **Segurança - Proteção Dados** | Todos os dados sensíveis (informações de perfil, dados de casos com informações de pacientes) devem ser armazenados e transmitidos de forma criptografada. | Fundamental para a proteção da privacidade e a conformidade legal, especialmente em um contexto de saúde.                                                                                                     |
| **RNF2.4** | **Segurança - Conformidade LGPD** | O sistema deve estar em conformidade com a Lei Geral de Proteção de Dados (LGPD) e outras regulamentações aplicáveis à saúde.   | Requisito legal obrigatório no Brasil para a gestão de dados pessoais e sensíveis, evitando penalidades e garantindo a confiança dos usuários.                                                                |
| **RNF2.5** | **Segurança - Vulnerabilidades** | O código e a infraestrutura devem ser regularmente auditados para identificar e corrigir vulnerabilidades de segurança.               | Garante a robustez do sistema contra ataques e a manutenção de um ambiente seguro ao longo do tempo.                                                                                                        |
| **RNF3.1** | **Usabilidade - Intuitividade** | A interface do usuário deve ser intuitiva, permitindo que usuários com conhecimento médio em tecnologia possam postar casos e interagir sem treinamento. | Essencial para a adoção da plataforma por profissionais que têm foco clínico, não tecnológico.                                                                                                                  |
| **RNF3.2** | **Usabilidade - Responsividade** | A interface (seja mobile ou web) deve ser responsiva e se adaptar a diferentes tamanhos de tela e dispositivos.                    | Garante uma boa experiência de uso em múltiplos dispositivos (smartphones, tablets, desktops).                                                                                                               |
| **RNF3.3** | **Usabilidade - Feedback** | O sistema deve fornecer feedback claro e imediato ao usuário sobre o status das operações.                                             | Aumenta a confiança do usuário no sistema e reduz a frustração.                                                                                                                                             |
| **RNF4.1** | **Confiabilidade - Disponibilidade** | O sistema deve ter uma disponibilidade mínima de 99.9% (equivalente a ~8.76 horas de inatividade por ano).                         | Garante que a plataforma esteja acessível quando os profissionais precisarem de suporte rápido para decisões clínicas.                                                                                         |
| **RNF4.2** | **Confiabilidade - Recuperação Desastres** | O sistema deve ter um plano de recuperação de desastres que permita a restauração dos dados em caso de falha grave (RTO 4h, RPO 1h). | Protege contra a perda de dados críticos e garante a continuidade do serviço em cenários de falha.                                                                                                          |
| **RNF5.1** | **Manutenibilidade - Modularidade** | O sistema deve ser construído com uma arquitetura modular para facilitar a manutenção e a adição de novas funcionalidades. | Reduz a complexidade do desenvolvimento, permite que equipes menores trabalhem independentemente e facilita a evolução do sistema.                                                                              |
| **RNF5.2** | **Manutenibilidade - Testabilidade** | Os componentes do sistema devem ser projetados para serem facilmente testáveis (testes unitários, de integração).                  | Aumenta a qualidade do código, reduz bugs e acelera o ciclo de desenvolvimento.                                                                                                                                |
| **RNF6.1** | **Compatibilidade - Dispositivos** | O aplicativo mobile deve ser compatível com as duas últimas versões dos sistemas operacionais iOS e Android.                         | Garante que a maioria dos usuários de dispositivos móveis possa acessar a plataforma.                                                                                                                          |
| **RNF6.2** | **Compatibilidade - Navegadores** | A interface web (se aplicável) deve ser compatível com as versões mais recentes dos principais navegadores (Chrome, Firefox, Safari, Edge). | Garante que usuários que acessam via web tenham uma experiência consistente, independente do navegador de preferência.                                                                                        |

---
