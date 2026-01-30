<style>
  /* --- WRAPPER --- */
  .bujified-wrapper {
    display: flex;
    justify-content: center;
    font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
    width: 100%;
    box-sizing: border-box;
    padding: 20px 0;
  }

  /* --- WIDGET BOX --- */
  .bujified-widget {
    width: 100%;
    max-width: 600px;
    min-width: 320px;
    background: #ffffff;
    padding: 35px;
    border-radius: 15px;
    box-shadow: 0 10px 40px rgba(0,0,0,0.08);
    border: 1px solid #f0f0f0;
    border-top: 6px solid #32CD32; /* Bujified Green */
    box-sizing: border-box;
    position: relative;
  }

  .widget-title {
    text-align: center; color: #1a1a1a; margin-bottom: 25px;
    font-size: 22px; font-weight: 800; text-transform: uppercase; letter-spacing: 1px;
  }

  /* --- INPUTS --- */
  input[type="date"] { 
    width: 100%; padding: 12px; border: 1px solid #ddd; border-radius: 6px; 
    font-size: 16px; text-align: center; box-sizing: border-box; 
  }

  /* --- BUTTONS --- */
  .bujified-btn {
    width: 100%; padding: 16px; background-color: #1a1a1a; color: white; border: none;
    border-radius: 8px; font-size: 16px; font-weight: bold; cursor: pointer;
    transition: background 0.3s; text-transform: uppercase; margin-top: 25px;
  }
  .bujified-btn:hover { background-color: #32CD32; }

  /* --- RESULTS AREA --- */
  .bujified-result-box {
    margin-top: 25px; padding: 20px; background-color: #f9f9f9;
    border-radius: 8px; display: none; text-align: center; border: 1px solid #eee;
  }

  /* --- TIMELINE STATUS STYLES --- */
  .status-icon { font-size: 32px; display: block; margin-bottom: 10px; }
  .status-text { font-weight: 800; font-size: 20px; display: block; margin-bottom: 5px; }
  .status-detail { font-size: 14px; color: #555; line-height: 1.5; margin-bottom: 15px; display:block; }
  
  .safe { background-color: #e8f5e9; border-color: #32CD32; color: #2e7d32; }
  .rush { background-color: #fff3e0; border-color: #ff9800; color: #ef6c00; }
  .late { background-color: #ffebee; border-color: #f44336; color: #c62828; }
  .holiday-block { background-color: #ffebee; border-color: #c62828; color: #b71c1c; border: 2px solid #c62828; }
  .holiday-crunch { background-color: #fffde7; border-color: #fbc02d; color: #f57f17; border: 2px solid #fbc02d; }

  .deadline-box { background: rgba(255, 255, 255, 0.6); border: 1px dashed #666; padding: 10px; border-radius: 6px; margin-top: 10px; }
  .deadline-label { font-size: 11px; text-transform: uppercase; font-weight: bold; color: #333; display: block; }
  .deadline-date { font-size: 18px; font-weight: 800; color: #1a1a1a; }
  
  /* --- OPTIONS (Text or Buttons) --- */
  .option-container { margin-top: 15px; padding-top: 15px; border-top: 1px solid rgba(0,0,0,0.1); display: none; }
  
  /* Style for the confirmation text */
  .confirmation-text {
    background-color: #e8f5e9;
    color: #2e7d32;
    padding: 10px;
    border-radius: 6px;
    font-weight: bold;
    font-size: 14px;
    border: 1px solid #c8e6c9;
  }

  .dtf-btn {
    display: block; width: 100%; background-color: #ff4081; color: white; text-decoration: none;
    padding: 14px; border-radius: 6px; font-weight: bold; text-transform: uppercase; font-size: 14px; text-align: center;
  }
</style>

<div class="bujified-wrapper">
  <div class="bujified-widget">
    <div class="widget-title">Check Deadlines</div>
    <p style="font-size:14px; color:#666; margin-bottom:10px; text-align:center;">Select your Event Date:</p>
    
    <input type="date" id="bujifiedEventDate">
    
    <button class="bujified-btn" type="button" onclick="window.checkTimeline()">Check Timeline</button>

    <div id="timeResult" class="bujified-result-box">
      <span id="statusIcon" class="status-icon"></span>
      <span id="statusText" class="status-text"></span>
      <span id="statusDetail" class="status-detail"></span>
      
      <div class="deadline-box" id="intakeBox">
        <span class="deadline-label">Submit Intake Form by:</span>
        <span id="deadlineDisplay" class="deadline-date"></span>
      </div>

      <div class="deadline-box" id="paymentBox">
        <span class="deadline-label">Final Payment & Qty by:</span>
        <span id="paymentDeadlineDisplay" class="deadline-date"></span>
      </div>

      <div id="intakeOption" class="option-container">
        <div class="confirmation-text">
          ✅ Timeline Approved.<br>
          Please confirm below by entering this date in the Event Date field.
        </div>
      </div>

      <div id="dtfOption" class="option-container">
        <p style="font-size:12px; color:#666; margin-bottom:5px;">Need it faster? Try our DTF Program:</p>
        <a href="https://www.bujified.com/dtf" class="dtf-btn">Shop Quick-Print (DTF) &rarr;</a>
      </div>
    </div>
  </div>
</div>

<script>
  (function() {
    window.checkTimeline = function() {
      // 1. Get Elements
      var dateInput = document.getElementById('bujifiedEventDate');
      var resultBox = document.getElementById('timeResult');
      var icon = document.getElementById('statusIcon');
      var text = document.getElementById('statusText');
      var detail = document.getElementById('statusDetail');
      
      var deadlineDisp = document.getElementById('deadlineDisplay');
      var paymentDisp = document.getElementById('paymentDeadlineDisplay');
      
      var dtfContainer = document.getElementById('dtfOption');
      var intakeContainer = document.getElementById('intakeOption');
      var intakeBox = document.getElementById('intakeBox');
      var paymentBox = document.getElementById('paymentBox');

      // 2. Validate Input
      var inputVal = dateInput.value;
      if (!inputVal) { alert("Please ensure the date is selected."); return; }

      // 3. Set Dates
      var eventDate = new Date(inputVal);
      var today = new Date();
      var idealDate = new Date(eventDate);
      idealDate.setDate(eventDate.getDate() - 28); // 4 Weeks
      var paymentDate = new Date(eventDate);
      paymentDate.setDate(eventDate.getDate() - 21); // 3 Weeks
      
      var options = { month: 'short', day: 'numeric', year: 'numeric' };
      
      // 4. Hardcoded Holiday Logic (2026 Spring Festival)
      var sfCutoff = new Date('2026-02-07T23:59:59'); 
      var sfEarliestDelivery = new Date('2026-03-10T00:00:00');
      var warningZoneStart = new Date('2026-01-30T00:00:00'); 
      var warningZoneEnd = new Date('2026-02-07T23:59:59');

      // Reset Visibility
      dtfContainer.style.display = "none";
      intakeContainer.style.display = "none";
      intakeBox.style.display = "block";
      paymentBox.style.display = "block";

      // A. HOLIDAY BLOCK (Impossible Date)
      if (today > sfCutoff && eventDate < sfEarliestDelivery) {
          resultBox.style.display = "block";
          resultBox.className = "bujified-result-box holiday-block";
          icon.innerHTML = "🛑";
          text.innerHTML = "TOO LATE";
          detail.innerHTML = "Factory holiday delays prevent Sublimation until March 10.";
          deadlineDisp.innerHTML = "Cutoff Passed";
          paymentDisp.innerHTML = "Cutoff Passed";
          dtfContainer.style.display = "block"; 
          return;
      }

      // B. HOLIDAY CRUNCH (Technically Safe but close)
      if (idealDate >= warningZoneStart && idealDate <= warningZoneEnd) {
          resultBox.style.display = "block";
          resultBox.className = "bujified-result-box holiday-crunch";
          icon.innerHTML = "⚠️";
          text.innerHTML = "HOLIDAY CRUNCH";
          detail.innerHTML = "Close to holiday cutoff. Order IMMEDIATELY.";
          deadlineDisp.innerHTML = "Jan 30 (Buffer)";
          paymentDisp.innerHTML = paymentDate.toLocaleDateString('en-US', options);
          intakeContainer.style.display = "block"; 
          return; 
      }

      // C. STANDARD LOGIC
      var diffTime = eventDate - today;
      var diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24));

      resultBox.style.display = "block";
      resultBox.className = "bujified-result-box"; 

      if (diffDays >= 28) {
        // Safe Zone
        resultBox.classList.add("safe");
        icon.innerHTML = "✅";
        text.innerHTML = "SAFE";
        detail.innerHTML = "Perfect timing for Sublimation.";
        deadlineDisp.innerHTML = idealDate.toLocaleDateString('en-US', options);
        paymentDisp.innerHTML = paymentDate.toLocaleDateString('en-US', options);
        intakeContainer.style.display = "block"; 
      } 
      else if (diffDays >= 14) {
        // Rush Zone
        resultBox.classList.add("rush");
        icon.innerHTML = "⚠️";
        text.innerHTML = "RUSH FEE";
        detail.innerHTML = "Late submission. Rush Fee applies.";
        deadlineDisp.innerHTML = "ASAP";
        paymentDisp.innerHTML = "ASAP";
        intakeContainer.style.display = "block"; 
      } 
      else {
        // Late Zone
        resultBox.classList.add("late");
        icon.innerHTML = "🛑";
        text.innerHTML = "TOO LATE";
        detail.innerHTML = "Cannot produce Custom Sublimation.";
        deadlineDisp.innerHTML = "Missed";
        paymentDisp.innerHTML = "Missed";
        dtfContainer.style.display = "block"; 
      }
    };
  })();
</script>
