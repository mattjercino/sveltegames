
# Svelte Games Project

This repository contains two Svelte-based games:

1. **Keyboard Trainer** - A game focused on practicing and improving key press speed.
2. **Quiz & Scenario Game** - A combined quiz and scenario game to test knowledge and practical application of keyboard shortcuts.

## Project Structure

- `/routes/+page.svelte`: Contains the **Keyboard Trainer** game.
- `/routes/quiz/+page.svelte`: Contains the **Quiz & Scenario** game.

## Getting Started

### Prerequisites

To run this project, you'll need to have the following installed on your machine:

- [Node.js](https://nodejs.org/) (v12 or higher)
- [npm](https://www.npmjs.com/) (usually installed with Node.js)

### Installation

1. **Clone the repository:**

   ```bash
   git clone https://github.com/mattjercino/sveltegames.git
   cd sveltegames
   ```

2. **Install the dependencies for the project:**

   ```bash
   npm install
   ```

### Running the Games

#### Keyboard Trainer

To run the **Keyboard Trainer** game, follow these steps:

1. **Navigate to the root directory:**

   ```bash
   cd sveltegames
   ```

2. **Run the development server:**

   ```bash
   npm run dev
   ```

### Build for Production

To build the apps for production, use the following command:

  From the root directory:

  ```bash
  npm run build
  ```

  The built files will be available in the `public` directory.

## Usage

### Keyboard Trainer

In this game, you will be prompted to press specific keys or combinations of keys on your keyboard. A visual representation of a keyboard will highlight the pressed keys, and you'll receive feedback on whether your response was correct. The game includes a countdown timer and a points system to track your progress.

### Quiz & Scenario Game

This game has two modes:

- **Quiz Mode:** Answer multiple-choice questions about keyboard shortcuts.
- **Scenario Mode:** Respond to on-screen prompts by pressing the correct keys on your keyboard.

The game switches between Quiz Mode and Scenario Mode based on your performance, and your score is tracked throughout.