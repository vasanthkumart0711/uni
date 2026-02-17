<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>University Multi-Tool Portal</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
    <style>
        :root { --bg: #f8fafc; --card: #ffffff; --accent: #3b82f6; }
        body { background-color: var(--bg); font-family: 'Plus Jakarta Sans', sans-serif; }
        .glass-card { background: var(--card); border-radius: 24px; box-shadow: 0 10px 15px -3px rgba(0,0,0,0.1); border: 1px solid #e2e8f0; }
        .sidebar-item { transition: all 0.2s; }
        .sidebar-item:hover { background: #eff6ff; color: #2563eb; transform: translateX(5px); }
        .active-nav { background: #2563eb !important; color: white !important; }
        .hidden { display: none; }
        .auth-screen { min-height: 100vh; display: flex; align-items: center; justify-content: center; background: radial-gradient(circle at top right, #e0f2fe, #f8fafc); }
        @keyframes slideUp { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .animate-view { animation: slideUp 0.4s ease-out forwards; }
    </style>
</head>
<body>

    <div id="login-page" class="auth-screen p-4">
        <div class="max-w-md w-full p-1 rounded-[32px] bg-gradient-to-br from-blue-500 to-indigo-600 shadow-2xl">
            <div class="bg-white p-10 rounded-[28px] text-center">
                <div class="w-20 h-20 bg-blue-50 rounded-3xl mx-auto mb-6 flex items-center justify-center text-4xl">🎓</div>
                <h1 class="text-3xl font-extrabold text-slate-900 mb-2">UniPortal v4.0</h1>
                <p class="text-slate-500 mb-8">Login: student/1234 or admin/1234</p>
                <form id="login-form" class="space-y-5">
                    <input type="text" id="login-user" required class="w-full p-4 bg-slate-50 border rounded-2xl outline-none focus:border-blue-500" placeholder="Username">
                    <input type="password" id="login-pass" required class="w-full p-4 bg-slate-50 border rounded-2xl outline-none focus:border-blue-500" placeholder="Password">
                    <p id="login-error" class="text-red-500 text-sm hidden">Invalid credentials!</p>
                    <button type="submit" class="w-full bg-blue-600 text-white font-bold py-4 rounded-2xl shadow-lg transition active:scale-95">Sign In</button>
                </form>
            </div>
        </div>
    </div>

    <div id="main-dashboard" class="flex min-h-screen hidden">
        <nav class="w-72 bg-white p-6 space-y-8 hidden md:block border-r border-slate-100">
            <div class="flex items-center gap-3 px-2 py-4">
                <div class="bg-blue-600 p-2 rounded-lg text-white">⚡</div>
                <h2 class="text-xl font-black text-slate-800">UNIPORTAL</h2>
            </div>
            <ul class="space-y-3" id="main-nav">
                <li><a href="#" data-target="shop-section" class="sidebar-item active-nav block p-4 rounded-2xl font-semibold flex items-center gap-4">🛒 <span>Catalog</span></a></li>
                <li><a href="#" data-target="timer-section" class="sidebar-item block p-4 rounded-2xl font-semibold flex items-center gap-4 text-slate-500">⏳ <span>Focus</span></a></li>
                <li><a href="#" data-target="todo-section" class="sidebar-item block p-4 rounded-2xl font-semibold flex items-center gap-4 text-slate-500">📝 <span>Tasks</span></a></li>
            </ul>
            <div class="pt-20">
                <p id="user-display" class="text-sm font-bold text-blue-600 capitalize"></p>
                <button onclick="location.reload()" class="mt-4 text-red-500 font-bold text-sm">Logout</button>
            </div>
        </nav>

        <main class="flex-1 p-10 overflow-y-auto">
            <section id="shop-section" class="content-view animate-view">
                <h2 class="text-3xl font-bold mb-6">University Store</h2>
                <div id="product-grid" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6"></div>
            </section>

            <section id="timer-section" class="content-view hidden animate-view">
                <div class="max-w-md mx-auto glass-card p-10 text-center">
                    <h2 id="timer-display" class="text-6xl font-black text-blue-600 mb-6">25:00</h2>
                    <button id="start-timer" class="bg-blue-600 text-white px-8 py-3 rounded-xl font-bold">Start Session</button>
                </div>
            </section>

            <section id="todo-section" class="content-view hidden animate-view">
                <div class="max-w-lg glass-card p-8">
                    <h2 class="text-2xl font-bold mb-4">Task Manager</h2>
                    <div class="flex gap-2 mb-6">
                        <input type="text" id="todo-input" class="flex-1 p-3 border rounded-xl outline-none" placeholder="New task...">
                        <button id="add-todo" class="bg-slate-900 text-white px-6 rounded-xl font-bold">Add</button>
                    </div>
                    <ul id="todo-list" class="space-y-2"></ul>
                </div>
            </section>
        </main>
    </div>

    <script>
        // AUTH SYSTEM
        document.getElementById('login-form').addEventListener('submit', (e) => {
            e.preventDefault();
            const u = document.getElementById('login-user').value.toLowerCase();
            const p = document.getElementById('login-pass').value;
            if(p === '1234') {
                document.getElementById('login-page').classList.add('hidden');
                document.getElementById('main-dashboard').classList.remove('hidden');
                document.getElementById('user-display').innerText = "Hello, " + u;
                renderStore();
            } else {
                document.getElementById('login-error').classList.remove('hidden');
            }
        });

        // NAVIGATION
        document.querySelectorAll('#main-nav a').forEach(link => {
            link.addEventListener('click', (e) => {
                const target = e.currentTarget.dataset.target;
                document.querySelectorAll('.content-view').forEach(v => v.classList.add('hidden'));
                document.getElementById(target).classList.remove('hidden');
                document.querySelectorAll('.sidebar-item').forEach(i => i.classList.remove('active-nav'));
                e.currentTarget.classList.add('active-nav');
            });
        });

        // STORE
        function renderStore() {
            const items = [
                {n: "Algorithms 4th Ed", i: "📚", p: 85},
                {n: "Graphing Calc", i: "🧮", p: 120},
                {n: "Focus Buds", i: "🎧", p: 199}
            ];
            document.getElementById('product-grid').innerHTML = items.map(x => `
                <div class="glass-card p-6 text-center">
                    <div class="text-5xl mb-4">${x.i}</div>
                    <h4 class="font-bold">${x.n}</h4>
                    <p class="text-blue-600 font-black mt-2">$${x.p}</p>
                </div>
            `).join('');
        }

        // TODO
        const todos = [];
        document.getElementById('add-todo').addEventListener('click', () => {
            const val = document.getElementById('todo-input').value;
            if(val) {
                todos.push(val);
                document.getElementById('todo-list').innerHTML = todos.map(t => `<li class="p-3 bg-slate-50 rounded-lg border">${t}</li>`).join('');
                document.getElementById('todo-input').value = '';
            }
        });

        // TIMER
        let timeLeft = 1500;
        document.getElementById('start-timer').addEventListener('click', function() {
            setInterval(() => {
                if(timeLeft > 0) {
                    timeLeft--;
                    const m = Math.floor(timeLeft / 60);
                    const s = timeLeft % 60;
                    document.getElementById('timer-display').innerText = `${m}:${s < 10 ? '0'+s : s}`;
                }
            }, 1000);
        });
    </script>
</body>
</html>
