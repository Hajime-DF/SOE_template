\# PC1 - Proposta do Projeto



\*\*Disciplina:\*\* Sistemas Operacionais Embarcados  

\*\*Universidade:\*\* Universidade de Brasília (UnB)  

\*\*Projeto:\*\* Driver Drowsiness \& Distraction Detection System (DMS)  

\*\*Plataforma:\*\* Raspberry Pi 4 Model B  

\*\*Sensor de Vídeo:\*\* SmartCam S600  

\*\*Linguagem de Desenvolvimento:\*\* C / C++ (POSIX Threads)



\## Membros da Dupla



\* Vinícius Eduardo Lopes da Silva - 202024061

\* Gabriel Hajime Yamaoka Sebata - 200018108



\---



\## 1. Termo de Abertura do Projeto (TAP)



\### 1.1. Justificativa e Contexto

A fadiga, o sono e a distração ao volante estão entre as principais causas de acidentes graves em rodovias. Segundo dados da Associação Brasileira de Medicina de Tráfego (ABRAMET, 2021), o sono e a fadiga estão associados a até 40% dos acidentes de trânsito no Brasil. A probabilidade de colisões graves na madrugada é três vezes maior do que no período diurno, caracterizando-se pela ausência de marcas de frenagem na pista devido à perda momentânea de consciência do condutor (microsono).



O público-alvo prioritário deste projeto é o setor de transporte de cargas e gestão de frotas rodoviárias. Motoristas profissionais enfrentam longas jornadas em vias mal iluminadas, onde episódios de microsono representam risco imediato à vida e grandes prejuízos materiais.



Para viabilizar a operação em ambientes automotivos e garantir facilidade de fixação no painel da cabine, o projeto utiliza a câmera inteligente \*\*SmartCam S600\*\*. A integração via interface USB/UVC permite a captação contínua do fluxo de vídeo do condutor com alta estabilidade de sinal.



\### 1.2. O Diferencial da Solução Proposta

No mercado e na academia, as soluções existentes apresentam gargalos claros:

1\. Sensores Veiculares Comerciais (Ex: Ituran Sensor de Fadiga): São eficientes, mas utilizam hardware proprietário fechado e possuem alto custo de aquisição e manutenção.

2\. Aplicativos de Smartphone com IA (Ex: Aplicativo brasileiro de IA / Época Negócios): Sofrem com aquecimento excessivo (thermal throttling) no painel do veículo, perda de frames, falta de latência determinística devido ao Garbage Collector do SO móvel e interrupções por chamadas ou notificações.

3\. Protótipos Acadêmicos em Python (Ex: Sistema com IA desenvolvido por estudantes / CNN Brasil): Apresentam alto consumo de memória e baixa taxa de quadros (FPS) em computadores de bordo sem GPU dedicada.



\#### O Diferencial Técnico Deste Projeto:

\* Execução Nativa em C/C++: Código compilado diretamente para a arquitetura ARM da Raspberry Pi 4, garantindo consumo de RAM inferior a 50 MB e uso eficiente da CPU.

\* Arquitetura Multithreading Determinística: Separação do software em 4 POSIX Threads (capture, processing, gpio e telemetry). Isso garante que operações de I/O de rede ou exibição gráfica nunca bloqueiem o loop crítico de captura e análise visual.

\* Resposta de Baixíssima Latência (< 100 ms): Acionamento direto dos pinos GPIO via biblioteca libgpiod assim que o evento crítico é validado, garantindo tempo de reação imediato para alertar o motorista.

\* Operação Offline-First com Telemetria Desacoplada: O alarme local via hardware funciona independentemente da conectividade de rede. Caso haja conexão, a quarta thread envia os logs de alerta para o servidor da frota sem impactar o desempenho do sistema.



\### 1.3. Objetivos do Projeto

\* Objetivo Geral: Desenvolver e validar um sistema embarcado autônomo em C/C++ para detecção de sonolência e distração de motoristas em tempo real utilizando a SmartCam S600, executando acionamento sonoro local determinístico e envio de telemetria remota.

\* Objetivos Específicos:

&#x20; \* Implementar captura contínua de vídeo da SmartCam S600 via API V4L2 (Video4Linux2 / USB UVC).

&#x20; \* Projetar uma arquitetura multithreading determinística utilizando POSIX Threads (pthread) sob o modelo Produtor-Consumidor.

&#x20; \* Desenvolver algoritmo geométrico de visão computacional para cálculo da Razão de Abertura Ocular (Eye Aspect Ratio - EAR) e orientação da cabeça.

&#x20; \* Emitir alerta sonoro via GPIO em menos de 100 ms após a confirmação de evento de risco.

&#x20; \* Transmitir dados de telemetria (ID do veículo, timestamp, tipo de evento e valor do EAR) para um servidor central remoto via soquetes em thread dedicada.



\### 1.4. Escopo do Projeto

\* Dentro do Escopo:

&#x20; \* Sistema embarcado voltado para a cabine de veículos de carga.

&#x20; \* Integração da SmartCam S600 à Raspberry Pi 4.

&#x20; \* Aplicação nativa em C/C++ estruturada em 4 threads (Captura, Processamento, Atuação e Telemetria).

&#x20; \* Acionamento de buzzer via GPIO.

&#x20; \* Comunicação com servidor de logs via protocolo de rede (Sockets/HTTP).

&#x20; \* Automação de compilação via Makefile e documentação de engenharia.

\* Fora do Escopo:

&#x20; \* Intervenção mecânica no sistema de freios ou direção do veículo.

&#x20; \* Modificação do firmware interno da SmartCam S600.



\---



\## 2. Fundamentação Científica dos Limiares Temporais



A diferenciação entre uma piscada fisiológica espontânea e um evento de microsono baseia-se na métrica PERCLOS (Percentage of Eye Closure, Wierwille et al., 1994) e na análise temporal do Eye Aspect Ratio (Soukupová \& Čech, 2016).



Estudos fisiológicos sobre parâmetros oculares (Caffier et al., 2003) estabelecem os seguintes intervalos de tempo:

\* Piscada Reflexa Normal: Dura entre 100 ms e 300 ms.

\* Piscada Prolongada / Fadiga Inicial: Dura entre 300 ms e 500 ms.

\* Microsono / Estado Crítico de Fadiga: Fechamento palpebral sustentado por período superior a 500 ms (Garcia et al., 2023).



\### Mapeamento Temporal para o Algoritmo (a 30 FPS):



Para converter o tempo limite de detecção em quantidade de quadros acumulados, utiliza-se a relação:



Frames = Tempo Limite (s) \* FPS da Câmera



Considerando o fluxo da SmartCam S600 capturado a 30 FPS, cada frame possui a duração aproximada de 33,3 ms:



| Estado do Condutor | Duração Real (Tempo) | Qtd. de Frames a 30 FPS | Ação do Algoritmo |

| :--- | :--- | :--- | :--- |

| Piscada Normal | 100 ms – 300 ms | 3 a 9 frames | Descartar / Incrementar contador normal |

| Microsono (Fadiga) | > 500 ms | >= 15 frames consecutivos | Disparar Alarme Sonoro Local (Buzzer) |

| Sono Profundo / Incapacidade | > 1,5 s | >= 45 frames consecutivos | Transmitir Alerta Crítico para a Nuvem |



\---



\## 3. Requisitos do Sistema



\### 3.1. Requisitos Funcionais (RF)

\* \[RF01] Captura de Vídeo: O sistema deve adquirir quadros da SmartCam S600 via V4L2/USB em resolução 640x480 a 30 FPS.

\* \[RF02] Detecção de Sonolência: O sistema deve identificar o fechamento ocular sustentado por mais de 15 frames consecutivos (> 500 ms) utilizando o cálculo do EAR (Soukupová \& Čech, 2016).

\* \[RF03] Detecção de Distração: O sistema deve gerar alerta quando o desvio angular da cabeça em relação à via persistir por mais de 2 segundos.

\* \[RF04] Atuação Local: O sistema deve acionar o buzzer ativo via GPIO com latência determinística ao confirmar evento crítico.

\* \[RF05] Telemetria de Frota: O sistema deve enviar dados do evento (ID do veículo, tipo de alerta, timestamp e EAR) para um servidor central sem afetar o FPS da captura.



\### 3.2. Requisitos Não-Funcionais (RNF)

\* \[RNF01] Desempenho (Throughput): Manter a taxa de processamento entre 25 e 30 FPS na Raspberry Pi 4 Model B.

\* \[RNF02] Latência de Atuação Local: O tempo entre a validação do 15º frame crítico e o disparo do pino GPIO deve ser menor que 100 ms.

\* \[RNF03] Isolamento da Rede (Desacoplamento): Eventuais falhas ou quedas na rede de telemetria não podem bloquear as threads de captura, processamento ou alarme local.

\* \[RNF04] Pegada de Memória (Footprint): O consumo de RAM da aplicação embarcada deve ser inferior a 50 MB.



\---



\## 4. Estrutura Analítica do Produto (EAP / WBS)



1\. Sistema de Monitoramento do Motorista (DMS)

│

├── 1.1 Gerenciamento do Projeto e Engenharia de Requisitos

│   ├── 1.1.1 Proposta do Projeto, TAP e Escopo (PC1)

│   ├── 1.1.2 Modelagem da Arquitetura de Threads e Sincronização (PC2)

│   └── 1.1.3 Relatório de Desempenho e Validação Final (PC3)

│

├── 1.2 Infraestrutura de Hardware e Módulos

│   ├── 1.2.1 Integração da Câmera SmartCam S600 via USB/V4L2

│   ├── 1.2.2 Montagem do Circuito do Buzzer (GPIO + Transistor de Acionamento)

│   └── 1.2.3 Configuração do Sistema Operacional (Linux Embarcado na RPi 4)

│

├── 1.3 Arquitetura de Software em C/C++ (POSIX Threads)

│   ├── 1.3.1 Thread de Captura (capture\_thread via V4L2/USB)

│   ├── 1.3.2 Thread de Processamento (processing\_thread / Algoritmo EAR)

│   ├── 1.3.3 Thread de Atuação Local (gpio\_thread / Controle do Buzzer)

│   ├── 1.3.4 Thread de Telemetria (server\_comm\_thread / Sockets HTTP)

│   └── 1.3.5 Mecanismos de Sincronização (Buffer Circular, Mutexes e CondVars)

│

├── 1.4 Infraestrutura do Servidor Remoto

│   ├── 1.4.1 Configuração da API REST / Server Socket para Recepção de Dados

│   ├── 1.4.2 Banco de Dados de Alertas e Log de Telemetria de Frota

│   └── 1.4.3 Painel/Dashboard de Exibição de Alertas em Tempo Real

│

└── 1.5 Integração, Otimização e Testes

&#x20;   ├── 1.5.1 Validação da Latência de Detecção (Aferição de Frames vs. Tempo)

&#x20;   ├── 1.5.2 Testes de Resiliência de Conexão com o Servidor (Modo Offline/Online)

&#x20;   └── 1.5.3 Teste de Carga de CPU, Consumo de RAM e Desempenho Térmico



\---



\## 5. Referências Bibliográficas



\* ABRAMET - Associação Brasileira de Medicina de Tráfego. (2021). \*Sono e Fadiga na Direção: Impactos na Segurança Viária\*. São Paulo: ABRAMET.

\* Caffier, P. P., Erdmann, U., \& Ullsperger, P. (2003). \*Experimental evaluation of eye blink parameters for drowsiness detection\*. Ergonomics, 46(14), 1475-1496.

\* Garcia, R., Santos, M., \& Oliveira, L. (2023). \*Real-Time Machine Learning-Based Driver Drowsiness Detection System\*. Sensors, MDPI, 23(8), 4102.

\* Raspberry Pi Trading. (2019). \*Raspberry Pi 4 Model B Product Brief\*. Raspberry Pi Foundation.

\* Soukupová, T., \& Čech, J. (2016). \*Real-time eye blink detection using facial landmarks\*. 21st Computer Vision Winter Workshop (CVWW), 1-8.

\* Wierwille, W. W., Wreggit, S. S., Kirn, C. L., Ellsworth, L. A., \& Fairchild, R. J. (1994). \*Research on vehicle-based driver status/drowsiness monitoring\*. National Highway Traffic Safety Administration (NHTSA) Technical Report, DOT HS 808 247.

