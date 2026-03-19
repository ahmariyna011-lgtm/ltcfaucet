<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>صنبور لايتكوين - Litecoin Faucet</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
        }

        .faucet-container {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            padding: 40px;
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
            text-align: center;
            max-width: 500px;
            width: 100%;
            border: 1px solid rgba(255, 255, 255, 0.2);
        }

        .logo {
            font-size: 3rem;
            background: linear-gradient(45deg, #f4b400, #ff9f1c);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
            margin-bottom: 10px;
            font-weight: bold;
        }

        h1 {
            color: white;
            font-size: 2rem;
            margin-bottom: 10px;
        }

        .subtitle {
            color: rgba(255, 255, 255, 0.8);
            margin-bottom: 30px;
            font-size: 1.1rem;
        }

        .balance {
            background: rgba(255, 255, 255, 0.2);
            border-radius: 15px;
            padding: 20px;
            margin-bottom: 30px;
            border: 1px solid rgba(255, 255, 255, 0.3);
        }

        .balance-label {
            color: rgba(255, 255, 255, 0.7);
            font-size: 0.9rem;
            margin-bottom: 5px;
        }

        .balance-amount {
            font-size: 2.5rem;
            font-weight: bold;
            background: linear-gradient(45deg, #00ff88, #00cc6a);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }

        .claim-btn {
            background: linear-gradient(45deg, #00ff88, #00cc6a);
            color: white;
            border: none;
            padding: 18px 40px;
            font-size: 1.3rem;
            font-weight: bold;
            border-radius: 50px;
            cursor: pointer;
            transition: all 0.3s ease;
            box-shadow: 0 10px 30px rgba(0, 255, 136, 0.3);
            margin-bottom: 20px;
            width: 100%;
        }

        .claim-btn:hover {
            transform: translateY(-3px);
            box-shadow: 0 15px 40px rgba(0, 255, 136, 0.4);
        }

        .claim-btn:disabled {
            background: #666;
            cursor: not-allowed;
            transform: none;
            box-shadow: none;
        }

        .timer {
            color: rgba(255, 255, 255, 0.9);
            font-size: 1.1rem;
            font-weight: bold;
            margin-bottom: 20px;
        }

        .address-input {
            width: 100%;
            padding: 15px;
            border: none;
            border-radius: 10px;
            font-size: 1rem;
            margin-bottom: 20px;
            text-align: center;
            background: rgba(255, 255, 255, 0.2);
            color: white;
            border: 1px solid rgba(255, 255, 255, 0.3);
        }

        .address-input::placeholder {
            color: rgba(255, 255, 255, 0.6);
        }

        .stats {
            display: flex;
            justify-content: space-around;
            margin-top: 30px;
            gap: 20px;
        }

        .stat {
            background: rgba(255, 255, 255, 0.1);
            padding: 15px;
            border-radius: 10px;
            flex: 1;
            border: 1px solid rgba(255, 255, 255, 0.2);
        }

        .stat-value {
            font-size: 1.5rem;
            font-weight: bold;
            color: #00ff88;
        }

        .stat-label {
            color: rgba(255, 255, 255, 0.7);
            font-size: 0.9rem;
        }

        @media (max-width: 480px) {
            .faucet-container {
                padding: 30px 20px;
            }
            
            .logo {
                font-size: 2.5rem;
            }
            
            h1 {
                font-size: 1.5rem;
            }
        }
    </style>
</head>
<body>
    <div class="faucet-container">
        <div class="logo">₿</div>
        <h1>صنبور لايتكوين</h1>
        <p class="subtitle">احصل على لايتكوين مجاني كل ساعة!</p>

        <div class="balance">
            <div class="balance-label">رصيدك الحالي</div>
            <div class="balance-amount">0.00002500 LTC</div>
        </div>

        <div class="timer" id="timer">الوقت المتبقي: 00:00:00</div>

        <input 
            type="text" 
            class="address-input" 
            id="walletAddress"
            placeholder="أدخل عنوان محفظتك اللايتكوين (LTC)"
        >

        <button class="claim-btn" id="claimBtn" onclick="claimLTC()">
            💧 اطلب اللايتكوين الآن
        </button>

        <div class="stats">
            <div class="stat">
                <div class="stat-value">1,247</div>
                <div class="stat-label">طلبات اليوم</div>
            </div>
            <div class="stat">
                <div class="stat-value">0.025 LTC</div>
                <div class="stat-label">إجمالي اليوم</div>
            </div>
            <div class="stat">
                <div class="stat-value">24 ساعة</div>
                <div class="stat-label">آخر طلب</div>
            </div>
        </div>
    </div>

    <script>
        let timeLeft = 3600; // ساعة واحدة بالثواني
        const claimBtn = document.getElementById('claimBtn');
        const timerEl = document.getElementById('timer');

        function updateTimer() {
            const hours = Math.floor(timeLeft / 3600);
            const minutes = Math.floor((timeLeft % 3600) / 60);
            const seconds = timeLeft % 60;

            timerEl.textContent = `الوقت المتبقي: ${hours.toString().padStart(2, '0')}:${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;

            if (timeLeft > 0) {
                timeLeft--;
                setTimeout(updateTimer, 1000);
            } else {
                claimBtn.disabled = false;
                claimBtn.textContent = '💧 اطلب اللايتكوين الآن';
            }
        }

        function claimLTC() {
            const address = document.getElementById('walletAddress').value;
            
            if (!address) {
                alert('يرجى إدخال عنوان محفظة اللايتكوين');
                return;
            }

            if (!address.startsWith('L') && !address.startsWith('M') && !address.startsWith('ltc1')) {
                alert('عنوان المحفظة غير صحيح');
                return;
            }

            claimBtn.disabled = true;
            claimBtn.textContent = 'جاري المعالجة...';
            
            // محاكاة عملية الطلب
            setTimeout(() => {
                alert(`تم إرسال 0.00002500 LTC إلى العنوان:
${address}`);
                timeLeft = 3600;
                updateTimer();
            }, 2000);
        }

        // بدء العد التنازلي
        updateTimer();
    </script>
</body>
</html>
