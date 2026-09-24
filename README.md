# pine-tradingview-rly-rsi

```pinescript

//@version=6
indicator('Rlytrue RSI Alerts', overlay=false)

// Настройки RSI
rsiLength = input(14, 'Период RSI')
overbought = input(70, 'Уровень перекупленности')
oversold = input(30, 'Уровень перепроданности')
middleLine = input(50, 'Средняя линия')

// Расчет RSI
rsiValue = ta.rsi(close, rsiLength)

// Нормализация значений для плавного градиента
greenIntensity = math.min(100, math.max(10, (rsiValue - overbought) / (100 - overbought) * 100))
redIntensity = math.min(100, math.max(20, (oversold - rsiValue) / oversold * 100))

// Цвет фона с плавным градиентом
bgColor = if rsiValue >= overbought
    color.new(#66ff00, 100 - greenIntensity)
else if rsiValue <= oversold
    color.new(#FF3636, 100 - redIntensity)
else
    color.new(color.gray, 100) // transparent

// Отрисовка
plot(rsiValue, 'RSI', color=#ffff00, linewidth=1)
hline(overbought, 'Overbought', color=#66ff00)
hline(oversold, 'Oversold', color=#FF3636)
hline(middleLine, 'Middle Line', color=color.rgb(255, 255, 255, 44))
bgcolor(bgColor)

// Отображение текущего значения RSI на графике
var table rsiTable = table.new(position.top_right, 1, 1)
if barstate.islast
    table.cell(rsiTable, 0, 0, "RSI: " + str.tostring(rsiValue, "#"), bgcolor=bgColor, text_color=color.white, text_size=26)

// Уведомления
if rsiValue >= overbought
    alert('RSI Перекупленность (>70)! Текущее значение: ' + str.tostring(rsiValue), alert.freq_once_per_bar)
if rsiValue <= oversold
    alert('RSI Перепроданность (<30)! Текущее значение: ' + str.tostring(rsiValue), alert.freq_once_per_bar)
```
