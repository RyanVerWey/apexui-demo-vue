<script setup lang="ts">
import { computed, onMounted, onUnmounted, reactive, ref } from "vue";
import {
  ApexAccordion,
  ApexAlert,
  ApexAppBar,
  ApexAutocomplete,
  ApexAvatar,
  ApexBadge,
  ApexButton,
  ApexButtonGroup,
  ApexCard,
  ApexChart,
  ApexCheckbox,
  ApexChip,
  ApexDataGrid,
  ApexDataTable,
  ApexDatePicker,
  ApexFileUpload,
  ApexIcon,
  ApexList,
  ApexNumberField,
  ApexPaper,
  ApexProgress,
  ApexRadioGroup,
  ApexSearchForm,
  ApexSelect,
  ApexSidebar,
  ApexStack,
  ApexStepper,
  ApexSwitch,
  ApexTabs,
  ApexTextarea,
  ApexTextField,
  ApexTimeline,
  ApexToolbar,
  ApexTypography,
  ApexWorkflowBoard,
} from "@apexui/vue";

type Mode = "light" | "dark";
type RouteId = "home" | "dashboard" | "work-orders" | "customers" | "settings" | "about";
type WorkOrderKey = "site" | "contact" | "city" | "date" | "crew" | "priority" | "budget" | "notes";

const routes: Array<{ id: RouteId; path: string; label: string; badge?: string }> = [
  { id: "home", path: "/", label: "Home" },
  { id: "dashboard", path: "/dashboard", label: "Metrics", badge: "live" },
  { id: "work-orders", path: "/work-orders", label: "Work orders" },
  { id: "customers", path: "/customers", label: "Customers", badge: "12" },
  { id: "settings", path: "/settings", label: "Settings" },
  { id: "about", path: "/about", label: "Package proof" },
];

const routeLookup = new Map(routes.map((route) => [route.path, route]));
const routeById = new Map(routes.map((route) => [route.id, route]));
const normalizeHash = () => {
  const next = window.location.hash.replace(/^#/, "") || "/";
  return routeLookup.has(next) ? next : "/";
};

const mode = ref<Mode>("light");
const currentPath = ref(normalizeHash());
const activeSettingsTab = ref("profile");
const submittedWorkOrder = ref(false);

const workOrder = reactive<Record<WorkOrderKey, string>>({
  site: "Bayside Medical Campus",
  contact: "Jamie Ortiz",
  city: "Providence, RI",
  date: "2026-06-22",
  crew: "north",
  priority: "high",
  budget: "18400",
  notes: "Replace rooftop controller, inspect line sensors, and stage customer approval before 15:00.",
});

const themeName = computed(() => `gilded-${mode.value}`);
const currentRoute = computed(() => routeLookup.get(currentPath.value) ?? routes[0]);
const shellTitle = computed(() => {
  if (currentRoute.value.id === "home") return "Northstar Field Services";
  return `${currentRoute.value.label} | Northstar Field Services`;
});
const routeSidebarItems = computed(() =>
  routes.map((route) => ({
    id: route.id,
    label: route.label,
    badge: route.badge,
  })),
);
const missingWorkOrderFields = computed(() =>
  (["site", "contact", "date", "crew", "priority"] as WorkOrderKey[]).filter((key) => !workOrder[key]),
);
const workOrderReady = computed(() => missingWorkOrderFields.value.length === 0);
const workOrderStatus = computed(() => {
  if (!submittedWorkOrder.value) return "Draft ready for review";
  return workOrderReady.value ? "Validated and queued for dispatch" : "Needs required fields";
});

const setMode = (next: Mode) => {
  mode.value = next;
};
const setModeFromSwitch = (event: Event) => {
  mode.value = (event as CustomEvent<{ checked: boolean }>).detail.checked ? "dark" : "light";
};
const syncHash = () => {
  currentPath.value = normalizeHash();
};
const navigateById = (event: Event) => {
  const id = (event as CustomEvent<RouteId>).detail;
  const route = routeById.get(id);
  if (route) window.location.hash = route.path;
};
const navigate = (path: string) => {
  window.location.hash = path;
};
const setSelectValue = (key: WorkOrderKey, event: Event) => {
  workOrder[key] = (event as CustomEvent<{ value: string }>).detail.value;
};
const setFieldValue = (key: WorkOrderKey, event: Event) => {
  workOrder[key] = (event.target as HTMLInputElement).value;
};
const setSettingsTab = (event: Event) => {
  activeSettingsTab.value = (event as CustomEvent<{ id: string }>).detail.id;
};
const submitWorkOrder = () => {
  submittedWorkOrder.value = true;
};

onMounted(() => window.addEventListener("hashchange", syncHash));
onUnmounted(() => window.removeEventListener("hashchange", syncHash));

const performanceChart = [
  { label: "First visit fix", value: 92 },
  { label: "On-time arrival", value: 88 },
  { label: "Parts ready", value: 81 },
  { label: "Renewals", value: 76 },
];
const revenueChart = [
  { label: "Install", value: 46 },
  { label: "Preventive", value: 32 },
  { label: "Emergency", value: 18 },
  { label: "Warranty", value: 9 },
];
const dashboardRows = [
  { route: "Metro North", dispatcher: "Avery Stone", open: 18, risk: "Low" },
  { route: "Harbor Corridor", dispatcher: "Mina Shah", open: 25, risk: "Watch" },
  { route: "Inland West", dispatcher: "Jon Bell", open: 14, risk: "Clear" },
  { route: "University Loop", dispatcher: "Priya Lane", open: 11, risk: "Parts hold" },
];
const dashboardColumns = [
  { key: "route", header: "Route" },
  { key: "dispatcher", header: "Dispatcher" },
  { key: "open", header: "Open" },
  { key: "risk", header: "Risk" },
];
const workflowColumns = [
  {
    id: "intake",
    title: "Intake",
    items: [
      { id: "wo-1184", title: "Hotel condenser alarm", meta: "07:45" },
      { id: "wo-1189", title: "Campus lockout report", meta: "09:20" },
    ],
  },
  {
    id: "scheduled",
    title: "Scheduled",
    items: [
      { id: "wo-1191", title: "Clinic pressure test", meta: "Crew B" },
      { id: "wo-1193", title: "Warehouse dock sensor", meta: "Crew F" },
    ],
  },
  {
    id: "closed",
    title: "Closed",
    items: [
      { id: "wo-1178", title: "Bank branch PM sweep", meta: "Signed" },
      { id: "wo-1179", title: "Arena chiller check", meta: "Paid" },
    ],
  },
];
const pipelineRows = [
  { account: "Granite Health", stage: "Renewal", owner: "Maya Chen", value: "$84K", next: "QBR" },
  { account: "Cobalt Hospitality", stage: "Proposal", owner: "Omar Haddad", value: "$126K", next: "Scope review" },
  { account: "StateLine Logistics", stage: "Implementation", owner: "Elena Rossi", value: "$212K", next: "Crew onboarding" },
  { account: "North Pier Arena", stage: "Discovery", owner: "Nia Brooks", value: "$58K", next: "Site walk" },
];
const pipelineColumns = [
  { key: "account", header: "Account" },
  { key: "stage", header: "Stage" },
  { key: "owner", header: "Owner" },
  { key: "value", header: "Value" },
  { key: "next", header: "Next step" },
];
const packageRows = [
  { package: "@apexui/vue", role: "Vue 3 wrappers", proof: "All page controls import from package" },
  { package: "@apexui/tokens", role: "Gilded themes", proof: "Shell uses data-apex-theme" },
  { package: "Vite", role: "Build host", proof: "Hash routes work on GitHub Pages" },
];
const packageColumns = [
  { key: "package", header: "Package" },
  { key: "role", header: "Role" },
  { key: "proof", header: "Proof" },
];
const ownerOptions = ["Maya Chen", "Omar Haddad", "Elena Rossi", "Nia Brooks"];
const crewOptions = [
  { label: "North crew", value: "north" },
  { label: "Harbor crew", value: "harbor" },
  { label: "Night response", value: "night" },
];
const priorityOptions = [
  { label: "Emergency", value: "emergency" },
  { label: "High", value: "high" },
  { label: "Scheduled", value: "scheduled" },
];
const localeOptions = [
  { label: "English, United States", value: "en-US" },
  { label: "English, Canada", value: "en-CA" },
  { label: "Spanish, United States", value: "es-US" },
];
const priorityRadioOptions = [
  { label: "Emergency dispatch", value: "emergency" },
  { label: "Normal queue", value: "scheduled" },
];
const settingsTabs = [
  { id: "profile", label: "Profile" },
  { id: "notifications", label: "Notifications" },
  { id: "controls", label: "Controls" },
];
const orderSteps = [
  { id: "intake", label: "Intake", description: "Request details captured" },
  { id: "dispatch", label: "Dispatch", description: "Crew and parts assigned" },
  { id: "closeout", label: "Closeout", description: "Customer signoff and invoice" },
];
const timelineEvents = [
  { label: "07:30", description: "Emergency queue opened", meta: "Harbor corridor" },
  { label: "09:10", description: "Two preventive visits pulled forward", meta: "Metro North" },
  { label: "11:40", description: "Parts variance cleared", meta: "Warehouse dock" },
];
const uploadedFiles = [
  { name: "roof-controller-photo.jpg", meta: "2.4 MB" },
  { name: "customer-scope.pdf", meta: "188 KB" },
];
const proofItems = [
  { id: "routing", heading: "Hash routing", content: "Six business pages share Vue state and work on static GitHub Pages hosting." },
  { id: "tokens", heading: "Gilded tokens", content: "The app shell switches between gilded-light and gilded-dark token scopes." },
  { id: "components", heading: "ApexUI controls", content: "Forms, charts, tables, navigation, tabs, workflow, and feedback use @apexui/vue." },
];
</script>

<template>
  <main class="app-shell" :data-apex-theme="themeName">
    <ApexAppBar :heading="shellTitle">
      <ApexButtonGroup slot="actions" label="Theme mode">
        <ApexButton size="sm" :variant="mode === 'light' ? 'primary' : 'secondary'" @click="setMode('light')">Light</ApexButton>
        <ApexButton size="sm" :variant="mode === 'dark' ? 'primary' : 'secondary'" @click="setMode('dark')">Dark</ApexButton>
      </ApexButtonGroup>
    </ApexAppBar>

    <div class="app-main">
      <div class="app-layout">
        <ApexSidebar
          heading="Northstar"
          label="Business routes"
          :active-id="currentRoute.id"
          :items="routeSidebarItems"
          @apexSelect="navigateById"
        >
          <ApexBadge slot="footer" tone="info">{{ themeName }}</ApexBadge>
        </ApexSidebar>

        <section class="app-page" :aria-label="currentRoute.label">
          <template v-if="currentRoute.id === 'home'">
            <ApexPaper>
              <div class="hero-layout">
                <ApexStack gap="md">
                  <ApexBadge tone="info">Regional field operations</ApexBadge>
                  <ApexTypography as="h1" variant="display">Northstar keeps service teams on time, stocked, and accountable.</ApexTypography>
                  <ApexTypography variant="body">
                    Premium dispatch software for HVAC, facilities, and field maintenance teams that need clean schedules,
                    signed work, and leadership metrics in one operating system.
                  </ApexTypography>
                  <ApexSearchForm label="Find a service plan" placeholder="Search contracts, sites, service lines"></ApexSearchForm>
                  <div class="action-row">
                    <ApexButton @click="navigate('/work-orders')">Create work order</ApexButton>
                    <ApexButton variant="secondary" @click="navigate('/dashboard')">View live metrics</ApexButton>
                  </div>
                </ApexStack>

                <div class="hero-visual" aria-label="Northstar daily operations proof">
                  <div class="visual-row">
                    <ApexIcon name="route" size="lg" decorative></ApexIcon>
                    <span>37 crews routed</span>
                  </div>
                  <ApexProgress label="SLA confidence" :value="91"></ApexProgress>
                  <ApexProgress label="Parts readiness" :value="84"></ApexProgress>
                  <ApexProgress label="Customer signoff" :value="78"></ApexProgress>
                </div>
              </div>
            </ApexPaper>

            <div class="proof-grid">
              <ApexCard eyebrow="Offer" heading="Dispatch command">
                Route planning, service history, and schedule risk stay visible before crews roll.
              </ApexCard>
              <ApexCard eyebrow="Customer proof" heading="14 minute faster closeout">
                Regional operators cut paperwork lag by moving approvals and evidence capture into the same flow.
              </ApexCard>
              <ApexCard eyebrow="Field ready" heading="No training deck required">
                Clean ApexUI controls make technician, dispatcher, and account workflows feel consistent.
              </ApexCard>
            </div>

            <ApexToolbar label="Launch desk">
              <ApexButton size="sm" @click="navigate('/customers')">Open pipeline</ApexButton>
              <ApexButton size="sm" variant="secondary" @click="navigate('/about')">Package proof</ApexButton>
            </ApexToolbar>
          </template>

          <template v-else-if="currentRoute.id === 'dashboard'">
            <div class="page-head">
              <ApexStack gap="sm">
                <ApexBadge tone="success">Operations live</ApexBadge>
                <ApexTypography as="h1" variant="title">Metrics dashboard</ApexTypography>
                <ApexTypography variant="body">Dispatch health, route load, and revenue mix for today.</ApexTypography>
              </ApexStack>
              <ApexButton variant="secondary" @click="navigate('/work-orders')">Add order</ApexButton>
            </div>

            <div class="stat-grid">
              <ApexCard eyebrow="Today" heading="142 visits">31 emergency, 86 preventive, 25 quoted follow-ups.</ApexCard>
              <ApexCard eyebrow="SLA" heading="96.4 percent">Only Harbor Corridor needs dispatcher review.</ApexCard>
              <ApexCard eyebrow="Revenue" heading="$418K">Install work leads booked value this week.</ApexCard>
            </div>

            <div class="chart-grid">
              <ApexChart label="Service performance" :data="performanceChart"></ApexChart>
              <ApexChart label="Revenue mix" :data="revenueChart"></ApexChart>
            </div>

            <div class="split-layout">
              <ApexDataTable caption="Route load" :columns="dashboardColumns" :rows="dashboardRows"></ApexDataTable>
              <ApexStack gap="md">
                <ApexWorkflowBoard :columns="workflowColumns"></ApexWorkflowBoard>
                <ApexTimeline :events="timelineEvents"></ApexTimeline>
              </ApexStack>
            </div>
          </template>

          <template v-else-if="currentRoute.id === 'work-orders'">
            <div class="page-head">
              <ApexStack gap="sm">
                <ApexBadge :tone="workOrderReady ? 'success' : 'warning'">{{ workOrderStatus }}</ApexBadge>
                <ApexTypography as="h1" variant="title">Work order intake</ApexTypography>
                <ApexTypography variant="body">Capture dispatch-ready scope, customer context, schedule, files, and priority.</ApexTypography>
              </ApexStack>
              <ApexStepper :steps="orderSteps" :active-index="submittedWorkOrder ? 1 : 0"></ApexStepper>
            </div>

            <div class="form-layout">
              <ApexPaper>
                <ApexStack gap="md">
                  <ApexTextField
                    label="Site name"
                    :value="workOrder.site"
                    placeholder="Customer site"
                    :error="submittedWorkOrder && !workOrder.site ? 'Site name is required.' : undefined"
                    @input="setFieldValue('site', $event)"
                  ></ApexTextField>
                  <ApexTextField
                    label="Site contact"
                    :value="workOrder.contact"
                    placeholder="Primary contact"
                    :error="submittedWorkOrder && !workOrder.contact ? 'Contact is required.' : undefined"
                    @input="setFieldValue('contact', $event)"
                  ></ApexTextField>
                  <ApexTextField label="Service city" :value="workOrder.city" placeholder="City, state" @input="setFieldValue('city', $event)"></ApexTextField>
                  <div class="field-grid">
                    <ApexDatePicker label="Target date" :value="workOrder.date" hint="Earliest available service date"></ApexDatePicker>
                    <ApexSelect label="Crew" :value="workOrder.crew" :options="crewOptions" @apexChange="setSelectValue('crew', $event)"></ApexSelect>
                    <ApexSelect label="Priority" :value="workOrder.priority" :options="priorityOptions" @apexChange="setSelectValue('priority', $event)"></ApexSelect>
                  </div>
                  <ApexNumberField label="Estimated budget" :value="workOrder.budget" :min="0" :step="100" hint="Used for approval routing"></ApexNumberField>
                  <ApexTextarea label="Scope notes" :value="workOrder.notes" @input="setFieldValue('notes', $event)"></ApexTextarea>
                  <ApexFileUpload label="Attachments" description="Photos, signed scope, inspection notes" :files="uploadedFiles"></ApexFileUpload>
                  <ApexCheckbox label="Customer approved after-hours access" checked></ApexCheckbox>
                  <ApexRadioGroup label="Dispatch posture" name="posture" value="scheduled" :options="priorityRadioOptions"></ApexRadioGroup>
                  <div class="action-row">
                    <ApexButton @click="submitWorkOrder">Validate order</ApexButton>
                    <ApexButton variant="secondary" @click="navigate('/dashboard')">Back to dashboard</ApexButton>
                  </div>
                </ApexStack>
              </ApexPaper>

              <ApexStack gap="md">
                <ApexAlert :tone="workOrderReady ? 'success' : 'warning'" heading="Validation">
                  Required fields: site, contact, date, crew, and priority. Missing {{ missingWorkOrderFields.length }}.
                </ApexAlert>
                <ApexCard eyebrow="Dispatch preview" :heading="workOrder.site">
                  {{ workOrder.contact }} in {{ workOrder.city }}. Crew {{ workOrder.crew }} with {{ workOrder.priority }} priority.
                </ApexCard>
                <ApexList>
                  <li><strong>Help copy</strong><span>Use exact site names from the customer record.</span></li>
                  <li><strong>Upload policy</strong><span>Attach site photos before emergency dispatch.</span></li>
                  <li><strong>Approval</strong><span>Budgets above $25K route to finance.</span></li>
                </ApexList>
              </ApexStack>
            </div>
          </template>

          <template v-else-if="currentRoute.id === 'customers'">
            <div class="page-head">
              <ApexStack gap="sm">
                <ApexBadge tone="info">Pipeline and records</ApexBadge>
                <ApexTypography as="h1" variant="title">Customer command center</ApexTypography>
                <ApexTypography variant="body">Account pipeline, owner coverage, and field record patterns in one page.</ApexTypography>
              </ApexStack>
              <ApexSearchForm label="Search customers" placeholder="Account, owner, city, stage"></ApexSearchForm>
            </div>

            <div class="split-layout">
              <ApexDataGrid caption="Customer pipeline" :columns="pipelineColumns" :rows="pipelineRows"></ApexDataGrid>
              <ApexPaper>
                <ApexStack gap="md">
                  <ApexAvatar name="Granite Health"></ApexAvatar>
                  <ApexTypography as="h2" variant="title">Granite Health</ApexTypography>
                  <ApexTypography variant="body">Eight campuses, 41 critical assets, renewal committee meets Friday.</ApexTypography>
                  <div class="chip-row">
                    <ApexChip selected>Renewal</ApexChip>
                    <ApexChip>Medical</ApexChip>
                    <ApexChip>High SLA</ApexChip>
                  </div>
                  <ApexProgress label="Renewal confidence" :value="86"></ApexProgress>
                  <ApexAutocomplete label="Account owner" value="Maya Chen" :options="ownerOptions"></ApexAutocomplete>
                  <ApexButton @click="navigate('/work-orders')">Start service request</ApexButton>
                </ApexStack>
              </ApexPaper>
            </div>

            <div class="proof-grid">
              <ApexCard eyebrow="Record pattern" heading="Contract context">Contacts, sites, and assets sit beside current revenue stage.</ApexCard>
              <ApexCard eyebrow="List pattern" heading="Next best action">Every account exposes one explicit operational next step.</ApexCard>
              <ApexCard eyebrow="Detail pattern" heading="Proof before pitch">Progress and owner fields use real form controls, not static screenshots.</ApexCard>
            </div>
          </template>

          <template v-else-if="currentRoute.id === 'settings'">
            <div class="page-head">
              <ApexStack gap="sm">
                <ApexBadge tone="info">Account controls</ApexBadge>
                <ApexTypography as="h1" variant="title">Settings and account</ApexTypography>
                <ApexTypography variant="body">Preferences, locale, notification posture, and theme controls for operations users.</ApexTypography>
              </ApexStack>
              <ApexSwitch label="Dark mode" :checked="mode === 'dark'" @apexChange="setModeFromSwitch"></ApexSwitch>
            </div>

            <ApexTabs label="Settings sections" :active-id="activeSettingsTab" :items="settingsTabs" @apexChange="setSettingsTab"></ApexTabs>

            <div class="settings-layout">
              <ApexPaper>
                <ApexStack gap="md">
                  <ApexTextField label="Display name" value="Ryan VerWey"></ApexTextField>
                  <ApexTextField label="Workspace" value="Northstar Field Services"></ApexTextField>
                  <ApexSelect label="Locale" value="en-US" :options="localeOptions"></ApexSelect>
                  <ApexButton>Save account</ApexButton>
                </ApexStack>
              </ApexPaper>

              <ApexStack gap="md">
                <ApexSwitch label="Dispatch alerts" description="Notify when route risk changes." checked></ApexSwitch>
                <ApexSwitch label="Customer approvals" description="Send approval requests from closeout." checked></ApexSwitch>
                <ApexSwitch label="Weekly executive digest" description="Summarize SLA, margin, and open risk." checked></ApexSwitch>
                <ApexButtonGroup label="Theme">
                  <ApexButton size="sm" :variant="mode === 'light' ? 'primary' : 'secondary'" @click="setMode('light')">gilded-light</ApexButton>
                  <ApexButton size="sm" :variant="mode === 'dark' ? 'primary' : 'secondary'" @click="setMode('dark')">gilded-dark</ApexButton>
                </ApexButtonGroup>
              </ApexStack>
            </div>
          </template>

          <template v-else>
            <div class="page-head">
              <ApexStack gap="sm">
                <ApexBadge tone="success">Package proof</ApexBadge>
                <ApexTypography as="h1" variant="title">About this Vue demo</ApexTypography>
                <ApexTypography variant="body">This static app proves ApexUI package integration through real business pages.</ApexTypography>
              </ApexStack>
              <ApexButton variant="secondary" @click="navigate('/')">Back home</ApexButton>
            </div>

            <div class="split-layout">
              <ApexDataTable caption="Installed package proof" :columns="packageColumns" :rows="packageRows"></ApexDataTable>
              <ApexPaper>
                <ApexStack gap="md">
                  <ApexIcon name="package" size="lg" decorative></ApexIcon>
                  <ApexTypography as="h2" variant="title">Framework-specific integration</ApexTypography>
                  <ApexTypography variant="body">
                    Vue imports ApexUI wrappers directly, token CSS once in main.ts, and uses a tiny hash router because vue-router is not installed.
                  </ApexTypography>
                  <ApexAlert tone="info" heading="Routing">Routes are hash based so deep links survive GitHub Pages refreshes.</ApexAlert>
                </ApexStack>
              </ApexPaper>
            </div>

            <ApexStack gap="sm">
              <ApexAccordion v-for="item in proofItems" :key="item.id" :heading="item.heading" :open="item.id === 'routing'">
                {{ item.content }}
              </ApexAccordion>
            </ApexStack>
          </template>
        </section>
      </div>
    </div>
  </main>
</template>
