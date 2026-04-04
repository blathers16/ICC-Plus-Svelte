<Dialog
    bind:open
    surface$style="width: 1920px; max-width: calc(100vw - 32px);"
    onSMUIDialogClosed={onclose}
>
    <Title class="dialog-title" tabindex={0} autofocus>
        Sound Effects
    </Title>
    <Content id="dialog--sfx" class="pb-2">
        <div style="position: relative; height: {$virtualizer.getTotalSize()}px; width: 100%;">
            {#each $virtualizer.getVirtualItems() as sRow (sRow.index)}
                <div class="py-3" data-index={sRow.index} use:observeResize={sRow.index} style="position: absolute; top: {sRow.start}px; width: 100%;">
                    <div class="row g-3 pb-3">
                        {#each sfxRows[sRow.index] as sfx, i}
                            <div class="col-xl-4 col-12">
                                <div class="point-slot">
                                    <div class="toolbar grey lighten-3 justify-space-around">
                                        <Wrapper text="Move Up">
                                            <IconButton class="mdi mdi-chevron-left" onclickcapture={() => moveSfxUp(sfx, sRow.index * 3 + i)} />
                                        </Wrapper>
                                        <Wrapper text="Delete Sound Effect">
                                            <IconButton class="mdi mdi-delete-forever" onclickcapture={() => deleteSfx(sfx)} />
                                        </Wrapper>
                                        <Wrapper text="Play Sound Effect">
                                            <IconButton class="mdi mdi-play" onclickcapture={() => playSfx(sfx)} />
                                        </Wrapper>
                                        <Wrapper text="Create New Requirement">
                                            <IconButton class="mdi mdi-key-plus" onclickcapture={() => {currentDialog = 'appRequirement'; data = sfx;}} />
                                        </Wrapper>
                                        <Wrapper text="Move Down">
                                            <IconButton class="mdi mdi-chevron-right" onclickcapture={() => moveSfxDown(sfx, sRow.index * 3 + i)} />
                                        </Wrapper>
                                    </div>
                                    <div class="row gy-4 p-3">
                                        {#if getFileSize(sfx.audio) > 1024000}
                                            <div class="col-12 text-center text-danger">Warning: The audio file size is too large and may affect performance on certain devices.</div>
                                        {/if}
                                        <div class="d-column col-12">
                                            <FormField>
                                                <Switch bind:checked={sfx.isDefault} color="secondary" class="switch-scale" />
                                                {#snippet label()}
                                                    This sound effect applies to all choices.
                                                {/snippet}
                                            </FormField>
                                            {#if sfx.isDefault}
                                                <FormField>
                                                    <Switch bind:checked={sfx.onSelected} color="secondary" class="switch-scale" />
                                                    {#snippet label()}
                                                        Play on choice select
                                                    {/snippet}
                                                </FormField>
                                                <FormField>
                                                    <Switch bind:checked={sfx.onDeselected} color="secondary" class="switch-scale" />
                                                    {#snippet label()}
                                                        Play on choice deselect
                                                    {/snippet}
                                                </FormField>
                                            {/if}
                                        </div>
                                        <div class="col-sm-6 col-12">
                                            <Textfield bind:value={sfx.id} onfocus={() => sfxId = sfx.id} onchange={() => changeSfxId(sfx)} label="Sfx Id" variant="filled" />
                                        </div>
                                        <div class="col-sm-6 col-12">
                                            <Textfield bind:value={sfx.name} label="Sfx Name" variant="filled" />
                                        </div>
                                        
                                        {#if sfx.isDefault}
                                            <div class="col-12">
                                                <CustomChipInput acValue={sfx.groups} acOptions={getGroups()} inputLabel="Group Id" getLabel={getGroupLabel} />
                                            </div>
                                        {/if}
                                        <div class="col-sm-6 col-12">
                                            <div class="px-3">Volume: {(sfx.volume * 100) | 0} %</div>
                                            <div class="px-3 music-player--volume">
                                                <Slider bind:value={sfx.volume} min={0} max={1} step={0.01} class="mx-2" onpointerup={onSliderUp} />
                                            </div>
                                        </div>
                                        <div class="col-sm-6 col-12">
                                            <div class="px-3">Pitch: {sfx.pitch >= 0 ? '+' : ''}{sfx.pitch / 100} st.</div>
                                            <div class="px-3 music-player--volume">
                                                <Slider bind:value={sfx.pitch} min={-1200} max={1200} step={100} class="mx-2" onpointerup={onSliderUp} />
                                            </div>
                                        </div>
                                    </div>
                                    <div class="row gx-0">
                                        <div class="col p-2">
                                            {#if sfx.requireds.length > 0}
                                                <Accordion>
                                                    <Panel variant="unelevated">
                                                        <Header class="p-0">
                                                            Requirements: {sfx.requireds.length}
                                                        </Header>
                                                        <AcdContent>
                                                            <div class="row gy-4">
                                                                {#each sfx.requireds as required, j}
                                                                    <div class="{(required.requireds && required.requireds.length > 0) || required.type === 'or' ? 'col-12' : 'col-sm-6 col-12'} p-2">
                                                                        <ObjectRequired required={required} isEditModeOn={true} />
                                                                        <Button onclickcapture={() => sfx.requireds.splice(j, 1)} class="mt-1" variant="raised">
                                                                            <Label>Delete</Label>
                                                                        </Button>
                                                                    </div>
                                                                {/each}
                                                            </div>
                                                        </AcdContent>
                                                    </Panel>
                                                </Accordion>
                                            {/if}
                                        </div>
                                    </div>
                                </div>
                            </div>
                        {/each}
                        {#if sRow.index === sfxRows.length - 1}
                            <div class="col-xl-4 col-12">
                                <button type="button" class="create-box col-12" style="min-height: 286px; font-size: 40px;" onclick={() => fileInput.click()} aria-label="Upload New Sound Effect">
                                    <i class="mdi mdi-plus-thick"></i>
                                </button>
                            </div>
                        {/if}
                    </div>
                </div>
            {/each}
        </div>
    </Content>
    <Actions>
        <div class="container-fluid">
            <div class="row p-0">
                <div class="col-sm-6 col-12">
                    <input type="file" accept=".mp3, .wav, .ogg, .m4a, .aac" bind:this={fileInput} bind:files={valueTypeFiles} onchange={uploadNewSfx} style="display: none;">
                    <Button action="" onclick={() => fileInput.click()}>
                        <Label class="dialog-actions--btn">Upload New Sound Effect</Label>
                    </Button>
                </div>
                <div class="col-sm-6 col-12">
                    <Button action="close">
                        <Label class="dialog-actions--btn">Close</Label>
                    </Button>
                </div>
            </div>
        </div>
    </Actions>
</Dialog>
{#if currentDialog === 'appRequirement' && typeof data !== 'undefined'}
    <AppRequirement open={currentDialog === 'appRequirement'} onclose={() => (currentDialog = 'none')} data={data} />
{/if}
<script lang="ts">
    import Accordion, { Panel, Header, Content as AcdContent} from '$lib/custom/accordion';
    import AppRequirement from '../AppRequirement.svelte';
    import Button, { Label } from '@smui/button';
    import Dialog, { Title, Content, Actions } from '@smui/dialog';
    import FormField from '@smui/form-field';
    import IconButton from '@smui/icon-button'; 
    import ObjectRequired from '../Object/ObjectRequired.svelte';
    import Slider from '@smui/slider';
    import Switch from '@smui/switch';
    import Textfield from '$lib/custom/textfield/Textfield.svelte';
    import { Wrapper } from '$lib/custom/tooltip';
    import { app, checkDupId, sfxMap, generateId, scrollToLastRow, loadSfx, playSfx, audioBuffers, snackbarVariables, getGroups, groupMap, getGroupLabel } from '$lib/store/store.svelte';
    import type { Requireds, SoundEffect } from '$lib/store/types';
    import { createVirtualizer } from '@tanstack/svelte-virtual';
    import { onMount } from 'svelte';
    import CustomChipInput from '$lib/store/CustomChipInput.svelte';
    
    let { open, onclose }: { open: boolean; onclose: () => void; } = $props();
    let currentDialog = $state<'none' | 'appRequirement'>('none');
    let data = $state<SoundEffect | Requireds>();
    let valueTypeFiles: FileList | null = $state(null);
    let fileInput: HTMLInputElement;
    let sfxId = '';
    let sfxRows = $derived.by(() => {
        const result: SoundEffect[][] = [];
        for (let i = 0; i < app.soundEffects.length; i += 3) {
            result.push(app.soundEffects.slice(i, i + 3));
        }
        if (result.length === 0) result.push([]);
        return result;
    });
    const rowCount = () => sfxRows.length;
    
    let virtualListEl = $state<HTMLDivElement>(document.getElementById('dialog--sfx') as HTMLDivElement);
    let virtualizer = $state(createVirtualizer({
        count: rowCount(),
        getScrollElement: () => virtualListEl,
        estimateSize: () => 358,
        overscan: 1,
        measureElement: (el) => Math.max(el.getBoundingClientRect().height, 358) //+72px
    }));


    onMount(() => {
        virtualListEl = document.getElementById('dialog--sfx') as HTMLDivElement;
        $virtualizer.setOptions({
            getScrollElement: () => virtualListEl,
            estimateSize: () => 358,
            measureElement: (el) => Math.max(el.getBoundingClientRect().height + 100, 358)
        });
    });

    function observeResize(el: HTMLDivElement, index: number) {

        const observer = new ResizeObserver(() => {
            $virtualizer.measureElement(el);
        });
        observer.observe(el);

        return {
            destroy() {
                observer.disconnect();
            }
        };
    }

    function changeSfxId(sfx: SoundEffect) {
        if (sfx.id === '') {
            sfx.id = sfxId;
        } else {
            if (sfx.id !== sfxId) {
                sfx.id = checkDupId(sfx.id, sfxMap);
                sfxMap.set(sfx.id, sfx);
                sfxMap.delete(sfxId);
                
                const buffer = audioBuffers.get(sfxId);
                if (buffer) {
                    audioBuffers.set(sfx.id, buffer);
                    audioBuffers.delete(sfxId);
                }
                sfxId = sfx.id;
            }
        }
    }

    function uploadNewSfx() {
        if (!valueTypeFiles) return;

        const file = valueTypeFiles[0];
        const allowed = ['mp3','wav','ogg','m4a','aac'];
        const ext = file.name.split('.').pop()?.toLowerCase();
        if (!ext || !allowed.includes(ext)) {
            snackbarVariables.isOpen = true;
            snackbarVariables.labelText = 'Failed to upload audio: Invalid file type.';
            return;
        }
        const reader = new FileReader;
        
        reader.onload = async () => {
            const audio = reader.result as string;
            const id = generateId(0, 4, 'sfx');

            app.soundEffects.push({
                id: id,
                name: file.name.replace(/\.[^/.]+$/, ""),
                audio: audio,
                volume: 1,
                pitch: 0,
                isDefault: false,
                onSelected: false,
                onDeselected: false,
                requireds: [],
                groups: []
            });
            sfxMap.set(id, app.soundEffects[app.soundEffects.length - 1]);
            loadSfx(id, audio);

            $virtualizer.setOptions({
                count: rowCount()
            });
            scrollToLastRow($virtualizer, virtualListEl, app.soundEffects.length - 1);
        }

        reader.readAsDataURL(file);
    }

    function deleteSfx(sfx: SoundEffect) {
        app.soundEffects.splice(app.soundEffects.indexOf(sfx), 1);
        sfxMap.delete(sfx.id);
        audioBuffers.delete(sfx.id);

        $virtualizer.setOptions({
            count: rowCount()
        });
    }

    function moveSfxUp(sfx: SoundEffect, num: number) {
        if (num > 0) {
            const prevIdx = app.soundEffects.indexOf(app.soundEffects[num - 1]);
            const curIdx = app.soundEffects.indexOf(sfx);
            [app.soundEffects[prevIdx], app.soundEffects[curIdx]] = [app.soundEffects[curIdx], app.soundEffects[prevIdx]];
        }
    }

    function moveSfxDown(sfx: SoundEffect, num: number) {
        if (num < app.soundEffects.length - 1) {
            const nextIdx = app.soundEffects.indexOf(app.soundEffects[num + 1]);
            const curIdx = app.soundEffects.indexOf(sfx);
            [app.soundEffects[curIdx], app.soundEffects[nextIdx]] = [app.soundEffects[nextIdx], app.soundEffects[curIdx]];
        }
    }

    function onSliderUp() {
        (document.activeElement as HTMLElement)?.blur();
    }

    function getFileSize(str: string): number {
        const raw = str.split(',')[1];
        const padding = (raw.endsWith('==') ? 2 : raw.endsWith('=') ? 1 : 0);
        return Math.floor(raw.length * 3 / 4 - padding);
    }
</script>