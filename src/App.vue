<script setup lang="ts">
import { ref } from "vue";
import {
  ApexAccordion,
  ApexAlert,
  ApexAppBar,
  ApexAutocomplete,
  ApexBadge,
  ApexButton,
  ApexButtonGroup,
  ApexCard,
  ApexChart,
  ApexCheckbox,
  ApexDataTable,
  ApexDatePicker,
  ApexFileUpload,
  ApexList,
  ApexNumberField,
  ApexPaper,
  ApexProgress,
  ApexRadioGroup,
  ApexSearchForm,
  ApexSidebar,
  ApexStack,
  ApexSwitch,
  ApexTabs,
  ApexTextarea,
  ApexTextField,
  ApexTimeline,
  ApexToolbar,
  ApexTypography,
} from "@apexui/vue";

const mode = ref<"light" | "dark">("light");
const activeSection = ref("pipeline");
const activeTab = ref("pipeline");
const theme = () => `ocean-${mode.value}`;
const setMode = (next: "light" | "dark") => (mode.value = next);
const setModeFromSwitch = (event: Event) => {
  mode.value = (event as CustomEvent<{ checked: boolean }>).detail.checked ? "dark" : "light";
};
const setSection = (event: Event) => {
  activeSection.value = (event as CustomEvent<string>).detail;
};
const setTab = (event: Event) => {
  activeTab.value = (event as CustomEvent<{ id: string }>).detail.id;
};

const sidebarItems = [
  { id: "pipeline", label: "Pipeline", badge: "3" },
  { id: "briefs", label: "Briefs" },
  { id: "releases", label: "Releases", badge: "1" },
];
const chartData = [{ label: "Tokens", value: 92 }, { label: "Components", value: 84 }, { label: "Routes", value: 76 }];
const rows = [
  { work: "Ocean token rollout", owner: "Vue team", status: "Ready" },
  { work: "Wrapper audit", owner: "Platform", status: "Review" },
  { work: "Content launch", owner: "Docs", status: "Live" },
];
const columns = [{ key: "work", header: "Work" }, { key: "owner", header: "Owner" }, { key: "status", header: "Status" }];
const listItems = [
  { label: "Props pass through", description: "Generated Vue wrapper mirrors Stencil" },
  { label: "Events stay open", description: "Consumers can wire route state" },
  { label: "Tokens load once", description: "Theme scope lives on app shell" },
];
const timelineItems = [
  { label: "Intake", description: "Brief accepted", meta: "08:40" },
  { label: "Review", description: "Design tokens verified", meta: "09:15" },
  { label: "Launch", description: "Pages workflow queued", meta: "next" },
];
const accordionItems = [
  { id: "routing", heading: "Routing", content: "Vue owns route state while ApexUI owns component behavior." },
  { id: "theme", heading: "Theme", content: "Ocean light and dark modes switch at the app shell." },
  { id: "forms", heading: "Forms", content: "Inputs, upload, and field controls are ApexUI components." },
];
const ownerOptions = ["Maya Chen", "Omar Haddad", "Elena Rossi"];
const priorityOptions = [{ label: "High", value: "high" }, { label: "Normal", value: "normal" }];
const tabItems = [{ id: "pipeline", label: "Pipeline" }, { id: "briefs", label: "Briefs" }, { id: "release", label: "Release" }];
const uploadFiles = [{ name: "vue-wrapper-audit.md", meta: "42 KB" }];
</script>

<template>
  <main class="app-shell" :data-apex-theme="theme()">
    <ApexAppBar heading="ApexUI Vue Studio">
      <ApexButtonGroup slot="actions" label="Theme mode">
        <ApexButton size="sm" :variant="mode === 'light' ? 'primary' : 'secondary'" @click="setMode('light')">Light</ApexButton>
        <ApexButton size="sm" :variant="mode === 'dark' ? 'primary' : 'secondary'" @click="setMode('dark')">Dark</ApexButton>
      </ApexButtonGroup>
    </ApexAppBar>

    <div class="app-main">
      <div class="app-layout">
        <ApexSidebar heading="Ocean app" label="Vue sections" :active-id="activeSection" :items="sidebarItems" @apexSelect="setSection">
          <ApexBadge slot="footer" tone="info">{{ theme() }}</ApexBadge>
        </ApexSidebar>

        <section class="app-page">
          <ApexPaper>
            <div class="app-hero">
              <ApexStack gap="md">
                <ApexBadge tone="info">Vue wrappers</ApexBadge>
                <ApexTypography as="h1" variant="display">Ocean operations workspace built from ApexUI.</ApexTypography>
                <ApexTypography variant="body">Generated Vue components handle the full app surface: navigation, forms, data, charts, and release workflow.</ApexTypography>
                <ApexSearchForm label="Find work" placeholder="Search briefs, owners, releases"></ApexSearchForm>
                <div class="app-actions">
                  <ApexButton>Save view</ApexButton>
                  <ApexButton variant="secondary">Open docs</ApexButton>
                  <ApexSwitch label="Dark mode" :checked="mode === 'dark'" @apexChange="setModeFromSwitch"></ApexSwitch>
                </div>
              </ApexStack>

              <ApexCard eyebrow="Quality" heading="Wrapper confidence">
                <ApexProgress label="Generated surface" :value="87"></ApexProgress>
                <ApexDatePicker label="Release date" value="2026-06-21"></ApexDatePicker>
                <ApexNumberField label="Routes" value="14"></ApexNumberField>
              </ApexCard>
            </div>
          </ApexPaper>

          <ApexToolbar label="Vue release desk">
            <ApexButton size="sm">Run audit</ApexButton>
            <ApexButton size="sm" variant="secondary">Create issue</ApexButton>
          </ApexToolbar>

          <div class="app-grid">
            <ApexChart label="Vue support" :data="chartData"></ApexChart>
            <ApexFileUpload label="Attach spec" description="Drop design notes or wrapper reports." :files="uploadFiles"></ApexFileUpload>
            <ApexList>
              <li v-for="item in listItems" :key="item.label">
                <strong>{{ item.label }}</strong>
                <span>{{ item.description }}</span>
              </li>
            </ApexList>
          </div>

          <div class="app-split">
            <ApexCard eyebrow="Brief" heading="Launch details">
              <ApexStack gap="md">
                <ApexTextField label="Workspace" value="Vue Ocean Launch"></ApexTextField>
                <ApexTextarea label="Notes" value="Wrapper demo uses token-owned visuals and layout-only app CSS."></ApexTextarea>
                <ApexAutocomplete label="Owner" value="" :options="ownerOptions"></ApexAutocomplete>
                <ApexRadioGroup label="Priority" name="priority" value="high" :options="priorityOptions"></ApexRadioGroup>
                <ApexCheckbox label="Publish with light and dark token modes" checked></ApexCheckbox>
              </ApexStack>
            </ApexCard>

            <ApexDataTable caption="Vue matrix" :columns="columns" :rows="rows"></ApexDataTable>
          </div>

          <div class="app-split">
            <ApexStack gap="sm">
              <ApexAccordion v-for="(item, index) in accordionItems" :key="item.id" :heading="item.heading" :open="index === 0">
                {{ item.content }}
              </ApexAccordion>
            </ApexStack>
            <ApexStack gap="md">
              <ApexTabs label="View" :active-id="activeTab" :items="tabItems" @apexChange="setTab"></ApexTabs>
              <ApexTimeline :events="timelineItems"></ApexTimeline>
            </ApexStack>
          </div>

          <ApexAlert tone="success" heading="Vue demo scope">Full app composition uses ApexUI Vue wrappers with ocean light and dark token modes.</ApexAlert>
        </section>
      </div>
    </div>
  </main>
</template>
