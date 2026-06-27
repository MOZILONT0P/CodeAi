<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Sea – AI Assistant</title>
  <!-- Puter AI SDK -->
  <script src="https://js.puter.com/v2/"></script>
  <link href="https://fonts.googleapis.com/css2?family=Inter:opsz,wght@14..32,300;400;500;600;700&display=swap" rel="stylesheet" />
  <style>
    /* === SAME STYLES AS BEFORE (kept compact) === */
    * { margin:0; padding:0; box-sizing:border-box; }
    :root {
      --bg-primary: #f0f4ff; --bg-secondary: #ffffff; --bg-sidebar: #e9edf5;
      --text-primary: #1a1a2e; --text-secondary: #4a4a6a; --text-muted: #8a8aa8;
      --border-light: #dce1ec; --accent: #4f6ef7; --accent-hover: #3a56d4;
      --accent-light: #eef1fe; --shadow-sm: 0 2px 8px rgba(0,0,0,0.06);
      --shadow-md: 0 8px 30px rgba(0,0,0,0.08); --shadow-lg: 0 16px 56px rgba(0,0,0,0.12);
      --radius: 16px; --radius-sm: 10px; --transition: 0.3s cubic-bezier(0.4,0,0.2,1);
      --font: 'Inter', sans-serif; --max-width: 900px; --sidebar-width: 280px;
    }
    html,body { height:100%; font-family:var(--font); background:var(--bg-primary); color:var(--text-primary); font-size:15px; line-height:1.6; -webkit-font-smoothing:antialiased; }
    ::-webkit-scrollbar { width:5px; }
    ::-webkit-scrollbar-thumb { background:#c5c9d6; border-radius:10px; }
    #app { display:flex; height:100vh; overflow:hidden; }
    .sidebar { width:var(--sidebar-width); background:var(--bg-sidebar); border-right:1px solid var(--border-light); display:flex; flex-direction:column; flex-shrink:0; transition:transform 0.4s cubic-bezier(0.4,0,0.2,1); z-index:10; backdrop-filter:blur(4px); background:rgba(233,237,245,0.9); }
    .sidebar-header { padding:24px 20px 16px; border-bottom:1px solid var(--border-light); display:flex; justify-content:space-between; align-items:center; }
    .sidebar-brand { font-size:26px; font-weight:700; letter-spacing:-0.5px; }
    .sidebar-brand span { color:var(--accent); }
    .sidebar-brand small { font-size:12px; font-weight:400; color:var(--text-muted); margin-left:6px; }
    .sidebar-close-btn { display:none; background:none; border:none; font-size:22px; color:var(--text-muted); cursor:pointer; padding:4px 8px; border-radius:6px; transition:var(--transition); }
    .sidebar-close-btn:hover { background:var(--border-light); }
    .sidebar-history { flex:1; overflow-y:auto; padding:16px 12px 20px; }
    .sidebar-history-title { font-size:12px; font-weight:600; text-transform:uppercase; letter-spacing:0.5px; color:var(--text-muted); padding:0 10px 10px; display:flex; justify-content:space-between; }
    .sidebar-history-title .clear-btn { background:none; border:none; color:var(--text-muted); font-size:12px; cursor:pointer; padding:2px 8px; border-radius:4px; transition:var(--transition); }
    .sidebar-history-title .clear-btn:hover { background:#fee2e2; color:#dc2626; }
    .history-item { display:flex; align-items:center; gap:10px; padding:10px 14px; border-radius:var(--radius-sm); cursor:pointer; transition:var(--transition); margin-bottom:3px; font-size:14px; color:var(--text-secondary); border:none; background:none; width:100%; text-align:left; animation:slideIn 0.3s ease; }
    .history-item:hover { background:var(--bg-primary); transform:translateX(4px); }
    .history-item.active { background:var(--accent-light); color:var(--accent); font-weight:500; box-shadow:inset 3px 0 0 var(--accent); }
    .history-item .icon { font-size:16px; opacity:0.6; }
    .history-item .text { flex:1; white-space:nowrap; overflow:hidden; text-overflow:ellipsis; }
    .history-item .delete-btn { background:none; border:none; color:var(--text-muted); cursor:pointer; font-size:14px; padding:2px 6px; border-radius:4px; opacity:0; transition:var(--transition); }
    .history-item:hover .delete-btn { opacity:1; }
    .history-item .delete-btn:hover { background:#fee2e2; color:#dc2626; }
    .history-empty { padding:30px 12px; color:var(--text-muted); text-align:center; }
    .sidebar-footer { padding:14px 20px; border-top:1px solid var(--border-light); font-size:12px; color:var(--text-muted); display:flex; justify-content:space-between; }
    .main { flex:1; display:flex; flex-direction:column; min-width:0; background:var(--bg-primary); background-image:radial-gradient(circle at 20% 30%, rgba(79,110,247,0.03) 0%, transparent 60%), radial-gradient(circle at 80% 70%, rgba(79,110,247,0.03) 0%, transparent 60%); animation:bgPulse 20s ease-in-out infinite alternate; }
    @keyframes bgPulse { 0% { background-position:0% 0%; } 100% { background-position:100% 100%; } }
    .main-topbar { padding:16px 28px; background:rgba(255,255,255,0.7); backdrop-filter:blur(8px); border-bottom:1px solid var(--border-light); display:flex; align-items:center; justify-content:space-between; flex-shrink:0; min-height:68px; }
    .main-topbar-left { display:flex; align-items:center; gap:16px; }
    .sidebar-toggle-btn { display:none; background:none; border:none; font-size:24px; color:var(--text-secondary); cursor:pointer; padding:4px 8px; border-radius:6px; transition:var(--transition); }
    .sidebar-toggle-btn:hover { background:var(--border-light); }
    .main-topbar-title { font-size:18px; font-weight:600; }
    .main-topbar-title .sub { font-weight:400; color:var(--text-muted); font-size:14px; }
    .main-topbar-right { display:flex; align-items:center; gap:12px; }
    .new-chat-btn { display:flex; align-items:center; gap:8px; padding:8px 18px; border-radius:var(--radius-sm); border:1px solid var(--border-light); background:var(--bg-secondary); color:var(--text-secondary); font-size:13px; font-weight:500; cursor:pointer; transition:var(--transition); font-family:var(--font); }
    .new-chat-btn:hover { background:var(--bg-primary); border-color:var(--accent); color:var(--accent); transform:translateY(-1px); box-shadow:var(--shadow-sm); }
    .chat-messages { flex:1; overflow-y:auto; padding:24px 28px 8px; display:flex; flex-direction:column; gap:6px; scroll-behavior:smooth; }
    .message { display:flex; gap:14px; padding:16px 20px; border-radius:var(--radius); max-width:85%; animation:messageSlide 0.35s cubic-bezier(0.4,0,0.2,1); margin-bottom:6px; }
    .message.user { align-self:flex-end; background:var(--accent); color:#fff; border-bottom-right-radius:4px; animation:messageSlideRight 0.35s cubic-bezier(0.4,0,0.2,1); }
    .message.assistant { align-self:flex-start; background:var(--bg-secondary); border:1px solid var(--border-light); border-bottom-left-radius:4px; box-shadow:var(--shadow-sm); animation:messageSlideLeft 0.35s cubic-bezier(0.4,0,0.2,1); }
    @keyframes messageSlide { from { opacity:0; transform:translateY(16px) scale(0.96); } to { opacity:1; transform:translateY(0) scale(1); } }
    @keyframes messageSlideRight { from { opacity:0; transform:translateX(20px); } to { opacity:1; transform:translateX(0); } }
    @keyframes messageSlideLeft { from { opacity:0; transform:translateX(-20px); } to { opacity:1; transform:translateX(0); } }
    @keyframes slideIn { from { opacity:0; transform:translateX(-10px); } to { opacity:1; transform:translateX(0); } }
    .message .avatar { width:32px; height:32px; border-radius:50%; flex-shrink:0; display:flex; align-items:center; justify-content:center; font-weight:600; font-size:14px; background:var(--bg-primary); color:var(--text-secondary); }
    .message.user .avatar { background:rgba(255,255,255,0.2); color:#fff; }
    .message .content { flex:1; min-width:0; word-wrap:break-word; overflow-wrap:break-word; font-size:14px; line-height:1.7; }
    .message .content pre { background:#1a1a2e; color:#e4e7ed; padding:16px 20px; border-radius:var(--radius-sm); overflow-x:auto; font-size:13px; line-height:1.6; margin:8px 0; font-family:'JetBrains Mono', monospace; max-width:100%; }
    .message .content code { font-family:'JetBrains Mono', monospace; font-size:13px; background:rgba(0,0,0,0.06); padding:2px 8px; border-radius:4px; }
    .message.user .content code { background:rgba(255,255,255,0.15); }
    .message .content ul, .message .content ol { padding-left:22px; margin:6px 0; }
    .message .content p { margin:4px 0; }
    .message .content blockquote { border-left:3px solid var(--accent); padding-left:14px; margin:8px 0; color:var(--text-secondary); }
    .typing-indicator { align-self:flex-start; background:var(--bg-secondary); border:1px solid var(--border-light); border-radius:var(--radius); border-bottom-left-radius:4px; padding:16px 24px; display:flex; gap:6px; align-items:center; box-shadow:var(--shadow-sm); animation:messageSlideLeft 0.3s ease; }
    .typing-indicator span { display:inline-block; width:10px; height:10px; border-radius:50%; background:var(--text-muted); animation:typingBounce 1.4s infinite both; }
    .typing-indicator span:nth-child(2) { animation-delay:0.2s; }
    .typing-indicator span:nth-child(3) { animation-delay:0.4s; }
    @keyframes typingBounce { 0%,80%,100% { transform:scale(0.6); opacity:0.4; } 40% { transform:scale(1); opacity:1; } }
    .welcome-state { flex:1; display:flex; flex-direction:column; align-items:center; justify-content:center; padding:40px 20px; text-align:center; animation:fadeIn 0.6s ease; }
    @keyframes fadeIn { from { opacity:0; transform:scale(0.96); } to { opacity:1; transform:scale(1); } }
    .welcome-state h1 { font-size:40px; font-weight:700; letter-spacing:-1px; color:var(--text-primary); margin-bottom:8px; }
    .welcome-state h1 span { color:var(--accent); }
    .welcome-state p { font-size:17px; color:var(--text-muted); max-width:480px; margin-bottom:28px; }
    .welcome-state .features { display:flex; gap:24px; flex-wrap:wrap; justify-content:center; margin-top:8px; }
    .welcome-state .feature { background:var(--bg-secondary); padding:18px 26px; border-radius:var(--radius); border:1px solid var(--border-light); box-shadow:var(--shadow-sm); font-size:14px; color:var(--text-secondary); min-width:140px; transition:var(--transition); animation:float 6s ease-in-out infinite; }
    .welcome-state .feature:nth-child(2) { animation-delay:1.5s; }
    .welcome-state .feature:nth-child(3) { animation-delay:3s; }
    .welcome-state .feature:nth-child(4) { animation-delay:4.5s; }
    @keyframes float { 0%,100% { transform:translateY(0px); } 50% { transform:translateY(-6px); } }
    .welcome-state .feature strong { color:var(--text-primary); display:block; font-size:16px; margin-bottom:4px; }
    .chat-input-area { padding:12px 28px 24px; background:var(--bg-primary); flex-shrink:0; border-top:1px solid transparent; }
    .chat-input-wrapper { display:flex; gap:12px; background:var(--bg-secondary); border-radius:var(--radius); border:1px solid var(--border-light); padding:6px 6px 6px 20px; box-shadow:var(--shadow-sm); transition:var(--transition); max-width:var(--max-width); margin:0 auto; }
    .chat-input-wrapper:focus-within { border-color:var(--accent); box-shadow:0 0 0 4px rgba(79,110,247,0.12), var(--shadow-sm); transform:scale(1.01); }
    .chat-input-wrapper textarea { flex:1; border:none; outline:none; resize:none; font-family:var(--font); font-size:14px; padding:10px 0; background:transparent; color:var(--text-primary); min-height:48px; max-height:160px; line-height:1.5; }
    .chat-input-wrapper textarea::placeholder { color:var(--text-muted); }
    .chat-input-wrapper .send-btn { padding:8px 24px; border-radius:var(--radius-sm); border:none; background:var(--accent); color:#fff; font-weight:600; font-size:14px; cursor:pointer; transition:var(--transition); font-family:var(--font); white-space:nowrap; height:46px; align-self:center; position:relative; overflow:hidden; }
    .chat-input-wrapper .send-btn:hover { background:var(--accent-hover); transform:translateY(-2px); box-shadow:var(--shadow-sm); }
    .chat-input-wrapper .send-btn:active { transform:scale(0.96); }
    .chat-input-wrapper .send-btn:disabled { opacity:0.5; cursor:not-allowed; transform:none; box-shadow:none; }
    .send-btn .ripple { position:absolute; border-radius:50%; background:rgba(255,255,255,0.3); transform:scale(0); animation:rippleAnim 0.6s linear; pointer-events:none; }
    @keyframes rippleAnim { to { transform:scale(4); opacity:0; } }
    .chat-input-footer { max-width:var(--max-width); margin:8px auto 0; font-size:12px; color:var(--text-muted); padding-left:4px; display:flex; justify-content:space-between; }
    .chat-input-footer .hint { display:flex; gap:16px; flex-wrap:wrap; }
    .chat-input-footer .hint span { opacity:0.7; }
    @media (max-width:768px) {
      .sidebar { position:fixed; top:0; left:0; bottom:0; transform:translateX(-100%); width:280px; box-shadow:var(--shadow-lg); border-right:none; border-radius:0 16px 16px 0; background:var(--bg-sidebar); }
      .sidebar.open { transform:translateX(0); }
      .sidebar-close-btn { display:block; }
      .sidebar-toggle-btn { display:block; }
      .main-topbar { padding:12px 16px; }
      .chat-messages { padding:16px 14px 4px; }
      .chat-input-area { padding:8px 14px 16px; }
      .message { max-width:92%; padding:14px 16px; }
      .welcome-state h1 { font-size:28px; }
      .welcome-state .features { flex-direction:column; gap:12px; }
      .welcome-state .feature { min-width:unset; padding:14px 18px; }
      .new-chat-btn span { display:none; }
      .chat-input-wrapper { padding:4px 4px 4px 14px; }
      .chat-input-wrapper textarea { font-size:13px; min-height:40px; }
      .chat-input-wrapper .send-btn { padding:6px 16px; font-size:13px; height:40px; }
    }
  </style>
</head>
<body>
  <div id="app">
    <!-- Sidebar -->
    <aside class="sidebar" id="sidebar">
      <div class="sidebar-header">
        <div class="sidebar-brand">Sea<span>.</span><small>v2</small></div>
        <button class="sidebar-close-btn" id="sidebarCloseBtn">✕</button>
      </div>
      <div class="sidebar-history" id="historyList">
        <div class="sidebar-history-title">
          History
          <button class="clear-btn" id="clearHistoryBtn">Clear all</button>
        </div>
        <div class="history-empty" id="historyEmpty">No conversations yet</div>
      </div>
      <div class="sidebar-footer"><span>⚡ Sea AI</span><span>Puter</span></div>
    </aside>

    <!-- Main -->
    <main class="main">
      <div class="main-topbar">
        <div class="main-topbar-left">
          <button class="sidebar-toggle-btn" id="sidebarToggleBtn">☰</button>
          <div class="main-topbar-title">Sea <span class="sub">· Puter</span></div>
        </div>
        <div class="main-topbar-right">
          <button class="new-chat-btn" id="newChatBtn"><span>+</span> <span>New</span></button>
        </div>
      </div>

      <div class="chat-messages" id="chatMessages">
        <div class="welcome-state" id="welcomeState">
          <h1>Sea <span>.</span></h1>
          <p>Powered by Puter – code, design, search, and more.</p>
          <div class="features">
            <div class="feature"><strong>💻 Code</strong> Build &amp; debug</div>
            <div class="feature"><strong>🎨 GUI</strong> Design interfaces</div>
            <div class="feature"><strong>🔍 Search</strong> Find answers</div>
            <div class="feature"><strong>🧠 Understand</strong> Natural language</div>
          </div>
        </div>
      </div>

      <div class="chat-input-area">
        <div class="chat-input-wrapper">
          <textarea id="chatInput" rows="1" placeholder="Ask me anything…"></textarea>
          <button class="send-btn" id="sendBtn">Send</button>
        </div>
        <div class="chat-input-footer">
          <div class="hint"><span>⌘ ↵ to send</span><span>Puter AI</span></div>
          <span id="charCount">0</span>
        </div>
      </div>
    </main>
  </div>

  <script>
    // ============================================================
    // SEA with Puter AI (real)
    // ============================================================
    (function() {
      'use strict';

      // -------------------- DOM refs --------------------
      const sidebar = document.getElementById('sidebar');
      const sidebarToggleBtn = document.getElementById('sidebarToggleBtn');
      const sidebarCloseBtn = document.getElementById('sidebarCloseBtn');
      const chatMessages = document.getElementById('chatMessages');
      const chatInput = document.getElementById('chatInput');
      const sendBtn = document.getElementById('sendBtn');
      const newChatBtn = document.getElementById('newChatBtn');
      const welcomeState = document.getElementById('welcomeState');
      const historyList = document.getElementById('historyList');
      const historyEmpty = document.getElementById('historyEmpty');
      const charCount = document.getElementById('charCount');
      const clearHistoryBtn = document.getElementById('clearHistoryBtn');

      // -------------------- State --------------------
      const state = {
        conversations: [],
        currentConversationId: null,
        isProcessing: false,
        restrictedTerms: [
          'illegal', 'hack', 'exploit', 'malware', 'virus', 'ransomware',
          'crack', 'keygen', 'torrent', 'pirate', 'piracy', 'drugs',
          'weapon', 'explosive', 'terrorism', 'abuse', 'harass',
          'doxx', 'swat', 'fraud', 'scam', 'phish'
        ]
      };

      // -------------------- Utility --------------------
      function generateId() {
        return Date.now().toString(36) + '-' + Math.random().toString(36).substr(2, 6);
      }
      function truncate(str, n = 40) {
        return str.length > n ? str.slice(0, n) + '…' : str;
      }
      function isRestricted(text) {
        const lower = text.toLowerCase();
        return state.restrictedTerms.some(term => lower.includes(term));
      }

      // -------------------- Storage --------------------
      function loadConversations() {
        try {
          const raw = localStorage.getItem('sea_conversations_puter');
          if (raw) state.conversations = JSON.parse(raw);
        } catch (_) { state.conversations = []; }
      }
      function saveConversations() {
        try {
          localStorage.setItem('sea_conversations_puter', JSON.stringify(state.conversations));
        } catch (_) {}
      }

      // -------------------- UI --------------------
      function updateUI() {
        renderHistory();
        const hasMessages = state.conversations.some(c => c.messages && c.messages.length > 0);
        if (hasMessages && state.currentConversationId) {
          const conv = state.conversations.find(c => c.id === state.currentConversationId);
          if (conv && conv.messages && conv.messages.length > 0) {
            welcomeState.style.display = 'none';
          } else {
            welcomeState.style.display = 'flex';
          }
        } else {
          welcomeState.style.display = 'flex';
        }
        sendBtn.disabled = state.isProcessing;
      }

      function renderHistory() {
        const title = historyList.querySelector('.sidebar-history-title');
        const empty = historyList.querySelector('.history-empty');
        historyList.innerHTML = '';
        if (title) historyList.appendChild(title);
        if (empty) historyList.appendChild(empty);

        const convs = state.conversations;
        if (convs.length === 0) {
          const emptyEl = document.createElement('div');
          emptyEl.className = 'history-empty';
          emptyEl.textContent = 'No conversations yet';
          historyList.appendChild(emptyEl);
          return;
        }
        const sorted = [...convs].sort((a, b) => (b.updatedAt || b.createdAt || 0) - (a.updatedAt || a.createdAt || 0));
        for (const conv of sorted) {
          const item = document.createElement('button');
          item.className = 'history-item';
          if (conv.id === state.currentConversationId) item.classList.add('active');
          const iconSpan = document.createElement('span');
          iconSpan.className = 'icon';
          iconSpan.textContent = '💬';
          item.appendChild(iconSpan);
          const textSpan = document.createElement('span');
          textSpan.className = 'text';
          textSpan.textContent = conv.title || 'Untitled';
          item.appendChild(textSpan);
          const delBtn = document.createElement('button');
          delBtn.className = 'delete-btn';
          delBtn.textContent = '✕';
          delBtn.addEventListener('click', (e) => {
            e.stopPropagation();
            deleteConversation(conv.id);
          });
          item.appendChild(delBtn);
          item.addEventListener('click', () => loadConversation(conv.id));
          historyList.appendChild(item);
        }
      }

      function createConversation(title) {
        const conv = {
          id: generateId(),
          title: title || 'New conversation',
          messages: [],
          createdAt: Date.now(),
          updatedAt: Date.now()
        };
        state.conversations.push(conv);
        state.currentConversationId = conv.id;
        saveConversations();
        renderHistory();
        renderMessages(conv);
        updateUI();
        return conv;
      }

      function loadConversation(id) {
        const conv = state.conversations.find(c => c.id === id);
        if (!conv) return;
        state.currentConversationId = id;
        renderMessages(conv);
        renderHistory();
        updateUI();
        setTimeout(() => chatMessages.scrollTop = chatMessages.scrollHeight, 50);
      }

      function deleteConversation(id) {
        if (!confirm('Delete this conversation?')) return;
        state.conversations = state.conversations.filter(c => c.id !== id);
        if (state.currentConversationId === id) {
          state.currentConversationId = null;
          welcomeState.style.display = 'flex';
          document.querySelectorAll('.message').forEach(el => el.remove());
        }
        saveConversations();
        renderHistory();
        updateUI();
      }

      function clearAllHistory() {
        if (!confirm('Delete all conversations?')) return;
        state.conversations = [];
        state.currentConversationId = null;
        saveConversations();
        document.querySelectorAll('.message').forEach(el => el.remove());
        welcomeState.style.display = 'flex';
        renderHistory();
        updateUI();
      }

      function getCurrentConversation() {
        if (!state.currentConversationId) return createConversation('New chat');
        const conv = state.conversations.find(c => c.id === state.currentConversationId);
        return conv || createConversation('New chat');
      }

      function renderMessages(conv) {
        document.querySelectorAll('.message').forEach(el => el.remove());
        welcomeState.style.display = 'none';
        if (!conv || !conv.messages || conv.messages.length === 0) {
          welcomeState.style.display = 'flex';
          return;
        }
        for (const msg of conv.messages) {
          appendMessageDOM(msg.role, msg.content, false);
        }
        setTimeout(() => chatMessages.scrollTop = chatMessages.scrollHeight, 50);
      }

      function appendMessageDOM(role, content, animate = true) {
        const wrapper = document.createElement('div');
        wrapper.className = `message ${role}`;
        const avatar = document.createElement('div');
        avatar.className = 'avatar';
        if (role === 'user') {
          avatar.textContent = 'U';
        } else {
          avatar.textContent = 'S';
          avatar.style.background = 'var(--accent-light)';
          avatar.style.color = 'var(--accent)';
        }
        wrapper.appendChild(avatar);
        const contentDiv = document.createElement('div');
        contentDiv.className = 'content';
        contentDiv.innerHTML = formatContent(content);
        wrapper.appendChild(contentDiv);
        chatMessages.appendChild(wrapper);
        welcomeState.style.display = 'none';
      }

      function formatContent(text) {
        let html = text;
        html = html.replace(/```(\w*)\n([\s\S]*?)```/g, (_, lang, code) =>
          `<pre><code>${escapeHtml(code.trim())}</code></pre>`
        );
        html = html.replace(/`([^`]+)`/g, (_, code) => `<code>${escapeHtml(code)}</code>`);
        html = html.replace(/\*\*([^*]+)\*\*/g, '<strong>$1</strong>');
        html = html.replace(/\*([^*]+)\*/g, '<em>$1</em>');
        html = html.replace(/\n/g, '<br>');
        html = html.replace(/(https?:\/\/[^\s]+)/g, '<a href="$1" target="_blank" rel="noopener">$1</a>');
        return html;
      }
      function escapeHtml(text) {
        const div = document.createElement('div');
        div.textContent = text;
        return div.innerHTML;
      }

      function showTyping() {
        const el = document.createElement('div');
        el.className = 'typing-indicator';
        el.id = 'typingIndicator';
        for (let i = 0; i < 3; i++) {
          const span = document.createElement('span');
          el.appendChild(span);
        }
        chatMessages.appendChild(el);
        chatMessages.scrollTop = chatMessages.scrollHeight;
      }
      function hideTyping() {
        const el = document.getElementById('typingIndicator');
        if (el) el.remove();
      }

      // -------------------- Puter AI call --------------------
      async function callPuter(prompt, history) {
        // Build a conversation array for Puter's chat
        const messages = [];
        // Add system instruction
        messages.push({
          role: 'system',
          content: 'You are Sea, a helpful AI assistant. You can write code, design GUIs, search for information, and explain concepts. Be concise but detailed when needed. Use markdown for code blocks.'
        });
        // Add history (last 6 messages)
        const recent = history.slice(-6);
        for (const msg of recent) {
          messages.push({
            role: msg.role === 'user' ? 'user' : 'assistant',
            content: msg.content
          });
        }
        // Add the current user message
        messages.push({ role: 'user', content: prompt });

        // Call Puter's chat API (available globally via script)
        if (typeof puter === 'undefined') {
          throw new Error('Puter SDK not loaded. Please check the script tag.');
        }
        const response = await puter.ai.chat(messages);
        return response.message.content;
      }

      // -------------------- Send message --------------------
      async function sendMessage() {
        const text = chatInput.value.trim();
        if (!text || state.isProcessing) return;

        if (isRestricted(text)) {
          const conv = getCurrentConversation();
          conv.messages.push({ role: 'user', content: text });
          conv.messages.push({
            role: 'assistant',
            content: "I'm sorry, but I can't respond to that request. It falls outside my safety guidelines."
          });
          conv.updatedAt = Date.now();
          if (!conv.title || conv.title === 'New conversation') conv.title = truncate(text, 30);
          saveConversations();
          renderMessages(conv);
          renderHistory();
          updateUI();
          chatInput.value = '';
          chatInput.style.height = 'auto';
          charCount.textContent = '0';
          return;
        }

        state.isProcessing = true;
        sendBtn.disabled = true;
        chatInput.disabled = true;

        const conv = getCurrentConversation();
        conv.messages.push({ role: 'user', content: text });
        conv.updatedAt = Date.now();
        if (!conv.title || conv.title === 'New conversation') conv.title = truncate(text, 30);
        saveConversations();

        appendMessageDOM('user', text, true);
        chatInput.value = '';
        chatInput.style.height = 'auto';
        charCount.textContent = '0';
        chatMessages.scrollTop = chatMessages.scrollHeight;

        showTyping();

        try {
          const response = await callPuter(text, conv.messages);
          hideTyping();
          conv.messages.push({ role: 'assistant', content: response });
          conv.updatedAt = Date.now();
          saveConversations();
          appendMessageDOM('assistant', response, true);
          chatMessages.scrollTop = chatMessages.scrollHeight;
        } catch (error) {
          hideTyping();
          const errorMsg = `❌ Error: ${error.message}. Please check your internet connection or try again.`;
          conv.messages.push({ role: 'assistant', content: errorMsg });
          conv.updatedAt = Date.now();
          saveConversations();
          appendMessageDOM('assistant', errorMsg, true);
          chatMessages.scrollTop = chatMessages.scrollHeight;
        }

        renderHistory();
        updateUI();
        state.isProcessing = false;
        sendBtn.disabled = false;
        chatInput.disabled = false;
        chatInput.focus();
      }

      // -------------------- New chat --------------------
      function startNewChat() {
        const conv = createConversation('New conversation');
        state.currentConversationId = conv.id;
        document.querySelectorAll('.message').forEach(el => el.remove());
        welcomeState.style.display = 'flex';
        renderHistory();
        updateUI();
        chatInput.focus();
      }

      // -------------------- Event listeners --------------------
      sendBtn.addEventListener('click', sendMessage);
      chatInput.addEventListener('keydown', (e) => {
        if (e.key === 'Enter' && !e.shiftKey) { e.preventDefault();
          sendMessage(); }
      });
      chatInput.addEventListener('input', () => {
        chatInput.style.height = 'auto';
        chatInput.style.height = Math.min(chatInput.scrollHeight, 160) + 'px';
        charCount.textContent = chatInput.value.length;
      });
      newChatBtn.addEventListener('click', startNewChat);
      sidebarToggleBtn.addEventListener('click', () => sidebar.classList.toggle('open'));
      sidebarCloseBtn.addEventListener('click', () => sidebar.classList.remove('open'));
      clearHistoryBtn.addEventListener('click', clearAllHistory);

      // Ripple
      sendBtn.addEventListener('mousedown', function(e) {
        const rect = this.getBoundingClientRect();
        const ripple = document.createElement('span');
        ripple.className = 'ripple';
        const size = Math.max(rect.width, rect.height);
        ripple.style.width = ripple.style.height = size + 'px';
        ripple.style.left = (e.clientX - rect.left - size / 2) + 'px';
        ripple.style.top = (e.clientY - rect.top - size / 2) + 'px';
        this.appendChild(ripple);
        setTimeout(() => ripple.remove(), 600);
      });

      // -------------------- Init --------------------
      function init() {
        loadConversations();
        if (state.conversations.length === 0) {
          createConversation('Welcome to Sea');
        } else {
          const sorted = [...state.conversations].sort((a, b) => (b.updatedAt || b.createdAt || 0) - (a.updatedAt || a
          .createdAt || 0));
          state.currentConversationId = sorted[0].id;
          renderMessages(sorted[0]);
        }
        updateUI();
        chatInput.focus();
        console.log('🌊 Sea with Puter initialized.');
      }

      window.__sea = { state, startNewChat, sendMessage, clearAllHistory };
      init();
    })();
  </script>
</body>
</html>
