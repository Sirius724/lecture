<!DOCTYPE html>
<html lang="ko" class="h-full bg-slate-950 text-slate-100">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>LiveClass Interact - 실시간 강의 소통 플랫폼</title>
  <!-- Tailwind CSS CDN -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Fonts: Inter and Fira Code for code blocks -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Fira+Code:wght@400;500;600;700&family=Inter:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">
  
  <script>
    tailwind.config = {
      darkMode: 'class',
      theme: {
        extend: {
          fontFamily: {
            sans: ['Inter', 'sans-serif'],
            mono: ['"Fira Code"', 'monospace'],
          },
          colors: {
            primary: {
              50: '#eef2ff',
              100: '#e0e7ff',
              400: '#818cf8',
              500: '#6366f1',
              600: '#4f46e5',
              700: '#4338ca',
            }
          }
        }
      }
    };
  </script>
  <style>
    /* Ensure code and commands are explicitly selectable for manual drag-copy */
    .selectable-text {
      -webkit-user-select: text !important;
      -moz-user-select: text !important;
      -ms-user-select: text !important;
      user-select: text !important;
    }
    
    /* Custom scrollbars */
    ::-webkit-scrollbar {
      width: 6px;
      height: 6px;
    }
    ::-webkit-scrollbar-track {
      background: rgba(15, 23, 42, 0.6);
    }
    ::-webkit-scrollbar-thumb {
      background: rgba(71, 85, 105, 0.6);
      border-radius: 9999px;
    }
    ::-webkit-scrollbar-thumb:hover {
      background: rgba(99, 102, 241, 0.8);
    }

    /* Floating Reaction Keyframe Animation */
    @keyframes floatUpFade {
      0% {
        transform: translateY(0) scale(0.8) rotate(0deg);
        opacity: 1;
      }
      50% {
        transform: translateY(-120px) scale(1.3) rotate(-10deg);
        opacity: 0.9;
      }
      100% {
        transform: translateY(-260px) scale(1.6) rotate(15deg);
        opacity: 0;
      }
    }
    .animate-float-reaction {
      animation: floatUpFade 2.2s cubic-bezier(0.22, 1, 0.36, 1) forwards;
      pointer-events: none;
    }

    /* Terminal blinking cursor */
    @keyframes blink {
      0%, 100% { opacity: 1; }
      50% { opacity: 0; }
    }
    .cursor-blink {
      animation: blink 1s infinite;
    }
  </style>
</head>
<body class="h-full flex flex-col overflow-hidden bg-slate-950 font-sans selection:bg-indigo-500 selection:text-white">

  <!-- Top Navigation Bar -->
  <header class="h-14 bg-slate-900/90 border-b border-slate-800/80 backdrop-blur px-4 flex items-center justify-between z-30 shrink-0">
    <div class="flex items-center gap-3">
      <!-- App Brand Logo -->
      <div class="flex items-center gap-2">
        <div class="w-8 h-8 rounded-xl bg-gradient-to-tr from-indigo-600 via-indigo-500 to-sky-400 flex items-center justify-center shadow-lg shadow-indigo-500/25">
          <svg class="w-4 h-4 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.2" d="M15 10l4.553-2.276A1 1 0 0121 8.618v6.764a1 1 0 01-1.447.894L15 14M5 18h8a2 2 0 002-2V8a2 2 0 00-2-2H5a2 2 0 00-2 2v8a2 2 0 002 2z"></path>
          </svg>
        </div>
        <div>
          <span class="font-extrabold text-sm md:text-base tracking-tight bg-gradient-to-r from-white via-slate-200 to-indigo-300 bg-clip-text text-transparent">LiveClass</span>
          <span class="text-xs font-semibold text-indigo-400 ml-0.5">Interact</span>
        </div>
      </div>

      <!-- Live Broadcast Badge -->
      <div class="hidden sm:flex items-center gap-1.5 px-2.5 py-0.5 rounded-full bg-rose-500/10 border border-rose-500/30 text-rose-400 text-xs font-semibold">
        <span class="w-2 h-2 rounded-full bg-rose-500 animate-pulse"></span>
        <span>LIVE</span>
      </div>

      <!-- Command History Counter (Click to open archive) -->
      <button id="openArchiveBtn" title="지금까지 전송된 커맨드 목록 확인" class="flex items-center gap-1.5 px-2.5 py-1 rounded-lg bg-slate-800/80 hover:bg-slate-700/80 border border-slate-700 text-slate-300 text-xs transition">
        <svg class="w-3.5 h-3.5 text-amber-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 012-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10"></path>
        </svg>
        <span>커맨드 보관함</span>
        <span id="archiveCountBadge" class="bg-indigo-600/80 text-white font-mono text-[10px] px-1.5 py-0.2 rounded-full">0</span>
      </button>
    </div>

    <!-- Right Header Controls: Profile, Mode switch, External tab -->
    <div class="flex items-center gap-2">
      <!-- User Profile Badge & Quick Rename -->
      <button id="profileEditBtn" class="flex items-center gap-2 px-2.5 py-1 rounded-lg bg-slate-800/70 hover:bg-slate-800 border border-slate-700 text-xs text-slate-300 transition" title="닉네임 변경">
        <div id="userAvatarDot" class="w-2.5 h-2.5 rounded-full bg-indigo-500 ring-2 ring-indigo-500/30"></div>
        <span id="userNicknameDisplay" class="font-medium max-w-[90px] md:max-w-[120px] truncate">수강생</span>
        <svg class="w-3 h-3 text-slate-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15.232 5.232l3.536 3.536m-2.036-5.036a2.5 2.5 0 113.536 3.536L6.5 21.036H3v-3.572L16.732 3.732z"></path>
        </svg>
      </button>

      <!-- Role Switcher (Host / Audience) -->
      <button id="roleToggleBtn" class="flex items-center gap-1.5 px-3 py-1 rounded-lg text-xs font-semibold bg-indigo-600 hover:bg-indigo-500 text-white shadow-sm shadow-indigo-600/30 transition">
        <span id="roleIcon">🎓</span>
        <span id="roleText">강사 모드</span>
      </button>

      <!-- Open in New Tab (Bypasses iframe camera & display sandbox limitations) -->
      <button id="openNewTabBtn" title="새 탭에서 열기 (카메라 권한 및 화면 공유 최적화)" class="p-1.5 rounded-lg bg-slate-800 hover:bg-slate-700 border border-slate-700 text-slate-300 transition">
        <svg class="w-4 h-4 text-sky-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 6H6a2 2 0 00-2 2v10a2 2 0 002 2h10a2 2 0 002-2v-4M14 4h6m0 0v6m0-6L10 14"></path>
        </svg>
      </button>
    </div>
  </header>

  <!-- Main Application Body -->
  <main class="flex-1 flex flex-col lg:flex-row overflow-hidden relative">
    
    <!-- LEFT COLUMN: Presentation Stage, Terminal Notice & Controls (Flex Grow) -->
    <section class="flex-1 flex flex-col min-w-0 bg-slate-950 overflow-y-auto">
      
      <!-- 1. Real-time Pinned Notice & Command Terminal Banner -->
      <div class="p-3 md:p-4 pb-2">
        <div class="rounded-2xl bg-slate-900 border border-slate-800 shadow-xl overflow-hidden relative group">
          <!-- Terminal Window Top Bar -->
          <div class="px-4 py-2.5 bg-slate-900/90 border-b border-slate-800/80 flex items-center justify-between">
            <div class="flex items-center gap-2">
              <!-- Window dots -->
              <div class="flex items-center gap-1.5">
                <span class="w-2.5 h-2.5 rounded-full bg-rose-500/80"></span>
                <span class="w-2.5 h-2.5 rounded-full bg-amber-500/80"></span>
                <span class="w-2.5 h-2.5 rounded-full bg-emerald-500/80"></span>
              </div>
              <span class="text-xs text-slate-400 font-mono ml-2 flex items-center gap-1.5">
                <span id="noticeTypeBadge" class="px-1.5 py-0.2 rounded bg-indigo-500/20 text-indigo-300 font-sans font-semibold text-[11px]">커맨드</span>
                <span class="text-slate-500 hidden sm:inline">•</span>
                <span id="noticeTimeDisplay" class="text-slate-500 text-[11px]">방금 전 업데이트</span>
              </span>
            </div>

            <!-- Action buttons: Copy & Lecturer Editor -->
            <div class="flex items-center gap-2">
              <!-- Lecturer Command Edit Button (Host Only) -->
              <button id="editNoticeBtn" class="hidden px-2.5 py-1 rounded-lg bg-indigo-600/30 hover:bg-indigo-600/50 text-indigo-300 text-xs font-medium border border-indigo-500/30 transition flex items-center gap-1">
                <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z"></path>
                </svg>
                <span>새 커맨드 배포</span>
              </button>

              <!-- Main Copy Button with Dual-Engine Copy -->
              <button id="copyCommandBtn" class="flex items-center gap-1.5 px-3 py-1.5 rounded-xl font-bold text-xs bg-gradient-to-r from-emerald-500 to-teal-500 hover:from-emerald-400 hover:to-teal-400 text-slate-950 shadow-md shadow-emerald-500/20 active:scale-95 transition transform">
                <svg id="copyCommandIcon" class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.2" d="M8 16H6a2 2 0 01-2-2V6a2 2 0 012-2h8a2 2 0 012 2v2m-6 12h8a2 2 0 002-2v-8a2 2 0 00-2-2h-8a2 2 0 00-2 2v8a2 2 0 002 2z"></path>
                </svg>
                <span id="copyCommandText">복사하기</span>
              </button>
            </div>
          </div>

          <!-- Command Monospace Display Area (Guaranteed selectable-text) -->
          <div class="p-3.5 md:p-4 bg-slate-950/80 font-mono text-xs md:text-sm text-emerald-300 selectable-text overflow-x-auto flex items-start gap-3">
            <span class="text-indigo-400 font-bold select-none shrink-0">$</span>
            <pre id="commandContent" class="selectable-text font-mono text-slate-200 whitespace-pre-wrap break-all leading-relaxed flex-1">git clone https://github.com/example/live-workshop.git</pre>
          </div>
        </div>
      </div>

      <!-- 2. Presentation Stage Screen Container -->
      <div class="px-3 md:px-4 flex-1 flex flex-col min-h-[320px] md:min-h-[420px]">
        <div id="screenContainer" class="flex-1 bg-slate-900 rounded-2xl border border-slate-800 shadow-2xl relative overflow-hidden flex items-center justify-center group">
          
          <!-- Screen Share Active Video Element -->
          <video id="screenVideo" autoplay playsinline class="w-full h-full object-contain hidden bg-black"></video>

          <!-- Standby / Idle Screen -->
          <div id="standbyScreen" class="flex flex-col items-center justify-center p-6 text-center max-w-md mx-auto">
            <div class="w-16 h-16 rounded-2xl bg-indigo-500/10 border border-indigo-500/20 text-indigo-400 flex items-center justify-center mb-4 shadow-inner">
              <svg class="w-8 h-8" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.8" d="M9.75 17L9 20l-1 1h8l-1-1-.75-3M3 13h18M5 17h14a2 2 0 002-2V5a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z"></path>
              </svg>
            </div>
            <h3 class="text-base md:text-lg font-bold text-white mb-1.5">실시간 강의 화면 대기 중</h3>
            <p class="text-xs text-slate-400 leading-relaxed mb-5">
              강사가 화면을 공유하면 이곳에 실시간 슬라이드 또는 코딩 화면이 표시됩니다.
            </p>

            <!-- Quick Start Button (Host Mode Only) -->
            <div id="hostQuickStartBox" class="flex flex-wrap items-center justify-center gap-2.5">
              <button id="quickScreenShareBtn" class="px-4 py-2 rounded-xl text-xs font-semibold bg-indigo-600 hover:bg-indigo-500 text-white shadow-lg shadow-indigo-600/30 transition flex items-center gap-2">
                <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12"></path>
                </svg>
                <span>화면 공유 시작하기</span>
              </button>
              <button id="quickVirtualCamBtn" class="px-3.5 py-2 rounded-xl text-xs font-semibold bg-purple-600/30 hover:bg-purple-600/50 text-purple-200 border border-purple-500/40 transition flex items-center gap-1.5">
                <span>🤖 가상 아바타 캠 켜기</span>
              </button>
            </div>
          </div>

          <!-- Floating Picture-in-Picture Presenter Camera -->
          <div id="pipCamBox" class="absolute bottom-4 right-4 w-40 sm:w-48 h-28 sm:h-32 rounded-xl overflow-hidden shadow-2xl border-2 border-indigo-500/60 bg-slate-950 hidden z-20 group/cam">
            <video id="camVideo" autoplay playsinline muted class="w-full h-full object-cover"></video>
            <!-- Camera Toolbar Overlay on hover -->
            <div class="absolute top-1.5 right-1.5 flex items-center gap-1 opacity-0 group-hover/cam:opacity-100 transition">
              <button id="switchCamModeBtn" title="실제 캠 / 가상 아바타 전환" class="w-6 h-6 rounded-lg bg-slate-900/80 hover:bg-indigo-600 text-white flex items-center justify-center text-[10px] transition">⇄</button>
              <button id="closeCamBtn" title="캠 닫기" class="w-6 h-6 rounded-lg bg-slate-900/80 hover:bg-rose-600 text-white flex items-center justify-center text-xs transition">✕</button>
            </div>
            <!-- Live PiP Label -->
            <div class="absolute bottom-1.5 left-2 text-[10px] font-bold text-white bg-slate-900/80 backdrop-blur px-2 py-0.5 rounded flex items-center gap-1.5">
              <span class="w-1.5 h-1.5 rounded-full bg-emerald-400 animate-pulse"></span>
              <span id="pipCamLabelText">강사 캠</span>
            </div>
          </div>

          <!-- Hidden Canvas for Generating Virtual Presenter Avatar Stream -->
          <canvas id="virtualCamCanvas" width="320" height="240" class="hidden"></canvas>

          <!-- Floating Emoji Container (Reaction Layer) -->
          <div id="reactionLayer" class="absolute inset-0 pointer-events-none overflow-hidden z-30"></div>
        </div>

        <!-- 3. Stage Action Bar: Screen/Camera controls & Audience Reactions -->
        <div class="py-3 flex flex-wrap items-center justify-between gap-3">
          <!-- Presenter Controls (Screen, Webcam, Fullscreen) -->
          <div class="flex items-center gap-2">
            <button id="bottomScreenShareBtn" class="px-3.5 py-1.5 rounded-xl bg-slate-800 hover:bg-slate-700 border border-slate-700 text-xs font-medium text-slate-200 transition flex items-center gap-1.5">
              <svg class="w-4 h-4 text-indigo-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9.75 17L9 20l-1 1h8l-1-1-.75-3M3 13h18M5 17h14a2 2 0 002-2V5a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z"></path>
              </svg>
              <span id="screenShareBtnLabel">화면 공유</span>
            </button>

            <button id="bottomCamBtn" class="px-3 py-1.5 rounded-xl bg-slate-800 hover:bg-slate-700 border border-slate-700 text-xs font-medium text-slate-200 transition flex items-center gap-1.5">
              <svg class="w-4 h-4 text-emerald-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 10l4.553-2.276A1 1 0 0121 8.618v6.764a1 1 0 01-1.447.894L15 14M5 18h8a2 2 0 002-2V8a2 2 0 00-2-2H5a2 2 0 00-2 2v8a2 2 0 002 2z"></path>
              </svg>
              <span>웹캠</span>
            </button>

            <button id="bottomVirtualCamBtn" class="px-3 py-1.5 rounded-xl bg-purple-950/40 hover:bg-purple-900/60 border border-purple-500/30 text-xs font-medium text-purple-200 transition flex items-center gap-1.5">
              <span>🤖 가상 아바타</span>
            </button>

            <button id="fullscreenBtn" title="전체화면 전환" class="p-1.5 rounded-xl bg-slate-800 hover:bg-slate-700 border border-slate-700 text-slate-300 transition">
              <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 8V4m0 0h4M4 4l5 5m11-1V4m0 0h-4m4 0l-5 5M4 16v4m0 0h4m-4 0l5-5m11 5l-5-5m5 5v-4m0 4h-4"></path>
              </svg>
            </button>
          </div>

          <!-- Audience Live Reaction Bar -->
          <div class="flex items-center gap-1.5 bg-slate-900/80 p-1 rounded-2xl border border-slate-800">
            <span class="text-[11px] font-medium text-slate-400 px-2 select-none hidden sm:inline">실시간 반응:</span>
            <button class="reaction-btn px-2.5 py-1 rounded-xl bg-slate-800/80 hover:bg-indigo-600/30 hover:scale-110 active:scale-95 transition text-base" data-emoji="👏" title="박수">👏</button>
            <button class="reaction-btn px-2.5 py-1 rounded-xl bg-slate-800/80 hover:bg-indigo-600/30 hover:scale-110 active:scale-95 transition text-base" data-emoji="🔥" title="최고">🔥</button>
            <button class="reaction-btn px-2.5 py-1 rounded-xl bg-slate-800/80 hover:bg-indigo-600/30 hover:scale-110 active:scale-95 transition text-base" data-emoji="💡" title="이해 완료">💡</button>
            <button class="reaction-btn px-2.5 py-1 rounded-xl bg-slate-800/80 hover:bg-indigo-600/30 hover:scale-110 active:scale-95 transition text-base" data-emoji="❓" title="질문 있어요">❓</button>
            <button class="reaction-btn px-2.5 py-1 rounded-xl bg-slate-800/80 hover:bg-indigo-600/30 hover:scale-110 active:scale-95 transition text-base" data-emoji="❤️" title="감사합니다">❤️</button>
          </div>
        </div>
      </div>
    </section>

    <!-- RIGHT COLUMN: Interactive Live Chat & Q&A Stream (~340px width on desktop) -->
    <aside class="w-full lg:w-84 xl:w-96 border-t lg:border-t-0 lg:border-l border-slate-800/80 bg-slate-900/95 flex flex-col h-72 lg:h-auto shrink-0 z-20">
      
      <!-- Chat Header & Filters -->
      <div class="p-3 border-b border-slate-800 flex items-center justify-between">
        <div class="flex items-center gap-1 bg-slate-950 p-1 rounded-xl border border-slate-800">
          <button id="chatTabAll" class="px-3 py-1 rounded-lg text-xs font-semibold bg-indigo-600 text-white transition">
            전체 채팅
          </button>
          <button id="chatTabQa" class="px-3 py-1 rounded-lg text-xs font-medium text-slate-400 hover:text-slate-200 transition flex items-center gap-1">
            <span>질문 모아보기</span>
            <span id="qaCountBadge" class="bg-amber-500/20 text-amber-300 text-[10px] px-1.5 rounded-full hidden">0</span>
          </button>
        </div>

        <!-- Chat Clear Button (Host Only) -->
        <button id="clearChatBtn" title="채팅 내역 지우기" class="hidden p-1.5 rounded-lg text-slate-400 hover:text-rose-400 hover:bg-slate-800 transition">
          <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"></path>
          </svg>
        </button>
      </div>

      <!-- Message Stream Scrollable List -->
      <div id="chatMessageList" class="flex-1 p-3 overflow-y-auto space-y-3">
        <!-- Welcome message -->
        <div class="p-2.5 rounded-xl bg-slate-800/40 border border-slate-800 text-center text-xs text-slate-400 leading-relaxed">
          👋 실시간 강의에 오신 것을 환영합니다!<br>질문이나 의견을 자유롭게 남겨보세요.
        </div>
      </div>

      <!-- Chat Input Area -->
      <div class="p-3 border-t border-slate-800 bg-slate-950/60">
        <!-- Question Checkbox Toggle -->
        <div class="flex items-center justify-between mb-2 px-1">
          <label class="flex items-center gap-1.5 cursor-pointer text-xs text-slate-300 select-none">
            <input type="checkbox" id="isQuestionCheck" class="w-3.5 h-3.5 rounded bg-slate-800 border-slate-700 text-indigo-600 focus:ring-0">
            <span class="text-amber-400 font-medium">❓ 질문으로 등록하기</span>
          </label>
          <span class="text-[10px] text-slate-500">Enter로 전송</span>
        </div>

        <!-- Text Input Form -->
        <form id="chatForm" class="flex items-center gap-2">
          <input 
            type="text" 
            id="chatInput" 
            placeholder="메시지를 입력하세요 (코드/커맨드 포함 가능)..." 
            class="flex-1 bg-slate-900 border border-slate-800 rounded-xl px-3 py-2 text-xs text-white placeholder-slate-500 focus:outline-none focus:border-indigo-500 transition"
            maxlength="300"
            autocomplete="off"
          />
          <button type="submit" class="px-3.5 py-2 rounded-xl bg-indigo-600 hover:bg-indigo-500 active:scale-95 text-white font-semibold text-xs transition flex items-center justify-center">
            <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 19l9 2-9-18-9 18 9-2zm0 0v-8"></path>
            </svg>
          </button>
        </form>
      </div>
    </aside>
  </main>

  <!-- MODAL 1: Broadcast Command / Notice Editor Modal (Lecturer Only) -->
  <div id="noticeModal" class="fixed inset-0 z-50 flex items-center justify-center p-4 bg-black/80 backdrop-blur-sm hidden">
    <div class="bg-slate-900 border border-slate-800 rounded-2xl w-full max-w-lg p-5 shadow-2xl relative overflow-hidden">
      <div class="flex items-center justify-between pb-3 border-b border-slate-800 mb-4">
        <h3 class="text-base font-bold text-white flex items-center gap-2">
          <span>📢 새 커맨드 / 지침 배포</span>
        </h3>
        <button id="closeNoticeModalBtn" class="text-slate-400 hover:text-white p-1 rounded-lg">✕</button>
      </div>

      <!-- Quick Preset Buttons -->
      <div class="mb-3">
        <label class="block text-xs font-semibold text-slate-400 mb-1.5">빠른 프리셋 선택</label>
        <div class="flex flex-wrap gap-1.5">
          <button class="preset-btn px-2.5 py-1 rounded-lg text-xs bg-slate-800 hover:bg-slate-700 text-slate-200 border border-slate-700" data-type="command" data-cmd="git clone https://github.com/example/live-workshop.git">git clone 예제</button>
          <button class="preset-btn px-2.5 py-1 rounded-lg text-xs bg-slate-800 hover:bg-slate-700 text-slate-200 border border-slate-700" data-type="command" data-cmd="npm install && npm run dev">npm run dev</button>
          <button class="preset-btn px-2.5 py-1 rounded-lg text-xs bg-slate-800 hover:bg-slate-700 text-slate-200 border border-slate-700" data-type="notice" data-cmd="⏱ 5분간 실습을 진행합니다. 완료되신 분은 👍 반응을 눌러주세요!">5분 실습 시작</button>
          <button class="preset-btn px-2.5 py-1 rounded-lg text-xs bg-slate-800 hover:bg-slate-700 text-slate-200 border border-slate-700" data-type="notice" data-cmd="💡 Q&A 세션입니다. 우측 채팅창의 [질문으로 등록]을 켜고 질문을 남겨주세요.">Q&A 세션 안내</button>
        </div>
      </div>

      <!-- Type Select -->
      <div class="mb-3">
        <label class="block text-xs font-semibold text-slate-400 mb-1">유형</label>
        <div class="grid grid-cols-2 gap-2">
          <button id="typeSelectCmd" class="py-2 rounded-xl text-xs font-semibold bg-indigo-600 text-white border border-indigo-500 transition">터미널 커맨드</button>
          <button id="typeSelectNotice" class="py-2 rounded-xl text-xs font-semibold bg-slate-800 text-slate-300 border border-slate-700 transition">실습 지침 / 공지</button>
        </div>
      </div>

      <!-- Command Textarea -->
      <div class="mb-4">
        <label class="block text-xs font-semibold text-slate-400 mb-1">배포 내용 (청중 화면에 즉시 표시 및 복사 지원)</label>
        <textarea 
          id="noticeContentInput" 
          rows="4" 
          class="w-full bg-slate-950 border border-slate-800 rounded-xl p-3 text-xs md:text-sm font-mono text-emerald-300 focus:outline-none focus:border-indigo-500 transition"
          placeholder="청중이 실행할 터미널 커맨드나 안내문을 입력하세요..."
        ></textarea>
      </div>

      <div class="flex items-center justify-end gap-2 pt-2 border-t border-slate-800">
        <button id="cancelNoticeModalBtn" class="px-4 py-2 text-xs font-medium text-slate-400 hover:text-white rounded-xl">취소</button>
        <button id="publishNoticeBtn" class="px-5 py-2 text-xs font-bold text-white bg-indigo-600 hover:bg-indigo-500 rounded-xl shadow-lg shadow-indigo-600/30 transition">실시간 배포하기</button>
      </div>
    </div>
  </div>

  <!-- MODAL 2: Command History Archive Modal ('커맨드 보관함') -->
  <div id="archiveModal" class="fixed inset-0 z-50 flex items-center justify-center p-4 bg-black/80 backdrop-blur-sm hidden">
    <div class="bg-slate-900 border border-slate-800 rounded-2xl w-full max-w-lg p-5 shadow-2xl relative flex flex-col max-h-[80vh]">
      <div class="flex items-center justify-between pb-3 border-b border-slate-800 mb-3">
        <h3 class="text-base font-bold text-white flex items-center gap-2">
          <span>📜 강의 커맨드 보관함</span>
        </h3>
        <button id="closeArchiveModalBtn" class="text-slate-400 hover:text-white p-1 rounded-lg">✕</button>
      </div>
      <p class="text-xs text-slate-400 mb-3">강의 중 배포된 모든 커맨드입니다. 놓친 커맨드를 언제든 다시 복사할 수 있습니다.</p>
      
      <div id="archiveList" class="flex-1 overflow-y-auto space-y-2.5 pr-1">
        <!-- Rendered dynamically -->
      </div>
    </div>
  </div>

  <!-- MODAL 3: Camera Permission Guide & Fallback Diagnostics -->
  <div id="cameraHelpModal" class="fixed inset-0 z-50 flex items-center justify-center p-4 bg-black/85 backdrop-blur-sm hidden">
    <div class="bg-slate-900 border border-slate-800 rounded-2xl w-full max-w-md p-6 shadow-2xl relative">
      <div class="flex items-start justify-between gap-3 mb-4">
        <div class="flex items-center gap-2.5">
          <div class="w-10 h-10 rounded-xl bg-amber-500/20 text-amber-400 border border-amber-500/30 flex items-center justify-center font-bold text-lg">
            📷
          </div>
          <div>
            <h3 class="text-base font-bold text-white">카메라 권한 및 장치 안내</h3>
            <p id="camErrorBadge" class="text-[11px] text-amber-300 font-mono mt-0.5">상태: 카메라 권한 확인 필요</p>
          </div>
        </div>
        <button id="closeCamHelpBtn" class="text-slate-400 hover:text-white p-1 rounded-lg">✕</button>
      </div>

      <div class="space-y-3 mb-5 text-xs text-slate-300">
        <!-- Solution 1: Instant Virtual Avatar -->
        <div class="p-3 rounded-xl bg-indigo-950/40 border border-indigo-500/40">
          <span class="font-bold text-indigo-300 block mb-1">🤖 [즉시 해결] 가상 강사 아바타 모드</span>
          <p class="text-slate-400 leading-relaxed mb-2.5">
            카메라 장비가 없거나 브라우저 권한이 거부된 환경에서도 실시간 말하는 강사 아바타 캠을 바로 실행합니다.
          </p>
          <button id="launchVirtualCamFromModalBtn" class="w-full py-2 rounded-lg font-bold text-xs bg-indigo-600 hover:bg-indigo-500 text-white shadow transition">
            가상 아바타 캠으로 즉시 시작하기
          </button>
        </div>

        <!-- Solution 2: Standalone Tab -->
        <div class="p-3 rounded-xl bg-slate-950 border border-slate-800">
          <span class="font-semibold text-slate-200 block mb-1">🪟 새 탭에서 열어 실제 웹캠 허용하기</span>
          <p class="text-slate-400 leading-relaxed mb-2">
            미리보기 창(iFrame) 보안상 웹캠 접근이 막힐 수 있습니다. 새 창에서 열면 정상 권한 승인 팝업이 나타납니다.
          </p>
          <button id="openNewTabFromModalBtn" class="w-full py-1.5 rounded-lg font-medium text-xs bg-slate-800 hover:bg-slate-700 text-slate-200 border border-slate-700 transition">
            새 창에서 실행하기
          </button>
        </div>
      </div>

      <div class="flex items-center justify-between pt-2 border-t border-slate-800">
        <button id="retryCameraBtn" class="px-3 py-1.5 text-xs font-semibold bg-slate-800 hover:bg-slate-700 text-slate-200 rounded-xl transition">실제 카메라 재시도</button>
        <button id="dismissCamHelpBtn" class="px-4 py-1.5 text-xs font-medium text-slate-400 hover:text-white rounded-xl">닫기</button>
      </div>
    </div>
  </div>

  <!-- MODAL 4: User Nickname / Profile Edit Modal -->
  <div id="profileModal" class="fixed inset-0 z-50 flex items-center justify-center p-4 bg-black/80 backdrop-blur-sm hidden">
    <div class="bg-slate-900 border border-slate-800 rounded-2xl w-full max-w-sm p-5 shadow-2xl relative">
      <h3 class="text-base font-bold text-white mb-3">내 프로필 설정</h3>
      <div class="space-y-3 mb-4">
        <div>
          <label class="block text-xs font-semibold text-slate-400 mb-1">닉네임</label>
          <input type="text" id="nicknameInput" class="w-full bg-slate-950 border border-slate-800 rounded-xl px-3 py-2 text-xs text-white focus:outline-none focus:border-indigo-500" maxlength="15">
        </div>
        <div>
          <label class="block text-xs font-semibold text-slate-400 mb-1">아바타 색상</label>
          <div class="flex items-center gap-2">
            <button class="color-picker-btn w-6 h-6 rounded-full bg-indigo-500 ring-2 ring-offset-2 ring-offset-slate-900 ring-indigo-500" data-color="#6366f1"></button>
            <button class="color-picker-btn w-6 h-6 rounded-full bg-emerald-500" data-color="#10b981"></button>
            <button class="color-picker-btn w-6 h-6 rounded-full bg-amber-500" data-color="#f59e0b"></button>
            <button class="color-picker-btn w-6 h-6 rounded-full bg-rose-500" data-color="#f43f5e"></button>
            <button class="color-picker-btn w-6 h-6 rounded-full bg-sky-500" data-color="#0ea5e9"></button>
            <button class="color-picker-btn w-6 h-6 rounded-full bg-purple-500" data-color="#a855f7"></button>
          </div>
        </div>
      </div>
      <div class="flex justify-end gap-2 pt-2 border-t border-slate-800">
        <button id="closeProfileModalBtn" class="px-3.5 py-1.5 text-xs text-slate-400 hover:text-white rounded-lg">취소</button>
        <button id="saveProfileBtn" class="px-4 py-1.5 text-xs font-bold bg-indigo-600 hover:bg-indigo-500 text-white rounded-lg transition">저장</button>
      </div>
    </div>
  </div>

  <!-- Toast Notification Stack -->
  <div id="toastContainer" class="fixed top-16 right-4 z-50 flex flex-col gap-2 pointer-events-none"></div>

  <script>
    /* LiveClass Interact Client Core Logic */
    
    // Application runtime state
    const state = {
      appId: typeof __app_id !== 'undefined' ? __app_id : 'liveclass-interact-app',
      user: {
        id: 'user_' + Math.random().toString(36).substring(2, 9),
        nickname: '수강생_' + Math.floor(100 + Math.random() * 900),
        role: 'host', // 'host' (강사) or 'audience' (청중)
        color: '#6366f1'
      },
      currentTab: 'all', // 'all' or 'qa'
      messages: [],
      commandHistory: [],
      activeNotice: {
        type: 'command',
        content: 'git clone https://github.com/example/live-workshop.git',
        time: Date.now(),
        author: '강사'
      },
      screenStream: null,
      camStream: null,
      isVirtualCam: false,
      virtualCamAnimId: null,
      selectedNoticeType: 'command'
    };

    // Load persisted user profile if available
    try {
      const savedUser = localStorage.getItem('liveclass_user_profile');
      if (savedUser) {
        const parsed = JSON.parse(savedUser);
        if (parsed.nickname) state.user.nickname = parsed.nickname;
        if (parsed.color) state.user.color = parsed.color;
      }
    } catch (e) {
      console.warn('LocalStorage unavailable:', e);
    }

    // Initialize local cross-tab BroadcastChannel for real-time local testing
    let broadcastChannel = null;
    try {
      broadcastChannel = new BroadcastChannel('liveclass_channel_' + state.appId);
      broadcastChannel.onmessage = (event) => {
        handleIncomingSync(event.data);
      };
    } catch (e) {
      console.warn('BroadcastChannel not supported:', e);
    }

    /**
     * DUAL-ENGINE CLIPBOARD COPY:
     * 1. Attempts modern navigator.clipboard.writeText.
     * 2. Fallbacks immediately to hidden textarea document.execCommand('copy').
     * Solves iframe and permission restrictions reliably.
     */
    function copyTextToClipboard(text, btnElement = null, successMsg = '복사되었습니다!') {
      if (!text) return;

      const performFallback = () => {
        try {
          const textArea = document.createElement("textarea");
          textArea.value = text;
          textArea.style.position = "fixed";
          textArea.style.top = "-9999px";
          textArea.style.left = "-9999px";
          textArea.setAttribute("readonly", "");
          document.body.appendChild(textArea);
          textArea.focus();
          textArea.select();
          const successful = document.execCommand('copy');
          document.body.removeChild(textArea);
          if (successful) {
            triggerCopySuccess(btnElement, successMsg);
          } else {
            showToast('복사에 실패했습니다. 수동으로 드래그 복사해주세요.', 'error');
          }
        } catch (err) {
          console.error('Fallback copy error:', err);
          showToast('복사 중 오류가 발생했습니다. 직접 드래그하여 복사하세요.', 'error');
        }
      };

      if (navigator.clipboard && window.isSecureContext) {
        navigator.clipboard.writeText(text)
          .then(() => {
            triggerCopySuccess(btnElement, successMsg);
          })
          .catch(() => {
            performFallback();
          });
      } else {
        performFallback();
      }
    }

    function triggerCopySuccess(btnElement, successMsg) {
      showToast(successMsg, 'success');
      
      if (btnElement) {
        const textSpan = btnElement.querySelector('span');
        const iconSvg = btnElement.querySelector('svg');
        const origText = textSpan ? textSpan.textContent : '';

        if (textSpan) textSpan.textContent = '복사 완료! ✔';
        btnElement.classList.add('ring-2', 'ring-emerald-400');

        setTimeout(() => {
          if (textSpan) textSpan.textContent = origText;
          btnElement.classList.remove('ring-2', 'ring-emerald-400');
        }, 1800);
      }
    }

    /**
     * Virtual Presenter Avatar Camera:
     * Creates an animated real-time video stream via HTML5 Canvas captureStream().
     * Zero hardware or browser permission requirements.
     */
    function startVirtualCam() {
      stopCameraStream();

      const canvas = document.getElementById('virtualCamCanvas');
      const ctx = canvas.getContext('2d');
      const pip = document.getElementById('pipCamBox');
      const camVideo = document.getElementById('camVideo');
      const labelText = document.getElementById('pipCamLabelText');

      state.isVirtualCam = true;
      let frame = 0;

      function renderAvatar() {
        frame++;
        const time = frame * 0.05;
        const w = canvas.width;
        const h = canvas.height;

        // Background studio gradient
        const bgGrad = ctx.createLinearGradient(0, 0, w, h);
        bgGrad.addColorStop(0, '#0f172a');
        bgGrad.addColorStop(1, '#1e1b4b');
        ctx.fillStyle = bgGrad;
        ctx.fillRect(0, 0, w, h);

        // Tech grid lines
        ctx.strokeStyle = 'rgba(99, 102, 241, 0.15)';
        ctx.lineWidth = 1;
        for (let x = 0; x < w; x += 32) {
          ctx.beginPath();
          ctx.moveTo(x, 0);
          ctx.lineTo(x, h);
          ctx.stroke();
        }
        for (let y = 0; y < h; y += 32) {
          ctx.beginPath();
          ctx.moveTo(0, y);
          ctx.lineTo(w, y);
          ctx.stroke();
        }

        // Animated speech particles
        for (let i = 0; i < 4; i++) {
          const px = (w * 0.2 + i * 50 + Math.sin(time + i) * 20) % w;
          const py = (h * 0.8 - ((frame * (1 + i * 0.2)) % h));
          ctx.fillStyle = 'rgba(129, 140, 248, 0.25)';
          ctx.beginPath();
          ctx.arc(px, py, 2.5, 0, Math.PI * 2);
          ctx.fill();
        }

        // Head bobbing logic (breathing / nodding)
        const headBob = Math.sin(time * 1.5) * 3;
        const cx = w / 2;
        const cy = h / 2 - 10 + headBob;

        // Torso / Clothes
        ctx.fillStyle = '#312e81';
        ctx.beginPath();
        ctx.ellipse(cx, h + 15, 75, 45, 0, 0, Math.PI * 2);
        ctx.fill();

        // Collar
        ctx.fillStyle = '#e2e8f0';
        ctx.beginPath();
        ctx.moveTo(cx - 24, h - 30);
        ctx.lineTo(cx, h - 5);
        ctx.lineTo(cx + 24, h - 30);
        ctx.fill();

        // Neck
        ctx.fillStyle = '#fbcfe8';
        ctx.fillRect(cx - 14, cy + 28, 28, 24);

        // Face
        ctx.fillStyle = '#fce7f3';
        ctx.beginPath();
        ctx.arc(cx, cy, 38, 0, Math.PI * 2);
        ctx.fill();

        // Hair
        ctx.fillStyle = '#1e1b4b';
        ctx.beginPath();
        ctx.arc(cx, cy - 8, 40, Math.PI * 0.9, Math.PI * 2.1);
        ctx.fill();

        // Smart Glasses frame
        ctx.strokeStyle = '#38bdf8';
        ctx.lineWidth = 2.5;
        ctx.strokeRect(cx - 28, cy - 8, 22, 14);
        ctx.strokeRect(cx + 6, cy - 8, 22, 14);
        ctx.beginPath();
        ctx.moveTo(cx - 6, cy - 1);
        ctx.lineTo(cx + 6, cy - 1);
        ctx.stroke();

        // Eyes (blinking)
        const isBlinking = (frame % 75) < 5;
        ctx.fillStyle = '#0f172a';
        if (isBlinking) {
          ctx.fillRect(cx - 20, cy - 2, 8, 2);
          ctx.fillRect(cx + 12, cy - 2, 8, 2);
        } else {
          ctx.beginPath();
          ctx.arc(cx - 16, cy - 1, 3.5, 0, Math.PI * 2);
          ctx.arc(cx + 16, cy - 1, 3.5, 0, Math.PI * 2);
          ctx.fill();
        }

        // Talking mouth
        const mouthOpen = Math.abs(Math.sin(time * 3)) * 4;
        ctx.fillStyle = '#e11d48';
        ctx.beginPath();
        ctx.ellipse(cx, cy + 22, 7, 2 + mouthOpen, 0, 0, Math.PI * 2);
        ctx.fill();

        // Corner Audio Equalizer Simulation
        ctx.fillStyle = '#38bdf8';
        for (let b = 0; b < 4; b++) {
          const barH = 4 + Math.abs(Math.sin(time * 4 + b)) * 12;
          ctx.fillRect(16 + b * 5, h - 16 - barH, 3, barH);
        }

        // Virtual Presenter Label
        ctx.fillStyle = 'rgba(15, 23, 42, 0.7)';
        ctx.fillRect(8, 8, 126, 18);
        ctx.fillStyle = '#a855f7';
        ctx.beginPath();
        ctx.arc(17, 17, 3.5, 0, Math.PI * 2);
        ctx.fill();
        ctx.fillStyle = '#f8fafc';
        ctx.font = 'bold 9px monospace';
        ctx.fillText('VIRTUAL PRESENTER', 26, 20);

        state.virtualCamAnimId = requestAnimationFrame(renderAvatar);
      }

      renderAvatar();

      try {
        const stream = canvas.captureStream(30);
        state.camStream = stream;
        camVideo.srcObject = stream;
        pip.classList.remove('hidden');
        if (labelText) labelText.textContent = '가상 아바타';
        showToast('가상 강사 아바타 캠이 켜졌습니다!', 'success');
      } catch (err) {
        console.error('Virtual cam capture failed:', err);
        showToast('가상 캠 생성에 실패했습니다.', 'error');
      }
    }

    function stopCameraStream() {
      if (state.virtualCamAnimId) {
        cancelAnimationFrame(state.virtualCamAnimId);
        state.virtualCamAnimId = null;
      }
      if (state.camStream) {
        state.camStream.getTracks().forEach(track => track.stop());
        state.camStream = null;
      }
      state.isVirtualCam = false;
    }

    async function toggleWebcam(forceVirtual = false) {
      const pip = document.getElementById('pipCamBox');
      const camVideo = document.getElementById('camVideo');
      const labelText = document.getElementById('pipCamLabelText');

      if (state.camStream) {
        stopCameraStream();
        camVideo.srcObject = null;
        pip.classList.add('hidden');
        showToast('강사 캠이 꺼졌습니다.');
        return;
      }

      if (forceVirtual) {
        startVirtualCam();
        return;
      }

      // Check if getUserMedia is permitted
      if (!navigator.mediaDevices || !navigator.mediaDevices.getUserMedia) {
        showCameraHelpModal('브라우저에서 getUserMedia API를 지원하지 않거나 iframe 보안에 의해 차단되었습니다.');
        return;
      }

      try {
        const stream = await navigator.mediaDevices.getUserMedia({
          video: { width: { ideal: 640 }, height: { ideal: 480 } },
          audio: false
        });

        stopCameraStream();
        state.camStream = stream;
        camVideo.srcObject = stream;
        pip.classList.remove('hidden');
        if (labelText) labelText.textContent = '실제 웹캠';
        showToast('강사 웹캠 PiP가 켜졌습니다.', 'success');

      } catch (err) {
        console.warn('Webcam permission or device error:', err);
        let errorDesc = '카메라 권한이 거부되었거나 장치를 찾을 수 없습니다.';
        
        if (err.name === 'NotAllowedError' || err.name === 'PermissionDeniedError') {
          errorDesc = '브라우저 권한 거부 또는 iframe 보안 차단 (NotAllowedError)';
        } else if (err.name === 'NotFoundError' || err.name === 'DevicesNotFoundError') {
          errorDesc = '연결된 웹캠 하드웨어를 찾을 수 없음 (NotFoundError)';
        } else if (err.name === 'NotReadableError') {
          errorDesc = '카메라가 다른 프로그램(Zoom, Meet 등)에서 이미 사용 중입니다.';
        }

        showCameraHelpModal(errorDesc);
      }
    }

    function showCameraHelpModal(reason) {
      const modal = document.getElementById('cameraHelpModal');
      const badge = document.getElementById('camErrorBadge');
      if (badge && reason) badge.textContent = `원인: ${reason}`;
      if (modal) modal.classList.remove('hidden');
    }

    async function toggleScreenShare() {
      const video = document.getElementById('screenVideo');
      const standby = document.getElementById('standbyScreen');
      const btnLabel = document.getElementById('screenShareBtnLabel');

      if (state.screenStream) {
        state.screenStream.getTracks().forEach(track => track.stop());
        state.screenStream = null;
        video.srcObject = null;
        video.classList.add('hidden');
        standby.classList.remove('hidden');
        if (btnLabel) btnLabel.textContent = '화면 공유';
        showToast('화면 공유가 종료되었습니다.');
        return;
      }

      try {
        const stream = await navigator.mediaDevices.getDisplayMedia({
          video: { cursor: "always" },
          audio: true
        });

        state.screenStream = stream;
        video.srcObject = stream;
        video.classList.remove('hidden');
        standby.classList.add('hidden');
        if (btnLabel) btnLabel.textContent = '공유 중지';
        showToast('강사 화면 공유가 시작되었습니다!', 'success');

        stream.getVideoTracks()[0].onended = () => {
          state.screenStream = null;
          video.srcObject = null;
          video.classList.add('hidden');
          standby.classList.remove('hidden');
          if (btnLabel) btnLabel.textContent = '화면 공유';
          showToast('화면 공유가 중단되었습니다.');
        };

      } catch (err) {
        console.warn('Screen share cancelled/failed:', err);
        showToast('화면 공유 권한이 거부되었거나 취소되었습니다.', 'error');
      }
    }

    function triggerReaction(emoji) {
      const layer = document.getElementById('reactionLayer');
      if (!layer) return;

      const el = document.createElement('div');
      el.className = 'absolute text-3xl select-none animate-float-reaction z-30';
      el.textContent = emoji;

      // Random horizontal position (15% to 85%)
      const randomLeft = 15 + Math.random() * 70;
      el.style.left = `${randomLeft}%`;
      el.style.bottom = '15px';

      layer.appendChild(el);

      // Broadcast reaction to other tabs
      broadcastSync({
        type: 'REACTION',
        emoji: emoji
      });

      setTimeout(() => {
        if (el.parentNode) el.parentNode.removeChild(el);
      }, 2300);
    }

    function renderMessages() {
      const list = document.getElementById('chatMessageList');
      if (!list) return;

      const filtered = state.messages.filter(msg => {
        if (state.currentTab === 'qa') return msg.isQuestion;
        return true;
      });

      if (filtered.length === 0) {
        list.innerHTML = `
          <div class="p-6 text-center text-xs text-slate-500">
            ${state.currentTab === 'qa' ? '등록된 질문이 없습니다.' : '아직 채팅 메시지가 없습니다.'}
          </div>
        `;
        return;
      }

      list.innerHTML = filtered.map(msg => {
        const isHost = msg.role === 'host';
        const formattedTime = new Date(msg.timestamp).toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });

        return `
          <div class="group/msg p-2.5 rounded-xl ${isHost ? 'bg-indigo-950/30 border border-indigo-500/30' : 'bg-slate-900/90 border border-slate-800'} transition">
            <div class="flex items-center justify-between gap-2 mb-1">
              <div class="flex items-center gap-1.5">
                <span class="w-2 h-2 rounded-full" style="background-color: ${msg.color || '#6366f1'}"></span>
                <span class="text-xs font-bold ${isHost ? 'text-indigo-300' : 'text-slate-300'}">${escapeHtml(msg.nickname)}</span>
                ${isHost ? '<span class="text-[10px] font-semibold px-1.5 py-0.2 rounded bg-indigo-500/30 text-indigo-300 border border-indigo-500/40">강사</span>' : ''}
                ${msg.isQuestion ? '<span class="text-[10px] font-bold px-1.5 py-0.2 rounded bg-amber-500/20 text-amber-300 border border-amber-500/40">Q&A 질문</span>' : ''}
              </div>
              <div class="flex items-center gap-1.5">
                <span class="text-[10px] text-slate-500 font-mono">${formattedTime}</span>
                <button class="copy-chat-btn opacity-0 group-hover/msg:opacity-100 p-1 rounded hover:bg-slate-800 text-slate-400 hover:text-white transition" data-text="${escapeHtml(msg.content)}" title="내용 복사">
                  <svg class="w-3 h-3" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 16H6a2 2 0 01-2-2V6a2 2 0 012-2h8a2 2 0 012 2v2m-6 12h8a2 2 0 002-2v-8a2 2 0 00-2-2h-8a2 2 0 00-2 2v8a2 2 0 002 2z"></path>
                  </svg>
                </button>
              </div>
            </div>
            <div class="text-xs text-slate-200 selectable-text break-words leading-relaxed pl-3.5 border-l-2 ${msg.isQuestion ? 'border-amber-400/50' : (isHost ? 'border-indigo-400/50' : 'border-slate-700/60')}">
              ${escapeHtml(msg.content)}
            </div>
          </div>
        `;
      }).join('');

      list.scrollTop = list.scrollHeight;

      // Update Q&A badge count
      const qaCount = state.messages.filter(m => m.isQuestion).length;
      const badge = document.getElementById('qaCountBadge');
      if (badge) {
        badge.textContent = qaCount;
        badge.classList.toggle('hidden', qaCount === 0);
      }
    }

    function updateNoticeDisplay() {
      const contentEl = document.getElementById('commandContent');
      const badgeEl = document.getElementById('noticeTypeBadge');
      const timeEl = document.getElementById('noticeTimeDisplay');

      if (contentEl) contentEl.textContent = state.activeNotice.content;
      if (badgeEl) {
        badgeEl.textContent = state.activeNotice.type === 'command' ? '터미널 커맨드' : '실습 지침';
        badgeEl.className = state.activeNotice.type === 'command' 
          ? 'px-1.5 py-0.2 rounded bg-indigo-500/20 text-indigo-300 font-sans font-semibold text-[11px]'
          : 'px-1.5 py-0.2 rounded bg-amber-500/20 text-amber-300 font-sans font-semibold text-[11px]';
      }
      if (timeEl) {
        timeEl.textContent = new Date(state.activeNotice.time).toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' }) + ' 배포';
      }

      // Update archive count badge
      const countBadge = document.getElementById('archiveCountBadge');
      if (countBadge) countBadge.textContent = state.commandHistory.length;
    }

    function publishNotice(content, type = 'command') {
      if (!content || !content.trim()) return;

      const newNotice = {
        type: type,
        content: content.trim(),
        time: Date.now(),
        author: state.user.nickname
      };

      state.activeNotice = newNotice;
      state.commandHistory.unshift(newNotice);
      updateNoticeDisplay();
      showToast('새 커맨드가 청중에게 실시간 배포되었습니다!', 'success');

      // Sync across tabs
      broadcastSync({
        type: 'NOTICE_UPDATE',
        notice: newNotice,
        history: state.commandHistory
      });
    }

    function renderArchiveModal() {
      const container = document.getElementById('archiveList');
      if (!container) return;

      if (state.commandHistory.length === 0) {
        container.innerHTML = `
          <div class="p-8 text-center text-xs text-slate-500">
            아직 배포된 커맨드가 없습니다.
          </div>
        `;
        return;
      }

      container.innerHTML = state.commandHistory.map((item, idx) => {
        const timeStr = new Date(item.time).toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });
        return `
          <div class="p-3 rounded-xl bg-slate-950 border border-slate-800 flex flex-col gap-2">
            <div class="flex items-center justify-between text-[11px] text-slate-400 font-mono">
              <span class="font-bold ${item.type === 'command' ? 'text-indigo-400' : 'text-amber-400'}">#${state.commandHistory.length - idx} [${item.type === 'command' ? '커맨드' : '공지'}]</span>
              <span>${timeStr}</span>
            </div>
            <pre class="text-xs font-mono text-emerald-300 selectable-text break-all whitespace-pre-wrap bg-slate-900 p-2.5 rounded-lg border border-slate-800">${escapeHtml(item.content)}</pre>
            <div class="flex justify-end">
              <button class="archive-copy-btn px-2.5 py-1 text-xs font-semibold rounded-lg bg-slate-800 hover:bg-slate-700 text-slate-200 border border-slate-700 transition flex items-center gap-1" data-cmd="${escapeHtml(item.content)}">
                <span>복사하기</span>
              </button>
            </div>
          </div>
        `;
      }).join('');
    }

    function broadcastSync(payload) {
      if (broadcastChannel) {
        try {
          broadcastChannel.postMessage(payload);
        } catch (e) {
          console.warn('Broadcast failed:', e);
        }
      }
    }

    function handleIncomingSync(data) {
      if (!data) return;

      if (data.type === 'CHAT_MESSAGE') {
        state.messages.push(data.message);
        renderMessages();
      } else if (data.type === 'CHAT_CLEAR') {
        state.messages = [];
        renderMessages();
        showToast('강사에 의해 채팅 내역이 초기화되었습니다.');
      } else if (data.type === 'NOTICE_UPDATE') {
        state.activeNotice = data.notice;
        if (data.history) state.commandHistory = data.history;
        updateNoticeDisplay();
        showToast('📢 강사로부터 새 커맨드가 도착했습니다!', 'info');
      } else if (data.type === 'REACTION') {
        const layer = document.getElementById('reactionLayer');
        if (layer) {
          const el = document.createElement('div');
          el.className = 'absolute text-3xl select-none animate-float-reaction z-30';
          el.textContent = data.emoji;
          el.style.left = `${15 + Math.random() * 70}%`;
          el.style.bottom = '15px';
          layer.appendChild(el);
          setTimeout(() => { if (el.parentNode) el.parentNode.removeChild(el); }, 2300);
        }
      }
    }

    function showToast(message, type = 'info') {
      const container = document.getElementById('toastContainer');
      if (!container) return;

      const toast = document.createElement('div');
      const colors = {
        success: 'bg-emerald-950/90 border-emerald-500/50 text-emerald-200',
        error: 'bg-rose-950/90 border-rose-500/50 text-rose-200',
        info: 'bg-slate-900/90 border-indigo-500/50 text-slate-100'
      };

      toast.className = `px-4 py-2.5 rounded-xl border text-xs font-semibold shadow-xl backdrop-blur transform transition-all duration-300 ease-out flex items-center gap-2 pointer-events-auto ${colors[type] || colors.info}`;
      
      const icon = type === 'success' ? '✔' : (type === 'error' ? '✖' : 'ℹ');
      toast.innerHTML = `<span>${icon}</span><span>${escapeHtml(message)}</span>`;

      container.appendChild(toast);

      setTimeout(() => {
        toast.classList.add('opacity-0', 'translate-x-4');
        setTimeout(() => {
          if (toast.parentNode) toast.parentNode.removeChild(toast);
        }, 300);
      }, 2600);
    }

    function escapeHtml(string) {
      if (!string) return '';
      return String(string)
        .replace(/&/g, '&amp;')
        .replace(/</g, '&lt;')
        .replace(/>/g, '&gt;')
        .replace(/"/g, '&quot;')
        .replace(/'/g, '&#039;');
    }

    function updateRoleUI() {
      const isHost = state.user.role === 'host';
      const roleText = document.getElementById('roleText');
      const roleIcon = document.getElementById('roleIcon');
      const editNoticeBtn = document.getElementById('editNoticeBtn');
      const clearChatBtn = document.getElementById('clearChatBtn');
      const hostQuickStartBox = document.getElementById('hostQuickStartBox');

      if (roleText) roleText.textContent = isHost ? '강사 모드' : '청중 모드';
      if (roleIcon) roleIcon.textContent = isHost ? '🎓' : '👥';
      
      if (editNoticeBtn) editNoticeBtn.classList.toggle('hidden', !isHost);
      if (clearChatBtn) clearChatBtn.classList.toggle('hidden', !isHost);
      if (hostQuickStartBox) hostQuickStartBox.classList.toggle('hidden', !isHost);

      showToast(`모드가 [${isHost ? '강사 모드' : '청중 모드'}]로 전환되었습니다.`);
    }

    // Attach all interactive event handlers
    document.addEventListener('DOMContentLoaded', () => {
      // 1. Initial State Setup
      state.commandHistory.push(state.activeNotice);
      updateNoticeDisplay();
      renderMessages();

      const userDisplay = document.getElementById('userNicknameDisplay');
      const userDot = document.getElementById('userAvatarDot');
      if (userDisplay) userDisplay.textContent = state.user.nickname;
      if (userDot) userDot.style.backgroundColor = state.user.color;

      // 2. Role Toggle
      document.getElementById('roleToggleBtn').addEventListener('click', () => {
        state.user.role = state.user.role === 'host' ? 'audience' : 'host';
        updateRoleUI();
      });
      updateRoleUI();

      // 3. Primary Command Copy Button
      document.getElementById('copyCommandBtn').addEventListener('click', (e) => {
        const text = state.activeNotice.content;
        copyTextToClipboard(text, e.currentTarget, '커맨드가 클립보드에 복사되었습니다!');
      });

      // 4. Standalone window opener
      const openInNewTab = () => {
        window.open(window.location.href, '_blank');
      };
      document.getElementById('openNewTabBtn').addEventListener('click', openInNewTab);
      document.getElementById('openNewTabFromModalBtn').addEventListener('click', openInNewTab);

      // 5. Media & Camera Controls
      document.getElementById('quickScreenShareBtn').addEventListener('click', toggleScreenShare);
      document.getElementById('bottomScreenShareBtn').addEventListener('click', toggleScreenShare);
      document.getElementById('quickVirtualCamBtn').addEventListener('click', () => toggleWebcam(true));
      document.getElementById('bottomVirtualCamBtn').addEventListener('click', () => toggleWebcam(true));
      document.getElementById('bottomCamBtn').addEventListener('click', () => toggleWebcam(false));
      document.getElementById('closeCamBtn').addEventListener('click', () => {
        stopCameraStream();
        document.getElementById('pipCamBox').classList.add('hidden');
      });
      document.getElementById('switchCamModeBtn').addEventListener('click', () => {
        if (state.isVirtualCam) toggleWebcam(false);
        else toggleWebcam(true);
      });

      // Camera help modal handlers
      const camModal = document.getElementById('cameraHelpModal');
      document.getElementById('closeCamHelpBtn').addEventListener('click', () => camModal.classList.add('hidden'));
      document.getElementById('dismissCamHelpBtn').addEventListener('click', () => camModal.classList.add('hidden'));
      document.getElementById('launchVirtualCamFromModalBtn').addEventListener('click', () => {
        camModal.classList.add('hidden');
        startVirtualCam();
      });
      document.getElementById('retryCameraBtn').addEventListener('click', () => {
        camModal.classList.add('hidden');
        toggleWebcam(false);
      });

      // Fullscreen
      document.getElementById('fullscreenBtn').addEventListener('click', () => {
        const stage = document.getElementById('screenContainer');
        if (!document.fullscreenElement) {
          stage.requestFullscreen().catch(err => showToast('전체화면 전환에 실패했습니다.', 'error'));
        } else {
          document.exitFullscreen();
        }
      });

      // 6. Live Emoji Reactions
      document.querySelectorAll('.reaction-btn').forEach(btn => {
        btn.addEventListener('click', () => {
          const emoji = btn.getAttribute('data-emoji');
          triggerReaction(emoji);
        });
      });

      // 7. Chat Send & Input
      const chatForm = document.getElementById('chatForm');
      const chatInput = document.getElementById('chatInput');
      const questionCheck = document.getElementById('isQuestionCheck');

      chatForm.addEventListener('submit', (e) => {
        e.preventDefault();
        const text = chatInput.value.trim();
        if (!text) return;

        const newMsg = {
          id: 'msg_' + Date.now() + '_' + Math.random().toString(36).substring(2, 6),
          nickname: state.user.nickname,
          role: state.user.role,
          color: state.user.color,
          content: text,
          isQuestion: questionCheck.checked,
          timestamp: Date.now()
        };

        state.messages.push(newMsg);
        renderMessages();
        broadcastSync({ type: 'CHAT_MESSAGE', message: newMsg });

        chatInput.value = '';
        if (questionCheck.checked) questionCheck.checked = false;
      });

      // Copy buttons within chat stream (delegation)
      document.getElementById('chatMessageList').addEventListener('click', (e) => {
        const copyBtn = e.target.closest('.copy-chat-btn');
        if (copyBtn) {
          const text = copyBtn.getAttribute('data-text');
          copyTextToClipboard(text, copyBtn, '채팅 내용이 복사되었습니다.');
        }
      });

      // 8. Chat Tabs
      const tabAll = document.getElementById('chatTabAll');
      const tabQa = document.getElementById('chatTabQa');
      tabAll.addEventListener('click', () => {
        state.currentTab = 'all';
        tabAll.className = 'px-3 py-1 rounded-lg text-xs font-semibold bg-indigo-600 text-white transition';
        tabQa.className = 'px-3 py-1 rounded-lg text-xs font-medium text-slate-400 hover:text-slate-200 transition flex items-center gap-1';
        renderMessages();
      });
      tabQa.addEventListener('click', () => {
        state.currentTab = 'qa';
        tabQa.className = 'px-3 py-1 rounded-lg text-xs font-semibold bg-indigo-600 text-white transition flex items-center gap-1';
        tabAll.className = 'px-3 py-1 rounded-lg text-xs font-medium text-slate-400 hover:text-slate-200 transition';
        renderMessages();
      });

      // Clear chat (Host only)
      document.getElementById('clearChatBtn').addEventListener('click', () => {
        state.messages = [];
        renderMessages();
        broadcastSync({ type: 'CHAT_CLEAR' });
        showToast('채팅 내역을 초기화했습니다.');
      });

      // 9. Notice / Command Broadcast Modal
      const noticeModal = document.getElementById('noticeModal');
      const noticeInput = document.getElementById('noticeContentInput');
      const typeCmdBtn = document.getElementById('typeSelectCmd');
      const typeNoticeBtn = document.getElementById('typeSelectNotice');

      document.getElementById('editNoticeBtn').addEventListener('click', () => {
        noticeInput.value = state.activeNotice.content;
        noticeModal.classList.remove('hidden');
      });
      document.getElementById('closeNoticeModalBtn').addEventListener('click', () => noticeModal.classList.add('hidden'));
      document.getElementById('cancelNoticeModalBtn').addEventListener('click', () => noticeModal.classList.add('hidden'));

      typeCmdBtn.addEventListener('click', () => {
        state.selectedNoticeType = 'command';
        typeCmdBtn.className = 'py-2 rounded-xl text-xs font-semibold bg-indigo-600 text-white border border-indigo-500 transition';
        typeNoticeBtn.className = 'py-2 rounded-xl text-xs font-semibold bg-slate-800 text-slate-300 border border-slate-700 transition';
      });
      typeNoticeBtn.addEventListener('click', () => {
        state.selectedNoticeType = 'notice';
        typeNoticeBtn.className = 'py-2 rounded-xl text-xs font-semibold bg-indigo-600 text-white border border-indigo-500 transition';
        typeCmdBtn.className = 'py-2 rounded-xl text-xs font-semibold bg-slate-800 text-slate-300 border border-slate-700 transition';
      });

      // Presets
      document.querySelectorAll('.preset-btn').forEach(btn => {
        btn.addEventListener('click', () => {
          noticeInput.value = btn.getAttribute('data-cmd');
          const type = btn.getAttribute('data-type');
          if (type === 'command') typeCmdBtn.click();
          else typeNoticeBtn.click();
        });
      });

      document.getElementById('publishNoticeBtn').addEventListener('click', () => {
        const text = noticeInput.value;
        if (!text.trim()) {
          showToast('배포할 내용을 입력해주세요.', 'error');
          return;
        }
        publishNotice(text, state.selectedNoticeType);
        noticeModal.classList.add('hidden');
      });

      // 10. Command Archive Modal
      const archiveModal = document.getElementById('archiveModal');
      document.getElementById('openArchiveBtn').addEventListener('click', () => {
        renderArchiveModal();
        archiveModal.classList.remove('hidden');
      });
      document.getElementById('closeArchiveModalBtn').addEventListener('click', () => archiveModal.classList.add('hidden'));

      // Copy from archive
      document.getElementById('archiveList').addEventListener('click', (e) => {
        const copyBtn = e.target.closest('.archive-copy-btn');
        if (copyBtn) {
          const cmd = copyBtn.getAttribute('data-cmd');
          copyTextToClipboard(cmd, copyBtn, '보관함 커맨드가 복사되었습니다!');
        }
      });

      // 11. Profile Edit Modal
      const profileModal = document.getElementById('profileModal');
      const nicknameInput = document.getElementById('nicknameInput');
      document.getElementById('profileEditBtn').addEventListener('click', () => {
        nicknameInput.value = state.user.nickname;
        profileModal.classList.remove('hidden');
      });
      document.getElementById('closeProfileModalBtn').addEventListener('click', () => profileModal.classList.add('hidden'));

      document.querySelectorAll('.color-picker-btn').forEach(btn => {
        btn.addEventListener('click', () => {
          document.querySelectorAll('.color-picker-btn').forEach(b => b.classList.remove('ring-2', 'ring-offset-2', 'ring-offset-slate-900', 'ring-indigo-500'));
          btn.classList.add('ring-2', 'ring-offset-2', 'ring-offset-slate-900', 'ring-indigo-500');
          state.user.color = btn.getAttribute('data-color');
        });
      });

      document.getElementById('saveProfileBtn').addEventListener('click', () => {
        const newNick = nicknameInput.value.trim();
        if (newNick) {
          state.user.nickname = newNick;
          document.getElementById('userNicknameDisplay').textContent = newNick;
          document.getElementById('userAvatarDot').style.backgroundColor = state.user.color;
          try {
            localStorage.setItem('liveclass_user_profile', JSON.stringify({
              nickname: state.user.nickname,
              color: state.user.color
            }));
          } catch (e) {}
          showToast('프로필이 저장되었습니다.', 'success');
        }
        profileModal.classList.add('hidden');
      });
    });
  </script>
</body>
</html>
