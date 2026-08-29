# Driver Drowsiness & Distraction Detection System (DMS)

Sistema embarcado autônomo e de alta performance desenvolvido em C/C++ para detecção de sonolência e distração de motoristas em tempo real. O sistema utiliza a câmera SmartCam S600 integrada a uma Raspberry Pi 4 Model B, executando um pipeline multithreading determinístico com atuação local via GPIO e telemetria remota desacoplada.

Este repositório faz parte das avaliações práticas da disciplina Sistemas Operacionais Embarcados da Universidade de Brasília (UnB).

---

## Integrantes da Dupla

* Vinícius Eduardo Lopes da Silva - Matrícula: 202024061
* Gabriel Hajime Yamaoka Sebata - Matrícula: 200018108

---

## O Cenário: A Threat Behind the Wheel

Imagine um motorista de caminhão enfrentando a terceira hora seguida de viagem em uma rodovia sem iluminação na madrugada. O cansaço se acumula de forma silenciosa. Aos poucos, as pálpebras começam a pesar e a velocidade de reação diminui drasticamente. Em um piscar de olhos prolongado — o chamado microsono —, o veículo percorre dezenas de metros completamente sem controle. Não há tempo para frear; a colisão acontece antes mesmo que o motorista perceba o perigo.

Segundo dados da Associação Brasileira de Medicina de Tráfego (ABRAMET, 2021), o sono e a fadiga estão associados a até 40% dos acidentes de trânsito no Brasil. A probabilidade de acidentes graves durante a madrugada chega a ser mais de três vezes maior do que no período diurno, e colisões causadas por microsonos se destacam pela ausência de marcas de frenagem na pista.

A maioria das soluções de visão computacional convencionais falha justamente no momento de maior risco: na escuridão da cabine durante a noite ou por falta de latência determinística no acionamento do alarme.

---

## A Solução Embarcada

Para responder a essa ameaça silenciosa, este projeto propõe o desenvolvimento de um Driver Monitoring System (DMS) autônomo, não invasivo e de baixíssima latência voltado para o setor de transporte de cargas e gestão de frotas rodoviárias.

Utilizando a câmera inteligente **SmartCam S600** integrada a uma **Raspberry Pi 4 Model B**, o sistema enxerga com clareza o rosto do motorista na cabine do veículo com alta estabilidade de sinal via interface USB/UVC.

Desenvolvida em C nativo sobre o sistema operacional Linux embarcado na Raspberry Pi 4, a aplicação processa o fluxo de vídeo via POSIX Threads (pthread) sob o modelo Produtor-Consumidor para extrair marcos faciais e monitorar em tempo real:

* **Fadiga / Microsono:** Cálculo da Razão de Abertura Ocular (Eye Aspect Ratio - EAR) com validação temporal ($\ge 15$ frames a 30 FPS, equivalente a $\ge 500\text{ ms}$, segundo Soukupová & Čech, 2016 e Wierwille et al., 1994).
* **Distração:** Verificação do desvio angular da cabeça em relação à via por tempo superior a 2 segundos.
* **Telemetria Desacoplada:** Transmissão de logs e eventos de risco para um servidor central sem bloquear o loop visual local.

Ao identificar o estado crítico, o sistema aciona de forma determinística um alarme sonoro via GPIO em menos de 100 ms, tempo suficiente para acordar o motorista e evitar uma tragédia.

---

## Arquitetura de Hardware

| Componente | Função |
| :--- | :--- |
| **Raspberry Pi 4 Model B** | Processamento central (Broadcom BCM2711 Quad-Core ARM Cortex-A72 @ 1.5GHz) |
| **SmartCam S600** | Captura contínua de fluxo de vídeo (Interface USB / V4L2 UVC) |
| **Buzzer Ativo + Transistor** | Atuador de alarme sonoro de baixa latência via pino GPIO (`libgpiod`) |

---

## Arquitetura de Software & SOE

O software é estruturado em C/C++ utilizando POSIX Threads (`pthread`) sob o modelo Produtor-Consumidor com buffers circulantes:

* **Thread 1 - Captura (`capture_thread`):** Interface direta com V4L2 (Video4Linux2) para aquisição de quadros da SmartCam S600 sem bloqueio de memória (640x480 a 30 FPS).
* **Thread 2 - Processamento (`processing_thread`):** Extração de pontos de controle faciais e cálculo geométrico do EAR e orientação facial.
* **Thread 3 - Atuação (`gpio_thread`):** Monitoramento dos contadores de alerta e acionamento direto do buzzer via registradores/GPIO com latência < 100 ms.
* **Thread 4 - Telemetria (`server_comm_thread`):** Envio assíncrono de alertas e métricas para servidor remoto via Sockets/HTTP.

---

## Documentação do Projeto

* [PC1 - Proposta do Projeto (TAP, Requisitos e EAP)](./PC1_Proposta_do_Projeto.md)

---

## Estrutura do Repositório

```text
.
├── docs/                      # Diagramas e documentação adicional
├── include/                   # Arquivos de cabeçalho (.h)
├── src/                       # Código-fonte em C/C++
│   ├── main.c                 # Inicialização e gerenciamento de pthreads
│   ├── capture.c              # Thread de captura de vídeo V4L2 (SmartCam S600)
│   ├── detector.c             # Algoritmo de visão computacional (EAR)
│   ├── gpio.c                 # Manipulação dos pinos GPIO (Buzzer)
│   └── telemetry.c            # Thread de telemetria remota
├── Makefile                   # Script de compilação
├── PC1_Proposta_do_Projeto.md # Documentação da Proposta (PC1)
└── README.md                  # Apresentação principal