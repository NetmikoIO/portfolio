# Netmiko Homelab
<br/>
<picture>
  <img src="/assets/images/homelab.png" alt="Banner" style="width: 100%;">
</picture>



> Nota: Todos los dispositivos están conectados al router principal para comunicación interna y pruebas de servicios.

---

## 🔹 Detalle de cada dispositivo

### PC Sobremesa
- **Sistema operativo:** Windows 11 Pro
- **Uso principal:** Virtualización de VMs, pruebas de redes, Docker, monitorización
- **Características clave:**
  - CPU: Intel Core i5-10400F
  - RAM: 16 GB
  - GPU: MSI RTX 3060 12GB
  - Almacenamiento: SSD 500GB + HDD 1TB

---

### Laptop Dualboot
- **Sistemas operativos:** Windows 11 / Ubuntu 24 LTS
- **Uso principal:** Cliente, servidor secundario, pruebas de scripts
- **Características clave:**
  - CPU: Intel Core i5-8250U
  - RAM: 12 GB
  - Almacenamiento: SSD 1TB para Windows 11 + SSD M.2 1TB para Ubuntu 24 LTS

---

### Raspberry Pi 4
- **RAM:** 4 GB
- **Uso principal:** Servidor IoT, broker MQTT, Pi-hole, monitorización con Grafana
- **Conexiones:** Ethernet 1 Gbps, Wi-Fi, GPIO para proyectos electrónicos

---

### M5Stack ESP32 S3 + RFID
- **Uso principal:** Ciberseguridad WiFi con Bruce, lectura de tarjetas RFID
- **Conexiones:** Lector de tarjetas, USB-C al PC

---

## 🔹 Tabla resumen 

| Dispositivo      | Sistema/Specs                     | Uso principal                      |
|-----------------|---------------------------------|----------------------------------|
| PC Sobremesa     | Win11, 16GB RAM, i5-10400F, RTX3060 | VMs, Docker, pruebas redes        |
| Laptop Dualboot  | Win11/Ubuntu24, 12GB RAM, SSD 1TB + M.2 1TB | Cliente/servidor secundario       |
| Raspberry Pi 4   | 4 GB RAM, Ethernet/Wi-Fi         | MQTT, IoT, Grafana, Pi-hole       |
| M5Stack ESP32 S3 | ESP32 S3, RFID                    | Bruce WiFi, RFID                  |

---

## 🔹 Notas adicionales
- Este homelab está pensado para **pruebas seguras en red local**, IoT y automatización.
- Todos los dispositivos están documentados y organizados para **aprendizaje y desarrollo de proyectos**.
- El README se mantendrá actualizado con cada nuevo proyecto o dispositivo añadido a **Homelab®️**.
