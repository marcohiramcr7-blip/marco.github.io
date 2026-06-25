<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dashboard IoT</title>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>
    <style>
        body { font-family: Arial, sans-serif; text-align: center; background-color: #f4f4f9; padding: 20px; }
        .card { background: white; padding: 20px; border-radius: 10px; box-shadow: 0 4px 8px rgba(0,0,0,0.1); max-width: 400px; margin: auto; }
        .temp { font-size: 3rem; font-weight: bold; color: #333; }
        .led-status { margin: 15px 0; font-size: 1.2rem; }
        .btn-buzzer { padding: 10px 20px; font-size: 1.2rem; border: none; border-radius: 5px; cursor: pointer; color: white; background-color: #e74c3c; }
        .btn-buzzer.active { background-color: #c0392b; box-shadow: inset 0 3px 5px rgba(0,0,0,0.2); }
    </style>
</head>
<body>

<div class="card">
    <h2>Monitor de Temperatura</h2>
    <div class="temp"><span id="valor-temp">--</span>°C</div>
    
    <div class="led-status">
        <strong>LED Activo:</strong> <span id="led-activo">Cargando...</span>
    </div>

    <button id="btn-buzzer" class="btn-buzzer" onclick="toggleBuzzer()">Activar Buzzer</button>
</div>

<script>
    // REEMPLAZA ESTO CON TUS DATOS DE FIREBASE
    const firebaseConfig = {
        apiKey: "IzaSyA0PtuzyRFtumwsl7XVZIAMBEuBiT5ijnY",
        databaseURL: "https://proyecto-redes-8d766-default-rtdb.firebaseio.com/"
    };

    // Inicializar Firebase
    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    let buzzerEstado = false;

    // Escuchar la Temperatura y los LEDs en tiempo real
    db.ref('/sensores/temperatura').on('value', (snapshot) => {
        document.getElementById('valor-temp').innerText = snapshot.val().toFixed(1);
    });

    db.ref('/estado').on('value', (snapshot) => {
        const estados = snapshot.val();
        let ledTexto = "Ninguno";
        if (estados.ledAzul) ledTexto = "🔵 Azul (Frío)";
        if (estados.ledVerde) ledTexto = "🟢 Verde (Normal)";
        if (estados.ledAmarillo) ledTexto = "🟡 Amarillo (Caliente)";
        
        document.getElementById('led-activo').innerText = ledTexto;
    });

    // Función para el botón del Buzzer
    function toggleBuzzer() {
        buzzerEstado = !buzzerEstado;
        db.ref('/control/buzzerWeb').set(buzzerEstado);
        
        const btn = document.getElementById('btn-buzzer');
        if(buzzerEstado) {
            btn.innerText = "Desactivar Buzzer";
            btn.classList.add("active");
        } else {
            btn.innerText = "Activar Buzzer";
            btn.classList.remove("active");
        }
    }
</script>

</body>
</html>
