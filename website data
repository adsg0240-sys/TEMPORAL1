// Global Configuration & Constants
const TOTAL_FRAMES = 240;
const IMAGES_DIR = 'public/frames/';
const FRAME_PREFIX = 'ezgif-frame-';
const FRAME_EXT = '.jpg';

// State Variables
const images = [];
let loadedCount = 0;
let targetProgress = 0;
let currentProgress = 0;
const LERP_EASE = 0.08; // Smoothness factor (lower = smoother/slower, higher = faster response)

// DOM Elements
const canvas = document.getElementById('animation-canvas');
const context = canvas.getContext('2d');
const preloader = document.getElementById('preloader');
const loaderBar = document.getElementById('loader-bar');
const loaderPercentage = document.getElementById('loader-percentage');
const header = document.querySelector('.header');
const scrollContainer = document.getElementById('scroll-section');

// Define narrative milestones (scroll progress ranges)
const narratives = [
    { el: document.getElementById('narrative-1'), start: 0.05, end: 0.25 },
    { el: document.getElementById('narrative-2'), start: 0.30, end: 0.50 },
    { el: document.getElementById('narrative-3'), start: 0.55, end: 0.75 },
    { el: document.getElementById('narrative-4'), start: 0.80, end: 0.96 }
];

// Initialize application
function init() {
    preloadFrames();
    setupEventListeners();
}

// Preload all 240 image frames
function preloadFrames() {
    for (let i = 1; i <= TOTAL_FRAMES; i++) {
        const img = new Image();
        // Pads numbers: 1 -> 001, 10 -> 010, 100 -> 100
        const frameNum = String(i).padStart(3, '0');
        img.src = `${IMAGES_DIR}${FRAME_PREFIX}${frameNum}${FRAME_EXT}`;

        img.onload = () => {
            loadedCount++;
            updatePreloaderProgress();
        };

        img.onerror = () => {
            console.error(`Failed to load frame: ${frameNum}`);
            loadedCount++; // Count as completed to prevent blocking loading screen
            updatePreloaderProgress();
        };

        images.push(img);
    }
}

// Update the luxury preloader progress bar and text
function updatePreloaderProgress() {
    const percentage = Math.min(100, Math.floor((loadedCount / TOTAL_FRAMES) * 100));
    loaderBar.style.width = `${percentage}%`;
    loaderPercentage.textContent = `${percentage}%`;

    if (loadedCount === TOTAL_FRAMES) {
        setTimeout(hidePreloader, 600); // Small delay for visual completion
    }
}

// Dismiss the preloader and trigger entrance animations
function hidePreloader() {
    preloader.style.opacity = '0';
    preloader.style.visibility = 'hidden';

    // Resize canvas initially and draw the first frame
    resizeCanvas();

    // Start main render loop
    requestAnimationFrame(renderLoop);
}

// Draw a specific frame to the canvas (with cover object-fit behavior)
function drawFrame(frameIndex) {
    const img = images[frameIndex];
    if (!img || !img.complete) return;

    // Clear previous drawing
    context.clearRect(0, 0, canvas.width, canvas.height);

    // Calculate aspect ratio for cover behavior
    const imgWidth = img.naturalWidth || img.width;
    const imgHeight = img.naturalHeight || img.height;

    const imgRatio = imgWidth / imgHeight;
    const canvasRatio = canvas.width / canvas.height;

    let drawWidth, drawHeight, drawX, drawY;

    if (imgRatio > canvasRatio) {
        // Image is wider than canvas
        drawHeight = canvas.height;
        drawWidth = canvas.height * imgRatio;
        drawX = (canvas.width - drawWidth) / 2;
        drawY = 0;
    } else {
        // Image is taller than canvas
        drawWidth = canvas.width;
        drawHeight = canvas.width / imgRatio;
        drawX = 0;
        drawY = (canvas.height - drawHeight) / 2;
    }

    context.drawImage(img, drawX, drawY, drawWidth, drawHeight);
}

// Main update and render loop (Runs at 60fps/monitor refresh rate)
function renderLoop() {
    // Interpolate progress smoothly towards scroll target
    currentProgress += (targetProgress - currentProgress) * LERP_EASE;

    // Map progress to a frame index (0 to 239)
    const frameIndex = Math.min(
        TOTAL_FRAMES - 1,
        Math.max(0, Math.floor(currentProgress * TOTAL_FRAMES))
    );

    // Draw the active frame
    drawFrame(frameIndex);

    // Update opacity & translations for overlays
    updateNarratives(currentProgress);

    // Continue loop
    requestAnimationFrame(renderLoop);
}

// Sync narration overlays to their corresponding ranges
function updateNarratives(progress) {
    narratives.forEach(item => {
        if (progress >= item.start && progress <= item.end) {
            item.el.classList.add('active');
        } else {
            item.el.classList.remove('active');
        }
    });
}

// Calculate normalized scroll progress inside the animation zone
function calculateScrollProgress() {
    const rect = scrollContainer.getBoundingClientRect();
    const viewportHeight = window.innerHeight;

    // The scrolling animation starts when the top of container meets viewport top
    const startScroll = rect.top + window.scrollY;
    // The scrolling animation ends when the bottom of container meets viewport bottom
    const endScroll = rect.bottom + window.scrollY - viewportHeight;

    const currentScroll = window.scrollY;

    // Protect from division by zero
    if (endScroll <= startScroll) return 0;

    const progress = (currentScroll - startScroll) / (endScroll - startScroll);
    return Math.min(1, Math.max(0, progress));
}

// Fit canvas resolution to window sizes considering device pixel ratios (DPR)
function resizeCanvas() {
    const dpr = window.devicePixelRatio || 1;
    canvas.width = window.innerWidth * dpr;
    canvas.height = window.innerHeight * dpr;

    // Scale drawings back to match styling coordinates
    context.setTransform(1, 0, 0, 1, 0, 0);

    // Redraw the current state frame immediately on resize
    const frameIndex = Math.min(
        TOTAL_FRAMES - 1,
        Math.max(0, Math.floor(currentProgress * TOTAL_FRAMES))
    );
    drawFrame(frameIndex);
}

// Setup listeners for scroll, resize, and custom UI components
function setupEventListeners() {
    // Listen to scroll events
    window.addEventListener('scroll', () => {
        targetProgress = calculateScrollProgress();

        // Shrink header nav on scroll down
        if (window.scrollY > 50) {
            header.classList.add('scrolled');
        } else {
            header.classList.remove('scrolled');
        }
    });

    // Resize listener
    window.addEventListener('resize', resizeCanvas);

    // Mobile nav toggle
    const navToggle = document.querySelector('.mobile-nav-toggle');
    const navLinks = document.querySelector('.nav-links');

    if (navToggle) {
        navToggle.addEventListener('click', () => {
            navToggle.classList.toggle('active');
            navLinks.classList.toggle('active');
        });
    }
}

// Start application
document.addEventListener('DOMContentLoaded', init);
