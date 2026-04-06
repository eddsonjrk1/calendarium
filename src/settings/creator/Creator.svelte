<script lang="ts">
    import { ButtonComponent, Platform, Notice } from "obsidian";
    import { onMount } from "svelte";

    import CreatorTitle from "./CreatorTitle.svelte";
    import History from "./Utilities/History.svelte";
    import General from "./Containers/general/General.svelte";
    import Dates from "./Containers/dates/Dates.svelte";
    import Celestials from "./Containers/celestials/Celestials.svelte";
    import Events from "./Containers/events/Events.svelte";
    import Eras from "./Containers/eras/EraContainer.svelte";
    import { SettingsSections, type CreatorSection } from "./creator.types";
    import { writable } from "svelte/store";
    import Sidebar from "./Containers/sidebar/Sidebar.svelte";
    import Seasonal from "./Containers/seasonal/Seasonal.svelte";
    import Locations from "./Containers/locations/Locations.svelte";

    async function handleSaveCalendar() {
        try {
            // Acessa a instância global do Obsidian e "pesca" o Calendarium diretamente da memória
            const app = (window as any).app;
            const calendariumPlugin = app.plugins.plugins["calendarium"];
            
            if (calendariumPlugin && typeof calendariumPlugin.saveSettings === "function") {
                await calendariumPlugin.saveSettings(); 
                new Notice("Calendário persistido com sucesso!");
            } else {
                new Notice("Aviso: Função de salvar não encontrada na instância.");
            }
        } catch (error) {
            new Notice("Falha ao salvar. Verifique logs do console.");
            console.error("Erro na persistência do Calendarium:", error);
        }
    }

    const mobile = Platform.isMobile;

    let ready = mobile;

    onMount(() => {
        ready = true;
    });

    let selected = writable<CreatorSection>("General");

    export let color: string | null = null;
    export let top: number;
</script>

{#if Platform.isTablet || Platform.isDesktop}
    <Sidebar {selected} sections={[...SettingsSections]} on:cancel />
    <div class="vertical-tab-content-container {$selected.toLowerCase()}">
        <History></History>
        <div class="vertical-tab-content">
            {#if $selected == "General"}
                <General />
            {/if}
            {#if $selected == "Dates"}
                <Dates />
            {/if}
            {#if $selected === "Eras"}
                <Eras />
            {/if}
            {#if $selected === "Seasons & weather"}
                <Seasonal />
            {/if}
            <!--  -->
            {#if $selected === "Locations"}
                <Locations />
            {/if}
            {#if $selected == "Events"}
                <Events />
            {/if}
            {#if $selected == "Celestial bodies"}
                <Celestials />
            {/if}
        </div>
    </div>
{:else}
    <div
        class="calendarium-creator"
        style="--creator-background-color: {color}; --top: {top}px;"
    >
        {#if ready}
            <CreatorTitle />
            <div class="inherit calendarium-creator-inner">
                <div class="calendarium-creator-app">
                    <General />
                    <Dates />
                    <Celestials />
                    <Seasonal />
                    <Locations />
                    <Events />
                    <div class="setting-item-control calendarium-save-action" style="margin-top: 1rem;">
                    <button class="mod-cta" on:click={handleSaveCalendar}>
                        Salvar
                    </button>
                </div>
                </div>
            </div>
        {/if}
    </div>
{/if}

<style>
    .calendarium-creator,
    .calendarium-creator .calendarium-creator-inner,
    .calendarium-creator .calendarium-creator-app {
        background-color: var(--creator-background-color);
    }
    .calendarium-creator-app {
        overflow: auto;
        height: 100%;
    }
    .vertical-tab-content {
        padding: var(--size-4-8);
        padding-top: 0;
    }
</style>
