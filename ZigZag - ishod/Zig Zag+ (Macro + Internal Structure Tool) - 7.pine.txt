//@version=5
indicator(title="Zig Zag+ (Macro + Internal Structure Tool)", shorttitle="ZigZag+", overlay = true, max_lines_count=500)

color = input.color(color.aqua, title="", group="Zig Zag [Color] [High/Low length] [Thickness]", inline="1")
length = input(16, title = "", group="Zig Zag [Color] [High/Low length] [Thickness]", inline="1")
lineWidth = input(2, title="", group="Zig Zag [Color] [High/Low length] [Thickness]", inline="1")
enableLine = input(true, title="Show", group="Zig Zag [Color] [High/Low length] [Thickness]", inline="1")

color2 = input.color(color.new(#6228ad, 0), title="", group="Zig Zag [Color] [High/Low length] [Thickness]", inline="2")
length2 = input(4, title = "", group="Zig Zag [Color] [High/Low length] [Thickness]", inline="2")
lineWidth2 = input(2, title="", group="Zig Zag [Color] [High/Low length] [Thickness]", inline="2")
enableLine2 = input(true, title="Show", group="Zig Zag [Color] [High/Low length] [Thickness]", inline="2")

h = ta.highest(high, length * 2 + 1)
l = ta.lowest(low, length * 2 + 1)

h2 = ta.highest(high, length2 * 2 + 1)
l2 = ta.lowest(low, length2 * 2 + 1)

f_isMin(len, l, h) =>
    l == low[len]
f_isMax(len, l, h) =>
    h == high[len]

var dirUp = false
var lastLow = high * 100
var lastHigh = 0.0
var timeLow = bar_index
var timeHigh = bar_index
var line li = na
f_drawLine() =>
    _li_color = enableLine ? color : na
    line.new(
         timeHigh - length, lastHigh,
         timeLow - length, lastLow,
         xloc.bar_index, color=_li_color, width=lineWidth
         )

if dirUp
    if (f_isMin(length, l, h) and low[length] < lastLow)
        lastLow := low[length]
        timeLow := bar_index
        line.delete(li)
        li := f_drawLine()
 
    if (f_isMax(length, l, h) and high[length] > lastLow)
        lastHigh := high[length]
        timeHigh := bar_index
        dirUp := false
        li := f_drawLine()
 
if not dirUp
    if (f_isMax(length, l, h) and high[length] > lastHigh)
        lastHigh := high[length]
        timeHigh := bar_index
        line.delete(li)
        li := f_drawLine()
    if f_isMin(length, l, h) and low[length] < lastHigh
        lastLow := low[length]
        timeLow := bar_index
        dirUp := true
        li := f_drawLine()
        if (f_isMax(length, l, h) and high[length] > lastLow)
            lastHigh := high[length]
            timeHigh := bar_index
            dirUp := false
            li := f_drawLine()

// Second line
var dirUp2 = false
var lastLow2 = high * 100
var lastHigh2 = 0.0
var timeLow2 = bar_index
var timeHigh2 = bar_index
var line li2 = na
f_drawLine2() =>
    _li_color2 = enableLine2 ? color2 : na
    line.new(
         timeHigh2 - length2, lastHigh2,
         timeLow2 - length2, lastLow2,
         xloc.bar_index, color=_li_color2, width=lineWidth2
         )

if dirUp2
    if (f_isMin(length2, l2, h2) and low[length2] < lastLow2)
        lastLow2 := low[length2]
        timeLow2 := bar_index
        line.delete(li2)
        li2 := f_drawLine2()

    if (f_isMax(length2, l2, h2) and high[length2] > lastLow2)
        lastHigh2 := high[length2]
        timeHigh2 := bar_index
        dirUp2 := false
        li2 := f_drawLine2()

if not dirUp2
    if (f_isMax(length2, l2, h2) and high[length2] > lastHigh2)
        lastHigh2 := high[length2]
        timeHigh2 := bar_index
        line.delete(li2)
        li2 := f_drawLine2()
    if f_isMin(length2, l2, h2) and low[length2] < lastHigh2
        lastLow2 := low[length2]
        timeLow2 := bar_index
        dirUp2 := true
        li2 := f_drawLine2()
        if (f_isMax(length2, l2, h2) and high[length2] > lastLow2)
            lastHigh2 := high[length2]
            timeHigh2 := bar_index
            dirUp2 := false
            li2 := f_drawLine2()

//Markup/Markdown candle options
showLabels = input(false, title="Label Markup/Markdown candles", group="Markup/Markdown Options", tooltip="Bearish:\nThis will label candles where the previous several candles all have consistently lower upper wicks, indicating a possible markdown in progress, and will highlight a candle where the upper wick grabs liquidity from above the last candles upper wick, and the body closes lower or equal to the previous candle.\n\nEntering at the close of these candles, with stoploss placed just above the wick can be an effective way to enter into a markdown already in progress.\n\nBullish\nThis will show candles where the previous several candles all have consistently higher lower wicks, indicating a possible markup in progress, and will highlight a candle where the lower wick grabs liquidity from below the last candles lower wick, and the body closes higher or equal to the previous candle.\n\nEntering at the close of these candles, with stoploss placed just below the wick can be an effective way to enter into a markup already in progress.")
showBarcolor = input(false, title="Color Markup/Markdown candles", group="Markup/Markdown Options", tooltip="Bearish:\nThis will color candles where the previous several candles all have consistently lower upper wicks, indicating a possible markdown in progress, and will highlight a candle where the upper wick grabs liquidity from above the last candles upper wick, and the body closes lower or equal to the previous candle.\n\nEntering at the close of these candles, with stoploss placed just above the wick can be an effective way to enter into a markdown already in progress.\n\nBullish\nThis will show candles where the previous several candles all have consistently higher lower wicks, indicating a possible markup in progress, and will highlight a candle where the lower wick grabs liquidity from below the last candles lower wick, and the body closes higher or equal to the previous candle.\n\nEntering at the close of these candles, with stoploss placed just below the wick can be an effective way to enter into a markup already in progress.")
 
isBullishTripleM = (low[4] < low[3] and low[3] < low[2] and low[2] < low[1] and (low[1] > low and close[1] <= close))
isBearishTripleM = (high[4] and high[4] > high[3] and high[3] > high[2] and high[2] > high[1] and (high[1] < high and close[1] >= close))
 
tripleMColor = isBullishTripleM ? color.green : ( isBearishTripleM ? color.red : na)
 
plotshape(isBullishTripleM and showLabels, style=shape.triangleup, color=color.green, title="long entry", location=location.belowbar, size=size.auto, offset=0)
plotshape(isBearishTripleM and showLabels, style=shape.triangledown, color=color.red, title="short entry", location=location.abovebar, size=size.auto, offset=0)
 
barcolor(showBarcolor ? tripleMColor : na)
 
alertcondition(isBearishTripleM or isBullishTripleM, title="A Markup/Markdown entry candle has been detected", message="A Markup/Markdown entry candle has been detected")
alertcondition(isBullishTripleM, title="Bullish Markup entry candle detected", message="Bullish Markup entry candle detected")
alertcondition(isBearishTripleM, title="Bearish Markdown entry candle detected", message="Bearish Markdown entry candle detected")
//end of Triple M options