<template>
  <aside class="sidebar" :class="{ collapsed: isCollapsed }">
    <div class="sidebar-brand">
      <div class="brand-text">
        <h1 class="brand-name">{{ t('nav.companyName') }}</h1>
        <span class="brand-sub">{{ t('nav.subtitle') }}</span>
      </div>
      <button class="collapse-btn" @click="toggleCollapsed" :title="isCollapsed ? 'Expand sidebar' : 'Collapse sidebar'">
        <svg width="16" height="16" viewBox="0 0 16 16" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round">
          <path v-if="!isCollapsed" d="M10 12L6 8L10 4"/>
          <path v-else d="M6 4L10 8L6 12"/>
        </svg>
      </button>
    </div>

    <nav class="sidebar-nav">
      <router-link to="/" :class="{ active: $route.path === '/' }" data-tooltip="Overview">
        <svg width="18" height="18" viewBox="0 0 18 18" fill="none" stroke="currentColor" stroke-width="1.5">
          <path d="M3 7.5L9 2L15 7.5V15.5H11.5V11H6.5V15.5H3V7.5Z" stroke-linejoin="round"/>
        </svg>
        <span>{{ t('nav.overview') }}</span>
      </router-link>
      <router-link to="/inventory" :class="{ active: $route.path === '/inventory' }" data-tooltip="Inventory">
        <svg width="18" height="18" viewBox="0 0 18 18" fill="none" stroke="currentColor" stroke-width="1.5">
          <path d="M3 7L9 4L15 7V14L9 17L3 14V7Z"/>
          <path d="M9 4V17"/>
          <path d="M3 7L15 7"/>
        </svg>
        <span>{{ t('nav.inventory') }}</span>
      </router-link>
      <router-link to="/orders" :class="{ active: $route.path === '/orders' }" data-tooltip="Orders">
        <svg width="18" height="18" viewBox="0 0 18 18" fill="none" stroke="currentColor" stroke-width="1.5">
          <rect x="3" y="2" width="12" height="14" rx="1.5"/>
          <path d="M6 6H12M6 9H12M6 12H9"/>
        </svg>
        <span>{{ t('nav.orders') }}</span>
      </router-link>
      <router-link to="/spending" :class="{ active: $route.path === '/spending' }" data-tooltip="Finance">
        <svg width="18" height="18" viewBox="0 0 18 18" fill="none" stroke="currentColor" stroke-width="1.5">
          <path d="M3 14V9M7 14V5M11 14V10M15 14V3" stroke-linecap="round"/>
        </svg>
        <span>{{ t('nav.finance') }}</span>
      </router-link>
      <router-link to="/demand" :class="{ active: $route.path === '/demand' }" data-tooltip="Demand Forecast">
        <svg width="18" height="18" viewBox="0 0 18 18" fill="none" stroke="currentColor" stroke-width="1.5">
          <path d="M2 13L7 8L11 11L16 5" stroke-linecap="round" stroke-linejoin="round"/>
          <path d="M12 5H16V9" stroke-linecap="round" stroke-linejoin="round"/>
        </svg>
        <span>{{ t('nav.demandForecast') }}</span>
      </router-link>
      <router-link to="/reports" :class="{ active: $route.path === '/reports' }" data-tooltip="Reports">
        <svg width="18" height="18" viewBox="0 0 18 18" fill="none" stroke="currentColor" stroke-width="1.5">
          <path d="M4 2H11L15 6V16H4V2Z" stroke-linejoin="round"/>
          <path d="M11 2V6H15"/>
          <path d="M7 9H12M7 12H10" stroke-linecap="round"/>
        </svg>
        <span>Reports</span>
      </router-link>
    </nav>

    <div class="sidebar-footer">
      <LanguageSwitcher />
      <ProfileMenu
        @show-profile-details="emit('show-profile-details')"
        @show-tasks="emit('show-tasks')"
      />
    </div>
  </aside>
</template>

<script>
import { ref, onMounted, onUnmounted } from 'vue'
import { useI18n } from '../composables/useI18n'
import LanguageSwitcher from './LanguageSwitcher.vue'
import ProfileMenu from './ProfileMenu.vue'

export default {
  name: 'Sidebar',
  components: { LanguageSwitcher, ProfileMenu },
  emits: ['show-profile-details', 'show-tasks'],
  setup(props, { emit }) {
    const { t } = useI18n()
    const isCollapsed = ref(false)

    const toggleCollapsed = () => {
      isCollapsed.value = !isCollapsed.value
    }

    // Auto-collapse on small screens
    const handleResize = () => {
      isCollapsed.value = window.innerWidth < 1024
    }

    onMounted(() => {
      handleResize()
      window.addEventListener('resize', handleResize)
    })

    onUnmounted(() => {
      window.removeEventListener('resize', handleResize)
    })

    return { t, emit, isCollapsed, toggleCollapsed }
  }
}
</script>

<style scoped>
.sidebar {
  width: 260px;
  min-width: 260px;
  background: #0f172a;
  border-right: 1px solid rgba(255, 255, 255, 0.06);
  display: flex;
  flex-direction: column;
  height: 100vh;
  position: sticky;
  top: 0;
  overflow-y: auto;
  transition: width 0.2s ease, min-width 0.2s ease;
}

.sidebar-brand {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 1.5rem 1.25rem 1.25rem;
  border-bottom: 1px solid rgba(255, 255, 255, 0.08);
  flex-shrink: 0;
}

.brand-name {
  font-size: 1.125rem;
  font-weight: 700;
  color: #ffffff;
  letter-spacing: -0.025em;
  margin-bottom: 0.25rem;
}

.brand-sub {
  font-size: 0.75rem;
  color: #94a3b8;
  font-weight: 400;
  display: block;
}

.sidebar-nav {
  flex: 1;
  padding: 0.75rem;
  display: flex;
  flex-direction: column;
  gap: 0.125rem;
  overflow-y: auto;
}

.sidebar-nav a {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 0.625rem 0.875rem;
  color: #94a3b8;
  text-decoration: none;
  font-size: 0.875rem;
  font-weight: 500;
  border-radius: 6px;
  border-left: 3px solid transparent;
  transition: all 0.15s ease;
  margin-left: -3px;
}

.sidebar-nav a:hover {
  color: #e2e8f0;
  background: rgba(255, 255, 255, 0.06);
}

.sidebar-nav a.active {
  color: #ffffff;
  background: rgba(37, 99, 235, 0.18);
  border-left-color: #2563eb;
}

.sidebar-nav a svg {
  flex-shrink: 0;
  opacity: 0.8;
}

.sidebar-nav a.active svg {
  opacity: 1;
}

.sidebar-footer {
  padding: 1rem 0.875rem;
  border-top: 1px solid rgba(255, 255, 255, 0.08);
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  flex-shrink: 0;
}

/* Flip dropdowns to open upward — both ProfileMenu and LanguageSwitcher use .dropdown-menu with top: calc(100% + 0.5rem) which clips at bottom of viewport */
.sidebar-footer :deep(.dropdown-menu) {
  top: auto;
  bottom: calc(100% + 0.5rem);
  left: 0;
  right: auto;
  min-width: 200px;
}

/* Style buttons for the dark sidebar background */
.sidebar-footer :deep(.language-button),
.sidebar-footer :deep(.profile-button) {
  background: rgba(255, 255, 255, 0.06);
  border-color: rgba(255, 255, 255, 0.12);
  color: #e2e8f0;
  width: 100%;
  justify-content: flex-start;
}

.sidebar-footer :deep(.language-button):hover,
.sidebar-footer :deep(.profile-button):hover {
  background: rgba(255, 255, 255, 0.1);
}

.sidebar-footer :deep(.profile-name) {
  color: #e2e8f0;
}

.sidebar-footer :deep(.globe-icon) {
  color: #94a3b8;
}

.sidebar-footer :deep(.chevron) {
  color: #94a3b8;
  margin-left: auto;
}

/* ── Collapsed state ── */

.sidebar.collapsed {
  width: 64px;
  min-width: 64px;
}

/* Brand: center the toggle button, hide text */
.sidebar.collapsed .sidebar-brand {
  justify-content: center;
  padding: 1.25rem 0;
}

.sidebar.collapsed .brand-text {
  display: none;
}

/* Nav links: center icons, hide labels */
.sidebar.collapsed .sidebar-nav {
  padding: 0.75rem 0.5rem;
  align-items: center;
}

.sidebar.collapsed .sidebar-nav a {
  justify-content: center;
  padding: 0.75rem;
  gap: 0;
  margin-left: 0;
  border-left: none;
  border-radius: 8px;
  position: relative;
}

.sidebar.collapsed .sidebar-nav a.active {
  background: rgba(37, 99, 235, 0.25);
}

/* Blue dot indicator replaces left border in collapsed mode */
.sidebar.collapsed .sidebar-nav a.active::before {
  content: '';
  position: absolute;
  left: 0;
  top: 50%;
  transform: translateY(-50%);
  width: 3px;
  height: 60%;
  background: #2563eb;
  border-radius: 0 2px 2px 0;
}

.sidebar.collapsed .sidebar-nav a span {
  display: none;
}

/* Tooltip on hover in collapsed mode */
.sidebar.collapsed .sidebar-nav a::after {
  content: attr(data-tooltip);
  position: absolute;
  left: calc(100% + 10px);
  top: 50%;
  transform: translateY(-50%);
  background: #1e293b;
  color: #e2e8f0;
  padding: 0.375rem 0.75rem;
  border-radius: 6px;
  font-size: 0.8125rem;
  font-weight: 500;
  white-space: nowrap;
  opacity: 0;
  pointer-events: none;
  transition: opacity 0.15s ease;
  z-index: 200;
  border: 1px solid rgba(255, 255, 255, 0.1);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
}

.sidebar.collapsed .sidebar-nav a:hover::after {
  opacity: 1;
}

/* Footer: icon-only buttons */
.sidebar.collapsed .sidebar-footer {
  padding: 1rem 0.5rem;
  align-items: center;
}

.sidebar.collapsed .sidebar-footer :deep(.language-button),
.sidebar.collapsed .sidebar-footer :deep(.profile-button) {
  justify-content: center;
  padding: 0.625rem;
  width: 100%;
}

.sidebar.collapsed .sidebar-footer :deep(.language-label),
.sidebar.collapsed .sidebar-footer :deep(.chevron) {
  display: none;
}

.sidebar.collapsed .sidebar-footer :deep(.profile-name) {
  display: none;
}

/* ── Toggle button ── */

.brand-text {
  min-width: 0;
  flex: 1;
}

.collapse-btn {
  flex-shrink: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 28px;
  height: 28px;
  background: rgba(255, 255, 255, 0.06);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 6px;
  color: #94a3b8;
  cursor: pointer;
  transition: all 0.15s ease;
  padding: 0;
}

.collapse-btn:hover {
  background: rgba(255, 255, 255, 0.12);
  color: #e2e8f0;
}

.sidebar.collapsed .collapse-btn {
  margin: 0 auto;
}
</style>
