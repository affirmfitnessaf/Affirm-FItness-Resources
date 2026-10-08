# Affirm-Fitness-Resources
These resources accompany Section 4 Activities 9 and 10 of _Fitness for Every Body Companion Workbook_
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Affirm Fitness Resources</title>
<style>
*{box-sizing:border-box}
body{font-family:Arial,sans-serif;line-height:1.5;margin:0;background:#f5f5f5;color:#222}
header{background:#171717;color:white;text-align:center;padding:28px 15px}
header h1{margin:0}
main{max-width:720px;margin:24px auto;padding:0 15px}
section{background:white;border:1px solid #ddd;border-radius:12px;padding:22px;margin-bottom:18px}
h2{margin-top:0}
label{display:block;font-weight:bold;margin:15px 0 5px}
input,select{display:block;width:100%;padding:12px;font-size:16px;border:1px solid #888;border-radius:6px;background:white;color:#222}
button{width:100%;padding:15px;background:#171717;color:white;border:0;border-radius:7px;font-size:17px;margin-top:20px;cursor:pointer}
.result{background:#f2f2f2;border-radius:8px;padding:14px;margin:12px 0}
.number{font-size:25px;font-weight:bold}
.small{font-size:14px;color:#555}
.warning{background:#fff3dc;border-left:4px solid #a60;padding:12px}
.hidden{display:none}
</style>
</head>
<body>
<header>
<h1>AFFIRM FITNESS</h1>
<p>Fitness for Every Body</p>
</header>
<main>
<section>
<h2>Free Nutrition Calculator</h2>
<p>Math is hard, so let me do it for you!</p>
<p>Calculate your estimated Resting Metabolic Rate (RMR), Total Daily Energy Expenditure (TDEE), daily Calorie goal, and macronutrient ranges.</p>
</section>

<section>
<h2>1. About Your Body</h2>
<label for="units">Measurement system</label>
<select id="units" onchange="changeUnits()">
<option value="imperial">Pounds and inches</option>
<option value="metric">Kilograms and centimeters</option>
</select>
<label id="weightLabel" for="weight">Weight (lb)</label>
<input id="weight" type="number" min="1" step="any">
<label id="heightLabel" for="height">Height (inches)</label>
<input id="height" type="number" min="1" step="any">
<label for="age">Age (years)</label>
<input id="age" type="number" min="1" max="120">
<label for="hormones">Hormonal category</label>
<select id="hormones">
<option value="t">T-dominant body</option>
<option value="e">E-dominant body</option>
</select>
<p class="small">The original Mifflin-St Jeor equation uses male and female categories. T-dominant and E-dominant are practical approximations that have not been independently validated for people using gender-affirming hormone therapy.</p>
</section>

<section>
<h2>2. Activity Level</h2>
<label for="activity">Activity factor</label>
<select id="activity">
<option value="1.2">Sedentary (1.2)</option>
<option value="1.375">Lightly Active (1.375)</option>
<option value="1.55">Moderately Active (1.55)</option>
<option value="1.725">Very Active (1.725)</option>
<option value="1.9">Extremely Active (1.9)</option>
</select>
<p class="small">
Sedentary: little or no intentional exercise, seated most of the day.<br>
Lightly Active: light exercise 1-2 days/week.<br>
Moderately Active: moderate exercise 3-5 days/week.<br>
Very Active: intense exercise 6-7 days/week.<br>
Extremely Active: very hard exercise and physically demanding job.
</p>
</section>

<section>
<h2>3. Your Goal</h2>
<label for="goal">Weight goal</label>
<select id="goal">
<option value="rapidGain">Rapid Gain (+500 Calories)</option>
<option value="slowGain">Slow Gain (+250 Calories)</option>
<option value="maintain" selected>Maintain</option>
<option value="slowLoss">Slow Loss (-250 Calories)</option>
<option value="rapidLoss">Rapid Loss (-500 Calories)</option>
</select>
<button type="button" onclick="calculate()">Calculate My Goals</button>
<p id="error" role="alert" style="color:#a00000"></p>
</section>

<section id="results" class="hidden" aria-live="polite">
<h2>Your Results</h2>
<div class="result">
Resting Metabolic Rate (RMR)
<div class="number" id="rmrResult"></div>
</div>
<div class="result">
Total Daily Energy Expenditure (TDEE)
<div class="number" id="tdeeResult"></div>
</div>
<div class="result">
Daily Calorie Goal
<div class="number" id="calorieResult"></div>
</div>

<h2>Macronutrient Goals</h2>
<p>These are ranges based on your selected goal.</p>
<div class="result">
<strong>Protein</strong>
<div class="number" id="proteinResult"></div>
<div id="proteinCalories"></div>
</div>
<div class="result">
<strong>Fat</strong>
<div class="number" id="fatResult"></div>
<div id="fatCalories"></div>
</div>
<div class="result">
<strong>Carbohydrates</strong>
<div class="number" id="carbResult"></div>
<div id="carbCalories"></div>
</div>
<p class="small">Each range is calculated independently. The minimum or maximum of every macronutrient should not all be selected simultaneously.</p>
<div id="warning" class="warning hidden"></div>
</section>

<section>
<h2>About These Calculations</h2>
<p>These calculations follow Activities 9 and 10 of the <em>Fitness for Every Body Companion Workbook</em>.</p>
<p>RMR uses the Mifflin-St Jeor equation. TDEE is estimated by multiplying RMR by an activity factor. Protein targets are based on body weight, fat targets on daily Calories, and carbohydrate targets on the remaining Calories.</p>
<p class="small">This calculator provides general educational estimates, not individualized medical or nutritional advice. Consult your healthcare team regarding individual needs.</p>
</section>
</main>

<script>
const $ = id => document.getElementById(id);
const fmt = n => Math.round(n).toLocaleString("en-US");

function changeUnits(){
  const metric = $("units").value === "metric";
  $("weightLabel").textContent = metric ? "Weight (kg)" : "Weight (lb)";
  $("heightLabel").textContent = metric ? "Height (cm)" : "Height (inches)";
  $("weight").value = "";
  $("height").value = "";
  $("results").classList.add("hidden");
}

function calculate(){
  $("error").textContent = "";
  $("results").classList.add("hidden");

  let weight = Number($("weight").value);
  let height = Number($("height").value);
  let age = Number($("age").value);

  if(!$("weight").value || !$("height").value || !$("age").value ||
     !Number.isFinite(weight) || !Number.isFinite(height) ||
     !Number.isFinite(age) || weight<=0 || height<=0 ||
     age<1 || age>120){
    $("error").textContent = "Please enter a valid weight, height, and age.";
    return;
  }

  if($("units").value === "imperial"){
    weight = weight / 2.2;
    height = height * 2.54;
  }

  const constant = $("hormones").value === "t" ? 5 : -161;
  const rmr = 10*weight + 6.25*height - 5*age + constant;
  const tdee = rmr * Number($("activity").value);

  const goals = {
    rapidGain: {adjust:500, pMin:1.6, pMax:2.0, fMin:.2, fMax:.3},
    slowGain: {adjust:250, pMin:1.6, pMax:2.0, fMin:.25, fMax:.3},
    maintain: {adjust:0, pMin:1.4, pMax:2.0, fMin:.2, fMax:.35},
    slowLoss: {adjust:-250, pMin:1.6, pMax:2.2, fMin:.2, fMax:.3},
    rapidLoss: {adjust:-500, pMin:1.6, pMax:2.2, fMin:.2, fMax:.3}
  };

  const goal = goals[$("goal").value];
  const calories = tdee + goal.adjust;

  if(rmr<=0 || tdee<=0 || calories<=0){
    $("error").textContent = "These inputs produce an invalid energy estimate. Please review them.";
    return;
  }

  const proteinMin = weight * goal.pMin;
  const proteinMax = weight * goal.pMax;
  const proteinCalMin = proteinMin * 4;
  const proteinCalMax = proteinMax * 4;

  const fatCalMin = calories * goal.fMin;
  const fatCalMax = calories * goal.fMax;
  const fatMin = fatCalMin / 9;
  const fatMax = fatCalMax / 9;

  const carbCalMin = calories - fatCalMax - proteinCalMax;
  const carbCalMax = calories - fatCalMin - proteinCalMin;
  const carbMin = carbCalMin / 4;
  const carbMax = carbCalMax / 4;

  $("rmrResult").textContent = fmt(rmr) + " Calories/day";
  $("tdeeResult").textContent = fmt(tdee) + " Calories/day";
  $("calorieResult").textContent = fmt(calories) + " Calories/day";

  $("proteinResult").textContent = fmt(proteinMin)+"–"+fmt(proteinMax)+" g/day";
  $("proteinCalories").textContent = fmt(proteinCalMin)+"–"+fmt(proteinCalMax)+" Calories";

  $("fatResult").textContent = fmt(fatMin)+"–"+fmt(fatMax)+" g/day";
  $("fatCalories").textContent = fmt(fatCalMin)+"–"+fmt(fatCalMax)+" Calories";

  $("carbResult").textContent = fmt(carbMin)+"–"+fmt(carbMax)+" g/day";
  $("carbCalories").textContent = fmt(carbCalMin)+"–"+fmt(carbCalMax)+" Calories";

  let warnings = [];
  if(calories<1500){
    warnings.push("Your daily Calorie goal is below 1,500. Consult a qualified healthcare professional before restricting intake to this level.");
  }
  if(carbCalMin<0){
    warnings.push("Your calculated minimum carbohydrate target is negative. The selected protein and fat ranges exceed the available Calories at their maximum values. These targets need individual adjustment.");
  }
  $("warning").textContent = warnings.join(" ");
  $("warning").classList.toggle("hidden",warnings.length===0);

  $("results").classList.remove("hidden");
  $("results").scrollIntoView({behavior:"smooth",block:"start"});
}
</script>
</body>
</html>
