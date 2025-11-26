# SleepArlet - Sistema de Detecção de Sonolência 

Sistema de monitoramento em tempo real que detecta quando os olhos permanecem fechados por mais de 0.8 segundos, emitindo alertas visuais e sonoros para prevenir acidentes por sonolência.

**Autor:** Isaac Mello

---

## 📋 Visão Geral

O SleepArlet utiliza visão computacional e deep learning para monitorar o estado dos olhos através da webcam, calculando o Eye Aspect Ratio (EAR) e aplicando modelos de classificação para determinar com precisão quando os olhos estão fechados.

### Tecnologias Utilizadas

- **MediaPipe Face Mesh**: Detecção facial e landmarks precisos
- **OpenCV**: Processamento de imagem e captura de vídeo
- **TensorFlow/Keras**: Modelos de deep learning para classificação avançada
- **NumPy**: Cálculos numéricos otimizados

---

## 🚀 Requisitos

### Sistema

- **Python**: 3.8, 3.9, 3.10 ou 3.11
- **Webcam**: Funcional e acessível
- **Sistema Operacional**: Windows, Linux ou macOS

> **⚠️ Nota:** Python 3.13 não é suportado pelo MediaPipe. Use Python 3.11 ou anterior.

### Dependências

- `opencv-python >= 4.8.0`
- `mediapipe >= 0.10.0`
- `numpy >= 1.24.0`
- `tensorflow >= 2.13.0`

---

## 📦 Instalação

### 1. Clone o repositório

```bash
git clone <repository-url>
cd wakeupi-ai
```

### 2. Verifique a versão do Python

```bash
python --version
```

Se necessário, instale Python 3.11 ou anterior em [python.org](https://www.python.org/downloads/).

### 3. Crie e ative o ambiente virtual

**Windows (PowerShell):**
```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
```

**Windows (CMD):**
```cmd
python -m venv venv
venv\Scripts\activate.bat
```

**Linux/macOS:**
```bash
python -m venv venv
source venv/bin/activate
```

### 4. Instale as dependências

```bash
pip install -r requirements.txt
```

---

## ▶️ Uso

Execute o script principal:

```bash
python main.py
```

### Controles

- **`q`**: Encerrar o programa

### Interface

O sistema exibe em tempo real:

- **Status dos olhos**: ABERTO/FECHADO para cada olho
- **EAR médio**: Eye Aspect Ratio calculado
- **Taxa de piscadas**: Piscadas por minuto
- **Total de piscadas**: Contador acumulado
- **Indicadores visuais**: Círculos coloridos nos olhos (verde=aberto, vermelho=fechado)

### Alerta de Sonolência

Quando os olhos permanecem fechados por **0.8 segundos**, o sistema dispara:

- **Alerta visual**: Borda vermelha pulsante na tela
- **Alerta sonoro**: Beep do sistema
- **Mensagem**: "VOCE DORMIU!!!! ACORDE AGORA!!!"

O alerta permanece ativo até que os olhos sejam abertos novamente.

---

## 🔧 Funcionamento Técnico

### Eye Aspect Ratio (EAR)

O sistema calcula o EAR usando 6 pontos específicos dos olhos detectados pelo MediaPipe:

```
EAR = (|p2-p6| + |p3-p5|) / (2 * |p1-p4|)
```

**Interpretação:**
- **EAR > 0.25**: Olhos abertos
- **EAR < 0.25**: Olhos fechados
- **EAR < 0.15**: Definitivamente fechado (detecção imediata)

### Threshold Adaptativo

O sistema utiliza um threshold adaptativo baseado no baseline individual:

- Calcula o baseline dinâmico dos olhos abertos
- Ajusta o threshold para 65% do baseline
- Mantém limites entre 0.18 e 0.28 para evitar falsos positivos

### Deep Learning (Opcional)

Quando habilitado, o sistema utiliza modelos CNN para validação em casos ambíguos:

- Modelo principal: Arquitetura ResNet-like
- Modelo leve: MobileNet-like para ensemble
- Ativado apenas quando EAR está próximo do threshold (zona de incerteza)

### Otimizações de Performance

- Processamento em resolução reduzida (480px)
- Deep learning apenas quando necessário (a cada 0.5s)
- Modificação in-place de frames para reduzir cópias
- MediaPipe com refinamento de landmarks para precisão

---

## 📁 Estrutura do Projeto

```
wakeupi-ai/
├── main.py                  # Script principal e orquestração
├── eye_detector.py          # Detecção de olhos e cálculo EAR
├── deep_eye_classifier.py   # Modelos de deep learning
├── alert_system.py          # Sistema de alertas visuais/sonoros
├── ui_modern.py             # Interface gráfica moderna
├── requirements.txt         # Dependências do projeto
└── README.md               # Documentação
```

---

## ⚙️ Configurações

### Parâmetros Ajustáveis

#### `main.py`

```python
deep_learning_check_interval = 0.5  # Intervalo para validação DL (segundos)
```

#### `eye_detector.py`

```python
EAR_THRESHOLD = 0.25              # Threshold base para detecção
EAR_SMOOTHING_FRAMES = 5          # Frames para suavização
```

#### `alert_system.py`

```python
flash_interval = 0.2               # Intervalo entre flashes (segundos)
beep_interval = 0.5               # Intervalo entre beeps (segundos)
```

#### Tempo de Alerta

O tempo para disparar o alerta está definido em `main.py`:

```python
if duration > 0.8:  # Threshold de sonolência (segundos)
    self.alerts.trigger_alert()
```

---

## 🐛 Solução de Problemas

### Rosto não detectado

- **Causa**: Iluminação insuficiente ou rosto fora do campo de visão
- **Solução**: Melhore a iluminação e posicione-se centralmente na frente da câmera

### Falsos positivos (alerta com olhos abertos)

- **Causa**: Threshold muito baixo ou baseline incorreto
- **Solução**: Aumente `EAR_THRESHOLD` em `eye_detector.py` (ex: 0.27 ou 0.28)

### Não detecta olhos fechados

- **Causa**: Threshold muito alto
- **Solução**: Diminua `EAR_THRESHOLD` em `eye_detector.py` (ex: 0.22 ou 0.23)

### Webcam não abre

- **Causa**: Webcam em uso por outro programa ou permissões
- **Solução**: Feche outros programas que usam a webcam e verifique permissões do sistema

### Erro ao instalar MediaPipe

- **Causa**: Versão do Python incompatível (Python 3.13)
- **Solução**: Instale Python 3.11 ou anterior

### FPS muito baixo

- **Causa**: Processamento pesado ou hardware limitado
- **Solução**: O sistema já está otimizado. Se necessário, desabilite deep learning em `main.py`:
  ```python
  self.detector = EyeDetector(use_deep_learning=False)
  ```

---

## 📝 Notas de Uso

- **Iluminação**: Mantenha boa iluminação frontal para melhor detecção
- **Posicionamento**: Mantenha o rosto visível e centralizado na câmera
- **Ambiente**: Funciona melhor com uma pessoa por vez na frente da câmera
- **Ajuste fino**: Ajuste o `EAR_THRESHOLD` conforme necessário para seu ambiente

---

## 📄 Licença

Este projeto é de uso pessoal e educacional.

**Desenvolvido por Isaac Mello - AI Engineer**
