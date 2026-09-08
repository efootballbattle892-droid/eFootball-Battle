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
        .btn-submit { width: 100%; background: linear-gradient(135deg, #25d366, #20ba5a); color: #fff; border: none; padding: 14px; border-radius: 8px; font-weight: bold; cursor: pointer; font-size: 16px; margin-top: 10px; }
        .switch-text { text-align: center; margin-top: 20px; font-size: 14px; color: #94a3b8; }
        .switch-text span { color: #3b82f6; cursor: pointer; font-weight: 600; text-decoration: underline; }
        nav { background: #1f2937; padding: 10px 15px; display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid #374151; position: sticky; top: 0; z-index: 100; }
        .logo { font-size: 16px; font-weight: bold; color: #25d366; cursor: pointer; }
        .nav-right { display: flex; align-items: center; gap: 8px; }
        .balance-badge { background: #374151; padding: 5px 10px; border-radius: 20px; font-size: 11px; color: #25d366; font-weight: bold; border: 1px solid #4b5563; }
        .profile-icon { width: 32px; height: 32px; background: #25d366; color: #fff; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-weight: bold; cursor: pointer; position: relative; font-size: 14px; overflow: hidden; background-size: cover; background-position: center; }
        .profile-menu { display: none; position: absolute; top: 45px; right: 0; background: #1f2937; border: 1px solid #374151; border-radius: 8px; width: 180px; box-shadow: 0 4px 12px rgba(0,0,0,0.4); z-index: 1000; }
        .profile-menu a, .profile-menu div { padding: 10px 15px; font-size: 14px; color: #f3f4f6; cursor: pointer; display: block; border-bottom: 1px solid #374151; text-decoration: none; }
        .main-container { max-width: 900px; margin: 20px auto; padding: 15px; }
        .box { background: #1f2937; border-radius: 10px; padding: 20px; margin-bottom: 20px; border: 1px solid #374151; }
        .tabs { display: flex; gap: 10px; margin-bottom: 15px; overflow-x: auto; padding-bottom: 5px; }
        .tab-btn { background: #374151; color: #fff; border: none; padding: 8px 16px; border-radius: 6px; cursor: pointer; font-weight: bold; white-space: nowrap; }
        .tab-btn.active { background: #25d366; color: #fff; }
        .bottom-nav { position: fixed; bottom: 0; left: 0; width: 100%; background: #1f2937; display: flex; justify-content: space-around; padding: 10px 0; border-top: 1px solid #374151; z-index: 1000; }
        .bottom-nav-item { text-align: center; color: #94a3b8; font-size: 11px; font-weight: bold; cursor: pointer; }
        .bottom-nav-item.active { color: #25d366; }
        .profile-card { background: #1e293b; padding: 20px; border-radius: 12px; border: 1px solid #334155; }
        .profile-info-row { display: flex; justify-content: space-between; padding: 10px 0; border-bottom: 1px solid #334155; font-size: 14px; }
        .profile-info-row span:last-child { font-weight: bold; color: #25d366; }
        .slots-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(260px, 1fr)); gap: 10px; margin-top: 10px; }
        .slot-item { background: #1e293b; border: 1px solid #334155; padding: 12px 15px; border-radius: 8px; display: flex; justify-content: space-between; align-items: center; font-size: 13px; }
        .slot-item.booked { border-color: #25d366; background: rgba(37, 211, 102, 0.05); }
        .slot-no { font-weight: bold; color: #facc15; }
        .match-card { background: #1e293b; border: 1px solid #334155; padding: 15px; border-radius: 10px; margin-bottom: 12px; font-size: 13px; }
    </style>
</head>
<body>
    <!-- এখানে আপনার বডি এবং স্ক্রিপ্ট কোডগুলো বসবে -->
</body>
</html>
