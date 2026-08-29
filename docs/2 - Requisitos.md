# | ID | Nome do Requisito | Descrição | Prioridade | Responsável | Observações |

# | :--- | :--- | :--- | :--- | :--- | :--- |

# | RF01 | Captura de Vídeo V4L2 | O sistema deve capturar o fluxo de vídeo da câmera SmartCam S600 via API Video4Linux2 (V4L2) em resolução 640x480 a 30 FPS. | Alta | Vinícius | Executado na capture\_thread. |   

# | RF02 | Detecção de Marcos Faciais | O algoritmo deve localizar o rosto do condutor e extrair os pontos anatômicos dos olhos e da face em tempo real. | Alta | Gabriel | Módulo de visão em C/C++. |

# | RF03 | Cálculo do EAR | O sistema deve calcular a Razão de Abertura Ocular (Eye Aspect Ratio) a cada frame processado. | Alta | Gabriel | Métrica para identificar o fechamento das pálpebras. |

# | RF04 | Detecção de Microsono | O sistema deve identificar evento de fadiga quando o EAR < 0.2 persistir por >= 15 frames consecutivos (>= 500 ms). | Alta | Gabriel | Validação baseada em literatura (Soukupová \& Čech). |

# | RF05 | Detecção de Distração | O sistema deve identificar distração quando houver desvio angular da cabeça em relação à pista por tempo > 2 s. | Média | Gabriel | Monitoramento de orientação facial. |

# | RF06 | Atuação Sonora Local | O sistema deve acionar o buzzer ativo via pino GPIO imediatamente ao confirmar um evento de microsono ou distração. | Alta | Vinícius | Executado na gpio\_thread. |

# | RF07 | Desativação do Alarme | O alarme sonoro deve ser interrompido automaticamente assim que o condutor retornar ao estado de atenção (EAR >= 0.2). | Alta | Vinícius | Controle de estado via registradores da GPIO. |

# | RF08 | Geração de Logs de Eventos | O sistema deve registrar localmente cada evento crítico contendo timestamp, ID do veículo, tipo de alerta e valor do EAR. | Média | Gabriel | Salvo em memória/buffer de telemetria. |

# | RF09 | Envio de Telemetria Remota | O sistema deve transmitir os eventos críticos para um servidor central/nuvem via soquete TCP/IP ou HTTP. | Média | Vinícius | Executado de forma assíncrona na server\_comm\_thread. |

# | RF10 | Gerenciamento de Threads | O sistema deve utilizar POSIX Threads (pthread) com controle de concorrência via mutex e variáveis de condição. | Alta | Vinícius | Padrão Produtor-Consumidor. |

# | RNF01 | Latência Determinística | O tempo entre a detecção do microsono pelo algoritmo e o acionamento físico do buzzer via GPIO deve ser menor que 100 ms. | Alta | Vinícius | Requisito de tempo real estrito. |

# | RNF02 | Desempenho de Vídeo | O pipeline visual deve manter uma taxa constante de processamento de pelo menos 30 quadros por segundo (FPS). | Alta | Gabriel | Evita perda de frames durante a viagem. |

# | RNF03 | Isolamento de Falhas | Falhas de conexão na transmissão de telemetria para a nuvem não devem bloquear ou atrasar a captura de vídeo nem o alarme local. | Alta | Vinícius | Desacoplamento via threads independentes. |

# | RNF04 | Baixo Consumo de Memória | O consumo total de memória RAM do sistema embarcado não deve ultrapassar 100 MB na Raspberry Pi 4. | Média | Ambos | Código otimizado em C/C++ sem uso de linguagens interpretadas. |

# | RNF05 | Operação em Baixa Luminosidade | A captura de vídeo e a extração de marcos faciais devem funcionar de forma estável sob iluminação noturna na cabine. | Alta | Gabriel | Garantido pelo suporte UVC da SmartCam S600. |

# | RNF06 | Inicialização Autônoma | O sistema deve iniciar a execução do pipeline de monitoramento automaticamente ao energizar a placa Raspberry Pi. | Média | Vinícius | Configurado via serviço systemd no Linux. |

