# Instalação do Mosquitto MQTT com Docker Compose

Este guia descreve como instalar e configurar o **Eclipse Mosquitto** usando Docker Compose com autenticação por senha, suporte a **MQTT** e **WebSockets**, persistência de dados e logs.

---

## 📋 Requisitos

- Docker instalado  
- Docker Compose instalado  
- Portas **1883** (MQTT) e **9001** (WebSockets) liberadas no firewall  

---

## 📂 Estrutura de diretórios

Faça o clone do repositorio do Github

```bash
git clone https://github.com/ItaloFreitasM/mosquitto-docker-compose.git

cd /mosquitto-docker-compose
```

---

## ⚙️ Arquivo de configuração `mosquitto.conf`

#### Dentro de `mosquitto-docker-compose/config/`, edite conforme necessário o arquivo `mosquitto.conf`:

```bash
# Desativa conexões anônimas
allow_anonymous false

# Porta MQTT padrão
listener 1883

# Habilita WebSockets
listener 9001
protocol websockets

# Persistência
persistence true
persistence_location /mosquitto/data/

# Logs
log_dest stdout
log_dest file /mosquitto/log/mosquitto.log

# Arquivo de senhas
password_file /mosquitto/config/passwd
```

---

## 🔑 Criando usuário e senha

#### Crie o arquivo passwd com o comando:

```bash
docker run --rm -it \
  -v $(pwd)/config:/mosquitto/config \
  eclipse-mosquitto mosquitto_passwd -c /mosquitto/config/passwd usuario
```
#### Digite a senha desejada quando solicitado.

---

## 📦 Arquivo docker-compose.yml

#### No diretório raiz (`mosquitto/`), edite o `docker-compose.yml` conforme necessário:

```bash
version: '3.9'

services:

  mqtt:
    container_name: mosquitto-mqtt
    image: eclipse-mosquitto:latest
    volumes:
      - ./config/mosquitto.conf:/mosquitto/config/mosquitto.conf
      - ./config/passwd:/mosquitto/config/passwd
      - ./data:/mosquitto/data
      - ./log:/mosquitto/log
    ports:
      - 1883:1883
      - 9001:9001
    restart: unless-stopped
```

---

## 🚀 Iniciando o serviço

#### No diretório onde está o docker-compose.yml:

```bash
docker compose up -d
```

---

## 🧪 Testando a conexão

#### Para testar o broker MQTT:

```bash
# Publicar mensagem
mosquitto_pub -h localhost -p 1883 -t "teste" -m "Olá MQTT" -u usuario -P senha

# Inscrever-se em um tópico
mosquitto_sub -h localhost -p 1883 -t "teste" -u usuario -P senha
```

---
## 📂 Estrutura fina
```bash
mosquitto/
├── config/
│   ├── mosquitto.conf
│   └── passwd
├── data/
├── log/
└── docker-compose.yml
```

---

## 📜 Referências

- [Eclipse Mosquitto - Documentação](https://mosquitto.org/documentation)
- [Mosquitto Docker Hub](https://hub.docker.com/_/eclipse-mosquitto)
