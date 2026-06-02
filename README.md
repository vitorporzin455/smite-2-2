# smite-2-2
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Smite 2 Build Builder</title>
    <style>
        /* --- ESTILOS CSS --- */
        :root {
            --bg-color: #0f1115;
            --card-bg: #1a1f29;
            --accent-color: #ffd700;
            --text-color: #f5f5f5;
            --text-dim: #b3b3b3;
        }

        body {
            margin: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--bg-color);
            color: var(--text-color);
        }

        header {
            background-color: #0b0d10;
            padding: 20px;
            text-align: center;
            border-bottom: 2px solid var(--accent-color);
        }

        header h1 {
            margin: 0 0 15px 0;
            color: var(--accent-color);
        }

        nav button {
            background: transparent;
            border: 1px solid var(--accent-color);
            color: var(--accent-color);
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
            margin: 0 5px;
            transition: 0.3s;
            border-radius: 4px;
        }

        nav button:hover {
            background-color: var(--accent-color);
            color: var(--bg-color);
        }

        .search-container {
            margin-top: 15px;
        }

        #searchBar {
            width: 80%;
            max-width: 400px;
            padding: 12px;
            border-radius: 5px;
            border: 1px solid #2d3545;
            background-color: #222;
            color: white;
            font-size: 16px;
        }

        main {
            padding: 20px;
        }

        .grid-section {
            max-width: 1200px;
            margin: 0 auto;
        }

        .grid-container {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
            gap: 20px;
            margin-top: 20px;
        }

        .card {
            background-color: var(--card-bg);
            border-radius: 8px;
            padding: 20px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.3);
            border: 1px solid #2d3545;
        }

        .card h3 {
            margin-top: 0;
            color: var(--accent-color);
            font-size: 22px;
        }

        .card .classe {
            font-style: italic;
            color: var(--text-dim);
            margin-bottom: 12px;
            font-size: 14px;
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        .card .passiva-titulo {
            font-weight: bold;
            color: #fff;
            margin-top: 15px;
            border-top: 1px solid #2d3545;
            padding-top: 10px;
        }

        .card .passiva-desc {
            font-size: 14px;
            color: var(--text-dim);
            margin-top: 5px;
            line-height: 1.4;
        }

        .hidden {
            display: none;
        }
    </style>
</head>
<body>

    <header>
        <h1>Smite 2 Builder</h1>
        <nav>
            <button onclick="showSection('deuses')">Deuses</button>
            <button onclick="showSection('itens')">Itens</button>
        </nav>
        <div class="search-container">
            <input type="text" id="searchBar" placeholder="Buscar deus ou item..." oninput="filtrarConteudo()">
        </div>
    </header>

    <main>
        <section id="sec-deuses" class="grid-section">
            <h2>Deuses</h2>
            <div id="lista-deuses" class="grid-container"></div>
        </section>

        <section id="sec-itens" class="grid-section hidden">
            <h2>Itens</h2>
            <div id="lista-itens" class="grid-container"></div>
        </section>
    </main>

    <script>
        // BANCO DE DADOS: Adicione novos deuses e itens diretamente aqui dentro dos colchetes []
        const deuses = [
            { nome: "Anúbis", classe: "Mago", passivaNome: "Sorrow", passivaDesc: "Ganha roubo de vida aumentado. Causar dano rouba Proteções Físicas e Mágicas do alvo." },
            { nome: "Baco", classe: "Guardião", passivaNome: "Chug", passivaDesc: "Beber do jarro enche o medidor. Quanto mais bêbado, mais mitigação de dano e poder recebe." },
            { nome: "Ymir", classe: "Guardião", passivaNome: "Frostbite", passivaDesc: "Habilidades aplicam gelo. Ataques básicos causam 100% a mais de dano a alvos congelados." },
            { nome: "Belona", classe: "Guerreiro", passivaNome: "Master of War", passivaDesc: "Acertar ou receber ataques básicos concede velocidade de movimento e proteções temporárias." },
            { nome: "Loki", classe: "Assassino", passivaNome: "Behind You", passivaDesc: "Causa dano adicional pelas costas ao desferir ataques básicos ou habilidades em inimigos." }
            // Continue adicionando os outros deuses aqui seguindo a mesma estrutura...
        ];

        const itens = [
            { nome: "Garra de Bancroft", tipo: "Poder Mágico / Roubo de Vida", passivaNome: "Foco de Bancroft", passivaDesc: "Ganha Poder Mágico e Roubo de Vida adicional baseado na quantidade de Vida perdida." },
            { nome: "Executador", tipo: "Velocidade de Ataque / Poder Físico", passivaNome: "Perfuração", passivaDesc: "Ataques básicos reduzem a Proteção Física do alvo em 7% por 3 segundos (acumula até 4 vezes)." },
            { nome: "Soberania", tipo: "Defesa Física / Vida", passivaNome: "Aura de Ferro", passivaDesc: "Concede uma aura que aumenta a Proteção Física e a Regeneração de Vida de aliados próximos." }
            // Continue adicionando os outros itens aqui seguindo a mesma estrutura...
        ];

        // Função para construir os cards na tela
        function renderizarConteudo() {
            const containerDeuses = document.getElementById('lista-deuses');
            const containerItens = document.getElementById('lista-itens');

            containerDeuses.innerHTML = deuses.map(deus => `
                <div class="card" data-nome="${deus.nome.toLowerCase()}">
                    <h3>${deus.nome}</h3>
                    <div class="classe">${deus.classe}</div>
                    <div class="passiva-titulo">Passiva: ${deus.passivaNome}</div>
                    <div class="passiva-desc">${deus.passivaDesc}</div>
                </div>
            `).join('');

            containerItens.innerHTML = itens.map(item => `
                <div class="card" data-nome="${item.nome.toLowerCase()}">
                    <h3>${item.nome}</h3>
                    <div class="classe">${item.tipo}</div>
                    <div class="passiva-titulo">Passiva: ${item.passivaNome}</div>
                    <div class="passiva-desc">${item.passivaDesc}</div>
                </div>
            `).join('');
        }

        // Alternar entre Abas
        function showSection(secao) {
            if (secao === 'deuses') {
                document.getElementById('sec-deuses').classList.remove('hidden');
                document.getElementById('sec-itens').classList.add('hidden');
            } else {
                document.getElementById('sec-deuses').classList.add('hidden');
                document.getElementById('sec-itens').classList.remove('hidden');
            }
            document.getElementById('searchBar').value = '';
            filtrarConteudo();
        }

        // Sistema de busca inteligente
        function filtrarConteudo() {
            const filtro = document.getElementById('searchBar').value.toLowerCase();
            const cards = document.querySelectorAll('.card');

            cards.forEach(card => {
                const nome = card.getAttribute('data-nome');
                card.style.display = nome.includes(filtro) ? "block" : "none";
            });
        }

        // Executa ao abrir a página
        window.onload = renderizarConteudo;
    </script>
</body>
</html>
