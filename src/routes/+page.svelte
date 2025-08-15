<script>
    import { gsap } from "gsap";
    import { shuffle } from "gsap/gsap-core";
    import { Physics2DPlugin } from "gsap/Physics2DPlugin";
    import { ScrambleTextPlugin } from "gsap/ScrambleTextPlugin";
    import { TextPlugin } from "gsap/TextPlugin";
    
    import { onMount } from "svelte";
    
    // Screens import
    import ExitButtonGame from '$lib/ExitButtonGame.svelte'
    import Panel from "$lib/Panel.svelte";
    import TicTacToe from "$lib/TicTacToe.svelte";
    import Starter from "$lib/Starter.svelte";

    import { non_click_overlay } from "$lib/stores.js";
    import { alert_panel } from "$lib/stores.js";

    import { StartPreTitleAnimation } from "$lib/Starter.svelte"

    gsap.registerPlugin(ScrambleTextPlugin, Physics2DPlugin, TextPlugin);

    onMount(() => {
        StartPreTitleAnimation();
    });
</script>

<div
    hidden={!$non_click_overlay}
    class="absolute top-0 h-full w-full z-30"
></div>
<div
    hidden
    id="white_screen"
    class="absolute top-0 h-full w-full flasher z-40 bg-white"
></div>
<video
    id="jumpscare"
    src="/jumpscare.mp4"
    autoplay
    loop
    muted
    hidden
    class="absolute z-40 top-0 w-full h-full object-cover opacity-0"
></video>

<div
    hidden
    id="text_box_parent"
    class="flex absolute overflow-hidden items-end justify-center h-3/4 w-full text-white text-4xl font-bold"
>
    <div
        id="text_box_text"
        class="flex justify-center items-center border-8 h-1/4 {!$alert_panel
            ? 'border-blue-900 bg-blue-950'
            : 'border-red-900 bg-red-950'} p-10 rounded-2xl"
    ></div>
</div>

<div class="h-screen w-full bg-black">
    <Starter />

    <Panel />

    <ExitButtonGame/>

    <TicTacToe />
</div>
