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
                <stop offset="0%" stop-color="#a78bfa"/>
                <stop offset="50%" stop-color="#e879f9"/>
                <stop offset="100%" stop-color="#fb923c"/>
              </linearGradient>
              <filter id="glow">
                <feGaussianBlur stdDeviation="2" result="coloredBlur"/>
                <feMerge>
                  <feMergeNode in="coloredBlur"/>
                  <feMergeNode in="SourceGraphic"/>
                </feMerge>
              </filter>
            </defs>
            <polygon points="16,2 30,10 30,22 16,30 2,22 2,10" stroke="url(#lg1)" stroke-width="2" fill="none" opacity="0.8" filter="url(#glow)"/>
            <polygon points="16,8 24,13 24,19 16,24 8,19 8,13" stroke="url(#lg1)" stroke-width="1.2" fill="rgba(167,139,250,0.12)"/>
            <circle cx="16" cy="16" r="3" fill="url(#lg1)" filter="url(#glow)">
              <animate attributeName="r" values="3;3.5;3" dur="2s" repeatCount="indefinite"/>
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
                <path d="M6 9l6 6 6-6" stroke="currentColor" stroke-width="2.5" stroke-linecap="round"/>
              </svg>
            </button>
            <transition name="lang-dropdown">
              <div class="lang-dropdown" v-if="langMenuOpen">
                <button v-for="lg in langs" :key="lg.code" class="lang-option" :class="{ active: cur === lg.code }" @click="selectLang(lg.code)">
                  <span class="lang-flag">{{ lg.flag }}</span>
                  <span class="lang-name">{{ lg.name }}</span>
                </button>
              </div>
            </transition>
          </div>

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
        <a v-for="l in navItems" :key="l.id" class="mlink" @click="goto(l.id); mOpen=false">
          <span class="mlink-icon">{{ getNavIcon(l.id) }}</span>
          {{ l.label }}
        </a>
      </div>
    </nav>

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
            <svg width="18" height="18" viewBox="0 0 24 24" fill="none"><path d="M12 5v14M5 12l7 7 7-7" stroke="#fff" stroke-width="2.5" stroke-linecap="round"/></svg>
            {{ t.hero.cta }}
          </button>
          <button class="btn btn-out" @click="goto('requests')">
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none"><path d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" stroke="currentColor" stroke-width="2" stroke-linecap="round"/></svg>
            {{ t.hero.viewAll }}
          </button>
        </div>

        <div class="stats anim-in" style="--d:0.75s">
          <div class="stat" v-for="(s,i) in heroStats" :key="i">
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
          <div v-for="(f,i) in t.why.list" :key="i" class="fcard reveal" :style="{ '--di': i }">
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
              <textarea class="ta" v-model="nw.txt" :placeholder="t.sub.ph" rows="5" @input="updateCharCount" maxlength="500"></textarea>
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
              <svg class="sel-arr" viewBox="0 0 24 24" width="18" height="18"><path d="M6 9l6 6 6-6" stroke="currentColor" stroke-width="2.5" fill="none" stroke-linecap="round"/></svg>
            </div>
            <transition name="err-slide">
              <span class="ferr" v-if="err.reg">⚠️ {{ err.reg }}</span>
            </transition>
          </div>

          <button class="btn btn-pri btn-full" :disabled="sending" @click="send">
            <span class="btn-shine"></span>
            <transition name="fade" mode="out-in">
              <span v-if="!sending" key="submit">
                <svg width="18" height="18" viewBox="0 0 24 24" fill="none"><path d="M22 2L11 13M22 2l-7 20-4-9-9-4 20-7z" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/></svg>
                {{ t.sub.btn }}
              </span>
              <span v-else key="loading" class="ldtxt">
                <svg class="spinner" viewBox="0 0 24 24" width="18" height="18"><circle cx="12" cy="12" r="10" stroke="currentColor" stroke-width="3" fill="none" stroke-dasharray="31.4 31.4" stroke-linecap="round"/></svg>
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

        <div class="pills reveal">
          <button class="pill" :class="{ on: actFilt === 'all' }" @click="actFilt='all'">
            <span class="pill-count">{{ reqs.length }}</span>
            {{ t.req.all }}
          </button>
          <button v-for="c in cats" :key="c" class="pill" :class="{ on: actFilt === c }" @click="actFilt = c">
            <span class="pill-count">{{ reqs.filter(r => r.category === c).length }}</span>
            {{ c }}
          </button>
        </div>

        <div class="sortlabel reveal">
          <svg width="14" height="14" viewBox="0 0 24 24" fill="none"><path d="M7 16V4M7 16l-3-3M7 16l3-3M17 8v12M17 8l3 3M17 8l-3 3" stroke="currentColor" stroke-width="2" stroke-linecap="round"/></svg>
          {{ t.req.sorted }}
          <span class="live-indicator">
            <span class="live-dot"></span>
            LIVE
          </span>
        </div>

        <transition-group name="card-list" tag="div" class="req-grid">
          <div v-for="(r, idx) in sorted" :key="r.id" class="rcard reveal vis" :style="{ '--di': idx }" @mousemove="tilt($event)" @mouseleave="untilt($event)">
            <div class="rcard-shine"></div>
            <div class="rcard-glow"></div>
            <div class="rcard-new" v-if="isNew(r)">🆕</div>
            
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
                    <path d="M20.84 4.61a5.5 5.5 0 0 0-7.78 0L12 5.67l-1.06-1.06a5.5 5.5 0 0 0-7.78 7.78l1.06 1.06L12 21.23l7.78-7.78 1.06-1.06a5.5 5.5 0 0 0 0-7.78z" 
                    :fill="liked(r)?'currentColor':'none'" 
                    stroke="currentColor" 
                    stroke-width="2" 
                    stroke-linecap="round" 
                    stroke-linejoin="round"/>
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
            <p>{{ t.req.empty }}</p>
          </div>
        </transition>
      </div>
    </section>

    <footer class="footer">
      <div class="ft-wrap">
        <div class="ft-brand">
          <div class="logo" style="margin-bottom:1rem" @click="goto('hero')">
            <svg class="logo-svg" viewBox="0 0 32 32" fill="none">
              <polygon points="16,2 30,10 30,22 16,30 2,22 2,10" stroke="url(#lg1)" stroke-width="2" fill="none" opacity="0.8"/>
              <circle cx="16" cy="16" r="3" fill="url(#lg1)"/>
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
              <path d="M20 6L9 17l-5-5" stroke="#fff" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"/>
            </svg>
            <svg v-else key="error" width="20" height="20" viewBox="0 0 24 24" fill="none">
              <path d="M18 6L6 18M6 6l12 12" stroke="#fff" stroke-width="3" stroke-linecap="round"/>
            </svg>
          </transition>
        </div>
        <div class="toast-body">
          <span class="toast-title">{{ toastErr ? (cur==='uz'?'Xatolik':cur==='ru'?'Ошибка':'Error') : (cur==='uz'?'Muvaffaqiyat':cur==='ru'?'Успех':'Success') }}</span>
          <span class="toast-txt">{{ toastMsg }}</span>
        </div>
        <button class="toast-close" @click="toastOn = false">✕</button>
      </div>
    </transition>

    <transition name="scale-fade">
      <button class="sctop" v-if="scrolled" @click="goto('hero')">
        <svg width="20" height="20" viewBox="0 0 24 24" fill="none">
          <path d="M12 19V5M5 12l7-7 7 7" stroke="currentColor" stroke-width="2.5" stroke-linecap="round"/>
        </svg>
      </button>
    </transition>

  </div>
</template>
























  <script>
  export default {
    name: 'App',
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

        langs: [
          { code: 'uz', name: "O'zbek", flag: '🇺🇿' },
          { code: 'ru', name: 'Русский', flag: '🇷🇺' },
          { code: 'en', name: 'English', flag: '🇬🇧' }
        ],
        cats: ["Ta'lim","Sog'liqni Saqlash","Yo'llar","Ekologiya","Sport","Madaniyat","Iqtisodiyot","Boshqa"],
        regs: ['Toshkent sh.','Toshkent vil.','Andijon vil.','Buxoro vil.',"Farg'ona vil.",'Jizzax vil.','Xorazm vil.','Namangan vil.','Navoiy vil.','Qashqadaryo vil.','Qoraqalpog\'iston Res.','Samarqand vil.','Sirdaryo vil.','Surxondaryo vil.'],

        i18n: {
          uz: {
            brand:'Xalq Ovozi',
            hero:{ badge:'Yangi platforma', line1:'Ovozingiz', line2:'Muhim!', desc:"Jamiyat uchun muhim bo'lgan takliflaringizni yuboring. Eng ko'p ovoz to'plagan takliflar amalga oshiriladi.", cta:'Talab Yuborish', viewAll:"Talablarni Ko'rish" },
            why:{ tag:'Nega biz', title:"Xalq Ovozi — Haqiqiy O'zgartirish", sub:"Biz xalq fikrini eshitamiz va eng muhim takliflarni amalga oshiramiz", list:[
              { ic:'🗳️', title:'Demokratik Ovoz', desc:"Har bir fuqaro o'z fikrini bildirib, jamiyat rivojga hissa qo'shadi." },
              { ic:'👁️', title:'Shaffoflik', desc:"Barcha takliflar ochiq. Ko'p ovoz to'plagan takliflar birinchi amalga oshiriladi." },
              { ic:'⚡', title:'Tezkor Javob', desc:"Eng dolzarb muammolarni tez va samarali hal qilish uchun darhol ko'rib chiqamiz." },
              { ic:'📍', title:"Viloyatlar Bo'yicha", desc:"Har viloyatning o'ziga xos muammolarini alohida e'tiborga olamiz." },
              { ic:'🤝', title:'Hamjamiyat', desc:"Birgalikda biz kuchliymiz. Jamoa bo'lib yurtimizni go'zallashtramiz." },
              { ic:'🌱', title:'Barqaror Rivojlanish', desc:'Uzoq muddatli va barqaror yechimlarni topishga intilamiz.' }
            ]},
            sub:{ tag:'Taklad', title:'Taklifingiz Yuboring', sub:"O'z fikr-mulohazalaringizni bildiring", catLbl:'Kategoriya', txtLbl:'Taklifingiz', ph:'Taklifingizni batafsil yozing...', regLbl:'Viloyat', selReg:'Viloyatni tanlang', btn:'Yuborish', ld:'Yuklanmoqda', discl:"Sizning talabingiz ko'rib chiqiladi va eng ko'p ovoz to'plagan takliflar amalga oshiriladi." },
            req:{ tag:'Talablar', title:'Barcha Talablar', sub:"Ovoz bering va eng muhim takliflarni qo'llab-quvvatlang", all:'Hammasi', sorted:"Eng ko'p ovoz bolan oldinda", empty:"Hozircha talablar yo'q" },
            ft:{ desc:"Xalq uchun, xalq tomonidan — birgalikda yaxshiroq kelajak quramiz.", links:'Tezkor Havolalar', rights:'Barcha huquqlar himoyalangan.' },
            toast:{ ok:'Taklifingiz muvaffaqiyatli yuborildi!', bad:"Xatolik yuz berdi. Qaytadan urinib ko'ring." },
            v:{ cat:'Kategoriyani tanlang', txt:'Taklifingizni yozing', txtM:'Kamida 10 belgi yozing', reg:'Viloyatni tanlang' }
          },
          ru: {
            brand:'Голос Народа',
            hero:{ badge:'Новая платформа', line1:'Ваш голос', line2:'Важен!', desc:'Отправьте предложения, важные для общества. Предложения с наибольшим числом голосов будут реализованы.', cta:'Отправить Запрос', viewAll:'Смотреть Запросы' },
            why:{ tag:'Почему мы', title:'Голос Народа — Реальные Перемены', sub:'Мы слушаем мнение людей и реализуем самые важные предложения', list:[
              { ic:'🗳️', title:'Демократический Голос', desc:'Каждый гражданин может высказать свое мнение и внести вклад.' },
              { ic:'👁️', title:'Прозрачность', desc:'Все предложения открыты. С наибольшим числом голосов реализуются первыми.' },
              { ic:'⚡', title:'Быстрый Ответ', desc:'Немедленно рассматриваем для быстрого решения актуальных проблем.' },
              { ic:'📍', title:'По Регионам', desc:'Особое внимание специфическим проблемам каждого региона.' },
              { ic:'🤝', title:'Сообщество', desc:'Вместе мы сильнее. Командой делаем страну прекраснее.' },
              { ic:'🌱', title:'Устойчивое Развитие', desc:'Долгосрочные и устойчивые решения, а не временные.' }
            ]},
            sub:{ tag:'Запрос', title:'Отправьте Предложение', sub:'Выскажите свое мнение', catLbl:'Категория', txtLbl:'Ваше предложение', ph:'Опишите предложение подробно...', regLbl:'Область', selReg:'Выберите область', btn:'Отправить', ld:'Загрузка', discl:'Ваш запрос будет рассмотрен и предложения с наибольшим числом голосов реализованы.' },
            req:{ tag:'Запросы', title:'Все Запросы', sub:'Голосуйте и поддерживайте важные предложения', all:'Все', sorted:'Сортировка по голосам', empty:'Пока нет запросов' },
            ft:{ desc:'Для народа, от народа — вместе строим лучшее будущее.', links:'Быстрые Ссылки', rights:'Все права защищены.' },
            toast:{ ok:'Успешно отправлено!', bad:'Произошла ошибка. Попробуйте снова.' },
            v:{ cat:'Выберите категорию', txt:'Напишите предложение', txtM:'Минимум 10 символов', reg:'Выберите область' }
          },
          en: {
            brand:"People's Voice",
            hero:{ badge:'New Platform', line1:'Your Voice', line2:'Matters!', desc:'Submit proposals important for society. Proposals with the most votes will be implemented.', cta:'Submit Request', viewAll:'View Requests' },
            why:{ tag:'Why Us', title:"People's Voice — Real Change", sub:'We listen to the people and implement the most important proposals', list:[
              { ic:'🗳️', title:'Democratic Voice', desc:'Every citizen can express opinion and contribute to society.' },
              { ic:'👁️', title:'Transparency', desc:'All proposals open. Most voted implemented first.' },
              { ic:'⚡', title:'Quick Response', desc:'We immediately review demands to solve pressing problems fast.' },
              { ic:'📍', title:'By Regions', desc:'Special attention to specific problems of each region.' },
              { ic:'🤝', title:'Community', desc:'Together we are stronger. As a team we make our country better.' },
              { ic:'🌱', title:'Sustainable Dev', desc:'Long-term and sustainable solutions, not temporary fixes.' }
            ]},
            sub:{ tag:'Submit', title:'Submit Your Proposal', sub:'Share your thoughts', catLbl:'Category', txtLbl:'Your Proposal', ph:'Describe your proposal in detail...', regLbl:'Region', selReg:'Select region', btn:'Submit', ld:'Loading', discl:'Your request will be reviewed. Proposals with most votes will be implemented.' },
            req:{ tag:'Requests', title:'All Requests', sub:'Vote and support the most important proposals', all:'All', sorted:'Sorted by most votes', empty:'No requests yet' },
            ft:{ desc:'For the people, by the people — building a better future together.', links:'Quick Links', rights:'All rights reserved.' },
            toast:{ ok:'Successfully submitted!', bad:'An error occurred. Try again.' },
            v:{ cat:'Select a category', txt:'Write your proposal', txtM:'At least 10 characters', reg:'Select a region' }
          }
        }
      }
    },

    computed: {
      t() { return this.i18n[this.cur] },
      navItems() {
        const h = { uz:['Bosh sahifa','Nega biz','Talab yuborish','Talablar'], ru:['Главная','Почему мы','Запрос','Запросы'], en:['Home','Why Us','Submit','Requests'] }
        return ['hero','about','submit','requests'].map((id,i) => ({ id, label: h[this.cur][i] }))
      },
      heroStats() {
        const l = { uz:['Talablar','Ovozlar','Kategoriyalar'], ru:['Запросов','Голосов','Категории'], en:['Requests','Votes','Categories'] }
        return [
          { val: this.reqs.length, lbl: l[this.cur][0] },
          { val: this.reqs.reduce((s,r)=>s+(r.likes||0),0), lbl: l[this.cur][1] },
          { val: this.cats.length, lbl: l[this.cur][2] }
        ]
      },
      sorted() {
        let list = [...this.reqs]
        if (this.actFilt !== 'all') list = list.filter(r => r.category === this.actFilt)
        return list.sort((a,b) => (b.likes||0) - (a.likes||0))
      }
    },

    watch: {
      theme(v) { localStorage.setItem('xv_theme', v) },
      cur(v) { localStorage.setItem('xv_lang', v) },
      reqs: { 
        deep: true, 
        handler(v) { 
          localStorage.setItem('xv_reqs', JSON.stringify(v))
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
      try { this.reqs = JSON.parse(localStorage.getItem('xv_reqs') || '[]') } catch(e) {}
      
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
    },

    methods: {
      mkUid() { 
        const id='u_'+Date.now().toString(36)+Math.random().toString(36).slice(2,8)
        localStorage.setItem('xv_uid',id)
        return id 
      },

      onScroll() {
        this.scrolled = window.scrollY > 50
        const el = document.documentElement
        this.scrollPct = (el.scrollTop / (el.scrollHeight - el.clientHeight)) * 100
      },

      goto(id) { 
        const el = document.getElementById(id)
        if(el) {
          el.scrollIntoView({ behavior:'smooth' })
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

      initCanvas() {
        const c = this.$refs.canvas
        if(!c) return
        c.width = window.innerWidth
        c.height = window.innerHeight
        
        this.particles = Array.from({ length: 120 }, () => ({
          x: Math.random() * c.width,
          y: Math.random() * c.height,
          vx: (Math.random() - 0.5) * 0.5,
          vy: (Math.random() - 0.5) * 0.5,
          r: Math.random() * 2 + 0.5,
          a: Math.random() * 0.6 + 0.2
        }))
        
        this.drawLoop()
      },

      drawLoop() {
        const c = this.$refs.canvas
        if(!c) return
        
        const ctx = c.getContext('2d')
        ctx.clearRect(0, 0, c.width, c.height)
        
        const dk = this.theme === 'dark'
        const col = dk ? '170,190,255' : '70,70,150'

        this.particles.forEach(p => {
          p.x += p.vx
          p.y += p.vy
          
          if(p.x < 0) p.x = c.width
          if(p.x > c.width) p.x = 0
          if(p.y < 0) p.y = c.height
          if(p.y > c.height) p.y = 0
          
          ctx.beginPath()
          ctx.arc(p.x, p.y, p.r, 0, Math.PI * 2)
          ctx.fillStyle = `rgba(${col},${p.a})`
          ctx.fill()
        })

        for(let i = 0; i < this.particles.length; i++) {
          for(let j = i + 1; j < this.particles.length; j++) {
            const dx = this.particles[i].x - this.particles[j].x
            const dy = this.particles[i].y - this.particles[j].y
            const d = Math.sqrt(dx * dx + dy * dy)
            
            if(d < 120) {
              ctx.beginPath()
              ctx.moveTo(this.particles[i].x, this.particles[i].y)
              ctx.lineTo(this.particles[j].x, this.particles[j].y)
              ctx.strokeStyle = `rgba(${col},${(1 - d / 120) * 0.2})`
              ctx.lineWidth = 1
              ctx.stroke()
            }
          }
        }
        
        this.animId = requestAnimationFrame(this.drawLoop)
      },

      observe() {
        const io = new IntersectionObserver(entries => {
          entries.forEach(e => {
            if(e.isIntersecting) {
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
              if(current >= target) {
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

      tilt(e) {
        const card = e.currentTarget
        const r = card.getBoundingClientRect()
        const rx = ((r.height / 2 - (e.clientY - r.top)) / r.height) * 8
        const ry = (((e.clientX - r.left) - r.width / 2) / r.width) * 8
        card.style.transform = `perspective(900px) rotateX(${rx}deg) rotateY(${ry}deg) scale(1.03)`
      },

      untilt(e) { 
        e.currentTarget.style.transform = '' 
      },

      selectCategory(c) {
        this.nw.cat = c
        this.err.cat = ''
      },

      updateCharCount() {
        if(this.err.txt && this.nw.txt.trim().length >= 10) {
          this.err.txt = ''
        }
      },

      validate() {
        this.err = { cat: '', txt: '', reg: '' }
        let ok = true
        
        if(!this.nw.cat) { 
          this.err.cat = this.t.v.cat
          ok = false 
        }
        if(!this.nw.txt.trim()) { 
          this.err.txt = this.t.v.txt
          ok = false 
        } else if(this.nw.txt.trim().length < 10) { 
          this.err.txt = this.t.v.txtM
          ok = false 
        }
        if(!this.nw.reg) { 
          this.err.reg = this.t.v.reg
          ok = false 
        }
        
        return ok
      },

async send() {
  if(!this.validate()) return;
  
  this.sending = true;
  
  const scriptURL = 'https://script.google.com/macros/s/AKfycbw942zwfIeumx4oYQ6DwWXj8VcM_5yBervTdn_C-mTzzKE8Ughlie-cLYXBUZMd0d7p/exec';

  const formData = new URLSearchParams();
  formData.append('vaqt', new Date().toLocaleString('uz-UZ'));
  formData.append('kategoriya', this.nw.cat);             
  formData.append('taklif', this.nw.txt);                  
  formData.append('viloyat', this.nw.reg);                   

  try {
    await fetch(scriptURL, {
      method: 'POST',
      mode: 'no-cors',
      headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
      body: formData.toString()
    });

    const newReq = {
      id: Date.now(),
      category: this.nw.cat,
      text: this.nw.txt,
      region: this.nw.reg,
      date: new Date().toISOString(),
      likes: 0,
      likedBy: []
    };
    this.reqs = [newReq, ...this.reqs];

    this.nw = { cat: '', txt: '', reg: '' };
    this.showToast(this.t.toast.ok, false);
    
    setTimeout(() => this.goto('requests'), 800);

  } catch(e) { 
    console.error('Xatolik:', e);
    this.showToast(this.t.toast.bad, true); 
  } finally {
    this.sending = false;
  }
},
      liked(r) { 
        return (r.likedBy || []).includes(this.uid) 
      },

      like(r) {
        if(!r.likedBy) r.likedBy = []
        if(!r.likes) r.likes = 0
        
        const i = r.likedBy.indexOf(this.uid)
        if(i > -1) {
          r.likedBy.splice(i, 1)
          r.likes--
        } else {
          r.likedBy.push(this.uid)
          r.likes++
          
          this.justLiked = r.id
          setTimeout(() => { this.justLiked = null }, 600)
        }
        
        this.reqs = [...this.reqs]
      },

      isNew(r) {
        return this.newRequestIds.includes(r.id)
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
        
        if(diff === 0) return this.cur === 'uz' ? 'Bugun' : this.cur === 'ru' ? 'Сегодня' : 'Today'
        if(diff === 1) return this.cur === 'uz' ? 'Kecha' : this.cur === 'ru' ? 'Вчера' : 'Yesterday'
        if(diff < 7) return `${diff} ${this.cur === 'uz' ? 'kun oldin' : this.cur === 'ru' ? 'дн. назад' : 'days ago'}`
        
        return d.toLocaleDateString(this.cur === 'uz' ? 'uz-UZ' : this.cur === 'ru' ? 'ru-RU' : 'en-US')
      }
    }
  }
  </script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800;900&display=swap');

*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
button{font-family:inherit;cursor:pointer;border:none;outline:none}

.app.dark{--bg:#000;--bg2:#0a0a0f;--card:rgba(18,18,28,0.85);--card-s:#12121c;--txt:#f5f5f7;--txt2:#a0a0b0;--txt3:#606070;--brd:rgba(255,255,255,0.08);--acc:#a78bfa;--acc2:#e879f9;--acc3:#fb923c;--glow:rgba(167,139,250,0.45);--glow2:rgba(232,121,249,0.35);--shadow:0 20px 60px rgba(0,0,0,0.6)}
.app.light{--bg:#f8f9fa;--bg2:#e9ecef;--card:rgba(255,255,255,0.8);--card-s:#fff;--txt:#1a1a2e;--txt2:#5a5a70;--txt3:#8888a0;--brd:rgba(0,0,0,0.08);--acc:#6366f1;--acc2:#a855f7;--acc3:#ea580c;--glow:rgba(99,102,241,0.35);--glow2:rgba(168,85,247,0.25);--shadow:0 20px 60px rgba(0,0,0,0.12)}

.app{min-height:100vh;background:var(--bg);color:var(--txt);font-family:'Inter',-apple-system,BlinkMacSystemFont,'Segoe UI',system-ui,sans-serif;overflow-x:hidden;position:relative;transition:background .5s ease,color .5s ease}

.noise-overlay{position:fixed;inset:0;z-index:1;pointer-events:none;background-image:url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E");background-size:160px 160px}
.app.dark .noise-overlay{opacity:.045}
.app.light .noise-overlay{opacity:.025}

.ambient{position:fixed;inset:0;z-index:0;pointer-events:none;overflow:hidden}
.orb{position:absolute;border-radius:50%;filter:blur(120px);animation:driftOrb 25s ease-in-out infinite alternate}
.app.dark .orb{opacity:.5}
.app.light .orb{opacity:.2}

.orb-1{width:700px;height:700px;background:#7c3aed;top:-250px;left:-220px;animation-delay:0s}
.orb-2{width:550px;height:550px;background:#a855f7;top:25%;right:-200px;animation-delay:-6s}
.orb-3{width:600px;height:600px;background:#ea580c;bottom:-220px;left:12%;animation-delay:-12s}
.orb-4{width:420px;height:420px;background:#0ea5e9;bottom:20%;left:-130px;animation-delay:-16s}
.orb-5{width:350px;height:350px;background:#e879f9;top:10%;left:55%;animation-delay:-8s}
.orb-6{width:480px;height:480px;background:#8b5cf6;top:60%;right:10%;animation-delay:-20s}

@keyframes driftOrb{0%{transform:translate(0,0) scale(1)}100%{transform:translate(60px,-80px) scale(1.15)}}

.particle-canvas{position:absolute;inset:0;width:100%;height:100%}
.grid-bg{position:absolute;inset:0;background-image:linear-gradient(rgba(167,139,250,0.06) 1px,transparent 1px),linear-gradient(90deg,rgba(167,139,250,0.06) 1px,transparent 1px);background-size:60px 60px}
.app.light .grid-bg{opacity:.6}
.gradient-overlay{position:absolute;inset:0;background:radial-gradient(circle at 50% 0%,rgba(167,139,250,0.08),transparent 50%)}

.progress-bar{position:fixed;top:0;left:0;height:3px;z-index:9999;background:linear-gradient(90deg,var(--acc),var(--acc2),var(--acc3));transition:width .1s linear}
.progress-glow{position:absolute;right:-3px;top:-4px;width:12px;height:12px;border-radius:50%;background:var(--acc2);box-shadow:0 0 20px 8px var(--acc2)}
.progress-particles{position:absolute;right:0;top:0;width:100px;height:100%;background:linear-gradient(90deg,transparent,rgba(255,255,255,0.3));animation:particleFlow 2s linear infinite}
@keyframes particleFlow{0%{transform:translateX(-100px);opacity:0}50%{opacity:1}100%{transform:translateX(100px);opacity:0}}

.nav{position:fixed;top:0;left:0;right:0;z-index:8000;transition:all .4s cubic-bezier(0.4,0,0.2,1)}
.nav.scrolled{background:var(--card);backdrop-filter:blur(40px) saturate(180%);-webkit-backdrop-filter:blur(40px) saturate(180%);box-shadow:0 4px 40px rgba(0,0,0,0.25);border-bottom:1px solid var(--brd)}
.nav-wrap{max-width:1200px;margin:0 auto;display:flex;align-items:center;justify-content:space-between;height:72px;padding:0 2rem;gap:1.5rem}

.logo{display:flex;align-items:center;gap:.7rem;cursor:pointer;transition:transform .3s ease}
.logo:hover{transform:scale(1.05)}
.logo-svg{width:38px;height:38px}
.logo-txt{font-size:1.3rem;font-weight:900;background:linear-gradient(135deg,var(--acc),var(--acc2),var(--acc3));-webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text;letter-spacing:-.5px}

.nav-links{display:flex;gap:2rem}
.nlink{color:var(--txt2);font-size:.95rem;font-weight:600;position:relative;padding:.4rem 0;transition:color .3s ease;cursor:pointer}
.nlink:hover{color:var(--txt)}
.nlink-line{position:absolute;bottom:-2px;left:0;width:0;height:2px;background:linear-gradient(90deg,var(--acc),var(--acc3));border-radius:2px;transition:width .4s cubic-bezier(0.4,0,0.2,1)}
.nlink:hover .nlink-line{width:100%}

.nav-right{display:flex;align-items:center;gap:.8rem}

.mobile-lang-wrapper{position:relative;display:flex}
.mobile-lang-btn{display:flex;align-items:center;gap:.4rem;padding:.5rem .8rem;border-radius:12px;background:var(--card);border:1px solid var(--brd);color:var(--txt);font-size:1.1rem;transition:all .3s cubic-bezier(0.4,0,0.2,1)}
.mobile-lang-btn:hover{border-color:var(--acc);box-shadow:0 4px 16px var(--glow)}
.current-flag{font-size:1.2rem}
.lang-dropdown{position:absolute;top:calc(100% + .5rem);right:0;min-width:160px;background:var(--card);backdrop-filter:blur(40px);border:1px solid var(--brd);border-radius:16px;padding:.5rem;box-shadow:var(--shadow);z-index:9999}
.lang-option{display:flex;align-items:center;gap:.8rem;width:100%;padding:.7rem 1rem;border-radius:12px;background:transparent;color:var(--txt2);font-size:.9rem;font-weight:600;transition:all .25s cubic-bezier(0.4,0,0.2,1);text-align:left}
.lang-option:hover{background:var(--brd);color:var(--txt)}
.lang-option.active{background:linear-gradient(135deg,var(--acc),var(--acc2));color:#fff}
.lang-flag{font-size:1.3rem}
.lang-name{flex:1}

.lang-dropdown-enter-active,.lang-dropdown-leave-active{transition:all .3s cubic-bezier(0.4,0,0.2,1)}
.lang-dropdown-enter-from,.lang-dropdown-leave-to{opacity:0;transform:translateY(-10px) scale(0.95)}

.tbtn{width:44px;height:44px;border-radius:14px;background:var(--card);border:1px solid var(--brd);font-size:1.2rem;display:flex;align-items:center;justify-content:center;transition:all .3s cubic-bezier(0.4,0,0.2,1)}
.tbtn:hover{transform:scale(1.1) rotate(15deg);box-shadow:0 6px 24px var(--glow)}
.theme-icon{display:inline-block;transition:transform .3s ease}

.mbtn{display:none;width:44px;height:44px;border-radius:14px;background:var(--card);border:1px solid var(--brd);flex-direction:column;align-items:center;justify-content:center;gap:5px}
.hline{width:22px;height:2.5px;background:var(--txt);border-radius:2px;transition:all .3s cubic-bezier(0.4,0,0.2,1)}
.h1.open{transform:rotate(45deg) translate(6px,6px)}
.h2.open{opacity:0}
.h3.open{transform:rotate(-45deg) translate(6px,-6px)}

.mmenu{max-height:0;overflow:hidden;background:var(--card);backdrop-filter:blur(40px);border-top:1px solid var(--brd);transition:max-height .4s cubic-bezier(0.4,0,0.2,1)}
.mmenu.open{max-height:400px}
.mlink{display:flex;align-items:center;gap:.8rem;padding:1rem 2rem;color:var(--txt2);font-size:.95rem;font-weight:600;border-bottom:1px solid var(--brd);transition:all .25s ease;cursor:pointer}
.mlink:hover{color:var(--txt);background:var(--brd);padding-left:2.5rem}
.mlink-icon{font-size:1.2rem}

@media(max-width:900px){
  .nav-links{display:none}
  .mbtn{display:flex}
}

.hero{position:relative;z-index:1;min-height:100vh;display:flex;align-items:center;justify-content:center;padding:120px 2rem 100px;text-align:center}
.hero-inner{max-width:800px;width:100%}

.badge{display:inline-flex;align-items:center;gap:.6rem;padding:.45rem 1.2rem;border-radius:24px;background:var(--card);backdrop-filter:blur(20px);border:1px solid var(--brd);color:var(--acc);font-size:.85rem;font-weight:700;margin-bottom:2rem;box-shadow:0 4px 20px rgba(0,0,0,0.1)}
.badge-pulse{width:9px;height:9px;border-radius:50%;background:#22c55e;box-shadow:0 0 12px #22c55e;animation:pulse 2s infinite}
@keyframes pulse{0%,100%{opacity:1;transform:scale(1)}50%{opacity:.5;transform:scale(1.4)}}

.h1-top{display:block;font-size:clamp(2.8rem,7vw,5.5rem);font-weight:800;color:var(--txt);letter-spacing:-2.5px;line-height:1.05}
.h1-bot{display:block;font-size:clamp(3.5rem,9vw,8rem);font-weight:900;line-height:1.05;letter-spacing:-4px;background:linear-gradient(135deg,var(--acc) 0%,var(--acc2) 40%,var(--acc3) 100%);background-size:200% 200%;-webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text;animation:gradientShift 6s ease infinite;position:relative}
@keyframes gradientShift{0%{background-position:0% 50%}50%{background-position:100% 50%}100%{background-position:0% 50%}}

.cursor-blink{display:inline-block;width:4px;height:1em;background:var(--acc);margin-left:.1em;animation:blink 1s infinite}
@keyframes blink{0%,50%{opacity:1}51%,100%{opacity:0}}

.hero-p{color:var(--txt2);font-size:1.15rem;line-height:1.9;max-width:580px;margin:1.5rem auto 3rem}

.hero-btns{display:flex;gap:1rem;justify-content:center;flex-wrap:wrap;margin-bottom:4rem}
.btn{position:relative;overflow:hidden;padding:1rem 2.5rem;border-radius:16px;font-size:1rem;font-weight:700;transition:all .3s cubic-bezier(0.4,0,0.2,1);display:inline-flex;align-items:center;gap:.6rem}
.btn-pri{background:linear-gradient(135deg,var(--acc),var(--acc2));color:#fff;box-shadow:0 8px 28px var(--glow)}
.btn-pri .btn-shine{position:absolute;top:0;left:-100%;width:60%;height:100%;background:linear-gradient(120deg,transparent,rgba(255,255,255,0.25),transparent);transition:left .6s ease;pointer-events:none}
.btn-pri:hover .btn-shine{left:150%}
.btn-pri:hover{transform:translateY(-3px);box-shadow:0 16px 48px var(--glow)}
.btn-out{background:var(--card);backdrop-filter:blur(16px);border:2px solid var(--brd);color:var(--txt)}
.btn-out:hover{border-color:var(--acc);box-shadow:0 12px 36px var(--glow);transform:translateY(-3px)}
.btn-full{width:100%;justify-content:center;margin-top:.8rem}
.btn-full:disabled{opacity:.5;transform:none!important;box-shadow:none!important;cursor:not-allowed}

.spinner{animation:spin 1s linear infinite}
@keyframes spin{to{transform:rotate(360deg)}}

.stats{display:flex;gap:3.5rem;justify-content:center;flex-wrap:wrap}
.stat{text-align:center}
.stat-n{font-size:clamp(2.2rem,5vw,3.2rem);font-weight:900;background:linear-gradient(135deg,var(--acc),var(--acc3));-webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text;line-height:1}
.stat-l{font-size:.8rem;font-weight:700;color:var(--txt3);letter-spacing:1.5px;text-transform:uppercase;margin-top:.5rem}

.hero-float{position:absolute;font-size:2.5rem;opacity:.3;animation:float 6s ease-in-out infinite;pointer-events:none}
.float-1{top:15%;left:10%;animation-delay:0s}
.float-2{top:25%;right:15%;animation-delay:2s}
.float-3{bottom:20%;left:12%;animation-delay:4s}
@keyframes float{0%,100%{transform:translateY(0) rotate(0deg)}50%{transform:translateY(-20px) rotate(10deg)}}

.anim-in{opacity:0;transform:translateY(30px);animation:slideUp .8s cubic-bezier(0.25,0.46,0.45,0.94) forwards;animation-delay:var(--d,0s)}
@keyframes slideUp{to{opacity:1;transform:translateY(0)}}

.sec{position:relative;z-index:1;padding:120px 2rem}
.sec-wrap{max-width:1200px;margin:0 auto}
.sec-head{text-align:center;margin-bottom:70px}

.tag{display:inline-block;padding:.35rem 1.1rem;border-radius:20px;background:var(--card);backdrop-filter:blur(12px);border:1px solid var(--brd);color:var(--acc);font-size:.8rem;font-weight:700;letter-spacing:1.6px;text-transform:uppercase;margin-bottom:1rem}
.sec-h2{font-size:clamp(2rem,5vw,3.2rem);font-weight:900;color:var(--txt);letter-spacing:-1.5px;line-height:1.2;margin-bottom:1rem}
.sec-p{color:var(--txt2);font-size:1.1rem;max-width:580px;margin:0 auto;line-height:1.8}

.reveal{opacity:0;transform:translateY(40px);transition:all .7s cubic-bezier(0.25,0.46,0.45,0.94);transition-delay:calc(var(--di,0) * 0.08s)}
.reveal.vis{opacity:1;transform:translateY(0)}

.sec-why{background:linear-gradient(180deg,transparent 0%,rgba(124,58,237,0.06) 50%,transparent 100%)}
.feat-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:1.5rem}
@media(max-width:900px){.feat-grid{grid-template-columns:repeat(2,1fr)}}
@media(max-width:600px){.feat-grid{grid-template-columns:1fr}}

.fcard{position:relative;overflow:hidden;background:var(--card);backdrop-filter:blur(30px);border:1px solid var(--brd);border-radius:24px;padding:2.5rem 2rem;transition:all .4s cubic-bezier(0.4,0,0.2,1);cursor:pointer}
.fcard:hover{box-shadow:var(--shadow);border-color:var(--acc);transform:translateY(-8px)}
.fcard-glow{position:absolute;top:-60px;left:50%;transform:translateX(-50%);width:180px;height:180px;border-radius:50%;background:var(--acc);filter:blur(70px);opacity:0;transition:opacity .5s ease;pointer-events:none}
.fcard:hover .fcard-glow{opacity:.3}
.fcard-border{position:absolute;inset:0;border-radius:24px;pointer-events:none;opacity:0;transition:opacity .5s ease;z-index:0}
.fcard:hover .fcard-border{opacity:1}
.fcard-border::before{content:'';position:absolute;inset:-1px;border-radius:25px;background:linear-gradient(135deg,transparent 30%,var(--acc),var(--acc2),transparent 70%);z-index:0}
.fcard-border::after{content:'';position:absolute;inset:1px;border-radius:23px;background:var(--card-s);z-index:1}
.fcard-sparkle{position:absolute;top:20px;right:20px;width:6px;height:6px;background:var(--acc);border-radius:50%;opacity:0;transition:opacity .3s ease;z-index:2}
.fcard:hover .fcard-sparkle{opacity:1;animation:sparkle 1.5s ease infinite}
@keyframes sparkle{0%,100%{transform:scale(1);opacity:1}50%{transform:scale(1.5);opacity:.5}}

.ficon{font-size:2.5rem;margin-bottom:1.2rem;transition:transform .3s ease;position:relative;z-index:2}
.fcard:hover .ficon{transform:scale(1.1) rotate(5deg)}
.ftitle{font-size:1.15rem;font-weight:800;color:var(--txt);margin-bottom:.7rem;transition:color .3s ease;position:relative;z-index:2}
.fcard:hover .ftitle{color:var(--acc)}
.fdesc{color:var(--txt2);font-size:.92rem;line-height:1.75;transition:color .3s ease;position:relative;z-index:2}
.fcard:hover .fdesc{color:var(--txt)}
.fcard-arrow{position:absolute;bottom:1.5rem;right:1.5rem;font-size:1.2rem;color:var(--acc);opacity:0;transform:translateX(-10px);transition:all .3s ease;z-index:2}
.fcard:hover .fcard-arrow{opacity:1;transform:translateX(0)}

.sec-sub{background:linear-gradient(180deg,rgba(232,121,249,0.05) 0%,transparent 100%)}
.form-box{max-width:700px;margin:0 auto;position:relative;background:var(--card);backdrop-filter:blur(35px);border:1px solid var(--brd);border-radius:32px;padding:3rem 2.5rem;box-shadow:var(--shadow)}
.form-box-glow{position:absolute;bottom:-120px;right:-120px;width:350px;height:350px;border-radius:50%;background:var(--acc2);filter:blur(110px);opacity:.12;pointer-events:none}
.form-box-particles{position:absolute;top:0;left:0;right:0;height:2px;background:linear-gradient(90deg,transparent,var(--acc),transparent);animation:particleSweep 3s linear infinite}
@keyframes particleSweep{0%{transform:translateX(-100%)}100%{transform:translateX(100%)}}

.fld{margin-bottom:1.8rem}
.flbl{display:flex;align-items:center;gap:.5rem;font-size:.9rem;font-weight:700;color:var(--txt);margin-bottom:.7rem;letter-spacing:.3px}
.flbl-icon{font-size:1.1rem}
.ast{color:var(--acc3)}

.chips{display:flex;flex-wrap:wrap;gap:.6rem}
.chip{padding:.55rem 1.3rem;border-radius:20px;font-size:.87rem;font-weight:700;background:var(--card-s);border:2px solid var(--brd);color:var(--txt2);transition:all .3s cubic-bezier(0.4,0,0.2,1)}
.chip:hover{border-color:var(--acc);color:var(--txt);transform:translateY(-2px)}
.chip.on{background:linear-gradient(135deg,var(--acc),var(--acc2));border-color:transparent;color:#fff;box-shadow:0 6px 24px var(--glow);transform:translateY(-2px)}

.textarea-wrap{position:relative}
.ta{width:100%;padding:1rem 1.3rem;border-radius:16px;font-size:.95rem;font-family:inherit;line-height:1.7;resize:vertical;background:var(--card-s);border:2px solid var(--brd);color:var(--txt);transition:all .3s ease}
.ta:focus{border-color:var(--acc);box-shadow:0 0 0 4px var(--glow);outline:none}
.ta::placeholder{color:var(--txt3)}
.char-count{position:absolute;bottom:.8rem;right:1rem;font-size:.75rem;color:var(--txt3);pointer-events:none}

.sel-wrap{position:relative}
.sel{width:100%;padding:1rem 3rem 1rem 1.3rem;border-radius:16px;font-size:.95rem;font-family:inherit;appearance:none;-webkit-appearance:none;background:var(--card-s);border:2px solid var(--brd);color:var(--txt);transition:all .3s ease;cursor:pointer}
.sel:focus{border-color:var(--acc);box-shadow:0 0 0 4px var(--glow);outline:none}
.sel-arr{position:absolute;right:1.2rem;top:50%;transform:translateY(-50%);color:var(--txt3);pointer-events:none}
.sel option{background:var(--card-s);color:var(--txt)}

.ferr{display:block;margin-top:.5rem;font-size:.82rem;color:#f87171;font-weight:600}
.err-slide-enter-active,.err-slide-leave-active{transition:all .3s ease}
.err-slide-enter-from,.err-slide-leave-to{opacity:0;transform:translateY(-10px)}

.discl{text-align:center;margin-top:1.6rem;color:var(--txt3);font-size:.85rem;line-height:1.7}

.sec-req{background:linear-gradient(180deg,transparent 0%,rgba(251,146,60,0.05) 100%)}
.pills{display:flex;flex-wrap:wrap;gap:.6rem;justify-content:center;margin-bottom:1.5rem}
.pill{display:flex;align-items:center;gap:.5rem;padding:.55rem 1.5rem;border-radius:20px;font-size:.85rem;font-weight:700;background:var(--card);backdrop-filter:blur(12px);border:2px solid var(--brd);color:var(--txt2);transition:all .3s cubic-bezier(0.4,0,0.2,1)}
.pill:hover{border-color:var(--acc);color:var(--txt)}
.pill.on{background:linear-gradient(135deg,var(--acc),var(--acc2));border-color:transparent;color:#fff;box-shadow:0 6px 22px var(--glow)}
.pill-count{display:inline-flex;align-items:center;justify-content:center;min-width:22px;height:22px;padding:0 .4rem;border-radius:12px;font-size:.75rem;font-weight:800;background:rgba(255,255,255,0.2)}

.sortlabel{display:flex;align-items:center;gap:.5rem;justify-content:center;color:var(--txt3);font-size:.82rem;font-weight:600;margin-bottom:2rem}
.live-indicator{display:inline-flex;align-items:center;gap:.4rem;margin-left:1rem;padding:.3rem .8rem;border-radius:12px;background:rgba(239,68,68,0.1);color:#ef4444;font-weight:700;font-size:.7rem}
.live-dot{width:6px;height:6px;border-radius:50%;background:#ef4444;animation:pulse 2s infinite}

.req-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(340px,1fr));gap:1.5rem}
@media(max-width:700px){.req-grid{grid-template-columns:1fr}}

.card-list-enter-active{transition:all .5s cubic-bezier(0.4,0,0.2,1)}
.card-list-leave-active{transition:all .3s cubic-bezier(0.4,0,0.2,1)}
.card-list-enter-from{opacity:0;transform:translateY(30px) scale(0.95)}
.card-list-leave-to{opacity:0;transform:translateY(-30px) scale(0.95)}
.card-list-move{transition:transform .5s cubic-bezier(0.4,0,0.2,1)}

.rcard{position:relative;overflow:hidden;background:var(--card);backdrop-filter:blur(30px);border:1px solid var(--brd);border-radius:24px;padding:1.8rem;display:flex;flex-direction:column;gap:1rem;transition:all .4s cubic-bezier(0.4,0,0.2,1);will-change:transform}
.rcard:hover{box-shadow:var(--shadow);border-color:var(--acc)}
.rcard-shine{position:absolute;top:0;left:-100%;width:55%;height:100%;background:linear-gradient(120deg,transparent,rgba(255,255,255,0.1),transparent);transition:left .6s ease;pointer-events:none}
.rcard:hover .rcard-shine{left:160%}
.rcard-glow{position:absolute;bottom:-60px;right:-60px;width:200px;height:200px;border-radius:50%;background:var(--acc);filter:blur(80px);opacity:0;transition:opacity .5s ease;pointer-events:none}
.rcard:hover .rcard-glow{opacity:.2}
.rcard-new{position:absolute;top:1rem;right:1rem;font-size:1.2rem;animation:bounce 1s ease infinite}
@keyframes bounce{0%,100%{transform:translateY(0)}50%{transform:translateY(-5px)}}

.rcard-top{display:flex;justify-content:space-between;align-items:center;gap:.8rem;flex-wrap:wrap;position:relative;z-index:1}
.rbadge{padding:.35rem 1rem;border-radius:14px;font-size:.78rem;font-weight:700;background:linear-gradient(135deg,var(--acc),var(--acc2));color:#fff}
.rloc{font-size:.8rem;color:var(--txt3);font-weight:500}
.rtxt{color:var(--txt);font-size:.94rem;line-height:1.75;flex:1;position:relative;z-index:1}
.rbot{display:flex;justify-content:space-between;align-items:center;padding-top:1rem;border-top:1px solid var(--brd);position:relative;z-index:1}
.rdate{font-size:.78rem;color:var(--txt3)}

.lbk{display:flex;align-items:center;gap:.5rem;padding:.5rem 1rem;border-radius:18px;background:var(--card-s);border:2px solid var(--brd);color:var(--txt2);transition:all .3s cubic-bezier(0.34,1.56,0.64,1);position:relative;overflow:hidden}
.lbk:hover{border-color:#f43f5e;transform:scale(1.15);box-shadow:0 6px 22px rgba(244,63,94,0.3)}
.lbk:hover .lheart{color:#f43f5e}
.lbk:hover .lcnt{color:#f43f5e}
.lbk.on{border-color:#f43f5e;background:rgba(244,63,94,0.15)}
.lbk.on .lheart{color:#f43f5e}
.lbk.on .lcnt{color:#f43f5e;font-weight:800}
.lbk.pulse .lheart{animation:heartPop .5s cubic-bezier(0.34,1.56,0.64,1)}
@keyframes heartPop{0%{transform:scale(1)}40%{transform:scale(1.6)}100%{transform:scale(1)}}

.lheart{display:flex;align-items:center;color:var(--txt3);transition:color .3s ease}
.lcnt{font-size:.9rem;font-weight:700;color:var(--txt2);min-width:20px;text-align:center;transition:color .3s ease}

.empty{text-align:center;padding:5rem 2rem}
.empty-ic{font-size:4rem;margin-bottom:1.5rem;animation:float 3s ease-in-out infinite}
.empty p{color:var(--txt3);font-size:1.1rem}

.footer{position:relative;z-index:1;padding:4rem 2rem 2rem;border-top:1px solid var(--brd);background:linear-gradient(180deg,transparent,var(--bg2))}
.ft-wrap{max-width:1200px;margin:0 auto;display:flex;justify-content:space-between;gap:3rem;flex-wrap:wrap;margin-bottom:3rem}
.ft-desc{color:var(--txt2);font-size:.9rem;line-height:1.8;max-width:360px;margin-top:.8rem}
.ft-social{display:flex;gap:.8rem;margin-top:1.2rem}
.social-link{width:40px;height:40px;border-radius:50%;background:var(--card);border:1px solid var(--brd);display:flex;align-items:center;justify-content:center;font-size:1.2rem;transition:all .3s cubic-bezier(0.4,0,0.2,1);text-decoration:none}
.social-link:hover{transform:translateY(-4px);box-shadow:0 8px 24px var(--glow);border-color:var(--acc)}
.ft-ltitle{font-size:.85rem;font-weight:800;color:var(--txt);letter-spacing:1px;text-transform:uppercase;margin-bottom:1rem}
.ft-link{display:block;color:var(--txt3);font-size:.9rem;padding:.3rem 0;transition:all .25s ease;cursor:pointer}
.ft-link:hover{color:var(--acc);padding-left:.5rem}
.ft-bot{max-width:1200px;margin:0 auto;padding-top:2rem;border-top:1px solid var(--brd);text-align:center}
.ft-bot p{color:var(--txt3);font-size:.85rem}

.toast{position:fixed;bottom:2.5rem;left:2.5rem;z-index:99999;display:flex;align-items:center;gap:1rem;padding:1.3rem 2rem;border-radius:24px;min-width:340px;max-width:420px;background:var(--card-s);border:1px solid var(--brd);color:var(--txt);font-size:.95rem;font-weight:600;box-shadow:0 20px 60px rgba(0,0,0,0.5);backdrop-filter:blur(40px)}
.toast:not(.err){border-color:rgba(34,197,94,0.4);box-shadow:0 20px 60px rgba(34,197,94,0.2)}
.toast.err{border-color:rgba(239,68,68,0.4);box-shadow:0 20px 60px rgba(239,68,68,0.2)}

.toast-slide-enter-active,.toast-slide-leave-active{transition:all .5s cubic-bezier(0.34,1.56,0.64,1)}
.toast-slide-enter-from,.toast-slide-leave-to{transform:translateX(-120%) scale(0.9);opacity:0}

.toast-ic{width:42px;height:42px;border-radius:50%;flex-shrink:0;display:flex;align-items:center;justify-content:center;background:linear-gradient(135deg,#22c55e,#16a34a);box-shadow:0 6px 20px rgba(34,197,94,0.5)}
.toast.err .toast-ic{background:linear-gradient(135deg,#ef4444,#dc2626);box-shadow:0 6px 20px rgba(239,68,68,0.5)}
.icon-swap-enter-active,.icon-swap-leave-active{transition:all .25s ease}
.icon-swap-enter-from,.icon-swap-leave-to{transform:scale(0) rotate(90deg);opacity:0}

.toast-body{display:flex;flex-direction:column;gap:.2rem;flex:1}
.toast-title{font-size:.85rem;font-weight:800;color:#22c55e;letter-spacing:.5px;text-transform:uppercase}
.toast.err .toast-title{color:#ef4444}
.toast-txt{font-size:.92rem;color:var(--txt2);line-height:1.5}
.toast-close{width:28px;height:28px;border-radius:50%;background:transparent;border:none;color:var(--txt3);font-size:1.1rem;display:flex;align-items:center;justify-content:center;transition:all .25s ease;cursor:pointer;flex-shrink:0}
.toast-close:hover{background:var(--brd);color:var(--txt);transform:rotate(90deg)}

.sctop{position:fixed;bottom:2.5rem;right:2.5rem;z-index:8000;width:54px;height:54px;border-radius:50%;background:var(--card);backdrop-filter:blur(20px);border:2px solid var(--brd);color:var(--txt);display:flex;align-items:center;justify-content:center;box-shadow:0 6px 28px rgba(0,0,0,0.25);transition:all .4s cubic-bezier(0.34,1.56,0.64,1)}
.sctop:hover{transform:translateY(-6px);box-shadow:0 12px 40px var(--glow);border-color:var(--acc)}

.scale-fade-enter-active,.scale-fade-leave-active{transition:all .4s cubic-bezier(0.34,1.56,0.64,1)}
.scale-fade-enter-from,.scale-fade-leave-to{transform:translateY(30px) scale(0.8);opacity:0}

.fade-enter-active,.fade-leave-active{transition:opacity .3s ease}
.fade-enter-from,.fade-leave-to{opacity:0}

@media(max-width:700px){
  .sec,.sec-why,.sec-sub,.sec-req{padding:90px 1.5rem}
  .hero{padding:110px 1.5rem 80px}
  .form-box{padding:2rem 1.5rem}
  .hero-btns{flex-direction:column;align-items:stretch}
  .hero-btns .btn{width:100%;max-width:100%;justify-content:center}
  .stats{gap:2rem}
  .toast{bottom:1.5rem;left:1.5rem;right:1.5rem;min-width:auto}
  .sctop{bottom:1.5rem;right:1.5rem;width:48px;height:48px}
  .nav-wrap{padding:0 1.5rem}
}

@media(max-width:500px){
  .logo-txt{display:none}
  .h1-top{font-size:2.2rem}
  .h1-bot{font-size:2.8rem}
}
</style>