<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Entrega Fácil - Marketplace de Condomínio</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        .hidden { display: none; }
        .active-tab { border-bottom: 4px solid #2563eb; color: #2563eb; }
    </style>
</head>
<body class="bg-gray-50 font-sans">

    <!-- TELA DE SELEÇÃO INICIAL -->
    <div id="selection-screen" class="fixed inset-0 bg-blue-600 flex items-center justify-center z-[100] px-4">
        <div class="bg-white p-8 rounded-3xl shadow-2xl max-w-md w-full text-center">
            <i class="fas fa-box-open text-5xl text-blue-600 mb-4"></i>
            <h1 class="text-3xl font-bold mb-2">Bem-vindo ao Entrega Fácil</h1>
            <p class="text-gray-500 mb-8">Como você deseja acessar o condomínio hoje?</p>
            
            <div class="grid gap-4">
                <button onclick="setRole('vendedor')" class="flex items-center justify-between p-4 border-2 border-gray-100 rounded-2xl hover:border-blue-500 hover:bg-blue-50 transition group">
                    <div class="text-left">
                        <span class="block font-bold text-lg group-hover:text-blue-700">Sou Vendedor</span>
                        <span class="text-sm text-gray-400 font-normal">Quero anunciar meus produtos</span>
                    </div>
                    <i class="fas fa-store text-2xl text-gray-300 group-hover:text-blue-500"></i>
                </button>

                <button onclick="setRole('comprador')" class="flex items-center justify-between p-4 border-2 border-gray-100 rounded-2xl hover:border-green-500 hover:bg-green-50 transition group">
                    <div class="text-left">
                        <span class="block font-bold text-lg group-hover:text-green-700">Sou Comprador</span>
                        <span class="text-sm text-gray-400 font-normal">Quero ver o que os vizinhos vendem</span>
                    </div>
                    <i class="fas fa-shopping-basket text-2xl text-gray-300 group-hover:text-green-500"></i>
                </button>
            </div>
        </div>
    </div>

    <!-- HEADER GERAL (Aparece após seleção) -->
    <header class="bg-white shadow-sm sticky top-0 z-50">
        <div class="container mx-auto px-4 py-4 flex justify-between items-center">
            <div class="flex items-center gap-2 cursor-pointer" onclick="location.reload()">
                <div class="bg-blue-600 text-white p-2 rounded-lg"><i class="fas fa-exchange-alt"></i></div>
                <h2 class="font-bold text-xl" id="role-title">Entrega Fácil</h2>
            </div>
            <div id="user-info" class="flex items-center gap-2 text-sm font-semibold text-gray-600">
                <i class="fas fa-map-marker-alt text-red-500"></i> Bloco A, Apto 102
            </div>
        </div>
    </header>

    <!-- ÁREA DO VENDEDOR -->
    <main id="seller-view" class="container mx-auto px-4 py-8 hidden">
        <div class="max-w-xl mx-auto">
            <h2 class="text-2xl font-bold mb-6">Anunciar Novo Produto</h2>
            <form onsubmit="addProduct(event)" class="bg-white p-6 rounded-2xl shadow-sm space-y-4">
                <div>
                    <label class="block text-sm font-bold mb-1">Nome do Produto</label>
                    <input type="text" id="p-name" required class="w-full p-3 bg-gray-50 rounded-xl border focus:ring-2 focus:ring-blue-500 outline-none" placeholder="Ex: Bolo de Cenoura">
                </div>
                <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label class="block text-sm font-bold mb-1">Preço (R$)</label>
                        <input type="number" step="0.01" id="p-price" required class="w-full p-3 bg-gray-50 rounded-xl border focus:ring-2 focus:ring-blue-500 outline-none">
                    </div>
                    <div>
                        <label class="block text-sm font-bold mb-1">Estoque Inicial</label>
                        <input type="number" id="p-stock" required class="w-full p-3 bg-gray-50 rounded-xl border focus:ring-2 focus:ring-blue-500 outline-none">
                    </div>
                </div>
                <button type="submit" class="w-full bg-blue-600 text-white font-bold py-4 rounded-xl hover:bg-blue-700 transition">Publicar Anúncio</button>
            </form>

            <h3 class="text-xl font-bold mt-10 mb-4">Meus Anúncios Ativos</h3>
            <div id="my-products-list" class="space-y-3">
                <!-- Injetado por JS -->
            </div>
        </div>
    </main>

    <!-- ÁREA DO COMPRADOR -->
    <main id="buyer-view" class="container mx-auto px-4 py-8 hidden">
        <h2 class="text-2xl font-bold mb-6">Produtos no seu Condomínio</h2>
        <div id="marketplace-grid" class="grid grid-cols-1 md:grid-cols-3 lg:grid-cols-4 gap-6">
            <!-- Injetado por JS -->
        </div>
    </main>

    <script>
        // Banco de Dados Simulado
        let products = [
            { id: 1, name: 'Pão Italiano Caseiro', price: 18.00, stock: 5, seller: 'Apto 42' },
            { id: 2, name: 'Brownie Recheado', price: 7.50, stock: 12, seller: 'Apto 105' }
        ];

        function setRole(role) {
            document.getElementById('selection-screen').classList.add('hidden');
            if (role === 'vendedor') {
                document.getElementById('seller-view').classList.remove('hidden');
                document.getElementById('role-title').innerText = "Entrega Fácil - Vendedor";
                renderSellerItems();
            } else {
                document.getElementById('buyer-view').classList.remove('hidden');
                document.getElementById('role-title').innerText = "Entrega Fácil - Marketplace";
                renderMarketplace();
            }
        }

        function addProduct(e) {
            e.preventDefault();
            const newProd = {
                id: Date.now(),
                name: document.getElementById('p-name').value,
                price: parseFloat(document.getElementById('p-price').value),
                stock: parseInt(document.getElementById('p-stock').value),
                seller: 'Meu Apto (Você)'
            };
            products.push(newProd);
            e.target.reset();
            renderSellerItems();
            alert("Produto cadastrado com sucesso!");
        }

        function renderSellerItems() {
            const list = document.getElementById('my-products-list');
            list.innerHTML = products.map(p => `
                <div class="bg-white p-4 rounded-xl shadow-sm border flex justify-between items-center">
                    <div>
                        <span class="font-bold block">${p.name}</span>
                        <span class="text-blue-600 font-semibold text-sm">R$ ${p.price.toFixed(2)}</span>
                    </div>
                    <div class="text-right">
                        <span class="block text-xs text-gray-400 italic">Estoque:</span>
                        <span class="font-bold text-gray-700">${p.stock} un.</span>
                    </div>
                </div>
            `).join('');
        }

        function renderMarketplace() {
            const grid = document.getElementById('marketplace-grid');
            grid.innerHTML = products.map(p => `
                <div class="bg-white rounded-2xl shadow-sm overflow-hidden border hover:shadow-lg transition">
                    <div class="h-40 bg-gray-100 flex items-center justify-center">
                        <i class="fas fa-image text-gray-300 text-4xl"></i>
                    </div>
                    <div class="p-4">
                        <div class="flex justify-between items-start mb-2">
                            <h3 class="font-bold text-gray-800">${p.name}</h3>
                            <span class="bg-blue-100 text-blue-700 text-[10px] px-2 py-1 rounded-full font-bold">${p.seller}</span>
                        </div>
                        <div class="flex justify-between items-end mt-4">
                            <div>
                                <span class="text-xs text-gray-400 block">Preço</span>
                                <span class="text-xl font-extrabold text-blue-700">R$ ${p.price.toFixed(2)}</span>
                            </div>
                            <div class="text-right">
                                <span class="text-[10px] text-gray-500 block">Disponível: <b>${p.stock}</b></span>
                                <button onclick="buyProduct(${p.id})" class="mt-1 bg-green-500 text-white px-4 py-2 rounded-lg text-sm font-bold hover:bg-green-600 transition">
                                    Comprar
                                </button>
                            </div>
                        </div>
                    </div>
                </div>
            `).join('');
        }

        function buyProduct(id) {
            const p = products.find(prod => prod.id === id);
            if(p.stock > 0) {
                p.stock--;
                alert(`Sucesso! Você comprou: ${p.name}. O vizinho do ${p.seller} será notificado.`);
                renderMarketplace();
            } else {
                alert("Produto esgotado!");
            }
        }
    </script>
</body>
</html>
