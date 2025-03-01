<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Realistic Balance Simulation with Water Effect</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      text-align: center;
      margin: 0;
      padding: 0;
      background: linear-gradient(to bottom, #87CEEB, #FFD700);
    }
    canvas {
      border: 1px solid #ccc;
      display: block;
      margin: 20px auto;
      background: #f7f7f7;
    }
    .controls {
      display: flex;
      justify-content: center;
      margin: 10px 0;
    }
    .control-group {
      margin: 0 20px;
    }
    .control-group label {
      display: block;
    }
    .slider {
      width: 200px;
    }
  </style>
</head>
<body>
  <h1>Realistic Balance Simulation with Water Effect</h1>
  <canvas id="balanceCanvas" width="800" height="400"></canvas>

  <div class="controls">
    <div class="control-group">
      <label>Left Mass: <span id="leftMassValue">5</span> kg</label>
      <input type="range" id="leftMass" class="slider" min="1" max="50" value="5">
      <label>Left Distance: <span id="leftDistanceValue">5</span> m</label>
      <input type="range" id="leftDistance" class="slider" min="5" max="50" step="5" value="5">
    </div>
    <div class="control-group">
      <label>Right Mass: <span id="rightMassValue">5</span> kg</label>
      <input type="range" id="rightMass" class="slider" min="1" max="50" value="5">
      <label>Right Distance: <span id="rightDistanceValue">5</span> m</label>
      <input type="range" id="rightDistance" class="slider" min="5" max="50" step="5" value="5">
    </div>
  </div>

  <script>
    const canvas = document.getElementById('balanceCanvas');
    const ctx = canvas.getContext('2d');

    // Sliders and display elements
    const leftMassSlider = document.getElementById('leftMass');
    const leftDistanceSlider = document.getElementById('leftDistance');
    const rightMassSlider = document.getElementById('rightMass');
    const rightDistanceSlider = document.getElementById('rightDistance');

    const leftMassValue = document.getElementById('leftMassValue');
    const leftDistanceValue = document.getElementById('leftDistanceValue');
    const rightMassValue = document.getElementById('rightMassValue');
    const rightDistanceValue = document.getElementById('rightDistanceValue');

    // Initial values
    let leftMass = parseInt(leftMassSlider.value);
    let leftDistance = parseInt(leftDistanceSlider.value);
    let rightMass = parseInt(rightMassSlider.value);
    let rightDistance = parseInt(rightDistanceSlider.value);

    // Update values and re-draw on slider change
    const updateValues = () => {
      leftMass = parseInt(leftMassSlider.value);
      leftDistance = parseInt(leftDistanceSlider.value);
      rightMass = parseInt(rightMassSlider.value);
      rightDistance = parseInt(rightDistanceSlider.value);

      leftMassValue.textContent = leftMass;
      leftDistanceValue.textContent = leftDistance;
      rightMassValue.textContent = rightMass;
      rightDistanceValue.textContent = rightDistance;

      draw();
    };

    // Add event listeners to sliders
    leftMassSlider.addEventListener('input', updateValues);
    leftDistanceSlider.addEventListener('input', updateValues);
    rightMassSlider.addEventListener('input', updateValues);
    rightDistanceSlider.addEventListener('input', updateValues);

    // Function to draw water effect
    const drawWaterEffect = (x, y, width, height, mass) => {
      // Draw container
      ctx.fillStyle = '#ADD8E6';
      ctx.fillRect(x, y - height, width, height);

      // Add border to container
      ctx.strokeStyle = 'blue';
      ctx.lineWidth = 2;
      ctx.strokeRect(x, y - height, width, height);

      // Draw water waves
      ctx.fillStyle = 'rgba(0, 0, 255, 0.6)';
      const waterHeight = (mass / 50) * height;
      const waveCount = 4;
      ctx.beginPath();
      for (let i = 0; i <= waveCount; i++) {
        const waveX = x + (i / waveCount) * width;
        const waveY = y - waterHeight + Math.sin(i * Math.PI) * 5;
        ctx.lineTo(waveX, waveY);
      }
      ctx.lineTo(x + width, y - height);
      ctx.lineTo(x, y - height);
      ctx.closePath();
      ctx.fill();
    };

    // Drawing function
    const draw = () => {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      // Calculate torques and tilt angle
      const leftTorque = leftMass * leftDistance;
      const rightTorque = rightMass * rightDistance;
      const angle = (rightTorque - leftTorque) / Math.max(leftTorque + rightTorque, 1) * 10; // Scale tilt

      // Draw the pivot base
      ctx.fillStyle = 'black';
      ctx.fillRect(canvas.width / 2 - 10, canvas.height / 2 + 10, 20, 40);

      // Draw the balance beam
      ctx.save();
      ctx.translate(canvas.width / 2, canvas.height / 2);
      ctx.rotate(angle * Math.PI / 180);
      ctx.fillStyle = 'black';
      ctx.fillRect(-300, -5, 600, 10); // Beam

      // Draw the left container with water effect
      drawWaterEffect(-leftDistance * 5 - 15, 0, 30, 50, leftMass);

      // Draw the right container with water effect
      drawWaterEffect(rightDistance * 5 - 15, 0, 30, 50, rightMass);
      ctx.restore();

      // Draw the pivot point
      ctx.fillStyle = 'black';
      ctx.beginPath();
      ctx.arc(canvas.width / 2, canvas.height / 2, 10, 0, Math.PI * 2);
      ctx.fill();

      // Display balance status
      let balanceStatus = '';
      if (angle > 5) {
        balanceStatus = 'ขวาหนัก';
      } else if (angle < -5) {
        balanceStatus = 'ซ้ายหนัก';
      } else {
        balanceStatus = 'สมดุล';
      }

      ctx.fillStyle = 'green';
      ctx.font = '20px Arial';
      ctx.textAlign = 'center';
      ctx.fillText(balanceStatus, canvas.width / 2, 50);
    };

    // Initial draw
    draw();
  </script>
</body>
</html>
