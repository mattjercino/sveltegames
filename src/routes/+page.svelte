<script>
    import { onMount, onDestroy, tick } from 'svelte';

    const keyboardLayout = [
        ["`\n~", "1\n!", "2\n@", "3\n#", "4\n$", "5\n%", "6\n^", "7\n&", "8\n*", "9\n(", "0\n)", "-\n_", "=\n+", { w: 2, label: "Backspace", code: "Backspace" }],
        [{ w: 1.5, label: "Tab", code: "Tab" }, "Q", "W", "E", "R", "T", "Y", "U", "I", "O", "P", "[\n{", "]\n}", { w: 1.5, label: "\\\n|", code: "Backslash" }],
        [{ w: 1.75, label: "Caps Lock", code: "CapsLock" }, "A", "S", "D", "F", "G", "H", "J", "K", "L", ";\n:", "'\n\"", { w: 2.25, label: "Enter", code: "Enter" }],
        [{ w: 2.25, label: "Shift", code: "ShiftLeft" }, "Z", "X", "C", "V", "B", "N", "M", ",\n<", ".\n>", "/\n?", { w: 2.75, label: "Shift", code: "ShiftRight" }],
        [{ w: 1.25, label: "Ctrl", code: "ControlLeft" }, { w: 1.25, label: "Win", code: "MetaLeft" }, { w: 1.25, label: "Alt", code: "AltLeft" }, { w: 6.25, label: "Space", code: "Space" }, { w: 1.25, label: "Alt", code: "AltRight" }, { w: 1.25, label: "Win", code: "MetaRight" }, { w: 1.25, label: "Menu", code: "ContextMenu" }, { w: 1.25, label: "Ctrl", code: "ControlRight" }]
    ];

    const defaultKeyWidth = 60; // default width for a single unit key in pixels

    let points = 0;
    let promptKey = null;
    let incorrectKey = null;
    let pressedKey = null;
    let countdown = 60;
    let intervalId = null;


    function getRandomKey() {
        // Flatten the keyboard layout and filter only alphabetic (uppercase) and numeric keys
        const keys = keyboardLayout.flat().filter(key => {
            if (typeof key === 'object') {
                return false; // Exclude special keys
            } else {
                // Split key labels by newline and take the first part (non-shifted key)
                const keyLabel = key.split('\n')[0];
                return /^[A-Z0-9]$/.test(keyLabel);
            }
        });
        const randomKey = keys[Math.floor(Math.random() * keys.length)];
        return randomKey;
    }

    function promptRandomKey() {
        const key = getRandomKey();
        promptKey = typeof key === 'object' ? key.code : key.split('\n')[0];
    }

    async function handleKeyDown(event) {
        pressedKey = event.key.length == 1 && event.code != "Space" ? event.key.toUpperCase() : event.code
        if (event.key.toLowerCase() === promptKey.toLowerCase() || event.code === promptKey) {
            points += 1;
            promptRandomKey();
        } else
        {
            incorrectKey = event.key.length == 1 && event.code != "Space" ? event.key.toUpperCase() : event.code
            window.removeEventListener('keydown', handleKeyDown);
            await tick();
            setTimeout(() => {window.addEventListener('keydown', handleKeyDown); incorrectKey = null}, 1000)
        }
    }

    function startCountdown() {
        intervalId = setInterval(() => {
            if (countdown > 0) {
                countdown -= 1;
            } else {
                clearInterval(intervalId);
                window.removeEventListener('keydown', handleKeyDown);
            }
        }, 1000);
    }

    onMount(() => {
        promptRandomKey();
        if (typeof window !== 'undefined') {
            window.addEventListener('keydown', handleKeyDown);
            startCountdown()
        }
    });

    onDestroy(() => {
        if (typeof window !== 'undefined') {
            window.removeEventListener('keydown', handleKeyDown);
        }
    });
</script>

<div class="keyboard">
    {#each keyboardLayout as row}
        <div class="row">
            {#each row as key}
                <div
                    class="key {promptKey === (typeof key === 'object' ? key.code : key.split('\n')[0]) ? 'active' : ''}
                    {incorrectKey === (typeof key === 'object' ? key.code : key.split('\n')[0]) ? 'incorrect' : ''}
                    {pressedKey === (typeof key === 'object' ? key.code : key.split('\n')[0]) ? 'pressed' : ''}"
                    style="--key-width: {defaultKeyWidth}px; width: calc(var(--key-width) * {typeof key === 'object' && key.w ? key.w : 1}); height: var(--key-width);"
                >
                    <div class="key-content">
                        {#each (typeof key === 'object' ? key.label : key).split('\n') as line}
                            <span>{line}</span>
                        {/each}
                    </div>
                </div>
            {/each}
        </div>
    {/each}
</div>
<div style="color: white">
    <p>Points: {points}</p>
    <p>Press the {promptKey} key!</p>
    <p>Time left: {countdown}s</p>
</div>

<style>
    .keyboard {
        display: flex;
        flex-direction: column;
        gap: 5px;
    }
    .row {
        display: flex;
        gap: 5px;
        width: 900px;
    }
    .key {
        display: flex;
        align-items: center;
        justify-content: center;
        border: 1px solid #ccc;
        border-radius: 4px;
        padding: 10px;
        background-color: #f9f9f9;
        font-size: 14px;
        color: #000000;
        text-align: center;
        box-sizing: border-box;
        flex-grow: 1;
        transition: background-color 0.1s;
    }
    .key.active {
        background-color: blue;
        color: white;
    }
    .key.incorrect {
        background-color: red;
        color: white;
    }
    .key.pressed {
        animation: pressAnimation 0.2s ease-out;
    }
    .key-content {
        display: flex;
        flex-direction: column;
        white-space: nowrap;
    }
    @keyframes pressAnimation {
        0% {
            transform: scale(1);
        }
        50% {
            transform: scale(0.90);
        }
        100% {
            transform: scale(1);
        }
    }
</style>