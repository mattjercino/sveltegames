<script>
  import { onMount, onDestroy } from 'svelte';

  let scenarios = [
    { description: 'Copy the selected text.', correctShortcut: 'Ctrl + C', relatedQuestionIndex: 0 },
    { description: 'Paste the copied text.', correctShortcut: 'Ctrl + V', relatedQuestionIndex: 1 },
    { description: 'Cut the selected text.', correctShortcut: 'Ctrl + X', relatedQuestionIndex: 3 },
  ];

  let questions = [
    {
      question: 'What is the shortcut to copy text?',
      correctAnswer: 'Ctrl + C',
      options: ['Ctrl + C', 'Alt + F4', 'Ctrl + Z', 'Shift + Delete'],
    },
    {
      question: 'What is the shortcut to paste text?',
      correctAnswer: 'Ctrl + V',
      options: ['Ctrl + V', 'Ctrl + P', 'Ctrl + Alt + Delete', 'Shift + Insert'],
    },
    {
      question: 'What is the shortcut to undo an action?',
      correctAnswer: 'Ctrl + Z',
      options: ['Ctrl + Z', 'Ctrl + A', 'Ctrl + N', 'Alt + Enter'],
    },
    {
      question: 'What is the shortcut to cut text?',
      correctAnswer: 'Ctrl + X',
      options: ['Ctrl + X', 'Alt + Tab', 'Ctrl + Shift + N', 'Shift + Home'],
    },
    {
      question: 'What is the shortcut to select all?',
      correctAnswer: 'Ctrl + A',
      options: ['Ctrl + A', 'Ctrl + E', 'Alt + Print Screen', 'Ctrl + Shift + End'],
    },
  ];

  let totalScenariosToComplete = 10;
  let completedScenariosCount = 0;
  let currentScenario = null;
  let message = '';
  let mode = 'quiz';
  let currentQuestionIndex = 0;
  let score = 0;
  let selectedAnswer = '';
  let answeredQuestions = [];
  let keySequence = '';
  let returningToScenarios = false;

  function shuffleOptions(options) {
    for (let i = options.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      [options[i], options[j]] = [options[j], options[i]];
    }
    return options;
  }

  function submitAnswer() {
    if (selectedAnswer === questions[currentQuestionIndex].correctAnswer) {
      score += 1;
      answeredQuestions.push(currentQuestionIndex);
      message = 'Correct!';

      if (returningToScenarios) {
        mode = 'scenarios';
        returningToScenarios = false;
        nextScenario();
      } else if (answeredQuestions.length === questions.length) {
        alert(`Quiz completed! Your score is ${score} out of ${questions.length}`);
        mode = 'scenarios';
        nextScenario();
      } else {
        currentQuestionIndex = getRandomUnansweredQuestionIndex();
        selectedAnswer = '';
        shuffleCurrentQuestionOptions();
      }
    } else {
      message = 'Incorrect! Try another question.';
      if (returningToScenarios) {
        selectedAnswer = '';
        shuffleCurrentQuestionOptions();
      } else {
        currentQuestionIndex = getRandomUnansweredQuestionIndex();
        selectedAnswer = '';
        shuffleCurrentQuestionOptions();
      }
    }
  }

  function handleKeydown(event) {
    keySequence = '';
    if (event.ctrlKey) keySequence += 'Ctrl + ';
    if (event.altKey) keySequence += 'Alt + ';
    if (event.shiftKey) keySequence += 'Shift + ';
    keySequence += event.key.toUpperCase();

    if (!['CONTROL', 'ALT', 'SHIFT'].includes(event.key.toUpperCase())) {
      if (keySequence === currentScenario.correctShortcut) {
        message = 'Correct!';
        setTimeout(nextScenario, 1000);
      } else {
        message = 'Incorrect! Returning to the quiz...';
        setTimeout(() => {
          mode = 'quiz';
          currentQuestionIndex = currentScenario.relatedQuestionIndex;
          selectedAnswer = '';
          returningToScenarios = true;
          shuffleCurrentQuestionOptions();
        }, 1000);
      }
      keySequence = '';
    }
  }

  function shuffleCurrentQuestionOptions() {
    if (questions[currentQuestionIndex] && questions[currentQuestionIndex].options) {
      questions[currentQuestionIndex].options = shuffleOptions([...questions[currentQuestionIndex].options]);
    } else {
      console.error('Error: Unable to shuffle options. Question data is missing or invalid.');
    }
  }

  function nextScenario() {
    if (completedScenariosCount < totalScenariosToComplete) {
      currentScenario = getRandomScenario();
      completedScenariosCount++;
      message = '';
      keySequence = '';
    } else {
      message = 'Congratulations! You completed all scenarios!';
      if (typeof window !== 'undefined') {
        window.removeEventListener('keydown', handleKeydown);
      }
    }
  }

  function getRandomScenario() {
    const randomIndex = Math.floor(Math.random() * scenarios.length);
    return scenarios[randomIndex];
  }

  function getRandomUnansweredQuestionIndex() {
    const unansweredQuestions = questions.filter((_, index) => !answeredQuestions.includes(index));
    if (unansweredQuestions.length === 0) return -1;
    const randomIndex = Math.floor(Math.random() * unansweredQuestions.length);
    return questions.indexOf(unansweredQuestions[randomIndex]);
  }

  onMount(() => {
    if (typeof window !== 'undefined') {
      window.addEventListener('keydown', handleKeydown);
      shuffleCurrentQuestionOptions();
    }
  });

  onDestroy(() => {
    if (typeof window !== 'undefined') {
      window.removeEventListener('keydown', handleKeydown);
    }
  });
</script>


<main>
  <h1>Keyboard Shortcuts Game</h1>

  {#if mode === 'quiz'}
    <section>
      <h2>Quiz Mode</h2>
      {#if questions[currentQuestionIndex] && questions[currentQuestionIndex].options}
        <div>
          <h2>{questions[currentQuestionIndex].question}</h2>
          <ul>
            {#each questions[currentQuestionIndex].options as option}
              <li>
                <input type="radio" bind:group={selectedAnswer} value={option} />
                {option}
              </li>
            {/each}
          </ul>
          <button on:click={submitAnswer}>Submit Answer</button>
          <div class="message">{message}</div>
        </div>
      {:else}
        <p>Error: Unable to load quiz data. Please try again.</p>
      {/if}
    </section>
  {:else if mode === 'scenarios'}
    <section>
      <h2>Scenario Mode</h2>
      {#if currentScenario}
        <div>
          <h2>{currentScenario.description}</h2>
          <p>Press the correct shortcut!</p>
          <div class="message" style="color: {message === 'Correct!' ? 'green' : 'red'};">
            {message}
          </div>
        </div>
      {/if}
    </section>
  {/if}
</main>

<style>
  main {
    text-align: center;
    padding: 50px;
    font-family: Arial, sans-serif;
    color: #FFFFFF;
  }

  section {
    margin: 30px 0;
  }

  div {
    text-align: center;
    margin: 20px;
    font-family: Arial, sans-serif;
  }

  .message {
    font-size: 1.5em;
    margin-top: 20px;
  }

  ul {
    list-style-type: none;
    padding: 0;
  }

  li {
    margin: 10px 0;
  }

  button {
    margin-top: 20px;
    padding: 10px 20px;
    font-size: 1em;
  }
</style>