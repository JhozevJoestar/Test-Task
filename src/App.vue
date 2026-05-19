<script setup>
import { ref, computed, onMounted, onUnmounted } from "vue";

// Helpers

function isoToMin(iso) {
  const m = iso.match(/T(\d{2}):(\d{2})/);
  return m ? +m[1] * 60 + +m[2] : 0;
}
function timeStrToMin(s) {
  const [h, m] = s.split(":").map(Number);
  return h * 60 + m;
}
function minToStr(min) {
  return `${String(Math.floor(min / 60)).padStart(2, "0")}:${String(min % 60).padStart(2, "0")}`;
}

// API

const data = ref(null);
const loading = ref(true);
const error = ref(null);

// State

const selectedDate = ref("");
const activeZones = ref([]);
const currentTime = ref(new Date());
const isDark = ref(true);

function toggleTheme() {
  isDark.value = !isDark.value;
  document.documentElement.classList.toggle("dark", isDark.value);
}

let timer = null;

onMounted(async () => {
  document.documentElement.classList.toggle("dark", isDark.value);

  try {
    const res = await fetch("https://hh.frontend.ark.software/api/booking");
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    data.value = await res.json();
    selectedDate.value = data.value.current_day;
    activeZones.value = [...allZones.value];
  } catch (e) {
    error.value = e.message;
  } finally {
    loading.value = false;
  }
  timer = setInterval(() => {
    currentTime.value = new Date();
  }, 60000);
});

onUnmounted(() => clearInterval(timer));

// Constants

const SLOT_H = 40;
const COL_W = 80;
const OVERLAP_INDENT = 4;
const INTERSECTION_WINDOW_MIN = 30;

const MONTH_RU = [
  "января",
  "февраля",
  "марта",
  "апреля",
  "мая",
  "июня",
  "июля",
  "августа",
  "сентября",
  "октября",
  "ноября",
  "декабря",
];
const DAY_RU = [
  "воскресенье",
  "понедельник",
  "вторник",
  "среда",
  "четверг",
  "пятница",
  "суббота",
];

const STATUS_CFG_LIGHT = {
  order: {
    New: { bg: "rgba(127, 215, 204, 0.16)", border: "#7fd7cc", label: "Новый", badgeBg: "rgba(255,255,255,0.12)" },
    Bill: { bg: "rgba(127, 215, 204, 0.16)", border: "#7fd7cc", label: "Пречек", badgeBg: "rgba(74,201,155,0.32)" },
    Closed: {
      bg: "rgba(127, 215, 204, 0.16)",
      border: "#7fd7cc",
      label: "Закрытый",
      badgeBg: "rgba(255,255,255,0.12)",
    },
    Banquet: {
      bg: "rgba(179, 72, 247, 0.16)",
      border: "#7b439e",
      label: "Банкет",
      badgeBg: "transparent",
    },
  },
  reservation: {
    "Живая очередь": {
      bg: "rgba(0, 151, 253, 0.16)",
      border: "#007aff",
      label: "Живая очередь",
      badgeBg: "rgba(255,255,255,0.12)",
    },
    Новая: { bg: "rgba(255, 112, 67, 0.16)", border: "#ff7043", label: "Ожидает подтверждения", badgeBg: "#007aff" },
    Заявка: {
      bg: "rgba(255, 112, 67, 0.16)",
      border: "#ff7043",
      label: "Ожидаем",
      badgeBg: "#007aff",
    },
    Открыт: {
      bg: "rgba(255, 112, 67, 0.16)",
      border: "#ff7043",
      label: "В зале",
      badgeBg: "rgba(74,201,155,0.32)",
    },
    Закрыт: {
      bg: "rgba(255, 112, 67, 0.16)",
      border: "#ff7043",
      label: "Закрыт",
      badgeBg: "rgba(255,255,255,0.12)",
    },
    Отменен: {
      bg: "rgba(255, 112, 67, 0.16)",
      border: "#ff7043",
      label: "Отменен",
      badgeBg: "rgba(255,255,255,0.12)",
    },
  },
};

const STATUS_CFG_DARK = {
  order: {
    New: { bg: "rgba(127, 215, 204, 0.16)", border: "#7fd7cc", label: "Новый", badgeBg: "rgba(255,255,255,0.12)" },
    Bill: { bg: "rgba(127, 215, 204, 0.16)", border: "#7fd7cc", label: "Пречек", badgeBg: "rgba(74,201,155,0.32)" },
    Closed: {
      bg: "rgba(127, 215, 204, 0.16)",
      border: "#7fd7cc",
      label: "Закрытый",
      badgeBg: "rgba(255,255,255,0.12)",
    },
    Banquet: {
      bg: "rgba(179, 72, 247, 0.16)",
      border: "#7b439e",
      label: "Банкет",
      badgeBg: "transparent",
    },
  },
  reservation: {
    "Живая очередь": {
      bg: "rgba(0, 151, 253, 0.16)",
      border: "#007aff",
      label: "Живая очередь",
      badgeBg: "rgba(255,255,255,0.12)",
    },
    Новая: { bg: "rgba(255, 112, 67, 0.16)", border: "#ff7043", label: "Ожидает подтверждения", badgeBg: "#007aff" },
    Заявка: {
      bg: "rgba(255, 112, 67, 0.16)",
      border: "#ff7043",
      label: "Ожидаем",
      badgeBg: "#007aff",
    },
    Открыт: {
      bg: "rgba(255, 112, 67, 0.16)",
      border: "#ff7043",
      label: "В зале",
      badgeBg: "rgba(74,201,155,0.32)",
    },
    Закрыт: {
      bg: "rgba(255, 112, 67, 0.16)",
      border: "#ff7043",
      label: "Закрыт",
      badgeBg: "rgba(255,255,255,0.12)",
    },
    Отменен: {
      bg: "rgba(255, 112, 67, 0.16)",
      border: "#ff7043",
      label: "Отменен",
      badgeBg: "rgba(255,255,255,0.12)",
    },
  },
};

const STATUS_CFG = computed(() =>
  isDark.value ? STATUS_CFG_DARK : STATUS_CFG_LIGHT,
);

// Computed

const openMin = computed(() =>
  data.value ? timeStrToMin(data.value.restaurant.opening_time) : 660,
);
const closeMin = computed(() =>
  data.value ? timeStrToMin(data.value.restaurant.closing_time) : 1420,
);

const allZones = computed(() => {
  if (!data.value) return [];
  return [...new Set(data.value.tables.map((t) => t.zone))];
});

const timeSlots = computed(() => {
  const slots = [];
  for (let m = openMin.value; m < closeMin.value; m += 30)
    slots.push(minToStr(m));
  return slots;
});

const filteredTables = computed(() => {
  if (!data.value) return [];
  return data.value.tables.filter((t) => activeZones.value.includes(t.zone));
});

const nowMin = computed(() => {
  if (!data.value) return null;
  try {
    const tz = data.value.restaurant.timezone;
    const str = currentTime.value.toLocaleTimeString("ru", {
      timeZone: tz,
      hour12: false,
      hour: "2-digit",
      minute: "2-digit",
    });
    const [h, m] = str.split(":").map(Number);
    return h * 60 + m;
  } catch {
    const d = currentTime.value;
    return d.getHours() * 60 + d.getMinutes();
  }
});

const nowLineTop = computed(() => {
  const min = nowMin.value;
  if (min === null || min < openMin.value || min > closeMin.value) return null;
  return ((min - openMin.value) / 30) * SLOT_H;
});

// Methods

function parseDateInfo(dateStr) {
  const [y, mo, d] = dateStr.split("-").map(Number);
  const date = new Date(y, mo - 1, d);
  const isToday = dateStr === data.value?.current_day;
  const idx = data.value?.available_days.indexOf(dateStr) ?? -1;
  const sub = isToday
    ? "сегодня"
    : idx === 1
      ? "завтра"
      : DAY_RU[date.getDay()];
  return { day: d, month: MONTH_RU[mo - 1], sub };
}

function toggleZone(zone) {
  const idx = activeZones.value.indexOf(zone);
  if (idx >= 0) {
    activeZones.value.splice(idx, 1);
  } else {
    activeZones.value.push(zone);
  }
}

function onGridScroll() {
  if (timeColEl.value && gridScrollEl.value) {
    timeColEl.value.scrollTop = gridScrollEl.value.scrollTop;
  }
}

const timeColEl = ref(null);
const gridScrollEl = ref(null);

function getBookings(table) {
  const raw = [
    ...table.orders.map((o) => ({
      key: `o-${o.id}`,
      kind: "order",
      status: o.status,
      startMin: isoToMin(o.start_time),
      endMin: isoToMin(o.end_time),
    })),
    ...table.reservations.map((r) => ({
      key: `r-${r.id}`,
      kind: "reservation",
      status: r.status,
      resId: r.id,
      name: r.name_for_reservation,
      people: r.num_people,
      phone: r.phone_number?.replace(/\D/g, "").slice(-4),
      startMin: isoToMin(r.seating_time),
      endMin: isoToMin(r.end_time),
    })),
  ].sort((a, b) => a.startMin - b.startMin);

  for (const b of raw) {
    b.col = 0;
    b.totalCols = 1;
    b.indent = 0;
    b.visibleContentHeight = null;
  }

  const intersected = new Set();
  for (const b of raw) {
    for (const o of raw) {
      if (
        o !== b &&
        Math.abs(o.startMin - b.startMin) <= INTERSECTION_WINDOW_MIN
      ) {
        intersected.add(b);
        intersected.add(o);
      }
    }
  }

  const groups = [];
  for (const b of raw.filter((item) => intersected.has(item))) {
    const group = groups.find((g) =>
      g.some(
        (item) =>
          Math.abs(item.startMin - b.startMin) <= INTERSECTION_WINDOW_MIN,
      ),
    );

    if (group) {
      group.push(b);
    } else {
      groups.push([b]);
    }
  }

  for (const group of groups) {
    const cols = [];
    for (const b of group.sort((a, b) => a.startMin - b.startMin)) {
      let col = cols.findIndex((endMin) => endMin <= b.startMin);
      if (col === -1) {
        col = cols.length;
        cols.push(b.endMin);
      } else {
        cols[col] = b.endMin;
      }
      b.col = col;
    }

    const totalCols = Math.max(cols.length, 1);
    for (const b of group) {
      b.totalCols = totalCols;
    }
  }

  for (const b of raw) {
    const overlapSource = raw
      .filter(
        (o) =>
          o !== b &&
          o.startMin < b.startMin &&
          o.endMin > b.startMin &&
          b.totalCols === 1 &&
          o.totalCols === 1,
      )
      .sort((a, b) => b.startMin - a.startMin)[0];

    b.indent = overlapSource ? overlapSource.indent + OVERLAP_INDENT : 0;

    const nextOverlap = raw
      .filter(
        (o) =>
          o !== b &&
          o.startMin > b.startMin &&
          o.startMin < b.endMin &&
          o.col === b.col,
      )
      .sort((a, b) => a.startMin - b.startMin)[0];

    if (nextOverlap) {
      b.visibleContentHeight = Math.max(
        ((nextOverlap.startMin - b.startMin) / 30) * SLOT_H - 7,
        28,
      );
    }
  }

  return raw;
}

function bookingStyle(b) {
  const cfg = (STATUS_CFG.value[b.kind] || {})[b.status] || {
    bg: "#13161e",
    border: "#323848",
    lc: "rgba(255,255,255,0.3)",
  };
  const colW = 100 / b.totalCols;
  const indent = b.indent || 0;
  const height = Math.max(((b.endMin - b.startMin) / 30) * SLOT_H - 2, 18);
  return {
    position: "absolute",
    top: `${((b.startMin - openMin.value) / 30) * SLOT_H}px`,
    height: `${height}px`,
    left:
      b.totalCols > 1
        ? `calc(${b.col * colW}% + ${indent}px)`
        : `${indent}px`,
    width:
      b.totalCols > 1
        ? `calc(${colW}% - ${indent + 1}px)`
        : `calc(100% - ${indent + 1}px)`,
    backgroundColor: cfg.bg,
    borderLeft: `2px solid ${cfg.border}`,
    "--booking-bg": cfg.bg,
    "--lc": cfg.lc || "#fff",
    "--badge-bg": cfg.badgeBg || "rgba(255,255,255,0.22)",
    "--booking-height": `${height}px`,
    "--visible-content-height": b.visibleContentHeight
      ? `${b.visibleContentHeight}px`
      : `${height}px`,
  };
}

function labelOf(b) {
  return (STATUS_CFG.value[b.kind] || {})[b.status]?.label ?? b.status;
}
</script>

<template>
  <div class="app">
    <!-- Navbar -->
    <nav class="navbar">
      <div class="nav-left">
        <span class="logo-airesto">AIRESTO</span>
        <span class="logo-sep">|</span>
        <span class="logo-name">{{
          data?.restaurant.restaurant_name ?? "..."
        }}</span>
      </div>
      <div class="nav-center">
        <div class="search-bar">
          <svg width="16" height="16" viewBox="0 0 16 16" fill="none">
            <circle
              cx="7"
              cy="7"
              r="5.5"
              stroke="currentColor"
              stroke-width="1.2"
            />
            <path
              d="M11 11L14 14"
              stroke="currentColor"
              stroke-width="1.2"
              stroke-linecap="round"
            />
          </svg>
          <span class="search-placeholder">⌘+Л поиск по имени</span>
        </div>
      </div>
      <div class="nav-right">
        <button
          class="nav-btn icon-btn"
          :title="isDark ? 'Светлая тема' : 'Тёмная тема'"
          @click="toggleTheme"
        >
          <!-- Sun (shown in dark mode → switch to light) -->
          <svg
            v-if="isDark"
            width="16"
            height="16"
            viewBox="0 0 24 24"
            fill="none"
          >
            <circle
              cx="12"
              cy="12"
              r="5"
              stroke="currentColor"
              stroke-width="1.8"
            />
            <path
              d="M12 2v2M12 20v2M2 12h2M20 12h2M4.93 4.93l1.41 1.41M17.66 17.66l1.41 1.41M4.93 19.07l1.41-1.41M17.66 6.34l1.41-1.41"
              stroke="currentColor"
              stroke-width="1.8"
              stroke-linecap="round"
            />
          </svg>
          <!-- Moon (shown in light mode → switch to dark) -->
          <svg v-else width="16" height="16" viewBox="0 0 24 24" fill="none">
            <path
              d="M21 12.79A9 9 0 1111.21 3a7 7 0 109.79 9.79z"
              stroke="currentColor"
              stroke-width="1.8"
              stroke-linecap="round"
              stroke-linejoin="round"
            />
          </svg>
        </button>
        <button class="nav-btn logout-btn">
          <svg width="14" height="14" viewBox="0 0 24 24" fill="none">
            <path
              d="M9 21H5a2 2 0 01-2-2V5a2 2 0 012-2h4M16 17l5-5-5-5M21 12H9"
              stroke="currentColor"
              stroke-width="1.8"
              stroke-linecap="round"
              stroke-linejoin="round"
            />
          </svg>
          Выйти
        </button>
      </div>
    </nav>

    <!-- Загрузка / Ошибка -->
    <div v-if="loading" class="state-center">
      <div class="spinner"></div>
      <span>Загрузка...</span>
    </div>

    <div v-else-if="error" class="state-center state-error">
      <svg width="24" height="24" viewBox="0 0 24 24" fill="none">
        <circle cx="12" cy="12" r="10" stroke="#ef4444" stroke-width="1.8" />
        <path
          d="M12 8v4M12 16h.01"
          stroke="#ef4444"
          stroke-width="2"
          stroke-linecap="round"
        />
      </svg>
      <span>Ошибка загрузки: {{ error }}</span>
    </div>

    <!-- Main -->
    <main v-else class="main">
      <h1 class="page-title">Бронирования</h1>

      <!-- Controls -->
      <div class="controls">
        <div class="ctrl-group">
          <span class="ctrl-label">Дата</span>
          <div class="date-tabs">
            <button
              v-for="day in data.available_days"
              :key="day"
              class="date-tab"
              :class="{ active: selectedDate === day }"
              @click="selectedDate = day"
            >
              <span class="dt-top"
                >{{ parseDateInfo(day).day }}
                {{ parseDateInfo(day).month }}</span
              >
              <span class="dt-sub">{{ parseDateInfo(day).sub }}</span>
            </button>
          </div>
        </div>

        <div class="ctrl-group">
          <span class="ctrl-label">Отображаемые зоны</span>
          <div class="zone-tabs">
            <button
              v-for="z in allZones"
              :key="z"
              class="zone-tab"
              :class="{ active: activeZones.includes(z) }"
              @click="toggleZone(z)"
            >
              {{ z }}
            </button>
          </div>
        </div>
      </div>

      <!-- Grid -->
      <div class="grid-outer">
        <!-- Left: fixed time labels -->
        <div class="time-panel">
          <div class="time-panel-hdr"></div>
          <div class="time-col" ref="timeColEl">
            <div v-for="slot in timeSlots" :key="slot" class="time-label">
              {{ slot }}
            </div>
          </div>
        </div>

        <!-- Right: scrollable grid -->
        <div class="grid-scroll" ref="gridScrollEl" @scroll="onGridScroll">
          <!-- Table headers -->
          <div class="table-headers">
            <div
              v-for="table in filteredTables"
              :key="table.id"
              class="th-cell"
            >
              <div class="th-main">
                <div class="th-num">#{{ table.number }}</div>
                <div class="th-cap">{{ table.capacity }} чел</div>
              </div>
              <div class="th-zone">{{ table.zone }}</div>
            </div>
          </div>

          <!-- Grid body -->
          <div
            class="grid-body"
            :style="{ height: timeSlots.length * SLOT_H + 'px' }"
          >
            <!-- Current time line -->
            <div
              v-if="nowLineTop !== null"
              class="now-line"
              :style="{ top: nowLineTop + 'px' }"
            >
              <span class="now-badge">{{ minToStr(nowMin) }}</span>
            </div>

            <!-- Table columns -->
            <div
              v-for="(table, ti) in filteredTables"
              :key="table.id"
              class="table-col"
              :style="{ left: ti * COL_W + 'px', width: COL_W + 'px' }"
            >
              <!-- Background grid cells -->
              <div
                v-for="(slot, si) in timeSlots"
                :key="slot"
                class="grid-cell"
                :class="{ 'is-hour': si % 2 === 0 }"
              ></div>

              <!-- Booking blocks -->
              <div
                v-for="b in getBookings(table)"
                :key="b.key"
                class="booking"
                :class="[
                  `booking-${b.kind}`,
                  { 'has-hidden-content': b.visibleContentHeight },
                ]"
                :style="bookingStyle(b)"
                :title="`${b.kind === 'reservation' ? b.name + ', ' + b.people + ' чел — ' : ''}${labelOf(b)} ${minToStr(b.startMin)}–${minToStr(b.endMin)}`"
              >
                <div class="bk-content">
                <template v-if="b.kind === 'order'">
                  <div class="bk-type">
                    {{ b.status === "Banquet" ? "Банкет" : "Заказ" }}
                  </div>
                  <div class="bk-status">
                    {{ labelOf(b) }}
                  </div>
                  <div class="bk-time">
                    {{ minToStr(b.startMin) }}-{{ minToStr(b.endMin) }}
                  </div>
                </template>
                <template v-else>
                  <div class="bk-res-id">№{{ b.resId }}</div>
                  <div class="bk-name">
                    {{ b.name }}; {{ b.people }}чел
                  </div>
                  <div class="bk-status">
                    {{ labelOf(b) }}
                  </div>
                  <div class="bk-phone">
                    <svg
                      width="9"
                      height="9"
                      viewBox="0 0 16 16"
                      fill="currentColor"
                    >
                      <path
                        d="M13.5 10.5l-2-2a1 1 0 00-1.4 0l-.8.8a8.5 8.5 0 01-3.1-3.1l.8-.8a1 1 0 000-1.4l-2-2A1 1 0 003.6 2L2.5 3.1C1.7 3.9 1.5 5 2 6.2a14 14 0 007.8 7.8c1.2.5 2.3.3 3.1-.5l1.1-1.1a1 1 0 00-.5-1.9z"
                      />
                    </svg>
                    {{ b.phone }}
                  </div>
                  <div class="bk-time">
                    {{ minToStr(b.startMin) }}-{{ minToStr(b.endMin) }}
                  </div>
                </template>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<style>
@import url("https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&display=swap");

*,
*::before,
*::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

:root {
  --bg: #f4f6f9;
  --bg-nav: #ffffff;
  --bg-hdr: #ffffff;
  --border: rgba(0, 0, 0, 0.09);
  --border-h: rgba(0, 0, 0, 0.16);
  --txt-1: #1e2330;
  --txt-2: #6b7280;
  --txt-3: #9ca3af;
  --accent: #3b82f6;
  --now: #ef4444;
  --cell-line: rgba(0, 0, 0, 0.05);
  --cell-line-hour: rgba(0, 0, 0, 0.11);
  --bk-type-color: rgba(0, 0, 0, 0.38);
  --bk-name-color: rgba(0, 0, 0, 0.75);
  --bk-phone-color: rgba(0, 0, 0, 0.38);
  --bk-time-color: rgba(0, 0, 0, 0.3);
  --hdr-shadow: 0 1px 4px rgba(0, 0, 0, 0.06);
}

html.dark {
  --bg: #1b1b1b;
  --bg-nav: #1b1b1b;
  --bg-hdr: #1b1b1b;
  --border: rgba(255, 255, 255, 0.16);
  --border-h: rgba(255, 255, 255, 0.16);
  --txt-1: #ffffff;
  --txt-2: rgba(255, 255, 255, 0.48);
  --txt-3: rgba(255, 255, 255, 0.48);
  --accent: #3b82f6;
  --now: #ff7043;
  --cell-line: rgba(255, 255, 255, 0.16);
  --cell-line-hour: rgba(255, 255, 255, 0.16);
  --bk-type-color: rgba(255, 255, 255, 0.38);
  --bk-name-color: rgba(255, 255, 255, 0.78);
  --bk-phone-color: rgba(255, 255, 255, 0.38);
  --bk-time-color: rgba(255, 255, 255, 0.28);
  --hdr-shadow: none;
}

html,
body {
  height: 100%;
  background: var(--bg);
  color: var(--txt-1);
  font-family: "Inter", sans-serif;
  font-size: 13px;
  -webkit-font-smoothing: antialiased;
}
#app {
  height: 100%;
  display: flex;
  flex-direction: column;
}
button {
  cursor: pointer;
  border: none;
  background: none;
  font-family: inherit;
  font-size: 13px;
}

/* Navbar */
.navbar {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 0 20px;
  height: 44px;
  background: rgba(255, 255, 255, 0.08);
  border-bottom: 0;
  box-shadow: none;
}
.nav-left {
  display: flex;
  align-items: center;
  gap: 8px;
  flex: 0 0 auto;
}
.nav-center {
  flex: 1;
  display: flex;
  justify-content: flex-end;
}
.nav-right {
  display: flex;
  align-items: center;
  gap: 8px;
  flex: 0 0 auto;
}

.logo-airesto {
  font-size: 11px;
  line-height: 16px;
  font-weight: 600;
  letter-spacing: 0;
  color: #fff;
}
.logo-sep {
  color: rgba(255, 255, 255, 0.64);
}
.logo-name {
  font-size: 11px;
  line-height: 16px;
  color: rgba(255, 255, 255, 0.64);
}

.search-bar {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 0 11px;
  height: 28px;
  width: 258px;
  background: #1b1b1d;
  border: 1px solid rgba(255, 255, 255, 0.16);
  border-radius: 8px;
  color: rgba(255, 255, 255, 0.48);
}
.search-placeholder {
  font-size: 11px;
  line-height: 16px;
  color: rgba(255, 255, 255, 0.64);
  white-space: nowrap;
}

.nav-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 4px;
  padding: 0;
  height: 24px;
  border-radius: 4px;
  background: rgba(255, 255, 255, 0.08);
  color: #fff;
  font-size: 11px;
  line-height: 16px;
  transition:
    background 0.15s,
    color 0.15s;
}
.nav-btn:hover {
  background: rgba(255, 255, 255, 0.14);
  color: #fff;
}
.icon-btn {
  width: 24px;
}
.logout-btn {
  width: 66px;
  border: 0;
}
.logout-btn svg {
  width: 16px;
  height: 16px;
}

/* States */
.state-center {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 12px;
  color: var(--txt-2);
}
.state-error {
  color: #f87171;
}

.spinner {
  width: 28px;
  height: 28px;
  border: 2px solid rgba(255, 255, 255, 0.1);
  border-top-color: var(--accent);
  border-radius: 50%;
  animation: spin 0.7s linear infinite;
}
@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}

/* Main */
.main {
  flex: 1;
  display: flex;
  flex-direction: column;
  overflow: hidden;
  padding: 20px 20px 0;
  gap: 14px;
}
.page-title {
  font-size: 30px;
  font-weight: 600;
  line-height: 1.15;
}

/* Controls */
.controls {
  display: flex;
  flex-direction: column;
  gap: 16px;
}
.ctrl-group {
  display: flex;
  flex-direction: column;
  gap: 4px;
}
.ctrl-label {
  font-size: 11px;
  line-height: 14px;
  color: rgba(255, 255, 255, 0.64);
  min-width: 120px;
}

.date-tabs {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
}
.date-tab {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: flex-start;
  padding: 4px 8px;
  height: 36px;
  min-height: 36px;
  border-radius: 8px;
  background: rgba(255, 255, 255, 0.04);
  color: #fff;
  transition: background 0.15s;
  white-space: nowrap;
}
.date-tab:hover {
  background: rgba(255, 255, 255, 0.1);
}
.date-tab.active {
  background: #007aff;
}
.dt-top {
  font-size: 11px;
  font-weight: 600;
  line-height: 14px;
  color: #fff;
}
.dt-sub {
  font-size: 11px;
  font-weight: 400;
  line-height: 14px;
  color: #fff;
}

.zone-tabs {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
}
.zone-tab {
  height: 24px;
  min-height: 24px;
  padding: 0 6px;
  border-radius: 4px;
  font-size: 11px;
  line-height: 16px;
  border: 0;
  background: rgba(255, 255, 255, 0.08);
  color: #fff;
  transition: all 0.15s;
}
.zone-tab:hover {
  background: rgba(255, 255, 255, 0.13);
}
.zone-tab.active {
  background: #007aff;
  color: #fff;
}

/* Grid layout */
.grid-outer {
  flex: 1;
  display: flex;
  overflow: hidden;
  min-height: 0;
}

.time-panel {
  width: 40px;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}
.time-panel-hdr {
  height: 48px;
}
.time-col {
  flex: 1;
  overflow: hidden;
  padding-right: 8px;
}
.time-label {
  width: 32px;
  height: 40px;
  display: flex;
  align-items: flex-start;
  padding-top: 0;
  justify-content: center;
  font-size: 11px;
  line-height: 14px;
  color: rgba(255, 255, 255, 0.48);
  white-space: nowrap;
}

.grid-scroll {
  flex: 1;
  overflow: auto;
  scrollbar-width: thin;
  scrollbar-color: rgba(255, 255, 255, 0.15) transparent;
}
.grid-scroll::-webkit-scrollbar {
  width: 6px;
  height: 6px;
}
.grid-scroll::-webkit-scrollbar-thumb {
  background: rgba(255, 255, 255, 0.15);
  border-radius: 3px;
}

/* Table headers */
.table-headers {
  display: flex;
  position: sticky;
  top: 0;
  z-index: 10;
  background: var(--bg-hdr);
  border-bottom: 1px solid rgba(255, 255, 255, 0.16);
  box-shadow: none;
}
.th-cell {
  width: 80px;
  height: 48px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  border-right: 1px solid rgba(255, 255, 255, 0.16);
  gap: 0;
}
.th-main {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 4px;
  width: 80px;
  height: 20px;
}
.th-num {
  font-size: 11px;
  line-height: 14px;
  color: rgba(255, 255, 255, 0.64);
  font-weight: 400;
}
.th-cap {
  font-size: 11px;
  line-height: 14px;
  color: rgba(255, 255, 255, 0.64);
  font-weight: 400;
}
.th-zone {
  font-size: 11px;
  line-height: 14px;
  color: rgba(255, 255, 255, 0.64);
  font-weight: 400;
}

/* Grid body */
.grid-body {
  position: relative;
  width: max-content;
  min-width: 100%;
}

.now-line {
  position: absolute;
  left: 0;
  right: 0;
  height: 1px;
  background: var(--now);
  z-index: 8;
  pointer-events: none;
}
.now-badge {
  position: absolute;
  left: -40px;
  top: 50%;
  transform: translateY(-50%);
  background: var(--now);
  color: #fff;
  font-size: 10px;
  font-weight: 600;
  padding: 1px 4px;
  border-radius: 3px;
  white-space: nowrap;
}

.table-col {
  position: absolute;
  top: 0;
  border-right: 1px solid rgba(255, 255, 255, 0.16);
}

.grid-cell {
  height: 40px;
  border-top: 1px solid rgba(255, 255, 255, 0.16);
}
.grid-cell.is-hour {
  border-top-color: var(--cell-line-hour);
}

/* Booking block */
.booking {
  position: absolute;
  overflow: hidden;
  border-radius: 4px;
  padding: 2px;
  cursor: pointer;
  z-index: 2;
  transition:
    filter 0.12s,
    box-shadow 0.12s,
    min-width 0.12s;
  color: #fff;
}
.booking:hover {
  filter: brightness(1.12);
  z-index: 40;
  height: auto !important;
  min-height: var(--booking-height);
  min-width: 128px;
  overflow: visible;
  box-shadow:
    0 16px 32px rgba(0, 0, 0, 0.34),
    0 0 0 1px rgba(255, 255, 255, 0.08);
}
.booking-reservation:hover {
  min-width: 138px;
}
.bk-content {
  position: relative;
  max-height: var(--visible-content-height);
  overflow: hidden;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}
.has-hidden-content .bk-content::after {
  content: "...";
  position: absolute;
  right: 0;
  bottom: 0;
  padding-left: 18px;
  color: #fff;
  line-height: 14px;
  background: linear-gradient(
    90deg,
    rgba(74, 42, 32, 0),
    var(--booking-bg, rgba(74, 42, 32, 0.95)) 45%
  );
}
.booking-order.has-hidden-content .bk-content::after {
  background: linear-gradient(
    90deg,
    rgba(37, 58, 57, 0),
    var(--booking-bg, rgba(37, 58, 57, 0.95)) 45%
  );
}
.booking:hover .bk-content {
  max-height: none;
  overflow: visible;
}
.booking:hover .bk-content::after {
  display: none;
}

.bk-type,
.bk-res-id {
  font-size: 8px;
  color: #fff;
  line-height: 8px;
}
.bk-type {
  font-size: 11px;
  font-weight: 600;
  line-height: 14px;
}
.bk-res-id {
  font-weight: 400;
}
.bk-status {
  display: inline-flex;
  align-items: center;
  max-width: 100%;
  width: fit-content;
  height: 12px;
  min-height: 12px;
  padding: 2px;
  border-radius: 4px;
  background: var(--badge-bg);
  font-size: 8px;
  color: var(--lc);
  line-height: 8px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  font-weight: 600;
}
.bk-name {
  font-size: 11px;
  color: #fff;
  line-height: 14px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  font-weight: 600;
}
.bk-phone {
  display: flex;
  align-items: center;
  gap: 0;
  font-size: 11px;
  color: #fff;
  line-height: 14px;
}
.bk-phone svg {
  width: 12px;
  height: 12px;
  padding: 2px;
}
.bk-time {
  font-size: 11px;
  color: #fff;
  line-height: 14px;
  min-width: 38px;
  margin-top: 0;
}
.booking:hover .bk-status,
.booking:hover .bk-name,
.booking:hover .bk-phone,
.booking:hover .bk-time {
  max-width: none;
  white-space: normal;
  overflow: visible;
  text-overflow: clip;
}
@media (max-width: 720px) {
  .main {
    padding: 24px 20px 0;
  }

  .page-title {
    font-size: 28px;
  }
}
</style>
