<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FFH4X LITE (Corrected Final Version)</title>
    <style>
        /* Importação de fonte e ícones */
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&display=swap');
        @import url('https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css');

        :root {
            --cor-fundo: #121212;
            --cor-painel: rgba(30, 30, 30, 0.9);
            --cor-destaque: #8a2be2;
            --cor-texto-primaria: #ffffff;
            --cor-texto-secundaria: #a0a0a0;
            --cor-borda: #333333;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; }

        body {
            font-family: 'Inter', sans-serif;
            background-image: url('https://i.ytimg.com/vi/S8LV_v95m3A/maxresdefault.jpg');
            background-size: cover;
            background-position: center;
            min-height: 100vh;
            overflow: hidden;
            user-select: none; 
            -webkit-user-select: none;
        }

        .painel-container {
            width: 450px;
            background-color: var(--cor-painel);
            backdrop-filter: blur(10px);
            border-radius: 8px;
            border: 1px solid var(--cor-borda);
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.6);
            display: flex;
            position: absolute;
            top: 100px;
            left: 100px;
            z-index: 100;
            transition: opacity 0.4s ease, transform 0.4s ease, filter 0.4s ease, box-shadow 0.4s ease;
        }

        .painel-container.stream-proof {
            opacity: 0.25;
            filter: grayscale(80%);
            box-shadow: none;
        }
        .painel-container.stream-proof:hover {
            opacity: 0.8;
            filter: grayscale(0%);
        }

        .painel-container.escondido {
            opacity: 0;
            transform: scale(0.95);
            pointer-events: none;
        }

        .nav-lateral {
            width: 60px;
            background-color: rgba(0, 0, 0, 0.2);
            padding: 10px 0;
            display: flex;
            flex-direction: column;
            align-items: center;
            border-right: 1px solid var(--cor-borda);
        }

        .nav-btn {
            width: 40px; height: 40px; margin-bottom: 10px; background-color: var(--cor-destaque);
            border: none; border-radius: 8px; color: white; font-size: 18px; cursor: pointer;
            display: flex; justify-content: center; align-items: center;
            transition: transform 0.2s ease, box-shadow 0.2s ease;
        }
        .nav-btn:hover { transform: scale(1.1); box-shadow: 0 0 10px var(--cor-destaque); }
        .nav-btn.ativo {
             box-shadow: 0 0 15px var(--cor-destaque), inset 0 0 5px rgba(0,0,0,0.5);
             transform: scale(1.05);
        }

        .conteudo-principal { width: 100%; display: flex; flex-direction: column; }

        .painel-header {
            display: flex; justify-content: space-between; align-items: center;
            padding: 10px 15px; background-color: var(--cor-destaque); cursor: move;
        }
        .header-titulo { font-weight: 600; font-size: 16px; }
        .header-titulo i { margin-right: 8px; }
        .btn-fechar { background: none; border: none; color: white; font-size: 18px; cursor: pointer; transition: transform 0.3s ease; }
        .btn-fechar:hover { transform: rotate(90deg); }

        .painel-body { padding: 20px; flex-grow: 1; }
        
        .aba-conteudo { display: none; }
        .aba-conteudo.ativa { display: block; }

        .grid-controles { display: grid; grid-template-columns: 1fr 1fr; gap: 20px 15px; align-items: center; }
        .controle-checkbox { display: flex; align-items: center; cursor: pointer; font-size: 15px; }
        .checkbox-input { display: none; }
        .checkbox-box {
            width: 20px; height: 20px; background-color: var(--cor-painel);
            border: 2px solid var(--cor-destaque); border-radius: 4px; margin-right: 10px;
            display: flex; justify-content: center; align-items: center; transition: background-color 0.2s;
        }
        .checkbox-box i { display: none; color: white; font-size: 12px; }
        .checkbox-input:checked + .checkbox-box { background-color: var(--cor-destaque); }
        .checkbox-input:checked + .checkbox-box i { display: block; }

        .controle-slider { display: flex; align-items: center; background-color: #2a2a2a; border-radius: 6px; padding: 5px 10px; }
        .slider-valor { font-weight: 500; margin-left: 10px; color: var(--cor-texto-secundaria); }
        input[type="range"] { -webkit-appearance: none; width: 100%; height: 20px; background: transparent; outline: none; }
        input[type="range"]::-webkit-slider-thumb { -webkit-appearance: none; width: 20px; height: 20px; background: var(--cor-destaque); border-radius: 4px; cursor: pointer; }

        .controle-select { position: relative; display: flex; align-items: center; }
        .select-box {
            width: 100%; padding: 8px 12px; background-color: var(--cor-destaque);
            border-radius: 6px; color: white; font-weight: 500; cursor: pointer;
            display: flex; justify-content: space-between; align-items: center;
        }
        .select-box i { transition: transform 0.3s ease; }
        .select-opcoes {
            display: none; position: absolute; top: 110%; left: 0; right: 0;
            background-color: #2a2a2a; border: 1px solid var(--cor-borda);
            border-radius: 6px; z-index: 10; overflow: hidden;
        }
        .select-opcoes.aberto { display: block; }
        .opcao { padding: 10px 12px; cursor: pointer; }
        .opcao:hover { background-color: var(--cor-destaque); }

        /* --- ELEMENTOS VISUAIS PARA SIMULAÇÃO --- */
        .fov-circle {
            position: fixed; top: 50%; left: 50%; border: 2px solid var(--cor-destaque);
            border-radius: 50%; transform: translate(-50%, -50%); pointer-events: none;
            display: none; box-shadow: 0 0 15px var(--cor-destaque);
        }
        .esp-box {
            position: fixed; border: 2px solid var(--cor-destaque); border-radius: 4px;
            pointer-events: none; display: none; color: white; font-size: 12px;
            text-shadow: 0 0 5px black; padding: 2px 5px;
        }
        .status-notificacao {
            position: fixed; bottom: 20px; left: 50%; transform: translateX(-50%);
            background-color: rgba(0,0,0,0.8); color: white; padding: 10px 20px;
            border-radius: 8px; border-top: 2px solid var(--cor-destaque); font-size: 14px;
            z-index: 200; opacity: 0; transition: opacity 0.5s, transform 0.5s; pointer-events: none;
        }
        .status-notificacao.visivel { opacity: 1; transform: translateX(-50%) translateY(-20px); }
    </style>
</head>
<body>

    <!-- Elementos visuais que serão controlados pelo JS -->
    <div class="fov-circle" id="fovCircle"></div>
    <div class="esp-box" id="espBox1" style="width: 80px; height: 150px; top: 30%; left: 70%;">Player1 [150m]</div>
    <div class="esp-box" id="espBox2" style="width: 70px; height: 140px; top: 50%; left: 20%;">Player2 [80m]</div>
    <div class="status-notificacao" id="statusNotificacao"></div>

    <div class="painel-container" id="painel">
        <nav class="nav-lateral">
            <button class="nav-btn ativo" data-aba="aim"><i class="fa-solid fa-crosshairs"></i></button>
            <button class="nav-btn" data-aba="esp"><i class="fa-solid fa-eye"></i></button>
            <button class="nav-btn" data-aba="misc"><i class="fa-solid fa-cog"></i></button>
        </nav>

        <main class="conteudo-principal">
            <header class="painel-header" id="painelHeader">
                <div class="header-titulo"><i class="fa-solid fa-angle-down"></i><span>FFH4X LITE</span></div>
                <button class="btn-fechar" id="btnFechar"><i class="fa-solid fa-times"></i></button>
            </header>

            <div class="painel-body">
                <!-- ABA AIM (MIRA) -->
                <div class="aba-conteudo ativa" id="aba-aim">
                    <div class="grid-controles">
                        <label class="controle-checkbox"><input type="checkbox" class="checkbox-input" id="checkAimbot"><span class="checkbox-box"><i class="fa-solid fa-check"></i></span>Ativar Aimbot</label>
                        <label class="controle-checkbox"><input type="checkbox" class="checkbox-input" id="checkFov"><span class="checkbox-box"><i class="fa-solid fa-check"></i></span>Exibir FOV</label>
                        <div class="controle-slider"><input type="range" min="0" max="100" value="20" class="slider-input" id="sliderFov"><span class="slider-valor">20.0</span></div>
                        <span>Regular FOV</span>
                        <span>Cabeça</span>
                        <div class="controle-select">
                            <div class="select-box" id="select-alvo"><span id="valor-selecionado">Alvo</span><i class="fa-solid fa-angle-down"></i></div>
                            <div class="select-opcoes" id="opcoes-alvo">
                                <div class="opcao" data-valor="Cabeça">Cabeça</div><div class="opcao" data-valor="Pescoço">Pescoço</div><div class="opcao" data-valor="Peito">Peito</div>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- ABA ESP (VISUALS) -->
                <div class="aba-conteudo" id="aba-esp">
                    <div class="grid-controles">
                        <!-- CHECKBOX "ATIVAR ESP" CORRIGIDO E NO LUGAR CERTO -->
                        <label class="controle-checkbox"><input type="checkbox" class="checkbox-input" id="checkEsp"><span class="checkbox-box"><i class="fa-solid fa-check"></i></span>Ativar ESP</label>
                        <span></span> <!-- Espaço vazio para alinhar -->
                        <label class="controle-checkbox"><input type="checkbox" class="checkbox-input"><span class="checkbox-box"><i class="fa-solid fa-check"></i></span>ESP Linha</label>
                        <label class="controle-checkbox"><input type="checkbox" class="checkbox-input" checked><span class="checkbox-box"><i class="fa-solid fa-check"></i></span>ESP Caixa</label>
                        <label class="controle-checkbox"><input type="checkbox" class="checkbox-input"><span class="checkbox-box"><i class="fa-solid fa-check"></i></span>ESP Nome</label>
                    </div>
                </div>

                <!-- ABA MISC (OUTROS) -->
                <div class="aba-conteudo" id="aba-misc">
                    <div class="grid-controles">
                        <label class="controle-checkbox"><input type="checkbox" class="checkbox-input" id="checkBypass"><span class="checkbox-box"><i class="fa-solid fa-check"></i></span>Ativar Bypass</label>
                        <label class="controle-checkbox"><input type="checkbox" class="checkbox-input" id="checkStreamProof"><span class="checkbox-box"><i class="fa-solid fa-check"></i></span>Ocultar da Transmissão</label>
                    </div>
                </div>
            </div>
        </main>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            const painel = document.getElementById('painel');
            const painelHeader = document.getElementById('painelHeader');
            const btnFechar = document.getElementById('btnFechar');
            
            const checkAimbot = document.getElementById('checkAimbot');
            const checkFov = document.getElementById('checkFov');
            const sliderFov = document.getElementById('sliderFov');
            const checkEsp = document.getElementById('checkEsp'); // Checkbox de ESP na sua aba
            const checkBypass = document.getElementById('checkBypass');
            const checkStreamProof = document.getElementById('checkStreamProof');

            const fovCircle = document.getElementById('fovCircle');
            const espBoxes = [document.getElementById('espBox1'), document.getElementById('espBox2')];
            const statusNotificacao = document.getElementById('statusNotificacao');
            
            const navButtons = document.querySelectorAll('.nav-btn');
            const abasConteudo = document.querySelectorAll('.aba-conteudo');

            // --- LÓGICA DE NAVEGAÇÃO POR ABAS ---
            navButtons.forEach(button => {
                button.addEventListener('click', () => {
                    navButtons.forEach(btn => btn.classList.remove('ativo'));
                    button.classList.add('ativo');

                    abasConteudo.forEach(aba => aba.classList.remove('ativa'));
                    const abaAtiva = document.getElementById('aba-' + button.dataset.aba);
                    if (abaAtiva) {
                        abaAtiva.classList.add('ativa');
                    }
                });
            });

            // --- LÓGICA DE SIMULAÇÃO ---
            function mostrarNotificacao(mensagem, duracao = 1500) {
                statusNotificacao.textContent = mensagem;
                statusNotificacao.classList.add('visivel');
                setTimeout(() => statusNotificacao.classList.remove('visivel'), duracao);
            }

            checkAimbot.addEventListener('change', () => mostrarNotificacao(checkAimbot.checked ? 'Aimbot Ativado' : 'Aimbot Desativado'));
            checkBypass.addEventListener('change', () => mostrarNotificacao(checkBypass.checked ? 'Bypass Ativado' : 'Bypass Desativado'));

            // LÓGICA CORRIGIDA PARA O ESP
            checkEsp.addEventListener('change', () => {
                const displayValue = checkEsp.checked ? 'block' : 'none';
                espBoxes.forEach(box => box.style.display = displayValue);
                mostrarNotificacao(checkEsp.checked ? 'ESP Ativado' : 'ESP Desativado');
            });

            function atualizarFov() {
                fovCircle.style.display = checkFov.checked ? 'block' : 'none';
                if(checkFov.checked) {
                    const fovValue = sliderFov.value * 5;
                    fovCircle.style.width = `${fovValue}px`;
                    fovCircle.style.height = `${fovValue}px`;
                }
            }
            checkFov.addEventListener('change', atualizarFov);
            sliderFov.addEventListener('input', () => {
                sliderFov.nextElementSibling.textContent = parseFloat(sliderFov.value).toFixed(1);
                atualizarFov();
            });

            checkStreamProof.addEventListener('change', () => {
                painel.classList.toggle('stream-proof', checkStreamProof.checked);
                mostrarNotificacao(checkStreamProof.checked ? 'Modo Transmissão Ativado' : 'Modo Transmissão Desativado');
            });

            // --- LÓGICA DO PAINEL ---
            let isDragging = false;
            let offset = { x: 0, y: 0 };
            painelHeader.addEventListener('mousedown', (e) => {
                isDragging = true;
                offset.x = e.clientX - painel.offsetLeft;
                offset.y = e.clientY - painel.offsetTop;
            });
            document.addEventListener('mousemove', (e) => {
                if (!isDragging) return;
                painel.style.left = `${e.clientX - offset.x}px`;
                painel.style.top = `${e.clientY - offset.y}px`;
            });
            document.addEventListener('mouseup', () => isDragging = false);
            
            btnFechar.addEventListener('click', () => painel.classList.add('escondido'));

            const selectAlvo = document.getElementById('select-alvo');
            const opcoesAlvo = document.getElementById('opcoes-alvo');
            const valorSelecionado = document.getElementById('valor-selecionado');
            selectAlvo.addEventListener('click', () => opcoesAlvo.classList.toggle('aberto'));
            opcoesAlvo.querySelectorAll('.opcao').forEach(opcao => {
                opcao.addEventListener('click', () => {
                    valorSelecionado.textContent = opcao.dataset.valor;
                });
            });

            // --- LÓGICA DE GESTOS ---
            let lastTap = 0;
            document.addEventListener('touchstart', (e) => {
                if (e.touches.length === 3) {
                    const currentTime = new Date().getTime();
                    if ((currentTime - lastTap) < 300) {
                        painel.classList.toggle('escondido');
                        e.preventDefault();
                    }
                    lastTap = currentTime;
                }
            }, { passive: false });
        });
    </script>

</body>
</html>
