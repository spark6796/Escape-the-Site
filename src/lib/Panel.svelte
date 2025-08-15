<script module>
    import { gsap } from "gsap";


    import { StartExitButtonPuzzle } from "./ExitButtonGame.svelte";
 
    import { alert_panel } from '$lib/stores'

    export function LoadPanel(){
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
        let panel_container = document.getElementById('text_box_parent')
        let panel_text_container = document.getElementById('text_box_text')
        panel_container.hidden = false
        const tl = gsap.timeline({
            onComplete:(()=>{
                panel_container.hidden = true;
                gsap.set(panel_text_container, { clearProps: "all" });
                document.getElementById('panel').hidden = true;
                StartExitButtonPuzzle()
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
            alert_panel.set(true)
        })
        .to(panel_text_container,
            {
                duration:1,
                text: 'YOU ARE FAKE'
            }
        )
        .to(panel_text_container,
            {
                duration:1,
                delay:2,
                scrambleText:{
                    text:'🔒 Lockdown initiated 🔒',
                    chars: "xX",
                },
            }
        )
        .to(panel_text_container,
            {
                duration:3,
                delay:1,
                text:'Welcome fellow intruder, Now you are stuck.',

            }
        )
        .call(()=>{
            let panel_main = document.getElementById('panel_main')
            gsap.to(panel_main,{opacity:0,duration:3})
        })
        .to(
            panel_text_container,
            {
                duration:3,
                delay: 1,
                text:'You want to exit? Lets play some games first'
            }
        )
        .to(
            panel_text_container,
            {
                scale:50,
                scrambleText:{
                    chars: "Ha",
                },
                delay:3,
                opacity:0,
                duration:4
            }
        )
    }
</script>

<div hidden id="panel" class="h-screen w-full overflow-hidden bg-black" style="{!$alert_panel ? 'background: radial-gradient(circle,rgba(0, 0, 0, 1) 24%, rgba(0, 6, 41, 1) 84%);': 'background: radial-gradient(circle, rgba(0, 0, 0, 1) 60%, rgb(41, 0, 0) 99%)'}">

        <div id="panel_main" class="flex justify-center items-center text-6xl font-bold {!$alert_panel ? 'text-blue-400' : 'text-red-800'} h-1/4">
            <img hidden={!$alert_panel ? true : false} src="alarm.gif" alt="🚨" class="h-full w-1/3 animate-pulse"/>
            <div id="panel_title" class="w-full text-center {$alert_panel ? 'animate-pulse':''}"></div>  
            <img hidden={!$alert_panel ? true : false} src="alarm.gif" alt="🚨" class="h-full w-1/3 animate-pulse"/>
        </div>
        
    </div>