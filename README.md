<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Perpustakaan Digital</title>
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
            color: #333;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
        }

        h1 {
            text-align: center;
            color: white;
            margin-bottom: 40px;
            font-size: 2.5em;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
        }

        .books-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 25px;
            margin-bottom: 40px;
        }

        .book-card {
            background: white;
            border-radius: 15px;
            padding: 20px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.2);
            transition: transform 0.3s ease, box-shadow 0.3s ease;
            cursor: pointer;
        }

        .book-card:hover {
            transform: translateY(-10px);
            box-shadow: 0 20px 40px rgba(0,0,0,0.3);
        }

        .book-cover {
            width: 100%;
            height: 200px;
            background: linear-gradient(45deg, #ff6b6b, #4ecdc4);
            border-radius: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 3em;
            color: white;
            margin-bottom: 15px;
            font-weight: bold;
        }

        .book-title {
            font-size: 1.3em;
            font-weight: bold;
            margin-bottom: 10px;
            color: #2c3e50;
        }

        .book-author {
            color: #7f8c8d;
            margin-bottom: 15px;
            font-style: italic;
        }

        .book-description {
            color: #555;
            line-height: 1.5;
            margin-bottom: 20px;
            font-size: 0.95em;
        }

        .read-btn {
            width: 100%;
            padding: 12px;
            background: linear-gradient(45deg, #667eea, #764ba2);
            color: white;
            border: none;
            border-radius: 25px;
            font-size: 1.1em;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .read-btn:hover {
            transform: scale(1.05);
            box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
        }

        /* Halaman PDF Viewer */
        .pdf-viewer {
            display: none;
            background: white;
            min-height: 100vh;
            padding: 20px;
        }

        .pdf-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
            padding-bottom: 15px;
            border-bottom: 2px solid #eee;
        }

        .back-btn {
            background: #e74c3c;
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 20px;
            cursor: pointer;
            font-weight: bold;
            transition: background 0.3s ease;
        }

        .back-btn:hover {
            background: #c0392b;
        }

        .pdf-container {
            border: 1px solid #ddd;
            border-radius: 10px;
            height: 80vh;
            overflow: hidden;
            box-shadow: 0 10px 30px rgba(0,0,0,0.1);
        }

        .pdf-container iframe {
            width: 100%;
            height: 100%;
            border: none;
        }

        /* Loading Animation */
        .loading {
            display: none;
            text-align: center;
            padding: 40px;
            color: white;
            font-size: 1.2em;
        }

        .spinner {
            border: 4px solid rgba(255,255,255,0.3);
            border-top: 4px solid white;
            border-radius: 50%;
            width: 40px;
            height: 40px;
            animation: spin 1s linear infinite;
            margin: 0 auto 20px;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        /* Responsive */
        @media (max-width: 768px) {
            .books-grid {
                grid-template-columns: 1fr;
                gap: 20px;
            }
            
            h1 {
                font-size: 2em;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>📚 Perpustakaan Digital</h1>
        
        <div class="loading" id="loading">
            <div class="spinner"></div>
            Memuat buku...
        </div>

        <div class="books-grid" id="booksGrid">
            <!-- Buku akan dimuat secara dinamis oleh JavaScript -->
        </div>
    </div>

    <!-- Halaman PDF Viewer -->
    <div class="pdf-viewer" id="pdfViewer">
        <div class="container">
            <div class="pdf-header">
                <button class="back-btn" onclick="showHome()">← Kembali ke Perpustakaan</button>
                <h2 id="currentBookTitle"></h2>
                <div></div>
            </div>
            <div class="pdf-container">
                <iframe id="pdfFrame" src=""></iframe>
            </div>
        </div>
    </div>

    <script>
        // Data 10 buku dengan link PDF (contoh menggunakan PDF publik/sample)
        const books = [
            {
                id: 1,
                title: "Algoritma dan Struktur Data",
                author: "John Doe",
                description: "Buku lengkap tentang algoritma dasar, struktur data, dan analisis kompleksitas.",
                cover: "📊",
                pdfUrl: "https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf"
            },
            {
                id: 2,
                title: "Pemrograman Python Modern",
                author: "Jane Smith",
                description: "Panduan lengkap Python dari dasar hingga aplikasi web dan machine learning.",
                cover: "🐍",
                pdfUrl: "https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf"
            },
            {
                id: 3,
                title: "JavaScript Esensial",
                author: "Mike Johnson",
                description: "Memahami JavaScript modern dengan ES6+, async/await, dan DOM manipulation.",
                cover: "⚡",
                pdfUrl: "https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf"
            },
            {
                id: 4,
                title: "Desain Sistem Database",
                author: "Sarah Wilson",
                description: "Prinsip desain database relasional, normalisasi, dan optimasi query SQL.",
                cover: "🗄️",
                pdfUrl: "https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf"
            },
            {
                id: 5,
                title: "Machine Learning Dasar",
                author: "David Brown",
                description: "Pengantar machine learning, supervised learning, dan neural networks.",
                cover: "🤖",
                pdfUrl: "https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf"
            },
            {
                id: 6,
                title: "Web Development Fullstack",
                author: "Lisa Chen",
                description: "Membangun aplikasi web lengkap dari frontend hingga backend.",
                cover: "🌐",
                pdfUrl: "https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf"
            },
            {
                id: 7,
                title: "Cybersecurity Essentials",
                author: "Robert Taylor",
                description: "Dasar-dasar keamanan siber, enkripsi, dan perlindungan data.",
                cover: "🔒",
                pdfUrl: "https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf"
            },
            {
                id: 8,
                title: "DevOps dan Cloud Computing",
                author: "Maria Garcia",
                description: "Praktik DevOps, CI/CD pipeline, dan deployment di cloud.",
                cover: "☁️",
                pdfUrl: "https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf"
            },
            {
                id: 9,
                title: "Mobile App Development",
                author: "Ahmed Khan",
                description: "Membangun aplikasi mobile native dan cross-platform.",
                cover: "📱",
                pdfUrl: "https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf"
            },
            {
                id: 10,
                title: "Data Science dengan R",
                author: "Emily Davis",
                description: "Analisis data dan visualisasi menggunakan bahasa pemrograman R.",
                cover: "📈",
                pdfUrl: "https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf"
            }
        ];

        // Fungsi untuk menampilkan halaman utama
        function showHome() {
            document.getElementById('booksGrid').style.display = 'grid';
            document.getElementById('pdfViewer').style.display = 'none';
            document.body.scrollTop = 0;
        }

        // Fungsi untuk membuka buku PDF
        function openBook(book) {
            document.getElementById('booksGrid').style.display = 'none';
            document.getElementById('pdfViewer').style.display = 'block';
            document.getElementById('currentBookTitle').textContent = book.title;
            document.getElementById('pdfFrame').src = book.pdfUrl;
        }

        // Fungsi untuk merender buku ke halaman
        function renderBooks() {
            const booksGrid = document.getElementById('booksGrid');
            booksGrid.innerHTML = '';

            books.forEach(book => {
                const bookCard = document.createElement('div');
                bookCard.className = 'book-card';
                bookCard.innerHTML = `
                    <div class="book-cover">${book.cover}</div>
                    <div class="book-title">${book.title}</div>
                    <div class="book-author">by ${book.author}</div>
                    <div class="book-description">${book.description}</div>
                    <button class="read-btn" onclick="openBook(${JSON.stringify(book).replace(/"/g, '&quot;')})">
                        📖 Read the Book
                    </button>
                `;
                booksGrid.appendChild(bookCard);
            });
        }

        // Inisialisasi aplikasi
        window.onload = function() {
            document.getElementById('loading').style.display = 'block';
            
            // Simulasi loading
            setTimeout(() => {
                document.getElementById('loading').style.display = 'none';
                renderBooks();
            }, 1500);
        };

        // Keyboard shortcut untuk kembali (Escape)
        document.addEventListener('keydown', function(e) {
            if (e.key === 'Escape') {
                showHome();
            }
        });
    </script>
</body>
</html>
