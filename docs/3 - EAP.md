# | ID | Componente | Descrição | Dados Técnicos | Comentários |

# | :--- | :--- | :--- | :--- | :--- |

# | 1 | Sub-sistema: Hardware | Componentes físicos e eletrônicos do sistema embarcado. | Plataforma embarcada e periféricos de I/O | Módulos para captura, processamento e alarme local. |

# | 1.1 | Processamento | Placa de computação principal para execução das threads. | Raspberry Pi 4 Model B (Broadcom BCM2711 Quad-Core @ 1.5GHz) | Executa o SO Linux e a aplicação nativa em C/C++. |

# | 1.2 | Sensor 1 | Câmera inteligente para aquisição contínua do rosto do motorista. | SmartCam S600 (Interface USB / UVC Video4Linux2) | Captura fluxo de vídeo em 640x480 a 30 FPS. |

# | 1.3 | Sensor 2 | N/A (Reservado para expansão de telemetria) | N/A | Não utilizado na versão atual do protótipo. |

# | 1.4 | Controle 1 | Pino I/O digital para acionamento do alarme sonoro local. | Pino GPIO (Broadcom) controlado via libgpiod | Saída digital acionada pela gpio\_thread. |

# | 1.5 | Controle 2 | Circuito de potência para interfaceamento com o atuador. | Transistor NPN (BC547) + Resistor de base (1k ohm) | Chaveamento para acionar o buzzer com segurança. |

# | 1.6 | Comunicação 1 | Interface USB para transferência do fluxo de vídeo. | Barramento USB 2.0/3.0 (UVC Standard) | Conecta a SmartCam S600 à Raspberry Pi 4. |

# | 1.7 | Comunicação 2 | Módulo de rede para envio de dados de telemetria. | Interface Ethernet / Wi-Fi (802.11ac) | Transmissão de alertas para o servidor da frota. |

# | 1.8 | Alimentação | Fonte de energia elétrica contínua para a placa e periféricos. | Fonte regulada 5V / 3A USB-C | Alimenta a Raspberry Pi 4 e a SmartCam S600. |

# | 2 | Sub-sistema: Software | Aplicação nativa em C/C++ estruturada em POSIX Threads. | Arquitetura Multithreading (pthread) | Modelo Produtor-Consumidor em Linux Embarcado. |

# | 2.1 | Controle | Thread de atuação local para gerenciamento do alarme sonoro. | gpio\_thread (libgpiod em C) | Aciona o buzzer com latência determinística < 100 ms. |

# | 2.2 | Navegação | Pipeline de visão computacional e extração de métricas. | processing\_thread (Algoritmo EAR / Visão Geométrica) | Calcula a abertura ocular e detecta o microsono. |

# | 2.3 | Interface | Thread de aquisição contínua do fluxo de vídeo. | capture\_thread (API V4L2 - Video4Linux2) | Alimenta o buffer circular com quadros do motorista. |

# | 2.4 | Diagnóstico | Thread de telemetria para registro e envio de alertas remotos. | server\_comm\_thread (Sockets TCP/IP) | Envia logs de fadiga/EAR sem bloquear o loop local. |

# | 2.5 | Sincronização | Mecanismo de controle de concorrência entre threads. | Mutexes (pthread\_mutex\_t) e CondVars | Evita condição de corrida no acesso aos buffers. |

# | 2.6 | Gestão de Memória | Estrutura de dados para troca contínua de quadros. | Buffer Circular em RAM (< 50 MB de footprint) | Garante tempo real sem estourar o consumo de memória. |

# | 2.7 | Sistema Operacional | Kernel Linux configurado para a placa de desenvolvimento. | Raspberry Pi OS (Debian 64-bit) | Gerencia a execução das pthreads e drivers V4L2/GPIO. |

# | 3 | Sub-sistema: Estrutura | Fixação física e invólucro do sistema na cabine. | Suportes de montagem e cabeamento | Garante a integridade mecânica no veículo. |

# | 3.1 | Chassi | Gabinete protetor da unidade de processamento central. | Case acrílico/ABS para Raspberry Pi 4 | Protege a placa contra poeira e vibrações do veículo. |

# | 3.2 | Suporte | Suporte ajustável para fixação da câmera no painel. | Suporte articulado com ventosa/adesivo 3M | Posiciona a SmartCam S600 alinhada ao rosto do condutor. |

# | 3.3 | Carenagem | Proteção física do circuito de atuação local. | Caixa patola / Protoboard de testes de bancada | Abriga o transistor, resistores e o buzzer ativo. |

# | 3.4 | Atuadores | Transdutor acústico para emissão de alerta sonoro ao motorista. | Buzzer Ativo 5V (Piezocélula Sonora) | Emite sinal sonoro de emergência ao confirmar microsono. |

# | 3.5 | Transmissão | Chicotes e cabos para conexão entre os módulos. | Cabo USB blindado e jumpers de conexão GPIO | Interconecta câmera, placa de processamento e circuito. |

# | 3.6 | Rodas/Hélices | N/A (Sistema fixo instalado na cabine do caminhão) | N/A | Não se aplica a sistemas de monitoramento estáticos. |

