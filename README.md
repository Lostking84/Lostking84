//@version=5
indicator(title="Advanced Elliot Wave Counter for BTCUSD Log Daily", overlay=true, scale=scale.log)

// Customizable parameters
waveLookback = input.int(title="Wave Lookback", defval=14)
correctionLookback = input.int(title="Correction Lookback", defval=7)
minWaveSize = input.float(title="Minimum Wave Size (%)", defval=3.0)
fibRetracementLevels = input.bool(title="Show Fibonacci Retracement Levels", defval=true)

// Function to check for higher highs/lower lows with adjustable lookback
isHigherHigh(high, lookback) =>
    high > ta.highest(high, lookback)

isLowerLow(low, lookback) =>
    low < ta.lowest(low, lookback)

// Function to check minimum wave size criteria
isSignificantWave(high, low, minSize) =>
    abs(high - low) / low * 100 > minSize

// Identify potential impulse waves
impulseWave = isHigherHigh(high, waveLookback) and isLowerLow(low, correctionLookback) and isSignificantWave(high, low, minWaveSize)

// Identify potential corrective waves
correctiveWave = not impulseWave

// Initialize wave counter variables
impulseCount = 0
correctionCount = 0

// Count and label waves
for i = 0 to bar_index
    if impulseWave[i]
        impulseCount += 1
        label.new(bar_index[i], high[i], str.format("I{0}", impulseCount), color=color.green)
    if correctiveWave[i]
        correctionCount += 1
        label.new(bar_index[i], low[i], str.format("C{0}", correctionCount), color=color.red)

// Fibonacci retracement levels (optional)
if fibRetracementLevels
    fibLevels = [0.236, 0.382, 0.5, 0.618, 0.786]
    for level in fibLevels
        hline(price * level, color=color.blue, linestyle=hline.style_dashed)
