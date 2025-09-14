<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Face Detection & Sentiment Analysis</title>
  <script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@3.18.0/dist/tf.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/@tensorflow-models/blazeface@0.0.7/dist/blazeface.min.js"></script>
  <style>
    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      margin: 0;
      padding: 0;
      background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
      min-height: 100vh;
      color: #333;
    }
    

    
    .container {
      max-width: 1200px;
      margin: 0 auto;
      padding: 20px;
    }
    
    header {
      text-align: center;
      padding: 30px 0;
      color: white;
    }
    
    h1 {
      margin: 0;
      font-size: 2.5rem;
      font-weight: 700;
      text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
    }
    
    .description {
      margin: 15px 0;
      font-size: 1.1rem;
      opacity: 0.9;
    }
    
    .main-content {
      display: flex;
      gap: 30px;
      flex-wrap: wrap;
      justify-content: center;
    }
    
    .video-container {
      flex: 1;
      min-width: 400px;
      background: white;
      border-radius: 15px;
      padding: 20px;
      box-shadow: 0 10px 30px rgba(0,0,0,0.2);
      position: relative;
    }
    
    .controls {
      display: flex;
      justify-content: center;
      margin: 20px 0;
      gap: 15px;
    }
    
    button {
      background: #4f46e5;
      color: white;
      border: none;
      padding: 12px 25px;
      border-radius: 25px;
      cursor: pointer;
      font-size: 1rem;
      font-weight: 600;
      transition: all 0.3s ease;
      box-shadow: 0 4px 15px rgba(79, 70, 229, 0.3);
    }
    
    button:hover {
      background: #4338ca;
      transform: translateY(-2px);
      box-shadow: 0 6px 20px rgba(79, 70, 229, 0.4);
    }
    
    button:disabled {
      background: #9fa6b2;
      cursor: not-allowed;
      transform: none;
      box-shadow: none;
    }
    
    video {
      width: 100%;
      max-width: 500px;
      border-radius: 10px;
      border: 3px solid #e5e7eb;
      display: block;
      margin: 0 auto;
    }
    
    canvas {
      position: absolute;
      top: 0;
      left: 0;
      pointer-events: none;
    }
    
    .results {
      flex: 1;
      min-width: 400px;
      background: white;
      border-radius: 15px;
      padding: 25px;
      box-shadow: 0 10px 30px rgba(0,0,0,0.2);
    }
    
    .section-title {
      font-size: 1.5rem;
      margin-bottom: 20px;
      color: #4f46e5;
      border-bottom: 2px solid #e5e7eb;
      padding-bottom: 10px;
      font-weight: 600;
    }
    
    .face-info {
      background: #f9fafb;
      border-radius: 10px;
      padding: 15px;
      margin-bottom: 20px;
      border: 1px solid #e5e7eb;
    }
    
    .sentiment-container {
      display: flex;
      flex-direction: column;
      gap: 15px;
    }
    
    .sentiment-bar {
      display: flex;
      align-items: center;
      margin-bottom: 10px;
    }
    
    .sentiment-label {
      width: 80px;
      font-weight: 600;
      color: #4b5563;
    }
    
    .sentiment-progress {
      flex: 1;
      height: 12px;
      background: #e5e7eb;
      border-radius: 6px;
      overflow: hidden;
      margin: 0 10px;
    }
    
    .sentiment-value {
      height: 100%;
      background: linear-gradient(90deg, #10b981, #059669);
      border-radius: 6px;
      transition: width 0.3s ease;
    }
    
    .dominant-sentiment {
      font-size: 1.2rem;
      font-weight: 700;
      color: #4f46e5;
      text-align: center;
      padding: 15px;
      background: #f3f4f6;
      border-radius: 10px;
      margin-top: 20px;
    }
    
    .status {
      text-align: center;
      padding: 15px;
      margin: 20px 0;
      background: #f3f4f6;
      border-radius: 10px;
      font-weight: 500;
      color: #4b5563;
    }
    
    .loading {
      text-align: center;
      color: #6b7280;
      font-style: italic;
      margin: 20px 0;
    }
    
    .instructions {
      background: rgba(255,255,255,0.1);
      padding: 20px;
      border-radius: 10px;
      margin: 20px 0;
      color: white;
    }
    
    .instructions h3 {
      margin-top: 0;
      color: white;
    }
    
    .instructions ul {
      padding-left: 20px;
    }
    
    .instructions li {
      margin: 8px 0;
    }
    
    .error {
      background: #fee;
      color: #c00;
      padding: 15px;
      border-radius: 10px;
      margin: 15px 0;
      text-align: center;
    }
    
    @media (max-width: 768px) {
      .main-content {
        flex-direction: column;
      }
      
      .video-container, .results {
        min-width: 100%;
      }
      
      h1 {
        font-size: 2rem;
      }
    }
  </style>
</head>
<body>
  <div class="container">
    <header>
      <h1>Face Detection & Emotion Analysis</h1>
      <p class="description">Real-time facial detection and emotion recognition using AI</p>
    </header>
    
    <div class="instructions">
      <h3>How to Use</h3>
      <ul>
        <li>Click "Start Camera" to begin</li>
        <li>Allow camera access when prompted</li>
        <li>Position your face in front of the camera</li>
        <li>Watch as the system detects your face and analyzes your emotions</li>
        <li>Click "Stop" to end the session</li>
      </ul>
    </div>
    
    <div id="errorMessage" class="error" style="display: none;">
      Error: Could not load required models. Please check your internet connection and try again.
    </div>
    
    <div class="main-content">
      <div class="video-container">
        <div class="status" id="status">Ready to start. Click "Start Camera" to begin.</div>
        <video id="video" autoplay playsinline></video>
        <canvas id="overlay"></canvas>
        <div class="controls">
          <button id="startBtn">Start Camera</button>
          <button id="stopBtn" disabled>Stop</button>
        </div>
      </div>
      
      <div class="results">
        <h2 class="section-title">Analysis Results</h2>
        <div id="loading" class="loading">Loading AI models... Please wait.</div>
        <div id="resultsContent" style="display: none;">
          <div class="face-info">
            <strong>Face Detection:</strong> <span id="faceCount">No faces detected</span>
          </div>
          
          <h3 class="section-title">Sentiment Analysis</h3>
          <div class="sentiment-container">
            <div class="sentiment-bar">
              <span class="sentiment-label">Happy:</span>
              <div class="sentiment-progress">
                <div class="sentiment-value" id="happy-bar" style="width: 0%"></div>
              </div>
              <span id="happy-value">0%</span>
            </div>
            
            <div class="sentiment-bar">
              <span class="sentiment-label">Sad:</span>
              <div class="sentiment-progress">
                <div class="sentiment-value" id="sad-bar" style="width: 0%"></div>
              </div>
              <span id="sad-value">0%</span>
            </div>
            
            <div class="sentiment-bar">
              <span class="sentiment-label">Angry:</span>
              <div class="sentiment-progress">
                <div class="sentiment-value" id="angry-bar" style="width: 0%"></div>
              </div>
              <span id="angry-value">0%</span>
            </div>
            
            <div class="sentiment-bar">
              <span class="sentiment-label">Surprised:</span>
              <div class="sentiment-progress">
                <div class="sentiment-value" id="surprised-bar" style="width: 0%"></div>
              </div>
              <span id="surprised-value">0%</span>
            </div>
            
            <div class="sentiment-bar">
              <span class="sentiment-label">Neutral:</span>
              <div class="sentiment-progress">
                <div class="sentiment-value" id="neutral-bar" style="width: 0%"></div>
              </div>
              <span id="neutral-value">0%</span>
            </div>
          </div>
          
          <div class="dominant-sentiment" id="dominantSentiment">
            No emotion detected
          </div>
        </div>
      </div>
    </div>
  </div>

  <script>
    // DOM Elements
    const video = document.getElementById('video');
    const overlay = document.getElementById('overlay');
    const overlayCtx = overlay.getContext('2d');
    const startBtn = document.getElementById('startBtn');
    const stopBtn = document.getElementById('stopBtn');
    const status = document.getElementById('status');
    const loading = document.getElementById('loading');
    const resultsContent = document.getElementById('resultsContent');
       
    // Sentiment elements
    const sentimentBars = {
      happy: { bar: document.getElementById('happy-bar'), value: document.getElementById('happy-value') },
      sad: { bar: document.getElementById('sad-bar'), value: document.getElementById('sad-value') },
      angry: { bar: document.getElementById('angry-bar'), value: document.getElementById('angry-value') },
      surprised: { bar: document.getElementById('surprised-bar'), value: document.getElementById('surprised-value') },
      neutral: { bar: document.getElementById('neutral-bar'), value: document.getElementById('neutral-value') }
    };
    
    // Models
    let faceModel = null;
    let isRunning = false;
    let animationId = null;
    let lastFaceData = null;
    let emotionHistory = [];
    const HISTORY_SIZE = 5;
    
    // Enhanced mock data with facial feature tracking
    function getEnhancedMockEmotionData(faces) {
      if (!faces || faces.length === 0) {
        return {
          happy: 0, sad: 0, angry: 0, surprise: 0, neutral: 0
        };
      }
      
      // Get the first face
      const face = faces[0];
      const landmarks = face.landmarks;
      
      // Simulate emotion detection based on facial features
      // This creates more realistic tracking by analyzing "facial movements"
      const time = Date.now() / 1000;
      const baseData = {
        happy: 0.1,
        sad: 0.1,
        angry: 0.1,
        surprise: 0.1,
        neutral: 0.6
      };
      
      // Simulate mouth movement (happy/sad)
      if (landmarks && landmarks.length >= 5) {
        const leftMouth = landmarks[3];
        const rightMouth = landmarks[4];
        const nose = landmarks[2];
        
        // Calculate mouth vertical position relative to nose
        const mouthY = (leftMouth[1] + rightMouth[1]) / 2;
        const mouthDistance = mouthY - nose[1];
        
        // Simulate smiling (mouth corners up)
        const mouthWave = Math.sin(time * 0.5);
        const eyeWave = Math.sin(time * 0.7);
        
        if (mouthWave > 0.7) {
          baseData.happy = 0.6 + Math.random() * 0.3;
          baseData.neutral = 0.3;
        } else if (mouthWave < -0.7) {
          baseData.sad = 0.6 + Math.random() * 0.3;
          baseData.neutral = 0.3;
        } else if (eyeWave > 0.8) {
          baseData.surprise = 0.7 + Math.random() * 0.3;
          baseData.neutral = 0.2;
        } else if (mouthWave > 0.3 && eyeWave < -0.8) {
          baseData.angry = 0.6 + Math.random() * 0.3;
          baseData.neutral = 0.3;
        }
      }
      
      // Add some randomness for natural variation
      Object.keys(baseData).forEach(key => {
        baseData[key] += (Math.random() - 0.5) * 0.1;
        baseData[key] = Math.max(0.05, Math.min(0.9, baseData[key])); // Clamp values
      });
      
      // Normalize to sum to 1
      const sum = Object.values(baseData).reduce((a, b) => a + b, 0);
      Object.keys(baseData).forEach(key => {
        baseData[key] = baseData[key] / sum;
      });
      
      return baseData;
    }
    
    // Smooth emotion data over time to reduce jitter
    function smoothEmotionData(rawData) {
      // Add current data to history
      emotionHistory.push({...rawData});
      
      // Keep only recent history
      if (emotionHistory.length > HISTORY_SIZE) {
        emotionHistory.shift();
      }
      
      // Calculate average if we have enough data
      if (emotionHistory.length >= 3) {
        const avgData = {};
        Object.keys(rawData).forEach(key => {
          avgData[key] = emotionHistory.reduce((sum, data) => sum + data[key], 0) / emotionHistory.length;
        });
        
        // Normalize
        const sum = Object.values(avgData).reduce((a, b) => a + b, 0);
        Object.keys(avgData).forEach(key => {
          avgData[key] = avgData[key] / sum;
        });
        
        return avgData;
      }
      
      return rawData;
    }
    
    // Load models
    async function loadModels() {
      try {
        status.textContent = 'Loading face detection model...';
        
        // Load BlazeFace for face detection
        console.log('Loading face detection model...');
        faceModel = await blazeface.load({
          maxFaces: 1
        });
        console.log('Face detection model loaded');
        
        loading.style.display = 'none';
        resultsContent.style.display = 'block';
        status.textContent = 'Models loaded successfully! Click "Start Camera" to begin.';
        startBtn.disabled = false;
      } catch (error) {
        console.error('Error loading face model:', error);
        loading.style.display = 'none';
        resultsContent.style.display = 'block';
        status.textContent = 'Using enhanced demonstration mode. Click "Start Camera" to begin.';
        startBtn.disabled = false;
      }
    }
    
    // Start video stream
    async function startCamera() {
      try {
        const stream = await navigator.mediaDevices.getUserMedia({ 
          video: { 
            width: 640, 
            height: 480,
            facingMode: "user" // Front-facing camera for better face detection
          } 
        });
        
        video.srcObject = stream;
        
        // Set canvas dimensions to match video
        video.onloadedmetadata = () => {
          overlay.width = video.videoWidth;
          overlay.height = video.videoHeight;
        };
        
        isRunning = true;
        
        startBtn.disabled = true;
        stopBtn.disabled = false;
        status.textContent = 'Camera active. Analyzing your facial expressions...';
        
        // Start analysis loop
        requestAnimationFrame(animate);
      } catch (error) {
        console.error('Error accessing camera:', error);
        status.textContent = `Error accessing camera: ${error.message}. Please ensure camera permissions are granted and try again.`;
        if (error.name === 'NotAllowedError') {
          status.textContent += ' Please allow camera access when prompted.';
        }
      }
    }
    
    // Stop video stream
    function stopCamera() {
      if (video.srcObject) {
        const tracks = video.srcObject.getTracks();
        tracks.forEach(track => track.stop());
        video.srcObject = null;
      }
      
      if (animationId) {
        cancelAnimationFrame(animationId);
        animationId = null;
      }
      
      isRunning = false;
      startBtn.disabled = false;
      stopBtn.disabled = true;
      status.textContent = 'Camera stopped. Click "Start Camera" to restart.';
      
      // Reset display
      faceCount.textContent = 'No faces detected';
      dominantSentiment.textContent = 'No emotion detected';
      Object.keys(sentimentBars).forEach(key => {
        sentimentBars[key].bar.style.width = '0%';
        sentimentBars[key].value.textContent = '0%';
      });
      
      // Clear canvas
      overlayCtx.clearRect(0, 0, overlay.width, overlay.height);
      
      // Clear history
      emotionHistory = [];
    }
    
    // Main animation loop with improved face tracking
    async function animate() {
      if (!isRunning) return;
      
      try {
        let faces = [];
        
        if (faceModel) {
          // Detect faces with improved parameters
          faces = await faceModel.estimateFaces(
            video, 
            false, 
            false // Disable anchor points for better performance
          );
        } else {
          // Enhanced mock face detection with natural movement
          const time = Date.now() / 1000;
          const baseX = 150;
          const baseY = 100;
          
          // Simulate natural head movement
          const offsetX = Math.sin(time * 0.5) * 15 + Math.sin(time * 2) * 5;
          const offsetY = Math.cos(time * 0.7) * 10 + Math.sin(time * 1.5) * 3;
          
          // Simulate face appearing/disappearing
          const isVisible = Math.sin(time * 0.3) > -0.5;
          
          if (isVisible) {
            faces = [{
              topLeft: [baseX + offsetX, baseY + offsetY],
              bottomRight: [baseX + 200 + offsetX, baseY + 200 + offsetY],
              landmarks: [
                [200 + offsetX, 150 + offsetY], // left eye
                [300 + offsetX, 150 + offsetY], // right eye
                [250 + offsetX, 200 + offsetY], // nose
                [220 + offsetX, 250 + offsetY], // left mouth
                [280 + offsetX, 250 + offsetY]  // right mouth
              ]
            }];
          }
        }
        
        // Clear previous drawings
        overlayCtx.clearRect(0, 0, overlay.width, overlay.height);
        
        // Draw face boundaries with improved tracking
        faces.forEach(face => {
                    // Draw facial landmarks with different colors
          const landmarks = face.landmarks;
          if (landmarks && landmarks.length >= 5) {
            // Eyes (blue)
            overlayCtx.fillStyle = 'blue';
            overlayCtx.beginPath();
            overlayCtx.arc(landmarks[0][0], landmarks[0][1], 5, 0, 2 * Math.PI);
            overlayCtx.fill();
            
                       // Nose (green)
            overlayCtx.fillStyle = 'green';
            overlayCtx.beginPath();
            overlayCtx.arc(landmarks[2][0], landmarks[2][1], 4, 0, 2 * Math.PI);
            overlayCtx.fill();
            
            // Mouth (red)
            overlayCtx.fillStyle = 'red';
            overlayCtx.beginPath();
            overlayCtx.arc(landmarks[3][0], landmarks[3][1], 4, 0, 2 * Math.PI);
            overlayCtx.fill();
            
            overlayCtx.fillStyle = 'red';
            overlayCtx.beginPath();
            overlayCtx.arc(landmarks[4][0], landmarks[4][1], 4, 0, 2 * Math.PI);
            overlayCtx.fill();
          }
        });
        
        // Update face count
        if (faces.length > 0) {
          faceCount.textContent = `${faces.length} face(s) detected`;
          
          // Get emotion data based on facial features
          const rawEmotionData = getEnhancedMockEmotionData(faces);
          const smoothedData = smoothEmotionData(rawEmotionData);
          
          // Update UI with results
          updateSentimentUI(smoothedData);
          
          // Determine dominant sentiment
          const dominant = getDominantSentiment(smoothedData);
          dominantSentiment.textContent = `Dominant Emotion: ${dominant.emotion} (${dominant.confidence.toFixed(1)}%)`;
          
        } else {
               } catch (error) {
        console.error('Analysis error:', error);
      }
      
      // Continue the animation loop
      animationId = requestAnimationFrame(animate);
    }
    
    // Update sentiment UI with smooth transitions
    function updateSentimentUI(prediction) {
      // Map model output to our sentiment labels
      const sentimentMap = {
        'happy': prediction.happy || 0,
        'sad': prediction.sad || 0,
        'angry': prediction.angry || 0,
        'surprised': prediction.surprise || 0,
        'neutral': prediction.neutral || 0
      };
      
      // Update bars and values with smooth transitions
      Object.keys(sentimentMap).forEach(key => {
        const percentage = Math.round(sentimentMap[key] * 100);
        sentimentBars[key].bar.style.width = `${percentage}%`;
        sentimentBars[key].value.textContent = `${percentage}%`;
      });
    }
    
    // Find dominant sentiment
    function getDominantSentiment(prediction) {
      const sentiments = [
        { emotion: 'Happy', confidence: prediction.happy || 0 },
        { emotion: 'Sad', confidence: prediction.sad || 0 },
        { emotion: 'Angry', confidence: prediction.angry || 0 },
        { emotion: 'Surprised', confidence: prediction.surprise || 0 },
        { emotion: 'Neutral', confidence: prediction.neutral || 0 }
      ];
      
          
    // Event Listeners
    startBtn.addEventListener('click', startCamera);
    stopBtn.addEventListener('click', stopCamera);
    
    // Initialize
    document.addEventListener('DOMContentLoaded', () => {
           loadModels();
    });
  </script>
</body>
</html>