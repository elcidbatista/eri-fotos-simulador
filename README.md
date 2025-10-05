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
            /* Fundo muito claro, quase branco, para um visual limpo */
            background-color: #f9fafb; 
        }
        .app-container {
            max-width: 600px;
            margin: 2rem auto;
            padding: 2.5rem; /* Aumenta o padding interno */
        }
        /* Transição mais sutil para um toque profissional */
        button, a.block {
            transition: background-color 0.2s ease-in-out, box-shadow 0.2s ease-in-out;
        }
    </style>
</head>
<body>

    <div class="app-container bg-white shadow-2xl rounded-2xl border border-gray-100"> 
        <header class="text-center mb-10">
            <h1 class="text-4xl font-extrabold text-gray-900 tracking-tight">Eri Fotos</h1>
            <p class="text-gray-600 text-lg font-normal mt-1">Ferramenta de Registro e Análise de Vendas</p>
        </header>

        <!-- Formulário de Entrada de Dados -->
        <form id="salesForm" class="space-y-6">

            <!-- Mensagem de Validação -->
            <div id="validationMessage" class="hidden p-4 bg-red-100 text-red-800 rounded-lg font-bold shadow-md border border-red-200" role="alert">
                Por favor, preencha o Nome e o Telefone (WhatsApp) corretamente.
            </div>

            <!-- Informações do Cliente -->
            <h2 class="text-xl font-bold text-gray-700 border-b border-gray-200 pb-2 mb-4">Dados do Cliente</h2>

            <div>
                <label for="clientName" class="block text-sm font-medium text-gray-700">Nome do Cliente</label>
                <input type="text" id="clientName" required class="mt-1 block w-full px-4 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-600 focus:border-indigo-600">
            </div>

            <div>
                <label for="clientEmail" class="block text-sm font-medium text-gray-700">Email (Opcional)</label>
                <input type="email" id="clientEmail" class="mt-1 block w-full px-4 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-600 focus:border-indigo-600">
            </div>

            <div>
                <label for="clientPhone" class="block text-sm font-medium text-gray-700">Telefone (WhatsApp) - Ex: 5511987654321</label>
                <!-- O input aceita apenas números para facilitar a geração do link wa.me -->
                <input type="number" id="clientPhone" required class="mt-1 block w-full px-4 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-600 focus:border-indigo-600" placeholder="Apenas números, inclua código do país e DDD">
            </div>

            <!-- Informações da Venda -->
            <h2 class="text-xl font-bold text-gray-700 border-b border-gray-200 pb-2 mb-4 pt-4">Detalhes da Venda</h2>

            <div>
                <label for="photoValue" class="block text-sm font-medium text-gray-700">Valor da Foto/Sessão (R$)</label>
                <input type="number" step="0.01" id="photoValue" value="0.00" required class="mt-1 block w-full px-4 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-600 focus:border-indigo-600">
            </div>

            <div>
                <label for="toyValue" class="block text-sm font-medium text-gray-700">Venda de Brinquedo (Opcional)</label>
                <select id="toyValue" class="mt-1 block w-full px-4 py-2 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-indigo-600 focus:border-indigo-600">
                    <option value="0|Nenhuma Venda">Não</option>
                    <option value="10|Brinquedo R$ 10,00">R$ 10,00</option>
                    <option value="15|Brinquedo R$ 15,00">R$ 15,00</option>
                    <option value="20|Brinquedo R$ 20,00">R$ 20,00</option>
                </select>
            </div>

            <!-- Total e Ação -->
            <div class="pt-8">
                <!-- Caixa de total com cor forte e contraste alto -->
                <div class="bg-indigo-700 text-white p-5 rounded-lg shadow-xl flex justify-between items-center mb-6">
                    <span class="text-xl font-semibold">TOTAL DA VENDA:</span>
                    <span id="totalDisplay" class="text-4xl font-extrabold">R$ 0,00</span>
                </div>

                <button type="submit" class="w-full flex justify-center py-3 px-4 rounded-lg shadow-lg text-lg font-bold text-white bg-green-600 hover:bg-green-700 focus:outline-none focus:ring-4 focus:ring-offset-2 focus:ring-green-300">
                    Concluir Venda e Gerar WhatsApp
                </button>
            </div>
        </form>

        <!-- Área de Resultado (Link WhatsApp) -->
        <div id="resultArea" class="hidden mt-10 p-6 bg-green-50 border border-green-200 rounded-xl shadow-lg">
            <h3 class="text-xl font-bold text-green-700 mb-4 flex items-center">
                <!-- Ícone de Check/Sucesso -->
                <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="w-6 h-6 mr-3">
                    <path d="M22 13v-3a8 8 0 0 0-8-8 8 8 0 0 0-8 8v3"></path><path d="M14 2h6a2 2 0 0 1 2 2v16a2 2 0 0 1-2 2h-6"></path><path d="M10 2h-6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h6"></path>
                </svg>
                Venda Registrada e Mensagem Pronta!
            </h3>
            <p class="mb-4 text-sm text-gray-700">O link foi gerado. Clique para abrir a conversa no WhatsApp.</p>

            <a id="whatsappLink" target="_blank" class="block text-center py-3 px-4 rounded-lg shadow-xl text-base font-bold text-white bg-blue-600 hover:bg-blue-700 focus:outline-none focus:ring-4 focus:ring-offset-2 focus:ring-blue-400">
                Abrir WhatsApp para o Cliente
            </a>
            
            <div class="mt-6">
                 <p class="text-sm font-medium text-gray-700">Mensagem gerada (prévia):</p>
                 <textarea id="messagePreview" rows="8" readonly class="w-full text-xs p-3 border border-gray-300 rounded-md mt-2 bg-white text-gray-800 shadow-inner"></textarea>
            </div>
        </div>

        ---

        <!-- Painel de Vendas (Gráfico) -->
        <section class="mt-12 pt-6 border-t border-gray-200">
            <h2 class="text-2xl font-bold text-gray-800 mb-4 flex items-center">
                 <!-- Ícone de Gráfico -->
                 <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="w-5 h-5 mr-2 text-indigo-700"><path d="M3 3v18h18"/><path d="M18.7 8l-5.7 5.7-2.3-2.3-7.2 7.2"/></svg>
                Análise de Desempenho (7 Dias)
            </h2>
            <div class="bg-white p-5 rounded-xl shadow-2xl border border-gray-100">
                <canvas id="salesChart"></canvas>
            </div>
        </section>

        <!-- Histórico de Vendas Recentes (Tabela) -->
        <section class="mt-12 pt-6 border-t border-gray-200">
            <h2 class="text-2xl font-bold text-gray-800 mb-4 flex items-center">
                <!-- Ícone de Lista/Histórico -->
                <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="w-5 h-5 mr-2 text-indigo-700"><rect x="3" y="3" width="7" height="7"/><rect x="14" y="3" width="7" height="7"/><rect x="14" y="14" width="7" height="7"/><rect x="3" y="14" width="7" height="7"/></svg>
                Últimas 5 Vendas Registradas
            </h2>
            <div class="bg-white rounded-xl shadow-2xl border border-gray-100 overflow-hidden">
                <table class="min-w-full divide-y divide-gray-200">
                    <thead class="bg-gray-50">
                        <tr>
                            <th scope="col" class="px-3 py-3 text-left text-xs font-bold text-gray-600 uppercase tracking-wider w-1/6">Data</th>
                            <th scope="col" class="px-3 py-3 text-left text-xs font-bold text-gray-600 uppercase tracking-wider w-1/2">Cliente</th>
                            <th scope="col" class="px-3 py-3 text-right text-xs font-bold text-gray-600 uppercase tracking-wider w-1/3">Total</th>
                        </tr>
                    </thead>
                    <tbody id="salesHistoryBody" class="bg-white divide-y divide-gray-200">
                        <!-- Linhas da tabela serão inseridas aqui via JavaScript -->
                    </tbody>
                </table>
            </div>
            <p class="text-xs text-gray-500 mt-3 text-center">Os dados são salvos localmente (no seu navegador) e atualizam em tempo real.</p>
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
        const salesHistoryBody = document.getElementById('salesHistoryBody'); // Novo elemento

        
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
            
            // Formato brasileiro
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
        
        // --- Funções de Armazenamento e Gráfico/Tabela ---

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
                    total: sale.total,
                    name: sale.name, // Novo campo
                    item: sale.item  // Novo campo
                }));
            } catch (error) {
                console.error("Erro ao carregar histórico de vendas:", error);
                return [];
            }
        }

        /**
         * Salva uma nova venda no histórico e atualiza o localStorage.
         * Foi atualizada para receber o nome e item da venda.
         * @param {number} totalValue - Valor total da venda.
         * @param {string} clientName - Nome do cliente.
         * @param {string} toyDescription - Descrição do item (brinquedo).
         */
        function saveSale(totalValue, clientName, toyDescription) {
            const history = loadSalesHistory();
            const newSale = {
                date: new Date().toISOString(), // Salva a data em formato ISO string
                total: totalValue,
                name: clientName,
                item: toyDescription 
            };
            // Adiciona a nova venda
            history.push(newSale);
            // Salva no localStorage
            localStorage.setItem(STORAGE_KEY, JSON.stringify(history));
        }

        /**
         * Agrupa o histórico de vendas por dia e totaliza (para o gráfico).
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
                // Verifica se a venda tem um objeto Date válido antes de continuar
                if (sale.date instanceof Date && !isNaN(sale.date)) {
                    const saleDateKey = sale.date.toISOString().substring(0, 10);
                    if (dailyTotals.hasOwnProperty(saleDateKey)) {
                        dailyTotals[saleDateKey] += sale.total;
                    }
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
         * Renderiza o histórico de vendas na tabela.
         */
        function renderSalesHistoryTable() {
            const history = loadSalesHistory();
            // Ordena pela data (mais recente primeiro)
            const sortedHistory = history.sort((a, b) => new Date(b.date) - new Date(a.date));
            
            // Limpa o conteúdo anterior
            salesHistoryBody.innerHTML = '';
            
            // Exibe apenas as últimas 5 transações
            const transactionsToDisplay = sortedHistory.slice(0, 5);

            if (transactionsToDisplay.length === 0) {
                salesHistoryBody.innerHTML = '<tr><td colspan="3" class="p-4 text-center text-sm text-gray-500 bg-white">Nenhuma venda registrada ainda.</td></tr>';
                return;
            }

            transactionsToDisplay.forEach(sale => {
                // Formata a data e o valor
                const saleDate = new Date(sale.date);
                const formattedDate = saleDate.toLocaleDateString('pt-BR', { day: '2-digit', month: '2-digit' });
                const formattedTotal = sale.total.toFixed(2).replace('.', ',');
                
                // Trunca o nome do cliente se for muito longo
                const clientName = sale.name && sale.name.length > 25 ? sale.name.substring(0, 22) + '...' : sale.name;

                const row = document.createElement('tr');
                row.className = 'border-b hover:bg-gray-50';
                
                row.innerHTML = `
                    <td class="p-3 text-sm text-gray-500">${formattedDate}</td>
                    <td class="p-3 font-medium text-gray-800">${clientName || 'Cliente sem nome'}</td>
                    <td class="p-3 text-sm font-semibold text-right text-indigo-700 whitespace-nowrap">R$ ${formattedTotal}</td>
                `;
                salesHistoryBody.appendChild(row);
            });
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
                        // Cor do gráfico mais escura e profissional
                        backgroundColor: 'rgba(79, 70, 229, 0.8)', // Indigo-600 com opacidade
                        borderColor: 'rgb(79, 70, 229)',
                        borderWidth: 1,
                        borderRadius: 4,
                    }]
                },
                options: {
                    responsive: true,
                    scales: {
                        y: {
                            beginAtZero: true,
                            title: {
                                display: true,
                                text: 'Valor (R$)',
                                color: '#4b5563' // gray-600
                            },
                            ticks: {
                                color: '#4b5563', // gray-600
                                callback: function(value) {
                                    return 'R$ ' + value.toFixed(2).replace('.', ',');
                                }
                            },
                            grid: {
                                color: '#f3f4f6' // very light gray grid lines
                            }
                        },
                        x: {
                            ticks: {
                                color: '#4b5563'
                            },
                            grid: {
                                display: false
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

            // 2. Calcula o valor final e pega informações do cliente
            const total = calculateTotal();
            const clientName = clientNameInput.value.trim();
            const toyDescription = toyValueSelect.options[toyValueSelect.selectedIndex].text;

            // 3. Salva a venda e atualiza os painéis
            saveSale(total, clientName, toyDescription);
            renderSalesChart();
            renderSalesHistoryTable(); // Atualiza a tabela
            
            // 4. Gera a URL e o texto da mensagem
            const { url, text } = generateWhatsAppLink(total);

            // 5. Atualiza a área de resultado no HTML
            whatsappLink.href = url;
            messagePreview.value = text;
            whatsappLink.textContent = `Abrir WhatsApp para ${clientName}`;
            
            // 6. Exibe a área de resultado e rola para ela
            resultArea.classList.remove('hidden');
            resultArea.scrollIntoView({ behavior: 'smooth' });

            // 7. Limpa o formulário (opcional, mas recomendado para nova venda)
            salesForm.reset();
            calculateTotal(); // Recalcula o total para R$ 0,00
            
            console.log('--- Venda Registrada e Painéis Atualizados ---');
        });

        // Inicializa o cálculo do total, o gráfico e a tabela na abertura
        calculateTotal();
        renderSalesChart();
        renderSalesHistoryTable();

    </script>

</body>
</html>
