<!DOCTYPE html>
<html>
<head>
  <title>Opossum Clicker</title>
  <style>
    /* CSS Styles */
    body {
      font-family: 'Arial', sans-serif;
      text-align: center;
      margin: 0;
      background: linear-gradient(135deg, #f0f8ff, #e6e6fa);
      color: #333;
    }
    h1 {
      background-color: #4b0082;
      color: white;
      padding: 20px;
      margin: 0;
      font-size: 2.5em;
      text-shadow: 2px 2px #483d8b;
    }
    .section {
      margin: 20px auto;
      max-width: 500px;
      padding: 15px;
      border: 2px solid #8a2be2;
      border-radius: 15px;
      background: rgba(255, 255, 255, 0.9);
      box-shadow: 2px 2px 10px rgba(0, 0, 0, 0.1);
    }
    .section h3 {
      color: #4b0082;
      font-size: 1.5em;
    }
    button {
      padding: 12px 25px;
      font-size: 1.2em;
      cursor: pointer;
      border: none;
      border-radius: 8px;
      background-color: #9370db;
      color: white;
      margin: 10px;
      box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.2);
      transition: background-color 0.3s ease, transform 0.2s ease;
    }
    button:hover {
      background-color: #6a5acd;
      transform: scale(1.05);
    }
    #colonyDisplay {
      display: flex;
      justify-content: center;
      flex-wrap: wrap;
      gap: 10px;
      padding: 10px;
    }
    .colonyArt {
      width: 50px;
      height: 50px;
    }
    #notification {
      position: fixed;
      top: 10px;
      left: 50%;
      transform: translateX(-50%);
      background-color: #9370db;
      color: white;
      padding: 10px 20px;
      border-radius: 5px;
      display: none;
      box-shadow: 2px 2px 10px rgba(0, 0, 0, 0.2);
      z-index: 9999;
      font-size: 1.2em;
    }
  </style>
</head>
<body>
  <h1>Opossum Clicker</h1>

  <!-- Counter Section -->
  <div id="counter" class="section">
    <p><strong>Opossums: 0</strong></p>
    <button id="clickButton">Click Me!</button>
  </div>

  <!-- Store Section -->
  <div id="store" class="section">
    <h3>Store</h3>
    <button id="upgrade">Buy Opossum Treats (Cost: 10 Opossums)</button>
    <button id="autoClicker">Buy Auto-Clicker (Cost: 50 Opossums)</button>
  </div>

  <!-- Achievements Section -->
  <div id="achievements" class="section">
    <h3>Achievements</h3>
    <p id="achievement1" class="hidden">🎉 Achievement Unlocked: First 10 Opossums!</p>
    <p id="achievement2" class="hidden">🏆 Achievement Unlocked: Opossum Overlord (100 Opossums)!</p>
  </div>

  <!-- Colony Section -->
  <div id="colony" class="section">
    <h3>Colony</h3>
    <div id="colonyDisplay">
      <!-- Pixel art will be dynamically added here -->
    </div>
  </div>

  <!-- Save and Load Section -->
  <div id="saveLoad" class="section">
    <h3>Save and Load Progress</h3>
    <button id="saveButton">Save Progress</button>
    <button id="loadButton">Load Progress</button>
  </div>

  <div id="notification"></div>

  <script>
    // JavaScript Code
    let count = 0; // Total opossums
    let points = 0; // Points for upgrades
    let treatMultiplier = 1; // Click multiplier
    let autoClickerCount = 0; // Number of auto-clickers
    let autoClickerInterval = null; // Interval for auto-clickers

    // DOM elements
    const counterElement = document.getElementById('counter');
    const clickButton = document.getElementById('clickButton');
    const upgradeButton = document.getElementById('upgrade');
    const autoClickerButton = document.getElementById('autoClicker');
    const achievement1 = document.getElementById('achievement1');
    const achievement2 = document.getElementById('achievement2');
    const colonyDisplay = document.getElementById('colonyDisplay');
    const notification = document.getElementById('notification');
    const saveButton = document.getElementById('saveButton');
    const loadButton = document.getElementById('loadButton');

    // Custom Notification Function
    function showNotification(message) {
      notification.textContent = message;
      notification.style.display = 'block';
      setTimeout(() => {
        notification.style.display = 'none';
      }, 2000);
    }

    // Add pixel art to the colony
    function addToColony(milestone) {
      const colonyArt = document.createElement('img');
      colonyArt.src = `images/colony${milestone}.png`; // Update with your file path
      colonyArt.alt = `Colony Milestone ${milestone}`;
      colonyArt.classList.add('colonyArt');
      colonyDisplay.appendChild(colonyArt);
    }

    // Update the game display
    function updateDisplay() {
      counterElement.querySelector('p strong').textContent = `Opossums: ${count}`;
    }

    // Check for achievements and colony milestones
    function checkAchievements() {
      if (count >= 10 && achievement1.classList.contains('hidden')) {
        achievement1.classList.remove('hidden');
        addToColony(1); // First milestone (10 opossums)
      }
      if (count >= 20 && document.querySelectorAll('.colonyArt').length < 2) {
        addToColony(2); // Second milestone (20 opossums)
      }
      if (count >= 100 && achievement2.classList.contains('hidden')) {
        achievement2.classList.remove('hidden');
        addToColony(10); // Major milestone (100 opossums)
      }
    }

    // Main button functionality
    clickButton.addEventListener('click', () => {
      count += treatMultiplier;
      points++;
      updateDisplay();
      checkAchievements();
    });

    // Store: Upgrade (Opossum Treats)
    upgradeButton.addEventListener('click', () => {
      if (count >= 10) {
        count -= 10;
        treatMultiplier++;
        showNotification("You bought Opossum Treats! Your clicks are now stronger.");
        updateDisplay();
      } else {
        showNotification("Not enough opossums to buy Opossum Treats!");
      }
    });

    // Store: Auto-Clicker
    autoClickerButton.addEventListener('click', () => {
      if (count >= 50) {
        count -= 50;
        autoClickerCount++;
        showNotification(`Auto-Clicker purchased! You now have ${autoClickerCount} Auto-Clickers.`);
        if (!autoClickerInterval) {
          autoClickerInterval = setInterval(() => {
            count += treatMultiplier * autoClickerCount;
            updateDisplay();
            checkAchievements();
          }, 1000);
        }
        updateDisplay();
      } else {
        showNotification("Not enough opossums to buy Auto-Clicker!");
      }
    });

    // Save game progress
    saveButton.addEventListener('click', () => {
      const gameData = {
        count: count,
        points: points,
        treatMultiplier: treatMultiplier,
        autoClickerCount: autoClickerCount,
        achievements: {
          first10: !achievement1.classList.contains('hidden'),
          overlord100: !achievement2.classList.contains('hidden'),
        },
        colony: colonyDisplay.innerHTML
      };
      localStorage.setItem('opossumClickerSave', JSON.stringify(gameData));
      showNotification("Game progress saved!");
    });

    // Load game progress
    loadButton.addEventListener('click', () => {
      const savedData = localStorage.getItem('opossumClickerSave');
      if (savedData) {
        const gameData = JSON.parse(savedData);
        count = gameData.count;
        points = gameData.points;
        treatMultiplier = gameData.treatMultiplier;
        autoClickerCount = gameData.autoClickerCount;
        if (gameData.achievements.first10
