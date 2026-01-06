<script setup>
import { computed, reactive, ref, watch, onMounted, onUnmounted } from 'vue'

const events = ref([
  { id: 'EVT10001', name: 'Tech Innovations Conference', category: 'Technology', duration: 8 },
  { id: 'EVT10002', name: 'Startup Pitch Day', category: 'Business', duration: 6 },
  { id: 'EVT10003', name: 'AI & Machine Learning Summit', category: 'Technology', duration: 10 },
  { id: 'EVT10004', name: 'Cybersecurity Workshop', category: 'Technology', duration: 4 },
  { id: 'EVT10005', name: 'Digital Marketing Bootcamp', category: 'Marketing', duration: 6 },
  { id: 'EVT10006', name: 'Blockchain and Cryptocurrency', category: 'Finance', duration: 5 },
  { id: 'EVT10007', name: 'Entrepreneurship Forum', category: 'Business', duration: 7 },
  { id: 'EVT10008', name: 'Data Science Hackathon', category: 'Technology', duration: 12 },
  { id: 'EVT10009', name: 'Leadership and Management Summit', category: 'Business', duration: 9 },
  { id: 'EVT10010', name: 'E-commerce Strategies', category: 'Marketing', duration: 6 },
  { id: 'EVT10011', name: 'AI for Business', category: 'Business', duration: 8 },
  { id: 'EVT10012', name: 'IoT & Smart Devices Expo', category: 'Technology', duration: 7 },
  { id: 'EVT10013', name: 'Brand Strategy and Growth', category: 'Marketing', duration: 5 },
  { id: 'EVT10014', name: 'Investment and Wealth Management', category: 'Finance', duration: 6 },
  { id: 'EVT10015', name: 'Financial Technology (FinTech) Summit', category: 'Finance', duration: 8 },
  { id: 'EVT10016', name: 'AI Ethics and Regulations', category: 'Technology', duration: 4 },
  { id: 'EVT10017', name: 'Business Analytics Workshop', category: 'Business', duration: 6 },
  { id: 'EVT10018', name: 'SEO and Content Marketing', category: 'Marketing', duration: 7 },
  { id: 'EVT10019', name: 'Cryptocurrency Investment Strategies', category: 'Finance', duration: 9 },
  { id: 'EVT10020', name: 'Social Media Marketing Trends', category: 'Marketing', duration: 5 },
])

const search = reactive({
  id: '',
  name: '',
  duration: '',
})

const categoryFilter = ref('All')

const pagination = reactive({
  currentPage: 1,
  itemsPerPage: 10,
})

const filteredEvents = computed(() => {
  return events.value.filter((event) => {
    const matchId = event.id.toLowerCase().includes(search.id.trim().toLowerCase())
    const matchName = event.name.toLowerCase().includes(search.name.trim().toLowerCase())
    const matchDuration = search.duration
      ? `${event.duration}`.toLowerCase().includes(search.duration.trim().toLowerCase())
      : true
    const matchCategory = categoryFilter.value === 'All' || event.category === categoryFilter.value
    return matchId && matchName && matchDuration && matchCategory
  })
})

const paginatedEvents = computed(() => {
  const start = (pagination.currentPage - 1) * pagination.itemsPerPage
  const end = start + pagination.itemsPerPage
  return filteredEvents.value.slice(start, end)
})

const totalPages = computed(() => {
  return Math.ceil(filteredEvents.value.length / pagination.itemsPerPage)
})

watch([() => search.id, () => search.name, () => search.duration, categoryFilter], () => {
  pagination.currentPage = 1
})

const form = reactive({
  username: '',
  password: '',
  confirm: '',
  category: 'Business',
  eventName: '',
})

const showPassword = ref(false)
const showConfirmPassword = ref(false)

const eventsByCategory = computed(() => {
  if (form.category === 'All') return events.value
  return events.value.filter((event) => event.category === form.category)
})

watch(
  () => form.category,
  () => {
    const firstEvent = eventsByCategory.value[0]?.name || ''
    form.eventName = firstEvent
  },
  { immediate: true }
)

const passwordsMatch = computed(() => {
  if (!form.confirm) return true
  return form.password === form.confirm
})

const submission = ref(null)

const handleSubmit = () => {
  if (!passwordsMatch.value) return
  submission.value = {
    username: form.username || '—',
    category: form.category || '—',
    event: form.eventName || '—',
  }
}

const whyItems = [
  {
    title: 'Human-centered design',
    desc: 'Experiences that balance business objectives with attendee satisfaction.',
    icon: '/person-standing.svg',
  },
  {
    title: 'Scalable solutions',
    desc: 'Robust systems that perform flawlessly from 10 to 10,000 guests.',
    icon: '/image-upscale.svg',
  },
  {
    title: 'Data-driven insights',
    desc: 'Analytics dashboards keep every stakeholder informed in real time.',
    icon: '/chart-no-axes-combined.svg',
  },
  {
    title: 'Enterprise security',
    desc: 'Compliance, privacy, and resilient infrastructure at every step.',
    icon: '/shield-user.svg',
  },
  {
    title: 'Streamlined registration',
    desc: 'Efficient registration process with intelligent defaults and autofill.',
    icon: '/notebook-pen.svg',
  },
  {
    title: 'Customizable branding',
    desc: 'Brand-perfect visuals with modular, theme-aware components.',
    icon: '/package.svg',
  },
]

const navLinks = [
  { label: 'Overview', href: '#hero' },
  { label: 'Why us', href: '#why' },
  { label: 'Events', href: '#events' },
  { label: 'Register', href: '#registration' },
]

const isHeaderVisible = ref(true)
const isMobileMenuOpen = ref(false)
let lastScrollY = 0
let ticking = false

const handleScroll = () => {
  if (!ticking) {
    window.requestAnimationFrame(() => {
      const currentScrollY = window.scrollY
      
      if (currentScrollY <= 0) {
        isHeaderVisible.value = true
      } else if (currentScrollY < lastScrollY) {
        isHeaderVisible.value = true
      } else if (currentScrollY > lastScrollY && currentScrollY > 80) {
        isHeaderVisible.value = false
      }
      
      lastScrollY = currentScrollY
      ticking = false
    })
    
    ticking = true
  }
}

const toggleMobileMenu = () => {
  isMobileMenuOpen.value = !isMobileMenuOpen.value
}

const closeMobileMenu = () => {
  isMobileMenuOpen.value = false
}

const handleResize = () => {
  if (typeof window === 'undefined') return
  if (window.innerWidth >= 992 && isMobileMenuOpen.value) {
    isMobileMenuOpen.value = false
  }
}

watch(isMobileMenuOpen, (isOpen) => {
  if (typeof document === 'undefined') return
  document.body.classList.toggle('no-scroll', isOpen)
})

onMounted(() => {
  lastScrollY = window.scrollY
  window.addEventListener('scroll', handleScroll, { passive: true })
  window.addEventListener('resize', handleResize, { passive: true })
})

onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll)
  window.removeEventListener('resize', handleResize)
  if (typeof document !== 'undefined') {
    document.body.classList.remove('no-scroll')
  }
})
</script>

<template>
  <div class="page-shell">
    <a href="#main-content" class="skip-link">Skip to main content</a>
    <div class="mobile-menu-backdrop" :class="{ open: isMobileMenuOpen }" @click="closeMobileMenu"></div>
    <div id="mobile-menu" class="mobile-menu" :class="{ open: isMobileMenuOpen }">
      <div class="mobile-menu-inner">
        <div class="mobile-menu-head">
          <div class="mobile-menu-brand">
            <img src="/calendar.png" alt="EventOS Studio Logo" class="brand-logo" />
            <p class="eyebrow brand-text">EventOS Studio</p>
          </div>
          <button class="hamburger close" type="button" aria-label="Close navigation" @click="closeMobileMenu">
            <span></span>
            <span></span>
            <span></span>
          </button>
        </div>
        <nav aria-label="Mobile primary navigation">
          <a
            v-for="link in navLinks"
            :key="`mobile-${link.href}`"
            :href="link.href"
            @click="closeMobileMenu"
          >
            {{ link.label }}
          </a>
        </nav>
        <div class="mobile-actions">
          <button class="btn-ghost" type="button" @click="closeMobileMenu">Login</button>
          <button class="btn-solid" type="button" @click="closeMobileMenu"><span>Sign Up</span></button>
        </div>
      </div>
    </div>

    <header class="site-header" :class="{ 'header-hidden': !isHeaderVisible }">
      <div class="container header-inner">
        <div class="header-brand">
          <img src="/calendar.png" alt="EventOS Studio Logo" class="brand-logo" />
          <p class="eyebrow brand-text brand-text-large">EventOS Studio</p>
        </div>
        <nav class="site-nav" aria-label="Primary">
          <a
            v-for="link in navLinks"
            :key="link.href"
            :href="link.href"
            @click="closeMobileMenu"
          >
            {{ link.label }}
          </a>
        </nav>
        <div class="header-actions">
          <button class="btn-ghost" type="button">Login</button>
          <button class="btn-solid" type="button"><span>Sign Up</span></button>
        </div>
        <button
          :class="['hamburger', { close: isMobileMenuOpen }]"
          type="button"
          aria-label="Toggle navigation"
          :aria-expanded="isMobileMenuOpen.toString()"
          aria-controls="mobile-menu"
          @click="toggleMobileMenu"
        >
          <span></span>
          <span></span>
          <span></span>
        </button>
      </div>
    </header>

    <main id="main-content" role="main" aria-label="Main content">
      <section id="hero" class="hero" aria-labelledby="hero-heading">
        <div class="container">
          <div class="row align-items-center">
            <div class="col-12 col-lg-6 hero__copy">
              <h1 id="hero-heading">Design events that everyone talks about.</h1>
              <p class="lede">
                Discover, explore, and register for professional events with an intuitive platform designed for seamless event management.
              </p>
              <div class="hero__cta">
                <button class="btn-solid" type="button"><span>Get Started</span></button>
                <button class="btn-ghost" type="button">Contact Sales</button>
              </div>
            </div>
            <div class="col-12 col-lg-6 hero__visual">
              <div class="glass-card">
                <img src="/event-management.webp" alt="Event management" />
                <div class="floating-stat">
                  <div>
                    <p class="label">Live attendees</p>
                    <p class="value">18,240</p>
                  </div>
                  <div>
                    <p class="label">Satisfaction</p>
                    <p class="value">98%</p>
                  </div>
                  <div>
                    <p class="label">Automations</p>
                    <p class="value">42</p>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </section>

      <section id="why" class="why" aria-labelledby="why-heading">
        <div class="container">
          <div class="section-head">
            <div>
              <h2 id="why-heading"><span class="section-icon" aria-hidden="true">►</span>Why choose us?</h2>
            </div>
          </div>

          <div class="row g-4">
            <div v-for="item in whyItems" :key="item.title" class="col-12 col-md-6 col-lg-4">
              <div class="why-card">
                <img class="why-number" :src="item.icon" :alt="item.title" />
                <h3>{{ item.title }}</h3>
                <p>{{ item.desc }}</p>
              </div>
            </div>
          </div>
        </div>
      </section>

      <section id="events" class="events" aria-labelledby="events-heading">
        <div class="container">
          <div class="section-head">
            <div>
              <h2 id="events-heading"><span class="section-icon" aria-hidden="true">►</span>Find the perfect event in seconds.</h2>
            </div>
          </div>

          <div class="filter-grid row g-3">
            <div class="col-12 col-md-4">
              <label class="field-label" for="filter-id">Event ID</label>
              <input 
                id="filter-id" 
                v-model="search.id" 
                type="text" 
                placeholder="e.g., EVT10008" 
                aria-label="Search by event ID"
              />
            </div>
            <div class="col-12 col-md-4">
              <label class="field-label" for="filter-name">Event Name</label>
              <input 
                id="filter-name" 
                v-model="search.name" 
                type="text" 
                placeholder="AI, Marketing, Leadership..." 
                aria-label="Search by event name"
              />
            </div>
            <div class="col-12 col-md-4">
              <label class="field-label" for="filter-duration">Duration (hours)</label>
              <input 
                id="filter-duration" 
                v-model="search.duration" 
                type="text" 
                placeholder="4, 6, 10..." 
                aria-label="Search by duration in hours"
              />
            </div>
          </div>

          <div class="category-filter" role="radiogroup" aria-label="Event category filter">
            <label class="chip">
              <input v-model="categoryFilter" type="radio" name="category" value="Technology" aria-label="Filter by Technology category" />
              <img src="/chip.svg" alt="" class="chip-icon" aria-hidden="true" />
              <span>Technology</span>
            </label>
            <label class="chip">
              <input v-model="categoryFilter" type="radio" name="category" value="Business" aria-label="Filter by Business category" />
              <img src="/briefcase.svg" alt="" class="chip-icon" aria-hidden="true" />
              <span>Business</span>
            </label>
            <label class="chip">
              <input v-model="categoryFilter" type="radio" name="category" value="Marketing" aria-label="Filter by Marketing category" />
              <img src="/megaphone.svg" alt="" class="chip-icon" aria-hidden="true" />
              <span>Marketing</span>
            </label>
            <label class="chip">
              <input v-model="categoryFilter" type="radio" name="category" value="Finance" aria-label="Filter by Finance category" />
              <img src="/dollar.svg" alt="" class="chip-icon" aria-hidden="true" />
              <span>Finance</span>
            </label>
            <label class="chip">
              <input v-model="categoryFilter" type="radio" name="category" value="All" aria-label="Show all categories" />
              <img src="/asterisk.svg" alt="" class="chip-icon" aria-hidden="true" />
              <span>All</span>
            </label>
          </div>

          <div class="table-shell">
            <div class="table-head" aria-live="polite" aria-atomic="true">{{ filteredEvents.length }} events</div>
            <div class="table-responsive">
              <table aria-label="Available events listing">
                <caption class="visually-hidden">List of available events with their ID, name, category, and duration</caption>
                <thead>
                  <tr>
                    <th scope="col">Event ID</th>
                    <th scope="col">Event Name</th>
                    <th scope="col">Category</th>
                    <th scope="col">Duration (Hours)</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="event in paginatedEvents" :key="event.id">
                    <td>{{ event.id }}</td>
                    <td>{{ event.name }}</td>
                    <td><span class="badge">{{ event.category }}</span></td>
                    <td>{{ event.duration }}</td>
                  </tr>
                  <tr v-if="paginatedEvents.length === 0">
                    <td colspan="4" class="empty">No events match your filters.</td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>

          <div v-if="filteredEvents.length > 0" class="pagination-controls">
            <div class="pagination-spacer"></div>
            <nav class="pagination-pages" aria-label="Pagination navigation">
              <button
                type="button"
                class="pagination-arrow"
                :disabled="pagination.currentPage === 1"
                @click="pagination.currentPage--"
                aria-label="Go to previous page"
              >
                <img src="/arrow-left.svg" alt="" aria-hidden="true" />
              </button>
              <button
                v-for="page in totalPages"
                :key="page"
                type="button"
                class="pagination-page"
                :class="{ active: pagination.currentPage === page }"
                @click="pagination.currentPage = page"
                :aria-label="`Go to page ${page}`"
                :aria-current="pagination.currentPage === page ? 'page' : undefined"
              >
                {{ page }}
              </button>
              <button
                type="button"
                class="pagination-arrow"
                :disabled="pagination.currentPage >= totalPages"
                @click="pagination.currentPage++"
                aria-label="Go to next page"
              >
                <img src="/arrow-right.svg" alt="" aria-hidden="true" />
              </button>
            </nav>
            <div class="pagination-info">
              <label class="field-label" for="items-per-page">Show</label>
              <select id="items-per-page" v-model.number="pagination.itemsPerPage" class="pagination-select" aria-label="Number of events per page">
                <option :value="5">5</option>
                <option :value="10">10</option>
                <option :value="20">20</option>
              </select>
              <span class="pagination-text">events per page</span>
            </div>
          </div>
        </div>
      </section>

      <section id="registration" class="registration" aria-labelledby="registration-heading">
        <div class="container">
          <div class="section-head">
            <div>
              <h2 id="registration-heading"><span class="section-icon" aria-hidden="true">►</span>Claim your seat.</h2>
            </div>
          </div>

          <form class="registration-form" @submit.prevent="handleSubmit" aria-label="Event registration form">
            <div class="form-fields-row">
              <div class="form-column">
                <div class="form-field">
                  <label class="field-label" for="username">Username</label>
                  <input 
                    id="username" 
                    v-model="form.username" 
                    type="text" 
                    placeholder="john_doe" 
                    required 
                    aria-required="true"
                  />
                </div>
                <div class="form-field">
                  <label class="field-label" for="password">Password</label>
                  <div class="password-field">
                    <input 
                      id="password" 
                      v-model="form.password" 
                      :type="showPassword ? 'text' : 'password'" 
                      placeholder="•••••••" 
                      required 
                      aria-required="true"
                    />
                    <button 
                      type="button" 
                      class="password-toggle" 
                      @click="showPassword = !showPassword"
                      :aria-label="showPassword ? 'Hide password' : 'Show password'"
                    >
                      <img 
                        :src="showPassword ? '/eye.svg' : '/eye-off.svg'" 
                        :alt="showPassword ? 'Hide password' : 'Show password'"
                        class="password-toggle-icon"
                      />
                    </button>
                  </div>
                </div>
                <div class="form-field">
                  <label class="field-label" for="confirm">Confirm password</label>
                  <div class="input-with-note">
                    <div class="password-field">
                      <input 
                        id="confirm" 
                        v-model="form.confirm" 
                        :type="showConfirmPassword ? 'text' : 'password'" 
                        placeholder="Repeat password" 
                        required 
                        aria-required="true"
                        :aria-describedby="!passwordsMatch ? 'password-mismatch' : undefined"
                        :aria-invalid="!passwordsMatch"
                      />
                      <button 
                        type="button" 
                        class="password-toggle" 
                        @click="showConfirmPassword = !showConfirmPassword"
                        :aria-label="showConfirmPassword ? 'Hide password' : 'Show password'"
                      >
                        <img 
                          :src="showConfirmPassword ? '/eye.svg' : '/eye-off.svg'" 
                          :alt="showConfirmPassword ? 'Hide password' : 'Show password'"
                          class="password-toggle-icon"
                        />
                      </button>
                    </div>
                    <span v-if="!passwordsMatch" id="password-mismatch" class="note" role="alert">Passwords do not match</span>
                  </div>
                </div>
              </div>

              <div class="form-column">
                <div class="form-field">
                  <p class="field-label">Category</p>
                  <div class="chip-stack" role="radiogroup" aria-label="Select category">
                    <label class="chip">
                      <input v-model="form.category" type="radio" name="form-category" value="Business" aria-label="Select Business category" />
                      <img src="/briefcase.svg" alt="" class="chip-icon" aria-hidden="true" />
                      <span>Business</span>
                    </label>
                    <label class="chip">
                      <input v-model="form.category" type="radio" name="form-category" value="Technology" aria-label="Select Technology category" />
                      <img src="/chip.svg" alt="" class="chip-icon" aria-hidden="true" />
                      <span>Technology</span>
                    </label>
                    <label class="chip">
                      <input v-model="form.category" type="radio" name="form-category" value="Marketing" aria-label="Select Marketing category" />
                      <img src="/megaphone.svg" alt="" class="chip-icon" aria-hidden="true" />
                      <span>Marketing</span>
                    </label>
                    <label class="chip">
                      <input v-model="form.category" type="radio" name="form-category" value="Finance" aria-label="Select Finance category" />
                      <img src="/dollar.svg" alt="" class="chip-icon" aria-hidden="true" />
                      <span>Finance</span>
                    </label>
                    <label class="chip">
                      <input v-model="form.category" type="radio" name="form-category" value="All" aria-label="Select all categories" />
                      <img src="/asterisk.svg" alt="" class="chip-icon" aria-hidden="true" />
                      <span>All</span>
                    </label>
                  </div>
                </div>
                <div class="form-field">
                  <label class="field-label" for="event">Event name</label>
                  <select id="event" v-model="form.eventName" required aria-required="true">
                    <option v-for="event in eventsByCategory" :key="event.name" :value="event.name">
                      {{ event.name }}
                    </option>
                  </select>
                </div>
              </div>
            </div>
            <div class="form-submit-row">
              <button class="btn-solid" type="submit" :disabled="!passwordsMatch" :aria-disabled="!passwordsMatch"><span>Register</span></button>
            </div>
          </form>

          <div v-if="submission" class="summary" role="status" aria-live="polite" aria-atomic="true">
            <p class="eyebrow">Submission summary</p>
            <div class="summary-grid">
              <div>
                <p class="label">Username</p>
                <p class="value">{{ submission.username }}</p>
              </div>
              <div>
                <p class="label">Category</p>
                <p class="value">{{ submission.category }}</p>
              </div>
              <div>
                <p class="label">Event</p>
                <p class="value">{{ submission.event }}</p>
              </div>
            </div>
          </div>
        </div>
      </section>
    </main>

    <footer class="site-footer" role="contentinfo" aria-label="Site footer">
      <div class="container footer-grid">
        <div class="footer-brand-section">
          <div class="footer-brand">
            <img src="/calendar.png" alt="EventOS Studio Logo" class="brand-logo" />
            <p class="eyebrow brand-text">EventOS Studio</p>
          </div>
          <p class="footer-about">Empowering organizations to deliver unforgettable events with cutting-edge technology and seamless experiences.</p>
        </div>
        <div class="footer-links-row">
          <nav class="footer-links" aria-label="Platform links">
            <p class="label">Platform</p>
            <a href="#events">Event catalog</a>
            <a href="#registration">Registration</a>
            <a href="#hero">Automations</a>
          </nav>
          <nav class="footer-links" aria-label="Company links">
            <p class="label">Company</p>
            <a href="#why">About</a>
            <a href="#registration">Contact</a>
            <a href="#events">Resources</a>
          </nav>
          <nav class="footer-links" aria-label="Social media links">
            <p class="label">Follow</p>
            <a href="https://www.linkedin.com" target="_blank" rel="noreferrer">
              <img src="/linkedin.svg" alt="LinkedIn" style="width: 20px; height: 20px; margin-right: 8px; vertical-align: middle;" />
              LinkedIn
            </a>
            <a href="https://www.youtube.com" target="_blank" rel="noreferrer">
              <img src="/youtube.svg" alt="YouTube" style="width: 20px; height: 20px; margin-right: 8px; vertical-align: middle;" />
              YouTube
            </a>
            <a href="https://www.instagram.com" target="_blank" rel="noreferrer">
              <img src="/instagram.svg" alt="Instagram" style="width: 20px; height: 20px; margin-right: 8px; vertical-align: middle;" />
              Instagram
            </a>
          </nav>
        </div>
      </div>
      <div class="container footer-meta">
        <p>© {{ new Date().getFullYear() }} EventOS Studio. All rights reserved.</p>
        <div class="footer-meta-links">
          <a href="#">Privacy</a>
          <a href="#">Terms</a>
        </div>
      </div>
    </footer>
  </div>
</template>
