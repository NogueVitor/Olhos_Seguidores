# Olhos Seguidores - Robô Interativo com Expressões Faciais

## 📚 Visão Geral

O **Olhos Seguidores** é um projeto de robô interativo desenvolvido em colaboração com meu colega de curso [Nicolas Wallace](https://github.com/nicolas-wallace) no final de 2024. O sistema exibe diferentes expressões faciais em um display OLED de 128x64 pixels, utilizando sensores ultrassônicos HC-SR04 para detecção de proximidade e botões para alterar emoções.

O robô "emocional" pode expressar diferentes estados: **neutro**, **feliz**, **triste**, **raiva**, além de possuir um efeito de **piscar** natural. Como carcaça para o projeto, foi utilizada uma caixa de papelão personalizada com uma folha impressa do corpo do personagem **8-Bits** do jogo **Brawl Stars**, criando uma apresentação temática e divertida.

**🎥 Veja o projeto em funcionamento:** [Demonstração prática dos Olhos Seguidores na carcaça do 8-Bits](https://www.youtube.com/watch?v=ng629rU_N2Q)

---

## Índice

- [Componentes Utilizados](#componentes-utilizados)
- [Funcionalidades Principais](#funcionalidades-principais)
- [Configuração dos Pinos](#configuração-dos-pinos)
- [Lógica de Funcionamento](#lógica-de-funcionamento)
- [Estrutura do Projeto](#estrutura-do-projeto)
- [Instalação e Configuração](#instalação-e-configuração)
- [Como Usar](#como-usar)
- [Expressões Faciais](#expressões-faciais)
- [Bibliotecas Necessárias](#bibliotecas-necessárias)
- [Simulação Online](#simulação-online)

## Componentes Utilizados

### Hardware Principal
- **Microcontrolador**: Arduino Uno
- **Display**: OLED SSD1306 (128x64 pixels)
- **Sensores**: 2x HC-SR04 (Ultrassônicos)
- **Controles**: 2x Botões (Push Button)
- **Resistores**: 2x 1kΩ (Pull-down dos botões)
- **Carcaça**: Caixa de papelão + Arte impressa do 8-Bits (Brawl Stars)

### Bibliotecas de Software
- **Wire** - Comunicação I2C
- **Adafruit GFX** - Gráficos e formas
- **Adafruit SSD1306** - Controle do display OLED

## Funcionalidades Principais

### 👁️ Movimentação dos Olhos
- **Detecção de Proximidade**: Os olhos se movem horizontalmente com base na distância detectada pelos sensores ultrassônicos esquerdo e direito
- **Algoritmo Inteligente**: A função `updateEyePosition()` ajusta a posição dos olhos de acordo com as distâncias medidas
- **Movimentação Suave**: Sistema de mapeamento que converte distâncias em posições na tela

### 🎭 Expressões Faciais
- **😐 Olhar Neutro**: Estado padrão do robô com olhos arredondados
- **😠 Olhar Bravo**: Ativado pelo botão vermelho, com sombreamento triangular nos cantos superiores dos olhos
- **😢 Olhar Triste**: Ativado pelo botão azul, com sombreamento triangular nos cantos inferiores dos olhos
- **😊 Olhar Feliz**: Exibido periodicamente, com contorno dos olhos e limpeza da parte inferior
- **😑 Piscar**: Ocorre a cada 25 ciclos, simulando um piscar natural com linhas horizontais

## Configuração dos Pinos

### Sensores Ultrassônicos
```cpp
// Sensor Esquerdo
#define SONAR_TRIGGER_LEFT 7
#define SONAR_ECHO_LEFT 6

// Sensor Direito  
#define SONAR_TRIGGER_RIGHT 5
#define SONAR_ECHO_RIGHT 4
```

### Botões de Controle
```cpp
#define BUTTON_SAD 3      // Botão azul (Tristeza)
#define BUTTON_ANGRY 2    // Botão vermelho (Raiva)
```

### Display OLED
```cpp
// Comunicação I2C
// SDA -> A4 (Arduino Uno)
// SCL -> A5 (Arduino Uno)
// Endereço I2C: 0x3C
```

## Lógica de Funcionamento

### Inicialização (`setup()`)
1. **Comunicação I2C**: Inicializa Wire para comunicação com o display
2. **Display OLED**: Configura o display SSD1306 no endereço 0x3C
3. **Pinos de Entrada/Saída**: Define modos dos sensores e botões
4. **Comunicação Serial**: Inicia comunicação serial para debug (9600 baud)

### Loop Principal (`loop()`)
1. **Leitura dos Sensores**: Captura distâncias dos sensores ultrassônicos esquerdo e direito
2. **Atualização da Posição**: Calcula nova posição dos olhos baseada nas distâncias
3. **Verificação dos Botões**: Lê estado dos botões de tristeza e raiva
4. **Controle de Expressões**: Gerencia ciclos temporais para piscar e expressões alternadas
5. **Renderização**: Atualiza o display com a nova expressão facial

### Algoritmo de Posicionamento
```cpp
void updateEyePosition(long dist1, long dist2) {
    // Restringe distâncias para faixa válida
    dist1 = constrain(dist1, 5, 100);
    dist2 = constrain(dist2, 5, 100);
    
    // Calcula offset baseado na diferença entre sensores
    int offset = map(abs(dist1 - dist2), 0, 100, 0, 20);
    
    // Move olhos para a direção do objeto mais próximo
    if (dist1 > dist2) {
        eyeX = min(OLED_WIDTH - 30, 64 + offset);
    } else {
        eyeX = max(40, 64 - offset);
    }
}
```

## Estrutura do Projeto

```
olhos-seguidores/
├── sketch.ino              # Código principal do Arduino
├── diagram.json            # Diagrama de conexões (Wokwi)
├── libraries.txt           # Lista de bibliotecas necessárias
├── wokwi-project.txt       # Informações do projeto Wokwi
└── README.md              # Documentação do projeto
```

## Instalação e Configuração

### Pré-requisitos
- **Arduino IDE** 1.8.0 ou superior
- **Bibliotecas Arduino**:
  - Adafruit GFX Library
  - Adafruit SSD1306
  - Wire (nativa)

### Instalação das Bibliotecas
1. Abra o Arduino IDE
2. Vá em **Sketch** → **Include Library** → **Manage Libraries**
3. Procure e instale:
   - `Adafruit GFX Library`
   - `Adafruit SSD1306`

### Configuração do Hardware
1. **Monte o circuito** conforme o diagrama de conexões
2. **Conecte o Arduino** ao computador via USB
3. **Carregue o código** (`sketch.ino`) no Arduino
4. **Teste as conexões** através do Monitor Serial

## Como Usar

### Operação Básica
1. **Ligue o Arduino** - O robô inicializa no estado neutro
2. **Aproxime objetos** dos sensores ultrassônicos para ver os olhos se moverem
3. **Pressione os botões** para alterar expressões:
   - **Botão Azul**: Ativa expressão triste
   - **Botão Vermelho**: Ativa expressão de raiva
4. **Observe as expressões automáticas**:
   - Piscar ocorre a cada 25 ciclos (~1.25 segundos)
   - Expressão feliz aparece periodicamente

### Interação com Sensores
- **Sensor Esquerdo**: Detecta objetos à esquerda, move olhos para a direita
- **Sensor Direito**: Detecta objetos à direita, move olhos para a esquerda
- **Ambos os Sensores**: Cria movimento dinâmico baseado na diferença de distâncias
- **Alcance Efetivo**: 5cm a 100cm para melhor resposta

## Expressões Faciais

### 😐 Expressão Neutra
```cpp
void neutralEyes() {
    // Desenha dois olhos arredondados preenchidos
    display.fillRoundRect(eyeX - 30, eyeY - 15, 25, 30, 5, WHITE);
    display.fillRoundRect(eyeX + 5, eyeY - 15, 25, 30, 5, WHITE);
}
```

### 😠 Expressão de Raiva
```cpp
void angryEyes() {
    // Olhos base + triângulos nos cantos superiores
    // Cria sombreamento que simula sobrancelhas franzidas
    display.fillTriangle(/* coordenadas dos triângulos superiores */);
}
```

### 😢 Expressão de Tristeza
```cpp
void sadEyes() {
    // Olhos base + triângulos nos cantos inferiores
    // Simula olhos caídos característicos da tristeza
    display.fillTriangle(/* coordenadas dos triângulos inferiores */);
}
```

### 😊 Expressão de Felicidade
```cpp
void happyEyes() {
    // Contorno dos olhos apenas + limpeza da parte inferior
    // Simula olhos sorrindo com a parte inferior "escondida"
    display.drawRoundRect(/* contornos dos olhos */);
    display.fillRect(0, 38, 128, 32, BLACK); // Limpa parte inferior
}
```

### 😑 Piscar
```cpp
void blinkEyes() {
    // Substitui os olhos por linhas horizontais
    // Simula pálpebras fechadas
    display.drawLine(eyeX - 30, eyeY, eyeX - 5, eyeY, WHITE);
    display.drawLine(eyeX + 5, eyeY, eyeX + 30, eyeY, WHITE);
}
```


## Bibliotecas Necessárias

### Adafruit GFX Library
```cpp
#include <Adafruit_GFX.h>
```
**Função**: Biblioteca base para gráficos, formas geométricas e texto

### Adafruit SSD1306
```cpp
#include <Adafruit_SSD1306.h>
```
**Função**: Controle específico do display OLED SSD1306

### Wire
```cpp
#include <Wire.h>
```
**Função**: Comunicação I2C entre Arduino e display (biblioteca nativa)

## Simulação Online

### Wokwi Simulator
Este projeto pode ser simulado online no **Wokwi**:
- **URL**: https://wokwi.com/projects/417341946682755073
- **Funcionalidades**:
  - ✅ Simulação completa do circuito
  - ✅ Teste de botões e sensores
  - ✅ Visualização em tempo real do display
  - ✅ Debug através do Monitor Serial

---

## 👥 Créditos

**Desenvolvedores**:
- [Nicolas Wallace](https://github.com/nicolas-wallace) - Colaborador
- [Vitor Nogueira](https://github.com/NogueVitor) - Colaborador

**Projeto desenvolvido em**: Final de 2024  
**Plataforma**: Arduino Uno + Wokwi Simulator  
**Tema**: Robótica Interativa e Expressões Faciais

---

**🎮 Inspiração Visual**: Personagem 8-Bits do jogo Brawl Stars  
**🎥 Demonstração**: [Vídeo dos Olhos Seguidores em ação](https://www.youtube.com/watch?v=ng629rU_N2Q)
