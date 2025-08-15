<script module>
    import { gsap } from "gsap";
    import { non_click_overlay } from '$lib/stores'
    import { StartPreTicTacToe } from '$lib/TicTacToe.svelte'

    let exit_buttons_shadow = $state('shadow-red-400')
    let exit_button_counter = $state(0)

    export function StartExitButtonPuzzle(){
        document.getElementById('exit_button_container').hidden = false
        let my_popping_list = [...Array(9).keys()].map(n => n + 1)
        for (let i = my_popping_list.length - 1; i > 0; i--) {
        let j = Math.floor(Math.random() * (i + 1));
            [my_popping_list[i], my_popping_list[j]] = [my_popping_list[j], my_popping_list[i]];
        }
        for (const exit_button_id of my_popping_list){
            let exit_button = document.getElementById('exit_'+exit_button_id)
            gsap.to(exit_button,
                {
                    opacity:1,
                    duration:2,
                    delay:1 + Math.random() * 2,
                    scrambleText:{
                        text:'EXIT',
                        chars: "X",
                    },
                }
            )
        }
        setTimeout(() => {
            exit_buttons_shadow = "shadow-red-700"
            document.getElementById('flashbangs_container').hidden = false
            non_click_overlay.set(false)
        }, 4500);
    }

    function ExitButtonCallback(button_id) {
        non_click_overlay.set(true)
        let button = document.getElementById(`exit_${button_id}`);
        let flashbang = document.getElementById(`flashbang_${button_id}`);

        exit_button_counter++;
        const tl = gsap.timeline({
            onComplete: () => {
                non_click_overlay.set(false)
                if (exit_button_counter >=2 ){
                    StartPreTicTacToe()
                }
            },
        });

        if (exit_button_counter == 1) {
            let white_screen = document.getElementById("white_screen");

            tl.to(button, {
                rotateX: 90,
                rotateY: 360,
                rotateZ: 20,
                opacity: 0,
                duration: 1,
            })
                .call(() => {
                    flashbang.classList.add("z-40");
                })
                .to(flashbang, {
                    scale: 5,
                    rotate: 360,
                })
                .call(() => {
                    white_screen.hidden = false;
                    flashbang.hidden = true;
                })
                .fromTo(
                    white_screen,
                    {
                        opacity: 1,
                    },
                    {
                        opacity: 0,
                        duration: 3,
                    },
                )
                .call(() => {
                    button.hidden = true;
                    white_screen.hidden = true;
                });
        } else {
            flashbang.hidden = true;
            let main = document.getElementById('exit_button_container')
            let jumpscare = document.getElementById("jumpscare");

            tl.to(button, {
                rotateX: 90,
                rotateY: 360,
                rotateZ: 20,
                opacity: 0,
                duration: 1,
            })
                .call(() => {
                    main.hidden = true;
                    jumpscare.hidden = false;
                })
                .to(jumpscare, {
                    opacity: 1,
                    duration: 1,
                    onComplete: () => (jumpscare.hidden = true),
                });
        }
    }
</script>

<div hidden id="exit_button_container" class="h-screen w-full bg-black overflow-hidden" style="background: radial-gradient(circle, rgba(0, 0, 0, 1) 60%, rgb(41, 0, 0) 99%)">
    
    {#each { length: 3 }, i}
        <div class="flex h-1/3 w-full text-white">
            {#each { length: 3 }, j}
                <div class="flex justify-center items-center h-full w-1/3 z-20">
                    <!-- svelte-ignore a11y_consider_explicit_label -->
                    <button
                        onclick={() => ExitButtonCallback(j + 1 + i * 3)}
                        id={`exit_${j + 1 + i * 3}`}
                        class="border-8 w-1/2 h-1/2 opacity-0 shadow-2xl {exit_buttons_shadow} rounded-4xl border-red-900 bg-red-950 font-bold text-5xl hover:border-red-500"
                    >
                        Exit
                    </button>
                </div>
            {/each}
        </div>
    {/each}
    
    <div hidden id="flashbangs_container" class="absolute top-0 h-screen w-full overflow-hidden">
            {#each {length: 3} , i}
                <div class="flex h-1/3 w-full text-white">
                    {#each {length: 3} , j}
                        <div class="flex justify-center items-center h-full w-1/3">
                            <img id={`flashbang_${j+1 + (i * 3)}`} src="flashbang.png" alt="" class="h-1/2">
                        </div>
                    {/each}
                </div>
            {/each}
    </div>
</div>

