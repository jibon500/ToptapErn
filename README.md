<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Adsterra Direct Integration</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background: linear-gradient(135deg, #ff9a9e, #fad0c4);
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
        }
        .header {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            background: linear-gradient(135deg, #1e3c72, #2a5298);
            color: #fff;
            padding: 10px;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
            z-index: 1000;
        }
        .header .message {
            white-space: nowrap;
            animation: scrollText 20s linear infinite;
        }
        @keyframes scrollText {
            0% { transform: translateX(100%); }
            100% { transform: translateX(-100%); }
        }
        .sub-header {
            position: fixed;
            top: 50px; /* Adjust based on main header height */
            left: 0;
            width: 100%;
            background: linear-gradient(135deg, #ff6f61, #ffcc00);
            color: #fff;
            padding: 10px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
            z-index: 1000;
        }
        .sub-header .time {
            font-weight: bold;
        }
        .sub-header .ip-address {
            font-weight: bold;
        }
        .container {
            background: linear-gradient(135deg, #ff6f61, #ffcc00);
            padding: 30px;
            border-radius: 15px;
            box-shadow: 0 10px 20px rgba(0, 0, 0, 0.2);
            text-align: center;
            color: #fff;
            margin-top: 100px; /* Adjust for headers */
        }
        .balance {
            font-size: 28px;
            font-weight: bold;
            margin-bottom: 20px;
        }
        .button {
            background: linear-gradient(135deg, #ff416c, #ff4b2b);
            color: white;
            padding: 12px 24px;
            border: none;
            border-radius: 25px;
            cursor: pointer;
            font-size: 16px;
            margin: 10px;
            transition: background 0.3s ease;
        }
        .button:hover {
            background: linear-gradient(135deg, #ff4b2b, #ff416c);
        }
        .withdraw-button {
            background: linear-gradient(135deg, #00c6ff, #0072ff);
        }
        .withdraw-button:hover {
            background: linear-gradient(135deg, #0072ff, #00c6ff);
        }
        .telegram-button {
            background: linear-gradient(135deg, #1e3c72, #2a5298);
        }
        .telegram-button:hover {
            background: linear-gradient(135deg, #2a5298, #1e3c72);
        }
        .popup {
            display: none;
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: linear-gradient(135deg, #ff6f61, #ffcc00);
            padding: 20px;
            border-radius: 15px;
            box-shadow: 0 10px 20px rgba(0, 0, 0, 0.2);
            z-index: 1000;
            color: #fff;
        }
        .popup h2 {
            margin-bottom: 20px;
            color: #fff;
        }
        .popup input, .popup select {
            width: 100%;
            padding: 10px;
            margin: 10px 0;
            border: none;
            border-radius: 25px;
            background: rgba(255, 255, 255, 0.2);
            color: #fff;
        }
        .popup input::placeholder {
            color: rgba(255, 255, 255, 0.7);
        }
        .popup button {
            background: linear-gradient(135deg, #ff416c, #ff4b2b);
            color: white;
            padding: 10px 20px;
            border: none;
            border-radius: 25px;
            cursor: pointer;
            margin: 5px;
            transition: background 0.3s ease;
        }
        .popup button.cancel {
            background: linear-gradient(135deg, #ff4b2b, #ff416c);
        }
        .popup button.cancel:hover {
            background: linear-gradient(135deg, #ff416c, #ff4b2b);
        }
    </style>
</head>
<body>

<div class="header">
    <div class="message">Best Income Site</div>
</div>

<div class="sub-header">
    <div class="time">Time: <span id="bdClock">Loading...</span></div>
    <div class="ip-address">Your IP: <span id="userIp">Loading...</span></div>
</div>

<div class="container">
    <div class="balance">Balance: <span id="balance">0.00000 USD</span></div>
    <button class="button" onclick="showAd()">Earn Money</button>
    <button class="button withdraw-button" onclick="openWithdrawPopup()">Withdraw</button>
    <button class="button telegram-button" onclick="window.open('https://t.me/yourtelegramlink', '_blank')">Join Telegram</button>
</div>

<div id="withdrawPopup" class="popup">
    <h2>Withdraw Funds</h2>
    <select id="paymentMethod">
        <option value="bkash">Bkash</option>
        <option value="rocket">Rocket</option>
        <option value="nagad">Nagad</option>
    </select>
    <input type="text" id="mobileNumber" placeholder="Enter Mobile Number">
    <input type="text" id="amount" placeholder="Enter Amount in USD">
    <button onclick="submitWithdraw()">Submit</button>
    <button class="cancel" onclick="closeWithdrawPopup()">Cancel</button>
</div>

<div id="countdownPopup" class="popup">
    <h2>Withdrawal in Progress</h2>
    <p>Time remaining: <span id="countdown">24:00:00</span></p>
    <button class="cancel" onclick="cancelWithdraw()">Cancel</button>
</div>

<script>
    // Fetch user IP address
    fetch('https://api.ipify.org?format=json')
        .then(response => response.json())
        .then(data => {
            const userIp = data.ip;
            document.getElementById('userIp').innerText = userIp;
            localStorage.setItem('userIp', userIp); // Save IP to localStorage
        })
        .catch(error => console.error('Error fetching IP:', error));

    // Bangladesh Time Clock
    function updateBangladeshTime() {
        const options = { timeZone: 'Asia/Dhaka', hour: '2-digit', minute: '2-digit', second: '2-digit', hour12: true };
        const bdTime = new Date().toLocaleTimeString('en-US', options);
        document.getElementById('bdClock').innerText = bdTime;
    }
    setInterval(updateBangladeshTime, 1000); // Update every second
    updateBangladeshTime(); // Initial call

    // Load balance from localStorage
    let balance = parseFloat(localStorage.getItem('userBalance')) || 0.00.000;
    document.getElementById('balance').innerText = balance.toFixed(5) + ' USD';

    let countdownInterval;

    function showAd() {
        // Simulate ad view and add to balance
        balance += 0.00.012; // Increment by 0.00.012 USD
        document.getElementById('balance').innerText = balance.toFixed(5) + ' USD';
        localStorage.setItem('userBalance', balance); // Save balance to localStorage

        // Trigger Adsterra directly
        try {
            window.open('https://www.effectiveratecpm.com/jnf86rs9n?key=0d5ffc804cf69b1c9c36358068cb80be', '_blank');
        } catch (e) {
            console.error('Adsterra blocked by browser');
        }
    }

    function openWithdrawPopup() {
        if (balance < 10) {
            alert('You need at least 10 USD to withdraw.');
            return;
        }
        document.getElementById('withdrawPopup').style.display = 'block';
    }

    function closeWithdrawPopup() {
        document.getElementById('withdrawPopup').style.display = 'none';
    }

    function submitWithdraw() {
        const paymentMethod = document.getElementById('paymentMethod').value;
        const mobileNumber = document.getElementById('mobileNumber').value;
        const amount = parseFloat(document.getElementById('amount').value);

        if (amount > balance) {
            alert('Insufficient balance');
            return;
        }

        // Deduct the withdrawal amount from balance
        balance -= amount;
        document.getElementById('balance').innerText = balance.toFixed(5) + ' USD';
        localStorage.setItem('userBalance', balance); // Update localStorage

        closeWithdrawPopup();
        document.getElementById('countdownPopup').style.display = 'block';
        startCountdown();
    }

    function startCountdown() {
        let timeLeft = 24 * 60 * 60; // 24 hours in seconds
        countdownInterval = setInterval(() => {
            timeLeft--;
            if (timeLeft <= 0) {
                clearInterval(countdownInterval);
                document.getElementById('countdownPopup').innerHTML = '<h2>Withdrawal Successful</h2>';
            } else {
                const hours = Math.floor(timeLeft / 3600);
                const minutes = Math.floor((timeLeft % 3600) / 60);
                const seconds = timeLeft % 60;
                document.getElementById('countdown').innerText = `${String(hours).padStart(2, '0')}:${String(minutes).padStart(2, '0')}:${String(seconds).padStart(2, '0')}`;
            }
        }, 1000);
    }

    function cancelWithdraw() {
        clearInterval(countdownInterval);
        document.getElementById('countdownPopup').style.display = 'none';
    }
</script>
<script type='text/javascript' src='//pl26020249.effectiveratecpm.com/44/7b/c6/447bc60ec6ef8629e5f39bc0559f71ba.js'></script>
</body>
</html>
