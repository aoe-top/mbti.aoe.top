<template>
  <div class="app-wrapper">
    <nav class="nav">
      <div class="container nav-content">
        <router-link :to="getLocalizedRoute('home')" class="nav-brand">MBTI测试</router-link>

        <!-- 移动端汉堡菜单按钮 -->
        <button class="mobile-menu-btn" @click="toggleMobileMenu" :class="{ active: isMobileMenuOpen }">
          <span></span>
          <span></span>
          <span></span>
        </button>

        <!-- 导航链接 -->
        <div class="nav-links" :class="{ 'mobile-open': isMobileMenuOpen }">
          <router-link :to="getLocalizedRoute('home')" class="nav-link" @click="closeMobileMenu">{{ $t('nav.home')
          }}</router-link>
          <router-link :to="getLocalizedRoute('test')" class="nav-link" @click="closeMobileMenu">{{ $t('nav.test')
          }}</router-link>
          <router-link :to="getLocalizedRoute('about')" class="nav-link" @click="closeMobileMenu">{{ $t('nav.about')
          }}</router-link>

          <!-- 语言切换器 -->
          <div class="language-switcher" ref="languageSwitcher">
            <button class="current-language-btn" @click="toggleLanguageMenu" :class="{ active: isLanguageMenuOpen }"
              :aria-expanded="isLanguageMenuOpen" :aria-haspopup="true" aria-label="选择语言">
              <span class="current-lang">
                {{ currentLangData.flag }} {{ currentLangData.name }}
              </span>
              <svg class="dropdown-icon" :class="{ rotated: isLanguageMenuOpen }" width="16" height="16"
                viewBox="0 0 24 24" fill="currentColor">
                <path d="M7 10l5 5 5-5z" />
              </svg>
            </button>

            <transition name="dropdown">
              <div v-if="isLanguageMenuOpen" class="language-dropdown" role="menu">
                <button v-for="lang in availableLanguages" :key="lang.code" @click="changeLanguage(lang.code)"
                  :class="['lang-option', { active: currentLanguage === lang.code }]" role="menuitem"
                  :aria-label="`切换到${lang.name}`">
                  <span class="lang-flag">{{ lang.flag }}</span>
                  <span class="lang-name">{{ lang.name }}</span>
                  <span v-if="currentLanguage === lang.code" class="check-icon">✓</span>
                </button>
              </div>
            </transition>
          </div>

          <a href="https://github.com/3DMXM/mbti.aoe.top" target="_blank" rel="noopener noreferrer"
            class="nav-link github-link" @click="closeMobileMenu">
            <svg width="20" height="20" viewBox="0 0 24 24" fill="currentColor">
              <path
                d="M12 0c-6.626 0-12 5.373-12 12 0 5.302 3.438 9.8 8.207 11.387.599.111.793-.261.793-.577v-2.234c-3.338.726-4.033-1.416-4.033-1.416-.546-1.387-1.333-1.756-1.333-1.756-1.089-.745.083-.729.083-.729 1.205.084 1.839 1.237 1.839 1.237 1.07 1.834 2.807 1.304 3.492.997.107-.775.418-1.305.762-1.604-2.665-.305-5.467-1.334-5.467-5.931 0-1.311.469-2.381 1.236-3.221-.124-.303-.535-1.524.117-3.176 0 0 1.008-.322 3.301 1.23.957-.266 1.983-.399 3.003-.404 1.02.005 2.047.138 3.006.404 2.291-1.552 3.297-1.23 3.297-1.23.653 1.653.242 2.874.118 3.176.77.84 1.235 1.911 1.235 3.221 0 4.609-2.807 5.624-5.479 5.921.43.372.823 1.102.823 2.222v3.293c0 .319.192.694.801.576 4.765-1.589 8.199-6.086 8.199-11.386 0-6.627-5.373-12-12-12z" />
            </svg>
            {{ $t('nav.github') }}
          </a>
        </div>

        <!-- 移动端遮罩层 -->
        <div class="mobile-overlay" v-if="isMobileMenuOpen" @click="closeMobileMenu"></div>
      </div>
    </nav>
    <main>
      <router-view v-slot="{ Component }">
        <transition name="fade" mode="out-in">
          <component :is="Component" />
        </transition>
      </router-view>
    </main>
    <footer class="footer">
      <div class="container">
        <p>&copy; {{ currentYear }} {{ $t('home.title') }} | {{ $t('common.copyright') }}</p>
        <div class="friendly-links-panel">
          <p class="friendly-links-title">友情链接</p>
          <p class="friendly-links-subtitle">实时同步站群最新友链，为测试页底部补充统一入口。</p>
          <div v-if="friendlyLinksPending" class="friendly-links-state">友链加载中...</div>
          <div v-else-if="friendlyLinksFailed" class="friendly-links-state">友链加载失败，请稍后重试。</div>
          <div v-else class="friendly-links-list">
            <a
              v-for="link in friendLinks"
              :key="link.url"
              :href="link.url"
              target="_blank"
              rel="noopener noreferrer"
              class="friendly-links-item"
            >
              {{ link.name }}
            </a>
          </div>
        </div>
        <div class="footer-links">
          <a href="https://github.com/3DMXM/mbti.aoe.top" target="_blank" rel="noopener noreferrer"
            class="footer-github-link">
            <svg width="16" height="16" viewBox="0 0 24 24" fill="currentColor">
              <path
                d="M12 0c-6.626 0-12 5.373-12 12 0 5.302 3.438 9.8 8.207 11.387.599.111.793-.261.793-.577v-2.234c-3.338.726-4.033-1.416-4.033-1.416-.546-1.387-1.333-1.756-1.333-1.756-1.089-.745.083-.729.083-.729 1.205.084 1.839 1.237 1.839 1.237 1.07 1.834 2.807 1.304 3.492.997.107-.775.418-1.305.762-1.604-2.665-.305-5.467-1.334-5.467-5.931 0-1.311.469-2.381 1.236-3.221-.124-.303-.535-1.524.117-3.176 0 0 1.008-.322 3.301 1.23.957-.266 1.983-.399 3.003-.404 1.02.005 2.047.138 3.006.404 2.291-1.552 3.297-1.23 3.297-1.30.653 1.653.242 2.874.118 3.176.77.84 1.235 1.911 1.235 3.221 0 4.609-2.807 5.624-5.479 5.921.43.372.823 1.102.823 2.222v3.293c0 .319.192.694.801.576 4.765-1.589 8.199-6.086 8.199-11.386 0-6.627-5.373-12-12-12z" />
            </svg>
            {{ $t('nav.viewSource') }}
          </a>
        </div>
      </div>
    </footer>
  </div>
</template>

<script lang="ts" setup>
import { computed, ref, onMounted, onUnmounted, watch } from 'vue'
import { useI18n } from 'vue-i18n'
import { useRoute, useRouter } from 'vue-router'
import { useSEO } from '@/composables/useSEO'

type FriendLink = {
  name: string
  url: string
}

const { locale } = useI18n()
const route = useRoute()
const router = useRouter()
const currentYear = computed(() => new Date().getFullYear())
const friendLinks = ref<FriendLink[]>([])
const friendlyLinksPending = ref(true)
const friendlyLinksFailed = ref(false)

// 初始化SEO
useSEO()

// 移动端菜单状态
const isMobileMenuOpen = ref(false)

// 语言下拉菜单状态
const isLanguageMenuOpen = ref(false)
const languageSwitcher = ref<HTMLElement>()

// 多语言设置
const availableLanguages = [
  { code: 'zh', name: '中文', flag: '🇨🇳' },
  { code: 'en', name: 'English', flag: '🇺🇸' },
  { code: 'ja', name: '日本語', flag: '🇯🇵' },
  { code: 'fr', name: 'Français', flag: '🇫🇷' },
  { code: 'de', name: 'Deutsch', flag: '🇩🇪' },
  { code: 'es', name: 'Español', flag: '🇪🇸' }
]

const currentLanguage = computed(() => locale.value)
const currentLangData = computed(() =>
  availableLanguages.find(lang => lang.code === currentLanguage.value) || availableLanguages[0]
)

// 获取当前页面名称（用于路由）
const getCurrentPageName = () => {
  return route.meta?.page as string || 'home'
}

// 切换语言并更新路由
const changeLanguage = (lang: string) => {
  const currentPage = getCurrentPageName()
  const newRouteName = `${lang}-${currentPage.charAt(0).toUpperCase()}${currentPage.slice(1)}`

  locale.value = lang
  localStorage.setItem('mbti-locale', lang)

  // 导航到新的语言路由
  router.push({ name: newRouteName }).catch(() => {
    // 如果路由不存在，回退到首页
    router.push(`/${lang}`)
  })

  isLanguageMenuOpen.value = false
  closeMobileMenu()
}

// 获取语言特定的路由链接
const getLocalizedRoute = (page: string) => {
  const lang = currentLanguage.value
  return `/${lang}${page === 'home' ? '' : '/' + page}`
}

// 切换语言菜单
const toggleLanguageMenu = () => {
  isLanguageMenuOpen.value = !isLanguageMenuOpen.value
}

// 关闭语言菜单
const closeLanguageMenu = () => {
  isLanguageMenuOpen.value = false
}

// 点击外部关闭菜单
const handleClickOutside = (event: Event) => {
  if (languageSwitcher.value && !languageSwitcher.value.contains(event.target as Node)) {
    closeLanguageMenu()
  }
}

// 键盘导航支持
const handleKeyDown = (event: KeyboardEvent) => {
  if (!isLanguageMenuOpen.value) return

  if (event.key === 'Escape') {
    closeLanguageMenu()
    return
  }

  if (event.key === 'ArrowDown' || event.key === 'ArrowUp') {
    event.preventDefault()
    const currentIndex = availableLanguages.findIndex(lang => lang.code === currentLanguage.value)
    let nextIndex

    if (event.key === 'ArrowDown') {
      nextIndex = (currentIndex + 1) % availableLanguages.length
    } else {
      nextIndex = currentIndex - 1 < 0 ? availableLanguages.length - 1 : currentIndex - 1
    }

    // 可以添加高亮预览逻辑
    console.log('Preview language:', availableLanguages[nextIndex].code)
  }

  if (event.key === 'Enter') {
    // 如果有预览的语言，选择它；否则保持当前选择
    closeLanguageMenu()
  }
}

// 移动端菜单
const toggleMobileMenu = () => {
  isMobileMenuOpen.value = !isMobileMenuOpen.value
  if (isMobileMenuOpen.value) {
    closeLanguageMenu()
  }
}

const closeMobileMenu = () => {
  isMobileMenuOpen.value = false
}

const loadFriendLinks = async () => {
  try {
    const response = await fetch('https://api.aoe.top/api/friendly/links')
    if (!response.ok) {
      throw new Error(`Failed to load links: ${response.status}`)
    }

    const data = await response.json()
    friendLinks.value = Array.isArray(data) ? data : []
  } catch (error) {
    console.error(error)
    friendlyLinksFailed.value = true
  } finally {
    friendlyLinksPending.value = false
  }
}

// 生命周期
onMounted(() => {
  document.addEventListener('click', handleClickOutside)
  document.addEventListener('keydown', handleKeyDown)
  loadFriendLinks()

  // 同步路由语言和i18n语言
  if (route.meta?.lang && route.meta.lang !== locale.value) {
    locale.value = route.meta.lang as string
  }
})

// 监听路由变化，同步语言设置
watch(() => route.meta?.lang, (newLang) => {
  if (newLang && newLang !== locale.value) {
    console.log('Route language changed:', newLang)
    locale.value = newLang as string
  }
}, { immediate: true })

onUnmounted(() => {
  document.removeEventListener('click', handleClickOutside)
  document.removeEventListener('keydown', handleKeyDown)
})
</script>

<style scoped>
.app-wrapper {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

main {
  flex: 1;
  padding: 4rem 0;
}

.footer {
  background: rgba(0, 0, 0, 0.2);
  padding: 1.5rem 0;
  text-align: center;
  color: white;
  backdrop-filter: blur(10px);
}

.footer-links {
  margin-top: 1rem;
}

.friendly-links-panel {
  margin-top: 1.5rem;
  padding-top: 1.25rem;
  border-top: 1px solid rgba(255, 255, 255, 0.15);
}

.friendly-links-title {
  margin: 0;
  font-size: 0.85rem;
  font-weight: 700;
  letter-spacing: 0.2em;
  text-transform: uppercase;
}

.friendly-links-subtitle {
  margin: 0.6rem auto 0;
  max-width: 40rem;
  color: rgba(255, 255, 255, 0.72);
  font-size: 0.92rem;
  line-height: 1.7;
}

.friendly-links-list {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 0.75rem;
  margin-top: 1rem;
}

.friendly-links-item,
.friendly-links-state {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: 0.65rem 1rem;
  border-radius: 999px;
  border: 1px solid rgba(255, 255, 255, 0.18);
  background: rgba(255, 255, 255, 0.08);
  color: rgba(255, 255, 255, 0.9);
  text-decoration: none;
  transition: transform 0.2s ease, border-color 0.2s ease, background 0.2s ease;
}

.friendly-links-item:hover {
  transform: translateY(-2px);
  border-color: rgba(255, 255, 255, 0.36);
  background: rgba(255, 255, 255, 0.14);
}

.friendly-links-state {
  width: fit-content;
  margin: 1rem auto 0;
}

.footer-github-link {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  color: #a0aec0;
  text-decoration: none;
  font-size: 0.9rem;
  transition: all 0.3s ease;
  padding: 0.5rem 1rem;
  border-radius: 6px;
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.footer-github-link:hover {
  color: white;
  background: rgba(255, 255, 255, 0.1);
  border-color: rgba(255, 255, 255, 0.2);
  transform: translateY(-1px);
}

.footer-github-link svg {
  transition: transform 0.3s ease;
}

.footer-github-link:hover svg {
  transform: scale(1.1);
}

/* 移动端菜单按钮 */
.mobile-menu-btn {
  display: none;
  flex-direction: column;
  background: transparent;
  border: none;
  cursor: pointer;
  padding: 8px;
  z-index: 1001;
}

.mobile-menu-btn span {
  width: 25px;
  height: 3px;
  background: white;
  margin: 3px 0;
  transition: 0.3s;
  border-radius: 1.5px;
}

.mobile-menu-btn.active span:nth-child(1) {
  transform: rotate(-45deg) translate(-6px, 6px);
}

.mobile-menu-btn.active span:nth-child(2) {
  opacity: 0;
}

.mobile-menu-btn.active span:nth-child(3) {
  transform: rotate(45deg) translate(-6px, -6px);
}

/* 移动端遮罩层 */
.mobile-overlay {
  display: none;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  z-index: 999;
}

/* GitHub链接特殊样式 */
.github-link {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  background: rgba(255, 255, 255, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.2);
  border-radius: 6px;
  transition: all 0.3s ease;
}

.github-link:hover {
  background: rgba(255, 255, 255, 0.15);
  border-color: rgba(255, 255, 255, 0.3);
  transform: translateY(-1px);
}

.github-link svg {
  transition: transform 0.3s ease;
}

.github-link:hover svg {
  transform: scale(1.1);
}

/* 语言切换器 */
.language-switcher {
  position: relative;
  display: inline-block;
}

.current-language-btn {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.5rem 0.75rem;
  border: 1px solid rgba(255, 255, 255, 0.2);
  background: rgba(255, 255, 255, 0.1);
  color: white;
  border-radius: 6px;
  cursor: pointer;
  transition: all 0.3s ease;
  font-size: 0.9rem;
  font-weight: 500;
  min-width: 120px;
  justify-content: space-between;
}

.current-language-btn:hover {
  background: rgba(255, 255, 255, 0.15);
  border-color: rgba(255, 255, 255, 0.3);
  transform: translateY(-1px);
}

.current-language-btn.active {
  background: rgba(255, 255, 255, 0.2);
  border-color: rgba(255, 255, 255, 0.4);
}

.current-lang {
  display: flex;
  align-items: center;
  gap: 0.25rem;
}

.dropdown-icon {
  transition: transform 0.3s ease;
  opacity: 0.7;
}

.dropdown-icon.rotated {
  transform: rotate(180deg);
}

.language-dropdown {
  position: absolute;
  top: 100%;
  left: 0;
  right: 0;
  margin-top: 0.5rem;
  background: rgba(45, 55, 72, 0.95);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 8px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
  z-index: 1000;
  overflow: hidden;
}

.lang-option {
  display: flex;
  align-items: center;
  width: 100%;
  padding: 0.75rem 1rem;
  border: none;
  background: transparent;
  color: white;
  cursor: pointer;
  transition: all 0.2s ease;
  font-size: 0.9rem;
  justify-content: space-between;
}

.lang-option:hover {
  background: rgba(255, 255, 255, 0.1);
}

.lang-option.active {
  background: linear-gradient(135deg, #4c51bf 0%, #667eea 100%);
}

.lang-option:not(:last-child) {
  border-bottom: 1px solid rgba(255, 255, 255, 0.05);
}

.lang-flag {
  margin-right: 0.5rem;
  font-size: 1.1em;
}

.lang-name {
  flex: 1;
  text-align: left;
}

.check-icon {
  color: #4ade80;
  font-weight: bold;
  margin-left: 0.5rem;
}

/* 下拉动画 */
.dropdown-enter-active,
.dropdown-leave-active {
  transition: all 0.3s ease;
  transform-origin: top;
}

.dropdown-enter-from,
.dropdown-leave-to {
  opacity: 0;
  transform: scaleY(0.8) translateY(-10px);
}

.dropdown-enter-to,
.dropdown-leave-from {
  opacity: 1;
  transform: scaleY(1) translateY(0);
}

/* 移动端响应式 */
@media (max-width: 768px) {
  .mobile-menu-btn {
    display: flex;
  }

  .mobile-overlay {
    display: block;
  }

  main {
    padding: 2rem 0;
  }

  .language-switcher {
    width: 100%;
    margin: 1rem 0;
    padding: 0 2rem;
    border-top: 1px solid rgba(255, 255, 255, 0.1);
    padding-top: 1rem;
  }

  .current-language-btn {
    width: 100%;
    padding: 0.75rem 1rem;
    font-size: 1rem;
    min-width: auto;
  }

  .language-dropdown {
    position: static;
    margin-top: 0.75rem;
    box-shadow: 0 4px 16px rgba(0, 0, 0, 0.2);
  }

  .lang-option {
    padding: 1rem;
    font-size: 1rem;
  }
}
</style>
