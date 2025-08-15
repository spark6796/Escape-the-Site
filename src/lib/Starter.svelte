<script module>
    import { gsap } from "gsap";
    import { LoadPanel } from '$lib/Panel.svelte'

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
    
    export function StartPreTitleAnimation(){
        let main_text = document.getElementById('main_text')
        let headphone_text = document.getElementById('headphone_text')
      
        const tl = gsap.timeline({
            onComplete: () => {
                document.getElementById('pre_title').hidden = true;
                StartTitleAnimation()
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
</script>

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