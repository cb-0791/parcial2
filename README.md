# Evaluación Parcial 2 - Seguridad de Redes

**Estudiante:** Cóndor Bautista  
**Matrícula:** 2025-0791  
**Materia:** Seguridad de Redes  
**Video Demostrativo:** 

---

## 📌 Propósito del Proyecto
Este proyecto documenta e implementa la topología de red segura para la evaluación del Parcial 2. Se aplican técnicas de hardening en dispositivos Cisco (IOS), enrutamiento dinámico autenticado (OSPF MD5), autenticación centralizada AAA en puertos de gestión física, servicios de direccionamiento dinámico (DHCP), administración remota segura (SSH) y monitoreo de infraestructura mediante el protocolo SNMP en un servidor Zabbix.

---

## 📐 Diagrama de la Topología

<img width="550" height="508" alt="imagen_2026-11-11_122357088" src="https://github.com/user-attachments/assets/a3277fe8-2517-4209-b3cb-aa50338640a7" />


### Direccionamiento IP Base (Matrícula: 2025-0791)
* **Red LAN Base:** `10.7.91.128/25`
* **Router R1-HQ (Gateway):** `10.7.91.201 /25`
* **Router R2-Secundario:** `10.7.91.202 /25`
* **Windows Server (AD/NPS):** `10.7.91.200 /25`
* **Ubuntu Server (Zabbix):** `10.7.91.225 /25`
* **Switch SW1-LAN:** `10.7.91.205 /25`
* **Rango DHCP LAN:** `10.7.91.128 - 10.7.91.254` (Excluidas IPs de gestión)

---

## 🛠️ Descripción de los Requisitos Implementados

### 1. Direccionamiento y Conectividad
Se configuraron las direcciones IP, máscaras y hostnames en todos los dispositivos de la infraestructura según la matrícula asignada, garantizando la conectividad punto a punto y el aislamiento de dominios de colisión en GNS3.

<img width="609" height="209" alt="imagen_2026-11-11_122542950" src="https://github.com/user-attachments/assets/33e3dd76-b693-4a5f-8a24-2aab7cc26326" />

<img width="602" height="200" alt="imagen_2026-11-11_123018052" src="https://github.com/user-attachments/assets/e233bd8d-4692-4826-827c-634c26a89118" />

---

### 2. OSPF con Cifrado MD5
Se estableció la adyacencia de enrutamiento OSPF en el Área 0 entre `R1-HQ` y `R2-Secundario`, habilitando la autenticación por clave de síntesis del mensaje (MD5) con la clave `SecretoOSPF2026`.

* **Comandos de verificación:**
  * `show ip ospf neighbor`
  * `show ip route ospf`

<img width="646" height="379" alt="imagen_2026-11-11_123112484" src="https://github.com/user-attachments/assets/bd63c30d-9c3e-44b4-bb7f-bfd922c4da85" />


---

### 3. Autenticación AAA en Consola y Auxiliar
Se aplicó la directiva `aaa new-model` restringiendo la autenticación y autorización **exclusivamente a las líneas físicas (`line con 0` y `line aux 0`)** del router, utilizando credenciales locales y permitiendo que los accesos remotos se manejen de forma independiente.

![Prueba de Acceso AAA por Consola](images/03_aaa_consola.png)

---

### 4. Servidor DHCP en el Router
Se configuró el pool `LAN-Pool` en el router `R1-HQ` excluyendo las direcciones estáticas críticas (`.200`, `.201`, `.205`, `.225`). Las estaciones de trabajo en la LAN reciben su direccionamiento y DNS de forma automática.

<img width="627" height="228" alt="imagen_2026-11-11_123209522" src="https://github.com/user-attachments/assets/479693da-48f2-42c2-bde3-5c1bbf58fc16" />

---

### 5. Acceso Administrativo por SSH
Se inhabilitó el protocolo inseguro Telnet en favor de SSHv2 (`transport input ssh`) tanto en el Router `R1-HQ` como en el Switch `SW1-LAN`, configurando claves RSA de 2048 bits y cuentas de usuario locales cifradas.

<img width="640" height="344" alt="imagen_2026-11-11_123458408" src="https://github.com/user-attachments/assets/81b3292f-b3d6-432a-a53e-5ed25c334e4f" />

---

### 6. Monitoreo SNMP en Zabbix
Se configuró la comunidad SNMP de solo lectura `RedSecureRO` en el router `R1-HQ` y se vinculó la plantilla de monitoreo `Cisco IOS by SNMP` en el servidor Zabbix, validando la recolección activa de métricas del dispositivo.

<img width="1047" height="312" alt="imagen_2026-11-11_123257082" src="https://github.com/user-attachments/assets/a146252c-20f0-4ce3-9c0d-f8ecb0ca46bf" />


---

### 7. Auditoría e Informe de Hardening CIS
Se redactó el script de auditoría CIS `hardening_audit.py` para analizar el estado de seguridad actual del equipo de red (verificando servicios vulnerables, cifrado de claves, búsquedas DNS y líneas VTY) y generar un reporte ejecutable de solo lectura.

<img width="1066" height="641" alt="imagen_2026-11-11_123615023" src="https://github.com/user-attachments/assets/601b1ca8-43e4-4717-a70b-dd81ad17e306" />
