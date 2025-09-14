# README-Sprint-3
README

# Toque de Craque ⚽📱

## 👥 Integrantes
- Guilherme Acacio rm 562475 
- Gustavo Mendez  rm 563753
- Enrico Almeida  rm 563265

---

## 📌 Descrição do Projeto
O **Toque de Craque** é um aplicativo inovador dedicado à valorização e divulgação do futebol feminino.  
Nosso objetivo é centralizar informações, ampliar o acesso a conteúdos de qualidade e oferecer uma experiência interativa e acessível para fãs do esporte.  

O app se organiza em três pilares principais:  

1. **Aba Passa Bola** → integração com o canal do YouTube, apresentando informações sobre as integrantes e seus vídeos mais recentes.  
2. **Aba Notícias** → atualização diária com as principais informações sobre futebol feminino, no estilo de grandes portais esportivos.  
3. **Aba IA Interativa** → inteligência artificial capaz de responder perguntas sobre o futebol feminino, trazendo estatísticas, curiosidades e dados históricos.  

Um diferencial do projeto é a integração com **IoT e sensor de som**, permitindo que o usuário interaja com o chatbot por meio de **áudios**, deixando a experiência mais natural, inclusiva e acessível.  

---

## 🏗️ Arquitetura Proposta

A arquitetura combina **FIWARE + MQTT** para conectar o sensor de som ao aplicativo.  

- **IoT (Sensor de Som)** → capta a voz do usuário.  
- **MQTT Broker** → recebe as mensagens publicadas pelo sensor (protocolo MQTT, porta 1883).  
- **IoT Agent MQTT** → traduz dados de MQTT para NGSIv2.  
- **Orion Context Broker** → gerencia contexto em tempo real e repassa ao app.  
- **MongoDB (Historical)** → armazena histórico de interações e estatísticas.  
- **Aplicação (App + IA)** → interface no celular, responsável por exibir notícias, vídeos e interagir com a IA via texto ou voz.  

### 🔗 Diagrama da Arquitetura
*(inserir aqui a imagem do diagrama que vocês criaram com Orion, IoT Agent, MongoDB e MQTT)*  

---

## 🛠️ Recursos Necessários

### Hardware
- Sensor de som (ex: microfone IoT em ESP32 ou Arduino com módulo).  
- Dispositivo móvel (Android/iOS).  

### Software
- **FIWARE Orion Context Broker**  
- **IoT Agent MQTT**  
- **Mosquitto MQTT Broker**  
- **MongoDB**  
- **Aplicativo Toque de Craque** (em React Native ou Flutter)  
- **Backend em Node.js/Python** para integração da IA  

---

## ▶️ Instruções de Uso
1. Subir o **MQTT Broker** na porta padrão 1883.  
2. Configurar o **IoT Agent MQTT** para comunicação com o Orion.  
3. Iniciar o **Orion Context Broker** e conectar ao MongoDB (porta 27017).  
4. Conectar o sensor de som ao broker MQTT.  
5. Executar o app *Toque de Craque* no celular.  
6. Interagir com a IA via texto ou **áudio captado pelo sensor IoT**.


## 🔌 Exemplo de Código IoT – Sensor de Som (ESP32 + MQTT)

```cpp
#include <WiFi.h>
#include <PubSubClient.h>

// Config WiFi
const char* ssid = "SEU_WIFI";
const char* password = "SENHA_WIFI";

// Config MQTT
const char* mqtt_server = "BROKER_IP";
const int mqtt_port = 1883;
const char* mqtt_topic = "toquedecraque/som";

WiFiClient espClient;
PubSubClient client(espClient);

const int micPin = 34;  // Pino ADC do microfone

void setup_wifi() {
  delay(10);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
  }
}

void reconnect() {
  while (!client.connected()) {
    if (client.connect("ESP32Client")) {
      client.publish(mqtt_topic, "Sensor de som conectado");
    } else {
      delay(5000);
    }
  }
}

void setup() {
  Serial.begin(115200);
  setup_wifi();
  client.setServer(mqtt_server, mqtt_port);
}

void loop() {
  if (!client.connected()) {
    reconnect();
  }
  client.loop();

  int micValue = analogRead(micPin);
  
  // Simulação: envia nível de som
  char msg[50];
  sprintf(msg, "Nivel de som: %d", micValue);
  client.publish(mqtt_topic, msg);

  delay(1000);
}
