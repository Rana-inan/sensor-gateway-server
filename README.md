Remote Sensing Application (Java) — Sensor •
Gateway • Server
README
Overview
A remote sensing system implemented in Java using socket programming for a Computer
Networks project.
The system simulates Temperature and Humidity sensors that periodically generate data, send it to
a Gateway, and the Gateway forwards it to a Server.
The Server stores the latest values and exposes them via a simple HTTP web interface.
Project Components
• TempSensor: Generates temperature values every second and sends them to the Gateway
(TCP).
• HumiditySensor: Generates humidity values every second and sends them to the Gateway
(UDP).
- If humidity > 80 → sends the value
- Else → sends ALIVE
• Gateway: Receives sensor data and forwards it to the Server (TCP). Also detects sensor
inactivity.
• Server: Receives forwarded data, stores the latest values in memory, and exposes HTTP
endpoints.
Technologies
• Java
• TCP / UDP sockets
• Java built-in HTTP server (HttpServer)
• Threading + concurrent data structure (ConcurrentHashMap)
Architecture & Data Flow
Communication Channels:
• TemperatureSensor → Gateway: TCP
• HumiditySensor → Gateway: UDP
• Gateway → Server: TCP
Handshake (Gateway ↔ Server):
Before sending sensor data, the Gateway performs a basic handshake with the Server:
• Gateway sends: HANDSHAKE_REQ
• Server replies: HANDSHAKE_ACK
After the handshake, the Gateway starts forwarding incoming sensor messages (plain text /
line-based).
Gateway Logic
Page 2
• Forwards received sensor messages to the Server.
• Tracks last-received time for each sensor.
• If no data is received from a sensor for a certain time window, the Gateway notifies the Server
with:
- SENSOR OFF
Server Logic
• Stores latest received data in a ConcurrentHashMap (in-memory).
• Provides HTTP endpoints to query the latest sensor values.
HTTP Endpoints:
• Temperature: http://localhost:8080/temperature
• Humidity: http://localhost:8080/humidity
Ports
• Server TCP (Gateway connection): 6000
• Server HTTP: 8080
• Gateway TCP (Temperature sensor): 5000
• Gateway UDP (Humidity sensor): 5001
How to Run (Order Matters)
Recommended run order: Server → Gateway → Sensors
Compile
javac Server.java Gateway.java TempSensor.java HumiditySensor.java
Start Server (Terminal 1)
java Server
Start Gateway (Terminal 2)
java Gateway
Start Sensors (Terminal 3 & 4)
Terminal 3:
java TempSensor
Terminal 4:
java HumiditySensor
Check data on browser
• Temperature: http://localhost:8080/temperature
• Humidity: http://localhost:8080/humidity
Example Messages
Page 3
• Humidity Sensor:
- ALIVE
- Humidity: 85
• Gateway to Server:
- forwarded sensor messages (plain text)
• Sensor inactivity:
- SENSOR OFF
Troubleshooting
Address already in use:
A port is still in use from a previous run.
• Close running terminals
• Wait a few seconds and try again
• (Optional) restart the terminal/PC
Connection refused:
• Make sure Server is running first (TCP 6000)
• Then start Gateway, then sensors
Known Issue / Note
Depending on the current build, there may be a case where the Server does not receive forwarded
temperature data from the Gateway. Further debugging may be required.
