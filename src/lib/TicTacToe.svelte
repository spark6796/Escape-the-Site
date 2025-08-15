<script module>
import { gsap } from "gsap";
import { shuffle } from "gsap/gsap-core";
import { non_click_overlay } from '$lib/stores.js';

let player_turn = $state(true)
let board = $state(
    [
        '','','',
        '','','',
        '','',''
    ]
)
let all_hidden = $state(false)
let winning_ids = $state([])

export function StartPreTicTacToe(){
        let text_box_parent = document.getElementById('text_box_parent')
        text_box_parent.hidden = false
        let text_box_text = document.getElementById('text_box_text')
        const tl = gsap.timeline({
            onComplete: () => {
                text_box_parent.hidden = true;
                gsap.set(text_box_text, { clearProps: "all" });
                text_box_text.innerText = ""
                
            }
        });
        tl.to(
            text_box_text,
            {
                duration:3,
                delay:2,
                opacity:1,
                text:'Haha You you suck. I Expected better.',
            }
        )
        .to(text_box_text,
            {
                duration:2,
                text: 'Cmon now try to beat me in tactactoe.'
            }
        )
        .call(()=>{
            StartTicTacToe()
        })
        .to(text_box_text,
            {
                x: 3000,
                delay:1,
                duration:2
            }
        )
}

function HandleTicTacToeInput(row,column){
    let button_id = `${row}${column}`    
    let element = document.getElementById(button_id)
    let index = row * 3 + column
    board[index] = "O"
    player_turn = false;
    MakeComputerTurn()
}

function GetTicTacToeStyles(button_id){
        let upper_sides = ['00','01','02']
        let left_sides = ['00','10','20']
        let bottom_sides = ['20','21','22']
        let right_sides = ['02','12','22']

        let classStr = [];
        if (upper_sides.includes(button_id)){
            classStr+='border-t-0 '
        }
        if (left_sides.includes(button_id)){
            classStr+='border-l-0 '
        }
        if (bottom_sides.includes(button_id)){
            classStr+='border-b-0 '
        }
        if (right_sides.includes(button_id)){
            classStr+='border-r-0 '
        }

        return classStr
}

function getWinningCombination(player) {
    const winPatterns = [
        [0, 1, 2], [3, 4, 5], [6, 7, 8], 
        [0, 3, 6], [1, 4, 7], [2, 5, 8], 
        [0, 4, 8], [2, 4, 6]
    ];
    
    const winningPattern = winPatterns.find(pattern => 
        pattern.every(index => board[index] === player)
    );
    
    return winningPattern; 
}

function checkWin(player) {
    const winPatterns = [
        [0, 1, 2], [3, 4, 5], [6, 7, 8], 
        [0, 3, 6], [1, 4, 7], [2, 5, 8], 
        [0, 4, 8], [2, 4, 6]
    ];
    
    return winPatterns.some(pattern => 
        pattern.every(index => board[index] === player)
    );
}

function minimax(player, depth = 0) {

    if (checkWin("X")) return { score: 10 - depth };
    if (checkWin("O")) return { score: depth - 10 };
    
    const availSpots = board.map((v, i) => v === "" ? i : null).filter(v => v !== null);
    if (availSpots.length === 0) return { score: 0 };

    const moves = [];

    for (const spot of availSpots) {
        const move = { index: spot };
        
        board[spot] = player;
        
        if (player === "X") {
            move.score = minimax("O", depth + 1).score;
        } else {
            move.score = minimax("X", depth + 1).score;
        }
        
        board[spot] = "";
        moves.push(move);
    }

    if (player === "X") {
        return moves.reduce((best, move) => 
            move.score > best.score ? move : best
        );
    } else {
        return moves.reduce((best, move) => 
            move.score < best.score ? move : best
        );
    }
}

function ShowWinningAnimation(){
    let winning_combination = getWinningCombination('X')
    let winning_elements = []
    for (const cell of winning_combination){
        let row = Math.floor(cell / 3) 
        let column = cell % 3
        winning_elements.push(document.getElementById(`${row}${column}`))
    }


    for (const element in winning_elements){
        winning_ids.push(winning_elements[element].id)
        let tl = gsap.timeline(
            {
                delay:2,

                onComplete:()=>{
                    winning_elements[element].style.opacity = 0
                    let white_screen = document.getElementById("white_screen");
                    white_screen.hidden = false;
                    gsap.fromTo(
                        white_screen,
                        {
                            opacity:1,
                            duration:1/5
                        },
                        {
                            opacity:0
                        }
                    )
                    setTimeout(() => {
                        white_screen.hidden = true;
                        GameEndCallback()
                    },2000)

                }
            })
        
        tl.call(()=>{
            all_hidden = true
            winning_elements[element].style.border = 0;
        }
        )
        .to(
            winning_elements[element],
            {
                delay:element/2,
                scale:3
            }
        
        )
        
        for (let i = 1 ; i<4; i++){
            setTimeout(() => {
                    winning_elements[element].classList.replace('text-red-500','text-green-500')
                    setTimeout(() => {
                        winning_elements[element].classList.replace('text-green-500','text-red-500')
                    },100)
                },500*i)
        }
    }
    
}

function GameEndCallback(){
    let tictactoe = document.getElementById('tictactoe')
    let panel_container = document.getElementById('text_box_parent')
    let panel_text_container = document.getElementById('text_box_text')
    panel_container.hidden = false
    const tl = gsap.timeline({
            onComplete:(()=>{
                
            })
        });
        
    tl.to(
        tictactoe,
        {
            opacity:0,
            onComplete:()=>{
                tictactoe.hidden = true
            }
        }
    )
    .to(
            panel_text_container,
            {
                duration:3,
                text:'Nice try. I saw that coming before you even clicked.',
            }
        )

}

function MakeComputerTurn(){
    const bestSpot = minimax("X").index;
    board[bestSpot] = "X";
    player_turn = true;
    if (checkWin("X")){
        console.log("COMPUTER WON LMAOOO")
        ShowWinningAnimation()
        non_click_overlay.set(true)
    }
    else if (!board.includes('')){
        GameEndCallback()
        console.log('DRAW MFFF')
    }
}

function StartTicTacToe(){
        let tictactoe = document.getElementById('tictactoe')
        tictactoe.hidden = false;
        let tictactoe_container = document.getElementById('tictactoe_container') 

        const tl = gsap.timeline({
            onComplete: () => {}
        });
        tl.call(
            ()=>{
                StartJoiningTicTacToeAnimation()
            }
        )
        .from(
            tictactoe,
            {
                x:-3000,
                delay:0.5,
                duration:2,
                opacity:0
            }
        )
        
}


function StartJoiningTicTacToeAnimation(){
        let elements = ['00','01','02','10','11','12','20','21','22']
        let shuffled_elements = shuffle(elements)

        for (const element_id_index in shuffled_elements) {
            
                const element = document.getElementById(shuffled_elements[element_id_index]);
                const angle = Math.random() * 360;
                const velocity = 20 + Math.random() * 200;
                setTimeout(() => {
                    element.classList.replace('border-red-800','border-red-950')
                    setTimeout(() => {
                        element.classList.replace('border-red-950','border-red-600')  
                        non_click_overlay.set(false);
                        board[0] = "X"
                    },300)
                },4000)

                
                gsap.from(element, 
                {
                    physics2D: {
                        velocity: velocity,
                        angle: angle,
                        gravity: -20,
                    },
                    rotation:3600,
                    duration: 4,
                    opacity: 0,
                    scale:0,
                },
                
            );

            }
        
        
}

</script>

<div
    id="tictactoe"
    hidden
    class="flex h-screen text-white w-full overflow-hidden bg-black"
    style="background: radial-gradient(circle, rgba(0, 0, 0, 1) 60%, rgb(41, 0, 0) 99%)"
>
    <div
        class="flex gap-8 animate-pulse font-extrabold flex-col justify-center items-center h-full w-1/3"
    >
        <div class="font-press text-4xl">FireWall</div>
        <div class="font-press text-3xl">vs</div>
        <div class="font-press text-4xl">YOU</div>
    </div>
    <div class="flex items-center justify-center h-full w-3/4">
        <div
            id="tictactoe_container"
            class="grid grid-cols-3 grid-rows-3 h-2/3 w-1/2 rounded-xl"
        >
            {#each { length: 3 }, i}
                {#each { length: 3 }, j}
                    <button
                        disabled={board[i * 3 + j] !== ''}
                        aria-label={`Tic Tac Toe cell ${i},${j}`}
                        onclick={() => {
                            HandleTicTacToeInput(i,j);
                        }}
                        id={`${i}${j}`}
                        class="flex {board[i * 3 + j] !== ''
                            ? ''
                            
                            : 'hover:border-8 hover:border-green-700'} justify-center items-center border-4 {GetTicTacToeStyles(
                            `${i}${j}`,
                        )}
                        {(all_hidden && !winning_ids.includes(`${i}${j}`)) ? 'opacity-0' : ''}
                        border-red-800 font-extrabold text-9xl text-red-500"
                    >
                    {board[i * 3 + j]}
                    </button>
                {/each}
            {/each}
        </div>
    </div>
</div>
