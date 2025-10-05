<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simulador de Vendas Eri Fotos</title>
    <!-- Inclui o Tailwind CSS para estilização rápida e responsiva -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Inclui a biblioteca Chart.js para geração de gráficos -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.3/dist/chart.umd.min.js"></script>
    <style>
        /* Define a fonte Inter e cores padrão */
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f7f7f7;
        }
        .app-container {
            max-width: 600px;
            margin: 2rem auto;
            padding: 1.5rem;
        }
    </style>
</head>
<body>

    <div class="app-container bg-white shadow-2xl rounded-xl">
        <header class="text-center mb-6">
            <h1 class="text-3xl font-bold text-gray-800">Eri Fotos</h1>
            <p class="text-indigo-600 text-lg">Simulador de Registro de Vendas e WhatsApp</p>
        </header>

        <!-- Formulário de Entrada de Dados -->
        <form id="salesForm" class="space-y-4">

            <!-- Mensagem de Validação -->
            <div id="validationMessage" class="hidden p-3 bg-red-100 text-red-700 rounded-md font-medium" role="alert">
                Por favor, preencha o Nome e o Telefone (WhatsApp) corretamente.
            </div>

            <!-- Informações do Cliente -->
            <h2 class="text-xl font-semibold text-gray-700 border-b pb-2 mb-4">Dados do Cliente</h2>

            <div>
                <label for="clientName" class="block text-sm font-medium text-gray-700">Nome do Cliente</label>
                <input type="text" id="clientName" required class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500">
            </div>

            <div>
                <label for="clientEmail" class="block text-sm font-medium text-gray-700">Email</label>
                <input type="email" id="clientEmail" class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500">
            </div>

            <div>
                <label for="clientPhone" class="block text-sm font-medium text-gray-700">Telefone (WhatsApp) - Ex: 5511987654321</label>
                <!-- O input aceita apenas números para facilitar a geração do link wa.me -->
                <input type="number" id="clientPhone" required class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500" placeholder="Apenas números, inclua código do país e DDD">
            </div>

            <!-- Informações da Venda -->
            <h2 class="text-xl font-semibold text-gray-700 border-b pb-2 mb-4 pt-4">Detalhes da Venda</h2>

            <div>
                <label for="photoValue" class="block text-sm font-medium text-gray-700">Valor da Foto/Sessão (R$)</label>
                <input type="number" step="0.01" id="photoValue" value="0.00" required class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500">
            </div>

            <div>
                <label for="toyValue" class="block text-sm font-medium text-gray-700">Venda de Brinquedo (Opcional)</label>
                <select id="toyValue" class="mt-1 block w-full px-3 py-2 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500">
                    <option value="0|Nenhuma Venda">Não</option>
                    <option value="10|Brinquedo R$ 10,00">R$ 10,00</option>
                    <option value="15|Brinquedo R$ 15,00">R$ 15,00</option>
                    <option value="20|Brinquedo R$ 20,00">R$ 20,00</option>
                </select>
            </div>

            <!-- Total e Ação -->
            <div class="pt-6">
                <div class="bg-indigo-50 p-4 rounded-lg shadow-inner flex justify-between items-center mb-6">
                    <span class="text-xl font-medium text-gray-700">TOTAL DA VENDA:</span>
                    <span id="totalDisplay" class="text-3xl font-bold text-indigo-700">R$ 0.00</span>
                </div>

                <button type="submit" class="w-full flex justify-center py-3 px-4 border border-transparent rounded-md shadow-lg text-lg font-medium text-white bg-green-600 hover:bg-green-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-green-500 transition duration-150 ease-in-out">
                    Concluir Venda e Gerar WhatsApp
                </button>
            </div>
        </form>

        <!-- Área de Resultado (Link WhatsApp) -->
        <div id="resultArea" class="hidden mt-8 p-6 bg-gray-100 border border-gray-300 rounded-lg">
            <h3 class="text-xl font-semibold text-gray-700 mb-4">Link de WhatsApp Gerado!</h3>
            <p class="mb-4 text-sm text-gray-600">O link foi gerado com a mensagem personalizada. Você pode enviá-lo para o cliente agora.</p>

            <a id="whatsappLink" target="_blank" class="block text-center py-3 px-4 border border-transparent rounded-md shadow-md text-base font-medium text-white bg-blue-500 hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500 transition duration-150 ease-in-out">
                Abrir WhatsApp para o Cliente
            </a>
            
            <div class="mt-4">
                 <p class="text-sm font-medium text-gray-600">Mensagem gerada (prévia):</p>
                 <textarea id="messagePreview" rows="8" readonly class="w-full text-xs p-3 border border-gray-300 rounded-md mt-2 bg-white text-gray-800"></textarea>
            </div>
        </div>

        ---

        <!-- Painel de Vendas (Gráfico) -->
        <section class="mt-8 pt-4 border-t border-gray-300">
            <h2 class="text-2xl font-bold text-gray-800 mb-4">Painel de Vendas (Últimos 7 Dias)</h2>
            <div class="bg-white p-4 rounded-lg shadow-md">
                <canvas id="salesChart"></canvas>
            </div>
            <p class="text-xs text-gray-500 mt-2 text-center">Este gráfico atualiza automaticamente a cada nova venda registrada.</p>
        </section>

    </div>

    <script>
        // Mapeamento dos elementos do DOM
        const clientNameInput = document.getElementById('clientName');
        const clientPhoneInput = document.getElementById('clientPhone');
        const photoValueInput = document.getElementById('photoValue');
        const toyValueSelect = document.getElementById('toyValue');
        const totalDisplay = document.getElementById('totalDisplay');
        const salesForm = document.getElementById('salesForm');
        const resultArea = document.getElementById('resultArea');
        const whatsappLink = document.getElementById('whatsappLink');
        const messagePreview = document.getElementById('messagePreview');
        const validationMessage = document.getElementById('validationMessage');
        const chartCanvas = document.getElementById('salesChart');
        
        // Constante para localStorage e Links
        const STORAGE_KEY = 'eriFotosSalesHistory';
        const instagramLink = "https://www.instagram.com/erinaldovieirafotografia";
        const banlekLink = "banlek.com/erinaldo";
        const contactPhone = "81986918163"; 
        
        let salesChartInstance = null; // Variável para armazenar a instância do Chart.js

        // --- Funções de Cálculo e Geração ---

        /**
         * Calcula o valor total da venda e atualiza a exibição.
         */
        function calculateTotal() {
            const photoValue = parseFloat(photoValueInput.value) || 0;
            const selectedToy = toyValueSelect.value;
            const toyPrice = parseFloat(selectedToy.split('|')[0]) || 0;

            const total = photoValue + toyPrice;
            
            totalDisplay.textContent = `R$ ${total.toFixed(2).replace('.', ',')}`;
            
            return total;
        }

        /**
         * Gera a mensagem personalizada e o link do WhatsApp.
         */
        function generateWhatsAppLink(totalValue) {
            const clientName = clientNameInput.value.trim();
            const clientPhone = clientPhoneInput.value.trim();
            const photoValue = parseFloat(photoValueInput.value) || 0;
            
            const selectedToy = toyValueSelect.value;
            const toyDescription = selectedToy.split('|')[1]; 
            
            const formattedPhotoValue = photoValue.toFixed(2).replace('.', ',');
            const formattedTotal = totalValue.toFixed(2).replace('.', ',');

            // 1. Constrói o corpo da mensagem
            const messageText = 
                `Olá, ${clientName}!` + 
                `\n\nObrigado por escolher a **Eri Fotos**! Sua compra foi registrada com sucesso.` +
                `\n\nDetalhes da Compra:` +
                `\n- Valor da Foto: R$ ${formattedPhotoValue}` +
                `\n- Venda de Brinquedo: ${toyDescription}` +
                `\n- Valor Total: R$ ${formattedTotal}` +
                `\n\nSiga meu trabalho e entre em contato:` + 
                `\n- Instagram: ${instagramLink}` +
                `\n- Meu portfólio: ${banlekLink}` +
                `\n- Contato Direto: ${contactPhone}` + 
                `\n\nConte comigo para seus próximos momentos!` +
                `\n\nAtenciosamente, Eri Fotos`; 

            // 2. Codifica o texto para ser seguro na URL
            const encodedMessage = encodeURIComponent(messageText);
            
            // 3. Constrói o link completo
            const whatsappUrl = `https://wa.me/${clientPhone}?text=${encodedMessage}`;
            
            return { url: whatsappUrl, text: messageText };
        }
        
        // --- Funções de Armazenamento e Gráfico ---

        /**
         * Carrega o histórico de vendas do localStorage.
         * @returns {Array<Object>} Lista de vendas.
         */
        function loadSalesHistory() {
            try {
                const history = JSON.parse(localStorage.getItem(STORAGE_KEY)) || [];
                // Garante que a data está em formato Date para fácil manipulação
                return history.map(sale => ({
                    date: new Date(sale.date),
                    total: sale.total
                }));
            } catch (error) {
                console.error("Erro ao carregar histórico de vendas:", error);
                return [];
            }
        }

        /**
         * Salva uma nova venda no histórico e atualiza o localStorage.
         * @param {number} totalValue - Valor total da venda.
         */
        function saveSale(totalValue) {
            const history = loadSalesHistory();
            const newSale = {
                date: new Date().toISOString(), // Salva a data em formato ISO string
                total: totalValue
            };
            history.push(newSale);
            localStorage.setItem(STORAGE_KEY, JSON.stringify(history));
        }

        /**
         * Agrupa o histórico de vendas por dia e totaliza.
         * @param {Array<Object>} history - Lista de vendas.
         * @returns {Object} { labels: string[], data: number[] }
         */
        function groupSalesByDay(history) {
            const today = new Date();
            today.setHours(0, 0, 0, 0);

            const dailyTotals = {};

            // Inicializa os totais dos últimos 7 dias a zero
            for (let i = 0; i < 7; i++) {
                const date = new Date(today);
                date.setDate(today.getDate() - i);
                const dateKey = date.toISOString().substring(0, 10); // YYYY-MM-DD
                dailyTotals[dateKey] = 0;
            }

            // Soma as vendas para cada dia dentro do período de 7 dias
            history.forEach(sale => {
                const saleDateKey = sale.date.toISOString().substring(0, 10);
                if (dailyTotals.hasOwnProperty(saleDateKey)) {
                    dailyTotals[saleDateKey] += sale.total;
                }
            });

            // Prepara os dados para o Chart.js
            const sortedKeys = Object.keys(dailyTotals).sort();
            
            const labels = sortedKeys.map(key => {
                // Formata a chave para dd/mm para o gráfico
                const [year, month, day] = key.split('-');
                return `${day}/${month}`;
            });
            
            const data = sortedKeys.map(key => dailyTotals[key]);

            return { labels, data };
        }

        /**
         * Renderiza o gráfico de vendas diárias.
         */
        function renderSalesChart() {
            const history = loadSalesHistory();
            const { labels, data } = groupSalesByDay(history);
            
            // Destrói a instância anterior do gráfico se existir
            if (salesChartInstance) {
                salesChartInstance.destroy();
            }

            salesChartInstance = new Chart(chartCanvas, {
                type: 'bar',
                data: {
                    labels: labels,
                    datasets: [{
                        label: 'Total Vendido (R$)',
                        data: data,
                        backgroundColor: 'rgba(79, 70, 229, 0.7)', // Indigo
                        borderColor: 'rgb(79, 70, 229)',
                        borderWidth: 1,
                        borderRadius: 5,
                    }]
                },
                options: {
                    responsive: true,
                    scales: {
                        y: {
                            beginAtZero: true,
                            title: {
                                display: true,
                                text: 'Valor (R$)'
                            },
                            ticks: {
                                callback: function(value) {
                                    return 'R$ ' + value.toFixed(2).replace('.', ',');
                                }
                            }
                        }
                    },
                    plugins: {
                        legend: {
                            display: false
                        },
                        tooltip: {
                             callbacks: {
                                label: function(context) {
                                    let label = context.dataset.label || '';
                                    if (label) {
                                        label += ': ';
                                    }
                                    if (context.parsed.y !== null) {
                                        label += 'R$ ' + context.parsed.y.toFixed(2).replace('.', ',');
                                    }
                                    return label;
                                }
                            }
                        }
                    }
                }
            });
        }


        // --- Inicialização e Listeners ---

        // Adiciona listeners para recálculo automático ao mudar os valores
        photoValueInput.addEventListener('input', calculateTotal);
        toyValueSelect.addEventListener('change', calculateTotal);

        // Listener para o envio do formulário
        salesForm.addEventListener('submit', function(e) {
            e.preventDefault();
            
            validationMessage.classList.add('hidden'); // Oculta mensagem de erro anterior

            // 1. Garante que o telefone e o nome foram preenchidos
            if (!clientNameInput.value.trim() || !clientPhoneInput.value.trim()) {
                validationMessage.classList.remove('hidden');
                return;
            }

            // 2. Calcula o valor final
            const total = calculateTotal();

            // 3. Salva a venda e atualiza o gráfico
            saveSale(total);
            renderSalesChart();
            
            // 4. Gera a URL e o texto da mensagem
            const { url, text } = generateWhatsAppLink(total);

            // 5. Atualiza a área de resultado no HTML
            whatsappLink.href = url;
            messagePreview.value = text;
            whatsappLink.textContent = `Abrir WhatsApp para ${clientNameInput.value.trim()}`;
            
            // 6. Exibe a área de resultado e rola para ela
            resultArea.classList.remove('hidden');
            resultArea.scrollIntoView({ behavior: 'smooth' });

            // 7. Limpa o formulário (opcional, mas recomendado para nova venda)
            salesForm.reset();
            calculateTotal(); // Recalcula o total para R$ 0.00
            
            console.log('--- Venda Registrada e Gráfico Atualizado ---');
        });

        // Inicializa o cálculo do total e o gráfico na abertura
        calculateTotal();
        renderSalesChart();

    </script>

</body>
</html>

