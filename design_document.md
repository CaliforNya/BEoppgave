# App Features

A simple number guessing app using the MVC design pattern.

What Will the Game Do?

- Separate game logic from user interaction and display.
- Provide feedback based on user guesses:
  - Too low
  - Too high
  - Correct guess with the number of attempts shown

1. How It Works:

   - A random number (1-100) is generated each game.
   - User guesses until the correct number is found.

2. Data Handling:

   - No need for a database—game data is stored temporarily in memory.

3. User Interface:
   - Simple input for guesses and feedback display.
   - Responsive and user-friendly design for both desktop and mobile devices.

## Game Elements:

1. Input Field:

   - Allows users to enter their guess.

2. Submit Button:

   - Sends the user’s guess to the backend for processing.

3. Feedback Message:

   - Displays whether the guess is too high, too low, or correct.

4. Attempt Counter:

   - Shows the number of guesses made by the user.

5. Reset Button:
   - Restarts the game with a new random number.

### Backend:

- JavaScript with Node.js and Express.
- No database—temporary data storage only

#### Frontend:

- HTML for structure.
- CSS for layout and design.
- JavaScript for handling user interactions and dynamically updating the interface.
- A simple form for user input and a section to display feedback messages.
