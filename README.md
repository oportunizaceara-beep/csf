<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CSF Manager - Cloud Sync</title>
    <link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap" rel="stylesheet">

    <style>
        :root {
            /* TEMA DARK */
            --primary: #6366f1; --bg: #0f172a; --sidebar-bg: #020617; 
            --surface: #1e293b; --border: #334155; --text-main: #f1f5f9; 
            --text-sec: #94a3b8; --success: #10b981; --warning: #f59e0b; 
            --danger: #ef4444; --info: #3b82f6;
        }
        body { font-family: 'Inter', sans-serif; background-color: var(--bg); color: var(--text-main); margin: 0; padding: 0; overflow-x: hidden; }
        
        /* UTILITÁRIOS */
        .hide { display: none !important; }
        .show { display: block !important; }
        .loading-overlay { position: fixed; inset: 0; background: rgba(0,0,0,0.7); display: flex; justify-content: center; align-items: center; z-index: 2000; color: white; font-weight: bold; flex-direction: column; gap: 15px; }
        .spinner { width: 40px; height: 40px; border: 4px solid rgba(255,255,255,0.3); border-top: 4px solid var(--primary); border-radius: 50%; animation: spin 1s linear infinite; }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }

        /* LOGIN */
        .login-wrapper { position: fixed; inset: 0; background-color: var(--bg); display: flex; justify-content: center; align-items: center; z-index: 9999; }
        .login-box { background: var(--surface); padding: 40px; border-radius: 20px; border: 1px solid var(--border); width: 100%; max-width: 380px; text-align: center; box-shadow: 0 20px 25px -5px rgba(0,0,0,0.5); }

        /* LAYOUT */
        .app-wrapper { display: grid; grid-template-columns: 260px 1fr; min-height: 100vh; }
        .sidebar { background: var(--sidebar-bg); border-right: 1px solid var(--border); padding: 25px; display: flex; flex-direction: column; }
        .main-content { padding: 30px; overflow-y: auto; height: 100vh; box-sizing: border-box; }

        /* SIDEBAR */
        .brand { font-size: 20px; font-weight: 700; display: flex; align-items: center; gap: 12px; margin-bottom: 50px; color: var(--text-main); }
        .nav-item { padding: 12px 15px; border-radius: 10px; cursor: pointer; display: flex; align-items: center; gap: 12px; color: var(--text-sec); transition: 0.2s; margin-bottom: 8px; font-weight: 500; }
        .nav-item:hover, .nav-item.active { background: rgba(99, 102, 241, 0.15); color: var(--primary); }

        /* CARDS & KPI */
        .kpi-row { display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 20px; margin-bottom: 30px; }
        .kpi-card { background: var(--surface); padding: 25px; border-radius: 16px; border: 1px solid var(--border); }
        .kpi-card p { margin: 10px 0 0 0; font-size: 32px; font-weight: 700; color: var(--text-main); }
        .kpi-card h3 { margin: 0; font-size: 13px; color: var(--text-sec); text-transform: uppercase; }

        .toolbar { background: var(--surface); padding: 15px 20px; border-radius: 14px; display: flex; justify-content: space-between; align-items: center; border: 1px solid var(--border); margin-bottom: 25px; flex-wrap: wrap; gap: 15px; }
        .search-box { display: flex; align-items: center; background: var(--bg); border-radius: 8px; padding: 0 12px; border: 1px solid var(--border); flex: 1; max-width: 400px; }
        .search-box input { border: none; background: transparent; padding: 12px; width: 100%; outline: none; color: var(--text-main); }

        .cards-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(300px, 1fr)); gap: 20px; }
        .course-card { background: var(--surface); border-radius: 16px; padding: 25px; border: 1px solid var(--border); transition: transform 0.2s; cursor: pointer; }
        .course-card:hover { transform: translateY(-4px); border-color: var(--primary); }
        .status-dot { height: 10px; width: 10px; border-radius: 50%; display: inline-block; margin-right: 6px; }

        /* CALENDAR */
        .calendar-wrapper { background: var(--surface); border-radius: 16px; border: 1px solid var(--border); padding: 20px; }
        .calendar-grid { display: grid; grid-template-columns: repeat(7, 1fr); gap: 1px; background: var(--border); border: 1px solid var(--border); margin-top: 20px; }
        .cal-day-header { background: var(--surface); padding: 10px; text-align: center; font-weight: 600; font-size: 13px; color: var(--text-sec); }
        .cal-day { background: var(--bg); min-height: 140px; padding: 6px; display: flex; flex-direction: column; gap: 2px; overflow: hidden; }
        .cal-day.empty { background: var(--surface); opacity: 0.3; }
        .current-day { background: rgba(99, 102, 241, 0.05); }
        .cal-event { font-size: 10px; padding: 4px 6px; margin-bottom: 2px; border-radius: 4px; cursor: pointer; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; font-weight: 600; display: flex; align-items: center; gap: 4px; transition:0.2s; }
        .cal-event:hover { transform: scale(1.02); z-index: 10; filter: brightness(1.2); }
        .evt-start { background: rgba(16, 185, 129, 0.2); color: #6ee7b7; border-left: 3px solid #10b981; }
        .evt-end { background: rgba(239, 68, 68, 0.2); color: #fca5a5; border-left: 3px solid #ef4444; }
        .evt-middle { background: rgba(59, 130, 246, 0.1); color: #93c5fd; height: 4px; padding: 0; margin: 4px 0; border-radius: 2px; }

        /* INSTRUTORES */
        .instructors-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(300px, 1fr)); gap: 20px; }
        .instructor-card { background: var(--surface); border: 1px solid var(--border); border-radius: 16px; padding: 20px; display: flex; flex-direction: column; gap: 15px; }
        .inst-header { display: flex; align-items: center; gap: 15px; }
        .inst-avatar { width: 50px; height: 50px; background: linear-gradient(135deg, var(--primary), #818cf8); color: white; border-radius: 50%; display: flex; justify-content: center; align-items: center; font-size: 20px; font-weight: 700; }
        .inst-card-actions { display: grid; grid-template-columns: 1fr auto auto; gap: 10px; margin-top: auto; }
        .btn-icon { width: 36px; height: 36px; display: flex; justify-content: center; align-items: center; border-radius: 8px; border: 1px solid var(--border); background: transparent; color: var(--text-sec); cursor: pointer; transition: 0.2s; }
        .btn-icon:hover { background: #334155; color: var(--text-main); }
        .btn-zap-card { background: #25D366; color: white; border: none; border-radius: 8px; font-weight: 600; font-size: 13px; display: flex; align-items: center; justify-content: center; gap: 8px; text-decoration: none; transition: 0.2s; }
        
        /* COMMON */
        .btn { padding: 8px 16px; border-radius: 8px; border: none; font-weight: 600; cursor: pointer; display: inline-flex; align-items: center; gap: 6px; }
        .btn-primary { background: var(--primary); color: white; }
        .btn-outline { background: transparent; border: 1px solid var(--border); color: var(--text-sec); }
        .form-control { width: 100%; padding: 12px; margin-top: 6px; background: var(--bg); border: 1px solid var(--border); border-radius: 8px; color: var(--text-main); box-sizing: border-box; }
        .form-control:disabled { opacity: 0.6; cursor: not-allowed; border-style: dashed; }
        
        .modal-overlay { position: fixed; inset: 0; background: rgba(0,0,0,0.8); display: none; justify-content: center; align-items: center; z-index: 1000; }
        .modal-content { background: var(--surface); padding: 30px; border-radius: 16px; width: 90%; max-width: 600px; max-height: 90vh; overflow-y: auto; border: 1px solid var(--border); color: var(--text-main); }
        .form-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; }

        .st-concluido { color: var(--success); } .bg-concluido { background: var(--success); }
        .st-pendente { color: var(--warning); } .bg-pendente { background: var(--warning); }
        .st-cancelado { color: var(--danger); } .bg-cancelado { background: var(--danger); }

        @media(max-width: 768px) { .app-wrapper { grid-template-columns: 1fr; } .sidebar { display: none; } }
    </style>
</head>
<body>

<div id="loading" class="loading-overlay hide">
    <div class="spinner"></div>
    <span>Sincronizando com a Nuvem...</span>
</div>

<div id="loginScreen" class="login-wrapper">
    <div class="login-box">
        <h2 style="margin:0 0 20px 0;">CSF Manager</h2>
        <form onsubmit="window.fazerLogin(event)">
            <input type="text" id="user" class="form-control" placeholder="Usuário" required style="margin-bottom:15px;">
            <input type="password" id="pass" class="form-control" placeholder="Senha" required>
            <p style="font-size:12px; color:var(--text-sec); margin-top:20px;">
                <b>Csfiac</b> (Admin) | <b>visitante</b> (Ver)
            </p>
            <button type="submit" class="btn btn-primary" style="width:100%; margin-top:10px; padding:12px;">ACESSAR SISTEMA</button>
        </form>
        <p id="loginError" style="color:var(--danger); display:none; margin-top:10px;">Credenciais Inválidas!</p>
    </div>
</div>

<div id="app" class="app-wrapper hide">
    <aside class="sidebar">
        <div class="brand"><span class="material-icons" style="color:var(--primary)">cloud_sync</span> CSF Cloud</div>
        <nav>
            <div class="nav-item active" id="navDash" onclick="window.alternarVisao('cards')"><span class="material-icons">dashboard</span> Cursos</div>
            <div class="nav-item" id="navCal" onclick="window.alternarVisao('calendar')"><span class="material-icons">calendar_month</span> Calendário</div>
            <div class="nav-item" id="navInst" onclick="window.alternarVisao('instructors')"><span class="material-icons">manage_accounts</span> Instrutores</div>
            <div class="nav-item admin-only" id="navConfig" onclick="window.alternarVisao('config')"><span class="material-icons">settings</span> Configurações</div>
        </nav>
        <div style="margin-top:auto; padding-top:20px; border-top:1px solid var(--border);">
            <div style="font-size:14px; font-weight:600;" id="labelUser">Usuario</div>
            <div style="font-size:11px; color:var(--text-sec);" id="labelRole">Permissão</div>
            <div style="margin-top:10px; cursor:pointer; color:var(--danger); font-size:12px; display:flex; align-items:center; gap:5px;" onclick="location.reload()">
                <span class="material-icons" style="font-size:16px;">logout</span> Sair
            </div>
        </div>
    </aside>

    <main class="main-content">
        <div class="kpi-row">
            <div class="kpi-card"><h3>Total Cursos</h3><p id="kpiTotal">0</p></div>
            <div class="kpi-card"><h3>Alunos Formados</h3><p id="kpiAlunos" style="color:var(--info)">0</p></div>
            <div class="kpi-card"><h3>Concluídos</h3><p id="kpiExec" style="color:var(--success)">0</p></div>
            <div class="kpi-card"><h3>Pendentes</h3><p id="kpiPendente" style="color:var(--warning)">0</p></div>
        </div>

        <div class="toolbar" id="mainToolbar">
            <div class="search-box">
                <span class="material-icons" style="color:var(--text-sec)">search</span>
                <input type="text" id="search" placeholder="Buscar..." onkeyup="window.renderizarTudo()">
            </div>
            <div style="display:flex; gap:10px;">
                <button class="btn btn-outline" onclick="window.exportarCSV()"><span class="material-icons">download</span> CSV</button>
                <button class="btn btn-primary admin-only" onclick="window.abrirModalCurso()"><span class="material-icons">add</span> Novo Curso</button>
            </div>
        </div>

        <div id="viewCards" class="cards-grid"></div>
        
        <div id="viewCalendar" class="calendar-wrapper hide">
            <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:20px;">
                <button class="btn btn-outline" onclick="window.mudarMes(-1)"><span class="material-icons">chevron_left</span></button>
                <div class="month-label" id="calMonthYear">Mês Ano</div>
                <button class="btn btn-outline" onclick="window.mudarMes(1)"><span class="material-icons">chevron_right</span></button>
            </div>
            <div class="calendar-grid" id="calGrid"></div>
            <div style="margin-top:15px; font-size:12px; color:var(--text-sec); display:flex; gap:15px;">
                <span style="display:flex; align-items:center; gap:5px; color:#6ee7b7;"><span class="material-icons" style="font-size:14px; color:#10b981;">play_arrow</span> Início</span>
                <span style="display:flex; align-items:center; gap:5px; color:#fca5a5;"><span class="material-icons" style="font-size:14px; color:#ef4444;">sports_score</span> Término</span>
            </div>
        </div>

        <div id="viewInstructors" class="hide">
            <div class="inst-actions admin-only">
                <button class="btn btn-primary" onclick="window.abrirModalInstrutor()"><span class="material-icons">person_add</span> Novo Instrutor</button>
            </div>
            <div id="instGrid" class="instructors-grid"></div>
        </div>

        <div id="viewConfig" class="hide" style="text-align:center; padding:50px;">
            <h2 style="color:var(--text-main);">Configuração Inicial</h2>
            <p style="color:var(--text-sec); margin-bottom:20px;">Use este botão apenas se o banco de dados estiver vazio para carregar a planilha original.</p>
            <button class="btn btn-primary" onclick="window.importarDadosPadrao()" style="padding:15px 30px; font-size:16px;">
                <span class="material-icons">cloud_upload</span> IMPORTAR DADOS PADRÃO PARA NUVEM
            </button>
        </div>
    </main>
</div>

<div class="modal-overlay hide" id="modalCurso">
    <div class="modal-content">
        <div style="display:flex; justify-content:space-between; margin-bottom:20px; padding-bottom:10px; border-bottom:1px solid var(--border);">
            <h2 style="margin:0;">Dados do Curso</h2>
            <span class="material-icons" onclick="window.fecharModal('modalCurso')" style="cursor:pointer;">close</span>
        </div>
        <form id="formCurso" onsubmit="window.salvarCurso(event)">
            <input type="hidden" id="editId">
            <div class="form-grid">
                <div style="grid-column:span 2;"><label>Nome do Curso</label><input type="text" id="inCurso" class="form-control" required></div>
                <div><label>ID Lote</label><input type="number" id="inIdLote" class="form-control"></div>
                <div><label>Cidade</label><input type="text" id="inCidade" class="form-control" required></div>
                <div><label>Início</label><input type="date" id="inInicio" class="form-control"></div>
                <div><label>Término</label><input type="date" id="inFim" class="form-control"></div>
                <div>
                    <label>Status</label>
                    <select id="inStatus" class="form-control">
                        <option value="CONCLUIDO">Concluído</option>
                        <option value="EM ANDAMENTO">Em Andamento</option>
                        <option value="PENDENTE">Pendente</option>
                        <option value="AGUARDANDO">Aguardando</option>
                        <option value="SEM RETORNO">Sem Retorno</option>
                    </select>
                </div>
                <div><label>Concluintes</label><input type="number" id="inAlunos" class="form-control" value="0"></div>
                <div style="grid-column:span 2;"><label>Local</label><input type="text" id="inLocal" class="form-control"></div>
                <div style="grid-column:span 2;">
                    <label>Instrutor</label>
                    <select id="inInstrutorSelect" class="form-control"><option value="">Selecione...</option></select>
                </div>
            </div>
            <div style="margin-top:20px; display:flex; justify-content:space-between;" class="modal-btns admin-only">
                <button type="button" class="btn" style="color:var(--danger);" onclick="window.excluirCurso()">Excluir</button>
                <button type="submit" class="btn btn-primary">Salvar</button>
            </div>
        </form>
    </div>
</div>

<div class="modal-overlay hide" id="modalInstrutor">
    <div class="modal-content" style="max-width:400px;">
        <div style="display:flex; justify-content:space-between; margin-bottom:20px;">
            <h2 style="margin:0;">Instrutor</h2>
            <span class="material-icons" onclick="window.fecharModal('modalInstrutor')" style="cursor:pointer;">close</span>
        </div>
        <form id="formInstrutor" onsubmit="window.salvarInstrutor(event)">
            <input type="hidden" id="editInstId">
            <label>Nome Completo</label><input type="text" id="inInstNome" class="form-control" required>
            <label style="margin-top:15px;">WhatsApp (DDD+Número)</label><input type="text" id="inInstTel" class="form-control">
            <div style="margin-top:25px; display:flex; justify-content:flex-end; gap:10px;" class="modal-btns admin-only">
                <button type="button" class="btn btn-outline" onclick="window.fecharModal('modalInstrutor')">Cancelar</button>
                <button type="submit" class="btn btn-primary">Salvar</button>
            </div>
        </form>
    </div>
</div>

<script type="module">
    // --- 1. CONFIGURAÇÃO FIREBASE CORRIGIDA ---
    import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
    import { getFirestore, collection, getDocs, addDoc, updateDoc, deleteDoc, doc, writeBatch } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

    // AGORA ESTÁ LIMPO E CORRETO:
    const firebaseConfig = {
      apiKey: "AIzaSyBq5qmLy6HAzZAeC4KCVjLfdCM77ttOU3A",
      authDomain: "csf-iac.firebaseapp.com",
      projectId: "csf-iac",
      storageBucket: "csf-iac.firebasestorage.app",
      messagingSenderId: "484645494526",
      appId: "1:484645494526:web:305acd754a4b77a2320faf",
      measurementId: "G-PFMGTE7085"
    };

    // Inicialização segura
    let db;
    try {
        const app = initializeApp(firebaseConfig);
        db = getFirestore(app);
        console.log("Firebase conectado com sucesso!");
    } catch (e) {
        console.error("Erro Firebase (Verifique o Config):", e);
        alert("Erro: Configuração do Firebase falhou. Veja o console.");
    }

    // --- 2. DADOS PADRÃO (BACKUP) ---
    // Você pode substituir isso pela lista completa se tiver
    const defaultCourses = [
        { idLote: 964, curso: "Drinques e Coquetéis", cidade: "Itapipoca", inicio: "2025-07-07", fim: "2025-07-25", local: "Assoc. Serrote", status: "CONCLUIDO", alunos: 19, instrutor: "Thamires Brenda Santos" },
        { idLote: 1228, curso: "Sanduiches e Hambúrgueres", cidade: "Itapipoca", inicio: "2025-10-06", fim: "2025-10-10", local: "Mulheres que Alimentam", status: "CONCLUIDO", alunos: 21, instrutor: "Eronildo almeida" },
        { idLote: 1599, curso: "Eletricista Básico", cidade: "Crateús", inicio: "2025-12-01", fim: "2025-12-12", local: "Cozinha Planaltina", status: "EM ANDAMENTO", alunos: 0, instrutor: "Iranley" }
    ];
    
    // --- 3. ESTADO GLOBAL ---
    let cursos = [];
    let instrutores = [];
    let currentMonth = new Date().getMonth();
    let currentYear = new Date().getFullYear();
    let currentUserRole = null;

    const USERS = [
        { u: 'Csfiac', p: '032147', role: 'admin' },
        { u: 'visitante', p: '1234', role: 'viewer' }
    ];

    // --- 4. FUNÇÕES EXPOSTAS AO WINDOW ---
    
    window.fazerLogin = function(e) {
        e.preventDefault();
        const u = document.getElementById('user').value.trim();
        const p = document.getElementById('pass').value.trim();
        const found = USERS.find(user => user.u === u && user.p === p);
        
        if(found) {
            currentUserRole = found.role;
            document.getElementById('loginScreen').classList.add('hide');
            document.getElementById('app').classList.remove('hide');
            
            document.getElementById('labelUser').innerText = found.u;
            document.getElementById('labelRole').innerText = found.role === 'admin' ? 'Administrador' : 'Visitante';
            
            aplicarPermissoes();
            carregarDadosNuvem();
        } else {
            document.getElementById('loginError').style.display = 'block';
        }
    }

    async function carregarDadosNuvem() {
        document.getElementById('loading').classList.remove('hide');
        cursos = [];
        instrutores = [];
        
        try {
            // Buscar Cursos
            const queryC = await getDocs(collection(db, "cursos"));
            queryC.forEach((doc) => {
                cursos.push({ fireId: doc.id, ...doc.data() });
            });

            // Buscar Instrutores
            const queryI = await getDocs(collection(db, "instrutores"));
            queryI.forEach((doc) => {
                instrutores.push({ fireId: doc.id, ...doc.data() });
            });

            window.renderizarTudo();
        } catch (e) {
            console.error(e);
            alert("Erro ao baixar dados do Firebase. Verifique sua conexão.");
        } finally {
            document.getElementById('loading').classList.add('hide');
        }
    }

    // Função Especial para Popular o Banco (Executar 1 vez)
    window.importarDadosPadrao = async function() {
        if(!confirm("Isso enviará os dados de exemplo para a nuvem. Continuar?")) return;
        
        document.getElementById('loading').classList.remove('hide');
        const batch = writeBatch(db);
        
        // Adiciona cursos
        defaultCourses.forEach(c => {
            const ref = doc(collection(db, "cursos"));
            batch.set(ref, c);
        });

        // Cria instrutores unicos
        const nomesInst = [...new Set(defaultCourses.map(c => c.instrutor))];
        nomesInst.forEach(nome => {
            if(nome) {
                const ref = doc(collection(db, "instrutores"));
                batch.set(ref, { nome: nome, tel: "" });
            }
        });

        await batch.commit();
        alert("Importação concluída!");
        carregarDadosNuvem();
    }

    // --- RENDERIZAÇÃO ---
    window.renderizarTudo = function() {
        atualizarKPIs();
        renderizarCards();
        renderizarCalendario();
        renderizarInstrutores();
    }

    function atualizarKPIs() {
        document.getElementById('kpiTotal').innerText = cursos.length;
        document.getElementById('kpiExec').innerText = cursos.filter(c => c.status === 'CONCLUIDO').length;
        document.getElementById('kpiPendente').innerText = cursos.filter(c => c.status !== 'CONCLUIDO').length;
        const totalAlunos = cursos.reduce((acc, c) => c.status === 'CONCLUIDO' ? acc + (parseInt(c.alunos) || 0) : acc, 0);
        document.getElementById('kpiAlunos').innerText = totalAlunos;
    }

    function renderizarCards() {
        const grid = document.getElementById('viewCards');
        grid.innerHTML = '';
        const termo = document.getElementById('search').value.toLowerCase();
        
        cursos.forEach((c) => {
            if(!(c.curso+c.cidade+(c.instrutor||'')).toLowerCase().includes(termo)) return;
            
            let stClass = 'st-pendente'; let dotBg = 'bg-pendente';
            if(c.status === 'CONCLUIDO') { stClass = 'st-concluido'; dotBg = 'bg-concluido'; }
            if(c.status.includes('SEM') || c.status.includes('AGUARDANDO')) { stClass = 'st-cancelado'; dotBg = 'bg-cancelado'; }
            const dt = c.inicio ? fmtData(c.inicio) : 'S/ Data';
            const idDisplay = c.idLote ? `#${c.idLote}` : '# N/A';

            grid.innerHTML += `
                <div class="course-card" onclick="window.abrirModalCurso('${c.fireId}')">
                    <div style="font-size:12px; color:var(--text-sec); font-weight:700; display:flex; justify-content:space-between;">
                        <span>${idDisplay}</span> <span>${c.cidade}</span>
                    </div>
                    <h3 style="margin:10px 0; color:var(--primary); font-size:16px;">${c.curso}</h3>
                    <div style="font-size:13px; color:var(--text-sec);">
                        <div>📅 ${dt} • 👨‍🏫 ${c.instrutor || '--'}</div>
                    </div>
                    <div style="margin-top:10px; font-size:12px; font-weight:600;" class="${stClass}">
                        <span class="status-dot ${dotBg}"></span> ${c.status}
                    </div>
                </div>`;
        });
    }

    function renderizarCalendario() {
        const grid = document.getElementById('calGrid');
        grid.innerHTML = '';
        const date = new Date(currentYear, currentMonth, 1);
        const monthNames = ["Janeiro", "Fevereiro", "Março", "Abril", "Maio", "Junho", "Julho", "Agosto", "Setembro", "Outubro", "Novembro", "Dezembro"];
        document.getElementById('calMonthYear').innerText = `${monthNames[currentMonth]} ${currentYear}`;

        ['Dom', 'Seg', 'Ter', 'Qua', 'Qui', 'Sex', 'Sáb'].forEach(d => grid.innerHTML += `<div class="cal-day-header">${d}</div>`);

        const firstDayIndex = date.getDay();
        const lastDay = new Date(currentYear, currentMonth + 1, 0).getDate();
        const today = new Date();

        for(let x=0; x<firstDayIndex; x++) grid.innerHTML += `<div class="cal-day empty"></div>`;

        const sortedCursos = [...cursos].sort((a,b) => (a.idLote || 0) - (b.idLote || 0));

        for(let i=1; i<=lastDay; i++) {
            const dateStr = `${currentYear}-${String(currentMonth+1).padStart(2,'0')}-${String(i).padStart(2,'0')}`;
            const isToday = i === today.getDate() && currentMonth === today.getMonth() && currentYear === today.getFullYear();
            
            let eventsHtml = '';
            sortedCursos.forEach((c) => {
                if(c.inicio && c.fim && dateStr >= c.inicio && dateStr <= c.fim) {
                    if(dateStr === c.inicio) {
                        eventsHtml += `<div class="cal-event evt-start" onclick="event.stopPropagation(); window.abrirModalCurso('${c.fireId}')" title="Início: ${c.curso}">▶ ${c.curso}</div>`;
                    } else if(dateStr === c.fim) {
                        eventsHtml += `<div class="cal-event evt-end" onclick="event.stopPropagation(); window.abrirModalCurso('${c.fireId}')" title="Fim: ${c.curso}">🏁 ${c.curso}</div>`;
                    } else {
                        eventsHtml += `<div class="evt-middle" title="Em andamento"></div>`;
                    }
                }
            });

            grid.innerHTML += `<div class="cal-day ${isToday ? 'current-day' : ''}"><div class="day-number">${i}</div>${eventsHtml}</div>`;
        }
    }

    function renderizarInstrutores() {
        const container = document.getElementById('instGrid');
        container.innerHTML = '';
        
        if(instrutores.length === 0) {
            container.innerHTML = '<div style="color:var(--text-sec); grid-column:span 3; text-align:center;">Nenhum instrutor cadastrado.</div>';
            return;
        }

        instrutores.forEach((inst) => {
            const count = cursos.filter(c => c.instrutor === inst.nome).length;
            const zapLink = inst.tel ? `https://wa.me/55${inst.tel.replace(/\D/g,'')}` : '#';
            const zapClass = inst.tel ? '' : 'disabled';
            const initials = inst.nome ? inst.nome.substring(0,2).toUpperCase() : '??';
            
            const btnEdit = currentUserRole === 'admin' ? `<button class="btn-icon" onclick="window.abrirModalInstrutor('${inst.fireId}')"><span class="material-icons" style="font-size:18px;">edit</span></button>` : '';
            const btnDel = currentUserRole === 'admin' ? `<button class="btn-icon delete" onclick="window.excluirInstrutor('${inst.fireId}')"><span class="material-icons" style="font-size:18px;">delete</span></button>` : '';

            container.innerHTML += `
                <div class="instructor-card">
                    <div class="inst-header">
                        <div class="inst-avatar">${initials}</div>
                        <div class="inst-info">
                            <h3>${inst.nome}</h3>
                            <p>${inst.tel || 'Sem telefone'}</p>
                        </div>
                    </div>
                    <div class="inst-stats"><span>Cursos</span><strong>${count}</strong></div>
                    <div class="inst-card-actions">
                        <a href="${zapLink}" target="_blank" class="btn-zap-card ${zapClass}"><span class="material-icons" style="font-size:16px;">chat</span> WhatsApp</a>
                        ${btnEdit} ${btnDel}
                    </div>
                </div>`;
        });
    }

    // --- CRUD ---
    window.abrirModalCurso = function(fireId = null) {
        document.getElementById('formCurso').reset();
        document.getElementById('editId').value = fireId || '';
        
        // Popula Select
        const sel = document.getElementById('inInstrutorSelect');
        sel.innerHTML = '<option value="">Selecione...</option>';
        instrutores.forEach(i => sel.innerHTML += `<option value="${i.nome}">${i.nome}</option>`);

        // Bloqueio se não for admin
        const inputs = document.querySelectorAll('#formCurso input, #formCurso select');
        inputs.forEach(el => el.disabled = (currentUserRole !== 'admin'));

        if(fireId) {
            const c = cursos.find(x => x.fireId === fireId);
            if(c) {
                document.getElementById('inCurso').value = c.curso;
                document.getElementById('inIdLote').value = c.idLote;
                document.getElementById('inCidade').value = c.cidade;
                document.getElementById('inInicio').value = c.inicio;
                document.getElementById('inFim').value = c.fim;
                document.getElementById('inStatus').value = c.status;
                document.getElementById('inAlunos').value = c.alunos;
                document.getElementById('inLocal').value = c.local || '';
                sel.value = c.instrutor || '';
            }
        }
        
        const btns = document.querySelector('#modalCurso .modal-btns');
        if(currentUserRole === 'admin') btns.classList.remove('hide'); else btns.classList.add('hide');

        document.getElementById('modalCurso').classList.remove('hide');
    }

    window.salvarCurso = async function(e) {
        e.preventDefault();
        if(currentUserRole !== 'admin') return;
        document.getElementById('loading').classList.remove('hide');

        const id = document.getElementById('editId').value;
        const data = {
            curso: document.getElementById('inCurso').value,
            idLote: parseInt(document.getElementById('inIdLote').value) || 0,
            cidade: document.getElementById('inCidade').value,
            inicio: document.getElementById('inInicio').value,
            fim: document.getElementById('inFim').value,
            status: document.getElementById('inStatus').value,
            alunos: document.getElementById('inAlunos').value,
            local: document.getElementById('inLocal').value,
            instrutor: document.getElementById('inInstrutorSelect').value
        };

        try {
            if(id) {
                await updateDoc(doc(db, "cursos", id), data);
            } else {
                await addDoc(collection(db, "cursos"), data);
            }
            window.fecharModal('modalCurso');
            carregarDadosNuvem();
        } catch(err) { console.error(err); alert("Erro ao salvar."); }
    }

    window.excluirCurso = async function() {
        if(!confirm("Excluir?")) return;
        const id = document.getElementById('editId').value;
        if(id) {
            document.getElementById('loading').classList.remove('hide');
            await deleteDoc(doc(db, "cursos", id));
            window.fecharModal('modalCurso');
            carregarDadosNuvem();
        }
    }

    window.abrirModalInstrutor = function(fireId = null) {
        document.getElementById('formInstrutor').reset();
        document.getElementById('editInstId').value = fireId || '';
        
        if(fireId) {
            const i = instrutores.find(x => x.fireId === fireId);
            if(i) {
                document.getElementById('inInstNome').value = i.nome;
                document.getElementById('inInstTel').value = i.tel;
            }
        }
        document.getElementById('modalInstrutor').classList.remove('hide');
    }

    window.salvarInstrutor = async function(e) {
        e.preventDefault();
        const id = document.getElementById('editInstId').value;
        const data = {
            nome: document.getElementById('inInstNome').value,
            tel: document.getElementById('inInstTel').value
        };
        document.getElementById('loading').classList.remove('hide');
        try {
            if(id) await updateDoc(doc(db, "instrutores", id), data);
            else await addDoc(collection(db, "instrutores"), data);
            window.fecharModal('modalInstrutor');
            carregarDadosNuvem();
        } catch(err) { console.error(err); alert("Erro."); }
    }

    window.excluirInstrutor = async function(id) {
        if(!confirm("Excluir?")) return;
        document.getElementById('loading').classList.remove('hide');
        await deleteDoc(doc(db, "instrutores", id));
        carregarDadosNuvem();
    }

    // --- UTILS ---
    window.alternarVisao = function(view) {
        document.getElementById('viewCards').classList.add('hide');
        document.getElementById('viewCalendar').classList.add('hide');
        document.getElementById('viewInstructors').classList.add('hide');
        document.getElementById('viewConfig').classList.add('hide');
        document.getElementById('mainToolbar').classList.remove('hide');
        
        document.querySelectorAll('.nav-item').forEach(el => el.classList.remove('active'));

        if(view === 'cards') {
            document.getElementById('viewCards').classList.remove('hide');
            document.getElementById('navDash').classList.add('active');
        } else if(view === 'calendar') {
            document.getElementById('viewCalendar').classList.remove('hide');
            document.getElementById('navCal').classList.add('active');
            renderizarCalendario();
        } else if(view === 'instructors') {
            document.getElementById('viewInstructors').classList.remove('hide');
            document.getElementById('navInst').classList.add('active');
            document.getElementById('mainToolbar').classList.add('hide');
            renderizarInstrutores();
        } else if(view === 'config') {
            document.getElementById('viewConfig').classList.remove('hide');
            document.getElementById('navConfig').classList.add('active');
            document.getElementById('mainToolbar').classList.add('hide');
        }
    }

    window.mudarMes = function(dir) {
        currentMonth += dir;
        if(currentMonth < 0) { currentMonth = 11; currentYear--; }
        if(currentMonth > 11) { currentMonth = 0; currentYear++; }
        renderizarCalendario();
    }

    window.fecharModal = function(id) { document.getElementById(id).classList.add('hide'); }
    function fmtData(d) { if(!d) return ''; const [y,m,x] = d.split('-'); return `${x}/${m}`; }
    function aplicarPermissoes() {
        if(currentUserRole === 'viewer') document.querySelectorAll('.admin-only').forEach(el => el.classList.add('hide'));
        else document.querySelectorAll('.admin-only').forEach(el => el.classList.remove('hide'));
    }
</script>
</body>
</html>
