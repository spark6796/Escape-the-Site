
<script>
    import { gsap } from "gsap";
    import { Physics2DPlugin } from "gsap/Physics2DPlugin";
    import { ScrambleTextPlugin } from "gsap/ScrambleTextPlugin";
    import {TextPlugin} from "gsap/TextPlugin";
    import { onMount } from 'svelte';
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

    function StartGameAnimation(){
        let delayCounter = 0;
        for (const layer in Things){
            for (const char in Things[layer]){
                        
                        const char_element = document.getElementById(char)
                        const angle = Math.random() * 360;
                        const velocity = 20 + Math.random() * 200;
                        gsap.to(
                            char_element,
                            {
                                physics2D: 
                                    {velocity: velocity,angle:angle,gravity:-400},
                                duration: 5,
                                opacity:0,
                                ease: "power2.in",
                                delay: delayCounter/10,
                                onComplete:(()=>{
                                    let credit_element = document.getElementById('credit')
                                    gsap.to(credit_element,{opacity:0})
                                })
                            }

                        )   
                        delayCounter += 2             
                    }

                }
    }
    onMount(()=>{
        return
        let main_text = document.getElementById('main_text')
        let headphone_text = document.getElementById('headphone_text')
      
        gsap.to(
            main_text,
            {
                opacity:1,
                duration:5, 

                text: "🚨 This site contains Flashing lights and Jumpscares. 🚨",
                onComplete:(()=>{
                    gsap.to(
                    headphone_text,
                    {
                        opacity:1,
                        duration:3,
                    }
                )
                })
            }
        )
        gsap.to(
            main_text,
           {
                opacity:0,
                physics2D: 
                {  gravity: 400 },
                duration:4
                ,delay:8,
                onStart:(()=>{
                    let headphone_text = document.getElementById('headphone_text')
                    gsap.to(
                        headphone_text,
                        {opacity:0,
                            duration:1,
                        }
                    )
                }),
                onComplete: (()=>{
                    document.getElementById('pre_title').hidden = true
                    let title_element = document.getElementById('title')
                    title_element.hidden = false
                    gsap.to(
                        title_element,
                        {
                            opacity:1,
                            duration:4,
                            onComplete:(()=>{
                                StartGameAnimation()
                            })
                        }
                    )
                })

           }
        )


    })
</script>

<div class="flex justify-center items-center overflow-hidden h-screen w-full bg-black">
    
    <!-- PRE GAME ANIMATION START ---------- -->
    <div hidden id="pre_title" class="flex flex-col gap-20 justify-center items-center h-full w-full">
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

</div>



