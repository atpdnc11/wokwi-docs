---
title: Referência do wokwi-pi-pico
sidebar_label: wokwi-pi-pico
---

Raspberry Pi Pico, uma placa microcontrolada RP2040 com processador ARM Cortex-M0+dual-core, 264k de RAM interna e flexível
Recurso de I/O programável (PIO).

![Raspberry Pi Pico](wokwi-pi-pico.svg)

## Nome dos Pinos

Os pinos GP0 a GP22 são pinos GPIO digitais. Os pinos GP26, GP27 e GP28 são pinos GPIO digitais com função de entrada analógica.

| Nome            | Descrição                     | Canal de entrada analógica |
| --------------- | ----------------------------- | -------------------------- |
| GP0 … GP22      | Pinos digitais GPIO (0 a 22)  |                            |
| GP26            | Pino digital GPIO 26          | 0                          |
| GP27            | Pino digital GPIO 27          | 1                          |
| GP28            | Pino digital GPIO 28          | 2                          |
| GND.1 … GND.8   | Pinos de aterramento \*       |                            |
| VSYS, VBUS, 3V3 | Fonte de alimentação positiva |                            |
| TP4 †           | Pino digital GPIO 23          |                            |
| TP5 †           | Pino digital GPIO 25 + LED    |                            |

\* Os números dos pinos físicos dos pinos de aterramento são 3, 8, 13, 18, 23, 28, 33 e 38.
† Esses pinos não aparecem no editor de diagrama visual, mas você pode usá-los em seu arquivo [diagram.json](../diagram-format).

Os pinos 3V3_EN / RUN / ADC_VREF não estão disponíveis na simulação e, portanto, são omitidos na tabela.

### LED integrado

O Rasberry Pi Pico tem um LED integrado, conectado ao pino GPIO 25. O LED acende quando o pino é elevado.

Você também pode usar a constante `LED_BUILTIN` para fazer referência ao LED em seu código Arduino:

```cpp
pinMode(LED_BUILTIN, OUTPUT);
digitalWrite(LED_BUILTIN, HIGH);
```

Veja o [Blink](https://wokwi.com/arduino/projects/297755575592157709) para um exemplo de código completo.

## Recursos do simulador

O Raspberry Pi Pico é simulado usando a [Biblioteca RP2040js](https://github.com/wokwi/rp2040js).
Esta tabela resume o status dos recursos da simulação:

| Periférico               | Status | Notas                                                |
| ------------------------ | ------ | ---------------------------------------------------- |
| Núcleo do processador    | ✔️     | Apenas um único núcleo é simulado                    |
| GPIO                     | 🟡     | Entrada/saída funcionando, interrupções ausentes     |
| PIO                      | ❌     |                                                      |
| USB                      | ❌     |                                                      |
| UART                     | 🟡     | Apenas TX (envio de dados do Pico para o computador) |
| I2C                      | ❌     |                                                      |
| SPI                      | ❌     |                                                      |
| PWM                      | ❌     |                                                      |
| Timer                    | ✔️     | A pausa do cronômetro ainda não foi implementada     |
| ARM SysTick Timer        | 🟡     | Implementação parcial                                |
| Watchdog                 | ❌     |                                                      |
| RTC                      | ❌     |                                                      |
| ADC + Sensor Temperatura | ❌     |                                                      |
| SSI                      | 🟡     | Apenas o mínimo para deixar o bootloader feliz       |
| GDB Debugging            | 🟡     | Implementado, mas sem a interface web-gdb            |

Legenda:
✔️ Simulado
🟡 Implementação parcial/trabalho em andamento
❌ Não implementado

Estamos adicionando os recursos que faltam em [transmissões ao vivo semanais](https://www.youtube.com/playlist?list=PLLomdjsHtJTxT-vdJHwa3z62dFXZnzYBm). Espere que a lista acima seja atualizada a cada uma ou duas semanas.

### Arduino core

O núcleo do Arduino fornece as funções integradas do Arduino, como `pinMode()` e `digitalRead()`, bem como um conjunto de bibliotecas padrão do Arduino, como Servo, Wire e SPI.

Ao compilar seu código para o Raspberry Pi Pi Pico, você pode escolher entre dois núcleos diferentes:

- O [núcleo oficial do Pi Pico](https://github.com/arduino/ArduinoCore-mbed), baseado no sistema operacional Mbed. Este é o padrão.
- [Mantido pela comunidade Pi Pico Arduino Core](https://github.com/earlephilhower/arduino-pico), construído sobre o [Pi Pico SDK](https://github.com/raspberrypi/pico-sdk).

Você pode aprender sobre as principais diferenças entre esses dois núcleos [neste comentário do GitHub](https://github.com/earlephilhower/arduino-pico/issues/117#issuecomment-830356795).

Para selecionar um núcleo, defina o atributo "env" da parte `wokwi-pi-pico`. Para o núcleo oficial do Arduino, use o valor "arduino-core". Para o núcleo mantido pela comunidade, defina "env" como "arduino-community". por exemplo.:

```json
  "parts": [
    {
      "type": "wokwi-pi-pico",
      "id": "pico",
      "attrs": {
        "env": "arduino-community"
      }
      …
    },
    …
  ]
```

### Monitor Serial

Você pode usar o Serial Monitor para receber informações do código em execução no Pi Pico, como impressões de depuração. Para configurar a conexão do monitor serial com o Raspberry Pi Pico, adicione as seguintes conexões ao seu arquivo [diagram.json](../diagram-format#onnections):

```json
  "connections": [
    [ "$serialMonitor:RX", "pico:GP0", "", [] ],
    [ "$serialMonitor:TX", "pico:GP1", "", [] ],
    …
  ]
```

O exemplo assume que o Pi Pico foi definido com um id de "pico", por ex.

```json
  "parts": [
    {
      "type": "wokwi-pi-pico",
      "id": "pico",
      …
    },
    …
  ]
```

Para inicializar o monitor Serial em seu código, use `Serial1.begin(115200)`, e então imprima as mensagens com `Serial1.println()`. Por exemplo:

```cpp
void setup() {
  Serial1.begin(115200);
  Serial1.println("Hello, world!");
}

void loop() { }
```

Observe o uso de `Serial1`. O `Serial` padrão no Arduino Core usa Serial over USB (CDC), que atualmente não é suportado na simulação. `Serial1`, em contraste, usa o hardware UART (conectado aos pinos GP0/GP1).

Para um exemplo completo, confira o [Exemplo de monitor serial Pi Pico](https://wokwi.com/arduino/projects/297755360074138125).

## Exemplos no simulador

- [LCD1602 com Pi Pico](https://wokwi.com/arduino/projects/297323005822894602)
- [Semáforo com Pi Pico](https://wokwi.com/arduino/projects/297322571959894536)
- [Pi Pico C++ SDK Blink](https://wokwi.com/arduino/projects/298013072042230285)
- [Pi Pico C++ SDK 7-Segment Example](https://wokwi.com/arduino/projects/298014884249993738)
