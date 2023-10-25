# 2.5-Embeded-Web
Codigo 
```python
import machine
import network
import socket

# Configuración de red WiFi
ssid = 'TU_SSID'
password = 'TU_CLAVE'

# Configurar la red WiFi
station = network.WLAN(network.STA_IF)
station.active(True)
station.connect(ssid, password)

# Configurar el pin del LED
led = machine.Pin(25, machine.Pin.OUT)  # LED integrado en el pin 25

# HTML para la página web
html = """<!DOCTYPE html>
<html>
<head>
    <title>ESP Web Server</title>
</head>
<body>
    <center>
        <h2>Raspberry Pi Pico Web Server</h2>
        <p><a href="/?led=on"><button style="height:100px;width:200px">ENCENDER LED</button></a></p>
        <p><a href="/?led=off"><button style="height:100px;width:200px">APAGAR LED</button></a></p>
        <p>Estado del LED: {}</p>
    </center>
</body>
</html>
"""

def web_page():
    if led.value() == 1:
        gpio_state = "ON"
    else:
        gpio_state = "OFF"

    return html.format(gpio_state)

# Configuración del servidor web
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.bind(('', 80))
s.listen(5)

while True:
    conn, addr = s.accept()
    print('Conexión desde %s' % str(addr))
    request = conn.recv(1024)
    request = str(request)

    led_on = request.find('/?led=on')
    led_off = request.find('/?led=off')

    if led_on == 6:
        print('ENCENDER LED')
        led.value(1)
    if led_off == 6:
        print('APAGAR LED')
        led.value(0)

    response = web_page()
    conn.send('HTTP/1.1 200 OK\n')
    conn.send('Content-Type: text/html\n')
    conn.send('Connection: close\n\n')
    conn.sendall(response)
    conn.close()
```
