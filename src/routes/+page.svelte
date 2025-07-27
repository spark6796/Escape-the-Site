
<script>
    import { gsap } from "gsap";
    import { Physics2DPlugin } from "gsap/Physics2DPlugin";
    import { ScrambleTextPlugin } from "gsap/ScrambleTextPlugin";
    import {TextPlugin} from "gsap/TextPlugin";
    import { onMount } from 'svelte';

    let alert_panel = $state(false)

    gsap.registerPlugin(
        ScrambleTextPlugin,
        Physics2DPlugin,
        TextPlugin
        );

    let Things = {
    First: {
        e1: 'E',
        s1: 'S',
        c1: 'C',
        a1: 'A',
        p1: 'P',
        e2: 'E'
    },
    Second: {
        t1: 'T',
        h1: 'H',
        e3: 'E'
    },
    Third: {
        s2: 'S',
        i1: 'I',
        t2: 'T',
        e4: 'E'
    }
    };

    function LoadPanel(){
        let panel = document.getElementById('panel')
        panel.hidden = false
        gsap.fromTo(
            panel,
            {
                opacity:0
            },

            {
                opacity:1,
                delay:1,
                duration:3,
                onComplete:(()=>{
                    StartPanelAnimations()
                })
            }
        )
    }

    function StartPanelAnimations(){
        let panel_title_container = document.getElementById('panel_title')
        gsap.to(
            panel_title_container,
            {
                duration:3,
                scrambleText:{
                    text:'ADMIN DASHBOARD',
                    chars: "X",
                },

                onComplete:(()=>{
                    StartPanelTextAnimation()
                })
            }
        )
    }

    function StartPanelTextAnimation(){
        let panel_text_container = document.getElementById('panel_text')
        const tl = gsap.timeline({
            onComplete:(()=>{
                
            })
        });
        tl.to(
            panel_text_container,
            {
                duration:3,
                text:'Welcome Admin.',
            }
        )
        .to(panel_text_container,
            {
                duration:0,
                delay:1,
                text: 'Verifying.'
            }
        )
        .to(panel_text_container,
            {
                duration:1,
                text: 'Verifying..'
            }
        )
        .to(panel_text_container,
            {
                duration:1,
                text: 'Verifying...'
            }
        )
        .call(()=>{
            alert_panel = true
        })
        .to(panel_text_container,
            {
                duration:1,
                text: 'YOU ARE FAKE'
            }
        )
    }


    function StartLoaderAnimation(){
        let loader_element = document.getElementById('loader')
        loader_element.hidden = false 
        gsap.to(
            loader_element,
            {
                opacity:1,
                delay:1,
                duration:1,
                onComplete:(()=>{
                    gsap.to(
                        loader_element,
                        {
                            opacity:0,
                            duration:1,
                            onComplete:(()=>{
                                document.getElementById('starter').hidden = true
                                LoadPanel()
                            })
                        }
                    )
                })
            })
    }

    function StartPreTitleAnimation(OnCompleteCallback){
        let main_text = document.getElementById('main_text')
        let headphone_text = document.getElementById('headphone_text')
      
        const tl = gsap.timeline({
            onComplete: () => {
                document.getElementById('pre_title').hidden = true;
                OnCompleteCallback();
            }
        });

        tl.to(main_text, {
            opacity: 1,
            duration: 5,
            text: "🚨 This site contains Flashing lights and Jumpscares. 🚨",
        })
        .to(headphone_text, {
            opacity: 1,
            duration: 3,
        })
        .to(headphone_text, {
            opacity: 0,
            duration: 1,
        })
        .to(main_text, {
            opacity: 0,
            duration: 4,
            ease: "power4.in",
            physics2D: { gravity: 400 }
        });

    }

    function StartTitleAnimation(){
        const title_element = document.getElementById('title');
        const credit_element = document.getElementById('credit');

        title_element.hidden = false

        const tl = gsap.timeline();

        tl.to(title_element, {
            opacity: 1,
            duration: 4
        });

        let delayCounter = 0;
        for (const layer in Things) {
            for (const char in Things[layer]) {
                const char_element = document.getElementById(char);
                const angle = Math.random() * 360;
                const velocity = 20 + Math.random() * 200;

                tl.to(char_element, {
                    physics2D: {
                        velocity: velocity,
                        angle: angle,
                        gravity: -400
                    },
                    duration: 3,
                    opacity: 0,
                    ease: "power2.in",
                    delay:3
                },delayCounter/10);

                delayCounter += 1;
            }
        }

        tl.to(credit_element, {
            opacity: 0,
            onComplete: () => {
                title_element.hidden = true;
                StartLoaderAnimation();
            }
        });


    }
    onMount(()=>{
        StartPreTitleAnimation(()=>{
            StartTitleAnimation()
        })

    })
</script>

<div class="h-screen w-full overflow-hidden bg-black">

<div id="starter" class="flex justify-center items-center overflow-hidden h-screen w-full bg-black">
    
    <!-- PRE GAME ANIMATION START ---------- -->
    <div id="pre_title" class="flex flex-col gap-20 justify-center items-center h-full w-full">
        <div id="main_text" class="opacity-0 font-bold text-4xl text-red-500"></div> 
        <div id="headphone_text" class="text-white opacity-0 font-bold text-3xl">
        🎧 Use headphones for best experience 🎧
        </div>
    </div>
    <div id="title" hidden class="flex flex-col justify-center items-center gap-10 font-bold text-9xl text-white h-full w-1/2 opacity-0">
        <div class="flex">
            {#each Object.entries(Things.First) as [id, char]}
                <div id={id}>
                {char}
                </div>
            {/each}
        </div> 
        <div class="flex text-7xl">
            {#each Object.entries(Things.Second) as [id, char]}
                <div id={id}>
                {char}
                </div>
            {/each}
        </div> 
        <div class="flex">
            {#each Object.entries(Things.Third) as [id, char]}
                <div id={id}>
                {char}
                </div>
            {/each}
        </div>

        <div id="credit" class="text-3xl"> 
            by Spark
        </div>

    </div>

    <!-- PRE GAME ANIMATION END ---------- -->

    <div hidden id="loader" class="h-1/4 opacity-0">
            <svg aria-hidden="true" class="w-full h-full animate-spin fill-blue-600 shadow-2xl shadow-blue-700 rounded-full" viewBox="0 0 100 101" fill="none" xmlns="http://www.w3.org/2000/svg">
            </svg>
    </div>

</div>
    


<div hidden id="panel" class="h-screen w-full overflow-hidden bg-black" style="{!alert_panel ? 'background: radial-gradient(circle,rgba(0, 0, 0, 1) 24%, rgba(0, 6, 41, 1) 84%);': 'background: radial-gradient(circle, rgba(0, 0, 0, 1) 60%, rgb(41, 0, 0) 99%)'}">

    <div class="flex justify-center items-center text-6xl font-bold {!alert_panel ? 'text-blue-400' : 'text-red-800'} h-1/4">
        <img hidden={!alert_panel ? true : false} src="alarm.gif" alt="🚨" class="h-full w-1/3 animate-pulse"/>
        <div id="panel_title" class="w-full text-center {alert_panel ? 'animate-pulse':''}"></div>  
        <img hidden={!alert_panel ? true : false} src="alarm.gif" alt="🚨" class="h-full w-1/3 animate-pulse"/>
    </div>
    <div class="flex justify-center items-center h-3/4 w-full text-white text-4xl font-bold">
        <div id="panel_text" class="border-8 {!alert_panel ? 'border-blue-900 bg-blue-950' : 'border-red-900 bg-red-950'}  p-10 rounded-2xl">
            
        </div>
    </div>
</div>

</div>


