# Wordle Game
This project is a simple implementation of the Wordle game using HTML, CSS, and JavaScript.

## HTML Structure

This section sets up the basic structure and layout of the Wordle game webpage. It includes a `**<div>**` container with an ID of game and a nested `**<div>**` with the class grid, which will be used to display the game’s interface. The layout and design are handled by the external CSS file (style.css), while the game’s logic and interactivity are managed by the external JavaScript file (index.js), linked as a module. The `**<head>**` section also ensures that the page is responsive and compatible across different browsers.

``` HTML
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width", initial-scale="1.0">
        <meta http-equiv = "X-UA-Compatible" content="IE=edge">
        <link rel="stylesheet" href="style.css">
        <title> Wordle Game</title>
    </head>

    <body>
        <div id="game">
            <div class="grid">
            </div>
        </div>
        
        <script src="index.js" type="module"></script>
    </body>

</html>
```

## CSS Structure

This CSS code defines the visual appearance of the Wordle game. It sets up a grid layout for the game board, where each box represents a letter in the player’s guesses. The code includes styles for different game states: an empty box, a correct letter in the correct position, and a correct letter in the wrong position. It also provides animations for the boxes when a guess is made. Custom color variables are used to easily manage the theme and ensure consistency across the design.

``` CSS
html,
body{
    background: var(--default);
}
:root {
    --default:#121213;
    --empty: #3a3a3c;
    --right: #538d4e;
    --wrong: #b59f3b;
}

#game{
    display: grid;
    place-items: center;
    width: 100%;
    height: 600px;
}

.grid{
    display: grid;
    grid-template-rows: repeat(6, auto);
    grid-template-columns: repeat(5, auto);
}

.box {
    width: 60px;
    height: 60px;
    border: 2px solid var(--empty);
    margin: 4px;
    color: white;
    text-transform: uppercase;
    display: grid;
    place-items: center;
    font-family: 'Times New Roman', Times, serif;
    font-size: 2.4rem;
}

.box.empty{
    background: var(--empty);
}
.box.right{
    background: var(--right);
}
.box.wrong{
    background: var(--wrong);
}

.flip {
    animation: squash 0.5s ease
}

@keyframes squash {
    0% {
        transform: scaleY(1);
    }
    50% {
        transform: scaleY(0);
    }
    100% {
        transform: scaleY(1);
    }
}
```

## JavaScript Functionality

This script powers the Wordle game by managing the game state, capturing user inputs, and updating the game interface. It starts by selecting a random secret word from a predefined dictionary and sets up a 6x5 grid where the player can enter their guesses.

### Key Features:
  ### 1.	Game State Management:
  This part of the code initializes the game state, including the secret word, a 6x5 grid for guesses, and variables to track the current row and column.
``` JS
 const state = {
      secret: dictionary[Math.floor(Math.random() * dictionary.length)],
      grid: Array(6).fill().map(() => Array(5).fill('')),
      currentRow: 0,
      currentCol: 0,
  }; 
```
### 2. Grid Drawing and Updates:

This section defines functions to create and update the grid dynamically based on the player’s guesses, ensuring the visual representation reflects the current state of the game.
```JS
function drawGrid(container) {
    const grid = document.createElement('div');
    grid.className = 'grid';

    for (let i = 0; i < 6; i++) {
        for (let j = 0; j < 5; j++) {
            drawBox(grid, i, j);
        }
    }
    container.appendChild(grid);
}

function updateGrid() {
    for (let i = 0; i < state.grid.length; i++) {
        for (let j = 0; j < state.grid[i].length; j++) {
            const box = document.getElementById(`box${i}${j}`);
            box.textContent = state.grid[i][j];
        }
    }
}
```

### 3. Keyboard Event Handling:

This feature listens for keyboard events, allowing players to input their guesses using the keyboard. It processes Enter, Backspace, and letter keys, facilitating an interactive gameplay experience.

```JS
function registerKeyboardEvents(){
    document.body.onkeydown = (e) => {
        const key = e.key;
        if (key === 'Enter') 
        {
            if(state.currentCol === 5) {
                const word = getCurrentWord();
                if (isWordValid(word)) {
                    revealWord(word);
                    state.currentRow++;
                    state.currentCol = 0;
                } 
                else{
                    alert('Not a valid word.');
                }
            }

        }

        if (key === 'Backspace') 
        {
            removeLetter();
        }

        if (isLetter(key)) 
        {
            addLetter(key);
        }

        updateGrid();
    };
}
```
### 4. Word Validation and Feedback:

The functions in this section check if the guessed word is valid and provide visual feedback based on the accuracy of the player’s guesses. This enhances the user experience by informing players about the correctness of their letters.
```JS
function isWordValid(word) {
    return dictionary.includes(word);
}

function revealWord(guess) {
    const row = state.currentRow;
    const animation_duration = 500;

    for (let i = 0; i < 5; i++) {
        const box = document.getElementById(`box${row}${i}`);
        const letter = box.textContent;

        setTimeout(() => {
            if (letter === state.secret[i]) {
                box.classList.add('right');
            } else if (state.secret.includes(letter)) {
                box.classList.add('wrong');
            } else {
                box.classList.add('empty');
            }
        }, ((i + 1) * animation_duration) / 2);

        box.classList.add('flip');
        box.style.animationDelay = `${(i * animation_duration) / 2}ms`;
    }
}
```

### 5. Win/Loss Condition:

This section evaluates the game’s outcome by checking if the player has guessed the word correctly or if they have run out of attempts. It provides alerts to inform the player of their success or failure.
```JS
const isWinner = state.secret === guess;
const isGameOver = state.currentRow === 5;

setTimeout(() => {
    if (isWinner) {
        alert('You win!');
    } else if (isGameOver) {
        alert(`You lose! The word was ${state.secret}`);
    }
}, 3 * animation_duration);
```

### 6. Animations:

This section introduces animations for visual feedback during gameplay, enhancing the interaction experience. The animation adds a fun dynamic element when revealing guesses.
```JS
function revealWord(guess) {
    // ...other code
    for (let i = 0; i < 5; i++) {
        box.classList.add('flip');
        box.style.animationDelay = `${(i * animation_duration) / 2}ms`;
    }
}

@keyframes squash {
    0% { transform: scaleY(1); }
    50% { transform: scaleY(0); }
    100% { transform: scaleY(1); }
}
```

## Final image
This image showcases the completed Wordle Game interface, illustrating the user experience and how players interact with the game. It features the grid layout for guesses, color indicators for letter correctness, and the overall design aesthetic that enhances gameplay. The visual elements demonstrate the functionality and style implemented throughout the project.

<img src="https://github.com/user-attachments/assets/f1f49e37-88c6-4d7d-99d1-d48029adc644" alt="Description of image" width="400" />
