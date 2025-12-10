<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CSF Manager - Versão Corrigida</title>
    <link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap" rel="stylesheet">

    <style>
        :root {
            /* TEMA DARK */
            --primary: #6366f1; 
            --bg: #0f172a;      
            --sidebar-bg: #020617; 
            --surface: #1e293b; 
            --border: #334155;  
            --text-main: #f1f5f9; 
            --text-sec: #94a3b8;  
            --success: #10b981; 
            --warning: #f59e0b; 
            --danger: #ef4444;
            --info: #3b82f6;
        }

        body { font-family: 'Inter', sans-serif; background-color: var(--bg); color: var(--text-main); margin: 0; padding: 0; overflow-x: hidden; }
        
        /* --- TELA DE LOGIN (FIXED) --- */
        .login-wrapper {
            position: fixed;
            inset: 0;
            background-color: var(--bg);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 9999;
        }

        .login-box {
            background: var(--surface);
            padding: 40px;
            border-radius: 20px;
            border: 1px solid var(--border);
            width: 100%;
            max-width: 380px;
            text-align: center;
            box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.5);
        }

        /* CLASSE PARA ESCONDER ELEMENTOS */
        .hide { display: none !important; }

        /* --- APP LAYOUT --- */
        .app-wrapper { display: grid; grid-template-columns: 260px 1fr; min-height: 100vh; }
        .sidebar { background: var(--sidebar-bg); border-right: 1px solid var(--border); padding: 25px; display: flex; flex-direction: column; }
        .main-content { padding: 30px; overflow-y: auto; height: 100vh; box-sizing: border-box; }

        /* SIDEBAR */
        .brand { font-size: 20px; font-weight: 700; display: flex; align-items: center; gap: 12px; margin-bottom: 50px; color: var(--text-main); }
        .nav-item { padding: 12px 15px; border-radius: 10px; cursor: pointer; display: flex; align-items: center; gap: 12px; color: var(--text-sec); transition: 0.2s; margin-bottom: 8px; font-weight: 500; }
        .nav-item:hover, .nav-item.active { background: rgba(99, 102, 241, 0.15); color: var(--primary); }
        
        /* KPI & TOOLBAR */
        .kpi-row { display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 20px; margin-bottom: 30px; }
        .kpi-card { background: var(--surface); padding: 25px; border-radius: 16px; border: 1px solid var(--border); position: relative; }
        .kpi-card p { margin: 10px 0 0 0; font-size: 32px; font-weight: 700; color: var(--text-main); }
        .kpi-card h3 { margin: 0; font-size: 13px; color: var(--text-sec); text-transform: uppercase; }

        .toolbar { background: var(--surface); padding: 15px 20px; border-radius: 14px; display: flex; justify-content: space-between; align-items: center; border: 1px solid var(--border); margin-bottom: 25px; flex-wrap: wrap; gap: 15px; }
        .search-box { display: flex; align-items: center; background: var(--bg); border-radius: 8px; padding: 0 12px; border: 1px solid var(--border); flex: 1; max-width: 400px; }
        .search-box input { border: none; background: transparent; padding: 12px; width: 100%; outline: none; color: var(--text-main); }

        /* CARDS */
        .cards-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(300px, 1fr)); gap: 20px; }
        .course-card { background: var(--surface); border-radius: 16px; padding: 25px; border: 1px solid var(--border); transition: transform 0.2s; cursor: pointer; }
        .course-card:hover { transform: translateY(-4px); border-color: var(--primary); }
        .status-dot { height: 10px; width: 10px; border-radius: 50%; display: inline-block; margin-right: 6px; }

        /* CALENDAR */
        .calendar-wrapper { background: var(--surface); border-radius: 16px; border: 1px solid var(--border); padding: 20px; }
        .calendar-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; }
        .month-label { font-size: 20px; font-weight: 700; text-transform: capitalize; }
        .calendar-grid { display: grid; grid-template-columns: repeat(7, 1fr); gap: 1px; background: var(--border); border: 1px solid var(--border); }
        .cal-day-header { background: var(--surface); padding: 10px; text-align: center; font-weight: 600; font-size: 13px; color: var(--text-sec); }
        .cal-day { background: var(--bg); min-height: 140px; padding: 6px; position: relative; display: flex; flex-direction: column; gap: 2px; overflow: hidden; }
        .cal-day.empty { background: var(--surface); opacity: 0.3; }
        .day-number { font-size: 14px; font-weight: 700; margin-bottom: 5px; color: var(--text-sec); padding-left: 2px; }
        .current-day { background: rgba(99, 102, 241, 0.05); }

        .cal-event { font-size: 10px; padding: 4px 6px; margin-bottom: 2px; border-radius: 4px; cursor: pointer; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; font-weight: 600; transition: 0.2s; display: flex; align-items: center; gap: 4px; }
        .cal-event:hover { transform: scale(1.02); z-index: 10; filter: brightness(1.2); }
        .evt-start { background: rgba(16, 185, 129, 0.2); color: #6ee7b7; border-left: 3px solid #10b981; }
        .evt-end { background: rgba(239, 68, 68, 0.2); color: #fca5a5; border-left: 3px solid #ef4444; }
        .evt-middle { background: rgba(59, 130, 246, 0.1); color: #93c5fd; height: 4px; padding: 0; margin: 4px 0; border-radius: 2px; }

        /* INSTRUTORES */
        .instructors-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(300px, 1fr)); gap: 20px; }
        .instructor-card { background: var(--surface); border: 1px solid var(--border); border-radius: 16px; padding: 20px; display: flex; flex-direction: column; gap: 15px; transition: transform 0.2s, box-shadow 0.2s; }
        .instructor-card:hover { transform: translateY(-3px); border-color: var(--primary); box-shadow: 0 10px 20px rgba(0,0,0,0.2); }
        .inst-header { display: flex; align-items: center; gap: 15px; }
        .inst-avatar { width: 50px; height: 50px; background: linear-gradient(135deg, var(--primary), #818cf8); color: white; border-radius: 50%; display: flex; justify-content: center; align-items: center; font-size: 20px; font-weight: 700; }
        .inst-info h3 { margin: 0; font-size: 16px; color: var(--text-main); }
        .inst-info p { margin: 2px 0 0 0; font-size: 13px; color: var(--text-sec); }
        .inst-stats { background: var(--bg); padding: 10px; border-radius: 8px; display: flex; justify-content: space-between; align-items: center; border: 1px solid var(--border); }
        .inst-stats strong { color: var(--primary); font-size: 14px; }
        .inst-card-actions { display: grid; grid-template-columns: 1fr auto auto; gap: 10px; margin-top: auto; }
        
        .btn-icon { width: 36px; height: 36px; display: flex; justify-content: center; align-items: center; border-radius: 8px; border: 1px solid var(--border); background: transparent; color: var(--text-sec); cursor: pointer; transition: 0.2s; }
        .btn-icon:hover { background: #334155; color: var(--text-main); }
        .btn-icon.delete:hover { border-color: var(--danger); color: var(--danger); background: rgba(239,68,68,0.1); }
        .btn-zap-card { background: #25D366; color: white; border: none; border-radius: 8px; font-weight: 600; font-size: 13px; display: flex; align-items: center; justify-content: center; gap: 8px; text-decoration: none; transition: 0.2s; }
        .btn-zap-card:hover { background: #1ebc57; }
        .btn-zap-card.disabled { background: var(--border); color: var(--text-sec); pointer-events: none; }

        .btn { padding: 8px 16px; border-radius: 8px; border: none; font-weight: 600; cursor: pointer; display: inline-flex; align-items: center; gap: 6px; }
        .btn-primary { background: var(--primary); color: white; }
        .btn-outline { background: transparent; border: 1px solid var(--border); color: var(--text-sec); }
        .form-control { width: 100%; padding: 12px; margin-top: 6px; background: var(--bg); border: 1px solid var(--border); border-radius: 8px; color: var(--text-main); box-sizing: border-box; }
        .form-control:disabled { opacity: 0.6; cursor: not-allowed; border-style: dashed; }

        .modal-overlay { position: fixed; inset: 0; background: rgba(0,0,0,0.8); display: none; justify-content: center; align-items: center; z-index: 1000; backdrop-filter: blur(4px); }
        .modal-content { background: var(--surface); padding: 30px; border-radius: 16px; width: 90%; max-width: 600px; max-height: 90vh; overflow-y: auto; border: 1px solid var(--border); color: var(--text-main); }
        .form-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; }
        
        .st-concluido { color: var(--success); }
        .st-pendente { color: var(--warning); }
        .st-cancelado { color: var(--danger); }
        .bg-concluido { background: var(--success); }
        .bg-pendente { background: var(--warning); }
        .bg-cancelado { background: var(--danger); }

        @media(max-width: 768px) {
            .app-wrapper { grid-template-columns: 1fr; }
            .sidebar { display: none; }
        }
    </style>
</head>
<body>

<div id="loginScreen" class="login-wrapper">
    <div class="login-box">
        <h2 style="margin:0 0 20px 0;">CSF Manager</h2>
        <form onsubmit="fazerLogin(event)">
            <input type="text" id="user" class="form-control" placeholder="Usuário" required style="margin-bottom:15px;">
            <input type="password" id="pass" class="form-control" placeholder="Senha" required>
            <p style="font-size:12px; color:var(--text-sec); margin-top:20px;">
                Tente: <b>Csfiac</b> (Admin) ou <b>visitante</b> (Ver)
            </p>
            <button type="submit" class="btn btn-primary" style="width:100%; margin-top:10px; padding:12px;">ACESSAR SISTEMA</button>
        </form>
        <p id="loginError" style="color:var(--danger); display:none; margin-top:10px;">Credenciais Inválidas!</p>
    </div>
</div>

<div id="app" class="app-wrapper hide">
    
    <aside class="sidebar">
        <div class="brand"><span class="material-icons" style="color:var(--primary)">grid_view</span> CSF Gestão</div>
        <nav>
            <div class="nav-item active" id="navDash" onclick="alternarVisao('cards')">
                <span class="material-icons">dashboard</span> Cursos
            </div>
            <div class="nav-item" id="navCal" onclick="alternarVisao('calendar')">
                <span class="material-icons">calendar_month</span> Calendário
            </div>
            <div class="nav-item" id="navInst" onclick="alternarVisao('instructors')">
                <span class="material-icons">manage_accounts</span> Instrutores
            </div>
        </nav>
        <div style="margin-top:auto; border-top:1px solid var(--border); padding-top:20px; display:flex; gap:10px; align-items:center;">
            <div style="flex:1;">
                <div style="font-size:14px; font-weight:600;" id="labelUser">Usuário</div>
                <div style="font-size:11px; color:var(--text-sec);" id="labelRole">Permissão</div>
            </div>
            <span class="material-icons" style="cursor:pointer; color:var(--danger);" onclick="location.reload()" title="Sair">logout</span>
        </div>
    </aside>

    <main class="main-content">
        
        <div class="kpi-row">
            <div class="kpi-card"><h3>Total Cursos</h3><p id="kpiTotal">0</p></div>
            <div class="kpi-card"><h3>Alunos Formados</h3><p id="kpiAlunos" style="color:var(--info)">0</p></div>
            <div class="kpi-card"><h3>Cursos Concluídos</h3><p id="kpiExec" style="color:var(--success)">0</p></div>
            <div class="kpi-card"><h3>Pendentes / Sem Retorno</h3><p id="kpiPendente" style="color:var(--warning)">0</p></div>
        </div>

        <div class="toolbar" id="mainToolbar">
            <div class="search-box">
                <span class="material-icons" style="color:var(--text-sec)">search</span>
                <input type="text" id="search" placeholder="Buscar..." onkeyup="renderizarTudo()">
            </div>
            <div style="display:flex; gap:10px;">
                <button class="btn btn-outline" onclick="exportarCSV()"><span class="material-icons">download</span> CSV</button>
                <button class="btn btn-primary admin-only" onclick="abrirModalCurso()"><span class="material-icons">add</span> Novo Curso</button>
            </div>
        </div>

        <div id="viewCards" class="cards-grid"></div>

        <div id="viewCalendar" class="calendar-wrapper hide">
            <div class="calendar-header">
                <button class="btn btn-outline" onclick="mudarMes(-1)"><span class="material-icons">chevron_left</span></button>
                <div class="month-label" id="calMonthYear">Janeiro 2025</div>
                <button class="btn btn-outline" onclick="mudarMes(1)"><span class="material-icons">chevron_right</span></button>
            </div>
            <div class="calendar-grid" id="calGrid"></div>
            <div style="margin-top:15px; font-size:12px; color:var(--text-sec); display:flex; gap:15px;">
                <span style="display:flex; align-items:center; gap:5px; color:#6ee7b7;"><span class="material-icons" style="font-size:14px; color:#10b981;">play_arrow</span> Início</span>
                <span style="display:flex; align-items:center; gap:5px; color:#fca5a5;"><span class="material-icons" style="font-size:14px; color:#ef4444;">sports_score</span> Término</span>
            </div>
        </div>

        <div id="viewInstructors" class="hide">
            <div class="inst-actions admin-only">
                <button class="btn btn-primary" onclick="abrirModalInstrutor()"><span class="material-icons">person_add</span> Novo Instrutor</button>
            </div>
            <div id="instGrid" class="instructors-grid"></div>
        </div>

    </main>
</div>

<div class="modal-overlay" id="modalCurso">
    <div class="modal-content">
        <div style="display:flex; justify-content:space-between; margin-bottom:20px; padding-bottom:10px; border-bottom:1px solid var(--border);">
            <h2 style="margin:0;">Dados do Curso</h2>
            <span class="material-icons" onclick="fecharModal('modalCurso')" style="cursor:pointer;">close</span>
        </div>
        <form id="formCurso" onsubmit="salvarCurso(event)">
            <input type="hidden" id="editIndex">
            <div class="form-grid">
                <div style="grid-column:span 2;"><label>Nome do Curso</label><input type="text" id="inCurso" class="form-control" required></div>
                <div><label>ID</label><input type="number" id="inId" class="form-control"></div>
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
                    <select id="inInstrutorSelect" class="form-control">
                        <option value="">Selecione...</option>
                    </select>
                    <input type="text" id="inInstrutorTexto" class="form-control" placeholder="Ou digite o nome aqui..." style="margin-top:5px; font-size:12px;">
                </div>
            </div>
            <div style="margin-top:20px; display:flex; justify-content:space-between;" class="modal-btns admin-only">
                <button type="button" class="btn" style="color:var(--danger);" onclick="excluirCurso()">Excluir</button>
                <button type="submit" class="btn btn-primary">Salvar</button>
            </div>
        </form>
    </div>
</div>

<div class="modal-overlay" id="modalInstrutor">
    <div class="modal-content" style="max-width:400px;">
        <div style="display:flex; justify-content:space-between; margin-bottom:20px; padding-bottom:10px; border-bottom:1px solid var(--border);">
            <h2 style="margin:0;">Instrutor</h2>
            <span class="material-icons" onclick="fecharModal('modalInstrutor')" style="cursor:pointer;">close</span>
        </div>
        <form id="formInstrutor" onsubmit="salvarInstrutor(event)">
            <input type="hidden" id="editInstId">
            <label>Nome Completo</label>
            <input type="text" id="inInstNome" class="form-control" required>
            <label style="margin-top:15px;">WhatsApp (DDD+Número)</label>
            <input type="text" id="inInstTel" class="form-control" placeholder="85999999999">
            <div style="margin-top:25px; display:flex; justify-content:flex-end; gap:10px;" class="modal-btns admin-only">
                <button type="button" class="btn btn-outline" onclick="fecharModal('modalInstrutor')">Cancelar</button>
                <button type="submit" class="btn btn-primary">Salvar</button>
            </div>
        </form>
    </div>
</div>

<script>
    // --- CONFIGURAÇÃO DE USUÁRIOS ---
    const USERS = [
        { u: 'Csfiac', p: '032147', role: 'admin', label: 'Administrador' },
        { u: 'visitante', p: '1234', role: 'viewer', label: 'Visitante' }
    ];

    // --- DADOS COMPLETOS ---
    const fullDataCourses = [
        { id: 964, curso: "Drinques e Coquetéis", cidade: "Itapipoca", inicio: "2025-07-07", fim: "2025-07-25", local: "Assoc. Moradores do Serrote", status: "CONCLUIDO", alunos: 19, instrutor: "Thamires Brenda Santos" },
        { id: 1228, curso: "Sanduiches e Hambúrgueres", cidade: "Itapipoca", inicio: "2025-10-06", fim: "2025-10-10", local: "Cozinha Mulheres que Alimentam", status: "CONCLUIDO", alunos: 21, instrutor: "Eronildo almeida" },
        { id: 1229, curso: "Caldos, Sopas e Consumes", cidade: "Itapipoca", inicio: "2025-10-13", fim: "2025-10-17", local: "Juventude da Ladeira", status: "CONCLUIDO", alunos: 16, instrutor: "Eronildo almeida" },
        { id: 1230, curso: "Espetinhos", cidade: "Itapipoca", inicio: "2025-10-20", fim: "2025-10-24", local: "Rede permanente de solidariedade", status: "CONCLUIDO", alunos: 14, instrutor: "Eronildo almeida" },
        { id: 1227, curso: "Pratinhos", cidade: "Tururu", inicio: "2025-10-20", fim: "2025-10-24", local: "Cozinha Protagonista", status: "CONCLUIDO", alunos: 15, instrutor: "Giovanna Mota" },
        { id: 761, curso: "Caldos, Sopas e Consumes", cidade: "Itapajé", inicio: "2025-06-09", fim: "2025-06-13", local: "Cozinha STAS - Cras Catão", status: "CONCLUIDO", alunos: 19, instrutor: "Cristiane Bonfim Farias" },
        { id: 764, curso: "Comida de Rua", cidade: "Umirim", inicio: "2025-05-19", fim: "2025-05-23", local: "Cozinha Amor e Sabores", status: "CONCLUIDO", alunos: 19, instrutor: "Jordana Tabosa Pinto Sales" },
        { id: 944, curso: "Manutenção de Máquinas de Costura", cidade: "Pentecoste", inicio: "2025-07-14", fim: "2025-08-01", local: "Associação Comunitára de Jardim", status: "CONCLUIDO", alunos: 19, instrutor: "Ingrid Gomes Barbosa" },
        { id: 1210, curso: "Comida de Rua", cidade: "Pentecoste", inicio: "2025-09-08", fim: "2025-09-12", local: "AD Monte Sião", status: "CONCLUIDO", alunos: 20, instrutor: "Cris Bonfim" },
        { id: 844, curso: "Espetinho", cidade: "Tejuçuoca", inicio: "", fim: "", local: "Cozinha de Tejuçuara", status: "SEM RETORNO", alunos: 0, instrutor: "" },
        { id: 1816, curso: "Caldos, Sopas e Consumes", cidade: "Uruburetama", inicio: "", fim: "", local: "Cozinha Vida Abundante", status: "SEM RETORNO", alunos: 0, instrutor: "" },
        { id: 769, curso: "Vidraceiro", cidade: "Uruburetama", inicio: "", fim: "", local: "Cozinha Vida Abundante", status: "SEM RETORNO", alunos: 0, instrutor: "" },
        { id: 1418, curso: "Penteado Básico", cidade: "Itapajé", inicio: "2025-10-06", fim: "2025-10-10", local: "Cozinha STAS", status: "CONCLUIDO", alunos: 19, instrutor: "Beatriz Cassimiro" },
        { id: 1417, curso: "Acertos e Consertos de Roupas", cidade: "Irauçuba", inicio: "2025-09-23", fim: "2025-10-06", local: "Assoc. Moradores Vila Juá", status: "CONCLUIDO", alunos: 14, instrutor: "Ingrid Gomes Barbosa" },
        { id: 1419, curso: "Pintura Industrial", cidade: "Tejuçuoca", inicio: "", fim: "", local: "Cozinha Tejuçuoca", status: "SEM RETORNO", alunos: 0, instrutor: "" },
        { id: 1420, curso: "Maquiagem", cidade: "General Sampaio", inicio: "2025-10-08", fim: "2025-10-30", local: "Cozinha Mãos Solidária", status: "CONCLUIDO", alunos: 20, instrutor: "Amanda Marcela" },
        { id: 1421, curso: "Conserto de Bike", cidade: "Apuiarés", inicio: "", fim: "", local: "Cozinha Moradores do Salgado", status: "SEM RETORNO", alunos: 0, instrutor: "" },
        { id: 843, curso: "Comida de Rua", cidade: "Baturité", inicio: "2025-05-28", fim: "2025-06-03", local: "Assoc. Evangélica Discíplos de Jesus", status: "CONCLUIDO", alunos: 19, instrutor: "Vinicius Martins de Lima" },
        { id: 580, curso: "Eletricista Básico", cidade: "Palmácia", inicio: "2025-03-27", fim: "2025-04-09", local: "Associação Palmácia dos Sonhos", status: "CONCLUIDO", alunos: 20, instrutor: "Bruno Kennedy de Barbosa Mendes" },
        { id: 1180, curso: "Caldos, Sopas e Consumes", cidade: "Aracoiaba", inicio: "", fim: "", local: "Comunidade Kolping", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1177, curso: "Sanduiches e Hambúrgueres", cidade: "Barreira", inicio: "2025-09-23", fim: "2025-09-29", local: "Associação Caiana", status: "CONCLUIDO", alunos: 23, instrutor: "Arlene Ribeiro" },
        { id: 1178, curso: "Pintura Industrial", cidade: "Redenção", inicio: "", fim: "", local: "Unidos Venceremos", status: "AGUARDANDO", alunos: 0, instrutor: "José Carlos Albuquerqu" },
        { id: 1365, curso: "Penteado Básico", cidade: "Aratuba", inicio: "2025-11-10", fim: "2025-11-14", local: "Associação Moradores Do Mussu", status: "CONCLUIDO", alunos: 17, instrutor: "Beatriz Cassimiro" },
        { id: 1368, curso: "Montagem de Bijuterias", cidade: "Guaramiranga", inicio: "2025-10-13", fim: "2025-10-27", local: "Cozinha Solidária do Pernambuqyuinho", status: "CONCLUIDO", alunos: 20, instrutor: "Lima filho" },
        { id: 1390, curso: "Auxiliar de Pedreiro", cidade: "Barreira", inicio: "", fim: "", local: "Assoc. Moradores Urua I", status: "AGUARDANDO", alunos: 0, instrutor: "Eduardo" },
        { id: 1369, curso: "Conserto de Bike", cidade: "Pacoti", inicio: "", fim: "", local: "Cozinha Solidária Granja", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1465, curso: "Maquiagem", cidade: "Palmácia", inicio: "2025-11-17", fim: "2025-12-08", local: "Associação Palmácia dos Sonhos", status: "EM ANDAMENTO", alunos: 0, instrutor: "Allyne Carol" },
        { id: 768, curso: "Manutenção de Máquinas de Costura", cidade: "Ubajara", inicio: "2025-06-23", fim: "2025-07-11", local: "Centro Espirito Especial De Ubajara", status: "CONCLUIDO", alunos: 19, instrutor: "Ingrid Gomes Barbosa" },
        { id: 1194, curso: "Caldos, Sopas e Consumes", cidade: "Tianguá", inicio: "2025-09-09", fim: "2025-09-15", local: "Cozinha Solidária de Timbaúba", status: "CONCLUIDO", alunos: 20, instrutor: "Francisco Kelton" },
        { id: 1199, curso: "Espetinhos", cidade: "Ipu", inicio: "2025-08-13", fim: "2025-08-19", local: "Cozinha da Associação Internacional", status: "CONCLUIDO", alunos: 16, instrutor: "Fernanda Dantas" },
        { id: 1328, curso: "Acertos e Consertos de Roupas", cidade: "Croatá", inicio: "", fim: "", local: "Paróquia Nossa Senhora das Dores", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1193, curso: "Sanduiches e Hambúrgueres", cidade: "Tianguá", inicio: "2025-09-24", fim: "2025-09-30", local: "Cozinha Santo Antônio", status: "CONCLUIDO", alunos: 19, instrutor: "Francisco Kelton" },
        { id: 1329, curso: "Conserto Eletrodomésticos", cidade: "Ibiapina", inicio: "", fim: "", local: "Sindicato dos Trabalhadores Rurais", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 620, curso: "Maquiagem", cidade: "Choró", inicio: "2025-03-17", fim: "2025-04-09", local: "Cozinha Casa de Mãe", status: "CONCLUIDO", alunos: 19, instrutor: "Kamila Ribeiro" },
        { id: 850, curso: "Comida de Rua", cidade: "Quixadá", inicio: "2025-08-11", fim: "2025-08-15", local: "Cozinha Dom Maurício", status: "CONCLUIDO", alunos: 20, instrutor: "Cris Bonfim" },
        { id: 1204, curso: "Poda e Arborismo", cidade: "Ibaretama", inicio: "", fim: "", local: "Dona Maria", status: "TURMA FORMADA", alunos: 0, instrutor: "" },
        { id: 1203, curso: "Jardinagem", cidade: "Ibicuitinga", inicio: "", fim: "", local: "Cozinha de Todos", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1208, curso: "Drinques e Coquetéis", cidade: "Quixadá", inicio: "2025-09-22", fim: "2025-10-10", local: "União Popular", status: "CONCLUIDO", alunos: 19, instrutor: "Thamires Brenda Santos" },
        { id: 1207, curso: "Pratinhos", cidade: "Quixadá", inicio: "", fim: "", local: "Unidos por Cristo", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1206, curso: "Espetinhos", cidade: "Quixeramobim", inicio: "", fim: "", local: "Movimento Mulheres Empreendedoras", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1205, curso: "Pratinhos", cidade: "Quixeramobim", inicio: "", fim: "", local: "Juntos com HB", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1202, curso: "Vidraceiro", cidade: "Banabuiú", inicio: "", fim: "", local: "Dona Mocinha", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1717, curso: "Pratinhos", cidade: "Quixeramobim", inicio: "", fim: "", local: "USPR Nossa Senhora das Dores", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1370, curso: "Pratinhos", cidade: "Milhã", inicio: "2025-08-25", fim: "2025-08-29", local: "Cozinha Alimentando Vidas", status: "CONCLUIDO", alunos: 20, instrutor: "Fernanda Dantas" },
        { id: 847, curso: "Espetinhos", cidade: "Pedra Branca", inicio: "", fim: "", local: "Cozinha Chico Leandro", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 848, curso: "Comida de Rua", cidade: "Mombaça", inicio: "", fim: "", local: "Cozinha Princesa do Mel", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 849, curso: "Compostagem Adubo", cidade: "Senador Pompeu", inicio: "", fim: "", local: "Cozinha do Alto", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1355, curso: "Pintura Industrial", cidade: "Pedra Branca", inicio: "", fim: "", local: "Cozinha Solidária Mineirolândia", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1364, curso: "Drinques e Coquetéis", cidade: "Milhã", inicio: "2025-09-29", fim: "2025-10-21", local: "Carnaubinha Solidária", status: "CONCLUIDO", alunos: 19, instrutor: "Marcelo" },
        { id: 1358, curso: "Sanduiches e Hambúrgueres", cidade: "Senador Pompeu", inicio: "", fim: "", local: "Cozinha do Povo", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1360, curso: "Penteado Básico", cidade: "Dep. Irapuan Pinheiro", inicio: "2025-10-20", fim: "2025-10-24", local: "Cozinha Solidária dos Amigos", status: "CONCLUIDO", alunos: 19, instrutor: "Beatriz Cassimiro" },
        { id: null, curso: "Auxiliar de Pedreiro", cidade: "Senador Pompeu", inicio: "", fim: "", local: "", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: null, curso: "Gesseiro Básico", cidade: "Piquet Carneiro", inicio: "", fim: "", local: "", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 584, curso: "Conserto de Bike", cidade: "Madalena", inicio: "2025-03-17", fim: "2025-04-07", local: "Cozinha ABECRA", status: "CONCLUIDO", alunos: 20, instrutor: "Kaio Evellyn Lemos Neres" },
        { id: 1016, curso: "Comida de Rua", cidade: "Itatira", inicio: "2025-09-10", fim: "2025-09-16", local: "Cooperativa Sertaneja Cearense", status: "CONCLUIDO", alunos: 20, instrutor: "Glenda Gadelha" },
        { id: 1013, curso: "Pratinhos", cidade: "Boa Viagem", inicio: "", fim: "", local: "Acolhendo para Viver", status: "SEM RETORNO", alunos: 0, instrutor: "" },
        { id: 766, curso: "Comida de Rua", cidade: "Itatira", inicio: "", fim: "", local: "Associação José Batista Nunes", status: "SEM RETORNO", alunos: 0, instrutor: "" },
        { id: 1335, curso: "Penteado Básico", cidade: "Caridade", inicio: "", fim: "", local: "Cozinha Viver é Tempo", status: "SEM RETORNO", alunos: 0, instrutor: "" },
        { id: null, curso: "Sanduiches e Hambúrgueres", cidade: "Boa Viagem", inicio: "", fim: "", local: "", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: null, curso: "Caldos, Sopas e Consumes", cidade: "Canindé", inicio: "", fim: "", local: "", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: null, curso: "Espetinhos", cidade: "Canindé", inicio: "", fim: "", local: "", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: null, curso: "Poda e Arborismo", cidade: "Itatira", inicio: "", fim: "", local: "", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: null, curso: "Sanduiches e Hambúrgueres", cidade: "Paramoti", inicio: "", fim: "", local: "", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 803, curso: "Pratinhos", cidade: "Acopiara", inicio: "", fim: "", local: "Trupe Tagarela", status: "SEM RETORNO", alunos: 0, instrutor: "" },
        { id: 1600, curso: "Drinques e Coquetéis", cidade: "Fortaleza", inicio: "", fim: "", local: "Projeto Renascer", status: "SEM RETORNO", alunos: 0, instrutor: "" },
        { id: 1599, curso: "Eletricista Básico", cidade: "Crateús", inicio: "2025-12-01", fim: "2025-12-12", local: "Cozinha Solidária Planaltina", status: "EM ANDAMENTO", alunos: 0, instrutor: "Iranley" },
        { id: 575, curso: "Penteado Básico", cidade: "Miraíma", inicio: "2025-05-05", fim: "2025-05-09", local: "Cozinha Pastoral da Criança", status: "CONCLUIDO", alunos: 20, instrutor: "Fernando César Nascimento" },
        { id: 1197, curso: "Acertos e Consertos de Roupas", cidade: "Aracati", inicio: "", fim: "", local: "Cozinha Beira do Rio", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1823, curso: "Pratinhos", cidade: "Aracati", inicio: "", fim: "", local: "Cozinha Mão Amiga", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1367, curso: "Eletricista Básico", cidade: "Jaguaruana", inicio: "", fim: "", local: "Cozinha Jurema", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 581, curso: "Sanduiches e Hambúrgueres", cidade: "São Benedito", inicio: "2025-04-08", fim: "2025-04-14", local: "Cozinha Galeria da Esperança", status: "CONCLUIDO", alunos: 20, instrutor: "Francisco Kelton" },
        { id: 567, curso: "Alongamento de Unhas", cidade: "Pentecoste", inicio: "2025-04-04", fim: "2025-04-28", local: "Central Das Organizaçoes", status: "CONCLUIDO", alunos: 20, instrutor: "Gerlandia vieira da Silva" },
        { id: 760, curso: "Sanduiches e Hambúrgueres", cidade: "Irauçuba", inicio: "2025-05-26", fim: "2025-05-30", local: "Cozinha Missi Solidário", status: "CONCLUIDO", alunos: 19, instrutor: "Jordana Tabosa Pinto Sales" },
        { id: 631, curso: "Alongamento de Unhas", cidade: "Solonópole", inicio: "2025-03-28", fim: "2025-04-22", local: "Cozinahando com Amor", status: "CONCLUIDO", alunos: 12, instrutor: "Maria Keiliane Moreira da Silva" },
        { id: 765, curso: "Comida de Rua", cidade: "Catunda", inicio: "", fim: "", local: "Cozinha da Esperança", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1817, curso: "Acertos de Roupas", cidade: "Monsenhor Tabosa", inicio: "", fim: "", local: "Cozinha Jucás", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1334, curso: "Alongamento de Unhas", cidade: "Tamboril", inicio: "", fim: "", local: "Cozinha Novo Tamboril", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1818, curso: "Espetinhos", cidade: "Hidrolândia", inicio: "", fim: "", local: "Cozinha Irajá", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1811, curso: "Pratinhos", cidade: "Iracema", inicio: "", fim: "", local: "Federação dos Amigos", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: null, curso: "Sanduiches e Hambúrgueres", cidade: "Ererê", inicio: "", fim: "", local: "Paróquia Jesus da Agonia", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1333, curso: "Sanduíches e Hamburgueres", cidade: "Fortaleza", inicio: "", fim: "", local: "Cozinha Santo Antônio", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1356, curso: "Caldos, sopas e consumês", cidade: "Fortaleza", inicio: "", fim: "", local: "Cozinha Santo Antônio", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1820, curso: "Pratinhos", cidade: "Fortaleza", inicio: "", fim: "", local: "Cozinha Santo Antônio", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1821, curso: "Gesseiro Básico", cidade: "Fortaleza", inicio: "", fim: "", local: "Cozinha Santo Antônio", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1179, curso: "Acertos de Roupas", cidade: "Fortaleza", inicio: "", fim: "", local: "Cozinha Escolhi Amar", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1198, curso: "Eletricista Básico", cidade: "Fortaleza", inicio: "2025-12-15", fim: "2025-12-26", local: "Cozinha Solnascente", status: "PENDENTE", alunos: 0, instrutor: "Cintia" },
        { id: 1822, curso: "Montagem de Bijuterias", cidade: "Fortaleza", inicio: "2025-12-08", fim: "2025-12-19", local: "Comunidade dos Trilhos", status: "PENDENTE", alunos: 0, instrutor: "Karol" },
        { id: 1195, curso: "Poda e Arborismo", cidade: "Fortaleza", inicio: "", fim: "", local: "Cozinha ACBA", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1196, curso: "Gesseiro Básico", cidade: "Fortaleza", inicio: "", fim: "", local: "Intituto Perifa", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1814, curso: "Compostagem Adubo", cidade: "Fortaleza", inicio: "", fim: "", local: "Cozinha DasMarias", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1815, curso: "Jardinagem", cidade: "Fortaleza", inicio: "", fim: "", local: "Associação União Vila Velha", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1362, curso: "Vidraceiro", cidade: "Fortaleza", inicio: "", fim: "", local: "Cozinha Ágape", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1366, curso: "Auxiliar de Pedreiro", cidade: "Fortaleza", inicio: "", fim: "", local: "Fazendo o Bem", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1812, curso: "Pintura Industrial", cidade: "Fortaleza", inicio: "", fim: "", local: "Novo Milênio", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1813, curso: "Caldos, Sopas e Consumes", cidade: "Fortaleza", inicio: "", fim: "", local: "Instituo Facis", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 846, curso: "Espetinhos", cidade: "Fortaleza", inicio: "", fim: "", local: "Cozinha Maravilha", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1838, curso: "Sanduiches e Hambúrgueres", cidade: "Fortaleza", inicio: "", fim: "", local: "Amigos da Natureza", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1742, curso: "Caldos, Sopas e Consumes", cidade: "Fortaleza", inicio: "", fim: "", local: "Maria de Lurdes", status: "AGUARDANDO", alunos: 0, instrutor: "" },
        { id: 1841, curso: "Conserto Eletrodomésticos", cidade: "Eusébio", inicio: "", fim: "", local: "USPR Casa de Abraão", status: "AGUARDANDO", alunos: 0, instrutor: "" }
    ];

    const fullDataInstructors = [
        { nome: "Thamires Brenda Santos", tel: "8893440515" },
        { nome: "Eronildo almeida", tel: "8896569769" },
        { nome: "Giovanna Mota", tel: "8591735449" },
        { nome: "Cristiane Bonfim Farias", tel: "85999223341" },
        { nome: "Jordana Tabosa Pinto Sales", tel: "85981871564" },
        { nome: "Ingrid Gomes Barbosa", tel: "8599149365" },
        { nome: "Cris Bonfim", tel: "8599223341" },
        { nome: "Beatriz Cassimiro", tel: "8596325812" },
        { nome: "Amanda Marcela", tel: "8591318337" },
        { nome: "Vinicius Martins de Lima", tel: "85998476506" },
        { nome: "Bruno Kennedy de Barbosa Mendes", tel: "85992431101" },
        { nome: "Arlene Ribeiro", tel: "85999223341" },
        { nome: "Lima filho", tel: "8586155745" },
        { nome: "Allyne Carol", tel: "8591660956" },
        { nome: "Francisco Kelton", tel: "8893769717" },
        { nome: "Fernanda Dantas", tel: "8592172021" },
        { nome: "Kamila Ribeiro", tel: "88988092092" },
        { nome: "Marcelo", tel: "8598087144" },
        { nome: "Kaio Evellyn Lemos Neres", tel: "88992268531" },
        { nome: "Glenda Gadelha", tel: "8592667353" },
        { nome: "Fernando César Nascimento", tel: "85994048269" },
        { nome: "Gerlandia vieira da Silva", tel: "85992588378" },
        { nome: "Maria Keiliane Moreira da Silva", tel: "88982241376" },
        { nome: "José Carlos Albuquerqu", tel: "85933005342" },
        { nome: "Eduardo", tel: "8586598389" },
        { nome: "Iranley", tel: "" },
        { nome: "Cintia", tel: "85984209317" },
        { nome: "Karol", tel: "85989726918" }
    ];

    // --- STATE ---
    let cursos = [];
    let instrutores = [];
    let currentMonth = new Date().getMonth();
    let currentYear = new Date().getFullYear();
    let currentUserRole = null;

    function fazerLogin(e) {
        e.preventDefault();
        const u = document.getElementById('user').value;
        const p = document.getElementById('pass').value;
        const errorMsg = document.getElementById('loginError');
        
        const foundUser = USERS.find(user => user.u === u && user.p === p);

        if(foundUser) {
            currentUserRole = foundUser.role;
            document.getElementById('loginScreen').classList.add('hide');
            document.getElementById('app').classList.remove('hide');
            document.getElementById('labelUser').innerText = foundUser.u;
            document.getElementById('labelRole').innerText = foundUser.label;
            initSystem();
        } else {
            errorMsg.style.display = 'block';
        }
    }

    function initSystem() {
        const savedCursos = localStorage.getItem('csf_final_cursos_v3');
        const savedInst = localStorage.getItem('csf_final_inst_v3');
        
        cursos = savedCursos ? JSON.parse(savedCursos) : fullDataCourses;
        instrutores = savedInst ? JSON.parse(savedInst) : fullDataInstructors;
        
        // Garante sincronia
        cursos.forEach(c => {
            if(c.instrutor && !instrutores.find(i => i.nome === c.instrutor)) {
                instrutores.push({ nome: c.instrutor, tel: '' });
            }
        });

        renderizarTudo();
        aplicarPermissoes();
    }

    function aplicarPermissoes() {
        if(currentUserRole === 'viewer') {
            document.querySelectorAll('.admin-only').forEach(el => el.classList.add('hide'));
        } else {
            document.querySelectorAll('.admin-only').forEach(el => el.classList.remove('hide'));
        }
    }

    function renderizarTudo() {
        atualizarKPIs();
        renderizarCards();
        renderizarCalendarioReal();
        renderizarGridInstrutores();
        salvarDados();
    }

    function salvarDados() {
        localStorage.setItem('csf_final_cursos_v3', JSON.stringify(cursos));
        localStorage.setItem('csf_final_inst_v3', JSON.stringify(instrutores));
    }

    function atualizarKPIs() {
        document.getElementById('kpiTotal').innerText = cursos.length;
        document.getElementById('kpiExec').innerText = cursos.filter(c => c.status === 'CONCLUIDO').length;
        document.getElementById('kpiPendente').innerText = cursos.filter(c => c.status !== 'CONCLUIDO').length;
        
        const totalAlunos = cursos.reduce((acc, c) => {
            return c.status === 'CONCLUIDO' ? acc + (parseInt(c.alunos) || 0) : acc;
        }, 0);
        document.getElementById('kpiAlunos').innerText = totalAlunos;
    }

    // --- CARDS ---
    function renderizarCards() {
        const grid = document.getElementById('viewCards');
        grid.innerHTML = '';
        const termo = document.getElementById('search').value.toLowerCase();
        
        cursos.forEach((c, idx) => {
            if(!(c.curso+c.cidade+(c.instrutor||'')).toLowerCase().includes(termo)) return;
            let stClass = 'st-pendente'; let dotBg = 'bg-pendente';
            if(c.status === 'CONCLUIDO') { stClass = 'st-concluido'; dotBg = 'bg-concluido'; }
            if(c.status.includes('SEM') || c.status.includes('AGUARDANDO')) { stClass = 'st-cancelado'; dotBg = 'bg-cancelado'; }
            const dt = c.inicio ? fmtData(c.inicio) : 'S/ Data';
            const idDisplay = c.id ? `#${c.id}` : '# N/A';

            grid.innerHTML += `
                <div class="course-card" onclick="abrirModalCurso(${idx})">
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

    // --- CALENDAR ---
    function renderizarCalendarioReal() {
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

        const sortedCursos = [...cursos].sort((a,b) => (a.id || 0) - (b.id || 0));

        for(let i=1; i<=lastDay; i++) {
            const dateStr = `${currentYear}-${String(currentMonth+1).padStart(2,'0')}-${String(i).padStart(2,'0')}`;
            const isToday = i === today.getDate() && currentMonth === today.getMonth() && currentYear === today.getFullYear();
            
            let eventsHtml = '';
            sortedCursos.forEach((c, idx) => {
                if(c.inicio && c.fim && dateStr >= c.inicio && dateStr <= c.fim) {
                    const realIdx = cursos.indexOf(c);
                    
                    if(dateStr === c.inicio) {
                        eventsHtml += `<div class="cal-event evt-start" onclick="event.stopPropagation(); abrirModalCurso(${realIdx})" title="Início: ${c.curso}">▶ ${c.curso}</div>`;
                    } else if(dateStr === c.fim) {
                        eventsHtml += `<div class="cal-event evt-end" onclick="event.stopPropagation(); abrirModalCurso(${realIdx})" title="Fim: ${c.curso}">🏁 ${c.curso}</div>`;
                    } else {
                        // Barra do meio
                        eventsHtml += `<div class="evt-middle" title="Em andamento: ${c.curso}"></div>`;
                    }
                }
            });

            grid.innerHTML += `
                <div class="cal-day ${isToday ? 'current-day' : ''}">
                    <div class="day-number">${i}</div>
                    ${eventsHtml}
                </div>`;
        }
    }

    function mudarMes(dir) {
        currentMonth += dir;
        if(currentMonth < 0) { currentMonth = 11; currentYear--; }
        if(currentMonth > 11) { currentMonth = 0; currentYear++; }
        renderizarCalendarioReal();
    }

    // --- INSTRUTORES ---
    function renderizarGridInstrutores() {
        const container = document.getElementById('instGrid');
        container.innerHTML = '';
        
        if(instrutores.length === 0) {
            container.innerHTML = '<div style="color:var(--text-sec); grid-column:span 3; text-align:center;">Nenhum instrutor cadastrado.</div>';
            return;
        }

        instrutores.forEach((inst, idx) => {
            const count = cursos.filter(c => c.instrutor === inst.nome).length;
            const zapLink = inst.tel ? `https://wa.me/55${inst.tel.replace(/\D/g,'')}` : '#';
            const zapClass = inst.tel ? '' : 'disabled';
            const initials = inst.nome.substring(0,2).toUpperCase();
            
            // Buttons logic for permission
            const btnEdit = currentUserRole === 'admin' ? `<button class="btn-icon" onclick="abrirModalInstrutor(${idx})"><span class="material-icons" style="font-size:18px;">edit</span></button>` : '';
            const btnDel = currentUserRole === 'admin' ? `<button class="btn-icon delete" onclick="excluirInstrutor(${idx})"><span class="material-icons" style="font-size:18px;">delete</span></button>` : '';

            container.innerHTML += `
                <div class="instructor-card">
                    <div class="inst-header">
                        <div class="inst-avatar">${initials}</div>
                        <div class="inst-info">
                            <h3>${inst.nome}</h3>
                            <p>${inst.tel || 'Sem telefone'}</p>
                        </div>
                    </div>
                    <div class="inst-stats">
                        <span>Cursos Vinculados</span>
                        <strong>${count}</strong>
                    </div>
                    <div class="inst-card-actions">
                        <a href="${zapLink}" target="_blank" class="btn-zap-card ${zapClass}">
                            <span class="material-icons" style="font-size:16px;">chat</span> WhatsApp
                        </a>
                        ${btnEdit}
                        ${btnDel}
                    </div>
                </div>`;
        });
    }

    // --- MODAIS & CRUD ---
    function abrirModalCurso(idx=-1) {
        document.getElementById('formCurso').reset();
        document.getElementById('editIndex').value = idx;
        const sel = document.getElementById('inInstrutorSelect');
        sel.innerHTML = '<option value="">Selecione...</option>';
        instrutores.forEach(i => sel.innerHTML += `<option value="${i.nome}">${i.nome}</option>`);

        // Config Permissões nos Inputs
        const inputs = document.querySelectorAll('#formCurso input, #formCurso select');
        inputs.forEach(el => el.disabled = (currentUserRole !== 'admin'));

        if(idx >= 0) {
            const c = cursos[idx];
            document.getElementById('inCurso').value = c.curso;
            document.getElementById('inId').value = c.id;
            document.getElementById('inCidade').value = c.cidade;
            document.getElementById('inInicio').value = c.inicio;
            document.getElementById('inFim').value = c.fim;
            document.getElementById('inStatus').value = c.status;
            document.getElementById('inAlunos').value = c.alunos;
            document.getElementById('inLocal').value = c.local || '';
            const exists = instrutores.find(i => i.nome === c.instrutor);
            if(exists) sel.value = c.instrutor; else document.getElementById('inInstrutorTexto').value = c.instrutor;
        }
        
        if(currentUserRole !== 'admin') {
            document.querySelector('#modalCurso .modal-btns').classList.add('hide');
        } else {
            document.querySelector('#modalCurso .modal-btns').classList.remove('hide');
        }

        document.getElementById('modalCurso').style.display = 'flex';
    }

    function salvarCurso(e) {
        e.preventDefault();
        if(currentUserRole !== 'admin') return;

        const idx = parseInt(document.getElementById('editIndex').value);
        const instFinal = document.getElementById('inInstrutorSelect').value || document.getElementById('inInstrutorTexto').value;
        const novo = {
            curso: document.getElementById('inCurso').value,
            id: document.getElementById('inId').value,
            cidade: document.getElementById('inCidade').value,
            inicio: document.getElementById('inInicio').value,
            fim: document.getElementById('inFim').value,
            status: document.getElementById('inStatus').value,
            alunos: document.getElementById('inAlunos').value,
            local: document.getElementById('inLocal').value,
            instrutor: instFinal
        };

        if(idx === -1) cursos.push(novo); else cursos[idx] = novo;
        if(instFinal && !instrutores.find(i => i.nome === instFinal)) instrutores.push({ nome: instFinal, tel: '' });

        fecharModal('modalCurso');
        renderizarTudo();
    }

    function excluirCurso() {
        if(currentUserRole !== 'admin') return;
        const idx = parseInt(document.getElementById('editIndex').value);
        if(idx>=0 && confirm('Excluir curso?')) {
            cursos.splice(idx,1);
            fecharModal('modalCurso');
            renderizarTudo();
        }
    }

    function abrirModalInstrutor(idx=-1) {
        document.getElementById('formInstrutor').reset();
        if(idx >= 0) {
            const i = instrutores[idx];
            document.getElementById('editInstId').value = i.nome;
            document.getElementById('inInstNome').value = i.nome;
            document.getElementById('inInstTel').value = i.tel;
        } else { document.getElementById('editInstId').value = "NEW"; }
        document.getElementById('modalInstrutor').style.display = 'flex';
    }

    function salvarInstrutor(e) {
        e.preventDefault();
        if(currentUserRole !== 'admin') return;
        
        const oldName = document.getElementById('editInstId').value;
        const newName = document.getElementById('inInstNome').value;
        const newTel = document.getElementById('inInstTel').value;

        if(oldName === "NEW") {
            instrutores.push({ nome: newName, tel: newTel });
        } else {
            const instIndex = instrutores.findIndex(i => i.nome === oldName);
            if(instIndex >= 0) {
                instrutores[instIndex] = { nome: newName, tel: newTel };
                cursos.forEach(c => { if(c.instrutor === oldName) c.instrutor = newName; });
            }
        }
        fecharModal('modalInstrutor');
        renderizarTudo();
    }

    function excluirInstrutor(idx) {
        if(currentUserRole !== 'admin') return;
        if(confirm('Excluir instrutor?')) {
            instrutores.splice(idx, 1);
            renderizarTudo();
        }
    }

    function alternarVisao(view) {
        document.getElementById('viewCards').classList.add('hide');
        document.getElementById('viewCalendar').classList.add('hide');
        document.getElementById('viewInstructors').classList.add('hide');
        document.getElementById('mainToolbar').classList.remove('hide'); 
        document.querySelectorAll('.nav-item').forEach(el => el.classList.remove('active'));

        if(view === 'cards') {
            document.getElementById('viewCards').classList.remove('hide');
            document.getElementById('navDash').classList.add('active');
        } else if(view === 'calendar') {
            document.getElementById('viewCalendar').classList.remove('hide');
            document.getElementById('navCal').classList.add('active');
            renderizarCalendarioReal();
        } else if(view === 'instructors') {
            document.getElementById('viewInstructors').classList.remove('hide');
            document.getElementById('navInst').classList.add('active');
            document.getElementById('mainToolbar').classList.add('hide'); 
            renderizarGridInstrutores();
        }
    }

    function exportarCSV() {
        let csv = "ID,Curso,Cidade,Instrutor,Inicio,Fim,Status,Concluintes\n";
        cursos.forEach(c => csv += `${c.id},"${c.curso}",${c.cidade},${c.instrutor},${c.inicio},${c.fim},${c.status},${c.alunos}\n`);
        const link = document.createElement('a');
        link.href = 'data:text/csv;charset=utf-8,' + encodeURI(csv);
        link.download = 'cursos_completo.csv';
        link.click();
    }

    function fecharModal(id) { document.getElementById(id).style.display = 'none'; }
    function fmtData(d) { if(!d) return ''; const [y,m,x] = d.split('-'); return `${x}/${m}`; }
</script>
</body>
</html>
