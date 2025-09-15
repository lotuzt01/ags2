<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Aplikasi Akuntansi Perdagangan Lanjutan</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- SheetJS (Library for Excel file generation) -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;600;700&display=swap');
        @import url('https://fonts.googleapis.com/css2?family=Fredoka+One&display=swap');
        
        body {
            font-family: 'Poppins', sans-serif;
            background: linear-gradient(135deg, #f0f4f8 0%, #d9e2ec 100%);
            color: #1a202c;
            min-height: 100vh;
        }
        .animated-title {
            font-family: 'Fredoka One', cursive;
            text-shadow: 
                -2px -2px 0 #fff,  
                2px -2px 0 #fff,
                -2px 2px 0 #fff,
                2px 2px 0 #fff,
                2px 2px 8px rgba(0,0,0,0.2);
        }
        .sidebar {
            background: linear-gradient(to bottom, #3b82f6, #1d4ed8);
            color: white;
        }
        .sidebar-item {
            display: flex;
            align-items: center;
            padding: 1rem;
            cursor: pointer;
            border-left: 4px solid transparent;
            transition: all 0.2s ease-in-out;
            border-radius: 0.5rem;
            margin: 0.5rem 0.5rem;
        }
        .sidebar-item.active {
            background-color: rgba(255, 255, 255, 0.2);
            border-left-color: #fcd34d;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
        }
        .sidebar-item:hover:not(.active) {
            background-color: rgba(255, 255, 255, 0.1);
        }
        .card {
            background-color: white;
            border-radius: 1.5rem;
            box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
            padding: 2rem;
        }
        .button-primary {
            background: linear-gradient(to right, #4ade80, #16a34a);
            color: white;
            border: none;
            border-radius: 9999px;
            padding: 0.75rem 1.5rem;
            font-weight: 600;
            transition: transform 0.2s ease-in-out;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }
        .button-primary:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 8px rgba(0, 0, 0, 0.15);
        }
        .input-field {
            width: 100%;
            padding: 0.75rem;
            border-radius: 0.75rem;
            border: 1px solid #e2e8f0;
            transition: border-color 0.2s ease-in-out, box-shadow 0.2s ease-in-out;
        }
        .input-field:focus {
            outline: none;
            border-color: #3b82f6;
            box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.2);
        }
        .tab-content {
            display: none;
        }
        .tab-content.active {
            display: block;
        }
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.5);
            justify-content: center;
            align-items: center;
            z-index: 1000;
        }
        .modal-content {
            background-color: white;
            padding: 2rem;
            border-radius: 1rem;
            box-shadow: 0 10px 20px rgba(0,0,0,0.2);
            max-width: 90%;
            max-height: 90%;
            overflow-y: auto;
        }
        .modal-close-btn {
            position: absolute;
            top: 1rem;
            right: 1rem;
            background: none;
            border: none;
            font-size: 1.5rem;
            color: #4a5568;
            cursor: pointer;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            border-radius: 1rem;
            overflow: hidden;
        }
        th, td {
            padding: 1rem;
            text-align: left;
            border-bottom: 1px solid #e2e8f0;
        }
        th {
            background-color: #f7fafc;
            color: #4a5568;
            font-weight: 600;
            text-transform: uppercase;
        }
        tr:last-child td {
            border-bottom: none;
        }
        .summary-card {
            background: #fff;
            border-radius: 1.5rem;
            padding: 1.5rem;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }
        #login-container {
            display: flex;
            justify-content: center;
            align-items: center;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(135deg, #3b82f6 0%, #1d4ed8 100%);
            z-index: 2000;
        }
        #login-form-card {
            background: white;
            border-radius: 1.5rem;
            padding: 3rem;
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.2);
            max-width: 350px;
            width: 90%;
        }
        .error-message {
            color: #ef4444;
            text-align: center;
            margin-top: 1rem;
        }
    </style>
</head>
<body class="bg-gray-100 flex flex-col min-h-screen">

    <!-- Login Container -->
    <div id="login-container">
        <div id="login-form-card" class="text-center">
            <h2 class="text-4xl font-bold mb-6 text-gray-800">Login</h2>
            <form id="login-form" class="space-y-6">
                <div>
                    <label for="username" class="block text-left text-gray-700 font-semibold mb-2">Username</label>
                    <input type="text" id="username" name="username" class="input-field" placeholder="LOTUZT" required>
                </div>
                <div>
                    <label for="password" class="block text-left text-gray-700 font-semibold mb-2">Password</label>
                    <input type="password" id="password" name="password" class="input-field" placeholder="12345" required>
                </div>
                <button type="submit" class="button-primary w-full py-3">Masuk</button>
            </form>
            <p id="login-error-message" class="error-message"></p>
            <div id="debug-info" class="text-sm text-gray-500 mt-4 text-left p-2 bg-gray-100 rounded">
                <!-- Debug information will be inserted here -->
            </div>
        </div>
    </div>

    <!-- Main App Container -->
    <div id="app-container" style="display: none;" class="flex-1 flex flex-col">
        <!-- Navbar -->
        <header class="bg-white shadow-md py-4 px-6 flex items-center justify-between sticky top-0 z-50">
            <div class="flex items-center space-x-4">
                <h1 class="text-3xl font-bold animated-title text-transparent bg-clip-text bg-gradient-to-r from-blue-500 to-green-500">Aplikasi Akuntansi</h1>
                <span class="text-sm text-gray-500 hidden md:block">by Your Company</span>
            </div>
            <div class="flex items-center space-x-4">
                <button id="download-report-btn" class="bg-gray-200 hover:bg-gray-300 text-gray-800 font-semibold py-2 px-4 rounded-full transition-colors duration-200">
                    Unduh Laporan
                </button>
                <div class="relative inline-block text-gray-800">
                    <select id="report-month" class="bg-gray-200 hover:bg-gray-300 py-2 px-4 rounded-full appearance-none pr-8 cursor-pointer transition-colors duration-200">
                        <option value="">Semua Bulan</option>
                        <option value="1">Januari</option>
                        <option value="2">Februari</option>
                        <option value="3">Maret</option>
                        <option value="4">April</option>
                        <option value="5">Mei</option>
                        <option value="6">Juni</option>
                        <option value="7">Juli</option>
                        <option value="8">Agustus</option>
                        <option value="9">September</option>
                        <option value="10">Oktober</option>
                        <option value="11">November</option>
                        <option value="12">Desember</option>
                    </select>
                    <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700">
                        <svg class="fill-current h-4 w-4" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20"><path d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z"/></svg>
                    </div>
                </div>
                <div class="relative inline-block text-gray-800">
                    <select id="report-year" class="bg-gray-200 hover:bg-gray-300 py-2 px-4 rounded-full appearance-none pr-8 cursor-pointer transition-colors duration-200">
                    </select>
                    <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700">
                        <svg class="fill-current h-4 w-4" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20"><path d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z"/></svg>
                    </div>
                </div>
                <button id="logout-btn" class="bg-red-500 hover:bg-red-600 text-white font-semibold py-2 px-4 rounded-full transition-colors duration-200">
                    Keluar
                </button>
            </div>
        </header>

        <div class="flex-1 flex overflow-hidden">
            <!-- Sidebar -->
            <aside class="sidebar w-64 p-4 shadow-xl overflow-y-auto">
                <h2 class="text-2xl font-bold mb-6 text-white text-center">Menu</h2>
                <nav>
                    <div class="sidebar-item active" onclick="showForm('summary-section')">
                        <svg class="w-6 h-6 mr-3" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 12l2-2m0 0l7-7 7 7M5 10v10a1 1 0 001 1h3m10-11l2 2m-2-2v10a1 1 0 01-1 1h-3m-6 0a1 1 0 001-1v-4a1 1 0 011-1h2a1 1 0 011 1v4a1 1 0 001 1m-6 0h6"></path></svg>
                        <span>Ringkasan</span>
                    </div>
                    <div class="sidebar-item" onclick="showForm('purchase-section')">
                        <svg class="w-6 h-6 mr-3" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M16 11V7a4 4 0 00-4-4H4a4 4 0 00-4 4v8a4 4 0 004 4h8a4 4 0 004-4v-4m-6 4h6m-6-4h6m6 0a2 2 0 11-4 0 2 2 0 014 0zm1-4a2 2 0 11-4 0 2 2 0 014 0z"></path></svg>
                        <span>Pembelian</span>
                    </div>
                    <div class="sidebar-item" onclick="showForm('sale-section')">
                        <svg class="w-6 h-6 mr-3" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 10h18M7 15h1m4 0h1m-7 4h12a3 3 0 003-3V8a3 3 0 00-3-3H6a3 3 0 00-3 3v8a3 3 0 003 3z"></path></svg>
                        <span>Penjualan</span>
                    </div>
                    <div class="sidebar-item" onclick="showForm('ramp-report-section')">
                        <svg class="w-6 h-6 mr-3" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 17v-4m0 0l-2 2m2-2l2 2m-2-2V7a2 2 0 012-2h2a2 2 0 012 2v10m-6 0h6"></path></svg>
                        <span>Laporan RAMP</span>
                    </div>
                    <div class="sidebar-item" onclick="showForm('other-transaction-section')">
                        <svg class="w-6 h-6 mr-3" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8c-1.66 0-3 1.34-3 3v2a3 3 0 006 0v-2c0-1.66-1.34-3-3-3zM18 10h-2.26a1.74 1.74 0 00-1.74 1.74v2.52a1.74 1.74 0 001.74 1.74H18v-6.5zm-12 0H6.26a1.74 1.74 0 011.74 1.74v2.52A1.74 1.74 0 016.26 18H6v-6.5z"></path></svg>
                        <span>Transaksi Lain</span>
                    </div>
                    <div class="sidebar-item" onclick="showForm('asset-section')">
                        <svg class="w-6 h-6 mr-3" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 21h-8m-4-6V8m-3 3V6a2 2 0 012-2h12a2 2 0 012 2v8a2 2 0 01-2 2h-6m-6-3a2 2 0 00-2 2v4a2 2 0 002 2h6a2 2 0 002-2v-4a2 2 0 00-2-2h-6z"></path></svg>
                        <span>Aset</span>
                    </div>
                    <div class="sidebar-item" onclick="showForm('capital-and-debt-section')">
                        <svg class="w-6 h-6 mr-3" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 9v6m-4-6v6m-4-6v6M3 7h18a2 2 0 012 2v6a2 2 0 01-2 2H3a2 2 0 01-2-2v-6a2 2 0 012-2z"></path></svg>
                        <span>Modal & Hutang</span>
                    </div>
                    <div class="sidebar-item" onclick="showForm('financial-analysis-section')">
                        <svg class="w-6 h-6 mr-3" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 19V6l3-3 3 3v13M12 6a9 9 0 00-9 9m9-9a9 9 0 019 9m-9 0a9 9 0 00-9-9"></path></svg>
                        <span>Analisis Keuangan</span>
                    </div>
                </nav>
                <div class="mt-auto pt-6 text-center">
                    <button id="clear-data-btn" class="bg-red-500 hover:bg-red-600 text-white font-semibold py-2 px-4 rounded-full transition-colors duration-200">
                        Bersihkan Semua Data
                    </button>
                </div>
            </aside>

            <!-- Main content -->
            <main class="flex-1 p-8 overflow-y-auto">
                <!-- Summary Section -->
                <div id="summary-section" class="tab-content active transition-opacity duration-500">
                    <h2 class="text-3xl font-bold text-gray-800 mb-6 text-center">Ringkasan Keuangan</h2>
                    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                        <div class="summary-card text-center">
                            <h3 class="text-xl font-semibold text-gray-600 mb-2">Total Penjualan</h3>
                            <p id="total-sales" class="text-3xl font-bold text-green-600">Rp 0</p>
                        </div>
                        <div class="summary-card text-center">
                            <h3 class="text-xl font-semibold text-gray-600 mb-2">Total Pembelian</h3>
                            <p id="total-purchases" class="text-3xl font-bold text-red-600">Rp 0</p>
                        </div>
                        <div class="summary-card text-center">
                            <h3 class="text-xl font-semibold text-gray-600 mb-2">Total Laba/Rugi</h3>
                            <p id="total-profit" class="text-3xl font-bold text-blue-600">Rp 0</p>
                        </div>
                        <div class="summary-card text-center">
                            <h3 class="text-xl font-semibold text-gray-600 mb-2">Total Aset</h3>
                            <p id="total-assets" class="text-3xl font-bold text-purple-600">Rp 0</p>
                        </div>
                        <div class="summary-card text-center">
                            <h3 class="text-xl font-semibold text-gray-600 mb-2">Total Hutang</h3>
                            <p id="total-debts" class="text-3xl font-bold text-orange-600">Rp 0</p>
                        </div>
                        <div class="summary-card text-center">
                            <h3 class="text-xl font-semibold text-gray-600 mb-2">Total Modal</h3>
                            <p id="total-capital" class="text-3xl font-bold text-teal-600">Rp 0</p>
                        </div>
                    </div>
                    <div class="mt-8 p-6 card">
                        <h3 class="text-2xl font-bold mb-4 text-center">Detail Transaksi</h3>
                        <table class="w-full text-left table-auto">
                            <thead>
                                <tr>
                                    <th class="py-2 px-4">Tanggal</th>
                                    <th class="py-2 px-4">Tipe</th>
                                    <th class="py-2 px-4">Deskripsi</th>
                                    <th class="py-2 px-4">Jumlah</th>
                                </tr>
                            </thead>
                            <tbody id="transaction-list">
                                <!-- Transactions will be inserted here by JavaScript -->
                            </tbody>
                        </table>
                    </div>
                </div>

                <!-- Purchase Section -->
                <div id="purchase-section" class="tab-content transition-opacity duration-500 card">
                    <h2 class="text-3xl font-bold text-gray-800 mb-6 text-center">Transaksi Pembelian</h2>
                    <form id="purchase-form" class="space-y-6">
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                            <div>
                                <label for="purchase-date" class="block text-gray-700 font-semibold mb-2">Tanggal</label>
                                <input type="date" id="purchase-date" name="date" class="input-field" required>
                            </div>
                            <div>
                                <label for="purchase-description" class="block text-gray-700 font-semibold mb-2">Deskripsi</label>
                                <input type="text" id="purchase-description" name="description" class="input-field" placeholder="Pembelian bahan baku" required>
                            </div>
                        </div>
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                            <div>
                                <label for="purchase-gross" class="block text-gray-700 font-semibold mb-2">Tonase Gross (Kg)</label>
                                <input type="number" id="purchase-gross" name="gross" class="input-field" placeholder="10000" required>
                            </div>
                            <div>
                                <label for="purchase-tare" class="block text-gray-700 font-semibold mb-2">Tonase Tare (Kg)</label>
                                <input type="number" id="purchase-tare" name="tare" class="input-field" placeholder="2000" required>
                            </div>
                        </div>
                        <div>
                            <label for="purchase-deduction-percent" class="block text-gray-700 font-semibold mb-2">Potongan (%)</label>
                            <input type="number" id="purchase-deduction-percent" name="deductionPercent" class="input-field" step="0.01" placeholder="2.5" required>
                        </div>
                        <div>
                            <label for="purchase-price" class="block text-gray-700 font-semibold mb-2">Harga per Kg (Rp)</label>
                            <input type="number" id="purchase-price" name="price" class="input-field" placeholder="5000" required>
                        </div>
                        <div>
                            <label for="purchase-amount" class="block text-gray-700 font-semibold mb-2">Total Biaya (Rp)</label>
                            <input type="text" id="purchase-amount" name="amount" class="input-field bg-gray-200" readonly>
                        </div>
                        <button type="submit" class="button-primary w-full py-3">Tambahkan Pembelian</button>
                    </form>
                </div>

                <!-- Sale Section -->
                <div id="sale-section" class="tab-content transition-opacity duration-500 card">
                    <h2 class="text-3xl font-bold text-gray-800 mb-6 text-center">Transaksi Penjualan</h2>
                    <form id="sale-form" class="space-y-6">
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                            <div>
                                <label for="sale-date" class="block text-gray-700 font-semibold mb-2">Tanggal</label>
                                <input type="date" id="sale-date" name="date" class="input-field" required>
                            </div>
                            <div>
                                <label for="sale-description" class="block text-gray-700 font-semibold mb-2">Deskripsi</label>
                                <input type="text" id="sale-description" name="description" class="input-field" placeholder="Penjualan produk jadi" required>
                            </div>
                        </div>
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                            <div>
                                <label for="sale-gross" class="block text-gray-700 font-semibold mb-2">Tonase Gross (Kg)</label>
                                <input type="number" id="sale-gross" name="gross" class="input-field" placeholder="10000" required>
                            </div>
                            <div>
                                <label for="sale-tare" class="block text-gray-700 font-semibold mb-2">Tonase Tare (Kg)</label>
                                <input type="number" id="sale-tare" name="tare" class="input-field" placeholder="2000" required>
                            </div>
                        </div>
                        <div>
                            <label for="sale-deduction-percent" class="block text-gray-700 font-semibold mb-2">Potongan (%)</label>
                            <input type="number" id="sale-deduction-percent" name="deductionPercent" class="input-field" step="0.01" placeholder="2.5" required>
                        </div>
                        <div>
                            <label for="sale-price" class="block text-gray-700 font-semibold mb-2">Harga per Kg (Rp)</label>
                            <input type="number" id="sale-price" name="price" class="input-field" placeholder="15000" required>
                        </div>
                        <div>
                            <label for="sale-amount" class="block text-gray-700 font-semibold mb-2">Total Biaya (Rp)</label>
                            <input type="text" id="sale-amount" name="amount" class="input-field bg-gray-200" readonly>
                        </div>
                        <button type="submit" class="button-primary w-full py-3">Tambahkan Penjualan</button>
                    </form>
                </div>

                <!-- RAMP Report Section - NEW -->
                <div id="ramp-report-section" class="tab-content transition-opacity duration-500 card">
                    <h2 class="text-3xl font-bold text-gray-800 mb-6 text-center">Laporan Penjualan RAMP</h2>
                    
                    <!-- Form Input Transaksi -->
                    <form id="ramp-form" class="space-y-6 p-6 bg-gray-50 rounded-lg shadow-inner mb-8">
                        <h3 class="text-2xl font-bold text-gray-700 mb-4">Input Transaksi Baru</h3>
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                            <div>
                                <label for="ramp-date" class="block text-gray-700 font-semibold mb-2">Tanggal</label>
                                <input type="date" id="ramp-date" name="date" class="input-field" required>
                            </div>
                            <div>
                                <label for="ramp-invoice" class="block text-gray-700 font-semibold mb-2">Nomor Invoice</label>
                                <input type="text" id="ramp-invoice" name="invoice" class="input-field" placeholder="INV-2024-001" required>
                            </div>
                            <div>
                                <label for="ramp-customer" class="block text-gray-700 font-semibold mb-2">Nama Customer/Pabrik</label>
                                <input type="text" id="ramp-customer" name="customer" class="input-field" placeholder="PT Maju Sejahtera" required>
                            </div>
                            <div>
                                <label for="ramp-gross" class="block text-gray-700 font-semibold mb-2">Tonase Gross (Kg)</label>
                                <input type="number" id="ramp-gross" name="gross" class="input-field" placeholder="10000" required>
                            </div>
                            <div>
                                <label for="ramp-tare" class="block text-gray-700 font-semibold mb-2">Tonase Tare (Kg)</label>
                                <input type="number" id="ramp-tare" name="tare" class="input-field" placeholder="2000" required>
                            </div>
                        </div>
                        <div>
                            <label for="ramp-deduction-percent" class="block text-gray-700 font-semibold mb-2">Potongan (%)</label>
                            <input type="number" id="ramp-deduction-percent" name="deductionPercent" class="input-field" step="0.01" placeholder="2.5" required>
                        </div>
                        <div>
                            <label for="ramp-price" class="block text-gray-700 font-semibold mb-2">Harga Jual per Ton (Rp)</label>
                            <input type="number" id="ramp-price" name="price" class="input-field" placeholder="500000" required>
                        </div>
                        <div>
                            <label for="ramp-ppn" class="block text-gray-700 font-semibold mb-2">PPN (%)</label>
                            <input type="number" id="ramp-ppn" name="ppn" class="input-field" step="0.01" placeholder="11" required>
                        </div>
                        <div>
                            <label for="ramp-pph" class="block text-gray-700 font-semibold mb-2">PPh (%)</label>
                            <input type="number" id="ramp-pph" name="pph" class="input-field" step="0.01" placeholder="2" required>
                        </div>
                        <button type="submit" class="button-primary w-full py-3">Tambahkan Transaksi RAMP</button>
                    </form>

                    <!-- Tabel Rekap Transaksi -->
                    <div class="p-6 card">
                        <h3 class="text-2xl font-bold mb-4 text-center">Rekap Transaksi RAMP</h3>
                        <div class="overflow-x-auto">
                            <table class="w-full text-sm">
                                <thead>
                                    <tr>
                                        <th class="py-2 px-2">Tanggal</th>
                                        <th class="py-2 px-2">Invoice</th>
                                        <th class="py-2 px-2">Customer</th>
                                        <th class="py-2 px-2">Gross (Kg)</th>
                                        <th class="py-2 px-2">Tare (Kg)</th>
                                        <th class="py-2 px-2">Netto 1 (Kg)</th>
                                        <th class="py-2 px-2">Potongan (%)</th>
                                        <th class="py-2 px-2">Netto 2 (Kg)</th>
                                        <th class="py-2 px-2">Harga per Ton</th>
                                        <th class="py-2 px-2">Total Harga</th>
                                        <th class="py-2 px-2">PPN (%)</th>
                                        <th class="py-2 px-2">PPh (%)</th>
                                        <th class="py-2 px-2">Total Tagihan</th>
                                    </tr>
                                </thead>
                                <tbody id="ramp-transaction-list">
                                    <!-- Data akan diisi oleh JavaScript -->
                                </tbody>
                            </table>
                        </div>
                    </div>
                </div>

                <!-- Other Transaction Section -->
                <div id="other-transaction-section" class="tab-content transition-opacity duration-500 card">
                    <h2 class="text-3xl font-bold text-gray-800 mb-6 text-center">Transaksi Lain-lain</h2>
                    <form id="other-transaction-form" class="space-y-6">
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                            <div>
                                <label for="other-transaction-date" class="block text-gray-700 font-semibold mb-2">Tanggal</label>
                                <input type="date" id="other-transaction-date" name="date" class="input-field" required>
                            </div>
                            <div>
                                <label for="other-transaction-description" class="block text-gray-700 font-semibold mb-2">Deskripsi</label>
                                <input type="text" id="other-transaction-description" name="description" class="input-field" placeholder="Biaya operasional, gaji, dll." required>
                            </div>
                        </div>
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                            <div>
                                <label for="other-transaction-type" class="block text-gray-700 font-semibold mb-2">Tipe Transaksi</label>
                                <select id="other-transaction-type" name="type" class="input-field" required>
                                    <option value="Pengeluaran">Pengeluaran</option>
                                    <option value="Pemasukan">Pemasukan</option>
                                </select>
                            </div>
                            <div>
                                <label for="other-transaction-amount" class="block text-gray-700 font-semibold mb-2">Jumlah (Rp)</label>
                                <input type="number" id="other-transaction-amount" name="amount" class="input-field" placeholder="500000" required>
                            </div>
                        </div>
                        <button type="submit" class="button-primary w-full py-3">Tambahkan Transaksi</button>
                    </form>
                </div>

                <!-- Asset Section -->
                <div id="asset-section" class="tab-content transition-opacity duration-500 card">
                    <h2 class="text-3xl font-bold text-gray-800 mb-6 text-center">Manajemen Aset</h2>
                    <form id="asset-form" class="space-y-6">
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                            <div>
                                <label for="asset-name" class="block text-gray-700 font-semibold mb-2">Nama Aset</label>
                                <input type="text" id="asset-name" name="name" class="input-field" placeholder="Kendaraan, gedung" required>
                            </div>
                            <div>
                                <label for="asset-value" class="block text-gray-700 font-semibold mb-2">Nilai Aset (Rp)</label>
                                <input type="number" id="asset-value" name="value" class="input-field" placeholder="50000000" required>
                            </div>
                        </div>
                        <button type="submit" class="button-primary w-full py-3">Tambahkan Aset</button>
                    </form>
                </div>

                <!-- Capital and Debt Section -->
                <div id="capital-and-debt-section" class="tab-content transition-opacity duration-500 card">
                    <h2 class="text-3xl font-bold text-gray-800 mb-6 text-center">Modal dan Hutang</h2>
                    <form id="capital-and-debt-form" class="space-y-6">
                        <div>
                            <label for="capital" class="block text-gray-700 font-semibold mb-2">Total Modal (Rp)</label>
                            <input type="number" id="capital" name="capital" class="input-field" placeholder="100000000" required>
                        </div>
                        <div>
                            <label for="debt" class="block text-gray-700 font-semibold mb-2">Total Hutang (Rp)</label>
                            <input type="number" id="debt" name="debt" class="input-field" placeholder="25000000" required>
                        </div>
                        <button type="submit" class="button-primary w-full py-3">Simpan Data</button>
                    </form>
                </div>
                
                <!-- Financial Analysis Section -->
                <div id="financial-analysis-section" class="tab-content transition-opacity duration-500 card">
                    <h2 class="text-3xl font-bold text-gray-800 mb-6 text-center">Analisis Keuangan</h2>
                    <p class="text-gray-600 mb-4 text-center">Dapatkan analisis keuangan berdasarkan data yang Anda masukkan.</p>
                    <div class="flex justify-center mb-6">
                        <button id="get-analysis-btn" class="button-primary py-3 px-6">
                            Dapatkan Analisis
                        </button>
                    </div>
                    <div id="analysis-output" class="p-6 bg-gray-50 rounded-lg text-gray-800 leading-relaxed shadow-inner">
                        <p class="text-center text-gray-500">Hasil analisis akan muncul di sini.</p>
                    </div>
                </div>

                <!-- Modal for alerts -->
                <div id="confirmation-modal" class="modal">
                    <div class="modal-content relative p-8">
                        <button onclick="closeModal()" class="modal-close-btn">&times;</button>
                        <p id="modal-text" class="text-center text-xl font-semibold mb-6"></p>
                        <div class="flex justify-center space-x-4">
                            <button id="modal-yes-btn" class="bg-red-500 hover:bg-red-600 text-white font-bold py-2 px-4 rounded-full" onclick="handleModalYes()">Ya</button>
                            <button id="modal-no-btn" class="bg-gray-500 hover:bg-gray-600 text-white font-bold py-2 px-4 rounded-full" onclick="handleModalNo()">Tidak</button>
                        </div>
                    </div>
                </div>
            </main>
        </div>
    </div>

    <script>
        // Data inisialisasi
        let allData = {
            purchases: [],
            sales: [],
            otherTransactions: [],
            assets: [],
            capitalAndDebt: { capital: 0, debt: 0 },
            rampSales: []
        };
        
        // Elemen-elemen UI
        const loginContainer = document.getElementById('login-container');
        const appContainer = document.getElementById('app-container');
        const loginForm = document.getElementById('login-form');
        const loginErrorMsg = document.getElementById('login-error-message');
        const logoutBtn = document.getElementById('logout-btn');
        const debugInfo = document.getElementById('debug-info');

        // Fungsi otentikasi
        function checkLoginStatus() {
            const isLoggedIn = localStorage.getItem('isLoggedIn') === 'true';
            if (isLoggedIn) {
                // Sembunyikan login dan tampilkan app jika sudah login
                loginContainer.style.display = 'none';
                appContainer.style.display = 'flex';
                loadData();
                showForm('summary-section');
            } else {
                // Tampilkan login jika belum login
                loginContainer.style.display = 'flex';
                appContainer.style.display = 'none';
            }
        }
        
        function handleLogin(event) {
            event.preventDefault();
            const usernameInput = document.getElementById('username').value.trim();
            const passwordInput = document.getElementById('password').value.trim();
            
            const validUsername = 'LOTUZT';
            const validPassword = '12345';

            // Menampilkan informasi debug yang lebih detail
            debugInfo.innerHTML = `
                <p><strong>Informasi Debug:</strong></p>
                <p>Username yang dimasukkan: <strong>'${usernameInput}'</strong> (panjang: ${usernameInput.length})</p>
                <p>Password yang dimasukkan: <strong>'${passwordInput}'</strong> (panjang: ${passwordInput.length})</p>
                <p>Username yang seharusnya: <strong>'${validUsername}'</strong> (panjang: ${validUsername.length})</p>
                <p>Password yang seharusnya: <strong>'${validPassword}'</strong> (panjang: ${validPassword.length})</p>
            `;
            console.log("Username input:", `'${usernameInput}'`, "Length:", usernameInput.length);
            console.log("Password input:", `'${passwordInput}'`, "Length:", passwordInput.length);
            console.log("Expected username:", `'${validUsername}'`, "Length:", validUsername.length);
            console.log("Expected password:", `'${validPassword}'`, "Length:", validPassword.length);

            // Cek apakah input cocok
            if (usernameInput === validUsername && passwordInput === validPassword) {
                localStorage.setItem('isLoggedIn', 'true');
                console.log("Login berhasil. Menampilkan aplikasi utama.");
                // Langsung sembunyikan login dan tampilkan app
                loginContainer.style.display = 'none';
                appContainer.style.display = 'flex';
                loginErrorMsg.textContent = '';
                loadData();
                showForm('summary-section');
            } else {
                loginErrorMsg.textContent = 'Nama pengguna atau kata sandi salah.';
                console.log("Login gagal.");
            }
        }
        
        function handleLogout() {
            localStorage.removeItem('isLoggedIn');
            loginForm.reset();
            checkLoginStatus();
        }

        // Fungsi utilitas
        function formatCurrency(amount) {
            return new Intl.NumberFormat('id-ID', { style: 'currency', currency: 'IDR', minimumFractionDigits: 0 }).format(amount);
        }

        // Tampilkan/sembunyikan bagian
        function showForm(formId) {
            document.querySelectorAll('.tab-content').forEach(form => {
                form.classList.remove('active');
            });
            document.getElementById(formId).classList.add('active');
            
            document.querySelectorAll('.sidebar-item').forEach(item => {
                item.classList.remove('active');
            });
            document.querySelector(`[onclick="showForm('${formId}')"]`).classList.add('active');
            
            // Perbarui tampilan laporan RAMP saat menu diklik
            if (formId === 'ramp-report-section') {
                updateRampReportDisplay();
            }
        }

        // Simpan data ke localStorage
        function saveData() {
            localStorage.setItem('accountingAppAllData', JSON.stringify(allData));
        }

        // Muat data dari localStorage
        function loadData() {
            const storedData = localStorage.getItem('accountingAppAllData');
            if (storedData) {
                allData = JSON.parse(storedData);
            }
            updateAllSummaries();
            populateYearDropdown();
        }

        // Perbarui semua ringkasan dan daftar transaksi
        function updateAllSummaries() {
            const selectedMonth = document.getElementById('report-month').value;
            const selectedYear = document.getElementById('report-year').value;

            const filteredData = {
                purchases: [],
                sales: [],
                otherTransactions: [],
                rampSales: []
            };
            
            // Filter data berdasarkan bulan dan tahun
            Object.keys(allData).forEach(key => {
                if (Array.isArray(allData[key])) {
                    filteredData[key] = allData[key].filter(transaction => {
                        const date = new Date(transaction.date);
                        const month = date.getMonth() + 1;
                        const year = date.getFullYear();
                        return (selectedMonth === '' || month == selectedMonth) &&
                               (selectedYear === '' || year == selectedYear);
                    });
                }
            });

            const totalSales = filteredData.sales.reduce((sum, t) => sum + (t.amount || 0), 0);
            const totalRampSales = filteredData.rampSales.reduce((sum, t) => sum + (t.totalInvoiceAmount || 0), 0);
            const totalPurchases = filteredData.purchases.reduce((sum, t) => sum + (t.amount || 0), 0);
            const otherIncome = filteredData.otherTransactions.filter(t => t.type === 'Pemasukan').reduce((sum, t) => sum + (t.amount || 0), 0);
            const otherExpenses = filteredData.otherTransactions.filter(t => t.type === 'Pengeluaran').reduce((sum, t) => sum + (t.amount || 0), 0);
            const totalAssets = allData.assets.reduce((sum, a) => sum + (a.value || 0), 0);
            const totalDebts = allData.capitalAndDebt.debt;
            const totalCapital = allData.capitalAndDebt.capital;
            
            const totalProfit = (totalSales + totalRampSales + otherIncome) - (totalPurchases + otherExpenses);
            
            document.getElementById('total-sales').textContent = formatCurrency(totalSales + totalRampSales);
            document.getElementById('total-purchases').textContent = formatCurrency(totalPurchases);
            document.getElementById('total-profit').textContent = formatCurrency(totalProfit);
            document.getElementById('total-assets').textContent = formatCurrency(totalAssets);
            document.getElementById('total-debts').textContent = formatCurrency(totalDebts);
            document.getElementById('total-capital').textContent = formatCurrency(totalCapital);

            // Perbarui daftar transaksi utama
            const transactionListEl = document.getElementById('transaction-list');
            transactionListEl.innerHTML = '';
            
            const allTransactions = [
                ...filteredData.purchases.map(t => ({ ...t, type: 'Pembelian' })),
                ...filteredData.sales.map(t => ({ ...t, type: 'Penjualan' })),
                ...filteredData.otherTransactions,
                ...filteredData.rampSales.map(t => ({ 
                    date: t.date, 
                    type: 'Penjualan RAMP', 
                    description: `Invoice: ${t.invoice}, Customer: ${t.customer}`,
                    amount: t.totalInvoiceAmount
                }))
            ].sort((a, b) => new Date(b.date) - new Date(a.date));

            allTransactions.forEach(t => {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td class="py-2 px-4">${t.date}</td>
                    <td class="py-2 px-4">${t.type}</td>
                    <td class="py-2 px-4">${t.description}</td>
                    <td class="py-2 px-4">${formatCurrency(t.amount || 0)}</td>
                `;
                transactionListEl.appendChild(row);
            });
            updateRampReportDisplay();
        }

        // Tambahkan fungsi untuk menampilkan data RAMP
        function updateRampReportDisplay() {
            const rampListEl = document.getElementById('ramp-transaction-list');
            rampListEl.innerHTML = '';

            const selectedMonth = document.getElementById('report-month').value;
            const selectedYear = document.getElementById('report-year').value;

            const filteredRampSales = allData.rampSales.filter(transaction => {
                const date = new Date(transaction.date);
                const month = date.getMonth() + 1;
                const year = date.getFullYear();
                return (selectedMonth === '' || month == selectedMonth) &&
                       (selectedYear === '' || year == selectedYear);
            }).sort((a, b) => new Date(b.date) - new Date(a.date));

            filteredRampSales.forEach(t => {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td class="py-2 px-2">${t.date}</td>
                    <td class="py-2 px-2">${t.invoice}</td>
                    <td class="py-2 px-2">${t.customer}</td>
                    <td class="py-2 px-2">${t.gross}</td>
                    <td class="py-2 px-2">${t.tare}</td>
                    <td class="py-2 px-2">${(t.netto1).toFixed(2)}</td>
                    <td class="py-2 px-2">${t.deductionPercent}</td>
                    <td class="py-2 px-2">${(t.netto2).toFixed(2)}</td>
                    <td class="py-2 px-2">${formatCurrency(t.price)}</td>
                    <td class="py-2 px-2">${formatCurrency(t.totalPrice)}</td>
                    <td class="py-2 px-2">${t.ppn}</td>
                    <td class="py-2 px-2">${t.pph}</td>
                    <td class="py-2 px-2">${formatCurrency(t.totalInvoiceAmount)}</td>
                `;
                rampListEl.appendChild(row);
            });
        }
        
        // Fungsi untuk mengisi dropdown tahun
        function populateYearDropdown() {
            const yearDropdown = document.getElementById('report-year');
            const currentYear = new Date().getFullYear();
            const startYear = 2020;
            yearDropdown.innerHTML = '<option value="">Semua Tahun</option>';
            for (let year = currentYear + 1; year >= startYear; year--) {
                const option = document.createElement('option');
                option.value = year;
                option.textContent = year;
                yearDropdown.appendChild(option);
            }
            yearDropdown.value = currentYear; // Set default to current year
        }

        // Tambahkan event listener untuk semua form
        const purchaseForm = document.getElementById('purchase-form');
        const saleForm = document.getElementById('sale-form');
        const rampForm = document.getElementById('ramp-form');
        const otherTransactionForm = document.getElementById('other-transaction-form');
        const assetForm = document.getElementById('asset-form');
        const capitalAndDebtForm = document.getElementById('capital-and-debt-form');
        const clearDataBtn = document.getElementById('clear-data-btn');
        const getAnalysisBtn = document.getElementById('get-analysis-btn');
        const downloadReportBtn = document.getElementById('download-report-btn');
        const reportMonthEl = document.getElementById('report-month');
        const reportYearEl = document.getElementById('report-year');
        
        // Fungsi untuk menangani penambahan transaksi pembelian
        function addPurchase(event) {
            event.preventDefault();
            const formData = new FormData(event.target);
            const newPurchase = Object.fromEntries(formData.entries());
            
            const gross = parseFloat(newPurchase.gross);
            const tare = parseFloat(newPurchase.tare);
            const deductionPercent = parseFloat(newPurchase.deductionPercent);
            const price = parseFloat(newPurchase.price);
            
            const netto1 = gross - tare;
            const netto2 = netto1 * (1 - deductionPercent / 100);
            const totalAmount = netto2 * price;

            Object.assign(newPurchase, {
                netto1: netto1,
                netto2: netto2,
                amount: totalAmount
            });
            
            allData.purchases.push(newPurchase);
            saveData();
            updateAllSummaries();
            event.target.reset();
            showForm('summary-section');
        }
        
        // Fungsi untuk menangani penambahan transaksi penjualan
        function addSale(event) {
            event.preventDefault();
            const formData = new FormData(event.target);
            const newSale = Object.fromEntries(formData.entries());
            
            const gross = parseFloat(newSale.gross);
            const tare = parseFloat(newSale.tare);
            const deductionPercent = parseFloat(newSale.deductionPercent);
            const price = parseFloat(newSale.price);
            
            const netto1 = gross - tare;
            const netto2 = netto1 * (1 - deductionPercent / 100);
            const totalAmount = netto2 * price;
            
            Object.assign(newSale, {
                netto1: netto1,
                netto2: netto2,
                amount: totalAmount
            });
            
            allData.sales.push(newSale);
            saveData();
            updateAllSummaries();
            event.target.reset();
            showForm('summary-section');
        }

        // Fungsi baru untuk menambahkan transaksi RAMP
        function addRampTransaction(event) {
            event.preventDefault();
            const formData = new FormData(event.target);
            const newRampTransaction = Object.fromEntries(formData.entries());
            
            // Konversi nilai ke tipe numerik dan hitung
            const gross = parseFloat(newRampTransaction.gross);
            const tare = parseFloat(newRampTransaction.tare);
            const deductionPercent = parseFloat(newRampTransaction.deductionPercent);
            const price = parseFloat(newRampTransaction.price);
            const ppn = parseFloat(newRampTransaction.ppn);
            const pph = parseFloat(newRampTransaction.pph);
            
            const netto1 = gross - tare;
            const netto2 = netto1 * (1 - deductionPercent / 100);
            const totalNetto2Ton = netto2 / 1000;
            const totalPrice = totalNetto2Ton * price;
            const totalPpn = totalPrice * (ppn / 100);
            const totalPph = totalPrice * (pph / 100);
            const totalInvoiceAmount = totalPrice + totalPpn - totalPph;

            // Tambahkan hasil perhitungan ke objek transaksi
            Object.assign(newRampTransaction, {
                netto1: netto1,
                netto2: netto2,
                totalPrice: totalPrice,
                totalInvoiceAmount: totalInvoiceAmount,
                amount: totalInvoiceAmount
            });

            allData.rampSales.push(newRampTransaction);
            saveData();
            updateAllSummaries();
            event.target.reset();
            showForm('ramp-report-section');
        }
        
        // Fungsi untuk memicu perhitungan otomatis pada form
        function setupLiveCalculation(formId) {
            const form = document.getElementById(formId);
            const grossInput = form.querySelector('[name="gross"]');
            const tareInput = form.querySelector('[name="tare"]');
            const deductionInput = form.querySelector('[name="deductionPercent"]');
            const priceInput = form.querySelector('[name="price"]');
            const amountInput = form.querySelector('[name="amount"]');

            const calculate = () => {
                const gross = parseFloat(grossInput.value) || 0;
                const tare = parseFloat(tareInput.value) || 0;
                const deduction = parseFloat(deductionInput.value) || 0;
                const price = parseFloat(priceInput.value) || 0;
                
                if (gross > 0 && tare > 0 && price > 0) {
                    const netto1 = gross - tare;
                    const netto2 = netto1 * (1 - deduction / 100);
                    const totalAmount = netto2 * price;
                    amountInput.value = formatCurrency(totalAmount);
                } else {
                    amountInput.value = 'Rp 0';
                }
            };

            [grossInput, tareInput, deductionInput, priceInput].forEach(input => {
                input.addEventListener('input', calculate);
            });
        }
        
        // Panggil fungsi setup untuk kedua form
        window.addEventListener('load', () => {
            setupLiveCalculation('purchase-form');
            setupLiveCalculation('sale-form');
        });

        // Fungsi unduh laporan Excel
        function downloadReport() {
            const worksheetData = [];
            // Header untuk sheet Ringkasan
            worksheetData.push(['Laporan Keuangan Perdagangan', '', '', '']);
            worksheetData.push(['Tanggal', new Date().toLocaleDateString('id-ID'), '', '']);
            worksheetData.push(['Ringkasan', '', '', '']);
            worksheetData.push(['Total Penjualan:', document.getElementById('total-sales').textContent, '', '']);
            worksheetData.push(['Total Pembelian:', document.getElementById('total-purchases').textContent, '', '']);
            worksheetData.push(['Total Laba/Rugi:', document.getElementById('total-profit').textContent, '', '']);
            worksheetData.push(['Total Aset:', document.getElementById('total-assets').textContent, '', '']);
            worksheetData.push(['Total Hutang:', document.getElementById('total-debts').textContent, '', '']);
            worksheetData.push(['Total Modal:', document.getElementById('total-capital').textContent, '', '']);
            worksheetData.push([]);
            worksheetData.push(['Transaksi', '', '', '']);
            worksheetData.push(['Tanggal', 'Tipe', 'Deskripsi', 'Jumlah']);
            
            const selectedMonth = document.getElementById('report-month').value;
            const selectedYear = document.getElementById('report-year').value;

            const filteredPurchases = allData.purchases.filter(transaction => {
                const date = new Date(transaction.date);
                const month = date.getMonth() + 1;
                const year = date.getFullYear();
                return (selectedMonth === '' || month == selectedMonth) &&
                       (selectedYear === '' || year == selectedYear);
            });

            const filteredSales = allData.sales.filter(transaction => {
                const date = new Date(transaction.date);
                const month = date.getMonth() + 1;
                const year = date.getFullYear();
                return (selectedMonth === '' || month == selectedMonth) &&
                       (selectedYear === '' || year == selectedYear);
            });
            
            const filteredOtherTransactions = allData.otherTransactions.filter(transaction => {
                const date = new Date(transaction.date);
                const month = date.getMonth() + 1;
                const year = date.getFullYear();
                return (selectedMonth === '' || month == selectedMonth) &&
                       (selectedYear === '' || year == selectedYear);
            });
            
            const filteredRampSales = allData.rampSales.filter(transaction => {
                const date = new Date(transaction.date);
                const month = date.getMonth() + 1;
                const year = date.getFullYear();
                return (selectedMonth === '' || month == selectedMonth) &&
                       (selectedYear === '' || year == selectedYear);
            });

            const allTransactions = [
                ...filteredPurchases.map(t => ({ ...t, type: 'Pembelian' })),
                ...filteredSales.map(t => ({ ...t, type: 'Penjualan' })),
                ...filteredOtherTransactions,
                ...filteredRampSales.map(t => ({ 
                    date: t.date, 
                    type: 'Penjualan RAMP', 
                    description: `Invoice: ${t.invoice}, Customer: ${t.customer}`,
                    amount: t.totalInvoiceAmount
                }))
            ].sort((a, b) => new Date(b.date) - new Date(a.date));
            
            allTransactions.forEach(t => {
                worksheetData.push([
                    t.date,
                    t.type,
                    t.description,
                    t.amount
                ]);
            });

            // Sheet baru untuk RAMP
            const rampData = [];
            rampData.push(['Laporan Penjualan RAMP', '', '', '', '', '', '', '', '', '']);
            rampData.push(['Tanggal', 'Invoice', 'Customer', 'Gross (Kg)', 'Tare (Kg)', 'Netto 1 (Kg)', 'Potongan (%)', 'Netto 2 (Kg)', 'Harga per Ton', 'Total Harga', 'PPN (%)', 'PPh (%)', 'Total Tagihan']);
            filteredRampSales.forEach(t => {
                rampData.push([
                    t.date,
                    t.invoice,
                    t.customer,
                    t.gross,
                    t.tare,
                    t.netto1,
                    t.deductionPercent,
                    t.netto2,
                    t.price,
                    t.totalPrice,
                    t.ppn,
                    t.pph,
                    t.totalInvoiceAmount
                ]);
            });

            const workbook = XLSX.utils.book_new();
            const summarySheet = XLSX.utils.aoa_to_sheet(worksheetData);
            const rampSheet = XLSX.utils.aoa_to_sheet(rampData);
            
            XLSX.utils.book_append_sheet(workbook, summarySheet, 'Ringkasan');
            XLSX.utils.book_append_sheet(workbook, rampSheet, 'Penjualan RAMP');
            
            XLSX.writeFile(workbook, 'Laporan_Keuangan_Perdagangan.xlsx');
        }
        
        // Fungsi untuk membersihkan semua data
        function clearAllData() {
            showCustomModal("Apakah Anda yakin ingin membersihkan semua data? Tindakan ini tidak dapat dibatalkan.", () => {
                localStorage.removeItem('accountingAppAllData');
                allData = {
                    purchases: [],
                    sales: [],
                    otherTransactions: [],
                    assets: [],
                    capitalAndDebt: { capital: 0, debt: 0 },
                    rampSales: []
                };
                updateAllSummaries();
                showCustomModal("Data telah dibersihkan!", null, false);
                showForm('summary-section');
            });
        }
        
        // Fungsi untuk analisis keuangan (placeholder)
        function getFinancialAnalysis() {
            showCustomModal("Fitur ini sedang dalam pengembangan. Silakan tunggu update berikutnya!", null, false);
        }

        // Custom modal functions
        let modalYesCallback = null;
        let modalNoCallback = null;

        function showCustomModal(message, onYes, hasButtons = true) {
            const modal = document.getElementById('confirmation-modal');
            const modalText = document.getElementById('modal-text');
            const yesBtn = document.getElementById('modal-yes-btn');
            const noBtn = document.getElementById('modal-no-btn');
            
            modalText.textContent = message;
            
            if (hasButtons) {
                yesBtn.style.display = 'inline-block';
                noBtn.style.display = 'inline-block';
                modalYesCallback = onYes;
                modalNoCallback = closeModal;
            } else {
                yesBtn.style.display = 'none';
                noBtn.style.display = 'none';
                modalYesCallback = null;
                modalNoCallback = null;
                setTimeout(() => closeModal(), 3000);
            }
            modal.style.display = 'flex';
        }

        function closeModal() {
            document.getElementById('confirmation-modal').style.display = 'none';
        }

        function handleModalYes() {
            if (modalYesCallback) {
                modalYesCallback();
            }
            closeModal();
        }

        function handleModalNo() {
            if (modalNoCallback) {
                modalNoCallback();
            }
            closeModal();
        }

        // fungsi untuk addOtherTransaction, addAsset, saveCapitalAndDebt
        function addOtherTransaction(event) {
            event.preventDefault();
            const formData = new FormData(event.target);
            const newTransaction = Object.fromEntries(formData.entries());
            newTransaction.amount = parseFloat(newTransaction.amount);
            allData.otherTransactions.push(newTransaction);
            saveData();
            updateAllSummaries();
            event.target.reset();
            showForm('summary-section');
        }

        function addAsset(event) {
            event.preventDefault();
            const formData = new FormData(event.target);
            const newAsset = Object.fromEntries(formData.entries());
            newAsset.value = parseFloat(newAsset.value);
            allData.assets.push(newAsset);
            saveData();
            updateAllSummaries();
            event.target.reset();
            showForm('summary-section');
        }

        function saveCapitalAndDebt(event) {
            event.preventDefault();
            const formData = new FormData(event.target);
            const data = Object.fromEntries(formData.entries());
            allData.capitalAndDebt.capital = parseFloat(data.capital);
            allData.capitalAndDebt.debt = parseFloat(data.debt);
            saveData();
            updateAllSummaries();
            showForm('summary-section');
        }

        // Tambahkan event listener untuk login dan logout
        loginForm.addEventListener('submit', handleLogin);
        logoutBtn.addEventListener('click', handleLogout);

        // Tambahkan event listener untuk semua form
        purchaseForm.addEventListener('submit', addPurchase);
        saleForm.addEventListener('submit', addSale);
        rampForm.addEventListener('submit', addRampTransaction);
        otherTransactionForm.addEventListener('submit', addOtherTransaction);
        assetForm.addEventListener('submit', addAsset);
        capitalAndDebtForm.addEventListener('submit', saveCapitalAndDebt);
        clearDataBtn.addEventListener('click', clearAllData);
        getAnalysisBtn.addEventListener('click', getFinancialAnalysis);
        downloadReportBtn.addEventListener('click', downloadReport);
        reportMonthEl.addEventListener('change', updateAllSummaries);
        reportYearEl.addEventListener('change', updateAllSummaries);

        // Muat data saat halaman dimuat
        window.addEventListener('load', () => {
            checkLoginStatus();
        });
    </script>
</body>
</html>

