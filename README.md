// Get DOM elements
const envelope = document.getElementById('envelope');
const letterWrapper = document.getElementById('letterWrapper');
const teddyContainer = document.getElementById('teddyContainer');
const bgMusic = document.getElementById('bgMusic');

// Track if the envelope has been opened
let envelopeOpened = false;

// Event listener for envelope click
envelope.addEventListener('click', function() {
    if (!envelopeOpened) {
        openEnvelope();
        envelopeOpened = true;
    }
});

// Function to open envelope
function openEnvelope() {
    // Add opened class to envelope
    envelope.classList.add('opened');
    
    // Play background music with error handling
    playMusic();
    
    // Show letter after a delay
    setTimeout(() => {
        letterWrapper.classList.add('visible');
    }, 600);
    
    // Show teddy bear after another delay
    setTimeout(() => {
        teddyContainer.style.pointerEvents = 'auto';
    }, 1200);
}

// Function to play background music
function playMusic() {
    // Create a simple romantic melody using Web Audio API
    // This will work without external audio files
    const audioContext = new (window.AudioContext || window.webkitAudioContext)();
    
    // Create a soft romantic melody
    playValentinesMelody(audioContext);
}

// Function to play Valentine's melody using Web Audio API
function playValentinesMelody(audioContext) {
    const notes = [
        { freq: 261.63, duration: 0.5 },  // C4
        { freq: 329.63, duration: 0.5 },  // E4
        { freq: 392.00, duration: 0.5 },  // G4
        { freq: 523.25, duration: 0.5 },  // C5
        { freq: 392.00, duration: 0.5 },  // G4
        { freq: 329.63, duration: 0.5 },  // E4
        { freq: 261.63, duration: 0.5 },  // C4
        { freq: 293.66, duration: 0.5 },  // D4
        { freq: 329.63, duration: 0.5 },  // E4
        { freq: 392.00, duration: 1 },    // G4
        { freq: 440.00, duration: 0.5 },  // A4
        { freq: 392.00, duration: 0.5 },  // G4
        { freq: 329.63, duration: 1 },    // E4
    ];

    let currentTime = audioContext.currentTime;
    
    notes.forEach((note) => {
        playNote(audioContext, note.freq, currentTime, note.duration);
        currentTime += note.duration;
    });

    // Loop the melody
    setTimeout(() => {
        if (envelopeOpened) {
            playValentinesMelody(audioContext);
        }
    }, (currentTime - audioContext.currentTime) * 1000);
}

// Function to play a single note
function playNote(audioContext, frequency, startTime, duration) {
    // Create oscillator
    const oscillator = audioContext.createOscillator();
    
    // Create gain node for volume envelope
    const gainNode = audioContext.createGain();
    
    // Create lowpass filter for soft sound
    const filter = audioContext.createBiquadFilter();
    filter.type = 'lowpass';
    filter.frequency.value = 2000;
    
    // Connect nodes
    oscillator.connect(filter);
    filter.connect(gainNode);
    gainNode.connect(audioContext.destination);
    
    // Set oscillator properties
    oscillator.type = 'sine';
    oscillator.frequency.value = frequency;
    
    // Create smooth volume envelope (attack, sustain, release)
    gainNode.gain.setValueAtTime(0, startTime);
    gainNode.gain.linearRampToValueAtTime(0.3, startTime + 0.05);
    gainNode.gain.linearRampToValueAtTime(0.3, startTime + duration - 0.1);
    gainNode.gain.linearRampToValueAtTime(0, startTime + duration);
    
    // Start and stop oscillator
    oscillator.start(startTime);
    oscillator.stop(startTime + duration);
}

// Add keyboard support
document.addEventListener('keydown', function(event) {
    if (event.code === 'Space' && !envelopeOpened) {
        event.preventDefault();
        openEnvelope();
        envelopeOpened = true;
    }
});

// Add touch support for better mobile experience
envelope.addEventListener('touchstart', function(event) {
    event.preventDefault();
    if (!envelopeOpened) {
        openEnvelope();
        envelopeOpened = true;
    }
});

// Initialize: Make sure letter is initially hidden
letterWrapper.classList.remove('visible');
