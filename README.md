<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CSF Manager - Cloud Sync Realtime</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
    <style>
        :root {
            --primary: #6366f1;
            --primary-hover: #4f46e5;
            --background: #0f172a;
            --sidebar: #020617;
            --surface: #1e293b;
            --surface-hover: #334155;
            --success: #10b981;
            --warning: #f59e0b;
            --danger: #ef4444;
            --info: #3b82f6;
            --text-primary: #f8fafc;
            --text-secondary: #94a3b8;
            --text-muted: #64748b;
            --border: #334155;
            --overlay: rgba(0, 0, 0, 0.7);
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Inter', sans-serif;
            background: var(--background);
            color: var(--text-primary);
            min-height: 100vh;
            overflow-x: hidden;
        }

        /* Login Screen */
        .login-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: var(--background);
            display: flex;
            align-items: center;
            justify-content: center;
            z-index: 9999;
        }

        .login-overlay.hidden {
            display: none;
        }

        .login-card {
            background: var(--surface);
            padding: 2.5rem;
            border-radius: 16px;
            width: 100%;
            max-width: 400px;
            box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5);
        }

        .login-logo {
            display: flex;
            align-items: center;
            gap: 12px;
            margin-bottom: 2rem;
            justify-content: center;
        }

        .login-logo .material-icons {
            font-size: 40px;
            color: var(--primary);
        }

        .login-logo h1 {
            font-size: 1.5rem;
            font-weight: 700;
            background: linear-gradient(135deg, var(--primary), #a855f7);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }

        .login-error {
            background: rgba(239, 68, 68, 0.1);
            border: 1px solid var(--danger);
            color: var(--danger);
            padding: 12px;
            border-radius: 8px;
            margin-bottom: 1rem;
            font-size: 14px;
            display: none;
        }

        .login-error.show {
            display: block;
        }

        /* Layout */
        .app-container {
            display: flex;
            min-height: 100vh;
        }

        /* Sidebar */
        .sidebar {
            width: 260px;
            background: var(--sidebar);
            border-right: 1px solid var(--border);
            position: fixed;
            height: 100vh;
            display: flex;
            flex-direction: column;
            z-index: 100;
            transition: transform 0.3s ease;
        }

        .sidebar-header {
            padding: 1.5rem;
            border-bottom: 1px solid var(--border);
        }

        .sidebar-logo {
            display: flex;
            align-items: center;
            gap: 12px;
        }

        .sidebar-logo .material-icons {
            font-size: 32px;
            color: var(--primary);
        }

        .sidebar-logo h1 {
            font-size: 1.25rem;
            font-weight: 700;
            color: var(--text-primary);
        }

        .sidebar-logo span {
            font-size: 0.7rem;
            color: var(--text-muted);
            display: block;
            margin-top: 2px;
        }

        .sidebar-nav {
            flex: 1;
            padding: 1rem 0;
        }

        .nav-item {
            display: flex;
            align-items: center;
            gap: 12px;
            padding: 12px 1.5rem;
            color: var(--text-secondary);
            cursor: pointer;
            transition: all 0.2s ease;
            border-left: 3px solid transparent;
        }

        .nav-item:hover {
            background: var(--surface);
            color: var(--text-primary);
        }

        .nav-item.active {
            background: var(--surface);
            color: var(--primary);
            border-left-color: var(--primary);
        }

        .nav-item .material-icons {
            font-size: 22px;
        }

        .sidebar-footer {
            padding: 1rem 1.5rem;
            border-top: 1px solid var(--border);
        }

        .user-info {
            display: flex;
            align-items: center;
            gap: 12px;
        }

        .user-avatar {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            background: var(--primary);
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: 600;
            font-size: 14px;
        }

        .user-details h4 {
            font-size: 14px;
            font-weight: 500;
        }

        .user-details span {
            font-size: 12px;
            color: var(--text-muted);
        }

        .logout-btn {
            display: flex;
            align-items: center;
            gap: 8px;
            margin-top: 1rem;
            padding: 8px 12px;
            background: transparent;
            border: 1px solid var(--border);
            color: var(--text-secondary);
            border-radius: 8px;
            cursor: pointer;
            width: 100%;
            justify-content: center;
            transition: all 0.2s ease;
        }

        .logout-btn:hover {
            background: var(--danger);
            border-color: var(--danger);
            color: white;
        }

        /* Main Content */
        .main-content {
            flex: 1;
            margin-left: 260px;
            padding: 2rem;
            min-height: 100vh;
        }

        /* Page Header */
        .page-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 2rem;
            flex-wrap: wrap;
            gap: 1rem;
        }

        .page-title {
            font-size: 1.75rem;
            font-weight: 700;
        }

        .page-actions {
            display: flex;
            gap: 12px;
            flex-wrap: wrap;
        }

        /* Buttons */
        .btn {
            display: inline-flex;
            align-items: center;
            gap: 8px;
            padding: 10px 20px;
            border-radius: 8px;
            font-size: 14px;
            font-weight: 500;
            cursor: pointer;
            transition: all 0.2s ease;
            border: none;
            font-family: inherit;
        }

        .btn-primary {
            background: var(--primary);
            color: white;
        }

        .btn-primary:hover {
            background: var(--primary-hover);
        }

        .btn-secondary {
            background: var(--surface);
            color: var(--text-primary);
            border: 1px solid var(--border);
        }

        .btn-secondary:hover {
            background: var(--surface-hover);
        }

        .btn-danger {
            background: var(--danger);
            color: white;
        }

        .btn-danger:hover {
            background: #dc2626;
        }

        .btn-success {
            background: var(--success);
            color: white;
        }

        .btn-success:hover {
            background: #059669;
        }

        .btn-icon {
            padding: 8px;
            border-radius: 8px;
            background: var(--surface);
            border: 1px solid var(--border);
            color: var(--text-secondary);
            cursor: pointer;
            transition: all 0.2s ease;
        }

        .btn-icon:hover {
            background: var(--surface-hover);
            color: var(--text-primary);
        }

        .btn-icon.whatsapp:hover {
            background: #25D366;
            border-color: #25D366;
            color: white;
        }

        /* Search Bar */
        .search-bar {
            position: relative;
            flex: 1;
            max-width: 400px;
        }

        .search-bar input {
            width: 100%;
            padding: 10px 16px 10px 44px;
            background: var(--surface);
            border: 1px solid var(--border);
            border-radius: 8px;
            color: var(--text-primary);
            font-size: 14px;
            font-family: inherit;
        }

        .search-bar input:focus {
            outline: none;
            border-color: var(--primary);
        }

        .search-bar input::placeholder {
            color: var(--text-muted);
        }

        .search-bar .material-icons {
            position: absolute;
            left: 14px;
            top: 50%;
            transform: translateY(-50%);
            color: var(--text-muted);
            font-size: 20px;
        }

        /* KPI Cards */
        .kpi-grid {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 1.5rem;
            margin-bottom: 2rem;
        }

        .kpi-card {
            background: var(--surface);
            border-radius: 12px;
            padding: 1.5rem;
            border: 1px solid var(--border);
        }

        .kpi-header {
            display: flex;
            justify-content: space-between;
            align-items: flex-start;
            margin-bottom: 1rem;
        }

        .kpi-icon {
            width: 48px;
            height: 48px;
            border-radius: 12px;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .kpi-icon.total { background: rgba(99, 102, 241, 0.1); color: var(--primary); }
        .kpi-icon.formados { background: rgba(16, 185, 129, 0.1); color: var(--success); }
        .kpi-icon.concluidos { background: rgba(59, 130, 246, 0.1); color: var(--info); }
        .kpi-icon.pendentes { background: rgba(245, 158, 11, 0.1); color: var(--warning); }

        .kpi-value {
            font-size: 2rem;
            font-weight: 700;
        }

        .kpi-label {
            font-size: 14px;
            color: var(--text-secondary);
        }

        /* Course Cards Grid */
        .courses-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
            gap: 1.5rem;
        }

        .course-card {
            background: var(--surface);
            border-radius: 12px;
            padding: 1.5rem;
            border: 1px solid var(--border);
            transition: all 0.2s ease;
        }

        .course-card:hover {
            border-color: var(--primary);
            transform: translateY(-2px);
        }

        .course-header {
            display: flex;
            justify-content: space-between;
            align-items: flex-start;
            margin-bottom: 1rem;
        }

        .course-title {
            font-size: 1.1rem;
            font-weight: 600;
            margin-bottom: 4px;
        }

        .course-id {
            font-size: 12px;
            color: var(--text-muted);
        }

        .status-badge {
            display: inline-flex;
            align-items: center;
            gap: 6px;
            padding: 4px 12px;
            border-radius: 20px;
            font-size: 12px;
            font-weight: 500;
        }

        .status-dot {
            width: 8px;
            height: 8px;
            border-radius: 50%;
        }

        .status-concluido { background: rgba(16, 185, 129, 0.1); color: var(--success); }
        .status-concluido .status-dot { background: var(--success); }

        .status-pendente { background: rgba(245, 158, 11, 0.1); color: var(--warning); }
        .status-pendente .status-dot { background: var(--warning); }

        .status-cancelado { background: rgba(239, 68, 68, 0.1); color: var(--danger); }
        .status-cancelado .status-dot { background: var(--danger); }

        .status-em-andamento { background: rgba(59, 130, 246, 0.1); color: var(--info); }
        .status-em-andamento .status-dot { background: var(--info); }

        .course-info {
            display: flex;
            flex-direction: column;
            gap: 8px;
            margin-bottom: 1rem;
        }

        .course-info-item {
            display: flex;
            align-items: center;
            gap: 8px;
            font-size: 14px;
            color: var(--text-secondary);
        }

        .course-info-item .material-icons {
            font-size: 18px;
            color: var(--text-muted);
        }

        .course-footer {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding-top: 1rem;
            border-top: 1px solid var(--border);
        }

        .course-instructor {
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .instructor-avatar {
            width: 32px;
            height: 32px;
            border-radius: 50%;
            background: var(--primary);
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 12px;
            font-weight: 600;
        }

        .course-actions {
            display: flex;
            gap: 8px;
        }

        /* Modal */
        .modal-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: var(--overlay);
            display: flex;
            align-items: center;
            justify-content: center;
            z-index: 1000;
            opacity: 0;
            visibility: hidden;
            transition: all 0.3s ease;
        }

        .modal-overlay.active {
            opacity: 1;
            visibility: visible;
        }

        .modal {
            background: var(--surface);
            border-radius: 16px;
            width: 100%;
            max-width: 500px;
            max-height: 90vh;
            overflow-y: auto;
            transform: scale(0.9);
            transition: transform 0.3s ease;
        }

        .modal-overlay.active .modal {
            transform: scale(1);
        }

        .modal-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 1.5rem;
            border-bottom: 1px solid var(--border);
        }

        .modal-title {
            font-size: 1.25rem;
            font-weight: 600;
        }

        .modal-close {
            background: none;
            border: none;
            color: var(--text-secondary);
            cursor: pointer;
            padding: 4px;
            border-radius: 4px;
            transition: all 0.2s ease;
        }

        .modal-close:hover {
            background: var(--surface-hover);
            color: var(--text-primary);
        }

        .modal-body {
            padding: 1.5rem;
        }

        .modal-footer {
            display: flex;
            justify-content: flex-end;
            gap: 12px;
            padding: 1.5rem;
            border-top: 1px solid var(--border);
        }

        /* Form */
        .form-group {
            margin-bottom: 1.25rem;
        }

        .form-label {
            display: block;
            margin-bottom: 8px;
            font-size: 14px;
            font-weight: 500;
            color: var(--text-secondary);
        }

        .form-input,
        .form-select {
            width: 100%;
            padding: 10px 14px;
            background: var(--background);
            border: 1px solid var(--border);
            border-radius: 8px;
            color: var(--text-primary);
            font-size: 14px;
            font-family: inherit;
        }

        .form-input:focus,
        .form-select:focus {
            outline: none;
            border-color: var(--primary);
        }

        .form-select {
            cursor: pointer;
        }

        .form-row {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 1rem;
        }

        /* Calendar */
        .calendar-container {
            background: var(--surface);
            border-radius: 12px;
            border: 1px solid var(--border);
            overflow: hidden;
        }

        .calendar-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 1.5rem;
            border-bottom: 1px solid var(--border);
        }

        .calendar-nav {
            display: flex;
            align-items: center;
            gap: 1rem;
        }

        .calendar-nav h3 {
            font-size: 1.25rem;
            font-weight: 600;
            min-width: 200px;
            text-align: center;
        }

        .calendar-grid {
            display: grid;
            grid-template-columns: repeat(7, 1fr);
        }

        .calendar-day-header {
            padding: 12px;
            text-align: center;
            font-size: 12px;
            font-weight: 600;
            color: var(--text-muted);
            text-transform: uppercase;
            border-bottom: 1px solid var(--border);
        }

        .calendar-day {
            min-height: 100px;
            padding: 8px;
            border-right: 1px solid var(--border);
            border-bottom: 1px solid var(--border);
            position: relative;
        }

        .calendar-day:nth-child(7n) {
            border-right: none;
        }

        .calendar-day.other-month {
            background: rgba(0, 0, 0, 0.2);
        }

        .calendar-day.other-month .day-number {
            color: var(--text-muted);
        }

        .day-number {
            font-size: 14px;
            font-weight: 500;
            margin-bottom: 4px;
        }

        .calendar-day.today .day-number {
            background: var(--primary);
            color: white;
            width: 28px;
            height: 28px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .calendar-event {
            display: flex;
            align-items: center;
            gap: 4px;
            padding: 4px 8px;
            border-radius: 4px;
            font-size: 11px;
            margin-bottom: 4px;
            cursor: pointer;
            overflow: hidden;
            text-overflow: ellipsis;
            white-space: nowrap;
        }

        .calendar-event.event-start {
            background: var(--success);
            color: white;
        }

        .calendar-event.event-end {
            background: var(--danger);
            color: white;
        }

        .calendar-event.event-middle {
            background: var(--info);
            height: 4px;
            padding: 0;
            margin: 8px 0;
        }

        .calendar-event .material-icons {
            font-size: 14px;
        }

        /* Instructors */
        .instructors-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
            gap: 1.5rem;
        }

        .instructor-card {
            background: var(--surface);
            border-radius: 12px;
            padding: 1.5rem;
            border: 1px solid var(--border);
            display: flex;
            align-items: center;
            gap: 1rem;
            transition: all 0.2s ease;
        }

        .instructor-card:hover {
            border-color: var(--primary);
        }

        .instructor-avatar-lg {
            width: 64px;
            height: 64px;
            border-radius: 50%;
            background: linear-gradient(135deg, var(--primary), #a855f7);
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.5rem;
            font-weight: 700;
            flex-shrink: 0;
        }

        .instructor-info {
            flex: 1;
        }

        .instructor-name {
            font-size: 1.1rem;
            font-weight: 600;
            margin-bottom: 4px;
        }

        .instructor-phone {
            font-size: 14px;
            color: var(--text-secondary);
            margin-bottom: 8px;
        }

        .instructor-courses {
            font-size: 12px;
            color: var(--text-muted);
        }

        .instructor-courses span {
            color: var(--primary);
            font-weight: 600;
        }

        .instructor-actions {
            display: flex;
            flex-direction: column;
            gap: 8px;
        }

        /* Settings */
        .settings-section {
            background: var(--surface);
            border-radius: 12px;
            padding: 1.5rem;
            border: 1px solid var(--border);
            margin-bottom: 1.5rem;
        }

        .settings-title {
            font-size: 1.1rem;
            font-weight: 600;
            margin-bottom: 1rem;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .settings-title .material-icons {
            color: var(--primary);
        }

        .settings-description {
            font-size: 14px;
            color: var(--text-secondary);
            margin-bottom: 1rem;
        }

        /* Loading */
        .loading {
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 3rem;
        }

        .spinner {
            width: 40px;
            height: 40px;
            border: 3px solid var(--border);
            border-top-color: var(--primary);
            border-radius: 50%;
            animation: spin 1s linear infinite;
        }

        @keyframes spin {
            to { transform: rotate(360deg); }
        }

        /* Empty State */
        .empty-state {
            text-align: center;
            padding: 3rem;
            color: var(--text-secondary);
        }

        .empty-state .material-icons {
            font-size: 64px;
            color: var(--text-muted);
            margin-bottom: 1rem;
        }

        .empty-state h3 {
            margin-bottom: 8px;
            color: var(--text-primary);
        }

        /* Page Section */
        .page-section {
            display: none;
        }

        .page-section.active {
            display: block;
        }

        /* Toast */
        .toast-container {
            position: fixed;
            bottom: 2rem;
            right: 2rem;
            z-index: 9999;
        }

        .toast {
            background: var(--surface);
            border: 1px solid var(--border);
            border-radius: 8px;
            padding: 1rem 1.5rem;
            margin-top: 0.5rem;
            display: flex;
            align-items: center;
            gap: 12px;
            animation: slideIn 0.3s ease;
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.3);
        }

        .toast.success { border-left: 4px solid var(--success); }
        .toast.error { border-left: 4px solid var(--danger); }
        .toast.info { border-left: 4px solid var(--info); }

        @keyframes slideIn {
            from {
                transform: translateX(100%);
                opacity: 0;
            }
            to {
                transform: translateX(0);
                opacity: 1;
            }
        }

        /* Responsive */
        @media (max-width: 768px) {
            .sidebar {
                transform: translateX(-100%);
            }

            .sidebar.open {
                transform: translateX(0);
            }

            .main-content {
                margin-left: 0;
                padding: 1rem;
            }

            .kpi-grid {
                grid-template-columns: repeat(2, 1fr);
            }

            .courses-grid,
            .instructors-grid {
                grid-template-columns: 1fr;
            }

            .page-header {
                flex-direction: column;
                align-items: stretch;
            }

            .search-bar {
                max-width: 100%;
            }

            .form-row {
                grid-template-columns: 1fr;
            }

            .calendar-day {
                min-height: 60px;
            }

            .calendar-event {
                font-size: 9px;
                padding: 2px 4px;
            }

            .mobile-menu-btn {
                display: flex !important;
            }
        }

        .mobile-menu-btn {
            display: none;
            position: fixed;
            top: 1rem;
            left: 1rem;
            z-index: 101;
            background: var(--surface);
            border: 1px solid var(--border);
            border-radius: 8px;
            padding: 8px;
            color: var(--text-primary);
            cursor: pointer;
        }

        .confirm-modal .modal-body {
            text-align: center;
            padding: 2rem;
        }

        .confirm-modal .material-icons.warning {
            font-size: 64px;
            color: var(--warning);
            margin-bottom: 1rem;
        }
    </style>
</head>
<body>
    <!-- Login Overlay -->
    <div class="login-overlay" id="loginOverlay">
        <div class="login-card">
            <div class="login-logo">
                <span class="material-icons">school</span>
                <h1>CSF Manager</h1>
            </div>
            <div class="login-error" id="loginError">Usuário ou senha inválidos!</div>
            <form id="loginForm">
                <div class="form-group">
                    <label class="form-label">Usuário</label>
                    <input type="text" class="form-input" id="loginUser" placeholder="Digite seu usuário" required>
                </div>
                <div class="form-group">
                    <label class="form-label">Senha</label>
                    <input type="password" class="form-input" id="loginPass" placeholder="Digite sua senha" required>
                </div>
                <button type="submit" class="btn btn-primary" style="width: 100%;">
                    <span class="material-icons">login</span>
                    Entrar
                </button>
            </form>
        </div>
    </div>

    <!-- Mobile Menu Button -->
    <button class="mobile-menu-btn" id="mobileMenuBtn">
        <span class="material-icons">menu</span>
    </button>

    <!-- App Container -->
    <div class="app-container">
        <!-- Sidebar -->
        <aside class="sidebar" id="sidebar">
            <div class="sidebar-header">
                <div class="sidebar-logo">
                    <span class="material-icons">school</span>
                    <div>
                        <h1>CSF Manager</h1>
                        <span>Cloud Sync Realtime</span>
                    </div>
                </div>
            </div>
            <nav class="sidebar-nav">
                <div class="nav-item active" data-page="dashboard">
                    <span class="material-icons">dashboard</span>
                    Dashboard
                </div>
                <div class="nav-item" data-page="calendar">
                    <span class="material-icons">calendar_month</span>
                    Calendário
                </div>
                <div class="nav-item" data-page="instructors">
                    <span class="material-icons">people</span>
                    Instrutores
                </div>
                <div class="nav-item admin-only" data-page="settings">
                    <span class="material-icons">settings</span>
                    Configurações
                </div>
            </nav>
            <div class="sidebar-footer">
                <div class="user-info">
                    <div class="user-avatar" id="userAvatar">U</div>
                    <div class="user-details">
                        <h4 id="userName">Usuário</h4>
                        <span id="userRole">Admin</span>
                    </div>
                </div>
                <button class="logout-btn" id="logoutBtn">
                    <span class="material-icons">logout</span>
                    Sair
                </button>
            </div>
        </aside>

        <!-- Main Content -->
        <main class="main-content">
            <!-- Dashboard Page -->
            <section class="page-section active" id="dashboardPage">
                <div class="page-header">
                    <h2 class="page-title">Dashboard</h2>
                    <div class="page-actions">
                        <div class="search-bar">
                            <span class="material-icons">search</span>
                            <input type="text" id="searchInput" placeholder="Buscar cursos...">
                        </div>
                        <button class="btn btn-secondary" id="exportCsvBtn">
                            <span class="material-icons">download</span>
                            Exportar CSV
                        </button>
                        <button class="btn btn-primary admin-only" id="newCourseBtn">
                            <span class="material-icons">add</span>
                            Novo Curso
                        </button>
                    </div>
                </div>

                <div class="kpi-grid">
                    <div class="kpi-card">
                        <div class="kpi-header">
                            <div>
                                <div class="kpi-value" id="kpiTotal">0</div>
                                <div class="kpi-label">Total de Cursos</div>
                            </div>
                            <div class="kpi-icon total">
                                <span class="material-icons">school</span>
                            </div>
                        </div>
                    </div>
                    <div class="kpi-card">
                        <div class="kpi-header">
                            <div>
                                <div class="kpi-value" id="kpiFormados">0</div>
                                <div class="kpi-label">Total Formados</div>
                            </div>
                            <div class="kpi-icon formados">
                                <span class="material-icons">workspace_premium</span>
                            </div>
                        </div>
                    </div>
                    <div class="kpi-card">
                        <div class="kpi-header">
                            <div>
                                <div class="kpi-value" id="kpiConcluidos">0</div>
                                <div class="kpi-label">Concluídos</div>
                            </div>
                            <div class="kpi-icon concluidos">
                                <span class="material-icons">check_circle</span>
                            </div>
                        </div>
                    </div>
                    <div class="kpi-card">
                        <div class="kpi-header">
                            <div>
                                <div class="kpi-value" id="kpiPendentes">0</div>
                                <div class="kpi-label">Pendentes</div>
                            </div>
                            <div class="kpi-icon pendentes">
                                <span class="material-icons">pending</span>
                            </div>
                        </div>
                    </div>
                </div>

                <div class="courses-grid" id="coursesGrid">
                    <div class="loading">
                        <div class="spinner"></div>
                    </div>
                </div>
            </section>

            <!-- Calendar Page -->
            <section class="page-section" id="calendarPage">
                <div class="page-header">
                    <h2 class="page-title">Calendário</h2>
                </div>

                <div class="calendar-container">
                    <div class="calendar-header">
                        <div class="calendar-nav">
                            <button class="btn-icon" id="prevMonth">
                                <span class="material-icons">chevron_left</span>
                            </button>
                            <h3 id="calendarTitle">Janeiro 2025</h3>
                            <button class="btn-icon" id="nextMonth">
                                <span class="material-icons">chevron_right</span>
                            </button>
                        </div>
                        <button class="btn btn-secondary" id="todayBtn">
                            <span class="material-icons">today</span>
                            Hoje
                        </button>
                    </div>
                    <div class="calendar-grid" id="calendarGrid">
                        <div class="calendar-day-header">Dom</div>
                        <div class="calendar-day-header">Seg</div>
                        <div class="calendar-day-header">Ter</div>
                        <div class="calendar-day-header">Qua</div>
                        <div class="calendar-day-header">Qui</div>
                        <div class="calendar-day-header">Sex</div>
                        <div class="calendar-day-header">Sáb</div>
                    </div>
                </div>
            </section>

            <!-- Instructors Page -->
            <section class="page-section" id="instructorsPage">
                <div class="page-header">
                    <h2 class="page-title">Instrutores</h2>
                    <div class="page-actions">
                        <button class="btn btn-primary admin-only" id="newInstructorBtn">
                            <span class="material-icons">person_add</span>
                            Novo Instrutor
                        </button>
                    </div>
                </div>

                <div class="instructors-grid" id="instructorsGrid">
                    <div class="loading">
                        <div class="spinner"></div>
                    </div>
                </div>
            </section>

            <!-- Settings Page -->
            <section class="page-section" id="settingsPage">
                <div class="page-header">
                    <h2 class="page-title">Configurações</h2>
                </div>

                <div class="settings-section">
                    <h3 class="settings-title">
                        <span class="material-icons">cloud_upload</span>
                        Importar Dados Padrão
                    </h3>
                    <p class="settings-description">
                        Importa um conjunto de dados de exemplo para o sistema. Isso irá adicionar cursos e instrutores de demonstração.
                    </p>
                    <button class="btn btn-primary" id="importDataBtn">
                        <span class="material-icons">upload</span>
                        Importar Dados
                    </button>
                </div>

                <div class="settings-section">
                    <h3 class="settings-title">
                        <span class="material-icons">delete_forever</span>
                        Limpar Dados
                    </h3>
                    <p class="settings-description">
                        Remove todos os cursos e instrutores do sistema. Esta ação não pode ser desfeita.
                    </p>
                    <button class="btn btn-danger" id="clearDataBtn">
                        <span class="material-icons">delete</span>
                        Limpar Todos os Dados
                    </button>
                </div>
            </section>
        </main>
    </div>

    <!-- Course Modal -->
    <div class="modal-overlay" id="courseModal">
        <div class="modal">
            <div class="modal-header">
                <h3 class="modal-title" id="courseModalTitle">Novo Curso</h3>
                <button class="modal-close" onclick="closeModal('courseModal')">
                    <span class="material-icons">close</span>
                </button>
            </div>
            <form id="courseForm">
                <div class="modal-body">
                    <div class="form-group">
                        <label class="form-label">Nome do Curso</label>
                        <input type="text" class="form-input" id="courseName" required>
                    </div>
                    <div class="form-row">
                        <div class="form-group">
                            <label class="form-label">ID Lote</label>
                            <input type="number" class="form-input" id="courseIdLote" required>
                        </div>
                        <div class="form-group">
                            <label class="form-label">Cidade</label>
                            <input type="text" class="form-input" id="courseCity" required>
                        </div>
                    </div>
                    <div class="form-row">
                        <div class="form-group">
                            <label class="form-label">Data Início</label>
                            <input type="date" class="form-input" id="courseStart" required>
                        </div>
                        <div class="form-group">
                            <label class="form-label">Data Fim</label>
                            <input type="date" class="form-input" id="courseEnd" required>
                        </div>
                    </div>
                    <div class="form-row">
                        <div class="form-group">
                            <label class="form-label">Status</label>
                            <select class="form-select" id="courseStatus" required>
                                <option value="Pendente">Pendente</option>
                                <option value="Em Andamento">Em Andamento</option>
                                <option value="Concluído">Concluído</option>
                                <option value="Cancelado">Cancelado</option>
                            </select>
                        </div>
                        <div class="form-group">
                            <label class="form-label">Nº Concluintes</label>
                            <input type="number" class="form-input" id="courseStudents" value="0" min="0">
                        </div>
                    </div>
                    <div class="form-group">
                        <label class="form-label">Local</label>
                        <input type="text" class="form-input" id="courseLocation" required>
                    </div>
                    <div class="form-group">
                        <label class="form-label">Instrutor</label>
                        <select class="form-select" id="courseInstructor" required>
                            <option value="">Selecione um instrutor</option>
                        </select>
                    </div>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" onclick="closeModal('courseModal')">Cancelar</button>
                    <button type="submit" class="btn btn-primary">Salvar</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Instructor Modal -->
    <div class="modal-overlay" id="instructorModal">
        <div class="modal">
            <div class="modal-header">
                <h3 class="modal-title" id="instructorModalTitle">Novo Instrutor</h3>
                <button class="modal-close" onclick="closeModal('instructorModal')">
                    <span class="material-icons">close</span>
                </button>
            </div>
            <form id="instructorForm">
                <div class="modal-body">
                    <div class="form-group">
                        <label class="form-label">Nome</label>
                        <input type="text" class="form-input" id="instructorName" required>
                    </div>
                    <div class="form-group">
                        <label class="form-label">Telefone</label>
                        <input type="tel" class="form-input" id="instructorPhone" placeholder="(00) 00000-0000">
                    </div>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" onclick="closeModal('instructorModal')">Cancelar</button>
                    <button type="submit" class="btn btn-primary">Salvar</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Confirm Modal -->
    <div class="modal-overlay confirm-modal" id="confirmModal">
        <div class="modal" style="max-width: 400px;">
            <div class="modal-body">
                <span class="material-icons warning">warning</span>
                <h3 id="confirmTitle">Confirmar Ação</h3>
                <p id="confirmMessage" style="color: var(--text-secondary); margin-top: 8px;">Tem certeza que deseja continuar?</p>
            </div>
            <div class="modal-footer" style="justify-content: center;">
                <button type="button" class="btn btn-secondary" onclick="closeModal('confirmModal')">Cancelar</button>
                <button type="button" class="btn btn-danger" id="confirmBtn">Confirmar</button>
            </div>
        </div>
    </div>

    <!-- Toast Container -->
    <div class="toast-container" id="toastContainer"></div>

    <!-- Firebase SDK -->
    <script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-firestore-compat.js"></script>

    <script>
        // Firebase Configuration
        const firebaseConfig = {
            apiKey: "AIzaSyBq5qmLy6HAzZAeC4KCVjLfdCM77ttOU3A",
            authDomain: "csf-iac.firebaseapp.com",
            projectId: "csf-iac",
            storageBucket: "csf-iac.firebasestorage.app",
            messagingSenderId: "484645494526",
            appId: "1:484645494526:web:f5ecc23c7c0edbca320faf",
            measurementId: "G-XL3WY01E73"
        };

        // Initialize Firebase
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();

        // Global State
        let cursos = [];
        let instrutores = [];
        let currentUser = null;
        let editingCourseId = null;
        let editingInstructorId = null;
        let currentCalendarDate = new Date();

        // Users
        const users = {
            'Csfiac': { password: '032147', role: 'admin', name: 'Administrador' },
            'visitante': { password: '1234', role: 'viewer', name: 'Visitante' }
        };

        // DOM Elements
        const loginOverlay = document.getElementById('loginOverlay');
        const loginForm = document.getElementById('loginForm');
        const loginError = document.getElementById('loginError');
        const sidebar = document.getElementById('sidebar');
        const mobileMenuBtn = document.getElementById('mobileMenuBtn');

        // Initialize App
        document.addEventListener('DOMContentLoaded', () => {
            setupEventListeners();
            checkSession();
        });

        // Setup Event Listeners
        function setupEventListeners() {
            // Login
            loginForm.addEventListener('submit', handleLogin);
            document.getElementById('logoutBtn').addEventListener('click', handleLogout);

            // Mobile Menu
            mobileMenuBtn.addEventListener('click', () => sidebar.classList.toggle('open'));

            // Navigation
            document.querySelectorAll('.nav-item').forEach(item => {
                item.addEventListener('click', () => navigateTo(item.dataset.page));
            });

            // Search
            document.getElementById('searchInput').addEventListener('input', (e) => {
                renderCourses(e.target.value);
            });

            // Buttons
            document.getElementById('newCourseBtn').addEventListener('click', () => openCourseModal());
            document.getElementById('newInstructorBtn').addEventListener('click', () => openInstructorModal());
            document.getElementById('exportCsvBtn').addEventListener('click', exportToCsv);
            document.getElementById('importDataBtn').addEventListener('click', importDefaultData);
            document.getElementById('clearDataBtn').addEventListener('click', clearAllData);

            // Calendar Navigation
            document.getElementById('prevMonth').addEventListener('click', () => changeMonth(-1));
            document.getElementById('nextMonth').addEventListener('click', () => changeMonth(1));
            document.getElementById('todayBtn').addEventListener('click', goToToday);

            // Forms
            document.getElementById('courseForm').addEventListener('submit', handleCourseSubmit);
            document.getElementById('instructorForm').addEventListener('submit', handleInstructorSubmit);

            // Close modals on overlay click
            document.querySelectorAll('.modal-overlay').forEach(overlay => {
                overlay.addEventListener('click', (e) => {
                    if (e.target === overlay) closeModal(overlay.id);
                });
            });
        }

        // Session Management
        function checkSession() {
            const session = localStorage.getItem('csf_session');
            if (session) {
                const user = JSON.parse(session);
                if (users[user.username]) {
                    loginUser(user.username);
                }
            }
        }

        // Login Handler
        function handleLogin(e) {
            e.preventDefault();
            const username = document.getElementById('loginUser').value;
            const password = document.getElementById('loginPass').value;

            if (users[username] && users[username].password === password) {
                localStorage.setItem('csf_session', JSON.stringify({ username }));
                loginUser(username);
                loginError.classList.remove('show');
            } else {
                loginError.classList.add('show');
            }
        }

        function loginUser(username) {
            currentUser = { ...users[username], username };
            loginOverlay.classList.add('hidden');
            
            // Update UI
            document.getElementById('userName').textContent = currentUser.name;
            document.getElementById('userRole').textContent = currentUser.role === 'admin' ? 'Administrador' : 'Visualizador';
            document.getElementById('userAvatar').textContent = currentUser.name.charAt(0).toUpperCase();

            // Hide admin-only elements for viewers
            if (currentUser.role !== 'admin') {
                document.querySelectorAll('.admin-only').forEach(el => el.style.display = 'none');
            }

            // Start realtime listeners
            startRealtimeListeners();
        }

        function handleLogout() {
            localStorage.removeItem('csf_session');
            currentUser = null;
            location.reload();
        }

        // Realtime Listeners
        function startRealtimeListeners() {
            // Courses listener
            db.collection('cursos').onSnapshot(snapshot => {
                cursos = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
                renderCourses();
                updateKPIs();
                renderCalendar();
            }, error => {
                console.error('Error listening to courses:', error);
                showToast('Erro ao carregar cursos', 'error');
            });

            // Instructors listener
            db.collection('instrutores').onSnapshot(snapshot => {
                instrutores = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
                renderInstructors();
                updateInstructorSelect();
            }, error => {
                console.error('Error listening to instructors:', error);
                showToast('Erro ao carregar instrutores', 'error');
            });
        }

        // Navigation
        function navigateTo(page) {
            document.querySelectorAll('.nav-item').forEach(item => item.classList.remove('active'));
            document.querySelector(`[data-page="${page}"]`).classList.add('active');
            
            document.querySelectorAll('.page-section').forEach(section => section.classList.remove('active'));
            document.getElementById(`${page}Page`).classList.add('active');

            sidebar.classList.remove('open');

            if (page === 'calendar') renderCalendar();
        }

        // KPIs
        function updateKPIs() {
            const total = cursos.length;
            const formados = cursos.reduce((sum, c) => sum + (parseInt(c.alunos) || 0), 0);
            const concluidos = cursos.filter(c => c.status === 'Concluído').length;
            const pendentes = cursos.filter(c => c.status === 'Pendente').length;

            document.getElementById('kpiTotal').textContent = total;
            document.getElementById('kpiFormados').textContent = formados;
            document.getElementById('kpiConcluidos').textContent = concluidos;
            document.getElementById('kpiPendentes').textContent = pendentes;
        }

        // Render Courses
        function renderCourses(searchTerm = '') {
            const grid = document.getElementById('coursesGrid');
            let filtered = cursos;

            if (searchTerm) {
                const term = searchTerm.toLowerCase();
                filtered = cursos.filter(c => 
                    c.curso?.toLowerCase().includes(term) ||
                    c.cidade?.toLowerCase().includes(term) ||
                    c.instrutor?.toLowerCase().includes(term)
                );
            }

            if (filtered.length === 0) {
                grid.innerHTML = `
                    <div class="empty-state" style="grid-column: 1 / -1;">
                        <span class="material-icons">school</span>
                        <h3>${searchTerm ? 'Nenhum curso encontrado' : 'Nenhum curso cadastrado'}</h3>
                        <p>${searchTerm ? 'Tente buscar por outro termo' : 'Clique em "Novo Curso" para adicionar'}</p>
                    </div>
                `;
                return;
            }

            grid.innerHTML = filtered.map(course => {
                const statusClass = getStatusClass(course.status);
                const initials = getInitials(course.instrutor || 'N/A');
                
                return `
                    <div class="course-card">
                        <div class="course-header">
                            <div>
                                <h3 class="course-title">${escapeHtml(course.curso || 'Sem nome')}</h3>
                                <span class="course-id">Lote #${course.idLote || 'N/A'}</span>
                            </div>
                            <span class="status-badge ${statusClass}">
                                <span class="status-dot"></span>
                                ${escapeHtml(course.status || 'N/A')}
                            </span>
                        </div>
                        <div class="course-info">
                            <div class="course-info-item">
                                <span class="material-icons">location_on</span>
                                ${escapeHtml(course.cidade || 'N/A')} - ${escapeHtml(course.local || 'N/A')}
                            </div>
                            <div class="course-info-item">
                                <span class="material-icons">event</span>
                                ${formatDate(course.inicio)} - ${formatDate(course.fim)}
                            </div>
                            <div class="course-info-item">
                                <span class="material-icons">people</span>
                                ${course.alunos || 0} concluintes
                            </div>
                        </div>
                        <div class="course-footer">
                            <div class="course-instructor">
                                <div class="instructor-avatar">${initials}</div>
                                <span>${escapeHtml(course.instrutor || 'Sem instrutor')}</span>
                            </div>
                            ${currentUser?.role === 'admin' ? `
                                <div class="course-actions">
                                    <button class="btn-icon" onclick="openCourseModal('${course.id}')" title="Editar">
                                        <span class="material-icons">edit</span>
                                    </button>
                                    <button class="btn-icon" onclick="deleteCourse('${course.id}')" title="Excluir">
                                        <span class="material-icons">delete</span>
                                    </button>
                                </div>
                            ` : ''}
                        </div>
                    </div>
                `;
            }).join('');
        }

        // Render Instructors
        function renderInstructors() {
            const grid = document.getElementById('instructorsGrid');

            if (instrutores.length === 0) {
                grid.innerHTML = `
                    <div class="empty-state" style="grid-column: 1 / -1;">
                        <span class="material-icons">people</span>
                        <h3>Nenhum instrutor cadastrado</h3>
                        <p>Clique em "Novo Instrutor" para adicionar</p>
                    </div>
                `;
                return;
            }

            grid.innerHTML = instrutores.map(instructor => {
                const initials = getInitials(instructor.nome);
                const courseCount = cursos.filter(c => c.instrutor === instructor.nome).length;
                const phone = instructor.tel ? instructor.tel.replace(/\D/g, '') : '';
                const hasValidPhone = phone.length >= 10;

                return `
                    <div class="instructor-card">
                        <div class="instructor-avatar-lg">${initials}</div>
                        <div class="instructor-info">
                            <h3 class="instructor-name">${escapeHtml(instructor.nome)}</h3>
                            <p class="instructor-phone">${instructor.tel || 'Sem telefone'}</p>
                            <p class="instructor-courses"><span>${courseCount}</span> curso(s) ministrado(s)</p>
                        </div>
                        <div class="instructor-actions">
                            ${hasValidPhone ? `
                                <button class="btn-icon whatsapp" onclick="openWhatsApp('${phone}')" title="WhatsApp">
                                    <span class="material-icons">chat</span>
                                </button>
                            ` : ''}
                            ${currentUser?.role === 'admin' ? `
                                <button class="btn-icon" onclick="openInstructorModal('${instructor.id}')" title="Editar">
                                    <span class="material-icons">edit</span>
                                </button>
                                <button class="btn-icon" onclick="deleteInstructor('${instructor.id}')" title="Excluir">
                                    <span class="material-icons">delete</span>
                                </button>
                            ` : ''}
                        </div>
                    </div>
                `;
            }).join('');
        }

        // Update Instructor Select
        function updateInstructorSelect() {
            const select = document.getElementById('courseInstructor');
            select.innerHTML = '<option value="">Selecione um instrutor</option>' +
                instrutores.map(i => `<option value="${escapeHtml(i.nome)}">${escapeHtml(i.nome)}</option>`).join('');
        }

        // Calendar
        function renderCalendar() {
            const grid = document.getElementById('calendarGrid');
            const title = document.getElementById('calendarTitle');
            
            const year = currentCalendarDate.getFullYear();
            const month = currentCalendarDate.getMonth();
            
            const months = ['Janeiro', 'Fevereiro', 'Março', 'Abril', 'Maio', 'Junho',
                           'Julho', 'Agosto', 'Setembro', 'Outubro', 'Novembro', 'Dezembro'];
            title.textContent = `${months[month]} ${year}`;

            const firstDay = new Date(year, month, 1);
            const lastDay = new Date(year, month + 1, 0);
            const startDate = new Date(firstDay);
            startDate.setDate(startDate.getDate() - firstDay.getDay());

            const today = new Date();
            today.setHours(0, 0, 0, 0);

            // Keep headers
            let html = `
                <div class="calendar-day-header">Dom</div>
                <div class="calendar-day-header">Seg</div>
                <div class="calendar-day-header">Ter</div>
                <div class="calendar-day-header">Qua</div>
                <div class="calendar-day-header">Qui</div>
                <div class="calendar-day-header">Sex</div>
                <div class="calendar-day-header">Sáb</div>
            `;

            const currentDate = new Date(startDate);
            
            for (let i = 0; i < 42; i++) {
                const dateStr = formatDateISO(currentDate);
                const isOtherMonth = currentDate.getMonth() !== month;
                const isToday = currentDate.getTime() === today.getTime();
                
                let classes = 'calendar-day';
                if (isOtherMonth) classes += ' other-month';
                if (isToday) classes += ' today';

                const events = getEventsForDate(dateStr);
                
                html += `
                    <div class="${classes}">
                        <div class="day-number">${currentDate.getDate()}</div>
                        ${events}
                    </div>
                `;
                
                currentDate.setDate(currentDate.getDate() + 1);
            }

            grid.innerHTML = html;
        }

        function getEventsForDate(dateStr) {
            let events = '';
            
            cursos.forEach(course => {
                if (!course.inicio || !course.fim) return;
                
                const start = course.inicio;
                const end = course.fim;
                
                if (dateStr === start) {
                    events += `
                        <div class="calendar-event event-start" title="${escapeHtml(course.curso)}">
                            <span class="material-icons">play_arrow</span>
                            ${escapeHtml(course.curso?.substring(0, 10) || '')}
                        </div>
                    `;
                } else if (dateStr === end) {
                    events += `
                        <div class="calendar-event event-end" title="${escapeHtml(course.curso)}">
                            <span class="material-icons">flag</span>
                            ${escapeHtml(course.curso?.substring(0, 10) || '')}
                        </div>
                    `;
                } else if (dateStr > start && dateStr < end) {
                    events += `<div class="calendar-event event-middle" title="${escapeHtml(course.curso)}"></div>`;
                }
            });
            
            return events;
        }

        function changeMonth(delta) {
            currentCalendarDate.setMonth(currentCalendarDate.getMonth() + delta);
            renderCalendar();
        }

        function goToToday() {
            currentCalendarDate = new Date();
            renderCalendar();
        }

        // Modal Functions
        function openModal(modalId) {
            document.getElementById(modalId).classList.add('active');
        }

        function closeModal(modalId) {
            document.getElementById(modalId).classList.remove('active');
        }

        function openCourseModal(courseId = null) {
            editingCourseId = courseId;
            const form = document.getElementById('courseForm');
            const title = document.getElementById('courseModalTitle');
            
            form.reset();
            
            if (courseId) {
                title.textContent = 'Editar Curso';
                const course = cursos.find(c => c.id === courseId);
                if (course) {
                    document.getElementById('courseName').value = course.curso || '';
                    document.getElementById('courseIdLote').value = course.idLote || '';
                    document.getElementById('courseCity').value = course.cidade || '';
                    document.getElementById('courseStart').value = course.inicio || '';
                    document.getElementById('courseEnd').value = course.fim || '';
                    document.getElementById('courseStatus').value = course.status || 'Pendente';
                    document.getElementById('courseStudents').value = course.alunos || 0;
                    document.getElementById('courseLocation').value = course.local || '';
                    document.getElementById('courseInstructor').value = course.instrutor || '';
                }
            } else {
                title.textContent = 'Novo Curso';
            }
            
            openModal('courseModal');
        }

        function openInstructorModal(instructorId = null) {
            editingInstructorId = instructorId;
            const form = document.getElementById('instructorForm');
            const title = document.getElementById('instructorModalTitle');
            
            form.reset();
            
            if (instructorId) {
                title.textContent = 'Editar Instrutor';
                const instructor = instrutores.find(i => i.id === instructorId);
                if (instructor) {
                    document.getElementById('instructorName').value = instructor.nome || '';
                    document.getElementById('instructorPhone').value = instructor.tel || '';
                }
            } else {
                title.textContent = 'Novo Instrutor';
            }
            
            openModal('instructorModal');
        }

        // Form Handlers
        async function handleCourseSubmit(e) {
            e.preventDefault();
            
            const courseData = {
                curso: document.getElementById('courseName').value,
                idLote: parseInt(document.getElementById('courseIdLote').value),
                cidade: document.getElementById('courseCity').value,
                inicio: document.getElementById('courseStart').value,
                fim: document.getElementById('courseEnd').value,
                status: document.getElementById('courseStatus').value,
                alunos: parseInt(document.getElementById('courseStudents').value) || 0,
                local: document.getElementById('courseLocation').value,
                instrutor: document.getElementById('courseInstructor').value
            };

            try {
                if (editingCourseId) {
                    await db.collection('cursos').doc(editingCourseId).update(courseData);
                    showToast('Curso atualizado com sucesso!', 'success');
                } else {
                    await db.collection('cursos').add(courseData);
                    showToast('Curso criado com sucesso!', 'success');
                }
                closeModal('courseModal');
            } catch (error) {
                console.error('Error saving course:', error);
                showToast('Erro ao salvar curso', 'error');
            }
        }

        async function handleInstructorSubmit(e) {
            e.preventDefault();
            
            const instructorData = {
                nome: document.getElementById('instructorName').value,
                tel: document.getElementById('instructorPhone').value
            };

            try {
                if (editingInstructorId) {
                    await db.collection('instrutores').doc(editingInstructorId).update(instructorData);
                    showToast('Instrutor atualizado com sucesso!', 'success');
                } else {
                    await db.collection('instrutores').add(instructorData);
                    showToast('Instrutor criado com sucesso!', 'success');
                }
                closeModal('instructorModal');
            } catch (error) {
                console.error('Error saving instructor:', error);
                showToast('Erro ao salvar instrutor', 'error');
            }
        }

        // Delete Functions
        function deleteCourse(courseId) {
            showConfirm('Excluir Curso', 'Tem certeza que deseja excluir este curso?', async () => {
                try {
                    await db.collection('cursos').doc(courseId).delete();
                    showToast('Curso excluído com sucesso!', 'success');
                } catch (error) {
                    console.error('Error deleting course:', error);
                    showToast('Erro ao excluir curso', 'error');
                }
            });
        }

        function deleteInstructor(instructorId) {
            showConfirm('Excluir Instrutor', 'Tem certeza que deseja excluir este instrutor?', async () => {
                try {
                    await db.collection('instrutores').doc(instructorId).delete();
                    showToast('Instrutor excluído com sucesso!', 'success');
                } catch (error) {
                    console.error('Error deleting instructor:', error);
                    showToast('Erro ao excluir instrutor', 'error');
                }
            });
        }

        // Confirm Dialog
        function showConfirm(title, message, onConfirm) {
            document.getElementById('confirmTitle').textContent = title;
            document.getElementById('confirmMessage').textContent = message;
            document.getElementById('confirmBtn').onclick = () => {
                closeModal('confirmModal');
                onConfirm();
            };
            openModal('confirmModal');
        }

        // Export CSV
        function exportToCsv() {
            if (cursos.length === 0) {
                showToast('Nenhum curso para exportar', 'info');
                return;
            }

            const headers = ['Curso', 'ID Lote', 'Cidade', 'Início', 'Fim', 'Status', 'Concluintes', 'Local', 'Instrutor'];
            const rows = cursos.map(c => [
                c.curso || '',
                c.idLote || '',
                c.cidade || '',
                c.inicio || '',
                c.fim || '',
                c.status || '',
                c.alunos || 0,
                c.local || '',
                c.instrutor || ''
            ]);

            let csv = headers.join(';') + '\n';
            rows.forEach(row => {
                csv += row.map(cell => `"${String(cell).replace(/"/g, '""')}"`).join(';') + '\n';
            });

            const blob = new Blob(['\ufeff' + csv], { type: 'text/csv;charset=utf-8;' });
            const link = document.createElement('a');
            link.href = URL.createObjectURL(blob);
            link.download = `cursos_${formatDateISO(new Date())}.csv`;
            link.click();
            
            showToast('CSV exportado com sucesso!', 'success');
        }

        // Import Default Data
        async function importDefaultData() {
            showConfirm('Importar Dados', 'Isso irá adicionar dados de exemplo. Deseja continuar?', async () => {
                try {
                    const batch = db.batch();

                    // Default Instructors
                    const defaultInstructors = [
                        { nome: 'Carlos Silva', tel: '(11) 99999-1234' },
                        { nome: 'Maria Santos', tel: '(21) 98888-5678' },
                        { nome: 'João Oliveira', tel: '(31) 97777-9012' }
                    ];

                    defaultInstructors.forEach(instructor => {
                        const ref = db.collection('instrutores').doc();
                        batch.set(ref, instructor);
                    });

                    // Default Courses
                    const defaultCourses = [
                        {
                            curso: 'Brigadista de Incêndio',
                            idLote: 1001,
                            cidade: 'São Paulo',
                            inicio: '2025-01-15',
                            fim: '2025-01-20',
                            status: 'Concluído',
                            alunos: 25,
                            local: 'Centro de Treinamento SP',
                            instrutor: 'Carlos Silva'
                        },
                        {
                            curso: 'NR-35 Trabalho em Altura',
                            idLote: 1002,
                            cidade: 'Rio de Janeiro',
                            inicio: '2025-02-01',
                            fim: '2025-02-05',
                            status: 'Em Andamento',
                            alunos: 18,
                            local: 'Unidade RJ',
                            instrutor: 'Maria Santos'
                        },
                        {
                            curso: 'CIPA - Comissão Interna',
                            idLote: 1003,
                            cidade: 'Belo Horizonte',
                            inicio: '2025-03-10',
                            fim: '2025-03-15',
                            status: 'Pendente',
                            alunos: 0,
                            local: 'Centro BH',
                            instrutor: 'João Oliveira'
                        }
                    ];

                    defaultCourses.forEach(course => {
                        const ref = db.collection('cursos').doc();
                        batch.set(ref, course);
                    });

                    await batch.commit();
                    showToast('Dados importados com sucesso!', 'success');
                } catch (error) {
                    console.error('Error importing data:', error);
                    showToast('Erro ao importar dados', 'error');
                }
            });
        }

        // Clear All Data
        async function clearAllData() {
            showConfirm('Limpar Dados', 'Esta ação irá remover TODOS os cursos e instrutores. Esta ação não pode ser desfeita!', async () => {
                try {
                    const batch = db.batch();

                    const coursesSnapshot = await db.collection('cursos').get();
                    coursesSnapshot.docs.forEach(doc => batch.delete(doc.ref));

                    const instructorsSnapshot = await db.collection('instrutores').get();
                    instructorsSnapshot.docs.forEach(doc => batch.delete(doc.ref));

                    await batch.commit();
                    showToast('Todos os dados foram removidos!', 'success');
                } catch (error) {
                    console.error('Error clearing data:', error);
                    showToast('Erro ao limpar dados', 'error');
                }
            });
        }

        // WhatsApp
        function openWhatsApp(phone) {
            const cleanPhone = phone.replace(/\D/g, '');
            if (cleanPhone.length >= 10) {
                const fullPhone = cleanPhone.startsWith('55') ? cleanPhone : '55' + cleanPhone;
                window.open(`https://wa.me/${fullPhone}`, '_blank');
            }
        }

        // Toast
        function showToast(message, type = 'info') {
            const container = document.getElementById('toastContainer');
            const toast = document.createElement('div');
            toast.className = `toast ${type}`;
            toast.innerHTML = `
                <span class="material-icons">${type === 'success' ? 'check_circle' : type === 'error' ? 'error' : 'info'}</span>
                <span>${message}</span>
            `;
            container.appendChild(toast);
            
            setTimeout(() => {
                toast.style.opacity = '0';
                setTimeout(() => toast.remove(), 300);
            }, 3000);
        }

        // Utility Functions
        function getStatusClass(status) {
            const classes = {
                'Concluído': 'status-concluido',
                'Pendente': 'status-pendente',
                'Cancelado': 'status-cancelado',
                'Em Andamento': 'status-em-andamento'
            };
            return classes[status] || 'status-pendente';
        }

        function getInitials(name) {
            if (!name) return 'N/A';
            return name.split(' ').map(n => n.charAt(0)).slice(0, 2).join('').toUpperCase();
        }

        function formatDate(dateStr) {
            if (!dateStr) return 'N/A';
            const [year, month, day] = dateStr.split('-');
            return `${day}/${month}/${year}`;
        }

        function formatDateISO(date) {
            return date.toISOString().split('T')[0];
        }

        function escapeHtml(text) {
            if (!text) return '';
            const div = document.createElement('div');
            div.textContent = text;
            return div.innerHTML;
        }
    </script>
</body>
</html>
