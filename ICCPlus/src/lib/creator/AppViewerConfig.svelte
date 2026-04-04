<Dialog
    bind:open
    escapeKeyAction={currentDialog === 'none' ? 'close' : ''}
    surface$style="width: 1200px; max-width: calc(100vw - 32px);"
    onSMUIDialogClosed={onclose}
>
    <Title tabindex={0} autofocus>
        Viewer Configuration
    </Title>
    <Content class="p-0">
        <Card class="no-shadow">
            <CardContent>
                <div class="container-fluid">
                    
                    <div class="row pb-2">
                        <div class="col-12 text-center">Loading Screen</div>
                        <div class="col-xl-3 col-lg-6 col-12 gy-2">
                            <div class="col-12 px-3">Background Color</div>
                            <ColorPicker bind:hex={app.viewerConfig.loadingBgColor} components={ChromeVariant} sliderDirection="horizontal" />
                            {#if app.viewerConfig.loadingBgImage}
                                <button type="button" onclickcapture={() => {currentDialog = 'appImageUpload'; imgProp = 'loadingBgImage'}} class="btn--image-background">
                                    <img src={app.viewerConfig.loadingBgImage} alt="Loading" loading="lazy" class="btn--image" style="max-width: 100px;" />
                                </button>
                            {/if}
                            <Button onclickcapture={() => {currentDialog = 'appImageUpload'; imgProp = 'loadingBgImage'}} variant="raised">
                                <Label>{app.viewerConfig.loadingBgImage === '' ? 'Set Background Image' : 'Change Image'}</Label>
                            </Button>
                            <div class="col-12 text-center">A large image may not be fully displayed before loading ends.</div>
                        </div>
                        <div class="col-xl-3 col-lg-6 col-12 gy-2">
                            <div class="col-12 px-3">Track Color</div>
                            <ColorPicker bind:hex={app.viewerConfig.loadingTrackColor} components={ChromeVariant} sliderDirection="horizontal" />
                            <div class="col-12 px-3 pt-3">Progression Color</div>
                            <ColorPicker bind:hex={app.viewerConfig.loadingCircleColor} components={ChromeVariant} sliderDirection="horizontal" />
                        </div>
                        <div class="col-xl-3 col-lg-6 col-12 gy-2">
                            <div class="col-12 px-3">Text Color</div>
                            <ColorPicker bind:hex={app.viewerConfig.loadingTextColor} components={ChromeVariant} sliderDirection="horizontal" />
                            <div class="col-12 px-3 pt-3">Text Shadow Color</div>
                            <ColorPicker bind:hex={app.viewerConfig.loadingTextShadow} components={ChromeVariant} sliderDirection="horizontal" />
                        </div>
                        <div class="col-xl-3 col-lg-6 col-12 gy-2">
                            <Select bind:value={app.viewerConfig.loadingType} label="Loading Type" variant="filled" alwaysFloat={true}>
                                {#each types as type (type.text)}
                                    <Option value={type.value}>{type.text}</Option>
                                {/each}
                            </Select>
                            <Textfield bind:value={app.viewerConfig.loadingText} label="Loading Text" variant="filled" />
                            <Select class="mb-4" bind:value={app.viewerConfig.loadingTextFont} label="Text Font" variant="filled">
                                {#each textFonts as textFont}
                                    <Option value={textFont}>{textFont}</Option>
                                {/each}
                            </Select>
                        </div>
                    </div>
                    <Accordion class="mb-4">
                        <Panel class="bordered-panel{panel01 ? ' on-top' : ''}" bind:open={panel01} variant="unelevated" conditionalRender={true}>
                            <Header class="p-0">
                                Loading Preview
                                {#snippet icon()}
                                    <IconButton toggle pressed={panel01} size="mini">
                                        <Icon class="mdi mdi-chevron-up" on></Icon>
                                        <Icon class="mdi mdi-chevron-down"></Icon>
                                    </IconButton>
                                {/snippet}
                            </Header>
                            <AcdContent class="mdc-dialog__content">
                                <div class="row">
                                    <div class="col-12 d-flex justify-center align-items-center position-relative" style={indBg} >
                                        <div class="indicator {app.viewerConfig.loadingType}" style={indicator}>
                                            <div>{app.viewerConfig.loadingText}<div>{loadingType}</div></div>
                                        </div>
                                    </div>
                                </div>
                            </AcdContent>
                        </Panel>
                    </Accordion>
                    <div class="row mb-4">
                        <div class="col-12 text-center">Tab</div>
                        <div class="col-lg-4 gy-2">
                            <div class="col-12 text-center px-5">
                                {#if app.viewerConfig.favicon}
                                    <button type="button" onclickcapture={() => {currentDialog = 'appImageUpload'; imgProp = 'favicon'}} class="btn--image-background">
                                        <img src={app.viewerConfig.favicon} alt="Favicon" loading="lazy" class="btn--image" style="max-width: 48px; aspect-ratio: 1 / 1;" />
                                    </button>
                                {/if}
                                <Button onclickcapture={() => {currentDialog = 'appImageUpload'; imgProp = 'favicon'}} variant="raised">
                                    <Label>{app.viewerConfig.favicon === '' ? 'Set Favicon' : 'Change Favicon'}</Label>
                                </Button>
                            </div>
                            <div class="col-12 text-center">
                                Tab icon. Square image (32x32 recommended).
                            </div>
                        </div>
                        <div class="col-lg-8 gy-2">
                            <Textfield bind:value={app.viewerConfig.title} onchange={() => {
                                if (app.viewerConfig.title === '') app.viewerConfig.title = 'CYOA Plus 2';
                            }} label="Tab Title" variant="filled" />
                            This text appears in browser tabs and search results.
                        </div>
                    </div>
                    <div class="row mb-4">
                        <div class="col-12 text-center">Export Options</div>
                        <div class="col-lg-6 gy-2">
                            <FormField>
                                <Switch bind:checked={app.viewerConfig.useSeparateImages} color="secondary" onSMUISwitchChange={() => {
                                    if (app.viewerConfig.useLocalViewer) app.viewerConfig.useLocalViewer = false;
                                }}  class="switch-scale" />
                                {#snippet label()}
                                    Export Images Separately
                                {/snippet}
                            </FormField>
                            <div class="col-12 text-center">Separating images from the JSON file can significantly improve initial loading speed.</div>
                        </div>
                        <div class="col-lg-6 gy-2">
                            <FormField>
                                <Switch bind:checked={app.viewerConfig.useLocalViewer} color="secondary" onSMUISwitchChange={() => {
                                    if (app.viewerConfig.useSeparateImages) app.viewerConfig.useSeparateImages = false;
                                }} class="switch-scale" />
                                {#snippet label()}
                                    Export with Local Viewer
                                {/snippet}
                            </FormField>
                            <div class="col-12 text-center">Embed the data into the viewer so it can run offline without an internet connection.<br>(External images, audio, or fonts require an internet connection.)</div>
                        </div>
                    </div>
                </div>
            </CardContent>
        </Card>
    </Content>
    <Actions>
        <Button action="close">
            <Label class="dialog-actions--btn">Close</Label>
        </Button>
    </Actions>
</Dialog>
{#if currentDialog === 'appImageUpload'}
    <ImageUpload open={currentDialog === 'appImageUpload'} onclose={() => (currentDialog = 'none')} imgObject={app.viewerConfig} imgProp={imgProp} canHaveURL={true} />
{/if}
<script lang="ts">
    import Accordion, { Panel, Header, Content as AcdContent} from '$lib/custom/accordion';
    import Button, { Label } from '@smui/button';
    import Card, { Content as CardContent } from '@smui/card';
    import ColorPicker, { ChromeVariant } from '$lib/custom/svelte-awesome-color-picker';
    import Dialog, { Title, Content, Actions } from '@smui/dialog';
    import FormField from '@smui/form-field';
    import IconButton, { Icon } from '@smui/icon-button';
    import ImageUpload from '$lib/store/ImageUpload.svelte';
    import Textfield from '$lib/custom/textfield/Textfield.svelte';
    import Select, { Option } from '$lib/custom/select';
    import Switch from '@smui/switch';
    import { app, textFonts } from '$lib/store/store.svelte';

    let { open, onclose }: { open: boolean; onclose: () => void; } = $props();
    const types = [{
        value: 'ind1',
        text: 'Type 1 (Loaded Size)'
    }, {
        value: 'ind2',
        text: 'Type 2 (Loading Progress)'
    }, {
        value: 'ind3',
        text: 'Type 3 (Loaded / Total Size)'
    }];
    let panel01 = $state(false);
    let currentDialog = $state<'none' | 'appImageUpload'>('none');
    let imgProp = $state('');
    let indBg = $derived.by(() => {
        const styles: string[] = [];

        styles.push(`background: ${app.viewerConfig.loadingBgColor};`);
        styles.push(`background-image: url('${app.viewerConfig.loadingBgImage}');`);
        styles.push(`background-size: 100% 100%;`);
        styles.push('min-height: 500px;');

        return styles.join(' ');
    });
    let indicator = $derived.by(() => {
        const styles: string[] = [];

        styles.push(`--color: ${app.viewerConfig.loadingTextColor};`);
        styles.push(`--circle: ${app.viewerConfig.loadingCircleColor};`);
        styles.push(`--track: ${app.viewerConfig.loadingTrackColor};`);
        styles.push(`--shadow: ${app.viewerConfig.loadingTextShadow};`);
        styles.push(`--font: ${app.viewerConfig.loadingTextFont};`);

        return styles.join(' ');
    });
    let loadingType = $derived.by(() => {
        if (app.viewerConfig.loadingType === 'ind3') return '0.0/0.0 MB';
        if (app.viewerConfig.loadingType === 'ind2') return '0.0 %';
        
        return '0.0 MB';
    });
</script>