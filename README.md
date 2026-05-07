# Controle de Velocidade de Motor DC com PWM e NodeMCU (Simulação Proteus)

Este projeto demonstra o controle de velocidade de um motor DC utilizando a técnica de PWM (Pulse Width Modulation). A simulação foi realizada no Proteus 8 Professional, utilizando um NodeMCU (ESP8266) programado através do VSCode com a extensão PlatformIO, configurado como um Arduino Uno (emulação).

## Sumário
1. [Introdução ao PWM](#introdução-ao-pwm)
2. [Componentes necessários](#componentes-necessários)
3. [Esquemático](#esquemático)
4. [Código-fonte](#código-fonte)
5. [Instruções de montagem](#instruções-de-montagem)
6. [Funcionamento do projeto](#funcionamento-do-projeto)

---

## 1. Introdução ao PWM
O **PWM (Pulse Width Modulation - Modulação por Largura de Pulso)** é uma técnica utilizada para obter resultados analógicos com meios digitais. O controle digital é usado para criar uma onda quadrada, um sinal alternando entre ligado (HIGH) e desligado (LOW).



Ao alterar o ciclo do trablho a porcentagem de tempo que o sinal permanece em nível alto em comparação com o tempo total de um ciclo, controlamos a tensão média entregue ao motor, alterando sua velocidade.

## 2. Componentes necessários
* **Microcontrolador:** NodeMCU V3 (ESP8266).
* **Driver de Motor:** L293D (Ponte H).
* **Atuador:** Motor DC.
* **Entrada:** Botão (Push-button).
* **Resistores:** 1x 0.1Ω ou 10kΩ (Pull-down para o botão conforme o esquema).
* **Alimentação:** Bateria de 5V.
* **Instrumentação:** Osciloscópio Virtual.

## 3. Esquemático
No Proteus, as conexões principais são:
* **D9 (NodeMCU)** conectado ao **EN1 (L293D)**: Entrada de PWM (Velocidade).
* **D2 (NodeMCU)** conectado ao **Botão**: Entrada de comando.



## 4. Código-fonte
O código utiliza a detecção de borda de subida para garantir que a velocidade aumente apenas uma vez por clique.

```cpp
#include <Arduino.h>

const int pinoBotao = 2;
const int pinoPWM   = 9;

int velocidade = 0;
bool ultimoEstadoBotao = LOW;

void setup() {
  pinMode(pinoBotao, INPUT);
  pinMode(pinoPWM, OUTPUT);

  analogWrite(pinoPWM, 0);
}

void loop() {
  int leitura = digitalRead(pinoBotao);

  if (leitura == HIGH && ultimoEstadoBotao == LOW) {
    velocidade += 64; 

    if (velocidade > 255) {
      velocidade = 0;
    }
    analogWrite(pinoPWM, velocidade);
  }
  ultimoEstadoBotao = leitura;
}
```
## 5. Instruções de montagem
1. No Proteus, certifique-se de que o pino GND do NodeMCU e do L293D estejam conectados ao mesmo terra.
2. O pino EN1 do L293D deve receber o sinal do pino definido como PWM no código.
3. Utilize o Osciloscópio conectado à saída PWM para observar a variação da largura de pulso conforme o botão é pressionado.

## 6. Funcionamento do projeto
* **Estado Inicial:** Motor parado, força = 0%.
* **Interação:** A cada clique no botão, a velocidade incrementa em 25% (Valores: 64, 128, 192, 255).
* **Reset:** Após atingir a velocidade máxima (100%), o próximo clique zera a velocidade (0%).
