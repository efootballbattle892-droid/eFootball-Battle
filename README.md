<!DOCTYPE html>
<html lang="bn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>eFootball Pro Tournaments</title>
    
    <!-- Firebase Scripts -->
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-analytics-compat.js"></script>

    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
        body { background-color: #0b0f19; color: #f3f4f6; line-height: 1.6; padding-bottom: 70px; }
        
        .auth-wrapper { display: flex; justify-content: center; align-items: center; min-height: 100vh; padding: 20px; background-color: #0b0f19; position: fixed; top: 0; left: 0; width: 100%; height: 100%; z-index: 9999; }
        .auth-container { width: 100%; max-width: 420px; background: linear-gradient(145deg, #171f2d, #111827); padding: 35px 30px; border-radius: 16px; box-shadow: 0 12px 35px rgba(0,0,0,0.6); border: 1px solid #2d3748; position: relative; overflow: hidden; }
        .auth-container::before { content: ''; position: absolute; top: 0; left: 0; width: 100%; height: 4px; background: linear-gradient(90deg, #25d366, #3b82f6); }
        .auth-container h2 { text-align: center; margin-bottom: 25px; color: #fff; font-size: 24px; font-weight: 700; }
        .auth-container h2 span { color: #25d366; }
        
        .form-group { margin-bottom: 18px; }
        .form-group label { display: block; margin-bottom: 6px; font-size: 13px; color: #94a3b8; font-weight: 500; text-transform: uppercase; }
        .form-group input, .form-group select, .form-group textarea { width: 100%; padding: 12px 15px; border-radius: 8px; border: 1px solid #334155; background: #0f172a; color: #fff; font-size: 15px; outline: none; }
        .form-group input:focus, .form-group select:focus, .form-group textarea:focus { border-color: #25d366; box-shadow: 0 0 0 3px rgba(37, 211, 102, 0.15); }
        
        .btn-submit { width: 100%; background: linear-gradient(135deg, #25d366, #20ba5a); color: #fff; border: none; padding: 14px; border-radius: 8px; font-weight: bold; cursor: pointer; font-size: 16px; margin-top: 10px; transition: all 0.3s ease; }
        .btn-submit:hover { opacity: 0.9; }
        .btn-submit:disabled { background: #4b5563; cursor: not-allowed; }
        
        .switch-text { text-align: center; margin-top: 20px; font-size: 14px; color: #94a3b8; }
        .switch-text span { color: #3b82f6; cursor: pointer; font-weight: 600; text-decoration: underline; }
        
        nav { background: #1f2937; padding: 10px 15px; display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid #374151; position: sticky; top: 0; z-index: 100; }
        .logo { font-size: 16px; font-weight: bold; color: #25d366; cursor: pointer; }
        .nav-right { display: flex; align-items: center; gap: 8px; }
        .balance-badge { background: #374151; padding: 5px 10px; border-radius: 20px; font-size: 11px; color: #25d366; font-weight: bold; border: 1px solid #4b5563; }
        .profile-icon { width: 32px; height: 32px; background: #25d366; color: #fff; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-weight: bold; cursor: pointer; position: relative; font-size: 14px; overflow: hidden; background-size: cover; background-position: center; }
        
        .main-container { max-width: 900px; margin: 20px auto; padding: 15px; }
        .box { background: #1f2937; border-radius: 10px; padding: 20px; margin-bottom: 20px; border: 1px solid #374151; }
        
        .notice-board { background: linear-gradient(135deg, #7f1d1d, #450a0a); border: 1px solid #ef4444; padding: 12px 15px; border-radius: 10px; margin-bottom: 15px; font-size: 13px; color: #fecaca; }
        .notice-board b { color: #f87171; }

        .tabs { display: flex; gap: 8px; margin-bottom: 15px; overflow-x: auto; padding-bottom: 5px; }
        .tab-btn { background: #374151; color: #fff; border: none; padding: 8px 14px; border-radius: 6px; cursor: pointer; font-weight: bold; white-space: nowrap; font-size: 12px; }
        .tab-btn.active { background: #25d366; color: #fff; }

        .bottom-nav { position: fixed; bottom: 0; left: 0; width: 100%; background: #1f2937; display: flex; justify-content: space-around; padding: 10px 0; border-top: 1px solid #374151; z-index: 1000; }
        .bottom-nav-item { text-align: center; color: #94a3b8; font-size: 12px; font-weight: bold; cursor: pointer; flex: 1; }
        .bottom-nav-item.active { color: #25d366; }

        .profile-card { background: #1e293b; padding: 20px; border-radius: 12px; border: 1px solid #334155; }
        .profile-info-row { display: flex; justify-content: space-between; padding: 10px 0; border-bottom: 1px solid #334155; font-size: 14px; }
        .profile-info-row span:last-child { font-weight: bold; color: #25d366; }

        .match-card { background: #1e293b; border: 1px solid #334155; padding: 15px; border-radius: 10px; margin-bottom: 12px; font-size: 13px; box-shadow: 0 4px 6px rgba(0,0,0,0.1); }
    </style>

    <script type='text/javascript'>
        const firebaseConfig = {
            apiKey: "AIzaSyBlYj9lohn53maKIp68pbbZEQJKDgsMgug",
            authDomain: "victory-efootball-battle.firebaseapp.com",
            projectId: "victory-efootball-battle",
            storageBucket: "victory-efootball-battle.firebasestorage.app",
            messagingSenderId: "781703585699",
            appId: "1:781703585699:web:456ba882ba652a954c0416",
            measurementId: "G-4DT2T114CG"
        };

        if (!firebase.apps.length) {
            firebase.initializeApp(firebaseConfig);
        }
        const db = firebase.firestore();

        const TELEGRAM_BOT_TOKEN = "7659515596:AAGVtejXRJQ5XGr7hmBvpyyV7-Nd14Mj8l4"; 
        const TELEGRAM_CHAT_ID = "8681927379";
        const IMGBB_API_KEY = "6d207e02198a847aa98d0a2a901485a2";
        const SITE_URL = "https://efootballbattle892-droid.github.io/eFootball-Battle/";

        const freeAdsList = [
            "https://www.profitableratecpmnetwork.com/us3wvb14?key=09cb11cc21ce1aa36a64d3dfc28b454c",
            "https://www.profitableratecpmnetwork.com/j21pmfmj54?key=83c92a4be2b8df1b736c7207e4471498",
            "https://www.profitableratecpmnetwork.com/stb51d4d?key=c6f00199bc014a69f7b97b688e3c824e",
            "https://www.profitableratecpmnetwork.com/puu0sj5i?key=31a6e12b54638b8207033e6a9bd06902",
            "https://www.profitableratecpmnetwork.com/sy8g4tnr3?key=f73048f9f99f8380d066b679b7b05370",
            "https://www.profitableratecpmnetwork.com/rekevt8z3w?key=55348ae12e370be0fda5a0712adbf5a8",
            "https://www.profitableratecpmnetwork.com/rvyqpiwv?key=41a8cb649637c78fd9eb9acf70709725",
            "https://www.profitableratecpmnetwork.com/mqaxkdcgt?key=d392235ca9c6b88e5eaef7b27e27de81",
            "https://www.profitableratecpmnetwork.com/y2atu1kv3i?key=40f65014cd039bfa8b11a10e5a2502ad",
            "https://www.profitableratecpmnetwork.com/pbhd6bygib?key=be86751f2bf6b99a3c4543625ec5cb50"
        ];

        let watchedAdsCount = 0;
        let completedSharesCount = 0;

        const countriesList = [
            "Argentina", "Brazil", "France", "Portugal", "England", 
            "Germany", "Spain", "Italy", "Netherlands", "Belgium", 
            "Croatia", "Japan", "South Korea", "Morocco", "Uruguay", "Colombia"
        ];

        window.addEventListener('DOMContentLoaded', () => {
            checkAuth();
            populateCountriesDropdown();
            loadAdminNotice();
            loadMarketplaceList();
            updateFreeTaskUI();
            calculateCoinPrice();
            loadFreeTournamentsList();
        });

        function populateCountriesDropdown(takenCountries = []) {
            let select = document.getElementById("paid-country-select");
            let freeSelect = document.getElementById("free-country-select");
            
            [select, freeSelect].forEach(s => {
                if (!s) return;
                s.innerHTML = "<option value=''>দেশ নির্বাচন করুন</option>";
                countriesList.forEach(country => {
                    if (!takenCountries.includes(country) || s.id === "free-country-select") {
                        let opt = document.createElement("option");
                        opt.value = country;
                        opt.innerText = country;
                        s.appendChild(opt);
                    }
                });
            });
        }

        function checkAuth() {
            let isLogged = localStorage.getItem("isLoggedIn");
            if (isLogged !== "true") {
                document.getElementById("auth-wrapper-box").style.display = "flex";
                document.getElementById("signup-box").style.display = "block";
                document.getElementById("login-box").style.display = "none";
                document.getElementById("main-site-content").style.display = "none";
            } else {
                document.getElementById("auth-wrapper-box").style.display = "none";
                document.getElementById("main-site-content").style.display = "block";
                loadUserData();
                loadMatchesList();
            }
        }

        function loadUserData() {
            let user = JSON.parse(localStorage.getItem("registeredUser")) || { 
                name: "Player", phone: "", balance: 0, 
                pvpCount: 0, paidCount: 0, profileImg: "" 
            };
            
            if (user.phone) {
                db.collection("users").doc(user.phone).get().then((doc) => {
                    if (doc.exists) {
                        let data = doc.data();
                        user.balance = data.balance || 0;
                        user.pvpCount = data.pvpCount || 0;
                        user.paidCount = data.paidCount || 0;
                        user.profileImg = data.profileImg || "";
                        localStorage.setItem("registeredUser", JSON.stringify(user));
                        updateUI(user);
                    } else {
                        updateUI(user);
                    }
                }).catch(() => { updateUI(user); });
            } else {
                updateUI(user);
            }
        }

        function updateUI(user) {
            document.getElementById("user-balance-badge").innerText = (user.balance || 0) + " Tk";
            
            let avatarLetter = (user.name || "P").charAt(0).toUpperCase();
            let navAvatar = document.getElementById("profile-avatar-letter");
            
            if (user.profileImg) {
                navAvatar.style.backgroundImage = "url('" + user.profileImg + "')";
                navAvatar.innerText = "";
            } else {
                navAvatar.style.backgroundImage = "none";
                navAvatar.innerText = avatarLetter;
            }

            document.getElementById("prof-name").innerText = user.name || "-";
            document.getElementById("prof-phone").innerText = user.phone || "-";
            document.getElementById("prof-balance").innerText = (user.balance || 0) + " Tk";
            document.getElementById("prof-pvp").innerText = (user.pvpCount || 0) + " টি";
            document.getElementById("prof-paid").innerText = (user.paidCount || 0) + " টি";
        }

        function calculateCoinPrice() {
            let coinInput = document.getElementById("coin-amount-input");
            let priceDisplay = document.getElementById("coin-price-display");
            if(!coinInput || !priceDisplay) return;

            let coins = parseInt(coinInput.value) || 0;
            let price = (coins * 0.90).toFixed(2);
            priceDisplay.innerText = price + " Tk";
        }

        function handleCoinBuy(event) {
            event.preventDefault();
            let coins = parseInt(document.getElementById("coin-amount-input").value) || 0;
            let gameId = document.getElementById("coin-game-id").value.trim();
            let konamiGmail = document.getElementById("coin-konami-gmail").value.trim();
            let konamiPass = document.getElementById("coin-konami-pass").value.trim();
            let user = JSON.parse(localStorage.getItem("registeredUser")) || {};

            if (coins < 10) { alert("⚠️ সর্বনিম্ন ১০ কয়েন সিলেক্ট করতে হবে।"); return; }
            let totalPrice = (coins * 0.90).toFixed(2);

            if ((user.balance || 0) < parseFloat(totalPrice)) {
                alert("⚠️ আপনার একাউন্টে পর্যাপ্ত টাকা ব্যালেন্স নেই!");
                return;
            }

            user.balance = (user.balance || 0) - parseFloat(totalPrice);
            localStorage.setItem("registeredUser", JSON.stringify(user));
            if(user.phone) db.collection("users").doc(user.phone).update({ balance: user.balance });
            loadUserData();

            let msg = "🪙 নতুন কয়েন ক্রয় রিকোয়েস্ট!\n👤 ক্রেতা: " + user.name + " (" + user.phone + ")\n🎮 গেম আইডি: " + gameId + "\n📧 কোনামি জিমেইল: " + konamiGmail + "\n🔑 কোনামি পাসওয়ার্ড: " + konamiPass + "\n🪙 কয়েন: " + coins + "\n💵 খরচ: " + totalPrice + " Tk";
            sendTelegramMessage(msg, "সফলভাবে কয়েন ক্রয়ের রিকোয়েস্ট পাঠানো হয়েছে!");
            event.target.reset();
            calculateCoinPrice();
        }

        function uploadMarketScreenshot() {
            let fileInput = document.getElementById("market-file-input");
            let urlInput = document.getElementById("market-img-url");
            if (fileInput.files.length === 0) return;

            let file = fileInput.files[0];
            let formData = new FormData();
            formData.append("image", file);
            urlInput.value = "আপলোড হচ্ছে...";

            fetch("https://api.imgbb.com/1/upload?key=" + IMGBB_API_KEY, { method: "POST", body: formData })
            .then(res => res.json())
            .then(data => {
                if (data.success) { urlInput.value = data.data.url; } 
                else { alert("ছবি আপলোড ব্যর্থ হয়েছে।"); urlInput.value = ""; }
            })
            .catch(() => { alert("ইন্টারনেট সমস্যা।"); urlInput.value = ""; });
        }

        function uploadFreeScreenshot() {
            let fileInput = document.getElementById("free-file-input");
            let urlInput = document.getElementById("free-img-url");
            if (fileInput.files.length === 0) return;

            let file = fileInput.files[0];
            let formData = new FormData();
            formData.append("image", file);
            urlInput.value = "আপলোড হচ্ছে...";

            fetch("https://api.imgbb.com/1/upload?key=" + IMGBB_API_KEY, { method: "POST", body: formData })
            .then(res => res.json())
            .then(data => {
                if (data.success) { urlInput.value = data.data.url; } 
                else { alert("ছবি আপলোড ব্যর্থ হয়েছে।"); urlInput.value = ""; }
            })
            .catch(() => { alert("ইন্টারনেট সমস্যা।"); urlInput.value = ""; });
        }

        function handleSellPostSubmit(event) {
            event.preventDefault();
            let title = document.getElementById("market-title").value.trim();
            let price = parseFloat(document.getElementById("market-price").value);
            let konamiGmail = document.getElementById("market-konami-gmail").value.trim();
            let konamiPass = document.getElementById("market-konami-pass").value.trim();
            let imgUrl = document.getElementById("market-img-url").value.trim();
            let user = JSON.parse(localStorage.getItem("registeredUser")) || {};

            let postData = {
                itemType: "eFootball ID Sell",
                title: title,
                price: price,
                konamiGmail: konamiGmail,
                konamiPass: konamiPass,
                imgUrl: imgUrl,
                sellerName: user.name,
                sellerPhone: user.phone,
                timestamp: firebase.firestore.FieldValue.serverTimestamp()
            };

            db.collection("marketplace").add(postData).then(() => {
                alert("✅ সফলভাবে আপনার আইডি বিক্রির বিজ্ঞাপন পোস্ট করা হয়েছে!");
                event.target.reset();
                document.getElementById("market-img-url").value = "";
                loadMarketplaceList();
            }).catch(err => alert("ত্রুটি: " + err.message));
        }

        function deleteMarketPost(postId, sellerPhone) {
            let user = JSON.parse(localStorage.getItem("registeredUser")) || {};
            if (user.phone !== sellerPhone) { alert("⚠️ অনুমতি নেই!"); return; }
            if (!confirm("ডিলিট করতে চান?")) return;

            db.collection("marketplace").doc(postId).delete().then(() => {
                alert("✅ পোস্ট ডিলিট হয়েছে!");
                loadMarketplaceList();
            });
        }

        function loadMarketplaceList() {
            let container = document.getElementById("marketplace-items-container");
            if (!container) return;
            let user = JSON.parse(localStorage.getItem("registeredUser")) || {};

            db.collection("marketplace").orderBy("timestamp", "desc").get().then(snapshot => {
                container.innerHTML = "";
                if (snapshot.empty) { container.innerHTML = "<p style='font-size:13px; color:#94a3b8;'>কোনো পোস্ট নেই।</p>"; return; }

                snapshot.forEach(doc => {
                    let d = doc.data(), docId = doc.id;
                    let div = document.createElement("div");
                    div.style.cssText = "background:#1e293b; border:1px solid #334155; padding:12px; border-radius:8px; margin-bottom:12px;";
                    
                    let deleteBtn = (user.phone && user.phone === d.sellerPhone) ? `<button onclick='deleteMarketPost("${docId}", "${d.sellerPhone}")' style='margin-top:8px; width:100%; background:#ef4444; color:#fff; padding:6px; border-radius:6px; font-size:12px; border:none; cursor:pointer;'>🗑️ ডিলিট পোস্ট</button>` : "";

                    div.innerHTML = `
                        <b style='color:#facc15;'>🎮 ${d.title}</b> - <span style='color:#25d366;'>${d.price} Tk</span>
                        <p style='font-size:12px; color:#cbd5e1; margin:6px 0;'>বিক্রেতা: ${d.sellerName}</p>
                        <a href='${d.imgUrl}' target='_blank'><img src='${d.imgUrl}' style='width:100%; max-height:150px; object-fit:cover; border-radius:6px;' onerror="this.onerror=null; this.src='https://via.placeholder.com/400x150?text=Image+Not+Found';"></a>
                        <button onclick='buyIdAccount("${docId}", ${d.price}, "${d.konamiGmail}", "${d.konamiPass}", "${d.title}")' style='margin-top:6px; width:100%; background:#25d366; color:#fff; padding:8px; border:none; border-radius:6px; font-weight:bold; cursor:pointer;'>🛒 আইডি কিনুন</button>
                        ${deleteBtn}
                    `;
                    container.appendChild(div);
                });
            });
        }

        function buyIdAccount(postId, price, gmail, pass, title) {
            let user = JSON.parse(localStorage.getItem("registeredUser")) || {};
            if ((user.balance || 0) < price) { alert("⚠️ পর্যাপ্ত ব্যালেন্স নেই!"); return; }
            if (!confirm(price + " টাকা দিয়ে আইডিটি কিনতে চান?")) return;

            user.balance -= price;
            localStorage.setItem("registeredUser", JSON.stringify(user));
            if (user.phone) db.collection("users").doc(user.phone).update({ balance: user.balance });
            loadUserData();

            alert("✅ সফল! কোনামি জিমেইল: " + gmail + " | পাসওয়ার্ড: " + pass);
            loadMarketplaceList();
        }

        function watchFreeAd() {
            if (watchedAdsCount >= 10) { alert("✅ ১০টি অ্যাড দেখা সম্পন্ন!"); return; }
            window.open(freeAdsList[watchedAdsCount], '_blank');
            watchedAdsCount++;
            updateFreeTaskUI();
        }

        function shareFreeSite(platform) {
            if (completedSharesCount >= 3) { alert("✅ শেয়ার সম্পন্ন!"); return; }
            window.open(SITE_URL, '_blank');
            completedSharesCount++;
            updateFreeTaskUI();
        }

        function updateFreeTaskUI() {
            let adStatusEl = document.getElementById("free-ad-status");
            let shareStatusEl = document.getElementById("free-share-status");
            let submitBtn = document.getElementById("free-submit-btn");
            let formFields = document.getElementById("free-form-fields");

            if (adStatusEl) adStatusEl.innerText = watchedAdsCount + "/10";
            if (shareStatusEl) shareStatusEl.innerText = completedSharesCount + "/3";

            if (watchedAdsCount >= 10 && completedSharesCount >= 3) {
                if (formFields) formFields.style.display = "block";
                if (submitBtn) submitBtn.disabled = false;
            } else {
                if (formFields) formFields.style.display = "none";
                if (submitBtn) submitBtn.disabled = true;
            }
        }

        function applyFreeTournament(event) {
            event.preventDefault();
            if (watchedAdsCount < 10 || completedSharesCount < 3) { alert("⚠️ শর্ত পূরণ হয়নি!"); return; }

            let user = JSON.parse(localStorage.getItem("registeredUser")) || {};
            let ingameName = document.getElementById("free-ingame-name").value.trim();
            let ingameId = document.getElementById("free-ingame-id").value.trim();
            let country = document.getElementById("free-country-select").value;
            let whatsapp = document.getElementById("free-whatsapp").value.trim();
            let imgUrl = document.getElementById("free-img-url").value.trim();

            if (!ingameName || !ingameId || !country || !whatsapp || !imgUrl) {
                alert("⚠️ সব তথ্য এবং স্ক্রিনশট লিংক দিন!");
                return;
            }

            db.collection("free_tournaments").where("userPhone", "==", user.phone).get().then(snapshot => {
                if (!snapshot.empty) {
                    alert("⚠️ আপনি এই ফ্রি টুর্নামেন্টে ইতিমধ্যে একবার আবেদন করেছেন! পুনরায় আবেদন করা যাবে না।");
                    return;
                }

                let freeData = {
                    ingameName: ingameName,
                    ingameId: ingameId,
                    country: country,
                    whatsapp: whatsapp,
                    imgUrl: imgUrl,
                    userPhone: user.phone,
                    userName: user.name,
                    timestamp: firebase.firestore.FieldValue.serverTimestamp()
                };

                db.collection("free_tournaments").add(freeData).then(() => {
                    alert("✅ সফলভাবে ফ্রি টুর্নামেন্টে আবেদন হয়েছে! (প্রতি ৭ দিন পর পর নতুন টুর্নামেন্ট ওপেন হয়)");
                    event.target.reset();
                    document.getElementById("free-img-url").value = "";
                    watchedAdsCount = 0;
                    completedSharesCount = 0;
                    updateFreeTaskUI();
                    loadFreeTournamentsList();
                });
            });
        }

        function loadFreeTournamentsList() {
            let container = document.getElementById("free-matches-container");
            if (!container) return;

            db.collection("free_tournaments").orderBy("timestamp", "desc").get().then(snapshot => {
                container.innerHTML = "";
                if (snapshot.empty) { container.innerHTML = "<p style='font-size:12px; color:#94a3b8;'>কোনো আবেদন নেই।</p>"; return; }

                snapshot.forEach(doc => {
                    let d = doc.data();
                    let div = document.createElement("div");
                    div.style.cssText = "background:#1e293b; padding:10px; border-radius:8px; margin-bottom:8px; font-size:12px; border:1px solid #334155;";
                    div.innerHTML = `<b>🎮 ইন-গেম নাম: ${d.ingameName}</b> (ID: ${d.ingameId})<br>🌍 দেশ: <span style='color:#38bdf8;'>${d.country}</span> | 📱 WhatsApp: ${d.whatsapp}`;
                    container.appendChild(div);
                });
            });
        }

        function loadAdminNotice() {
            db.collection("settings").doc("notice").get().then((doc) => {
                let noticeBox = document.getElementById("dynamic-admin-notice");
                if (doc.exists && doc.data().text && noticeBox) {
                    noticeBox.innerHTML = "📢 <b>জরুরি নোটিশ:</b> " + doc.data().text;
                    noticeBox.style.display = "block";
                }
            }).catch(() => {});
        }

        function switchToLogin() {
            document.getElementById("signup-box").style.display = "none";
            document.getElementById("login-box").style.display = "block";
        }

        function switchToSignup() {
            document.getElementById("login-box").style.display = "none";
            document.getElementById("signup-box").style.display = "block";
        }

        function handleSignup(event) {
            event.preventDefault();
            let name = document.getElementById("su-name").value.trim();
            let phone = document.getElementById("su-phone").value.trim();
            let pass = document.getElementById("su-pass").value.trim();

            db.collection("users").doc(phone).get().then((doc) => {
                if (doc.exists) {
                    alert("⚠️ এই নম্বর দিয়ে অ্যাকাউন্ট রয়েছে!");
                    switchToLogin();
                } else {
                    let userData = { name: name, phone: phone, pass: pass, balance: 0, pvpCount: 0, paidCount: 0, profileImg: "" };
                    db.collection("users").doc(phone).set(userData).then(() => {
                        localStorage.setItem("registeredUser", JSON.stringify(userData));
                        localStorage.setItem("isLoggedIn", "true");
                        alert("সাইন আপ সফল!");
                        checkAuth();
                    });
                }
            });
        }

        function handleLogin(event) {
            event.preventDefault();
            let phone = document.getElementById("li-phone").value.trim();
            let pass = document.getElementById("li-pass").value.trim();

            db.collection("users").doc(phone).get().then((doc) => {
                if (doc.exists && doc.data().pass === pass) {
                    let data = doc.data();
                    let userData = { name: data.name, phone: data.phone, pass: data.pass, balance: data.balance || 0, pvpCount: data.pvpCount || 0, paidCount: data.paidCount || 0, profileImg: data.profileImg || "" };
                    localStorage.setItem("registeredUser", JSON.stringify(userData));
                    localStorage.setItem("isLoggedIn", "true");
                    alert("লগইন সফল!");
                    checkAuth();
                } else {
                    alert("ভুল নম্বর বা পাসওয়ার্ড!");
                }
            });
        }

        function handleLogout() {
            localStorage.removeItem("isLoggedIn");
            localStorage.removeItem("registeredUser");
            checkAuth();
        }

        function switchSection(sectionName) {
            document.getElementById("home-section").style.display = (sectionName === 'home') ? 'block' : 'none';
            document.getElementById("profile-section").style.display = (sectionName === 'profile') ? 'block' : 'none';
            
            let navItems = document.querySelectorAll('.bottom-nav-item');
            navItems.forEach(item => item.classList.remove('active'));
            if(sectionName === 'home') navItems[0].classList.add('active');
            else if(sectionName === 'profile') navItems[1].classList.add('active');
        }

        function switchMainTab(tabName) {
            document.getElementById("tab-content-paid").style.display = (tabName === 'paid') ? 'block' : 'none';
            document.getElementById("tab-content-slot").style.display = (tabName === 'slot') ? 'block' : 'none';
            document.getElementById("tab-content-free").style.display = (tabName === 'free') ? 'block' : 'none';
            document.getElementById("tab-content-market").style.display = (tabName === 'market') ? 'block' : 'none';
            document.getElementById("tab-content-coin").style.display = (tabName === 'coin') ? 'block' : 'none';
            
            document.getElementById("main-tab-paid").className = (tabName === 'paid') ? 'tab-btn active' : 'tab-btn';
            document.getElementById("main-tab-slot").className = (tabName === 'slot') ? 'tab-btn active' : 'tab-btn';
            document.getElementById("main-tab-free").className = (tabName === 'free') ? 'tab-btn active' : 'tab-btn';
            document.getElementById("main-tab-market").className = (tabName === 'market') ? 'tab-btn active' : 'tab-btn';
            document.getElementById("main-tab-coin").className = (tabName === 'coin') ? 'tab-btn active' : 'tab-btn';

            if(tabName === 'market') loadMarketplaceList();
            if(tabName === 'free') loadFreeTournamentsList();
        }

        function switchProfileSubTab(tabName) {
            document.getElementById("profile-main-view").style.display = (tabName === 'main') ? 'block' : 'none';
            document.getElementById("profile-deposit-view").style.display = (tabName === 'deposit') ? 'block' : 'none';
            document.getElementById("profile-withdraw-view").style.display = (tabName === 'withdraw') ? 'block' : 'none';
        }

        function switchPaidRoomTab(amount, btnElement) {
            let buttons = document.querySelectorAll('#paid-room-tabs-container .tab-btn');
            buttons.forEach(btn => btn.classList.remove('active'));
            if(btnElement) btnElement.classList.add('active');
            document.getElementById("paid-room-select").value = amount;
            loadPaidTournamentsList(); 
        }

        function sendTelegramMessage(message, successMessage) {
            let url = "https://api.telegram.org/bot" + TELEGRAM_BOT_TOKEN + "/sendMessage";
            let data = { chat_id: TELEGRAM_CHAT_ID, text: message, parse_mode: "HTML" };
            fetch(url, { method: "POST", headers: { "Content-Type": "application/json" }, body: JSON.stringify(data) })
            .then(res => res.json())
            .then(resData => { if(resData.ok && successMessage) alert(successMessage); });
        }

        function handleDeposit(event) {
            event.preventDefault();
            let method = document.getElementById("dep-method").value;
            let amount = parseFloat(document.getElementById("dep-amount").value);
            let senderNo = document.getElementById("dep-sender-no").value.trim();
            let trxId = document.getElementById("dep-trxid").value.trim();
            let user = JSON.parse(localStorage.getItem("registeredUser")) || {};

            let message = "💰 ডিপোজিট রিকোয়েস্ট!\n👤 " + user.name + " (" + user.phone + ")\n💳 " + method + " - " + amount + " Tk\n📱 প্রেরক: " + senderNo + "\n🔑 TrxID: " + trxId;
            sendTelegramMessage(message, "ডিপোজিট রিকোয়েস্ট পাঠানো হয়েছে!");
            event.target.reset();
            switchProfileSubTab('main');
        }

        function handleWithdraw(event) {
            event.preventDefault();
            let method = document.getElementById("wd-method").value;
            let amount = parseFloat(document.getElementById("wd-amount").value);
            let targetPhone = document.getElementById("wd-target-phone").value.trim();
            let user = JSON.parse(localStorage.getItem("registeredUser")) || {};

            if (amount > (user.balance || 0)) { alert("⚠️ পর্যাপ্ত ব্যালেন্স নেই!"); return; }

            user.balance -= amount;
            localStorage.setItem("registeredUser", JSON.stringify(user));
            if (user.phone) db.collection("users").doc(user.phone).update({ balance: user.balance });
            loadUserData();

            let message = "💳 উইথড্র রিকোয়েস্ট!\n👤 " + user.name + " (" + user.phone + ")\n💳 " + method + " - " + amount + " Tk\n📱 নম্বর: " + targetPhone;
            sendTelegramMessage(message, "উইথড্র রিকোয়েস্ট পাঠানো হয়েছে!");
            event.target.reset();
            switchProfileSubTab('main');
        }

        function applyPaidTournament(event) {
            event.preventDefault();
            let roomFee = parseFloat(document.getElementById("paid-room-select").value);
            let playerName = document.getElementById("paid-player-name").value.trim();
            let playerId = document.getElementById("paid-player-id").value.trim();
            let selectedCountry = document.getElementById("paid-country-select").value;
            let user = JSON.parse(localStorage.getItem("registeredUser")) || {};

            if ((user.balance || 0) < roomFee) { alert("⚠️ পর্যাপ্ত ব্যালেন্স নেই!"); return; }

            user.balance -= roomFee;
            user.paidCount = (user.paidCount || 0) + 1;
            localStorage.setItem("registeredUser", JSON.stringify(user));

            if (user.phone) db.collection("users").doc(user.phone).update({ balance: user.balance, paidCount: user.paidCount });
            loadUserData();

            let matchData = {
                type: "Paid Tournament",
                fee: roomFee,
                playerName: playerName,
                playerId: playerId,
                country: selectedCountry,
                userPhone: user.phone,
                timestamp: firebase.firestore.FieldValue.serverTimestamp()
            };

            db.collection("matches").add(matchData).then(() => {
                alert("✅ পেইড টুর্নামেন্টে সফল আবেদন!");
                loadPaidTournamentsList();
                event.target.reset();
            });
        }

        function applyPvpMatch(event) {
            event.preventDefault();
            let betAmount = parseFloat(document.getElementById("pvp-bet-amount").value);
            let pvpName = document.getElementById("pvp-ingame-name").value.trim();
            let roomCode = document.getElementById("pvp-room-code").value.trim();
            let user = JSON.parse(localStorage.getItem("registeredUser")) || {};

            if ((user.balance || 0) < betAmount) { alert("⚠️ পর্যাপ্ত ব্যালেন্স নেই!"); return; }

            user.balance -= betAmount;
            user.pvpCount = (user.pvpCount || 0) + 1;
            localStorage.setItem("registeredUser", JSON.stringify(user));

            if (user.phone) db.collection("users").doc(user.phone).update({ balance: user.balance, pvpCount: user.pvpCount });
            loadUserData();

            db.collection("matches").add({
                type: "PvP Match",
                bet: betAmount,
                creatorName: pvpName,
                creatorPhone: user.phone,
                roomCode: roomCode,
                status: "Open",
                timestamp: firebase.firestore.FieldValue.serverTimestamp()
            }).then(() => {
                loadPvpMatchesList();
                event.target.reset();
                alert("সফলভাবে PvP রুম তৈরি হয়েছে!");
            });
        }

        function loadMatchesList() {
            loadPaidTournamentsList();
            loadPvpMatchesList();
        }

        function loadPaidTournamentsList() {
            let container = document.getElementById("paid-matches-container");
            if (!container) return;
            let selectedFee = parseFloat(document.getElementById("paid-room-select").value);

            db.collection("matches").where("type", "==", "Paid Tournament").where("fee", "==", selectedFee).get().then((snapshot) => {
                container.innerHTML = "";
                let takenCountries = [];

                snapshot.forEach(doc => {
                    let d = doc.data();
                    if (d.country) takenCountries.push(d.country);
                });
                populateCountriesDropdown(takenCountries);

                snapshot.forEach(doc => {
                    let d = doc.data();
                    let div = document.createElement("div");
                    div.style.cssText = "background:#1e293b; padding:10px; border-radius:8px; margin-bottom:6px; font-size:12px;";
                    div.innerHTML = `<b>👤 ${d.playerName}</b> | 🌍 ${d.country}`;
                    container.appendChild(div);
                });
            });
        }

        function loadPvpMatchesList() {
            let container = document.getElementById("pvp-matches-container");
            if (!container) return;

            db.collection("matches").where("type", "==", "PvP Match").get().then((snapshot) => {
                container.innerHTML = "";
                snapshot.forEach((doc) => {
                    let m = doc.data(), id = doc.id;
                    let div = document.createElement("div");
                    div.className = "match-card";
                    div.innerHTML = `<b style='color:#25d366;'>🎮 PvP (${m.bet} Tk)</b><br>স্ট্যাটাস: ${m.status}<br><button onclick='joinPvpMatch("${id}", ${m.bet}, "${m.creatorPhone}")' style='margin-top:6px; background:#25d366; color:#fff; border:none; padding:6px; border-radius:4px; cursor:pointer;'>জয়েন করুন</button>`;
                    container.appendChild(div);
                });
            });
        }
    </script>
</head>
<body>

    <div class='auth-wrapper' id='auth-wrapper-box' style='display: none;'>
        <div class='auth-container' id='signup-box'>
            <h2>eFootball <span>Matchday</span></h2>
            <form onsubmit='handleSignup(event)'>
                <div class='form-group'><label>আপনার নাম</label><input id='su-name' required type='text'/></div>
                <div class='form-group'><label>মোবাইল নম্বর</label><input id='su-phone' required type='tel'/></div>
                <div class='form-group'><label>পাসওয়ার্ড</label><input id='su-pass' required type='password'/></div>
                <button class='btn-submit' type='submit'>সাইন আপ করুন</button>
            </form>
            <div class='switch-text'>অ্যাকাউন্ট আছে? <span onclick='switchToLogin()'>লগইন করুন</span></div>
        </div>

        <div class='auth-container' id='login-box' style='display: none;'>
            <h2>ইউজার <span>লগইন</span></h2>
            <form onsubmit='handleLogin(event)'>
                <div class='form-group'><label>মোবাইল নম্বর</label><input id='li-phone' required type='tel'/></div>
                <div class='form-group'><label>পাসওয়ার্ড</label><input id='li-pass' required type='password'/></div>
                <button class='btn-submit' type='submit'>লগইন করুন</button>
            </form>
            <div class='switch-text'>অ্যাকাউন্ট নেই? <span onclick='switchToSignup()'>সাইন আপ করুন</span></div>
        </div>
    </div>

    <div id='main-site-content' style='display: none;'>
        <nav>
            <div>
                <div class='logo' onclick='switchSection("home")'>⚽ eFootball</div>
            </div>
            <div class='nav-right'>
                <div class='balance-badge' id='user-balance-badge'>0 Tk</div>
                <div class='profile-icon' id='profile-avatar-letter' onclick='switchSection("profile")'>S</div>
            </div>
        </nav>

        <div class='main-container'>
            <div class='notice-board' id='dynamic-admin-notice' style='display:none;'></div>

            <div class='box' id='home-section'>
                <div class='tabs' style='margin-bottom: 15px;'>
                    <button class='tab-btn active' id='main-tab-paid' onclick='switchMainTab("paid")'>🏆 Paid</button>
                    <button class='tab-btn' id='main-tab-slot' onclick='switchMainTab("slot")'>🎮 PvP</button>
                    <button class='tab-btn' id='main-tab-free' onclick='switchMainTab("free")'>🎁 Free (7 Days)</button>
                    <button class='tab-btn' id='main-tab-market' onclick='switchMainTab("market")'>🛒 ID Sell</button>
                    <button class='tab-btn' id='main-tab-coin' onclick='switchMainTab("coin")'>🪙 Coin Buy</button>
                </div>

                <!-- Paid Tournament Tab -->
                <div id='tab-content-paid'>
                    <div class='tabs' id='paid-room-tabs-container'>
                        <button class='tab-btn' onclick='switchPaidRoomTab("10", this)'>10 Tk</button>
                        <button class='tab-btn' onclick='switchPaidRoomTab("50", this)'>50 Tk</button>
                        <button class='tab-btn active' onclick='switchPaidRoomTab("100", this)'>100 Tk</button>
                    </div>
                    <div style='background: #1e293b; padding: 15px; border-radius: 12px;'>
                        <form onsubmit='applyPaidTournament(event)'>
                            <input id='paid-room-select' type='hidden' value='100'>
                            <div class='form-group'><label>নাম</label><input id='paid-player-name' required type='text'/></div>
                            <div class='form-group'><label>আইডি</label><input id='paid-player-id' required type='text'/></div>
                            <div class='form-group'><label>দেশ</label><select id='paid-country-select' required><option value=''>দেশ বাছুন</option></select></div>
                            <button class='btn-submit' type='submit'>আবেদন করুন</button>
                        </form>
                    </div>
                    <div id='paid-matches-container' style='margin-top:15px;'></div>
                </div>

                <!-- PvP Tab -->
                <div id='tab-content-slot' style='display: none;'>
                    <div style='background: #1e293b; padding: 15px; border-radius: 12px;'>
                        <form onsubmit='applyPvpMatch(event)'>
                            <div class='form-group'><label>বাজি (Tk)</label><input id='pvp-bet-amount' required type='number'/></div>
                            <div class='form-group'><label>নাম</label><input id='pvp-ingame-name' required type='text'/></div>
                            <div class='form-group'><label>রুম কোড</label><input id='pvp-room-code' required type='text'/></div>
                            <button class='btn-submit' type='submit'>রুম তৈরি</button>
                        </form>
                    </div>
                    <div id='pvp-matches-container' style='margin-top:15px;'></div>
                </div>

                <!-- Free Tournament Tab -->
                <div id='tab-content-free' style='display: none;'>
                    <div style='background: #1e293b; padding: 15px; border-radius: 12px; border: 1px solid #334155;'>
                        <h4 style='color: #facc15; margin-bottom: 8px; font-size: 14px;'>🎁 ফ্রি টুর্নামেন্ট (প্রতি ৭ দিন পর পর ওপেন, আনলিমিটেড স্লট)</h4>
                        <p style='font-size: 12px; color: #94a3b8; margin-bottom: 10px;'>১০টি অ্যাড দেখুন ও ৩টি শেয়ার করুন। প্রতি টুর্নামেন্টে মাত্র ১ বার আবেদন করা যাবে।</p>
                        
                        <div style='background: #0f172a; padding: 10px; border-radius: 8px; margin-bottom: 15px; font-size: 12px;'>
                            <div style='display:flex; justify-content:space-between; margin-bottom:8px;'>
                                <span>অ্যাড দেখা: <b id='free-ad-status' style='color:#ef4444;'>0/10</b></span>
                                <button type='button' onclick='watchFreeAd()' style='background:#3b82f6; color:#fff; border:none; padding:4px 10px; border-radius:4px; cursor:pointer;'>অ্যাড দেখুন</button>
                            </div>
                            <div style='display:flex; justify-content:space-between;'>
                                <span>শেয়ার: <b id='free-share-status' style='color:#ef4444;'>0/3</b></span>
                                <button type='button' onclick='shareFreeSite("whatsapp")' style='background:#25d366; color:#fff; border:none; padding:4px 10px; border-radius:4px; cursor:pointer;'>শেয়ার করুন</button>
                            </div>
                        </div>

                        <div id='free-form-fields' style='display: none;'>
                            <form onsubmit='applyFreeTournament(event)'>
                                <div class='form-group'><label>ইন-গেম নাম (In-game Name)</label><input id='free-ingame-name' required type='text'/></div>
                                <div class='form-group'><label>ইন-গেম আইডি (In-game ID)</label><input id='free-ingame-id' required type='text'/></div>
                                <div class='form-group'><label>যে দেশ নিয়ে খেলবেন</label><select id='free-country-select' required><option value=''>দেশ নির্বাচন করুন</option></select></div>
                                <div class='form-group'><label>WhatsApp নম্বর</label><input id='free-whatsapp' placeholder='হোয়াটসঅ্যাপ নম্বর' required type='tel'/></div>
                                <div class='form-group'>
                                    <label>স্ক্রিনশট আপলোড</label>
                                    <div style='display: flex; gap: 8px;'>
                                        <input id='free-img-url' placeholder='ছবির লিংক' required type='text' style='width:100%;'/>
                                        <input accept='image/*' id='free-file-input' onchange='uploadFreeScreenshot()' style='display: none;' type='file'/>
                                        <button onclick='document.getElementById("free-file-input").click()' style='background: #334155; color: #fff; border: none; padding: 0 12px; border-radius: 6px; cursor: pointer;' type='button'>📷</button>
                                    </div>
                                </div>
                                <button class='btn-submit' id='free-submit-btn' type='submit'>আবেদন করুন (মাত্র ১ বার)</button>
                            </form>
                        </div>
                    </div>
                    <div id='free-matches-container' style='margin-top:15px;'></div>
                </div>

                <!-- ID Sell Tab -->
                <div id='tab-content-market' style='display: none;'>
                    <div style='background: #1e293b; padding: 15px; border-radius: 12px; margin-bottom: 15px;'>
                        <form onsubmit='handleSellPostSubmit(event)'>
                            <div class='form-group'><label>বিবরণ</label><input id='market-title' required type='text'/></div>
                            <div class='form-group'><label>দাম (Tk)</label><input id='market-price' required type='number'/></div>
                            <div class='form-group'><label>কোনামি জিমেইল</label><input id='market-konami-gmail' required type='email'/></div>
                            <div class='form-group'><label>পাসওয়ার্ড</label><input id='market-konami-pass' required type='text'/></div>
                            <div class='form-group'>
                                <label>স্ক্রিনশট</label>
                                <div style='display: flex; gap: 8px;'>
                                    <input id='market-img-url' required type='text' style='width:100%;'/>
                                    <input accept='image/*' id='market-file-input' onchange='uploadMarketScreenshot()' style='display: none;' type='file'/>
                                    <button onclick='document.getElementById("market-file-input").click()' style='background: #334155; color: #fff; border: none; padding: 0 12px; border-radius: 6px; cursor: pointer;' type='button'>📷</button>
                                </div>
                            </div>
                            <button class='btn-submit' type='submit'>পাবলিশ করুন</button>
                        </form>
                    </div>
                    <div id='marketplace-items-container'></div>
                </div>

                <!-- Coin Buy Tab (Added Konami Gmail & Password) -->
                <div id='tab-content-coin' style='display: none;'>
                    <div style='background: #1e293b; padding: 15px; border-radius: 12px;'>
                        <form onsubmit='handleCoinBuy(event)'>
                            <div class='form-group'><label>কয়েন পরিমাণ</label><input id='coin-amount-input' type='number' min='10' step='10' value='100' oninput='calculateCoinPrice()' required /></div>
                            <p style='margin-bottom:10px; font-size:13px;'>মূল্য: <b id='coin-price-display' style='color:#25d366;'>90.00 Tk</b></p>
                            <div class='form-group'><label>গেম আইডি</label><input id='coin-game-id' required type='text'/></div>
                            <div class='form-group'><label>কোনামি জিমেইল (Konami Gmail)</label><input id='coin-konami-gmail' required type='email' placeholder='আপনার কোনামি জিমেইল দিন'/></div>
                            <div class='form-group'><label>পাসওয়ার্ড (Konami Password)</label><input id='coin-konami-pass' required type='text' placeholder='আপনার কোনামি পাসওয়ার্ড দিন'/></div>
                            <button class='btn-submit' type='submit'>কয়েন অর্ডার</button>
                        </form>
                    </div>
                </div>
            </div>

            <!-- Profile Section -->
            <div class='box' id='profile-section' style='display: none;'>
                <h3 style='color: #25d366; margin-bottom: 15px;'>👤 প্রোফাইল</h3>
                <div class='profile-card' style='margin-bottom: 15px;'>
                    <div class='profile-info-row'><span>নাম:</span><span id='prof-name'>-</span></div>
                    <div class='profile-info-row'><span>নম্বর:</span><span id='prof-phone'>-</span></div>
                    <div class='profile-info-row'><span>ব্যালেন্স:</span><span id='prof-balance'>0 Tk</span></div>
                    <div class='profile-info-row'><span>PvP ম্যাচ:</span><span id='prof-pvp'>0 টি</span></div>
                    <div class='profile-info-row'><span>পেইড টুর্নামেন্ট:</span><span id='prof-paid'>0 টি</span></div>
                </div>
                <div id='profile-main-view'>
                    <div style='display: flex; gap: 10px; margin-bottom: 20px;'>
                        <button onclick='switchProfileSubTab("deposit")' style='flex: 1; background: #25d366; color: #fff; border: none; padding: 10px; border-radius: 8px; cursor: pointer;'>ডিপোজিট</button>
                        <button onclick='switchProfileSubTab("withdraw")' style='flex: 1; background: #ef4444; color: #fff; border: none; padding: 10px; border-radius: 8px; cursor: pointer;'>উইথড্র</button>
                    </div>
                </div>
                <div id='profile-deposit-view' style='display: none; background: #1e293b; padding: 15px; border-radius: 12px;'>
                    <form onsubmit='handleDeposit(event)'>
                        <div class='form-group'><label>মাধ্যম</label><select id='dep-method'><option value='Bkash'>Bkash</option><option value='Nagad'>Nagad</option></select></div>
                        <div class='form-group'><label>টাকা</label><input id='dep-amount' required type='number'/></div>
                        <div class='form-group'><label>প্রেরক নম্বর</label><input id='dep-sender-no' required type='tel'/></div>
                        <div class='form-group'><label>TrxID</label><input id='dep-trxid' required type='text'/></div>
                        <button class='btn-submit' type='submit'>ডিপোজিট পাঠান</button>
                    </form>
                </div>
                <div id='profile-withdraw-view' style='display: none; background: #1e293b; padding: 15px; border-radius: 12px;'>
                    <form onsubmit='handleWithdraw(event)'>
                        <div class='form-group'><label>মাধ্যম</label><select id='wd-method'><option value='Bkash'>Bkash</option><option value='Nagad'>Nagad</option></select></div>
                        <div class='form-group'><label>টাকা</label><input id='wd-amount' required type='number'/></div>
                        <div class='form-group'><label>নম্বর</label><input id='wd-target-phone' required type='tel'/></div>
                        <button class='btn-submit' type='submit'>উইথড্র পাঠান</button>
                    </form>
                </div>
                <button onclick='handleLogout()' style='background: #ef4444; color: #fff; border: none; padding: 10px; border-radius: 8px; width: 100%; cursor: pointer;'>লগআউট</button>
            </div>
        </div>

        <div class='bottom-nav'>
            <div class='bottom-nav-item active' onclick='switchSection("home")'>🏠 হোম</div>
            <div class='bottom-nav-item' onclick='switchSection("profile")'>👤 প্রোফাইল</div>
        </div>
    </div>
</body>
</html>
