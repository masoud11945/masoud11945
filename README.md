//@version=5
indicator("my indicators" , overlay = true )
showsma1 = input.bool(defval = false , title = "SMA 9")
showsma2 = input.bool(defval = false , title = "SMA 30")
showsma3 = input.bool(defval = false , title = "SMA 50")
showsma4 = input.bool(defval = false , title = "SMA 100")
showsma5 = input.bool(defval = false , title = "SMA 200")
showichi = input.bool(defval = false , title = "ICHI")
showbb = input.bool(defval = false , title = "BB")
showsuper = input.bool(defval = false , title = "Super Trend")
showsar = input.bool(defval = false , title = "SAR")
showrsi = input.bool(defval = false , title = "RSI")
//showsma1
len = input.int(9, minval=1, title="Length")
src = input(close, title="Source")
offset = input.int(title="Offset", defval=0, minval=-500, maxval=500)
out = ta.sma(src, len)
plot(showsma1 ? out : na, color=color.rgb(6, 219, 16), title="MA", offset=offset)

ma(source, length, type) =>
    switch type
        "SMA" => ta.sma(source, length)
        "EMA" => ta.ema(source, length)
        "SMMA (RMA)" => ta.rma(source, length)
        "WMA" => ta.wma(source, length)
        "VWMA" => ta.vwma(source, length)

typeMA = input.string(title = "Method", defval = "SMA", options=["SMA", "EMA", "SMMA (RMA)", "WMA", "VWMA"], group="Smoothing")
smoothingLength = input.int(title = "Length", defval = 5, minval = 1, maxval = 100, group="Smoothing")

smoothingLine = ma(out, smoothingLength, typeMA)
plot(smoothingLine, title="Smoothing Line", color=#f37f20, offset=offset, display=display.none)

//showsma2
len2 = input.int(30, minval=1, title="Length")
//src = input(close, title="Source")
offset2 = input.int(title="Offset", defval=0, minval=-500, maxval=500)
out2 = ta.sma(src, len2)
plot(showsma2 ? out2 : na, color=color.red, title="MA", offset=offset2)

//showsma3
len3 = input.int(50, minval=1, title="Length")
//src = input(close, title="Source")
offset3 = input.int(title="Offset", defval=0, minval=-500, maxval=500)
out3 = ta.sma(src, len3)
plot(showsma3 ? out3 : na, color=color.black, title="MA", offset=offset3)

//showsma4
len4 = input.int(100, minval=1, title="Length")
//src = input(close, title="Source")
offset4 = input.int(title="Offset", defval=0, minval=-500, maxval=500)
out4 = ta.sma(src, len4)
plot(showsma4 ? out4 : na, color=color.black, title="MA", offset=offset4)

//showsma4
len5 = input.int(200, minval=1, title="Length")
//src = input(close, title="Source")
offset5 = input.int(title="Offset", defval=0, minval=-500, maxval=500)
out5 = ta.sma(src, len5)
plot(showsma5 ? out5 : na, color=color.black, title="MA", offset=offset5)

//showichi
conversionPeriods = input.int(9, minval=1, title="Conversion Line Length")
basePeriods = input.int(26, minval=1, title="Base Line Length")
laggingSpan2Periods = input.int(52, minval=1, title="Leading Span B Length")
displacement = input.int(26, minval=1, title="Lagging Span")
donchian(len) => math.avg(ta.lowest(len), ta.highest(len))
conversionLine = donchian(conversionPeriods)
baseLine = donchian(basePeriods)
leadLine1 = math.avg(conversionLine, baseLine)
leadLine2 = donchian(laggingSpan2Periods)
plot( showichi ? conversionLine : na, color=#2962FF, title="Conversion Line")
plot( showichi ? baseLine : na, color=#B71C1C, title="Base Line")
plot( showichi ? close : na, offset = -displacement + 1, color=#43A047, title="Lagging Span")
p1 = plot( showichi ? leadLine1 : na, offset = displacement - 1, color=#A5D6A7,
  title="Leading Span A")
p2 = plot( showichi ? leadLine2 : na, offset = displacement - 1, color=#EF9A9A,
  title="Leading Span B")
plot( showichi ? leadLine1 > leadLine2 ? leadLine1 : leadLine2 : na, offset = displacement - 1, title = "Kumo Cloud Upper Line", display = display.none) 
plot( showichi ? leadLine1 < leadLine2 ? leadLine1 : leadLine2 : na, offset = displacement - 1, title = "Kumo Cloud Lower Line", display = display.none) 
fill(p1, p2, color = leadLine1 > leadLine2 ? color.rgb(67, 160, 71, 90) : color.rgb(244, 67, 54, 90))

//showbb
length = input.int(20, minval=1)
maType = input.string("SMA", "Basis MA Type", options = ["SMA", "EMA", "SMMA (RMA)", "WMA", "VWMA"])
//src = input(close, title="Source")
mult = input.float(2.0, minval=0.001, maxval=50, title="StdDev")


basis = ma(src, length, maType)
dev = mult * ta.stdev(src, length)
upper = basis + dev
lower = basis - dev
offset6 = input.int(0, "Offset", minval = -500, maxval = 500)
plot( showbb ? basis : na, "Basis", color=#FF6D00, offset = offset6)
p1bb = plot(showbb ? upper : na, "Upper", color=#2962FF, offset = offset6)
p2bb = plot(showbb ? lower : na, "Lower", color=#2962FF, offset = offset6)
fill(p1bb, p2bb, title = "Background", color=color.rgb(33, 150, 243, 95))
//showsuper
atrPeriod = input.int(10,    "ATR Length", minval = 1)
factor =    input.float(3.0, "Factor",     minval = 0.01, step = 0.01)

[supertrend, direction] = ta.supertrend(factor, atrPeriod)

supertrend := barstate.isfirst ? na : supertrend
upTrend =    plot( showsuper ? direction < 0 ? supertrend : na : na, "Up Trend",   color = color.green, style = plot.style_linebr)
downTrend =  plot( showsuper ? direction < 0 ? na : supertrend : na, "Down Trend", color = color.red,   style = plot.style_linebr)
bodyMiddle = plot( showsuper ? barstate.isfirst ? na : (open + close) / 2 : na, "Body Middle",display = display.none)

fill(bodyMiddle, upTrend,   color.new(color.green, 90), fillgaps = false)
fill(bodyMiddle, downTrend, color.new(color.red,   90), fillgaps = false)

alertcondition(direction[1] > direction, title='Downtrend to Uptrend', message='The Supertrend value switched from Downtrend to Uptrend ')
alertcondition(direction[1] < direction, title='Uptrend to Downtrend', message='The Supertrend value switched from Uptrend to Downtrend')
alertcondition(direction[1] != direction, title='Trend Change', message='The Supertrend value switched from Uptrend to Downtrend or vice versa')


//showsar
start = input(0.02)
increment = input(0.02)
maximum = input(0.2, "Max Value")
out6 = ta.sar(start, increment, maximum)
plot(showsar ? out6 : na, "ParabolicSAR", style=plot.style_cross, color=#2962FF)

//showrsi
//src = close, len = input(14, minval=1, title="Length")
up = rma(max(change(src), 0), len)
down = rma(-min(change(src), 0), len)
rsi = down == 0 ? 100 : up == 0 ? 0 : 100 - (100 / (1 + up / down))

//coloring method below

src2 = close, len1 = input(70, minval=1, title="UpLevel")
src3 = close, len2 = input(30, minval=1, title="DownLevel")
isup() => rsi > len1
isdown() => rsi < len2
barcolor(isup() ? green : isdown() ? red : na )
