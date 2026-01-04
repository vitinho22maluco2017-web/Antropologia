<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Antropologia Adventure Quest</title>
    <style>
        body { font-family: Arial, sans-serif; text-align: center; background: linear-gradient(to bottom, #87CEEB, #98FB98); margin: 0; padding: 0; }
        #game { max-width: 900px; margin: auto; padding: 20px; border: 1px solid #ccc; background: white; box-shadow: 0 0 10px rgba(0,0,0,0.1); }
        #scene { position: relative; height: 300px; background: url('https://via.placeholder.com/900x300?text=Mapa+Mundial+Antropológico') no-repeat center; background-size: cover; border: 2px solid #333; margin-bottom: 20px; }
        #avatar { position: absolute; bottom: 10px; left: 50px; width: 50px; height: 50px; background: url('https://via.placeholder.com/50x50?text=Explorador') no-repeat center; background-size: cover; transition: left 1s ease; }
        #npc { position: absolute; bottom: 10px; right: 50px; width: 50px; height: 50px; background: url('https://via.placeholder.com/50x50?text=Hominídeo') no-repeat center; background-size: cover; display: none; }
        button { margin: 10px; padding: 10px; background: #4CAF50; color: white; border: none; cursor: pointer; border-radius: 5px; }
        button:hover { background: #45a049; transform: scale(1.05); transition: 0.2s; }
        #question { font-size: 18px; margin: 20px; }
        #options { display: flex; flex-direction: column; align-items: center; }
        #score { font-size: 20px; color: blue; }
        #feedback { font-size: 16px; margin: 10px; color: green; animation: fadeIn 0.5s; }
        #image { width: 300px; height: 200px; margin: 10px auto; border: 1px solid #ccc; display: block; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        @keyframes jump { 0%, 100% { transform: translateY(0); } 50% { transform: translateY(-10px); } }
        .correct { animation: jump 0.5s; }
    </style>
</head>
<body>
    <div id="game">
        <h1>Antropologia Adventure Quest</h1>
        <p id="score">Pontos: 0 | Pergunta: 1/900 | Região: Base Antropológica</p>
        <div id="scene">
            <div id="avatar"></div>
            <div id="npc"></div>
        </div>
        <button id="start">Iniciar Aventura</button>
        <div id="quiz" style="display: none;">
            <img id="image" src="" alt="Imagem ilustrativa">
            <p id="question"></p>
            <div id="options"></div>
            <p id="feedback"></p>
        </div>
        <button id="next" style="display: none;">Próxima Região</button>
    </div>

    <script>
        const regions = ["Base Antropológica", "África", "América", "Ásia", "Europa", "Oceania"];
        const regionImages = {
            "Base Antropológica": "https://via.placeholder.com/300x200?text=Base+Antropológica",
            "África": "https://upload.wikimedia.org/wikipedia/commons/thumb/5/5a/Lucy_reconstruction.jpg/300px-Lucy_reconstruction.jpg", // Imagem real de Lucy
            "América": "https://upload.wikimedia.org/wikipedia/commons/thumb/0/0e/Totem_pole.jpg/300px-Totem_pole.jpg", // Totem
            "Ásia": "https://upload.wikimedia.org/wikipedia/commons/thumb/4/4f/Claude_L%C3%A9vi-Strauss.jpg/300px-Claude_L%C3%A9vi-Strauss.jpg", // Lévi-Strauss
            "Europa": "https://upload.wikimedia.org/wikipedia/commons/thumb/3/3c/Human_evolution_diagram.svg/300px-Human_evolution_diagram.svg.png", // Evolução
            "Oceania": "https://upload.wikimedia.org/wikipedia/commons/thumb/5/5f/Margaret_Mead.jpg/300px-Margaret_Mead.jpg" // Mead
        };
        // Banco de 900 perguntas (exemplo com 10; expanda para 900 como antes)
        const questions = [
            { q: "O que é antropologia?", a: ["Estudo de insetos", "Estudo do homem e suas culturas", "Estudo de estrelas", "Estudo de plantas"], correct: 1, img: "https://via.placeholder.com/300x200?text=Antropologia" },
            { q: "Quem é considerado o pai da antropologia moderna?", a: ["Franz Boas", "Aristóteles", "Galileu", "Newton"], correct: 0, img: "https://upload.wikimedia.org/wikipedia/commons/thumb/4/4c/Franz_Boas.jpg/300px-Franz_Boas.jpg" },
            { q: "O que é etnocentrismo?", a: ["Julgar outras culturas pelos próprios padrões", "Estudar culturas antigas", "Viajar pelo mundo", "Coletar artefatos"], correct: 0, img: "https://via.placeholder.com/300x200?text=Etnocentrismo" },
            { q: "Qual é o foco da antropologia física?", a: ["Evolução humana e biologia", "Línguas antigas", "Economias modernas", "Religiões"], correct: 0, img: "https://upload.wikimedia.org/wikipedia/commons/thumb/3/3c/Human_evolution_diagram.svg/300px-Human_evolution_diagram.svg.png" },
            { q: "O que é um hominídeo?", a: ["Um tipo de macaco", "Ancestral humano", "Uma ferramenta", "Uma cultura"], correct: 1, img: "https://upload.wikimedia.org/wikipedia/commons/thumb/5/5a/Lucy_reconstruction.jpg/300px-Lucy_reconstruction.jpg" },
            { q: "Quem descobriu o fóssil de Lucy?", a: ["Donald Johanson", "Margaret Mead", "Claude Lévi-Strauss", "Edward Tylor"], correct: 0, img: "https://upload.wikimedia.org/wikipedia/commons/thumb/5/5a/Lucy_reconstruction.jpg/300px-Lucy_reconstruction.jpg" },
            { q: "O que é relativismo cultural?", a: ["Todas as culturas são iguais", "Entender culturas em seu contexto", "Ignorar diferenças", "Julgar culturas"], correct: 1, img: "https://via.placeholder.com/300x200?text=Relativismo+Cultural" },
            { q: "Qual subárea da antropologia estuda línguas?", a: ["Antropologia linguística", "Arqueologia", "Antropologia social", "Antropologia física"], correct: 0, img: "https://via.placeholder.com/300x200?text=Línguas" },
            { q: "O que é arqueologia?", a: ["Estudo de estrelas", "Escavação de artefatos antigos", "Análise de economias", "Estudo de animais"], correct: 1, img: "https://upload.wikimedia.org/wikipedia/commons/thumb/0/0e/Archaeological_site.jpg/300px-Archaeological_site.jpg" },
            { q: "Quem foi Margaret Mead?", a: ["Uma arqueóloga", "Uma antropóloga cultural", "Uma física", "Uma historiadora"], correct: 1, img: "https://upload.wikimedia.org/wikipedia/commons/thumb/5/5f/Margaret_Mead.jpg/300px-Margaret_Mead.jpg" },
            // ... (Adicione até 900, cada uma com 'img' para imagem ilustrativa)
        ];

        let currentRegion = 0;
        let currentQuestionIndex = 0;
        let score = 0;
        let questionCount = 1;

        document.getElementById('start').addEventListener('click', startGame);
        document.getElementById('next').addEventListener('click', nextRegion);

        function startGame() {
            document.getElementById('start').style.display = 'none';
            loadRegion();
        }

        function loadRegion() {
            document.getElementById('scene').style.backgroundImage = `url('${regionImages[regions[currentRegion]]}')`;
            document.getElementById('score').textContent = `Pontos: ${score} | Pergunta: ${questionCount}/900 | Região: ${regions[currentRegion]}`;
            currentQuestionIndex = 0;
            loadQuestion();
        }

        function loadQuestion() {
            if (currentQuestionIndex >= questions.length) {
                currentQuestionIndex = 0; // Repete
            }
            const q = questions[currentQuestionIndex];
            document.getElementById('image').src = q.img;
            document.getElementById('question').textContent = q.q;
            const optionsDiv = document.getElementById('options');
            optionsDiv.innerHTML = '';
            q.a.forEach((opt, i) => {
                const btn = document.createElement('button');
                btn.textContent = opt;
                btn.addEventListener('click', () => checkAnswer(i, q.correct));
                optionsDiv.appendChild(btn);
            });
            document.getElementById('quiz').style.display = 'block';
            document.getElementById('feedback').textContent = '';
        }

        function checkAnswer(selected, correct) {
            const avatar = document.getElementById('avatar');
            const npc = document.getElementById('npc');
            const feedback = document.getElementById('feedback');
            if (selected === correct) {
                score += 10;
                feedback.textContent = 'Correto! O avatar avança!';
                feedback.style.color = 'green';
                avatar.style.left = `${parseInt(avatar.style.left || 50) + 100}px`; // Move avatar
                avatar.classList.add('correct');
                setTimeout(() => avatar.classList.remove('correct'), 500);
                npc.style.display = 'block'; // Mostra NPC de sucesso
            } else {
                score -= 5;
                feedback.textContent = 'Errado! O avatar recua.';
                feedback.style.color = 'red';
                avatar.style.left = `${Math.max(50, parseInt(avatar.style.left || 50) - 50)}px`; // Recua
                npc.style.display = 'none';
            }
            document.getElementById('score').textContent = `Pontos: ${score} | Pergunta: ${questionCount}/900 | Região: ${regions[currentRegion]}`;
            document.getElementById('next').style.display = 'block';
        }

        function nextRegion() {
            currentRegion = (currentRegion + 1) % regions.length;
            questionCount++;
            document.getElementById('next').style.display = 'none';
            loadRegion();
        }
    </script>
</body>
</html>
