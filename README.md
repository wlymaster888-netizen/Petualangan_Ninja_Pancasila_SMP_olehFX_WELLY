# Petualangan_Ninja_Pancasila_SMP_olehFX_WELLY
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Petualangan Ninja Pancasila SMP</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600;800&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Poppins', sans-serif;
            overflow: hidden;
            touch-action: none;
            background: linear-gradient(to bottom, #030712, #0f172a, #1e293b);
        }
        canvas {
            display: block;
            width: 100%;
            height: 100%;
        }
        @keyframes pulseGlow {
            0%, 100% { transform: scale(1); filter: drop-shadow(0 0 12px rgba(245, 158, 11, 0.7)); }
            50% { transform: scale(1.08); filter: drop-shadow(0 0 24px rgba(245, 158, 11, 0.95)); }
        }
        .ninja-glow {
            animation: pulseGlow 2s infinite;
        }
    </style>
</head>
<body class="relative w-full h-screen select-none">

    <canvas id="gameCanvas"></canvas>

    <!-- Top HUD Overlay -->
    <div id="ui-layer" class="absolute top-0 left-0 w-full p-4 flex justify-between items-start pointer-events-none z-10">
        <div class="bg-slate-900/85 backdrop-blur-md border-2 border-amber-500/50 rounded-2xl px-5 py-2.5 shadow-xl flex items-center gap-3">
            <span class="text-2xl">🥷</span>
            <div>
                <div class="text-xs text-amber-400 font-semibold tracking-wider">NINJA RANK</div>
                <div id="rankValue" class="text-sm md:text-lg font-extrabold text-white">Genin Pancasila</div>
            </div>
            <div class="ml-4 pl-4 border-l border-slate-700">
                <div class="text-xs text-amber-400 font-semibold tracking-wider">NILAI / POIN</div>
                <div class="flex items-center gap-1.5">
                    <span id="scoreValue" class="text-base md:text-lg font-extrabold text-amber-300">0</span>
                    <span class="text-xs text-amber-400 font-bold">(<span id="pointVal">0</span>/100)</span>
                </div>
            </div>
        </div>

        <div class="bg-slate-900/85 backdrop-blur-md border-2 border-indigo-500/50 rounded-2xl px-5 py-2.5 shadow-xl text-right">
            <div class="text-xs text-indigo-400 font-semibold tracking-wider">MISI TINGKAT SMP</div>
            <div class="text-sm md:text-lg font-extrabold text-white"><span id="progressValue" class="text-indigo-400">0</span> / 20 Soal</div>
            <div id="starsDisplay" class="flex justify-end gap-1 mt-0.5 text-xs">⭐ 0/20</div>
        </div>
    </div>

    <!-- Start Screen Modal -->
    <div id="startScreen" class="absolute inset-0 bg-slate-950/92 backdrop-blur-lg flex flex-col items-center justify-center z-50 text-white p-6 overflow-y-auto">
        <div class="text-6xl md:text-8xl mb-3 ninja-glow">🥷📜</div>
        <h1 class="text-3xl md:text-5xl font-black text-center mb-2 bg-gradient-to-r from-amber-400 to-orange-500 bg-clip-text text-transparent">
            Petualangan Ninja Pancasila SMP
        </h1>
        <p class="text-xs md:text-base mb-6 text-center max-w-lg text-slate-300 font-medium">
            Jelajahi hutan pegunungan magis di bawah matahari & bulan, lompat menaiki awan sambil ditemani kupu-kupu, capung, burung, dan kelelawar. Jawab <span class="text-amber-400 font-bold">20 Soal Pancasila SMP</span>, kumpulkan Bintang, dan raih nilai 100 beserta Piala Kehormatan!
        </p>

        <div class="bg-slate-900/90 border-2 border-slate-800 p-5 rounded-3xl shadow-2xl max-w-md w-full mb-6">
            <h2 class="text-sm md:text-base font-bold mb-2 text-center text-amber-400">Panduan Ninja:</h2>
            <ul class="text-xs md:text-sm space-y-1.5 font-medium text-slate-300">
                <li class="flex items-center gap-2">🕹️ Gunakan Tombol Kiri/Kanan atau D-Pad di bawah untuk bergerak.</li>
                <li class="flex items-center gap-2">☁️ Ninja melompat otomatis saat mendarat di awan/pohon.</li>
                <li class="flex items-center gap-2">📜 Sentuh Gulungan Misterius untuk menjawab soal (+5 poin & 1 Bintang).</li>
            </ul>
        </div>

        <button id="btnStart" class="bg-gradient-to-r from-amber-500 to-orange-600 hover:from-amber-400 hover:to-orange-500 text-slate-950 text-lg md:text-xl font-black py-3.5 px-10 rounded-full shadow-[0_6px_0_#b45309] active:shadow-[0_0px_0_#b45309] active:translate-y-[6px] transition-all">
            MULAI PETUALANGAN
        </button>
    </div>

    <!-- Quiz Modal for SMP Level Questions -->
    <div id="quizModal" class="hidden absolute inset-0 bg-slate-950/85 backdrop-blur-md flex items-center justify-center z-40 p-4">
        <div class="bg-slate-900 border-4 border-amber-500 rounded-3xl p-5 md:p-7 w-full max-w-xl shadow-2xl relative max-h-[90vh] overflow-y-auto">
            <div class="absolute -top-5 left-1/2 -translate-x-1/2 bg-amber-500 text-slate-950 font-black px-5 py-1 rounded-full border-2 border-slate-900 shadow-md text-sm md:text-lg tracking-wider">
                Tantangan Gulungan <span id="qNum">1</span>/20 (⭐ +1)
            </div>

            <div class="text-center mt-3 mb-2 text-xs font-bold text-amber-400 uppercase tracking-widest" id="qCategory">Kategori</div>
            <h2 id="qText" class="text-sm md:text-lg font-bold text-center text-white mb-5 leading-relaxed">
                Pertanyaan Pancasila SMP akan muncul di sini.
            </h2>

            <div id="optionsContainer" class="space-y-2.5">
                <!-- Options injected via JavaScript -->
            </div>

            <div id="feedbackMsg" class="hidden mt-3 text-center text-sm md:text-base font-black p-2.5 rounded-2xl"></div>
        </div>
    </div>

    <!-- Win Screen Modal -->
    <div id="winScreen" class="hidden absolute inset-0 bg-slate-950/95 backdrop-blur-xl flex flex-col items-center justify-center z-50 text-white p-6 overflow-y-auto">
        <div class="text-7xl md:text-9xl mb-3 animate-bounce">🏆</div>
        <h1 class="text-3xl md:text-5xl font-black text-center mb-2 text-amber-400">LUAR BIASA!</h1>
        <div class="bg-amber-500/20 border-2 border-amber-500/50 rounded-2xl px-6 py-3 mb-4 text-center">
            <div class="text-xs text-amber-300 font-bold uppercase">Skor Akhir / Nilai</div>
            <div class="text-4xl font-black text-amber-400">100 / 100 ⭐</div>
        </div>
        <p class="text-xs md:text-base text-center max-w-md text-slate-300 font-semibold mb-6">
            Kamu berhasil menjawab seluruh 20 soal Pancasila SMP dengan benar, mengumpulkan 20 Bintang, dan berhak atas <span class="text-amber-400 font-bold">Piala Grandmaster Ninja Pancasila</span>!
        </p>

        <button id="btnRestart" class="bg-gradient-to-r from-emerald-500 to-teal-600 hover:from-emerald-400 hover:to-teal-500 text-slate-950 text-base md:text-lg font-black py-3.5 px-10 rounded-full shadow-[0_6px_0_#047857] active:shadow-[0_0px_0_#047857] active:translate-y-[6px] transition-all">
            MAIN LAGI
        </button>
    </div>

    <!-- Mobile Touch D-Pad Controls -->
    <div id="touchControls" class="absolute bottom-6 left-6 right-6 flex justify-between items-center md:hidden z-20 pointer-events-auto">
        <div class="flex gap-3">
            <button id="btnLeft" class="w-16 h-16 bg-slate-800/80 active:bg-amber-500 text-white rounded-2xl border-2 border-slate-700 flex items-center justify-center text-2xl shadow-lg">⬅️</button>
            <button id="btnRight" class="w-16 h-16 bg-slate-800/80 active:bg-amber-500 text-white rounded-2xl border-2 border-slate-700 flex items-center justify-center text-2xl shadow-lg">➡️</button>
        </div>
        <div class="text-xs text-slate-400 font-medium bg-slate-900/60 px-4 py-2 rounded-xl border border-slate-800">
            Geser / Tombol untuk Gerak
        </div>
    </div>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        // UI elements
        const startScreen = document.getElementById('startScreen');
        const btnStart = document.getElementById('btnStart');
        const rankValue = document.getElementById('rankValue');
        const scoreValue = document.getElementById('scoreValue');
        const pointVal = document.getElementById('pointVal');
        const progressValue = document.getElementById('progressValue');
        const starsDisplay = document.getElementById('starsDisplay');
        const quizModal = document.getElementById('quizModal');
        const winScreen = document.getElementById('winScreen');
        const btnRestart = document.getElementById('btnRestart');
        const qNum = document.getElementById('qNum');
        const qCategory = document.getElementById('qCategory');
        const qText = document.getElementById('qText');
        const optionsContainer = document.getElementById('optionsContainer');
        const feedbackMsg = document.getElementById('feedbackMsg');

        // Game World Configuration (Zoomed out maximum for expansive mountain/forest map)
        const WORLD_WIDTH = 1200;
        const WORLD_HEIGHT = 8000;

        let gameState = 'START';
        let score = 0;
        let points = 0;
        let progress = 0;
        let starsCount = 0;
        let lastTime = 0;
        let currentScrollItem = null;

        const camera = { x: 0, y: 0 };

        // Ninja Player Object
        const player = {
            x: 600,
            y: WORLD_HEIGHT - 200,
            width: 34,
            height: 46,
            vx: 0,
            vy: 0,
            speed: 450,
            jumpForce: -850,
            gravity: 1700,
            facingRight: true,
            isGrounded: false
        };

        // Inputs
        const keys = { ArrowLeft: false, ArrowRight: false, a: false, d: false };
        const touch = { left: false, right: false };

        // Platforms, Quiz Items, and Scenery Elements (Mountains, Trees, Flowers, River, Creatures)
        let platforms = [];
        let scrollItems = [];
        let scenery = {
            mountains: [],
            sunsAndMoons: [],
            trees: [],
            flowers: [],
            riverSegments: [],
            creatures: []
        };

        // 20 SMP Level Pancasila Questions
        const smpQuestions = [
            {
                id: 1,
                title: "Sidang BPUPKI Pertama",
                q: "Sidang BPUPKI pertama yang membahas rumusan dasar negara dilaksanakan pada tanggal...",
                options: ["29 Mei - 1 Juni 1945", "10 - 17 Juli 1945", "18 Agustus 1945", "22 Juni 1945"],
                correct: 0
            },
            {
                id: 2,
                title: "Perumusan Piagam Jakarta",
                q: "Panitia Sembilan yang menghasilkan Piagam Jakarta dibentuk pada tanggal...",
                options: ["1 Juni 1945", "22 Juni 1945", "17 Agustus 1945", "18 Agustus 1945"],
                correct: 1
            },
            {
                id: 3,
                title: "Fungsi Pancasila sebagai Paradigma",
                q: "Kedudukan Pancasila sebagai kerangka acuan dalam pembangunan nasional disebut sebagai...",
                options: ["Dasar Negara", "Ideologi Terbuka", "Paradigma Pembangunan", "Pandangan Hidup Bangsa"],
                correct: 2
            },
            {
                id: 4,
                title: "Nilai Sila ke-4 dalam Demokrasi",
                q: "Penerapan nilai Sila ke-4 Pancasila dalam kehidupan bernegara sesuai sistem ketatanegaraan adalah...",
                options: ["Gotong royong membersihkan lingkungan", "Musyawarah mufakat dalam pengambilan keputusan", "Mengembangkan sikap saling mencintai", "Menjunjung tinggi nilai kemanusiaan"],
                correct: 1
            },
            {
                id: 5,
                title: "Sifat Ideologi Pancasila",
                q: "Mengapa Pancasila disebut sebagai ideologi terbuka bagi bangsa Indonesia?",
                options: ["Dapat diubah kapan saja sesuai keinginan penguasa", "Mengandung nilai-nilai dasar yang kaku dan mutlak", "Nilai-nilainya dapat dikembangkan sesuai dinamika zaman tanpa kehilangan jatidiri", "Berasal dari budaya luar yang modern"],
                correct: 2
            },
            {
                id: 6,
                title: "Pancasila dalam Pembukaan UUD 1945",
                q: "Rumusan resmi Pancasila yang sah dan konstitusional tercantum dalam Pembukaan UUD 1945 pada alinea ke-...",
                options: ["Alinea ke-1", "Alinea ke-2", "Alinea ke-3", "Alinea ke-4"],
                correct: 3
            },
            {
                id: 7,
                title: "Fungsi Utama Pandangan Hidup",
                q: "Sebagai pandangan hidup bangsa (Way of Life), Pancasila berfungsi sebagai...",
                options: ["Sumber dari segala sumber hukum negara", "Petunjuk arah dalam mencapai cita-cita dan menyelesaikan masalah berbangsa", "Alat pemersatu kekuasaan politik", "Dasar berdirinya struktur militer nasional"],
                correct: 1
            },
            {
                id: 8,
                title: "Pengamalan Sila ke-5",
                q: "Contoh perwujudan keadilan sosial bagi seluruh rakyat Indonesia di lingkungan masyarakat adalah...",
                options: ["Membayar pajak tepat waktu dan menikmati fasilitas umum secara adil", "Memilih pertemanan berdasarkan status sosial", "Mengikuti pemilihan umum secara tertib", "Melaksanakan ibadah sesuai agama masing-masing"],
                correct: 0
            },
            {
                id: 9,
                title: "Peran PPKI",
                q: "Pada tanggal 18 Agustus 1945, PPKI mengesahkan UUD 1945 dan menetapkan...",
                options: ["Teks Proklamasi Kemerdekaan", "Piagam Jakarta secara utuh", "Presiden dan Wakil Presiden serta pengesahan UUD", "Sistem pertahanan keamanan rakyat"],
                correct: 2
            },
            {
                id: 10,
                title: "Ancaman terhadap Ideologi Negara",
                q: "Wujud nyata bela negara bagi pelajar SMP dalam mempertahankan Pancasila dari ancaman non-militer adalah...",
                options: ["Menguasai teknologi militer canggih", "Meningkatkan literasi digital dan menyaring informasi hoax serta radikalisme", "Menutup diri dari budaya asing sepenuhnya", "Menghafalkan seluruh sejarah dunia"],
                correct: 1
            },
            {
                id: 11,
                title: "Tokoh Perumus Dasar Negara",
                q: "Tiga tokoh yang mengusulkan rumusan dasar negara pada sidang BPUPKI adalah Soekarno, Mr. Mohammad Yamin, dan...",
                options: ["Dr. Soepomo", "Drs. Moh. Hatta", "Ki Hajar Dewantara", "A.A. Maramis"],
                correct: 0
            },
            {
                id: 12,
                title: "Makna Sila Pertama",
                q: "Sila pertama 'Ketuhanan Yang Maha Esa' mengandung nilai utama berupa...",
                options: ["Persamaan derajat sesama manusia", "Kebebasan memeluk agama dan toleransi antarumat beragama", "Semangat gotong royong dan kekeluargaan", "Cinta tanah air dan bangsa"],
                correct: 1
            },
            {
                id: 13,
                title: "Sumber dari Segala Sumber Hukum",
                q: "Kedudukan Pancasila sebagai sumber dari segala sumber hukum negara tercantum dalam...",
                options: ["Ketetapan MPR No. II/MPR/1978", "UU No. 12 Tahun 2011", "Undang-Undang Dasar Negara Republik Indonesia Tahun 1945", "Dekrit Presiden 5 Juli 1959"],
                correct: 1
            },
            {
                id: 14,
                title: "Gotong Royong sebagai Budaya",
                q: "Gotong royong yang merupakan akar budaya bangsa Indonesia mencerminkan pengamalan sila ke-...",
                options: ["Sila ke-2", "Sila ke-3", "Sila ke-4", "Sila ke-5"],
                correct: 1
            },
            {
                id: 15,
                title: "Fungsi Pancasila dalam Hubungan Internasional",
                q: "Dalam pergaulan internasional, politik luar negeri bebas aktif Indonesia bersumber dari nilai-nilai...",
                options: ["UUD 1945 alinea pertama", "Pancasila secara utuh", "Keputusan Dewan Keamanan PBB", "Manifesto politik nasional"],
                correct: 1
            },
            {
                id: 16,
                title: "Peristiwa Sumpah Pemuda",
                q: "Sumpah Pemuda yang dikumandangkan pada 28 Oktober 1928 merupakan landasan kuat bagi perwujudan sila ke-...",
                options: ["Sila ke-1", "Sila ke-2", "Sila ke-3", "Sila ke-4"],
                correct: 1
            },
            {
                id: 17,
                title: "Hak dan Kewajiban Warga Negara",
                q: "Keseimbangan antara hak dan kewajiban dalam kehidupan bernegara merupakan pengamalan sila ke-...",
                options: ["Sila ke-2", "Sila ke-3", "Sila ke-4", "Sila ke-5"],
                correct: 3
            },
            {
                id: 18,
                title: "Demokrasi Pancasila",
                q: "Ciri khas utama dari Demokrasi Pancasila dibandingkan demokrasi liberal adalah...",
                options: ["Pengambilan keputusan berdasarkan suara terbanyak mutlak tanpa musyawarah", "Mengutamakan musyawarah mufakat untuk mencapai kesepakatan bersama", "Dominasi penuh oleh partai politik pemenang pemilu", "Kebebasan mutlak tanpa batas aturan moral"],
                correct: 1
            },
            {
                id: 19,
                title: "Wawasan Nusantara",
                q: "Cara pandang bangsa Indonesia terhadap diri dan lingkungannya sebagai satu kesatuan ideologi, politik, ekonomi, sosial budaya, dan pertahanan keamanan disebut...",
                options: ["Ketahanan Nasional", "Wawasan Nusantara", "Nusantara Raya", "Geopolitik Kepulauan"],
                correct: 1
            },
            {
                id: 20,
                title: "Pancasila sebagai Jiwa Bangsa",
                q: "Menurut Prof. Dr. Mr. Soepomo, Pancasila lahir dari kepribadian bangsa yang telah hidup berabad-abad, sehingga Pancasila berfungsi sebagai...",
                options: ["Jiwa dan Kepribadian Bangsa Indonesia", "Peraturan administratif sementara", "Dokumen perjanjian antar suku", "Kontrak sosial politik kekuasaan"],
                correct: 0
            }
        ];

        function generateLevel() {
            platforms = [];
            scrollItems = [];
            scenery.mountains = [];
            scenery.sunsAndMoons = [];
            scenery.trees = [];
            scenery.flowers = [];
            scenery.riverSegments = [];
            scenery.creatures = [];

            // Background Mountains (Parallax layers across heights)
            for (let i = 0; i < 12; i++) {
                scenery.mountains.push({
                    x: (i * 400) - 200,
                    y: WORLD_HEIGHT - (i * 750) - 1000,
                    w: 700,
                    h: 900,
                    color: i % 2 === 0 ? '#111827' : '#1e293b'
                });
            }

            // Suns and Moons scattered across the vertical altitudes
            for (let i = 0; i < 6; i++) {
                let isSun = (i % 2 === 0);
                scenery.sunsAndMoons.push({
                    x: isSun ? 150 : WORLD_WIDTH - 150,
                    y: i * 1400 + 500,
                    radius: isSun ? 55 : 35,
                    type: isSun ? 'sun' : 'moon'
                });
            }

            // Winding River flowing down the map
            let rY = WORLD_HEIGHT;
            let rX = 850;
            while (rY > 0) {
                scenery.riverSegments.push({ x: rX, y: rY - 450, w: 140, h: 470 });
                rX += Math.sin(rY / 500) * 100;
                rY -= 450;
            }

            // Ground platform at bottom
            platforms.push({ x: 0, y: WORLD_HEIGHT - 60, width: WORLD_WIDTH, height: 60, type: 'ground' });

            // Generate upward platforms and place 20 quiz items
            let currentY = WORLD_HEIGHT - 240;
            let stepY = 320;

            for (let i = 0; i < smpQuestions.length; i++) {
                const pWidth = 160 + Math.random() * 60;
                let pX = Math.random() * (WORLD_WIDTH - pWidth - 250) + 80;
                
                if (Math.abs(pX + pWidth/2 - rX) < 120) pX -= 180;

                platforms.push({
                    x: pX,
                    y: currentY,
                    width: pWidth,
                    height: 22,
                    type: 'cloud'
                });

                // Place quiz scroll item right above this platform
                scrollItems.push({
                    id: smpQuestions[i].id,
                    data: smpQuestions[i],
                    x: pX + pWidth / 2 - 20,
                    y: currentY - 55,
                    width: 40,
                    height: 40,
                    collected: false,
                    floatOffset: Math.random() * Math.PI
                });

                // Add forest scenery near platforms (trees & flowers)
                if (i % 2 === 0) {
                    scenery.trees.push({ x: pX - 70, y: currentY - 45, size: 35 + Math.random() * 25 });
                } else {
                    scenery.flowers.push({ x: pX + pWidth + 25, y: currentY + 10, color: i % 3 === 0 ? '#f43f5e' : '#a855f7' });
                }

                currentY -= stepY;
            }

            // Add flying creatures: birds, bats, butterflies, and dragonflies across altitudes
            const creatureTypes = ['bird', 'bat', 'butterfly', 'dragonfly'];
            for (let y = 300; y < WORLD_HEIGHT - 300; y += 220) {
                let type = creatureTypes[Math.floor(Math.random() * creatureTypes.length)];
                scenery.creatures.push({
                    type: type,
                    x: Math.random() * (WORLD_WIDTH - 200) + 100,
                    y: y + (Math.random() * 100 - 50),
                    baseY: y,
                    speed: 60 + Math.random() * 80,
                    direction: Math.random() > 0.5 ? 1 : -1,
                    range: 150 + Math.random() * 100,
                    wingPhase: Math.random() * Math.PI * 2
                });
            }
        }

        window.addEventListener('keydown', e => {
            if (e.key === 'ArrowLeft' || e.key.toLowerCase() === 'a') keys.ArrowLeft = true;
            if (e.key === 'ArrowRight' || e.key.toLowerCase() === 'd') keys.ArrowRight = true;
        });

        window.addEventListener('keyup', e => {
            if (e.key === 'ArrowLeft' || e.key.toLowerCase() === 'a') keys.ArrowLeft = false;
            if (e.key === 'ArrowRight' || e.key.toLowerCase() === 'd') keys.ArrowRight = false;
        });

        function setupTouchBtn(id, touchKey) {
            const el = document.getElementById(id);
            if (!el) return;
            el.addEventListener('touchstart', (e) => { e.preventDefault(); touch[touchKey] = true; });
            el.addEventListener('touchend', (e) => { e.preventDefault(); touch[touchKey] = false; });
            el.addEventListener('mousedown', () => touch[touchKey] = true);
            el.addEventListener('mouseup', () => touch[touchKey] = false);
        }
        setupTouchBtn('btnLeft', 'left');
        setupTouchBtn('btnRight', 'right');

        function resizeCanvas() {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
        }
        window.addEventListener('resize', resizeCanvas);

        function startGame() {
            startScreen.classList.add('hidden');
            gameState = 'PLAYING';
            score = 0;
            points = 0;
            progress = 0;
            starsCount = 0;
            scoreValue.textContent = score;
            pointVal.textContent = points;
            progressValue.textContent = progress;
            starsDisplay.textContent = `⭐ ${starsCount}/20`;
            rankValue.textContent = "Genin Pancasila";
            player.x = WORLD_WIDTH / 2 - player.width / 2;
            player.y = WORLD_HEIGHT - 150;
            player.vy = 0;
            generateLevel();
            lastTime = performance.now();
            requestAnimationFrame(gameLoop);
        }

        btnStart.addEventListener('click', startGame);
        btnRestart.addEventListener('click', () => {
            winScreen.classList.add('hidden');
            startGame();
        });

        function triggerQuiz(scrollItem) {
            currentScrollItem = scrollItem;
            const qData = scrollItem.data;
            qNum.textContent = qData.id;
            qCategory.textContent = qData.title;
            qText.textContent = qData.q;
            optionsContainer.innerHTML = '';
            feedbackMsg.classList.add('hidden');

            qData.options.forEach((opt, idx) => {
                const btn = document.createElement('button');
                btn.className = "w-full text-left bg-slate-800 hover:bg-indigo-600/60 border-2 border-slate-700 text-white text-xs md:text-sm font-semibold py-3 px-4 rounded-2xl shadow transition-all active:scale-[0.98]";
                btn.textContent = `${String.fromCharCode(65 + idx)}. ${opt}`;
                btn.onclick = () => handleAnswer(idx, qData);
                optionsContainer.appendChild(btn);
            });

            quizModal.classList.remove('hidden');
            gameState = 'QUIZ';
        }

        function handleAnswer(selectedIdx, qData) {
            const buttons = optionsContainer.querySelectorAll('button');
            buttons.forEach(b => b.disabled = true);

            if (selectedIdx === qData.correct) {
                feedbackMsg.innerHTML = "✨ BENAR! +5 Poin & ⭐ 1 Bintang! ✨";
                feedbackMsg.className = "mt-3 text-center text-sm md:text-base font-black p-3 rounded-2xl bg-emerald-500/20 text-emerald-400 border-2 border-emerald-500/50";
                feedbackMsg.classList.remove('hidden');

                points += 5;
                score = points;
                progress += 1;
                starsCount += 1;

                scoreValue.textContent = score;
                pointVal.textContent = points;
                progressValue.textContent = progress;
                starsDisplay.textContent = `⭐ ${starsCount}/20`;

                if (progress >= 7 && progress < 14) rankValue.textContent = "Chunin Pancasila";
                if (progress >= 14) rankValue.textContent = "Jonin Pancasila";

                currentScrollItem.collected = true;

                setTimeout(() => {
                    quizModal.classList.add('hidden');
                    gameState = 'PLAYING';
                    if (progress >= smpQuestions.length) {
                        winScreen.classList.remove('hidden');
                        gameState = 'WIN';
                    }
                }, 1300);
            } else {
                feedbackMsg.textContent = "❌ Belum tepat! Pelajari kembali materi Pancasila SMP.";
                feedbackMsg.className = "mt-3 text-center text-sm md:text-base font-black p-3 rounded-2xl bg-rose-500/20 text-rose-400 border-2 border-rose-500/50";
                feedbackMsg.classList.remove('hidden');

                setTimeout(() => {
                    buttons.forEach(b => b.disabled = false);
                    feedbackMsg.classList.add('hidden');
                }, 1500);
            }
        }

        function update(dt) {
            // Horizontal Movement
            player.vx = 0;
            if (keys.ArrowLeft || keys.a || touch.left) {
                player.vx = -player.speed;
                player.facingRight = false;
            }
            if (keys.ArrowRight || keys.d || touch.right) {
                player.vx = player.speed;
                player.facingRight = true;
            }

            player.x += player.vx * dt;

            // Screen boundary wrapping
            if (player.x < 0) player.x = 0;
            if (player.x > WORLD_WIDTH - player.width) player.x = WORLD_WIDTH - player.width;

            // Vertical Gravity & Jump
            player.vy += player.gravity * dt;
            player.y += player.vy * dt;

            // Platform Collisions
            player.isGrounded = false;
            platforms.forEach(p => {
                if (
                    player.vy > 0 &&
                    player.x + player.width > p.x &&
                    player.x < p.x + p.width &&
                    player.y + player.height >= p.y &&
                    player.y + player.height - player.vy * dt <= p.y + 12
                ) {
                    player.y = p.y - player.height;
                    player.vy = player.jumpForce; // Automatic ninja jump
                    player.isGrounded = true;
                }
            });

            // Prevent falling below world
            if (player.y > WORLD_HEIGHT) {
                player.y = WORLD_HEIGHT - 120;
                player.vy = player.jumpForce;
            }

            // Update Flying Creatures positions
            scenery.creatures.forEach(c => {
                c.x += c.direction * c.speed * dt;
                if (c.x < 50 || c.x > WORLD_WIDTH - 50) {
                    c.direction *= -1;
                }
                c.wingPhase += dt * 12;
            });

            // Scroll Items Collection Check
            scrollItems.forEach(item => {
                if (!item.collected) {
                    const dx = (player.x + player.width / 2) - (item.x + item.width / 2);
                    const dy = (player.y + player.height / 2) - (item.y + item.height / 2);
                    const dist = Math.sqrt(dx * dx + dy * dy);

                    if (dist < 45) {
                        triggerQuiz(item);
                    }
                }
            });

            // Camera Follow Player Smoothly (Vertical Focus)
            const targetCamY = player.y - canvas.height / 2;
            camera.y += (targetCamY - camera.y) * 8 * dt;

            if (camera.y < 0) camera.y = 0;
            if (camera.y > WORLD_HEIGHT - canvas.height) camera.y = WORLD_HEIGHT - canvas.height;
        }

        function draw() {
            // Background gradient
            ctx.fillStyle = '#030712';
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            ctx.save();
            const scale = canvas.width / WORLD_WIDTH;
            ctx.scale(scale, scale);
            ctx.translate(0, -camera.y);

            // 1. Draw Suns and Moons
            scenery.sunsAndMoons.forEach(sm => {
                ctx.save();
                ctx.shadowColor = sm.type === 'sun' ? '#fbbf24' : '#93c5fd';
                ctx.shadowBlur = 35;
                ctx.fillStyle = sm.type === 'sun' ? '#f59e0b' : '#cbd5e1';
                ctx.beginPath();
                ctx.arc(sm.x, sm.y, sm.radius, 0, Math.PI * 2);
                ctx.fill();
                ctx.restore();
            });

            // 2. Draw Background Mountains
            scenery.mountains.forEach(m => {
                ctx.fillStyle = m.color;
                ctx.beginPath();
                ctx.moveTo(m.x, m.y + m.h);
                ctx.lineTo(m.x + m.w / 2, m.y);
                ctx.lineTo(m.x + m.w, m.y + m.h);
                ctx.closePath();
                ctx.fill();

                // Mountain snow caps
                ctx.fillStyle = '#94a3b8';
                ctx.beginPath();
                ctx.moveTo(m.x + m.w / 2, m.y);
                ctx.lineTo(m.x + m.w / 2 - 60, m.y + 110);
                ctx.lineTo(m.x + m.w / 2 + 60, m.y + 110);
                ctx.closePath();
                ctx.fill();
            });

            // 3. Draw Flowing River
            scenery.riverSegments.forEach(r => {
                ctx.fillStyle = '#0284c7';
                ctx.fillRect(r.x, r.y, r.w, r.h);
                ctx.fillStyle = 'rgba(255, 255, 255, 0.25)';
                ctx.fillRect(r.x + 25, r.y + 20, 12, r.h - 40);
            });

            // 4. Draw Forest Trees
            scenery.trees.forEach(t => {
                ctx.fillStyle = '#451a03'; // Tree trunk
                ctx.fillRect(t.x + t.size / 2 - 6, t.y, 12, 35);
                ctx.fillStyle = '#166534'; // Foliage
                ctx.beginPath();
                ctx.arc(t.x + t.size / 2, t.y, t.size / 2, 0, Math.PI * 2);
                ctx.fill();
            });

            // 5. Draw Flowers
            scenery.flowers.forEach(f => {
                ctx.fillStyle = f.color;
                ctx.beginPath();
                ctx.arc(f.x, f.y, 6, 0, Math.PI * 2);
                ctx.fill();
                ctx.fillStyle = '#facc15';
                ctx.beginPath();
                ctx.arc(f.x, f.y, 2, 0, Math.PI * 2);
                ctx.fill();
            });

            // 6. Draw Platforms
            platforms.forEach(p => {
                ctx.fillStyle = p.type === 'ground' ? '#14532d' : '#334155';
                ctx.beginPath();
                if (ctx.roundRect) {
                    ctx.roundRect(p.x, p.y, p.width, p.height, 10);
                } else {
                    ctx.rect(p.x, p.y, p.width, p.height);
                }
                ctx.fill();

                ctx.fillStyle = p.type === 'ground' ? '#22c55e' : '#38bdf8';
                ctx.fillRect(p.x, p.y, p.width, 4);
            });

            // 7. Draw Flying Creatures (Birds, Bats, Butterflies, Dragonflies)
            scenery.creatures.forEach(c => {
                ctx.save();
                ctx.translate(c.x, c.y + Math.sin(c.wingPhase) * 15);
                if (c.direction < 0) ctx.scale(-1, 1);

                if (c.type === 'bird') {
                    // Flying Bird
                    ctx.strokeStyle = '#cbd5e1';
                    ctx.lineWidth = 2.5;
                    ctx.beginPath();
                    let wingY = Math.sin(c.wingPhase * 2) * 10;
                    ctx.moveTo(-15, wingY);
                    ctx.quadraticCurveTo(0, -10, 15, wingY);
                    ctx.stroke();
                } else if (c.type === 'bat') {
                    // Flying Bat
                    ctx.fillStyle = '#64748b';
                    ctx.beginPath();
                    ctx.ellipse(0, 0, 8, 5, 0, 0, Math.PI * 2);
                    ctx.fill();
                    // Wings
                    ctx.beginPath();
                    let bWing = Math.sin(c.wingPhase * 3) * 12;
                    ctx.moveTo(0, 0);
                    ctx.lineTo(-18, -10 + bWing);
                    ctx.lineTo(-10, 5);
                    ctx.lineTo(0, 0);
                    ctx.lineTo(18, -10 + bWing);
                    ctx.lineTo(10, 5);
                    ctx.fill();
                } else if (c.type === 'butterfly') {
                    // Fluttering Butterfly
                    let fScale = Math.abs(Math.sin(c.wingPhase * 4));
                    ctx.fillStyle = '#f43f5e';
                    ctx.beginPath();
                    ctx.ellipse(-8, -4, 8 * fScale, 10, -0.3, 0, Math.PI * 2);
                    ctx.ellipse(8, -4, 8 * fScale, 10, 0.3, 0, Math.PI * 2);
                    ctx.fill();
                    ctx.fillStyle = '#fef08a';
                    ctx.fillRect(-2, -6, 4, 12);
                } else if (c.type === 'dragonfly') {
                    // Soaring Dragonfly
                    ctx.fillStyle = '#38bdf8';
                    ctx.fillRect(-3, -12, 6, 24);
                    // Wings
                    ctx.fillStyle = 'rgba(255, 255, 255, 0.7)';
                    let dWing = Math.sin(c.wingPhase * 5) * 6;
                    ctx.beginPath();
                    ctx.ellipse(-14, -2, 14, 3, 0.2 + dWing * 0.05, 0, Math.PI * 2);
                    ctx.ellipse(14, -2, 14, 3, -0.2 - dWing * 0.05, 0, Math.PI * 2);
                    ctx.fill();
                }
                ctx.restore();
            });

            // 8. Draw Scroll Quiz Items
            const time = performance.now() / 1000;
            scrollItems.forEach(item => {
                if (!item.collected) {
                    const floatY = item.y + Math.sin(time * 3 + item.floatOffset) * 6;
                    
                    ctx.shadowColor = '#f59e0b';
                    ctx.shadowBlur = 18;

                    ctx.fillStyle = '#fbbf24';
                    ctx.beginPath();
                    ctx.arc(item.x + item.width / 2, floatY + item.height / 2, 22, 0, Math.PI * 2);
                    ctx.fill();

                    ctx.shadowBlur = 0;

                    ctx.font = '20px sans-serif';
                    ctx.textAlign = 'center';
                    ctx.textBaseline = 'middle';
                    ctx.fillText('📜', item.x + item.width / 2, floatY + item.height / 2);

                    ctx.fillStyle = '#fef08a';
                    ctx.font = 'bold 11px Poppins';
                    ctx.fillText(`Q${item.id} ⭐`, item.x + item.width / 2, floatY - 16);
                }
            });

            // 9. Draw Ninja Player Character
            ctx.save();
            ctx.translate(player.x + player.width / 2, player.y + player.height / 2);
            if (!player.facingRight) ctx.scale(-1, 1);

            // Ninja Outfit & Red Scarf
            ctx.fillStyle = '#f43f5e';
            ctx.fillRect(-player.width / 2, -player.height / 2 + 10, player.width, player.height - 10);

            ctx.fillStyle = '#1e293b';
            ctx.fillRect(-player.width / 2 + 4, -player.height / 2 + 14, player.width - 8, player.height - 18);

            // Ninja Head
            ctx.fillStyle = '#334155';
            ctx.beginPath();
            ctx.arc(0, -player.height / 2 + 10, 14, 0, Math.PI * 2);
            ctx.fill();

            // Headband
            ctx.fillStyle = '#f59e0b';
            ctx.fillRect(-14, -player.height / 2 + 6, 28, 6);

            // Flowing Scarf
            ctx.fillStyle = '#f43f5e';
            ctx.beginPath();
            ctx.moveTo(-10, -player.height / 2 + 8);
            ctx.quadraticCurveTo(-25, -5, -32, 5);
            ctx.quadraticCurveTo(-18, 2, -10, -2);
            ctx.fill();

            ctx.restore();

            ctx.restore();
        }

        function gameLoop(timestamp) {
            if (gameState !== 'PLAYING') return;

            const dt = (timestamp - lastTime) / 1000;
            lastTime = timestamp;

            update(Math.min(dt, 0.1));
            draw();

            requestAnimationFrame(gameLoop);
        }

        resizeCanvas();
    </script>
</body>
</html>
