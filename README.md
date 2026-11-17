
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="theme-color" content="#08152d">

  <title>RamPay - Votre argent, en mouvement</title>

  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    body {
      font-family: Arial, sans-serif;
      background: #08152d;
      color: white;
      padding: 18px;
    }

    header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 35px;
    }

    .logo {
      font-size: 27px;
      font-weight: bold;
      color: white;
    }

    .logo span {
      color: #d9b66d;
    }

    .profile {
      text-decoration: none;
      color: #d9b66d;
      border: 1px solid #d9b66d;
      padding: 10px 13px;
      border-radius: 10px;
      font-size: 13px;
    }

    .welcome {
      margin-bottom: 25px;
    }

    .welcome h1 {
      font-size: 25px;
      margin-bottom: 8px;
    }

    .welcome p {
      color: #aab6ca;
    }

    .balance {
      background: linear-gradient(135deg, #172b4d, #263c5e);
      padding: 23px;
      border-radius: 18px;
      margin-bottom: 30px;
      border: 1px solid #344969;
    }

    .balance p {
      color: #c0cada;
    }

    .amount {
      font-size: 30px;
      font-weight: bold;
      color: #d9b66d;
      margin: 12px 0 20px;
    }

    .actions {
      display: flex;
      gap: 10px;
    }

    button {
      flex: 1;
      padding: 13px 8px;
      border: none;
      border-radius: 10px;
      background: #d9b66d;
      color: #08152d;
      font-weight: bold;
      cursor: pointer;
    }

    button:disabled {
      opacity: 0.6;
      cursor: wait;
    }

    .section-title {
      margin-bottom: 18px;
      font-size: 22px;
    }

    .card {
      background: #122442;
      border: 1px solid #344969;
      padding: 22px;
      border-radius: 16px;
      margin-bottom: 18px;
    }

    .card h3 {
      color: #d9b66d;
      font-size: 22px;
      margin-bottom: 10px;
    }

    .card p {
      color: #c0cada;
      margin-bottom: 12px;
    }

    .price {
      font-size: 20px;
      font-weight: bold;
      color: white;
    }

    .order {
      width: 100%;
      margin-top: 12px;
    }

    footer {
      text-align: center;
      color: #8492a8;
      margin-top: 35px;
      font-size: 12px;
      padding-bottom: 20px;
    }
  </style>
</head>

<body>

  <header>
    <div class="logo">Ram<span>Pay</span></div>

    <a href="connexion.html" class="profile">
      👤 Se connecter
    </a>
  </header>

  <section class="welcome">
    <h1>Bienvenue chez RamPay</h1>
    <p>Votre argent, en mouvement.</p>
  </section>

  <section class="balance">
    <p>Mon portefeuille</p>

    <div class="amount">0 FCFA</div>

    <div class="actions">
      <button onclick="alert('Le dépôt sera bientôt disponible')">
        + Déposer
      </button>

      <button onclick="alert('Le transfert sera bientôt disponible')">
        Transférer
      </button>
    </div>
  </section>

  <h2 class="section-title">Nos cartes</h2>

  <div class="card">
    <h3>VISA</h3>
    <p>Carte RamPay Visa</p>
    <div class="price">2 500 FCFA</div>

    <button class="order"
      onclick="commander('visa', this)">
      Commander
    </button>
  </div>

  <div class="card">
    <h3>LIGHT</h3>
    <p>Carte RamPay Light</p>
    <div class="price">4 000 FCFA</div>

    <button class="order"
      onclick="commander('light', this)">
      Commander
    </button>
  </div>

  <div class="card">
    <h3>GOLD</h3>
    <p>Carte RamPay Gold</p>
    <div class="price">6 000 FCFA</div>

    <button class="order"
      onclick="commander('gold', this)">
      Commander
    </button>
  </div>

  <footer>
    © 2026 RamPay. Tous droits réservés.
  </footer>

  <script>

    // CONNEXION AU SERVEUR RAMPAY

    const API_URL =
      "https://rampay-server.onrender.com";

    // COMMANDER UNE CARTE

    async function commander(carte, bouton) {

      const telephone = prompt(
        "Entrez votre numéro de téléphone pour continuer le paiement :"
      );

      if (!telephone || !telephone.trim()) {
        alert("Veuillez entrer un numéro de téléphone.");
        return;
      }

      const ancienTexte = bouton.textContent;

      bouton.disabled = true;
      bouton.textContent = "Préparation...";

      try {

        const response = await fetch(
          API_URL + "/payer",
          {
            method: "POST",

            headers: {
              "Content-Type": "application/json"
            },

            body: JSON.stringify({
              carte: carte,
              telephone: telephone.trim()
            })
          }
        );

        const data = await response.json();

        if (!response.ok || !data.success) {
          throw new Error(
            data.error || "Impossible de préparer le paiement."
          );
        }

        if (!data.url) {
          throw new Error("Lien de paiement introuvable.");
        }

        const paymentUrl = new URL(data.url);

        if (paymentUrl.protocol !== "https:") {
          throw new Error("Lien de paiement non sécurisé.");
        }

        alert("Redirection vers FedaPay. Veuillez patienter.");

        window.location.href = paymentUrl.href;

      } catch (error) {

        console.error("Erreur RamPay :", error);

        alert(
          "Une erreur est survenue : " + error.message
        );

      } finally {

        bouton.disabled = false;
        bouton.textContent = ancienTexte;

      }
    }

  </script>

</body>
</html>

