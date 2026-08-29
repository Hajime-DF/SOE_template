# \# Termo de Abertura do Projeto (TAP)

# 

# \*\*Nome do Projeto:\*\* Driver Drowsiness \& Distraction Detection System (DMS)  

# \*\*Data de Início:\*\* 23/08/2026  

# \*\*Data de Término:\*\* 04/12/2026  

# 

# \---

# 

# \## Visão Geral do Projeto

# 

# \### Descrição do Problema

# A fadiga, o sono e a distração ao volante são fatores críticos que afetam diretamente a segurança no transporte rodoviário. Segundo a Associação Brasileira de Medicina de Tráfego (ABRAMET, 2021), o sono e a fadiga estão associados a até 40% dos acidentes de trânsito no Brasil. O problema afeta principalmente motoristas profissionais de transporte de cargas e gestores de frotas rodoviárias. Durante a madrugada, acidentes por microsono são até três vezes mais prováveis e caracterizam-se pela ausência de marcas de frenagem na pista, resultando em episódios de alto risco humano e elevado prejuízo material. As soluções convencionais de visão computacional costumam falhar pela falta de iluminação na cabine ou por latência imprevisível ao disparar alarmes em tempo real.

# 

# \### Estado da Arte

# Atualmente, as abordagens para monitoramento de fadiga dividem-se em três pilares principais:

# 1\. \*\*Sistemas Veiculares Comerciais Dedicados:\*\* Equipamentos proprietários e integrados à telemetria de grandes frotas (ex: sensores de fadiga industriais). Possuem alta eficiência, porém dependem de ecossistemas fechados e apresentam alto custo de aquisição.

# 2\. \*\*Aplicativos de Smartphone com IA:\*\* Soluções baseadas em telefones celulares fixados ao painel. Apesar de acessíveis, sofrem com estresse térmico (\*thermal throttling\*), falta de latência determinística devido ao \*Garbage Collector\* dos SOs móveis e interrupções por chamadas ou notificações.

# 3\. \*\*Protótipos Acadêmicos em Python/OpenCV:\*\* Projetos baseados em redes neurais em linguagem Python (ex: pesquisas universitárias com OpenCV). Embora funcionais, demandam alto processamento e resultam em baixa taxa de quadros (FPS) em sistemas embarcados sem GPU dedicada.

# 

# O diferencial deste projeto é a execução nativa em C/C++ sobre Linux Embarcado na Raspberry Pi 4 Model B integrada à câmera SmartCam S600, garantindo tempo de resposta determinístico (< 100 ms) via GPIO, baixo consumo de RAM (< 50 MB) e isolamento entre captura visual e telemetria remota via POSIX Threads.

# 

# \### Objetivos

# \* \*\*Objetivo Geral:\*\* Desenvolver e validar um sistema embarcado autônomo em C/C++ para detecção de sonolência e distração de motoristas em tempo real, executando acionamento sonoro local determinístico e envio de telemetria remota.

# \* \*\*Objetivos Específicos:\*\*

# &#x20; \* Implementar a captura contínua de vídeo da SmartCam S600 via API Video4Linux2 (V4L2/USB) a 30 FPS.

# &#x20; \* Projetar uma arquitetura multithreading em C/C++ (POSIX Threads) utilizando o modelo Produtor-Consumidor.

# &#x20; \* Desenvolver algoritmo geométrico para cálculo do \*Eye Aspect Ratio\* (EAR) e estimativa da orientação da cabeça.

# &#x20; \* Emitir alerta sonoro via GPIO (buzzer ativo) com latência inferior a 100 ms após a validação de evento crítico ($\\ge 15$ frames consecutivos com $EAR < 0.2$).

# &#x20; \* Enviar relatórios de eventos para um servidor central de telemetria sem impactar a taxa de quadros do processamento local.

# 

# \---

# 

# \## Escopo do Projeto

# 

# \* \*\*Dentro do Escopo:\*\*

# &#x20; \* Desenvolvimento de aplicação nativa em C/C++ para a plataforma Raspberry Pi 4 Model B.

# &#x20; \* Arquitetura multithreading dividida em 4 threads: Captura (V4L2), Processamento (EAR), Atuação (GPIO/Buzzer) e Telemetria (Sockets).

# &#x20; \* Integração da câmera SmartCam S600 via interface USB/UVC.

# &#x20; \* Circuito de atuação local via pino GPIO com transistor e buzzer ativo.

# &#x20; \* Envio assíncrono de logs de evento (timestamp, ID do veículo e EAR) para servidor remoto.

# &#x20; \* Documentação completa de engenharia, Makefile e estrutura do repositório.

# \* \*\*Fora do Escopo:\*\*

# &#x20; \* Modificação do firmware interno da câmera SmartCam S600.

# &#x20; \* Intervenção mecânica, elétrica ou atuadores no sistema de frenagem/direção do veículo.

# &#x20; \* Desenvolvimento de aplicativo móvel para smartphones (Android/iOS).

# 

# \---

# 

# \## Stakeholders

# 

# \* \*\*Professores e Avaliadores da Disciplina SOE (UnB):\*\* Acompanhamento, orientação técnica e avaliação acadêmica do projeto.

# \* \*\*Desenvolvedores do Projeto (Equipe):\*\* Vinícius Eduardo e Gabriel Hajime, responsáveis pelo projeto, implementação e validação do sistema embarcado.

# \* \*\*Empresas de Gestão de Frotas e Transporte de Cargas:\*\* Beneficiários finais indiretos interessados em soluções de baixo custo para redução de acidentes nas estradas.

# \* \*\*Motoristas Profissionais:\*\* Usuários finais operacionais que utilizam o dispositivo para prevenção de microsonos na cabine.

# 

# \---

# 

# \## Recursos do Projeto

# 

# \### Membros da Equipe

# 

# | Nome | Matrícula | Curso | Funções |

# | :--- | :--- | :--- | :--- |

# | Vinícius Eduardo Lopes da Silva | 202024061 | Engenharia Eletrônica | Desenvolvimento C/C++, V4L2 e Arquitetura de Threads |

# | Gabriel Hajime Yamaoka Sebata | 200018108 | Engenharia Eletrônica | Processamento de Imagem (EAR), GPIO e Telemetria |

# 

# \### Orçamento Estimado (R$)

# \* \*\*Raspberry Pi 4 Model B (4GB):\*\* R$ 550,00 \*(recurso próprio/disponível)\*

# \* \*\*Câmera SmartCam S600 (USB/UVC):\*\* R$ 180,00 \*(recurso próprio/disponível)\*

# \* \*\*Componentes Eletrônicos (Buzzer ativo, Transistor BC547, Resistores, Protoboard):\*\* R$ 25,00

# \* \*\*Cartão MicroSD 32GB Class 10:\*\* R$ 45,00

# \* \*\*Fonte Oficial 5V 3A USB-C:\*\* R$ 60,00

# \* \*\*Custo Total Estimado:\*\* \*\*R$ 860,00\*\* \*(utilizando componentes e módulos de bancada já disponíveis)\*

# 

# \### Esforço Estimado (horas)

# \* \*\*Engenharia de Requisitos e Documentação (PC1, PC2, PC3):\*\* 30 horas/membro

# \* \*\*Modelagem e Desenvolvimento de Código em C/C++ (Threads/V4L2/GPIO):\*\* 50 horas/membro

# \* \*\*Montagem de Hardware e Circuito Eletrônico:\*\* 15 horas/membro

# \* \*\*Testes de Validação, Latência e Ajustes de Desempenho:\*\* 25 horas/membro

# \* \*\*Esforço Total Estimado:\*\* \*\*120 horas por integrante\*\* (240 horas totais da equipe ao longo do semestre).

# 

# \---

# 

# \## Referências

# 

# \* ABRAMET - Associação Brasileira de Medicina de Tráfego. \*Sono e Fadiga na Direção: Impactos na Segurança Viária\*. São Paulo: ABRAMET, 2021.

# \* P. P. Caffier, U. Erdmann e P. Ullsperger, \*Experimental evaluation of eye blink parameters for drowsiness detection\*. Ergonomics, vol. 46, no. 14, pp. 1475-1496, 2003.

# \* R. Garcia, M. Santos e L. Oliveira, \*Real-Time Machine Learning-Based Driver Drowsiness Detection System\*. Sensors, MDPI, vol. 23, no. 8, p. 4102, 2023.

# \* T. Soukupová e J. Čech, \*Real-time eye blink detection using facial landmarks\*. 21st Computer Vision Winter Workshop (CVWW), pp. 1-8, 2016.

# \* W. W. Wierwille, S. S. Wreggit, C. L. Kirn, L. A. Ellsworth e R. J. Fairchild, \*Research on vehicle-based driver status/drowsiness monitoring\*. National Highway Traffic Safety Administration (NHTSA) Technical Report, DOT HS 808 247, 1994.

