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

        .tabs { display: flex; gap: 10px; margin-bottom: 15px; overflow-x: auto; padding-bottom: 5px; }
        .tab-btn { background: #374151; color: #fff; border: none; padding: 8px 16px; border-radius: 6px; cursor: pointer; font-weight: bold; white-space: nowrap; }
        .tab-btn.active { background: #25d366; color: #fff; }

        .bottom-nav { position: fixed; bottom: 0; left: 0; width: 100%; background: #1f2937; display: flex; justify-content: space-around; padding: 10px 0; border-top: 1px solid #374151; z-index: 1000; }
        .bottom-nav-item { text-align: center; color: #94a3b8; font-size: 12px; font-weight: bold; cursor: pointer; flex: 1; }
        .bottom-nav-item.active { color: #25d366; }

        .profile-card { background: #1e293b; padding: 20px; border-radius: 12px; border: 1px solid #334155; }
        .profile-info-row { display: flex; justify-content: space-between; padding: 10px 0; border-bottom: 1px solid #334155; font-size: 14px; }
        .profile-info-row span:last-child { font-weight: bold; color: #25d366; }

        .slots-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(260px, 1fr)); gap: 10px; margin-top: 10px; }
        .slot-item { background: #1e293b; border: 1px solid #334155; padding: 12px 15px; border-radius: 8px; display: flex; justify-content: space-between; align-items: center; font-size: 13px; }
        .slot-item.booked { border-color: #25d366; background: rgba(37, 211, 102, 0.05); }
        .slot-no { font-weight: bold; color: #facc15; }

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
        });

        function populateCountriesDropdown(takenCountries = []) {
            let select = document.getElementById("paid-country-select");
            if (!select) return;
            select.innerHTML = "<option value=''>দেশ নির্বাচন করুন</option>";

            countriesList.forEach(country => {
                if (!takenCountries.includes(country)) {
                    let opt = document.createElement("option");
                    opt.value = country;
                    opt.innerText = country;
                    select.appendChild(opt);
                }
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
            
            let profAvatarImg = document.getElementById("prof-avatar-img");
            let profAvatarLetter = document.getElementById("prof-avatar-letter");
            let profImgInput = document.getElementById("prof-img-url-input");

            if (user.profileImg) {
                navAvatar.style.backgroundImage = "url('" + user.profileImg + "')";
                navAvatar.innerText = "";

                if(profAvatarImg && profAvatarLetter) {
                    profAvatarImg.src = user.profileImg;
                    profAvatarImg.style.display = "block";
                    profAvatarLetter.style.display = "none";
                }
                if(profImgInput) profImgInput.value = user.profileImg;
            } else {
                navAvatar.style.backgroundImage = "none";
                navAvatar.innerText = avatarLetter;

                if(profAvatarImg && profAvatarLetter) {
                    profAvatarImg.style.display = "none";
                    profAvatarLetter.style.display = "block";
                    profAvatarLetter.innerText = avatarLetter;
                }
            }

            document.getElementById("prof-name").innerText = user.name || "-";
            document.getElementById("prof-phone").innerText = user.phone || "-";
            document.getElementById("prof-balance").innerText = (user.balance || 0) + " Tk";
            document.getElementById("prof-pvp").innerText = (user.pvpCount || 0) + " টি";
            document.getElementById("prof-paid").innerText = (user.paidCount || 0) + " টি";
        }

        function updateProfilePicture() {
            let imgUrl = document.getElementById("prof-img-url-input").value.trim();
            if (!imgUrl) {
                alert("⚠️ দয়া করে সঠিক ছবির ইমেজ লিংক দিন।");
                return;
            }

            let user = JSON.parse(localStorage.getItem("registeredUser")) || {};
            user.profileImg = imgUrl;
            localStorage.setItem("registeredUser", JSON.stringify(user));

            if (user.phone) {
                db.collection("users").doc(user.phone).update({
                    profileImg: imgUrl
                }).then(() => {
                    alert("✅ সফলভাবে প্রোফাইল ছবি আপডেট হয়েছে!");
                    loadUserData();
                }).catch(err => { alert("ত্রুটি: " + err.message); });
            }
        }

        function uploadProfileFile() {
            let fileInput = document.getElementById("prof-file-input");
            if (fileInput.files.length === 0) return;
            
            let file = fileInput.files[0];
            let formData = new FormData();
            formData.append("image", file);
            let inputField = document.getElementById("prof-img-url-input");
            inputField.value = "আপলোড হচ্ছে...";

            fetch("https://api.imgbb.com/1/upload?key=" + IMGBB_API_KEY, {
                method: "POST",
                body: formData
            })
            .then(res => res.json())
            .then(data => {
                if (data.success) {
                    inputField.value = data.data.url;
                    updateProfilePicture();
                } else {
                    alert("আপলোড ব্যর্থ হয়েছে।");
                    inputField.value = "";
                }
            })
            .catch(() => {
                alert("ইন্টারনেট সমস্যা।");
                inputField.value = "";
            });
        }

        function uploadMarketScreenshot() {
            let fileInput = document.getElementById("market-file-input");
            let urlInput = document.getElementById("market-img-url");
            if (fileInput.files.length === 0) return;

            let file = fileInput.files[0];
            let formData = new FormData();
            formData.append("image", file);
            urlInput.value = "আপলোড হচ্ছে...";

            fetch("https://api.imgbb.com/1/upload?key=" + IMGBB_API_KEY, {
                method: "POST",
                body: formData
            })
            .then(res => res.json())
            .then(data => {
                if (data.success) {
                    urlInput.value = data.data.url;
                } else {
                    alert("ছবি আপলোড ব্যর্থ হয়েছে।");
                    urlInput.value = "";
                }
            })
            .catch(() => {
                alert("ইন্টারনেট সমস্যা।");
                urlInput.value = "";
            });
        }

        function handleSellPostSubmit(event) {
            event.preventDefault();
            let title = document.getElementById("market-title").value.trim();
            let price = parseFloat(document.getElementById("market-price").value);
            let imgUrl = document.getElementById("market-img-url").value.trim();
            let user = JSON.parse(localStorage.getItem("registeredUser")) || {};

            if (!title || isNaN(price) || !imgUrl) {
                alert("⚠️ সব তথ্য এবং স্ক্রিনশট লিংক সঠিকভাবে দিন।");
                return;
            }

            let postData = {
                itemType: "eFootball ID Sell",
                title: title,
                price: price,
                imgUrl: imgUrl,
                sellerName: user.name,
                sellerPhone: user.phone,
                sellerImg: user.profileImg || "",
                timestamp: firebase.firestore.FieldValue.serverTimestamp()
            };

            db.collection("marketplace").add(postData).then(() => {
                alert("✅ সফলভাবে আপনার আইডি বিক্রির বিজ্ঞাপন পোস্ট করা হয়েছে এবং হোমে পাবলিশ হয়েছে!");
                event.target.reset();
                document.getElementById("market-img-url").value = "";
                loadMarketplaceList();
            }).catch(err => alert("ত্রুটি: " + err.message));
        }

        function loadMarketplaceList() {
            let container = document.getElementById("marketplace-items-container");
            if (!container) return;

            db.collection("marketplace").orderBy("timestamp", "desc").get().then(snapshot => {
                container.innerHTML = "";
                if (snapshot.empty) {
                    container.innerHTML = "<p style='font-size:13px; color:#94a3b8;'>বর্তমানে কোনো আইডি বিক্রির পোস্ট নেই।</p>";
                    return;
                }

                snapshot.forEach(doc => {
                    let d = doc.data();
                    let div = document.createElement("div");
                    div.style.cssText = "background:#1e293b; border:1px solid #334155; padding:12px; border-radius:8px; margin-bottom:12px;";
                    
                    let avatarInitial = (d.sellerName || "U").charAt(0).toUpperCase();
                    let avatarStyle = d.sellerImg ? "background-image:url('" + d.sellerImg + "'); background-size:cover;" : "background:#25d366;";

                    div.innerHTML = `
                        <div style='display:flex; justify-content:space-between; align-items:center; margin-bottom:8px;'>
                            <b style='color:#facc15; font-size:14px;'>🎮 ${d.itemType}</b>
                            <span style='background:#25d366; color:#fff; padding:2px 8px; border-radius:4px; font-size:12px; font-weight:bold;'>দাম: ${d.price} Tk</span>
                        </div>
                        <p style='font-size:13px; color:#cbd5e1; margin-bottom:8px;'>বিবরণ: ${d.title}</p>
                        
                        <div style='display:flex; align-items:center; gap:8px; margin-bottom:8px; background:#0f172a; padding:6px 10px; border-radius:6px; cursor:pointer;' onclick='viewSellerProfile("${d.sellerPhone}", "${d.sellerName}", "${d.sellerImg || ""}")'>
                            <div style='width:24px; height:24px; border-radius:50%; display:flex; align-items:center; justify-content:center; color:#fff; font-size:11px; font-weight:bold; ${avatarStyle}'>${d.sellerImg ? "" : avatarInitial}</div>
                            <span style='font-size:12px; color:#38bdf8; text-decoration:underline;'>বিক্রেতা: ${d.sellerName} (প্রোফাইল দেখুন)</span>
                        </div>

                        <div style='margin-bottom:8px;'>
                            <a href='${d.imgUrl}' target='_blank'>
                                <img src='${d.imgUrl}' style='width:100%; max-height:180px; object-fit:cover; border-radius:6px; border:1px solid #334155;'/>
                            </a>
                        </div>
                        <a href='https://api.whatsapp.com/send?phone=+88${d.sellerPhone}&text=I%20want%20to%20buy%20your%20eFootball%20ID:%20${encodeURIComponent(d.title)}%20for%20${d.price}Tk' target='_blank' style='display:block; text-align:center; background:#25d366; color:#fff; padding:8px; border-radius:6px; font-size:12px; font-weight:bold; text-decoration:none;'>
                            💬 বিক্রেতার সাথে যোগাযোগ করুন (WhatsApp)
                        </a>
                    `;
                    container.appendChild(div);
                });
            }).catch(() => {});
        }

        function viewSellerProfile(phone, name, img) {
            db.collection("users").doc(phone).get().then(doc => {
                let pvp = 0, paid = 0;
                if(doc.exists) {
                    let data = doc.data();
                    pvp = data.pvpCount || 0;
                    paid = data.paidCount || 0;
                }
                
                let modalHtml = `
                    <div id='seller-modal' style='position:fixed; top:0; left:0; width:100%; height:100%; background:rgba(0,0,0,0.8); z-index:9999; display:flex; justify-content:center; align-items:center; padding:15px;'>
                        <div style='background:#1f2937; padding:20px; border-radius:12px; width:100%; max-width:350px; border:1px solid #374151; text-align:center; position:relative;'>
                            <button onclick='document.getElementById("seller-modal").remove()' style='position:absolute; top:10px; right:10px; background:#ef4444; color:#fff; border:none; width:24px; height:24px; border-radius:50%; cursor:pointer;'>✕</button>
                            <div style='width:60px; height:60px; border-radius:50%; margin:0 auto 10px auto; display:flex; align-items:center; justify-content:center; font-size:24px; color:#fff; font-weight:bold; ${img ? "background-image:url("+img+"); background-size:cover;" : "background:#25d366;"}'>${img ? "" : name.charAt(0)}</div>
                            <h3 style='color:#fff; font-size:16px; margin-bottom:5px;'>${name}</h3>
                            <p style='color:#94a3b8; font-size:12px; margin-bottom:15px;'>নম্বর: ${phone}</p>
                            <div style='text-align:left; background:#111827; padding:10px; border-radius:8px; font-size:13px;'>
                                <div style='display:flex; justify-content:space-between; margin-bottom:6px;'><span>PvP ম্যাচ খেলা:</span><b>${pvp} টি</b></div>
                                <div style='display:flex; justify-content:space-between;'><span>Paid টুর্নামেন্ট:</span><b>${paid} টি</b></div>
                            </div>
                        </div>
                    </div>
                `;
                document.body.insertAdjacentHTML('beforeend', modalHtml);
            });
        }

        function buyCoinPack(coinAmount, price) {
            let user = JSON.parse(localStorage.getItem("registeredUser")) || {};
            let konamiId = prompt("আপনার eFootball আইডি বা কোনামি আইডি (Email) লিখুন যেখানে কয়েন নিতে চান:");
            if (!konamiId) return;

            let message = "🪙 কয়েন অর্ডার রিকোয়েস্ট!\n👤 ক্রেতা: " + user.name + " (" + user.phone + ")\n📦 প্যাক: " + coinAmount + " Coins\n💵 দাম: " + price + " Tk\n🔑 কোনামি আইডি: " + konamiId;
            sendTelegramMessage(message, "সফলভাবে কয়েন অর্ডার সাবমিট হয়েছে! এডমিন আপনার আইডিতে কয়েন টপ-আপ করে দেবেন।");
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
            let submitBtn = document.getElementById("su-btn");
            let name = document.getElementById("su-name").value.trim();
            let phone = document.getElementById("su-phone").value.trim();
            let pass = document.getElementById("su-pass").value.trim();

            let phoneRegex = /^01[3-9]\d{8}$/;
            if (!phoneRegex.test(phone)) {
                alert("⚠️ সঠিক বাংলাদেশি মোবাইল নম্বর দিন (যেমন: 01XXXXXXXXX)");
                return;
            }

            submitBtn.disabled = true;
            submitBtn.innerText = "যাচাই করা হচ্ছে...";

            db.collection("users").doc(phone).get().then((doc) => {
                if (doc.exists) {
                    alert("⚠️ এই মোবাইল নম্বর দিয়ে ইতিমধ্যে অ্যাকাউন্ট রয়েছে!");
                    submitBtn.disabled = false;
                    submitBtn.innerText = "সাইন আপ করুন";
                    switchToLogin();
                } else {
                    let userData = { 
                        name: name, phone: phone, pass: pass, 
                        balance: 0, pvpCount: 0, paidCount: 0, profileImg: "" 
                    };
                    db.collection("users").doc(phone).set(userData).then(() => {
                        localStorage.setItem("registeredUser", JSON.stringify(userData));
                        localStorage.setItem("isLoggedIn", "true");
                        alert("সাইন আপ সফল হয়েছে!");
                        checkAuth();
                    }).catch((error) => {
                        alert("ত্রুটি: " + error.message);
                        submitBtn.disabled = false;
                        submitBtn.innerText = "সাইন আপ করুন";
                    });
                }
            });
        }

        function handleLogin(event) {
            event.preventDefault();
            let phone = document.getElementById("li-phone").value.trim();
            let pass = document.getElementById("li-pass").value.trim();

            db.collection("users").doc(phone).get().then((doc) => {
                if (doc.exists) {
                    let data = doc.data();
                    if (data.pass === pass) {
                        let userData = {
                            name: data.name, phone: data.phone, pass: data.pass,
                            balance: data.balance || 0,
                            pvpCount: data.pvpCount || 0, paidCount: data.paidCount || 0,
                            profileImg: data.profileImg || ""
                        };
                        localStorage.setItem("registeredUser", JSON.stringify(userData));
                        localStorage.setItem("isLoggedIn", "true");
                        alert("লগইন সফল হয়েছে!");
                        checkAuth();
                    } else {
                        alert("ভুল পাসওয়ার্ড!");
                    }
                } else {
                    alert("এই নম্বরে কোনো অ্যাকাউন্ট নেই!");
                }
            });
        }

        function handleLogout() {
            localStorage.removeItem("isLoggedIn");
            localStorage.removeItem("registeredUser");
            alert("লগআউট করা হয়েছে!");
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

            if (isNaN(amount) || amount <= 0) {
                alert("⚠️ সঠিক ডিপোজিট পরিমাণ লিখুন।");
                return;
            }

            let message = "💰 নতুন ডিপোজিট রিকোয়েস্ট!\n👤 " + user.name + " (" + user.phone + ")\n💳 " + method + " - " + amount + " Tk\n📱 প্রেরক: " + senderNo + "\n🔑 TrxID: " + trxId;
            sendTelegramMessage(message, "ডিপোজিট রিকোয়েস্ট সফলভাবে অ্যাডমিনের কাছে পাঠানো হয়েছে!");
            event.target.reset();
            switchProfileSubTab('main');
        }

        function handleWithdraw(event) {
            event.preventDefault();
            let method = document.getElementById("wd-method").value;
            let amount = parseFloat(document.getElementById("wd-amount").value);
            let targetPhone = document.getElementById("wd-target-phone").value.trim();
            let note = document.getElementById("wd-trxid").value.trim();
            let user = JSON.parse(localStorage.getItem("registeredUser")) || {};

            if (amount < 100) { alert("⚠️ সর্বনিম্ন ১০০ টাকা উইথড্র করতে হবে।"); return; }
            if (amount > (user.balance || 0)) { alert("⚠️ আপনার একাউন্টে পর্যাপ্ত টাকা ব্যালেন্স নেই।"); return; }

            user.balance -= amount;
            localStorage.setItem("registeredUser", JSON.stringify(user));

            if (user.phone) {
                db.collection("users").doc(user.phone).update({ balance: user.balance }).catch(() => {});
            }
            loadUserData();

            let message = "💳 নতুন টাকা উইথড্র রিকোয়েস্ট!\n👤 " + user.name + " (" + user.phone + ")\n💳 " + method + " - " + amount + " Tk\n📱 নম্বর: " + targetPhone + "\n🔑 নোট: " + note;
            sendTelegramMessage(message, "উইথড্র রিকোয়েস্ট সফলভাবে সাবমিট হয়েছে!");
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

            if (!playerName || !playerId || !selectedCountry) {
                alert("⚠️ সঠিক ইন-গেম নাম, আইডি এবং দেশ সিলেক্ট করুন!");
                return;
            }

            if ((user.balance || 0) < roomFee) {
                alert("⚠️ আপনার একাউন্টে পর্যাপ্ত টাকা ব্যালেন্স নেই!");
                return;
            }

            db.collection("matches").where("type", "==", "Paid Tournament").where("fee", "==", roomFee).get().then((snapshot) => {
                let validDocs = [];
                snapshot.forEach(doc => {
                    let d = doc.data();
                    if (d.playerName && d.country) {
                        validDocs.push({ id: doc.id, ...d });
                    }
                });

                if (validDocs.length >= 16) {
                    alert("⚠️ এই টুর্নামেন্টে ইতিমধ্যে সর্বোচ্চ ১৬ জন প্লেয়ার পূর্ণ হয়ে গেছে!");
                    return;
                }

                let takenCountries = [];
                let alreadyJoined = false;
                validDocs.forEach(d => {
                    takenCountries.push(d.country);
                    if(d.userPhone === user.phone) alreadyJoined = true;
                });

                if (alreadyJoined) {
                    alert("⚠️ আপনি এই টুর্নামেন্টে ইতিমধ্যে আবেদন করেছেন!");
                    return;
                }

                if (takenCountries.includes(selectedCountry)) {
                    alert("⚠️ এই দেশ ইতিমধ্যে অন্য কেউ সিলেক্ট করেছে!");
                    return;
                }

                user.balance -= roomFee;
                user.paidCount = (user.paidCount || 0) + 1;
                localStorage.setItem("registeredUser", JSON.stringify(user));

                if (user.phone) {
                    db.collection("users").doc(user.phone).update({ 
                        balance: user.balance, 
                        paidCount: user.paidCount
                    });
                }
                loadUserData();

                let matchData = {
                    type: "Paid Tournament",
                    fee: roomFee,
                    playerName: playerName,
                    playerId: playerId,
                    country: selectedCountry,
                    userPhone: user.phone,
                    status: "Open",
                    timestamp: firebase.firestore.FieldValue.serverTimestamp()
                };

                db.collection("matches").add(matchData).then(() => {
                    let totalPool = roomFee * 16;
                    let message = "🚨 নতুন পেইড টুর্নামেন্ট আবেদন (" + roomFee + " Tk)!\n👤 " + user.name + " (" + user.phone + ")\n🎮 প্লেয়ার: " + playerName + " (ID: " + playerId + ")\n🌍 দেশ: " + selectedCountry + "\n💰 প্রাইজ পুল: " + totalPool + " Tk";
                    sendTelegramMessage(message, "সফলভাবে পেইড টুর্নামেন্টে আবেদন হয়েছে!");
                    loadPaidTournamentsList();
                    event.target.reset();
                });
            });
        }

        function applyPvpMatch(event) {
            event.preventDefault();
            let matchType = document.getElementById("pvp-type").value;
            let betInput = document.getElementById("pvp-bet-amount").value;
            let pvpName = document.getElementById("pvp-ingame-name").value.trim();
            let pvpId = document.getElementById("pvp-ingame-id").value.trim();
            let roomCode = document.getElementById("pvp-room-code").value.trim();
            let user = JSON.parse(localStorage.getItem("registeredUser")) || {};

            let betAmount = parseFloat(betInput);
            if (isNaN(betAmount) || betAmount <= 0) { alert("⚠️ ভুল এমাউন্ট!"); return; }

            if ((user.balance || 0) < betAmount) {
                alert("⚠️ পর্যাপ্ত ব্যালেন্স নেই!");
                return;
            }

            user.balance -= betAmount;
            user.pvpCount = (user.pvpCount || 0) + 1;
            localStorage.setItem("registeredUser", JSON.stringify(user));

            if (user.phone) {
                db.collection("users").doc(user.phone).update({ balance: user.balance, pvpCount: user.pvpCount });
            }
            loadUserData();

            let matchData = {
                type: "PvP Match",
                bet: betAmount,
                matchStyle: matchType,
                creatorName: pvpName,
                creatorId: pvpId,
                creatorPhone: user.phone,
                roomCode: roomCode,
                status: "Open",
                joinedCount: 1,
                timestamp: firebase.firestore.FieldValue.serverTimestamp()
            };

            db.collection("matches").add(matchData).then(() => {
                loadPvpMatchesList();
                event.target.reset();
                alert("সফলভাবে PvP রুম তৈরি হয়েছে!");
            });
        }

        function joinPvpMatch(matchId, betAmount, creatorPhone) {
            let user = JSON.parse(localStorage.getItem("registeredUser")) || {};
            if (user.phone === creatorPhone) { alert("নিজের রুমে জয়েন করা যাবে না!"); return; }
            if ((user.balance || 0) < betAmount) { alert("পর্যাপ্ত ব্যালেন্স নেই!"); return; }

            let joinerName = prompt("আপনার ইন-গেম নাম লিখুন:");
            let joinerId = prompt("আপনার ইন-গেম আইডি লিখুন:");
            if (!joinerName || !joinerId) return;

            user.balance -= betAmount;
            localStorage.setItem("registeredUser", JSON.stringify(user));
            if (user.phone) db.collection("users").doc(user.phone).update({ balance: user.balance });
            loadUserData();

            db.collection("matches").doc(matchId).update({
                joinerName: joinerName.trim(),
                joinerId: joinerId.trim(),
                joinerPhone: user.phone,
                status: "Running",
                joinedCount: 2
            }).then(() => {
                alert("সফলভাবে ম্যাচে জয়েন করেছেন!");
                loadPvpMatchesList();
            });
        }

        function cancelPvpRoom(matchId, betAmount, creatorPhone) {
            let user = JSON.parse(localStorage.getItem("registeredUser")) || {};
            if (user.phone !== creatorPhone || !confirm("রুমটি বাতিল করতে চান?")) return;

            user.balance = (user.balance || 0) + betAmount;
            localStorage.setItem("registeredUser", JSON.stringify(user));
            if (user.phone) db.collection("users").doc(user.phone).update({ balance: user.balance });
            loadUserData();

            db.collection("matches").doc(matchId).delete().then(() => loadPvpMatchesList());
        }

        function applyFreeTournament(event) {
            event.preventDefault();
            let user = JSON.parse(localStorage.getItem("registeredUser")) || {};
            if ((user.pvpCount || 0) < 10 || (user.paidCount || 0) < 10) {
                alert("⚠️ শর্ত পূরণ হয়নি! কমপক্ষে ১০টি PvP এবং ১০টি Paid ম্যাচ খেলতে হবে।");
                return;
            }
            let freeName = document.getElementById("free-p-name").value.trim();
            let freeId = document.getElementById("free-p-id").value.trim();
            if(!freeName || !freeId) return;

            let message = "🎁 ফ্রি টুর্নামেন্ট আবেদন!\n👤 " + user.name + " (" + user.phone + ")\n🎮 ইন-গেম: " + freeName + " (ID: " + freeId + ")";
            sendTelegramMessage(message, "সফলভাবে ফ্রি টুর্নামেন্টে আবেদন হয়েছে!");
            event.target.reset();
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
                let takenCountries = [], registeredPlayers = [];

                snapshot.forEach(doc => {
                    let d = doc.data();
                    if (d.playerName && d.country) {
                        takenCountries.push(d.country);
                        registeredPlayers.push(d);
                    }
                });

                populateCountriesDropdown(takenCountries);
                let winnerPool = selectedFee * 16 * 0.90;

                let headerInfo = document.createElement("div");
                headerInfo.style.cssText = "margin-bottom:12px; font-size:13px; color:#facc15; font-weight:bold; background:#1e293b; padding:10px; border-radius:8px; border:1px solid #334155;";
                headerInfo.innerHTML = "🏆 " + selectedFee + " Tk টুর্নামেন্ট | স্লট: <b style='color:#25d366;'>" + registeredPlayers.length + "/16</b> | প্রাইজপুল: <b style='color:#fff;'>" + winnerPool + " Tk</b>";
                container.appendChild(headerInfo);

                let gridDiv = document.createElement("div");
                gridDiv.className = "slots-grid";

                for (let i = 1; i <= 16; i++) {
                    let player = registeredPlayers[i - 1];
                    let slotBox = document.createElement("div");
                    if (player) {
                        slotBox.className = "slot-item booked";
                        slotBox.innerHTML = "<div><span class='slot-no'>#স্লট " + i + "</span><br/><b style='color:#fff;'>" + player.playerName + "</b></div><div style='text-align:right;'><span style='font-size:11px; color:#38bdf8;'>🌍 " + player.country + "</span></div>";
                    } else {
                        slotBox.className = "slot-item";
                        slotBox.innerHTML = "<div><span class='slot-no'>#স্লট " + i + "</span><br/><span style='color:#64748b;'>খালি আছে</span></div>";
                    }
                    gridDiv.appendChild(slotBox);
                }
                container.appendChild(gridDiv);
            });
        }

        function loadPvpMatchesList() {
            let container = document.getElementById("pvp-matches-container");
            if (!container) return;

            db.collection("matches").where("type", "==", "PvP Match").get().then((snapshot) => {
                container.innerHTML = "";
                let user = JSON.parse(localStorage.getItem("registeredUser")) || {};
                let hasMatch = false;

                snapshot.forEach((doc) => {
                    let m = doc.data(), id = doc.id;
                    if (!m.creatorName) return;
                    hasMatch = true;

                    let div = document.createElement("div");
                    div.className = "match-card";
                    let btnHtml = "";
                    if (m.status === "Open") {
                        if (user.phone !== m.creatorPhone) {
                            btnHtml = "<button onclick='joinPvpMatch(\"" + id + "\", " + m.bet + ", \"" + m.creatorPhone + "\")' style='margin-top:8px; background:#25d366; color:#fff; border:none; padding:8px; border-radius:6px; font-size:12px; font-weight:bold; width:100%; cursor:pointer;'>⚔️ " + m.bet + " টাকা দিয়ে জয়েন করুন</button>";
                        } else {
                            btnHtml = "<button onclick='cancelPvpRoom(\"" + id + "\", " + m.bet + ", \"" + m.creatorPhone + "\")' style='margin-top:8px; background:#ef4444; color:#fff; border:none; padding:6px; border-radius:6px; font-size:11px; width:100%; cursor:pointer;'>❌ রুম বাতিল করুন</button>";
                        }
                    } else {
                        btnHtml = "<p style='color:#38bdf8; font-size:12px; margin-top:6px;'>রানিং রুম কোড: <b>" + m.roomCode + "</b></p>";
                    }
                    div.innerHTML = "<b style='color:#25d366;'>🎮 PvP ম্যাচ (" + m.bet + " Tk)</b><br/>স্ট্যাটাস: <b>" + m.status + "</b>" + btnHtml;
                    container.appendChild(div);
                });
                if (!hasMatch) container.innerHTML = "<p style='font-size:13px; color:#94a3b8;'>কোনো PvP ম্যাচ নেই।</p>";
            });
        }
    </script>
</head>
<body>

    <div class='auth-wrapper' id='auth-wrapper-box' style='display: none;'>
        <div class='auth-container' id='signup-box'>
            <h2>eFootball <span>Matchday</span></h2>
            <form onsubmit='handleSignup(event)'>
                <div class='form-group'><label>আপনার নাম</label><input id='su-name' placeholder='নাম লিখুন' required type='text'/></div>
                <div class='form-group'><label>মোবাইল নম্বর</label><input id='su-phone' placeholder='০১xxxxxxxx' required type='tel'/></div>
                <div class='form-group'><label>পাসওয়ার্ড</label><input id='su-pass' placeholder='পাসওয়ার্ড দিন' required type='password'/></div>
                <button class='btn-submit' id='su-btn' type='submit'>সাইন আপ করুন</button>
            </form>
            <div class='switch-text'>অ্যাকাউন্ট আছে? <span onclick='switchToLogin()'>লগইন করুন</span></div>
        </div>

        <div class='auth-container' id='login-box' style='display: none;'>
            <h2>ইউজার <span>লগইন</span></h2>
            <form onsubmit='handleLogin(event)'>
                <div class='form-group'><label>মোবাইল নম্বর</label><input id='li-phone' placeholder='০১xxxxxxxx' required type='tel'/></div>
                <div class='form-group'><label>পাসওয়ার্ড</label><input id='li-pass' placeholder='পাসওয়ার্ড দিন' required type='password'/></div>
                <button class='btn-submit' type='submit'>লগইন করুন</button>
            </form>
            <div class='switch-text'>অ্যাকাউন্ট নেই? <span onclick='switchToSignup()'>সাইন আপ করুন</span></div>
        </div>
    </div>

    <div id='main-site-content' style='display: none;'>
        <nav>
            <div>
                <div class='logo' onclick='switchSection("home")'>⚽ eFootball</div>
                <span style='font-size: 11px; color: #94a3b8; font-weight: bold;'>Pro Tournaments</span>
            </div>
            <div class='nav-right'>
                <div class='balance-badge' id='user-balance-badge'>0 Tk</div>
                <div class='profile-icon' id='profile-avatar-letter' onclick='switchSection("profile")'>S</div>
            </div>
        </nav>

        <div class='main-container'>
            <div class='notice-board' id='dynamic-admin-notice' style='display:none;'></div>

            <!-- হোম সেকশন -->
            <div class='box' id='home-section'>
                <div class='tabs' style='margin-bottom: 15px;'>
                    <button class='tab-btn active' id='main-tab-paid' onclick='switchMainTab("paid")'>🏆 Paid Tournaments</button>
                    <button class='tab-btn' id='main-tab-slot' onclick='switchMainTab("slot")'>🎮 Instant PvP</button>
                    <button class='tab-btn' id='main-tab-free' onclick='switchMainTab("free")'>🎁 Free Tournament</button>
                    <button class='tab-btn' id='main-tab-market' onclick='switchMainTab("market")'>🛒 ID Sell</button>
                    <button class='tab-btn' id='main-tab-coin' onclick='switchMainTab("coin")'>🪙 Buy Coins</button>
                </div>

                <!-- 1. Paid Tournament Tab -->
                <div id='tab-content-paid'>
                    <div class='tabs' id='paid-room-tabs-container'>
                        <button class='tab-btn' onclick='switchPaidRoomTab("10", this)'>10 Tk</button>
                        <button class='tab-btn' onclick='switchPaidRoomTab("50", this)'>50 Tk</button>
                        <button class='tab-btn active' onclick='switchPaidRoomTab("100", this)'>100 Tk</button>
                    </div>
                    <div style='background: #1e293b; padding: 15px; border-radius: 12px; border: 1px solid #334155; margin-bottom: 15px;'>
                        <h4 style='color: #4ade80; margin-bottom: 8px; font-size: 14px;'>পেইড টুর্নামেন্ট আবেদন</h4>
                        <form onsubmit='applyPaidTournament(event)'>
                            <div class='form-group'>
                                <label style='font-size: 12px;'>রুম ফি</label>
                                <select id='paid-room-select' onchange='loadPaidTournamentsList()'>
                                    <option value='10'>10 Tk</option>
                                    <option value='50'>50 Tk</option>
                                    <option value='100' selected>100 Tk</option>
                                </select>
                            </div>
                            <div class='form-group'><label style='font-size: 11px;'>ইন-গেম নাম</label><input id='paid-player-name' placeholder='নাম লিখুন' required type='text'/></div>
                            <div class='form-group'><label style='font-size: 11px;'>ইন-গেম আইডি</label><input id='paid-player-id' placeholder='আইডি লিখুন' required type='text'/></div>
                            <div class='form-group'>
                                <label style='font-size: 11px;'>পছন্দের দেশ</label>
                                <select id='paid-country-select' required><option value=''>দেশ নির্বাচন করুন</option></select>
                            </div>
                            <button class='btn-submit' type='submit'>আবেদন করুন</button>
                        </form>
                    </div>
                    <div class='box' style='background: #171f2d; margin-top: 15px;'>
                        <h4 style='color: #4ade80; margin-bottom: 10px; font-size: 14px;'>🏆 স্লট লিস্ট</h4>
                        <div id='paid-matches-container'>লোড হচ্ছে...</div>
                    </div>
                </div>

                <!-- 2. PvP Tab -->
                <div id='tab-content-slot' style='display: none;'>
                    <div style='background: #1e293b; padding: 15px; border-radius: 12px; border: 1px solid #334155; margin-bottom: 15px;'>
                        <h4 style='color: #60a5fa; margin-bottom: 8px; font-size: 14px;'>PvP ম্যাচ রুম তৈরি</h4>
                        <form onsubmit='applyPvpMatch(event)'>
                            <div class='form-group'><label style='font-size: 12px;'>ধরন</label><select id='pvp-type'><option value='Friendly PvP'>Friendly PvP Match</option></select></div>
                            <div class='form-group'><label style='font-size: 11px;'>বাজির পরিমাণ (Tk)</label><input id='pvp-bet-amount' placeholder='যেমন: 50' required type='number'/></div>
                            <div class='form-group'><label style='font-size: 11px;'>ইন-গেম নাম</label><input id='pvp-ingame-name' placeholder='নাম' required type='text'/></div>
                            <div class='form-group'><label style='font-size: 11px;'>ইন-গেম আইডি</label><input id='pvp-ingame-id' placeholder='আইডি' required type='text'/></div>
                            <div class='form-group'><label style='font-size: 11px;'>রুম কোড</label><input id='pvp-room-code' placeholder='রুম কোড' required type='text'/></div>
                            <button class='btn-submit' type='submit'>রুম তৈরি করুন</button>
                        </form>
                    </div>
                    <div class='box' style='background: #171f2d; margin-top: 15px;'>
                        <h4 style='color: #25d366; margin-bottom: 10px; font-size: 14px;'>লাইভ PvP ম্যাচ লিস্ট</h4>
                        <div id='pvp-matches-container'>লোড হচ্ছে...</div>
                    </div>
                </div>

                <!-- 3. Free Tournament Tab -->
                <div id='tab-content-free' style='display: none;'>
                    <div style='background: #1e293b; padding: 15px; border-radius: 12px; border: 1px solid #334155;'>
                        <h4 style='color: #facc15; margin-bottom: 8px; font-size: 14px;'>🎁 ফ্রি টুর্নামেন্ট (শর্ত: ১০টি PvP ও ১০টি Paid ম্যাচ খেলা বাধ্যতামূলক)</h4>
                        <form onsubmit='applyFreeTournament(event)'>
                            <div class='form-group'><label style='font-size: 11px;'>ইন-গেম নাম</label><input id='free-p-name' placeholder='নাম' required type='text'/></div>
                            <div class='form-group'><label style='font-size: 11px;'>ইন-গেম আইডি</label><input id='free-p-id' placeholder='আইডি' required type='text'/></div>
                            <button class='btn-submit' type='submit'>আবেদন করুন</button>
                        </form>
                    </div>
                </div>

                <!-- 4. eFootball ID Sell Tab -->
                <div id='tab-content-market' style='display: none;'>
                    <div style='background: #1e293b; padding: 15px; border-radius: 12px; border: 1px solid #334155; margin-bottom: 15px;'>
                        <h4 style='color: #38bdf8; margin-bottom: 8px; font-size: 14px;'>🛒 আপনার eFootball ID বিক্রির বিজ্ঞাপন দিন</h4>
                        <p style='font-size: 12px; color: #94a3b8; margin-bottom: 10px;'>এখানে ছবি আপলোড করলে তা হোম পেজে সবার জন্য পাব্লিক হয়ে যাবে।</p>
                        <form onsubmit='handleSellPostSubmit(event)'>
                            <div class='form-group'><label style='font-size: 11px;'>আইডির বিবরণ</label><input id='market-title' placeholder='যেমন: 3 Epic Card ID, Max Squad' required type='text'/></div>
                            <div class='form-group'><label style='font-size: 11px;'>মূল্য (Tk)</label><input id='market-price' placeholder='দাম' required type='number'/></div>
                            <div class='form-group'>
                                <label style='font-size: 11px;'>স্ক্রিনশট আপলোড (স্কোয়াড/প্লেয়ার লিস্ট)</label>
                                <div style='display: flex; gap: 8px;'>
                                    <input id='market-img-url' placeholder='ছবির লিংক' required style='width: 100%;' type='text'/>
                                    <input accept='image/*' id='market-file-input' onchange='uploadMarketScreenshot()' style='display: none;' type='file'/>
                                    <button onclick='document.getElementById("market-file-input").click()' style='background: #334155; color: #fff; border: none; padding: 0 12px; border-radius: 6px; cursor: pointer;' type='button'>📷</button>
                                </div>
                            </div>
                            <button class='btn-submit' type='submit'>বিজ্ঞাপন পাবলিশ করুন</button>
                        </form>
                    </div>

                    <div class='box' style='background: #171f2d; margin-top: 15px;'>
                        <h4 style='color: #facc15; margin-bottom: 10px; font-size: 14px;'>📢 পাব্লিক মার্কেটপ্লেস (সব ইউজারের আইডি বিক্রির পোস্ট)</h4>
                        <div id='marketplace-items-container'>লোড হচ্ছে...</div>
                    </div>
                </div>

                <!-- 5. Buy Coins Tab -->
                <div id='tab-content-coin' style='display: none;'>
                    <div class='box' style='background: #1e293b; border: 1px solid #334155;'>
                        <h4 style='color: #facc15; margin-bottom: 10px; font-size: 15px;'>🪙 eFootball Coin টপ-আপ প্যাক</h4>
                        <div style='display: flex; flex-direction: column; gap: 12px;'>
                            <div style='background: #0f172a; padding: 12px; border-radius: 8px; border: 1px solid #334155; display: flex; justify-content: space-between; align-items: center;'>
                                <div><b style='color: #fff;'>530 eFootball Coins</b><p style='color: #25d366; font-size: 13px;'>মূল্য: ৪০০ টাকা</p></div>
                                <button onclick='buyCoinPack(530, 400)' style='background: #25d366; color: #fff; border: none; padding: 8px 14px; border-radius: 6px; font-weight: bold; cursor: pointer;'>কিনুন</button>
                            </div>
                            <div style='background: #0f172a; padding: 12px; border-radius: 8px; border: 1px solid #334155; display: flex; justify-content: space-between; align-items: center;'>
                                <div><b style='color: #fff;'>1050 eFootball Coins</b><p style='color: #25d366; font-size: 13px;'>মূল্য: ৭৫০ টাকা</p></div>
                                <button onclick='buyCoinPack(1050, 750)' style='background: #25d366; color: #fff; border: none; padding: 8px 14px; border-radius: 6px; font-weight: bold; cursor: pointer;'>কিনুন</button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- প্রোফাইল সেকশন (ডিপোজিট ও উইথড্র বাটন মেনুসহ) -->
            <div class='box' id='profile-section' style='display: none;'>
                <h3 style='color: #25d366; margin-bottom: 15px;'>👤 আপনার প্রোফাইল ও ওয়ালেট</h3>
                <div style='text-align: center; margin-bottom: 20px;'>
                    <div style='width: 80px; height: 80px; margin: 0 auto 10px auto; border-radius: 50%; background: #25d366; color: #fff; display: flex; align-items: center; justify-content: center; font-size: 32px; font-weight: bold; overflow: hidden; border: 2px solid #334155;'>
                        <span id='prof-avatar-letter'>S</span>
                        <img id='prof-avatar-img' src='' style='width: 100%; height: 100%; object-fit: cover; display: none;'/>
                    </div>
                    <div style='display: flex; gap: 8px; max-width: 320px; margin: 0 auto;'>
                        <input id='prof-img-url-input' placeholder='প্রোফাইল ছবির Direct URL লিংক' style='padding: 6px 10px; font-size: 11px; width: 100%; border-radius: 4px; border: 1px solid #334155; background: #0f172a; color: #fff;' type='text'/>
                        <input accept='image/*' id='prof-file-input' onchange='uploadProfileFile()' style='display: none;' type='file'/>
                        <button onclick='document.getElementById("prof-file-input").click()' style='background: #334155; color: #fff; border: none; padding: 0 10px; border-radius: 4px; cursor: pointer;' type='button'>📷</button>
                        <button onclick='updateProfilePicture()' style='background: #25d366; color: #fff; border: none; padding: 6px 10px; border-radius: 4px; font-size: 11px; font-weight: bold; cursor: pointer;' type='button'>সেভ</button>
                    </div>
                </div>
                
                <div class='profile-card' style='margin-bottom: 15px;'>
                    <div class='profile-info-row'><span>নাম:</span><span id='prof-name'>-</span></div>
                    <div class='profile-info-row'><span>নম্বর:</span><span id='prof-phone'>-</span></div>
                    <div class='profile-info-row'><span>টাকা ব্যালেন্স:</span><span id='prof-balance'>0 Tk</span></div>
                    <div class='profile-info-row'><span>সম্পন্ন PvP ম্যাচ:</span><span id='prof-pvp'>0 টি</span></div>
                    <div class='profile-info-row'><span>সম্পন্ন Paid টুর্নামেন্ট:</span><span id='prof-paid'>0 টি</span></div>
                </div>

                <!-- প্রোফাইলের ভেতরে ডিপোজিট ও উইথড্র মেনু বাটন -->
                <div id='profile-main-view'>
                    <div style='display: flex; gap: 10px; margin-bottom: 20px;'>
                        <button onclick='switchProfileSubTab("deposit")' style='flex: 1; background: #25d366; color: #fff; border: none; padding: 12px; border-radius: 8px; font-weight: bold; cursor: pointer; font-size: 14px;'>💳 ডিপোজিট করুন</button>
                        <button onclick='switchProfileSubTab("withdraw")' style='flex: 1; background: #ef4444; color: #fff; border: none; padding: 12px; border-radius: 8px; font-weight: bold; cursor: pointer; font-size: 14px;'>🪙 উইথড্র করুন</button>
                    </div>
                </div>

                <!-- ডিপোজিট ফর্ম (লুকানো থাকবে, বাটনে ক্লিক করলে দেখাবে) -->
                <div id='profile-deposit-view' style='display: none; background: #1e293b; padding: 15px; border-radius: 12px; border: 1px solid #334155; margin-bottom: 20px;'>
                    <div style='display: flex; justify-content: space-between; align-items: center; margin-bottom: 10px;'>
                        <h4 style='color: #25d366; font-size: 15px;'>💳 টাকা ডিপোজিট করুন</h4>
                        <button onclick='switchProfileSubTab("main")' style='background: #334155; color: #fff; border: none; padding: 4px 10px; border-radius: 4px; cursor: pointer; font-size: 12px;'>⬅️ ব্যাক</button>
                    </div>
                    <p style='font-size: 12px; color: #facc15; margin-bottom: 10px; font-weight: bold;'>ডিপোজিট নম্বর: 01622842102 (Send Money)</p>
                    <form onsubmit='handleDeposit(event)'>
                        <div class='form-group'><label style='font-size: 11px;'>মাধ্যম</label><select id='dep-method'><option value='Bkash'>Bkash</option><option value='Nagad'>Nagad</option></select></div>
                        <div class='form-group'><label style='font-size: 11px;'>টাকা পরিমাণ</label><input id='dep-amount' placeholder='যেমন: 100' required type='number'/></div>
                        <div class='form-group'><label style='font-size: 11px;'>প্রেরক নম্বর</label><input id='dep-sender-no' placeholder='০১xxxxxxxx' required type='tel'/></div>
                        <div class='form-group'><label style='font-size: 11px;'>TrxID</label><input id='dep-trxid' placeholder='TrxID লিখুন' required type='text'/></div>
                        <button class='btn-submit' type='submit'>ডিপোজিট সাবমিট করুন</button>
                    </form>
                </div>

                <!-- উইথড্র ফর্ম (লুকানো থাকবে, বাটনে ক্লিক করলে দেখাবে) -->
                <div id='profile-withdraw-view' style='display: none; background: #1e293b; padding: 15px; border-radius: 12px; border: 1px solid #334155; margin-bottom: 20px;'>
                    <div style='display: flex; justify-content: space-between; align-items: center; margin-bottom: 10px;'>
                        <h4 style='color: #ef4444; font-size: 15px;'>🪙 টাকা উইথড্র করুন</h4>
                        <button onclick='switchProfileSubTab("main")' style='background: #334155; color: #fff; border: none; padding: 4px 10px; border-radius: 4px; cursor: pointer; font-size: 12px;'>⬅️ ব্যাক</button>
                    </div>
                    <form onsubmit='handleWithdraw(event)'>
                        <div class='form-group'><label style='font-size: 11px;'>মাধ্যম</label><select id='wd-method'><option value='Bkash'>Bkash</option><option value='Nagad'>Nagad</option></select></div>
                        <div class='form-group'><label style='font-size: 11px;'>টাকা পরিমাণ (সর্বনিম্ন ১০০)</label><input id='wd-amount' placeholder='যেমন: 100' required type='number'/></div>
                        <div class='form-group'><label style='font-size: 11px;'>নম্বর</label><input id='wd-target-phone' placeholder='০১xxxxxxxx' required type='tel'/></div>
                        <div class='form-group'><label style='font-size: 11px;'>নোট / ID</label><input id='wd-trxid' placeholder='নোট দিন' required type='text'/></div>
                        <button class='btn-submit' type='submit' style='background: linear-gradient(135deg, #ef4444, #dc2626);'>উইথড্র পাঠান</button>
                    </form>
                </div>

                <div style='text-align: center; margin-top: 20px;'>
                    <button onclick='handleLogout()' style='background: #ef4444; color: #fff; border: none; padding: 10px 20px; border-radius: 8px; font-weight: bold; cursor: pointer;'>🚪 লগআউট করুন</button>
                </div>
            </div>
        </div>

        <div class='bottom-nav'>
            <div class='bottom-nav-item active' onclick='switchSection("home")'>🏠 হোম</div>
            <div class='bottom-nav-item' onclick='switchSection("profile")'>👤 প্রোফাইল ও ওয়ালেট</div>
        </div>
    </div>
</body>
</html>
