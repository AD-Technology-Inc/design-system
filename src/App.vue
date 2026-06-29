<script setup>
import { ref, onMounted, computed } from "vue";

// Theme State
const theme = ref("system"); // 'light' | 'dark' | 'system'
const isThemeMenuOpen = ref(false);
const isResolvedDark = ref(false);
const hasSavedTheme = computed(() => localStorage.getItem("theme") !== null);

// UI States
const isDialogOpen = ref(false);
const isMobileSidebarOpen = ref(false);
const nameInputRef = ref(null);

// Users Data
const users = ref([
  { name: "John Doe", email: "john@mail.com", role: "Admin", status: "Active" },
  {
    name: "Jane Smith",
    email: "jane@mail.com",
    role: "User",
    status: "Pending",
  },
  {
    name: "Mike Ross",
    email: "mike@mail.com",
    role: "User",
    status: "Blocked",
  },
]);

const defaultUsers = [
  { name: "John Doe", email: "john@mail.com", role: "Admin", status: "Active" },
  {
    name: "Jane Smith",
    email: "jane@mail.com",
    role: "User",
    status: "Pending",
  },
  {
    name: "Mike Ross",
    email: "mike@mail.com",
    role: "User",
    status: "Blocked",
  },
];

// Form Input State
const newUser = ref({
  name: "",
  email: "",
  role: "User",
  active: true,
});

const userToDelete = ref(null);

// Theme Management Functions
const setTheme = (newTheme) => {
  theme.value = newTheme;
  localStorage.setItem("theme", newTheme);
  applyTheme();
};

const applyTheme = () => {
  const isDark =
    theme.value === "dark" ||
    (theme.value === "system" &&
      window.matchMedia("(prefers-color-scheme: dark)").matches);

  isResolvedDark.value = isDark;

  if (isDark) {
    document.documentElement.classList.add("dark");
  } else {
    document.documentElement.classList.remove("dark");
  }
};

// User Actions
const saveUser = () => {
  if (!newUser.value.name.trim() || !newUser.value.email.trim()) return;
  users.value.push({
    name: newUser.value.name,
    email: newUser.value.email,
    role: newUser.value.role,
    status: newUser.value.active ? "Active" : "Pending",
  });

  // Reset Form
  newUser.value = {
    name: "",
    email: "",
    role: "User",
    active: true,
  };
};

const confirmDelete = (index) => {
  userToDelete.value = index;
  isDialogOpen.value = true;
};

const deleteUser = () => {
  if (userToDelete.value !== null) {
    users.value.splice(userToDelete.value, 1);
    userToDelete.value = null;
  }
  isDialogOpen.value = false;
};

const restoreDefaultUsers = () => {
  users.value = [...defaultUsers];
};

const focusCreateInput = () => {
  if (nameInputRef.value) {
    nameInputRef.value.focus();
  }
};

const getBadgeClass = (status) => {
  switch (status.toLowerCase()) {
    case "active":
      return "badge-success";
    case "pending":
      return "badge-warning";
    case "blocked":
      return "badge-danger";
    default:
      return "";
  }
};

// Lifecycle Hooks
onMounted(() => {
  // Initialize theme
  const saved = localStorage.getItem("theme");
  if (saved) {
    theme.value = saved;
  }
  applyTheme();

  // Listen to system changes
  const mediaQuery = window.matchMedia("(prefers-color-scheme: dark)");
  mediaQuery.addEventListener("change", () => {
    if (theme.value === "system") {
      applyTheme();
    }
  });

  // Click outside to close dropdowns
  window.addEventListener("click", (e) => {
    if (!e.target.closest(".theme-selector-container")) {
      isThemeMenuOpen.value = false;
    }
  });
});
</script>

<template>
  <!-- APP SHELL -->
  <div class="app-shell">
    <!-- DESKTOP SIDEBAR -->
    <aside class="sidebar app-shell-sidebar">
      <div class="sidebar-header">
        <strong>Design System</strong>
      </div>

      <div class="sidebar-content">
        <nav class="sidebar-menu">
          <a class="sidebar-item active" href="#"> Dashboard </a>
          <a class="sidebar-item" href="#"> Users </a>
          <a class="sidebar-item" href="#"> Projects </a>
          <a class="sidebar-item" href="#"> Settings </a>
        </nav>
      </div>

      <div class="sidebar-footer">
        <button class="button button-outline button-sm w-full">Logout</button>
      </div>
    </aside>

    <!-- MOBILE SIDEBAR DRAWER -->
    <div
      v-if="isMobileSidebarOpen"
      class="fixed inset-0 z-50 flex md:hidden"
      role="dialog"
      aria-modal="true"
    >
      <!-- Overlay -->
      <div
        class="fixed inset-0 bg-black/60 backdrop-blur-sm"
        @click="isMobileSidebarOpen = false"
      ></div>

      <!-- Drawer Content -->
      <div
        class="relative flex w-full max-w-xs flex-1 flex-col bg-sidebar text-sidebar-foreground border-r border-sidebar-border h-full animate-in slide-in-from-left duration-200"
      >
        <!-- Close button inside header -->
        <div
          class="flex h-16 items-center justify-between px-6 border-b border-sidebar-border"
        >
          <strong>Design System</strong>
          <button
            @click="isMobileSidebarOpen = false"
            class="button button-outline button-sm px-2"
          >
            <svg
              xmlns="http://www.w3.org/2000/svg"
              width="16"
              height="16"
              fill="none"
              stroke="currentColor"
              stroke-width="2"
              viewBox="0 0 24 24"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M6 18 18 6M6 6l12 12"
              />
            </svg>
          </button>
        </div>

        <div class="flex-1 overflow-y-auto p-6">
          <nav class="sidebar-menu">
            <a
              class="sidebar-item active"
              href="#"
              @click="isMobileSidebarOpen = false"
            >
              Dashboard
            </a>
            <a
              class="sidebar-item"
              href="#"
              @click="isMobileSidebarOpen = false"
            >
              Users
            </a>
            <a
              class="sidebar-item"
              href="#"
              @click="isMobileSidebarOpen = false"
            >
              Projects
            </a>
            <a
              class="sidebar-item"
              href="#"
              @click="isMobileSidebarOpen = false"
            >
              Settings
            </a>
          </nav>
        </div>

        <div class="p-6 border-t border-sidebar-border">
          <button class="button button-outline button-sm w-full">Logout</button>
        </div>
      </div>
    </div>

    <!-- MAIN -->
    <div class="app-shell-main">
      <!-- NAVBAR -->
      <header class="navbar">
        <div class="flex items-center gap-3">
          <!-- Mobile Menu Trigger -->
          <button
            @click="isMobileSidebarOpen = true"
            class="md:hidden button button-outline button-sm px-2"
          >
            <svg
              xmlns="http://www.w3.org/2000/svg"
              width="18"
              height="18"
              fill="none"
              stroke="currentColor"
              stroke-width="2"
              viewBox="0 0 24 24"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M3.75 6.75h16.5M3.75 12h16.5m-16.5 5.25h16.5"
              />
            </svg>
          </button>
          <div class="navbar-brand">SaaS System</div>
        </div>

        <nav class="navbar-nav">
          <a class="navbar-link active hidden sm:inline-block" href="#">Home</a>
          <a class="navbar-link hidden sm:inline-block" href="#">Docs</a>
          <a class="navbar-link hidden sm:inline-block" href="#">API</a>

          <!-- Theme Toggler Dropdown -->
          <div class="theme-selector-container relative">
            <button
              @click="isThemeMenuOpen = !isThemeMenuOpen"
              class="button button-outline button-sm flex items-center justify-center p-2"
              aria-label="Toggle theme"
            >
              <!-- Sun Icon -->
              <svg
                v-if="theme === 'light'"
                xmlns="http://www.w3.org/2000/svg"
                width="16"
                height="16"
                viewBox="0 0 24 24"
                fill="none"
                stroke="currentColor"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
                class="h-4 w-4 text-amber-500"
              >
                <circle cx="12" cy="12" r="4" />
                <path d="M12 2v2" />
                <path d="M12 20v2" />
                <path d="m4.93 4.93 1.41 1.41" />
                <path d="m17.66 17.66 1.41 1.41" />
                <path d="M2 12h2" />
                <path d="M20 12h2" />
                <path d="m6.34 17.66-1.41 1.41" />
                <path d="m19.07 4.93-1.41 1.41" />
              </svg>
              <!-- Moon Icon -->
              <svg
                v-else-if="theme === 'dark'"
                xmlns="http://www.w3.org/2000/svg"
                width="16"
                height="16"
                viewBox="0 0 24 24"
                fill="none"
                stroke="currentColor"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
                class="h-4 w-4 text-violet-400"
              >
                <path d="M12 3a6 6 0 0 0 9 9 9 9 0 1 1-9-9Z" />
              </svg>
              <!-- Laptop / System Icon -->
              <svg
                v-else
                xmlns="http://www.w3.org/2000/svg"
                width="16"
                height="16"
                viewBox="0 0 24 24"
                fill="none"
                stroke="currentColor"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
                class="h-4 w-4"
              >
                <rect width="20" height="14" x="2" y="3" rx="2" />
                <line x1="8" x2="16" y1="21" y2="21" />
                <line x1="12" x2="12" y1="17" y2="21" />
              </svg>
            </button>

            <div
              v-if="isThemeMenuOpen"
              class="absolute right-0 mt-2 w-36 rounded-md border border-border bg-card p-1 shadow-md z-50 animate-in fade-in slide-in-from-top-1 duration-100"
            >
              <button
                @click="
                  setTheme('light');
                  isThemeMenuOpen = false;
                "
                :class="[
                  'w-full text-left px-2.5 py-1.5 text-sm rounded-sm hover:bg-accent hover:text-accent-foreground flex items-center gap-2',
                  theme === 'light'
                    ? 'bg-accent/50 text-foreground font-medium'
                    : 'text-muted-foreground',
                ]"
              >
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  width="14"
                  height="14"
                  viewBox="0 0 24 24"
                  fill="none"
                  stroke="currentColor"
                  stroke-width="2"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  class="h-3.5 w-3.5"
                >
                  <circle cx="12" cy="12" r="4" />
                  <path d="M12 2v2" />
                  <path d="M12 20v2" />
                  <path d="M2 12h2" />
                  <path d="M20 12h2" />
                </svg>
                Light
              </button>
              <button
                @click="
                  setTheme('dark');
                  isThemeMenuOpen = false;
                "
                :class="[
                  'w-full text-left px-2.5 py-1.5 text-sm rounded-sm hover:bg-accent hover:text-accent-foreground flex items-center gap-2',
                  theme === 'dark'
                    ? 'bg-accent/50 text-foreground font-medium'
                    : 'text-muted-foreground',
                ]"
              >
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  width="14"
                  height="14"
                  viewBox="0 0 24 24"
                  fill="none"
                  stroke="currentColor"
                  stroke-width="2"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  class="h-3.5 w-3.5"
                >
                  <path d="M12 3a6 6 0 0 0 9 9 9 9 0 1 1-9-9Z" />
                </svg>
                Dark
              </button>
              <button
                @click="
                  setTheme('system');
                  isThemeMenuOpen = false;
                "
                :class="[
                  'w-full text-left px-2.5 py-1.5 text-sm rounded-sm hover:bg-accent hover:text-accent-foreground flex items-center gap-2',
                  theme === 'system'
                    ? 'bg-accent/50 text-foreground font-medium'
                    : 'text-muted-foreground',
                ]"
              >
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  width="14"
                  height="14"
                  viewBox="0 0 24 24"
                  fill="none"
                  stroke="currentColor"
                  stroke-width="2"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  class="h-3.5 w-3.5"
                >
                  <rect width="20" height="14" x="2" y="3" rx="2" />
                  <line x1="12" x2="12" y1="17" y2="21" />
                </svg>
                System
              </button>
            </div>
          </div>
        </nav>
      </header>

      <!-- CONTENT -->
      <main class="app-shell-content">
        <!-- BREADCRUMB -->
        <div class="breadcrumb">
          <span class="breadcrumb-item"><a href="#">Home</a></span>
          <span class="breadcrumb-separator">/</span>
          <span class="breadcrumb-item"><a href="#">Dashboard</a></span>
          <span class="breadcrumb-separator">/</span>
          <span class="breadcrumb-item active">Overview</span>
        </div>

        <!-- ACTION BAR -->
        <div
          class="flex flex-col sm:flex-row sm:items-center sm:justify-between gap-4 my-6"
        >
          <div>
            <h2>Dashboard</h2>
            <p class="muted">Overview of system activity</p>
          </div>

          <div class="flex gap-2">
            <button class="button button-secondary">Export</button>
            <button @click="focusCreateInput" class="button button-primary">
              Create User
            </button>
          </div>
        </div>

        <!-- CARDS GRID -->
        <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8">
          <div class="card">
            <div class="card-header">
              <div class="card-title">Users</div>
              <div class="card-description">Active accounts</div>
            </div>
            <div class="card-content">
              <h3>{{ users.length }}</h3>
            </div>
          </div>

          <div class="card">
            <div class="card-header">
              <div class="card-title">Revenue</div>
              <div class="card-description">Monthly income</div>
            </div>
            <div class="card-content">
              <h3>$12,400</h3>
            </div>
          </div>

          <div class="card">
            <div class="card-header">
              <div class="card-title">Errors</div>
              <div class="card-description">System issues</div>
            </div>
            <div class="card-content">
              <h3>3</h3>
            </div>
          </div>
        </div>

        <!-- SPLIT LAYOUT FOR FORM & THEME TESTBED -->
        <div class="grid grid-cols-1 lg:grid-cols-2 gap-6 mb-8">
          <!-- FORM CARD -->
          <div class="card">
            <div class="card-header">
              <div class="card-title">Create User</div>
              <div class="card-description">Add a new account</div>
            </div>

            <form
              @submit.prevent="saveUser"
              class="card-content flex flex-col gap-4"
            >
              <div class="form-group">
                <label class="form-label">Name</label>
                <input
                  ref="nameInputRef"
                  v-model="newUser.name"
                  class="form-input"
                  type="text"
                  placeholder="John Doe"
                  required
                />
                <span class="form-helper">Full name of the user</span>
              </div>

              <div class="form-group">
                <label class="form-label">Email</label>
                <input
                  v-model="newUser.email"
                  class="form-input"
                  type="email"
                  placeholder="john@example.com"
                  required
                />
              </div>

              <div class="form-group">
                <label class="form-label">Role</label>
                <select v-model="newUser.role" class="form-select">
                  <option value="User">User</option>
                  <option value="Admin">Admin</option>
                </select>
              </div>

              <div class="form-group">
                <label class="form-checkbox-wrapper">
                  <input
                    v-model="newUser.active"
                    type="checkbox"
                    class="form-checkbox"
                  />
                  Active account
                </label>
              </div>

              <div class="flex justify-end gap-3 mt-2">
                <button
                  type="button"
                  @click="
                    newUser = {
                      name: '',
                      email: '',
                      role: 'User',
                      active: true,
                    }
                  "
                  class="button button-secondary"
                >
                  Clear
                </button>
                <button type="submit" class="button button-primary">
                  Save
                </button>
              </div>
            </form>
          </div>

          <!-- THEME TESTBED CARD -->
          <div class="card">
            <div class="card-header">
              <div class="card-title">Theme Controller & Tokens</div>
              <div class="card-description">
                Inspect active colors and test mode transitions.
              </div>
            </div>

            <div class="card-content flex flex-col gap-6">
              <!-- Segmented Switcher -->
              <div class="flex flex-col gap-2">
                <span class="form-label">Select Active Mode</span>
                <div
                  class="grid grid-cols-3 gap-2 p-1 bg-muted rounded-lg border border-border"
                >
                  <button
                    type="button"
                    @click="setTheme('light')"
                    :class="[
                      'button button-sm flex items-center justify-center gap-2 border-transparent',
                      theme === 'light'
                        ? 'bg-card text-foreground shadow-sm font-semibold'
                        : 'bg-transparent text-muted-foreground hover:text-foreground',
                    ]"
                  >
                    ☀️ Light
                  </button>
                  <button
                    type="button"
                    @click="setTheme('dark')"
                    :class="[
                      'button button-sm flex items-center justify-center gap-2 border-transparent',
                      theme === 'dark'
                        ? 'bg-card text-foreground shadow-sm font-semibold'
                        : 'bg-transparent text-muted-foreground hover:text-foreground',
                    ]"
                  >
                    🌙 Dark
                  </button>
                  <button
                    type="button"
                    @click="setTheme('system')"
                    :class="[
                      'button button-sm flex items-center justify-center gap-2 border-transparent',
                      theme === 'system'
                        ? 'bg-card text-foreground shadow-sm font-semibold'
                        : 'bg-transparent text-muted-foreground hover:text-foreground',
                    ]"
                  >
                    💻 System
                  </button>
                </div>
              </div>

              <!-- Active Status Info -->
              <div
                class="rounded-lg border border-border bg-muted/40 p-4 text-sm flex flex-col gap-1.5"
              >
                <div class="flex justify-between">
                  <span class="text-muted-foreground">Configured Theme:</span>
                  <span class="font-medium capitalize text-foreground">{{
                    theme
                  }}</span>
                </div>
                <div class="flex justify-between">
                  <span class="text-muted-foreground">Resolved App State:</span>
                  <span
                    class="font-medium flex items-center gap-1.5 text-foreground"
                  >
                    <span
                      :class="[
                        'h-2 w-2 rounded-full',
                        isResolvedDark ? 'bg-violet-500' : 'bg-amber-500',
                      ]"
                    ></span>
                    {{ isResolvedDark ? "Dark Mode" : "Light Mode" }}
                  </span>
                </div>
                <div class="flex justify-between">
                  <span class="text-muted-foreground"
                    >Persistence (LocalStorage):</span
                  >
                  <span class="font-mono text-xs text-foreground">{{
                    hasSavedTheme
                      ? `'theme' = "${theme}"`
                      : "None (System Default)"
                  }}</span>
                </div>
              </div>

              <!-- Color Palette Swatches -->
              <div class="flex flex-col gap-3">
                <span class="form-label">Design Tokens</span>
                <div class="grid grid-cols-2 sm:grid-cols-3 gap-3">
                  <div
                    class="flex flex-col gap-1.5 p-2 rounded-md border border-border bg-card"
                  >
                    <div
                      class="h-8 rounded w-full border border-border"
                      style="background-color: var(--background)"
                    ></div>
                    <span class="text-xs font-mono font-medium text-foreground"
                      >--background</span
                    >
                  </div>
                  <div
                    class="flex flex-col gap-1.5 p-2 rounded-md border border-border bg-card"
                  >
                    <div
                      class="h-8 rounded w-full border border-border"
                      style="background-color: var(--foreground)"
                    ></div>
                    <span class="text-xs font-mono font-medium text-foreground"
                      >--foreground</span
                    >
                  </div>
                  <div
                    class="flex flex-col gap-1.5 p-2 rounded-md border border-border bg-card"
                  >
                    <div
                      class="h-8 rounded w-full border border-border"
                      style="background-color: var(--card)"
                    ></div>
                    <span class="text-xs font-mono font-medium text-foreground"
                      >--card</span
                    >
                  </div>
                  <div
                    class="flex flex-col gap-1.5 p-2 rounded-md border border-border bg-card"
                  >
                    <div
                      class="h-8 rounded w-full border border-border"
                      style="background-color: var(--primary)"
                    ></div>
                    <span class="text-xs font-mono font-medium text-foreground"
                      >--primary</span
                    >
                  </div>
                  <div
                    class="flex flex-col gap-1.5 p-2 rounded-md border border-border bg-card"
                  >
                    <div
                      class="h-8 rounded w-full border border-border"
                      style="background-color: var(--secondary)"
                    ></div>
                    <span class="text-xs font-mono font-medium text-foreground"
                      >--secondary</span
                    >
                  </div>
                  <div
                    class="flex flex-col gap-1.5 p-2 rounded-md border border-border bg-card"
                  >
                    <div
                      class="h-8 rounded w-full border border-border"
                      style="background-color: var(--border)"
                    ></div>
                    <span class="text-xs font-mono font-medium text-foreground"
                      >--border</span
                    >
                  </div>
                  <div
                    class="flex flex-col gap-1.5 p-2 rounded-md border border-border bg-card"
                  >
                    <div
                      class="h-8 rounded w-full border border-border"
                      style="background-color: var(--success)"
                    ></div>
                    <span class="text-xs font-mono font-medium text-foreground"
                      >--success</span
                    >
                  </div>
                  <div
                    class="flex flex-col gap-1.5 p-2 rounded-md border border-border bg-card"
                  >
                    <div
                      class="h-8 rounded w-full border border-border"
                      style="background-color: var(--warning)"
                    ></div>
                    <span class="text-xs font-mono font-medium text-foreground"
                      >--warning</span
                    >
                  </div>
                  <div
                    class="flex flex-col gap-1.5 p-2 rounded-md border border-border bg-card"
                  >
                    <div
                      class="h-8 rounded w-full border border-border"
                      style="background-color: var(--info)"
                    ></div>
                    <span class="text-xs font-mono font-medium text-foreground"
                      >--info</span
                    >
                  </div>
                  <div
                    class="flex flex-col gap-1.5 p-2 rounded-md border border-border bg-card"
                  >
                    <div
                      class="h-8 rounded w-full border border-border"
                      style="background-color: var(--destructive)"
                    ></div>
                    <span class="text-xs font-mono font-medium text-foreground"
                      >--danger</span
                    >
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- STATE-BASED COMPONENTS SHOWCASE -->
        <div class="card mb-8">
          <div class="card-header">
            <div class="card-title">State-Based Component System</div>
            <div class="card-description">
              Showcase of unified status states (Success, Info, Warning, Danger) across Badges, Buttons, and Alerts.
            </div>
          </div>
          <div class="card-content flex flex-col gap-6">
            <!-- Badges & Buttons Showcase -->
            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
              <div>
                <span class="form-label mb-2 block">Status Badges</span>
                <div class="flex flex-wrap gap-2">
                  <span class="badge badge-success">Active / Success</span>
                  <span class="badge badge-info">Tip / Info</span>
                  <span class="badge badge-warning">Pending / Warning</span>
                  <span class="badge badge-danger">Blocked / Danger</span>
                </div>
              </div>
              <div>
                <span class="form-label mb-2 block">Action Buttons</span>
                <div class="flex flex-wrap gap-2">
                  <button class="button button-success">Success</button>
                  <button class="button button-info">Info</button>
                  <button class="button button-warning">Warning</button>
                  <button class="button button-danger">Danger</button>
                </div>
              </div>
            </div>

            <!-- Alerts Showcase -->
            <div class="flex flex-col gap-3">
              <span class="form-label">Alert Banners (New Reusable Component)</span>
              <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                <div class="alert alert-success">
                  <span class="text-lg leading-none">✅</span>
                  <div class="alert-content">
                    <div class="alert-title">Success Alert</div>
                    <div class="alert-description">Changes have been saved successfully to the cloud.</div>
                  </div>
                </div>

                <div class="alert alert-info">
                  <span class="text-lg leading-none">ℹ️</span>
                  <div class="alert-content">
                    <div class="alert-title">Info Notice</div>
                    <div class="alert-description">A new system update is available. Check the logs.</div>
                  </div>
                </div>

                <div class="alert alert-warning">
                  <span class="text-lg leading-none">⚠️</span>
                  <div class="alert-content">
                    <div class="alert-title">Warning Alert</div>
                    <div class="alert-description">Your session will expire in 5 minutes due to inactivity.</div>
                  </div>
                </div>

                <div class="alert alert-danger">
                  <span class="text-lg leading-none">🚨</span>
                  <div class="alert-content">
                    <div class="alert-title">Danger Alert</div>
                    <div class="alert-description">Failed to save changes. Please check connection.</div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- TABLE -->
        <div v-if="users.length > 0" class="card">
          <div class="card-header">
            <div class="card-title">Users</div>
            <div class="card-description">Manage system users</div>
          </div>

          <div class="card-content">
            <div class="table-container">
              <table class="table table-hover table-striped">
                <thead>
                  <tr>
                    <th>Name</th>
                    <th>Email</th>
                    <th>Role</th>
                    <th>Status</th>
                    <th class="w-[100px]">Actions</th>
                  </tr>
                </thead>

                <tbody>
                  <tr v-for="(user, index) in users" :key="index">
                    <td>{{ user.name }}</td>
                    <td>{{ user.email }}</td>
                    <td>{{ user.role }}</td>
                    <td>
                      <span :class="['badge', getBadgeClass(user.status)]">{{
                        user.status
                      }}</span>
                    </td>
                    <td>
                      <button
                        @click="confirmDelete(index)"
                        class="button button-outline button-sm text-destructive hover:bg-destructive/10 border-transparent hover:border-destructive/20"
                      >
                        Delete
                      </button>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>

        <!-- EMPTY STATE (Reactively bound) -->
        <div v-else class="card mt-8">
          <div class="empty-state">
            <div class="empty-state-icon">📦</div>
            <div class="empty-state-title">No records yet</div>
            <div class="empty-state-description">
              Start by creating your first entry in the system.
            </div>

            <div class="empty-state-actions">
              <button @click="focusCreateInput" class="button button-primary">
                Create User
              </button>
              <button
                @click="restoreDefaultUsers"
                class="button button-outline"
              >
                Restore Defaults
              </button>
            </div>
          </div>
        </div>
      </main>
    </div>
  </div>

  <!-- DIALOG overlay + modal -->
  <div
    v-if="isDialogOpen"
    class="dialog-overlay"
    @click.self="isDialogOpen = false"
  >
    <div class="dialog">
      <div class="dialog-header">
        <div class="dialog-title">Delete Item</div>
        <div class="dialog-description">
          This action cannot be undone. Are you sure you want to delete this
          user?
        </div>
      </div>

      <div class="dialog-footer">
        <button class="button button-secondary" @click="isDialogOpen = false">
          Cancel
        </button>
        <button class="button button-danger" @click="deleteUser">Delete</button>
      </div>
    </div>
  </div>
</template>
