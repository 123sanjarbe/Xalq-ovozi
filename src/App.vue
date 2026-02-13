<template>
  <div class="app" :class="theme" ref="app">
    <div class="noise-overlay"></div>
    <div class="ambient">
      <div class="orb orb-1"></div>
      <div class="orb orb-2"></div>
      <div class="orb orb-3"></div>
      <div class="orb orb-4"></div>
      <div class="orb orb-5"></div>
      <div class="orb orb-6"></div>
      <canvas ref="canvas" class="particle-canvas"></canvas>
      <div class="grid-bg"></div>
      <div class="gradient-overlay"></div>
    </div>

    <div class="progress-bar" :style="{ width: scrollPct + '%' }">
      <div class="progress-glow"></div>
      <div class="progress-particles"></div>
    </div>

    <nav class="nav" :class="{ scrolled: scrolled }">
      <div class="nav-wrap">

        <div class="logo" @click="goto('hero')">
          <svg class="logo-svg" viewBox="0 0 32 32" fill="none">
            <defs>
              <linearGradient id="lg1" x1="0%" y1="0%" x2="100%" y2="100%">
                <stop offset="0%" stop-color="#a78bfa" />
                <stop offset="50%" stop-color="#e879f9" />
                <stop offset="100%" stop-color="#fb923c" />
              </linearGradient>
              <filter id="glow">
                <feGaussianBlur stdDeviation="2" result="coloredBlur" />
                <feMerge>
                  <feMergeNode in="coloredBlur" />
                  <feMergeNode in="SourceGraphic" />
                </feMerge>
              </filter>
            </defs>
            <polygon points="16,2 30,10 30,22 16,30 2,22 2,10" stroke="url(#lg1)" stroke-width="2" fill="none"
              opacity="0.8" filter="url(#glow)" />
            <polygon points="16,8 24,13 24,19 16,24 8,19 8,13" stroke="url(#lg1)" stroke-width="1.2"
              fill="rgba(167,139,250,0.12)" />
            <circle cx="16" cy="16" r="3" fill="url(#lg1)" filter="url(#glow)">
              <animate attributeName="r" values="3;3.5;3" dur="2s" repeatCount="indefinite" />
            </circle>
          </svg>
          <span class="logo-txt">{{ t.brand }}</span>
        </div>

        <div class="nav-links" id="desktopLinks">
          <a v-for="l in navItems" :key="l.id" class="nlink" @click="goto(l.id)">
            <span>{{ l.label }}</span>
            <span class="nlink-line"></span>
          </a>
        </div>

        <div class="nav-right">
          <div class="mobile-lang-wrapper" ref="langDropdown">
            <button class="mobile-lang-btn" @click="toggleLangMenu">
              <span class="current-flag">{{ getCurrentFlag() }}</span>
              <svg width="14" height="14" viewBox="0 0 24 24" fill="none">
                <path d="M6 9l6 6 6-6" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" />
              </svg>
            </button>
            <transition name="lang-dropdown">
              <div class="lang-dropdown" v-if="langMenuOpen">
                <button v-for="lg in langs" :key="lg.code" class="lang-option" :class="{ active: cur === lg.code }"
                  @click="selectLang(lg.code)">
                  <span class="lang-flag">{{ lg.flag }}</span>
                  <span class="lang-name">{{ lg.name }}</span>
                </button>
              </div>
            </transition>
          </div>

          <button v-if="isAdmin" class="admin-btn" @click="showAdminPanel = !showAdminPanel" title="Admin Panel">
            <svg width="20" height="20" viewBox="0 0 24 24" fill="none">
              <path d="M12 15a3 3 0 100-6 3 3 0 000 6z" stroke="currentColor" stroke-width="2" />
              <path
                d="M19.4 15a1.65 1.65 0 00.33 1.82l.06.06a2 2 0 010 2.83 2 2 0 01-2.83 0l-.06-.06a1.65 1.65 0 00-1.82-.33 1.65 1.65 0 00-1 1.51V21a2 2 0 01-2 2 2 2 0 01-2-2v-.09A1.65 1.65 0 009 19.4a1.65 1.65 0 00-1.82.33l-.06.06a2 2 0 01-2.83 0 2 2 0 010-2.83l.06-.06a1.65 1.65 0 00.33-1.82 1.65 1.65 0 00-1.51-1H3a2 2 0 01-2-2 2 2 0 012-2h.09A1.65 1.65 0 004.6 9a1.65 1.65 0 00-.33-1.82l-.06-.06a2 2 0 010-2.83 2 2 0 012.83 0l.06.06a1.65 1.65 0 001.82.33H9a1.65 1.65 0 001-1.51V3a2 2 0 012-2 2 2 0 012 2v.09a1.65 1.65 0 001 1.51 1.65 1.65 0 001.82-.33l.06-.06a2 2 0 012.83 0 2 2 0 010 2.83l-.06.06a1.65 1.65 0 00-.33 1.82V9a1.65 1.65 0 001.51 1H21a2 2 0 012 2 2 2 0 01-2 2h-.09a1.65 1.65 0 00-1.51 1z"
                stroke="currentColor" stroke-width="2" />
            </svg>
          </button>

          <button v-if="isAdmin" class="logout-btn" @click="logout" title="Chiqish">
            <svg width="20" height="20" viewBox="0 0 24 24" fill="none">
              <path d="M9 21H5a2 2 0 01-2-2V5a2 2 0 012-2h4M16 17l5-5-5-5M21 12H9" stroke="currentColor"
                stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
            </svg>
          </button>

          <button class="tbtn" @click="toggleTheme">
            <span class="theme-icon">{{ theme === 'dark' ? '☀️' : '🌙' }}</span>
          </button>
          <button class="mbtn" @click="mOpen = !mOpen">
            <span class="hline h1" :class="{ open: mOpen }"></span>
            <span class="hline h2" :class="{ open: mOpen }"></span>
            <span class="hline h3" :class="{ open: mOpen }"></span>
          </button>
        </div>
      </div>

      <div class="mmenu" :class="{ open: mOpen }">
        <a v-for="l in navItems" :key="l.id" class="mlink" @click="goto(l.id); mOpen = false">
          <span class="mlink-icon">{{ getNavIcon(l.id) }}</span>
          {{ l.label }}
        </a>
      </div>
    </nav>

    <transition name="modal-fade">
      <div class="modal-overlay" v-if="showAdminPanel" @click="showAdminPanel = false">
        <div class="admin-modal" @click.stop>
          <div class="admin-header">
            <h3>🔐 Admin Panel</h3>
            <button class="admin-close" @click="showAdminPanel = false">✕</button>
          </div>

          <div class="admin-stats-grid">
            <div class="admin-stat-card">
              <div class="admin-stat-icon">📊</div>
              <div class="admin-stat-value">{{ reqs.length }}</div>
              <div class="admin-stat-label">Jami Takliflar</div>
            </div>
            <div class="admin-stat-card">
              <div class="admin-stat-icon">❤️</div>
              <div class="admin-stat-value">{{ totalLikes }}</div>
              <div class="admin-stat-label">Jami Ovozlar</div>
            </div>
            <div class="admin-stat-card">
              <div class="admin-stat-icon">📅</div>
              <div class="admin-stat-value">{{ todayRequests }}</div>
              <div class="admin-stat-label">Bugungi Takliflar</div>
            </div>
          </div>

          <div class="admin-search">
            <input type="text" v-model="adminSearch" placeholder="🔍 Qidirish..." class="admin-search-input">
          </div>

          <div class="admin-requests">
            <div v-for="r in filteredAdminRequests" :key="r.id" class="admin-req-item">
              <div class="admin-req-content">
                <div class="admin-req-meta">
                  <span class="admin-req-badge">{{ r.category }}</span>
                  <span class="admin-req-region">📍 {{ r.region }}</span>
                  <span class="admin-req-date">{{ fmtDate(r.date) }}</span>
                </div>
                <p class="admin-req-text">{{ r.text }}</p>
                <div class="admin-req-stats">
                  <span>❤️ {{ r.likes || 0 }} ovoz</span>
                  <span>👤 {{ r.likedBy?.length || 0 }} foydalanuvchi</span>
                </div>
              </div>
              <button class="admin-delete-btn" @click="deleteRequest(r.id)" title="O'chirish">
                <svg width="18" height="18" viewBox="0 0 24 24" fill="none">
                  <path
                    d="M3 6h18M19 6v14a2 2 0 01-2 2H7a2 2 0 01-2-2V6m3 0V4a2 2 0 012-2h4a2 2 0 012 2v2M10 11v6M14 11v6"
                    stroke="currentColor" stroke-width="2" stroke-linecap="round" />
                </svg>
              </button>
            </div>
          </div>
        </div>
      </div>
    </transition>

    <transition name="modal-fade">
      <div class="modal-overlay" v-if="showEmailLogin" @click="showEmailLogin = false">
        <div class="email-modal" @click.stop>
          <button class="modal-close" @click="showEmailLogin = false">✕</button>
          <h3>🔐 Admin kirish</h3>
          <p class="email-desc">Admin paneliga kirish uchun emailingiz va parolni kiriting</p>
          <input type="email" v-model="loginEmail" placeholder="Email manzilingiz" class="email-input">
          <input type="password" v-model="loginPassword" placeholder="Parol" class="email-input">
          <button class="btn btn-pri btn-full" @click="checkAdminLogin">Kirish</button>
        </div>
      </div>
    </transition>









    
  <section id="hero" class="hero">
      <div class="hero-inner">

        <div class="badge anim-in" style="--d:0s">
          <span class="badge-pulse"></span>
          <span>{{ t.hero.badge }}</span>
        </div>

        <h1 class="hero-h1">
          <span class="h1-top anim-in" style="--d:0.15s">{{ t.hero.line1 }}</span>
          <span class="h1-bot anim-in" style="--d:0.3s">
            {{ t.hero.line2 }}
            <span class="cursor-blink"></span>
          </span>
        </h1>

        <p class="hero-p anim-in" style="--d:0.45s">{{ t.hero.desc }}</p>

        <div class="hero-btns anim-in" style="--d:0.6s">
          <button class="btn btn-pri" @click="goto('submit')">
            <span class="btn-shine"></span>
            <svg width="18" height="18" viewBox="0 0 24 24" fill="none">
              <path d="M12 5v14M5 12l7 7 7-7" stroke="#fff" stroke-width="2.5" stroke-linecap="round" />
            </svg>
            {{ t.hero.cta }}
          </button>
          <button class="btn btn-out" @click="goto('requests')">
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none">
              <path d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" stroke="currentColor" stroke-width="2"
                stroke-linecap="round" />
            </svg>
            {{ t.hero.viewAll }}
          </button>
          <button v-if="!isAdmin" class="btn btn-ghost" @click="showEmailLogin = true">
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none">
              <path
                d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z"
                stroke="currentColor" stroke-width="2" stroke-linecap="round" />
            </svg>
            Admin
          </button>
        </div>

        <div class="stats anim-in" style="--d:0.75s">
          <div class="stat" v-for="(s, i) in heroStats" :key="i">
            <div class="stat-n">
              <span class="count-up" :data-target="s.val">0</span>
            </div>
            <div class="stat-l">{{ s.lbl }}</div>
          </div>
        </div>
      </div>

      <div class="hero-float float-1">🎯</div>
      <div class="hero-float float-2">✨</div>
      <div class="hero-float float-3">🚀</div>
    </section>

    <section id="about" class="sec sec-why">
      <div class="sec-wrap">
        <div class="sec-head">
          <span class="tag reveal">{{ t.why.tag }}</span>
          <h2 class="sec-h2 reveal">{{ t.why.title }}</h2>
          <p class="sec-p reveal">{{ t.why.sub }}</p>
        </div>
        <div class="feat-grid">
          <div v-for="(f, i) in t.why.list" :key="i" class="fcard reveal" :style="{ '--di': i }">
            <div class="fcard-glow"></div>
            <div class="fcard-border"></div>
            <div class="fcard-sparkle"></div>
            <div class="ficon">{{ f.ic }}</div>
            <h3 class="ftitle">{{ f.title }}</h3>
            <p class="fdesc">{{ f.desc }}</p>
            <div class="fcard-arrow">→</div>
          </div>
        </div>
      </div>
    </section>

    <section id="submit" class="sec sec-sub">
      <div class="sec-wrap">
        <div class="sec-head">
          <span class="tag reveal">{{ t.sub.tag }}</span>
          <h2 class="sec-h2 reveal">{{ t.sub.title }}</h2>
          <p class="sec-p reveal">{{ t.sub.sub }}</p>
        </div>

        <div class="form-box reveal">
          <div class="form-box-glow"></div>
          <div class="form-box-particles"></div>

          <div class="fld">
            <label class="flbl">
              <span class="flbl-icon">📂</span>
              {{ t.sub.catLbl }} <span class="ast">*</span>
            </label>
            <div class="chips">
              <button v-for="c in cats" :key="c" class="chip" :class="{ on: nw.cat === c }" @click="selectCategory(c)">
                {{ c }}
              </button>
            </div>
            <transition name="err-slide">
              <span class="ferr" v-if="err.cat">⚠️ {{ err.cat }}</span>
            </transition>
          </div>

          <div class="fld">
            <label class="flbl">
              <span class="flbl-icon">✍️</span>
              {{ t.sub.txtLbl }} <span class="ast">*</span>
            </label>
            <div class="textarea-wrap">
              <textarea class="ta" v-model="nw.txt" :placeholder="t.sub.ph" rows="5" @input="updateCharCount"
                maxlength="500"></textarea>
              <span class="char-count">{{ nw.txt.length }} / 500</span>
            </div>
            <transition name="err-slide">
              <span class="ferr" v-if="err.txt">⚠️ {{ err.txt }}</span>
            </transition>
          </div>

          <div class="fld">
            <label class="flbl">
              <span class="flbl-icon">📍</span>
              {{ t.sub.regLbl }} <span class="ast">*</span>
            </label>
            <div class="sel-wrap">
              <select class="sel" v-model="nw.reg">
                <option value="">{{ t.sub.selReg }}</option>
                <option v-for="r in regs" :key="r" :value="r">{{ r }}</option>
              </select>
              <svg class="sel-arr" viewBox="0 0 24 24" width="18" height="18">
                <path d="M6 9l6 6 6-6" stroke="currentColor" stroke-width="2.5" fill="none" stroke-linecap="round" />
              </svg>
            </div>
            <transition name="err-slide">
              <span class="ferr" v-if="err.reg">⚠️ {{ err.reg }}</span>
            </transition>
          </div>

          <button class="btn btn-pri btn-full" :disabled="sending" @click="send">
            <span class="btn-shine"></span>
            <transition name="fade" mode="out-in">
              <span v-if="!sending" key="submit">
                <svg width="18" height="18" viewBox="0 0 24 24" fill="none">
                  <path d="M22 2L11 13M22 2l-7 20-4-9-9-4 20-7z" stroke="currentColor" stroke-width="2"
                    stroke-linecap="round" stroke-linejoin="round" />
                </svg>
                {{ t.sub.btn }}
              </span>
              <span v-else key="loading" class="ldtxt">
                <svg class="spinner" viewBox="0 0 24 24" width="18" height="18">
                  <circle cx="12" cy="12" r="10" stroke="currentColor" stroke-width="3" fill="none"
                    stroke-dasharray="31.4 31.4" stroke-linecap="round" />
                </svg>
                {{ t.sub.ld }}
              </span>
            </transition>
          </button>

          <p class="discl">⚡ {{ t.sub.discl }}</p>
        </div>
      </div>
    </section>

    <section id="requests" class="sec sec-req">
      <div class="sec-wrap">
        <div class="sec-head">
          <span class="tag reveal">{{ t.req.tag }}</span>
          <h2 class="sec-h2 reveal">{{ t.req.title }}</h2>
          <p class="sec-p reveal">{{ t.req.sub }}</p>
        </div>

        <div class="filter-controls reveal">
          <div class="pills">
            <button class="pill" :class="{ on: actFilt === 'all' }" @click="actFilt = 'all'">
              <span class="pill-count">{{ reqs.length }}</span>
              {{ t.req.all }}
            </button>
            <button v-for="c in cats" :key="c" class="pill" :class="{ on: actFilt === c }" @click="actFilt = c">
              <span class="pill-count">{{reqs.filter(r => r.category === c).length}}</span>
              {{ c }}
            </button>
          </div>

          <div class="sort-options">
            <button class="sort-btn" :class="{ active: sortBy === 'likes' }" @click="sortBy = 'likes'">
              <svg width="16" height="16" viewBox="0 0 24 24" fill="none">
                <path
                  d="M20.84 4.61a5.5 5.5 0 0 0-7.78 0L12 5.67l-1.06-1.06a5.5 5.5 0 0 0-7.78 7.78l1.06 1.06L12 21.23l7.78-7.78 1.06-1.06a5.5 5.5 0 0 0 0-7.78z"
                  stroke="currentColor" stroke-width="2" />
              </svg>
              Eng ko'p ovoz
            </button>
            <button class="sort-btn" :class="{ active: sortBy === 'date' }" @click="sortBy = 'date'">
              <svg width="16" height="16" viewBox="0 0 24 24" fill="none">
                <path d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" stroke="currentColor" stroke-width="2" />
              </svg>
              Eng yangi
            </button>
          </div>
        </div>

        <div class="sortlabel reveal">
          <svg width="14" height="14" viewBox="0 0 24 24" fill="none">
            <path d="M7 16V4M7 16l-3-3M7 16l3-3M17 8v12M17 8l3 3M17 8l-3 3" stroke="currentColor" stroke-width="2"
              stroke-linecap="round" />
          </svg>
          {{ sortBy === 'likes' ? "Eng ko'p ovoz olgan oldinda" : "Eng yangi oldinda" }}
          <span class="live-indicator">
            <span class="live-dot"></span>
            LIVE
          </span>
        </div>

        <div class="search-bar reveal">
          <svg class="search-icon" width="20" height="20" viewBox="0 0 24 24" fill="none">
            <path d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" stroke="currentColor" stroke-width="2"
              stroke-linecap="round" />
          </svg>
          <input type="text" v-model="searchQuery" placeholder="Takliflarni qidirish..." class="search-input">
          <button v-if="searchQuery" class="search-clear" @click="searchQuery = ''">✕</button>
        </div>

        <transition-group name="card-list" tag="div" class="req-grid">
          <div v-for="(r, idx) in sorted" :key="r.id" class="rcard reveal vis" :style="{ '--di': idx }">
            <div class="rcard-shine"></div>
            <div class="rcard-glow"></div>
            <div class="rcard-new" v-if="isNew(r)">🆕</div>

            <div class="rcard-trending" v-if="isTrending(r)">
              <svg width="14" height="14" viewBox="0 0 24 24" fill="none">
                <path d="M13 7h8m0 0v8m0-8l-8 8-4-4-6 6" stroke="currentColor" stroke-width="2.5" stroke-linecap="round"
                  stroke-linejoin="round" />
              </svg>
              Trending
            </div>

            <div class="rcard-top">
              <span class="rbadge">{{ r.category }}</span>
              <span class="rloc">📍 {{ r.region }}</span>
            </div>

            <p class="rtxt">{{ r.text }}</p>

            <div class="rbot">
              <span class="rdate">{{ fmtDate(r.date) }}</span>
              <button class="lbk" :class="{ on: liked(r), pulse: justLiked === r.id }" @click.stop="like(r)">
                <span class="lheart">
                  <svg width="17" height="17" viewBox="0 0 24 24">
                    <path
                      d="M20.84 4.61a5.5 5.5 0 0 0-7.78 0L12 5.67l-1.06-1.06a5.5 5.5 0 0 0-7.78 7.78l1.06 1.06L12 21.23l7.78-7.78 1.06-1.06a5.5 5.5 0 0 0 0-7.78z"
                      :fill="liked(r) ? 'currentColor' : 'none'" stroke="currentColor" stroke-width="2"
                      stroke-linecap="round" stroke-linejoin="round" />
                  </svg>
                </span>
                <span class="lcnt">{{ r.likes }}</span>
              </button>
            </div>
          </div>
        </transition-group>

        <transition name="fade">
          <div class="empty" v-if="sorted.length === 0">
            <div class="empty-ic">🌐</div>
            <p>{{ searchQuery ? "Hech narsa topilmadi" : t.req.empty }}</p>
          </div>
        </transition>

        <div class="load-more-section" v-if="hasMoreRequests">
          <button class="btn btn-out load-more-btn" @click="loadMore">
            <svg width="18" height="18" viewBox="0 0 24 24" fill="none">
              <path d="M19 9l-7 7-7-7" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" />
            </svg>
            Yana yuklash
          </button>
        </div>
      </div>
    </section>

    <footer class="footer">
      <div class="ft-wrap">
        <div class="ft-brand">
          <div class="logo" style="margin-bottom:1rem" @click="goto('hero')">
            <svg class="logo-svg" viewBox="0 0 32 32" fill="none">
              <polygon points="16,2 30,10 30,22 16,30 2,22 2,10" stroke="url(#lg1)" stroke-width="2" fill="none"
                opacity="0.8" />
              <circle cx="16" cy="16" r="3" fill="url(#lg1)" />
            </svg>
            <span class="logo-txt">{{ t.brand }}</span>
          </div>
          <p class="ft-desc">{{ t.ft.desc }}</p>
          <div class="ft-social">
            <a href="#" class="social-link">📘</a>
            <a href="#" class="social-link">📷</a>
            <a href="#" class="social-link">🐦</a>
            <a href="#" class="social-link">💼</a>
          </div>
        </div>
        <div class="ft-links">
          <h4 class="ft-ltitle">{{ t.ft.links }}</h4>
          <a v-for="l in navItems" :key="l.id" class="ft-link" @click="goto(l.id)">{{ l.label }}</a>
        </div>
      </div>
      <div class="ft-bot">
        <p>© 2026 {{ t.brand }}. {{ t.ft.rights }}</p>
      </div>
    </footer>

    <transition name="toast-slide">
      <div class="toast" v-if="toastOn" :class="{ err: toastErr }">
        <div class="toast-ic">
          <transition name="icon-swap" mode="out-in">
            <svg v-if="!toastErr" key="success" width="20" height="20" viewBox="0 0 24 24" fill="none">
              <path d="M20 6L9 17l-5-5" stroke="#fff" stroke-width="3" stroke-linecap="round" stroke-linejoin="round" />
            </svg>
            <svg v-else key="error" width="20" height="20" viewBox="0 0 24 24" fill="none">
              <path d="M18 6L6 18M6 6l12 12" stroke="#fff" stroke-width="3" stroke-linecap="round" />
            </svg>
          </transition>
        </div>
        <div class="toast-body">
          <span class="toast-title">{{ toastErr ? (cur === 'uz' ? 'Xatolik' : cur === 'ru' ? 'Ошибка' : 'Error') :
            (cur === 'uz' ? 'Muvaffaqiyat' : cur === 'ru' ? 'Успех' :'Success') }}</span>
          <span class="toast-txt">{{ toastMsg }}</span>
        </div>
        <button class="toast-close" @click="toastOn = false">✕</button>
      </div>
    </transition>

    <transition name="scale-fade">
      <button class="sctop" v-if="scrolled" @click="goto('hero')">
        <svg width="20" height="20" viewBox="0 0 24 24" fill="none">
          <path d="M12 19V5M5 12l7-7 7 7" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" />
        </svg>
      </button>
    </transition>

  </div>
</template>























<script>
import HeroSection from './components/HeroSection.vue'
export default {
  name: 'App',
   components: {
    HeroSection
  },
  data() {
    return {
      theme: 'dark',
      cur: 'uz',
      scrolled: false,
      scrollPct: 0,
      mOpen: false,
      langMenuOpen: false,
      actFilt: 'all',
      nw: { cat: '', txt: '', reg: '' },
      err: { cat: '', txt: '', reg: '' },
      sending: false,
      reqs: [],
      uid: '',
      toastOn: false,
      toastMsg: '',
      toastErr: false,
      animId: null,
      particles: [],
      justLiked: null,
      newRequestIds: [],

      isAdmin: false,
      showAdminPanel: false,
      showEmailLogin: false,
      loginEmail: '',
      loginPassword: '',
      adminEmail: '123sanjarbe@gmail.com',
      adminPassword: '123Sanjar3211',
      adminSearch: '',
      sortBy: 'likes',
      searchQuery: '',
      displayLimit: 12,

      // Real-time polling
      pollingInterval: null,
      lastFetchTime: 0,

      langs: [
        { code: 'uz', name: "O'zbek", flag: '🇺🇿' },
        { code: 'ru', name: 'Русский', flag: '🇷🇺' },
        { code: 'en', name: 'English', flag: '🇬🇧' }
      ],
      cats: ["Ta'lim", "Sog'liqni Saqlash", "Yo'llar", "Ekologiya", "Sport", "Madaniyat", "Iqtisodiyot", "Boshqa"],
      regs: ['Toshkent sh.', 'Toshkent vil.', 'Andijon vil.', 'Buxoro vil.', "Farg'ona vil.", 'Jizzax vil.', 'Xorazm vil.', 'Namangan vil.', 'Navoiy vil.', 'Qashqadaryo vil.', 'Qoraqalpog\'iston Res.', 'Samarqand vil.', 'Sirdaryo vil.', 'Surxondaryo vil.'],

      i18n: {
        uz: {
          brand: 'Xalq Ovozi',
          hero: { badge: 'Yangi platforma', line1: 'Ovozingiz', line2: 'Muhim!', desc: "Jamiyat uchun muhim bo'lgan takliflaringizni yuboring. Eng ko'p ovoz to'plagan takliflar amalga oshiriladi.", cta: 'Talab Yuborish', viewAll: "Talablarni Ko'rish" },
          why: {
            tag: 'Nega biz', title: "Xalq Ovozi — Haqiqiy O'zgartirish", sub: "Biz xalq fikrini eshitamiz va eng muhim takliflarni amalga oshiramiz", list: [
              { ic: '🗳️', title: 'Demokratik Ovoz', desc: "Har bir fuqaro o'z fikrini bildirib, jamiyat rivojga hissa qo'shadi." },
              { ic: '👁️', title: 'Shaffoflik', desc: "Barcha takliflar ochiq. Ko'p ovoz to'plagan takliflar birinchi amalga oshiriladi." },
              { ic: '⚡', title: 'Tezkor Javob', desc: "Eng dolzarb muammolarni tez va samarali hal qilish uchun darhol ko'rib chiqamiz." },
              { ic: '📍', title: "Viloyatlar Bo'yicha", desc: "Har viloyatning o'ziga xos muammolarini alohida e'tiborga olamiz." },
              { ic: '🤝', title: 'Hamjamiyat', desc: "Birgalikda biz kuchliymiz. Jamoa bo'lib yurtimizni go'zallashtramiz." },
              { ic: '🌱', title: 'Barqaror Rivojlanish', desc: 'Uzoq muddatli va barqaror yechimlarni topishga intilamiz.' }
            ]
          },
          sub: { tag: 'Taklad', title: 'Taklifingiz Yuboring', sub: "O'z fikr-mulohazalaringizni bildiring", catLbl: 'Kategoriya', txtLbl: 'Taklifingiz', ph: 'Taklifingizni batafsil yozing...', regLbl: 'Viloyat', selReg: 'Viloyatni tanlang', btn: 'Yuborish', ld: 'Yuklanmoqda', discl: "Sizning talabingiz ko'rib chiqiladi va eng ko'p ovoz to'plagan takliflar amalga oshiriladi." },
          req: { tag: 'Talablar', title: 'Barcha Talablar', sub: "Ovoz bering va eng muhim takliflarni qo'llab-quvvatlang", all: 'Hammasi', sorted: "Eng ko'p ovoz bolan oldinda", empty: "Hozircha talablar yo'q" },
          ft: { desc: "Xalq uchun, xalq tomonidan — birgalikda yaxshiroq kelajak quramiz.", links: 'Tezkor Havolalar', rights: 'Barcha huquqlar himoyalangan.' },
          toast: { ok: 'Taklifingiz muvaffaqiyatli yuborildi!', bad: "Xatolik yuz berdi. Qaytadan urinib ko'ring." },
          v: { cat: 'Kategoriyani tanlang', txt: 'Taklifingizni yozing', txtM: 'Kamida 10 belgi yozing', reg: 'Viloyatni tanlang' }
        },
        ru: {
          brand: 'Голос Народа',
          hero: { badge: 'Новая платформа', line1: 'Ваш голос', line2: 'Важен!', desc: 'Отправьте предложения, важные для общества. Предложения с наибольшим числом голосов будут реализованы.', cta: 'Отправить Запрос', viewAll: 'Смотреть Запросы' },
          why: {
            tag: 'Почему мы', title: 'Голос Народа — Реальные Перемены', sub: 'Мы слушаем мнение людей и реализуем самые важные предложения', list: [
              { ic: '🗳️', title: 'Демократический Голос', desc: 'Каждый гражданин может высказать свое мнение и внести вклад.' },
              { ic: '👁️', title: 'Прозрачность', desc: 'Все предложения открыты. С наибольшим числом голосов реализуются первыми.' },
              { ic: '⚡', title: 'Быстрый Ответ', desc: 'Немедленно рассматриваем для быстрого решения актуальных проблем.' },
              { ic: '📍', title: 'По Регионам', desc: 'Особое внимание специфическим проблемам каждого региона.' },
              { ic: '🤝', title: 'Сообщество', desc: 'Вместе мы сильнее. Командой делаем страну прекраснее.' },
              { ic: '🌱', title: 'Устойчивое Развитие', desc: 'Долгосрочные и устойчивые решения, а не временные.' }
            ]
          },
          sub: { tag: 'Запрос', title: 'Отправьте Предложение', sub: 'Выскажите свое мнение', catLbl: 'Категория', txtLbl: 'Ваше предложение', ph: 'Опишите предложение подробно...', regLbl: 'Область', selReg: 'Выберите область', btn: 'Отправить', ld: 'Загрузка', discl: 'Ваш запрос будет рассмотрен и предложения с наибольшим числом голосов реализованы.' },
          req: { tag: 'Запросы', title: 'Все Запросы', sub: 'Голосуйте и поддерживайте важные предложения', all: 'Все', sorted: 'Сортировка по голосам', empty: 'Пока нет запросов' },
          ft: { desc: 'Для народа, от народа — вместе строим лучшее будущее.', links: 'Быстрые Ссылки', rights: 'Все права защищены.' },
          toast: { ok: 'Успешно отправлено!', bad: 'Произошла ошибка. Попробуйте снова.' },
          v: { cat: 'Выберите категорию', txt: 'Напишите предложение', txtM: 'Минимум 10 символов', reg: 'Выберите область' }
        },
        en: {
          brand: "People's Voice",
          hero: { badge: 'New Platform', line1: 'Your Voice', line2: 'Matters!', desc: 'Submit proposals important for society. Proposals with the most votes will be implemented.', cta: 'Submit Request', viewAll: 'View Requests' },
          why: {
            tag: 'Why Us', title: "People's Voice — Real Change", sub: 'We listen to the people and implement the most important proposals', list: [
              { ic: '🗳️', title: 'Democratic Voice', desc: 'Every citizen can express opinion and contribute to society.' },
              { ic: '👁️', title: 'Transparency', desc: 'All proposals open. Most voted implemented first.' },
              { ic: '⚡', title: 'Quick Response', desc: 'We immediately review demands to solve pressing problems fast.' },
              { ic: '📍', title: 'By Regions', desc: 'Special attention to specific problems of each region.' },
              { ic: '🤝', title: 'Community', desc: 'Together we are stronger. As a team we make our country better.' },
              { ic: '🌱', title: 'Sustainable Dev', desc: 'Long-term and sustainable solutions, not temporary fixes.' }
            ]
          },
          sub: { tag: 'Submit', title: 'Submit Your Proposal', sub: 'Share your thoughts', catLbl: 'Category', txtLbl: 'Your Proposal', ph: 'Describe your proposal in detail...', regLbl: 'Region', selReg: 'Select region', btn: 'Submit', ld: 'Loading', discl: 'Your request will be reviewed. Proposals with most votes will be implemented.' },
          req: { tag: 'Requests', title: 'All Requests', sub: 'Vote and support the most important proposals', all: 'All', sorted: 'Sorted by most votes', empty: 'No requests yet' },
          ft: { desc: 'For the people, by the people — building a better future together.', links: 'Quick Links', rights: 'All rights reserved.' },
          toast: { ok: 'Successfully submitted!', bad: 'An error occurred. Try again.' },
          v: { cat: 'Select a category', txt: 'Write your proposal', txtM: 'At least 10 characters', reg: 'Select a region' }
        }
      }
    }
  },

  computed: {
    t() { return this.i18n[this.cur] },
    navItems() {
      const h = { uz: ['Bosh sahifa', 'Nega biz', 'Talab yuborish', 'Talablar'], ru: ['Главная', 'Почему мы', 'Запрос', 'Запросы'], en: ['Home', 'Why Us', 'Submit', 'Requests'] }
      return ['hero', 'about', 'submit', 'requests'].map((id, i) => ({ id, label: h[this.cur][i] }))
    },
    heroStats() {
      const l = { uz: ['Talablar', 'Ovozlar', 'Kategoriyalar'], ru: ['Запросов', 'Голосов', 'Категории'], en: ['Requests', 'Votes', 'Categories'] }
      return [
        { val: this.reqs.length, lbl: l[this.cur][0] },
        { val: this.totalLikes, lbl: l[this.cur][1] },
        { val: this.cats.length, lbl: l[this.cur][2] }
      ]
    },
    sorted() {
      let list = [...this.reqs]

      if (this.actFilt !== 'all') {
        list = list.filter(r => r.category === this.actFilt)
      }

      if (this.searchQuery.trim()) {
        const q = this.searchQuery.toLowerCase()
        list = list.filter(r =>
          r.text.toLowerCase().includes(q) ||
          r.category.toLowerCase().includes(q) ||
          r.region.toLowerCase().includes(q)
        )
      }

      if (this.sortBy === 'likes') {
        list.sort((a, b) => (b.likes || 0) - (a.likes || 0))
      } else {
        list.sort((a, b) => new Date(b.date) - new Date(a.date))
      }

      return list.slice(0, this.displayLimit)
    },
    totalLikes() {
      return this.reqs.reduce((sum, r) => sum + (r.likes || 0), 0)
    },
    todayRequests() {
      const today = new Date().toDateString()
      return this.reqs.filter(r => new Date(r.date).toDateString() === today).length
    },
    filteredAdminRequests() {
      if (!this.adminSearch.trim()) return this.reqs
      const q = this.adminSearch.toLowerCase()
      return this.reqs.filter(r =>
        r.text.toLowerCase().includes(q) ||
        r.category.toLowerCase().includes(q) ||
        r.region.toLowerCase().includes(q)
      )
    },
    hasMoreRequests() {
      let total = this.reqs.length
      if (this.actFilt !== 'all') {
        total = this.reqs.filter(r => r.category === this.actFilt).length
      }
      if (this.searchQuery.trim()) {
        const q = this.searchQuery.toLowerCase()
        total = this.reqs.filter(r =>
          r.text.toLowerCase().includes(q) ||
          r.category.toLowerCase().includes(q) ||
          r.region.toLowerCase().includes(q)
        ).length
      }
      return this.displayLimit < total
    }
  },

  watch: {
    theme(v) { localStorage.setItem('xv_theme', v) },
    cur(v) { localStorage.setItem('xv_lang', v) },
    isAdmin(v) { localStorage.setItem('xv_admin', v) },
    reqs: {
      deep: true,
      handler(v) {
        this.saveToLocalStorage()
        this.$nextTick(() => {
          this.updateStats()
        })
      }
    }
  },

  mounted() {
    this.theme = localStorage.getItem('xv_theme') || 'dark'
    this.cur = localStorage.getItem('xv_lang') || 'uz'
    this.uid = localStorage.getItem('xv_uid') || this.mkUid()
    this.isAdmin = localStorage.getItem('xv_admin') === 'true'

    this.loadFromLocalStorage()
    this.startRealTimePolling()

    window.addEventListener('scroll', this.onScroll)
    document.addEventListener('click', this.handleClickOutside)

    this.$nextTick(() => {
      this.initCanvas()
      this.observe()
      this.animateCounters()
    })
  },

  beforeUnmount() {
    window.removeEventListener('scroll', this.onScroll)
    document.removeEventListener('click', this.handleClickOutside)
    if (this.animId) cancelAnimationFrame(this.animId)
    if (this.pollingInterval) clearInterval(this.pollingInterval)
  },

  methods: {
    mkUid() {
      const id = 'u_' + Date.now().toString(36) + Math.random().toString(36).slice(2, 8)
      localStorage.setItem('xv_uid', id)
      return id
    },

    startRealTimePolling() {
      this.pollingInterval = setInterval(() => this.fetchNewRequests(), 15000)
    },

    async fetchNewRequests() {
      try {
        const scriptURL = 'https://script.google.com/macros/s/AKfycbw942zwfIeumx4oYQ6DwWXj8VcM_5yBervTdn_C-mTzzKE8Ughlie-cLYXBUZMd0d7p/exec'

        const response = await fetch(`${scriptURL}?action=getAll&timestamp=${Date.now()}`, {
          method: 'GET',
          headers: {
            'Accept': 'application/json'
          }
        })

        if (response.ok) {
          const data = await response.json()

          if (data && data.requests && Array.isArray(data.requests)) {
            this.mergeNewRequests(data.requests)
          }
        }
      } catch (error) {
        console.error('Polling xatosi:', error)
      }
    },

    mergeNewRequests(serverRequests) {
      const currentIds = new Set(this.reqs.map(r => r.id))
      const newRequests = []

      serverRequests.forEach(serverReq => {
        const existingReq = this.reqs.find(r => r.id === serverReq.id)

        if (!existingReq) {

          newRequests.push({
            ...serverReq,
            likedBy: this.loadLikedByForRequest(serverReq.id) || []
          })
        } else {
          existingReq.likes = serverReq.likes || 0
          existingReq.category = serverReq.category
          existingReq.text = serverReq.text
          existingReq.region = serverReq.region
          existingReq.date = serverReq.date
        }
      })

      if (newRequests.length > 0) {
        this.reqs = [...newRequests, ...this.reqs]
        this.saveToLocalStorage()
      }
    },

    loadLikedByForRequest(requestId) {
      const likesData = JSON.parse(localStorage.getItem('xv_likes') || '{}')
      return likesData[requestId] || []
    },

    saveLikedByForRequest(requestId, likedBy) {
      const likesData = JSON.parse(localStorage.getItem('xv_likes') || '{}')
      likesData[requestId] = likedBy
      localStorage.setItem('xv_likes', JSON.stringify(likesData))
    },

    loadFromLocalStorage() {
      try {
        const stored = localStorage.getItem('xv_reqs')
        if (stored) {
          this.reqs = JSON.parse(stored)

          this.reqs.forEach(req => {
            if (!req.likedBy) {
              req.likedBy = this.loadLikedByForRequest(req.id)
            }
          })
        }
      } catch (e) {
        console.error('Load error:', e)
        this.reqs = []
      }
    },

    saveToLocalStorage() {
      try {
        localStorage.setItem('xv_reqs', JSON.stringify(this.reqs))

        this.reqs.forEach(req => {
          if (req.likedBy && req.likedBy.length > 0) {
            this.saveLikedByForRequest(req.id, req.likedBy)
          }
        })
      } catch (e) {
        console.error('Save error:', e)
      }
    },

    onScroll() {
      this.scrolled = window.scrollY > 50
      const el = document.documentElement
      this.scrollPct = (el.scrollTop / (el.scrollHeight - el.clientHeight)) * 100
    },

    goto(id) {
      const el = document.getElementById(id)
      if (el) {
        el.scrollIntoView({ behavior: 'smooth' })
        this.mOpen = false
      }
    },

    getNavIcon(id) {
      const icons = { hero: '🏠', about: '💡', submit: '✍️', requests: '📋' }
      return icons[id] || '•'
    },

    toggleTheme() {
      this.theme = this.theme === 'dark' ? 'light' : 'dark'
    },

    toggleLangMenu() {
      this.langMenuOpen = !this.langMenuOpen
    },

    selectLang(code) {
      this.cur = code
      this.langMenuOpen = false
    },

    getCurrentFlag() {
      const lang = this.langs.find(l => l.code === this.cur)
      return lang ? lang.flag : '🌐'
    },

    handleClickOutside(event) {
      if (this.$refs.langDropdown && !this.$refs.langDropdown.contains(event.target)) {
        this.langMenuOpen = false
      }
    },

    checkAdminLogin() {
      if (this.loginEmail.toLowerCase().trim() === this.adminEmail.toLowerCase() &&
        this.loginPassword.trim() === this.adminPassword) {
        this.isAdmin = true
        localStorage.setItem('xv_admin', 'true')
        this.showEmailLogin = false
        this.showToast('Admin sifatida kirdingiz!', false)
        this.loginEmail = ''
        this.loginPassword = ''
      } else {
        this.showToast('Email yoki parol noto\'g\'ri!', true)
      }
    },

    logout() {
      this.isAdmin = false
      localStorage.setItem('xv_admin', 'false')
      this.showAdminPanel = false
      this.showToast('Tizimdan chiqdingiz!', false)
    },

    async deleteRequest(id) {
      if (confirm('Bu taklifni o\'chirmoqchimisiz?')) {
        try {
          const scriptURL = 'https://script.google.com/macros/s/AKfycbw942zwfIeumx4oYQ6DwWXj8VcM_5yBervTdn_C-mTzzKE8Ughlie-cLYXBUZMd0d7p/exec'

          const formData = new URLSearchParams()
          formData.append('action', 'delete')
          formData.append('id', id)

          await fetch(scriptURL, {
            method: 'POST',
            mode: 'no-cors',
            headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
            body: formData.toString()
          })

          this.reqs = this.reqs.filter(r => r.id !== id)
          this.showToast('Taklif o\'chirildi!', false)
        } catch (e) {
          console.error('Delete error:', e)
          this.showToast('O\'chirishda xatolik!', true)
        }
      }
    },

    isTrending(r) {
      const dayAgo = Date.now() - 24 * 60 * 60 * 1000
      const isRecent = new Date(r.date).getTime() > dayAgo
      const hasLikes = (r.likes || 0) >= 5
      return isRecent && hasLikes
    },

    loadMore() {
      this.displayLimit += 12
    },

    initCanvas() {
      const c = this.$refs.canvas
      if (!c) return
      c.width = window.innerWidth
      c.height = window.innerHeight

      this.particles = Array.from({ length: 60 }, () => ({
        x: Math.random() * c.width,
        y: Math.random() * c.height,
        vx: (Math.random() - 0.5) * 0.3,
        vy: (Math.random() - 0.5) * 0.3,
        r: Math.random() * 1.5 + 0.5,
        a: Math.random() * 0.5 + 0.2
      }))

      this.drawLoop()
    },

    drawLoop() {
      const c = this.$refs.canvas
      if (!c) return

      const ctx = c.getContext('2d')
      ctx.clearRect(0, 0, c.width, c.height)

      const dk = this.theme === 'dark'
      const col = dk ? '170,190,255' : '70,70,150'

      this.particles.forEach(p => {
        p.x += p.vx
        p.y += p.vy

        if (p.x < 0) p.x = c.width
        if (p.x > c.width) p.x = 0
        if (p.y < 0) p.y = c.height
        if (p.y > c.height) p.y = 0

        ctx.beginPath()
        ctx.arc(p.x, p.y, p.r, 0, Math.PI * 2)
        ctx.fillStyle = `rgba(${col},${p.a})`
        ctx.fill()
      })

      for (let i = 0; i < this.particles.length; i++) {
        for (let j = i + 1; j < this.particles.length; j++) {
          const dx = this.particles[i].x - this.particles[j].x
          const dy = this.particles[i].y - this.particles[j].y
          const d = Math.sqrt(dx * dx + dy * dy)

          if (d < 100) {
            ctx.beginPath()
            ctx.moveTo(this.particles[i].x, this.particles[i].y)
            ctx.lineTo(this.particles[j].x, this.particles[j].y)
            ctx.strokeStyle = `rgba(${col},${(1 - d / 100) * 0.15})`
            ctx.lineWidth = 0.8
            ctx.stroke()
          }
        }
      }

      this.animId = requestAnimationFrame(this.drawLoop)
    },

    observe() {
      const io = new IntersectionObserver(entries => {
        entries.forEach(e => {
          if (e.isIntersecting) {
            const d = parseInt(e.target.style.getPropertyValue('--di') || '0')
            setTimeout(() => e.target.classList.add('vis'), d * 80)
          }
        })
      }, { threshold: 0.1 })

      document.querySelectorAll('.reveal').forEach(el => io.observe(el))
    },

    animateCounters() {
      setTimeout(() => {
        document.querySelectorAll('.count-up').forEach(el => {
          const target = parseInt(el.getAttribute('data-target'))
          let current = 0
          const increment = target / 50
          const timer = setInterval(() => {
            current += increment
            if (current >= target) {
              el.textContent = target
              clearInterval(timer)
            } else {
              el.textContent = Math.floor(current)
            }
          }, 30)
        })
      }, 1000)
    },

    updateStats() {
      this.animateCounters()
    },

    selectCategory(c) {
      this.nw.cat = c
      this.err.cat = ''
    },

    updateCharCount() {
      if (this.err.txt && this.nw.txt.trim().length >= 10) {
        this.err.txt = ''
      }
    },

    validate() {
      this.err = { cat: '', txt: '', reg: '' }
      let ok = true

      if (!this.nw.cat) {
        this.err.cat = this.t.v.cat
        ok = false
      }
      if (!this.nw.txt.trim()) {
        this.err.txt = this.t.v.txt
        ok = false
      } else if (this.nw.txt.trim().length < 10) {
        this.err.txt = this.t.v.txtM
        ok = false
      }
      if (!this.nw.reg) {
        this.err.reg = this.t.v.reg
        ok = false
      }

      return ok
    },

    async sendTelegramNotification(text, category, region) {
      const botToken = '7743930999:AAHpsCFzDCeqyrmsFqFd8eyP18ks6SPgWV4'
      const chatId = '8585002118'

      const message = `🆕 Yangi taklif!\n\n📂 Kategoriya: ${category}\n📍 Viloyat: ${region}\n✍️ Taklif: ${text}\n\n⏰ Vaqt: ${new Date().toLocaleString('uz-UZ')}`

      try {
        await fetch(`https://api.telegram.org/bot${botToken}/sendMessage`, {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({
            chat_id: chatId,
            text: message,
            parse_mode: 'HTML'
          })
        })
      } catch (e) {
        console.error('Telegram xabar yuborishda xatolik:', e)
      }
    },

    async send() {
      if (!this.validate()) return;

      this.sending = true;

      const scriptURL = 'https://script.google.com/macros/s/AKfycbw942zwfIeumx4oYQ6DwWXj8VcM_5yBervTdn_C-mTzzKE8Ughlie-cLYXBUZMd0d7p/exec';

      const newReqId = Date.now()
      const formData = new URLSearchParams();
      formData.append('id', newReqId)
      formData.append('vaqt', new Date().toISOString());
      formData.append('kategoriya', this.nw.cat);
      formData.append('taklif', this.nw.txt);
      formData.append('viloyat', this.nw.reg);
      formData.append('likes', 0);

      try {
        await fetch(scriptURL, {
          method: 'POST',
          mode: 'no-cors',
          headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
          body: formData.toString()
        });

        const newReq = {
          id: newReqId,
          category: this.nw.cat,
          text: this.nw.txt,
          region: this.nw.reg,
          date: new Date().toISOString(),
          likes: 0,
          likedBy: []
        };

        this.reqs = [newReq, ...this.reqs];

        await this.sendTelegramNotification(this.nw.txt, this.nw.cat, this.nw.reg);

        this.nw = { cat: '', txt: '', reg: '' };
        this.showToast(this.t.toast.ok, false);

        setTimeout(() => this.goto('requests'), 800);

      } catch (e) {
        console.error('Xatolik:', e);
        this.showToast(this.t.toast.bad, true);
      } finally {
        this.sending = false;
      }
    },

    liked(r) {
      return (r.likedBy || []).includes(this.uid)
    },

    async like(r) {
      if (!r.likedBy) r.likedBy = []
      if (!r.likes) r.likes = 0

      const wasLiked = r.likedBy.includes(this.uid)
      const i = r.likedBy.indexOf(this.uid)

      if (i > -1) {
        r.likedBy.splice(i, 1)
        r.likes--
      } else {
        r.likedBy.push(this.uid)
        r.likes++

        this.justLiked = r.id
        setTimeout(() => { this.justLiked = null }, 600)
      }

      this.reqs = [...this.reqs]
      this.saveToLocalStorage()

      try {
        const scriptURL = 'https://script.google.com/macros/s/AKfycby1OC95PuU3r2oUhs2eSpgm0c2SbsSOuFiM5gTMYCeaqEqzaF5UN6f_gxavcKlrPXoG/exec'

        const formData = new URLSearchParams()
        formData.append('action', 'updateLikes')
        formData.append('id', r.id)
        formData.append('likes', r.likes)

        await fetch(scriptURL, {
          method: 'POST',
          mode: 'no-cors',
          headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
          body: formData.toString()
        })
      } catch (e) {
        console.error('Like update error:', e)
      }
    },

    isNew(r) {
      const hourAgo = Date.now() - 60 * 60 * 1000
      return new Date(r.date).getTime() > hourAgo
    },

    showToast(msg, isErr) {
      this.toastMsg = msg
      this.toastErr = isErr
      this.toastOn = true

      setTimeout(() => {
        this.toastOn = false
      }, 4000)
    },

    fmtDate(ds) {
      const d = new Date(ds)
      const diff = Math.floor((Date.now() - d) / 86400000)

      if (diff === 0) return this.cur === 'uz' ? 'Bugun' : this.cur === 'ru' ? 'Сегодня' : 'Today'
      if (diff === 1) return this.cur === 'uz' ? 'Kecha' : this.cur === 'ru' ? 'Вчера' : 'Yesterday'
      if (diff < 7) return `${diff} ${this.cur === 'uz' ? 'kun oldin' : this.cur === 'ru' ? 'дн. назад' : 'days ago'}`

      return d.toLocaleDateString(this.cur === 'uz' ? 'uz-UZ' : this.cur === 'ru' ? 'ru-RU' : 'en-US')
    }
  }
}
</script>

<style>
@import "@/assets/app.css";
</style>