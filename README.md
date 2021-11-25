# 21b - Avaliação Prática 2

Leia atentamente as informações a seguir:

1. Atualize o arquivo `ALUNO.json` com seu nome e e-mail e faça um commit imediatamente.
1. **Faça o seu trabalho de maneira ética!**
1. **A cada 30 minutos você deverá fazer um commit no seu código!**
    - Códigos que não tiverem commit a cada 30min ou que mudarem drasticamente entre os commits serão descartados (conceito I) !!
    - Você deve inserir mensagens condizentes nos commits!
1. Duração: 3h
1. Usar o código exemplo deste repositório, ele já possui:
    - Diplay do módulo OLED1
    - TC/ RTT/ RTC/ AFEC já adicionados no ASF Wizard
    - Deve utilizar o rtos e suas funcionalidades.

:triangular_flag_on_post: **Ao finalizar a entrega preencher o formulário (uma única vez!):**

## Descrição

Nesta entrega vocês irão desenvolver um controlador para uma máquina de café expresso! O sistema possui dois botões para o usuário selecionar a quantidade de café: Simples ou Duplo. A ideia aqui é controlar o quanto de café será feito através do tempo na qual a bomba da máquina fica ligada.

Antes de permitir que o usuário sedento por cafeína faca o café você deve iniciar o aquecimento da máquina e garantir que ela esteja quente o suficiente antes de iniciar a passagem da água. Isso será realizado lendo a temperatura da máquina via uma entrada analógica. O controle do aquecedor será realizado via a chamada de duas funções.

![](img.png)

### Interação com a máquina

A máquina de café possui três dispositivos que vocês precisam interagir: Aquecedor (**FLOW HEATER**); Sensor de Temperatura da água e Bomba de água (**PUMP**), como detalhado a seguir:

![](https://knowyourgrinder.com/wp-content/uploads/2016/01/illy_competition_schema.png)

> REF: https://knowyourgrinder.com/wp-content/uploads/2016/01/illy_competition_schema.png

#### Aquecedor

Sistema ON/OFF que aquece a água para fazer o café, deve ser controlado com as funções a seguir:

``` c
// Controle do aquecimento
void coffee_heat_on(void);  // aquece a água
void coffee_heat_off(void); // desliga o aquecimento da água
```

> As funções já estão implementadas!

#### Sensor de temperatura

Você deve realizar a leitura analógica do pino **DAC0** que indica a temperatura atual do reservatório de água, para converter o valor lido na entrada analógica para graus célcios utilize a fórmula a seguir:

``` 
Temperatura_celcios =  100 * AFEC
                      ------------
                        4095
```

- **AFEC** é o valor digital lido via conversão analógica digital.

A temperatura do reservatório é gerada no pino DAC0, para ler o valor você deve conectar o pino em uma entrada analógica.

```c
  |----------|    temperatura
  |     dac0 |------->|
  |          |        |
  |     afec |<-------| ligar com jump!
  |----------|
```

![](jump.png)

> Dicas:
> 
> 1. Para leitura do dado analógico usar o AFEC0 canal 0 (EXT2 PIN 3)
> 1. 1s de taxa de amostragem é mais que o suficiente.

#### Bomba de água

O controle da bomba de água (que faz o café) deve ser realizado via as funções a seguir (já implementadas):

```c
// Controle da bomba
void coffee_pump_on();  // liga bomba
void coffee_pump_off(); // desliga bomba
```

Ao fazer o café a temperatura da água irá baixar, por conta disso você não deve verificar a temperatura da água enquanto estiver fazendo o café, apenas antes do inicio do processo.

#### Tipos de café 

A máquina deve produzir dois tipos de café: Simples e Duplo, a diferença entre eles é: o botão que ativa o modo, o tempo na qual o café a bomba ficará ligado e o LED que irá piscar quando o café estiver sendo feito.

| Modo    | Botão  | LED    | Tempo |
|---------|--------|--------|-------|
| Simples | OLED 1 | OLED 1 |  5s   |
| Simples | OLED 1 | OLED 1 |  10s  |

#### LEDs

Os LEDS referentes a cada botão devem se comportar da seguinte maneira:

- Piscar enquanto a temperatura da máquina não chegou em 80 C
- Devem ficar acesos para indicar que a máquina está pronta para fazer um café.
- O LED do modo selecionado deve piscar enquanto estiver produzindo café, os demais devem manter apagado.
 
#### Comportamento

Ao ligar a máquina (placa energizada) o sistema deve começar a aquecer a água e bloquear qualquer ação do usuário de fazer café até a temperatura atingir 80C, enquanto isso todos os LEDs devem piscar, indicando que o aquecimento da água.

Com a água atingido a temperatura certa, os LEDs devem ficar acesos e a máquina já pode começar fazer café. Quando um dos modos forem selecionados lembrar de ligar a bomba e deixar ela ligada pelo tempo definido.

Após acabar o processo de fazer café, o sistema deve verificar a temperatura da água antes de permitir que um novo café seja feito (a passagem da água esfria 'a água na câmara).

:bangbang: A máquina só deve liberar para fazer café quando a temperatura da água atingir pelo menos 80°C.

## C


Requisitos funcionais (resumo):

- Cafe simples/ Duplo funcionando
    - controlar a bomba com as funções: `coffee_pump_on` e `coffee_pump_off()`
- Aquecer a máquina chamando a função: `coffee_heat_on()`
- Só permitir que o café seja feito com a água no mínimo 80C.

Assista ao vídeo no youtube para ver o comportamento da máquina:

Requisitos técnicos:

- Usar RTT para calcular o tempo que a bomba vai ficar ligada.
- Usar TC para piscar os LEDs.
- Usar AFEC para leitura da temperatura.
- Usar fila e semáforos para troca de dados.

## B - stand-by

Identificar quando a máquina está inativa (20 segundos sem uso) e desligar o aquecimento da água e desligar os LEDs entrando em modo stand-by. Quando qualquer botão for apertado ligar novamente a máquina (com o mesmo comportamento de quando ela é ligada pela primeira vez).

> Enquanto em stand-by os LEDs devem ficar apagados

## B - oled

Exibir no OLED informações sobre a feitura do café:

  - modo de café selecionado
  - temperatura atual da água 
  - barra de progresso de quando estiver fazendo café.

## A - Tempo

Possibilitar que o usuário altere o tempo de cada tipo de café (Simples, Duplo). Se o usuário apertar e manter o botão pressionado o tempo na qual ele manteve o botão pressionado deve ser a nova base de tempo para o modo em questão. Enquanto o botão estiver pressionado lembre de ligar a bomba, e quando soltar desligar!