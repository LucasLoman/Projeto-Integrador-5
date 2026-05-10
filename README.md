# 🏭 IoT Estoque

Sistema de controle de estoque com dispositivo IoT simulado via ESP32 (Wokwi), backend em FastAPI e dashboard web em tempo real.

---

## 📋 Sobre o Projeto

Este projeto simula um sistema IoT empresarial de controle de estoque. Um ESP32 (simulado no Wokwi) representa um terminal físico de movimentação, onde o funcionário se identifica por biometria e registra entradas ou saídas de produtos. Os dados são enviados via HTTP para uma API na nuvem, que os persiste e os exibe em um painel web com histórico e gráficos.

### Fluxo do sistema

```
ESP32 (Wokwi)
    │
    │  Biometria simulada (botões +/-)
    │  Seleção de produto (teclado 4x4)
    │  Digitação de quantidade (teclado 4x4)
    │
    │  HTTP POST /movimentacoes
    ▼
FastAPI (Railway)
    │
    │  Valida e persiste a movimentação
    │
    ▼
Frontend (Dashboard Web)
    │
    │  Histórico em tabela
    │  Gráficos de entrada/saída
    └─ Acessível em qualquer lugar
```

---

## 🛠️ Tecnologias

| Camada | Tecnologia |
|--------|-----------|
| Dispositivo IoT | ESP32 simulado no [Wokwi](https://wokwi.com) |
| Firmware | C++ (Arduino) |
| Sensor biométrico | Adafruit Fingerprint (AS608 simulado) |
| Backend | Python + FastAPI |
| Deploy | Railway |
| Frontend | HTML, CSS, JavaScript, Chart.js |
| Tunnel local (dev) | localtunnel |

---

## 📁 Estrutura do Projeto

```
Projeto-Integrador-V/
├── projeto/
│   ├── main.py              # Backend FastAPI
│   ├── requirements.txt     # Dependências Python
│   ├── Procfile             # Configuração Railway
│   ├── movimentacoes.json   # Banco de dados (gerado automaticamente)
│   └── static/
│       └── index.html       # Frontend (dashboard + histórico)
│
├── wokwi/
│   ├── sketch.ino           # Firmware do ESP32
│   ├── diagram.json         # Circuito simulado (Wokwi)
│   └── libraries.txt        # Bibliotecas Arduino
│
└── README.md
```

---

## ⚙️ Hardware Simulado (Wokwi)

| Componente | Função |
|-----------|--------|
| ESP32 DevKit V1 | Microcontrolador principal |
| Display OLED 128x64 | Exibe o estado atual do sistema |
| Teclado Matricial 4x4 | Digita ID do produto e quantidade |
| Botão Verde (+) | Navega para cima / incrementa |
| Botão Vermelho (-) | Navega para baixo / decrementa |
| Botão Azul (OK) | Confirma seleção / avança etapa |

### Pinagem

| Componente | Pinos ESP32 |
|-----------|-------------|
| OLED SDA/SCL | D21 / D22 |
| Teclado Linhas | D13, D12, D14, D27 |
| Teclado Colunas | D26, D25, D33, D32 |
| Botão + | D18 |
| Botão - | D19 |
| Botão OK | D5 |

---

## 🔄 Fluxo de Operação do ESP32

```
1. [ Biometria ]
   +/- seleciona o funcionário
   OK confirma → animação de leitura

2. [ Ação ]
   +/- alterna entre ENTRADA e SAÍDA
   OK confirma

3. [ Produto ]
   Teclado digita o ID (1-8)
   Preview do nome aparece em tempo real
   OK confirma

4. [ Quantidade ]
   Teclado digita a quantidade
   OK confirma e envia

5. [ Resultado ]
   Sucesso! / Falhou!
   OK reinicia o fluxo
```

---

## 👥 Funcionários Cadastrados

| ID | Nome |
|----|------|
| 1 | Ana Lima |
| 2 | Bruno Costa |
| 3 | Carla Dias |
| 4 | Diego Nunes |
| 5 | Eva Rocha |

---

## 📦 Produtos Cadastrados

| ID | Produto | Unidade |
|----|---------|---------|
| 1 | Parafuso M6 | pcs |
| 2 | Cimento CP-II | sac |
| 3 | Cabo Elétrico | mts |
| 4 | Tinta Látex | lts |
| 5 | Tubo PVC 1/2 | pcs |
| 6 | Rolo de Lixa | pcs |
| 7 | Fio Terra 2.5 | mts |
| 8 | Disjuntor 20A | pcs |

---

## 🚀 Como Rodar Localmente

### Pré-requisitos

- Python 3.10+
- Node.js (para o localtunnel)
- Conta no [Wokwi](https://wokwi.com)

### 1. Clonar o repositório

```bash
git clone https://github.com/seu-usuario/Projeto-Integrador-V.git
cd Projeto-Integrador-V/projeto
```

### 2. Instalar dependências

```bash
pip install fastapi uvicorn
```

### 3. Rodar o backend

```bash
python -m uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

### 4. Expor com localtunnel (para o Wokwi acessar)

```bash
npm install -g localtunnel
lt --port 8000 --local-host 0.0.0.0
```

### 5. Configurar o Wokwi

No arquivo `sketch.ino`, substitua a URL:

```cpp
const char* API_URL = "http://SUA-URL.loca.lt/movimentacoes";
```

### 6. Acessar o dashboard

```
http://localhost:8000
```

---

## ☁️ Deploy na Nuvem (Railway)

O backend e o frontend estão hospedados no [Railway](https://railway.app).

### Configuração

- **Root Directory:** `projeto`
- **Start Command:** `uvicorn main:app --host 0.0.0.0 --port $PORT`

### URL de produção

```
https://projeto-integrador-5-production.up.railway.app
```

No `sketch.ino`, use a URL do Railway com `WiFiClientSecure`:

```cpp
const char* API_URL = "https://projeto-integrador-5-production.up.railway.app/movimentacoes";
```

---

## 📡 Endpoints da API

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/movimentacoes` | Registra uma movimentação (ESP32) |
| `GET` | `/movimentacoes` | Lista o histórico completo |
| `GET` | `/movimentacoes/{id}` | Busca movimentação por ID |
| `DELETE` | `/movimentacoes` | Limpa o histórico |
| `GET` | `/status` | Status da API |

### Payload do ESP32

```json
{
  "funcionario_id": 3,
  "acao": "entrada",
  "produto_id": 7,
  "quantidade": 10
}
```

### Resposta da API

```json
{
  "id": 1,
  "funcionario_id": 3,
  "funcionario_nome": "Carla Dias",
  "acao": "entrada",
  "produto_id": 7,
  "produto_nome": "Fio Terra 2.5",
  "produto_unidade": "mts",
  "quantidade": 10,
  "data_hora": "2026-01-05T18:33:00"
}
```

---

## 📊 Dashboard

O painel web possui duas seções:

**Histórico**
- Tabela com todas as movimentações
- Filtros por tipo de ação (Todos / Entradas / Saídas)
- Atualização automática a cada 5 segundos

**Dashboard**
- Gráfico de barras — entradas vs saídas por produto
- Gráfico donut — distribuição percentual de ações
- Gráfico de linha — movimentações ao longo do tempo
- Ranking de produtos mais movimentados
- Ranking de funcionários mais ativos

---

## 📚 Bibliotecas Arduino

```
Adafruit Fingerprint Sensor Library
Adafruit SSD1306
Adafruit GFX Library
Keypad
ArduinoJson
WiFiClientSecure (built-in ESP32)
```

---

## 👨‍💻 Autores

Desenvolvido como Projeto Integrador — Curso de Tecnologia da Informação.
