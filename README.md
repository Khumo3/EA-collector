<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>EA Collector App</title>
  <script src="https://js.yoco.com/sdk/v1/yoco-sdk-web.js"></script>
  <style>
    body {
      margin: 0;
      font-family: Arial, sans-serif;
      background: #000;
      color: white;
      display: flex;
      align-items: center;
      justify-content: center;
      min-height: 100vh;
    }
    .container {
      text-align: center;
      padding: 20px;
    }
    img.logo {
      width: 150px;
      margin-bottom: 20px;
    }
    button {
      padding: 12px 25px;
      margin: 10px;
      font-size: 16px;
      border: none;
      border-radius: 8px;
      background: #00c853;
      color: white;
      cursor: pointer;
    }
    input[type="text"], input[type="email"] {
      padding: 10px;
      font-size: 16px;
      margin: 10px 0;
      border-radius: 5px;
      border: none;
      width: 100%;
      max-width: 300px;
    }
    iframe {
      width: 100vw;
      height: 100vh;
      border: none;
    }
  </style>
</head>
<body>

<!-- Step 1: Email Entry -->
<div class="container" id="emailScreen">
  <img class="logo" src="your-logo.png" alt="EA Collector" />
  <h2>Welcome to EA Collector</h2>
  <p>Enter your email to continue</p>
  <input type="email" id="userEmail" placeholder="Enter your email" />
  <button onclick="checkAccess()">Continue</button>
</div>

<!-- Step 2: Payment -->
<div class="container" id="paymentScreen" style="display:none;">
  <p>This email does not have access. Pay a fee of R500 to continue.</p>
  <button onclick="startPayment()">Pay R500</button>
</div>

<!-- Step 3: Mentor ID + Confirm Email -->
<div class="container" id="accessScreen" style="display:none;">
  <p>Enter your Mentor ID and email to proceed</p>
  <input type="text" id="mentorId" placeholder="Enter Mentor ID" />
  <input type="email" id="confirmEmail" placeholder="Enter your email" />
  <button onclick="verifyAccess()">Continue</button>
</div>

<!-- Step 4: App Content -->
<div id="appScreen" style="display:none;">
  <iframe src="https://eacollectorapp.netlify.app"></iframe>
</div>

<script>
  const freeEmails = [
    "collectorea9@gmail.com",
    "rataukhumo28@gmail.com"
  ];
  let paidEmails = JSON.parse(localStorage.getItem('paidEmails') || '[]');
  let currentEmail = '';

  function checkAccess() {
    const email = document.getElementById('userEmail').value.trim().toLowerCase();
    currentEmail = email;

    if (freeEmails.includes(email) || paidEmails.includes(email)) {
      showMentorScreen();
    } else {
      document.getElementById('emailScreen').style.display = 'none';
      document.getElementById('paymentScreen').style.display = 'block';
    }
  }

  function startPayment() {
    const yoco = new window.YocoSDK({ publicKey: 'pk_live_05923745rr75RK0f23e4' });
    yoco.showPopup({
      amountInCents: 50000,
      currency: 'ZAR',
      name: 'EA Collector Access',
      description: 'App subscription payment',
      callback: function (result) {
        if (result.error) {
          alert("Payment failed: " + result.error.message);
        } else {
          paidEmails.push(currentEmail);
          localStorage.setItem('paidEmails', JSON.stringify(paidEmails));
          showMentorScreen();
        }
      }
    });
  }

  function showMentorScreen() {
    document.getElementById('emailScreen').style.display = 'none';
    document.getElementById('paymentScreen').style.display = 'none';
    document.getElementById('accessScreen').style.display = 'block';
    document.getElementById('confirmEmail').value = currentEmail;
  }

  function verifyAccess() {
    const mentorId = document.getElementById('mentorId').value.trim();
    const email = document.getElementById('confirmEmail').value.trim().toLowerCase();
    if (mentorId && email) {
      document.getElementById('accessScreen').style.display = 'none';
      document.getElementById('appScreen').style.display = 'block';
    } else {
      alert("Please fill in both fields.");
    }
  }
</script>

</body>
</html>
